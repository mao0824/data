# git的一些操作

# git基于master创建新分支

**1.切换到被copy的分支（master），并且从远端拉取最新版本**

#### $git checkout master

#### $git pull

#### 2.从当前分支拉copy开发分支

#### **$git checkout -b dev**

#### Switched to a new branch 'dev'

#### 3.把新建的分支push到远端

#### **$git push origin dev**

#### 4.关联

#### $git branch --set-upstream-to=origin/dev

#### 5.再次拉取验证

#### $git pull



删除分支： $ git branch -d mybranch
强制删除分支： $ git branch -D mybranch
列出所有分支： $ git branch

查看各个分支最后一次提交： $ git branch -v

查看哪些分支合并入当前分支： $ git branch –merged

查看哪些分支未合并入当前分支： $ git branch –no-merged

更新远程库到本地： $ git fetch origin
推送分支： $ git push origin mybranch
取远程分支合并到本地： $ git merge origin/mybranch
取远程分支并分化一个新分支： $ git checkout -b mybranch origin/mybranch