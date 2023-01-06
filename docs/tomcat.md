- 日志配置
	- 运行日志有5类：catalina 、 localhost 、 manager 、 admin 、 host-manager
	- 每类日志有7种级别：WARNING > INFO > CONFIG > FINE > FINER > FINEST (lowest value) 级别越高报的信息越少
	  修改运行日志报的信息级别在conf/logging.propertie
	- SEVERE (最高级别) > WARNING > INFO > CONFIG > FINE > FINER(精心) > FINEST (所有内容,最低级别)
	- server.xml
		- ```xml
		  <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
		  
		                 prefix="localhost_access_log." suffix=".txt"
		  
		                 pattern="%h %l %u %t &quot;%r&quot; %s %b" />
		  
		  ```
		- pattern 参数：
		  %a - 远端IP地址
		  %A - 本地IP地址
		  %b - 发送的字节数，不包括HTTP头，如果为0，使用"－"
		  %B - 发送的字节数，不包括HTTP头
		  %h - 远端主机名(如果resolveHost=false，远端的IP地址）
		  %H - 请求协议
		  %l - 从identd返回的远端逻辑用户名（总是返回 '-'）
		  %m - 请求的方法（GET，POST，等）
		  %p - 收到请求的本地端口号
		  %q - 查询字符串(如果存在，以 '?'开始)
		  %r - 请求的第一行，包含了请求的方法和URI
		  %s - 响应的状态码
		  %S - 用户的session ID
		  %t - 日志和时间，使用通常的Log格式
		  %u - 认证以后的远端用户（如果存在的话，否则为'-'）
		  %U - 请求的URI路径
		  %v - 本地服务器的名称
		  %D - 处理请求的时间，以毫秒为单位
		  %T - 处理请求的时间，以秒为单位