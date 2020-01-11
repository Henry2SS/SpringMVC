## SpringMVC学习的第三天

### 一、RequestMapping注解的属性

- 1. path 和 value 都是指路径，只填写一个参数的时候，默认的就是 path 或者 value。
    
    `类型： String`

- 2. method={RequestMethod.POST}
  
    指定方法的一个请求方式吧，默认是 GET。这里可以指定方法的一个请求方式，如 POST等。
    
    比如这里使用了POST方法，然后点击超链接的时候，会出现 `HTTP Status 405 - Method Not Allowed`
     
     `这里的method其实是一个RequestMethod的一个数组。RequestMethod是一个枚举类。所以可以直接用 RequestMethod. 的方式得到里面的值。`

- 3. params 是指参数
  
    如果在 @RequestMapping(path="/hello",params={”username=heihei“})

    在.jsp文件写的超链接中，要在指定的链接的后面跟上 `‘?username=heihei’` 。而且要使得这个key-value对的值都要正确。

    如果使用的值不对的话，那么就会报 `HTTP Status 400 - Bad Request`

- 4. header 参数


### 二、解决 收到POST请求传回来的中文乱码的问题

- 1. 在 web.xml 文件中配置一个过滤器。
  ```
  <!-- 解决中文乱码：配置一个过滤器-->
  <filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
  ```

### 三、自定义类型转换器

- 1. 第一步：自己在 utils 包下写一个类型转换的类，实现 Converter<S,T> 接口，实现里面的方法；举个例子，如果想要一个String转换成Date的类型转换器，那么在方法重写中，就去写
```
public Date convert(String s) {
        if(s == null){
            throw new RuntimeException("请传入数据！");
        }
        DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
        try {
            return df.parse(s);
        } catch (ParseException e) {
            throw new RuntimeException("数据类型转化失败！");
        }
    }
```

- 2. 第二步:在 springmvc.xml 文件中,配置一个类型转换器
  
  ```
  <!-- 配置数据类型转换器      -->
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set >
                <bean class="utils.StringToDateConverter"></bean>
            </set>
        </property>
    </bean>
  ```

  - 3. 第三步:在 springmvc.xml 文件中配置 annotation-driven,也就是把上面配置的bean的id注册起来。
  ```
      <mvc:annotation-driven conversion-service="conversionService"/>
  ```

 - 4. 至此,就可以实现,某些特定的类型之间的相互转换了。