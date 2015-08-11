# SAE 入门手册

## 1. 开发环境与第一个版本
### 1.1 开发环境

参考
相关工具 
http://www.sinacloud.com/doc/sae/python/tools.html

http://chaos2.qiniudn.com/sae/build/html/ch00/try.html

SAE 使用 python 2.7

本地开发 

dev_server.py: 似乎直接运行脚本在我的电脑上有 bug, 发现可以把脚本 copy 到需要部署的目录中使用...
后来发现已经正常安装在 %PATH 中, 但需要 sudo 才可直接运行

部署

saecloud

注意, 一定在应用的本地开发目录，也就是index.wsgi和config.yaml所在的目录进行各种操作

### 1.2 第一个版本
从
http://www.sinacloud.com/doc/sae/python/tutorial.html#hello-world

copy两个文件`index.wsgi`和`config.yaml`的代码

index.wsgi

```
import sae

def app(environ, start_response):
    status = '200 OK'
    response_headers = [('Content-type', 'text/plain')]
    start_response(status, response_headers)
    return ['Hello, world!']

application = sae.create_wsgi_app(app)
```

config.yaml

name 是 SAE 应用的名字

```
name: gbmj
version: 1
```

使用 dev_server.py 本地测试(8080)

使用 `$saecloud deploy` 上传到 sae

### 1.3 Bottle 的第一个版本

install bottle

参考 SAE

修改 index.wsgi

```python
from bottle import Bottle, run

import sae

app = Bottle()

@app.route('/')
def hello():
    return "Hello, world! - Bottle"

application = sae.create_wsgi_app(app)
```

---

refs

- [42分钟乱入 SAE 手册!-)](http://chaos2.qiniudn.com/sae/build/html/index.html)
- [SAE官方文档-python](http://www.sinacloud.com/doc/sae/python/index.html)
- [基于bottle和SAE的微信公众号后台搭建](https://github.com/csufuyi/pythoncamp0/blob/master/source/part3/1.md)