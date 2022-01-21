# Git入门与实践

## Git 基础

### 基础操作

- git config  --global user.name 你的目标用户名 git config  --global user.email 你的目标邮箱名 添添加global为全局
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

&ensp;&ensp;&ensp;&ensp;- git commit 直接执行 在 vim 中编辑

- git commit am ”注释“ 添加并暂存

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

&ensp;&ensp;&ensp;&ensp;- git diff 查看工作树和暂存区的区别

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

### git commit 规范

commit message格式

```
<type>(<scope>): <subject>
```

type(必须)

用于说明git commit的类别，只允许使用下面的标识。

feat：新功能（feature）。

fix/to：修复bug，可以是QA发现的BUG，也可以是研发自己发现的BUG。

fix：产生diff并自动修复此问题。适合于一次提交直接修复问题
to：只产生diff不自动修复此问题。适合于多次提交。最终修复问题提交时使用fix
docs：文档（documentation）。

style：格式（不影响代码运行的变动）。

refactor：重构（即不是新增功能，也不是修改bug的代码变动）。

perf：优化相关，比如提升性能、体验。

test：增加测试。

chore：构建过程或辅助工具的变动。

revert：回滚到上一个版本。

merge：代码合并。

sync：同步主线或分支的Bug。

**scope(可选)**

scope用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同。

例如在Angular，可以是location，browser，compile，compile，rootScope， ngHref，ngClick，ngView等。如果你的修改影响了不止一个scope，你可以使用*代替。

**subject(必须)**

subject是commit目的的简短描述，不超过50个字符。

建议使用中文（感觉中国人用中文描述问题能更清楚一些）。

结尾不加句号或其他标点符号。

根据以上规范git commit message将是如下的格式：

```
fix(DAO):用户查询缺少username属性 
feat(Controller):用户查询接口开发
```