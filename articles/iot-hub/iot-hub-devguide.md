<properties
 pageTitle="Entwicklungsleitfaden für IoT Hub | Microsoft Azure"
 description="In diesem Entwicklungsleitfaden für Azure IoT Hub werden Themen wie IoT Hub-Endpunkte, Sicherheit, Geräteidentitätsregistrierung und Messaging abgedeckt."
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="01/07/2016"
 ms.author="dobett"/>

# Entwicklungsleitfaden für Azure IoT Hub

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von IoT-Geräten und einem Anwendungs-Back-End ermöglicht.

Azure IoT Hub ermöglicht Folgendes:

* Eine sichere Kommunikation unter Verwendung von Zugriffssteuerung und Sicherheitsanmeldeinformationen auf Gerätebasis
* Zuverlässiges, hyperskalierbares Messaging zwischen Cloud und Gerät (Cloud-to-Device, C2D) sowie zwischen Gerät und Cloud (Device-to-Cloud, D2C)
* Eine problemlose Geräteanbindung mithilfe von Gerätebibliotheken für die gängigsten Sprachen und Plattformen

In diesem Artikel werden die folgenden Themen behandelt:

- [Endpunkte](#endpoints). In diesem Abschnitt werden die verschiedenen Endpunkte beschrieben, die jeder IoT Hub für Laufzeit- und Verwaltungsvorgänge bereitstellt.
- [Geräteidentitätsregistrierung](#device-identity-registry). Dieser Abschnitt beschreibt, welche Informationen in jeder IoT Hub-Geräteidentitätsregistrierung gespeichert werden und wie Sie auf die Informationen zugreifen und sie ändern können.
- [Sicherheit](#security). In diesem Abschnitt wird das Sicherheitsmodell beschrieben, mit dem sowohl für Geräte als auch für Cloudkomponenten Zugriff auf IoT Hub-Funktionalität gewährt werden.
- [Messaging](#messaging). In diesem Abschnitt werden die Messagingfunktionen (D2C und C2D) beschrieben, die von IoT Hub verfügbar gemacht werden.
- [Kontingente und Drosselung](#throttling). In diesem Abschnitt werden die Kontingente zusammengefasst, die bei der Verwendung von IoT Hub gelten.

## Endpunkte <a id="endpoints"></a>

Azure IoT Hub ist ein mehrinstanzenfähiger Dienst, der seine Funktionen für zahlreiche Akteure bereitstellt. Das nachstehende Diagramm zeigt die verschiedenen Endpunkte, die von IoT Hub verfügbar gemacht werden.

![IoT Hub-Endpunkte][img-endpoints]

Im Folgenden finden Sie eine Beschreibung der Endpunkte:

* **Ressourcenanbieter**: Der IoT Hub-Ressourcenanbieter legt eine [Azure-Ressourcen-Manager][lnk-arm]-Schnittstelle offen, mit der Azure-Abonnementbesitzer IoT Hubs erstellen, IoT Hub-Eigenschaften aktualisieren und IoT Hubs löschen können. IoT Hub-Eigenschaften steuern im Gegensatz zur Zugriffssteuerung auf Geräteebene (siehe [Zugriffssteuerung](#accesscontrol) weiter unten) Sicherheitsrichtlinien auf Hubebene und funktionale Optionen für das C2D- und D2C-Messaging. Der Ressourcenanbieter ermöglicht außerdem das [Exportieren von Geräteidentitäten](#importexport).
* **Geräteidentitätsverwaltung**: Jeder IoT Hub legt einen Satz an HTTP-REST-Endpunkten zum Verwalten von Geräteidentitäten offen (zum Erstellen, Abrufen, Aktualisieren und Löschen). Geräteidentitäten werden zur Geräteauthentifizierung und für die Zugriffssteuerung eingesetzt. Weitere Informationen finden Sie unter [Geräteidentitätsregistrierung](#device-identity-registry).
* **Geräteendpunkte**: Für jedes Gerät, das in der Geräteidentitätsregistrierung bereitgestellt wird, stellt IoT Hub einen Satz an Endpunkten zur Verfügung, der von einem Gerät zum Senden und Empfangen von Nachrichten verwendet wird. Diese Endpunkte werden aktuell bei Verwendung des HTTP- und des [AMQP][lnk-amqp]-Protokolls verfügbar gemacht:
    - *Senden von D2C-Nachrichten*. Verwenden Sie diesen Endpunkt, um D2C-Nachrichten zu senden. Weitere Informationen finden Sie unter [Messaging zwischen Gerät und Cloud](#d2c).
    - *Empfangen von C2D-Nachrichten*. Ein Gerät verwendet diesen Endpunkt, um gezielte C2D-Nachrichten zu empfangen. Weitere Informationen finden Sie unter [Messaging zwischen Cloud und Gerät](#c2d).
* **Dienstendpunkte**: Jeder IoT Hub legt einen Satz an Endpunkten offen, die von Ihrem Anwendungs-Back-End zur Kommunikation mit Ihren Geräten verwendet werden können. Diese Endpunkte werden aktuell nur bei Verwendung des [AMQP][lnk-amqp]-Protokolls verfügbar gemacht.
    - *Empfangen von D2C-Nachrichten*. Dieser Endpunkt ist kompatibel mit [Azure Event Hubs][lnk-event-hubs]. Er kann von einem Back-End-Dienst dazu verwendet werden, alle D2C-Nachrichten zu lesen, die von Ihren Geräten gesendet werden. Weitere Informationen finden Sie unter [Messaging zwischen Gerät und Cloud](#d2c).
    - *Senden von C2D-Nachrichten und Empfangen von Übermittlungsbestätigungen*. Diese Endpunkte ermöglichen Ihrem Anwendungs-Back-End das Senden von zuverlässigen C2D-Nachrichten sowie das Empfangen zugehöriger Übermittlungs- oder Ablaufbestätigungen. Weitere Informationen finden Sie unter [Messaging zwischen Cloud und Gerät](#c2d).

Im Artikel [IoT Hub-APIs und -SDKs][lnk-apis-sdks] werden die verschiedenen Möglichkeiten zum Zugriff auf diese Endpunkte beschrieben.

Schließlich ist es wichtig zu wissen, dass alle IoT Hub-Endpunkte das [TLS][lnk-tls]-Protokoll nutzen und dass Endpunkte niemals auf unverschlüsselten/unsicheren Kanälen offengelegt werden.

### Lesen von Daten aus Event Hubs-kompatiblen Endpunkten <a id="eventhubcompatible"></a>

Wenn Sie das [Azure Service Bus SDK für .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) oder den [Event Hubs-Ereignisprozessorhost][] verwenden, können Sie eine beliebige IoT Hub-Verbindungszeichenfolge mit den richtigen Berechtigungen verwenden und anschließend **messages/events** als Event Hub-Name nutzen.

Bei der Verwendung von SDKs (oder Produktintegrationen), die nicht IoT Hub-fähig sind, müssen Sie einen Event Hubs-kompatiblen Endpunkt und den Event Hub-Namen aus den IoT Hub-Einstellungen im [Azure-Portal][] abrufen:

1. Klicken Sie auf dem IoT Hub-Blatt auf **Einstellungen** und anschließend auf **Messaging**.
2. Im Abschnitt **D2C-Einstellungen** befinden sich die Werte **Event Hub-kompatibler Endpunkt**, **Event Hub-kompatibler Name** und **Partitionen**.

    ![][img-eventhubcompatible]

> [AZURE.NOTE] Manchmal erfordert das SDK einen Wert für **Hostname** oder **Namespace**. Entfernen Sie in diesem Fall das Schema aus dem **Event Hub-kompatiblen Endpunkt**. Wenn es sich bei Ihrem Event Hub-kompatiblen Endpunkt beispielsweise um **sb://iothub-ns-myiothub-1234.servicebus.windows.net/** handelt, lautet der **Hostname** **iothub-ns-myiothub-1234.servicebus.windows.net** und der **Namespace** **iothub-ns-myiothub-1234**.

Sie können in diesem Fall eine beliebige gemeinsam genutzte Sicherheitsrichtlinie mit den **ServiceConnect**-Berechtigungen zur Verbindungsherstellung mit dem angegebenen Event Hub verwenden.

Wenn Sie eine Event Hub-Verbindungszeichenfolge mit den zuvor angegebenen Informationen erstellen müssen, verwenden Sie das folgende Muster:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

Nachfolgend finden Sie eine Liste der SDKs und Integrationen, die Sie mit Event Hub-kompatiblen Endpunkten verwenden können, die IoT Hub verfügbar macht:

* [Java-Event Hubs-Client](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm-Spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md), Sie können sich die [Spoutquelle](https://github.com/apache/storm/tree/master/external/storm-eventhubs) bei GitHub ansehen.
* [Apache Spark-Integration](../hdinsight/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

## Geräteidentitätsregistrierung

Jeder IoT Hub verfügt über eine Geräteidentitätsregistrierung, die Sie zum Erstellen von gerätebasierten Ressourcen im Dienst verwenden können. Hierzu zählt beispielsweise eine Warteschlange mit gesendeten C2D-Nachrichten. Außerdem wird der Zugriff auf geräteseitige Endpunkte ermöglicht, wie im Abschnitt [Zugriffssteuerung](#accesscontrol) beschrieben.

Auf höherer Ebene ist die Geräteidentitätsregistrierung eine RESTful-fähige Sammlung von Geräteidentitätsressourcen. In den folgenden Abschnitten werden die Eigenschaften von Geräteidentitätsressourcen sowie die Vorgänge beschrieben, die über die Registrierung für Identitäten ausgeführt werden können.

> [AZURE.NOTE] Nähere Informationen zum HTTP-Protokoll und den SDKs, die Sie für die Interaktion mit der Geräteidentitätsregistrierung verwenden können, finden Sie unter [IoT Hub-APIs und -SDKs][lnk-apis-sdks].

### Geräteidentitätseigenschaften <a id="deviceproperties"></a>

Geräteidentitäten werden als JSON-Dokumente mit den folgenden Eigenschaften dargestellt.

| Eigenschaft | Optionen | Beschreibung |
| -------- | ------- | ----------- |
| deviceId | erforderlich, bei Aktualisierungen schreibgeschützt | Eine Zeichenfolge mit Berücksichtigung von Klein-/Großschreibung (bis zu 128 Zeichen lang), die aus alphanumerischen ASCII-Zeichen (7 Bit) + `{'-', ':', '.', '+', '&percnt;', '_', '&num;', '&ast;', '?', '!', '(', ')', ',', '=', '&commat;', ';', '&dollar;', '''}` besteht. |
| generationId | erforderlich, schreibgeschützt | Eine vom Hub generierte Zeichenfolge mit Berücksichtigung von Klein-/Großschreibung, die bis zu 128 Zeichen lang ist. Diese Zeichenfolge dient zur Unterscheidung von Geräten mit derselben **deviceId**, wenn diese gelöscht und neu erstellt wurden. |
| etag | erforderlich, schreibgeschützt | Eine Zeichenfolge, die ein schwaches Etag für die Geräteidentität gemäß [RFC7232][lnk-rfc7232] darstellt.|
| auth | optional | Ein zusammengesetztes Objekt, das Authentifizierungsinformationen und Sicherheitsdaten enthält. |
| auth.symkey | optional | Ein zusammengesetztes Objekt, das einen primären und einen sekundären Schlüssel enthält, die im Base64-Format gespeichert sind. |
| status | erforderlich | Kann **Aktiviert** oder **Deaktiviert** lauten. Sofern der Status **Aktiviert** lautet, kann das Gerät eine Verbindung herstellen. Lautet die Einstellung **Deaktiviert**, kann dieses Gerät auf keinen geräteseitigen Endpunkt zugreifen. |
| statusReason | optional | Eine 128 Zeichen lange Zeichenfolge, die die Ursache des Geräteidentitätsstatus speichert. Alle UTF-8-Zeichen sind zulässig. |
| statusUpdateTime | schreibgeschützt | Datum und Uhrzeit der letzten Statusaktualisierung. |
| connectionState | schreibgeschützt | **Verbunden** oder **Getrennt**; repräsentiert den Geräteverbindungsstatus aus IoT Hub-Sicht. |
| connectionStateUpdatedTime | schreibgeschützt | Datum und Uhrzeit der letzten Aktualisierung des Verbindungsstatus. |
| lastActivityTime | schreibgeschützt | Datum und Uhrzeit der letzten Geräteverbindung oder der Zeitpunkt, zu dem das letzte Mal eine Nachricht empfangen oder gesendet wurde. |

> [AZURE.NOTE]Der Verbindungsstatus kann nur den Status der Verbindung aus Sicht von IoT Hub widerspiegeln. Aktualisierungen dieser Statusanzeige können sich je nach Netzwerkbedingungen und -konfigurationen verzögern.

### Geräteidentitätsvorgänge

Die IoT Hub-Geräteidentitätsregistrierung macht die folgenden Vorgänge verfügbar:

* Erstellen einer Geräteidentität
* Aktualisieren einer Geräteidentität
* Abrufen von Geräteidentitäten nach ID
* Löschen einer Geräteidentität
* Auflisten von bis zu 1.000 Identitäten

Sämtliche der oben aufgeführten Vorgänge erlauben die Verwendung optimistischer Nebenläufigkeit gemäß [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT] Die einzige Möglichkeit zum Abrufen aller Identitäten in der Identitätsregistrierung eines Hubs besteht darin, die Funktion [Export](#importexport) zu verwenden.

Für die Geräteidentitätsregistrierung eines IoT Hubs gilt Folgendes:
- Sie enthält keine Anwendungsmetadaten.
- Der Zugriff kann wie bei einem Wörterbuch über **deviceId** als Schlüssel erfolgen.
- Es werden keine Abfragen mit Ausdrücken unterstützt.

Eine IoT-Lösung verfügt in der Regel über einen lösungsspezifischen Speicher mit anwendungsspezifischen Metadaten. Der lösungsspezifische Speicher in einer Lösung für intelligente Gebäude würde beispielsweise den Raum enthalten, in dem ein Temperatursensor bereitgestellt wird.

### Deaktivieren von Geräten

Sie können Geräte deaktivieren, indem Sie die Eigenschaft **status** für eine Identität in der Registrierung aktualisieren. Typischerweise wird dieser Ansatz in zwei Szenarien verwendet:

- Während eines Vorgangs zur Bereitstellungsorchestrierung. Weitere Informationen finden Sie unter [Entwerfen der Lösung – Gerätebereitstellung][lnk-guidance-provisioning].
- Wenn aus einem beliebigen Grund die Sicherheit eines Geräts als gefährdet eingestuft oder das Gerät nicht mehr als autorisiert betrachtet wird.

### Exportieren von Geräteidentitäten <a id="importexport"></a>

Exportvorgänge sind Aufträge mit langer Ausführungszeit, die einen vom Kunden bereitgestellten Blobcontainer zum Lesen und Schreiben von Geräteidentitätsdaten verwenden.

Sie können einen Massenexport von Geräteidentitäten aus der Identitätsregistrierung eines IoT Hubs durchführen. Hierbei werden asynchrone Vorgänge im [Endpunkt des IoT Hub-Ressourcenanbieters](#endpoints) verwendet.

Folgende Vorgänge sind für Exportaufträge möglich:

* Erstellen eines Exportauftrags
* Abrufen des Status eines ausgeführten Auftrags
* Abbrechen eines ausgeführten Auftrags

> [AZURE.NOTE] Für jeden Hub kann zu jedem Zeitpunkt nur ein Auftrag ausgeführt werden.

Ausführliche Informationen zu den Import- und Export-APIs finden Sie unter [Azure IoT Hub – Ressourcenanbieter-APIs][lnk-resource-provider-apis].

#### Aufträge

Alle Exportaufträge verfügen über folgenden Eigenschaften:

| Eigenschaft | Optionen | Beschreibung |
| -------- | ------- | ----------- |
| jobId | vom System generiert, wird bei der Erstellung ignoriert | |
| creationTime | vom System generiert, wird bei der Erstellung ignoriert | |
| endOfProcessingTime | vom System generiert, wird bei der Erstellung ignoriert | |
| Typ | schreibgeschützt | **Export** |
| status | vom System generiert, wird bei der Erstellung ignoriert | **Zur Warteschlange hinzugefügt**, **Gestartet**, **Abgeschlossen**, **Fehler** |
| progress | vom System generiert, wird bei der Erstellung ignoriert | Ganzzahliger Wert, der den Prozentsatz der Fertigstellung angibt. |
| outputBlobContainerURI | erforderlich für alle Aufträge | Blob-SAS-URI (Shared Access Signature) mit Schreibzugriff auf einen Blobcontainer (siehe [Erstellen und Verwenden einer SAS mit dem Blob-Dienst][lnk-createuse-sas]). Wird zur Ausgabe des Auftragsstatus und der Ergebnisse verwendet. |
| includeKeysInExport | optional | Bei Festlegung auf **true** werden Schlüssel in der Exportausgabe eingeschlossen. Andernfalls werden Schlüssel als **null** exportiert. Die Standardeinstellung ist **false**. |
| failureReason | vom System generiert, wird bei der Erstellung ignoriert | Wenn der Status **Fehler** lautet, handelt es sich um eine Zeichenfolge, die die Ursache angibt. |

#### Exportieren von Aufträgen

Exportaufträge akzeptieren einen Blob-SAS-URI als Parameter. Dadurch wird der Schreibzugriff auf einen Blobcontainer gewährt, in den der Auftrag die Ergebnisse ausgeben kann.

Der Auftrag schreibt die Ausgabeergebnisse in den angegebenen Blobcontainer in eine Datei namens **job\_{job\_id}\_devices.txt**. Diese Datei enthält die Geräteidentitäten, die gemäß Festlegung in den [Geräteidentitätseigenschaften](#deviceproperties) als JSON serialisiert wurden. Die Sicherheitsdaten werden auf **null** gesetzt, wenn **includeKeysInExport** auf **false** festgelegt ist.

**Beispiel**:

```
{"deviceId":"devA","auth":{"symKey":{"primaryKey":"123"}},"status":"enabled"}
{"deviceId":"devB","auth":{"symKey":{"primaryKey":"234"}},"status":"enabled"}
{"deviceId":"devC","auth":{"symKey":{"primaryKey":"345"}},"status":"enabled"}
{"deviceId":"devD","auth":{"symKey":{"primaryKey":"456"}},"status":"enabled"}
```

## Sicherheit <a id="security"></a>

In diesem Abschnitt werden die Optionen zum Schützen von Azure IoT Hub beschrieben.

### Zugriffssteuerung <a id="accesscontrol"></a>

IoT Hub verwendet den folgenden Satz an *Berechtigungen*, um Zugriff auf den Endpunkt für jeden IoT Hub zu gewähren. Berechtigungen beschränkten den Zugriff auf einen IoT Hub basierend auf der Funktionalität.

* **RegistryRead**. Diese Berechtigung gewährt Lesezugriff auf die Geräteidentitätsregistrierung. Weitere Informationen finden Sie unter [Geräteidentitätsregistrierung](#device-identity-registry).
* **RegistryReadWrite**. Diese Berechtigung gewährt Lese- und Schreibzugriff auf die Geräteidentitätsregistrierung. Weitere Informationen finden Sie unter [Geräteidentitätsregistrierung](#device-identity-registry).
* **ServiceConnect**. Diese Berechtigung gewährt Zugriff auf die clouddienstseitigen Endpunkte für Kommunikation und Überwachung. Beispielsweise wird Back-End-Clouddiensten die Berechtigung erteilt, D2C-Nachrichten zu empfangen, C2D-Nachrichten zu senden und die zugehörigen Übermittlungsbestätigungen zu empfangen.
* **DeviceConnect**. Diese Berechtigung gewährt Zugriff auf die geräteseitigen Endpunkte für die Kommunikation. Beispielsweise wird die Berechtigung zum Senden von D2C-Nachrichten und das Empfangen von C2D-Nachrichten erteilt. Diese Berechtigung wird von Geräten verwendet.

Sie können Berechtigungen auf folgende Weise festlegen:

* **Gemeinsam genutzte Zugriffsrichtlinien auf Hubebene**. Gemeinsam genutzte Zugriffsrichtlinien können eine beliebige Kombination der im vorigen Abschnitt aufgeführten Berechtigungen gewähren. Sie können Richtlinien im [Azure-Portal][lnk-management-portal] oder programmgesteuert mithilfe von [Azure IoT Hub-Ressourcenanbieter-APIs][lnk-resource-provider-apis] definieren. Ein neu erstellter IoT Hub verfügt über die folgenden Standardrichtlinien:

    - *iothubowner*: Richtlinie mit sämtlichen Berechtigungen
    - *service*: Richtlinie mit **ServiceConnect**-Berechtigung
    - *device*: Richtlinie mit **DeviceConnect**-Berechtigung
    - *registryRead*: Richtlinie mit **RegistryRead**-Berechtigung
    - *registryReadWrite*: Richtlinie mit den Berechtigungen **RegistryRead** und **RegistryWrite**

* **Sicherheitsanmeldeinformationen auf Gerätebasis**. Jeder IoT Hub enthält eine [Geräteidentitätsregistrierung](#device-identity-registry). Sie können für jedes Gerät in dieser Registrierung Sicherheitsanmeldeinformationen konfigurieren, die **DeviceConnect**-Berechtigungen erteilen, deren Gültigkeitsbereich auf die entsprechenden Geräteendpunkte festgelegt ist.

**Beispiel**. In einer typischen IoT-Lösung gilt Folgendes:
- Die Komponente zur Geräteverwaltung verwendet die *registryReadWrite*-Richtlinie.
- Die Ereignisprozessorkomponente nutzt die *service*-Richtlinie.
- Die Laufzeit-Geschäftslogikkomponente für Geräte verwendet die *service*-Richtlinie.
- Einzelne Geräte stellen unter Verwendung von Anmeldeinformationen eine Verbindung her, die in der IoT Hub-Identitätsregistrierung gespeichert sind.

Einen Leitfaden zu IoT Hub-Sicherheitsthemen finden Sie im Abschnitt zur Sicherheit unter [Entwerfen der Lösung][lnk-guidance-security].

### Authentifizierung

Azure IoT Hub gewährt Zugriff auf Endpunkte, indem ein Token zur Verifizierung mit gemeinsam genutzten Zugriffsrichtlinien und Sicherheitsanmeldeinformationen für die Geräteidentität verglichen wird.

Sicherheitsanmeldeinformationen, beispielsweise symmetrische Schlüssel, werden niemals über eine physische Verbindung gesendet.

> [AZURE.NOTE] Der Azure IoT Hub-Ressourcenanbieter wird über Ihr Azure-Abonnement geschützt, ebenso wie alle Anbieter im [Azure-Ressourcen-Manager][lnk-azure-resource-manager].

#### Format des Sicherheitstokens <a id="tokenformat"></a>

Das Sicherheitstoken weist das folgende Format auf:

	SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Es werden die folgenden Werte erwartet:

| Wert | Beschreibung |
| ----- | ----------- |
| {signature} | Eine HMAC-SHA256-Signaturzeichenfolge in folgendem Format: `{URL-encoded-resourceURI} + "\n" + expiry` |
| {resourceURI} | Das URI-Präfix (nach Segment) der Endpunkte, auf die mit diesem Token zugegriffen werden kann. Beispiel: `/events` |
| {expiry} | UTF8-Zeichenfolge, dargestellt als die Anzahl von Sekunden seit dem 1. Januar 1970 um 00:00:00 UTC. |
| {URL-encoded-resourceURI} | URL-codierter Ressourcen-URI (Kleinschreibung) |
| {policyName} | Der Name der gemeinsam genutzten Zugriffsrichtlinie, auf die dieses Token verweist. Nicht vorhanden, wenn Token auf Anmeldeinformationen der Geräteregistrierung verweisen. |

**Hinweis zum Präfix**: Das URI-Präfix wird nach Segment, nicht nach Zeichen berechnet. Beispielsweise ist `/a/b` ein Präfix für `/a/b/c`, aber nicht für `/a/bc`.

#### Protokolldetails

Jedes unterstützte Protokoll, z. B. AMQP und HTTP, transportiert Token auf unterschiedliche Weise.

HTTP implementiert Authentifizierung, indem ein gültiges Token in den **Authorization**-Anforderungsheader eingeschlossen wird. Das Token kann auch über den Abfrageparameter **Authorization** transportiert werden.

Bei Verwendung von [AMQP][lnk-amqp] unterstützt IoT Hub [SASL PLAIN][lnk-sasl-plain] und die auf [AMQP-Ansprüchen basierte Sicherheit][lnk-cbs].

Im Falle der auf AMQP-Ansprüchen basierten Sicherheit gibt der Standard vor, wie die oben aufgeführten Token übertragen werden.

Für SASL PLAIN kann der **Benutzername** Folgendes sein:

* `{policyName}&commat;sas.root.{iothubName}` im Fall von Token auf Hubebene.
* `{deviceId}` im Fall von Token für Geräte.

In beiden Fällen enthält das Kennwortfeld ein Token gemäß Beschreibung im Abschnitt [Tokenformat](#tokenformat).

> [AZURE.NOTE] Die [Azure IoT Hub-SDKs][lnk-apis-sdks] generieren automatisch Token, wenn eine Verbindung mit dem Dienst hergestellt wird. In einigen Fällen unterstützen die SDKs nicht alle Protokolle oder Authentifizierungsmethoden.

#### SASL PLAIN und CBS im Vergleich

Bei Verwendung von SASL PLAIN kann ein Client, der eine Verbindung mit einem IoT Hub herstellt, ein einziges Token für jede TCP-Verbindung verwenden. Wenn das Token abläuft, wird die TCP-Verbindung mit dem Dienst getrennt, und es wird ein Versuch zur erneuten Verbindungsherstellung ausgelöst. Dieses Verhalten ist für eine Komponente im Anwendungs-Back-End unproblematisch, kann für eine geräteseitige Anwendung jedoch sehr schädlich sein. Dies hat folgende Gründe:

*  Gateways stellen eine Verbindung üblicherweise für eine Vielzahl von Geräten her. Bei Verwendung von SASL PLAIN muss für jedes Gerät, das eine Verbindung mit einem IoT Hub herstellen möchte, eine eigene TCP-Verbindung erstellt werden. Dies erhöht den Verbrauch von Rechen- und Netzwerkressourcen erheblich und führt zu einer höheren Latenz für jede Geräteverbindung.
* Der erhöhte Ressourcenverbrauch bei der erneuten Verbindungsherstellung nach jedem Tokenablauf wirkt sich negativ auf Geräte mit Ressourceneinschränkungen aus.

### Gültigkeitsbereich für Anmeldeinformationen auf Hubebene

Sie können den Bereich für Sicherheitsrichtlinien auf Hubebene festlegen, indem Sie Token mit eingeschränktem Ressourcen-URI erstellen. Beispielsweise lautet der Endpunkt zum Senden von D2C-Nachrichten von einem Gerät **/devices/{deviceId}/events**. Sie können eine gemeinsam genutzte Sicherheitsrichtlinie auf Hubebene mit **DeviceConnect**-Berechtigungen auch dazu verwenden, um ein Token zu signieren, dessen resourceURI-Element **/devices/{deviceId}** lautet. So wird ein Token erstellt, das nur zum Senden von Gerätenachrichten im Namen von Gerät **deviceId** verwendet werden kann.

Dieser Mechanismus ist mit einer [Event Hubs-Herausgeberrichtlinie][lnk-event-hubs-publisher-policy] vergleichbar und ermöglicht die Implementierung von benutzerdefinierten Authentifizierungsmethoden. Siehe hierzu den Abschnitt zur Sicherheit unter [Entwerfen der Lösung][lnk-guidance-security].

## Nachrichten

IoT Hub stellt Messaging-Grundtypen für die Kommunikation zwischen folgenden Komponenten bereit:
- [C2D](#c2d): von einem Anwendungs-Back-End (*Dienst* oder *Cloud*).
- [D2C](#d2c): von einem Gerät zu einem Anwendung-Back-End.

Die wichtigsten Eigenschaften beim IoT Hub-Messaging sind eine zuverlässige und stabile Übermittlung von Nachrichten. Dies bietet Ausfallsicherheit bei zeitweiligen Verbindungsproblemen auf Geräteseite und Lastspitzen bei der Ereignisverarbeitung auf Cloudseite. IoT Hub implementiert *mindestens einmal* Übermittlungsgarantien für D2C- und C2D-Messaging.

IoT Hub unterstützt mehrere geräteseitige Protokolle (z. B. AMQP und HTTP/1). Um eine nahtlose Interoperabilität zwischen Protokollen zu gewährleisten, definiert IoT Hub ein gemeinsames Nachrichtenformat, das von allen geräteseitigen Protokollen unterstützt wird.

### Nachrichtenformat <a id="messageformat"></a>

IoT Hub-Nachrichten umfassen:

* Einen Satz an *Systemeigenschaften*. Diese Eigenschaften werden von IoT Hub interpretiert oder festgelegt. Dieser Satz ist vordefiniert.
* Einen Satz an *Anwendungseigenschaften*. Dies ist ein Wörterbuch mit Zeichenfolgeneigenschaften, die die Anwendung definieren und auf die die Anwendung zugreifen kann, ohne den Nachrichtentext deserialisieren zu müssen. IoT Hub ändert diese Eigenschaften nie.
* Ein nicht lesbarer binärer Textkörper.

Weitere Informationen dazu, wie die Nachricht in verschiedenen Protokollen codiert wird, finden Sie unter [IoT Hub-APIs und -SDKs][lnk-apis-sdks].

Die folgende Tabelle zeigt den Satz an Systemeigenschaften in IoT Hub-Nachrichten.

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| MessageId | Eine vom Benutzer festgelegte Kennung für die Nachricht, wird üblicherweise für Anforderung-Antwort-Muster verwendet. Format: Eine Zeichenfolge mit Berücksichtigung von Klein-/Großschreibung (bis zu 128 Zeichen lang), die aus alphanumerischen ASCII-Zeichen (7 Bit) + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` besteht. |
| Sequenznummer | Eine Nummer (für jede Gerätewarteschlange eindeutig), die jeder C2D-Nachricht von IoT Hub zugewiesen wird |
| To | Gibt in [C2D](#c2d)-Nachrichten das Feld für das Ziel an.|
| ExpiryTimeUtc | Datum und Uhrzeit des Nachrichtenablaufs. |
| EnqueuedTime | Zeitpunkt des Empfangs der Nachricht durch IoT Hub. |
| CorrelationId | Zeichenfolgeneigenschaft, die üblicherweise die Nachrichten-ID der Anforderung bei Anforderung-Antwort-Mustern enthält. |
| UserId | Dient zum Festlegen des Ursprungs von Nachrichten. Wenn IoT Hub Nachrichten generiert, wird diese Eigenschaft auf `{iot hub name}` festgelegt. |
| Ack | Wird in C2D-Nachrichten verwendet, um IoT Hub anzuweisen, als Ergebnis der Nachrichtenverarbeitung durch das Gerät Feedback zu generieren. Mögliche Werte: **none** (Standardeinstellung): es wird keine Feedbacknachricht generiert, **positive**: Feedbacknachricht empfangen, wenn die Nachricht abgeschlossen wurde, **negative**: Feedbacknachricht empfangen, wenn die Nachricht ohne vollständige Verarbeitung durch das Gerät abgelaufen ist (oder die maximale Anzahl von Zustellversuchen erreicht wurde), **full**: Feedback wird sowohl bei erfolgreicher als auch nicht erfolgreicher Nachrichtenverarbeitung generiert. Weitere Informationen finden Sie unter [Nachrichtenfeedback](#feedback). |
| ConnectionDeviceId | Wird von IoT Hub für D2C-Nachrichten festgelegt. Diese Eigenschaft enthält die **deviceId** des Geräts, das die Nachricht sendet. |
| ConnectionDeviceGenerationId | Wird von IoT Hub für D2C-Nachrichten festgelegt. Diese Eigenschaft enthält die **generationId** (gemäß [Geräteidentitätseigenschaften](#deviceproperties)) des Geräts, das die Nachricht gesendet hat. |
| ConnectionAuthMethod | Wird von IoT Hub für D2C-Nachrichten festgelegt. Diese Eigenschaft enthält Informationen zur verwendeten Methode zum Authentifizieren des Geräts, das die Nachricht sendet. Weitere Informationen finden Sie unter [D2C-Anti-Spoofing](#antispoofing).|

### Auswählen des Kommunikationsprotokolls <a id="amqpvshttp"></a>

IoT Hub unterstützt für die geräteseitige Kommunikation sowohl [AMQP][lnk-amqp] als auch das HTTP/1-Protokoll. Bei der Verwendung dieser Protokolle müssen Sie Folgendes berücksichtigen:

* **C2D-Muster**. HTTP/1 verfügt über keine effiziente Methode zum Implementieren von Serverpushvorgängen. Daher fragen Geräte bei Verwendung von HTTP/1 IoT Hub für C2D-Nachrichten ab. Dies ist sowohl für das Gerät als auch für IoT Hub ineffizient. Die aktuellen Richtlinien bei Verwendung von HTTP/1 sehen vor, für jedes Gerät ein Abrufintervall von weniger als 1 pro 25 Minuten festzulegen. AMQP dagegen unterstützt Serverpushvorgänge beim Empfang von C2D-Nachrichten und ermöglicht es, Nachrichten von IoT Hub sofort per Push an das Gerät zu übertragen. Wenn die Übermittlungslatenz eine wichtige Rolle spielt, ist daher die Verwendung des AMQP-Protokolls die bevorzugte Option. Für Geräte, die nur selten verbunden werden, kann durchaus HTTP/1 verwendet werden.
* **Bereichsgateways**. Aufgrund der Einschränkungen von HTTP/1 in Bezug auf Serverpushvorgänge ist es für eine Verwendung in [Szenarien mit Bereichsgateway][lnk-azure-gateway-guidance] nicht geeignet.
* **Geräte mit eingeschränkten Ressourcen**. HTTP/1-Bibliotheken sind deutlich kleiner als AMQP-Bibliotheken. Wenn daher das Gerät über weniger Ressourcen verfügt (beispielsweise weniger als 1 MB RAM), dann steht möglicherweise nur HTTP/1 als Protokollimplementierung zur Verfügung.
* **Netzwerkausnahme**. AMQP lauscht standardmäßig auf Port 5672. Dies kann Probleme in Netzwerken verursachen, die für Nicht-HTTP-Protokolle geschlossen sind.
* **Größe der Nutzlast**. AMQP ist ein Binärprotokoll, das deutlich kompakter ist als HTTP/1.

Im Allgemeinen wird empfohlen, nach Möglichkeit AMQP zu verwenden und HTTP/1 nur dann zu einzusetzen, wenn Geräteressourcen oder Netzwerkkonfiguration eine Verwendung von AMQP nicht zulassen. Bei der Verwendung von HTTP/1 sollte die Abrufhäufigkeit pro Gerät auf weniger als 1 Abruf pro 25 Minuten festgelegt werden. Für die Entwicklung ist zweifelsohne eine höhere Abfragehäufigkeit von Vorteil.

Schließlich sollte noch der Einsatz eines [Azure IoT-Protokollgateways][lnk-azure-protocol-gateway] erwogen werden, das die Bereitstellung eines hochleistungsfähigen MQTT-Gateways mit einer direkten Schnittstelle zu IoT Hub emöglicht. Das MQTT-Protokoll unterstützt Serverpushvorgänge (und ermöglicht auf diese Weise die sofortige Übermittlung von C2D-Nachrichten an das Gerät) und steht für Geräte mit sehr wenigen Ressourcen zur Verfügung. Der wesentliche Nachteil bei diesem Ansatz ist die Anforderung, selbst ein Protokollgateway zu hosten und zu verwalten.

### Gerät an Cloud <a id="d2c"></a>

Wie im Abschnitt [Endpunkte](#endpoints) erläutert, werden D2C-Nachrichten über einen geräteseitigen Endpunkt (**/devices/{deviceId}/messages/events**) gesendet und über einen dienstseitigen Endpunkt (**/messages/events**) empfangen, der mit [Event Hubs][lnk-event-hubs] kompatibel ist. Auf diese Weise können Sie standardmäßige Event Hubs-Integration und SDKs zum Empfangen von D2C-Nachrichten verwenden.

IoT Hub implementiert das D2C-Messaging ähnlich wie [Event Hubs][lnk-event-hubs], wobei das D2C-Messaging von IoT Hub eher mit *Ereignissen* in Event Hubs als mit [Service Bus][lnk-servicebus]-*Nachrichten* vergleichbar ist.

Diese Implementierung hat folgende Auswirkungen:

* Ähnlich wie *Ereignisse* in Event Hubs sind D2C-Nachrichten dauerhaft und werden in einem IoT Hub bis zu 7 Tage aufbewahrt (siehe [Optionen für die D2C-Konfiguration](#d2cconfiguration)).
* D2C-Nachrichten werden in einem festen Satz an Partitionen partitioniert, der zum Zeitpunkt der Erstellung festgelegt wird (siehe [Optionen für die D2C-Konfiguration](#d2cconfiguration)).
* Analog zu Event Hubs müssen Clients, die D2C-Nachrichten lesen, Partitionen und Prüfpunkte verarbeiten können. Siehe [Event Hubs: Ereignisconsumer][lnk-event-hubs-consuming-events].
* Wie Event Hubs-Ereignisse können D2C-Nachrichten maximal 256 KB umfassen und in Batches gruppiert werden, um Sendevorgänge zu optimieren. Batches können maximal 256 KB groß sein und maximal 500 Nachrichten enthalten.

Es gibt jedoch einige wichtige Unterschiede zwischen dem D2C-Messaging von IoT Hub und Event Hubs:

* Wie im Abschnitt [Sicherheit](#security) erläutert, ermöglicht IoT Hub eine Authentifizierung und Zugriffsteuerung auf Gerätebasis.
* IoT Hub unterstützt Millionen von gleichzeitig verbundenen Geräten (siehe [Kontingente und Drosselung](#throttling)), während Event Hubs auf 5.000 AMQP-Verbindungen pro Namespace beschränkt ist.
* IoT Hub ermöglicht keine zufällige Partitionierung mit **PartitionKey**. D2C-Nachrichten werden basierend auf ihrer ursprünglichen **Geräte-ID** partitioniert.
* Die Skalierung funktioniert in IoT Hub etwas anders als in Event Hubs. Weitere Informationen finden Sie unter [Skalieren von IoT Hub][lnk-guidance-scale].

Hinweis: Dies bedeutet nicht, dass Sie in allen Szenarien IoT Hub durch Event Hubs ersetzen können. In einigen Szenarien mit Ereignisverarbeitung kann es beispielsweise erforderlich sein, Ereignisse in Bezug auf eine andere Eigenschaft oder ein anderes Feld neu zu partitionieren, bevor die Datenströme analysiert werden können. In diesem Szenario können Sie mit einem Event Hub zwei Bestandteile der Pipeline zur Datenstromverarbeitung entkoppeln.

Ausführliche Informationen zum Verwenden des D2C-Messaging finden Sie unter [IoT Hub-APIs und -SDKs][lnk-apis-sdks].

#### Datenverkehr ohne Telemetrie

In vielen Fällen senden Geräte nicht nur Telemetriedatenpunkte, sondern auch *interaktive* Nachrichten und Anforderungen, die eine Ausführung und Verarbeitung durch die Anwendungsebene mit der Geschäftslogik erfordern. Beispiele: Kritische Warnungen, die eine bestimmte Aktion im Back-End auslösen müssen, oder Geräteantworten auf vom Back-End gesendete Befehle.

Weitere Informationen zur besten Verarbeitungsmethode für diese Art von Nachrichten finden Sie im Abschnitt zur [D2C-Verarbeitung][lnk-guidance-d2c-processing].

#### Optionen für die D2C-Konfiguration <a id="d2cconfiguration"></a>

Ein IoT Hub macht die folgenden Eigenschaften zum Steuern des D2C-Messaging verfügbar:

* **Anzahl von Partitionen**. Legen Sie diese Eigenschaft bei der Erstellung fest, um die Anzahl von Partitionen für die D2C-Ereigniserfassung zu definieren.
* **Aufbewahrungsdauer**. Diese Eigenschaft legt die Aufbewahrungszeit für D2C-Nachrichten fest. Als Standardwert ist ein Tag festgelegt, dieser Wert kann jedoch auf sieben Tage erhöht werden.

Analog zu Event Hubs ermöglicht IoT Hub die Verwaltung von Consumergruppen am empfangenden D2C-Endpunkt.

Sie können all diese Eigenschaften sowohl über das [Azure-Portal][lnk-management-portal] als auch programmgesteuert über [Azure IoT Hub-Ressourcenanbieter-APIs][lnk-resource-provider-apis] ändern.

#### Eigenschaften zum Schutz vor Spoofing <a id="antispoofing"></a>

Um ein Gerätespoofing beim D2C-Messaging zu verhindern, versieht IoT Hub alle Nachrichten mit den folgenden Eigenschaften:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Die ersten beiden Eigenschaften enthalten die Werte für **deviceId** und **generationId** des ursprünglichen Geräts, wie beschrieben unter [Geräteidentitätseigenschaften](#deviceproperties).

Die Eigenschaft **ConnectionAuthMethod** enthält ein serialisiertes JSON-Objekt mit den folgenden Eigenschaften:

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

### C2D-Nachrichten <a id="c2d"></a>

Wie im Abschnitt [Endpunkte](#endpoints) beschrieben, können Sie C2D-Nachrichten über einen dienstseitigen Endpunkt (**/messages/devicebound**) senden, und ein Gerät kann diese Nachrichten über einen geräteseitigen Endpunkt (**/devices/{deviceId}/messages/devicebound**) empfangen.

Jede C2D-Nachricht ist für ein einziges Gerät bestimmt, indem die **to**-Eigenschaft auf **/devices/{deviceId}/messages/devicebound** festgelegt wird.

**Wichtig**: Jede Gerätewarteschlange kann maximal 50 C2D-Nachrichten enthalten. Der Versuch, eine größere Anzahl von Nachrichten an dasselbe Gerät zu senden, führt zu einem Fehler.

#### Nachrichtenlebenszyklus <a id="message lifecycle"></a>

Um *mindestens eine* Übermittlungsgarantie zu implementieren, werden C2D-Nachrichten dauerhaft in Gerätewarteschlangen gespeichert, und Geräte müssen den *Abschluss* der Nachricht explizit bestätigen, damit IoT Hub eine Nachricht aus der Warteschlange entfernen kann. Auf diese Weise wird Ausfallsicherheit bei Verbindungs- und Gerätefehlern gewährleistet.

Die folgende Abbildung zeigt den Lebenszyklus einer C2D-Nachricht und ihren jeweiligen Status.

![Lebenszyklus von C2D-Nachrichten][img-lifecycle]

Wenn der Dienst eine Nachricht sendet, wird diese als *Zur Warteschlange hinzugefügt* betrachtet. Wenn ein Gerät eine Nachricht *empfangen* möchte, *sperrt* IoT Hub die Nachricht (Status wird auf **Nicht sichtbar** gesetzt), um anderen Threads auf demselben Gerät das Empfangen anderer Nachrichten zu ermöglichen. Wenn ein Gerätethread die Verarbeitung für ein Gerät abschließt, wird IoT Hub hierüber durch den *Abschluss* der Nachricht benachrichtigt.

Darüber hinaus bestehen folgende Möglichkeiten
- Ein Gerät kann die Nachricht *ablehnen*, woraufhin IoT Hub sie in den Status **Unzustellbar** versetzt.
- Das Gerät kann den Vorgang auch *abbrechen*, woraufhin IoT die Nachricht wieder in die Warteschlange einreiht (Status **Zur Warteschlange hinzugefügt**).

Bei der Nachrichtenverarbeitung durch den Thread könnte ein Fehler auftreten, ohne dass IoT Hub hierüber benachrichtigt wird. In diesem Fall werden Nachrichten automatisch vom Status **Nicht sichtbar** zurück in den Status **Zur Warteschlange hinzugefügt** versetzt, wenn ein *Timeout für die Sichtbarkeit (oder Sperrung)* abgelaufen ist (Standardeinstellung: 1 Minute). Eine Nachricht kann zwischen den Statuswerten **Zur Warteschlange hinzugefügt** und **Nicht sichtbar** maximal so oft wechseln, wie in der Eigenschaft *Anzahl maximaler Zustellungen* in IoT Hub festgelegt wurde. Nachdem diese Anzahl überschritten wurde, legt IoT Hub als Status der Nachricht **Unzustellbar** fest. Ebenso kennzeichnet IoT Hub eine Nachricht als **unzustellbar**, wenn ihre Gültigkeitsdauer abgelaufen ist (siehe [Gültigkeitsdauer](#ttl)).

Ein Tutorial zu C2D-Nachrichten finden Sie unter [Erste Schritte mit Azure IoT Hub-Nachrichten zwischen Cloud und Gerät][lnk-getstarted-c2d-tutorial]. Informationen dazu, wie verschiedene APIs und SDKs die C2D-Funktionalität verfügbar machen, finden Sie unter [IoT Hub-APIs und -SDKs][lnk-apis-sdks].

> [AZURE.NOTE] Typischerweise werden C2D-Nachrichten immer dann abgeschlossen, wenn der Verlust der Nachricht keine Auswirkung auf die Anwendungslogik hat. Dies kann in vielen verschiedenen Szenarien zutreffen. Es trifft beispielsweise dann zu, wenn der Nachrichteninhalt erfolgreich im lokalen Speicher abgelegt oder ein Vorgang erfolgreich ausgeführt wurde oder wenn die Nachricht kurzlebige Informationen enthält, deren Verlust sich nicht auf die Funktionalität der Anwendung auswirkt. Bei Tasks mit langer Ausführungsdauer können Sie die C2D-Nachricht abschließen, nachdem die Taskbeschreibung in den lokalen Speicher geschrieben wurde. Anschließend wird das Anwendungs-Back-End mit einer oder mehreren D2C-Nachrichten in verschiedenen Phasen über den Taskfortschritt benachrichtigt.

#### Gültigkeitsdauer <a id="ttl"></a>

Jede C2D-Nachricht verfügt über eine Gültigkeitsdauer. Diese kann (in der Eigenschaft **ExpiryTimeUtc**) explizit durch den Dienst oder durch IoT Hub mithilfe der standardmäßigen als IoT Hub-Eigenschaft gesetzten *Gültigkeitsdauer* festgelegt werden. Siehe [Optionen für die C2D-Konfiguration](#c2dconfiguration).

#### Nachrichtenfeedback <a id="feedback"></a>

Beim Senden einer C2D-Nachricht kann der Dienst das Übermitteln von Feedback auf Nachrichtenbasis anfordern, um über den finalen Status dieser Nachricht informiert zu werden.

- Wenn die **Ack**-Eigenschaft auf **positive** festgelegt wird, generiert IoT Hub nur dann eine Feedbacknachricht, wenn die C2D-Nachricht den Status **Abgeschlossen** erreicht hat.
- Wenn Sie die **Ack**-Eigenschaft auf **negative** festlegen, generiert IoT Hub nur dann eine Feedbacknachricht, wenn die C2D-Nachricht den Status **Unzustellbar** erreicht.
- Bei Festlegung der **Ack**-Eigenschaft auf **full** generiert IoT Hub in beiden Fällen eine Feedbacknachricht.

Wie im Abschnitt [Endpunkte](#endpoints) erläutert, übermittelt IoT Hub Feedback über einen dienstseitigen Endpunkt (**/messages/servicebound/feedback**) in Form von Nachrichten. Die Semantik für den Empfang von Feedback stimmt mit der für C2D-Nachrichten überein, die den gleichen Nachrichtenlebenszyklus aufweisen. Nachrichtenfeedback wird nach Möglichkeit in einer einzigen Nachricht zusammengefasst, die das folgende Format aufweist.

Jede vom Feedbackendpunkt empfangene Nachricht umfasst die folgenden Eigenschaften:

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| EnqueuedTime | Zeitstempel, der die Erstellung des Batches angibt. |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

Der Nachrichtenkörper ist ein serialisiertes JSON-Array aus Datensätzen, von denen jeder die folgenden Eigenschaften aufweist:

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| EnqueuedTimeUtc | Zeitstempel, der den Zeitpunkt des Nachrichtenergebnisses angibt. Diese Eigenschaft kann beispielsweise angeben, wann das Gerät abgeschlossen wurde oder die Nachricht abgelaufen ist. |
| OriginalMessageId | **MessageId** der C2D-Nachricht, auf die sich das Feedback bezieht. |
| Beschreibung | Zeichenfolgenwerte für die zuvor genannten Ergebnisse. |
| DeviceId | **DeviceId** des Zielgeräts für die C2D-Nachricht, auf die sich das Feedback bezieht. |
| DeviceGenerationId | **DeviceGenerationId** des Zielgeräts für die C2D-Nachricht, auf die sich das Feedback bezieht. |

**Wichtig**: Der Dienst muss eine **MessageId** für die C2D-Nachricht angeben, damit das Feedback der ursprünglichen Nachricht zugeordnet werden kann.

**Beispiel**. Nachfolgend wird ein Beispiel für eine Feedbacknachricht gezeigt.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

#### Optionen für die C2D-Konfiguration <a id="c2dconfiguration"></a>

Jeder IoT Hub legt die folgenden Konfigurationsoptionen für das C2D-Messaging offen:

| Eigenschaft | Beschreibung | Bereich und Standardwert |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | Standardmäßige Gültigkeitsdauer für C2D-Nachrichten. | ISO\_8601-Intervall bis 2D (mindestens 1 Minute). Standardwert: 1 Stunde. |
| maxDeliveryCount | Maximale Zustellungsanzahl für C2D-Gerätewarteschlangen pro Gerät. | 1 bis 100. Standardwert: 10 |
| feedback.ttlAsIso8601 | Aufbewahrungsdauer für dienstgebundene Feedbacknachrichten. | ISO\_8601-Intervall bis 2D (mindestens 1 Minute). Standardwert: 1 Stunde. |
| feedback.maxDeliveryCount | Maximale Zustellungsanzahl für Feedbackwarteschlangen. | 1 bis 100. Standardwert: 100. |

## Kontingente und Drosselung <a id="throttling"></a>

Jedes Azure-Abonnement kann maximal 10 IoT Hubs enthalten.

Jeder IoT Hub wird mit einer bestimmten Anzahl von Einheiten in einer spezifischen SKU bereitgestellt (weitere Informationen finden Sie unter [Azure IoT Hub – Preise][lnk-pricing]). SKU und Anzahl von Einheiten legen das maximale Kontingent an Nachrichten fest, die pro Tag gesendet werden können, sowie die maximale Anzahl von Geräteidentitäten in der Identitätsregistrierung. Die Anzahl von Identitäten in der Registrierung beschränkt die Anzahl von gleichzeitig verbundenen Geräten.

Die SKU legt auch die Drosselungslimits fest, die IoT Hub für Vorgänge erzwingt.

### Kontingent für die Geräteidentitätsregistrierung

IoT Hub lässt pro Einheit und Tag (unabhängig von der SKU) maximal 1100 Geräteaktualisierungen (Erstellen, Aktualisieren, Löschen) zu.

### Vorgangsdrosselung

Bei der Vorgangsdrosselung wird die Datenübertragungsrate pro Minute begrenzt, um einen Missbrauch zu verhindern. IoT Hub versucht, möglichst keine Fehler zurückzugeben, es werden jedoch Ausnahmen ausgelöst, wenn die Drosselungsgrenze zu lange überschritten wird.

Die nachfolgende Liste zeigt alle erzwungenen Werte für die Drosselung. Die Werte beziehen sich auf einen einzelnen Hub.

| Drosselung | Wert pro Hub |
| -------- | ------------- |
| Vorgänge in der Identitätsregistrierung (Erstellen, Abrufen, Aktualisieren, Löschen), einzelne Import-/Exportvorgänge oder Massenimport/-export | 100/Minute/Einheit, bis zu 5.000/Minute |
| Geräteverbindungen | 100/Sekunde/Einheit |
| Senden von Nachrichten von Geräten an die Cloud | 120/Sekunden/Einheit (für S2), 12/Sekunden/Einheit (für S1); mindestens 100/Sekunde |
| C2D-Vorgänge (Senden, Empfangen, Feedback) | 100/Minute/Einheit |

**Hinweis**: Die Kontingente oder Drosselungsgrenzwerte können jederzeit angehoben werden, indem die Anzahl von bereitgestellten Einheiten in einem IoT Hub erhöht wird.

## Nächste Schritte

Nachdem Sie in diesem Dokument einen Überblick über die Entwicklung für IoT Hub erhalten haben, können Sie die folgenden Links nutzen, um mehr über diese Themen zu erfahren:

- [Erste Schritte mit IoT Hubs (Tutorial)][lnk-get-started]
- [Betriebssystemplattformen und Hardwarekompatibilität][lnk-compatibility]
- [Azure IoT Developer Center][lnk-iotdev]
- [Entwerfen der Lösung][lnk-guidance]

[Event Hubs-Ereignisprozessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[Azure-Portal]: https://portal.azure.com

[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-compatibility]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx

[lnk-azure-gateway-guidance]: iot-hub-guidance.md#field-gateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-createuse-sas]: ../storage-dotnet-shared-access-signature-part-2/
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_0107_2016-->

