## **单机mongo启用认证**
```
security:
		authorization: "enabled"
```
启动
mongod -f /etc/mongod.conf

##  **创建超级管理员**
```
use admin
db.createUser({"user":"admin","pwd":"WAer8R59G6","roles":["root"]});
```
## **首先要use admin**
```
db.createUser({user:"jason",pwd:"leanwork2018",roles:[{ role: "readAnyDatabase", db: "admin" }]});
db.createUser({user:"bwrakeback",pwd:"aLHGzwJP0",roles:["readWriteAnyDatabase"]});
退出开启认证模式，重启，再建立普通用户
use admin
db.auth('admin',' RhFUUWI6vQ0k');
```
##  **创建普通用户**
```
use mq-beta
db.createUser({user:"mq-beta",pwd:"abc123",roles:[{role:"readWrite",db:"mq-beta"}]});
建表
db.createCollection("test1")
删表
db.getCollection('test1').remove({})
删除用户
db.system.users.find()
```
### **删除一条记录**
```
db.t_pub_user.find({ "_id" : "dd2ba7f0-e7f9-453e-b960-ebdc83b251d3"})
{ "_id" : "dd2ba7f0-e7f9-453e-b960-ebdc83b251d3", "_class" : "com.lwork.bw.transfer.pubauth.domain.to.User", "tenantId" : "T000004", "productId" : "BW", "username" : "Tom", "phone" : "", "isPhoneVerified" : false, "email" : "tom@lwork.com", "isEmailVerified" : true, "createTime" : ISODate("1970-01-17T08:01:47.058Z"), "lastLoginTime" : ISODate("1970-01-17T20:33:03.058Z"), "isAlive" : false, "isSCUser" : false, "isPendingMigrateUser" : true }
_id这个是key，后面的""是值。
db.t_customer_profiles.deleteOne({"_id" : "3fd38e17-6a78-42d8-983a-545620295c07"})

db.users.find({name:/hurry/});
db.t_product_deploy.find({url:/3308/});
```
#### **创建一个监控账户，库admin**
```
db.createUser({user:"zabbixmonitor",pwd:"leanwork2018",roles:["clusterMonitor"]});
```
### **因为mongodb默认是从主节点读写数据的，副本节点上不允许读，需要设置副本节点可以读**
`db.getMongo().setSlaveOk();`

**副本集状态**
-----
rs.status();

**冻结Secondary节点**
-------------

如果需要对Primary做一下维护，但是不希望在维护的这段时间内将其它Secondary节点选举为Primary节点，可以在每次Secondary节点上执行freeze命令，强制使它们始终处于Secondary节点状态。

myapp:SECONDARY> rs.freeze(100)
注：只能在Secondary节点上执行

复制代码
myapp:PRIMARY> rs.freeze(100)
{
	"ok" : 0,
	"errmsg" : "cannot freeze node when primary or running for election. state: Primary",
	"code" : 95,
	"codeName" : "NotSecondary"
}
复制代码
如果要解冻Secondary节点，只需执行

myapp:SECONDARY> rs.freeze()

添加和删除节点
-------
				   rs.add("ip+端口号")添加节点
这时使用命令rs.remove("IP+端口")即可移除该节点
