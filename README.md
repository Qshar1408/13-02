# Домашнее задание к занятию  «Защита хоста»
#### Грибанов Антон. FOPS-31

### Задание 1

1. Установите **eCryptfs**.
2. Добавьте пользователя cryptouser.
3. Зашифруйте домашний каталог пользователя с помощью eCryptfs.


*В качестве ответа  пришлите снимки экрана домашнего каталога пользователя с исходными и зашифрованными данными.*  

#### Решение:
```bash
sudo apt update
sudo apt install -y ecryptfs-utils

#sudo adduser --encrypt-home cryptonec
sudo adduser cryptonec
sudo usermod -aG sudo cryptonec

su - cryptonec
pwd
touch readmefile
#ls -al /home/cryptonec
ls -al ~
exit

sudo ls -al /home/cryptonec
sudo ecryptfs-migrate-home -u cryptonec #Миграция домашнего каталога пользователя cryptonec
sudo ls -al /home/cryptonec
```
   ##### До шифрования
![13-02](https://github.com/Qshar1408/13-02/blob/main/img/hw_13_02_001.png)

   ##### После шифрования
![13-02](https://github.com/Qshar1408/13-02/blob/main/img/hw_13_02_002.png)

### Задание 2

1. Установите поддержку **LUKS**.
2. Создайте небольшой раздел, например, 100 Мб.
3. Зашифруйте созданный раздел с помощью LUKS.

*В качестве ответа пришлите снимки экрана с поэтапным выполнением задания.*

#### Решение:

```bash
sudo apt install -y gparted cryptsetup
cryptsetup --version

sudo fdisk -l #Определяем подключенную флешку
mount #Если флешка подмонтирована - отключаем
sudo umount /dev/sdb4 
sudo cryptsetup -y -v --type luks2 luksFormat /dev/sdb4 #Подготавливаем раздел (luksFormat)
sudo cryptsetup -q luksFormat /dev/sdb4
sudo cryptsetup luksOpen /dev/sdb4 disk #Открываем раздел
ls /dev/mapper/disk

sudo dd if=/dev/zero of=/dev/mapper/disk
sudo mkfs.ext4 /dev/mapper/disk

mkdir .secret 
sudo mount /dev/mapper/disk .secret/ #Монтируем раздел

sudo umount .secret
sudo cryptsetup luksClose disk #Закрываем раздел
```
   1. Устанавливаем поддержку LUKS.
![13-02](https://github.com/Qshar1408/13-02/blob/main/img/hw_13_02_003.png)
   2. Создаем небольшой раздел, например, 100 Мб.
![13-02](https://github.com/Qshar1408/13-02/blob/main/img/hw_13_02_003.png)
![13-02](https://github.com/Qshar1408/13-02/blob/main/img/hw_13_02_004.png)
   3. Зашифровываем созданный раздел с помощью LUKS.
![13-02](https://github.com/Qshar1408/13-02/blob/main/img/hw_13_02_005.png)
![13-02](https://github.com/Qshar1408/13-02/blob/main/img/hw_13_02_006.png)

## Дополнительные задания (со звёздочкой*)

Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале

### Задание 3 *

1. Установите **apparmor**.
2. Повторите эксперимент, указанный в лекции.
3. Отключите (удалите) apparmor.


*В качестве ответа пришлите снимки экрана с поэтапным выполнением задания.*

 #### Решение:
```bash
sudo apt install -y apparmor-profiles apparmor-utils apparmor-profiles-extra
sudo aa-status
sudo systemctl status apparmor.service

sudo systemctl stop apparmor.service
sudo aa-teardown

sudo cp /usr/bin/man /usr/bin/man1
sudo cp /bin/ping /usr/bin/man
sudo getcap $(which ping)
sudo getcap $(which man)
sudo setcap cap_net_raw+ep $(which man)

ping -c 1 localhost
man -c 1 localhost
sudo aa-enforce man
man -c 1 localhost

sudo mv /usr/bin/man1 /usr/bin/man
```
![13-02](https://github.com/Qshar1408/13-02/blob/main/img/hw_13_02_007.png)
![13-02](https://github.com/Qshar1408/13-02/blob/main/img/hw_13_02_008.png)
![13-02](https://github.com/Qshar1408/13-02/blob/main/img/hw_13_02_009.png)
![13-02](https://github.com/Qshar1408/13-02/blob/main/img/hw_13_02_010.png)
