<properties
   pageTitle="Übersicht über das Reliable Services-Programmiermodell von Service Fabric"
   description="Erfahren Sie mehr über das Reliable Services-Programmmodell von Service Fabric, und schreiben Sie eigene Dienste."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="jessebenson; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="masnider;jesseb"/>

# Übersicht über Reliable Services
Service Fabric vereinfacht das Schreiben und Verwalten zuverlässiger zustandsloser und zustandsbehafteter Dienste (Reliable Services). In diesem Dokument wird Folgendes behandelt:

1. Das Reliable Service-Programmiermodell für statusfreie und statusbehaftete Dienste
2. Die unterschiedlichen Optionen, die beim Schreiben eines Reliable Service zur Auswahl stehen
3. Szenarien und Beispiele für die Verwendung von Reliable Services und wie sie geschrieben werden

Reliable Services zählen zu den in Service Fabric verfügbaren Programmiermodellen. Weitere Informationen zum Reliable Actors-Programmiermodell finden Sie in der [Einführung](service-fabric-reliable-actors-introduction.md).

Dienste in Service Fabric setzen sich aus der Konfiguration, dem Anwendungscode und optional einem Zustand zusammen.

Service Fabric verwaltet die Lebensdauer von Diensten von der Bereitstellung und Implementierung bis hin zur Aktualisierung und zum Löschen über die [Anwendungsverwaltung von Service Fabric](service-fabric-deploy-remove-applications.md).

## Was sind Reliable Services?
Reliable Services bieten ein einfaches, leistungsfähiges und erstklassiges Programmiermodell, mit dem Sie alle wichtigen Funktionen in Ihrer Anwendung programmieren können. Das Reliable Services-Programmiermodell bietet Folgendes:

1. Bei zustandsbehafteten Diensten können Sie mit dem Reliable Service-Programmiermodell den Zustand mithilfe von zuverlässigen Auflistungen (Reliable Collections) konsistent und zuverlässig direkt im Dienst speichern. Die zuverlässigen Auflistungen bieten einen einfachen Satz von hoch verfügbaren Auflistungsklassen. Wenn Sie bereits mit C#-Auflistungen gearbeitet haben, sind Ihnen diese vertraut. Dienste benötigten bisher für die zuverlässige Zustandsverwaltung externe Systemen. Mit zuverlässigen Auflistungen können Sie den Status zusammen mit dem Server speichern und dabei von der gleichen hohen Verfügbarkeit und Zuverlässigkeit, die Sie von hochverfügbaren externen Speichern gewohnt sind, und von zusätzlichen Latenzverbesserungen profitieren, die Server und Status-Anbieter zusammenbringen.

2. Ein einfaches Modell für die Ausführung von eigenem Code, das Ihren gewohnten Programmiermodellen ähnelt: Der Code enthält einen klar definierten Einstiegspunkt und einen leicht zu verwaltenden Lebenszyklus.

3. Ein austauschbares Kommunikationsmodell – wählen Sie den gewünschten Übertragungskanal, z. B. HTTP mit [Web-API](service-fabric-reliable-services-communication-webapi.md), WebSockets, benutzerdefinierte TCP-Protokolle usw. Reliable Services bieten einige nützliche vorkonfigurierte Optionen. Sie können aber auch eigene Optionen verwenden.

## Was unterscheidet Reliable Services von anderen Diensten?
Reliable Services sind nicht wie andere Dienste, die Sie möglicherweise bereits geschrieben haben. Service Fabric bietet Zuverlässigkeit, Verfügbarkeit, Konsistenz und Skalierbarkeit.

+ <u>Zuverlässigkeit</u>: Der Dienst wird auch dann fortgesetzt, wenn in der Umgebung Probleme auftreten, wie etwa Computerausfälle oder Netzwerkprobleme.

+ <u>Verfügbarkeit</u>: Der Dienst ist erreichbar und reaktionsfähig (was nicht heißt, dass Ihre Dienste nicht von extern nicht auffindbar oder erreichbar sein können).

+ <u>Skalierbarkeit</u>: Die Dienste sind von bestimmter Hardware entkoppelt und können durch das Hinzufügen oder Entfernen von Hardware oder virtuellen Ressourcen nach Bedarf vergrößert oder verkleinert werden. Die Dienste lassen sich einfach partitionieren (insbesondere, wenn sie statusbehaftet sind), um sicherzustellen, dass Teile des Diensts unabhängig voneinander skaliert werden und auf Fehler reagieren können. Schließlich fördert Service Fabric das Erstellen einfacher Dienste, indem in einem einzigen Prozess Tausende von Diensten bereitgestellt werden können, anstatt ganze Betriebssysteminstanzen zu erzwingen oder für eine einzige Instanz einer bestimmten Arbeitsauslastung zu reservieren.

+ <u>Konsistenz</u>: Bei allen in diesem Dienst gespeicherten Informationen kann die Konsistenz garantiert werden (gilt nur für zustandsbehaftete Dienste – mehr dazu später).

## Dienstlebenszyklus
Ungeachtet dessen, ob der Dienst zustandsbehaftet oder zustandslos ist, bieten Reliable Services einen einfachen Lebenszyklus, mit dem Sie den Code schnell als Plug-in bereitstellen und beginnen können. Sie brauchen zum Ausführen des Diensts tatsächlich nur eine oder zwei Methoden zu implementieren.

+ CreateServiceReplicaListeners/CreateServiceInstanceListeners: Hier werden vom Dienst die zu verwendenden Kommunikationsstapel definiert. Der Kommunikationsstapel (z. B. [Web-API](service-fabric-reliable-services-communication-webapi.md)) definiert die überwachenden Endpunkte des Diensts (wie er von Clients erreicht wird) sowie die Interaktion der angezeigten Nachrichten mit dem übrigen Dienstcode.

+ RunAsync – Hiermit führt der Dienst seine gesamte Geschäftslogik aus. Das bereitgestellte Abbruchtoken dient als Signal, wenn die Arbeit beendet werden soll. Wenn Sie beispielsweise einen Dienst haben, der permanent Nachrichten aus einer ReliableQueue abrufen und verarbeiten muss, wird dieser Vorgang hiermit ausgeführt.

Dies sind die wichtigsten Ereignisse im Lebenszyklus eines Reliable Service:

1. Das Serviceobjekt (von "StatelessService" oder "StatefulService" abgeleitet) wird erstellt.

2. Die Methode „CreateServiceReplicaListeners/CreateServiceInstanceListeners“ wird aufgerufen. Der Dienst erhält dadurch die Möglichkeit, die gewünschten Kommunikationslistener zurückzugeben.
  + Dies ist optional, obwohl die meisten Dienste einige Endpunkte direkt verfügbar machen.

3. Nach dem Erstellen der Kommunikationslistener wird der Dienst geöffnet.
  + Kommunikationslistener verfügen über eine Methode namens „OpenAsync()“, die zu diesem Zeitpunkt aufgerufen wird und die Überwachungsadresse für den Dienst zurückgibt. Wenn Ihr Reliable Service einen der integrierten ICommunicationListener verwendet, erfolgt dies automatisch.

4. Sobald der Kommunikationslistener geöffnet wurde, wird der Aufruf „RunAsync()“ für den Hauptdienst ausgelöst.
  + RunAsync ist optional. Wenn der Dienst alle Vorgänge nur direkt infolge von Benutzeraufrufen ausführt, muss "RunAsync()" nicht implementiert werden.

Wenn der Dienst heruntergefahren wird (weil er gelöscht, aktualisiert oder einfach von einem bestimmten Speicherort verschoben wurde), bleibt die Aufrufreihenfolge gleich: Zunächst wird in den Kommunikationslistenern „CloseAsync()“ aufgerufen und dann das an „RunAsync()“ übergebene Abbruchtoken abgebrochen.

## Dienstbeispiele
Nachdem Sie diese Programmiermodell nun kennen, werfen wir einen Blick auf zwei Dienste, um zu sehen, wie diese Elemente zusammenspielen.

### Zustandslose Reliable Services
Ein zustandsloser Dienst hat buchstäblich keinen Zustand, oder der vorhandene Zustand wird vollständig verworfen und erfordert keinerlei Synchronisierung, Replikation, Persistenz oder hohe Verfügbarkeit.

Denken Sie beispielsweise an einen Rechner, der keinen Speicher hat und alle Zahlen und durchzuführenden Operationen gleichzeitig erhält.

In diesem Fall kann der Dienst "RunAsync()" leer sein, da er keine Aufgaben im Hintergrund auszuführen hat. Wenn der Rechnerdienst erstellt wird, gibt er einen Listener vom Typ „ICommunicationListener“ (z. B. [Web-API](service-fabric-reliable-services-communication-webapi.md)) zurück, der auf einem Port einen Überwachungsendpunkt öffnet. Dieser Überwachungsendpunkt wird mit den verschiedenen Methoden verknüpft (z. B. "Add (n1, n2)"), die die öffentliche API des Rechners definieren.

Erfolgt ein Aufruf durch einen Client, wird die entsprechende Methode ausgelöst. Der Rechnerdienst führt an den bereitgestellten Daten die erforderlichen Operationen durch und gibt das Ergebnis zurück. Es wird kein Zustand gespeichert.

Die Tatsache, dass kein interner Zustand gespeichert wird, macht den Beispielrechner äußerst einfach. Die meisten Dienste sind jedoch nicht wirklich zustandslos, sondern lagern ihren Zustand in einen anderen Speicher aus (z. B. sind Web-Apps, für die in einem Sicherungsspeicher oder -cache ein Sitzungsstatus hinterlegt sein muss, nicht vollständig zustandslos).

Zustandslose Dienste werden in Service Fabric beispielsweise häufig als Front-End verwendet, das die öffentliche API für eine Webanwendung verfügbar macht. Der Front-End-Dienst kommuniziert in diesem Fall mit zustandsbehafteten Diensten, um eine Benutzeranforderung zu verarbeiten. Aufrufe von Clients werden dabei an einen bekannten Port wie Port 80 geleitet, der von dem zustandslosen Dienst überwacht wird. Dieser zustandslose Dienst erhält den Aufruf und ermittelt, ob dieser von einem vertrauenswürdigen Teilnehmer stammt und für welchen Dienst er bestimmt ist. Der zustandslose Dienst leitet den Aufruf anschließend an die richtige Partition des zustandsbehafteten Diensts weiter und wartet auf eine Antwort. Sobald er eine Antwort erhält, sendet er eine Antwort an den ursprünglichen Client zurück.

### Zustandsbehaftete Reliable Services
Bei einem zustandsbehafteten Dienst muss ein gewisser Teil des Zustands konsistent und präsent sein, damit der Dienst funktioniert. Nehmen wir einen Dienst, der kontinuierlich einen gleitenden Durchschnitt einiger Werte berechnet, die regelmäßig aktualisiert werden. Er benötigt zu diesem Zweck sowohl den aktuellen Satz eingehender, zu verarbeitender Anforderungen als auch den aktuellen Durchschnittswert. Jeder Dienst, der Informationen in einem externen Speicher abruft, verarbeitet und speichert (z. B. in einem modernen Azure-Blob- oder Tabellenspeicher), ist zustandsbehaftet – er behält seinen Zustand einfach im externen Zustandsspeicher bei.

Die meisten Dienste speichern heute ihren Zustand extern, da der externe Speicher die für den Zustand erforderliche Zuverlässigkeit, Verfügbarkeit, Skalierbarkeit und Konsistenz bietet. In Service Fabric müssen zustandsbehaftete Dienste ihren Zustand nicht extern speichern, da Service Fabric diese Anforderungen für den Code und den Zustand des Diensts übernimmt.

Angenommen, Sie möchten einen Dienst schreiben, der Anforderungen für eine Reihe von Konvertierungen an einem Bild sowie das zu konvertierende Bild erhalten hat. Service Fabric würde für diesen Dienst einen Kommunikationslistener (z. B. WebAPI) zurückgeben, der einen Kommunikationsport für Übermittlungen über eine API wie `ConvertImage(Image i, IList<Conversion> conversions)` öffnet. Der Dienst könnte in dieser API die Informationen empfangen und die Anforderung in einer ReliableQueue speichern. Anschließend würde er zur Nachverfolgung der Anforderung Token an den Client zurückgeben (da die Verarbeitung der Anforderungen eine Weile dauern kann).

RunAsync kann in diesem Dienst komplexer sein: Der Dienst hätte in RunAsync eine Schleife, die Anforderungen aus „IReliableQueue“ abruft, die aufgelisteten Konvertierungen durchführt und die Ergebnisse in „IReliableDictionary“ speichert. Der Client kann daraufhin konvertierte Bilder abrufen. Um sicherzustellen, dass das Bild auch bei einem Fehler nicht verloren geht, ruft der Reliable Service es aus der Warteschlange ab, führt die Konvertierungen durch und speichert das Ergebnis in einer Transaktion. Auf diese Weise wird die Meldung tatsächlich nur aus der Warteschlange entfernt. Die Ergebnisse werden nach Abschluss der Konvertierung im Ergebniswörterbuch gespeichert. Wenn während der Verarbeitung ein Fehler auftritt (z. B. wenn der Computer, auf dem diese Instanz des Codes ausgeführt wird, ausfällt), bleibt die Anforderung zur erneuten Verarbeitung in der Warteschlange.

Bei diesem Dienst ist zu beachten, dass er wie ein normaler .NET-Dienst erscheint – der einzige Unterschied besteht darin, dass die verwendeten Datenstrukturen („IReliableQueue“ und „IReliableDictionary“) von Service Fabric bereitgestellt werden und daher äußerst zuverlässig, verfügbar und konsistent sind.

## Anwendungsbereiche für Reliable Services-APIs
Wenn einer der folgenden Punkte auf Ihre Anwendungsdienstanforderungen zutrifft, sollten Sie Reliable Service-APIs in Erwägung ziehen:

- Sie müssen ein bestimmtes Anwendungsverhalten auf mehreren Zustandseinheiten (z. B. Bestellungen und Bestellpositionen) bereitstellen.

- Der Zustand Ihrer Anwendung kann auf natürliche Weise als zuverlässige Wörterbücher und Warteschlangen modelliert werden.

- Der Zustand muss hoch verfügbar sein, und der Zugriff muss mit geringer Latenz erfolgen.

- Ihre Anwendung muss in einer oder mehreren zuverlässigen Auflistungen die Parallelität bzw. Granularität der ausgeführten Vorgängen steuern.

- Sie möchten die Kommunikation verwalten und das Partitionierungsschema für Ihren Dienst steuern.

- Ihr Code benötigt eine Freethread-Laufzeitumgebung.

- Ihre Anwendung muss zuverlässige Wörterbücher oder Warteschlangen zur Laufzeit dynamisch erstellen oder zerstören.

- Sie müssen von Service Fabric bereitgestellte Sicherungs- und Wiederherstellungsfunktionen für den Zustand Ihres Diensts programmgesteuert ausführen.*

- Ihre Anwendung muss den Änderungsverlauf ihrer Zustandseinheiten protokollieren.*

- Sie möchten benutzerdefinierte Zustandsanbieter selbst entwickeln oder von Dritten nutzen*

> [AZURE.NOTE]*Diese Funktionen stehen mit der allgemeinen Verfügbarkeit des SDK zur Verfügung


## Nächste Schritte
+ [Reliable Services – Schnellstart](service-fabric-reliable-services-quick-start.md)
+ [Lesen Sie mehr über die erweiterte Verwendung von Reliable Services](service-fabric-reliable-services-advanced-usage.md)
+ [Erfahren Sie mehr über das Reliable Actors-Programmiermodell](service-fabric-reliable-actors-introduction.md)
 

<!---HONumber=Nov15_HO4-->