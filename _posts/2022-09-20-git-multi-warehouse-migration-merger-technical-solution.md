## Git 多仓库合并

假设要合并ABC 不同的分支到新工程X，ABC 工程作为X工程的子目录

> 迁移A 工程的5.0分支到新工程X的A1目录
>
> 迁移B工程的dev分支 到新工程X的B1目录
>
> 迁移C 工程的6.0分支 到新工程X的C1目录

期望：合并后的目录结构

新工程X（master分支）

```markdown
.
├── A1 (原A工程的某分支)
├── B1 (原B工程的某分支)
├── C1 (原C工程的某分支)
.git
README.md
```

### 准备工程合并目录

新建合并用的目录 `combine`

```sh
mkdir combine && cd combine
```

切换到combine 目录，新建合并后的工程目录；假设新合并工程为 `argo`，仓库地址为http://git.analysys.cn/sloong/argo.git

```sh
mkdir argo && cd argo
git init
git remote add origin http://git.analysys.cn/sloong/argo.git
```

准备迁移后的分支

```sh
echo "" > _base.txt
git add .
git commit -m"准备基础分支"
git push origin master
```

git clone 需要合并的项目分支到本地

在combine空目录，将需要合并的工程(可指定分支）全部clone到本地（后续也可以继续加项目合并，后面的步骤一样）

git 操作指令： `git clone -b <分支名> <仓库地址>`

```sh
# 切换到combine 目录
cd ..
# 假设我这要合并这三个子工程。 
git clone http://git.analysys.cn/noah/streaming.git
git clone http://git.analysys.cn/noah/ark-canary-api.git
git clone http://git.analysys.cn/noah/ark-canary-web.git

# 如果只合并子项目的某个分支，那么可以指定下载分支，不需要clone 整个仓库，加上 -b <分支名> 参数即可
git clone -b 5.0 http://git.analysys.cn/noah/streaming.git
git clone -b dev-5.0 http://git.analysys.cn/noah/ark-canary-api.git
git clone -b dev http://git.analysys.cn/noah/ark-canary-web.git
```

这时完成 `git clone` 操作后目录下结构如下

```sh
# tree -L 2
.
├── argo
├── ark-canary-api
│   └── .git
│   └── 工程文件
├── ark-canary-web
│   └── .git
│   └── ......
└── streaming
    └── ......
```

### 调整待合并项目的目录结构

我们期望合并过去的项目都是在自己单独的目录下，如果直接合并，实际是工程目录下的文件/文件夹 平铺到合并后的，而且会存在冲突，所以希望合并过去的子项目，都位于合并工程的特定子目录中

假设我需要合并的主工程名称为argo，那么期望的目录结构如下：

```markdown
├── argo 								-- 合并后工程目录
│   ├── argo-streaming  -- streaming工程 并且重命名为argo-streaming 
│   │   ├── ...... 					-- streaming 工程文件
│   ├── argo-server     -- ark-canary-api工程 并且重命名为argo-server
│   ├── argo-web        -- ark-canary-web工程 并且重命名为argo-web
```

所以我们需要对每一个子工程其进行 `git mv` 操作，在 `git mv`同时可以对原工程进行子目录改名。

```sh
# 切换到子工程 ark-canary-api
cd ark-canary-api
# 新建 需要重命名的 目录，不需要重命名，就是保持和工程同名
mkdir argo-server
# 将子工程的所有文件和文件夹（包括隐藏文件，排除.git） git mv 到 刚创建的 argo-server
# 下面这条指令可以移走所有工程内容到新建的目录，如果是其他子工程，替换掉argo-server即可
ls -A | grep -wv '.git\|argo-server' | xargs -t -I '{}' git mv {} argo-server/{}

# ls -a 显示指定路径中的所有文件，包括隐藏文件
# grep -wv 1.要仅显示与搜索模式不匹配的行，请使用-v 2.-w 选项告诉grep 仅返回指定字符串是整个单词（由非单词字符括起来）的那些行。
# xargs 的一个选项 -I，使用 -I 指定一个替换字符串 {}，这个字符串在 xargs 扩展时会被替换掉，当 -I 与 xargs 结合使用，每一个参数命令都会被执行一次：
# git mv 命令用于移动或重命名一个文件、目录或软连接。
```

移动后的当前子工程的目录结构如下

```sh
ark-canary-api git:(dev-5.0) tree -L 2
.
└── argo-server
    ├── README.md
    ├── pom.xml
    ├── ark-controller
    ......
```

对改动进行 `git commit`操作，只需要操控本地仓库，所以不要`push origin`

```sh
git add .
git commit -m"一级目录结构改造，移动到argo-server"
```

**分别对其他两个子工程进行上述操作**

### 给 主工程 添加远程仓库

实际是给argo工程，添加 clone下来本地的三个子工程的仓库地址

涉及的git操作指令为： `git remote add <shortname> <子项目本地仓库地址>`

```sh
# cd argo  --argo目录

# remote add 本地仓库地址，并分别起的远程仓库别名
git remote add r_server ../ark-canary-api
# 其他两个也都添加上
git remote add r_web ../ark-canary-web
git remote add r_streaming ../streaming

# 检查结果如下：
argo git:(master) git remote -v
origin	http://git.analysys.cn/sloong/argo.git (fetch)
origin	http://git.analysys.cn/sloong/argo.git (push)
r_server	../ark-canary-api (fetch)
r_server	../ark-canary-api (push)
r_web	../ark-canary-web (fetch)
r_web	../ark-canary-web (push)
r_streaming	../installer (fetch)
r_streaming	../installer (push)
```

### 添加子工程的辅助分支(要合并子工程的分支)

先`fetch all`

```sh
# cd argo --argo目录

# fetch 下所有子工程仓库
git fetch --all
```

再`checkout -b`创建子项目对应的辅助分支

涉及的git 操作指令：`git checkout -b <分支名> <仓库地址/别名>`

```sh
git checkout -b b_server r_server/dev-5.0
git checkout -b b_web r_web/dev
git checkout -b b_streaming r_streaming/5.0
```

### 切换回主分支，合并辅助分支

将辅助分支合并到主工程的分支上，因为各个子项目都有单独的子目录，所以不会出现冲突。

涉及的git 操作指令：`git merge -b <分支名> --allow-unrelated-histories`。因为原本几个仓库不相关，所以合并时，需要加上 `--allow-unrelated-histories`

```sh
# cd argo --还是argo目录
git checkout master

# b_streaming 分支名 是上一步创建的辅助分支
git merge b_streaming --allow-unrelated-histories
git merge b_web --allow-unrelated-histories
git merge b_server --allow-unrelated-histories
```

### 合并收尾，清除临时分支和remote 仓库

```sh
# 删除3个远程仓库链接
git remote remove r_streaming
git remote remove r_web
git remote remove r_server
# 最后再删除3个辅助分支
git branch -D b_streaming
git branch -D b_web
git branch -D b_server
```

最后`push origin` ，完成多项目的迁移合并

git push origin master

![git_multi_project_combine_01](https://chuilee-1257187978.cos.ap-nanjing.myqcloud.com/blog/git_multi_project_combine_01.png)

本质来看就两个步骤：

1. 将各个子项目git仓库作为远程库拉取到合并库中的一个辅助分支
2. 再将此辅助分支合并入当前分支

## 迁移合并多项目的多分支

领导觉得太简单，又提要求说：

>  “不仅要将多个项目仓库合并到一个工程仓库，保留commits 记录同时，还要将不同项目的不同分支迁移合并到新工程对应版本分支”
>
> 恕我直言，可以实现！

上面所有的步骤最终将多个项目的某个分支合并到了新工程的master分支，但如果又要迁移合并多项目，还要迁移这些项目的几个分支合并到新的指定分支上，所以迁移的工作量成倍增长：重复上述7步骤 * n个项目* m个分支

需求简化：假设要同时

- 迁移ABC 工程的4.6分支到新工程的4.6分支
- 迁移ABC 工程的5.0 到新工程的5.0分支
- 迁移ABC 工程的6.0 到新工程的master分支

期望：合并后的目录结构

新工程X（4.6分支）
.

```markdown
├── A1 (原A工程的4.6.0分支)
├── B1 (原B工程的4.6.1分支)
├── C1 (原C工程的4.6.0分支)
.git
README.md
```

新工程X（5.0分支）

```markdown
.
├── A1 (原A工程的5.0分支)
├── B1 (原B工程的5.2分支)
├── C1 (原C工程的5.4分支)
.git
README.md
```

新工程X（master分支） 同理省略……

### 多项目多分支迁移合并到指定分支方案

**关键点：一定在一开始的时候要基于空白分支先把需要的版本分支建出来**，然后对应不同的分支 重复做前面的`1.5`、`1.6`两个步骤。

因为在最初的步骤涉及到关键修改，所以**一定要是一个新仓库、从头开始**。

合并迁移的步骤大致上面一致，只是部分步骤需要做一些改动，所以就不开篇重写，就将各个步骤的改动写上来：

**改动1：** 上述1.1.3 步骤基于master分支，多建几个要迁移的分支：

```sh
# argo 目录
# 如果要迁移多分支，那么基于上面的master 空白工程
git checkout -b 4.6
git checkout -b 5.0
git checkout -b 6.0
```

**改动2:** 不算改动，说明下：在步骤1.2 中，**不clone -b 下载指定分支了，直接clone 整个仓库操作**

```sh
cd combine

# clone 子项目整个仓库到本地，因为如果要合并3个分支，如果指定分支，那么要做3遍
git clone http://git.analysys.cn/noah/presto-udf.git
```

**改动3：**步骤1.5，因为需要用到子工程多个分支，那么`git checkout -b <分支别名>`分支时，分支别名带上版本名，一次把要用的分支全部建出来

```sh
git checkout -b b_udf_4.6 r_udf/4.6
git checkout -b b_udf_5.0 r_udf/5.0
git checkout -b b_web_4.6 r_web/4.6
......
```

其他子项目或者更多分支， 类似重复。

删除辅助分支 也需要一个个删除

**改动4：**步骤1.6，merge子项目分支 时，merge 指定的版本分支别名。比如合并后版本分支为5.0，那么前面的merge 也是对应的5.0 的分支别名

迁移合并不同分支，先切换到对应的分支上，例如迁移5.0 分支，那么先切到5.0 空分支，然后执行后续操作。

```sh
# 做4.6 分支合并时，先要切换主工程分支到4.6，再做git merge
git checkout 4.6
git merge b_udf_4.6 --allow-unrelated-histories
# 做5.0 分支合并时，先要切换主工程分支到6.0，再做git merge
git checkout 5.0
git merge b_udf_5.0 --allow-unrelated-histories
......
```

其他子项目或者更多分支， 类似重复

### 效果展示

![git_multi_project_combine_02](https://chuilee-1257187978.cos.ap-nanjing.myqcloud.com/blog/git_multi_project_combine_02.png)

![git_multi_project_combine_04](https://chuilee-1257187978.cos.ap-nanjing.myqcloud.com/blog/git_multi_project_combine_04.png)

本质上还是同样的思路，只是在最初的时候建立相关的空白分支，clone 下整个仓库，在建辅助分支时，一次性将要用的子项目分支都建成辅助分支，最后 将子项目的辅助分支 merge 到主工程对应的版本分支上

## 转载

**https://blog.95id.com/git-multi-repo-combine.html**