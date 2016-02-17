<properties 
   pageTitle="Azure- und Service Bus-Warteschlangen – Vergleich und Gegenüberstellung | Microsoft Azure"
   description="Analysiert die Unterschiede und Gemeinsamkeiten zwischen den beiden zurzeit von Azure angebotenen Warteschlangentypen."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="11/18/2015"
   ms.author="sethm" />

# Azure-Warteschlangen und Service Bus-Warteschlangen – Vergleich und Gegenüberstellung.

In diesem Artikel werden die Unterschiede und Ähnlichkeiten zwischen den beiden Warteschlangentypen analysiert, die zurzeit von Microsoft Azure angeboten werden: Azure-Warteschlangen und Service Bus-Warteschlangen. Mithilfe dieser Informationen können Sie die beiden Technologien vergleichen und abgrenzen und sind in der Lage, besser informierte Entscheidungen darüber zu treffen, welche Lösung Ihre Anforderungen am besten erfüllt.

## Einführung

Microsoft Azure unterstützt zwei Typen von Warteschlangen: **Azure-Warteschlangen** und **Service Bus-Warteschlangen**.

**Azure-Warteschlangen** gehören zur Infrastruktur von [Azure Storage](https://azure.microsoft.com/services/storage/). Sie verfügen über eine einfache REST-basierte Get-/Put-/Peek-Oberfläche, die ein zuverlässiges und konsequentes Messaging innerhalb von Diensten und zwischen diesen gewährleistet.

**Service Bus-Warteschlangen** gehören der weiter gefassten [Azure-Messaging](https://azure.microsoft.com/services/service-bus/)-Infrastruktur an, die neben Warteschlangen auch Veröffentlichungen/Abonnements, Remotebereitstellung von Webdiensten und Integrationsmuster unterstützt. Weitere Informationen zu Service Bus-Warteschlangen, Themen/Abonnements und Relays finden Sie unter [Übersicht über Service Bus-Messaging](service-bus-messaging-overview.md).

Obwohl beide Warteschlangentechnologien parallel vorhanden sind, wurden Azure-Warteschlangen zuerst als dedizierter Warteschlangenspeicher-Mechanismus eingeführt, der auf den Azure-Speicherdiensten aufsetzt. Service Bus-Warteschlangen basieren auf der umfassenderen Infrastruktur für "Brokermessaging". Diese dient der Integration von Anwendungen oder Anwendungskomponenten, die sich auf mehrere Kommunikationsprotokolle, Datenverträge, vertrauenswürdige Domänen und/oder Netzwerkumgebungen erstrecken.

In diesem Artikel werden die beiden Warteschlangentechnologien von Azure verglichen. Dabei wird erörtert, welche Unterschiede in Bezug auf Verhalten und Implementierung der jeweils bereitgestellten Leistungsmerkmale bestehen. Darüber hinaus enthält der Artikel Hinweise dazu, welche Eigenschaften für die jeweiligen Anwendungsentwicklungsanforderungen am besten geeignet sind.

## Überlegungen zur Wahl der richtigen Technologie

Azure-Warteschlangen und Service Bus-Warteschlangen sind Implementierungen des Message Queuing-Diensts, der zurzeit von Microsoft Azure angeboten wird. Die beiden Implementierungen weisen geringfügig unterschiedliche Funktionsmerkmale auf. Das heißt, Sie können sich für eine entscheiden oder beide verwenden. Welche Sie auswählen, hängt von den Anforderungen Ihrer spezifischen Lösung bzw. dem zu lösenden geschäftlichen oder technischen Problem ab.

Bei der Wahl der Warteschlangentechnologie, die für eine bestimmte Lösung am besten geeignet ist, sollten sich Lösungsarchitekten und -entwickler an die folgenden Empfehlungen halten. Weitere Informationen finden Sie im nächsten Abschnitt.

Als Lösungsarchitekt/-entwickler sollten Sie die **Verwendung von Azure-Warteschlangen** in den folgenden Situationen in Betracht ziehen:

- Von der Anwendung müssen Nachrichten mit einer Kapazität von über 80 GB in einer Warteschlange gespeichert werden, wobei die Lebensdauer der Nachrichten unter sieben Tagen liegt.

- Der Verarbeitungsfortschritt einer Nachricht soll von der Anwendung innerhalb der Warteschlange nachverfolgt werden. Dies ist beim Absturz eines Workerprozesses, von dem die Nachricht verarbeitet wird, von Vorteil. Ein nachfolgender Workerprozess kann diese Informationen verwenden, um die Verarbeitung an der Stelle, an der sich der Absturz ereignet hat, fortzusetzen.

- Sie benötigen serverseitige Protokolle aller Transaktionen, die für die Warteschlangen ausgeführt wurden.

Als Lösungsarchitekt/-entwickler sollten Sie die **Verwendung von Service Bus-Warteschlangen** in den folgenden Situationen in Betracht ziehen:

- Die Lösung muss in der Lage sein, Nachrichten ohne Abruf der Warteschlange empfangen zu können. Bei Service Bus kann dies erreicht werden, indem ein Empfangsvorgang mit langem Abrufintervall mithilfe der auf TCP basierenden Protokolle verwendet wird, die Service Bus unterstützen.

- Die Lösung erfordert von der Warteschlange die Zustellung nach dem FIFO-Prinzip (First-In-First-Out).

- Sie wünschen eine symmetrische Benutzeroberfläche in Azure- und unter Windows Server (private Cloud). Weitere Informationen finden Sie unter [Service Bus for Windows Server](https://msdn.microsoft.com/library/dn282144.aspx) (in englischer Sprache).

- Die Lösung muss in der Lage sein, die automatische Duplikaterkennung zu unterstützen.

- Sie wünschen eine Anwendung, die Nachrichten als parallele Datenströme mit langer Ausführungsdauer verarbeitet (Nachrichten werden mithilfe der [SessionId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx)-Eigenschaft für die Nachricht einem Datenstrom zugeordnet). In diesem Modell konkurriert jeder Knoten in der verarbeitenden Anwendung um Datenströme und nicht um Nachrichten. Wenn ein Datenstrom an einen verarbeitenden Knoten übergeben wird, kann der Knoten den Status des Anwendungsdatenstroms mithilfe von Transaktionen untersuchen.

- Beim Senden oder Empfangen mehrerer Nachrichten über eine Warteschlange muss sich die Lösung durch Transaktionsfähigkeit und Unteilbarkeit auszeichnen.

- Die Gültigkeitsdauer (Time To Live, TTL) der anwendungsspezifischen Arbeitsauslastung kann sieben Tage überschreiten.

- Die Anwendung verarbeitet Nachrichten, die zwar 64 KB überschreiten können, die Grenze von 256 KB aber wahrscheinlich nicht erreichen werden.

- Sie müssen ein rollenbasiertes Zugriffsmodell für Warteschlangen bereitstellen, die Absendern und Empfängern unterschiedliche Rechte/Berechtigungen gewähren.

- Die Warteschlangengröße überschreitet 80 GB nicht.

- Sie möchten den auf Standards basierenden AMQP 1.0-Messagingbroker verwenden. Weitere Informationen zu AMQP finden Sie unter [Service Bus AMQP – Übersicht](service-bus-amqp-overview.md).

- Sie können schließlich eine Migration von der warteschlangenbasierten Punkt-zu-Punkt-Kommunikation zu einem Nachrichtenaustauschmodell in Erwägung ziehen, um zusätzliche Empfänger (Abonnenten) nahtlos zu integrieren, von denen jeder eine Kopie einiger oder aller an die Warteschlange gesendeten Nachrichten erhält. Der letzte Punkt bezieht sich auf die Veröffentlichungs-/Abonnementfunktion, die von Service Bus selbst bereitgestellt wird.

- Ihre Messaginglösung muss in der Lage sein, die "At-Most-Once"-Zustellung zu garantieren, ohne dass zusätzliche Infrastrukturkomponenten implementiert werden müssen.

- Sie möchten in der Lage sein, Nachrichtenbatches zu veröffentlichen und zu verarbeiten.

- Eine vollständige Integration in den Windows Communication Foundation (WCF)-Kommunikationsstapel in .NET Framework ist erforderlich.

## Vergleich von Azure-Warteschlangen und Service Bus-Warteschlangen

In den Tabellen der folgenden Abschnitte sind die Warteschlangenfunktionen logisch gruppiert. Sie erkennen also auf einen Blick, welche Funktionen in Azure-Warteschlangen und Service Bus-Warteschlangen verfügbar sind.

## Grundlegende Funktionen

In diesem Abschnitt werden einige der grundlegenden Warteschlangenfunktionen verglichen, die von Azure-Warteschlangen und Azure Service Bus-Warteschlangen bereitgestellt werden.

|Vergleichskriterien|Azure-Warteschlangen|Service Bus-Warteschlangen|
|---|---|---|
|Reihenfolgengarantie|**Nein** <br/><br>Weitere Informationen finden Sie in der ersten Anmerkung im Abschnitt "Weitere Informationen".</br>|**Ja – First-In-First-Out (FIFO)**<br/><br>(durch Verwendung von Messagingsitzungen)|
|Zustellungsgarantie|**At-Least-Once**|**At-Least-Once**<br/><br/>**At-Most-Once**|
|Unterstützung von Transaktionen|**Nein**|**Ja**<br/><br/>(durch Verwendung lokaler Transaktionen)|
|Empfangsverhalten|**Nicht blockierend**<br/><br/>(wird sofort beendet, wenn keine neue Nachricht gefunden wird)|**Blockieren mit/ohne Timeout**<br/><br/>(bietet ein langes Abrufintervall bzw. die ["Comet-Technik"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Nicht blockierend**<br/><br/>(nur durch Verwendung .NET-verwalteter APIs)|
|API im Pushstil|**Nein**|**Ja**<br/><br/>[OnMessage](https://msdn.microsoft.com/library/azure/jj908682.aspx)- und **OnMessage**-Sitzungs-.NET-API.|
|Empfangsmodus|**Peek & Lease**|**Peek & Lock**<br/><br/>**Receive & Delete**|
|Exklusiver Zugriffsmodus|**Leasebasiert**|**Sperrenbasiert**|
|Lease-/Sperrdauer|**30 Sekunden (Standard)**<br/><br/>**7 Tage (maximal)** (Sie können eine Nachrichtenlease mithilfe der [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx)-API erneuern oder freigeben.)|**60 Sekunden (Standard)**<br/><br/>Eine Nachrichtensperre kann mit der [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx)-API verlängert werden.|
|Lease-/Sperrengranularität|**Nachrichtenebene**<br/><br/>(Jede Nachricht kann einen anderen Timeoutwert aufweisen, den Sie nach Bedarf bei der Verarbeitung der Nachricht mithilfe der [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx)-API aktualisieren können.)|**Warteschlangenebene**<br/><br/>(Jede Warteschlange weist eine Sperrengranularität auf, die auf alle enthaltenen Nachrichten angewendet wird; die Sperre kann jedoch mit der [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx)-API verlängert werden.)|
|Batchempfang|**Ja**<br/><br/>(explizit angebende Nachrichtenanzahl beim Abrufen von Nachrichten, bis maximal 32 Nachrichten)|**Ja**<br/><br/>(implizites Aktivieren einer PreFetch-Eigenschaft oder explizites Aktivieren durch Verwendung von Transaktionen)|
|Batchversand|**Nein**|**Ja**<br/><br/>(durch Verwendung von Transaktionen oder clientseitiger Batchverarbeitung)|

### Zusätzliche Informationen

- Für Nachrichten in Azure-Warteschlangen gilt normalerweise das FIFO-Prinzip, manchmal kann jedoch die Reihenfolge auch falsch sein, z. B. wenn die Sichtbarkeitstimeoutdauer der Nachricht abläuft (etwa als Ergebnis des Absturzes einer Clientanwendung während der Verarbeitung). Wenn das Sichtbarkeitstimeout abläuft, wird die Nachricht für die Warteschlange erneut sichtbar, damit sie von einem anderen Worker aus der Warteschlange entnommen werden kann. Zu diesem Zeitpunkt wird die erneut sichtbare Nachricht ggf. hinter eine Nachricht in der Warteschlange platziert (um erneut aus dieser entnommen zu werden), die ursprünglich nach ihr in die Warteschlange gestellt wurde.

- Wenn Sie bereits Azure Storage-Blobs oder -Tabellen verwenden und mit der Verwendung von Warteschlangen beginnen, werden 99,9 % Verfügbarkeit garantiert. Wenn Sie Blobs oder Tabellen mit Service Bus-Warteschlangen verwenden, ist die Verfügbarkeit geringer.

- Das garantierte FIFO-Prinzip in Service Bus-Warteschlangen erfordert die Verwendung von Messagingsitzungen. Wenn die Anwendung bei der Verarbeitung einer im **Peek & Lock**-Modus empfangenen Nachricht abstürzt, startet die Anwendung, sobald ein Warteschlangenempfänger das nächste Mal eine Messagingsitzung akzeptiert, mit der Fehlernachricht, nachdem die zugehörige Gültigkeitsdauer (TTL) abgelaufen ist.

- Azure-Warteschlangen unterstützen Standardwarteschlangenszenarios, z. B. das Abkoppeln von Anwendungskomponenten zur Erhöhung von Skalierbarkeit und Fehlertoleranz, Lastenausgleich und das Erstellen von Prozessworkflows.

- Service Bus-Warteschlangen unterstützen die *At-Least-Once*-Zustellungsgarantie. Darüber hinaus kann die *At-Most-Once*-Semantik unterstützt werden, indem mithilfe des Sitzungszustands der Anwendungszustand gespeichert wird und indem mithilfe von Transaktionen Nachrichten automatisch empfangen werden und der Sitzungszustand aktualisiert wird. Der Azure Workflow-Dienst verwendet dieses Verfahren, um die At-Most-Once-Zustellung zu garantieren.

- Azure-Warteschlangen bieten ein einheitliches und konsistentes Programmiermodell für Warteschlangen, Tabellen und Blobs – für Entwickler und für Betriebsteams.

- Service Bus-Warteschlangen unterstützen lokale Transaktionen im Kontext einer einzelnen Warteschlange.

- Der Von Service Bus unterstützte *Receive & Delete*-Modus bietet die Möglichkeit, die Anzahl der Übermittlungsvorgänge (und damit verbundene Gebühren) auf Kosten einer verminderten Zustellungssicherheit zu reduzieren.

- Azure-Warteschlangen stellen ein Leasingprinzip bereit, über das die Leasedauer für Nachrichten verlängert werden kann. Auf diese Weise können die Workerprozesse eine kurze Leasedauer für Nachrichten beibehalten. Sollte ein Workerprozess abstürzen, kann die Nachricht folglich von einem anderen Workerprozess schnell verarbeitet werden. Ein Workerprozess kann darüber hinaus die Leasedauer für eine Nachricht verlängern, wenn deren Verarbeitungszeit über die aktuelle Leasedauer hinausgeht.

- Azure-Warteschlangen verfügen über ein Sichtbarkeitstimeout, das Sie festlegen können, wenn eine Nachricht der Warteschlange hinzugefügt bzw. aus dieser entfernt wird. Außerdem können Sie eine Nachricht anhand verschiedener Leasewerte zur Laufzeit aktualisieren und unterschiedliche Werte für mehrere Nachrichten aktualisieren, die sich in derselben Warteschlange befinden. Service Bus-Sperrtimeouts sind in den Warteschlangenmetadaten definiert. Sie können die Sperre jedoch verlängern, indem Sie die [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx)-Methode aufrufen.

- Das maximale Timeout für das Blockieren einer empfangenen Nachricht in Service Bus-Warteschlangen beträgt 24 Tage. REST-basierte Timeouts verfügen jedoch über einen maximalen Wert von 55 Sekunden.

- Mithilfe der clientseitigen Batchverarbeitung von Service Bus kann ein Warteschlangenclient mehrere Nachrichten als Batch in einen einzelnen Sendevorgang einfügen. Die Batchverarbeitung ist nur für asynchrone Sendevorgänge verfügbar.

- Durch Features wie etwa die Obergrenze von 200 TB für Azure-Warteschlangen (bzw. ein höherer Wert, wenn Sie Konten virtualisieren) und eine uneingeschränkte Anzahl von Warteschlangen ist die Anwendung eine ideale Plattform für SaaS-Anbieter.

- Azure-Warteschlangen bieten einen flexiblen und leistungsstarken delegierten Zugriffssteuerungsmechanismus.

## Erweiterte Funktionen

In diesem Abschnitt werden die von Azure-Warteschlangen und Service Bus-Warteschlangen bereitgestellten erweiterten Funktionen verglichen.

|Vergleichskriterien|Azure-Warteschlangen|Service Bus-Warteschlangen|
|---|---|---|
|Zeitgesteuerte Zustellung|**Ja**|**Ja**|
|Automatisch generierte unzustellbare Nachrichten|**Nein**|**Ja**|
|Vergrößern des TTL-Werts einer Warteschlange|**Ja**<br/><br/>(über direktes Update des Sichtbarkeitstimeouts)|**Ja**<br/><br/>(über eine dedizierte API-Funktion bereitgestellt)|
|Unterstützung für nicht verarbeitbare Nachrichten|**Ja**|**Ja**|
|Direktes Update|**Ja**|**Ja**|
|Serverseitiges Transaktionsprotokoll|**Ja**|**Nein**|
|Speichermetrik|**Ja**<br/><br/>**Minutenmetriken**: stellen in Echtzeit Metriken für die Verfügbarkeit, TPS, Anzahl der API-Aufrufe, Fehleranzahl usw. zur Verfügung (aggregiert pro Minute und gemeldet innerhalb weniger Minuten nach Ereigniseintritt in der Produktion). Weitere Informationen finden Sie unter [Informationen zu Metriken der Speicheranalyse](https://msdn.microsoft.com/library/azure/hh343258.aspx).|**Ja**<br/><br/>(Massenabfragen durch Aufrufen von [GetQueues](https://msdn.microsoft.com/library/azure/hh293128.aspx))|
|Zustandsverwaltung|**Nein**|**Ja**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx)|
|Automatische Nachrichtenweiterleitung|**Nein**|**Ja**|
|Warteschlangeninhalt endgültig löschen|**Ja**|**Nein**|
|Nachrichtengruppen|**Nein**|**Ja**<br/><br/>(durch Verwendung von Messagingsitzungen)|
|Anwendungszustand pro Nachrichtengruppe|**Nein**|**Ja**|
|Duplikaterkennung|**Nein**|**Ja**<br/><br/>(auf der Absenderseite konfigurierbar)|
|WCF-Integration|**Nein**|**Ja**<br/><br/>(stellt direkt einsatzbereite WCF-Bindungen bereit)|
|WF-Integration|**Benutzerdefiniert**<br/><br/>(erfordert das Erstellen einer benutzerdefinierten WF-Aktivität)|**Systemeigenes Format**<br/><br/>(stellt sofort einsetzbare WF-Aktivitäten bereit)|
|Durchsuchen von Nachrichtengruppen|**Nein**|**Ja**|
|Abrufen von Nachrichtensitzungen anhand der ID|**Nein**|**Ja**|

### Zusätzliche Informationen

- Beide Warteschlangentechnologien ermöglichen das Planen der Zustellung einer Nachricht zu einem späteren Zeitpunkt.

- Die automatische Warteschlangenweiterleitung ermöglicht, dass Tausende von Warteschlangen ihre Nachrichten automatisch an eine einzelne Warteschlange weiterleiten, aus der die empfangene Anwendung die Nachricht abruft. Sie können diesen Mechanismus verwenden, um Sicherheit zu erzielen, den Datenfluss zu steuern und Speicher zwischen den einzelnen Nachrichtenverlegern zu isolieren.

- Azure-Warteschlangen bieten Unterstützung zum Aktualisieren von Nachrichteninhalten. Sie können diese Funktionen verwenden, um Zustandsinformationen und inkrementelle Statusupdates in der Nachricht beizubehalten, sodass sie vom letzten bekannten Prüfpunkt statt von Anfang an verarbeitet wird. Mit Service Bus-Warteschlangen können Sie das gleiche Szenario mithilfe von Nachrichtensitzungen aktivieren. Sitzungen ermöglichen das Speichern und Abrufen des Anwendungsverarbeitungszustands (mithilfe von [SetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx) und [GetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx)).

- Unzustellbare Nachrichten, die nur von Service Bus-Warteschlangen unterstützt werden, leisten wertvolle Hilfe beim Isolieren von Nachrichten, die von der Empfangsanwendung nicht erfolgreich verarbeitet werden können bzw. die ihr Ziel aufgrund einer abgelaufenen TTL-Eigenschaft (Time-To-Live) nicht erreichen können. Der TTL-Wert gibt an, wie lange eine Nachricht in der Warteschlange verbleibt. Bei Service Bus wird die Nachricht in eine bestimmte Warteschlange mit dem Namen "$DeadLetterQueue" verschoben, sobald die Gültigkeitsdauer abläuft.

- Zur Suche nach nicht verarbeitbaren Nachrichten in Azure-Warteschlangen überprüft die Anwendung die DequeueCount-Eigenschaft der Nachricht, wenn eine Nachricht aus der Warteschlange entfernt wird. Wenn DequeueCount über einem angegebenen Schwellenwert liegt, verschiebt die Anwendung die Nachricht in eine von der Anwendung definierte "Warteschlange für unzustellbare Nachrichten".

- Bei Azure-Warteschlangen können Sie ein ausführliches Protokoll aller für die Warteschlange ausgeführten Transaktionen sowie aggregierte Metriken abrufen. Beide erleichtern das Debuggen und verdeutlichen, wie Azure-Warteschlangen von der Anwendung verwendet werden. Weiter dienen diese Informationen dazu, die Anwendungsleistung zu optimieren und die Kosten für die Verwendung von Warteschlangen zu senken.

- Das von Service Bus unterstützte Konzept "Nachrichtensitzung" macht es möglich, dass Nachrichten, die einer bestimmten logischen Gruppe angehören, einem vorgegebenen Empfänger zugeordnet werden. Auf diese Weise entsteht eine sitzungsähnliche Affinität zwischen Nachrichten und den betreffenden Empfängern. Sie können diese erweiterte Funktionalität in Service Bus aktivieren, indem Sie die [SessionID](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx)-Eigenschaft für eine Nachricht festlegen. Empfänger können dann auf eine bestimmte Sitzungs-ID lauschen und Nachrichten empfangen, die den angegebenen Sitzungsbezeichner gemeinsam haben.

- Die von Service Bus-Warteschlangen unterstützte Duplikaterkennung entfernt gemäß dem Wert der [MessageID](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx)-Eigenschaft automatisch doppelt vorhandene Nachrichten, die an eine Warteschlange bzw. ein Thema gesendet wurden.

## Kapazität und Kontingente

In diesem Abschnitt werden Azure-Warteschlangen und Service Bus-Warteschlangen im Hinblick auf die Kapazität und ggf. gültige Kontingente verglichen.

|Vergleichskriterien|Azure-Warteschlangen|Service Bus-Warteschlangen|
|---|---|---|
|Maximale Warteschlangengröße|**200 TB**<br/><br/>(beschränkt auf die Kapazität eines einzelnen Speicherkontos)|**1–80 GB**<br/><br/>(wird bei Erstellung einer Warteschlange und dem [Aktivieren von Partitionierung](service-bus-partitioning.md) definiert – weitere Informationen finden Sie im Abschnitt "Zusätzliche Informationen")|
|Maximale Nachrichtengröße|**64 KB**<br/><br/>(48 KB bei Verwendung der **Base64**-Codierung)<br/><br/>Azure unterstützt große Nachrichten, indem Warteschlangen und Blobs kombiniert werden – in diesem Fall können bis zu 200 GB für ein einzelnes Element in der Warteschlange gespeichert werden.|**256 KB**<br/><br/>(Einschließlich Header und Text. Die maximale Headergröße beträgt 64 KB.)|
|Maximaler TTL-Wert der Nachricht|**7 Tage**|**Unbegrenzt**|
|Maximale Anzahl von Warteschlangen|**Unbegrenzt**|**10.000**<br/><br/>(pro Dienstnamespace, kann jedoch erhöht werden)|
|Maximale Anzahl gleichzeitiger Clients|**Unbegrenzt**|**Unbegrenzt**<br/><br/>(Die Beschränkung auf 100 gleichzeitige Verbindungen gilt nur für die Kommunikation über das TCP-Protokoll.)|

### Zusätzliche Informationen

- In Service Bus werden Grenzwerte für die Warteschlangengröße durchgesetzt. Die maximale Warteschlangengröße wird bei der Erstellung der Warteschlange angegeben und kann einen Wert zwischen 1 und 80 GB aufweisen. Wenn der bei der Erstellung der Warteschlange festgelegte Größenwert erreicht ist, werden zusätzlich eingehende Nachrichten abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. Weitere Informationen zu Kontingenten in Service Bus finden Sie unter [Service Bus-Kontingente](service-bus-quotas.md).

- Sie können Service Bus-Warteschlangen in Größen von 1, 2, 3, 4 oder 5 GB erstellen (die Standardgröße ist 1 GB). Bei aktivierter Partitionierung (Standardeinstellung) erstellt Service Bus 16 Partitionen für jedes angegebene GB. Wenn Sie also eine Warteschlange mit einer Größe von 5 GB erstellen, beträgt die maximale Warteschlangengröße bei 16 Partitionen 5*16 = 80 GB. Die maximale Größe der partitionierten Warteschlange oder des partitionierten Themas wird im zugehörigen Eintrag im [klassischen Azure-Portal][] angezeigt.

- Wenn der Inhalt der Nachricht bei Azure-Warteschlangen nicht XML-sicher ist, muss er mit **Base64** codiert werden. Wenn Sie die Nachricht mit **Base64** codieren, darf die Benutzernutzlast statt 64 KB nur 48 KB betragen.

- Bei Service Bus-Warteschlangen besteht jede in einer Warteschlange gespeicherte Nachricht aus zwei Teilen: einem Header und einem Text. Die Gesamtgröße der Nachricht darf 256 KB nicht überschreiten.

- Wenn Clients über das TCP-Protokoll mit Service Bus-Warteschlangen kommunizieren, ist die maximale Anzahl gleichzeitiger Verbindungen mit einer einzelnen Service Bus-Warteschlange auf 100 beschränkt. Diese Anzahl wird zwischen Absendern und Empfängern aufgeteilt. Wenn dieses Kontingent erreicht wird, werden nachfolgende Anforderungen für zusätzliche Verbindungen abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. Diese Begrenzung gilt nicht für Clients, die über die REST-basierte API eine Verbindung mit Warteschlangen herstellen.

- Wenn Sie mehr als 10.000 Warteschlangen in einem einzelnen Service Bus-Dienstnamespace benötigen, können Sie sich mit dem Azure-Supportteam in Verbindung setzen und eine Erhöhung des Werts anfordern. Um für Service Bus auf mehr als 10.000 Warteschlangen zu skalieren, können Sie mithilfe des [klassischen Azure-Portals][] auch zusätzliche Namespaces erstellen.

## Verwaltung und Abläufe

In diesem Abschnitt werden die von Azure-Warteschlangen und Service Bus-Warteschlangen bereitgestellten Verwaltungsfunktionen verglichen.

|Vergleichskriterien|Azure-Warteschlangen|Service Bus-Warteschlangen|
|---|---|---|
|Verwaltungsprotokoll|**REST über HTTP/HTTPS**|**REST über HTTPS**|
|Laufzeitprotokoll|**REST über HTTP/HTTPS**|**REST über HTTPS**<br/><br/>** AMQP 1.0 Standard (TCP mit TLS)**| 
|. NET-verwaltete API|**Ja**<br/><br/>(.NET verwaltete Speicherclient-API)|**Ja**<br/><br/>(.NET-verwaltete API für Brokermessaging)|
|Systemeigenes C++|**Ja**|**Nein**|
|Java-API|**Ja**|**Ja**|
|PHP-API|**Ja**|**Ja**|
|Node.js-API|**Ja**|**Ja**|
|Unterstützung beliebiger Metadaten|**Ja**|**Nein**|
|Benennungsregeln für Warteschlangen|**Bis zu 63 Zeichen**<br/><br/>(Warteschlangennamen müssen in Kleinbuchstaben geschrieben sein.)|**Bis zu 260 Zeichen**<br/><br/>(Bei Warteschlangennamen wird die Groß-/Kleinschreibung unterschieden.)|
|Funktion zum Abrufen der Warteschlangenlänge|**Ja**<br/><br/>(ungefährer Wert, wenn Nachrichten nach dem TTL-Wert ablaufen, ohne gelöscht zu werden)|**Ja**<br/><br/>(genau, Zeitpunktwert)|
|Peek-Funktion|**Ja**|**Ja**|

### Zusätzliche Informationen

- Azure-Warteschlangen unterstützen beliebige Attribute, die auf die Warteschlangenbeschreibung angewendet werden können, in Form von Name-Wert-Paaren.

- Beide Warteschlangentechnologien bieten außerdem die Möglichkeit, einen Blick in eine Nachricht zu werfen, ohne sie zu sperren. Dies kann bei der Implementierung eines Explorer-/Browsertools für die Warteschlange hilfreich sein.

- Die verwalteten .NET-APIs für Brokermessaging von Service Bus nutzen im Vergleich zu "REST über HTTP" Vollduplex-TCP-Verbindungen zur Leistungsoptimierung und unterstützen das AMQP 1.0-Standardprotokoll.

- Azure-Warteschlangennamen dürfen 3 bis 63 Zeichen lang sein und Kleinbuchstaben, Ziffern und Bindestriche enthalten. Weitere Informationen finden Sie unter [Benennen von Warteschlangen und Metadaten](https://msdn.microsoft.com/library/azure/dd179349.aspx).

- Namen von Service Bus-Warteschlangen können bis zu 260 Zeichen lang sein und verfügen über weniger restriktive Benennungsregeln. Namen von Service Bus-Warteschlangen dürfen Buchstaben, Ziffern, Punkte (.), Bindestriche (-) und Unterstriche (\_) enthalten.

## Leistung

In diesem Abschnitt werden Azure-Warteschlangen und Service Bus-Warteschlangen im Hinblick auf die Leistung verglichen.

|Vergleichskriterien|Azure-Warteschlangen|Service Bus-Warteschlangen|
|---|---|---|
|Maximaler Durchsatz|**Bis zu 2.000 Nachrichten pro Sekunde**<br/><br/>(basierend auf Vergleichstests mit Nachrichten von 1 KB)|**Bis zu 2.000 Nachrichten pro Sekunde**<br/><br/>(basierend auf Vergleichstests mit Nachrichten von 1 KB)|
|Durchschnittliche Latenz|**10 ms**<br/><br/>(mit deaktiviertem [TCP Nagle-Algorithmus](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx))|**20–25 ms**|
|Begrenzungsverhalten|**Bei HTTP 503-Code ablehnen**<br/><br/>(Begrenzte Anforderungen sind nicht abrechenbar.)|**Mit Ausnahme/HTTP 503 ablehnen**<br/><br/>(Begrenzte Anforderungen sind nicht abrechenbar.)|

### Zusätzliche Informationen

- Eine einzelne Azure-Warteschlange kann bis zu 2.000 Transaktionen pro Sekunde verarbeiten. Eine Transaktion ist ein **Put**-, **Get**-, oder **Delete**-Vorgang. Das Senden einer einzelnen Nachricht an eine Warteschlange (**Put**) wird als eine Transaktion gezählt, während der Empfang einer Nachricht häufig ein zweistufiger Vorgang ist, der den Abruf (**Get**) gefolgt von einer Anforderung zum Entfernen der Nachricht aus der Warteschlange (**Delete**) umfasst. Folglich werden i. d. R. zwei Transaktionen ausgeführt, um eine Nachricht erfolgreich aus einer Warteschlange zu entfernen. Das Abrufen mehrerer Nachrichten in einem Batch kann die negativen Auswirkungen verringern, da Sie bis zu 32 Nachrichten mithilfe von **Get** in einer Transaktion abrufen können, gefolgt von einem **Delete**-Vorgang für jede dieser Nachrichten. Damit ein besserer Durchsatz erzielt wird, können Sie mehrere Warteschlangen erstellen (ein Speicherkonto kann uneingeschränkt viele Warteschlangen aufweisen).

- Wenn die Anwendung den maximalen Durchsatz für eine Azure-Warteschlange erreicht, wird normalerweise die Antwort "HTTP 503 – Server ausgelastet" vom Warteschlangendienst zurückgegeben. In diesem Fall sollte die Anwendung die Wiederholungslösung mit exponentieller Backoffverzögerung auslösen.

- Die Latenz von Azure-Warteschlangen beträgt durchschnittlich 10 Millisekunden, wenn kleine Nachrichten (unter 10 KB) von einem gehosteten Dienst behandelt werden, der sich am selben Ort (bzw. derselben Region) wie das Speicherkonto befindet.

- Sowohl von Azure-Warteschlangen als auch von Service Bus-Warteschlangen wird das Begrenzungsverhalten erzwungen, indem Anforderungen an eine begrenzte Warteschlange angelehnt werden. Begrenzte Anforderungen werden jedoch von keiner der Warteschlangen als abrechenbar behandelt.

- Vergleichstests für Service Bus-Warteschlangen haben gezeigt, dass eine einzelne Warteschlange bei einer Nachrichtengröße von ca. 1 KB einen Nachrichtendurchsatz von bis zu 2.000 Nachrichten pro Sekunde erreichen kann. Verwenden Sie mehrere Warteschlangen, um einen höheren Durchsatz zu erzielen. Weitere Informationen zur Leistungsoptimierung mit Service Bus finden Sie unter [Bewährte Methoden für Leistungsoptimierungen mithilfe von Service Bus-Brokermessaging](service-bus-performance-improvements.md).

- Sobald der maximale Durchsatz für eine Service Bus-Warteschlange erreicht ist, wird (bei Verwendung der verwalteten .NET-API für Brokermessaging) eine [ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx)-Antwort bzw. (bei Verwendung der REST-basierten API) eine HTTP 503-Antwort an den Warteschlangenclient zurückgegeben. Dies weist darauf hin, dass die Warteschlange eingeschränkt ist.

## Authentifizierung und Autorisierung

In diesem Abschnitt werden die von Azure-Warteschlangen und Service Bus-Warteschlangen unterstützten Autorisierungsfunktionen erläutert.

|Vergleichskriterien|Azure-Warteschlangen|Service Bus-Warteschlangen|
|---|---|---|
|Authentifizierung|**Symmetrischer Schlüssel**|**Symmetrischer Schlüssel**|
|Sicherheitsmodell|Delegierter Zugriff über SAS-Token|SAS|
|Verbund von Identitätsanbietern|**Nein**|**Ja**|

### Zusätzliche Informationen

- Jede Anforderung an eine der beiden Warteschlangentechnologien muss authentifiziert werden. Öffentliche Warteschlangen mit anonymem Zugriff werden nicht unterstützt. Mithilfe von SAS können Sie dieses Szenario verwalten, indem Sie eine lesegeschützte SAS, eine schreibgeschützte SAS oder sogar eine SAS mit Vollzugriff veröffentlichen.

- Das von Azure-Warteschlangen bereitgestellte Authentifizierungsschema schließt die Verwendung eines symmetrischen Schlüssels ein. Dieser entspricht einem hashbasierten Message Authentication Code (HMAC), der mit dem SHA-256-Algorithmus berechnet und als **Base64**-Zeichenfolge codiert wird. Weitere Informationen zum jeweiligen Protokoll finden Sie unter [Authentifizierung für die Azure-Speicherdienste](https://msdn.microsoft.com/library/azure/dd179428.aspx). Service Bus-Warteschlangen unterstützen ein ähnliches Modell mithilfe symmetrischer Schlüssel. Weitere Informationen finden Sie unter [SAS-Authentifizierung (Shared Access Signature) mit Service Bus](service-bus-shared-access-signature-authentication.md).

## Kosten

In diesem Abschnitt werden Azure-Warteschlangen und Service Bus-Warteschlangen im Hinblick auf Kosten verglichen.

|Vergleichskriterien|Azure-Warteschlangen|Service Bus-Warteschlangen|
|---|---|---|
|Kosten für Warteschlangentransaktionen|**0,0036 $**<br/><br/>(pro 100.000 Transaktionen)|**Basic-Stufe**: **0,05 $**<br/><br/>(pro Million Vorgänge)|
|Abrechenbare Vorgänge|**Alle**|**Nur Senden/Empfangen**<br/><br/>(keine Gebühren für andere Vorgänge)|
|Transaktionen im Leerlauf|**Abrechenbar**<br/><br/>(Die Abfrage einer leeren Warteschlange wird als abrechenbare Transaktion angerechnet.)|**Abrechenbar**<br/><br/>(Ein Empfangsvorgang für eine leere Warteschlange wird als abrechenbare Nachricht angerechnet.)|
|Speicherkosten|**0,07 $**<br/><br/>(pro GB/Monat)|**0,00 $**|
|Übertragungskosten für ausgehende Daten|**0,12–0,19 $**<br/><br/>(je nach Geografie)|**0,12–0,19 $**<br/><br/>(je nach Geografie)|

### Zusätzliche Informationen

- Datenübertragungen werden auf Grundlage der Gesamtmenge von Daten abgerechnet, die das Azure-Rechenzentrum über das Internet in einem bestimmten Abrechnungszeitraum verlassen.

- Datenübertragungen zwischen Azure-Diensten, die sich in derselben Region befinden, sind nicht gebührenpflichtig.

- Zum Zeitpunkt der Erstellung dieses Dokuments sind sämtliche eingehenden Datenübertragungen nicht gebührenpflichtig.

- Die Unterstützung langer Abrufintervalle vorausgesetzt, kann sich die Verwendung von Service Bus-Warteschlangen in Situationen, in denen eine Zustellung mit niedriger Latenz erforderlich ist, als kosteneffektiv erweisen.

>[AZURE.NOTE] Änderungen bei allen Kosten bleiben vorbehalten. Die vorangehende Tabelle enthält die Preise, die zum Zeitpunkt der Erstellung dieses Dokuments gültig waren, und schließt keine Werbeangebote ein, die möglicherweise gerade gültig sind. Aktuelle Preisinformationen für Azure finden Sie auf der Seite [Azure-Preise](https://azure.microsoft.com/pricing/). Weitere Informationen zu Service Bus-Preisen finden Sie unter [Service Bus Preise](https://azure.microsoft.com/pricing/details/service-bus/).

## Zusammenfassung

Durch ein tieferes Verständnis der beiden Technologien können Sie eine fundiertere Entscheidung zu der verwendeten Warteschlangentechnologie treffen. Die Entscheidung für Azure-Warteschlangen oder Service Bus-Warteschlangen hängt eindeutig von mehreren Faktoren ab. Diese Faktoren können sich nach den individuellen Anforderungen der Anwendung und der Architektur richten. Wenn die Anwendung bereits die Kernfunktionen von Microsoft Azure verwendet, ziehen Sie es möglicherweise vor, Azure-Warteschlangen zu verwenden, insbesondere, wenn es um grundlegende Kommunikation und Messaging zwischen Diensten geht oder Sie Warteschlangen benötigen, die größer sind als 80 GB.

Da Service Bus-Warteschlangen eine Vielzahl erweiterter Funktionen wie Sitzungen, Transaktionen, Duplikaterkennung, automatische unzustellbare Nachrichten und bewährte Funktionen für Veröffentlichung/Abonnements bieten, werden Sie diese vielleicht vorziehen, wenn Sie eine Hybridanwendung erstellen bzw. diese Funktionen von der Anwendung benötigt werden.

## Nächste Schritte

Die folgenden Artikel enthalten weitere Anleitungen und Informationen zur Verwendung von Azure-Warteschlangen oder Service Bus-Warteschlangen.

- [Verwenden von Servicebus-Warteschlangen](service-bus-dotnet-how-to-use-queues.md)
- [Verwenden des Warteschlangenspeicherdiensts](../storage/storage-dotnet-how-to-use-queues.md)
- [Bewährte Methoden für Leistungsoptimierungen mithilfe von Service Bus-Brokermessaging](service-bus-performance-improvements.md)
- [Introducing Queues and Topics in Azure Service Bus (Einführung in Warteschlangen und Themen in Azure Service Bus, in englischer Sprache)](http://www.code-magazine.com/article.aspx?quickid=1112041)
- [The Developer's Guide to Service Bus (Entwicklerhandbuch für Service Bus, in englischer Sprache)](http://www.cloudcasts.net/devguide/)
- [Azure Tables and Queues Deep Dive (Fundierte Einblicke in Azure-Tabellen und -Warteschlangen, in englischer Sprache)](http://www.microsoftpdc.com/2009/SVC09)
- [Azure Storage Architecture (Azure Storage-Architektur, in englischer Sprache)](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
- [Using the Queuing Service in Azure (Verwenden des Warteschlangendiensts in Azure, in englischer Sprache)](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
- [Understanding Azure Storage Billing – Bandwidth, Transactions, and Capacity (Grundlagen zur Abrechnung von Azure Storage – Bandbreite, Transaktionen und Kapazität, in englischer Sprache)](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)


[klassischen Azure-Portal]: http://manage.windowsazure.com
[klassischen Azure-Portals]: http://manage.windowsazure.com
 

<!---HONumber=AcomDC_0128_2016-->