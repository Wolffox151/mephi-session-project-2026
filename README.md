```bash
# Создание папки для результатов.
mkdir -p ~/mephi-session-project-2026

# Просмотр сетевых подключений.
nmcli connection show

# Просмотр метода настройки IPv4.
nmcli connection show ens3 | grep ipv4.method

# Включение DHCP для подключения ens3.
nmcli connection modify ens3 ipv4.method auto

# Активация подключения ens3.
nmcli connection up ens3

# Установка имени хоста.
hostnamectl set-hostname mephi-2026.domain.local

# Просмотр сведений о хосте.
hostnamectl

# Сохранение проверки внешней связи.
ping -c 4 8.8.8.8 > ~/mephi-session-project-2026/ping.out

# Просмотр результата проверки связи.
cat ~/mephi-session-project-2026/ping.out

# Обновление пакетов системы.
dnf update -y

# Установка nginx и утилит capabilities.
dnf install -y nginx libcap-ng-utils

# Создание папки для RPM-пакетов.
mkdir -p /tmp/mephi-tcpdump

# Загрузка tcpdump и недостающих зависимостей.
dnf download --resolve --downloaddir=/tmp/mephi-tcpdump tcpdump

# Установка скачанных RPM, включая повторную установку.
rpm -Uvh --replacepkgs /tmp/mephi-tcpdump/*.rpm

# Проверка установленного пакета tcpdump.
rpm -q tcpdump

# Сохранение истории транзакций DNF.
dnf history > ~/mephi-session-project-2026/dnf.out

# Просмотр дисков и разделов.
lsblk

# Создание таблицы разделов GPT на дополнительном диске.
parted --script /dev/sda mklabel gpt

# Создание одного раздела на доступное пространство.
parted --script /dev/sda mkpart primary ext4 1MiB 100%

# Обновление сведений ядра о разделах.
partprobe /dev/sda

# Создание ext4 с меткой MEPHI_WEB.
mkfs.ext4 -L MEPHI_WEB /dev/sda1

# Создание точки монтирования.
mkdir -p /mephi-web

# Добавление автомонтирования по метке без дублирования строки.
grep -qxF 'LABEL=MEPHI_WEB /mephi-web ext4 defaults 0 2' /etc/fstab || printf '\nLABEL=MEPHI_WEB /mephi-web ext4 defaults 0 2\n' >> /etc/fstab

# Обновление настроек systemd.
systemctl daemon-reload

# Монтирование файловой системы.
mount /mephi-web

# Сохранение файла fstab для сдачи.
cp /etc/fstab ~/mephi-session-project-2026/fstab

# Запуск nginx.
systemctl start nginx

# Включение автозапуска nginx.
systemctl enable nginx

# Сохранение журнала nginx за текущую загрузку.
journalctl -u nginx -b > ~/mephi-session-project-2026/journalctl.out

# Создание директории проекта.
mkdir -p /data/mephi-2026

# Создание группы разработчиков.
groupadd developers

# Создание user1 с UID 5501.
useradd -m -u 5501 -G developers user1

# Создание user2 с UID 5502.
useradd -m -u 5502 -G developers user2

# Создание user3 с UID 5503.
useradd -m -u 5503 -G developers user3

# Создание группы кураторов с GID 4444.
groupadd -g 4444 curators

# Создание первого куратора.
useradd -m -G curators curator1

# Создание второго куратора.
useradd -m -G curators curator2

# Назначение владельца и группы директории проекта.
chown root:developers /data/mephi-2026

# Настройка доступа и наследования группы.
chmod 2770 /data/mephi-2026

# Разрешение кураторам читать каталог и заходить в него.
setfacl -m g:curators:r-x /data/mephi-2026

# Наследование прав кураторов новыми объектами.
setfacl -d -m g:curators:r-x /data/mephi-2026

# Просмотр прав и ACL директории.
ls -ld /data/mephi-2026 && getfacl /data/mephi-2026

# Наследование прав разработчиков новыми объектами.
setfacl -d -m g:developers:rwx /data/mephi-2026

# Просмотр итоговых ACL.
getfacl /data/mephi-2026

# Сохранение сведений о директории проекта.
stat /data/mephi-2026 > ~/mephi-session-project-2026/stat.out

# Сохранение пользователей и групп.
cp /etc/passwd /etc/group ~/mephi-session-project-2026/

# Просмотр прав исполняемого файла tcpdump.
ls -l /usr/sbin/tcpdump

# Назначение capability для захвата сетевых пакетов.
setcap cap_net_raw+ep /usr/sbin/tcpdump

# Просмотр и сохранение capabilities tcpdump.
getcap /usr/sbin/tcpdump | tee ~/mephi-session-project-2026/getcap.out

# Проверка запуска tcpdump от имени user1.
sudo -u user1 tcpdump

# Просмотр и сохранение режима SELinux.
getenforce | tee ~/mephi-session-project-2026/getenforce.out

# Просмотр исходного контекста web-каталога.
ls -ldZ /mephi-web

# Добавление постоянного правила SELinux для сайта.
semanage fcontext -a -t httpd_sys_content_t "/mephi-web(/.*)?"

# Применение контекста к каталогу и его содержимому.
restorecon -Rv /mephi-web

# Просмотр контекста после изменения.
ls -ldZ /mephi-web

# Добавление сведений о web-каталоге в stat.out.
stat /mephi-web >> ~/mephi-session-project-2026/stat.out

# Запрет интерактивного входа для curator1.
usermod -s /sbin/nologin curator1

# Запрет интерактивного входа для curator2.
usermod -s /sbin/nologin curator2

# Просмотр оболочек кураторов.
grep curator /etc/passwd

# Просмотр имён и оболочек остальных участников.
getent passwd nnt user1 user2 user3 | cut -d: -f1,7

# Обновление сохранённого файла passwd.
cp /etc/passwd ~/mephi-session-project-2026/passwd

# Установка срока действия пароля user1: 90 дней.
chage -M 90 user1

# Установка срока действия пароля user2: 90 дней.
chage -M 90 user2

# Установка срока действия пароля user3: 90 дней.
chage -M 90 user3

# Установка срока действия пароля curator1: 90 дней.
chage -M 90 curator1

# Установка срока действия пароля curator2: 90 дней.
chage -M 90 curator2

# Установка минимальной длины пароля: 12 символов.
echo 'minlen = 12' | tee -a /etc/security/pwquality.conf

# Применение проверки качества пароля также для root.
echo 'enforce_for_root' | tee -a /etc/security/pwquality.conf

# Сохранение настроек паролей для сдачи.
cp /etc/shadow /etc/security/pwquality.conf ~/mephi-session-project-2026/

# Повторное копирование настроек паролей.
cp /etc/shadow /etc/security/pwquality.conf ~/mephi-session-project-2026/

# Создание страницы с номером зачётной книжки.
echo "Hello from Student: М265852" | tee /mephi-web/index.html

# Сохранение резервной копии конфигурации nginx.
cp -a /etc/nginx/nginx.conf /etc/nginx/nginx.conf.mephi-backup

# Изменение корня сайта на /mephi-web.
sed -i 's|/usr/share/nginx/html|/mephi-web|g' /etc/nginx/nginx.conf

# Проверка конфигурации и её применение при отсутствии ошибок.
nginx -t && systemctl reload nginx

# Просмотр страницы и сохранение ответа сервера.
curl http://localhost/ | tee ~/mephi-session-project-2026/curl.out

# Сохранение истории выполненных команд.
history > ~/mephi-session-project-2026/history.out
```