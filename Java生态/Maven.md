## Maven

## 安装配置

maven项目寻找jar包的顺序

1. 在本地仓库当中寻找jar包
2. 如果本地仓库中没有则在中央仓库当中寻找jar包，假如有maven私服的话就现在maven的私服当中寻找jar包，没有的话最后到中央仓库当中寻找jar包



设置本地仓库路径

找到`conf/settings.xml`文件

在其中的`<settings>`标签下加入`<localRespository>本地仓库路径</localRespository>`标签



配置中央仓库路径



阿里云maven仓库

在`<mirrors>`标签下插入

```xml
 <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>        
</mirror>
```



在eclipse当中配置 maven



perferences->Maven->Installations->add自己安装的maven并且勾选

perferences->Maven->User Settings->选择自己安装的maven的settings.xml文件



