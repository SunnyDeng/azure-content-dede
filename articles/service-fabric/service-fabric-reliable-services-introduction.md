<properties
   pageTitle="Übersicht über das Service Fabric Reliable Services-Programmiermodell | Microsoft Azure"
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
   ms.date="01/13/2016"
   ms.author="masnider;jesseb"/>

# Übersicht über Reliable Services
Azure Service Fabric vereinfacht das Schreiben und Verwalten zustandsloser und zustandsbehafteter Dienste (Reliable Services). In diesem Dokument wird Folgendes behandelt:

- Das Reliable Services-Programmiermodell für zustandslose und zustandsbehaftete Dienste
- Die Optionen, die beim Schreiben von Reliable Services zur Auswahl stehen
- Szenarien und Beispiele für die Verwendung von Reliable Services und wie sie geschrieben werden

Reliable Services zählen zu den in Service Fabric verfügbaren Programmiermodellen. Weitere Informationen zum Reliable Actors-Programmiermodell finden Sie unter [Einführung in Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

Dienste in Service Fabric setzen sich aus der Konfiguration, dem Anwendungscode und (optional) einem Zustand zusammen.

Service Fabric verwaltet die Lebensdauer von Diensten von der Bereitstellung und Implementierung bis hin zur Aktualisierung und Löschung. Diese Vorgänge erfolgen über die [Anwendungsverwaltung von Service Fabric](service-fabric-deploy-remove-applications.md).

## Was sind Reliable Services?
Reliable Services bietet ein einfaches, leistungsfähiges High-Level-Programmiermodell, mit dem Sie alles Wichtige in Ihrer Anwendung programmieren können. Das Reliable Services-Programmiermodell bietet Folgendes:

- Für zustandsbehaftete Dienste ermöglicht das Reliable Services-Programmiermodell eine konsistente und zuverlässige Speicherung des Zustands direkt innerhalb des Diensts anhand von Reliable Collections. Hierbei handelt es sich um einen einfachen Satz von hoch verfügbaren Auflistungsklassen, die jedem vertraut sind, der bereits mit C#-Auflistungen gearbeitet hat. Bisher benötigten Dienste externe Systeme für die zuverlässige Zustandsverwaltung. Mit Reliable Collections können Sie den Zustand zusammen mit dem Computedienst speichern und dabei von der gleichen hohen Verfügbarkeit und Zuverlässigkeit, die Sie von hoch verfügbaren externen Speichern gewohnt sind, und von zusätzlichen Latenzverbesserungen profitieren, die durch das Zusammenbringen von Computedienst und Zustand erzielt werden.

- Ein einfaches Modell für die Ausführung Ihres eigenen Codes, das den gewohnten Programmiermodellen ähnelt. Der Code besitzt einen klar definierten Einstiegspunkt und einen leicht zu verwaltenden Lebenszyklus.

- Ein austauschbares Kommunikationsmodell. Verwenden Sie den gewünschten Transport, z. B. HTTP mit [Web-API](service-fabric-reliable-services-communication-webapi.md), WebSockets, benutzerdefinierte TCP-Protokolle usw. Reliable Services bieten einige nützliche vorkonfigurierte Optionen. Sie können aber auch eigene Optionen bereitstellen.

## Was unterscheidet Reliable Services von anderen Diensten?
Reliable Services in Service Fabric sind nicht wie andere Dienste, die Sie möglicherweise bereits geschrieben haben. Service Fabric bietet Zuverlässigkeit, Verfügbarkeit, Konsistenz und Skalierbarkeit.

- **Zuverlässigkeit**: Der Dienst wird auch dann fortgesetzt, wenn in der Umgebung Probleme auftreten, wie etwa Computerausfälle oder Netzwerkprobleme.

- **Verfügbarkeit**: Ihr Dienst bleibt erreichbar und reaktionsfähig. (Das bedeutet nicht, dass Sie keine Dienste besitzen können, die von außerhalb nicht auffindbar oder erreichbar sind.)

- **Skalierbarkeit**: Die Dienste sind von bestimmter Hardware entkoppelt und können durch das Hinzufügen oder Entfernen von Hardware oder virtuellen Ressourcen nach Bedarf vergrößert oder verkleinert werden. Die Dienste lassen sich einfach partitionieren (insbesondere, wenn sie statusbehaftet sind), um sicherzustellen, dass Teile des Diensts unabhängig voneinander skaliert werden und auf Fehler reagieren können. Schließlich fördert Service Fabric das Erstellen einfacher Dienste, indem in einem einzigen Prozess Tausende von Diensten bereitgestellt werden können, anstatt ganze Betriebssysteminstanzen zu erzwingen oder für eine einzige Instanz einer bestimmten Arbeitsauslastung zu reservieren.

- **Konsistenz**: Bei allen in diesem Dienst gespeicherten Informationen kann die Konsistenz garantiert werden (gilt nur für zustandsbehaftete Dienste – mehr dazu später).

## Dienstlebenszyklus
Ungeachtet dessen, ob der Dienst zustandsbehaftet oder zustandslos ist, bieten Reliable Services einen einfachen Lebenszyklus, mit dem Sie den Code schnell einbinden und beginnen können. Sie müssen zum Ausführen des Diensts nur eine oder zwei Methoden implementieren.

- **CreateServiceReplicaListeners/CreateServiceInstanceListeners**: Hier werden vom Dienst die zu verwendenden Kommunikationsstapel definiert. Der Kommunikationsstapel, z. B. [Web-API](service-fabric-reliable-services-communication-webapi.md), definiert den/die lauschenden Endpunkt(e) für den Dienst (sodass er für Clients erreichbar ist). Er definiert auch, wie die angezeigten Nachrichten letztlich mit dem Rest des Dienstcodes interagieren.

- **RunAsync**: Hiermit führt der Dienst seine Geschäftslogik aus. Das bereitgestellte Abbruchtoken dient als Signal, wenn die Arbeit beendet werden soll. Wenn Sie beispielsweise einen Dienst haben, der permanent Nachrichten aus einer Reliable Queue abrufen und verarbeiten muss, wird dieser Vorgang hiermit ausgeführt.

### Starten des Diensts

Die wichtigsten Ereignisse im Lebenszyklus von Reliable Services sind:

1. Das Dienstobjekt (das sich vom zustandslosen oder vom zustandsbehafteten Dienst ableitet) wird erstellt.

2. Die `CreateServiceReplicaListeners`/`CreateServiceInstanceListeners`-Methode wird aufgerufen, sodass der Dienst einen oder mehrere Kommunikationslistener seiner Wahl zurückzugeben kann.
  - Dies ist optional, obwohl die meisten Dienste einige Endpunkte direkt verfügbar machen.

3. Nach dem Erstellen der Kommunikationslistener wird der Dienst geöffnet.
  - Kommunikationslistener verfügen über eine Methode namens `OpenAsync()`, die zu diesem Zeitpunkt aufgerufen wird und die Lauschadresse für den Dienst zurückgibt. Wenn Ihr Reliable Services-Dienst einen der integrierten ICommunicationListener verwendet, erfolgt dies automatisch.

4. Sobald der Kommunikationslistener geöffnet wurde, wird die `RunAsync()`-Methode für den Hauptdienst aufgerufen.
  - Beachten Sie, dass `RunAsync()` optional ist. Wenn der Dienst alle Vorgänge nur direkt infolge von Benutzeraufrufen ausführt, muss `RunAsync()` nicht implementiert werden.

### Beenden des Diensts

Wenn der Dienst heruntergefahren wird (um gelöscht, aktualisiert oder verschoben zu werden), wird die Aufrufreihenfolge umgekehrt: Zuerst wird das von `RunAsync()` verwendete Abbruchtoken frei, und dann wird `CloseAsync()` für die Kommunikationslistener aufgerufen.

Beim Herunterfahren zustandsbehafteter Dienste gibt es einige wichtige Punkte zu beachten:

- Service Fabric stuft kein anderes Replikat Ihres Diensts auf den primären Status hoch, bis `CloseAsync` und `RunAsync` zurückgegeben sind. Wenn Sie einen integrierten Kommunikationslistener verwenden, wird die `CloseAsync`-Methode für Sie verarbeitet.

- Zwar gibt es keine zeitliche Begrenzung für die Rückgabe dieser Methoden, aber Sie verlieren sofort die Möglichkeit zum Schreiben in Reliable Collections und können daher Ihre eigentlichen Arbeiten nicht abschließen. Es wird empfohlen, dass Sie sie so schnell wie möglich nach dem Empfang der Abbruchanforderung zurückgeben.

## Beispieldienste
Nachdem Sie diese Programmiermodell nun kennen, werfen wir einen Blick auf zwei Dienste, um zu sehen, wie diese Elemente zusammenspielen.

### Zustandslose Reliable Services
Ein zustandsloser Dienst hat buchstäblich keinen Zustand, oder der vorhandene Zustand wird vollständig verworfen und erfordert keinerlei Synchronisierung, Replikation, Persistenz oder hohe Verfügbarkeit.

Denken Sie beispielsweise an einen Rechner, der keinen Speicher hat und alle Zahlen und durchzuführenden Operationen gleichzeitig erhält.

In diesem Fall kann der Dienst „RunAsync()“ leer sein, da er keine Aufgaben im Hintergrund auszuführen hat. Wenn der Rechnerdienst erstellt wird, gibt er einen ICommunicationListener (z. B. [Web-API](service-fabric-reliable-services-communication-webapi.md)) zurück, der auf einem Port einen Lauschendpunkt öffnet. Dieser Lauschendpunkt wird mit den verschiedenen Methoden verknüpft (z. B. „Add(n1, n2)“), die die öffentliche API des Rechners definieren.

Erfolgt ein Aufruf durch einen Client, wird die entsprechende Methode ausgelöst. Der Rechnerdienst führt an den bereitgestellten Daten die erforderlichen Operationen durch und gibt das Ergebnis zurück. Es wird kein Zustand gespeichert.

Die Tatsache, dass kein interner Zustand gespeichert wird, macht den Beispielrechner äußerst einfach. Aber die meisten Dienste sind nicht wirklich zustandslos. Stattdessen lagern sie ihren Zustand in einen anderen Speicher aus. (Beispielsweise ist jede Web-App, die zum Speichern des Sitzungszustands einen Sicherungsspeicher oder Cache verwendet, nicht völlig zustandslos.)

Zustandslose Dienste werden in Service Fabric beispielsweise häufig als Front-End verwendet, das die öffentliche API für eine Webanwendung verfügbar macht. Der Front-End-Dienst kommuniziert in diesem Fall mit zustandsbehafteten Diensten, um eine Benutzeranforderung zu verarbeiten. Aufrufe von Clients werden dabei an einen bekannten Port wie Port 80 geleitet, an dem der zustandslose Dienst lauscht. Dieser zustandslose Dienst erhält den Aufruf und ermittelt, ob dieser von einem vertrauenswürdigen Teilnehmer stammt und für welchen Dienst er bestimmt ist. Der zustandslose Dienst leitet den Aufruf anschließend an die richtige Partition des zustandsbehafteten Diensts weiter und wartet auf eine Antwort. Sobald der zustandslose Dienst eine Antwort erhält, sendet er eine Antwort an den ursprünglichen Client zurück.

### Zustandsbehaftete Reliable Services
Bei einem zustandsbehafteten Dienst muss ein gewisser Teil des Zustands konsistent und präsent sein, damit der Dienst funktioniert. Nehmen wir einen Dienst, der kontinuierlich einen gleitenden Durchschnitt eines Werts berechnet, der regelmäßig aktualisiert wird. Der Dienst benötigt zu diesem Zweck den aktuellen Satz eingehender, zu verarbeitender Anforderungen und den aktuellen Durchschnittswert. Jeder Dienst, der Informationen in einem externen Speicher abruft, verarbeitet und speichert (z. B. in einem modernen Azure-Blob- oder Tabellenspeicher), ist zustandsbehaftet. Er bewahrt seinen Zustand nur im externen Zustandsspeicher auf.

Die meisten Dienste speichern heute ihren Zustand extern, da der externe Speicher die für den Zustand erforderliche Zuverlässigkeit, Verfügbarkeit, Skalierbarkeit und Konsistenz bietet. In Service Fabric müssen zustandsbehaftete Dienste ihren Zustand nicht extern speichern, da Service Fabric diese Anforderungen für den Code und den Zustand des Diensts übernimmt.

Angenommen, Sie möchten einen Dienst schreiben, der Anforderungen für eine Reihe von Konvertierungen an einem Bild sowie das zu konvertierende Bild erhält. Service Fabric würde für diesen Dienst einen Kommunikationslistener (z. B. Web-API) zurückgeben, der einen Kommunikationsport für Übermittlungen über eine API wie `ConvertImage(Image i, IList<Conversion> conversions)` öffnet. Der Dienst könnte in dieser API die Informationen empfangen und die Anforderung in einer Reliable Queue speichern. Anschließend würde er zur Nachverfolgung der Anforderung ein Token an den Client zurückgeben (da die Verarbeitung der Anforderungen eine Weile dauern kann).

In diesem Dienst könnte RunAsync etwas komplexer ausfallen. Der Dienst könnte in RunAsync eine Schleife verwenden, die Anforderungen aus „IReliableQueue“ abruft, die aufgelisteten Konvertierungen durchführt und die Ergebnisse in „IReliableDictionary“ speichert. Der Client kann daraufhin konvertierte Bilder abrufen. Um sicherzustellen, dass das Bild auch bei einem Fehler nicht verloren geht, würde dieser Reliable Services-Dienst aus der Warteschlange aussteigen, die Konvertierungen durchführen und das Ergebnis in einer Transaktion speichern. In diesem Fall wird die Nachricht tatsächlich nur aus der Warteschlange entfernt, und die Ergebnisse werden im Ergebniswörterbuch gespeichert, wenn die Konvertierungen abgeschlossen sind. Wenn während der Verarbeitung ein Fehler auftritt (z. B. wenn der Computer, auf dem diese Instanz des Codes ausgeführt wird, ausfällt), bleibt die Anforderung zur erneuten Verarbeitung in der Warteschlange.

Zu diesem Dienst ist zu beachten, dass er wie ein normaler .NET-Dienst erscheint. Der einzige Unterschied besteht darin, dass die verwendeten Datenstrukturen („IReliableQueue“ und „IReliableDictionary“) von Service Fabric bereitgestellt werden und daher äußerst zuverlässig, verfügbar und konsistent sind.

## Anwendungsbereiche für Reliable Services-APIs
Wenn einer der folgenden Punkte auf Ihre Anwendungsdienstanforderungen zutrifft, sollten Sie Reliable Services-APIs in Erwägung ziehen:

- Sie müssen ein bestimmtes Anwendungsverhalten auf mehreren Zustandseinheiten (z. B. Bestellungen und Bestellpositionen) bereitstellen.

- Der Zustand Ihrer Anwendung kann auf natürliche Weise als Reliable Dictionaries und Queues modelliert werden.

- Der Zustand muss hoch verfügbar sein, und der Zugriff muss mit geringer Latenz erfolgen.

- Ihre Anwendung muss in einer oder mehreren Reliable Collections die Parallelität bzw. Granularität von ausgeführten Vorgängen steuern.

- Sie möchten die Kommunikation verwalten oder das Partitionierungsschema für Ihren Dienst steuern.

- Ihr Code benötigt eine Laufzeitumgebung mit dem Threadingmodell „Free“.

- Ihre Anwendung muss Reliable Dictionaries oder Queues zur Laufzeit dynamisch erstellen oder zerstören.

- Sie müssen von Service Fabric bereitgestellte Sicherungs- und Wiederherstellungsfeatures für den Zustand Ihres Diensts programmgesteuert ausführen.*

- Ihre Anwendung muss den Änderungsverlauf ihrer Zustandseinheiten protokollieren.*

- Sie möchten benutzerdefinierte Zustandsanbieter selbst entwickeln oder von Drittanbietern nutzen.*

> [AZURE.NOTE]* Diese Funktionen stehen mit der allgemeinen Verfügbarkeit des SDK zur Verfügung.


## Nächste Schritte
+ [Reliable Services – Schnellstart](service-fabric-reliable-services-quick-start.md)
+ [Erweiterte Verwendung von Reliable Services](service-fabric-reliable-services-advanced-usage.md)
+ [Das Reliable Actors-Programmiermodell](service-fabric-reliable-actors-introduction.md)

<!---HONumber=AcomDC_0114_2016-->