---
title:  git教程
tags:  程序员基础知识
---
------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>[《git官方网站》](https://git-scm.com/book/en/v2"点击跳转")。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------
# 0 git、gitlib、github、gitlab

git 是一个开源的分布式版本控制系统。

gitlib 是 实现git 功能的库。

github 是一个用git做版本控制的项目托管平台，是官方的。

gitlab 是一个用git做版本控制的项目托管平台，相当于是自己搭建的github。

GitHub和GitLab的区别？

二者都是基于web的Git仓库，在很大程度上GitLab是仿照GitHub来做的，它们都提供了分享开源项目的平台，为开发团队提供了存储、分享、发布和合作开发项目的中心化云存储的场所。

GitHub作为开源代码库及版本控制系统，拥有超过900万的开发者用户，目前仍然是最火的开源项目托管系统。GitHub同时提供公共仓库和私有仓库，但如果要使用私有仓库，是需要付费的。

而GitLab解决了这个问题，你可以在上面创建私人的免费仓库。

GitLab让开发团队对他们的代码仓库拥有更多的控制，相比于GitHub，它有不少的特色：

* 允许免费设置仓库权限；

* 允许用户选择分享一个project的部分代码；

* 允许用户设置project的获取权限，进一步的提升安全性；

* 可以设置获取到团队整体的改进进度；

* 通过innersourcing让不在权限范围内的人访问不到该资源。

从代码私有性方面来看，有时公司并不希望员工获取到全部的代码，这个时候GitLab无疑是更好的选择。但对于开源项目而言，GitHub依然是代码托管的首选。




# 1 安装与配置
## 1.1 安装
### 1.1.1 Linux平台
&emsp;&emsp;Debian/Ubuntu Git 安装命令为：
```bash
sudo apt install git  //安装git
git --version       //查看git版本

```
### 1.1.2 windows平台
&emsp; &emsp;从<http://msysgit.github.io/> 下载git，并运行。完成安装之后，就可以使用命令行的 git 工具（已经自带了 ssh 客户端）了，另外还有一个图形界面的 Git 项目管理工具。
&emsp;&emsp;在开始菜单里找到"Git"->"Git Bash"，会弹出 Git 命令窗口。

## 1.2 配置
&emsp;&emsp;Git提供了一个叫做git config 的工具，专门用来配置或读取相应的工作环境变量。这些环境变量，决定了 Git 在各个环节的具体工作方式和行为。这些变量可以存放在以下三个不同的地方：

* `/etc/gitconfig` 文件——系统中对所有用户都普遍适用的配置。若使用 git config 时用 --system 选项，读写的就是这个文件。
* `~/.gitconfig` 文件——用户目录下的配置文件只适用于该用户。若使用 git config 时用 --global 选项，读写的就是这个文件。
* 当前项目目录中的`.git/config`——这里的配置仅仅针对当前项目有效。

&emsp;&emsp;每一个级别的配置都会覆盖上层的相同配置，所以 .git/config 里的配置会覆盖 /etc/gitconfig 中的同名变量。

&emsp;&emsp;在 Windows 系统上，Git 会找寻用户主目录下的 .gitconfig 文件。主目录即 $HOME 变量指定的目录，一般都是 C:\Documents and Settings\$USER。此外，Git 还会尝试找寻 /etc/gitconfig 文件，只不过看当初 Git 装在什么目录，就以此作为根目录来定位。

### 1.2.1 配置用户信息
```bash
$ git config --global user.name "user_naem"   //配置用户名
$ git config --global user.email "user_Email" //配置用户邮箱
```
&emsp;&emsp;如果用了 --global 选项，那么更改的配置文件就是位于你用户主目录下的那个，以后你所有的项目都会默认使用这里配置的用户信息。
&emsp;&emsp;如果要在某个特定的项目中使用其他名字或者电邮，只要去掉 --global 选项重新配置即可，新的设定保存在当前项目的 .git/config 文件里。

### 1.2.2 配置文本编辑器
&emsp;&emsp;设置Git默认使用的文本编辑器, 一般可能会是 Vi 或者 Vim。如果你有其他偏好，比如 Emacs 的话，可以重新设置：:
```bash
$ git config --global core.editor emacs
```
### 1.2.3 配置差异分析工具
&emsp;&emsp;还有一个比较常用的是，在解决合并冲突时使用哪种差异分析工具。比如要改用 vimdiff 的话：
```bash
$ git config --global merge.tool vimdiff
```
&emsp;&emsp;Git 可以理解kdiff3，tkdiff，meld，xxdiff，emerge，vimdiff，gvimdiff，ecmerge，和 opendiff 等合并工具的输出信息。
### 1.2.4 配置颜色和别名

&emsp;&emsp; 让Git显示颜色，会让命令输出看起来更醒目:
```bash
git config --global color.ui // true让Git显示颜色，会让命令输出看起来更醒目:
```

&emsp;&emsp; 为git命令配置别名:
```bash
git config --global alias.别名 git命令名 //为git命令配置别名
```
### 1.2.5 其它
&emsp;&emsp;在Git工作区的根目录下创建一个特殊的.gitignore文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件。

### 1.2.6 查看配置信息
&emsp;&emsp;要检查已有的配置信息，可以使用 git config --list 命令：
```bash
$ git config --list //查看所有的配置信息
```
&emsp;&emsp;有时候会看到重复的变量名，那就说明它们来自不同的配置文件（比如 /etc/gitconfig和~/.gitconfig），不过最终Git实际采用的是最后一个。
这些配置我们也可以在 ~/.gitconfig 或 /etc/gitconfig 查看。
&emsp;&emsp;也可以直接查阅某个环境变量的设定，只要把特定的名字跟在后面即可，像这样
```bash
$ git config user.name
```
# 2 基本操作
## 2.1 创建仓库
### 2.1.1 直接创建本地仓库
```bash
 git init         //将当前文件夹初始化为仓库
 git init dirname //将当前文件夹或指定文件夹初始化为仓库，如果不存在则新建
```
### 2.1.2 拷贝远程仓库到本地
```bash
git clone  版本库的URL   [本地目录名]   #克隆远程仓库到目标目录中,省略本地目录则克隆到当前目录下

#git clone  支持多种协议： 
#   git clone ssh://[user@]hostname[:port]/path/to/repo.git/
#   git clone git://hostname[:port]/path/to/repo.git/
#   git clone http[s]://hostname[:port]/path/to/repo.git/
#   git clone ftp[s]://hostname[:port]/path/to/repo.git
#   git clone rsync://hostname[:port]/path/to/repo.git 
    
#    git clone --local /opt/git/project.git   #本地仓库
#    git clone file:///opt/git/project.git    #本地仓库
  
```
* 通常，URL包含有关传输协议，远程服务器地址和存储库路径。Git支持ssh，git，http和https协议（另外，ftp和ftps可用于fetch，但效率低下，不推荐使用）。git协议不进行身份验证，因此在不安全的网络上应谨慎使用。
* ssh协议也可以使用另一种类似scp的语法，这种语法只有在第一个冒号前没有斜线时才能识别。这有助于与包含冒号的本地路径区分。

```bash
git clone [user@]host.xz:path/to/repo.git/
```
* ssh和git协议还支持用户名扩展：

```bash
git clone ssh://[user@]host.xz[:port]/~[user]/path/to/repo.git/
git clone git://host.xz[:port]/~[user]/path/to/repo.git/
git clone [user@]host.xz:/~[user]/path/to/repo.git/
```
* 当Git不知道如何处理某个传输协议时，它会尝试使用remote-\<transport>远程助手（如果存在的话）。要显式请求远程助手，可以使用以下语法，其中\<address>可能是一个路径，一个服务器和路径，或者是由被调用的特定远程助手识别的任意类似URL的字符串。：

```bash
<transport>::<address>
```


## 2.2 提交文件到仓库
```bash
 git add filename         //将文件提交到暂存区
 git commit –m “提交说明”  //将暂存区文件提交到版本库,并且添加提示信息
 
 git commit -am “提交说明” //等效于git add 加 git commit
```
## 2.3 查看仓库状态
```bash
git status     //查看仓库详细状态，工作区文件是否add，暂存区文件是否commit
git status -s  //查看仓库简述状态
```
## 2.4 查看仓库状态变化的内容
```bash
git diff filename            //查看工作区文件与上一次add后的不同之处
git diff --cached  filename  //查看暂存区文件与上一次commit后的不同之处
git diff HEAD filename  //查看工作区和暂存区中的文件与版本库里面最新版本的区别
git diff --stat             //只显示摘要
```
## 2.5 查看提交历史
```bash
git log   //查看详细commit历史，可以看到各版本的id,但看不到已经删除的commit
git log --oneline     //查看简洁的commit历史
git log --graph       //查看提交历史中什么时候出现了分支、合并
git log --reverse     //逆序显示
git log --author=name //查看name的提交历史
git log --since={time} --before={time}  //查看指定时间的提交历史
git log --until={time} --after={time}   //查看指定时间的提交历史
git log --no-merges //隐藏合并提交历史

git log --graph --pretty=oneline --abbrev-commit //图形化查看提交和合并历史
git reflog //查看所有分支的所有操作记录（包括commit和reset的操作），包括已删除的commit记录
```
## 2.6 撤销修改与版本回退
```bash
git checkout filename //撤销对工作区文件的修改到最近一次git add后（没有提交到暂存区）

git reset HEAD filename //撤销对暂存区中的文件修改到最近一次commit后的，再用git checkout就能丢弃修改（已经提交到暂存区，但没有提交到版本库）

git reset --hard //版本回退，撤销修改（已经commit到版本库，但还没有推送到远程仓库）
git reset --hard HEAD^  //版本回退一次
git reset --hard HEAD^^ //版本回退两次
git reset --hard HEAD~n //版本回退n次
git reset --hard 版本id  //回退到指定版本号

git checkout 版本id 文件名  //将某个文件恢复到某个版本时的状态
```
## 2.7 删除文件
```bash
rm filename          //从目录中删除文件

git rm filename      //删除暂存区和工作区文件
git commit –m “注释”  //将删除提交版本库
//执行这两个命令就可以从版本库中删除文件，如果不想删除 git checkout filename撤销删除
   
git rm -f filename   //如果在commit后，进行了修改且已经add到暂存区，则使用-f选项强制删除
git rm --cached filename   //只删除暂存区文件，不删除工作区文件
```
## 2.8 移动文件和重命名
```bash
git mv rsc dest //移动文件和重命名
```
# 3 分支管理
```bash
git checkout -b 分支名  //创建并切换，相当于以下两条命令：

git branch 分支名       //创建分支
git checkout 分支名     //切换到分支

git branch              //列出所有分支，当前分支前面会标一个*号
git show-branch         //查看分支信息

git merge 分支名 //用于合并指定分支到当前分支。在之前应切换到主分支。默认使用fast forward
git merge --no-ff -m "merge with no-ff" 分支名 //禁用fast forward，避免删除分支时丢掉分支信息。
    
git branch -d 分支名    //删除已经合并的某分支
git branch -D 分支名    // 强制删除未合并的分支

//HEAD指针指向当前分支，master指针指向主分支。
```
# 4 工作快照
```bash
git stash        //把当前工作现场“储藏”起来，等以后恢复现场后继续工作。

git stash list   //查看快照

git stash pop    //恢复现场并删除快照，相当于以下两个命令

git stash apply  //恢复现场但并不删除快照
git stash drop   //删除快照；
```
# 5 远程仓库
## 5.1 查看远程仓库信息
```bash
git remote    //查看远程库信息
git remote -v //查看远程库的详细信息
```
## 5.2 管理远程主机
```bash
git remote show 主机名              //可以查看该主机的详细信息。

git remote add  主机名 地址          //  添加远程主机。 默认主机为origin
git remote rm  主机名                //用于删除远程主机。
git remote rename  原主机名 新主机名  // 用于远程主机改名
```

## 5.3 取回远程分支
```bash
git fetch 远程主机名  [分支名] 
//将远程主机的[某分支]更新全部取回本地。对本地代码没影响。

git branch -r  //查看远程分支， 远程分支用  远程主机/分支名  来访问
git branch -a  //查看所有分支
git checkout -b newBrach  远程主机/分支名   //创建并切换到远程分支
git merge 远程主机/分支名    //将远程分支合并到当前分支
```
## 5.4 推送本地分支
```bash
git push  [远程主机]  [本地分支名]:[远程分支名]   
    //将本地分支的更新，推送到远程主机, 
    //如果省略远程分支名，则表示将本地分支给推送与之存在"追踪关系"的远程分支（通常两者同名）
    //如果该远程分支不存在，则会被新建。
    //如果省略本地分支名，则表示删除指定远程分支，因为这等同于推送一个空的本地分支到远程分支。
    //如果当前分支与远程分支之间存在追踪关系，则本地分支和远程分支都可以省略。
    //如果当前分支只有一个追踪分支，那么主机名都可以省略。
    //如果当前分支与多个主机存在追踪关系，则可用-u选项指定一个默认主机后面就可不加任何参数使用git push.
    
git push --all origin    //将所有本地分支都推送到origin主机。
git push --force origin  //强制推送，覆盖远程分支。
```
## 5.5 取回远程分支并合并
```bash
git pull [远程主机] [远程分支]:[本地分支] 
    //取回远程主机某分支，与本地的指定分支合并。
    //如果远程分支是与当前分支合并，则本地分支可省略。等效于：
    //git fetch 远程主机 分支名
    //git merge 远程主机/分支名 
    //如果当前分支与远程分支存在追踪关系，则本地分支与远程分支都可省略
    //如果当前分支只有一个追踪分支，则远程主机可以省略。
```   
## 5.6 其它
```bash   
git branch --set-upstream 本地分支 远程主机/分支名   //将本地分支追踪到远程分支
git pull --rebase 远程主机 远程分支名:本地分支名      //合并远程分支与本地分支时采用rebase模式。
   
git pull -p    //在本地删除远程已经删除的分支。等同于下面的命令
git fetch --prune origin 
git fetch -p   //在本地删除远程已经删除的分支。

git rebase //把本地未push的分叉提交历史整理成直线

```



# 6 标签
```bash
git tag tagname          //为当前分支创建标签
git tag tagname commitid //为指定commitid打上标签

git tag tagname commitid  -a -m  "标签说明"  //为指定commitid打上带说明信息的标签
git tag tagname commitid  -s -m  "标签说明"   //用私钥签名一个标签

git show tagname       //查看标签信息
git tag 或git tag -l   //查看所有标签

git tag -d tagname //删除本地标签
git push origin :refs/tags/tagname //删除远程标签，如果标签已推送本地和远程都需删除

git push origin tagname  //将指定标签推送到远程
git push origin --tags   //将本地所有标签推送到远程
```
# 7 创建SSH Key。
&emsp;&emsp;首先在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH：

```bash
ssh-keygen -t rsa -b 4096 -C "youremail@example.com"
```
&emsp;&emsp;然后登录github，账户——>设置——>添加SSH key.pub的内容，新建仓库。
 
# 8 开启两步验证后推送失败的解决方法
&emsp;&emsp;首先在github设置中启用Personal Access Token                                                                      
&emsp;&emsp;再输入密码时输入访问令牌而不是账户密码
&emsp;&emsp;如果不想每次都输密码则可以使用: `git config --global credential.helper store`

      
# 9 搭建git服务器

```bash
    sudo apt install git   #安装git
    sudo adduser git  #创建git用户
```
* 创建证书登录：收集所有需要登录的用户的公钥，就是他们自己的id_rsa.pub文件，把所有公钥导入到`/home/git/.ssh/authorized_keys`文件里，一行一个。
* 初始化Git仓库：先选定一个目录作为Git仓库，假定是/srv/sample.git，在/srv目录下输入`sudo git init --bare sample.git`，Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以.git结尾。然后，把owner改为git：`sudo chown -R git:git sample.git`
* 禁用bash登录：出于安全考虑，第二步创建的git用户不允许登录bash，这可以通过编辑/etc/passwd文件完成。找到类似下面的一行：` git:x:1001:1001:,,,:/home/git:/bin/bash   `改为： `git:x:1001:1001:,,,:/home/git:/usr/bin/git-bash`,这样，git用户可以正常通过ssh使用git，但无法登录bash，因为我们为git用户指定的git-bash每次一登录就自动退出。
 
# 10 为项目添加成员
&emsp;&emsp; 仓库-->设置-->member-->权限
&emsp;&emsp; 为分支设置权限， 仓库-->设置-->branches --> Protected branches -->权限



------

&emsp;&emsp;<font color=blue>**_版权声明_**</font>：本文参考了<font color=blue>[《git官方网站》](https://git-scm.com/book/en/v2"点击跳转")。</font><font color=red>未经作者允许，<font color=blue>严禁用于商业出版</font>，否则追究法律责任。网络转载请注明出处，这是对原创者的起码的尊重！！！</font>

------






