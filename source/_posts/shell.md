---
title: bash
urlname: bash
top: false
categories:
  - 编程语言
tags:
  - shell
author: jesonlin
date: 2022-12-19 15:13:32
update: 2022-12-22 18:12:45
---

shell

<!-- more -->

## 引言-shell、shell脚本、shell环境
1. Shell 是指一种用 C 语言编写的应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。Shell 既是一种命令语言，又是一种程序设计语言。  
2. Shell 脚本（shell script），是一种为 shell 编写的脚本程序。  
3. Shell 编程跟 java、php 编程一样，只要有一个能编写代码的文本编辑器和一个能解释执行的脚本解释器就可以了。  
4. 在一般情况下，人们并不区分 `#!/bin/bash`和 `#!/bin/sh`。"#!"告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 程序。
***  

## bash、dash、sh
1. bash(GNU Bourne-Again Shell)：是许多Linux平台的内定Shell。  
2. dash(Debian Almquist Shell)：有人把bash从NetBSD移植到Linux并更名为dash。Dash Shell 比 Bash Shell 小的多。  
3. sh(Bourne-Again Shell)：本是bash的符号链接，但鉴于bash过于复杂，并建议将 /bin/sh 指向 dash。
4. 现在，Debian和Ubuntu中，/bin/sh默认已经指向dash，这是一个不同于bash的shell，它主要是为了执行脚本而出现，而不是交互，它速度更快，但功能相比bash要少很多，语法严格遵守POSIX标准。
5. 查看bash/dash/sh所在位置：`whereis sh`。  
6. 查看sh指向：`ll /bin/sh`。  
7. 切换sh指向：`sudo dpkg-reconfigure dash/bash`。   
***  

## 运行方式
1. bash x.sh  
    bash命令执行脚本，首行的#!/bin/sh是注释不起作用，新开子进程执行
2. ./x.sh  
    可执行文件执行脚本，先加权限 chmod u+rx，再按首行执行解释器执行，新开子进程执行
3. source ./x.sh == . ./x.sh  
    当前进程执行
***  

## shell 各种括号
1. ` $(command) ` 和 `` `command` ``  命令替换，后者更通用，前者不是所有shell都支持。  
2. ` ${var} ` 和 ` $var `  变量取值  
3. ` ${} ` 模式匹配  
    1. ` variable#pattern `  去掉左边最小匹配。  
    2. ` variable##pattern ` 去掉左边最大匹配。  
    3. ` variable%pattern `  去掉右边最小匹配。  
    4. ` variable%%pattern`  去掉右边最大匹配。  
    5. 只有在pattern中使用通配符` * `，最小最大匹配才有区别。  
    6. ` * ? [...] [!...]`  表示0个或多个，只有一个，匹配中括号里的字符，不匹配中括号里的字符。  
    7. ` ${0##*/} `  得到运行程序的名称，并只取"/"之后的部分。  
4. ` $[1+2] ((1+2)) `  数学运算，只能整数。浮点数当作字符串处理。
5. ` $[ conditon ]` 等价于 ` test `  必须左右括号旁边加空格。  
6.  ` (())  [[]] `  分别针对数学表达式和字符串表达式的加强版

## shell 变量  
1. shell变量： 弱类型变量，局部变量和环境变量  
2. 定义变量： 
    1. 显示定义： `NAME=xxx 或 NAME="xxx"`，等号两边不能有空格。  
    2. 隐式定义： `for file in $(ls /etc)`，将/etc目录下的文件名循环出来。
    3. let为变量赋值：`let a=10+20`, shell不擅长计算
    4. 命令结果赋值给变量：`c=$(ls -l /etc) 或 c=ls -l /etc`。
    5. 变量值有空格等特殊字符可以用单引号或者双引号。
3. 使用变量：
    1. 使用定义过的变量： `${NAME}`。  
    2. 只读变量： `readonly NAME`。  
    3. 删除变量： `unset NAME`，不能删除只读变量。
    4. 作用范围：仅当前进程，要实现父进程共享给子进程，需要export导出
    5. 系统环境变量：`env|more`
4. 字符串：  
    1. 单引号： `str='this is a string.'`。  
    2. 双引号： `str="my name is ${NAME}"`，双引号里可以有变量，可以出现转义字符。    
    3. 拼接字符串： `str="hello, ${NAME}"`。    
    4. 获取字符串长度： `${#str}`。  
    5. 提取字符串： `${str:0:4}`。  
    6. 查找子字符串： `` `expr index "${str}" io` ``。  查找字符i或o的位置，哪个先出现查找哪个。  
5. 数组：  
    1. bash只支持一维数组，从0开始，括号表示，元素空格隔开。  
    2. 定义数组： `num_name=(value0 value1 value2)` 或者 `num_name[0]=value0`。  
    3. 读取数组所有元素： ``${num_name[@]}``。  
    4. 获取数组元素个数： `${#num_name[@]}`。  
    5. 获取数组单个元素的长度： `${#num_name[n]}`。  
6. 注释：
    1. 单行注释： `#`。  
    2. 多行注释： `:<<EOF 注释的多行内容 EOF`。  

## shell 参数
1. 脚本内获取传递参数： 
    1. 脚本名： `$0`。  
    2. 第n个参数： `$n`。  
2. 其他处理参数的字符： 
    1. 传参个数： `$#`。  
    2. 以一个单字符串形式显示所有参数： `$*`，不包括第0个参数脚本名。  
    3. 脚本运行的当前进程ID号： `$$`。  

## shell 运算符
1. 算术运算符：原生bash不支持  
    1. 使用expr计算： `` val=`expr 1 + 2` ``。  运算符两边要有空格。要被反引号包含。  
    2. 符号： `+, -, \*, /, %`， 加减乘除取余。  
    3. 相等、不相等： `[ a == b ]` `[ a != b ]`。 中括号左右要有空格。下同。
2. 关系运算符： 只支持数字，或者字符串的值是数字  
    1. 是否相等： `[ $a -eq $b ]`  
    2. 是否不等： `[ $a -ne $b ]`  
    3. 是否左边大于右边： `[ $a -gt $b ]`  
    4. 是否左边小于右边： `[ $a -lt $b ]`  
    5. 是否左边大于等于右边： `[ $a -ge $b ]`  
    6. 是否左边小于等于右边： `[ $a -le $b ]`  
3. 布尔运算符：
    1. 非运算： `[ ! true ]`  
    2. 或运算： `[ $a -gt 100 -o $b -lt 100 ]`  
    3. 与运算： `[ $a -ge 100 -a $b -le 100 ]` 
4. 逻辑运算符
    1. 逻辑与： `[ $a -ge 100 && $b -le 100 ]`
    2. 逻辑或： `[ $a -ge 100 || $b -le 100 ]`
5. 字符串运算符：
    1. 字符串是否相等： `[ $a = $b ]`  
    2. 字符串是否不等： `[ $a != $b ]`  
    3. 字符串长度是否为0： `[ -z ${str} ]`  
    4. 字符串长度是否不为0： `[ -n ${str} ]`  
    5. 字符串长度是否为空： `[ $str ]` 
6. 文件测试运算符：
    0. 用于检查Unix文件的各种属性
    1. 检测文件是否存在： `[ -e file ]`  
    2. 检测文件是否为空： `[ -s file ]`  
    3. 检测文件是否是目录： `[ -d file ]`  
    4. 检测文件是否是普通文件(既不是目录也不是设备文件)： `[ -f file]`  
    5. 检测文件是否可读/可写/可执行： `[ -r/-w/-x file ]`  

## shell echo命令  
1. 用于字符串输出： `echo "hello,world"` 等价于 `echo hello,world`  
2. 显示换行： `echo -e "\n"` -e开启转义,即使没有`\n`也是默认换行
3. 显示不换行： `echo -e "OK! \c"`
3. 读取标准输入： `read Variable`  
4. 结果重定向： `echo "it is a test" > myfile`  
5. 显示命令结果： `` echo `date` `` 显示当前日期 

## shell printf命令
1. ` printf format-string [arguments...] `  默认不换行  
2. ` printf "hello, world!\n" `  
3. ` printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg `  左对齐宽度为10个字符` -10s `
4. ` printf "%-10s %-8s %-4.2f\n" 张三 男 66.66`  
5. ` printf "%s" abc def` 会输出` abcdef `,这是因为format-string会被重用  
6. ` printf "%s\n" abc def` 会输出` abc 换行 def`  

## shell test命令
1. ` test condition ` 等价于 ` [ condition ] ` 用于检查某个条件是否成立

## shell 流程控制
1.  if else-if else
    ```
    if condition1
    then
        command1
        command2
    elif condition2
    then
        command3    
    else
        command4  #不能为空
    fi
    ```  
    适用于终端写成一行` if [ $(ps -ef | grep -c "ssh") -gt 1 ]; then echo "true"; fi `  
2. for 循环
    ```
    for var in item1 ... itemN
    do
        command1
        ...
        commandN
    done
    ```  
    适用于终端写成一行` for var in item1 ... itemN; do command1; ... commandN; done; `  
3. while 循环
    ```
    while condition  #condition 为 ":" 时无限循环, 为 "read file"时读取输入，"ctrl+d"退出。  
    do
        command  #e.g. let "n++" 让n自增1
    done
    ```  
4. until 循环
    ```
    until condition  #condition为true时停止
    do 
        command
    done
    ```
5. case 语句
    ```
    case value1 in
    mode1)      #每个模式以")"结束
        command1
        ;;      #两个";;"表示break
    mode2)
        command2
        ;;
    *)
        command3
        ;;
    esac
    ```
6. break/continue 跳出循环
    1. break 跳出所有循环
    2. continue 跳出当前循环

## shell 函数  
必须先定义才能使用，返回0-255,0成功，其他数字失败。
1. 无参数函数
    ```
    funname() {
        echo "this is a function."
        return int
    }
    funname   #函数执行 函数返回结果通过"$?"获得
    ```
2. 带参数函数
    ```
    funname() {
        echo "第一个参数 ${1} "  #通过"${n}"在函数内部获取函数参数
        echo "第二个参数 ${2} "
        echo "参数总数 $# "
        echo "作为字符串输出所有参数 $* "
    }
    funname 1 2 3   #调用函数，输入1,2,3 输出
    ```
3. `$?` 仅对其上一条指令负责，一旦函数返回后其返回值没有立即通过该方式获取，则会被其他指令执行结果覆盖。
    ```
    fun() {
        return ` expr 1 + 1 `
    }
    fun         #调用函数
    echo $?     #打印2 调用函数返回的值，如果return数值就为该数，否则返回最后一条执行成功与否的状态。
    echo $?     #打印0 上一条echo执行成功与否
    ```
3. 如何获取函数内的字符串返回
    ```
    fun() {
        return "string"
    }
    fun
    echo $?     #返回255 提示说需要返回数字
    echo `fun`  #将函数当做命令执行，获取其输出
    echo $(fun) #同上
    ```

## 输入/输出重定向
1. 将输出重定向到文件 ` command > file `   
2. 将输出追加到文件 ` command >> file `  
3. 键输入重定向到文件 ` command < file `  
4. 将文件 描述符为n的文件重定向到file ` n > file `
5. 将输出文件m和n合并 ` n >& m` 
6. 将输入文件m和n合并 ` n <& m`
7. 将输入重定向文件1输出重定向到文件2 ` command < file1 > file2 `  
8. 将输出重定向到黑洞，即不显示 ` command > /dev/null ` 特殊文件，写入的内容都会被丢弃，起到禁止输出的作用。  
9. 0是标准输入，1是标准输出，2是标准错误输出。  
10. 将命令结果的输出追加到指定文件： `command >> /PATH_TO_FILE 2>&1` 

## shell 文件包含
1. 使用`.`来执行(需要可执行权限):  
    ` ./test.sh `
2. 使用`sh`或者`source`来执行:  
    ` sh test.sh `   
***

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
    cur_timestamp=`date -d -1days +%s`
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

## 常用操作
1. 脚本里生成配置文件
    ```
    #!/bin/sh

    cat > /root/a.sh << EOF
    #!/bin/sh

    echo "hello bash"
    EOF
    ```


## REFERENCE
[菜鸟教程](https://www.runoob.com/linux/linux-shell.html)
