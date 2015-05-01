<properties
	pageTitle="Verwalten von Azure Storage mithilfe von Azure Automation"
	description="Erfahren Sie, wie der Azure Automation-Dienst zur angemessenen Verwaltung von Azure Storage verwendet werden kann."
	services="storage, automation"
	documentationCenter=""
	authors="jodoglevy"
	manager="eamono"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="jolevy"/>



#Verwalten von Azure Storage mithilfe von Azure Automation

Dieser Leitfaden bietet eine Einführung in den Azure Automation-Dienst und zeigt, wie Azure Automation zur Vereinfachung der Verwaltung Ihrer Azure Storage-Blobs, -Tabellen und -Warteschlangen genutzt werden kann.


## Was ist Azure Automation?

[Azure Automation](http://azure.microsoft.com/services/automation/) ist ein Azure-Dienst zum Vereinfachen der Cloudverwaltung über die Automatisierung von Prozessen. Mit Azure Automation können zeitintensive, manuelle, fehleranfällige und häufig ausgeführte Aufgaben automatisiert werden, um Zuverlässigkeit und Effizienz zu steigern und eine schnellere Amortisierung für Ihre Organisation zu erzielen.

Azure Automation bietet ein äußerst zuverlässiges und hochverfügbares Workflow-Ausführungsmodul, das nach Ihren Anforderungen skaliert werden kann, wenn Ihre Organisation wächst. In Azure Automation können Prozesse manuell, durch Drittanbietersysteme oder in geplanten Intervallen gestartet werden, sodass Aufgaben genau nach Bedarf ausgeführt werden.

Indem Sie die Aufgaben in Zusammenhang mit der Cloudverwaltung mit Azure Automation automatisieren, verringern Sie den Verwaltungsaufwand und ermöglichen es Ihren IT- und DevOps-Mitarbeitern, sich Aufgaben zu widmen, die einen Mehrwert für Ihr Unternehmen liefern.


## Wie kann Azure Automation Sie beim Verwalten von Azure Storage unterstützen?

Azure Storage kann in Azure Automation über die PowerShell-Cmdlets verwaltet werden, die in den [Azure PowerShell-Tools](https://msdn.microsoft.com/library/azure/jj156055.aspx) zur Verfügung stehen. Diese PowerShell-Cmdlets für Azure Storage sind in Azure Automation integriert und sofort einsatzfähig, sodass Sie alle Aufgaben in Zusammenhang mit der Verwaltung von Blobs, Tabellen und Warteschlangen innerhalb des Diensts ausführen können. Sie können diese Cmdlets in Azure Automation auch an die Cmdlets für andere Azure-Dienste koppeln, um komplexe Aufgaben über Azure-Dienste und Drittanbietersysteme hinweg zu automatisieren.

Die [Azure Automation-Runbookgalerie](http://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) enthält eine Vielzahl von Produktteam- und Community-Runbooks für den Einstieg in die Automatisierung der Verwaltung von Azure Storage, anderen Azure-Diensten und Drittanbietersystemen. Zu den Galerierunbooks zählen:

 * [Entfernen von Azure Storage-Blobs, die älter sind als X Tage](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
 * [Herunterladen eines Blobs aus Azure Storage nach Azure Automation](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
 * [Erstellen von Kopien von Datenfestplatten virtueller Azure-Computer in einem Azure Cloud Service](https://gallery.technet.microsoft.com/scriptcenter/Make-copies-of-Azure-VM-065a6394)


## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Azure Automation und dessen Verwendung zum Verwalten von Azure Storage-Blobs, -Tabellen und -Warteschlangen vertraut gemacht haben, folgen Sie diesen Links, um weitere Informationen zu Azure Automation zu erhalten.

Azure Automation: [Lernprogramm für den Einstieg](automation-create-runbook-from-samples.md)


<!--HONumber=52-->