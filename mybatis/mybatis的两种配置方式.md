## mybatis的两种配置方式

#### xml配置

```yml
mybatis:
  #标注mybatis配置文件的位置
  config-location: classpath:mybatis-config.xml
  #标注待解析的mapper的xml文件位置
  mapper-locations: classpath:mapper/*.xml 
  #标注实体类位置
  type-aliases-package: com.xj.demo.pojo
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
    	<!-- #开启mybatis驼峰式命名规则自动转换 -->
        <setting name="mapUnderscoreToCamelCase" value="true" />
    </settings>
    <typeAliases>
        <typeAlias alias="Integer" type="java.lang.Integer" />
        <typeAlias alias="Long" type="java.lang.Long" />
        <typeAlias alias="HashMap" type="java.util.HashMap" />
        <typeAlias alias="LinkedHashMap" type="java.util.LinkedHashMap" />
        <typeAlias alias="ArrayList" type="java.util.ArrayList" />
        <typeAlias alias="LinkedList" type="java.util.LinkedList" />
    </typeAliases>
</configuration>

```

更多setting

```xml
<settings>
//开启全局缓存，默认开启
  <setting name="cacheEnabled" value="true"/>
//开启延时加载，默认关闭
  <setting name="lazyLoadingEnabled" value="true"/>
//按需加载对象属性。默认关闭，如果开启了会加载对象的所有属性
  <setting name="multipleResultSetsEnabled" value="true"/>
//使用数据库列别名代替列名，默认开启
  <setting name="useColumnLabel" value="true"/>
//允许JDBC自动生成主键，默认关闭且需要驱动支持
  <setting name="useGeneratedKeys" value="false"/>
//映射策略，默认PARTIAL只会映射没有定义嵌套的结果集
  <setting name="autoMappingBehavior" value="PARTIAL"/>
//发现未知列时的策略默认为NONE不做提醒
  <setting name="autoMappingUnknownColumnBehavior" value="NONE"/>
//配置默认的执行器默认SIMPLE
  <setting name="defaultExecutorType" value="SIMPLE"/>
//自动超时时间
  <setting name="defaultStatementTimeout" value="25"/>
  
//允许在嵌套语句中使用分页,默认False 
  <setting name="safeRowBoundsEnabled" value="false"/>
 
//是否开启驼峰命名映射默认False
  <setting name="mapUnderscoreToCamelCase" value="false"/>
//本地缓存策略默认SESSION
  <setting name="localCacheScope" value="SESSION"/>
//没有为参数提供JDBC类型时，的默认类型，一般为OTHER
  <setting name="jdbcTypeForNull" value="OTHER"/>
 
//定义触发延时加载的方法：逗号分隔
  <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
</settings>
```



#### `appllication.yml`中直接配置Mybatis

```yml
#mybatis
mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.xj.demo.pojo
  configuration:　　# 开启驼峰uName自动映射到u_name
    map-underscore-to-camel-case: true
```