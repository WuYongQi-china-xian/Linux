
//============================================介绍
		我们经常在普通用户下用到sudo命令
		
		有些命令只有 root账户可以执行，如shutdown，
		那么如果想让普通用户也能关机，
		可以将普通用户的账户添加到sudoers白名单
		//============或者切换root账户执行该命令	//当然也可以切换到其它用户，要指定的
	 
		为了不用频繁切换root，
		我们可以将普通用户添加到sudoers里面
		
		需要root手动将账户添加到sudoers列表中，
		才能让普通账户执行sudo命令
//============================================添加方式
	1).在root账户下添加原来的普通用户进sudoers
		root ALL=(ALL) ALL
		
		eric ALL=(ALL) ALL
	2).如果你希望之后执行的sudo命令时不需要输入密码，
		修改如下:
		
		eric ALL=(ALL) NOPASSWD:ALL
		
	3).切换回普通用户，尝试执行 sudo 命令
		
		sudo shutdown -h now 即可执行关机命令
		
	4).关于错误提示，如果用户不在 sudoers 列表中，
		将会得到以下提示：
		eric is not in the sudoers file.
		This incident will be reported.
//============================================配置文件详细说明		
	1). visudo 是 sudoers 专用编辑器.
		//此工具的优点在于，我们添加配置不准确的时候
		//保存退出时会给我们错误信息
	2). 使用 sudo -l	，查询当前用户用的，输入自己的密码
		查看哪些命令是可以执行的，
		哪些是禁止的
	3). sudoers 的第一行算一个规则 
		#号表示描述说明，并不执行，但后面不能有空格
	4). sudoers 的规则分为两类
		一类是别名定义，这个不是必须的
		一类是授权规则，但授权规则是必须的
		//==============================别名定义详解 
					//==========格式：
					Alias_Type NAME = item1,item2,...
						或
					Alias_Type NAME = item1,item2,item3:NAME = item4,item5
						//==========  Alias_Type 别名类型：分类
						Host_Alias  定义主机别名
						User_Alias  用户别名,用户或用户组，"用户组"前面要加%
						Runas_Alias 用来定义runas别名，这个别名指的是"目标用户",即sudo允许切换至的用户
									//Runas_Alias定义的是某个系统的用户
						Cmnd_Alias  定义的命令别名
						//========== NAME 就是别名
						比如：SYNADM、SYN_ADM、SYNAD0
						//========== item 指成员
						如果一个别名下有多个成员
						成员之间通过半角逗号分隔	// 半角逗号 ,
						//===================
						1).成员必须真实存在的：用户名、主机名、命令文件必须 真实存在
						2).定义什么类型的别名，就必须有什么样类型的成员相配
							如：Host_Alias  类型别名 ====> 成员：主机名(包括远程登陆的主机名)、ip地址(单个或整段)、掩码等
								User_Alias 	类型别名 ====> 必须用系统用户作为成员
								Runas_Alias 类型别名 ====> 必须用系统用户作为成员
								Cmnd_Alias  类型别名 ====> 必须是真实存在文件全路径，用可以用通配符表示
							//====================示例：
								Host_Alias	HT01=localhost,st05,st04,10,0,4,255.255.255.0,192.168.1.0/24
								Host_Alias	HT02=st08,st10
								Host_Alias	HT01=localhost,st05:HT02=st08,st10	//写在一起时，有冒号分隔
								
								User_Alias	SYSAD=linuxidc,linuxsir,bnnb,lanhaitun
								
								Cmnd_Alias	usermag=/usr/sbin/adduser,/usr/sbin/userdel,/usr/bin/passwd[A-Za-z]*,/bin/chown,/bin/chmod
		//==============================授权规则详解
		
			授权用户 主机=命令动作
					//这三个动做缺一不可
					/**这两点可以省略
						1).但在动作之前也可以指定切换到特定用户下，
							在这里指定切换的用户要用括号括起来
						2).如果不需要密码直接运行命令，应该加上NOPASSWD参数，
						
					*/
					//=================================示例：
					linuxidc ALL=/bin/chown,/bin/chmod
							表示"linuxidc用户"可以在"任何可能出现的主机名系统中"切换
							到root用户下，输入密码(linuxidc用户的密码吗？)，可以执行	/bin/chown 和 /bin/chmod 命令
							//在省略指定切换到哪个用户下时，默认切换到root用户下
							//这些命令都省略时。。。省略一个不是表示 这个意思
							
					linuxidc ALL=(root) /bin/chown,/bin/chmod		
							//表示可以切换到root下执行 	 /bin/chown
							//	可以切换到任何用户下执行 /bin/chmod 
					
					linuxidc ALL=(root) NOPASSWD: /bin/chown,/bin/chmod
							//表示可以切换到root 下执行   /bin/chown 不需要输入linuxidc用户密码
							//	  可以切换到任何用户下执行/bin/chmod 需要输入linuxidc用户密码
							
					
//==============================案例：
//==============================案例：
//==============================案例：
//==============================案例：
//==============================案例：
		1).普通linux想通过	more /etc/shadow	文件内容时会出现
			/etc/shadow 权限不足
			//因为这这
		2).解决办法，使用：
				sudo more /etc/shadow
				
		3).登陆root账号
			visudo 	//加入内容：
			obs ALL=NOPASSWD: /bin/more		//表示  obs 用户可以切换到root下来执行obs
			sudo -l		//查看可执行的sudo命令，需要输入自己的密码
		4). sudo more /etc/shadow
			
			//这样不止可以查看 /etc/shadow
			//还可以查看一些其它的文件
			
			//这样就只能使用这个命令查看这个文件
			obs ALL=NOPASSWD: /bin/more /etc/shadow
 
