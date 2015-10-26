<properties
	pageTitle="Verwalten von Azure HDInsight mit Azure Automation"
	description="Erfahren Sie, wie Azure Automation zum Verwalten von Azure HDInsight verwendet werden kann."
	services="HDInsight, automation"
	documentationCenter=""
	authors="elcooper"
	manager="eamono"
	editor=""/>

<tags
	ms.service="HDInsight"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/13/2015"
	ms.author="elcooper"/>



#Verwalten von Azure HDInsight mit Azure Automation
Dieses Handbuch bietet eine Einführung zum Azure Automation-Dienst und wie dieser verwendet werden kann, um die Verwaltung von Clustern zu vereinfachen und allgemeine Aufgaben in Azure HDInsight zu automatisieren.

## Was ist Azure Automation?
[Azure Automation](http://azure.microsoft.com/services/automation/) ist ein Azure-Dienst für die Vereinfachung der Cloudverwaltung durch eine Prozessautomatisierung. Mithilfe von Azure Automation können manuelle, häufig wiederholte, lang andauernde und fehleranfällige Aufgaben automatisiert werden, um die Zuverlässigkeit und Effizienz für Ihre Organisation zu erhöhen und die Amortisierungszeit zu verkürzen.

Azure Automation bietet ein äußerst zuverlässiges, hoch verfügbares Workflow-Ausführungsmodul, das sich durch Skalierung an Ihre Anforderungen anpasst. In Azure Automation können Prozesse manuell, von Systemen von Drittanbietern oder in geplanten Intervallen gestartet werden, damit Aufgaben genau im Bedarfsfall ausgeführt werden.

Planen Sie die automatische Ausführung Ihrer Cloud-Verwaltungsaufgaben durch Azure Automation und reduzieren Sie so Ihren Verwaltungsaufwand und ermöglichen Sie es IT- und DevOps-Mitarbeitern, sich auf Arbeiten zu konzentrieren, die dem Unternehmen einen wirtschaftlichen Wert bringen.


## Wie kann Azure Automation beim Verwalten von Azure HDInsight nützlich sein?

HDInsight kann in Azure Automation mithilfe der [Azure HDInsight-Cmdlets](https://msdn.microsoft.com/library/azure/dn479228.aspx) verwaltet werden, die in den [Azure PowerShell-Tools](https://msdn.microsoft.com/library/azure/jj156055.aspx) verfügbar sind. Azure Automation verfügt standardmäßig über diese Cmdlets, sodass Sie HDInsight-Verwaltungsaufgaben innerhalb des Dienstes ausführen können. Sie können diese Cmdlets in Azure Automation auch mit den Cmdlets für andere Azure-Dienste koppeln, um komplexe Aufgaben in Azure-Diensten und Systemen anderer Hersteller zu automatisieren.

Mit den Azure-HDInsight-Cmdlets können Sie Aufgaben wie die Bereitstellung von HDInsight-Clustern in Linux oder Windows, die Skalierung und Verwaltung von Clustern und die Übermittlung von MapReduce-Jobs automatisieren. Dies sind nur einige der vielen Aufgaben, die Sie mithilfe von PowerShell in Azure Automation automatisieren können.


## Nächste Schritte
Nun, da Sie die Grundlagen von Azure Automation kennen und wissen, wie es zum Verwalten von Azure HDInsight verwendet werden kann, folgen Sie diesem Link, um mehr über Azure Automation zu erfahren.

* Weitere Informationen erhalten Sie im Lernprogramm [Erste Schritte](../automation-create-runbook-from-samples.md) zu Azure Automation.
* Beispieln in [Script Center](http://aka.ms/scriptcentergallery) anzeigen.  

 

<!---HONumber=Oct15_HO3-->