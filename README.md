# manual-ArchLinux
! Данный гайд я делал скорее для себя чем для кого-то, но если он кому то поможет мне будет очень приятно. Писал его основываясь на [Этом руководстве](https://github.com/Vlador1337/Arch-Linux-Install-Guide-RU) и на [Оффициальном руководстве](https://wiki.archlinux.org/title/Installation_guide_(Русский)#После_установки).
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

Настроим конфиг pacman, это не обязательно но появятся прикольные плюшки:

```bash
nano /etc/pacman.conf
```
Далее нужно найти Misc options и сделать так же как показанно:
```
#UseSyslog
Color
ILoveCandy
#NoProgressBar
CheckSpace
#VerbosePkgLists
ParallelDownloads = 10
```
Синхронизируем репозиторий для установки пакетов в будующем:
```bash
pacman -Syy
```
## 3.1 Установка системы 

Смонтируем корневой раздел корневой раздел "/dev/sda3" в катало "/mnt" передтем как выполнять установку:
```bash
mount /dev/sda3 /mnt
```
Для домашнего раздела(/dev/sda4) создадим и смонтируем папку home:
```bash
mkdir /mnt/home/ && mount /dev/sda4 /mnt/home
```
Далее установим самый необходимый минимум для нашей системы:
```bash
pacstrap -K /mnt base base-devel linux linux-firmware sudo nano 
```

# 4 Настройка системы 

После установки сгенерируем файл "/etc/fstab" для нашей системы:
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```
Теперь физически переключимся на корневой раздел:
```bash
arch-chroot /mnt
```
После этого повторяем пункт 3:
```bash
nano /etc/pacman.conf
#Редактируем как показанно в пункте
pacman -Syy
```
## 4.1Настройка часового пояса

Создаем символическую ссылку для установки часового пояса:
```bash
ln -sf /usr/share/zoneinfo/Регион(Russia)/Город(Yekaterinburg)
```
Соответственно меняете ваш регион и город под себя.

Настраиваем локали, это наши языки в системе. Редактируем файл
```bash
nano /etc/locale.gen
```
В данном файле нужно раскомментировать(убрать знак "#") перед en_US-UTF-8 UTF-8 и ru_RU.UTF-8 UTF-8, после этого
сохраняем "Ctrl+S" и выходим "Ctrl+X"

Генерируем локали:
```bash
locale-gen
```
Вводим данную команду что бы синхронизировать ваши часы и сгенерировать директорию /etc/adjtime:
```bash
hwclock --systohc
```
Создаем и устанавливаем переменную "LANG" этой командой мы задаем язык нашей системе:
```bash
echo LANG=ru_RU.UTF-8 > /etc/locale.conf
```
# 4.2 Настройка компьютера
Задаем имя нашего компьютера пусть это будет "ArchInst":
```bash
echo ArchInst > /etc/hostname
```

Редактируем "hosts":
```bash
nano /etc/hosts
```
И вставляем туда параметры снизу (ArchInst изменить на имя которое вы дали своему пк):
```bash
127.0.0.1   localhost
::1         localhost
127.0.1.1   ArchInst
```

Устанавливаем пароль для учетной записи root:
```bash
passwd
```
Нужно дважды ввести пароль, будь внимателен так как ты его не увидишь пока вводишь.

# 4.3 Установка GRUB загрузчика.
Устанавливем пакеты GRUB и EFIbootmanager:
```bash
pacman -S grub efibootmgr os-prober mtools
```
os-prober - сканирует существующие диски на наличие других ОС

Создаем директорию для загрузчика и монтируем "/dev/sda1":
```bash
mkdir /boot/efi
mount /dev/sda1 /boot/efi
```
Устанавливаем загрузчик:
```bash
grub-install --target=x86_64-efi --bootloader-id=grub_uefi
```
```
Output

Installing for x86_64-efi platform.
Installation finished. No error reported
```
Генерируем конфигурационный файл "/boot/grub/grub.cfg":
```bash
grub-mkconfig -o /boot/grub/grub.cfg
```
Здесь могут быть решения на ваши проблемы. Так же данная страница будет полезна если вы хотите установить Arch рядом с виндой.
[Все вот тут. Оффц. про GRUB](https://wiki.archlinux.org/title/GRUB_(Русский)).

# 6. Создание учетной записи обычного пользователя и чучуть о них

# The End
После того как ты сделал все что указанно выше кончай заниматься этой фигней старина:
```bash
exit
```
Незабудь размонтировать раздел "/mnt"
```bash
umount /mnt
```
```bash
reboot
```
После перезагрузки тебя должен встретить GRUB загрузчик, если что-то не получается или не понятно задавай вопросы, буду помогать с решением.
