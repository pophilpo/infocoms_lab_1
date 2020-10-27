# Попов Филипп. К3120
## Лабораторная работа №1 (подготовительная): Подготовка серверной инфраструктуры
### Задача 1. Анализ Сетевой конфигурации Операционной системы.
Для отображения всех сетевых интерфейсов была использована команда `ip link show`. <br />
Вывод команды:<br /><br />
`1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00`<br />
`2: enp2s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
link/ether 30:9c:23:3f:27:f7 brd ff:ff:ff:ff:ff:ff`<br /><br />
Вывод данной команды содержит пронумерованный список интерфейсов, присутствующих в системе. Информация об интерфейсе содержит две строки: в первой указывается имя интерфейса, установленные флаги состояния, MTU (максимально допустимый размер фрейма в байтах), тип и размер очереди фреймов; во второй строке — тип соединения, MAC-адрес, широковещательный адрес и т. п.

#### Сетевой интерфейс `lo`
Сетевой интерфейс `lo (loopback interface)` присутствует в большинстве ОС, даже если они не подключены к сети интернет. IP адрес данного интерфейса: `127.0.0.1`.
Любой трафик, который устройство отправляет по этому адресу будет возвращен отправляющему устройству. Данный интерфейс используют для идентификации устройства или, например, для запуска локальных серверов, для тестирования при разработке. <br />
#### Сетевой интерфейс `Ethernet`
Имя сетевого интерфейса `Ethernet` на моём устройстве - `enp2s0`, где `en=Ethernet`, `p2=bus number`, `s0=slot number`. Сетевой интерфейс позволяет устройству подключиться к локальной сети (LAN), используя Ethernet в качестве механизма передачи.
#### Параметры сетевых интерфейсов.
`MTU` - Maximum transmission unit: Максимальная единица передачи означает максимальный размер полезного блока данных одного пакета.<br />
`qdisc` - Является основным строительным блоком, на котором построен весь контроль трафика в Linux, а также называется дисциплиной очереди.<br />
`qdisc fq_codel (Fair Queuing Controlled Delay)` - это дисциплина в очереди, которая сочетает в себе систему Fair Queuing со схемой CoDel AQM. <br />
`state UP/DOWN` - Состояние интерфейса. <br />
`mode { ip6ip | ipip | mplsip | any }` - Указывает режим, в котором интерфейс должен работать. `DEFAULT` режим равен режиму `ip6ip (IPv6-Over-IPv4)`. <br />
`group` - Указывает группу, к которой принадлежит устройство. Доступные группы перечислены в файле `/etc/iproute2/group`. <br />
`qlen` - Указывает максимальную длину очереди устройства. <br />
### Задача 2. Изучение и практическая работа с утилитой `ping`.
#### Назначение.
Утилита `ping (Packet Internet Groper)` используется для проверки сетевого соединения между хостом и сервером/хостом. Эта утилита принимает на вход IP-адрес или URL и посылает пакет данных по указанному адресу с сообщением "PING" и получает ответ от сервера/хоста. Время ответа записывается и называется задержкой (`latency`). <br />
#### Пример использования.
Для проверки работоспособности сайта `www.github.com` можно использовать утилиту `ping`. <br />
`ping www.github.com` <br />
Вывод после выполнения данной команды: <br />
`PING github.com (140.82.121.3) 56(84) bytes of data.` <br />
`64 bytes from lb-140-82-121-3-fra.github.com (140.82.121.3): icmp_seq=1 ttl=53 time=52.4 ms` <br />
В данном выводе мы можем увидеть, что мы получили ответ от `github.com (140.82.121.3)` размером в 64 байта `(64 bytes)`. Время на ответ заняло 52.4 миллисекунды `(time=52.4 ms)`. Сайт работает! <br />
С помощью флага `-c` можно указать количество запросов, которые мы хотим сделать, и после их выполнения получить анализ проведенной работы. <br />
`ping -c 5 github.com` <br />
Вывод после выполнения данной команды: <br />
`64 bytes from lb-140-82-121-3-fra.github.com (140.82.121.3): icmp_seq=1 ttl=53 time=51.8 ms` <br />
`64 bytes from lb-140-82-121-3-fra.github.com (140.82.121.3): icmp_seq=2 ttl=53 time=50.3 ms` <br />
`64 bytes from lb-140-82-121-3-fra.github.com (140.82.121.3): icmp_seq=3 ttl=53 time=50.5 ms` <br />
`64 bytes from lb-140-82-121-3-fra.github.com (140.82.121.3): icmp_seq=4 ttl=53 time=50.8 ms` <br />
`64 bytes from lb-140-82-121-3-fra.github.com (140.82.121.3): icmp_seq=5 ttl=53 time=50.3 ms` <br />

`--- github.com ping statistics ---` <br />
`5 packets transmitted, 5 received, 0% packet loss, time 4006ms` <br />
`rtt min/avg/max/mdev = 50.259/50.701/51.769/0.564 ms` <br />
Последняя строчка сообщает нам о минимальном, среднем, максимальном времени ответа, а так же `Среднеквадратическое отклонение` времени ответа. <br />
#### Дополнительные ключи.
Утилита `ping` версии `s20190709` имеет 34 дополнительных ключа. <br />
Ключи для форматирования вывода: <br />
`-q (quiet output); -v (verbose output); -D (print timestamps); -U (print user-to-user latency);` <br />
Опции для IPv4: <br />
`-4 (use IPv4); -b (allow pinging broadcast); -R (record route); -T (define timestamp);`<br />
Опции для IPv6: <br />
`-6 (use IPv6); -F (define flow label, default is random); -N (use icmp6 node info query);` <br />
Пример использования утилиты с дополнительными ключами: <br />
`ping -w 15 -i 5 github.com` - Отправлять запросы с интервалами `(-i 5)` в пять секунд в течение `(-w 15)` 15 секунд. <br />
С использованием дополнительных ключей можно получить множество разной информации. Например, проверить производительность сети: <br />
`ping -f github.com`. Данная команда будет отправлять запросы как можно быстрее, пока хост не перестанет отвечать или не заблокирует IP адрес устройства, с которого были отправлены запросы. Не все пользователи могут использовать этот флаг, в моём случае пришлось запускать команду с правами пользователя `root`.

### Задача 3. Установка, настройка и запуск SQL-ориентированной СУБД.
#### Установка PostgreSQL.
Установить PostgreSQL на Arch linux можно при помощи команды <br />
`sudo pacman -S postgresql` <br />
Для переключения на postgresql необходимо ввести команду: <br />
`sudo -iu postgres` <br />
Прежде чем работать с данной СУБД необходимо инициализировать кластер: <br />
`initdb -D /var/lib/postgres/data`, где `-D` - это место по умолчанию, где должен храниться кластер базы данных.
#### Запуск СУБД.
Для запуска PostgreSql необходимо выполнить следующую команду: <br />
`systemctl start postgresql.service`
#### Создание пользователя.
Создать пользователя можно с помощью данной команды: <br />
`createuser --interactive`

### Задача 4. Установка, настройка и запуск WEB-сервера (Apache/Nginx).
#### Установка WEB-сервера Nginx.
Установить WEB-сервер Nginx на Arch linux можно при помощи команды: <br />
`sudo pacman -S nginx`
#### Запуск WEB-сервера Nginx.
Запустить WEB-сервер Nginx можно при помощи команды: <br />
`systemctl start nginx.service` <br />
Стартовая страница будет доступна по адресу: <br />
`http://127.0.0.1/`

### Задача 5. Установка, настройка и запуск FTP-сервера.
#### Установка FTP-сервера.
Для запуска FTP-сервреа нам понадобится `vsftpd`. Установить эту утилиту можно при помощи команды: <br />
`sudo pacman -S vsftpd`
#### Запуск FTP-серсера
Запустить FTP-сервер можно при помощи команды: <br />
`sudo systemctl start vsftpd`

### Задача 6. Создание пользователя для удаленного SSH-подключения с возможностями администратора.
Для создания нового пользователя с правами администратора воспользуемся командой: <br />
`adduser test_user` - Создание пользователя <br />
`usermod -aG sudo test_user` - Добавление пользователя в группу с правами администратора <br />
Разрешим новому пользователю подключаться к устройству по SSH изменив файл <br />
`/etc/ssh/sshd_config` <br />
В данный файл добавим строчку <br />
`AllowUsers test_user root` <br />
Для того чтобы изменения вступили в силу - перезапустим сервис `sshd`: <br />
`systemctl restart sshd`
