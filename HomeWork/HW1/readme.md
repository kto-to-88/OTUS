# Лабораторная работа. Базовая настройка коммутатора.
###  Задание:

  **1. Часть 1. Проверка конфигурации коммутатора по умолчанию;**
  
  **2. Часть 2. Создание сети и настройка основных параметров устройства;**
  * Настройте базовые параметры коммутатора;
  * Настройте IP-адрес для ПК;

  **3. Часть 3. Проверка сетевых подключений;**
  * Отобразите конфигурацию устройства.
  * Протестируйте сквозное соединение, отправив эхо-запрос.
  * Протестируйте возможности удаленного управления с помощью Telnet.
###  Решение:
**1. Часть 1.  Проверка конфигурации коммутатора по умолчанию**

1. В симуляторе CPT создадим топологию устройств согласно условию работы.

   ![](./jpg/lab1.PNG)

|    Устройство    |   Интерфейс    |   IP-адрес / префикс     |
|-----------------:|:---------------|-------------------------:|
|      SW2960      |      FA0/1     | 192.168.10.3/24          | 
|      PC-PT       |      FA0       | 192.168.10.2/24          |
2. Для первоначальной настройки коммутатора подключимся к нему консольным кабелем. На PC выберем "подключение через терминал", настройки оставим по умолчанию.

![](./jpg/lab1-1.PNG)

3. Установим соединение к коммутатору через консоль. 

![](./jpg/lab1-2.PNG)

4. Введём в CLI комманду "enable" и далее "show running-config". Получим вывод конфигурации "по умолчанию":

```
Building configuration...

Current configuration : 1080 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Switch
!
!
!
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2

Switch#sh running-config   
Building configuration...

Current configuration : 1080 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Switch
!
!
!
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
 no ip address
 shutdown
!
!
!
!
line con 0
!
line vty 0 4
 login
line vty 5 15
 login
!
!
!
!
end
```
**2. Часть 2. Создание сети и настройка основных параметров устройства;**
  1. Настройка базовых параметров коммутатора;
  * Введём в CLI комманду "enable" далее "en" и попадём в привелигированный режим. Далее введём "configure terminal" далее "conf t" и попадём в режим глобальной конфигурации.
  * Зададим имя устройства:

    hostname SW1.
  * Далее, чтобы при ошибочном вводе комманд коммутатор не искал доменный сервер введём комманду:

    ```
    (config)#no ip domain-lookup
    ```
  * Установим пароль cisco на консольное соединение:
    
    ```
    (config)#line console 0
    (config-line)#password cisco
    ```
    
    Далее включим запрос пароля для доступа по консоли:
 
    ```
    SW1(config-line)#login
    
    ```
     Теперь при подключении через консоль появляется запрос на ввод пароля.
    
![](./jpg/lab1-3.PNG)
  * Закроем досуп для привелигированного режима :

    введём "conf t", затем "enable secret cisco". Параметр secret хранит пароль в конфигурации коммутатора в зашифрованном виде и при попытки зайти в привелигированный режим требуется ввод пароля.
  * Настроим подключение к коммутатору через сеть посредством протокола Tenlet:

   Введём следующие комманды: 
   
   ```
   (config)#line vty 0 4 - настроим 5 терминальных линий
   (config-line)#transport input telnet - выберем протокол доступа
   (config-line)# password cisco - зададим пароль на вход
   ```

Далее включим шифрование всех паролей на устройстве: "service password-encryption"

Теперь пароли хранятся в зашифрованном виде.
 * Для предупреждения несанкционированного доступа настроим баннер:

```
   SW1(config)#banner motd 7
Enter TEXT message.  End with the character '7'.
********************************************
###########DO NOT ENTRY#####################
********************************************7
```
    
  3. Настройка IP-адрес для ПК;























 
