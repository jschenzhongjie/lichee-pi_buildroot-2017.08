# lichee-pi_buildroot-2017.08


首先安装一些依赖，比如linux头文件：

apt-get install linux-headers-$(uname -r)

然后下载安装：

wget https://buildroot.org/downloads/buildroot-2017.08.tar.gz
tar xvf buildroot-2017.08.tar.gz
cd buildroot-2017.08/
make menuconfig
配置
首先配置工具链，因为之前开发uboot和内核都用到了自己下载的工具链，所以这里也配置成外部工具链。

在本机上外部工具链配置为： /opt/gcc-linaro-6.3.1-2017.05-x86_64_arm-linux-gnueabihf/

工具链前缀是： arm-linux-gnueabihf

外部工具链gcc版本：我们使用的是最新的6.3版本

外部工具链内核头文件：是在 arm-linux-gnueabi/libc/usr/include/linux/version.h 里读取内核版本信息。本机的版本是4.6

C库还是选择传统的glibc。需要小体积可以选uclibc（需要自行编译安装）。

再在system 设置下主机名，root密码等。

最后就是配置自己需要的软件包，在menuconfig中选中即可。

有时候下载速度慢，可以复制下载链接，使用迅雷等下载好后，拷贝到dl目录下，会自动识别。

编译
make

>>> 有时候构建会出现莫名其妙的错误，make clean下会ok？
编译完成后，会生成 output/images/rootfs.tar，此即所需的根文件系统

默认失能串口登录，需要修改 /etc/inittab :

ttyS0::respawn:/sbin/getty -L ttyS0 115200 vt100 # GENERIC_SERIAL




make jffs2 rootfs 
总空间是16M-1M-64K-4M=0xAF0000

cd output/
chmod a-s target/bin/busybox
#mkfs.jffs2 -s 0x100 -e 0x10000 -p 0x1AF0000 -d rootfs/ -o jffs2.img
sudo mkfs.jffs2 -s 0x100 -e 0x10000 -p 0xAF0000 -d target/ -o ./images/jffs2.img

页大小0x100 256字节

块大小0x10000 64k

jffs2分区总空间0xAF0000

jffs2.img是生成的文件系统镜像。


