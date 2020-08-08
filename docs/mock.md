# 数据Mock
> 创建时间：2020-08-08               
> 更新时间：{docsify-updated}

## Mock介绍

YApi的 Mock 功能可以根据用户的输入接口信息如协议、URL、接口名、请求头、请求参数、返回数据生成 Mock 接口，这些接口会自动生成模拟数据，创建者可以自由构造需要的数据。mock技术对前端开发来说接触得比较多，简单来说，mock就是一种模拟真实数据的一种技术手段。

mock地址解析：YApi平台网址 + mock + 您的项目id + 接口实际请求path

假设 YApi 的部署地址为：http://192.168.32.230:3000 然后用这个地址作为示例

```
mockd 地址： http://192.168.32.230:3000/mock/29/api/SysLoginConfig/LoginValidate
```

!> 注：项目 id 可以在项目设置里查看到

## Mock实际使用场景

使用最多的场景是当后端人员已经写好接口文档（也可以在yapi接口网站定义好出入参数），此时接口还没开始进入开发阶段，但是在市场上前后端分离的项目中，其实前端和后端可以同时开始开发项目，前端是非常依赖接口数据来做交互，如果没有接口，前端只能做静态页面，大大降低了前端开发速度和效率，如果用mockjs来模拟数据，完全是可以当做接口是已经开发完成的状态，前端来根据数据来做交互，而且可以随意定义好数据，方便在开发过程更好验证有没有bug，开发出来质量更好。


### 定义 mock 数据示例

项目 -> 接口编辑 -> 返回数据设置

返回数据设置有两种方式，最新版本默认是基于 json+注释 的方式，另外一种是基于 json-schema 定义数据结构,请根据实际情况灵活选择使用。

## 方式1. mockjs

![图 14](../images/cdbdab80d1819344022a3cea55e87e6ba641ae5c26fc7386491402b787f70034.png)  

### 原理
基于 [mockjs](http://mockjs.com/)，跟 `Mockjs` 区别是 `yapi` 基于 json + 注释 定义 `mock` 数据，无法使用 `mockjs` 原有的函数功能。

1. 正则表达式需要基于 rule 书写，示例如下：

```js
{
  "name|regexp": "[a-z0-9_]+?",
  "type|regexp": "json|text|xml"
}
```

2. 支持替换请求的 query, body 参数
```js
{
  "name": "${query.name}", //请求的url是/path?name=xiaoming, 返回的name字段是xiaoming
  "type": "${body.type}",   //请求的requestBody type=1,返回的type字段是1
}
```

3. 示例

```js
/**
 * 这是一个接口返回数据示例
 */
{
    "errcode": 0,
    "errmsg": "@word",
    "data": {
        "id": "@id", //@id 随机生成 id
        "name": "@name" //@name 随机生成用户名
    }
}
```

详细使用文档请查看：[Mockjs 官网](http://mockjs.com/)

## 方式2. json-schema

![图 15](../images/88422eb0a72b11e63e1a9a519fb7fe3c49a2d1284abb378cb575f4f73b75a6ee.png)  

开启 json-schema 功能后，根据 json-schema 定义的数据结构，生成随机数据。

如何生成随机的邮箱或 ip

![图 16](../images/63ab87062c168d113e261d9ec9814a80c59b24a8ab971b27aed3ceb7f5749196.png)  


点击高级设置，选择 format 选项，比如选择 email 则该字段生成随机邮箱字符串。

### 集成 mockjs

基本书写方式为 `mock` 的数据占位符@xxx, 你可以在返回数据的mock输入框内，可以看到有很多下拉值，具体字段详见[Mockjs 官网](http://mockjs.com/)

![图 17](../images/a6b40d7eae3392ded7290bf10a40929deb78fa17fdbd607d08f7a52ed44b68f7.png)  
![图 18](../images/63803e59243a81bbe525aa93e4e3c69c3f4bdb9352bf8139a4866b04ec616838.png)  

!> 如果不是以@字符开头的话或者匹配不到Mockjs中的占位符就会直接生成输入的值

## 如何使用 Mock

#### 1. 在 js 代码直接请求yapi提供的 mock 地址（不用担心跨域问题）
在代码直接请求 yapi 提供的 mock 地址，以 jQuery 为例：

```js
let url = 'http://192.168.32.230:3000/mock/29/api'; // 接口还没有开发完成的时候需要请求的mock地址
// let prefix = 'http://正式环境的的ip地址'
$.post(url+'/SysLoginConfig/LoginValidate', {username: 'xxx'}, function(res){
    console.log(res) //返回上图预览部分的数据
})
```

#### 2. 基于本地服务器反向代理
优点:不用修改项目代码

##### 2.1 基于 nginx 反向代理
```shell
location /baseapi
{
proxy_pass   http://yapi.xxx.com/mock/2817/baseapi; #baseapi后面没有"/"
}
```

##### 2.1 基于 Charles 代理

点击 Charles 工具栏下的 tools >> Rewrite Settings 填写如下信息：

![图 19](../images/9f23edbe684d7df498cbf71c334c60bfbb631833c536510224e633b575043815.png)  

## mock请求严格模式

 新增 mock 接口请求字段参数验证功能，具体使用方法如下：

 1. 打开 项目 -> 设置 开启 mock 严格模式
 2. 针对 query, form 中设置的必须字段会进行必填校验

![图 20](../images/987e15df7b31a26383bc096da64c850c6dd3ddb4b6aeccaca01a177075c44778.png)  

  3. 针对 req_body_type 是json schema 格式的数据进行校验
    ![图 21](../images/1cfbac1dd95e4d5a228c3af8d7b0bebd7e07909ed66186fcca8c0e9a6b29f3c8.png)  
    ![图 22](../images/3789767abd46149aabe7250c481db3e53c6611b407da70ff0745f30991054289.png)  


 