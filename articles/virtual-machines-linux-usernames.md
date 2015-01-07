 <properties urlDisplayName="User Names in Linux" pageTitle="Auswählen von Benutzernamen für Linux in Azure" metaKeywords="" description="Learn how to select user names for a Linux virtual machine in Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Selecting User Names for Linux on Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/18/2014" ms.author="szark" />



#Auswählen von Benutzernamen für Linux in Azure#

Wenn Sie einen virtuellen Linux-Computer erstellen, können Sie einen Namen als Benutzername auswählen oder den standardmäßigen akzeptieren, *azureuser*. In den meisten Fällen ist dieser neue Benutzer im Basisimage nicht vorhanden und wird während des Bereitstellungsprozesses erstellt. Wenn der Benutzer im VM-Basisimage vorhanden ist, konfiguriert der Azure-Linux-Agent in einigen Fällen einfach das Kennwort (und/oder den SSH-Schlüssel) für diesen Benutzer auf der Grundlage der Informationen die Sie beim Erstellen der VM festgelegt haben.

**Allerdings** definiert Linux einen Satz von Benutzernamen, die nicht verwendet werden sollten. Der Bereitstellungsprozess **schlägt fehl**, wenn Sie versuchen, einen virtuellen Linux-Computer mithilfe eines vorhandenen Systembenutzers bereitzustellen, der als Benutzer mit einer UID 0-99 definiert ist. Ein typisches Beispiel ist der `root`-Benutzer, der die UID 0 hat.

 - Weitere Informationen: [Linux-Standardbasisimage - Benutzer-ID-Bereiche](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

Folgende Benutzernamen sollten Sie bei der Bereitstellung eines virtuellen Linux-Computers vermeiden. Wir empfehlen, dass Sie **diese Benutzernamen nicht verwenden,** denn der Bereitstellungsprozess könnte fehlschlagen.


## openSUSE
- abrt
- adm
- audio
- bin
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel


## SLES
- audio
- bin
- cdrom
- console
- daemon
- dialout
- disk
- floppy
- ftp
- ftp
- games
- haldaemon
- kmem
- lp
- lp
- mail
- maildrop
- man
- messagebus
- modem
- news
- news
- nobody
- nogroup
- polkituser
- postfix
- public
- root
- shadow
- sshd
- sys
- test
- trusted
- tty
- users
- utmp
- uucp
- uuidd
- video
- wheel
- www
- wwwrun
- xok

 
## CentOS
- abrt
- adm
- audio
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel


## Ubuntu
- adm
- admin
- audio
- backup
- bin
- cdrom
- crontab
- daemon
- dialout
- dip
- disk
- fax
- floppy
- fuse
- games
- gnats
- irc
- kmem
- landscape
- libuuid
- list
- lp
- mail
- man
- messagebus
- mlocate
- netdev
- news
- nobody
- nogroup
- operator
- plugdev
- proxy
- root
- sasl
- shadow
- src
- ssh
- sshd
- staff
- sudo
- sync
- sys
- syslog
- tape
- tty
- users
- utmp
- uucp
- video
- voice
- whoopsie
- www-data
 

<!--HONumber=35.1-->

<!--HONumber=35.1-->
