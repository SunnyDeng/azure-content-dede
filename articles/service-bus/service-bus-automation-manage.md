<properties
	pageTitle="Verwalten von Azure Service Bus mithilfe von Azure Automation"
	description="Erfahren Sie, wie der Azure Automation-Dienst zum Verwalten von Azure Service Bus verwendet werden kann."
	services="service-bus, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="core"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/13/2015"
	ms.author="csand"/>



#Verwalten von Azure Service Bus mithilfe von Azure Automation

Dieser Leitfaden bietet eine Einführung in den Azure Automation-Dienst und zeigt, wie Azure Automation zur Vereinfachung der Verwaltung von Azure Service Bus genutzt werden kann.

## Was ist Azure Automation?

[Azure Automation](http://azure.microsoft.com/services/automation/) ist ein Azure-Dienst für die Vereinfachung der Cloudverwaltung durch eine Prozessautomatisierung. Mithilfe von Azure Automation können manuelle, häufig wiederholte, lang andauernde und fehleranfällige Aufgaben automatisiert werden, um die Zuverlässigkeit und Effizienz für Ihre Organisation zu erhöhen und die Amortisierungszeit zu verkürzen.

Azure Automation bietet ein äußerst zuverlässiges, hoch verfügbares Workflow-Ausführungsmodul, das sich durch Skalierung an Ihre Anforderungen anpasst. In Azure Automation können Prozesse manuell, von Systemen von Drittanbietern oder in geplanten Intervallen gestartet werden, damit Aufgaben genau im Bedarfsfall ausgeführt werden.

Indem Sie die Aufgaben im Zusammenhang mit der Cloudverwaltung mit Azure Automation automatisieren, verringern Sie den Verwaltungsaufwand und ermöglichen es Ihren IT- und DevOps-Mitarbeitern, sich Aufgaben zu widmen, die einen Mehrwert für Ihr Unternehmen liefern.


## Wie kann Azure Automation Sie beim Verwalten von Azure Service Bus unterstützen?

Service Bus kann in Azure Automation mithilfe der [Service Bus-REST-API](https://msdn.microsoft.com/library/azure/hh780717.aspx) verwaltet werden. Innerhalb von Azure Automation können Sie PowerShell-Workflowskripts schreiben, um viele Ihrer Service Bus-Aufgaben mithilfe der REST-API durchzuführen. Sie können diese REST-API-Aufrufe in Azure Automation auch mit den Cmdlets für andere Azure-Dienste koppeln, um komplexe Aufgaben in Azure-Diensten und Systemen anderer Hersteller zu automatisieren.


## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Azure Automation und dessen Verwendung zum Verwalten von Azure Service Bus vertraut gemacht haben, folgen Sie diesen Links, um weitere Informationen zu Azure Automation zu erhalten.

* Lernprogramm [Erste Schritte](../automation-create-runbook-from-samples.md) zu Azure Automation
* Artikel [Verwalten von Service Bus mit PowerShell](service-bus-powershell-how-to-provision.md)
 

<!---HONumber=July15_HO4-->