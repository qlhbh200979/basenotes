# node mongodb

## 安装

[Mongodb For Windows](http://www.cnblogs.com/highsea90/p/4195927.html)
[Window 安装MongoDB](http://www.runoob.com/mongodb/mongodb-window-install.html)

### 安装auth

> 如果针对已经安装window service 的情况，需要先停止window Services并移除该服务，然后重新安装并加上--auth


```shell
# 会默认移除服务并重新安装带有验证的服务
mongod --auth --logpath "G:\MongoDB\Server\3.2\data\log\MongoDb.log" --dbpath "G:\MongoDB\Server\3.2\data\db" --reinstall

```

### 将MongoDB服务器作为Windows服务运行

> 请注意，你必须有管理权限才能运行下面的命令。执行以下命令将MongoDB服务器作为Windows服务运行：

```shell
mongod.exe --bind_ip yourIPadress --logpath "C:\data\dbConf\mongodb.log" --logappend --dbpath "C:\data\db" --port yourPortNumber --serviceName "YourServiceName" --serviceDisplayName "YourServiceName" --install
```

下表为mongodb启动的参数说明：

|  参数                 |  说明                              |
|-----------------------|------------------------------------|
|--bind_ip              |绑定服务IP，若绑定127.0.0.1，则只能本机访问，不指定默认本地所有IP  |
|--logpath              |定MongoDB日志文件，注意是指定文件不是目录|
|--logappend            |使用追加的方式写日志|    
|--dbpath               |指定数据库路径|
|--port                 |指定服务端口号，默认端口27017|
|--serviceName          |指定服务名称|
|--serviceDisplayName   |指定服务名称，有多个mongodb服务时执行。|
|--install              |指定作为一个Windows服务安装。|
|--reinstall            |将当前window服务重新安装     |
|--directoryperdb       |每个数据库一个文件目录       |
|--remove               |配合--serviceName使用删除一个服务|

服务安装成功后，可以使用一下命令启动/停止window服务

```shell
net start mongodb
net stop  mongodb
```

### 常用命令
```shell
show dbs                    显示所有数据库
db.foo.save({})             创建|更新某个数据库
db.foo.insert({})           插入某个数据库
db.foo.find()               显示某个数据库
db.foo.find({"foo":"foo"})  对于当前数据库中的foo集合进行查找，条件是数据中有一个属性叫foo，且foo的值为foo
db.version()                查看当前版本
db.getMongo()               获取链接当前数据库的服务器
db.addUser()                增加数据库用户
show users                  查看用户
show collections            显示当前数据库的集合
db.createCollection("foo")  创建一个名为"foo"的空数据库
use foo                     切换到某个数据库
db.dropDatabase()           删除当前数据库

```

其它指令可以使用：

```shell
help | db.help() | db.yourColl.help() | db.youColl.find().help()
```

## 数据库权限/角色

安装好MongoDB后，在cmd中使用**mongo**进入数据库

### 创建第一个账户

> 刚进去时，mongodb允许你免认证创建第一个账户用户，一旦创建了一个后就必须
> authorized.

```shell
use admin 

db.createUser({
    user:'admin',
    pwd:'admin',
    roles:[
        {
            role:'useAdminAnyDatabase',
            db:'admin'
        }
    ]
})

```

注意：**创建的第一个账户必须是具有管理所有数据的权限的角色账号，否则数据库将需要重新创建，因为无法管理所有数据库，所以无法创建其它数据库及创建其相应的账号**

**角色分类：**

+ 用户使用权限
+ 管理员权限
+ 所有数据库权限集
+ 最高权限
+ 集群权限
+ 备份/恢复权限


----------------------------------------------------

常见用户角色列表：

| 角色            | 描述                                     |
|-----------------|------------------------------------------|
|read             | 指定数据库用户角色                          |
|readWrite        | 指定数据库用户角色                          |
|dbAdmin          | 指定数据库管理员角色，管理表结构、索引等权限不能分配用户权限角色  |
|dbOwner          | 指定数据库管理员角色，当前数据库最高权限         |
|userAdmin        | 指定数据库管理员角色，拥有指定任务用户角色、权限特权      |
|readAnyDatabase  | 所有数据库读权限                    
|readWriteAnyDatabase | 所有数据库读写权限                         |
|userAdminAnyDatabase | 所有数据库userAdmin权限
|dbAdminAnyDatabase   | 所有数据库dbAdmin权限
|root                 | superuser 最高权限                         |


上述主要列举出常见使用的角色，除此之外还有：数据库恢复备份/集群角色


### 授权方式

```shell
use admin
db.auth("admin","12345678") #认证，返回1表示成功

#或
mongo -u admin -p 12345678 --authenticationDatabase admin
```

### 创建指定数据库用户

管理员角色账户授权后，可以进行创建指定db用户

```shell
use mytest
db.createUser({
   user: "test",
   pwd: "12345678",
   roles: [
      { role: "dbOwner", db: "mytest" }
   ]
 }
)
```


## base

> no sql 数据库

+ schema 纯洁的数据库原型
+ model  是经过schema构造来的，除了schema定义的数据库骨架外，还具有数据库行为模型，他相当于管理数据库属性、行为的类
+ entity