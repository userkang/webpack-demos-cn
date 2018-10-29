## 写在开头：

此 Webpack 教程是阮老师在 Webpack 1.x 的版本上做的。现在 Webpack 的版本已经改动较大，建议有基础的同学，就直接上官网看最新的就好了。这个教程可以用来了解下 Webpack 的前世今生。

## 开始

这个项目是一些简单的 Webpack 示例集合

这些示例特意用简单明了的方式编写，你将会发现跟着这些例子学习这个强大的工具并非难事。

## 如何使用

首先，全局安装 [Webpack](https://www.npmjs.com/package/webpack) 和
[webpack-dev-server](https://www.npmjs.com/package/webpack-dev-server)

```bash
$ npm i -g webpack webpack-dev-server
```

然后，克隆这个仓库

```bash
$ git clone https://github.com/userkang/webpack-demos-cn.git
```

安装依赖

```bash
$ cd webpack-demos
$ npm install
```

现在，去项目 demo\* 目录下开始源码体验之旅吧

```bash
$ cd demo01
$ npm run dev
```

如果上面的命令没有自动打开浏览器，你可能需要自己在浏览器访问
http://127.0.0.1:8080

## 前言：什么是 Webpack

Webpack 是为浏览器构建 JavaScirpt 模块脚本的前端工具

它可以像 Browserify 一样使用，并且更加强大

```bash
$ browserify main.js > bundle.js
# 相当于
$ webpack main.js bundle.js
```

Webpack 需要一个 `webpack.config.js` 的配置文件，它只是一个 CommonJs 模块

```javascript
// webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
}
```

有了这个文件之后，你可以不带参数的调用 Webpack

```bash
$ webpack
```

你需要了解一些命令行选项

- `webpack` – 开发环境构建
- `webpack -p` – 生产环境构建(压缩混淆脚本)
- `webpack --watch` – 监听变动并自动打包
- `webpack -d` – 生成 map 映射文件
- `webpack --colors` – 构建过程带颜色输出

你可能需要像下面这样在 package.json 中自定义一些脚本 `scripts`

```javascript
// package.json
{
  // ...
  "scripts": {
    "dev": "webpack-dev-server --devtool eval --progress --colors",
    "deploy": "NODE_ENV=production webpack -p"
  },
  // ...
}
```

## 目录

1. [Entry file](#demo01-entry-file-source)
1. [Multiple entry files](#demo02-multiple-entry-files-source)
1. [Babel-loader](#demo03-babel-loader-source)
1. [CSS-loader](#demo04-css-loader-source)
1. [Image loader](#demo05-image-loader-source)
1. [CSS Module](#demo06-css-module-source)
1. [UglifyJs Plugin](#demo07-uglifyjs-plugin-source)
1. [HTML Webpack Plugin and Open Browser Webpack Plugin](#demo08-html-webpack-plugin-and-open-browser-webpack-plugin-source)
1. [Environment flags](#demo09-environment-flags-source)
1. [Code splitting](#demo10-code-splitting-source)
1. [Code splitting with bundle-loader](#demo11-code-splitting-with-bundle-loader-source)
1. [Common chunk](#demo12-common-chunk-source)
1. [Vendor chunk](#demo13-vendor-chunk-source)
1. [Exposing Global Variables](#demo14-exposing-global-variables-source)
1. [React router](#demo15-react-router-source)

## Demo01: Entry file ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo01))

入口文件是 Webpack 进行读取构建 `bundle.js` 文件的一个文件

例如， `main.js` 就是一个入口文件.

```javascript
// main.js
document.write('<h1>Hello World</h1>')
```

index.html

```html
<html>
  <body>
    <script type="text/javascript" src="bundle.js"></script>
  </body>
</html>
```

Webpack 遵循 `webpack.config.js` 来构建 `bundle.js`.

```javascript
// webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
}
```

启动服务，访问 http://127.0.0.1:8080 .

```bash
$ cd demo01
$ npm run dev
```

## Demo02: Multiple entry files ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo02))

多个入口文件也是可以的。在多页面 应用中，每个页面拥有不同的入口文件，用这个就非常管用了。

```javascript
// main1.js
document.write('<h1>Hello World</h1>')

// main2.js
document.write('<h2>Hello Webpack</h2>')
```

index.html

```html
<html>
  <body>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: {
    bundle1: './main1.js',
    bundle2: './main2.js'
  },
  output: {
    filename: '[name].js'
  }
}
```

## Demo03: Babel-loader ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo03))

Loaders 是一种预处理器，它可以在 Webpack 编译之前把你应用中的静态资源进行转换 ([更多信息](https://webpack.docschina.org/loaders/))。

举个例子， [Babel-loader](https://www.webpackjs.com/loaders/babel-loader/) 可以在 Webpack 编译这些 JS 文件之前，先将 JSX/ES6 语法的文件转换成普通 ES5 语法的文件。Webpack 官网可以查看目前支持的 [loaders](https://www.webpackjs.com/loaders/)。

`main.jsx` 是一个 JSX 文件.

```javascript
// main.jsx
const React = require('react')
const ReactDOM = require('react-dom')

ReactDOM.render(<h1>Hello, world!</h1>, document.querySelector('#wrapper'))
```

index.html

```html
<html>
  <body>
    <div id="wrapper"></div>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      }
    ]
  }
}
```

上边的代码段用到了 `babel-loader`， 它需要 Babel 的预设插件 [babel-preset-es2015](https://www.npmjs.com/package/babel-preset-es2015) and [babel-preset-react](https://www.npmjs.com/package/babel-preset-react) 来转义 ES6 和 React。

## Demo04: CSS-loader ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo04))

Webpack 允许在 JS 文件里包含 CSS，并通过 [CSS-loader](https://www.webpackjs.com/loaders/css-loader/)来预处理 CSS 文件。

main.js

```javascript
require('./app.css')
```

app.css

```css
body {
  background-color: blue;
}
```

index.html

```html
<html>
  <head>
    <script type="text/javascript" src="bundle.js"></script>
  </head>
  <body>
    <h1>Hello World</h1>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  }
}
```

注意，你必须用到两个 loaders 去转换 CSS 文件。一个是 [CSS-loader](https://www.webpackjs.com/loaders/css-loader/) 来读取 CSS 文件，另一个是 [Style-loader](https://www.webpackjs.com/loaders/style-loader/) 用来把 `<style>` 标签插入 HTML 页面。

接下来，让我们启动服务。

```bash
$ cd demo04
$ npm run dev
```

事实上，Webpack 会将一个内联样式表插入到 `index.html`。

```html
<head>
  <script type="text/javascript" src="bundle.js"></script>
  <style type="text/css">
    body {
      background-color: blue;
    }
  </style>
</head>
```

## Demo05: Image loader ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo05))

Webpack 可以在 JS 文件中包含图片。

main.js

```javascript
var img1 = document.createElement('img')
img1.src = require('./small.png')
document.body.appendChild(img1)

var img2 = document.createElement('img')
img2.src = require('./big.png')
document.body.appendChild(img2)
```

index.html

```html
<html>
  <body>
    <script type="text/javascript" src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.(png|jpg)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 8192
            }
          }
        ]
      }
    ]
  }
}
```

[url-loader](https://www.npmjs.com/package/url-loader)
将图片文件转成 `img` 标签。如果图片的大小小于 8192 字节，将会被转成 base64 的格式，否则还事会转成一条普通的链接。

启动服务就可以看到，`small.png` 和 `big.png` 这两张图片转成了不同的两种格式。

```html
<img src="data:image/png;base64,iVBOR...uQmCC">
<img src="4853ca667a2b8b8844eb2693ac1b2578.png">
```

## Demo06: CSS Module ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo06))

`css-loader?modules` 的 [CSS Module](https://github.com/css-modules/css-modules) 功能可以给 JS 模块的 CSS 设置一个局部作用域。你可以用 `:global(selector)` ([更多](https://css-modules.github.io/webpack-demo/)) 关掉它，使样式变成全局的。

index.html

```html
<html>
<body>
  <h1 class="h1">Hello World</h1>
  <h2 class="h2">Hello Webpack</h2>
  <div id="example"></div>
  <script src="./bundle.js"></script>
</body>
</html>
```

app.css

```css
/* local scope */
.h1 {
  color: red;
}

/* global scope */
:global(.h2) {
  color: blue;
}
```

main.jsx

```javascript
var React = require('react')
var ReactDOM = require('react-dom')
var style = require('./app.css')

ReactDOM.render(
  <div>
    <h1 className={style.h1}>Hello World</h1>
    <h2 className="h2">Hello Webpack</h2>
  </div>,
  document.getElementById('example')
)
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      },
      {
        test: /\.css$/,
        use: [
          {
            loader: 'style-loader'
          },
          {
            loader: 'css-loader',
            // 开启 CSS Module 功能
            options: {
              modules: true
            }
          }
        ]
      }
    ]
  }
}
```

启动服务。

```bash
$ cd demo06
$ npm run dev
```

访问 http://127.0.0.1:8080, 你将看到 `h1` 是红色的，因为它的样式是局部作用域，`h2` 是蓝色的，因为它的作用域是全局的。

## Demo07: UglifyJs Plugin ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo07))

Webpack 用一套插件系统扩展了它的功能。比如，[UglifyJs Plugin](https://webpack.js.org/plugins/uglifyjs-webpack-plugin/) 就是其中的一个流行的插件，它可以压缩混淆输出的 js 代码。

main.js

```javascript
var longVariableName = 'Hello'
longVariableName += ' World'
document.write('<h1>' + longVariableName + '</h1>')
```

index.html

```html
<html>
<body>
  <script src="bundle.js"></script>
</body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack')
var UglifyJsPlugin = require('uglifyjs-webpack-plugin')

module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  // 使用插件
  plugins: [new UglifyJsPlugin()]
}
```

启动服务，将会看到 `main.js` 被压缩成了下面的样式

```javascript
var o = 'Hello'
;(o += ' World'), document.write('<h1>' + o + '</h1>')
```

## Demo08: HTML Webpack Plugin and Open Browser Webpack Plugin ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo08))

This demo shows you how to load 3rd-party plugins.

这个例子将讲讲怎么载入第三方的插件。

[html-webpack-plugin](https://github.com/ampedandwired/html-webpack-plugin) 将会
为你创建一个 `index.html` 文件。 [open-browser-webpack-plugin](https://github.com/baldore/open-browser-webpack-plugin) 可以在 Webpack 编译完成后打开一个新窗口。

main.js

```javascript
document.write('<h1>Hello World</h1>')
```

webpack.config.js

```javascript
var HtmlwebpackPlugin = require('html-webpack-plugin')
var OpenBrowserPlugin = require('open-browser-webpack-plugin')

module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new HtmlwebpackPlugin({
      title: 'Webpack-demos',
      filename: 'index.html'
    }),
    new OpenBrowserPlugin({
      url: 'http://localhost:8080'
    })
  ]
}
```

启动服务。

```bash
$ cd demo08
$ npm run dev
```

现在你不仅不需要手动的写 `index.html` 文件，而且也不用手动的打开浏览器了。Webpack 都帮你完成了。

## Demo09: Environment flags ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo09))

你可以用环境变量来区分开发环境。

main.js

```javascript
document.write('<h1>Hello World</h1>')

if (__DEV__) {
  document.write(new Date())
}
```

index.html

```html
<html>
<body>
  <script src="bundle.js"></script>
</body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack')

// DefinePlugin 允许创建一个在编译时可以配置的全局常量
var devFlagPlugin = new webpack.DefinePlugin({
  __DEV__: JSON.stringify(JSON.parse(process.env.DEBUG || 'false'))
})

module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [devFlagPlugin]
}
```

现在打开 `demo09/package.json`，你需要在 `scripts` 字段下添加如下的代码，向 webpack 传递环境变量。

```javascript
// package.json
{
  // ...
  "scripts": {
    "dev": "cross-env DEBUG=true webpack-dev-server --open",
  },
  // ...
}
```

启动服务。

```javascript
$ cd demo09
$ npm run dev
```

## Demo10: Code splitting ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo10))

对于一个大型 app，把所有代码塞到一个文件可能不是很好维护。Webpack 可以把一个庞大的 JS 文件拆分成几块。尤其，有些代码只有用的时候才引入可以按需加载。

Webpack 用 `require.ensure` 来定义一个代码点。

```javascript
// main.js
require.ensure(['./a'], function(require) {
  var content = require('./a')
  document.open()
  document.write('<h1>' + content + '</h1>')
  document.close()
})
```

`require.ensure` 告诉 Webpack `./a.js` 应该从 `bundle.js` 中拆分出来，单独生成一个文件。

```javascript
// a.js
module.exports = 'Hello World'
```

现在 Webpack 来负责依赖、输出和运行时需要的一些东西。你不必在投入过多的精力在 `index.html` 和 `webpack.config.js` 上了。

```html
<html>
  <body>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
}
```

启动服务。

```bash
$ cd demo10
$ npm run dev
```

表面上，你不会感到什么变化。然而，Webpack 已经把 `main.js` 和 `a.js` 构建成两个不同的文件（`bundle.js` and `0.bundle.js`），并且按依赖关系依次引入。

## Demo11: Code splitting with bundle-loader ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo11))

另一种进行代码分离的方法是用 [bundle-loader](https://www.npmjs.com/package/bundle-loader)。

```javascript
// main.js

// 现在 a.js 被引入，它将会被打包到另一个文件
var load = require('bundle-loader!./a.js')

// 为了等待 a.js 中的模块变得可用
// 你可能需要使用 async wait
load(function(file) {
  document.open()
  document.write('<h1>' + file + '</h1>')
  document.close()
})
```

`require('bundle-loader!./a.js')` 告诉 Webpack 从另一个包里加载 `a.js`。

现在 Webpack 将会构建 `main.js` 到 `bundle.js` 中, `a.js` 到 `0.bundle.js` 中。

## Demo12: Common chunk ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo12))

当多个 Js 文件有共同的依赖，我们可以 [CommonsChunkPlugin](https://webpack.js.org/plugins/commons-chunk-plugin/) 把公共的部分提取出来生成一个文件。这对浏览器缓存和节省带宽是非常有用的。

```javascript
// main1.jsx
var React = require('react')
var ReactDOM = require('react-dom')

ReactDOM.render(<h1>Hello World</h1>, document.getElementById('a'))

// main2.jsx
var React = require('react')
var ReactDOM = require('react-dom')

ReactDOM.render(<h2>Hello Webpack</h2>, document.getElementById('b'))
```

index.html

```html
<html>
  <body>
    <div id="a"></div>
    <div id="b"></div>
    <script src="commons.js"></script>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
  </body>
</html>
```

上面的 `commons.js` 就是 `main1.jsx` 和 `main2.jsx` 的公共部分。也就是包含了 `react` 和 `react-dom`。

webpack.config.js

```javascript
var webpack = require('webpack')

module.exports = {
  entry: {
    bundle1: './main1.jsx',
    bundle2: './main2.jsx'
  },
  output: {
    filename: '[name].js'
  },
  module: {
    rules: [
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      }
    ]
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: 'commons',
      // (the commons chunk name)

      filename: 'commons.js'
      // (the filename of the commons chunk)
    })
  ]
}
```

## Demo13: Vendor chunk ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo13))

你也可以用 CommonsChunkPlugin 提取第三方的 Js 生成一个单独的文件。

main.js

```javascript
var $ = require('jquery')
$('h1').text('Hello World')
```

index.html

```html
<html>
  <body>
    <h1></h1>
    <script src="vendor.js"></script>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
var webpack = require('webpack')

module.exports = {
  entry: {
    app: './main.js',
    vendor: ['jquery']
  },
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor',
      filename: 'vendor.js'
    })
  ]
}
```

上面的代码，`entry.vendor: ['jquery']` 告诉 Webpack `jeuery` 应该被打包到一个公共 `vendor.js` 包中去。

如果你想让一个全局的变量在每一个模块可用，比如 `$` 和 `jQuery` 不用 `require("jquery")` 就可以直接用。那么你需要使用 `ProvidePlugin` ([Official doc](https://webpack.js.org/plugins/provide-plugin/)) 这个插件，它可以自动的载入模块，而不需要到处 import 或者 require。

```javascript
// main.js
$('h1').text('Hello World')

// webpack.config.js
var webpack = require('webpack')

module.exports = {
  entry: {
    app: './main.js'
  },
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new webpack.ProvidePlugin({
      $: 'jquery',
      jQuery: 'jquery'
    })
  ]
}
```

当然，这种情况，你还需要手动的全局的载入 `jquery.js`

## Demo14: Exposing global variables ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo14))

如果你想要用到一些全局变量，但是又不想在打包的时候，把它打包到 bundle 文件中去。这个时候你可以在 `webpack.config.js` 中配置 `externals` 字段。

比如，我们定义了一个 `data.js` 文件

```javascript
// data.js
var data = 'Hello World'
```

index.html

```html
<html>
  <body>
    <script src="data.js"></script>
    <script src="bundle.js"></script>
  </body>
</html>
```

注意，这里 Webpack 只会打包 `bundle.js`， 而不会打包 `data.js`。

我们可以 `data` 字段暴露盛一个全局变量

```javascript
// webpack.config.js
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      }
    ]
  },
  externals: {
    // require('data') is external and available
    //  on the global var data
    data: 'data'
  }
}
```

现在，你可以在脚本中像引入其他模块一样引入 `data`。但它实际是一个全局变量。

```javascript
// main.jsx
var data = require('data')
var React = require('react')
var ReactDOM = require('react-dom')

ReactDOM.render(<h1>{data}</h1>, document.body)
```

你同样可以把 `react` 和 `react-dom` 添加到 `externals` 中，这样可以大大缩短编译时间和编译后 `bundle.js` 文件的大小。

## Demo15: React router ([source](https://github.com/userkang/webpack-demos-cn/tree/master/demo15))

这个演示用 webpack 来构建 [React-router](https://github.com/rackt/react-router/blob/0.13.x/docs/guides/overview.md) 的官方例子.

让我们想象一个带有仪表盘、收件箱和日历的小应用。

```
+---------------------------------------------------------+
| +---------+ +-------+ +--------+                        |
| |Dashboard| | Inbox | |Calendar|      Logged in as Jane |
| +---------+ +-------+ +--------+                        |
+---------------------------------------------------------+
|                                                         |
|                        Dashboard                        |
|                                                         |
|                                                         |
|   +---------------------+    +----------------------+   |
|   |                     |    |                      |   |
|   | +              +    |    +--------->            |   |
|   | |              |    |    |                      |   |
|   | |   +          |    |    +------------->        |   |
|   | |   |    +     |    |    |                      |   |
|   | |   |    |     |    |    |                      |   |
|   +-+---+----+-----+----+    +----------------------+   |
|                                                         |
+---------------------------------------------------------+
```

webpack.config.js

```javascript
module.exports = {
  entry: './index.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      },
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      }
    ]
  }
}
```

index.js

```javascript
import React from 'react'
import { render } from 'react-dom'
import { BrowserRouter, Switch, Route, Link } from 'react-router-dom'

import './app.css'

class App extends React.Component {
  render() {
    return (
      <div>
        <header>
          <ul>
            <li>
              <Link to="/app">Dashboard</Link>
            </li>
            <li>
              <Link to="/inbox">Inbox</Link>
            </li>
            <li>
              <Link to="/calendar">Calendar</Link>
            </li>
          </ul>
          Logged in as Jane
        </header>
        <main>
          <Switch>
            <Route exact path="/" component={Dashboard} />
            <Route path="/app" component={Dashboard} />
            <Route path="/inbox" component={Inbox} />
            <Route path="/calendar" component={Calendar} />
            <Route path="*" component={Dashboard} />
          </Switch>
        </main>
      </div>
    )
  }
}

class Dashboard extends React.Component {
  render() {
    return (
      <div>
        <p>Dashboard</p>
      </div>
    )
  }
}

class Inbox extends React.Component {
  render() {
    return (
      <div>
        <p>Inbox</p>
      </div>
    )
  }
}

class Calendar extends React.Component {
  render() {
    return (
      <div>
        <p>Calendar</p>
      </div>
    )
  }
}

render(
  <BrowserRouter>
    <Route path="/" component={App} />
  </BrowserRouter>,
  document.querySelector('#app')
)
```

index.html

```html
<html>
  <body>
    <div id="app"></div>
    <script src="/bundle.js"></script>
  </body>
</htmL>
```

启动服务。

```bash
$ cd demo15
$ npm run dev
```

## 相关链接

- [Webpack docs](https://webpack.js.org/concepts/)
- [webpack-howto](https://github.com/petehunt/webpack-howto), by Pete Hunt
- [SurviveJS Webpack book](https://survivejs.com/webpack/introduction/), by Juho Vepsäläinen
- [Diving into Webpack](https://web-design-weekly.com/2014/09/24/diving-webpack/), by Web Design Weekly
- [Webpack and React is awesome](http://www.christianalfoni.com/articles/2014_12_13_Webpack-and-react-is-awesome), by Christian Alfoni
- [Browserify vs Webpack](https://medium.com/@housecor/browserify-vs-webpack-b3d7ca08a0a9), by Cory House

## 证书

MIT
