# **Задание 1**
В файле, над которым вы уже работали над статической, динамической маршрутизацией и dhcp в 4 и 6 уроке Lesson4Homework.pkt, необходимо добавить новый шлюз между сетью 192.168.10.0/24 и Router0 (либо настройте Router0 как такой шлюз, но добавьте взамен новый роутер, входящий в сети 172.16.0.0/16 и 172.17.0.0/16).
Необходимо настроить, чтобы на данном шлюзе выполнялась трансляция адресов и портов — перегруженный NAT.
Обратите внимание на правильные настройки протоколов маршрутизации.
Прежде чем приступить к выполнению домашнего задания, обязательно озаботьтесь, какой адрес вы выделите для образовавшейся новой сети, какие адреса вы назначите сетевым интерфейсам. Где нужен dhcp-сервер, а где нет. Не остались ли IP-адреса на сетевых интерфейсах, которые не используются? Внимательно подпишите все настройки и проверьте.
В качестве домашнего задания пришлите сам файл pkt и в комментарии к работе перечень настроек (вводимых команд или вывод sh run) на Router 0 и новом роутере.

### **Перенастройка роутера *Router0* (сети `10.0.0.0/8`, `172.16.0.0/16` `172.17.0.0/16`)**

- Добавлен новой сетевой интерфейс: *PT-ROUTER-NM-1FFE*;

- Перенастройка роутера:

```
en
conf t

no ip dhcp excluded-address 192.168.1.1
no ip dhcp pool pool.192.168.1.0

int fa0/0
no ip addr 192.168.1.1 255.255.255.0
shut
exit

int fa6/0
ip addr 10.0.0.1 255.0.0.0
no shut
exit

router rip
no network 192.168.1.0
network 10.0.0.0
exit

end
wr
```

<details>
  <summary>Подробно</summary>

```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#
Router(config)#no ip dhcp excluded-address 192.168.1.1
Router(config)#no ip dhcp pool pool.192.168.1.0
Router(config)#
Router(config)#int fa0/0
Router(config-if)#no ip addr 192.168.1.1 255.255.255.0
Router(config-if)#shut
Router(config-if)#exit
Router(config)#
Router(config)#int fa6/0
Router(config-if)#ip addr 10.0.0.1 255.0.0.0
Router(config-if)#no shut
Router(config-if)#exit
Router(config)#
Router(config)#router rip
Router(config-router)#no network 192.168.1.0
Router(config-router)#network 10.0.0.0
Router(config-router)#exit
Router(config)#
Router(config)#end
Router#wr
%SYS-5-CONFIG_I: Configured from console by console

Building configuration...
[OK]
```

</details>

### **Настройка нового роутера *Router Local* (сети `192.168.1.0/24`, `10.0.0.0/8`)**

- Настройка роутера:

```
en
conf t

ip dhcp pool pool.192.168.1.0
network 192.168.1.0 255.255.255.0
default-router 192.168.1.1
dns-server 8.8.8.8
exit

ip dhcp excluded-address 192.168.1.1

int fa0/0
ip addr 192.168.1.1 255.255.255.0
no shut
exit

int fa5/0
ip addr 10.0.0.2 255.0.0.0
no shut
exit

router rip
version 2
network 10.0.0.0
exit

access-list 1 permit 192.168.1.0 0.0.0.255
ip nat inside source list 1 int fa5/0 overload

int fa0/0
ip nat inside
int fa5/0
ip nat outside
exit

end
wr
```

<details>
  <summary>Подробно</summary>

```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#
Router(config)#ip dhcp pool pool.192.168.1.0
Router(dhcp-config)#network 192.168.1.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.1.1
Router(dhcp-config)#dns-server 8.8.8.8
Router(dhcp-config)#exit
Router(config)#
Router(config)#ip dhcp excluded-address 192.168.1.1
Router(config)#
Router(config)#int fa0/0
Router(config-if)#ip addr 192.168.1.1 255.255.255.0
Router(config-if)#no shut
Router(config-if)#exit
Router(config)#
Router(config)#int fa5/0
Router(config-if)#ip addr 10.0.0.2 255.0.0.0
Router(config-if)#no shut
Router(config-if)#exit
Router(config)#
Router(config)#router rip
Router(config-router)#version 2
Router(config-router)#network 10.0.0.0
Router(config-router)#exit
Router(config)#
Router(config)#access-list 1 permit 192.168.1.0 0.0.0.255
Router(config)#ip nat inside source list 1 int fa5/0 overload
Router(config)#
Router(config)#int fa0/0
Router(config-if)#ip nat inside
Router(config-if)#int fa5/0
Router(config-if)#ip nat outside
Router(config-if)#exit
Router(config)#
Router(config)#end
Router#wr
%SYS-5-CONFIG_I: Configured from console by console

Building configuration...
[OK]
```

</details>

### **Конфигурации роутеров и таблицы маршрутизации**

- Router 0 (сети `10.0.0.0/8`, `172.16.0.0/16` `172.17.0.0/16`):

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

C    10.0.0.0/8 is directly connected, FastEthernet6/0
C    172.16.0.0/16 is directly connected, FastEthernet5/0
C    172.17.0.0/16 is directly connected, FastEthernet4/0
R    172.18.0.0/16 [120/1] via 172.17.1.1, 00:00:18, FastEthernet4/0
                   [120/1] via 172.16.2.1, 00:00:01, FastEthernet5/0
R    192.168.2.0/24 [120/1] via 172.17.1.1, 00:00:18, FastEthernet4/0
R    192.168.3.0/24 [120/1] via 172.16.2.1, 00:00:01, FastEthernet5/0
```

<details>
  <summary>Концигурация (sh run)</summary>

```
Router#sh run
Building configuration...

Current configuration : 887 bytes
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
 no ip address
 duplex auto
 speed auto
 shutdown
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
interface FastEthernet6/0
 ip address 10.0.0.1 255.0.0.0
!
router rip
 version 2
 network 10.0.0.0
 network 172.16.0.0
 network 172.17.0.0
!
ip classless
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

- Router Local (сети `192.168.1.0/24`, `10.0.0.0/8`):

```
Router# sh ip ro
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

C    10.0.0.0/8 is directly connected, FastEthernet5/0
R    172.16.0.0/16 [120/1] via 10.0.0.1, 00:00:17, FastEthernet5/0
R    172.17.0.0/16 [120/1] via 10.0.0.1, 00:00:17, FastEthernet5/0
R    172.18.0.0/16 [120/2] via 10.0.0.1, 00:00:17, FastEthernet5/0
C    192.168.1.0/24 is directly connected, FastEthernet0/0
R    192.168.2.0/24 [120/2] via 10.0.0.1, 00:00:17, FastEthernet5/0
R    192.168.3.0/24 [120/2] via 10.0.0.1, 00:00:17, FastEthernet5/0
```

<details>
  <summary>Концигурация (sh run)</summary>

```
Router#sh run
Building configuration...

Current configuration : 1034 bytes
!
version 12.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Router
!
ip dhcp excluded-address 192.168.1.1
!
ip dhcp pool pool.192.168.1.0
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 8.8.8.8
!
ip cef
no ipv6 cef
!
interface FastEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 ip nat inside
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
 shutdown
!
interface Serial3/0
 no ip address
 shutdown
!
interface FastEthernet4/0
 no ip address
 shutdown
!
interface FastEthernet5/0
 ip address 10.0.0.2 255.0.0.0
 ip nat outside
!
router rip
 version 2
 network 10.0.0.0
!
ip nat inside source list 1 interface FastEthernet5/0 overload
ip classless
!
ip flow-export version 9
!
access-list 1 permit 192.168.1.0 0.0.0.255
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

### **Проверка доступности сетей**

- Проверка доступности сетей из сети `192.168.1.0/24` (трассировка от хоста NET1 - PC1):

```
C:\>tracert 192.168.2.2

Tracing route to 192.168.2.2 over a maximum of 30 hops: 

  1   1 ms      0 ms      0 ms      192.168.1.1
  2   0 ms      0 ms      0 ms      10.0.0.1
  3   11 ms     12 ms     0 ms      172.17.1.1
  4   0 ms      0 ms      0 ms      192.168.2.2

Trace complete.
```

```
C:\>tracert 192.168.3.2

Tracing route to 192.168.3.2 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      192.168.1.1
  2   0 ms      0 ms      0 ms      10.0.0.1
  3   1 ms      12 ms     12 ms     172.16.2.1
  4   18 ms     11 ms     10 ms     192.168.3.2

Trace complete.
```

Доступа к сети `192.168.1.0/24` из сетей `192.168.2.0/24` и `192.168.3.0/24` нет - так и должно быть в этом задании.