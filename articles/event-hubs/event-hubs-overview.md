<properties 
   pageTitle="Übersicht über Azure Event Hubs | Microsoft Azure"
   description="Einführung und Übersicht zu Azure Event Hubs."
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sethm" />

# Übersicht über Event Hubs

Viele moderne Lösungen sind darauf ausgelegt, adaptive Kundenerfahrungen bereitzustellen oder durch fortlaufendes Feedback und automatisierte Telemetrie-Produkte zu verbessern. Solche Lösungen stehen der Herausforderung gegenüber, auf sichere und zuverlässige Weise sehr große Informationsmengen von vielen Herausgebern gleichzeitig verarbeiten zu müssen. Microsoft Azure Event Hubs ist ein verwalteter Plattformdienst, der eine Grundlage für die Aufnahme umfangreicher Daten in einer Vielzahl von Szenarios bereitstellt. Beispiele für solche Szenarios sind Verhaltensanalysen in mobilen Apps, Verkehrsinformationen von Webfarmen, Ereignisaufzeichnungen in Konsolenspielen oder von Industriemaschinen oder verbundenen Fahrzeugen erfasste Telemetriedaten. In Lösungsarchitekturen übernehmen Event Hubs im Allgemeinen eine Funktion als "Eingangstür" für eine Ereignispipeline, häufig als *Ereigniserfasser* bezeichnet. Ein Ereigniserfasser ist eine Komponente oder ein Dienst zwischen Ereignisherausgeber und Ereignisverarbeitung zum Entkoppeln der Erzeugung eines Ereignisstreams von der Verarbeitung dieser Ereignisse.

![Ereignis-Hubs](./media/event-hubs-overview/IC759856.png)

Azure Event Hubs ist ein Dienst zur Ereignisverarbeitung, der riesige Mengen an Ereignis- und Telemetriedaten erfassen kann und gleichzeitig eine niedrige Latenz und hohe Zuverlässigkeit bietet. Dieser Dienst ist in Verbindung mit anderen nachgeschalteten Diensten besonders für Anwendungsinstrumentierung, Benutzeroberflächen oder Workflowverarbeitung sowie für Internet of Things (IoT)-Szenarios geeignet. Der Event Hubs-Dienst bietet eine Funktion zur Verarbeitung von Nachrichtenstreams, und obwohl Event Hubs Entitäten ähnlich wie Warteschlangen und Themen sind, weisen einige Merkmale stark vom herkömmlichen Enterprisemessaging ab. Enterprisemessaging-Szenarios erfordern häufig anspruchsvolle Funktionen wie z. B. Sequenzierung, unzustellbare Nachrichten, Transaktionsunterstützung und hohe Zustellungsgarantien, während für die Ereignisannahme vor allem ein hoher Durchsatz und Verarbeitunsflexibilität für Ereignisstreams wichtig sind. Daher unterscheiden sich Event Hubs von Service Bus-Themen darin, dass sie für Szenarios mit hohem Durchsatz und hoher Ereignisverarbeitung optimiert sind. In Event Hubs sind einige der Messagingfunktionen, die für Themen verfügbar sind, nicht direkt implementiert. Wenn Sie diese Funktionen benötigen, sind Themen weiterhin die optimale Wahl.

Ein Event Hub wird auf Namespaceebene in Service Bus erstellt, ähnlich wie Warteschlangen und Themen. Event Hubs verwendet AMQP und HTTP als primäre API-Schnittstellen. Das folgende Diagramm zeigt die Beziehung zwischen Event Hubs und Service Bus.

![Ereignis-Hubs](./media/event-hubs-overview/IC741188.png)

## Konzeptionelle Übersicht

Event Hubs bietet Nachrichtenstreaming über ein partitioniertes Consumermuster. Für Warteschlangen und Themen wird ein Modell [konkurrierender Consumer](https://msdn.microsoft.com/library/dn568101.aspx) verwendet, in dem jeder Consumer aus derselben Warteschlange oder Ressource liest. Diese Konkurrenz um Ressourcen sorgt letztlich für Komplexitäts- und Skalierungsgrenzen für Streamverarbeitungsanwendungen. Event Hubs verwendet ein partitioniertes Consumermuster, in dem jeder Consumer nur eine bestimmte Teilmenge oder Patition des Nachrichtenstreams liest. Dieses Muster ermöglicht eine horizontale Skalierung für die Ereignisverarbeitung und bietet andere streambezogene Features, die in Warteschlangen und Themen nicht verfügbar sind.

### Partitionen

Eine Partition ist eine geordnete Sequenz von Ereignissen, die in einem Event Hub vorhanden ist. Neu eingehende Ereignisse werden am Ende dieser Sequenz hinzugefügt. Eine Partition kann als "Commitprotokoll" betrachtet werden

![Ereignis-Hubs](./media/event-hubs-overview/IC759857.png)

In Partitionen werden Daten für eine konfigurierte Dauer beibehalten, die auf Event Hub-Ebene festgelegt wird. Diese Einstellung gilt für alle Partitionen im Event Hub. Ereignisse laufen nach Zeit ab. Sie können nicht direkt gelöscht werden. Ein Event Hub enthält mehrere Partitionen. Jede Partition ist unabhängig und enthält eine eigene Datensequenz. Partitionen wachsen daher oft mit unterschiedlicher Geschwindigkeit.

![Ereignis-Hubs](./media/event-hubs-overview/IC759858.png)

Die Anzahl der Partitionen wird bei der Erstellung des Event Hubs angegeben und muss zwischen 2 und 32 liegen (der Standardwert ist 4). Partitionen sind ein Mechanismus zur Datenorganisation und beziehen sich eher auf den für die verarbeitenden Anwendungen erforderlichen Grad der Downstreamparallelität als auf den Event Hubs-Durchsatz. Daher steht die ausgewählte Anzahl der Partitionen in einem Event Hub in direktem Zusammenhang mit der erwarteten Anzahl gleichzeitiger Leser. Nach dem Erstellen des Event Hubs kann die Anzahl der Partitionen nicht mehr geändert werden. Sie sollten daher beim Festlegen dieser Zahl die langfristig erwartete Größenordnung berücksichtigen. Das Limit von 32 Partitionen kann in Absprache mit dem Service Bus-Team erhöht werden.

Partitionen sind identifizierbar und können direkt adressiert werden, es ist jedoch im Allgemeinen vorzuziehen, Daten nicht an bestimmte Partitionen zu senden. Stattdessen können Sie Konstrukte höherer Ebene verwenden, die in den eingeführt, die den Abschnitten [Ereignisherausgeber](#event-publisher) und [Herausgeberrichtlinie](#capacity-and-security) erläutert werden.

Im Kontext von Event Hubs werden Nachrichten als *Ereignisdaten* bezeichnet. Ereignisdaten enthalten den Hauptteil des Ereignisses, einen benutzerdefinierten Eigenschaftenbehälter und verschiedene Metadaten über das Ereignis wie den Offset in der Partition und die Nummer in der Streamsequenz. Partitionen werden mit einer Sequenz von Ereignisdaten gefüllt.

## Ereignisherausgeber

Jede Entität, die Ereignisse oder Daten an einen Event Hub sendet, ist ein *Ereignisherausgeber*. Ereignisherausgeber können Ereignisse über HTTPS oder AMQP 1.0 veröffentlichen. Ereignisherausgeber identifizieren sich mit einem SAS-Token (Shared Access Signature) bei einem Event Hub und können eine eindeutige Identität aufweisen oder ein gemeinsames SAS-Token verwenden, je nach den Anforderungen des Szenarios.

Weitere Informationen zur Arbeit mit SAS finden Sie unter [SAS-Authentifizierung (Shared Access Signature) mit Service Bus](../service-bus/service-bus-shared-access-signature-authentication.md).

### Allgemeine Herausgeberaufgaben

In diesem Abschnitt werden allgemeine Aufgaben für Ereignisherausgeber beschrieben.

#### Abrufen eines SAS-Tokens

Shared Access Signature (SAS) ist der Authentifizierungsmechanismus für Event Hubs. Service Bus bietet SAS-Richtlinien auf Namespace- und Event Hub-Ebene. Ein SAS-Token wird aus einem SAS-Schlüssel generiert und ist ein SHA-Hash einer URL, der in einem bestimmten Format codiert ist. Mit dem Namen des Schlüssels (der Richtlinie) und dem Token kann Service Bus den Hash nachgenerieren und somit den Absender authentifizieren. In der Regel werden SAS-Token für Ereignisherausgeber nur mit der Berechtigung **Senden** für einen bestimmten Event Hub erstellt. Dieser SAS-Token-URL-Mechanismus bildet die Grundlage für die Herausgeberidentifizierung, die in der Herausgeberrichtlinie eingeführt wurde. Weitere Informationen zur Arbeit mit SAS finden Sie unter [SAS-Authentifizierung (Shared Access Signature) mit Service Bus](../service-bus/service-bus-shared-access-signature-authentication.md).

#### Veröffentlichen eines Ereignisses

Sie können ein Ereignis über AMQP 1.0 oder HTTPS veröffentlichen. Service Bus bietet eine [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx)-Klasse zum Veröffentlichen von Ereignissen für einen Event Hub von .NET-Clients aus. Für andere Runtimes und Plattformen können beliebiebige AMQP 1.0-Clients verwendet werden, z. B. [Apache Qpid](http://qpid.apache.org/). Sie können Ereignisse einzeln oder als Batch veröffentlichen. Jede Veröffentlichung (Ereignisdateninstanz) ist auf 256 KB beschränkt, unabhängig davon, ob es sich um ein einzelnes Ereignis oder einen Batch handelt. Das Veröffentlichen von größeren Ereignissen führt zu einem Fehler. Es ist eine bewährte Methode für Herausgeber, die Partitionen innerhalb des Event Hubs nicht zu beachten und nur einen *Partitionsschlüssel* (im nächsten Abschnitt eingeführt) oder die eigene Identität über das SAS-Token anzugeben.

Die Wahl zwischen AMQP oder HTTPS ist auf das Verwendungsszenario bezogen. AMQP erfordert die Einrichtung eines persistenten bidirektionalen Sockets zusätzlich zu TLS (Transport Level Security) oder SSL/TLS. Dies kann bezüglich des Netzwerkverkehrs ein kostenintensiver Vorgang sein, geschieht jedoch nur am Anfang einer AMQP-Sitzung. HTTPS weist einen geringeren anfänglichen Aufwand auf, erfordert jedoch zusätzlichen SSL-Mehraufwand für jede Anforderung. AMQP bietet Herausgebern, die häufig Ereignisse veröffentlichen, deutliche Einsparungen in der Leistung, der Wartezeit und dem Durchsatz.

### Partitionsschlüssel

Ein Partitionsschlüssel ist ein Wert, der in spezifischen Partitionen eingehenden Daten für die Datenorganisation zugeordnet wird. Der Partitionsschlüssel ist ein vom Absender bereitgestellter Wert, der an einen Event Hub übergeben wird. Er wird über eine statische Hashfunktion verarbeitet, deren Ergebnis die Partitionszuweisung erstellt. Wenn Sie beim Veröffentlichen eines Ereignisses keinen Partitionsschlüssel angeben, wird eine Roundrobinzuordnung verwendet. Bei der Verwendung von Schlüsseln ist dem Ereignisherausgeber nur der Partitionsschlüssel bekannt, nicht die Partition, in der die Ereignisse veröffentlicht werden. Dieses Entkoppeln von Schlüssel und Partition entbindet den Absender davon, zu viel über die Downstreamverarbeitung und Speicherung von Ereignissen wissen zu müssen. Partitionsschlüssel sind wichtig zum Organisieren von Daten für die Downstreamverarbeitung, stehen jedoch grundsätzlich nicht in Zusammenhang mit Partitionen. Eine gerätebezogene oder für einen Benutzer eindeutige Identität stellt einen guten Partitionsschlüssel dar, es können aber auch andere Attribute wie z. B. Geografie zum Gruppieren von verwandten Ereignissen in einer einzelnen Partition verwendet werden. Die folgende Abbildung zeigt, wie Ereignisabsender mithilfe von Partitionsschlüsseln Partitionen fixieren.

![Ereignis-Hubs](./media/event-hubs-overview/IC759859.png)

Event Hubs stellt sicher, dass alle Ereignisse, die denselben Partitionsschlüsselwert verwenden, in der richtigen Reihenfolge und an dieselbe Partition übermittelt werden. Wenn Partitionsschlüssel mit Herausgeberrichtlinien (im nächsten Abschnitt beschrieben) verwendet werden, müssen die Identität des Herausgebers und der Wert des Partitionsschlüssels übereinstimmen. Andernfalls tritt ein Fehler auf.

### Ereignisconsumer

Eine Entität, die Ereignisdaten aus einem Event Hub liest, ist ein Ereignisconsumer. Alle Ereignisconsumer lesen den Ereignisstream über Partitionen in einer Consumergruppe. Jede Partition sollte nur jeweils einen aktiven Leser aufweisen. Alle Event Hubs-Consumer stellen über eine AMQP 1.0-Sitzung eine Verbindung her, in der Ereignisse übermittelt werden, sobald sie verfügbar sind. Der Client muss die Verfügbarkeit der Daten nicht abfragen.

#### Verbrauchergruppen

Der Veröffentlichen-/Abonnieren-Mechanismus von Event Hubs erfolgt durch Consumergruppen. Eine Consumergruppe ist eine Ansicht (Status, Position oder Offset) eines gesamten Event Hubs. Mithilfe von Consumergruppen können mehrere verarbeitende Anwendungen jeweils eine separate Ansicht des Ereignisstreams aufweisen und den Stream unabhängig voneinander in einem unabhängigen Tempo und mit eigenen Offsets lesen. In einer Streamverarbeitungsarchitektur entspricht jede Downstreamanwendung einer Consumergruppe. Wenn Sie Ereignisdaten in den langfristigen Speicher schreiben möchten, ist die entsprechende Speicherschreibanwendung eine Consumergruppe. Komplexe Ereignisverarbeitung wird von einer anderen separaten Consumergruppe ausgeführt. Sie können auf Partitionen nur über eine Consumergruppe zugreifen. In einem Event Hub gibt es immer eine Standardconsumergruppe, und Sie können bis zu 20 Consumergruppen für einen Event Hub auf Standardebene erstellen.

Es folgen Beispiele für die URI-Konvention für Consumergruppen:

	//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
	//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>

Die folgende Abbildung zeigt die Ereignisconsumer in ihren Consumergruppen.

![Ereignis-Hubs](./media/event-hubs-overview/IC759860.png)

#### Streamoffsets

Ein Offset ist die Position eines Ereignisses innerhalb einer Partition. Sie können sich einen Offset als einen clientseitigen Zeiger vorstellen. Der Offset ist eine Nummerierung des Ereignisses in Byte. Dies ermöglicht Ereignisconsumern (Lesern), ein Punkt im Ereignisstream anzugeben, ab dem Ereignisse gelesen werden sollen. Sie können den Offset als Zeitstempel oder als Offsetwert angeben. Es liegt in der Verantwortung jedes Consumers, seine eigenen Offsetwerte außerhalb des Event Hubs-Diensts zu speichern.

![Ereignis-Hubs](./media/event-hubs-overview/IC759861.png)

Innerhalb einer Partition enthält jedes Ereignis einen Offset. Mit diesem Offset können Consumer die Position in der Ereignissequenz für eine bestimmte Partition anzeigen. Offsets können als Zahl oder als Zeitstempelwert an den Event Hub übergeben werden, wenn ein Leser eine Verbindung herstellt.

#### Setzen von Prüfpunkten

Das *Setzen von Prüfpunkten* ist ein Vorgang, bei dem Leser ihre Position innerhalb einer Partitionsereignissequenz markieren oder bestätigen. Dies liegt in der Verantwortung des Consumers und erfolgt auf Partitionsbasis innerhalb einer Consumergruppe. Das bedeutet, dass für jede Consumergruppe jeder Partitionsleser seine aktuelle Position im Ereignisstream nachverfolgen muss und den Dienst informieren kann, wenn er den Datenstrom als abgeschlossen betrachtet. Wenn ein Leser die Verbindung zu eine Partition trennt, beginnt nach dem erneuten Herstellen der Verbindung das Lesen bei dem Prüfpunkt, der zuvor durch den letzten Leser dieser Partition in dieser Consumergruppe übermittelt wurde. Wenn ein Leser eine Verbindung herstellt, übergibt er diesen Offset an den Event Hub, um die Position für den Lesevorgang anzugeben. Auf diese Weise können mithilfe von Prüfpunkten Ereignisse von Downstreamanwendungen als "abgeschlossen" markiert werden, und sie bieten Stabilität im Fall eines Failovers zwischen Lesern, die auf unterschiedlichen Computern ausgeführt werden. Da Ereignisdaten während des Aufbewahrungszeitraums beibehalten werden, der zum Zeitpunkt der Erstellung des Event Hubs angegeben wurde, ist es möglich, zu älteren Daten zurückzuspringen, indem ein niedrigerer Offset gegenüber diesem Prüfpunktvorgang angegeben wird. Durch diesen Mechanismus ermöglicht das Setzen von Prüfpunkten Failoverstabilität und kontrollierte erneute Wiedergabe des Ereignisstreams.

#### Allgemeine Consumeraufgaben

In diesem Abschnitt werden allgemeine Aufgaben für Event Hubs Ereignisconsumer oder -Leser beschrieben. Alle Event Hubs-Consumer stellen Verbindungen mit AMQP 1.0 her. AMQP 1.0 ist ein bidirektionaler Kommunikationskanal mit Sitzungs- und Statuskenntnis. Jede Partition weist eine AMQP 1.0-Linksitzung auf, die den Transport von Ereignissen nach Partition getrennt ermöglicht.

##### Herstellen einer Verbindung mit einer Partition

Um Ereignisse von einem Event Hub zu nutzen, muss ein Consumer eine Verbindung mit einer Partition herstellen. Wie bereits erwähnt greifen Sie auf Partitionen immer über eine Consumergruppe zu. Es gehört zum partitionierten Consumermodell, dass jeweils nur ein einziger Leser innerhalb einer Consumergruppe in einer Partition aktiv sein sollte. Oft werden bei direkten Verbindungen die Reader-Verbindungen zu bestimmten Partitionen mithilfe eines Leasingmechanismus koordiniert. Auf diese Weise ist es möglich, dass jede Partition in einer Consumergruppe nur über einen aktive Leser verfügt. Die Verwaltung der Position in der Sequenz für einen Leser ist eine wichtige Aufgabe, die durch das Setzen von Prüfpunkten erreicht wird. Diese Funktionalität wird mit der [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx)-Klasse für .NET-Clients vereinfacht. [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) ist ein intelligenter Consumer-Agent und wird im nächsten Abschnitt beschrieben.

##### Lesen von Ereignissen

Nachdem eine AMQP 1.0-Sitzung und ein Link für eine bestimmte Partition geöffnet wurde, werden Ereignisse vom Event Hubs-Dienst an den AMQP 1.0-Client übergeben. Dieser Mechanismus ermöglicht einen höheren Durchsatz und eine niedrigere Latenz als Pull-basierte Mechanismen wie z. B. HTTP GET. Wenn Ereignisse an den Client gesendet werden, enthält jede Ereignisdateninstanz wichtige Metadaten wie z. B. den Offset und die Sequenznummer, die zur Vereinfachung des Setzens von Prüfpunkten in der Ereignissequenz verwendet werden.

![Ereignis-Hubs](./media/event-hubs-overview/IC759862.png)

Es ist die Aufgabe des Benutzers, diesen Offset so zu verwalten, dass der Fortschritt bei der Verarbeitung des Datenstroms optimal verwaltet werden kann.

## Kapazität und Sicherheit

Event Hubs ist eine hochgradig skalierbare parallele Architektur für Streameingänge. Daher sind verschiedene wichtige Aspekte beim Festlegen der Größe und Skalierung einer Lösung für Event Hubs zu berücksichtigen. Das erste dieser Kapazitätsmaße sind die *Durchsatzeinheiten*, die im folgenden Abschnitt beschrieben werden.

### Durchsatzeinheiten

Die Durchsatzkapazität von Event Hubs wird durch Durchsatzeinheiten gesteuert. Durchsatzeinheiten werden vorab als Kapazitätseinheiten erworben. Eine einzelne Durchsatzeinheit umfasst Folgendes:

- Eingang: Bis zu 1 MB pro Sekunde oder 1000 Ereignisse pro Sekunde.

- Ausgang: Bis 2 MB pro Sekunde.

Der Eingang wird auf die Kapazität beschränkt, die der erworbenen Anzahl von Durchsatzeinheiten entspricht. Das Senden von Daten über diese Menge hinaus führt zur Ausnahme "Datenträgerkontingent überschritten". Diese Menge ist entweder 1 MB pro Sekunde oder 1000 Ereignisse pro Sekunde, je nachdem, was zuerst eintritt. Am Ausgang erfolgt keine Beschränkung, jedoch ist dieser auf Datenübertragungsmenge anhand der erworbenen Durchsatzeinheiten beschränkt: 2 MB pro Sekunde pro Durchsatzeinheit. Wenn Sie Ausnahmen für die Veröffentlichungsrate erhalten oder einen größeren Ausgang erwarten, überprüfen Sie, wie viele Durchsatzeinheiten Sie für den Namespace erworben haben, in dem der Event Hub erstellt wurde. Um weitere Durchsatzeinheiten zu erhalten, können Sie die Einstellung auf der Seite **Namespaces** auf der Registerkarte **Skalieren** im [klassischen Azure-Portal][] anpassen. Sie können diese Einstellung auch mit den Azure-APIs ändern.

Während Partitionen ein Datenorganisationskonzept sind, sind Durchsatzeinheiten ausschließlich ein Kapazitätskonzept. Durchsatzeinheiten werden auf Stundenbasis abgerechnet und im Voraus erworben. Nach dem Erwerb werden Durchsatzeinheiten für mit einem Minimum von einer Stunde in Rechnung gestellt. Bis zu 20 Durchsatzeinheiten können für einen Service Bus-Namespace erworben werden, und für ein Azure-Konto besteht ebenfalls eine Grenze von 20 Durchsatzeinheiten. Diese Durchsatzeinheiten werden für alle Event Hubs in einem bestimmten Namespace gemeinsam genutzt.

Durchsatzeinheiten werden nach bestmöglicher Leistung bereitgestellt und stehen nicht immer für einen sofortigen Erwerb zur Verfügung. Wenn Sie eine bestimmte Kapazität benötigen, empfiehlt es sich, diese Durchsatzeinheiten im Voraus zu erwerben. Wenn Sie mehr als 20 Durchsatzeinheiten benötigen, können Sie sich an den Service Bus-Support wenden, um weitere Einheiten zu erwerben. Bis zu den ersten 100 Durchsatzeinheiten werden diese in Blöcken von je 20 erworben. Darüber hinaus können Sie auch Blöcke von je 100 Durchsatzeinheiten erwerben.

Es wird empfohlen, Durchsatzeinheiten und Partitionen sorgfältig miteinander abzustimmen, um eine optimale Skalierbarkeit mit Event Hubs zu erreichen. Eine einzelne Partition weist über eine maximale Skalierung von einer Durchsatzeinheit auf. Die Anzahl der Durchsatzeinheiten sollte kleiner oder gleich der Anzahl der Partitionen in einem Event Hub sein.

Preisinformationen finden Sie unter [Event Hubs Preise](https://azure.microsoft.com/pricing/details/event-hubs/).

### Herausgeberrichtlinie

Event Hubs ermöglicht eine abgestufte Kontrolle über Ereignisherausgeber durch *Herausgeberrichtlinien*. Herausgeberrichtlinien sind eine Sammlung von Laufzeitfunktionen, mit denen große Mengen unabhängiger Herausgeber verwaltet werden können. Mit Herausgeberrichtlinien verwendet jeder Herausgeber einen eigenen eindeutigen Bezeichner für die Veröffentlichung von Ereignissen in einem Event Hub. Dabei kommt der folgende Mechanismen zum Einsatz:

	//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>

Sie müssen Herausgebernamen nicht im Voraus erstellen, jedoch müssen diese mit dem SAS-Token übereinstimmen, das beim Veröffentlichen eines Ereignisses verwendet wird, um die Identitäten unabhängiger Herausgeber sicherzustellen. Weitere Informationen zu SAS finden Sie unter [SAS-Authentifizierung (Shared Access Signature) mit Service Bus](../service-bus/service-bus-shared-access-signature-authentication.md). Bei Verwendung von Herausgeberrichtlinien wird der Wert **PartitionKey** auf den Herausgebernamen festgelegt. Für eine ordnungsgemäße Funktion müssen diese Werte übereinstimmen.

## Zusammenfassung

Azure Event Hubs bietet einen hyperskalierbaren Ereignis- und Telemetrieverarbeitungsdienst, der für die Überwachung allgemeiner Anwendungs- und Benutzerworkflows in jeder Größenordnung verwendet werden kann. Mit der Möglichkeit, Veröffentlichen-/Abonnieren-Funktionen mit niedriger Latenz und enormem Umfang anzubieten, sind Event Hubs der Einstiegspunkt für Big Data. Durch die Identitäts-und Sperrlisten auf Herausgeberbasis können diese Funktionen in verbreitete Internet of Things-Szenarios erweitert werden. Weitere Informationen zum Entwickeln von Event Hubs-Anwendungen finden Sie im [Programmierleitfaden für Event Hubs](event-hubs-programming-guide.md).

## Nächste Schritte

Nun, da Sie sich mit Event Hubs-Konzepten vertraut gemacht haben, können Sie mit den folgenden Szenarios fortfahren:

- Beginnen Sie mit einem [Event Hubs-Lernprogramm].
- Eine vollständige [Beispielanwendung mit Verwendung von Ereignis-Hubs].
- Eine [Messaginglösung mit Warteschlange] unter Verwendung von Service Bus-Warteschlangen.

[klassischen Azure-Portal]: http://manage.windowsazure.com
[Event Hubs-Lernprogramm]: event-hubs-csharp-ephcs-getstarted.md
[Beispielanwendung mit Verwendung von Ereignis-Hubs]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[Messaginglösung mit Warteschlange]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0128_2016-->