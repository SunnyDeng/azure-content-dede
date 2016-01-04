<properties 
	pageTitle="Konfigurieren eines virtuellen Netzwerks mithilfe einer Netzwerkkonfigurationsdatei" 
	description="Anweisungen zum Exportieren und Importieren einer Netzwerkkonfigurationsdatei mithilfe des Azure-Verwaltungsportals zum Erstellen oder Ändern von virtuellen Netzwerken." 
	services="virtual-network" 
	documentationCenter="" 
	authors="telmosampaio" 
	manager="carmonm" 
	editor="tysonn"/>

<tags
	ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services" 
	ms.date="12/07/2015"
	ms.author="telmos"/>

# Konfigurieren eines virtuellen Netzwerks mithilfe einer Netzwerkkonfigurationsdatei

Sie können ein virtuelles Netzwerk (VNet) mithilfe des Azure-Verwaltungsportals oder einer Netzwerkkonfigurationsdatei konfigurieren.

## Erstellen und Ändern einer Netzwerkkonfigurationsdatei 
Die einfachste Möglichkeit zum Erstellen einer Netzwerkkonfigurationsdatei ist der Export der Netzwerkeinstellungen aus einer vorhandenen Konfiguration eines virtuellen Netzwerks und die anschließende Bearbeitung der Datei, damit sie die Einstellungen enthält, die Sie für Ihre virtuellen Netzwerke konfigurieren möchten.

Wenn Sie die Netzwerkkonfigurationsdatei bearbeiten möchten, können Sie sie einfach öffnen, die notwendigen Änderungen vornehmen und die Datei speichern. Sie können einen beliebigen *XML*-Editor verwenden, um Änderungen an der Netzwerkkonfigurationsdatei vorzunehmen.

Befolgen Sie die Anleitungen zu [Schemaeinstellungen zur Netzwerkkonfigurationsdatei](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Azure betrachtet ein Subnetz, in dem etwas bereitgestellt wurde, als **in Gebrauch**. Wenn Sie ein Subnetz in Gebrauch ist, kann es nicht geändert werden. Verschieben Sie vor allen Änderungen alles, was Sie im Subnetz bereitgestellt haben, in ein anderes Subnetz, das nicht verändert wird. Siehe [Verschieben eines virtuellen Computers oder einer Rolleninstanz in ein anderes Subnetz](virtual-networks-move-vm-role-to-subnet.md).

## Exportieren und Importieren von Einstellungen eines virtuellen Netzwerks mithilfe des Verwaltungsportals  
Sie können Netzwerkkonfigurationseinstellungen, die in Ihrer Netzwerkkonfigurationsdatei enthalten sind, mithilfe von PowerShell oder des Verwaltungsportals importieren oder exportieren. Die folgenden Anweisungen helfen Ihnen beim Exportieren und Importieren mithilfe des Verwaltungsportals.

### So exportieren Sie die Netzwerkeinstellungen
Beim Export werden alle Einstellungen für die virtuellen Netzwerke in Ihrem Abonnement in eine XML-Datei geschrieben.

1. Melden Sie sich beim **Verwaltungsportal** an.
2. Klicken Sie im Verwaltungsportal am unteren Rand der Seite **Netzwerke** auf **Exportieren**. 
3. Überprüfen Sie im Fenster **Netzwerkkonfiguration exportieren**, ob Sie das Abonnement ausgewählt haben, für das Sie die Netzwerkeinstellungen exportieren möchten. Markieren Sie anschließend das Häkchen in der unteren rechten Ecke. 
4. Wenn Sie dazu aufgefordert werden, speichern Sie die Datei *NetworkConfig.xml* am gewünschten Speicherort.


### So importieren Sie die Netzwerkeinstellungen

1. Klicken Sie im Navigationsbereich im **Verwaltungsportal** unten links auf **Neu**.
2. Klicken Sie auf **Network Services** -> **Virtuelles Netzwerk** -> **Konfiguration importieren**.
3. Wechseln Sie auf der Seite **Die Netzwerkkonfigurationsdatei importieren** zu Ihrer Netzwerkkonfigurationsdatei, und klicken Sie dann auf den Pfeil für **Weiter**.
4. Auf der Seite **Netzwerk wird erstellt** wird auf dem Bildschirm angezeigt, welche Teile Ihrer Netzwerkkonfiguration geändert oder erstellt werden. Wenn die Änderungen Ihnen richtig erscheinen, markieren Sie das Häkchen, um mit dem Aktualisieren oder Erstellen Ihres virtuellen Netzwerks fortzufahren. 

<!---HONumber=AcomDC_1210_2015-->