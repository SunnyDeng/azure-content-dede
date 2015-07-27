<properties 
   pageTitle="Sichern von Azure Automation"
   description="Beschreibt, wie Sie die Inhalte eines Automation-Kontos sichern, damit diese nach dem Löschen eines Automation-Kontos beibehalten werden können."
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

# Sichern von Azure Automation

Wenn Sie ein Automation-Konto in Microsoft Azure löschen, werden alle Objekte im Konto gelöscht – darunter Runbooks, Module, Einstellungen, Aufträge und Objekte. Die Objekte können nicht wiederhergestellt werden, nachdem das Konto gelöscht wurde. Sie können die Inhalte Ihres Automation-Kontos mithilfe der folgenden Informationen sichern, bevor Sie das Konto löschen.

## Runbooks

Sie können Ihre Runbooks entweder unter Verwendung des Azure-Verwaltungsportals oder mithilfe des Cmdlets [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) in Windows PowerShell in Skriptdateien exportieren. Diese Skriptdateien können in ein anderes Automation-Konto importiert werden, wie beschrieben unter [Erstellen oder Importieren eines Runbooks](https://msdn.microsoft.com/library/dn643637.aspx).


## Integrationsmodule

Integrationsmodule können nicht aus Azure Automation exportiert werden. Sie müssen sicherstellen, dass diese außerhalb des Automation-Kontos verfügbar sind.

## Objekte

[Objekte](https://msdn.microsoft.com/library/dn939988.aspx) können nicht aus Azure Automation exportiert werden. Sie müssen sich unter Verwendung des Azure-Verwaltungsportals die Details zu Variablen, Anmeldeinformationen, Zertifikaten, Verbindungen und Zeitplänen notieren. Anschließend müssen Sie alle Objekte, die in Runbooks verwendet und in ein anderes Automation-Konto importiert werden sollen, manuell erstellen.

Sie können mithilfe von [Azure-Cmdlets](https://msdn.microsoft.com/library/dn690262.aspx) Details aus nicht verschlüsselten Objekten abrufen und diese entweder zu Referenzzwecken speichern, oder Sie können gleichwertige Objekte in einem anderen Automation-Konto erstellen.

Es ist nicht möglich, den Wert verschlüsselter Variablen oder die Kennwortfelder für Anmeldeinformationen mithilfe von Cmdlets abzurufen. Wenn Sie diese Werte nicht kennen, können Sie sie mithilfe der Aktivitäten [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) und [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) aus einem Runbook abrufen.

Zertifikate können nicht aus Azure Automation exportiert werden. Sie müssen sicherstellen, dass die erforderlichen Zertifikate außerhalb von Azure zur Verfügung stehen.

## Verwandte Artikel

- [Erstellen oder Importieren eines Runbooks](https://msdn.microsoft.com/library/dn643637.aspx)
- [Automation-Objekte](https://msdn.microsoft.com/library/dn939988.aspx)
- [Azure-Cmdlets](https://msdn.microsoft.com/library/dn690262.aspx) 

<!---HONumber=July15_HO3-->