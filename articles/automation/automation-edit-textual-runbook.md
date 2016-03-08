<properties 
	pageTitle="Bearbeiten von Textrunbooks in Azure Automation"
	description="Dieser Artikel enthält verschiedene Verfahren zum Arbeiten mit PowerShell und PowerShell-Workflow-Runbooks in Azure Automation unter Verwendung des Text-Editors."
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
	ms.date="02/18/2016"
	ms.author="magoedte;bwren" />

# Bearbeiten von Textrunbooks in Azure Automation

Mit dem Text-Editor in Azure Automation können Sie [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks) und [PowerShell-Workflow-Runbooks](automation-runbook-types.md#powershell-workflow-runbooks) bearbeiten. Dieser Editor bietet neben den üblichen Features anderer Code-Editoren wie Intellisense und Farbcodierung zusätzliche spezielle Features, um den Zugriff auf für Runbooks typische Ressourcen zu ermöglichen. Dieser Artikel enthält detaillierte Schritte zum Ausführen verschiedener Funktionen mit diesem Editor.

Der Text-Editor enthält ein Feature zum Einfügen von Code für Aktivitäten, Objekte und untergeordnete Runbooks in ein Runbook. Sie müssen den Code nicht selbst eingeben, sondern können eine Auswahl aus einer Liste verfügbarer Ressourcen treffen und den entsprechenden Code in das Runbook einfügen.

Jedes Runbook in Azure Automation umfasst zwei Versionen: eine Entwurfsversion und eine veröffentlichte Version. Sie bearbeiten die Entwurfsversion des Runbooks und veröffentlichen sie anschließend, damit sie ausgeführt werden kann. Die veröffentlichte Version kann nicht bearbeitet werden. Weitere Informationen finden Sie unter [Veröffentlichen eines Runbooks](automation-creating-importing-runbook.md#publishing-a-runbook).

Informationen zum Arbeiten mit [grafischen Runbooks](automation-runbook-types.md#graphical-runbooks) finden Sie unter [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md).

## So bearbeiten Sie ein Runbook mit dem Azure-Portal

Gehen Sie wie folgt vor, um ein Runbook zur Bearbeitung im Text-Editor zu öffnen.

1. Wählen Sie im Azure-Portal Ihr Automation-Konto aus.
2. Klicken Sie auf die Kachel **Runbooks**, um die Liste mit den Runbooks zu öffnen.
3. Klicken Sie auf den Namen des Runbooks, das Sie bearbeiten möchten, und klicken Sie dann auf die Schaltfläche **Bearbeiten**.
6. Nehmen Sie die gewünschten Änderungen vor.
7. Klicken Sie auf **Speichern**, wenn die Bearbeitung abgeschlossen ist.
8. Klicken Sie auf **Veröffentlichen**, wenn die aktuelle Entwurfsversion des Runbooks veröffentlicht werden soll.

### So fügen Sie ein Cmdlet in ein Runbook ein

2. Platzieren Sie den Cursor im Zeichenbereich des Text-Editors an der Position, an der Sie das Cmdlet einfügen möchten.
3. Erweitern Sie im Bibliotheksteuerelement den Knoten **Cmdlets**. 
3. Erweitern Sie das Modul mit dem Cmdlet, das Sie verwenden möchten.
4. Klicken Sie mit der rechten Maustaste auf das einzufügende Cmdlet, und wählen Sie **Zum Zeichenbereich hinzufügen**. Wenn für das Cmdlet mehrere Parameter festgelegt sind, wird der Standardsatz hinzugefügt. Sie können das Cmdlet auch erweitern, um einen anderen Parametersatz auszuwählen.
4. Der Code für das Cmdlet wird mit der gesamten Liste der Parameter eingefügt.
5. Geben Sie für alle erforderlichen Parameter anstelle des Datentyps in spitzen Klammern (<>) einen zulässigen Wert ein. Entfernen Sie alle nicht benötigten Parameter.

### So fügen Sie Code für ein untergeordnetes Runbook in ein Runbook ein

2. Platzieren Sie den Cursor im Zeichenbereich des Text-Editors an der Position, an der Sie den Code für das [untergeordnete Runbook](automation-child-runbooks.md) einfügen möchten.
3. Erweitern Sie im Bibliotheksteuerelement den Knoten **Runbooks**. 
3. Klicken Sie mit der rechten Maustaste auf das einzufügende Runbook, und wählen Sie **Zum Zeichenbereich hinzufügen**.
4. Der Code für das untergeordnete Runbook wird mit allen Platzhaltern für alle Runbook-Parameter eingefügt.
5. Ersetzen Sie die Platzhalter durch entsprechende Werte für jeden Parameter.

### So fügen Sie ein Objekt in ein Runbook ein

2. Platzieren Sie den Cursor im Zeichenbereich des Text-Editors an der Position, an der Sie den Code für das untergeordnete Runbook einfügen möchten.
3. Erweitern Sie im Bibliotheksteuerelement den Knoten **Objekte**. 
4. Erweitern Sie den Knoten für den gewünschten Objekttyp.
3. Klicken Sie mit der rechten Maustaste auf das einzufügende Objekt, und wählen Sie **Zum Zeichenbereich hinzufügen**. Wählen Sie für [Variablenobjekte](automation-variables.md) entweder **„Variable abrufen“ zum Zeichenbereich hinzufügen** oder **„Variable festlegen“ zum Zeichenbereich hinzufügen**. Dies ist abhängig davon, ob Sie die Variable abrufen oder festlegen möchten.
4. Der Code für das Objekt wird in das Runbook eingefügt.



## So bearbeiten Sie ein Runbook mit dem Azure-Portal

Gehen Sie wie folgt vor, um ein Runbook zur Bearbeitung im Text-Editor zu öffnen.

1. Wählen Sie im Azure-Portal die Option **Automation**, und klicken Sie auf den Namen eines Automation-Kontos.
2. Wählen Sie die Registerkarte **Runbooks**.
3. Klicken Sie auf den Namen des Runbooks, das Sie bearbeiten möchten, und wählen Sie dann die Registerkarte **Autor**.
5. Klicken Sie unten auf dem Bildschirm auf **Bearbeiten**.
6. Nehmen Sie die gewünschten Änderungen vor.
7. Klicken Sie auf **Speichern**, wenn die Bearbeitung abgeschlossen ist.
8. Klicken Sie auf **Veröffentlichen**, wenn die aktuelle Entwurfsversion des Runbooks veröffentlicht werden soll.

### So fügen Sie eine Aktivität in ein Runbook ein

1. Platzieren Sie den Cursor im Zeichenbereich des Text-Editors an der Position, an der Sie die Aktivität einfügen möchten.
1. Klicken Sie unten auf dem Bildschirm auf **Einfügen** und anschließend auf **Aktivität**.
1. Wählen Sie in der Spalte **Integrationsmodul** das Modul aus, das die Aktivität enthält.
1. Wählen Sie im Bereich **Aktivität** eine Aktivität aus.
1. Beachten Sie in der Spalte **Beschreibung** die Beschreibung der Aktivität. Sie können auch auf „Detaillierte Hilfe anzeigen“ klicken, um die Hilfe zur Aktivität im Browser zu starten.
1. Klicken Sie auf den Pfeil nach rechts. Wenn die Aktivität über Parameter verfügt, werden sie zur Information aufgelistet.
1. Klicken Sie dann die Taste „Aktivieren“. Code zum Ausführen der Aktivität wird in das Runbook eingefügt.
1. Sind für die Aktivität Parameter erforderlich, geben Sie anstelle des Datentyps in spitzen Klammern (<>) einen zulässigen Wert ein.

### So fügen Sie Code für ein untergeordnetes Runbook in ein Runbook ein

1. Platzieren Sie den Cursor im Zeichenbereich des Text-Editors an der Position, an der Sie das [untergeordnete Runbook](automation-child-runbooks.md) einfügen möchten.
2. Klicken Sie unten auf dem Bildschirm auf **Einfügen** und anschließend auf **Runbook**.
3. Wählen Sie das einzufügende Runbook aus der mittleren Spalte aus, und klicken Sie auf den Pfeil nach rechts.
4. Wenn das Runbook über Parameter verfügt, werden sie zur Information aufgelistet.
5. Klicken Sie dann die Taste „Aktivieren“. Code zum Ausführen des ausgewählten Runbooks wird in das aktuelle Runbook eingefügt.
7. Sind für das Runbook Parameter erforderlich, geben Sie anstelle des Datentyps in spitzen Klammern (<>) einen zulässigen Wert ein.

### So fügen Sie ein Objekt in ein Runbook ein

1. Platzieren Sie zum Abrufen des Objekts den Cursor im Zeichenbereich des Text-Editors an der Position, an der Sie die Aktivität einfügen möchten.
1. Klicken Sie unten auf dem Bildschirm auf **Einfügen** und anschließend auf **Einstellung**.
1. Wählen Sie in der Spalte **Einstellungsaktion** die gewünschte Aktion aus.
1. Wählen Sie aus der mittleren Spalte verfügbare Objekte aus.
1. Klicken Sie dann die Taste „Aktivieren“. Code zum Abrufen oder Festlegen des Objekts wird in das Runbook eingefügt.



## So bearbeiten Sie ein Azure Automation-Runbook mit Windows PowerShell

Zum Bearbeiten eines Runbooks mit Windows PowerShell verwenden Sie einen Editor Ihrer Wahl und speichern es in einer PS1-Datei. Mit dem Cmdlet [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) können Sie den Inhalt des Runbooks abrufen, und mit dem Cmdlet [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) können Sie das vorhandene Entwurfsrunbook durch das geänderte Runbook ersetzen.

### So rufen Sie den Inhalt eines Runbooks mit WindowsPowerShell ab

Die folgenden Beispielbefehle zeigen, wie das Skript für ein Runbook abgerufen und in einer Skriptdatei gespeichert wird. In diesem Beispiel wird die Entwurfsversion abgerufen. Sie können auch die veröffentlichte Version des Runbooks abrufen, auch wenn diese Version nicht geändert werden kann.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### So ändern Sie den Inhalt eines Runbooks mit WindowsPowerShell

Die folgenden Beispielbefehle zeigen, wie der vorhandene Inhalt eines Runbooks durch den Inhalt einer Skriptdatei ersetzt wird. Hinweis: Hierbei handelt es sich um das gleiche Beispielverfahren wie unter [So importieren Sie ein Runbook aus einer Skriptdatei mit Windows PowerShell](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS).

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## Verwandte Artikel

- [Erstellen oder Importieren eines Runbooks in Azure Automation](automation-creating-importing-runbook.md)
- [Grundlagen des PowerShell-Workflows](automation-powershell-workflow.md)
- [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md)
- [Zertifikate](automation-certificates.md)
- [Verbindungen](automation-connections.md)
- [Anmeldeinformationen](automation-credentials.md)
- [Zeitpläne](automation-schedules.md)
- [Variablen](automation-variables.md)

<!---HONumber=AcomDC_0224_2016-->