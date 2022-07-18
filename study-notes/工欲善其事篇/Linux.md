#### 批量杀死所有进程

ps -aux | grep python | awk '{print $2}' | xargs kill -9