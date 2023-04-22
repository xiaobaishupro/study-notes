## Spring Security自定义身份验证

#### 1.springsecurity身份验证的两种实现

想要在Spring Security中自定义身份验证时，可以实现自定义`AuthenticationProvider`或自定义`UserDetailsService`。

```
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth
    .userDetailsService(sysUserService).passwordEncoder(passwordEncoder());方式一
    //.authenticationProvider(authProvider) ;方式二
}
```

在AuthenticationProvider中，可以检查用户名和密码，并返回包含自定义对象的`Authentication`。

在`UserDetailsService`中，只获得用户名，当返回自定义UserDeatails时，框架会对密码进行检查。

#### 2.一些知识

##### AuthenticationManager

AuthenticationManager（接口）是认证相关的核心接口，也是发起认证的出发点

认证是由 AuthenticationManager 来管理的，但是真正进行认证的是 AuthenticationManager 中定义的 AuthenticationProvider。AuthenticationManager 中可以定义有多个 AuthenticationProvider。

##### UserDetailsService

这个接口只提供一个接口loadUserByUsername(String username)，可以通过扩展这个接口来显示获取我们的用户信息，用户登录时传递的用户名和密码也是通过这里这查找出来的用户名和密码进行校验，但是真正的校验不在这里，而是由AuthenticationManager以及AuthenticationProvider负责的，需要强调的是，如果用户不存在，不应返回NULL，而要抛出异常UsernameNotFoundException。

##### DaoAuthenticationProvider

当我们使用 authentication-provider 元素来定义一个 AuthenticationProvider 时，如果没有指定对应关联的 AuthenticationProvider 对象，Spring Security 默认会使用 DaoAuthenticationProvider。DaoAuthenticationProvider 在进行认证的时候需要一个 UserDetailsService 来获取用户的信息 UserDetails，其中包括用户名、密码和所拥有的权限等。所以如果我们需要改变认证的方式，我们可以实现自己的 AuthenticationProvider；如果需要改变认证的用户信息来源，我们可以实现 UserDetailsService。

##### GrantedAuthority

该接口表示了当前用户所拥有的权限（或者角色）信息。这些信息有授权负责对象AccessDecisionManager来使用，并决定最终用户是否可以访问某资源（URL或方法调用或域对象）。鉴权时并不会使用到该对象。

#### 3.AuthenticationProvider vs UserDetailsService

下面是大佬回答

一

> **选择AuthenticationProvider:**当使用不同的身份验证系统，并且数据库/数据模型中没有提供密码时，必须使用AuthenticationProvider。例如，曾经有一个项目，客户有一个集中式身份验证系统（CAS），所以系统不知道密码，必须实现AuthenticationProvider并将给定的密码发送给CAS，并根据其答案进行操作。
>
> **UserDetailsService：**选择但是在另一个系统中，将密码存储在数据库中，所以我所要做的就是实现UserDetailsService并检查用户是否存在于我的数据库中，spring-security必须完成其余的工作。

二

> 经常有一些关于UserDetailsService的混淆。它纯粹是一个用于用户数据的DAO，除了将数据提供给框架内的其他组件外，不执行其他功能。特别是，它不对用户进行身份验证，这是由AuthenticationManager完成的。在许多情况下，如果您需要自定义身份验证过程，则直接实现AuthenticationProvider更有意义。

> AuthenticationProvider将用户（请求）提供的用户名和密码与系统用户进行身份验证（比较）（这可以是任何系统，如维护注册用户列表的DB）
>
> UserDetailsService实现负责获取与用户提供的用户名匹配的系统用户详细信息。在这里，它只获取具有相同用户名的用户，而不告诉应用程序身份验证是成功还是失败。