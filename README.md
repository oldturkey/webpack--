# webpack--
翻译一下阮一峰老师的webpack教程
原文 链接：https://github.com/ruanyf/webpack-demos
Demo03: Babel-loader (source)
main.jsx is a JSX file.
Loaders是将你的资源文件预处理的应用程序（app），例如，Babel-loader可以将jsx/es6 文件转换成js 文件，官方文档由完整的loaders资源表

const React = require('react');
const ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.querySelector('#wrapper')
);
index.html
<html>
  <body>
    <div id="wrapper"></div>
    <script src="bundle.js"></script>
  </body>
</html>
webpack.config.js
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        loader: 'babel-loader?presets[]=es2015&presets[]=react'
      },
    ]
  }
};
在配置文件中，module.loaders 字段(field)用于指定预加载器（loader）,上面的代码用了babel loader 同时也要使用es2015的插件和babel-react的插件，去编译es6和React，你也可以采用另外一种方式去设置babel 查询选项。
module: {
  loaders: [
    {
      test: /\.jsx?$/,
      exclude: /node_modules/,
      loader: 'babel-loader',
      query: {
        presets: ['es2015', 'react']
      }
    }
  ]
}
Demo04: CSS-loader (source)
Webpack allows you to require CSS in JS file, then preprocessed CSS file with CSS-loader.
main.js
require('./app.css');
app.css
body {
  background-color: blue;
}
index.html
<html>
  <head>
    <script type="text/javascript" src="bundle.js"></script>
  </head>
  <body>
    <h1>Hello World</h1>
  </body>
</html>
webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      { test: /\.css$/, loader: 'style-loader!css-loader' },
    ]
  }
};
注意，你必须使用两个加载器来转换css文件，首先是css loader来读取css文件，然后另一个是style-loader 用来将样式表插入HTML页面，不同的加载器中间使用 ！ 进行分隔。
启动服务器以后，html页面加载了css样式表：
<head>
  <script type="text/javascript" src="bundle.js"></script>
  <style type="text/css">
    body {
      background-color: blue;
    }
  </style>
</head>
Demo05: Image loader (source)
URL-loader用于转换image 文件，如果图像文件的大小小于8192字节，它将被转换为Data URL，否则，它将被转换为普通地址，如你所见，问号（？）用于将字节限制大小的参数传递到加载器中。
webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192' }
    ]
  }
};
main.js
var img1 = document.createElement("img");
img1.src = require("./small.png");
document.body.appendChild(img1);

var img2 = document.createElement("img");
img2.src = require("./big.png");
document.body.appendChild(img2);
Demo06: CSS Module (source)
Css-loader?modules （查询参数的模块） 能够使css模块化
这意味着你的css模块默认是本地作用域范围的css，你可以选择使用：global 选择器来改变他的默认规则，切换他的默认范围
index.html
<html>
<body>
  <h1 class="h1">Hello World</h1>
  <h2 class="h2">Hello Webpack</h2>
  <div id="example"></div>
  <script src="./bundle.js"></script>
</body>
</html>
app.css
.h1 {
  color:red;
}

:global(.h2) {
  color: blue;
}
main.jsx
var React = require('react');
var ReactDOM = require('react-dom');
var style = require('./app.css');

ReactDOM.render(
  <div>
    <h1 className={style.h1}>Hello World</h1>
    <h2 className="h2">Hello Webpack</h2>
  </div>,
  document.getElementById('example')
);
webpack.config.js
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        query: {
          presets: ['es2015', 'react']
        }
      },
      {
        test: /\.css$/,
        loader: 'style-loader!css-loader?modules'
      }
    ]
  }
};
Launch the server.
$ webpack-dev-server
 结果你可以发现所有的h2标签都变成了蓝色，h1只有在react组件中的变成了红色，这是因为css-modules 的作用域作用


Demo07: UglifyJs Plugin (source)
Webpack 有一个插件系统来拓展他的功能，例如，Uglifyjs 插件可以压缩输出的代码
main.js
var longVariableName = 'Hello';
longVariableName += ' World';
document.write('<h1>' + longVariableName + '</h1>');
index.html
<html>
<body>
  <script src="bundle.js"></script>
</body>
</html>
webpack.config.js
var webpack = require('webpack');
var uglifyJsPlugin = webpack.optimize.UglifyJsPlugin;
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new uglifyJsPlugin({
      compress: {
        warnings: false
      }
    })
  ]
};
执行后，main.js将被压缩成如下代码：（不仅仅是不换行，且对变量名做了压缩）
var o="Hello";o+=" World",document.write("<h1>"+o+"</h1>")

Demo08: HTML Webpack Plugin and Open Browser Webpack Plugin (source)

这个demo用于向你展示如何加载第三方插件。
Html-webpack-plugin 可以为你创建 index.html，而open-browser-webpack-plugin 当webpack指令运行加载的时候，会自动打开一个url网页地址；
main.js
document.write('<h1>Hello World</h1>');
webpack.config.js
var HtmlwebpackPlugin = require('html-webpack-plugin');
var OpenBrowserPlugin = require('open-browser-webpack-plugin');

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
};
Run webpack-dev-server.
$ webpack-dev-server
现在你就不用再创建index.html了，并且也不用自己打开网页，运行webpack就可以直接打开网页。
Demo09: Environment flags (source)
你可以设置在有开发环境标志的开发环境中使用某些代码
main.js
document.write('<h1>Hello World</h1>');

if (__DEV__) {
  document.write(new Date());
}
index.html
<html>
<body>
  <script src="bundle.js"></script>
</body>
</html>
webpack.config.js
var webpack = require('webpack');

var devFlagPlugin = new webpack.DefinePlugin({
  __DEV__: JSON.stringify(JSON.parse(process.env.DEBUG || 'false'))
});

module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  plugins: [devFlagPlugin]
};
现在可以为webpack设置环境变量
# Windows
$ set DEBUG=true
$ webpack-dev-server



Demo10: Code splitting (source)
	在大型的web应用程序中项目中，将所有代码放到一个单独的文件中不是效率最大化的方法，webpack允许你将他们分割成几块，特别是如果某些模块的代码只是在一些情况下被调用，这些块可以响应请求进行加载。
首先，你要用require.ensure 来定义一个分割点
// main.js
require.ensure(['./a'], function(require) {
  var content = require('./a');
  document.open();
  document.write('<h1>' + content + '</h1>');
  document.close();
});
require.ensure告诉webpack，a.js应该是从bundle.js分割开来的，并且形成了单块文件
// a.js   module.exports = 'Hello World';

现在webpack需要管理依赖，输出文件和运行的东西，你不必把任何冗余的代码放置在index.html和配置文件中。
<html>
  <body>
    <script src="bundle.js"></script>
  </body>
</html>
webpack.config.js
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  }
};
Launch the server.
$ webpack-dev-server
表面上，你不会感觉到任何差异，然而，实际上webpack构建了main.js和a.js到不同的块中，并且在需求的时候从bundle.js中加载1.bundle..js.
Demo11: Code splitting with bundle-loader (source)
Another way of code splitting is using bundle-loader.
另一种方法用来分割代码：bundle-loader；
// main.js
// Now a.js is requested, it will be bundled into another filevar load = require('bundle-loader!./a.js');
// To wait until a.js is available (and get the exports)//  you need to async wait for it.load(function(file) {
  document.open();
  document.write('<h1>' + file + '</h1>');
  document.close();
});
require('bundle-loader!./a.js')告诉 webpack 从其他模块加载a.js；
Demo12: Common chunk (source)
当多个脚本具有相同的块时，可以用CommonsChunkPlugin提取公共的部分分离为单独的文件；
// main1.jsxvar React = require('react');var ReactDOM = require('react-dom');
ReactDOM.render(
  <h1>Hello World</h1>,
  document.getElementById('a')
);
// main2.jsxvar React = require('react');var ReactDOM = require('react-dom');
ReactDOM.render(
  <h2>Hello Webpack</h2>,
  document.getElementById('b')
);
index.html
<html>
  <body>
    <div id="a"></div>
    <div id="b"></div>
    <script src="init.js"></script>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
  </body>
</html>
webpack.config.js
var CommonsChunkPlugin = require("webpack/lib/optimize/CommonsChunkPlugin");module.exports = {
  entry: {
    bundle1: './main1.jsx',
    bundle2: './main2.jsx'
  },
  output: {
    filename: '[name].js'
  },
  module: {
    loaders:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        query: {
          presets: ['es2015', 'react']
        }
      },
    ]
  },
  plugins: [
    new CommonsChunkPlugin('init.js')
  ]
}
Demo13: Vendor chunk (source)
你也可以通过CommonsChunkPlugin用一段脚本将供应商库的文件提取到一个单独的文件。
main.js
var $ = require('jquery');
$('h1').text('Hello World');
index.html
<html>
  <body>
    <h1></h1>
    <script src="vendor.js"></script>
    <script src="bundle.js"></script>
  </body>
</html>
webpack.config.js
var webpack = require('webpack');
module.exports = {
  entry: {
    app: './main.js',
    vendor: ['jquery'],
  },
  output: {
    filename: 'bundle.js'
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin(/* chunkName= */'vendor', /* filename= */'vendor.js')
  ]
};
如果你希望一个可用模块可以像一个变量一样放在所有模块中，就像不需要在每个模块中写 require(“jquery”)，就可以使用$和jQuery一样，那么你需要使用ProvidePlugin 插件；
Demo14: Exposing global variables (source)
如果你想使用一些全局变量，并且你不想将他们被webpack 打包，你可以在webpack.config.js配置文件中使用externals 外场来实现；
例如，我们有一个data.js文件：
var data = 'Hello World';
我们可以将data暴露为一个全局变量：
// webpack.config.jsmodule.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    loaders:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        query: {
          presets: ['es2015', 'react']
        }
      },
    ]
  },
  externals: {
    // require('data') is external and available
    //  on the global var data
    'data': 'data'
  }
};
现在你引入了data作为一个可变模块在你的代码中，但是实际上他是一个全局变量；
// main.jsxvar data = require('data');var React = require('react');var ReactDOM = require('react-dom');
ReactDOM.render(
  <h1>{data}</h1>,
  document.body
);
Demo15: Hot Module Replacement (source)
模块热替换(HMR)交换, 添加, 或者删除模块, 同时应用持续运行, 不需要页面刷新.
你有两种方法使用webpack-dev-server 去让模块热交换有效；
(1) Specify --hot and --inline on the command line
$ webpack-dev-server --hot --inline
指令的含义：
--hot：添加热模块替换插件并且将服务切换到热模式
--inline 内联： 嵌入webpack-dev-server 运行时进行打包；
--hot-inline：并且增加了webpack/hot/dev-server  入口
(2)Modify webpack.config.js. 修改配置文件
add new webpack.HotModuleReplacementPlugin() to the plugins field
第一步添加webpack.HotModuleReplacementPlugin() 进入插件区域
add webpack/hot/dev-server and webpack-dev-server/client?http://localhost:8080 to the entry field
第二部 添加两句入口文件
webpack.config.js looks like the following.
var webpack = require('webpack');var path = require('path');
module.exports = {
  entry: [
    'webpfack/hot/dev-server',
    'webpack-dev-server/client?http://localhost:8080',
    './index.js'
  ],
  output: {
    filename: 'bundle.js',
    publicPath: '/static/'
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin()
  ],
  module: {
    loaders: [{
      test: /\.jsx?$/,
      exclude: /node_modules/,
      loader: 'babel-loader',
      query: {
        presets: ['es2015', 'react']
      },
      include: path.join(__dirname, '.')
    }]
  }
};

Now launch the dev server.
$ webpack-dev-server
访问http：//localhost：8080，你需要看‘hello World’ 在你的浏览器显示屏里；
不要关闭服务器，打开新的终端去编辑App.js，并且修改‘hello World’ 变成‘hello webpack’，保存，再看看浏览器中发生了什么改变
App.js
import React, { Component } from 'react';
export default class App extends Component {
  render() {
    return (
      <h1>Hello World</h1>
    );
  }
}
index.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
ReactDOM.render(<App />, document.getElementById('root'));
index.html
<html>
  <body>
    <div id='root'></div>
    <script src="/static/bundle.js"></script>
  </body>
</html>

Demo16: React router (source)
这个demo 使用webpac 去构建了 React-router’s 官方示例，让我们设想一个带有仪表板，收件箱和日历的小应用程序；


