```bash
#提交文件
git commit -m "第一次更新"
#跳过使用暂存区域:git add [fileName]
git commit -a -m "第二次提交"

#撤销操作
#提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了

$ git commit -m 'initial commit'
$ git add forgotten_file
$ git commit --amend

#取消暂存文件
$ git reset HEAD CONTRIBUTING.md
#撤销对文件的修改
$ git checkout -- CONTRIBUTING.md

#撤销commit
git reset --soft HEAD~1
#参数：--soft：不删除工作空间改动代码，撤销commit，不撤销git add . 。--mixed：不删除工作空间代码，撤销commit，并且撤销git add .操作。--hard 删除工作空间代码，恢复到上一次的commit状态。

#查看分支
git branch [参数]
#空参：本地分支
#-a:所有分支
#-r:远端分支

#本地分支关联远端分支：更改关联后可以直接pull和push不需要在后面跟远端分支名
git branch --set-upstream-to=origin/dev master

#push到远端指定仓库
git push origin HEAD:feature_panyexiong
```







