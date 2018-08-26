# spring-security-jwt
spring security结合jwt的demo
参考链接：
https://segmentfault.com/a/1190000009231329#articleHeader6
https://blog.csdn.net/itguangit/article/details/78960127

## 运行整个项目过程
 - 启动main函数
 - 注册验证组件，即SecurityConfig类中的 configure(AuthenticationManagerBuilder auth)方法，这里注册了自定义的CustomAuthenticationProvider组件
 - 设置验证规则，即SecurityConfig类中的-configure(HttpSecurity http)方法，设置了各种路由访问规则
 - 初始化过滤组件，即JWTLoginFilter和JWTAuthenticationFilter会被初始化
 
## 获取Token流程
  该项目定义所有"/login"请求交给JWTLoginFilter处理，用来获取JWT。  
  使用curl来测试，终端输入:  
  curl -H "Content-Type: application/json" -X POST -d '{"username":"admin","password":"123456"}' http://127.0.0.1:8080/login
  过程如下：
 - 拿到传入JSON，解析用户名密码 JWTLoginFilter 类 attemptAuthentication 方法
 - 自定义身份验证组件CustomAuthenticationProvider，进行身份验证，该类的authenticate（）方法(前提，该类的supports方法要返回true)
 - 验证成功，JWTLoginFilter类successfulAuthentication()方法
 - 生成JWT，TokenAuthenticationService类的 addAuthentication()方法
 
## 访问需要验证的资源的流程
   该项目定义访问"/hello"请求需要"AUTH_WRITE"权限，即需要验证当前用户是否有该权限，Token生效才能访问该资源  
   处理流程：
   - 接到请求进行拦截，-JWTAuthenticationFilter中的doFilter()方法
   - 验证JWT，-TokenAuthenticationService类中的getAuthentication()方法
   - 访问controller
   
 ###备注
 其实在Spring Security中，对于GrantedAuthority接口实现类来说是不区分是Role还是Authority，二者区别就是如果是hasAuthority判断，就是判断整个字符串，判断hasRole时，系统自动加上ROLE_到判断的Role字符串上，也就是说hasRole("CREATE")和hasAuthority('ROLE_CREATE')是相同的。利用这些可以搭建完整的RBAC体系。