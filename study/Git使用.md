# Git使用

## 一、简介

![git](http://datadoc.kingdee.com/Public/Uploads/2018-08-16/5b7575395d8cb.png)

**1. Head**
这是当前分支版本顶端的别名，也就是在当前分支你最近的一个提交。

**2. Index**
Index 也被称为 `staging area`，是指一整套即将被下一个提交的文件集合。他也是将成为 HEAD 的父亲的那个 commit。

**3. Workspace(Working Copy)**
Workspace 代表你正在工作的那个文件集。

**4. Flow**
当第一次 checkout 一个分支，Head 就指向当前分支的最近一个 commit。在 Head 中的文件集和在 Index 中的文件集是相同的，在 working copy 的文件集和 HEAD, Index 中的文件集是完全相同的。所有三者(`Head`, `Index(staging)`, `working copy`)都是相同的状态，git 很 happy。

当对一个文件执行一次修改，它 working copy 不再和 Index, Head 相同，git 会标记这个文件是修改过的。
当执行一个 `git add .`，它就 `stages the file in the index`，现在的 working copy 和 Index 区是相同的，但是他们和 Head 区是不同的。

当执行一个 git commit，git 就创建一个新的 commit，随后 Head 就指向这个新的 commit，而 Index, working copy 的状态和 Head 就又完全匹配相同了。

## 二、使用

### 1, 获取仓库

#### 1.1 本地创建仓库

**step1：初始化**

```git
git init E:\workspace\demo
```

**step2：和远程仓库关联**

```git
git remote add origin https://github.com/binjf/study.git # 添加远程链接指向，可多个
git remote set-url https://github.com/binjf/study.git # 设置新的远程链接指向
```

**step3：推送到远程仓库**

```git
git push -u origin master # 第一次推送时需要加参数“-u”
```

#### 1.2 clone 远程仓库

```
git clone https://github.com/binjf/study.git
```

### 2, 提交代码

**step1：把代码添加到 Index** 

```
git add xxx xxx xxx
```

**step2：提交到本地库**

```
git commit -m '新增xxx'
```

### 3, 拉取代码

#### 3.1 获取远程仓库的分支代码到本地仓库

```
git fetch # 拉取远程所有分支到本地仓库
git fetch origin master # 拉取远程master分支到本地仓库
git fetch —all —-
git reset —hard origin/test
```

#### 3.2 当前分支 rebase 当前仓库分支

```
git rebase # rebase当前同名分支
git rebase master # rebase master分支
```

### 4, 推送代码

```
git push # 把当前分支推送到远程仓库的同名分支
git push origin develop:develop # 把当前分支推送到远程仓库的同名分支
git push origin develop:master # 把当前分支推送到远程仓库的master分支
```

### 5, 解决冲突

**step1：解决冲突**

**step2：标记冲突已解决 `git add xxx xxx`**

**step3：继续 rebase、merge**

### 6, 分支操作

#### 6.1 创建分支

基于 master 分支创建 develop 分支，并切换到 develop 分支，没有 master 参数表示基于当前分支创建develop。

```
git checkout -b develop master
```

基于 master 分支创建 develop 分支，没有 master 参数表示基于当前分支创建 develop

```
git branch -t develop master
```

#### 6.2 切换分支

```
git checkout master
```

注：切换时，需要把 work、index 空间有变动则会出现在切换的分支中

#### 6.3 暂存分支

当分支存在编辑时，需要切换分支或拉新代码覆盖当前编辑的代码，需要把当前分支暂存到本地仓库。需要继续编辑时，在切换到原来分支，并弹出之前的暂存。

暂存当前的编辑：`git stash`
注：当前版本需要编辑加入到 index 中才能暂存，也就是需要先执行 `git add .`

弹出之前的编辑：`git stash pop`

#### 6.4 合并分支

`git merge master develop`  表示把 develop 的变动合并到 master 分支，没有 develop 参数表示 rebase 当前分支。

如果出现冲突，处理方式：

- 编辑文件解决冲突，用 `git add xxx.txt` 标记已解决冲突，再执行 `git merge —continue`
- 执行  `git merge —abort` 放弃 merge
- 当 merge 有冲突且解决后推送代码时需要用参数 `-f` 

#### 6.5 rebase 分支

`git rebase master develop` 表示把 develop 的变动合并到 master 分支，没有 develop 参数表示 rebase 当前分支。

#### 6.6 删除分支

删除本地分支：`git branch -D develop`

删除远程分支：`git push origin :develop`

#### 6.7 回滚分支

```
git log # 查看日志
git reset —soft $commitID # 回滚到指定 commit，之前的都将被删除
git push 或 git push -f # 当确定回滚是正确的，推送被拒绝后，可强制提交
```

### 7, 打标签

```
git tag 列出所有tag
git tag [tag name] 打轻量标签
git tag -a [tag name] -m [message] 附注标签 #案例 :git tag -a v1.0 -m ‘v1.0 release’
git tag -a [tag name] [version] 后期打标签
git tag -d [tag] 删除本地tag #案例 ： git tag -d v1.0
git push origin —delete tag <tagname> 删除远程tag
git show [tag] 查看tag信息
git push [remote] [tag] 提交指定tag #案例 git push origin v1.0
git push [remote] —tags 提交所有tag
```

### 8, 命令对比

#### 8.1 rebase 和 merger

1，rebase 改变分支基点（某个 commit），计算合并代码放入当前分支（可能有冲突），分支线路为一条直线。

2，merger 是两个分支代码合并后产生一个新commit（可能有冲突），分支线条会相交。

#### 8.2 reset 和 revert

1，reset 取消之前 commit，HEAD 指针往后退。

`git reset —mixed $commitID` 保留 workspace，回滚 index 到 workspace，抛弃 HEAD 到指定的commitID，HEAD 重新指向特定 commitID。

`git reset —soft $commitID`  保留 workspace、index 里面东西，抛弃 HEAD 到指定的 commitID，HEAD 重新指向特定 commitID。
`git reset —hard $commitID`  抛弃 workspace、index，HEAD 到指定的 commitID，HEAD 重新指向特定commitID。

2，revert 用新的 commit 覆盖之前之前 commit，HEAD 指针往前进。

#### 8.3 cherry-pick 和 cherry

cherry-pick 是把某个 commit 应用到当前分支：`git cherry-pick d835ab3`

cherry 是计算当前分支跟指定分支对比，有哪些不同的 commit：`git cherry develop master`

### 9, 常用命令

```
git clone https://github.com/binjf/study.git
git fetch; git rebase # 拉取远程分支到本地仓库，并把本地仓库分支代码 rebase 到当前工作代码
git status # 查看git 本地仓库、index、work 的状态
git push # 推送当前分支代码到远程仓库同名分支上
git push -f # 强制推送当前分支代码到远程仓库同名分支上（当merge、rebase有冲突且解决后推送代码时需要用参数“-f”）
```

```
git stash
git stash list
git stash apply
git stash pop

git pull --rebase

gitk
gitk --all
```



### 10, 合并某个分支某次提交

```
 例如要将 A 分支的一个 commit 合并到 B 分支：
 首先切换到 A 分支
 git checkout A
 git log
 找出要合并的 commit ID:
 例如 ceccba9057689af95b63fc6676d6728cdcdac66d
 然后切换到 B 分支上
 git checkout B
 git cherry-pick  ceccba9057689af95b63fc6676d6728cdcdac66d
 然后就将A分支的某个 commit 合并到了 B 分支了
```

