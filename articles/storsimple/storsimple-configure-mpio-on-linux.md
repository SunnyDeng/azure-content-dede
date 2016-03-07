<properties 
   pageTitle="Konfigurieren von MPIO auf einem StorSimple-Linux-Host | Microsoft Azure"
   description="Konfigurieren von MPIO auf einem StorSimple-Gerät, das mit einem Linux-Host mit CentOS 6.6 verbunden ist"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="alkohli" />

# Konfigurieren von MPIO auf einem StorSimple-Host mit CentOS

In diesem Artikel werden die Schritte erläutert, die zum Konfigurieren von Multipfad-E/A (Multipathing IO, MPIO) auf Ihrem CentOS 6.6-Hostserver ausgeführt werden müssen. Der Hostserver ist zur Gewährleistung hoher Verfügbarkeit über iSCSI-Initiatoren mit Ihrem Microsoft Azure StorSimple-Gerät verbunden. Nachfolgend wird im Detail beschrieben, wie Multipfadgeräte automatisch erkannt und wie die Einrichtung für StorSimple-Volumes durchgeführt wird.

Dieses Verfahren kann auf alle Modelle der StorSimple 8000-Geräteserie angewendet werden.

>[AZURE.NOTE] Die Vorgehensweise gilt nicht für ein virtuelles StorSimple-Gerät. Weitere Informationen zu diesem Thema finden Sie in den Abschnitten zur Konfiguration von Hostservern für virtuelle Geräte.

## Grundlegendes zu Multipfad 

Mit dem Multipfadfeature können Sie mehrere E/A-Pfade zwischen einem Hostserver und einem Speichergerät konfigurieren. Diese E/A-Pfade sind physische SAN-Verbindungen, die separate Kabel, Switches, Netzwerkschnittstellen und Controller umfassen können. Multipfad aggregiert die E/A-Pfade, um ein neues Gerät zu konfigurieren, dem sämtliche dieser aggregierten Pfade zugeordnet sind.

Multipfad bietet zwei Vorteile:

- **Hohe Verfügbarkeit**: Es bietet einen alternativen Pfad, wenn ein beliebiges Element im E/A-Pfad (z. B. ein Kabel, Switch, eine Netzwerkschnittstelle oder ein Controller) ausfällt.

- **Lastenausgleich**: Je nach Konfiguration Ihres Speichergeräts kann die Leistung erhöht werden, da Lasten in den E/A-Pfaden erkannt und dynamisch verteilt werden.


### Grundlegendes zu Multipfadkomponenten 

In Linux umfasst Multipfad Kernelkomponenten und Benutzerbereichskomponenten, wie in der nachstehenden Auflistung gezeigt wird.

- **Kernel**: Die Hauptkomponente ist *device-mapper*, die für eine E/A-Umleitung sorgt und ein Failover für Pfade und Pfadgruppen unterstützt.

1. **Benutzerbereich**: Hier werden über *multipath-tools* Multipfadgeräte verwaltet, indem Anweisungen an das Mutipfadmodul "device-mapper" gesendet werden. Die Tools umfassen Folgendes:

	- **Multipath**: Listet Multipfadgeräte auf und konfiguriert diese.
		
	- **Multipathd**: Daemon, der Multipfad ausführt und die Pfade überwacht.
	
	- **Devmap-name**: Stellt einen aussagekräftigen Gerätenamen für "udev" in Gerätezuordnungen bereit.
 
	- **Kpartx**: Ordnet lineare Gerätezuordnungen Gerätepartitionen zu, um Multipfadzuordnungen partitionieren zu können.
	
	- **Multipath.conf**: Konfigurationsdatei für Multipfaddaemon, der zum Überschreiben der integrierten Konfigurationstabelle verwendet wird.

### Grundlegendes zur Konfigurationsdatei "multipath.conf"

Die Konfigurationsdatei `/etc/multipath.conf` ermöglicht eine Konfiguration vieler der Multipfadfunktionen durch den Benutzer. Der `multipath`-Befehl und der Kerneldaemon `multipathd` verwenden die in dieser Datei gefundenen Informationen. Die Datei wird nur während der Konfiguration der Multipfadgeräte geprüft. Stellen Sie sicher, dass alle gewünschten Änderungen vorgenommen wurden, bevor Sie den Befehl `multipath` ausführen. Wenn Sie die Datei im Anschluss ändern, müssen Sie Multipfad beenden und neu starten, damit die Änderungen wirksam werden.

Die Datei "multipath.conf" enthält fünf Abschnitte:

- **Standardeinstellungen auf Systemebene** *(defaults)*: Sie können die Standardwerte auf Systemebene überschreiben.

1. **Gesperrte Geräte** *(blacklist)*: Sie können eine Liste der Geräte konfigurieren, die nicht über "device-mapper" gesteuert werden sollen.

1. **Ausnahmen für schwarze Liste** *(blacklist\_exceptions)*: Sie können festlegen, dass bestimmte Geräte als Multipfadgeräte behandelt werden, selbst wenn Sie in der schwarzen Liste aufgeführt sind.

1. **Einstellungen für den Speichercontroller** *(devices)*: Sie können Konfigurationseinstellungen festlegen, die auf Geräte mit Hersteller- und Produktinformationen angewendet werden.

1. **Gerätespezifische Einstellungen** *(multipaths)*: Sie können diesen Abschnitt dazu verwenden, die Konfigurationseinstellungen für einzelne LUNs zu optimieren.

## Konfigurieren von Multipfad auf StorSimple-Geräten mit Verbindung zu einem Linux-Host

Ein StorSimple-Gerät, das mit einem Linux-Host verbunden ist, kann für hohe Verfügbarkeit und Lastenausgleich konfiguriert werden. Angenommen, der Linux-Host ist über zwei Schnittstellen mit dem SAN verbunden, und das Gerät ist ebenfalls über zwei Schnittstellen mit dem SAN verbunden. Wenn sich diese Schnittstellen im selben Subnetz befinden, stehen 4 Pfade zur Verfügung. Wenn sich jedoch die DATA-Schnittstelle zum Gerät und die Hostschnittstelle in einem unterschiedlichen IP-Subnetz befinden (und nicht routingfähig sind), stehen nur 2 Pfade zur Verfügung. Sie können Multipfad zur automatischen Erkennung aller verfügbaren Pfade konfigurieren, einen Lastenausgleichsalgorithmus für diese Pfade auswählen, spezifische Konfigurationseinstellungen für reine StorSimple-Volumes festlegen und Multipfad anschließend aktivieren und überprüfen.

Das folgende Verfahren beschreibt, wie Multipfad konfiguriert wird, wenn ein StorSimple-Gerät mit zwei Netzwerkschnittstellen mit einem Host mit zwei Netzwerkschnittstellen verbunden ist.

## Voraussetzungen

In diesem Abschnitt werden die Konfigurationsvoraussetzungen für CentOS-Server und Ihr StorSimple-Gerät beschrieben.

### Auf dem CentOS-Host



1. Stellen Sie sicher, dass Ihr CentOS-Host über zwei aktivierte Netzwerkschnittstellen verfügt. Geben Sie Folgendes ein:

	`ifconfig`

	Das folgende Beispiel zeigt die Ausgabe, wenn zwei Netzwerkschnittstellen (`eth0` und `eth1`) auf dem Host vorhanden sind.

    	[root@centosSS ~]# ifconfig
    	eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
      	inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
      	inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
      	inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
      	UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
     	RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:1000 
      	RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
    
    	eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
      	inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
      	inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
      	inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
      	UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
      	RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:1000 
      	RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
    
    	loLink encap:Local Loopback  
      	inet addr:127.0.0.1  Mask:255.0.0.0
      	inet6 addr: ::1/128 Scope:Host
      	UP LOOPBACK RUNNING  MTU:65536  Metric:1
      	RX packets:12 errors:0 dropped:0 overruns:0 frame:0
      	TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
      	collisions:0 txqueuelen:0 
      	RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)


1. Installieren Sie *iSCSI-initiator-utils* auf Ihrem CentOS-Server. Führen Sie die folgenden Schritte aus, um *iSCSI-initiator-utils* zu installieren.

	1. Melden Sie sich als `root` bei Ihrem CentOS-Host an.

	1. Installieren Sie *iSCSI-initiator-utils*. Geben Sie Folgendes ein:
		
		`yum install iscsi-initiator-utils`


	1. Nachdem *iSCSI-initiator-utils* erfolgreich installiert wurde, starten Sie den iSCSI-Dienst. Geben Sie Folgendes ein:

		`service iscsid start`

		Gelegentlich wird `iscsid` nicht gestartet, dann ist die Option `--force` erforderlich.

	1. Um sicherzustellen, dass Ihr iSCSI-Initiator beim Start aktiviert wird, verwenden Sie den `chkconfig`-Befehl, um den Dienst zu starten.

		`chkconfig iscsi on`


	1. Führen Sie den folgenden Befehl aus, um die ordnungsgemäße Einrichtung zu überprüfen:
	
		`chkconfig --list | grep iscsi`
	
		Nachfolgend sehen Sie eine Beispielausgabe.

			iscsi   0:off   1:off   2:on3:on4:on5:on6:off
			iscsid  0:off   1:off   2:on3:on4:on5:on6:off

		Im obigen Beispiel können Sie sehen, dass Ihre iSCSI-Umgebung zur Startzeit auf den Ausführungsebenen 2, 3, 4 und 5 ausgeführt wird.


1. Installieren Sie *device-mapper-multipath*. Geben Sie Folgendes ein:

	`yum install device-mapper-multipath`

	Die Installation wird gestartet. Geben Sie **Y** ein, wenn Sie zur Bestätigung aufgefordert werden.



### Auf dem StorSimple-Gerät

Ihr StorSimple-Gerät muss folgende Anforderungen erfüllen:

- Es sind mindestens zwei Schnittstellen für iSCSI aktiviert. Um sicherzustellen, dass auf Ihrem StorSimple-Gerät zwei Schnittstellen für iSCSI aktiviert sind, führen Sie die folgenden Schritte im klassischen Azure-Portal für Ihr StorSimple-Gerät aus:

	1. Melden Sie sich beim klassischen Portal für Ihr StorSimple-Gerät an.

	1. Wählen Sie Ihren StorSimple-Manager-Dienst aus, klicken Sie auf **Geräte**, und wählen Sie das gewünschte StorSimple-Gerät aus. Klicken Sie auf **Konfigurieren**, und überprüfen Sie die Einstellungen für die Netzwerkschnittstellen. Nachfolgend sehen Sie einen Screenshot, der zwei für iSCSI aktivierte Netzwerkschnittstellen zeigt. In diesem Beispiel sind DATA 2 und DATA 3 – beides 10-GbE-Schnittstellen – für iSCSI aktiviert.
	
		![MPIO-Konfiguration für StorSimple-Gerät DATA 2](./media/storsimple-configure-mpio-on-linux/IC761347.png)
	
		![MPIO-Konfiguration für StorSimple-Gerät DATA 3](./media/storsimple-configure-mpio-on-linux/IC761348.png)

		Auf der Seite **Konfigurieren**

		1. Stellen Sie sicher, dass beide Netzwerkschnittstellen für iSCSI aktiviert sind. Das Feld **iSCSI-aktiviert** sollte auf **Ja** festgelegt sein.
		2. Stellen Sie sicher, dass die Netzwerkschnittstellen über dieselbe Geschwindigkeit verfügen, für beide entweder 1 GbE oder 10 GbE.
		3. Notieren Sie sich die IPv4-Adressen der iSCSI-aktivierten Schnittstellen, und bewahren Sie sie für eine spätere Verwendung auf dem Host auf.


- Die iSCSI-Schnittstellen auf Ihrem StorSimple-Gerät sollten vom CentOS-Server aus erreichbar sein.

	Um dies zu überprüfen, müssen Sie die IP-Adressen der iSCSI-aktivierten Netzwerkschnittstellen Ihres StorSimple-Geräts auf dem Hostserver bereitstellen. Die verwendeten Befehle und die entsprechende Ausgabe für DATA2 (10.126.162.25) und DATA3 (10.126.162.26) wird nachfolgend gezeigt:

    	[root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
    	10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    	10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target


### Hardwarekonfiguration

Es wird empfohlen, die zwei iSCSI-Netzwerkschnittstellen aus Gründen der Redundanz über separate Pfade zu verbinden. Die nachstehende Abbildung zeigt die empfohlene Hardwarekonfiguration für hohe Verfügbarkeit und Lastenausgleich mit Multipfad für Ihren CentOS-Server und das StorSimple-Gerät.

![MPIO-Hardwarekonfiguration für StorSimple-Gerät mit Verbindung zu Linux-Host](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Die Abbildung oben zeigt Folgendes:

- Ihr StorSimple-Gerät ist in einer Aktiv/Passiv-Konfiguration mit zwei Controllern enthalten.

- An Ihre Gerätecontroller sind zwei SAN-Switches angeschlossen.
 
- Auf Ihrem StorSimple-Gerät sind zwei iSCSI-Initiatoren aktiviert.
 
- Auf Ihrem CentOS-Host sind zwei Netzwerkschnittstellen aktiviert.

Über die obige Konfiguration werden 4 separate Pfade zwischen Ihrem Gerät und dem Host bereitgestellt, wenn Host und Datenschnittstellen routingfähig sind.

>[AZURE.IMPORTANT] 
>
>- Es wird nicht empfohlen, 1-GbE- und 10-GbE-Netzwerkschnittstellen für Multipfad zu mischen. Bei Verwendung von zwei Netzwerkschnittstellen sollten beide denselben Typ aufweisen.
>- Auf Ihrem StorSimple-Gerät sind DATA0, DATA1, DATA4 und DATA5 1-GbE-Schnittstellen, während es sich bei DATA2 und DATA3 um 10-GbE-Schnittstellen handelt.

## Konfigurationsschritte

Die Konfigurationsschritte für Multipfad umfassen das Konfigurieren der verfügbaren Pfade für die automatische Erkennung, das Festlegen des gewünschten Lastenausgleichsalgorithmus, das Aktivieren von Multipfad und schließlich das Überprüfen der Konfiguration. Jeder dieser Schritte wird in den folgenden Abschnitten ausführlich erläutert.

### Schritt 1: Konfigurieren von Multipfad für die automatische Erkennung

Geräte mit Unterstützung für Multipfad können automatisch erkannt und konfiguriert werden.

1. Initialisieren Sie die Datei `/etc/multipath.conf`. Geben Sie Folgendes ein:

	 `Copy mpathconf --enable`
	
	Mit dem obigen Befehl wird die Datei `sample/etc/multipath.conf` erstellt.


1. Starten Sie den Multipfaddienst. Geben Sie Folgendes ein:

    ``Copy service multipathd start``
	
	Die folgende Ausgabe wird angezeigt:

	`Starting multipathd daemon:`

1. Aktivieren Sie die automatische Erkennung von Multipfaden. Geben Sie Folgendes ein:

	`mpathconf --find_multipaths y`

	Durch diesen Befehl wird der Abschnitt "defaults" Ihrer Datei `multipath.conf` wie folgt geändert:

		defaults {
		find_multipaths yes
		user_friendly_names yes
		path_grouping_policy multibus
		}

### Schritt 2: Konfigurieren von Multipfad für StorSimple-Volumes

Standardmäßig werden in der Datei "multipath.conf" alle Geräte auf die schwarze Liste gesetzt und umgangen. Sie müssen Ausnahmen für die schwarze Liste definieren, um Multipfad für Volumes auf StorSimple-Geräten verwenden zu können.

1. Bearbeiten Sie die Datei `/etc/mulitpath.conf`. Geben Sie Folgendes ein:

	`vi /etc/multipath.conf`

1. Suchen Sie in der Datei "multipath.con" nach dem Abschnitt "blacklist\_exceptions". Ihr StorSimple-Gerät muss in diesem Abschnitt als Ausnahme für die schwarze Liste aufgeführt sein. Sie können die Auskommentierung der relevanten Zeilen in dieser Datei aufheben, um die Datei wie nachfolgend gezeigt zu ändern (verwenden Sie nur das für Sie spezifische Gerätemodell):

    	blacklist_exceptions {
    	    device {
    	               vendor  "MSFT"
    	               product "STORSIMPLE 8100*"
    	    }
    	    device {
    	               vendor  "MSFT"
    	               product "STORSIMPLE 8600*"
    	    }
    	   }

### Schritt 3: Konfigurieren von Multipfad mit Roundrobin

Dieser Lastenausgleichsalgorithmus verwendet alle verfügbaren Pfade zum aktiven Controller in ausgeglichener Form (Roundrobin).

1. Bearbeiten Sie die Datei `/etc/multipath.conf`. Geben Sie Folgendes ein:

	`vi /etc/multipath.conf`

1. Legen Sie im Abschnitt `defaults` den Wert für `path_grouping_policy` auf `multibus` fest. `path_grouping_policy` gibt die Standardrichtlinie für die Pfadgruppierung an, die auf nicht festgelegte Pfade angewendet wird. Der Abschnitt "defaults" sieht aus wie nachstehend gezeigt.

	    defaults {
	            user_friendly_names yes
	            path_grouping_policy multibus
	    }



> [AZURE.NOTE] 
Die gängigsten Werte für `path_grouping_policy` lauten:
	
> - failover = 1 Pfad pro Prioritätsgruppe
> - multibus = alle gültigen Pfade in einer Prioritätsgruppe

### Schritt 4: Aktivieren von Multipfad

1. Starten Sie den `multipathd`-Daemon neu. Geben Sie Folgendes ein:

    `service multipathd restart`

1. Die Ausgabe sieht folgendermaßen aus:

    	[root@centosSS ~]# service multipathd start
    	Starting multipathd daemon:  [OK]




### Schritt 5: Überprüfen von Multipfad

1. Stellen Sie zunächst folgendermaßen sicher, dass die iSCSI-Verbindung mit dem StorSimple-Gerät eingerichtet ist:


	1. Führen Sie eine Erkennung für Ihr StorSimple-Gerät aus. Geben Sie Folgendes ein:
		
		`iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>`

		Die Ausgabe sieht folgendermaßen aus, wenn die IP-Adresse für DATA0 10.126.162.25 lautet und auf dem StorSimple-Gerät Port 3260 für ausgehenden iSCSI-Datenverkehr geöffnet ist:

		    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
		    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target


		Kopieren Sie den IQN Ihres StorSimple-Geräts, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`, aus der vorstehenden Ausgabe.



	1. Stellen Sie über den Ziel-IQN eine Verbindung mit dem Gerät her. Das StorSimple-Gerät ist in diesem Fall das iSCSI-Ziel. Geben Sie Folgendes ein:

		`iscsiadm -m node --login -T <IQN of iSCSI target>`

		Das folgende Beispiel zeigt die Ausgabe bei Verwendung des Ziel-IQN `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target`. Die Ausgabe weist darauf hin, dass die Verbindungsherstellung mit den zwei iSCSI-aktivierten Netzwerkschnittstellen auf Ihrem Gerät erfolgreich war.

		    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
	    	Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
	    	Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
	    	Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
	    	Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
	    	Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
	    	Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
	    		Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.


		Wenn Sie hier nur eine Hostschnittstelle und zwei Pfade sehen, müssen Sie beide Schnittstellen auf dem Host für iSCSI aktivieren. Folgen Sie den [detaillierten Anweisungen in der Linux-Dokumentation](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html).

	
	1. Das StorSimple-Gerät macht ein Volume für den CentOS-Server verfügbar. Weitere Informationen finden Sie unter [Schritt 6: Erstellen eines Volumes](storsimple-deployment-walkthrough.md#step-6-create-a-volume) mit dem klassischen Azure-Portal auf Ihrem StorSimple-Gerät.

	1. Überprüfen Sie die verfügbaren Pfade. Geben Sie Folgendes ein:

		`multipath –l`

		Das folgende Beispiel zeigt die Ausgabe für zwei Netzwerkschnittstellen auf einem StorSimple-Gerät, das mit einer einzigen Hostnetzwerkschnittstelle mit zwei verfügbaren Pfaden verbunden ist.

		    mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
    		size=100G features='0' hwhandler='0' wp=rw
    		`-+- policy='round-robin 0' prio=0 status=active
    		  |- 7:0:0:1 sdc 8:32 active undef running
    		  `- 6:0:0:1 sdd 8:48 active undef running

		Das folgende Beispiel zeigt die Ausgabe für zwei Netzwerkschnittstellen auf einem StorSimple-Gerät, das mit zwei Hostnetzwerkschnittstellen mit vier verfügbaren Pfaden verbunden ist.
		
		    mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
    		size=100G features='0' hwhandler='0' wp=rw
    		`-+- policy='round-robin 0' prio=0 status=active
    		  |- 17:0:0:0 sdb 8:16 active undef running
    		  |- 15:0:0:0 sdd 8:48 active undef running
    		  |- 14:0:0:0 sdc 8:32 active undef running
    		  `- 16:0:0:0 sde 8:64 active undef running

		Nachdem Sie die Pfade konfiguriert haben, folgen Sie den Anweisungen für Ihr Hostbetriebssystem (CentOS 6.6), um das Volume einzubinden und zu formatieren.


## Problembehandlung für Multipfad

In diesem Abschnitt werden einige nützliche Tipps bereitgestellt, um mögliche Probleme bei der Konfiguration von Multipfad zu beheben.

F: Änderungen, die an der Datei `multipath.conf` vorgenommen wurden, treten nicht in Kraft.

A: Wenn Sie Änderungen an der Datei `multipath.conf` vornehmen, müssen Sie den Multipfaddienst neu starten. Geben Sie den folgenden Befehl ein:
    
    service multipathd restart

F: Ich habe zwei Netzwerkschnittstellen auf dem StorSimple-Gerät und zwei Netzwerkschnittstellen auf dem Host aktiviert. Wenn ich die verfügbaren Pfade aufliste, werden nur zwei Pfade angezeigt. Ich habe erwartet, dass vier verfügbare Pfade angezeigt werden.

A: Stellen Sie sicher, dass die zwei Pfade sich im selben Subnetz befinden und routingfähig sind. Wenn sich die zwei Netzwerkschnittstellen in unterschiedlichen vLANs befinden und nicht routingfähig sind, werden nur zwei Pfade angezeigt. Eine Möglichkeit der Überprüfung besteht darin sicherzustellen, dass Sie beide Hostschnittstellen über eine Netzwerkschnittstelle auf dem StorSimple-Gerät erreichen können. Hierzu müssen Sie sich an den [Microsoft Support](storsimple-contact-microsoft-support.md) wenden, da diese Überprüfung nur über eine Supportsitzung durchgeführt werden kann.

F: Wenn ich die verfügbaren Pfade aufliste, wird keine Ausgabe angezeigt.

A: Wenn keinerlei Pfade für Multipfad angezeigt werden, deutet dies auf ein Problem mit dem Multipfaddaemon hin. Wahrscheinlich liegt ein Problem in der Datei `multipath.conf` vor.

Sie sollten möglicherweise auch prüfen, ob nach der Verbindungsherstellung mit dem Ziel überhaupt Datenträger angezeigt werden. Eine fehlende Antwort von der Multipfadauflistung kann auch bedeuten, dass keine Datenträger vorhanden sind.

- Verwenden Sie den folgenden Befehl, um den SCSI-Bus erneut zu überprüfen: 
 
	`$ rescan-scsi-bus.sh ` (Teil des sg3\_utils-Pakets)
 
- Geben Sie die folgenden Befehle ein:

	`$ dmesg | grep sd*`
 
- Oder

	`$ fdisk –l`
 
	Auf diese Weise werden Detailinformationen zu den vor Kurzem hinzugefügten Datenträgern zurückgegeben.
  
- Ermitteln Sie mithilfe der folgenden Befehle, ob es sich um einen StorSimple-Datenträger handelt:
 
	`cat /sys/block/<DISK>/device/model`
 
	Die so zurückgegebene Zeichenfolge gibt an, ob es sich um einen StorSimple-Datenträger handelt.

Eine weniger wahrscheinliche Ursache könnte ein veralteter Wert für "iscsid pid" sein. Verwenden Sie den folgenden Befehl, um sich von den iSCSI-Sitzungen abzumelden:

    iscsiadm -m node --logout -p <Target_IP>

Wiederholen Sie diesen Befehl für alle verbundenen Netzwerkschnittstellen auf dem iSCSI-Ziel, Ihrem StorSimple-Gerät. Nachdem Sie sich von allen iSCSI-Sitzungen abgemeldet haben, verwenden Sie den iSCSI-Ziel-IQN, um die iSCSI-Sitzung wiederherzustellen. Geben Sie den folgenden Befehl ein:

    iscsiadm -m node --login -T <TARGET_IQN>


F: Ich bin nicht sicher, ob mein Gerät auf der Zulassungsliste befindet.

A: Um zu überprüfen, ob sich Ihr Gerät auf der Zulassungsliste befindet, verwenden Sie den folgenden interaktiven Befehl für die Problembehandlung:

	multipathd –k
	multipathd> show devices
	available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored


Weitere Informationen finden Sie unter [Verwenden interaktiver Befehle zur Problembehandlung für Multipfad](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html) (in englischer Sprache).

## Liste nützlicher Befehle

|Typ|Befehl|Beschreibung|
|---|---|---|
|**iSCSI**|`service iscsid start`|Starten des iSCSI-Diensts|
||`service iscsid stop`|Beenden des iSCSI-Diensts|
||`service iscsid restart`|Neustarten des iSCSI-Diensts|
||`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>`|Ermitteln verfügbarer Ziele für die angegebene Adresse|
||`iscsiadm -m node --login -T <TARGET_IQN>`|Anmelden am iSCSI-Ziel|
||`iscsiadm -m node --logout -p <Target_IP>`|Abmelden vom iSCSI-Ziel|
||`cat /etc/iscsi/initiatorname.iscsi`|Drucken des iSCSI-Initiatornamens|
||`iscsiadm –m session –s <sessionid> -P 3`|Überprüfen des Status der iSCSI-Sitzung und des auf dem Host ermittelten Volumes|
||`iscsi –m session`|Zeigt alle iSCSI-Sitzungen zwischen dem Host und dem StorSimple-Gerät|
| | | |
|**Multipfad**|`service multipathd start`|Starten des Multipfad-Daemons|
||`service multipathd stop`|Beenden des Multipfad-Daemons|
||`service multipathd restart`|Neustarten des Multipfad-Daemons|
||`chkconfig multipathd on` </br> ODER </br> `mpathconf –with_chkconfig y`|Aktivieren des Multipfad-Daemons beim Systemstart|
||`multipathd –k`|Starten der interaktiven Konsole für die Problembehandlung|
||`multipath –l`|Auflisten von Multipfad-Verbindungen und -Geräten|
||`mpathconf --enable`|Erstellen einer Beispiel-Multipfad-Konfigurationsdatei in `/etc/mulitpath.conf`|
||||

## Nächste Schritte

Wenn Sie MPIO auf einem Linux-Host konfigurieren, müssen Sie möglicherweise auf die folgenden CentOS 6.6-Dokumente zurückgreifen:

- [Einrichten von MPIO auf CentOS](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
- [Linux-Trainingshandbuch](http://linux-training.be/files/books/LinuxAdm.pdf)

<!---HONumber=AcomDC_0224_2016-->