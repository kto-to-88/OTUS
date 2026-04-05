# Лабораторная работа. Настройка и проверка расширенных списков контроля доступа.
## Топология

![](./jpg/1.PNG)

## Таблица адресации

![](./jpg/2.PNG)

## Таблица VLAN

|    VLAN          |  NAME          |   Назначенный интерфейс                                           |
|-----------------:|:---------------|------------------------------------------------------------------:|
|      20          |     Management | S2: F0/5                                                          | 
|      30          |     Operations | S1: F0/6                                                          |
|       40         |    Sales       | S2: F0/18                                                         |
|      999         |   ParkingLot   | S1: F0/2-4, F0/7-24, G0/1-2 S2: F0/2-4, F0/6-17, F0/19-24, G0/1-2 |
|        1000      |  Собственная   |                                                                   |



## Часть 1. Создание сети и настройка основных параметров устройства
### Шаг 1. Создайте сеть согласно топологии.
**Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.**

***Подключение согласно топологии выполнено. В силу невозможности включения сервера на R1, добавлен отдельный сервер, подключенный через коммутатор KVAZI_SWITCH***

### Шаг 2. Произведите базовую настройку маршрутизаторов.

a.	Назначьте маршрутизатору имя устройства.
```
hostname R1
```
b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
```
no ip domain-lookup
```
c.	Назначьте cisco в качестве зашифрованного пароля привилегированного режима EXEC.
```
enable secret 5 $1$mERr$hx5rVt7rPNoS4wqbXKX7m0
```
d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
```
line con 0
 exec-timeout 60 0
 password cisco
```
e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
```
line vty 0 4
 login local
```
f.	Зашифруйте открытые пароли.
```
service password-encryption
```
g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
```
banner motd ^C
***************STOP!!!******************^C
```
h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R1#copy running-config startup-config 
```
### Шаг 3. Настройте базовые параметры каждого коммутатора.

a.	Присвойте коммутатору имя устройства.
```
hostname S1
```
b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
```
ip domain-name otus.ru
```
c.	Назначьте cisco в качестве зашифрованного пароля привилегированного режима EXEC.
```
enable secret 5 $1$mERr$hx5rVt7rPNoS4wqbXKX7m0
```
d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
```
enable secret 5 $1$mERr$hx5rVt7rPNoS4wqbXKX7m0

```
e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
```
line con 0
 password 7 0822455D0A16
```
f.	Зашифруйте открытые пароли.
```
service password-encryption
```
g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
```
banner motd ^C
************STOP!!!*************^C
```
h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R1#copy running-config startup-config 
```

## Часть 2. Настройка сетей VLAN на коммутаторах.

### Шаг 1. Создайте сети VLAN на коммутаторах.

**a.	Создайте необходимые VLAN и назовите их на каждом коммутаторе из приведенной выше таблицы.**
```
S1# sh vl br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
20   MGMT                             active    
30   Operations                       active    Fa0/6
40   Sales                            active    
999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/7
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
1000 NATIVE                           active    

S2#sh vl br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
20   MGMT                             active    Fa0/5
40   SALES                            active    Fa0/18
999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/6
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
1000 NATIVE                           active    

```
**b.	Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации.**
```
S1: interface Vlan20
 ip address 10.20.0.2 255.255.255.0
ip default-gateway 10.20.0.1
```
**c.	Назначьте все неиспользуемые порты коммутатора VLAN Parking Lot, настройте их для статического режима доступа и административно деактивируйте их.**
 ```
S2#sh vl br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------

999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/6
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2

 interface FastEthernet:   Fa0/2, Fa0/3, Fa0/4, Fa0/6 Fa0/7, Fa0/8, Fa0/9, Fa0/10
                           Fa0/11, Fa0/12, Fa0/13, Fa0/14, Fa0/15, Fa0/16, Fa0/17, Fa0/19
                           Fa0/20, Fa0/21, Fa0/22, Fa0/23, Fa0/24, Gig0/1, Gig0/2
 switchport access vlan 999
 switchport mode access
 shutdown 
```
### Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора.

**a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.**
```

```
**b.	Выполните команду show vlan brief, чтобы убедиться, что сети VLAN назначены правильным интерфейсам.**
```
S1# sh vl br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
20   MGMT                             active    
30   Operations                       active    Fa0/6
40   Sales                            active    
999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/7
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
1000 NATIVE                           active    

S2#sh vl br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
20   MGMT                             active    Fa0/5
40   SALES                            active    Fa0/18
999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/6
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
1000 NATIVE                           active    
```

## Часть 3. ·Настройте транки (магистральные каналы).

### Шаг 1. Вручную настройте магистральный интерфейс F0/1.

**a.	Измените режим порта коммутатора на интерфейсе F0/1, чтобы принудительно создать магистральную связь. Не забудьте сделать это на обоих коммутаторах.**
```
interface FastEthernet0/1
 switchport trunk native vlan 1000
 switchport trunk allowed vlan 20,30,40,1000
 switchport mode trunk
 switchport nonegotiate
```
**b.	В рамках конфигурации транка установите для native vlan значение 1000 на обоих коммутаторах. При настройке двух интерфейсов для разных собственных VLAN сообщения об 
ошибках могут отображаться временно.**
```
1000 NATIVE                           active   
```
**c.	В качестве другой части конфигурации транка укажите, что VLAN 20, 30, 40 и 1000 разрешены в транке.**
```
switchport trunk allowed vlan 20,30,40,1000
```
**d.	Выполните команду show interfaces trunk для проверки портов магистрали, собственной VLAN и разрешенных VLAN через магистраль.**
```
S1#sh interfaces trunk 
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000
Fa0/5       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       20,30,40,1000
Fa0/5       20,30,40,1000
```

### Шаг 2. Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.

**a.	Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.**
```
interface FastEthernet0/5
 switchport trunk native vlan 1000
 switchport trunk allowed vlan 20,30,40,1000
 switchport mode trunk
 switchport nonegotiate
```
**b.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.**
```
S1#copy running-config startup-config 
```
**c.	Используйте команду show interfaces trunk для проверки настроек транка.**
```
S1#sh int tr
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000
Fa0/5       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       20,30,40,1000
Fa0/5       20,30,40,1000
```
Часть 4. Настройте маршрутизацию.

### Шаг 1. Настройка маршрутизации между сетями VLAN на R1.

**a.	Активируйте интерфейс G0/0/1 на маршрутизаторе.**
```
interface GigabitEthernet0/1
no sh
```
**b.	Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации.
Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейс для 
собственной VLAN не имеет назначенного IP-адреса. Включите описание для каждого подинтерфейса.**
```
interface GigabitEthernet0/1.20
 encapsulation dot1Q 20
 ip address 10.20.0.1 255.255.255.0
!
interface GigabitEthernet0/1.30
 encapsulation dot1Q 30
 ip address 10.30.0.1 255.255.255.0
 ip access-group OPERATIONS_NO-PING_SALEL in
!
interface GigabitEthernet0/1.40
 encapsulation dot1Q 40
 ip address 10.40.0.1 255.255.255.0
 ip access-group SALES_NO_SSH_TO_MGMT in
!
interface GigabitEthernet0/1.1000
 encapsulation dot1Q 1000 native
 no ip address
```
**c.	Настройте интерфейс Loopback 1 на R1 с адресацией из приведенной выше таблицы.**
```
interface Loopback1
 ip address 172.16.1.1 255.255.255.0
```
**d.	С помощью команды show ip interface brief проверьте конфигурацию подынтерфейса.**
```
R1#sh ip interface brief 
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/1.20  10.20.0.1       YES manual up                    up 
GigabitEthernet0/1.30  10.30.0.1       YES manual up                    up 
GigabitEthernet0/1.40  10.40.0.1       YES manual up                    up 
GigabitEthernet0/1.1000unassigned      YES unset  up                    up 
```
### Шаг 2. Настройка интерфейса R2 g0/0/1 с использованием адреса из таблицы и маршрута по умолчанию с адресом следующего перехода 10.20.0.1
```
interface GigabitEthernet0/1
 ip address 10.20.0.4 255.255.255.0
 duplex auto
 speed auto
!
ip route 0.0.0.0 0.0.0.0 10.20.0.1 
```
















