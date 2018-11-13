Linux安装mailx配置SMTP发送邮件


在Ubuntu 16.04 LTS上安装Postfix邮件系统，配置发送邮件服务器。
介绍

我有一台Ubuntu服务器，上面运行了若干应用。我希望某个应用崩溃或出问题的时候，系统后台自动把故障信息发到我的邮箱。这样，免去了我远程登录服务器，查看运行故障日志的烦恼。此处仅需要服务器能发送邮件即可，不需收取邮件。

Postfix是Linux平台上应用广泛的邮件系统。本文中将在Ubuntu平台上安装配置Postfix，实现发送邮件功能。
步骤一：安装Postfix

首先更新软件包：

sudo apt-get update

然后安装Mailutils(Postfix在该包中):

sudo apt-get install mailutils

安装中首先会出现问题请你确认，选择Y

Do you want to continue?[Y/n]

在确认配置页面，tab至ok，回车：

以下配置页面共5个选项，我们选择默认选项Internet Site：

下面的配置页面是输入系统邮件名字，保持默认：

安装完毕。
步骤二：配置Postfix

Postfix需要在配置中监听loopbackinterface，打开主Postfix配置文件：

sudo vi /etc/postfix/main.cf

将inet_interfaces = all更为inet_interfaces = loopback-only，保存退出。

重启Postfix：

sudo service postfix restart

步骤三：测试发送邮件

echo "测试邮件正文" | mail -s "邮件标题" your_email_address

用你的邮箱地址代替your_email_address。

怎么样，能收到吗？如果你的服务器只有Ip地址，没有对应的域名，很可能你也像我一样收不到邮件。因为此处发出的邮件默认发件地址为：你登录服务器的用户名@你服务器的名字，像我的地址就为user1@svr5。而绝大部分收件人的邮件服务器会对发件电子邮件地址有效性做出判断，如果不与常规域名后缀（如xxx.com, xxx.net等等）一致，会判定为垃圾邮件而丢弃。所以，我们需要进入步骤四。
步骤四：改变发件地址

打开Postfix配置文件：

sudo vi /etc/postfix/main.cf

在文件末尾增加以下内容：

smtp_generic_maps = hash:/etc/postfix/generic

保存退出。打开以下文件：

sudo vi /etc/postfix/generic

添加以下内容：

你的用户名@你的服务器名字   你希望对方看到的电子邮件地址

如我在服务器登录用户名为user1，我的服务器名字为svr5，我希望对方看到的电子邮件地址为user@126.com，那么添加内容为：

user1@svr5   user@126.com

如果你希望对方回复，user@126.com应该是你希望收到回复邮件的地址。如果不希望对方回复，那么这个地址可以不一定为真实地址，但格式要符合邮件地址要求，否则会被对方邮件服务器丢弃。

重启Postfix：

sudo service postfix restart

再试试，能收到邮件了吗？

echo "测试邮件正文" | mail -s "邮件标题" your_email_address

OK.

此文参考以下两篇文章：

    如何在Ubuntu 16.04上安装并配置Postfix作为只发送SMTP服务器
    Postfix masquerading or changing outgoing SMTP email or mail address

作者：fanzhh
链接：https://www.jianshu.com/p/78e2e1914ec1
來源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。
