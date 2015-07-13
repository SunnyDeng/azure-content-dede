<properties
	pageTitle="Installieren von MySQL auf einem virtuellen Computer mit OpenSUSE Linux in Azure"
	description="Erfahren Sie mehr über die Installation von MySQL auf einem virtuellen Computer in Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="mysql"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/22/2015"
	ms.author="kathydav"/>

# Installieren von MySQL auf einem virtuellen Computer mit OpenSUSE Linux in Azure

[MySQL][MySQL] ist eine beliebte Open-Source-SQL-Datenbank. In diesem Lernprogramm wird Folgendes erläutert:

- Verwenden des [Azure-Verwaltungsportals][AzurePortal] zum Erstellen eines virtuellen Computers mit OpenSUSE Linux von einem über Azure verfügbaren Image
- Gewusst wie: Verbinden des virtuellen Computers über SSH oder PuTTY
- Gewusst wie: Installieren von MySQL auf dem virtuellen Computer

[AZURE.INCLUDE [antares-iaas-signup-iaas](../../includes/antares-iaas-signup-iaas.md)]

## Erstellen eines virtuellen Computers mit OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

##Installieren und Ausführen von MySQL auf dem virtuellen Computer

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

##Zusammenfassung
In diesem Lernprogramm haben Sie gelernt, einen virtuellen Computer mit OpenSUSE Linux zu erstellen und mit SSH oder PuTTY eine Remote-Verbindung dazu herzustellen. Sie haben außerdem erfahren, wie Sie MySQL auf dem virtuellen Linux-Computer installieren und konfigurieren. Weitere Informationen zu MySQL finden Sie in der [MySQL-Dokumentation][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com
 

<!---HONumber=July15_HO1-->