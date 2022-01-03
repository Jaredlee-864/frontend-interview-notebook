##### node 可以做什么？
  - 轻量级、高性能的 Web 服务
  - 前后端 JavaScript 同构开发
  - 便捷高效的前端工程化

##### Nodejs 架构
  <image src="./images/nodejs架构.png" />

  - Natives modules
    - 当前层内容由js实现
    - 提供应用程序可以直接调用的库，如 fs\path\http 等
    - js 语言无法直接操作底层硬件设置，需要一个桥梁，即 Builtin Modules
  - Builtin Modules "胶水层"
    - 主要由 c++ 代码编写而成
  - 具体的功能模块儿
    - V8：执行 JS 代码，提供桥梁接口
    - Libuv: 事件循环、事件队列、异步IO
    - 第三方功能模块：zlib、http、c-ares 等

##### 为什么是 node ？
  - Reactor，单线程完成多线程的操作
  - node基于reactor模式，结合js语言本身的 单线程模式、事件循环架构、异步编程等特定，让单线程远离阻塞，从而通过异步非阻塞IO更好的使用CPU的资源，并且实现高并发的请求处理
  - nodejs 更适用于 IO 密集型高并发请求，不适用于大量且复杂的业务处理。

##### nodejs异步IO
  - IO 是应用程序的瓶颈所在
  - 异步 IO 提高性能，无需原地等待结果返回
  - IO 操作属于操作系统几倍，平台都有对应的实现
  - Nodejs 单线程配合事件驱动框架以及Libuv实现的异步IO

##### nodejs 事件驱动框架

##### nodejs 单线程
  - nodejs 单线程指的是 nodejs 的主线程是单线程的

##### nodejs 的应用场景
  - 操作数据库提供API服务
  - 实时聊天应用程序
  - nodejs 更加适合 IO 密集型任务

##### nodejs 全局变量
  - nodejs中全局变量是 global，global 的根本作用就是作为宿主。全局对象可以看做是全局变量的宿主
  - `__filename`: 返回正在执行脚本文件的绝对路径
  - `__dirname`: 返回正在执行脚本所在目录
  - timer 类函数：执行顺序与事件循环间的关系
  - process: 提供与当前进程互动的接口
    1. 资源： cpu 内存
      - `process.memoryUsage()` 内存使用
      - `process.cpuUsage()` cpu使用
    2. 运行环境：运行目录、node环境、cpu架构、用户环境、系统平台
      - `process.cwd` 运行目录
      - `process.version` node版本信息
      - `process.versions` node里所有类库的版本
      - `process.arch` cpu架构
      - `process.env.NODE_ENV` 当前node环境
      - `process.env.PATH`  当前系统配置的系统变量
      - `process.env.USERPROFILE/HOME(Mac)` 当前系统用户管理员目录
      - `process.platform` 当前操作系统
    3. 运行状态：启动参数、PID、运行时间
      - `process.argv` 启动参数
      - `process.argv0` 快捷操作，参数第一个值
      - `process.execArgv` --参数
      - `process.pid` PID
      - `process.uptime` 文件运行时间
    4. 事件：
      ```js
      process.on('exit', (code)=> {
        // exit 里只能执行同步代码
        console.log('exit' + code)
      })
      process.on('beforeExit', (code)=> {
        console.log('beforeExit' + code)
      })
      console.log("代码执行完了")
      process.exit() // 手动结束，不会触发上面两个事件
      ```
    5. 标准输出 输入 错误
    ```js
    console.log = function(data){
      process.stdout.write("---" + data + "\n")
    }

    proces.stdin.pipe(process.stdout)

    process.stdin.setEncoding("utf-8")
    process.stdin.on("readable", () => {
      let chunk = process.stdin.read()
      if (chunk != null) {
        process.stdout.write('data ' + chunk)
      }
    })
    ```
  - require: 实现模块的加载
  - module、export: 处理模块的导出



#### node 核心模块
##### path 模块
  - `path.basename()` 获取路径中基础名称
    - 返回的是路径中最后一部分
    - 第二个参数表示扩展名，如果说没有设置则返回完整的文件名称加后缀，设置并匹配到，则返回不加扩展名的文件名称
    - 第二个参数作为后缀时，如果没有在当前路径中被匹配到，那么就会hulve
    - 处理目录路径的时候，如果结尾处有路径分隔符，则也会被忽略掉
  - `path.dirname()` 获取路径中目录名称
    - 返回路径中最后一个部分的上一层目录所在路径
  - `path.extname()` 获取路径中扩展名称
    - 返回path路径中相应文件的后缀名
    - 如果 path 路径当中存在多个点，它匹配的是最后一个点
  - `path.isAbsolute()` 获取路径是否为绝对路径
  - `path.join()` 拼接多个路径
  - `path.resolve()` 返回绝对路径
    - `resolve([from], to)`
    - from 内容默认会传入当前工作目录
    ```js
    path.resolve('a','b'); // 此时 a b 都属于to的部分，结果是 当前工作目录/a/b
    path.resolve('/a','b'); // 此时 /a 属于 from 部分，b 属于to的部分，结果是 /a/b
    path.resolve('a','/b'); // 此时 a 属于 from 部分，/b 属于to的部分，此时会考虑a和/b有没有关系，此处没有任何关系，返回 /b

    // 实际使用的时候，例如找index.html的全部目录
    path.resolve("index.html")
    ```
  - `path.parse()` 解析路径
    ```js
    const obj = path.parse('/a/b/c/index.html')
    /* obj = {
      root: '/',
      dir: '/a/b/c',
      base: 'index.html',
      ext: '.html',
      name: 'index'
    } */
    ```
  - `path.format()` 序列化路径
  - `path.normalize()` 规范化路径

##### Buffer （Buffer缓冲区）
  1. Buffer 是什么？
   - 无须 require 的一个全局变量
   - 实现 Nodejs 平台下的二进制数据操作
   - Buffer 是不占据 V8 堆内存大小的一片内存空间
   - Buffer 内存的使用由 Node 来控制，由 V8 的 GC 回收
   - 一般配合 Stream 流使用，充当数据缓冲区
  2. 创建 Buffer 实例
   - alloc: 创建指定字节大小的 buffer
    ```js
    const b1 = Buffer.alloc(10);
    ```
   - allocUnsafe: 创建制定大小的 buffer （不安全）
    ```js
    const b2 = Buffer.allocUnsafe(10);
    ```
   - from：接收数据，创建 buffer
    ```js
    // from 接收三种类型的参数，string，array，buffer
    const b3 = Buffer.from("1");
    const b4 = Buffer.from([1, 2, 3]);
    
    const b5 = Buffer.alloc(2);
    const b6 = Buffer.from(b5);
    // 注b5 和 b6 不是同一片空间，修改其中任何一个不会影响到另一个
    ```
  3. Buffer 实例方法
   - fill: 使用数据填充 buffer，并返回填充后的buffer
    ```js
    let buf = Buffer.alloc(6);
    buf.fill("123"); // 123123
    buf.fill('123456767'); // 123456
    buf.fill('123', 1, 3); // 从第一位开始填充，到第三位结束，不包括第三位。典型的顾头不顾尾
    console.log(buf.toString()) // 12
    buf.fill(123); // 数字类型的话，每个位置上都填充数字123
    ```
   - write： 向 buffer 中写入数据
    ```js
    let buf = Buffer.alloc(6);
    buf.write("123"); // 123
    buf.write('123456767'); // 123456
    buf.write('123', 1, 3); // 从第一位开始写入，到第三位结束，不包括第三位。典型的顾头不顾尾
    ```
   - toString: 从 buffer 中提取数据，按照制定的编码方式进行编码
    ```js
    let buf = Buffer.from('测试测试');
    buf.toString(); // 测试测试
    buf.toString('utf-8', 3, 9); // 试测
    ```
   - slice: 截取 buffer
    ```js
    let buf = Buffer.from('测试测试');
    const b1 = buf.slice(); // 测试测试
    const b2 = buf.slice(3); // 试测试
    const b3 = buf.slice(3, 9); // 试测
    const b4 = buf.slice(-3); // 试
    ```
   - indexOf: 在 buffer 中查找数据，并返索引值
     - 用法同数组的 indexOf 操作
   - copy: 拷贝 buffer 中的数据
    ```js
    let b1 = Buffer.alloc(6);
    let b2 = Buffer.from('测试')；

    b2.copy(b1, 3, 3, 6)
    // 第二个参数表示从b1的第三个位置开始写入
    // 第三个和第四个表示从 b2 的那个位置开始读和到哪个位置结束
    ```
  4. Buffer 静态方法
    - concat: 将多个buffer拼接成一个新的 buffer
    - isBuffer: 判断当前数据是否为 buffer
  5. 自定义 Buffer 之 split
   ```js
   ArrayBuffer.prototype.split = function(seperator) {
     let len = Buffer.from(seperator).length
     let result = []
     let start = 0
     let offset = 0

     while( offset = this.indexOf(seperator, start) !== -1) {
       result.push(this.slice(start, offset))
       start = offset + len
     }
     this.slice(start)
     return result
   }
   ```

##### FS 模块
  - 文件基本常识
     1. 权限位
        - r 可读
        - w 可写
        - x 可执行
      一般的文件都是可读可写不可执行，转换成10进制的数字为438（常用）
     2. 标识符
        - r 可读
        - w 可写
        - s 同步
        - + 执行相反操作
        - x 排他操作
        - a 追加操作
     3. 描述符
        - fd 就是操作系统分配给被打开文件的标识（nodejs中从3开始，0，1，2 被标准输入输出错误占了）
  - 文件操作API（任何API都有同步和异步操作）
    - readFile: 从指定文件中读取数据
    - writeFile: 向指定文件中写入数据
    - appendFile: 追加的方式向指定文件中写入数据
    - copyFile: 将某个文件中的数据拷贝至另一文件
    - watchFile: 对指定文件进行监控
      ```js
      fs.watchFile('data.txt', {interval: 20}, (curr, prev)=> {
        // mtime 为文件的修改时间
        if(curr.mtime !== prev.mtime){
          console.log('文件被修改了')
        }
      })
      ```
  - 文件的打开与关闭
    ```js
    // open
    fs.open(path.resolve('data.txt', 'r', (err, fd) => {
      console.log('opened', fd);
      // close
      fs.close(fd, (err)=> {
        console.log("closed")
      })  
    }))
    ```
  - 大文件读写操作
    ```js
    const fs = require('fs')

    // read: 所谓的读操作，是将数据从磁盘文件中写入到 buffer 中
    let buf = Buffer.alloc(10)

    fs.open('data.txt', 'r', (err, rfd) => {
      console.log(rfd)
      // 0 代表offset 代表从buffer的哪个位置开始写入
      // 3 代表length 读取的长度
      // 0 代表position 从文件中的哪个位置开始读
      fs.read(rfd, buf, 0, 3, 0, (err, readBytes, data) => {
        console.log(readBytes) // 读取了多少字节
        console.log(data)
      })
    })

    // write  将缓冲区里面的内容写入到磁盘中

    buf = Buffer.from("1234567890")
    fs.open('b.txt', 'w', (err, wfd) => {
      // 0 offset 代表从 buffer 的哪个文职开始读
      // 3 length 读取的长度
      // 0 position 从文件的哪个位置开始写
      fs.write(wfd, buf, 0, 3, 0, (err, written, buffer) => {
        // written 实际写入的字节数
        console.log(written)
        console.log(buffer)
        console.log(buffer.toString())
        fs.close(wfd, (err) => {
          console.log(wfd + "closed")
        })
      })
    })
    ```
  - 文件拷贝自定义实现
    ```js
    const buf = Buffer.alloc(10)
    // 打开a文件，利用 read 将数据保存到 buffer 暂存起来
    // 打开b文件，利用 write 将 buffer 暂存数据写入 b 文件

    const BUFFER_SIZE = buf.length
    let readOffset = 0
    fs.open('a.txt', 'r', (err, rfd) => {
      fs.open('b.txt', 'a+', (err, wfd) => {
        function next(){
            fs.read(rfd, buf, 0, BUFFER_SIZE, readOffset, (err, readbytes) => {
              if(!readbytes){
                //如果条件成立，说明已经读完
                fs.close(rfd, ()=>{})
                fs.close(wfd, ()=>{})
                console.log('copied')
                return
              }
              readOffset += readbytes
            fs.write(wfd, buf, 0, readbytes, 0, (err, written) => {
              next()
            })
          })
        }
        next();
      })
    })
    ```
  - 目录操作 API
    - access: 判断文件或目录是否具有操作权限
      ```js
      fs.access('a.txt', (err) => {
        if (err) {
          console.log(err)
        } else {
          console.log('有操作权限')
        }
      })
      ```
    - stat: 获取目录及文件信息
      ```js
      fs.stat('a.txt', (err, statObj) => {
        console.log(statObj.size)
        console.log(statObj.isFile())
        console.log(statObj.isDirectory())
      })
      ```
    - mkdir: 创建目录（默认创建basename文件夹，不加recursive时）
      ```js
      fs.mkdir('a/b/c', { recursive: true },(err) => {
        if (!err) {
          console.log('创建成功')
        } else {
          console.log(err)
        }
      })
      ```
    - rmdir: 删除目录（默认删除basename文件夹，不加recursive时）
      ```js
      fs.rmdir('a/b/c', { recursive: true }, (err) => {
        if (!err) {
          console.log('删除成功')
        } else {
          console.log(err)
        }
      })
      ```
    - readdir: 读取目录中的内容
      ```js
      fs.readdir('a/b',(err, files) => {
        console.log(files)
      })
      ```
    - unlink: 删除指定文件
      ```js
      fs.unlink('a/a.txt',(err) => {
        if (!err) {
          console.log('删除成功')
        }
      })
      ```
  - 创建目录之同步实现
    ```js
    const fs = require('fs')
    const path = require('path')
    /**
     * 01 调用时需要接收类似的 a/b/c 的路径，之间使用 / 进行链接
     * 02 利用 / 分割符将路径进行拆分，将每一项放入一个数组中进行管理 ['a', 'b', 'c']
     * 03 遍历上述数组，我们需要拿到每一项，然后与前一项进行拼接 /
     * 04 判断一个当前对拼接之后的路径是否具有可操作性权限，如果有则证明存在，否则的话需要执行创建
     * */
    function makeDirSycn(dirPath) {
      let items = dirPath.split(path.sep)
      for(let i = 1; i<= items.length; i++) {
        let dir = items.slice(0, i).join(path.sep)
        try {
          fa.assessSync(dir)
        }catch(e) {
          fs.mkdirSync(dir)
        }
      }
    }
    ```
  - 创建目录之异步实现
    ```js
    const fs = require('fs')
    const path = require('path')
    function mkdir(dirPath, cb){
      let parts = dirPath.split("/")
      let index = 1
      
      function next() {
        in (index > parts.length) return cb && cb()

        let current = parts.slice(0, index++).join('/')

        fs.asscess(current, (err) => {
          if(err) {
            fs.mkdir(current, next)
          } else {
            next()
          }
        })
      }
    }
    next()

    mkdir('a/b/c', () => {
      console.log('创建成功')
    })

    // 将 access 与 mkdir 处理成 async... 风格
    const { promisify } = require('util')
    const access = promisify(fs.access)
    const mkdir = promisify(fs.mkdir)

    async function myMkdir(dirPath, cb) {
      let parts = dirPath.split('/')
      for(let i = 1; i <= parts.length; i++){
        let current = parts.slice(0, i).join('/')
        try {
          await access(current)
        } catch (err) {
          await mkdir(current)
        }
      }
      cb && cb()
    }
    ```
  - 递归删除目录
    ```js
    const fs = require('fs')
    const path = require('path')
    
    /**
     * 需求： 自定义一个函数，接收一个路径，然后执行操作
     * 01 判断当前传入的路径是否为一个文件，直接删除当前文件即可
     * 02 如果当前传入的是一个目录，我们需要继续读取目录中的内容，然后再执行删除操作
     * 03 将删除行为定义为一个函数，然后通过递归方式调用
     * 04 将当前的名称拼接成在删除时可使用的路径
     * */

    function myRmdir (dirPath, cb) {
      // 判断当前 dirPath 类型
      fs.stat(dirPath, (err, statObj) => {
        if(statObj.idDirectory()) {
          // 目录 --> 继续读取
          fs.readdir(dirPath, (err, files) => {
            let dirs = files.map(item => {
              return path.join(dirPath, item)
            })
            let index = 0
            function next(){
              if(index === dirs.length) return fs.rmdir(dirPath, cb)

              let current = dirs[index ++]

              myRmdir(current, next)
            }
            next()
          })
        } else {
          // 文件 --> 直接删除
          fs.unlink(dirPath, cb)
        }
      })
    }
    ```

  - 模块化历程
    - 模块化是前端走向工程化中的重要一环
    - 早起JavaScript语言层面没有模块化的规范
    - 常见的模块化规范
      - Commonjs 规范
        - CJS 是个超集，是整个语言层面上的规范，类似于ECMAScript。模块化只是众多规范其中的一部分，还有IO流、二进制操作和buffer操作等
        - CJS中的模块加载都是同步完成的，不适合在浏览器中运行
      - AMD规范
      - CMD规范
        - 整合 CJS 和 AMD 的产物
      - ES modules 规范
    - ES6 中将模块化纳入标准规范
    - 当下常用规范是 CommonJS 与 ESM

  - Commonjs 规范
    - 分三个部分
      - 模块引用
      - 模块定义
      - 模块标识
    - nodejs 与 Commonjs
      - 任意一个文件就是一模块，具有独立的作用域
      - 使用 require 导入其他模块
      - 将模块 ID 传入 require 实现目标模块定位
    - nodejs 与 commonjs 关联
      - module 属性
        - 任意js文件就是一个模块，可以直接使用module属性
        - id: 返回模块标识符，一般是一个绝对路径
        - filename: 返回文件路径的绝对模块
        - loaded: 返回布尔值，标识模块是否完成加载
        - parent: 返回对象存放调用当前模块的模块
        - children: 返回数组，存放当前模块调用的其他模块
        - exports: 返回当前模块需要暴露的内容
        - paths: 返回数组，存放不同目录下的node_modules位置
      - exports 属性
        - module.exports 与 exports 的区别
          node中通过 module.exports 执行导出操作，而 exports 是 node 中为了方便书写，给每个提供的一个变量，指向 module.exports 的内存地址。因此可以使用 exports 直接导出内容。**但是不能给 exports 重新赋值，重新赋值 exports 就会和 module.exports 断开联系，无法再使用 exports 进行内容导出**
        - require 属性
          - 基本功能是读入并且执行一个模块文件
          - resolve: 返回模块文件绝对路径
          - extensions: 依据不同后缀名执行解析操作
          - main: 返回主模块对象

  - 模块分类及加载流程
    - 模块分类
      - 内置模块
      - 文件模块
    - 模块加载速度
      - 核心模块：Node源码编译时写入到二进制文件中
      - 文件模块：代码运行时，动态加载
    - 加载流程
      - 路径分析：依据标识符确定模块位置
        - 路径标识符
        - 非路径标识符（常见于核心模块）
        - 查找策略（模块路径）
          - module.paths 的值。不同路径下的查找node_modules的路径
          - <image src="./images/module.paths 图.png"/>
          - 简单来说就是：从当前文件的父级目录开始，直到当前项目所在的盘符根目录结束
          - 使用 require 加载模块的时候，会遍历 module.paths 数组的每一项，直到找到位置。如果数组中的每一项都找不到，会抛出错误
      - 文件定位：确定目标模块中具体的文件及文件类型
        - 项目下存在 m1.js 模块，导入时使用 require('m1') 语法
        - 按照.js, .json, .node 的顺序补足文件名，之后查找文件：m1.js -> m1.json -> m1.node
        - 上述查找不到，node 认为这时 m1 为一个目录，这个时候会将 m1 当成一个包来处理。这个时候 node 也遵循了 commonjs 的规范，首先会从当前目录下查找package.json 文件，然后使用 JSON.parse() 解析，从而取出描述文件中的 main 属性值
        - 如果 main 属性依然没有扩展名，依然会按照.js, .json, .node 的顺序补足文件名，然后按照顺序查找
        - 如果上次查找依然找不到文件，node 会将 index 作为目标模块中的具体文件名称。依次查找 index.js, index.json, index.node
        - 如果还没有找到，会一层目录一层目录往上重复查找
        - 最终还没有找到，会抛出异常
      - 编译执行：采用对应的方式完成文件的编译执行
        - 将某个具体类型的文件按照相应的方式进行编译和执行
        - 创建新对象，按路径载入，完成编译执行
          - js 文件的编译执行
            - 使用fs模块同步读入目标文件内容
            - 对内容进行语法包装，生成可执行的 js 函数，并执行
            - 调用函数时传入 exports、modules、require 等属性值
          - json 文件编译执行
            - 将读取到的内容通过 JSON.parse() 进行解析，并将结果返回给exports对象
      - 注意一点：加载过程中的缓存优先原则
        - 提高模块的加载速度
        - 当前模块不存在，则经历一次完整的加载流程
        - 模块加载完成后，使用路径作为索引进行缓存

  - VM模块使用
    - 作用：创建独立运行的沙箱环境

  - 模块加载模拟实现
    - 核心逻辑
      - 路径分析
      - 缓存有限
      - 文件定位
      - 编译执行
    ```js
    const fs = require('fs')
    const path = require('path')
    const vm = require('vm')

    function Module(id){
      this.id = id;
      this.exports = {}
    } 

    Module._extensions = {
      '.js'(module) {
        // 读取
        let content = fs.readFileSync(module.id, 'utf-8')

        // 包装
        content = Module.wrapper[0] + content + Module.wrapper[1]

        // VM
        let compileFn = vm.runInThisContext(content)

        // 准备参数的值
        let exports = module.exports
        let dirname = path.filename(module.id)
        let filename = module.id

        // 调用
        compileFn.call(exports, exports, module, myRequire, filename, dirname)

      }
      '.json'(module) {
        // 读取
        let content = JSON.parse(fs.readFileSync(module.id, 'utf-8'))

        module.exports = content
      }
    }

    Module.wrapper = [
      '(function(){',
      '})'
    ]

    Module._resolveFilename = function(filename){
      // 利用 path 将 filename 转为绝对路径
      let absPath = path.resolve(__dirname, filename);

      if (fs.existSync(absPath)) {
        // 条件成立说明 abs 对应的内容存在
        return absPath
      } else {
        // 文件定位
        let suffix = Object.keys(Module._extensions)
        
        for (let i = 0; i < suffix.length; i++){
          let newPath = absPath + suffix[i];
          if(fs.existSync(newPath)){
            return newPath
          }
        }
      }
      throw new Error(`${filename} is not exist` )
    }

    Module.prototype.load = function(){
      let extname = path.extname(this.id)
      Module._extension[extname](this)
    }

    function myRequire(filename){
      // 1、获取绝对路径
      let mPath = Module._resolveFilename(filename)

      // 2、缓存优先
      let cacheModule = Module._cache[mPath];

      if(cacheModule) return cacheModule.exports

      // 3、创建空对象，加载目标模块

      let module = new Module(mPath);
      
      // 4 缓存已加载过的模块
      Module._cache[mPath] = module;

      // 5 执行加载（编译执行）
      module.load()

      // 6 返回数据
      return module.exports
    }

    let obj = myRequire('./a')
    console.log(obj)
    ```
  - 事件模块
    - 通过 EventEmitter 类实现事件的统一管理
    - node.js 是基于事件驱动的异步操作架构，内置 events 模块
    - events 模块提供了 EventEmitter 类
    - node.js 中很多内置核心模块继承 EventEmitter(例如：fs, http ...)
    - EventEmitter 常见API
      - on: 添加当事件被触发时调用的回调函数
      - emit：触发事件，按照注册的顺序同步调用每一个事件监听器
      - once: 添加当事件在注册之后首次被触发时调用的回调函数
      - off: 移除特定的监听器

  - 发布订阅模式
    - 简单来说就是一个定义对象间一对多的依赖关系的模型，而且不同对象间还实现了解耦。解决了在promise还没出现之前的回调地狱的问题。
    - 要素
      - 缓存队列，存放订阅者的信息
      - 具有增加、删除订阅的能力
      - 状态改变时通知所有订阅者执行监听
    - 与观察者模式的区别
      - 发布订阅中存在订阅中心
      - 状态发生改变时，发布订阅无须主动通知
    - 简单代码实现
      ```js
      class PubSub {
        constructor(){
          this._events = {}
        }

        // 注册
        subscribe(event, callback) {
          if (this._events[event]){
            this._events.push(callback)
          } else {
            this._events[event] = [callback]
          }
        }

        // 发布
        publish(event, ...args) {
          const items = this._events[event]
          if(items && items.length){
            items.forEach(() => {
              callback.call(this, ...args)
            })
          }
        }
      }

      ```
  - EventEmitter 模拟实现
    ```js
    function MyEvent(){
      this._events = Object.create(null)
    }

    MyEvent.prototype.on = function(type, callback) {
      if(this._events[type]) {
        this._events[type].push(callback)
      } else {
        this._events[type]= [callback]
      }
    }

    MyEvent.prototype.emit = function(type, ...args) {
      if(this._events && this._events[type].length) {
        this._events[type].forEach((cb) => {
          cb.call(this, ...args)
        })
      } 
    }

    MyEvent.prototype.off = function(type, callback) {
      if(this._events && this._events[type]){
        this._events[type] = this._events[type].filter(item => item !== callback && item.link !== callback)
      }
    }

    MyEvent.prototype.once = function(type, callback) {
      let foo = function(...args){
        callback.call(this, ...args)
        this.off(type, foo)
      }
      foo.link = callback
      this.on(type, foo)
    }
    ```

  - 浏览器中的事件循环
    - 从上之下执行所有的同步代码
    - 执行过程中将遇到的宏任务与微任务添加至相应的队列
    - 同步代码执行完毕后，执行满足条件的微任务回调
    - 微任务队列执行完毕后执行所有满足需求的宏任务回调
    - 循环事件环操作
    - 注意：每执行一个宏任务之后就会立刻检查微任务队列

  - nodejs中的事件循环
    - 与浏览器不同，node有6个任务队列如下
      - timers: 执行setTimeout 与 setInterval 回调
      - pending callbacks: 执行系统操作的回调，例如 tcp, udp
      - idle, prepare: 只在系统内部进行使用
      - poll: 执行与 I/O 相关的回调
      - check: 执行 setImmediate 中的回调
      - close callbacks: 执行 close 事件的回调
    - 完整事件循环
      - 执行同步代码，将不同的任务添加至相应的队列
      - 所有同步代码执行后会去执行满足条件的微任务
      - 所有微任务代码执行后会执行 timer 队列中满足的宏任务
      - timer 中的所有宏任务执行完成后就会一次切换其他队列
      - 注意：在完成队列切换之前会先清空微任务代码
    <image src="./images/nodejs事件循环.png">
    - nodejs中 process.nextTick 为微任务，优先级高于 promise.then
    <image src="./images/nodejs事件循环分析.png">
  
  - node 与浏览器事件循环机制的不同
    - 任务队列数不同
      - 浏览器只有两个任务队列（宏任务 + 微任务）
      - nodejs中有6个事件队列（ 6 + 微任务）
    - nodejs微任务执行时机不同
      - 二者都会在同步代码执行完毕后执行微任务
      - 浏览器平台下每当一个宏任务执行完毕后，就会清空微任务
      - nodejs平台在事件队列切换时会去清空微任务（旧版，新版已于浏览器保持一致）
    - 微任务的优先级不同
      - 浏览器事件循环中，微任务存放于事件队列，先进先出
      - nodejs 中 process.nextTick 先于 promise.then

  - 核心模块之 stream
    - 流处理数据的优势
      - 时间效率：流的分段处理可以同时操作多个数据 chunk
      - 空间效率：同一时间流无须占据大内存空间
      - 使用方便：流配合管理，扩展程序变得简单
      - nodejs中内置了stream，它实现了流操作对象
    - nodejs中流的分类
      - Readable: 可读流，能够实现数据的读取
      - Writeable: 可写流，能够实现数据的写操作
      - Duplex：双工流，即可读又可写
      - Transform: 转换流，可读可写，还能实现数据转换
    - nodejs流特点
      - stream 模块实现了四个具体的抽象
      - 所有流都继承自 EventEmitter
    - 可读流
      - 消费数据
        - readable事件: 当流中存在可读取数据时触发
        - data事件:当六中数据块传给消费者后触发
      ```js
      const { Readable } = require('stream')

      // 模拟底层数据
      let source = ['a', 'b', 'c']

      // 自定义类继承 Readable
      class MyReadable extends Readable {
        constructor(source){
          super()
          this.source = source
        }
        _read() {
          let data = this.source.shift() || null;
          this.push(data)
        }

        // 实例化
        let myReabable = new MyReadabled(source)

        myReadable.on('readable', () => {
          let data = null
          // reabable 默认为暂停模式，需要主动调read方法将数据读取出来。read参数为每次读取的字节数
          while((data = myReadable.read(2)) !== null){
            cobnsole.log(data.toString())
          }
        })

        myReadable.on('data', (chunk) => {
          console.log(chunk.toString())
        })
      }
      ```
    - 可写流
      - 可写流事件
        - pipe 事件：可读流调用 pipe() 方法时触发
        - unpipe 事件：可读流调用 uppipe() 方法时触发
        - drain 事件：write 方法返回 false, 数据又可以继续写入时触发
      ```js
      const { Writable } = require('stream')

      // 自定义类继承 Writable
      class MyWritable extends Writable {
        constructor(){
          super()
        }
        _write(chunk, en, done) {
          process.stdout.wirte(chunk.toString())
          process.nextTick(done)
        }

        // 实例化
        let myWritable = new MyWritable(source)

        myWritable.write('test MyWritable', 'utf-8', () => {
          console.log('end')
        })
      ```
    - Duplex 双工流
      - 自定义双工流
        - 继承 Duplex 类
        - 重写 _read 方法，调用 push 生产数据
        - 重写 _write 方法，调用 write 消费数据
      ```js
      let { Readable, Writable, Duplex } = require('stream')

      // 模拟底层数据
      let source = ['a', 'b', 'c']

      // 自定义双工流
      class MyDuplex extends Duplex {
        constructor(options) {
          super(source, options)
          this.source = source
        }
        _read() {
          let data = this.source.shift() || null
          this.push(data)
        }
        _write() {
          if(Buffer.isBuffer(chunk)){
            chunk = chunk.toString()
          }
          process.stdout.write(chunk + "----->")
          process.nextTick(next)
        }
      }

      // 实例化
      let myDuplex = new MyDuplex(source)

      myDuplex.on('data', (chunk) => {
        console.log(chunk.toString())
      })

      myDuplex.write('测试数据'， ‘utf-8’, () => {
        console.log('双工流测试可写操作')
      })
      ```
    - Transform 也是一个双工流
      - 自定义转换流
        - 继承 Transform 类
        - 重写 _transform 方法，调用 push 和 callback
        - 重写 _flush 方法，处理剩余数据（非必要）
      ```js
      const {Transform} = require('stream')

      class MyTransform extends Transform{
        constructor(options){
          super()
        }
        _transform(chunk, encoding, callback) {
          this.push(chunk.toString().toUpperCase())
          callback(null)
        }

        let a = new MyTransform()

        a.write('a')
        a.write('b')
        a.end('c')

        a.pipe(process.stdout) // ABC
      }
      ```
    - 文件可读流
    ```js
    const fs = require('fs')
    
    // 文件可读流的创建与消费
    let rs = fs.createReadStream('test.txt', {
      flags: 'r',
      encoding: null,
      fd: null,
      mode: 438,
      autoClose: true,
      start: 0,
      end: 3,
      highWaterMark: 4
    })

    rs.on('data', (chunk) => {
      console.log(chunk.toString())
      rs.pause()
      setTimeout(() => {
        rs.resume()
      },1000)
    })

    rs.on('readable', () => {
      /* let data = rs.read()
      console.log(data) */

      let data
      while((data = rs.read(1) !== null)){
        console.log(data.toString())
        
        console.log('--------', rs._readbaleState.length)
      }
    })

    // 文件可读流事件与应用

    // 实例化 rs 时就执行了
    rs.on('open', (fd) => {
      console.log(fd, '文件打开了')
    })

    // 数据被消费完之后
    rs.on('close', () => {
      console.log('文件关闭了')
    })

    // 在close事件之前执行
    rs.on('end', () => {
      console.log('当数据被清空之后')
    })

    rs.on('error', (err) => {
      console.log(err)
    })
    ```
    - 文件可写流
    ```js
    const fs = require('fs')

    const ws = fs.createWriteStream('test.txt', {
      flag: 'w',
      mode: 438,
      fs: null,
      encoding: 'utf-8',
      start: 0,
      highWaterMark: 3
    })

    // fs.ws的数据是 字符串 或者 buffer
    ws.write('test ws 1', () => {
      console.log('write done 1')
    })

    ws.write('test ws 2', () => {
      console.log('write done 2')
    })

    ws.open('open', (fd) => {
      console.log('open', fd)
    })

    // close 是在数据写入操作全部完成之后再执行
    ws.on('close', () => {
      console.log('file closed')
    })

    // end 执行之后就意味着数据写入操作完成
    ws.end()

    // error
    ws.on('error', (err) => {
      console.log(err)
    })
    ```

    - write 执行流程
    <image src='./images/write执行流程.png' />
    ```js
    const fs = require('fs')

    const ws = fs.createWriteSteam('test.txt', {
      highWaterMark: 3
    })

    let flag = ws.write('1')

    console.log(flag) // true

    let flag = ws.write('2')

    console.log(flag) // true

    let flag = ws.write('3')

    console.log(flag) // false

    // 一般用不上，因为有更好的pipe方法
    ws.on('drain', () => {
      console.log('drain emit')
    })
    ```
    - 控制写入速度
    ```js
    /**
     * 需求：‘你好世界’ 写入指定的文件
     * 01 一次性写入
     * 02 分批写入
     * 对比：
     * */
    
    let fs = require('fs')

    let ws = fs.createWriteStream('test.txt', {
      highWaterMark: 3
    })

    // 一次性写入
    ws.write('你好世界')
    
    // 控制写入速度
    let source = '你好世界'.split('')
    let num = 0;

    function executeWrite() {
      flag = true
      while(num !== 4 && flag) {
        flag = ws.write(source[num])
        num ++
      }
    }

    executeWrite()

    ws.on('drain', () => {
      console.log('drain 执行了')
      executeWrite()
    })
    ```

    - 背压机制（back pressure）
    - 模拟文件可读流
    ```js
    const fs = require('fs')
    const EventEmitter = require('events')

    class MyFileReadStream extends EventEmitter {
      constructor(path, options = {}){
        super()
        this.path = path;
        this.flags = options.flags || 'r'
        this.mode = options.mode || 438
        this.autoClose = options.autoClose || true
        this.start = options.start || 0
        this.end = options.end
        this.highWaterMark = options.highWaterMark || 64 * 1024
        this.readOffset = 0

        this.open()
        this.on('newListener', (type) => {
          console.log(type)
          if(type === 'data'){
            
          }
        })
      }

      open() {
        // 原生open方法打开指定位置的文件
        fs.open(this.path, this.flags, this.mode, (err, fd) => {
          if(err){
            this.emit('error', err)
          }
          this.fd = fd
          this.emit('open', fd)
        })
      }

      read() {
        console.log(this.fd)
        if(typeof this.fd !== 'number') {
          return this.once('open', this.read)
        }

        let buf = Buffer.allco(this.highWaterMark)

        let howmuchtoread
        if(this.end) {
         howmuchtoread = Math.min(this.highWaterMark, this.end - this.readOffset + 1)
        } else {
          howmuchtoread = this.highWaterMark
        }
        fs.read(this.fd, buf, 0, howmuchtoread, this.readOffset, (err, readBytes) => {
          if(readBytes) {
            this.readOffset += readBytes
            this.emit('data', buf.slice(0, readBytes))
            this.read()
          } else {
            this.emit('end')
            this.close()
          }
        })
      }
      close() {
        fs.close(this.fd, () => {
          this.emit('close')
        })
      }
    }

    let rs = new MyFileReadStream('test.txt')

    rs.on('open', (fd) => {
      console.log('open', fd)
    })

    rs.on('error', (err) => {
      console.log(err)
    })

    rs.on('data', (chunk) => {
      console.log(chunk.toString())
    })

    rs.on('end', () => {
      console.log('end')
    })

    rs.on('close', () => {
      console.log('close')
    })
    ```

    - 链表结构
      - 数组的缺点
        - 数组存储数据的长度具有上线
        - 数组存在塌陷问题
      - 链表是什么？
        - 链表是一系列节点的集合
        - 每个节点都具有指向下一个节点的属性（next）
      - 链表分类
        - 双向链表
        - 单项链表(主要讨论)
        <image src="./images/单向链表结构.png">
        - 循环链表

    - 单项链表实现
    ```js
    /**
     * 01 node + head + null
     * 02 head ----> null
     * 03 size
     * 04 next element
     * 05 增加 删除 修改 查询 清空
     *  */
    class Node {
      constructor(element, next) {
        this.element = element;
        this.next = next
      }
    }

    class LinkedList{
      constructor(head, size){
        this.head = null
        this.size = 0
      }

      _getNode(index) {
        if(index < 0 || index > this.size) {
          throw new Error('cross the border')
        }
        let currentNode = this.head
        for(let i = 0; i < index; i++){
          currentNode = currentNode.next
        }
        return currentNode
      }

      add(index, element) {
        if(arguments.length === 1) {
          element = index
          index = this.size
        }
        if(index < 0 || index > this.size) {
          throw new Error('cross the border')
        }
        if(index === 0) {
          // 保存 head
          let head = this.head
          this.head = new Node(element, head)
        } else {
          let prevNode = this._getNode(index - 1)
          prevNode.next = new Node(elelment, prevNode.next)
        }
        this.size++
      }

      remove(index) {
        let head = this.head
        if(index === 0) {
          this.head = head.next
        } else {
          let prevNode = this._getNode(index -1)
          prevNode.next = prevNode.next.next
        }
        this.size--
      }

      set(index, element) {
        let node = this._getNode(index);
        node.element = element
      }

      get(index) {
        return this._getNode(index)
      }

      clear() {
        this.head = null
        this.size = 0
      }

    }
    ```

    - 单向链表实现队列
    ```js
    class Node {
      constructor(element, next) {
        this.element = element;
        this.next = next
      }
    }

    class LinkedList{
      constructor(head, size){
        this.head = null
        this.size = 0
      }

      _getNode(index) {
        if(index < 0 || index > this.size) {
          throw new Error('cross the border')
        }
        let currentNode = this.head
        for(let i = 0; i < index; i++){
          currentNode = currentNode.next
        }
        return currentNode
      }

      add(index, element) {
        if(arguments.length === 1) {
          element = index
          index = this.size
        }
        if(index < 0 || index > this.size) {
          throw new Error('cross the border')
        }
        if(index === 0) {
          // 保存 head
          let head = this.head
          this.head = new Node(element, head)
        } else {
          let prevNode = this._getNode(index - 1)
          prevNode.next = new Node(elelment, prevNode.next)
        }
        this.size++
      }

      remove(index) {
        let rmNode = null
        if(index === 0) {
          rmNode = this.head
          if(!rmNode){
            return undefined
          }
          this.head = rmNode.next
        } else {
          let prevNode = this._getNode(index -1)
          rmNode = prevNode.next
          prevNode.next = prevNode.next.next
        }
        this.size--
        return rmNode
      }

      set(index, element) {
        let node = this._getNode(index);
        node.element = element
      }

      get(index) {
        return this._getNode(index)
      }

      clear() {
        this.head = null
        this.size = 0
      }
    }

    class Queue{
      constructor(){
        this.linkedList = new LinkedList()
      }
      enQueue(data){
        this.linkedList.add(data)
      }
      deQueue(){
        this.linkedList.remove(0) 
      }
    }
    ```

    - 文件可写流实现
    ```js
    const fs = require('fs')
    const EventEmitter = require('events')
    const Queue = require('./linkedlist')

    class MyWriteStream extends EventEmitter {
      constructor(path, options = {}) {
        super()
        this.path = path
        this.flags = options.flags || 'w'
        this.mode = options.mode || 438
        this.autoClose = options.autoClose || true
        this.start = options.start || 0
        this.encoding = options.encoding || 'utf8'
        this.highWaterMark = options.highWaterMark || 16 * 1024

        this.open()

        this.writeOffset = this.start
        this.writing = false
        this.writeLen = 0
        this.needDrain = false
        this.cache = new Queue()
      }
      open(){
        // 原生 fs.open
        fs.open(this.path, this.flags, (err, fd) => {
          if(err) {
            this.emit('error', err)
          } else {
            this.fd = fd
            this.emit('open', fd)
          }
        })
      }

      write(chunk, encoding, cb) {
        chunk = Buffer.is(chunk) ? chunk : Buffer.form(chunk)

        this.writeLen += chunk.length

        let flag = this.writeLen < this.highWaterMark

        this.needDrain = !flag

        if(this.writing) {
          // 当前正在写入，所以内容应该排队
          this.cache.enQueue({chunk, encoding, cb})
        } else {
          this.writing = true
          // 当前不是正在写入就执行写入操作
          this._write(chunk, encoding, ()=>{
            cb()
            // 清空排队的内容
            this._clearBuffer()
          })
        }

        return flag
      }

      _clearBuffer() {
        let data = this.cache.deQueue()
        if(data) {
          this._write(data.element.chunk, data.element.encoding, () =>{
            data.element.cb && dat.element.cb()
            this._clearBuffer()
          })
        } else {
          if(this.needDrain){
            this.needDrain = false
            this.emit('drain')
          }
        }
        
      }

      _write(chunk, encoding, cb) {
        if(typeof this.fd !== 'number') {
          this.once('open', () => {
            return this._write(chunk, encoding, cb)
          })
        }

        fs.write(this.fd, chunk, this.start, chunk.length, this.writeOffset, (err, written) => {
          this.writeOffset =+ written
          this.writeLen -= written
          cb && cb()
        })
      }
    }
    ```

    - pipe 方法
      - pipe 方法实现
      ```js
      const fs = require('fs')
      const EventEmitter = require('events')

      class MyFileReadStream extends EventEmitter {
        constructor(path, options = {}){
          super()
          this.path = path;
          this.flags = options.flags || 'r'
          this.mode = options.mode || 438
          this.autoClose = options.autoClose || true
          this.start = options.start || 0
          this.end = options.end
          this.highWaterMark = options.highWaterMark || 64 * 1024
          this.readOffset = 0

          this.open()
          this.on('newListener', (type) => {
            console.log(type)
            if(type === 'data'){
              
            }
          })
        }

        open() {
          // 原生open方法打开指定位置的文件
          fs.open(this.path, this.flags, this.mode, (err, fd) => {
            if(err){
              this.emit('error', err)
            }
            this.fd = fd
            this.emit('open', fd)
          })
        }

        read() {
          console.log(this.fd)
          if(typeof this.fd !== 'number') {
            return this.once('open', this.read)
          }

          let buf = Buffer.allco(this.highWaterMark)

          let howmuchtoread
          if(this.end) {
          howmuchtoread = Math.min(this.highWaterMark, this.end - this.readOffset + 1)
          } else {
            howmuchtoread = this.highWaterMark
          }
          fs.read(this.fd, buf, 0, howmuchtoread, this.readOffset, (err, readBytes) => {
            if(readBytes) {
              this.readOffset += readBytes
              this.emit('data', buf.slice(0, readBytes))
              this.read()
            } else {
              this.emit('end')
              this.close()
            }
          })
        }
        close() {
          fs.close(this.fd, () => {
            this.emit('close')
          })
        }
        pipe(ws){
          this.on('data', (data) => {
            let flag = ws.write(data)
            if(!flag) {
              this.pause()
            }
            ws.on('drain', () => {
              this.resume()
            })
          })
        }
      }
      ```


##### 通信

  - 通信的基本原理
    - 通信的必要条件
      - 主机之间需要传输介质
      - 主机上必须有网卡设备
      - 主机之间需要协商网络速率
  
  - 网络通讯的方式
    - 交换机通信（组成局域网）
      - 通过 Mac 地址来唯一标识一台主机
      - 问题
        - 交换机的接口数量有限
        - 局域网存在大量主机会造成广播风暴
    - 路由器通信
      - 不同局域网之间的通信

  - 网络层次模型
    - OSI七层模型
      - 应用层：用户与网络的接口
      - 表示层：数据加密、转换、压缩
      - 会话层：控制网络连接建立与终止
      - 传输层：控制数据传输的可靠性
      - 网络层：确定目标网络
      - 数据链路层：确定目标主机
      - 物理层：各种物理设备和标准
    - 数据从A到B，先封装（自上而下）再解封（自下而上）

  - 数据封装与解封装
    - 封装
      - 应用层 -> data
      - 传输层 -> 目标端口、源端口、data(TCP与UDP协议)
      - 网络层 -> 目标IP、源IP、目标端口、源端口、data
      - 数据链路层 -> 目标Mac、源Mac、目标IP、源IP、目标端口、源端口、data
      - 物理层 -> 经过网卡调试，表层二进制数据（高低电压）
    - 解封装
      - 与封装过程相反

  - 创建 TCP 通信
    - 通信事件
      - listening: 调用 server.listen 方法之后触发
      - connection：新的连接建立时触发
      - close：当server关闭时触发
      - error: 当错误出现的时候触发
      - data事件：当接收到数据的时候触发该事件
      - write方法：在socket上发送数据，默认是 UTF8 编码
      - end操作：当socket的一段发送 FIN 包时触发，结束可读端
    ```js
    // server.js

    const net = require('net')

    // 创建服务端实例
    const server = net.createServer()

    const PORT = 1234
    const HOST = 'localhost'

    server.listen(PORT, HOST)

    server.on('listening', () => {
      console.log(`服务端已经开启在 ${HOST}${PORT}`)
    })

    // 接收消息 会写消息
    server.on('connection', (socket) => {
      socket.on('data', (chunk) => {
        const msg = chunk.toString()
        console.log(msg)
      })

      // 回写数据
      socket.write(Buffer.from('你好' + msg))
    })

    server.on('close', () => {
      console.log('服务端关闭了')
    })

    server.on('error', (err) => {
      if(err.code === "EADDRINUSE") {
        console.log('地址正在被使用')
      }
    })


    // client.js
    const net = require('net')

    const client = new.createConnection({
      port: 1234,
      host: '127.0.0.1'
    })

    client.on('connect', () => {
      client.write('拉钩教育')
    })

    client.on('data', (chunk) => {
      console.log(chunk.toString())
    })

    client.on('error', (err) => {
      console.log(err)
    })

    client.on('close', () => {
      console.log('客户端断开连接')
    })
    ```
  - TCP粘包及解决
    - 产生原因：
      - 通信包含数据发送端和接收端，发送端会累计数据统一发送，接收端在处理数据的时候，也不是立马使用数据，也是将数据放在缓存中，然后再对数据进行获取和使用。好处是可以减少IO操作带来的性能消耗，但是对数据的使用就会产生粘包问题。对于什么时候发送数据，取决于TCP的拥塞机制。
    - 解决方式
      - 延长数据发送的时间间隔，会降低数据的发送效率
      - 拆包封包

  - 数据的封包与拆包
    ```js
    class myTransformCode{
      constructor(){
        this.packageHeaderLen = 4
        this.serialNum = 0
        this.serialLen = 2
      }

      // 编码
      encode(data, serialNum) {
        const body = Buffer.from(data)

        // 01 先按照指定的长度来申请一片内存空间作为 header 来使用
        const headerBuf = Buffer.alloc(this.packageHeadLen)

        // 02
        headerBuf.writeInt16BE(serialNum || this.serialNum)

        headerBuf.writeInt16BE(body.length, this.serialNum)

        if(serialNum === undefined){
          this.serialNum ++
        }

        return Buffer.concat([headerBuf, body])
      }

      // 解码
      decode(buffer) {
        const headerBuf = buffer.slice(0, this.packageHeaderLen)
        const bodyBuf = buffer.slice(this.packageHeaderLen)

        return {
          serialNum: headerBuf.readInt16BE()
          bodyLength: headerBuf.readInt16BE(this.seriallen)
          body: bodyBuf.toString()
        }
      }

      // 获取包长度的方法
      getPackageLen(buffer){
        if(buffer.length < this.packageHeaderLen) {
          return 0
        } else {
          return this.packageHeaderLen + buffer.readInt16BE(this.serialLen)
        }
      }
    }
    
    module.exports = myTransformCode
    ```

  - 封包解决粘包
    ```js
    // server.js
    const net = require('net')
    const myTransform = require('./myTransform.js')

    const server = net.createServer()

    server.listen('1234', 'localhost')

    let overageBuffer = null

    let ts = new MyTransform()

    server.on('listening', () => {
      console.log('服务端运行再 localhost:1234')
    })

    server.on('connection', (socket) => {
      socket.on('data', (chunk) => {
        if(overageBuffer){
          chunk = Buffer.concat([overageBuffer, chunk])
        }
        let packageLen = 0
        while(packagelen = ts.getPackagelen(chunk)){
          const packageCon = chunk.slice(0, packageLen)
          chunk = chunk.slice(packageLen)

          const ret = ts.decode(pageageCon)
          console.log(ret)

          socket.write(ts.encode(ret.body, ret.serialNum))
        }
        overageBuffer = chunk
        socket.write(Buffer.from('您好' + msg))
      })
    })

    // client.js
        const net = require('net')
    const myTransform = require('./myTransform.js')

    const client = net.createConnection({
      host:'localhost',
      port: 1234
    })


    let overageBuffer = null

    let ts = new MyTransform()

    client.write(ts.encode('你好世界1'))
    client.write(ts.encode('你好世界2'))
    client.write(ts.encode('你好世界3'))
    client.write(ts.encode('你好世界4'))
    client.write(ts.encode('你好世界5'))
    client.write(ts.encode('你好世界6'))

    client.on('listening', () => {
      console.log('服务端运行在 localhost:1234')
    })

    client.on('data', (chunk) => {
      if(overageBuffer){
          chunk = Buffer.concat([overageBuffer, chunk])
        }
        let packageLen = 0
        while(packagelen = ts.getPackagelen(chunk)){
          const packageCon = chunk.slice(0, packageLen)
          chunk = chunk.slice(packageLen)

          const ret = ts.decode(pageageCon)
          console.log(ret)
        }
        overageBuffer = chunk
    })
    ```
  - http 协议
    ```js
    const http = require('http')

    // 创建 服务端
    let server = http.createServer((req, res) => {
      // 针对于请求和相应完成各自的操作
      console.log(1111)
    })

    server.listen(1234, () => {
      console.log('http server is listening')
    })
    ```

  - 获取 http 请求信息
    ```js
    const http = require('http')
    const url = require('url')

    // 创建 服务端
    let server = http.createServer((req, res) => {
      // 请求路径
      let { pathname, query } = url.parse(req.url, true)

      // 请求方式
      console.log(req.method)

      // 版本号
      console.log(req.httpVersion)

      // 请求头
      console.log(req.headers)

      // 请求体获取数据
      let arr = []
      req.on('data', (data) => {
        arr.push(data)
      })
      req.on('end', () => {
        console.log(Buffer.concat(arr).toString())
      })

    })

    server.listen(1234, () => {
      console.log('http server is listening')
    })
    ```
  - 设置 http 响应
    ```js
    const http = require('http')

    const server = http.createServer((req, res) => {
      console.log('有请求进来')

      // res
      /* res.write('ok')
      res.end() */

      /* res.end('test ok') */
      res.statusCode = 304
      res.serHeader('Content-type', 'text/html;charset=utf-8')
      res.end('你好世界')
    })

    server.listen(1234, () => {
      console.log('server is running')
    })
    ```
  - 代理客户端
    ```js
    // agent-server.js
    const http = require('http')
    const url = require('url')
    const queryStirng = require('queryString')

    const server = http.createServer((req, res) => {
      console.log('请求进来了')
      let {pathname, query} = url.parse(req.url)
      connsole.log(pathname, query)

      // post
      let arr = []
      req.on('data', (data) => {
        arr.push(data)
      })

      req.on('end', () => {
        let obj = Buffer.concat(arr).toString()
        
        if(req.headers['Content-type'] === 'application/json'){
          let a = JSON.parse(obj)
          a.add = '互联网人的大学'
          res.end(JSON.stringify(a))
        } else if(req.headers['Content-type'] === 'application/x-www-urlencoded') {
          let ret = queryStirng.parse(obj)
          res.end(JSON.stringify(ret))
        }
      })
    })

    server.listen(1234, () => {
      console.log('server is running')
    })
    ```

  - 代理客户端解决跨域
    ```js
    const http = require('http')

    let options = {
      host: 'localhost',
      port: 1234,
      path: '/'
      method: 'POST'
    }

    let server = http.createServer((request, response)=>{
      let req = http.request(options, (res) => {
        let arr = []
        res.on('data', (data) => {
          arr.push(data)
        })

        res.on('end', () => {
          // console.log(Buffer.concat(arr).toString())

          let ret = Buffer.concat(arr).toString()
          response.setHeader('Content-type', 'text/html;charset=utf-8')
          response.end(ret)
        })
      })

      req.end('你好世界')
    })

    server.listen(2345, () => {
      console.log('本地服务启动了')
    })
    ```

  - http 静态服务
    ```js
    const http = require('http')
    const url = require('url')
    const fs = require('fs')
    const mime = require('mime') // 第三方包

    const server = http.createServer((req, res) => {
      // 路径处理
      let {pathname, query} = url.parse(req.url)
      pathname = decodeURIComponent(pathname)
      let absPath = path.join(__dirname, pathname)
      // 目标资源状态处理
      fs.stat(absPath, (err, statObj) => {
        if(err) {
          res.statusCode = 404
          res.end('Not found')
          return
        }
        if(statObj.isFile()){
          fs.readfile(absPath, (err, data) => {
            res.setHeader('Content-type', mime.getType(absPath) + ';charset=utf-8')
            res.end(data)
          })
        } else {
          fs.readfile(path.join(absPath, 'index,html'), (err, data) => {
            res.setHeader('Content-type', mime.getType(absPath) + ';charset=utf-8')
            res.end(data)
          })
        }
      })
      res.end()
    })
    ```
  
  - 静态服务工具 lgserve