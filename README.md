# manual-ArchLinux
! Данный гайд я делал скорее для себя чем для кого-то, но если он кому то поможет мне будет очень приятно. Писал его основываясь на [этом руководстве](https://github.com/Vlador1337/Arch-Linux-Install-Guide-RU) но в некоторые моменты у меня с ним возникали трудности поэтому и сделал этот мануал.
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
 Посмотрим какие диски у нас есть, для этого воспользуемся командой:
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

Перед вами должно появится маленькое окошко в котором нужно выбрать gpt. 

Если данное окошко не появилось но у вас все так же как и на скринах ниже, то у вас и так все правильно, но если нет,
то у вас разметка MBR и чтобы её поменять воспользуйтесь утилитой gpart либо gdisk, как именно это сделать я добавлю когда нибудь, но название утилит я вам дал так что посмотрите.


Рассмотрим наш графический интерфейс:
![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/photo_2024-12-14_13-00-59.jpg)

Нажимаю на "New" и задаю размер 512M, этот раздел в будующем будет отвечать за наш grub загрузчик:
![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/photo_2024-12-14_13-00-56.jpg)

Далее выбираем вкладку "Type" и выбираем EFI System:
![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/photo_2024-12-14_13-00-54.jpg)

Раздел для grub загрузчика готов! Идем дальше.

Создаем swap раздел (раздел подкачки) обычно будет хватать 4G практически всегда, если у вас слишком мало места, то можно сделать и 1G.
Его можно вообще не делать, но я бы советовал все таки его создавать т.к. он будет помогать разгружать нашу оперативную память.

![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/photo_2024-12-14_13-00-51.jpg)

Когда мы дали разделу 4G переходим во вкладку "Type" и выбираем Linux Swap. То как у вас должно получиться показанно на скрине сверху.

Далее создаем корневой каталог и Home(по желанию),для них оставляем по дефолту "Linux filesystem" больше места старайтесь выделять для корневой папки, т.к. все пакеты которые вы будет устанавливать будут занимать
место именно в ней и если вы выделите мало места, то она быстро засорится, а чистить диск в линукс это тот ещё геморой.

![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/photo_2024-12-14_13-00-45.jpg)

Как вы можете заметить больше места достается корневому каталогу, у вас должно быть так же.
Если не хотите делать Home, то просто все место отдавайте корневой папке.

После того, как вы разделили диск выбираем "Write". Перед вами вылезет строка в которой обязательно нужно написать "yes" иначе изменения не сохранятся.

![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/photo_2024-12-14_13-00-31.jpg)


Проверяем нашу работу:
```bash
fdisk -l #можете так же посмотреть через lsblk, возможно кому-то будет удобнее
```

После этого меняем нашу файловую систему наших разделов:

Для grub раздела:
```bash
mkfs.fat -F32 /dev/sda1
```

Для swap раздела подкачки:
```bash
mkswap /dev/sda2
swapon /dev/sda2
```
Для корневого раздела и для раздела home используется одна файловая система так что мы просто меняем номер раздела, а команда остается такая же:
```bash
mkfs.ext4 /dev/sda3
mkfs.ext4 /dev/sda4
```

# 3.Установка Arch Linux

