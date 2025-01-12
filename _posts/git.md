+++
title = 'Learning git'
date = 2024-03-19T16:22:49+08:00
draft = false
+++

#### Useful commands

View all files  that have been modified but not yet added to the stagging area

```git
git status
git status --procelain # only shows the files which have been modified
```

View the contents that could be committed

```git
# check the difference between the staging area and the last commit
git diff --cached
# Show the difference between currently modified content in the working directory and the staging area
git diff
# Show the difference between working directory and stagging are of a specified file
git diff path/to/file
```

The above command will show the differences between the changes that have been staged and the last commit. We can also use `git status` command to check the files in the stagging area. It will list all the files staged and files haven't been staged.

```git
git status
```

Show commit history

```git
git log --oneline
```

##### .gitignore

在Git中，`.gitignore`文件中的更改会立即生效，但这种生效是指对未跟踪（untracked）文件的处理。该文件指定了Git应该忽略的文件或目录模式。一旦你编辑并保存了`.gitignore`文件，Git将使用新的忽略规则来确定哪些文件应该被忽略。

但是，对于已经被跟踪（tracked）的文件（即已经被Git纳入版本控制的文件），更改`.gitignore`是不会自动生效的。如果你的`.gitignore`文件中新增了规则来忽略已跟踪的文件，你需要手动从跟踪状态中移除（untrack）这些文件，使更改生效。这可以通过`git rm --cached`命令来完成，这样文件会保留在磁盘中，但不会再被Git跟踪。

例如，假设你已经将`log.txt`文件提交到了Git仓库中，之后你决定应该忽略它，你在`.gitignore`中添加了`log.txt`规则，为了让这个更改生效，你需要执行：

sh

```git
git rm --cached log.txt
git commit -m "Stop tracking log.txt"
```

之后，`log.txt`文件将不会再被Git跟踪，`.gitignore`中的规则将适用于它。新的忽略规则也会从这一刻开始适用于你的工作目录中的未跟踪文件。

要查看所有已跟踪的文件，你可以使用Git的`ls-files`命令。打开命令行或者终端，切换到你的Git仓库目录下，然后执行下面的命令：

sh

```git
git ls-files
```

这个命令会列出所有当前被Git跟踪的文件。这些文件是在你的工作目录中，且之前已经被添加到Git索引（通过`git add`命令）的文件。

如果你想要查看包括在子模块中的已跟踪文件，可以使用`--recurse-submodules`选项：

sh

```git
git ls-files --recurse-submodules
```

这些命令会列出你的Git仓库中所有已跟踪的文件，帮助你了解哪些文件是Git正管理的。

要忽略一个已经被跟踪的文件夹及其目录下的所有内容，如`bin/`目录，你需要执行两个步骤：

1. 首先，在你的`.gitignore`文件中添加规则来指定忽略该文件夹。打开`.gitignore`文件并添加以下内容：

plaintext

```git
   bin/
```

这条规则告诉Git忽略`bin`目录及其所有内容。如果`.gitignore`文件不存在，你需要创建它。

2. 然后，你需要从Git跟踪中移除（但不删除文件）这个文件夹。在命令行或终端中，使用以下命令：

sh

```git
   git rm -r --cached bin/
   git commit -m "Untrack bin/ directory"
```

`git rm -r --cached`命令会递归地从Git索引中移除`bin/`目录和其下的所有文件，但实际文件会保留在你的工作目录中。`--cached`选项确保只是从跟踪状态中移除，而不会删除物理文件。

然后，使用`git commit`来提交这个变更。这将更新你的仓库，以反映这个文件夹不再被跟踪。

之后，`bin/`目录及其内容就不会被Git跟踪了，并且任何对`.gitignore`的更新将立即应用于未跟踪的文件和目录。
