<properties linkid="manage-linux-common-task-mongodb-virtual-machine" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Linux virtual machine in Azure" metaKeywords="Azure vm CentOS, Azure vm Linux, Linux vm, Linux MongoDB" description="Learn how to create an Azure virtual machine with CentOS Linux, and then use SSH or PuTTY to install MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Installieren von MongoDB auf einem virtuellen Computer, auf dem CentOS Linux in Azure ausgeführt wird

[MongoDB][MongoDB] ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank. Im Azure-Verwaltungsportal können Sie einen virtuellen Computer erstellen, auf dem CentOS Linux aus der Image-Galerie ausgeführt wird. Anschließend können Sie auf dem virtuellen Computer eine MongoDB-Datenbank installieren und konfigurieren.

Sie erhalten Informationen zu folgenden Themen:

-   Verwenden des Verwaltungsportals zum Auswählen und Installieren eines virtuellen Linux-Computers, auf dem CentOS Linux aus der Galerie ausgeführt wird.

-   Verbinden des virtuellen Computers über SSH oder PuTTY
-   Installieren von MongoDB auf dem virtuellen Computer

## Erstellen eines virtuellen Computers, auf dem CentOS Linux ausgeführt wird

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

## Datenträger anfügen

[WACOM.INCLUDE [attach-data-disk-centos-vm-in-portal](../includes/attach-data-disk-centos-vm-in-portal.md)]

## Installieren und Ausführen von MongoDB auf dem virtuellen Computer

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

## Zusammenfassung

In diesem Lernprogramm haben Sie erfahren, wie Sie einen virtuellen Linux-Computer erstellen und eine Remoteverbindung über SSH oder PuTTY herstellen. Sie haben auch erfahren, wie Sie MongoDB auf dem virtuellen Linux-Computer installieren und konfigurieren. Weitere Informationen zu MongoDB finden Sie in der [MongoDB-Dokumentation][MongoDB-Dokumentation].

  [MongoDB]: http://www.mongodb.org/
  [MongoDB-Dokumentation]: http://www.mongodb.org/display/DOCS/Home
