---
title: NuGet基础及使用
date: 2019-12-20 20:31:18
categories: .NET技术
tags:
  - NuGet
  - 包管理工具
---

## NuGet简介
要学一个新东西，应从3个方面入手：是什么(what)、能做什么(why)、怎么做(how)，可以直接看 [官方文档](https://docs.microsoft.com/zh-cn/nuget/) 来入手细化学习的。

### 什么是Nuget
NuGet实际是一个包管理器，相当于Java的Maven、node的npm。通俗来讲，NuGet就是一个dll的仓库管理工具，可以维护dll的版本，解决dll之间的依赖关系，方便dll的发布及项目引用。
<!--more-->

NuGet包含3块概念：NuGet包、NuGet仓库和NuGet工具，下面逐一进行说明。

#### NuGet包
官方的说法如下：
>简单来说，NuGet包是具有`.nupkg`扩展的单个 ZIP 文件，此扩展包含编译代码 (Dll)、与该代码相关的其他文件以及描述性清单（包含包版本号等信息）。

通俗的来讲，我们可以将研发的公共组件（Dll），通过nuget打包成`.nupkg`文件进行发布，而`.nupkg`里面包含了编译代码（Dll）、描述性清单（版本号等）、其他文件（xxx.xml（api文件）、ReadMe.md）、依赖关系等。
打包完成的`.nupkg`文件

#### NuGet仓库

![nuget](https://docs.microsoft.com/zh-cn/nuget/media/nuget-roles.png)

### NuGet能做什么
比如我们工程引用了别的部门发布的dll组件，期间我们发现他们发布的新版本存在严重问题，这个情况我们可以降低版本，并告知他们进行修复。等他们修复完成之后，可能会把dll发布到svn上或者别的平台，我们手动去下载，替换工程中的dll引用。有了NuGet，只需要在工程中修改下版本号，就完成了上述操作。当然好处不只这么一点，比如dll引用存在依赖项时，NuGet自动帮我们处理掉，简化整个环境搭建的过程。

### 怎么用NuGet
命令工具（nuget Cli）、UI工具（Visual Studio）


## 操作使用
### Nuget包应用及下载
我用的Visual Studio 2017 提供程序包管理器 UI 和程序包管理器控制台，所以可以直接在项目中引用NuGet包，通过UI程序可以使操作非常简单，详细内容可以直接参考MSDN[
https://docs.microsoft.com/zh-cn/nuget/consume-packages/install-use-packages-visual-studio]。

### Nuget包创建及发布
Nuget适用于所有.NET项目（.NET Standard、.NET Core、.NET FrameWork），项目格式（SDK 样式或非 SDK 样式）决定了使用和创建 NuGet 包所需的一些工具和方法。
| Header One     | Header Two     |
| :------------- | :------------- |
| Item One       | Item Two       |
对于NetCore，dotnet CLI


## 准备环境
但是Visual Studio 不会自动包含 nuget.exe CLI，必须按照前面所述单独安装，所以不支持对.Net FrameWork平台进行打包，我们需要去官网上下载[NuGet.exe][https://dist.nuget.org/win-x86-commandline/latest/nuget.exe]。
>注意：NuGet.exe并不是安装包而是执行程序，直接将NuGet.exe放到某个目录下，并配置系统参数Path完成注册。



## 打包
### 创建.nuspec
打开控制台程序，直接到应用程序.csproj所在的文件夹下，执行下述命令，可以自动创建.nuspec文件
```bash
nuget spec [<package-name>]
```
如果忽略 `<package-name>`，生成的文件是 `Package.nuspec`。 如果提供 `Contoso.Utility.UsefulStuff` 等名称，则文件是 `Contoso.Utility.UsefulStuff.nuspec`。

### 配置.nuspec文件
重点是Version、User、Owner，其他依赖关系Package.config可以自动关联起来。

### 执行打包
使用以下命令来执行
```bash
nuget pack   #执行文件打包
nuget pack -Prop Configuration=Release   #默认是打包Debug版本，通过此设置打包Release版本
```

使用Nuget打包，contentFiles的用法
1、首先没办法使用Package.json了，需要转换为PackageReference。（.NET FRAMEWORK平台下会有这个问题，.NET CORE本身就是所以没问题）
2、其次，关于File的配置，其target必须是以/contentFiles/{codeLanguage}/{TxM}/{any?}这种形式，不会也会识别不了。
3、注意缓存文件，这个把我坑了很久，手动去删除吧
```bash
nuget delete Com.Epoint.QDHelperPlug 1.0.0 -source MyDevlop.pkgRep
del F:\SoftwareTemp\Microsoft\NuGetTemp\Packages\com.epoint.qdhelperplug
```
4、二进制文件，需要将buildAction="EmbeddedResource"

## 搭建本地仓库
配置NuGet.Config文件，我的路径是`用户目录+AppData\Roaming\NuGet\NuGet.Config`。

```xml
<config>
  <add key="repositorypath" value="F:\rep\NuGetPackages" />
  <add key="defaultPushSource" value="F:\dev\NugetPackages" />
</config>

<!--设置全局缓存-->
 <add key="globalPackagesFolder" value="F:\SoftwareTemp\Microsoft\NuGetTemp\Packages" />
```

C:\Program Files (x86)\NuGet\Config\Microsoft.VisualStudio.Offline.config 设置缓存文件
C:\Users\<UserName>\AppData\Roaming\NuGet\NuGet.Config 设置Nuget配置

增加本地仓库
```bash
nuget sources Add -Name "MyServer" -Source F:\dev\NugetPackages
```

## 搭建私有仓库

### 创建私有服务
使用Visual Studio创建Asp.net Web Application的空白引用（注意是.Net FramWork 4.6+版本），然后通过Nuget来引用NuGet.Server包，工程便搭建完成了。里面注意对ApiKey做下配置。

### 挂载IIS
直接使用Visual Studio中的发布，将发布的程序挂载到IIS就行了，然后试运行以下不报错就可以。

### 配置本地配置

1. 增加服务器源
```bash
nuget sources Add -Name "MyServer" -Source \\myserver\packages
```

2. 配置连接ApiKey
```bash
nuget setApiKey serversapikeyvalue -Source ServerName
```

### 发布包
将指定的nupkg上传到服务器上，需要注意如果已经上传过了需要先删除掉
```bash
nuget push <package> -Source ServerName

nuget push .\Com.Epoint.QDHelperPlug.1.0.0.nupkg -source MyDevlop.pkgRep
```
