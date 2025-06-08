# shell 编程

#### 写在最前

```shell
\c :转义字符 ，不换行，一般默认换行
-n :不换行,echo -n "Where do you work? "：这条命令用于在命令行中显示文本“Where do you work? ”。-n选项的作用是防止echo命令在输出的文本末尾自动添加一个换行符。这样，光标就会停留在“?”之后，而不是跳到下一行
$REPLY :使用了特殊变量 REPLY，它保存了 read 命令读取的最后一个输入  :echo -n "Where do you work? "
															read
															echo "I guess $REPLY keeps you busy!"
-p :使用 read 的 -p 参数，可以直接在提示信息后读取用户输入
-e :echo 命令的 -e 选项来启用对转义序列的支持
$LOGNAME: 是一个环境变量，通常包含当前用户的登录名
```

```shell
#在 Shell 脚本中，let 命令用于执行算术运算。它主要用来进行整数的算术计算，包括加、减、乘、除以及位运算等。let 命令非常适合在脚本中做循环控制、条件判断或其他需要数学运算的地方。
#!/bin/bash

i=10
let "i += 5"     # i 现在等于 15
let "i -= 3"     # i 现在等于 12
let "i *= 2"     # i 现在等于 24
let "i /= 4"     # i 现在等于 6
let "i %= 5"     # i 现在等于 1
#虽然 let 是 Bash 的一个内部命令，但在某些较旧的 shell 如 /bin/sh 中可能不可用。在这种情况下，可以使用 expr 或者 awk 来代替
a=5
b=3
c=$(expr $a + $b)
echo "The sum is $c"
```



### 1. shebang

* shebang指的是出现在文本文件中第一行的两个字符：#!

  以**#!/bin/bsah** 开头的文件，程序执行时调用/bin/bash,也就是bash解释器

  以**#!/usr/bin/python** 开头的文件，程序执行时调用pythion解释器

  以#**!/ust/bin/env**  解释器名称 ，是一种在不同平台上都能正确找到解释器的办法

```shell
#history命令 
echo #HISTFILE
-c 清除历史
-r 回复历史

# 调用历史记录命令
# !历史命令ID ,快速执行历史命令
echo $HISTSIZE
!3000 #执行第3000条命令
!! # 执行上条命令
```



### 1. 变量命名规则

1. 变量名称可以由字母、数字和下划线组成，但不能以数字开头，环境变量名建议大写

2. 等号两侧不能有空格

3. 在bash中，变量默认类型都是字符串类型，无法进行数值运算

4. 变量的值如果有空格，需要使用双引号或单引号括起来

   > 弱类型语言：不需要主动声明变量类型 ，shell默认为字符串

```shell
创建变量
# 变量赋值时等号两边不能有空格

B=5		//创建变量
echo $B //输出变量
撤销变量
unset B 
//静态变量
readonly B=5  //不能更改，撤销
#输出
echo $name

```

#### 单引号和双引号和反引号

> 单引号变量，不识别特殊语法
>
> 双引号变量，能识别特殊语法
>
> 反引号: 会保留反引号中的功能,例如: 
>
> ```shell
> abc@abc-virtual-machine:~/桌面/shell_program$ name=`ls`
> abc@abc-virtual-machine:~/桌面/shell_program$ echo $name
> hello.sh name_ls.sh #这是执行echo $name的结果,反引号会保留ls
> abc@abc-virtual-machine:~/桌面/shell_program$ 
> 
> 
> ```

```bash
abc@abc-virtual-machine:~/桌面/shell_program$ name1="哈哈哈"
abc@abc-virtual-machine:~/桌面/shell_program$ echo $name1
哈哈哈    #输出变量name1
abc@abc-virtual-machine:~/桌面/shell_program$ name2='${name1}' # 赋值变量name1的值给name2
abc@abc-virtual-machine:~/桌面/shell_program$ echo $name2
${name1} # name2的值不识别特殊符号

##正确赋值
abc@abc-virtual-machine:~/桌面/shell_program$ name2="${name1}"
abc@abc-virtual-machine:~/桌面/shell_program$ echo $name2
哈哈哈
abc@abc-virtual-machine:~/桌面/shell_program$ 

```

2. 执行脚本的方式

> 使用sh或者bash  ./wenjian.sh : 开启子shell执行脚本文件,文件的变量命名空间在子shell中
>
> 使用source或者` .`执行时,则是在当前shell环境下执行,会修改当前环境中的变量

#### **PATH变量**

* 每个用户都有自己的环境变量配置文件，~/.bash_profile  ~/.bashrc,，且以个人配置文件优先加载变量，读取以个人环境变量配置生效

* 当需要给每个用户都使用某个变量时，写入全局 /etc/profile

### shell的特殊变量

参数 ———跟在脚本后面的

```shell
#例如 
ls -l l为参数
bash ./test.sh 参数1 参数2 参数3
```

```shell
$0   获取shell文件名，以及脚本路径
$n   获取shell脚本的第n个参数， n 在0-9中间，大于9则写${10}
$#   获取shell执行的总参数的个数
$*   获取shell脚本的所有参数，不加引号等同于$@ 
$@   同上
```

编写脚本

```shell
#!/bin/bsah
echo '特殊变量 $0 $1 $2 ...的事件'
echo '结果：' $0 $1 $2 
echo "#########################"
echo '特殊变量$# ，获取参数的总个数'
echo "结果：" $#
echo "#########################"
echo "特殊变量$* ,实践"
echo '结果：' $*
echo "#########################"
echo "特殊变量#@， 实践"
echo '结果：'$@

```

```shell
结果：
abc@abc-virtual-machine:~/桌面/shell_program$ sh ./can_shu.sh ning chen 23 24 25 haha
特殊变量 $0 $1 $2 ...的事件
结果： ./can_shu.sh ning chen
#########################
特殊变量$# ，获取参数的总个数
结果： 6
#########################
特殊变量ning chen 23 24 25 haha ,实践
结果： ning chen 23 24 25 haha
#########################
特殊变量#@， 实践
结果：ning chen 23 24 25 haha

```

```bash
$* 和 $@ 的区别你了解吗?
$*和 $@ 都表示传递给函数或脚本的所有参数
当 $*和 $@ 不被双引号""包围时，它们之间没有任何区别，都是将接收到的每个参数看做一份数
据，彼此之间以空格来分隔。
但是当它们被双引号""包含时，就会有区别了:
"$*"会将所有的参数从整体上看做一份数据，而不是把每个参数都看做一份数据,

"$@"仍然将每个参数都看作一份数据，彼此之间是独立的
```

### 特殊变量2

```bash
$? 上一次运行状态的返回值，0正确，运行成功，非0表示失败
$$ 当前shell脚本的进程号
$! 上一次后台进程的PID
$_  取得之前执行的命令，最后一个参数

```

脚本控制返回值：

```bash
# 脚本执行完毕后会得到一个返回值

```

### 字符串操作作

```bash
name="ningchencheng"
${name}       --返回变量值
${name}       --变量字符串的长度
${name:4}     --返回截取第四位开始的字符串
${name#cheng} --删除含有cheng的子串
${name##ch}   --删除最长匹配的sh
${name%ch}    --从结尾往前删除最短的ch
${name%%ch}   --从结尾往前删除最长的ch子串

替换
${name/str1/str2} --用str2替换第一个出现的str1
${name//str1/str2}  --用str2代替所有的str1

```

### 4.运算符

* expr :手工命令行计数器

```shell
expr 表达式
# 用空格隔开每一项
# 对包含空格和其他字符串的字符要用引号包起来
expr length "this is a test"
输出结果：14
expr 14 % 9 # 运算中件要用空格
 5
expr 10 + 10
 20
expr 1000 + 900
 1900
expr 30 / 3 / 2
 5
expr 30 \* 3 (使用乘号时，必须用反斜线屏蔽其特定含义。因为shell可能会误解显示星号的意义)
 90
expr 30 * 3
expr: Syntax error (语法运算)
```

```shell
# 可使用
$((表达式)) 或 $[表达式] 直接进行计算
#可省略使用转义字符

#实例
#计算（2+3）*4的值
 echo $[(2+3)*4]
 
# 使用脚本编写
#!/bin/bash
sum=$[$1 + $2]
echo sum=$sum


```

### 5.条件判断

* 基本语法： test  条件

  ```bash
  a=ning
  test $a=ning
  echo $?  #判断上一条指令返回是否成功 ，0即成功
  ```

* [ 判断条件 ]

  ```shell
  [ $a=hell ] #判断条件两端要有空格
  echo $?
  ```

* 两个整数之间的判断

  ```shell
  -eq 等于（equal）     -ne 不等于（not equal）
  -lt 小于 （less than）  -le 小于等于 （less equal）
  -gt 大于 （greater than）  -ge大于等于 （greater than）
  # 如果是两字符串之间比较，用"="判断相等，用"!= "判断不等
  
  [2 -le 8] #判断2<8
  
  ```

* 文件判断

  ```shell
  if [ -f "/bin/data" ]  # -f:一个测试操作符，用于检查指定的文件是否是一个普通文件（即非目录,判断/bin/data文件是否存在
  ```
  
  

* 文件权限判断

  ```shell
  [ -r sum.sh ] #判断sum.sh文件是否有读取权限
  [ -x xxx.sh ]
  [ -w xxx.sh ]
  ```

* 多条件判断

  && 表示前一条命令执行成功时，才执行后一条命令，|| 表示上一条命令失败后才执行下一条命令

  ```shell
  [ 2 -le 8 ] && echo ok || ehock not ok # 类似于三目运算符
  ```

### 6. 流程控制

* if语句

  ```shell
  if [ 2 -gt 3 ];then
   程序段
  fi
  
  
  # 多分枝
  if [ 条件判断 ];then
  	程序段
  elif [条件判断];then
  	程序段
  else
  	程序段
  fi	
  ```

* 文件测试

  ```shell
  [ -f file_name ] -f:当文件存在是为真
  -b:block ,当file_name存在且是块文件时返回真
  -d: directory ,判断file_name是否为目录
  -h : 判断file_name是否为链接文件
  -c : 判断file_name是否为字符文件
  -e :判断file_name是否存在，不管是否为文件
  
  ```
  
  
  
* case语句

  ```shell
  # case语句有头有尾，条件以但括号）结束。一个条件结束以;;结束
  #!/bin/bash
  case $1 in
  1)
          echo "这是1"
          ;;
  2)
          echo "this is tow"
          ;;
  3)
          echo "this is three"
          ;;
  *)
          echo "this is ${1}"
          ;;
  esac
  ```

* for 循环

  ```bash
  for((初始值;条件; ; ))
  do 
  	程序段
  done
  
  # 从1加到100
  for i in { 1..100 };do sum=$[$sum+$i];done echo $sum
  ```

* while循环

  ```bash
  while [ 条件判断 ]
  do
   	程序段
  done
  
  #从1加到100
  #!/bin/bash
  i=$1
  sum=0
  while [ $i -lt 100 ]
  do 
  # 	sum=$[ $sum+$1 ]
  #	i=$[ $i+1 ]
  # 第二种写法
  	let sum+=a
  	let a++
done
  echo $sum
  ```

### 7. read读取控制输入

* read

  ```bash
  read
  参数
  -t :等待时间（秒） ，如果不设置，则一直等待
  -p :读取值时的提示符，
  
  #!/bin/bash
  read -t 10 -p "数入您的姓名：" name
  echo "Wellcome,$name"
  ```

#### 7-2.数组

* ```shell
  #定义数组
  arr=()  #空数组
  arr[0]=a
  arr[1]=b
  arr[3]=c
  #也可以直接定义
  arr2=(a b c)   #元素之间以空格隔开
  ```

* 关联数组

  ```shell
  #关联数组可以使用非数组作为下标，可以是任意字符串
  # declare -A 命令用于声明一个关联数组（也称为哈希表或字典），这是一种可以使用字符串作为索引的数据结构
  # 声明一个关联数组
  declare -A user_info
  user_info['name']='ning'
  user_info['age']=22
  #或者
  user_info=([name]='ning',[age]=18)
  
  
  ```

* 多维数组

  ```shell
  数组名[索引1][索引2]=值
  数组名[索引1，索引2]=值
  ```

* 获取数组的值

  ```shell
  echo ${arr[0]} # 获取第一个元素
  #获取最后一个元素
  echo ${arr[-1]}
  #获取所有元素
  echo ${arr[*]}  或者 echo ${arr[@]}
  #统计数组的长度
  echo ${#arr[*]}
  #获取数组的下标
  echo ${!arr[@]}   echo${!user_info[@]}
  
  ```

* 删除数组元素和数组

  ```shell
  unset arr2[2]  #删除索引未2的元素
  unset user_info[age]  #删除索引为age的元素
  unset arr2    # 删除数组
  ```

* 循环遍历数组

  ```shell
  for i in "${$arr1[*]}"
  do
  echo $i
  done
  #通过下标取值
  for i in "${!arr1[@]}"
  do
  echo ${arr1[i]}
  done
  ```

  

### 8.函数

* 系统函数的使用

  ```bash
  #!/bin/bash
  echo "$1_log_$(date +%s)"
  # 输出第一个参数和log当前时间戳组合,使用$(函数名 )调用系统函数
  
  ```

* basename

  ```shell
  # basename str suffix 
  # basename命令会删除所有前缀包括最后一个（`/`）字符，取在最后一个路径的文件名
  # str :文件的相对路径或绝对路径名
  # suffix :指定后缀，会将文件名的后缀去掉
  
  #!/bin/bash
  basename /root/test.txt .txt
  输出test
  ```

* dirname 

  ```bash
  # dirname /root/test.txt
  # 剪切test.txt文件之前的路径字符串
  # 输出/root
  ```

* 自定义函数

  基本语法

  ```shell
  function name (){
  	Action;
  	return int;
  }
  # 必须在调用函数之前声明函数
  # 函数返回值只能通过$?系统变量获得，如果没有return ,将会以最后一条语句作为返回值，
  # return 后跟数值n (0-255)
  
  
  #!/bin/bash
  function add(){
          sum=$[$1 + $2 ]
          echo $1+$2"的和为：" $sum
  }
  read -p "请输入第一个整数：" a
  read -p "请输入第二个整数：" b
  
  add $a $b #调用函数 参数1 参数2
  
   #可修改为
   #!/bin/bash
  function add(){
          s=$[$1 + $2 ]
          echo $s
  }
  read -p "请输入第一个整数：" a
  read -p "请输入第二个整数：" b
  sum=$[add $a $b] #调用函数 参数1 参数2
  echo "和为："$sum
  
  
  ```

### 9.正则表达式



```bash
#^ :匹配开头,配合grep
grep ^a ：匹配以a开头的内容
# $ :匹配以结尾
grep abs$ : 匹配以abs结尾的内容
#特殊字符 . :通配符，匹配一个字符，任意字符
grep r..t : 匹配r和t中间隔两个字符
#特殊符号 * ：匹配*号之前字符的一个或多个字符，匹配任意次
grep r*t: 匹配r之后一个或多个重复字符
# [ ],中括号，匹配字符区间，[6,9],匹配6或9，[6-9],匹配6-9，[0-9]*:匹配0-9任意多个字符
```

### 10 . 文本处理工具

* cut

  ```bash
  cut命令从文件的每一行剪切字节、字符和字段并将这些字节、字符、字段输出
  1.基本用法
  cut [ 选项参数 ] filename
  # 参数
  -f : 列号，选取第几列
  -d: 分隔符，按照指定的分隔符分割列，默认制表符是“\t”
  -c : 按字符进行切割，后面加n表示取第几列，比如 -c 1
  
  
  ```

* grep

  ```shell
  grep -q xxx 静默，将结果不打印
  -v : 取反 ，#grep -v love test.txt将文档中的love排除，输出非包含love的结果
  -R :递归，# 可以查找目录下某个文件内是否包含被查找内容
  
   
  ```

* sed

  ```shell
  sed(stream editor)流编辑器
  #一次处理一行内容，把当前处理的行存储在临时缓冲区，接着用sed命令处理缓冲区中的内容，处理完成后把缓冲区的内容送往屏幕
  #处理后的结果会输出到标准输出
  sed 选项 命令 文件
  sed 选项 -f 脚本 文件
  -r : 支持正则
  -i : 支持写入到文件内，如果不加-i，对实际文件内容不产生任何效果
  #d : 删除
  sed -r '/root/d' ./passwd.txt  # 删除含有root的行
  sed -r '3d' ./passwd.txt #删除文件的第三行
  sed -r '3,$d' ./passwd.txt #将第三行后面的内容删除（3，$）,第三行-最后一行
  # s : 替换
  ser -r 's/root/ning/' ./passwd.txt
  #root:原来的内容 # ning:需要替换的内容 #s:替换
  #加g,全局替换
  sed -r 's/root/ning/g' ./passwd.txt
  # r :读取文件
  sed -r '$r 1.txt' ./passwd.txt
  #$:最后，将1.txt的内容读取到添加到。/passwd.txt的最后一行
  #w :写，另存为
  sed -r 'w 22.txt' ./passwd.txt
  #将passwd.txt的内容写入到222.txt中
  
  #a :追加信息(在某一行之后)
  sed -r '/^root/a 字符插件你'./passwd.txt
  
  #i :添加信息（在某一行之前）
  sed -r '/2i 123/' ./passwd.txt
  
  #c :整行替换
  sed -r '/2c 123123' ./passwd.txt #将第2行替换为123123
  ```

* awk

  ```bash
  # 一个强大的文本分析工具，把文件逐行读入，以空格为默认分割符将每行切片，切开的部分在进行分析处理
  # 基本用法
  awk [ 选项参数 ] ‘/pattern1/{action1} /pattern2/{action2}...’ filename
  3#pattern : 表示awk在数据中查找的内容，就是匹配模式,（正则表达式）
  #action: 再找到匹配内容时执行的一系列命令
  # 选项参数
  -F 指定文件分隔符
  -V 赋值一个用户自定义变量
  # 查找password文件以root开头的所有行，并输出第7列
   cat passwd | awk -F ':' '/^root/{print $7}
   # 查找password文件以root开头的所有行，并输出第1列和第7列,$NF是最后一列，并以逗号分割
    cat passwd | awk -F ':' '/^root/{print $1","$7","$NF}
  #取出01aa,sh
filename=$(awk -F '/' '{print $NF}' <<< "/home/abc/桌面/shell_program/01aa.sh")
  echo "$filename"
  # <<< 是一个here string,用于将字符串直接传递给awk命令
  ```
  
  