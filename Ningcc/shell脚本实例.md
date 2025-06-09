# shell脚本实例

#### 写在最前

* 从5开始所有脚本.sh文件存储在ubuntu64 clone2虚拟机中桌面/shell_program中

#### 1. read的使用

```shell
#!/bin/bash
#" this is to test the use of read "
echo "输入名字： \c"
read name
echo "你好，$name"
echo
echo -n "你在哪里工作？"
read
echo "在 $REPLY 一定使你忙碌吧;"
echo
echo -p "输入你的职业"
read
echo "你是一个$REPLY"
echo
```

#### 2.if 的使用

```shell

```

#### 3.编写自动创建用户并设置初始密码的脚本

```shell
#!/bin/bash
read -p "请输入新建用户名称:" name
useradd $name
echo "123456" | passwd --stdin $name &> /dev/null
echo "$naem 创建完成，密码是123456"
#--stdin:它指示程序从标准输入（standard input，简称stdin）读取数据，而不是从文件或其他数据源读取。在许多命令行工具中，这可以让你直接键入文本或管道（pipe）其他命令的输出作为该程序的输入。
```

#### 4. shell变量的综合应用实例

```shell
vi 5_zonghe.sh

#!/bin/bash
#"this script is for 测试变量 "
#set -x
wow="hi,boy"
country="China"
echo "$wow,my name is ning from $country"
echo Home Directory:$HOME
echo commond line here is:
echo $0 $*
echo Before shift operation
echo number of arguments = $#
echo all the arguments:$*
echo \$0=$0, \$1=%1, \$2=$2
shift
echo "另一个执行结果："
echo number of arguments:$#
echo all the arguments:$*
echo \$0=$0,\$1=$1,\$2=$2

```

#### 5.判断/bin目录下data文件是否存在

```shell
vi 6_testFile
#!/bin/bash
file_name=/bin/data
if [ -f "$file_name" ]
then
	echo "$file_name文件存在"
else
	echo "$file_name文件不存在"
fi
```

#### 5-1. while循环快速定义数组

```shell
# 从外部文件中定义一个数组
# ！/bin/bash
while read line  # 从< ./hosts.txt 文件中一行一行读取
do
	hosts[i++]=$line #读取的每一行进行赋值
done < ./hosts.txt
echo "数组hosts:${hosts[*]}"
echo "数组的索引：${!hosts[@]}"
# for循环遍历输出hosts数组
for j in "${hosts[@]}"
do
	echo ${hosts[j]}
done
```

#### 5-2. for循环快速定义数组

```shell
#从外部文件中获取数据作为数组
#!/bin/bash
for line in `cat ./hosts.txt` #for循环取值时以空格结束，而while则是以\n结束（回车）
#host1 host2 host3 有所不同，两种执行不同
do
	hosts[i++]=$line
done
#for 循环输出数组
for i in "${!hosts[@]}"
do
	echo "$i:${hosts[$i]}"
done
```

#### 5-3. 使用数组统计文件中的性别个数（引出awk）

```shell
echo $line | wak `{print $1}`  #输出一行中的第一列，列数以空格区分
echo $line | wak `{print $2}`  #输出一行中的第二列，列数以空格区分
#sex.txt
maike man
lee man
han woman
jan woman
son man       
#脚本编写
#!/bin/bash
declare -A sex #定义关联数组
while read line
do
        type=`echo $line | awk '{print $2}'`
        let sex[$type]++
done < ./sex.txt
for i in "${!sex[@]}"
do
        echo $i:${sex[$i]}
done
#输出结果
man:3
woman:2

```

5-