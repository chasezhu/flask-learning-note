# Flask

## 第一部分 Flask简介
### 第1章 安装
与其他框架相比，flask是个小型（微型）框架，但是并不可使用的功能比其他框架少，flask通过可拓展实现各种需求。

Flask有个主要依赖：路由、调试和Web服务器网关接口（WSGI）由Werkzeug提供；模板系统由Jinja2提供。

Flask不支持原生数据库访问、web表单验证和用于认证等高级功能。这些功能以可拓展形式实现，然后再与核心包集成。

#### 1.1 使用虚拟环境
这里不做介绍，因为本人的开发过程中没使用过这种场景。

#### 1.2 使用pip安装
大多数Python包都使用pip安装，使用虚拟环境时会自动安装pip。激活虚拟环境后，pip所在的路径会被添加进PATH。

```bash
pip install flask
```

### 第2章 程序的基本结构
#### 2.1 初始化
Flask程序都需要创建一个实例。Web服务器用WSGI协议，把接受自客户端的所有请求都转交给这个对象。（有点难以解释，记住这个固定写法就可以）代码如下：
```python
from flask import Flask
app = Flask(__name__)
# 构造函数有一个必须指定的参数，程序主模块或包的名字，绝大多数情况，__name__ ---> flask用这个参数决定程序的根目录
```

#### 2.2 路由和视图函数
客户端（B/S是Web浏览器）把请求发给web服务器，web服务器再把请求发给flask程序实例。实例需要知道每个请求运行哪些代码，所以保存了一个URL到Python函数的映射关系。**处理URL和函数之间的关系的程序称为路由**，代码如下：
```python
@app.route('/')
def index():
    return '<h1>Hello Flask!</h1>'
```

在浏览器中访问`服务器域名/`后会触发服务器执行index()。这个函数的返回值称为*响应*。index()函数称为视图函数（view function），返回的响应可以是HTML简单字符串，也可以是复杂的表单等。

日常中在网页地址中包含可变部分。例如：www.taobao.com/<*staff*>,staff是地址的一部分。flask支持这种形式的url，只需要在route装饰器中使用特殊的语法就行：
```python
#尖括号中的内容就是动态部分，任何能匹配静态部分的URL都会映射到这个路由上。/user/<int:id>
@app.route('/user/<name>')
def user(name):
    return '<h1>Hello %s!</h1>' % name
```
#### 2.3 启动服务器
```python
# 确报这个脚本由其他脚本引入时，程序假定父级脚本会启动不同的服务器，因此不会执行app.run() 
# debug=Ture ---> 调试模式
if __name__ == '__main__':
    app.run(debug=True)
```

#### 2.4 一个简单的程序
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
    return '<h1>Hello!</h1>'

if __name__ == '__main__':
    app.run(debug=True)
```

#### 2.5 请求-响应循环
##### 2.5.1 程序和请求上下文
flask从客户端收到请求时，要让视图函数能放为一些对象，这样才能处理请求。