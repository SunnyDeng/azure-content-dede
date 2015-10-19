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

## Gerätebereitstellung

In IoT-Lösungen werden Geräteinformationen in vielen unterschiedlichen Systemen und Speichern verwaltet. Einer von mehreren Speichern, in denen anwendungsspezifische Geräteinformationen vorgehalten werden, ist die [Identitätsregistrierung von IoT Hub][IoT Hub Developer Guide - identity registry]. Wir bezeichnen den Prozess der Erstellung von erforderlichen Geräteinformationen in all diesen Systemen als *Bereitstellung*.

Für die Gerätebereitstellung gibt es viele Anforderungen und Strategien (weitere Informationen unter [IoT Hub-Geräteverwaltung – Anleitung]). Die [Identitätsregistrierung von IoT Hub][IoT Hub Developer Guide - identity registry] stellt die APIs bereit, die Sie zum Integrieren von IoT Hub in Ihren Bereitstellungsprozess benötigen.

## Bereichsgateways

Ein Bereichsgateway ist eine spezielle Geräte-Appliance- bzw. allgemeine Software, die zum Ermöglichen der Kommunikation und unter Umständen auch als lokales Gerätekontrollsystem und Hub für die Verarbeitung von Gerätedaten dient. Ein Bereichsgateway kann lokale Verarbeitungs- und Kontrollfunktionen für die Geräte durchführen. Auf der anderen Seite kann damit die Gerätetelemetrie gefiltert oder aggregiert werden, um die Datenmenge zu reduzieren, die an das Back-End übertragen wird.

Der Bereich eines Bereichsgateways enthält das Bereichsgateway selbst und alle Geräte, die daran angeschlossen sind. Bereichsgateways sind außerhalb der dedizierten Datenverarbeitungseinrichtungen angeordnet und normalerweise standortgebunden. Ein Bereichsgateway unterscheidet sich von einem normalen Router für Datenverkehr darin, dass es beim Verwalten des Zugriffs und des Informationsflusses eine aktive Rolle spielt. Dies bedeutet, dass es sich um eine Entität mit Anwendungsadressierung und Netzwerkverbindung oder Sitzungsterminal handelt (in diesem Kontext können Gateways beispielsweise bei der Gerätebereitstellung, Datentransformation, Protokollübersetzung und Ereignisregelverarbeitung eingesetzt werden). NAT-Geräte oder -Firewalls können jedoch nicht als Bereichsgateways dienen, da es keine expliziten Verbindungs- oder Sitzungsterminals sind, sondern sie leiten eher Verbindungen oder Sitzungen weiter (oder verweigern dies).

### Transparente und nicht transparente Bereichsgateways

In Bezug auf Geräteidentitäten werden Bereichsgateways als *transparent* bezeichnet, wenn andere Geräte im Bereich über Geräteidentitätseinträge in der IoT Hub-Identitätsregistrierung verfügen. Dagegen werden sie als *nicht transparent* bezeichnet, falls die einzige Identität in der IoT Hub-Identitätsregistrierung die Identität des Bereichsgateways ist.

Es ist wichtig zu beachten, dass durch die Verwendung von *nicht transparenten* Gateways verhindert wird, dass IoT Hub [für die Geräteidentität Maßnahmen gegen Spoofing][IoT Hub Developer Guide - Anti-spoofing] bereitstellt. Da alle Geräte im Bereich des Bereichsgateways in IoT Hub als einzelnes Gerät dargestellt werden, unterliegen sie Drosselungen und Kontingenten ebenfalls als einzelnes Gerät.

### Weitere Überlegungen

Bei der Implementierung eines Bereichsgateways können Sie die [Geräte-SDKs von Azure IoT][] verwenden. Einige SDKs verfügen über spezifische Funktionen für das Bereichsgateway, z. B. Multiplexing mehrerer Geräte auf derselben Verbindung mit IoT Hub. Wie unter [IoT Hub-Entwicklerleitfaden – Auswählen des Kommunikationsprotokolls][] beschrieben, sollten Sie die Verwendung von HTTP/1 als Transportprotokoll für Bereichsgateways vermeiden.

## Verwenden von benutzerdefinierten Schemas/Diensten für die Geräteauthentifizierung

Mit IoT Hub können Sie Sicherheitsanmeldeinformationen und die Zugriffssteuerung pro Gerät konfigurieren, indem Sie die [Geräteidentitätsregistrierung][IoT Hub Developer Guide - identity registry] verwenden.

Auch wenn eine IoT-Lösung bereits stark an einer benutzerdefinierten Geräteidentitätsregistrierung bzw. einem Authentifizierungsschema beteiligt ist, können dafür trotzdem andere IoT Hub-Features genutzt werden, indem ein *Tokendienst* für IoT Hub erstellt wird.

Der Tokendienst ist ein sich selbst bereitstellender Clouddienst, bei dem eine IoT Hub-*SAS-Richtlinie* mit **DeviceConnect**-Berechtigungen verwendet wird, um Token mit *Gerätebereich* zu erstellen.

  ![][img-tokenservice]

Dies sind die wichtigsten Schritte des Tokendienstmusters.

1. Erstellen Sie eine [IoT Hub-SAS-Richtlinie][IoT Hub Developer Guide - Security] mit **DeviceConnect**-Berechtigungen für IoT Hub. Diese Richtlinie wird vom Tokendienst zum Signieren der Token verwendet.
2. Wenn ein Gerät auf IoT Hub zugreifen möchte, fordert es Ihren Tokendienst als signiertes Token an. Das Gerät kann Ihre benutzerdefinierte Geräteidentitätsregistrierung bzw. das Authentifizierungsschema verwenden.
3. Der Tokendienst gibt ein Token zurück, das gemäß [IoT Hub-Entwicklerleitfaden – Sicherheit][] erstellt wird, wobei `/devices/{deviceId}` als `resourceURI` verwendet wird. `deviceId` ist das Gerät, das authentifiziert wird.
4. Das Gerät nutzt das Token direkt mit IoT Hub.

Der Tokendienst kann die Gültigkeitsdauer für das Token wie gewünscht festlegen. Nach dem Ablaufen trennt IoT Hub die Verbindung, und das Gerät muss vom Tokendienst ein neues Token anfordern. Eine kurze Gültigkeitsdauer führt natürlich dazu, dass sich die Auslastung für das Gerät und den Tokendienst gleichermaßen erhöht.

Es ist wichtig, darauf hinzuweisen, dass die Geräteidentität weiterhin in IoT Hub erstellt werden muss, damit das Gerät die Verbindung herstellen kann. Dies bedeutet auch, dass die Zugriffssteuerung pro Gerät (durch das Deaktivieren der Geräteidentitäten gemäß [IoT Hub-Entwicklerleitfaden – Identitätsregistrierung][]) auch dann noch funktioniert, wenn das Gerät per Token authentifiziert wird. So müssen keine Token mit sehr langer Lebensdauer vorhanden sein.

### Vergleich mit einem benutzerdefinierten Gateway

Das Tokendienstmuster ist der empfohlene Weg zum Implementieren einer benutzerdefinierten Identitätsregistrierung bzw. eines Authentifizierungsschemas mit IoT Hub, da IoT Hub die Behandlung eines Großteils des Lösungsdatenverkehrs überlassen wird. Es gibt aber auch Fälle, in denen das benutzerdefinierte Authentifizierungsschema so eng mit dem Protokoll (z. B. [TLS-PSK][]) verknüpft ist, dass ein Dienst zum Verarbeiten des gesamten Datenverkehrs (*benutzerdefiniertes Gateway*) erforderlich ist. Weitere Informationen finden Sie im Thema [Protokollgateway][].

## Nächste Schritte

Folgen Sie diesen Links, um mehr über Azure IoT Hub zu erfahren:

- [Erste Schritte mit IoT Hubs (Tutorial)][lnk-get-started]
- [Was ist Azure IoT Hub?][]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[IoT Hub Developer Guide - identity registry]: iot-hub-devguide.md#identityregistry
[IoT Hub-Entwicklerleitfaden – Identitätsregistrierung]: iot-hub-devguide.md#identityregistry
[IoT Hub-Geräteverwaltung – Anleitung]: iot-hub-device-management.md
[IoT Hub Developer Guide - Anti-spoofing]: iot-hub-devguide.md#antispoofing
[Geräte-SDKs von Azure IoT]: iot-hub-sdks-summary.md
[IoT Hub-Entwicklerleitfaden – Auswählen des Kommunikationsprotokolls]: iot-hub-devguide.md#amqpvshttp
[IoT Hub Developer Guide - Security]: iot-hub-devguide.md#security
[IoT Hub-Entwicklerleitfaden – Sicherheit]: iot-hub-devguide.md#security
[TLS-PSK]: https://tools.ietf.org/html/rfc4279
[Protokollgateway]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Was ist Azure IoT Hub?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Oct15_HO2-->