> 参考书籍《GitHub入门与实践》[日]大塚弘记

前面的简介和账号创建直接略过，从需要输入代码的部分开始：

## 3 使用前的准备

#### 3.1 设置SSH Key
第一个问题出现了，什么是SSH Key呢？简单搜索了一下还挺复杂，但我的理解是GitHub通过SSH Key与本地的仓库相连接，也就是说我们在本地创建SSH Key然后用它连接到GitHub就行了。打开下载好的GitBash开始输入：

```Bash
$ ssh-keygen -t rsa -C "email@xxx.com"
```
运行完成后设置一下密码。设置完成后系统会告诉你id_rsa和id_rsa.pub的位置以及key的指纹和图像（fingerprint,randomart image）分别是什么。
其中id_rsa是私有密钥，id_rsa.pub是**公开密钥**，GitHub即通过**公开密钥**认证来连接已有仓库。

#### 3.2 添加公开密钥
先通过：

```Bash
$ cat ~/.ssh/id_rsa.pub
```
查看自己的Key，然后在GitHub网页上找到Add SSH Key的入口，Title处输入密钥名称，Key部分粘贴Key内容即可。（注意Key不包括邮件但包括ssh-rsa）完成后会收到提示邮件。

文中提到完成设置之后就可以用私人密钥与GitHub进行认证和通信了，即运行：
```Bash
$ ssh -T git@github.com
```
不过公开密钥和私有密钥的区别是什么呢？我搜了半天也没有找到明确的解释，我猜是公开密钥用来连接本地仓库和远程仓库，私人密钥来确认文件的传输吧。

## 4 基本操作

#### 4.1 本地仓库初始化

这部分是关于本地的Git仓库的基本操作。
首先打开GitBash，建立一个目录并初始化仓库：

```Bash
$ mkdir git-tutorial
$ cd git-tutorial
$ git init
```

成功后会生成.git目录，其内存放的便是管理当前目录内容所需的仓库数据。称之为“工作树”，文件编辑在工作树中进行，仓库内是记录和快照。

创建+初始化仓库完成后，可以查看其状态：

```Bash
$ git status
```

结果会显示①当前位置（分支）②有无可提交内容；显然新建的仓库肯定啥都没有。

于是建立一个README.md文件进行实验：

```Bash
#建立文件
$ touch README.md
$ git status
#把它添加到暂存区
$ git add README.md
$ git status
#保存历史记录
$ git commit -m "first commit"
$ git status
```

这里提到了git commit命令——保存仓库的历史记录，我记得之间用push把本地文件传到GitHub之前也有git commit命令，之前一直没搞清楚commit和push都是上传，为什么要进行两次呢。现在清楚了，commit提交的是“历史记录”，未来可以通过commit命令的记录复原文件。

#### 4.2 关于提交日志

```Bash
$ git log                 #显示以往提交的hash值，提交人和时间。
$ git log --pretty=short  #不显示日期
$ git log README.md       #只显示与指定文件相关的日志
$ git log -p
$ git lop -p README.md    #只看某文件提交前后的差别
```

#### 4.3 查看工作树、暂存区和最新提交的区别

```Bash
$ git diff            #对比工作区(未add)和暂存区(add之后),如果当前暂存区没有文件，就比较当前工作区和版本库(上一次commit之后)的差别
$ git diff HEAD       #对比工作区(未add)和暂存区（add但未commit），分别和版本库(上一次commit之后)
$ git diff --cached   #对比暂存区(add之后)和版本库(上一次commit之后)
```

#### 4.4 分支的操作

```Bash
$ git branch                    #查看所有分支，*为当前所在
$ git checkout -b feature-A     #创建名为feature-A的分支并且切换到它
$ git branch feature-A          #效果等同
$ git checkout feature-A        #效果等同
```

在feature-A分支下对README.md修改，并不会影响master分支下的README.md，这就是Git分支的优点。

```Bash
$ git checkout -                #切换回上一个分支
$ git checkout master           #切回master,等同上一条
$ git merge --no-ff feature -A  #合并分支A至master
$ git log --graph               #图表输出日志
```

* ## 4.3 更改提交

  处理文件的回溯，推进，以及提交信息错误或者是因为出现了简单的错误而不想占用过多历史空间的情况。

```Bash
$ git reset --hard hash值       #回复到某hash值的状态（所有文件）
$ git reflog                    #git log只能以当前状态为终点查看日志，而这可以看所有历史
$ git commit --amend            #修改提交信息（如之前的first commit）（Vim）
$ git rebase -i HEAD~2          #选定当前分支中包含HEAD（最新提交）在内的2个最新历史记录为对象，并在编辑器中打开
```

rebase提交后会出现：
pick hashA aaa
pick hashB bbb
……

若想将bbb的历史记录压缩到aaa里，将第二行的pick改成fixup后保存并关闭编辑器即可。

之后再将aaa分支与master合并，便省去了多合并一次bbb的历史。

## 5 推送至远程仓库  

之前的操作都是针对本地仓库在git上进行的，现在开始学习GitHub上的远程仓库操作。

### 5.1 添加远程仓库

首先在GitHub网站上建立新的仓库，注意一般不勾选自动建立README.md的选项，防止本地仓库与远程仓库失去“整合性”（当然也可以后期强制覆盖）。

然后将该仓库设置成本地仓库的远程仓库：

```{.python .input .Bash}
$ git remote add origin git@github.com:yourname/yourrepositories.git
```

这一步完成后Git会自动将远程仓库的名字设置为origin。

### 5.2 推送至远程仓库

```{.python .input .Bash}
$ git push -u origin master         #将当前分支内容推送给远程仓库的origin的master分支
```

注意此处“-u”参数的作用在于，将origin仓库的master分支设置为本地当前分支的上游（upstream），这使得未来通过git pull从远程仓库获取内容时本地仓库的这个分支可以直接从origin-master获得内容。（只有第一次推送带参数-u，之后不用再带）

本地master以外的分支推送也是同理：

```{.python .input .Bash}
$ git checkout -b feature-B         #切换到分支B
$ git push -u origin feature-B      #推送分支B，即将本地的B分支推送到远程（origin）
```

### 5.3 从远程仓库获取

#### 获取远程仓库

首先**切换到其他目录**下，准备将GitHub上的仓库clone到本地：

```{.python .input .Bash}
$ git clone git@github.com:yourname/yourrepositories.git
```

运行后我出现了Enter passphrase for key '/c/Users/Shinelon/.ssh/id_rsa':，直接输入密码继续。
（在其他目录下也要先git init建立仓库数据，否则这一步会报错！）
完成后本地仓库的master分支与远程仓库的master分支完全相同。

使用-a参数可以同时查看本地仓库和远程仓库的分支信息：

```{.python .input .Bash}
$ git branch-a
```

#### 获取远程某分支

```{.python .input .Bash}
$ git checkout -b feature-X origin/feature-Y
```

以远程仓库的分支Y为来源，在本地仓库中建立X分支。

### 5.4 向远程分支提交修改

一般情况下，如果要对自己/他人的远程分支提交修改，可以如下操作：

```{.python .input .Bash}
$ git checkout -b featrueX origin/featureY          #从远程获取Y分支
$ git diff                                          #在本地对X，即获取到的Y进行修改
$ git commit -am "xxx"                              #加入暂存区并提交，=add+commit
$ git push                                          #将本地X分支推送到Y
```

获取最新分支

```{.python .input .Bash}
$ git pull origin featureY
```

将本地的X通过Y更新到最新。

到这里书中关于推送的内容已经结束了，但是我还有一些问题，所以自己尝试解决一下：

### 5.5 一些问题

**1. 在本地建立仓库直接新建文件夹，然后cd到目录下git init即可；远程则是GitHub网页建立新仓库。通过git clone命令能够将origin（远程）的仓库直接克隆到本地，而本地到origin的push命令却只能push某个分支的内容到远程？**

答：确实是这样，要推送全部分支到origin的话需要使用

```{.python .input .Bash}
$ git push -all origin                             #这样即是将本地所有分支都推送到了origin
```



**2.练习时创建了大量分支，本地和远程都有，要如何删除？**

答：

```{.python .input .Bash}
$ git branch -d <BranchName>                       #删除了本地的BranchName分支
$ git push origin :<BranchName>                    #将一个空分支推送至远程BranchName，效果等同与删除远程BranchName
$ git push origin --delete <BranchName>            #真正的删除指令
```



**3.分支只能在本地之间切换吗？**

答：是这样，而且研究了一下我发现git branch和git checkout虽然在新建分支并切换的时候效果等同，但实际上branch就是一个新建的命令，checkout才是切换至某分支的指令。

更多简单的指令可以看看这个网址，感觉说的还比较详细：[Git教程](https://www.yiibai.com/git/git_push.html)。



**4.今天在本地修改文件后同步到GitHub中，直接使用git commit -am '说明'进行提交，结果出现了一点小bug，部分GitHub中的文件夹丢失了。具体原因还不是很清楚。**

答：改用

```{.python .input .Bash}
$ git add .
$ git commit -m ' '
$ git push
```

另外补充一个git add的使用说明：![](gitadd.png)

## 6 快捷施法

**1.显示快捷键：**

【Shift+/】

**2.通过URL直接查看分支间的差别：**

①分支4-0-stable和3-2-stable之间的差别：

https://github.com/rails/rails/ **compare/4-0-stabel...3-2-stable**

②查看master分支在最近7天内的差别：

https://github.com/rails/rails/ **compare/master@{7.day.ago}...master**

③查看master分支在2013年9月9日与现在的区别
https://github.com/rails/rails/ **compare/master@{2013-09-09}...master**

### 6.1 Issue的使用

**1.给Issue添加标签/里程碑，以及GFM语法。（略）**

**2.GFM的独有功能Tasklist语法：**

> 本日要做的任务

- [x] 早上8点以前起床
- [ ] 看完《怪诞心理学》并还回图书馆
- [ ] 学习时间超过5h
- [ ] 运动至少30min

**3.Close Issue：**

若一个处于OPEN状态的Issue处理完毕，可在该提交中以以下任意格式描述提交信息，从而Close该Issue：

fix#24 fixes#24 fixed#24 /fix可以换成close和resolve

### 6.2 Pull Request

**1.以.diff或.patch格式获取Pull Request：**

如下输入即可：

https://github.com/UserName/RepositoryName **/pull/28.diff** 28为Pull Request的编号#28

https://github.com/UserName/RepositoryName **/pull/28.patch**

**2.在Conversation引用评论：**

选中想要引用的部分，然后按【R】。

**3.Files Changed：**

该标签页可以查看当前Pull Request更改的内容以及前后的差别，但是默认情况下空格的改变也会高亮显示，不方便，可以在URL末尾添加：

“?w=1”

便不再显示空格的区别。