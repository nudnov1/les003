Example to LVM practice:

# Step * Уменьшить том под / до 8G
# Step * Выделить том под /home
# Step * Выделить том под /var - сделать в mirror
# Step * /home - сделать том для снапшотов
# Step * Прописать монтирование в fstab. 

#	Работа со снапшотами:
		- сгенерить файлы в /home/
		- снять снапшот
		- удалить часть файлов
		- восстановится со снапшота




# Step 1 - Create new Volume Groupe.
	pvcreate /dev/sdb
	vgcreate otus /dev/sdb
	lvcreate -l+80%FREE -n test otus
# Step 2 - Длā начала разметим диск длā будущего исполþзованиā LVM

# Step 3 - LVM Resizing
			Расширение LVM
			Уменьшение LV
# Step 4 - LVM Snapshot

# Step 5 - LVM Mirroring