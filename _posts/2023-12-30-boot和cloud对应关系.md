https://www.cnblogs.com/guanghe/p/14700381.html

# SpringBoot版本

```
　　Spring Boot 2.2.5.RELEASE` 表示 `主版本.次版本.增量版本（Bug修复）
```

- 主要 - 版本中的第一个数字。2 和 3 是 Python 的著名 主要版本。主要部分是基于日历的最常见组件。
- 次要 - 版本中的第二个数字。7 是 Python 的最受欢迎的 次要版本。
- 微小 - 版本中的第三个且通常是最终数字。有时 称为 “补丁” 部分。
- 修饰符 - 可选的文本标记，例如 “dev”、“alpha”、“beta”、 “rc1”，依此类推。

绝大多数现代版本标识符是由两个或 三个数字段组成，以及可选的修饰符。通常 建议不要使用四个数字段的版本。

- Alpha：不建议使用，主要是以实现软件功能为主，通常只在软件开发者内部交流，Bug较多；
- Beta：该版本相对于α版已有了很大的改进，消除了严重的错误，但还是存在着一些缺陷，需要经过多次测试来进一步消除；
- GA：General Availability，正式版本，官方推荐使用此版本，在国外都是用GA来说明release版本；
- M：又叫里程碑版本，表示该版本较之前版本有功能上的重大更新；
- PRE(不建议使用)：预览版，内部测试版，主要是给开发人员和测试人员测试和找BUG用的；
- Release：最终版本，Release不会以单词形式出现在软件封面上，取而代之的是符号®；
- RC：该版本已经相当成熟了，基本上不存在导致错误的BUG，与即将发行的正式版相差无几；
- SNAPSHOT：快照版，可以稳定使用，且仍在继续改进版本。
- SR.X：修正版，服务版本，当项目发布积累到一定程度，需要修复该版本中的某个错误后以此来命名，X表示数字。

# SpringCloud版本

　　Spring Cloud 为了要管理每个版本的子项目清单，避免版本名与子项目的发布号混淆，所以没有采用版本号的方式，而是通过命名的方式。这些版本名字采用了伦敦地铁站的名字，根据字母表的顺序来对应版本时间顺序，比如：Angel，Brixton，不过现在已经改为按日期为格式 CalVer ，例如：2020.0.2 CURRENT GA

　　项目发现了不止一种有用的方法在版本中使用日期。 作为对比，CalVer 并未像 “语义化版本” 那样选择单一方案， 而是引入了开发人员的 标准术语：

- `YYYY` - 年份全称 - 2006、2016、2106
- `YY` - 年份缩写 - 6、16、106
- `0Y` - 以零填充的年份 - 06、16、106
- `MM` - 月份缩写 - 1、2 … 11、12
- `0M` - 以零填充的月份 - 01、02 … 11、12
- `WW` - 星期（自年初开始）- 1、2、33、52
- `0W` - 以零填充的星期 - 01、02、33、52
- `DD` - 日 - 1、2 … 30、31
- `0D` - 以零填充的日 - 01、02 … 30、31

　　传统的递增版本号是从 0 开始， 而日期段是从 1 开始的，且年份缩写和以零填充的年份 是相对于 2000 年。还请注意，星期的使用 通常与月/日互斥。

# SpringBoot与SpringCloud版本对应关系

官方查询：

　　通过打开 [https://start.spring.io/actuator/info ](https://start.spring.io/actuator/info)可以得到一串json，其中对应关系一目了然。

　　或在 https://spring.io/projects/spring-cloud#learn 的对应版本号后，点击 Reference Doc. 。能看到很明显的 Supported Boot Version 字样。

# 为什么要版本对应

　　因为版本不对应，项目直接启动失败：

![img](https://img2020.cnblogs.com/blog/955092/202104/955092-20210425151118419-1581111302.png)







# Springcloud-alibaba版本对应关系

https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88%E6%9C%AC%E8%AF%B4%E6%98%8E

可以直接从github上看到写的很详细

| Spring Cloud Alibaba Version                              | Sentinel Version | Nacos Version | RocketMQ Version | Dubbo Version | Seata Version |
| --------------------------------------------------------- | ---------------- | ------------- | ---------------- | ------------- | ------------- |
| 2.2.7.RELEASE*                                            | 1.8.1            | 2.0.3         | 4.6.1            | 2.7.13        | 1.3.0         |
| 2.2.6.RELEASE                                             | 1.8.1            | 1.4.2         | 4.4.0            | 2.7.8         | 1.3.0         |
| 2021.1 or 2.2.5.RELEASE or 2.1.4.RELEASE or 2.0.4.RELEASE | 1.8.0            | 1.4.1         | 4.4.0            | 2.7.8         | 1.3.0         |
| 2.2.3.RELEASE or 2.1.3.RELEASE or 2.0.3.RELEASE           | 1.8.0            | 1.3.3         | 4.4.0            | 2.7.8         | 1.3.0         |
| 2.2.1.RELEASE or 2.1.2.RELEASE or 2.0.2.RELEASE           | 1.7.1            | 1.2.1         | 4.4.0            | 2.7.6         | 1.2.0         |
| 2.2.0.RELEASE                                             | 1.7.1            | 1.1.4         | 4.4.0            | 2.7.4.1       | 1.0.0         |
| 2.1.1.RELEASE or 2.0.1.RELEASE or 1.5.1.RELEASE           | 1.7.0            | 1.1.4         | 4.4.0            | 2.7.3         | 0.9.0         |
| 2.1.0.RELEASE or 2.0.0.RELEASE or 1.5.0.RELEASE           | 1.6.3            | 1.1.1         | 4.4.0            | 2.7.3         | 0.7.1         |

## 毕业版本依赖关系(推荐使用)

下表为按时间顺序发布的 Spring Cloud Alibaba 以及对应的适配 Spring Cloud 和 Spring Boot 版本关系（由于 Spring Cloud 版本命名有调整，所以对应的 Spring Cloud Alibaba 版本号也做了对应变化）

| Spring Cloud Alibaba Version      | Spring Cloud Version        | Spring Boot Version |
| --------------------------------- | --------------------------- | ------------------- |
| 2.2.7.RELEASE                     | Spring Cloud Hoxton.SR12    | 2.3.12.RELEASE      |
| 2021.1                            | Spring Cloud 2020.0.1       | 2.4.2               |
| 2.2.6.RELEASE                     | Spring Cloud Hoxton.SR9     | 2.3.2.RELEASE       |
| 2.1.4.RELEASE                     | Spring Cloud Greenwich.SR6  | 2.1.13.RELEASE      |
| 2.2.1.RELEASE                     | Spring Cloud Hoxton.SR3     | 2.2.5.RELEASE       |
| 2.2.0.RELEASE                     | Spring Cloud Hoxton.RELEASE | 2.2.X.RELEASE       |
| 2.1.2.RELEASE                     | Spring Cloud Greenwich      | 2.1.X.RELEASE       |
| 2.0.4.RELEASE(停止维护，建议升级) | Spring Cloud Finchley       | 2.0.X.RELEASE       |
| 1.5.1.RELEASE(停止维护，建议升级) | Spring Cloud Edgware        | 1.5.X.RELEASE       |

## 依赖管理

Spring Cloud Alibaba BOM 包含了它所使用的所有依赖的版本。

### RELEASE 版本

#### Spring Cloud 2020

如果需要使用 Spring Cloud 2020 版本，请在 dependencyManagement 中添加如下内容

```
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>2021.1</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

#### Spring Cloud Hoxton

如果需要使用 Spring Cloud Hoxton 版本，请在 dependencyManagement 中添加如下内容

```
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>2.2.7.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

#### Spring Cloud Greenwich

如果需要使用 Spring Cloud Greenwich 版本，请在 dependencyManagement 中添加如下内容

```
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>2.1.4.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

#### Spring Cloud Finchley

如果需要使用 Spring Cloud Finchley 版本，请在 dependencyManagement 中添加如下内容

```
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>2.0.4.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

#### Spring Cloud Edgware

如果需要使用 Spring Cloud Edgware 版本，请在 dependencyManagement 中添加如下内容

```java
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>1.5.1.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```