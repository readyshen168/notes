# 服务器配置

[参考元宝链接](https://yuanbao.tencent.com/chat/naQivTmsDa/bf91ad3c-d698-4da6-820a-53e19cf0455f)

## 安全

### 云服务器安全组规则

界面中的最后一条 ::/0 ALL 拒绝可能只是平台对 IPv6 流量的显式拒绝，而 IPv4 流量的默认拒绝是系统内置的。

在未设置允许 ICMP 规则的前提下能 ping 通服务器，是因为 UFW 允许服务器回应来自外部的 ping 请求，而不是因为它允许外部的 ping 请求进入。这个回应过程得益于 UFW 的默认允许出站策略和有状态包检测机制。

### UFW 防火墙设置

- 检查状态: `sudo ufw status`
- 临时关闭: `sudo ufw disable`
- 永久关闭: `sudo systemctl disable ufw` `sudo systemctl stop ufw`
- 重新启用: `sudo ufw enable`

## SHELL 设置

### oh-my-zh [参考](https://yuanbao.tencent.com/chat/naQivTmsDa/6cf2188d-1a7b-448e-a945-bf01564a906b)

1. 安装 zsh: `sudo apt update && sudo apt install zsh`

2. 安装 oh-my-zsh: `sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"`
   此时出现~/.oh-my-zsh/ 目录， 里面有 oh-my-zsh.sh、plugins 目录、templates/zshrc.zsh-template 文件

3. 把 SHELL 换成 zsh: `sudo chsh -s $(which zsh)`, 需要重启系统 `sudo reboot`

4. 手动建立~/.zshrc 文件：

   ```bash
   cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
   source ~/.zshrc
   ```

5. 关于 git 插件，已默认启用，~/.zshrc 中存在一行：plugins=(git)

6. 将~/.oh-my-zsh/plugins/git/README.md 复制到 notes/ 目录中，zshGitFast.md, 里面有大量 git 的快捷命令

7. 安装实用插件（推荐）
   为了获得更强大的命令行体验，强烈建议安装以下两个插件：

   - zsh-syntax-highlighting：为你输入的命令提供语法高亮，正确的命令显示绿色，错误显示红色。
   - zsh-autosuggestions：根据你的命令历史，在你输入时给出灰色提示，按 → 键即可补全。

   ```bash
    # 下载语法高亮插件
        git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

    # 下载自动建议插件
        git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

    # 下载完成后
        plugins=(
            git
            zsh-syntax-highlighting
            zsh-autosuggestions
        )
   ```

8. 主题更换：

   ```bash
     # ~/.zshrc:
   ZSH_THEME="agnoster"

     # 进阶推荐：Powerlevel10k 主题
   git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k

     # ~/.zshrc:
   ZSH_THEME="powerlevel10k/powerlevel10k"

   source ~/.zshrc

   ```

### 每天一个 git 快捷命令

1. gc: git commit -v | v 是 verbose 的简写，表示更详细的信息
2. gc!: git commit -v --amend | 修改最近一次提交，如果没有 v，则不会显示 diff 内容
3. gca: git commit add -v | 把已跟踪的文件的修改合并 add、commit 一次性提交
4. gpv: git push -v | 本地分支推送到远程仓库，显示详细信息
5. glols --all: git log --graph --pretty=‘%Cred%h%Creset -%C(auto)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset’ --stat | 美化展示的 git log
