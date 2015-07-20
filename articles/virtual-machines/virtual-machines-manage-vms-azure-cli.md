<properties
   pageTitle="Verwalten der virtuellen Azure-Computer mithilfe der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows | Microsoft Azure"
   description="Beschreibt das Erstellen, Verwalten und Löschen von virtuellen Azure-Computern mit der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="dlepow"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/09/2015"
   ms.author="danlep"/>

# Verwalten der virtuellen Computer mithilfe der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows

Viele täglich ausgeführte Aufgaben zur Verwaltung Ihrer virtuellen Computer können mithilfe der Azure-Befehlszeilenschnittstelle automatisiert werden. In diesem Artikel finden Sie Beispiele für Befehle für einfachere Aufgaben und Links zu Artikeln, die die Befehle für komplexere Aufgaben behandeln.

>[AZURE.NOTE]Wenn Sie die Azure-Befehlszeilenschnittstelle noch nicht installiert und konfiguriert haben, finden Sie [hier](../xplat-cli-install.md) Anweisungen dazu. Eine Schnellstartanleitung für die gleichen Aufgaben in PowerShell finden Sie unter [Verwalten Ihrer VMs mit Azure PowerShell](virtual-machines-manage-vms-powershell.md).

## So verwenden Sie die Beispielbefehle
Sie müssen einen Teil des Texts in den Befehlen durch Text ersetzen, der für Ihre Umgebung geeignet ist. Die < and >-Symbole zeigen Text an, der ersetzt werden muss. Wenn Sie den Text ersetzen, entfernen Sie die Symbole, behalten jedoch die Anführungszeichen bei.

> [AZURE.NOTE]Wenn Sie die Ausgaben der Konsolenbefehle programmgesteuert speichern und bearbeiten möchten, sollten Sie möglicherweise ein JSON-Analysetool verwenden, z. B. **[jq](https://github.com/stedolan/jq)**, **[jsawk](https://github.com/micha/jsawk)** oder für die Aufgabe geeignete Sprachbibliotheken.

## Anzeigen von Informationen über einen virtuellen Computer

Dies ist eine einfache Aufgabe, die Sie häufig verwenden. Verwenden Sie sie zum Abrufen von Informationen zu einem virtuellen Computer, Ausführen von Aufgaben auf diesem oder Abrufen von Ausgaben zur Speicherung in einer Variablen.

Um Informationen über den virtuellen Computer zu erhalten, führen Sie diesen Befehl aus und ersetzen allen Text in Anführungszeichen einschließlich der < and >-Zeichen:

     azure vm show -g <group name> -n <virtual machine name>

Führen Sie zum Speichern der Ausgabe in einer $vm-Variable als JSON-Dokument folgenden Code aus:

    vmInfo=$(azure vm show -g <group name> -n <virtual machine name> --json)

Sie können die stdout-Ausgabe auch in eine Datei umleiten.

## Anmelden bei einem virtuellen Computer auf Linux-Basis

In der Regel sind Linux-Computer über SSH verbunden. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux auf Azure](virtual-machines-linux-use-ssh-key.md). Übersicht über Azure Resource Manager
## Anhalten eines virtuellen Computers

Führen Sie den folgenden Befehl aus:

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT]Verwenden Sie diesen Parameter, um die virtuelle IP-Adresse des Clouddiensts beizubehalten, falls es sich um den letzten virtuellen Computer in diesem Clouddienst handelt. <br><br> Wenn Sie diesen Parameter verwenden, wird der virtuelle Computer dennoch in Rechnung gestellt.

## Starten eines virtuellen Computers

Führen Sie diesen Befehl aus: Azure Resource Manager Overview azure vm start <group name> <virtual machine name>

## Anfügen eines Datenträgers

Sie müssen auch entscheiden, ob Sie einen neuen Datenträger anfügen oder einen, der Daten enthält. Für einen neuen Datenträger erstellt der Befehl die VHD-Datei und fügt sie im selben Befehl an.

Um einen neuen Datenträger anzufügen, führen Sie den folgenden Befehl aus:

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb>

Um einen vorhandenen Datenträger anzufügen, führen Sie den folgenden Befehl aus:

    azure vm disk attach <resource-group> <vm-name> [vhd-url]

## Erstellen eines virtuellen Linux-Computers

Um einen neuen virtuellen Linux-basierten Computer zu erstellen, benötigen Sie mehrere Werte wie einen Ressourcengruppennamen, einen Speicherort, einen Imagenamen, einen Namen für den virtuellen Computer und ein Speicherkonto zum Speichern des zugrunde liegenden VHD-Images. Wenn Sie alle benötigten Informationen haben, die Sie verwenden möchten, kann über die folgende Eingabe in der Azure-Befehlszeilenschnittstelle eine interaktive Sitzung erstellt werden, in der Sie zur Eingabe dieser Werte aufgefordert werden:

    azure vm create

Wenn Sie diese Werte bereits kennen, finden Sie die entsprechenden Schalter zur direkten Übergabe der Werte, indem Sie Folgendes eingeben: `azure help vm create`.

## Nächste Schritte

Viele weitere Verwendungsbeispiele für die Azure-Befehlszeilenschnittstelle mit dem **arm**-Modus finden Sie unter [Verwenden der Microsoft Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit Azure Resource Manager](../xplat-cli-resource-manager.md). Weitere Informationen über die Konzepte und Azure-Ressourcen erhalten Sie unter [Übersicht über Azure Resource Manager](../resource-group-overview.md).
 

<!---HONumber=July15_HO2-->