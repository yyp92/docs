# React的页面传值，路由跳转

## search传值（拼接url的传值方式）

### 特点：刷新不消失

```js
var path = {
    pathname: "/home",
    search: `?id=12` 
}

// 跳转
history.push(path);

// 取
props.location.search;//得到的结果是字符串  ?id=12
```

### 或者直接拼接url（最推荐）

```js
// 直接拼接url方式，简单粗暴
history.push("/home?id=12&name=zqq")
```

## params---定义路由方式

### 特点：需要在路由表中，先定义路由参数

（1）定义路由

```js
// 该路由表示后面可以拼接一个名为id的参数
<Route path="/home/:id"></Route>
```

（2）用法

```js
// Link组件跳转
<Link to="/home/26">用户</Link>

// 函数式跳转
history.push("/home/37")

// 取参数
props.params.id
```

## query方式

### 特点：刷新页面，参数消失；复制链接打开新页面，没有参数

用法

```js
var path = { 
    pathname: "/home",
    query: {id: 3,name: "zqq"}
};

// Linke组件跳转
<Link to={path}>用户<Link>

// history函数跳转
history.push(path)

// 取参数
props.location.query;
```

## state方式

### 特点：刷新不会消失；复制链接打开新页面，没有参数

用法和写法与query相同;

```js
var path = { 
    pathname: "/home",
    state: {id: 3,name: "zqq"}
};

// Linke组件跳转
<Link to={path}>用户<Link>

// history函数跳转
history.push(path)

// 取参数
props.location.state;
```

# histroy--路由信息

注意：在页面中，可以通过props.history获得；在子组件中，要么把父组件的history传给子组件，要么使用withRouter高阶组件包裹子组件，或者通过useHistory来获取路由信息；

```js
// useHistory方式
import { useHistory } from "react-router";

const history = useHistory();

history.push({
    pathname: "/search",
    search: `?source=bounty`
})
```
