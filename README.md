## **NFS**

При выполнении задания использовалось следующее ПО:

###**Хост**
** ОС - Linux Mint 19.3 **
** Гипервизор - VirtualBox 6.1.6**
** Средство для создания и конфигурирования виртуальной среды - Vagrant 2.2.7 **
** Создание образа виртуальной машины - Packer 1.5.5 **
** Система контроля версий -  Git 2.26.2 **

###**Виртуальные машины**
**сервер NFS** 
**ОС** - **CentOS Linux release 7.6.1810**
**имя** - **srv**
**ip** - **10.10.10.2**

**клиент NFS**
**ОС** - **CentOS Linux release 7.6.1810**
**имя** - **ws1**
**ip** - **10.10.10.3**


### **Установка и настройка NFS на сервер**

Установим NFS на сервер 
```
[vagrant@srv ~]$ sudo yum install nfs-utils
```

Настраиваем автозапуск

```
[vagrant@srv ~]$ sudo systemctl enable rpcbind nfs-server
Created symlink from /etc/systemd/system/multi-user.target.wants/nfs-server.service to /usr/lib/systemd/system/nfs-server.service.
```

Запускаем сервис

```
[vagrant@srv ~]$ sudo systemctl start rpcbind nfs-server
```

Создадим директорию для подключения пользователям и разрешим в ней чтение/запись

```
[vagrant@srv ~]$ sudo mkdir -p /mnt/nfs/upload
[vagrant@srv ~]$ sudo chmod -R 777 /mnt/nfs/upload/
```
Настроим правла для подключения директории

```
[vagrant@srv ~]$ cat /etc/exports
/mnt/nfs/upload 10.10.10.0/24(rw,sync,wdelay,secure,root_squash,no_all_squash)
```

Для вступления настроек в силу необходимо перечитать конфигурацию

```
[vagrant@srv ~]$ sudo exportfs -r

```

Проверим результат

```
[vagrant@srv ~]$ sudo exportfs 
/mnt/nfs/upload
		10.10.10.0/24
```

Настройки успешно применились

### **Установка и настройка NFS на клиент**

Установим NFS на клиент
```
[vagrant@srv ~]$ sudo yum install nfs-utils
```

Настраиваем автозапуск

```
[vagrant@ws1 ~]$ sudo systemctl enable rpcbind
```

Запускаем сервис

```
[vagrant@srv ~]$ sudo systemctl start rpcbind
```

Создадим директорию для монтирования 

```
[vagrant@ws1 ~]$ sudo mkdir -p /mnt/nfs/upload
```

Проверяем доступность ресурса и пробуем монтирование в директорию `mnt/nfs/upload`

```
[vagrant@ws1 ~]$ sudo mount -t nfs 10.10.10.2:/mnt/nfs/upload /mnt/nfs/upload/

[vagrant@ws1 ~]$ df -h |grep /mnt/nfs
10.10.10.2:/mnt/nfs/upload   40G  2.9G   38G   8% /mnt/nfs/upload
```

Для автоматического подключения добавим запись в `/etc/fstab`

Проверим возможность записи в директорию с клиента

```
[vagrant@ws1 ~]$ touch /mnt/nfs/upload/testfile
[vagrant@ws1 upload]$ ll -l
total 0
-rw-rw-r--. 1 vagrant vagrant 0 May 20 14:07 testfile
```

Проверим возможность наличие файла с сервера

```
[vagrant@srv ~]$ ll -l /mnt/nfs/upload/
total 0
-rw-rw-r--. 1 vagrant vagrant 0 May 20 14:07 testfile
```
