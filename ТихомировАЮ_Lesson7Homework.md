> Автор: *Тихомиров Андрей*

# **Задание 1**
Используйте разные варианты ключей, чтобы определить, какие у вас есть открытые порты (TCP и UDP), какие прослушивающие TCP-сокеты, установленные TCP-соединения (как исходящие, так и входящие). Выберите 10 произвольных строк и проанализируйте: какой установлен протокол, прослушивающий сокет, или соединение (входящее либо исходящее). Если порт нестандартный или не зарезервирован за каким-то протоколом, попробуйте уточнить имя процесса или его идентификатор (чтобы опознать его в списке процессов или диспетчере задач). Узнайте, что это за сервис и зачем ему нужны соединения. Можно искать в интернете информацию о сервисе.  

Отчет должен содержать подробное описание 10 произвольных строк:
```
    что это за сокет,
    какой протокол, в каком состоянии соединение,
    какой сервис или программа его использует,
    зачем. 
	Отчет должен быть подготовлен в формате docx или pdf и сдан в качестве домашнего задания. Отчет может содержать скриншоты, но не должен состоять из одного скриншота вывода netstat без каких-либо пояснений.
```

- Операционная система: *Ubuntu 18.04*	

- Используемая команда:
`sudo netstat -natup`

- Используемые опции команды `netstat`:  
  - `-n` - для выключения преобразования номеров портов в имена;  
  - `-a` - для отображения всех сокетов (и прослушиваемых, и установленных);  
  - `-tu` - для отображения *TCP* и *UDP* сокетов (чтобы исключить *UNIX* сокеты);  
  - `-p` - для отображения *PID* процесса, использующего сокет;

- Для исключения из вывода сокетов `tcp6` и `udp6` передадим вывод команды `netstat` в `grep`:  
`sudo netstat -natup | grep -Pv '^(tcp|udp)6'

Вывод команды:

```ShellSession
root@ubuntu-vbox:~# netstat -natup | grep -Pv '^(tcp|udp)6'
Активные соединения с интернетом (servers and established)
Proto Recv-Q Send-Q Local Address Foreign Address State       PID/Program name
tcp        0      0 0.0.0.0:139             0.0.0.0:*               LISTEN      1201/smbd
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1177/nginx: master
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      629/systemd-resolve
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1157/sshd
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      4233/cupsd
tcp        0      0 127.0.0.1:6010          0.0.0.0:*               LISTEN      3003/sshd: cbpi@pts
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN      1177/nginx: master
tcp        0      0 0.0.0.0:445             0.0.0.0:*               LISTEN      1201/smbd
tcp        0      0 192.168.41.250:22       192.168.41.2:62677      ESTABLISHED 2693/sshd: cbpi [pr
tcp        0      0 192.168.41.250:22       192.168.41.2:62691      ESTABLISHED 2695/sshd: cbpi [pr
tcp        0      0 192.168.41.250:34434    192.168.41.252:445      ESTABLISHED -
udp        0      0 0.0.0.0:5353            0.0.0.0:*                           722/avahi-daemon: r
udp        0      0 0.0.0.0:47032           0.0.0.0:*                           722/avahi-daemon: r
udp        0      0 127.0.0.53:53           0.0.0.0:*                           629/systemd-resolve
udp        0      0 0.0.0.0:68              0.0.0.0:*                           860/dhclient
udp        0      0 192.168.41.255:137      0.0.0.0:*                           1145/nmbd
udp        0      0 192.168.41.250:137      0.0.0.0:*                           1145/nmbd
udp        0      0 0.0.0.0:137             0.0.0.0:*                           1145/nmbd
udp        0      0 192.168.41.255:138      0.0.0.0:*                           1145/nmbd
udp        0      0 192.168.41.250:138      0.0.0.0:*                           1145/nmbd
udp        0      0 0.0.0.0:138             0.0.0.0:*                           1145/nmbd
udp        0      0 0.0.0.0:631             0.0.0.0:*                           4234/cups-browsed
```

#### 1,2 Анализ строк:  
```
tcp        0      0 0.0.0.0:139             0.0.0.0:*               LISTEN      1201/smbd
tcp        0      0 0.0.0.0:445             0.0.0.0:*               LISTEN      1201/smbd
```

- Анализ содержимого строк:  
  - `tcp` - протокол, используемый сокетами: `TCP`;  
  - `0      0` - счетчик байт не скопированных программой из сокета и счетчик байт не подтверждённых удалённым узлом;  
    - `0.0.0.0:139` - адрес и номер порта локального сокета: любой локальный IP адрес машины, порт `TCP 139`;  
	- `0.0.0.0:445` - адрес и номер порта локального сокета: любой локальный IP адрес машины, порт `TCP 445`;
  - `0.0.0.0:*` - адрес и номер порта удаленного сокета: любой удаленный адрес, любой `TCP` порт;  
  - `LISTEN` - состояние сокета: порты `TCP 139` и `TCP 445` прослушиваются (сокеты ожидают входящих подключений);
  - `1201/smbd` - идентификатор процесса и имя процесса, владеющего сокетами: `PID = 1201; Process name = smbd`.

- Анализ портов `139/tcp` и `445/tcp`:
  - Порт `139/tcp` назначен службе `netbios-ssn` (чтобы программа, которая использует эту службу, знала какой порт использовать);
  - Порт `445/tcp` назначен службе `Microsoft Naked CIFS`.

```ShellSession
cbpi@ubuntu-vbox:~ $ cat /etc/services | grep 139/tcp
netbios-ssn     139/tcp                         # NETBIOS session service
cbpi@ubuntu-vbox:~ $ cat /etc/services | grep '\s445/tcp'
microsoft-ds    445/tcp                         # Microsoft Naked CIFS
```

- Анализ информации о процессе `PID 1201`:
  - запущен системным менеджером Linux `systemd`;  
  - запущен от пользователся `root`;  
  - исполняемый файл находится в директории `/usr/sbin/smbd`.

```ShellSession
cbpi@ubuntu-vbox:~ $ pstree -ps 1201
systemd(1)---smbd(1201)-T-cleanupd(1205)
                        +-lpqd(1206)
                        L-smbd-notifyd(1204)
cbpi@ubuntu-vbox:~ $ ps -aux | grep '1201\s'
root      1201  0.0  0.1 363800 20668 ?        Ss   18:52   0:00 /usr/sbin/smbd --foreground --no-process-group
```

- Анализ информации о программе `smbd`:  
  - исполняемый файл предоставляет службы `SMB/CIFS`;  
  - исполняемый файл входит в пакет `samba`;  
  - `smbd` в определенном случае требует записи в `/etc/services`, которая будет задавать соответсвие служба - порт.
  
```ShellSession
cbpi@ubuntu-vbox:~ $ whatis smbd
smbd (8)             - server to provide SMB/CIFS services to clients
cbpi@ubuntu-vbox:~ $ apt-file search /usr/sbin/smbd
samba: /usr/sbin/smbd					
cbpi@ubuntu-vbox:~ $ man samba
...
FILES
        ...
       /etc/services
           If running the server via the meta-daemon inetd, this file must contain a mapping of
           service name (e.g., netbios-ssn) to service port (e.g., 139) and protocol type (e.g.,
           tcp).
...
```

- Резюмируем: 
 
TCP сокет `0.0.0.0:139` используется программой *Samba* для поддержки передачи данных по протоколу `NetBIOS`.  
TCP сокет `0.0.0.0:445` используется программой *Samba* для поддержки передачи данных непосредственно поверх TCP (без `NetBIOS`).  
*Samba* - программа, которая позволяет обращаться к сетевым дискам на различных операционных системах.

#### 3,4 Анализ строк:  
```
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1177/nginx: master
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN      1177/nginx: master
```

- Анализ содержимого строк:  
  - `tcp` - протокол, используемый сокетами: `TCP`;  
  - `0      0` - счетчик байт не скопированных программой из сокета и счетчик байт не подтверждённых удалённым узлом;  
    - `0.0.0.0:80` - адрес и номер порта локального сокета: любой локальный IP адрес машины, порт `TCP 80`;  
	- `0.0.0.0:443` - адрес и номер порта локального сокета: любой локальный IP адрес машины, порт `TCP 443`;
  - `0.0.0.0:*` - адрес и номер порта удаленного сокета: любой удаленный адрес, любой `TCP` порт;  
  - `LISTEN` - состояние сокета: порты `80/tcp` и `443/tcp` прослушиваются (Сокеты ожидают входящих подключений);
  - `1177/nginx` - идентификатор процесса и имя процесса, владеющего сокетом: `PID = 1177; Process name = nginx`.

- Анализ портов `80/tcp` и `443/tcp`:
  - Порт `80/tcp` назначен для использования протоколом `HTTP`;  
  - Порт `443/tcp` назначен для использования протоколом `HTTPS` или `http over TLS/SSL`.

```ShellSession
cbpi@ubuntu-vbox:~ $ cat /etc/services | grep '\s80/tcp\s'
http            80/tcp          www             # WorldWideWeb HTTP
cbpi@ubuntu-vbox:~ $ cat /etc/services | grep 443/tcp
https           443/tcp                         # http protocol over TLS/SSL
```

- Анализ информации о процессе `PID 1177`:
  - запущен системным менеджером Linux `systemd`;  
  - запущен от пользователся `root`;  
  - исполняемый файл находится в директории `/usr/sbin/nginx`.

```ShellSession
cbpi@ubuntu-vbox:~ $ pstree -ps 1177
systemd(1)---nginx(1177)-T-nginx(1178)
                         +-nginx(1179)
                         +-nginx(1180)
                         L-nginx(1181)
cbpi@ubuntu-vbox:~ $ ps -aux | grep [1]177
root      1177  0.0  0.0 158560  1724 ?        Ss   18:52   0:00 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
```

- Анализ информации о программе `nginx`:  
  - исполняемый файл предоставляет службу `HTTP/HTTPS` и почтовго сервера;  
  - исполняемый файл входит в различные пакеты `nginx*`;  
  - основной конфигурационный файл `nginx` - это файл `/etc/nginx/nginx.conf`.
  
```ShellSession
cbpi@ubuntu-vbox:~ $ whatis nginx
nginx (8)            - "HTTP and reverse proxy server, mail proxy server"
cbpi@ubuntu-vbox:~ $ apt-file search /usr/sbin/nginx
nginx-core: /usr/sbin/nginx
nginx-extras: /usr/sbin/nginx
nginx-full: /usr/sbin/nginx
nginx-light: /usr/sbin/nginx				
cbpi@ubuntu-vbox:~ $ man samba
...
FILES
     ...
     /etc/nginx/nginx.conf
             The main configuration file.
...
```  

- Резюмируем: 
 
В данном случае, сокеты `0.0.0.0:80` и `0.0.0.0:443` используется программой *nginx* для обеспечения работы `http`/`https` сервера. 

> Конфигурационный файл `/etc/nginx/nginx.conf` в свою очередь использует файлы в директории `/etc/nginx/sites-enabled/`:  
>
> ```ShellSession
> root@ubuntu-vbox:~# cat /etc/nginx/nginx.conf | head -n64 | tail -n1
>         include /etc/nginx/sites-enabled/*;
> root@ubuntu-vbox:~# ls -lA /etc/nginx/sites-enabled
> итого 0
> lrwxrwxrwx 1 root root 34 фев  3 00:17 defconf -> /etc/nginx/sites-available/default
> ```
>
> 1. Изменим конфигурацию для использования портов `83/tcp` и `446/tcp`:
>
> ```ShellSession
> root@ubuntu-vbox:~# cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default.bak
> root@ubuntu-vbox:~# sed -i 's/80 /83 /; s/443 /446 /' /etc/nginx/sites-available/default
> root@ubuntu-vbox:~# nginx -t
> nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
> nginx: configuration file /etc/nginx/nginx.conf test is successful
> root@ubuntu-vbox:~# service nginx reload
> ```
>
> 2. Проверим какие порты прослушиваются теперь:
>
> ```ShellSession
> root@ubuntu-vbox:~# netstat -natup | grep nginx | grep -v tcp6
> tcp        0      0 0.0.0.0:83              0.0.0.0:*               LISTEN      1177/nginx: master
> tcp        0      0 0.0.0.0:446             0.0.0.0:*               LISTEN      1177/nginx: master
> ```
>
> 3. Откроем на той же машине в браузере адрес: `http://192.168.41.250:83/` и проверим сокеты снова:
>
> ```ShellSession
> root@ubuntu-vbox:~# netstat -natup | grep nginx | grep -v tcp6
> tcp        0      0 0.0.0.0:83              0.0.0.0:*               LISTEN      1177/nginx: master
> tcp        0      0 0.0.0.0:446             0.0.0.0:*               LISTEN      1177/nginx: master
> tcp        0      0 192.168.41.250:83       192.168.41.250:41524    ESTABLISHED 5715/nginx: worker
> ```
>
> 4. Откроем на другой машине в браузере адрес: `http://192.168.41.250:83/` и проверим сокеты снова:
>
> ```ShellSession
> root@ubuntu-vbox:~# netstat -natup | grep nginx | grep -v tcp6
> tcp        0      0 0.0.0.0:83              0.0.0.0:*               LISTEN      1177/nginx: master
> tcp        0      0 0.0.0.0:446             0.0.0.0:*               LISTEN      1177/nginx: master
> tcp        0      0 192.168.41.250:83       192.168.41.250:41524    ESTABLISHED 5715/nginx: worker
> tcp        0      0 192.168.41.250:83       192.168.41.2:57014      ESTABLISHED 5715/nginx: worker
> ```
>
> При осуществлении подключения к порту `tcp 83` процесс `nginx: master` порождает дочерний процесс `nginx: worker`   
>
> ```ShellSession
> root@ubuntu-vbox:~# pstree -ps 5715
> systemd(1)---nginx(1177)---nginx(5715)
> ```
>
> В данном случае, один и тот же `worker` обслуживает несколько подключений, однако это может быть изменено в настройках `nginx`.  
> Для нескольких подключений этот процесс использует один и тот же локальный TCP сокет `192.168.41.250:83`  
> В качество удаленных сокетов используются IP адреса машин и данамически выделенные порты: `192.168.41.250:41524` и `192.168.41.2:57014`  
> Cостоние сокетов в данном примере - `ESTABLISHED`, т.е. установлено.  
> Процесс `nginx: master` при этом продолжает прослушивать порты `tcp 83` и `446/tcp` на любом локальном адресе.

#### 5 Анализ строки:  
```
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      629/systemd-resolve
```

- Анализ содержимого строки:  
  - `tcp` - протокол, используемый сокетом: `TCP`;  
  - `0      0` - счетчик байт не скопированных программой из этого сокета и счетчик байт не подтверждённых удалённым узлом;  
  - `127.0.0.53:53` - адрес и номер порта локального сокета: локальный IP адрес машины `127.0.0.53`, порт `TCP 53`;  
  - `0.0.0.0:*` - адрес и номер порта удаленного сокета: любой удаленный адрес, любой `TCP` порт;  
  - `LISTEN` - состояние сокета: порт `TCP 53` прослушивается (Сокет ожидает входящих подключений);
  - `629/systemd-resolve` - идентификатор процесса и имя процесса, владеющего сокетом: `PID = 629; Process name = systemd-resolve`.

- Анализ порта `53/tcp`:
  - Порт `53/tcp` назначен службе `domain` (локальный сервер доменных имен).

```ShellSession
root@ubuntu-vbox:~# cat /etc/services | grep '\s53/tcp'
domain          53/tcp                          # Domain Name Server
```

- Анализ информации о процессе `PID 629`:
  - запущен системным менеджером Linux `systemd`;  
  - запущен от пользователся `systemd+`;  
  - исполняемый файл находится в директории `/lib/systemd/systemd-resolved`.

```ShellSession
root@ubuntu-vbox:~# pstree -ps 629
systemd(1)---systemd-resolve(629)
root@ubuntu-vbox:~# ps -aux | grep [6]29
systemd+   629  0.0  0.0  70760  6112 ?        Ss   фев02   0:00 /lib/systemd/systemd-resolved
```

- Анализ информации о службе `systemd-resolved`:  
  - исполняемый файл предоставляет локальный `DNS` сервер;  
  - исполняемый файл входит в пакет `systemd`;  
  - `systemd-resolved` предоставляет локальный DNS на `loopback` интерфейсе `127.0.0.53`.
  
```ShellSession
root@ubuntu-vbox:~# whatis systemd-resolved
systemd-resolved (8) - Network Name Resolution manager
root@ubuntu-vbox:~# apt-file search /lib/systemd/systemd-resolved
systemd: /lib/systemd/systemd-resolved
root@ubuntu-vbox:~# man systemd-resolved
...
DESCRIPTION
       systemd-resolved is a system service that provides network name resolution to local
       applications. It implements a caching and validating DNS/DNSSEC stub resolver, as well as
       an LLMNR resolver and responder. Local applications may submit network name resolution
       requests via three interfaces:
...
       .   Additionally, systemd-resolved provides a local DNS stub listener on IP address
           127.0.0.53 on the local loopback interface. Programs issuing DNS requests directly,
           bypassing any local API may be directed to this stub, in order to connect them to
           systemd-resolved. Note however that it is strongly recommended that local programs use
           the glibc NSS or bus APIs instead (as described above), as various network resolution
           concepts (such as link-local addressing, or LLMNR Unicode domains) cannot be mapped to
           the unicast DNS protocol.
...
```

- Резюмируем: 
 
TCP сокет `127.0.0.53:53` используется службой *systemd-resolved* для работы локального `DNS` сервера, который ускоряет работу, запоминая (кэшируя) ответы на повторяющиеся DNS-запросы и отвечая на них гораздо быстрее, чем внешние DNS-серверы.

#### 6 Анализ строки:  
```
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1157/sshd
```

- Анализ содержимого строки:  
  - `tcp` - протокол, используемый сокетом: `TCP`;  
  - `0      0` - счетчик байт не скопированных программой из этого сокета и счетчик байт не подтверждённых удалённым узлом;  
  - `0.0.0.0:22` - адрес и номер порта локального сокета: любой локальный IP адрес машины, порт `TCP 22`;  
  - `0.0.0.0:*` - адрес и номер порта удаленного сокета: любой удаленный адрес, любой `TCP` порт;  
  - `LISTEN` - состояние сокета: порт `TCP 22` прослушивается (Сокет ожидает входящих подключений);
  - `1157/sshd` - идентификатор процесса и имя процесса, владеющего сокетом: `PID = 1157; Process name = sshd`.

- Анализ порта `22/tcp`:
  - Порт `22/tcp` назначен службе `ssh`.

```ShellSession
cbpi@ubuntu-vbox:~ $ cat /etc/services | grep '\s22/tcp'
ssh             22/tcp                          # SSH Remote Login Protocol
```

- Анализ информации о процессе `PID 1157`:
  - запущен системным менеджером Linux `systemd`;  
  - запущен от пользователся `root`;  
  - исполняемый файл находится в директории `/usr/sbin/sshd`.

```ShellSession
cbpi@ubuntu-vbox:~ $ pstree -ps 1157
systemd(1)---sshd(1157)-T-sshd(2693)---sshd(3003)---bash(3005)---pstree(6214)
                        L-sshd(2695)---sshd(3004)---sftp-server(3010)
cbpi@ubuntu-vbox:~ $ ps -aux | grep [1]157
root      1157  0.0  0.0  72296  5760 ?        Ss   фев02   0:00 /usr/sbin/sshd -D
```

- Анализ информации о службе `ssh`:  
  - исполняемый файл предоставляет службу клиента для подключений по протоколу `ssh`;  
  - исполняемый файл входит в пакет `openssh-server`.
  
```ShellSession
cbpi@ubuntu-vbox:~ $ whatis ssh
ssh (1)              - OpenSSH SSH client (remote login program)
cbpi@ubuntu-vbox:~ $ apt-file search /usr/sbin/sshd
openssh-server: /usr/sbin/sshd
cbpi@ubuntu-vbox:~ $ man ssh
...
DESCRIPTION
     ssh (SSH client) is a program for logging into a remote machine and for executing commands on
     a remote machine.  It is intended to provide secure encrypted communications between two
     untrusted hosts over an insecure network.  X11 connections, arbitrary TCP ports and
     UNIX-domain sockets can also be forwarded over the secure channel.
...
```

- Резюмируем: 
 
TCP сокет `0.0.0.0:22` используется службой *ssh* для обеспечения возможности подключения по одноименному протоколу.

#### 7 Анализ строки:  
```
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      4233/cupsd
```

- Анализ содержимого строки:  
  - `tcp` - протокол, используемый сокетом: `TCP`;  
  - `0      0` - счетчик байт не скопированных программой из этого сокета и счетчик байт не подтверждённых удалённым узлом;  
  - `127.0.0.1:631` - адрес и номер порта локального сокета: локальный IP адрес машины `127.0.0.1`, порт `TCP 631`;  
  - `0.0.0.0:*` - адрес и номер порта удаленного сокета: любой удаленный адрес, любой `TCP` порт;  
  - `LISTEN` - состояние сокета: порт `TCP 22` прослушивается (Сокет ожидает входящих подключений);
  - `4233/cupsd` - идентификатор процесса и имя процесса, владеющего сокетом: `PID = 4233; Process name = cupsd`.

- Анализ порта `631/tcp`:
  - Порт `631/tcp` назначен службе `ipp` - *Internet Printing Protocol*.

```ShellSession
cbpi@ubuntu-vbox:~ $ cat /etc/services | grep 631/tcp
ipp             631/tcp                         # Internet Printing Protocol
```

- Анализ информации о процессе `PID 4233`:
  - запущен системным менеджером Linux `systemd`;  
  - запущен от пользователся `root`;  
  - исполняемый файл находится в директории `/usr/sbin/cupsd`.

```ShellSession
cbpi@ubuntu-vbox:~ $ pstree -ps 4233
systemd(1)---cupsd(4233)
cbpi@ubuntu-vbox:~ $ ps -aux | grep [4]233
root      4233  0.0  0.0 108108  8752 ?        Ss   00:05   0:00 /usr/sbin/cupsd -l
```

- Анализ информации о службе `cupsd`:  
  - исполняемый файл предоставляет службу планировщика для службы печати `cups`;  
  - исполняемый файл входит в пакеты `cups-client` и `cups-daemon`.
  
```ShellSession
cbpi@ubuntu-vbox:~ $ whatis cupsd
cupsd (8)            - cups scheduler
cbpi@ubuntu-vbox:~ $ apt-file search /usr/sbin/cupsd
cups-client: /usr/sbin/cupsdisable
cups-daemon: /usr/sbin/cupsd
cbpi@ubuntu-vbox:~ $ man cupsd
...
DESCRIPTION
       cupsd  is  the scheduler for CUPS. It implements a printing system based upon the Internet Printing Proto?
       col, version 2.1, and supports most of the requirements for IPP Everywhere. If no options are specified on
       the command-line then the default configuration file /etc/cups/cupsd.conf will be used.
...
```

- Резюмируем: 
 
TCP сокет `127.0.0.1:631` используется службой *cupsd* для обеспечения печати с помощью протокола `ipp` на локальной машине.

#### 8,9 Анализ строк:  
```
tcp        0      0 192.168.41.250:22       192.168.41.2:62677      ESTABLISHED 2693/sshd: cbpi [pr
tcp        0      0 192.168.41.250:22       192.168.41.2:62691      ESTABLISHED 2695/sshd: cbpi [pr
```

- Анализ содержимого строк:  
  - `tcp` - протокол, используемый сокетами: `TCP`;  
  - `0      0` - счетчик байт не скопированных программой из сокета и счетчик байт не подтверждённых удалённым узлом;  
  - `192.168.41.250:22` - адрес и номер порта локального сокета: локальный IP адрес машины `192.168.41.250`, порт `TCP 22`;  
    - `192.168.41.2:62677` - адрес и номер порта удаленного сокета: IP адрес машины `192.168.41.2`, порт `TCP 62677`;  
	- `192.168.41.2:62691` - адрес и номер порта удаленного сокета: IP адрес машины `192.168.41.2`, порт `TCP 62691`;  
  - `ESTABLISHED` - состояние сокета: порт `TCP 22` - соединение установлено;
    - `2693/sshd` - идентификатор процесса и имя процесса, владеющего сокетом: `PID = 2693; Process name = sshd`;
	- `2695/sshd` - идентификатор процесса и имя процесса, владеющего сокетом: `PID = 2695; Process name = sshd`.

- Анализ порта `22/tcp`:
  - Порт `22/tcp` назначен службе `ssh`.

```ShellSession
cbpi@ubuntu-vbox:~ $ cat /etc/services | grep '\s22/tcp'
ssh             22/tcp                          # SSH Remote Login Protocol
```

- Анализ информации о процессах `PID 2693` и `PID 2695`:
  - оба процесса запущены родительским процессом `sshd(1157)`;  
  - оба процесса запущены от пользователся `root`;  
  - процесс `PID 2693` отвечает за установленное `ssh` подключение к терминалу `pts/0`;  
  - процесс `PID 2695` отвечает за установленное `sftp` подключение;

```ShellSession
cbpi@ubuntu-vbox:~ $ pstree -ps 2693
systemd(1)---sshd(1157)---sshd(2693)---sshd(3003)---bash(3005)---pstree(7475)
cbpi@ubuntu-vbox:~ $ pstree -ps 1157
systemd(1)---sshd(1157)-T-sshd(2693)---sshd(3003)---bash(3005)---pstree(7480)
                        L-sshd(2695)---sshd(3004)---sftp-server(3010)
cbpi@ubuntu-vbox:~ $ ps -aux | grep [2]693
root      2693  0.0  0.0 107784  7132 ?        Ss   фев02   0:00 sshd: cbpi [priv]
cbpi@ubuntu-vbox:~ $ ps -aux | grep [2]695
root      2695  0.0  0.0 107784  7172 ?        Ss   фев02   0:00 sshd: cbpi [priv]
cbpi@ubuntu-vbox:~ $ ps -ef | grep '[s]shd' | grep -v ^root
cbpi      3003  2693  0 фев02 ?     00:00:03 sshd: cbpi@pts/0
cbpi      3004  2695  0 фев02 ?     00:00:00 sshd: cbpi@notty
```

- Резюмируем: 
 
TCP сокет `192.168.41.250:22` используется службой *ssh* для обеспечения активного подключения к машине по одноименному протоколу и для обеспечения обмена файлами по протоколу `sftp`.  

#### 10 Анализ строки:  
```
tcp        0      0 192.168.41.250:34434    192.168.41.252:445      ESTABLISHED -
```

- Анализ содержимого строки:  
  - `tcp` - протокол, используемый сокетом: `TCP`;  
  - `0      0` - счетчик байт не скопированных программой из этого сокета и счетчик байт не подтверждённых удалённым узлом;  
  - `192.168.41.250:34434` - адрес и номер порта локального сокета: локальный IP адрес машины `192.168.41.250`, порт `TCP 34434`;  
  - `192.168.41.252:445` - адрес и номер порта удаленного сокета: IP адрес машины `192.168.41.252`, порт `TCP 445`;  
  - `ESTABLISHED` - состояние сокета: порт `TCP 34434` - соединение установлено;
  - информация о процессе, который использует локальный сокет отсутствует.

- В файле `/etc/services` отсутствует соответствие порту `TCP 34434`.

Удаленный сокет `192.168.41.252:445` используется сетевым хранилищем *Synology NAS* для обмена файлами по протоколу *CIFS*.

> 1. Размонтируем подключенный сетевой диск *Synology*:
>
> ```ShellSession
> cbpi@ubuntu-vbox:~ $ sudo umount /media/cbpi/*
> 
> ```
> 
> 2. Проверим сокеты:
>
> ```ShellSession
> cbpi@ubuntu-vbox:~ $ sudo netstat -natup | grep -Pv '^(tcp|udp)6' | grep :34434
> tcp        0      0 192.168.41.250:34434    192.168.41.252:445      TIME_WAIT   -
> cbpi@ubuntu-vbox:~ $ sudo netstat -natup | grep -Pv '^(tcp|udp)6' | grep :34434
> cbpi@ubuntu-vbox:~ $
> ```
>
> Сразу после размонтирования сетевых дисков сокет перешел в состояние `TIME_WAIT` - 
> ожидание после закрытия для обработки пакетов всё ещё находящихся в сети, после чего был окончательно закрыт.
>
> 3. Смонтируем сетевой диск и проверим сокеты:
> 
> ```ShellSession
> cbpi@ubuntu-vbox:~ $ sudo ~/bin/mntSyn
> cbpi@ubuntu-vbox:~ $ sudo netstat -natup | grep 252:445
> tcp        0      0 192.168.41.250:34966    192.168.41.252:445      ESTABLISHED -
> ```

- Резюмируем: 
 
TCP сокет `192.168.41.250:34434` (как и сокет `192.168.41.250:34966`) используется для обмена данными с сетевым хранилищем.  
Порт для сокета выделяется динамически при монтировании сетевого диска.  

# **Задание 2**
Выбрать из приведенных примеров эхо-сервера (либо написать самостоятельно на любом языке, или найти в интернете), запустить на машине (можно виртуальной или VDS, если используете).
Примеры эхо-сервера:
```
    echo7.c — пример на С,
    phpecho.php — пример на PHP,
    pyecho.py и pyecho2.php — примеры на Python/  
	#### Также доступны примеры на Java от нашего преподавателя Алексея Степченко, простой пример эхо-сервера и клиента в архиве samples.zip и более сложный пример в simple_server.zip.  
	#### Используя Telnet, Putty (в Windows), либо собственный клиент, присоединиться одним, двумя, тремя соединениями к серверу, отследить с помощью netstat поведение и точно так же добавить в отчет.
```

---

1. Код эхо-сервера на `Python`:

```Python
import asyncore
import socket

host = ''
port = 4545
echo_answer = 'Echo: '

class EchoHandler(asyncore.dispatcher_with_send):
    def handle_read(self):
        data = self.recv(1024)
        if data == "close":
            print 'connection closed'
            self.close()
        self.send(echo_answer)
        self.send(data)

class EchoServer(asyncore.dispatcher):
    def __init__(self, host, port):
        asyncore.dispatcher.__init__(self)
        self.create_socket(socket.AF_INET, socket.SOCK_STREAM)
        self.set_reuse_addr()
        self.bind((host,port))
        self.listen(15)

    def handle_accept(self):
        pair = self.accept()
        if pair is not None:
            sock, addr = pair
            print 'connected to: ', addr
            handler = EchoHandler(sock)

server = EchoServer(host, port)
asyncore.loop()
```

2. Проверим сокеты до запуска эхо-сервера:

```ShellSession
cbpi@ubuntu-vbox:~/bin $ sudo netstat -natup | grep -Pv '^(tcp|udp)6'
Активные соединения с интернетом (servers and established)
Proto Recv-Q Send-Q Local Address Foreign Address State       PID/Program name
tcp        0      0 0.0.0.0:139             0.0.0.0:*               LISTEN      1201/smbd
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1177/nginx: master
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      629/systemd-resolve
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1157/sshd
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      4233/cupsd
tcp        0      0 127.0.0.1:6010          0.0.0.0:*               LISTEN      3003/sshd: cbpi@pts
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN      1177/nginx: master
tcp        0      0 0.0.0.0:445             0.0.0.0:*               LISTEN      1201/smbd
tcp        0      0 192.168.41.250:34966    192.168.41.252:445      ESTABLISHED -
tcp        0      0 192.168.41.250:22       192.168.41.2:62677      ESTABLISHED 2693/sshd: cbpi [pr
tcp        0      0 192.168.41.250:22       192.168.41.2:62691      ESTABLISHED 2695/sshd: cbpi [pr
udp        0      0 0.0.0.0:5353            0.0.0.0:*                           722/avahi-daemon: r
udp        0      0 0.0.0.0:47032           0.0.0.0:*                           722/avahi-daemon: r
udp        0      0 127.0.0.53:53           0.0.0.0:*                           629/systemd-resolve
udp        0      0 0.0.0.0:68              0.0.0.0:*                           860/dhclient
udp        0      0 192.168.41.255:137      0.0.0.0:*                           1145/nmbd
udp        0      0 192.168.41.250:137      0.0.0.0:*                           1145/nmbd
udp        0      0 0.0.0.0:137             0.0.0.0:*                           1145/nmbd
udp        0      0 192.168.41.255:138      0.0.0.0:*                           1145/nmbd
udp        0      0 192.168.41.250:138      0.0.0.0:*                           1145/nmbd
udp        0      0 0.0.0.0:138             0.0.0.0:*                           1145/nmbd
udp        0      0 0.0.0.0:631             0.0.0.0:*                           4234/cups-browsed
```

3. Запустим эхо сервер:

```ShellSession
cbpi@ubuntu-vbox:~/bin $ python echo.py
```

4. Проверим сокеты снова:

```ShellSession
cbpi@ubuntu-vbox:~/bin $ sudo netstat -natup | grep :4545
tcp        0      0 0.0.0.0:4545            0.0.0.0:*               LISTEN      8121/python       
```

- Анализ содержимого строк:  
  - `tcp` - протокол, используемый сокетом: `TCP`;  
  - `0      0` - счетчик байт не скопированных программой из этого сокета и счетчик байт не подтверждённых удалённым узлом;  
  - `0.0.0.0:4545` - адрес и номер порта локального сокета: любой локальный IP адрес машины, порт `TCP 4545`;  
  - `0.0.0.0:*` - адрес и номер порта удаленного сокета: любой удаленный адрес, любой `TCP` порт;  
  - `LISTEN` - состояние сокета: порты `TCP 139` и `TCP 445` прослушиваются (сокеты ожидают входящих подключений);
  - `8121/python` - идентификатор процесса и имя процесса, владеющего сокетами: `PID = 8121; Process name = python`.

5. Подключимся к машине с помощью `PuTTY` используя тип подключения `RAW` и указанный в коде порт `4545`:

- Сервер:

```ShellSession
cbpi@ubuntu-vbox:~/bin $ python echo.py 
connected to:  ('192.168.41.2', 61214)
```

- Клиент:

```
hello?
Echo: hello?
```

6. Проверим сокеты:

```ShellSession
cbpi@ubuntu-vbox:~/bin $ sudo netstat -natup | grep :4545
tcp        0      0 0.0.0.0:4545            0.0.0.0:*               LISTEN      8121/python
tcp        0      0 192.168.41.250:4545     192.168.41.2:61214      ESTABLISHED 8121/python
```

Было установлено соединение:  
  - Локальный сокет: `192.168.41.250:4545`  
  - Удаленный сокет: `192.168.41.2:61214`

При этом, процесс `8121/python` продолжает прослушивать порт `TCP 4545` на любом локальном IP адресе машины.

7. Осуществим еще одно подключение к эхо-серверу и проверим сокеты:

```ShellSession
cbpi@ubuntu-vbox:~/bin $ sudo netstat -natup | grep :4545
tcp        0      0 0.0.0.0:4545            0.0.0.0:*               LISTEN      8121/python
tcp        0      0 192.168.41.250:4545     192.168.41.2:61273      ESTABLISHED 8121/python
tcp        0      0 192.168.41.250:4545     192.168.41.2:61214      ESTABLISHED 8121/python
```

Было установлено еще одно соединение:  
  - Локальный сокет: `192.168.41.250:4545`  
  - Удаленный сокет: `192.168.41.2:61273`

Процесс `8121/python` продолжает прослушивать порт `TCP 4545` на любом локальном IP адресе машины.

8. Добавим третье соединение и проверим сокеты:

```ShellSession
cbpi@ubuntu-vbox:~/bin $ sudo netstat -natup | grep :4545
tcp        0      0 0.0.0.0:4545            0.0.0.0:*               LISTEN      8121/python
tcp        0      0 192.168.41.250:4545     192.168.41.2:61273      ESTABLISHED 8121/python
tcp        0      0 192.168.41.250:4545     192.168.41.2:61367      ESTABLISHED 8121/python
tcp        0      0 192.168.41.250:4545     192.168.41.2:61214      ESTABLISHED 8121/python
```

Было установлено еще одно соединение:  
  - Локальный сокет: `192.168.41.250:4545`  
  - Удаленный сокет: `192.168.41.2:61367`

Процесс `8121/python` продолжает прослушивать порт `TCP 4545` на любом локальном IP адресе машины.

9. Закроем все подключения отправкой команды `close` эхо серверу и проверим сокеты:

- Эхо сервер:

```
connected to:  ('192.168.41.2', 61214)
connected to:  ('192.168.41.2', 61273)
connected to:  ('192.168.41.2', 61367)
connection closed
connection closed
connection closed
```

- Сокеты:

```ShellSession
cbpi@ubuntu-vbox:~/bin $ sudo netstat -natup | grep :4545
tcp        0      0 0.0.0.0:4545            0.0.0.0:*               LISTEN      8121/python
```

---
