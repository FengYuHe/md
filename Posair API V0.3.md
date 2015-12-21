# Posair API
======

## 接口地址
公共接口访问地址是：http://pos.dolink.co/rest/v1

## 基本概念
* `node`  节点：拥有序列号的虚拟对象，可承载多个设备。
* `device` 设备：实体或虚拟的功能集合体，比如：POS 代理器，可拥有多个功能（`channel`）。
* `Protocol` 协议：设备功能的接口定义。比如：打印机的打印功能协议。
* `channel` 设备功能点：即设备和协议之间的绑定，一个功能的具体实现。

## REST API

### 回调

* [`POST /user/callback`](#postCallback)：设定回调
* [`DELETE /user/callback`](#deleteCallback)：清除回调

### 激活配对

* [`GET /node/:nodeId/activate`](#activate)：设备请求激活当前节点
* [`POST /node`](#claim)：用户请求配对一个节点

### 设备

* [`GET /device`](#getAllDevice)：获取用户下所有设备
* [`GET /device/:deviceId`](#getDevice)：获取指定设备
* [`DELETE /device/:deviceId`](#deleteDevice)：删除指定设备
* [`POST /remcall/:deviceId/channel/:channelId`](#postdata)：远程调用设备功能－－下发指令

-------------------

### **回调**
=================
<a name="postCallback" />
### 设定回调
**[POST] /user/callback**
#### 注释
对指定用户设定回调。

用户下的设备上报数据时，将通过调用此回调发送数据给用户。

此接口需带上`access_token`作为请求凭证去请求接口，访问接口时，可以把它们附加到 HTTP 头的 
`Authorization` 或者以 `access_token=<token>` 的形式附加到 URL 访问地址上。

`access_token`由服务端生成，通过登录后台管理Web中获取

#### 参数
* `url`  回调地址

#### Example
```sh
curl -X 'POST' \
-H 'Content-Type: application/json' \
-H 'Authorization: YOUR_ACCESS_TOKEN' \
-d '{ "url" : "http://my-app.herokuapp.com/callback_handler" }' \
-i http://localhost/rest/user/callback
```
#### Sample JSON Response
```json
{
  "result": 1,
  "error": null,
  "id": 0
}
```
-----------------------
<a name="deleteCallback" />
### 清除回调
**[DELETE] /user/callback**
#### 注释
清楚指定用户现有回调。

一旦此请求返回，则系统不再发布消息到以前的回调。

此接口需带上`access_token`作为请求凭证去请求接口，访问接口时，可以把它们附加到 HTTP 头的 
`Authorization` 或者以 `access_token=<token>` 的形式附加到 URL 访问地址上。

`access_token`由服务端生成，通过登录后台管理Web中获取

#### Example
```sh
curl -X 'DELETE' \
-H 'Authorization: YOUR_ACCESS_TOKEN' \
-i http://localhost/rest/user/callback
```
#### Sample JSON Response
```json
{
  "result": 1,
  "error": null,
  "id": 0
}
```
-----------------------

### **激活配对**
=================

<a name="activate" />
### 设备请求激活当前节点
**[GET] /node/:nodeId/activate**
#### 注释
设备调用激活请求，成功返回`token`，`token`用于设备调用其他接口的凭证

* `nodeId` 设备的序列号

#### Example
```sh
curl -i http://localhost/rest/node/123456789ABC/activate
```
#### Sample JSON Response
```json
{
  "type": "node_activate",
  "data": {
    "owner": "448c1540-5ac0-11e5-be4d-1dbcb42a9fe6",
    "nodeId": "123456789ABC",
    "token": "ntok-b8820cc38b8232322d0e12c9b649e632"
  }
}
```
-----------------------
<a name="claim" />
### 用户请求配对一个节点
**[POST] /node**
#### 注释
此接口需带上`access_token`作为请求凭证去请求接口，访问接口时，可以把它们附加到 HTTP 头的 
`Authorization` 或者以 `access_token=<token>` 的形式附加到 URL 访问地址上。

30S没有与设备激活配对成功则返回超时

* `access_token`由服务端生成，通过登录后台管理Web中获取

#### 参数
* `nodeId` 设备的序列号

#### Example
```sh
curl -X 'POST' \
-H 'Authorization: YOUR_ACCESS_TOKEN' \
-H 'Content-Type: application/json' \
-d '{"nodeId":"123456789ABC"}' \
-i http://localhost/rest/node
```
#### Sample JSON Response
```json
{
  "type": "node_claim",
  "data": {
    "metadata": {
      "hardwareType": "unknown"
    },
    "owner": "448c1540-5ac0-11e5-be4d-1dbcb42a9fe6",
    "nodeId": "123456789ABC",
    "token": "ntok-b8820cc38b8232322d0e12c9b649e632"
  }
}
```
-----------------------

### **设备**
=================

<a name="getAllDevice" />
### 获取用户下所有设备
**[GET] /device**
#### 注释
此接口需带上`access_token`作为请求凭证去请求接口，访问接口时，可以把它们附加到 HTTP 头的 
`Authorization` 或者以 `access_token=<token>` 的形式附加到 URL 访问地址上。

* `access_token`由服务端生成，通过登录后台管理Web中获取


#### Example
```sh
curl -X 'GET' \
-H 'Authorization: YOUR_ACCESS_TOKEN' \
-i http://localhost/rest/device
```
#### Sample JSON Response
```json
{
  "type": "list",
  "data": [
    {
      "owner": "448c1540-5ac0-11e5-be4d-1dbcb42a9fe6",
      "topic": "$device/123456789",
      "schema": "http://schema.dolink.co/service/device",
      "supportedMethods": null,
      "supportedEvents": [],
      "id": "123456789",
      "naturalId": "1",
      "naturalIdType": "hue",
      "name": "Broken",
      "signatures": {
        "manufacturer:productModelId": "LCT001",
        "dolink:manufacturer": "Phillips",
        "dolink:productName": "Hue",
        "dolink:productType": "Light",
        "dolink:thingType": "light"
      },
      "manufacturer": "Phillips",
      "channels": [
        {
          "topic": "$device/123456789/channel/batch",
          "schema": "http://schema.dolink.co/protocol/core/batching",
          "supportedMethods": [
            "setBatch"
          ],
          "supportedEvents": [],
          "id": "batch",
          "protocol": "core/batching",
          "deviceId": "123456789"
        },
        {
          "topic": "$device/123456789/channel/on-off",
          "schema": "http://schema.dolink.co/protocol/on-off",
          "supportedMethods": [
            "set",
            "toggle",
            "turnOff",
            "turnOn"
          ],
          "supportedEvents": [],
          "id": "1",
          "protocol": "on-off",
          "deviceId": "123456789"
        }
      ]
    }
  ]
}
```
----------------------
<a name="getDevice" />
### 获取指定设备
**[GET] /device/:deviceId**
#### 注释
此接口需带上`access_token`作为请求凭证去请求接口，访问接口时，可以把它们附加到 HTTP 头的 
`Authorization` 或者以 `access_token=<token>` 的形式附加到 URL 访问地址上。

* `access_token`由服务端生成，通过登录后台管理Web中获取
* `deviceId` 设备ID,设备唯一标识

#### Example
```sh
curl -X 'GET' \
-H 'Authorization: YOUR_ACCESS_TOKEN' \
-i http://localhost/rest/device/123456789
```
#### Sample JSON Response
```json
{
  "type": "object",
  "data": {
    "owner": "448c1540-5ac0-11e5-be4d-1dbcb42a9fe6",
    "topic": "$device/123456789",
    "schema": "http://schema.dolink.co/service/device",
    "supportedMethods": null,
    "supportedEvents": [],
    "id": "123456789",
    "naturalId": "1",
    "naturalIdType": "hue",
    "name": "Broken",
    "signatures": {
      "manufacturer:productModelId": "LCT001",
      "dolink:manufacturer": "Phillips",
      "dolink:productName": "Hue",
      "dolink:productType": "Light",
      "dolink:thingType": "light"
    },
    "manufacturer": "Phillips",
    "channels": [
      {
        "topic": "$device/123456789/channel/batch",
        "schema": "http://schema.dolink.co/protocol/core/batching",
        "supportedMethods": [
          "setBatch"
        ],
        "supportedEvents": [],
        "id": "batch",
        "protocol": "core/batching",
        "deviceId": "123456789"
      },
      {
        "topic": "$device/123456789/channel/on-off",
        "schema": "http://schema.dolink.co/protocol/on-off",
        "supportedMethods": [
          "set",
          "toggle",
          "turnOff",
          "turnOn"
        ],
        "supportedEvents": [],
        "id": "1",
        "protocol": "on-off",
        "deviceId": "123456789"
      }
    ]
  }
}
```
-----------------------
<a name="deleteDevice" />
### 删除指定设备
**[DELETE] /device/:deviceId**
#### 注释
此接口需带上`access_token`作为请求凭证去请求接口，访问接口时，可以把它们附加到 HTTP 头的 
`Authorization` 或者以 `access_token=<token>` 的形式附加到 URL 访问地址上。

* `access_token`由服务端生成，通过登录后台管理Web中获取
* `deviceId` 设备ID,设备唯一标识

#### Example
```sh
curl -X 'DELETE' \
-H 'Authorization: YOUR_ACCESS_TOKEN' \
-i http://localhost/rest/device/123456789
```
#### Sample JSON Response
```json
{
  "type": "object",
  "data": {
    "success": true
  }
}
```
-----------------------
<a name="putdata" />
### 远程调用设备功能－－下发指令
**[POST] /remcall/:deviceId/channel/:channelId**
#### 注释
此接口需带上`access_token`作为请求凭证去请求接口，访问接口时，可以把它们附加到 HTTP 头的 
`Authorization` 或者以 `access_token=<token>` 的形式附加到 URL 访问地址上。

* `access_token`由服务端生成，通过登录后台管理Web中获取
* `deviceId` 设备ID,设备唯一标识
* `channelId` 设备功能点，例：posair

#### Example
```sh
curl -X 'POST' \
-H 'Authorization: YOUR_ACCESS_TOKEN' \
-H 'Content-Type: application/json' \
-d '{
    "method": "print",
    "params": "SGVsbG8gV29ybGQhIOS9oOWlve+8gQ=="
  }' \
-i http://localhost/rest/remcall/device/123456789/channel/posair
```
#### Sample JSON Response
```json
{
  "type": "object",
  "data": {
    "success": true
  }
}
```
-----------------------


## MQTT

### 连接
客户端以`token`作为`username`的形式请求服务端连接，服务端获取`username`验证`token`是否合法，如果合法则允许连接，否则拒绝连接。

-----------------------

### 消息
* 一旦连接上后，立刻通过发布下列主题
`$cloud/device/:deviceId/event/announce`
`$cloud/device/:deviceId/channel/:channelId/event/announce`
上报`device`和`device channel`，数据格式**参考`posair.json`**。服务端接收到上报后，根据数据中`device`和`device channel`，新增或者修改相应的`device`和`device channel`。

* 与此同时，客户端会订阅主题
$cloud/device/:device/channel
获取服务端指令，指令格式为`jsonrpc`。


* 当设备有状态发生变化和数据上报时，客户端将通过发布以下主题
`$cloud/device/:deviceId/channel/:channelId/event/state`
`$cloud/device/:deviceId/channel/:channelId/event/data`
发布设备状态变化和数据。


#### 参数说明
* `deviceId` 设备ID,设备唯一标识
* `channelId` 设备功能点，例：posair
