<properties linkid="manage-linux-common-task-mysql-virtual-machine" urlDisplayName="Install MySQL" pageTitle="Install MySQL on a Linux virtual machine in Azure" metaKeywords="Azure vm OpenSUSE, Linux vm" description="Learn how to create an Azure virtual machine with OpenSUSE Linux, and then use SSH or PuTTY to install MySQL." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running OpenSUSE Linux in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Installieren von MySQL auf einem virtuellen Computer mit OpenSUSE Linux in Azure

[MySQL][MySQL] ist eine beliebte Open-Source-SQL-Datenbank. Mit dem [Azure-Verwaltungsportal][Azure-Verwaltungsportal] können Sie über die Bildergalerie einen virtuellen Computer mit OpenSUSE Linux erstellen. Anschließend können Sie auf dem virtuellen Computer eine MySQL-Datenbank installieren und konfigurieren.

In diesem Lernprogramm lernen Sie Folgendes:

-   Verwenden des Verwaltungsportals zum Erstellen eines virtuellen Computers mit OpenSUSE Linux über die Galerie

-   Verbinden des virtuellen Computers über SSH oder PuTTY

-   Installieren von MySQL auf dem virtuellen Computer

## Erstellen eines virtuellen Computers mit OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Installieren und Ausführen von MySQL auf dem virtuellen Computer

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Zusammenfassung

In diesem Lernprogramm haben Sie erfahren, wie Sie einen virtuellen OpenSUSE Linux-Computer erstellen und eine Remoteverbindung über SSH oder PuTTY herstellen. Sie haben außerdem erfahren, wie Sie MySQL auf dem virtuellen Linux-Computer installieren und konfigurieren. Weitere Informationen zu MySQL finden Sie in der [MySQL-Dokumentation][MySQL-Dokumentation].

  [MySQL]: http://www.mysql.com
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [create-and-configure-opensuse-vm-in-portal]: ../includes/create-and-configure-opensuse-vm-in-portal.md
  [install-and-run-mysql-on-opensuse-vm]: ../includes/install-and-run-mysql-on-opensuse-vm.md
  [MySQL-Dokumentation]: http://dev.mysql.com/doc/
