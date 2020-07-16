### 创建admin用户名与密码
- pycharm中Tools-》Run manage task
- makemigrations
- migrate
- createsuperuser

### c创建项目
- django-admin startproject mysite
- 运行程序 python manage.py runserver
### 创建app
- python manage.py startapp blog



### 安装
- pip install django
- pip install MySQL

### 常用操作命令
- python manage.py makemigrations
- python manage.py migrate
- python manage.py shell

### 创建管理员账户
- python manage.py createsuperuser

### 数据操作
1. SystemInfo.objects().all()
2. SystemInfo.objects.filter() # 筛选返回n个结果
3. exclude() === not in
-  F对象语法格式
filter()