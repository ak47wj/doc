# 城信图语后台API开发接口规范

[toc]

## 1. 关于本规范

### 1.1 目的

为规范城信图语后台API开发，实现不同开发团队的互相调用，明确API的路由规则、参数名称和消息格式等，特制定本规范。

### 1.2 术语
API：又叫API接口，通常是一些预先定义的函数，目的是提供应用程序与开发人员基于某软件或硬件得以访问一组例程的能力，而又无需访问源码，或理解内部工作机制的细节。这里主要是指Web开发时，将自身的资源开放给其他开发者来调用。对外提供的API 调用使得站点之间相互独立，但内容关联性更强。

REST架构：REST是所有Web应用都应该遵守的架构设计指导原则。REST要求，必须通过统一的接口来对资源执行各种操作。对于每个资源只能执行一组有限的操作。（7个HTTP方法 : GET / POST / PUT / DELETE / PATCH / HEAD / OPTIONS）。

RESTful API：符合REST架构设计的API。

## 2. 协议、域名

API与用户的通信协议，总是使用HTTPS协议，确保交互数据的传输安全。

研发中心发布的API，不同的产品采用不同的域名，比如城信图语的项目通常以fzjc.apps.chinadci.com为域名，采用https://fzjc.apps.chinadci.com/{Application Name}/api作为基础的Url（BaseUrl）。

## 3. API路径规则

采用RESTful架构中，每个路径代表一种资源（resource），所以路径中不能有动词，只能有名词，而且所用的名词代表资源的名称，往往与数据库的表格名对应，通常采用复数形式。
### 3.1 获取资源列表
GET： /api/{resource name}

示例：GET /api/articlemeta    （获取文章基本信息列表）

### 3.2 获取单个资源
GET：/api/{resource name}/{id|uid}

示例：GET /api/articlemeta/{id}    （获取单个文章基本信息）

### 3.3 创建一个资源
POST： /api/{resource name}

示例：POST /api/articlemeta    （新增文章）

### 3.4 更新一个资源
[POST|PUT]： /api/{resource name}/{id|uid}

示例：POST /api/articlemeta/{id}    （编辑文章）

### 3.5 删除一个资源
DELETE： /api/{resource name}/{id|uid}

示例：DELETE /api/articlemeta/{id}    （删除文章）
## 4. API参数规则
### 4.1 访问请求令牌

对于一些访问API需要授权用户访问令牌的，可以在请求头中设置Authorization的内容为访问令牌，也可以在Swagger中通过设置authorization的值为访问令牌，即能正常访问API返回结果。

### 4.2 分页参数

pagesize：分页请求每页记录数，默认每页20条记录

pagenum：分页请求页码

### 4.3 多值参数
同一个参数如果要传多个值，各值中间要以英文逗号(,)隔开。
## 5. API返回消息
### 5.1 消息类型
API返回消息的格式统一为UTF-8字符集的JSON格式。
### 5.2 消息格式

API服务返回内容：
```javascript
{
    "code": 0,
    "message": "",
    "pageNum": 2,
    "pageSize": 30,
    "count": 136,
    "data": [],
    "metadata": [],
    "summary": [],
    "style": {}
}
```

code：必须，0表示正常返回，> 1 表示发生错误。

message : 通常表示错误信息，正常为空。

pageNum: 表示分页查询结果第几页，查询结果从第一页开始。

pageSize：表示分页查询结果每页多少条记录，默认一页20条记录。

count：表示查询结果记录总数

data：存放列表数据的数组

metadata：当前列表数据的元数据信息，为数组类型，元数据的格式以下会详细解释。

summary：列表数据的分类汇总信息，比如全文检索查询结果的分类汇总。

style：列表数据的样式。
#### 5.2.1 元数据格式

元数据项的内容：

```javascript
{
    "displayname": "土地使用信息",
    "fields": [
        {
            "fieldkey": "nydmj",
            "fieldname": "农用地面积",
            "unit": "公顷"
        },
        {
            "fieldkey": "jsydmj",
            "fieldname": "建设用地面积",
            "unit": "公顷"
        }
    ],
    "dimension": [
        "nydmj",
        "jsydmj"
    ]
}
```

displayname：数据名。

fields：数据的字段，内容为数组。

fieldKey：字段关键字。

fieldname：字段名。

unit：字段值的单位，为可选项。

dimension：维度字段，字符串数组，一般用于描述哪些字段参与图表绘制。

### 5.3 图表接口格式

#### 5.3.1 接口方法
接口方法支持GET或POST

![image-20210709165907361](graphmethods.png)

#### 5.3.2 接口参数
接口参数必须提供dimension参数，参数值支持多值形式，中间以英文逗号(,)隔开，比如：...&dimension=name,value,unit，该参数用于前端指定返回结果JSON的哪些Key的值需要参与图表要素的表达。

#### 5.3.3 返回数据标准一

```javascript
{
    "code": 0,
    "message": "",
    "count": 1,
    "metadata": [
        {
            "fields": [
                {
                    "fieldkey": "key1",
                    "fieldname": "其它属性1"
                },
                {
                    "fieldkey": "key2",
                    "fieldname": "其它属性2"
                },
                {
                    "fieldkey": "name",
                    "fieldname": "名称"
                },
                {
                    "fieldkey": "value",
                    "fieldname": "数值"
                },
                {
                    "fieldkey": "unit",
                    "fieldname": "单位"
                }
            ],
            "dimension": [
                "name",
                "value",
                "unit"
            ]
        }
    ],
    "data": [
        {
            "key1": "......",
            "key2": "......",
            "name": "农用地",
            "value": 87096.48,
            "unit": "公顷"
        },
        {
            "key1": "......",
            "key2": "......",
            "name": "建设用地",
            "value": 100483.6,
            "unit": "公顷"
        },
        {
            "key1": "......",
            "key2": "......",
            "name": "未利用地",
            "value": 12166.72,
            "unit": "公顷"
        }
    ]
}
```

注意：dimension的Key名需要和图表样式中定义的一致，否则不能出来图表。为了使图表样式尽量通用化，dimension的Key名尽量通用化，比如通常采用为name(图表项名), value(图表项值), unit(图表项单位) ......

后台开发人员想要获知dimension的Key名，可以进入黑猫运维的图表配置管理，找到对应的图表，打开查看静态Json项内容可以获知。

![graphsampledata](graphsampledata.png)

图表返回数据标准一格式的数据适用于表达柱状图、条形图、折线图、饼图、表格类等通用型图表。

#### 5.3.4 返回数据标准二

```javascript
{
    "code": 0,
    "message": "",
    "count": 5,
    "metadata": [
        {
            "fields": [
                {
                    "fieldkey": "key1",
                    "fieldname": "其它属性1"
                },
                {
                    "fieldkey": "key2",
                    "fieldname": "其它属性2"
                }
            ],
            "dimension": [
                "name",
                "value"
            ]
        }
    ],
    "data": [
        {
            "key1": "......",
            "key2": "......",
            "children": [
                {
                    "key": "Dimension1 Key",
                    "value": 123.45,
                    "name": "Dimension1 Name",
                    "unit": "Dimension1 Unit"
                },
                {
                    "key": "Dimension2 Key",
                    "value": 234.56,
                    "name": "Dimension2 Name",
                    "unit": "Dimension2 Unit"
                },
                {
                    "key": "Dimension3 Key",
                    "value": 345.67,
                    "name": "Dimension3 Name",
                    "unit": "Dimension3 Unit"
                }
            ]
        }
    ]
}
```
图表返回数据标准二格式的数据适用于表达文本类、指标类的较复杂的图表，具体情况可以进入黑猫运维的图表配置管理，找到对应的图表，打开查看静态Json项内容可以获知。

## 6. HTTP错误状态码

401：未授权访问

301：重定向，前端无需添加到back列表

500：服务器内部

## 7. API请求的消息格式
本规范允许采用两种请求格式，一是标准的JSON格式，contenttype为application/json，另一种是Form提交数据，ContentType为application/x-www-form-urlencoded或者multipart/form-data。
### 7.1 JSON数据格式

```javascript
{
    "PropertyName1": "ProperyValue1",
    "PropertyName": "ProperyValue2",
    ......
}
```

### 7.2 Form表单数据
PropertyName1= ProperyValue1& PropertyName2= ProperyValue2

注：Value需要经过UrlEncode。
