# EIGRP

### Цели:
 ##### Настроить EIGRP в С.-Петербург
 ##### Использовать named EIGRP

### Описание/Пошаговая инструкция выполнения домашнего задания:
##### 1. В офисе С.-Петербург настроить EIGRP.
##### 2. R32 получает только маршрут по умолчанию.
##### 3. R16-17 анонсируют только суммарные префиксы.
##### 4. Использовать EIGRP named-mode для настройки сети.
##### 5. Настройка осуществляется одновременно для IPv4 и IPv6.

## ___________________________________________________________________

### Ход работы

**Часть схемы сети, над которой будет производиться настройка.**

![](./jpg/EIGRP.PNG)

##### 1. В офисе С.-Петербург настроить EIGRP.

***Ниже в пунктах 2-5 будет представлен процесс настройки EIGRP в офисе С.-Петербург***

##### 2. R32 получает только маршрут по умолчанию.

***Настроим R32 в соответствии с заданием:***

```
R32:
R32#sh run
!
hostname R32
!
ip cef
ipv6 unicast-routing
ipv6 cef
!
interface Loopback0
 ip address 32.32.32.32 255.255.255.255
 ipv6 address FC00::32/128
!
interface Ethernet0/0
 description "to R16 [e0/3]"
 ip address 10.0.0.75 255.255.255.254
 ipv6 address FC01:0:0:74::1/127
 ipv6 eigrp 100
!
router eigrp TRIADA-SPB - запустим процесс named mode
 !
 address-family ipv4 unicast autonomous-system 100 - откроем бок настроек ipv4 для АС100
  !
  topology base  - войдём в настройки текущей топологии
   distribute-list prefix ONLY-DEF in - отфильтруем приходящие маршруты
  exit-af-topology
  network 10.0.0.74 0.0.0.1 - включим EIGRP на интерфейсах из диапазона
  eigrp router-id 32.32.32.32 - зададим ID роутера
  eigrp stub receive-only
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 100
  !
  topology base
   distribute-list prefix-list ONLY-DEFv6 in - подключим фильтрацию
  exit-af-topology
  eigrp router-id 32.32.32.32
  eigrp stub receive-only - R32 тупиковый, только принимает маршруты
 exit-address-family
!
ip prefix-list ONLY_DEF seq 5 permit 0.0.0.0/0 - только точный дефолт
!
!
ipv6 prefix-list ONLY-DEFv6 seq 5 permit ::/0 - только ::/0
!
end
```

##### 3. R16-17 анонсируют только суммарные префиксы.
##### 4. Использовать EIGRP named-mode для настройки сети.
##### 5. Настройка осуществляется одновременно для IPv4 и IPv6.















