# LINUX SHELL脚本攻略

## 小试牛刀

#### 1.在终端显示出信息

```bash
source  当前shell，不用设置x权限 
bash    开启子shell，不用设置x权限
./      开启子shell，需要权限
```

#### 2.使用变量与环境变量

```bash
varname=value
echo $varname || $(varname) => value
echo "we have 5 ${fruit}(s)"


export PATH=$PATH:/path

#获得长度
length=${#var}
#识别当前SHELL
echo $SHELL
#检查是否是超级用户

if [ $UID -ne 0 ];then
    echo "non root user"
else 
    echo "root user"
fi

if test $UID -ne 0
    then
        echo non user root
else
    echo root user
fi
```

#### 3.使用函数添加函数变量名

```bash
PATH=/usr/bin;/bin
LD_LIBRARY_PATH=/usr/lib;/lib
```

#### 4.使用shell进行运算

```bash
#当使用let时，变量名之前不需要再添加$
let result=no1+no2
let no1++
let no1+=6
result=$[ no1 + no2 ]
result=$[ $no1 + no2 ]
result=$(( no1 + 50 ))
result=`expr 3 + 4`
result=$(expr $no1 + 5)
```

##### 5.玩转文件描述符与重定向

- 0——stdin（标准输入）

- 1——stdout（标准输出）

- 2——stderr（标准错误）

```bash
#使用大于号将文本保存到文件中
echo "This is a sample text 1" > temp.txt
#使用双大于号将文本追加到文件中
echo "This is a sample text 2" >> temp.txt
#标准错误
echo ls + 2> out.txt
#分别重定向到不同的文件
cmd 2> stderr.txt 1> stdout.txt
#重定向到重一个文件
cmd > alloutput.txt 2>&1 或者 cmd &> alloutput.txt

cmd < file
```

#### 6.数组与关联数组

```bash
#定义数组
array_var=(test1 test2 test3)
array_var[0]="test1"
array_var[1]="test2"

#打印特定索引的数组元素内容
echo ${array_var[$index]}
#以列表形式打印出数组的所有值
echo ${array_var[*]}
echo ${array_var[@]}
#打印数组长度
echo ${#array_var[*]}


#定义关联数组
#使用字符串作为索引
1.声明语句将一个变量定义为关联数组
declare -A ass_array
2.
ass_array=([index2]=val1 [index2]=val2)
ass_array[index1]=val1
ass_array[index2]=val2

#输入某个索引值
echo ${ass_array[index1]}


#列出数组索引
echo ${!array_var[*]}
echo ${!array_var[@]}
```

#### 1.不按回车键读入n个字符

```bash
read -n 2 var //读取2个字符
read -s var //无回显的方式读取密码
read -p "enter input" var //显示提示信息
read -t timeout var //时间限制
read -d delim_char var //特定符号结束
```

#### 2.持续运行命令直至成功

```bash
#持续命令直至执行成功
repeate wget -c https://www.example.com/sofeware-0.1.tar.gz
repeate(){
    while true
    do
     $@ && return;
    done
}
#更快的做法
repeate(){
    while :;
    do 
      $@ && return;
    done
}

#加入延时
repeate(){
    while :;
    do
      $@ && return;
      sleep 30;
    done
}
```

#### 3.分隔符与迭代器

```bash
data="name, gender, rollno, location"
oldIFS=$IFS
IFS=, 
for item in $data
do
    echo Item: $item
done
IFS=$oldIFS

#面向列表的for循环
for var in list
do
    commands;
done
echo {1..50}
echo {a..z}{A..Z}

#迭代指定范围的数字
for((i=0;i<10;i++)){
    commands;
}

#循环到条件满足为止
while condition
do 
    commands;
done

#until循环
x=0;
until [ $x -eq 9 ]
do
    let x++; echo $x;
done
```

#### 4.比较与测试

```bash
if condition;
then
    commands;
fi


if condition;
then 
    commands;
else if condition;then
else
    commands;
fi
```

##### 算术比较

```bash
[ $var -eq 0 ] or [ $var -eq 0 ]
-eq
-gt
-le
-lt
-ge
```

##### 文件系统相关测试

- [ -f $fiile_var ] :

- [ -x $var ]

- [ -d $var ]

- [ -e $var ]

- [ -c $var ]

- [ -b $var ]

- [ -w $var ]

- [ -r $var ]

- [ -L $var ]

## 命令之乐

### 1. cat

```bash
#单个文件
cat file.txt
//this is a line inside file.txt

#打印多个
cat one.txt two.txt
//this is from one.txt
//this is from two.txt


#管道符作为cat命令的标准输入
output_from_some commands | cat
echo "text through stdin" | cat - file.txt
//-被作为stdin文本的文件名


#去掉多余的空白行
cat -s file

#将制表符显示为^I
cat -T file

#生成行号
cat -n file
#跳过空白行，生成行号
cat -nb file
```

### 2. script,scriptreplay

```bash
script -t 2> timing.log output.session
scriptreplay timing.log output.session
```

### 3. find

```bash
find base_path

find . -print #print选项使用\n分割输出每个文件或目录名
```

#### 3.1 根据文件名或正则表达式进行搜索

```bash
find path -name -i "example" -print
#-i或略字母大小写
```

支持逻辑操作符。-a和-and操作，-o和-or

```bash
#打印出所有的.txt和.pdf 
find . \(-name '*.txt' -o -name '*.pdf' \) -print

#打印选择以s开头且其中包含e的文件
find . \(-name "*e*" -a -name "s*" \) -print

find -path '*/slynux/*' -name '*.txt' -print

#使用正则
find . -iregex '.*\(\.py\|.sh\)$'
#否定参数
#匹配所有不以.txt结尾的文件
find . ! -name '*.txt' -print


```

#### 3.2 基于目录深度的搜索

find命令在查找时会遍历完所有的子目录。默认情况下，find命令不会跟符号链接。-L选项可以强制改变这种行为。-maxdepth和-mindepth可以限制遍历的目录深度。

```bash
find -L /proc -maxdepth 1 -name 'bundlemaker.def' 2>/dev/null


find . -mindepth 2 -name 'f*' -print
```

-maxdepth和-mindepth应该在find命令中及早出现

#### 3.3 根据文件类型搜索

类Unix系统将一切都视为文件。文件具有不同的类型，例如普通文件、目录、字符设备、块设备、符号链接、硬链接、套接字以及FIFO等。

- 普通文件    f

- 符号链接    l

- 目录            d

- 字符设备     c

- 块设备         b

- 套接字         s

- FIFO             p             

#### 3.4 根据文件的时间戳进行搜索

以天为单位

- 访问时间(-atime): 用户最近一次访问文件的时间

- 修改时间(-mtime): 文件内容最后一次被修改的时间

- 变化时间(-ctime): 文件元数据(例如权限或所有权)最后一次改变的时间

```bash
#打印出最近7天内被访问过的左右文件
find . -type f -atime 7 -print

#打印出访问时间超过7天的所有时间
find . -type f -atime +7 -print
```

以分钟为单位

- -amin

- -mmin

- -cmin

#### 3.5 基于文件大小的搜索

- b     块（512字节）

- c      字节

- w     字

- k      千字节（1024字节）

- M     兆字节（1024k字节）

- G      吉字节（1024m字节）

#### 3.5 基于文件权限和所有权的匹配

```bash
find . -type f -perm 644 -print
find . -type f -user slynux -print
```

#### 3.6 利用find执行相应操作

1 删除匹配的文件

```bash
find . -type - name '*swp' -delete
```

2 执行命令

```bash
find . -type f -user root -exec chown slynux {} \;
```

find命令使用{}代表文件名。对于每一个匹配的文件，find命令会将{}替换成相应的文件名并更改该文件的所有权。如果find命令找到了root所拥有的两个文件，那么它会将其所有者改为slynux。

该命令结尾的\;。必须对分号进行转义，否则shell会将其视为find命令的结束，而非chown命令的结束。

```bash
find . -type f -name '*.c' -exec cat {} \;>all_c_files.txt
find . -type f -name '*.c' -exec cat {} > all_c_files.txt \;
find . -type f -name '*.c' -exec cat {} >all_c_files.txt +
```

使用>操作符将来自find的数据重定向到all_c_files.txt，没有使用>>(追加)的原因时find命令的全部输出就只有一个数据流(stdin),而只有当多个数据流被追加到单个文件中时才有必要使用>>。

```bash
find . -type f -mtime +10 -name '*.c' -exec cp {} OLD \;
```

-exec只能接受单个命令。不过可以写一个sh

```bash
-exec ./commands.sh {} \;
```

#### 3.7 让find跳过特定的目录

```bash
find devel/source_path -name '.git' -prune -o -type f -print
```

### 4. 玩转xargs

xargs命令应该紧跟在管道符之后，它使用标准输入作为主要的数据源，将从stdin中读取的数据作为指定命令的参数并执行该命令。

#### 4.1 将多行输入转换成单行输出

```bash
cat example.txt | xargs
```

#### 4.2 将单行输入转换成多行输出

```bash
cat example.txt | xargs -n 3
```

-n选项可以限制每次调用命令时用到的参数个数。每行n个元素

xargs命令接受来自stdin的输入，将数据解析成单个元素，然后调用指定命令并将这些元素作为该命令的 参数。xargs默认使用空白字符分割输入并执行/bin/echo

#### 4.3 指定分隔符

```bash
echo "splitxsplit2xsplit3xsplit4" | xargs -d x
split split2 split3 split4
echo "splitxsplit2xsplit3xsplit4" | xargs -d x -n 2
split split2
split3 split4
```

#### 4.4 格式化参数

```bash
#!/bin/bash
#文件名 echo.sh
echo $*'#'

./echo.sh arg1 arg2 arg3
#每次调用一个参数
./echo.sh arg1
./echo.sh arg2

#每次调用一到两个
./echo.sh arg1 arg2
./echo.sh arg3

#调用所有
./echo arg1 arg2 arg3

一个包含所有参数的arg.txt
arg1
arg2
arg3

cat arg.txt | args -n 1 ./echo.sh
cat arg.txt | args -n 2 ./echo.sh
cat arg.txt | args -n 3 ./echo.sh
#!/bin/bash arg1 arg2 arg3 #


cat args.txt | xargs -I {} ./ehco.sh -p {} -l
-p #!/bin/bash -l #
-p arg1 -l #
-p arg2 -l #
-p arg3 -l #
```

#### 4.5 结合find使用xargs

```bash
find . -name "*.txt" -print | xargs rm -f
find . -type f -name '*.txt' -print0 | xargs -0 rm -f
#xargs 提供了-0选项，允许将 NULL 作为分隔符，
#而 find 命令也心有灵犀地提供了对应的选项来产生以 NULL 字符作为分隔符的输出。
```

#### 4.6 统计源代码目录中所有c程序文件的行数

```bash
find . -type f -name "*.c" -print0 | xargs -0 wc -l
```

#### 4.7 结合stdin，巧妙运用while语句和子shell

```bash
cat files.txt | (while read arg; do cat $arg; done)
#等同于 cat files.txt | xargs -I cat {}


```

## tr

tr只能通过stdin接受输入无法通过命令行参数接受。tr [options] set1 set2

来自stdin的输入字符会按照位置从set1映射到set2，然后将输出写入stdout。set1和set2是字符类或字符数组。如果两个字符数组的长度不相等，那么set2会不断复制其最后一个字符，直至长度与set1相同。如果set2的长度大于set1，那么set2中超出set1的那部分字符则全部被忽略。

```bash
echo "hello who is this" | tr 'a-z' 'A-Z'
HELLO WHO IS THIS


echo 12345 | tr '0-9' '9876543210'
87654 #已加密
echo 87654 | tr '9876543210' '0-9' 
12345 #已解密
```

### 删除字符

```bash
cat file.txt | tr -d '[set1]'
echo "hello 123 world 456" | tr -d [0-9] 
hello  world 
```

### 字符组补集

tr -c [set1] [set2]

如果只给set1，那么tr会删除所有不在set1的字符。如果也给set2，tr会将不在set1中的字符转换成set2中的字符。如果使用-c选项，set1和set2必须都给出。如果-c和-d选项同时出现，你只能使用set1，其他所有字符都会被删除

```bash
echo hello 1 char 2 next 4 | tr -d -c '0-9\n'
124

echo hello 1 char 2 next 4 | tr -c '0-9\n' ' '
      1      2      4
```

### 用tr压缩字符

```tr -s [需要被压缩的一组字符]```

```bash
echo "gun    is   not     unix." | tr -s " "
gun is not unix.
```

删除多余的换行符

```bash
cat multi_blanks.txt | tr -s '\n'


cat sum.txt
1
2
3
4
5
cat sum.txt | echo $[ $(tr '\n' '+0' ) 0 ]
15

first 1
second 2
third 3
cat test.txt | tr -d 'a-z' | echo "total: $[$(tr ' ' '+')]"
total: 6
```

### 字符类

- alnum:字母和数字

- alpha:字母

- cntrl：控制

- digit：数字

- graph：图形字符

- lower：小写字母

- print：可打印字符

- punct：标点符号

- space：空白字符

- upper：大写字母

- xdigit：十六进制字符
