1介绍  https://www.freebuf.com/news/141607.html
nmap是用来探测计算机网络上的主机和服务的一种安全扫描器。为了绘制网络拓扑图Nmap的发送特制的数据包到目标主机然后对返回数据包进行分析。Nmap是一款枚举和测试网络的强大工具。

2特点
主机探测
端口扫描
版本检测
支持探测脚本的编写
安装
官网:http://nmap.org 图形化:Zenmap

3基本操作
3.1基本快速扫描
Nmap 默认发送一个arp的ping数据包来探测目标主机在1-10000范围内所开放的端口。

nmap 10.130.1.43


3.2快速扫描多个目标
nmap <target ip1 address> <target ip2 address>
nmap 10.130.1.28 10.130.1.43
一款强大的安全扫描器nmap：不老的神器

3.3详细描述输出扫描
简单扫描并对返回的结果详细描述输出,这个扫描是可以看到扫描的过程的,漫长的扫描的过程中可以看到百分比 就不会显得那么枯燥而且可以提升逼格。

nmap -vv 10.1.1.254


亲测,-v和-vv扫描几乎是一样都都是列出了详细的扫描过程。

3.4指定端口和范围扫描
nmap 默认扫描目标1-10000范围内的端口号。我们则可以通过参数-p 来设置我们将要扫描的端口号

nmap -p(range) <target IP> 
namp -p3389,20-100 10.130.1.43


3.5扫描除过某一个ip外的所有子网主机
nmap 10.130.1.1/24 -exclude 10.130.1.1
扫描除过某一个文件中的ip外的子网主机
nmap 10.130.1.1/24 -excludefile gov.txt
显示扫描的所有主机的列表
nmap -sL 10.130.1.1/24
sP ping 扫描
nmap 可以利用类似window/linux 系统下的ping方式进行扫描

nmap -sP <target ip>
一般来说 我们会用这个命令去扫描内网的一个ip范围用来做内网的主机发现。

nmap -sP 10.130.1.1-255


PING扫描不同于其它的扫描方式因为它只用于找出主机是否是存在在网络中的.它不是用来发现是否开放端口的.PING扫描需要ROOT权限如果用户没有ROOT权限,PING扫描将会使用connect()调用.

3.6sS SYN半开放扫描
nmap -sS 192.168.1.1
Tcp SYN Scan (sS) 这是一个基本的扫描方式,它被称为半开放扫描因为这种技术使得Nmap不需要通过完整的握手就能获得远程主机的信息。Nmap发送SYN包到远程主机但是它不会产生任何会话.因此不会在目标主机上产生任何日志记录,因为没有形成会话。这个就是SYN扫描的优势.如果Nmap命令中没有指出扫描类型,默认的就是Tcp SYN.但是它需要root/administrator权限。

sT TCP扫描
nmap -sT 192.168.1.1
不同于Tcp SYN扫描,Tcp connect()扫描需要完成三次握手,并且要求调用系统的connect().Tcp connect()扫描技术只适用于找出TCP和UDP端口。

sU UDP扫描
nmap -sU 192.168.1.1
这种扫描技术用来寻找目标主机打开的UDP端口.它不需要发送任何的SYN包因为这种技术是针对UDP端口的。UDP扫描发送UDP数据包到目标主机并等待响应,如果返回ICMP不可达的错误消息说明端口是关闭的如果得到正确的适当的回应说明端口是开放的.

sF FIN标志的数据包扫描
nmap -sF 110.130.1.43


可以看出这个扫描的话 会漏扫许多~FIN扫描也不会在目标主机上创建日志(FIN扫描的优势之一).个类型的扫描都是具有差异性的,FIN扫描发送的包只包含FIN标识,NULL扫描不发送数据包上的任何字节,XMAS扫描发送FIN、PSH和URG标识的数据包.

sV Version版本检测扫描
nmap -sV 192.168.1.135
本检测是用来扫描目标主机和端口上运行的软件的版本.它不同于其它的扫描技术它不是用来扫描目标主机上开放的端口不过它需要从开放的端口获取信息来判断软件的版本.使用版本检测扫描之前需要先用TCPSYN扫描开放了哪些端口。



这个扫描的话速度会慢一些67.86秒扫一个IP。

O OS操作系统类型的探测
nmap -O 10.130.1.43
远程检测操作系统和软件Nmap的OS检测技术在渗透测试中用来了解远程主机的操作系统和软件是非常有用的通过获取的信息你可以知道已知的漏洞。Nmap有一个名为的nmap-OS-DB数据库该数据库包含超过2600操作系统的信息。Nmap把TCP和UDP数据包发送到目标机器上然后检查结果和数据库对照。



osscan-guess 猜测匹配操作系统
nmap -O --osscan-guess 192.168.1.134
通过Nmap准确的检测到远程操作系统是比较困难的需要使用到Nmap的猜测功能选项,–osscan-guess猜测认为最接近目标的匹配操作系统类型。



PN No ping扫描
nmap -O -PN 192.168.1.1/24
如果远程主机有防火墙IDS和IPS系统你可以使用-PN命令来确保不ping远程主机因为有时候防火墙会组织掉ping请求.-PN命令告诉Nmap不用ping远程主机。使用-PN参数可以绕过PING命令,但是不影响主机的系统的发现。



这个扫描整个c段局域网的话还是比较耗时的但是信息收集很详细大概耗时9分钟。

T 设置时间模板
nmap -sS -T<0-5> 192.168.1.134
优化时间控制选项的功能很强大也很有效但有些用户会被迷惑。此外 往往选择合适参数的时间超过了所需优化的扫描时间。因此Nmap提供了一些简单的 方法使用6个时间模板使用时采用-T选项及数字(0 – 5) 或名称。模板名称有paranoid (0)、sneaky (1)、polite (2)、normal(3)、 aggressive (4)和insane (5)

paranoid、sneaky模式用于IDS躲避
Polite模式降低了扫描 速度以使用更少的带宽和目标主机资源。
Normal为默认模式因此-T3 实际上是未做任何优化。
Aggressive模式假设用户具有合适及可靠的网络从而加速 扫描.
nsane模式假设用户具有特别快的网络或者愿意为获得速度而牺牲准确性。
网段扫描格式
nmap -sP <network address > </CIDR >
解释CIDR 为你设置的子网掩码(/24 , /16 ,/8 等)

10.1.1.0/24  =  10.1.1.1-10.1.1.255       #c段扫描
10.1.1.0/16  =  10.1.1.1-10.1.255.255     #b段扫描
10.1.1.0/8   =  10.1.1.1-10.255.255.255   #a段扫描
从文件中读取需要扫描的IP列表
nmap -iL ip-address.txt
一款强大的安全扫描器nmap：不老的神器

路由跟踪扫描
路由器追踪功能能够帮网络管理员了解网络通行情况同时也是网络管理人员很好的辅助工具通过路由器追踪可以轻松的查处从我们电脑所在地到目标地之间所经常的网络节点并可以看到通过各个节点所花费的时间

nmap -traceroute www.baidu.com


A OS识别,版本探测,脚本扫描和traceroute综合扫描
此选项设置包含了1-10000的端口ping扫描操作系统扫描脚本扫描路由跟踪服务探测。

nmap -A 10.130.1.43

命令混合式扫描
命令混合扫描可以做到类似参数-A所完成的功能但又能细化到我们所需特殊要求。所以一般高手选择这个混合扫描

nmap -vv -p1-100,3306,3389 -O -traceroute 10.130.1.43
这些参数都是可以灵活调用的具体根据具体的扫描来使用各个参数。

一款强大的安全扫描器nmap：不老的神器一款强大的安全扫描器nmap：不老的神器

nmap -p1-65535 -sV -sS -T4 10.130.1.134
使SYN扫描并进行Version版本检测 使用T4(aggressive)的时间模板对目标ip的全端口进行扫描。

输出格式
扫描的结果输出到屏幕,同时会存储一份到grep-output.txt

nmap -sV -p 139,445 -oG grep-output.txt 10.0.1.0/24
扫描结果输出为html

nmap -sS -sV -T5 10.0.1.99 --webxml -oX - | xsltproc --output file.html
nmap高级用法之脚本使用
按照脚本分类进行扫描
nmap --script 类别
nmap官方脚本文档: https://nmap.org/nsedoc/

一款强大的安全扫描器nmap：不老的神器

左侧列出了脚本的分类点击分类 可以看到每一个分类下有很多具体的脚本供我们使用。nmap --script=类别这里的类别可以填写下面14大分类中的其中之一也可以填写分类里面的具体漏洞扫描脚本。nmap脚本分类:

- auth: 负责处理鉴权证书绕开鉴权的脚本  
- broadcast: 在局域网内探查更多服务开启状况如dhcp/dns/sqlserver等服务  
- brute: 提供暴力破解方式针对常见的应用如http/snmp等  
- default: 使用-sC或-A选项扫描时候默认的脚本提供基本脚本扫描能力  
- discovery: 对网络进行更多的信息如SMB枚举、SNMP查询等  
- dos: 用于进行拒绝服务攻击  
- exploit: 利用已知的漏洞入侵系统  
- external: 利用第三方的数据库或资源例如进行whois解析  
- fuzzer: 模糊测试的脚本发送异常的包到目标机探测出潜在漏洞 
- intrusive: 入侵性的脚本此类脚本可能引发对方的IDS/IPS的记录或屏蔽
- malware: 探测目标机是否感染了病毒、开启了后门等信息  
- safe: 此类与intrusive相反属于安全性脚本  
- version: 负责增强服务与版本扫描Version Detection功能的脚本  
- vuln: 负责检查目标机是否有常见的漏洞Vulnerability如是否有MS08_067
使用具体脚本进行扫描
nmap --script 具体的脚本 www.baidu.com
常用脚本使用案例
扫描服务器的常见漏洞
nmap --script vuln <target>
检查FTP是否开启匿名登陆
nmap --script ftp-anon <target>
PORT   STATE SERVICE
21/tcp open  ftp
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--   1 1170     924            31 Mar 28  2001 .banner
| d--x--x--x   2 root     root         1024 Jan 14  2002 bin
| d--x--x--x   2 root     root         1024 Aug 10  1999 etc
| drwxr-srwt   2 1170     924          2048 Jul 19 18:48 incoming [NSE: writeable]
| d--x--x--x   2 root     root         1024 Jan 14  2002 lib
| drwxr-sr-x   2 1170     924          1024 Aug  5  2004 pub
|_Only 6 shown. Use --script-args ftp-anon.maxlist=-1 to see all.
对MySQL进行暴破解
nmap --script=mysql-brute <target>
3306/tcp open  mysql
| mysql-brute:
|   Accounts
|     root:root - Valid credentials
一款强大的安全扫描器nmap：不老的神器

可以看出已经暴力成功破解了MySQL,在368秒内进行45061次猜测平均TPS为146.5。

对MsSQL进行暴破解
nmap -p 1433 --script ms-sql-brute --script-args userdb=customuser.txt,passdb=custompass.txt <host>
| ms-sql-brute:
|   [192.168.100.128\TEST]
|     No credentials found
|     Warnings:
|       sa: AccountLockedOut
|   [192.168.100.128\PROD]
|     Credentials found:
|       webshop_reader:secret => Login Success
|       testuser:secret1234 => PasswordMustChange
|_      lordvader:secret1234 => Login Success
对Oracle数据库进行暴破解
nmap --script oracle-brute -p 1521 --script-args oracle-brute.sid=ORCL <host>
PORT     STATE  SERVICE REASON
1521/tcp open  oracle  syn-ack
| oracle-brute:
|   Accounts
|     system:powell => Account locked
|     haxxor:haxxor => Valid credentials
|   Statistics
|_    Perfomed 157 guesses in 8 seconds, average tps: 19
对pgSQL的暴力破解
nmap -p 5432 --script pgsql-brute <host>
5432/tcp open  pgsql
| pgsql-brute:
|   root:<empty> => Valid credentials
|_  test:test => Valid credentials
对SSH进行暴力破解
nmap -p 22 --script ssh-brute --script-args userdb=users.lst,passdb=pass.lst --script-args ssh-brute.timeout=4s <target>
22/ssh open  ssh
| ssh-brute:
|  Accounts
|    username:password
|  Statistics
|_   Performed 32 guesses in 25 seconds.
利用DNS进行子域名暴力破解
nmap --script dns-brute www.baidu.com
λ nmap --script dns-brute www.baidu.com                      

Starting Nmap 7.50 ( https://nmap.org ) at 2017-07-25 13:12 ?
Nmap scan report for www.baidu.com (180.97.33.108)           
Host is up (0.018s latency).                                 
Other addresses for www.baidu.com (not scanned): 180.97.33.10
Not shown: 998 filtered ports                                
PORT    STATE SERVICE                                        
80/tcp  open  http                                           
443/tcp open  https                                          

Host script results:                                         
| dns-brute:                                                 
|   DNS Brute-force hostnames:                               
|     admin.baidu.com - 10.26.109.19                         
|     mx.baidu.com - 61.135.163.61                           
|     svn.baidu.com - 10.65.211.174                          
|     ads.baidu.com - 10.42.4.225                                                

Nmap done: 1 IP address (1 host up) scanned in 92.64 seconds
一款强大的安全扫描器nmap：不老的神器

额(⊙⊙) 这个admin.baidu.com后面那个10.26.109.19难道真的是百度内网的管理平台地址

检查VMWare ESXESXi和服务器CVE-2009-3733中的路径遍历漏洞
nmap --script http-vmware-path-vuln -p80,443,8222,8333 <host>
| http-vmware-path-vuln:
|   VMWare path traversal (CVE-2009-3733): VULNERABLE
|     /vmware/Windows 2003/Windows 2003.vmx
|     /vmware/Pentest/Pentest - Linux/Linux Pentest Bravo.vmx
|     /vmware/Pentest/Pentest - Windows/Windows 2003.vmx
|     /mnt/vmware/vmware/FreeBSD 7.2/FreeBSD 7.2.vmx
|     /mnt/vmware/vmware/FreeBSD 8.0/FreeBSD 8.0.vmx
|     /mnt/vmware/vmware/FreeBSD 8.0 64-bit/FreeBSD 8.0 64-bit.vmx
|_    /mnt/vmware/vmware/Slackware 13 32-bit/Slackware 13 32-bit.vmx
查询VMware服务器vCenterESXESXiSOAP API以提取版本信息。
λ nmap --script vmware-version -p443 10.0.1.4

Starting Nmap 7.50 ( https://nmap.org ) at 2017-07-25 12:26 ?D1ú±ê×?ê±??
Nmap scan report for 10.0.1.4
Host is up (0.0019s latency).

PORT    STATE SERVICE
443/tcp open  https
| vmware-version:
|   Server version: VMware ESXi 6.5.0
|   Build: 4887370
|   Locale version: INTL 000
|   OS type: vmnix-x86
|_  Product Line ID: embeddedEsx
Service Info: CPE: cpe:/o:vmware:ESXi:6.5.0

Nmap done: 1 IP address (1 host up) scanned in 6.28 seconds
一款强大的安全扫描器nmap：不老的神器

参数详解
Nmap支持主机名,ip,网段的表示方式例如:blah.highon.coffee, namp.org/24, 192.168.0.1;10.0.0-25.1-254

-iL filename                    从文件中读取待检测的目标,文件中的表示方法支持机名,ip,网段
-iR hostnum                     随机选取,进行扫描.如果-iR指定为0,则是无休止的扫描
--exclude host1[, host2]        从扫描任务中需要排除的主机           
--exculdefile exclude_file      排除文件中的IP,格式和-iL指定扫描文件的格式相同
主机发现
-sL                     仅仅是显示,扫描的IP数目,不会进行任何扫描
-sn                     ping扫描,即主机发现
-Pn                     不检测主机存活
-PS/PA/PU/PY[portlist]  TCP SYN Ping/TCP ACK Ping/UDP Ping发现
-PE/PP/PM               使用ICMP echo, timestamp and netmask 请求包发现主机
-PO[prococol list]      使用IP协议包探测对方主机是否开启   
-n/-R                   不对IP进行域名反向解析/为所有的IP都进行域名的反响解析
扫描技巧
-sS/sT/sA/sW/sM                 TCP SYN/TCP connect()/ACK/TCP窗口扫描/TCP Maimon扫描
-sU                             UDP扫描
-sN/sF/sX                       TCP NullFINand Xmas扫描
--scanflags                     自定义TCP包中的flags
-sI zombie host[:probeport]     Idlescan
-sY/sZ                          SCTP INIT/COOKIE-ECHO 扫描
-sO                             使用IP protocol 扫描确定目标机支持的协议类型
-b “FTP relay host”             使用FTP bounce scan
指定端口和扫描顺序
-p                      特定的端口 -p80,443 或者 -p1-65535
-p U:PORT               扫描udp的某个端口, -p U:53
-F                      快速扫描模式,比默认的扫描端口还少
-r                      不随机扫描端口,默认是随机扫描的
--top-ports "number"    扫描开放概率最高的number个端口,出现的概率需要参考nmap-services文件,ubuntu中该文件位于/usr/share/nmap.nmap默认扫前1000个
--port-ratio "ratio"    扫描指定频率以上的端口
服务版本识别
-sV                             开放版本探测,可以直接使用-A同时打开操作系统探测和版本探测
--version-intensity "level"     设置版本扫描强度,强度水平说明了应该使用哪些探测报文。数值越高服务越有可能被正确识别。默认是7
--version-light                 打开轻量级模式,为--version-intensity 2的别名
--version-all                   尝试所有探测,为--version-intensity 9的别名
--version-trace                 显示出详细的版本侦测过程信息
脚本扫描
-sC                             根据端口识别的服务,调用默认脚本
--script=”Lua scripts”          调用的脚本名
--script-args=n1=v1,[n2=v2]     调用的脚本传递的参数
--script-args-file=filename     使用文本传递参数
--script-trace                  显示所有发送和接收到的数据
--script-updatedb               更新脚本的数据库
--script-help=”Lua script”      显示指定脚本的帮助
OS识别
-O              启用操作系统检测,-A来同时启用操作系统检测和版本检测
--osscan-limit  针对指定的目标进行操作系统检测(至少需确知该主机分别有一个open和closed的端口)
--osscan-guess  推测操作系统检测结果,当Nmap无法确定所检测的操作系统时会尽可能地提供最相近的匹配Nmap默认进行这种匹配
防火墙/IDS躲避和哄骗
-f; --mtu value                 指定使用分片、指定数据包的MTU.
-D decoy1,decoy2,ME             使用诱饵隐蔽扫描
-S IP-ADDRESS                   源地址欺骗
-e interface                    使用指定的接口
-g/ --source-port PROTNUM       使用指定源端口  
--proxies url1,[url2],...       使用HTTP或者SOCKS4的代理 

--data-length NUM               填充随机数据让数据包长度达到NUM
--ip-options OPTIONS            使用指定的IP选项来发送数据包
--ttl VALUE                     设置IP time-to-live域
--spoof-mac ADDR/PREFIX/VEBDOR  MAC地址伪装
--badsum                        使用错误的checksum来发送数据包
Nmap 输出
-oN                     将标准输出直接写入指定的文件
-oX                     输出xml文件
-oS                     将所有的输出都改为大写
-oG                     输出便于通过bash或者perl处理的格式,非xml
-oA BASENAME            可将扫描结果以标准格式、XML格式和Grep格式一次性输出
-v                      提高输出信息的详细度
-d level                设置debug级别,最高是9
--reason                显示端口处于带确认状态的原因
--open                  只输出端口状态为open的端口
--packet-trace          显示所有发送或者接收到的数据包
--iflist                显示路由信息和接口,便于调试
--log-errors            把日志等级为errors/warings的日志输出
--append-output         追加到指定的文件
--resume FILENAME       恢复已停止的扫描
--stylesheet PATH/URL   设置XSL样式表转换XML输出
--webxml                从namp.org得到XML的样式
--no-sytlesheet         忽略XML声明的XSL样式表
其他nmap选项
-6                      开启IPv6
-A                      OS识别,版本探测,脚本扫描和traceroute
--datedir DIRNAME       说明用户Nmap数据文件位置
--send-eth / --send-ip  使用原以太网帧发送/在原IP层发送
--privileged            假定用户具有全部权限
--unprovoleged          假定用户不具有全部权限,创建原始套接字需要root权限
-V                      打印版本信息
-h                      输出帮助