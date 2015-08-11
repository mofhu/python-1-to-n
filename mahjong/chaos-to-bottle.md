# Bottle 入门手册

缘起:

SAE 架构已有, 如何把向听数计算器部署到网站上?

尝试使用最简单的 python 框架之一 `Bottle`.

结果相当顺利的成功了(大约两个 30 min就完成了可用原型), 展现出了 python 快速构建原型的强大能力.

###[MVP repo](https://github.com/organization-lab/gbmj-sae/tree/bdb901bfb29ab3263f028f2c91987a6585d73ce0)

### 生成网页内容

from [Bottle tutorial](http://bottlepy.org/docs/0.12/tutorial.html):

```python
from bottle import route, run

@route('/hello')
def hello():
    return "Hello World!"

run(host='localhost', port=8080, debug=True)
```

### 接受用户输入

容易想到用一个文本框和按钮接受输入(也是天凤牌理的输入方式)

google (bottle python input) -> [stackoverflow](http://stackoverflow.com/questions/17257810/raw-input-analog-in-bottle) -> [Bottle request data](http://bottlepy.org/docs/dev/tutorial.html#request-data)

Bottle 可以通过 [HTML 表格](http://bottlepy.org/docs/dev/tutorial.html#html-form-handling)接受输入

模仿范例即得到自己用的版本:

```python
@app.route('/hand')
def hand():
    return '''
        <form action="/handcal" method="post">
            Hand: <input name="hand" type="text" />
            <input value="Calculate" type="submit" />
        </form>
    '''
```

### 处理与输出

接受输入并赋值后, 处理起来与本地运行完全相同. (即把平时通过 input 获得输入改为通过 HTTP GET/POST 方法获得输入)

输出方面, 由于输出多行信息(且最好还能适当优化 HTML 格式), 使用了 Bottle 自带的简单模板 [SimpleTemplate Engine](http://bottlepy.org/docs/0.12/stpl.html)

HTML 前端完全不懂(前面表格也因此出了一些小问题), 如果要做更复杂的展示, 可能还需要一些尝试. 使用了一个在线测试器提高调试模板的效率.

---

Reference

- [Bottle tutorial](http://bottlepy.org/docs/0.12/tutorial.html)
- [bottle-simple-todo](https://bitbucket.org/ZoomQuiet/bottle-simple-todo/wiki/GudierFresher)
- [向听数计算器 GitHub repo - commit of MVP](https://github.com/organization-lab/gbmj-sae/tree/bdb901bfb29ab3263f028f2c91987a6585d73ce0)
- [HTML 在线测试器](http://www.w3school.com.cn/tiy/t.asp?f=html_list_unordered)