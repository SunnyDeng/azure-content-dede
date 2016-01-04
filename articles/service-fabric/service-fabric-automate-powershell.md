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
	ms.date="12/14/2015"
	ms.author="ryanwi"/>

# Bereitstellen, Aktualisieren, Testen und Entfernen von Service Fabric-Anwendungen mithilfe von PowerShell

Dieser Artikel veranschaulicht die Automatisierung allgemeiner Aufgaben zum Bereitstellen, Aktualisieren, Entfernen und Testen von Service Fabric-Anwendungen mithilfe von PowerShell.

## Voraussetzungen

Bevor Sie mit den Aufgaben in diesem Artikel beginnen, müssen Sie [Laufzeit, SDK und Tools installieren](service-fabric-get-started.md). Dabei werden auch die **ServiceFabric**- und **ServiceFabricTestability**-PowerShell-Module installiert. [Aktivieren Sie die PowerShell-Skriptausführung](service-fabric-get-started.md#enable-powershell-script-execution), und [installieren und starten Sie einen lokalen Cluster](service-fabric-get-started.md#install-and-start-a-local-cluster), damit Sie die Beispiele in diesem Artikel ausführen können.

Die Beispiele in diesem Artikel verwenden die [**WordCount**-Beispielanwendung](http://aka.ms/servicefabricsamples) (zählt zu den Erste-Schritte-Beispielen). Laden Sie die Beispielanwendung herunter, und erstellen Sie sie.

Bevor Sie die in diesem Artikel aufgeführten PowerShell-Befehle ausführen, stellen Sie zuerst mit [**Connect-ServiceFabricCluster**](https://msdn.microsoft.com/library/azure/mt125938.aspx) eine Verbindung mit dem lokalen Service Fabric-Cluster her:

```powershell
Connect-ServiceFabricCluster localhost:19000
```

## Aufgabe: Bereitstellen einer Service Fabric-Anwendung

Nachdem Sie die Anwendung erstellt haben, und der Anwendungstyp gepackt wurde, können Sie die Anwendung in einem lokalen Service Fabric-Cluster bereitstellen. Packen Sie zuerst die WordCount-Anwendung in Visual Studio, indem Sie im Projektmappen-Explorer mit der rechten Maustaste auf **WordCount** klicken und **Paket** auswählen. Unter [Modellieren von Anwendungen in Service Fabric](service-fabric-application-model.md) finden Sie Informationen zu den Dienst- und Anwendungsmanifesten und zum Paketlayout. Die Bereitstellung umfasst das Hochladen des Anwendungspakets, das Registrieren des Anwendungstyps und das Erstellen der Anwendungsinstanz. Verwenden Sie die Anweisungen in diesem Abschnitt, um eine neue Anwendung in einem Cluster bereitzustellen.

### Schritt 1: Hochladen des Anwendungspakets
Beim Hochladen des Anwendungspakets in den ImageStore wird das Paket an einem Speicherort gespeichert, an dem interne Service Fabric-Komponenten auf das Paket zugreifen können. Das Anwendungspaket enthält die erforderlichen Anwendungs- und Dienstmanifeste sowie Code-/Konfigurations-/Datenpakete zum Erstellen der Anwendungs- und Dienstinstanzen. Mit dem Befehl [**Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) wird das Paket hochgeladen. Beispiel:

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### Schritt 2: Registrieren des Anwendungstyps
Beim Registrieren des Anwendungspakets werden der Anwendungstyp und die Version im verfügbaren Anwendungsmanifest deklariert. Das System liest das im vorherigen Schritt hochgeladene Paket, überprüft es (entspricht der lokalen Ausführung von[**Test-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx)), verarbeitet den Inhalt des Pakets und kopiert das verarbeitete Paket an einen internen Systemspeicherort. Führen Sie das Cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) aus:

```powershell
Register-ServiceFabricApplicationType WordCount
```
Um die im Cluster registrierten Anwendungstypen anzuzeigen, führen Sie das Cmdlet aus:

```powershell
Get-ServiceFabricApplicationType
```

### Schritt 3: Erstellen der Anwendungsinstanz
Eine Anwendung kann mit einer beliebigen Version des Anwendungstyps instanziiert werden, die mit dem Befehl [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx) erfolgreich registriert wurde. Der Name jeder Anwendung muss mit dem **fabric:**-Schema beginnen und für jede Anwendungsinstanz eindeutig sein. Der Name des Anwendungstyps und die Version des Anwendungstyps werden in der Datei **ApplicationManifest.xml** deklariert. Wenn im Anwendungsmanifest des Zielanwendungstyps Standarddienste festgelegt wurden, werden diese ebenfalls in diesem Schritt erstellt.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

Der Befehl [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) listet alle erfolgreich erstellten Anwendungsinstanzen zusammen mit ihrem Gesamtzustand auf. Der Befehl [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) listet alle Dienstinstanzen auf, die innerhalb einer bestimmten Anwendungsinstanz erfolgreich erstellt wurden. Die Standarddienste (sofern vorhanden) werden aufgelistet.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## Aufgabe: Aktualisieren einer Service Fabric-Anwendung

Sie können eine zuvor bereitgestellte Service Fabric-Anwendung aktualisieren. Mit dieser Aufgabe wird die WordCount -Anwendung aktualisiert, die unter „Aufgabe: Bereitstellen einer Service Fabric-Anwendung“ bereitgestellt wurde. Weitere Informationen finden Sie unter [Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md).

### Schritt 1: Aktualisieren der Anwendung

Ändern Sie den Code im WordCount-Dienst.

Nach dem Aktualisieren des Dienstcodes müssen Sie die Versionsnummer des Diensts in der Datei **ServiceManifest.xml** erhöhen (sie befindet sich im Verzeichnis **PackageRoot** des WordCount-Projekts). Suchen Sie das **CodePackage**-Element des Manifests, und ändern Sie die Dienstversion in 2.0.0. Die entsprechenden Zeilen in der Datei "ServiceManifest.xml" sollten wie folgt lauten:

```xml
<ServiceManifest Name="WordCountServicePkg" Version="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="WordCountServiceType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="2.0.0">
	  ...
```

Nun müssen Sie die Datei „ApplicationManifest.xml“ aktualisieren (sie befindet sich im WordCount-Anwendungsprojekt unter der WordCount-Lösung). Aktualisieren Sie das **ServiceManifestRef**-Element, sodass Version 2.0.0.0 des **WordCountServicePkg-Projekts** verwendet wird. Aktualisieren Sie auch **ApplicationTypeVersion** von 1.0.0.0 auf 2.0.0.0. Die entsprechenden Zeilen in der Datei "ApplicationManifest.xml" sollten wie folgt lauten:

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="WordCount" ApplicationTypeVersion="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
...
<ServiceManifestRef ServiceManifestName="WordCountServicePkg" ServiceManifestVersion="2.0.0" />
```

Nachdem Sie diese Änderungen vorgenommen haben, speichern Sie die Dateien, und erstellen Sie das WordCount-Projekt neu. Packen Sie jetzt die aktualisierte Anwendung durch Klicken mit der rechten Maustaste auf das WordCount-Projekt, und wählen Sie **Paket**. Damit wird ein Anwendungspaket erstellt, das bereitgestellt werden kann. Die aktualisierte Anwendung kann nun bereitgestellt werden.

### Schritt 2: Kopieren und Registrieren des aktualisierten Anwendungspakets

Die Anwendung ist nun erstellt und gepackt und kann aktualisiert werden. Wenn Sie ein PowerShell-Fenster als Administrator öffnen und [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) eingeben, sollte angezeigt werden, dass Version 1.0.0 des WordCount-Anwendungstyps bereitgestellt wurde. Für das WordCount-Beispiel befindet sich das Anwendungspaket in: *C:\\ServiceFabricSamples\\Services\\WordCount\\WordCount\\pkg\\Debug*.

Kopieren Sie jetzt das aktualisierte Anwendungspaket in den Service Fabric-ImageStore (in dem die Anwendungspakete von Service Fabric gespeichert werden). Der **ApplicationPackagePathInImageStore**-Parameter informiert Service Fabric darüber, wo sich das Anwendungspaket befindet. Der folgende Befehl kopiert das Anwendungspaket nach **WordCountV2** im ImageStore:

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2
```

Im nächsten Schritt wird die neue Version der Anwendung bei Service Fabric registriert. Dazu kann das Cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) verwendet werden:

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

Wenn dieser Befehl nicht erfolgreich ist, müssen Sie den Dienst möglicherweise neu erstellen, wie in Schritt 1 beschrieben.

### Schritt 3: Starten des Upgrades
Auf Anwendungsupgrades können verschiedene Upgradeparameter, Timeoutwerte und Integritätskriterien angewendet werden. Weitere Informationen finden Sie in den Dokumenten [Parameter für Anwendungsupgrades](service-fabric-application-upgrade-parameters.md) und [Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md). In dieser exemplarischen Vorgehensweise behalten Sie die Standardwerte (und die empfohlenen Werte) der Evaluierungskriterien für die Dienstintegrität bei. Alle Dienste und Instanzen sollten nach dem Upgrade _fehlerfrei_ sein. Sie sollten jedoch**HealthCheckStableDuration** auf 60 Sekunden erhöhen (sodass die Dienste mindestens 20 Sekunden fehlerfrei sind, bevor zur nächsten Upgradedomäne übergegangen wird). Wir legen außerdem **UpgradeDomainTimeout** auf 1.200 Sekunden und **UpgradeTimeout** auf 3.000 Sekunden fest. Schließlich legen wir für **UpgradeFailureAction** den Wert **rollback** fest, sodass Service Fabric die Anwendung auf die vorherige Version zurücksetzt, wenn während des Upgrades Fehler erkannt werden.

Nun können Sie das Anwendungsupgrade mithilfe des Cmdlets [**Start ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) beginnen:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Beachten Sie, dass der Name der Anwendung dem Namen der zuvor bereitgestellten v1.0.0-Anwendung entspricht (fabric:/WordCount). Service Fabric verwendet diesen Namen, um die zu aktualisierende Anwendung zu ermitteln. Wenn Sie die Timeoutwerte zu kurz festlegen, wird möglicherweise eine Fehlermeldung zu den Timeoutwerten angezeigt. Lesen Sie [Problembehandlung bei Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md), oder erhöhen Sie die Timeoutwerte.

Sie können den Fortschritt des Anwendungsupgrades mit dem [Service Fabric-Explorer](service-fabric-visualizing-your-cluster.md) oder dem Cmdlet [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) überwachen:

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

In wenigen Minuten sollte das Cmdlet [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) anzeigen, dass alle Upgradedomänen (vollständig) aktualisiert wurden.

## Aufgabe: Testen einer Service Fabric-Anwendung

Um hochwertige Dienste schreiben zu können, müssen Entwickler dazu in der Lage sein, Fehler in unzuverlässigen Infrastrukturen auszulösen, um die Stabilität ihrer Dienste testen zu können. Service Fabric ermöglicht Entwicklern das Auslösen von Fehleraktionen, um das Verhalten von Diensten beim Auftreten von Fehlern mit Chaos- und Failovertestszenarien zu testen. Weitere Informationen finden Sie unter [Testability – Übersicht](service-fabric-testability-overview.md).

### Schritt 1: Ausführen des Chaostestszenarios
Beim Chaosszenario werden Fehler im gesamten Service Fabric-Cluster generiert. Fehler, die normalerweise in Zeiträumen von mehreren Monaten oder Jahren auftreten, werden auf wenige Stunden komprimiert. Bei dieser Kombination aus überlappenden Fehlern mit hoher Fehlerrate werden auch Spezialfälle erkannt, die sonst nicht auffallen würden. Im folgenden Beispiel wird das Chaostestszenario für 60 Minuten ausgeführt.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### Schritt 2: Ausführen des Failovertestszenarios
Das Failovertestszenario zielt auf eine bestimmte Dienstpartition ab, statt auf den gesamten Cluster. Andere Dienste sind davon nicht betroffen. Bei diesem Szenario werden eine Sequenz von simulierten Fehlern und die Dienstüberprüfung durchlaufen, während Ihre Geschäftslogik ausgeführt wird. Ein Fehler bei der Dienstüberprüfung weist auf ein Problem hin, das genauer untersucht werden muss. Beim Failovertest wird nur jeweils ein Fehler ausgelöst, während beim Chaostestszenario mehrere Fehler ausgelöst werden können. Im folgenden Beispiel wird der Failovertest für 60 Minuten für den fabric:/WordCount/WordCountService-Dienst ausgeführt.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Connect-ServiceFabricCluster

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## Aufgabe: Entfernen einer Service Fabric-Anwendung
Sie können eine Instanz einer bereitgestellten Anwendung löschen, den bereitgestellten Anwendungstyp aus dem Cluster entfernen und das Anwendungspaket aus dem ImageStore entfernen.

### Schritt 1: Entfernen einer Anwendungsinstanz
Wird eine Anwendungsinstanz nicht mehr benötigt, kann diese mit dem Cmdlet [**Remove-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx) dauerhaft entfernt werden. Hierbei werden auch alle Dienste automatisch entfernt, die mit der Anwendung verknüpft sind, d. h., der Dienstzustand wird vollständig und dauerhaft entfernt. Dieser Vorgang kann nicht rückgängig gemacht werden, und der Anwendungsstatus kann nicht wiederhergestellt werden.

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

<!---HONumber=AcomDC_1217_2015-->