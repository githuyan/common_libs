## Tmux

> 一个终端复用器，在Linux中我们开启一个窗口就开启了一个会话，这个窗口关闭，这个会话也中断了，使用 Tmux 命令可以再一一个窗口开启多个会话，方便调试

**参考：** [Tmux 使用教程 - 阮一峰的网络日志 (ruanyifeng.com)](https://www.ruanyifeng.com/blog/2019/10/tmux.html)

### 基础操作

> tmux 的绝大部分快捷键都需要先 ctrl + b 作为前缀操作

```shell
tmux ls  // 查看当前所有会话

tmux new -s <session-name>  // 创建一个新的会话
tmux kill-session -t <session-name>  // 删除会话
ctrl + b d  // 退出（分离）当前窗口 

tmux attach-session -t <session-name>  // 重新接入会话
tmux switch -t <session-name>   // 切换会话
tmux rename-session -t old_name new_name  // 重命名

ctrl + b  s  // 列出所有会话

// 窗格操作
tmux split-window  // 划分上下两个窗格
ctrl + b % 

tmux split-window -h  // 划分左右两个窗格
ctrl + b "  

// 光标移动
tmux select-pane -u  // 上
ctrl + b ;

d 下
l 左
r 右

ctrl + b x  // 关闭当前窗口
ctl + b q   // 显示窗格编号
ctl + b z   // 当前窗口全屏显示， 重复使用变回原来大小


// 列出所有快捷键
tmux list-keys

// 列出所有 tmux 会话的信息
tmux info

```

