<properties
   pageTitle="Runbookausgabe und -meldungen in Azure Automation | Microsoft Azure"
   description="Beschreibt, wie Ausgaben und Fehlermeldungen von Runbooks in Azure Automation erstellt und abgerufen werden."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/03/2016"
   ms.author="magoedte;bwren" />

# Runbookausgabe und -meldungen in Azure Automation

Die meisten Azure Automation-Runbooks haben eine Ausgabe, z. B. eine Fehlermeldung für den Benutzer oder ein komplexes Objekt, das von einem anderen Workflow genutzt werden soll. Windows PowerShell bietet [mehrere Datenströme](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx) zum Senden der Ausgabe eines Skripts oder Workflows. Da jeder dieser Datenströme in Azure Automation unterschiedlich verwendet wird, sollten Sie beim Erstellen eines Runbooks die bewährten Methoden befolgen.

Die folgende Tabelle enthält eine kurze Beschreibung der einzelnen Datenströme und erläutert ihr Verhalten im Azure-Verwaltungsportal beim Ausführen veröffentlichter Runbooks und [Testen von Runbooks](http://msdn.microsoft.com/library/azure/dn879147.aspx). Ausführlichere Informationen zu den verschiedenen Datenströmen finden Sie in den nachfolgenden Abschnitten.

| Stream | Beschreibung | Veröffentlicht | Test|
|:---|:---|:---|:---|
|Ausgabe|Objekte, die von anderen Runbooks genutzt werden.|Wird in den Auftragsverlauf geschrieben.|Wird im Testausgabebereich angezeigt.|
|Warnung|Für den Benutzer vorgesehene Warnmeldung.|Wird in den Auftragsverlauf geschrieben.|Wird im Testausgabebereich angezeigt.|
|Error|Für den Benutzer vorgesehene Fehlermeldung. Anders als bei einer Ausnahme wird das Runbook nach einer Fehlermeldung standardmäßig fortgesetzt.|Wird in den Auftragsverlauf geschrieben.|Wird im Testausgabebereich angezeigt.|
|Ausführlich|Meldungen mit allgemeinen Informationen oder Debuginformationen.|Wird nur in den Auftragsverlauf geschrieben, wenn die ausführliche Protokollierung für das Runbook aktiviert ist.|Wird nur im Testausgabebereich angezeigt, wenn „$VerbosePreference“ im Runbook auf „Continue“ festgelegt ist.|
|Status|Datensätze, die automatisch vor und nach jeder Aktivität im Runbook generiert werden. Das Runbook sollte nicht versuchen, eigene Statusdatensätze zu erstellen, da diese für einen interaktiven Benutzer vorgesehen sind.|Wird nur in den Auftragsverlauf geschrieben, wenn die Statusprotokollierung für das Runbook aktiviert ist.|Wird nicht im Testausgabebereich angezeigt.|
|Debuggen|Für einen interaktiven Benutzer vorgesehene Meldungen. Sollte nicht in Runbooks verwendet werden.|Wird nicht in den Auftragsverlauf geschrieben.|Wird nicht im Testausgabebereich angezeigt.|

## Ausgabedatenstrom

Der Ausgabedatenstrom dient zur Ausgabe von Objekten, die bei korrekter Ausführung von einem Skript oder Workflow erstellt werden. In Azure Automation wird dieser Datenstrom hauptsächlich für Objekte verwendet, die von [übergeordneten Runbooks, die das aktuelle Runbook aufrufen](automation-child-runbooks.md), genutzt werden sollen. Wenn Sie ein Runbook über ein übergeordnetes Runbook [inline aufrufen](automation-child-runbooks.md/#InlineExecution), werden Daten aus dem Ausgabedatenstrom an das übergeordnete Runbook zurückgegeben. Verwenden Sie den Ausgabedatenstrom nur dann zum Anzeigen allgemeiner Informationen für den Benutzer, wenn Sie wissen, dass das Runbook nie von einem anderen Runbook aufgerufen wird. In der Regel empfiehlt es sich jedoch, den [ausführlichen Datenstrom](#Verbose) zu verwenden, um allgemeine Informationen für den Benutzer bereitzustellen.

Sie können Daten in den Ausgabedatenstrom schreiben, indem Sie [Write-Output](http://technet.microsoft.com/library/hh849921.aspx) verwenden oder das Objekt im Runbook in einer eigenen Zeile platzieren.

	#The following lines both write an object to the output stream.
	Write-Output –InputObject $object
	$object

### Ausgabe einer Funktion

Wenn Sie in einer in Ihrem Runbook enthaltenen Funktion in den Ausgabedatenstrom schreiben, wird die Ausgabe an das Runbook zurückgegeben. Weist das Runbook diese Ausgabe einer Variablen zu, wird sie nicht in den Ausgabedatenstrom geschrieben. Wenn Sie innerhalb der Funktion in andere Datenströme schreiben, wird die Ausgabe in den entsprechenden Datenstrom für das Runbook geschrieben.

Sehen Sie sich folgendes Beispielrunbook an:

	Workflow Test-Runbook
	{
	   Write-Verbose "Verbose outside of function"
	   Write-Output "Output outside of function"
	   $functionOutput = Test-Function

	   Function Test-Function
	   {
	      Write-Verbose "Verbose inside of function"
	      Write-Output "Output inside of function"
	   }
	}

Der Ausgabedatenstrom für den Runbookauftrag würde wie folgt aussehen:

	Output outside of function

Der ausführliche Datenstrom für den Runbookauftrag würde wie folgt aussehen:

	Verbose outside of function
	Verbose inside of function

### Deklarieren des Ausgabedatentyps

Ein Workflow kann den Datentyp seiner Ausgabe mit dem [OutputType-Attribut](http://technet.microsoft.com/library/hh847785.aspx) angeben. Dieses Attribut hat zur Laufzeit keinerlei Auswirkung, gibt dem Runbookautor zur Entwurfszeit aber Auskunft über die erwartete Ausgabe des Runbooks. Mit der Weiterentwicklung des Toolsets für Runbooks wird die Deklaration der Ausgabedatentypen zur Entwurfszeit zunehmend an Bedeutung gewinnen. Daher empfiehlt es sich, diese Deklaration in alle von Ihnen erstellten Runbooks einzuschließen.

Das folgende Beispielrunbook gibt ein Zeichenfolgenobjekt aus und enthält eine Deklaration des Ausgabetyps. Wenn Ihr Runbook ein Array eines bestimmten Typs ausgibt, sollten Sie trotzdem den Typ angeben und kein Array des Typs.

	Workflow Test-Runbook
	{
	   [OutputType([string])]

	   $output = "This is some string output."
	   Write-Output $output
	}

## Nachrichtendatenströme

Im Gegensatz zum Ausgabedatenstrom dienen Nachrichtendatenströme zum Übermitteln von Informationen an den Benutzer. Es gibt mehrere Nachrichtendatenströme für verschiedene Arten von Informationen, und jeder dieser Nachrichtendatenströme wird von Azure Automation anders behandelt.

### Warnungs- und Fehlerdatenströme

Die Warnungs- und Fehlerdatenströme dienen zum Protokollieren von Problemen, die in einem Runbook auftreten. Sie werden in den Auftragsverlauf geschrieben, wenn ein Runbook ausgeführt wird, und im Testausgabebereich im Azure-Verwaltungsportal angezeigt, wenn ein Runbook getestet wird. Standardmäßig wird das Runbook nach einer Warnung oder einem Fehler fortgesetzt. Sie können angeben, dass das Runbook bei einer Warnung oder einem Fehler angehalten werden soll, indem Sie vor dem Erstellen der Meldung eine [Einstellungsvariable](#PreferenceVariables) im Runbook festlegen. Soll ein Runbook beim Auftreten eines Fehlers wie bei einer Ausnahme angehalten werden, legen Sie z. B. **$ErrorActionPreference** auf „Stop“ fest.

Erstellen Sie mit dem Cmdlet [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) bzw. [Write-Error](http://technet.microsoft.com/library/hh849962.aspx) eine Warn- oder Fehlermeldung. Aktivitäten können ebenfalls in diese Datenströme schreiben.

	#The following lines create a warning message and then an error message that will suspend the runbook.

	$ErrorActionPreference = "Stop"
	Write-Warning –Message "This is a warning message."
	Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."

### Ausführlicher Datenstrom

Der ausführliche Nachrichtendatenstrom dient für allgemeine Informationen zu Runbookvorgängen. Da der [Debugdatenstrom](#Debug) in einem Runbook nicht verfügbar ist, sollten ausführliche Meldungen für Debuginformationen verwendet werden. Standardmäßig werden ausführliche Meldungen von veröffentlichten Runbooks nicht im Auftragsverlauf gespeichert. Um ausführliche Meldungen zu speichern, konfigurieren Sie für veröffentlichte Runbooks im Azure-Verwaltungsportal auf der Registerkarte „Konfigurieren“ des Runbooks die Einstellung „Ausführliche Datensätze protokollieren“. In den meisten Fällen sollte aus Leistungsgründen die Standardeinstellung (keine Protokollierung ausführlicher Datensätze) für Runbooks beibehalten werden. Aktivieren Sie diese Option nur zum Beheben oder Debuggen eines Runbooks.

Beim [Testen eines Runbooks](http://msdn.microsoft.com/library/azure/dn879147.aspx) werden auch dann keine ausführlichen Meldungen angezeigt, wenn das Runbook zum Protokollieren ausführlicher Datensätze konfiguriert ist. Um beim [Testen eines Runbooks](http://msdn.microsoft.com/library/azure/dn879147.aspx) ausführliche Meldungen anzuzeigen, müssen Sie die $VerbosePreference-Variable auf „Continue“ festlegen. Ist diese Variable festgelegt, werden im Testausgabebereich des Azure-Verwaltungsportals ausführliche Meldungen angezeigt.

Erstellen Sie eine ausführliche Meldung mit dem Cmdlet [Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx).

	#The following line creates a verbose message.

	Write-Verbose –Message "This is a verbose message."

### Debugdatenstrom

Der Debugdatenstrom dient zur Verwendung mit einem interaktiven Benutzer und sollte nicht in Runbooks benutzt werden.

## Statusdatensätze

Wenn Sie ein Runbook zum Protokollieren von Statusdatensätzen konfigurieren (auf der Registerkarte „Konfigurieren“ des Runbooks im Azure-Verwaltungsportal), wird vor und nach der Ausführung jeder Aktivität ein Datensatz in den Auftragsverlauf geschrieben. In den meisten Fällen sollte zum Maximieren der Leistung die Standardeinstellung (keine Protokollierung von Statusdatensätzen) für Runbooks beibehalten werden. Aktivieren Sie diese Option nur zum Beheben oder Debuggen eines Runbooks. Beim Testen eines Runbooks werden auch dann keine Statusmeldungen angezeigt, wenn das Runbook zum Protokollieren von Statusdatensätzen konfiguriert ist.

Das Cmdlet [Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) ist in einem Runbook ungültig, da es zur Verwendung mit einem interaktiven Benutzer vorgesehen ist.

## Einstellungsvariablen

Windows PowerShell ermittelt anhand von [Einstellungsvariablen](http://technet.microsoft.com/library/hh847796.aspx), wie auf Daten reagiert werden soll, die an verschiedene Ausgabedatenströme gesendet werden. Sie können diese Variablen in einem Runbook festlegen, um zu steuern, wie es auf die in unterschiedliche Datenströme gesendeten Daten reagiert.

In der folgenden Tabelle sind die Einstellungsvariablen, die in Runbooks verwendet werden können, mit ihren gültigen Werten und Standardwerten aufgeführt. Beachten Sie, dass diese Tabelle nur die Werte enthält, die in einem Runbook zulässig sind. Bei der Verwendung in Windows PowerShell außerhalb von Azure Automation sind weitere Werte für die Einstellungsvariablen zulässig.

| Variable| Standardwert| Gültige Werte|
|:---|:---|:---|
|WarningPreference|Weiter|Stop<br>Continue<br>SilentlyContinue|
|ErrorActionPreference|Weiter|Stop<br>Continue<br>SilentlyContinue|
|VerbosePreference|SilentlyContinue|Stop<br>Continue<br>SilentlyContinue|

In der folgenden Tabelle wird das Verhalten für die in Runbooks zulässigen Einstellungsvariablenwerte beschrieben.

| Wert| Verhalten|
|:---|:---|
|Weiter|Protokolliert die Meldung und setzt die Ausführung des Runbooks fort.|
|SilentlyContinue|Setzt die Ausführung des Runbooks ohne Protokollierung der Meldung fort. Die Meldung wird ignoriert.|
|Beenden|Protokolliert die Meldung und hält das Runbook an.|

## Abrufen der Runbookausgabe und -meldungen

### Azure-Verwaltungsportal

Sie können die Details eines Runbookauftrags im Azure-Verwaltungsportal auf der Registerkarte „Aufträge“ eines Runbooks anzeigen. Die Zusammenfassung des Auftrags zeigt die Eingabeparameter und den [Ausgabedatenstrom](#Output) sowie allgemeine Informationen zum Auftrag und alle aufgetretenen Ausnahmen. Der Verlauf enthält Meldungen aus dem [Ausgabedatenstrom](#Output), den [Warnungs- und Fehlerdatenströmen](#WarningError) sowie dem [ausführlichen Datenstrom](#Verbose) und den [Statusdatensätzen](#Progress), wenn das Runbook zum Protokollieren von ausführlichen Meldungen und Statusdatensätzen konfiguriert ist.

### Windows PowerShell

In Windows PowerShell können Sie Ausgaben und Meldungen mithilfe des Cmdlets [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/dn690268.aspx) aus einem Runbook abrufen. Dieses Cmdlet erfordert die ID des Auftrags und verfügt über einen Stream-Parameter, mit dem der zurückzugebende Datenstrom angegeben wird. Wenn Sie „Any“ angeben, werde alle Datenströme für den Auftrag zurückgegeben.

Im folgenden Beispiel wird ein Beispielrunbook gestartet und anschließend auf seinen Abschluss gewartet. Danach wird der Ausgabedatenstrom aus dem Auftrag abgerufen.

	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped")
	}

	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

### Grafische Inhaltserstellung

Für grafische Runbooks steht eine zusätzliche Protokollierung in Form einer Ablaufverfolgung auf Aktivitätsebene zur Verfügung. Es gibt zwei Stufen der Ablaufverfolgung: „Standard“ und „Ausführlich“. Bei einer Ablaufverfolgung der Stufe „Standard“ können sie die Start- und Endzeit jeder Aktivität im Runbook sowie Informationen zu Aktivitätswiederholungen sehen, beispielsweise die Anzahl von Versuchen sowie die Startzeit der Aktivität. Bei einer Ablaufverfolgung der Stufe „Ausführlich“ erhalten Sie alle Informationen der Stufe „Standard“ plus Ein- und Ausgabedaten für jede Aktivität. Beachten Sie, dass die Ablaufverfolgungsdatensätze aktuell mithilfe des ausführlichen Datenstroms geschrieben werden, deshalb müssen Sie die ausführliche Protokollierung aktivieren, wenn Sie die Ablaufverfolgung aktivieren. Für grafische Runbooks mit aktivierter Ablaufverfolgung ist es nicht mehr erforderlich, Statusdatensätze zu protokollieren, da die Ablaufverfolgung der Stufe „Standard“ denselben Zweck erfüllt und mehr Informationen liefert.

![Ansicht der Auftragsdatenströme bei der grafischen Inhaltserstellung](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Sie können im oben angezeigten Screenshot sehen, dass bei aktivierter ausführlicher Protokollierung und Ablaufverfolgung für grafische Runbooks in der Ansicht der Datenströme für die Produktionsaufträge deutlich mehr Informationen zur Verfügung stehen. Diese zusätzlichen Informationen können für die Behandlung von Produktionsproblemen mit einem Runbook von wesentlicher Bedeutung sein. Deshalb sollten sie nur zu diesem Zweck und nicht als allgemeine Maßnahme aktiviert werden. Die Anzahl von Ablaufverfolgungsdatensätzen kann erheblich sein. Bei der Ablaufverfolgung für grafische Runbooks können Sie zwei bis vier Datensätze pro Aktivität erhalten – abhängig davon, ob Sie die Ablaufverfolgung der Stufe „Standard“ oder „Ausführlich“ konfiguriert haben. Sofern Sie diese Informationen nicht zur Nachverfolgung des Fortschritts eines Runbooks im Rahmen der Problembehandlung benötigen, sollten Sie die Ablaufverfolgung deaktiviert lassen.

**Führen Sie die folgenden Schritte aus, um die Ablaufverfolgung auf Aktivitätsebene zu aktivieren.**

 1. Öffnen Sie im Azure-Portal Ihr Automation-Konto.

 2. Klicken Sie auf die Kachel **Runbooks**, um die Liste mit den Runbooks zu öffnen.

 3. Klicken Sie auf dem Blatt „Runbooks“ auf ein grafisches Runbook in der Liste, um dieses auszuwählen.

 4. Klicken Sie auf dem Blatt „Einstellungen“ für das ausgewählte Runbook auf **Protokollierung und Ablaufverfolgung**.

 5. Klicken Sie auf dem Blatt „Protokollierung und Ablaufverfolgung“ unter „Ausführliche Datensätze protokollieren“ auf **Ein**, um die ausführliche Protokollierung zu aktivieren. Ändern Sie unterhalb von „Ablaufverfolgung auf Aktivitätsebene“ die Stufe der Ablaufverfolgung basierend auf Ihren Anforderungen in **Standard** oder **Ausführlich**.<br>

    ![Blatt „Protokollierung und Ablaufverfolgung“ bei der grafischen Inhaltserstellung](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

## Verwandte Artikel

- [Nachverfolgen eines Runbookauftrags](automation-runbook-execution.md)
- [Untergeordnete Runbooks](http://msdn.microsoft.com/library/azure/dn857355.aspx)

<!---HONumber=AcomDC_0309_2016-->