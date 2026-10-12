## Маршрутизация на основе политик (PBR) 
### 1. Настроите политику маршрутизации для сетей офиса.
### 2. Распределите трафик между двумя линками с провайдером.
### 3. Настроите отслеживание линка через технологию IP SLA.(только для IPv4)
### 4. Настройте для офиса Лабытнанги маршрут по-умолчанию.
### 5. План работы и изменения зафиксированы в документации .

## ___________________________________________________________________

### Ход работы

**Часть схемы сети, над которой будет производиться настройка.**

![](./jpg/1.PNG)

### 1. Настроите политику маршрутизации для сетей офиса.

***Посредством PBR разделим трафик от подсетей VL30 и VL31 по 2 линкам через R28 до Триады.***


```
ip access-list standard VL30
 permit 192.168.30.0 0.0.0.255
ip access-list standard VL31
 permit 192.168.31.0 0.0.0.255
!
ip sla 1
 icmp-echo 10.0.0.138 source-ip 10.0.0.139
 frequency 10
ip sla schedule 1 life forever start-time now
ip sla 2
 icmp-echo 10.0.0.140 source-ip 10.0.0.141
 frequency 10
ip sla schedule 2 life forever start-time now
!
route-map PBR permit 10
 match ip address VL30
 set ip next-hop verify-availability 10.0.0.138 10 track 1
 set ip next-hop verify-availability 10.0.0.140 20 track 2
!
route-map PBR permit 20
 match ip address VL31
 set ip next-hop verify-availability 10.0.0.140 10 track 2
 set ip next-hop verify-availability 10.0.0.138 20 track 1
!
route-map PBR deny 30
```




















