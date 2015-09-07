<properties
   pageTitle="Microsoft Azure Service Fabric – Kommunizieren mit Diensten"
	description="Dieser Artikel beschreibt, wie Sie eine Verbindung mit Diensten in Service Fabric-Anwendungen herstellen und die Kommunikation mit den Diensten einrichten."
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
In der Welt der Microservices besteht eine Lösung in der Regel aus vielen verschiedenen Diensten, die jeweils eine bestimmte Aufgabe ausführen. Diese Microservices kommunizieren miteinander, um einen End-to-End-Workflow zu ermöglichen. Darüber hinaus verbinden sich Clientanwendungen zur Kommunikation mit den Diensten. In diesem Dokument wird erläutert, wie Service Fabric die Einrichtung der Kommunikation mit Diensten erleichtert, die Sie mit Service Fabric schreiben.

## Wichtige Konzepte
Im Folgenden werden einige wichtige Konzepte erläutert, die Sie kennen sollten, wenn Sie die Kommunikation mit Diensten einrichten.
### Kommunikation nach dem Client-Server-Modell
Die Kommunikations-APIs von Service Fabric bauen auf dem Client-Service-Interaktionstyp auf, der auch dann angewendet wird, wenn die Interaktion zwischen zwei Diensten im gleichen Cluster erfolgt. Als Server agiert ein Zieldienst, mit dem sich ein Client oder ein anderer Dienst verbindet. Der Zieldienst überwacht eingehende Anforderungen. Der Client, bei dem es sich um einen anderen Dienst im Cluster handeln kann, verbindet sich mit dem Server und gibt Aufrufe an den Server aus.
### Verschieben von Diensten
In einem verteilten System können die ausgeführten Dienstinstanzen aus verschiedenen Gründen von einem Computer auf einen anderen verschoben werden, z. B. zum Lastenausgleich, wenn Lastmetriken für den Ressourcenausgleich konfiguriert wurden oder aufgrund von Upgrades, Failover, horizontaler Skalierung und anderen Bedingungen. Daraus folgt, dass sich die Endpunktadressen einer Dienstinstanz ändern können. Aus diesem Grund muss bei der Einrichtung der Kommunikation mit der Dienstinstanz die folgende Schleife ausgeführt werden. Diese Details sind abstrahiert, wenn Sie die Kommunikations-APIs von Service Fabric verwenden.

* **Auflösen**: Alle Dienstinstanzen in Service Fabric verfügen über einen eindeutigen URI, z.. B. "fabric:/MeineAnwendung/MeinDienst". Diese URIs ändern sich nicht. Jede Dienstinstanz stellt außerdem Endpunkte bereit, die sich ändern, wenn Dienstinstanzen verschoben werden. Dies ist mit Websites vergleichbar, die zwar konstante URLs verwenden, deren IP-Adresse sich jedoch ändern kann. Ähnlich wie das DNS (Domain Name System) im World Wide Web, das Website-URLs in IP-Adressen auflöst, verfügt auch die Service Fabric-Plattform über einen Systemdienst, der URIs in Endpunkte auflöst. Dieser Schritt umfasst das Auflösen des URI der Dienstinstanz in einen Endpunkt.

* **Verbinden**: Nachdem der Dienst-URI in Endpunktadressen aufgelöst wurde, wird als Nächstes versucht, eine Verbindung mit dem Dienst herzustellen. Die Verbindung schlägt fehlt, wenn sich die Endpunktadresse wegen eines verschobenen Diensts (z. B. nach einem Computerabsturz oder Lastenausgleich) geändert hat.

* **Wiederholen**: Wenn der Verbindungsversuch fehlschlägt, müssen die obigen Schritte zum Auflösen und Verbinden solange wiederholt werden, bis die Verbindung hergestellt werden kann.

## Auswählen der Kommunikations-API
Service Fabric bietet verschiedene Optionen für Kommunikation-APIs. Welche API in Ihrem Fall am besten geeignet ist, hängt vom Programmiermodell, dem Kommunikationsframework und der Programmiersprache Ihrer Dienste ab.
### Kommunikation für Reliable Actors
Wenn Ihre Dienste mit der Reliable Actors-API geschrieben wurden, sind sämtliche Kommunikationsdetails abstrahiert und die Kommunikation erfolgt als Methodenaufrufe an den ActorProxy. In diesem Fall sind die folgenden Informationen für Sie nicht relevant.

### Kommunikationsoptionen für Reliable Services
Wenn Ihr Dienst mit der Reliable Services-API geschrieben wurde, können Sie aus einer Reihe verschiedener Optionen auswählen. Welche Service Fabric-Kommunikations-APIs geeignet sind, hängt davon ab, für welches Kommunikationsprotokoll Sie sich entscheiden.

* **Sie verwenden kein bestimmtes Kommunikationsprotokoll und suchen eine einfache und schnell zu implementierende Lösung**: Wenn Sie kein bestimmtes Kommunikationsframework verwenden, ist der [Standardstapel](service-fabric-reliable-services-communication-default.md) ideal, da dieser ein ähnliches Modell wie das Actor-Kommunikationsmodell ermöglicht. Dies ist die einfachste und schnellste Methode für den Einstieg in die Dienstkommunikation. Die Methode bietet eine stark typisierte RPC-Kommunikation, in der die meisten Kommunikationsdetails abstrahiert sind, sodass das Aufrufen einer Methode für einen Remotedienst im Code in etwa dem Aufrufen einer lokalen Objektinstanz entspricht. Diese Klassen abstrahieren die Auflösung, Verbindung, Wiederholung und Fehlerbehandlung beim Einrichten des Kommunikationskanals und ermöglichen ein Kommunikationsmodell auf Basis von Methodenaufrufen. Hierbei wird die `ServiceCommunicationListener`-Klasse ist für die serverseitige und die `ServiceProxy`-Klasse für die clientseitige Kommunikation verwendet.

* **HTTP**: Die Flexibilität der HTTP-basierten Kommunikation können Sie nutzen, indem Sie Service Fabric-Kommunikations-APIs verwenden, mit denen der Kommunikationsmechanismus definiert werden kann, die Logik für Auflösung, Verbindung und Wiederholung jedoch abstrahiert ist. Sie können beispielsweise die Web-API zum Angeben des Kommunikationsmechanismus und die [Klassen `ICommunicationClient` und `ServicePartitionClient`](service-fabric-reliable-services-communication.md) zum Einrichten der Kommunikation verwenden.
* **WCF**: Wenn der vorhandene Code das WCF-Kommunikationsframework verwendet, können Sie WcfCommunicationListener für die Serverseite und die WcfCommunicationClient- und ServicePartitionClient-Klassen für den Client verwenden. Weitere Informationen finden Sie in [diesem Artikel](service-fabric-reliable-services-communication-wcf.md).

* **Andere Kommunikationsframeworks einschließlich benutzerdefinierter Protokolle**: Wenn Sie ein anderes Kommunikationsframework einschließlich benutzerdefinierter Kommunikationsprotokolle verwenden möchten, können Sie Service Fabric-Kommunikations-APIs nutzen, die sich in den Kommunikationsstapel einhängen lassen und die Mechanismen für Auflösung und Verbindung abstrahieren. Weitere Informationen finden Sie [in diesem Artikel](service-fabric-reliable-services-communication.md).

### Kommunikation zwischen Diensten in unterschiedlichen Programmiersprachen
Alle Service Fabric-Kommunikations-APIs sind derzeit nur in C# verfügbar. Wenn einer Ihrer Dienste in einer anderen Programmiersprache geschrieben ist, z. B. in Java oder Node.JS, müssen Sie einen eigenen Kommunikationsmechanismus schreiben.

## Nächste Schritte
* [Standardkommunikationsstapel des Reliable Services-Frameworks ](service-fabric-reliable-services-communication-default.md)
* [Reliable Services-Kommunikationsmodell](service-fabric-reliable-services-communication.md)
* [Erste Schritte mit Web-API-Diensten von Microsoft Azure Service Fabric mit selbstgehostetem OWIN-Server](service-fabric-reliable-services-communication-webapi.md)
* [WCF-basierter Kommunikationsstapel für Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=August15_HO9-->