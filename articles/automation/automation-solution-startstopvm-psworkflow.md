<properties 
	pageTitle="Starten und Beenden von virtuellen Computern mit Azure Automation - PowerShell-Workflow | Microsoft Azure"
	description="Grafische Version der Azure Automation-Lösung, einschließlich Runbooks zum Starten und Beenden von klassischen virtuellen Computern."
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
- Verwenden Sie die Runbooks als Tutorials zur Runbookerstellung. 

> [AZURE.SELECTOR]
- [Graphical](automation-solution-startstopvm-graphical.md)
- [PowerShell Workflow](automation-solution-startstopvm-psworkflow.md)

Dies ist die PowerShell-Workflow-Runbookversion dieser Lösung. Sie ist auch über [grafische Runbooks](automation-solutions-startstopvm-graphical.md) verfügbar.

## Abrufen der Lösung

Diese Lösung besteht aus zwei PowerShell-Workflow-Runbooks, die Sie unter den folgenden Links herunterladen können. Links zu den grafischen Runbooks finden Sie in der [grafischen Version](automation-solutions-startstopvm-graphical.md) dieser Lösung.

| Runbook | Link | Typ | Beschreibung |
|:---|:---|:---|:---|
| Start-AzureVMs | [Starten von klassischen virtuellen Azure-Computern](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | PowerShell-Workflow | Startet alle klassischen virtuellen Computer eines Azure-Abonnements oder alle virtuellen Computer mit einem bestimmten Dienstnamen. |
| Stop-AzureVMs | [Beenden von klassischen virtuellen Azure-Computern](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | PowerShell-Workflow | Beendet alle virtuellen Computer eines Automatisierungskontos oder alle virtuellen Computer mit einem bestimmten Dienstnamen. |


## Installieren der Lösung

### 1\. Installieren der Runbooks

Nach dem Herunterladen der Runbooks können Sie sie mit dem Verfahren unter [Importieren eines Runbooks](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook) importieren.

### 2\. Überprüfen der Beschreibung und der Anforderungen
Die Runbooks enthalten einen kommentierten Hilfetext mit einer Beschreibung und den erforderlichen Assets. Diese Informationen erhalten Sie auch in diesem Artikel.

### 3\. Konfigurieren von Assets
Die Runbooks erfordern die folgenden Assets, die Sie erstellen und mit den entsprechenden Werten füllen müssen.

| Assettyp | Assetname | Beschreibung |
|:---|:---|:---|:---|
| Credential | AzureCredential | Enthält die Anmeldeinformationen für ein Konto mit der Berechtigung zum Starten und Beenden virtueller Computer im Azure-Abonnement. Alternativ können Sie ein anderes Anmeldeinformationsasset im Parameter **Credential** der Aktivität **Add-AzureAccount** angeben. |
| Variable | AzureSubscriptionId | Enthält die Abonnement-ID Ihres Azure-Abonnements. |

## Verwenden der Lösung

### Parameter

Die Runbooks verfügen jeweils über die folgenden Parameter. Sie müssen Werte für alle obligatorischen Parameter angeben und können optional Werte für andere Parameter je nach Bedarf bereitstellen.

| Parameter | Typ | Erforderlich | Beschreibung |
|:---|:---|:---|:---|
| ServiceName | string | Nein | Wenn ein Wert angegeben wird, werden alle virtuellen Computer mit diesem Dienstnamen gestartet oder beendet. Wenn kein Wert angegeben wird, werden alle klassischen virtuellen Computer im Azure-Abonnement gestartet oder beendet. |
| AzureSubscriptionIdAssetName | string | Nein | Enthält den Namen des [Variablen-Assets](#installing-the-solution), in dem die Abonnement-ID des Azure-Abonnements enthalten ist. Wenn Sie keinen Wert angeben, wird *AzureSubscriptionId* verwendet. |
| AzureCredentialAssetName | string | Nein | Enthält den Namen des [Anmeldeinformationsassets](#installing-the-solution), in dem die Anmeldeinformationen für das zu verwendende Runbook enthalten sind. Wenn Sie keinen Wert angeben, wird *AzureCredential* verwendet. |

### Starten der Runbooks

Sie können eine Methode unter [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md) verwenden, um die Runbooks in dieser Lösung zu starten.

In den folgenden Beispielbefehlen wird Windows PowerShell verwendet, um **StartAzureVMs** auszuführen und alle virtuellen Computer mit dem Dienstnamen *MyVMService* zu starten.

	$params = @{"ServiceName"="MyVMService"}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### Ausgabe

Die Runbooks [geben eine Meldung für jeden virtuellen Computer aus](automation-runbook-output-and-messages.md), um anzuzeigen, ob die Anweisung zum Starten bzw. Beenden erfolgreich übermittelt wurde. Sie können in der Ausgabe nach einer bestimmten Zeichenfolge suchen, um das Ergebnis für jedes Runbook zu ermitteln. In der folgenden Tabelle sind die möglichen ausgegebenen Zeichenfolgen aufgeführt.

| Runbook | Bedingung | Message |
|:---|:---|:---|
| Start-AzureVMs | Virtueller Computer wird bereits ausgeführt | MyVM wird bereits ausgeführt |
| Start-AzureVMs | Startanforderung für virtuellen Computer wurde übermittelt | MyVM wurde gestartet |
| Start-AzureVMs | Fehler bei der Startanforderung für den virtuellen Computer | MyVM konnte nicht gestartet werden |
| Stop-AzureVMs | Virtueller Computer wird bereits ausgeführt | MyVM wurde bereits beendet |
| Stop-AzureVMs | Startanforderung für virtuellen Computer wurde übermittelt | MyVM wurde gestartet |
| Stop-AzureVMs | Fehler bei der Startanforderung für den virtuellen Computer | MyVM konnte nicht gestartet werden |

Der folgende Codeausschnitt aus einem Runbook versucht beispielsweise, alle virtuellen Computer mit dem Dienstnamen *MyServiceName* zu starten. Wenn eine der Startanforderungen einen Fehler verursacht, können Fehleraktionen ausgeführt werden.

	$results = Start-AzureVMs -ServiceName "MyServiceName"
	foreach ($result in $results) {
		if ($result -like "* has been started" ) {
			# Action to take in case of success.
		}
		else {
			# Action to take in case of error.
		}
	}


## Detaillierte Aufschlüsselung

Es folgt eine detaillierte Aufschlüsselung der Runbooks in dieser Lösung. Sie können diese Informationen verwenden, um die Runbooks anzupassen oder lediglich als Referenz zu verwenden.

### Parameter

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

Der Workflow startet durch Abrufen der Werte für die [Eingabeparameter](#using-the-solution). Wenn die Assetnamen nicht bereitgestellt werden, werden Standardnamen verwendet.

### Ausgabe

    # Returns strings with status messages
    [OutputType([String])]

Diese Zeile deklariert, dass es sich bei der Ausgabe des Runbooks um eine Zeichenfolge handelt. Dies ist nicht erforderlich, wird jedoch für den Fall empfohlen, dass das Runbook als [untergeordnetes Runbook](automation-child-runbooks.md) verwendet wird. So wird einem übergeordneten Runbook mitgeteilt, welcher Ausgabetyp zu erwarten ist.

### Authentifizierung

	# Connect to Azure and select the subscription to work against
	$Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
	$null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
	$SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

In den nächsten Zeilen werden die [Anmeldeinformationen](automation-configuring.md#configuring-authentication-to-azure-resources) und das Azure-Abonnement für den Rest des Runbooks festgelegt. Zunächst verwenden wir **Get-AutomationPSCredential**, um das Asset abzurufen, das die Anmeldeinformationen mit Zugriff auf das Starten und Beenden der virtuellen Computer im Azure-Abonnement enthält. **Add-AzureAccount** verwendet dann dieses Asset zum Festlegen der Anmeldeinformationen. Die Ausgabe wird einer Dummyvariablen zugewiesen, damit sie nicht in der Runbookausgabe enthalten ist.

Das Variablenasset mit der Abonnement-ID wird dann mit **Get-AutomationVariable** abgerufen, und das Abonnement wird mit **Select-AzureSubscription** festgelegt.

### Abrufen virtueller Computer

	# If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
	{ 
		$VMs = Get-AzureVM -ServiceName $ServiceName
	}
    else 
	{ 
		$VMs = Get-AzureVM
	}

Mit **Get-AzureVM** werden die virtuellen Computer abgerufen, mit denen das Runbook arbeiten soll. Wenn ein Wert in der Eingabevariablen **ServiceName** bereitgestellt wird, werden nur die virtuellen Computer mit dem jeweiligen Dienstnamen abgerufen. Wenn **ServiceName** leer ist, werden alle virtuellen Computer abgerufen.

### Starten/Beenden virtueller Computer und Senden der Ausgabe

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
		if ($VM.PowerState -eq "Started")
		{
			# The VM is already started, so send notice
			Write-Output ($VM.InstanceName + " is already running")
		}
		else
		{
			# The VM needs to be started
        	$StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

	        if ($StartRtn.OperationStatus -ne 'Succeeded')
	        {
				# The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
	        }
			else
			{
				# The VM started, so send notice
				Write-Output ($VM.InstanceName + " has been started")
			}
		}
    }

In den nächsten Zeilen werden die einzelnen virtuellen Computer schrittweise durchlaufen. Zuerst wird der **PowerState** des virtuellen Computers überprüft, um festzustellen, ob er je nach Runbook bereits ausgeführt wird oder beendet wurde. Wenn er sich bereits im Zielstatus befindet, wird eine Meldung an die Ausgabe gesendet und das Runbook beendet. Andernfalls wird mit **Start-AzureVM** oder **Stop-AzureVM** versucht, den virtuellen Computer zu starten oder zu beenden. Das Ergebnis der Anforderung wird in einer Variablen gespeichert. Eine Meldung wird dann zur Ausgabe gesendet, die angibt, ob die Anforderung zum Starten oder Beenden erfolgreich gesendet wurde.


## Verwandte Artikel

- [Untergeordnete Runbooks in Azure Automation](automation-child-runbooks.md) 
- [Runbookausgabe und -meldungen in Azure Automation](automation-runbook-output-and-messages.md)

<!---HONumber=Oct15_HO1-->