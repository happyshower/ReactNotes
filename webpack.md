# 学习记录

### webpack

- webpack 安装及其 plugins 和 loaders 安装

  ```
  //安装webpack
  npm i webpack webpack-cli -D

  npm i -save-dev css-loader
  npm i -save-dev style-loader
  npm i less less-loader -save-dev

  //安装eslint
  npm install eslint --save-dev
  npm install eslint-webpack-plugin --save-dev

  npm install html-webpack-plugin --save-dev

  //安装babel
  npm install -D babel-loader @babel/core @babel/preset-env

  //开发环境(需要配置开发环境文件，创建一个webpack.dev.js)
  npx webpack server --config ./config/webpack.dev.js
  //生产环境(需要配置开发环境文件，创建一个webpack.prod.js)
  npx webpack --config ./config/webpack.prod.js

  //自动化(热部署)
  npm i webpack-dev-server -D

  //提取css成单独文件
  npm i mini-css-extract-plugin -D

  //Css兼容性处理
  npm install --save-dev postcss-loader postcss postcss-preset-env

  //优化和压缩 CSS
  npm install css-minimizer-webpack-plugin --save-dev
  //多线程
  npm install --save-dev thread-loader
  //减少体积 ( Babel 在每个文件都插入了辅助代码，使代码体积过大！)
  npm install -D @babel/plugin-transform-runtime

  //压缩图片体积
  npm install image-minimizer-webpack-plugin --save-dev
  //无损压缩
  npm install imagemin-gifsicle imagemin-jpegtran imagemin-optipng imagemin-svgo --save-dev
  //有损压缩
  npm install imagemin-gifsicle imagemin-mozjpeg imagemin-pngquant imagemin-svgo --save-dev
  ```

- webpack 基本配置

  1. entry：打包入口（相对路径）
  2. output：输出打包后的文件
     - path: 所有文件的输出路径
     - filename: 入口文件打包输出文件名（绝对路径）
     - clean: 自动清空上次打包的内容（true 或 false）
  3. module：加载器，配置 loader
     - test 定义匹配文件规则
     - use 使用的加载器，打包 css 资源一般用 style-loader,css-loader(从右到左)
  4. plugins：插件
  5. optimization：优化
  6. mode：开发环境（development 或 production）
  7. devtool：配置打包文件和源文件的资源映射方式

  ```js
  const HtmlWebpackPlugin = require("html-webpack-plugin");
  const path = require("path");
  const ESLintPlugin = require("eslint-webpack-plugin");
  const MiniCssExtractPlugin = require("mini-css-extract-plugin");
  const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
  const os = require("os");
  const TerserWebpackPlugin = require("terser-webpack-plugin");

  //返回系统中 CPU 的数量
  const threads = os.cpus().length;

  module.exports = {
    // 单入口
    entry: "./src/main.js", //相对路径
    //多入口
    // entry:{
    //     app:'./src/app.js',
    //     main:'./src/main.js'
    // },
    //输出
    output: {
      path: path.resolve(__dirname, "../dist"), //绝对路径
      //单文件输出
      filename: "static/js/main.js",
      //多文件输出命名方式
      // filename:'[name].js'

      //自动清空上次打包的内容
      clean: true,
    },
    //加载器
    module: {
      //loader的配置
      rules: [
        {
          test: /\.css$/, //匹配以 .css 结尾的文件
          use: [
            MiniCssExtractPlugin.loader, //将CSS提取到单独的文件中
            "css-loader", //将css资源编译成commonjs的模块到js中
            {
              loader: "postcss-loader", //对 css 进行兼容性处理，如添加浏览器前缀等
              options: {
                postcssOptions: {
                  plugins: ["postcss-preset-env"], //将 css 代码转换为更兼容的形式，以适应不同的浏览器环境
                },
              },
            },
          ],
        },
        {
          test: /\.less$/, //匹配以 .less 结尾的文件
          use: [
            MiniCssExtractPlugin.loader, //将CSS提取到单独的文件中
            "css-loader", //将css资源编译成commonjs的模块到js中
            {
              loader: "postcss-loader", //对 css 进行兼容性处理，如添加浏览器前缀等
              options: {
                postcssOptions: {
                  plugins: ["postcss-preset-env"],
                },
              },
            },
            "less-loader", //将 less 文件编译为 css 文件
          ],
        },
        {
          test: /\.s[ac]ss$/, //匹配以 .scss/.sass 结尾的文件
          use: [
            MiniCssExtractPlugin.loader, //将CSS提取到单独的文件中
            "css-loader", //将css资源编译成commonjs的模块到js中
            {
              loader: "postcss-loader", //对 css 进行兼容性处理，如添加浏览器前缀等
              options: {
                postcssOptions: {
                  plugins: ["postcss-preset-env"],
                },
              },
            },
            "sass-loader", //将 sass/scss 文件编译为 css 文件
          ],
        },
        {
          test: /\.styl$/, //匹配以 .styl 结尾的文件
          use: [
            MiniCssExtractPlugin.loader, //将CSS提取到单独的文件中
            "css-loader", //将css资源编译成commonjs的模块到js中
            {
              loader: "postcss-loader", //对 css 进行兼容性处理，如添加浏览器前缀等
              options: {
                postcssOptions: {
                  plugins: ["postcss-preset-env"],
                },
              },
            },
            "stylus-loader", //将 styl 文件编译为 css 文件
          ],
        },
        {
          test: /\.(png|jpe?g|gif|webp|svg)$/, //匹配不同后缀的图片文件
          type: "asset/resource",
          parser: {
            dataUrlCondition: {
              //小于10kb的图片转base64，减少请求数量
              maxSize: 10 * 1024,
            },
          },
          generator: {
            //输出图片名称
            //hash:10==>取hash值的前10位
            filename: "static/images/[hash:10][ext][query]",
          },
        },
        {
          test: /\.(ttf|woff2?|mp3|avi)$/, //匹配不同后缀的音频文件
          type: "asset/resource",
          generator: {
            //输出音频名称
            //hash:10==>取hash值的前10位
            filename: "static/media/[hash:10][ext][query]",
          },
        },
        {
          test: /\.js$/,
          exclude: /node_modules/, //排除node_modules中的js文件
          use: [
            {
              loader: "thread-loader", //开启多线程
              options: {
                works: threads, //进程数量
              },
            },
            {
              loader: "babel-loader",
              options: {
                // presets:['@babel/preset-env']
                cacheDirectory: true, //开启babel缓存
                cacheCompression: false, //关闭缓存文件压缩
                plugins: ["@babel/plugin-transform-runtime"],
              },
            },
          ],
        },
      ],
    },
    //插件
    plugins: [
      new HtmlWebpackPlugin({
        //模板：以public/index.html文件创建新的html文件
        template: path.resolve(__dirname, "../public/index.html"),
      }),
      new ESLintPlugin({
        //查找和修复 JavaScript 代码中的问
        context: path.resolve(__dirname, "../src"), //指定文件根目录
        cache: true,
        cacheLocation: path.resolve(
          __dirname,
          "../node_modules/.cache/eslintcache"
        ),
        threads, //开启多线程和设置进程数量
      }),
      new MiniCssExtractPlugin({
        filename: "static/css/main.css", //输出 CSS 文件的名称
      }),
    ],
    optimization: {
      //压缩的操作
      minimizer: [
        //压缩css
        new CssMinimizerPlugin(),
        //压缩js
        new TerserWebpackPlugin({
          parallel: threads, //开启多线程和设置进程数量
        }),
      ],
    },
    mode: "production", //development为开发环境，production为生产模式
    devtool: "source-map", //维护打包处理后的代码与源代码之间的映射关系
  };
  ```
