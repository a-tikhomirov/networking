# **Настройка роутеров**

- Router 0 (сети `192.168.1.0/24`, `172.16.0.0/16` `172.17.0.0/16`):

```
en
conf t
int fa 4/0
ip add 172.17.0.1 255.255.0.0
no shut
exit
int fa 5/0
ip add 172.16.0.1 255.255.0.0
no shut
exit
ip route 192.168.2.0 255.255.255.0 172.17.1.1
ip route 192.168.3.0 255.255.255.0 172.16.2.1
end
wr
```

- Router 1 (сети `192.168.2.0/24`, `172.17.0.0/16` `172.18.0.0/16`):

```
en
conf t
int fa 4/0
ip add 172.17.1.1 255.255.0.0
no shut
exit
int fa 5/0
ip add 172.18.1.1 255.255.0.0
no shut
exit
ip route 192.168.1.0 255.255.255.0 172.17.0.1
ip route 192.168.3.0 255.255.255.0 172.18.2.1
end
wr
```

- Router 2 (сети `192.168.3.0/24`, `172.16.0.0/16` `172.18.0.0/16`):

```
en
conf t
int fa 4/0
ip add 172.18.2.1 255.255.0.0
no shut
exit
int fa 5/0
ip add 172.16.2.1 255.255.0.0
no shut
exit
ip route 192.168.2.0 255.255.255.0 172.18.1.1
ip route 192.168.1.0 255.255.255.0 172.16.0.1
end
wr
```

<details>
  <summary>Подробно</summary>

- Router 0 (сети `192.168.1.0/24`, `172.16.0.0/16` `172.17.0.0/16`):

```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#int fa 4/0
Router(config-if)#ip add 172.17.0.1 255.255.0.0
Router(config-if)#no shut

%LINK-5-CHANGED: Interface FastEthernet4/0, changed state to down
Router(config-if)#exit
Router(config)#int fa 5/0
Router(config-if)#ip add 172.16.0.1 255.255.0.0
Router(config-if)#no shut

%LINK-5-CHANGED: Interface FastEthernet5/0, changed state to down
Router(config-if)#exit
Router(config)#ip route 192.168.2.0 255.255.255.0 172.17.1.1
Router(config)#ip route 192.168.3.0 255.255.255.0 172.16.2.1
Router(config)#end
Router#
%SYS-5-CONFIG_I: Configured from console by console

Router#wr
Building configuration...
[OK]
```

- Router 1 (сети `192.168.2.0/24`, `172.17.0.0/16` `172.18.0.0/16`):

```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#int fa 4/0
Router(config-if)#ip add 172.17.1.1 255.255.0.0
Router(config-if)#no shut

Router(config-if)#
%LINK-5-CHANGED: Interface FastEthernet4/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet4/0, changed state to up

Router(config-if)#exit
Router(config)#int fa 5/0
Router(config-if)#ip add 172.18.1.1 255.255.0.0
Router(config-if)#no shut

%LINK-5-CHANGED: Interface FastEthernet5/0, changed state to down
Router(config-if)#exit
Router(config)#ip route 192.168.1.0 255.255.255.0 172.17.0.1
Router(config)#ip route 192.168.3.0 255.255.255.0 172.18.2.1
Router(config)#end
Router#
%SYS-5-CONFIG_I: Configured from console by console

Router#wr
Building configuration...
[OK]
```

- Router 2 (сети `192.168.3.0/24`, `172.16.0.0/16` `172.18.0.0/16`):

```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#int fa 4/0
Router(config-if)#ip add 172.18.2.1 255.255.0.0
Router(config-if)#no shut

Router(config-if)#
%LINK-5-CHANGED: Interface FastEthernet4/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet4/0, changed state to up

Router(config-if)#exit
Router(config)#int fa 5/0
Router(config-if)#ip add 172.16.2.1 255.255.0.0
Router(config-if)#no shut

Router(config-if)#
%LINK-5-CHANGED: Interface FastEthernet5/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet5/0, changed state to up

Router(config-if)#exit
Router(config)#ip route 192.168.2.0 255.255.255.0 172.18.1.1
Router(config)#ip route 192.168.1.0 255.255.255.0 172.16.0.1
Router(config)#end
Router#
%SYS-5-CONFIG_I: Configured from console by console

Router#wr
Building configuration...
[OK]
```

</details>

# **Конфигурация роутеров и таблицы маршрутизации**

- Router 0 (сети `192.168.1.0/24`, `172.16.0.0/16` `172.17.0.0/16`):

```
Router#sh ip ro
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

C    172.16.0.0/16 is directly connected, FastEthernet5/0
C    172.17.0.0/16 is directly connected, FastEthernet4/0
C    192.168.1.0/24 is directly connected, FastEthernet0/0
S    192.168.2.0/24 [1/0] via 172.17.1.1
S    192.168.3.0/24 [1/0] via 172.16.2.1
```

<details>
  <summary>Концигурация (sh run)</summary>

```
Router#sh run
Building configuration...

Current configuration : 853 bytes
!
version 12.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Router
!
ip cef
no ipv6 cef
!
interface FastEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Serial2/0
 no ip address
 clock rate 2000000
 shutdown
!
interface Serial3/0
 no ip address
 clock rate 2000000
 shutdown
!
interface FastEthernet4/0
 ip address 172.17.0.1 255.255.0.0
!
interface FastEthernet5/0
 ip address 172.16.0.1 255.255.0.0
!
ip classless
ip route 192.168.2.0 255.255.255.0 172.17.1.1 
ip route 192.168.3.0 255.255.255.0 172.16.2.1 
!
ip flow-export version 9
!
line con 0
!
line aux 0
!
line vty 0 4
 login
!
end
```

</details>

- Router 1 (сети `192.168.2.0/24`, `172.17.0.0/16` `172.18.0.0/16`):

```
Router#sh ip ro
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

C    172.17.0.0/16 is directly connected, FastEthernet4/0
C    172.18.0.0/16 is directly connected, FastEthernet5/0
S    192.168.1.0/24 [1/0] via 172.17.0.1
C    192.168.2.0/24 is directly connected, FastEthernet0/0
S    192.168.3.0/24 [1/0] via 172.18.2.1
```

<details>
  <summary>Концигурация (sh run)</summary>

```
Router#sh run
Building configuration...

Current configuration : 853 bytes
!
version 12.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Router
!
ip cef
no ipv6 cef
!
interface FastEthernet0/0
 ip address 192.168.2.1 255.255.255.0
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Serial2/0
 no ip address
 clock rate 2000000
 shutdown
!
interface Serial3/0
 no ip address
 clock rate 2000000
 shutdown
!
interface FastEthernet4/0
 ip address 172.17.1.1 255.255.0.0
!
interface FastEthernet5/0
 ip address 172.18.1.1 255.255.0.0
!
ip classless
ip route 192.168.1.0 255.255.255.0 172.17.0.1 
ip route 192.168.3.0 255.255.255.0 172.18.2.1 
!
ip flow-export version 9
!
line con 0
!
line aux 0
!
line vty 0 4
 login
!
end
```

</details>

- Router 2 (сети `192.168.3.0/24`, `172.16.0.0/16` `172.18.0.0/16`):

```
Router#sh ip ro
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

C    172.16.0.0/16 is directly connected, FastEthernet5/0
C    172.18.0.0/16 is directly connected, FastEthernet4/0
S    192.168.1.0/24 [1/0] via 172.16.0.1
S    192.168.2.0/24 [1/0] via 172.18.1.1
C    192.168.3.0/24 is directly connected, FastEthernet0/0
```

<details>
  <summary>Концигурация (sh run)</summary>

```
Router#sh run
Building configuration...

Current configuration : 853 bytes
!
version 12.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Router
!
ip cef
no ipv6 cef
!
interface FastEthernet0/0
 ip address 192.168.3.1 255.255.255.0
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Serial2/0
 no ip address
 clock rate 2000000
 shutdown
!
interface Serial3/0
 no ip address
 clock rate 2000000
 shutdown
!
interface FastEthernet4/0
 ip address 172.18.2.1 255.255.0.0
!
interface FastEthernet5/0
 ip address 172.16.2.1 255.255.0.0
!
ip classless
ip route 192.168.2.0 255.255.255.0 172.18.1.1 
ip route 192.168.1.0 255.255.255.0 172.16.0.1 
!
ip flow-export version 9
!
line con 0
!
line aux 0
!
line vty 0 4
 login
!
end
```

</details>

# **Проверка доступности сетей**

- Проверка доступности сетей из сети 192.168.1.0/8 (трассировка от хоста 192.168.1.2):

```
tracert 192.168.2.2

Tracing route to 192.168.2.2 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      192.168.1.1
  2   0 ms      0 ms      0 ms      172.17.1.1
  3   0 ms      0 ms      0 ms      192.168.2.2

Trace complete.
```

```
tracert 192.168.3.2

Tracing route to 192.168.3.2 over a maximum of 30 hops: 

  1   0 ms      1 ms      0 ms      192.168.1.1
  2   1 ms      0 ms      0 ms      172.16.2.1
  3   0 ms      1 ms      0 ms      192.168.3.2

Trace complete.
```

- Проверка доступности сетей из сети 192.168.2.0/8 (трассировка от хоста 192.168.2.2):

```
tracert 192.168.1.2

Tracing route to 192.168.1.2 over a maximum of 30 hops: 

  1   0 ms      0 ms      1 ms      192.168.2.1
  2   0 ms      1 ms      0 ms      172.17.0.1
  3   3 ms      10 ms     13 ms     192.168.1.2

Trace complete.
```

```
tracert 192.168.3.2

Tracing route to 192.168.3.2 over a maximum of 30 hops: 

  1   1 ms      0 ms      0 ms      192.168.2.1
  2   1 ms      1 ms      0 ms      172.18.2.1
  3   0 ms      0 ms      0 ms      192.168.3.2

Trace complete.
```

- Проверка доступности сетей из сети 192.168.3.0/8 (трассировка от хоста 192.168.3.2):

```
tracert 192.168.1.2

Tracing route to 192.168.1.2 over a maximum of 30 hops: 

  1   1 ms      0 ms      1 ms      192.168.3.1
  2   0 ms      1 ms      0 ms      172.16.0.1
  3   0 ms      0 ms      0 ms      192.168.1.2

Trace complete.
```

```
tracert 192.168.2.2

Tracing route to 192.168.2.2 over a maximum of 30 hops: 

  1   1 ms      0 ms      0 ms      192.168.3.1
  2   0 ms      0 ms      0 ms      172.18.1.1
  3   0 ms      1 ms      0 ms      192.168.2.2

Trace complete.
```

