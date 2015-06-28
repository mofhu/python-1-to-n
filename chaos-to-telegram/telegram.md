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