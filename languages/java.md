# Java
## Contents
* [Reference](#Reference)
* [The Java Language Specification](#thejavalanguagespecification)


## Reference
* Java书籍经典Top10： http://developer.51cto.com/art/200906/129320.htm
* *.java(source file) -->javac--> *.class(binary file，one class one file) -->jar cf xxx.jar XXX*.class -> *.jar([packed file)
* 在命令行使用 java 命令执行 java 程序时，应该在顶级包所对应的目录下执行。
* Apache Ant
	* http://ant.apache.org/
	* Apache Ant is a Java library and command-line tool that help building software.
	* Apache Ant is a Java library and command-line tool whose mission is to drive processes described in build files as targets and extension points dependent upon each other. The main known usage of Ant is the build of Java applications. Ant supplies a number of built-in tasks allowing to compile, assemble, test and run Java applications. Ant can also be used effectively to build non Java applications, for instance C or C++ applications. More generally, Ant can be used to pilot any type of process which can be described in terms of targets and tasks.
	* Ant is written in Java. Users of Ant can develop their own "antlibs" containing Ant tasks and types, and are offered a large number of ready-made commercial or open-source "antlibs".
	* Ant is extremely flexible and does not impose coding conventions or directory layouts to the Java projects which adopt it as a build tool.
* Apache Ivy
	* http://ant.apache.org/ivy/index.html
	* Apache Ivy is a very powerful dependency manager oriented toward Java dependency management, although it can be used to manage dependencies of any kind.
* Maven
	* http://maven.apache.org/
	* Apache Maven is a software project management and comprehension tool. Based on the concept of a project object model (POM), Maven can manage a project's build, reporting and documentation from a central piece of information.
	* 设置conf/settings.xml:
		* localRepository: 
			* The path to the local repository maven will use to store artifacts.  
			* Default: ${user.home}/.m2/repository.   
			* 本地的repository目录按照Dependency的groupid + artifactid + version，然后以点号作分隔，来建立文件夹的。
			* jar包的名字： artifactId + "-" + version + ".jar"。
	* Maven常用命令： 
		* 创建Maven的普通java项目： mvn archetype:create -DgroupId=packageName -DartifactId=projectName  
		* 创建Maven的Web项目： mvn archetype:create -DgroupId=packageName -DartifactId=webappName -DarchetypeArtifactId=maven-archetype-webapp    
		* 编译源代码： mvn compile
		* 编译测试代码：mvn test-compile
		* 运行测试：mvn test   
		* 产生site：mvn site
		* 打包：mvn package
		* 在本地Repository中安装jar：mvn install
		* 清除产生的项目：mvn clean
		* 生成eclipse项目：mvn eclipse:eclipse, 注意，需确保定义Classpath Variables: M2_REPO，指向本地maven类库目录${user.home}/.m2/repository。
		* 生成idea项目：mvn idea:idea
		* 组合使用goal命令，如只打包不测试：mvn -DskipTests package
		* 编译测试的内容：mvn test-compile
		* 只打jar包: mvn jar:jar
		* 只测试而不编译，也不测试编译：mvn test -skipping compile -skipping test-compile  ( -skipping 的灵活运用，当然也可以用于其他组合命令)  
		* 清除eclipse的一些系统设置:mvn eclipse:clean
		* ps：一般使用情况是这样，首先通过cvs或svn下载代码到本机，然后执行mvn eclipse:eclipse生成ecllipse项目文件，然后导入到eclipse就行了；修改代码后执行mvn compile或mvn test检验，也可以下载eclipse的maven插件。
		* Compile, package, and install all binary and source artifacts to your local repository, without running any tests: mvn -DskipTests source:jar-no-fork install
* PDE: Plug-in Development Toolkit
* JDT: Java Development Toolkit
* CDT: C Development Tookkit
* SWT: Standard Widget Toolkit
* *.jar.sha1 -> *.jar的校验文件
* RandomizedTesting: 
	* 一个 JUnit 测试的扩展，封装了内置重复随机的测试和超时控制、参数工厂、线程执行组等等。
	* import com.carrotsearch.randomizedtesting.generators.RandomStrings
	* http://labs.carrotsearch.com/randomizedtesting.html
* morfologik
	* import morfologik.stemming.*;
	* http://morfologik.blogspot.com/
* javax.servlet_3.0.0.v201112011016.jar
	* import javax.servlet.RequestDispatcher;
	import javax.servlet.ServletConfig;
	import javax.servlet.ServletException;
	import javax.servlet.http.HttpServlet;
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;
* Nekohtml
	* import org.cyberneko.html.parsers.SAXParser;
	* http://nekohtml.sourceforge.net/
* Spatial4j
	* https://github.com/spatial4j/spatial4j
* JMX（Java Management Extensions，即Java管理扩展）是一个为应用程序、设备、系统等植入管理功能的框架。JMX可以跨越一系列异构操作系统平台、系统体系结构和网络传输协议，灵活的开发无缝集成的系统、网络和服务管理应用。
* Jetty 是一个开源的servlet容器，它为基于Java的web容器，例如JSP和servlet提供运行环境。Jetty是使用Java语言编写的，它的API以一组JAR包的形式发布。开发人员可以将Jetty容器实例化成一个对象，可以迅速为一些独立运行（stand-alone）的Java应用提供网络和web连接。  => 可以代替Tomcat
* Eclipse Java设置：
	* 设置classpath： eclipse: 右键选中 Project, Properties, Java Build Path, Libraries, Add External Class Folder。
	* META-INF的作用
	* *.java 中的 public class 的名字要和 .java 的文件名字相同。
	* Eclipse Java 中的 Package Name 并设定成 Source Folder 下的子目录名。
	* 查看当前快捷键设置： Help --> Key Assist...
* JDK src
	* 大多数Java API（常用类）的源代码（用Java写的），在JDK安装目录下的src.zip中都有，如果想用eclipse看，建议如下设置：
		* 在菜单Window-Preferences中，左边选Java-Installed JREs，右边选当前打勾的那个，然后Edit，在弹出的窗口里，JRE System Libraries中选.../rt.jar（如果是JDK，请选tools.jar），然后点source attachment的按钮，指定你JDK目录下那个src.zip。
		* 之后，在任意Java项目中，打开项目目录的JRE System Library/rt.jar，双击所有java或javax开头的包的类，都可看到源代码。
* Java版本：
	* 标准版（J2SE） 
		* 主要用于桌面应用软件的编程；
		* J2SE 包含那些构成Java语言核心的类。比如：数据库连接、接口定义、输入/输出、网络编程。
	* 企业版（J2EE） 
		* Java2的企业版，主要用于分布式的网络程序的开发，如电子商务网站和ERP系统。
		* J2EE 包含J2SE 中的类，并且还包含用于开发企业级应用的类，比如：EJB、servlet、JSP、XML、事务控制。
	* 微缩版（J2ME） 
		* 主要应用于嵌入式系统开发，如手机和PDA的编程；
		* J2ME 包含J2SE中一部分类，用于消费类电子产品的软件开发。比如：呼机、智能卡、手机、PDA、机顶盒。
* Java虚拟机、JRE、JDK的关系：
	* jdk1.8.0_25， jdk-8u25， jre-8u25
	* Java SE Development Kit (JDK) and Java SE Runtime Environment (JRE).
	* Java虚拟机（Java Virtual Machine 简称JVM）
	* jvm包含在jre中，jre包含在jdk中。
	* JDK中包含Java编译器与JRE，JRE中包含JVM与Java运行支持文件。
	* eclipse、idea等其他IDE有自己的编译器而不是用JDK bin目录中自带的，所以在安装时你会发现他们只要求你选中jre路径就ok了。
	* java -version
		java version "1.8.0_251"
		Java(TM) SE Runtime Environment (build 1.8.0_251-b08)
		Java HotSpot(TM) Client VM (build 25.251-b08, mixed mode, sharing)
		----------------------------------------------------
		java version "1.8.0_251"
		Java(TM) SE Runtime Environment (build 1.8.0_251-b08)
		Java HotSpot(TM) 64-Bit Server VM (build 25.251-b08, mixed mode)
	
	## The Java Language Specification
	* 类型：
		* 基本类型（Primitive Type）：
			* 数值类型：
				* 整型：有 byte、short、int 和 long，它们的值分别是8位、16位、32位和64位带符号位的二进制补码整数（signed two's-complement integers）；有 char，它的值是16位无符号整数，表示UTF-16代码单元。
					* 范围：
						* byte: -128 ~ 127
						* short: -32768 ~ 32767
						* int: -2147483648 ~ 2147483647
						* long: -9223372036854775808 ~ 9223372036854775807
						* char: '\u0000' ~ '\uffff', 即从 0 ~ 65535
				* 浮点型：有 float，它的值包括32位 IEEE 754浮点数；有 double，它的值包括64位 IEEE 754浮点数。
					* 范围：
						* float: 1.4E-45(1.4*10^-45 = 0.00...014) ~ 3.4028235E38(3.4028235*10^38 = 340282350...0)（java.lang.Float.MIN_VALUE ~ java.lang.Float.MAX_VALUE）
						* double: 
			* boolean类型：
				* 有true和false两个值。
		* 引用类型（Reference Type）：
			* 类类型、接口类型、数组类型
	* 类、接口
		* public interface XXX { ...... }
		* interface IfA; class CB;  => interface IfB extends IfA;  或  class CA implements IfA;  或  class CC extends CB;
		* 如果类缺少 public 修饰符，则对类声明的访问仅限于在其中声明类的包。
		* 字段可以被声明为 final，在这种情况下，它们只能被赋值一次。任何字段声明都可以 包括一条初始化语句。
	* 对象：
		1. code: { Value v1 = new Value();  v1.val = 5;  Value v2 = v1;  v2.val = 6; }   => v1.val == v2.val  <= 由于v1.val 和 v2.val 引用由唯一的 new 表达式创建的一个 Value 对象中相同的 实例变量。
		2. 每个对象都有一个关联的锁，同步化方法和同步化语句使用这个锁来控制多个线程对状态的并发访问。
		3. 每个对象都属于某个特殊的类：创建表达式中提及的用于生成对象的类、其Class对象用于调用自反方法以生成对象的类，或者用于通过字符串串接运算符+隐式创建对象的String对象。
	* 变量：
		* a class variable: 在类声明中使用关键字 static 声明的字段，或者在接口声明中使用或不使用关键字 static 声明的字段。当准备好类或接口时，就会创建类变量并将其初始化为一个默认值。当卸载类或接口时，就会有效地使类变量停止存在。
		* a instance variable: 在类声明中不使用关键字 static 声明的字段。
	* Object 类
		* Object 类是所有其他类的超类。Object 类型的变量可以存储一个指向空引用或任何对象的引用，无论该对象是类的实例还是一个数组。所有的类和数组类型都继承Object 类的方法。
	* String 类
		* String 类的实例表示 Unicode 字符序列。
	* 参数化类型
		* // Vector<int> -- illegal, primitive types cannot be arguments
	* 装箱和拆箱转换
		* 装箱转换把基本类型的值转换成相应的引用类型的值。boolean、byte、char、short、int、long、float、double -> Boolean、Byte、Character、Short、Integer、Long、Float、Double。
		* 拆箱转换把引用类型的值转换成相应的基本类型的值。
	* 访问控制：
		* protected，能在同一个包里被访问，能在不同包但是是它的子类中被访问。
		* private，只能在同一个类里被范围。
		* 默认，只能在同一个包里（即包内部）被访问。
	* 备注：
		* import 声明，允许使用简单名称引用来自其他包的类型以及类型的静态成员。  按需导入类声明： import xxx.*;
		* Java代码的声明和实现都在同一个.java文件中实现，这与一般的C++代码分为.h和.cpp文件不同。Boost库的代码在编写是使用了.hpp文件（.h + .cpp）。
		* System.getProperties().list(System.out);   // java 获取当前应用程序和系统的属性特征，比如路径、用户名...
	* Java 编码风格与命名规范：
		* 包命名
			* 包名按照域名的范围从大到小逐步列出，恰好和Internet上的域名命名规则相反。
			* 由一组以“.”连接的标识符构成，通常第一个标识符为符合网络域名的两个或者三个英文小写字母，例如：com，org，net，...，后接公司名/组织机构名，再接项目名。
		* 类，接口命名
			* 类的名字必须由大写字母开头而单词中的其他字母均为小写;如果类名称由多个单词组成，则每个单词的首字母均应为大写例如TestPage;如果类名称中包含单词缩写，则这个所写词的每个字母均应大写，如：XMLExample,还有一点命名技巧就是由于类是设计用来代表对象的，所以在命名类时应尽量选择名词。
		* 方法名
			* 方法的名字的第一个单词应以小写字母作为开头，后面的单词则用大写字母开头。可以为动词或动词+名词组合。
				* 设置/获取某个值的Method，应该遵循setV/getV规范
				* 返回长度的Method，应该命名为length
				* 测试某个布尔值的Method，应该命名为isV
				* 将对象转换为某个特定类型的Mehod应该命名为toF
				* getDate(); length(); isReady(); toOracleFormat();
		* 变量名
			1. 普通变量命名应该采用首字母小写，其他字母首字母大写的方式。
			2. final static变量的名字应该都大写，并且指出完整含义。如果一个常量名称由多个单词组成，则应该用下划线来分割这些单词。如：NUM_DAYS_IN_WEEK MAX_VALU
			3. 如果需要对变量名进行缩写时，一定要注意整个代码中缩写规则的一致性。如：context=ctx message=msg
			4. 通过在结尾处放置一个量词，就可创建更加统一的变量。如：First(一组变量中的第一个) Last(一组变量中的最后一个) Next(一组变量中的下一个变量) Prev(一组变量中的上一个) Cur(一组变量中的当前变量)
			5. 无论什么时候，均提倡应用常量取代数字、固定字符串。也就是说，程序中除0，1以外，尽量不应该出现其他数字。
			6. 索引变量：i、j、k等只作为小型循环的循环索引变量。
			7. 逻辑变量：避免用flag来命名状态变量，用is来命名逻辑变量。如： if(isClosed){ dosomeworks; return; }
		* 数组
			* 总是使用以下方式定义数组。如： int[] arr = new int[10];
			* 禁止使用C语言的是形式。如： 禁止 int arr[] = new int[10];
		* 集合
			* 数组或者容器推荐命名方式为名词+s的方式，例如：List persons = getPerson(); for(Person person : persons){ dosomeworks; }
		* 泛型
			* 应该尽量简明扼要(最好是一个字母)，以利于与普通的class或interface区分。
				* Container中的Element应该用E表示；Map里的key用K表示，value用V；Type用T表示；异常用X表示。
			* 如果需要接收多个Type类型的参数，应该用邻接T的大写字母——例如S——来依次表示，当然也可以用T1, T2这样的方式。
				* public class HashSet extends AbstractSet {…} public class HashMap extends AbstractMap {…} public class ThreadLocal {…} public interface Functor { T val() throws X; }
		* 推荐的命名
			1. 当要区别接口和实现类的时候，可以在类的后面加上“Impl”。
			interface Container class ContainerImpl
			2. Exception类最好能用“Exception”做为类命名的结尾。
			DataNotFoundException InvalidArgumentException
			3. 抽象类最好能用“Abstract”做为类命名的开头。
			AbstractBeanDefinition AbstractBeanFactory
			4. Test类最好能用“Test”做为类命名的结尾。
			ContainerTest
			5. 简称与缩写(不推荐使用)。
			cp代表colorPoint buf代表buffer off代表offset len代表length
			6. 除非是在循环中，否则一般不推荐使用单个字母作为变量名，不过也有例外，即约定俗成的单个字母。
			b代表byte c代表char d代表double e代表Exception f代表float i, j, k代表整数 l代表long o代表Object s代表String v代表某些类型的特定值
		* 代码风格
			* 花括号
				* 花括号统一采用以下格式：if(bool experssion){ dosomework; }
				* 除非花括号中为空，不然任何情况下不能省略花括号，并且花括号必须换行，例如：if(i==0){ return; } while(true) {}
				* 以下写法禁止出现：禁止 if(i != 0) return; 禁止 if(i !=0) {return;}
			* 括号
				* 括号的前，后一个字符不需要空格，例如：Person p = new Person(“Jack”， 17);
			* 空格
				1. 逗号之后紧跟一个空格。Person p = new Person(“Jack”， 16, “China”);
				2. 二元操作符前后跟空格。 int i = a + b – c * d;
				3. 一元操作符不需要空格，for语句分号后有空格。 for(int i = 0; I < 10; i++){ dosomework; }
				4. 括号前后不需要空格
			* 类
				* 类的定义结构按照顺序为：
					1. 常量
					2. 成员变量
					3. 构造函数
					4. 成员函数
					5. get和set方法
				* 各个部分之间留出一个空行。
				* 例如——规范类模板：
				class Person{ private final static int MAX_AGE = 100; private String firstname = “Jack”; public Person(){} public Person(String firstname){ this.firstname = firstname; } public void doExercise(){ dosomeworks; run(); } private void run(){ dosomeworks; } public getFirstname(){ return firstname; } public setFirstname(String firstname){ this.firstname = firstname; } }
				* 构造函数
					1. 参数为空的构造函数出现在最上方
					2. 有调用关系的构造函数相邻
					3. 参数尽量由少到多从上至下排序
					4. 使用成员变量
					在类的方法内引用成员变量了命名冲突以外，不使用this。非特殊情况在类的方法内都不使用get和set方法存取成员变量。
				* 方法
					* 有调用关系的方法尽量放在相邻的位置，public和private方法可以交叉放置。
					* get和set方法，所有需要公开的成员变量都要符合良好的javabean规范，提供get和set方法，尽量使用IDE工具自动生成。
			* Javadoc注释
				* 在每个程序的最开始部分，一般都用Javadoc注释对程序的总体描述以及版权信息，之后在主程序中可以为每个类、接口、方法、字段添加 Javadoc注释，每个注释的开头部分先用一句话概括该类、接口、方法、字段所完成的功能，这句话应单独占据一行以突出其概括作用，在这句话后面可以跟随更加详细的描述段落。在描述性段落之后还可以跟随一些以Javadoc注释标签开头的特殊段落，例如上面例子中的@auther和@version，这些段落将在生成文档中以特定方式显示。

