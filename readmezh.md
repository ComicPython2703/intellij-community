# IntelliJ 开源仓库

本仓库是 JetBrains IDE 代码库的开源部分。
它也是 [IntelliJ 平台开发](https://www.jetbrains.com/opensource/idea) 的基础。

这些说明将帮助您构建并运行 IntelliJ 平台 / IntelliJ IDEA / PyCharm 的开源部分。

如果您是社区新手，希望贡献代码或帮助他人学习，请参阅 [CONTRIBUTING.md](https://github.com/JetBrains/intellij-community/blob/master/CONTRIBUTING.md) 开始入门。

以下约定将用于指代您机器上的目录：
* `<USER_HOME>` 是您操作系统用户的主目录。
* `<IDEA_HOME>` 是 **IntelliJ 源代码** 的根目录。

___
## 获取源代码

本节将指导您获取项目源码，并帮助您在 IDE 中打开项目之前，避免在 git 配置及其他步骤中遇到常见问题。

#### 前置条件
- 已安装 [Git](https://git-scm.com/)
- 安装 [IntelliJ IDEA 2023.2](https://www.jetbrains.com/idea/download) 或更高版本。
- 对于 **Windows** 系统，请设置以下 git 配置以避免克隆过程中的常见问题：
  ```
  git config --global core.longpaths true
  git config --global core.autocrlf input
  ```

#### 克隆主仓库

IntelliJ 开源仓库可从 [GitHub 仓库](https://github.com/JetBrains/intellij-community) 获取，可以克隆或下载为 zip 文件（基于某个分支）到 `<IDEA_HOME>`。
**master**（_默认_）分支包含的源代码将用于创建所有 JetBrains IDE 的下一个主要版本。
旧版 JetBrains IDE 的分支名称和内部版本号可以在
[内部版本号范围](https://plugins.jetbrains.com/docs/intellij/build-number-ranges.html) 页面找到。

您可以直接使用 IntelliJ IDEA [克隆此项目](https://www.jetbrains.com/help/idea/manage-projects-hosted-on-github.html#clone-from-GitHub)。

或者，在终端中按照以下步骤操作：

   ```
   git clone https://github.com/JetBrains/intellij-community.git
   cd intellij-community
   ```

> [!TIP]
> - **为了更快下载**：如果不需要完整的仓库历史记录，可以创建[浅层克隆](https://git-scm.com/docs/git-clone#Documentation/git-clone.txt---depthdepth)
> 要仅下载仓库的最新版本，请在 `clone` 后添加 `--depth 1` 选项。
> - 在 IntelliJ IDEA 中克隆也支持创建浅层克隆。

#### 获取 Android 模块
IntelliJ IDEA 需要从独立 Git 仓库获取额外的 Android 模块。

在项目根目录 `<IDEA_HOME>` 下运行以下脚本以获取所需模块：
- Linux/macOS：`./getPlugins.sh`
- Windows：`getPlugins.bat`

> [!IMPORTANT]
>
>  请始终将 `intellij-community` 和 `android` Git 仓库 `git checkout` 到相同的分支/标签。


---
## 构建 IntelliJ IDEA

> [标准 GitHub 运行器](https://docs.github.com/en/actions/concepts/runners/github-hosted-runners) 由于磁盘大小限制，已无法用于构建此项目。
> 现在我们使用[更大的运行器](https://docs.github.com/en/enterprise-cloud@latest/actions/concepts/runners/larger-runners)，这些运行器仅适用于使用 GitHub Team 或 GitHub Enterprise Cloud 计划的组织和企业。
> 个人 GitHub 账户的用户可以使用[预编译的二进制文件](https://github.com/JetBrains/intellij-community/releases)，
> 或者在本地从源代码构建 IntelliJ IDEA。

这些说明将帮助您从源代码构建 IntelliJ IDEA，这是 IntelliJ 平台开发的基础。
需要 IntelliJ IDEA '**2023.2**' 或更高版本。

### 在 IDE 中打开 IntelliJ IDEA 源代码
使用最新的 IntelliJ IDEA，点击 '**文件 | 打开**'，选择 `<IDEA_HOME>` 目录。
如果 IntelliJ IDEA 显示有关缺少或过时必需插件（例如 Kotlin）的消息，
请[启用、升级或安装该插件](https://www.jetbrains.com/help/idea/managing-plugins.html) 并重启 IntelliJ IDEA。


### 构建配置步骤
1. **JDK 设置**

- 使用 JetBrains Runtime 21（不含 JCEF）进行编译
  - IDE 将在首次构建时提示下载
> [!IMPORTANT]
>
> 需要**不含** JCEF 的 JetBrains Runtime。如果 `jbr-21` SDK 指向 JCEF 版本，请将其更改为非 JCEF 版本：
> - 将 `idea.is.internal=true` 添加到 `idea.properties` 并重启 IDE。
> - 转到 '**项目结构 | SDKs**'
> - 点击 '浏览' → '下载...'
> - 选择版本 21 和供应商 'JetBrains Runtime'
> - 要确认 JDK 是否正确，请导航到选择 jbr-21 的 SDK 页面。搜索 `jcef`，应该**_不会_**产生任何结果。

2. **Maven 配置**：如果 **Maven** 插件被禁用，请[添加路径变量](https://www.jetbrains.com/help/idea/absolute-path-variables.html) "**MAVEN_REPOSITORY**"，指向 `<USER_HOME>/.m2/repository` 目录。

3. **内存设置**
  - 确保您的计算机至少有 **8GB** 内存。
  - 如果内存刚好达到最小值，请在 '**设置 | 构建、执行、部署 | 编译器**' 中禁用 "**并行编译独立模块**"。
  - 如果可用内存明显更高，请将 "**用户本地堆大小**" 增加到 `3000`。


### 从源代码构建 IntelliJ IDEA 应用程序

**要从源代码构建 IntelliJ IDEA**，请从主菜单中选择 '**构建 | 构建项目**'。

**要构建安装包**，请在 `<IDEA_HOME>` 目录下运行 [installers.cmd](installers.cmd) 脚本。`installers.cmd` 可在 Windows 和 Unix 系统上运行。
构建安装包的选项作为系统属性传递给 `installers.cmd` 命令。
您可以在 [BuildOptions.kt](platform/build-scripts/src/org/jetbrains/intellij/build/BuildOptions.kt) 中找到可用属性列表。

传递 --debug 可暂停并等待调试器连接到端口 5005

安装包构建示例：
```bash
# 仅为当前操作系统构建安装包：
./installers.cmd -Dintellij.build.target.os=current
```

> [!TIP]
> 
> `installers.cmd` 用于从命令行运行 [OpenSourceCommunityInstallersBuildTarget](build/src/OpenSourceCommunityInstallersBuildTarget.kt)。
> 您也可以直接在 IDEA 中调用它，使用运行配置 `Build IntelliJ IDEA Installers (current OS)`。


#### 使用 Docker 的构建环境
要在预装了依赖项和工具的 Docker 容器内构建安装包，请在 `<IDEA_HOME>` 目录下运行以下命令（在 Windows 上，请使用 PowerShell）：
```bash
docker build . --target intellij_idea --tag intellij_idea_env
docker run --rm --user "$(id -u)" --volume "${PWD}:/community" intellij_idea_env
```
> [!NOTE]
> 
> 请记得为容器的用户指定 `--user "$(id -u)"` 参数，以匹配主机的用户。
> 这可以防止检出仓库、构建输出（如果有）出现权限问题。

---
## 运行 IntelliJ IDEA
要运行从源代码构建的 IntelliJ IDEA，请从主菜单中选择 '**运行 | 运行**'。这将使用预配置的运行配置 `IDEA`。

要对构建运行测试，请将这些设置应用到 '**运行 | 编辑配置... | 模板 | JUnit**' 配置选项卡：
* 工作目录：`<IDEA_HOME>/bin`
* VM 选项：`-ea`


#### 在 CI/CD 环境中运行 IntelliJ IDEA

要在 IntelliJ IDEA 之外运行测试，请在 `<IDEA_HOME>` 目录下执行 `tests.cmd` 命令。`tests.cmd` 可在 Windows 和 Unix 系统上使用。
运行测试的选项作为系统属性传递给 `tests.cmd` 命令。
您可以在 [TestingOptions.kt](platform/build-scripts/src/org/jetbrains/intellij/build/TestingOptions.kt) 中找到可用属性列表。

```bash
# 运行特定的运行配置：
./tests.cmd -Dintellij.build.test.configurations=ApiCheckTest
```
```bash
# 运行特定测试：
./tests.cmd -Dintellij.build.test.patterns=com.intellij.util.ArrayUtilTest
```

要调试测试，请使用：`-Dintellij.build.test.debug.suspend=true -Dintellij.build.test.debug.port=5005`

`tests.cmd` 仅用于从命令行运行 [CommunityRunTestsBuildTarget](build/src/CommunityRunTestsBuildTarget.kt)。
您也可以直接在 IDEA 中调用它，示例请参见运行配置 `tests`。