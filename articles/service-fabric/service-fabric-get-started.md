<properties
   pageTitle="Einrichten der Entwicklungsumgebung | Microsoft Azure"
   description="Installieren Sie Laufzeit, SDK und Tools, und erstellen Sie einen lokalen Entwicklungscluster. Nach Abschluss des Setups können Sie mit der Erstellung von Clientanwendungen beginnen."
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
   ms.date="11/17/2015"
   ms.author="seanmck"/>

# Vorbereiten Ihrer Entwicklungsumgebung
 Zum Entwickeln und Ausführen von [Service Fabric-Anwendungen][1] auf dem Entwicklungscomputer müssen Sie die Laufzeit, das SDK und Tools installieren sowie einen lokalen Cluster einrichten.

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

## Nächste Schritte
Ihre Entwicklungsumgebung ist nun eingerichtet, und Sie können mit der Entwicklung und Ausführung von Apps beginnen.

- [Erstellen Ihrer ersten Service Fabric-Anwendung in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Weitere Informationen zum Bereitstellen und Verwalten von Anwendungen in Ihrem lokalen Cluster](service-fabric-get-started-with-a-local-cluster.md)
- [Weitere Informationen zu Programmiermodellen: Reliable Actors und Reliable Services](service-fabric-choose-framework.md)
- [Service Fabric-Beispiele auf GitHub](https://aka.ms/servicefabricsamples)
- [Visualisieren des Clusters mit Service Fabric-Explorer](service-fabric-visualizing-your-cluster.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric-Kampagnenseite"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "WebPI-Link"

<!---HONumber=AcomDC_1125_2015-->