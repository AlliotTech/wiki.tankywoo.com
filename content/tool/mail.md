---
title: "Email相关"
date: 2013-08-17 07:36
---

Msmtp + Mutt 搭建邮件客户端

首先得了解MUA，MTA的概念 (可参考鸟哥私房菜服务器篇)

* MUA(Mail User Agent): 主要用于接收、阅读、撰写邮件. 如thunderbird, 以为web的gmail, 或者简单的基于文本的mutt, mail命令(`mailx`)等.
* MTA(Mail Transfer Agent): 基于SMTP协议, 主要用于邮件的转发，如Postfix，Exim4, Sendmail, msmtp
* MDA(Mail Delivery Agent): 在邮件经过N个MTA达到目标的邮件服务器(local MTA)时, MDA将邮件放到相应的目录下.
* Mailbox: 信箱, linux下一般存在/var/spool/mail下

![mail process](http://tankywoo-wb.b0.upaiyun.com/mail-1.jpg)

(图片引用自[How a mail server works](http://xmodulo.com/how-mail-server-works.html))

更详细的基本概念:

* [How a mail server works](http://xmodulo.com/how-mail-server-works.html)
* [MTA, MDA what r they actually?](http://www.linuxquestions.org/questions/linux-software-2/mta-mda-what-r-they-actually-208129/)
* [Mail Server Components – MTA , MDA & MUA](http://www.nextstep4it.com/mail-server-components-mta-mda-mua/)
* [How email works (MTA, MDA, MUA)](http://ccm.net/contents/116-how-email-works-mta-mda-mua)

<!-- -->

* POP3(Post Office Protocol 3): 用于邮件访问. 标准端口995(SSL)和110(非SSL)
* IMAP(Internet Mail Access Protocol): 交互式邮件存取协议. 用于邮件访问. 标准端口993(SSL)和143(非SSL)
* SMTP(Simple Mail Transfer Protocol): 简单邮件传输协议. 控制邮件的中转. 标准端口465/994(SSL)和25(非SSL)

POP3和IMAP的区别是前者本地的操作如删除不会同步到服务器. IMAP则是同步的.

## msmtp ##

负责smtp的功能

[主页](http://msmtp.sourceforge.net)

> msmtp is an SMTP client. In the default mode, it transmits a mail to an SMTP server which takes care of further delivery. To use this program with your mail user agent (MUA), create a configuration file with your mail account(s), and tell your MUA to call msmtp instead of /usr/sbin/sendmail.

在用户home目录新建一个.msmtprc配置文件

	account default
	host smtp.163.com	#邮件服务器，我用的是163的
	from xxx@163.com
	auth login
	user xxx@163.com	#账号
	password *******	#密码
	logfile /var/log/msmtp.log

因为这里涉及到了密码，所以建议把配置文件的权限改为600

测试:

	$ msmtp xxx@xx.com(收件人邮箱)

然后随便输入一些字符，按Ctrl+D退出，查看收件人邮箱是否有邮件


## mutt ##

[主页](http://www.mutt.org)

> Mutt is a small but very powerful text-based mail client for Unix operating systems.

在用户home目录新建一个.muttrc配置文件

	set sendmail="/usr/bin/msmtp"	#这里设置调用的MTA
	set use_from=yes
	set realname="XXX"				#发件人名称
	set editor="vim"				#调用的编辑器

测试:

	$ echo 'Hello World' | mutt -s 'Hi, Just a test!' xxx@xx.com

`w` 可以更改邮件的状态, 有:

* N: 新邮件
* O: 老邮件, 但未读
* r: 已读
* D: 将删除

## 参考资料 ##

* [Gentoo文档:Mutt电子邮件快速入门指南](http://www.gentoo.org/doc/zh_cn/guide-to-mutt.xml?style=printable)
* [msmtp官方文档:Using msmtp with Mutt](http://msmtp.sourceforge.net/doc/mutt+msmtp.txt)
* [Arch - Mutt](https://wiki.archlinux.org/index.php/Mutt)
* [使用mutt作为email客户端](http://www.jianshu.com/p/bebbf2db2cd8)
* [Mutt cheat sheet](http://sheet.shiar.nl/mutt)