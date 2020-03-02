---
title: '[problem] flask api + vue 跨域问题'
date: 2019-11-21 16:29:00
categories: 'problem'
tags:
    - 'problem'
---

# 问题

使用flask提供json数据，用vue在前端获取json数据展示。

发现无法获取到该json数据，但其它网站的api的json数据是可以拿到的。

# 解决

后来，查到是由于遇到`跨域请求`的问题，通过设置flask允许其它域访问即可。

域 = 协议名 + 主机名 + 端口号

禁止不同域的访问是为了防止`CSRF`(跨站请求伪造)攻击。

通过`CORS`(跨源资源共享)允许的请求。

添加如下语句开启允许访问的资源：
```python
CORS(app, resources=r'/*')
```

# 示例

`demo.py`

```python
from flask import Flask
from flask_cors import CORS

app = Flask(__name__)
CORS(app, resources=r'/*')

@app.route('/')
def hello_world():
    return {'hello': 'world!'}

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```

`test.html`

```html
<div id="app">
    <h2>
        ----
        {{info}}
    </h2>
</div>

<script src="https://unpkg.com/vue"></script>

<script>
    const app = new Vue({
        el:'#app',
        data:{
            info:''
        },
        created() {
            fetch('http://127.0.0.1:5000')
            .then(response => response.json())
            .then(json => {
                this.info = json
            })
        },
    })
</script>
```

# PS

同理，不仅是flask会出现`跨域请求`的问题，其它语言为了安全性，也是有禁止跨域请求的。
