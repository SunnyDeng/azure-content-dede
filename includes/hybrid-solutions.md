#Azure Service Bus

Ob eine Anwendung oder ein Dienst in einer Cloud betrieben wird oder lokal installiert ist – sie bzw. er muss oft mit anderen Anwendungen oder Diensten interagieren. In Azure wird als umfangreich einsetzbare Lösung hierfür Service Bus bereitgestellt. Der vorliegende Artikel bietet einen Überblick über diese Technologie und beschreibt, wann Sie den Einsatz von Service Bus in Erwägung ziehen sollten.

## Service Bus – Grundlagen
Verschiedene Situationen erfordern verschiedene Kommunikationsarten. Manchmal ist die beste Lösung, Anwendungen Nachrichten über einfache Warteschlangen senden und empfangen zu lassen. In anderen Situationen ist eine normale Warteschlange nicht genug, sondern eine Warteschlange mit einem Veröffentlichungs- und Abonnementsmechanismus ist besser geeignet. Und in manchen Fällen wird einfach nur eine Verbindung zwischen den Anwendungen benötigt, und Warteschlangen sind nicht erforderlich. Service Bus bietet alle drei Optionen, sodass Ihre Anwendungen auf mehrere verschiedene Arten miteinander interagieren können.

Service Bus ist ein Clouddienst mit mehreren Mandanten. Das bedeutet, dass der Dienst von mehreren Benutzern gemeinsam genutzt wird. Jeder Benutzer, z. B. auch ein Anwendungsentwickler, erstellt einen *Namespace* und definiert dann die erforderlichen Kommunikationsmechanismen innerhalb dieses Namespace. [Abbildung 1](#Fig1) zeigt, wie dies aussieht.

<a name="Fig1"></a>![Diagramm von Azure-Servicebus][svc-bus]
 
**Abbildung 1: Service Bus stellt einen mehrinstanzenfähigen Dienst zum Verbinden von Anwendungen über die Cloud bereit.**

Innerhalb eines Namespace können Sie eine oder mehr Instanzen von vier verschiedenen Kommunikationsmechanismen verwenden, von denen jeder die Anwendungen auf andere Weise verbindet. Die Auswahlmöglichkeiten sind:

- *Warteschlangen* für eine unidirektionale Kommunikation. Jede Warteschlange agiert als Zwischenstufe (manchmal auch *Broker* genannt), die gesendete Nachrichten speichert, bis diese empfangen werden. Jede Nachricht wird von einem einzelnen Empfänger empfangen.
- *Themen* für die unidirektionale Kommunikation mithilfe von *Abonnements*, wobei ein einzelnes Thema mehrere Abonnements umfassen kann. Wie bei eine Warteschlange agiert ein Thema als Broker, aber jedes Abonnement kann optional Filter verwenden, um nur Nachrichten zu empfangen, die bestimmte Kriterien erfüllen.
- *Relays* für eine bidirektionale Kommunikation. Anders als Warteschlangen und Themen speichert ein Relay keine gesendeten Nachrichten – es ist kein Broker. Stattdessen leitet es sie an eine Zielanwendung weiter.
- *Event Hubs* für den Empfang großer Mengen an Ereignis- und Telemetriedaten in der Cloud, mit geringer Latenz und hoher Zuverlässigkeit.

Wenn Sie eine Warteschlange, ein Thema, ein Relay oder ein Event Hub erstellen, geben Sie diesem Objekt einen Namen. In Kombination mit der Bezeichnung Ihres Namespace ist dieser Name ein eindeutiger Bezeichner für das Objekt. Anwendungen können diesen Namen an Service Bus übermitteln und dann mithilfe der Warteschlange, des Themas, des Relays oder des Event Hubs miteinander kommunizieren.

Für die Kommunikation mit einem dieser Objekte können Windows-Anwendungen Windows Communication Foundation (WCF) nutzen. Für Warteschlangen, Themen und Event Hubs können Windows-Anwendungen auch eine über Service Bus definierte Messaging-API verwenden. Damit diese Objekte von Nicht-Windows-Anwendungen einfacher verwendet werden können, stellt Microsoft SDKs für Java, Node.js und andere Sprachen bereit. Sie können auch mithilfe von REST-APIs über HTTP auf Warteschlangen, Themen und Event Hubs zugreifen.

Es ist wichtig zu verstehen, dass, obwohl Service Bus selbst in einer Cloud (in den Azure-Rechenzentren von Microsoft) ausgeführt wird, die Anwendungen, die es verwenden, überall laufen können. Sie können Service Bus zum Verbinden von Anwendungen auf Azure oder innerhalb Ihres eigenen Datencenters verwenden. Sie können damit auch eine Anwendung, die auf Azure oder einer anderen Cloud-Plattform läuft, mit einer lokalen Anwendung oder mit Tablets oder Telefonen verbinden. Es ist auch möglich, Haushaltsgeräte, Sensoren und andere Vorrichtungen mit einer zentralen Anwendung oder miteinander zu verbinden. Service Bus ist ein generischer Kommunikationsmechanismus in der Cloud, auf den praktisch überall zugegriffen werden kann. Wie Sie es verwenden, hängt davon ab, was Ihre Anwendungen leisten müssen.


## Warteschlangen

Angenommen, Sie entscheiden sich dafür, zwei Anwendungen über eine Servicebus-Warteschlange miteinander zu verbinden. [Abbildung 2](#Fig2) illustriert diese Situation.

<a name="Fig2"></a>![Diagramm mit Service Bus-Warteschlangen][queues]
 
**Abbildung 2: Service Bus-Warteschlangen bieten eine asynchrone unidirektionale Kommunikation.**

Der Vorgang ist einfach: Ein Sender sendet eine Nachricht an eine Service Bus-Warteschlange, und ein Empfänger greift diese Nachricht zu einem späteren Zeitpunkt auf. Eine Warteschlange kann wie in [Abbildung 2](#Fig2) gezeigt einen einzelnen Empfänger aufweisen, oder mehrere Anwendungen können dieselbe Warteschlange auslesen. In der letzten Situation wird jede Nachricht nur von einem Empfänger gelesen. Für einen Multicastdienst sollten Sie stattdessen ein Thema verwenden.

Jede Nachricht besteht aus zwei Teilen: einem Satz von Eigenschaften (jede davon ist ein Paar aus Schlüssel und Wert) und einem binären Nachrichtentext. Die Art der Verwendung richtet sich danach, welche Aufgaben in der Anwendung ausgeführt werden sollen. Wenn eine Anwendung z. B. eine Nachricht über einen kürzlich erfolgten Verkauf sendet, kann die Nachricht die Eigenschaften *Verkäufer="Ava"* und *Menge=10000* enthalten. Der Nachrichtentext kann ein gescanntes Bild des unterzeichneten Verkaufsvertrags enthalten oder auch leer sein.

Ein Empfänger kann eine Nachricht von einer Service Bus-Warteschlange auf zwei verschiedene Arten lesen. Bei Verwendung der ersten Option, *ReceiveAndDelete*, wird eine Nachricht aus der Warteschlange entfernt und sofort gelöscht. Dies ist einfach, aber wenn der Empfänger ausfällt, bevor die Verarbeitung der Nachricht abgeschlossen ist, geht die Nachricht verloren. Da die Nachricht dann aus der Warteschlange entfernt worden ist, kann kein anderer Empfänger auf sie zugreifen.

Die zweiten Option, *PeekLock*, dient zur Lösung dieses Problems. Wie bei ReceiveAndDelete wird auch bei PeekLock eine gelesene Nachricht aus der Warteschlange entfernt. Die Nachricht wird allerdings nicht gelöscht, sondern gesperrt und für andere Empfänger unsichtbar gemacht. Anschließend wird auf eines von drei Ereignissen gewartet.

- Wenn der Empfänger die Nachricht erfolgreich verarbeitet, wird die Meldung *Complete* ausgegeben, und die Warteschlange löscht die Nachricht. 
- Wenn der Empfänger entscheidet, dass er die Nachricht nicht erfolgreich verarbeiten kann, wird die Meldung *Abandon* ausgegeben. In diesem Falle hebt die Warteschlange die Sperrung der Nachricht auf und macht sie für andere Empfänger verfügbar.
- Wenn der Empfänger innerhalb eines konfigurierbaren Zeitraums (standardmäßig 60 Sekunden) keine der beiden Meldungen ausgibt, nimmt die Warteschlange an, dass der Empfänger ausgefallen ist. In diesem Falle verhält sie sich so, als hätte der Empfänger die Meldung "Abandon" ausgegeben und macht die Nachricht für andere Empfänger verfügbar.

Beachten Sie, was geschieht: Dieselbe Nachricht kann zweimal ausgegeben werden, möglicherweise sogar an zwei verschiedene Empfänger. Anwendungen, die Service Bus-Warteschlangen verwenden, müssen darauf vorbereitet sein. Um die Erkennung von Duplikaten zu erleichtern, hat jede Nachricht eine eindeutige MessageID-Eigenschaft, die standardmäßig gleich bleibt, egal, wie oft die Nachricht von einer Warteschlange gelesen wird.

Warteschlangen sind in einigen Situationen sinnvoll. Sie ermöglichen eine Kommunikation zwischen Anwendungen auch dann, wenn beide zur selben Zeit ausgeführt werden, was bei Batch- und Mobilanwendungen sehr praktisch sein kann. Eine Warteschlange mit mehreren Empfängern bietet auch automatischen Lastenausgleich, da gesendete Nachrichten zwischen diesen Empfängern verteilt werden.


## Themen

So hilfreich sie generell auch sind – Warteschlangen sind nicht immer die beste Lösung. Manchmal sind Servicebus-Themen besser. [Abbildung 3](#Fig3) illustriert diesen Gedanken.

<a name="Fig3"></a>![Diagramm mit Service Bus-Themen und -Abonnements][topics-subs]
 
**Abbildung 3: Auf Grundlage des in einer Abonnementanwendung definierten Filters kann diese einige oder alle Nachrichten empfangen, die an ein Service Bus-Thema gesendet wurden.**

Ein Thema ist in mancherlei Weise einer Warteschlange ähnlich. Sender schicken Nachrichten an ein Thema auf dieselbe Weise, auf die sie Nachrichten an eine Warteschlange schicken, und diese Nachrichten sehen genauso aus wie bei den Warteschlangen. Der große Unterschied ist, dass bei Themen jede empfangende Anwendung durch Definition eines *Filters* ihr eigenes Abonnement einrichten kann.. Ein Abonnement sieht dann nur die Nachrichten, die auf diesen Filter passen. [Abbildung 3](#Fig3) zeigt z. B. einen Sender und ein Thema mit drei Abonnenten, jeder mit seinem eigenen Filter:

- Abonnent 1 empfängt nur Nachrichten, die die Eigenschaft *Verkäufer="Ava"* haben.
- Abonnent 2 empfängt Nachrichten, die die Eigenschaft *Verkäufer="Ruby"* und/oder eine Eigenschaft namens *Menge* haben, deren Wert größer ist als 100.000. Ruby könnte die Vertriebschefin sein und sowohl ihre eigenen Verkäufe sehen wollen als auch diejenigen anderer Verkäufer ab einer bestimmten Menge.
- Abonnent 3 hat seinen Filter auf *True* gesetzt, was bedeutet, dass er alle Nachrichten empfängt. Diese Anwendung könnte z. B. zum Verwalten eines Überwachungspfads zuständig sein. Daher muss sie alle Nachrichten sehen können.

Wie bei Warteschlangen können Abonnenten eines Themas Nachrichten entweder über ReceiveAndDelete oder über PeekLock lesen. Anders als bei Warteschlangen kann jedoch eine einzelne an ein Thema gesendete Nachricht von mehreren Abonnenten empfangen werden. Dieser Ansatz, der allgemein als *Veröffentlichen und Abonnieren* bezeichnet wird, ist sinnvoll, wenn mehrere Anwendungen an denselben Nachrichten interessiert sind. Durch Definition eines passenden Filters kann jeder Abonnent genau den Teil aus der Nachricht herausziehen, den er mitbekommen muss.


## Relays

Sowohl Warteschlangen als auch Themen ermöglichen eine asynchrone unidirektionale Kommunikation über einen Broker. Der Verkehr fließt in nur eine Richtung, und es besteht keine direkte Verbindung zwischen Sendern und Empfängern. Aber was tun, wenn dies nicht genug ist? Angenommen, Ihre Anwendungen sollen Nachrichten sowohl senden als auch empfangen, oder sie möchten vielleicht eine direkte Verbindung zwischen ihnen herstellen, ohne einen Broker zum Speichern der Nachrichten zu verwenden. Für diese Art von Szenario stellt Service Bus Relays zur Verfügung, wie in [Abbildung 4](#Fig4) gezeigt.

<a name="Fig4"></a>![Service Bus Relay – Diagramm][relay]
 
**Abbildung 4: Service Bus Relay ermöglicht eine synchrone bidirektionale Kommunikation zwischen Anwendungen.**

Die naheliegende Frage lautet: Wozu wird Service Bus Relay eingesetzt? Selbst wenn ich keine Warteschlangen benötige, wieso sollten Anwendungen über einen Clouddienst kommunizieren und nicht direkt miteinander interagieren? Die Antwort ist, dass eine direkte Kommunikation schwieriger sein kann, als Sie denken.

Nehmen wir an, Sie möchten zwei lokale Anwendungen miteinander verbinden, die beide in Datencentern Ihres Unternehmens laufen. Jede dieser Anwendungen befindet sich hinter einer Firewall, und wahrscheinlich nutzt jedes Rechenzentrum eine Netzwerkadressübersetzung (NAT). Die Firewall blockiert alle eingehenden Nachrichten auf fast allen Ports, und NAT bedeutet, dass die Computer, auf denen jede Anwendung läuft, keine feste IP-Adresse haben, auf die Sie von außerhalb des Rechenzentrums nicht direkt zugreifen können. Ohne zusätzliche Hilfe ist die Verbindung dieser Anwendungen über das öffentliche Internet problematisch.

Ein Service Bus Relay bietet diese Unterstützung. Zur bidirektionalen Kommunikation über ein Relay baut jede Anwendung eine ausgehende TCP-Verbindung mit Service Bus auf und hält diese offen. Jede Kommunikation zwischen den beiden Anwendungen läuft über diese beiden Verbindungen. Da jede Verbindung innerhalb des Rechenzentrums hergestellt wurde, erlaubt die Firewall, dass eingehender Datenverkehr an jede Anwendung gesendet wird, ohne dass neue Ports geöffnet werden müssen. Dieser Ansatz löst auch das NAT-Problem, da jede Anwendung während der gesamten Kommunikation einen konstanten Endpunkt in der Cloud aufweist. Durch den Datenaustausch über das Relay können die Anwendungen die Probleme vermeiden, die die Kommunikation erschweren können.

Um Service Bus Relay verwenden zu können, nutzen die Anwendungen Windows Communication Foundation (WCF). Service Bus bietet WCF-Anbindungen, die den Windows-Anwendungen die Interaktion über Relays erleichtern. Anwendungen, die WCF bereits verwenden, geben normalerweise nur eine dieser Anbindungen an und kommunizieren dann miteinander über ein Relay. Anders als bei Warteschlangen und Themen erfordert allerdings die Verwendung von Relays mit Nicht-Windows-Anwendungen, soweit sie überhaupt möglich ist, einigen Programmierungsaufwand, da dafür keine Standardbibliotheken existieren.

Anders als bei Warteschlangen und Themen werden Relays nicht explizit durch Anwendungen erstellt. Wenn eine Anwendung zum Empfangen von Nachrichten eine TCP-Verbindung mit Service Bus aufbaut, wird ein Relay automatisch erstellt. Wenn die Verbindung ausfällt, wird das Relay gelöscht. Damit eine Anwendung ein von einem bestimmten Listener erzeugtes Relays finden kann, bietet Service Bus eine Registry, die es Anwendungen erlaubt, ein bestimmtes Relay anhand seines Namens zu finden.

Relays sind die richtige Lösung, wenn direkte Kommunikation zwischen Anwendungen erforderlich ist. Denken Sie z. B. an ein Fluglinien-Reservierungssystem, das in einem lokalen Rechenzentrum läuft und auf das von Eincheck-Terminals, Mobilgeräten und anderen Computern zugegriffen wird. Anwendungen, die auf all diesen Systemen laufen, könnten auf Service Bus Relay in der Cloud vertrauen, um immer dann zu kommunizieren, wenn sie gerade ausgeführt werden.

## Event Hubs

Event Hubs ist ein hoch skalierbares Erfassungssystem, das Millionen von Ereignissen pro Sekunde verarbeiten kann und so Ihrer Anwendung ermöglicht, die massiven Datenmengen zu verarbeiten und zu analysieren, die von verbundenen Geräten und Anwendungen erzeugt werden. Sie könnten z. B. ein Event Hub verwenden, um Live-Motorleistungsdaten für einen Fuhrpark zu erfassen. Nach der Erfassung in Event Hubs können Sie Daten über einen beliebigen Echtzeitanalyseanbieter oder ein Speichercluster transformieren und speichern. Weitere Informationen zu Event Hubs finden Sie unter [Übersicht über Event Hubs][].

## Zusammenfassung

Das Verbinden von Anwendungen zählte schon immer zur Erstellung von Komplettlösungen und die Palette von Szenarien, in denen erforderlich ist, dass Anwendungen und Dienste miteinander kommunizieren, nimmt stetig zu, da Anwendungen und Geräte in zunehmenden Maße mit dem Internet verbunden sind. Service Bus stellt cloudbasierte Technologien bereit, um diese Aufgabe über Warteschlangen, Themen, Relays und Event Hubs zu bewältigen und damit diese wesentliche Funktion leichter und breiter verfügbar zu machen.

[svc-bus]: ./media/hybrid-solutions/SvcBus_01_architecture.png
[queues]: ./media/hybrid-solutions/SvcBus_02_queues.png
[topics-subs]: ./media/hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[relay]: ./media/hybrid-solutions/SvcBus_04_relay.png
[Übersicht über Event Hubs]: https://msdn.microsoft.com/library/azure/dn836025.aspx

<!---HONumber=July15_HO2-->