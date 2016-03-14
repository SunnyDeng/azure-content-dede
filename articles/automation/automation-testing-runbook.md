<properties 
	pageTitle="Testen eines Runbooks in Azure Automation"
	description="Bevor Sie ein Runbook in Azure Automation veröffentlichen, können Sie es testen, um sicherzustellen, dass es wie erwartet funktioniert. In diesem Artikel wird beschrieben, wie Sie ein Runbook testen und die Ausgabe anzeigen."
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
	ms.date="02/23/2016"
	ms.author="magoedte;bwren" />

# Testen eines Runbooks in Azure Automation
Wenn Sie ein Runbook testen, wird die [Entwurfsversion](automation-creating-importing-runbook.md#publishing-a-runbook) ausgeführt, und alle darin ausgeführten Aktionen werden abgeschlossen. Es wird kein Auftragsverlauf erstellt, aber die [Ausgabe-](automation-runbook-output-and-messages.md#output-stream) sowie die [Warnungs- und Fehlerdatenströme](automation-runbook-output-and-messages.md#message-streams) werden im Testausgabebereich angezeigt. Nachrichten an den [ausführlichen Datenstrom](automation-runbook-output-and-messages.md#message-streams) werden im Ausgabebereich nur angezeigt, wenn die Variable [$VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) auf "Continue" festgelegt ist.

Auch wenn die Entwurfsversion ausgeführt wird, führt das Runbook den Workflow trotzdem normal aus und wendet alle Aktionen auf die Ressourcen in der Umgebung an. Aus diesem Grund sollten Sie nur Runbooks für Ressourcen testen, die sich nicht in der Produktionsumgebung befinden.

Das Verfahren zum Testen der verschiedenen [Runbooktypen](automation-runbook-types.md) ist identisch, und es besteht kein Unterschied zwischen Tests mit dem Text-Editor und dem grafischen Editor im Azure-Portal.


## So starten Sie ein Runbook im Azure-Portal

Sie können mit jedem [Runbooktyp](automation-runbook-types.md) im Azure-Portal arbeiten.

1. Öffnen Sie die Entwurfsversion des Runbooks entweder im [Text-Editor](automation-editing-a-runbook#Portal) oder im [grafischen Editor](automation-graphical-authoring-intro.md).
2. Klicken Sie auf die Schaltfläche **Test** um das Blatt "Test" zu öffnen.
3. Wenn das Runbook über Parameter verfügt, werden diese im linken Bereich aufgeführt. Hier können Sie die für den Test zu verwendenden Werte angeben.
4. Wenn Sie den Test auf einem [Hybrid Runbook Worker](automation-hybrid), ausführen möchten, ändern Sie die **Testlaufeinstellungen** auf **Hybrid Worker**, und wählen Sie den Namen der Zielgruppe aus. Andernfalls behalten Sie den Standardwert **Azure** bei, um den Test in der Cloud auszuführen.
5. Klicken Sie auf die Schaltfläche **Start**, um den Test zu starten.
6. Wenn das Runbook ein [PowerShell-Workflow](automation-runbook-types.md#powershell-workflow-runbooks) oder [grafisch](automation-runbook-types.md#graphical-runbooks) ist, können Sie es während des Tests mit den Schaltflächen unterhalb des Ausgabebereichs beenden oder anhalten. Wenn Sie das Runbook anhalten, wird die aktuelle Aktivität vor der Unterbrechung abgeschlossen. Nachdem das Runbook angehalten wurde, können Sie es beenden oder erneut starten.
7. Untersuchen Sie die Ausgabe des Runbooks im Ausgabebereich.



## So starten Sie ein Runbook im Azure-Portal

Sie können im Azure-Portal nur [PowerShell-Workflow-Runbooks](automation-runbook-types.md#powershell-workflow-runbooks) verwenden.


1. [Öffnen Sie die Entwurfsversion des Runbooks](automation-edit-textual-runbook.md#to-edit-a-runbook-with-the-azure-portal).
2. Klicken Sie auf die Schaltfläche **Test**, um den Test zu starten. Wenn das Runbook über Parameter verfügt, wird ein Dialogfeld angezeigt, in dem Sie Werte festlegen können, die für den Test verwendet werden sollen.
6. Sie können das Runbook während des Tests mit den Schaltflächen unterhalb des Ausgabebereichs beenden oder anhalten. Wenn Sie das Runbook anhalten, wird die aktuelle Aktivität vor der Unterbrechung abgeschlossen. Nachdem das Runbook angehalten wurde, können Sie es beenden oder erneut starten.
7. Untersuchen Sie die Ausgabe des Runbooks im Ausgabebereich.


## Verwandte Artikel

- [Erstellen und Importieren eines Runbooks in Azure Automation](automation-creating-importing-runbook.md)
- [Grafische Runbooks in Azure Automation](automation-graphical-authoring-intro.md)
- [Bearbeiten von Textrunbooks in Azure Automation](automation-edit-textual-runbook.md)
- [Runbookausgabe und -meldungen in Azure Automation](automation-runbook-output-and-messages.md)

<!---HONumber=AcomDC_0302_2016-->