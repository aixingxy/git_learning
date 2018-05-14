# Git学习笔记

## 创建ssh key
``` bash
git config --global user.name "xxy"
git config --global user.email 775415344@qq.com

ssh-keygen -t rsa -C "775415344@qq.com"
# 三次回车

# 查看秘钥
cd ~/.ssh
vim id_rsa.pub
# 复制秘钥
# 打开Github网站进行登录
# 到个人设置页Personal Settings
# 找到SSH and GPG keys
# 选择新建SSH key：new ssh key
# 填写和粘贴公钥内ring（不含中文）

```

# 1. 创建git项目
## 克隆远程版本库
``` bash
git clone git@github.com:atommutou/gitlearning.git
```

## 创建本地版本库
## 添加远程库
``` bash
mkdir gitlearning
cd gitlearning
echo "# gitlearning" >> README.md
git init
git add README.md
git commit -m "first commit"
remote add origin git@github.com:atommutou/gitlearning.git
git remote add origin git@github.com:atommutou/gitlearning.git
git push -u origin master
# 第一次推送master分支，加上-u参数，Git不但会把本地的master分支内容推送的远程新的master，还把本地master分支和远程的master分支关联起来。
# 以后推送，使用下面的语句就可以了
git push origin master
```

# 2. 分支操作
## 创建分支
``` bash
git checkout -b dev # 创建分支并直接切换
# 相当于下面两个句子
git branch dev # 创建分支
git checkout dev # 切换分支
```

## 查看分支
``` bash
git branch # 当前分支钱前面会有一个*号
```

## 切换分支
``` bash
git checkout dev
```

## 合并分支
``` bash
git merge dev # 用于合并指定分支到当前分支
# 使用Fast-forward
```
## 查看分支合并情况
``` bash
git log --graph --pretty=oneline --abbrev-commit
```
##

## 删除分支
``` bash
git branch -d dev
```

# 3. 后悔操作
## 版本回退
在Git中，用HEAD表示当前版本，上一个版本就是HEAD^，上上个版本就是HEAD^^，上100个版本HEAD~100

``` bash
# 使用--hard强制恢复到某次提交，并且git log中也不会显示上一次的提交
git reset --hard HEAD^
```
如果希望再次回到最新提交，可以通过git reflog查看每一条记录，来寻找commit_id,再使用
``` bash
git reset --hard commit_id
```
## 丢弃工作区的修改
``` bash
git checkout -- xx.file
```

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令```git checkout -- file```。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令```git reset HEAD file```，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库
