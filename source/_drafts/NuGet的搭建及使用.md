---
title: NuGet的搭建及使用
tags: NuGet
categories: .NET学习
date: 2019-04-18 20:33:48
---


## NuGet简介
什么是NuGet，熟悉JAVA的人应该知道Maven，而NuGet则是.Net平台的“Maven”。
通俗来讲，NuGet就是一个管理dll的仓库，维护dll的版本。比如我们程序引用了别的部门发布的dll，但是我们发现dll存在问题，于是告知他们进行修复。等他们修复完成之后，可能会把dll发布到svn上或者别的平台，我们手动去下载，替换工程中的dll引用。有了NuGet，只需要在工程中修改下版本号，就完成了上述操作。当然好处不只这么一点，比如dll引用存在依赖项时，NuGet自动帮我们处理掉，简化整个环境搭建的过程。
https://docs.microsoft.com/zh-cn/nuget/

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

<!--more-->

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
