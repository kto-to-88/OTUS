# Лабораторная работа - Конфигурация безопасности коммутатора 

## Топология

![](./jpg/1.PNG)

![](./jpg/2.PNG)

## Часть 1. Настройка основного сетевого устройства

**•	Создайте сеть.**

**•	Настройте маршрутизатор R1.**

**•	Настройка и проверка основных параметров коммутатора**

## Часть 2. Настройка сетей VLAN

**•	Сконфигруриуйте VLAN 10.**

**•	Сконфигруриуйте SVI для VLAN 10.**

**•	Настройте VLAN 333 с именем Native на S1 и S2.**

**•	Настройте VLAN 999 с именем ParkingLot на S1 и S2.**

## Часть 3: Настройки безопасности коммутатора.

**•	Реализация магистральных соединений 802.1Q.**

**•	Настройка портов доступа**

**•	Безопасность неиспользуемых портов коммутатора**

**•	Документирование и реализация функций безопасности порта.**

**•	Реализовать безопасность DHCP snooping.**

**•	Реализация PortFast и BPDU Guard**

**•	Проверка сквозной связанности.**

## Ход работы
## _____________________________________________________________________

## Часть 1. Настройка основного сетевого устройства

### Шаг 1. Создайте сеть.

**a.	Создайте сеть согласно топологии.**

**b.	Инициализация устройств**

## Шаг 2. Настройте маршрутизатор R1.

**a.	Загрузите следующий конфигурационный скрипт на R1.**
**Откройте окно конфигурации**
```
enable
configure terminal
hostname R1
no ip domain lookup
ip dhcp excluded-address 192.168.10.1 192.168.10.9
ip dhcp excluded-address 192.168.10.201 192.168.10.202
ip dhcp relay information trust-all
!
ip dhcp pool Students
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 domain-name CCNA2.Lab-11.6.1
!
interface Loopback0
 ip address 10.10.1.1 255.255.255.0
!
interface GigabitEthernet0/0/1
 description Link to S1
 ip address 192.168.10.1 255.255.255.0
 no shutdown
!
line con 0
 logging synchronous
 exec-timeout 0 0
```
**b.	Проверьте текущую конфигурацию на R1, используя следующую команду:**
```
R1# show ip interface brief
```

![](./jpg/3.PNG)

**c.	Убедитесь, что IP-адресация и интерфейсы находятся в состоянии up / up (при необходимости устраните неполадки).**

![](./jpg/4.PNG)

## Шаг 3. Настройка и проверка основных параметров коммутатора

**a.	Настройте имя хоста для коммутаторов S1 и S2.**
```
hostname S1
```
**b.	Запретите нежелательный поиск в DNS.**
```
no ip domain-lookup
```
**c.	Настройте описания интерфейса для портов, которые используются в S1 и S2.**
```
interface FastEthernet0/1
 description TO-S2
!
interface FastEthernet0/5
 description TO-R1
!
interface FastEthernet0/6
 description TO-PC-A
```
**d.	Установите для шлюза по умолчанию для VLAN управления значение 192.168.10.1 на обоих коммутаторах.**
```
ip default-gateway 192.168.10.1
```
## Часть 2. Настройка сетей VLAN на коммутаторах.

**Шаг 1. Сконфигруриуйте VLAN 10.**

**Добавьте VLAN 10 на S1 и S2 и назовите VLAN - Management.**

**Шаг 2. Сконфигруриуйте SVI для VLAN 10.**

**Настройте IP-адрес в соответствии с таблицей адресации для SVI для VLAN 10 на S1 и S2. Включите интерфейсы SVI и предоставьте описание для интерфейса.**

**Шаг 3. Настройте VLAN 333 с именем Native на S1 и S2.**

**Шаг 4. Настройте VLAN 999 с именем ParkingLot на S1 и S2.**

![](./jpg/5.PNG)

![](./jpg/6.PNG)

## Часть 3. Настройки безопасности коммутатора.

**Шаг 1. Релизация магистральных соединений 802.1Q.**

**a.	Настройте все магистральные порты Fa0/1 на обоих коммутаторах для использования VLAN 333 в качестве native VLAN.**
```
!
interface FastEthernet0/1
 description TO-S2
 switchport trunk native vlan 333
 switchport trunk allowed vlan 10,333
 switchport mode trunk
 switchport nonegotiate
```
**b.	Убедитесь, что режим транкинга успешно настроен на всех коммутаторах.**
```
S1# show interface trunk

Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      333

Port        Vlans allowed on trunk
Fa0/1       10,333

Port        Vlans allowed and active in management domain
Fa0/1       10,333

Port        Vlans in spanning tree forwarding state and not pruned
```
```
S2#show interfaces trunk 
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      333

Port        Vlans allowed on trunk
Fa0/1       10,333

Port        Vlans allowed and active in management domain
Fa0/1       10,333

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       10,333
```
**c.	Отключить согласование DTP F0/1 на S1 и S2.**
```
interface FastEthernet0/1
 description TO-S2
 switchport trunk native vlan 333
 switchport trunk allowed vlan 10,333
 switchport mode trunk
 switchport nonegotiate
```
**d.	Проверьте с помощью команды show interfaces.**
```
S1(S2)# show interfaces f0/1 switchport 
Negotiation of Trunking: Off
```

## Шаг 2. Настройка портов доступа

**a.	На S1 настройте F0/5 и F0/6 в качестве портов доступа и свяжите их с VLAN 10.**
```
!
interface FastEthernet0/5
 description TO-R1
 switchport access vlan 10
 switchport mode access
 switchport nonegotiate
!
interface FastEthernet0/6
 description TO-PC-A
 switchport access vlan 10
 switchport mode access
 switchport nonegotiate
 ```
**b.	На S2 настройте порт доступа Fa0/18 и свяжите его с VLAN 10.**
 ```
interface FastEthernet0/18
 description TO-PC-B
 switchport access vlan 10
 switchport mode access
 switchport nonegotiate
 ```

## Шаг 3. Безопасность неиспользуемых портов коммутатора

**a.	На S1 и S2 переместите неиспользуемые порты из VLAN 1 в VLAN 999 и отключите неиспользуемые порты.**

**b.	Убедитесь, что неиспользуемые порты отключены и связаны с VLAN 999, введя команду  show.**
```
S1# show interfaces status
Port      Name               Status       Vlan       Duplex  Speed Type
Fa0/1     TO-S2              connected    trunk      auto    auto  10/100BaseTX
Fa0/2                        disabled 999        auto    auto  10/100BaseTX
Fa0/3                        disabled 999        auto    auto  10/100BaseTX
Fa0/4                        disabled 999        auto    auto  10/100BaseTX
Fa0/5     TO-R1              connected    10         auto    auto  10/100BaseTX
Fa0/6     TO-PC-A            connected    10         auto    auto  10/100BaseTX
Fa0/7                        disabled 999        auto    auto  10/100BaseTX
Fa0/8                        disabled 999        auto    auto  10/100BaseTX
Fa0/9                        disabled 999        auto    auto  10/100BaseTX
Fa0/10                       disabled 999        auto    auto  10/100BaseTX
Fa0/11                       disabled 999        auto    auto  10/100BaseTX
Fa0/12                       disabled 999        auto    auto  10/100BaseTX
Fa0/13                       disabled 999        auto    auto  10/100BaseTX
Fa0/14                       disabled 999        auto    auto  10/100BaseTX
Fa0/15                       disabled 999        auto    auto  10/100BaseTX
Fa0/16                       disabled 999        auto    auto  10/100BaseTX
Fa0/17                       disabled 999        auto    auto  10/100BaseTX
Fa0/18                       disabled 999        auto    auto  10/100BaseTX
Fa0/19                       disabled 999        auto    auto  10/100BaseTX
Fa0/20                       disabled 999        auto    auto  10/100BaseTX
Fa0/21                       disabled 999        auto    auto  10/100BaseTX
Fa0/22                       disabled 999        auto    auto  10/100BaseTX
Fa0/23                       disabled 999        auto    auto  10/100BaseTX
Fa0/24                       disabled 999        auto    auto  10/100BaseTX
Gig0/1                       disabled 999        auto    auto  10/100BaseTX
Gig0/2                       disabled 999        auto    auto  10/100BaseTX
```
```
S2#show interfaces status 
Port      Name               Status       Vlan       Duplex  Speed Type
Fa0/1                        connected    trunk      auto    auto  10/100BaseTX
Fa0/2                        disabled 999        auto    auto  10/100BaseTX
Fa0/3                        disabled 999        auto    auto  10/100BaseTX
Fa0/4                        disabled 999        auto    auto  10/100BaseTX
Fa0/5                        disabled 999        auto    auto  10/100BaseTX
Fa0/6                        disabled 999        auto    auto  10/100BaseTX
Fa0/7                        disabled 999        auto    auto  10/100BaseTX
Fa0/8                        disabled 999        auto    auto  10/100BaseTX
Fa0/9                        disabled 999        auto    auto  10/100BaseTX
Fa0/10                       disabled 999        auto    auto  10/100BaseTX
Fa0/11                       disabled 999        auto    auto  10/100BaseTX
Fa0/12                       disabled 999        auto    auto  10/100BaseTX
Fa0/13                       disabled 999        auto    auto  10/100BaseTX
Fa0/14                       disabled 999        auto    auto  10/100BaseTX
Fa0/15                       disabled 999        auto    auto  10/100BaseTX
Fa0/16                       disabled 999        auto    auto  10/100BaseTX
Fa0/17                       disabled 999        auto    auto  10/100BaseTX
Fa0/18    TO-PC-B            connected    10         auto    auto  10/100BaseTX
Fa0/19                       disabled 999        auto    auto  10/100BaseTX
Fa0/20                       disabled 999        auto    auto  10/100BaseTX
Fa0/21                       disabled 999        auto    auto  10/100BaseTX
Fa0/22                       disabled 999        auto    auto  10/100BaseTX
Fa0/23                       disabled 999        auto    auto  10/100BaseTX
Fa0/24                       disabled 999        auto    auto  10/100BaseTX
Gig0/1                       disabled 999        auto    auto  10/100BaseTX
Gig0/2                       disabled 999        auto    auto  10/100BaseTX
```






























