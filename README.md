# PyRE

`PyRE`是一个基于`控制流数据输入`的规则引擎，基于Python3.5实现，并使用Django Rest框架来实现API接口和结果查询。

测试规则校验所使用到的数据集文件在`src/ruleengine/app/raw_signal.py`中，所使用的数据库为`db.sqlite3`，运行程序时，上述测试数据集会执行语句插入数据库中。

## 核心工作流

该引擎会读取每一行数据并和已有规则进行校验，最终自动给出结果。

## 快速开始

首先，下载库到本地

```
git clone https://github.com/rynuk/PyRE.git
```

执行这一命令后，还会将`sqlite DB`的本地副本进行复制，因此无需再进行迁移和验证。

### 注意

建议最好使用`venu`虚拟环境。

设置好环境之后，安装第三方库：

```
pip install -r requirements.txt
```

### 安装

安装并启动一个Django程序非常简单，切换到`manage.py`文件所在路径，执行：

```
python manage.py runserver
```

此时，程序将正常运行在本地服务器[8000端口](http://localhost:8000/)

你可以通过以下地址和账户密码访问管理员页面：

````
http://localhost:8000/admin
username: appuser
password: appuser
````

在这个页面你还可以对规则和数据流进行增删查改。

## 规则引擎数据校验使用方法

### 加载raw_signal.json

API:

`http://localhost:8000/v1/load/`

点击`POST`会向上述接口发送`POST`请求，同时将raw_signal.json文件加载并插入数据库，同时返回每一项违背规则的数据以及原因。

![image](src/images/load.png?raw=true)

### 创建规则

[TODO]创建规则下一版本会使用DSL语言创建，例如：

`ATL1 Datetime Should Be > 2020-05-11 00:00:00`

当前版本只接受类json格式创建：

`<signal value> <value Type> <should/should not be> <greater than/lass than/equal to> <value>`

#### 使用API接口

API：

`http://localhost:8000/v1/rule/`

向上述接口地址发送`GET`请求，会列出当前存在的所有规则。

![image](src/images/rule.png?raw=true)

向上述接口地址发送`POST`请求，会创建新规则：

```
data:
{
	"appliesOn": "ATL1", 
	"valueType": "String", 
	"ruleType": "Should Be", 
	"operator": "=", 
	"value": "HIGH"
}
```

### 校验单一数据

该规则引擎支持使用`POST`请求验证单一数据，会返回该数据违反的规则和原因。

API：

`http://localhost:8000/v1/signal/`

示例：

```
data:
{
	"signal": "ATL2",
	"value": "20.6",
	"value_type": "Integer"
}
```
```
Response:
{
    "violatedRules": [
        "ATL2 Integer Should Be > 50"
    ],
    "isViolated": true,
    "signal": "ATL2",
    "value": "20.6",
    "value_type": "Integer"
}
```

# MORE

更多使用文档和演示以及设计文档和下一版本改进，请查看在线文档[PyRE规则引擎使用及设计文档](https://www.yuque.com/books/share/fabdf0b2-58e1-4e38-9803-ba71825987b9)