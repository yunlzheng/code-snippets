# Shell Script

## 变量

设置环境变量

> 当登录获取bash之后，当前bash就是一个独立的进程，任何由该bash衍生出的命令被称为子进程

> 子进程仅会继承父继承的环境变量，不会继承父进程的自定义变量

```shell
# 自定义变量（局部变量）
myname=ylzheng

# 自定义环境变量（全局变量，子进程中可用）
export myname=ylzheng

# 在当前进程中执行文件定义内容(父进程中生效)
source ~/.bashrc
```

取消环境变量

```
unset myname
```

查看环境变量

```
# 查看环境变量
env
# 查看所有变量
set
```

特殊的环境变量：

* HOME: 代表用户的主文件夹
* SHELL: 当前环境使用的shell程序
* PATH：执行文件查找路径
* LANG：语系数据
* RANDOM: 随机数生成器
* PS1: 提示符设置，例如: ${ret_status} %{$fg[cyan]%}%c%{$reset_color%} $(git_prompt_info)
* $: 当前SHELL的PID，例如： echo $$
* ?: 上一个命令的执行状态码：例如： echo $?·

## 命令别名

别名设置

```
alias lm='ls -l | more'
```

查看所有别名

```
alias
```

取消别名

```
unalias lm
```

## 数据流从定向

## 管道命令

### 选取命令：cut.grep

### 排序命令：sort,wc,uniq

### 字符转换命令：tr,col,join,paste,expand

### 参数代换：xargs
