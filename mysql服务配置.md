## mysql服务配置 ##

### 1、进入系统文档 ###
	cd /etc/systemd/system

### 2、创建一个mysql.service文件 ###
	mkdir mysql.service

### 3、编辑文件内容如下： ###
	#
	# Simple MySQL systemd service file
	#
	# systemd supports lots of fancy features, look here (and linked docs) for a full list: 
	#   http://www.freedesktop.org/software/systemd/man/systemd.exec.html
	#
	# Note: this file ( /usr/lib/systemd/system/mysql.service )
	# will be overwritten on package upgrade, please copy the file to 
	#
	#  /etc/systemd/system/mysql.service 
	#  
	# to make needed changes.
	# 
	# systemd-delta can be used to check differences between the two mysql.service files.
	#
	
	[Unit]
	Description=MySQL Community Server
	After=network.target
	After=syslog.target
	
	[Install]
	WantedBy=multi-user.target
	Alias=mysql.service
	
	[Service]
	User=mysql //用户名
	Group=mysql //密码
	
	# Execute pre and post scripts as root
	#PermissionsStartOnly=true
	
	# Needed to create system tables etc.
	ExecStartPre=/usr/bin/mysql-systemd-start pre
	#Type=forking
	# Start main service
	ExecStart=/usr/bin/mysqld_safe --basedir=/usr //mysql启动路径
	#ExecStart=/usr/local/tomcat/bin/startup.sh
	#ExecStop=/usr/local/tomcat/bin/shutdown.sh
	
	# Don't signal startup success before a ping works
	ExecStartPost=/usr/bin/mysql-systemd-start post
	
	# Give up if ping don't get an answer
	TimeoutSec=600
	
	Restart=always
	PrivateTmp=false


### 4、附件:redis 服务配置文件，tomcat服务配置文件，mongo配置文件

**tomcat:**
	
	[Unit]
	Description=tomcat
	After=network.target
	
	[Service]
	User=tomcat
	Group=tomcat
	
	Type=forking
	
	ExecStart=/usr/local/tomcat/bin/startup.sh
	ExecStop=/usr/local/tomcat/bin/shutdown.sh
	PrivateTmp=true
	
	[Install]
	WantedBy=multi-user.target

**Redis:**

	[Unit]
	Description=redis
	
	[Install]
	Alias=redis.service
	
	[Service]
	User=root
	Group=root
	
	# Execute pre and post scripts as root
	#PermissionsStartOnly=true
	
	# Needed to create system tables etc.
	#ExecStartPre=/usr/bin/mysql-systemd-start pre
	Type=forking
	#PIDFile=/var/run/redis.pid
	# Start main service
	ExecStart=/home/dspm/redis/redis-server /home/dspm/redis/redis.conf
	#ExecStart=/usr/local/tomcat/bin/startup.sh
	ExecStop=/home/dspm/redis/redis-server shutdown
	
	# Don't signal startup success before a ping works

**Mongo:**
	
	[Unit]
	Description=mongodb
	
	[Install]
	Alias=mongodb.service
	
	[Service]
	User=root
	Group=root
	Type=forking
	ExecStart=/home/dspm/mongodb/bin/mongod -f /home/dspm/mongodb/mongo.conf
	ExecStop=/home/dspm/mongodb/bin/mongod --shutdown -f /home/dspm/mongodb/mongo.conf
	# Don't signal startup success before a ping works
	PrivateTmp=true

**mysql:**

	[Unit]
	Description=MySQL Community Server
	After=network.target
	After=syslog.target
	
	[Install]
	WantedBy=multi-user.target
	Alias=mysql.service
	
	[Service]
	User=mysql
	Group=mysql
	
	# Execute pre and post scripts as root
	PermissionsStartOnly=true
	
	# Needed to create system tables etc.
	ExecStartPre=/usr/bin/mysql-systemd-start pre
	
	# Start main service
	ExecStart=/usr/bin/mysqld_safe
	
	# Don't signal startup success before a ping works
	ExecStartPost=/usr/bin/mysql-systemd-start post
	
	# Give up if ping don't get an answer
	TimeoutSec=600
	
	Restart=always
	PrivateTmp=false




                                                                                                                                                                                                                       