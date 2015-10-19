<properties 
   pageTitle="Hinzufügen von Azure Automation-Runbooks zu Wiederherstellungsplänen" 
   description="In diesem Artikel erfahren Sie, wie Sie mit Azure Site Recovery und Azure Automation Wiederherstellungspläne erweitern und komplexe Aufgaben bei der Wiederherstellung zu Azure durchführen können." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="10/07/2015"
   ms.author="ruturajd@microsoft.com"/>

  
   

# Hinzufügen von Azure Automation-Runbooks zu Wiederherstellungsplänen


Dieses Lernprogramm beschreibt die Integration von Azure Site Recovery und Azure Automation, um die Erweiterung von Wiederherstellungsplänen zu ermöglichen. Wiederherstellungspläne können die Wiederherstellung Ihrer durch Azure Site Recovery geschützten virtuellen Computer sowohl bei der Replikation zur sekundären Cloud als auch bei der Replikation zu Azure orchestrieren. Darüber hinaus tragen sie zu einer **durchgängig exakten**, **wiederholbaren**, und **automatisierten** Wiederherstellung bei. Beim Failover virtueller Computer zu Azure erweitert die Azure Automation-Integration die Wiederherstellungspläne und ermöglicht die Ausführung von Runbooks für beeindruckende Automatisierungsaufgaben.

Falls Sie noch nicht mit Azure Automation vertraut sind, können Sie sich [hier](http://azure.microsoft.com/services/automation/) registrieren und [hier](http://azure.microsoft.com/documentation/scripts/) die entsprechenden Beispielskripts herunterladen. Weitere Informationen zu [Azure Site Recovery](http://azure.microsoft.com/services/site-recovery/) sowie zur Orchestrierung der Wiederherstellung in Azure mithilfe von Wiederherstellungsplänen finden Sie [hier](http://azure.microsoft.com/blog/?p=166264).

In diesem Lernprogramm erfahren Sie, wie Sie Azure Automation-Runbooks in Wiederherstellungspläne integrieren. Wir automatisieren einfache Aufgaben, für die zuvor ein manueller Eingriff erforderlich war, und konvertieren eine Wiederherstellung mit mehreren Schritten in eine Wiederherstellungsaktion, die sich mit nur einem Klick durchführen lässt. Außerdem erfahren Sie, wie Sie Probleme in einem einfachen Skript beheben können.

## Schützen der Anwendung in Azure

Wir beginnen mit einer einfachen Anwendung mit zwei virtuellen Computern. Hier haben wir eine HRweb-Anwendung von Fabrikam. „Fabrikam-Hrweb-frontend“ und „Fabrikam-Hrweb-backend“ sind die beiden virtuellen Computer, die mithilfe von Azure Site Recovery in Azure geschützt werden. Gehen Sie wie folgt vor, um die virtuellen Computer mit Azure Site Recovery zu schützen:

1.  Aktivieren Sie den Schutz für Ihre virtuellen Computer.

2.  Vergewissern Sie sich, dass die erste Replikation der virtuellen Computer abgeschlossen ist und dass die Replikation funktioniert.

3.  Warten Sie, bis die erste Replikation abgeschlossen ist und der Replikationsstatus „Geschützt“ angezeigt wird.

![](media/site-recovery-runbook-automation/01.png)
---------------------

In diesem Lernprogramm erstellen wir einen Wiederherstellungsplan für die HRweb-Anwendung von Fabrikam, um für die Anwendung ein Failover zu Azure durchzuführen. Anschließend integrieren wir den Plan in ein Runbook, das nach dem Failover auf dem virtuellen Azure-Computer einen Endpunkt für die Bereitstellung von Webseiten über den Port 80 erstellt.

Zuerst erstellen wir einen Wiederherstellungsplan für unsere Anwendung.

## Erstellen des Wiederherstellungsplans

Für die Wiederherstellung der Anwendung in Azure müssen Sie einen Wiederherstellungsplan erstellen. In einem Wiederherstellungsplan können Sie die gewünschte Wiederherstellungsreihenfolge für die virtuellen Computer angeben. Der virtuelle Computer aus der ersten Gruppe wird als erster wiederhergestellt und gestartet. Danach folgt der virtuelle Computer aus der zweiten Gruppe.

Erstellen Sie einen Wiederherstellungsplan wie den folgenden:

![](media/site-recovery-runbook-automation/12.png)

Weitere Informationen zu Wiederherstellungsplänen finden Sie in der [Dokumentation](https://msdn.microsoft.com/library/azure/dn788799.aspx "hier").

Im nächsten Schritt erstellen wir die erforderlichen Artefakte in Azure Automation.

## Erstellen des Automation-Kontos und der dazugehörigen Ressourcen

Für die Erstellung von Runbooks benötigen Sie ein Azure Automation-Konto. Falls Sie noch kein Konto besitzen, navigieren Sie zur Registerkarte für Azure Automation (![](media/site-recovery-runbook-automation/02.png)), und erstellen Sie ein neues Konto.

1.  Benennen Sie das Konto.

2.  Geben Sie eine geografische Region für das Konto an.

Es empfiehlt sich, für das Konto die gleiche Region zu verwenden wie für den ASR-Tresor.

![](media/site-recovery-runbook-automation/03.png)

Erstellen Sie anschließend folgende Ressourcen in dem Konto:

### Hinzufügen eines Abonnements als Ressource

1.  Fügen Sie den Azure Automation-Ressourcen eine neue Einstellung hinzu (![](media/site-recovery-runbook-automation/04.png)), und klicken Sie auf ![](media/site-recovery-runbook-automation/05.png).

2.  Wählen Sie als Variablentyp die Option **Zeichenfolge** aus.

3.  Geben Sie als Variablenname die Zeichenfolge **AzureSubscriptionName** an.

    ![](media/site-recovery-runbook-automation/06.png)

4.  Geben Sie als Wert der Variablen den tatsächlichen Namen Ihres Azure-Abonnements an.

	![](media/site-recovery-runbook-automation/07_1.png)

Den Namen Ihres Abonnements finden Sie im Azure-Portal auf der Seite mit den Einstellungen für Ihr Konto.

### Hinzufügen der Azure-Anmeldeinformationen als Ressource

Azure Automation stellt mithilfe von Azure PowerShell eine Verbindung mit dem Abonnement her und verwendet die Artefakte dort. Dazu ist eine Authentifizierung mit Ihrem Microsoft-Konto oder mit einem Geschäfts- oder Schulkonto erforderlich. Sie können die Anmeldeinformationen für das Konto in einer Ressource speichern, damit das Runbook sie gefahrlos verwenden kann.

1.  Fügen Sie den Azure Automation-Ressourcen eine neue Einstellung hinzu (![](media/site-recovery-runbook-automation/04.png)), und klicken Sie auf ![](media/site-recovery-runbook-automation/09.png).

2.  Legen Sie den Anmeldeinformationstyp auf **Windows PowerShell-Anmeldeinformationen** fest.

3.  Geben Sie als Name die Zeichenfolge **AzureCredential** an.

    ![](media/site-recovery-runbook-automation/10.png)

4.  Geben Sie den Benutzernamen und das Kennwort für die Anmeldung an.

Damit stehen die beiden Einstellungen in Ihren Ressourcen zur Verfügung.

![](media/site-recovery-runbook-automation/11.png)

Weitere Informationen zum Herstellen einer Verbindung mit Ihrem Abonnement über PowerShell finden Sie [hier](../install-configure-powershell.md).

Im nächsten Schritt wird in Azure Automation ein Runbook erstellt, das nach dem Failover einen Endpunkt für den virtuellen Front-End-Computer hinzufügen kann.

## Azure Automation-Kontext

ASR übergibt eine Kontextvariable an das Runbook, um Sie beim Schreiben deterministischer Skripts zu unterstützen. Man könnte argumentieren, dass die Namen von Cloud-Dienst und virtuellem Computer vorhersehbar sind, dies ist aber nicht immer der Fall – beispielsweise in einem Szenario, in dem sich der Name des virtuellen Computers aufgrund von nicht unterstützten Zeichen in Azure geändert hat. Aus diesem Grund wird diese Information als Teil des *Kontexts* an den ASR-Wiederherstellungsplan übergeben.

Hier sehen Sie ein Beispiel für die Kontextvariable:

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


Die folgende Tabelle enthält jeweils den Namen und die Beschreibung der einzelnen Variablen im Kontext:

**Variablenname** | **Beschreibung**
---|---
RecoveryPlanName | Name des ausgeführten Plans. Ermöglicht die Durchführung von Aktionen auf der Grundlage des Namens mithilfe des gleichen Skripts.
FailoverType | Gibt an, ob es sich um ein Testfailover, um ein geplantes Failover oder um ein nicht geplantes Failover handelt. 
FailoverDirection | Gibt an, ob die Wiederherstellung auf den primären oder sekundären Standort erfolgt.
GroupID | Identifiziert die Gruppennummer innerhalb des Wiederherstellungsplans, wenn der Plan ausgeführt wird
VmMap | Array mit allen virtuellen Computern in der Gruppe.
VMMap-Schlüssel | Eindeutiger Schlüssel (GUID) für jeden virtuellen Computer. Die GUID entspricht der VMM-ID des virtuellen Computers, sofern zutreffend. 
RoleName | Name der Azure-VM, die wiederhergestellt wird.
CloudServiceName | Name des Azure Cloud-Diensts, unter dem der virtuelle Computer erstellt wird.


Den kontextspezifischen VmMap-Schlüssel können Sie in ASR auf der Eigenschaftenseite des virtuellen Computers unter der VM-GUID-Eigenschaft ermitteln.

![](media/site-recovery-runbook-automation/13.png)

## Erstellen eines Automation-Runbooks

Erstellen Sie nun das Runbook, um auf dem virtuellen Front-End-Computer den Port 80 zu öffnen.

1.  Erstellen Sie im Azure Automation-Konto ein neues Runbook mit dem Namen **OpenPort80**.

![](media/site-recovery-runbook-automation/14.png)

2.  Navigieren Sie zur Erstelleransicht des Runbooks, und starten Sie den Entwurfsmodus.

3.  Geben Sie zunächst die Variable für den Wiederherstellungsplankontext an.

```
	param (
		[Object]$RecoveryPlanContext
	)

```
  

4.  Stellen Sie dann unter Angabe der Anmeldeinformationen und des Abonnementnamens eine Verbindung mit dem Abonnement her.

```
	$Cred = Get-AutomationPSCredential -Name 'AzureCredential'
	
	# Connect to Azure
	$AzureAccount = Add-AzureAccount -Credential $Cred
	$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
	Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
```

> Hinweis: Hier werden die Azure-Ressourcen **AzureCredential** und **AzureSubscriptionName** verwendet.

5.  Geben Sie die Endpunktdetails und die GUID des virtuellen Computers an, für den Sie den Endpunkt verfügbar machen möchten (in diesem Fall der virtuelle Front-End-Computer).

```
	# Specify the parameters to be used by the script
	$AEProtocol = "TCP"
	$AELocalPort = 80
	$AEPublicPort = 80
	$AEName = "Port 80 for HTTP"
	$VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
```

Hierbei werden das Azure-Endpunktprotokoll, der lokale Port auf dem virtuellen Computer und der ihm zugeordnete öffentliche Port angegeben. Bei diesen Variablen handelt es sich um erforderliche Parameter für die Azure-Befehle, die virtuellen Computern Endpunkte hinzufügen. Der VMGUID-Parameter enthält die GUID des virtuellen Computers, auf dem Sie arbeiten müssen.

6.  Das Skript extrahiert nun den Kontext für die angegebene VM-GUID und erstellt einen Endpunkt auf dem entsprechenden virtuellen Computer.

```
	#Read the VM GUID from the context
	$VM = $RecoveryPlanContext.VmMap.$VMGUID

	if ($VM -ne $null)
	{
		# Invoke pipeline commands within an InlineScript

		$EndpointStatus = InlineScript {
			# Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
			# This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

			$Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
				Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
				Update-AzureVM
			Write-Output $Status
		}
	}
```

7. Klicken Sie anschließend auf die Veröffentlichungsoption (![](media/site-recovery-runbook-automation/20.png)), um das Skript für die Ausführung verfügbar zu machen. 

Hier sehen Sie noch einmal das gesamte Skript:

```
  workflow OpenPort80
  {
	param (
		[Object]$RecoveryPlanContext
	)

	$Cred = Get-AutomationPSCredential -Name 'AzureCredential'
	
	# Connect to Azure
	$AzureAccount = Add-AzureAccount -Credential $Cred
	$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
	Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

	# Specify the parameters to be used by the script
	$AEProtocol = "TCP"
	$AELocalPort = 80
	$AEPublicPort = 80
	$AEName = "Port 80 for HTTP"
	$VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
	
	#Read the VM GUID from the context
	$VM = $RecoveryPlanContext.VmMap.$VMGUID

	if ($VM -ne $null)
	{
		# Invoke pipeline commands within an InlineScript

		$EndpointStatus = InlineScript {
			# Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
			# This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

			$Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
				Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
				Update-AzureVM
			Write-Output $Status
		}
	}
  }
```

## Hinzufügen des Skripts zum Wiederherstellungsplan

Wenn das Skript bereit ist, können Sie es dem zuvor erstellten Wiederherstellungsplan hinzufügen.

1.  Fügen Sie im erstellten Wiederherstellungsplan nach der zweiten Gruppe ein Skript hinzu. ![](media/site-recovery-runbook-automation/15.png)

2.  Geben Sie einen Skriptnamen an. Hierbei handelt es sich lediglich um einen Anzeigenamen für das Skript, der innerhalb des Wiederherstellungsplans verwendet wird.

3.  Wählen Sie im Bereich für das Azure-Failoverskript den Namen des Azure Automation-Kontos aus.

4.  Wählen Sie in den Azure-Runbooks das zuvor erstellte Runbook aus.

![](media/site-recovery-runbook-automation/16.png)

## Primäre serverseitige Skripts

Wenn Sie ein Failover an Azure ausführen, können Sie auch auswählen, primäre serverseitige Skripts auszuführen. Diese Skripts werden während des Failovers auf dem VMM-Server ausgeführt. Primäre serverseitige Skripts sind nur für die Phasen vor und nach dem Herunterfahren verfügbar. Dies liegt daran, dass typischerweise zu erwarten ist, dass der primäre Standort nicht erreichbar ist, wenn ein Notfall eintritt. Bei einem ungeplanten Failover werden primäre serverseitige Skripts nur dann ausgeführt, wenn Sie sich für primäre serverseitige Vorgänge entscheiden. Wenn sie nicht erreichbar sind oder das Zeitlimit überschritten wurde, wird das Failover weiterhin die virtuellen Computer wiederherstellen. Primäre serverseitige Skripts stehen nicht für VMware/physische/Hyper-V-Sites ohne VMM in mit Schutz in Azure – während der Failover an Azure übergeben wird. Bei einem Failback von Azure an den lokalen Standort können primäre serverseitige Skrips (Runbooks) für alle Ziele außer VMware verwendet werden.

## Testen des Wiederherstellungsplans

Nachdem Sie dem Plan das Runbook hinzugefügt haben, können Sie ein Test-Failover initiieren, um es in Aktion zu sehen. Für die Anwendung und den Wiederherstellungsplan sollte immer ein Test-Failover durchgeführt werden, um sicherzustellen, dass alles fehlerfrei funktioniert.

1.  Wählen Sie den Wiederherstellungsplan aus, und initiieren Sie ein Test-Failover.

2.  Während der Planausführung gibt der Status des Runbooks Aufschluss darüber, ob das Runbook ausgeführt wurde.

    ![](media/site-recovery-runbook-automation/17.png)

3.  Auf der Auftragsseite in Azure Automation steht zudem ein detaillierter Ausführungsstatus für das Runbook zur Verfügung.

    ![](media/site-recovery-runbook-automation/18.png)

4.  Nach Abschluss des Failovers können Sie prüfen, ob die Ausführung erfolgreich war. Rufen Sie hierzu die Seite des virtuellen Azure-Computers auf, und sehen Sie sich die Endpunkte an.

![](media/site-recovery-runbook-automation/19.png)

## Beispielskripts

In diesem Lernprogramm haben Sie gesehen, wie Sie eine häufig verwendete Aufgabe (das Hinzufügen eines Endpunkts zu einem virtuellen Azure-Computer) automatisieren können. Azure Automation bietet jedoch noch viele weitere Automatisierungsmöglichkeiten. Microsoft und die Azure Automation-Community stellen Beispiel-Runbooks, die Ihnen das Erstellen eigener Lösungen erleichtern, sowie Hilfsprogramm-Runbooks zur Verfügung, die Sie als Bausteine für umfangreichere Automatisierungen verwenden können. Erstellen Sie mithilfe des Katalogs leistungsstarke, mit nur einem Klick ausführbare Wiederherstellungspläne für Ihre Anwendungen mit Azure Site Recovery.

## Zusätzliche Ressourcen

[Übersicht über Azure Automation](http://msdn.microsoft.com/library/azure/dn643629.aspx "Übersicht über Azure Automation")

[Beispielskripts für Azure Automation](http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Beispielskripts für Azure Automation")

 

<!---HONumber=Oct15_HO2-->