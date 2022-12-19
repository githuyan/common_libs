## Tmux

> 一个终端复用器，在Linux中我们开启一个窗口就开启了一个会话，这个窗口关闭，这个会话也中断了，使用 Tmux 命令可以再一一个窗口开启多个会话，方便调试

**参考：** [Tmux 使用教程 - 阮一峰的网络日志 (ruanyifeng.com)](https://www.ruanyifeng.com/blog/2019/10/tmux.html)

### 基础操作

> tmux 的绝大部分快捷键都需要先 ctrl + b 作为前缀操作
>
> 先按 ctrl + b 再按 <指令键>

```shell
# 所有的快捷键操作都是在会话内

# 查看信息
tmux ls  # 查看当前所有会话（会话外|内）
ctrl + b s  # 查看所有会话
tmux list-keys  # 列出所有快捷键
tmux info  # 列出所有 tmux 会话的信息
ctl + b q   # 显示窗格编号


# 会话设置
tmux rename-session -t <old_session-name|id> <new_session-name>   # 重命名会话（会话外）
ctrl + b $  # 重命名会话

tmux new -s <session-name|id>  # 创建会话（会话外）
tmux attach-session -t <session-name|id>  # 重新接入会话（会话外）
tmux switch -t <session-name|id>  # 切换会话（会话外）

ctrl + d  # 退出本	次会话
    
tmux kill-session -t <session-name|id>  # 删除会话（会话外）


# 常用窗口操作
ctrl + b x  # 关闭当前窗口

ctrl + b 上下左右键  # 光标在窗口间移动

ctrl + b %   # (这个%需要使用shift+%)  上下切分窗口
ctrl + b "  # (这个"需要使用shift+")  左右切分窗口


tmux split-window  # 划分上下两个窗格
ctrl + b %   # (这个%需要使用shift+%)

tmux split-window -h  # 划分左右两个窗格
ctrl + b "  # (这个"需要使用shift+")

# 光标移动
tmux select-pane -u  # 上
ctrl + b ;

d 下
l 左
r 右

ctl + b q   # 显示窗格编号
ctl + b z   # 当前窗口全屏显示， 重复使用变回原来大小
```

