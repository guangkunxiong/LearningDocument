# GitHub入门与实践

## Git基础

### 基础操作

- git init 初始化仓库

- git status 查看当前仓储的状态

```纯文本
On branch master
Your branch is up to date with 'origin/master'.  --当前所在的分支

Changes not staged for commit: --未提交的更改
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   "other/ideavim\345\255\246\344\271\240.md
```


- git add 向暂存区添加文件

- git commit -保存仓库的历史记录

&ensp;&ensp;&ensp;&ensp;- git commit -m"注释"

&ensp;&ensp;&ensp;&ensp;- git commit 直接执行  在vim中编辑

​		- git commit am ”注释“ 添加并暂存

```纯文本
Please enter the commit message for your changes. Lines starting --请输入更改的提交消息。
# with '#' will be ignored, and an empty message aborts the commit.
#
# On branch master
# Your branch is up to date with 'origin/master'.
#
# Changes to be committed:
#       modified:   "other/ideavim\345\255\246\344\271\240.md
```


- git log 查看提交日志

&ensp;&ensp;&ensp;&ensp;- git log —pretty=short 只显示提交的第一行

&ensp;&ensp;&ensp;&ensp;- git log 文件/目录 只显示提交的第一行

&ensp;&ensp;&ensp;&ensp;- git log -p 显示文件的改动 同样可以添加指定的文件和文件夹

- git diff 查看更改前后的区别

&ensp;&ensp;&ensp;&ensp;- git diff查看工作树和暂存区的区别

```纯文本
diff --git "a/other/ideavim\345\255\246\344\271\240.md" "b/other/ideavim\345\255\246\344\271\240.md"
index b682540..238473d 100644
--- "a/other/ideavim\345\255\246\344\271\240.md"  --添加的内容
+++ "b/other/ideavim\345\255\246\344\271\240.md"  --删除的内容
@@ -82,6 +82,28 @@
 `]/`  跳转到当前注释块结束处；
 `%`  不仅能移动到匹配的(),{}或[]上，而且能在#if，#else， #endif之间跳跃。

+
+
+> 说明：`<C-x>` 代表组合键 `Ctrl` + `x`
+
+- `<C-]>` 跳转到当前标识符的定义位置 （相当于在当前光标位置的单词上按住ctrl用鼠标点击）
```

### 分支操作

- git branch 显示分支一览

- git checkout -b 创建切换分支

  ```
  git checkout -b dev
  Switched to a new branch 'dev'
  等效于
  git branch dev
  git checkout dev
  ```

- git checkout - 切换到上一个分支

- git merge 合并分支

  ```
  git checkout master
  git merge --no-ff dev 合并分支
  ```

- git log --graph 已图形的方式查看分支

### 更改提交的操作

- git reset 回溯历史版本
- git commit --amend 修改提交信息
- git rebase -i 压缩历史
- git rebase 更改历史

推送到远程仓库

- git remote add 添加远程仓库
- git push 推送到远程仓库
- git clone 获取远程仓库
- git pull 获取最新的远程仓库分支

