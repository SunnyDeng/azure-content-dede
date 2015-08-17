<properties 
   pageTitle="Variable Objekte in Azure Automation"
   description="Variable Objekte sind Werte, die allen Runbooks in Azure Automation zur Verfügung stehen. Dieser Artikel stellt eine ausführliche Beschreibung von Variablen bereit und zeigt, wie diese in Textrunbooks und grafischen Runbooks eingesetzt werden."
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
   ms.date="05/21/2015"
   ms.author="bwren" />

# Variable Objekte in Azure Automation

Variable Objekte sind Werte, die allen Runbooks in Ihrem Automation-Konto zur Verfügung stehen. Sie können über das Azure-Portal, in Windows PowerShell sowie in einem Runbook erstellt, modifiziert und abgerufen werden. Automatisierungsvariablen sind in folgenden Szenarien hilfreich:

- Gemeinsame Nutzung eines Werts durch mehrere Runbooks.

- Gemeinsame Nutzung eines Werts durch mehrere Aufträge des gleichen Runbooks.

- Verwalten eines Werts über das Portal oder über die von Runbooks verwendete Windows PowerShell-Befehlszeile.

Automatisierungsvariablen sind persistent und bleiben daher verfügbar, auch wenn ein Runbook nicht ausgeführt werden kann. Dadurch kann ein Wert von einem Runbook festgelegt und von einem anderen oder vom gleichen Runbook bei der nächsten Ausführung verwendet werden.

Beim Erstellen einer Variablen können Sie festlegen, dass diese verschlüsselt gespeichert wird. Wenn eine Variable verschlüsselt wird, wird sie sicher in Azure Automation gespeichert, und ihr Wert kann vom Cmdlet [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx), das zum Lieferumfang des Azure PowerShell-Moduls gehört, nicht abgerufen werden. Ein verschlüsselter Wert kann ausschließlich über die Aktivität **Get-AutomationVariable** in einem Runbook abgerufen werden.

>[AZURE.NOTE]Zu den sicheren Objekten in Azure Automation gehören Anmeldeinformationen, Zertifikate, Verbindungen und verschlüsselte Variablen. Diese Objekte werden mithilfe eines eindeutigen Schlüssels verschlüsselt und in Azure Automation gespeichert, der für jedes Automation-Konto generiert wird. Dieser Schlüssel wird mit einem Masterzertifikat verschlüsselt und in Azure Automation gespeichert. Vor dem Speichern eines sicheren Objekts wird der Schlüssel für das Automation-Konto mit dem Masterzertifikat verschlüsselt und anschließend zum Verschlüsseln des Objekts verwendet.

## Variablentypen

Beim Erstellen einer Variablen über das Azure-Portal müssen Sie einen Datentyp aus der Dropdownliste angeben, damit das entsprechende Steuerelement zur Eingabe des Variablenwerts im Portal angezeigt werden kann. Die Variable ist nicht auf diesen Datentyp beschränkt, Sie müssen die Variable jedoch in Windows PowerShell festlegen, wenn Sie einen anderen Wertetyp angeben möchten. Wenn Sie **Nicht definiert** angeben, wird der Wert der Variablen auf **$null** festgelegt und Sie müssen den Wert mithilfe des Cmdlets [Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx) oder der Aktivität **Set-AutomationVariable** festlegen. Sie können den Wert für einen komplexen Variablentyp nicht über das Portal festlegen, Sie können jedoch einen Wert jedes Typs in Windows PowerShell bereitstellen. Komplexe Typen werden als [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx) zurückgegeben.

Sie können mehrere Werte in einer einzigen Variable speichern, indem Sie ein Array oder eine Hashtabelle erstellen und in der Variable speichern.

## Cmdlets und Workflowaktivitäten

Die Cmdlets in der folgenden Tabelle werden zum Erstellen und Verwalten von Automation-Variablen mit Windows PowerShell verwendet. Sie gehören zum Lieferumfang des [Azure PowerShell-Moduls](../powershell-install-configure.md), das zur Verwendung in Automation-Runbooks verfügbar ist.

|Cmdlets|Beschreibung|
|:---|:---|
|[Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx)|Ruft den Wert einer vorhandenen Variable ab.|
|[New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx)|Erstellt eine neue Variable und legt ihren Wert fest.|
|[Remove-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913775.aspx)|Entfernt eine vorhandene Variable.|
|[Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx)|Legt den Wert für eine vorhandene Variable fest.|

Die Workflowaktivitäten in der folgenden Tabelle werden für den Zugriff auf Automation-Variablen in einem Runbook verwendet. Sie sind nur zur Verwendung in einem Runbook verfügbar und gehören nicht zum Lieferumfang des Azure PowerShell-Moduls.

|Workflowaktivitäten|Beschreibung|
|:---|:---|
|Get-AutomationVariable|Ruft den Wert einer vorhandenen Variable ab.|
|Set-AutomationVariable|Legt den Wert für eine vorhandene Variable fest.|

>[AZURE.NOTE]Vermeiden Sie die Verwendung von Variablen im Parameter "–Name" von **Get-AutomationVariable** in einem Runbook, da dies die Ermittlung von Abhängigkeiten zwischen Runbooks und Automation-Variablen zur Entwurfszeit erschweren kann.

## Erstellen einer neuen Automation-Variablen

### So erstellen Sie eine neue Variable über das Azure-Portal

1. Klicken Sie in Ihrem Automation-Konto im oberen Fensterbereich auf **Objekte**.
1. Klicken Sie unten im Fenster auf **Einstellung hinzufügen**.
1. Klicken Sie auf **Variable hinzufügen**.
1. Schließen Sie den Assistenten ab, und aktivieren Sie das Kontrollkästchen, um die neue Variable zu speichern.


### So erstellen Sie eine neue Variable über das Azure-Vorschauportal

1. Klicken Sie in Ihrem Automation-Konto auf **Objekte**, um das Blatt **Objekte** zu öffnen.
1. Klicken Sie auf **Variablen**, um das Blatt **Variablen** zu öffnen.
1. Klicken Sie oben im Blatt auf **Variable hinzufügen**.
1. Geben Sie die erforderlichen Daten ein, und klicken Sie auf **Erstellen**, um die neue Variable zu speichern.


### So erstellen Sie eine neue Variable mit Windows PowerShell

Das Cmdlet [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx) erstellt eine neue Variable und legt ihren anfänglichen Wert fest. Sie können den Wert mithilfe von [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx) abrufen. Wenn es sich um einen einfachen Wert handelt, wird der gleiche Typ zurückgegeben. Wenn es sich um einen komplexen Wert handelt, wird ein **PSCustomObject** zurückgegeben.

Die folgenden Beispielbefehle zeigen, wie eine Variable vom Typ "string" erstellt und anschließend der Wert dieser Variablen zurückgegeben wird.


	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' –Encrypted $false –Value 'My String'
	$string = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

Die folgenden Beispielbefehle zeigen, wie eine Variable eines komplexen Typs erstellt wird und anschließend die Eigenschaften dieser Variablen zurückgegeben werden. In diesem Fall wird ein Objekt für einen virtuellen Computer von **Get-AzureVM** verwendet.

	$vm = Get-AzureVM –ServiceName "MyVM" –Name "MyVM"
	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
	
	$vmValue = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
	$vmName = $ vmValue.Name
	$vmIpAddress = $ vmValue.IpAddress



## Verwenden einer Variablen in einem Runbook

Verwenden Sie die Aktivität **Set-AutomationVariable**, um den Wert einer Automation-Variablen in einem Runbook festzulegen, und die Aktivität **Get-AutomationVariable**, um den Wert abzurufen. Die Verwendung der Cmdlets **Set-AzureAutomationVariable** oder **Get-AzureAutomationVariable** in einem Runbook empfiehlt sich nicht, da sie weniger effizient sind als die Workflowaktivitäten. Außerdem können Sie den Wert sicherer Variablen mit **Get-AzureAutomationVariable** nicht abrufen. Die einzige Möglichkeit, eine neue Variable aus einem Runbook zu erstellen, ist die Verwendung des Cmdlets [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx).


### Beispiele für Textrunbooks

#### Festlegen und Abrufen eins einfachen Werts aus einer Variablen

Die folgenden Beispielbefehle zeigen, wie Sie eine Variable in einem Textrunbook festlegen und abrufen. In diesem Beispiel wird angenommen, dass die Variablen des Typs "integer" *NumberOfIterations* und *NumberOfRunnings* sowie die Variable des Typs "string" *SampleMessage* bereits erstellt wurden.

	$NumberOfIterations = Get-AutomationVariable -Name 'NumberOfIterations'
	$NumberOfRunnings = Get-AutomationVariable -Name 'NumberOfRunnings'
	$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
	
	Write-Output "Runbook has been run $NumberOfRunnings times."
	
	for ($i = 1; $i -le $NumberOfIterations; $i++) {
	   Write-Output "$i`: $SampleMessage"
	}
	Set-AutomationVariable –Name NumberOfRunnings –Value (NumberOfRunngs += 1)


#### Festlegen und Abrufen eines komplexen Objekts in einer Variablen

Der folgende Beispielcode zeigt, wie eine Variable mit einem komplexen Wert in einem Textrunbook aktualisiert wird. In diesem Beispiel wird ein virtueller Azure-Computer mit **Get-AzureVM** abgerufen und in einer vorhandenen Automation-Variablen gespeichert. Wie im Abschnitt [Variablentypen](#variable-types) erläutert, erfolgt die Speicherung als "PSCustomObject".

	$vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
	Set-AutomationVariable -Name "MyComplexVariable" -Value $vm


Im folgenden Code wird der Wert aus der Variablen abgerufen und zum Starten des virtuellen Computers verwendet.

	$vmObject = Get-AutomationVariable -Name "MyComplexVariable"
	if ($vmObject.PowerState -eq 'Stopped') {
	   Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
	}


#### Festlegen und Abrufen eine Auflistung in einer Variablen

Der folgende Beispielcode zeigt, wie eine Variable mit einer Auflistung komplexer Werte in einem Textrunbook aktualisiert wird. In diesem Beispiel werden mehrere virtuelle Azure-Computer mit **Get-AzureVM** abgerufen und in einer vorhandenen Automation-Variablen gespeichert. Wie im Abschnitt [Variablentypen](#variable-types) erläutert, erfolgt die Speicherung als PSCustomObject-Auflistung gespeichert.

	$vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

Im folgenden Code wird die Auflistung aus der Variablen abgerufen und zum Starten der virtuellen Computer verwendet.

	$vmValues = Get-AutomationVariable -Name "MyComplexVariable"
	ForEach ($vmValue in $vmValues)
	{
	   if ($vmValue.PowerState -eq 'Stopped') {
	      Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
	   }
	}

### Beispiel für grafische Runbooks

In einem grafischen Runbook fügen Sie **Get-AutomationVariable** oder **Set-AutomationVariable** hinzu, indem Sie im Bereich "Bibliothek" des grafischen Editors mit der rechten Maustaste auf die Variable klicken und die gewünschte Aktivität auswählen.

![Hinzufügen einer Variablen zum Zeichenbereich](media/automation-variables/variable-add-canvas.png)

#### Festlegen von Werten in einer Variablen

Die folgende Abbildung zeigt Beispielaktivitäten zum Aktualisieren einer Variablen mit einem einfachen Wert in einem grafischen Runbook. In diesem Beispiel wird ein einzelner virtueller Azure-Computer mit **Get-AzureVM** abgerufen, und der Name des Computers wird in einer vorhandenen Automation-Variablen vom Typ "string" gespeichert. Es spielt keine Rolle, ob es sich bei der [Verknüpfung um eine Pipeline oder eine Sequenz](automation-graphical-authoring-intro.md#links-and-workflow) handelt, da in der Ausgabe nur ein einziges Objekt zu erwarten ist.

![Festlegen einer einfachen Variablen](media/automation-variables/set-simple-variable.png)

Die folgende Abbildung zeigt Beispielaktivitäten zum Aktualisieren einer Variablen mit einem komplexen Wert in einem grafischen Runbook. Der einzige Unterschied zum vorherigen Beispiel besteht darin, dass kein **Feldpfad** für die **Aktivitätsausgabe** in der Aktivität **Set-AutomationVariable** angegeben wird, sodass nicht nur eine Eigenschaft des Objekts, sondern das Objekt gespeichert wird. Wie im Abschnitt [Variablentypen](#variable-types) erläutert, erfolgt die Speicherung als "PSCustomObject".

![Festlegen einer komplexen Variablen](media/automation-variables/set-complex-variable.png)

Die folgende Abbildung zeigt eine ähnliche Funktionalität wie das vorherige Beispiel, es werden jedoch mehrere virtuelle Computer in der Variablen gespeichert. Hier muss eine [Sequenzverknüpfung](automation-graphical-authoring-intro.md#links-and-workflow) verwendet werden, sodass die Aktivität **Set-AutomationVariable** den gesamten Satz virtueller Computer in einer einzigen Auflistung empfängt. Würde eine [Pipelineverknüpfung](automation-graphical-authoring-intro.md#links-and-workflow) verwendet, würde die Aktivität **Set-AutomationVariable** für jedes Objekt getrennt ausgeführt werden. Dadurch würde nur der letzte virtuelle Computer gespeichert. Wie im Abschnitt [Variablentypen](#variable-types) erläutert, erfolgt die Speicherung als PSCustomObject-Auflistung.

![Festlegen einer komplexen Auflistungsvariablen](media/automation-variables/set-complex-variable-collection.png)

#### Abrufen von Werten aus einer Variablen

Die folgende Abbildung zeigt Beispielaktivitäten zum Abrufen und Verwenden einer Variablen in einem grafischen Runbook. Die erste Aktivität ruft die virtuellen Computer ab, die im vorherigen Beispiel in der Variablen gespeichert wurden. Bei der Verknüpfung muss es sich um eine [Pipeline](automation-graphical-authoring-intro.md#links-and-workflow) handeln, sodass die Aktivität **Start-AzureVM** einmal für jedes aus der Aktivität **Get-AutomationVariable** übermittelte Objekt ausgeführt wird. Dies funktioniert auf gleiche Weise, unabhängig davon, ob ein oder mehrere Objekte in der Variablen gespeichert sind. Die Aktivität **Start-AzureVM** verwendet Eigenschaften von "PSCustomObject", das jeden virtuellen Computer darstellt.

![Abrufen einer komplexen Variablen](media/automation-variables/get-complex-variable.png)

Die folgende Abbildung zeigt das Filtern der Objekte, die in einer Variablen in einem grafischen Runbook gespeichert sind. Der Verknüpfung im vorherigen Beispiel wird eine [Bedingung](automation-graphical-authoring-intro.md#links-and-workflow) hinzugefügt, um nur nach den virtuellen Computern zu filtern, die beim Festlegen der Variablen beendet waren.

![Abrufen einer komplexen Variablen mit Filter](media/automation-variables/get-complex-variable-filter.png)


## Verwandte Artikel

- [Verknüpfungen bei der grafischen Erstellung](automation-graphical-authoring-intro.md#links-and-workflow)
 

<!---HONumber=August15_HO6-->