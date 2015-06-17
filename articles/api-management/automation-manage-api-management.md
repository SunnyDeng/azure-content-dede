<properties
	pageTitle="Verwalten von Azure API Management mit Azure Automation"
	description="Erfahren Sie, wie der Azure Automation-Dienst zum Verwalten von Azure API Management verwendet werden kann."
	services="api-management, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2015"
	ms.author="csand"/>



#Verwalten von Azure API Management mit Azure Automation

Dieser Leitfaden bietet eine Einführung in den Azure Automation-Dienst und zeigt, wie er zur Vereinfachung der Verwaltung von Azure API Management genutzt werden kann.

## Was ist Azure Automation?

[Azure Automation](http://azure.microsoft.com/services/automation/) ist ein Azure-Dienst für die Vereinfachung der Cloudverwaltung durch eine Prozessautomatisierung. Mithilfe von Azure Automation können manuelle, häufig wiederholte, lang andauernde und fehleranfällige Aufgaben automatisiert werden, um die Zuverlässigkeit und Effizienz für Ihre Organisation zu erhöhen und die Amortisierungszeit zu verkürzen.

Azure Automation bietet ein äußerst zuverlässiges, hoch verfügbares Workflow-Ausführungsmodul, das sich durch Skalierung an Ihre Anforderungen anpasst. In Azure Automation können Prozesse manuell, von Systemen von Drittanbietern oder in geplanten Intervallen gestartet werden, damit Aufgaben genau im Bedarfsfall ausgeführt werden.

Indem Sie die Aufgaben im Zusammenhang mit der Cloudverwaltung mit Azure Automation automatisieren, verringern Sie den Verwaltungsaufwand und ermöglichen es Ihren IT- und DevOps-Mitarbeitern, sich Aufgaben zu widmen, die einen Mehrwert für Ihr Unternehmen liefern.


## Wie kann Azure Automation beim Verwalten von Azure API Management nützlich sein?

API Management kann in Azure Automation mithilfe der [API Management-REST-API](https://msdn.microsoft.com/library/azure/dn776326.aspx) verwaltet werden. Innerhalb von Azure Automation können Sie PowerShell-Workflowskripts schreiben, um viele Ihrer API Management-Aufgaben mithilfe der REST-API durchzuführen. Sie können diese REST-API-Aufrufe in Azure Automation auch mit den Cmdlets für andere Azure-Dienste koppeln, um komplexe Aufgaben in Azure-Diensten und Systemen anderer Hersteller zu automatisieren.


## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Azure Automation und dessen Einsatz zur Verwaltung von Azure API Management vertraut gemacht haben, folgen Sie diesen Links, um weitere Informationen zu erhalten.

* Weitere Informationen erhalten Sie im Lernprogramm [Erste Schritte](../automation-create-runbook-from-samples.md) zu Azure Automation.
* Lesen Sie den Community-Blogbeitrag [PowerShell Module for the \#Azure API Management REST APIs](https://alexandrebrisebois.wordpress.com/2014/08/17/powershell-module-for-the-azure-api-management-rest-apis/) .
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->