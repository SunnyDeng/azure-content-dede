<properties 
	pageTitle="Starten und Beenden von virtuellen Computern – Graph | Microsoft Azure"
	description="PowerShell-Workflow-Version der Azure Automation-Projektmappe, einschließlich Runbooks zum Starten und Beenden von klassischen virtuellen Computern."
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
	ms.date="09/25/2015"
	ms.author="bwren" />

# Azure Automation-Lösung – Starten und Beenden virtueller Computer

Diese Azure Automation-Lösung enthält Runbooks zum Starten und Beenden klassischer virtueller Computer. Sie können die Lösung für folgende Zwecke einsetzen:

- Verwenden Sie die Runbooks unverändert in Ihrer eigenen Umgebung. 
- Ändern Sie die Runbooks, um benutzerdefinierte Funktionen auszuführen.  
- Rufen Sie die Runbooks im Rahmen einer Gesamtlösung aus einem anderen Runbook auf. 
- Verwenden Sie die Runbooks als Lernprogramme in Schulungen zur Runbookerstellung. 

> [AZURE.SELECTOR]
- [Graphical](automation-solution-startstopvm-graphical.md)
- [PowerShell Workflow](automation-solution-startstopvm-psworkflow.md)

Dies ist die grafische Runbookversion dieser Projektmappe. Sie ist auch über [PowerShell-Workflow-Runbooks](automation-solutions-startstopvm-psworkflow.md) verfügbar.

## Abrufen der Projektmappe

Diese Projektmappe besteht aus zwei grafischen Runbooks, die Sie unter den folgenden Links herunterladen können. Links zu den PowerShell-Workflow-Runbooks finden Sie unter der [PowerShell-Workflowversion](automation-solutions-startstopvm-psworkflow.md) dieser Projektmappe.


| Runbook | Link | Typ | Beschreibung |
|:---|:---|:---|:---|
| StartAzureClassicVM | [Starten des grafischen Azure Classic VM-Runbooks](https://gallery.technet.microsoft.com/scriptcenter/Start-Azure-Classic-VM-c6067b3d) | Grafisch | Startet alle klassischen virtuellen Computer eines Azure-Abonnements oder alle virtuellen Computer mit einem bestimmten Dienstnamen. |
| StopAzureClassicVM | [Beenden des grafischen Azure Classic VM-Runbooks](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-Classic-VM-397819bd) | Grafisch | Beendet alle virtuellen Computer eines Automatisierungskontos oder alle virtuellen Computer mit einem bestimmten Dienstnamen. |


## Installieren der Lösung

### 1\. Installieren der Runbooks

Nach dem Herunterladen der Runbooks können Sie sie mit dem Verfahren unter [Grafische Runbooks – Verfahren](automation-graphical-authoring-intro.md#graphical-runbook-procedures) importieren.

### 2\. Überprüfen der Beschreibung und der Anforderungen
Die Runbooks enthalten eine Aktivität namens **Read Me** mit einer Beschreibung und den erforderlichen Assets. Sie können diese Informationen anzeigen, indem Sie die **Read Me**-Aktivität und dann den Parameter **Workflow Script** auswählen. Diese Informationen erhalten Sie auch in diesem Artikel.

### 3\. Konfigurieren von Assets
Die Runbooks erfordern die folgenden Assets, die Sie erstellen und mit den entsprechenden Werten füllen müssen. Die Namen sind standardmäßige Namen. Sie können Assets mit unterschiedlichen Namen verwenden, wenn Sie diese Namen beim Starten des Runbooks in den [Eingabeparametern](#using-the-solution) angeben.

| Assettyp | Standardname | Beschreibung |
|:---|:---|:---|:---|
| [Credential](automation-credentials.md) | AzureCredential | Enthält die Anmeldeinformationen für ein Konto mit der Berechtigung zum Starten und Beenden von virtuellen Computern im Azure-Abonnement. |
| [Variable](automation-variables.md) | AzureSubscriptionId | Enthält die Abonnement-ID Ihres Azure-Abonnements. |

## Verwenden der Lösung

### Parameter

Die Runbooks verfügen jeweils über die folgenden [Eingabeparameter](automation-starting-a-runbook#runbook-parameters). Sie müssen Werte für alle obligatorischen Parameter angeben und können optional Werte für andere Parameter je nach Bedarf bereitstellen.

| Parameter | Typ | Erforderlich | Beschreibung |
|:---|:---|:---|:---|
| ServiceName | string | Nein | Wenn ein Wert angegeben wird, werden alle virtuellen Computer mit diesem Dienstnamen gestartet oder beendet. Wenn kein Wert angegeben wird, werden alle klassischen virtuellen Computer im Azure-Abonnement gestartet oder beendet. |
| AzureSubscriptionIdAssetName | string | Nein | Enthält den Namen des [Variablen-Assets](#installing-the-solution), in dem die Abonnement-ID des Azure-Abonnements enthalten ist. Wenn Sie keinen Wert angeben, wird *AzureSubscriptionId* verwendet. |
| AzureCredentialAssetName | string | Nein | Enthält den Namen des [Anmeldeinformations-Assets](#installing-the-solution), in dem die Anmeldeinformationen für das zu verwendende Runbook enthalten sind. Wenn Sie keinen Wert angeben, wird *AzureCredential* verwendet. |

### Starten der Runbooks

Sie können eine Methode unter [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md) verwenden, um die Runbooks in dieser Projektmappe zu starten.

Im folgenden Beispielbefehl wird Windows PowerShell verwendet, um **StartAzureClassicVM** auszuführen und alle virtuellen Computer mit dem Dienstnamen *MyVMService* zu starten.

	$params = @{"ServiceName"="MyVMService"}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "StartAzureClassicVM" –Parameters $params

### Ausgabe

Die Runbooks [geben eine Meldung für jeden virtuellen Computer aus](automation-runbook-output-and-messages.md), um anzuzeigen, ob die Anweisung zum Starten bzw. Beenden erfolgreich übermittelt wurde. Sie können in der Ausgabe nach einer bestimmten Zeichenfolge suchen, um das Ergebnis für jedes Runbook zu ermitteln. In der folgenden Tabelle sind die möglichen ausgegebenen Zeichenfolgen aufgeführt.

| Runbook | Bedingung | Meldung |
|:---|:---|:---|
| StartAzureClassicVM | Virtueller Computer wird bereits ausgeführt | MyVM wird bereits ausgeführt |
| StartAzureClassicVM | Startanforderung für virtuellen Computer wurde übermittelt | MyVM wurde gestartet |
| StartAzureClassicVM | Fehler bei der Startanforderung für den virtuellen Computer | MyVM konnte nicht gestartet werden |
| StopAzureClassicVM | Virtueller Computer wird bereits ausgeführt | MyVM wurde bereits beendet |
| StopAzureClassicVM | Startanforderung für virtuellen Computer wurde übermittelt | MyVM wurde gestartet |
| StopAzureClassicVM | Fehler bei der Startanforderung für den virtuellen Computer | MyVM konnte nicht gestartet werden |


Unten sehen Sie eine Abbildung der Verwendung von **StartAzureClassicVM** als [untergeordnetes Runbook](automation-child-runbooks.md) in einem grafischen Beispielrunbook. Hierbei werden die bedingten Links in der folgenden Tabelle verwendet.

| Link | Kriterien |
|:---|:---|
| Link bei Erfolg | $ActivityOutput['StartAzureClassicVM'] -like "* wurde gestartet" |
| Link bei Fehler | $ActivityOutput['StartAzureClassicVM'] -notlike "* wurde gestartet" |

![Beispiel für untergeordnetes Runbook](media/automation-solution-startstopvm/graphical-childrunbook-example.png)


## Detaillierte Aufschlüsselung

Es folgt eine detaillierte Aufschlüsselung der Runbooks in dieser Projektmappe. Sie können diese Informationen verwenden, um die Runbooks anzupassen oder um davon für die Erstellung Ihrer eigenen Projektmappen zu lernen.
 

### Authentifizierung

![Authentifizierung](media/automation-solution-startstopvm/graphical-authentication.png)

Das Runbook beginnt mit Aktivitäten zum Festlegen der [Anmeldeinformationen](automation-configuring.md#configuring-authentication-to-azure-resources) und des Azure-Abonnements, die bzw. das für den Rest des Runbooks verwendet werden.

Mit den ersten beiden Aktivitäten (**Get Subscription Id** und **Get Azure Credential**) werden die [Assets](#installing-the-solution) abgerufen, die von den nächsten beiden Aktivitäten verwendet werden. Bei diesen Aktivitäten können die Assets direkt angegeben werden, aber hierfür sind die Namen der Assets erforderlich. Da wir zulassen, dass Benutzer diese Namen in den [Eingabeparametern](#using-the-solution) angeben, benötigen wir diese Aktivitäten zum Abrufen der Assets mit einem Namen, der über einen Eingabeparameter angegeben wird.

Mit **Add-AzureAccount** werden die Anmeldeinformationen festgelegt, die für den Rest des Runbooks verwendet werden. Das Asset mit den Anmeldeinformationen, das über **Get Azure Credential** abgerufen wird, muss Zugriff auf das Starten und Beenden von virtuellen Computern im Azure-Abonnement haben. Das verwendete Abonnement wird mit **Select-AzureSubscription** ausgewählt, wobei die Abonnement-ID aus **Get Subscription Id** verwendet wird.

### Abrufen virtueller Computer

![VMs abrufen](media/automation-solution-startstopvm/graphical-getvms.png)

Das Runbook muss ermitteln, welche virtuellen Computer verwendet werden und ob diese bereits gestartet oder beendet wurden (je nach Runbook). Mit einer der beiden Aktivitäten werden die virtuellen Computer abgerufen. **Get VMs in Service** wird ausgeführt, wenn der Eingabeparameter *ServiceName* für das Runbook einen Wert enthält. **Get All VMs** wird ausgeführt, wenn der Eingabeparameter *ServiceName* für das Runbook keinen Wert enthält. Diese Logik wird von den bedingten Links vor jeder Aktivität ausgeführt.

Beide Aktivitäten verwenden das **Get-AzureVM**-Cmdlet. **Get All VMs** verwendet den Parametersatz **ListAllVMs** zum Zurückgeben aller virtuellen Computer. **Get VMs in Service** verwendet den Parametersatz **GetVMByServiceAndVMName** und stellt den Eingabeparameter **ServiceName** für den Parameter **ServiceName** bereit.

### Zusammenführen von virtuellen Computern

![VMs zusammenführen](media/automation-solution-startstopvm/graphical-mergevms.png)

Die Aktivität **Merge VMs** ist erforderlich, um die Eingabe für **Start-AzureVM** sicherzustellen. Hierfür werden Name und Dienstname der zu startenden virtuellen Computer benötigt. Diese Eingabe kann entweder von **Get All VMs** oder **Get VMs in Service** stammen, aber **Start-AzureVM** kann nur eine Aktivität für die Eingabe angeben.

Die Lösung besteht in der Erstellung des **Merge VMs**-Elements, von dem das **Write-Output**-Cmdlet ausgeführt wird. Der Parameter **InputObject** für dieses Cmdlet ist ein PowerShell-Ausdruck, mit dem die Eingabe der beiden vorherigen Aktivitäten kombiniert wird. Es wird nur eine dieser Aktivitäten ausgeführt, sodass nur ein Ausgabesatz erwartet wird. Von **Start-AzureVM** kann diese Ausgabe für seine Eingabeparameter verwendet werden.

### Starten und Beenden von virtuellen Maschinen

![VMs starten](media/automation-solution-startstopvm/graphical-startvm.png) ![VMs beenden](media/automation-solution-startstopvm/graphical-stopvm.png)

Je nach Runbook wird bei den nächsten Aktivitäten versucht, das Runbook mit **Start-AzureVM** bzw. **Stop-AzureVM** zu starten oder zu beenden. Da der Aktivität ein Pipelinelink vorangestellt ist, wird sie einmal für jedes Objekt ausgeführt, das von **Merge VMs** zurückgegeben wird. Der Link ist bedingt. Die Aktivität wird daher nur ausgeführt, wenn das *RunningState*-Element des virtuellen Computers *Stopped* für **Start-AzureVM** und *Started* für **Stop-AzureVM** lautet. Wenn diese Bedingung nicht erfüllt ist, wird **Notify Already Started** oder **Notify Already Stopped** ausgeführt, um per **Write-Output** eine Nachricht zu senden.

### Senden der Ausgabe

![Benachrichtigung über Starten von VMs](media/automation-solution-startstopvm/graphical-notifystart.png) ![Benachrichtigung über Beenden von VMs](media/automation-solution-startstopvm/graphical-notifystop.png)

Der letzte Schritt im Runbook ist das Senden einer Ausgabe mit der Information, ob die Anforderung zum Starten oder Beenden für jeden virtuellen Computer erfolgreich übermittelt wurde. Es ist jeweils eine separate **Write-Output**-Aktivität vorhanden, und mit bedingten Links ermitteln wir, welche ausgeführt werden soll. **Notify VM Started** oder **Notify VM Stopped** wird ausgeführt, wenn *OperationStatus* den Zustand *Succeeded* aufweist. Wenn *OperationStatus* einen anderen Wert aufweist, wird **Notify Failed To Start** oder **Notify Failed to Stop** ausgeführt.


## Verwandte Artikel

- [Grafische Erstellung in Azure Automation](automation-graphical-authoring-intro.md)
- [Untergeordnete Runbooks in Azure Automation](automation-child-runbooks.md) 
- [Runbookausgabe und -meldungen in Azure Automation](automation-runbook-output-and-messages.md)

<!---HONumber=Oct15_HO1-->