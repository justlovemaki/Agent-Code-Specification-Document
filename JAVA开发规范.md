2. 代码开发规范文档 (Code Development Specification Document)

本规范旨在提升代码质量、可读性、可维护性，并确保团队协作的一致性。

2.1. 命名风格

【强制】 代码中的命名均不能以 _ 或 $ 开始或结束。

【强制】 严禁使用拼音与英文混合的方式，更不允许直接使用中文。

【强制】 类名使用 UpperCamelCase 风格。

【强制】 方法名、参数名、成员变量、局部变量都统一使用 lowerCamelCase 风格。

【强制】 常量命名全部大写，单词间用 _ 隔开。

【强制】 抽象类命名使用 Abstract 或 Base 开头；异常类命名使用 Exception 结尾；测试类命名以它要测试的类名开始，以 Test 结尾。

【强制】 POJO 类中的任何布尔类型的变量，都不要加 is 前缀。

【强制】 包名统一使用小写，点分隔符之间有且仅有一个自然语义的英语单词。

2.2. 代码格式

【强制】 大括号的使用规则：如果为空，则 {}即可；如果不为空，左大括号前不换行，左大括号后换行；右大括号前换行；右大括号后还有 else 等代码则不换行。

【强制】 if/for/while/switch/do 等保留字与括号之间都必须加空格。

【强制】 任何二目、三目运算符的左右两边都需要加一个空格。

【强制】 采用 4 个空格缩进，禁止使用 Tab 字符。

【强制】 单行字符数限制不超过 120 个，超出需要换行。

【强制】 IDE 的 text file encoding 设置为 UTF-8。

2.3. OOP 规约

【强制】 避免通过一个类的对象引用访问此类的静态变量或静态方法，直接用类名访问。

【强制】 所有的覆写方法，必须加 @Override 注解。

【强制】 Object 的 equals 方法容易抛空指针异常，应使用常量或确定有值的对象来调用 equals。

推荐: 使用 java.util.Objects#equals(Object a, Object b)。

【强制】 所有整型包装类对象之间值的比较，全部使用 equals 方法比较。

【强制】 浮点数之间的等值判断，基本数据类型不能用 ==，包装类不能用 equals。

说明: 应指定一个误差范围，并在范围内比较；或使用 BigDecimal 进行运算。

【强制】 禁止使用构造方法 BigDecimal(double) 的方式把 double 值转化为 BigDecimal 对象。

正例: new BigDecimal("0.1") 或 BigDecimal.valueOf(0.1)。

【强制】 POJO 类属性必须使用包装数据类型；RPC 方法的返回值和参数必须使用包装数据类型；所有局部变量推荐使用基本数据类型。

【强制】 序列化类新增属性时，请不要修改 serialVersionUID 字段。

【强制】 构造方法里面禁止加入任何业务逻辑，如有初始化逻辑，请放在 init 方法中。

2.4. 集合处理

【强制】 只要覆写 equals，就必须覆写 hashCode。

【强制】 判断所有集合内部的元素是否为空，使用 isEmpty() 方法，而不是 size()==0 的方式。

【强制】 使用 stream.Collectors.toMap() 时，必须使用 (v1, v2) -> v2 等方式指定 key 重复时的处理策略，否则会抛 IllegalStateException。

【强制】 ArrayList 的 subList 结果不可强转成 ArrayList，否则会抛出 ClassCastException。

【强制】 使用集合转数组的方法，必须使用集合的 toArray(T[] array)，传入的是类型完全一致、长度为 0 的空数组。

【强制】 不要在 foreach 循环里进行元素的 remove/add 操作。remove 元素请使用 Iterator 方式。

2.5. 并发处理

【强制】 获取单例对象需要保证线程安全，其中的方法也要保证线程安全。

【强制】 线程资源必须通过线程池提供，不允许在应用中自行显式创建线程。

【强制】 线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor 的方式。

说明: Executors 返回的线程池对象的弊端在于：FixedThreadPool 和 SingleThreadPool 允许的请求队列长度为 Integer.MAX_VALUE，可能堆积大量请求，从而导致 OOM。CachedThreadPool 允许的创建线程数量为 Integer.MAX_VALUE，可能会创建大量的线程，从而导致 OOM。

【强制】 SimpleDateFormat 是线程不安全的类，一般不要定义为 static 变量，如果定义为 static，必须加锁，或者使用 DateUtils 工具类。

推荐: 使用 JDK8 的 DateTimeFormatter。

【强制】 必须回收自定义的 ThreadLocal 变量，尤其在线程池场景下，使用 try-finally 块进行回收。

3. 文档与注释规范文档 (Documentation & Commenting Specification Document)

本规范旨在提高代码的可理解性，降低维护成本。

【强制】 类、类属性、类方法的注释必须使用 Javadoc 规范，使用 /** 内容 */ 格式，不得使用 // xxx 方式。

【强制】 所有的抽象方法（包括接口中的方法）必须要用 Javadoc 注释、除了返回值、参数异常说明外，还必须指出该方法做什么事情，实现什么功能。

【强制】 所有的类都必须添加创建者和创建日期。

正例:

code
Java
download
content_copy
expand_less
/**
 * @author your_name
 * @date yyyy/MM/dd
 */

【强制】 方法内部单行注释，在被注释语句上方另起一行，使用 // 注释。方法内部多行注释使用 /* */ 注释，注意与代码对齐。

【强制】 所有的枚举类型字段必须要有注释，说明每个数据项的用途。

【推荐】 代码修改的同时，注释也要进行相应的修改，尤其是参数、返回值、异常、核心逻辑等。

【参考】 好的命名、代码结构是自解释的，注释力求精简准确、表达到位。避免过度注释。

【参考】 特殊注释标记，请注明标记人与标记时间，并及时处理。

TODO: (标记人, 标记时间, [预计处理时间]) 表示需要实现，但目前还未实现的功能。

FIXME: (标记人, 标记时间, [预计处理时间]) 表示代码是错误的，需要及时纠正。

4. 项目结构规范文档 (Project Structure Specification Document)

本规范旨在定义清晰的应用分层、依赖管理规则，确保项目结构的合理性与可扩展性。

4.1. 应用分层

【推荐】 推荐分层结构如下，默认上层依赖于下层：

终端显示层 (View): 各个端的模板渲染并执行显示的层。

开放接口层 (API): 可直接封装 Service 接口暴露成 RPC 接口；通过 Web 封装成 http 接口等。

Web 层 (Controller): 对访问控制进行转发，各类基本参数校验，或者不复用的业务简单处理等。

Service 层: 相对具体的业务逻辑服务层。

Manager 层: 通用业务处理层。负责对第三方平台封装、对 Service 层通用能力的下沉、对 DAO 的组合复用。

DAO 层 (Data Access Object): 数据访问层，与底层 MySQL、Oracle 等进行数据交互。

【参考】 分层异常处理规约：

DAO 层: 发生异常，直接 throw new DAOException(e)，不需要打印日志。

Service/Manager 层: 必须捕获异常并记录日志到磁盘。

Web 层: 绝不应该继续往上抛异常，应直接跳转到友好错误页面。

API 层: 要将异常处理成错误码和错误信息方式返回。

4.2. 二方库依赖

【强制】 定义 GAV 遵从以下规则:

GroupId 格式: com.{公司/BU}.业务线.[子业务线]，最多 4 级。

ArtifactId 格式: 产品线名-模块名。

Version 格式: 主版本号.次版本号.修订号。

【强制】 线上应用不要依赖 SNAPSHOT 版本 (安全包除外)。

【强制】 依赖于一个二方库群时，必须定义一个统一的版本变量，避免版本号不一致。

【强制】 禁止在子项目的 pom 依赖中出现相同的 GroupId，相同的 ArtifactId，但是不同的 Version。

【推荐】 所有 pom 文件中的依赖声明放在 <dependencies> 语句块中，所有版本仲裁放在 <dependencyManagement> 语句块中。

5. 系统安全规范文档 (System Security Specification Document)

本规范旨在从源头防止常见的安全漏洞，保障系统和用户数据的安全。

【强制】 隶属于用户个人的页面或者功能必须进行权限控制校验。

【强制】 用户敏感数据禁止直接展示，必须对展示数据进行脱敏。

正例: 手机号：139****1219。

【强制】 用户输入的 SQL 参数严格使用参数绑定或者 METADATA 字段值限定，防止 SQL 注入，禁止字符串拼接 SQL 访问数据库。

【强制】 用户请求传入的任何参数必须做有效性验证。

说明: 忽略参数校验可能导致：页面 page size 过大导致内存溢出、恶意 order by 导致数据库慢查询、SSRF、任意重定向、SQL 注入等。

【强制】 禁止向 HTML 页面输出未经安全过滤或未正确转义的用户数据，以防止 XSS 攻击。

【强制】 表单、AJAX 提交必须执行 CSRF 安全验证。

【强制】 在使用平台资源，譬如短信、邮件、电话、下单、支付，必须实现正确的防重放机制，如数量限制、疲劳度控制、验证码校验。

【强制】 对于文件上传功能，需要对于文件大小、类型进行严格检查和控制。

【强制】 配置文件中的密码需要加密。