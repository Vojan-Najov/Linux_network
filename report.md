# Linux Network

Linux networks configuration on virtual machines.

## Contents

1. [ipcalc tool](#part-1-ipcalc-tool)
2. [Static routing between two machines](#part-2-static-routing-between-two-machines)
3. [iperf3 utility](#part-3-iperf3-utility)
4. [Network firewall](#part-4-network-firewall)
5. [Static network routing](#part-5-static-network-routing)

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
- Вызвать ip r и показать, что добавился маршрут в таблицу маршрутизации:
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

- Добавить в роутеры r1 и r2 статические маршруты в файле конфигураций. \
  Вызвать `ip r` и показать таблицы с маршрутами на обоих роутерах.
  - **r1:** \
    <img src="./misc/images/route_27.png" alt="route_27" width="700"/>
  - **r2:** \
    <img src="./misc/images/route_28.png" alt="route_28" width="700"/>
- Запустить команды на ws11: \
  `ip r list 10.10.0.0/[маска сети]` и `ip r list 0.0.0.0/0`: \
  <img src="./misc/images/route_29.png" alt="route_29" width="700"/>
- Машина `ws11` находится в сети 10.10.0.0/18, и таблица маршрутизации содержит запись о том, что все пакеты для этой сети адресуются от ip адресса установленного для интерфейса внутри этой сети. Для остальных адресатов вне этой сети используется маршрут по умолчанию с перенаправлением пакетов к роутеру `r1`. Предполагается, что более частный маршрут для внутренней сети преобладает над маршрутом по умолчанию. 

#### 5.5. Построение списка маршрутизаторов

- Запустить на r1 команду дампа `$>tcpdump -tnv -i eth0`. \
  При помощи утилиты `traceroute` построить список маршрутизаторов на пути от `ws11` до `ws21`: \
  <img src="./misc/images/route_30.png" alt="route_30" width="700"/>

