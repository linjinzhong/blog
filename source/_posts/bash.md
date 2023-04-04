---
title: bash
urlname: bash
top: false
categories:
  - 编程语言
tags:
  - bash
author: jesonlin
date: 2022-12-19 15:13:32
update: 2022-12-22 18:12:45
---

Bash 是 Unix 系统和 Linux 系统的一种 Shell（命令行环境），是目前绝大多数 Linux 发行版的默认 Shell。

<!-- more -->


## shell含义和种类
1. Shell 这个单词的原意是“外壳”，跟 kernel（内核）相对应，比喻内核外面的一层，即用户跟内核交互的对话界面。
2. Shell 是指一种用 C 语言编写的应用程序，提供了一个界面，用户通过这个界面访问操作系统内核的服务。Shell 是一个命令解释器，解释用户输入的命令。
3. 用户可以用 Shell 命令写出各种小程序，又称为脚本（script）。这些脚本都通过 Shell 的解释执行，而不通过编译。
4. shell包含bash、sh、csh、tcsh、ksh、zsh、fish，其中bash是最常用的shell。


## 常用命令
1. `date`命令  
    ```
    # 获取当前格式化日期
    date_time=`date +%Y/%m/%d-%H:%M:%S`
    # 获取前一天日期
    early_date=`date -d -1days +%Y/%m/%d`
    # 获取后一天日期
    early_date=`date -d +1days +%Y/%m/%d`
    # 获取当前时间戳
    cur_timestamp=`date +%s`
    # 获取前1天此时的时间戳
    cur_timestamp=e
    # 获取后一天此时的时间戳
    cur_timestamp=`date -d +1days +%s`
    ```
2. 操作mysql数据库
    ```
    # sql语句
    sql="update ${mysql_table} set status=-1 where status=1;"
    # 执行sql语句
    op=`mysql --host="localhost" --user=root --password=密码 -v -v -e "${sql}"`
    # 参数-v -v 输出sql语句和结果
    # 参数-N输出结果不含列名，-s静默输出，每个结果一行
    ```

3. 脚本里生成配置文件
    ```
    #!/bin/sh

    cat > /root/a.sh << EOF
    #!/bin/sh

    echo "hello bash"
    EOF
    ```


## 实践1
```
#!/bin/bash
#
# 注释：说明当前脚本功能

# 设置脚本错误停止、变量不存在停止、打印命令、管道命令错误一个也停止
set -euxo pipefail

# 脚本文件名和参数
echo '脚本名称：$0=' $0
echo '参数数量：$#=' $#
echo '参数1：$1=' $1
echo '参数2：$2=' $2
echo '所有参数：$@=' $@

# 遍历打印参数
for i in "$@";
do
    echo $i
done

# 读取文件
filename='/etc/hosts'
while read myline
do
    echo "$myline"
done < $filename

# 函数定义
#######################################
# Cleanup files from the backup dir
# Globals:
#   BACKUP_DIR
#   ORACLE_SID
# Arguments:
#   None
# Returns:
#   None
#######################################
cleanup() {
  ...
}

# 退出值为0（成功）
exit 0
# 退出值为1（失败）

exit 1
```


## 实践2-删除数据库前N天创建的前缀为xxx的表
```
#!/bin/bash
#
# 注释：删除数据库N天前创建的前缀为table_prefix的表

set -euxo pipefail

N=7
table_prefix='tb_xxx_'

__str__="删除数据库$N天前创建的前缀为${table_prefix}的表"
echo -e "title: $__str__\n"

host=''
port=''
user=''
passwd=''
db=""
mysql_export="export MYSQL_PWD=${passwd}"
mysql_login="mysql -h$host -P$port -u$user -D$db"
echo -e "db: $mysql_login\n"

sql_select="select TABLE_NAME from INFORMATION_SCHEMA.TABLES where TABLE_SCHEMA=\"$db\" and TABLE_NAME like '${table_prefix}%' and DATEDIFF(now(), CREATE_TIME)>$N order by CREATE_TIME asc"

tables=`$mysql_export && $mysql_login -N -e "$sql_select"`
echo -e "res_sql_select: $tables\n"

if [ "$tables" = "" ]; then
    echo "NULL, exit"
    exit 0
fi

wait_del_tables=''
cnt=0
for table in $tables; do
    wait_del_tables="$wait_del_tables,$db.$table"
    ((cnt++))
done
wait_del_tables=${wait_del_tables:1}
echo -e "wait_del_tables: $wait_del_tables\n"

sql_drop="drop table $wait_del_tables"
res=`$mysql_export && $mysql_login -e "$sql_drop"`
echo -e "res_sql_drop: $res\n"

echo -e "Successfully drop $cnt tables\n"
```


## 实践3-进程监控和保活
```
#!/bin/bash
#
# 注释：进程监控和保活

set -euxo pipefail

# 参数检查
if [ $# -lt 2 ]
then
    echo "please input PROGRAM_DIR and PROGRAM_NAME"
    exit 0
fi

# 进入进程启动目录
cd ${PROGRAM_DIR}

# 检查和启动进程
RUN_CHECK() {
    # 检查进程是否存活
    RUN_CHECK=`ps -ef | grep ${PROGRAM_NAME} | grep -wv grep | grep -wv vi | grep -wv tail | grep -wv ${PROGRAM_CURRENT} | grep -v "\.sh" | grep -v "/alarm " | wc -l`    
    echo "check_result: ${RUN_CHECK}"

    # 进程不存在，则启动
    if [ ${RUN_CHECK} -lt 1 ]
    then
        NOW_TIME=`date +%Y/%m/%d-%H:%M:%S`
        echo "${NOW_TIME}, restart the program: $?"
        echo 'nohup hexo s &' | bash
        echo "Fatal: ${PROGRAM_NAME} down ${RUN_CHECK} ${NOW_TIME}" >> down.log
    fi
}

# 每5秒检查一次
STEP=5
for (( i=0; i<60; i=(i+${STEP}) )) do
    RUN_CHECK
    sleep ${STEP}
done
exit 0
```


## shell配置
1. 查看当前默认shell和已安装shell
    ```
    $ echo $SHELL
    $ cat /etc/shells
    ```
2. 查看bash版本
    ```
    $ bash --version
    $ echo $BASH_VERSION
    ```


## 基本语法
1. echo-原样输出参数
    ```
    # 原因输出包括换行格式
    $ echo "<HTML>
    <HEAD>
          <TITLE>Page Title</TITLE>
    </HEAD>
    <BODY>
          Page body.
    </BODY>
    </HTML>"

    # -n 去掉末尾默认的换行符
    $ echo -n a;echo b
    ab

    # -e 解释引号(单/双引号)参数里的特殊字符
    $ echo "hello\nworld"
    hello\nworld

    $ echo -e 'hello\nworld'
    hello
    world
    ```
2. 命令格式
    ```
    # 短形式
    $ ls -r

    # 长形式
    $ ls --reverse

    # 太长换行，末尾加'\'
    $ echo foo bar
    # 等同于
    $ echo foo \
    bar
    ```
3. 空格-区分不同参数
    ```
    $ <command> foo bar
    ```
4. 分号-命令结束符，后面命令不依赖前面命令执行成功与否
    ```
    $ clear; ls
    ```
5. 命令组合符号-&& 和 ||
    ```
    # 依赖前面命令执行成功才能执行后面命令
    $ <command_1> && <command_2>

    # 前面命令执行失败才执行后面命令
    $ <command_1> || <command_2>
    ```
6. type-显示命令类型
    ```
    $ type echo
    ```
7. 快捷键
    - `ctrl + L`：清除屏幕
    - `ctrl + C`：终止当前正在执行的命令


## 模式扩展
1. `~`：自动扩展成当前用户的主目录；
2. `?`：代表文件路径里面的任意单个字符，不包括空字符；
3. `*`：代表文件路径里面的任意数量的任意个字符，包括零个字符；
    ```
    # 显示所有隐藏文件
    $ echo .*

    # 显示隐藏文件排除.和..
    $ echo .[!.]*
    ```
4. `[]`：可以匹配方括号里的任意一个字符；
    ```
    # 文件名扩展
    $ echo a[bc]d
    # 匹配文件存在，显示如下
    abd acd
    # 匹配文件不存在，显示如下
    a[bc]d

    # !表示匹配除了b以外
    $ echo a[!b]d
    ``` 
5. `[start-end]`：连续范围扩展，
    ```
    [a-c] 等同于[abc]
    [0-9] 等同于[0123456789]
    [!0-9] 表示除0-9外的任意字符
    ```
6. `{}`: 表示分别扩展大括号里的所有值，使用逗号分割，逗号前后不能有空格
    ```
    # 非文件名扩展，总是扩展，不管文件是否存在
    $ echo d{a,b,c,d}g
    dag dbg dcg ddg

    # 与其他组合，优先于其他
    $ echo /bin/{cat,b*}
    /bin/cat /bin/bx /bin/bxx ...
    ```
7. `{start..end}`：扩展成一个连续序列
    ```
    # 顺序扩展
    $ echo d{a..c}g
    dag dbg dcg ddg

    # 逆序扩展
    $ echo d{c..a}g
    dcg dbg dag

    # 创建系列目录
    $ mkdir fiel_{a,b,c}.txt

    # for循环
    for i in {1..4}
    do
        echo $i
    done

    # 整数前导0
    $ echo {01..3}
    01 02 03

    # 间隔步长
    $ echo {1..10..2}
    1 3 5 7 9
    $ echo {a..g..2}
    a c e g
    ```
8. `${}`：变量扩展
    ```
    $ echo ${SHELL}
    /bin/bash

    <!-- 所有以S开头的变量名 -->
    $ echo  ${!S*}
    ```
9. `$()`：子命令扩展
    ```
    $ echo $(date)
    <!-- 等同于 -->
    $ echo `date`

    <!-- 可嵌套 -->
    $ echo $(ls $(pwd))
    ```
10. `$(())`：算术扩展，扩展成整数运算结果
    ```
    $ echo $((2 + 2))
    4
    ```
11. `[[:class:]]`：字符类，文件名扩展，class扩展成该类之中任意一个
    1. `[[:alnum:]]`：匹配任意英文字母与数字
    2. `[[:alpha:]]`：匹配任意英文字母
    3. `[[:digit:]]`：匹配任意数字0-9
    4. `[[:lower:]]`：匹配任意小写字母a-z
    5. `[[:upper:]]`：匹配任意大写字母A-Z
    6. `[[:blank:]]`：匹配空格和Tab健
    7. `[[:cntrl:]]`：匹配ASCII 码 0-31 的不可打印字符
    9. `[[:print:]]`：匹配ASCII 码 32-127 的可打印字符。
    10. `[[:xdigit:]]`：匹配16进制字符（A-F、a-f、0-9）
    ```
    # 打印数字开头的文件名
    $ echo [[:digit:]]*
    # 打印非数字开头的文件名
    $ echo [![:digit:]]*
    ```
12. 注意项
    1. 通配符是先解释再执行
    2. 文件名扩展在不匹配时，会解释成原样输出，再执行
    3. 只适用于单层路径，即不能匹配路径分隔符（`/`）。如果要匹配子目录里的文件，可以使用`ls */*.txt`
13. 量词语法，也是文件名扩展
    1. `?()`：模式匹配零次或1次
    2. `*()`：模式匹配0次或多次
    3. `+()`：模式匹配一次或多次
    4. `@()`：模式匹配仅一次
    5. `!()`：模式匹配给定模式之外的任何内容
    ```
    # 匹配文件有且只有一个.txt或.php后缀
    $ ls abc@(.txt|.php)
    ```


## 引号和转义-bash只有字符串一种数据类型
1. 转义-转义特殊字符
    ```
    # 转义打印特殊字符比如 $、 &、 *、\
    $ echo \$date
    $date
    $ echo \\
    \

    # 转义打印不可打印字符，需要echo -e ''
    \a: 响铃
    \b: 退格
    \t: 制表符
    \r: 回车
    \n: 换行

    # 换行符加上\转换为普通空字符
    $ mv \
    /root/a/b \
    /root/b/b
    # 等同于
    $ mv /root/a/b /roott/b/b
    ```
2. 单引号-用于保留字符的字面含义
    1. 单引号使得bash扩展、变量引用、算术运算、子命令失效。
    2. 反斜杠转义功能在单引号内失效，故在单引号内使用单引号，不能使用转义
        ```
        $ echo $'it\'s'
        it's

        # 等同于
        $ echo "it's"
        ```
3. 双引号-除了`&`、`` ` ``、`\`保留特殊含义
    ```
    # 换行符失效
    $ echo "hello 
    world"
    hello
    world

    # 文件名空格情况
    $ ls "one file.txt"

    # 保留原始命令结果输出
    $ echo $(cal)
    March 2023 Su Mo Tu We Th Fr Sa 1 2 3 4 5 ... 31
    $ echo "$(cal)"
         March 2023     
    Su Mo Tu We Th Fr Sa
              1  2  3  4
     5  6  7  8  9 10 11
    12 13 14 15 16 17 18
    19 20 21 22 23 24 25
    26 27 28 29 30 31
    ```
4. here 文档
    ```
    << token
    text
    text
    token

    # 可发生变量替换，支持反斜杠转义，不支持通配符和引号
    $ foo='hello world'
    $ cat << _example_
    $foo
    "$foo"
    '$foo'
    _example_
    hello world
    "hello world"
    'hello world'

    # 如果不希望变量替换，则将here文档的开始标记放在单引号之中

    # here 字符串，将字符串通过标准输入，传递给命令
    $ md5sum <<< 'ddd'
    # 等同于
    $ echo 'ddd'|md5sum
    ```


## 变量-环境变量和自定义变量
1. 显示所有变量
    ```
    set
    ```
2. 创建/赋值变量-只有字符串，无类型概念
    ```
    myvar='hello world'
    myvar=hello
    myvar2=myvar
    ```
3. 删除变量
    ```
    unset myvar
    # 因为不存在的变量一律等于空字符串
    myvar=''
    ```
4. 输出变量
    ```
    export NAME=value
    ```
5. 特殊变量
    1. `$?`：为上一个命令退出码，成功为0，失败为其他
    2. `$$`：当前shell的进程ID
    3. `$@`：脚本的参数值
    4. `$#`：脚本的参数数量
6. 变量默认值
    ```
    # 变量存在且不为空，返回其值，否则返回默认值xxx
    ${varname:-xxx}

    # 变量存在且不为空，返回其值，否则设置并返回默认值xxx
    ${varname:=xxx}

    # 变量存在且不为空，返回其值，否则打印xxx并中断脚本
    ${varname:?xxx}

    # 变量存在且不为空，返回xxx，否则返回空
    ${varname:+xxx}

    # 上述中varname可以用数字1到9表示脚本参数
    filename=${1:?"filename missing."}
    ```
7. readonly只读命令
    ```
    readonly foo=1
    ```
8. let命令
    ```
    # 直接执行算术表达式
    let foo=1+2

    # 同时多个
    $ let "v1 = 1" "v2 = v1++"
    $ echo $v1,$v2
    2,1
    ```

## 字符串操作
1. 获取字符串长度
    ```
    ${#varname}
    ```
2. 子串-${varname:offset:length}
    ```
    # 从头部位置offset（>=0开始），截取长度为length的子串
    ${varname:offset:length}
    
    # 从头部位置offset（>=0开始），截取到尾部的子串
    ${varname:offset}

    # 从尾部未知offset（<=-1开始），负数前面必须有个空格
    foo="abcd efg hijkm"
    ${varname: -5}
    hijkm
    # length为正数表示长度
    ${varname: -5:2}
    # length为负数排除尾部负数个字符
    ${varname: -5:-2}
    hij
    ```
3. 搜索和替换-#/%
    1. 头部尾部匹配替换
        ```
        # 头部/尾部匹配删除，pattern可以使用*?[]等通配符
        # 删除最短头部匹配，返回剩余
        ${variable#pattern}
        # 删除最长头部匹配，返回剩余
        ${variable##pattern}
        # 删除最短尾部匹配，返回剩余
        ${variable%pattern}
        # 删除最长尾部匹配，返回剩余
        ${variable%%pattern}    

        # e.g.删除文件路径，只返回文件名
        $ path=/home/cam/book/long.file.name
        ${path##*/}
        long.file.name
        # e.g.删除文件名，只返回文件路径
        ${path%/*}
        /home/cam/book

        # 头部匹配替换，只有#，最长匹配替换
        ${variable/#pattern/string}
        # e.g.
        $ foo=XXX.jpg
        $ echo ${foo/#XXX/xxx}
        xxx.jpg
        # 尾部匹配替换，只有%，最长匹配替换
        ${variable/%pattern/string}
        # e.g.
        $ foo=XXX.jpg
        $ echo ${foo/%jpg/JPG}
        XXX.JPG

        # e.g.
        $ path=/home/cam/book/long.file.name
        $ echo ${path%.*}
        /home/cam/book/long.file
        $ echo ${path%%.*}
        /home/cam/book/long
        ```
    2. 任意位置的模式匹配
        ```
        # 任意位置最长匹配，仅替换第一个
        ${variable/pattern/string}
        # 任意位置最长匹配，替换所有
        ${variable//pattern/string}
        # 省略string表示替换为空字符串即删除

        # e.g.
        $ path=/home/cam/foo/foo.name
        $ echo ${path/foo/bar}
        /home/cam/bar/foo.name
        $ echo ${path//foo/bar}
        /home/cam/bar/bar.name

        # e.g.将所有:改为换行符
        # 使用-e表示将替换后的\n字符，解释为换行
        $ echo -e ${PATH//:/'\n'}

        # e.g.通配符替换
        $ phone="555-456-1414"
        $ echo ${phone/5?4/-}
        55-56-1414
        ```
4. 改变大小写
    ```
    # 转为大写
    ${variable^^}

    # 转为小写
    ${variable,,}
    ```


## 算术表达式
1. 算术运算-只能整数
    ```
    # 自动忽略空格，不返回值，结果不为0则$?为0成功，否则为1失败
    ((...))

    # 返回值
    $ echo $((2 + 2))
    4

    # 可以嵌套
    $ echo $(((2**3) * 2))
    16
    $ echo $((2**(3 * 2)))
    64
    $ echo $((2**$((3 * 2))))
    64

    # 内部字符串被当做变量名，可不加$
    number=2
    $ echo $((number + 1))

    # 内部变量不存在或变量值为字符串，默认当做0做运算
    ```
2. 支持的符号
    - `+`：加法
    - `-`：减法
    - `*`：乘法
    - `/`：除法（整除）
    - `%`：余数
    - `**`：指数
    - `++`：自增运算（变量前缀或后缀）
    - `--`：自减运算（变量前缀或后缀）
3. 数值进制
    - `number`：没有任何特殊表示法的数字是十进制数（以10为底）。
    - `0number`：八进制数。
    - `0xnumber`：十六进制数。
    - `base#number`：base进制的数。
    ```
    $ echo $((0xff))
    255
    $ echo $((2#11111111))
    255
    ```
4. 位运算
    - `<<`：位左移运算，把一个数字的所有位向左移动指定的位。
    - `>>`：位右移运算，把一个数字的所有位向右移动指定的位。
    - `&`：位的“与”运算，对两个数字的所有位执行一个AND操作。
    - `|`：位的“或”运算，对两个数字的所有位执行一个OR操作。
    - `~`：位的“否”运算，对一个数字的所有位取反。
    - `^`：位的异或运算（exclusive or），对两个数字的所有位执行一个异或操作。
5. 逻辑运算-逻辑表达式为真，返回1，否则返回0。
    - `<`：小于
    - `>`：大于
    - `<=`：小于或相等
    - `>=`：大于或相等
    - `==`：相等
    - `!=`：不相等
    - `&&`：逻辑与
    - `||`：逻辑或
    - `!`：逻辑否
    expr1?expr2:expr3`：三元条件运算符。若表达式expr1的计算结果为非零值（算术真）- `，则执行表达式expr2，否则执行表达式expr3。    
6. 赋值运算
    ```
    $ echo $((a=1))
    1
    还有+=、-=、*=、<<=、|=等
    ```
7. 求值运算-`,`在`$((...))`执行前后连个表达式，并返回后一个表达式的值
    ```
    $ echo $((foo = 1 + 2, 3 * 4))
    12
    $ echo $foo
    3
    ```
8. expr
    ```
    $ foo=3
    $ expr $foo + 2
    5
    ```
9. let命令
    ```
    # 命令用于将算术运算的结果，赋予一个变量。不能有空格
    $ let x=2+3
    $ echo $x
    5
    ```


## bash脚本
0. 运行方式
    ```
    # 首行的#!/bin/sh是注释不起作用，新开子进程执行
    bash x.sh

    # 可执行文件执行脚本，先加权限，再按首行执行解释器执行，新开子进程执行
    chmod u+rx
    ./x.sh`  
        
    # 当前进程执行，主要为了脚本内部加载外部库
    source ./x.sh == . ./x.sh
    ```
1. 读取输入/文件-read
    ```
    # 读取输入
    echo -n "输入一些文本 > "
    read text
    echo "你的输入：$text"

    # 超时3秒和提示
    read -t 3 -p "Enter one or more values > "
    # 没有指定变量返回到环境变量REPLY
    echo "REPLY = '$REPLY'"

    # 读取文件
    filename='/etc/hosts'
    while read myline
    do
        echo "$myline"
    done < $filename
    ```
2. 条件判断
    ```
    # 基本
    if test $1 = 'root'
    then
        echo 'hello root'
    elif [ $1 = 'jesonlin' ]; then
        echo 'hello jesonlin'
    else
        echo 'you are not root'
    fi

    # true/false 特殊命令
    if true
    then
        echo 'always true'
    fi

    # 条件三种样式
    test -e /tmp/file.txt
    [ -e /tmp/file.txt ]
    [[ -e /tmp/file.txt ]]  # 第三种还支持正则

    # 条件是一个命令，可以是test，也可以是其他命令，命令返回值为0表示判断成立
    # 文件是否存在
    [ -a file ]
    [ -e file ]
    # 文件是否是目录
    [ -d file ]

    # 字符串判断，变量放在双引号之中
    [ $str ]  # 是否不为空
    [ -n $str ]  # 长度是否大于0
    [ -z $str ]  # 长度是否为0
    [ "$str1" == "$str2" ]  # 是否相等
    [ "$str1" != "$str2" ]  # 是否不相等
    [ "$str1" '>' "$str2" ]  # 字符串1是否大于2

    # 整数判断
    [ $num1 -eq $num2 ]  # 是否相等
    [ $num1 -ne $num2 ]  # 是否不相等
    [ $num1 -le $num2 ]  # 是否小于或等于
    [ $num1 -lt $num2 ]  # 是否小于
    [ $num1 -ge $num2 ]  # 是否大于或等于
    [ $num1 -gt $num2 ]  # 是否大于

    # 正则判断是否是整数
    if [[ "$0" =~ ^-?[0-9]+=$ ]]
    then
        echo '$0 is an integer'
    else
        echo 's0 is not an integer'
    fi

    # 逻辑运算
    &&(与) ||(或) !(非)

    # case结构
    case "$OS" in
      FreeBSD) echo "This is FreeBSD" ;;
      Darwin) echo "This is Mac OSX" ;;
      AIX) echo "This is AIX" ;;
      Minix) echo "This is Minix" ;;
      Linux) echo "This is Linux" ;;
      *) echo "Failed to identify this OS" ;;
    esac
    ```
3. 循环
    ```
    # while 循环
    number=0
    while [ "$number" -lt 10 ]; do
      echo "Number = $number"
      number=$((number + 1))
    done

    # until 循环-不符合条件（判断条件失败）就不断循环
    until condition; do
      commands
    done

    # for...in 循环
    # 列表由通配符产生，遍历当前目录下所有png图片
    for i in *.png; do
      ls -l $i
    done

    # for 循环，类似C语言，变量不必加$
    for (( i=0; i<5; i=i+1 )); do
      echo $i
    done

    # break, continue, 同C语言
    ```
4. 函数
    ```
    # 第一种
    fn() {
      # codes
    }

    # 第二种
    function fn() {
      # codes
    }

    # 参数变量
    # 函数的参数变量，与脚本参数变量是一致的
    $1~$9：函数的第一个到第9个的参数。
    $0：函数所在的脚本名。
    $#：函数的参数总数。
    $@：函数的全部参数，参数之间使用空格分隔。
    $*：函数的全部参数，参数之间使用变量$IFS值的第一个字符分隔，

    # Bash 函数体内直接声明的变量，属于全局变量，整个脚本都可以读取。
    # 函数体内不仅可以声明全局变量，还可以修改全局变量。
    # 函数里面可以用local命令声明局部变量，只在函数体内有效，函数体外没有定义。
    ```
5. 数组
    ```
    # 定义数组
    ARRAY[INDEX]=value
    ARRAY=(value1 value2 value3)
    ARRAY=([2]value1 [0]value2 [1]value3)  # 指定每个元素位置
    $ mp3s=( *.mp3 )  # 通配符定位数组
    $ read -a dice  # 用户命令行输入传入数组

    # 读取数组
    $ echo ${array[i]}
    $ foo=(a b c d e f)
    $ echo ${foo[@]}
    a b c d e f
    for i in "${names[@]}"; do
      echo $i
    done

    # 数组长度
    ${#array[*]}
    ${#array[@]}

    # 数组序号
    $ arr=([5]=a [9]=b [23]=c)
    $ echo ${!arr[@]}
    5 9 23
    $ echo ${!arr[*]}
    5 9 23

    # 提取数组1号开始的3个成员
    $ echo ${arr[@]:1:3}
    # 提取数组所有个成员
    $ echo ${arr[@]:0}

    # 追加数组成员，不能有空格
    $ arr+=(d e f)

    # 删除数组某个成员，只是隐藏，而不是删除
    $ unset arr[0]
    # 清空数组
    $ use tarr

    # 关联数组，类似map
    $ declare -A colors  # 必须带-A选项
    $ colors["red"]="#ff0000"
    $ colors["green"]="#00ff00"
    $ colors["blue"]="#0000ff"
    # 索引
    $ echo ${colors["blue"]}
    ```
6. 脚本命令
    0. bash 执行脚本会创建一个子shell去执行，默认忽略不存在的变量
    1. set 命令-修改子shell环境的运行参数，即定制环境
        ```
        #!/bin/bash 

        # 不存在变量就会报错
        set -u

        # 执行命令前打印命令本身，行首以+表示
        set -x  # 命令打印
        set +x  # 取消命令打印

        # 脚本有错误就停止
        set -e  # 开启
        set +e  # 关闭-e选项
        command || { echo "command failed"; exit 1; }
        # 管道命令以最后一个命令成功与否代表整个命令的返回值
        # 管道命令中只要一个子命令失败就终止脚本
        set -eo pipefail

        # 不运行，只检查语法
        set -n

        # 总结-写法一
        set -euxo pipefail
        ```
7. 常见错误
    1. 命令失败情况
        ```
        # 如果$dir_name不存在，会失败，会切换进用户主目录，执行接下来命令
        cd $dir_name
        rm *
        # 正确写法
        [[ -d $dir_name ]] && cd $dir_name && rm *
        # 不放心删除东西可以先打印看看
        [[ -d $dir_name ]] && cd $dir_name && echo rm *
        ```
    2. 定位问题
        ```
        # 打印行号
        echo "This is line $LINENO"

        # 打印函数堆栈上的函数名
        function func1()
        {
          echo "func1: FUNCNAME0 is ${FUNCNAME[0]}"
          echo "func1: FUNCNAME1 is ${FUNCNAME[1]}"
          echo "func1: FUNCNAME2 is ${FUNCNAME[2]}"
          func2
        }

        # 脚本调用堆栈
        function func1()
        {
          echo "func1: BASH_SOURCE0 is ${BASH_SOURCE[0]}"
          echo "func1: BASH_SOURCE1 is ${BASH_SOURCE[1]}"
          echo "func1: BASH_SOURCE2 is ${BASH_SOURCE[2]}"
          func2
        }
        ```


## REFERENCE
[菜鸟教程](https://www.runoob.com/linux/linux-shell.html)
[Bash 脚本教程](https://wangdoc.com/bash/)