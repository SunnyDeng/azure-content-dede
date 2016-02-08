<properties 
   pageTitle="Zeitpläne in Azure Automation | Microsoft Azure"
   description="Automation-Zeitpläne werden verwendet, um die automatische Ausführung von Runbooks in Azure Automation zu planen. In diesem Artikel wird beschrieben, wie Zeitpläne erstellt werden."
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
   ms.date="01/27/2016"
   ms.author="bwren" />

# Zeitpläne in Azure Automation

Automation-Zeitpläne werden zur automatischen Ausführung von Runbooks verwendet. Ein Zeitplan kann ein einzelnes Datum und eine einzelne Uhrzeit enthalten, damit das Runbook einmal ausgeführt wird. Es kann sich aber auch um einen wiederholt auszuführenden Zeitplan handeln, mit dem das Runbook mehrmals gestartet wird. Über Runbooks kann üblicherweise nicht auf Zeitpläne zugegriffen werden.

>[AZURE.NOTE]  Zeitpläne unterstützen derzeit keine Azure Automation DSC-Konfigurationen.

## Windows PowerShell-Cmdlets

Die Cmdlets in der folgenden Tabelle werden zum Erstellen und Verwalten von Variablen mit Windows PowerShell in Azure Automation verwendet. Sie sind im [Azure PowerShell-Modul](../powershell-install-configure.md) enthalten.

|Cmdlets|Beschreibung|
|:---|:---|
|[Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx)|Ruft einen Zeitplan ab.|
|[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx)|Erstellt einen neuen Zeitplan.|
|[Remove-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx)|Entfernt einen Zeitplan.|
|[Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx)|Legt die Eigenschaften für einen vorhandenen Zeitplan fest.|
|[Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx)|Ruft geplante Runbooks ab.|
|[Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx)|Ordnet ein Runbook einem Zeitplan zu.|
|[Unregister-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690273.aspx)|Hebt die Zuordnung eines Runbook zu einem Zeitplan auf.|

## Erstellen eines neuen Zeitplans

### So erstellen Sie einen neuen Zeitplan über das Azure-Portal


1. Klicken Sie in Ihrem Automation-Konto im oberen Fensterbereich auf **Objekte**.
1. Klicken Sie unten im Fenster auf **Einstellung hinzufügen**.
1. Klicken Sie auf **Zeitplan hinzufügen**.
1. Schließen Sie den Assistenten ab, und aktivieren Sie das Kontrollkästchen, um die neue Variable zu speichern.

### So erstellen Sie einen neuen Zeitplan über das Azure-Vorschauportal

1. Klicken Sie in Ihrem Automation-Konto auf **Objekte**, um das Blatt **Objekte** zu öffnen.
1. Klicken Sie auf **Zeitpläne**, um das Blatt **Zeitpläne** zu öffnen.
1. Klicken Sie oben im Blatt auf **Zeitplan hinzufügen**.
1. Geben Sie die erforderlichen Daten ein, und klicken Sie auf **Erstellen**, um den neuen Zeitplan zu speichern.

### So erstellen Sie einen neuen Zeitplan mit Windows PowerShell

Das Cmdlet [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx) erstellt einen neuen Zeitplan und legt den Wert für einen vorhandenen Zeitplan fest. Im folgenden Beispiel erstellen Windows PowerShell-Befehle einen neuen Zeitplan namens "My Daily Schedule", der morgen um 12 Uhr beginnt und ein Jahr lang jeden Tag ausgeführt wird:

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "My Daily Schedule"
	$startTime = (Get-Date).Date.AddDays(1).AddHours(12)
	$expiryTime = $startTime.AddYears(1)
	
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime $startTime –ExpiryTime $expiryTime –DayInterval 1


## Siehe auch
- [Planen eines Runbooks in Azure Automation](automation-scheduling-a-runbook.md)
 

<!---HONumber=AcomDC_0128_2016-->