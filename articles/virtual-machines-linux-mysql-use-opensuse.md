<properties linkid="virtual-machines-linux-mysql-use-opensuse" urlDisplayName="Install MongoDB" pageTitle="nstall MongoDB on a virtual machine running CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Learn how to install Mongo DB on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Installieren von MySQL auf einem virtuellen Computer mit OpenSUSE Linux in Azure

[MySQL][MySQL] ist eine beliebte Open-Source-SQL-Datenbank. Mit dem [Azure-Verwaltungsportal][Azure-Verwaltungsportal] können Sie über die Bildergalerie einen virtuellen Computer mit OpenSUSE Linux erstellen. Anschließend können Sie auf dem virtuellen Computer eine MySQL-Datenbank installieren und konfigurieren.

In diesem Lernprogramm lernen Sie Folgendes:

-   Verwenden des Verwaltungsportals zum Erstellen eines virtuellen Computers mit OpenSUSE Linux über die Galerie
-   Verbinden des virtuellen Computers über SSH oder PuTTY
-   Installieren von MySQL auf dem virtuellen Computer

## Erstellen eines Azure-Abonnements

Falls Sie noch nicht über ein Azure-Abonnement verfügen, können Sie sich [kostenlos][kostenlos] anmelden. Führen Sie nach der Anmeldung die folgenden Schritte aus, um das Lernprogramm fortzusetzen.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## Erstellen eines virtuellen Computers mit OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Installieren und Ausführen von MySQL auf dem virtuellen Computer

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Zusammenfassung

In diesem Lernprogramm haben Sie gelernt, wie Sie einen virtuellen Computer mit OpenSUSE Linux erstellen und mit SSH oder PuTTY eine Remote-Verbindung dazu herstellen. Sie haben außerdem erfahren, wie Sie MySQL auf dem virtuellen Linux-Computer installieren und konfigurieren. Weitere Informationen zu MySQL finden Sie in der [MySQL-Dokumentation][MySQL-Dokumentation].

  [MySQL]: http://www.mysql.com
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [kostenlos]: http://azure.microsoft.com
  [antares-iaas-signup-iaas]: ../includes/antares-iaas-signup-iaas.md
  [create-and-configure-opensuse-vm-in-portal]: ../includes/create-and-configure-opensuse-vm-in-portal.md
  [install-and-run-mysql-on-opensuse-vm]: ../includes/install-and-run-mysql-on-opensuse-vm.md
  [MySQL-Dokumentation]: http://dev.mysql.com/doc/
