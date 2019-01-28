
###  GIT 版本控制

GIT是一个代码版本管理工具，项目开发过程中，代码要不断的维护更新，应用程序也在不断的变得越来越复杂，手动更新维护版本会让我们开发很繁琐，所以我们会使用版本管理工具

市场上流行的版本管理工具：

Git / SVN

Git是分布式的代码版本管理，SVN是集中式的管理

SVN拥有中央服务器，所有的代码版本都管理在中央服务器，一旦中央服务出了问题，影响开发进度

Git没有中央服务器又或者说每一个客户端都可以是一个中央服务器，在客户端上会有代码完整的版本，更方便于多人协作开发。


### Git的使用方式

先去安装git  可以在git bash 和 git cmd 中执行git命令 

使用git管理代码需要先去建立一个git仓库

git init <dirname> 

再去 配置全局的用户名和邮箱
git config --global username''
git config --global email''


然后进行代码的开发，当开发到某一个节点的时候，应该赶紧的保存到本地仓库中并且建立版本

git add <filename || .>

使用git status 命令查看一下目前git的情况

使用 git config --global alias.st status 创建快捷操作 git st == git status

没有问题的话就可以提交到本地仓库 

git commit -m '注释'

git commit -am '注释' = git add . & git commit -m '注释'


每一次提交都会带来一个版本，每一个版本都有一个版本号 HEAD，当我们想要回退版本的时候就可以:

git reset --hard HEAD(前6，7位)

当我们回退了，又提交了之后可能通过git log 看不到某些版本了，git reflog


##### Git分支 branch

当我们系统功能越来越复杂的时候，发现在一个工作区里开发很不方便，可以建立分支来进行模块的开发

也就说，我们可以根据项目的功能模块来建立分支分别进行开发

> master分支是伴随着创建仓库的时候就存在的，本地的主分支

创建分支：

git branch <branchname>

> 创建分支的时候会把git支的代码copy一份

查看分支: 

git branch

切换分支:

git checkout <branchname>

分支的代码开发完成后，需要进行合并

git merge <branchname>

merge动作中包含了commit

当分支开发完成，不需要了的话，可以删掉分支

git branch -d <branch_name>


当A分支上某个位置写的是a代码，B分支上这个位置写的是b代码，然后master合并了B分支，此时，master上这个位置写的是b代码，当我们视图让master合并A分支的时候，就会出现冲突：

master b 

A      a

git会保留冲突拒绝合并

<<<<<<
当前的 b
======
要合并的 a
>>>>>>



### Git多人协作


GitHub使用方式：

1. 注册登录

2. 在客户端生成ssh key 
    
    作用: 客户端（我的电脑）想要和github仓库进行交互的时候依靠两种方式ssh（常用），https

    如果想要使用ssh的方法的时候，就需要让客户端生成一个sshkey并配置在github账号中

    ssh-keygen -t rsa 

3. 需要全局配置用户名和邮箱

    git config --global username....

    注意用户名和邮箱配置成github的用户名和邮箱





在开发项目过程中通常都需要多个人协作开发，在多人协作的时候也需要一个远端仓库来进行中间协调

如何拥有远端仓库：

1. 公司可以自己开发一个git服务器

2. 可以使用开源，现成的远端仓库： GitHub / GitLab / 码云 。。。

假设 “我” 是项目经理，小组里还有一个成员 “Tom”，刚刚开始做项目， “我” 已经搭建好了整个项目环境

“我” 就要创建本地仓库

在这里有两种情况： 

1. 先在github上建立远端仓库，然后克隆到本地，这个时候远端仓库和本地的仓库已经建立好连接，再本次仓库中构建项目

2. 项目已经再一个本地仓库中构建好了, 然后再去创建远端仓库，就需要“我”去建立联系

    git remote add origin git@github.com:Erioser/GP8-LAGOU.git

    然后 git pull origin master // 远端master分支代码拉下来

    结果出现了这样的问题：fatal: refusing to merge unrelated histories

    pull其实执行的是拉下代码并合并，因为远端有readme，本地没有，所以不给咱们合并，要么在本地建立个readme，要么强行pull

    git pull --rebase origin master

    然后 将本地仓库的代码提交到远端仓库

3. 记住： 远端的master分支其实就是我们的发布分支（最终的代码），只有组长的本地master才允许去提交到远端master

4. 邀请Tom加入开发 尤其完成后，Tom会在Tom的本地去clone远端仓库

5. “我” 为Tom分配了任务，Tom准备去开发

    Tom在本地建立一个功能分支去进行开发 (tom-login)，开发完成一个版本之后，注意，不能让tom将自己本地的master和Tom-login合并！

    因为我们保证所有开发者本地的master都和远端的master是一样的

    所以tom准备去像组长汇报，然后tom将自己的本地tom-login分支的代码提交到远端仓库的某一个分支上

    git push origin tom-login // tom-login分支如果没有的话会自动创建 其实， 专门接受tom提交的代码的分支也可以让组件提前建好

    此时“我”需要去review tom所写的代码，知道Tom的代码审核通过了

6. “我” 会在本地建立tom-login 并且将远端的tom-login代码拉下来，检查无误后，“我”会将本地的tom-login合并到本地的master,将合并后的完整的代码提交到远端的发布分支（master）


8. 招聘了Jack， Jack就会克隆代码, 然后进行和上面一样的操作了

9. 当“我”已经给远端发布分支（master）提交一次版本后，通知组员，Tom 和 Jack就得将远端已经最近的master同步到自己本地的master中，保证自己本地的master是最新的，然后再将自己本地的master合并到自己开发的本地子分支里继续开发...

    当出现冲突的时候，商量着解决。。。