### 什么是MapStruct

MapStruct 是一个代码生成器，它基于约定优于配置方法，极大地简化了Java bean类型之间映射的实现。生成的映射代码使用简单的方法调用，因此速度快、类型安全且易于理解。

#### 官网地址

https://mapstruct.org/

#### 官网文档地址

https://mapstruct.org/documentation/stable/reference/html/#non-shipped-annotations



### 调用方式

1.spring注入方式：在Mapper中加入 componentModel="spring"
@Mapper(componentModel="spring")
写上之后，可以通过 @autowire 注解来注入这个工具类

2.默认方式
在类中写上CarConverter INSTANCE = Mappers.getMapper(CarConverter.class);
其中 CarConverter 是类名;



### **注解说明**

```
@Mapper 只有在接口加上这个注解， MapStruct 才会去实现该接口
    @Mapper 里有个 componentModel 属性，主要是指定实现类的类型，一般用到两个
    default：默认，可以通过 Mappers.getMapper(Class) 方式获取实例对象
    spring：在接口的实现类上自动添加注解 @Component，可通过 @Autowired 方式注入
@Mapping：属性映射，若源对象属性与目标对象名字一致，会自动映射对应属性
    source：源属性
    target：目标属性
    dateFormat：String 到 Date 日期之间相互转换，通过 SimpleDateFormat，该值为 SimpleDateFormat              的日期格式
    ignore: 忽略这个字段
@Mappings：配置多个@Mapping
@MappingTarget 用于更新已有对象
@InheritConfiguration 用于继承配置

```

@Mapper

`@Mapper` 注解的 `componentModel` 属性，`componentModel` 属性用于指定自动生成的接口实现类的组件类型，这个属性支持四个值：

- default: 这是默认的情况，mapstruct 不使用任何组件类型, 可以通过Mappers.getMapper(Class)方式获取自动生成的实例对象。
- cdi: the generated mapper is an application-scoped CDI bean and can be retrieved via @Inject
- spring: 生成的实现类上面会自动添加一个@Component注解，可以通过Spring的 @Autowired方式进行注入
- jsr330: 生成的实现类上会添加@javax.inject.Named 和@Singleton注解，可以通过 @Inject注解获取











### 类型不一致

```java
  @Mappings({
            @Mapping(target = "createTime", expression = "java(com.java.mmzsblog.util.DateTransform.strToDate(source.getCreateTime()))"),
    })
```

编译时使用了expression中定义的表达式对目标字段 `createTime` 进行了转换；然后你还会发现 `updateTime` 字段也被自动从 LocalDateTime 类型转换成了 String 类型



当字段类型不一致时，以下的类型之间是 `mapstruct` 自动进行类型转换的:

- 1、基本类型及其他们对应的包装类型。
  此时 `mapstruct` 会自动进行拆装箱。不需要人为的处理
- 2、基本类型的包装类型和string类型之间

除此之外的类型转换我们可以通过定义表达式来进行指定转换

- 基本数据类型及其包装类与String之间互相转换时可自动转换，其他转换需手动标注
- 基本数据类型及其包装类之间互相转换时可自动转换，其他转换需要手动标注
- **若实体类中新增或删除字段后，MapStruct不会自动重新编译生成类，需手动删除后再次编译**

### 字段名不一致

```
  @Mappings({
            @Mapping(source = "id", target = "userId"),
            @Mapping(source = "name", target = "userName")
    })
```

当字段名不一致时，通过使用 `@Mappings` 注解指定对应关系，编译后即可实现对应字段的赋值

### 属性是枚举类型

实体类我们还是改用 `UserEnum`:

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class UserEnum {
    private Integer id;
    private String name;
    private UserTypeEnum userTypeEnum;
}
```

被映射对象 `UserVO5` 改为：

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class UserVO5 {
    private Integer id;
    private String name;
    private String type;
}
```

枚举对象是

```java
@Getter
@AllArgsConstructor
public enum UserTypeEnum {
    Java("000", "Java开发工程师"),
    DB("001", "数据库管理员"),
    LINUX("002", "Linux运维员");
    
    private String value;
    private String title;

}
```

那么我们定义的接口还是照常定义，不会受到它是枚举就有所变化

```java
   @Mapping(source = "userTypeEnum", target = "type")
    UserVO5 toConvertVO5(UserEnum source);

    UserEnum fromConvertEntity5(UserVO5 userVO5);
```

很明显， `mapstruct` 通过枚举类型的内容，帮我们把枚举类型转换成字符串，并给type赋值，可谓是小心使得万年船啊。看来这巧媳妇儿不仅仅优秀还心细啊……



### 多级嵌套



### 示例

- 同名同属性字段转换

  ```
  //无需特别声明 直接转换即可
  Target convertToTarget(Source source) 
  ```

- 不同名同属性字段转换

  ```
  //需要使用@Mapping注解指定转换字段的名称
  //解释：source中的number转换为tagert中的myNumber
  @Mapping(source = "number", target = "myNumber")
  Target differentNameConvert(Source source);
  ```

  

- 默认值的使用

  ```
  //需使用@Mapping中的defaultValue字段进行标识
  //解释：若age字段为null，则使用defaultValue的值
  @Mapping(source = "age", target = "age", defaultValue = "34")
  Target defaultValueConvert(Source source);
  ```

  

- 常量的使用

  ```
  //需使用@Mapping中的constant字段进行标识
  //解释：不论target中的oldNumber转换值是什么，最后都使用constant的值
  @Mapping(target = "oldNumber", constant = "35")
  Target constantConvert(Source source);
  ```

  

- 忽略某个字段转换

  ```
  //需使用@Mapping中的ignore字段进行标识
  //解释：忽略target中的name字段转换 不论source中name的值是什么 最后tagert中的name值都是null
  @Mapping(target = "name", ignore = true)
  Target ignoreConvert(Source source);
  ```

  

- 单独参数转换

  ```
  //需标明单独参数转换后的字段名称
  //解释：将传入的sex字段传递给target中的sex
  @Mapping(source = "sex", target = "sex")
  Target aloneParmConvert(Integer sex);
  ```

  

- 更新数据源

  ```
  //需使用@MappingTarget字段标明
  //解释：将SonTarget中的值更新给Target 要改变谁 在谁前面加@MappingTarget
  void updateTargetConvert(@MappingTarget Target target, SonTarget sonTarget);
  ```

  

- 自定义数据类型转换

  ```
  default Target myConvert(Source source) {
      // some mapping logic
  }
  ```

  

- 多数据源转换

  ```
  //需使用@Mapping标明转换时参数传递的规则
  //解释：将source中的ignore字段值传递到target的ignore	将sourceTwo的id字段值传递到target中的id
  @Mappings({
        	@Mapping(source = "source.ignore", target = "ignore"),
        	@Mapping(source = "sourceTwo.id", target = "id")
  })
  SonTarget multiSourceConvert(Source source, SourceTwo sourceTwo);
  ```

  

- 表达式的使用

  ```
  //需使用@Mapping中的expression字段标识所使用的表达式
  //解释：引用Java表达式中DateTuil中的date方法处理date的转换逻辑	全路径
  @Mapping(target = "stringDate", expression = "java(cn.hutool.core.date.DateUtil.date(date))")
  SonTarget expressionConvert(Date date);
        
  //解释：可在接口上面的@Mapper中引入某个类，这样就可以直接写方法，不用写全路径
  @Mapper(imports = {DateUtil.class})
  @Mapping(target = "date", expression = "java(DateUtil.date(date))")
  SonTarget expressionConvert(Date date);
  ```

  

- 子父类转换

  ```
  //需使用@BeanMapping指定返回的类型
  //解释：SonTarget是Target的子类，该方法用父类接受，返回子类属性
  @BeanMapping(resultType = SonTarget.class)
  Target childenConvert(Source source);
  ```

  

- 逆向转换

  ```
  //需使用InheritInverseConfiguration标识，该注解是根据源和目标的类型去自动识别，若存在多个相同源转换目标的方法时，需指定逆向转换的方法
  //解释：将differentNameConvert方法进行逆向转换
  //优点：若原differentNameConvert方法上有很多@Mapping规则的话，无需再次书写即可使用
  @InheritInverseConfiguration(name = "differentNameConvert")
  Source convertToSource(Target target);
  ```

  

- dateFormat的使用

  ```
  //需使用@mapping中的dateFormat注解进行标识转换的形式
  //解释：source.date字段转换为target中的stringDate，转换格式为dateFormat指定的格式
  @Mappings({
        @Mapping(source = "source.date", target = "stringDate", dateFormat = "yyyy-MM-dd HH:mm:ss"),  //Date转换成String
        @Mapping(source = "source.stringDate", target = "date", dateFormat = "yyyy-MM-dd HH:mm")  //String转换成Date
  })
  Target childenConvert(Source source);
  ```

  

- numberFormat的使用

  ```
  //解释：String转数字类型后若想指定数字类型的格式可使用numberFormat指定  数字格式化numberFormat，保留两位小数
  @Mapping(target = "number", numberFormat = "#.00")
  Target differentTypeConvert(Source source);
  ```

  

- @BeforeMapping的使用

  ```
  //解释：在转换之前对某个实体进行操作
  @BeforeMapping
  default void beforeConvert(Source source){
      // some mapping logic
  }
  ```

- @BeanMapping

  ```
  ignoreByDefault:避免不需要的赋值、避免属性覆盖
  
  	 /**
       * @Description:忽略mapstruct默认映射行为，只为配置@Mapping映射值
       */
      @BeanMapping(ignoreByDefault = true)
      @Mapping(source = "id",target = "id")
      PopupContentVo es2ToVo(PopupContentES es);
  ```

  

- @AfterMapping的使用

  ```java
  //解释：在转换之后对某个实体进行操作
  @AfterMapping
  default void afterConvert(Source source){
      // some mapping logic
  }
  
  例如
  @AfterMapping
  default void convertBuyed(Person person, @MappingTarget PersonDto personDto) {
  if (person.getBuyed() != null && person.getBuyed() == 1) {
  personDto.setBuyStatus("已购");
  }
  }
  
  
  ```

  

- 集合转换-无特殊要求

  ```
  //解释：直接定义转换方法即可，MapStruct会自动生成Source转换成Target的方法，然后循环去调用
  List<Target> convertToTargets(List<Source> sources);
  ```

  

- 集合转换-需自定义规则

  ```
  //解释：自定义Source转换Target规则，MapStruct会自动调用sourceConvertToTarget方法来替代他自己生成的方法
  //此时自定义规则才会生效
  List<Target> convertToTargets(List<Source> sources);
  
  @Mapping(source = "name", target = "name2")
  Target sourceConvertToTarget(Source source);
  ```

  

- 集合转换-需自定义规则(存在多个自定义规则)

  ```
  //解释：存在多个转换方法时，MapStruct无法自动选择，此时需使用@IterableMapping与@Named联合指定
  //qualifiedByName指定调用的转换规则，@Named标明被调用的名称，这里的两个名称需一致
  @IterableMapping(qualifiedByName = "sourceTwoConvertToTarget")
  List<Target> convertToTargets(List<SourceTwo> sourceTwos);
  
  @Mapping(source = "name", target = "name2")
  @Named("sourceTwoConvertToTarget")
  Target sourceTwoConvertToTarget(SourceTwo sourceTwo);
  
  Target sourceTwoConvertToTarge2(SourceTwo sourceTwo);
  ```

  



### 常见问题

No property named “XXX“ exists in source parameter(s). Did you mean “null“?
根本原因：看一下项目的/classes里面，看对应的类是不是编译生成了，或者说类是有的，但是里面没有geter/setter等方法（主要是Lombok的版本原因）。
情况一、可能是idea版本的问题：idea 2018 之前的版本需要添加下面的配置，后期的版本就不需要了，我自己用的2019.3，不需要这个依赖也能跑 ，如果加上，一定要注意这个依赖的版本，需要和 org.mapstruct:mapstruct 包的版本完全一致，否则报错

		<dependency>
	      <groupid>org.mapstruct</groupid>
	      <artifactid>mapstruct-processor</artifactid>
	      <version>${org.mapstruct.version}</version>
	 			<!--这里我用的版本号 ：1.4.2.Final -->
	      <scope>provided</scope>
	</dependency>

情况二、可能是没写maven插件: 在biuld里面加上（注意这里maven-compiler-plugin的版本要在3.6以上），最好和下面的版本一致，否则可能仍然会报错

```
<build>
    <plugins>
        <plugin>
            <groupid>org.apache.maven.plugins</groupid>
            <artifactid>maven-compiler-plugin</artifactid>
            <version>3.8.1</version>
            <configuration>
                <source>1.8
                <target>1.8</target>
                <annotationprocessorpaths>
                    <path>
                        <groupid>org.mapstruct</groupid>
                        <artifactid>mapstruct-processor</artifactid>
                        <version>${org.mapstruct.version}</version>
                      	<!--这里我用的版本号 ：1.4.2.Final -->
                    </path>
                  	<path>
                        <groupid>org.projectlombok</groupid>
                        <artifactid>lombok</artifactid>
                        <version>1.18.12</version>
                    </path>
                </annotationprocessorpaths>
            </configuration>
        </plugin>
    </plugins>
</build>
```

Couldn’t retrieve @Mapper annotation
情况一、包冲突：项目中使用了swagger，swagger里面也包含mapstruct，排除掉就好

```
<dependency>
    <groupid>io.springfox</groupid>
    <artifactid>springfox-swagger2</artifactid>
    <version>${swagger2.version}</version>
    <scope>compile</scope>
    <exclusions>
        <exclusion>
          <groupid>org.mapstruct</groupid>
          <artifactid>mapstruct</artifactid>
        </exclusion>
    </exclusions>
</dependency>
```

情况二、导入mapstruct项目时，同时使用了 mapstruct-jdk8 和 mapstruct-processor 但是版本不一致，将版本号改为一样即可。

```
<dependency>
    <groupid>org.mapstruct</groupid>
    <artifactid>mapstruct-jdk8</artifactid>
    <version>1.2.0.Final</version>
</dependency>
<dependency>
    <groupid>org.mapstruct</groupid>
    <artifactid>mapstruct-processor</artifactid>
    <version>1.2.0.Final</version>
</dependency>

```

idea启动报错，java: Internal error in the mapping processor: java.lang.NullPointerException at org.ma…
解决方法：
1.设置File→settings→Compiler的User-local build process VM options (overrides Shared options)选项设置
-Djps.track.ap.dependencies=false



### **idea安装插件**

MapStruct Support







### 性能

常用的几种方案做下性能上的对比，这样更能看出MapsStruts的效率，具体测试代码如下

```java
User user =  new User(1L,"小王",15800001111L,25,1,"中国北京朝阳");
long timeTaken;
long time1 = System.currentTimeMillis();
UserDTO userDTO;
for (int i = 0;i<500000;i++){
    userDTO = new UserDTO();
    org.apache.commons.beanutils.BeanUtils.copyProperties(userDTO,user);
}
long time2 = System.currentTimeMillis();
timeTaken =time2-time1;

System.out.println("Apache Commons BeanUtils:"+timeTaken+"(ms)");

long time3 = System.currentTimeMillis();

for (int i = 0;i<500000;i++){
    userDTO = new UserDTO();
    BeanUtils.copyProperties(user,userDTO);
}
long time4 = System.currentTimeMillis();

timeTaken =time4-time3;
System.out.println("Spring BeanUtils:"+timeTaken+"(ms)");

long time5 = System.currentTimeMillis();
for (int i = 0;i<500000;i++){
    UserDTO userDTO1 = UserConvert.INSTANCE.convert(user);
}
long time6 = System.currentTimeMillis();
timeTaken =time6-time5;

System.out.println("MapStruct:"+timeTaken+"(ms)");

long time7 = System.currentTimeMillis();
for (int i = 0;i<500000;i++){
    UserDTO userDTO1 =copyField(user);
}
long time8 = System.currentTimeMillis();
timeTaken =time8-time7;

System.out.println("Getter/Setter:"+timeTaken+"(ms)");
```



执行结果：

```
Apache Commons BeanUtils:5767(ms)
Spring BeanUtils:1102(ms)
MapStruct:14(ms)
Getter/Setter:14(ms)
```













### 测试用例

可以用springboot 的Junit做单元测试，也可以直接用main函数（前提是不涉及到spring的特性）

```java
public class DemoApp {
    
    public static void main(String[] args) {
        CreateAccountDTO createAccountDTO = new CreateAccountDTO();
        List<Account> accounts = new ArrayList<>();
        accounts.add(new Account("ACT2021051200000001", 1000));
        accounts.add(new Account("ACT2021051200000002", 5000));
        createAccountDTO.setKey("326D1478E3914C8A");
        createAccountDTO.setAccounts(accounts);

        User user = new User("Jaemon", Lists.newArrayList("SZ", "NS"));
		
        // 执行1
//        CreateAccountVO createAccountVO = AccountConvertUtils.INSTANCE.convert(createAccountDTO);
        // 执行2
//        CreateAccountVO createAccountVO = AccountConvertUtils.INSTANCE.convertWithMapping(createAccountDTO);
        // 执行3
        CreateAccountVO createAccountVO = AccountConvertUtils.INSTANCE.convert(user, createAccountDTO);

        createAccountDTO.getAccounts().add(new Account("ACT2021051200000003", 1010));

        System.out.println(createAccountDTO.toString());
        System.out.println(createAccountVO.toString());
    }
}

```

























### 参考文档

mapstruct使用详解 https://www.cnblogs.com/mmzs/p/12735212.html#_labelTop



Java中的MapStruct用法详解 https://www.jb51.net/article/243411.htm



BeanUtils.copyProperties() 对比 mapstruct,你知道这两种是什么拷贝吗

https://blog.csdn.net/qq_43578385/article/details/112669246



MapStruct最全使用教材---避坑大全

https://www.pudn.com/news/62d7d6bf55398e076bace9a0.html#%E9%9B%86%E5%90%88%E8%BD%AC%E6%8D%A2-%E9%9C%80%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%84%E5%88%99



使用MapStruct 解决对象之间转换、深拷贝问题

https://blog.csdn.net/u010979642/article/details/116715936

MapStruct---多级嵌套结构的例子

https://blog.csdn.net/m0_48805880/article/details/121418321