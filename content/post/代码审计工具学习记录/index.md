---
title: 代码审计工具学习记录
date: 2024-07-26
categories:
    - 学习记录
---

在通读了官方文档后，有一些值得着重注意的点：主要产品都使用什么方法扫描？
## 优势
- It is open source (free).
- Supports many languages (go, python, java, javascript, php, and more).
- There are about 1000 predefined rules you can use “out of the box”.
- Does NOT require buildable source code.
- There is no Domain Specific Language (DSL) to learn, you can make custom patterns to match the code you are targeting.
- Easy to use for any developer, does not require expertise.

## Products 产品分类
Semgrep包含多重代码审计方法，包括SAST，SCA和Secrets。这些方式可以根据用户需要进行组合。此文档着重关注SAST方式。
> 静态应用程序安全测试 SAST (Static Application Security Testing): 通过直接查看应用程序的源代码发现各种安全漏洞，以避免损失。SAST工具和扫描程序基本都是在应用程序代码完全编译之前使用，因此也可以将它们称为“白盒”工具。
> 软件成分分析 SCA (Software Composition Analysis): 

### Semgrep Code
一种静态应用安全测试（SAST）解决方案，除了Semgrep OSS之外，还使用了专有的Semgrep分析，如**跨文件**（文件间）和**跨功能**（文件内）数据流（cross-function or interprocedural analysis）。这使得真阳性率高于Semgrep OSS。
使用方式为在线网站+命令行。
> 真阳性率true positives rate: 检测出来的真阳性样本数除以所有真实阳性样本数。
>> Java 中的强类型与其编译时和运行时检查相结合，降低了利用整数或布尔输入执行注入式攻击的可能性。Semgrep Pro可以通过利用这些检查来减少误报。
>>Semgrep OSS引擎根据模式进行匹配，这可能会导致误报（FPs），但只有专有的Semgrep能够检测布尔值和整数值，并将其标记为未受污染或安全，从而消除误报。

Semgrep 代码使用**Rules**（规则,封装模式匹配逻辑和数据流分析）来扫描代码，以查找安全问题、样式违规、漏洞等。只要发现代码与规则定义的模式相匹配，Semgrep就会生成并向用户报告发现的问题。
> [官方规则注册表及社区](https://semgrep.dev/r)

除了注册表中可用的规则之外，用户还可以编写**自定义规则**，以确定Semgrep代码在软件仓库中检测到的内容。无论是使用已有规则，还是编写自定义规则，了解Semgrep代码运行的规则都有助于了解它是如何检测安全问题的。
Semgrep Code是透明的，用户可以配置其运行的规则，并检查其语法，从而了解发现问题的方式。用户还可以自定义规则的内容，以提高规则的真阳性率，或让 Semgrep 向开发人员发送相关信息。

### Semgrep OSS
Semgrep OSS是一款快速、轻量级的程序分析工具，可帮助检测代码中的安全问题。使用的是 Semgrep 的 LGPL 2.1 开源引擎。
> 注意：在集成方面，Semgrep OSS和Semgrep Code都可用于扫描本地代码，也可集成到 CI/CD 管道中，自动对软件源进行持续扫描。

### Semgrep Secrets
Semgrep Secrets 扫描代码，检测暴露的 API 密钥、密码和其他凭证。一旦暴露，恶意行为者就会利用这些凭据泄露数据或访问敏感系统。Semgrep Secrets 可帮助用户确定:
- 哪些Secrets已经提交到用户的存储库中。
- Secrets的验证状态；例如，有效的Secrets是指那些经过网络服务测试并确认能够成功授予资源或身份验证的Secrets。它们正在使用中。
- 对于 GitHub 存储库：公共或私有存储库中是否存在凭证。
Semgrep通过优先处理有效的泄露Secrets，并在开发人员的PR和MR中直接发布评论，告知开发人员有效的Secrets，从而节省安全工程师的时间和精力。

### Semgrep Supply Chain
Semgrep Supply Chain 是一款软件构成分析（SCA）工具，可检测代码库中由开源依赖关系引入的安全漏洞。它还可以:
- 生成软件物料清单（SBOM），提供完整的开源组件清单
- 查询有关依赖项的信息
- 支持执行公司的开源软件包许可要求
Semgrep Supply Chain（Semgrep供应链管理软件）能够分析锁定文件（lockfile）中的依赖关系，然后根据锁定文件扫描用户的代码库，查找可达到的结果。某些语言（如 Java）有多个受支持的锁文件，这取决于用户的软件包管理器。要使用Semgrep Supply Chain扫描锁文件，该文件必须具有其中一个支持的锁文件名。

## Language support 支持语言

#### Semgrep Code

Maturity level 成熟等级 GA(Parse Rate 99%+): C, C++, C#, Go, Java, JavaScript, Kotlin, Python, TypeScript, Ruby, Rust, JSX, PHP, Scala, Swift, Generic, JSON, Terraform

Maturity level 成熟等级 BETA(Parse Rate 95%+): Apex, Elixir

Maturity level 成熟等级 Experimental(Parse Rate 90%+): Bash, Cairo, Clojure, Dart, Dockerfile, Hack, HTML, Jsonnet, Julia, Lisp, Lua, Ocaml, R, Scheme, Solidity, YAML, XML

#### Semgrep OSS

Bash, C, C++, C#, Cairo, Clojure, Dart, Dockerfile, Generic, Go, Hack, HTML, Java, JavaScript, JSON, Jsonnet, Julia, Lisp, Lua, Kotlin, Ruby, Rust, JSX, OCaml, PHP, Python, R, Scala, Scheme, Solidity, Swift, TypeScript, YAML, XML

#### Semgrep Supply Chain

对于某些语言，如JavaScript和Python，还需要对清单文件（manifest file）进行解析，以确定反式性（transitivity）。

Maturity level 成熟等级 GA(Parse Rate 99%+): C#(NuGet), Go(Go modules), Java(Gradle, Maven), JavaScript or TypeScript(npm, Yarn, Yarn 2, Yarn 3, pnpm), Python(pip, pip-tools, Pipenv, Poetry), Ruby(RubyGems)
Maturity level 成熟等级 Lockfile-only: Rust(Cargo§), Dart(Pub), Elixir(Hex), Kotlin(Gradle, Maven), PHP(Composer), Scala(Maven), Swift(SwiftPM)

## 如何使用Semgrep进行扫描（WSL）
由于我只有Windows设备，而本地的Semgrep及Ocaml都还不支持Windows，于是使用内置WSL服务。安装教程可查阅微软[官方文档](https://learn.microsoft.com/zh-cn/windows/wsl/install)。
安装指令:
``python3 -m pip install semgrep``
登录Semgrep账号:
``semgrep login``
同时可以安装VSCode中的Semgrep官方插件，就可以在VSCode命令行中使用Semgrep扫描指令。


## 核心方法：Run Rules
使用Rules规则进行静态代码分析，检查各种代码问题，包括安全漏洞、常见编程错误、最佳实践、代码风格等。同时，用户也可以根据自己的需求创建和定制规则。
[官方规则编写教程（超详细）](https://semgrep.dev/learn)

### Pattern syntax 模式语法
可以在一条字符串中寻找给定的模式子串相同的所有子串。如Pattern``return 42``可以匹配函数中的顶层语句或任何嵌套语句：
```
def foo(x):
  if x > 1:
     if x > 2:
       return 42
  return 42
```
在命令行中，使用``--pattern``（或``-e``）标记指定patterns。在一个配置文件中可以指定多个coordinating patterns。


## 备注

> CI(Continuous Integration) 持续集成: 是在源代码变更后自动检测、拉取、构建和（在大多数情况下）进行单元测试的过程。持续集成是启动管道的环节（尽管某些预验证 —— 通常称为 上线前检查(pre-flight checks) —— 有时会被归在持续集成之前）。
> 持续集成的目标是快速确保开发人员新提交的变更是好的，并且适合在代码库中进一步使用。
> CD(continuous deployment)持续部署: 持续交付的下一步，指的是代码通过评审以后，自动部署到生产环境。
> 持续部署的目标是，代码在任何时刻都是可部署的，可以进入生产阶段。

> LGPL(Lesser General Public License)开源协议: LGPL 允许商业软件通过类库引用(link)方式使用LGPL类库而不需要开源商业软件的代码。这使得采用LGPL协议的开源代码可以被商业软件作为类库引用并发布和销售。
> 但是如果修改LGPL协议的代码或者衍生，则所有修改的代码，涉及修改部分的额外代码和衍生的代码都必须采用LGPL协议。因此LGPL协议的开源代码很适合作为第三方类库被商业软件引用，但不适合希望以LGPL协议代码为基础，通过修改和衍生的方式做二次开发的商业软件采用。
> GPL/LGPL都保障原作者的知识产权，避免有人利用开源代码复制并开发类似的产品

tbc