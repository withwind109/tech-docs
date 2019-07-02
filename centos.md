#### yum安装软件

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

### systemctl管理自定义系统服务

```
//使用 PageUp 或 PageDown 翻页，查看完毕后按 q 退出。
//查看系统所有安装的服务项
systemctl list-unit-files --type=service
//查看系统所有运行的服务项
systemctl list-units --type=service
//查看系统所有开机自启动的服务项
systemctl list-unit-files --type=service | grep enabled
查看指定服务项状态
systemctl status <服务项名称>

//启动服务
systemctl start <服务项名称>
//停止服务
systemctl stop <服务项名称>
//重启服务
systemctl restart <服务项名称>
//重新读取配置文件，如果该服务不能重启，但又必须使用新的配置，这条命令会很有用。
systemctl reload <服务项名称>
//使服务开机自启动
systemctl enable <服务项名称>
//使服务不要开机自启动
systemctl disable <服务项名称>
//禁用服务，这可以防止服务被其他服务间接启动，也无法通过 start 或 restart 命令来启动服务。
systemctl mask <服务项名称>
//启用服务，仅针对于已禁用的服务。
systemctl unmask <服务项名称>
//重新读取所有服务项，修改、添加、删除服务项之后需要执行以下命令。
systemctl daemon-reload
```
https://qizhanming.com/blog/2018/08/06/how-to-install-nginx-on-centos-7
