<properties
	pageTitle="Automatisieren der Service Fabric-Anwendungsverwaltung mithilfe von PowerShell | Microsoft Azure"
	description="Bereitstellen, Aktualisieren, Testen und Entfernen von Service Fabric-Anwendungen mithilfe von PowerShell."
	services="service-fabric"
	documentationCenter=".net"
	authors="rwike77"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-fabric"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/02/2016"
	ms.author="ryanwi"/>

# Automatisieren des Anwendungslebenszyklus mithilfe von PowerShell

Viele Aspekte des [Service Fabric-Anwendungslebenszyklus](service-fabric-application-lifecycle.md) können automatisiert werden. Dieser Artikel veranschaulicht die Automatisierung allgemeiner Aufgaben zum Bereitstellen, Aktualisieren, Entfernen und Testen von Service Fabric-Anwendungen mithilfe von PowerShell.

## Voraussetzungen
Bevor Sie mit den Aufgaben in diesem Artikel beginnen, müssen Sie Folgendes sicherstellen:

+ Machen Sie sich mit den unter [Technische Übersicht über Service Fabric](service-fabric-technical-overview.md) beschriebenen Service Fabric-Konzepten vertraut.
+ [Installieren Sie die Laufzeitversion, das SDK und die Tools](service-fabric-get-started.md). Dabei wird auch das PowerShell-Modul **ServiceFabric** installiert.
+ [Aktivieren Sie die PowerShell-Skriptausführung.](service-fabric-get-started.md#enable-powershell-script-execution)
+ Starten Sie einen lokalen Cluster. Öffnen Sie ein neues PowerShell-Fenster als Administrator, und führen Sie das Clustersetupskript aus dem SDK-Ordner aus: `& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
+ Bevor Sie die in diesem Artikel aufgeführten PowerShell-Befehle ausführen, stellen Sie zuerst mit [**Connect-ServiceFabricCluster**](https://msdn.microsoft.com/library/azure/mt125938.aspx) eine Verbindung mit dem lokalen Service Fabric-Cluster her: `Connect-ServiceFabricCluster localhost:19000`
+ Die folgenden Aufgaben erfordern ein Anwendungspaket der Version 1 für die Bereitstellung und ein Anwendungspaket der Version 2 für das Upgrade. Laden Sie die [**WordCount**-Beispielanwendung](http://aka.ms/servicefabricsamples) (bei den Erste-Schritte-Beispielen zu finden) herunter. Erstellen und packen Sie die Anwendung in Visual Studio, indem Sie im Projektmappen-Explorer mit der rechten Maustaste auf **WordCount** klicken und **Paket** auswählen. Kopieren Sie das Paket der Version 1 in `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` nach `C:\Temp\WordCount`. Kopieren Sie `C:\Temp\WordCount` nach `C:\Temp\WordCountV2`, um das Anwendungspaket der Version 2 für das Upgrade zu erstellen. Öffnen Sie `C:\Temp\WordCountV2\ApplicationManifest.xml` in einem Text-Editor. Ändern Sie im **ApplicationManifest**-Element das **ApplicationTypeVersion**-Attribut von „1.0.0“ in „2.0.0“. Dadurch wird die Versionsnummer der Anwendung aktualisiert. Speichern Sie die geänderte Datei „ApplicationManifest.xml“.

## Aufgabe: Bereitstellen einer Service Fabric-Anwendung

Nachdem Sie die Anwendung erstellt und gepackt (oder das Anwendungspaket heruntergeladen) haben, können Sie die Anwendung in einem lokalen Service Fabric-Cluster bereitstellen. Die Bereitstellung umfasst das Hochladen des Anwendungspakets, das Registrieren des Anwendungstyps und das Erstellen der Anwendungsinstanz. Verwenden Sie die Anweisungen in diesem Abschnitt, um eine neue Anwendung in einem Cluster bereitzustellen.

### Schritt 1: Hochladen des Anwendungspakets
Beim Hochladen des Anwendungspakets in den ImageStore wird das Paket an einem Speicherort gespeichert, an dem interne Service Fabric-Komponenten auf das Paket zugreifen können. Das Anwendungspaket enthält die erforderlichen Anwendungs- und Dienstmanifeste sowie Code-, Konfigurations- und Datenpakete zum Erstellen der Anwendungs- und Dienstinstanzen. Mit dem Befehl [**Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) wird das Paket hochgeladen. Beispiel:

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### Schritt 2: Registrieren des Anwendungstyps
Beim Registrieren des Anwendungspakets werden der Anwendungstyp und die Version im verfügbaren Anwendungsmanifest deklariert. Das System liest das in Schritt 1 hochgeladene Paket, überprüft es (entspricht der lokalen Ausführung von [**Test-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx)), verarbeitet den Inhalt des Pakets und kopiert das verarbeitete Paket an einen internen Systemspeicherort. Führen Sie das Cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) aus:

```powershell
Register-ServiceFabricApplicationType WordCount
```
Um alle im Cluster registrierten Anwendungstypen anzuzeigen, führen Sie das Cmdlet [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125871.aspx) aus:

```powershell
Get-ServiceFabricApplicationType
```

### Schritt 3: Erstellen der Anwendungsinstanz
Eine Anwendung kann mit einer beliebigen Version des Anwendungstyps instanziiert werden, die mit dem Befehl [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx) erfolgreich registriert wurde. Der Name jeder Anwendung wird bei der Bereitstellung deklariert. Es muss mit dem **fabric:**-Schema beginnen und für jede Anwendungsinstanz eindeutig sein. Der Name des Anwendungstyps und die Version des Anwendungstyps werden in der Datei **ApplicationManifest.xml** des Anwendungspakets deklariert. Wenn im Anwendungsmanifest des Zielanwendungstyps Standarddienste festgelegt wurden, werden diese ebenfalls in diesem Schritt erstellt.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

Der Befehl [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) listet alle erfolgreich erstellten Anwendungsinstanzen zusammen mit ihrem Gesamtzustand auf. Der Befehl [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) listet alle Dienstinstanzen auf, die innerhalb einer bestimmten Anwendungsinstanz erfolgreich erstellt wurden. Die Standarddienste (sofern vorhanden) werden aufgelistet.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## Aufgabe: Aktualisieren einer Service Fabric-Anwendung
Sie können eine zuvor bereitgestellte Service Fabric-Anwendung mit einem Anwendungspaket aktualisieren. Mit dieser Aufgabe wird die WordCount -Anwendung aktualisiert, die unter „Aufgabe: Bereitstellen einer Service Fabric-Anwendung“ bereitgestellt wurde. Weitere Informationen finden Sie unter [Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md).

Um dieses Beispiel einfach zu halten, wurde nur die Versionsnummer der Anwendung im Anwendungspaket WordCountV2 aktualisiert, das bei den Voraussetzungen erstellt wurde. Ein realistischeres Szenario würde eine Aktualisierung der Dienstcode-, Konfigurations- oder Datendateien sowie das Packen der Anwendung mit aktualisierten Versionsnummern beinhalten.

### Schritt 1: Hochladen des aktualisierten Anwendungspakets
Die WordCount-Anwendung der Version 1 kann nun aktualisiert werden. Wenn Sie ein PowerShell-Fenster als Administrator öffnen und [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) eingeben, wird angezeigt, dass Version 1.0.0 des WordCount-Anwendungstyps bereitgestellt wurde.

Kopieren Sie jetzt das aktualisierte Anwendungspaket in den Service Fabric-ImageStore (in dem die Anwendungspakete von Service Fabric gespeichert werden). Der **ApplicationPackagePathInImageStore**-Parameter informiert Service Fabric darüber, wo sich das Anwendungspaket befindet. Der folgende Befehl kopiert das Anwendungspaket nach **WordCountV2** im ImageStore:

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### Schritt 2: Registrieren des aktualisierten Anwendungstyps
Im nächsten Schritt wird die neue Version der Anwendung bei Service Fabric registriert. Dazu kann das Cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) verwendet werden:

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### Schritt 3: Starten des Upgrades
Auf Anwendungsupgrades können verschiedene Upgradeparameter, Timeoutwerte und Integritätskriterien angewendet werden. Weitere Informationen finden Sie in den Dokumenten [Parameter für Anwendungsupgrades](service-fabric-application-upgrade-parameters.md) und [Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md). Alle Dienste und Instanzen sollten nach dem Upgrade _fehlerfrei_ sein. Legen Sie **HealthCheckStableDuration** auf 60 Sekunden fest (sodass die Dienste mindestens 20 Sekunden fehlerfrei sind, bevor zur nächsten Upgradedomäne übergegangen wird). Wir legen außerdem **UpgradeDomainTimeout** auf 1.200 Sekunden und **UpgradeTimeout** auf 3.000 Sekunden fest. Schließlich legen wir für **UpgradeFailureAction** den Wert **rollback** fest, sodass Service Fabric die Anwendung auf die vorherige Version zurücksetzt, wenn während des Upgrades Fehler erkannt werden.

Nun können Sie das Anwendungsupgrade mithilfe des Cmdlets [**Start ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) beginnen:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Beachten Sie, dass der Name der Anwendung dem Namen der zuvor bereitgestellten v1.0.0-Anwendung entspricht (fabric:/WordCount). Service Fabric verwendet diesen Namen, um die zu aktualisierende Anwendung zu ermitteln. Wenn Sie die Timeoutwerte zu kurz festlegen, wird möglicherweise eine Fehlermeldung zu den Timeoutwerten angezeigt. Lesen Sie [Problembehandlung bei Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md), oder erhöhen Sie die Timeoutwerte.

### Schritt 4: Überprüfen des Upgradevorgangs
Sie können den Fortschritt des Anwendungsupgrades mit dem [Service Fabric-Explorer](service-fabric-visualizing-your-cluster.md) oder dem Cmdlet [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) überwachen:

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

Nach wenigen Minuten zeigt das Cmdlet [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) an, dass alle Upgradedomänen (vollständig) aktualisiert wurden.

## Aufgabe: Testen einer Service Fabric-Anwendung

Um hochwertige Dienste schreiben zu können, müssen Entwickler dazu in der Lage sein, Fehler in unzuverlässigen Infrastrukturen auszulösen, um die Stabilität ihrer Dienste testen zu können. Service Fabric ermöglicht Entwicklern das Auslösen von Fehleraktionen, um das Verhalten von Diensten beim Auftreten von Fehlern mit Chaos- und Failovertestszenarien zu testen. Weitere Informationen finden Sie unter [Testability – Übersicht](service-fabric-testability-overview.md).

### Schritt 1: Ausführen des Chaostestszenarios
Beim Chaosszenario werden Fehler im gesamten Service Fabric-Cluster generiert. Fehler, die normalerweise in Zeiträumen von mehreren Monaten oder Jahren auftreten, werden auf wenige Stunden komprimiert. Bei dieser Kombination aus überlappenden Fehlern mit hoher Fehlerrate werden auch Spezialfälle erkannt, die sonst nicht auffallen würden. Im folgenden Beispiel wird das Chaostestszenario für 60 Minuten ausgeführt.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### Schritt 2: Ausführen des Failovertestszenarios
Das Failovertestszenario zielt auf eine bestimmte Dienstpartition ab, statt auf den gesamten Cluster. Andere Dienste sind davon nicht betroffen. Bei diesem Szenario werden eine Sequenz von simulierten Fehlern und die Dienstüberprüfung durchlaufen, während Ihre Geschäftslogik ausgeführt wird. Ein Fehler bei der Dienstüberprüfung weist auf ein Problem hin, das genauer untersucht werden muss. Beim Failovertest wird nur jeweils ein Fehler ausgelöst, während beim Chaostestszenario mehrere Fehler ausgelöst werden können. Im folgenden Beispiel wird der Failovertest für 60 Minuten für den fabric:/WordCount/WordCountService-Dienst ausgeführt.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## Aufgabe: Entfernen einer Service Fabric-Anwendung
Sie können eine Instanz einer bereitgestellten Anwendung löschen, den bereitgestellten Anwendungstyp aus dem Cluster entfernen und das Anwendungspaket aus dem ImageStore entfernen.

### Schritt 1: Entfernen einer Anwendungsinstanz
Wird eine Anwendungsinstanz nicht mehr benötigt, kann diese mit dem Cmdlet [**Remove-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx) dauerhaft entfernt werden. Hierbei werden auch alle Dienste automatisch entfernt, die mit der Anwendung verknüpft sind, d. h., der Dienstzustand wird vollständig und dauerhaft entfernt. Dieser Vorgang kann nicht rückgängig gemacht werden, und der Anwendungsstatus kann nicht wiederhergestellt werden.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### Schritt 2: Aufheben der Registrierung des Anwendungstyps
Wenn Sie eine bestimmte Version eines Anwendungstyps nicht mehr benötigen, heben Sie die Registrierung der Version mit dem Cmdlet [**Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx) auf. Durch das Aufheben der Registrierung nicht verwendeter Typen wird Speicherplatz freigegeben, der vom Anwendungspaket im ImageStore verwendet wird. Die Registrierung eines Anwendungstyps kann nur aufgehoben werden, wenn keine Anwendungen für den Typ instanziiert sind und keine ausstehenden Anwendungsupgrades vorliegen, die auf den Typ verweisen.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### Schritt 3: Entfernen des Anwendungspakets
Nachdem die Registrierung des Anwendungstyps aufgehoben wurde, kann das Anwendungspaket mit dem Cmdlet [**Remove-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx) aus dem ImageStore entfernt werden.

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## Nächste Schritte
[Service Fabric-Anwendungslebenszyklus](service-fabric-application-lifecycle.md)

[Bereitstellen von Anwendungen](service-fabric-deploy-remove-applications.md)

[Anwendungsupgrade](service-fabric-application-upgrade.md)

[Azure Service Fabric-Cmdlets](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Azure Service Fabric-Testability-Cmdlets](https://msdn.microsoft.com/library/azure/mt125844.aspx)

<!---HONumber=AcomDC_0309_2016-->