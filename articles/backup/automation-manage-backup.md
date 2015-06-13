 
<properties
	pageTitle="Verwalten von Azure-Sicherung unter Verwendung der Azure-Automatisierung" 
	description="Erfahren Sie, wie der Azure-Automatisierung-Dienst zum Verwalten von Azure-Sicherung verwendet werden kann."
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
  
#Verwalten von Azure-Sicherung unter Verwendung der Azure-Automatisierung
 
Dieses Handbuch bietet eine Einführung für den Azure Automation-Dienst und wie es zur Vereinfachung der Verwaltung der Azure-Sicherung verwendet werden kann.
 
## Was ist Azure Automation?
 
[Azure Automation](http://azure.microsoft.com/services/automation/) ist ein Azure-Dienst für die Vereinfachung der Cloudverwaltung durch Prozessautomatisierung. Mit Azure Automation können zeitintensive, manuelle, fehleranfällige und häufig ausgeführte Aufgaben automatisiert werden, um Zuverlässigkeit und Effizienz zu steigern und eine schnellere Amortisierung für Ihre Organisation zu erzielen.
 
Azure Automation bietet ein äußerst zuverlässiges und hochverfügbares Workflow-Ausführungsmodul, das nach Ihren Anforderungen skaliert werden kann, wenn Ihre Organisation wächst. In Azure Automation können Prozesse manuell, durch Drittanbietersysteme oder in geplanten Intervallen gestartet werden, sodass Aufgaben genau nach Bedarf ausgeführt werden.
 
Indem Sie die Aufgaben in Zusammenhang mit der Cloudverwaltung mit Azure Automation automatisieren, verringern Sie den Verwaltungsaufwand und ermöglichen es Ihren IT- und DevOps-Mitarbeitern, sich Aufgaben zu widmen, die einen Mehrwert für Ihr Unternehmen liefern.
 
 
## Wie kann die Azure-Automatisierung zum Azure-Sicherung verwalten?
 
Sicherung kann in Azure-Automatisierung mithilfe von PowerShell-Cmdlets, die in verfügbaren verwaltet werden die [Windows MSOnlineBackup Modul](https://technet.microsoft.com/en-us/library/hh770400.aspx). Azure-Automatisierung kann diese PowerShell-Cmdlets aufgerufen wird, damit Sie alle Aufgaben der Sicherung innerhalb des Diensts durchführen können. Sie können diese Cmdlets in Azure Automation auch an die Cmdlets für andere Azure-Dienste koppeln, um komplexe Aufgaben über Azure-Dienste und Drittanbietersysteme hinweg zu automatisieren.
 
 
## Nächste Schritte
 
Nachdem Sie haben gelernt, dass die Grundlagen von Azure-Automatisierung und wie es zum Verwalten von Azure-Sicherung verwendet werden kann, folgen Sie diesen Links, um weitere Informationen zum Azure-Automatisierung.
 
* Sehen Sie sich den Azure-Automatisierung [Erste Schritte](http://go.microsoft.com/fwlink/?LinkId=390560)

<!---HONumber=GIT-SubDir--> 