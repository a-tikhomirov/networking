# **Задание 1**
Скачать файл Lesson4Homework.pkt (из 4 урока) либо воспользоваться уже исправленным файлом, который вы делали на четвертом уроке. Учтите, что статические маршруты имеют приоритет перед динамическими, поэтому, чтобы динамическая маршрутизация заработала, нужно сначала выключить статические маршруты через команду no ip route.  
Настроить динамическую маршрутизацию с помощью протокола RIPv2.  
В локальных сетях трех офисов организации настройте выдачу адресов по протоколу DHCP.  
Проверьте, что любые два компьютера из разных сетей попарно пингуются. Учтите, что адрес одному компьютеру теперь может быть присвоен другим, и поэтому актуальный IP-адрес рабочей станции надо смотреть в настройках TCP/IP либо с помощью команды ipconfig.

### **Настройка хостов**

- На всех хостах установлено получение IP по `DHCP`.

- Хост 192.168.1.2 переименован в `NET1 - PC1`
- Хост 192.168.1.3 переименован в `NET1 - PC2`

- Хост 192.168.2.2 переименован в `NET2 - PC1`
- Хост 192.168.2.3 переименован в `NET2 - PC2`

- Хост 192.168.3.2 переименован в `NET3 - PC1`
- Хост 192.168.3.3 переименован в `NET3 - PC1`

### **Настройка роутеров**

- Router 0 (сети `192.168.1.0/24`, `172.16.0.0/16` `172.17.0.0/16`):

```
en
conf t

no ip route 192.168.2.0 255.255.255.0 172.17.1.1
no ip route 192.168.3.0 255.255.255.0 172.16.2.1

ip dhcp pool pool.192.168.1.0
network 192.168.1.0 255.255.255.0
default-router 192.168.1.1
dns-server 8.8.8.8
exit

ip dhcp excluded-address 192.168.1.1
router rip
version 2
network 192.168.1.0
network 172.16.0.0
network 172.17.0.0
exit

end
wr
```

- Router 1 (сети `192.168.2.0/24`, `172.17.0.0/16` `172.18.0.0/16`):

```
en
conf t

no ip route 192.168.1.0 255.255.255.0 172.17.0.1
no ip route 192.168.3.0 255.255.255.0 172.18.2.1

ip dhcp pool pool.192.168.2.0
network 192.168.2.0 255.255.255.0
default-router 192.168.2.1
dns-server 8.8.8.8
exit

ip dhcp excluded-address 192.168.2.1
router rip
version 2
network 192.168.2.0
network 172.17.0.0
network 172.18.0.0
exit

end
wr
```

- Router 2 (сети `192.168.3.0/24`, `172.16.0.0/16` `172.18.0.0/16`):

```
en
conf t

no ip route 192.168.2.0 255.255.255.0 172.18.1.1
no ip route 192.168.1.0 255.255.255.0 172.16.0.1

ip dhcp pool pool.192.168.3.0
network 192.168.3.0 255.255.255.0
default-router 192.168.3.1
dns-server 8.8.8.8
exit

ip dhcp excluded-address 192.168.3.1
router rip
version 2
network 192.168.3.0
network 172.16.0.0
network 172.18.0.0
exit

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
Router(config)#
Router(config)#no ip route 192.168.2.0 255.255.255.0 172.17.1.1
Router(config)#no ip route 192.168.3.0 255.255.255.0 172.16.2.1
Router(config)#
Router(config)#ip dhcp pool pool.192.168.1.0
Router(dhcp-config)#network 192.168.1.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.1.1
Router(dhcp-config)#dns-server 8.8.8.8
Router(dhcp-config)#exit
Router(config)#
Router(config)#ip dhcp excluded-address 192.168.1.1
Router(config)#router rip
Router(config-router)#version 2
Router(config-router)#network 192.168.1.0
Router(config-router)#network 172.16.0.0
Router(config-router)#network 172.17.0.0
Router(config-router)#exit
Router(config)#
Router(config)#end
Router#wr
%SYS-5-CONFIG_I: Configured from console by console

Building configuration...
[OK]
```

- Router 1 (сети `192.168.2.0/24`, `172.17.0.0/16` `172.18.0.0/16`):

```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#
Router(config)#no ip route 192.168.1.0 255.255.255.0 172.17.0.1
Router(config)#no ip route 192.168.3.0 255.255.255.0 172.18.2.1
Router(config)#
Router(config)#ip dhcp pool pool.192.168.2.0
Router(dhcp-config)#network 192.168.2.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.2.1
Router(dhcp-config)#dns-server 8.8.8.8
Router(dhcp-config)#exit
Router(config)#
Router(config)#ip dhcp excluded-address 192.168.2.1
Router(config)#router rip
Router(config-router)#version 2
Router(config-router)#network 192.168.2.0
Router(config-router)#network 172.17.0.0
Router(config-router)#network 172.18.0.0
Router(config-router)#exit
Router(config)#
Router(config)#end
Router#wr
%SYS-5-CONFIG_I: Configured from console by console

Building configuration...
[OK]
```

- Router 2 (сети `192.168.3.0/24`, `172.16.0.0/16` `172.18.0.0/16`):

```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#
Router(config)#no ip route 192.168.2.0 255.255.255.0 172.18.1.1
Router(config)#no ip route 192.168.1.0 255.255.255.0 172.16.0.1
Router(config)#
Router(config)#ip dhcp pool pool.192.168.3.0
Router(dhcp-config)#network 192.168.3.0 255.255.255.0
Router(dhcp-config)#default-router 192.168.3.1
Router(dhcp-config)#dns-server 8.8.8.8
Router(dhcp-config)#exit
Router(config)#
Router(config)#ip dhcp excluded-address 192.168.3.1
Router(config)#router rip
Router(config-router)#version 2
Router(config-router)#network 192.168.3.0
Router(config-router)#network 172.16.0.0
Router(config-router)#network 172.18.0.0
Router(config-router)#exit
Router(config)#
Router(config)#end
Router#wr
%SYS-5-CONFIG_I: Configured from console by console

Building configuration...
[OK]
```

</details>

### **Конфигурации роутеров и таблицы маршрутизации**

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
R    172.18.0.0/16 [120/1] via 172.16.2.1, 00:00:09, FastEthernet5/0
                   [120/1] via 172.17.1.1, 00:00:16, FastEthernet4/0
C    192.168.1.0/24 is directly connected, FastEthernet0/0
R    192.168.2.0/24 [120/1] via 172.17.1.1, 00:00:16, FastEthernet4/0
R    192.168.3.0/24 [120/1] via 172.16.2.1, 00:00:09, FastEthernet5/0
```

<details>
  <summary>Концигурация (sh run)</summary>

```
Router#sh run
Building configuration...

Current configuration : 994 bytes
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
router rip
 version 2
 network 172.16.0.0
 network 172.17.0.0
 network 192.168.1.0
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

R    172.16.0.0/16 [120/1] via 172.17.0.1, 00:00:23, FastEthernet4/0
                   [120/1] via 172.18.2.1, 00:00:16, FastEthernet5/0
C    172.17.0.0/16 is directly connected, FastEthernet4/0
C    172.18.0.0/16 is directly connected, FastEthernet5/0
R    192.168.1.0/24 [120/1] via 172.17.0.1, 00:00:23, FastEthernet4/0
C    192.168.2.0/24 is directly connected, FastEthernet0/0
R    192.168.3.0/24 [120/1] via 172.18.2.1, 00:00:16, FastEthernet5/0
```

<details>
  <summary>Концигурация (sh run)</summary>

```
Router#sh run
Building configuration...

Current configuration : 994 bytes
!
version 12.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Router
!
ip dhcp excluded-address 192.168.2.1
!
ip dhcp pool pool.192.168.2.0
 network 192.168.2.0 255.255.255.0
 default-router 192.168.2.1
 dns-server 8.8.8.8
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
router rip
 version 2
 network 172.17.0.0
 network 172.18.0.0
 network 192.168.2.0
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
R    172.17.0.0/16 [120/1] via 172.18.1.1, 00:00:15, FastEthernet4/0
                   [120/1] via 172.16.0.1, 00:00:20, FastEthernet5/0
C    172.18.0.0/16 is directly connected, FastEthernet4/0
R    192.168.1.0/24 [120/1] via 172.16.0.1, 00:00:20, FastEthernet5/0
R    192.168.2.0/24 [120/1] via 172.18.1.1, 00:00:15, FastEthernet4/0
C    192.168.3.0/24 is directly connected, FastEthernet0/0
```

<details>
  <summary>Концигурация (sh run)</summary>

```
Router#sh run
Building configuration...

Current configuration : 994 bytes
!
version 12.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Router
!
ip dhcp excluded-address 192.168.3.1
!
ip dhcp pool pool.192.168.3.0
 network 192.168.3.0 255.255.255.0
 default-router 192.168.3.1
 dns-server 8.8.8.8
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
router rip
 version 2
 network 172.16.0.0
 network 172.18.0.0
 network 192.168.3.0
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

### **Проверка доступности сетей**

- Проверка доступности сетей из сети 192.168.1.0/24 (трассировка от хоста NET1 - PC1):

```
C:\>tracert 192.168.2.2

Tracing route to 192.168.2.2 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      192.168.1.1
  2   0 ms      0 ms      0 ms      172.17.1.1
  3   0 ms      0 ms      0 ms      192.168.2.2

Trace complete.
```

```
C:\>tracert 192.168.3.2

Tracing route to 192.168.3.2 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      192.168.1.1
  2   0 ms      0 ms      0 ms      172.16.2.1
  3   0 ms      0 ms      0 ms      192.168.3.2

Trace complete.
```

- Проверка доступности сетей из сети 192.168.2.0/24 (трассировка от хоста NET2 - PC1):

```
tracert 192.168.1.2

Tracing route to 192.168.1.2 over a maximum of 30 hops: 

  1   0 ms      1 ms      0 ms      192.168.2.1
  2   0 ms      7 ms      0 ms      172.17.0.1
  3   0 ms      1 ms      1 ms      192.168.1.2

Trace complete.
```

```
tracert 192.168.3.2

Tracing route to 192.168.3.2 over a maximum of 30 hops: 

  1   1 ms      0 ms      1 ms      192.168.2.1
  2   0 ms      0 ms      1 ms      172.18.2.1
  3   0 ms      0 ms      0 ms      192.168.3.2

Trace complete.
```

- Проверка доступности сетей из сети 192.168.3.0/24 (трассировка от хоста NET3 - PC1):

```
tracert 192.168.1.2

Tracing route to 192.168.1.2 over a maximum of 30 hops: 

  1   1 ms      1 ms      0 ms      192.168.3.1
  2   0 ms      0 ms      0 ms      172.16.0.1
  3   1 ms      0 ms      0 ms      192.168.1.2

Trace complete.
```

```
tracert 192.168.2.2

Tracing route to 192.168.2.2 over a maximum of 30 hops: 

  1   1 ms      1 ms      0 ms      192.168.3.1
  2   0 ms      0 ms      0 ms      172.18.1.1
  3   0 ms      1 ms      3 ms      192.168.2.2

Trace complete.
```

# **Усложненное задание**
Попробуйте использовать не три сети класса C, а три подсети сети класса A, используя бесклассовую адресацию. Учтите, что если маршруты в подсети будут идти через разные маршрутизаторы, может понадобится отключить суммаризацию маршрутов через no auto-summary.

### **Настройка роутеров**

- Router 0 (сети `10.0.0.0/24`, `172.16.0.0/16` `172.17.0.0/16`):

```
en
conf t

no ip route 192.168.2.0 255.255.255.0 172.17.1.1
no ip route 192.168.3.0 255.255.255.0 172.16.2.1

ip dhcp pool pool.10.0.0.0
network 10.0.0.0 255.255.255.0
default-router 10.0.0.1
dns-server 8.8.8.8
exit

ip dhcp excluded-address 10.0.0.1

interface FastEthernet0/0
ip address 10.0.0.1 255.255.255.0
exit

router rip
version 2
no auto-summary
network 10.0.0.0
network 172.16.0.0
network 172.17.0.0
exit

end
wr
```

- Router 1 (сети `10.0.1.0/24`, `172.17.0.0/16` `172.18.0.0/16`):

```
en
conf t

no ip route 192.168.1.0 255.255.255.0 172.17.0.1
no ip route 192.168.3.0 255.255.255.0 172.18.2.1

ip dhcp pool pool.10.0.1.0
network 10.0.1.0 255.255.255.0
default-router 10.0.1.1
dns-server 8.8.8.8
exit

ip dhcp excluded-address 10.0.1.1

interface FastEthernet0/0
ip address 10.0.1.1 255.255.255.0
exit

router rip
version 2
no auto-summary
network 10.0.0.0
network 172.17.0.0
network 172.18.0.0
exit

end
wr
```

- Router 2 (сети `10.0.2.0/24`, `172.16.0.0/16` `172.18.0.0/16`):

```
en
conf t

no ip route 192.168.2.0 255.255.255.0 172.18.1.1
no ip route 192.168.1.0 255.255.255.0 172.16.0.1

ip dhcp pool pool.10.0.2.0
network 10.0.2.0 255.255.255.0
default-router 10.0.2.1
dns-server 8.8.8.8
exit

ip dhcp excluded-address 10.0.2.1

interface FastEthernet0/0
ip address 10.0.2.1 255.255.255.0
exit

router rip
version 2
no auto-summary
network 10.0.0.0
network 172.16.0.0
network 172.18.0.0
exit

end
wr
```

<details>
  <summary>Подробно</summary>

- Router 0 (сети `10.0.0.0/24`, `172.16.0.0/16` `172.17.0.0/16`):

```
Router#en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#
Router(config)#no ip route 192.168.2.0 255.255.255.0 172.17.1.1
Router(config)#no ip route 192.168.3.0 255.255.255.0 172.16.2.1
Router(config)#
Router(config)#ip dhcp pool pool.10.0.0.0
Router(dhcp-config)#network 10.0.0.0 255.255.255.0
Router(dhcp-config)#default-router 10.0.0.1
Router(dhcp-config)#dns-server 8.8.8.8
Router(dhcp-config)#exit
Router(config)#
Router(config)#ip dhcp excluded-address 10.0.0.1
Router(config)#
Router(config)#interface FastEthernet0/0
Router(config-if)#ip address 10.0.0.1 255.255.255.0
Router(config-if)#exit
Router(config)#
Router(config)#router rip
Router(config-router)#version 2
Router(config-router)#no auto-summary
Router(config-router)#network 10.0.0.0
Router(config-router)#network 172.16.0.0
Router(config-router)#network 172.17.0.0
Router(config-router)#exit
Router(config)#
Router(config)#end
Router#wr
%SYS-5-CONFIG_I: Configured from console by console

Building configuration...
[OK]
```

- Router 1 (сети `10.0.1.0/24`, `172.17.0.0/16` `172.18.0.0/16`):

```
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#no ip route 192.168.1.0 255.255.255.0 172.17.0.1
Router(config)#no ip route 192.168.3.0 255.255.255.0 172.18.2.1
Router(config)#
Router(config)#ip dhcp pool pool.10.0.1.0
Router(dhcp-config)#network 10.0.1.0 255.255.255.0
Router(dhcp-config)#default-router 10.0.1.1
Router(dhcp-config)#dns-server 8.8.8.8
Router(dhcp-config)#exit
Router(config)#
Router(config)#ip dhcp excluded-address 10.0.1.1
Router(config)#
Router(config)#interface FastEthernet0/0
Router(config-if)#ip address 10.0.1.1 255.255.255.0
Router(config-if)#exit
Router(config)#
Router(config)#router rip
Router(config-router)#version 2
Router(config-router)#no auto-summary
Router(config-router)#network 10.0.0.0
Router(config-router)#network 172.17.0.0
Router(config-router)#network 172.18.0.0
Router(config-router)#exit
Router(config)#
Router(config)#end
Router#wr
%SYS-5-CONFIG_I: Configured from console by console

Building configuration...
[OK]
```

- Router 2 (сети `10.0.2.0/24`, `172.16.0.0/16` `172.18.0.0/16`):

```
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#
Router(config)#no ip route 192.168.2.0 255.255.255.0 172.18.1.1
Router(config)#no ip route 192.168.1.0 255.255.255.0 172.16.0.1
Router(config)#
Router(config)#ip dhcp pool pool.10.0.2.0
Router(dhcp-config)#network 10.0.2.0 255.255.255.0
Router(dhcp-config)#default-router 10.0.2.1
Router(dhcp-config)#dns-server 8.8.8.8
Router(dhcp-config)#exit
Router(config)#
Router(config)#ip dhcp excluded-address 10.0.2.1
Router(config)#
Router(config)#interface FastEthernet0/0
Router(config-if)#ip address 10.0.2.1 255.255.255.0
Router(config-if)#exit
Router(config)#
Router(config)#router rip
Router(config-router)#version 2
Router(config-router)#no auto-summary
Router(config-router)#network 10.0.0.0
Router(config-router)#network 172.16.0.0
Router(config-router)#network 172.18.0.0
Router(config-router)#exit
Router(config)#
Router(config)#end
Router#wr
%SYS-5-CONFIG_I: Configured from console by console

Building configuration...
[OK]
```

</details>

### **Конфигурация роутеров и таблицы маршрутизации**

- Router 0 (сети `10.0.0.0/24`, `172.16.0.0/16` `172.17.0.0/16`):

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

     10.0.0.0/24 is subnetted, 3 subnets
C       10.0.0.0 is directly connected, FastEthernet0/0
R       10.0.1.0 [120/1] via 172.17.1.1, 00:00:03, FastEthernet4/0
R       10.0.2.0 [120/1] via 172.16.2.1, 00:00:05, FastEthernet5/0
C    172.16.0.0/16 is directly connected, FastEthernet5/0
C    172.17.0.0/16 is directly connected, FastEthernet4/0
R    172.18.0.0/16 [120/1] via 172.17.1.1, 00:00:03, FastEthernet4/0
                   [120/1] via 172.16.2.1, 00:00:05, FastEthernet5/0
```

<details>
  <summary>Концигурация (sh run)</summary>

```
Router#sh run
Building configuration...

Current configuration : 993 bytes
!
version 12.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Router
!
ip dhcp excluded-address 10.0.0.1
!
ip dhcp pool pool.10.0.0.0
 network 10.0.0.0 255.255.255.0
 default-router 10.0.0.1
 dns-server 8.8.8.8
!
ip cef
no ipv6 cef
!
interface FastEthernet0/0
 ip address 10.0.0.1 255.255.255.0
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
router rip
 version 2
 network 10.0.0.0
 network 172.16.0.0
 network 172.17.0.0
 no auto-summary
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

- Router 1 (сети `10.0.1.0/24`, `172.17.0.0/16` `172.18.0.0/16`):

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

     10.0.0.0/24 is subnetted, 3 subnets
R       10.0.0.0 [120/1] via 172.17.0.1, 00:00:00, FastEthernet4/0
C       10.0.1.0 is directly connected, FastEthernet0/0
R       10.0.2.0 [120/1] via 172.18.2.1, 00:00:27, FastEthernet5/0
R    172.16.0.0/16 [120/1] via 172.17.0.1, 00:00:00, FastEthernet4/0
                   [120/1] via 172.18.2.1, 00:00:27, FastEthernet5/0
C    172.17.0.0/16 is directly connected, FastEthernet4/0
C    172.18.0.0/16 is directly connected, FastEthernet5/0
```

<details>
  <summary>Концигурация (sh run)</summary>

```
Router#sh run
Building configuration...

Current configuration : 993 bytes
!
version 12.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Router
!
ip dhcp excluded-address 10.0.1.1
!
ip dhcp pool pool.10.0.1.0
 network 10.0.1.0 255.255.255.0
 default-router 10.0.1.1
 dns-server 8.8.8.8
!
ip cef
no ipv6 cef
!
interface FastEthernet0/0
 ip address 10.0.1.1 255.255.255.0
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
router rip
 version 2
 network 10.0.0.0
 network 172.17.0.0
 network 172.18.0.0
 no auto-summary
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

- Router 2 (сети `10.0.2.0/24`, `172.16.0.0/16` `172.18.0.0/16`):

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

     10.0.0.0/24 is subnetted, 3 subnets
R       10.0.0.0 [120/1] via 172.16.0.1, 00:00:01, FastEthernet5/0
R       10.0.1.0 [120/1] via 172.18.1.1, 00:00:20, FastEthernet4/0
C       10.0.2.0 is directly connected, FastEthernet0/0
C    172.16.0.0/16 is directly connected, FastEthernet5/0
R    172.17.0.0/16 [120/1] via 172.16.0.1, 00:00:01, FastEthernet5/0
                   [120/1] via 172.18.1.1, 00:00:20, FastEthernet4/0
C    172.18.0.0/16 is directly connected, FastEthernet4/0
```

<details>
  <summary>Концигурация (sh run)</summary>

```
Router#sh run
Building configuration...

Current configuration : 993 bytes
!
version 12.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Router
!
ip dhcp excluded-address 10.0.2.1
!
ip dhcp pool pool.10.0.2.0
 network 10.0.2.0 255.255.255.0
 default-router 10.0.2.1
 dns-server 8.8.8.8
!
ip cef
no ipv6 cef
interface FastEthernet0/0
 ip address 10.0.2.1 255.255.255.0
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
router rip
 version 2
 network 10.0.0.0
 network 172.16.0.0
 network 172.18.0.0
 no auto-summary
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

### **Проверка доступности сетей**

- Проверка доступности сетей из сети `10.0.0.0/24` (трассировка от хоста NET1 - PC1):

```
C:\>tracert 10.0.1.2

Tracing route to 10.0.1.2 over a maximum of 30 hops: 

  1   0 ms      0 ms      2 ms      10.0.0.1
  2   0 ms      0 ms      0 ms      172.17.1.1
  3   0 ms      1 ms      0 ms      10.0.1.2

Trace complete.
```

```
C:\>tracert 10.0.2.2

Tracing route to 10.0.2.2 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      10.0.0.1
  2   1 ms      0 ms      0 ms      172.16.2.1
  3   0 ms      0 ms      0 ms      10.0.2.2

Trace complete.
```

- Проверка доступности сетей из сети `10.0.1.0/24` (трассировка от хоста NET2 - PC1):

```
C:\>tracert 10.0.0.2

Tracing route to 10.0.0.2 over a maximum of 30 hops: 

  1   1 ms      0 ms      2 ms      10.0.1.1
  2   1 ms      0 ms      1 ms      172.17.0.1
  3   1 ms      0 ms      0 ms      10.0.0.2

Trace complete.
```

```
C:\>tracert 10.0.2.2

Tracing route to 10.0.2.2 over a maximum of 30 hops: 

  1   1 ms      0 ms      0 ms      10.0.1.1
  2   0 ms      0 ms      1 ms      172.18.2.1
  3   1 ms      1 ms      0 ms      10.0.2.2

Trace complete.
```

- Проверка доступности сетей из сети `10.0.2.0/24` (трассировка от хоста NET3 - PC1):

```
C:\>tracert 10.0.0.2

Tracing route to 10.0.0.2 over a maximum of 30 hops: 

  1   1 ms      0 ms      0 ms      10.0.2.1
  2   0 ms      1 ms      0 ms      172.16.0.1
  3   3 ms      0 ms      0 ms      10.0.0.2

Trace complete.
```

```
C:\>tracert 10.0.1.2

Tracing route to 10.0.1.2 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      10.0.2.1
  2   1 ms      0 ms      0 ms      172.18.1.1
  3   0 ms      0 ms      1 ms      10.0.1.2

Trace complete.
```