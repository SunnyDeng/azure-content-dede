<properties
   pageTitle="Kommunikation und Verbindung mit Diensten in Azure Service Fabric | Microsoft Azure"
   description="Hier erfahren Sie, wie Sie eine Verbindung mit Diensten in Service Fabric-Anwendungen herstellen und die Kommunikation mit den Diensten einrichten."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/21/2015"
   ms.author="kunalds"/>


# Kommunizieren mit Diensten
In der Welt der Microservices besteht eine Lösung in der Regel aus vielen verschiedenen Diensten, die jeweils eine bestimmte Aufgabe ausführen. Diese Microservices kommunizieren miteinander, um einen End-to-End-Workflow zu ermöglichen. Darüber hinaus verbinden sich Clientanwendungen zur Kommunikation mit den Diensten. In diesem Dokument erfahren Sie, wie Azure Service Fabric die Einrichtung der Kommunikation mit Diensten erleichtert, die Sie mit Service Fabric schreiben.

## Wichtige Begriffe
Im Folgenden werden einige zentrale Konzepte für die Einrichtung der Kommunikation mit Diensten erläutert.
### Kommunikation nach dem Client-Server-Modell
Die Kommunikations-APIs von Service Fabric bauen auf dem Client-Server-Interaktionstyp auf. Dies gilt auch, wenn die Interaktion zwischen zwei Diensten im gleichen Cluster erfolgt. Als Server fungiert ein Zieldienst, mit dem sich ein Client oder ein anderer Dienst verbindet. Der Zieldienst lauscht auf eingehende Anforderungen. Der Client, bei dem es sich um einen anderen Dienst im Cluster handeln kann, verbindet sich mit dem Server und gibt Aufrufe an den Server aus.
### Verschieben von Diensten
In einem verteilten System können die ausgeführten Dienstinstanzen im Laufe der Zeit auf einen anderen Computer übergehen. Dieser Übergang kann unterschiedliche Gründe haben – beispielsweise einen Lastenausgleich, wenn Lastmetriken für Ressourcenausgleich, Upgrades, Failover und horizontales Skalieren konfiguriert wurden. Dies hat die Änderung der Endpunktadressen einer Dienstinstanz zur Folge. Zum Einrichten der Kommunikation mit der Dienstinstanz muss die folgende Schleife ausgeführt werden. Diese Details sind abstrahiert, wenn Sie die Kommunikations-APIs von Service Fabric verwenden.

* **Auflösen**: Alle Dienstinstanzen in Service Fabric besitzen eindeutige URIs. So bleiben etwa „fabric:/MeineAnwendung/MeinDienst“ und die dazugehörigen URIs immer unverändert. Jede Dienstinstanz macht außerdem Endpunkte verfügbar, die sich bei der Verschiebung von Dienstinstanzen ändern. Dies ist mit Websites vergleichbar, die zwar konstante URLs verwenden, deren IP-Adresse sich jedoch ändern kann. Ähnlich wie das DNS (Domain Name System) im Web, das Website-URLs zu IP-Adressen auflöst, verfügt auch die Service Fabric-Plattform über einen Systemdienst, der URIs zu Endpunkten auflöst. Dieser Schritt umfasst das Auflösen des URI der Dienstinstanz in einen Endpunkt.

* **Verbinden**: Nachdem der Dienst-URI zu einer Endpunktadresse aufgelöst wurde, wird versucht, eine Verbindung mit dem Dienst herzustellen. Dieser Versuch ist unter Umständen nicht erfolgreich, wenn sich die Endpunktadresse aufgrund einer Verschiebung des Diensts (etwa nach einem Computerabsturz oder Lastenausgleich) geändert hat.

* **Wiederholen**: Ist die Verbindungsherstellung nicht erfolgreich, müssen die obigen Schritte zum Auflösen und Verbinden solange wiederholt werden, bis die Verbindung hergestellt werden kann.

## Optionen für Kommunikations-APIs
Service Fabric bietet eine Reihe unterschiedlicher Optionen für Kommunikations-APIs. Welche Option in Ihrem Fall am besten geeignet ist, hängt vom Programmiermodell, dem Kommunikationsframework und der Programmiersprache Ihrer Dienste ab.
### Kommunikation für Reliable Actors
Bei mit der Reliable Actors-API geschriebenen Diensten werden alle Kommunikationsdetails abstrahiert. Die Kommunikation erfolgt in Form von ActorProxy-Methodenaufrufen, sodass Sie ab hier nicht mehr weiterzulesen brauchen.

### Kommunikationsoptionen für Reliable Services
Wenn Ihr Dienst mit der Reliable Services-API geschrieben wurde, können Sie aus einer Reihe von Optionen wählen. Die Wahl der Service Fabric-Kommunikations-APIs hängt vom verwendeten Kommunikationsprotokoll ab.

* **Kein bestimmtes Protokoll:** Wenn Sie kein bestimmtes Kommunikationsframework verwenden und schnell eine unkomplizierte Lösung implementieren möchten, ist der [Standardstapel](service-fabric-reliable-services-communication-remoting.md) ideal, da dieser ein ähnliches Modell bietet wie das Actor-Kommunikationsmodell. Dies ist die einfachste und schnellste Methode für den Einstieg in die Dienstkommunikation. Sie bietet eine stark typisierte RPC-Kommunikation, in der die meisten Kommunikationsdetails abstrahiert sind, sodass das Aufrufen einer Methode für einen Remotedienst im Code in etwa dem Aufrufen einer lokalen Objektinstanz entspricht. Diese Klassen abstrahieren die Auflösung, Verbindung, Wiederholung und Fehlerbehandlung beim Einrichten des Kommunikationskanals und ermöglichen ein Kommunikationsmodell auf Basis von Methodenaufrufen. Hierbei wird die `ServiceCommunicationListener`-Klasse ist für die serverseitige und die `ServiceProxy`-Klasse für die clientseitige Kommunikation verwendet.

* **HTTP**: Profitieren Sie von der Flexibilität HTTP-basierter Kommunikation, indem Sie Service Fabric-Kommunikations-APIs verwenden, mit denen der Kommunikationsmechanismus definiert werden kann, die Logik für Auflösung, Verbindung und Wiederholung aber abstrahiert ist. So können Sie beispielsweise die Web-API zum Angeben des Kommunikationsmechanismus und die [Klassen `ICommunicationClient` und `ServicePartitionClient`](service-fabric-reliable-services-communication.md) zum Einrichten der Kommunikation verwenden.
* **WCF**: Wenn der vorhandene Code das WCF-Kommunikationsframework verwendet, können Sie „WcfCommunicationListener“ für die Serverseite und die Klassen „WcfCommunicationClient“ und „ServicePartitionClient“ für den Client verwenden. Weitere Informationen finden Sie in folgenden Artikel: [WCF-basierter Kommunikationsstapel für Reliable Services](service-fabric-reliable-services-communication-wcf.md).

* **Andere Kommunikationsframeworks (einschließlich benutzerdefinierter Protokolle)**: Wenn Sie ein anderes Kommunikationsframework (einschließlich benutzerdefinierter Kommunikationsprotokolle) verwenden möchten, können Sie Service Fabric-Kommunikations-APIs nutzen, die sich in den Kommunikationsstapel integrieren lassen und die Mechanismen für Auflösung und Verbindung abstrahieren. Weitere Informationen finden Sie im folgenden Artikel: [Das Reliable Service-Kommunikationsmodell](service-fabric-reliable-services-communication.md).

### Kommunikation zwischen Diensten in unterschiedlichen Programmiersprachen
Alle Service Fabric-Kommunikations-APIs sind derzeit nur in C# verfügbar. Falls einer Ihrer Dienste in einer anderen Programmiersprache (etwa in Java oder Node.JS) geschrieben ist, müssen Sie einen eigenen Kommunikationsmechanismus schreiben.

## Nächste Schritte
* [Standardkommunikationsstapel des Reliable Services-Frameworks ](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services-Kommunikationsmodell](service-fabric-reliable-services-communication.md)
* [Erste Schritte mit Web-API-Diensten von Microsoft Azure Service Fabric mit selbstgehostetem OWIN-Server](service-fabric-reliable-services-communication-webapi.md)
* [WCF-basierter Kommunikationsstapel für Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_1223_2015-->