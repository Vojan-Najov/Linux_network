# Linux Network

Linux networks configuration on virtual machines.

## Contents

1. [ipcalc tool](#part-1-ipcalc-tool)
2. [Static routing between two machines](#part-2-static-routing-between-two-machines)
3. [iperf3 utility](#part-3-iperf3-utility)
4. [Network firewall](#part-4-network-firewall)
5. [Static network routing](#part-5-static-network-routing)
6. [Dynamic IP configuration using DHCP](#part-6-dynamic-ip-configuration-using-dhcp)
7. [NAT](#part-7-nat)
8. [Bonus. Introduction to SSH Tunnels](#part-8-bonus-introduction-to-ssh-tunnels)

## Part 1. ipcalc tool

- Поднять вирутальную машину (ws1): \
  <img src="./misc/images/ipcalc_01.png" alt="ipcalc_01" width="700"/>

#### 1.1. Сети и маски

- Определить адрес сети 192.167.38.54/13: \
  **Ответ: 192.160.0.0** \
  <img src="./misc/images/ipcalc_02.png" alt="ipcalc_02" width="700"/>
- Перевод маски 255.255.255.0 в префиксную и двоичную запись: \
  **Ответ: /24 и 11111111.11111111.11111111.00000000** \
  <img src="./misc/images/ipcalc_03.png" alt="ipcalc_03" width="700"/>
- Перевод маски /15 в обычную и двоичную запись: \
  **Ответ: 255.254.0.0 и 11111111.11111110.00000000.00000000** \
  <img src="./misc/images/ipcalc_04.png" alt="ipcalc_04" width="700"/>
- Перевод маски 11111111.11111111.11111111.11110000 в обычную и префиксную запись: \
  **Ответ: 255.255.255.240 и /28** \
  <img src="./misc/images/ipcalc_05.png" alt="ipcalc_05" width="700"/>
- Минимальный и максимальный хост в сети 12.167.38.4 при маске /8: \
  **Ответ: минимальный адрес - 12.0.0.1, максимальный - 12.255.255.254** \
  <img src="./misc/images/ipcalc_06.png" alt="ipcalc_06" width="700"/>
- Минимальный и максимальный хост в сети 12.167.38.4 при маске 11111111.11111111.00000000.00000000: \
  **Ответ: минимальный адрес - 12.167.0.1, максимальный - 12.167.255.254** \
  <img src="./misc/images/ipcalc_07.png" alt="ipcalc_07" width="700"/>
- Минимальный и максимальный хост в сети 12.167.38.4 при маске 255.255.254.0: \
  **Ответ: минимальный адрес - 12.167.38.1, максимальный - 12.167.39.254** \
  <img src="./misc/images/ipcalc_08.png" alt="ipcalc_08" width="700"/>
- Минимальный и максимальный хост в сети 12.167.38.4 при маске /4: \
  **Ответ: минимальный адрес - 0.0.0.1, максимальный - 15.255.255.254** \
  <img src="./misc/images/ipcalc_09.png" alt="ipcalc_09" width="700"/>

#### 1.2. localhost

- Определить, можно ли обратиться к приложению, работающему на localhost, со следующими IP: \
  194.34.23.100, 127.0.0.2, 127.1.0.1, 128.0.0.1 \
  **Ответ: 127.0.0.2, 127.1.0.1 - да; 194.34.23.100, 128.0.0.1 - нет** \
  <img src="./misc/images/ipcalc_10.png" alt="ipcalc_10" width="700"/> \
  <img src="./misc/images/ipcalc_11.png" alt="ipcalc_11" width="700"/>

#### 1.3. Диапазоны и сегменты сетей

- Какие из перечисленных IP можно использовать в качестве публичного, а какие только в качестве частных: \
  **Ответ: публичные адреса - 134.43.0.2,  172.0.2.1, 192.172.0.1, 172.68.0.2 и 192.169.168.1; \
           частные адреса - 10.0.0.45, 192.168.4.2, 172.20.250.4, 172.16.255.255, 10.10.10.10** \
  <img src="./misc/images/ipcalc_12.png" alt="ipcalc_12" width="700"/>
- Какие из перечисленных IP адресов шлюза возможны у сети 10.10.0.0/18: \
  **Ответ: для шлюза подходят адреса - 10.10.0.2, 10.10.10.10 или 10.10.1.255;** \
  **не подходят - 10.0.0.1 и 10.10.100.1 - так как они находятся вне данной подсети** \
  <img src="./misc/images/ipcalc_13.png" alt="ipcalc_13" width="700"/>
  
## Part 2. Static routing between two machines

- Поднять две виртуальные машины (ws1 и ws2): \
  <img src="./misc/images/static_01.png" alt="static_01" width="700"/> \
  Добавим по дополнительному сетевому интерфейсу (internal network) для каждой из машин:
  - машина ws1 \
    <img src="./misc/images/static_02.png" alt="static_02" width="700"/>
  - машина ws2 \
    <img src="./misc/images/static_03.png" alt="static_03" width="700"/>
- С помощью команды `ip a` посмотреть существующие сетевые интерфейсы:
  - машина ws1 \
    <img src="./misc/images/static_04.png" alt="static_04" width="700"/>
  - машина ws2 \
    <img src="./misc/images/static_05.png" alt="static_05" width="700"/>
- Описать сетевой интерфейс, соответствующий внутренней сети, на обеих машинах и задать следующие адреса и маски. \
  Для этого изменим конфигурационные файлы командой `sudo vim /etc/netplan/00-installer-config.yaml`, описав интерфейсы `enp0s8`:
  - ws1 - 192.168.100.10, маска /16 \
    <img src="./misc/images/static_06.png" alt="static_06" width="700"/>
  - ws2 - 172.24.116.8, маска /12 \
    <img src="./misc/images/static_07.png" alt="static_07" width="700"/>
- Выполнить команду `netplan apply` для перезапуска сервиса сети:
  - машина ws1 \
    <img src="./misc/images/static_08.png" alt="static_08" width="700"/>
  - машина ws2 \
    <img src="./misc/images/static_09.png" alt="static_09" width="700"/>

#### 2.1. Добавление статического маршрута вручную

- Добавить статический маршрут от одной машины до другой и обратно при помощи команды `ip r add`
  - машина ws1 : `$>sudo ip r add 172.24.116.8 dev enps08` \
    <img src="./misc/images/static_10.png" alt="static_10" width="700"/>
  - машина ws2 : `$>sudo ip r add 192.168.100.10 dev enps08` \
    <img src="./misc/images/static_11.png" alt="static_11" width="700"/>
- Пропинговать соединение между машинами
  - Пинг машины ws2 \
    <img src="./misc/images/static_12.png" alt="static_12" width="700"/>
  - Пинг машины ws1 \
    <img src="./misc/images/static_13.png" alt="static_13" width="700"/>

#### 2.2. Добавление статического маршрута с сохранением
- Перезапустить машины командой `reboot`
  - ws1: \
    <img src="./misc/images/static_14.png" alt="static_14" width="700"/>
  - ws2: \
    <img src="./misc/images/static_15.png" alt="static_15" width="700"/>
- Добавить статический маршрут от одной машины до другой с помощью файла etc/netplan/00-installer-config.yaml:
  - машина ws1: `$sudo vim /etc/netplan/00-installer-config.yaml` \
    <img src="./misc/images/static_16.png" alt="static_16" width="700"/> \
    <img src="./misc/images/static_19.png" alt="static_19" width="700"/>
  - машина ws2: `$sudo vim /etc/netplan/00-installer-config.yaml` \
    <img src="./misc/images/static_17.png" alt="static_17" width="700"/> \
    <img src="./misc/images/static_18.png" alt="static_18" width="700"/>
- Пропинговать соединение между машинами:
  - Пинг машины ws2 \
    <img src="./misc/images/static_20.png" alt="static_20" width="700"/>
  - Пинг машины ws1 \
    <img src="./misc/images/static_21.png" alt="static_21" width="700"/>

## Part 3. iperf3 utility

#### 3.1. Скорость соединения

- Перевести и записать в отчёт: 8 Mbps в MB/s, 100 MB/s в Kbps, 1 Gbps в Mbps:
  - 8 Mbps = 1 MB/s;
  - 100 MB/s = 819200 Kbps (для двоичных приставок Меби и Киби); \
    100 MB/s = 800000 Kbps (для десятичных приставок Мега и Кило);
  - 1 Gbps = 1024 Mbps (для двоичных приставок Гиби и Меби); \
    1 Gbps = 1000 Mbps (для десятичных приставок Гига и Мега);
- Измерить скорость соединения между ws1 и ws2:
  - на машине ws2 запустим сервер iperf3 командой `$>iperf3 -s -f K` \
    <img src="./misc/images/iperf3_02.png" alt="iperf3_02" width="700"/>
  - на машине ws1 запустим клиент iperf3 командой `$>iperf3 -c 172.24.116.8 -f K` \
    <img src="./misc/images/iperf3_01.png" alt="iperf3_01" width="700"/>
  - **Битрейт отправителя (ws1) - 127798 KBytes/sec;** \
    **Битрейт получателя (ws2) - 127629 KBytes/sec;**
  - на машине ws1 запустим сервер iperf3 командой `$>iperf3 -s -f K` \
    <img src="./misc/images/iperf3_03.png" alt="iperf3_03" width="700"/>
  - на машине ws2 запустим клиент iperf3 командой `$>iperf3 -c 192.168.100.10 -f K` \
    <img src="./misc/images/iperf3_04.png" alt="iperf3_04" width="700"/>
  - **Битрейт отправителя (ws2) - 132990 KBytes/sec;** \
    **Битрейт получателя (ws1) - 132800 KBytes/sec;**

## Part 4. Network firewall

#### 4.1. Утилита iptables

- Создать файл /etc/firewall.sh, имитирующий фаерволл, на ws1 и ws2:
  - Машина ws1: \
    <img src="./misc/images/firewall_01.png" alt="firewall_01" width="700"/>
  - Машина ws2: \
    <img src="./misc/images/firewall_02.png" alt="firewall_02" width="700"/>
- Запустить файлы на обеих машинах командами `$>chmod +x /etc/firewall.sh` и `$>/etc/firewall.sh`:
  - Машина ws1: \
    <img src="./misc/images/firewall_03.png" alt="firewall_03" width="700"/>
  - Машина ws2: \
    <img src="./misc/images/firewall_04.png" alt="firewall_04" width="700"/>
- Правила обрабатываются в порядке их следования в таблице. Поэтому для исходящего icmp пакета на машине ws1 сначала будет следовать действие REJECT (отклонить пакет), и следующее действие ACCEPT (разрешение) уже не имеет силы, и соответственно эта машина не пингуется, т.е. не отвечает на запрос icmp. На машине ws2 напротив действие REJECT следует после действия ACCEPT, в результате чего исходящий пакет будет отправлен, т.е. машина будет пинговаться.

#### 4.2. Утилита nmap

- Командой ping найти машину, которая не "пингуется", после чего утилитой nmap показать, что хост машины запущен:
  - Успешный пинг машины ws2 с машины ws1 \
    <img src="./misc/images/firewall_05.png" alt="firewall_05" width="700"/>
  - Неуспешный пинг машины ws1 с машины ws2 и вывод утилиты `nmap` \
    <img src="./misc/images/firewall_06.png" alt="firewall_06" width="700"/>

## Part 5. Static network routing

- Cеть: \
  <img src="./misc/images/part5_network.png" alt="route_00" width="700"/>
- Поднять пять виртуальных машин (3 рабочие станции (ws11, ws21, ws22) и 2 роутера (r1, r2)) \
  <img src="./misc/images/route_01.png" alt="route_01" width="700"/> \
  <img src="./misc/images/route_02.png" alt="route_02" width="700"/> \
  <img src="./misc/images/route_03.png" alt="route_03" width="700"/> \
  <img src="./misc/images/route_04.png" alt="route_04" width="700"/> \
  <img src="./misc/images/route_05.png" alt="route_05" width="700"/>

#### 5.1. Настройка адресов машин

- Настроить конфигурации машин в etc/netplan/00-installer-config.yaml согласно сети на рисунке выше. А также добавим имена машин в файл `/etc/hosts`.
  - **Машина ws11:** \
    в настройках VirtualBox сетевой адаптер 1 изменим на internal net с именем intnet_wsr1; \
    а затем на самой машине изменим конфигурационные файлы: \
    <img src="./misc/images/route_06.png" alt="route_06" width="700"/>
  - **Машина r1:** \
    в настройках VirtualBox сетевой адаптер 1 изменим на internal net с именем intnet_wsr1 \
    и добавим второй сетевой адаптер internal net с именем сети intnet_rr; \
    а затем на самой машине изменим конфигурационные файлы: \
    <img src="./misc/images/route_07.png" alt="route_07" width="700"/>
  - **Машина ws21:** \
    в настройках VirtualBox сетевой адаптер 1 изменим на internal net с именем intnet_wsr2; \
    а затем на самой машине изменим конфигурационные файлы: \
    <img src="./misc/images/route_08.png" alt="route_08" width="700"/>
  - **Машина ws22:** \
    в настройках VirtualBox сетевой адаптер 1 изменим на internal net с именем intnet_wsr2; \
    а затем на самой машине изменим конфигурационные файлы: \
    <img src="./misc/images/route_09.png" alt="route_09" width="700"/>
  - **Машина r2:** \
    в настройках VirtualBox сетевой адаптер 1 изменим на internal net с именем intnet_rr; \
    и добавим второй сетевой адаптер internal net с именем сети intnet_wsr2; \
    а затем на самой машине изменим конфигурационные файлы: \
    <img src="./misc/images/route_09_2.png" alt="route_09_2" width="700"/>
- Применим настройки конфигурационного файла netplan командой`$>sudo netplan apply`
- Перезапустить сервис сети. Командой `$>ip -4 a` проверить, что адрес машины задан верно. \
  Также пропинговать ws22 с ws21. Аналогично пропинговать r1 с ws11.
  - **Машина ws11:** \
    <img src="./misc/images/route_10.png" alt="route_10" width="700"/>
  - **Машина r1:** \
    <img src="./misc/images/route_11.png" alt="route_11" width="700"/>
  - **Машина ws21:** \
    <img src="./misc/images/route_12.png" alt="route_12" width="700"/>
  - **Машина ws22:** \
    <img src="./misc/images/route_13.png" alt="route_13" width="700"/>
  - **Машина r2:** \
    <img src="./misc/images/route_14.png" alt="route_14" width="700"/>

#### 5.2. Включение переадресации IP-адресов.

- Для включения переадресации IP, выполним команду на роутерах: \
  `$>sysctl -w net.ipv4.ip_forward=1`
  - **Машина r1:** \
    <img src="./misc/images/route_15.png" alt="route_15" width="700"/>
  - **Машина r2:** \
    <img src="./misc/images/route_16.png" alt="route_16" width="700"/>
- Чтобы переадресация оставалась работать после перезагрузки системы, \
  изменим конфигурационный файл /etc/sysctl.conf:
  - **Машина r1:** \
    <img src="./misc/images/route_17.png" alt="route_17" width="700"/>
  - **Машина r2:** \
    <img src="./misc/images/route_18.png" alt="route_18" width="700"/>

#### 5.3. Установка маршрута по-умолчанию

- Настроить маршрут по-умолчанию (шлюз) для рабочих станций.
  - **Машина ws11:** \
    <img src="./misc/images/route_19.png" alt="route_19" width="700"/>
  - **Машина ws21:** \
    <img src="./misc/images/route_20.png" alt="route_20" width="700"/>
  - **Машина ws22:** \
    <img src="./misc/images/route_21.png" alt="route_21" width="700"/>
- Вызвать `ip r` и показать, что добавился маршрут в таблицу маршрутизации:
  - **Машина ws11:** \
    <img src="./misc/images/route_22.png" alt="route_22" width="700"/>
  - **Машина ws21:** \
    <img src="./misc/images/route_23.png" alt="route_23" width="700"/>
  - **Машина ws22:** \
    <img src="./misc/images/route_24.png" alt="route_24" width="700"/>
- Пропинговать с `ws11` роутер `r2` и показать на `r2`, что пинг доходит. \
  Для этого использовать команду: `$>sudo tcpdump -tn -i eth0` \
    <img src="./misc/images/route_25.png" alt="route_25" width="700"/> \
    <img src="./misc/images/route_26.png" alt="route_26" width="700"/>

#### 5.4. Добавление статических маршрутов

- Добавить в роутеры `r1` и `r2` статические маршруты в файле конфигураций. \
  Вызвать `ip r` и показать таблицы с маршрутами на обоих роутерах.
  - **r1:** \
    <img src="./misc/images/route_27.png" alt="route_27" width="700"/>
  - **r2:** \
    <img src="./misc/images/route_28.png" alt="route_28" width="700"/>
- Запустить команды на `ws11`: \
  `ip r list 10.10.0.0/[маска сети]` и `ip r list 0.0.0.0/0`: \
  <img src="./misc/images/route_29.png" alt="route_29" width="700"/>
- Машина `ws11` находится в сети 10.10.0.0/18, и таблица маршрутизации содержит запись о том, что все пакеты для этой сети адресуются от ip адресса установленного для интерфейса внутри этой сети. Для остальных адресатов вне этой сети используется маршрут по умолчанию с перенаправлением пакетов к роутеру `r1`. Предполагается, что более частный маршрут для внутренней сети преобладает над маршрутом по умолчанию. 

#### 5.5. Построение списка маршрутизаторов

- Запустить на `r1` команду дампа `$>tcpdump -tnv -i eth0`. \
  При помощи утилиты `traceroute` построить список маршрутизаторов на пути от `ws11` до `ws21`: \
  <img src="./misc/images/route_30.png" alt="route_30" width="700"/> \
  <img src="./misc/images/route_31.png" alt="route_31" width="700"/>
- Дамп команды `tcpdum` производит большой вывод: \
  в начале видно, что пересылаются пакеты с адреса `ws11` на адресс `ws21`: \
  <img src="./misc/images/route_32.png" alt="route_32" width="700"/> \
  также можно проследить примерный маршрут в обратном направлении: \
  от `ws21` до `w11`, от `r2` до `ws11` и от `r1` до `ws11`: \
  <img src="./misc/images/route_33.png" alt="route_33" width="700"/> \
  или `ws21 -> r2 -> r1 -> ws11`; \
  т.e. `ws11 -> r1 -> r2 -> ws21`;

#### 5.6. Использование протокола ICMP при маршрутизации

- Запустить на `r1` перехват сетевого трафика, проходящего через eth0 с помощью команды: `tcpdump -n -i eth0 icmp` \
  Пропинговать с `ws11` несуществующий IP (например, 10.30.0.111) с помощью команды: `ping -c 1 10.30.0.111` \
  <img src="./misc/images/route_34.png" alt="route_34" width="700"/> \
  <img src="./misc/images/route_35.png" alt="route_35" width="700"/>

## Part 6. Dynamic IP configuration using DHCP

- Установим dhcp сервер командой `$>sudo apt install isc-dhcp-server`
- Для `r2` настроить в файле `/etc/dhcp/dhcpd.conf` конфигурацию службы `DHCP`: \
  указать адрес маршрутизатора по-умолчанию, DNS-сервер и адрес внутренней сети \
  в файле `resolv.conf` прописать nameserver 8.8.8.8 \
  <img src="./misc/images/dhcp_01.png" alt="dhcp_01" width="700"/>
- Машину `ws21` перезагрузить при помощи `reboot` и через `ip a` показать, что она получила адрес. \
  Также пропинговать `ws22` с `ws21`. \
  <img src="./misc/images/dhcp_02.png" alt="dhcp_02" width="700"/> \
  <img src="./misc/images/dhcp_03.png" alt="dhcp_03" width="700"/>
- Указать MAC адрес у `ws11`, для этого в `etc/netplan/00-installer-config.yaml` надо добавить строки: `macaddress: 10:10:10:10:10:BA, dhcp4: true`
  - Сначала изменим мак-адрес в настройках сети для виртуальной машины в `VirtualBox`: \
    <img src="./misc/images/dhcp_04.png" alt="dhcp_04" width="700"/>
  - Затем на самой машине: \
    <img src="./misc/images/dhcp_05.png" alt="dhcp_05" width="700"/>
- Для `r1` настроить аналогично `r2`, но сделать выдачу адресов с жесткой привязкой к MAC-адресу (`ws11`).
  - Машина `r1`: \
    <img src="./misc/images/dhcp_06.png" alt="dhcp_06" width="700"/>
  - Машина `ws11`: \
    <img src="./misc/images/dhcp_07.png" alt="dhcp_07" width="700"/> \
    <img src="./misc/images/dhcp_08.png" alt="dhcp_08" width="700"/>
- Запросить с `ws21` обновление ip адреса. \
  <img src="./misc/images/dhcp_09.png" alt="dhcp_09" width="700"/>
- Опции dhcp сeрвера: 
  - dhcp-lease-time;
  - routers;
  - domain-name-servers;

## Part 7. NAT

- Установим apache сервер командой `$>sudo apt install apache2`
- В файле `/etc/apache2/ports.conf` на `ws22` и `r1` изменить строку `Listen 80` на `Listen 0.0.0.0:80`, \
  то есть сделать сервер Apache2 общедоступным; \
  Запустить веб-сервер Apache командой `service apache2 start` на `ws22`и `r1`:
  - машина `ws22`: \
    <img src="./misc/images/nat_01.png" alt="nat_01" width="700"/>
  - машина `r1`: \
    <img src="./misc/images/nat_02.png" alt="nat_02" width="700"/>
- Добавить в фаервол, созданный по аналогии с фаерволом из Части 4, на `r2` следующие правила: \
  удаление правил в таблице filter - `iptables -F` \
  удаление правил в таблице "NAT" - `iptables -F -t nat` \
  отбрасывать все маршрутизируемые пакеты - `iptables --policy FORWARD DROP` \
  <img src="./misc/images/nat_03.png" alt="nat_03" width="700"/>
- Проверить соединение между `ws22` и `r1` командой `ping`: \
  <img src="./misc/images/nat_04.png" alt="nat_04" width="700"/> \
  <img src="./misc/images/nat_05.png" alt="nat_05" width="700"/>
- Добавить в файл ещё одно правило: \
  разрешить маршрутизацию всех пакетов протокола ICMP \
  <img src="./misc/images/nat_06.png" alt="nat_06" width="700"/>
- Проверить соединение между `ws22` и `r1` командой ping: \
  <img src="./misc/images/nat_07.png" alt="nat_07" width="700"/> \
  <img src="./misc/images/nat_08.png" alt="nat_08" width="700"/>
- Добавить в файл ещё два правила: \
  включить SNAT, а именно маскирование всех локальных ip из локальной сети, находящейся за r2: \
  включить DNAT на 8080 порт машины r2 и добавить к веб-серверу Apache, запущенному на ws22, доступ извне сети
  - Для того, чтобы включить SNAT, добавим новую запись в `iptables`: \
    `iptables -t nat -A POSTROUTING -o enp0s3 -s 10.20.0.0/26 -j SNAT --to-source 10.100.0.12` \
    ключ -t указывает используемую таблицу; \
    запись добавляется в цепочку POSTROUTING и предписывает выполнить действие SNAT для всех пакетов, попадающих под критерий -o enp0s3, т.е. использующих в качестве исходящего внешний интерфейс enp0s3; \
    Для SNAT обязательно указывается параметр --to-source, который используется для указания адреса, присваиваемому пакету, теперь именно этот адрес будет указываться в качестве исходящего; \
    ключ -s 10.20.0.0/26 - явно задать диапазон адресов для преобразования;
  - Для того, чтобы включить DNAT, добавим новую запись в `iptables`: \
    `iptables -t nat -A PREROUTING -i enp0s3 -p tcp --dport 8080 -j DNAT --to-destination 10.20.0.20:80` \
    DNAT - или изменения адреса и порта назначения пакета. Это действие применимо в цепочках PREROUTING и OUTPUT, т.е. на самом входе в брандмауэр; \
    ключ -i enp0s3 указывает входящий интерфейс;
  -  Так как политика для цепочки FORWARD состоит в том, чтобы отбрасывать пакеты -- явно создадим запись позволяющую перенаправлять новые и установленные соединия `iptables -t filter -A FORWARD -m conntrack --cstate NEW,ESTABLISHED,RELATED -j ACCEPT`
- В итоге файл `etc/firewall.sh` примет вид: \
  <img src="./misc/images/nat_09.png" alt="nat_09" width="700"/>
- Проверить соединение по TCP для SNAT, для этого с ws22 подключиться к серверу Apache на r1: \
  <img src="./misc/images/nat_10.png" alt="nat_10" width="700"/>
- Проверить соединение по TCP для DNAT, для этого с r1 подключиться к серверу Apache на ws22 командой telnet (обращаться по адресу r2 и порту 8080): \
  <img src="./misc/images/nat_11.png" alt="nat_11" width="700"/>

## Part 8. Bonus. Introduction to SSH Tunnels

- Запустить на `r2` фаервол с правилами из Части 7 \
  <img src="./misc/images/ssh_tunnels_01.png" alt="ssh_tunnels_01" width="700"/>
- Запустить веб-сервер Apache на `ws22` только на `localhost` (то есть в файле `/etc/apache2/ports.conf` изменить строку `Listen 80` на `Listen localhost:80`) \
  <img src="./misc/images/ssh_tunnels_02.png" alt="ssh_tunnels_02" width="700"/>
- Воспользоваться Local TCP forwarding с `ws21` до `ws22`, чтобы получить доступ к веб-серверу на `ws22` с `ws21`:
  - Для начала изменим конфигурационный файл `/etc/ssh/sshd_config` на `ws22` и перезапустим ssh сервер: \
    <img src="./misc/images/ssh_tunnels_03.png" alt="ssh_tunnels_03" width="700"/>
  - На машине `ws21` с помощью `tmux`  создадим второй терминал. \
    В первом терминале подключимся к ssh серверу на `ws22` командой `ssh -L 9999:127.0.0.1:80 10.20.0.20` \
   опция -L указывает, что подключения к TCP-порту 9999 на локальном хосте должен быть перенаправлен на указанный хост и порт на удаленной стороне. \
   Во втором терминале с помощью `telnet 127.0.0.1 9999` пробуем подключиться к серверу `apache` на `ws22` \
    <img src="./misc/images/ssh_tunnels_04.png" alt="ssh_tunnels_04" width="700"/> \
    <img src="./misc/images/ssh_tunnels_05.png" alt="ssh_tunnels_05" width="700"/>
- Воспользоваться Remote TCP forwarding c `ws11` до `ws22`, чтобы получить доступ к веб-серверу на `ws22` с `ws11`
  - Сначала установить `ssh сервер` на машине `ws11` и разрешим перенапрваление tcp добавив в конфигурационный файл `/etc/ssh/sshd_config` запись `AllowTcpForwarding yes`
  - Подключимся с машины `ws22` к `ssh серверу` на `ws11` командой: `$>ssh -R 9999:127.0.0.1:80 10.10.0.2` \
    После успешного подключения, на `ws11` SSH-сервер начинает слушать порт 9999. При подключении к порту 9999 на `ws11`, SSH-клиент на `ws22` устанавливает соединение с localhost (коим и является для себя самого `ws22`) на порт 80 и передает по этому соединению данные, принятые ssh-сервером на `ws11` на порт 9999.
    <img src="./misc/images/ssh_tunnels_06.png" alt="ssh_tunnels_06" width="700"/> \
    <img src="./misc/images/ssh_tunnels_07.png" alt="ssh_tunnels_07" width="700"/>
  - Теперь на машине `ws11` подключимся к `apache` серверу на `ws22`: \
    <img src="./misc/images/ssh_tunnels_08.png" alt="ssh_tunnels_08" width="700"/>


