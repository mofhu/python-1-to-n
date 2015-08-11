# argparse - Python standard library 


## Outline

- why argparse? 为什么要用?
- how to begin? 怎么上手?

## why argparse? 为何要学 argparse 模块?

### 为什么要用参数?

对于一个能完成特定功能的程序, 直接用命令行调用最为简单(相对于进入之后大量的用户 i/o 来说). 因此通过一串参数表明调用程序的功能, 也是一种最常用的模式(几个月前的自己还觉得一堆命令参数很麻烦, 现在就已经入坑了XD). 

如同一个好的函数通常用参数来接受输入, 一个好的程序也应当通过命令行参数来接受输入. 这样的交互优点可能有: 更易于使用和自动化调用; 更容易控制用户的输入输出.

具体的例子: 在 GitBook auto summary 和 orglab/mahjong 早期开发中, 均使用了 rawinput/input 函数在程序内接受用户输入. 如果想要进一步自动化操作(如定期自动导出), 就需要设法在程序内模拟用户输入. 这在技术上麻烦了很多, 不如直接在命令行接受参数作为输入. 因此, 后期都增加了相应的功能.

### 怎么接受参数比较好?
对于这两个程序, 因结构比较简单, 参数数量也少. 直接手动写接受参数也可行(初期直接用 sys.argv unpack实现). 但有以下问题:

  - 可能自己的实现中有 bug
  - 长期写程序时复用性如何?

浏览 python doc 时看到 argparse 的入门教程(tutorial)[1]. 于是用最简方法应用到自己的 Gitbook auto summary 仓库之中.

## how to begin? 怎么上手?

The `argparse` module makes it easy to write user-friendly command-line interfaces. The program defines what arguments it requires, and argparse will figure out how to parse those out of sys.argv. The argparse module also automatically generates help and usage messages and issues errors when users give the program invalid arguments. [2]

简单来说, argparse 可以帮助我们很容易的写出命令行交互界面(command-line interface), 并且可以自动生成很好的注释, 帮助及错误处理, 便于程序被大家(和自己)使用. 

在 GitBook auto summary (commit [5005eb](https://github.com/Frank-the-Obscure/GitBook-auto-summary/commit/5005ebf8c1baeb4a093ec263d6a3ad87ff5cc42d)) 中的相关代码和注释

```python
parser = argparse.ArgumentParser()
parser.add_argument('-o', '--overwrite', help='overwrite on SUMMARY.md', 
                    action="store_true") # 定义 overwrite 参数
parser.add_argument('directory', help='the directory of your GitBook root') # 定义 directory 参数
args = parser.parse_args() # Convert argument strings to objects and assign them as attributes of the namespace. Return the populated namespace.
overwrite = args.overwrite
dir_input = args.directory
if args.overwrite:
    print(dir_input, 'overwrite')
else:
    print(dir_input)
```

MVP:

- argparse.ArgumentParser()
- ArgumentParser.add_argument()
- ArgumentParser.parse_args()


---

References

1. [Argparse Tutorial](https://docs.python.org/3/howto/argparse.html)
2. [argparse - py std lib](https://docs.python.org/3/library/argparse.html)