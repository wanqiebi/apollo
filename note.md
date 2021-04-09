### 总体设计
* Config Service提供配置的读取、推送等功能，服务对象是Apollo Client(客户端)。
* Admin Service提供配置的修改、发布等功能，服务对象是Apollo Portal(管理界面)。
* Config Service和Admin Service都是多实例、无状态部署，所以需要将自己注册到Eureka中并保持心跳。
* 在Eureka之上我们架了一层Meta Server用于封装Eureka的服务发现接口。
* 为了简化部署，我们实际上会把Config Service、Eureka和Meta Server三个逻辑角色部署在同一个JVM进程中。
* Client通过域名访问Meta Server获取Config Service服务列表(IP+Port)，而后直接通过IP+Port访问服务，同时在Client侧会做load balance、错误重试。
* Portal通过域名访问Meta Server获取Admin Service服务列表(IP+Port)，而后直接通过IP+Port访问服务，同时在Portal侧会做load balance、错误重试。

### 默认的端口
1. apollo-configservice端口：8080
2. apollo-adminservice端口：8090
3. apollo-portal端口：8070

### Windows本地启动
1. 创建数据库
```
source scripts\sql\apolloconfigdb.sql
source scripts\sql\apolloportaldb.sql
```
2. 配置构建脚本(配置数据源和meta server地址)
```
useUnicode=true&characterEncoding=utf8&serverTimezone=Asia/Shanghai
scripts\build.bat
scripts\build.sh
```
3. 先后启动apollo-configservice、apollo-adminservice、apollo-portal
```
java -jar apollo-configservice-1.9.0-SNAPSHOT.jar
java -jar apollo-adminservice-1.9.0-SNAPSHOT.jar
java -jar apollo-portal-1.9.0-SNAPSHOT.jar
```
4. [访问Portal](http://localhost:8070/)  
账号：apollo/admin
