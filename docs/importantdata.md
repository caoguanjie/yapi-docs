# 数据导入
> 创建时间：2020-08-10               
> 更新时间：{docsify-updated}

## Postman 数据导入

1. 首先在 postman 导出接口

![图 16](../images/14baf24360ba40ed78233796ad212a3668df729d3bf61e44553f72e1ad1b36fe.png)  

2. 选择 collection_v1,点击 export 导出接口到文件 xxx

![图 17](../images/122ce0a3382150e5e95bf2a4916785e3ea56ade22b55cd1a2e18a9c74178d5e9.png)  

3. 打开 yapi 平台，进入到项目页面，点击数据管理，选择相应的分组和 postman 导入  方式， 选择刚才保存的文件路径，开始导入数据

![图 18](../images/08167385ca42b125cd6e7a15572188045459f0011cb53e90c61815f375757f67.png)  


## HAR数据导入

可用 chrome 实现录制接口数据的功能，方便开发者快速导入项目接口

1. 打开 Chrome 浏览器开发者工具，点击 network，首次使用请先 clear 所有请求信息，确保录制功能开启（红色为开启状态）

![图 19](../images/78111d107470f9d42e33d7c1db8a183adefd2e1205358bcc1cbd90501d6209fa.png)  

2. 操作页面实际功能，完成后点击 save as HAR with content,将数据保存到文件 xxx
!> Tips: save as HAR with content这个选项导出的是所有录制的接口，不是单个接口。
![图 20](../images/dad88b80c64a4442925e0e839675c08994d5ff53cd485a73611beabdaac6e967.png)  

3. 打开 yapi 平台，进入到项目页面，点击数据管理，选择相应的分组和 har 导入  方式， 选择刚才保存的文件路径，开始导入数据

![图 21](../images/57f9adef67b9aaff38f7e307e2901442ea340bff2c352d5e026e21960265165c.png)  

但是这样导入接口也没有那么完美，接口名字是接口路径，以及可能接口入参可能会有被转码的情况。如下图

![图 23](../images/debd8183d1a48e994db2aaae219fa5bd8a0e589fe24f504673275930ad439775.png)  
![图 24](../images/e3011e4cdc759eaaa1ecc78a0b57460c98e775a5b9597f94b2521955784220c9.png)  

!> Tips: har 数据导入只支持 response.content.mimeType 为 application/json 类型的数据

## Swagger 数据导入

[什么是 Swagger ？](docs/knowledge.md?id=什么是swagger)

1.生成 JSON 语言编写的 Swagger API 文档文件

例如这样的数据 （http://petstore.swagger.io/v2/swagger.json），可以将其内容复制到 JSON 文件中。

> Tips: yapi支持 swagger url 导入功能

2.打开 yapi 平台，进入到项目页面，点击数据管理，选择相应的分组和 swagger 导入  方式， 选择刚才的文件，开始导入数据

![图 25](../images/8617ee6d68cb8b251b51488cc8c71f7740f6c0a76001f589adda245e91be9d4f.png)  
![图 26](../images/ff1916aed7372065526561ee4d1a56e9f90c19f2daffe2a3e6127fb8525d0a26.png)  
![图 27](../images/b7eb94a27e3f1bb66d9b212898ac2d56c7956334950ce224119db1f0bd3780f6.png)  


## JSON 数据导入

可以导入在 yapi 平台导出的 json 接口数据。

![图 28](../images/dc5423e651330bfae89603a8bddb52fb1629c42e8baa3846b10a2c0230351782.png)  

## 通过命令行导入接口数据
YApi 支持通过命令行导入接口数据，他的应用场景是做自动化集成，比如配合 swagger ，接口文档前端不用维护，交由后端生成。

#### 使用方法

第一步，确保 yapi-cli >= 1.2.7 版本，如果低于此版本请升级 yapi-cli 工具

```shell
npm install -g yapi-cli
```

第二步，在任意一个目录下新建配置文件 yapi-import.json，内容如下：

```json
{
  "type": "swagger",
  "token": "17fba0027f300248b804",
  "file": "swagger.json",
  "merge": "normal",
  "server": "http://yapi.local.qunar.com:3000"
}
```
`type` 是数据数据方式，目前官方只支持 swagger

`token` 是项目 token，在 项目设置 -> token 设置获取

`file` 是 swagger 接口文档文件，可使用绝对路径或 url

`merge` 有三种导入方式(v1.3.23+支持) normal, good, mergin

1. 普通模式(normal)：不导入已存在的接口；
2. 智能合并(good)：已存在的接口，将合并返回数据的 response，适用于导入了 swagger 数据，保留对数据结构的改动；
3. 完全覆盖(mergin)：不保留旧数据，完全使用新数据，适用于接口定义完全交给后端定义， 默认为 normal

`server` 是 yapi 服务器地址

第三步，在新建配置文件的当前目录，执行下面指令

```
yapi import
```