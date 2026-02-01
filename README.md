## Модуль №1. Настройка сетевой инфраструктуры  

[Задание модуля](https://github.com/akkullow/demo2025_SySA/blob/main/task/mod1_demo25_task.pdf)

***Топология сети***
<br>

![Топология сети](https://thumb.cloud.mail.ru/weblink/thumb/xw1/FaD5/1rM9dzh7e)



###  Задание 1.  Произведите базовую настройку устройств (решено)  



● Настройте имена устройств согласно топологии. Используйте полное доменное имя 
● На всех устройствах необходимо сконфигурировать IPv4  
● IP-адрес должен быть из приватного диапазона, в случае, если сеть локальная, согласно RFC1918 
● Локальная сеть в сторону HQ-SRV(VLAN100) должна вмещать не более 64 адресов   
● Локальная сеть в сторону HQ-CLI(VLAN200) должна вмещать не более 16 адресов 
● Локальная сеть в сторону BR-SRV должна вмещать не более 32 адресов  
● Локальная сеть для управления(VLAN999) должна вмещать не более 8  адресов
● Сведения об адресах занесите в отчёт, в качестве примера используйте Таблицу 3  


<details>
<summary> Настройка имен на устройствах </summary>
<br/>

Полное имя можно подглядеть в таблице с DNS записями. Полное наименование делается только на хостах!
| Устройство | FQDN имя            |
| ---------- | ------------------- | 
| ISP        | ISP                 |
| HQ-RTR     | hq-rtr.au-team.irpo |
| BR-RTR     | br-rtr.au-team.irpo |
| HQ-SRV     | hq-srv.au-team.irpo |
| HQ-CLI     | hq-cli.au-team.irpo |
| BR-SRV     | br-srv.au-team.irpo |

**ISP:**
```
hostnamectl set-hostname ISP; exec bash
```

**HQ-RTR:**
<details> <summary> vESR </summary>

```
config
hostname hq-rtr.au-team.irpo
exit
commit
confirm
```
</details>
<details>
<summary> ALT Server </summary>

```
hostnamectl set-hostname hq-rtr.au-team.irpo; exec bash
```
</details>

<br/>

**BR-RTR:**
```
config
hostname br-rtr.au-team.irpo
exit
commit
confirm
```

**HQ-SRV:**
```
hostnamectl set-hostname hq-srv.au-team.irpo; exec bash
```

**BR-SRV:**
```
hostnamectl set-hostname br-srv.au-team.irpo; exec bash
```

**HQ-CLI:**
```
hostnamectl set-hostname hq-cli.au-team.irpo; exec bash
```
</details>


<details>
<summary> Распределение IPv4 адресов и настройка IP адрессации  </summary>

  ● IP-адрес должен быть из приватного диапазона, в случае, если сеть локальная, согласно [RFC1918](https://ru.wikipedia.org/wiki/%D0%A7%D0%B0%D1%81%D1%82%D0%BD%D1%8B%D0%B9_IP-%D0%B0%D0%B4%D1%80%D0%B5%D1%81)  
  ● Локальная сеть в сторону HQ-SRV(VLAN100) должна вмещать не более 64 адресов    
  ***Используем /26 маску***  
  ● Локальная сеть в сторону HQ-CLI(VLAN200) должна вмещать не более 16 адресов      
  ***Используем /28 маску***   
  ● Локальная сеть в сторону BR-SRV должна вмещать не более 32 адресов               
  ***Используем /27 маску***  
  ● Локальная сеть для управления(VLAN999) должна вмещать не более 8 адресов         
  ***Используем /29 маску***  

Таблица IP адрессации
<br/>

Данную информацию необходимо внести в таблицу 3. (пункт интерфейс и шлюз пропускаем при заполнении)
<details> 
<summary> vESR </summary>

| Имя устройства | Интерфейс | IP           | Маска           | Префикс      | Шлюз         |
| -------------- | --------- | ------------ | --------------- | ------------ | ------------ |
| ISP            | ens32     | DHCP         | --------------- | ------------ | ------------ |
|                | ens36     | 172.16.5.1   | 255.255.255.240 | (/28)        | ------------ |
|                | ens35     | 172.16.4.1   | 255.255.255.240 | (/28)        | ------------ |
| HQ-RTR         | gi1/0/1   | 172.16.4.2   | 255.255.255.240 | (/28)        | 172.16.4.1   |
|                | gi1/0/2   | 192.168.0.1  | 255.255.255.192 | (/26)        | ------------ |
|                | gi1/0/3   | 192.168.1.1  | 255.255.255.240 | (/28)        | ------------ |
|                | gi1/0/4   | 192.168.99.1 | 255.255.255.248 | (/29)        | ------------ |
| BR-RTR         | gi1/0/1   | 172.16.5.2   | 255.255.255.240 | (/28)        | 172.16.5.1   |
|                | gi1/0/2   | 192.168.2.1  | 255.255.255.224 | (/27)        | ------------ |
| HQ-SRV         | ens192    | 192.168.0.2  | 255.255.255.192 | (/26)        | 192.168.0.1  |
| HQ-CLI         | -         | DHCP         | --------------- | ------------ | ------------ |
| BR-SRV         | ens192    | 192.168.2.2  | 255.255.255.224 | (/27)        | 192.168.2.1  |

</details>

<details> 
<summary> ALT Server </summary>

| Имя устройства | Интерфейс | IP           | Маска           | Префикс      | Шлюз         |
| -------------- | --------- | ------------ | --------------- | ------------ | ------------ |
| ISP            | ens32     | DHCP         | --------------- | ------------ | ------------ |
|                | ens36     | 172.16.5.1   | 255.255.255.240 | (/28)        | ------------ |
|                | ens35     | 172.16.4.1   | 255.255.255.240 | (/28)        | ------------ |
| HQ-RTR         | ens32     | 172.16.4.2   | 255.255.255.240 | (/28)        | 172.16.4.1   |
|                | ens33     | 192.168.0.1  | 255.255.255.192 | (/26)        | ------------ |
|                | ens34     | 192.168.1.1  | 255.255.255.240 | (/28)        | ------------ |
|                | ens35     | 192.168.99.1 | 255.255.255.248 | (/29)        | ------------ |
| BR-RTR         | gi1/0/1   | 172.16.5.2   | 255.255.255.240 | (/28)        | 172.16.5.1   |
|                | gi1/0/2   | 192.168.2.1  | 255.255.255.224 | (/27)        | ------------ |
| HQ-SRV         | ens192    | 192.168.0.2  | 255.255.255.192 | (/26)        | 192.168.0.1  |
| HQ-CLI         | -         | DHCP         | --------------- | ------------ | ------------ |
| BR-SRV         | ens192    | 192.168.2.2  | 255.255.255.224 | (/27)        | 192.168.2.1  |

</details>

**Настройка IP адрессации на роутерах**
<br/>

При первом логине на роутерах, каждый затребует смену пароля:

```
password P@ssword
commit
confirm
```

<br/>

**Перед настройкой IP адрессации необходимо в параметрах сетевых адресов выяснить MAC адреса каждого интерфейса и соотнести интерфейсы руг сдругом согласно топологии**

Настраиваем саму **IP адрессацию**:

*****Для HQ-RTR*****

<details> 
<summary> vESR </summary>

```
configure
interface gigabitethernet 1/0/1
ip address 192.168.0.1/26
ip firewall disable
exit
interface gigabitethernet 1/0/2
ip address 192.168.99.1/29
ip firewall disable
exit
interface gigabitethernet 1/0/3
ip address 172.16.4.2/28
ip firewall disable
exit
interface gigabitethernet 1/0/4
ip address 192.168.1.1/28
ip firewall disable
exit
exit
commit
confirm
```

Установка ISP в качестве шлюза по умолчанию:

```
configure
ip route 0.0.0.0/0 172.16.4.1
exit
commit
confirm
```
</details>

<details> 
<summary> ALT Server </summary>

```
vim /etc/net/ifaces/ens32/options
```

Привести файл ***/etc/net/ifaces/ens32/options*** к виду:
```
TYPE=eth
BOOTPROTO=static
CONFIG_IPV4=yes
DISABLED=no
```

Копируем файл ***/etc/net/ifaces/ens32/options*** в папки ens33, ens34, ens35.
```
cp /etc/net/ifaces/ens3{2,3}/options
cp /etc/net/ifaces/ens3{2,4}/options
cp /etc/net/ifaces/ens3{2,5}/options
# на запрос о перезаписи файла вводим y и нажимаем Enter
```

Устанавливаем IP адреса на интерфейсах:
```
echo 172.16.4.2/28 > /etc/net/ifaces/ens32/ipv4address
echo 192.168.0.1/26 > /etc/net/ifaces/ens33/ipv4address
echo 192.168.1.1/28 > /etc/net/ifaces/ens34/ipv4address
echo 192.168.99.1/29 > /etc/net/ifaces/ens35/ipv4address
```

Установка ISP в качестве шлюза по умолчанию:

```
echo default via 172.16.4.1 > /etc/net/ifaces/ens32/ipv4route
```

Установка адреса DNS сервера:

```
echo nameserver 8.8.8.8 > /etc/net/ifaces/ens32/resolv.conf
```

</details>

</br>

*****Для BR-RTR*****
```
configure
interface gigabitethernet 1/0/1
ip address 172.16.5.2/28
ip firewall disable
exit
interface gigabitethernet 1/0/2
ip address 192.168.2.1/27
ip firewall disable
exit
exit
commit
confirm
```
Установка ISP в качестве шлюза по умолчанию:
```
configure
ip route 0.0.0.0/0 172.16.5.1
exit
commit
confirm
```

**Настрока IP адрессации на серверах**

*****HQ-SRV*****
Правим файл ***/etc/net/ifaces/ens192/options***:
```
BOOTPROTO=static
TYPE=eth
CONFIG_WIRELESS=no
SYSTEMD_BOOTPROTO=static
CONFIG_IPV4=yes
DISABLED=no
NM_CONTROLLED=no
SYSTEMD_CONTROLLED=no
```

Устанавливаем IP адрес:
```
echo 192.168.0.2/26 > /etc/net/ifaces/ens192/ipv4address
```
Устанавливаем шлюз по умолчанию:
```
echo default via 192.168.0.1 > /etc/net/ifaces/ens192/ipv4route
```
Устанавливаем адрес DNS сервера:
```
echo nameserver 8.8.8.8 > /etc/net/ifaces/ens192/resolv.conf
```
Перезапускаем сервис network
```
systemctl restart network
```

*****BR-SRV*****
Правим файл ***/etc/net/ifaces/ens192/options***:
```
BOOTPROTO=static
TYPE=eth
CONFIG_WIRELESS=no
SYSTEMD_BOOTPROTO=static
CONFIG_IPV4=yes
DISABLED=no
NM_CONTROLLED=no
SYSTEMD_CONTROLLED=no
```

Устанавливаем IP адрес:
```
echo 192.168.2.2/27 > /etc/net/ifaces/ens192/ipv4address
```
Устанавливаем шлюз по умолчанию:
```
echo default via 192.168.2.1 > /etc/net/ifaces/ens192/ipv4route
```
Устанавливаем адрес DNS сервера:
```
echo nameserver 8.8.8.8 > /etc/net/ifaces/ens192/resolv.conf
```
Перезапускаем сервис network
```
systemctl restart network
```

<br/>


● Сведения об адресах занесите в отчёт, в качестве примера используйте Таблицу 3  
| Имя устройства | IP-Адрес              | Шлюз по умолчанию   |
|   ----------   |  -------------------  |        -----        |  

</details>
<br/>

### Задание 2. Настройка ISP (решено) 


● Настройте адресацию на интерфейсах:  
o Интерфейс, подключенный к магистральному провайдеру, получает адрес по DHCP  
o Настройте маршруты по умолчанию там, где это необходимо  
o Интерфейс, к которому подключен HQ-RTR, подключен к сети  
172.16.4.0/28  
o Интерфейс, к которому подключен BR-RTR, подключен к сети  
172.16.5.0/28  
o На ISP настройте динамическую сетевую трансляцию в сторону  
HQ-RTR и BR-RTR для доступа к сети Интернет  


<details>
<summary>Решение</summary>
<br/>

**Настройка интерфейса, который получает IP-адрес по DHCP**

Файл **`options`** (в директории интерфейса) приводим к следующему виду:
```
BOOTPROTO=dhcp
TYPE=eth
DISABLED=no
CONFIG_IPV4=yes
```
> **`BOOTPROTO=dhcp`** - заменили статический способ настройки адреса на динамическое получение

<br/>

**Настройка статических IP-адресов на интерфейсах**
1. Нужно преоверить созадны ли папки для интерфейсов и если их нет - создать:
2. 
```
mkdir /etc/net/ifaces/ens35/
mkdir /etc/net/ifaces/ens36/
```

2. Копируем файл **options** из каталога ens32 в каталоги ens35 и ens36 для удобства настройки
```
cp /etc/net/ifaces/ens32/options /etc/net/ifaces/ens35/options
cp /etc/net/ifaces/ens32/options /etc/net/ifaces/ens36/options
```

3. Приводим содержимое файлов **options** для ens35 и ens36 к следующему виду:
```
BOOTPROTO=static
TYPE=eth
DISABLED=no
CONFIG_IPV4=yes
```

4. Назначаем IPv4 адреса для интерфейсов
```
vim /etc/net/ifaces/ens35/ipv4address в него пишем: 172.16.4.1/28
#сохраняем, выходим

vim /etc/net/ifaces/ens36/ipv4address в него пишем: 172.16.5.1/28
#сохраняем, выходим
```

5. Перезагружаем службу **network** и проверяем адрессацию:
```
systemctl restart network
ip -c -br a
```
*IP адреса должны быть назначены всем интерфейсам и соответствовать топологии*

<br/>

**Включение маршрутизации**

В файле **`/etc/net/sysctl.conf`** изменяем строку:
```
net.ipv4.ip_forward = 1
```

Изменения в файле **`sysctl.conf`** применяем перезагрузкой службы **network**:
```
systemctl restart network
```

Изменения в файле **`sysctl.conf`** проверряем командой:
```
sysctl -a | grep ip_forward
```
*Значение net.ipv4.ip_forward должно быть 1*


**Настройка динамической сетевой трансляции (NAT)**

<details>
<summary>Через iptables - лучше использовать его</summary>
<br/>
Установка iptables:

```
apt-get update && apt-get -y install iptables
```

Навтройка правил NAT
```
iptables -A POSTROUTING -t nat -s 172.16.4.0/28 ! -d 172.16.4.0/28 -o ens192 -j MASQUERADE 
iptables -A POSTROUTING -t nat -s 172.16.5.0/28 ! -d 172.16.5.0/28 -o ens192 -j MASQUERADE 
```

Сохраняем правила NAT
```
iptables-save > /etc/sysconfig/iptables
```

</details>

<details>
<summary>Через firewalld</summary>
<br/>
Установка firewalld:

```
apt-get update && apt-get -y install firewalld && systemctl enable --now firewalld
```
Правила к исходящим пакетам (в сторону провайдера):
```
firewall-cmd --permanent --zone=public --add-interface=<<интерфейс, который смотрит в интернет>>
```
Правила к входящим пакетам (к локальной сети):
```
firewall-cmd --permanent --zone=trusted --add-interface=<<интерфейс для HQ-RTR>>
```
```
firewall-cmd --permanent --zone=trusted --add-interface=<<интерфейс для BR-RTR>>
```
Включение NAT:
```
firewall-cmd --permanent --zone=public --add-masquerade
```
Сохранение правил:
```
firewall-cmd --complete-reload
```

Проверьте работу firewalld
```
systemctl status firewalld
```

При необходимости запустите
```
systemctl enable --now firewalld
```

<br/>

Сохраняем правила:
```
iptables-save > /etc/sysconfig/iptables
```

<br/>

Включаем и добавляем **`iptables`** в автозагрузку:
```
systemctl enable --now iptables
```
</details>



</details>

<br/>


### Задание 3. Создание локальных учетных записей (решено)      

● Создайте пользователя sshuser на серверах HQ-SRV и BR-SRV  
o Пароль пользователя sshuser с паролем P@ssw0rd  
o Идентификатор пользователя 1010  
o Пользователь sshuser должен иметь возможность запускать sudo без дополнительной аутентификации.  

<details>
<summary> Решение </summary>

#### HQ-SRV | BR-SRV  
```
useradd -m -u 1010 sshuser
passwd sshuser
P@ssw0rd
P@ssw0rd
usermod -aG wheel sshuser
visudo
```
В конце файла нужно добавить следующую строку:  
```
sshuser ALL=(ALL) NOPASSWD:ALL
```
Проверяем:  
```
su - sshuser
sudo whoami
```  
● Создайте пользователя net_admin на маршрутизаторах HQ-RTR и BR-RTR  
o Пароль пользователя P@$$word  


#### HQ-RTR 


<details> 
<summary> vESR </summary>

```
configure 
username net_admin
password P@$$word
privilege 15
exit
exit
commit
confirm
```
</details>

<details> 
<summary> ALT Server </summary>

```
useradd net_admin
passwd netadmin
P@ssw0rd
P@ssw0rd
usermod -aG wheel net_admin
visudo
```
В конце файла нужно добавить следующую строку:  
```
net_admin ALL=(ALL) NOPASSWD:ALL
```
Проверяем:  
```
su - net_admin
sudo whoami
```

</details>

</br>



####  BR-RTR  
```
configure 
username net_admin
password P@$$word
privilege 15
exit
exit
commit
confirm
```

</details>
<br/>

###  Задание 4. Настройте на интерфейсе HQ-RTR в сторону офиса HQ виртуальный коммутатор (не решено):   

● Сервер HQ-SRV должен находиться в ID VLAN 100  
o Клиент HQ-CLI в ID VLAN 200  
o Создайте подсеть управления с ID VLAN 999  
o Основные сведения о настройке коммутатора и выбора реализации разделения на VLAN занесите в отчёт  

<details>
<summary>Не решено</summary>
<br/>
</details>
<br/>

###  Задание 5. Настройка безопасного удаленного доступа на серверах HQ-SRV и BR-SRV (решено)

● Для подключения используйте порт 2024  
● Разрешите подключения только пользователю sshuser  
● Ограничьте количество попыток входа до двух  
● Настройте баннер «Authorized access only»  

<details>
<summary>Решение</summary>
<br/>

Приводим указанные строки в файле **`/etc/openssh/sshd_config`** к следующим значениям:
```
Port 2024
MaxAuthTries 2
PasswordAuthentication yes
Banner /etc/openssh/bannermotd
AllowUsers  sshuser
```
> В параметре **AllowUsers** вместо пробела используется **`Tab`**

<br/>

Создаем файл **`bannermotd`**:
```
----------------------
Authorized access only
----------------------
```

<br/>

Перезагружаем службу:
```
systemctl restart sshd
```

</details>

<br/>

### Задание 6. Между офисами HQ и BR необходимо сконфигурировать IP туннель (решено)
- Сведения о туннеле занесите в отчёт  
- На выбор технологии GRE или IP in IP  



<details>
<summary>Решение</summary>
<br/>

**Создаем интерфейс *****GRE*****-туннеля на *****HQ-RTR*****:**

<details>
<summary> vESR </summary>

**Для HQ-RTR**

```
tunnel gre 1
ttl 16
ip firewall disable
local address 172.16.4.2
remote address 172.16.5.2
ip address 172.16.1.1/30
enable
exit
exit
commit
confirm
```

```
configure
ip route 192.168.2.0/27 172.16.1.2
end
commit confirm
```

**Для BR-RTR**

```
tunnel gre 1
ttl 16
ip firewall disable
local address 172.16.5.2
remote address 172.16.4.2
ip address 172.16.1.2/30
enable
exit
exit
commit
confirm
```


```
configure
ip route 192.168.0.0/26 172.16.1.1
ip route 192.168.1.0/28 172.16.1.1
end
commit confirm
```

</details>

<details>
<summary> ALT Server </summary>

**Включение маршрутизации**

В файле **`/etc/net/sysctl.conf`** изменяем строку:

```
net.ipv4.ip_forward = 1
```

Изменения в файле **`sysctl.conf`** применяем перезагрузкой службы **network**:

```
systemctl restart network
```

Изменения в файле **`sysctl.conf`** проверряем командой:

```
sysctl -a | grep ip_forward
```
*Значение net.ipv4.ip_forward должно быть 1*


**Настройка туннеля на HQ-RTR**

```
mkdir /etc/net/ifaces/tun1
vim /etc/net/ifaces/tun1/options 
# в файл пишем:
TYPE=iptun                #тип интерфейса
TUNTYPE=gre               #тип туннеля
TUNLOCAL=172.16.4.2       #адрес ens32 на HQ-RTR
TUNREMOTE=172.16.5.2      #адрес gi1/0/1 на BR-RTR
TUNOPTIONS='ttl 16'       #время жизни пакета в туннеле
HOST=ens32                #интрефейс HQ-RTR, на котором поднимается GRE
```
**Настройка IP адреса интерфейса туннеля на HQ-RTR**

```
echo 172.16.1.1/30 > /etc/net/ifaces/tun1/ipv4address
```

**Настройка стутического маршрута в сеть BR-Net через туннель на HQ-RTR**

```
echo 192.168.2.0/27 via 172.16.1.2 > /etc/net/ifaces/tun1/ipv4route
```

**Перезашружаем службу ***network*** на HQ-RTR **:

```
systemctl restart network
```

**Проверяем наличие туннеля на HQ-RTR**

```
ip -c a
```
*****Должен быть интерфейс tun1 с адресом 172.16.1.1/30*****






**Настройка туннеля на BR-RTR**

```
mkdir /etc/net/ifaces/tun1
vim /etc/net/ifaces/tun1/options 
# в файл пишем:
TYPE=iptun                #тип интерфейса
TUNTYPE=gre               #тип туннеля
TUNLOCAL=172.16.5.2       #адрес ens32 на BR-RTR
TUNREMOTE=172.16.4.2      #адрес gi1/0/1 на HQ-RTR
TUNOPTIONS='ttl 16'       #время жизни пакета в туннеле
HOST=ens32                #интрефейс BR-RTR, на котором поднимается GRE
```
**Настройка IP адреса интерфейса туннеля на BR-RTR**

```
echo 172.16.1.2/30 > /etc/net/ifaces/tun1/ipv4address
```

**Настройка стутического маршрута в сеть HQ-Net через туннель на BR-RTR**

```
echo 192.168.0.0/26 via 172.16.1.1 > /etc/net/ifaces/tun1/ipv4route
echo 192.168.1.0/28 via 172.16.1.1 > /etc/net/ifaces/tun1/ipv4route
```

**Перезашружаем службу ***network*** на BR-RTR **:

```
systemctl restart network
```

**Проверяем наличие туннеля на BR-RTR**

```
ip -c a
```
*****Должен быть интерфейс tun1 с адресом 172.16.1.2/30*****




**Проверяем работу туннеля сначала пингуя c HQ-RTR интерфейс тунеля BR-RTR, потом BR-SRV, оба пинга должны проходить**

```
ping 172.16.1.2
ping 192.168.2.2
```

</details>


<br/>

Проверка настройки GRE и статической маршрутизации: **HQ-SRV и BR-SRV должны пинговаться между собой**

</details>

<br/>

### Задание 7. Обеспечьте динамическую маршрутизацию: ресурсы одного офиса должны быть доступны из другого офиса. Для обеспечения динамической маршрутизации используйте link state протокол на ваше усмотрение (не решено) 

*****!!!В 6 задании для туннеля настроена статическая маршрутизация - офисы доступны друг для друга*****

- Разрешите выбранный протокол только на интерфейсах в ip туннеле

- Маршрутизаторы должны делиться маршрутами только друг с другом

- Обеспечьте защиту выбранного протокола посредством парольной защиты

- Сведения о настройке и защите протокола занесите в отчёт

</br>

### Задание 8. Настройка динамической трансляции адресов (решено) 


- Настройте динамическую трансляцию адресов для обоих офисов.

- Все устройства в офисах должны иметь доступ к сети Интернет


<details>
<summary>Решение</summary>
<br/>

<details>
<summary> vESR </summary>

**Создаем список локальных адресов, которысм разрешен выход в сеть**

*****Для HQ-RTR*****
```
config
object-group network LOCAL_SRV
ip address-range 192.168.0.2-192.168.0.62
exit
object-group network LOCAL_CLI
ip address-range 192.168.1.2-192.168.1.14
end
commit
confirm
```

*****Для BR-RTR*****
```
config
object-group network LOCAL_SRV
ip address-range 192.168.2.2-192.168.2.30
end
commit
confirm
```


**Конфигурируем NAT**

*****Для HQ-RTR*****
```
config
nat source
pool TRANSLATE
ip address-range 172.16.4.2
exit
ruleset SNAT
to interface gi1/0/1
rule 1
match source-address LOCAL_SRV
action source-nat pool TRANSLATE
enable
rule 2
match source-address LOCAL_CLI
action source-nat pool TRANSLATE
enable
end
commit
confirm
```

*****Для BR-RTR*****
```
config
nat source
pool TRANSLATE
ip address-range 172.16.5.2
exit
ruleset SNAT
to interface gi1/0/1
rule 1
match source-address LOCAL_SRV
action source-nat pool TRANSLATE
enable
end
commit
confirm
```
</details>

<details>
<summary> ALT Server </summary>
 

<details>
<summary>Через iptables - лучше использовать его</summary>
<br/>
  
Установка iptables:
  
```
apt-get update && apt-get -y install iptables
```

Навтройка правил NAT для HQ-RTR
```
iptables -A POSTROUTING -t nat -s 192.168.0.0/26 ! -d 192.168.0.0/26 -o ens32 -j MASQUERADE
iptables -A POSTROUTING -t nat -s 192.168.1.0/28 ! -d 192.168.1.0/28 -o ens32 -j MASQUERADE
```

Навтройка правил NAT для BR-RTR
```
iptables -A POSTROUTING -t nat -s 192.168.2.0/27 ! -d 192.168.2.0/27 -o ens32 -j MASQUERADE
```

Сохраняем правила NAT
```
iptables-save > /etc/sysconfig/iptables
```

Автозапуск iptables
```
systemctl enable --now iptables
```

</details>


<details>
<summary>Через firewalld</summary>
<br/>
  
Установка firewalld:
```
apt-get update && apt-get -y install firewalld && systemctl enable --now firewalld
```
Правила к исходящим пакетам (в сторону провайдера):
```
firewall-cmd --permanent --zone=public --add-interface=ens32    #интерфейс, который смотрит в интернет
```
Правила к входящим пакетам (к локальной сети):
```
firewall-cmd --permanent --zone=trusted --add-interface=ens33   #интерфейс для HQ-RTR
```
```
firewall-cmd --permanent --zone=trusted --add-interface=ens34   #интерфейс для BR-RTR
```
Включение NAT:
```
firewall-cmd --permanent --zone=public --add-masquerade
```
Сохранение правил:
```
firewall-cmd --complete-reload
```

Проверьте работу firewalld
```
systemctl status firewalld
```

При необходимости запустите
```
systemctl enable --now firewalld
```

<br/>

Сохраняем правила:
```
iptables-save > /etc/sysconfig/iptables
```

<br/>

Включаем и добавляем **`iptables`** в автозагрузку:
```
systemctl enable --now iptables
```
</details>

</details>

*****Проверяем пингуя с клиента IP любого общедоступнго DNS севрере в интернете (например 8.8.8.8)*****

</details>

<br/>

### Задание 9. Настройка протокола динамической конфигурации хостов (решено)

- Настройте нужную подсеть

- Для офиса HQ в качестве сервера DHCP выступает маршрутизатор HQ-RTR.

- Клиентом является машина HQ-CLI.

- Исключите из выдачи адрес маршрутизатора

- Адрес шлюза по умолчанию – адрес маршрутизатора HQ-RTR.

- Адрес DNS-сервера для машины HQ-CLI – адрес сервера HQ-SRV.

- DNS-суффикс для офисов HQ – au-team.irpo

- Сведения о настройке протокола занесите в отчёт



<details>
<summary>Решение</summary>
<br/>

*****Для HQ-RTR*****

<details>
<summary>vESR</summary>
  
```
config
ip dhcp-server pool CLIENT
network 192.168.1.0/28
address-range 192.168.1.2-192.168.1.14
domain-name "au-team.irpo"
default-router 192.168.1.1
dns-server 192.168.0.2
exit
ip dhcp-server
exit
commit
confirm
```
</details>

<details>
<summary>ALT Serves</summary>

#### Обновляем данные репозиториев
```
apt-get update
```

#### Устанавливаем пакет DHCP сервера
```
apt-get install dhcp-server
```

#### Устанавливаем интерфейс раздачи DHCP адресов
```
vim /etc/sysconfig/dhcpd   #В нем правим строчку:

DHCPDARGS=ens34   #интерфейс, который смотрит на HQ-CLI
```

#### Настраиваем DHCP сервер
```
cp /etc/dhcp/dhcpd{.conf.sample,.conf}
vim /etc/dhcp/dhcpd.conf
```
![image](https://thumb.cloud.mail.ru/weblink/thumb/xw1/gSf7/EKGpqDp6s)

#### Запускаем DHCP сервер
```
service dhcpd start
sustemctl status dhcpd
```

</details>

***После выполнения настроек можно включать машину клиента и проверять работу DHCP: клиент должен получть все параметры.***

</details>

<br/>

###  Задание 10. Настройка DNS для офисов HQ и BR (решено) 
- Основной DNS-сервер реализован на HQ-SRV.

- Сервер должен обеспечивать разрешение имён в сетевые адреса устройств и обратно в соответствии с таблицей 2

- В качестве DNS сервера пересылки используйте любой общедоступный DNS сервер

<details>

<summary>Решение</summary>
<table align="center">
  <tr>
    <td align="center">Устройство</td>
    <td align="center">Запись</td>
    <td align="center">Тип</td>
  </tr>
  <tr>
    <td align="center">HQ-RTR</td>
    <td align="center">hq-rtr.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">BR-RTR</td>
    <td align="center">br-rtr.au-team.irpo</td>
    <td align="center">A</td>
  </tr>
  <tr>
    <td align="center">HQ-SRV</td>
    <td align="center">hq-srv.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">HQ-CLI</td>
    <td align="center">hq-cli.au-team.irpo</td>
    <td align="center">A,PTR</td>
  </tr>
  <tr>
    <td align="center">BR-SRV</td>
    <td align="center">br-srv.au-team.irpo</td>
    <td align="center">A</td>
  </tr>
  <tr>
    <td align="center">HQ-RTR</td>
    <td align="center">moodle.au-team.irpo</td>
    <td align="center">CNAME</td>
  </tr>
  <tr>
    <td align="center">BR-RTR</td>
    <td align="center">wiki.au-team.irpo</td>
    <td align="center">CNAME</td>
  </tr>
</table>

<p align="center">Таблица 2</p>

<br/>

Для реализации DNS на HQ-SRV необходимо установить службу bind, а также 
создать 1 файл для зоны прямого просмотра au-team.irpo и 1 файл для зоны обратного просмотра: 
 - irpo.db для зоны au-team.irpo  
 - 0.db для зоны обратного просмотр 0.168.192.in-addr.arpa

**На HQ-SRV**

*****Установка bind*****
```
apt-get update && apt-get install -y bind
```

*****Настройка опций DNS*****

```
vim /etc/bind/options.conf

>>Правим следующие параметры:

listen-on { any; };
#listen-on_ipv6 { any; };  <- закоментировать
forward first;
forwarders { 8.8.8.8; };
allow-query { any; };
```

*****Включаем службу BIND*****
```
systemctl enable --now bind
```

*****Устанавливаем файлы DNS зон*****
```
vim /etc/bind/local.conf
>> указываем следующие параметры DNS зон:

zone "au-team.irpo" {
    type master;
    file "irpo.db";
};

zone "0.168.192.in-addr.arpa" {
    type master;
    file "0.db";
};

```

*****Копируем файлы DNS зон*****
```
cp /etc/bind/zone/{localdomain,irpo.db}
cp /etc/bind/zone/{127.in-addr.arpa,0.db}
```

*****Назначем пользователя named владельцем файлов DNS зон*****
```
chown root:named /etc/bind/zone/{irpo,0}.db
```

*****Настраиваем зону прямого просмотра в файле irpo.db:*****   
![Image](https://thumb.cloud.mail.ru/weblink/thumb/xw1/fQ8E/XM3VFxEWc)

*****Настраиваем зону обратного просмотра в файле 0.db:*****  
![Image](https://thumb.cloud.mail.ru/weblink/thumb/xw1/ASPP/sM8LgrRAy)

*****Перезапускаем службу bind*****
```
systemctl restart bind
```

*****Проверяем работу зону прямого просмотра на клиенте*****  
![Image](https://thumb.cloud.mail.ru/weblink/thumb/xw1/4Z7G/giPZDAYKu)

*****Проверяем работу зону обратного просмотра на клиенте*****  
![Image](https://thumb.cloud.mail.ru/weblink/thumb/xw1/DZ7L/ibQvx3Q8h)

*****Проверяем работу форвардинга запросов***** 
![Image](https://thumb.cloud.mail.ru/weblink/thumb/xw1/vDTa/YRQZoNHK2)

<br/>


</details>

<br/>

###  Задание 11. Настройте часовой пояс согласно месту проведению экзамена (решено)

<details>
<summary>Решение</summary>

Для настройки корректной временной зоны необходимо выполнить следующик команды: 

***Можно (и нужно) использовать Tab для автозавершения команд***

**ISP:**
```
timedatectl set-timezone Asia/Anadyr   
```


**HQ-SRV:**
```
timedatectl set-timezone Asia/Anadyr  
```

**BR-SRV:**
```
timedatectl set-timezone Asia/Anadyr  
```

**HQ-CLI:**
```
timedatectl set-timezone Asia/Anadyr  
```

**HQ-RTR:**

<details> 
<summary> ALT Server </summary>
```
timedatectl set-timezone Asia/Anadyr  
```
</details> 

<details> 
<summary> vESR </summary>
```
set date 12:00:00 20 June 2025
commit
confirm
```
</details> 

**BR-RTR:**

<details> 
<summary> ALT Server </summary>
```
timedatectl set-timezone Asia/Anadyr  
```
</details> 

<details> 
<summary> vESR </summary>
```
set date 12:00:00 20 June 2025
commit
confirm
```
</details> 

**Месяц в команде ***set date 12:00:00 20 June 2025*** принимает значения:**  
*****January February March April May June July August September October November December*****

</details>
<br/>

### Задание 12. Настройка общей папки SAMBA на HQ-SRV
<details>
<summary>Решение</summary>
  
***Скачивание пакета samba***
```
apt-get install -y samba
```
***Создаём директорию для общей папки***
```
mkdir /srv/share
```
***Даём этой директории полные права***
```
chmod 777 /srv/share
```
***Заходим в файл smb.conf***
```
nano /etc/samba/smb.conf
```
***В самом конце файла вписываем следующие строчки***
```
[share]
    comment = Public Folder
    path = /srv/share
    public = yes
    writable = yes
    read only = no
    guest ok = yes
    create mask = 0777
    directory mask = 0777
    force create mode = 0777
    force directory mode = 0777
```

> **`[share]`** - название папки (это название будут видеть пользователи)<br/>
> **`comment`** - комментарий.<br/>
> **`path`** - путь, где будет расположена общая папка.<br/>
> **`public`** - для общего доступа. Установите в yes, если хотите, чтобы все могли работать с ресурсом.<br/>
> **`writable`** - этот параметр отвечает за разрешение записи в сетевую папку.<br/>
> **`read only`** - только для чтения. Установите no, если у пользователей должна быть возможность создавать папки и файлы.<br/>
> **`guest ok`** - разрешает доступ к папке гостевой учетной записи.<br/>
> **`create mask, directory mask, force create mode, force directory mode`** — при создании новой папки или файла назначаются указанные права. В нашем примере права будут полные.<br/>

***Добавляем samba в автозагрузку***
```
systemctl enable --now smb
```
***Перезапускаем samba***
```
systemctl restart smb
```
***Проверяем работает ли samba***
```
systemctl status smb
```
***Идём на HQ-CLI, открываем "компьютер" и в адресной строке вбиваем:***
```
smb://192.168.0.2    (где 192.168.0.2 - IP HQ-SRV)
```
![image](https://github.com/champsysadm/website/blob/main/image.png)
</details>
<br/>

### Задание 13. Установка ВЕБ-СЕРВЕРА на HQ-SRV
<details>
<summary>Решение</summary>

**LAMP (Linux Apache MySql Php) - нужен для развёртывания веб-сервера и веб-сайта.**

***Установка пакета lamp***
```
apt-get install -y lamp-server
```
***Добавляем службу httpd2 (отвечает за веб-сервер) в автозагрузку***
```
systemctl enable --now httpd2
```
***Проверяем работу веб-сервера***
```
systemctl status httpd2
```
</details>
<br/>

### Задание 14. GIT на HQ-SRV
<details>
<summary>Решение</summary>

***На HQ-SRV скачиваем git - систему контроля версий. С помощью него мы будем клонировать с githab сайт и в дальнейшем распологать его на нашем веб-сервере.***
```
apt-get install -y git
```
***Переходим в папку, где должен в будущем находится сайт.***
```
cd /var/www/html
```
***Клонируем с удалённого githab сайт.***
```
git clone https://github.com/champsysadm/site.git
```
***Перемещаем все файлы из папки site в папку html***
```
mv /var/www/html/site/*  /var/www/html/
```
***На HQ-CLI открываем браузер и вписываем ip-адрес hq-srv***
![image](https://github.com/champsysadm/website/blob/main/i.png)

</details>
<br/>

### Задание 15. установка YANDEX-BROWSER на HQ-CLI
<details>
<summary>Решение</summary>
<br/>
  
***На HQ-CLI заходим в браузер и в поисковой строке пишем:***
```
yandex browser rpm
```
***В первом сайте скачиваем файл:***
```
yandex-browser-corporate
```
***Далее в терминале HQ-CLI выполняем команды:***
```
apt-get update
apt-get install -y binutils jq  squashfs-tools
```
***Затем переходим в папку загрузки***
```
cd /home/user/Загрузки
```
***Запускаем установку браузера***
```
rpm -i yandex-browser нажимаем на клавишу TAB, а потом клавишу ENTER
```
</details>
