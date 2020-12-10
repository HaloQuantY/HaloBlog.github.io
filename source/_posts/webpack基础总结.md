## webpack 基础总结

### 基础使用方法

- 配置webpack.config.js
- 项目根目录cli下运行npx webpack指令，默认以webpack.config.js配置运行

#### 配置webpack.config.js

​	webpack.config.js最基本的是以下5个属性，入口文件，输出目录，loader，插件和打包模式：

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.js",
  output: {
      path: path.resolve(__dirname, "pathname"),
      filename: "filename"
  },
  module: {
      rules: [
          
      ]
  },
  plugins: [
      
  ],
  mode: "development/production"
};
```





### 开发环境基础配置

- 打包js资源
- 打包html资源
- 打包样式资源
- 打包图片资源
- 打包其他文件
- 配置devserver
- 配置source-map

#### 打包js资源

​	webpack默认就可以打包js资源，只需要在入口js文件当中引入对应的模块，运行webpack指令就可以将入口文件依赖的模块整合到输出js文件当中。

#### 打包html资源

​	webpack默认只会打包js文件，其他文件的打包需要使用插件或者loader进行处理。

​	html资源使用`html-webpack-plugin`资源进行自动生成，输出的html文件会自动引入打包好的js文件。

```javascript
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  plugins: [
      new HtmlWebpackPlugin({
          // 传入一个配置对象中可以设置模板html文件
          template: "./src/index.html"
      })
  ]
};
```

#### 打包样式资源

​	开发环境下打包样式资源不过多考虑兼容性等问题，最基础的样式资源打包使用style-loader和css-loader两个loader进行打包。

```javascript
module.exports = {
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    "style-loader",
                    "css-loader"
                ]
            },
            {
                test: /\.less$/,
                use: [
                    "style-loader",
                    "css-loader",
                    // 处理less资源需要在最下方添加less-loader
                    "less-loader"
                ]
            }
        ]
    }
};
```

​	然后在入口js文件中直接引入css/less文件即可

```javascript
// index.js
import "./src/xxx.less"
```

​	关于style-loader，css-loader和less-loader，在webpack配置文件当中写的loader列表，执行顺序是由下至上。

​	因此对于less文件会先执行less-loader将less文件转换为css文件，然后在入口js文件当中引入css文件时使用css-loader将css文件整合到输出js代码中，最后在html文件中使用style-loader将css代码分离到html文档的style标签中。

​	开发环境下这种执行流程在生产环境中不同，生产环境需要将css分离成单独文件并且需要执行兼容性适配，将选用不同插件和loader进行处理。

#### 打包图片资源

- 打包html文件以外的图片资源
- 处理html中的img元素等自带的图片资源

##### 打包html文件以外图片资源

​	使用file-loader和url-loader对图片资源进行打包，在配置文件当中书写的时url-loader，但url-loader依赖于file-loader因此需要下载两个包。

```javascript
module.exports = {
    module: {
        rules: [
            {
                test: /\.(jpg|png|gif|svg)$/,
                loader: "url-loader",
                // 对于url-loader需要设置一些选项属性
                options: {
                    // 此处limit属性表示大小在8kb以下图片会整合到js文件中，不单独生成一个链接以减少请求数量
                    limit: 8 * 1024,
                    // esModule属性表示是否启用es模块语法，由于后面使用到的html-loader不对esModule兼容，因此选择关闭
                    esModule: false
                }
            }
        ]
    }
};
```

##### 打包html中图片资源

​	使用html-loader对html文件中图片资源进行整合。

```javascript
module.exports = {
    module: {
        rules: [
            test: /\.html$/,
            loader: "html-loader"
        ]
    }
};
```

#### 打包其他资源

​	对于其他资源（主要是字体），都可以使用file-loader进行打包，只是对于test属性，需要替换成为exclude属性进行识别。

```javascript
module.exports = {
	module: {
		rules: [
            {
                exclude: /\.(html|js|css|less|jpg|png|gif|svg)$/,
                loader: "file-loader"
            }
        ]		
	}
};
```

#### 配置devServer

​	devServer可以在源文件改动时自动进行重构建，需要使用`webpack-dev-server`包（依赖于webpack和webpack-cli）实现。

```javascript
const path = require("path");

module.exports = {
    devServer: {
        // 输出目录
        contentBase: path.resolve(__dirname, "dist"),
        // 是否进行gzip压缩
        compress: true,
        // 本地服务器启动端口
        port: 3000
    }
};
```

​	要启动devServer使用`npx webpack serve`指令即可。

​	devServer中进行的项目重构建仅在内存当中进行，不会进行硬盘读写，因此速度较快并且不会在本地硬盘中实际构建项目。

#### 配置source-map

​	在配置js中，devtool属性会指定source-map类型，根据不同的类型webpack会构建不同srouce-map：

https://www.webpackjs.com/configuration/devtool/

```javascript
module.exports = {
    devtool: "eval-source-map/source-map"
}
```

​	对于开发环境一般使用eval-source-map，而构建环境不隐藏代码情况下可以直接使用source-map。

#### 开发环境webpack.config.js配置

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");

module.exports = {
    entry: "./src/js/index.js",
    output: {
        path: path.resolve(__dirname, "dist"),
        filename: "js/main.js"
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    "style-loader",
                    "css-loader"
                ]
            },
            {
                test: /\.less$/,
                use: [
                    "style-loader",
                    "css-loader",
                    "less-loader"
                ]
            },
            {
                test: /\.(jpg|png|gif|svg)$/,
                loader: "url-loader",
                options: {
                    limit: 8 * 1024,
                    esModule: false
                }
            },
            {
                test: /\.html$/,
                loader: "html-loader"
            },
            {
                exclude: /\.(html|js|css|less|jpg|png|gif|svg)$/,
                loader: "file-loader"
            }
        ],
        plugins: [
            new HtmlWebpackPlugin({
                template: "./src/index.html"
            }),
            new CleanWebpackPlugin()
        ],
        mode: "development",
        devtool: "eval-source-map",
        devServer: {
            contentBase: path.resolve(__dirname, "dist"),
            compress: true,
            port: 3000
        }
    }
};
```



### 生产环境配置

​	生产环境配置主要进行css拆分为单独文件以及对代码进行压缩。

- 打包单独css文件并进行兼容性和压缩处理
- 对js文件进行语法检查，兼容性处理和压缩
- 压缩html文件

#### css文件处理

​	开发环境中css集成到了输出的js文件当中，并通过style-loader引入到html文件的style标签中。

​	生产环境中需要将css代码整合到单独的css文件中，并进行兼容性和压缩处理。

##### 拆分为单独css文件

​	使用`mini-css-extract-plugin`实现从输出js文件中对css文件的提取。

```javascript
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
    plugins: [
      new MiniCssExtractPlugin({
          // 输出目录需要在插件中进行定义
          filename: "css/built.css"
      })  
    ],
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    // 相比开发环境替换了style-loader
                    MiniCssExtractPlugin.loader,
                    "css-loader"
                ]
            }
        ]
    }
};
```

##### 兼容性处理

​	兼容性处理需要使用loader`postcss-loader`，该loader需要和`postcss-preset-env`配合使用，达到兼容特定浏览器效果。

```javascript
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
    plugins: [
      new MiniCssExtractPlugin({
          // 输出目录需要在插件中进行定义
          filename: "css/built.css"
      })  
    ],
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    // 相比开发环境替换了style-loader
                    MiniCssExtractPlugin.loader,
                    "css-loader",
                    // ！兼容性处理在此处添加loader配置
                    {
                        loader: "postcss-loader",
                        options: {
                            postcssOptions: {
                                plugins: [["postcss-preset-env"]]
                            }
                        }
                    }
                ]
            }
        ]
    }
};
```

​	兼容性处理还需要在package.json中，添加需要兼容的浏览器列表

```javascript
// package.json

"browsersList": {
    "development": [
        "last 1 chrome version",
        "last 1 firefox version",
        "last 1 safari version"
    ],
    "production": [
        ">0.2%",
        "not dead",
        "not op_mini all"
    ]
}

// 浏览器列表中支持的浏览器默认会以生产模式进行，如要在开发模式中运行需要修改环境变量
// process.env.NODE_ENV = "development"; 此行添加在webpack.config.js
```

##### 压缩css代码

​	使用插件`optimize-css-assets-webpack-plugin`即可以完成对css代码的压缩。

```javascript
const OptimizeCssAssetsWebpackPlugin = require("optimize-css-assets-webpack-plugin");

module.exports = {
  plugins: [
      new OptimizeCssAssetsWebpackPlugin()
  ]
};
```

#### js文件处理

​	对于js文件，生产环境当中需要进行js语法检查，兼容性处理和代码压缩。

##### js语法检查

​	Js文件的语法检查常用eslint工具，webpack使用时需要加载`eslint`和`eslint-loader`

```javascript
module.exports = {
  module: {
      rules: [
          {
              test: /\.js$/,
              // 设置规则排除node_modules文件夹下所有文件
              exclude: /node_modules/,
              loader: "eslint-loader",
              options: {
                  // 自动修复error
                  fix: true
              }
          }
      ]
  }  
};
```

​	eslint还需要在package.json中设置代码规则，一般使用airbnb规则。要将airbnb规则在eslint中应用需要在npm中下载`eslint-config-airbnb-base`(不包含react插件)和`eslint-plugin-import`作为依赖。

```javascript
// package.json

"eslintConfig": {
    "extends": "aribnb-base"
}
```

##### js兼容性处理

​	js文件的兼容性处理使用babel工具，加载`babel-loader`，`babel/core`和`@babel/preset-env`作为依赖。

```javascript
module.exports = {
  module: {
      rules: [
          {
              test: /\.js$/,
              exclude: /node_modules/,
              loader: "babel-loader"
          }
      ]
  }  
};
```

​	然后在项目根目录下创建`.babelrc`文件，并在其中写入设置

```javascript
// .babelrc

{
    "presets": [
        [
        	"@babel/preset-env"    
        ]
    ]
}
```

​	进行以上设置就可以对ES6语法进行兼容转换，但要实现API兼容如promise等功能还需要使用`@babel/runtime`插件进行转换。

​	下载`@babel/runtime`,`@babel/plugin-transform-runtime`和`@babel/runtime-corejs3`进行API转换。`@babel/runtime`插件是Babel提供的polyfill插件

https://awdr74100.github.io/2020-03-16-webpack-babelloader/

```javascript
// .babelrc

{
    "presets": [
        [
            "@babel/preset-env"
        ]
    ],
    "plugins": [
        [
            "@babel/plugin-transform-runtime",
            {
                "corejs": 3
            }
        ]
    ]
}
```





