<properties
	pageTitle="Verwalten des Azure-Schlüsseltresors mit Azure Automation"
	description="Erfahren Sie, wie der Azure Automation-Dienst zum Verwalten des Azure-Schlüsseltresors verwendet werden kann."
	services="Key-Vault, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2015"
	ms.author="csand"/>



#Verwalten des Azure-Schlüsseltresors mit Azure Automation

Dieser Leitfaden bietet eine Einführung in den Azure Automation-Dienst und zeigt, wie Azure Automation zur einfacheren Verwaltung Ihrer Schlüssel und geheimen Schlüssel im Azure-Schlüsseltresor verwendet werden kann.

## Was ist Azure Automation?

[Azure Automation](http://azure.microsoft.com/services/automation/) ist ein Azure-Dienst für die Vereinfachung der Cloudverwaltung durch Prozessautomatisierung. Mithilfe von Azure Automation können manuelle, häufig wiederholte, lang andauernde und fehleranfällige Aufgaben automatisiert werden, um die Zuverlässigkeit und Effizienz für Ihre Organisation zu erhöhen und die Amortisierungszeit zu verkürzen.

Azure Automation bietet ein äußerst zuverlässiges, hoch verfügbares Workflowausführungsmodul, das sich durch Skalierung an Ihre Anforderungen anpasst. In Azure Automation können Prozesse manuell, von Systemen von Drittanbietern oder in geplanten Intervallen gestartet werden, damit Aufgaben genau im Bedarfsfall ausgeführt werden.

Indem Sie die Aufgaben im Zusammenhang mit der Cloudverwaltung mit Azure Automation automatisieren, verringern Sie den Verwaltungsaufwand und ermöglichen es Ihren IT- und DevOps-Mitarbeitern, sich Aufgaben zu widmen, die einen Mehrwert für Ihr Unternehmen liefern.


## Wie kann Azure Automation beim Verwalten des Azure-Schlüsseltresors nützlich sein?

Der Schlüsseltresor kann in Azure Automation mithilfe der [Azure-Schlüsseltresor-Cmdlets](https://msdn.microsoft.com/library/azure/dn868052.aspx) verwaltet werden, die in den [Azure PowerShell-Tools](https://msdn.microsoft.com/library/azure/jj156055.aspx) verfügbar sind. Azure Automation verfügt standardmäßig über diese Cmdlets, sodass Sie Schlüsseltresor-Verwaltungsaufgaben innerhalb des Dienstes ausführen können. Sie können diese Cmdlets in Azure Automation auch mit den Cmdlets für andere Azure-Dienste koppeln, um komplexe Aufgaben in Azure-Diensten und Systemen anderer Hersteller zu automatisieren.

Mit den Azure-Schlüsseltresor-Cmdlets können Sie folgende Aufgaben ausführen: Erstellen oder Importieren eines Schlüssels, Erstellen oder Aktualisieren einen geheimen Schlüssels, Aktualisieren der Attribute eines Schlüssels, Abrufen eines Schlüssels oder eines geheimen Schlüssels oder Löschen eines Schlüssels oder eines geheimen Schlüssels.


## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Azure Automation und dessen Einsatz zum Verwalten des Azure-Schlüsseltresors vertraut gemacht haben, folgen Sie diesen Links, um weitere Informationen zu Azure Automation zu erhalten.

* Weitere Informationen erhalten Sie im Lernprogramm [Erste Schritte](../automation-create-runbook-from-samples.md) zu Azure Automation.
* Weitere Informationen finden Sie in den [PowerShell-Skripts des Azure-Schlüsseltresors](https://gallery.technet.microsoft.com/scriptcenter/Azure-Key-Vault-Powershell-1349b091).

<!--HONumber=54--> 