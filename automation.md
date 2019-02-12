#### webpack性能优化

* 减少Webpack打包时间  
  i. 优化Loader。影响打包效率的首先是Babel。因为babel将代码转成字符串生成AST，然后对AST继续转变最后再生成新的代码，项目越大，转换的代码越多，效率越低。因此可以优化Loader的文件搜索范围，还可以把Babel编译后的文件缓存起来
  ```javascript
  loader: 'babel-loader?cacheDirectory= true',
  exclude: /node_modules/,
  ```
  ii. HappyPack将Loader的同步执行转换为并行的。
  iii. 代码压缩，UglifyJS
  iv. resolve.extensions, 尽量把出现频率高的后缀排在前面, resolve.alias 通过别名来映射一个路径，能让webpack更快的找到路径，module.noParse 如果确定一个文件没有依赖，可以使用该属性不让webpack扫描该文件

* 减少webpack打包后的文件体积  
  i. 按需加载，将每个路由单独打包为一个文件， 按需加载。
  ii. Scope Hoisting(作用域提升): 会分析出模块之前的依赖关系，尽可能的把打包出来的模块合并到一个函数中去，webpack 4 启用optimization.concatenateModules即可。
  iii. Tree Shaking可以删除项目中未被引用的代码



