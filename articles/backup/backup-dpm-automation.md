<properties
	pageTitle="Azure Backup: Bereitstellen und Verwalten von Sicherungen für DPM mit PowerShell | Microsoft Azure"
	description="Erfahren Sie, wie Sie Azure Backup für Data Protection Manager (DPM) mithilfe von PowerShell bereitstellen und verwalten."
	services="backup"
	documentationCenter=""
	authors="AnuragMehrotra"
	manager="jwhit"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/26/2015" ms.author="jimpark"; "aashishr"; "sammehta"; "anuragm"/>


# Bereitstellen und Verwalten der Sicherung in Azure für Data Protection Manager (DPM)-Server mit PowerShell
In diesem Artikel erfahren Sie, wie Sie PowerShell zum Einrichten von Azure Backup auf einem DPM-Server sowie zum Verwalten von Sicherungen und Wiederherstellungen verwenden.

## Einrichten der PowerShell-Umgebung
Bevor Sie mit PowerShell Sicherungen von Data Protection Manager auf Azure verwalten können, benötigen Sie die richtige Umgebung in PowerShell. Zu Beginn der PowerShell-Sitzung müssen Sie den folgenden Befehl ausführen, um die richtigen Module zu importieren und korrekt auf die DPM-Cmdlets verweisen zu können:

```
PS C:\> & "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"

Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## Einrichtung und Registrierung
Vorbereitung:

1. [Laden Sie die neueste PowerShell herunter](https://github.com/Azure/azure-powershell/releases) (erforderliche Mindestversion ist 1.0.0).
2. Aktivieren Sie die Azure Backup-Cmdlets, indem Sie über das Cmdlet **Switch-AzureMode** in den *AzureResourceManager*-Modus wechseln:

```
PS C:\> Switch-AzureMode AzureResourceManager
```

Die folgenden Installations- und Registrierungsaufgaben können mit PowerShell automatisiert werden:

- Erstellen eines Sicherungstresors
- Installieren des Azure Backup-Agents
- Registrieren beim Azure Backup-Dienst
- Netzwerkeinstellungen
- Verschlüsselungseinstellungen

### Erstellen eines Sicherungstresors

> [AZURE.WARNING]Kunden, die Azure Backup zum ersten Mal verwenden, müssen den Azure Backup-Anbieter registrieren, der mit ihrem Abonnement verwendet werden soll. Führen Sie hierzu den folgenden Befehl aus: Register-AzureProvider -ProviderNamespace "Microsoft.Backup"

Sie können mit dem Cmdlet **New-AzureRMBackupVault** einen neuen Sicherungstresor erstellen. Der Sicherungstresor ist eine ARM-Ressource. Deshalb müssen Sie ihn innerhalb einer Ressourcengruppe einfügen. Führen Sie die folgenden Befehle in einer Azure PowerShell-Konsole mit erhöhten Rechten aus:

```
PS C:\> New-AzureResourceGroup –Name “test-rg” -Region “West US”
PS C:\> $backupvault = New-AzureRMBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GRS
```

Mit dem Cmdlet **Get-AzureRMBackupVault** können Sie eine Liste aller Sicherungstresore in einem bestimmten Abonnement abrufen.


### Installieren des Azure Backup-Agents auf einem DPM-Server
Bevor Sie den Azure Backup-Agent installieren, müssen Sie das Installationsprogramm herunterladen, damit es auf dem Windows-Server verfügbar ist. Die neueste Version des Installationsprogramms erhalten Sie im [Microsoft Download Center](http://aka.ms/azurebackup_agent) oder im Dashboard des Sicherungstresors. Speichern Sie das Installationsprogramm an einem leicht zugänglichen Speicherort wie *C:\\Downloads*.

Um den Agent zu installieren, führen Sie den folgenden Befehl **auf dem DPM-Server** in einer PowerShell-Konsole mit erhöhten Rechten aus:

```
PS C:\> MARSAgentInstaller.exe /q
```

Dadurch wird der Agent mit allen Standardoptionen installiert. Der Installationsvorgang im Hintergrund dauert einige Minuten. Wenn Sie die */nu*-Option nicht angeben, wird am Ende der Installation das Fenster **Windows Update** geöffnet, um nach Updates zu suchen.

Der Agent wird in der Liste der installierten Programme angezeigt. Um die Liste der installierten Programme anzuzeigen, wechseln Sie zu **Systemsteuerung** > **Programme** > **Programme und Funktionen**.

![Agent installiert](./media/backup-dpm-automation/installed-agent-listing.png)

#### Installationsoptionen
Um alle über die Befehlszeile verfügbaren Optionen anzuzeigen, verwenden Sie den folgenden Befehl:

```
PS C:\> MARSAgentInstaller.exe /?
```

Die verfügbaren Optionen umfassen:

| Option | Details | Standard |
| ---- | ----- | ----- |
| /q | Unbeaufsichtigte Installation | - | | /p: "location" | Der Pfad zum Installationsordner für den Azure Backup-Agent. | C:\\Program Files\\Microsoft Azure Recovery Services Agent || /s: "Location" | Der Pfad zum Cacheordner für den Azure Backup-Agent. | C:\\Program Files\\Microsoft Azure Recovery Services Agent\\Scratch || /m | Microsoft Update abonnieren | - | | /nu | Nach Abschluss der Installation nicht nach Updates suchen | - | | /d | Microsoft Azure Recovery Services Agent wird deinstalliert | - | | /ph | Proxyhostadresse | - | | /po | Proxyhost-Portnummer | - | | /pu | Proxyhost-Benutzername | - | | /pw | Proxykennwort | - |

### Registrieren beim Azure Backup-Dienst
Vor der Registrierung beim Azure Backup-Dienst müssen Sie sicherstellen, dass die [Voraussetzungen](backup-azure-dpm-introduction.md) erfüllt sind. Die Voraussetzungen lauten wie folgt:

- Gültiges Azure-Abonnement
- Ein Sicherungstresor

Führen Sie zum Herunterladen der Tresoranmeldedaten das Cmdlet **Get-AzureBackupVaultCredentials** in einer Azure PowerShell-Konsole aus, und speichern Sie sie an einem geeigneten Speicherort, z. B. *C:\\Downloads*.

```
PS C:\> $credspath = "C:"
PS C:\> $credsfilename = Get-AzureRMBackupVaultCredentials -Vault $backupvault -TargetLocation $credspath
PS C:\> $credsfilename
f5303a0b-fae4-4cdb-b44d-0e4c032dde26_backuprg_backuprn_2015-08-11--06-22-35.VaultCredentials
```

Das Registrieren des Computer beim Tresor erfolgt mithilfe des [Start-DPMCloudRegistration](https://technet.microsoft.com/library/jj612787)-Cmdlets:

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-DPMCloudRegistration -DPMServerName "TestingServer" -VaultCredentialsFilePath $cred
```

Dadurch wird der DPM-Server mit dem Namen „TestingServer“ unter Verwendung der angegebenen Tresoranmeldedaten im Microsoft Azure-Tresor registriert.

> [AZURE.IMPORTANT]Verwenden Sie keine relativen Pfade, um die Tresoranmeldedatendatei anzugeben. Sie müssen einen absoluten Pfad als Eingabe für das Cmdlet angeben.

### Anfängliche Konfigurationseinstellungen
Sobald der DPM-Server im Azure Backup-Tresor registriert ist, wird er mit den standardmäßigen Abonnementeinstellungen gestartet. Diese Abonnementeinstellungen umfassen Netzwerk, Verschlüsselung und den Stagingbereich. Um die Abonnementeinstellungen zu ändern, müssen Sie zunächst mit dem [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793)-Cmdlet ein Handle für die vorhandenen (standardmäßigen) Einstellungen abrufen:

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Alle Änderungen werden an diesem lokalen PowerShell-Objekt ```$setting``` vorgenommen, und anschließend wird das vollständige Objekt zum Speichern der Änderungen mit dem Cmdlet [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) per Commit in DPM und Azure Backup übernommen. Sie müssen das ```–Commit```-Kennzeichen verwenden, um sicherzustellen, dass die Änderungen gespeichert werden. Die Einstellungen werden erst angewendet und von Azure Backup verwendet, wenn ein Commit ausgeführt wurde.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

### Netzwerk
Wenn die Verbindung des DPM-Computers mit dem Azure Backup-Dienst im Internet über einen Proxyserver hergestellt wird, müssen die Proxyservereinstellungen bereitgestellt werden, damit die Sicherungen erfolgreich erstellt werden. Dazu werden die Parameter ```-ProxyServer```, ```-ProxyPort```, ```-ProxyUsername``` und ```ProxyPassword``` mit dem [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791)-Cmdlet verwendet. In diesem Beispiel gibt es keinen Proxyserver, daher werden alle Proxy-bezogenen Informationen explizit gelöscht.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Die Bandbreitennutzung kann auch mit Optionen für ```-WorkHourBandwidth``` und ```-NonWorkHourBandwidth``` für eine bestimmte Anzahl von Tagen der Woche gesteuert werden. In diesem Beispiel wird keine Drosselung festgelegt.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

### Konfigurieren des Stagingbereichs
Der Azure Backup-Agent auf dem DPM-Server benötigt temporären Speicher für Daten, die aus der Cloud wiederhergestellt werden (Stagingbereich). Konfigurieren Sie den Stagingbereich mit dem [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791)-Cmdlet und dem ```-StagingAreaPath```-Parameter.

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

Im obigen Beispiel wird der Stagingbereich im PowerShell-Objekt ```$setting``` auf *C:\\StagingArea* festgelegt. Stellen Sie sicher, dass der angegebene Ordner bereits vorhanden ist. Andernfalls schlägt der endgültige Commit für die Abonnementeinstellungen fehl.


### Verschlüsselungseinstellungen
Die Sicherungsdaten, die an Azure Backup gesendet werden, werden verschlüsselt, um die Vertraulichkeit der Daten zu schützen. Die Verschlüsselungspassphrase ist das "Kennwort" zum Entschlüsseln der Daten zum Zeitpunkt der Wiederherstellung. Es ist wichtig, diese Informationen nach dem Festlegen zu sichern.

Im folgenden Beispiel konvertiert der erste Befehl die Zeichenfolge ```passphrase123456789``` in eine sichere Zeichenfolge und weist diese der Variablen namens ```$Passphrase``` zu. Der zweite Befehl legt die sichere Zeichenfolge in ```$Passphrase``` als Kennwort für die Verschlüsselung von Sicherungen fest.

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [AZURE.IMPORTANT]Sichern Sie die Passphraseninformationen, nachdem Sie sie festgelegt haben. Es ist nicht möglich, Daten aus Azure ohne diese Passphrase wiederherzustellen.

Jetzt sollten Sie alle erforderlichen Änderungen am ```$setting```-Objekt vorgenommen haben. Denken Sie daran, einen Commit für die Änderungen auszuführen:

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## Schützen von Daten auf Azure Backup
In diesem Abschnitt fügen Sie DPM einen Produktionsserver hinzu, und anschließend schützen Sie die Daten im lokalen DPM-Speicher und auf Azure Backup. In den Beispielen wird das Sichern von Dateien und Ordnern demonstriert. Die Logik kann problemlos erweitert werden, um beliebige von DPM unterstützte Datenquellen zu sichern. Alle DPM-Sicherungen werden durch eine Schutzgruppe (Protection Group, PG) mit vier Teilen gesteuert:

1. **Gruppenmitglieder** ist eine Liste der zu schützenden Objekte (in DPM auch bezeichnet als *Datenquellen*), die in der gleichen Schutzgruppe geschützt werden sollen. Sie können aufgrund der unterschiedlichen Sicherungsanforderungen z. B. Produktions-VMs in einer Schutzgruppe und SQL-Serverdatenbanken in einer anderen Schutzgruppe schützen. Bevor Sie eine Datenquelle auf einem Produktionsserver sichern können, müssen Sie sicherstellen, dass der DPM-Agent auf dem Server installiert ist und von DPM verwaltet wird. Führen Sie die Schritte zum [Installieren des DPM-Agents](https://technet.microsoft.com/library/bb870935.aspx) und Verknüpfen des DPM-Agents mit dem entsprechenden DPM-Server aus.
2. Die **Datenschutzmethode** gibt die Zielspeicherorte für Sicherungen an, d. h. Band, Datenträger und Cloud. In diesem Beispiel werden Daten auf dem lokalen Datenträger und in der Cloud geschützt.
3. Ein **Sicherungszeitplan** gibt an, wann Sicherungen ausgeführt und wie oft die Daten zwischen dem DPM-Server und dem Produktionsserver synchronisiert werden müssen.
4. Ein **Aufbewahrungszeitplan**, der angibt, wie lange die Wiederherstellungspunkte in Azure beibehalten werden sollen.

### Erstellen einer Schutzgruppe
Erstellen Sie zunächst mithilfe des [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722)-Cmdlets eine neue Schutzgruppe.

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Das obige Cmdlet erstellt eine Schutzgruppe mit dem Namen *ProtectGroup01*. Eine vorhandene Schutzgruppe kann später auch geändert werden, um die Sicherung in der Azure Cloud hinzuzufügen. Um Änderungen an einer (neuen oder vorhandenen) Schutzgruppe vorzunehmen, müssen wir jedoch ein Handle für ein *änderbares* Objekt mit dem [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713)-Cmdlet abrufen.

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### Hinzufügen von Gruppenmitgliedern zur Schutzgruppe
Jeder DPM-Agent kennt die Liste der Datenquellen auf dem Server, auf dem er installiert ist. Um der Schutzgruppe eine Datenquelle hinzuzufügen, muss der DPM-Agent zunächst eine Liste mit den Datenquellen zurück an den DPM-Server senden. Anschließend werden eine oder mehrere Datenquellen ausgewählt und der Schutzgruppe hinzugefügt. Hierzu müssen die folgenden Schritte in PowerShell ausgeführt werden:

1. Rufen Sie eine Liste aller von DPM verwalteten Server über den DPM-Agent ab.
2. Wählen Sie einen Server aus.
3. Rufen Sie eine Liste aller Datenquellen auf dem Server ab.
4. Wählen Sie eine oder mehrere Datenquellen aus, und fügen Sie sie der Schutzgruppe hinzu.

Die Liste der Server, auf denen der DPM-Agent installiert ist und vom DPM-Server verwaltet wird, wird mit dem [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600)-Cmdlet abgerufen. In diesem Beispiel filtern wir, um nur den Produktionsserver mit dem Namen *productionserver01* für die Sicherung zu konfigurieren.

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”
```

Rufen Sie jetzt die Liste der Datenquellen auf ```$server``` mit dem [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605)-Cmdlet ab. In diesem Beispiel filtern wir nach dem Volume *D:*, das wir für das Backup konfigurieren möchten. Diese Datenquelle wird der Schutzgruppe dann mithilfe des [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732)-Cmdlets hinzugefügt. Denken Sie daran, zum Vornehmen von Änderungen das *änderbare* Schutzgruppenobjekt ```$MPG``` zu verwenden.

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Wiederholen Sie diesen Schritt, bis Sie der Schutzgruppe alle ausgewählten Datenquellen hinzugefügt haben. Sie können auch mit einer Datenquelle beginnen, die Schritte zum Erstellen der Schutzgruppe durchführen und der Schutzgruppe später weitere Datenquellen hinzufügen.

### Auswählen der Datenschutzmethode
Nachdem die Datenquellen der Schutzgruppe hinzugefügt wurden, legen Sie als Nächstes mithilfe des [Set-DPMProtectionType](https://technet.microsoft.com/library/hh881725)-Cmdlets die Schutzmethode fest. In diesem Beispiel wird die Schutzgruppe für die Sicherung auf dem lokalen Datenträger und in der Cloud eingerichtet. Sie müssen auch die zu schützende Datenquelle mit dem Cmdlet [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) und dem "-Online"-Flag angeben.

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### Festlegen der Beibehaltungsdauer
Legen Sie die Beibehaltungsdauer für die Sicherungspunkte mithilfe des [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762)-Cmdlets fest. Es erscheint ungewöhnlich, die Beibehaltungsdauer vor dem Definieren des Sicherungszeitplans festzulegen, durch die Verwendung des ```Set-DPMPolicyObjective```-Cmdlets wird jedoch automatisch ein Standardsicherungszeitplan festgelegt, der anschließend geändert werden kann. Es ist immer möglich, zuerst den Sicherungszeitplan und danach die Aufbewahrungsrichtlinie festzulegen.

Im folgenden Beispiel legt das Cmdlet die Aufbewahrungsparameter für Sicherungen auf Datenträger fest. Durch diese Einstellungen werden Sicherungen 10 Tage beibehalten und Daten alle 6 Stunden zwischen dem Produktionsserver und dem DPM-Server synchronisiert. ```SynchronizationFrequencyMinutes``` definiert nicht, wie oft ein Sicherungspunkt erstellt wird, aber wie häufig Daten auf den DPM-Server kopiert werden. Dadurch wird verhindert, dass Sicherungen zu groß werden.

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

Für Sicherungen auf Azure (in DPM werden diese als Onlinesicherungen bezeichnet) kann die Beibehaltungsdauer für [langfristig Aufbewahrung nach einem GFS-Schema (Grandfather-Father-Son)](backup-azure-backup-cloud-as-tape.md) konfiguriert werden. Sie können also eine kombinierte Aufbewahrungsrichtlinie mit täglichen, wöchentlichen, monatlichen und jährlichen Aufbewahrungsrichtlinien definieren. In diesem Beispiel erstellen wir ein Array, das das gewünschte komplexe Aufbewahrungsschema darstellt, und anschließend konfigurieren wir die Beibehaltungsdauer mit dem [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762)-Cmdlet.

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### Festlegen des Sicherungszeitplans
DPM legt automatisch einen Standardzeitplan fest, wenn Sie das Schutzziel mithilfe des ```Set-DPMPolicyObjective```-Cmdlets angeben. Um die Standardzeitpläne zu ändern, verwenden Sie das [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749)-Cmdlet gefolgt vom [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723)-Cmdlet.

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

Im obigen Beispiel ist ```$onlineSch``` ein Array mit vier Elementen, das den vorhandenen Onlineschutzzeitplan für die Schutzgruppe im GFS-Schema enthält:

1. ```$onlineSch[0]``` enthält den täglichen Zeitplan.
2. ```$onlineSch[1]``` enthält den wöchentlichen Zeitplan.
3. ```$onlineSch[2]``` enthält den monatlichen Zeitplan.
4. ```$onlineSch[3]``` enthält den jährlichen Zeitplan.

Wenn Sie den wöchentlichen Zeitplan ändern möchten, müssen Sie folglich auf ```$onlineSch[1]``` verweisen.

### Erste Sicherung
Wenn Sie eine Datenquelle zum ersten Mal sichern, muss DPM ein erstes Replikat erstellen, durch das eine Kopie der zu schützenden Datenquelle auf dem DPM-Replikatvolume erstellt wird. Diese Aktivität kann entweder für einen bestimmten Zeitpunkt geplant oder mit dem [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715)-Cmdlet mit dem ```-NOW```-Parameter manuell ausgelöst werden.

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```
### Ändern der Größe des DPM-Replikat- und des Wiederherstellungspunktvolumes
Mit dem Cmdlet [Set-DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) können Sie auch die Größe des DPM-Replikatvolumes sowie des Schattenkopievolumes ändern (siehe das folgende Beispiel): Get-DatasourceDiskAllocation -Datasource $DS Set-DatasourceDiskAllocation -Datasource $DS -ProtectionGroup $MPG -manual -ReplicaArea (2gb) -ShadowCopyArea (2gb)

### Commit für Änderungen an der Schutzgruppe
Zum Schluss muss für die Änderungen ein Commit ausgeführt werden, bevor DPM die Sicherung für die neue Schutzgruppenkonfiguration durchführen kann. Dazu wird das [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758)-Cmdlet verwendet.

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```
## Anzeigen der Sicherungspunkte
Sie können das [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746)-Cmdlet verwenden, um eine Liste aller Wiederherstellungspunkte für eine Datenquelle abzurufen. In diesem Beispiel rufen wir Folgendes ab: alle Schutzgruppen auf dem DPM-Server, die in einem Array ```$PG``` gespeichert werden, die Datenquellen, die ```$PG[0]``` entsprechen, und alle Wiederherstellungspunkte für eine Datenquelle.

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## Wiederherstellen von Daten, die auf Azure geschützt werden
Zum Wiederherstellen von Daten wird eine Kombination aus einem ```RecoverableItem```-Objekt und einem ```RecoveryOption```-Objekt verwendet. Im vorherigen Abschnitt haben wir eine Liste der Sicherungspunkte für eine Datenquelle abgerufen.

Das folgende Beispiel zeigt, wie Sie eine virtuelle Hyper-V-Maschine in Azure Backup wiederherstellen, indem Sie Sicherungspunkte mit dem Wiederherstellungsziel kombinieren. Dies umfasst:

- Erstellen einer Wiederherstellungsoption mit dem [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592)-Cmdlet
- Abrufen des Arrays von Sicherungspunkten mit dem ```Get-DPMRecoveryPoint```-Cmdlet
- Auswählen eines Sicherungspunkts für die Wiederherstellung

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Die Befehle können mühelos für beliebige Datenquellentypen erweitert werden.

## Nächste Schritte

- Weitere Informationen zu Azure Backup für DPM finden Sie unter [Einführung in DPM Backup](backup-azure-dpm-introduction.md)

<!---HONumber=AcomDC_1203_2015-->