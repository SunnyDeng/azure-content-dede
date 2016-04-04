<properties 
	pageTitle="Auswählen von Benutzernamen für Linux | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Benutzernamen für einen virtuellen Linux-Computer in Azure auswählen." 
	services="virtual-machines-linux" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="szark"/>



#Auswählen von Benutzernamen für Linux in Azure#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Beim Bereitstellen eines virtuellen Linux-Computers in Azure müssen Sie den Namen eines Benutzers ohne Root-Rechte angeben, den Sie später bei der Anmeldung am virtuellen Computer benutzen können. Sie können den Namen des neuen Benutzers wählen, oder Sie können den Standardnamen „azureuser“ übernehmen, wenn Sie die Bereitstellung über das klassische Azure-Portal durchführen.

In den meisten Fällen ist dieser Benutzer im Basisimage nicht vorhanden und wird während des Bereitstellungsprozesses erstellt. Wenn der Benutzer im VM-Basisimage vorhanden ist, konfiguriert der Azure Linux Agent in einigen Fällen einfach das Kennwort (und/oder den SSH-Schlüssel) für diesen Benutzer auf der Grundlage der Informationen, die Sie beim Erstellen der VM festgelegt haben.

**Allerdings** definiert Linux einen Satz von Benutzernamen, die nicht verwendet werden sollten. Der Bereitstellungsprozess **schlägt fehl**, wenn Sie versuchen, einen virtuellen Linux-Computer mithilfe eines vorhandenen Systembenutzers bereitzustellen, der als Benutzer mit einer UID zwischen 0 und 99 definiert ist. Ein typisches Beispiel ist der Benutzer `root`, der die UID 0 hat.

 - Siehe auch: [Linux-Standardbasis – Benutzer-ID-Bereiche](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

Folgende Benutzernamen sollten Sie bei der Bereitstellung eines virtuellen Linux-Computers vermeiden. Von der Verwendung dieser Benutzernamen **wird abgeraten**, da der Bereitstellungsprozess für einen virtuellen Computer ansonsten fehlschlagen kann.


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

 

<!---HONumber=AcomDC_0323_2016-->