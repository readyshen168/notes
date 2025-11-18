# gitLab git 的实验

## 分支定理

1. 分支指向某个提交 commit, Git 必须知道它的所有父提交 [参考](https://yuanbao.tencent.com/chat/naQivTmsDa/6f7e44d7-8243-49bb-a0cf-c3a419cf46d9)
   这就意味着：

   1. 从不同分支合并的提交， 即便只推送了某个分支到远程仓库，该分支所指向的提交的所有父提交都会被推送到远程仓库；

2. rebase 的时候，是可以舍弃掉当前 commit 的

   1. 解决了冲突后，rebase 过来的分支完全可以覆盖当前 commit

3. 分支跟踪：

   ```bash
      # 查看本地分支
      git branch

      # 查看所有远程(包括远程)
      git branch -a

      # 查看远程分支
      git branch -r

      # 查看所有分支的跟踪关系
      git branch -vv

      # 设置上游分支（精细到远程分支的完整方法）
      git branch --set-upstream-to=github/branch1 branch1

      # git push -u origin branch1 自动创建branch1的上游分支origin/branch1

   ```

   1. 一次性推送所有本地分支到远程（无论是否有上游跟踪分支）： `git push --all github`
   2. 单分支推送： `git checkout branch1; git push github branch1`
   3. 不切换分支，直接推送指定分支： `git push github branch1:branch1`
   4. 设定上游跟踪分支：`git push --set-upstream github branch1`
   5. 在其他主机上随时可以 fetch 到新支的信息，获取所有远程分支信息： `git fetch github`
   6. 在其他主机上切换到特定分支： `git checkout -b branch1 github/branch1`
      简化写法： `git checkout branch1` git 会自动把 github/branch1 设置为 branch1 的上游跟踪分支

## git config

- 配置文件位置： ~/.gitconfig
- 查看所有配置项： git config --global --list

ubuntu 上 git 网络连接问题：GnuTLS recv error (-110): The TLS connection was non-properly terminated

- git config --global http.sslBackend "gnutls"
- 撤销上面的配置： git config --global --unset http.sslBackend
- git ls-remote <URL 地址> 验证

## git rm

oh-my-zsh 简写：grm

.gitignore 添加 notes/，但不起效，证明 Git 已经跟踪过 notes,需要从 Git 中删除 notes:

```bash
 # 从Git索引中删除notes目录(保留本地文件)
git rm -r --cached notes # 简写： grmc -r notes
 # 提交删除操作
git commit -m "停止跟踪notes目录"

 # 现在.gitignore会生效
```

## 远程仓库相关操作

查看当前的远程仓库信息: `git remote-v`

- 改名:`git remote rename origin github`
- 修改 URL: `git remote set-url <name> <newurl>`
- 移除远程仓库: `git remote rm <name>`
- 从 https 变为 ssh 连接:`git remote set-url <name> git@github.com:用户名/仓库名.git`
  [关于设置 ssh 连接 github](https://yuanbao.tencent.com/chat/naQivTmsDa/23da6bdd-6505-4792-8768-e6fe45af359c)

## git checkout commitA 分离 HEAD 状态

## git cherry-pick commitX

重要的是 commit, 也就是某个提交的 SHA1 快照，只要有这个快照，就可以把这个提交合并到所在的分支

### git checkout commitA & git cherry-pick commitX 的综合实验

文件 test 提交 4 个版本 ABCD, 并推送到 github, 测试：

1. git cherry-pick commitA, 推送到远程

   ```bash

   ```

2. git checkout commitA, 做出更改，提交 commitA', 然后 git cherry-pick commitA'， 推送到远程

   ```bash

   ```

使用 git reflog 查看 commit 历史以及 SHA1 值

## git rebase

1. 分支变基合并：demo1 分支有 3 个 commit, commitA、B、C，demo2 分支有 2 个 commit, commita、 b, 此时在 demo2 上 git rebase demo1, demo1 的哪个 commit 会变基？

   [实验分支](../gitLab/test?branch=git#rebase#demo1)
   [实验分支](../gitLab/test?branch=git#rebase#demo2)

2. xxx

## git reset --hard

## 本地、远程删除分支

准备工作：

- 确保没有位于 demo 分支上：
  git checkout main
  或： git switch main

- 确认本地和远程都存在 demo 分支：
  git branch -a

1. 删除已合并的本地分支
   git branch -d demo

2. 强制删除未合并的本地分支
   git branch -D demo

3. 从远程恢复分支，重新拉取
   git fetch origin
   git checkout -b demo origin/demo

4. 远程删除分支
   git push origin -d demo
   OR: git push origin --delete demo

## 嵌套 Git 仓库的管理(gitlink)

[参考元宝](https://yuanbao.tencent.com/chat/naQivTmsDa/2b20a193-7010-4014-a454-4a39105527f1)

```bash
目录结构：
   Learn/
      .git/
      - baby-git/
         .git/
      - gitlearn/
         .git/
      - notes/
         .git/
```

1. 原始管理法：

   - 把所有子目录分别推送到 github 远程仓库
   - 所有在 Learn 仓库中侦测到的子目录更改，都要先确认子目录本身的 git 仓库是否已经更新
   - 在其他机器上通过 git clone --recursive 来完整恢复环境

2. subtree 管理法： 先不要使用`--squash`

   - 清理子目录的本地 Git 跟踪

   ```bash
   git rm --cached gitLab -r
   git rm --cached gitLearn -r
   git rm --cached notes -r
   ```

   - 提交本次更改
     git commit -m "chore: 移除子目录的本地跟踪，为转换为 subtree 做准备"

   - 将子目录添加为 Subtree

   ```bash
     # 为每个子仓库添加一个远程地址别名
   git remote add gitLab-origin <gitLab仓库的远程URL>
   git remote add gitLearn-origin <gitLearn仓库的远程URL>
   git remote add notes-origin <notes仓库的远程URL>

     # 使用 git subtree add命令将它们添加进来

       # 添加 gitLab 为 subtree
   git subtree add --prefix=gitLab gitLab-origin main --squash
       # 添加 gitLearn 为 subtree
   git subtree add --prefix=gitLearn gitLearn-origin main --squash
       # 添加 notes 为 subtree
   git subtree add --prefix=notes notes-origin main --squash

   ```

   --prefix=<目录>：指定子仓库在主仓库中的路径，这里就是目录名本身
   main：指定要拉取的子仓库的分支名，请根据你的实际情况替换（可能是 master 或 main）。
   执行成功后，子仓库的代码就被合并到主仓库的指定目录下了，并且主仓库会生成一次新的提交

   - 推送到主仓库远程

   `git push origin main`

   - 拉取子仓库的更新（同步最新代码）

   `git subtree pull --prefix=gitLab gitLab-origin main --squash`

   1. 从 gitLab-origin 远程仓库的 main 分支拉取最新更改。
   2. 尝试将其与主仓库的 gitLab 目录合并。
   3. 如果产生代码冲突，你需要像解决普通合并冲突一样手动解决，然后提交
   4. 使用 --squash 会将子仓库的多笔提交合并为一笔，记录到主仓库
      对 gitLearn 和 notes 目录执行相同的操作。

3. 在 Learn 仓库通过两个分支来尝试以上两种管理模式

   | 操作场景         | 手动管理分支 (main)                     | Subtree 管理分支 (subtree)                                   |
   | :--------------- | :-------------------------------------- | :----------------------------------------------------------- |
   | 添加子项目 ​     | 手动克隆，保持各自独立的.git 目录       | 使用 git subtree add 命令，将子项目代码和历史合并到主仓库    |
   | 克隆项目 ​       | 需递归克隆 (git clone --recursive)      | 普通克隆 (git clone) 即可                                    |
   | 提交子项目修改 ​ | 进入子项目目录，像普通 Git 仓库一样操作 | 可直接在主项目提交，或使用 git subtree push 推回子项目远程库 |
   | 更新子项目 ​     | 进入子项目目录，执行 git pull           | 使用 git subtree pull 命令                                   |
   | 核心差异 ​       | 显式分离：子项目是完全独立的仓库        | 集成透明：子项目代码成为主项目的一部分，对协作者不可见       |

4. 在 ubuntu 云服务上克隆 Learn 仓库

   | 克隆场景             | 克隆命令示例                                   | 本地获得的分支            | 说明                                                                 |
   | :------------------- | :--------------------------------------------- | :------------------------ | :------------------------------------------------------------------- |
   | 默认克隆 ​           | git clone <仓库 URL>                           | 仅远程默认分支（如 main） | 这是最常见的方式，速度最快，但只能看到一个分支                       |
   | 克隆特定分支 ​       | git clone -b tree <仓库 URL>                   | 仅指定的 tree 分支 ​      | 只获取单个分支的内容，其他分支不可见                                 |
   | 克隆单一分支（推荐） | ​ git clone -b tree --single-branch <仓库 URL> | 仅指定的 tree 分支 ​      | 与上一条类似，但通过 --single-branch 参数优化，可节省时间和磁盘空间  |
   | 克隆所有分支 ​       | git clone <仓库 URL>后执行 git branch -a       | 所有分支的元数据 ​        | 本地工作目录仅显示默认分支，但可通过 git checkout 切换到任何其他分支 |

最佳实践：

- 在 ubuntu 机器上，先拉取 subtree 分支：
  `git clone -b tree --single-branch <仓库URL>`

- 切换到手动管理模式（main 分支）：
  `git checkout main`
  Git 会自动从远程老区 main 分支的最新内容
