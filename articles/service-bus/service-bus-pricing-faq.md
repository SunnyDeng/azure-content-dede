<properties 
   pageTitle="Häufig gestellte Fragen zu Service Bus-Preisen | Microsoft Azure"
   description="In diesem Abschnitt werden einige häufig gestellte Fragen zur Service Bus-Preisstruktur beantwortet."
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
   ms.workload="na"
   ms.date="12/28/2015"
   ms.author="sethm" />

# Häufig gestellte Fragen zu Service Bus-Preisen

In diesem Abschnitt werden einige häufig gestellte Fragen zur Service Bus-Preisstruktur beantwortet. Sie können auch die [Azure-Support-FAQ](http://go.microsoft.com/fwlink/?LinkID=185083) aufrufen, wenn Sie nach allgemeinen Microsoft Azure-Preisinformationen suchen. Vollständige Informationen zu Service Bus-Preisen finden Sie unter [Service Bus-Preise](http://azure.microsoft.com/pricing/details/service-bus/).

>[AZURE.NOTE]Die Preisstruktur für Event Hubs wird im Thema [Event Hubs Verfügbarkeit und Support – häufig gestellte Fragen](event-hubs-availability-and-support-faq.md) beschrieben. Weitere Informationen hierzu finden Sie zudem im Thema [Event Hubs-Preise](http://azure.microsoft.com/pricing/details/event-hubs/).

- [Wie werden die Kosten für Service Bus berechnet?](#how-do-you-charge-for-service-bus)
- [Bei welcher Nutzung von Service Bus werden Daten übertragen? Bei welcher nicht?](#what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not)
- [Was genau ist ein Service Bus-Relay?](#what-exactly-is-a-service-bus-quotrelayquot)
- [Wie wird die Verbrauchseinheit "Relaystunden" berechnet?](#how-is-the-relay-hours-meter-calculated)
- [Was geschieht, wenn mehrere Listener mit einem bestimmten Relay verbunden sind?](#what-if-i-have-more-than-one-listener-connected-to-a-given-relay)
- [Wie wird die Verbrauchseinheit "Nachrichten" für Relays berechnet?](#how-is-the-messages-meter-calculated-for-relays)
- [Fallen für Service Bus Speicherkosten an?](#does-service-bus-charge-for-storage)
- [Gibt es für Service Bus Nutzungskontingente?](#does-service-bus-have-any-usage-quotas)

## Wie werden die Kosten für Service Bus berechnet?

Vollständige Informationen zur Preisgestaltung von Servicebus finden Sie unter [Service Bus – Preisdetails und Abrechnung](https://msdn.microsoft.com/library/dn831889.aspx) sowie unter [Service Bus-Preise](http://azure.microsoft.com/pricing/details/service-bus/). Neben den aufgeführten Preisen werden Ihnen die damit verbundenen aus dem Datencenter ausgehenden Datenübertragungen berechnet, mit denen Ihre Anwendung bereitgestellt wird. Weitere Details finden Sie im Abschnitt [Bei welcher Nutzung von Service Bus werden Daten übertragen? Bei welcher nicht?](#what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not).

## Bei welcher Nutzung von Service Bus werden Daten übertragen? Bei welcher nicht?

Jede Datenübertragung innerhalb der jeweiligen Azure-Region ist kostenfrei. Bei ausgehenden Datenübertragungen außerhalb einer Region fallen Kosten zu einem Tarif von $0,15 pro GB von den Regionen Nordamerika und Europa an sowie $0,20 pro GB von der Region Asien-Pazifik. Sämtliche eingehenden Datenübertragungen sind kostenfrei.

## Was genau ist ein Service Bus-Relay?

Ein Relay ist eine Service Bus-Entität, die Nachrichten zwischen Clients und Webdiensten vermittelt. Das Relay stellt den Dienst mit einer dauerhaften, ermittelbaren Service Bus-Adresse, einer zuverlässigen Verbindung mit Firewall-/NAT-Ausnahmen und weiteren Funktionen wie automatischem Lastenausgleich zur Verfügung. Ein Relay wird an einer bestimmten Service Bus-Adresse implizit instanziiert und geöffnet (Namespace-URL), sobald ein relayfähiger WCF-Dienst oder "Relaylistener" zum ersten Mal eine Verbindung mit dieser Adresse herstellt. Anwendungen erstellen Relaylistener mithilfe der verwalteten .NET-API von Service Bus, die spezielle relayfähige Versionen der standardmäßigen WCF-Bindungen bietet.

## Wie wird die Verbrauchseinheit "Relaystunden" berechnet?

Relaystunden werden für den kumulativen Zeitraum in Rechnung gestellt, in dem jedes Service Bus Relay während eines bestimmten Abrechnungszeitraums "geöffnet" ist. Ein Relay wird an einer bestimmten Service Bus-Adresse implizit instanziiert und geöffnet (Dienstnamespace-URL), sobald ein relayfähiger WCF-Dienst oder "Relaylistener" zum ersten Mal eine Verbindung mit dieser Adresse herstellt. Das Relay wird erst geschlossen, wenn der letzte Listener die Verbindung mit dieser Adresse trennt. Zu Abrechnungszwecken wird ein Relay daher ab der Verbindungsherstellung durch den ersten Relaylistener bis zur Trennung der Verbindung mit der Service Bus-Adresse des Relays durch den letzten Relaylistener als "geöffnet" betrachtet. Anders ausgedrückt: Ein Relay gilt als "geöffnet", sobald mindestens ein Relaylistener mit seiner Service Bus-Adresse verbunden ist.

## Was geschieht, wenn mehrere Listener mit einem bestimmten Relay verbunden sind?

In einigen Fällen können mit einem einzelnen Relay in Service Bus mehrere Listener verbunden sein. Das kann bei Diensten mit Lastenausgleich vorkommen, die die WCF-Bindungen **netTCPRelay** oder **HttpRelay** verwenden, oder bei Sendungsereignislistenern, die die WCF-Bindung **netEventRelay** verwenden. Ein Relay in Service Bus wird als "geöffnet" betrachtet, wenn mindestens ein Relaylistener mit ihm verbunden ist. Das Hinzufügen weiterer Listener zu einem offenen Relay verändert nicht dessen Status im Hinblick auf die Abrechnung. Die Anzahl von Relaysendern (Clients, die Nachrichten von Relays aufrufen oder an diese senden), die mit dem Relay verbunden sind, hat ebenfalls keine Auswirkung auf die Berechnung der Relaystunden.

## Wie wird die Verbrauchseinheit "Nachrichten" für Relays berechnet?

Im Allgemeinen werden abrechenbare Nachrichten für Relays mit derselben Methode berechnet, die oben für Brokerentitäten (Warteschlangen, Themen und Abonnements) beschrieben wird. Es gibt jedoch einige wichtige Ausnahmen:

1. Das Senden einer Nachricht an ein Service Bus Relay wird als "ganz durchgehendes" Senden an den Relaylistener behandelt, der die Nachricht erhält, und nicht als ein Senden an das Service Bus Relay gefolgt von einer Zustellung an den Relaylistener. Aus diesem Grund führt ein Dienstaufruf im Anforderungs-/Antwortstil (von bis zu 64 KB) für einen Relaylistener zu zwei abrechenbaren Nachrichten: Eine Nachricht ist für die Anforderung und eine Nachricht für die Antwort abrechenbar (unter der Voraussetzung, dass die Antwort ebenfalls <= 64 KB ist). Dies unterscheidet sich vom Einsatz einer Warteschlange für die Vermittlung zwischen einem Client und einem Dienst. In letzterem Fall würde dasselbe Anforderungs-/Antwort-Muster erfordern, dass eine Anfrage an die Warteschlange gesandt wird, gefolgt von einem Entfernen aus der Warteschlange/einer Zustellung an den Dienst, wiederum gefolgt von einer Antwortsendung an eine andere Warteschlange und einem Entfernen aus der Warteschlange/einer Zustellung an den Client. Mit denselben Annahmen für die Größe (<= 64 KB) würde dieses vermittelte Warteschlangenmuster darum zu vier abrechenbaren Nachrichten führen, also der doppelten Menge im Vergleich zur Anwendung desselben Musters mithilfe eines Relays. Natürlich gibt es Vorteile beim Einsatz von Warteschlangen, wenn man dieses Muster verwenden möchte, wie etwa Dauerhaftigkeit und Lastenausgleich. Diese Vorteile können die zusätzlichen Kosten rechtfertigen.

2. Relays, die anhand der WCF-Bindung **netTCPRelay** geöffnet werden, behandeln Nachrichten nicht als einzelne Nachrichten, sondern als Datenstrom, der durch das System fließt. Anders ausgedrückt: Nur der Absender und der Listener besitzen Einblick in das Framing der einzelnen Nachrichten, die mit dieser Bindung gesendet/empfangen werden. Zum Zweck der Berechnung abrechenbarer Nachrichten werden deshalb für Relays, die die **netTCPRelay**-Bindung verwenden, alle Daten als ein Datenstrom behandelt. In diesem Fall berechnet Service Bus die Gesamtmenge der über die einzelnen Relays gesendeten oder empfangen Daten auf 5-Minuten-Basis und teilt diese Menge durch 64 KB, um so die Anzahl abrechenbarer Nachrichten für das fragliche Relay während dieses Zeitraums zu ermitteln.

## Fallen für Service Bus Speicherkosten an?

Nein, für Service Bus fallen keine Speicherkosten an. Es gibt allerdings ein Kontingent, das die Höchstmenge an Daten begrenzt, die pro Warteschlange/Thema beibehalten werden kann. Weitere Informationen finden Sie im nächsten Abschnitt.

## Gibt es für Service Bus Nutzungskontingente?

Microsoft legt für jeden Clouddienst standardmäßig ein aggregiertes monatliches Nutzungskontingent fest, das über alle Abonnements eines Kunden hinweg berechnet wird. Uns ist bewusst, dass Sie möglicherweise mehr benötigen als diese Grenzwerte. Sie können sich gerne jederzeit an unseren Kundendienst wenden und uns Ihre Anforderungen mitteilen, damit wir die Grenzwerte entsprechend anpassen können. Für Service Bus gelten die folgenden aggregierten Nutzungskontingente:

- 5 Milliarden Nachrichten

- 2 Millionen Relaystunden

Wir behalten uns das Recht vor, ein Kundenkonto zu deaktivieren, das die Nutzungskontingente in einem Monat überschritten hat. Wir informieren allerdings per E-Mail darüber und unternehmen mehrere Versuche, den Kunden zu kontaktieren, ehe wir diese Maßnahme ergreifen. Kunden, die diese Kontingente überschreiten, haben die Kosten für die Überschreitung zu tragen.

Genau wie andere Dienste in Azure setzt Service Bus eine Reihe spezifischer Kontingente durch, die eine faire Nutzung der Ressourcen sicherstellen. Im Folgenden finden Sie die Nutzungskontingente, die vom Dienst durchgesetzt werden:

- **Warteschlangen-/Themengröße**: Bei der Erstellung einer Warteschlange oder eines Themas legen Sie jeweils die maximale Größe fest. Dieses Kontingent kann einen Wert von 1, 2, 3, 4 oder 5 GB aufweisen. Wenn die festgelegte Größe erreicht ist, werden zusätzlich eingehende Nachrichten abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen.

- **Anzahl gleichzeitiger Verbindungen**
	- **Warteschlange/Thema/Abonnement**: Die Anzahl der gleichzeitigen TCP-Verbindungen für eine Warteschlange, ein Thema oder ein Abonnement ist auf 100 eingeschränkt. Wenn dieses Kontingent erreicht wird, werden nachfolgende Anforderungen für zusätzliche Verbindungen abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen. Für jede Messagingfactory unterhält Service Bus eine TCP-Verbindung, wenn einer der von ihr erstellten Clients einen ausstehenden aktiven Vorgang aufweist oder ein Vorgang vor weniger als 60 Sekunden abgeschlossen wurde. REST-Vorgänge werden nicht zu den gleichzeitigen TCP-Verbindungen hinzugezählt.


- **Anzahl gleichzeitiger Listener auf einem Relay**: Die Anzahl gleichzeitiger **netTcpRelay**- und **netHttpRelay**-Listener auf einem Relay ist auf 25 begrenzt (1 für ein **NetOneway**-Relay).

- **Anzahl der gleichzeitigen Relaylistener pro Namespace**: Service Bus setzt ein Limit von 2.000 gleichzeitigen Relaylistenern pro Dienstnamespace durch. Wenn dieses Kontingent erreicht wird, werden nachfolgende Anforderungen für das Öffnen zusätzlicher Relaylistener abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen.

- **Anzahl der Themen/Warteschlangen pro Dienstnamespace**: Die maximale Anzahl der Themen/Warteschlangen (permanente, durch Speicher gesicherte Entitäten) für einen Dienstnamespace ist auf 10.000 beschränkt. Wenn dieses Kontingent erreicht wird, werden nachfolgende Anforderungen zum Erstellen eines neuen Themas bzw. einer neuen Warteschlange für den Dienstnamespace zurückgewiesen. In diesem Fall zeigt das [klassische Azure-Portal][] eine Fehlermeldung an, oder der anfordernde Clientcode empfängt eine Ausnahme, je nachdem, ob der Erstellungsversuch über das Portal oder über den Clientcode erfolgt ist.

- **Kontingente für die Nachrichtengröße**
	- **Warteschlange/Thema/Abonnement**
		- **Nachrichtengröße**: Jede Nachricht ist auf eine Gesamtgröße von 256 KB einschließlich Nachrichtenheader begrenzt.
		- **Nachrichtenheadergröße**: Jeder Nachrichtenheader ist auf 64 KB begrenzt.

	- **NetOneway- und NetEvent-Relays**: Jede Nachricht ist auf eine Gesamtgröße von 64 KB einschließlich Nachrichtenheader begrenzt.
	- **Http- und NetTcp-Relays**: Service Bus setzt keine obere Grenze hinsichtlich der Größe von Nachrichten durch.

	Nachrichten, die diese Größenkontingente überschreiten, werden abgelehnt, und vom aufrufenden Code wird eine Ausnahme empfangen.

- **Anzahl von Abonnements pro Thema**: Die maximale Anzahl von Abonnements pro Thema ist auf 2.000 beschränkt. Wenn dieses Kontingent erreicht ist, werden nachfolgende Anforderungen zum Erstellen weiterer Abonnements für das Thema zurückgewiesen. In diesem Fall zeigt das [klassische Azure-Portal][] eine Fehlermeldung an, oder der anfordernde Clientcode empfängt eine Ausnahme, je nachdem, ob der Erstellungsversuch über das Portal oder über den Clientcode erfolgt ist.

- **Anzahl von SQL-Filtern pro Thema**: Die maximale Anzahl von SQL-Filtern pro Thema ist auf 2.000 beschränkt. Wenn dieses Kontingent erreicht ist, werden nachfolgende Anforderungen für die Erstellung weiterer Filter für das Thema zurückgewiesen, und der aufrufende Code empfängt eine Ausnahme.

- **Anzahl von Korrelationsfiltern pro Thema**: Die maximale Anzahl von Korrelationsfiltern pro Thema ist auf 100.000 beschränkt. Wenn dieses Kontingent erreicht ist, werden nachfolgende Anforderungen für die Erstellung weiterer Filter für das Thema zurückgewiesen, und der aufrufende Code empfängt eine Ausnahme.

Weitere Informationen zu Kontingenten finden Sie unter [Service Bus-Kontingente](service-bus-quotas.md).

## Nächste Schritte

Weitere Informationen zu Service Bus Messaging finden Sie in folgenden Themen.

- [Einführung in Azure Service Bus Premium-Messaging (Blogbeitrag)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Einführung in Azure Service Bus Premium-Messaging (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Übersicht über Service Bus-Messaging](service-bus-messaging-overview.md)
- [Übersicht über die Architektur von Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Verwenden von Service Bus-Warteschlangen](service-bus-dotnet-how-to-use-queues.md)

[klassische Azure-Portal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0107_2016-->