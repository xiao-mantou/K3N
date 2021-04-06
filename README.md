###### [中文](https://github.com/xiao-mantou/K3N/blob/main/README-zh.md)
# K3N
K3 D1
## Telnet
Original Version: V23.1.8.89
For China: [cn.dat](https://tbvv.net/k3/cn.dat), for worldwide: [us.dat](https://tbvv.net/k3/us.dat) / [de.dat](https://tbvv.net/k3/de.dat)
192.168.2.1>Advanced>Restore>Configuration File>Browse>xx.dat>Restore Configuration, One restore one season, reboot router will lost telnet
[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)>192.168.2.1>Telnet>Open>Entry`Command`

[Source](https://tbvv.net/posts/0101-k3.html#%E5%88%B7%E5%85%A5root%E5%9B%BA%E4%BB%B6)
## Backup
mount
ls
cd /xxx
cat /dev/mtd0 > mtd0.bin

mtd0:cfe
mtd1:firemware generated settings
mtd2
mtd3
mtd4
mtd5
mtd6:firmware
mtd7:included in mtd6
mtd8
## cfe
Flash command has been blocked since v260 
Downgrade to 246 or lower
[HxD](https://mh-nexus.de/en/downloads.php?product=HxD20)
Copy `0x400` ~ `0xF9F`(**meet plenty of `00` before `0x13FC`**) from origianl mtd0, replace the same offset in target cfe
Verify your work using  [broadcom_cfe_tool](https://github.com/corberan/broadcom_cfe_tool) 
192.168.2.x 225.225.225.0
[Tftpd](https://tftpd32.jounin.net/tftpd32_download.html)
`cd tmp`
`tftp -g -r xxx.bin 192.168.2.x`
`cat xxx.bin > /dev/mtdblock0 && reboot`[Source](https://www.right.com.cn/forum/thread-259360-1-1.html)
Wait for reboot finishing!

# Quantum DAX firmware mod for K3
Linux:
`sudo apt install binwalk`
`sudo apt-get update`
`binwalk xxx.bin`

- CAN be flashed through cfe:

DECIMAL | HEXADECIMAL | DESCRIPTION
------- | ----------- | -----------
0 | 0x0 | TRX firmware header, xxx
28 | 0x1C | LZMA compressed data, xxx
2220612 | 0x21E244 | Squashfs filesystem, xxx

- CAN NOT be flashed through cfe, has some data before TRX,LZMA&Squashfs:

DECIMAL | HEXADECIMAL | DESCRIPTION
------- | ----------- | -----------
114692 | 0x1C004 | LZMA compressed data, xxx
525312 | 0x80400 | TRX firmware header, xxx
525340 | 0x8041C | LZMA compressed data, xxx
2775712 | 0x2A5AA0 | Squashfs filesystem, xxx, size: 32877312 bytes, xxx

Get the part(same as mtd6) that can be flashed through cfe:
`dd if=Original.bin of=New.bin bs=1 skip=525312 count=35127712`
**525312 :** `TRX firmware header start`
**35127712 :** `Squashfsfilesystem start` + `Squashfs filesystem size` - `TRX firmware header start` eg.(2775712+32877312-525312)
# cfe flash
`192.168.2.100` 225.225.225.0
`ping 192.168.2.1` hold Reset for 10s, wait for ttl=100
[Tftpd](https://tftpd32.jounin.net/tftpd32_download.html)
`192.168.2.1/do.htm?cmd=flash+-noheader+192.168.2.x:/xxx.bin+nflash0.trx`
[Source](https://tbvv.net/posts/0101-k3.html#%E5%88%B7%E5%85%A5%E6%96%B9%E6%B3%952-CFE%E4%B8%8B%E5%88%B7%E5%85%A5-%EF%BC%9A)
# Other
Better flash cfe in firmware, Flash firmware in cfe
# settings optimize
~~2.4G 802.11n
5G 802.11ac/n 80Mhz~~
# TO Do
command
ttl
###### Tag
PHICOMM K3 AC3150 Dual Band Wi-Fi Gigabit Smart Router
Quantum DAX/WL-WN538A8 AC3200 (This guide is no target to this product)