### springSecurity

https://juejin.cn/post/7106300827035238407

https://juejin.cn/post/6846687598442708999

##### 1. SpringSecurity是什么

Spring Security是一个能够为基于Spring的企业应用系统提供声明式的安全访问控制解决方案的安全框架。

Spring Security对Web安全性的支持大量地依赖于Servlet过滤器，相当于一层一层的过滤器。

##### 2.SpringSecurity工作流程

一个web请求会经过一条过滤器链，在经过过滤器链的过程中会完成认证与授权，如果中间发现这条请求未认证或者未授权，会根据被保护API的权限去抛出异常，然后由异常处理器去处理这些异常。

##### 3. 📝SpringSecurity的重要概念

知道了Spring Security的大致工作流程之后，我们还需要知道一些非常重要的概念也可以说是组件：

- **SecurityContext**：上下文对象，`Authentication`对象会放在里面。
- **SecurityContextHolder**：用于拿到上下文对象的静态工具类。
- **Authentication**：认证接口，定义了认证对象的数据形式。
- **AuthenticationManager**：用于校验`Authentication`，返回一个认证完成后的`Authentication`对象。

**1.SecurityContext**

上下文对象，认证后的数据就放在这里面，接口定义如下：

```java
public interface SecurityContext extends Serializable {
 // 获取Authentication对象
 Authentication getAuthentication();

 // 放入Authentication对象
 void setAuthentication(Authentication authentication);
}
```

这个接口里面只有两个方法，其主要作用就是get or set `Authentication`。

**2. SecurityContextHolder**

```typescript
public class SecurityContextHolder {

 public static void clearContext() {
  strategy.clearContext();
 }

 public static SecurityContext getContext() {
  return strategy.getContext();
 }
    
    public static void setContext(SecurityContext context) {
  strategy.setContext(context);
 }

}
```

可以说是`SecurityContext`的工具类，用于get or set or clear `SecurityContext`，默认会把数据都存储到当前线程中。

**3. Authentication**

```java
public interface Authentication extends Principal, Serializable {
 
 Collection<? extends GrantedAuthority> getAuthorities();
 Object getCredentials();
 Object getDetails();
 Object getPrincipal();
 boolean isAuthenticated();
 void setAuthenticated(boolean isAuthenticated) throws IllegalArgumentException;
}
```

这几个方法效果如下：

- **`getAuthorities`**: 获取用户权限，一般情况下获取到的是**用户的角色信息**。
- **`getCredentials`**: 获取证明用户认证的信息，通常情况下获取到的是密码等信息。
- **`getDetails`**: 获取用户的额外信息，（这部分信息可以是我们的用户表中的信息）。
- **`getPrincipal`**: 获取用户身份信息，在未认证的情况下获取到的是用户名，**在已认证的情况下获取到的是 UserDetails。**
- **`isAuthenticated`**: 获取当前 `Authentication` 是否已认证。
- **`setAuthenticated`**: 设置当前 `Authentication` 是否已认证（true or false）。


`Authentication`只是定义了一种在SpringSecurity进行认证过的数据的数据形式应该是怎么样的，要有权限，要有密码，要有身份信息，要有额外信息。

**4. AuthenticationManager** 

```java
public interface AuthenticationManager {
 // 认证方法
 Authentication authenticate(Authentication authentication)
   throws AuthenticationException;
}
```

`AuthenticationManager`定义了一个认证方法，它将一个未认证的`Authentication`传入，返回一个已认证的`Authentication`，默认使用的实现类为：ProviderManager。

接下来大家可以构思一下如何将这四个部分，串联起来，构成Spring Security进行认证的流程：

\1. 👉先是一个请求带着身份信息进来
 \2. 👉经过`AuthenticationManager`的认证，
 \3. 👉再通过`SecurityContextHolder`获取`SecurityContext`，
\4. 👉最后将认证后的信息放入到`SecurityContext`。



