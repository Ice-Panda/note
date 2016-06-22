# git笔记
修改提交说明
#### git commit --amend 可以直接修改上一次commit的消息
#### 提交数据中不小心提交了不该提交的文件
git rm --cached filename  
git commit --amend
#### 差异比较
git diff --word-diff 可以逐子比较
git add 之后的文件需要通过 git diff --cached 才能看到差异
#### 进度保存
git stash 可以保存和回复工作进度  
在切换工作分支之前执行git stash可以保存工作进度
git stash  
git checkout new_branch  
切回来时用git stash pop
git chekcout old_branch
git stash pop