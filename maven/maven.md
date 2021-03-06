maven有效地帮助我们管理项目的整个生命周期，包括项目清理、编译、构建、测试、打包、安装、发布、生成报告等，并且通过依赖和插件管理，实现开发环境的统一，避免冲突及管理无序现象。

## 生命周期和阶段
maven将工程（Project）的构建过程理解为不同的生命周期(LifeCycle)和阶段（Phase）。 在工程的构建过程中，存在着不同的生命周期，这些生命周期互相独立，之间也没有一定的顺序关系。 每个生命周期又划分为不同的阶段（Phase）。阶段之间有明确的顺序关系， 同一生命周期内的阶段必须按顺序依次执行。

![](../images/project.png)

Maven的核心思想基于生命周期管理。这意味着构建和发布一个项目的步骤是有很清晰的定义的。构建项目的人，只需要知道一小部分构建命令和POM配置，以获得期望结果。
Maven内置3种生命周期：
* Default： 默认生命周期处理项目发布。
* Clean： 负责项目清理。
* Site： 负责项目文档发布。

![](../images/life.png)

### Default

* Validate： 确定项目是否配置正确，必要资源是否可用。
* Compiler： 编译项目源码。
* test： 使用合适的单元测试框架，测试编译结果。测试要求代码被打包或者发布。
* package: 讲代码打包成发行版本，如：jar。
* integration-test： 如果需要在一个综合环境中运行我们的测试，这个阶段将会运行和布署项目到该环境中。
* verify: 运行集成测试检验，确保软件质量达标。
* install： 安装包到本地仓库，作为其他本地项目依赖。
* deploy: 完成所有构建、打包阶段，发布到远程仓库。

### clean

* pre-clean 准备清理。
* clean 执行清理工作。
* post-clean 执行清理后的后续工作。

### site：生成项目文档和站点

* pre-site 准备生成。
* site 生成项目站点和文档。
* post-site 执行生成文档后的后续工作。
* site-deploy 发布项目文档。

## 插件和Goal

![](../images/plugin.png)

Maven中定义的工程周期和阶段只是抽象的概念，不涉及具体的功能。 具体的功能由插件（Plugin）实现。一个插件可以实现多个目标（Goal）。

为了解耦插件的功能和工程阶段，实现高度的可配置性，maven规定插件只是实现目标的功能， 通过配置来决定在哪个阶段执行（Execution)哪些目标操作。 甚至可以把一个Goal绑定到多个Phase，以实现复用。

maven内置了一些默认的插件，并根据不同的工程packing类型在各个phase中默认绑定了一些goal。 下表中列出default生命周期中各阶段默认绑定的goal，其中goal按照管理使用pluginname:goalname的方式标记：

| Pahse     | Plugin:Goal     |
| :------------- | :------------- |
| process-resources       | resources:resources       |
|compile           |	compiler:compile|
|process-test-resources| 	resources:testResources|
|test-compile|compiler:testCompile|
|test|	surefire:test|
|package|ejb:ejb/ejb3:ejb3/jar:jar/par:par/rar:rar/war:war|
|install|install:install|
|deploy|deploy:deploy|

总结下，就是如下的图：

![](../images/sum.png)

## 两个文件
### pom.xml

该文件是maven项目的核心，POM（Project Object Model项目对象模型）包含了一个project所需要的所有信息，独立于实际代码，避免了两者之间互相影响。文件中定义了项目的基本信息，描述项目如何构建，申明项目依赖等。同时，对插件的引用也是在此文件中申明，包括项目构建过程中所需要的插件的配置信息。

### setting.xml

默认文件的位置为\${M2_HOME}/conf/setting.xml,是全局配置，我们通常将它复制到${user.home}/.m2/setting.xml，为当前用户配置。如果两者都存在，以用户范围的settings.xml优先权更高。

## 坐标与依赖

### 坐标元素

一组maven坐标包括的坐标元素有：groupId、artifactId、version、packaging、classifier。

*  groupId——定义当前Maven项目隶属的实际项目。
*  artifactId——定义实际项目中的一个Maven模块。
* version——当前Maven项目的版本。
* packaging——打包方式。
* classifier ——用来帮助定义构建输出的一些附属构件。
举例如下：


    <groupId>org.apache.hadoop</groupId>
      <artifactId>hadoop-project</artifactId>
      <version>2.7.2</version>
      <description>Apache Hadoop Project POM</description>
      <name>Apache Hadoop Project POM</name>
    <packaging>pom</packaging>

### 依赖
在<scope>字段配置依赖的范围，不同的配置分别控制依赖与classpath（编译classpath，测试classpath，运行classpath）之间不同的关系。

* compile: 表示dependency都可以在生命周期中使用。 而且，这些dependencies 会传递到依赖的项目中。适用于所有阶段，会随着项目一起发布。
* provided： 表明了dependency 由JDK或者容器提供，例如Servlet  AP和一些Java EE APIs。这个scope 只能作用在编译和测试时，同时没有传递性。
* runtime: 表示dependency不作用在编译时，但会作用在运行和测试时，如JDBC驱动，适用运行和测试阶段。
* test: 表示dependency作用在测试时，不作用在运行时。  只在测试时使用，用于编译和运行测试代码。不会随项目发布。
* system: 跟provided  相似，但是在系统中要以外部JAR包的形式提供，maven不会在repository查找它。

### 依赖排除

可通过exclusions标签，排除不需要的依赖，通过一下配置即可排除spark中的jersey-server依赖。依赖排除是dependency级别，而非pom文件，也即，只要在一个dependency中排除了该依赖，其他地方调用则无需再配置同样的内容，仅需要groupId和artifactId标签即可。

    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-core_2.10</artifactId>
        <version>${spark.version}</version>
        <exclusions>
            <exclusion>
                <groupId>com.sun.jersey</groupId>
                <artifactId>jersey-server</artifactId>
            </exclusion>
        </exclusions>
    </dependency>


  通过dependencyManagement可以指定我们使用的软件包版本，通常情况下我们仅指定了自己引入的包版本。但实际上，dependencyManagement可以将版本依赖传递到引入包的依赖上面，同时可以统一多个版本。

### 配置标签Properties
Properties 标签下可以有多个自定义名称的property配置，这类配置可以被子项目继承，通常配置在父项目中，用于全局配置或者版本约束。
配置示例：

    <project>
    ...
    <properties>
    <scala.version>2.11.8</scala.version>
    </properties>
    ...
    </project>


### 构建标签build

#### finalName

finalName标签可定义最终打包的jar名称，在maven-jar-plugin插件中，会自动读取该属性，jar打包生成的包名前缀即读取自finalName标签。

#### pluginManagement

插件管理有子标签 plugins，形如插件调用方式。插件管理目的主要有：版本统一，简化通用配置。
通过version标签，可以统一插件版本，并且在调用的时候无需重复指定版本。
通过configuration标签，可进行通用的配置，在调用的时候会自动继承配置属性。

    <pluginManagement>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-dependency-plugin</artifactId>
              <version>${maven-dependency-plugin.version}</version>
            </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-enforcer-plugin</artifactId>
              <version>${maven-enforcer-plugin.version}</version>
              <configuration>
                <rules>
                  <requireMavenVersion>
                    <version>[3.0.2,)</version>
                  </requireMavenVersion>
                  <requireJavaVersion>
                    <version>[1.7,)</version>
                  </requireJavaVersion>
                </rules>
              </configuration>
            </plugin>
          </plugins>
        </pluginManagement>

如果不是第三方插件，Maven官方插件可以仅配置artifactId，分组默认为：org.apache.maven.plugins。
简化版配置方式：

    <plugin>
    <artifactId>maven-antrun-plugin</artifactId>
    <version>1.3</version>
    </plugin>

同样，调用方式也可以仅使用artifactId。如果是通用配置，调用方式则可以进一步简化。

    <plugins>
        <plugin>
            <artifactId>maven-compiler-plugin</artifactId>
        </plugin>
        <plugin>
            <artifactId>maven-dependency-plugin</artifactId>
        </plugin>
        <plugin>
            <artifactId>maven-enforcer-plugin</artifactId>
        </plugin>
    </plugins>

    第三方插件需要指定groupId和artifactId，但同样可以使用pluginManagement来管理，简化配置。

        <build>
        <plugins>
             <!--scala代码编译配置-->
             <plugin>
                  <groupId>net.alchim31.maven</groupId>
                  <artifactId>scala-maven-plugin</artifactId>
             </plugin>
         </plugins>
        </build>

#### 插件配置

##### 通用配置

通常情况下通过可 configuration 标签配置其他属性，而 configuration 子标签配置，都是按照各个插件自定义来配置。如：

    /**
     * @goal query
     * @phase package
     * /
    public class MyBindedQueryMojo extends AbstractMojo
    {
        /**
         * @parameter expression="${query.url}"
         * /
        private String url;

        /**
         * @parameter default-value="60"
         * /
        private int timeout;

        /**
         * @parameter
         * /
        private String[] options;

        public void execute() throws MojoExecutionException
        {
            ...
        }
    }

自定义插件配置如下：

    <project>
      ...
      <build>
        <plugins>
          <plugin>
            <artifactId>maven-myquery-plugin</artifactId>
            <version>1.0</version>
            <executions>
              <execution>
                <id>execution1</id>
                <phase>install</phase>
                <configuration>
                  <url>http://www.bar.com/query</url>
                  <timeout>15</timeout>
                  <options>
                    <option>four</option>
                    <option>five</option>
                    <option>six</option>
                  </options>
                </configuration>
                <goals>
                  <goal>query</goal>
                </goals>
              </execution>
            </executions>
          </plugin>
        </plugins>
      </build>
      ...
    </project>

##### 阶段配置 executions

可以通过executions标签，为不同生命周期阶段的分别作配置。

    <plugin>
        <groupId>net.alchim31.maven</groupId>
        <artifactId>scala-maven-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
            <execution>
                <id>compile-scala</id>
                <phase>compile</phase>
                <goals>
                    <goal>add-source</goal>
                    <goal>compile</goal>
                </goals>
            </execution>
            <execution>
                <id>scala-test-compile</id>
                <goals>
                    <goal>add-source</goal>
                    <goal>testCompile</goal>
                </goals>
            </execution>
            <execution>
                <phase>process-resources</phase>
                <goals>
                    <goal>compile</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <scalaVersion>${scala.version}</scalaVersion>
            <recompileMode>incremental</recompileMode>
            <javacArgs>
                <javacArg>-source</javacArg>
                <javacArg>${java.version}</javacArg>
                <javacArg>-target</javacArg>
                <javacArg>${java.version}</javacArg>
            </javacArgs>
        </configuration>
    </plugin>
