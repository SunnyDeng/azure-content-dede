<properties 
	pageTitle="Installieren von MongoDB auf einem virtuellen Computer unter Windows Server" 
	description="Erfahren Sie, wie Sie MongoDB auf einem virtuellen Azure-Computer unter Windows Server installieren." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="kathydav"/>

#Installieren von MongoDB auf einem virtuellen Computer unter Windows Server

[MongoDB][MongoDB] ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank. Mit dem [Azure-Verwaltungsportal][AzureManagementPortal] können Sie über die Bildergalerie einen virtuellen Windows Server-Computer erstellen. Anschließend können Sie auf dem virtuellen Computer eine MongoDB-Datenbank installieren und konfigurieren.

In diesem Artikel wird Folgendes behandelt:

- Verwenden des Verwaltungsportals zum Erstellen eines virtuellen Windows Server-Computers über die Galerie
- Herstellen der Verbindung zum virtuellen Computer über Remotedesktop
- Anfügen eines Datenträgers zum virtuellen Computer
- Installieren von MongoDB auf dem virtuellen Computer

## Erstellen eines virtuellen Windows Server-Computers

Nachfolgend sind allgemeine Anweisungen aufgeführt, die Sie modifizieren können, indem Sie einen Endpunkt erstellen, damit remote auf MongoDB zugegriffen werden kann. (Sie können ihn auch später erstellen. Die entsprechende Beschreibung folgt auf die Anweisungen zum Installieren von MongoDB.) Fügen Sie auf der letzten Seite des Assistenten einen Endpunkt hinzu, und konfigurieren Sie ihn wie folgt:

- Weisen Sie ihm den Namen **Mongo** zu.
- Verwenden Sie **TCP** als Protokoll.
- Legen Sie sowohl für die öffentlichen als auch für die privaten Ports den Wert **27017** fest.
 
[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## Datenträger anfügen
Fügen Sie einen Datenträger an den virtuellen Computer an, und initialisieren Sie ihn anschließend für die Verwendung durch Windows, damit ein entsprechender Speicher für den virtuellen Computer verfügbar ist. Sie können entweder einen vorhandenen Datenträger anfügen, wenn Sie bereits über zu verwendende Daten verfügen, oder Sie können einen leeren Datenträger anfügen.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Informationen zum Initialisieren des Datenträgers finden Sie in [Anfügen eines Datenträgers an einen virtuellen Windows-Computer](storage-windows-attach-disk.md) unter "Initialisieren eines neues Datenträgers unter Windows Server".

## Installieren und Ausführen von MongoDB auf dem virtuellen Computer 

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

##Zusammenfassung
In diesem Lernprogramm haben Sie erfahren, wie Sie einen virtuellen Windows Server-Computer erstellen, eine Remoteverbindung dazu herstellen und anschließend einen Datenträger anfügen. Sie haben außerdem erfahren, wie Sie MongoDB auf dem virtuellen Windows-Computer installieren und konfigurieren. Weitere Informationen zu MongoDB finden Sie in der [MongoDB-Dokumentation][MongoDocs].

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzureManagementPortal]: http://manage.windowsazure.com
 

<!---HONumber=58_postMigration-->