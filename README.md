# manual-ArchLinux
## 1.Подключение к интернету
!Если вы работаете с компьютера и у вас подкючен интернет кабель, то просто проверьте свое соединение командой ping как это сделать(если кто-то не знает) показанно ниже,
если же у вас ноутбук или по какой-то любой другой причине вам нужно подключиться к Wifi для интернета читайте этот пункт.

Вводим: 
```bash
iwclt
```
Смотрим ваши сетевые карты: 
```bash
device list
```
Выбераем сетевую карту через которую мы будем сканировать подключатсья к wifi,
чаще всего это wlan0

Сканируем сети вокруг:
```bash
station wlan0 scan
```
И выводим их на кран
```bash
station wlan0 get-networks
```
Подключаемся к сети:
```bash
station wlan0 connect test312
```
Вместо test312 вводите свою Wifi сеть и вводите пароль, при вводе пароля его не будет видно так что вводите внимательно!
Проверяем подключились ли мы и есть ли интернет:
```bash
ping google.com
```
Если все успешно, то перед вами должен быть такой вывод:
```bash
PING google.com (142.251.1.102) 56(84) bytes of data.
64 bytes from lb-in-f102.1e100.net (142.251.1.102): icmp_seq=1 ttl=59 time=58.6 ms
64 bytes from lb-in-f102.1e100.net (142.251.1.102): icmp_seq=2 ttl=59 time=49.3 ms
64 bytes from lb-in-f102.1e100.net (142.251.1.102): icmp_seq=3 ttl=59 time=47.7 ms
64 bytes from lb-in-f102.1e100.net (142.251.1.102): icmp_seq=4 ttl=59 time=53.7 ms
64 bytes from lb-in-f102.1e100.net (142.251.1.102): icmp_seq=5 ttl=59 time=47.7 ms
```
Выходим из этого режима:
```bash
exit
```

## 2. Деление диска на разделы
### Посмотрим какие диски у нас есть, для этого воспользуемся командой
```bash
fdisk -l
```
Мы увидим примерно это:
```bash
Output

Disk /dev/sda: 20GiB, 21474836480 bytes, 41943040 sectors
Disk model: QEMU HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

Disk /dev/loop0: 662.69 MiB, 694882304 bytes, 1357192 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

В данном случае мы будем разбивать диск /dev/sda у вас же может он быть другим или если вы устанавливаете на ssd, то он может выглядеть вот так - "nvme0n1"
тогда вы вместо "/dev/sda" будете писать "/dev/nvme0n1".

Все остальное можно игонорировать.

Приступим к разметке диска:
```bash
cfdisk /dev/sda  #sda - диск который вы будете размечать
```






