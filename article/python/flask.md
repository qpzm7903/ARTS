#flask
[官方文档](https://dormousehole.readthedocs.io/en/latest/)

名词解释



`WSGI`是什么

`url_for` 是根据什么来找到对应的处理？

>  [`url_for()`](https://dormousehole.readthedocs.io/en/latest/api.html#flask.url_for) 根据登录视图的名称生成相应的 URL 
>
> 根据视图名称和发生成 URL 。视图相关联的名称亦称为 *端点* ，缺省情况下，端点名称与视图函数名称相同。



`view`视图是什么

蓝图下的每一个方法就是一个视图，视图可以返回模板，可以重定向到其他的视图，可以返回文本



`bluprint` / 蓝图是什么 

> 是一种组织一组相关视图及其他代码的方式，有点像spring mvc里面的controller，聚合了一些操作



`flash`函数

>用于储存在渲染模块时可以调用的信息。



`g`变量是什么

>
>
>A namespace object that can store data during an [application context](https://dormousehole.readthedocs.io/en/latest/appcontext.html). This is an instance of [`Flask.app_ctx_globals_class`](https://dormousehole.readthedocs.io/en/latest/api.html#flask.Flask.app_ctx_globals_class), which defaults to [`ctx._AppCtxGlobals`](https://dormousehole.readthedocs.io/en/latest/api.html#flask.ctx._AppCtxGlobals).



[`abort()`](https://dormousehole.readthedocs.io/en/latest/api.html#flask.abort) 作用是什么

>会引发一个特殊的异常，返回一个 HTTP 状态码。它有一个可选参数， 用于显示出错信息，若不使用该参数则返回缺省出错信息



## 快速入门

运行

配置

```powershell
> $env:FLASK_APP = "flaskr"
> $env:FLASK_ENV = "development"
> flask run
```



```bash
$ export FLASK_APP=flaskr
$ export FLASK_ENV=development
$ flask run
```

在`pycharm`中

![image-20210917212359550](C:\Users\qpzm7903\AppData\Roaming\Typora\typora-user-images\image-20210917212359550.png)

简单使用

部署



## 教程



蓝图、视图



布局



安装



部署





继续



如果要继续开发 Blog项目，建议尝试以下内容：

- 点击帖子标题，显示帖子详细页面。 -- DONE
- 喜欢或者不喜欢一个帖子。 -- DONE
- 评论。-- DONE
- 对评论进行评论。
- 标记。点击标记显示所有带有该标记的帖子。-- DONE
- 一个可以过滤标题的搜索框。 -- DONE
- 分页显示，每页只显示五个帖子。-- DONE
- 帖子可以上传图片。
- 帖子支持用 Markdown 撰写。
- 一个新帖子的 RSS 源。
- 异常事务处理
- 事务嵌套处理





## 扩展





## 存疑

执行 `pytest`提示

>
>
>PS D:\workspace\blog_demo> pytest
>ImportError while loading conftest 'D:\workspace\blog_demo\tests\conftest.py'.
>tests\conftest.py:5: in <module>
>    from myblog import create_app
>E   ModuleNotFoundError: No module named 'myblog'



此时的结构树如下；



### flask如何支持restful

