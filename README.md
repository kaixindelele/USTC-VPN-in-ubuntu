# USTC-VPN-in-ubuntu
中科大(ustc)-openvpn-Ubuntu环境配置教程(丫的，我发到github上，不会被删了吧？不会吧？惊恐~)

中科大(ustc)-openvpn-Ubuntu环境配置教程(删除后重置版)

@[toc]
## 参考链接：
 http://openvpn.ustc.edu.cn/
 
## Windows如何使用？

Windows太简单了，直接看官网的教程就OK了~。

## 关于申请账号
如何申请账号只要是科大学籍的学生，尤其是联培的学生，应该都可以申请账号

工作日发邮件参考内容如下：

-----原始邮件-----

    发件人:(你自己的科大邮箱账号)@mail.ustc.edu.cn
    发送时间:2018-06-26 16:14:50 (星期二)
    收件人: netservice@ustc.edu.cn
    抄送: 
    主题: 申请开通vpn账号
    
    老师您好，由于我需要去
    XX，因此申请开通vpn账号。
    我的学院是:XXX
    姓名:XXX
    学号: XXX

## 配置步骤：
打开终端，安装的OpenVPN的包：

    sudo apt-get install openvpn

下载配置文件：
openvpn.zip,下载后解压缩，假定解压缩文件夹为
~/Downloads/openvpn需要把这里的文件复制到openvpn的安装包里： 
这里如果我没记错的话，应该不能手动复制粘贴~

    sudo mv ~/Downloads/openvpn/etc/openvpn

修改配置文件： 很明显，根据官方教程里的解释，我们不需要修改移动和联通的，只需要修改dianxin就好的了修改好的文件如下： 

    (用的时候将中文注释删掉即可，也不知道有中文注释是否会报错。)
    client
    #下面的三段是为了解决DNS解析的问题，如果不加，我这边上不了谷歌
    #这下面三句好像和国内网络有些冲突，反正随缘啦~
    client
    script-security 3
    up /etc/openvpn/update-resolv-conf
    down /etc/openvpn/update-resolv-conf
    
    dev tap
    proto udp
    remote 218.22.21.12 1194
    resolv-retry infinite
    nobind
    mute-replay-warnings
    redirect-gateway
    #下面是需要注释掉的
    #user nobody
    #group nobody
    
    persist-key
    persist-tun
    #下面是需要修改的，基本上和我一样就好了。作用是配置好ca.crt文件的路径
    ca /etc/openvpn/ca.crt
    
    comp-lzo
    #下面好像改了也无法自动回归命令行，这个还没有找到解决方案
    verb 4
    #下面也是需要修改的，如果用下载的源代码，则需要手动输入账号和密码
    #但是如果新建一个文件，用vim 新建
    #sudo vim account
    #第一行:账号名称
    #第二行:密码
    #这里真的要自己新建一个文件！别直接删了
    
    auth-user-pass account
    
    reneg-sec 360000x

    ## - - -dianxin.ovpn over- - -

## 使用vpn
打开终端，打开vpn：，这里的路径一定要切换到/etc/openvpn

    cd /etc/openvpn

这是属于第一种手动配置，效果也是最稳定的一种，虽然会占用一个终端~

    sudo openvpn --config ustc-dianxin.ovpn

**最后出现 Initialization Sequence Completed 的信息就表示已经连上了**。这时ifconfig tap0和route都会显示连到了211.86网段。 
当然能连上谷歌应该才算是真正的成功了～

解决后台运行的方案（自动回到命令行）：
方案一：打开vpn的命令换成下面的就好了： 
这里的路径，也有问题，也好像需要先登录手动的，然后设置这个，反正我就刚开始成功过，后来就很尴尬了~

    openvpn /etc/openvpn/ustc-dianxin.ovpn > /dev/null &

试了一下，成功的方法是这样的，上面手动的打开后，不关闭原来的终端，然后Ctrl + shift + t 新建一个 
终端，这里的目录，应该还会/etc/openvpn. 
然后用上面这神奇的命令，为了保险起见，咱们弄成这个：

    sudo openvpn ustc-dianxin.ovpn > /dev/null &

然后返回值是一个数值，这里你再看看能不能打开谷歌～ 
打开后，试试关闭两个窗口，看看还能不能打开谷歌～ 
如果都行的话，就OK了～

## 开机自启动的问题（已解决，好像又不行了）：
方案二： 新打开一个终端，执行命令：

    sudo vim /etc/rc.local

然后加入如下内容:

    sudo openvpn --config /etc/openvpn/ustc-dianxin.ovpn
    sudo openvpn/etc/openvpn/ustc-dianxin.ovpn > /dev/null/ &12

这样开机就会自启动程序，并且不用占用一个终端。 
反正我第一次是成功了～goodluck～ 
emmmm，后来不知道怎么回事儿，就不行了，只有上面第一个手动的，占用一个终端的才可以，难受～ 



