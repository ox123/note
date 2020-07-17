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
- F对象语法格式

  ```django
  from django.db.models import F
  filter(id__gte==F('count'))
  ```

- Q对象

  ```django
  from django.db.models import Q
  # 或者语法
  filter(Q(id__gt==2) || Q(name__in='xx'))
  # 并且语法
  filter(Q(id__gt==2) && Q(name__in='xx'))
  # not == ~Q()
  ```

4. 聚合函数

   ```
   from django.db.models import sum
   SystemInfo.objects.aggregate(sum('id'))
   ```




### 分页数据查询

```
from django.core.paginator import Paginator
```

### 页面视图

- 跳转： 

  ```python
  path('admin/', admin.site.urls,name="index"),
  
  from django.shortcuts import render, redirect
  redirect("/index/")
  ```

- 通过名字获取路径

  ```python
  from django.urls import reverse
  path = reverse("index") // 动态路由信息获取
  redirect(path)
  ```

- namespace

  ```python
  path(r'back/', include("backend.urls",namespace="backend")),
  namepsace:name # 获取动态路由
  reverse('backend:index') # 这种方式才能获取动态路由，如果没有设置namespace，则根据name获取
  ```

- HttpRequest对象

- HttpResponse对象