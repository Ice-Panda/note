# odoo development essential
## Index
- [Set up]()
- [Module]()
- [Inheritance]() 
- [Data]() 
- [Modle]()
- [View]()
- [ORM]()
- [QWeb]()
- [External Api]()
- [Deployment]()


## Set up
### install
#### instanll on debain/ubuntu   
1. $ git clone odoo_source
2. ./odoo.py setup_deps
3. ./odoo.py setup_pg

#### init database
1. createuser --superuser  
2. createdb v9dev  
3. ./odoo.py -d v9dev `[--without-demo-data=all]` 默认odoo会将测试数据加载，这个参数可以禁用
#### create database from  template
1. createdb --template=v9dev v9test
2. psql -l 
3. dropdb v9test

> odoo 不同版本数据库是不兼容的  

### configuration files/options
默认情况下，odoo会将配置文件保存在用户目录下~/.openerp-serverrc  
--save 参数可以将当前的参数保存到~/.openerp-serverrc中  
`./odoo.py --save --stop-after-init`    
`--stop--after-init` 在初始化完成后停止，通常用来测试检查module安装升级。  
还可以指定配置文件`--conf=<filepath>`
### change xmlrpc port 
可以修改`--xmlrpc-server=<port>`来修改默认的端口号，这样子可以在一台机器上运行多个实例。  
./odoo.py --xmlrpc-server=8090  
./odoo.py --xmlrpc-server=8091   

### logging
`--log-level`可以设置日志级别，默认odoo把日志打印到console中，`--logfile=<filepath>`可以将日志写到文件中。  

**--debug**当系统发生异常时，odoo会启动pdb。  

## odoo App
module和app是不同的，module是一个包涵__init__.py，还有__openerp.py的文件夹，app类似于HR等一些功能组织的中心，其它modules可以修改添加功能。  
### 修改和扩展modules
关于继承：通常不应该直接在原始模块上作修改，这样会破坏模块的结构，而是重新写一个模块。
### 创建新的module
upgrade ./odoo.py -d v9dev -u module_name `－u`可以直接更新moule,但是需要天际－d选项   
### 添加菜单



 


