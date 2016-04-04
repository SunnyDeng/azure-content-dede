## Übersicht
Beim Erstellen eines neuen virtuellen Computers (VM) in einer Ressourcengruppe durch Bereitstellen eines Images von [Azure Marketplace](https://azure.microsoft.com/marketplace/) hat das Standard-Betriebssystemlaufwerk eine Größe von 127 GB. Obwohl es möglich ist, der VM Datenträger hinzuzufügen (die Anzahl hängt von der SKU ab, die Sie ausgewählt haben), und darüber hinaus empfohlen wird, Anwendungen und CPU-intensive Workloads auf diesen zusätzlichen Datenträgern zu installieren, müssen Kunden oft das Betriebssystemlaufwerk erweitern, um bestimmte Szenarien wie z. B. die folgenden zu unterstützen:

1.  Unterstützung von Legacyanwendungen, die Komponenten auf dem Betriebssystemlaufwerk installieren.
2.  Migrieren eines lokalen physischen PCs oder virtuellen Computers mit einem größeren Betriebssystemlaufwerk.

>[AZURE.IMPORTANT]Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: Ressourcen-Manager und klassische Bereitstellungen. Dieser Artikel behandelt die Verwendung des Ressourcen-Manager-Modells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells.

## Ändern der Größe des Betriebssystemlaufwerks
In diesem Artikel wird beschrieben, wie die Größe des Betriebssystemlaufwerks mit den Ressourcen-Manager-Modulen von [Azure Powershell](../articles/powershell-install-configure.md) geändert wird. Öffnen Sie Powershell ISE oder Powershell-Fenster im Administratormodus, und führen Sie die folgenden Schritte aus:

1.  Melden Sie sich im Ressourcenverwaltungsmodus bei Ihrem Microsoft Azure-Konto an, und wählen Sie Ihr Abonnement wie folgt aus:

    ```Powershell
    Login-AzureRmAccount
    Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
    ```

2.  Legen Sie den Namen Ihrer Ressourcengruppe und VM wie folgt fest:

    ```Powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3.  Rufen Sie einen Verweis auf Ihre VM wie folgt ab:

    ```Powershell
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Halten Sie die VM vor dem Ändern der Größe des Datenträgers wie folgt an:

    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```

5.  Und hier ist der Moment, auf den wir gewartet haben! Stellen Sie die Größe des Betriebssystemdatenträgers auf den gewünschten Wert ein, und aktualisieren Sie die VM wie folgt:

    ```Powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
    ```

    >[AZURE.WARNING]Die neue Größe sollte die Größe des vorhandenen Datenträgers überschreiten. Das zulässige Maximum sind 1.023 GB.

6.  Das Aktualisieren der VM kann einige Sekunden dauern. Starten Sie die VM nach Abschluss der Befehlsausführung wie folgt neu:

    ```Powershell
    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```

Das ist alles! Stellen Sie jetzt eine RDP-Verbindung mit der VM her, öffnen Sie die Computerverwaltung (oder die Datenträgerverwaltung), und erweitern Sie das Laufwerk mit dem neu zugewiesenen Speicherplatz.

## Zusammenfassung
In diesem Artikel haben wir Azure Resource Manager-Module von Powershell verwendet, um das Betriebssystemlaufwerk eines virtuellen IaaS-Computers zu erweitern. Hier sehen Sie noch einmal das gesamte Skript:

```Powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## Nächste Schritte
Obwohl wir uns in diesem Artikel hauptsächlich auf die Erweiterung des Betriebssystem-Datenträgers der VM konzentriert haben, kann das entwickelte Skript durch Änderung einer einzigen Codezeile auch zum Erweitern der Datenträger verwendet werden, die der VM angefügt sind. Um z. B. den ersten Datenträger zu erweitern, der der VM angehängt ist, ersetzen Sie das ```OSDisk```-Objekt von ```StorageProfile``` mit dem ```DataDisks```-Array, und verwenden Sie einen numerischen Index, um – wie unten dargestellt – einen Verweis auf den ersten angefügten Datenträger zu erhalten:

```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Entsprechend können Sie andere Datenträger referenzieren, die der VM angefügt sind, indem Sie entweder – wie oben gezeigt – einen Index, oder – wie unten dargestellt – die ```Name```-Eigenschaft des Datenträgers verwenden:

```Powershell
($vm.StorageProfile.DataDisks | Where {$_.Name -eq 'my-second-data-disk'})[0].DiskSizeGB = 1023
```

Wenn Sie herausfinden möchten, wie Datenträger einer Azure Resource Manager-VM angefügt werden, lesen Sie diesen [Artikel](../articles/virtual-machines/virtual-machines-windows-attach-disk-portal.md).

<!---HONumber=AcomDC_0323_2016-->