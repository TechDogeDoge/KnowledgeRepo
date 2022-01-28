git add .  将修改添加到暂存区

git chekout . 将未添加到暂存区的修改全部删除回退

git commit -m 进行一次提交

git commit --amend 进行一次附加提交，不会产生额外的节点

git branch 查看所有本地分支

git checkout branch_name 切换到指定分支

git merge branch_b 将branch_b合入到当前分支，如下图

![image-20211109235409265](C:\Users\jiaoy\AppData\Roaming\Typora\typora-user-images\image-20211109235409265.png)



git push origin branch_name 将本地分支推送到指定远端分支

git pull origin branch_name = git fetch + git merge 将指定远端分支合入到本地分支，版本数不是线性

git pull origin master --rebase = git fetch + git rebase 将指定远端分支rebase到本地分支，版本树为线性

git rebase main

![image-20211110002926045](C:\Users\jiaoy\AppData\Roaming\Typora\typora-user-images\image-20211110002926045.png)

git checkout commit_id 将HEAD指向指定commit节点，一般默认HEAD指向当前分支，当前分支指向最新commit节点

git checkout HEAD^ 将HEAD指向当前commit节点的前一个节点

git checkout HEAD~n 将HEAD指向当前commit节点的前 $n$ 个节点

git branch -f main HEAD~n 强制将main分支指向当前commit节点的前 $n$ 个节点

![image-20211110003532714](C:\Users\jiaoy\AppData\Roaming\Typora\typora-user-images\image-20211110003532714.png)

git reset commit_id 将分支退回到指定commit节点

![image-20211110215452820](C:\Users\jiaoy\AppData\Roaming\Typora\typora-user-images\image-20211110215452820.png)

git revert commit_id 通过建立一个新的commit节点的方式来将分支退回到指定commit节点

![image-20211110215741868](C:\Users\jiaoy\AppData\Roaming\Typora\typora-user-images\image-20211110215741868.png)

git cherry-pick commit_id1 commit_id2 commit_id3

