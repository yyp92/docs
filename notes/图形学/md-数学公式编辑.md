# markdown 数学公式编辑

## 使用

### 如何插入公式

`LATEX`的数学公式由两种形式：行中公式和独立公式。​​

行中公式放在文中与其它文字混编，独立公式单独成行。

行中公式可以用如下方法表示：

`$ 数学公式 $​`​​​​​​

独立公式可以用如下方法表示：

`$$ 数学公式 $$​`​​​

### 如何输入上下标

`^`表示上标，`_`表示下标。如果上下标的内容多于一个字符，需要用 `{}` 将这些内容括成一个整体。上下标可以嵌套，也可以同时使用。

🌰

```bash
$$ x^{y^z}=(1+{\rm e}^x)^{-2xy^w} $$
```

👉

$$
x^{y^z}=(1+{\rm e}^x)^{-2xy^w}
$$

另外，如果要在左右两边都有上下标，可以使用 `\sideset` 命令；也可以简单地在符号前面多打一个上下标，此时会以行内公式渲染。

🌰

```bash
$$ \sideset{^1_2}{^3_4}\bigotimes \quad or \quad {^1_2}\bigotimes {^3_4} $$
```

👉

$$
\quad {^1_2}\bigotimes {^3_4}
$$

### 如何输入括号和分隔符

`()`、`[]` 和 `|` 表示符号本身，使用 `\{\}` 来表示 `{}` 。当要显示大号的括号或分隔符时，要用 `\left` 和 `\right` 命令。

一些特殊的括号：

| 输入      | 显示  | 输入      | 显示  |
| ------- | --- | ------- | --- |
| \langle | ⟨   | \rangle | ⟩   |
| \lceil  | ⌈   | \rceil  | ⌉   |
| \lfloor | ⌊   | \rfloor | ⌋   |
| \lbrace | {   | \rbrace | }   |
| \lvert  | \|  | \rvert  | \|  |
| \lVert  | ∥   | \rVert  | ∥   |

🌰

```bash
$$ f(x,y,z) = 3y^2z \left( 3+\frac{7x+5}{1+y^2} \right) $$
```

👉

$$
f(x,y,z) = 3y^2z \left( 3+\frac{7x+5}{1+y^2} \right)
$$

有时要用 `\left.` 或 `\right.` 进行匹配而不显示本身。

### 如何输入分数

通常使用 `\frac {分子} {分母}` 来生成一个分数，分数可多层嵌套。如果分式较为复杂，亦可使用 `分子 \over 分母` 此时分数仅有一层。

🌰

```bash
$$ \frac{a-1}{b-1} \quad or \quad {a+1 \over b+1} $$
```

👉

$$
\frac{a-1}{b-1} \quad or \quad {a+1 \over b+1}
$$

当分式 **仅有两个字符时** 可直接输入 `\frac ab` 来快速生成一个 。

### 如何输入开方

使用 `\sqrt [根指数，省略时为2] {被开方数}` 命令输入开方。

🌰

```bash
$$ \sqrt{2} \quad or \quad \sqrt[n]{3} $$
```

👉

$$
\sqrt{2} \quad or \quad \sqrt[n]{3}
$$

### 如何输入省略号

数学公式中常见的省略号有两种，`\ldots` 表示与 **文本底线** 对齐的省略号，`\cdots` 表示与 **文本中线** 对齐的省略号。

🌰

```bash
$$ f(x_1,x_2,\underbrace{\ldots}_{\rm ldots} ,x_n) = x_1^2 + x_2^2 + \underbrace{\cdots}_{\rm cdots} + x_n^2 $$
```

👉

$$
f(x_1,x_2,\underbrace{\ldots}_{\rm ldots} ,x_n) = x_1^2 + x_2^2 + \underbrace{\cdots}_{\rm cdots} + x_n^2
$$

### 如何输入向量

使用 `\vec{向量}` 来自动产生一个向量。也可以使用 `\overrightarrow` 等命令自定义字母上方的符号。

🌰

```bash
$$ \vec{a} \cdot \vec{b}=0 $$
```

👉

$$
\vec{a} \cdot \vec{b}=0
$$

🌰

```bash
$$ xy \text{ with arrows:} \quad \overleftarrow{xy} \; \mid \; \overleftrightarrow{xy} \; \mid \; \overrightarrow{xy} $$ 
```

👉

$$
xy \text{ with arrows:} \quad \overleftarrow{xy} \; \mid \; \overleftrightarrow{xy} \; \mid \; \overrightarrow{xy}
$$

### 如何输入积分

使用 `\int_积分下限^积分上限 {被积表达式}` 来输入一个积分。

🌰

```bash
$$ \int_0^1 {x^2} \,{\rm d}x $$
```

👉

$$
\int_0^1 {x^2} \,{\rm d}x
$$

本例中 `\,` 和 `{\rm d}` 部分可省略，但加入能使式子更美观，详见“[在字符间加入空格](https://www.zybuluo.com/codeep/note/163962#3%E5%9C%A8%E5%AD%97%E7%AC%A6%E9%97%B4%E5%8A%A0%E5%85%A5%E7%A9%BA%E6%A0%BC)”及“[如何进行字体转换](https://www.zybuluo.com/codeep/note/163962#13%E5%A6%82%E4%BD%95%E8%BF%9B%E8%A1%8C%E5%AD%97%E4%BD%93%E8%BD%AC%E6%8D%A2)”。

### 如何输入极限运算

使用 `\lim_{变量 \to 表达式} 表达式` 来输入一个极限。如有需求，可以更改 `\to` 符号至任意符号。

🌰

```bash
$$ \lim_{n \to \infty} \frac{1}{n(n+1)} \quad and \quad \lim_{x\leftarrow{示例}} \frac{1}{n(n+1)} $$
```

👉

$$
\lim_{n \to \infty} \frac{1}{n(n+1)} \quad and \quad \lim_{x\leftarrow{示例}} \frac{1}{n(n+1)}
$$

### 如何输入累加、累乘运算

使用 `\sum_{下标表达式}^{上标表达式} {累加表达式}` 来输入一个累加。与之类似，使用 `\prod` `\bigcup` `\bigcap` 来分别输入累乘、并集和交集，更多符号可参考“[其它特殊字符](https://www.zybuluo.com/codeep/note/163962#12%E5%A6%82%E4%BD%95%E8%BE%93%E5%85%A5%E5%85%B6%E5%AE%83%E7%89%B9%E6%AE%8A%E5%AD%97%E7%AC%A6)”。 
此类符号在行内显示时上下标表达式将会移至右上角和右下角，如 。

🌰

```bash
$$ \sum_{i=1}^n \frac{1}{i^2} \quad and \quad \prod_{i=1}^n \frac{1}{i^2} \quad and \quad \bigcup_{i=1}^{2} \Bbb{R} $$
```

👉

$$
\sum_{i=1}^n \frac{1}{i^2} \quad and \quad \prod_{i=1}^n \frac{1}{i^2} \quad and \quad \bigcup_{i=1}^{2} \Bbb{R}
$$

### 如何输入希腊字母

输入 `\小写希腊字母英文全称` 和 `\首字母大写希腊字母英文全称` 来分别输入小写和大写希腊字母。 
**对于大写希腊字母与现有字母相同的，直接输入大写字母即可。**

| 输入       | 显示         | 输入      | 显示        | 输入       | 显示         | 输入       | 显示         |
| -------- | ---------- | ------- | --------- | -------- | ---------- | -------- | ---------- |
| \alpha   | $\alpha$   | A       | $A$       | \beta    | $\beta$    | B        | $B$        |
| \gamma   | $\gamma$   | \Gamma  | $\Gamma$  | \delta   | $\delta$   | \Delta   | $\Delta$   |
| \epsilon | $\epsilon$ | E       | $E$       | \zeta    | $\zeta$    | Z        | $Z$        |
| \eta     | $\eta$     | H       | $H$       | \theta   | $\theta$   | \Theta   | $\Theta$   |
| \iota    | $\iota$    | I       | $I$       | \kappa   | $\kappa$   | K        | $K$        |
| \lambda  | $\lambda$  | \Lambda | $\Lambda$ | \mu      | $\mu$      | M        | $M$        |
| \nu      | $\nu$      | N       | $N$       | \xi      | $\xi$      | \Xi      | $\Xi$      |
| o        | $o$        | O       | $O$       | \pi      | $\pi$      | \Pi      | $\Pi$      |
| \rho     | $\rho$     | P       | $P$       | \sigma   | $\sigma$   | \Sigma   | $\Sigma$   |
| \tau     | $\tau$     | T       | $T$       | \upsilon | $\upsilon$ | \Upsilon | $\Upsilon$ |
| \phi     | $\phi$     | \Phi    | $\Phi$    | \chi     | $\chi$     | X        | $X$        |
| \psi     | $\psi$     | \Psi    | $\Psi$    | \omega   | $\omega$   | \Omega   | $\Omega$   |

**部分字母有变量专用形式，以 `\var-` 开头。**

| 小写形式     | 大写形式   | 变量形式        | 显示                              |
| -------- | ------ | ----------- | ------------------------------- |
| \epsilon | E      | \varepsilon | $\epsilon \| E \| \varepsilon$  |
| \theta   | \Theta | \vartheta   | $\theta \| \Theta \| \vartheta$ |
| \rho     | P      | \varrho     | $\rho \| P \| \varrho$          |
| \sigma   | \Sigma | \varsigma   | $\sigma \| \Sigma \| \varsigma$ |
| \phi     | \Phi   | \varphi     | $\phi \| \Phi \| \varphi$       |

### 如何输入其他符号

#### 关系运算符

| 输入       | 显示         | 输入         | 显示           | 输入        | 显示          | 输入         | 显示           |
| -------- | ---------- | ---------- | ------------ | --------- | ----------- | ---------- | ------------ |
| \pm      | $\pm$      | \times     | $\times$     | \div      | $\div$      | \mid       | $\mid$       |
| \nmid    | $\nmid$    | \cdot      | $\cdot$      | \circ     | $\circ$     | \ast       | $\ast$       |
| \bigodot | $\bigodot$ | \bigotimes | $\bigotimes$ | \bigoplus | $\bigoplus$ | \leq       | $\leq$       |
| \geq     | $\geq$     | \neq       | $\neq$       | \approx   | $\approx$   | \equiv     | $\equiv$     |
| \sum     | $\sum$     | \prod      | $\prod$      | \coprod   | $\coprod$   | \backslash | $\backslash$ |

#### 集合运算符

| 输入        | 显示          | 输入      | 显示        | 输入          | 显示            |
| --------- | ----------- | ------- | --------- | ----------- | ------------- |
| \emptyset | $\emptyset$ | \in     | $\in$     | \notin      | $\notin$      |
| \subset   | $\subset$   | \supset | $\supset$ | \subseteq   | $\subseteq$   |
| \supseteq | $\supseteq$ | \cap    | $\cap$    | \cup        | $\cup$        |
| \vee      | $\vee$      | \wedge  | $\wedge$  | \uplus      | $\uplus$      |
| \top      | $\top$      | \bot    | $\bot$    | \complement | $\complement$ |

#### 对数运算符

| 输入   | 显示     | 输入  | 显示    | 输入  | 显示    |
| ---- | ------ | --- | ----- | --- | ----- |
| \log | $\log$ | \lg | $\lg$ | \ln | $\ln$ |

#### 三角运算符

| 输入       | 显示         | 输入    | 显示      | 输入       | 显示         |
| -------- | ---------- | ----- | ------- | -------- | ---------- |
| \backsim | $\backsim$ | \cong | $\cong$ | \angle A | $\angle A$ |
| \sin     | $\sin$     | \cos  | $\cos$  | \tan     | $\tan$     |
| \csc     | $\csc$     | \sec  | $\sec$  | \cot     | $\cot$     |

#### 微积分运算符

| 输入       | 显示         | 输入     | 显示       | 输入     | 显示       |
| -------- | ---------- | ------ | -------- | ------ | -------- |
| \int     | $\int$     | \iint  | $\iint$  | \iiint | $\iiint$ |
| \partial | $\partial$ | \oint  | $\oint$  | \prime | $\prime$ |
| \lim     | $\lim$     | \infty | $\infty$ | \nabla | $\nabla$ |

#### 逻辑运算符

| 输入       | 显示         | 输入         | 显示           | 输入          | 显示            |
| -------- | ---------- | ---------- | ------------ | ----------- | ------------- |
| \because | $\because$ | \therefore | $\therefore$ | \neg        | $\neg$        |
| \forall  | $\forall$  | \exists    | $\exists$    | \not\subset | $\not\subset$ |
| \not<    | $\not<$    | \not>      | $\not>$      | \not=       | $\not=$       |

#### 戴帽符号

| 输入         | 显示           | 输入              | 显示                | 输入        | 显示          |
| ---------- | ------------ | --------------- | ----------------- | --------- | ----------- |
| \hat{xy}   | $\hat{xy}$   | \widehat{xyz}   | $\widehat{xyz}$   | \bar{y}   | $\bar{y}$   |
| \tilde{xy} | $\tilde{xy}$ | \widetilde{xyz} | $\widetilde{xyz}$ | \acute{y} | $\acute{y}$ |
| \breve{y}  | $\breve{y}$  | \check{y}       | $\check{y}$       | \grave{y} | $\grave{y}$ |
| \dot{x}    | $\dot{x}$    | \ddot{x}        | $\ddot{x}$        |           |             |

若需要在特定文字顶部\底部放置内容，可使用 `\overset{顶部内容}{正常内容}` 和 `\underset{底部内容}{正常内容}` 命令。

🌰

```bash
$$ \verb+\overset{above}{level}+ \qquad \overset{xx}{ABC} \;\; \mid \quad \overset{x^2}{\longmapsto}\ \, \mid \quad \overset{\bullet\circ\circ\bullet}{T} $$ 
```

👉

$$
\verb+\overset{above}{level}+ \qquad \overset{xx}{ABC} \;\; \mid \quad \overset{x^2}{\longmapsto}\ \, \mid \quad \overset{\bullet\circ\circ\bullet}{T}
$$

#### 连线符号

其它可用的文字修饰符可参见官方文档 [“Additional decorations”](https://math.meta.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference&#35answer-13081)。

| 输入                                                                                                                                                   | 显示                                               |
| ---------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| \fbox{a+b+c+d}**高级框选需[声明 `enclose` 标签](http://home.ustc.edu.cn/~zzx2002/new/2021/08/04/mathjax/&#355%E6%B7%BB%E5%8A%A0%E5%88%A0%E9%99%A4%E7%BA%BF)** | $\fbox{a+b+c+d}$                                 |
| \overleftarrow{a+b+c+d}                                                                                                                              | $\overleftarrow{a+b+c+d}$                        |
| \overrightarrow{a+b+c+d}                                                                                                                             | $\overrightarrow{a+b+c+d}$                       |
| \overleftrightarrow{a+b+c+d}                                                                                                                         | $\overleftrightarrow{a+b+c+d}$                   |
| \underleftarrow{a+b+c+d}                                                                                                                             | $\underleftarrow{a+b+c+d}$                       |
| \underrightarrow{a+b+c+d}                                                                                                                            | $\underrightarrow{a+b+c+d}$                      |
| \underleftrightarrow{a+b+c+d}                                                                                                                        | $\underleftrightarrow{a+b+c+d}$                  |
| \overline{a+b+c+d}                                                                                                                                   | $\overline{a+b+c+d}$                             |
| \underline{a+b+c+d}                                                                                                                                  | $\underline{a+b+c+d}$                            |
| \overbrace{a+b+c+d}^{Sample}                                                                                                                         | $\overbrace{a+b+c+d}^{Sample}$                   |
| \underbrace{a+b+c+d}_{Sample}                                                                                                                        | $\underbrace{a+b+c+d}_{Sample}$                  |
| \overbrace{a+\underbrace{b+c}_{1.0}+d}^{2.0}                                                                                                         | $\overbrace{a+\underbrace{b+c}_{1.0}+d}^{2.0}$   |
| \underbrace{a\cdot a\cdots a}_{b\text{ times}}                                                                                                       | $\underbrace{a\cdot a\cdots a}_{b\text{ times}}$ |

#### 箭头符号

- 推荐使用符号：

| 输入       | 显示         | 输入      | 显示        | 输入                       | 显示                         |
| -------- | ---------- | ------- | --------- | ------------------------ | -------------------------- |
| \to      | $\to$      | \mapsto | $\mapsto$ | \underrightarrow{1℃/min} | $\underrightarrow{1℃/min}$ |
| \implies | $\implies$ | \iff    | $\iff$    | \impliedby               | $\impliedby$               |

- 其它可用符号：

| 输入                  | 显示                    | 输入                  | 显示                    |
| ------------------- | --------------------- | ------------------- | --------------------- |
| \uparrow            | $\uparrow$            | \Uparrow            | $\Uparrow$            |
| \downarrow          | $\downarrow$          | \Downarrow          | $\Downarrow$          |
| \leftarrow          | $\leftarrow$          | \Leftarrow          | $\Leftarrow$          |
| \rightarrow         | $\rightarrow$         | \Rightarrow         | $\Rightarrow$         |
| \leftrightarrow     | $\leftrightarrow$     | \Leftrightarrow     | $\Leftrightarrow$     |
| \longleftarrow      | $\longleftarrow$      | \Longleftarrow      | $\Longleftarrow$      |
| \longrightarrow     | $\longrightarrow$     | \Longrightarrow     | $\Longrightarrow$     |
| \longleftrightarrow | $\longleftrightarrow$ | \Longleftrightarrow | $\Longleftrightarrow$ |

### 如何进行字体转换

若要对公式的某一部分字符进行字体转换，可以用 `{\字体 {需转换的部分字符}}` 命令，其中 `\字体` 部分可以参照下表选择合适的字体。一般情况下，公式默认为斜体字 italic 。

示例中 **全部大写** 的字体仅大写可用。

| 输入    | 全字母可用 | 显示             | 输入           | 仅大写可用          | 显示                |
| ----- | ----- | -------------- | ------------ | -------------- | ----------------- |
| \rm   | 罗马体   | $\rm Sample$   | **\mathcal** | **花体（数学符号等）**  | $\mathcal SAMPLE$ |
| \it   | 斜体    | $\it Sample$   | **\mathbb**  | **黑板粗体（定义域等）** | $\mathbb SAMPLE$  |
| \bf   | 粗体    | $\bf Sample$   | \mit         | 数学斜体           | $SAMPLE$          |
| \sf   | 等线体   | $\sf Sample$   | \scr         | 手写体            | -暂时不支持            |
| \tt   | 打字机体  | $\tt Sample$   |              |                |                   |
| \frak | 旧德式字体 | $\frak Sample$ |              |                |                   |

> **@lymd** `\boldsymbol{\alpha}` 用来表示向量或者矩阵的加粗斜体，如向量 $\vec{a}$。

转换字体十分常用，例如在积分中：

🌰

```bash
$$
    \begin{array}{cc} \mathrm{Bad} & \mathrm{Better} \\ \hline \\ \int_0^1 x^2 dx & \int_0^1 x^2 \,{\rm d}x \end{array} 
$$
```

👉

$$
\begin{array}{cc} \mathrm{Bad} & \mathrm{Better} \\ \hline \\ \int_0^1 x^2 dx & \int_0^1 x^2 \,{\rm d}x \end{array} 

$$

注意比较两个式子间 dx 与 dx 的不同。 
使用 `\operatorname` 命令也可以达到相同的效果，详见“[定义新的运算符](http://home.ustc.edu.cn/~zzx2002/new/2021/08/04/mathjax/&#351%E5%AE%9A%E4%B9%89%E6%96%B0%E7%9A%84%E8%BF%90%E7%AE%97%E7%AC%A6-operatorname)”。

### 如何高亮一行公式

使用 `\bbox[底色, (可选)边距, (可选)边框 border: 框宽度 框类型 框颜色]` 命令来高亮一行公式。 
底色和框颜色支持详见“[更改文字颜色](http://home.ustc.edu.cn/~zzx2002/new/2021/08/04/mathjax/&#354%E6%9B%B4%E6%94%B9%E6%96%87%E5%AD%97%E9%A2%9C%E8%89%B2-color)”，边距及框宽度支持 `绝对像素 px` 或 `相对大小 em`，框类型支持 `实线 solid` 或 `虚线 dashed`。

> 暂时不支持此语法

### 大括号和行标的使用

在 `\left` 和 `\right` 之后加上要使用的括号来创建自动匹配高度的圆括号 `(` `)`，方括号 `[` `]` 和花括号 `\{` `\}`。 
在每个公式末尾前使用 `\tag {行标}` 来实现行标。

🌰

```bash
$$ f\left( \left[ \frac{ 1+\left\{x,y\right\} }{ \left( \frac xy + \frac yx \right) (u+1) }+a \right]^{3/2} \right) \tag {行标} $$ 
```

👉

$$
f\left( \left[ \frac{ 1+\left\{x,y\right\} }{ \left( \frac xy + \frac yx \right) (u+1) }+a \right]^{3/2} \right) \tag {行标} 
$$

如果你需要在不同的行显示对应括号，可以在每一行对应处使用 `\left.` 或 `\right.` 来放一个“不存在的括号”。

🌰

```bash
$$ \begin{align*} a=&\left(1+2+3+ \cdots \right. \\ &\cdots+\left. \infty-2+\infty-1+\infty\right) \end{align*} $$ 
```

> 👉 此语法暂时不支持

如果你需要将大括号里面显示的分隔符也变大，可以使用 `\middle` 命令，此处分别使用单竖线 `|` 和双竖线 `\\|` 。

🌰

```bash
$$
\left\langle  
    q \; \middle|
        \frac{\frac xy}{\frac uv}
    \middle\| p 
\right\rangle
$$
```

👉

$$
\left\langle  
    q \; \middle|
        \frac{\frac xy}{\frac uv}
    \middle\| p 
\right\rangle
$$

## 资料

- [LaTeX 公式篇](https://zhuanlan.zhihu.com/p/110756681)

- [markdown 拓展 - 对数学公式的支持 - 掘金](https://juejin.cn/post/6988467298352775198)

- [markdown 数学公式Latex语法 - 掘金](https://juejin.cn/post/6844903605720317959)

- [Cmd Markdown 公式指导手册 - 作业部落 Cmd Markdown 编辑阅读器](https://www.zybuluo.com/codeep/note/163962)
