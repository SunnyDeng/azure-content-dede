<properties 
   pageTitle="Runbookeinstellungen"
   description="Beschreibt die Konfigurationseinstellungen für ein Runbook in Azure Automation und stellt Informationen dazu bereit, wie diese Einstellungen mit dem Azure-Verwaltungsportal und Windows PowerShell geändert werden können."
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
   ms.date="04/13/2015"
   ms.author="bwren" />

# Runbookeinstellungen

Jedes Runbook in Azure Automation verfügt über mehrere Einstellungen, mit denen das Runbook identifiziert und sein Protokollierungsverhalten geändert werden kann. Nachfolgend wird jede dieser Einstellungen beschrieben, und Sie erfahren, wie die Einstellungen geändert werden können.

## Einstellungen

### Name und Beschreibung

Sie können den Namen eines Runbooks nach dessen Erstellung nicht mehr ändern. Die Beschreibung ist optional und kann bis zu 512 Zeichen umfassen.

### Tags

Tags ermöglichen es Ihnen, bestimmte Wörter oder Ausdrücke zuzuweisen, um ein Runbook zu identifizieren. Wenn Sie beispielsweise in Runbook zum [Runbookkatalog](https://msdn.microsoft.com/library/dn781422.aspx) hinzufügen, geben Sie mithilfe bestimmter Tags an, in welchen Kategorien das Runbook gelistet werden soll. Sie können mehrere Tags für ein Runbook angeben, wenn sie diese durch Kommas voneinander trennen.

### Protokollierung

Standardmäßig werden keine Verbose- und Progress-Datensätze in den Auftragsverlauf geschrieben. Sie können die Einstellungen für ein bestimmtes Runbook ändern, um diese Datensätze zu protokollieren. Weitere Informationen zu diesen Datensätzen finden Sie unter [Runbookausgabe und Meldungen](https://msdn.microsoft.com/library/dn879148.aspx).

## Ändern von Runbookeinstellungen

### Ändern von Runbookeinstellungen mit dem Azure-Verwaltungsportal

Sie können die Einstellungen für ein Runbook im Azure-Verwaltungsportal oder über die Seite **Konfigurieren** für das Runbook ändern.

1. Wählen Sie im Azure-Verwaltungsportal die Option **Automation**, und klicken Sie auf den Namen eines Automation-Kontos.
1. Wählen Sie die Registerkarte **Runbooks**.
1. Klicken Sie auf den Namen eines Runbooks.
1. Wählen Sie die Registerkarte **Konfigurieren** aus.

### Ändern von Runbookeinstellungen mit Windows PowerShell

Sie können mit dem Cmdlet [Set-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx) die Einstellungen für ein Runbook ändern. Wenn Sie mehrere Tags angeben möchten, können Sie entweder ein Array oder eine einzelne Zeichenfolge mit durch Kommas getrennten Werten zum Tags-Parameter hinzufügen. Sie rufen die aktuellen Tags mit [Get-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx) ab.

Die folgenden Beispielbefehle zeigen, wie Sie die Eigenschaften für ein Runbook festlegen. In diesem Beispiel werden drei Tags zu den vorhandenen Tags hinzugefügt, und es wird festgelegt, dass Verbose-Datensätze protokolliert werden sollen.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Sample-TestRunbook"
	$tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
	$tags += "Tag1,Tag2,Tag3"
	Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## Verwandte Artikel
- [Runbookausgabe und Meldungen](../automation-runbook-output-and-messages) 
- [Erstellen oder Importieren eines Runbooks](https://msdn.microsoft.com/library/dn643637.aspx)

<!---HONumber=58--> 