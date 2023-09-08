# nestjs学习

## 原理

### nest 的核心实现原理

**通过装饰器给 class 或者对象添加 metadata，并且开启 ts 的 emitDecoratorMetadata 来自动添加类型相关的 metadata，然后运行的时候通过这些元数据来实现依赖的扫描，对象的创建等等功能。**

### 装饰器的实现原理

就是 Reflect.getMetadata、Reflect.defineMetadata 这些 api。通过在 class、method 上添加 metadata，然后扫描到它的时候取出 metadata 来做相应的处理来完成各种功能。

Nest 的 Controller、Module、Service 等等所有的装饰器都是通过 Reflect.meatdata 给类或对象添加元数据的，然后初始化的时候取出来做依赖的扫描，实例化后放到 IOC 容器里。

实例化对象还需要构造器参数的类型，这个开启 ts 的 emitDecoratorMetadata 的编译选项之后， ts 就会自动添加一些元数据，也就是 design:type、design:paramtypes、design:returntype 这三个，分别代表被装饰的目标的类型、参数的类型、返回值的类型。

## 命令

```bash
# 安装
npm install -g @nestjs/#cli

# 更新
npm update -g @nestjs/cli

# 创建项目
nest new 项目名


# 生成一个module
nest generate module aaa

# 生成一个controller 
nest generate controller aaa

# 生成一个service 
nest generate service aaa

# 生成一个模块完整的代码
nest generate resource xxx


# 使用 tsc 或者 webpack 构建代码
nest build
# 启动开发服务，支持 watch 和调试 
nest start 
# 打印 node、npm、nest 包的依赖版本
nest info 


# 调试
nest start --debug
```

## Nest 知识点

### 流程

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/24060e0f32204907887ede38c1aa018c~tplv-k3u1fbpfcp-jj-mark:1512:0:0:0:q75.png)

- 首先，请求会被 middleware 处理，这一层可以复用 express 的中间件生态，实现 session、static files 等功能。这个 middleware 也可以是 Nest 的那种 class 的 middleware，可以注入 provider。

- 然后在具体的路由会经历 Guard 的处理，它可以通过 ExecutionContext 拿到目标 class、handler 的metadata 等信息，可以实现权限验证等功能。

- 之后是 Interceptor 可以在请求前后做一些处理，它也可以通过 ExecutionContext 拿到 class、handler 的信息。

- 在到达 handler 之前，还会对参数用 Pipe 做下检验和转换。

- 这个过程中不管是哪一层抛的异常，都会被 Exception Filter 处理下，返回给用户友好的响应信息。

### Nest 全部的装饰器

- @Module： 声明 Nest 模块
- @Controller：声明模块里的 controller
- @Injectable：声明模块里可以注入的 provider
- @Inject：通过 token 手动指定注入的 provider，token 可以是 class 或者 string
- @Optional：声明注入的 provider 是可选的，可以为空
- @Global：声明全局模块
- @Catch：声明 exception filter 处理的 exception 类型
- @UseFilters：路由级别使用 exception filter
- @UsePipes：路由级别使用 pipe
- @UseInterceptors：路由级别使用 interceptor
- @SetMetadata：在 class 或者 handler 上添加 metadata
- @Get、@Post、@Put、@Delete、@Patch、@Options、@Head：声明 get、post、put、delete、patch、options、head 的请求方式
- @Param：取出 url 中的参数，比如 /aaa/:id 中的 id
- @Query: 取出 query 部分的参数，比如 /aaa?name=xx 中的 name
- @Body：取出请求 body，通过 dto class 来接收
- @Headers：取出某个或全部请求头
- @Session：取出 session 对象，需要启用 express-session 中间件
- @HostParm： 取出 host 里的参数
- @Req、@Request：注入 request 对象
- @Res、@Response：注入 response 对象，一旦注入了这个 Nest 就不会把返回值作为响应了，除非指定 passthrough 为true
- @Next：注入调用下一个 handler 的 next 方法
- @HttpCode： 修改响应的状态码
- @Header：修改响应头
- @Redirect：指定重定向的 url
- @Render：指定渲染用的模版引擎

### Nest 的 middleware 和 interceptor 都是在请求前后加入一些逻辑的，这俩区别是啥呢？

- interceptor 是能从 ExecutionContext 里拿到目标 class 和 handler，进而通过 reflector 拿到它的 metadata 等信息的，这些 middleware 就不可以。

- 再就是 interceptor 里是可以用 rxjs 的操作符来组织响应处理流程的，middleware 里也不可以。

> 它们都是 Nest AOP 思想的实现，但是 interceptor 更适合处理与具体业务相关的逻辑，而 middleware 适合更通用的处理逻辑。

### nest 的 interceptor 就用了 rxjs 来处理响应，但常用的 operator 也就这么几个：

- tap: 不修改响应数据，执行一些额外逻辑，比如记录日志、更新缓存等
- map：对响应数据做修改，一般都是改成 {code, data, message} 的格式
- catchError：在 exception filter 之前处理抛出的异常，可以记录或者抛出别的异常
- timeout：处理响应超时的情况，抛出一个 TimeoutError，配合 catchErrror 可以返回超时的响应

### pipe

内置的 Pipe 有这些：

- ValidationPipe
- ParseIntPipe
- ParseBoolPipe
- ParseArrayPipe
- ParseUUIDPipe
- DefaultValuePipe
- ParseEnumPipe
- ParseFloatPipe
- ParseFilePipe

### IOC 和 AOP

- 通过 IOC 实现了对象的自动创建、依赖的自动组装。

- 通过 AOP 实现了通用逻辑的抽取和复用。

## docker

### 命令

- FROM：基于一个基础镜像来修改
- WORKDIR：指定当前工作目录
- COPY：把容器外的内容复制到容器内
- EXPOSE：声明当前容器要访问的网络端口，比如这里起服务会用到 8080
- RUN：在容器内执行命令
- CMD：容器启动的时候执行的命令

```dockerfile
FROM node:latest

WORKDIR /app

COPY . .

RUN npm config set registry https://registry.npmmirror.com/

RUN npm install -g http-server

EXPOSE 8080

CMD ["http-server", "-p", "8080"]
```

## 资料

- [json.schemastore.org/nest-cli](https://link.juejin.cn/?target=https%3A%2F%2Fjson.schemastore.org%2Fnest-cli "https://json.schemastore.org/nest-cli")
- [RxJS - operator](https://rxjs.dev/guide/operators#creation-operators-1)
