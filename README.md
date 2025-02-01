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

Выходим из этого режима:
```bash
exit
```

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
то у вас разметка MBR и чтобы её поменять читайте [этот пункт!!!](#Преобразование-диска-MBR-в-GPT), как именно это сделать я добавлю когда нибудь, но название утилит я вам дал так что посмотрите.


Рассмотрим наш графический интерфейс:
![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/photo_2024-12-14_13-00-59.jpg)

Нажимаю на "New" и задаю размер 512M, этот раздел в будущем будет отвечать за наш grub загрузчик:
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
После этого повторяем пункт 3 и дополнительно раскоментируем [multilib]:
```bash
nano /etc/pacman.conf
#Редактируем как показанно в пункте 3
```
```
#Раскоментируем мультилиб
[multilib]
Include = /etc/pacman.d/mirrorlist
```
```bash
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
grub-install --target=x86_64-efi --efi-directory=/boot/efi --boot-directory=/boot/efi/EFI
```
--bootloader-id=grub_uefi - Вместо grub_uefi пишите название для BIOS
```
Output

Installing for x86_64-efi platform.
Installation finished. No error reported
```
Если у вас ошибка с efibootmgr вам поможет эта команда: --no-nvram 
Генерируем конфигурационный файл "/boot/grub/grub.cfg":
```bash
grub-mkconfig -o /boot/grub/grub.cfg
```
Здесь могут быть решения на ваши проблемы. Так же данная страница будет полезна если вы хотите установить Arch рядом с виндой.
[Все вот тут. Оффц. про GRUB](https://wiki.archlinux.org/title/GRUB_(Русский)).

# 5. Установка необходимых пакетов и окружающих сред. 
## Установка двайверов и полезных пакетов:
Для видеокарт от Intel:
```bash
sudo pacman -S lib32-mesa vulkan-intel lib32-vulkan-intel vulkan-icd-loader lib32-vulkan-icd-loader intel-media-driver libva-intel-driver xf86-video-intel
```

Для видеокарт от NVIDIA:
```bash
sudo pacman -S nvidia nvidia-dkms nvidia-utils lib32-nvidia-utils nvidia-settings vulkan-icd-loader lib32-vulkan-icd-loader lib32-opencl-nvidia opencl-nvidia libxnvctrl
```

Для видеокарт от AMD/ATI:
```bash
sudo pacman -S lib32-mesa vulkan-radeon lib32-vulkan-radeon vulkan-icd-loader lib32-vulkan-icd-loader xf86-video-ati
```

Доустанавливаем пакеты:
```bash
pacman -S Networkmanager vim firefox git python3
```
Активируем Networkmanager:
```bash
systemctl enable NetworkManager
```
## 5.1 Установка Gnome:
На любое окружение есть темы так что не обязательно они будут выглядеть 1 в 1 как на картинках!
Устанавливаем xorg в качестве сервера отображения:
```bash
pacman -S xorg xorg-server xorg-apps
```
Далее устанавливаем само окружение gnome и несколько доп пакетов:
```bash
pacman -S gnome gnome-extra thunar
```
И активируем дисплейный менеджер:
```bash
systemctl enable gdm
```
![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/Gnome.jpg)

В чем их различие можно посмотреть [вот здесь](#про-дисплейные-менеджеры)
Мы также будем использовать другие для других окружений

## 5.2 Установка KDE Plasma
На любое окружение есть темы так что не обязательно они будут выглядеть 1 в 1 как на картинках!
Устанавливаем xorg в качестве сервера отображения:
```bash
pacman -S xorg xorg-server xorg-apps 
```
Установка Kde plasma:
```bash
pacman -S plasma plasma-wayland-session sddm nemo
#можете добавить kde-applications но там много ненужного легче будет докачать то что нужно
```
Активируем дисплейный менеджер
```bash
systemctl enable sddm.service
```
В чем их различие можно посмотреть [вот здесь](#про-дисплейные-менеджеры)

![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/KDEPlasma.jpg)

## 5.3 Установка Xfce
На любое окружение есть темы так что не обязательно они будут выглядеть 1 в 1 как на картинках!
Устанавливаем xorg в качестве сервера отображения:
```bash
pacman -S xorg xorg-server xorg-apps 
```
Установка Xfce:
```bash
sudo pacman -S xfce4-goodies file-roller  leafpad epdfview galculator lightdm lightdm-gtk-greeter
 lightdm-gtk-greeter-settings capitaine-cursors arc-gtk-theme xdg-user-dirs-gtk
```
Активируем дисплейный менеджер
```bash
systemctl enable lightdm
```

![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/Xfce.jpg)

## 5.4 Установка Hyperland
На любое окружение есть темы так что не обязательно они будут выглядеть 1 в 1 как на картинках!
Подробнее про установку и настройку вы можете прочитать на [официальной странице HyprlandWiki](https://wiki.hyprland.org/Getting-Started/Installation/)

Устанавливаем xorg в качестве сервера отображения:
```bash
pacman -S Wayland
```
Установка Hyprland:
```bash
sudo pacman -S hyprland hyprpaper xdg-desktop-portal-hyprland waybar wofi kitty sddm
```
Активируем дисплейный менеджер
```bash
systemctl enable sddm.service
```

Фото взято из [этого репозитория](https://github.com/JaKooLit/Arch-Hyprland), очень красивый конфиг Hyprland который устанавливается вот так:
```bash
git clone --depth=1 https://github.com/JaKooLit/Arch-Hyprland.git ~/Arch-Hyprland
cd ~/Arch-Hyprland
chmod +x install.sh
./install.sh
```
Лучше перейдите на его репозиторий и сами посмотрите что вам потребуется выбирать во время установки.

![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/Hyprland.jpg)

## 5.5 Установка LXQT
На любое окружение есть темы так что не обязательно они будут выглядеть 1 в 1 как на картинках!
Этот вариант лучше ставить если у вас слабый компьютер или ноутбук.

Устанавливаем xorg в качестве сервера отображения:
```bash
pacman -S xorg xorg-server xorg-apps
```
Устанавливаем само окружение:
```bash
sudo pacman -S lxqt xdg-utils ttf-freefont sddm
```
Установка доп. библиотек:
```bash
sudo pacman -S libpulse libstatgrab libsysstat lm_sensors oxygen-icons pavucontrol-qt
```
Этой системой я не пользовался(не было надобности так что, что в xdg-utils я без понятия.

Активируем дисплейный менеджер
```bash
systemctl enable sddm.service
```

![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/Lxqt.jpg)

# 6. Создание учетной записи обычного пользователя и чучуть о них.
Напишите свое имя вместо “User123”.
```bash
useradd -m -G wheel user123
```
-m — создаёт домашний каталог пользователя по адресу /home/[имя пользователя]. В каталоге создаётся набор стартовых файлов, владельцем которых назначается новый пользователь.

-G — определяет список дополнительных групп, в которые входит пользователь. По умолчанию новый пользователь добавляется только в начальную группу.

wheel — группа администрирования, обычно используемая для предоставления доступа к sudo и su.

Задаем пароль новому пользователю:
```bash
passwd User123
```
Что бы дать пользователю sudo возпользуйтесь:
```bash
sudo visudo
```
Если вы не смогли выйти из vim и вышли спомощью Ctrl + Z, то при попытке повторного ввода команды или размонтирования диска может быть предупреждение что этот процесс уже запущен, что бы его остановить вводим"
```bash
fg
```
и ::q чтобы выйти из vim

И раскомментируйте данную строчку убрав знак #:
```
# %wheel ALL=(ALL) ALL
```
Если ваш пользователь в другой группе вы можете его добавить в группу sudo. Вместо User123 имя вашего пользователя:
```bash
usermod -aG sudo User123
```
Опция -a означает «присоединить» (или «добавить»), а -G — это сокращение от «группы». 
Таким образом, эта комбинация добавляет указанную группу к указанному пользователю, не затрагивая существующие группы пользователя. 

Устанавливаем пароль для учетной записи root:
```bash
passwd
```
Нужно дважды ввести пароль, будь внимателен так как ты его не увидишь пока вводишь.


# The End
После того как ты сделал все что указанно выше кончай заниматься этой фигней старина:
```bash
exit
```
Незабудь размонтировать раздел "/mnt"
```bash
umount -R /mnt
```
```bash
reboot
```
После перезагрузки тебя должен встретить GRUB загрузчик, если что-то не получается или не понятно задавай вопросы, буду помогать с решением.

# Про дисплейные менеджеры
[Информацию взял тут](https://dzen.ru/a/Y6iCA91VZx3sMYYO)


### GDM (Gnome Display Manager или экранный менеджер GNOME)

Помимо ввода логина и пароля пользователю доступна смена среды рабочего стола, смена графического сервера  действия по работе с сеансом (спящий режим, перезагрузка, выключение).

Для меня преимуществами GDM является то, что он «из коробки» имеет достойный внешний вид и его не нужно настраивать дополнительно, если вы используете его вне «родной» среды рабочего стола.
Недостатками является то, что при установке в дистрибутив тащится большое количество зависимостей GNOME.

Шесть расширений в GNOME которые устанавливаю всегда

### LightDM (Light Display Manager)

Легкий, быстрый и расширяемый дисплейный менеджер. Функциональность LightDM такая же, как и у GDM, но
в отличие от собрата он имеет кодовую базу попроще и не тянет в дистрибутив зависимости GNOME.
До версии 17.10 LightDM являлся дисплейным менеджером по-умолчанию в Ubuntu.

### LXDM (LX Display Manager)
Разрабатывался как альтернатива и замена для GDM. Главными преимуществами является легковесность и энергоэффективность (что делает удобным его использование на ноутбуках).
Таким образом, при помощи LXDM и используемой среды рабочего окружения LXDE можно оживить тот ПК или ноутбук,
который кажется совсем устаревшим в плане производительности оборудования.

### SDDM (Simple Desktop Display Manager)

Дисплейный менеджер, который пришел на смену KDE Display Manager (KDM). Написан он на C++ и полностью интегрируется в среду рабочего стола KDE Plasma 5. 

Внешний вид SDDM
Это тот дисплейный менеджер, который я выбираю всегда при работе с вновь установленным Archlinux.  Очевидным преимуществом является простота изменения и отсутствие перегруженности интерфейса. Недостатком является то, что «из коробки» SDDM выглядит очень не очень. Сразу тянется рука подкрутить что-нибудь.

# Файловые менеджеры
Их существует достаточно много, но здесь я покажу только те которые нравятся мне. Я не знаю чем они отличаются так что выбирать буду по визуалу. 

Thunar:

![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/Thunar.jpg)

Dolphine:

![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/Dolphin.jpg)

Nemo:

![Image alt](https://github.com/Maruma02/manual-ArchLinux/raw/assets/Nemo.jpg)


Все картинки взяты из интернета для примерного понимания.

# Установка yay/aur:
```bash
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

# Преобразование диска MBR в GPT

Для преобразования таблицы разделов MBR в GPT с помощью sgdisk используйте опцию -g/--mbrtogpt:
```bash
sgdisk -g /dev/sda
```
Для преобразования GPT в MBR используйте опцию -m/--gpttombr. Имейте в виду, что преобразовать более четырёх разделов из GPT в MBR невозможно.
```bash
sgdisk -m /dev/sda
```
