## **Linux**系统实现log日志自动清理 ##


### 背景简介 ###

>在实际项目中由于服务器内存有限，人工制定的定时清理时常会忘记。因此启用linux系统定时任务去执行日志清理功能。[引用文献](http://blog.csdn.net/lmb55/article/details/54837679)


### 创建shell脚本及执行权限 ###

	touch /**/**/auto-del-log.sh    //创建脚本文件
	cd /**/**                       //进入该目录
	chmod +x auto-del-log.sh        //修改执行权限

### 编辑shell脚本 ###
	vi auto-del-log.sh              //编辑执行文件

	#!/bin/sh
    find /**/**/logs/ -mtime +10  -name "*.log.*" -exec rm -rf {} \;  
 
### 添加到自动更新脚本 ###
	crontab -e

添加执行shell脚本

	30 0 * * * /**/**/auto-del-log.sh

crontab样式注释：
	# For details see man 4 crontabs
	
	# Example of job definition:
	# .---------------- minute (0 - 59)
	# |  .------------- hour (0 - 23)
	# |  |  .---------- day of month (1 - 31)
	# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
	# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
	# |  |  |  |  |
	# *  *  *  *  * user-name  command to be executed  


shell脚本说明：

	说明：  
	find：Linux查找命令，用户查找指定条件的文件  
	/home/apache-tomcat-7.0.55-13-990*/webapps/HollyProxyServer2.0/WEB-INF/logs/：需要进行清理的目标目录  
	-mtime：标准语句写法  
	+10 ：数字代表天数  
	-name "*.log.*"：目标文件的类型，带有log的所有文件  
	-exec：固定写法  
	rm -rf：强制删除包括目录在内的文件  
	{} \;：固定写法，一对大括号+空格+\+;  

    eg:
	find /usr/local/tomcat/logall/monitor/ -mtime +7  -name "*.log.*" -exec rm -rf {} \;

### 举例说明 ###
    #!/bin/sh
	
    log_path=/home/tomcat/logs      //日志存放目录
    d=`date +%Y-%m-%d`              //时间获取
    d90=`date -d'7 day ago' +%Y-%m-%d`    //获取删除时间7天前
    cd ${log_path} && cp catalina.out $log_path/cron/catalina.out.$d.log             //catalina.out文件分割，复制文件到 $log_path/cron/ 目录下生成新的日志文件
    echo > catalina.out          //新的空白catalina.out文件继续记录日志
    rm -rf $log_path/cron/catalina.out.${d90}.log       //删除超时文件

    find ${log_path} -mtime +7  -name "*.*.log" -exec rm -rf {} \;             //删除${log_path}目录下超过7天格式为"*.*.log"的文件
    find /home/tomcat/logall/monitor/ -mtime +7  -name "*.log.*" -exec rm -rf {} \;
    find /home/tomcat/logall/user/ -mtime +7  -name "*.log.*" -exec rm -rf {} \;
    find /home/tomcat/logall/admin/ -mtime +7  -name "*.log.*" -exec rm -rf {} \; 
