# K3N
K3 D1
## Telnet
预装固件: V23.1.8.89
[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
[开启Telnet](https://tbvv.net/posts/0101-k3.html#%E5%88%B7%E5%85%A5root%E5%9B%BA%E4%BB%B6)
## 备份
`mount`查看U盘挂载
`ls`查看目录文件
`cd /xxx`更改当前目录至U盘
`cat /dev/mtd0 > mtd0.bin` 复制到当前目录

mtd0:cfe
mtd1:固件生成的设置
mtd2
mtd3
mtd4
mtd5
mtd6:固件
mtd7:包含于mtd6
mtd8
## cfe
刷入命令自 v260 被锁定
降级至 246 或更低
[HxD](https://mh-nexus.de/en/downloads.php?product=HxD20)
复制备份的mtd0的 `0x400` ~ `0xF9F`(**直到遇见`0x13FC`之前的大片`00`**)，替换至目标cfe的相同偏移
使用[broadcom_cfe_tool](https://github.com/corberan/broadcom_cfe_tool) 验证修改
192.168.2.x 225.225.225.0
[Tftpd](https://tftpd32.jounin.net/tftpd32_download.html)
`cd tmp`
`tftp -g -r xxx.bin 192.168.2.x`
`cat xxx.bin > /dev/mtdblock0 && reboot`[来源](https://www.right.com.cn/forum/thread-259360-1-1.html)
等待至重启完成!

# Wavlink固件修改
Linux:
`sudo apt install binwalk`安装binwalk
`sudo apt-get update`更新
`binwalk xxx.bin`分析固件

- 可以使用cfe刷入的固件:

DECIMAL | HEXADECIMAL | DESCRIPTION
------- | ----------- | -----------
0 | 0x0 | TRX firmware header, xxx
28 | 0x1C | LZMA compressed data, xxx
2220612 | 0x21E244 | Squashfs filesystem, xxx

- 不可以使用cfe刷入的固件，在TRX,LZMA和Squashfs之前有数据:

DECIMAL | HEXADECIMAL | DESCRIPTION
------- | ----------- | -----------
114692 | 0x1C004 | LZMA compressed data, xxx
525312 | 0x80400 | TRX firmware header, xxx
525340 | 0x8041C | LZMA compressed data, xxx
2775712 | 0x2A5AA0 | Squashfs filesystem, xxx, size: 32877312 bytes, xxx

`dd if=Original.bin of=New.bin bs=1 skip=525312 count=35127712`复制出想要的部分
**525312 :** `TRX firmware header 开始值`
**35127712 :** `Squashfsfilesystem 开始值` + `Squashfs filesystem 大小` - `TRX firmware header 开始值` 例如上面(2775712+32877312-525312)
# cfe刷入
`192.168.2.100` 225.225.225.0
`ping 192.168.2.1` 按住重置10秒, 等待ttl=100
[Tftpd](https://tftpd32.jounin.net/tftpd32_download.html)
`192.168.2.1/do.htm?cmd=flash+-noheader+192.168.2.x:/xxx.bin+nflash0.trx`
[来源](https://tbvv.net/posts/0101-k3.html#%E5%88%B7%E5%85%A5%E6%96%B9%E6%B3%952-CFE%E4%B8%8B%E5%88%B7%E5%85%A5-%EF%BC%9A)
# 其他
尽可能在固件中刷cfe，最好在cfe中刷固件
# settings optimize
~~2.4G 802.11n 40Mhz
5G 802.11ac/n 80Mhz~~
# TO Do
command
ttl
