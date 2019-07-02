### 安装软件

是在Fedora和RedHat以及SUSE中基于rpm的软件包管理器，它可以使系统管理人员交互和自动化地更新与管理RPM软件包，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软体包，无须繁琐地一次次下载、安装。

```
//显示已经安装的软件包
yum list installed
//安装软件包
yum install tomcat
//卸载软件包
yum remove tomcat
//列出软件包的依赖
yum deplist tomcat
//装软件的时候，会有中断，让用户选择是否要继续，可以用 -y 来应答所有的 yes
yum -y install tomcat
//显示软件包的描述信息和概要信息
yum info tomcat
//yum update
yum update
//升级某一个软件包
yum update tomcat
//检查可更新的程序
yum check-update
```
