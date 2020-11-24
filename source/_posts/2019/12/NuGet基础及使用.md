---
title: NuGet基础及使用
date: 2019-12-20 20:31:18
categories: .NET技术
tags:
  - NuGet
  - 包管理工具
---

# NuGet简介
学一个新内容应从3个方面入手：是什么(what)、能做什么(why)、怎么做(how)，我是直接参考 [官方文档](https://docs.microsoft.com/zh-cn/nuget/) 着手细化学习。

## 什么是Nuget
首先给自己解答下什么是Nuget？  看下官方文档怎么说的：
>适用于任何现代开发平台的基本工具可充当一种机制，通过这种机制，开发人员可以创建、共享和使用有用的代码。 通常，此类代码捆绑到“包”中，其中包含编译的代码（如 DLL）以及在使用这些包的项目中所需的其他内容。
对于 .NET（包括 .NET Core），共享代码的 Microsoft 支持的机制则为 NuGet ，其定义如何创建、托管和使用面向 .NET 的包，并针对每个角色提供适用工具。

一脸懵逼？ 熟悉Java或者Node.js的话应该知道Maven或npm，而对应.Net平台的支持机制就是NuGet，他实际是一个包管理器。
通俗来讲，NuGet就是一个维护dll的仓库管理器，通过他可以简化dll的版本维护、解决dll之间的依赖关系，来方便dll的发布及项目使用。
<!--more-->

在深入细化理解下，NuGet包含3块概念：NuGet包、NuGet仓库和NuGet工具

### NuGet包
官方的说法如下：
>简单来说，NuGet包是具有`.nupkg`扩展的单个 ZIP 文件，此扩展包含编译代码 (Dll)、与该代码相关的其他文件以及描述性清单（包含包版本号等信息）。

我们可以将研发的公共组件（Dll）通过nuget进行对外发布，但发布之前需要将dll打包成`.nupkg`文件，而`.nupkg`实际只是一个 ZIP 文件，里面包含了Dll文件、清单描述（版本号等）、其他文件（ReadMe.md）等。

### NuGet仓库
打包完成的`.nupkg`文件不会像原来发布dll一样存放到git或svn中，而是存放到一台单独的Nuget管理服务器上，由NuGet统一管理包文件，这台服务器便是NuGet仓库。
Nuget仓库可以微软提供的nuget.org共有仓库，也支持可以由组织和个人创建的私有仓库。

![nuget](https://docs.microsoft.com/zh-cn/nuget/media/nuget-roles.png)

### NuGet工具
Nuget工具提供了我们打包、上传、下载Nuget包等功能，有多套客户端工具：
1、dotnet CLI（适用于 .NET Core 和 .NET Standard 库）
2、nuget.exe CLI（适用于 NET Framework 库）
3、Visual Studio工具（控制台、UI）

## NuGet能做什么

### 简化DLL引用和还原
DLL不会再存放到源代码管理存储库以减少代码仓库的大小，并且可以简化DLL引用和更新的操作，减少这人工部分的成本。
项目不再直接添加dll的引用，而是通过项目引用表来自动引用，该列表有两种“包管理格式”（可以简单理解为.NET CORE 和 .NET Framework）：
- PackageReference：(NuGet 4.0+) 维护直接位于项目文件中的项目顶层依赖项的列表，因此无需单独文件。
- packages.config：(NuGet 1.0+) 一种XML文件，用于维护项目中所有依赖项的简单列表，包括其他已安装包的依赖项 。

### 管理DLL依赖关系
不知道大家有没有遇到过，项目引用了一个DLL，但是由于这个DLL依赖了其他DLL导致了系统问题，为了简化处理有些人会选择将SVN中存放DLL文件夹下所有DLL都引用到项目中，导致项目很大。
但是有Nuget，这方面的问题完全不需要人为去干预了，他会自动管理依赖关系自动引用所需的dll。

## 怎么用Nuget
1、通过Visual Studio UI工具查找需要引用的Nuget包，执行安装dll引用，如果要切换dll版本也可以直接在UI工具中使用，非常人性化（用Maven就是一个XML，难道包名、版本号等都要求背下来的吗，可怕）。
2、如果通过UI工具在项目引用了Nuget，Visual Studio会自动帮我们更新PackageReference或packages.config，其他人更新到源码会通过`项目引用表`自动下载引用Nuget包。
3、如果你的工程生成的dll是需要对外发布的，则需要通过项目文件创建`.nuspec`文件，并通过Nuget读取这份文件进行打包`.nupkg`文件，最后发布到仓库中。

# 基础使用
## 安装客户端工具
 - dotnet.exe：适用于 .NET Core 和 .NET Standard 库，包含在 [.NET Core SDK](https://dotnet.microsoft.com/download) 中，并在所有平台上提供核心 NuGet 功能。
 - nuget.exe：适用于 .NET Framework 库，提供 Windows 上的所有 NuGet 功能以及 Mac 和 Linux 上在 Mono 下运行时的大多数功能。
 - Visual Studio：通过包管理器 UI 和包管理器控制台提供 NuGet 功能。


 两个NuGet CLI工具是的选择，若要详细比较功能可以参阅 [功能可用性](https://docs.microsoft.com/zh-cn/nuget/install-nuget-client-tools#feature-availability) ：
 （1）若要面向 .NET Core 或 .NET Standard，请使用 dotnet CLI。 dotnet CLI 是 SDK 样式项目格式所必需的，该格式使用 SDK 属性。
 （2）要面向 .NET Framework（仅限非 SDK 样式项目），请使用 nuget.exe CLI。 如果项目从 packages.config 迁移到 PackageReference，请使用 dotnet CLI。

由于Visual Studio 不会自动包含 nuget.exe CLI，不支持对.Net FrameWork平台进行打包，我们需要去官网上下载[NuGet.exe](https://dist.nuget.org/win-x86-commandline/latest/nuget.exe)。
>注意：NuGet.exe并不是安装包而是执行程序，直接将NuGet.exe放到某个目录下，并配置系统参数Path完成注册。

## Nuget基础操作
通过Visual Studio的UI工具操作使用非常的简便，包括：查询包、安装包、更新包、还原包，详细内容可以直接查阅 [官方文档](https://docs.microsoft.com/zh-cn/nuget/consume-packages/overview-and-workflow)

默认情况下，PackageReference用于.NET Core项目、.NET Standard项目，.NET 框架项目支持PackageReference，但当前默认为packages.config。
就我用下来的感觉，PackageReference相对packages.config最多的一个优势是，他可以将Nuget包中的元数据资产（比如系统的XML配置文件）拷贝到生成目录下，详情请见 [控制依赖项资产](https://docs.microsoft.com/zh-cn/nuget/consume-packages/package-references-in-project-files#controlling-dependency-assets)，所以有这方面需求就切换过去吧。

## 创建Nuget包
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
### 引用元数据资产
使用Nuget打包，contentFiles的用法
1、首先没办法使用Package.json了，需要转换为PackageReference。（.NET FRAMEWORK平台下会有这个问题，.NET CORE本身就是所以没问题）
2、其次，关于File的配置，其target必须是以/contentFiles/{codeLanguage}/{TxM}/{any?}这种形式，不然也会识别不了。
3、注意缓存文件，这个把我坑了很久，手动去删除吧
```bash
nuget delete Com.Epoint.QDHelperPlug 1.0.0 -source MyDevlop.pkgRep
del F:\SoftwareTemp\Microsoft\NuGetTemp\Packages\com.epoint.qdhelperplug
```
4、二进制文件，需要将buildAction="EmbeddedResource"

## 发布Nuget包
将指定的nupkg上传到服务器上，需要注意如果已经上传过了需要先删除掉
```bash
nuget push <package> -Source ServerName

nuget push .\Com.Epoint.QDHelperPlug.1.0.0.nupkg -source MyDevlop.pkgRep
```

# 扩展配置

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

### 配置文件路径备忘
C:\Program Files (x86)\NuGet\Config\Microsoft.VisualStudio.Offline.config 设置缓存文件
C:\Users\<UserName>\AppData\Roaming\NuGet\NuGet.Config 设置Nuget配置

### 增加本地仓库
```bash
nuget sources Add -Name "MyServer" -Source F:\dev\NugetPackages
```

## 搭建私有仓库

### 创建私有服务
使用Visual Studio创建Asp.net Web Application的空白引用（注意是.Net FramWork 4.6+版本），然后通过Nuget来引用NuGet.Server包，工程便搭建完成了。里面注意对ApiKey做下配置。

### 挂载到IIS
直接使用Visual Studio中的发布，将发布的程序挂载到IIS就行了，然后试运行以下不报错就可以。

### 配置本地配置
1、 增加服务器源
```bash
nuget sources Add -Name "MyServer" -Source \\myserver\packages
```

2、配置连接ApiKey
```bash
nuget setApiKey serversapikeyvalue -Source ServerName
```

# 扩展知识点

## 包版本控制
### 版本基础知识
特定版本号的格式为 Major.Minor.Patch[-Suffix] ，其中的组件具有以下含义：
- Major：重大更改
- Minor：新增功能，但可向后兼容
- Patch：仅可向后兼容的 bug 修复
- -Suffix（可选）：连字符后跟字符串

示例：
>1.0.1
6.11.1231
4.3.1-rc
2.2.44-beta1

### 预发布版本
- -alpha：Alpha 版本，通常用于在制品和试验品。
- -beta：Beta 版本，通常指可用于下一计划版本的功能完整的版本，但可能包含已知 bug。
- -rc：候选发布，通常可能为最终（稳定）版本，除非出现重大 bug。

### 版本范围限制
配置`allowedVersions`属性进行版本控制，引用包依赖项时，NuGet 支持使用间隔表示法来指定版本范围，汇总如下：

| Notation  | 应用的规则    | 说明                                     |
|:--------- |:------------- | ---------------------------------------- |
| 1.0       | x ≥ 1.0       | 最低版本（包含）                         |
| (1.0,)    | x > 1.0       | 最低版本（独占）                         |
| [1.0]     | x == 1.0      | 精确的版本匹配                           |
| (,1.0]    | x ≤ 1.0       | 最高版本（包含）                         |
| (,1.0)    | x < 1.0       | 最高版本（独占）                         |
| [1.0,2.0] | 1.0 ≤ x ≤ 2.0 | 精确范围（包含）                         |
| (1.0,2.0) | 1.0 < x < 2.0 | 精确范围（独占）                         |
| [1.0,2.0) | 1.0 ≤ x < 2.0 | 混合了最低版本（包含）和最高版本（独占） |
| (1.0)     | 无效          | 无效                                     |
