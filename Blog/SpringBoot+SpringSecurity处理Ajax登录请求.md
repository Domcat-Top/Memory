### 创建工程

首先我们需要创建一个Spring Boot工程，创建时需要引入Web、Spring Security、MySQL和MyBatis(数据库框架其实随意，我这里使用MyBatis)，创建好之后，依赖文件如下：  

```
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.3.1</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>commons-codec</groupId>
    <artifactId>commons-codec</artifactId>
    <version>1.11</version>
</dependency>
```  

注意最后一个**commons-codec**依赖是我手动加入进来的，这是一个Apache的开源项目，可以用来生成MD5消息摘要，我在后文中将对密码进行简单的处理。  

### 创建数据库并配置

为了简化逻辑，我这里创建了三个表，分别是用户表、角色表、用户角色关联表，如下：  

![p271]()  

接下来我们需要在application.properties中对自己的数据库进行简单的配置，这里各位小伙伴视自己的具体情况而定。  

```
spring.datasource.url=jdbc:mysql:///vueblog
spring.datasource.username=root
spring.datasource.password=123
```  

### 构造实体类

这里主要是指构造用户类，这里的用户类比较特殊，必须实现UserDetails接口，如下：  

```
public class User implements UserDetails {
    private Long id;
    private String username;
    private String password;
    private String nickname;
    private boolean enabled;
    private List<Role> roles;

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return enabled;
    }

    @Override
    public List<GrantedAuthority> getAuthorities() {
        List<GrantedAuthority> authorities = new ArrayList<>();
        for (Role role : roles) {
            authorities.add(new SimpleGrantedAuthority("ROLE_" + role.getName()));
        }
        return authorities;
    }
    //getter/setter省略...
}
```  

实现了UserDetails接口之后，该接口中有几个方法需要我们实现，四个返回Boolean的方法都是见名知意，enabled表示档期账户是否启用，这个我数据库中确实有该字段，因此根据查询结果返回，其他的为了简单期间都直接返回true，getAuthorities方法返回当前用户的角色信息，用户的角色其实就是roles中的数据，将roles中的数据转换为List<GrantedAuthority>之后返回即可，**这里有一个要注意的地方，由于我在数据库中存储的角色名都是诸如‘超级管理员’、‘普通用户’之类的，并不是以```ROLE_```这样的字符开始的，因此需要在这里手动加上```ROLE_```,切记**。  

另外还有一个Role实体类，比较简单，按照数据库的字段创建即可，这里不再赘述。  

### 创建UserService

这里的UserService也比较特殊，需要实现UserDetailsService接口，如下：  

```
@Service
public class UserService implements UserDetailsService {
    @Autowired
    UserMapper userMapper;
    @Autowired
    RolesMapper rolesMapper;

    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        User user = userMapper.loadUserByUsername(s);
        if (user == null) {
            //避免返回null，这里返回一个不含有任何值的User对象，在后期的密码比对过程中一样会验证失败
            return new User();
        }
        //查询用户的角色信息，并返回存入user中
        List<Role> roles = rolesMapper.getRolesByUid(user.getId());
        user.setRoles(roles);
        return user;
    }
}
```  

实现了UserDetailsService接口之后，我们需要实现该接口中的loadUserByUsername方法，即根据用户名查询用户。这里注入了两个MyBatis中的Mapper，UserMapper用来查询用户，RolesMapper用来查询角色。在loadUserByUsername方法中，首先根据传入的参数（参数就是用户登录时输入的用户名）去查询用户，如果查到的用户为null，可以直接抛一个UsernameNotFoundException异常，但是我为了处理方便，返回了一个没有任何值的User对象，这样在后面的密码比对过程中一样会发现登录失败的（这里大家根据自己的业务需求调整即可），如果查到的用户不为null，此时我们根据查到的用户id再去查询该用户的角色，并将查询结果放入到user对象中，这个查询结果将在user对象的getAuthorities方法中用上。  

### Security配置

我们先来看一下我的Security配置，然后我再来一一解释：  

```
@Configuration
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    UserService userService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userService).passwordEncoder(new PasswordEncoder() {
            @Override
            public String encode(CharSequence charSequence) {
                return DigestUtils.md5DigestAsHex(charSequence.toString().getBytes());
            }

            /**
             * @param charSequence 明文
             * @param s 密文
             * @return
             */
            @Override
            public boolean matches(CharSequence charSequence, String s) {
                return s.equals(DigestUtils.md5DigestAsHex(charSequence.toString().getBytes()));
            }
        });
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/admin/**").hasRole("超级管理员")
                .anyRequest().authenticated()//其他的路径都是登录后即可访问
                .and().formLogin().loginPage("/login_page").successHandler(new AuthenticationSuccessHandler() {
            @Override
            public void onAuthenticationSuccess(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Authentication authentication) throws IOException, ServletException {
                httpServletResponse.setContentType("application/json;charset=utf-8");
                PrintWriter out = httpServletResponse.getWriter();
                out.write("{\"status\":\"ok\",\"msg\":\"登录成功\"}");
                out.flush();
                out.close();
            }
        })
                .failureHandler(new AuthenticationFailureHandler() {
                    @Override
                    public void onAuthenticationFailure(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, AuthenticationException e) throws IOException, ServletException {
                        httpServletResponse.setContentType("application/json;charset=utf-8");
                        PrintWriter out = httpServletResponse.getWriter();
                        out.write("{\"status\":\"error\",\"msg\":\"登录失败\"}");
                        out.flush();
                        out.close();
                    }
                }).loginProcessingUrl("/login")
                .usernameParameter("username").passwordParameter("password").permitAll()
                .and().logout().permitAll().and().csrf().disable();
    }

    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().antMatchers("/reg");
    }
}
```  

这是我们配置的核心，小伙伴们听我一一道来：   

1.首先这是一个配置类，因此记得加上@Configuration注解，又因为这是Spring Security的配置，因此记得继承WebSecurityConfigurerAdapter。  
2.将刚刚创建好的UserService注入进来，一会我们要用。  
3.configure(AuthenticationManagerBuilder auth)方法中用来配置我们的认证方式，在auth.userDetailsService()方法中传入userService，这样userService中的loadUserByUsername方法在用户登录时将会被自动调用。后面的passwordEncoder是可选项，可写可不写，因为我是将用户的明文密码生成了MD5消息摘要后存入数据库的，因此在登录时也需要对明文密码进行处理，所以就加上了passwordEncoder，加上passwordEncoder后，直接new一个PasswordEncoder匿名内部类即可，这里有两个方法要实现，看名字就知道方法的含义，第一个方法encode显然是对明文进行加密，这里我使用了MD5消息摘要，具体的实现方法是由commons-codec依赖提供的；第二个方法matches是密码的比对，两个参数，第一个参数是明文密码，第二个是密文，这里只需要对明文加密后和密文比较即可（小伙伴如果对此感兴趣可以继续考虑密码加盐）。   
4.configure(HttpSecurity http)用来配置我们的认证规则等，authorizeRequests方法表示开启了认证规则配置，antMatchers("/admin/**").hasRole("超级管理员")表示```/admin/**```的路径需要有‘超级管理员’角色的用户才能访问，我在网上看到小伙伴对hasRole方法中要不要加```ROLE_```前缀有疑问,这里是不要加的，如果用hasAuthority方法才需要加。anyRequest().authenticated()表示其他所有路径都是需要认证/登录后才能访问。接下来我们配置了登录页面为login_page，登录处理路径为/login，登录用户名为username，密码为password，并配置了这些路径都可以直接访问，注销登陆也可以直接访问，最后关闭csrf。在successHandler中，使用response返回登录成功的json即可，切记不可以使用defaultSuccessUrl，defaultSuccessUrl是只登录成功后重定向的页面，failureHandler也是由于相同的原因不使用failureUrl。    
5.configure(WebSecurity web)方法中我配置了一些过滤规则，不赘述。  
6.另外，对于静态文件，如```/images/**```、```/css/**```、```/js/**```这些路径，这里默认都是不拦截的。  

### Controller

最后来看看我们的Controller，如下：  

```
@RestController
public class LoginRegController {

    /**
     * 如果自动跳转到这个页面，说明用户未登录，返回相应的提示即可
     * <p>
     * 如果要支持表单登录，可以在这个方法中判断请求的类型，进而决定返回JSON还是HTML页面
     *
     * @return
     */
    @RequestMapping("/login_page")
    public RespBean loginPage() {
        return new RespBean("error", "尚未登录，请登录!");
    }
}
```  

这个Controller整体来说还是比较简单的，RespBean一个响应bean，返回一段简单的json，不赘述，这里需要小伙伴注意的是```login_page```,我们配置的登录页面是一个```login_page```,但实际上```login_page```并不是一个页面，而是返回一段JSON，这是因为当我未登录就去访问其他页面时Spring Security会自动跳转到到```login_page```页面，但是在Ajax请求中，不需要这种跳转，我要的只是是否登录的提示，所以这里返回json即可。  

### 测试
