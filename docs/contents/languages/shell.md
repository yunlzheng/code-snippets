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

```
 ┌───────────┐            ┌───────────┐         ┌───────────┐
 |   file    | --stdin -> | Command   |-stdout->|  device   |
 └───────────┘            └───────────┘         └───────────┘
                                |
                              stderr
                                |
                           ┌───────────┐
                           |  device   |
                           └───────────┘
```

* 标准输入(stdin):代码为0，使用<或者<<;
* 标准输出（stdout）:代码为1，使用>或者>>;
* 标准错误输出（stderr）:代码为2，使用 2> 或者 2>>;

> ```>```表示覆盖数据，```>>```表示累加数据

示例：

```shell
# 分别将标准输出，和标准错误输出到文件中
find /home -name .bashrc > list_right 2> list_error
# 将标准错误输错到/dev/null
find /home -name .bashrc 2> /dev/null 
# 将标准输入输出写到同一个文件中
find /home -name .bashrc &> list 
```

标准输入：

```shell
# 将.bashrc写入到catfile
cat > catfile < ~/.bashrc

# 使用EOF将内容写入文件
cat > catfile << eof
> this is test
eof
```

### 命令执行判断依据

* cmd;cmd 不考虑命令相关性连续执行命令
* cmd1 && cmd2: 在cmd1执行成功的情况下执行cmd2
* cmd1 || cmd2: 在cmd1执行失败的情况下执行cmd2

示例：

```
ls /tmp/abc || mkdir /tmp/abc && touch /tmp/abc/hehe
```

## 管道命令

``` 
          stodou  | stdin        stdout |     stdin
 ┌───────────┐    |     ┌───────────┐   |      ┌───────────┐
 |   cmd1    |    |     |    cmd2   |   |      |    cmd3   |
 └───────────┘    |     └───────────┘   |      └───────────┘
                  |                     |
```

示例:

```
ls -al /etc | less
```

* 管道只能处理stdout,stderr会忽略；
* 管道命令必须能够接受来自前一个命令的数据成为stdin继续处理；

### 选取命令：cut.grep

### 排序命令：sort,wc,uniq

### 字符转换命令：tr,col,join,paste,expand

### 参数代换：xargs
