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

### cut分解数据或者文字

根据-d的分割字符将信息切割成为数段，并取出-f指定的段

```shell
echo $PATH | cut -d ':' -f 1,2,3,5
```

指定输出第12-15的字符

```shell
export | cut -c 12-15


```

### grep匹配

```shell
# 当一行中出现root就取出
last | grep 'root'

# 当一行中没有root就取出 
last | grep -v 'root'

# 使用特殊颜色显示关键字
last | grep --color=auto 'root'

# 使用正则表达式匹配
last | grep [a-zA-Z]
```

### sort排序

```shell
# 以文字类型顺序排序
cat /etc/passwd | sort

# 通过-t指定内容按照“：”进行分为列，并以第3列为基准文字类型排序
cat /etc/passwd | sort -t ':' -k 3

# 通过-t指定内容按照“：”进行分为列，并以第3列为基准排序，并以数字类型排序
cat /etc/passwd | sort -t ':' -k 3 -n
```

### uniq去重

```shell
# 获取所有登录信息 | 获取最后登录名 | 并按照文字顺序排序 | 去除所有重复项，-c自定显示统计计数
last | cut -d ' ' -f 1 | sort | uniq -c
```

### wc计算文件内容

```shell
# 统计文件中的行数（-l）字数(-w)以及字符数（-m）
cat /etc/man.conf  | wc -l -w -m
```

### tee双向重定向

```shell
# 将last的输出写入到last.list文件的同时，也将输出作为cut的输入
last | tee last.list | cut -d " " -f 1

# 将last的输出追加到last.list文件的同时，也将输出作为cut的输入
last | tee -a last.list | cut -d " " -f 1
```

### tr删除或者替换文本

```shell
# 将所有的小写字母变为大写字母
last | tr '[a-z]' '[A-Z]'

# 删除输出中所有的“：”
cat /etc/passwd | tr -d ':'
```

## 日志查询技巧

```
sed -n '/起始时间/,/结束时间/p' 日志文件
sed -n '/2014-12-17 16:17:20/,/2014-12-17 16:17:36/p'  test.log
```
