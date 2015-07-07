<properties
	pageTitle="Azure Backup: Bereitstellen und Verwalten der Sicherung für DPM mit Windows PowerShell | Microsoft Azure"
	description="Informationen zum Bereitstellen und Verwalten von Azure Backup für DPM (Data Protection Manager) mithilfe von Windows PowerShell"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="jimpark"/>


# Bereitstellen und Verwalten der Sicherung auf Azure für DPM-Server (Data Protection Manager) mithilfe von Windows PowerShell
In diesem Artikel erfahren Sie, wie Sie die Windows PowerShell®-Befehlszeilenschnittstelle zum Einrichten von Azure Backup auf einem DPM-Server und zum Verwalten von Sicherungen und Wiederherstellungen verwenden.

## Einrichten der Windows PowerShell-Umgebung
Bevor Sie Windows PowerShell zum Verwalten von Azure Backup verwenden können, benötigen Sie die richtige Windows PowerShell-Version und die Einrichtung der Umgebungsvariablen.

Überprüfen Sie, ob Sie über die PowerShell-Version 3.0 oder 4.0 verfügen. Um die Version von Windows PowerShell zu finden, geben Sie diesen Befehl in eine Windows/DPM PowerShell-Eingabeaufforderung ein.

```
$PSVersionTable
```

Sie erhalten den folgenden Informationstyp:

| Name | Wert |
| ---- | ----- |
| PSVersion | 3.0 |
| WSManStackVersion | 3.0 |
| SerializationVersion | 1.1.0.1 |
| CLRVersion | 4.0.30319.18444 |
| BuildVersion | 6.2.9200.16481 |
| PSCompatibleVersions | {1.0, 2.0, 3.0} |
| PSRemotingProtocolVersion | 2.2 |

Überprüfen Sie, dass der Wert der **PSVersion** 3.0 oder 4.0 ist. Falls nicht, finden Sie weitere Informationen unter [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) oder [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

## Einrichtung und Registrierung
### Installieren des Azure Backup-Agents auf dem DPM-Server
Um den Azure Backup-Agent zu installieren, müssen Sie das Installationsprogramm heruntergeladen und auf dem Windows-Server verfügbar haben. Sie erhalten die neueste Version des Installationsprogramms im [Microsoft Download Center](aka.ms/azurebackup_agent). Speichern Sie das Installationsprogramm an einem leicht zugänglichen Speicherort wie *C:*.

Um den Agent zu installieren, führen Sie den folgenden Befehl in einer hochgestuften DPM Windows PowerShell-Konsole aus:

```
PS C:> MARSAgentInstaller.exe /q
```

Dadurch wird der Agent mit allen Standardoptionen installiert. Der Installationsvorgang im Hintergrund dauert einige Minuten. Wenn Sie die */nu*-Option nicht angeben, wird das Fenster "Windows Update" am Ende geöffnet, um nach Updates zu suchen.

Der Agent wird in der Liste der installierten Programme angezeigt. Um die Liste der installierten Programme anzuzeigen, wechseln Sie zu **Systemsteuerung** > **Programme** > **Programme und Funktionen**.

![Agent installiert](./media/backup-dpm-automation/installed-agent-listing.png)

#### Installationsoptionen
Um alle über die Befehlszeile verfügbaren Optionen anzuzeigen, verwenden Sie den folgenden Befehl:

```
PS C:> MARSAgentInstaller.exe /?
```

Die verfügbaren Optionen umfassen:

| Option | Details | Standard |
| ---- | ----- | ----- |
| /q | Unbeaufsichtigte Installation | - | | /p: "location" | Der Pfad zum Installationsordner für den Azure Backup-Agent. | C:\Program Files\\Microsoft Azure Recovery Services Agent || /s: "Location" | Der Pfad zum Cacheordner für den Azure Backup-Agent. | C:\Program Files\\Microsoft Azure Recovery Services Agent\\Scratch || /m | Microsoft Update abonnieren | - | | /nu | Nach Abschluss der Installation nicht nach Updates suchen | - | | /d | Microsoft Azure Recovery Services Agent wird deinstalliert | - | | /ph | Proxyhostadresse | - | | /po | Proxyhost-Portnummer | - | | /pu | Proxyhost-Benutzername | - | | /pw | Proxykennwort | - |

### Registrieren beim Azure Backup-Dienst
Bevor Sie sich beim Azure Backup-Dienst registrieren können, stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind: - Gültiges Azure-Abonnement ist vorhanden - Erstellen eines Sicherungstresors - Herunterladen der Tresoranmeldedaten und Speichern an einem zentralen Ort (z. B. *C:\Downloads*). Die Tresoranmeldedaten können auch der Einfachheit halber umbenannt werden.

Das Registrieren des Computer beim Tresor erfolgt mithilfe des [Start-DPMCloudRegistration](https://technet.microsoft.com/library/jj612787)-Cmdlets:

```
PS C:> Start-DPMCloudRegistration -DPMServerName "TestingServer" -VaultCredentialsFilePath "C:\DPMTESTVault_Friday, September 5, 2014.VaultCredentials"
```

Dadurch wird der DPM-Server mit dem Namen "TestingServer" beim Microsoft Azure-Tresor mithilfe der Tresoranmeldedaten in *C:* registriert.

> [AZURE.IMPORTANT]Verwenden Sie keine relativen Pfade, um die Tresoranmeldedatendatei anzugeben. Sie müssen einen absoluten Pfad als Eingabe für das Cmdlet angeben.

### Konfigurieren von DPM-Sicherungsoptionen (Abonnementeinstellungen) für die Onlinesicherung
Sobald der DPM-Server, für den Onlineschutz registriert ist, werden die Standardabonnementeinstellungen festgelegt, die verschiedenen Optionen zur Sicherung angeben. Wenn Sie die Abonnementeinstellungen ändern möchten, müssen Sie die vorhandene Einstellung abrufen und gemäß Ihren Anforderungen ändern.

#### Abrufen von vorhandenen Abonnementeinstellungen für den DPM-Server
Um die DPM-Onlineabonnementeinstellungen zu konfigurieren, müssen Sie zuerst die vorhandenen Einstellungen mithilfe des [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793.aspx)-Cmdlets abrufen:

```
$Setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Sie müssen diesen Befehl ausführen, um das Abonnementeinstellungsobjekt abzurufen, das verwendet werden kann, um verschiedene Optionen zu konfigurieren und die Einstellungen schließlich zu speichern. Das Ausführen des Befehls für einen neuen DPM-Server gibt die Standardeinstellungen zurück.

#### Ändern von Abonnementeinstellungen
Sie können die folgenden Einstellungen für das DPM-Onlinesicherungsabonnement mit dem [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791)-Cmdlet konfigurieren:

##### Stagingbereich
Der Azure Backup-Agent auf dem DPM-Server benötigt temporären Speicher für Daten, die aus der Cloud wiederhergestellt werden (Stagingbereich). Sie können den folgenden Befehl verwenden, um den Stagingbereich zu konfigurieren. Dadurch wird der Stagingbereich als "C:\StagingArea" für **$setting** festgelegt, aber die Konfiguration wird erst dann auf den DPM-Server angewendet, wenn Sie die Einstellungen übernehmen.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -StagingAreaPath "C:\StagingArea"
```

> [AZURE.NOTE]Stellen Sie sicher, dass der im obigen Befehl angegebene Ordner vorhanden ist. Andernfalls erhalten Sie eine Fehlermeldung beim Speichern der Abonnementeinstellungen.

##### Netzwerk
Verbindungen vom DPM-Computer zum Internet erfolgen über einen Proxyserver. Die Proxyeinstellungen können dem Agent bereitgestellt werden. In diesem Beispiel gibt es keinen Proxyserver, daher werden alle Proxy-bezogenen Informationen explizit gelöscht.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoProxy
```

Die Bandbreitennutzung kann auch mit den Optionen für *Arbeitsstunden-Bandbreite* und *Stunden für nicht arbeitsbezogene Bandbreite* für eine bestimmte Anzahl von Tagen der Woche gesteuert werden. In diesem Beispiel wird keine Drosselung festgelegt.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoThrottle
```

##### Verschlüsselungseinstellungen
Die Sicherungsdaten, die an Azure Backup gesendet werden, werden verschlüsselt, um die Vertraulichkeit der Daten zu schützen. Die Verschlüsselungspassphrase ist das "Kennwort" zum Entschlüsseln der Daten zum Zeitpunkt der Wiederherstellung.

> [AZURE.IMPORTANT]Es ist wichtig, diese Informationen nach dem Festlegen zu sichern.

Im folgenden Beispiel konvertiert der erste Befehl die Zeichenfolge "passphrase123456789" in eine sichere Zeichenfolge und weist die sichere Zeichenfolge der Variablen namens "$Passphrase" zu. Der zweite Befehl legt die sichere Zeichenfolge in "$Passphrase" als Kennwort zum Verschlüsseln von Sicherungen fest.

```
PS C:> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force
```

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -EncryptionPassphrase $Passphrase
```

#### Speichern von Abonnementeinstellungen
Sie müssen die Änderungen am DPM-Server mit der Option *-Commit* übernehmen.

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -Commit
```

> [AZURE.NOTE]Einstellungen werden nur angewendet, wenn sie übernommen wurden.

## Schützen von Daten auf Azure Backup
In den nächsten Schritten erfahren Sie, wie Sie einen Produktionsserver zu DPM hinzufügen und dann die Daten auf dem Datenträger und online in Azure schützen können. In den Beispielen wird das Sichern von Dateien und Ordnern demonstriert. Die Logik kann problemlos erweitert werden, um allen unterstützten Datenquellen zu sichern. Als Voraussetzung müssen Sie den DPM-Server für den Onlineschutz zum einrichten und registrieren.

Alle DPM-Sicherungen unterliegen einer Schutzgruppe (Protection Group, PG), die vier Teile umfasst.

1. **Gruppenmitglieder** ist eine Liste der zu schützenden Objekte, die in der gleichen Schutzgruppe geschützt werden sollen. Sie möchten zum Beispiel Produktions-VMs in einer Schutzgruppe und die Test-VMs in einer anderen Schutzgruppe schützen, da sie andere Sicherungsanforderungen aufweisen können.
2. **Datenschutzmethode** wird verwendet, um anzugeben, wo Sie die Daten schützen möchten. In diesem Beispiel werden Daten auf Datenträgern und im Onlineschutz in der Azure-Cloud geschützt.
3. Ein **Sicherungszeitplan**, der angibt, wann Sicherungen ausgeführt werden müssen und wie oft die Daten zwischen dem DPM-Server und dem Produktionsserver synchronisiert werden sollen.
4. Ein **Aufbewahrungszeitplan**, der angibt, wie lange die Wiederherstellungspunkte in Azure beibehalten werden sollen.

### Erstellen einer Schutzgruppe und Sichern von Daten in regelmäßigen Intervallen
#### Hinzufügen eines Produktionsservers zu einem DPM-Server
Bevor Sie Daten auf einem Produktionsserver sichern können, müssen Sie sicherstellen, dass der DPM-Agent auf dem Produktionsserver installiert ist und von DPM verwaltet wird. Führen Sie die Schritte [hier](https://technet.microsoft.com/library/bb870935.aspx) zum Installieren des DPM-Agents und zum Konfigurieren für die Sicherung durch einen entsprechenden DPM-Server aus.

#### Erstellen einer Schutzgruppe
Da die Sicherung automatisiert wird, wird in diesem Artikel davon ausgegangen, dass nichts konfiguriert wurde. Sie beginnen mit dem Erstellen einer neuen Schutzgruppe mithilfe des [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722.aspx)-Cmdlets.

```
PS C:> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Bevor Sie Änderungen an der Schutzgruppe vornehmen, muss sie mithilfe des [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713.aspx)-Cmdlets in den änderbaren Modus versetzt werden.

```
PS C:> $MPG = Get-ModifiableProtectionGroup $PG
```

Die oben aufgeführten Cmdlets erstellen eine Schutzgruppe namens "ProtectGroup01", führen aber noch keine Sicherung durch. Sie müssen definieren, was gesichert werden soll, wann Sicherungen ausgeführt werden und wo die Sicherungen gespeichert werden.

### Hinzufügen von Gruppenmitgliedern zur Schutzgruppe
Es gibt mehrere Schritte zum Hinzufügen einer Datenquelle zur Schutzgruppe.

#### Abrufen des Produktionsservers (PS), den Sie sichern möchten
Sie können mithilfe des [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600.aspx)-Cmdlets alle PS abrufen, für die der DPM-Agent durch den DPM-Server verwaltet und installiert wird. In diesem Beispiel wird gefiltert und nur der PS mit dem Namen "productionserver01" für die Sicherung konfiguriert.

```
PS C:> $PS = Get-ProductionServer -DPMServerName "TestingServer" |where {($_.servername) –contains “productionserver01”
```

### Ausführen einer Abfrage zum Abrufen einer Liste der Datenquellen auf dem PS
Sie können eine Abfrage auf dem PS-Computer ausführen, um eine Liste mit allen Datenquellen abzurufen, die DPM mit dem [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605.aspx)-Cmdlet sichern kann. In diesem Beispiel können Sie die Datenquelle "D:\" auf dem PS, für den Sie Schutz konfigurieren möchten, filtern.

```
PS C:> $DS = Get-Datasource -ProductionServer $PS -Inquire | where { $_.Name -contains “D:\” }
```

#### Hinzufügen der Datenquelle für den Schutz
Sie können die Datenquelle der Schutzgruppe "ProtectGroup01", die Sie im Beispiel oben erstellt haben, mithilfe des [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx)-Cmdlets hinzufügen.

```
PS C:> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

### Auswählen der Datenschutzmethode
Wenn Sie die Datenquelle der Schutzgruppe hinzugefügt haben, müssen Sie die Schutzmethode angeben. In diesem Fall richten Sie die Schutzgruppe für den kurzfristigen Schutz auf Festplatte und dem langfristigen Schutz auf Band ein.

```
PS C:> Set-DPMProtectionType -ProtectionGroup $PG -ShortTerm Disk –LongTerm Online
```

### Angeben von Wiederherstellungspunktzielen
#### Festlegen der Beibehaltungsdauer
Sie können die Beibehaltungsdauer für die Schutzgruppe mithilfe von [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762.aspx) festlegen. Der folgende Befehl legt die *Beibehaltungsdauer* und die *Synchronisierungsfrequenz* für den datenträgerbasierten Wiederherstellungspunkt fest.

```
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequency 360
```

Für Onlinewiederherstellungspunkte können Sie Beibehaltungsdauern für verschiedene GFS-Schemas (täglich, wöchentlich, monatlich und jährlich) konfigurieren. In diesem Beispiel erstellen Sie ein Objekt mit verschiedenen Beibehaltungsdauern und konfigurieren dann die Onlinebeibehaltungsdauer mithilfe des Objekts, wie im folgenden Beispiel gezeigt wird.

```
PS C:> $RRlist = @()
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

#### Festlegen von Sicherungszeitplänen
DPM legt Standardzeitpläne automatisch fest, wenn Sie das Schutzziel mithilfe des **Set-PolicyObjective**-Cmdlets angeben. Um die Standardzeitpläne zu ändern, verwenden Sie das [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749.aspx)-Cmdlet gefolgt vom [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723.aspx)-Cmdlet.

```
PS C:> $onlineSch=Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTermOnline
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
```

#### Erstellen von Replikaten
Wenn Sie die Datenquelle zum ersten Mal sichern, muss DPM ein erstes Replikat erstellen, das eine Kopie der zu schützenden Datenquelle auf dem DPM-Replikatvolume erstellt. Sie können die Erstellung eines Replikats automatisch zu einem bestimmten Zeitpunkt oder manuell mithilfe des [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715.aspx)-Cmdlets planen.

```
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

#### Übernehmen der Schutzgruppe
Abschließend müssen Sie die Änderungen übernehmen, damit DPM die Sicherung für die Schutzgruppe mithilfe des [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758.aspx)-Cmdlets durchführen kann.

```
PS C:> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## Abrufen einer Liste aller Onlinewiederherstellungspunkte
Sie können das [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746.aspx)-Cmdlet zum Abrufen einer Liste aller Wiederherstellungspunkte für eine Datenquelle verwenden. In diesem Beispiel rufen Sie alle Schutzgruppen auf dem DPM-Server ab, die in einem Array "$PG" gespeichert werden. Sie rufen die Datenquellen ab, die "$PG [0]" entsprechen, und Sie rufen alle Wiederherstellungspunkte für eine Datenquelle ab.

```
PS C:> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## Wiederherstellen von Daten, die auf Azure geschützt werden
In diesem Beispiel wird demonstriert, wie ein virtueller Hyper-V-Computer von einem Onlinewiederherstellungspunkt aus wiederhergestellt wird, aber die Befehle können für jeden Datenquellentyp problemlos erweitert werden.

Zunächst müssen Sie eine Wiederherstellungsoption mithilfe des [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592.aspx)-Cmdlets erstellen. Für das folgende Beispiel stellen Sie eine Hyper-V-Datenquelle an einem alternativen Speicherort wieder her.

```
PS C:> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “c:\VMRecovery”
```

Nach der Konfiguration der Wiederherstellungsoption müssen Sie den Onlinewiederherstellungspunkt wiederherstellen, den Sie im Abschnitt [Abrufen einer Liste aller Onlinewiederherstellungspunkte](#Getting-a-list-of-all-Online-Recovery-Points) abgerufen haben.

```
PS C:> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints -RecoveryOption $RecoveryOption
```
## Nächste Schritte
Weitere Informationen zu Azure Backup für DPM finden Sie unter [Einführung in Azure DPM Backup](backup-azure-dpm-introduction.md)

<!---HONumber=62-->