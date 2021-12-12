# devops-netology

Здравствуйте, Фкирдымов Тимур комментарий к заданию 3.5


***1. Узнайте о sparse (разряженных) файлах.***   
Ознакомился.

***2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?***  
Не могут, потому что ссылаются на один и тот же inode. Если мы изменим права на одном файле – жесткой ссылке, то права также изменятся у второго(третьего и т.д.) файла-жесткой ссылки.

***3. Сделайте vagrant destroy на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим…. Данная конфигурация создаст новую виртуальную машину с двумя дополнительными неразмеченными дисками по 2.5 Гб.***  
Выполнил:  
vagrant@vagrant:~$ lsblk  
NAME                 MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT  
sda                    8:0    0   64G  0 disk  
├─sda1                 8:1    0  512M  0 part /boot/efi  
├─sda2                 8:2    0    1K  0 part  
└─sda5                 8:5    0 63.5G  0 part  
  ├─vgvagrant-root   253:0    0 62.6G  0 lvm  /  
  └─vgvagrant-swap_1 253:1    0  980M  0 lvm  [SWAP]  
sdb                    8:16   0  2.5G  0 disk  
sdc                    8:32   0  2.5G  0 disk

***4. Используя fdisk, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.***  
Выполнил:  
Disk /dev/sdb: 2.51 GiB, 2684354560 bytes, 5242880 sectors  
Disk model: VBOX HARDDISK  
Units: sectors of 1 * 512 = 512 bytes  
Sector size (logical/physical): 512 bytes / 512 bytes  
I/O size (minimum/optimal): 512 bytes / 512 bytes  
Disklabel type: dos  
Disk identifier: 0x08745461

Device     Boot   Start     End Sectors  Size Id Type  
/dev/sdb1          2048 4196351 4194304    2G 83 Linux  
/dev/sdb2       4196352 5242879 1046528  511M 83 Linux

***5. Используя sfdisk, перенесите данную таблицу разделов на второй диск.***  
Выполнил:  
$ sudo sfdisk -d /dev/sdb > part_table  
$ sudo sfdisk /dev/sdc < part_table  
$ lsblk  
NAME                 MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT  
…  
sdb                    8:16   0  2.5G  0 disk  
├─sdb1                 8:17   0    2G  0 part  
└─sdb2                 8:18   0  511M  0 part  
sdc                    8:32   0  2.5G  0 disk  
├─sdc1                 8:33   0    2G  0 part  
└─sdc2                 8:34   0  511M  0 part

***6. Соберите mdadm RAID1 на паре разделов 2 Гб.***  
Выполнил:  
$ sudo mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1  
$ lsblk  
NAME                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT  
…  
sdb                    8:16   0  2.5G  0 disk  
├─sdb1                 8:17   0    2G  0 part  
│ └─md0                9:0    0    2G  0 raid1  
└─sdb2                 8:18   0  511M  0 part  
sdc                    8:32   0  2.5G  0 disk  
├─sdc1                 8:33   0    2G  0 part  
│ └─md0                9:0    0    2G  0 raid1  
└─sdc2                 8:34   0  511M  0 part

***7. Соберите mdadm RAID0 на второй паре маленьких разделов.***  
$ sudo mdadm --create --verbose /dev/md1 --level=0 --raid-devices=2 /dev/sdb2 /dev/sdc2  
$ lsblk  
NAME                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT  
…  
sdb                    8:16   0  2.5G  0 disk  
├─sdb1                 8:17   0    2G  0 part  
│ └─md0                9:0    0    2G  0 raid1  
└─sdb2                 8:18   0  511M  0 part  
  └─md1                9:1    0 1018M  0 raid0  
sdc                    8:32   0  2.5G  0 disk  
├─sdc1                 8:33   0    2G  0 part  
│ └─md0                9:0    0    2G  0 raid1  
└─sdc2                 8:34   0  511M  0 part  
  └─md1                9:1    0 1018M  0 raid0

***8. Создайте 2 независимых PV на получившихся md-устройствах.***  
$ sudo pvcreate /dev/md0  
$ sudo pvcreate /dev/md1  
$ sudo pvs  
     PV            VG           Fmt  Attr PSize    PFree  
  /dev/md0                     lvm2 ---    <2.00g   <2.00g  
  /dev/md1                     lvm2 ---  1018.00m 1018.00m  
  /dev/sda5  vgvagrant lvm2 a--   <63.50g       0

***9. Создайте общую volume-group на этих двух PV.***  
$ sudo vgcreate vg35 /dev/md0 /dev/md1  
$ sudo pvs   						# для более подробной можно vgdisplay  
     PV         VG         Fmt  Attr PSize    PFree
  /dev/md0   vg35         lvm2 a--    <2.00g   <2.00g  
  /dev/md1   vg35         lvm2 a--  1016.00m 1016.00m  
  /dev/sda5  vgvagrant lvm2 a--   <63.50g       0

***10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.***  
$ sudo lvcreate -L100 -n lv1 vg35 /dev/md1  
$ lsblk  
…  
sdb                    8:16   0  2.5G  0 disk  
├─sdb1                 8:17   0    2G  0 part  
│ └─md0                9:0    0    2G  0 raid1  
└─sdb2                 8:18   0  511M  0 part  
  └─md1                9:1    0 1018M  0 raid0  
    └─vg35-lv1       253:2    0  100M  0 lvm  
sdc                    8:32   0  2.5G  0 disk  
├─sdc1                 8:33   0    2G  0 part  
│ └─md0                9:0    0    2G  0 raid1  
└─sdc2                 8:34   0  511M  0 part  
  └─md1                9:1    0 1018M  0 raid0  
    └─vg35-lv1       253:2    0  100M  0 lvm

***11. Создайте mkfs.ext4 ФС на получившемся LV.***  
$ sudo mkfs.ext4 /dev/vg35/lv1  
Creating filesystem with 25600 4k blocks and 25600 inodes  
Allocating group tables: done  
Writing inode tables: done  
Creating journal (1024 blocks): done  
Writing superblocks and filesystem accounting information: done

***12. Смонтируйте этот раздел в любую директорию, например, /tmp/new.***  
$ mkdir /tmp/new  
$ sudo mount /dev/vg35/lv1 /tmp/new  
$ df –h  
…  
/dev/mapper/vg35-lv1         93M   72K   86M   1% /tmp/new

***13. Поместите туда тестовый файл, например wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz***  
$ cd /tmp/new  
/tmp/new$ ls  
lost+found  test.gz

***14. Прикрепите вывод lsblk***  
$ lsblk  
NAME                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT  
sda                    8:0    0   64G  0 disk  
├─sda1                 8:1    0  512M  0 part  /boot/efi  
├─sda2                 8:2    0    1K  0 part  
└─sda5                 8:5    0 63.5G  0 part  
  ├─vgvagrant-root   253:0    0 62.6G  0 lvm   /  
  └─vgvagrant-swap_1 253:1    0  980M  0 lvm   [SWAP]  
sdb                    8:16   0  2.5G  0 disk  
├─sdb1                 8:17   0    2G  0 part  
│ └─md0                9:0    0    2G  0 raid1  
└─sdb2                 8:18   0  511M  0 part  
  └─md1                9:1    0 1018M  0 raid0  
    └─vg35-lv1       253:2    0  100M  0 lvm   /tmp/new  
sdc                    8:32   0  2.5G  0 disk  
├─sdc1                 8:33   0    2G  0 part  
│ └─md0                9:0    0    2G  0 raid1  
└─sdc2                 8:34   0  511M  0 part  
  └─md1                9:1    0 1018M  0 raid0  
    └─vg35-lv1       253:2    0  100M  0 lvm   /tmp/new

***15. Протестируйте целостность файла:***  
$ gzip -t /tmp/new/test.gz  
$ echo $?  
0

***16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1***  
$ sudo pvmove /dev/md1 /dev/md0  
  /dev/md1: Moved: 4.00%  
  /dev/md1: Moved: 100.00%

***17. Сделайте --fail на устройство в вашем RAID1 md***  
$ cat /proc/mdstat  
    …  
    md0 : active raid1 sdc1[1] sdb1[0]  
    2094080 blocks super 1.2 [2/2] [UU]  
$ sudo mdadm --fail /dev/md0 /dev/sdc1  
  mdadm: set /dev/sdc1 faulty in /dev/md0

***18. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.***  
$ dmesg  
[17440.599972] md/raid1:md0: Disk failure on sdc1, disabling device.  
               md/raid1:md0: Operation continuing on 1 devices.  

***19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:***  
$ gzip -t /tmp/new/test.gz  
$ echo $?  
0

***20. Погасите тестовый хост, vagrant destroy***  
Выполнил.
