# From chaos to telegram

### Objective: 

既然自己想做的东西还没有成型, 不妨随便找些东西练手呐!

和大妈闲谈时, 大妈提到有个 telegram 的坑. 也在助教报名后被拉到了 telegram之中. 

So, 试着看看能不能解决这个小的真实需求?

- 具体需求: 自动化导出 telegram 的记录 (?)

### From chaos ... 

需求是什么...? 都是不确定的

启动 `不确定情况下的学习` 吧!

- [telegram website](https://telegram.org/) + wiki + GitHub search telegram ...
- 发现 telegram 很奇葩的(?)没有导出聊天记录功能...
- [Backup or archive chat history #384](https://github.com/DrKLO/Telegram/issues/384)
  - 这里是官方 android 客户端的 issue...似乎官方不想解决.
  - 某些用户已经给出了自己的解决方案...
- [HOWTO backup your Telegram chats (if you don’t fear the terminal)](http://www.haykranen.nl/2014/12/02/howto-backup-your-telegram-chats/)
  - 另外的资源...

看上去, 最艰难的 `从0到1` 已经有人走过去了. 下面就是应用 python, 自己进入这个体系测试. 

- [telegram-cli](https://github.com/vysheng/tg)
- [telegram-cli-backup](https://github.com/psamim/telegram-cli-backup)

就作为第二个从 1 到 n 的小项目(第一个是 mascot csv process)吧

over.

### 实现思路

MVP: 复刻已有 cli 客户端实现 + py 

later: bot to copy automatically

## How to do it?


### 站在巨人的肩膀上

 安装 `telegram-cli`  
 in mac, 按照 README 所说流程即可.
 
 ```
 brew install libconfig
 brew install readline
 brew install lua
 brew install python
 brew install libevent
 brew install jansson
 export CFLAGS="-I/usr/local/include -I/usr/local/Cellar/readline/6.3.8/include"
 export LDFLAGS="-L/usr/local/lib -L/usr/local/Cellar/readline/6.3.8/lib"
 ./configure && make
 ```
 
可能需要使用 `sudo bin/telegram-cli -k tg-server.pub` 进行第一次登陆

phone number的格式: +86 1xxxxxxxxxx

在 cli 中导出:

`dialog_list`: 输出所有的对话对象(包括个人和群组)

`history <dialog_name> [limit]`: 在 shell 中输出历史记录

```
> history Telegram
[21:42]  Telegram »»» Telegram code 39386
[21:44]  Telegram <<< Test
[21:45]  Telegram <<< test in cli
[21:59]  Telegram »»» Telegram code 70607
[21:59]  Telegram »»» Telegram code 63859

> history Telegram 2 # 只导出两条记录
[21:59]  Telegram »»» Telegram code 70607
[21:59]  Telegram »»» Telegram code 63859
```

### Next

使用 python 调用命令行, 实现自动化操作 telegram-cli, 并自动存储输出.

初步思路: 使用 `subprocess` 模块

```
import subprocess

p = subprocess.Popen('ls', stdout=subprocess.PIPE, shell=True)
print(p.stdout.read())
```

把`ls` 替换成`sudo bin/telegram-cli` 时 似乎就不太 work 了

忽然发现, **通过命令行调用其它程序 i/o 是一个非常重要的功能.** 

看来这个坑比想象中有意思得多啊!

---

### 纠结的尝试

设计了最简的两个脚本尝试使用 subprocess 模块控制 i/o

问题在于, subprocess 似乎只能进行一次 i/o 控制, 不能多次交互(?).

纠结的初步尝试卡壳了...需要启动 wheel of python :)

### 继续尝试 20150705

改用 `fcntl` 模块 [ref](http://www.cnblogs.com/yangxudong/p/3753846.html)

commit f9f581: basic version

commit fe6b36: 更多的交互.

但发现了新的问题: 

使用 subprocess 的问题在于, 子进程并不能通过某种方式来反馈需要信息输入. 因此可能无法实现复杂的输入选择(简单流程可以写死成几个固定输入, 但无法实现随机应变)

看上去使用 shell script 是一个交互的简单方式. 可以不影响正常使用 shell, 只是把记录输出为文件.

- 但问题是不便于自动化和选择输入, 不论写死代码还是后续处理文档, 感觉都有些尴尬.
- 另外发现了 python 的 `pty` module 也许可以用来实现类似功能?

还有的其它办法: 看看 lua 脚本是如何实现导出的.

- 20150706 继续尝试

发现了 [`Pexpect`](https://github.com/pexpect/pexpect).

(by google: `python shell 交互` --看到了吗, 经过几天的思考, 搜索的关键词定义愈发清晰准确.)  
[如何用Python交互执行shell脚本](http://my.oschina.net/memorybox/blog/94183)


- 20150707 

关于 why use tty, not subprocess 的深入(?) 机制解释.  
深入理解需要明白 buffer 和 pipe 等概念, 暂不深究.  
http://pexpect.readthedocs.org/en/latest/FAQ.html

## Chaos to pexpect

- 20150710

开始使用 `pexpect` 模块研究 cli

看了几段之后, 有点混乱. 突然意识到需要整理框架, 和按照我的目标(tg-cli)行动:

- `spawn()` 用来创建新进程
- 交互的关键: `expect()` 和 `send()` [link](http://pexpect.readthedocs.org/en/latest/overview.html)
- `interact()`可让用户操作.
- `logfile()`可输出 log 为指定文件

```
import pexpect
child = pexpect.spawn ('ftp ftp.openbsd.org')
child.expect ('Name .*: ') #如果 expect 不到, 会卡住到 timeout
child.sendline ('anonymous')
child.expect ('Password:')
child.sendline ('noah@example.com')
child.expect ('ftp> ')
child.sendline ('ls /pub/OpenBSD/')
child.expect ('ftp> ')
print child.before   # Print the result of the ls command.
child.interact()     # Give control of the child to the user.
```

完成了最早的想法: py 调用另一个 py 脚本, 并读取另一个脚本输出与用户交互

原则上, 通过积累相关输出, 应该可以实现最初想法.

- 输入登陆码等(需要通过 pexpect 输送内容到 tg, 便于判断)
- 开始输出历史记录时, 记录下 log

### 20150712

- 可以很开心的使用 pexpect 交互了.
- 但是正想在 tg-cli 测试时, 发现 tg-cli 挂了...
- 在 GitHub 搜索 issue 等折腾半个多小时, 似乎有这么几个问题: 
  - 最近 telegram 被攻击? [issue 692](https://github.com/vysheng/tg/issues/692)
  - 被墙? (ping 服务器不通..) 
  - 也许需要换 vpn 之类再测试了?
  - 真是伤感的故事...

### 20150721

- 又试着登录 tg-cli, 失灵时不灵(几次登录有一次可用)
- 也许又可以继续开发了 :)