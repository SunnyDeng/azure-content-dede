<properties
   pageTitle="Azure Automation – Hybrid-Runbook-Worker"
   description="Der vorliegende Artikel stellt Informationen zum Installieren und Verwendung der Azure Automation-Funktion ";Hybrid-Runbook-Worker"; bereit, mit der Sie Runbooks auf Computern in Ihrem lokalen Datencenter ausführen können."
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
   ms.date="07/10/2015"
   ms.author="bwren" />

# Azure Automation – Hybrid-Runbook-Worker

Runbooks in Azure Automation können nicht auf Ressourcen in Ihrem lokalen Datencenter zugreifen, da sie in der Azure-Cloud ausgeführt werden. Die Funktion "Hybrid-Runbook-Worker" von Azure Automation ermöglicht Ihnen das Ausführen von Runbooks auf Computern in Ihrem Datencenter, um lokale Ressourcen verwalten zu können. Die Runbooks werden in Azure Automation gespeichert und verwaltet und bei der Ausführung an einen oder mehrere lokale Computer übermittelt.

Diese Funktionalität wird in der folgenden Abbildung veranschaulicht.

![Hybrid-Runbook-Worker – Übersicht](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-overview.png)

Sie können einen oder mehrere Computer in Ihrem Datencenter als Hybrid-Runbook-Worker einsetzen und Runbooks aus Azure Automation starten. Jeder Worker erfordert den Microsoft-Verwaltungs-Agent mit einer Verbindung zu Azure Operational Insights und der Azure Automation-Runbookumgebung. Operational Insights wird nur zum Installieren und Warten des Verwaltungs-Agents und zum Überwachen der Workerfunktionalität eingesetzt. Die Übermittlung von Runbooks und die Anweisung zu ihrer Ausführung erfolgt durch Azure Automation.

![Hybrid-Runbook-Worker – Komponenten](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-components.png)

Es gelten keine eingehenden Firewallanforderungen zur Unterstützung von Hybrid-Runbook-Workern. Der Agent auf dem lokalen Computer initiiert die gesamte Kommunikation mit Azure Automation in der Cloud. Wenn ein Runbook gestartet wird, erstellt Azure Automation eine Anweisung, die vom Agent abgerufen wird. Der Agent ruft anschließend das Runbook und alle Parameter über einen Pullvorgang ab. Außerdem werden alle im Runbook verwendeten [Objekte](http://msdn.microsoft.com/library/dn939988.aspx) aus Azure Automation abgerufen.

## Hybrid-Runbook-Workergruppen

Jeder Hybrid-Runbook-Worker ist ein Mitglied einer Hybrid-Runbook-Workergruppe, die Sie beim Installieren des Agents angeben. Eine Gruppe kann einen einzelnen Agent umfassen, aber für eine höhere Verfügbarkeit können Sie mehrere Agents installieren.

Wenn Sie ein Runbook auf einen Hybrid-Runbook-Worker starten, geben Sie die Gruppe an, auf der das Runbook ausgeführt werden soll. Die Mitglieder der Gruppe legen fest, welcher Worker die Anforderung verarbeitet. Sie können keinen bestimmten Worker angeben.

## Installieren von Hybrid-Runbook-Worker

### Vorbereiten der Azure Automation-Umgebung

Führen Sie die folgenden Schritte aus, um Ihre Azure Automation-Umgebung für Hybrid-Runbook-Worker vorzubereiten.

#### 1\. Erstellen eines Azure Operational Insights-Arbeitsbereichs
Sofern Sie noch nicht über einen Operational Insights-Arbeitsbereich in Ihrem Azure-Konto verfügen, erstellen Sie diesen mithilfe der Anweisungen unter [Einrichten Ihres Operational Insights-Arbeitsbereichs](../operational-insights-setup-workspace). Wenn Sie bereits über einen Arbeitsbereich verfügen, können Sie diesen verwenden.

#### 2\. Bereitstellen der Automation-Lösung
Die Automation-Lösung in Operational Insights lädt per Push Komponenten herunter, die zum Konfigurieren und Unterstützen der Runbookumgebung erforderlich sind. Befolgen Sie die Anweisungen unter [Operational Insights-Lösungen](../operational-insights-add-solution), um das **Azure Automation**-Pack zu installieren.

### Konfigurieren der lokalen Computer
Führen Sie die folgenden Schritte für jeden lokalen Computer aus, der als Hybrid-Runbook-Worker fungieren soll.


#### 1\. Installieren des Microsoft-Verwaltungs-Agents
Der Microsoft-Verwaltungs-Agent verbindet den Computer mit Operational Insights und ermöglicht die Ausführung von Logik aus Lösungen. Befolgen Sie die Anweisungen unter [Direktes Verbinden von Computern mit Operational Insights](../operational-insights-direct-agent), um den Agent auf dem lokalen Computer zu installieren und ihn mit Operational Insights zu verbinden.

#### 2\. Installieren der Runbookumgebung und Verbindungsherstellung mit Azure Automation
Wenn Sie einen Computer zu Operational Insights hinzufügen, lädt die Automation-Lösung per Push das PowerShell-Modul **HybridRegistration** herunter, in dem das Cmdlet **Add-HybridRunbookWorker** enthalten ist. Sie verwenden dieses Cmdlet zum Installieren der Runbookumgebung auf dem Computer und registrieren diesen bei Azure Automation.

Öffnen Sie eine PowerShell-Sitzung im Administratormodus, und führen Sie den folgenden Befehl zum Importieren des Moduls aus.

	Import-Module HybridRegistration

Wenn Sie eine Fehlermeldung erhalten, nach der das Modul nicht gefunden wurde, müssen Sie möglicherweise den folgenden Befehl verwenden, der den vollständigen Pfad zur Moduldatei angibt.

	Import-Module "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomationFiles\HybridRegistration\HybridRegistration.psd1"

Führen Sie dann das Cmdlet **Add-HybridRunbookWorker** mit der folgenden Syntax aus:

	Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>


- **Name** ist der Name der Hybrid-Runbook-Workergruppe. Wenn die Gruppe bereits im Automation-Konto vorhanden ist, wird der aktuelle Computer der Gruppe hinzugefügt. Ist die Gruppe nicht vorhanden, wird sie hinzugefügt.
- **EndPoint** ist die URL des Agent-Diensts. Sie können diese URL aus dem Blatt **Schlüssel verwalten** im Azure-Vorschauportal abrufen.  
- **Token** ist der **Primäre Zugriffsschlüssel** im Blatt **Schlüssel verwalten**. Sie können das Blatt "Schlüssel verwalten" öffnen, indem Sie im Bereich "Elemente" für das Automation-Konto auf das Schlüsselsymbol klicken.<br><br>![Hybrid-Runbook-Worker – Übersicht](media/automation-hybrid-runbook-worker/elements-panel-keys.png)


#### 3\. Installieren von PowerShell-Modulen
Runbooks können beliebige Aktivitäten und Cmdlets der Module verwenden, die Sie in Ihrer Azure Automation-Umgebung installiert haben. Diese Module werden nicht automatisch auf den lokalen Computern bereitgestellt, sie müssen manuell installiert werden. Eine Ausnahme stellt das standardmäßig installierte Azure-Modul dar. Es bietet Zugriff auf Cmdlets für alle Azure-Dienste und -Aktivitäten für Azure Automation.

Da der primäre Zweck der Funktion "Hybrid-Runbook-Worker" darin besteht, lokale Ressourcen zu verwalten, müssen Sie sehr wahrscheinlich die Module zur Unterstützung dieser Ressourcen installieren. Unter [Installieren von Modulen](http://msdn.microsoft.com/library/dd878350.aspx) finden Sie Informationen zum Installieren von PowerShell-Modulen.

## Starten von Runbooks auf einem Hybrid-Runbook-Worker

Unter [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md) werden die verschiedenen Methoden zum Starten eines Runbooks beschrieben. Hybrid-Runbook-Worker fügt die Option **RunOn** hinzu, mit der Sie den Namen einer Hybrid-Runbook-Workergruppe angeben können. Beim Angeben einer Gruppe wird das Runbook von den Workern in dieser Gruppe abgerufen und ausgeführt. Wird diese Option nicht angegeben, erfolgt die Ausführung in normaler Weise in Azure Automation.

Wenn Sie ein Runbook im Azure-Vorschauportal starten, wird die Option **Ausführen auf** angezeigt. Sie können entweder **Azure** oder **Hybrid-Worker** auswählen. Bei Auswahl von **Hybrid-Worker** können Sie anschließend aus einer Dropdownliste die gewünschte Gruppe auswählen.

Verwenden des Parameters **RunOn**: Mit dem folgenden Befehl wird unter Verwendung von Windows PowerShell ein Runbook namens "Test-Runbook" auf einer Hybrid-Runbook-Workergruppe mit dem Namen "MyHybridGroup" gestartet.

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

>[AZURE.NOTE]Der Parameter **RunOn** wurde dem Cmdlet **Start-AzureAutomationRunbook** in Version 0.9.1 von Microsoft Azure PowerShell hinzugefügt. Sie sollten die [neueste Version herunterladen](http://azure.microsoft.com/downloads), wenn auf Ihrem System eine Vorgängerversion installiert ist. Sie müssen diese Version auf einer Arbeitsstation installieren, auf der Sie das Runbook aus Windows PowerShell starten. Eine Installation auf dem Workercomputer ist nur erforderlich, wenn Sie Runbooks von diesem Computer starten möchten. Sie können ein Runbook auf einem Hybrid-Runbook-Worker derzeit nicht aus einem anderen Runbook starten, da hierzu die neueste Version von Azure PowerShell in Ihrem Automation-Konto installiert sein müsste. Die neueste Version wird in Kürze automatisch in Azure Automation aktualisiert und automatisch auf die Worker gepusht.


## Erstellen von Runbooks für Hybrid-Runbook-Worker

Es gibt keine Unterschiede in der Struktur von Runbooks, die in Azure Automation oder auf einem Hybrid-Runbook-Worker ausgeführt werden. Die verwendeten Runbooks unterscheiden sich jedoch wahrscheinlich dahingehend, dass Hybrid-Runbook-Worker typischerweise lokale Ressourcen in Ihrem Datencenter verwalten, während Runbooks in Azure Automation üblicherweise Ressourcen in der Azure-Cloud verwalten.

### Runbookberechtigungen

Runbooks werden im Kontext des lokalen Systemkontos auf dem Hybrid-Runbook-Worker ausgeführt, deshalb müssen sie sich gegenüber den Ressourcen authentifizieren, auf die sie zugreifen. Sie können nicht dieselbe [Methode verwenden, die typischerweise für die Runbookauthentifizierung bei Azure-Ressourcen](automation-configuring.md#configuring-authentication-to-azure-resources) verwendet wird, da sie auf Ressourcen außerhalb von Azure zugreifen.

Sie können die Objekte [Anmeldeinformationen](http://msdn.microsoft.com/library/dn940015.aspx) und [Zertifikat](http://msdn.microsoft.com/library/dn940013.aspx) in Ihrem Runbook gemeinsam mit Cmdlets zur Angabe von Anmeldeinformationen verwenden, um eine Authentifizierung für verschiedene Ressourcen durchzuführen. Das folgende Beispiel zeigt einen Teil eines Runbooks, mit dem ein Computer neu gestartet wird. Es werden Anmeldeinformationen aus einem Anmeldeinformationsobjekt und der Name des Computers aus einem Variablenobjekt abgerufen, anschließend werden diese Werte im Cmdlet "Restart-Computer" eingesetzt.

	$Cred = Get-AutomationCredential "MyCredential"
	$Computer = Get-AutomationVariable "ComputerName"

	Restart-Computer -ComputerName $Computer  -Credential $Cred

Sie können auch [InlineScript](automation-powershell-workflow.md#inline-script) verwenden, mit dem Codeblöcke mit den im allgemeinen [PSCredential-Parameter](http://technet.microsoft.com/library/jj129719.aspx) angegebenen Anmeldeinformationen auf einem anderen Computer ausgeführt werden.


### Erstellen und Testen von Runbooks

Sie können ein Runbook für Hybrid-Runbook-Worker in Azure Automation bearbeiten, es ist jedoch möglicherweise schwierig, das Runbook im Editor zu testen. Die PowerShell-Module für den Zugriff auf die lokalen Ressourcen sind möglicherweise nicht in Ihrer Azure Automation-Umgebung installiert, sodass beim Testen Fehler auftreten. Wenn Sie die erforderlichen Module installieren, wird das Runbook ausgeführt, der Test kann aufgrund des fehlenden Zugriffs auf die lokalen Ressourcen jedoch nicht vollständig ausgeführt werden.

## Beziehung zu Service Management Automation (SMA)

[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) ist eine Komponente in Microsoft Azure Pack, mit der dieselben Runbooks, die von Azure Automation unterstützt werden, in Ihrem lokalen Datencenter ausgeführt werden können. Im Gegensatz zu Azure Automation erfordert SMA eine lokale Installation, die das Microsoft Azure Pack-Verwaltungsportal und eine Datenbank zum Speichern von Runbooks und SMA-Konfiguration einschließt. Azure Automation stellt diese Dienste in der Cloud bereit, Sie müssen lediglich die Hybrid-Runbook-Worker in Ihrer lokalen Umgebung verwalten.

Wenn Sie SMA bereits verwenden, können Sie Ihre Runbooks nach Azure Automation verschieben, damit diese ohne Änderungen mit Hybrid-Runbook-Worker verwendet werden. Hierbei wird vorausgesetzt, dass eine eigene Authentifizierung gegenüber den verwendeten Ressourcen durchgeführt wird, wie beschrieben unter [Erstellen von Runbooks für Hybrid-Runbook-Worker](#creating-runbooks-for-hybrid-runbook-worker). Runbooks werden in SMA im Kontext des Dienstkontos auf dem Workerserver ausgeführt, der möglicherweise die Authentifizierung für die Runbooks bereitstellt.

Sie können anhand der folgenden Kriterien prüfen, ob Azure Automation mit Hybrid-Runbook-Worker oder Service Management Automation die bessere Wahl für Ihre Anforderungen ist.

- SMA erfordert eine lokale Installation des Microsoft Azure Packs, das mehr lokale Ressourcen und einen höheren Wartungsaufwand erfordert als Azure Automation, bei dem nur ein Agent auf den lokalen Runbookworkern installiert wird. Die Agents werden von Operational Insights verwaltet, wodurch der Wartungsaufwand weiter sinkt.
- Azure Automation speichert Runbooks in der Cloud und übermittelt diese an lokale Hybrid-Runbook-Worker. Wenn Ihre Sicherheitsrichtlinie dieses Verhalten nicht zulässt, sollten Sie SMA verwenden.
- Microsoft Azure Pack steht als kostenloser Download bereit, während bei Azure Automation Abonnementkosten anfallen können. Azure: Muss mehrere Datenbanken für SMA verwalten.
- Azure Automation mit Hybrid-Runbook-Worker ermöglicht Ihnen das Verwalten von Runbooks für Cloudressourcen und lokale Ressourcen von einer zentralen Stelle, im Gegensatz zur getrennten Verwaltung über Azure Automation und SMA.
- Azure Automation bietet erweiterte Funktionen wie z. B. die grafische Erstellung, die in SMA nicht verfügbar ist.


## Verwandte Artikel

- [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md)
- [Bearbeiten eines Runbooks in Azure Automation](https://msdn.microsoft.com/library/dn879137.aspx)
 

<!---HONumber=July15_HO3-->