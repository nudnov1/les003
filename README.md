#    Example to LVM practice:

#  * Уменьшить том под / до 8G
#  * Выделить том под /home
#  * Выделить том под /var - сделать в mirror
#  * /home - сделать том для снапшотов
#  * Прописать монтирование в fstab. 

#	Работа со снапшотами:
	- сгенерить файлы в /home/
	- снять снапшот
	- удалить часть файлов
	- восстановится со снапшота



## Подготовим временный том для / раздела:
	- pvcreate /dev/sdb
	- vgcreate vg_root /dev/sdb
    - lvcreate -n lv_root -l +100%FREE /dev/vg_root


## Создадим на нем файловую систему и смонтируем его, чтобы перенести туда данные:
	- mkfs.xfs /dev/vg_root/lv_root
	- mount /dev/vg_root/lv_root /mnt

## Создадим на нем файловую систему и смонтируем его, чтобы перенести туда данные:
    - xfsdump -J - /dev/VolGroup00/LogVol00 | xfsrestore -J - /mnt
## Затем переконфигурируем grub для того, чтобы при старте перейти в новый /
    - for i in /proc/ /sys/ /dev/ /run/ /boot/; do mount --bind $i /mnt/$i; done
    - chroot /mnt/
    - grub2-mkconfig -o /boot/grub2/grub.cfg

## Обновим образ initrd.

   cd /boot ; for i in `ls initramfs-*img`; do dracut -v $i `echo $i|sed "s/initramfs-//g; 
   s/.img//g"` --force; done
* Перезагружаемся

## Теперь нам нужно изменить размер старой VG и вернуть на него рут. Для этого удаляем старый LV размеров в 40G и создаем новый на 8G:
	- lvremove /dev/VolGroup00/LogVol00
	- lvcreate -n VolGroup00/LogVol00 -L 8G /dev/VolGroup00
	- mkfs.xfs /dev/VolGroup00/LogVol00
	- mount /dev/VolGroup00/LogVol00 /mnt
	- mount /dev/VolGroup00/LogVol00 /mnt


## Так же как в первый раз переконфигурируем grub, за исключением правки /etc/grub2/grub.cfg
	- for i in /proc/ /sys/ /dev/ /run/ /boot/; do mount --bind $i /mnt/$i; done
    - chroot /mnt/
	- grub2-mkconfig -o /boot/grub2/grub.cfg
	- cd /boot ; for i in `ls initramfs-*img`; do dracut -v $i `echo $i|sed "s/initramfs-//g; s/.img//g"` --force; done


## Пока не перезагружаемся и не выходим из под chroot - мы можем заодно перенести /var
* На свободных дисках создаем зеркало:

	- pvcreate /dev/sdc /dev/sdd
	- vgcreate vg_var /dev/sdc /dev/sdd
	- lvcreate -L 950M -m1 -n lv_var vg_var
	
* Создаем на нем ФС и перемещаем туда /var:


## Step 5 - 