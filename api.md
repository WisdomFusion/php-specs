# 接口及接口文档规范

## 简介

API 接口在 Web 开发中应用广泛，本文旨在为合作应用访问或 APP 接口访问等开放服务提供统一的HTTP接口调用与交互规范，尽可能避免开发和项目间沟通出现问题，节省开发成本及时间。

## 文档结构

* 接口文档均采用 Markdown 格式，扩展名统一使用 `.md`。
* API 文档有必要包含（不限于）项目简介、开发人员列表、CHANGELOG、名词解释、请数据包格式等内容。

### 项目简介

简要说明项目的来龙去脉，并对接口文档适用对象作简要说明。

| **系统名称**     | project name |
| **项目负责人**   | PM           |
| **接口作者**     | Developers   |
| **文档提交日期** | modified     |

### 开发人员列表

开发人员列表

### CHANGELOG

| 版本号 | 修改内容简介 |   修改日期 | 修改人       |
|--------|--------------|------------|--------------|
|    1.0 | 说明         | 2016-04-01 | WisdomFusion |
|        |              |            |              |
|        |              |            |              |
|        |              |            |              |
|        |              |            |              |
|        |              |            |              |
|        |              |            |              |
|        |              |            |              |
|        |              |            |              |
|        |              |            |              |
|        |              |            |              |

### 名词解释

解释 API 接品文档中规定的通用名词，如 API_KEY, API_SECRET 等。

### 请求数据包格式规范

**参数约定**

所有参数均用小写字母，多个单词用 `_` 连接，如 `access_token`，单词尽可能简单、准确。

**JSON 输出格式**

API调用时如果传递 format 参数为 json（大小写不敏感），则正常响应包符合如下规范的 json 字符串：

* HTTP 响应头中的 Content-Type 指定为 application/json, charset=utf-8
* 字符串编码格式是 UTF-8

**XML 输出格式**

* 文档编码格式 UTF-8
* 接口的返回数据中，数组对应的 xml 节点包含 `list="true"` 属性，其子节点的标签名跟对应的数据有联系，并且同个数组内的同级节点的标签名一致。例如表示问题标题列表对应的 xml 输出可能为：

```xml
<questionList list="true">
    <title><![CDATA[北京一共有几个区？]]></title>
    <title><![CDATA[百度大厦的地址是什么？]]></title>
</questionList>
```
* 接口的返回数据中，对象类型和普通数据类型数据（string, int, double, bool）对应的 xml 节点不包含 list 属性或者 list 属性值为 false，节点标签名具有实际意义，与数据所描述的信息相符。例如，表示问题的数据对应的 xml 输出为：

```xml
<question list="false">
    <title><![CDATA[百度大厦的地址是什么？]]></title>
    <url><![CDATA[http://zhidao.baidu.com/question/133295964.html]]</url>
    <content><![CDATA[如题，百度大厦地址在]]</ content >
</question>
```

**错误响应输出格式**

错误响应输出内容符合以下规范：

* 返回内容由error_code, error_msg, request_args 这3个属性组成，分别用于描述错误码，错误信息，以及调用 API 时所传递的所有参数的信息
* request_args 属性是一个数组，由若干包含 key 和 value 属性的对象组成

## 错误码定义

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
|            |                                 |                          |
|            |                                 |                          |
|            |                                 |                          |
|            |                                 |                          |
|            |                                 |                          |
|            |                                 |                          |
|            |                                 |                          |
|            |                                 |                          |
|            |                                 |                          |

## 接口细则

API 接口需要包含的内容：

* **功能**：功能说明
* **请求方法**：POST|GET
* **请求URL**：REST URL
* **请求参数**：各参数说明，包括参数名、类型和描述
* **响应参数**：各字段说明，包括标签名和描述
* **示例**：给出一个请求和响应的示例

## 模板

[接口文档模板](api-doc-template.md)

