title: Django 删除表遇到的一些错误
date: 2017-10-18 11:36:13
categories:
keywords: django, 删除表
tags: django, 删除表
---



python manage.py sqlmigrate DouYin 0001  


逗比的 Django, 从数据库删除以后，就无法再次创建数据库成功。折腾了好久，终于摸索出一条路。 删除你不需要的代码和 服务器端的 数据库，然后本地依旧执行一次

	python manage.py makemigrations         

	python manage.py migrate      

，然后使用这个命令：
	python manage.py sqlmigrate 【你的 Modle 名称】 0001 

就能看到一个 Sql 语句，把这个语句在服务器端执行一下就可以了，等同于在服务器端创建了新的数据表，然后使用 Django 的表中的数据就正常了，可以正常删除添加操作。
如果遇到  新加了字段总是报错，可以给新加的这个字段添加 默认值，然后执行就 Ok 了。
![](https://raw.githubusercontent.com/gdky005/PictureResource/master/django-error/django-delete_error-1.png)

### 自动增长和主键只有一个生效了

![](https://raw.githubusercontent.com/gdky005/PictureResource/master/django-error/django-delete_error-2.png)
上面的这种方法不行的哦！
具体的解决办法是：
![](https://raw.githubusercontent.com/gdky005/PictureResource/master/django-error/django-delete_error-3.png)

### 删除以后就无法再次创建成功，what?

	python manage.py makemigrations app

app 表示你的 Module  

