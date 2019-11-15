<font size=6>**Git 学习笔记**</font>

子笔记只是一个简单的个人学习笔记，详细资料可参考廖雪峰的 Git 博客十分详细，[廖雪峰 Git 教程](https://www.liaoxuefeng.com/wiki/896043488029600/896067074338496)
---

## 安装 Git

参照网上安装法法，资料一大堆。

## 创建版本库

创建一个版本库非常简单，首先，选择一个合适的地方，创建一个空目录，通过 **git init** 命令把这个目录变成 Git 可以管理的仓库：

	$ git init 


## 查看工作区和版本库里最新版本的区别

	$ git diff HEAD -- fileName

---

	$ git diff HEAD -- readme.txt 
	diff --git a/readme.txt b/readme.txt
	index 76d770f..a9c5755 100644
	--- a/readme.txt
	+++ b/readme.txt
	@@ -1,4 +1,4 @@
	 Git is a distributed version control system.
	 Git is free software distributed under the GPL.
	 Git has a mutable index called stage.
	-Git tracks changes.
	+Git tracks changes of files.

## 撤销修改

git checkout -- fileName 可以丢弃工作区的修改：

	$ git checkout -- readme.txt

命令 git checkout -- readme.txt 意思就是，把 readme.txt 文件在工作区的修改全部撤销，这里有两种情况：

- 一种是 readme.txt 自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

- 一种是 readme.txt 已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次git commit 或 git add 时的状态。


**git checkout -- file命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到git checkout命令。**


## 删除文件

在Git中，删除也是一个修改操作，一般情况下，你通常直接在文件管理器中把没用的文件删了，或者用rm命令删了：

	$ rm test.txt

这个时候，Git知道你删除了文件，因此，工作区和版本库就不一致了，git status命令会立刻告诉你哪些文件被删除了。

现在你有两个选择，一是确实要从版本库中删除该文件，那就用命令git rm删掉，并且git commit

	$ git rm test.txt
	rm 'test.txt'
	
	$ git commit -m "remove test.txt"
	[master d46f35e] remove test.txt
	 1 file changed, 1 deletion(-)
	 delete mode 100644 test.txt

现在，文件就从版本库中被删除了。


另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：

	$ git checkout -- test.txt

git checkout其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

 **注意：从来没有被添加到版本库就被删除的文件，是无法恢复的！**






