<properties
 pageTitle="Verwalten von HPC Pack-Clustercomputeknoten | Microsoft Azure"
 description="Informationen zu PowerShell-Skripttools zum Hinzufügen, Entfernen, Starten und Beenden von HPC Pack-Clustercomputeknoten in Azure"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Verwalten der Anzahl und Verfügbarkeit von Computeknoten in einem HPC Pack-Cluster in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Wenn Sie einen HPC Pack-Cluster auf virtuellen Azure-Computern erstellt haben, möchten Sie wahrscheinlich verschiedene virtuelle Computer für Computeknoten im Cluster hinzufügen, entfernen, starten (bereitstellen) oder beenden (Bereitstellung aufheben). Zum Durchführen dieser Aufgaben können Sie Azure PowerShell-Skripts ausführen, die auf dem virtuellen Computer für den Hauptknoten installiert sind (ab HPC Pack 2012 R2 Update 1). Mit diesen Skripts können Sie die Anzahl und Verfügbarkeit der HPC Pack-Clusterressourcen steuern und so auch Kosten senken.

>[AZURE.NOTE]Die Skripts befinden sich im Ordner "%CCP\_HOME%bin" auf dem Hauptknoten. Sie müssen alle Skripts als Administrator ausführen.

## Voraussetzungen

* **HPC Pack-Cluster auf virtuellen Azure-Computern** – Erstellen Sie einen HPC Pack-Cluster im klassischen Bereitstellungsmodell (Dienstverwaltungsmodus). Verwenden Sie dazu mindestens HPC Pack 2012 R2 Update 1. Sie können beispielsweise die Bereitstellung automatisieren, indem Sie das HPC Pack-VM-Image in Azure Marketplace und ein Azure PowerShell-Skript verwenden. Informationen und Voraussetzungen finden Sie unter [Erstellen eines HPC-Clusters mit dem HPC Pack IaaS-Bereitstellungsskript](virtual-machines-hpcpack-cluster-powershell-script.md).

* **Azure-Veröffentlichungseinstellungsdatei oder -Verwaltungszertifikat:** Sie müssen einen der folgenden beiden Schritte auf dem Hauptknoten ausführen:

    * **Importieren der Azure-Veröffentlichungseinstellungsdatei **. Führen Sie hierzu die folgenden Azure PowerShell-Cmdlets auf dem Hauptknoten aus:

    ```
    Get-AzurePublishSettingsFile 
         
    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```
    
    * **Konfigurieren des Azure-Verwaltungszertifikats auf dem Hauptknoten**. Wenn Sie über die CER-Datei verfügen, importieren Sie sie in "CurrentUser\\My certificate store", und führen Sie dann das folgende Azure PowerShell-Cmdlet für Ihre Azure-Umgebung (AzureCloud oder AzureChinaCloud) aus:

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## Hinzufügen von virtuellen Computern für Computeknoten

Fügen Sie Computeknoten mit dem Script **Add-HpcIaaSNode.ps1** hinzu.

### Syntax
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### Parameter

* **ServiceName**: Name des Clouddiensts, dem virtuelle Computer für neue Computeknoten hinzugefügt werden.

* **ImageName:** Name des Azure-VM-Images, das über das klassische Azure-Portal oder das Azure PowerShell-Cmdlet **Get-AzureVMImage** abgerufen werden kann. Das Image muss die folgenden Anforderungen erfüllen:

    1. Es muss ein Windows-Betriebssystem installiert sein.

    2. HPC Pack muss in der Computeknotenrolle installiert sein.

    3. Bei dem Image muss es sich um ein privates Image in der Kategorie "Benutzer" und nicht um ein öffentliches Azure-VM-Image handeln.

* **Quantity**: Anzahl der hinzufügenden virtuellen Computer für Computeknoten.

* **InstanceSize**: Größe der virtuellen Computer für Computeknoten.

* **DomainUserName**: Domänenbenutzername, der zum Verknüpfen der neuen virtuellen Computer mit der Domäne verwendet wird.

* **DomainUserPassword***: Kennwort des Domänenbenutzers

* **NodeNameSeries** (optional): Benennungsmuster für die Computeknoten. Folgendes Format ist erforderlich: &lt;*Root\_Name*&gt;&lt;*Start\_Number*&gt;%. "MyCN%10%" gibt beispielsweise mehrere Namen für Computeknoten, beginnend ab "MyCN11" an. Wenn kein Wert angegeben ist, verwendet das Skript die im HPC-Cluster konfigurierten Benennungsreihen für Knoten.

### Beispiel

Im folgenden Beispiel werden 20 virtuelle Computer für Computeknoten mit der Größe "Large" basierend auf dem VM-Image "hpccnimage1" dem Clouddienst "hpcservice1" hinzugefügt.

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## Entfernen von virtuellen Computern für Computeknoten

Entfernen Sie Computeknoten mit dem Script **Remove-HpcIaaSNode.ps1**.

### Syntax

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### Parameter

 * **Name**: Namen der zu entfernenden Clusterknoten. Platzhalter werden unterstützt. Der Name des Parametersatzes ist "Name". Sie können nicht die beiden Parameter **Name** und **Node** angeben.

* **Node***: Das HpcNode-Objekt für die zu entfernenden Knoten, das über das HPC PowerShell-Cmdlet [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx) abgerufen werden kann. Der Name des Parametersatzes ist "Node". Sie können nicht die beiden Parameter **Name** und **Node** angeben.

* **DeleteVHD** (optional): Einstellung zum Löschen der Datenträger, die den zu entfernenden virtuellen Computern zugeordnet sind.

* **Force** (optional): Einstellung, mit der HPC-Knoten offline geschaltet werden, bevor sie entfernt werden.

* **Confirm** (optional): Aufforderung zur Bestätigung vor dem Ausführen des Befehls.

* **WhatIf**: Einstellung, mit der beschrieben wird, was geschehen würde, wenn Sie den Befehl ausführen würden, ohne ihn tatsächlich auszuführen.

### Beispiel

Im folgenden Beispiel werden Knoten offline geschaltet, deren Namen mit *HPCNode-CN-* beginnen. Anschließend werden die Knoten und die zugeordneten Datenträger entfernt.

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## Starten von virtuellen Computern für Computeknoten

Starten Sie Computeknoten mit dem Script **Start-HpcIaaSNode.ps1**.

### Syntax

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### Parameter

* **Name**: Namen der zu startenden Clusterknoten. Platzhalter werden unterstützt. Der Name des Parametersatzes ist "Name". Sie können nicht die beiden Parameter **Name** und **Node** angeben.

* **Node**: Das HpcNode-Objekt für die zu startenden Knoten, das über das HPC PowerShell-Cmdlet [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx) abgerufen werden kann. Der Name des Parametersatzes ist "Node". Sie können nicht die beiden Parameter **Name** und **Node** angeben.

### Beispiel

Im folgenden Beispiel werden Knoten gestartet, deren Namen mit *HPCNode-CN-* beginnen.

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## Beenden von virtuellen Computern für Computeknoten

Beenden Sie Computeknoten mit dem Script **Stop-HpcIaaSNode.ps1**.

### Syntax

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### Parameter


* **Name**: Namen der zu beendenden Clusterknoten. Platzhalter werden unterstützt. Der Name des Parametersatzes ist "Name". Sie können nicht die beiden Parameter **Name** und **Node** angeben.

* **Node**: Das HpcNode-Objekt für die zu beendenden Knoten, das über das HPC PowerShell-Cmdlet "Get-HpcNode" abgerufen werden kann. Der Name des Parametersatzes ist "Node". Sie können nicht die beiden Parameter **Name** und **Node** angeben.

* **Force** (optional): Einstellung, mit der HPC-Knoten offline geschaltet werden, bevor sie beendet werden.

### Beispiel

Im folgenden Beispiel werden Knoten offline geschaltet und anschließend beendet, deren Namen mit *HPCNode-CN-* beginnen.

Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force

## Nächste Schritte

* Wenn Sie die Azure-Computeressourcen entsprechend der aktuellen Workload der Aufträge und Aufgaben im Cluster automatisch vergrößern oder verkleinern möchten, finden Sie entsprechende Informationen unter [Automatisches Hoch- und Herunterskalieren von Azure-Computeressourcen in einem HPC Pack-Cluster](virtual-machines-hpcpack-cluster-node-autogrowshrink.md).

<!---HONumber=AcomDC_1210_2015-->