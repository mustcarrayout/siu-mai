- SpringMVC是基于Servlet的，没有Servlet就没有SpringMVC；
- Servlet有什么东西？
	- web.xml
	- filter
	- handler（exception）
	- XXServlet
- SpringMVC的核心之一是**DispatcherServlet**；
- SpringMVC=Spring+Servlet
	- 配置文件代码化
	- 核心功能Servlet仍然是servlet；
	- **启动的入口还是Servlet的init**；
- ```xml
  <web-app>
    <!-- 容器怎么调用代码，这就是入口 -->
      <listener>
          <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
      </listener>
      <context-param>
          <param-name>contextConfigLocation</param-name>
          <param-value>/WEB-INF/app-context.xml</param-value>
      </context-param>
  
      <servlet>
          <servlet-name>app</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
          <init-param>
              <param-name>contextConfigLocation</param-name>
              <param-value></param-value>
          </init-param>
          <load-on-startup>1</load-on-startup>
      </servlet>
  
      <servlet-mapping>
          <servlet-name>app</servlet-name>
          <url-pattern>/app/*</url-pattern>
      </servlet-mapping>
  
  </web-app>
  ```