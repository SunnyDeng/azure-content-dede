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
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/26/2016"
   ms.author="seanmck"/>

# Vorbereiten Ihrer Entwicklungsumgebung
 Zum Entwickeln und Ausführen von [Azure Service Fabric-Anwendungen][1] auf dem Entwicklungscomputer müssen Sie die Laufzeit, das SDK und Tools installieren. Sie müssen auch die Ausführung der im SDK enthaltenen Windows PowerShell-Skripts aktivieren.

## Voraussetzungen
### Unterstützte Betriebssystemversionen
Die folgenden Betriebssystemversionen werden bei der Entwicklung unterstützt:

- Windows 7
- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

Für die Tools für Service Fabric ist mindestens Visual Studio 2015 erforderlich. Diese Version finden Sie auf der [Visual Studio-Website][2].

> [AZURE.NOTE] Wenn Sie keine der unterstützten Betriebssystemversionen verwenden oder Visual Studio nicht auf Ihrem Computer installieren möchten, können Sie einen virtuellen Azure-Computer mit Windows Server 2012 R2 und der vorinstallierten Version von Visual Studio einrichten. Dazu können Sie ein Image aus dem Azure-Katalog für virtuelle Maschinen verwenden.

## Installieren von Laufzeit, SDK und Tools

Der Webplattform-Installer führt die Installation der Service Fabric-Komponenten aus. Es gibt drei Optionen für die Installation:

- [Installieren der Service Fabric-Laufzeit, des SDKs und der Tools für Visual Studio 2015](full-bundle-vs2015)
- [Installieren der Service Fabric-Laufzeit, des SDKs und der Tools für Visual Studio „15“ Preview](full-bundle-dev15)
- [Installieren der Service Fabric-Laufzeit und des SDKs (keine Visual Studio-Tools)](core-sdk)


## Aktivieren der PowerShell-Skriptausführung

Service Fabric verwendet Windows PowerShell-Skripts zum Erstellen eines lokalen Entwicklungsclusters und zum Bereitstellen von Anwendungen aus Visual Studio. Die Ausführung dieser Skripts wird von Windows standardmäßig blockiert. Um die Skripts zu aktivieren, müssen Sie die PowerShell-Ausführungsrichtlinie ändern. Öffnen Sie PowerShell als Administrator, und geben Sie folgenden Befehl ein:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Nächste Schritte
Ihre Entwicklungsumgebung ist nun eingerichtet, und Sie können mit der Entwicklung und Ausführung von Apps beginnen.

- [Erstellen Ihrer ersten Service Fabric-Anwendung in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Weitere Informationen zum Bereitstellen und Verwalten von Anwendungen in Ihrem lokalen Cluster](service-fabric-get-started-with-a-local-cluster.md)
- [Weitere Informationen zu Programmiermodellen: Reliable Services und Reliable Actors](service-fabric-choose-framework.md)
- [Service Fabric-Codebeispiele auf GitHub](https://aka.ms/servicefabricsamples)
- [Visualisieren des Clusters mit Service Fabric-Explorer](service-fabric-visualizing-your-cluster.md)
- [Folgen Sie dem Service Fabric-Lernpfad, um eine umfassende Einführung in die Plattform zu erhalten.](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric-Kampagnenseite"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "WebPI-Link für VS 2015"
[full-bundle-dev15]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "WebPI-Link für Dev15"
[core-sdk]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=ServiceFabricSDK "WebPI-Link für Core SDK"

<!---HONumber=AcomDC_0330_2016-->