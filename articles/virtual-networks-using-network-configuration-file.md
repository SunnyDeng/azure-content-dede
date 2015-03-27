<properties 
	pageTitle="Konfigurieren eines virtuellen Netzwerks mithilfe einer Netzwerkkonfigurationsdatei" 
	description="Anweisungen zum Exportieren und Importieren einer Netzwerkkonfigurationsdatei mithilfe des Azure-Verwaltungsportals zum Erstellen oder Ändern von virtuellen Netzwerken. " 
	services="virtual-network" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor="tysonn"/>

<tags
	ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services" 
	ms.date="02/18/2015"
	ms.author="cherylmc"/>

#Konfigurieren eines virtuellen Netzwerks mithilfe einer Netzwerkkonfigurationsdatei

## Übersicht

Wenn Sie ein virtuelles Netzwerk konfigurieren möchten, können Sie den Assistenten des Verwaltungsportals verwenden oder eine Netzwerkkonfigurationsdatei erstellen und importieren. Azure verwendet die Netzwerkkonfigurationsdatei zum Definieren der Einstellungen des virtuellen Netzwerks. 

Sie können den Assistenten des Verwaltungsportals für die Erstkonfiguration des virtuellen Netzwerks verwenden und später Änderungen daran vornehmen, indem Sie die Konfigurationsdatei direkt bearbeiten. Wenn Sie z. B. mehrere virtuelle Netzwerke für separate Abonnements konfigurieren, können Sie zuerst mithilfe des Assistenten des Verwaltungsportals eine Netzwerkkonfigurationsdatei erstellen. Anschließend können Sie die Datei exportieren, um sie als Vorlage zu verwenden oder verschiedene Einstellungen in ihr zu bearbeiten, und wieder in das Verwaltungsportal importieren. Dies kann eine effiziente Möglichkeit sein, mehrere virtuelle Netzwerke zu erstellen, wenn Sie mehr als ein Abonnement haben. 

Wenn Sie Änderungen an Ihren Netzwerkkonfigurationseinstellungen vornehmen möchten, bevor Sie Cloud-Dienste oder virtuelle Computer im Netzwerk bereitstellen, können Sie die Datei exportieren, bearbeiten und dann wieder in Azure importieren. Sie können eine Netzwerkkonfigurationsdatei auch verwenden, um Ihre Netzwerkkonfigurationseinstellungen zu sichern, wenn Sie ein virtuelles Netzwerk wiederherstellen möchten.

##Erstellen und Ändern einer Netzwerkkonfigurationsdatei 
Die einfachste Möglichkeit zum Erstellen einer Netzwerkkonfigurationsdatei ist der Export der Netzwerkeinstellungen aus einer vorhandenen Konfiguration eines virtuellen Netzwerks und die anschließende Bearbeitung der Datei, damit sie die Einstellungen enthält, die Sie für Ihre virtuellen Netzwerke konfigurieren möchten. Sie können auch eine Beispieldatei abrufen und ändern.

Wenn Sie die Netzwerkkonfigurationsdatei bearbeiten möchten, können Sie sie einfach öffnen, die notwendigen Änderungen vornehmen und diese speichern. Sie können einen beliebigen *xml*-Editor verwenden, um Änderungen an der Netzwerkkonfigurationsdatei vorzunehmen. 

Befolgen Sie die Anleitungen für Schemaeinstellungen zur Netzwerkkonfigurationsdatei. Wenn Sie Ihre Netzwerkkonfigurationsdatei erstellen, werden die Einstellungen für Ihr aktuelles Abonnement in Azure mit den Einstellungen in der Datei überschrieben. Wenn Sie Änderungen an Werten in der Datei vornehmen, die mit den Einstellungsrichtlinien nicht kompatibel sind, wird das virtuelle Netzwerk möglicherweise nicht auf die gewünschte Weise konfiguriert, und in einigen Fällen erlaubt Azure nicht, die Datei zu importieren. Informationen zu den in einer Netzwerkkonfigurationsdatei enthaltenen spezifischen Einstellungen finden Sie unter [Konfigurationsschema für Azure Virtual Network](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

Azure betrachtet ein Subnetz, in dem etwas bereitgestellt wurde, als "in Gebrauch". Wenn Sie ein Subnetz in Gebrauch ist, kann es nicht geändert werden. Verschieben Sie vor allen Änderungen alles, was Sie im Subnetz bereitgestellt haben, in ein anderes Subnetz, das nicht verändert wird.   Siehe [Verschieben eines virtuellen Computers oder einer Rolleninstanz in ein anderes Subnetz](https://msdn.microsoft.com/library/azure/dn643636.aspx).



## Exportieren und Importieren von Einstellungen eines virtuellen Netzwerks mithilfe des Verwaltungsportals  
Sie können Netzwerkkonfigurationseinstellungen, die in Ihrer Netzwerkkonfigurationsdatei enthalten sind, mithilfe von PowerShell oder des Verwaltungsportals importieren oder exportieren. Die folgenden Anweisungen helfen Ihnen beim Exportieren und Importieren mithilfe des Verwaltungsportals. 

### So exportieren Sie die Netzwerkeinstellungen
Beim Export werden alle Einstellungen für die virtuellen Netzwerke in Ihrem Abonnement in eine XML-Datei geschrieben. 

1. Melden Sie sich auf dem **Verwaltungsportal** an.
2. Klicken Sie im Verwaltungsportal am unteren Rand der Seite **Netzwerke** auf **Exportieren**. 
3. Überprüfen Sie im Fenster **Netzwerkkonfiguration exportieren**, ob Sie das Abonnement ausgewählt haben, für das Sie die Netzwerkeinstellungen exportieren möchten. Markieren Sie anschließend das Häkchen in der unteren rechten Ecke. 
4. Wenn Sie dazu aufgefordert werden, speichern Sie die Datei *NetworkConfig.xml* am Speicherort Ihrer Wahl.
### So importieren Sie die Netzwerkeinstellungen


1. Klicken Sie im Navigationsbereich des **Verwaltungsportals** unten links auf **Neu**.
2. Klicken Sie auf **Netzwerkdienste** -> **Virtuelles Netzwerk** -> **Konfiguration importieren**.
3. Wechseln Sie auf der Seite **Die Netzwerkkonfigurationsdatei importieren** zu Ihrer Netzwerkkonfigurationsdatei, und klicken Sie dann auf den Pfeil für **Weiter**.
4. Auf der Seite **Netzwerk wird erstellt** wird auf dem Bildschirm angezeigt, welche Teile Ihrer Netzwerkkonfiguration geändert oder erstellt werden. Wenn die Änderungen Ihnen richtig erscheinen, markieren Sie das Häkchen, um mit dem Aktualisieren oder Erstellen Ihres virtuellen Netzwerks fortzufahren. 


## Zusätzliche Ressourcen
Weitere Informationen zu virtuellen Netzwerken finden Sie unter:

-  [Virtuelle Netzwerke im Überblick](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)
-  [Netzwerkkonfigurationsschema - Optionale Einstellungen für Cloud-Dienste](https://msdn.microsoft.com/library/azure/jj156091.aspx)
-  [Einstellungen für virtuelle Netzwerke im Verwaltungsportal](https://msdn.microsoft.com/library/azure/jj156074.aspx)
-  [FAQs zu virtuellen Netzwerken](https://msdn.microsoft.com/library/azure/dn133803.aspx)
-  [Konfigurationsaufgaben für virtuelle Netzwerke](https://msdn.microsoft.com/library/azure/jj156206.aspx)
-  [Konfigurieren eines VPNs mit mehreren Standorten](https://msdn.microsoft.com/library/azure/dn690124.aspx)
-  [Konfigurieren einer VNet-zu-VNet-Verbindung](https://msdn.microsoft.com/library/azure/dn690122.aspx)




<!--HONumber=47-->
