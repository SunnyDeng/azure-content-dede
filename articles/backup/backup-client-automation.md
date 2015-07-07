<properties
	pageTitle="Bereitstellen und Verwalten der Sicherung für Windows-Server und -Clients mit Windows PowerShell | Microsoft Azure"
	description="Informationen zum Bereitstellen und Verwalten von Azure Backup mit Windows PowerShell"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="aashishr"/>


# Bereitstellen und Verwalten der Sicherung auf Azure für Windows-Server/Windows-Clients mit Windows PowerShell
In diesem Artikel erfahren Sie, wie Sie die Windows PowerShell®-Befehlszeilenschnittstelle zum Einrichten von Azure Backup auf Windows-Servern oder Windows-Clients und zum Verwalten von Sicherungen und Wiederherstellungen verwenden.

## Einrichten der Windows PowerShell-Umgebung
Bevor Sie Windows PowerShell zum Verwalten von Azure Backup verwenden können, benötigen Sie die richtige Windows PowerShell-Version und die Einrichtung der Umgebungsvariablen.

## Einrichtung und Registrierung
Die folgenden Aufgaben für Einrichtung und Registrierung können mit Windows PowerShell automatisiert werden: Installation des Azure Backup-Agents - Registrierung beim Azure Backup-Dienst - Netzwerk

### Installieren des Azure Backup-Agents
Um den Azure Backup-Agent zu installieren, müssen Sie das Installationsprogramm heruntergeladen und auf dem Windows-Server verfügbar haben. Sie erhalten die neueste Version des Installationsprogramms im [Microsoft Download Center](aka.ms/azurebackup_agent). Speichern Sie das Installationsprogramm an einem leicht zugänglichen Speicherort wie *C:\Downloads*. Um den Agent zu installieren, führen Sie den folgenden Befehl in einer hochgestuften Windows PowerShell-Konsole aus:

```
PS C:> MARSAgentInstaller.exe /q
```

Dadurch wird der Agent mit allen Standardoptionen installiert. Der Installationsvorgang im Hintergrund dauert einige Minuten. Wenn Sie die **/nu**-Option nicht angeben, wird das Fenster **Windows Update** am Ende geöffnet, um nach Updates zu suchen.

Der Agent wird in der Liste der installierten Programme angezeigt. Um die Liste der installierten Programme anzuzeigen, wechseln Sie zu **Systemsteuerung** > **Programme** > **Programme und Funktionen**.

![Agent installiert](./media/backup-client-automation/installed-agent-listing.png)

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
Bevor Sie sich beim Azure Backup-Dienst registrieren können, stellen Sie sicher, dass die [Voraussetzungen](backup-try-azure-backup-in-10-mins.md) erfüllt sind: - Gültiges Azure-Abonnement ist vorhanden - Erstellen eines Sicherungstresors - Herunterladen der Tresoranmeldedaten und Speichern an einem zentralen Ort (z. B. *C:\Downloads*). Die Tresoranmeldedaten können auch der Einfachheit halber umbenannt werden.

Das Registrieren des Computer beim Tresor erfolgt mithilfe des [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx)-Cmdlets:

```
PS C:> Start-OBRegistration -VaultCredentials "C:\Downloads\register.vaultcredentials" -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : Australia East

Machine registration succeeded.
```

> [AZURE.IMPORTANT]Verwenden Sie keine relativen Pfade, um die Tresoranmeldedatendatei anzugeben. Sie müssen einen absoluten Pfad als Eingabe für das Cmdlet angeben.


### Netzwerk
Wenn die Konnektivität des Windows-Computers mit dem Internet über einen Proxyserver hergestellt wird, können die Proxyeinstellungen auch dem Agent bereitgestellt werden. In diesem Beispiel gibt es keinen Proxyserver, daher werden alle Proxy-bezogenen Informationen explizit gelöscht.

Die Bandbreitennutzung kann auch mit den Optionen für *Arbeitsstunden-Bandbreite* und *Stunden für nicht arbeitsbezogene Bandbreite* für eine bestimmte Anzahl von Tagen der Woche gesteuert werden.

Das Festlegen von Proxy- und Bandbreitendetails erfolgt mithilfe des [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx)-Cmdlets:

```
PS C:> Set-OBMachineSetting -NoProxy
Server properties updated successfully.
```

```
PS C:> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

### Verschlüsselungseinstellungen
Die Sicherungsdaten, die an Azure Backup gesendet werden, werden verschlüsselt, um die Vertraulichkeit der Daten zu schützen. Die Verschlüsselungspassphrase ist das "Kennwort" zum Entschlüsseln der Daten zum Zeitpunkt der Wiederherstellung. Es ist wichtig, diese Informationen nach dem Festlegen zu sichern.

```
PS C:> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

## Deinstallieren des Azure Backup-Agents
Das Deinstallieren des Azure Backup-Agents kann mithilfe des folgenden Befehls erfolgen:

```
PS C:> .\MARSAgentInstaller.exe /d /q
```

Das Deinstallieren die Agentbinärdateien vom Computer hat andere Auswirkungen: -Der Dateifilter wird vom Computer entfernt, und die Nachverfolgung von Änderungen wird beendet. - Alle Richtlinieninformationen werden vom Computer entfernt, aber die Richtlinieninformationen werden weiterhin im Dienst gespeichert. - Alle Sicherungszeitpläne werden entfernt, und keine weiteren Sicherungen werden vorgenommen.

Allerdings bleiben die in Azure gespeicherten Daten an Ort und Stelle und werden gemäß der von Ihnen eingerichteten Aufbewahrungsrichtlinie beibehalten. Ältere Punkte werden automatisch ersetzt.

## Remoteverwaltung
Die gesamte Verwaltung der Azure Backup-Agents, Richtlinien und Datenquellen kann remote über Windows PowerShell durchgeführt werden. Der Computer, der remote verwaltet wird, muss ordnungsgemäß vorbereitet werden.

Standardmäßig ist der WinRM-Dienst für den manuellen Start konfiguriert. Der Starttyp muss auf *Automatisch* festgelegt werden, und der Dienst sollte gestartet werden. Um sicherzustellen, dass der WinRM-Dienst ausgeführt wird, muss der Wert der Statuseigenschaft *Wird ausgeführt* lauten.

```
PS C:> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...

- Windows PowerShell should be configured for remoting.
```

```
PS C:> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:> Set-ExecutionPolicy unrestricted -force
```

Der Computer kann jetzt remote verwaltet werden - beginnend mit der Installation des Agents. Beispielsweise kopiert das folgende Skript den Agent auf den Remotecomputer und installiert ihn.

```
PS C:> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:> $args = "/q"
PS C:> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```
## Nächste Schritte
Weitere Informationen zu Azure Backup für Windows-Server und -Clients finden Sie unter [Einführung in Azure Backup](backup-introduction-to-azure-backup.md)

<!---HONumber=62-->