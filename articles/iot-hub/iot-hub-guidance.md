<properties
 pageTitle="IoT Hub-Lösung – Anleitung | Microsoft Azure"
 description="Themen mit Anleitungen zu Gateways, zur Gerätebereitstellung und zur Authentifizierung für die Entwicklung von IoT-Lösungen mithilfe von Azure IoT Hub."
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
 ms.date="02/03/2016"
 ms.author="dobett"/>

# Entwerfen der Lösung

Dieser Artikel enthält Anleitungen zum Entwerfen der folgenden Funktionen in der IoT-Lösung (Internet of Things, Internet der Dinge):

- Gerätebereitstellung
- Bereichsgateways
- Geräte-Authentifizierung

## Gerätebereitstellung

IoT-Lösungen speichern Daten zu einzelnen Geräten wie z. B.:

- Geräteidentität und Authentifizierungsschlüssel
- Typ und Version der Gerätehardware
- Gerätestatus
- Softwareversionen und -funktionen
- Befehlsverlauf des Geräts

Die Gerätedaten, die von einer bestimmten IoT-Lösung gespeichert werden, richten sich nach den jeweiligen Anforderungen der Lösung. Von einer Lösung müssen aber mindestens die Geräteidentitäten und Authentifizierungsschlüssel gespeichert werden. Azure IoT Hub enthält eine [Identitätsregistrierung][lnk-devguide-identityregistry], die Werte für jedes Gerät speichern kann, z. B. IDs, Authentifizierungsschlüssel und Statuscodes. Eine Lösung kann andere Azure-Dienste wie Tabellen, Blobs oder Azure DocumentDB nutzen, um zusätzliche Gerätedaten zu speichern.

Die *Gerätebereitstellung* ist der Prozess des Hinzufügens der ersten Gerätedaten zu den Speichern in Ihrer Lösung. Damit ein neues Gerät eine Verbindung mit Ihrem Hub herstellen kann, müssen Sie der [IoT Hub-Identitätsregistrierung][lnk-devguide-identityregistry] eine neue Geräte-ID und Schlüssel hinzufügen. Im Rahmen des Bereitstellungsprozesses müssen Sie unter Umständen gerätespezifische Daten in anderen Lösungsspeichern initialisieren.

Im Artikel [Anleitung für die IoT Hub-Geräteverwaltung][lnk-device-management] werden einige allgemeine Strategien für die Gerätebereitstellung beschrieben. Mit den [IoT Hub-Identitätsregistrierungs-APIs][lnk-devguide-identityregistry] können Sie IoT Hub in Ihren Bereitstellungsprozess integrieren.

## Bereichsgateways

Bei einer IoT-Lösung ist zwischen Ihren Geräten und Ihrem IoT Hub ein *Bereichsgateway* angeordnet. Es befindet sich normalerweise in der Nähe Ihrer Geräte. Ihre Geräte kommunizieren direkt mit dem Bereichsgateway, indem sie ein von den Geräten unterstütztes Protokoll nutzen. Das Bereichsgateway kommuniziert mit IoT Hub über ein Protokoll, das von IoT Hub unterstützt wird. Ein Bereichsgateway kann ein spezialisiertes Einzelgerät oder eine Software sein, die auf vorhandener Hardware ausgeführt wird.

Ein Bereichsgateway unterscheidet sich von einem einfachen Gerät für das Routing von Datenverkehr (z. B. einem Gerät für die Netzwerkadressübersetzung (NAT) oder einer Firewall), da es üblicherweise eine aktive Rolle bei der Verwaltung des Zugriffs und des Informationsflusses in der Lösung hat. Beispielsweise kann ein Bereichsgateway folgende Aufgaben ausführen:

- Verwalten von lokalen Geräten. Ein Bereichsgateway könnte beispielsweise das Verarbeiten von Ereignisregeln und das Senden von Befehlen an Geräte als Antwort auf bestimmte Telemetriedaten durchführen.
- Filtern oder Aggregieren von Telemetriedaten, bevor sie an IoT Hub weiterleitet werden. Dies kann die Datenmenge verringern, die an IoT Hub gesendet wird, und möglicherweise die Kosten der Lösung reduzieren.
- Helfen beim Bereitstellen von Geräten.
- Transformieren von Telemetriedaten, um die Verarbeitung in Ihrem Lösungs-Back-End zu ermöglichen.
- Ausführen von Protokollübersetzungen, um Geräten die Kommunikation mit IoT Hub zu ermöglichen, selbst wenn sie nicht die Transportprotokolle verwenden, die IoT Hub unterstützt.

> [AZURE.NOTE] In der Regel stellen Sie ein Bereichsgateway lokal auf Ihren Geräten bereit, aber Sie können in einigen Szenarien auch ein [Protokollgateway][lnk-gateway] in der Cloud bereitstellen.

### Typen von Bereichsgateways

Ein Bereichsgateway kann *transparent* oder *nicht transparent* sein:

| &nbsp; | Transparentes Gateway | Nicht transparentes Gateway|
|--------|-------------|--------|
| In der IoT Hub-Identitätsregistrierung gespeicherte Identitäten | Identitäten aller angeschlossenen Geräte | Nur die Identität des Bereichsgateways |
| IoT Hub kann [Schutz vor Spoofing im Hinblick auf Geräteidentitäten][lnk-devguide-antispoofing] bieten | Ja | Nein |
| [Drosselungen und Kontingente][lnk-throttles-quotas] | Auf jedes Gerät anwenden | Auf das Bereichsgateway anwenden |

> [AZURE.IMPORTANT]  Bei Verwenden eines nicht transparenten Gatewaymusters nutzen alle Geräte, die sich über dieses Gateway verbinden, dieselbe C2D-Warteschlange, die maximal 50 Nachrichten enthalten darf. Daraus folgt, dass das nicht transparente Gatewaymuster nur dann verwendet werden sollte, wenn sehr wenige Geräte eine Verbindung über die einzelnen Bereichsgateways herstellen und ihr C2D-Datenverkehr niedrig ist.

### Weitere Überlegungen

Für die Implementierung eines Bereichsgateways können Sie die [Geräte-SDKs von Azure IoT][lnk-device-sdks] verwenden. Einige Geräte-SDKs bieten bestimmte Funktionen, die Sie beim Implementieren eines Bereichsgateways unterstützen, z. B. die Möglichkeit, die Kommunikation von mehreren Geräten über eine Verbindung mit IoT Hub im Multiplexverfahren zu übertragen. Wie unter [IoT Hub-Entwicklerleitfaden – Auswählen des Kommunikationsprotokolls][lnk-devguide-protocol] beschrieben, sollten Sie die Verwendung von HTTP/1 als Transportprotokoll für Bereichsgateways vermeiden.

## Benutzerdefinierte Geräteauthentifizierung

Mit der [Geräteidentitätsregistrierung][lnk-devguide-identityregistry] von IoT Hub können Sie Sicherheitsanmeldeinformationen und die Zugriffssteuerung pro Gerät konfigurieren. Auch wenn eine IoT-Lösung bereits erheblichen Anteil an einer benutzerdefinierten Geräteidentitätsregistrierung bzw. einem Authentifizierungsschema hat, können Sie diese vorhandene Infrastruktur in IoT Hub integrieren, indem ein *Tokendienst* erstellt wird. Auf diese Weise können Sie andere IoT-Features in der Lösung nutzen.

Ein Tokendienst ist ein benutzerdefinierter Clouddienst. Er nutzt eine *SAS-Richtlinie* von IoT Hub mit **DeviceConnect**-Berechtigungen, um Token mit *Gerätebereich* zu erstellen. Mit diesen Token kann ein Gerät eine Verbindung mit Ihrem IoT Hub herstellen.

  ![Schritte des Tokendienstmusters][img-tokenservice]

Dies sind die wichtigsten Schritte des Tokendienstmusters:

1. Erstellen Sie eine [IoT Hub-SAS-Richtlinie][lnk-devguide-security] mit **DeviceConnect**-Berechtigungen für IoT Hub. Sie können diese Richtlinie im [Azure-Portal][lnk-portal] oder programmgesteuert erstellen. Diese Richtlinie wird vom Tokendienst zum Signieren der von ihm erstellten Token verwendet.
2. Wenn ein Gerät auf IoT Hub zugreifen muss, fordert es von Ihrem Tokendienst ein signiertes Token an. Das Gerät kann mit Ihrer benutzerdefinierten Geräteidentitätsregistrierung bzw. mit dem Authentifizierungsschema authentifiziert werden, um die Geräteidentität zu ermitteln, die der Tokendienst zum Erstellen des Tokens verwendet.
3. Der Tokendienst gibt ein Token zurück. Das Token wird gemäß [Abschnitt über Sicherheit im IoT Hub-Entwicklerleitfaden][lnk-devguide-security] erstellt, indem `/devices/{deviceId}` als `resourceURI` verwendet wird. `deviceId` ist hierbei das zu authentifizierende Gerät. Der Tokendienst verwendet die SAS-Richtlinie, um das Token zu erstellen.
4. Das Gerät nutzt das Token direkt mit IoT Hub.

> [AZURE.NOTE] Sie können die .NET-Klasse [SharedAccessSignatureBuilder][lnk-dotnet-sas] oder die Java-Klasse [IotHubServiceSasToken][lnk-java-sas] zum Erstellen eines Tokens im Tokendienst verwenden.

Der Tokendienst kann die Gültigkeitsdauer für das Token wie gewünscht festlegen. Wenn das Token abläuft, trennt IoT Hub die Geräteverbindung. Das Gerät muss dann ein neues Token vom Tokendienst anfordern. Wenn Sie eine kurze Ablaufzeit verwenden, erhöht sich die Last für das Gerät und den Tokendienst gleichermaßen.

Damit ein Gerät eine Verbindung mit Ihrem Hub herstellen kann, müssen Sie es der IoT Hub-Geräteidentitätsregistrierung hinzufügen – auch wenn das Gerät für die Verbindung ein Token und keinen Geräteschlüssel verwendet. Aus diesem Grund können Sie weiterhin die Zugriffssteuerung pro Gerät nutzen, indem Sie Geräteidentitäten in der [IoT Hub-Identitätsregistrierung][lnk-devguide-identityregistry] aktivieren oder deaktivieren, wenn sich das Gerät mit einem Token authentifiziert. Dies verringert die Risiken der Verwendung von Token mit langen Ablaufzeiten.

### Vergleich mit einem benutzerdefinierten Gateway

Das Tokendienstmuster ist der empfohlene Weg zur Implementierung einer benutzerdefinierten Identitätsregistrierung bzw. eines Authentifizierungsschemas mit IoT Hub. Dies wird empfohlen, da der größte Teil des Lösungsdatenverkehrs weiterhin über IoT Hub abgewickelt wird. Es gibt aber auch Fälle, in denen das benutzerdefinierte Authentifizierungsschema so eng mit dem Protokoll verknüpft ist, dass ein Dienst zum Verarbeiten des gesamten Datenverkehrs (*benutzerdefiniertes Gateway*) erforderlich ist. Beispiele hierfür sind [Transport Layer Security (TLS) und vorinstallierte Schlüssel (PSKs)][lnk-tls-psk]. Weitere Informationen finden Sie im Thema [Protokollgateway][lnk-gateway].

## Gerätetakt <a id="heartbeat"></a>

Die [IoT Hub-Identitätsregistrierung][lnk-devguide-identityregistry] enthält das Feld **connectionState**. Sie dürfen das Feld **connectionState** nur während der Entwicklung und des Debuggings verwenden. IoT-Lösungen sollten das Feld zur Laufzeit nicht abfragen (um beispielsweise zum Prüfen, ob ein Gerät verbunden ist, um zu bestimmen, ob eine C2D-Nachricht oder SMS gesendet werden sollte). Wenn Ihre IoT-Lösung wissen muss, ob ein Gerät verbunden ist (entweder zur Laufzeit oder mit höherer Genauigkeit als von der **connectionState**-Eigenschaft ermöglicht), muss Ihre Lösung das *Taktmuster* implementieren.

Beim Taktmuster sendet das Gerät D2C-Nachrichten mindestens einmal pro festgelegtem Zeitraum (z. B. mindestens einmal pro Stunde). Dies bedeutet, dass selbst wenn ein Gerät keine zu sendenden Daten hat, es dennoch eine leere D2C-Nachricht sendet (in der Regel mit einer Eigenschaft, die sie als Takt identifiziert). Auf Dienstseite verwaltet die Lösung eine Zuordnung mit dem letzten für jedes Gerät empfangenen Takts und nimmt an, dass es ein Problem mit einem Gerät gibt, wenn es innerhalb des erwarteten Zeitraums keine Taktnachricht empfängt.

Eine komplexere Implementierung kann die Informationen aus der [Vorgangsüberwachung][lnk-devguide-opmon] enthalten, um Geräte zu bestimmen, die erfolglos versuchen, eine Verbindung herzustellen oder zu kommunizieren. Wenn Sie das Taktmuster implementieren, sollten Sie [IoT Hub-Kontingente und -Drosselungen][] überprüfen.

> [AZURE.NOTE] Wenn für eine IoT-Lösung der Geräteverbindungsstatus ausschließlich dazu benötigt wird, um zu bestimmen, ob C2D-Nachrichten gesendet werden müssen, und wenn Nachrichten nicht an große Gruppen von Geräten übertragen werden, sollten Sie in Betracht ziehen, eine kurze Ablaufzeit zu verwenden. Dadurch wird dasselbe, aber deutlich effizientere Ergebnis erzielt, als wenn die Registrierung des Geräteverbindungsstatus mit dem Taktmuster aufrechterhalten wird. Es ist auch durch die Anforderung von Nachrichtenbestätigungen möglich, vom IoT Hub darüber benachrichtigt zu werden, von welchen Geräten Nachrichten empfangen werden können und welche nicht online oder ausgefallen sind. Weitere Informationen zu C2D-Nachrichten finden Sie im [Entwicklungsleitfaden für IoT Hub][lnk-devguide-messaging].

## Nächste Schritte

Folgen Sie diesen Links, um mehr über Azure IoT Hub zu erfahren:

- [Erste Schritte mit IoT Hub (Lernprogramm)][lnk-get-started]
- [Was ist Azure IoT Hub?][lnk-what-is-hub]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-device-management]: iot-hub-device-management.md
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-device-sdks]: iot-hub-sdks-summary.md
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-gateway]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-what-is-hub]: iot-hub-what-is-iot-hub.md
[lnk-portal]: https://portal.azure.com
[lnk-throttles-quotas]: ../azure-subscription-service-limits.md/#iot-hub-limits
[lnk-devguide-antispoofing]: iot-hub-devguide.md#antispoofing
[lnk-devguide-protocol]: iot-hub-devguide.md#amqpvshttp
[lnk-devguide-messaging]: iot-hub-devguide.md#messaging
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[IoT Hub-Kontingente und -Drosselungen]: iot-hub-devguide.md#throttling

<!---HONumber=AcomDC_0316_2016-->