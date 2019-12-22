# 1. 使用 Eclipse 开发 web 项目(JSP 项目) Tomcat

# 2. 在 Eclipse 中创建的 Web 项目：

- 浏览器可以直接访问 WebContent 中的文件，例如 `http://localhost:8888/MyProject/index1.jsp`，其中的index1.jps 就在 WebContent 目录中；
- 但是 WEB-INF 中的文件，无法通过客户端（浏览器）直接访问，只能通过请求转发来访问；
- **注意**：并不是任何的内部跳转都能访问 WEB-INF；原因是跳转有2种方式：请求转发、重定向。

# 3. 配置 Tomcat 运行时环境

JSP <--> Servlet

- 将 Tomcat/lib 中的 servlet-api.jar 加入项目的构建路径
- 右键项目 -> Build Path -> Add library -> Server Runtime

#  4. 部署 Tomcat

在 servers 面板新建一个 Tomcat 实例，再在该实例中部署项目（右键 - add）之后运行

- 注意：一般建议将 Eclipse 中的 Tomcat 与本地 Tomcat 的配置信息保持一致：在 Eclipse中的 Tomcat 设置为托管模式：【第一次】创建 Tomcat 实例之后，双击，选择 Server Location 的第二项

# 5. 统一字符集编码

- 编码分类：
  - 设置 JSP 文件的编码（JSP 文件中的 pageEncoding 属性）： jsp -> java
  - 设置浏览器读取 jsp 文件的编码（jsp 文件中 content 属性）
  - 一般上述设置成一致的编码，推荐使用 UTF-8
- 文本编码：
  - 将整个 Eclipse 中的文件 统一设置 （推荐）
  - 设置某一个项目
  - 设置单独文件

# 6. JSP 的页面元素：

## 1. 脚本 Scriptlet

- ```jsp
  <%
  	局部变量、java 语句
  %>
  ```

- ```jsp
  <%!
  	全局变量、定义方法    
  %>
  ```

- ```jsp
  <%=输出表达式>
  ```

  

**注意**：

- 一般而言，修改 web.xml、配置文件、java ，需要重启 Tomcat 服务，但是如果修改 JSP/HTML/CSS/JS, 不需要重启。
- out.println() 不能回车；要想回车：“\<br/>”, 即 out.print() <%= %> 可以直接解析 HTML 代码

## 2. 指令

- Page 指令  <%@ page ... %>

  page 指定的属性：

  	- language：JSP 页面使用的脚本语言
  	- import：导入类
  	- pageEncoding：jsp 文件自身编码，即 jsp 转成 java 的编码
  	- contentType：浏览器解析 jsp 的编码

3. 注释

   - HTML 注释  `<!-- -->`, 可以客户通过浏览器查看源码所查看
   - Java 注释  `//  /* */`
   - JSP 注释  `<%-- --%>`

# 7. JSP内置对象（自带的， 不需要 new 也能使用的对象）

## 1. out：输出对象，向客户端输出内容

## 2. request：请求对象；存储“客户端向服务端发送的请求信息”

- request 的常见方法：
  - String getParameter(String name) : 根据请求字段名 Key，返回字段值 value；
  - String[] getParameterValues(String name): 根据请求的字段名key，返回多个字段值 value （例如 checkbox）
  - void setCharacterEncoding("编码格式 UTF-8")：设置post方式的请求编码（Tomcat 以前默认 iso-8859-1，Tomcat8 以后 utf-8）
  - getRequestDispatcher("b.jsp").forward(request, response); : 请求转发的方式跳转页面 A -> B
  - ServletContext getServletContext(): 获取项目的 ServletContext 对象

实例：注册

​	register.jsp, show.jsp

- get 提交方式： method=“get” 和地址栏、超链接（\<a href="xx"/>）请求方式默认都属于 get 提交方式。

- get 与 post 请求方式的区别：

  a. get 方式在地址栏显示 请求信息 （但是地址栏能够容纳的信息有限，4 - 5 KB；如果请求数据存在大文件，只能用post请求方式）

  b. 文件上传操作，必须是 post （一般推荐使用 post）



统一请求的编码 request：

​	get 方式请求，如果出现乱码，解决：

  1. 统一每一个变量的编码（不推荐）

     ```java
     new String(旧编码， 新编码);
     name = new String(name.getBytes("iso-8859-1"), "utf-8");
     ```

2. 修改 server.xml, 一次性的更改 Tomcat 默认 get 提交方式的编码（UTF-8），建议使用  Tomcat 时，首先在 server.xml 中统一 get 方式的编码.. URIEncoding="UTF-8"

## 3. response: 响应对象

- 提供的方法：

  - void addCookie(Cookie cookie); // 服务端向客户端增加 cookie 对象
  - void sendRedirect(String location) throws IOException; // 页面跳转的一种方式（重定向）
  - void setContentType(String type); // 设置服务端响应的编码（设置服务端的 contentType 类型）

  示例：登陆 login.jsp -> check.jsp

  - 请求转发和重定向的区别：

    |                          | 请求转发        | 重定向            |
    | :----------------------: | :-------------- | ----------------- |
    |      地址栏是否改变      | 不变(check.jsp) | 改变(success.jsp) |
    | 是否保留第一次请求的数据 | 保留            | 不保留            |
    |         请求次数         | 1               | 2                 |

## 4. session (服务端)

- Cookie （客户端，不是内置对象）：Cookie 是由服务端生成的，再发送给客户端保存。相当于本地缓存的作用：客户端（hello.mp4, zs/abc）-> 服务端（hello.mp4; zs/abc）

  作用：提高访问服务端的效率，但是安全性较差。

  Cookie： name=value

  ```java
  javax.servlet.http.Cookie
  public Cookie(String name, String value);
  String getName(); // 获取 name
  String getValue(); // 获取 value
  String setValue(); // 获取 value
  void setMaxAge(int expiry); //最大有效期（秒）
  ```

  服务端准备Cookie：

  ​	`response.addCookie(Cookie cookie);`

  页面跳转 (转发，重定向)，客户端获取 cookie：

  `request.getCookies();`

  a. 服务端增加 cookie: response 对象；客户端获取对象：request对象

  b. 不能直接获取某一个单独对象，只能一次性将全部的cookie拿到

  - 通过 F12 可以发现，除了自己设置的 Cookie 对象外，还有一个 name 为 JSESSIONID 的 cookie；

  - 建议 cookie 只保存 英文和数字，否则需要进行编码、解码。

  

- session: 会话 （从开始到结束）

  a. 浏览网站：开始 - 关闭

  b. 购物：浏览、付款、退出

  c. 电子邮件：浏览、写邮件、退出

  session 机制：

  - 客户端第一次请求服务端时，（jsessionid - sessionid）服务端会产生一个 session 对象（用于保存客户的信息）；
  - 并且每个 session 对象都会有一个唯一的sessionId（用于区分其他 session）；

- 服务端会产生一个 cookie，并且该 cookie 的 name=JSESSIONID，value=服务端 sessionId 的值；
    - 然后服务端会在响应客户端的同时将该cookie发送给客户端，至此客户端就有了一个cookie (JSESSIONID);
    - 因此，客户端的cookie就可以和服务端的session一一对应（JSESSIONID - sessionID）
    - 客户端第二/n次请求服务端时: 服务端会先用客户端cookie种的JSESSIONID 去服务端的 session 中匹配sessionid，如果匹配成功（cookie jsession 和 session sessionid），说明此用户不是第一次访问，无需登录

session 总结: 

     	1. session 存储在服务端；
     	2. session 是在同一个用户（客户）请求时共享；
     	3. 实现机制：第一次客户请求时产生一个 sessionid 并复制给 cookie 的 jsessionid 然后发给客户端。最终通过 session 的 sessionid 一一对应关系。

Cookie总结：

	1. 客户端在第一次请求服务端时，如果服务端发现此请求没有JSESSIONID, 则会创建一个 name=JSESSIONID 的 cookie 并返回给客户端；
 	2. Cookie 不是内置对象，要使用必须 new

  session 方法：

  ```java
  String getId(); //获取 sessionId
  boolean isNew(); //判断是否是 新用户（第一次访问）
  void invalidate(); //使 session 失效（退出登录、注销）
  
  void setAttribute();
  void getAttribute();
  
  void setMaxInactiveInterval(second); // 设置最大有效非活动时间
  int getMaxInactiveInterval(); //获取最大有效非活动时间
  ```

  session 和 cookie 的区别：

|          | session | cookie   |
| :------- | :------ | -------- |
| 保存位置 | 服务端  | 客户端   |
| 安全性   | 较安全  | 较不安全 |
| 保存内容 | Object  | String   |

## 5. application（全局对象）

```java
String getContextPath(); //虚拟路径
String getRealPath(String name); //绝对路径（虚拟路径）
```

## 6. pageContent

## 7. config

## 8. page

## 9. exception

# 8. 四种范围对象（小 -> 大）

- pageContext  JSP页面容器 （page对象）； //当前页面有效

- request  请求对象  //同一次请求有效

- session  会话对象  //同一次会话有效

- application  全局对象  //全局有效（整个项目有效）

## 以上4个对象共有的方法

```java
Object getAttribute(String name);  //根据属性名，获取属性值
void setAttribute(String name, Object obj); //设置属性值（新增，修改）
	setAttribute("a", "b");  //如果a对象之前不存在，则新建一个a对象；如果a之前已经存在，则将a的值改为b
void removeAttribute(String name);  //根据属性名，删除对象
```

1. pageContext  当前页面有效（页面跳转后无效）
2. request  同一次请求有效，其他请求无效（请求转发后有效；重定向后无效）；
3. session  同一次会话有效，（无论怎么跳转都有效，关闭或切换浏览器后无效；从登陆到退出之间全部有效）；
4. application  全局变量，整个项目运行期间都有效（切换浏览器仍然有效），关闭服务或其他项目 无效。

想要多个项目共享或重启后仍然有效：JNDI

- 以上的4个范围对象，通过 setAttribute() 复制，通过 getAttribute() 取值；
- 以上范围对象，尽量使用最小的范围。因为对象的范围越大，性能损耗越大。











