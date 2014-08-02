<properties linkid="manage-windows-common-task-mongodb-vm" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Windows Server virtual machine" metaKeywords="Azure vm, Azure MongoDB, Azure remote desktop" description="Learn how to create an Azure virtual machine with Windows Server 2008 R2, and then use Remote Desktop to install MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running Windows Server in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Installieren von MongoDB auf einem virtuellen Windows Server-Computer in Azure
==============================================================================

[MongoDB](http://www.mongodb.org/) ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank. Mit dem [Azure-Verwaltungsportal](http://manage.windowsazure.com) können Sie über die Bildergalerie einen virtuellen Windows Server-Computer erstellen. Anschließend können Sie auf dem virtuellen Computer eine MongoDB-Datenbank installieren und konfigurieren.

In diesem Lernprogramm lernen Sie Folgendes:

-   Verwenden des Verwaltungsportals zum Erstellen eines virtuellen Windows Server-Computers über die Galerie
-   Herstellen einer Verbindung zu einem virtuellen Computer mit Remotedesktop
-   Installieren von MongoDB auf dem virtuellen Computer

Erstellen eines virtuellen Computers mit Windows Server 2008 R2
---------------------------------------------------------------

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

Anfügen eines Datenträgers
--------------------------

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

Installieren und Ausführen von MongoDB auf dem virtuellen Computer
------------------------------------------------------------------

[WACOM.INCLUDE [install-and-run-mongo-on-win2k8-vm](../includes/install-and-run-mongo-on-win2k8-vm.md)]

Zusammenfassung
---------------

In diesem Lernprogramm haben Sie erfahren, wie Sie einen virtuellen Windows Server-Computer erstellen und eine Remoteverbindung dazu herstellen. Sie haben außerdem erfahren, wie Sie MongoDB auf dem virtuellen Windows-Computer installieren und konfigurieren. Weitere Informationen zu MongoDB finden Sie in der [MongoDB-Dokumentation](http://www.mongodb.org/display/DOCS/Home).

