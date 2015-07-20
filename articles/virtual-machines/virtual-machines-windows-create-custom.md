<properties 
	pageTitle="Erstellen eines benutzerdefinierten virtuellen Computers mit Windows in Azure" 
	description="Erfahren Sie, wie Sie in Azure einen benutzerdefinierten virtuellen Computer erstellen, auf dem Windows ausgeführt wird." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>


<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="kathydav"/>

#Erstellen eines benutzerdefinierten virtuellen Computers mit Windows

Ein *benutzerdefinierter* virtueller Computer ist lediglich ein virtueller Computer, den Sie mit der Option **Aus Katalog** erstellen, da Sie durch diese Option mehr Konfigurationsmöglichkeiten erhalten als durch die Option **Schnellerfassung**. Zu diesen Möglichkeiten gehören:

- Verbinden des virtuellen Computers mit einem virtuellen Netzwerk
- Installieren von VM-Agent und -Erweiterungen z. B. für Antischadsoftware 
- Hinzufügen des virtuellen Computers zu einem vorhandenen Cloud-Dienst
- Hinzufügen des virtuellen Computers zu einem vorhandenen Speicherkonto
- Hinzufügen des virtuellen Computers zu einer Verfügbarkeitsgruppe

> [AZURE.IMPORTANT]Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, damit Sie sich direkt über den Hostnamen oder über eine lokal eingerichtete Verbindung mit ihm verbinden können, stellen Sie sicher, dass Sie das virtuelle Netzwerk schon dann angeben, wenn Sie den virtuellen Computer erstellen. Ein virtueller Computer kann so konfiguriert werden, dass er nur zu dem Zeitpunkt Teil eines virtuellen Netzwerks werden kann, zu dem er erstellt wird. Ausführliche Informationen über virtuelle Netzwerke erhalten Sie unter [Überblick über Azure Virtual Network](http://go.microsoft.com/fwlink/p/?LinkID=294063).

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]


 

<!---HONumber=July15_HO2-->