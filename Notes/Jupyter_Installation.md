> 1年前(2019)还完全是小白时摸索的笔记，大概率用不上了，但是还是不舍得删掉。

# Windows

在接触JupyterNotebook之前我有折腾过一阵子Anaconda，所以理所当然地打算通过Anaconda来使用。

首先Anaconda的Navigator里自带有JupyterNotebook，点击之后也能顺利使用。但是打开Jupyter之后的界面里有很多乱七八糟的文件，很不舒服，而且显然这是在默认路径底下，如果我在这样的环境下开始操作，怕是以后都找不到代码写到哪里去了。

所以第一步是**更改Jupyter的工作/储存环境**。

网上大部分的教程是先去找Jupyter的安装文件夹，如D:\Anaconda3\Scripts。

然后输入命令让Jupyter产生配置文件Jupyter_notebook_config.py。具体命令是打开cmd后：
```
cd D:\Anaconda3\Scripts
jupyter notebook --generate-config
```
执行完成后在C:\Users\yourname\.jupyter下找到刚才生成的.py文件即可。

**但是**我在.jupyter底下并没有找到这个文件！查了很多资料也没搞清楚原因，最有可能的结果我感觉是Anaconda和Python的版本问题或者我什么时候不小心删掉了关键文件（？）。所以选择了简单的解决办法，用conda重装一次Jupyter Notebook。

打开Anaconda Prompt，输入：
```
conda install jupyter notebook
```
系统提示有很多组件需要更新版本，直接输入y后enter。

再重复上面的产生配置文件操作，顺利找到Jupyter_notebook_config.py文件，然后通过Ctrl+F搜索找到：
```
#c.NotebookApp.notebook_dir=''
```
在''内填入你希望Jupyter Notebook的默认工作路径即可，例如我是：F:\_the_4th\Jupyter_Notebook。

最后再将Jupyter Notebook的快捷方式右键属性，把“目标”项中的%USERPROFILE%修改成刚才的默认工作路径就行啦。

# Linux

目前使用JupyterNotebook都是通过Windows下安装的Anaconda3进行的，每次启动都要小等一会儿而且会有一个命令行窗口不能关闭，否则会断开网页上Jupyter和本地的连接。更惨的是用了没多久我的老旧游戏本居然会发烫，不得不开启强劲的游戏模式予以冷却，着实有点讽刺。
考虑到既然自己已经买了几个月的云服务器，把Jupyter装到上面24小时运行，我打开就能用岂不是美滋滋。

### 1.安装Anaconda3

之前一直是以root的身份在操作系统，总担心一个不小心rm rf/*了。所以先切换一下身份...

```{.python .input .Bash}
$ su - sayori                         #加 - 符号同时切换到新用户的工作环境中
$ wget https://repo.continuum.io/archive/Anaconda3-2019.07-Linux-x86_64.sh
```

2019年7月25日的版本，大概600+MB，漫长地等待……
（突然想起来之前自己装过Python3.7，系统自带Python2.7.5，不知道再装Anaconda会不会出现什么冲突）

安装完成后，运行

```{.python .input .Bash}
$ bash Anaconda3-2019.07-Linux-x86_64.sh
```

结果出现了错误：/bin/bash: cannot execute binary file。更换到root用户后正常。
一路enter+yes,安装到/root/anaconda3。

完成后直接运行anaconda会出现错误：commend not
found。需要添加环境变量：

```{.python .input .Bash}
$ echo 'export PATH=/home/root/anaconda3/bin:$PATH' >> ~/.bashrc  #在anaconda的bin目录加入PATH
$ source ~/.bashrc                                    		  #更新配置文件
$ conda --version                                     		  #检验是否安装成功
```

之后我在网上了解到因为Anaconda的服务器在国外，一般要转成国内的镜像源否则conda下载速度会很慢，那就转一下吧：

```{.python .input .Bash}
$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
$ conda config --set show_channel_urls
```

都处理完了之后，用户名前面多了个(base)。查了一下在.bashrc文件中加入conda
deactivate命令可以消除字样，但是怎么感觉这个命令像是把anaconda给关了呢……试一下：

```{.python .input .Bash}
$ echo 'conda deactivate' >> ~/.bashrc
```

重启服务器后(base)字样消失。输入anaconda后继续出现commend not found错误，姑且留着(base)看看。
接下来处理Jupyter，网上说在/root/anaconda3/下有.jupyter文件，里面有.py的配置文件。但是我进去看了下并没有，所以之后的操作和Windows上有些类似，先创建配置文件：

```{.python .input .Bash}
$ jupyter notebook --generate-config --allow-root
#非root用户去掉后面的--allow-root
```

出现：Writing default config to: /root/.jupyter/jupyter_notebook_config.py表面成功了。
然后安装Jupyter相关的插件：

```{.python .input .Bash}
$ pip install jupyter_nbextensions_configurator
$ pip install jupyter_contrib_nbextensions
$ jupyter nbextensions_configurator enable --user
$ jupyter contrib nbextension install --user
```

然后更改密码：

```{.python .input .Bash}
$ ipython
in [1]: from notebook.auth import passwd
in [2]: passwd()                              #输完会获得一个密钥，复制一下保存
```

接下来编辑配置文件jupyter_notebook_config.py：

```{.python .input .Bash}
$ vim /root/.jupyter/jupyter_notebook_config.py

c.NotebookApp.ip = '*'            #即对外提供访问的ip
c.NotebookApp.port=8888                      #即对外提供访问的端口
c.NotebookApp.open_browser = False            #False即启动不打开浏览器
c.NotebookApp.password = u'sha1:dcae22e6f7b0:e2dd5aa3e1731c05e6fd611a49d616dfcd215bf8'  #密钥
```

！！！我太南了。
配置完后一直出现各种bug，在这里详细解决一下：

①在命令窗口就报错，端口8888已被占用，然后切换到别的端口却打不开网页？
期间尝试了多次把c.NotebookApp.ip=''换成云服务器的公有ip和私有ip都不顶用，端口port也是换了又换（在云服务器的安全组里可以开放新的端口），最后换成了全新的8889，然而并没有用。
最后我试了下公有ip:8888，能顺利打开之前装好的宝塔面板，但是想不起来账号密码了。来个：

```{.python .input .Bash}
$ bt default
```

看看原来的账号密码，结果又只显示密码不显示账号，还报错说Python的print语法有错误。我推测是刚装了anaconda3带的Python和linux系统自带的Python2有冲突？总之差点连宝塔都登不上去，费劲辛苦找到账号赶紧改了账号密码。
②什么都设置好了，在命令行运行：

```{.python .input .Bash}
$ jupyter notebook --allow-root
```

都能正常显示，但是告诉我running at:http://monika:8889。去浏览器里公用ip:8889——网页无法打开。
之后尝试了很久都打不开，最后灵机一动把宝塔面板的端口换成了8889，Jupyter的换成了8888，然后就成了……
（到底是为什么呢……）
总之是终于运行好了，然后输入密码进入，发现路径乱遭糟的，不行得改成一个新的文件夹：
在之前的.jupyter/jupyter_notebook_config.py里再加一行

```{.python .input .Bash}
c.NotebookApp.notebook_dir ='yourpath'。
```

就OK了。

最后就是完成让JupyterNotebook一直运行的任务：

```{.python .input .Bash}
$ nohup jupyter notebook --allow-root &     #nohup让其不挂断的运行，&维持在后台
#以下是配合后台运行常用的命令
$ jobs                                      #查看当前终端后台运行的任务
$ ps                                        #查看当前的所有进程
$ kill %jobnum
$ kill %PID                                 #结束后台进程
$ fg                                        #将后台命令调至前台继续运行
$ fg %jobnum
```

Jupyter notebook真的好用！以至于我想直接用它来管理GitHub里的所有东西，如何实现呢？通过更改.jupyter/jupyter_notebook_config.py可以在Jupyter中管理整个文件夹，但是打开.md文件时却不能像GitHub一样直接预览。那怎么行？果断安装notedown插件：

```{.python .input .Bash}
$ pip install notedown
$ vim .jupyter/jupyter_notebook_config.py
加上c.NotebookApp.contents_manager_class = ‘notedown.NotedownContentsManager’
```

重启一下Jupyter即可。