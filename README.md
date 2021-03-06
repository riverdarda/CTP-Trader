# CTP-Trader
此项目基于几年前在一家私募时写的CTP交易机的简化版本，主要是用来实现其他语言下达交易信号，利用C++写的交易主机完成交易，包含两个部分：  
> 1、交易主机；  
> 2、下单  

最近抽空重新整理了下，并借鉴github上的一些项目，编译环境是windows+vs2017，api版本32位。 并没有使用qt，所以改成64为很方便，代码基本复制过去即可。  

当时的出发点是私募中的很多策略师使用的语言各不相同，python/matlab/R/c++的，我自己平时也是也是使用python和c++为主，对于python而言策略开发到实盘交易可以很方便的借鉴vnpy的轮子自己定制化修改，但是对于matlab和R或是其他数据分析类的语言就不太方便，C++本身虽然接口很容易，不过编写策略略的效率太低，并且那家公司本身并不是做高频交易的，所以使用别的语言发信号，C++对接CTP交易是可行的，基于这个业务背景，就写了这个交易主机，核心原理很简单，基本稍微了解CTP的内容即可。  

修改了原来的一些部分，当然，主要是因为当时写的很多细节现在自己现在也有些忘记了，核心理念是利用socket完成进程间的通信，参考了下github的一些项目， 

## Socket
使用Socket可以很方便的完成不同程序之间的进程间通讯，如此只需要将C++写成的主机写成服务器，其他语言（python为例）只需要连接端口，然后将输入的格式标准化即可，标准化的输入格式见CSV文件，如此一来只需读取CSV文件，然后利用程序直接下单，其他语言如Matlab也可以很方便的完成Socket编程，样例里给出了python发信号交易的程序

## 支持多品种与无人值守
需要注意的是频繁的下单，1s内超过1次，CTP会报错，有两种处理方式，一种是将订单存放在消息队列列，每一秒下单，另一种是在下单的时候就是间隔1s即可，这里为了方便，采用后者，效果上是一样的。C++写的交易主机在非交易阶段自动关闭，交易时段自动打开，支持无人值守的自动化交易功能，基于当时的业务，低频策略，所以在夜盘交易主机是自动关闭的，如有需要，只需要在程序里额外修改启动时间。

## 多账户
C++对于多账户的支持很方便，多定义一个交易类，程序里并没有写进去，但是也很方便，如果账户多个定义的话，对应的下单也要添加相应的账户

## 仅支持交易
C++的交易主机只有TraderApi，因为仅仅只需交易，无需核算风险，订阅市场的一些列操作，实盘交易数据的获取可以额外利用数据库读取，但是一个很方便的可行方案是利用wind提供的api接口，对于vba/matlab/python/r/c++/c#均有支持，而且读取方便，所以如果不是高频交易，这样的组合更加方便，利用python wind接口读取数据，完成交易信号，利用C++下单，无需维护数据库，降低维护成本，当然如果有精力也可以构建数据库，或者是在交易主机中进一步修改。

## 修改
这个仅仅是个Demo，而且当时主要是做日频全市场期货策略，所以维护起来较为方便，即使下单失败，由于频率很低，交易员可以很容易的发现问题，包括断网宕机等问题处理起来都很轻松，但是如果要将主机应用到频率相对高点的地方，可能需要稍微修改，可以注册simnow账号测试问题
如果有其他问题，欢迎通过邮箱与我交流zfgshdx@126.com




