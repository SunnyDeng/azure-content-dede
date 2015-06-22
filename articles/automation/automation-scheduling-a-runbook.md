<properties 
   pageTitle="Planen eines Runbooks in Azure Automation"
   description="Beschreibt die Erstellung eines Zeitplans in Azure Automation, sodass ein Runbook automatisch zu einer bestimmten Uhrzeit oder nach einem sich wiederholenden Zeitplan gestartet wird."
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

# Planen eines Runbooks in Azure Automation

Um ein Runbook in Azure-Automation für die Ausführung zu einer bestimmten Uhrzeit zu planen, müssen Sie es mit einem oder mehreren Zeitplänen verknüpfen. Ein Zeitplan kann zur einmaligen Ausführung oder zur wiederholten Ausführung nach einer angegebenen Anzahl an Stunden oder Tagen konfiguriert werden. Ein Runbook kann mit mehreren Zeitplänen verknüpft werden, und mit einem Zeitplan können mehrere Runbooks verknüpft sein.

## Erstellen eines Zeitplans

Sie können einen neuen Zeitplan entweder über das Azure Verwaltungsportal oder mit Windows PowerShell erstellen. Sie haben auch die Möglichkeit, einen neuen Zeitplan zu erstellen, wenn Sie ein Runbook über das Azure-Verwaltungsportal mit einem Zeitplan verknüpfen.

### So erstellen Sie einen neuen Zeitplan über das Azure-Verwaltungsportal

1. Wählen Sie im Azure-Verwaltungsportal die Option "Automation", und klicken Sie auf den Namen eines Automation-Kontos.
1. Wählen Sie die Registerkarte **Objekte** aus.
1. Klicken Sie unten im Fenster auf **Einstellung hinzufügen**.
1. Klicken Sie auf **Zeitplan hinzufügen**.
1. Geben Sie einen **Namen** und optional eine **Beschreibung** für den neuen Zeitplan ein.
1. Wählen Sie die Häufigkeit der Ausführung für den Zeitplan aus: **Einmalig**, **Stündlich** oder **Täglich**.
1. Geben Sie eine **Startzeit** sowie weitere Optionen an, je nachdem, welche Art von Zeitplan Sie ausgewählt haben.

### So erstellen Sie einen neuen Zeitplan mit Windows PowerShell

Sie können das Cmdlet [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) verwenden, um einen neuen Zeitplan in Azure Automation zu erstellen. Sie müssen angeben, zu welcher Zeit der Zeitplan starten soll und ob er einmalig, stündlich oder täglich ausgeführt werden soll.

Die folgenden Beispielbefehle zeigen, wie ein neuer Zeitplan erstellt wird, der ab 20. Januar 2015 jeden Tag um 15:30 ausgeführt wird.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime "1/20/2015 15:30:00" –DayInterval 1

## Verknüpfen eines Zeitplans mit einem Runbook

Ein Runbook kann mit mehreren Zeitplänen verknüpft werden, und mit einem Zeitplan können mehrere Runbooks verknüpft sein. Wenn ein Runbook über Parameter verfügt, können Sie Werte für diese bereitstellen. Sie müssen Werte für alle verbindlichen Parameter angeben; für optionale Parameter können Sie Werte angeben. Diese Werte werden jedes Mal verwendet, wenn das Runbook durch diesen Zeitplan gestartet wird. Sie können das gleiche Runbook einem anderen Zeitplan zuordnen und dabei andere Parameterwerte festlegen.

### So verknüpfen Sie einen Zeitplan mit einem Runbook über das Azure-Verwaltungsportal

1. Wählen Sie im Azure-Verwaltungsportal die Option **Automation**, und klicken Sie auf den Namen eines Automation-Kontos.
1. Wählen Sie die Registerkarte **Runbooks**.
1. Klicken Sie auf den Namen des Runbooks, das Sie mit einem Zeitplan verknüpfen möchten.
1. Klicken Sie auf die Registerkarte **Zeitplan**.
2. Wenn das Runbook gegenwärtig nicht mit einem Zeitplan verknüpft ist, stehen Ihnen die Optionen **Mit einem neuen Zeitplan verknüpfen** oder **Mit einem vorhandenen Zeitplan verknüpfen** zur Verfügung. Wenn das Runbook gegenwärtig mit einem Zeitplan verknüpft ist, klicken Sie auf **Link** am unteren Rand des Fensters, um auf diese Optionen zuzugreifen.
1. Wenn das Runbook über Parameter verfügt, werden Sie zur Eingabe von Werten für diese Parameter aufgefordert.  

### So verknüpfen Sie einen Zeitplan mit einem Runbook mit Windows PowerShell

Sie können das Cmdlet [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) verwenden, um einen Zeitplan mit einem Runbook zu verknüpfen. Sie können Werte für die Runbookparameter im Parameter "Parameters" angeben. Weitere Informationen zum Angeben von Parameterwerten finden Sie unter [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md).

Folgende Beispielbefehle zeigen, wie Sie einen Zeitplan mit einem Runbook mit Parametern verknüpfen.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Test-Runbook"
	$scheduleName = "Sample-DailySchedule"
	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## Deaktivieren eines Zeitplans

Wenn Sie einen Zeitplan deaktivieren, werden sämtliche damit verknüpften Runbooks nicht mehr nach diesem Zeitplan ausgeführt. Sie können einen Zeitplan manuell deaktivieren oder während der Erstellung eines stündlichen oder täglichen Zeitplans eine Ablaufzeit festlegen. Wenn der Ablaufzeitpunkt erreicht ist, wird der Zeitplan deaktiviert.

### So deaktivieren Sie einen Zeitplan über das Azure-Verwaltungsportal

Sie können einen Zeitplan im Azure-Verwaltungsportal über die Seite mit den Zeitplandetails für den Zeitplan deaktivieren.

1. Wählen Sie im Azure-Verwaltungsportal die Option "Automation", und klicken Sie auf den Namen eines Automation-Kontos.
1. Wählen Sie die Registerkarte "Objekte" aus.
1. Klicken Sie auf den Namen eines Zeitplans, um die Detailseite zu öffnen.
2. Ändern Sie **Aktiviert** zu **Nein**.

### So deaktivieren Sie einen Zeitplan mit Windows PowerShell

Sie können das Cmdlet [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) verwenden, um die Eigenschaften eines vorhandenen Zeitplans zu ändern. Um den Zeitplan zu deaktivieren, legen Sie für den Parameter **IsEnabled** den Wert **false** fest.

Die folgenden Beispielbefehle zeigen, wie ein Zeitplan deaktiviert wird.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –IsEnabled $false

## Verwandte Artikel

- [Zeitplanobjekte in Azure Automation](http://msdn.microsoft.com/library/azure/dn940016.aspx)
- [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md)

<!---HONumber=58--> 