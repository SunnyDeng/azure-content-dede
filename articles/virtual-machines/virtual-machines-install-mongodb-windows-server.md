<properties
	pageTitle="Installieren von MongoDB auf einem virtuellen Windows-Computer | Microsoft Azure"
	description="Erfahren Sie, wie Sie MongoDB auf einem virtuellen Azure-Computer installieren, der mit dem klassischen Bereitstellungsmodell unter Windows Server erstellt wurde."
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
	ms.date="10/14/2015"
	ms.author="dkshir"/>

#Installieren von MongoDB auf einem virtuellen Windows-Computer

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


[MongoDB][MongoDB] ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank. Im [Azure-Portal][AzurePortal] können Sie über den Imagekatalog einen virtuellen Windows Server-Computer unter Verwendung des klassischen Bereitstellungsmodells erstellen. Anschließend können Sie auf dem virtuellen Computer eine MongoDB-Datenbank installieren und konfigurieren.


## Erstellen eines virtuellen Computers unter Windows Server

Befolgen Sie diese Anweisungen, um einen virtuellen Computer zu erstellen.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE]Sie können beim Erstellen des virtuellen Computers einen Endpunkt für MongoDB hinzufügen und wie folgt konfigurieren: Vergeben Sie den Namen **Mongo**, verwenden Sie **TCP** als Protokoll, und legen Sie die öffentlichen und privaten Ports auf **27017** fest.

## Datenträger anfügen
Fügen Sie einen Datenträger an den virtuellen Computer an, und initialisieren Sie ihn anschließend für die Verwendung durch Windows, damit ein entsprechender Speicher für den virtuellen Computer verfügbar ist. Sie können entweder einen vorhandenen Datenträger anfügen, wenn Sie bereits über zu verwendende Daten verfügen, oder Sie können einen leeren Datenträger anfügen.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Informationen zum Initialisieren des Datenträgers finden Sie in [Anfügen eines Datenträgers an einen virtuellen Windows-Computer](storage-windows-attach-disk.md) unter "Initialisieren eines neues Datenträgers unter Windows Server".

## Installieren und Ausführen von MongoDB auf dem virtuellen Computer

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

##Zusammenfassung
In diesem Lernprogramm haben Sie erfahren, wie Sie einen virtuellen Windows Server-Computer erstellen, eine Remoteverbindung dazu herstellen und anschließend einen Datenträger anfügen. Sie haben außerdem erfahren, wie Sie MongoDB auf dem Windows-basierten virtuellen Computer installieren und konfigurieren. Sie können nun von dem Windows-basierten virtuellen Computer aus auf MongoDB zugreifen, indem Sie die fortgeschrittenen Themen in der[MongoDB-Dokumentation][MongoDocs] befolgen.

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_1125_2015-->