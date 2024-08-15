# Git教程

## 1. 版本控制

### 1.1 简介

你可以把一个版本控制系统（VCS）理解为一个“数据库”，在需要的时候，它可以帮你完整地保存一个项目的快照。当你需要查看一个之前的快照（称之为“版本”）时，版本控制系统可以显示出当前版本与上一个版本之间的所有改动的细节。版本控制系统主要分为两种，集中式版本控制和分布式版本控制。CVS和SVN就是典型的集中式版本控制系统，而Git是目前世界上最先进的分布式版本控制系统。

### 1.2 集中式版本控制

集中式版本控制的仓库是集中存放在中央服务器的，而干活的时候，用的都是自己的电脑，所以要先从中央服务器取得最新的版本，然后开始干活，干完活了，再把自己的活推送给中央服务器。集中式版本控制系统最大的弊端就是必须联网才能工作。在局域网的情况下效率不错，但是通过外放访问的话，由于网络带宽和稳定性的因素将导致效率极低。

![image-20181102142150512](https://ww2.sinaimg.cn/large/006tNbRwgy1fwtp4hl5mgj30my0gmgqh.jpg)

### 1.3 分布式版本控制

分布式版本控制系统没有“中央服务器”的概念，每个人的电脑上都是一个完整的版本库，这样，你工作的时候，就不需要联网了，因为版本库就在你自己的电脑上。既然每个人电脑上都有一个完整的版本库，那多个人如何协作呢？比方说你在自己电脑上改了文件A，你的同事也在他的电脑上改了文件A，这时，你们俩之间只需把各自的修改推送给对方，就可以互相看到对方的修改了。

和集中式版本控制系统相比，分布式版本控制系统的安全性要高很多，因为每个人电脑里都有完整的版本库，某一个人的电脑坏掉了不要紧，随便从其他人那里复制一个就可以了。

![image-20181102142309723](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtp5s77a0j30s20o8k03.jpg)



## 2. Git基础

### 2.1 简介

Git 是用于 Linux内核开发的版本控制工具。与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持，使源代码的发布和交流极其方便。 Git 的速度很快，这对于诸如 Linux kernel 这样的大项目来说自然很重要。 Git最为出色的是它的合并跟踪   (merge tracing)能力。

### 2.2 安装

Git提供了不同系统的版本支持，可从Git官网下载，这里以windows版本为例。

从官网https://git-scm.com下载最新版本，下载后直接执行安装即可。

![image-20181102142600686](https://ww1.sinaimg.cn/large/006tNbRwgy1fwtpfplpboj30jg0fctg6.jpg)

### 2.3 Git Bash

Git Bash是Git在windows下的一个模拟终端，由于Git本身是Linux下的一个工具，如果想在Windows下使用Git，就必须模拟Linux系统的环境。这就是Git Bash的作用。

接下来的Git的学习都将在此终端进行。双击桌面的Git Bash图标，即可打开Git终端。

![image-20181102142729850](https://ww1.sinaimg.cn/large/006tNbRwgy1fwtpflgashj314g0k6wg4.jpg)

### 2.4 添加用户信息

因为Git是分布式版本控制系统，因此每台装有git的主机都需要有自己的名字。这时需要在git配置中添加一个用户名和Email地址。(--global参数表示全局设置，适用于所有仓库)

在Git Bash中输入以下命令：

1） $ git config --global user.name "名字"

在Git的全局配置中添加一个用户名

2）$ git config --global user.email "邮箱地址"

在Git的全局配置中添加一个邮箱

3）可以使用git config --list查看全局的配置信息，当中可以看到你所添加的用户名和邮箱信息

![image-20181102143049812](https://ww1.sinaimg.cn/large/006tNbRwgy1fwtpff6p11j31b20ii11p.jpg)

### 2.5 创建本地版本库

创建完用户信息之后，接下来我们就可以开始创建一个本地的版本库了。

1）在任意一个磁盘中新建一个目录，例如：在D盘中创建一个文件夹叫mygit。

2）在Git Bash中进入此文件夹。

![image-20181102143214464](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtpf8uc7yj31780i6ad2.jpg)

3）使用git init命令将mygit目录初始化一个空的本地版本库

![image-20181102143325147](https://ww2.sinaimg.cn/large/006tNbRwgy1fwtpghecl7j317k0ikgrg.jpg)

OK，这样我们就创建了一个本地版本库，只不过这个库的工作区中没有任何的内容，但是有一个隐藏文件夹“.git”。这个文件夹就是Git的版本库。

（备注：在Git中是有版本库、工作区、暂存区这几个概念，这部分内容将在后面进行详细介绍）

### 2.6 添加文件到版本库

版本库有了，那么我们就可以向版本库中添加文件了。

1） 在mygit目录中创建一个文本文件（例如hello.txt）并填写一些内容

2）在Git Bash中使用命令git add hello.txt

3）再使用命令git commit -m "提交说明" 提交到版本库中

![image-20181102143451688](https://ww1.sinaimg.cn/large/006tNbRwgy1fwtphzycm8j31aa0lotii.jpg)

### 2.7 修改文件

接下来对hello.txt文件进行修改，再添加一些内容。然后使用git status命令进行查看。git status命令可以让我们时刻关注仓库当前的状态。

![image-20181102143554218](https://ww3.sinaimg.cn/large/006tNbRwgy1fwtpj3pwimj31ia0s0gtz.jpg)

红色的部分告诉我们hello.txt的文件已经修改，但是还没有提交到仓库。

还可以使用git diff命令查看修改前和修改后的内容。

![image-20181102143644863](https://ww3.sinaimg.cn/large/006tNbRwgy1fwtpjy21ygj31bs0sqaho.jpg)

修改完后还是使用git add和git commit命令提交到本地版本库中。

![image-20181102143719793](https://ww1.sinaimg.cn/large/006tNbRwgy1fwtpkk11n4j31d40my45j.jpg)

我们再次使用git status命令查看版本库当前的状态，这时你会发现刚才修改的内容已经提交到版本库中，没有任何可提交的内容了。

![image-20181102143748741](https://ww3.sinaimg.cn/large/006tNbRwgy1fwtpl1pgr3j31c60gy78v.jpg)

### 2.8 工作区域版本库

**工作区（Working Directory）**

在之前创建的mygit目录就是工作区，我们创建的任何文件都是存放在工作区中。

![image-20181102143852893](https://ww3.sinaimg.cn/large/006tNbRwgy1fwtpm5ukodj31500hqdhf.jpg)

**版本库（Repository）**

工作区有一个隐藏目录“.git”，这个就是Git的本地版本库。

![image-20181102143930748](https://ww2.sinaimg.cn/large/006tNbRwgy1fwtpmtnn7ej313y0hkjt0.jpg)

说明：

Git的版本库里存了很多内容，其中最重要的就一个是暂存区（stage），还有Git为我们自动创建的第一个主分支master，以及指向master的一个指针叫HEAD（HEAD还可以指向其他的分支，作用就是做分支切换）。

![image-20190929082956846](https://tva1.sinaimg.cn/large/006y8mN6gy1g7g3ysaqhvj314s0ou47m.jpg)

我们把文件往Git版本库里添加的时候，是分两步执行的：

第一步是用git add把文件添加进去，实际上就是把新建或修改的文件添加到暂存区。

第二步是用git commit执行提交，实际上就是把暂存区的所有内容提交到当前master分支。

### 2.9 撤销修改

如果我们想撤销暂存区或者工作区的操作，可以使用git restore命令。

1.撤销暂存区的修改

命令：git restore --staged <文件名>

例如：git restore --staged hello.txt

![image-20190927110424063](https://tva1.sinaimg.cn/large/006y8mN6gy1g7dwad47b3j30p705pq38.jpg)

撤销暂存区的内容并不会影响工作区

2.撤销工作区的修改

命令：git restore <文件名>

例如：git restore hello.txt

![image-20190927110619501](https://tva1.sinaimg.cn/large/006y8mN6gy1g7dwcd7q77j30p305bt8z.jpg)

撤销工作区会将文件内容撤销回修改前的状态

### 2.10 版本回退

可以使用git log命令查看最近的提交日志列表，会有相应的版本号、用户信息、提交时间和提交说明等内容，后续可以再根据这些版本号进行版本回退。

![image-20181102144125538](https://ww2.sinaimg.cn/large/006tNbRwgy1fwtpotgv80j31aq0vg199.jpg)

说明：

commit：提交的版本号

Author：提交人信息

Date：提交时间

最后的是提交说明信息

- ##### 使用git reset 回退版本

reset命令有三个参数，这三个参数将导致回退的内容将在不同的作用域中（工作区、暂存区、版本库）。

--soft ：表示回退版本库的内容，此时回退的内容会在暂存区保存，相当于执行了git add命令将回退的内容

加入到暂存区，可以继续使用git commit命令再次提交回版本库。

--mixed ：这个参数表示回退版本库以及暂存区的内容，工作区不受影响。此时回退的内容就像在工作区新

建了内容一样，但为执行git add命令加入暂存区。可以执行git add 然后再执行git commit提交回版本库（默认选项）

--hard ：这个参数表示表示一并回退版本库、暂存区、工作区的内容。

例如：git reset --soft HEAD^  表示在版本库的master分支中回退到上一个版本

HEAD说明：

1） HEAD表示回退到当前最新版本

例如：git reset --mixed HEAD  (回退到当前最新的版本)

2） HEAD^表示回退到上一个版本

例如：git reset --mixed HEAD^  (回退到上一个版本)

3） HEAD~2表示回退到上两个版本（其中2可以替换成其他数字，表示回退到上几个版本）

例如：git reset --mixed HEAD~2  (回退到上两个版本)

版本号说明：

可以将HEAD替换成版本号，表示回退到指定的版本。版本号不一定需要全部输入，只需要输入前面几个字符即可。

例如：git reset --mixed 版本号 （回退到指定版本，并将版本内容重置到暂存区）

- ##### 使用git revert回退版本

revert和reset的区别：revert是回退指定版本的内容并提交一个新的commit，不影响之前提交的内容。而reset是回退到指定版的同时，删除该版本之后的所有提交。

例如，新建一个hello.txt并添加内容“AAAAA”，然后提交到版本库。

![image-20181210220433280](https://ww3.sinaimg.cn/large/006tNbRwgy1fy201k706wj30qn075wf3.jpg)

修改hello.txt再次添加内容“BBBBB”，然后提交到版本库

![image-20181210220543398](https://ww1.sinaimg.cn/large/006tNbRwgy1fy202s7t3cj30ot06ct97.jpg)

此时文件的内容为

![image-20181210214713765](https://ww2.sinaimg.cn/large/006tNbRwgy1fy1zjj880tj30nw04hdfs.jpg)

然后通过git log命令查看提交日志，已存在两次提交记录。

![image-20181210220619836](https://ww1.sinaimg.cn/large/006tNbRwgy1fy203erpesj30qw0ap3zh.jpg)

使用git revert HEAD回退到内容为“AAAAA”的版本，然后查看log日志，此时会发现git新建了一个提交，并将HEAD指向了这个提交。而之前的提交并内有删除。

![image-20181210225326416](https://ww1.sinaimg.cn/large/006tNbRwgy1fy21gfnbi9j30vr0iimz6.jpg)

再次查看hello.txt发现内容已经已经回退。

![image-20181210225641301](https://ww3.sinaimg.cn/large/006tNbRwgy1fy21jta2xfj30ny03mdfr.jpg)

### 2.11 删除文件和文件夹

1） 使用git rm "文件名" 命令删除工作区中的文件

例如：git rm hello.txt

![image-20181102145205263](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtpzvngyij313w0mcgtr.jpg)

注意：删除之后，记得要将此操作commit到版本库中，因为此时删除的动作还只是在暂存区中，并未同步到版本库。

2) 使用git rm 目录名 -r -f 删除文件夹及其下所有的文件

例如：git rm demo -r -f

![image-20181102145244004](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtq0jzisvj313y0lstgj.jpg)

注意：执行完同样需要commit到版本库中

### 2.12 本地分支

1）分支的作用

分支在实际中有什么用呢？假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。有了分支，你可以创建一个属于你自己的分支，别人看不到，其他人将继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的主分支上，这样，既安全，又不影响别人工作。

2）分支的概念

每次提交，Git都把它们串成一条时间线，这条时间线就是一个分支。截止到目前，只有一条时间线，在Git里，这个分支叫主分支，即master分支。HEAD严格来说不是指向提交，而是指向master，master才是指向提交的，所以，HEAD指向的就是当前分支。

![image-20190928153820738](https://tva1.sinaimg.cn/large/006y8mN6gy1g7g3p97an1j30y40eoq3q.jpg)

每次提交，master分支都会向前移动一步，这样，随着你不断提交，master分支的线也越来越长：

![image-20190928153847902](https://tva1.sinaimg.cn/large/006y8mN6gy1g7g3qgqba5j30ko0g0act.jpg)

当我们创建新的分支，例如dev时，Git新建了一个指针叫dev，指向master相同的提交，再把HEAD指向dev，就表示当前分支在dev上：

![image-20190928153904637](https://tva1.sinaimg.cn/large/006y8mN6gy1g7g3s9ouzgj30z40moq87.jpg)

从现在开始，对工作区的修改和提交就是针对dev分支了，比如新提交一次后，dev指针往前移动一步，而master指针不变：

![image-20190928153922592](https://tva1.sinaimg.cn/large/006y8mN6gy1g7g3u2fwlqj313u0jidkk.jpg)

假如我们在dev上的工作完成了，就可以把dev合并到master上。Git怎么合并呢？最简单的方法，就是直接把master指向dev的当前提交，就完成了合并：

![image-20190928153944696](https://tva1.sinaimg.cn/large/006y8mN6gy1g7g3w49frkj314w0kitfa.jpg)

合并完分支后，甚至可以删除dev分支。删除dev分支就是把dev指针给删掉，删掉后，我们就剩下了一条master分支：

![image-20190928154002253](https://tva1.sinaimg.cn/large/006y8mN6gy1g7g3xfrmvkj315a0citcw.jpg)

3) 分支的使用

创建分支: git branch 分支名

![image-20181102145952462](https://ww2.sinaimg.cn/large/006tNbRwgy1fwtq80lghyj31760fs42g.jpg)

切换分支: git checkout 分支名

![image-20181102150112697](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtq9ed77ej31680k4q92.jpg)

说明：也可以使用 "git checkout -b 分支名" 直接创建并切换分支

查看分支: git branch

![image-20181102150149194](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtqa1atpmj316u0fi78q.jpg)

说明：*号表标识前正在操作的分支  

合并分支：git merge 分支名

我们先切换到master分之下，然后合并dev分支的内容

![image-20181102150243738](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtqayycoqj31740imq9v.jpg)

这样，我们就把dev分支的内容合并到了master分支上

删除分支：git branch -d 分支名

![image-20181102150315198](https://ww1.sinaimg.cn/large/006tNbRwgy1fwtqbilu99j317a0hgn2l.jpg)

### 2.13 版本冲突

在合并分支时，并不一定都成功。这是因为当两个分支都做了修改并且都提交到了版本库中，这样在合并的时候可能就会产生版本冲突。

场景案例:

1) 在master主分支中对A文件进行修改并提交

![image-20181102150406660](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtqcewjsej31b40ks0zu.jpg)

2）切换到dev分支也对A文件进行内容修改并提交

![image-20181102150432053](https://ww1.sinaimg.cn/large/006tNbRwgy1fwtqcugijuj31b40p44a8.jpg)

3）切换回master分支合并dev分支的内容，这是就产生版本冲突，合并失败

![image-20181102150500549](https://ww3.sinaimg.cn/large/006tNbRwgy1fwtqdbsh4mj31be0j0jzp.jpg)

原因分析：

因为master分支和dev分支各自都分别有新的提交。这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就导致了冲突的原因。

解决办法：

我们查看文件内容，Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容。

![image-20181102150540578](https://ww1.sinaimg.cn/large/006tNbRwgy1fwtqe1f654j30ts0nyadd.jpg)

我们手动将内容修改合并后再进行提交以解决此问题。

![image-20181102150622419](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtqerqu1tj30t00l4acp.jpg)

最后提交合并内容

![image-20181102150728424](https://ww1.sinaimg.cn/large/006tNbRwgy1fwtqfw2gzkj31860jcq9o.jpg)

### 2.14 内容比较

git diff 用于比较内容的差别。

比较规则：

先比较暂存区内容，如果暂存区为空时则比较当前版本库中HEAD所指向的分支内容。

1）比较当前的工作区和当前分支的内容差别

例如：git diff 

![image-20181102150830781](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtqgyxmm3j316a0nmdoa.jpg)

说明：git diff 默认是比较所有文件的内容。也可以指定文件名，用于比较当前文件的内容。例如：git diff hello.txt

2) 比较当前的工作区和指定分支的内容差别

例如: git diff 分支名

![image-20181102150904516](https://ww1.sinaimg.cn/large/006tNbRwgy1fwtqhjzl5zj31600qmwnz.jpg)

说明：同样可以指定具体某个文件进行内容比。例如：git diff dev hello.txt

3) 比较两个分支之间的内容差别

例如：git diff 分支名..分支名

![image-20181102150934126](https://ww1.sinaimg.cn/large/006tNbRwgy1fwtqi2u7bjj31720sogwi.jpg)

说明：同样可以指定具体某个文件进行内容比。例如：git diff master..dev hello.txt

4) 比较当前工作区与当前分支上一个版本的内容

例如：git diff HEAD^

![image-20181102151003102](https://ww2.sinaimg.cn/large/006tNbRwgy1fwtqim6agzj31780tgk2i.jpg)

说明：还可以比较上n个版的内容。例如：git diff HEAD~2 (比较上两个版本的内容)。并且可以指定文件名，比较个某个文件内容。例如：git diff HEAD~2 hello.txt

5) 比较当前工作区与指定分支上一个版本的内容

![image-20181102151059740](https://ww2.sinaimg.cn/large/006tNbRwgy1fwtqjkapwnj318w0w6n96.jpg)

说明：也可以比较上n个版的内容和比较个指定文件的内容。同上

### 2.15 保存工作状态

有这样一种情况，例如你在主分支master下进行一些代码的内容修改，突然由于紧急突发原因不得不让你立马切换至dev分之下进行一些bug修复。但是当你要切换到dev分支时，不得不先提交master下的内容，否则无法进行切换。如下图：

![image-20181102151151522](https://ww3.sinaimg.cn/large/006tNbRwgy1fwtqkhdfd4j31fe0gagrd.jpg)

这时，可以利用git来帮我们先保存master分支下的工作状态，然后就可以安心切换到dev分之下进行工作，等完成之后再切换回master分支，然后再恢复回之前的工作状态继续工作。这时可以使用git stash命令。

1）保存当前工作状态: git stash

![image-20181102151300381](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtqloab3ej31fi0b2792.jpg)

保存后，就可以切换到其他分支

![image-20181102151326634](https://ww2.sinaimg.cn/large/006tNbRwgy1fwtqm4sarjj319s0hawjv.jpg)

在其他分支工作完后，再切换回master分支

2）查看保存的工作状态列表：git stash list

![image-20181102151410528](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtqmvrjk6j319o0d8gpl.jpg)

说明：{0}是保存的状态号

3）恢复状态：git stash pop stash@{状态号}

例如：git stash pop stash@{0}

![image-20181102151445983](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtqnhk25wj31a60h2n5b.jpg)

这样，就成功恢复到之前保存的状态中

### 2.16 标签

发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针，所以，创建和删除标签都是瞬间完成的。

**标签的好处**

当我们要对某个版本进行打包发布时，要找到对应的版本号（也就是commit ID），但是

版本号是一串并不好记的字符串。如：63c4b0c15f897ab4b1c50519f2946af7c5545e8e

这样的字符串,这时标签就非常有用了。因为标签是一个很容易记的名字，一个标签跟一个版本号是绑定在一起的，所以只要根据标签查找版本号就可以了。

1）创建标签：git tag 标签名

![image-20181102151703319](https://ww1.sinaimg.cn/large/006tNbRwgy1fwtqpvx6imj319k0fcq78.jpg)

说明：默认标签是打在最新提交的版本号上的，也可以打在指定的版本号上。例如：

git tag v1.0 63c4b0c (只要写上版本号的前几位即可)

2）查看所有标签：git tag

![image-20181102151752592](https://ww3.sinaimg.cn/large/006tNbRwgy1fwtqqqbtnlj31ae0hsdjn.jpg)

3）根据标签查看版本内容：git show 标签名

![image-20181102151831225](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtqrezdf3j31b0116k2c.jpg)

4）删除标签：git tag -d 标签名

![image-20181102151912025](https://ww4.sinaimg.cn/large/006tNbRwgy1fwtqs4kyygj31ay0j6teq.jpg)

## 3. 使用Github

### 3.1 简介

GitHub 是一个面向开源及私有软件项目的托管平台，因为只支持 Git 作为唯一的版本库格式进行托管，故名 GitHub。它于 2008 年 4 月 10 日正式上线，除了 Git 代码仓库托管及基本的 Web 管理界面以外，还提供了订阅、讨论组、文本渲染、在线文件编辑器、协作图谱（报表）、代码片段分享（Gist）等功能。作为开源代码库以及版本控制系统，Github拥有超过900万开发者用户。随着越来越多的应用程序转移到了云上，Github已经成为了管理软件开发以及发现已有代码的首选方法。

Github工作原理：

![image-20181102152205329](https://ww2.sinaimg.cn/large/006tNbRwgy1fwtqv58na7j311y0sge81.jpg)

### 3.2 注册Github账号

访问www.github.com官网，在首页填写相应信息，点击Sign up for GitHub

![image-20181102152424022](https://ww1.sinaimg.cn/large/006tNbRwgy1fwtqxkbeo0j31io0r8e81.jpg)

### 3.3 创建远程版本库

1）登陆到github后创建新的仓库等操作

![image-20181103061019608](https://ww4.sinaimg.cn/large/006tNbRwgy1fwugjce7gjj31kw0qlqg9.jpg)

2）点击Start a Project开始创建一个项目,接下来会跳转到新建仓库信息页面

![image-20181103061111405](https://ww4.sinaimg.cn/large/006tNbRwgy1fwugk83a15j31kw0unn5e.jpg)

填写仓库名（Repository name），其他可以默认值即可。

说明：

Repository name ：要创建的仓库名称

Description ： 仓库的简要描述

Public ： 是否是公开的仓库（所有人都能访问）

Private ： 是否是私有的仓库（仅限个人访问）

Initialize this repository with a README : 

是否在初始化仓库时创建一个README说明文件

3）点击Create repository，创建成功后动跳转到当前仓库信息页面，这样我们就新建好了一个远程仓库

![image-20181103061235532](https://ww3.sinaimg.cn/large/006tNbRwgy1fwuglphhg6j31kw0uuwl9.jpg)

### 3.4 将本地库同步到远程版本库

在上一节中，我们在Github上创建了一个空的远程仓库，但仓库是空的，没有任何内容，在这一节中，我们将在本地创建一个本地库，并把它同步到远程仓库中

1）创建本地版本库，并添加内容到本地版本库中

![image-20181103061408382](https://ww1.sinaimg.cn/large/006tNbRwgy1fwugnao6ipj31as0om142.jpg)

2）将本地仓库同步到远程仓库

使用以下命令：

语法: git remote add <远程主机名> <github版本库地址>

例如：git remote add origin https://github.com/baiczsy/myrepo.git

![image-20181103061516178](https://ww3.sinaimg.cn/large/006tNbRwgy1fwugogo3paj31bk090jv9.jpg)

说明：在同步远程仓库时需要给这个远程仓库指定远程主机名（如上面的origin）。这样在后续的更新或提交过程中都会使用到这个主机名来操作远程仓库。

可以使用git remote命令进行查看仓前的远程主机名

![image-20181103061554032](https://ww3.sinaimg.cn/large/006tNbRwgy1fwugp4mlg8j31bs07640p.jpg)

我们也可以在add同步之后去修改远程主机名，使用以下命令：

git remote rename <原主机名> <新主机名>

例如：git remote rename myrepo origin

![image-20181103061632120](https://ww3.sinaimg.cn/large/006tNbRwgy1fwugps21l0j31c20aetcg.jpg)

3）同步完成之后，就可以使用push命令将本地仓库的内容推送到远程版本库的指定分支上

语法：git push <远程主机名> <分支名>

例如：git push myrepo master

![image-20181103061706821](https://ww3.sinaimg.cn/large/006tNbRwgy1fwugqe4k35j31bs0k6wnr.jpg)

### 3.5 提交与更新

1）推送本地分支内容到远程版本库的分支中（提交）

git push <远程主机名> <本地分支>:<远程分支>

例如：

git push myrepo master:master

说明：默认远程分支就是master，所以后面的:master可以省略，但是如果要push到远程其他的分支那么就必须指定。

2）拉取远程版本库的分支更新到本地分支并执行合并（更新）

git pull <远程版本库别名> <远程分支>:<本地分支>

例如:

git pull myrepo master:master

说明：在拉取的时候，分支的名称顺序正好是和push相反的，如果你正在使用本地的master分支操作，那么可以省略后面的:master，表示拉取更新并合并到当前的分支，如果要合并到本地其他的分支就必须指定。

### 3.6 clone远程版本库到本地

将Github远程仓库clone到本地，使用以下命令：

语法：git clone <github版本库地址>

例如：git clone https://github.com/baiczsy/evergreenframework.git

![image-20181103061821507](https://ww2.sinaimg.cn/large/006tNbRwgy1fwugrnn7yhj31kw0fgk3b.jpg)

说明：clone的时候，默认的远程主机名为origin。当然，也可以修改远程主机名,只要加上 "-o 新主机名"即可。

例如：clone -o myrepo https://github.com/baiczsy/testrrepo.git 

上面的myrepo就是重新指定的远程主机名。

![image-20181103061857905](https://ww1.sinaimg.cn/large/006tNbRwgy1fwugsbh6loj31kw0man8j.jpg)

这样我们就将远程版本库clone到本地了

### 3.7 创建远程分支

1）先创建一个本地分支

![image-20181104140456925](https://ww1.sinaimg.cn/large/006tNbRwgy1fwvzvhkh9pj31hu0cw0wi.jpg)

可以查看一下分支状态，可以看到当前本地分支是dev

![image-20181104141356615](https://ww1.sinaimg.cn/large/006tNbRwgy1fww04tqkitj31hw0eo41r.jpg)

2）将本地分支push到远程服务器

![image-20181104143540204](https://ww4.sinaimg.cn/large/006tNbRwgy1fww0rfoagbj31hu0ewtds.jpg)

说明：“:”号右边的是远程分支名称，push的时候会自动在远程仓库创建一个

dev的分支，并且这个名称并不要求和本地分支同名。

例如：git push origin dev:remote_dev

### 3.8 删除远程分支

最简单的方式就是推送一个空的本地分支到远程分支，其实就相当于删除远程分支。

![image-20181104144114053](https://ww1.sinaimg.cn/large/006tNbRwgy1fww0x7w611j31hw0dkwir.jpg)

也可以使用"git push origin --delete 远程分支名"来删除

![image-20181104144324660](https://ww1.sinaimg.cn/large/006tNbRwgy1fww0zhccd6j31hu0d80x2.jpg)

### 3.9 使用SSH协议登陆

![image-20200402173237603](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200402173237603.png)

github支持https和ssh两种安全连接协议。默认使用https进行安全连接，我们也可以选择使用ssh。而SSH简单点说就是一种网络协议，用于计算机之间的加密登录。如果一个用户从本地计算机，使用SSH协议登录另一台远程计算机，我们就可以认为，这种登录是安全的，即使被中途截获，密码也不会泄露。ssh支持的秘钥类型有很多，如RSA、DSA等等，下面使用RSA的加密类型，它是一种非对称的加密协议，如果对加密算法和原理不了解，请自行去网上查阅相关资料。

1）使用ssh-keygen命令创建秘钥

例如：ssh-keygen -t rsa -C "youremail@qq.com"

![image-20181103080204440](https://ww1.sinaimg.cn/large/006tNbRwgy1fwujrlv0ilj31kw0w8nao.jpg)

说明：

-t 表示加密类型，这里加密类型指定为rsa

-C 表示添加秘钥的描述，可以指定自己的email地址

一路回车后，最终会在当前用户目录下创建一个.ssh的隐藏文件夹，进入文件夹进行查看，可以发现此时已经创建了一个id_rsa私钥和一个id_rsa.pub公钥

![image-20181103080757755](https://ww4.sinaimg.cn/large/006tNbRwgy1fwujxpt6icj30ui05y3ze.jpg)

2）将公钥添加到github中

可以使用cat命令查看公钥中的内容，然后选中进行复制

![image-20181103082100395](https://ww4.sinaimg.cn/large/006tNbRwgy1fwukbas7e7j31kw0b8gsx.jpg)

在github的个人设置中点击SSH and GPG keys菜单

![image-20181103081245557](https://ww3.sinaimg.cn/large/006tNbRwgy1fwuk2qjz0gj30da0h6mxv.jpg)

然后点击右上角的New SSH key

![image-20181103081542952](https://ww2.sinaimg.cn/large/006tNbRwgy1fwuk5slmldj316k0fqq5m.jpg)

填写Title，并将复制的公钥粘贴到key一栏中，点击Add SSH key完成添加

![image-20181103081646945](https://ww2.sinaimg.cn/large/006tNbRwgy1fwuk6wgsoej315u0ladh7.jpg)

后续当我们克隆一个仓库或者执行更新和提交操作时就可以选择使用ssh

![image-20181103083014117](https://ww2.sinaimg.cn/large/006tNbRwgy1fwukqgi3vdj30jk0baq4e.jpg)

### 3.10 团队协作

1）登陆GitHub后，点击右上角头像弹出下拉菜单，然后点击Settings

![image-20181103062104118](https://ww4.sinaimg.cn/large/006tNbRwgy1fwuguhwy3lj30l60kmtae.jpg)

2）进入设置页面后，点解左边菜单栏的Organizations选项

![image-20181103062154057](https://ww3.sinaimg.cn/large/006tNbRwgy1fwugvcxq9cj31kw0tu43x.jpg)

3）点击右上角的New organizations新建一个组织

![image-20181103062229513](https://ww1.sinaimg.cn/large/006tNbRwgy1fwugvzdwfzj31d80ya7e5.jpg)

4）填写Organization name（组织名称）以及Billing email（邮箱地址，任意）,然后点击Create organization创建组织

![image-20181103062303634](https://ww4.sinaimg.cn/large/006tNbRwgy1fwugwkh29hj31bw0ssgty.jpg)

5）点击Finish完成。会自动跳转到以下页面，你会看到People组织中已经有你一个人，可以点击右下角的Invite someone搜索你想要加入组织的人的账号，然后点击添加，等待对方邮件确认即可加入。接着点击Create a new repository创建一个组织仓库即可，创建仓库的步骤与之前一样。

![image-20181103062358566](https://ww1.sinaimg.cn/large/006tNbRwgy1fwugxj4bwmj31kw0m1q6z.jpg)