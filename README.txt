Загрузка ОС

Процессор переходит на адрес биос и загружает его
биос выбирает носитель информации, на носителе находится загрузчик
загрузчик может загружать сразу ОС или передавать управление следующему загрузчику
второй загрузчик (LILO, GRUB, NTLDR(windows)) загружает ядро ОС и грузит initrd(там конфиги и модули нужные ядру)
далее запускается ядро. Происходит иницилизация устройств, конфигуриров процессоров, памяти и тд
После всех процессов иницилизации ядро запускает процесс init

dmesg - все сообщения ядра
/var/log/dmesg - информация о загрузки
ядро лежит в директории /boot
загрузчик в /boot/grub

Инициализация системы
systemd — это система инициализации и системный диспетчер
unit:
	.service, .mount(точки монтирования), .device(устройства), socket
/usr/lib/systemd/system/ – юниты из установленных пакетов RPM
/etc/systemd/system/ - юниты, созданные системным администратором
Для создания простейшего юнита надо описать три секции: [Unit], [Service], [Install]

[Unit]
Description=Daily apt download activities
After=network.target
Requires=mysql.service
Wants=redis.service

[Service]
Type=oneshot
Environment=RACK_ENV=production

ExecStart=/usr/local/bin/bundl
ExecStop=/usr/local/bin/bundle
ExecReload=/ExecStar
TimeoutSec=300

[Install] в каком уровне запуска должен стартовать сервис
WantedBy=multi-user.target

systemctl  - все запущенные юниты
systemctl --failed - все сломаные юниты
systemctl --type=service фильтр юнитов


lsb_release -a версия ОС

systemd-cg
top Для cgroups(модуль ядра)
systemctl cat apport-forward@.service
systemctl daemon-reload

RAM
vmstat 
swap - раздел жесткого диска, который исп, если заканчивается RAM
swapon swapoff
mkswap создание раздела подкачки
buff(heap) - место в памяти, зарезервированное под конкретные процессы
cache - использованные недавно файлы 

cat /proc/meminfo
top
shift+m - сортировка по памяти
shift+f - выбрать по чему сортировать. s чтобы сохранить. q Для возврата
VIRT, RES, SHR и %MEM
Эти три поля связаны с потреблением памяти процессами. «VIRT» - это общий объем памяти, потребляемой процессом. Это включает код программы, данные, хранящиеся процессом в памяти, а также любые области памяти, которые были выгружены на диск. «RES» - это память, используемая процессом в ОЗУ, а «% MEM» выражает это значение в процентах от общего объема доступной ОЗУ. Наконец, «SHR» - это объем памяти, совместно используемый другими процессами.

жесткий диск
iostat показатели чтения и записи на устройство
/etc -конфигурация системы и ее компонентов
/opt/ - папка для ПО от третьих поставщиков
/var - часто меняющиеся данные
/usr - все установленные пакеты программ, документация, код ядра
разделы жестких дисков
/ - самый большой раздел
/boot - загруз раздел.(примерно 50-100 MB, Хранится ядро) монтируется на отдельный раздел тк старые биосы могу не увидеть эту папку в общем разделе
/home - можно монтировать как сетевую папку
/var - лучше примонтировать ssd, тк частое обращение к памяти
sda1 - sda4 зарезевированы под разделы жесткого диска
логические начинаются с sda5
fdisk - разбиение ЖД 
mkfs.ext4 /dev/sdb5 - создание файловой системы
/etc/fstab - монитрование на постоянно основе

RAID 0 - часть файла записывает на 1 диск, часть на другой
+ файл одновременно записывается на все диски
- потеряются все данные, если один диск сломается
подходит для non critical систем

RAID 1 - один диск дублирует второй
+ возрастает скорость чтения с диска
+ безопасное хранение данных
- объем диска не возрастает, тк они дублируют друг друга

RAID 5 - минимум 3 диска. https://adatahelp.ru/wp-content/uploads/2020/10/raid-5-1024x607.png
+ легко восстановить данные
- из 3х дисков по 1 Тб доступно будет только 2 Тб , тк 3й ушел под служебную инфу(таблицу)

RAID 10 - минимум 4 диска. Диски должны быть в парах. объединяет RAID 0 и RAID 1
+ Быстрая скорость IO
- Покупая 4 Тб, получаем 2. Тк два других - зеркало


iptables -L  посмотреть все правила
Netfilter
есть разделы INPUT FORWARD OUTPUT
для докера сущ отдельный раздел DOCKER
можно налету менять порт sudo iptables -A DOCKER -p tcp  --dport 100 -d 172.17.0.2 -j ACCEPT
-A - добавить правило, -D удалить
-p -порт и его тип
-s source (если не использовать флаг, то anywhere)
-d destination (если не использовать флаг, то anywhere)
-j ACCEPT - разрешить, DROP - запретить, REJECT- отклонить. Пользователи придет сообщение, что пакет отклонен
чтобы сохранилось после  перезагрузки /etc/init.d/iptables save
iptables -S - правила по умолчинию

netstat -ie тоже самое что и ifconfig
netstat -tuna
t -tcp
u - udp
n - ip вместо hostname
a - все установленные соединения
l - порты, которые слушают
p - программы, которые используют порт 

speedtest cli - утилита для измерения скорости сети. Утили не выходит в интернет а стучится в сервер провайдера, максимально близкий к клиенту.

traceroute ya.ru для отслеживания пути пакета (можно проверять доступность маршрутизаторов)

mtr то же , что и traceroute, но пакеты летят постоянно

telnet ip port - для установки канала связи

почистить кэш
sync; echo 1 > /proc/sys/vm/drop_caches

создать swap file
dd if=/dev/zero of=/root/myswapfile bs=1M count=1024 #1 ГБ
chmod 600 /root/myswapfile
mkswap /root/myswapfile
swapon /root/myswapfile
# cat /etc/fstab
/root/myswapfile               swap                    swap    defaults        0 0

lsof -p PID - открытые процессом файлы
locate logfileNAME - Найти лог файл

dockerd - управление ресурсами контейнеров

жесткая ссылка - создает файл с тем же inode. Нельзя делать ссылку на папке
мягкая - создает файл со своим inode. Ссылается на имя файла

df -i проверить inodes

echo $$ > /sys/fs/cgroup/cpuset/group0/tasks
https://habr.com/ru/company/selectel/blog/303190/

MBR(master boot record) - первые 512 байт диска. Участок в начале ЖД, зарезервированный для загрузчика ОС

lsblk
pvcreate /dev/sdb 
vgextend sintezm-client /dev/sdb
lvdisplay
lvextend -l +100%FREE /dev/sintezm-client/root
resize2fs /dev/sintezm-client/root
df -h

https://darksf.ru/2020/01/02/nastrojka-i-upravlenie-lvm-razdelami-v-linux/


Для nexus
lvextend -l +100%FREE /dev/nexus_repo/repo
resize2fs /dev/nexus_repo/repo
