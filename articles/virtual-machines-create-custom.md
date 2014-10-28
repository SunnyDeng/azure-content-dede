<properties linkid="manage-linux-howto-custom-create-vm" urlDisplayName="Create a custom VM" pageTitle=" Create a custom virtual machine running Linux in Azure" metaKeywords="Azure custom vm, creating custom vm" description="Learn how to create a custom virtual machine in Azure." metaCanonical="http://www.windowsazure.com/de-de/manage/windows/how-to-guides/custom-create-a-vm/" services="virtual-machines" documentationCenter="" title="" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Erstellen eines benutzerdefinierten virtuellen Computers

Ein *benutzerdefinierter* virtueller Computer ist ein virtueller Computer, den Sie mit der Option **Aus Katalog** erstellen, da Sie durch diese Option mehr Konfigurationsmöglichkeiten erhalten als durch die Option **Schnellerfassung**. Dazu gehören:

-   Mehr Imageoptionen zum Erstellen des virtuellen Computers (VM)
-   Verbinden des virtuellen Computers mit einem virtuellen Netzwerk
-   Installieren von VM-Agent und -Erweiterungen z. B. für Antischadsoftware
-   Hinzufügen des virtuellen Computers zu einem vorhandenen Cloud-Dienst
-   Hinzufügen des virtuellen Computers zu einer Verfügbarkeitsgruppe

**Wichtig**: Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, damit Sie sich direkt über den Hostnamen oder über eine lokal eingerichtete Verbindung mit ihm verbinden können, stellen Sie sicher, dass Sie das virtuelle Netzwerk schon dann angeben, wenn Sie den virtuellen Computer erstellen. Ein virtueller Computer kann so konfiguriert werden, dass er nur zu dem Zeitpunkt Teil eines virtuellen Netzwerks werden kann, zu dem er erstellt wird. Ausführliche Informationen über virtuelle Netzwerke erhalten Sie unter [Überblick über Azure Virtual Network][Überblick über Azure Virtual Network].

[WACOM.INCLUDE [virtual-machines-create-WindowsVM][virtual-machines-create-WindowsVM]]

  [Überblick über Azure Virtual Network]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [virtual-machines-create-WindowsVM]: ../includes/virtual-machines-create-WindowsVM.md
