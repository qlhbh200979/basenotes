## DOS命令

<https://blog.henix.info/blog/windows-cmdbasic/_.html>

### 常用命令

+ cd /d [drive3]:[path]    *允许切换盘符*
+ cd [path]         *只允许在当前盘符下切换目录*
+ if [not]exist %path% (echo '存在路径')  
+ set %path%  *读取环境变量*
+ set path=%path%;d:\qq;	*设置临时环境变量*



### 配置环境变量与重启

> 一个程序启动时，环境变量被复制到该程序所在的环境中，在该程序执行过程中不会被除该程序以外的其他程序所改变。

修改环境变量之后
+ 如果受影响的是应用程序，那么只要简单地重新启动此应用程序，环境变量的修改就会反映到该程序中，而不必重新启动计算机；
+ 如果受影响的是系统服务，就必须重新启动才能将环境变量的修改反映到系统服务中（因为没有办法在不重启计算机的情况下重新启动系统服务管理器）