# Netfilter 范例

Netfilter提供了强大的过滤match，流识别机制，使每一个数据包都可以和一个五元组标示的流关联起来，这样就可以对整个流而不是单独的数据包进行更加人性化的操作，而对流的识别以及之后的过滤作用最大的就是mark机制，注意这个mark并不是数据包本身的，它只在本机协议栈内有效。

Netfilter代码可以识别一个流的头包，然后会将一个mark打入该流，接下来的数据包可以直接从流中取出该mark来进行过滤而不必再遍历整个规则链了，类似下面的规则是常用的：

```bash
iptables -t mangle -I PREROUTING -j CONNMARK --restore-mark
iptables -t mangle -A PREROUTING -m mark ! --mark 0 -j ACCEPT
iptables -t mangle -A PREROUTING -m state --state ESTABLISHED -j ACCEPT
iptables -t mangle -N mark_Policy
iptables -t mangle -A mark_Policy $matches1 -j MARK --set-mark 100
iptables -t mangle -A mark_Policy $matches2 -j MARK --set-mark 100
iptables -t mangle -A mark_Policy -m mark ! --mark 0 -j CONNMARK --save-mark
```

类似一种cache机制，只有一个流的第一个数据包才要遍历整个规则链，其余的就可以直接restore出来mark了，接下来协议栈可以根据该mark来进行过滤或者进行Policy Routing。
