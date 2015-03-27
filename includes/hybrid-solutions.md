#Azure-Servicebus

Ob eine Anwendung oder ein Dienst in einer Cloud betrieben wird oder lokal installiert ist - sie bzw. er muss oft mit anderen Anwendungen oder Diensten interagieren. Damit dies in allgemein nützlicher Weise geschieht, bietet Azure die Option Service Bus. In diesem Artikel betrachten wir diese Technologie und beschreiben, worum es sich dabei handelt und warum Sie sie gut gebrauchen könnten.

## Service Bus - Grundlagen
Verschiedene Situationen erfordern verschiedene Kommunikationsarten. Manchmal ist die beste Lösung, Anwendungen Nachrichten über einfache Warteschlangen senden und empfangen zu lassen. In anderen Situationen ist eine normale Warteschlange nicht genug, sondern eine Warteschlange mit einem Veröffentlichungs- und Abonnementsmechanismus ist besser geeignet. Und in manchen Fällen wird einfach nur eine Verbindung zwischen den Anwendungen benötigt, und Warteschlangen sind nicht erforderlich. Service Bus bietet alle drei Optionen, sodass Ihre Anwendungen auf mehrere verschiedene Arten miteinander interagieren können.

Service Bus ist ein Cloud-Dienst mit mehreren Mandanten. Das bedeutet, dass der Dienst von mehreren Benutzern gemeinsam genutzt wird. Jeder Benutzer, z. B. auch ein Anwendungsentwickler, erstellt einen  *namespace* und definiert dann die erforderlichen Kommunikationsmechanismen innerhalb dieses Namespace. [Abbildung 1](#Fig1) zeigt, wie dies aussieht.

<a name="Fig1"></a>![Diagramm von Azure Service Bus][svc-bus]
 
**Abbildung 1: Service Bus bietet einen Dienst mit mehreren Mandanten zur Verbindung von Anwendungen über die Cloud.**

Innerhalb eines Namespace können Sie eine oder mehr Instanzen von vier verschiedenen Kommunikationsmechanismen verwenden, von denen jeder die Anwendungen auf andere Weise verbindet. Die Auswahlmöglichkeiten sind:

- *Warteschlangen* für eine unidirektionale Kommunikation. Jede Warteschlange agiert als Zwischenstufe (manchmal auch  *broker* genannt), die gesendete Nachrichten speichert, bis diese empfangen werden. Jede Nachricht wird von einem einzelnen Empfänger empfangen.
- *Themen* für die unidirektionale Kommunikation mit *subscriptions*, wobei ein einzelnes Thema mehrere Abonnements aufweisen kann. Wie bei eine Warteschlange agiert ein Thema als Broker, aber jedes Abonnent kann optional Filter verwenden, um nur Nachrichten zu empfangen, die bestimmte Kriterien erfüllen.
- *Relais* für eine bidirektionale Kommunikation. Anders als Warteschlangen und Themen speichert ein Relais keine gesendeten Nachrichten - es ist kein Broker. Stattdessen leitet es sie an eine Zielanwendung weiter.
- *Event Hubs* für Ereignis- und Telemetrieeingänge in erheblichem Umfang in der Cloud, mit geringer Latenz und hoher Zuverlässigkeit.

Wenn Sie eine Warteschlange, ein Thema, ein Relais oder ein Event Hub erstellen, geben Sie diesem Objekt einen Namen. In Kombination mit der Bezeichnung Ihres Namespace ist dieser Name ein eindeutiger Bezeichner für das Objekt. Anwendungen können diesen Namen an Service Bus übermitteln und dann mithilfe der Warteschlange, des Themas, des Relais oder des Event Hubs miteinander kommunizieren. 

Für die Kommunikation mit einem dieser Objekte können Windows-Anwendungen Windows Communication Foundation (WCF) nutzen. Für Warteschlangen, Themen und Event Hubs können Windows-Anwendungen auch eine über Service Bus definierte Messaging-API verwenden. Damit diese Objekte von Nicht-Windows-Anwendungen einfacher verwendet werden können, stellt Microsoft SDKs für Java, Node.js und andere Sprachen bereit. Sie können auch mithilfe von REST-APIs über HTTP auf Warteschlangen, Themen und Event Hubs zugreifen. 

Es ist wichtig zu verstehen, dass, obwohl Service Bus selbst in einer Cloud (in den Azure-Rechenzentren von Microsoft) ausgeführt wird, die Anwendungen, die es verwenden, überall laufen können. Sie können Service Bus zum Verbinden von Anwendungen auf Azure oder innerhalb Ihres eigenen Rechenzentrums verwenden. Sie können damit auch eine Anwendung, die auf Azure oder einer anderen Cloud-Plattform läuft, mit einer lokalen Anwendung oder mit Tablets oder Telefonen verbinden. Es ist auch möglich, Haushaltsgeräte, Sensoren und andere Vorrichtungen mit einer zentralen Anwendung oder miteinander zu verbinden. Service Bus ist ein generischer Kommunikationsmechanismus in der Cloud, auf den praktisch überall zugegriffen werden kann. Wie Sie es verwenden, hängt davon ab, was Ihre Anwendungen leisten müssen.


## Warteschlangen

Angenommen, Sie entscheiden sich dafür, zwei Anwendungen über eine Service Bus-Warteschlange miteinander zu verbinden. [Abbildung 2](#Fig2) veranschaulicht diese Situation.

<a name="Fig2"></a>![Diagramm mit Service Bus-Warteschlangen][queues]
 
**Abbildung 2: Service Bus-Warteschlangen bieten asynchrone unidirektionale Kommunikation.**

Der Prozess ist ganz einfach: Ein Sender sendet eine Nachricht an eine Service Bus-Warteschlange, und ein Empfänger greift diese Nachricht zu einem späteren Zeitpunkt auf. Eine Warteschlange kann entweder einen einzelnen Empfänger aufweisen, wie [Abbildung 2](#Fig2) zeigt, oder mehrere Anwendungen können aus derselben Warteschlange lesen. In der letzten Situation wird jede Nachricht nur von einem Empfänger gelesen. Für einen Multicastdienst sollten Sie stattdessen ein Thema verwenden.

Jede Nachricht besteht aus zwei Teilen: einem Satz von Eigenschaften (jede davon ist ein Paar aus Schlüssel und Wert) und einem binären Nachrichtentext. Wie sie verwendet werden, hängt davon ab, was eine Anwendung zu tun versucht. Beispielsweise kann eine Anwendung mit dem Senden einer Nachricht über einen kürzlich erfolgten Verkauf gehören die Eigenschaften *Seller="Ava"* und *Amount=10000*. Der Nachrichtentext kann ein gescanntes Bild des unterzeichneten Verkaufsvertrags enthalten oder auch leer sein.

Ein Empfänger kann eine Nachricht von einer Service Bus-Warteschlange auf zwei verschiedene Arten lesen. Die erste Option namens *ReceiveAndDelete* besteht darin, eine Nachricht aus der Warteschlange zu entfernen und sofort zu löschen. Dies ist einfach, aber wenn der Empfänger ausfällt, bevor die Verarbeitung der Nachricht abgeschlossen ist, geht die Nachricht verloren. Da die Nachricht dann aus der Warteschlange entfernt worden ist, kann kein anderer Empfänger auf sie zugreifen. 

Die zweite Option, *PeekLock*, soll dieses Problem lösen. Wie bei ReceiveAndDelete wird auch bei PeekLock eine gelesene Nachricht aus der Warteschlange entfernt. Die Nachricht wird allerdings nicht gelöscht, sondern gesperrt und für andere Empfänger unsichtbar gemacht. Anschließend wird auf eines von drei Ereignissen gewartet.

- Wenn der Empfänger die Nachricht erfolgreich verarbeitet, wird die Meldung *Complete* ausgegeben, und die Warteschlange löscht die Nachricht. 
- Wenn der Empfänger entscheidet, dass er die Nachricht nicht erfolgreich verarbeiten kann, wird die Meldung *Abandon* ausgegeben. In diesem Falle hebt die Warteschlange die Sperrung der Nachricht auf und macht sie für andere Empfänger verfügbar.
- Wenn der Empfänger innerhalb eines konfigurierbaren Zeitraums (standardmäßig 60 Sekunden) keine der beiden Meldungen ausgibt, nimmt die Warteschlange an, dass der Empfänger ausgefallen ist. In diesem Falle verhält sie sich so, als hätte der Empfänger die Meldung "Abandon" ausgegeben und macht die Nachricht für andere Empfänger verfügbar.

Bitte beachten Sie, was hier passieren kann: Dieselbe Nachricht kann zweimal ausgegeben werden, vielleicht sogar an zwei verschiedene Empfänger. Anwendungen, die Service Bus-Warteschlangen verwenden, müssen darauf vorbereitet sein. Um die Erkennung von Duplikaten zu erleichtern, hat jede Nachricht eine eindeutige MessageID, die standardmäßig gleich bleibt, egal, wie oft die Nachricht von einer Warteschlange gelesen wird. 

Warteschlangen sind in einigen Situationen sinnvoll. Sie ermöglichen eine Kommunikation zwischen Anwendungen auch dann, wenn beide zur selben Zeit ausgeführt werden, was bei Batch- und Mobilanwendungen sehr praktisch sein kann. Eine Warteschlange mit mehreren Empfängern bietet auch automatischen Lastenausgleich, da gesendete Nachrichten zwischen diesen Empfängern verteilt werden.


## Themen

So hilfreich sie generell auch sind - Warteschlangen sind nicht immer die beste Lösung. Manchmal sind Service Bus-Themen besser. [Abbildung 3](#Fig3) veranschaulicht dieses Konzept.

<a name="Fig3"></a>![Diagramm mit Service Bus-Themen und -Abonnements][topics-subs]
 
**Abbildung 3: Auf Grundlage des Filters, der in einer Abonnements-Anwendung definiert ist, kann diese einige oder alle Nachrichten empfangen, die an ein Service Bus-Thema gesendet wurden.**

Ein Thema ist in mancherlei Weise einer Warteschlange ähnlich. Sender schicken Nachrichten an ein Thema auf dieselbe Weise, auf die sie Nachrichten an eine Warteschlange schicken, und diese Nachrichten sehen genauso aus wie bei den Warteschlangen. Der Hauptunterschied ist, dass Themen es jeder empfangenden Anwendung ermöglichen, ihr eigenes Abonnement zu erstellen, indem sie einen  *filter* definieren. Dem Abonnent werden dann nur die Meldungen angezeigt, die diesem Filter entsprechen. [Abbildung 3](#Fig3) zeigt z. B. einen Sender und ein Thema mit drei Abonnenten, jeder mit seinem eigenen Filter:

- Abonnent 1 empfängt nur Nachrichten, die die Eigenschaft  *Seller="Ava"* haben.
- Abonnent 2 empfängt Nachrichten, die die Eigenschaft  *Seller="Ruby"* und/oder eine Eigenschaft namens  *Amount* haben, deren Wert größer ist als 100.000. Ruby könnte die Vertriebschefin sein und sowohl ihre eigenen Verkäufe sehen wollen als auch diejenigen anderer Verkäufer ab einer bestimmten Menge.
- Abonnent 3 hat seinen Filter auf  *True* gesetzt, was bedeutet, dass er alle Nachrichten empfängt. Diese Anwendung könnte z. B. zum Verwalten eines Überwachungspfads zuständig sein. Daher muss sie alle Nachrichten sehen können.

Wie bei Warteschlangen können Abonnenten eines Themas Nachrichten entweder über ReceiveAndDelete oder über PeekLock lesen. Anders als bei Warteschlangen kann jedoch eine einzelne an ein Thema gesendete Nachricht von mehreren Abonnenten empfangen werden. Dieser Ansatz, der so genannte *publish and subscribe*, ist nützlich, wenn mehrere Anwendungen an denselben Nachrichten interessiert sind. Durch Definition eines passenden Filters kann jeder Abonnent genau den Teil aus der Nachricht herausziehen, den er mitbekommen muss.


## Relais

Sowohl Warteschlangen als auch Themen ermöglichen eine asynchrone unidirektionale Kommunikation über einen Broker. Der Verkehr fließt in nur eine Richtung, und es besteht keine direkte Verbindung zwischen Sendern und Empfängern. Aber was tun, wenn dies nicht genug ist? Angenommen, Ihre Anwendungen sollen Nachrichten sowohl senden als auch empfangen, oder sie möchten vielleicht eine direkte Verbindung zwischen ihnen herstellen, ohne einen Broker zum Speichern der Nachrichten zu verwenden. Um Szenarien wie dieses zu behandeln, bietet Service Bus Relais, wie [Abbildung 4](#Fig4) zeigt.

<a name="Fig4"></a>![Diagramm für Service Bus Relay][relay]
 
**Abbildung 4: Service Bus-Relais ermöglichen eine synchrone bidirektionale Kommunikation zwischen Anwendungen.**

Die naheliegende Frage zu Relais ist: Warum sollte ich eines verwenden? Selbst wenn ich keine Warteschlangen benötige, wieso sollten Anwendungen über einen Cloud-Dienst kommunizieren und nicht direkt miteinander interagieren? Die Antwort ist, dass eine direkte Kommunikation schwieriger sein kann, als Sie denken.

Nehmen wir an, Sie möchten zwei lokale Anwendungen miteinander verbinden, die beide in Rechenzentren Ihres Unternehmens laufen. Jede dieser Anwendungen befindet sich hinter einer Firewall, und wahrscheinlich nutzt jedes Rechenzentrum eine Netzwerkadressübersetzung (NAT). Die Firewall blockiert alle eingehenden Nachrichten auf fast allen Ports, und NAT bedeutet, dass die Computer, auf denen jede Anwendung läuft, keine feste IP-Adresse haben, auf die Sie von außerhalb des Rechenzentrums nicht direkt zugreifen können. Ohne zusätzliche Hilfe ist die Verbindung dieser Anwendungen über das öffentliche Internet problematisch.

Ein Service Bus-Relais leistet diese Hilfe. Zur bidirektionalen Kommunikation über ein Relais baut jede Anwendung eine ausgehende TCP-Verbindung mit Service Bus auf und hält diese offen. Jede Kommunikation zwischen den beiden Anwendungen läuft über diese beiden Verbindungen. Da jede Verbindung innerhalb des Rechenzentrums hergestellt wurde, erlaubt die Firewall, dass eingehender Datenverkehr an jede Anwendung gesendet wird, ohne dass neue Ports geöffnet werden müssen. Dieser Ansatz löst auch das NAT-Problem, da jede Anwendung während der gesamten Kommunikation einen konstanten Endpunkt in der Cloud aufweist. Durch den Datenaustausch über das Relais können die Anwendungen die Probleme vermeiden, die die Kommunikation erschweren können. 

Um Service Bus-Relais verwenden zu können, nutzen die Anwendungen Windows Communication Foundation (WCF). Service Bus bietet WCF-Anbindungen, die den Windows-Anwendungen die Interaktion über Relais erleichtern. Anwendungen, die WCF bereits verwenden, geben normalerweise nur eine dieser Anbindungen an und kommunizieren dann miteinander über ein Relais. Anders als bei Warteschlangen und Themen erfordert allerdings die Verwendung von Relais mit Nicht-Windows-Anwendungen, soweit sie überhaupt möglich ist, einigen Programmierungsaufwand, da dafür keine Standardbibliotheken existieren.

Anders als bei Warteschlangen und Themen werden Relais nicht explizit durch Anwendungen erstellt. Wenn eine Anwendung zum Empfangen von Nachrichten eine TCP-Verbindung mit Service Bus aufbaut, wird ein Relais automatisch erstellt. Wenn die Verbindung ausfällt, wird das Relais gelöscht. Damit eine Anwendung ein von einem bestimmten Listener erzeugtes Relais finden kann, bietet Service Bus eine Registry, die es Anwendungen erlaubt, ein bestimmtes Relais anhand seines Namens zu finden.

Relais sind die richtige Lösung, wenn direkte Kommunikation zwischen Anwendungen erforderlich ist. Denken Sie z. B. an ein Fluglinien-Reservierungssystem, das in einem lokalen Rechenzentrum läuft und auf das von Eincheck-Terminals, Mobilgeräten und anderen Computern zugegriffen wird. Anwendungen, die auf all diesen Systemen laufen, könnten auf Service Bus-Relais in der Cloud vertrauen, um immer dann zu kommunizieren, wenn sie gerade ausgeführt werden.

## Event Hubs

Event Hubs ist ein hoch skalierbares Erfassungssystem, das Millionen von Ereignissen pro Sekunde verarbeiten kann und so Ihrer Anwendung ermöglicht, die massiven Datenmengen zu verarbeiten und zu analysieren, die von verbundenen Geräten und Anwendungen erzeugt werden. Sie könnten z. B. ein Event Hub verwenden, um Live-Motorleistungsdaten für einen Fuhrpark zu erfassen. Nach der Erfassung in Event Hubs können Sie Daten über einen beliebigen Echtzeit-Analyseanbieter oder ein Speichercluster transformieren und speichern. Weitere Informationen zu Event Hubs finden Sie unter [Übersicht über Event Hubs][].

## Zusammenfassung

Das Verbinden von Anwendungen zählte schon immer zur Erstellung von Komplettlösungen und die Palette von Szenarien, in denen erforderlich ist, dass Anwendungen und Dienste miteinander kommunizieren, nimmt stetig zu, da Anwendungen und Geräte in zunehmenden Maße mit dem Internet verbunden sind. Service Bus stellt cloudbasierte Technologien bereit, um diese Aufgabe über Warteschlangen, Themen, Relais und Event Hubs zu bewältigen und damit diese wesentliche Funktion leichter und breiter verfügbar zu machen.

[svc-bus]: ./media/hybrid-solutions/SvcBus_01_architecture.png
[queues]: ./media/hybrid-solutions/SvcBus_02_queues.png
[topics-subs]: ./media/hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[relay]: ./media/hybrid-solutions/SvcBus_04_relay.png
[Event Hubs overview]: https://msdn.microsoft.com/library/azure/dn836025.aspx

<!--HONumber=47-->
