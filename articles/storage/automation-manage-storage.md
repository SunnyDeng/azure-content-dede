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
	ms.date="02/20/2016"
	ms.author="jolevy"/>



#Verwalten von Azure Storage mithilfe von Azure Automation

Dieser Leitfaden bietet eine Einführung in den Azure Automation-Dienst und zeigt, wie Azure Automation zur Vereinfachung der Verwaltung Ihrer Azure Storage-Blobs, -Tabellen und -Warteschlangen genutzt werden kann.


## Was ist Azure Automation?

[Azure Automation](https://azure.microsoft.com/services/automation/) ist ein Azure-Dienst für die Vereinfachung der Cloudverwaltung durch eine Prozessautomatisierung. Mit Azure Automation können zeitintensive, manuelle, fehleranfällige und häufig ausgeführte Aufgaben automatisiert werden, um Zuverlässigkeit und Effizienz zu steigern und eine schnellere Amortisierung für Ihre Organisation zu erzielen.

Azure Automation bietet ein äußerst zuverlässiges und hochverfügbares Workflow-Ausführungsmodul, das nach Ihren Anforderungen skaliert werden kann, wenn Ihre Organisation wächst. In Azure Automation können Prozesse manuell, durch Drittanbietersysteme oder in geplanten Intervallen gestartet werden, sodass Aufgaben genau nach Bedarf ausgeführt werden.

Indem Sie die Aufgaben in Zusammenhang mit der Cloudverwaltung mit Azure Automation automatisieren, verringern Sie den Verwaltungsaufwand und ermöglichen es Ihren IT- und DevOps-Mitarbeitern, sich Aufgaben zu widmen, die einen Mehrwert für Ihr Unternehmen liefern.


## Wie kann Azure Automation Sie beim Verwalten von Azure Storage unterstützen?

Azure Storage kann in Azure Automation mit den PowerShell-Cmdlets verwaltet werden, die in [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx) zur Verfügung stehen. Diese PowerShell-Cmdlets für Azure Storage sind in Azure Automation integriert und sofort einsatzfähig, sodass Sie alle Aufgaben in Zusammenhang mit der Verwaltung von Blobs, Tabellen und Warteschlangen innerhalb des Diensts ausführen können. Sie können diese Cmdlets in Azure Automation auch mit den Cmdlets für andere Azure-Dienste koppeln, um komplexe Aufgaben in Azure-Diensten und Systemen anderer Hersteller zu automatisieren.

Der [Azure Automation-Runbookkatalog](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) enthält eine Vielzahl von Runbooks vom Produktteam und der Community für den Einstieg in die Automatisierung der Verwaltung von Azure Storage, anderen Azure-Diensten und Drittanbietersystemen. Zu den Katalogrunbooks zählen:

 * [Remove Azure Storage Blobs that are Certain Days Old Using Automation Service](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761) (Entfernen von Azure Storage-Blobs, die eine bestimmte Anzahl an Tagen alt sind, mit dem Automation-Dienst)
 * [Download a Blob from Azure Storage](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745) (Herunterladen eines Blobs aus Azure Storage)
 * [Backup all disks for a single Azure VM or for all VMs in a Cloud Service](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5) (Sichern aller Datenträger eines einzelnen virtuellen Azure-Computers oder aller virtuellen Computer in einem Clouddienst)


## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Azure Automation und dessen Verwendung zum Verwalten von Azure Storage-Blobs, -Tabellen und -Warteschlangen vertraut gemacht haben, folgen Sie diesen Links, um weitere Informationen zu Azure Automation zu erhalten.

Azure Automation-Tutorial [Erstellen oder Importieren eines Runbooks in Azure Automation](../automation/automation-creating-importing-runbook.md).
 

<!---HONumber=AcomDC_0224_2016-->