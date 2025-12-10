---
title: "常用命令行指令参考"
date: 2025-12-10T11:28:47+08:00
draft: false
categories: ['文档介绍']
summary: "详细的命令行指令参考手册，包含 Windows PowerShell、Linux Terminal、Git 和 GitHub CLI 的常用命令及实用技巧" 
description: "一份完整的开发者命令行速查表，涵盖 Windows PowerShell、Linux/Unix 终端、Git 版本控制和 GitHub CLI 工具的常用指令、参数说明|和实际使用场景示例"
comments: true
tags: ["命令行", "PowerShell", "Linux", "Git", "GitHub"] 
---

## Windows PowerShell 常用指令

### 文件和目录操作

**列出目录内容**

- `Get-ChildItem` / `ls` - 列出当前目录内容
- `ls -Force` - 显示隐藏文件
- `ls -Recurse` - 递归列出所有子目录
- `ls | Sort-Object Length -Descending` - 按文件大小排序

**切换目录**

- `Set-Location <路径>` / `cd` - 切换目录
- `cd ~` - 返回用户主目录
- `cd ..` - 返回上级目录
- `cd -` - 返回上一个目录

**创建文件和目录**

- `New-Item -ItemType Directory <名称>` / `mkdir` - 创建目录
- `New-Item -ItemType File <名称>` / `ni` - 创建文件

**复制、移动和删除**

- `Copy-Item <源> <目标>` 复制文件
  
- `Move-Item <源> <目标>` / `mv` - 移动/重命名文件

- `Remove-Item <路径> ` 删除文件

**查看文件**

- `Get-Content <文件>` / `cat` - 查看文件内容
- `Get-Content <文件> -Tail 10` - 查看最后 10 行
- `Get-Content <文件> -Wait` - 实时查看文件更新（类似 tail -f）
- `Test-Path <路径>` - 检查路径是否存在

------

### 文本处理和搜索

- `Select-String <模式> <文件>` - 在文件中搜索文本（类似 grep）
- `Select-String "error" *.log` - 在所有 log 文件中搜索
- `ls -Recurse | Select-String "TODO"` - 递归搜索
- `Out-File <文件>` - 输出重定向到文件
- `Tee-Object <文件>` - 同时输出到控制台和文件

------

### 系统信息和管理

**进程管理**

- `Get-Process` / `ps` - 查看进程列表
- `Get-Process | Sort-Object CPU -Descending | Select-Object -First 5` - CPU 占用最高的 5 个进程
- `Stop-Process -Name <进程名>` - 结束进程

**服务管理**

- `Get-Service` - 查看服务列表
- `Get-Service | Where-Object {$_.Status -eq "Running"}` - 只显示运行中的服务
- `Start-Service <服务名>` - 启动服务
- `Stop-Service <服务名>` - 停止服务

**系统信息**

- `Get-ComputerInfo` - 查看计算机信息
- `Get-NetIPAddress` - 查看网络配置
- `Get-Disk` - 查看磁盘信息
- `Get-Volume` - 查看卷信息
- `systeminfo` - 显示详细系统信息

------

### 环境变量

- `$env:PATH` - 查看 PATH 环境变量
- `$env:变量名 = "值"` - 设置临时环境变量
- `[Environment]::SetEnvironmentVariable("变量名", "值", "User")` - 永久设置

------

### 网络操作

- `Test-Connection <主机>` / `ping` - 测试网络连接
- `Invoke-WebRequest <URL>` / `curl` - 发送 HTTP 请求
- `Invoke-WebRequest -Uri <URL> -OutFile <文件>` - 下载文件
- `Get-NetTCPConnection` - 查看 TCP 连接
- `ipconfig` - 查看 IP 配置
- `ipconfig /flushdns` - 刷新 DNS 缓存

------

### 实用命令

- `Clear-Host` / `cls` - 清屏
- `Get-History` / `history` - 查看命令历史
- `Get-Help <命令>` - 获取帮助
- `Get-Help <命令> -Examples` - 查看使用示例
- `Get-Command` - 列出所有可用命令
- `Get-Command *process*` - 搜索包含 process 的命令
- `Get-Alias` - 查看所有别名
- `Measure-Object` - 统计对象
- `Start-Process <程序>` - 启动程序
- `Get-Location` / `pwd` - 显示当前路径

------

## Linux Terminal 常用指令

### 文件和目录操作

**列出目录**

- `ls` - 列出目录内容
- `ls -l` - 长格式显示（详细信息）
- `ls -a` - 显示隐藏文件
- `ls -lh` - 人性化显示文件大小
- `ls -lt` - 按修改时间排序
- `ls -lS` - 按文件大小排序
- `ls -R` - 递归列出所有子目录

**切换目录**

- `cd <路径>` - 切换目录
- `cd` / `cd ~` - 返回主目录
- `cd ..` - 返回上级目录
- `cd -` - 返回上一个目录
- `pwd` - 显示当前完整路径

**创建**

- `mkdir <目录名>` - 创建目录
- `mkdir -p a/b/c` - 递归创建多级目录
- `touch <文件名>` - 创建空文件或更新时间戳

**复制、移动和删除**

- `cp <源> <目标>` - 复制文件
- `cp -r <源目录> <目标>` - 递归复制目录
- `cp -i <源> <目标>` - 交互式复制（覆盖前询问）
- `mv <源> <目标>` - 移动/重命名文件
- `rm <文件>` - 删除文件
- `rm -r <目录>` - 递归删除目录
- `rm -f <文件>` - 强制删除
- `rm -rf <目录>` - 强制递归删除（危险！）
- `rm -i <文件>` - 交互式删除（推荐）

**链接**

- `ln -s <源> <链接名>` - 创建符号链接
- `readlink -f <链接>` - 查看链接目标

------

### 文件查看和编辑

**查看文件**

- `cat <文件>` - 显示文件内容
- `cat -n <文件>` - 显示行号
- `less <文件>` - 分页查看（可前后翻页）
- `more <文件>` - 分页查看（只能向前）
- `head <文件>` - 查看文件开头
- `head -n 20 <文件>` - 查看前 20 行
- `tail <文件>` - 查看文件末尾
- `tail -n 20 <文件>` - 查看最后 20 行
- `tail -f <文件>` - 实时查看文件更新
- `tail -f <文件> | grep "error"` - 实时过滤查看

**编辑文件**

- `nano <文件>` - 简单文本编辑器
- `vim <文件>` / `vi <文件>` - 强大的文本编辑器

**统计文件**

- `wc <文件>` - 统计文件
- `wc -l <文件>` - 统计行数
- `wc -w <文件>` - 统计单词数
- `wc -c <文件>` - 统计字节数

------

### 文件搜索和查找

**find 命令**

- `find <路径> -name <名称>` - 按名称查找
- `find . -name "*.txt"` - 查找所有 txt 文件
- `find . -type f -name "*.log"` - 查找文件（不含目录）
- `find . -type d -name "node_modules"` - 查找目录
- `find . -mtime -7` - 查找 7 天内修改的文件
- `find . -size +100M` - 查找大于 100MB 的文件
- `find . -name "*.tmp" -delete` - 查找并删除

**grep 命令**

- `grep <模式> <文件>` - 搜索文本
- `grep -r "TODO" .` - 递归搜索当前目录
- `grep -i "error" log.txt` - 忽略大小写
- `grep -n "function" code.js` - 显示行号
- `grep -v "debug" log.txt` - 反向匹配（不包含）
- `grep -E "error|warning" log.txt` - 使用正则表达式
- `grep -c "error" log.txt` - 统计匹配行数

**其他查找工具**

- `locate <文件名>` - 快速查找文件
- `updatedb` - 更新 locate 数据库
- `which <命令>` - 查找命令位置
- `whereis <程序>` - 查找程序位置

------

### 文件权限

- `chmod <权限> <文件>` - 修改文件权限
- `chmod 755 script.sh` - 数字方式（rwxr-xr-x）
- `chmod +x script.sh` - 添加执行权限
- `chmod -R 644 directory/` - 递归修改
- `chown <用户>:<组> <文件>` - 修改所有者
- `chgrp <组> <文件>` - 修改所属组
- `umask` - 查看或设置默认权限掩码

------

### 进程管理

**查看进程**

- `ps` - 查看进程
- `ps aux` - 查看所有进程详细信息
- `ps -ef` - 另一种格式
- `ps aux | grep <进程名>` - 搜索特定进程
- `top` - 实时查看系统资源（按 q 退出）
- `htop` - 增强版 top

**管理进程**

- `kill <PID>` - 结束进程
- `kill -9 <PID>` - 强制结束
- `killall <进程名>` - 按名称结束进程
- `pkill <进程名>` - 按模式匹配结束
- `bg` - 将任务放到后台
- `fg` - 将后台任务调到前台
- `jobs` - 查看后台任务
- `nohup <命令> &` - 后台运行，不受终端关闭影响

------

### 磁盘和存储

- `df -h` - 查看磁盘使用情况
- `du -sh <目录>` - 查看目录大小
- `du -h --max-depth=1` - 查看各子目录大小
- `du -sh * | sort -h` - 按大小排序
- `free -h` - 查看内存使用
- `lsblk` - 列出块设备
- `mount` - 挂载文件系统
- `umount` - 卸载文件系统

------

### 系统信息

- `uname -a` - 完整系统信息
- `uname -r` - 内核版本
- `hostname` - 主机名
- `whoami` - 当前用户
- `id` - 用户 ID 和组 ID
- `uptime` - 系统运行时间和负载
- `date` - 系统时间
- `cal` - 日历
- `lsb_release -a` - Linux 发行版信息（Ubuntu/Debian）
- `cat /etc/os-release` - 系统版本信息

------

### 网络操作

**连接测试**

- `ping <地址>` - 测试网络连接
- `ping -c 4 google.com` - 发送 4 个包后停止
- `traceroute <地址>` - 追踪路由路径

**文件传输**

- `curl <URL>` - 发送 HTTP 请求
- `curl -O <URL>` - 下载文件（保持原文件名）
- `curl -o <文件名> <URL>` - 下载并重命名
- `curl -I <URL>` - 只获取 HTTP 头
- `curl -X POST -d "data" <URL>` - 发送 POST 请求
- `wget <URL>` - 下载文件
- `wget -c <URL>` - 断点续传

**远程连接**

- `ssh <用户>@<主机>` - SSH 连接
- `ssh -p <端口> <用户>@<主机>` - 指定端口
- `scp <源> <用户>@<主机>:<目标>` - 安全复制文件
- `scp -r <目录> <用户>@<主机>:<目标>` - 复制目录

**网络信息**

- `netstat -tuln` - 查看网络端口（传统）
- `ss -tuln` - 查看网络端口（现代）
- `ip addr` / `ifconfig` - 查看网络接口
- `nslookup <域名>` - DNS 查询
- `dig <域名>` - DNS 详细查询

------

### 压缩和解压

**tar 格式**

- `tar -czf <文件.tar.gz> <目录>` - 压缩为 tar.gz
- `tar -xzf <文件.tar.gz>` - 解压 tar.gz
- `tar -xzf <文件.tar.gz> -C <目标目录>` - 解压到指定目录
- `tar -tvf <文件.tar.gz>` - 查看压缩包内容

**zip 格式**

- `zip -r <文件.zip> <目录>` - 创建 zip 压缩包
- `unzip <文件.zip>` - 解压 zip
- `unzip <文件.zip> -d <目标目录>` - 解压到指定目录

**gzip 格式**

- `gzip <文件>` - 压缩为 .gz
- `gunzip <文件.gz>` - 解压 .gz

------

### 文本处理工具

**sed（流编辑器）**

- `sed 's/old/new/g' file.txt` - 替换文本
- `sed -i 's/old/new/g' file.txt` - 直接修改文件

**awk（文本处理）**

- `awk '{print $1}' file.txt` - 打印第一列
- `awk -F',' '{print $2}' file.csv` - 指定分隔符

**排序和去重**

- `sort <文件>` - 排序
- `sort -r <文件>` - 反向排序
- `sort -n <文件>` - 按数字排序
- `uniq <文件>` - 去除重复行
- `sort file.txt | uniq` - 配合使用
- `uniq -c <文件>` - 统计重复次数

**其他工具**

- `cut -d',' -f1,3 file.csv` - 按分隔符提取列
- `tr 'a-z' 'A-Z' < file.txt` - 字符转换

------

### 系统管理

**用户管理**

- `sudo <命令>` - 以管理员权限执行
- `su` - 切换用户
- `su -` - 切换到 root
- `passwd` - 修改密码
- `useradd <用户名>` - 创建用户
- `userdel <用户名>` - 删除用户
- `usermod -aG <组> <用户>` - 添加用户到组

**服务管理（systemd）**

- `systemctl start <服务>` - 启动服务
- `systemctl stop <服务>` - 停止服务
- `systemctl restart <服务>` - 重启服务
- `systemctl status <服务>` - 查看状态
- `systemctl enable <服务>` - 设置开机启动
- `journalctl -u <服务>` - 查看服务日志

------

### 实用快捷键

- **Ctrl + C** - 终止当前命令
- **Ctrl + Z** - 暂停当前命令
- **Ctrl + D** - 退出终端
- **Ctrl + L** - 清屏
- **Ctrl + A** - 光标移到行首
- **Ctrl + E** - 光标移到行尾
- **Ctrl + U** - 删除光标前的内容
- **Ctrl + K** - 删除光标后的内容
- **Ctrl + R** - 搜索命令历史
- **Tab** - 自动补全
- **!!** - 执行上一条命令
- **!$** - 上一条命令的最后一个参数

------

### 其他实用命令

- `clear` - 清屏
- `history` - 查看命令历史
- `history | grep <关键词>` - 搜索历史命令
- `echo <文本>` - 输出文本
- `echo "text" > file.txt` - 覆盖写入
- `echo "text" >> file.txt` - 追加写入
- `alias <别名>='<命令>'` - 创建别名
- `watch <命令>` - 定期执行命令
- `watch -n 2 df -h` - 每 2 秒更新
- `xargs` - 将标准输入转换为命令参数
- `tee <文件>` - 同时输出到文件和标准输出

------

## Git 常用指令

### 初始化和配置

**初始化仓库**

- `git init` - 初始化新仓库
- `git clone <URL>` - 克隆远程仓库
- `git clone <URL> <目录名>` - 克隆到指定目录
- `git clone --depth 1 <URL>` - 浅克隆（只克隆最新提交）
- `git clone -b <分支名> <URL>` - 克隆指定分支

**配置 Git**

- `git config --global user.name "<名字>"` - 设置用户名
- `git config --global user.email "<邮箱>"` - 设置邮箱
- `git config --list` - 查看所有配置
- `git config --global core.editor "vim"` - 设置编辑器
- `git config --global alias.st status` - 设置别名

------

### 基本操作

**查看状态**

- `git status` - 查看工作区状态
- `git status -s` - 简短格式

**添加和提交**

- `git add <文件>` - 添加文件到暂存区
- `git add .` - 添加所有修改
- `git add -A` - 添加所有变化（包括删除）
- `git add -p` - 交互式添加
- `git commit -m "<消息>"` - 提交
- `git commit -am "<消息>"` - 添加并提交已跟踪文件
- `git commit --amend` - 修改最后一次提交
- `git commit --amend --no-edit` - 修改提交但不改消息

**推送和拉取**

- `git push` - 推送到远程
- `git push origin <分支名>` - 推送指定分支
- `git push -u origin <分支名>` - 推送并设置上游
- `git push --force` / `git push -f` - 强制推送（危险！）
- `git push --tags` - 推送标签
- `git pull` - 拉取并合并
- `git pull --rebase` - 使用 rebase 方式拉取
- `git fetch` - 获取但不合并
- `git fetch --all` - 获取所有远程分支
- `git fetch --prune` - 获取并清理已删除的远程分支

------

### 分支操作

**查看分支**

- `git branch` - 查看本地分支
- `git branch -a` - 查看所有分支（包括远程）
- `git branch -r` - 只查看远程分支
- `git branch -v` - 查看分支及最后一次提交

**创建和切换分支**

- `git branch <分支名>` - 创建分支
- `git checkout <分支名>` - 切换分支
- `git checkout -b <分支名>` - 创建并切换
- `git checkout -b <分支名> origin/<分支名>` - 从远程分支创建
- `git switch <分支名>` - 切换分支（新语法）
- `git switch -c <分支名>` - 创建并切换（新语法）

**合并和删除分支**

- `git merge <分支名>` - 合并分支
- `git merge --no-ff <分支名>` - 非快进合并
- `git merge --squash <分支名>` - 压缩合并
- `git branch -d <分支名>` - 删除已合并分支
- `git branch -D <分支名>` - 强制删除分支
- `git branch -m <旧名> <新名>` - 重命名分支
- `git push origin --delete <分支名>` - 删除远程分支

------

### 查看历史

**日志查看**

- `git log` - 查看提交历史
- `git log --oneline` - 每个提交一行
- `git log --graph` - 图形化显示
- `git log --oneline --graph --all` - 图形化显示所有分支
- `git log -p` - 显示详细差异
- `git log --author="<作者>"` - 按作者筛选
- `git log --since="2 weeks ago"` - 按时间筛选
- `git log --grep="<关键词>"` - 搜索提交消息
- `git log <文件>` - 查看文件历史
- `git log --stat` - 显示文件统计

**差异比较**

- `git diff` - 查看未暂存的更改
- `git diff --staged` / `git diff --cached` - 查看已暂存的更改
- `git diff <分支1> <分支2>` - 比较分支
- `git diff <提交1> <提交2>` - 比较提交
- `git diff HEAD` - 比较工作区和最新提交

**其他查看命令**

- `git show <提交>` - 查看提交详情
- `git show HEAD` - 查看最新提交
- `git blame <文件>` - 查看每行的修改者
- `git shortlog` - 按作者分组显示

------

### 撤销和回退

**撤销修改**

- `git restore <文件>` - 撤销工作区修改（新语法）
- `git restore --staged <文件>` - 取消暂存（新语法）
- `git checkout -- <文件>` - 撤销修改（旧语法）

**重置提交**

- `git reset <文件>` - 取消暂存
- `git reset HEAD~` - 撤销最后一次提交，保留更改
- `git reset --soft HEAD~` - 撤销提交，更改在暂存区
- `git reset --hard HEAD~` - 撤销提交并丢弃更改（危险！）
- `git reset --hard <提交>` - 重置到指定提交

**回退操作**

- `git revert <提交>` - 创建新提交来撤销
- `git revert HEAD` - 撤销最新提交
- `git clean -fd` - 删除未跟踪的文件
- `git clean -n` - 预览将要删除的文件

------

### 暂存操作（Stash）

- `git stash` - 暂存当前更改
- `git stash save "描述"` - 暂存并添加描述
- `git stash list` - 查看暂存列表
- `git stash pop` - 恢复并删除最新暂存
- `git stash apply` - 恢复但不删除暂存
- `git stash apply stash@{0}` - 恢复指定暂存
- `git stash drop` - 删除最新暂存
- `git stash drop stash@{0}` - 删除指定暂存
- `git stash clear` - 清空所有暂存
- `git stash show` - 查看暂存内容

------

### 远程仓库

**查看远程**

- `git remote` - 查看远程仓库
- `git remote -v` - 查看详细信息

**管理远程**

- `git remote add <名称> <URL>` - 添加远程仓库
- `git remote add origin <URL>` - 添加 origin
- `git remote remove <名称>` - 删除远程仓库
- `git remote rename <旧名> <新名>` - 重命名
- `git remote set-url <名称> <新URL>` - 修改 URL
- `git remote show <名称>` - 查看详细信息
- `git remote prune origin` - 清理已删除的远程分支引用

------

### 标签操作

- `git tag` - 查看所有标签
- `git tag <标签名>` - 创建轻量标签
- `git tag -a <标签名> -m "<消息>"` - 创建带注释的标签
- `git tag -d <标签名>` - 删除本地标签
- `git push origin <标签名>` - 推送标签
- `git push origin --tags` - 推送所有标签
- `git push origin :refs/tags/<标签名>` - 删除远程标签

------

### 高级操作

**变基（Rebase）**

- `git rebase <分支>` - 变基到指定分支
- `git rebase -i HEAD~3` - 交互式变基最近 3 个提交
- `git rebase --continue` - 解决冲突后继续
- `git rebase --abort` - 取消变基

**其他高级命令**

- `git cherry-pick <提交>` - 将指定提交应用到当前分支
- `git reflog` - 查看引用日志（恢复丢失的提交）
- `git bisect start` - 开始二分查找问题提交
- `git submodule add <URL>` - 添加子模块
- `git submodule update --init` - 初始化并更新子模块

------

### .gitignore 配置

`New-Item -ItemType File .gitignore`创建 `.gitignore` 文件来忽略不需要跟踪的文件：

```gitignore
# 忽略日志文件
*.log

# 忽略 node_modules 目录
node_modules/

# 忽略环境变量文件
.env
.env.local

# 忽略构建输出
dist/
build/

# 忽略操作系统文件
.DS_Store
Thumbs.db

# 忽略 IDE 配置
.vscode/
.idea/
```

------

## GitHub CLI 常用指令

### 认证

- `gh auth login` - 登录 GitHub 账号
- `gh auth status` - 查看认证状态
- `gh auth logout` - 登出
- `gh auth refresh` - 刷新令牌
- `gh config set editor vim` - 设置编辑器

------

### 仓库操作

**创建和克隆**

- `gh repo create` - 创建仓库
- `gh repo create <名称>` - 创建指定名称的仓库
- `gh repo create --public` - 创建公开仓库
- `gh repo create --private` - 创建私有仓库
- `gh repo create --clone` - 创建并克隆
- `gh repo clone <仓库>` - 克隆仓库
- `gh repo clone owner/repo` - 克隆指定仓库

**查看和管理**

- `gh repo view` - 查看仓库信息
- `gh repo view --web` - 在浏览器中打开
- `gh repo list` - 列出仓库
- `gh repo list <用户名>` - 列出指定用户的仓库
- `gh repo list --limit 50` - 限制显示数量
- `gh repo fork` - Fork 仓库
- `gh repo fork --clone` - Fork 并克隆
- `gh repo delete <仓库>` - 删除仓库
- `gh repo rename <新名称>` - 重命名仓库  or `gh repo rename owner/repo 新名字`
- `gh repo sync` - 同步 fork

------

### Pull Request

**创建 PR**

- `gh pr create` - 创建 PR
- `gh pr create --title "标题" --body "描述"` - 指定标题和描述
- `gh pr create --web` - 在浏览器中创建
- `gh pr create --draft` - 创建草稿 PR

**查看 PR**

- `gh pr list` - 列出 PR
- `gh pr list --state open` - 只显示开放的
- `gh pr list --author <用户名>` - 按作者筛选
- `gh pr list --label <标签>` - 按标签筛选
- `gh pr view <编号>` - 查看详情
- `gh pr view <编号> --web` - 在浏览器中查看
- `gh pr view <编号> --comments` - 查看评论

**操作 PR**

- `gh pr checkout <编号>` - 检出 PR 分支
- `gh pr diff <编号>` - 查看差异
- `gh pr merge <编号>` - 合并 PR
- `gh pr merge <编号> --squash` - 压缩合并
- `gh pr merge <编号> --rebase` - 变基合并
- `gh pr close <编号>` - 关闭 PR
- `gh pr reopen <编号>` - 重新打开
- `gh pr ready <编号>` - 标记为准备好

**审查 PR**

- `gh pr review <编号>` - 审查 PR
- `gh pr review <编号> --approve` - 批准
- `gh pr review <编号> --request-changes` - 请求更改
- `gh pr review <编号> --comment` - 添加评论
- `gh pr checks <编号>` - 查看 CI/CD 状态

------

### Issue

**创建 Issue**

- `gh issue create` - 创建 Issue
- `gh issue create --title "标题" --body "描述"` - 指定内容
- `gh issue create --web` - 在浏览器中创建
- `gh issue create --label "bug,help wanted"` - 添加标签

**查看 Issue**

- `gh issue list` - 列出 Issues
- `gh issue list --state open` - 只显示开放的
- `gh issue list --assignee <用户名>` - 按指派人筛选
- `gh issue list --label <标签>` - 按标签筛选
- `gh issue list --author <用户名>` - 按作者筛选
- `gh issue view <编号>` - 查看详情
- `gh issue view <编号> --web` - 在浏览器中查看

**操作 Issue**

- `gh issue close <编号>` - 关闭 Issue
- `gh issue close <编号> --comment "已修复"` - 关闭并评论
- `gh issue reopen <编号>` - 重新打开
- `gh issue edit <编号>` - 编辑 Issue
- `gh issue edit <编号> --add-label "bug"` - 添加标签
- `gh issue comment <编号>` - 添加评论
- `gh issue status` - 查看状态

------

### Gist

- `gh gist create <文件>` - 创建 Gist
- `gh gist create --public <文件>` - 创建公开 Gist
- `gh gist create --desc "描述" <文件>` - 添加描述
- `gh gist list` - 列出 Gists
- `gh gist view <ID>` - 查看 Gist
- `gh gist view <ID> --web` - 在浏览器中查看
- `gh gist edit <ID>` - 编辑 Gist
- `gh gist delete <ID>` - 删除 Gist
- `gh gist clone <ID>` - 克隆到本地

------

### Release

**创建和查看**

- `gh release create <标签>` - 创建 Release
- `gh release create v1.0.0 --title "版本 1.0.0" --notes "说明"` - 指定内容
- `gh release create v1.0.0 *.zip` - 附加文件
- `gh release create v1.0.0 --draft` - 创建草稿
- `gh release list` - 列出所有 Releases
- `gh release view <标签>` - 查看详情
- `gh release view <标签> --web` - 在浏览器中查看

**下载和管理**

- `gh release download <标签>` - 下载资源
- `gh release download <标签> --pattern "*.zip"` - 下载匹配文件
- `gh release delete <标签>` - 删除 Release
- `gh release upload <标签> <文件>` - 上传文件

------

### GitHub Actions

**工作流管理**

- `gh workflow list` - 列出工作流
- `gh workflow view <工作流>` - 查看详情
- `gh workflow view <工作流> --web` - 在浏览器中查看
- `gh workflow run <工作流>` - 触发工作流
- `gh workflow run <工作流> --ref <分支>` - 在指定分支运行

**运行管理**

- `gh run list` - 列出运行记录
- `gh run list --workflow <工作流名>` - 按工作流筛选
- `gh run view <运行ID>` - 查看详情
- `gh run view <运行ID> --log` - 查看日志
- `gh run watch <运行ID>` - 实时查看状态
- `gh run rerun <运行ID>` - 重新运行
- `gh run cancel <运行ID>` - 取消运行

------

### 其他功能

**浏览和搜索**

- `gh browse` - 在浏览器中打开仓库
- `gh browse <文件>` - 打开指定文件
- `gh browse --settings` - 打开设置
- `gh search repos <关键词>` - 搜索仓库
- `gh search repos --stars ">1000"` - 按星标搜索
- `gh search repos --language python` - 按语言搜索
- `gh search issues <关键词>` - 搜索 Issues
- `gh search prs <关键词>` - 搜索 PRs

**API 和扩展**

- `gh api <端点>` - 调用 GitHub API
- `gh api user` - 获取用户信息
- `gh alias set <别名> <命令>` - 创建别名
- `gh extension install <扩展>` - 安装扩展
- `gh extension list` - 列出扩展

**标签和项目**

- `gh label list` - 列出标签
- `gh label create <名称>` - 创建标签
- `gh project list` - 列出项目
- `gh project view <编号>` - 查看项目