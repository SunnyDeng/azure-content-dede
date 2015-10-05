<properties
	pageTitle="Installieren von MySQL auf einem virtuellen OpenSUSE Linux-Computer in Microsoft Azure"
	description="Erfahren Sie mehr über die Installation von MySQL auf einem virtuellen Computer in Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="cynthn"/>

# Installieren von MySQL auf einem virtuellen Computer mit OpenSUSE Linux in Azure

[MySQL][MySQL] ist eine beliebte Open-Source-SQL-Datenbank. In diesem Tutorial erfahren Sie, wie Sie einen virtuellen Computer mit OpenSUSE Linux erstellen und anschließend MySQL installieren.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Erstellen eines virtuellen Computers mit OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Installieren und Ausführen von MySQL auf dem virtuellen Computer

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Nächste Schritte
Einzelheiten zu MySQL finden Sie in der [MySQL-Dokumentation][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com

<!---HONumber=Sept15_HO4-->