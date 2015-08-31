<properties
   pageTitle="Aktualisieren der Service Fabric-Entwicklungsumgebung | Microsoft Azure"
   description="Aktualisieren Sie die Service Fabric-Entwicklungsumgebung, um die neuesten Versionen von Laufzeit, SDK und Tools zu verwenden."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2015"
   ms.author="seanmck"/>

# Aktualisieren der Service Fabric-Entwicklungsumgebung

 Für Service Fabric werden regelmäßig neue Versionen für Laufzeit, SDK und Tools bereitgestellt, die bei der lokalen Entwicklung verwendet werden können. Indem Sie Ihre lokale Entwicklungsumgebung mit diesen Versionen auf dem aktuellen Stand halten, stellen Sie sicher, dass Sie immer Zugriff auf die neuesten Funktionen, Fehlerkorrekturen und Leistungsverbesserungen haben, wenn Sie Anwendungen lokal erstellen und testen.

## Bereinigen des lokalen Clusters

 Service Fabric unterstützt derzeit nicht die Aktualisierung der Service Fabric-Laufzeit, während ein lokaler Cluster ausgeführt wird. Um eine fehlerfreie Aktualisierung zu gewährleisten, ist es wichtig, zuerst den lokalen Cluster zu bereinigen.

 >[AZURE.NOTE]Beim Bereinigen des lokalen Clusters werden alle bereitgestellten Anwendungen und die dazugehörigen Daten entfernt.

 Sie können Ihren lokalen Cluster wie folgt bereinigen:


 1. Schließen Sie alle anderen PowerShell-Fenster, und starten Sie ein neues Fenster als Administrator.

 2. Navigieren Sie zum Verzeichnis "ClusterSetup": `cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"`

 3. Führen Sie `.\CleanCluster.ps1` aus.


## Aktualisieren von Laufzeit, SDK und Tools

 Nachdem Sie den vorhandenen Cluster erfolgreich bereinigt haben, können Sie wie folgt mit dem Upgrade fortfahren:


 1. Starten Sie den Webplattform-Installer, um das [Update auf die neue Version durchzuführen][1].

 2. Starten Sie nach Abschluss des Vorgangs ein neues PowerShell-Fenster als Administrator, und navigieren Sie mit `cd "$ENV:ProgramFiles\Microsoft SDKs\ServiceFabric\ClusterSetup"` zum Einrichtungsverzeichnis des Clusters.

 3. Führen Sie `.\DevClusterSetup.ps1` aus, um den lokalen Cluster einzurichten.

Das ist alles! Sie können Visual Studio jetzt starten und mit dem Erstellen von Service Fabric-Anwendungen fortfahren.

>[AZURE.NOTE]Die Standardprojektstruktur wurde in dieser Version geändert. Es sollte möglich sein, vorhandene Projekte in Visual Studio zu öffnen und auszuführen. Falls beim Erstellen, Bereitstellen oder Debuggen von Anwendungen aber Probleme auftreten, sollten Sie die Erstellung eines neuen Projekts und die Migration Ihres Codes erwägen.

 [1]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "WebPI-Link"

<!---HONumber=August15_HO8-->