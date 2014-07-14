
# Azure-Servicebus

Ob Software in einer Cloud betrieben wird oder lokal installiert ist - sie muss oft mit anderer Software interagieren. Damit dies in allgemein nützlicher Weise geschieht, bietet Azure die Option Servicebus. In diesem Artikel betrachten wir diese Technologie und beschreiben, worum es sich dabei handelt und warum Sie sie gut gebrauchen könnten.

## Inhaltsverzeichnis

* [Servicebus - Grundlagen](#fundamentals)
* [Warteschlangen](#queues)
* [Themen](#topics)
* [Relais](#relays)

## <a name="fundamentals"></a>Servicebus - Grundlagen

Verschiedene Situationen erfordern verschiedene Kommunikationsarten. Manchmal ist die beste Lösung, Anwendungen Nachrichten über einfache Warteschlangen senden und empfangen zu lassen. In anderen Situationen ist eine normale Warteschlange nicht genug, sondern eine Warteschlange mit einem Veröffentlichungs- und Abonnementsmechanismus ist besser geeignet. Und in manchen Fällen wird einfach nur eine Verbindung zwischen den Anwendungen benötigt, und Warteschlangen sind nicht erforderlich. Service Bus bietet alle drei Optionen, sodass Ihre Anwendungen auf mehrere verschiedene Arten miteinander interagieren können.

Servicebus ist ein Clouddienst mit mehreren Mandanten. Das bedeutet, dass der Dienst von mehreren Benutzern gemeinsam genutzt wird. Jeder Benutzer, z. B. auch ein Anwendungsentwickler, erstellt einen *Namespace* und definiert dann die erforderlichen Kommunikationsmechanismen innerhalb dieses Namespace. [Abbildung 1](#Fig1) zeigt, wie dies aussieht.

<a name="Fig1"></a>![Diagramm von
Azure-Servicebus](./media/hybrid-solutions/SvcBus_01_architecture.png)

**Abbildung 1: Servicebus bietet einen Dienst mit mehreren Mandanten zur Verbindung von Anwendungen über die Cloud.**

Innerhalb eines Namespace können Sie eine oder mehr Instanzen von drei verschiedenen Kommunikationsmechanismen verwenden, von denen jeder die Anwendungen auf andere Weise verbindet. Die Auswahlmöglichkeiten sind:

* *Warteschlangen* für eine unidirektionale Kommunikation. Jede
  Warteschlange agiert als Zwischenstufe (manchmal auch *Broker*
  genannt), die gesendete Nachrichten speichert, bis diese empfangen
  werden.
* *Themen*, die eine unidirektionale Kommunikation über *Abonnements*
  anbieten. Wie bei einer Warteschlange agiert ein Thema als Broker,
  aber erlaubt jedem Abonnenten, nur Nachrichten zu sehen, die bestimmte
  Kriterien erfüllen.
* *Relais* für eine bidirektionale Kommunikation. Anders als
  Warteschlangen und Themen speichert ein Relais keine gesendeten
  Nachrichten - es ist kein Broker. Stattdessen leitet es sie an eine
  Zielanwendung weiter.

Wenn Sie eine Warteschlange, ein Thema oder ein Relais erstellen, geben Sie diesem Objekt einen Namen. In Kombination mit der Bezeichnung Ihres Namespace ist dieser Name ein eindeutiger Bezeichner für das Objekt. Anwendungen können diesen Namen an Servicebus übermitteln und dann mithilfe der Warteschlange, des Themas oder des Relais miteinander kommunizieren.

Für die Kommunikation mit einem dieser Objekte können Windows-Anwendungen Windows Communication Foundation (WCF) nutzen. Für Warteschlangen und Themen können Windows-Anwendungen auch eine über den Servicebus definierte Messaging-API verwenden. Auf Warteschlangen und Themen kann auch über HTTP zugegriffen werden, und für eine einfachere Verwendung mit Windows-Anwendungen bietet Microsoft SDKs für Java, Node js und andere Sprachen.

Es ist wichtig zu verstehen, dass, obwohl Servicebus selbst in einer Cloud (in den Azure-Datencentern von Microsoft) ausgeführt wird, die Anwendungen, die es verwenden, überall laufen können. Sie können Servicebus zum Verbinden von Anwendungen auf Azure oder innerhalb Ihres eigenen Datencenters verwenden. Sie können damit auch eine Anwendung, die auf Azure oder einer anderen Cloud-Plattform läuft, mit einer lokalen Anwendung oder mit Tablets oder Telefonen verbinden. Es ist auch möglich, Haushaltsgeräte, Sensoren und andere Vorrichtungen mit einer zentralen Anwendung oder miteinander zu verbinden. Servicebus ist ein generischer Kommunikationsmechanismus in der Cloud, auf den praktisch überall zugegriffen werden kann. Wie Sie es verwenden, hängt davon ab, was Ihre Anwendungen leisten müssen.

## <a name="queues"></a>Warteschlangen

Angenommen, Sie entscheiden sich dafür, zwei Anwendungen über eine Servicebus-Warteschlange miteinander zu verbinden. [Abbildung 2](#Fig2) illustriert diese Situation.

<a name="Fig2"></a>![Diagramm mit
Servicebus-Warteschlangen](./media/hybrid-solutions/SvcBus_02_queues.png)

**Abbildung 2: Servicebus-Warteschlangen für eine asynchrone unidirektionale Kommunikation.**

Der Prozess ist ganz einfach: Ein Sender sendet eine Nachricht an eine Servicebus-Warteschlange, und ein Empfänger greift diese Nachricht zu einem späteren Zeitpunkt auf. Eine Warteschlange kann wie in [Abbildung 2](#Fig2) gezeigt einen einzelnen Empfänger aufweisen, oder mehrere Anwendungen können dieselbe Warteschlange auslesen. In der zweiten Situation wird jede Nachricht normalerweise von nur einem Empfänger gelesen: Warteschlangen bieten keine Multicast-Dienste.

Jede Nachricht besteht aus zwei Teilen: einem Satz von Eigenschaften (jede davon ist ein Paar aus Schlüssel und Wert) und einem binären Nachrichtentext. Wie sie verwendet werden, hängt davon ab, was eine Anwendung zu tun versucht. Wenn eine Anwendung z. B. eine Nachricht über einen kürzlich erfolgten Verkauf sendet, kann die Nachricht die Eigenschaften *Verkäufer="Ava"* und *Menge=10000* haben. Der Nachrichtentext kann ein gescanntes Bild des unterzeichneten Verkaufsvertrags enthalten oder auch leer sein.

Ein Empfänger kann eine Nachricht von einem Servicebus auf zwei verschiedene Arten lesen. Die erste Option namens ReceiveAndDelete besteht darin, eine Nachricht aus der Warteschlange zu entfernen und sofort zu löschen. Dies ist einfach, aber wenn der Empfänger ausfällt, bevor die Verarbeitung der Nachricht abgeschlossen ist, geht die Nachricht verloren. Da die Nachricht dann aus der Warteschlange entfernt worden ist, kann kein anderer Empfänger auf sie zugreifen.

Die zweite Option, PeekLock, soll dieses Problem lösen. Wie bei ReceiveAndDelete wird auch bei PeekLock eine gelesene Nachricht aus der Warteschlange entfernt. Die Nachricht wird allerdings nicht gelöscht, sondern gesperrt und für andere Empfänger unsichtbar gemacht. Anschließend wird auf eines von drei Ereignissen gewartet.

* Wenn der Empfänger die Nachricht erfolgreich verarbeitet, wird die
  Meldung "Complete" ausgegeben, und die Warteschlange löscht die
  Nachricht.
* Wenn der Empfänger entscheidet, dass er die Nachricht nicht
  erfolgreich verarbeiten kann, wird die Meldung "Abandon" ausgegeben.
  In diesem Falle hebt die Warteschlange die Sperrung der Nachricht auf
  und macht sie für andere Empfänger verfügbar.
* Wenn der Empfänger innerhalb eines konfigurierbaren Zeitraums
  (standardmäßig 60 Sekunden) keine der beiden Meldungen ausgibt, nimmt
  die Warteschlange an, dass der Empfänger ausgefallen ist. In diesem
  Falle verhält sie sich so, als hätte der Empfänger die Meldung
  "Abandon" ausgegeben und macht die Nachricht für andere Empfänger
  verfügbar.

Bitte beachten Sie, was hier passieren kann: Dieselbe Nachricht kann zweimal ausgegeben werden, vielleicht sogar an zwei verschiedene Empfänger. Anwendungen, die Servicebus-Warteschlangen verwenden, müssen darauf vorbereitet sein. Um die Erkennung von Duplikaten zu erleichtern, hat jede Nachricht eine eindeutige MessageID, die standardmäßig gleich bleibt, egal, wie oft die Nachricht von einer Warteschlange gelesen wird.

Warteschlangen sind in einigen Situationen sinnvoll. Sie ermöglichen eine Kommunikation zwischen Anwendungen auch dann, wenn beide zur selben Zeit ausgeführt werden, was bei Batch- und Mobilanwendungen sehr praktisch sein kann. Eine Warteschlange mit mehreren Empfängern bietet auch automatischen Lastenausgleich, da gesendete Nachrichten zwischen diesen Empfängern verteilt werden.

## <a name="topics"></a>Themen

So hilfreich sie generell auch sind - Warteschlangen sind nicht immer die beste Lösung. Manchmal sind Servicebus-Themen besser. [Abbildung 3](#Fig3) illustriert diesen Gedanken.

<a name="Fig3"></a>![Diagramm mit Servicebus-Themen
und
-Abonnements](./media/hybrid-solutions/SvcBus_03_topicsandsubscriptions.png)

**Abbildung 3: Auf Grundlage des Filters, der in einer Abonnements-Anwendung definiert ist, kann diese einige oder alle Nachrichten empfangen, die an ein Servicebus-Thema gesendet wurden.**

Ein Thema ist in mancherlei Weise einer Warteschlange ähnlich. Sender schicken Nachrichten an ein Thema auf dieselbe Weise, auf die sie Nachrichten an eine Warteschlange schicken, und diese Nachrichten sehen genauso aus wie bei den Warteschlangen. Der große Unterschied ist, dass bei Themen jede empfangende Anwendung durch Definition eines *Filters* ihr eigenes Abonnement einrichten kann.. Ein Abonnement sieht dann nur die Nachrichten, die auf diesen Filter passen. [Abbildung 3](#Fig3) zeigt z. B. einen Sender und ein Thema mit drei Abonnenten, jeder mit seinem eigenen Filter:

* Abonnent 1 empfängt nur Nachrichten, die die Eigenschaft
  *Verkäufer="Ava"* haben.
* Abonnent 2 empfängt Nachrichten, die die Eigenschaft
  *Verkäufer="Ruby"* und/oder eine Eigenschaft namens *Menge* haben,
  deren Wert größer ist als 100.000. Ruby könnte die Vertriebschefin
  sein und sowohl ihre eigenen Verkäufe sehen wollen als auch diejenigen
  anderer Verkäufer ab einer bestimmten Menge.
* Abonnent 3 hat seinen Filter auf *True* gesetzt, was bedeutet, dass er
  alle Nachrichten empfängt. Diese Anwendung könnte z. B. für die
  regelmäßige Durchführung eines Audit-Trails verantwortlich und deshalb
  darauf angewiesen sein, jede Nachricht zu sehen.

Wie bei Warteschlangen können Abonnenten eines Themas Nachrichten entweder über ReceiveAndDelete oder über PeekLock lesen. Anders als bei Warteschlangen kann jedoch eine einzelne an ein Thema gesendete Nachricht von mehreren Abonnenten empfangen werden. Dieser Ansatz, der allgemein als *Veröffentlichen und Abonnieren* bezeichnet wird, ist sinnvoll, wenn mehrere Anwendungen an denselben Nachrichten interessiert sind. Durch Definition eines passenden Filters kann jeder Abonnent genau den Teil aus der Nachricht herausziehen, den er mitbekommen muss.

## <a name="relays"></a>Relais

Sowohl Warteschlangen als auch Themen ermöglichen eine asynchrone unidirektionale Kommunikation über einen Broker. Der Verkehr fließt in nur eine Richtung, und es besteht keine direkte Verbindung zwischen Sendern und Empfängern. Aber was tun, wenn dies nicht genug ist? Angenommen, Ihre Anwendungen sollen sowohl senden als auch empfangen, oder sie möchten vielleicht eine direkte Verbindung zwischen ihnen herstellen, ohne einen Ort dazwischen, an dem die Nachrichten gespeichert werden. Um Probleme wie dieses zu lösen, bietet Servicebus Relais, wie in [Abbildung 4](#Fig4) gezeigt.

<a name="Fig4"></a>![Diagramm eines
Servicebus-Relais](./media/hybrid-solutions/SvcBus_04_relay.png)

**Abbildung 4: Servicebus-Relais ermöglichen eine synchrone bidirektionale Kommunikation zwischen Anwendungen.**

Die naheliegende Frage über Relais ist: Warum sollte ich eines verwenden? Selbst wenn ich keine Warteschlangen benötige, wieso sollten Anwendungen über einen Clouddienst kommunizieren und nicht direkt miteinander interagieren? Die Antwort ist, dass eine direkte Kommunikation schwieriger sein kann, als Sie denken.

Nehmen wir an, Sie möchten zwei lokale Anwendungen miteinander verbinden, die beide in Datencentern Ihres Unternehmens laufen. Jede dieser Anwendungen befindet sich hinter einer Firewall, und wahrscheinlich nutzt jedes Rechenzentrum eine Netzwerkadressübersetzung (NAT). Die Firewall blockiert alle eingehenden Nachrichten auf fast
allen Ports, und NAT bedeutet, dass die Computer, auf denen jede Anwendung läuft, wahrscheinlich keine feste IP-Adresse haben. Ohne zusätzliche Hilfe ist die Verbindung dieser Anwendungen über das öffentliche Internet problematisch.

Ein Servicebus-Relais leistet diese Hilfe. Zur bidirektionalen Kommunikation über ein Relais baut jede Anwendung eine ausgehende TCP-Verbindung mit Servicebus auf und hält diese offen. Jede Kommunikation zwischen den beiden Anwendungen läuft über diese beiden Verbindungen. Da jede Verbindung innerhalb des Datencenters hergestellt wurde, erlaubt die Firewall, dass eingehender Datenverkehr an jede Anwendung über das Relais gesendet wird, ohne dass neue Ports geöffnet werden müssen. Dieser Ansatz löst auch das NAT-Problem, da jede Anwendung während der gesamten Kommunikation einen konstanten Endpunkt aufweist. Durch den Datenaustausch über das Relais können die Anwendungen die Probleme vermeiden, die die Kommunikation erschweren können.

Um Servicebus-Relais verwenden zu können, nutzen die Anwendungen Windows Communication Foundation (WCF). Servicebus bietet WCF-Anbindungen, die den Windows-Anwendungen die Interaktion über Relais erleichtern. Anwendungen, die WCF bereits verwenden, geben normalerweise nur eine dieser Anbindungen an und kommunizieren dann miteinander über ein Relais. Anders als bei Warteschlangen und Themen erfordert allerdings die Verwendung von Relais mit Nicht-Windows-Anwendungen, soweit sie überhaupt möglich ist, einigen Programmierungsaufwand, da dafür keine Standardbibliotheken existieren.

Anders als bei Warteschlangen und Themen werden Relais nicht explizit durch Anwendungen erstellt. Wenn eine Anwendung zum Empfangen von Nachrichten eine TCP-Verbindung mit Servicebus aufbaut, wird ein Relais automatisch erstellt. Wenn die Verbindung ausfällt, wird das Relais gelöscht. Damit eine Anwendung ein von einem bestimmten Listener erzeugtes Relais finden kann, bietet Servicebus eine Registry, die ein bestimmtes Relais immer anhand seines Namens auffindet.

Relais sind die richtige Lösung, wenn direkte Kommunikation erforderlich ist. Denken Sie z. B. an ein Fluglinien-Reservierungssystem, das in einem lokalen Datencenter läuft und auf das von Eincheck-Terminals, Mobilgeräten und anderen Computern zugegriffen wird. Anwendungen, die auf all diesen Systemen laufen, könnten auf Servicebus-Relais in der Cloud vertrauen, um immer dann zu kommunizieren, wenn sie gerade ausgeführt werden.

Die Verbindung von Anwendungen war immer ein Teil des Aufbaus kompletter Lösungen, und es ist kaum abzusehen, dass die damit zusammenhängenden Probleme jemals verschwinden. Durch Bereitstellung Cloud-basierter Technologien zu diesem Zweck strebt Servicebus an, diese grundlegende Funktion leichter und breiter verfügbar zu machen.