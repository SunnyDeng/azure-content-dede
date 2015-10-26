<properties
	pageTitle="Verwalten von Azure SQL-Datenbanken mit Azure Automation"
	description="Erfahren Sie, wie der Azure Automation-Dienst zur angemessenen Verwaltung von Azure SQL-Datenbanken verwendet werden kann."
	services="sql-database, automation"
	documentationCenter=""
	authors="jodoglevy"
	manager="jeffreyg"
	editor="monicar"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="jolevy"/>



#Verwalten von Azure SQL-Datenbanken mit Azure Automation

Dieser Leitfaden bietet eine Einführung in den Azure Automation-Dienst und zeigt, wie Azure Automation zur Vereinfachung der Verwaltung Ihrer Azure SQL-Datenbanken genutzt werden kann.


## Was ist Azure Automation?

[Azure Automation](http://azure.microsoft.com/services/automation/) ist ein Azure-Dienst für die Vereinfachung der Cloudverwaltung durch eine Prozessautomatisierung. Mit Azure Automation können zeitintensive, manuelle, fehleranfällige und häufig ausgeführte Aufgaben automatisiert werden, um Zuverlässigkeit und Effizienz zu steigern und eine schnellere Amortisierung für Ihre Organisation zu erzielen.

Azure Automation bietet ein äußerst zuverlässiges und hochverfügbares Workflow-Ausführungsmodul, das nach Ihren Anforderungen skaliert werden kann, wenn Ihre Organisation wächst. In Azure Automation können Prozesse manuell, durch Drittanbietersysteme oder in geplanten Intervallen gestartet werden, sodass Aufgaben genau nach Bedarf ausgeführt werden.

Indem Sie die Aufgaben in Zusammenhang mit der Cloudverwaltung mit Azure Automation automatisieren, verringern Sie den Verwaltungsaufwand und ermöglichen es Ihren IT- und DevOps-Mitarbeitern, sich Aufgaben zu widmen, die einen Mehrwert für Ihr Unternehmen liefern.


## Inwiefern unterstützt Azure Automation die Verwaltung von Azure SQL-Datenbanken?

Azure SQL-Datenbank kann in Azure Automation durch die Verwendung von [PowerShell-Cmdlets für Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/dn546726.aspx) verwaltet werden, die in den [Azure PowerShell-Tools](https://msdn.microsoft.com/library/azure/jj156055.aspx) zur Verfügung stehen. In Azure Automation sind diese PowerShell-Cmdlets für Azure SQL-Datenbanken sofort einsatzfähig, sodass Sie alle Aufgaben in Zusammenhang mit der Verwaltung Ihrer SQL-Datenbanken innerhalb des Diensts ausführen können. Sie können diese Cmdlets in Azure Automation auch mit den Cmdlets für andere Azure-Dienste koppeln, um komplexe Aufgaben in Azure-Diensten und Systemen anderer Hersteller zu automatisieren.

Azure Automation bietet außerdem die Möglichkeit, über die Ausgabe von SQL-Befehlen in PowerShell direkt mit SQL-Servern zu kommunizieren.

Der [Azure Automation-Runbookkatalog](http://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) enthält eine Vielzahl von Runbooks vom Produktteam und der Community für den Einstieg in die Automatisierung der Verwaltung von Azure SQL-Datenbank, anderen Azure-Diensten und Drittanbietersystemen. Zu den Katalogrunbooks zählen:

 * [How to use a SQL Command in an Azure Automation Runbook](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2) (in englischer Sprache)
 * [Azure SQL Database: Vertically Scale](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f) (in englischer Sprache)
 * [Truncate table in SQL DB if DB is approaching max size](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b) (in englischer Sprache)
 * [Indexes tables in an Azure database if they have a high fragmentation](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea) (in englischer Sprache)

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Azure Automation vertraut gemacht haben, folgen Sie diesen Links, um weitere Informationen zu Azure Automation zu erhalten.

 * Lernprogramm [Erste Schritte](../automation-create-runbook-from-samples.md) zu Azure Automation
 * Lesen Sie den Blogbeitrag [Azure Automation: Your SQL Agent in the Cloud](http://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) (in englischer Sprache).
 

<!---HONumber=Oct15_HO3-->