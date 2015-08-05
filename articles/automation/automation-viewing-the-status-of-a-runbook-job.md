<properties 
   pageTitle="Anzeigen des Status eines Runbookauftrags in Azure Automation"
   description="Wenn Sie ein Runbook in Azure Automation startet, wird ein Auftrag erstellt. Dieser Artikel stellt Informationen zum Nachverfolgen von Aufträgen und Anzeigen des aktuellen Auftragsstatus bereit."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/30/2015"
   ms.author="bwren" />

# Anzeigen des Status eines Runbookauftrags in Azure Automation


Wenn Sie ein Runbook in Azure Automation startet, wird ein Auftrag erstellt. Ein Auftrag ist eine einzelne Ausführungsinstanz eines Runbooks. Ein einzelnes Runbook kann über mehrere Aufträge verfügen, die jeweils einen Satz von Werten für die Parameter des Runbooks beinhalten. Es gibt verschiedene Möglichkeiten, den Status eines bestimmten Auftrags oder aller Aufträge für ein oder mehrere Runbooks zu überprüfen.

## Auftragsstatus

Die folgende Tabelle beschreibt die verschiedenen Status, die für einen Auftrag möglich sind.

| Status| Beschreibung|
|:---|:---|
|Abgeschlossen|Der Auftrag wurde erfolgreich abgeschlossen.|
|Fehler|Der Auftrag wurde mit einem Fehler beendet.|
|Fehler, auf Ressourcen wird gewartet|Beim Auftrag ist ein Fehler aufgetreten, da der Auftrag dreimal den Grenzwert für die [gleichmäßige Verteilung](http://msdn.microsoft.com/library/azure/3179b655-ab39-407a-9169-33571f958325#fairshare) erreicht hat und jedes Mal vom gleichen Prüfpunkt oder vom Anfang des Runbooks gestartet wurde.|
|In Warteschlange|Der Auftrag wartet darauf, dass Ressourcen für einen Automation-Worker verfügbar werden, damit er gestartet werden kann.|
|Wird gestartet|Der Auftrag wurde einem Worker zugewiesen, und das System ist in Begriff, ihn zu starten.|
|Wird fortgesetzt|Das System ist in Begriff, den Auftrag fortzusetzen, nachdem er angehalten wurde.|
|Wird ausgeführt|Der Auftrag wird ausgeführt.|
|Wird ausgeführt, auf Ressourcen wird gewartet|Der Auftrag wurde entladen, da er den Grenzwert für die [gleichmäßige Verteilung](http://msdn.microsoft.com/library/azure/3179b655-ab39-407a-9169-33571f958325#fairshare) erreicht hat. Er wird in Kürze vom letzten Prüfpunkt wiederaufgenommen.|
|Beendet|Der Auftrag wurde vom Benutzer beendet, bevor er abgeschlossen wurde.|
|Wird beendet|Das System ist in Begriff, den Auftrag zu beenden.|
|Ausgesetzt|Der Auftrag wurde vom Benutzer, vom System oder von einem Befehl im Runbook angehalten. Ein Auftrag, der angehalten wurde, kann erneut gestartet werden und wird vom letzten Prüfpunkt oder vom Anfang des Runbooks fortgesetzt, wenn er keine Prüfpunkte besitzt. Das Runbook wird nur im Fall einer Ausnahme vom System angehalten. Standardmäßig ist "ErrorActionPreference" auf **Continue** festgelegt. Dies bedeutet, dass der Auftrag bei einem Fehler weiterhin ausgeführt wird. Wenn diese Einstellungsvariable auf **Stop** festgelegt wird, wird der Auftrag bei einem Fehler angehalten.|
|Wird angehalten|Das System versucht, den Auftrag auf Anforderung des Benutzers anzuhalten. Das Runbook muss den nächsten Prüfpunkt erreichen, bevor es angehalten werden kann. Wenn der letzte Prüfpunkt bereits verstrichen ist, wird das Runbook abgeschlossen, bevor es angehalten werden kann.|

## Anzeigen des Auftragsstatus über das Azure-Verwaltungsportal

### Automation Dashboard

Das Automation Dashboard zeigt eine Zusammenfassung aller Runbooks für ein bestimmtes Automation-Konto. Es enthält auch eine Nutzungsübersicht für das Konto. Das Zusammenfassungsdiagramm zeigt die Gesamtzahl der Aufträge für alle Runbooks, die während einer bestimmten Anzahl von Tagen oder Stunden den jeweiligen Status erreicht haben. Sie können in der oberen rechten Ecke des Diagramms einen Zeitraum auswählen. Die Zeitachse des Diagramms ändert sich entsprechend der Art des ausgewählten Zeitraums. Sie können die Zeitlinie für einen bestimmten Status anzeigen, indem Sie am oberen Bildschirmrand auf den Status klicken.

Zeigen Sie das Automation Dashboard mithilfe der folgenden Schritte an.

1. Wählen Sie im Azure-Verwaltungsportal die Option **Automation**, und klicken Sie auf den Namen eines Automation-Kontos.
1. Klicken Sie auf die Registerkarte **Dashboard**.

### Runbook-Dashboard

Das Runbook-Dashboard zeigt eine Zusammenfassung für ein einzelnes Runbook. Das Zusammenfassungsdiagramm zeigt die Gesamtzahl der Aufträge für das Runbook, die während einer bestimmten Anzahl von Tagen oder Stunden den jeweiligen Status erreicht haben. Sie können in der oberen rechten Ecke des Diagramms einen Zeitraum auswählen. Die Zeitachse des Diagramms ändert sich entsprechend der Art des ausgewählten Zeitraums. Sie können die Zeitlinie für einen bestimmten Status anzeigen, indem Sie am oberen Bildschirmrand auf den Status klicken.

Zeigen Sie das Runbook-Dashboard mithilfe der folgenden Schritte an.

1. Wählen Sie im Azure-Verwaltungsportal die Option **Automation**, und klicken Sie auf den Namen eines Automation-Kontos.
1. Klicken Sie auf den Namen eines Runbooks.
1. Klicken Sie auf die Registerkarte **Dashboard**.

### API-Zusammenfassung

Sie können eine Liste aller Aufträge, die für ein bestimmtes Runbook erstellt wurden, sowie deren aktuellen Status anzeigen. Sie können diese Liste nach Auftragsstatus und dem Datumsbereich für die letzte Änderung des Auftrags filtern. Klicken Sie auf den Namen eines Auftrags, um detaillierte Informationen und die Auftragsausgabe anzuzeigen. Die Detailansicht des Auftrags enthält die Werte für die Runbookparameter, die für diesen Auftrag bereitgestellt wurden.

Zeigen Sie die Aufträge für ein Runbook mithilfe der folgenden Schritte an.

1. Wählen Sie im Azure-Verwaltungsportal die Option **Automation**, und klicken Sie auf den Namen eines Automation-Kontos.
1. Klicken Sie auf den Namen eines Runbooks.
1. Wählen Sie die Registerkarte **Aufträge** aus.
1. Klicken Sie auf die Spalte **Erstellter Auftrag**, um detaillierte Informationen und die Auftragsausgabe anzuzeigen.

## Abrufen des Auftragsstatus mithilfe von Windows PowerShell

Sie können [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) verwenden, um die für ein Runbook erstellten Aufträge und die Details zu einem bestimmten Auftrag anzuzeigen. Wenn Sie ein Runbook über Windows PowerShell mithilfe des Befehls [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx) starten, gibt er den resultierenden Auftrag zurück. Verwenden Sie [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx), um die Ausgabe eines Auftrags abzurufen.

Die folgenden Beispielbefehle rufen den letzten Auftrag für ein Beispielrunbook ab und zeigen seinen Status, die für die Runbookparameter bereitgestellten Werte und die Ausgabe des Auftrags an.

	$job = (Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" | sort LastModifiedDate –desc)[0]
	$job.Status
	$job.JobParameters
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## Verwandte Artikel

- [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md) 

<!---HONumber=July15_HO4-->