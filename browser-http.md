### 从输入url到展示的过程
  1. DNS解析，解析出域名对应的IP地址
  2. 建立TCP连接，三次握手
  3. 发送请求，分析url，设置请求报文（头，体）
  4. 服务器返回请求的数据
  5. 浏览器根据返回的数据
     1. HTML parser --> DOM Tree。解析HTML结构，构建成 AST DOM树
        - 标记化算法，进行元素状态的标记
        - dom 树构建
     2. CSS parser --> Style Tree。解析CSS， 构建 AST STYLE 样式树
        - 解析css代码，生成样式树
     3. attachment --> Render Tree。
        - 结合 dom树 和 style树，生成渲染树
     4. layout 布局
     5. GPU painting：像素绘制页面


### Http 缓存机制

- http 1.0 和 http 1.1 缓存的区别
  - http 1.0 使用 Expires 头标记缓存时间
  - http 1.0 使用 cache-control 头标记缓存机制，可以使用该属性不同的属性值定义所需的缓存机制

- Cache-Control 属性值
  - no-store 不缓存，每次发送请求到服务器获取内容，服务器返回全部内容
  - max-age = 1000000 标记过期时间。在该时间内都不会向服务器发起请求，过期后发送请求请求内容并更新过期时间
  - private 私人缓存，即只允许客户端缓存
  - public 公开缓存，客户端和服务器（shared cache -> 中间服务器）都可以缓存
  - no-cache 缓存但是需要重新校验，如果服务器返回校验规则。使用缓存前需要向服务器发送请求校验文件是否有改变，若改变了，服务器返回(200状态码)最新内容并在请求头中表面最新校验信息；如果没改变，服务器返回 304 状态码，报文中只包含头部信息，客户端使用缓存。
  - must-revalidate 必须重新校验

- 校验规则
  - Last-Modified/If-Modified-Since
    - 如果服务器返回的报文中包含 `Last-Modified: Wed, 21 Oct 2015 07:28:00 GMT` 信息
    - 客户端在发送请求报文是，会包含 `If-Modified-Since/If-Unmodified-Since: Last-Modified: Wed, 21 Oct 2015 07:28:00 GMT` 信息
    - 服务端会对比 Last-Modified 和 If-Modified-Since/If-Unmodified-Since 信息，如果服务端的时间在之后，接口返回 200，返回最新内容，包括最新的 Last_Modified; 如果是之前或者相等，表示没有修改，返回 304 状态码，报文中只包含头部信息，客户端使用缓存。

  - ETag/If-None-Match
    - `ETag: W/"<etag_value>"` W/ 表示使用弱校验器(Weak Validator)(待探索...)。etag_value 通常来说可以使用 内容的哈希、上次修改时间戳的哈希或仅使用修订号等等。
    - 如果服务器返回的报文中包含 `ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"` 或者 `ETag: W/"0815"` 信息
    - 客户端在发送请求报文是，会包含 `If-None-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"` 信息
    - 服务端会对比匹配 33a64df551425fcc55e4d42a148795d9f25f89d4 如果匹配到，表示没有修改，返回 304 状态码，报文中只包含头部信息，客户端使用缓存。如果没匹配到，接口返回 200，返回最新内容，包括最新的 ETag;

### 为什么是三次握手和四次挥手
  - 三次握手的原因：其实是四次，只不过在服务器进行握手的时候，同时将 SYN（synchronous） 和 ACK（acknowledgement） 放到一个报文里面传送给客户端了，这样就减少了一次发送报文的时间，提高了效率。
  - 四次挥手的原因：这是因为当客户端向服务端发送FIN报文时，只是表示客户端不会向服务端发送数据了。此时服务端会返回一个 ACK报文 给客户端。但是服务端可能还没有传输完数据，需要继续发送数据给客户端，客户端也可以继续接收数据。当服务端确定发送完数据时，向客户端发送 FIN（finish）报文，表示服务端发送完数据，可以关闭链接，客户端返回 ACK 报文后，链接关闭。