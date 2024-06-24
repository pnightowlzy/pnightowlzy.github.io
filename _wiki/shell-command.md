---
layout: wiki
title: Shell Command
cate1: Linux
cate2: Shell
description: Shell Command相关操作记录
keywords: Shell Command
---
定义：
- Crtl = C

# 简单命令
`top` 查看运行资源情况
`history` 查看历史输入的 `bash` 命令
`C + r` 搜索历史命令， 可以使用 `history | grep (你要查找的命令)`

# grep 
`grep` 是一种强大的文本搜索工具，它能够使用正则表达式搜索文本，并把匹配的行打印出来。以下是一些基本的 `grep` 命令相关操作：

1. 在文件中搜索字符串：
   `grep "search_string" filename`

2. 在多个文件中搜索：
   `grep "search_string" file1 file2 file3`

3. 在目录中递归搜索：
   `grep -R "search_string" directory_path`

4. 忽略大小写进行搜索：
   `grep -i "search_string" filename`

5. 只打印匹配行的数量：
   `grep -c "search_string" filename`

6. 搜索不匹配的行：
   `grep -v "search_string" filename`

7. 使用正则表达式进行搜索：
   `grep -E "[a-z]+" filename`

这里 `search_string` 是你要搜索的字符串。

以上只是 `grep` 命令的一些基本用法，`grep` 还有许多其他的高级用法和选项，可以参阅 `man grep` 来查看更详细的用法。

# sed
sed是流编辑器（stream editor）的缩写。它是文本处理中不可或缺的工具，能够配合正则表达式使用，功能不同凡响。sed命令众所周知的一个用法是进行文本替换。这则攻略包括了sed命令大部分的常用技术。
文本替换

(1) sed可以替换给定文本中的字符串
```bash
sed 's/pattern/replace_string/' file

cat linux.txt 
linux    aaabbcc
linuxxx 
unix

cat linux.txt|sed 's/linux/mac/'
mac    aaabbcc
macxx 
unix
```
(2) 源文件替换

在默认情况下，sed只会打印替换后的文本。如果需要在替换的同时保存更改，可以使用-i选项，可以将替换结果应用于原文件。很多用户在进行替换之后，会借助重定向来保存文件：
```bash
sed -i 's/linux/mac/' linux.txt 
cat linux.txt 
mac    aaabbcc
macxx 
unix
```
(3) 替换所有内容

之前看到的sed命令会将每一行中第一处符合模式的内容替换掉。但是如果要替换所有内容，我们需要在命令尾部加上参数g，其方法如下：`$ sed 's/pattern/replace_string/g' file`
后缀/g意味着sed会替换每一处匹配。但是有时候我们只需要从第n处匹配开始替换。对此，可以使用/Ng选项。
```bash
$ echo thisthisthisthis | sed 's/this/THIS/2g'
thisTHISTHISTHIS
$ echo thisthisthisthis | sed 's/this/THIS/3g'
thisthisTHISTHIS
$ echo thisthisthisthis | sed 's/this/THIS/4g'
thisthisthisTHIS

# 字符/在sed中被作为定界符使用。我们可以像下面一样使用任意的定界符：

sed 's:text:replace:g'
sed 's|text|replace|g'
# 当定界符出现在样式内部时，我们必须用前缀\对它进行转义：
sed 's|te\|xt|replace|g'
# \|是一个出现在样式内部并经过转义的定界符。
```
(4) 空白行剔除，空白行可以用正则表达式 ^$ 进行匹配：
```bash
$ sed '/^$/d' file
# /pattern/d会移除匹配样式的行。


cat file.txt                                                                                                                                                                                    1 ↵
1



23 


4
5


sed '/^$/d' file.txt
1
23 
4
5

# 直接在文件中进行修改
sed '/^$/d' -i file.txt
cat file.txt 
1
23 
4
5
```
(5) 已匹配字符串

在sed中，我们可以用 &标记匹配样式的字符串，这样就能够在替换字符串时使用已匹配的内容。
```bash
$ echo this is an example | sed 's/\w\+/[&]/g'
[this] [is] [an] [example]
# 正则表达式 \w\+ 匹配每一个单词，然后我们用[&]替换它。& 对应于之前所匹配到的单词。
```
(6) 子串匹配标记（\1）

& 代表匹配给定样式的字符串。但我们也可以匹配给定样式的其中一部分。来看看具体的做法。
```bash
$ echo this is digit 7 in a number | sed 's/digit \([0-9]\)/\1/'
this is 7 in a number
```
这条命令将digit 7替换为7。样式中匹配到的子串是7。(pattern)用于匹配子串。模式被包括在使用斜线转义过的()中。对于匹配到的第一个子串，其对应的标记是 \1，匹配到的第二个子串是 \2，往后依次类推。下面的示例中包含了多个匹配：
```bash
$ echo seven EIGHT | sed 's/\([a-z]\+\) \([A-Z]\+\)/\2 \1/'
EIGHT seven
```
([a-z]+)匹配第一个单词，([A-Z]+)匹配第二个单词。\1和\2用来引用它们。这种引用被称为向后引用（back reference）。在替换部分，它们的次序被更改为\2 \1，因此结果就呈现出逆序的形式。
```bash
echo seven SEVEN  | sed 's/\([a-z]\+\) \([A-Z]\+\)/\2 to \1/' 
SEVEN to seven
```
(7) 引用
```bash
$ text=hello
$ echo hello world | sed "s/$text/HELLO/"
HELLO world
```



