# iptables

## 什么是 iptables

iptables 是 Linux 的防火墙管理工具，真正实现防火墙功能的是 Netfilter 。

## 表
- filter : 用于存放所有与防火墙相关操作的默认表
- nat : 用于网络地址转换
- mangle : 对数据包的一些传输特性进行修改，如：TTL、TLS、MARK
- raw : 用于配置数据表，raw 中的数据包不会被系统跟踪
- security : 用于强制访问控制

表的优先级：

raw > mangle > nat > filter

### 几个名词

- TTL ： Time To Live 生存时间值
- TOS ： Type Of Service 服务类型
- MARK : 标记

## 默认链

- INPUT
- OUTPUT
- FORWARD
- PREROUTING
- POSTROUTING

数据包的流转：

- 封包进入 -> PREROUTING -> INPUT -> OUTPUT -> POSTROUTING -> 封包传出
- 封包进入 -> PREROUTING -> FORWARD -> 封包传出

## 表和链的关系

表由预先定义的链组成。

表 | 链
:------|:-----|
filter | `INPUT` `OUTPUT` `FORWARD`
nat    | `PREROUTING` `INPUT` `OUTPUT` `POSTROUTING`
mangle | `PREROUTING` `INPUT` `OUTPUT` `FORWARD` `POSTROUTING`
raw    | `PREROUTING` `OUTPUT`
security  | `INPUT` `OUTPUT` `FORWARD`

## 部分 command 参数

### 通用匹配

参数 | 含义
:------|:-----|
`-s` `--src` `--source` | 匹配源地址，必须是 `IP` `IP/MASK` 地址前可以加 `!` 取反
`-d` `--dst` `--destination` | 匹配目标地址
`-p` | 用于匹配协议 `tcp` `udp` `icmp`
`-i` `--in-interface` | 用于指定数据流入网卡
`-o` `--out-interface` | 用于指定数据流出网卡

### 扩展匹配

#### `-p tcp` 的三种扩展

参数 | 含义 | 简单用法
:------|:-----| :-----
`--dport` `--destination-port`  |指定目标端口 | `--dport 22` 指定目标单个端口<br>`--dport 22-25` 指定连续目标端口
`--sport` `--source-port`  |指定源端口   |同上
`--tcp-flags`  | tcp 标志位 （SYN ACK FIN PSH RST URG） |  一般跟两个参数：检查的标志位，必须为 1 的标志位 。`--tcpflags syn,ack,fin,rst syn` 表示检查 4 个标志位，syn 必须为 1 ；可以简写为 `--syn`。

#### `-p udp` 的两种扩展

- `--dport`
- `--sport`

#### `-p icmp` 的扩展

- `--icmp-type`

#### `-m `

用法 | 含义
:------|:-----
`-m multiport --destination-ports 21,23,25` | 指定多个（最多 15 个）不连续的端口，可用参数： `--source-port `
` -m limit --limit 3/hour`  |  限制平均流量，可用参数： `/second` `/minute` `/hour` `/day`
` -m limit --limit-burst 5` | 限制瞬间传入的峰值
` -m mac --mac-source  00:00:00:00:00:01`| 指定网卡硬件地址
` -m mark --mark 1 `   |  标记
` -m addrtype --dst-type LOCAL`  |

` -m state --state `

- INVALID：无效的封包，例如数据破损的封包状态
- ESTABLISHED：已经联机成功的联机状态；
- NEW：想要新建立联机的封包状态；
- RELATED：这个最常用！表示这个封包是与我们主机发送出去的封包有关， 可能是响应封包或者是联机成功之后的传送封包！这个状态很常被设定，因为设定了他之后，只要未来由本机发送出去的封包，即使我们没有设定封包的 INPUT 规则，该有关的封包还是可以进入我们主机， 可以简化相当多的设定规则。

```
iptables -A INPUT -m limit --limit-burst 5
iptables -A INPUT -m limit --limit 3/hour
```

`-m owner`

用法 | 含义
:-----------|:-----
`-m owner --uid-owner`  |  用来比对来自本机的封包，是否为某特定使用者所产生的，这样可以避免服务器使用 root 或其它身分将敏感数据传送出，可以降低系统被骇的损失。可惜这个功能无法比对出来自其它主机的封包。
`-m owner --gid-owner`  |  用来比对来自本机的数据包，是否为某特定使用者群组所产生的，使用时机同上。
`-m owner --pid-owner`|  用来比对来自本机的数据包，是否为某特定行程所产生的，使用时机同上。
`-m owner --sid-owner`  |  用来比对来自本机的数据包，是否为某特定联机（Session ID）的响应封包，使用时机同上。

```
iptables -A OUTPUT -m owner --sid-owner 100
```

### -j ACTION

参数 | 含义
:------|:-----
DROP  |  丢弃
REJECT  |  拒绝
ACCEPT  |  接受
custom_chain  |  转向一个自定义的链
DNAT  | 主要用于内部服务器被外网访问（发布服务）
SNAT  | 主要用于实现内网客户端访问外部主机时使用（局域网上网用）
MASQUERADE  |  源地址伪装，当外网ip不固定时，这个配置 I 可以自动获取外网ip
REDIRECT  |  重定向
MARK  |  打防火墙标记
RETURN  |  在自定义链执行完毕后，返回原规则链


```
#适用于外网ip地址固定场景
iptables -t nat -I POSTROUTING -o 外网网卡 -s 内网网段 -j SNAT --to-source 外网ip地址
#适用于共享动态ip地址上网（如adsl拨号，dhcp获取外网ip）
iptables -t nat -I POSTROUTING -o 外网网卡 -s 内网网段 -j MASQUERADE
#将外网端口映射到内网
iptables -t nat -I PREROUTING -i 外网网卡 -d 外网ip tcp --dport 发布的端口 -j DNAT --to-destination 内网服务ip:端口
iptables -t nat -A POSTROUTING  -o br0   -s 内网段/24  -j MASQUERADE
# 映射多个外网 ip 上网
iptables -t nat -A POSTROUTING -s 10.0.0.0/255.255.255.0 -o eth0 -j SNAT 124.42.60.11 -124.42.60.16
```

## 自定义链

### 创建自定义链

```
iptables -N TEST
```

### 自定义链中加入规则

```
iptables -I TEST -p tcp --sport 81 -j ACCEPT
```

### 将自定义链加入默认链

```
iptables -I INPUT -p tcp -j TEST
```

### 重命名自定义链

```
iptables -E TEST TEST_1
```

### 删除自定义链

删除时需要满足两个条件：

- 自定义链没有被任何默认链引用，即自定义链的引用计数为 0 ；
- 自定义链中没有任何规则，即自定义链为空。

```
iptables -X TEST_1
```

## 配置路由转发

```
# 配置端口转发
iptables -t nat -I PREROUTING -i enx00e04c534458 -p tcp --dport 86 -j DNAT --to-destination 192.168.1.31:86
# 配置 ip 地址伪装
iptables -t nat -I POSTROUTING -s 192.168.1.31 -o enx00e04c534458 -j MASQUERADE
```

## 参考链接

- [Iptables 指南 1.1.19](https://www.frozentux.net/iptables-tutorial/cn/iptables-tutorial-cn-1.1.19.html#TRAVERSINGOFTABLES)
- [Iptables (简体中文)](https://wiki.archlinux.org/index.php/iptables_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29)
- [iptables命令详解和举例](https://blog.csdn.net/qq_38892883/article/details/79709023)
- [防火墙之地址转换SNAT DNAT](https://blog.csdn.net/chengxuyuanyonghu/article/details/64441374)
- [Iptables详解](https://www.cnblogs.com/losbyday/p/5850435.html)
