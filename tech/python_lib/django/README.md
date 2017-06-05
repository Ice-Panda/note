# Django源码解析

django runserver 启动流程

- 执行 `./manage.py runserver` --> 调用`django.core.management`中`execute_from_command_line(sys.argv)`
    > django-admin 也是调用此方法
- 新建 `utility = ManagementUtility(argv)`
- 调用 `utility.execute()`
    - 取出 subcommand :即传递的命令参数
    - 添加默认参数
    - 检查 settings 中是否存在 INSTALLED_APPS
        > settings 使用 LazySettings 
        - 否:使用` conf.global_settings `作为默认设置
        > 默认 django 使用 `conf.__init__中`

django manage.py 命令参数在`django.core.management.commands`下.

如果需要自定义命令可以在该文件夹下,**命令名称与文件名相同**

# **init**.py

# apps

# contrib

# dispatch

# middleware

# templatetags

# utils

# **main**.py

# bin

# core

# forms

# shortcuts.py

# test

# views

# **pycache**

# conf

# db

# http

# template

# urls
