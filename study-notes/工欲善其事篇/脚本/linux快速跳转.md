#### linux快速跳转目录脚本

> 放在linux脚本目录，如.bashrc, profile.d，在此目录下的脚本会开机自启， （需要重启）/etc/profile.d (都放在.bashrc文件中也可以)

**参考：**

- [(4条消息) Linux系统启动运行自定义脚本_qq_1034406352的博客-CSDN博客](https://blog.csdn.net/qq_35180423/article/details/120702360) 

##### 源码（linux)

```shell
#!/bin/sh  
# mark
export MARKPATH=$HOME/.marks
export MARKDEFAULT=sanguo#设置你的默认书签，可以直接输入g跳转

function g {
    local m=$1
    if [ "$m" = "" ]; then m=$MARKDEFAULT; fi
    cd -P "$MARKPATH/$m" 2>/dev/null || echo "No such mark: $m"
}
function mark {
    mkdir -p "$MARKPATH"
    local m=$1
    if [ "$m" = "" ]; then m=$MARKDEFAULT; fi
    rm -f "$MARKPATH/$m"
    ln -s "$(pwd)" "$MARKPATH/$m"
}
function unmark {
    local m=$1
    if [ "$m" = "" ]; then m=$MARKDEFAULT; fi
    rm -i "$MARKPATH/$m"
}
function gs {
    ls -l "$MARKPATH" | grep ^l | cut -d ' ' -f 13-
}

_completemarks() {
    local curw=${COMP_WORDS[COMP_CWORD]}
    local wordlist=$(ls -l "$MARKPATH" | grep ^l | cut -d ' ' -f 13)
    COMPREPLY=($(compgen -W '${wordlist[@]}' -- "$curw"))
    return 0
}

complete -F _completemarks g unmark
```

**使用方法：**

1. 添加标记：跳转到目标目录，使用命令`mark alisa_name` 标记路径并设置别名
2. 移除标记：使用命令`unmark alisa_name`移除`alisa_name`标记
3. 跳转标记点：使用命令`g alisa_name`，跳转标记点



##### 注意事项

1. 此脚本需要放在linux指定目录，才能开机自启
