---
title: Maven版本发布
date: 2021-11-21 16:53:14
tags:
---

# 背景
解决分支管理及版本发布，解决人工维护实现自动化发布。

# Maven Release
## 环境搭建
1. 在POM文件中添加SCM
```xml
 <scm>
    <connection>scm:git:ssh://git@github.com/username/reponame.git</connection>
    <developerConnection>scm:git:ssh://git@github.com/username/reponame.git</developerConnection>
</scm>
```

2. 添加插件
```xml
 <!-- 版本发布插件 -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-release-plugin</artifactId>
    <version>3.0.0-M4</version>
    <configuration>
        <autoVersionSubmodules>true</autoVersionSubmodules>
        <tagNameFormat>v@{project.version}</tagNameFormat>
        <!-- 需要研究一下 ReleaseProfile，通过超级POM可以看到如果满足之后，打包会将源码和JavaDoc一起发布 -->
        <!-- 1. 需要尝试下是否启效果 -->
        <!-- 2. 通过-DuseReleaseProfile=false是否能真的关闭 -->
        <arguments>-Pmaster -DuseReleaseProfile=false</arguments>
    </configuration>
</plugin>
```

3. 版本定义
在POM文件中对版本的定义需要以-SNAPSHOT为后缀（比如1.0.0-SNAPSHOT）

4. 父子POM
这些类型在父POM中添加，子POM为具体的JAR。

## 适用方式
1. 通过执行`mvn release:prepare-with-pom`来构件发布环境
2. 通过执行`mvn release:rollback`还原版本
3. 通过执行`mvn release:perform`打包发布
4. 还有一个命令是`mvn release:branch`还没细化研究

step1. 清理下当前工程mvn clean

Step2：基于Release分支执行mvn release:prepare -Pprod -DdryRun=true
```bash
Dependency 'com.epoint.bidoe:Epoint.BidOE.UniverseDomain' is a snapshot (1.3.2-SNAPSHOT)
: Which release version should it be set to? 1.3.2: :
What version should the dependency be reset to for development? 1.3.2: : 1.3.3-SNAPSHOT

```

Step3. 使用最新的Tag执行Jar打包发布到仓库，mvn release:perform -Pprod


## 产品工程
### 需求背景
1. 保障开发环境和发布环境的隔离，互不影响。
2. 保障产品对外发版的一致性、统一管控发版节奏。
3. 统一版本，解决项目依赖的复杂度，比如领域API、评标Frame、评标应用、发版主POM的版本定义。
3. 需要解决发布过程的复杂性、人工成本。
4. 如何解决

### 开发环境
开发环境一律使用（-SNAPSHOT）结尾，默认发布
7.1.51.2-SNAPSHOT、Frame（8.51.0-SNAPSHOT）、System（1.0.0-SNAPSHOT）
Startup发布的问题
（1）修改POM依赖7.1.51.2
（2）打出对应的分支v7.1.51.2

MASTER => 7.1.51.2-SNAPSHOT

### 发布环境
#### 1、版本大发版
发布内容：发版主POM（7.1.51.2）、Frame组件（8.51.0）、System组件（1.0.0）、Startup应用系统。
由开发版本（-SNAPSHOT）转换为正式版本。

#### 2、运维问题修复
方向1：所有版本不变（包括了组件包）。问题需要强更新，规范性也不对。
方向2：POM版本不变，组件包版本变动。同上。
方向3：所有版本变动

#### 3、项目技术支撑

### 执行方式
整体依托于jenkins自动化发布，对几个仓库同步处理，底层技术使用maven release组件。
Step1. release -> marge master  <1.0-SNAPSHOT> 
Step2. release -> mvn release   <TAG 1.0> 
Step3. release -> marge develop <1.1-SNAPSHOT> 


