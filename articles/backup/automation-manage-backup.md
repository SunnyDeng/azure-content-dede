<properties 
	pageTitle="Verwalten von Azure Backup mithilfe von Azure Automation" 
	description="Erfahren Sie, wie der Azure Automation-Dienst zum Verwalten von Azure Backup verwendet werden kann." 
	services="backup, automation" 
	documentationCenter="" 
	authors="eamonoreilly" 
	manager="jwinter" 
	editor=""/>

<tags 
	ms.service="backup" 
	ms.workload="storage-backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="eamono"/>


#Verwalten von Azure Backup mithilfe von Azure Automation

Dieser Leitfaden bietet eine Einführung in den Azure Automation-Dienst und zeigt, wie Azure Automation zur Vereinfachung der Verwaltung von Azure Backup genutzt werden kann.

## Was ist Azure Automation?

[Azure Automation](http://azure.microsoft.com/services/automation/) ist ein Azure-Dienst für die Vereinfachung der Cloudverwaltung durch eine Prozessautomatisierung. Mit Azure Automation können zeitintensive, manuelle, fehleranfällige und häufig ausgeführte Aufgaben automatisiert werden, um Zuverlässigkeit und Effizienz zu steigern und eine schnellere Amortisierung für Ihre Organisation zu erzielen.

Azure Automation bietet ein äußerst zuverlässiges und hochverfügbares Workflow-Ausführungsmodul, das nach Ihren Anforderungen skaliert werden kann, wenn Ihre Organisation wächst. In Azure Automation können Prozesse manuell, durch Drittanbietersysteme oder in geplanten Intervallen gestartet werden, sodass Aufgaben genau nach Bedarf ausgeführt werden.

Indem Sie die Aufgaben in Zusammenhang mit der Cloudverwaltung mit Azure Automation automatisieren, verringern Sie den Verwaltungsaufwand und ermöglichen es Ihren IT- und DevOps-Mitarbeitern, sich Aufgaben zu widmen, die einen Mehrwert für Ihr Unternehmen liefern.


## Wie kann Azure Automation Sie beim Verwalten von Azure Backup unterstützen?

Azure Backup kann in Azure Automation durch die Verwendung von PowerShell-Cmdlets verwaltet werden, die im [Windows MSOnlineBackup-Modul](https://technet.microsoft.com/de-de/library/hh770400.aspx) zur Verfügung stehen. Diese PowerShell-Cmdlets können von Azure Automation aufgerufen werden, sodass Sie alle Aufgaben im Zusammenhang mit der Verwaltung von Azure Backup innerhalb des Diensts ausführen können. Sie können diese Cmdlets in Azure Automation auch an die Cmdlets für andere Azure-Dienste koppeln, um komplexe Aufgaben über Azure-Dienste und Drittanbietersysteme hinweg zu automatisieren.


## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Azure Automation und seiner Verwendung zum Verwalten von Azure Backup vertraut gemacht haben, folgen Sie diesen Links, um weitere Informationen zu Azure Automation zu erhalten.

* Weitere Informationen finden Sie im Handbuch [Erste Schritte mit Azure Automation](http://go.microsoft.com/fwlink/?LinkId=390560).
 

<!---HONumber=July15_HO3-->