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
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/20/2015"
   ms.author="seanmck"/>

# Erste Schritte beim Bereitstellen und Aktualisieren von Anwendungen im lokalen Cluster
Das Azure Service Fabric-SDK umfasst eine vollständige lokale Entwicklungsumgebung, die Sie verwenden können, um schnell mit der Bereitstellung und Verwaltung von Anwendungen in einem lokalen Cluster zu beginnen. In diesem Artikel verwenden Sie Windows PowerShell, um einen lokalen Cluster zu erstellen, eine vorhandene Anwendung bereitzustellen und die Anwendung auf eine neue Version zu aktualisieren.

> [AZURE.NOTE] In diesem Artikel wird vorausgesetzt, dass Sie bereits [eine Entwicklungsumgebung eingerichtet haben](service-fabric-get-started.md).

## Erstellen eines lokalen Clusters
Ein Service Fabric-Cluster ist ein Satz von Hardwareressourcen, auf dem Sie Anwendungen bereitstellen können. Normalerweise umfasst ein Cluster zwischen fünf und mehreren Tausend Computern. Das Service Fabric-SDK enthält aber eine Clusterkonfiguration, die auf einem einzelnen Computer ausgeführt werden kann.

Sie müssen dabei bedenken, dass der lokale Service Fabric-Cluster kein Emulator oder Simulator ist. Er führt den gleichen Plattformcode wie Cluster mit mehreren Computern aus. Der einzige Unterschied ist, dass die Plattformprozesse, die normalerweise auf fünf Computer verteilt sind, auf einem Computer ausgeführt werden.

Das SDK bietet zwei Möglichkeiten zum Einrichten eines lokalen Clusters: ein Windows PowerShell-Skript und die Taskleisten-App Local Cluster Manager. In diesem Tutorial wird das PowerShell-Skript verwendet.

> [AZURE.NOTE] Wenn Sie bereits einen lokalen Cluster durch die Bereitstellung einer Anwendung über Visual Studio erstellt haben, können Sie diesen Abschnitt überspringen.


1. Starten Sie als Administrator ein neues PowerShell-Fenster.

2. Führen Sie das Skript für die Clustereinrichtung aus dem SDK-Ordner aus:

	```powershell
	& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
	```

    Das Einrichten des Clusters dauert einige Augenblicke. Nach Abschluss des Einrichtungsvorgangs wird normalerweise folgende Ausgabe angezeigt:

    ![Ausgabe bei der Clustereinrichtung][cluster-setup-success]

    Sie können nun damit beginnen, eine Anwendung in Ihrem Cluster bereitzustellen.

## Bereitstellen von Anwendungen
Das Service Fabric-SDK umfasst eine Vielzahl von Frameworks und Entwicklertools zum Erstellen von Anwendungen. Wenn Sie lernen möchten, wie Sie Anwendungen in Visual Studio erstellen können, lesen Sie [Erstellen Ihrer ersten Service Fabric-Anwendung in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

In diesem Tutorial verwenden wir eine vorhandene Beispielanwendung (mit dem Namen WordCount), damit wir uns auf die Verwaltung der Plattform konzentrieren können, einschließlich Bereitstellung, Überwachung und Upgrade.


1. Starten Sie als Administrator ein neues PowerShell-Fenster.

2. Importieren Sie das Service Fabric-SDK für das PowerShell-Modul.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. Erstellen Sie ein Verzeichnis zum Speichern der Anwendung, die Sie herunterladen und bereitstellen möchten, z. B. „C:\\ServiceFabric“.

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. [Laden Sie die WordCount-Anwendung](http://aka.ms/servicefabric-wordcountapp) an den Speicherort herunter, den Sie erstellt haben.

5. Stellen Sie eine Verbindung mit dem lokalen Cluster her:

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Rufen Sie den Bereitstellungsbefehl des SDK zum Erstellen einer neuen Anwendung auf, indem Sie einen Namen und einen Pfad zum Anwendungspaket angeben.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Wenn alles funktioniert, sollte folgende Ausgabe angezeigt werden:

    ![Bereitstellen einer Anwendung im lokalen Cluster][deploy-app-to-local-cluster]

7. Um die Anwendung in Aktion zu sehen, starten Sie den Browser, und navigieren Sie zu [http://localhost:8081/wordcount/index](http://localhost:8081/wordcount/index). Die Ausgabe sollte folgendermaßen aussehen:

    ![Benutzeroberfläche der bereitgestellten Anwendung][deployed-app-ui]

    Die WordCount-Anwendung ist sehr einfach. Sie enthält clientseitigen JavaScript-Code zum Generieren von zufälligen „Wörtern“ mit fünf Zeichen, die dann per ASP.NET-Web-API an die Anwendung weitergeleitet werden. Bei einem zustandsbehafteten Dienst wird die Anzahl von Wörtern verfolgt. Sie werden basierend auf dem ersten Buchstaben des Worts partitioniert.

    Die Anwendung, die wir bereitgestellt haben, enthält vier Partitionen. Wörter, die mit A bis G beginnen, werden in der ersten Partition gespeichert, Wörter mit H bis N in der zweiten Partition usw.

## Anzeigen von Anwendungsdetails und des Anwendungsstatus
Nach dem Bereitstellen der Anwendung sehen wir uns nun die App-Details in PowerShell an.

1. Fragen Sie alle bereitgestellten Anwendungen im Cluster ab:

    ```powershell
    Get-ServiceFabricApplication
    ```

    Vorausgesetzt, dass Sie nur die WordCount-App bereitgestellt haben, sehen Sie etwa Folgendes:

    ![Abfrage aller bereitgestellten Anwendungen in PowerShell][ps-getsfapp]

2. Machen Sie den nächsten Schritt, indem Sie die Dienste abfragen, die in der WordCount-Anwendung enthalten sind.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Auflisten von Diensten für die Anwendung in PowerShell][ps-getsfsvc]

    Beachten Sie, dass die Anwendung aus zwei Diensten besteht: dem Web-Front-End- und dem zustandsbehafteten Dienst, der die Wörter verwaltet.

3. Sehen Sie sich schließlich die Liste der Partitionen für WordCountService an:

    ![Anzeigen der Dienstpartitionen in PowerShell][ps-getsfpartitions]

    Die Befehle, die Sie gerade verwendet haben, sind wie alle Service Fabric-PowerShell-Befehle für alle Cluster verfügbar, mit denen Sie eine Verbindung herstellen – lokal oder remote.

    Für eine eher visuelle Möglichkeit der Interaktion mit dem Cluster können Sie das webbasierte Tool Service Fabric-Explorer verwenden, indem Sie im Browser zu [http://localhost:19080/Explorer](http://localhost:19080/Explorer) navigieren.

    ![Anzeigen von Anwendungsdetails im Service Fabric-Explorer][sfx-service-overview]

    > [AZURE.NOTE] Weitere Informationen zum Service Fabric-Explorer finden Sie unter [Visualisieren des Clusters mit dem Service Fabric-Explorer](service-fabric-visualizing-your-cluster.md).

## Upgraden einer Anwendung
Service Fabric ermöglicht Upgrades ohne Ausfallzeit, indem die Integrität der Anwendung überwacht wird, während sie im Cluster bereitgestellt wird. Führen Sie nun ein einfaches Upgrade der WordCount-Anwendung durch.

Die neue Version der Anwendung zählt nur die Wörter, die mit einem Vokal beginnen. Während das Upgrade bereitgestellt wird, sehen wir zwei Änderungen im Verhalten der Anwendung. Erstens sollte sich die Rate verlangsamen, mit der die Anzahl zunimmt, da weniger Wörter gezählt werden. Da die erste Partition zwei Vokale enthält („A“ und „E“) und alle anderen Partitionen nur einen enthalten, sollte zweitens die Anzahl dieser Partition letztlich größer als die der anderen sein.

1. [Laden Sie das Paket für WordCount Version 2](http://aka.ms/servicefabric-wordcountappv2) an denselben Speicherort herunter, an den Sie das Paket für Version 1 heruntergeladen haben.

2. Kehren Sie zum PowerShell-Fenster zurück, und verwenden Sie den Upgradebefehl des SDK, um die neue Version des Clusters zu registrieren. Beginnen Sie dann mit dem Upgrade der Anwendung „fabric:/WordCount“.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Die Ausgabe in PowerShell sollte etwa wie folgt aussehen, wenn das Upgrade beginnt.

    ![Upgradestatus in PowerShell][ps-appupgradeprogress]

3. Möglicherweise ist es für Sie während des Upgrades einfacher, dessen Status über den Service Fabric-Explorer zu überwachen. Öffnen Sie ein Browserfenster, und navigieren Sie zu [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Klicken Sie in der Struktur auf der linken Seite auf **Anwendungen**, und wählen Sie dann **Upgrades in Bearbeitung** aus.

    ![Upgradestatus im Service Fabric-Explorer][sfx-upgradeprogress]

    Beachten Sie, dass die Anzeige des Upgradefortschritts den Status des Upgrades innerhalb der Upgradedomänen Ihres Clusters darstellt. Während das Upgrade in den einzelnen Domänen durchgeführt wird, werden Integritätsprüfungen ausgeführt, um sicherzustellen, dass sich die Anwendung richtig verhält.

4. Wenn Sie die vorherige Abfrage der Dienste, die in der Anwendung „fabric:/WordCount“ enthalten sind, erneut ausführen, werden Sie Folgendes feststellen: Die Version von WordCountService hat sich geändert, die Version von WordCountWebService aber nicht:

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Abfragen der Anwendungsdienste nach dem Upgrade][ps-getsfsvc-postupgrade]

    Dies verdeutlicht, wie Anwendungsupgrades von Service Fabric verwaltet werden. Es ist nur die Gruppe der Dienste (bzw. der Code-/Konfigurationspakete in diesen Diensten) betroffen, die sich geändert haben, und der Upgradevorgang wird schneller und zuverlässiger.

5. Kehren Sie schließlich zum Browser zurück, um das Verhalten der neuen Anwendungsversion zu beobachten. Die Anzahl steigt wie erwartet langsamer an, und die erste Partition enthält eine etwas größere Zahl.

    ![Anzeigen der neuen Version der Anwendung im Browser][deployed-app-ui-v2]

## Nächste Schritte
- Nachdem Sie nun einige vordefinierte Anwendungen bereitgestellt und aktualisiert haben, können Sie [selbst eine Anwendung in Visual Studio erstellen](service-fabric-create-your-first-application-in-visual-studio.md).
- Alle Aktionen, die auf dem lokalen Cluster in diesem Artikel ausgeführt werden, können auch in einem [Azure-Cluster](service-fabric-cluster-creation-via-portal.md) ausgeführt werden.
- Das Upgrade, das wir in diesem Artikel durchgeführt haben, ist sehr einfach. In der [Dokumentation zu Upgrades](service-fabric-application-upgrade.md) erfahren Sie mehr über die Leistungsfähigkeit und Flexibilität von Service Fabric-Upgrades.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png

<!---HONumber=AcomDC_0218_2016-->