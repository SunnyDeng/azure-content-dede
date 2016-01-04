<properties
   pageTitle="Service Fabric-Anwendungsupgrade mit PowerShell | Microsoft Azure"
   description="Dieser Artikel bietet eine exemplarische Vorgehensweise für das Bereitstellen einer Service Fabric-Anwendung, Ändern des Codes und Einführen eines Upgrades mithilfe von PowerShell."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>



# Service Fabric-Anwendungsupgrade per PowerShell

Die am häufigsten angewendete und empfohlene Methode für Upgrades ist das überwachte parallele Upgrade. Service Fabric überwacht die Integrität der aktualisiertem Anwendung basierend auf einer Reihe von Integritätsrichtlinien. Wenn die Anwendungen in einer Upgradedomäne (UD) aktualisiert wurden, evaluiert Service Fabric die Anwendungsintegrität und ermittelt, ob mit der nächsten Upgradedomäne fortgefahren wird oder ob das Upgrade basierend auf den Integritätsrichtlinien fehlschlägt. Ein überwachtes Anwendungsupgrade kann mithilfe der verwalteten oder systemeigenen APIs, PowerShell oder REST ausgeführt werden. Eine Anleitung zum Durchführen eines Upgrades mit Visual Studio finden Sie unter [Upgrade Ihrer Anwendung mit Visual Studio](service-fabric-application-upgrade-tutorial.md).

Bei dem von Service Fabric überwachten parallelen Upgrade kann der Anwendungsadministrator die Integritätsevaluierungsrichtlinie konfigurieren, mit der Service Fabric ermittelt, ob die Anwendung fehlerfrei ist. Darüber hinaus kann der Administrator auch die Aktion konfigurieren, die ausgeführt wird, wenn die Integritätsevaluierung fehlschlägt, z. B. einen automatischen Rollback. In diesem Abschnitt wird ein überwachtes Upgrade für eines der SDK-Beispiele mit PowerShell Schritt für Schritt erläutert.

## Schritt 1: Erstellen und Bereitstellen des Beispiels „Visual Objects“

Diese Schritte können erfolgen, indem Sie die Anwendung von GitHub herunterladen und die Dateien **webgl-utils.js** und **gl-matrix-min.js** (wie in der Infodatei des Beispiels beschrieben) dem Projekt hinzufügen. Ohne diesen Schritt funktioniert die Anwendung nicht. Nachdem Sie die Dateien dem Projekt hinzugefügt haben, erstellen und veröffentlichen Sie die Anwendung, indem Sie mit der rechten Maustaste auf das Anwendungsprojekt klicken, dann auf **VisualObjectsApplication** klicken und wie folgt im Menü „Service Fabric“ den Befehl „Veröffentlichen“ auswählen. Weitere Informationen finden Sie im [Lernprogramm für Service Fabric-Anwendungsupgrades](service-fabric-application-tutorial.md). Alternativ können Sie Ihre Anwendung mithilfe von PowerShell bereitstellen.

> [AZURE.NOTE]Bevor Sie Service Fabric-Befehle in PowerShell verwenden können, müssen Sie zunächst mit dem Cmdlet `Connect-ServiceFabricCluster` eine Verbindung mit dem Cluster herstellen. Auch wird davon ausgegangen, dass der Cluster bereits auf dem lokalen Computer eingerichtet wurde. Siehe dazu den Artikel [Set up your Service Fabric development environment](service-fabric-get-started.md) (in englischer Sprache).

Nach dem Erstellen des Projekts in Visual Studio können Sie mit dem PowerShell-Befehl **Copy-ServiceFabricApplicationPackage** das Anwendungspaket in den ImageStore kopieren und dann mit dem Cmdlet **Register-ServiceFabricApplicationPackage** die Anwendung in der Service Fabric-Laufzeitumgebung registrieren. Schließlich können Sie mithilfe des Cmdlets **New-ServiceFabricApplication** eine Instanz der Anwendung starten. Diese drei Schritte entsprechen der Verwendung des Menüelements "Bereitstellen" in Visual Studio.

Nun können Sie mit dem [Service Fabric-Explorer den Cluster und die Anwendung anzeigen](service-fabric-visualizing-your-cluster.md). Die Anwendung verfügt über einen Webdienst, zu dem Sie wechseln können, indem Sie in der Adressleiste von Internet Explorer [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) eingeben. Sie sollten einige unverankerte visuelle Objekte sehen, die sich auf dem Bildschirm bewegen. Darüber hinaus können Sie **Get-ServiceFabricApplication** zum Überprüfen des Anwendungsstatus verwenden.

## Schritt 2: Aktualisieren des Beispiels „Visual Objects“

Sie werden feststellen, dass sich die visuellen Objekte mit der Version, die in Schritt 1 bereitgestellt wurde, nicht drehen. Wir aktualisieren diese Anwendung so, dass sich die visuellen Objekte drehen.

Wählen Sie das Projekt „VisualObjects.ActorService“ in der Projektmappe „VisualObjects“ aus, und öffnen Sie die Datei „StatefulVisualObjectActor.cs“. Navigieren Sie in der Datei zur `MoveObject`-Methode, kommentieren Sie `this.State.Move()` aus, und kommentieren Sie `this.State.Move(true)` aus. Diese Änderung bewirkt, dass sich die Objekte nach dem Upgrade des Diensts drehen.

Wir müssen auch die Datei *ServiceManifest.xml* (unter „PackageRoot“) des Projekts **VisualObjects.DataService** aktualisieren. Aktualisieren Sie *CodePackage* und die Dienstversion auf 2.0., und aktualisieren Sie die entsprechenden Zeilen in der Datei *ServiceManifest.xml*. Sie können den Visual Studio-Befehl *Manifestdateien bearbeiten* verwenden, nachdem Sie mit der rechten Maustaste auf die Projektmappe geklickt haben, um Änderungen an der Manifestdatei vorzunehmen – siehe [Lernprogramm für Service Fabric-Anwendungsupgrades](service-fabric-application-tutorial.md).


Nach diesen Änderungen sollte das Manifest wie folgt aussehen (Änderungen sind hervorgehoben):

```xml
<ServiceManifestName="VisualObjects.ActorService"Version="2.0"xmlns="http://schemas.microsoft.com/2011/01/fabric"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code"Version="2.0">
```

Nun müssen wir die Datei *ApplicationManifest.xml* (im Projekt **VisualObjects** unter der Projektmappe **VisualObjects**) so aktualisieren, dass Version 2.0 des Projekts **VisualObjects.ActorService** verwendet wird. Außerdem muss die Version der Anwendung von „1.0.0.0“ in „2.0.0.0“ geändert werden. Die entsprechenden Zeilen in der Datei *ApplicationManifest.xml* sollten wie folgt lauten:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"ApplicationTypeName="VisualObjects"ApplicationTypeVersion="2.0.0.0"xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService"ServiceManifestVersion="2.0" />
```


Erstellen Sie jetzt das Projekt, indem Sie das Projekt **ActorService** auswählen, mit der rechten Maustaste klicken und „Erstellen“ auswählen. (Wenn Sie „Alles neu erstellen“ auswählen, müssen Sie möglicherweise die Versionen für andere Projekte sowie in den zugehörigen Dateien *ServiceManifest.xml* und *ApplicationManifest.xml* aktualisieren, da sich der Code geändert hat.) Jetzt packen Sie die aktualisierte Anwendung, indem Sie mit der rechten Maustaste auf das Projekt *VisualObjectsApplication* klicken und anschließend das Service Fabric-Menü und dann „Paket“ auswählen. Damit wird ein Anwendungspaket erstellt, das bereitgestellt werden kann. Die aktualisierte Anwendung kann nun bereitgestellt werden.


## Schritt 3: Festlegen der Integritätsrichtlinien und Upgradeparameter

Machen Sie sich mit den [Anwendungsupgradeparametern](service-fabric-application-upgrade-parameters.md) und dem [Upgradevorgang](service-fabric-application-upgrade.md) vertraut, um sich eine umfassende Übersicht über die verschiedenen Upgradeparameter, Timeouts und Integritätskriterien zu verschaffen. In dieser exemplarischen Vorgehensweise behalten wir das Standardevaluierungskriterium für die Dienstintegrität (und die empfohlenen Werte) bei, d. h., dass alle Dienste und Instanzen nach dem Upgrade _fehlerfrei_ sein sollten. Lassen Sie uns aber *HealthCheckStableDuration* auf 60 Sekunden erhöhen (sodass die Dienste mindestens 20 Sekunden fehlerfrei sind, bevor zur nächsten Upgradedomäne übergegangen wird). Wir legen außerdem *UpgradeDomainTimeout* auf 1200 Sekunden und *UpgradeTimeout* auf 3000 Sekunden fest. Schließlich legen wir für *UpgradeFailureAction* den Wert "Rollback" fest, sodass Service Fabric die Anwendung auf die vorherige Version zurücksetzt, wenn während des Upgrades Probleme erkannt werden. Somit haben wir folgende Upgradeparameter für den Start des Upgrades (in Schritt 4) angegeben:

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000


## Schritt 4: Vorbereiten der Anwendung für das Upgrade

Die Anwendung ist nun erstellt und kann aktualisiert werden. Wenn Sie als Administrator ein PowerShell-Fenster öffnen und **Get-ServiceFabricApplication** eingeben, sollte angezeigt werden, dass der Anwendungstyp "1.0.0.0" von **VisualObjects** bereitgestellt wurde. Das Anwendungspaket ist unter dem folgenden relativen Pfad gespeichert, in dem Sie das Service Fabric-SDK dekomprimiert haben: *Samples\\Services\\Stateful\\VisualObjects\\VisualObjects\\obj\\x64\\Debug*. In diesem Verzeichnis befindet sich der Ordner "Package" – hier ist das Anwendungspaket gespeichert. Überprüfen Sie die Zeitstempel, um sicherzustellen, dass es sich um die neueste Version handelt (möglicherweise müssen Sie auch die Pfade entsprechend ändern).

Jetzt kopieren Sie das aktualisierte Anwendungspaket in den Service Fabric-ImageStore (in dem die Anwendungspakete von Service Fabric gespeichert werden). Der *ApplicationPackagePathInImageStore*-Parameter informiert Service Fabric darüber, wo sich das Anwendungspaket befindet. Wir haben die aktualisierte Anwendung mit dem folgenden Befehl in "VisualObjects\_V2" abgelegt (möglicherweise müssen Sie die Pfade wieder entsprechend ändern).

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Im nächsten Schritt wird diese Anwendung bei Service Fabric registriert. Dies kann über den folgenden Befehl erfolgen:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Wenn der obige Befehl nicht erfolgreich ausgeführt wird, müssen Sie wahrscheinlich alle Dienste neu erstellen. Wie in Schritt 2 erwähnt, müssen Sie möglicherweise auch Ihre WebService-Version aktualisieren.

## Schritt 5: Starten des Anwendungsupgrades

Jetzt können wir das Anwendungsupgrade mit dem folgenden Befehl starten:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Beachten Sie, dass als Anwendungsname der in der Datei *ApplicationManifest.xml* angegebene Name verwendet wird. Service Fabric verwendet diesen Namen, um die zu aktualisierende Anwendung zu ermitteln. Wenn Sie die Timeoutwerte zu kurz festlegen, wird möglicherweise eine entsprechende Fehlermeldung angezeigt. Diesbezügliche Informationen finden Sie im Abschnitt "Problembehandlung". Sie können die Timeoutwerte erhöhen.

Während das Anwendungsupgrade durchgeführt wird, können Sie es im Service Fabric-Explorer oder mit dem folgenden PowerShell-Befehl überwachen: **Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects**.

Nach wenigen Minuten sollte nach Verwendung des obigen PowerShell-Befehls angezeigt werden, dass alle Upgradedomänen aktualisiert (abgeschlossen) wurden. Und Sie werden feststellen, dass die visuellen Objekte in Ihrem Browserfenster sich jetzt drehen!

Sie können auch versuchen, als Übung Version 2 in Version 3 oder gar Version 2 zurück in Version 1 zu ändern (ja, ein Upgrade von Version 2 auf Version 1 ist möglich). Experimentieren Sie mit den Timeouts und Integritätsrichtlinien, um sich schrittweise vertraut zu machen. Bei der Bereitstellung in einem Azure-Cluster unterscheiden sich die Parameter von den bei der Bereitstellung in einem lokalen Cluster verwendeten Parametern. Daher empfiehlt sich eine konservative Festlegung der Timeouts.


## Nächste Schritte

Unter [Upgrade Ihrer Anwendung mit Visual Studio](service-fabric-application-upgrade-tutorial.md) werden Sie schrittweise durch das Upgrade der Anwendung mithilfe von Visual Studio geführt.

Steuern Sie die Upgrades von Anwendungen mithilfe von [Upgradeparametern](service-fabric-application-upgrade-parameters.md).

Machen Sie Ihre Anwendungsupgrades kompatibel, indem Sie sich mit der [Datenserialisierung](service-fabric-application-upgrade-data-serialization.md) vertraut machen.

Erfahren Sie, wie Sie erweiterte Funktionen beim Upgrade Ihrer Anwendung nutzen, indem Sie sich mit den [weiterführenden Themen](service-fabric-application-upgrade-advanced.md) beschäftigen.

Informationen zum Beheben gängiger Probleme bei Anwendungsupgrades finden Sie in den Anweisungen unter [Problembehandlung bei Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md).

<!---HONumber=AcomDC_1125_2015-->