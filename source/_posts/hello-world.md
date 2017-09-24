title: shell脚本编程
tags:
  - linux
  - shell
categories:
  - shell
author: chenm
date: 2017-09-23 16:18:00
---
## shell应用实例
```
    ex1.sh
    
    #!/bin/sh
    #This is to show what a example looks like.
    echo "My First Shell!"
    echo "This is current directory."
    /bin/pwd
    echo
    echo "This is files."
    /bin/ls 

    使用 sh ex1 运行脚本程序即可

    如果我们想使用shell脚本，指定计划任务，
    比如每周的周一到周五给管理员发一个信息
    （比如当前主机的信息，如内存的使用情况，在线人数，磁盘空间等）
    
    #!/bin/sh
    /bin/date +%F >> /test/shelldir/ex2.info
    echo "disk info:" >> /test/shelldir/ex2.info
    /bin/df -h >> /test/shelldir/ex2.info
    echo >> /test/shelldir/ex2.info
    echo "online users:" >> /test/shelldir/ex2.info
    /usr/bin/who | /bin/grep -v root >> /test/shelldir/ex2.info
    echo "memory info:" >> /test/shelldir/ex2.info
    /usr/bin/free -m >> /test/shelldir/ex2.info
    echo >> /test/shelldir/ex2.info
    #write root
    /usr/bin/write root < /test/shelldir/ex2.info && /bin/rm /test/shelldir/ex2.info
    #crontab -e
    #0 9 * * 1-5 /bin /sh /test/ex2.sh

```

<!-- more -->

## 自定义变量
```
    变量：是shell传递数据的一种方法，用来代表每个取值的符号名。
    Shell有两类变量：临时变量和永久变量
    临时变量是shell程序内部定义的，其使用范围仅限于定义它的程序，
    对其它程序不可见。包括：用户自定义变量、位置变量。
    永久变量是环境变量，其值不随shell脚本的执行结束而消失。
    
    变量的定义：
    用户定义的变量由字母或下划线开头，由字母。数字或下划线序列组成
    ，并且大小写字母意义不同。
    变量名长度没有限制，在使用变量时，要在变量名前加上前缀"$"，
    一般变量使用大小写字母表示，并且是英文字母开头，赋值号"="两边
    没有空格。NUM=5，STR= "A String"
    可以将一个命令的执行结果赋值给变量，但是需要使用命令替换符。``。
    注意单引号和双引号的区别，""号会吧里面的变量值进行输出，''是会吧内容原封不动输出，不会识别里面的变量。
    使用set命令查看所有变量。
    使用unset命令删除指定变量
    
    变量赋值单引号和双引号的区别
    [root@silencer ~]# name="chenm"
    [root@silencer ~]# echo $name
    chenm
    
    [root@silencer ~]# myname="my name is $name"
    [root@silencer ~]# echo mynewname
    mynewname
    
    [root@silencer ~]# mynewname='my name is $name'
    [root@silencer ~]# echo $mynewname
    my name is $name

    命令替换符
    
    `` 是反引号，esc键下的那个键。
    $(cmd)和`cmd`的作用是相同的,在执行一条命令时，会先将其中的 ``，或者是$() 中的语句当作命令执行一遍，再将结果加入到原命令中重新执行
    $( ) 并不是没有毙端的...
    反引号 基本上可用在全部的 unix shell 中使用，若写成 shell script ，其移植性比较高。
    而 $() 并不见的每一种 shell 都能使用.
    
    
    [root@silencer ~]# p1=`date`
    [root@silencer ~]# echo $p1
    Mon Sep 4 03:19:53 CST 2017
```

## 占位变量
```
    在shell里面还有两种特殊变量，一种是位置变量，
    还有种是特殊的变量，在我们写Shell的时候十分的常用。
    位置变量 ls -l file1 file2 file3... (n范围1-9)在代码中使用
    最多9个。

    #!/bin/sh
    DATE=`/bin/date +%Y%m%d`
    echo "TODAY IS $DATE"
    /bin/ls -l $1
    /bin/ls -l $2
    /bin/ls -l $3
    
    $1,$2,$3参数由执行时传递过来
    sh ex3.sh /etc /test /usr
```

## 特殊变量
```
    $* 这个程序所有参数
    $# 这个程序的参数个数
    $$ 这个程序的PID
    $! 执行上一个后台命令的PID
    $? 执行上一个命令的返回值
    $(0-9) 显示位置变量
    
    #!/bin/sh
    DATE=`/bin/date +%F`
    echo "today is $DATE"
    echo '$# :' $#
    echo '$* :' $*
    echo '$? :' $?
    echo '$$ :' $$
    echo '$0 :' $0
    
    
    [root@silencer shell]# sh ex4.sh /aa /bb /cc
    today is 2017-09-04
    $# : 3
    $* : /aa /bb /cc
    $? : 0
    $$ : 8776
    $0 : ex4.sh
```
    
## read，键盘录入  
```
    命令从键盘读入数据，赋值给变量
   
    #!/bin/sh
    read f s t
    echo "the first is $f"
    echo "the second is $s"
    echo "the third is $t"
  
  
    [root@silencer shell]# sh ex5.sh
    1 2 4
    the first is 1
    the second is 2
    the third is 4

    sh -x extt.sh 表示跟踪shell脚本执行到哪一步
    [root@silencer shell]# sh -x ex5.sh
    + read f s t
    5 10 21
    + echo 'the first is 5'
    the first is 5
    + echo 'the second is 10'
    the second is 10
    + echo 'the third is 21'
    the third is 21
    
    +表示执行到 read这一行
```
  
## shell运算
```
    expr命令，对整数进行运算。不能计算小数。
    注意点：1.expr的运算必须用空格隔开。
            2.\*表示转义字符 *乘法
            3.保持先算乘除后算加减，如果需要优先运算则需要加命令替换符。
            4.也可对变量进行运算操作。
    
    中间都有空格
    [root@silencer shell]# expr 10 + 5
    15
    
    乘法需转义
    [root@silencer shell]# expr 10 * 5
    expr: syntax error
    [root@silencer shell]# expr 10 \* 5
    50
    
    需要优先运算则需要加命令替换符
    [root@silencer shell]# expr (10 - 2)  \* 5
    -bash: syntax error near unexpected token `10'
    [root@silencer shell]# expr `expr 10 - 2`  \* 5
    40

    可以对变量进行运算
    [root@silencer shell]# NUM=30
    [root@silencer shell]# echo `expr $NUM + 8`
    38
    
    不支持小数
    [root@silencer shell]# expr 10 / 3
    3
    [root@silencer shell]# expr 10.2 / 3
    expr: non-numeric argument
```

## 测试命令
```    
    使用test命令可以对文件、字符串等进行测试，
    一般配合控制语句使用，不应该单独使用。如下：
    
    字符串测试
    test str1=str2  测试字符串是否相等
    test str1!=str2 测试字符串是否不相等
    test str1   测试字符串是否不为空
    test -n str1    测试字符串是否不为空
    test -z str1    测试字符串是否为空
    
    
    int测试
    test int1 -eq int2  测试整数是否相等 
    test int1 -ne int2  测试整数是否不相等
    test int1 -ge int2  测试int1是否>=int2
    test int1 -le int2  测试int1是否 <=int2
    test int1 -lt int2  测试int1是否<int2
    
    文件测试
    test -d file    指定文件是否目录
    test -f file    指定文件是否常规文件
    test -x file    指定文件是否可执行
    test -r file    指定文件是否可读
    test -w file    指定文件是否可写
    test -a file    指定文件是否存在
    test -s file    指定文件大小是否非0
```
    
## if语句
```
    语法格式: if test -d $1 then ... else...fi
    变量测试语句可用[]进行简化，如 test -d $1 等价于[ -d $1 ]
    
    #!/bin/sh
    # if test $1 then ... else ... fi
    if [ -d $1 ] 
    then 
    	echo "this is a directory!"
    else
    	echo "this is not a directory!"
    fi
    注意[ -d $1 ]中间都有空格
```

## if elseif语句
```
	if[ -d $1 ]
    then ..
    elif [-f $1 ]
    then ...
    else ...
    fi
    
    #!/bin/sh
    # if test  then ... elif test then ... else ... fi
    if [ -d $1 ]
    	then
    	echo "is a directory!"
    elif [ -f $1 ]
    	then
    	echo "is a file!"
    else 
    	echo "error!"
    fi 
```

## 逻辑的(and)与(or)
```
    &&   逻辑的 AND 的意思, -a 也是这个意思
    ||  逻辑的 OR 的意思， -o 也是这个意思
    
    #!/bin/sh
    # -a -o 
    if [ $1 -eq $2 -a $1 = 1 ]
    	then
    	echo "param1 == param2 and param1 = 1"
    elif [ $1 -ne $2 -o $1 = 2  ]
    	then
    	echo  "param1 != param2 or param1 = 2"
    else
     	echo "others"
    fi

```

## for循环（名字表循环）
```   
    for...done语句格式：
    for 变量 in 名字表
    do
        命令列表
    done    
    
    
    #!/bin/sh
    # for var in [params] do ... done
    for var in 1 2 3 4 5 6 7 8 9 10
    do 
    	echo "number is $var"
    done
	
```
    
## select循环

```
select 变量 in  列表
do
	命令列表
done

#!/bin/sh
# select var in [params] do ... done
select var in "java" "c++" "php" "linux" "python" "ruby" "c#" 
do 
	break
done
echo "you selected $var"

[root@silencer shell]# sh ex10.sh
1) java
2) c++
3) php
4) linux
5) python
6) ruby
7) c#
#? 1
you selected java

列出所有选项，输入选择第几项，执行命令
```

## case 语句
```
    case 变量 in
        字符串 1) 命令列表1;;
        ...
        字符串 n) 命令列表n;;
    esac
    
    #!/bin/sh
    read op
    case $op in
            a)
     	echo "you selected a";;
            b)
    	echo "you selected b";;
    	c)
    	echo "you selected c";;
    	*)
    	echo "error"
    esac
	
```
 
## while循环
```
    while 条件
    do
        命令
    done
    
    #!/bin/sh
    #while test do ... done
    
    num=1
    sum=0
    while [ $num -le 100 ]
    do
    	sum=`expr $sum + $num`
    	num=`expr $num + 1`
    done
    #sleep 5
    echo $sum
    
    sleep 5 等待5s
	
```
    
## break和continue test

```  
    在shell编程里也会出现break和continue，它的含义和我们java里一样。
    break 跳出循环
    continue 继续执行下一次循环
    
    #!/bin/sh
    i=0
    while [ $i -le 100 ]
    do
    	i=`expr $i + 1`
    	if [ $i -eq 5 -o $i -eq 10 ]
    		then continue;
    	else 
    		echo "this number is $i"
    	fi
    
    	if [ $i -eq 15 ]
    		then break;
            fi 
    done
```