<properties urlDisplayName="Install MongoDB" pageTitle="Installieren von MongoDB auf einem virtuellen Computer unter Windows Server" metaKeywords="Azure vm, Azure MongoDB, Azure remote desktop" description="Learn how to install MongoDB on an Azure VM running Windows Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running Windows Server in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="kathydav" />

#Installieren von MongoDB auf einem virtuellen Computer unter Windows Server

[MongoDB][MongoDB] ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank.  Mit dem [Azure-Verwaltungsportal][AzureManagementPortal] können Sie über die Bildergalerie einen virtuellen Windows Server-Computer erstellen.  Anschließend können Sie auf dem virtuellen Computer eine MongoDB-Datenbank installieren und konfigurieren.

In diesem Artikel wird Folgendes erläutert:

- Verwenden des Verwaltungsportals zum Erstellen eines virtuellen Computers unter Windows Server über die Galerie
- Stellen Sie per Remotedesktop eine Verbindung zum virtuellen Computer her
- Anfügen eines Datenträgers an einen virtuellen Computer
- Installieren von MongoDB auf dem virtuellen Computer

## Erstellen eines virtuellen Computers unter Windows Server

Im Folgenden finden Sie allgemeine Anweisungen. Sie können sie ändern, indem Sie einen Endpunkt erstellen, um den Remotezugriff auf MongoDB zuzulassen. (Sie können ihn auch später erstellen. Eine Beschreibung findet sich im Anschluss an die Anweisungen für das Installieren von MongoDB.) Fügen Sie auf der letzten Seite des Assistenten einen Endpunkt hinzu, und konfigurieren Sie ihn wie folgt:

- Benennen Sie ihn mit **Mongo**
- Verwenden Sie **TCP** als Protokoll
- Legen Sie die öffentlichen und privaten Ports auf **27017** fest.
 
[WACOM.INCLUDE [virtuelle-Computer-WindowsVM-erstellen](../includes/virtual-machines-create-WindowsVM.md)]

## Datenträger anfügen
Fügen Sie zum Bereitstellen eines Speichers für den virtuellen Computer einen Datenträger an, und initialisieren Sie ihn anschließend, damit er durch Windows verwendet werden kann. Sie können entweder einen vorhandenen Datenträger anfügen, wenn Sie bereits über zu verwendende Daten verfügen, oder Sie können einen leeren Datenträger anfügen.

[WACOM.INCLUDE [vorgehensweise-festplatte-anhaengen-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

Anweisungen in Bezug auf die Initialisierung des Datenträgers finden Sie unter "Gewusst wie: Initialisieren eines neues Datenträgers unter Windows Server" in [Gewusst wie: Anfügen eines Datenträgers an einen virtuellen Windows-Computer](http://azure.microsoft.com/de-de/documentation/articles/storage-windows-attach-disk/).

## Installieren und Ausführen von MongoDB auf dem virtuellen Computer 

[WACOM.INCLUDE [mongo-auf-win2k8-vm-installieren-und-ausfuehren](../includes/install-and-run-mongo-on-win2k8-vm.md)]

##Zusammenfassung
In diesem Lernprogramm haben Sie erfahren, wie Sie einen virtuellen Windows Server-Computer erstellen, eine Remoteverbindung dazu herstellen und einen Datenträger anfügen.  Sie haben außerdem erfahren, wie Sie MongoDB auf dem virtuellen Windows-Computer installieren und konfigurieren. Weitere Informationen zu MongoDB finden Sie in der [MongoDB-Dokumentation][MongoDocs].

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzureManagementPortal]: http://manage.windowsazure.com

<!--HONumber=35_1-->
