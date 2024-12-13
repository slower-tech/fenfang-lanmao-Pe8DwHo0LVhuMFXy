
在使用`Python`处理文件路径时，强烈建议使用`pathlib`。


`pathlib`以面向对象的方式处理文件路径，既避免了很多陷阱，也能使执行许多路径的相关操作变得更容易。


本篇总结了常用的使用`pathlib`进行文件路径处理的方法。


# 1\. 常用操作


首先介绍如何使用`pathlib`来完成一些常规的文件路径相关操作。


## 1\.1\. 构造路径


构建路径对象，只需要将文件或文件夹路径的字符串传给`Path`即可。



```
from pathlib import Path

fp = "D:\\temp\\pathlib"
path = Path(fp)
path

# path 对象
# WindowsPath('D:/temp/pathlib')

```

构造路径对象之后，`Path`会自动判断出是`windows`还是`linux`下的路径。


## 1\.2\. 拼接和拆分路径


用字符串来拼接和拆分路径时，最麻烦的就是不同系统中路径分隔符（`\ 和 /`）的处理。


使用`Path`对象，能够避免此困扰。



```
new_path = path.joinpath("abc")
new_path
# WindowsPath('D:/temp/pathlib/abc')

new_path = Path(fp, "test.py")
new_path
# WindowsPath('D:/temp/pathlib/test.py')

```

使用`joinpath`或者直接创建`Path`对象时拼接路径，不需要指定路径分隔符。


使用`Path`拆分路径也方便，它提供了多个属性来获取文件信息。



```
my_path = Path(fp, "program.py")
my_path
# WindowsPath('D:/temp/pathlib/program.py')

# 文件完整名
my_path.name
# 'program.py'

# 文件目录
my_path.parent
# WindowsPath('D:/temp/pathlib')

# 文件名（不带后缀）
my_path.stem
# 'program'

# 文件后缀名
my_path.suffix
# '.py'

# 修改文件后缀
my_path.with_suffix(".go")
# WindowsPath('D:/temp/pathlib/program.go')

```

## 1\.3\. 相对路径和绝对路径


相对路径转换为绝对路径，推荐使用`Path`对象的`resolve`方法。



```
path = Path("main.py")
path
# WindowsPath('main.py')

# 转换为绝对路径
path.resolve()
# WindowsPath('D:/projects/python/samples/main.py')

```

## 1\.4\. 遍历目录


遍历目录也是常用的文件路径操作。



```
fp = "D:\\temp\\pathlib\\a"
path = Path(fp)

for f in path.glob("*.txt"):
    print(f)

# D:\temp\pathlib\a\1.txt
# D:\temp\pathlib\a\2.txt
# D:\temp\pathlib\a\3.txt

```

`glob`函数是只遍历目录下的文件，如果要遍历子目录中的文件，使用`rglob`函数。



```
for f in path.rglob("*.txt"):
    print(f)

# D:\temp\pathlib\a\1.txt
# D:\temp\pathlib\a\2.txt
# D:\temp\pathlib\a\3.txt
# D:\temp\pathlib\a\sub_a\sub_1.txt

```

## 1\.5\. 读写文件


传统的读写文件方式，一般都是两个步骤：先通过open函数打开文件，再进行读或者写。



```
# 写入
with open("d:\\readme.txt", "w") as f:
    f.write("abcdefg")

# 读取
with open("d:\\readme.txt", "r") as f:
    content = f.read()
    print(content)
    # abcdefg

```

使用`Path`对象，读写操作更加简单，代码也更清晰。



```
fp = "d:\\readme.txt"
path = Path(fp)
path.write_text("uvwxyz")

content = path.read_text()
print(content)
# uvwxyz

```

# 2\. 更方便的操作


除了上面的常用操作，对于下面这些略微复杂文件路径的操作，


使用`Path`也能更容易的完成。


## 2\.1\. 检查文件或目录是否存在



```
fp = "D:\\temp\\pathlib\\a"
path = Path(fp)

path.is_dir() # True
path.is_file() # False
path.exists() # True

```

## 2\.2\. 创建目录


创建目录使用`Path`对象可以帮助我们自动处理异常情况。



```
path = Path("D:\\temp\\a\\b\\c\\d")
path.mkdir(exist_ok=True, parents=True)

```

`exist_ok`和`parents`参数为了创建文件夹时省了很多判断。


`exist_ok=True`表示如果**文件夹d**存在就不创建，也不报错，反之会报错。


`parents=True`表示**文件夹d**的上层的各级文件夹如果不存在就自动创建，反之如果**文件夹d**的上层有不存在的文件夹则报错。


## 2\.3\. 路径自动规范化


使用`Path`来操作路径，不用过于关心不同操作系统的路径分割符问题。


在`windows`系统中，也可以使用`linux`的路径分割符，比如，下面两种方式都可以正常运行。



```
fp = "D:\\temp\\pathlib\\a"
path = Path(fp)

fp = "D:/temp/pathlib/a"
path = Path(fp)

```

# 3\. 与os.path对比


`pathlib`主要就是为了取代`os.path`，它们之间的对比整理如下：




| **路径操作** | \*\*pathlib \*\* | **os.path** |
| --- | --- | --- |
| 读取所有文件内容 | `path.read_text()` | `open(path).read()` |
| 获取绝对文件路径 | `path.resolve()` | `os.path.abspath(path)` |
| 获取文件名 | `path.name` | `os.path.basename(path)` |
| 获取父目录 | `path.parent` | `os.path.dirname(path)` |
| 获取文件扩展名 | `path.suffix` | `os.path.splitext(path)[1]` |
| 文件名（不包含扩展名） | `path.stem` | `os.path.splitext(path)[0]` |
| 相对路径 | `path.relative_to(parent)` | `os.path.relpath(path, parent)` |
| 验证路径是否为文件 | `path.is_file()` | `os.path.isfile(path)` |
| 验证路径是否为目录 | `path.is_dir()` | `os.path.isdir(path)` |
| 创建目录 | `path.mkdir(parents=True)` | `os.makedirs(path)` |
| 获取当前目录 | `pathlib.Path.cwd()` | `os.getcwd()` |
| 获取主目录 | `pathlib.Path.home()` | `os.path.expanduser("~")` |
| 按模式查找文件 | `path.glob(pattern)` | `glob.iglob(pattern)` |
| 递归查找文件 | `path.rglob(pattern)` | `glob.iglob(pattern, recursive=True)` |
| 规格化路径分隔符 | `pathlib.Path(name)` | `os.path.normpath(name)` |
| 拼接路径 | `Path(paraent, name)` | `os.path.join(parent, name)` |
| 获取文件大小 | `path.stat().st_size` | `os.path.getsize(path)` |
| 遍历文件树 | `path.walk()` | `os.walk()` |
| 将文件重定向到新路径 | `path.rename(target)` | `os.rename(path, target)` |
| 删除文件 | `path.unlink()` | `os.remove(path)` |


对比两种方式，就能体会pathlib的改进带来的好处。


 本博客参考[milou加速器](https://xinminxuehui.org)。转载请注明出处！
