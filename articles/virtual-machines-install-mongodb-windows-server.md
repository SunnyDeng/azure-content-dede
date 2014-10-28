<properties linkid="manage-windows-common-task-mongodb-vm" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Windows Server virtual machine" metaKeywords="Azure vm, Azure MongoDB, Azure remote desktop" description="Learn how to install MongoDB on an Azure VM running Windows Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running Windows Server in Azure" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Installieren von MongoDB auf einem virtuellen Computer mit Windows Server

[MongoDB][MongoDB] ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank. Mit dem [Azure-Verwaltungsportal][Azure-Verwaltungsportal] können Sie über die Bildergalerie einen virtuellen Windows Server-Computer erstellen. Anschließend können Sie auf dem virtuellen Computer eine MongoDB-Datenbank installieren und konfigurieren.

In diesem Artikel wird Folgendes behandelt:

-   Verwenden des Verwaltungsportals zum Erstellen eines virtuellen Windows Server-Computers über die Galerie
-   Herstellen der Verbindung zum virtuellen Computer über Remotedesktop
-   Anfügen eines Datenträgers zum virtuellen Computer
-   Installieren von MongoDB auf dem virtuellen Computer

## Erstellen eines virtuellen Windows Server-Computers

Nachfolgend sind allgemeine Anweisungen aufgeführt, die Sie modifizieren können, indem Sie einen Endpunkt erstellen, damit remote auf MongoDB zugegriffen werden kann. (Sie können ihn auch später erstellen. Die entsprechende Beschreibung folgt auf die Anweisungen zum Installieren von MongoDB.) Fügen Sie auf der letzten Seite des Assistenten einen Endpunkt hinzu, und konfigurieren Sie ihn wie folgt:

-   Weisen Sie ihm den Namen **Mongo** zu.
-   Verwenden Sie **TCP** als Protokoll.
-   Legen Sie sowohl für die öffentlichen als auch für die privaten Ports den Wert **27017** fest.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM][virtual-machines-create-WindowsVM]]

## Datenträger anfügen

Fügen Sie einen Datenträger an den virtuellen Computer an, und initialisieren Sie ihn anschließend für die Verwendung durch Windows, damit ein entsprechender Speicher für den virtuellen Computer verfügbar ist. Sie können entweder einen vorhandenen Datenträger anfügen, wenn Sie bereits über Daten verfügen, die Sie verwenden möchten, oder Sie fügen einen leeren Datenträger hinzu.

[WACOM.INCLUDE [howto-attach-disk-windows-linux][howto-attach-disk-windows-linux]]

Anweisungen zum Initialisieren des Datenträgers finden Sie unter "Vorgehensweise: Initialisieren eines neuen Datenträgers in Windows Server" in [Anfügen eines Datenträgers an einen virtuellen Computer][Anfügen eines Datenträgers an einen virtuellen Computer].

## Installieren und Ausführen von MongoDB auf dem virtuellen Computer

[WACOM.INCLUDE [install-and-run-mongo-on-win2k8-vm][install-and-run-mongo-on-win2k8-vm]]

## Zusammenfassung

In diesem Lernprogramm haben Sie erfahren, wie Sie einen virtuellen Windows Server-Computer erstellen, eine Remoteverbindung dazu herstellen und anschließend einen Datenträger anfügen. Sie haben außerdem erfahren, wie Sie MongoDB auf dem virtuellen Windows-Computer installieren und konfigurieren. Weitere Informationen zu MongoDB finden Sie in der [MongoDB-Dokumentation][MongoDB-Dokumentation].

  [MongoDB]: http://www.mongodb.org/
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [virtual-machines-create-WindowsVM]: ../includes/virtual-machines-create-WindowsVM.md
  [howto-attach-disk-windows-linux]: ../includes/howto-attach-disk-windows-linux.md
  [Anfügen eines Datenträgers an einen virtuellen Computer]: http://azure.microsoft.com/de-de/documentation/articles/storage-windows-attach-disk/
  [install-and-run-mongo-on-win2k8-vm]: ../includes/install-and-run-mongo-on-win2k8-vm.md
  [MongoDB-Dokumentation]: http://www.mongodb.org/display/DOCS/Home
