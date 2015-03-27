<properties 
   pageTitle="Erstellen und Hochladen einer FreeBSD-VHD in Azure" 
   description="Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die das FreeBSD-Betriebssystem enthält." 
   services="virtual-machines" 
   documentationCenter="" 
   authors="KylieLiang" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services" 
   ms.date="02/05/2015"
   ms.author="kyliel"/>

# Erstellen und Hochladen einer FreeBSD-VHD in Azure 

Dieser Artikel erläutert, wie Sie eine virtuelle Festplatte (Virtual Hard Disk, VHD), die das FreeBSD-Betriebssystem enthält, erstellen und hochladen, um sie als eigenes Image für die Erstellung von virtuellen Computern in Azure zu nutzen. 

##Voraussetzungen##
In diesem Artikel wird davon ausgegangen, dass Sie über die folgenden Elemente verfügen:

- **Azure-Abonnement**: Wenn Sie über kein Abonnement verfügen, können Sie in wenigen Minuten ein kostenloses Testkonto einrichten. Weitere Informationen finden Sie unter [Erstellen eines Azure-Kontos](http://azure.microsoft.com/documentation/articles/php-create-account/). 

- **Azure PowerShell-Tools** - Sie haben das Microsoft Azure PowerShell-Modul installiert und für die Verwendung Ihres Abonnements konfiguriert. Informationen zum Download des Moduls finden Sie unter [Azure-Downloads](http://azure.microsoft.com/downloads/). Ein Lernprogramm zum Installieren und Konfigurieren des Moduls ist hier verfügbar. Sie verwenden das Cmdlet [Azure-Downloads](http://azure.microsoft.com/downloads/) zum Hochladen der VHD.

- **FreeBSD-Betriebssystem in einer VHD-Datei installiert** - Sie haben ein unterstütztes FreeBSD-Betriebssystem auf einer virtuellen Festplatte installiert. Es gibt verschiedene Tools zum Erstellen von VHD-Dateien. Beispielsweise können Sie eine Virtualisierungslösung wie Hyper-V zum Erstellen der VHD-Datei und zum Installieren des Betriebssystems verwenden. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](http://technet.microsoft.com/library/hh846766.aspx). 

> [AZURE.NOTE] Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem Cmdlet [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx) in das VHD-Format konvertieren.

Diese Aufgabe umfasst die folgenden fünf Schritte.

## Schritt 1: Vorbereiten des hochzuladenden Images ##

Wie für die FreeBSD-Installation auf Hyper-V steht [hier](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx) ein Lernprogramm zur Verfügung.

Führen Sie auf dem virtuellen Computer, auf dem das FreeBSD-Betriebssystem installiert wurde, die folgenden Prozeduren aus:

1. **Aktivieren von DHCP**

		# echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
		# service netif restart

2. **Aktivieren von SSH**

    SSH ist nach der Installation von einem Datenträger standardmäßig aktiviert. Falls nicht oder wenn Sie die FreeBSD-VHD direkt verwenden, geben Sie Folgendes ein:

		# echo 'sshd_enable="YES"' >> /etc/rc.conf 
		# ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key 
		# ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key 
		# service sshd restart

3. **Einrichten der seriellen Konsole**

		# echo 'console="comconsole vidconsole"' >> /boot/loader.conf
		# echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. **Installieren von sudo**

    Das Root-Konto ist in Azure deaktiviert, und Sie müssen sudo mit einem nicht privilegierten Benutzer verwenden, um Befehle mit erweiterten Berechtigungen auszuführen.

		# pkg install sudo

5. Voraussetzungen für den Azure-Agent

    5.1 **Installieren von Python**

		# pkg install python27 py27-asn1
		# ln -s /usr/local/bin/python2.7 /usr/bin/python

    5.2 **Installieren von wget**

		# pkg install wget 

6. **Installieren des Azure-Agents**

    Die neueste Version des Azure-Agents finden Sie immer auf [github](https://github.com/Azure/WALinuxAgent/releases). Version 2.0.10 und höher unterstützt offiziell FreeBSD 10 und höhere Versionen.

		# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.10/waagent --no-check-certificate
		# mv waagent /usr/sbin
		# chmod 755 /usr/sbin/waagent
		# /usr/sbin/waagent -install

    **Important**: After installation, please double check it is running.

		# service -e | grep waagent
		/etc/rc.d/waagent
		# cat /var/log/waagent.log

    Nun können Sie Ihren virtuellen Computer **herunterfahren**. Außerdem könnten Sie Schritt 7 vor dem Herunterfahren ausführen, dieser Schritt ist jedoch optional.

7. Die Aufhebung der Bereitstellung ist optional. Damit wird das System bereinigt und für eine erneute Bereitstellung vorbereitet.

    Mit dem folgenden Befehl werden auch das zuletzt bereitgestellte Benutzerkonto und die zugehörigen Daten gelöscht.

		# waagent -deprovision+user

## Schritt 2: Erstellen eines Speicherkontos in Azure ##

Sie benötigen in Azure ein Speicherkonto, um eine VHD-Datei hochladen zu können, die zum Erstellen eines virtuellen Computers verwendet werden kann. Über das Azure-Verwaltungsportal können Sie ein Speicherkonto erstellen.

1. Melden Sie sich auf dem Azure-Verwaltungsportal an.

2. Klicken Sie in der Befehlsleiste auf **Neu**.

3. Klicken Sie auf **Datendienste** > **Speicher** > **Schnellerfassung**.

	![Quick create a storage account](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Füllen Sie die Felder wie folgt aus:
	
	- Geben Sie unter **URL** einen Unterdomänennamen ein, der in der URL für das Speicherkonto verwendet werden soll. Der Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten. Dieser Name wird der Hostname in der URL, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird.
			
	- Wählen Sie den **Standort oder die Affinitätsgruppe** für das Speicherkonto aus. Mit einer Affinitätsgruppe können Sie Ihre Cloud-Dienste und Speicher im selben Datencenter platzieren.
		 
	- Sie können wählen, ob Sie **Georeplikation** für das Speicherkonto verwenden möchten. Georeplikation ist als Standard voreingestellt. Mit dieser Option werden Ihre Daten kostenfrei an einem sekundären Speicherort repliziert. Wenn es am primären Speicherort zu einem Ausfall kommt, wird ein Failover auf den sekundären Speicherort durchgeführt. Der sekundäre Speicherort wird automatisch zugewiesen und kann nicht verändert werden. Wenn Sie aufgrund gesetzlicher Vorschriften oder Unternehmensrichtlinien mehr Kontrolle über den Speicherort des cloudbasierten Speichers benötigen, können Sie die Georeplikation deaktivieren. Beachten Sie jedoch, dass bei einem späteren Aktivieren der Georeplikation eine einmalige Datenübertragungsgebühr fällig wird, um Ihre vorhandenen Daten in dem sekundären Speicherort zu replizieren. Die Speicherdienste ohne Georeplikation werden mit einem Rabatt angeboten. Ausführliche Informationen zur Verwaltung der Georeplikation für Speicherkonten finden Sie unter: [Erstellen, Verwalten oder Löschen eines Speicherkontos](../storage-create-storage-account/#replication-options).

	![Enter storage account details](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)


5. Klicken Sie auf **Speicherkonto erstellen**. Das Konto wird nun unter **Speicher** angezeigt.

	![Storage account successfully created](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Als Nächstes erstellen Sie einen Container für die hochgeladenen VHDs. Klicken Sie auf den Namen des Speicherkontos und dann auf **Container**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Klicken Sie auf **Container erstellen**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Geben Sie einen **Namen** für den Container ein, und wählen Sie die **Zugriffsrichtlinie** aus.

	![Container name](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

    > [AZURE.NOTE] Der Container ist standardmäßig privat, und der Containerzugriff ist auf den Kontobesitzer eingeschränkt. Um öffentliche Lesezugriffe auf die im Container enthaltenen Blobs, jedoch nicht auf die Containereigenschaften und -metadaten, zuzulassen, verwenden Sie die Option "Öffentlicher Blob". Verwenden Sie die Option "Öffentlicher Container", um vollständigen öffentlichen Lesezugriff auf den Container und die Blobs zuzulassen.

## Schritt 3: Vorbereiten der Verbindung mit Microsoft Azure ##

Bevor Sie eine .vhd-Datei hochladen können, müssen Sie eine sichere Verbindung zwischen dem Computer und Ihrem Abonnement in Azure herstellen. Zu diesem Zweck können Sie die Microsoft Azure Active Directory-Methode oder die Zertifikatmethode verwenden.

<h3>Verwenden der Microsoft Azure AD-Methode</h3>

1. Öffnen Sie die Azure PowerShell-Konsole.

2. Geben Sie den folgenden Befehl ein:  
	`Add-AzureAccount`
	
	Durch diesen Befehl wird ein Anmeldefenster geöffnet, sodass Sie sich mit Ihrem Geschäfts- oder Schulkonto anmelden können.

	![PowerShell Window](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. Die Anmeldeinformationen werden von Azure authentifiziert und gespeichert, dann wird das Fenster geschlossen.

<h3>Verwenden eines Zertifikats</h3> 

1. Öffnen Sie die Azure PowerShell-Konsole. 

2. Geben Sie Folgendes ein: 
	`Get-AzurePublishSettingsFile`.

3. Es wird ein Browserfenster geöffnet, und Sie werden aufgefordert, eine PUBLISHSETTINGS-Datei herunterzuladen. Sie enthält Informationen und ein Zertifikat für Ihr Microsoft Azure-Abonnement.

	![Browser download page](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Speichern Sie die .publishsettings-Datei. 

4. Geben Sie Folgendes ein: 
	`Import-AzurePublishSettingsFile <PathToFile>`
	
	Dabei stellt `<Pfad zu Datei>` den vollständigen Pfad zur Datei ".publishsettings" dar. 

   Weitere Informationen finden Sie unter [Erste Schritte mit Microsoft Azure-Cmdlets](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx). 
	
   Weitere Informationen zur Installation und Konfiguration von PowerShell finden Sie unter [Installieren und Konfigurieren von Microsoft Azure PowerShell](http://www.windowsazure.com/documentation/articles/install-configure-powershell/). 

## Schritt 4: Hochladen der .vhd-Datei ##

Wenn Sie die .vhd-Datei hochladen, können Sie diese .vhd-Datei an einem beliebigen Speicherort innerhalb des Blobspeichers ablegen. In den folgenden Befehlsbeispielen stellt **BlobStorageURL** den URL für das Speicherkonto dar, das Sie im Schritt 2 erstellt haben. Zudem stellt **YourImagesFolder** den Container innerhalb des Blobspeichers dar, in dem Sie Ihre Images speichern möchten. **VHDName** ist die Bezeichnung, die im Verwaltungsportal zur Identifizierung der virtuellen Festplatte angezeigt wird. **PathToVHDFile** enthält den vollständigen Pfad und Namen der VHD-Datei. 


1. Geben Sie über das Azure PowerShell-Fenster, welches Sie im vorherigen Schritt verwendet haben, Folgendes ein:

		Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>		

## Schritt 5: Erstellen eines virtuellen Computers mit hochgeladener VHD ##
Nach dem Hochladen fügen Sie die VHD-Datei als Image zu der Ihrem Abonnement zugeordneten Liste benutzerdefinierter Images hinzu, und erstellen Sie einen virtuellen Computer mit diesem benutzerdefinierten Image.

1. Geben Sie über das Azure PowerShell-Fenster, welches Sie im vorherigen Schritt verwendet haben, Folgendes ein:

		Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    **Wichtig**: Verwenden Sie zunächst Linux als Betriebssystemtyp, da die aktuelle Version von Azure PowerShell nur "Linux" oder "Windows" als Parameter akzeptiert.

2. Nachdem Sie die vorherigen Schritte abgeschlossen haben, wird das neue Image aufgelistet, sobald Sie die Registerkarte **Images** im Azure-Verwaltungsportal auswählen.  

    ![add image](./media/virtual-machines-freebsd-create-upload-vhd/addfreebsdimage.png)

3. Erstellen Sie einen virtuellen Computer über den Katalog. Dieses neue Image steht unter **Eigene Images** zur Verfügung. Wählen Sie das neue Image aus, und durchlaufen Sie die Anweisungen, um einen Hostnamen, Kennwort/SSH-Schlüssel usw. einzurichten. 

	![custom image](./media/virtual-machines-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. Nach Abschluss der Bereitstellung sehen Sie, dass Ihre FreeBSD-VM in Azure ausgeführt wird. 

	![freebsd image in azure](./media/virtual-machines-freebsd-create-upload-vhd/freebsdimageinazure.png)

<!--HONumber=47-->
