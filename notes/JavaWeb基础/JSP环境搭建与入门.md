### 3. Tomcat 目录

bin: 可执行文件（starup.bat, shutdown.bat）

conf: 配置文件（server.xml）

lib: Tomcat 依赖的 jar 文件

log: 日志文件（记录出错等）

temp: 临时文件

webapps: 可执行的项目（将我们开发的项目放入该目录）

work: 放由 jsp 翻译成的 java ， 以及编译成的class文件  (jsp ->  java -> class)

### 4. 配置 Tomcat

 	1. 配置 JDK (必须配置 JAVA_HOME)
     - JAVA_HOME, CLASSPATH, PATH
 	2. 配置 CATALINA_HOME
 	3. 双击 bin/startup.bat 启动 Tomcat
     - 常见错误：可能与其他服务的端口号冲突
     - Tomcat 端口号默认端口 8080 （此端口号较为常见，容易冲突），建议修改此端口

### 5. 访问 Tomcat

- http://localhost:8888
- 常见状态码
  - 200：一切正常
  - 300/301：页面重定向（跳转）
  - 404：资源不存在
  - 403：一般是权限不足（如果访问 a 目录，但是 a 目录设置不可见）
  - 500：服务器内部错文（代码有误）
  - 其他编码：【积累】

### 6. JSP 在HTML中嵌套 Java 代码

在项目 /WEB-INF/web.xml 中设置默认的初始页面

```html
<welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
</welcome-file-list>
```

### 7. 虚拟路径

#### 方式一

将 web 项目配置到 webapps 以外的目录

conf/server.xml 中配置

```html
<Context docBase="" path="/JspProject" />
```

docBase: 实际路径

path：虚拟路径（绝对路径、相对路径【相对于webapps】）

#### 方式二

`apache-tomcat-8.5.45\conf\Catalina\localhost` 中新建 `项目名.xml` 中新增一行：

```html
<Context docBase="" path="/JspProject" />
```

### 8. 虚拟主机

 通过 www.test.com 访问本机

- conf/server.xml

  ```html
  <Engine name="Catalina" defaultHost="www.test.com">
      <Host appBase="D:\study\JspProject" name="www.test.com">			<Context docBase="D:\study\JspProject" path="/" />
      </Host>
  ```

- C:\Windows\System32\drivers\etc\hosts

  增加一行 `127.0.0.1      www.test.com`

流程：

​	www.test.com -> host 找映射关系 -> server.xml 找 Engine 的 defaultHost -> 通过 "/" 映射到 D:\study\JspProject



### 9. JSP 执行流程

jsp -> java (Servlet 文件) -> class

apache-tomcat-8.5.45\work\Catalina\localhost\JspProject\org\apache\jsp

第一次访问：服务端将 jsp 翻译成 Java, 再讲 Java 编译成 class 文件

第二次访问：直接访问文件（如果服务端代码修改了，将会在访问是重新翻译、编译）

JSP 和 Servlet 可以相互转换

因为第一次请求服务端会有**翻译**和**编译**的过程，因此比较慢；后续访问可以直接访问class，因此速度比较快。但是如果服务端修改了代码，则再次访问时会重新翻译、编译。











