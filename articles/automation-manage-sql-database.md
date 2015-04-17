<properties
	pageTitle="Verwalten von Azure SQL-Datenbanken mit Azure Automation"
	description="Erfahren Sie, wie der Azure Automation-Dienst zur angemessenen Verwaltung von Azure SQL-Datenbanken verwendet werden kann."
	services="sql-database, automation"
	documentationCenter=""
	authors="jodoglevy"
	manager="eamono"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="jolevy"/>



#Verwalten von Azure SQL-Datenbanken mit Azure Automation

Dieser Leitfaden bietet eine Einführung in den Azure Automation-Dienst und zeigt, wie Azure Automation zur Vereinfachung der Verwaltung Ihrer Azure SQL-Datenbanken genutzt werden kann.


## Was ist Azure Automation?

[Azure Automation](http://azure.microsoft.com/services/automation/) ist ein Azure-Dienst zum Vereinfachen der Cloudverwaltung über die Automatisierung von Prozessen. Mit Azure Automation können zeitintensive, manuelle, fehleranfällige und häufig ausgeführte Aufgaben automatisiert werden, um Zuverlässigkeit und Effizienz zu steigern und eine schnellere Amortisierung für Ihre Organisation zu erzielen.

Azure Automation bietet ein äußerst zuverlässiges und hochverfügbares Workflow-Ausführungsmodul, das nach Ihren Anforderungen skaliert werden kann, wenn Ihre Organisation wächst. In Azure Automation können Prozesse manuell, durch Drittanbietersysteme oder in geplanten Intervallen gestartet werden, sodass Aufgaben genau nach Bedarf ausgeführt werden.

Indem Sie die Aufgaben in Zusammenhang mit der Cloudverwaltung mit Azure Automation automatisieren, verringern Sie den Verwaltungsaufwand und ermöglichen es Ihren IT- und DevOps-Mitarbeitern, sich Aufgaben zu widmen, die einen Mehrwert für Ihr Unternehmen liefern.


## Inwiefern unterstützt Azure Automation die Verwaltung von Azure SQL-Datenbanken?

Azure SQL-Datenbanken können in Azure Automation durch die Verwendung von PowerShell-Cmdlets verwaltet werden, die in den [Azure PowerShell-Tools](https://msdn.microsoft.com/library/azure/jj156055.aspx) zur Verfügung stehen. In Azure Automation sind diese PowerShell-Cmdlets für Azure SQL-Datenbanken sofort einsatzfähig, sodass Sie alle Aufgaben in Zusammenhang mit der Verwaltung Ihrer SQL-Datenbanken innerhalb des Diensts ausführen können. Sie können diese Cmdlets in Azure Automation auch an die Cmdlets für andere Azure-Dienste koppeln, um komplexe Aufgaben über Azure-Dienste und Drittanbietersysteme hinweg zu automatisieren.

Azure Automation bietet außerdem die Möglichkeit, über die Ausgabe von SQL-Befehlen in PowerShell direkt mit SQL-Servern zu kommunizieren.


## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Azure Automation vertraut gemacht haben, folgen Sie diesen Links, um weitere Informationen zu Azure Automation zu erhalten.

Azure Automation: [Lernprogramm für den Einstieg](automation-create-runbook-from-samples.md)

<!--HONumber=49-->