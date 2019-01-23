## 一、背景&目标
当前新建项目时，配置文件往往都是从其它项目拷贝过来，拷贝时很容易踩坑，并且不容易定位问题。尤其对新人来说由于在对公司项目结构、基础中间件、配置还不了解的情况下，会浪费很多不必要的时间。故，希望通过脚手架解决此痛点，达到如下几个基本目标：
- 1.统一规范工程项目&代码层次结构——约定大于配置；
- 2.提升工作效率——脚手架&代码生成器&自定义插件；
- 3.降低项目维护成本——易于组内成员相互协防；

另，发现当前各自创建的项目，存在如下几个现象，希望借机推进组内代码规范落地：

- 1.项目&包层次结构不统一；
- 2.配置文件命名、存放位置不统一；
- 3.类命名不统一（有的叫entity，有的叫BO，有的叫Domain；有的叫job，有的叫crane，有的叫schedule；接口命名有的I开头，有的无I开头）；

 希望在项目模板（脚手架）中在每个层次环节代码中直接给予相应的Demo，以此达到规范引导作用。

## 二、脚手架使用方法
主要有如下三种方式（其中2.1方式主要用在本地有脚手架的前提下）：
````
[INFO] Using property: groupId = com.ydj.test
Define value for property 'artifactId': test-mydemo
[INFO] Using property: artifactId = test-mydemo
Define value for property 'version' 1.0-SNAPSHOT: : 
[INFO] Using property: version = 1.0-SNAPSHOT
Define value for property 'package' com.ydj.test: : com.ydj.test.mydemo
````

`建议提示：`不要使用默认包名com.ydj.test，而应com.ydj.test.mydemo

### 2.1、使用本地maven仓库中脚手架创建新项目

直接执行如下maven命令，根据提示依次输入groupId、version、package信息即可：
````
mvn archetype:generate -DarchetypeCatalog=local
````
 
### 2.2、使用发布到公司maven私服的脚手架创建新项目——命令方式
````
mvn archetype:generate -DarchetypeGroupId=com.ydj.test -DarchetypeArtifactId=ydj-probase-archetype -DarchetypeVersion=1.0-SNAPSHOT -DarchetypeCatalog=internal
````
### 2.3、使用发布到公司maven私服的脚手架创建新项目——IDEA界面方式
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190123105247985.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xvdmVKYXZhWURK,size_16,color_FFFFFF,t_70)（和我们平时使用官方的脚手架一样）
   
## 三、脚手架创建过程
### 3.1、抽象简化出一模板项目ydj-probase
（模板骨架项目中保留如pom、spring、log4j等最小配置和最基本示例代码）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190123110608907.png)
### 3.2、生成脚手架
- 在ydj-probase根路径下执行maven命令：
````
mvn archetype:create-from-project
````
 
- 按需编辑archetype-metadata.xml文件（如添加了如appKey非默认属性，则需要同时编辑test/resources/projects/basic/archetype.properties文件）：
````
<requiredProperties>
  <requiredProperty key="artifactId"/>
  <requiredProperty key="groupId">
    <defaultValue>com.ydj.test</defaultValue>
  </requiredProperty>
  <requiredProperty key="version"/>
  <requiredProperty key="package">
    <defaultValue>${groupId}</defaultValue>
  </requiredProperty>
  <requiredProperty key="appKey">
    <defaultValue>${artifactId}</defaultValue>
  </requiredProperty>
</requiredProperties>
````

- 然后 target/generated-sources/archetype 即是根据ydj-probase生成的脚手架。切换到archetype目录下再执行以下maven命令，将刚才创建的骨架，安装到maven本地的repository目录下:
````
mvn install
````

- 在archetype目录下继续执行如下maven命令，让maven能够找到该项目并且知道它是个骨架项目（信息将保存到如/Users/ares/.m2/repository/archetype-catalog.xml文件中）:
````
mvn archetype:crawl
````

### 3.3、发布脚手架到公司私服
- 在target/generated-sources/archetype下pom.xml文件添加如下：
````
    <distributionManagement>
        <repository>
            <id>maven-releases</id>
            <name>maven-releases</name>
            <url>http://mvn.ydj.com/repository/maven-releases/</url>
        </repository>
        <snapshotRepository>
            <id>maven-snapshots</id>
            <name>maven-snapshots</name>
            <url>http://mvn.ydj.com/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
````
- 然后，继续执行如下mave命令发布到公司的maven私服仓库：
````
mvn deploy
````

- 即发布脚手架：
````
脚手架信息：
archetypeGroupId=com.ydj.test
archetypeArtifactId=ydj-probase-archetype
archetypeVersion=1.0-SNAPSHOT
name=ydj-probase-archetype
````
