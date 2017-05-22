# dml0

## mdadm

sudo apt install mdadm
cat /proc/mdstat
sudo umount /dev/md0
      sudo mdadm --stop /dev/md0
sudo mdadm --remove /dev/md0
lsblk -o NAME,SIZE,FSTYPE,TYPE,MOUNTPOINT
   NAME                 SIZE FSTYPE      TYPE MOUNTPOINT
   sda                465.8G             disk
   sdb                931.5G             disk
   sdc                931.5G             disk
   sdd                119.2G             disk
   sde                119.2G             disk
   sdf                119.2G             disk
   sdg                119.2G             disk
   sdh                465.8G             disk
   sdi                465.8G             disk
   sdj                465.8G             disk
   sdk                 57.9G             disk
   ├─sdk1               487M ext2        part /boot
   ├─sdk2                 1K             part
   └─sdk5              57.4G LVM2_member part
     ├─dml--vg-root    25.5G ext4        lvm  /
     └─dml--vg-swap_1  31.9G swap        lvm  [SWAP]

sudo mdadm --zero-superblock /dev/sda-j
sudo nano /etc/mdadm/mdadm.conf
   ARRAY /dev/md0 metadata=1.2 name=mdadmwrite:0 UUID=7261fb9c:976d0d97:30bc63ce:85e76e91
sudo update-initramfs -u

sudo mdadm --create --verbose /dev/md0 --level=0 --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
sudo mkfs.ext4 -F /dev/md0
sudo mkdir -p /mnt/md0
sudo mount /dev/md0 /mnt/md0
echo '/dev/md0 /mnt/md0 ext4 defaults,nofail,discard 0 0' | sudo tee -a /etc/fstab

sudo mdadm --create --verbose /dev/md1 --level=1 --raid-devices=2 /dev/sdc /dev/sdd
sudo mkfs.ext4 -F /dev/md1
sudo mkdir -p /mnt/md1
sudo mount /dev/md1 /mnt/md1
echo '/dev/md1 /mnt/md1 ext4 defaults,nofail,discard 0 0' | sudo tee -a /etc/fstab

sudo mdadm --create --verbose /dev/md5 --level=5 --raid-devices=4 /dev/sdg /dev/sdi /dev/sdj /dev/sdk
sudo mkfs.ext4 -F /dev/md5
sudo mkdir -p /mnt/md5
sudo mount /dev/md5 /mnt/md5
echo '/dev/md5 /mnt/md5 ext4 defaults,nofail,discard 0 0' | sudo tee -a /etc/fstab

sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
   ARRAY /dev/md1 metadata=1.2 name=dml:1 UUID=819cb825:1530074a:fd95a69a:27c873e2
   ARRAY /dev/md0 metadata=1.2 name=dml:0 UUID=195e2b06:6344cab6:42735de0:3b8b3dbc
   ARRAY /dev/md5 metadata=1.2 spares=1 name=dml:5 UUID=eb6397b7:820557b5:8a3c165c:b63e2c9d
sudo update-initramfs -u

df -h -x devtmpfs -x tmpfs