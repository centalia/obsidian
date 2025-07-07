
# 1. Установка Arch Linux
## 1.1 Определяем диск

`lsblk --scsi` //Команда для просмотра SATA/USB дисков

Если так получилось, что ставить линух на NVMe 
`lsblk --nvme` //Команда для просмотра NVMe дисков
#### План разделов GPT для UEFI на SSD

| Раздел | Название | Формат | Размер         | Назначение         |
| ------ | -------- | ------ | -------------- | ------------------ |
| sda1   | efi      | FAT32  | 300 MiB        | Загрузочный раздел |
| sda2   | boot     | EXT4   | 1 GiB          | Ядро               |
| sda3   | swap     | SWAP   | по размеру ОЗУ | Раздел подкачки    |
| sda4   | root     | BTRFS  | ост. место     | Система            |

_при юзании `btfrs`, если не разделить `efi` и `boot` на разные разделы, не получится настроить `grub` для автоматической загрузки последнего удачного входа, будет загружаться всегда пункт меню по умолчанию,_ _а именно не будут работать эти параметры в файле `/etc/default/grub`:_

_`GRUB_DEFAULT=saved`_  
_`GRUB_SAVEDEFAULT=true`_
## 1.2 Разбивка диска 

1. Создаем разделы:
   
- _раздел EFI (300M)_  
    Last sector, ±sectors or ±size{K,M,G,T,P} (2048-500118158, default 500117503): `+300M`  

- _раздел BOOT (1G)_  
    Last sector, ±sectors or ±size{K,M,G,T,P} (616448-500118158, default 500117503): `+1G`  

- _раздел SWAP (8G) размер выбираем равным оперативной памяти_  
    Last sector, ±sectors or ±size{K,M,G,T,P} (2713600-500118158, default 500117503): `+16G`  

- _раздел ROOT (отдаем оставшееся место)_  
    Last sector, ±sectors or ±size{K,M,G,T,P} (19490816-500118158, default 500117503): ):`↵`  

2. Задаем типы разделов:

- _задаем тип EFI разделу_  
    Partition number (1-4, default 4): `1`  
    Partition type or alias (type L to list all): `1`  

- _задаем тип SWAP разделу_  
    Partition number (1-4, default 4): `3`  
    Partition type or alias (type L to list all): `19`  

- _остальные разделы не трогаем_

3.  Сохраняем таблицу

## 1.3 Форматируем разделы

- Форматируем efi
`mkfs.fat -F32 /dev/sda1`

- Форматируем boot
`mkfs.ext4 -L boot /dev/sda2`

- Форматируем и включаем swap
~~~ Arch 
mkswap -L swap /dev/sda3  
swapon /dev/sda3
~~~
  
- Форматируем root
`mkfs.btrfs -L arch /dev/sda4 -f`
## 1.4 Монтируем разделы

- Создаем тома и подтома 
~~~ Arch 
mount /dev/sda4 /mnt  
btrfs su cr /mnt/@ //корень  
btrfs su cr /mnt/@var  
btrfs su cr /mnt/@home  
btrfs su cr /mnt/@snapshots  
umount /mnt
~~~
  
- Монтируем разделы
~~~ Arch 
mount -o noatime,compress=lzo,space_cache=v2,ssd,subvol=@ /dev/sda4 /mnt  
mkdir -p /mnt/{home,boot,var,.snapshots}  
mount -o noatime,compress=lzo,space_cache=v2,ssd,subvol=@var /dev/sda4 /mnt/var  
mount -o noatime,compress=lzo,space_cache=v2,ssd,subvol=@home /dev/sda4 /mnt/home  
mount -o noatime,compress=lzo,space_cache=v2,ssd,subvol=@snapshots /dev/sda4 /mnt/.snapshots  
mount /dev/sda2 /mnt/boot  
mkdir /mnt/boot/efi  
mount /dev/sda1 /mnt/boot/efi
~~~
  
## 1.5 Установка системы

- Ставим систему
`pacstrap /mnt base base-devel linux linux-headers linux-firmware intel-ucode|amd-ucode nano`

- Генерируем fstab
`genfstab -pU /mnt >> /mnt/etc/fstab`

- Меняем корневой каталог на `/mnt`
`arch-chroot /mnt`

- Задаем пароль root
`passwd`

- Даем имя ПК
`nano /etc/hostname`

- Настраиваем временную зону
`ln -sf /usr/share/zoneinfo/Eupore/Moscow /etc/localtime`

- Открываем файл с локалями
`nano /etc/locale.gen`
- Раскомментируем в содержимом файла `locale.gen`
~~~ Arch 
ru_RU.UTF8 UTF8  
en_US.UTF8 UTF8
~~~

  - Создаем локали
`locale-gen`

- Настраиваем язык консоли, добавляем кириллицу
~~~ Arch 
nano /etc/vconsole.conf  
  
//в содержимом файла vconsole.conf:  
  
KEYMAP=ru  
FONT=cyr-sun16
~~~
  
  - Устанавливаем язык системы по умолчанию
~~~ Arch 
nano /etc/locale.conf  
  
//в содержимом файла locale.conf:  
LANG="ru_RU.UTF-8"
~~~
  
- Инициализируем пакетный менеджер pacman
`pacman-key --init`

- Загружаем ключи
`pacman-key --populate archlinux`

  - Настраиваем pacman
~~~ Arch 
nano /etc/pacman.conf  
  
Раскомментируем в содержимом файла pacman.conf  
[multilib]  
Include = /etc/pacman.d.mirrorlist
~~~
  
- Опционально можно включить следующие опции в секции # Misc options:
		- Color — цветная подсветка pacman;
		- ParallelDownloads` — количество параллельных загрузок, рекомендация не ниже 5;
		- ILoveCandy — можно добавить забавный прогрессбар загрузки пакетов в стиле игры Pacman;

- Обновляем, устанавливаем необходимое
~~~ Arch
pacman -Sy  
pacman -S bash-completion openssh arch-install-scripts networkmanager git wget htop neofetch xdg-user-dirs pacman-contrib ntfs-3g 
~~~
  
- _чтобы заработал `bash-completion` при использовании `TAB`, необходимо выйти из `chroot` (`Ctrl+D`) и войти снова `arch-chroot /mnt`_

- Создаем начальный загрузочный диск
~~~ Arch
mkinitcpio -p linux  
 nano /etc/sudoers  
  
//Раскомментируем в содержимом файла sudoers  
%wheel ALL=(ALL:ALL) ALL
~~~
  
- Создаем пользователя
`useradd -mg users -G wheel <<имя_пользователя>> `

- Задаем пароль пользователю (рекомендуется отличный от пароля root)
`passwd <<имя_пользователя>> `

- Добавляем в загрузку сетевой менеджер
`systemctl enable NetworkManager.service`

- Ставим загрузчик Grub 
~~~ Arch

pacman -S grub efibootmgr grub-btrfs os-prober  
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=Arch  
grub-mkconfig -o /boot/grub/grub.cfg
~~~

# 2. Установка графической оболочки
### 2.1 KDE

- Ставим `SDDM`
`pacman -S sddm`

- Ставим `Xorg` и `Plasma`
~~~ Arch
pacman -S --needed xorg  
pacman -S --needed plasma
~~~
Если нужны приложения ставим `kde-applications`

- Запускаем SDDM и сеть
~~~ Arch
sudo systemctl enable sddm  
sudo systemctl enable NetworkManager
~~~

### 2.2 Cosmic

## 2.3 GNOME 

# 3. Настройка системы

## 3.1 Настройка видеокарты
#### 3.1.1 Установка драйверов

Перед установкой драйвера отключаем к хуям _"Secure Boot"_ в UEFI, ибо из-за этого модули драйвера могут не загрузиться.

- NVIDIA ~~*"пидорасы из Санта Клары"*~~
~~~
sudo pacman -S nvidia-dkms nvidia-utils lib32-nvidia-utils nvidia-settings lib32-opencl-nvidia opencl-nvidia libxnvctrl vulkan-icd-loader lib32-vulkan-icd-loader libva-nvidia-driver
~~~
> ==Warning==
> Для правильной работы DKMS требуется также установить заголовки текущей версии ядра. Например, для стандартного ядра `linux` заголовки требуемые для сборки модулей находится внутри пакета `linux-headers`.

#### 3.1.2 Настраиваем драйвер
##### 3.1.2.1 Включаем KMS

> ***KMS - специальной подсистемы отвечающей за настройку режимов (разрешения/частоты обновления***

Совершенно обязательным шагом для всех обладателей карточек от зеленых пидорсов из Санта Клары это включение поддержки Kernel Mode Setting устройств вывода изображения (*встроенного дисплея или монитора*) на уровне ядра.

То есть, по сути KMS это промежуточное звено между DRM драйвером и контроллером вашего дисплея. 

Включение KMS позволяет:

- Использовать рабочие окружения на базе Wayland
- Задействовать PRIME Sync/PRIME Offload на ноутбуках с гибридной графикой (исправляет тиринг)
- Исправить проблемы с фреймбуфером, такие как установка неправильного разрешения в TTY.
- Использовать VA-API драйвер для аппаратного ускорения видео в Firefox или видеоплеере (см. далее).

Для того чтобы включить KMS в драйвере NVIDIA необходимо создать файл `/etc/modprobe.d/nvidia-kms.conf`
~~~ Arch
sudo nano /etc/modprobe.d/nvidia-kms.conf

/etc/modprobe.d/nvidia-kms.conf
options nvidia_drm modeset=1
~~~

> Примечание
> Начиная с версии драйвера 545.xx через пробел можно указать параметр `fbdev=1` для задействования аппаратного ускорения фреймбуфера. 

##### 3.1.2.2 Специальные переменные окружения

Эти переменные можно указать в Litris или в аргументах запуска в Steam.

> Примечание 
> После указания всех переменных обязательно добаляй в конце "_%command%_", для того чтобы Steam понимал, что указаны именно системные переменные окружения для запуска игры, а не параметры специфичные для этой самой игры

`__GL_THREADED_OPTIMIZATIONS=1` **(По умолчанию выключено)** - Активируем многопоточную обработку OpenGL. Используете выборочно для нативных игр/приложений, ибо иногда может наоборот вызывать регрессию производительности. Некоторые игры и вовсе могут не запускаться с данной переменной (К примеру, некоторые нативно-запускаемые части Metro).

`__GL_MaxFramesAllowed=1` **(По умолчанию - 2)** - Задает тип буферизации кадров драйвером. Можете указать значение _"3"_ (Тройная буферизация) для большего количества FPS и улучшения производительности в приложениях/играх с VSync. Мы рекомендуем задавать вовсе _"1"_ (т.е. не использовать буферизацию, подавать кадры так как они есть). Это может заметно уменьшить значение FPS в играх, но взамен вы получите лучшие задержки отрисовки и реальный физический отклик, т.к. кадр будет отображаться вам сразу на экран без лишних этапов его обработки.

`__GL_YIELD="USLEEP"` **(По умолчанию без значения)** - Довольно специфичный параметр, _"USLEEP"_ - снижает нагрузку на CPU и некоторым образом помогает в борьбе с тирингом, а _"NOTHING"_ дает больше FPS при этом увеличивая нагрузку на процессор.

`__GL_SHADER_DISK_CACHE_SKIP_CLEANUP=1` **(По умолчанию 0)** - отключает ограничение кэша шейдеров OpenGL/Vulkan (по умолчанию располагается по пути `~/.cache/nvidia`). Рекомендуется для современных нативных игр и DXVK 2.0+, где размер кэша может достигать более гигабайта.

`__GL_SYNC_DISPLAY_DEVICE` - указывает монитор с частотой которого драйвер NVIDIA будет осуществлять синхронизацию. Это нужно для конфигураций с двумя и более мониторами для предотвращения заиканий на дисплеях с высокой частотой обновления на некоторых композиторах (например Muffin). Представим, что у вас есть два дисплея, один 144 Гц, другой 60. В переменную следует указать видеовыход, в который подключен монитор с наибольшей частотой обновления. Чтобы определить какой монитор, в какой выход подключен можно использовать утилиту `xrandr`. Данную переменную имеет смысл указывать глобально, то есть в файле `/etc/environment`

~~~ Arch
sudo nano /etc/environment

 __GL_SYNC_DISPLAY_DEVICE=HDMI-0
~~~

##### 3.1.2.3 Добавляем модули в образ initramfs
~~~
sudo nano /etc/mkinitcpio.conf.d/10-modules.conf

/etc/mkinitcpio.conf.d/10-modules.conf
MODULES+=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)
~~~

## 3.2 Чистим мусор
К сожалению бывает ставишь оболочку, а помимо минимального набора ставится нахуй ненужный мусор. Пометим все пакеты из группы пакетов `gnome` или `plasma` как неявно установленных, то есть подтянутых в качестве зависимостей, после чего идет изменение причины установки базовых пакетов окружения уже как явно установленных, что позволяет разделить действительно нужные и мусорные пакеты по причине их установки и удалить все лишние пакеты к хуям собачим.

### 3.2.1 GNOME 
~~~
sudo pacman -D --asdeps $(pacman -Qqg gnome)
	sudo pacman -D --asexplicit gnome-shell mutter gdm gnome-control-center gnome-console nautilus gnome-session gnome-settings-daemon gvfs gvfs-mtp
sudo pacman -Rsn $(pacman -Qqgdtt gnome)
~~~

### 3.2.2 KDE Plasma
~~~
sudo pacman -D --asdeps $(pacman -Qqg plasma)
sudo pacman -D --asexplicit plasma-desktop breeze-gtk kde-gtk-config plasma-pa bluedevil sddm sddm-kcm plasma-nm
sudo pacman -Rsn $(pacman -Qqgdtt plasma)
~~~

## 3.3 Оптимизируем систему 

Переходя к оптимизации системы всегда помни, что чистый Arch Linux - это фундамент, и требуется уйма надстроек для нормальной работы системы. Прежде чем начнать, убедить что у тебя нет важных дел.

Прежде чем приступать к сборке пакетов, меняем флаги компиляции, что являются указателями для компилятора, какие инструкции и оптимизации использовать при сборке программ. 
#### 3.3.1 makepkg.conf

Для это делаем юзерский конфиг `~/.makepkg.conf` в домашней директории, чтобы переопределить системные настройки:

~~~
nano ~/.makepkg.conf

CFLAGS="-march=native -mtune=native -O2 -pipe -fno-plt -fexceptions \
      -Wp,-D_FORTIFY_SOURCE=3 -Wformat -Werror=format-security \
      -fstack-clash-protection -fcf-protection"
CXXFLAGS="$CFLAGS -Wp,-D_GLIBCXX_ASSERTIONS"
RUSTFLAGS="-C opt-level=3 -C target-cpu=native -C link-arg=-z -C link-arg=pack-relative-relocs"
MAKEFLAGS="-j$(nproc) -l$(nproc)"
~~~

Данные флаги компилятора выжимают максимум производительности при компиляции, но могут вызывать ошибки сборки в очень редких приложениях. Если такое случится, то отключаем параметр `lto` в строке `options` добавив перед ним символ восклицательного знака `!lto`.

#### 3.3.2 Юзаем tmpfs для сборки в ОЗУ
Во время сборки программ компилируются множество временных промежуточных файлов и записываются на диск (HDD/SSD) для последующей компоновки в исполняемый файл или библиотеку.

Для ускорения процесса сборки пакетов можно использовать вместо HDD/SSD - оперативную память, а точнее _tmpfs_.

Поскольку ОЗУ значительно быстрее любого HDD или SSD, то сборка происходит быстрее.

Помимо этого уменьшается нагрузка на систему ввода-вывода, и как следствие меньше изнашивается диск.

Использовать tmpfs для makepkg можно несколькими способами:

- Непосредственно указывать переменную перед сборкой:
~~~
BUILDDIR=/tmp/makepkg makepkg -sric
~~~

- Для сборки всего - задать параметр (раскомментировать в файле `/etc/makepkg.conf`) `BUILDDIR` для использования директории `/tmp`:
~~~
BUILDDIR=/tmp/makepkg
~~~

- Создать отдельную директорию _tmpfs_ заданного размера:
	- Необходимо добавить в `/etc/fstab` директорию для монтирования _tmpfs_, указав путь и максимальный объём директории, которая может расширяться при работе _tmpfs_ (учти что _tmpfs_ использует ОЗУ, поэтому внимательно подходи к вопросу выделяемого объема, он не должен превышать общий объем доступной памяти, несмотря на то, что изначально _tmpfs_ ничего не занимает), например:

   ~~~
     tmpfs   /var/tmp/makepkg tmpfs rw,nodev,nosuid,size=16G          0  0 
     ~~~

	- Далее, как и в предыдущем случае, указать `BUILDDIR` в `/etc/makepkg.conf`, но уже с путем к директории указанной в _fstab_

> Warning
> На системах с небольшим количеством ОЗУ (например 4 ГБ и менее) _tmpfs_ может негативно сказаться на сборке тяжёлых пакетов, что может привести к недостатку ОЗУ для сборки.

> Примечание
> Можно указать параметр PKGDEST для определения директории собранного пакета.

Количество доступного и используемого места в _tmpfs_ можно посмотреть:
~~~
df -h | grep tmpfs
~~~

#### 3.3.3 Включаем ccache

В Linux системах есть не так много программ, сборка которых может занять больше двух часов, но они все таки есть. Потому, было бы неплохо ускорить повторную компиляцию таких программ как Wine/Proton-GE и т.д.

ccache - это кэш для компиляторов C/C++, в частности совместимый с компиляторами GCC/Clang, цель которого состоит в ускорении повторного процесса компиляции одного и того же кода. Это значит, что если при сборке программы новой версии, будут замечены полностью идентичные блоки исходного кода в сравнении с его старой версией, то компиляция этих исходных текстов производиться не будет. Вместо этого, уже готовый, скомпилированный код старой версии будет вынут из кэша ccache. За счёт этого и достигается многократное ускорение процесса компиляции.

- Для начала ставим пакет
~~~
sudo pacman -S ccache
~~~

- Активируем в настройках makepkg. Для этого отредактируем `~/.makepkg.conf`
~~~
BUILDENV=(!distcc color ccache check !sign)
~~~

>**!!!!!!!!!Warning**
 ccache может ломать сборку некоторых программ, поэтому осторожно с ним.

#### 3.3.4 Устанавливаем полезные службы и демонюг

##### zram-generator

[zram-generator](https://aur.archlinux.org/packages/zram-generator/) — демон для создания блочных устройств ZRAM. ZRAM - это альтернативный механизм подкачки в ядре Linux, который позволяет избавиться от обычной подкачки на диске и сжимать неиспользуемые данные прямо внутри памяти ресурсами CPU.

TODO1: Изучить zram-generator

##### systemd-oomd

OOM киллером называют специальный демон, который предотвращает возникновение так называемых OOM (Out-Of-Memory) ситуаций. Если по простому, то он просто убивает самый "жирный" процесс в группе процессов (cgroup) прежде, чем он забьет всю память и ваш компьютер зависнет. В ядре Linux уже есть встроенный OOM киллер, но он отличается медленной скоростью реакции, поэтому лучше использовать OOM киллер в пространстве пользователя. Система инициализации systemd предлагает встроенный OOM киллер - `systemd-oomd`, который отличается малым потреблением ресурсом в фоне и не создает нагрузки на процессор, которая свойственна другим OOM киллерам из-за отслеживания потребления памяти процессов без использования механизма PSI, предлагаемым ядром Linux. 

~~~
sudo systemctl enable --now systemd-oomd
~~~

##### Ananicy CPP

[Ananicy CPP](https://gitlab.com/ananicy-cpp/ananicy-cpp) — это форк одноименного демона, распределяющий приоритет задач. Его установка очень сильно повышает отклик системы. В отличии от оригинального Ananicy, данный форк переписан полностью на C++, из-за чего достигается прирост в скорости работы.

~~~
git clone https://aur.archlinux.org/ananicy-cpp.git && cd ananicy-cpp //Клонируем репу
makepkg -sric //Собираем и ставим пакет
sudo systemctl enable --now ananicy-cpp //Запускаем демонюгу
cd .. && rm -rf ananicy-cpp //Убираем за собой
~~~

##### TRIM

[TRIM](https://ru.wikipedia.org/wiki/Trim_(команда_для_накопителей)) - это встроенная команда контроллера для очищения уже неиспользуемых ячеек на твердотельном накопителе. Её очень полезно периодически выполнять с целью профилактики SSD.

~~~
sudo systemctl enable fstrim.timer
~~~

Если используем не systemd то:
~~~
sudo fstrim -v /
~~~

> Предупреждение
> Если .юзаешь  Btrfs и ее версия ядра 6.2 и выше, то выполнять включение службы для осуществления периодическего выполнения команды TRIM - не нужно, т. к. Btrfs сам выполняет её в асинхронном режиме.

##### Автоматическая очистка кэша pacman
Кэш пакетов pacman имеет очень хуёвое свойство забиваться и со временем занимает много места на диске. Чтобы этого не происходило, сделаем небольшой демон, который будет автоматически его очищать, например, каждую неделю. В этом нам могут встроенные средства systemd для создания таймеров.

- Для этого сначала создадим службу `pacman-cleaner.service`:
~~~
/etc/systemd/system/pacman-cleaner.service
[Unit]
Description=Cleans pacman cache

[Service]
Type=oneshot
ExecStart=/usr/bin/pacman -Scc --noconfirm

[Install]
WantedBy=multi-user.target
~~~

- После создания службы, сделаем для него таймер `pacman-cleaner.timer`:
~~~
/etc/systemd/system/pacman-cleaner.timer
[Unit]
Description=Run clean of pacman cache every week

[Timer]
OnCalendar=weekly
AccuracySec=1h // <- Здесь задаем интервал срабатывания
Persistent=true

[Install]
WantedBy=timers.target
~~~

- А теперь запустим
~~~
sudo systemctl enable --now pacman-cleaner.timer
~~~

##### irqbalance

[irqbalance](https://github.com/Irqbalance/irqbalance) - это демон, что автоматически балансирует обработку прерываний по ядрам процессора.

~~~
sudo pacman -S irqbalance
sudo systemctl enable --now irqbalance
~~~

##### Отрубаем к хуям мусорные службы
###### Службы индексирования файлов
В Linux как и в Винде есть службы индексирования поиска, которая занимается тем, что в фоновом режиме проходит по всей файловой системе в поисках новых файлов или каталогов, чтобы внести их в собственную базу, которая будет использована для ускорения встроенного поиска или поиска в файловом менеджере. На первый взгляд все звучит заебись, но на практике жрет память, как хром, и нагружает диск.

В Linux подобные службы есть только у рабочих окружений GNOME и KDE Plasma. В KDE Plasma встроенным файловым индексатором является Baloo, который отличается своей склонностью часто "подтекать" с точки зрения потребления памяти, а в GNOME есть Tracker 3, который хоть и менее прожорливый по сравнению с аналогом от KDE, но все ещё потребляет не мало ресурсов.

Так как отрубаем всех подобных служб может положительно влиять на жизнь диска:

- **GNOME** 
~~~
systemctl --user mask tracker-extract-3 tracker-miner-fs-3 tracker-miner-fs-control-3 tracker-miner-rss-3 tracker-writeback-3 tracker-xdg-portal-3
rm -rf ~/.cache/tracker ~/.local/share/tracker
~~~

- **KDE Plasma**
~~~
balooctl6 suspend
balooctl6 disable
balooctl6 purge
~~~

###### Юзерские службы GNOME

GSD (gnome-settings-daemon) - службы настройки GNOME и связанных приложений. Это просто службы-настройки на все случаи жизни, которые просто жрут оперативную память в ожидании когда тебе, или другому приложению, к примеру, понадобиться настроить или интегрировать поддержку планшета Wacom в рабочее окружение, или для уведомления о различных событиях, таких как недостаточное место на диске или начале печати, а также для применения изменений совершенных в настройках GNOME на лету. 

Че можно отрубить к хуям?

- Служба интеграции рабочего окружения с графическим планшетом Wacom. Позволяет настраивать яркость планшета средствами окружения.

~~~
systemctl --user mask org.gnome.SettingsDaemon.Wacom.service
~~~

- Служба уведомления о начале печати. Если нет принтера или просто они нахуй не нужны.

~~~
systemctl --user mask org.gnome.SettingsDaemon.PrintNotifications.service
~~~

- Служба управления цветовыми профилями дисплея и принтеров. Если его отключить, то не будет работать тёплый режим экрана (Системный аналог Redshift):

~~~
systemctl --user mask org.gnome.SettingsDaemon.Color.service
~~~

- Служба управления специальными возможностями системы:
~~~
systemctl --user mask org.gnome.SettingsDaemon.A11ySettings.service
~~~

- Служба управления беспроводными интернет-подключениями и Bluetooth.
~~~
systemctl --user mask org.gnome.SettingsDaemon.Wwan.service
systemctl --user mask org.gnome.SettingsDaemon.Rfkill.service
~~~

- Службы защиты от неавторизованных USB устройств при блокировке экрана:
~~~
systemctl --user mask org.gnome.SettingsDaemon.UsbProtection.service
~~~

- Службу для автоматической блокировки экрана:
~~~
systemctl --user mask org.gnome.SettingsDaemon.ScreensaverProxy.service
~~~

- Служба для автоматического управления общим доступом к файлам и директориям:

~~~
systemctl --user mask org.gnome.SettingsDaemon.Sharing.service
~~~

- Служба интеграции рабочего окружения с карт-ридером:
~~~
systemctl --user mask org.gnome.SettingsDaemon.Smartcard.service
~~~



























##### Низкие задержки звука
[PipeWire](https://wiki.archlinux.org/title/PipeWire_(Русский)) - заебцовая альтернатива PulseAudio, которая призвана избавить от проблем PulseAudio, уменьшить задержки звука и потребление памяти. Внатуре охуенная вещь.

- Ставим Pipewire и запускаем службу
~~~
sudo pacman -S pipewire pipewire-pulse pipewire-jack lib32-pipewire gst-plugin-pipewire wireplumber realtime-privileges rtkit
systemctl --user enable --now pipewire pipewire.socket pipewire-pulse wireplumber
~~~

- Добавляем себя в группу `realtime`
~~~
sudo usermod -aG realtime "$USER"
~~~

- Создадим пути для хранения конфигурационных файлов в домашней директории:

~~~
mkdir -p ~/.config/pipewire/pipewire.conf.d
~~~

- В созданной директории создадим файл `10-no-resampling.conf`

~~~
nano ~/.config/pipewire/pipewire.conf.d/10-no-resampling.conf

10-no-resampling.conf
 context.properties = {
   default.clock.rate = 48000
   default.clock.allowed-rates = [ 44100 48000 96000 192000 ]
 }
~~~

Фактически здесь мы настраиваем две вещи: первое, это частота дискретизации, используемая по умолчанию `defalut.clock.rate`, в зависимости от которой PipeWire так же считает оптимальные задержки для вывода звука, а именно значения `quantum`. Во-вторых, мы явно перечисляем все доступные частоты дискретизации `defalut.clock.allowed-rates`, поддерживаемые нашим устройством вывода звука (. Это нужно для того, чтобы PipeWire не делал ресемплирования, то есть изменения частоты дискретизации исходного аудиопотока к частоте используемой по умолчанию, что может повлечь за собой ухудшение качества итогового звучания, а также дополнительные накладные расходы в виде нагрузки на CPU.

Важно отметить, что PipeWire выполняет переключение между указанными частотами дискретизации только в состоянии покоя непосредственно перед началом вывода нового аудиопотока, однако если вы начинаете проигрывать ещё один аудиопоток, то PipeWire не поменяет частоту дискретизации с учетом второго аудиопотока, а продолжит использовать ту же частоту дискретизации, что и у первого, и это может опять же привести к ситуации ресемплинга второго аудиопотока до уровня первого. В случае же если первоначальный аудиопоток не попадал в указанный диапозон частот, то он также бы ресемплировался от целовой к самой "ближайшей" частоте из перечисленных.

Чтобы узнать весь диапозон частот доступных для вашего устройства:

**Звуковой чип**
~~~
cat /proc/asound/card0/codec\#0 | grep -A 8 "Audio Output" -m 1 | grep rates
~~~

**ЦАП**
~~~
cat /proc/asound/card0/stream0 | grep Rates | uniq
~~~

Частоты, которые были получены таким образом, нужно прописать через пробел взамен тех, что даны в примере выше. Если доступно несколько устройств, то при помощи команды `cat /proc/asound/cards` узнайте номер звуковой карты, которая используется непосредственно для вывода звука, и подставьте его в команду выше.



~~~
- Включаем службу-таймер для обновления ключей 
``` Arch
sudo systemctl enable --now archlinux-keyring-wkd-sync.timer



-  Включение 32-битного репозитория
~~~ Arch
sudo nano /etc/pacman.conf           

Раскоментируем последние две строчки 
 [multilib]
 Include = /etc/pacman.d/mirrorlist
~~~

- Ставим reflector и rsync
~~~ Arch
sudo pacman -S reflector rsync
~~~
Reflector'ом ставим германские зеркала ибо они наиболее свежие и быстрые (для европейскиой части РФ)
~~~
sudo reflector --verbose --country 'Germany' -l 25 --sort rate --save /etc/pacman.d/mirrorlist
~~~
Если за границей то в комманде выше меняет Germany на Russia или ту страну в которой находишься.

Можно вручную добавлять зеркала из https://archlinux.org/mirrorlist/

~~~ Arch

sudo nano /etc/pacman.d/mirrorlist
~~~

## 3.4 Ставим софт

Шрифт для канзи, хираганы, катаканы
~~~
sudo pacman -S adobe-source-han-serif-jp-fonts
~~~

Для работы с архивами ставим:
~~~ Arch
sudo pacman -S lrzip unrar unzip unace p7zip squashfs-tools unarchiver ark
~~~
Последний больше для оболочки GNOME, чем для других.

- Мой набор джентельмента

~~~ Arch
sudo pacman -S qbittorrent firefox vlc code
~~~

Ставим yay

~~~
git clone https://aur.archlinux.org/yay.git && cd yay && makepkg -sric
~~~

Ставим Hyper, zsh, oh-my-zsh

```
yay -S hyper-bin && pacman -S zsh && sh -c "$(wget https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

Ставим тему Powerlevel10K

```
yay -S ttf-meslo-nerd-font-powerlevel10k //Ставим пропатченные шрифты для темы

yay -S --noconfirm zsh-theme-powerlevel10k-git && echo 'source /usr/share/zsh-theme-powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
```

Steam, wine, proton
~~~
sudo pacman -S steam ttf-liberation wine wine-mono winetricks
~~~

~~~
git clone https://github.com/Frogging-Family/wine-tkg-git.git
cd wine-tkg-git/wine-tkg-git
~~~

~~~
nano customization.cfg
~~~

~~~
yay -S ntsync-dkms ntsync-header
~~~

~~~
yay -S portproton protonup-qt dxvk-bin vkd3d-proton-bin
~~~

~~~
WINEPREFIX=~/.wine setup_dxvk install
~~~

~~~
setup_vkd3d_proton install ~/.wine
~~~