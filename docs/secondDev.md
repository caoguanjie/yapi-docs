# 二次开发
> 创建时间：2020-08-10                
> 更新时间：{docsify-updated}

## 目录结构

```
|-- config.json
|-- init.lock
|-- log
`-- vendors
    |-- CHANGELOG.md
    |-- LICENSE
    |-- README.md
    |-- client
    |-- common
    |-- config_example.json
    |-- doc
    |-- exts
    |-- nodemon.json
    |-- npm-debug.log
    |-- package.json
    |-- plugin.json
    |-- server
    |-- static
    |-- test
    |-- webpack.alias.js
    |-- yapi-base-flow.jpg
    |-- ydocfile.js
    `-- ykit.config.js
```

## 技术栈说明

后端： koa mongoose

前端： react redux

## 启动开发环境服务器

```shell
##进入到项目文件夹
cd vendors
npm run dev
# 启动服务器后，请访问 127.0.0.1:{config.json配置的端口}，初次运行会有个编译的过程，请耐心等候
```

## 启动生产环境服务器
```shell
##进入到项目文件夹
cd vendors
ykit pack -m
node server/app.js
```
!> 每次修改完项目里面的内容时候，需要`ykit pack -m`打包一下，更新的内容才会显示出来

## 针对自动鉴权插件的二次开发
> 起因是：在安装自动鉴权插件yapi-plugin-interface-oauth2-token的时候，不知道什么原因，每次保存完了`获取token的地址`之后，接口地址都会发生变化，导致无法正常自动更新token，因此小编尝试把插件的请求方法修改


1. 进入到项目目录，找到自动获取token的插件代码

```
cd [项目目录]/vendors/node_modules/yapi-plugin-interface-oauth2-token/utils/syncTokenUtil.js
```

2. 定位到token请求失败的代码片段

```js
async execGetToken(getTokenUrl, type, headers_data, data, dataJson, dataType, oauthData, projectData) {
    ...
    // 主要是getTokenUrl的地址错误，导致axios请求没反应，没有返回参数。
     response = await axios.get(getTokenUrl, {
        params: params,
        headers: headersData
    });

}
```

3. 用`request`库替换`axios`，换一种发起请求的库

```js
// 先导入request库，官网地址https://github.com/request/request。 
// request-promise 是基于蓝鸟的可以支持promise语法的库
const rp = require('request-promise');

async execGetToken(getTokenUrl, type, headers_data, data, dataJson, dataType, oauthData, projectData) {
    ...
    let domain; // 域名，因为保存的getTokenUrl没有域名，需要根据项目找到域名
    projectData.env.forEach((element)=>{
        if(element._id == oauthData.env_id){
        domain = element.domain
        }
    })
     var options = {
          uri: domain + getTokenUrl,
          qs: params,
          resolveWithFullResponse: true, //得到完整的response对象，如果为false只能得到响应体
          json: true // Automatically parses the JSON string in the response
      };
      
      let _response =  await rp(options)
      // 组合成axios需要的对象属性，因为axios最后会返回以下属性的固定格式，为了不影响插件的正常流程，我们是想采用request模拟axios的请求方式
      response = {
        data: _response.body,
        headers: _response.headers,
        status: _response.statusCode,
        request: _response.request
      }
    //  response = await axios.get(getTokenUrl, {
    //     params: params,
    //     headers: headersData
    // });
}

```

4. 以上代码开发完之后，用命令检查结果

```shell
cd vendors
##测试的时候输入以下命令
npm run dev

##部署的时候换以下命令
ykit pack -m
node server/app.js
```