<properties 
	pageTitle="Erstellen oder Importieren eines Runbooks in Azure Automation"
	description="Dieser Artikel beschreibt, wie Sie in Azure Automation ein neues Runbook erstellen oder ein Runbook aus einer Datei importieren."
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
	ms.date="09/22/2015"
	ms.author="bwren" />

# Erstellen oder Importieren eines Runbooks in Azure Automation

Sie können Azure Automation ein Runbook hinzufügen, indem Sie entweder [ein neues Runbook erstellen](#creating-a-new-runbook) oder ein vorhandenes Runbook aus einer Datei oder aus dem [Runbook-Katalog](automation-runbook-gallery.md) importieren. Dieser Artikel enthält Informationen zum Erstellen und Importieren von Runbooks aus einer Datei. Einzelheiten zum Zugreifen auf Community-Runbooks und Module erfahren Sie in [Runbook und Modulkataloge für Azure Automation](automation-runbook-gallery.md).

## Erstellen eines neuen Runbooks

Sie können ein neues Runbook in Azure Automation mithilfe eines der Azure-Portale oder Windows PowerShell erstellen. Sobald das Runbook erstellt wurde, können Sie es mithilfe der Informationen in [Grundlagen des PowerShell-Workflows](automation-powershell-workflow.md) und [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md) bearbeiten.

### So erstellen Sie ein neues Azure Automation-Runbook mit dem Azure-Portal

Sie können im Azure-Portal nur [PowerShell-Workflow-Runbooks](automation-runbook-types.md#powershell-workflow-runbooks) verwenden.

1. Klicken Sie im Azure-Portal auf **Neu** > **App Services** > **Automation** > **Runbook** > **Schnellerfassung**.
2. Geben Sie die erforderlichen Informationen ein, und klicken Sie dann auf **Erstellen**. Der Runbook-Name muss mit einem Buchstaben beginnen und kann Buchstaben, Zahlen, Unterstriche und Bindestriche enthalten.
3. Wenn Sie das Runbook jetzt bearbeiten möchten, klicken Sie auf **Runbook bearbeiten**. Klicken Sie andernfalls auf **OK**.
4. Ihr neues Runbook wird auf der Registerkarte **Runbooks** angezeigt.


### So erstellen Sie mit dem Azure-Vorschauportal ein neues Azure Automation-Runbook

1. Öffnen Sie im Azure-Vorschauportal Ihr Automation-Konto. 
2. Klicken Sie auf die Kachel **Runbooks**, um die Liste mit den Runbooks zu öffnen.
3. Klicken Sie auf die Schaltfläche **Runbook hinzufügen** und anschließend auf **Neues Runbook erstellen**.
2. Geben Sie einen **Namen** für das Runbook ein, und wählen den [Typ](automation-runbook-types.md) aus. Der Runbook-Name muss mit einem Buchstaben beginnen und kann Buchstaben, Zahlen, Unterstriche und Bindestriche enthalten.
3. Klicken Sie auf **Erstellen**, um das Runbook zu erstellen und den Editor zu öffnen.


### So erstellen Sie ein neues Azure Automation-Runbook mit Windows PowerShell

Sie können das [New AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690272.aspx)-Cmdlet verwenden, um ein leeres [PowerShell-Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks) verwenden. Sie können den Parameter **Name** verwenden, um ein leeres Runbook erstellen, das Sie später bearbeiten können, oder Sie können den Parameter **Pfad** angeben, um eine Skriptdatei zu importieren.

Die folgenden Beispiele zeigen, wie Sie ein neues Automation-Anmeldeinformationsobjekt erstellen.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    New-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName 

## Importieren eines Runbooks aus einer Datei in Azure Automation

Sie können ein neues Runbook in Azure Automation erstellen, indem Sie ein PowerShell-Skript, einen PowerShell-Workflow (.ps1-Erweiterung) oder ein exportiertes grafisches Runbook (.graphrunbook) importieren. Geben Sie den [Runbook-Typ](automation-runbook-types.md) an, der beim Import erstellt wird. Berücksichtigen Sie dabei die folgenden Aspekte.

- Eine .graphrunbook-Datei kann nur in ein neues [grafisches Runbook](automation-runbook-types.md#graphical-runbooks) importiert werden, und grafische Runbooks können nur aus einer .graphrunbook-Datei erstellt werden.
- Eine .ps1-Datei mit einem PowerShell-Workflow kann nur in ein [PowerShell-Workflow- Runbook](automation-runbook-types.md#powershell-workflow-runbooks) importiert werden. Enthält die Datei mehrere PowerShell-Workflows, schlägt der Import fehl. Sie müssen jeden Workflow in einer eigenen Datei speichern und einzeln importieren.
- Eine .ps1-Datei, die keinen Workflow enthält, kann in ein [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) oder ein [PowerShell-Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks) importiert werden. Beim Importieren in einen PowerShell-Workflow Runbook wird es in einen Workflow konvertiert, und Kommentare zu den vorgenommenen Änderungen werden in das Runbook aufgenommen.

### So importieren Sie ein Runbook aus einer Datei mit dem Azure-Portal
Mit dem folgenden Verfahren können eine Skriptdatei in Azure Automation importieren. Beachten Sie, dass Sie mit diesem Portal nur eine .ps1-Datei in ein PowerShell-Workflow-Runbook importieren können. Für andere Typen müssen Sie das Azure-Vorschauportal verwenden.

1. Wählen Sie im Azure-Verwaltungsportal die Option **Automation**, und wählen Sie ein Automation-Konto aus.
2. Klicken Sie auf **Importieren**.
3. Klicken Sie auf **Datei suchen** und suchen Sie die zu importierende Skriptdatei.
4. Wenn Sie das Runbook jetzt bearbeiten möchten, klicken Sie auf **Runbook bearbeiten**. Klicken Sie andernfalls auf OK.
5. Das neue Runbook wird auf der **Runbooks**-Registerkarte für das Automation-Konto angezeigt.
6. Sie müssen [das Runbook veröffentlichen](#publishing-a-runbook), bevor Sie es ausführen können.


### So importieren Sie ein Runbook aus einer Datei mit dem Azure-Vorschauportal
Mit dem folgenden Verfahren können eine Skriptdatei in Azure Automation importieren. Beachten Sie, dass Sie mit diesem Portal nur eine .ps1-Datei in ein PowerShell-Workflow-Runbook importieren können.

1. Öffnen Sie im Azure-Vorschauportal Ihr Automation-Konto. 
2. Klicken Sie auf die Kachel **Runbooks**, um die Liste mit den Runbooks zu öffnen.
3. Klicken Sie auf die Schaltfläche **Runbook hinzufügen** und anschließend auf **Importieren**.
4. Klicken Sie auf **Runbook-Datei**, um die zu importierende Datei auszuwählen.
2. Wenn das Feld **Name** aktiviert ist, haben Sie die Möglichkeit, ihn zu ändern. Der Runbook-Name muss mit einem Buchstaben beginnen und kann Buchstaben, Zahlen, Unterstriche und Bindestriche enthalten.
3. Wählen Sie unter Berücksichtigung der oben aufgeführten Einschränkungen einen [Runbook-Typ](automation-runbook-types.md) aus.
3. Das neue Runbook in der Liste der Runbooks für das Automation-Konto angezeigt.
4. Sie müssen [das Runbook veröffentlichen](#publishing-a-runbook), bevor Sie es ausführen können.

### So importieren Sie ein Runbook aus einer Skriptdatei mit Windows PowerShell

Sie können das [Set AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690267.aspx)-Cmdlet verwenden, um eine Skriptdatei in die Entwurfsversion eines vorhandenen Runbooks zu importieren. Die Skriptdatei muss einen einzelnen Windows PowerShell-Workflow enthalten. Wenn das Runbook bereits über eine Entwurfsversion verfügt, schlägt der Import fehl, insofern Sie nicht den Overwrite-Parameter verwenden. Nachdem das Runbook importiert wurde, können Sie es mit [Publish-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690266.aspx) veröffentlichen.

Die folgenden Beispiele zeigen, wie Sie eine Skriptdatei in ein vorhandenes Runbook importieren und es dann veröffentlichen.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition –AutomationAccountName $automationAccountName –Name $runbookName –Path $ scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName


## Veröffentlichen eines Runbooks

Wenn Sie ein neues Runbooks erstellen oder importieren, müssen Sie es veröffentlichen, bevor Sie es ausführen können. Jedes Runbook in Azure Automation umfasst eine Entwurfsversion und eine veröffentlichte Version. Nur die veröffentlichte Version kann ausgeführt werden, und nur die Entwurfsversion kann bearbeitet werden. Die veröffentlichte Version bleibt von Änderungen an der Entwurfsversion unberührt. Wenn die Entwurfsversion zur Verfügung gestellt werden soll, können Sie sie veröffentlichen und auf diese Weise die veröffentlichte Version mit der Entwurfsversion überschreiben.

## So veröffentlichen Sie ein Runbook mithilfe des Azure-Portals

1. Öffnen Sie das Runbook im Azure-Portal.
1. Klicken Sie am oberen Bildschirmrand auf **Autor**.
1. Klicken Sie am unteren Bildschirmrand auf **Veröffentlichen** und wählen Sie bei der Überprüfungsmeldung **Ja**.

## So veröffentlichen Sie ein Runbook mithilfe des Azure-Vorschauportals

1. Öffen Sie das Runbook im Azure-Vorschauportal.
1. Klicken Sie auf die Schaltfläche **Edit**.
1. Klicken Sie auf **Veröffentlichen** und wählen Sie bei der Überprüfungsmeldung **Ja**.


## So veröffentlichen Sie ein Runbook mit Windows PowerShell

Sie können das Cmdlet [Publish-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690266.aspx) verwenden, um ein Runbook mit Windows PowerShell zu veröffentlichen. Die folgenden Beispielbefehle zeigen, wie Sie ein neues Beispiel-Runbook veröffentlichen.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Sample-TestRunbook"
	
	Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName



## Verwandte Artikel

- [Runbook und Modulkataloge für Azure Automation](automation-runbook-gallery.md)
- [Bearbeiten von Textrunbooks in Azure Automation](automation-edit-textual-runbook.md)
- [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md)

<!---HONumber=Oct15_HO1-->