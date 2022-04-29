# css-module原理

## 大体原理

- 可以看到以 :local 包裹的才会被编译，不是 :local 包裹的会作为全局样式。

- composes-with 和 composes 的作用相同，都是做样式的组合，可以看到编译之后会把 compose 的多个选择器合并到一起。也就是一对多的映射关系。

- 实现了 :local 的选择器名字的转换，实现了 compose 的样式组合，最后会把映射关系都放到 :export 这个样式下。

- 这样 css-loader 调用 postcss-modules-scope 完成了作用域的编译之后，不就能从 :export 拿到映射关系了么？

- 然后就可以用这个映射关系生成 js 模块，组件里就可以用 styles.xxx 的方式引入对应的 css 了。

## 实现原理代码:

完整代码：github: https://github.com/QuarkGluonPlasma/postcss-plugin-exercize

```js
const selectorParser = require("postcss-selector-parser");

function generateScopedName(name) {
    const randomStr = Math.random().toString(16).slice(2);
    return `_${randomStr}__${name}`;
};

const plugin = (options = {}) => {
    return {
        postcssPlugin: "my-postcss-modules-scope",
        Once(root, helpers) {
            const exports = {};

            function exportScopedName(name) {
                const scopedName = generateScopedName(name);

                exports[name] = exports[name] || [];

                if (exports[name].indexOf(scopedName) < 0) {
                    exports[name].push(scopedName);
                }

                return scopedName;
            }

            function localizeNode(node) {
                switch (node.type) {
                    case "selector":
                        node.nodes = node.map(localizeNode);
                        return node;
                    case "class":
                        return selectorParser.className({
                            value: exportScopedName(
                                node.value,
                                node.raws && node.raws.value ? node.raws.value : null
                            ),
                        });
                    case "id": {
                        return selectorParser.id({
                            value: exportScopedName(
                                node.value,
                                node.raws && node.raws.value ? node.raws.value : null
                            ),
                        });
                    }
                }
            }

            function traverseNode(node) {
                switch (node.type) {
                    case "root":
                    case "selector": {
                        node.each(traverseNode);
                        break;
                    }
                    case "id":
                    case "class":
                        exports[node.value] = [node.value];
                        break;
                    case "pseudo":
                        if (node.value === ":local") {
                            const selector = localizeNode(node.first, node.spaces);

                            node.replaceWith(selector);

                            return;
                        }
                }
                return node;
            }

            // 处理 :local 选择器
            root.walkRules((rule) => {
                const parsedSelector = selectorParser().astSync(rule);

                rule.selector = traverseNode(parsedSelector.clone()).toString();

                rule.walkDecls(/composes|compose-with/i, (decl) => {
                    const localNames = parsedSelector.nodes.map((node) => {
                        return node.nodes[0].first.first.value;
                    })
                    const classes = decl.value.split(/\s+/);

                    classes.forEach((className) => {
                        const global = /^global\(([^)]+)\)$/.exec(className);

                        if (global) {
                            localNames.forEach((exportedName) => {
                                exports[exportedName].push(global[1]);
                            });
                        } else if (Object.prototype.hasOwnProperty.call(exports, className)) {
                            localNames.forEach((exportedName) => {
                                exports[className].forEach((item) => {
                                    exports[exportedName].push(item);
                                });
                            });
                        } else {
                            throw decl.error(
                                `referenced class name "${className}" in ${decl.prop} not found`
                            );
                        }
                    });

                    decl.remove();
                });

            });

            // 处理 :local keyframes
            root.walkAtRules(/keyframes$/i, (atRule) => {
                const localMatch = /^:local\((.*)\)$/.exec(atRule.params);

                if (localMatch) {
                    atRule.params = exportScopedName(localMatch[1]);
                }
            });

            // 生成 :export rule
            const exportedNames = Object.keys(exports);

            if (exportedNames.length > 0) {
                const exportRule = helpers.rule({ selector: ":export" });

                exportedNames.forEach((exportedName) =>
                    exportRule.append({
                        prop: exportedName,
                        value: exports[exportedName].join(" "),
                        raws: { before: "\n  " },
                    })
                );

                root.append(exportRule);
            }
        },
    };
};

plugin.postcss = true;

module.exports = plugin;
```

## 总结

CSS 实现模块隔离主要有运行时和编译时两类方案：

- 运行时通过命名空间来区分，比如 BEM 规范。

- 编译时自动转换选择器名字，添加上唯一标识，比如 scoped 和 css-modules。
  
  - scoped 是通过给元素添加 data-xxx 属性，然后在 css 中添加 [data-xx] 的属性选择器来实现的，对开发者来说是透明的。是 vue-loader 实现的，主要用在 vue 里。
  
  - css-modules 则是通过编译修改选择器名字为全局唯一的方式实现的，开发者需要用 styles.xx 的方式来引用编译后的名字，对开发者来说不透明，但是也有能配合编辑器实现智能提示的好处。是 css-loader 实现的，vue、react 都可用。
    
    - css-loader 是通过 postcss 插件来实现 css-modules 的，其中最核心的是 postcss-modules-scope 插件。
    
    - 自己写了一个 postcss-modules-scope 插件：
      
      - 遍历所有选择器，对 :local 伪元素包裹的选择器做转化，并且收集到 exports 中。
      
      - 对 composes 的选择器做一对多的映射，也收集到 exports 中。
      
      - 根据 exports 收集到的映射关系生成 :exports 样式
  
  - 通过 JS 来管理 css，也就是 css-in-js。












