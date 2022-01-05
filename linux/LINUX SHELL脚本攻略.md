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
