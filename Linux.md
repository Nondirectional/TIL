# Linux

## lsof

`lsof` (list open files) 是一个在 Linux/Unix 系统中非常强大的工具，用于显示当前系统上所有打开的文件。在 Linux 中，“一切皆文件”，这包括普通文件、目录、网络套接字（sockets）、管道、设备文件等。因此，`lsof` 不仅可以查看进程打开的文件，还可以查看网络连接和硬件设备。

### `lsof` 命令的基本用法和输出说明

直接运行 `lsof` 命令会显示系统上所有进程打开的所有文件，输出信息量巨大。通常，我们需要结合选项来过滤和查找特定的信息。

**基本格式：**

```bash
lsof [选项] [文件]
```

**输出字段说明：**

当 `lsof` 命令运行时，它会输出多列信息，每行代表一个打开的文件：

- **COMMAND**：进程的名称。

- **PID**：进程的ID。

- **TID**：线程ID（如果可用）。

- **USER**：进程的拥有者。

- FD

  ：文件描述符 (File Descriptor)。这表示文件是如何被使用的。常见的有：

  - `cwd`：当前工作目录
  - `txt`：程序的可执行文本文件
  - `mem`：内存映射文件
  - `mmap`：内存映射设备
  - `数字`：实际的文件描述符（0：标准输入，1：标准输出，2：标准错误）。后面可能会跟 `r` (只读), `w` (只写), `u` (读写)。

- TYPE

  ：文件的类型。常见的有：

  - `REG`：普通文件
  - `DIR`：目录
  - `CHR`：字符设备
  - `BLK`：块设备
  - `FIFO`：命名管道
  - `SOCK`：UNIX 域套接字
  - `IPv4`：IPv4 套接字
  - `IPv6`：IPv6 套接字

- **DEVICE**：设备号（主设备号,次设备号）。

- **SIZE/OFF**：文件的大小或文件描述符的偏移量。

- **NODE**：文件在文件系统中的索引节点号 (inode)。对于网络连接，它可能是节点的描述。

- **NAME**：文件的名称或网络连接的详细信息（如 IP 地址、端口号）。

### `lsof` 常用选项和示例

以下是一些 `lsof` 命令的常用选项和示例：

1. **列出所有打开的文件（通常需要root权限）：**

```bash
sudo lsof
```

   输出可能非常长，可以结合 `less` 或 `more` 进行分页查看：

```bash
sudo lsof | less
```

2. **列出指定用户打开的文件：**

```bash
lsof -u 用户名
# 示例：
lsof -u root
lsof -u gtwang
```

   排除某个用户：

```bash
lsof -u ^root  # 列出除了root用户之外所有用户打开的文件
```

3. **列出指定进程打开的文件：**

   - 按PID查找：

```bash
lsof -p PID
 # 示例：
 lsof -p 1234
```

   - 按进程名查找：

```bash
lsof -c 进程名
 # 示例：
lsof -c httpd
 lsof -c sshd
```

 可以指定多个进程名：

```bash
lsof -c java -c httpd
```

4. **列出打开特定文件或目录的进程：**

```bash
lsof /path/to/file
   # 示例：
   lsof /var/log/messages
   lsof /dev/null
```

   对于目录，默认只会显示该目录本身被打开的情况。如果要递归地显示目录及其子目录中的所有打开文件，使用 `+D` 选项：

```bash
lsof +D /var/log
```

5. **列出网络连接（套接字）：**


```bash
   lsof -i
```

   - 列出所有TCP连接：

```bash
 lsof -i tcp
```

   - 列出所有UDP连接：

```bash
 lsof -i udp
```

   - 列出所有监听的端口：

```bash
 lsof -i -s tcp:listen
```

   - 列出指定端口的连接：

```bash
 lsof -i :端口号
 # 示例：
 lsof -i :80 # 查看80端口的连接
 lsof -i :22 # 查看22端口的连接
```

   - 列出指定IP地址和端口的连接：

```bash
 lsof -i @IP地址:端口号
 # 示例：
 lsof -i @192.168.1.100:80
```

   - 列出某个进程打开的特定端口：

```bash
 lsof -p PID -i :端口号
```

   - 只显示IPv4或IPv6连接：

```bash
 lsof -i4  # 只显示IPv4
 lsof -i6  # 只显示IPv6
```

6. 结合多个条件（AND 运算）：

   lsof 默认多个条件是 OR 关系，可以使用 -a 选项使其变为 AND 关系。

   

```bash
   # 列出用户'root'且进程名为'java'打开的文件
   lsof -a -u root -c java
   # 列出用户'john'且网络端口为22的文件
   lsof -a -u john -i :22
```

7. **只显示PID：**

   

```bash
   lsof -t /path/to/file  # 只显示打开该文件的进程PID
   lsof -t -i :80         # 只显示监听80端口的进程PID
```

   这个选项常用于与其他命令结合，例如 `kill`：

   

```bash
   kill -9 $(lsof -t -i :8080) # 杀死所有使用8080端口的进程
```

8. **根据文件描述符查找：**

   

```bash
   lsof -d 文件描述符
   # 示例：
   lsof -d cwd   # 列出当前工作目录的文件
   lsof -d txt   # 列出程序文本文件
   lsof -d 0,1,2 # 列出标准输入、标准输出、标准错误
```

9. 提高性能（不解析主机名和端口服务名）：

   当输出量非常大时，DNS解析和端口服务名解析可能会很慢。使用 -n 和 -P 选项可以加快速度：

   

```bash
   lsof -n -P
```

   - `-n`：不将网络号转换为hostname。
   - `-P`：不将端口号转换为端口服务名称。

10. 重复执行 lsof：

使用 -r 选项可以每隔一段时间重复执行 lsof 命令，类似于 watch 命令。


 ```bash
lsof -r 5  # 每5秒刷新一次
 ```

### 常见应用场景

- 查找哪个进程占用了某个文件或端口：

  当一个文件无法删除，或者一个端口被占用导致服务无法启动时，lsof 是一个非常有用的工具。

  - 找出占用 `/mnt/data/my_file.txt`的进程：

 ```bash
lsof /mnt/data/my_file.txt
 ```

  - 找出占用 80 端口的进程：


 ```bash
sudo lsof -i :80
 ```

- 查看进程打开了哪些文件：

  了解特定进程的 I/O 活动。


```bash
lsof -p PID
```

- 恢复被删除但仍在使用的文件：

  如果一个文件被删除，但某个进程仍然打开着它，那么这个文件实际上并没有被完全释放，仍然可以通过特殊的文件描述符路径访问。


```bash
  # 假设 /var/log/syslog 被意外删除了，但rsyslogd进程仍在写入
  sudo lsof | grep deleted | grep syslog
  # 找到类似这样的输出：
  # rsyslogd  1234  root    5w   REG   253,0  12345  123456 /var/log/syslog (deleted)
  # 其中的 5w 是文件描述符，253,0 是设备号，123456 是inode
  # 可以通过 /proc/PID/fd/文件描述符 恢复：
  cp /proc/1234/fd/5 /var/log/syslog
```

- 监控网络连接：

  查看当前系统的网络连接状态和活跃的会话。


```bash
lsof -i tcp -s tcp:estab # 查看所有已建立的TCP连接
```

##  awk

`awk` 是 Linux/Unix 环境下一种强大的文本处理工具。它不仅仅是一个命令，更像一门编程语言，用于在文件中查找模式、对行进行操作并生成报告。`awk` 的名称来源于其三位开发者 Alfred **A**ho、Peter **W**einberger 和 Brian **K**ernighan 的姓氏首字母。

本教程将从基础概念讲起，逐步深入到 `awk` 的高级用法。

### 1. `awk` 的基本工作原理

`awk` 每次读取一行输入，然后将其与指定的模式进行比较。如果行与模式匹配，`awk` 就会执行相应的动作。如果行不与任何模式匹配，`awk` 就会跳过该行。所有行处理完毕后，`awk` 就会退出。

其基本语法结构为：

```bash
awk 'pattern { action }' filename
```

其中：
* `pattern`：是一个正则表达式，用于匹配输入行。
* `action`：是一系列 `awk` 语句，当 `pattern` 匹配时执行。
* `filename`：是输入文件。如果没有指定文件，`awk` 会从标准输入读取。

### 2. `awk` 的内置变量

`awk` 提供了许多内置变量，它们在处理文本时非常有用：

* `$0`：表示当前行的所有内容。
* `$1`, `$2`, `$3`, ...：表示当前行的第一个字段、第二个字段、第三个字段，依此类推。默认情况下，字段之间用空格或制表符分隔。
* `NR`：表示当前处理的行号（Number of Record）。
* `NF`：表示当前行的字段数量（Number of Fields）。
* `FS`：输入字段分隔符（Field Separator），默认为空格或制表符。
* `OFS`：输出字段分隔符（Output Field Separator），默认为空格。
* `RS`：输入记录分隔符（Record Separator），默认为换行符。
* `ORS`：输出记录分隔符（Output Record Separator），默认为换行符。

### 3. `awk` 的基本用法示例

为了更好地理解 `awk`，我们创建一个名为 `data.txt` 的示例文件：

```
Apple Red 10
Banana Yellow 5
Orange Orange 8
Grape Purple 12
```

#### 3.1. 打印整行

```bash
awk '{print}' data.txt
```
这会打印 `data.txt` 中的所有行。`{print}` 是一个默认的动作，如果没有指定模式，它将对每一行执行。

#### 3.2. 打印特定字段

```bash
awk '{print $1, $3}' data.txt
```
输出：
```
Apple 10
Banana 5
Orange 8
Grape 12
```
这里我们打印了每一行的第一个字段和第三个字段。注意 `$1, $3` 之间用逗号隔开，这会导致输出时字段之间默认使用 `OFS`（即空格）分隔。

#### 3.3. 自定义输出分隔符

```bash
awk '{print $1 "-" $3}' data.txt
```
输出：
```
Apple-10
Banana-5
Orange-8
Grape-12
```
这里我们自定义了输出字段之间的分隔符为 `-`。

#### 3.4. 使用 `BEGIN` 和 `END` 块

`BEGIN` 块在 `awk` 开始处理任何输入行之前执行一次。`END` 块在 `awk` 处理完所有输入行之后执行一次。

```bash
awk 'BEGIN {print "--- Inventory Report ---"} {print $1, $3} END {print "--- End of Report ---"}' data.txt
```
输出：
```
--- Inventory Report ---
Apple 10
Banana 5
Orange 8
Grape 12
--- End of Report ---
```

#### 3.5. 统计行数

```bash
awk 'END {print NR}' data.txt
```
输出：
```
4
```
`NR` 在 `END` 块中使用时，表示处理的总行数。

#### 3.6. 统计字段数

```bash
awk '{print "Line " NR ": " NF " fields"}' data.txt
```
输出：
```
Line 1: 3 fields
Line 2: 3 fields
Line 3: 3 fields
Line 4: 3 fields
```

### 4. 模式匹配

`awk` 的强大之处在于其模式匹配功能。模式可以是正则表达式、比较表达式、范围模式等。

#### 4.1. 基于正则表达式的模式匹配

匹配包含 "Apple" 的行：

```bash
awk '/Apple/ {print}' data.txt
```
输出：
```
Apple Red 10
```

匹配以 "O" 开头的行：

```bash
awk '/^O/ {print}' data.txt
```
输出：
```
Orange Orange 8
```

匹配以数字结尾的行：

```bash
awk '/[0-9]$/ {print}' data.txt
```
这会打印所有行，因为所有行都以数字结尾。

#### 4.2. 基于比较表达式的模式匹配

打印第三个字段值大于 7 的行：

```bash
awk '$3 > 7 {print}' data.txt
```
输出：
```
Apple Red 10
Orange Orange 8
Grape Purple 12
```

打印第一个字段是 "Banana" 的行：

```bash
awk '$1 == "Banana" {print}' data.txt
```
输出：
```
Banana Yellow 5
```

#### 4.3. 组合模式

使用 `&&` (逻辑与) 和 `||` (逻辑或) 组合模式：

打印第一个字段是 "Apple" 并且第三个字段大于 5 的行：

```bash
awk '$1 == "Apple" && $3 > 5 {print}' data.txt
```
输出：
```
Apple Red 10
```

打印第一个字段是 "Apple" 或者第三个字段是 "8" 的行：

```bash
awk '$1 == "Apple" || $3 == 8 {print}' data.txt
```
输出：
```
Apple Red 10
Orange Orange 8
```

#### 4.4. 范围模式

范围模式由两个模式组成，`awk` 会打印从第一个模式匹配的行到第二个模式匹配的行之间的所有行（包括这两行）。

```bash
awk '/Banana/,/Grape/ {print}' data.txt
```
输出：
```
Banana Yellow 5
Orange Orange 8
Grape Purple 12
```

### 5. 设置字段分隔符 `FS`

默认情况下，`awk` 使用空格或制表符作为字段分隔符。您可以使用 `-F` 选项或在 `BEGIN` 块中设置 `FS` 变量来指定不同的分隔符。

创建一个 `grades.csv` 文件：
```csv
John,Math,90
Mary,Science,85
Peter,English,78
```

#### 5.1. 使用 `-F` 选项

```bash
awk -F',' '{print $1, $3}' grades.csv
```
输出：
```
John 90
Mary 85
Peter 78
```

#### 5.2. 在 `BEGIN` 块中设置 `FS`

```bash
awk 'BEGIN {FS=","} {print $1, $3}' grades.csv
```
效果同上。

### 6. `awk` 编程特性

`awk` 包含了一些基本的编程特性，使其功能更加强大。

#### 6.1. 变量

`awk` 允许您定义自己的变量。

计算总和：
```bash
awk '{sum += $3} END {print "Total quantity:", sum}' data.txt
```
输出：
```
Total quantity: 35
```
这里 `sum` 是一个用户定义的变量。

#### 6.2. 条件语句 (`if/else`)

```bash
awk '{
    if ($3 > 7) {
        print $1, "is large"
    } else {
        print $1, "is small"
    }
}' data.txt
```
输出：
```
Apple is large
Banana is small
Orange is large
Grape is large
```

#### 6.3. 循环 (`for`, `while`)

虽然 `awk` 对每行自动循环，但在 `action` 块内部也可以使用循环。

```bash
awk '{
    for (i = 1; i <= NF; i++) {
        print "Field " i ": " $i
    }
    print "---"
}' data.txt
```
输出：
```
Field 1: Apple
Field 2: Red
Field 3: 10
---
Field 1: Banana
Field 2: Yellow
Field 3: 5
---
Field 1: Orange
Field 2: Orange
Field 3: 8
---
Field 1: Grape
Field 2: Purple
Field 3: 12
---
```

#### 6.4. 数组

`awk` 支持关联数组（也称为哈希表或字典）。

统计每种颜色的数量：

```bash
awk '{count[$2]++} END {for (color in count) print color ": " count[color]}' data.txt
```
输出（顺序可能不同）：
```
Red: 1
Yellow: 1
Orange: 1
Purple: 1
```

### 7. 将 `awk` 命令写入文件

如果 `awk` 脚本比较复杂，可以将其写入一个文件，然后使用 `-f` 选项执行：

创建一个名为 `script.awk` 的文件：
```awk
#!/usr/bin/awk -f

BEGIN {
    print "--- Processed Data ---"
    FS=" " # 确保字段分隔符正确
}

{
    if ($3 >= 10) {
        print "High stock for", $1, " (Quantity:", $3 ")"
    } else if ($3 < 10 && $3 > 5) {
        print "Medium stock for", $1, " (Quantity:", $3 ")"
    } else {
        print "Low stock for", $1, " (Quantity:", $3 ")"
    }
}

END {
    print "--- End of Processing ---"
}
```

然后执行：

```bash
awk -f script.awk data.txt
```
输出：
```
--- Processed Data ---
High stock for Apple  (Quantity: 10)
Low stock for Banana  (Quantity: 5)
Medium stock for Orange  (Quantity: 8)
High stock for Grape  (Quantity: 12)
--- End of Processing ---
```

### 8. 结合管道使用 `awk`

`awk` 经常与其他命令结合使用，通过管道（`|`）传递数据。

例如，查看当前系统用户及其 Shell 信息：

```bash
cat /etc/passwd | awk -F':' '{print "User:", $1, "\tShell:", $7}'
```
（输出会显示很多用户，这里只做示例）
```
User: root 	Shell: /bin/bash
User: daemon 	Shell: /usr/sbin/nologin
...
```
