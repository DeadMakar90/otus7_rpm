# otus7_rpm
##Размещаем свой RPM в своем репозитории
Цель: Часто в задачи администратора входит не только установка пакетов, но и сборка и поддержка собственного репозитория. Этим и займемся в ДЗ.
1) создать свой RPM (можно взять свое приложение, либо собрать к примеру апач с определенными опциями)
2) создать свой репо и разместить там свой RPM
реализовать это все либо в вагранте, либо развернуть у себя через nginx и дать ссылку на репо 

* реализовать дополнительно пакет через docker
Критерии оценки: 5 - есть репо и рпм
+1 - сделан еще и докер образ

###1. Сборка RPM пакета Zabbix:

Выполняем установку пакетов, необходимых для сборки:
```
sudo yum groupinstall -y "Development Tools"
sudo yum install -y wget createrepo yum-utils gnutls-devel
```
Скачиваем пакеты исходников:
```
cd /root
sudo wget https://repo.zabbix.com/zabbix/3.0/rhel/7/SRPMS/zabbix-3.0.31-1.el7.src.rpm
sudo wget https://repo.zabbix.com/non-supported/rhel/7/x86_64/iksemel-1.4-2.el7.centos.x86_64.rpm
sudo wget https://repo.zabbix.com/non-supported/rhel/7/x86_64/iksemel-devel-1.4-2.el7.centos.x86_64.rpm
sudo wget https://repo.zabbix.com/non-supported/rhel/7/x86_64/iksemel-utils-1.4-2.el7.centos.x86_64.rpm
```
Устанавливаем пакеты:
```
sudo rpm -i *64.rpm
sudo rpm -i zabbix-3.0.31-1.el7.src.rpm
```
Устанавливаем зависимости:
```
sudo yum-builddep -y /root/rpmbuild/SPECS/zabbix.spec
```
Собираем пакет:
```
sudo rpmbuild -bb /root/rpmbuild/SPECS/zabbix.spec
```

###2. Создаем репозиторий

Устанавливаем необходимые пакеты:
```
sudo yum install -y epel-release
sudo yum install -y nginx
sudo systemctl enable nginx
```
Создаем папку с репозиторием:
```
sudo mkdir /usr/share/nginx/html/repo
```
Копируем в папу собранные пакеты:
```
sudo cp /root/rpmbuild/RPMS/x86_64/zabbix*.rpm /usr/share/nginx/html/repo
```
Сздаем репозиторий:
```
sudo createrepo /usr/share/nginx/html/repo/
sudo createrepo --update /usr/share/nginx/html/repo
```
Приводим репозиторий к виду:
```
sudo touch /etc/yum.repos.d/zabbixotus.repo
echo -e "[zabbixotus]\nname=zabbixotus\nbaseurl=http://localhost/repo\ngpgcheck=0\nenabled=1" | sudo tee -a /etc/yum.repos.d/zabbixotus.repo
```
Запускаем nginx:
```
sudo systemctl start nginx
```
Для проверки репозитория выполняем команду:
```
yum list | grep zabbixotus
```
Команда должна вернуть список пакетов из репозитория.