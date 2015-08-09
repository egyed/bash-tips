# Shell基本功

## 基础

1. shell终端提示符，$表示的是普通用户，#表示的root用户。
2. 启动shell时候运行的脚本是~/.bash\_rc，登录shell的时候运行的是~/.bash\_profile，另外~/.bash\_history用于保存用户运行过的命令。
3. shell中双引号与单引号的区别：双引号中可以进行变量替换，但是单引号中不可以。
4. printf命令同C语言中的printf命令用法一致，使用空格来分割参数，printf不像echo那样会自动添加换行符，需要自己添加\n

	```
	 printf "%-5s %-10s %-4s\n" No Name Mark
	```
5. echo会自动追加换行符，可以使用选项-n来忽略结尾的换行符。另外，默认情况下，echo命令是不接受任何转义序列的，比如：

    ```
    echo "1\t2\t3"
    1\t2\t3
    ```
如果要输出转义序列，那么需要使用-e参数:

	```
	echo -e "1\t2\t3"
	1	2	3
	```

6. bash中每一个变量的值都是按字符串去存储的，无论给变量赋值的时候有没有使用引号。
7. 可以通过env命令来查看当前系统的环境变量。查看某个进程在运行时的环境变量:

	```
	cat /proc/$PID/environ | tr '\0' '\n'
	```
因为直接通过proc得到的变量列表是通过\0来分割的，所以使用tr进行替换，方便查看。

8. 环境变量是指未在当前进程中定义，而从父进程中继承而来的变量。比如环境变量HTTP_PROXY，它定义了连接需要使用哪个代理服务器。该环境变量通常被设置成：

	```
	HTTP_PROXY=192.168.1.23:3182
	export HTTP_PROXY
	```
export用来设置环境变量，从此之后，从当前shell脚本继承的任何程序都会继承这个变量。

9. 拼接PATH:

	```
	export PATH="$PATH:/home/user/bin"
	```
	使用:进行分割。不能使用单引号，单引号内变量不能展开。

10. 获取变量值的长度:

	```
	length=${#var}
	```

11. ${parameter:+expression} 如果parameter有值切不为空，则使用expression的值。

	```
	a=${b:+hehe}
	echo $a
	```
	输出a的值为空
	
	```
	b="nini"
	a=${b:+hehe}
	echo $a
	```
	
	输出a的值为hehe
	
12. 可以使用let命令执行简单的数学计算：

	```
	no1=4;no2=5;
	let result=no1+no2
	let no1++
	let no1--
	let no+=6
	let no-=6
	```
13. 操作符[]的使用方法和let命令相似：

	```
	result=$[ no1 + no2 ]
	result=$[ $no1 + 5 ]
	```
	
	[]中的变量可以使用$前缀也可以不使用。

14. 也可以使用(())，但是在使用(())的时候，变量名前必须加$

	```
	result=$(( no1 + 50 ))
	```
15. 使用expr命令也可以进行算术操作

	```
	result=`expr 3 + 4`
	```
	
16. 文件描述符

	```
	stdin:0
	stdout:1
	stderr:2
	```
17. 当一个命令执行完毕之后，可以通过echo $?来查看执行结果，如果结果是0，那么说明执行成功，如果是非0，那么说明发生了错误并退出。

18. 错误重定向与标准重定向到不同的文件

	```
	cmd 2>strerr.txt 1>stdout.txt
	```
	错误重定向和标准重定向到不同文件
	
	```
	cmd 2>&1 output.txt
	cmd &> output.txt
	```
	如果不希望输出内容，那么可以将输出重定向到文件/dev/null
	
	```
	cmd 2>/dev/null
	```
	
19. 使用tee命令，既可以将输出重定向到文件，又可以将输出重定向到终端

	```
	cat a*|tee out.txt
	```
默认的情况下tee命令会将文件进行覆盖，如果只是想追加，那么可以使用-a参数，例如:

	```
	cat a*|tee -a out.txt
	```

20. 可以使用stdin作为命令参数，只需要将-作为参数即可，例如:

	```
	echo who is this|tee -
	who is this
	who is this
	```
	
21. 自定义文件描述符

	```
	exec 3<input.txt
	cat <&3
	```
	使用文件描述符3打开并读取文件，然后就可以在其他命令中使用文件描述符3了。
	
22. 定义数组方法一:
	
	```
	array_var=(1 2 3 4 5 6)
	```

23. 定义数组方法二:

	```
	array_var[0]="test1"
	array_var[1]="test2"
	array_var[2]="test3"
	```
24. 获取数组中某个元素的值

	```
	echo ${array_var[0]}
	echo ${array_var[$index]}
	```

25. 输出数组中所有的值

	```
	echo ${array_var[*]}
	echo ${array_var[@]}
	```
26. 输出数组的长度

	```
	echo ${#array_var[*]}
	```
27. 关联数组

	```
	declare -A fruits_value
	fruits_value=([apple]='100 dollars', [orange]='150 dollars')
	```
	
	获取元素：
	
	```
	echo ${fruits_value[apple]}
	```
	
	获取key列表
	
	```
	echo ${!ass_array[*]}
	echo ${!ass_array[@]}
	```
	
	获取value列表
	
	```
	echo ${ass_array[*]}
	echo ${ass_array[@]}
	```
28. 使用unalias命令取消别名，直接运行alias可以显示当前所有alias的命令列表，作为安全监测的一项措施。

29. 在alias中可以使用$@来代替命令参数

	```
	alias rm='cp $@ ~/backup && rm $@'
	```
30. 时间命令
	
	查看当前时间:
	
	```
	date
	2015年 07月 05日 星期日 19:17:00 CST
	```	

	查看当前时间戳:
	
	```
	date +%s
	1436095039
	```
	
	将给定的时间转换为时间戳
	
	```
	date --date "2015-01-01 10:00" +%s
	1420077600
	```
	
	按格式显时间戳
	
	```
	date --date "2015-01-01 10:00" "+%Y-%m-%d %H:%M:%S"
	2015-01-01 10:00:00
	```
	
	常用时间格式
	
	<table>
		<tr>
			<th>日期内容</th>
			<th>格式</th>
		</tr>
		<tr>
			<td>星期</td>
			<td>
			%a为缩写，比如Sat
			%A为全拼，比如Satureday
			</td>
		</tr>
		<tr>
			<td>月份</td>
			<td>%m</td>
		</tr>
		<tr>
			<td>日期</td>
			<td>%d</td>
		</tr>
		<tr>
			<td>固定格式日期</td>
			<td>%D</td>
		</tr>
		<tr>
			<td>年</td>
			<td>%y</td>
		</tr>
		<tr>
			<td>小时</td>
			<td>%H</td>
		</tr>
		<tr>
			<td>分钟</td>
			<td>%M</td>
		</tr>
		<tr>
			<td>秒</td>
			<td>%S</td>
		</tr>
		<tr>
			<td>Unix纪元</td>
			<td>%s</td>
		</tr>
	</table>

31. 函数的定义

	```
	function fname()
	{
	  statements;
	}
	```
	
	也可以省略掉function，例如:
	
	```
	fname()
	{
	  statements;
	}
	```
	
32. 函数的参数

	```
	fname arg1 arg2;
	```
	
	在函数中可以直接访问参数
	
	```
	$1 第一个参数
	$2 第二个参数
	$n 第N个参数
	$@ 参数扩展"$1" "$2" "$3"
	$* 同样也是参数扩展 $1c$2c$3 其中c是IFS第一个字符
	```
	函数也可以像环境变量一样使用export导出,这样函数的作用域就可以扩展到子进程当中，例如:
	
	```
	export -f fname
	```
	
33. 将命令输出读入变量：

	可以使用基于子shell的方式:
	
	```
	cmd_output=$(COMMANDS)
	```
	
	也可以使用反引用的方式:
	
	```
	cmd_output=`COMMANDS`
	```
	
34. 子shell：子shell是一个独立的进程，可以使用()操作符来定义一个子shell，例如:
	```
	pwd;
	(cd /bin;ls);
	pwd;
	```
	以上命令pwd的输出不变，当命令在子shell中执行时，不会对当前的shell有任何影响。

35. 使用read命令将键盘输入读入到变量当中

	读入输入的前10个字符:
	
	```
	read -n 10 test
	echo -e "$test"
	```
	
	无回显的的方式输入密码:
	
	```
	read -s var
	```
	
	显示提示信息
	
	```
	read -p "Enter Input:" var
	```
	
	在特定时限内读取输入
	
	```
	read -t timeout var
	```
	
	以特定定界符作为输入行的结束
	
	```
	read -d delim_char var
	```
	
36. IFS是存储定界符的环境变量。它是当前shell环境下使用的默认界定字符串。

	```
	data="name,sex,rollno,location"
	oldIFS=$IFS
	IFS=,
	for item in $data;
	do
	    echo Item: $item
	done
	```
	
	以上代码的循环就可以以逗号作为分割符对字符串进行分割遍历了。
	
37. 序列生成

	```
	echo {1..50} # 生成1到50个数字
	echo {a..z} # 生成字母序列
	```
	
	序列可以直接应用于循环当中
	
	```
	for i in {a..z}; do actions; done;
	```
	
38. shell中使用C语言格式的for循环

	```
	for((i=0;i<10;i++))
	{
		commands;
	}
	```
	
39. while循环
	
	```
	while condition
	do
	    commands;
	done
	```
	
40. if条件

	```
	if condition;
	then
	    commands;
	fi
	```
	
	```
	if condition;
	then
	    commands;
	else if condition; then
	    commands;
	fi
	```
41. 更简单的形式

	```
	[ condition ] && action; # 如果condition为真，那么执行action;
	[ condition ] || action; # 如果condition为假，那么执行action;
	```
42. 比较字符串的时候使用双括号

	```
	[[ $str1 = $str2 ]]
	```
	
## 归档

1. 用tar命令对文件进行归档

	```
	tar -cf output.tar file1 file2 file3 folder1...
	```
	
	也可以使用通配符的形式，比如:
	
	```
	tar -cvf txt.tar *.txt
	```
	
2. 使用-t列出归档中包含的所有文件

	```
	tar -tf ./archive.tar
	```

3. 如果需要在归档和显示列表的时候列出更多的信息，可以使用-v或者-vv参数.

4. 文件名必须紧跟在-f参数之后，而且-f应该是参数中的最后一个

	```
	tar -cvf output.tar f1 f2 ... fn
	```

5. 使用-r参数向归档中添加文件

	```
	tar -rvf original.tar new_file
	```

6. 从归档中提取文件或文件夹

	```
	tar -xvf archive.tar
	```
	
	可以只提取指定的文件
	
	```
	tar -xvf file.tar file1 file4
	```
	
	通过使用-C参数，可以解压到指定的目录

7. 拼接两个归档文件

	```
	tar -Af file1.tar file2.tar
	```
	
8. 使用rsync来备份系统快照

	rsync可以对位于不同位置的文件和目录进行同步，它利用差异计算以及压缩技术，来最小化数据传输量，相对于cp，rsync的优势在于高效利用了差异算法。
	
	将源目录复制到目的端：
	
	```
	rsync -av source_path destination_path
	```
	
	例如:
	
	```
	rsync -av /home/slynux/data scgj@192.168.100.2:/home/data
	```
	
	-a 表示要进行归档
	
	-v 表示输出详细信息
	
	就路径格式而言，如果我们在原路径末尾使用/，那么rsync会将source_path尾端目录中的所有内容复制到目的端。如果不使用/，rsync会将原路径本身复制到目的端。
	
	如果在目标路径末尾使用/，那么rsync会将来自源端内容复制到目的端中。如果没有/,rsync会创建一个同名的目录，然后将源端内容复制到这个目录中。
	
9. 在使用rsync的时候，可以使用rsync命令的-z参数来制定在网络传输时压缩数据。
10. 使用--exclude参数对rsync的内容进行排除：

	```
	rsync -avz source dist --exclude "*.txt"
	```
	
	还可以使用--exclude-from FILEPATH来通过一个列表文件指定需要排除的文件。

11. 在使用rsync的时候，通过--delete参数，来删除那些在源端已经不存在的文件。
	
## 监控磁盘

1. df是disk free的缩写，du是disk usage的缩写
2. 找出某个文件或者多个文件所占用的磁盘空间:
	
	```
	du FILENAME1 FILENAME2
	```
3. 获得目录中所有文件的磁盘占用情况，并在每一行中显示各个文件的占用详情

	```
	du -a DIRECTORY
	```
	如果只是du DIRECTORY只会显示子目录占用的空间而不会显示文件占用的空间。
4. du默认输出的是文件占用的总字节数目，使用选项-h会以可读的单位进行输出。
5. du -ch 会在输出的最后一行有一个总用量的统计
	
	```
	du -ch nginx_access.tar.gz scgj-runner-1.0-SNAPSHOT.jar 
	89M     nginx_access.tar.gz
	45M     scgj-runner-1.0-SNAPSHOT.jar
	133M    总用量
	```
6. du -sh DIRECTORY 只输出合计数据
7. 以特定单位输出

	```
	du -b FILE 
	du -k FILE
	du -m FILE
	...
	```
8. 排除：

	```
	du --exclude "*.txt" FILES
	```
	
## 进程管理

1. ps命令默认只是显示PID、TTY、TIME以及CMD命令，如果要显示多列，那么可以使用-f参数。
2. 默认的情况下，ps只显示当前终端的进程情况，如果要获取系统中每个进程的信息，可以使用-e选项，选项-ax也能产生同样的输出。
3. 可以使用-o选项来制定要展示的列，ps [other options] -o pid,tty...
4. 使用--sort可以对ps的指定字段进行排序，例如：

	```
	ps -ef --sort -pcpu|head # 获取占CPU使用最多的十个进程
	ps -ef --sort +pcpu|head # 获取占CPU使用最少的十个进程
	```
5. ps命令使用-C参数可以按照命令名称匹配进程

	```
	ps -C java -o pid= # 等于号的作用是不显示表头
	```
6. ps命令使用-L参数来显示线程的相关信息，当使用该参数的时候，会显示出两列，NLWP和NLP，其中NLWP是进程的线程数量，NLP是每个线程的ID。
7. ps -e -o pid,nlwp,cmd --sort -nlwp|head 显示分配线程数最多的进程。
8. ps -eo pid,cmd e 显示进程的环境变量。
9. 使用file命令可以获取一个文件的类型：
 
	```
	file /bin/ls
/bin/ls: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.18, stripped
	```
10. kill -l 列出所有的信号
11. kill PROCESS_ID_LIST kill默认发出一个TERM信号，进程ID列表默认使用空格进行分割。
12. kill -S SINGNAL PID

	```
	SIGHUP: 1 对控制进程或终端的终结进行挂起检测
	SIGINT: 2 按下Ctrl + C 时发送该信号
	SIGKILL: 9 强行杀死进程
	SIGTERM: 15 默认终止进程
	SIGSTP: 20 按下Ctrl + Z是发送该信号
	```
13. killall -s SIGNAL process_name
14. 在shell中捕捉并响应信号：

	```
	trap 'signal_handler_function_name' SIGNAL LIST
	```


## 文本处理

1. 正则表达式的基本组成

	<table>
		<tr>
			<th>正则表达式</th>
			<th>描述</th>
		</tr>
		<tr>
			<td>^</td>
			<td>行起始标记</td>
		</tr>
		<tr>
			<td>$</td>
			<td>行尾标记</td>
		</tr>
		<tr>
			<td>.</td>
			<td>匹配任意一个字符</td>
		</tr>
		<tr>
			<td>[]</td>
			<td>匹配中括号之间的任意一个字符</td>
		</tr>
		<tr>
			<td>[^]</td>
			<td>匹配除中括号之外的任意一个字符</td>
		</tr>
		<tr>
			<td>[-]</td>
			<td>匹配指定范围内的任意一个字符</td>
		</tr>
		<tr>
			<td>?</td>
			<td>匹配之前的项一次或0次</td>
		</tr>
		<tr>
			<td>+</td>
			<td>匹配之前的项一次或多次</td>
		</tr>
		<tr>
			<td>*</td>
			<td>匹配之前的项0次或多次</td>
		</tr>
		<tr>
			<td>()</td>
			<td>标记用于匹配的子串</td>
		</tr>
		<tr>
			<td>{n}</td>
			<td>匹配之前的项n次</td>
		</tr>
		<tr>
			<td>{n,}</td>
			<td>匹配之前的项至少N次</td>
		</tr>
		<tr>
			<td>{n,m}</td>
			<td>指定匹配的最小次数和最大次数</td>
		</tr>
		<tr>
			<td>|</td>
			<td>交替，匹配两边的任意一项</td>
		</tr>
		<tr>
			<td>\</td>
			<td>将上述特殊字符进行转义</td>
		</tr>
	</table>
	
2. grep命令可以对多个文件进行搜索，例如：

	```
	grep "match_text" file1 file2 file3 ...
	```
	
3. 使用--color选项可以在输出行中对匹配的串进行标记，例如：

	```
	grep word filename --color=auto
	```

4. grep命令只能匹配match_text中的某些特殊字符，如果要使用正则表达式，可以使用-E选项或者egrep命令。

	```
	grep -E "[a-z]+" filename
	```
	
	```
	egrep "[a-z]+" filename
	```

5. 在egrep中使用-o选项可以只输出匹配的部分，例如：

	```
	➜ /Users/chihongze > echo this is a line. | egrep -o '[a-z]+\.'
	line.
	```
6. 使用-c选项可以直接统计行数，无需在wc -l一次

	```
	grep -c "text" filename
	```

7. 使用-n选项来打印出匹配行的行号

	```
	cat sample1.txt|grep linux -n
	```
8. 使用-i参数来忽略大小写

	```
	echo hello world|grep -i "HELLO"
	```
9. 用grep匹配多个样式，可以使用-e来指定多个样式：
	
	```
	grep -e "pattern1" -e "pattern2"
	```
10. 如果只是想知道能否匹配成功而不去查看匹配的字符串，那么可以通过设置grep的静默选项-q来实现，如果命令运行成功，则会返回0，如果失败会返回非0值。

11. 使用-A选项，查看匹配结果之后的指定行数，例如:

	```
	➜ /Users/chihongze > seq 10|grep 5 -A 3
	5
	6
	7
	8
	```
	
	使用-B选项，查看匹配结果之后的指定行数，例如：
	
	```
	➜ /Users/chihongze > seq 10|grep 5 -B 3
	2
	3
	4
	5
	```
	
	使用-C选项，查看之前以及之后的指定行数，例如：
	
	```
	➜ /Users/chihongze > seq 10|grep 5 -C 3
	2
	3
	4
	5
	6
	7
	8
	```
12. 使用cut提取特定的字段和列

	```
	cut -f 2,3 filename
	```
13. 使用--complement打印出指定列之外的所有列

	```
	cut -f3 --complement student_data.txt
	```
14. 使用-d选项来指定定界符
	
	```
	cut -f2 -d ';' filename
	```

15. cut可以使用如下表示法来表示提取范围

	```
	N- 从第N个元素到行尾
	N-M 从第N个元素到第M个元素
	-M 从第一个元素到第M个元素
	```

	```
	-b 表示字节
	-c 表示字符
	-f 定义字段
	```
	
	```
	➜ /Users/chihongze > cut -d : -f 1-3 /etc/passwd|tail
	_krb_krbtgt:*:230
	_krb_kadmin:*:231
	_krb_changepw:*:232
	_krb_kerberos:*:233
	_krb_anonymous:*:234
	_assetcache:*:235
	_coremediaiod:*:236
	_xcsbuildagent:*:237
	_xcscredserver:*:238
	_launchservicesd:*:239
	```
	
16. 使用sed来替换文本中的字符串:

	```
	sed 's/pattern/replace_string/' file
	```
	
	默认情况下，sed只会打印出替换后的文本，不会对原文件进行改动，通过使用-i参数来实现对文件的改变。
	
	```
	sed -i 's/text/replace/' file
	```
	
	默认情况下，sed只会对每一行第一处符合模式的内容进行替换，但是要替换所有的内容，需要在命令末尾加上'g'：
	
	```
	➜ /Users/chihongze > cat ./test.txt
	nihaoshabi shabi shabi
	tashishabi heheshabi
	➜ /Users/chihongze > sed 's/shabi/dashuai/' test.txt
	nihaodashuai shabi shabi
	tashidashuai heheshabi
	➜ /Users/chihongze > sed 's/shabi/dashuai/g' test.txt
	nihaodashuai dashuai dashuai
	tashidashuai hehedashuai
	```
	如果有时候只需要从第N处开始替换，那么就可以使用/Ng选项。
	
17. 移除匹配样式的行：

	```
	sed /pattern/d file
	```
	
18. 已匹配字符串标记，在sed中，可以用&标记匹配样式的字符串，这样就能够在替换字符串时使用已匹配的内容，比如：

	```
$ echo this is an example | sed 's/\w\+/[&]/g'
[this] [is] [an] [example]
	```
	
19. 字符/在sed中被用作界定符，sed支持任意界定符，比如：

	```
	 echo the text will be relpaced|sed 's|text|replace|g'
	```
	
	当界定符出现在样式内部的时候，必须使用前缀\对它进行转义。
	
	```
	echo the text will be relpaced|sed 's|t\|ext|replace|g'
	```
	
20. 子串匹配标记，sed支持\1 \2形式的子串匹配，例如：

	```
	echo seven EIGHT | sed 's/\([a-z]\+\) \([A-Z]\+\)/\2 \1/'
	```
21. 利用管道组合多个sed命令:

	```
	sed 'expression' | sed 'expression'
	```
	
	等价于
	
	```
	sed `expression1;expression2`
	```
	
	或者
	
	```
	sed -e 'expression' -e 'expression'
	```
	
22. awk 基本结构

	```
	awk 'BEGIN{print "start"} pattern {commands} END {print "end"}' file
	```
	
23. awk内置变量

	```
	NR: 记录数量，执行过程中对应于当前行号
	NF: 字段数量，执行过程中对应于当前行的字段数，可以通过$NF来查看最后一个字段，$(NF-1)为倒数第二个字段。
	$0: 当前行的文本内容
	```
24. 将外界的值传递给awk

	```
	➜ /Users/chihongze > var1=1
	➜ /Users/chihongze > var2=2
	echo|awk -v v1=$var1 -v v2=$var2 '{print v1 v2}' 
	```

25. 使用过滤模式对awk处理的行进行过滤，例如：

	```
	awk 'NR < 5' # 行号小于5的行
	awk 'NR==1,NR==4' # 行号在1到5之间的行
	awk '/linux/' # 包含样式Linux的行
	awk '!/linux/' # 不包含模式Linux的行
	```
	
26. 使用-F 'delimiter' 来指定分割字符
27. awk中可以直接使用for循环，例如: for(i=0;i<10;i++) {print $i;}
28. awk内建的字符串函数们:
	
	```
	length(string) 返回字符串的长度
	index(string, search_string) 返回search_string在字符串中出现的位置
	substr(string, start-position, end-position) 对字符串进行截取
	sub(regex, replacement_str, string) 将第一处内容替换为replacement_str
	gsub(regex, replacement_str, string) 将所有匹配的地方替换为replacement_str
	match(regex, string) 检查正则是否能匹配，如果匹配返回非0，不能匹配返回0
	```

29. 使用paste命令对文件进行拼接

	```
➜ /Users/chihongze/works/learn_bash > cat ./f1.txt 
Sam
Jack
Tom
James
Jason
➜ /Users/chihongze/works/learn_bash > cat ./f2.txt
1
2
3
4
5
6
➜ /Users/chihongze/works/learn_bash > cat ./f3.txt
Java
Python
MySQL
C++
Ruby
Bash
➜ /Users/chihongze/works/learn_bash > paste ./f1.txt ./f2.txt ./f3.txt 
Sam	1	Java
Jack	2	Python
Tom	3	MySQL
James	4	C++
Jason	5	Ruby
	6	Bash
	```


## 下载工具

1. wget URL 下载网页或者远程文件
	
	wget URL1 URL2 URL3 同时下载多个地址
	
	wget URL -O filename -o logfilename
	
	-O 指定输出文件名 -o 指定一个日志文件
	
	wget -t 5 URL 指定下载失败的重试次数为5次
	
	wget -t 0 URL 指定不断的进行重试
	
	wget --limit-rate 20k URL 限速为20K
	
	wget -Q 100m URL 限制磁盘配额为100m
	
	wget -c URL 使用断点续传，如果使用wget进行的下载在完成之前被中断，那么使用-c参数可以从断点开始下载。
	
	wget --user username --password pass URL 如果需要HTTP或者FTP认证，使用--user和--password来提供认证信息
	
2. curl工具的使用：http://my.oschina.net/chihz/blog/96101

## 各种喜闻乐见的命令

1. 用cat将多个文件的内容拼接在一起：

	```
	cat file1 file2 file3 ...
	```
	
	```
	echo 'Text through stdin' | cat - file.txt
	```
	
	在上面的命令中，-被作为stdin文本的文件名，可以将管道前通过stdout输出的内容拼接到file.txt中。
	
2. cat -s file 删除文件中多余的空白行
3. cat -T file 以^I的方式来显示文件中的制表符。
4. cat -n file 显示文件的行号
5. cat -n -b file 显示文件行号并跳过空行
6. 录制终端会话 script -t 2> timing.log -a output.session
7. 播放终端会话 scriptreplay timing.log output.session
8. find命令的基本格式：find base_path other_options
9. 根据文件名或正则表达式进行搜索：

	-name 的参数指定了文件名所必须匹配的字符串，可以将通配符作为参数使用，比如*.txt能够匹配所有以txt结尾的文件名。
	
	-iname 跟name参数功能一致，但是会忽略大小写。
	
	如果要匹配多个条件中的一个，可以采用OR条件操作，比如：
	
	find . \( -name "\*.txt" -o -name "\*.pdf" \) -print
	
	使用path参数可以根据通配符来匹配文件路径，比如：
	
	```
	find /home/users -path "*/slynux/*" -print
	```
	
	-name总是与给定的文件名进行匹配，-path将文件路径作为一个整体进行匹配。-regex跟-path类似，但是-regex是基于正则进行匹配。
	
	-iregex可以忽略大小写
	
	find可以使用!作为否定参数，例如：
	
	```
	find . ! -name "*.txt" -print
	```
	
	搜索所有不以txt结尾的文件。
	
10. find命令在搜索的时候会遍历所有子目录，我们可以使用深度搜索选项-maxdepth和-mindepth来限制find遍历目录的深度。

	只允许在当前目录下查找：
	
	```
	find . -maxdepth 1 -name "f*" -print
	```
	
	设置find开始遍历的最小深度：
	
	```
	find . -mindepth 2 -name "f*" -print
	```
	
11. 根据文件类型进行搜索

	```
	find . -type d -print 列出所有的目录
	find . -type f -print 只列出普通文件
	find . -type l -print 只列出符号链接
	```
	
12. 根据文件时间进行搜索

	```
	find . -type f -atime -7 -print 打印出最近7天内被访问过的所有文件
	find . -type f -atime 7 -print 打印出恰好在7天前被访问过的所有文件
	find . -type f -atime +7 -print 打印出访问时间超过7天的文件
	```
	
	-atime 用户最近一次访问文件的时间
	
	-mtime 文件内容最后一次被修改的时间
	
	-ctime 文件元数据，比如所有权，最后一次改变的时间
	
	-xtime都是基于天的时间单位，还可以以-xmin以分钟作为时间单位进行搜索。
	
13. 基于文件大小的搜索

	```
	find . -type f -size +2k 列出所有大于2K的文件
	find . -type f -size -2k 列出所有小于2K的文件
	find . -type f -size 2k 列出所有等于2K的文件
	```
	
	除了k之外，还有其它的大小单元，比如M以及G
	
14. 删除匹配的文件

	```
	find . -type f -name "*.swp" -delete
	```
	
	删除目录下所有的swp文件
	
15. 基于find回调其它命令

	```
	find . -name "*.java" -exec wc -l {}  \;
	```
	
	统计Java源文件的行数
	
16. xargs命令总是跟在管道操作符之后，将管道前的命令输出作为参数传递给xargs所指定的命令。xargs会把从stdin接收的数据重新格式化，再将其作为参数提供给其它的命令。
	1) 将多行输入转换成单行输出，比如:
	
		```
		➜ /Users/chihongze/works/learn_bash > cat ./example.txt 
		1 2 3 4 5 6
		7 8 9 10
		11 12
		➜ /Users/chihongze/works/learn_bash > cat ./example.txt|xargs
		1 2 3 4 5 6 7 8 9 10 11 12
		```
	2)指定每行最大的参数数量为n，可以将任何来自stdin的文本划分为多行，每行n个参数，每一个参数都是由" "隔开，例如：
	
		```
		➜ /Users/chihongze/works/learn_bash > cat ./example.txt|xargs -n 3
		1 2 3
		4 5 6
		7 8 9
		10 11 12
		```
17. 可以通过-d选项用自定义的分隔符来分割参数，例如：

	```
	echo "splitXsplitXsplitXsplitXsplit"|xargs -d X
	```
	
	集合-n选项，可以将输入划分为多行，每行包含两个参数：
	
	```
	[#2#scgj@web01 ~]$ echo "splitXsplitXsplitXsplitXsplit"|	xargs -d X -n 2
	split split
	split split
	split
	```
	
18. 参数切割与命令执行

	```
	[#10#scgj@web01 ~/chihz]$ cat ./cecho.sh 
	#!/bin/bash

	echo $*'#'
	[#9#scgj@web01 ~/chihz]$ echo "arg1 arg2 arg3"|xargs -n 1 ./cecho.sh 
	arg1#
	arg2#
	arg3#
	将参数按每行一个展开，执行命令3次
	[#11#scgj@web01 ~/chihz]$ echo "arg1 arg2 arg3"|xargs  ./cecho.sh 
	arg1 arg2 arg3#
	将输入整体作为一个参数
	```

19. 使用-I选项来进行参数替换

	```
	[#29#scgj@web01 ~/chihz]$ echo -e "a1\nb1\nc1"|xargs -I {} ./cecho.sh {}
	a1#
	b1#
	c1#
	```

20. 使用tr进行字符转换

	```
	tr [options] set1 set2
	```
	
	将来自stdin的输入字符从set1映射到set2，然后输出到stdout，如果set2的长度小于set1，set2会不断重复最后一个字符，直到与set1长度相等，如果set2长度大于set1，那么set2中超出set1长度的那部分字符将会被全部忽略。
	
	```
	➜ /Users/chihongze > echo "HELLO WHO IS THIS"|tr 'A-Z' 'a-z'
	hello who is this
	```
	
21. 使用tr来删除字符

	```
	cat file.txt|tr -d '[set1]'
	```
	
	删除set1中的字符集
	
22. 字符补集

	使用选项-c可以得到字符补集，例如：
	
	```
	➜ /Users/chihongze > echo "hello 1 char 2 next 4"|tr -d -c '0-9'   
	124
	```
	
23. 使用 -s 选项对重复的字符进行压缩，例如：

	```
	➜ /Users/chihongze > echo "GNU is     not       Unix"|tr -s ' '
	GNU is not Unix
	```

24. 使用md5sum对文件进行校验

	```
	md5sum filename
	```
	
	可以同时对多个文件生成md5摘要
	
	```
	md5sum file1 file2 file3 ...
	```
	
	另外，可以通过sha1sum命令来对文件进行sha1校验
	
25. base64

	```
	➜ /Users/chihongze > echo "heheeh" | base64
	aGVoZWVoCg==
	```
	
29. 同时对多个文件进行排序

	```
	sort file1.txt file2.txt > sorted.txt
	```
	
	或者是
	
	```
	sort file1.txt file2.txt -o sorted.txt
	```
	
	按照数字顺序进行排序
	
	```
	sort -n file.txt
	```
	
	按照逆序进行排序
	
	```
	sort -r file.txt
	```
	
	对两个已经排序过的文件进行合并
	
	```
	sort -m sorted1 sorted2
	```
	
30. uniq

	```
	uniq -u file
	```
	
	只显示文件中唯一的行
	
	```
	uniq -c file
	```
	
	显示文件中行出现的次数
	
	```
	uniq -d
	```
	
	找出文件中重复的行
	
	
31. 创建临时文件与创建临时目录

	```
	[#1#scgj@web01 ~]$ mktemp
	/tmp/tmp.JH3AGZacoD
	[#2#scgj@web01 ~]$ mktemp -d
	/tmp/tmp.l8ZAUgiASu
	```
	
	仅仅生成名称，但是不去真实创建
	
	```
	mktemp -u
	```
	
32. 使用split对文件进行分割

	```
	split -b 10k data.file
	```
	
	将data.file分割成多个文件，每个文件的大小为10K
	
	如果希望要以数字为后缀，那么需要指定参数-d
	
	```
	split -b 10k data.file -d
	```
	
	split对文件进行分割之后，默认前缀都是x，如果要指定自己的文件前缀，可以在命令的最后面进行指定，例如：
	
	```
	split [COMMAND_ARGS] PREFIX
	```
	
	如果不是想按照数据块大小，而是想按照行数进行分割的话，可以使用-l参数，例如:
	
	```
	split -l 10 data.file
	```
	
33. ${VAR%.*} 从$VAR中删除位于与%右侧通配符所匹配的内容，通配符从右向左进行匹配。比如，提取文件名的操作:

	```
	➜ /Users/chihongze > VAR=sample.jpg
	➜ /Users/chihongze > ${VAR%.jpg}
	➜ /Users/chihongze > name=${VAR%.jpg}
	➜ /Users/chihongze > echo $name
	```
	
	% 属于非贪婪操作，它从右到左找出匹配通配符的最短结果，还有另一个操作符，%%，这个操作符是贪婪的
	
	```
	➜ /Users/chihongze > VAR=hack.fun.book.txt
	➜ /Users/chihongze > echo ${VAR%.*}
hack.fun.book
	➜ /Users/chihongze > echo ${VAR%%.*}
hack

	```
	
34. ${VAR#.*} 删除位于右侧通配符所匹配的字符串，从左到右进行匹配，例如：

	```
	➜ /Users/chihongze > VAR=hack.fun.book.txt
	➜ /Users/chihongze > echo ${VAR#*.}
	fun.book.txt
	➜ /Users/chihongze > echo ${VAR##*.}
txt
	```
	
## 文件操作

1. 使用comm进行文件的交集和差集运算：
	
	在使用comm命令之前，首先应该对文件内容进行排序
	
	```
	➜ /Users/chihongze/works/learn_bash > comm A.txt B.txt
	apple
			carrot
			cookies
					gold
	iron
			orange
	silver
	steel
	```
	
	comm命令会以\t为分隔符，将文件分成三列，第一列是只在A.txt中出现的行，第二列是指在B.txt中出现的行，第三列是交集。可以控制只显示哪一列，例如：
	
	```
	comm A.txt B.txt -1 -2
	```
	
	这样就只会显示第三列，即交集部分
	
2. Linux中，目录拥有一个特殊的权限叫做粘滞位，如果目录设置了粘滞位，只有创建该目录的用户才能，即便其它用户或组对该目录应有写权限。粘滞位权限用t或者T来表示

	```
	[#6#scgj@web01 /]$ ll|grep tmp
	drwxrwxrwt.   9 root root  4096 8月   9 03:08 tmp
	```

3. 使用chmod来设置文件权限

	```
	chmod u=rwx g=rw o=r file
	```
	
	u —— 用户权限 
	g —— 用户组权限
	o —— 其它实体权限
	a —— 上述所有权限
	
	```
	chmod o+x filename
	chmod a+x filename
	chmod a-x filename
	```
	
	也可以用八进制数字来表示
	
	```
	r —— 4
	w —— 2
	x —— 1
	chmod 764 filename
	```
	
	设置粘滞位
	
	```
	chmod a+t dir_name
	```
	
	使用-R参数，可以以递归的方式来设置文件权限
	
4. 使用chown 改变文件属主
	
	```
	chown user.group filename
	```
	
	同样使用-R可以递归改变
	
5. s位可以让其它用户以文件属主的身份来执行命令，例如:

	```
	chmod +s executable_file
	chown root.root executable_file
	chmod +s executable_file
	./executable_file
	```
	
6. 通过chattr命令，可以对ext2、ext3、ext4文件系统下的文件进行锁定操作，这个命令必须由root来执行，和这个命令对应的是lsattr

	```
	chattr [-RV] [mode]
	-R 递归修改所有的文件以及子目录
	-V 详细显示修改内容，并打印输出
	```
	
	chattr常用参数
	
	```
	+ 在原有参数设定的基础上追加参数
	- 在原有参数设定的基础上移除参数
	= 更新为制定参数
	a append 指定该参数后，只能向文件追加数据，不能删除
	c compress 指定该参数后，设定文件是否经压缩后再存储
	i immutable 设定文件不能被修改、删除、重命名
	s 安全删除文件或目录，文件被删除后全部收回磁盘空间
	u 与s相反，删除后会保留数据块，以便以后可以恢复
	```
	
	通过lsattr命令来查看一个文件的锁定属性。

7. 创建软连接

	```
	ln -s target symbolic_link_name
	```
	
8. head -n N file 如果N是正数，那么会打印文件的前N行，如果N是负数，那么会打印除了abs(N)之外所有的行。

9. tail -n N file 如果N是正数，那么会打印文件的最后N行，如果N是负数，那么会打印除了前abs(N)之外所有的行。

