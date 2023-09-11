### JSP（动态网页，基于BS架构）
- 不是和是否有“动感”混为一谈
- 看网页是否会随时间、地点、用户操作的改变而改变
### 两种架构（BS/CS）
CS:
- A、如果软件升级 所有(客户端)软件都需要升级
- B、维护麻烦、需要维护每一台客户端软件
- C、每一台客户端都需要安装客户端软件
BS:
- 客户端可以通过浏览器直接访问服务端
**注意BS CS各有优势谁也不是谁的替代品 **
### 九大内置对象
#### 1、request
- 含义：请求对象，存储“客户端向服务端发送的请求信息“

| 常见方法                                                   | 参数类型                                   | 返回值              |
| ---------------------------------------------------------- | ------------------------------------------ | ------------------- |
| getParameter（String name）                                | key（input标签name的属性）                 | value               |
| getParameterValues（String name）                          | key（input标签name的属性）                 | String[]  多个value |
| SetCharacterEncoding (“编码格式utf-8”)                     | 默认（tomca7.0：“iso-8859-1“，8.0”utf-8“） | Void                |
| getRequestDispatcher（”b.jsp”）.forward(request,response); | 请求转发的页面                             | 无                  |
| getServletContext（）                                      | 无                                         | Object              |
#### 2、response响应对象
| 常见方法                                                 | 参数类型                                   | 返回值 |
| -------------------------------------------------------- | ------------------------------------------ | ------ |
| ( 服务端向客户端增加cookie对象)addCookie(Cookie  cookid) | cookid                                     | void   |
| (重定向)sendRedirect(String location) throws IOException | locationc重定向页面                        | void   |
| （设置编码格式）setContetType(String type)               | 默认（tomca7.0：“iso-8859-1“，8.0”utf-8“） | Void   |
| **比较**                                                 |                                            |        |

|                            | 请求转发                                                   | 重定向                                            |
| -------------------------- | ---------------------------------------------------------- | ------------------------------------------------- |
|                            | getRequestDispatcher（”b.jsp”）.forward(request,response); | sendRedirect(String location) throws IOException; |
| 地址栏是否改变             | 不变（check.jsp）                                          | 改变(success.jsp)                                 |
| 是否保留第一次请求时的数据 | 保留                                                       | 不保留                                            |
| 请求的次数                 | 1                                                          | 2                                                 |

#### 3、session（服务端）会话对象
 ** Cookie（客户端，不是内置对象）需要new：Cookie是由服务端生成的，再发给客户端保存 相当于本地缓存： 客户端->服务端**

| 常见方法                            | 参数类型    | 返回值         |
| ----------------------------------- | ----------- | -------------- |
| 添加cookieaddCookie(Cookie  cookid) | cookid      | void           |
| request.getCookies()                | 无 全部获取 | 对象集         |
| String getName()                    | 无          | 对应cookies[i] |
| String getValue()                   | 无          | 对应cookies[i] |
| Void setmaxAge(int expry毫秒)       | expry毫秒   | Void           |

服务端增加cookie：response对象 
客户端获取cookie：request对象
建议Cookie中只保存英文和数字否则需要编码和解码

- 每次访问服务端都会产生一个session，为什么不会匹配错误呢 因为有JSESSIONID
- 客户端第一次请求服务端时，服务端会产生一个session对象（用于保存该客户的信息）；并且每个session对象都会有一个唯一的sessionID（用去区分其他session），服务端会产生一个cookie，并且改cookie的name=JSESSIONID，vale=服务端的sessionid的值然后服务端会在响应客户端的同时将该cookie发送给客户端所以和护短就有了一个cookie（JSESSIONISD），这样客户端的cookie就可以和服务端的session一一对应了
- 之后请求服务端是：服务端会先用客户端cookie的JSESSIONID去服务端的session中匹配sessionid，如果匹配成功，说明此用户不是第一次访问，就无需登陆
- 强调：1、色的Dion存储在服务端 2、session实在同一个客户端（客户）请求时共享3、实现机制：在第一次客户请求时 产生一个sessionid并复制给cookie的jsessiod发给客户端
**Session**

| 常见方法                   | 参数类型                | 返回值 |
| -------------------------- | ----------------------- | ------ |
| SetAtribute(KV)            | 给session赋值           | value  |
| getAtribute(K)             | K                       | value  |
| getID()                    | 获取sessionID           | String |
| setMaxInactiveInterval(秒) | 设置最大有效 非活动时间 | void   |
| get MaxInactiveInterva(秒) | 获取最大有效 非活动时间 | int    |

**比较**

|            | Session                       | Cookie                        |
| ---------- | ----------------------------- | ----------------------------- |
| 保存的位置 | 服务端                        | 客户端                        |
| 安全性     | 较安全                        | 不安全                        |
| 保存的内容 | （String name，Object value） | （String name，String value） |

客户端在第一次请求服务端是如果服务端发现此请求没有JSESSIONID则会创建一个name=JSEESSIONID的cookie返回给客户端
#### 4、application全局对象

| 常见方法                       | 参数类型     | 返回值                 |
| ------------------------------ | ------------ | ---------------------- |
| getContexPath()                | 无           | 返回当前项目的虚拟路径 |
| getRealPath( getContexPath() ) | 项目虚拟路径 | 返回项目绝对路径       |

#### 5、pageContext  JSP页面容器
#### 6、config  配置对象（服务器配置信息）
#### 7、page   当前JSP页面对象（相当于java中的this）
#### 8、Out  输出对象，向客户端输出内容
#### 9、exeception 异常对象