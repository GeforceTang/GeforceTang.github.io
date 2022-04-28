---
title: 前端工程化工具（npm）
date: 2022-04-01 14:15:01
tags:
---

## NPM

NPM 的全称是 Node Package Manager，随 NodeJS 一起安装的包管理工具，方便让 JavaScript 开发者下载、安装、上传以及管理已经安装的包。

### 版本号

在学习工具使用之前先掌握下基础知识。包管理器不多说，懂 nuget（c#）、maven（java）的都懂。
版本号分为 3 个部分：MajorVersion.MinorVersion.RevisionNumber

- 主版本号（MajorVersion）：属于大版本升级，一般版本之间存在较大差异，前后不兼容等。

- 次版本号（MinorVersion）：属于正常的版本升级，迭代新功能等。
- 修正版本号（RevisionNumber）：属于补丁版本，对现有的问题做修正。

#### 版本标识符（~、^、\*）

- `~`：会匹配最近的小版本依赖包。比如`~1.2.3`会匹配所有`1.2.x`版本，但是不包括`1.3.0`
- `^`：会匹配最新的大版本依赖包。比如`^1.2.3`会匹配所有`1.x.x`的包，包括`1.3.0`，但是不包括`2.0.0`
- 无前缀：指定版本号。比如`1.2.3`指定特定的版本
- `*`：安装最新版本的依赖包。可能会造成版本不兼容，**慎用**

### 基本命令

`npm -v`：查看版本号

`npm config list`：查看配置信息。比如`npm`源等。

- 通过`npm config set registry = "https://registry.npm.taobao.org/"`设置阿里镜像。
- 通过`npm config set cache D:\.Caches\DevReps\npm\.cache`设置缓存位置
- 通过`npm config set prefix D:\.Caches\DevReps\npm\.npm`设置包全局安装位置

`npm init`：初始化 package.json，参数`-y`跳过输入自动生成。

`npm install`：根据 package.json 的配置安装插件（缩写`npm i`）。

- 参数`--global(-g)`：全局安装
- 参数`--save(-S)`：安装包信息保持到 package.json 的 dependencies（生产阶段依赖）
- 参数`--save-dev(-D)`：安装包信息保存到 package.json 的 devDependencies（开发环境依赖）
- 参数`--save-optional(-O)`：安装包信息将保存到 package.json 的 optionalDependencies（可选的依赖）
- 参数`--save-exact(-E)`：精确安装指定模块版本。比如`npm install gulp -ES`

`npm update`：更新。参数类似`npm install`。不带包名表示`package.json`里面的所有依赖更新。

`npm uninstall`：卸载。参数类似`npm install`。带包名表示卸载`package.json`里面的所有依赖。

`npm run dev`：执行`dev`命令。`dev`是在`package.json`的`scripts`中配置的命令

### 实用技巧

#### 修改 npm 源

在安装某个包比较慢需要切换 npm 源，但又不想修改默认的源地址，可以使用`--registry`参数。

```bash
# 在npm install XXX时加入--registry URL即可，不会影响到本地配置
npm --registry https://registry.npm.taobao.org install koa
```

#### 管理模块的缓存

```bash
npm cache add <tarball file>
npm cache add <folder>
npm cache add <tarball url>
npm cache add <name>@<version>

npm cache ls [<path>]

# 清除npm本地缓存
npm cache clean [<path>]
```

#### 查看安装的模块

可以看到开发目录和全局的模块及依赖

```bash
npm ls [[<@scope>/]<pkg> ...]
npm ls -g
```

#### 查看包的安装路径

-g 可以查看全局的包暗转路径

```bash
npm root [-g]
```

#### 检查模块是否已经过时

此命令会列出所有已经过时的包，可以及时进行包的更新

```bash
npm outdated [[<@scope>/]<pkg> ...]
```

#### 查看某条命令的详细帮助

```bash
npm help <term> [<terms..>]
```

#### 查看模块的注册信息

```bash
npm view [<@scope>/]<name>[@<version>] [<field>[.<subfield>]...]

# 查看模块的依赖关系
npm view gulp dependencies

# 查看模块的源文件地址
npm view gulp repository.url

# 查看模块的贡献者，包含邮箱地址
npm view npm contributors
```

### npm 命令

#### 启动模块

基础语法

```bash
npm start [-- <args>]
```

该命令写在 package.json 文件 scripts 的 start 字段中，可以自定义命令来配置一个服务器环境和安装一系列的必要程序，如

```json
"scripts": {
    "start": "gulp -ws"
}
```

此时在 cmd 中输入 npm start 命令相当于执行 gulpfile.js 文件自定义的 watch 和 server 命令。

如果 package.json 文件没有设置 start，则将直接启动 node server.js

#### 停止模块

基础语法

```bash
npm stop [-- <args>]
```

#### 重新启动模块

基础语法

```bash
npm restart [-- <args>]
```

#### 测试模块

基础语法

```bash
npm test [-- <args>]
npm tst [-- <args>]
```

该命令写在 package.json 文件 scripts 的 test 字段中，可以自定义该命令来执行一些操作，如

```json
"scripts": {
    "test": "gulp release"
},
```

此时在 cmd 中输入 npm test 命令相当于执行 gulpfile.js 文件自定义的 release 命令。

## YARN

yarn 同 npm，它的出现主要是解决 npm 存在的一些缺陷，

### yarn 准备

#### 安装 yarn

```bash
npm install -g yarn

yarn --version(-v)
```

#### 配置 yarn

```bash
yarn config list		# 查看yarn配置
yarn config set registry https://registry.npm.taobao.org/		#修改yarn的源镜像为淘宝源
yarn config set global-folder "xxx\Yarn\global"		# 具体目录请改成自己的
yarn config set prefix "xxx\Yarn\global\"			# 需要把此目录加到系统环境变量
yarn config set cache-folder "xxx\Yarn\cache"			# 具体目录请改成自己的

# 查看所有配置
yarn config list

# 查看当前yarn的bin的位置
yarn global bin

# 查看当前yarn的全局安装位置
yarn global dir
```

#### 同 npm 命令对比

|                          npm | yarn                    |
| ---------------------------: | :---------------------- |
|                  npm install | yarn                    |
|     npm install react --save | yarn add react          |
|   npm uninstall react --save | yarn remove react       |
| npm install react --save-dev | yarn add react -D/--dev |
|            npm update --save | yarn upgrade            |

## Reference

[1]: https://www.cnblogs.com/PeunZhang/p/5553574.html "【原】npm常用命令详解"
[2]: https://baijiahao.baidu.com/s?id=1726289783347888551&wfr=spider&for=pc "npm常用命令操作"
[3]: https://www.cnblogs.com/longkui-site/p/15856888.html "yarn的安装与使用"
