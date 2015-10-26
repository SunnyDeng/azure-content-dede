<properties
   pageTitle="Einrichten der Service Fabric-Entwicklungsumgebung | Microsoft Azure"
   description="Installieren Sie Laufzeit, SDK und Tools für Service Fabric und erstellen Sie einen lokalen Entwicklungscluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/07/2015"
   ms.author="seanmck"/>

# Einrichten der Service Fabric-Entwicklungsumgebung
 Dieser Artikel befasst sich mit allem, was Sie zum Erstellen von [Service Fabric][1]-Apps benötigen, einschließlich Installation von Laufzeit, SDK, Tools sowie Einrichtung eines lokalen Clusters.

 >[AZURE.NOTE]Diese Anweisungen beziehen sich auf die Einrichtung neuer PCs. Wenn Sie eine frühere Version von Service Fabric auf Ihrem PC installiert haben, folgen Sie den [Anweisungen zum Aktualisieren der Entwicklungsumgebung](service-fabric-update-your-development-environment.md).

## Voraussetzungen
### Unterstützte Betriebssystemversionen
Die folgenden Betriebssystemversionen werden unterstützt:

- Windows 8/8.1
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

Die Tools für Service Fabric hängen von Visual Studio 2015 ab, das Sie [hier][2] finden.

> [AZURE.NOTE]Wenn Sie keine der unterstützten Betriebssystemversionen verwenden oder Visual Studio 2015 nicht auf Ihrem PC installieren möchten, können Sie mit Windows Server 2012 R2 und der vorinstallierten Version von Visual Studio 2015 einen virtuellen Azure-Computer einrichten, indem Sie ein Image aus dem VM-Katalog verwenden.

## Installieren von Laufzeit, SDK und Tools

Die Installation der Service Fabric-Komponenten erfolgt mit dem Webplattform-Installer. Folgen Sie diesen Anweisungen, um die Komponenten zu installieren:

1. Mit dem Webplattform-Installer können Sie [das SDK herunterladen][3].

2. Klicken Sie auf **Installieren**, um den Installationsvorgang zu starten.

3. Lesen und akzeptieren Sie den Endbenutzer-Lizenzvertrag.

Die Installation wird automatisch fortgesetzt.

## Aktivieren der PowerShell-Skriptausführung

Service Fabric verwendet Windows PowerShell-Skripts zum Erstellen eines lokalen Entwicklungsclusters und zum Bereitstellen von Anwendungen aus Visual Studio. Die Ausführung dieser Skripts wird von Windows standardmäßig blockiert. Um die Skripts zu aktivieren, müssen Sie die PowerShell-Ausführungsrichtlinie ändern. Öffnen Sie PowerShell als Administrator, und geben Sie folgenden Befehl ein:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Installieren und Starten eines lokalen Clusters
Ein lokaler Cluster stellt die Topologie mit mehreren Computern dar, die Sie letztendlich in der Produktionsumgebung auf einem einzelnen Entwicklungscomputer verwenden. Um den lokalen Cluster einzurichten, gehen Sie folgendermaßen vor:


1. Schließen Sie alle anderen PowerShell-Fenster, und starten Sie ein neues Fenster als Administrator.

2. Navigieren zum Verzeichnis für das Clustersetup

    ```powershell
    cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"
    ```

3. Ausführen

    ```powershell
    .\DevClusterSetup.ps1
    ```

Nach wenigen Augenblicken sehen Sie eine Ausgabe mit Knoteninformationen und der Bestätigung, dass der Cluster erfolgreich erstellt wurde. In manchen Fällen werden beim Starten des Service Fabric-Hostdiensts und der Naming-Dienste Warnungen angezeigt. Dies ist normal; es werden außerdem Diese sind normal und werden gefolgt von kurz angezeigten grundlegenden Informationen zum Cluster.

> [AZURE.NOTE]Der lokale Cluster verwendet genau dieselbe Laufzeit, die später in Azure ausgeführt wird. Die Laufzeit ist weder simuliert noch emuliert. Der einzige Unterschied besteht darin, dass alle Knoten auf einem einzelnen Computer ausgeführt werden, anstatt auf mehreren Computern verteilt zu sein, wie es in Azure der Fall sein wird.

## Überprüfen der Clustereinrichtung

Mit dem Explorer-Tool von Service Fabric, das im SDK enthalten ist, können Sie überprüfen, ob der Cluster erfolgreich erstellt wurde.

1. Starten Sie den Service Fabric-Explorer durch Ausführen von

    ```powershell
    . "$env:ProgramW6432\Microsoft SDKs\Service Fabric\Tools\ServiceFabricExplorer\ServiceFabricExplorer.exe"
    ```

2. Erweitern Sie oben links den Knoten "Onebox/Local Cluster".

3. Stellen Sie sicher, dass die Anwendungs- und Knotenansichten grün sind.

Wenn ein Element nicht grün ist oder ein Fehler angezeigt wird, warten Sie einige Zeit, und klicken Sie auf die Schaltfläche "Aktualisieren". Wenn das Problem weiterhin besteht, lesen Sie die [Anleitung zur Problembehandlung bei der Einrichtung](service-fabric-troubleshoot-local-cluster-setup.md).

## Nächste Schritte
Ihre Entwicklungsumgebung ist nun eingerichtet, und Sie können mit der Entwicklung und Ausführung von Apps beginnen.

- [Weitere Informationen zu Programmiermodellen: Reliable Actors und Reliable Services](service-fabric-choose-framework.md)
- [Erste Schritte mit der Reliable Services-API](service-fabric-reliable-services-quick-start.md)
- [Erste Schritte mit der Reliable Actors-API](service-fabric-reliable-actors-get-started.md)
- [Service Fabric-Beispiele auf GitHub](https://github.com/azure/servicefabric-samples)
- [Visualisieren des Clusters mit Service Fabric-Explorer](service-fabric-visualizing-your-cluster.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric-Kampagnenseite"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "WebPI-Link"

<!---HONumber=Oct15_HO3-->