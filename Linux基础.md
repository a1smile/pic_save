#   **目录结构**

![image-20241208142013596](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208142013596.png)

只有一个树形结构

根目录为‘/’，所有的文件都在它下面

注意：开头的/表示根目录

后面的/表示层级关系

###### **远程连接**

```
在虚拟机终端中输入：ifconfig
找到end33中的IP地址
在FinalShell中链接虚拟机

```

###### 命令与命令行的区别

学习Linux，本质上是学习在命令行下熟练使用Linux的各类命令。

```
命令行：即Linux终端（Terminal），是一种命令提示符页面。以纯“字符”的形式操作系统，可以使用各种字符化命令对系统发出操作指令。
```

```
命令：即Linuxc程序。一个命令就是一个Linux的程序。命令没有图形化页面，可以在命令行（终端中）提供字符化的反馈。
```

###### 命令的通用格式

```
conmmand [-options] [parameter]
·command:命令本身
·options:[可选，非必填]命令的一些选项，可以通过选项控制命令的行为细节
·parameter：[可选，非必填]命令的参数。多数用于命令的指向目标等
语法中的[]表示可选的意思
```

示例：

```
·ls -l/home/itheima     ls是命令本身，-l是选项，/home/itheima是参数
·意思是：以列表的形式，显示/home/itheima目录内的内容
·cp -r test1 test2     cp是命令本身，-r是选项，test1和test2是参数
·意思是：复制文件夹test1 成为test2
```

# 命令基础

## ls命令

### ls命令基本定义

ls命令的作用是在命令行中，以平铺的形式，展示当前工作目录（默认HOME目录）下的内容（文件或者文件夹），语法细节如下：

```
ls[-a -l -h][Linux路径]
```

-  -a  -l  -h 是**可选**的选项

- Linux路径是此命令**可选**的参数

当不使用选项和参数，直接使用ls命令本体，表示：以平铺形式，列出**当前工作目录**下的内容

![image-20241208143245339](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208143245339.png)

**注意：**

Linux系统的命令行终端，在启动的时候，默认会加载：

- 当前登录用户的HOME目录作为当前工作目录，所以ls命令列出的是HOME目录的内容

- HOME目录：每个Linux操作用户在Linux系统的**个人账户目录**（专属目录），路径在：/home/用户名

```
·如，图中的Linux用户是daihongsong，其HOME目录是：/home/daihongsong
·Windows系统和Linux系统。均设有用户的HOME目录
```

### ls命令的参数

```
ls[-a -l -h][Linux路径]
```

- 当ls不使用参数，表示列出：当前工作目录的内容，即用户的HOME目录。

- 当使用参数。ls命令的参数表示：指定一个Linux路径，列出指定路径的内容

如：

![image-20241208144746163](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208144746163.png)

通过ls / 列出根目录的内容

### ls命令的 -a选项

-a 选项，表示all的意思，即列出全部文件（包含隐藏的文件/文件夹）

![image-20241208144955840](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208144955840.png)

可以看到，ls -a 对比 ls 列出的内容更多。

- 图中以 . 开头的，表示是Linux系统的隐藏文件/文件夹（只要以 . 开头，就自动隐藏）

- 只有通过 -a 选项，才能看到这些隐藏的文件/文件夹

### ls命令的 -l 选项

-  -l 选项，表示：以列表（竖向排列）的形式展示内容，并展示更多信息

![image-20241208145714564](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208145714564.png)

#### ls选项可以混合使用

如： -a 和 -l 可以组合应用

写法： 

```
·ls -l-a
·ls -la
·ls -al
```

三种写法都是一样的

![image-20241208150011375](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208150011375.png)



#### ls选项和参数的组合使用

![image-20241208150105486](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208150105486.png)

### ls命令的 -h选项

- -h 表示以易于阅读的形式，列出文件大小，如K, M , G

- -h 选项必须要搭配 -l 一起使用 

![image-20241208150518225](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208150518225.png)

## 目录切换相关命令（cd / pwd）

### cd命令 切换工作目录

cd命令来自英文：Change Directory

**语法：**

```
cd[Linux路径]
```

- cd命令无需选项，只有参数，表示要切换到哪个目录下

- cd命令直接执行，不写参数，表示返回到用户的HOME目录

![image-20241208151406232](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208151406232.png)



### pwd命令查看当前工作目录

通过ls来验证当前的工作目录，其实是不恰当的

我们可以通过pwd命令，来查看当前所在的工作目录

pwd命令来自：Print Work Directory

**语法：**

```
pwd
```

*  pwd命令，无选项，无参数，直接输入pwd即可

![image-20241208151823741](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208151823741.png)

## 相对路径、绝对路径和特殊路径符

**比如进入Desktop**

**· cd Desktop     相对路径写法**

![image-20241208152751806](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208152751806.png)

**·cd/home/daihongsong/Desktop  绝对路径写法**

![image-20241208152905027](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208152905027.png)

### **相对路径**：

以**当前目录为起点**，描述路径的一种写法，路径描述**无需**以 / 开头

### **绝对路径**：

以**根目录为起点**，描述路径的一种写法，路径描述以 / 开头

### 特殊路径符

```
.    表示当前目录。比如cd ./Desktop表示切换到当前目录下的Desktop目录内，和cd Desktop效果一致
```

```
..   表示上一级目录，比如：cd .. 即可切换到上一级目录，cd ../..切换到上二级的目录
```

```
~    表示HOME目录，比如cd ~    即可切换到HOME目录或cd~/Desktop，切换到HOME内的Desktop目录
```

## mkdir命令创建目录（文件夹）

mkdir来自英文：**M**a**k**e **Dir**ectory

**语法：**

```
mkdir [-p] Linux路径
```

· 参数**必填** 。表示Linux路径，即要创建的文件夹的路径，相对路径和绝对路径均可

· -p选项**可选**，表示**自动创建不存在的父目录**，适用于创建**连续多层级**的目录

注意：创建文件夹需要修改权限，请确保操作均在HOME目录内，不要在HOME外操作，涉及到权限问题，HOME外无法成功

## 文件操作命令

### touch命令创建文件

可以通过touch命令创建文件

语法：

```
touch Linux路径
```

·touch命令无选项，参数必填，表示要创建的文件路径，相对、绝对、特殊路径符均可以使用

![image-20241208161510800](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208161510800.png)



#### 文件夹和文件的分辨

![image-20241208161628202](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208161628202.png)

第一列中   d   开头的是文件夹

-开头是文件

### cat命令查看文件内容

语法：

```
cat Linux路径
```

-  cat没有选项，只有必填参数，参数表示：被查看的文件路径，相对、绝对、特殊路径符都可以使用

![image-20241208162405365](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208162405365.png)

### more命令查看文件内容

同cat的区别：

- cat是直接将内容全部显示出来
- more支持翻页，如果文件内容过多，可以一页页的展示

**语法：**

```
more Linux路径
```

- 没有选项，只有必填参数：被查看的文件路径，相对、绝对、特殊路径符都可以使用

- 在查看的过程中，通过**空格**翻页
- 通过 q 退出查看

### cp命令 复制文件/文件夹

cp命令可以用于复制文件、文件夹

cp命令来自于：**c**o**p**y

语法：

```
cp [-r] 参数1 参数2
```

- -r选项，可选，用于复制文件夹使用，表示递归
- 参数1，Linux路径，表示被复制的文件/文件夹
- 参数2，Linux路径，表示要复制去的地方

### mv命令移动文件/文件夹

mv命令可以用于移动文件/文件夹

mv命令来自：**m**o**v**e

语法：

```
mv 参数1  参数2
```

- 参数1，Linux路径，表示被移动的文件或文件夹
- 参数2，Linux路径，表示要移动去的地方，如果目标不存在，则进行改名，**确保目标存在**

### rm删除文件/文件夹

rm命令可以用于删除文件/文件夹

rm命令来自：**r**e**m**ove

**语法：**

```
rm [-r -f] 参数1 参数2 ...... 参数N 
```

- 同cp命令一样，-r 选项用于删除文件夹
- -f  表示force，强制删除（不会弹出提示确认信息）
  - 普通用户删除内容不会弹出提示，只有root管理员用户删除内容会有提示
  - 使用一般用户用不到-f 选项
- 参数1、 参数2、 ...... 、参数N   表示要删除的文件/文件夹路径，按照空格隔开

#### rm命令-通配符

rm命令支持通配符*，用来做**模糊匹配**

- 符号 * 表示通配符，即匹配任意内容（包含空），示例：
- test*，表示匹配任何**以test开头**的内容
- *test，表示匹配任何**以test结尾**的内容
- *test *，表示匹配任何**包含test**的内容 

#### 强制删除 -f选项

- 可以通过su - root，并输入密码（和普通用户默认一样）临时切换到root用户体验
- 通过输入 exit 命令，返回普通用户

rm是一个危险的命令，特别是在处于root（超级管理员）用户的时候，要谨慎使用。

**如下命令，千万不要在root管理员用户下执行：**

**rm -rf/**

**rm -rf/***

**效果等同于在Windows上执行C盘格式化**

### 查找命令（which , find）

#### which命令 查命令

Linux命令的本体就是一个个的二进制可执行程序

和Windows系统中的.exe文件是一个意思

我们可以通过which命令，查看所使用的一系列命令的程序文件存放的位置

语法：

```
which 要查找的命令
```

#### find命令 查文件

通过find命令去搜索指定的文件

语法：

```
find 起始路径  -name “被查找文件名”
```

为了确保拥有最大的权限，可以在整个系统完成搜索，可以临时切换到root用户来获得管理员权限

##### 模糊查询

根据语法 ： **find 起始路径 -name “被查找文件名”**

被查找文件名，支持使用通配符*来做模糊查询

- 符号 * 表示通配符，即匹配任意内容（包含空），示例：
- test*，表示匹配任何**以test开头**的内容
- *test，表示匹配任何**以test结尾**的内容
- *test *，表示匹配任何**包含test**的内容 

基于通配符的含义，可以结合find命令做文件的模糊查询

##### find命令-按文件大小查找文件

语法：

```
find 起始路径 -size +|-n[kMG]
```

- +、- 表示大于和小于
- n表示大小数字
- kMG表示大小单位，k（小写字母）表示kb，M表示MB，G表示GB

示例：

```
·查找小于10kB的文件：find/ -size -10k
·查找大于100MB的文件：find/ -size +100M
·查找大于1GB的文件：find/ -size +1G
```

ctrl + c 强制停止搜索

### grep、wc和管道符

#### grep命令

可以通过grep命令，从文件中通过关键字过滤文件行

语法：

```
grep [-n] 关键字 文件路径
```

- 选项 -n ，可选，表示在结果中显示匹配的行的行号
- 参数、关键字，必填，表示过滤的关键字，带有空格或其他特殊符号，建议使用“ ”将关键字包围起来
- 参数、文件路径，必填，表示要过滤内容的文件路径，**可作为内容输入端口**

#### wc命令 做数量统计

可以通过wc命令统计文件的行数、单词数量等

语法：

```
wc[-c -m -l -w] 文件路径
```

- 选项，-c ，统计bytes数量
- 选项，-m，统计字符数量
- 选项，-l ， 统计行数
- 选项，-w，统计单词数量
- 参数，文件路径，被统计的文件，**可以作为内容输入端口**（可以做管道符的输入）

```
不加选项时，输出：
行数  单词数 字节数 文件路径
```

#### 管道符   |

含义：将管道符左边命令的结果，作为右边命令的输入

![image-20241208214214600](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208214214600.png)

如上图：

- cat test.txt的输出结果（文件内容）
- 作为右边grep命令的输入（被过滤文件）

管道符可以进行嵌套

![image-20241208215252494](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208215252494.png)

## echo、tail和重定向符

### echo命令

可以使用echo命令在命令行内输出指定内容

类似于编程中的print语句

语法：

```
echo 输出的内容
```

- 无需选项，只有一个参数，表示要输出的内容，复杂内容可以用“ ”包围
- 在终端上显示：Hello Linux

![image-20241208215847459](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208215847459.png)

- - 带有空格或 \ 等特殊符号，建议使用双引号包围
  - 因为不包围的话，空格后容易被识别为参数2，尽管echo不受影响，但要养成习惯

![image-20241208220245400](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208220245400.png)

### 反引号 `

如下的命令：echo pwd

![image-20241208220511294](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208220511294.png)

本意是想，输出当前的工作路径，但是pwd被作为普通字符输出了。



我们可以通过命令用反引号（通常也称之为飘号）` 将其包围

被 ` 包围的内容，会被作为命令执行，而非普通字符

![image-20241208220702778](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241208220702778.png)

### 重定向符 >和>>

- ">"。将左侧命令的结果，**覆盖**写入到符号右侧指定的文件中
- “>>”，将左侧命令的结果，**追加**写入到符号右侧指定的文件中

演示：

* echo "Hello Linux" > itheima.txt

![image-20241209000029623](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241209000029623.png)

* echo"Hello itheima" > itheima.txt ，再次执行。覆盖新内容

![image-20241209000127248](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241209000127248.png)

* echo"Hello itcast" >> itheima.txt，再次执行，使用>>追加新内容

![image-20241209000214393](C:\Users\D1519\AppData\Roaming\Typora\typora-user-images\image-20241209000214393.png)

### tail命令

使用tail命令，可以查看文件尾部内容，跟踪文件的最新更改

语法：

```
tail [-f -num] Linux路径
```

- 参数，Linux路径，表示被跟踪的文件路径
- 选项，-f，表示持续跟踪
- 选项，-num（填具体查看行数），表示查看尾部多少行，不填默认10行