# API 及接口文档规范

## 1. 简介

API 接口在 Web 开发中应用广泛，本文旨在为合作应用访问或 APP 接口访问等开放服务提供统一的HTTP接口调用与交互规范，尽可能避免开发和项目间沟通出现问题，节省开发成本及时间。

## 2. 文档结构

* 接口文档均采用 Markdown 格式，扩展名统一使用 `.md`。
* API 文档有必要包含（不限于）项目简介、开发人员列表、CHANGELOG、名词解释、通信约定、请数据包格式等内容。

### 2.1 项目简介

简要说明项目的来龙去脉，并对接口文档适用对象作简要说明。

| 项目     | 内容         |
|----------|--------------|
| 系统名称 | project name |
| 负 责 人 | PM name      |
| 提交日期 | 2016-04-01   |

### 2.2 开发人员列表

开发人员列表

| 姓名   | 职位       | Git user.name | Git user.email         |
|--------|------------|---------------|------------------------|
| 胡志飞 | 开发工程师 | WisdomFusion  | WisdomFusion#gmail.com |
| ...    |            |               |                        |

### 2.3 CHANGELOG

| 版本号 | 修改内容简介 |   修改日期 | 修改人       |
|--------|--------------|------------|--------------|
|    1.0 | 说明         | 2016-04-01 | WisdomFusion |
|    ... |              |            |              |

### 2.4 名词解释

解释 API 接品文档中规定的通用名词，如 API_KEY, API_SECRET 等。

### 2.5 通信约定

约定 HTTP 请求方法（具体接口需要准确指定请求方法，下文有说明），返回格式的说明，一般接口用 JSON 格式数据包即可，更通用的接口需要同时提供 XML 格式数据包。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<error>ERROR_CODE</error>
```

```json
{
  "error":"ERROR_CODE"
}
```

最重要的是，需要约定**加密方式**，所有的 HTTP 通信都需要加密，本节必需明确加密方式及使用方法。

### 2.6 请求数据包格式规范

#### 2.6.1 参数约定

所有参数均用小写字母，多个单词用 `_` 连接，如 `access_token`，单词尽可能简单、准确。

#### 2.6.2 XML 输出格式

* 文档编码格式 UTF-8
* 接口的返回数据中，数组对应的 xml 节点包含 `list="true"` 属性，其子节点的标签名跟对应的数据有联系，并且同个数组内的同级节点的标签名一致。例如表示问题标题列表对应的 xml 输出可能为：

```xml
<questionList list="true">
  <title><![CDATA[为用户解决什么问题？]]></title>
  <title><![CDATA[从哪些方面着手去解决？]]></title>
</questionList>
```

* 接口的返回数据中，对象类型和普通数据类型数据（`string`, `int`, `double`, `bool`）对应的xml节点不包含list属性或者list属性值为false，节点标签名具有实际意义，与数据所描述的信息相符。例如，表示问题的数据对应的xml输出为：

```xml
<question list="false">
  <title><![CDATA[为用户解决哪些问题？]]></title>
  <url><![CDATA[https://github.com/]]</url>
  <content><![CDATA[从哪些方向着手去解决？]]</ content >
</question>
```

#### 2.6.3 JSON 输出格式

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

#### 2.6.4 错误响应输出格式

错误响应输出内容符合以下规范：

* 返回内容由 `error_code`, `error_msg`, `request_args` 这3个属性组成，分别用于描述错误码，错误信息，以及调用 API 时所传递的所有参数的信息
* `request_args` 属性是一个数组，由若干包含 `key` 和 `value` 属性的对象组成

## 2.7 错误码定义

| error_code | error_msg                       | 描述                     |
|------------|---------------------------------|--------------------------|
|          0 | Success                         | 成功                     |
|          1 | Unknown error                   | 未知错误                 |
|          2 | Service temporarily unavailable | 服务暂时不可用           |
|          3 | Unsupported API method          | API 接口不被支持         |
|          4 | API request limit reached       | 请求数达到上限           |
|          5 | Unauthorized client IP address  | API 调用端的 IP 未被授权 |
|        100 | Invalid parameter               | 参数无效或数数数量不符   |
|        101 | Invalid API key                 | API key 无效             |
|        ... |                                 |                          |

## 2.8 接口细则

API 接口需要包含的内容：

* **功能**：功能说明
* **请求方法**：POST|GET
* **请求URL**：REST URL
* **请求参数**：各参数说明，包括参数名、是否必选、类型和描述，需要注意的是如果接口需要提供 JSON 和 XML 两种格式的数据包的话，请求参数必须加上 `format`，可选值为 `json` 或 `xml`
* **响应参数**：各字段说明，包括标签名和描述
* **示例**：给出一个请求和响应的示例

如，

**功能**

获取用户信息

**请求方法**

`GET`

**请求URL**

`/api/user`

**请求参数**

| 参数名 | 必选 | 类型   | 描述                        |
|--------|------|--------|-----------------------------|
| userid | Y    | int    | 用户 ID，不可为空           |
| format | Y    | string | 返回数据的格式，json 或 xml |

**响应参数**

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


**示例**：给出一个请求和响应的示例

XML 格式的响应信息如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<user>
  <account>test@test.com</account>
  <version><![CDATA[用户版]]></version>
  <expired>2011-06-06</expired>
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
  "user":{
    "account":"test@test.com",
    "version":"试用版",
    "expired":"2011-06-06",
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

## 3. 模板

[接口文档模板](api-doc-template.md)

