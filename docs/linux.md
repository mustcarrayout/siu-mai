## 后台服务
```shell

# 新增服务文件
touch /usr/lib/systemd/system/something.service
# 给权限
chmod 644 /usr/lib/systemd/system/something.service

# service内容格式
[Unit]
Description=sendemail service
After=multi-user.target
Wants=network.target

[Service]
Type=idle
#启动服务的命令
ExecStart= /usr/bin/python3 /usr/local/src/sykj_bkhand/app.py  
[Install]
WantedBy=multi-user.target

# 配置服务开机启动
# 重新加载服务
systemctl daemon-reload
# 开启启动
systemctl enable service_name
# 取消开机启动
sysmtectl disable service_name

```

```shell


```

- vim 在行尾新增一行
- ```shell
  :%s/$/\0\r/g
  ```
- [[/etc/profile、/etc/bashrc、~/.bashrc、~/.bash_profile之间的区别]]
	- /etc/profile contains Linux system wide environment and startup programs. It is used by all users with bash, ksh, sh shell. Usually used to set PATH variable, user limits, and other settings for user. It only runs for login shell. If you wanted to make large changes or application specific changes use /etc/profile.d directory.
	- 系统变量=/etc/profile,影响所有用户
	- 用户变量=~/.bashrc 然后加source
	- /etc/profile做了什么事情?
		- 登录shell之后执行**/etc/profile.d/*.sh**脚本
- ![vim](https://www.runoob.com/wp-content/uploads/2015/10/vi-vim-cheat-sheet-sch.gif)



# mac

- ```shell
  # 允许任意来源的软件安装
  sudo spctl --master-disable
  # 关闭允许任意来源的软件安装
  sudo spctl --master-enable
  ```