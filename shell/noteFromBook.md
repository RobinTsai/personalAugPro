Book Name: Linux命令行与shell脚本编程大全(第3版)

# Day 1

## command

- `ls` 
    + `-a`: all
    + `-l`: long info
    + `-F`: classify 分类
    + `-R`: recursion (这个好用，嵌套文件也能找)

- `ln` 
    + `-s`: 软链
    + 无 `-s`: 硬链，但不可链文件夹，同样是同一个文件，因为inode相同，可用 `ls -i` 查看

- `cat` 
    + `-n`: 行号
    + `-b`: 空行无行号
    + `-s`: 压缩连续的空行为一个

- `mkdir`
    + `-p`: parents, 自动创建缺失的父目录

- `uniq`: 按行去重
    + `-c`: count, 输出重复的次数
    + `-d`: print continuous duplicated lines ONLY ONCE
    + `-D`: print continuous duplicated lines ALL
    + `-s N`: skip first N chars, `N` is a number
    + `-i`: ignore case
    + `-u`: only print unique lines
    + `-w N`: only first N chars, `N` is a number

- `rm` 不进回收站，所以最好复写
- `less` > `more`: 比more多了强大的键盘操作
- `du`: 磁盘命令，可查看所有文件和大小（包括嵌套、隐藏文件）
- `ps`: 只能看当前用户进程
    + `-ef`: 查看所有进程
- `top`: 查看占用内存情况

## shell

- 外部命令：如 `ps`
- 内部命令：如 `cd`. 以上两个用 `type/which (-a)`试试看，还有 `whereis`
- `history`: 对应文件 `~/.zsh_history`. 在Terminal注销时，命令会更新到文件中
- `;`: 单行命令的串行执行
- `()`: 创建子shell执行命令。子shell的成本高，会拖慢速度
- `coproc`: 协程，并行方式执行命令
- `(zsh; zsh; zsh; ps --forest)`分析
    + 遇见 `(`，创建一个子shell, `)`结束这个子shell
    + 执行第一个zsh。然而这个时候，已经进入了第一个zsh shell，所以后面的命令没有执行
    + 当你按`CTRL+D`时，退出第一个zsh shell，执行第二个 zsh
    + 所以这个脚本从开始执行，你需要按三次 `CTRL+D`，才能输出命令 `ps --forest`的结果

# Day 2

## 环境变量

- `set` 单独用是查看用户变量，加参数是用来设置shell的执行方式。另外还有 `env`, `printenv` 来查看环境变量。
- 环境变量的设置要用 `=`，导成全局要用 `export`
- subshell中修改全局环境变量，仅当前shell中有效，用 `export`覆盖也不行
- subshell继承的变量只能是父shell导出(`export`)的
- 一般地，使用变量用 `$`，操作变量不用 `$`，仅 `printenv`例外.如 `unset $my_var`
    + 注意变量值为带有空格的字符串时，要使用 `"$var"`(带引号)
- 单个点号加入 `$PATH`变量，执行本目录命令就不用 `./`了。但这样还有其他问题：重启系统后即丢失 => 持久化,即放入 `profile.d/`下的shell中。这关系到下面讲的三种shell不同的启动方式
- 三种shell不同的启动方式
    + 1. 登陆时作为默认登录 shell
    + 2. 作为交互式 shell
    + 3. 作为运行脚本的非交互式 shell

- 1. 默认登陆shell
    + 五个不同的启动文件入口 
        * `/etc/profile` 主启动文件, 所有user都会读
        * 1, `~/.bash_profile`; `~/`即 `$HOME/`, 每个用户有自己的 `$HOME`
        * 2, `~/.bash_login`
        * 3, `~/.profile`(上面三个有顺序，且只会执行一个)
        * `~/.bashrc` (这个文件可储存 **个人用户永久性变量**)
    + `/etc/profile`，每个帐户登陆时会执行它，可顺此看代码。它主要迭代了所有 `/etc/profile.d/`下的 `.sh`文件
    +  `$HOME/`下的文件一般只用到其中一到两个，这些文件定义了一些环境变量，并在每次启动bash shell时生效

- 2. 交互式shell
    + 只运行 `~/.bashrc`文件。(这就是为何我在这里引入项目build脚本时，每开一个shell都会执行的原因)

- 3. 非交互式shell

## 数组变量

- 定义: `myArr=(one two three)`注意:所有的空格有无是严格的
- 输出所有：`echo ${myArr[*]}`(用 `*`可输出所有)
- `unset myArr[2]`后，数组其他key->value不会变


# Day 3

## scripts

- 命令替换: 命令赋值给变量。
    + 反引号包裹
    + `$()`包裹
    + 上两个命令是不一样的。当赋值给变量时，用反引号只将命令赋值，不执行命令。而 `$()`将执行结果赋值

- 命令替换会 **创建一个子shell**来运行命令。`./`会创建一个子shell，不加路径时不是子shell
- `<<` 叫 **内联输入重定向**,真正的输入其实来自用户，它只是用来标记结束的字符(串)

# Day 4

## Mathematics

- `expr`不建议使用,弃学
- `$[]`包裹。仅整数运算。(zsh提供了浮点数运算)
- `bc` 命令,'bash calculator',支持浮点运算(内置变量 `scale`)

## Exit Code

- 变量`$?` ，一个成功的退出应显示为0，否则是个正数值
- `exit` 可自定义状态码(0~255)

## Structured Commands

### if-then

- `if-then(-elif-then)(-else)-fi`: `if`后面必须是命令(不是0或非0)，按退出状态码(0表示成功)判断. if的命令后有分号 `;`，可以把 `then`放在同行

```shell
    if Condition
    then
        Commands
    elif Command2; then
        Commands
    else 
        Commands
    fi
```

- Condition: `test`和 `[ CONDITION ]`. 用于条件测试(数值、字符串、文件)，可和if一起使用，弥补if缺陷. (注意严格的空格)
    + `test $var1`,只要变量不为空，都通过（应该是这样）
    + 数值比较. `[ n1 -eq n2 ]`. `-eq`,`-ge`,`-gt`,`-le`,`-lt`,`-ne`
    + 字符串比较. `[ str1 = str2 ]`. `=`/ `==,`!=`,`<`,`>`; `-n str1`(是否长度非0),`-z`(是否长度为0)
        * 未定义的变量用字符串长度测试,输出为0
        * `>`, `<`在使用时大多情况下要加转义符，否则认为是重定向
    + 文件比较.
        * `-d` is directory?
        * `-e` is existent?
        * `-f` is a file?
        * `-r`, `-w`, `-x` is readable/writable/executable
        * `-s` is empty?
        * `-nt` is newer than
        * `-ot` is older than
- 复合条件. `&&` and `||`.`[ condition1 ] && [ condition2 ]`.
- `(( EXPRESSION ))`. 高级 **数学**表达式. 支持 `++`, `--`, `!`, `~`, `**`(幂), `<<`, `>>`, `&`, `|`, `&&`, `||`
- `[[ EXPRESSION ]]`. 高级 **字符串**表达式. 支持模式匹配 (有些shell可能不支持)

### Case

```shell
case $variable in 
    pattern1 | pattern2) 
        commands1;;     # can lay it in up one line
    pattern3) 
        commands2
        ;;
    *) commands3;;
esac
```

### For

- 重定向可以写在 done 后面，这样就不会输出在shell中了

```shell
for var in LIST         # there is 'var', not '$var'.
do                      # for var in list; do ...
    commands1           # there use '$var', not 'var'
done > output.log       # 重定向(非必需)可以写这里
```

- `LIST` can be:
    + `str1 str2 str3` elements seperate by SPACE by default, or use quotes for a sentence as one element
    + SPACE means 空格，制表符，换行符. 更改 `IFS`的值来更改这个符号.
        * IFS(Internal Field Separator), 内部字段分割符
        * `IFS=:`
        * `IFS=$'\n'`. 至于为什么用 `$`，自己用到的时候查吧
        * `IFS=$'\n':;`. 指定多种分隔符
    + `$list` use variables. `list=ele1 ele2 ele3`, `list=$list" ele4"`(字串拼接)
    + `$(cat $file)` 从命令中读. 以上说过`$()`是一种命令替换符
    + `/home/robin/test/*` 用通配符. 这时应该将 `commands1`中所有用到 `$var`的地方用引号括起来(避免空格)

```shell
IFS.OLD=$IFS  # store default value for restore
IFS=$'\n'     # set customized value
IFS=$IFS.OLD  # restore default value
```

- For as C lang `for (( i = 1; i <= 10; i++ ))`

### While

```shell
while Condition          # test or []
do 
    Commands
done
```

### until

- 只有在退出状态码为0时终止

```shell
until Condition          # test or []. 
do 
    Commands
done
```

### break and continue

- `break N`
- `continue N`

# Day 5

## User Input

- `$0`, shell脚本名
- `$$`, shell本身的PID
- `$1`~ `$9`, `${10}`... `${n}`, 参数
- `$#`, 参数个数
- `$*`和`$@`: 返回所有参数。但 `$*`把所有参数当作一个变量(可以有空格的变量)保存，而 `$@`当作一个字符串中独立的单词。在输出时没有分别，但用for时就有区别了.
- `basename` can remove path, only leave file name
- `shift N` 移动变量,单独用即可。N可省，默认为1,$1删除, $2->$1, $3->$2
- shell中-a后跟参数值就是用 `case`, `shift` 组合使用的
- `getopt`是一个处理选项和参数的工具. (一般你看到的-ab是-a,-b的组合，就是它做的)
- `getopts`扩展了 `getopt`
    + -a "test1 test2",使用时多了引号，是它做到的
    + 你用的 `-pabc123_`,-p能和值放在一起是它做到的
- `read`:
    + 读取用户输入
        * `read name`, 将输入信息存入 `name`中
        * `-p`, 读时自定义提示 `read -p 'Please enter your name: ' name`
        * 不指定以上的name变量时，它默认把输入放入 `$REPLY`中
        * `-t`,指定用户输入的时间(s)，超时返回一个非0状态码
        * 输入是实时的，所以用 case可以及时限定输入的长度(是y就退出)
        * `-s`隐藏用户输入的内容
    + 读取文件
        * 技巧： `cat test | while read line ...` (read自动读取下一行)

# Day 6

## 再探重定向

- 文件描述符: 0, 1, 2; 自定义的:3~8 
    + `0`, STDIN
    + `1`, STDOUT; `1>`, `1>>` 将STDOUT重定向
    + `2`, STDERR; `2>`, `2>>` 将STDERR重定向; 
    + `cat file 1> file1 2> file2`, 分别重定向
    + `&>`, `&>>`; 将STDOUT和STDERR都重定向到一个文件。(自动地shell认为错误信息优先级高)
    + `>&2`, 改变重定向, 可以将STDOUT导向STDERR.(shell默认将STDERR导向STDOUT)
- 永久重定向 `exec 1>out.log`; 在脚本中某位置设定了它后，之后的STDOUT会重定向到out.log (恢复:中间量)
- 输入重定向 `exec 0< file`; 可以在脚本中使用，把文件重定向到STDIN (之后可直接用read)
- 自定义重定向 `exec 3>>test.log` ( `>>`比 `>`好), `echo '...' >&3`
- 自定义读写文件描述符 `exec 3<> testfile`.即可读入，也可写入
- 手动关闭文件描述符 `exec 3>&-`
- `>/dev/null` 阻止输出
- `tee` T型管道, 一方输出到stdout, 一方输出到指定文件(默认覆盖)
- `tee -a` 输出追加

- 这个脚本可以将数据按格式填充到模板中

```shell
#!/bin/bash

outfile='members.sql'       # define output file
IFS=','                     # redefine seperator ('cause it's from .csv file)
while read lname fname address city state zip   # read each line into each variable
do 
    cat >> $outfile << EOF  # each line below as INPUT output to file, and end when EOF
    # the next lines till EOF will be as INPUT (STDIN),
    # so having spaces at front or not is different
INSERT INTO members (lname,fname,address,city,state,zip) VALUES ('$lname','$fname','$address','$city','$state','$zip')  # $lname is variable, will be replaced
EOF    # attention, this line must not have space at front. because ' EOF'!='EOF' 
done < ${1}                 # read from $1 parameter (file)
```

## 临时文件 `mktemp`

- 创建临时文件 `mktemp file.XX...X` (3+个X会自动生成)
- `-t` 在系统临时目录中创建，并返回全路径
- `-d` 创建临时目录

## 控制脚本

- Linux信号
    + 1, SIGHUP, 挂起进程
    + 2, SIGINT, 终止, CTRL+C
    + 3, SIGQUIT, 停止, 停止进程可保留在内存中，以待继续运行
    + 9, SIGKILL, 无条件终止
    + 15, SIGTERM, 尽可能终止
    + 17, SIGSTOP, 无条件停止但不终止
    + 18, SIGTSTP, 停止或暂停但不终止, CTRL+Z
    + 19, SIGCONT, 继续运行停止的进程
- `kill -9 PID`, `-9` 无条件终止
- `./shell.sh &`, `&` 后台运行脚本, (但此进程还是和当前终端会话连在一起, 终止会话->终止进程)
- `nohup ./shell.sh &` 阻断SIGHUP信号 (退出终端依旧运行, 输出内容重定向到nohup.out)
- `trap` 捕获信号 
- `jobs -l` 查看作业
- `bg`, `fg`, 重启作业 (backgroud or frontground)
- `nice`, `renice`, 调整优先级
- `at` 定时作业 `atq`, `atrm`
- `cron` 安排定时作业

## function

- 定义 

```shell
function NAME {
    COMMANDS
}
```
or 
```shell
name() {
    COMMANDS
}
```

- 使用 (先定义，后使用)

```shell
NAME # no need ()
```

- `return` 退出并指定退出状态码 (0~255) (必须是numberic型的)
- `local`声明的变量作用域是本函数 (果然实践才能发现很多奇怪的问题)
    + 不要随意用local, 我将自己shell中的变量改成local，出现了很奇怪的现象
    + 请在shell中试试 `files=$(ls)`和 `local files=$(ls)`.
        + `local files=$(ls)` 等效于`local files=file1 file2 file3`, 会分别定义 `files=file1`, `file2=''`, `file3=''`, 而 `files=$(ls)`会存成一个变量
- `return` 和 `exit`的异同：
    + 都是退出，且返回退出码
    + `return`仅仅退出函数，而 `exit`可能会退出shell

# Day 7

## Create Lib

- `source` or `.`, Use `source ./shell` or `. ./shell`. 
    + `source`与 `./shell.sh` 或 `sh shell.sh`的不同. 用 source是在当前shell中执行，而用另外两种方式其实是在子shell(bash)中进行的,子shell执行完退出后，方法、变量都不能被使用了
    + 但为什么 `source ~/.profile`等一些操作是系统级的呢？当前shell和再开一个shell，它们的关系是兄弟关系啊，肯定是系统对这些文件做了什么

# Day 8

- `select` 会在shell中自动生成menu
- 例: (You can chose the Number before the menus)
    + ```shell
    PS3='Enter the option:' # attention this
    select x in 'a' 'b' 'c' 'd'
    do 
        case $x in 
            'a')
                echo 'You chose A'; break;;
            'b')
                echo 'You chose B'; break;;
            'c') 
                echo 'You chose C'; break;;
            *) 
                clear
                echo 'You chose other';;
        esac
    done
    ```

- `sed`

# Last Day (funny shell)

## send msg

- `who` who is online; `who -T` check open 'mesg' or not;
- `whoami` who am I
- `mesg` check; `mesg y` open; `mesg n` close;
- `write user pts/1` open to send msg to 'user pts/1'

<hr>

bookFrom<鸟哥的Linux私房菜>

找到了可能是他的博客: [link](http://www.cnblogs.com/ningvsban/category/423741.html)

# Day 1

- `cal`: calendar
- `man`等命令中，用 `/STR`来搜索字符串(向下), `?`是向上, `n`next, `N`last
- `nl -w N FILE`可带补0的行号输出文件内容
- `find`比 `whereis/locate`搜文件慢，后者是从数据库中查的
- 还记得字符串的 **部分裁剪**吗？ `#`, `##`, `%`, `%%`(修改ozsh时用了)
    + `#`, `##` 是从左往右
    + `%`, `%%` 是从右往左
    + `#`, `%`, 最短匹配
    + `##`, `%%`, 最长匹配
    + `${VAR#*STR}` 剪掉最短匹配 `*STR`. (VAR是变量名)
    + `${VAR##*STR}` 剪掉最长匹配 `*STR`
    + `${VAR%STR*}` 剪掉最短匹配 `STR*`
    + `${VAR%%STR*}` 剪掉最长匹配 `STR*`
- 变量内容的 **部分替换** `/`, `//`
    + `/`, 仅一处 `${VAR/SEARCH/REPLACEMENT}`
    + `//`, 全部处 `${VAR//SEARCH/REPLACEMENT}`
- 变量的测试及赋值 `-`/`:-`, `+`/`:+`, `=`/`:=`, `?`/`:?`
    + `NEW_VAR=${OLD_VAR-NEW_VALUE}`和 `NEW_VAR=${OLD_VAR:-NEW_VALUE}`
        * 相当于 `NEW_VAR = OLD_VAR ? OLD_VAR : NEW_VALUE`
        * 但两者的判断不一样，前者是 **有没有定义**，后者为 **是不是空**, 区别的场景就是，有没有手动定义为空字符串
    + `+`/`:+`与 `-`相反
    + `=`/ `:=`把旧变量 `OLD_VAR`也变成 `NEW_VALUE`
    + `?`/ `:?` 为空时报出错误, 否则进行赋值
    + 总之，没有冒号时，只检测有没有define，有冒号才会认为空字符串是false

# Day 2

- `ls ./test || mkdir test` 利用的是返回的错误码 `$?`
- `cut -d CHAR -f NUMBER` **选取命令**和 `grep`功能相似. 这个很有用啊，你可以按每一行固定格式进行解析后显示
    + `NUMBER` 还可以是 `N1,N2,N3`, `N1-N3`, `N1-`, `-N3`
    + 另外还有一个参数 `-c`, 按字符进行cut. (-d: delimiter)
- `sort` 可进行排序 `-t`, `-k`连用，指定分隔符并选取第几列进行排序
- 字符转换命令
    + `tr`, translating, 单字符的替换/删除, 但支持正则 `[a-z]`/`[A-Z]`
    + `col`, 过滤控制字符
    + `join`, 可用mysql中的join的用法来思考其作用,或用excel对列的操作来思考, 可实现按某一字段，匹配两个文件的行，进行整合
        * `join file1 file2`
        * `join -j N file1 file2` (N为列号，默认为1)
        * `join -1 2 -2 3 file1 file2`, 按file1的第2列与file2的第3列进行匹配，并合并
        * `join file1 file2 | join - file3`, `|`和 `-`一起使用实现标准输入
    + `paste` 将两个文件按行以tab分隔联结
    + `expand` [tab]转空格

# Day 3

- `seq [FROM] [STEP] [LAST]`, 生成连续的数字, 这个功能和 for一起使用挺好的
- `sh [-nvx] script.sh`可用来查询语法
    + `-n`, 不执行script, 只查询语法问题
    + `-v`, 执行前先输出script内容
    + `-x`, 执行的过程(包含代码)全列出来
