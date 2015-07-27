<properties
   pageTitle="Migrieren von Runbooks und Integrationspaketen aus Orchestrator"
   description="Beschreibt, wie Runbooks und Integrationspakete von System Center Orchestrator nach Azure Automation migriert werden."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/22/2015"
   ms.author="bwren" />


# Migrieren von Orchestrator nach Azure Automation

Runbooks in [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) basieren auf Aktivitäten der Integrationspaketen, die speziell für Orchestrator geschrieben sind, während Runbooks in Azure Automation auf Windows PowerShell-Workflows basieren. Grafische Runbooks in Azure Automation haben ein ähnliches Aussehen wie Orchestrator-Runbooks, die enthaltenen Aktivitäten repräsentieren PowerShell-Cmdlets, untergeordnete Runbooks und Objekte.

Das [System Center Orchestrator Migration Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) umfasst Tools, die Sie beim Konvertieren von Runbooks von Orchestrator nach Azure Automation unterstützen. Zusätzlich zur Konvertierung der Runbooks selbst müssen Sie die Integrationspakete mit den verwendeten Aktivitäten mithilfe von Windows PowerShell-Cmdlets in Integrationsmodule konvertieren.

Nachfolgend wird das grundlegende Verfahren zum Konvertieren von Orchestrator-Runbooks nach Azure Automation gezeigt. Jeder dieser Schritte wird in den nachstehenden Abschnitten ausführlich beschrieben.

1.  Laden Sie das [System Center Orchestrator Migration Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) herunter, das die in diesem Artikel beschriebenen Tools und Module enthält.
2.  Installieren Sie das [Modul mit Standardaktivitäten](#standard-activities-module) in Azure Automation. Dieses umfasst konvertierte Versionen der standardmäßigen Orchestrator-Aktivitäten, die von konvertierten Runbooks verwendet werden können.
2.  Installieren Sie für die von Ihren Runbooks verwendeten Integrationspakete die [System Center Orchestrator-Integrationsmodule](#system-center-orchestrator-integration-modules) in Azure Automation.
3.  Konvertieren Sie benutzerdefinierte Integrationspakete und Integrationspakete von Drittanbietern mit dem [Integration Pack Converter](#integration-pack-converter), und installieren Sie sie in Azure Automation.
4.  Erstellen Sie globale Orchestrator-Objekte in Azure Automation manuell neu, da für diese Migration keine automatisierte Methode zur Verfügung steht.
5.  Konvertieren Sie Orchestrator-Runbooks mit dem [Runbook Converter](#runbook-converter-coming-soon) (in Kürze verfügbar), und installieren Sie sie in Azure Automation.
6.  Konfigurieren Sie einen [Hybrid-Runbook-Worker](#hybrid-runbook-worker) in Ihrem lokalen Datencenter, um die konvertierten Runbooks auszuführen.

## Service Management Automation (SMA)

[Service Management Automation](http://technet.microsoft.com/library/dn469260.aspx) (SMA) dient wie Orchestrator zum Speichern und Ausführen von Runbooks in Ihrem lokalen Datencenter und führt dieselben Integrationsmodule aus wie Azure Automation. Sobald der [Runbook Converter](#runbook-converter-coming-soon) verfügbar ist, kann er zum Konvertieren von Orchestrator-Runbooks in grafische Runbooks verwendet werden, diese werden jedoch in SMA nicht unterstützt. Sie können in SMA dennoch das [Modul mit Standardaktivitäten](#standard-activities-module) und die [System Center Orchestrator-Integrationsmodule](#system-center-orchestrator-integration-modules) installieren, müssen jedoch Ihre [Runbooks manuell umschreiben](http://technet.microsoft.com/library/dn469262.aspx).

## Hybrid-Runbook-Worker

Runbooks in Orchestrator werden auf einem Datenbankserver gespeichert und auf Runbookservern ausgeführt, beides in Ihrem lokalen Datencenter. Runbooks in Azure Automation werden in der Azure-Cloud gespeichert und können mit einem [Hybrid-Runbook-Worker](automation-hybrid-runbook-worker.md) in Ihrem lokalen Datencenter ausgeführt werden. Üblicherweise führen Sie aus Orchestrator konvertierte Runbooks in dieser Weise aus, da sie zur Ausführung auf lokalen Servern konzipiert sind.

## Integration Pack Converter

Der Integration Pack Converter konvertiert Integrationspakete, die mit OIT (Orchestrator Integration Toolkit) in Integrationsmodule basierend auf Windows PowerShell konvertiert wurden, sodass sie in Azure Automation oder Service Management Automation importiert werden können.

Wenn Sie den Integration Pack Converter ausführen, wird ein Assistent gestartet, der Ihnen die Auswahl einer Integrationspaketdatei (.oip) ermöglicht. Der Assistent listet anschließend die im Integrationspaket enthaltenen Aktivitäten auf, und Sie können die zu migrierenden Aktivitäten auswählen. Wenn Sie den Assistenten abschließen, wird ein Modul erstellt, dass ein entsprechendes Cmdlet für jede Aktivität im ursprünglichen Integrationspaket enthält.


### Parameter

Alle Eigenschaften einer Aktivität im Integrationspaket werden in Parameter des zugehörigen Cmdlets im Integrationsmodul konvertiert. Windows PowerShell-Cmdlets enthalten einen Satz [allgemeiner Parameter](http://technet.microsoft.com/library/hh847884.aspx), die für alle Cmdlets verwendet werden können. Beispielsweise können über den Parameter "-Verbose" detaillierte Informationen zur Verwendung eines Cmdlets ausgegeben werden. Die Cmdlets dürfen keinen Parameter aufweisen, die denselben Namen verwenden wie ein allgemeiner Parameter. Wenn eine Aktivität über eine Eigenschaft mit demselben Namen wie ein allgemeiner Parameter verfügt, fordert der Assistent Sie auf, einen anderen Namen für den Parameter anzugeben.

### Überwachen von Aktivitäten

Runbooks für die Überwachung beginnen in Orchestrator mit einer [Überwachungsaktivität](http://technet.microsoft.com/library/hh403827.aspx) und werden fortlaufend ausgeführt, bis ein bestimmtes Ereignis ausgelöst wird. Azure Automation bietet keine Unterstützung für Runbooks zur Überwachung, deshalb werden im Integrationspaket enthaltene Überwachungsaktivitäten nicht konvertiert. Stattdessen wird im Integrationsmodul ein Platzhalter-Cmdlet für die Überwachungsaktivität erstellt. Dieses Cmdlet hat keine Funktionalität, ermöglicht aber die Installation von konvertierten Runbooks, die die Überwachungsaktivität verwenden. Dieses Runbook kann nicht in Azure Automation ausgeführt werden, aber zur Änderung durch einen Benutzer installiert werden.

### Integrationspakete, die nicht konvertiert werden können

Integrationspakete, die nicht mit OIT erstellt wurden, darunter einige von Microsoft erstellte Integrationspakete, können mit diesem Tool nicht konvertiert werden. Von Microsoft bereitgestellte Integrationspakete wurden in Integrationsmodule konvertiert, sodass sie in Azure Automation oder in Service Management Automation installiert werden können.


## Modul mit Standardaktivitäten

Orchestrator umfasst einen Satz an [Standardaktivitäten](http://technet.microsoft.com/library/hh403832.aspx), die nicht in einem Integrationspaket enthalten sind, aber von vielen Runbooks verwendet werden. Das Modul mit Standardaktivitäten ist ein Integrationsmodul das eine Cmdlet-Entsprechung für jede dieser Aktivitäten umfasst. Sie müssen dieses Integrationsmodul in Azure Automation installieren, bevor Sie konvertierte Runbooks importieren, die eine Standardaktivität verwenden.

Zusätzlich zur Unterstützung von konvertierten Runbooks können die Cmdlets im Modul mit Standardaktivitäten von einem Benutzer mit Orchestrator-Erfahrung dazu verwendet werden, neue Runbooks in Azure Automation zu erstellen. Wenngleich die Funktionalität aller Standardaktivitäten mithilfe von Cmdlets ausgeführt werden kann, weicht die Funktionsweise möglicherweise ab. Die Cmdlets im konvertierten Modul mit Standardaktivitäten arbeiten in gleicher Weise wie die entsprechenden Aktivitäten und verwenden dieselben Parameter. Dies kann den Erstellern von vorhandenen Orchestrator-Runbooks beim Wechsel auf Azure Automation-Runbooks helfen.

## System Center Orchestrator-Integrationsmodule
Microsoft stellt [Integrationspakete](http://technet.microsoft.com/library/hh295851.aspx) zum Erstellen von Runbooks zur Automatisierung von System Center-Komponenten und anderen Produkten bereit. Wenn Sie zurzeit einige dieser Integrationspakete aus [TechNet](http://www.microsoft.com/download/details.aspx?id=39622) herunterladen, können diese nicht mit dem Integration Pack Converter konvertiert werden. Dies ist auf bekannte Probleme zurückzuführen, die in der RC-Version des System Center Orchestrator Migration Toolkits behoben werden. [System Center Orchestrator-Integrationsmodule](http://www.microsoft.com/download/details.aspx?id=47324&WT.mc_id=rss_alldownloads_all) enthalten konvertierte Versionen dieser Integrationspakete, damit diese vor Erscheinen dieser Version in Azure Automation und Service Management Automation importiert werden können.

## Runbook Converter (in Kürze verfügbar)

Dieses Tool konvertiert Orchestrator-Runbooks in grafische Runbooks, die in Azure Automation importiert werden können. Weitere Informationen zu diesem Tool werden bereitgestellt, sobald das Tool zur Verfügung steht.

## Verwandte Artikel

- [System Center 2012 – Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
- [Hybrid-Runbook-Worker](automation-hybrid-runbook-worker.md)
- [Orchestrator-Standardaktivitäten](http://technet.microsoft.com/library/hh403832.aspx)
 

<!---HONumber=July15_HO3-->