
当搭建好自动化框架，补充了很多用例之后，如何进行代码版本管理，确保自动化用例的稳定可运行。以及如何方便的让其他人使用（例如，不需要下载代码及搭建自动化测试环节即可使用）并方便的获取测试报告以及如何让自动化测试项目真正的产生价值则成为一个问题。
自动化测试一般可以在以下环节体现价值：

- 自动化测试是CI、DevOps的核心环节，用于快速交付；
- 功能回归 开发提测后，跑一遍原来的自动化测试（接口或UI），进行快速回归；
- 提测后测试手动触发，作为冒烟测试和提测卡点；
- 核心接口监控及报警，用于线上定时巡检并及时出错预警。

对于自动化测试代码，我们可以使用Git进行版本管理，对于自动化项目的方便运行及定时巡检，我们可以使用Jenkins任务来实现。

## 10.1  CI、CD和DevOps

CI、CD、DevOps是软件开发流程中比较重要的概念，旨在快速发现问题，快速交付以及打破开发运维之间的壁垒，形成软件开发流程的完整闭环。

### 10.1.1  CI持续集成

CI即Continuous Integration持续集成。持续，指连续不断的。集成，指将多个特性（Feature）或模块集成在一起（比如将feature特性分支Merge到dev主开发分支）。持续集成来源于敏捷开发的概念，主张小增量、快速并不断的进行集成，来快速发现模块集成中的问题，以避免特性出现重大偏离。
持续集成的主要目标是快速开发并确保质量，主要包含以下三个环节：

- Code：编码，及编写提交代码；
- Build：构建，即自动编译打包；
- Test：单元测试（自测），包括代码检查、单元测试、集成测试等；

持续集成旨在快速验证，因此自动化构建和自动化测试是持续集成的关键环节。理想模型时，开发者只需要一次Commit提交或Merge操作，便可自动触发构建和验证，并根据情况进行打回或接受集成。
持续集成中常用的工具包括代码仓库GitLab、自动构建Maven、Ant、Gradle、单元测试Junit、TestNG；代码检查Sonar Qube等等，而整个流程的整合一般使用Jenkins、TeamCity等。

### 10.1.2  CD持续交付/部署

CD即Continuous Delivery持续交付或Continuous Deployment持续部署。

- Code：编码，及编写提交代码；
- Build：自动化构建，即自动编译打包；
- Test：单元测试，包括代码检查、单元测试、集成测试等；
- Release：自动发版（交付）
- Deploy：自动部署上线

### 11.1.3  DevOps开发运维一体化

图 15.x  DevOps示意图

- Plan：计划，项目管理；
- Code：编码，及编写提交代码；
- Build：自动化构建，即自动编译打包；
- Test：自动化测试，包括代码检查、单元测试、集成测试等；
- Release：自动发版（交付）
- Deploy：自动部署上线
- Operate：线上运维
- Monitor：线上监控

## 10.2  Git基础入门及常用操作

软件代码并不是一次成型的，而是通过不断的迭代修改，更新代码而逐渐完善和优化出来的。当我们需要对一批文档和代码进行持续性的修改或更新操作，特别是多人协作时，版本管理则成为不可或缺的工具。
图15.1  SVN于Git的区别
目前最流行的两款版本管理工具为SVN和Git。SVN是一种集中的版本管理工具，简单易用，权限控制分明，所有版本在服务器集中管理。所有的检出和提交都需要连接服务器。
Git是一种分布式版本管理工具，使用Git无需连接服务器便可在本地提交版本和建立分支，只有当协作或需要发布时才需要将本地的版本汇集到远程仓库。Git相较于SVN更加方便灵活。

### 10.2.1  Git安装及配置

1. Git安装

Git的安装非常方便，Windows系统从官网下载安装包，运行安装，Linux/Mac OS系统用相应的安装命令安装Git即可。
1．Windows系统Git安装
从Git官网<https://git-scm.com/下载Git安装包，如图10.3所示。>
运行并使用默认选项安装即可。
图10.3  从Git官网下周Windows安装包
2．Linux/Mac OS
CentOS系统可以使用“sudo yum install git”命令安装。
Ubuntu系统可以使用“sudo apt-get install git”命令安装；
Mac OS系统可以先安装Brew，然后使用“sudo brew install git”命令安装。
3．验证是否安装成功
打开命令行或终端，输入“git version”能查看到所安装git的版本号证明安装成功。
2.Git配置
Git配置可以通过git config命令查看或修改。配置分为系统级（--system）、用户级（--global)和项目级（--local)，使用对应的参数可以查看和设置不同级别的配置项。

> 注：系统级的配置放在C:\Program Files\Git\mingw64\etc\gitconfig（Windows)或/etc/gitconfig（Linux/Mac OS)文件中。

用户级的配置放在C:\Users\当前用户.gitconfig(Windows)或~/.gitconfig（Linux/Mac OS)文件中。
项目级的配置放在Git项目的.git/config文件中。
1．设置用户标识
用户标识用于Git识别提交者，需要设置用户的名称和邮箱，一般设置为用户级（--global），设置方法如下，只需设置一次，如果只查看不修改提交也可以不设置。

```
git config --global user.name 你的姓名
git config --global user.email 你的邮箱
```

2．保存身份认证
在向GitLab或GitHub等远程仓库推送文件或代码时一般需要输入用户名、密码进行身份认证如果不想每次推送都输入用户名密码，可以使用以下命令。
git config --global credential.helper store
3．查看配置
可以通过git config -l命令查看当前的配置,或者加上--system/--global/--local查看不同级别的配置，如查看用户级配置，命令如下：

```
git config --global -l
```

### 11.2.2  Git基本操作

Git一般分为工作区、缓存区、本地仓库和远程仓库4个部分。工作区就是Git项目当前目录的用户文件夹及文件等。暂存区和本地仓库位于当前目录的.git目录中。远程仓库不是必须的，当需要发布给其他人使用或跟其他人协同开发时，可以使用远程仓库。常见的远程仓库如GitHub、Gitee及开源软件GitLab（企业一般使用GitLab私有部署搭建内部的远程仓库平台，已防止代码外泄）等。
Git的常见操作为git add、git commit、git pull及git push等。Git基本操作如下图所示。
图15.2  Git基本操作
1．创建本地仓库
想要将普通目录（文件夹）变成一个拥有Git版本库的项目，可以在命令行该目录下，使用git init命令初始化Git版本库。对于已经存在的项目也可以使用git clone命令来获得这个版本库。两个命令的使用说明如下：

- `git init`：初始化本地git仓库，把当前目录变成一个Git项目；
- `git clone <url>`：克隆远程仓库。

注意：对于非公开的原创仓库git clone时一边需要提供身份凭证，如果使用http开头的远程版本库地址，一般可以使用自己原创版本库平台的用户名及密码进行认证。如果使用git开头的远程版本库地址，则需要提前在远程版本库平台中配置自己当前计算机的公钥信息。
2．查看状态与变更
Git项目中工作区的文件一般有Untracked（未版本跟踪）、Unmodified（未修改）、Modified（已修改）、Staged（暂存）4种状态。在Git项目中可以使用git status命令来查看各个文件的状态（其中，一般不显示Untracked及Unmodified的文件，除非单独查看该文件状态），同时New File（新文件）也属于Modified状态。文件状态转换如下图所示：
图15.2  Git文件的4种状态
常用查看文件状态及对比文件变动的命令如下：

- `git status`：查看当前变动状态；
- `git diff` ：查看变动的内容（一般与HEAD指向的最新版本对比）；
- `git blame <file>`：查看指定文件每一行具体由谁修改。

3．追踪文件并生成快照
提交版本前首先要使用git add命令，对文件当前变动状态做个快照，如果这个文件之前未进行版本追踪（如新文件），则同时将该文件加入到变化追踪列表中去。git add除可以对单个文件进行追踪并生成快照外，还可以指定对某一目录及子目录所有文件进行追踪并生成快照，其中“.”则表示当前目录及子目录中所有文件。常用命令如下。

- ` git add <file>  `：跟踪并快照指定文件；
- `git add .` ：跟踪并快照当前目录所有改动过的文件。

4．提交版本
在使用git add对变动生成外快照后，变可以使用git commit提交版本。提交版本时必须指定commit信息（做了哪些变动等）。可以使用-m来指定commit信息，如果git commit直接回车将会进入vim编辑界面，用于编辑大段的commit信息，此时输入:wq来保存退出并执行提交版本。常用的命令如下。

- `git commit -m "commit msg"`：提交所有更新过的文件；
- `git commit -a -m "commit msg"`：对当前所有已追踪文件做快照（相当于git add）并提交；
- `git commit --amend`：修改上一次提交信息；

5．查看提交历史
如果想要查看版本提交历史记录，可以使用git log命令，git log按版本记录按最新到最久的顺序排列，每个版本包含commit号、提交者名称及Email、提交时间以及版本说明等。
常用的命令如下：

- `git log`：查看提交历史；
- `git log -p <file>`：查看指定文件的提交历史；

### 11.2.3  Git重置及撤销操作

版本管理最重要的就是提供撤销及恢复功能，如我们可以使用git checkout HEAD来放弃修改、使用git reset来重制版本、使用git revert来撤销操作等。常用命令如下：

- `git checkout HEAD <file>`：放弃指定文件当前的修改，恢复到已提交的最新版本状态；
- `git reset <commit id> --hard`：将工作区重置到某一版本状态；
- `git revert <commit id>`： 撤销（反向操作）指定版本以及之前版本的修改；

> 注意：使用git reset重置到某一版本后会删除该版本之后的所有版本。

git revert则是将指定版本及之前的操作进行反向操作，这时候往往和当前状态会形成冲突，用户需要解决冲突并提交新的版本。注意使用git revert是恢复到指定commit id的前一个版本。git revert不会删除之前的版本记录。

### 11.2.4  Git分支操作

为了并行开发并切不相互影响，Git中提供了分支（branch）的功能。不同的分支可以在某一版本的基础上提交版本，形成自己独立的版本线路。不同的分支往往用于并行开发不同的功能，当这些功能开发完成后，便可以将其所有的版本合并的某个主分支中，来完成功能的集成。
1.分支操作
常用的分支操作命令如下：

- `git branch`： 显示所有本地分支
- `git checkout <branch/tag>` 切换到指定的分支或标签
- `git branch <new-branch>` 创建新分支
- `git branch -D <branch>` 删除本地分支

2.合并与衍合
合并代码（即集成）是多分支操作时的常见操作之一，git merge用于将另一个分支的功能合并到当前分支。git rebase则通过撤销重做把目标分支的提交记录插入到当前分支的版本记录中。

- `git merge <branch>`： 合并指定分支功能到当前分支；
- `git rebase <branch>`： 撤销重做当前分支某些版本，衍合指定分支的版本记录到当前分支；

3.软件开发常用Git Flow分支管理模型
Git Flow是一种Git分支管理模型。将分支分为主分支（Master)，开发分支（Develop），特性分支（Feature）、提测分支（Release）和维护分支（Hotfixes)分支五种，如图10-5所示。
图10-5（Git Flow 5中分支）
 1. 主分支（Master分支）
主分支，分支名一般为master，主要用于部署上线。为保持“干净”和“稳定”，主分支不允直接修改和提交，只能从其他分支（一般是经过测试后的Release）合并更新。Master所有的提交都需要打标签（Tag）。
 2. 开发分支（Develop分支)
主开发分支，分支名一般为develop或其缩写。
主开发分支（汇流分支），不同的特性分支开发完后一般要合并到主开发分支进行集成。主开发分支一般变动频繁，不太稳定。
 3. 特性分支（Feature分支）
特性分支，规范分支名一般以feature-开头，组合所开发的功能特性名称进行命名。特性分支用于某个新功能的开发，开发完成后汇集到主开发分支（Develop分支）。合并后一般会删除该特性分支。
 4. 提测分支（Release分支）
由于Develop分支变动很频繁，当某些功能开发完后会基于Develop分支创建Release分支，以供部署和测试。
Release分支一般命名为release-加上相应的版本号或者日期。
一般Release提测之后，不允许再合并之后的Develop分支的更，Bug修改可以直接在Release分支上进行。
用于测试环境测试和用于预发环境测试的分支可以是统一个Release分支。
Release分支测试完成，需要上线时，将Release分支合并到Master和Develop分支，同时在Master分支打上相应的Release版本号，部署上线后便可以删除Release分支。
 5. 维护分支（Hotfix分支）
Hotfix分支一般用于解决线上问题。名称一般为hotfix-加Bug号或说明组成，Hotfix分支基于当前Master分支创建，测试完成后合并回Master合Develop分支，同时在Master分支打上相应的Tag标签。

### 11.2.5  Git远程仓库操作

如果需要使用远程仓库进行展示或协作。可以在GitHub、Gitee平台或企业内部GitLab平台注册一个账号，并新建一个远程仓库。将当前Git项目关联到远程仓库，当前分支关联远程同名分支。之后便可以使用git pull和git push来更新及推送代码。常用操作命令如下：

- `git remote -v`：查看当前项目关联到远程版本库信息；
- `git remote show <remote>`：查看指定远程仓库详细信息；
- `git remote add <remote> <url>`：添加远程仓库；
- `git fetch <remote>`：从远程仓库拉取代码；
- `git pull <remote> <branch>` ：拉取代码并与本地代码进行合并（可能出现冲突）；
- `git push <remote> <branch>` ：推送本地版本到远程仓库；
- `git push --tags`：推送本地所有标签到远程仓库；

### 11.2.6  冲突处理

在使用git revert撤销操作、git pull更新代码以及git merge合并分支代码时有时候会出现冲突。冲突一般是由于不同版本或不同的用户对同一文件的同一段进行了修改导致的。Git无法判断应该使用谁的修改。这是一般会自动融合，并形成冲突，由用户来自决定几修复这些冲突并提交新的版本。
遇到冲突时，需要手动打开包含冲突的文件，将`  <<<<<<< HEAD ` 到`  =======中间是第一种更改 `，`=======`到`>>>>>>> END`之间是另一种更改。删除掉`<<<<<<< HEAD`、`=======`、`>>>>>>> END`等无效代码，保留需要保留的内容。重新git add和git commit即可。

## 10.3   Jenkins简介及环境搭建

### 10.3.1  Jenkins简介

Jenkins是一款开源持续集成工具，来源于商业软件Hudson。由于Jenkins开源、良好的操作界面以及丰富的插件，使得Jenkins在持续集成多个阶段得到广泛的使用。
Jenkins以任务（Job）为基础，配合Git、SVN等版本管理工具及Maven、Ant、Gradle等构建工具以及众多插件，来完成持续不断的自动构建、部署、测试软件项目。
Jenkins的常见使用场景如下：

- 自动或手动打包（构建）
- 自动或手动一键环境部署
- 检测到代码更新时自动进行UT测试并同意或拒绝合并
- 定时或手动执行自动化测试

### 10.3.2  Jenkins环境搭建

Jenkins由Java语言编写，可以运行于Tomcat等应用服务器，同时jenkins.war包中自带一个开发服务器，也可以独立运行。
使用Jenkins开发环境的步骤如下：

1. 安装JDK1.8并配置环境变量

安装Jenkins需要先安装JDK，Jenkins2.*版本需要使用JDK1.8版本。
2. 下载Jenkins安装包
Jenkins下载地址：<https://jenkins.io/download/，如图15.1。>
下载Windows安装包或jenkins.war包都可以，这里按jenkins.war包讲解。
图15.1  下载Jenkins安装包

> 注：由于Java的跨平台性以及jenkins.war包自带一个开发服务器，Linux或MacOS系统安装了JDK1.8也可以下载jenkins.war进行测试使用。

> 注：MacOS系统安装了brew工具后也可以在终端使用`brew install jenkins`命令安装并使用`brew services start jenkins`来启动jenkins服务，默认端口8080，启动后打开浏览器访问<http://localhost:8080即可。>

3.启动Jenkins开发服务器
打开命令行，查看Java版本

```bash
java -version
```

确认JDK版本为1.8.x后使用`java -jar <jenkins.war路径>`来运行开发服务器，如图15.2所示。

```
java -jar jenkins.war
```

图15.2  运行Jenkins开发服务器
等待开发服务启动完成，注意此时不要关闭命令行窗口（否则服务退出）。Jenkins开发服务器默认使用8080端口（同Tomcat）。如果8080端口被占用，可使用--httpPort 来指定端口号。

```
java -jar jenkins.war --httpPort 8081
```

第一次使用Jenkins开发服务器启动时，会在当前用户的根目录下创建.jenkins文件夹。
Windows系统用户根目录指用户文件夹，一般为`C:\Users\<用户名>`。MacOS系统用户根目录为`/Users/<用户名>`。Linux系统root用户，用户根目录为/root/，非root用户，用户根目录为`/home/<用户名>。`
如果想要使用jenkins.war包重新安装jenkins，可以关闭命令行，删除.jenkins目录，然后重新运行java -jar D:\jenkins.war启动Jenkins。

> 注：使用Windows安装包jenkins.msi安装

> 注：如果下载Windows版安装包jenkins.msi，下载后双击打开，一路下一步安装即可。使用jenkins.msi安装时，Jenkins默认安装在C:\Program Files\Jenkins目录。

5.解锁Jenkins
打开浏览器访问：<http://localhost:8080。如图15.3所示。>
图15.3 解锁Jenkins
Jenkins初始密码是为了防止可以访问Jenkins网页却没有Jenkins所在服务器权限的用户偷偷部署或重新部署Jenkins服务。由于我们是本地运行，Jenkins初始密钥就在当前计算机上。拖动路径到浏览器标签页（Chrome浏览器可以查看本地文件内容），复制密码，填入并点击继续，如图15.4及215.5所示。
图15.4  查看Jenkins初始密码
图15.5  解锁Jenkins
6.安装推荐插件
解锁Jenkins点击继续稍微等待后，进入选择插件页面，在当前网络较好的情况下可以安装推荐的插件，其中包含Git、Pipline、Forders等常用插件。
推荐插件说明如表15.1所示：
表15.1 Jenkins社区推荐插件

| 插件名称                                                                                      | 插件分类     | 说明                                |
| ----------------------------------------------------------------------------------------- | -------- | --------------------------------- |
| Folders                                                                                   | 组织管理     | 可以为任务创建文件夹来组织任务                   |
| OWASP Markup Formatter                                                                    | 组织管理     | 可以在任务描述中输入安全但HTML标记               |
| Build Timeout                                                                             | 构建功能     | 构建超时时间插件                          |
| Timestamper                                                                               | 构建功能     | 为命令行输出提供时间戳                       |
| Workspace Cleanup                                                                         | 构建功能     | 工作区清理插件                           |
| Credentials Binding                                                                       | 构建功能     | 运行身份凭证绑定环境变量                      |
| Git                                                                                       | 源码管理     | Git源码管理插件                         |
| Subversion                                                                                | 源码管理     | SVN源码管理插件                         |
| SSH Build Agents                                                                          | 分布式部署    | SSH远程构建插件                         |
| Mailer                                                                                    | 通知及发布    | 邮件通知插件                            |
| Email Extension                                                                           | 通知及发布    | 扩展邮件通知插件                          |
| Ant                                                                                       | 构建工具     | Ant构建插件                           |
| Gradle                                                                                    | 构建工具     | Gradle构建插件                        |
| LDAP                                                                                      | 用户管理及安全  | LDAP登录插件                          |
| PAM Authentication                                                                        | 用户管理及安全  | 可插拔式认证模块，可以根据用户的网段、时间、用户名、密码等实现认证 |
| Matrix Authorization Strategy                                                             | 用户管理及安全  | 提供基于矩阵但安全授权策略                     |
| Pipeline                                                                                  | 流水线及持续交付 | 流水线（一套流程描述语法）插件                   |
| Pipeline: Stage View                                                                      | 流水线及持续交付 | 流水线：阶段试图插件                        |
| Pipeline: GitHub Groovy Libraries                                                         | 流水线及持续交付 | 运行从GitHub动态加载Pipeline Groovy库     |
| GitHub Branch Source                                                                      | 用户管理及安全  | GitHub分支源插件                       |
| Localization: Chinese (Simplified)                                                        | 本地化      | 本地化：中文简体插件                        |
| 点击后，等待安装完成。由于Jenkins插件来源于国外的网站，如果部分插件安装不成功，可以进行重试。多次重试仍无法安装可以暂时跳过。随后在Jenkins插件管理页面进行搜索安装。 |          |                                   |

> 注：插件也支持离线安装，将之前安装的Jenkins目录plugins目录下的所有.jpi文件拷吧到新的Jenkins安装目录的plugins下，重启Jenkins即可完成安装。

7.创建管理员
根据提示新建第一个管理员用户，管理员用户用于登录Jenkins、系统管理、创建任务、创建用户等，密码要牢记。如图15.6所示，然后点击保存并完成。
图15.6 创建管理员
8.配置服务器运行端口
创建为管理员用户后，进入服务器域名及端口配置，这里使用默认即可。如果本机8080端口已占用，可以修改为其他端口。如图15.7所示，点击保存并完成（Save and Finish）。
图15.7 配置服务器运行端口
点击保存并完成后，点击立即使用Jenkins，进入Jenkins主界面，如图15.8所示。
图15.8  Jenkin主界面

## 10.3  Jenkins自由风格任务配置

在Jenkins主界面点击左侧的新建任务，可以创建一个新任务，以接口自动化测试为例，输入任务名称，选择“构建一个自由风格的软件项目”，点击确定，如图15.9所示。

### 10.3.1  任务类型

图15.9 新建任务
常见的任务类型如下：

- **构建自由风格的软件项目**，是Jenkins自带的任务类型，用于结合Git、SVN等版本管理（SCM）工具来自定义构建流程。一般使用该种类型。
- 流水线是Pipeline插件提供的一种任务类型，是Jenkins2.0以后推出的一种新功能，可以使用代码来描述构建参数、触发器以及各阶段执行过程等。
- 多配置项目，由插件Matrix Authorization Strategy提供，可以实现同一个项目根据不同的环境及参数进行不同的构建过程。
- GitHub组织，适用于GitHub项目。
- 多分支流水线，根据项目多个分支创建一系列流水线。
- 文件夹，由Forder插件提供，用于分类任务，文件夹下面可以建立其他任务。

注：如果安装了其他插件，可能会多出其他的任务类型，同理，如果缺失某种类型，有可能是没有安装某种插件。
一个自由风格的任务配置一般通用配置（General）、源码管理、构建触发器、构建环境、构建（Build）及构建后操作几个组成部分，其中核心是构建操作。
构建及构建后操作可以添加多个不同类型的操作步骤。
15.10  任务通用配置

### 10.3.2  通用配置

通用配置是任务的一些基本配置，如任务描述，是否需要参数、是否允许并非（同时）构建等等，场景配置项如下：

- 描述：项目描述，如果安装了OWASP Markup Formatter插件，可以使用部分安全的HTML来编写描述。
- 丢弃旧的构建：按配置删除之前构建的内容。
- 参数化构建过程：为执行构建提供输入框、下拉框等表单参数，以根据参数执行构建。
- 关闭构建：不执行下方的构建步骤。
- 在必要的时候并发构建：满足策略要求时进行并发构建。默认情况下任务，每次构建需要按序执行，上次构建完成后才能开始本次构建。
- GitHub项目：用于快速集成GitHub项目，由插件GitHub Branch Source提供。
- 需要锁定资源（This build requires lockable resources）：构建时锁定部分资源。
- 构建节流（Throttle builds）：限制构建频次。

点击”高级“按钮可以查看其他的配置选项，如：

- 安静期：设置每次构建的最小间隔时间。
- 重试次数：设置拉取代码失败时的重试次数。
- 该项目的上游项目正在构建时阻止该项目构建：任务可以配置上下游依赖关系，勾选该选项时，上游项目（任务）构建时不允许该任务执行构建
- 该项目的下游项目正在构建时阻止该项目构建：下游项目（任务）构建时不允许该任务执行构建。
- 使用自定义的工作空间：任务默认在Jenkins安装目录的Workspace目录下克隆代码，根据项目代码仓库名称生成文件夹。该选项可以自定义任务的工作空间（拉取代码的位置）和显示名称。
- 保留构建的依赖日志：保留每次的构建日志。

### 10.3.3  源码管理

源码管理用于配置，拉取代码的软件代码仓库，通过插件支持Git及SVN等版本管理系统（SCM）。一般需要配置项目远程仓库地址及分支等，非公开的仓库还需要配置身份瓶凭证（Credentials）才能拉取代码。

- 无：不使用版本管理（SCM）工具拉取项目源码。
- Git：拉取Git远程仓库指定分支源码。
- Subversion：从SVM服务器拉取源码。

### 10.3.4  构建触发器

创建好的任务可以手动构建（点一下“立即构建”链接执行），也可以在某种情况下自动构建。构建触发器（Trigger）控制该任务在什么情况下执行构建操作。
常见的构建出发器如下：

- **触发远程构建：**设置一个Token（令牌），允许通过URL触发该任务的构建。
- **其他工程构建后触发：**在其他工程（任务）构建后，触发该任务的执行。这里的其他工程便是该任务的上游任务，可以配置上游任务稳定构建、不稳定构建或构建失败时才触发该任务的构建。
- **定时构建：**通过日程表设置，定时或者每隔一定时间循环自动构建。
- **轮询SCM****：**通过日程表设置，定时或每隔一定时间检查所配置SCM（版本管理工具），如Git仓库代码是否有更新。检测到代码更新时执行构建。
- GitHub hook trigger for GITScm polling：通过GitHub Webhooks触发构建，Webhooks是项目代码仓库的一种配置，在发生指定事件（如推送commit）时发送请求到所配置的接口。

默认不配置任何触发器时，为手工构建，及点一次“立即构建”，执行一次构建。定时构建设置指定的时间，自动执行，如自动化测试。轮询构建常用于检测到代码更新时自动进行冒烟测试或自动打包。

### 10.3.5  构建环境

构建环境时构建前的一些环境配置，如清理工作空间、使用证书密钥等。
常见的配置项如下：
Delete workspace before build starts：构建开始时删除工作空间，构建时会重新生成工作空间，相当于完全清空之前的构建。
User secret text(s) for files(s)：配置使用个人证书、私钥文件、用户名密码等。
About the build if it's stuck：配置指定策略（如超时时间），检测到卡住时退出构建。
And timestamps to the Console Output：在命令行输出种增加时间戳，由插件Timestamper提供。
With Ant：使用Ant执行构建，有插件Ant提供。
在构建操作不需要额外的环境配置时，这些选项可以忽略。

### 10.3.6  构建步骤

构建（Build）步骤是任务的核心操作。原本的构建一般由指C、C++、Java、Go等编译型编写的软件，或前端工程编译及打包（如jar包、war包、apk包，ipa包）等过程。广义的构建可以是任何的需要自动化处理的流程。
构建可以设置多个步骤，根据插件支持不同类型的如Ant、Maven、Gradle及shell、Windows批处理命令等多种操作。
通过shell或Window批处理命令，我们可以调用更多的工具如Python、JMeter等各种工具来灵活的组织各种自动化流程。
常见的构建步骤如下：

- 执行Windows批处理命令：根据用户输入的命令生成批处理（.bat）文件并执行。
- 执行shell：更加用户输入的命令生成shell（.sh）文件并执行。
- 调用顶层Maven目标：
- Invoke Ant：调用Ant命令。
- Invoke Gradle Script：调用Gradle脚本。
- Run with timeout：执行带超时时间的命令。
- Set build status to "pending" on GitHub commit：设置GitHub commit状态为“pending” 处理中。

### 10.3.7  构建后操作

构建后操作是在构建完成后需要执行的操作，如发布报告、发送邮件等。构建后操作可以根据当前的构建状态来选择执行，如构建失败、不稳定构建等。不稳定构建指一会儿构建成功，一会儿构建失败。在高级选项中还支持更详细的出发策略，如第一次构建失败，连续N次构建失败等。
常见的构建后操作如下：

- 归档成品：
- 构建其他工程：触发构建其他任务
- 记录文件的指纹用于追踪：
- Aggregate downstream test results：聚合测试结果以供下载。
- Publish JUnit test result report：发布JUnit测试报告。
- Git Publisher：
- E-mail Notification：发送邮件通知
- Editable Email Notification：发送可编辑邮件通知
- Set Github commit status (universal)：设置GitHub commit状态
- Set build status on GitHub commit [deprecated]：设置GitHub commit 构建状态[该选项已废弃]
- Delete workspace when build is done：构建后删除工作空间。

## 10.4  接口自动化测试任务实践

### 10.4.1  自动化任务配置

1.新建一个任务，填入名称，如apitest，选择“构建一个自由风格的项目”，点击完成，进入任务配置页面。
2.（可选）在通用配置（General）中填写描述，如，“接口自动化测试项目”。
3.源码管理选择Git，填写项目仓库地址，如<https://gitee.com/hanzhichao/api-auto.git。>
以Gitee为例。仓库地址可以在仓库主页点击“克隆/下载”按钮进行复制，如图15.11所示。
图15.11 复制仓库地址

> 注：常见的仓库地址有HTTPS和SSH两种类型，两者主要的是授权方式不同。前者地址以<https://开通，一般可以使用有访问该权限的用户名及密码进行授权。后者以git@开头，>

一般使用用户密钥进行授权。这里使用HTTPS方式。
如果项目是开源（Public）模式，如本项目，即任何人都可以直接克隆代码，则不需需要填写身份凭证（Credentials）。默认拉取master分支的代码，如需要拉取其他分支，可以修改或增加，如图15.12所示。
图16.12 源码管理配置
如果项目是私有仓库，则需要添加身份凭据，以HTTPS格式的仓库地址为例，点击Credentials下面的添加按钮，如图15.13所示，作用域（Domain）默认全局凭证（即所有任务都可以使用），类型选择用户名密码（Username with password），填写远程仓库如Gitee的用户名点击保存。
图15.13 添加凭据
添加完凭证后，在Credentials下的下拉框中选择该身份凭证。

> 注：如果仓库地址下显示红色的访问权限报错提示，一般为身份凭证设置或选择的有问题。

3.构建触发器及暂时不选（使用手工构建），构建环境也不需要额外的配置。
4.构建选项点击“增加构建步骤”，根据当前的Jenkins服务所在计算系统，以Linux或MacOS系统为例，选择执行shell。Windows系统用户可以选择“执行Windows批处理命令”。
在下方的输入框中输入执行所有Pytest用例的命令，如：

```bash
$ python3 -m pytest .
```

选择如图15.14所示。

图15.14 增加构建步骤

执行构建步骤前，会自动克隆源码配置的代码仓库，并cd进入到项目目录。在该目录下执行“pytest . ”即使用pytest收集并执行当前目录下的所有用例。
如果命令只输入“pytest .”，则要求此时的用户（启动Jenkin服务使用的用户）在命令行输入pytest命令时可以找到对应的可执行文件，如pytest.exe来执行操作。即pytest.exe所在目录（Windows系统，pytest.exe可执行呈现安装在，Python安装目录的Scripts目录中）配置到环境变量PATH中去，这样输入pytest才能找到对应程序。
使用“python3 -m pytest .”是使用Python调起pytest来执行用例（pytest本质上是Python的一个模块）。一方面可以放在pytest可执行程序所在目录没有配置到PATH中去，另一方面使用python命令执行代码时会自动将当前目录（项目根目录）加入到PYTHONTHON，即Python查找模块即包的路径中去，避免出现导入模块报错问题。

> 注：由于实际部署时，Jenkins服务运行用户和登录的用户不一样，加之用户配置的环境变量PATH是用户级别的，其他用户（Jenkins用户）可能访问不到的情况，如Jenkins找不

到python可执行程序，则可以将python改为python的绝对路径。
如果是Linux/MacOS用户，可以在前面添加

```
#!/bin/bash -ilex
```

如果是Windows用户，只要一个Python环境时，则应该使用：

```bash
python - m pytest .
```

或使用python的绝对路径。
5.暂时不添加构建后操作，点击保存，返回任务（工程）页面。
6.在任务页面点击左侧菜单的“立即构建”链接，执行第一次自动化测试。
构建默认在Master节点即Jenkin所在服务器上运行。在下方的构建历史（Build Histrory）中可以查看到每一次构建记录，前面的图标表示构建状态，一般红色表示失败，蓝色表示成功，闪烁的灰色并带有进度条表示构建中。如图15.15所示。
图15.15 立即构建 执行自动化测试任务
点击对应构建记录前面的状态图标，可以查看构建日志，如图15.16所示。
图15.16 构建日志
图片上方菜单表示是apitest项目的第2次构建。从具体的日志中可以看到构建使用的用户为superhin，项目的工作空间为/Users/superhin/.jenkins/workspace/apitest。
下面是Git拉取项目代码的过程，然后将我们输入的命令生成随机名称的.sh文件进行执行。由于执行Pytest命令时报错，因此结束是本次构建过程是失败（FALUTRUE）。
通过查看报错日志，是缺少allure模块，考虑到这是一个Python的三方包，因此可能是当前环境没有安装该项目的依赖包导致。解决方法为，打开命令行，进入项目所在目录，并安装依赖，命令如下：

```bash
cd /Users/superhin/.jenkins/workspace/apitest
pip3 install -r requirements.txt
```

由于每次构建使用的都是同一个python解释器，因此只需要安装一次依赖包即可。

> 注：用户需要根据日志的提示，cd到自己项目的工作空间进行安装，Windows系统的用户一般应使用pip命令进行安装。

安装完项目依赖后，重新执行构建，可以在构建日志中查看到Pytest运行用例的命令后输出结果以及最后的用例统计，如图15.17所示。
图15.17 构建成功时的构建日志
如果用例全部通过（包含跳过的用例），则构建状态为成功（SUCCESS）。存在失败的用例或异常，则构建状态为失败（FAILURE）。

### 10.4.2  发布报告

单纯从构建日志中查看测试结果比较不太直观，执行自动化测试应该有测试报告以供查看。
本章使用的示例项目ApiAuto默认生成了两种测试报告，一种是基于pytest-html插件生成的HTML报告，默认生成在项目reports目录下，名称为report.html。另一种是基于allure-pytest插件生成的Allure测试报告数据，生成在reprots目录下的allure_data下。具体配置可以查看项目下的pytest.ini文件，部分配置如下：

```ini
[pytest]
addopts = -v --strict-markers --html=reports/report.html --self-contained-html --alluredir=reports/allure_data
```

1.通过“工作空间”下载查看测试报告
在任务主页点击工作区，可以进入项目目录，可以在项目目录中进入reports目录可以打包下载测试报告以及日志进行查看，如图15.18所示。
图15.18 通过工作区查看测试报告

> 注：点击log.txt后的查看其中中文显示为乱码，是因为日志文件编码格式的原因。点击查看report.html时，显示的是html源码。allure_data中的报告数据需要使用Allure命令行工具进行生成才能查看。

这种查看报告的方法比较麻烦，我们可以通过构建够步骤配合相应的插件将测试报告发布到任务首页。
#### 发布HTML测试报告
发布HTML测试报告需要安装HTML Publisher这个插件。插件的安装方式为，点击左上角的Jenkins图标返回Jenkin面板（Dashboard）主界面。点击左侧菜单的系统管理，如tu 15.19所示。
图15.19 Jenkin面板（Dashboard）主界面
点击插件管理，在右侧选择第二个Tab可选插件，输入插件名称HTML Publisher，如图15.20。
图15.20 搜索可选插件
勾选插件，点击Install without restart。
由于每次插件安装需要重启Jenkins，在插件安装界面勾选下方的“安装完成后重启Jenkins（空闲时）”，入托15.21所示。
图15.21 安装插件过程

> 注：插件安装完后，也可以访问<http://localhost:8080/restart页面，点击是，手动重启Jenkins。Windows用户使用java> -jar jenkins.war包运行的Jenkins则需要停止进程重新运行该命令。

重启Jenkins后 ，重新登录，进入apitest项目（任务）页面，点击左侧菜单的“配置”链接来修改之前的构建配置。
在构建后操作中，点击“增加购机后操作步骤”，选择Publish HTML reports（发布HTML报告），点击Reprots下的“新增”按钮。
在报告目录（HTMLdirectory to archive）中填写reports，报告文件（Index pages[s])中填写report.html，在下方的选项中还可以填写报告的网页标题（HTML中的Title），和报告显示标题，如图15.22所示。

图15.22 发布报告配置

点击保存，返回任务页面。点击左侧菜单的“立即构建”，重新执行一次。刷新当前页面，便可以在任务页面，左侧菜单及主试图中查看到“接口测试报告”链接，如图15.23所示。

图15.23 重新执行后促销接口测试报告菜单

为了安全起见，Jenkins默认会禁止发布的HTML报告中的所有Javascript代码及嵌入的CSS样式，导致生成的报告只有数据而没有样式。解决的办法非常多，最简单通过Jenkins系统管理中的脚本命令行，执行以下命令，临时关闭CSP（HTML内容安全策略）。
点击左上角Jenkins图标，返回Jenkins主页面，点击系统管理，找到并点击“脚本命令行”链接，输入以下命令并点击运行。如图15.24所示。
System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "")

图15.24 执行脚本临时关闭内容安全策略

> 注：该方法只是临时关闭内容安全策略，重启Jenkins后需要重新执行。

运行该脚本后，返回任务管理界面，重新执行构建，点击“接口测试报告”链接，即可查看到带样式的测试报告，如图15.25所示。

图15.25 查看HTML测试报告

#### 发布Allure测试报告
相比于pytest-html插件生成的测试报告，allure-pytest生成的测试报告更加华丽和信息丰富。在Jenkins中可以通过Allure插件来发布Allure生成的报告。
插件安装方式同HTML Pubbisher，在系统管理，插件管理页面，可选插件中搜索Allure，勾选安装并重启Jenkins。如图15.26所示。

图15.26  搜索安装Allure插件
重启完成，重新登录Jenkins后，进入apitest项目的配置页面，添加构建后操作步骤，选择“Allure Report”，在Results下的Path中填写生成的Allure报告数据目录reports/allure-data，点击保存，如图15.27所示。

图15.27 添加构建后操作——Allure Report

> 注：下方的红色报错是因为还没有配置Allure命令行工具，下一步进行配置。

Allure-Pytest插件生成的报告数据需要使用Allure命令行allure-commandline工具进行解析并生成具体的测试报告。
Allure命令行工具可以配置Jenkins自动安装（在下一次构建后执自动下载，速度较慢），也可以手动安装，这里建议手动在Jenkins服务所在计算机上，下载allure-commandline，下载地址如下。
<https://repo.maven.apache.org/maven2/io/qameta/allure/allure-commandline/>
版本可以选择最新的，这里以2.10.0版本为例。下载2.10.0下的allure-commandline-2.10.0.zip并后解压，放在某一目录即可，如/usr/local/下，即工具目录为/usr/local/allure-2.10.0。
下载完成后，进入Jenkins系统设置，选择全局工具配置，下拉找到Allure Commandline。点击新增Allure Commandline，去掉勾选自动安装，别名填写allure，安装目录填写解压后的allure-commandline目录，如/usr/local/allure-2.10.0，点击保存，如图15.28所示。
图15.28  Allure命令行工具配置
返回apitest任务界面，再次点击“立即构建”，等构建完成后，点击右侧菜单或任务主界面中的Allure Report链接，即可查看Allure报告，如图15.29所示。
图15.29 在Jenkins中查看Allure 报告
在报告页面可以通过以分类（Categories）、测试套件（Suites）、图表（Graphs）、时间线（TImeline）、行为（Behavious）、包（Packages）等不同视角查看用例执行结果，也可以切换显示语言为中文。点击浏览器的返回按钮可以回到任务页面。

> 注：HTML报告和Allure报告一般选择一种即可，基于Robot Framework框架的自动化测试项目可以搜索安装Robot Framework相关插件来发布报告。

### 10.4.3  发送邮件通知

除发布报告外，我们希望构建（自动化测试）执行完毕后，发送邮件通知。在ApiAuto该测试框架中，已经集成了测试完成后自动发送邮件的功能，可以在项目pytest.ini中，或者在命令行中使用参数--send-email参数来测试运行完，自动发送邮件。
这里我们不使用框架自带的邮件通知功能，而改为使用Jenkins构建后操作进行发送邮件通知。优点为：
 1. 框架不需要自己实现邮件通知功能。
 2. 可以根据构建状态（失败、不稳定）来选择性发送邮件通知或通知不同的人。
 3. 可以包含包括Git拉取代码等完整的构建信息。
 4. 使用Email Extention插件还可以自定义邮件模板等。
发送邮件通知的常见选择有两种，一种是Mailer插件提供的简单邮件通知，另一种是由Email Extention插件提供的扩展型邮件通知。

#### 简单邮件通知配置

发送邮件功能需要配置管理员邮箱，以及发送邮件的SMTP服务器，及用户名密码。常用的电子邮箱如新浪、163、126、QQ邮箱以及企业邮箱等一般都提供SMTP发送邮件功能。需要在个人邮箱设置里找到并开启该功能，并在相关帮助说明中，找到相关的SMTP服务地址。以新浪邮箱为例，如图15.30所示。
图15.30  新浪邮箱开启SMTP服务
为了避免邮箱密码泄露，一般需要设置一个授权码代替密码使用。
开启邮箱SMTP服务后，进入Jenkins系统管理中的系统配置页面。
首先需要设置管理员邮箱。找到Jenkins Location下的系统管理员邮件地址，建议和发送邮件的邮箱一致，如<test_results@sina.com>，如图15.31。
图15.31  设置管理员邮件地址
在系统配置中下拉找到邮件通知，填写SMTP服务器，如smtp.sina.com，点击高级按钮，勾选使用SMTP认证，填写用户名及授权码，勾选使用SSL协议，如图15.32所示。
图15.32 邮件通知配置
返回apitest任务页面，点击配置，增加构建后操作步骤，选择E-mail Notifacation（邮件通知）。
在Recipients（收件人）中填写收件人地址，多个地址以空格隔开。如图15.33所示。
图15.33  邮件通知配置
默认会在构建失败或者构建不稳定（比如部分用例失败）或由不稳定变为稳定时发送。勾选下方“每次不稳定的构建都发送邮件通知则”每次不稳定构建都发送邮件通知。
勾选下方的单独发送邮件给对构建造成不良影响的责任人，是将邮件由群发，改为单独发给每个人。
为模拟失败情况，我们临时把构建步骤中的shell改为

```bash
$ python3 -m pytest 不存在的目录
```

保存任务配置，重新执行构建，更构建完成后查看构建日志，在邮件日志最后可以看到：

```
......
Sending e-mails to: ivan-me@163.com superhin@126.com
Finished: FAILURE
```

登录收件邮箱，查看邮件内容，如图15.34所示：

图15.34  查看Jenkins邮件通知
日志中报告任务（Job）的链接，以及完整的构建日志。
2. 扩展型邮件通知
由于邮件通知所提供的邮件功能较弱，我们可以使用Email Extension插件，来更灵活的配置构建之后的邮件报告。
Email Extension的使用，同样需要首先在系统管理，系统配置中设置管理员邮件地址（已经设置过则不需要重复设置），然后下拉找到Extened E-mail Notification（扩展邮件通知）。
填写SMTP server（SMTP服务器地址），如smtp.sina.com。
填写SMTP Port，如465。
点击高级，在Credentials（身份凭据）下点添加，选择Username with password（用户名密码），输入邮箱用户名及授权码，添加后，选择刚刚添加的身份凭证，勾选Use SSL，如图15.35所示。
图15.35  扩展邮件通知系统配置
下方还有很多默认项配置，如默认邮件类型，默认收件人，默认邮件模板等等，这些也可以在每个任务中单独配置，点击保存。
返回任务apitest，修改配置，添加构建后操作步骤，Editable Email Notification（可编辑邮件通知）。
修改Project Recipient List（项目收件人列表），从默认收件人$DEFAULT_RECIPIENTS（在系统配置中可以设置），改为具体的收件人，多个收件人用英文逗号隔开。
Content Type（邮件内容类型）选择HTML（text/html）。
Default Subject（邮件主题）由默认邮件主题$DEFAULT_SUBJECT，修改为，如

```
[CRM]接口测试报告
```

修改Default Content（默认邮件内容），如：

```
<h3>Hi, All</h3>

接口自动化测试执行完成，点击以下链接查看在下Allure测试报告：<br/>
<a href="http://localhost:8080/job/apitest/allure/">http://localhost:8080/job/apitest/allure/</a> <br/>
附件中为HTML测试报告及Jenkins构建日志
```

Attachments附件可以添加本项目中生成的一些文件，如测试报告reports/report.html。
Attach Build Log（是否附带Jenkins构建日志）选择Attach Build Log。
如图15.36所示。
图15.36  任务扩展邮件通知配置
可点击Advanced Settings来进行更多的编辑，如Triggers（触发器），来根据测试结果来发送邮件给不同的人，可以配置多个触发器。如图15.37所示，设置的为在每次执行完都（Always）发送邮件给上面配置的收件人。
图15.37  邮件触发器设置
点击保存，重新构建apitest，登录收件人邮箱，查看邮件报告，如图15.38所示。
图15.37  自定义邮件报告

### 10.4.4  定时构建及轮询SCM

除了手动点击“立即构建”来执行自动化测试外，有时我们需要定时执行，如每隔10分钟自动构建（运行自动化测试），或者检测到自动化项目有代码更新时进行自动构建。我们可以使用构建触发器实现。
1.定时构建
回到任务配置页面，在构建触发器中，勾选定时构建，如图15.38所示。在日程表中填写，日程计划，如：

```
H/2 * * * *
```

图15.38  定时构建配置
Jenkins日程表是一种表达执行时间的语法，类似于Linux的crontab定时配置。
日程表按空格分为5段，从左到右分别为分钟、小时、日期、月、星期。每个阶段可以使用H或*（任何一个时刻）、数字n（该时刻）、数字n,m（在n或m时刻），数字n-m（在n-m时刻）来灵活的组织。
一些使用示例如下：

- `H/2 * * * *`：每分钟构建一次。
- `H * * * *`：每小时任选一时刻构建一次。
- `H 10 1 * *`：每月1号早上10点执行一次。
- `H 0-8,20-23 * * 1-5`：周一到周五，早上0点到8点以及晚上8点23点没小时任选一时刻运行。

点击保存，等待一定时间后，可以看到Build History下自动触发了新的构建。
2.轮询SCM
SCM即Git等版本管理工具，通过日程表配置，进行检测代码是否更新。当检测代码更新时自动触发构建，如图15.39所示，配置每两分钟检查一次代码仓库，如果没有更新则不忽略，如果检查到有更新则触发自动构建。
图15.39  轮询SCM设置

### 10.4.5  参数化构建

上面的构建只支持运行所有用例，如果我们想只运行某个模块的用例，或者运行指定“标记”的用例该如何实现呢？
参数话构建可以为任务提供表单选项，让用户可以根据选项灵活的选择如何构建。如，我们可以提供一个文本框参数，让用户填写要运行的用例目录，以及提供一个下拉框，支持用户指定标记等。
常见的参数类型如下：

- 凭据参数：在执行构建时，提供一个下拉框，可以选择一个已创建的身份凭据，以供构建时使用。
- 字符参数：在执行构建时，提供一个输入框，来输入一个参数值，以供构建时使用。
- 密码参数：在执行构建时，提供一个密码输入框（输入时不显示明文），来输入一个参数值，以供构建时使用。
- 布尔值参数：在执行构建时，提供一个勾选框，可以勾选或者不勾选，以供构建时时使用。
- 文件参数：在执行构建时，提供一个上传文件框，用户可以上传文件，以供构建时使用。
- 文本参数：在执行构建时，提供一个多行文本框，用户可以输入一段文本，以供构建时使用。
- 运行时参数：在执行构建时，提供一个下拉框，可以选择某个上游项目的指定构建记录，以供本任务构建时使用。
- 选项参数：在执行构建时，提供一个下拉框，用户可以选择某个选项，以供构建时使用。

返回apitest任务页面，点击配置，在General（通用配置）中勾选参数化构建过程，点击添加参数。
首先，添加一个字符参数，名称为path，默认值为testcases，描述填写“指定要执行的用例或用例目录，多个以空格隔开。”。
继续添加一个选项参数，名称为mark，默认值为空，填写ApiAuto项目pytest.ini中已注册的markers标记，每个一行，第一行留空，表示不选任何标记。

```
smoke
scene
abnormal
ddt
hzc
jw
```

描述填写“选择运行带指定标记的用例。”，如图15.40所示。
图15.40  添加构建参数
参数名称即该参数的变量名，建议使用英文字母（合法变量名标示符）。在构建步骤中便可以通过标识来获得用户输入或选择的具体值。
在Windows批处理脚本中，使用“%变量名%”来获取变量值，在Linux或MacOS系统中使用“$变量名”或“${变量名}”来获取变量值。
以执行shell为例，在任务配置下拉到构建。将原来的python3 -m pytest .修改为如下命令：

```shell
if [ -z "$mark" ]; then
  python3 -m pytest $path
else
  python3 -m pytest $path -m $mark
fi
```

即，如果mark变量为空，则不带-m运行，否则指定-m加指定标记运行。
如果使用Windows批处理命令，可以使用如下命令：

```bat
@echo off
if "%mark%" == "" (
    python -m pytest %path%
) else (
    python -m pytest %path% -m %mark%
)
```

保存配置，回到任务界面，点击Build with Parameters（使用参数进行构建），默认不指定标签，运行testcases目录所有用例。我们可以修改为运行testcases\test_customer.py和testcases\test_leads.py两个模块的带smoke标记的用例，点击开始构建，如图15.41所示。

图15.41  使用参数运行自动化测试

> 注：在使用定时构建时，会使用默认参数执行构建。

## 10.5  Jenkins流水线

Jenkins 流水线（Pipeline）是Jenkins 2.x版本后支持的一种构建流程的描述语法，安装Pipeline插件后，在新建任务时，便可以选择“流水线”类型。
新建任务apitest2，选择“流水线”，点击确定，如图15.42所示。
15.42  流水线任务配置
不同于“自由风格的项目”，流水线类型的项目只有General（通用配置）、构建触发器、高级项目选项和流水线几个配置。其中，核心是流水线设置，配置中提供一个多行文本框来编写流水线脚本，也可以在下拉框选择Pipeline script from SCM，即从Git等代码仓库中读取Jenkinsfile中的Pipeline脚本进行执行。
Pipeline脚本有声明式（Declarative Pipeline）和脚本式（Scripted Pipeline）和两种语法类型。
两者都使用Groovy语法，声明式语法表示简单明了，最外层为pipeline {}，脚本式语法最外层为node {}，支持定义变量及if-else条件判断等，属于进阶使用。
详细语法可以参考Jenkins官方文档：<https://www.jenkins.io/doc/book/pipeline/>

### 10.5.1  声明式语法

声明式语法分为pipeline（流水线）、stage（阶段）、steps（步骤）三层，
顶层为pipeline,可以包括多个stages。
一个stage可以包含多个steps，也可以包含stages，嵌套多个子阶段。
steps根据支持使用sh、bat、echao及由插件提供的git、mail等命令，每个步骤一行。

1.全局pipeline配置
以声明式式语法为例，pipeline {}中可用选项为：

- agent：必填，选择执行节点，如any（任意节点），通过label（节点标签）或通过docker、dockerfile、kubernetes指定运行节点。
- options：可选，任务配置，如丢弃旧的构建、超时时间、安静期等，也可以在任务配置中的General中设置。
- parameters：可选，参数化构建，同任务配置General中的使用参数化构建。
- environment：可选，可以设置额外的环境变量。
- tools：可选，用于选择Maven、Ant等构建工具。
- triggers：可选，构建触发器，可以使用语法描述构建触发器，也可以在配置中的构建触发器中设置。
- stages：必填，构建阶段，里面可以声明多个阶段。
- post：可选，构建后操作，可以包含多个步骤。

2.stage阶段配置
stages中可以声明多个stage（阶段），每个stage {} 中可用的选项为：

- agent：可选，该阶段执行节点，每个阶段可以选择不同的节点执行，不写则使用pipeline中的agent设置。
- options：可选，为该阶段设置如超时时间等配置。
- environment：可选，为该阶段设置额外的环境变量。
- input：可选，提供用户交互表单，等待用户输入。
- when：可选，设置该阶段执行条件。
- steps：必填，测试步骤，可以填写多个测试步骤，每个步骤一行。
- stages：可选，阶段中可以包含多个子阶段。
- parallel：可选，并行阶段，类似于stages，parallel中可以包含多个stage，这些stage将并发运行。

3.steps步骤描述

- sh：执行shell语句。
- bat：执行Windows批处理命令。
- echo：输出信息到日志。
- git：拉取代码，由Git插件提供。
- mail：发送邮件通知，由Mailer插件提供。
- allure：使用全局工具配置的allure命令行工具，生成报告，由Allure插件提供。

在流水线类型的任务配置中，可以点击Pipeline编辑框下的“流水线语法”链接，使用表单来生成不同的步骤命令。
15.43  流水线语法生成器

### 10.5.2  自动化项目示例

以上一节的接口自动化测试流程为例，我们将流程分为“拉取代码”，“执行用例”两个阶段，设置定时任务每2分钟运行一次。用例运行后，发布Allure报告及发送邮件。
参考语法如下。

```groovy
pipeline {
  agent any
  options { timeout(time: 60, unit: 'SECONDS') }
  environment {
    REPO = 'https://gitee.com/hanzhichao/api-auto.git'
  }
  triggers {
        cron('H/2 * * * *')
  }
  stages {
    stage('拉取代码') {
      steps {
        git "${env.REPO}"
      }
    }
    stage('执行用例') {
      steps {
        sh "python3 -m pytest testcases"
      }
    }
  }
  post {
    always {
        allure includeProperties: false, jdk: '', results: [[path: 'reports/allure_data']]
        mail to: 'ivan@163.com,superhin@126.com', from: 'test_results@sina.com', bcc: '', cc: '', replyTo: '',
        subject: '[CRM]接口测试报告',
        mimeType: 'text/html', charset: 'utf-8',
        body: '''<h3>Hi, All</h3>
接口自动化测试执行完成，点击以下链接查看在下Allure测试报告：<br/>
<a href="http://localhost:8080/job/apitest/allure/">http://localhost:8080/job/apitest/allure/</a> <br/>
附件中为HTML测试报告及Jenkins构建日志'''
    }
  }
}
```

执行效果如图15.44所示。
图15.44  使用Pipeline构建效果
执行时默认显示Stage View（阶段试图），显示每一轮各个阶段的执行情况和平均耗时。也可以安装Blue Ocean使得界面更加美观。

> 注：如果在任务配置中使用Pipeline script from SCM，可以去掉拉取代码阶段，参考<https://gitee.com/hanzhichao/api-auto/blob/master/Jenkinsfile>

## 小结

本章主要讲解了基于Git的分布式版本管理工具的使用及使用Jenkins进行持续集成运行自动化的例子。读者应掌握Git的常见操作及具备使用Jenkins进行持续集成的能力。
