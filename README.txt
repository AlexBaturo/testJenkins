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

systemd-cgtop top Для cgroups(модуль ядра)
systemctl cat apport-forward@.service
systemctl daemon-reload