<properties urlDisplayName="User Names in Linux" pageTitle="Ausw&auml;hlen von Benutzernamen f&uuml;r Linux in Azure" metaKeywords="" description="Erfahren Sie, wie Sie Benutzernamen f&uuml;r einen virtuellen Linux-Computer in Azure ausw&auml;hlen." metaCanonical="" services="virtual-machines" documentationCenter="" title="Ausw&auml;hlen von Benutzernamen f&uuml;r Linux in Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="szark" />

# Auswählen von Benutzernamen für Linux in Azure

Wenn Sie eine Instanz eines virtuellen Linux-Computers auf Azure erstellen, können Sie den bereitzustellenden Benutzernamen auswählen (der Standardname ist *azureuser*). In den meisten Fällen ist dieser neue Benutzer im Basisimage nicht vorhanden und wird während des Bereitstellungsprozesses erstellt. Wenn der Benutzer im VM-Basisimage bereits vorhanden ist, konfiguriert der Azure-Linux-Agent in einigen Fällen einfach das Kennwort (und/oder den SSH-Schlüssel) für diesen Benutzer auf der Grundlage der Informationen der bereitstellenden Konfiguration, die beim Erstellen der VM übergeben werden.

**Achtung:** Linux definiert einen Satz von Benutzernamen, die bei der Erstellung neuer Benutzer nicht verwendet werden sollten. Der Bereitstellungsprozess verursacht einen **Fehler**, wenn Sie versuchen, eine Linux-VM mithilfe eines vorhandenen Systembenutzers (Benutzer mit der UID 0-99) bereitzustellen. Ein typisches Beispiel ist der `root`-Benutzer mit der UID 0.

-   Weitere Informationen: [Linux-Standardbasisimage - Benutzer-ID-Bereiche][Linux-Standardbasisimage - Benutzer-ID-Bereiche]

Die folgenden Listen enthalten Benutzernamen, die Sie bei der Bereitstellung eines virtuellen Linux-Computers vermeiden sollten. Zur Sicherheit sollten Sie **diese Benutzernamen nicht verwenden**, wenn Sie eine Linux-VM bereitstellen, da der Bereitstellungsprozess sonst fehlschlagen kann.

## openSUSE

-   abrt
-   adm
-   audio
-   bin
-   bin
-   cdrom
-   cgred
-   daemon
-   dbus
-   dialout
-   dip
-   disk
-   floppy
-   ftp
-   games
-   gopher
-   haldaemon
-   halt
-   kmem
-   lock
-   lp
-   mail
-   man
-   mem
-   nfsnobody
-   nobody
-   ntp
-   operator
-   oprofile
-   postdrop
-   postfix
-   qpidd
-   root
-   rpc
-   rpcuser
-   saslauth
-   shutdown
-   slocate
-   sshd
-   stapdev
-   stapusr
-   sync
-   sys
-   tape
-   test
-   tcpdump
-   tty
-   users
-   utempter
-   utmp
-   uucp
-   vcsa
-   video
-   wheel

## SLES

-   audio
-   bin
-   cdrom
-   console
-   daemon
-   dialout
-   disk
-   floppy
-   ftp
-   ftp
-   games
-   haldaemon
-   kmem
-   lp
-   lp
-   mail
-   maildrop
-   man
-   messagebus
-   modem
-   news
-   news
-   nobody
-   nogroup
-   polkituser
-   postfix
-   public
-   root
-   shadow
-   sshd
-   sys
-   test
-   trusted
-   tty
-   users
-   utmp
-   uucp
-   uuidd
-   video
-   wheel
-   www
-   wwwrun
-   xok

## CentOS

-   abrt
-   adm
-   audio
-   bin
-   cdrom
-   cgred
-   daemon
-   dbus
-   dialout
-   dip
-   disk
-   floppy
-   ftp
-   ftp
-   games
-   gopher
-   haldaemon
-   halt
-   kmem
-   lock
-   lp
-   mail
-   man
-   mem
-   nfsnobody
-   nobody
-   ntp
-   operator
-   oprofile
-   postdrop
-   postfix
-   qpidd
-   root
-   rpc
-   rpcuser
-   saslauth
-   shutdown
-   slocate
-   sshd
-   stapdev
-   stapusr
-   sync
-   sys
-   tape
-   test
-   tcpdump
-   tty
-   users
-   utempter
-   utmp
-   uucp
-   vcsa
-   video
-   wheel

## Ubuntu

-   adm
-   admin
-   audio
-   backup
-   bin
-   cdrom
-   crontab
-   daemon
-   dialout
-   dip
-   disk
-   fax
-   floppy
-   fuse
-   games
-   gnats
-   irc
-   kmem
-   landscape
-   libuuid
-   list
-   lp
-   mail
-   man
-   messagebus
-   mlocate
-   netdev
-   news
-   nobody
-   nogroup
-   operator
-   plugdev
-   proxy
-   root
-   sasl
-   shadow
-   src
-   ssh
-   sshd
-   staff
-   sudo
-   sync
-   sys
-   syslog
-   tape
-   tty
-   users
-   utmp
-   uucp
-   video
-   voice
-   whoopsie
-   www-data

  [Linux-Standardbasisimage - Benutzer-ID-Bereiche]: http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html
