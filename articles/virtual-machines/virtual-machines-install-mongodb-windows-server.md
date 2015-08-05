<properties
	pageTitle="Installieren von MongoDB auf einem virtuellen Computer unter Windows Server"
	description="Erfahren Sie, wie Sie MongoDB auf einem virtuellen Azure-Computer unter Windows Server installieren."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="dkshir"/>

#Installieren von MongoDB auf einem virtuellen Computer unter Windows Server

[MongoDB][MongoDB] ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank. Mit dem [Azure-Verwaltungsportal][AzureManagementPortal] können Sie über die Bildergalerie einen virtuellen Windows Server-Computer erstellen. Anschließend können Sie auf dem virtuellen Computer eine MongoDB-Datenbank installieren und konfigurieren.


## Erstellen eines virtuellen Computers unter Windows Server

Befolgen Sie diese Anweisungen, um einen virtuellen Computer zu erstellen.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

> [AZURE.NOTE]Sie können beim Erstellen des virtuellen Computers einen Endpunkt für MongoDB hinzufügen und wie folgt konfigurieren: Vergeben Sie den Namen **Mongo**, verwenden Sie **TCP** als Protokoll, und legen Sie die öffentlichen und privaten Ports auf **27017** fest.

## Datenträger anfügen
Fügen Sie einen Datenträger an den virtuellen Computer an, und initialisieren Sie ihn anschließend für die Verwendung durch Windows, damit ein entsprechender Speicher für den virtuellen Computer verfügbar ist. Sie können entweder einen vorhandenen Datenträger anfügen, wenn Sie bereits über zu verwendende Daten verfügen, oder Sie können einen leeren Datenträger anfügen.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Informationen zum Initialisieren des Datenträgers finden Sie in [Anfügen eines Datenträgers an einen virtuellen Windows-Computer](storage-windows-attach-disk.md) unter "Initialisieren eines neues Datenträgers unter Windows Server".

## Installieren und Ausführen von MongoDB auf dem virtuellen Computer

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

##Zusammenfassung
In diesem Lernprogramm haben Sie erfahren, wie Sie einen virtuellen Windows Server-Computer erstellen, eine Remoteverbindung dazu herstellen und anschließend einen Datenträger anfügen. Sie haben außerdem erfahren, wie Sie MongoDB auf dem virtuellen Windows-Computer installieren und konfigurieren. Sie können nun von dem virtuellen Windows-Computer aus auf MongoDB zugreifen, indem Sie die fortgeschrittenen Themen in der[MongoDB-Dokumentation][MongoDocs] befolgen.

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzureManagementPortal]: http://manage.windowsazure.com

<!---HONumber=July15_HO4-->