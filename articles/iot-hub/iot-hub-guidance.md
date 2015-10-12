<properties
 pageTitle="Azure IoT Hub – Anleitung | Microsoft Azure"
 description="Enthält Anleitungsthemen für Lösungen mit Azure IoT Hub."
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="kevinmil"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Azure IoT Hub – Anleitung

## Vergleich: IoT Hub und Event Hubs
Eine der wichtigsten Anwendungsmöglichkeiten für Azure IoT Hub ist die Erfassung der Telemetriedaten von Geräten. Aus diesem Grund wird IoT Hub häufig mit [Event Hubs] verglichen. Dies ist ein Dienst zur Ereignisverarbeitung, der riesige Mengen an Ereignis- und Telemetriedaten für die Cloud erfassen kann und gleichzeitig eine niedrige Latenz und hohe Zuverlässigkeit bietet.

Bei den Diensten bestehen aber viele Unterschiede, die in den folgenden Abschnitten beschrieben werden.

| Bereich | IoT Hub | Event Hubs |
| ---- | ------- | ---------- |
| Kommunikationsmuster | Device-to-Cloud (D2C)-Ereigniseingang und Cloud-to-Device (C2D)-Messaging. | Nur Ereigniseingang (meist für D2C-Szenarien). |
| Sicherheit | Identität pro Gerät und widerrufbare Zugriffssteuerung. Siehe [IoT Hub-Entwicklerleitfaden – Sicherheit]. | Event Hub-weite [SAS-Richtlinien][Event Hub - security] mit begrenzter Widerrufsunterstützung mithilfe von [Richtlinien des Herausgebers][Event Hub publisher policies]. Im Kontext von IoT-Lösungen ist es häufig erforderlich, eine benutzerdefinierte Lösung zur Unterstützung von Anmeldeinformationen pro Gerät und Maßnahmen zum Schutz vor Spoofing zu implementieren. |
| Skalieren | IoT Hubs ist für die Unterstützung von Millionen von gleichzeitig verbundenen Geräten optimiert. | Event Hubs können eine eingeschränktere Anzahl von gleichzeitigen Verbindungen unterstützen: bis zu 5.000 AMQP-Verbindungen gemäß [Service Bus-Kontingenten]. Dafür können Benutzer bei Event Hubs die Partition für jede gesendete Nachricht angeben. |
| Geräte-SDKs | Bei IoT Hub werden [Geräte-SDKs][Azure IoT Hub SDKs] für viele verschiedene Plattformen und Sprachen bereitgestellt. | Event Hubs wird unter .NET und C unterstützt und verfügt über AMQP- und HTTP-Sendeschnittstellen. |

Schlussfolgerung: Auch wenn nur der D2C-Telemetrieeingang als Anwendungsfall vorhanden ist, bietet IoT Hub einen Dienst, der speziell für die Verbindung von IoT-Geräten ausgelegt ist. Außerdem werden die Wertbeiträge für diese Szenarien mit IoT-spezifischen Features weiter ausgebaut. Event Hubs sind für den Ereigniseingang in großem Umfang ausgelegt, sowohl im Kontext von Szenarien im Rechenzentrum als auch außerhalb davon. Es ist nicht ungewöhnlich, IoT Hub und Event Hubs zusammen zu verwenden. Dabei wird IoT Hub für die D2C-Kommunikation eingesetzt, und mit Event Hubs wird der nachgeschaltete Ereigniseingang in Echtzeit-Verarbeitungsmodule geregelt.

## Gerätebereitstellung <a id="provisioning"></a>
Bei IoT-Lösungen werden die Geräteinformationen in vielen unterschiedlichen Systemen und Speichern verwaltet. Die [Identitätsregistrierung von IoT Hub][IoT Hub Developer Guide - identity registry] ist eine von vielen anderen Speichern, mit denen anwendungsspezifische Geräteinformationen verwaltet werden. Wir bezeichnen den Prozess der Erstellung der erforderlichen Geräteinformationen in all diesen Systemen als *Bereitstellung*.

Für die Gerätebereitstellung gibt es viele Anforderungen und Strategien (weitere Informationen unter [IoT Hub-Geräteverwaltung – Anleitung]). Die [Identitätsregistrierung von IoT Hub][IoT Hub Developer Guide - identity registry] stellt die APIs bereit, die Sie zum Integrieren von IoT Hub in Ihren Bereitstellungsprozess benötigen.

## Bereichsgateways <a id="fieldgateways"></a>
Ein Bereichsgateway ist eine spezielle Geräte-Appliance- bzw. allgemeine Software, die zum Ermöglichen der Kommunikation und unter Umständen auch als lokales Gerätekontrollsystem und Hub für die Verarbeitung von Gerätedaten dient. Ein Bereichsgateway kann lokale Verarbeitungs- und Kontrollfunktionen für die Geräte durchführen. Auf der anderen Seite kann damit die Gerätetelemetrie gefiltert oder aggregiert werden, um die Datenmenge zu reduzieren, die an das Back-End übertragen wird.

Der Bereich eines Bereichsgateways enthält das Bereichsgateway selbst und alle Geräte, die daran angeschlossen sind. Bereichsgateways sind außerhalb der dedizierten Datenverarbeitungseinrichtungen angeordnet und normalerweise standortgebunden. Ein Bereichsgateway unterscheidet sich von einem normalen Router für Datenverkehr darin, dass es beim Verwalten des Zugriffs und des Informationsflusses eine aktive Rolle spielt. Dies bedeutet, dass es sich um eine Entität mit Anwendungsadressierung und Netzwerkverbindung oder Sitzungsterminal handelt (in diesem Kontext können Gateways beispielsweise bei der Gerätebereitstellung, Datentransformation, Protokollübersetzung und Ereignisregelverarbeitung eingesetzt werden). NAT-Geräte oder -Firewalls können jedoch nicht als Bereichsgateways dienen, da es keine expliziten Verbindungs- oder Sitzungsterminals sind, sondern sie leiten eher Verbindungen oder Sitzungen weiter (oder verweigern dies).

### Transparente und nicht transparente Bereichsgateways
In Bezug auf Geräteidentitäten werden Bereichsgateways als *transparent* bezeichnet, wenn andere Geräte im Bereich über Geräteidentitätseinträge in der IoT Hub-Identitätsregistrierung verfügen. Dagegen werden sie als *nicht transparent* bezeichnet, falls die einzige Identität in der IoT Hub-Identitätsregistrierung die Identität des Bereichsgateways ist.

Es ist wichtig zu beachten, dass durch die Verwendung von *nicht transparenten* Gateways verhindert wird, dass IoT Hub [für die Geräteidentität Maßnahmen gegen Spoofing][IoT Hub Developer Guide - Anti-spoofing] bereitstellt. Da alle Geräte im Bereich des Bereichsgateways in IoT Hub als einzelnes Gerät dargestellt werden, unterliegen sie Drosselungen und Kontingenten ebenfalls als einzelnes Gerät.

### Weitere Überlegungen

Bei der Implementierung eines Bereichsgateways können Sie die Geräte-SDKs von Azure IoT verwenden. Einige SDKs verfügen über spezifische Funktionen für das Bereichsgateway, z. B. Multiplexing mehrerer Geräte auf derselben Verbindung mit IoT Hub. Wie in [IoT Hub-Entwicklerleitfaden – Auswählen des Kommunikationsprotokolls] beschrieben, sollten Sie die Verwendung von HTTP/1 als Transportprotokoll für Bereichsgateways vermeiden.

## Verwenden von benutzerdefinierten Schemas/Diensten für die Geräteauthentifizierung <a id="customauth"></a>
Mit Azure IoT Hub können Sie Sicherheitsanmeldeinformationen und die Zugriffssteuerung pro Gerät konfigurieren, indem Sie die [Geräteidentitätsregistrierung][IoT Hub Developer Guide - identity registry] verwenden. Auch wenn eine IoT-Lösung bereits stark an einer benutzerdefinierten Geräteidentitätsregistrierung bzw. einem Authentifizierungsschema beteiligt ist, können dafür trotzdem andere IoT Hub-Features genutzt werden, indem ein *Tokendienst* für IoT Hub erstellt wird.

Der Tokendienst ist ein sich selbst bereitstellender Clouddienst, bei dem eine IoT Hub-SAS-Richtlinie mit **DeviceConnect**-Berechtigungen verwendet wird, um Token mit *Gerätebereich* zu erstellen.

  ![][img-tokenservice]

Dies sind die wichtigsten Schritte des Tokendienstmusters.

1. Erstellen Sie eine [IoT Hub-SAS-Richtlinie][IoT Hub Developer Guide - Security] mit **DeviceConnect**-Berechtigungen für IoT Hub. Diese Richtlinie wird vom Tokendienst zum Signieren der Token verwendet.
2. Wenn ein Gerät auf IoT Hub zugreifen möchte, fordert es Ihren Tokendienst als signiertes Token an. Das Gerät kann Ihre benutzerdefinierte Geräteidentitätsregistrierung bzw. das Authentifizierungsschema verwenden.
3. Der Tokendienst gibt ein Token zurück, das gemäß [IoT Hub-Entwicklerleitfaden – Sicherheit] erstellt wird, wobei `/devices/{deviceId}` als `resourceURI` verwendet wird. `deviceId` ist das Gerät, das authentifiziert wird.
4. Das Gerät nutzt das Token direkt mit IoT Hub.

Der Tokendienst kann die Gültigkeitsdauer für das Token wie gewünscht festlegen. Nach dem Ablaufen trennt IoT Hub die Verbindung, und das Gerät muss vom Tokendienst ein neues Token anfordern. Eine kurze Gültigkeitsdauer führt natürlich dazu, dass sich die Auslastung für das Gerät und den Tokendienst gleichermaßen erhöht.

Es ist wichtig, darauf hinzuweisen, dass die Geräteidentität weiterhin in IoT Hub erstellt werden muss, damit das Gerät die Verbindung herstellen kann. Dies bedeutet auch, dass die Zugriffssteuerung pro Gerät (durch das Deaktivieren der Geräteidentitäten gemäß [IoT Hub-Entwicklerleitfaden – Identitätsregistrierung]) auch dann noch funktioniert, wenn das Gerät per Token authentifiziert wird. So müssen keine Token mit sehr langer Lebensdauer vorhanden sein.

### Vergleich mit einem benutzerdefinierten Gateway
Das Tokendienstmuster ist der empfohlene Weg zum Implementieren einer benutzerdefinierten Identitätsregistrierung bzw. eines Authentifizierungsschemas mit IoT Hub, da IoT Hub die Behandlung eines Großteils des Lösungsdatenverkehrs überlassen wird. Es gibt aber auch Fälle, in denen das benutzerdefinierte Authentifizierungsschema so eng mit dem Protokoll (z. B. [TLS-PSK]) verknüpft ist, dass ein Dienst zum Verarbeiten des gesamten Datenverkehrs (*benutzerdefiniertes Gateway*) erforderlich ist. Weitere Informationen finden Sie im Artikel [Protokollgateway].

## Skalieren von IoT Hub
IoT Hub kann bis zu einer Million gleichzeitig verbundene Geräte unterstützen, indem die Anzahl von IoT Hub S1- oder S2-Einheiten auf 2.000 erhöht wird. Weitere Informationen finden Sie unter [IoT Hub – Preise][lnk-pricing].

Jede IoT Hub-Einheit lässt eine bestimmte Anzahl von Geräteidentitäten in der Registrierung zu, die alle gleichzeitig verbunden sein können. Außerdem ist eine bestimmte Anzahl von täglichen Nachrichten zulässig.

Um Ihre Lösung richtig skalieren zu können, müssen Sie sich nach dem jeweiligen IoT Hub-Anwendungsfall richten. Achten Sie besonders auf den erforderlichen Spitzendurchsatz für die folgenden Kategorien von Vorgängen:

* Device-to-Cloud (D2C)-Nachrichten
* Cloud-to-Device (C2D)-Nachrichten
* Identitätsregistrierungsvorgänge

Sehen Sie sich zusätzlich zu diesen Durchsatzinformationen auch [IoT Hub-Kontingente und -Drosselungen] an, und entwerfen Sie Ihre Lösung entsprechend.

### D2C- und C2D-Nachrichtendurchsatz
Die beste Methode zum Skalieren einer IoT Hub-Lösung ist die Auswertung des Datenverkehrs pro Gerät.

D2C-Nachrichten basieren auf diesen Richtlinien für den anhaltenden Durchsatz:

| Preisstufe | Anhaltender Durchsatz | Anhaltende Senderate |
| ---- | -------------------- | ------------------- |
| S1 | Bis zu 8 KBit/Stunde pro Gerät | Durchschnitt von 4 Nachrichten/Stunde pro Gerät |
| S2 | Bis zu 4 KBit/Min. pro Gerät | Durchschnitt von 2 Nachrichten/Min. pro Gerät |

Beim Empfangen von D2C-Nachrichten kann das Anwendungs-Back-End den folgenden maximalen Durchsatz erwarten (über alle Reader).

| Preisstufe | Anhaltender Durchsatz |
| ---- | -------------------- |
| S1 | Bis zu 120 KBit/Min. pro Einheit, Minimum von 2 MBit/s |
| S2 | Bis zu 4 MBit/Min. pro Einheit, Minimum von 2 MBit/s |

Die Leistung wird bei C2D-Nachrichten pro Gerät skaliert, und jedes Gerät empfängt bis zu 5 Nachrichten pro Minute.

### Durchsatz von Identitätsregistrierungsvorgängen
IoT Hub-Identitätsregistrierungsvorgänge sollten keine Laufzeitvorgänge sein, da sie sich größtenteils auf die Gerätebereitstellung beziehen. Genaue Zahlen zur Burst-Leistung finden Sie unter [IoT Hub-Kontingente und -Drosselungen].

### Sharding (Horizontales Partitionieren)
Eine einzelne IoT Hub-Einheit kann zwar auf Millionen von Geräten skaliert werden, aber es kann sein, dass Ihre Lösung bestimmte Leistungsmerkmale benötigt, die von einer einzelnen IoT Hub-Einheit nicht gewährleistet werden können. In diesem Fall wird empfohlen, Ihre Geräte auf mehrere IoT Hubs zu partitionieren, um Datenverkehr-Bursts zu glätten und den erforderlichen Durchsatz oder die Vorgangsraten zu erzielen.

## Hohe Verfügbarkeit und Notfallwiederherstellung
Als Azure-Dienst sorgt IoT Hub für hohe Verfügbarkeit, indem Redundanzen auf Azure-Regionsebene bereitgestellt werden, ohne dass zusätzlicher Aufwand für die Lösung anfällt. Azure bietet außerdem eine Reihe von Features zum Erstellen von Lösungen mit Funktionen für die Notfallwiederherstellung oder, falls erforderlich, für die regionsübergreifende Verfügbarkeit. Lösungen müssen entsprechend entworfen und vorbereitet werden, um diese Features nutzen zu können und für Geräte oder Benutzer für eine globale, regionsübergreifende hohe Verfügbarkeit zu sorgen. Im Artikel [Azure-Geschäftskontinuität – Technische Anleitung] werden die integrierten Features von Azure für Geschäftskontinuität und Notfallwiederherstellung beschrieben. Das Dokument [Notfallwiederherstellung und hohe Verfügbarkeit für Azure-Anwendungen] enthält Architekturinformationen zu Strategien für Azure-Anwendungen in Bezug auf Notfallwiederherstellung und hohe Verfügbarkeit.

## Regionales Failover mit IoT Hub
Eine ausführliche Erläuterung von Bereitstellungstopologien in IoT-Lösungen würde den Rahmen dieses Abschnitts sprengen, aber mit Blick auf hohe Verfügbarkeit und Notfallwiederherstellung gehen wir auf das Bereitstellungsmodell für das *regionale Failover* ein.

Bei einem Modell für regionales Failover wird das Back-End der Lösung hauptsächlich an einem Rechenzentrumstandort ausgeführt, aber eine IoT Hub-Einheit mit Back-End wird zu Failoverzwecken in einer weiteren Rechenzentrumregion bereitgestellt. Wenn die IoT Hub-Einheit im primären Rechenzentrum ausfällt oder die Netzwerkverbindung vom Gerät zum primären Rechenzentrum unterbrochen wird, ist dies dann entsprechend abgedeckt. Geräte nutzen jeweils einen sekundären Dienstendpunkt, wenn das primäre Gateway nicht erreichbar ist. Mit einer regionsübergreifenden Failoverfunktion kann die Lösungsverfügbarkeit über die hohe Verfügbarkeit einer einzelnen Region hinweg verbessert werden.

Sie benötigen grob Folgendes, um ein Modell für regionales Failover mit IoT Hub zu implementieren:

* **Sekundäre IoT Hub-Einheit und Logik für Geräterouting:** Bei einer Dienstunterbrechung in der primären Region müssen Geräte eine Verbindung zur sekundären Region herstellen. Da die meisten beteiligten Dienste zustandsbehaftet sind, wird der Failoverprozess zwischen Regionen häufig von Lösungsadministratoren ausgelöst. Die beste Möglichkeit, Geräte über den neuen Endpunkt zu informieren und gleichzeitig die Kontrolle über den Prozess zu behalten, besteht darin, für die Geräte eine regelmäßige Prüfung eines *Concierge*-Diensts auf den derzeit aktiven Endpunkt durchführen zu lassen. Der Concierge-Dienst kann eine einfache Webanwendung sein, die repliziert wird und mithilfe von DNS-Umleitungsverfahren (z. B. per [Azure Traffic Manager]) erreichbar gehalten wird.
* **Identitätsregistrierungsreplikation:** Um verwendet werden zu können, muss die sekundäre IoT Hub-Einheit alle Geräteidentitäten enthalten, für die eine Verbindung mit der Lösung hergestellt werden soll. Für die Lösung sollten georeplizierte Backups von Geräteidentitäten vorgehalten und auf die sekundäre IoT Hub-Einheit hochgeladen werden, bevor der aktive Endpunkt für die Geräte gewechselt wird. Die Funktionen zum Exportieren der Geräteidentität von IoT Hub sind in diesem Zusammenhang sehr nützlich ([IoT Hub-Entwicklerleitfaden – Identitätsregistrierung]).
* **Zusammenführungslogik:** Wenn die primäre Region wieder verfügbar ist, müssen die Status und Daten, die am sekundären Standort erstellt wurden, zurück zur primären Region migriert werden. Dies bezieht sich hauptsächlich auf Geräteidentitäten und Anwendungsmetadaten, die mit der primären IoT Hub-Einheit und etwaigen anderen anwendungsspezifischen Speichern in der primären Region zusammengeführt werden müssen. Zum Vereinfachen dieses Schritts ist es normalerweise ratsam, idempotente Vorgänge zu nutzen. So werden Nebeneffekte nicht nur für die letztendliche konsistente Verteilung von Ereignissen verringert, sondern auch für Duplikate oder die außerordentliche Bereitstellung von Ereignissen. Außerdem sollte die Anwendungslogik so konzipiert sein, dass mögliche Inkonsistenzen oder leicht veraltete Zustände toleriert werden. Die „Heilung“ des Systems (bzw. der Vorgang auf Basis von RPOs (Recovery Point Objectives (RPO)) nimmt zusätzliche Zeit in Anspruch . Der folgende Artikel enthält weitere Informationen zu diesem Thema: [Failsafe: Leitfaden zu robusten Cloudarchitekturen].




[img-tokenservice]: media/iot-hub-guidance/tokenservice.png

[IoT Hub Developer Guide - identity registry]: iot-hub-devguide.md#identityregistry
[IoT Hub-Entwicklerleitfaden – Identitätsregistrierung]: iot-hub-devguide.md#identityregistry
[IoT Hub-Entwicklerleitfaden – Auswählen des Kommunikationsprotokolls]: iot-hub-devguide.md#amqpvshttp
[IoT Hub Developer Guide - Security]: iot-hub-devguide.md#security
[IoT Hub-Entwicklerleitfaden – Sicherheit]: iot-hub-devguide.md#security
[IoT Hub Developer Guide - Anti-spoofing]: iot-hub-devguide.md#antispoofing
[Protokollgateway]: iot-hub-protocol-gateway.md
[IoT Hub-Kontingente und -Drosselungen]: iot-hub-devguide.md#throttling

[IoT Hub-Geräteverwaltung – Anleitung]: iot-hub-device-management.md
[Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview/
[Event Hub publisher policies]: ../event-hubs-overview/#common-publisher-tasks
[Service Bus-Kontingenten]: ../service-bus/service-bus-quotas/
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md

[TLS-PSK]: https://tools.ietf.org/html/rfc4279

[Azure-Geschäftskontinuität – Technische Anleitung]: https://msdn.microsoft.com/library/azure/hh873027.aspx
[Notfallwiederherstellung und hohe Verfügbarkeit für Azure-Anwendungen]: https://msdn.microsoft.com/library/azure/dn251004.aspx
[Failsafe: Leitfaden zu robusten Cloudarchitekturen]: https://msdn.microsoft.com/library/azure/jj853352.aspx

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub

<!---HONumber=Oct15_HO1-->