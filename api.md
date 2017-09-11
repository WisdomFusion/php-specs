# API 及接口文档规范

## 0. Version

v1.0

## 1. 简介

API 接口在 Web 开发中应用广泛，本文旨在为合作应用访问或 APP 接口访问等开放服务提供统一的 HTTP 接口调用与交互规范，尽可能避免开发和项目间沟通出现问题，节省开发成本及时间。

## 2. 文档要求

* 接口文档均采用 Markdown 格式，便于接口文档也加入版本控制系统中，扩展名统一使用 `.md`。
* 文档存放在项目根目录中 `/docs/` 目录下 `/docs/api.md`。
* API 文档有必要包含（不限于）项目简介、开发人员列表、CHANGELOG、名词解释、通信约定、请数据包格式等内容。

### 2.1 名词解释

解释 API 接品文档中规定的通用名词，如 API_KEY, API_SECRET 等。

### 2.2 通信协议

API 与客户端通信协议，总是使用 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 协议。

### 2.3 通信约定

约定 HTTP 请求方法（具体接口需要准确指定请求方法，下文有说明），返回格式的说明，一般接口用 JSON 格式数据包即可，更通用的接口需要同时提供 XML 格式数据包。

**XML**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<error>ERROR_CODE</error>
```

**JSON**

```json
{
  "error":"ERROR_CODE"
}
```

最重要的是，需要约定**加密方式**，所有的 HTTP 通信都需要加密，本节必需明确加密方式及使用方法。

### 2.4 请求数据包格式规范

#### 2.4.1 参数约定

所有参数均用小写字母，多个单词用 `_` 连接，如 `access_token`，单词尽可能简单、准确。

#### 2.4.2 XML 输出格式

* 文档编码格式 UTF-8
* 接口的返回数据中，数组对应的 xml 节点包含 `list="true"` 属性，其子节点的标签名跟对应的数据有联系，并且同个数组内的同级节点的标签名一致。例如表示问题标题列表对应的 xml 输出可能为：

  ```xml
  <questionList list="true">
    <title><![CDATA[为用户解决什么问题？]]></title>
    <title><![CDATA[从哪些方面着手去解决？]]></title>
  </questionList>
  ```

* 接口的返回数据中，对象类型和普通数据类型数据（`string`, `int`, `double`, `bool`）对应的 xml 节点不包含 list 属性或者 list 属性值为 false，节点标签名具有实际意义，与数据所描述的信息相符。例如，表示问题的数据对应的xml输出为：

  ```xml
  <question list="false">
    <title><![CDATA[为用户解决哪些问题？]]></title>
    <url><![CDATA[https://github.com/]]</url>
    <content><![CDATA[从哪些方向着手去解决？]]</content>
  </question>
  ```

#### 2.4.3 JSON 输出格式

API调用时如果传递 `format` 参数为 `json`（大小写不敏感），则正常响应包符合如下规范的 `json` 字符串：

* HTTP 响应头中的 `Content-Type` 指定为 `application/json, charset=utf-8`
* 字符串编码格式是 `UTF-8`
* PHP 数组的标准 JSON 字符串
* 标准 JSON 格式请参看官网：[JSON (JavaScript Object Notation)](http://www.json.org/)

一个复杂点的 JSON 输出示例：

```json
{
  "links": {
    "self": "http://example.com/articles",
    "next": "http://example.com/articles?page[offset]=2",
    "last": "http://example.com/articles?page[offset]=10"
  },
  "data": [{
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "JSON API paints my bikeshed!"
    },
    "relationships": {
      "author": {
        "links": {
          "self": "http://example.com/articles/1/relationships/author",
          "related": "http://example.com/articles/1/author"
        },
        "data": { "type": "people", "id": "9" }
      },
      "comments": {
        "links": {
          "self": "http://example.com/articles/1/relationships/comments",
          "related": "http://example.com/articles/1/comments"
        },
        "data": [
          { "type": "comments", "id": "5" },
          { "type": "comments", "id": "12" }
        ]
      }
    },
    "links": {
      "self": "http://example.com/articles/1"
    }
  }]
}
```

#### 2.4.4 错误响应输出格式

错误响应输出内容符合以下规范：

* 返回内容由 `error_code`, `error_msg`, `request_args` 这3个属性组成，分别用于描述错误码，错误信息，以及调用 API 时所传递的所有参数的信息
* `request_args` 属性是一个数组，由若干包含 `key` 和 `value` 属性的对象组成

### 2.5 RESTful API 路由设计规则

#### 2.5.1 域名

    https://example.org**/api/**

#### 2.5.2 版本

    https://api.example.com**/v1/**

另一种做法是，将版本号放在HTTP头信息中，但不如放入URL方便和直观。

#### 2.5.3 路由

在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用复数，多个单词可用 `-` 联接（类似域名规则）。

举例来说，有一个API提供动物园（zoo）的信息，还包括各种动物和雇员的信息，则它的路径应该设计成下面这样。

    https://api.example.com/v1/zoos
    https://api.example.com/v1/animals
    https://api.example.com/v1/employees

#### 2.5.4 HTTP 动词

对于资源的具体操作类型，由HTTP动词表示。常用的HTTP动词有下面五个（括号里是对应的SQL命令）。

    GET（SELECT）：从服务器取出资源（一项或多项）。
    POST（CREATE）：在服务器新建一个资源。
    PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
    PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
    DELETE（DELETE）：从服务器删除资源。

还有两个不常用的HTTP动词。

    HEAD：获取资源的元数据。
    OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。

下面是一些例子。

    GET /zoos：列出所有动物园
    POST /zoos：新建一个动物园
    GET /zoos/ID：获取某个指定动物园的信息
    PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
    PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
    DELETE /zoos/ID：删除某个动物园
    GET /zoos/ID/animals：列出某个指定动物园的所有动物
    DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物


### 2.6 状态码定义

W3 为 HTTP 请求定义了超过 40 个标准状态码，但实际 API 在设计时，这么多状态码过于繁重，个人建议使用如下三类状态码即可：

* 200 成功状态
* 400 客户端错误状态
* 500 服务端错误状态

可以根据实际业务添加必要的其他状态码，如 422 表单验证失败，401 认证失败等等，最后不要超过8个。这些状态码的含义参看 [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。

**200 - OK**

OK了，正常执行，以下示例为 PHP 框架 Laravel 返回带有分页数据：

```javascript
{
    "status": 200,
    "message": "success",
    "data": {
        "current_page": 1,
        "from": 1,
        "last_page": 1,
        "next_page_url": null,
        "path": "http://apiurl/api/foobar",
        "per_page": 20,
        "prev_page_url": null,
        "to": 2,
        "total": 2,
        "data": [
            {
                // ...
            },
            {
                // ...
            },
            // ...
        ]
    }
}
```

**400 - Bad Request (client-side error)**

服务器无法理解请求的格式，客户端不应当尝试再次使用相同的内容发起请求。一般性错误都用该错误状态，返回数据的 `message` 字段描述客户端请求的错误所在。

```javascript
{
    "status": 400,
    "message": "该用户已产生数据，无法执行删除操作！",
    "data": {}
}
```

**401 - Unauthorized**

请求未授权。如果请求 header 没有 Authorization 字段，服务器端应该在返回 401 Unauthorized 的同时在 header 中用 WWW-Authorization 字段指出授权方式，以便客户端带上登录信息重新发起请求。如果 Authorization 字段已经存在，则表明登录信息不正确（含已过期）。

```javascript
{
    "status": 401,
    "message": "Unauthorized",
    "data": {}
}
```

**422 - Form Validation Failed**

表单验证失败。

```javascript
{
    "status": 400,
    "message": "Form validation failed",
    "data": {
        "email": [
            "邮箱地址不合法。"
        ],
        "password": [
            "密码不能为空。"
        ]
    }
}
```

**500 - Internal Server Error (server-side error)**

服务器内部错误。服务器遇到异常情况，无法完成该请求。

```javascript
{
    "status": 500,
    "message": "服务器在删除该数据时出错，请重试或联系网站管理员。",
    "data": {}
}
```

### 2.7 接口细则

直接以一个接口示例来说明：

### 获取用户信息

* **功能：**获取用户信息

* **请求方法：**`GET`

* **请求 URL：**`/api/user`

* **请求参数：**

| 参数名 | 必选 | 类型   | 描述                        |
|--------|------|--------|-----------------------------|
| userid | Y    | int    | 用户 ID，不可为空           |
| format | Y    | string | 返回数据的格式，json 或 xml |

* **返回示例：**

XML 格式的响应信息如下：
  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<success>true</success>
<user>
  <account>test@test.com</account>
  <version><![CDATA[用户版]]></version>
  <expired>2015-06-06</expired>
  <space>
    <total>2</total>
    <remain>1.9</remain>
    <used>0.1</used>
  </space>
  <traffic>
    <total>5</total>
    <remain>4.8</remain>
    <used>0.2</used>
  </traffic>
</user>
```

JSON 格式的响应信息如下：

```json
{
  "success":true,
  "user":{
    "account":"test@test.com",
    "version":"试用版",
    "expired":"2015-06-06",
    "space":{
      "total":2,
      "remain":1.9,
      "used":0.1
    },
    "traffic":{
      "total":5,
      "remain":4.8,
      "used":0.2
    }
  }
}
```

* **响应参数：**

返回数据 user

| 字段名       | 描述         |
|--------------|--------------|
| accout       | 用户账户     |
| version      | 版本信息     |
| expired_time | 到期时间     |
| space        | 用户空间信息 |
| traffic      | 用户流量信息 |

space 包含字段列表：

| 字段名 | 描述                     |
|--------|--------------------------|
| total  | 用户空间总量，单位 G     |
| remain | 用户空间剩余量，单位 G   |
| used   | 用户空间使用总量，单位 G |

traffic 包含字段列表：

| 字段名 | 描述                     |
|--------|--------------------------|
| total  | 用户空间总量，单位 G     |
| remain | 用户空间剩余量，单位 G   |
| used   | 用户空间使用总量，单位 G |

## 3. 接口文档模板

[接口文档模板](api-doc-template.md)

