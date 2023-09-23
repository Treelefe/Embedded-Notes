# 文件操作

## 一、git diff --查看文件差异

git diff 有一下使用场景：

- 查看工作区、暂存区、本地仓库之间的差异。
- 查看文件在两个特定版本之间的差异。
- 查看文件在两个分支之间的差异。

![![![Alt text](29a3533d1bfecf552c0b596100f5efb.jpg)](<git diff.jpg>)](<../Images/git diff.jpg>)
&emsp;

### 1. 查看工作区和暂存区的文件差异：

默认情况下，git diff 命令只会显示工作区和暂存区之间的差异。
![![Alt text](image-12.png)](../Images/image-12.png)

使用 git add 添加修改到暂存区，再次使用 git diff 命令，可以看到暂存区和本地仓库之间没有差异。
![![Alt text](image-11.png)](../Images/image-11.png)

&emsp;

### 2. 查看文件在工作区和版本库之间的差异：

```c
git diff HEAD

//HEAD表示当前版本，即最近一次提交的内容，也可以使用 commit id 代替 HEAD
```

![![Alt text](image-13.png)](../Images/image-13.png)

因为文件修改只是添加到了暂存区，并没有提交到本地仓库，所以差异还是存在的，如果使用 git commit 提交后，是什么情况：
![![Alt text](image-14.png)](../Images/image-14.png)
可见工作区和版本库之间的差异已经没有了。

&emsp;

### 3. 查看暂存区和版本库之间的差异：

```dotnetcli
git diff --cached
```

&emsp;

### 4. 查看两个特定版本之间的差异：

```c
git diff 版本号1 版本号2

//可以使用HEAD和HEAD~比较当前版本和上一个版本差异
git diff HEAD~ HEAD

//加上指定文件名查看特定文件差异：
git diff 版本号1 版本号2 文件名
```

> HEAD~1 表示当前版本和上一个版本,HEAD~2 表示上上个版本...

![![Alt text](image-15.png)](../Images/image-15.png)

### 5. 查看两个分支之间的差异：

```dotnetcli
git diff 分支名1 分支名2
```

&emsp;

# 删除文件

假如仓库中有一个文件 file1.txt，现在我想删除仓库中的这个文件

### 1. 从工作区删除：

```c
//1.删除工作区的file1.txt文件
rm file1.txt
//2.添加修改到暂存区
git add file1.txt
//3.提交到本地仓库
git commit -m "delete file1.txt"
```

可以看出，这样的方式去删除仓库中的一个文件还是比较麻烦的，因为需要先删除工作区，然后再添加到暂存区，最后提交到本地仓库。

### 2.使用 git rm 命令：

```c
git rm file1.txt
```

同时也会删除工作区中的 file1.txt 文件。
![![Alt text](image.png)](../Images/image16.png)
