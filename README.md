# ToyVpn

fork code from google



我这里是用VM装的Ubuntu，

测试设备、PC和VM里的Ubuntu均可正常上网冲浪

我这里的设备机的ip是192.168.1.224

# 服务端准备

1、首先搭建一个linux系的系统，确保能够ping通 有自己的ip地址

![](https://raw.githubusercontent.com/SymphonyZhang/ImageRepository/main/img_server_ip.png)

linux系的系统端 ：serverip

![](https://raw.githubusercontent.com/SymphonyZhang/ImageRepository/main/ping_server_ip.png)

其他设备：ping ip

2、在linux系的系统里安装编译工具 `g++`、 `gcc`这些



3、把 Server文件夹里的`Makefile` 和 `ToyVpnServer.cpp`丢到linux系的系统中（我这里建了一个`gvpn`文件夹，名字自取）,这里使用了root用户，如果不用root用户，可以自己给文件开权限，情况自己斟酌

![](https://raw.githubusercontent.com/SymphonyZhang/ImageRepository/main/server_files.png)



4、在`gvpn`路径下运行`make`命令

![](https://raw.githubusercontent.com/SymphonyZhang/ImageRepository/main/make.png)



到了这里，前期准备工作完成！



下面开始配置`tun`

以下操作全部都是在gvpn这个路径下完成的，按顺序贴上以下命令并按回车，你也可以在`ToyVpnServer.cpp`这个文件中找到步骤说明



- `echo 1 > /proc/sys/net/ipv4/ip_forward`

- `iptables -t nat -A POSTROUTING -s 10.0.0.0/8 -o eth0 -j MASQUERADE`

- `ip tuntap add dev tun0 mode tun`

- `ifconfig tun0 10.0.0.1 dstaddr 10.0.0.2 up`

- `./ToyVpnServer tun0 8000 test -m 1400 -a 10.0.0.2 32 -d 8.8.8.8 -r 0.0.0.0 0`

至此 ，VPN服务端已经启动完成！



# 客户端准备

把项目clone到本地，导入as中，最好版本高点的，这里我使用的是4.2.2版本的

然后找台设备把app运行起来，这里要保证这台设备是可以ping 通 服务端的

![](https://raw.githubusercontent.com/SymphonyZhang/ImageRepository/main/device_ping.png)



这时候，你可以先用浏览器进行正常的上网冲浪测试，结果应该是正常可用的。

然后打开App

在第一项中填入服务器的IP，我这里是 192.168.1.42，第二项填入的是8000，第三项填入的是test，然后点击connect，这时弹框上选择的是 **确定** ，提示要设置pin码的也跟着设置。

这时有toast弹框提示 连接成功了 connected！ 可看到状态栏左边有个云朵图标，右边有个钥匙图标。



这里的app里有一个空闲机制，如果过阵子没用，就会自动断掉，app自行崩溃，但是一直操作有数据收发的话，还是能一直操作连接，不会断的。



在这时，如果打开浏览器，同样上网冲浪，应该就回访问不了了，因为数据都通过VPN传到服务端了，但是我们的服务端没有做数据的转发操作的。所以算是正常现象。



这里可以用`Wireshark` 监控以下流量走向

![](https://raw.githubusercontent.com/SymphonyZhang/ImageRepository/main/result.png)
