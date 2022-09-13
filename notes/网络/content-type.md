# HTTP Header 中的 Content-Type

## 规范

Content-Type 语法如下：

```js
Content-Type: text/html; charset=UTF-8
Content-Type: multipart/form-data; boundary=something
```

Content-Type 包含三个指令：

- media type：声明传输数据的媒体类型（ [MIME](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FHTTP%2FBasics_of_HTTP%2FMIME_types "https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/MIME_types") ）；
- charset：声明传输数据采用的是何种字符集；
- boundary：数据分界符，有多部分数据实体时（multipart/form-data、multipart/byteranges），该指令是必需的，用于封装消息的多个部分的边界；其由 1 到 70 个字符组成，浏览器中会自动生成，该字符集对于通过网关鲁棒性良好，不以空白结尾。

## POST 请求常用数据类型

### application/x-www-form-urlencoded

该值是 Form 默认的编码方式，使用该值时，提交表单时内容必须经过如下规则编码：

1. 空格转换为 “+” 号；非字母数字的其它字符转换为类似于“%E0”的两位 16 进制表示的 ASCII 码；换行符被转换为“CR LF”；
2. 数据项名称和数据值以“=”号分割，数据项与数据项之间以“&”分割；

```html
<form action="https://xxx.com/api/submit" method="post">
    <input type="text" name="name" value="Javon Yan">
    <input type="text" name="age" value="18">
    <button type="submit">Submit</button>
</form>
```

**请求头及请求体：**

```js
// Request Header 部分省略
POST /foo HTTP/1.1
Content-Length: 37
content-type: application/x-www-form-urlencoded

// Body
name=Javon+Yan&age=18
```

### multipart/form-data

对于二进制文件或者非 ASCII 字符的传输，`application/x-www-form-urlencoded` 是低效的。对于包含文件、二进制数据、非 ASCII 字符的内容，应该使用 `multipart/form-data`。 `multipart/form-data` 的请求体包含多个部分，需要通过 boundary 字符分割。

以下示例中，Form 表单 enctype 设置为 `multipart/form-data` ，请求头及请求体如下所示，浏览器自动生成随机的 boundary 并添加在请求头 Content-Type 中，请求体也根据生成的 boundary 分割各个字段的数据。

```html
<form action="https://xxx.com/api/submit" method="post" enctype="multipart/form-data">
    <input type="text" name="name" value="Javon Yan">
    <input type="text" name="age" value="18">
    <input type="file" name="file">
    <button type="submit">Submit</button>
</form>
```

**请求头及请求体：**

```js
// Request Header 部分省略
POST /foo HTTP/1.1
Content-Length: 10240
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryujecLxDFPt6acCab

// Body
------WebKitFormBoundaryujecLxDFPt6acCab
Content-Disposition: form-data; name="name"

Javon Yan
------WebKitFormBoundaryujecLxDFPt6acCab
Content-Disposition: form-data; name="age"

18
------WebKitFormBoundaryujecLxDFPt6acCab
Content-Disposition: form-data; name="file"; filename="avatar.png"
Content-Type: image/png

... (png binary data) ....
------WebKitFormBoundaryujecLxDFPt6acCab--
```

### application/json

`application/json` 作为响应头比较常见，目前也流行在 POST 请求中使用，以序列化的 JSON 字符串形式传输，更易于后端解析，可读性更高。

微信小程序中 wx.request API 默认便是使用此方式传输数据。

### application/octet-stream

用于传输二进制数据。可用于上传文件的场景。在 Postman 中，还可以看到 "binary" 这一类型，指的就是一些二进制文件类型。如 `application/pdf`，指定了特定二进制文件的 MIME 类型。就像对于text文件类型若没有特定的子类型（subtype），就使用 `text/plain`。类似的，二进制文件没有特定或已知的 subtype，就使用 `application/octet-stream`，这是应用程序文件的默认值，一般很少直接使用 。

对于 `application/octet-stream`，只能提交二进制，而且只能提交一个二进制，如果提交文件的话，只能提交一个文件，后台接收参数只能有一个，而且只能是流（或者字节数组）。

很多 web 服务器使用默认的 `application/octet-stream` 来发送未知类型。出于一些安全原因，对于这些资源浏览器不允许设置一些自定义默认操作，导致用户必须存储到本地以使用。一般来说，设置正确的MIME类型很重要。

## 其它常见值

| 类型            | 描述                                     | 典型示例                                                                                                                                             |
| ------------- | -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| `text`        | 表明文件是普通文本，理论上是人类可读                     | `text/plain`, `text/html`, `text/css, text/javascript`                                                                                           |
| `image`       | 表明是某种图像。不包括视频，但是动态图（比如动态gif）也使用image类型 | `image/gif`, `image/png`, `image/jpeg`, `image/bmp`, `image/webp`, `image/x-icon`, `image/vnd.microsoft.icon`                                    |
| `audio`       | 表明是某种音频文件                              | `audio/midi`, `audio/mpeg, audio/webm, audio/ogg, audio/wav`                                                                                     |
| `video`       | 表明是某种视频文件                              | `video/webm`, `video/ogg`                                                                                                                        |
| `application` | 表明是某种二进制数据                             | `application/octet-stream`, `application/pkcs12`, `application/vnd.mspowerpoint`, `application/xhtml+xml`, `application/xml`,  `application/pdf` |

### 图片类型

| MIME 类型         | 图片类型                    |
| --------------- | ----------------------- |
| `image/gif`     | GIF 图片 (无损耗压缩方面被PNG所替代) |
| `image/jpeg`    | JPEG 图片                 |
| `image/png`     | PNG 图片                  |
| `image/svg+xml` | SVG图片 (矢量图)             |

### [音频与视频类型](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/MIME_types#%E9%9F%B3%E9%A2%91%E4%B8%8E%E8%A7%86%E9%A2%91%E7%B1%BB%E5%9E%8B "Permalink to 音频与视频类型")

| MIME 类型                                                 | 音频或视频类型                                                                                                                                                                             |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `audio/wave` `audio/wav` `audio/x-wav` `audio/x-pn-wav` | 音频流媒体文件。一般支持PCM音频编码 (WAVE codec "1") ，其他解码器有限支持（如果有的话）。                                                                                                                             |
| `audio/webm`                                            | WebM 音频文件格式。Vorbis 和 Opus 是其最常用的解码器。                                                                                                                                                |
| `video/webm`                                            | 采用WebM视频文件格式的音视频文件。VP8 和 VP9是其最常用的视频解码器。Vorbis 和 Opus 是其最常用的音频解码器。                                                                                                                  |
| `audio/ogg`                                             | 采用OGG多媒体文件格式的音频文件。 Vorbis 是这个多媒体文件格式最常用的音频解码器。                                                                                                                                      |
| `video/ogg`                                             | 采用OGG多媒体文件格式的音视频文件。常用的视频解码器是 Theora；音频解码器为Vorbis 。                                                                                                                                  |
| `application/ogg`                                       | 采用OGG多媒体文件格式的音视频文件。常用的视频解码器是 Theora；音频解码器为Vorbis 。                                                                                                                                  |
| `application/json`                                      | application/json (MIME_type)<br>[Media type - Wikipedia](https://en.wikipedia.org/wiki/Media_type#Common_examples)<br>https://www.iana.org/assignments/media-types/application/json |
