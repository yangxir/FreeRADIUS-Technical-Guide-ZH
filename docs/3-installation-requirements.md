

# 第三章 - 安装要求

本章内容描述：

- 安装选项
- 安装FreeRADIUS

本章节中使用的FreeRADIUS版本是2.2.1，所有说明和示例均基于此版本。

本章还包括关于以下内容的建议：

- 安装前的需求和关键流程规划
- 预构建的安装包
- 从源代码安装

详细的实现和安装流程可参阅FreeRADIUS实现指南。  
还提供了FreeRADIUS参考指南，包含有关配置文件的详细信息，以及帮助解决常见安装和配置问题。

### 3.0 安装选项

安装FreeRADIUS的最简单方法是使用可用的预构建二进制包。二进制包可用于Ubuntu或Debian基础系统以及RedHat基础系统。

对于非Linux系统，可以编译并安装FreeRADIUS源代码；然而，除非有网络管理员专家可以提供支持，否则不推荐使用此安装方法。请联系Network RADIUS SARL（电子邮件：SALES@NetworkRadius.com）以获取帮助规划和安装源代码的选项。Network RADIUS SARL还提供专家顾问服务，帮助规划和实施FreeRADIUS源代码客户安装。

### 3.0.1 服务器生命周期

本节描述如何规划服务器的完整生命周期。

安装是服务器生命周期中的第一步，并且安装后，持续的服务器进程包括测试、维护和审计。因此，安装服务器的过程应当考虑到服务器的完整生命周期。

构建服务器的第一步是设计周围的生态系统。这个生态系统包括服务器所需的数据库、与服务器通信的外部NAS，以及网络配置等其他因素。

设计复杂的系统可能会很困难，超出了本文档的范围。重要的是要注意，RADIUS服务器需要完成包括“响应一个或两个数据包”的操作目标，不能仅仅通过简单的“响应”来实现。

生态系统的架构对于性能和稳定性至关重要。例如，如果RADIUS服务器需要轻松接收每秒成千上万的**Accounting-Request**数据包，则系统需要能够处理那些数据包（如将数据包传输到数据库），并且需要能够处理一定数量的这些数据包，可能是每秒几个数据包。


一旦系统设计完成，下一步是安装和配置基本服务。此过程确保服务器正常运行并准备好处理流量。  
安装过程将在下面详细介绍。

安装后，系统需要配置以便持续运行。配置过程包括检查日志文件是否轮换，旧记录是否从账单表中清除，是否已配置定期备份，并确保监控和告警系统就位。目标是确保系统稳定，并且可以在没有管理员持续干预的情况下运行。

配置系统后，下一步是测试。

测试应该是自动化的并有文档记录。系统应当经过性能、合规性、稳定性和处理边缘条件的能力的测试。所有监控和告警过程应经过测试，以确保警报按预期生成。正确的测试能够确保最坏的结果——警报系统未能生成警报——在必要时避免发生。

理想情况下，应该有一个“实验室”系统，仅用于测试，和一个“生产”系统用于实时流量。在系统首次启动时，在其投入使用前，应该进行一轮“验收”测试。测试完成后，若做了任何变更，必须在实验室系统中进行验证，确保变更已生效，之后再推送到生产环境。如果没有遵循此过程，任何变更推送到生产系统可能会导致服务中断，对持续运行产生严重后果。

所有更改都应在版本控制系统中跟踪。这样做可以确保，如果生产环境出现问题，可以恢复到“已知正常”的配置。它还允许生产配置复制到另一个系统以实现冗余和高可用性。

当系统达到生命周期的终点时，新系统应按上述步骤进行安装，然后旧系统将被停用。我们建议在新系统投入使用后，继续保留旧系统一段时间。与无法恢复到“已知正常”的配置相比，保留旧系统的成本相对较低。

上述描述是对RADIUS服务器生命周期的简短介绍。更详细的文档请参阅Network RADIUS SARL提供的文档。

### 3.0.2 依赖性

任何LDAP和SQL依赖性都需要各自的客户端库来与LDAP或SQL服务器通信。预构建的FreeRADIUS包不包含这些外部依赖项。

从源代码安装需要一个有效的C编译器，例如GNU编译器集合或Clang，以及GNU Make工具。如果您的系统没有安装这些工具，则configure过程将失败并报错。为了解决此问题，请安装这些工具，并重新运行configure。建议尽可能使用预构建的安装包。

### 3.0.3 下载FreeRADIUS二进制包

可以从第三方网站下载适用于多种平台的二进制包。以下链接提供了第三方网站的相关信息：[http://freeradius.org/download.html](http://freeradius.org/download.html)。


### FreeRADIUS源代码

不推荐从源代码进行安装。如果您选择从源代码安装，我们建议联系Network RADIUS SARL寻求帮助。

对于任何非Linux系统，建议编译并安装FreeRADIUS源代码，而不是使用预构建的二进制包。其他需要安装源代码的原因包括：

- 系统没有可用的预构建安装包
- 安装实验模块
- 对源代码进行本地更改（不推荐）

最新的FreeRADIUS源代码可以从[http://freeradius.org](http://freeradius.org)下载。下载源代码，请按照以下说明操作：

1. 点击下载（Download）链接进入下载页面。
2. 点击tar.gz链接。
3. 保存文件到磁盘。

### 3.1 安装FreeRADIUS

#### 3.1.1 从预构建的二进制包安装（在Ubuntu上）

Ubuntu系统使用apt工具套件来查找和安装软件包。以下命令将查找与FreeRADIUS相关的所有软件包：

```bash
$ apt-cache search freeradius
```


上面的Ubuntu搜索返回以下软件包：

- **freeradius** - 高性能可配置的RADIUS服务器
- **freeradius-common** - FreeRADIUS公共文件
- **freeradius-dbg** - FreeRADIUS包的调试符号
- **libfreeradius-dev** - FreeRADIUS共享库开发文件
- **libfreeradius2** - FreeRADIUS共享库
- **freeradius-dialupadmin** - 用于管理FreeRADIUS服务器的PHP脚本集
- **freeradius-iodbc** - FreeRADIUS服务器的ODBC模块
- **freeradius-krb5** - FreeRADIUS服务器的Kerberos模块
- **freeradius-ldap** - FreeRADIUS服务器的LDAP模块
- **freeradius-mysql** - FreeRADIUS服务器的MySQL模块
- **freeradius-postgresql** - FreeRADIUS服务器的PostgreSQL模块
- **freeradius-utils** - FreeRADIUS客户端工具
- **libfreeradius3** - FreeRADIUS共享库

第一个包（freeradius）是基础服务器。典型配置所需的所有模块都将通过此包进行安装。其他包是可选模块，添加了如LDAP或MySQL等功能。

可以根据需要向基础服务器添加任何其他软件包。安装附加软件包不会影响FreeRADIUS服务器的操作性能或内存使用。

FreeRADIUS服务器也可以通过以下命令安装并作为守护进程运行：

```bash
$ sudo apt-get install freeradius
```

如果服务器未投入使用（生产环境），则可以通过调试模式停止服务，而不会产生负面影响，使用以下命令：

```bash
$ sudo service freeradius stop
```

本文件的其余部分假定FreeRADIUS二进制文件可在**PATH**环境变量中找到。运行服务器的命令在本文中称为**radiusd**。

### 3.1.1.1 基于Debian的系统

在基于Debian的系统中，可能会有一些不同。某些基于Debian的系统将二进制文件称为**freeradius**，而不是**radiusd**。可以通过以下命令创建别名：

1. 更改目录到**/usr/sbin**：

```bash
$ cd /usr/sbin
```

2. 创建符号链接：

```bash
$ sudo ln -s freeradius radiusd
```



> 本文件假定读者已经熟悉各种配置文件的位置。在基于Debian的系统中，配置文件位于`/etc/freeradius`目录下。本文档提到的配置文件目录是`/raddb`，而不是`/etc/freeradius`。

### 3.1.1.2 Ubuntu系统防火墙

最近版本的Ubuntu系统具有防火墙功能。为了允许RADIUS流量到达服务器，必须添加防火墙规则。以下规则允许来自任何系统的包通过防火墙发送到RADIUS服务器：

1. 添加防火墙规则，允许RADIUS认证请求通过1812端口：

```bash
$ ufw allow 1812/udp
```

2. 添加防火墙规则，允许RADIUS计费请求通过1813端口：

```bash
$ ufw allow 1813/udp
```

3. 为了验证规则是否已正确安装，请运行以下命令：

```bash
$ ufw status | grep 181
```

监视器上应该出现以下确认信息：

```
1812/udp             ALLOW       Anywhere
1813/udp             ALLOW       Anywhere
```

上述规则允许接收来自任何系统的数据包。然而，所有不在内部RADIUS服务器系统列表中的数据包将被排除，因此RADIUS服务器不会被妥协。服务器的默认防火墙设置是忽略所有外部数据包，只允许来自localhost的数据包。

### 3.1.2 从预构建的二进制包安装（在RedHat上）

RedHat系统使用`yum`工具来查找和安装软件包。运行以下命令以查找与FreeRADIUS相关的所有软件包：

```bash
$ yum search freeradius
```


搜索找到以下软件包。请注意，某些列表可能会因系统不同而略有差异：

- **freeradius** - FreeRADIUS服务器
- **freeradius-mysql** - MySQL绑定支持FreeRADIUS
- **freeradius-postgresql** - PostgreSQL绑定支持FreeRADIUS
- **freeradius-unixODBC** - Unix ODBC绑定支持FreeRADIUS
- **freeradius2** - FreeRADIUS服务器
- **freeradius2-krb5** - Kerberos 5支持FreeRADIUS
- **freeradius2-ldap** - LDAP支持FreeRADIUS
- **freeradius2-mysql** - MySQL支持FreeRADIUS
- **freeradius2-perl** - Perl支持FreeRADIUS
- **freeradius2-postgresql** - PostgreSQL支持FreeRADIUS
- **freeradius2-python** - Python支持FreeRADIUS
- **freeradius2-unixODBC** - Unix ODBC支持FreeRADIUS
- **freeradius2-utils** - FreeRADIUS工具

上面列出的第一个包（**freeradius**）是版本1的基础服务器。为了安装版本2，建议安装**freeradius2**包，该包安装了典型配置所需的所有模块。

其他软件包是可选模块，增加了如LDAP或MySQL等功能。可以根据需要添加多个可选软件包。安装软件包不会影响FreeRADIUS服务器的性能或内存使用。

要安装FreeRADIUS服务器并以守护进程运行，请使用以下命令：

```bash
$ sudo yum install freeradius2
```

如果服务器未投入使用（生产环境），则可以随时通过调试模式停止服务，并运行以下命令：

```bash
$ sudo service freeradius stop
```

> 本文件的其余部分假定FreeRADIUS二进制文件可在**PATH**环境变量中找到。  
> 在基于RedHat的系统中，二进制文件被称为**radiusd**。只有超级用户才能执行以下部分的命令。

> 本文件的其余部分假定读者已经熟悉各种配置文件的位置。  
> 在基于RedHat的系统中，配置文件被放置在`/etc/raddb`目录中。本文档提到的是`raddb`目录，而不是`/etc/raddb`。



### 3.1.2.1 RedHat系统防火墙

最近版本的RedHat系统具有防火墙功能。网络管理员必须添加以下规则，以允许RADIUS流量到达服务器。

1. 添加防火墙规则，允许RADIUS认证请求通过1812端口：

```bash
$ iptables -A INPUT -p udp --dport 1812 -j ACCEPT
```

2. 添加防火墙规则，允许RADIUS计费请求通过1813端口：

```bash
$ iptables -A INPUT -p udp --dport 1813 -j ACCEPT
```

3. 为了验证规则是否已安装，请运行以下命令：

```bash
$ iptables -L -n | grep 181
```

上述规则允许接收来自任何系统的数据包。然而，所有未包含在内部RADIUS服务器系统列表中的数据包都被排除，因此RADIUS服务器不会受到威胁。服务器的默认防火墙设置是忽略所有外部数据包，只允许来自**localhost**的数据包。

这些规则允许任何系统将数据包发送到RADIUS服务器。允许服务器接收来自任何系统的数据包不会妥协RADIUS服务器，因为服务器有一个系统列表，其中列出了它被允许接收数据包的系统。服务器的默认防火墙设置是忽略所有外部数据包，只允许来自**localhost**的数据包。

### 3.1.3 从源代码安装

尽管可以从源代码安装FreeRADIUS，但对于大多数实现来说，不推荐使用这种方法。

从源代码安装需要大量专业知识。除非您有内部专家，否则建议使用预构建的选项。如果您没有内部专家，但有独特的安装要求，并且需要定制安装，建议联系Network Radius SARL寻求源代码安装支持（电子邮件：SALES@NetworkRadius.com）。

在安装FreeRADIUS之前，请以root身份登录。`make install`过程会将服务器安装到`/usr/local/`目录中。以下步骤将安装FreeRADIUS程序：

```bash
$ tar -zxf freeradius-server-2.2.1.tar.gz
$ cd freeradius-server-2.2.1
$ ./configure
$ make
$ sudo make install
```

要更改服务器的安装位置，请使用以下命令：

```bash
$ ./configure --prefix=/opt
```

> 请参阅`./configure --help`以了解`configure`可用的完整选项列表。

> 当从源代码安装时，如果使用了扩展认证协议（EAP）用于WiFi、WiMAX或802.1x，则需要创建测试证书。安装预构建的包时，不需要创建测试证书。



### 3.1.3.1 模块

配置过程构建所有可能的模块。它通过查看freeRADIUS代码（例如，rlm_sql）并确定模块需要的外部库（例如，SQL客户端库）来完成。如果依赖项可用，则构建该模块。如果缺少依赖项，则该模块不会构建。如果某些模块在该时刻不可用，服务器仍然会运行。然而，缺少的模块可能会影响功能性（例如，MySQL可能不可用）。

大多数可选模块，如MySQL，都是从客户端库和开发头文件安装到用户的系统上的。由于系统之间的差异，本文档无法涵盖所有可能的排列。推荐尽可能使用预构建的系统特定包。

### 3.1.3.2 验证安装

要验证FreeRADIUS安装，运行服务器的调试模式。如果系统安装正确，屏幕上将显示一大段以“Ready to process requests.”结尾的文本。这条消息表示FreeRADIUS服务器已成功安装。以下是验证消息的示例：

```bash
$ radiusd -X
...
Listening on authentication address * port 1812
Listening on accounting address * port 1813
Listening on authentication address 127.0.0.1 port 18120 as server inner-tunnel
Listening on proxy address * port 1814
Ready to process requests.
```

> 本文档的其余部分假定FreeRADIUS二进制文件可在**PATH**环境变量中找到。  
> 本文档的其余部分假定读者熟悉各种配置文件的位置。请注意，本文档指的是`raddb`目录，而不是`/usr/local/etc/raddb`。

### 3.1.3.3 安装概述

安装过程是容错的。我们不推荐在生产系统上重新安装服务器，尽管这是可能的。同样，构建过程可以根据需要多次重新运行，而不会导致任何问题；因此，重新运行构建过程是安全的。

基础功能使服务器能够执行计费功能，同时支持PAP、CHAP、MS-CHAP和少数几种EAP身份验证类型。安装服务器不会覆盖您的配置。

一个可能出现的问题是构建后缺少依赖项。FreeRADIUS服务器的设计目标是尽量减少外部依赖项的数量；因此，如果某个第三方库不可用，则使用该库的任何功能都不可用。当服务器被构建时，它包括本地系统上可用的任何库，例如SQL、LDAP，如果它们存在于本地。如果发现构建后缺少所需的依赖项，则必须在本地系统上安装适当的第三方库。
这是文件内容的翻译：

如果在完成安装后需要添加第三方库，只需在新库安装到服务器后重新运行构建过程。这个过程可以根据需要重复多次，直到所有第三方库的安装完成。

---
