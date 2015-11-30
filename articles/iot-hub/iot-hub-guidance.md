<properties
 pageTitle="IoT Hub-Lösung – Anleitung | Microsoft Azure"
 description="Themen mit Anleitungen zu Gateways, zur Gerätebereitstellung und zur Authentifizierung für die Entwicklung von IoT-Lösungen mithilfe von Azure IoT Hub."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/10/2015"
 ms.author="dobett"/>

# Entwerfen der Lösung

Dieser Artikel enthält Anleitungen zum Entwerfen der folgenden Funktionen in der IoT-Lösung:

- Gerätebereitstellung
- Bereichsgateways
- Geräte-Authentifizierung

## Gerätebereitstellung

IoT-Lösungen speichern Daten zu einzelnen Geräten wie z. B.:

- Geräteidentität und Authentifizierungsschlüssel
- Typ und Version der Gerätehardware
- Gerätestatus
- Softwareversionen und -funktionen
- Befehlsverlauf des Geräts

Die Gerätedaten, die eine bestimmte IoT-Lösung speichert, hängen von den spezifischen Anforderungen der Lösung ab. Aber eine Lösung muss mindestens Geräteidentitäten und Authentifizierungsschlüssel speichern. IoT Hub enthält eine [Identitätsregistrierung][lnk-devguide-identityregistry], die Werte für jedes Gerät speichern kann, wie z. B. IDs, Authentifizierungsschlüssel und Statuscodes. Eine Lösung kann andere Azure-Dienste wie Tabellen, Blobs oder DocumentDB nutzen, um zusätzliche Gerätedaten zu speichern.

Die *Gerätebereitstellung* ist der Prozess des Hinzufügens der ersten Gerätedaten zu den Speichern in Ihrer Lösung. Damit ein neues Gerät eine Verbindung mit Ihrem Hub herstellen kann, müssen Sie eine neue Geräte-ID und Schlüssel zur [IoT Hub-Identitätsregistrierung][lnk-devguide-identityregistry] hinzufügen. Im Rahmen des Bereitstellungsprozesses müssen Sie möglicherweise gerätespezifische Daten in anderen Lösungsspeichern initialisieren.

Im Artikel [Anleitung für die IoT Hub-Geräteverwaltung][lnk-device-management] werden einige allgemeine Strategien für die Gerätebereitstellung beschrieben. Mit den [IoT Hub-Identitätsregistrierungs-APIs][lnk-devguide-identityregistry] können Sie IoT Hub in Ihren Bereitstellungsprozess integrieren.

## Bereichsgateways

In einer IoT-Lösung befindet sich das *Bereichsgateway* zwischen Ihren Geräten und IoT Hub, normalerweise in der Nähe Ihrer Geräte. Ihre Geräte kommunizieren direkt mit dem Bereichsgateway. Dazu wird ein Protokoll verwendet, dass von den Geräten unterstützt wird. Das Bereichsgateway kommuniziert mit IoT Hub mithilfe eines Protokolls, das von IoT Hub unterstützt wird. Ein Bereichsgateway kann ein spezialisiertes Einzelgerät oder eine Software sein, die auf vorhandener Hardware ausgeführt wird.

Ein Bereichsgateway unterscheidet sich von einem einfachen Gerät für das Routing von Datenverkehr (z. B. einem NAT-Gerät oder einer Firewall), da es üblicherweise eine aktive Rolle bei der Verwaltung des Zugriffs und des Informationsflusses in der Lösung hat. Beispielsweise kann ein Bereichsgateway folgende Aufgaben ausführen:

- Verwalten von lokalen Geräten. Ein Bereichsgateway könnte beispielsweise das Verarbeiten von Ereignisregeln und das Senden von Befehlen an Geräte als Antwort auf bestimmte Telemetriedaten durchführen.
- Filtern oder Aggregieren von Telemetriedaten, bevor sie an IoT Hub weiterleitet werden. Dies kann die Datenmenge verringern, die an IoT Hub gesendet wird, und möglicherweise die Kosten der Lösung reduzieren.
- Helfen beim Bereitstellen von Geräten.
- Transformieren von Telemetriedaten, um die Verarbeitung in Ihrem Lösungs-Back-End zu ermöglichen.
- Ausführen von Protokollübersetzungen, um Geräten die Kommunikation mit IoT Hub zu ermöglichen, selbst wenn sie nicht die Transportprotokolle verwenden, die IoT Hub unterstützt.

> [AZURE.NOTE]Während Sie in der Regel ein Bereichsgateway lokal auf Ihren Geräten bereitstellen, könnten Sie in einigen Szenarien ein [Protokollgateway][lnk-gateway] in der Cloud bereitstellen.

### Typen von Bereichsgateways

Ein Bereichsgateway kann *transparent* oder *nicht transparent* sein:

| &nbsp; | Transparent | Nicht transparent |
|--------|-------------|--------|
| In der IoT Hub-Identitätsregistrierung gespeicherte Identitäten | Alle verbundenen Geräte | Nur die Identität des Bereichsgateways |
| IoT Hub kann [Schutz vor Spoofing im Hinblick auf Geräteidentitäten][lnk-devguide-antispoofing] bieten | Ja | Nein |
| [Drosselungen und Kontingente][lnk-throttles-quotas] | Auf jedes Gerät anwenden | Auf das Bereichsgateway anwenden |

### Weitere Überlegungen

Für die Implementierung eines Bereichsgateways können Sie die [Geräte-SDKs von Azure IoT][lnk-device-sdks] verwenden. Einige Geräte-SDKs bieten bestimmte Funktionen, die Sie beim Implementieren eines Bereichsgateways unterstützen, z. B. die Möglichkeit, die Kommunikation von mehreren Geräten über eine Verbindung mit IoT Hub im Multiplexverfahren zu übertragen. Wie unter [IoT Hub-Entwicklerleitfaden – Auswählen des Kommunikationsprotokolls][lnk-devguide-protocol] beschrieben, sollten Sie die Verwendung von HTTP/1 als Transportprotokoll für Bereichsgateways vermeiden.

## Benutzerdefinierte Geräteauthentifizierung

Mit der [Geräteidentitätsregistrierung][lnk-devguide-identityregistry] von IoT Hub können Sie Sicherheitsanmeldeinformationen und die Zugriffssteuerung pro Gerät konfigurieren. Auch wenn eine IoT-Lösung bereits erheblichen Anteil an einer benutzerdefinierten Geräteidentitätsregistrierung bzw. einem Authentifizierungsschema hat, können Sie diese vorhandene Infrastruktur in IoT Hub integrieren, indem ein *Tokendienst* erstellt wird. Auf diese Weise können Sie andere IoT-Features in der Lösung nutzen.

Der Tokendienst ist ein benutzerdefinierter Clouddienst, bei dem eine IoT Hub-*SAS-Richtlinie* mit **DeviceConnect**-Berechtigungen verwendet wird, um Token mit *Gerätebereich* zu erstellen, durch die ein Gerät eine Verbindung mit IoT Hub herstellen kann.

  ![][img-tokenservice]

Dies sind die wichtigsten Schritte des Tokendienstmusters:

1. Erstellen Sie eine [IoT Hub-SAS-Richtlinie][lnk-devguide-security] mit **DeviceConnect**-Berechtigungen für IoT Hub. Sie können diese Richtlinie im [Vorschauportal][lnk-preview-portal] oder programmgesteuert erstellen. Diese Richtlinie wird vom Tokendienst zum Signieren der von ihm erstellten Token verwendet.
2. Wenn ein Gerät auf IoT Hub zugreifen muss, fordert es von Ihrem Tokendienst ein signiertes Token an. Das Gerät kann mit Ihrer benutzerdefinierten Geräteidentitätsregistrierung bzw. mit dem Authentifizierungsschema authentifiziert werden, um die Geräteidentität zu ermitteln, die der Tokendienst verwendet, um das Token zu erstellen.
3. Der Tokendienst gibt ein Token zurück, das gemäß [IoT Hub-Entwicklerleitfaden – Sicherheit][lnk-devguide-security] erstellt wird, wobei `/devices/{deviceId}` als `resourceURI` verwendet wird. `deviceId` ist das Gerät, das authentifiziert wird. Der Tokendienst verwendet die SAS-Richtlinie, um das Token zu erstellen.
4. Das Gerät nutzt das Token direkt mit IoT Hub.

> [AZURE.NOTE]Sie können die .NET-Klasse [SharedAccessSignatureBuilder][lnk-dotnet-sas] oder die Java-Klasse [IotHubServiceSasToken][lnk-java-sas] zum Erstellen eines Tokens im Tokendienst verwenden.

Der Tokendienst kann die Gültigkeitsdauer für das Token wie gewünscht festlegen. Nach dem Ablauf des Tokens trennt IoT Hub die Verbindung mit dem Gerät, und das Gerät muss vom Tokendienst ein neues Token anfordern. Wenn Sie eine kurze Ablaufzeit verwenden, erhöht sich die Last für das Gerät und den Tokendienst gleichermaßen.

Damit ein Gerät eine Verbindung mit Ihrem Hub herstellen kann, müssen Sie es zur IoT Hub-Geräteidentitätsregistrierung hinzufügen, auch wenn das Gerät für die Verbindung ein Token und keinen Geräteschlüssel verwendet. Aus diesem Grund können Sie weiterhin die Zugriffssteuerung pro Gerät nutzen, indem Sie Geräteidentitäten in der [IoT Hub-Identitätsregistrierung][lnk-devguide-identityregistry] aktivieren oder deaktivieren, wenn sich das Gerät mit einem Token authentifiziert. Dies verringert die Risiken der Verwendung von Token mit langen Ablaufzeiten.

### Vergleich mit einem benutzerdefinierten Gateway

Das Tokendienstmuster ist der empfohlene Weg zum Implementieren einer benutzerdefinierten Identitätsregistrierung bzw. eines Authentifizierungsschemas mit IoT Hub, da IoT Hub weiterhin die Verarbeitung eines Großteils des Lösungsdatenverkehrs übernimmt. Es gibt aber auch Fälle, in denen das benutzerdefinierte Authentifizierungsschema so eng mit dem Protokoll (z. B. [TLS-PSK][lnk-tls-psk]) verknüpft ist, dass ein Dienst zum Verarbeiten des gesamten Datenverkehrs (*benutzerdefiniertes Gateway*) erforderlich ist. Weitere Informationen finden Sie im Thema [Protokollgateway][lnk-gateway].

## Nächste Schritte

Folgen Sie diesen Links, um mehr über Azure IoT Hub zu erfahren:

- [Erste Schritte mit IoT Hub (Lernprogramm)][lnk-get-started]
- [Was ist Azure IoT Hub?][lnk-what-is-hub]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-device-management]: iot-hub-device-management.md

[lnk-device-sdks]: iot-hub-sdks-summary.md
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-gateway]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-what-is-hub]: iot-hub-what-is-iot-hub.md
[lnk-preview-portal]: https://portal.azure.com
[lnk-throttles-quotas]: ../azure-subscription-service-limits.md/#iot-hub-limits
[lnk-devguide-antispoofing]: iot-hub-devguide.md#antispoofing
[lnk-devguide-protocol]: iot-hub-devguide.md#amqpvshttp
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html

<!---HONumber=Nov15_HO4-->