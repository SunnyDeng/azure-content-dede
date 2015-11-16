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
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/03/2015"
   ms.author="bwren" />

# Azure Automation – Hybrid-Runbook-Worker

Runbooks in Azure Automation können nicht auf Ressourcen in Ihrem lokalen Datencenter zugreifen, da sie in der Azure-Cloud ausgeführt werden. Die Funktion "Hybrid-Runbook-Worker" von Azure Automation ermöglicht Ihnen das Ausführen von Runbooks auf Computern in Ihrem Datencenter, um lokale Ressourcen verwalten zu können. Die Runbooks werden in Azure Automation gespeichert und verwaltet und bei der Ausführung an einen oder mehrere lokale Computer übermittelt.

Diese Funktionalität wird in der folgenden Abbildung veranschaulicht.

![Hybrid-Runbook-Worker – Übersicht](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-overview.png)

Sie können einen oder mehrere Computer in Ihrem Datencenter als Hybrid-Runbook-Worker einsetzen und Runbooks aus Azure Automation starten. Jeder Worker erfordert den Microsoft-Verwaltungs-Agent mit einer Verbindung zu Microsoft Operations Management Suite und der Azure Automation-Runbookumgebung. Operations Management Suite wird nur zum Installieren und Warten des Verwaltungs-Agents und zum Überwachen der Workerfunktionalität eingesetzt. Die Übermittlung von Runbooks und die Anweisung zu ihrer Ausführung erfolgt durch Azure Automation.

![Hybrid-Runbook-Worker – Komponenten](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-components.png)

>[AZURE.NOTE]Operational Insights wird gerade in Operations Management Suite integriert, und möglicherweise wird der eine oder der andere Name im Portal und in der Dokumentation verwendet.

Es gelten keine eingehenden Firewallanforderungen zur Unterstützung von Hybrid-Runbook-Workern. Der Agent auf dem lokalen Computer initiiert die gesamte Kommunikation mit Azure Automation in der Cloud. Wenn ein Runbook gestartet wird, erstellt Azure Automation eine Anweisung, die vom Agent abgerufen wird. Der Agent ruft anschließend das Runbook und alle Parameter über einen Pullvorgang ab. Außerdem werden alle im Runbook verwendeten [Objekte](http://msdn.microsoft.com/library/dn939988.aspx) aus Azure Automation abgerufen.

>[AZURE.NOTE]Hybrid Runbook Worker unterstützen gegenwärtig keine [DSC-Konfigurationen](automation-dsc-overview.md).

## Hybrid-Runbook-Workergruppen

Jeder Hybrid-Runbook-Worker ist ein Mitglied einer Hybrid-Runbook-Workergruppe, die Sie beim Installieren des Agents angeben. Eine Gruppe kann einen einzelnen Agent umfassen, aber für eine höhere Verfügbarkeit können Sie mehrere Agents installieren.

Wenn Sie ein Runbook auf einen Hybrid-Runbook-Worker starten, geben Sie die Gruppe an, auf der das Runbook ausgeführt werden soll. Die Mitglieder der Gruppe legen fest, welcher Worker die Anforderung verarbeitet. Sie können keinen bestimmten Worker angeben.

## Anforderungen von Hybrid Runbook Worker

Sie müssen mindestens einen lokalen Computer zum Ausführen von Hybridrunbookaufträgen festlegen. Dieser Computer muss Folgendes aufweisen:

- Windows Server 2012 oder höher
- Windows PowerShell 4.0 oder höher

Beachten Sie die folgenden Empfehlungen für Hybridworker:

- Legen Sie für hohe Verfügbarkeit in jeder Gruppe mehrere Hybrid-Worker fest.  
- Hybridworker können mit Service Management Automation- oder System Center Orchestrator-Runbookservern parallel betrieben werden.
- Es empfiehlt sich, einen Computer zu verwenden, der sich physisch in der Nähe oder in der Region des Automation-Kontos befindet, da die Auftragsdaten bei Abschluss eines Auftrags an Azure Automation zurückgesendet werden.

Firewallanforderungen:

- Der auf dem lokalen Computer ausgeführte Hybrid Runbook Worker muss an Port 443, 9.354 und 30.000-30.199 über ausgehenden Zugriff auf *.cloudapp.net verfügen.

## Installieren von Hybrid-Runbook-Worker
Das folgende Verfahren beschreibt die Installation und Konfiguration von Hybrid Runbook Worker. Führen Sie die ersten beiden Schritte einmal für Ihre Automation-Umgebung durch, und wiederholen Sie dann die verbleibenden Schritte für jeden Workercomputer.

### 1\. Erstellen eines Operations Management Suite-Arbeitsbereichs
Sofern Sie noch nicht über einen Operations Management Suite-Arbeitsbereich verfügen, erstellen Sie diesen mithilfe der Anweisungen unter [Einrichten Ihres Operational Insights-Arbeitsbereichs](../operational-insights/operational-insights-onboard-in-minutes.md). Wenn Sie bereits über einen Arbeitsbereich verfügen, können Sie diesen verwenden.

### 2\. Hinzufügen der Automation-Lösung zum Operations Management Suite-Arbeitsbereich
Die Funktionalität von Operations Management Suite kann durch Lösungen erweitert werden. Die Automation-Lösung fügt Azure Automation Funktionalität hinzu, einschließlich der Unterstützung für Hybrid Runbook Worker. Wenn Sie die Lösung Ihrem Arbeitsbereich hinzufügen, werden Workerkomponenten automatisch per Push auf den Agent-Computer übertragen, den Sie im nächsten Schritt installieren werden.

Befolgen Sie die Anweisungen unter [Hinzufügen einer Lösung über den Lösungskatalog](../operational-insights/operational-insights-setup-workspace.md#1-add-solutions), um Ihrem Operations Management Suite-Arbeitsbereich die Lösung **Automation** hinzuzufügen.

### 3\. Installieren des Microsoft-Verwaltungs-Agents
Der Microsoft-Verwaltungs-Agent verbindet Computer mit Operations Management Suite. Wenn Sie den Agent auf dem lokalen Computer installieren und mit Ihrem Arbeitsbereich verbinden, werden automatisch die erforderlichen Komponenten für Hybrid Runbook Worker heruntergeladen.

Befolgen Sie die Anweisungen unter [Direktes Verbinden von Computern mit Operational Insights](../operational-insights/operational-insights-direct-agent.md), um den Agent auf dem lokalen Computer zu installieren. Sie können diesen Vorgang für mehrere Computer wiederholen, um Ihrer Umgebung mehrere Worker hinzuzufügen.

Wenn der Agent erfolgreich mit Operations Management Suite verbunden ist, wird er im Operations Management Suite-Bereich **Einstellungen** auf der Registerkarte **Verbundene Datenquellen** aufgeführt. Sie können sicherstellen, dass der Agent die Automation-Lösung ordnungsgemäß heruntergeladen hat, indem Sie prüfen, ob unter „C:\\Programme\\Microsoft Monitoring Agent\\Agent“ ein Ordner namens **AzureAutomationFiles** vorhanden ist.

### 4\. Installieren der Runbookumgebung und Verbindungsherstellung mit Azure Automation
Wenn Sie Operations Management Suite einen Agent hinzufügen, lädt die Automation-Lösung per Push das PowerShell-Modul **HybridRegistration** herunter, in dem das Cmdlet **Add-HybridRunbookWorker** enthalten ist. Sie verwenden dieses Cmdlet zum Installieren der Runbookumgebung auf dem Computer und registrieren diesen bei Azure Automation.

Öffnen Sie eine PowerShell-Sitzung im Administratormodus, und führen Sie die folgenden Befehle zum Importieren des Moduls aus.

	cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation<version>\HybridRegistration"
	Import-Module HybridRegistration.psd1


Führen Sie dann das Cmdlet **Add-HybridRunbookWorker** mit der folgenden Syntax aus:

	Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

Sie finden die für dieses Cmdlet erforderlichen Informationen auf dem Blatt **Schlüssel verwalten** im Azure-Vorschauportal. Öffnen Sie dieses Blatt, indem Sie im Bereich "Elemente" für das Automation-Konto auf das Schlüsselsymbol klicken.

![Hybrid-Runbook-Worker – Übersicht](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

- **Name** ist der Name der Hybrid-Runbook-Workergruppe. Wenn die Gruppe bereits im Automation-Konto vorhanden ist, wird der aktuelle Computer der Gruppe hinzugefügt. Ist die Gruppe nicht vorhanden, wird sie hinzugefügt.
- **EndPoint** ist das Feld **URL** auf dem Blatt **Schlüssel verwalten**.
- **Token** ist der **Primäre Zugriffsschlüssel** im Blatt **Schlüssel verwalten**.  

Verwenden Sie den Schalter **-Verbose** mit **Add-HybridRunbookWorker**, um detaillierte Informationen über die Installation zu erhalten.

### 5\. Installieren von PowerShell-Modulen
Runbooks können beliebige Aktivitäten und Cmdlets der Module verwenden, die Sie in Ihrer Azure Automation-Umgebung installiert haben. Diese Module werden nicht automatisch auf den lokalen Computern bereitgestellt, sie müssen manuell installiert werden. Eine Ausnahme stellt das standardmäßig installierte Azure-Modul dar. Es bietet Zugriff auf Cmdlets für alle Azure-Dienste und -Aktivitäten für Azure Automation.

Da der primäre Zweck der Funktion "Hybrid-Runbook-Worker" darin besteht, lokale Ressourcen zu verwalten, müssen Sie sehr wahrscheinlich die Module zur Unterstützung dieser Ressourcen installieren. Unter [Installieren von Modulen](http://msdn.microsoft.com/library/dd878350.aspx) finden Sie Informationen zum Installieren von PowerShell-Modulen.

## Entfernen von Hybrid Runbook Worker

Sie können Hybrid Runbook Worker von einem lokalen Computer entfernen, indem Sie auf dem Computer das Cmdlet **Remove-HybridRunbookWorker** ausführen. Verwenden Sie den Schalter **-Verbose**, um ein ausführliches Protokoll zum Entfernungsvorgang zu erhalten.

## Starten von Runbooks auf einem Hybrid-Runbook-Worker

Unter [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md) werden die verschiedenen Methoden zum Starten eines Runbooks beschrieben. Hybrid-Runbook-Worker fügt die Option **RunOn** hinzu, mit der Sie den Namen einer Hybrid-Runbook-Workergruppe angeben können. Beim Angeben einer Gruppe wird das Runbook von den Workern in dieser Gruppe abgerufen und ausgeführt. Wird diese Option nicht angegeben, erfolgt die Ausführung in normaler Weise in Azure Automation.

Wenn Sie ein Runbook im Azure-Vorschauportal starten, wird die Option **Ausführen auf** angezeigt. Sie können entweder **Azure** oder **Hybrid-Worker** auswählen. Bei Auswahl von **Hybrid-Worker** können Sie anschließend aus einer Dropdownliste die gewünschte Gruppe auswählen.

Verwenden des Parameters **RunOn**: Mit dem folgenden Befehl wird unter Verwendung von Windows PowerShell ein Runbook namens "Test-Runbook" auf einer Hybrid-Runbook-Workergruppe mit dem Namen "MyHybridGroup" gestartet.

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

>[AZURE.NOTE]Der Parameter **RunOn** wurde dem Cmdlet **Start-AzureAutomationRunbook** in Version 0.9.1 von Microsoft Azure PowerShell hinzugefügt. Sie sollten die [neueste Version herunterladen](http://azure.microsoft.com/downloads), wenn auf Ihrem System eine Vorgängerversion installiert ist. Sie müssen diese Version auf einer Arbeitsstation installieren, auf der Sie das Runbook aus Windows PowerShell starten. Eine Installation auf dem Workercomputer ist nur erforderlich, wenn Sie Runbooks von diesem Computer starten möchten. Sie können ein Runbook auf einem Hybrid-Runbook-Worker derzeit nicht aus einem anderen Runbook starten, da hierzu die neueste Version von Azure PowerShell in Ihrem Automation-Konto installiert sein müsste. Die neueste Version wird in Kürze automatisch in Azure Automation aktualisiert und automatisch auf die Worker gepusht.

## Problembehandlung bei Runbooks auf Hybrid Runbook Worker

[Runbookausgabe und -meldungen](automation-runbook-output-and-messages.md) werden von Hybridworkern an Azure Automation gesendet, genau wie Runbookaufträge, die in der Cloud ausgeführt werden. Sie können auch die Verbose- und Progress-Streams auf dieselbe Weise aktivieren wie für andere Runbooks.

Protokolle werden lokal auf jedem Hybridworker unter "C:\\ProgramData\\Microsoft\\System Center\\Orchestrator\\7.2\\SMA\\Sandboxes" gespeichert.


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

- SMA erfordert eine lokale Installation des Microsoft Azure Packs, das mehr lokale Ressourcen und einen höheren Wartungsaufwand erfordert als Azure Automation, bei dem nur ein Agent auf den lokalen Runbookworkern installiert wird. Die Agents werden von Operations Management Suite verwaltet, wodurch der Wartungsaufwand weiter sinkt.
- Azure Automation speichert Runbooks in der Cloud und übermittelt diese an lokale Hybrid-Runbook-Worker. Wenn Ihre Sicherheitsrichtlinie dieses Verhalten nicht zulässt, sollten Sie SMA verwenden.
- Microsoft Azure Pack steht als kostenloser Download bereit, während bei Azure Automation Abonnementkosten anfallen können. Azure: Muss mehrere Datenbanken für SMA verwalten.
- Azure Automation mit Hybrid-Runbook-Worker ermöglicht Ihnen das Verwalten von Runbooks für Cloudressourcen und lokale Ressourcen von einer zentralen Stelle, im Gegensatz zur getrennten Verwaltung über Azure Automation und SMA.
- Azure Automation bietet erweiterte Funktionen wie z. B. die grafische Erstellung, die in SMA nicht verfügbar ist.


## Verwandte Artikel

- [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md)
- [Bearbeiten eines Runbooks in Azure Automation](https://msdn.microsoft.com/library/dn879137.aspx)
 

<!---HONumber=Nov15_HO2-->