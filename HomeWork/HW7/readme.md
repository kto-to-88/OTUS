# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами

## Топология

![](./jpg/1.PNG)

## 	Таблица адресации

![](./jpg/2.PNG)

## Цели
### Часть 1. Создание сети и настройка основных параметров устройства
### Часть 2. Выбор корневого моста
### Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
### Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов
# __________________________________________________________________

### Часть 1:	Создание сети и настройка основных параметров устройства
**В части 1 вам предстоит настроить топологию сети и основные параметры маршрутизаторов.**
#### Шаг 1:	Создайте сеть согласно топологии.
**Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.**

Собранная топология представлена выше.

#### Шаг 2:	Выполните инициализацию и перезагрузку коммутаторов.

```
reload
```
Выполнено для всех 3 коммутаторов.

#### Шаг 3:	Настройте базовые параметры каждого коммутатора.

**a.	Отключите поиск DNS.**
```
ip domain-name otus.ru
```
**b.	Присвойте имена устройствам в соответствии с топологией.**
```
hostname S1
```
Аналогично S2 и S3
**c.	Назначьте cisco в качестве зашифрованного пароля доступа к привилегированному режиму.**
```
enable secret 5 $1$mERr$hx5rVt7rPNoS4wqbXKX7m0
```
**d.	Назначьте cisco в качестве паролей консоли и VTY и активируйте вход для консоли и VTY каналов.**
```
username admin secret 5 $1$mERr$hx5rVt7rPNoS4wqbXKX7m0
line vty 0 4
 logging synchronous
 login local
 transport input ssh
```
**e.	Настройте logging synchronous для консольного канала.**
```
logging synchronous
```
**f.	Настройте баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.**
```
banner motd ^C
**********************STOP!!!************************^C
```
**g.	Задайте IP-адрес, указанный в таблице адресации для VLAN 1 на всех коммутаторах.**
```
interface Vlan1
 ip address 192.168.1.2 255.255.255.0
```
**h.	Скопируйте текущую конфигурацию в файл загрузочной конфигурации.**
```
copy running-config startup-config
```
#### Шаг 4:	Проверьте связь.

**Проверьте способность компьютеров обмениваться эхо-запросами.**

**Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2?	______________**

![](./jpg/3.PNG)

**Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3?	______________**

![](./jpg/4.PNG)

**Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3?	______________**

![](./jpg/5.PNG)

### Часть 2:	Определение корневого моста

#### Шаг 1:	Отключите все порты на коммутаторах.
```
conf t
interface range fastEthernet 0/1-4
shutdown 
```
И так для каждого коммутатора

#### Шаг 2:	Настройте подключенные порты в качестве транковых.
```
conf t
interface range fastEthernet 0/1-4
switchport mode trunk
```
И так для каждого коммутатора

#### Шаг 3:	Включите порты F0/2 и F0/4 на всех коммутаторах.
```
conf t
interface range fastEthernet 0/2, fastEthernet 0/4
no shutdown 
```
И так для каждого коммутатора

#### Шаг 4:	Отобразите данные протокола spanning-tree.
S1:
```
S1#sh sp
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000C.CF5B.395B
             Cost        19
             Port        2(FastEthernet0/2)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0060.476C.EA27
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Root FWD 19        128.2    P2p
Fa0/4            Desg FWD 19        128.4    P2p
```

S2:
```
S2#sh sp
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000C.CF5B.395B
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     000C.CF5B.395B
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p
Fa0/4            Desg FWD 19        128.4    P2p
```

S3:
```
S3#sh sp
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     000C.CF5B.395B
             Cost        19
             Port        4(FastEthernet0/4)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00D0.BAAA.22A4
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/4            Root FWD 19        128.4    P2p
Fa0/2            Altn BLK 19        128.2    P2p
```
























