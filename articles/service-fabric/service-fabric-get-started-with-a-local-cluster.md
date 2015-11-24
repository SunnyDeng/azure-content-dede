<properties
   pageTitle="Erste Schritte beim Bereitstellen und Aktualisieren von Apps im lokalen Cluster | Microsoft Azure"
   description="Richten Sie einen lokalen Service Fabric-Cluster ein, stellen Sie eine vorhandene Anwendung in dem Cluster bereit, und aktualisieren Sie die Anwendung."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/16/2015"
   ms.author="seanmck"/>

# Erste Schritte beim Bereitstellen und Aktualisieren von Anwendungen im lokalen Cluster
Das Service Fabric-SDK umfasst eine vollständige lokale Entwicklungsumgebung und ermöglicht es Ihnen, schnell mit der Bereitstellung und Verwaltung von Anwendungen in einem lokalen Cluster zu beginnen. In diesem Artikel verwenden Sie Windows PowerShell, um einen lokalen Cluster zu erstellen, eine vorhandene Anwendung bereitzustellen und die Anwendung auf eine neue Version zu aktualisieren.

> [AZURE.NOTE]In diesem Artikel wird vorausgesetzt, dass Sie bereits [eine Entwicklungsumgebung eingerichtet haben](service-fabric-get-started.md).

## Erstellen eines lokalen Clusters
Ein Service Fabric-Cluster ist ein Satz von Hardwareressourcen, auf dem Sie Anwendungen bereitstellen können. In der Regel besteht ein Cluster aus 5 bis zu mehreren 1000 Computern, aber das Service Fabric-SDK enthält eine Clusterkonfiguration, die auf einem einzelnen Computer ausgeführt werden kann.

> [AZURE.NOTE]Der lokale Service Fabric-Cluster ist kein Emulator oder Simulator. Er führt den gleichen Plattformcode wie Cluster mit mehreren Computern aus. Der einzige Unterschied ist, dass die Plattformprozesse, die normalerweise auf fünf Computer verteilt sind, auf einem ausgeführt werden.

Das SDK bietet zwei Möglichkeiten zum Einrichten eines lokalen Clusters: ein Windows PowerShell-Skript und die Taskleisten-App Local Cluster Manager. In diesem Tutorial wird das PowerShell-Skript verwendet.

> [AZURE.NOTE]Wenn Sie bereits einen lokalen Cluster durch die Bereitstellung einer Anwendung über Visual Studio erstellt haben, können Sie diesen Abschnitt überspringen.

1. Starten Sie als Administrator ein neues PowerShell-Fenster.
2. Führen Sie das Skript für die Clustereinrichtung aus dem SDK-Ordner aus:

	```powershell
	& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
	```

  Die Clustereinrichtung dauert eine Weile. Anschließend sollte eine Ausgabe angezeigt werden, die etwa wie folgt aussieht:

  ![Ausgabe bei der Clustereinrichtung][cluster-setup-success]

  Sie können nun damit beginnen, eine Anwendung in Ihrem Cluster bereitzustellen.

## Bereitstellen von Anwendungen
Das Service Fabric-SDK umfasst eine Vielzahl von Frameworks und Entwicklertools zum Erstellen von Anwendungen. Wenn Sie lernen möchten, wie Sie Anwendungen in Visual Studio erstellen können, lesen Sie [Erstellen Ihrer ersten Anwendung in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md). In diesem Tutorial verwenden wir eine vorhandene Beispielanwendung (mit dem Namen WordCount), damit wir uns auf die Verwaltung der Plattform konzentrieren können, einschließlich Bereitstellung, Überwachung und Upgrade.

1. Starten Sie als Administrator ein neues PowerShell-Fenster.
2. Importieren Sie das Service Fabric-SDK für das PowerShell-Modul.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. Erstellen Sie ein Verzeichnis zum Speichern der Anwendung, die Sie herunterladen und bereitstellen, z. B. C:\\Service Fabric.

  ```powershell
  mkdir c:\ServiceFabric\
  cd c:\ServiceFabric\
  ```

4. Laden Sie die WordCount-Anwendung von [hier](http://aka.ms/servicefabric-wordcountapp) in den Speicherort herunter, den Sie erstellt haben.

5. Stellen Sie eine Verbindung mit dem lokalen Cluster her:

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Rufen Sie den Bereitstellungsbefehl des SDK zum Erstellen einer neuen Anwendung auf, geben Sie dabei einen Namen und einen Pfad zum Anwendungspaket an.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

  Wenn alles funktioniert, sollte folgende Ausgabe angezeigt werden:

  ![Bereitstellen einer Anwendung im lokalen Cluster][deploy-app-to-local-cluster]

7. Um die Anwendung in Aktion zu sehen, starten Sie den Browser, und navigieren Sie zu [http://localhost:8081/wordcount/index](http://localhost:8081/wordcount/index). Die Ausgabe sollte folgendermaßen aussehen:

  ![Benutzeroberfläche der bereitgestellten Anwendung][deployed-app-UI]

  Die WordCount-Anwendung ist sehr einfach. Sie enthält clientseitigen JavaScript-Code zum Generieren von zufälligen „Wörter“ mit fünf Zeichen, die dann über eine ASP.NET-WebAPI an die Anwendung weitergeleitet werden. Ein zustandsbehafteter Dienst verfolgt die Anzahl der gezählten Wörter, partitioniert basierend auf dem ersten Zeichen des Worts. Die Anwendung, die wir bereitgestellt haben, enthält vier Partitionen. Also werden Wörter, die mit A bis G beginnen, in der ersten Partition gespeichert, Wörter, die mit H bis N beginnen, werden in der zweiten Partition gespeichert usw.

## Anzeigen von Anwendungsdetails und des Anwendungsstatus
Sehen wir uns anhand der bereitgestellten Anwendung einige App-Details in PowerShell an.

1. Fragen Sie alle bereitgestellten Anwendungen im Cluster ab:

  ```powershell
  Get-ServiceFabricApplication
  ```

  Vorausgesetzt, dass Sie nur die WordCount-App bereitgestellt haben, sehen Sie etwa Folgendes:

  ![Abfrage aller bereitgestellten Anwendungen in PowerShell][ps-getsfapp]

2. Gehen Sie eine Ebene weiter, indem Sie die Dienste abfragen, die in der WordCount-Anwendung enthalten sind.

  ```powershell
  Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
  ```

  ![Auflisten von Diensten für die Anwendung in PowerShell][ps-getsfsvc]

  Beachten Sie, dass die Anwendung aus zwei Diensten besteht, dem Web-Front-End- und dem zustandsbehafteten Dienst, der die Wörter verwaltet.

3. Sehen Sie sich schließlich die Liste der Partitionen für WordCountService an:

  ![Anzeigen der Dienstpartitionen in PowerShell][ps-getsfpartitions]

  Die Befehle, die Sie gerade verwendet haben, sind wie alle Service Fabric-PowerShell-Befehle für alle Cluster verfügbar, mit denen Sie eine Verbindung herstellen – lokal oder remote.

  Für eine eher visuelle Möglichkeit der Interaktion mit dem Cluster können Sie das webbasierte Tool Service Fabric-Explorer verwenden, indem Sie im Browser zu [http://localhost:19080/Explorer](http://localhost:19080/Explorer) navigieren.

  ![Anzeigen von Anwendungsdetails im Service Fabric-Explorer][sfx-service-overview]

  >[AZURE.NOTE]Weitere Informationen zum Service Fabric-Explorer finden Sie unter [Visualisieren des Clusters mit dem Service Fabric-Explorer](service-fabric-visualizing-your-cluster.md)

## Upgraden einer Anwendung
Service Fabric ermöglicht Upgrades ohne Ausfallzeit, indem die Integrität der Anwendung überwacht wird, während sie im Cluster bereitgestellt wird. Führen Sie nun ein einfaches Upgrade der WordCount-Anwendung durch.

Die neue Version der Anwendung zählt nur die Wörter, die mit einem Vokal beginnen. Während das Upgrade bereitgestellt wird, sehen wir zwei Änderungen im Verhalten der Anwendung. Erstens sollte sich die Rate verlangsamen, mit der die Anzahl zunimmt, da weniger Wörter gezählt werden. Da die erste Partition zwei Vokale enthält („A“ und „E“) und alle anderen nur einen enthalten, sollte zweitens die Anzahl dieser Partition letztlich größer als die der anderen sein.

1. Laden Sie das Paket für Version 2 von [hier](http://aka.ms/servicefabric-wordcountappv2) in denselben Speicherort herunter, in den Sie das Paket für Version 1 heruntergeladen haben.

2. Kehren Sie zum PowerShell-Fenster zurück, und verwenden den Upgradebefehl des SDK, um die neue Version des Clusters zu registrieren und mit dem Upgrade von „fabric:/WordCount“ zu beginnen.

  ```powershell
  Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" @{"UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
  ```

  Die Ausgabe in PowerShell sollte etwa wie folgt aussehen, wenn das Upgrade beginnt.

  ![Upgradestatus in PowerShell][ps-appupgradeprogress]

3. Möglicherweise ist es für Sie während des Upgrades einfacher, dessen Status über den Service Fabric-Explorer zu überwachen. Öffnen Sie ein Browserfenster, und navigieren Sie zu [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Klicken Sie in der Struktur auf der linken Seite auf **Anwendungen**, und wählen Sie dann **Upgrades in Bearbeitung** aus.

  ![Upgradestatus im Service Fabric-Explorer][sfx-upgradeprogress]

  Beachten Sie, dass die Anzeige des Upgradefortschritts den Status des Upgrades innerhalb der Upgradedomänen Ihres Clusters darstellt. Während das Upgrade in den einzelnen Domänen durchgeführt wird, werden Integritätsprüfungen ausgeführt, um sicherzustellen, dass sich die Anwendung ordnungsgemäß verhält, bevor der Vorgang fortgesetzt wird.

4. Wenn Sie die vorherige Abfrage der Dienste, die in der Anwendung „fabric:/WordCount“ enthalten sind, erneut ausführen, werden Sie feststellen, dass sich die Version von WordCountService geändert hat, die Version von WordCountWebService jedoch nicht:

  ```powershell
  Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
  ```

  ![Abfragen der Anwendungsdienste nach dem Upgrade][ps-getsfsvc-postupgrade]

  Dadurch wird deutlich, wie Service Fabric Anwendungsupgrades verwaltet: Es werden nur die Dienste (bzw. Code-/Konfigurationspakete innerhalb dieser Dienste) verarbeitet, die geändert wurden. Dies macht Upgrades schneller und zuverlässiger.

5. Kehren Sie schließlich zum Browser zurück, um das Verhalten der neuen Anwendungsversion zu beobachten. Wie erwartet, steigt die Anzahl langsamer an, und die erste Partition enthält eine etwas größere Zahl.

  ![Anzeigen der neuen Version der Anwendung im Browser][deployed-app-UI-v2]

## Nächste Schritte
- Nachdem Sie nun einige vordefinierte Anwendungen bereitgestellt und aktualisiert haben, können Sie [selbst eine Anwendung in Visual Studio erstellen](service-fabric-create-your-first-application-in-visual-studio.md).
- Alle Aktionen, die auf dem lokalen Cluster in diesem Artikel ausgeführt werden, können auch im [Azure-Cluster](service-fabric-cluster-creation-via-portal.md) ausgeführt werden.
- Das Upgrade, das in diesem Artikel durchgeführt wurde, ist sehr einfach. In der [Dokumentation zu Upgrades](service-fabric-application-upgrade.md) erfahren Sie mehr über die Leistungsfähigkeit und Flexibilität von Service Fabric-Upgrades.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI.png
[deployed-app-ui2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI2.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png

<!---HONumber=Nov15_HO4-->