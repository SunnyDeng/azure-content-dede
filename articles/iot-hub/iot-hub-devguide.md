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
 ms.date="02/03/2016"
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
* **Geräteendpunkte**: Für jedes Gerät, das in der Geräte-Identitätsregistrierung bereitgestellt wird, stellt IoT Hub eine Reihe von Endpunkten zur Verfügung, die von einem Gerät zum Senden und Empfangen von Nachrichten verwendet wird:
    - *Senden von D2C-Nachrichten*. Verwenden Sie diesen Endpunkt, um D2C-Nachrichten zu senden. Weitere Informationen finden Sie unter [Messaging zwischen Gerät und Cloud](#d2c).
    - *Empfangen von C2D-Nachrichten*. Ein Gerät verwendet diesen Endpunkt, um gezielte C2D-Nachrichten zu empfangen. Weitere Informationen finden Sie unter [Messaging zwischen Cloud und Gerät](#c2d).

    Diese Endpunkte werden über die Protokolle HTTP, [MQTT][lnk-mqtt] und [AMQP][lnk-amqp] verfügbar gemacht. Beachten Sie, dass AMQP auch über [WebSockets][lnk-websockets] an Port 443 verfügbar ist.
* **Dienstendpunkte**: Jeder IoT Hub legt eine Reihe von Endpunkten offen, die von Ihrem Anwendungs-Back-End zur Kommunikation mit Ihren Geräten verwendet werden können. Diese Endpunkte werden aktuell nur bei Verwendung des [AMQP][lnk-amqp]-Protokolls verfügbar gemacht.
    - *Empfangen von D2C-Nachrichten*. Dieser Endpunkt ist mit [Azure Event Hubs][lnk-event-hubs] kompatibel und kann von einem Back-End-Dienst dazu verwendet werden, alle D2C-Nachrichten zu lesen, die von Ihren Geräten gesendet werden. Weitere Informationen finden Sie unter [Messaging zwischen Gerät und Cloud](#d2c).
    - *Senden von C2D-Nachrichten und Empfangen von Übermittlungsbestätigungen*. Diese Endpunkte ermöglichen Ihrem Anwendungs-Back-End das Senden von zuverlässigen C2D-Nachrichten sowie das Empfangen zugehöriger Übermittlungs- oder Ablaufbestätigungen. Weitere Informationen finden Sie unter [Messaging zwischen Cloud und Gerät](#c2d).

Im Artikel [Microsoft Azure IoT SDKs][lnk-apis-sdks] werden die verschiedenen Möglichkeiten zum Zugriff auf diese Endpunkte beschrieben.

Schließlich ist es wichtig zu wissen, dass alle IoT Hub-Endpunkte das [TLS][lnk-tls]-Protokoll nutzen und Endpunkte niemals auf unverschlüsselten/unsicheren Kanälen verfügbar gemacht werden.

### Lesen von Daten aus Event Hubs-kompatiblen Endpunkten <a id="eventhubcompatible"></a>

Wenn Sie das [Azure Service Bus SDK für .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) oder den [Event Hubs-Ereignisprozessorhost][] verwenden, können Sie eine beliebige IoT Hub-Verbindungszeichenfolge mit den richtigen Berechtigungen verwenden und anschließend **messages/events** als Event Hub-Namen nutzen.

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

Jeder IoT Hub verfügt über eine Geräte-Identitätsregistrierung, die Sie zum Erstellen gerätebasierter Ressourcen im Dienst verwenden können. Hierzu zählt beispielsweise eine Warteschlange mit gesendeten C2D-Nachrichten. Außerdem wird der Zugriff auf geräteseitige Endpunkte ermöglicht, wie im Abschnitt [Zugriffssteuerung](#accesscontrol) beschrieben.

Auf höherer Ebene ist die Geräteidentitätsregistrierung eine RESTful-fähige Sammlung von Geräteidentitätsressourcen. In den folgenden Abschnitten werden die Eigenschaften von Geräteidentitätsressourcen sowie die Vorgänge beschrieben, die über die Registrierung für Identitäten ausgeführt werden können.

> [AZURE.NOTE] Nähere Informationen zum HTTP-Protokoll und den SDKs, die Sie für die Interaktion mit der Geräte-Identitätsregistrierung verwenden können, finden Sie unter [Microsoft Azure IoT SDKs][lnk-apis-sdks].

### Geräteidentitätseigenschaften <a id="deviceproperties"></a>

Geräteidentitäten werden als JSON-Dokumente mit den folgenden Eigenschaften dargestellt.

| Eigenschaft | Optionen | Beschreibung |
| -------- | ------- | ----------- |
| deviceId | erforderlich, bei Aktualisierungen schreibgeschützt | Eine Zeichenfolge mit Berücksichtigung von Klein-/Großschreibung (bis zu 128 Zeichen lang), die aus alphanumerischen ASCII-Zeichen (7 Bit) + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` besteht. |
| generationId | erforderlich, schreibgeschützt | Eine vom Hub generierte Zeichenfolge mit Berücksichtigung von Klein-/Großschreibung, die bis zu 128 Zeichen lang ist. Diese Zeichenfolge dient zur Unterscheidung von Geräten mit derselben **deviceId**, wenn diese gelöscht und neu erstellt wurden. |
| etag | erforderlich, schreibgeschützt | Eine Zeichenfolge, die ein schwaches Etag für die Geräteidentität gemäß [RFC7232][lnk-rfc7232] darstellt.|
| auth | optional | Ein zusammengesetztes Objekt, das Authentifizierungsinformationen und Sicherheitsdaten enthält. |
| auth.symkey | optional | Ein zusammengesetztes Objekt, das einen primären und einen sekundären Schlüssel enthält, die im Base64-Format gespeichert sind. |
| status | erforderlich | Kann **Aktiviert** oder **Deaktiviert** lauten. Sofern der Status **Aktiviert** lautet, kann das Gerät eine Verbindung herstellen. Lautet die Einstellung **Deaktiviert**, kann dieses Gerät auf keinen geräteseitigen Endpunkt zugreifen. |
| statusReason | optional | Eine 128 Zeichen lange Zeichenfolge, die die Ursache des Geräteidentitätsstatus speichert. Alle UTF-8-Zeichen sind zulässig. |
| statusUpdateTime | schreibgeschützt | Datum und Uhrzeit der letzten Statusaktualisierung. |
| connectionState | schreibgeschützt | **Verbunden** oder **Getrennt**; repräsentiert den Geräteverbindungsstatus aus IoT Hub-Sicht. **Wichtig**: Dieses Feld darf nur für Entwicklungs-/Debuggingzwecke verwendet werden. Der Verbindungszustand wird nur für Geräte aktualisiert, die AMQP oder MQTT verwenden. Er basiert außerdem auf Pings auf Protokollebene (MQTT- oder AMQP-Pings) und kann eine Verzögerung von maximal 5 Minuten haben. Aus diesen Gründen sind falsch positive Rückmeldungen möglich, z. B. als verbunden gemeldete Geräte, die tatsächlich aber getrennt sind. |
| connectionStateUpdatedTime | schreibgeschützt | Datum und Uhrzeit der letzten Aktualisierung des Verbindungsstatus. |
| lastActivityTime | schreibgeschützt | Datum und Uhrzeit der letzten Geräteverbindung oder der Zeitpunkt, zu dem das letzte Mal eine Nachricht empfangen oder gesendet wurde. |

> [AZURE.NOTE] Der Verbindungsstatus kann nur den Status der Verbindung aus Sicht von IoT Hub widerspiegeln. Aktualisierungen dieser Statusanzeige können sich je nach Netzwerkbedingungen und -konfigurationen verzögern.

### Geräteidentitätsvorgänge

Die IoT Hub-Geräteidentitätsregistrierung macht die folgenden Vorgänge verfügbar:

* Erstellen einer Geräteidentität
* Aktualisieren einer Geräteidentität
* Abrufen von Geräteidentitäten nach ID
* Löschen einer Geräteidentität
* Auflisten von bis zu 1.000 Identitäten
* Exportieren aller Identitäten in Blobspeicher
* Importieren von Identitäten aus Blobspeicher

Sämtliche der oben aufgeführten Vorgänge erlauben die Verwendung optimistischer Nebenläufigkeit gemäß [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT] Die einzige Möglichkeit zum Abrufen aller Identitäten in der Identitätsregistrierung eines Hubs besteht darin, die Funktion [Export](#importexport) zu verwenden.

Für die Identitätsregistrierung eines IoT Hub-Geräts gilt Folgendes:

- Enthält keinerlei Anwendungsmetadaten.
- Der Zugriff kann wie bei einem Wörterbuch über die **deviceId** als Schlüssel erfolgen.
- Ausdrucksbasierte Abfragen werden nicht unterstützt.

Eine IoT-Lösung verfügt in der Regel über einen lösungsspezifischen Speicher mit anwendungsspezifischen Metadaten. Der lösungsspezifische Speicher in einer Lösung für intelligente Gebäude würde beispielsweise den Raum enthalten, in dem ein Temperatursensor bereitgestellt wird.

> [AZURE.IMPORTANT] Sie dürfen die Geräte-Identitätsregistrierung nur für Geräteverwaltungs- und -bereitstellungsvorgänge nutzen. Vorgänge mit hohem Durchsatz zur Laufzeit dürfen nicht von der Ausführung von Vorgängen in der Geräte-Identitätsregistrierung abhängen. Das Überprüfen des Verbindungsstatus eines Geräts vor dem Senden eines Befehls ist z. B. kein unterstütztes Muster. Überprüfen Sie die [Drosselungsraten](#throttling) für die Geräte-Identitätsregistrierung und das Muster des [Gerätetakts][lnk-guidance-heartbeat].

### Deaktivieren von Geräten

Sie können Geräte deaktivieren, indem Sie die Eigenschaft **status** für eine Identität in der Registrierung aktualisieren. Typischerweise wird dieser Ansatz in zwei Szenarien verwendet:

- Während eines Vorgangs zur Bereitstellungsorchestrierung. Weitere Informationen finden Sie unter [Entwerfen der Lösung – Gerätebereitstellung][lnk-guidance-provisioning].
- Wenn aus einem beliebigen Grund die Sicherheit eines Geräts als gefährdet eingestuft oder das Gerät nicht mehr als autorisiert betrachtet wird.

### Exportieren von Geräteidentitäten <a id="importexport"></a>

Exportvorgänge sind Aufträge mit langer Ausführungszeit, die einen vom Kunden bereitgestellten Blobcontainer zum Speichern von Geräte-Identitätsdaten verwenden, die aus der Identitätsregistrierung gelesen werden.

Sie können einen Massenexport von Geräte-Identitäten aus der Identitätsregistrierung eines IoT Hubs durchführen. Hierbei werden asynchrone Vorgänge im [Endpunkt des IoT Hub-Ressourcenanbieters](#endpoints) verwendet.

Folgende Vorgänge sind für Exportaufträge möglich:

* Erstellen eines Exportauftrags
* Abrufen des Status eines ausgeführten Auftrags
* Abbrechen eines ausgeführten Auftrags

> [AZURE.NOTE] Für jeden Hub kann zu jedem Zeitpunkt nur ein Auftrag ausgeführt werden.

Ausführliche Informationen zu den Import- und Export-APIs finden Sie unter [Azure IoT Hub – Ressourcenanbieter-APIs][lnk-resource-provider-apis].

Weitere Informationen zum Ausführen von Import- und Exportaufträgen finden Sie unter [Massenverwaltung von IoT Hub-Geräte-Identitäten][lnk-bulk-identity].

### Exportieren von Aufträgen

Alle Exportaufträge verfügen über folgenden Eigenschaften:

| Eigenschaft | Optionen | Beschreibung |
| -------- | ------- | ----------- |
| jobId | vom System generiert, wird bei der Erstellung ignoriert | |
| creationTime | vom System generiert, wird bei der Erstellung ignoriert | |
| endOfProcessingTime | vom System generiert, wird bei der Erstellung ignoriert | |
| Typ | schreibgeschützt | **ExportDevices** |
| status | vom System generiert, wird bei der Erstellung ignoriert | **Zur Warteschlange hinzugefügt**, **Gestartet**, **Abgeschlossen**, **Fehler** |
| progress | vom System generiert, wird bei der Erstellung ignoriert | Ganzzahliger Wert, der den Prozentsatz der Fertigstellung angibt. |
| outputBlobContainerURI | erforderlich für alle Aufträge | Blob-SAS-URI (Shared Access Signature) mit Schreibzugriff auf einen Blobcontainer (siehe [Erstellen und Verwenden einer SAS mit dem Blob-Dienst][lnk-createuse-sas]). Wird zur Ausgabe des Auftragsstatus und der Ergebnisse verwendet. |
| excludeKeysInExport | optional | Falls **false**, werden Schlüssel in der Exportausgabe eingeschlossen. Andernfalls werden Schlüssel als **null** exportiert. Die Standardeinstellung ist **false**. |
| failureReason | vom System generiert, wird bei der Erstellung ignoriert | Wenn der Status **Fehler** lautet, handelt es sich um eine Zeichenfolge, die die Ursache angibt. |

Exportaufträge akzeptieren einen Blob-SAS-URI als Parameter. Dadurch wird der Schreibzugriff auf einen Blobcontainer gewährt, in den der Auftrag die Ergebnisse ausgeben kann.

Der Auftrag schreibt die Ausgabeergebnisse in den angegebenen Blobcontainer in eine Datei namens **devices.txt**. Diese Datei enthält die Geräteidentitäten, die gemäß Festlegung in den [Geräteidentitätseigenschaften](#deviceproperties) als JSON serialisiert wurden. Der Authentifizierungswert ist für jedes Gerät in der Datei **devices.txt** auf **null** festgelegt, wenn **excludeKeysInExport** auf **true** festgelegt ist.

**Beispiel**:

```
{"id":"devA","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
{"id":"devB","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
{"id":"devC","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
```

### Importieren von Geräte-Identitäten

Importvorgänge sind Aufträge mit langer Ausführungszeit, die einen vom Kunden bereitgestellten Blobcontainer zum Schreiben von Geräte-Identitätsdaten in die Geräte-Identitätsregistrierung verwenden.

Sie können einen Massenimport von Geräte-Identitäten in die Identitätsregistrierung eines IoT Hubs durchführen. Hierbei werden asynchrone Vorgänge im [Endpunkt des IoT Hub-Ressourcenanbieters](#endpoints) verwendet.

Folgende Vorgänge sind für Importaufträge möglich:

* Erstellen eines Importauftrags
* Abrufen des Status eines ausgeführten Auftrags
* Abbrechen eines ausgeführten Auftrags

> [AZURE.NOTE] Für jeden Hub kann zu jedem Zeitpunkt nur ein Auftrag ausgeführt werden.

Ausführliche Informationen zu den Import- und Export-APIs finden Sie unter [Azure IoT Hub – Ressourcenanbieter-APIs][lnk-resource-provider-apis].

Weitere Informationen zum Ausführen von Import- und Exportaufträgen finden Sie unter [Massenverwaltung von IoT Hub-Geräte-Identitäten][lnk-bulk-identity].

### Importieren von Aufträgen

Alle Importaufträge verfügen über folgende Eigenschaften:

| Eigenschaft | Optionen | Beschreibung |
| -------- | ------- | ----------- |
| jobId | vom System generiert, wird bei der Erstellung ignoriert | |
| creationTime | vom System generiert, wird bei der Erstellung ignoriert | |
| endOfProcessingTime | vom System generiert, wird bei der Erstellung ignoriert | |
| Typ | schreibgeschützt | **ImportDevices** |
| status | vom System generiert, wird bei der Erstellung ignoriert | **Zur Warteschlange hinzugefügt**, **Gestartet**, **Abgeschlossen**, **Fehler** |
| progress | vom System generiert, wird bei der Erstellung ignoriert | Ganzzahliger Wert, der den Prozentsatz der Fertigstellung angibt. |
| outputBlobContainerURI | erforderlich für alle Aufträge | Blob-SAS-URI (Shared Access Signature) mit Schreibzugriff auf einen Blobcontainer (siehe [Erstellen und Verwenden einer SAS mit dem Blob-Dienst][lnk-createuse-sas]). Wird zur Ausgabe des Auftragsstatus verwendet. |
| inputBlobContainerURI | erforderlich | Blob-SAS-URI (Shared Access Signature) mit Lesezugriff auf einen Blobcontainer (siehe [Erstellen und Verwenden einer SAS mit dem Blob-Dienst][lnk-createuse-sas]). Der Auftrag liest die zu importierenden Geräte-Informationen aus diesem Blob. |
| failureReason | vom System generiert, wird bei der Erstellung ignoriert | Wenn der Status **Fehler** lautet, handelt es sich um eine Zeichenfolge, die die Ursache angibt. |

Importaufträge verwenden zwei Blob-SAS-URIs (Shared Access Signature) als Parameter. Einer gewährt Schreibzugriff auf einen Blobcontainer, um dem Auftrag das Ausgeben seines Status zu ermöglichen. Der andere gewährt Lesezugriff auf einen Blobcontainer, um dem Importauftrag das Lesen seiner Eingabedaten zu ermöglichen.

Der Auftrag liest die Eingabedaten aus dem angegebenen Blobcontainer in einer Datei namens **devices.txt**. Diese Datei enthält die Geräteidentitäten, die gemäß Festlegung in den [Geräteidentitätseigenschaften](#deviceproperties) als JSON serialisiert wurden. Sie können das Standardimportverhalten jedes Geräts überschreiben, indem Sie eine **ImportMode**-Eigenschaft hinzufügen. Diese Eigenschaft kann einen der folgenden Werte annehmen:

| importMode | Beschreibung |
| -------- | ----------- |
| **createOrUpdate** | Wenn ein Gerät mit der angegebenen **ID** nicht vorhanden ist, wird es neu registriert. <br/>Wenn das Gerät bereits vorhanden ist, werden vorhandene Informationen mit den bereitgestellten Eingabedaten ohne Berücksichtigung des **ETag**-Werts überschrieben. |
| **create** | Wenn ein Gerät mit der angegebenen **ID** nicht vorhanden ist, wird es neu registriert. <br/>Wenn das Gerät bereits vorhanden ist, wird ein Fehler in die Protokolldatei geschrieben. |
| **update** | Wenn ein Gerät mit der angegebenen **ID** bereits vorhanden ist, werden vorhandene Informationen mit den bereitgestellten Eingabedaten ohne Berücksichtigung des **ETag**-Werts überschrieben. <br/>Wenn das Gerät nicht vorhanden ist, wird ein Fehler in die Protokolldatei geschrieben. |
| **updateIfMatchETag** | Wenn ein Gerät mit der angegebenen **ID** bereits vorhanden ist, werden vorhandene Informationen mit den bereitgestellten Eingabedaten nur überschrieben, wenn es eine Übereinstimmung mit einem **ETag** gibt. <br/>Wenn das Gerät nicht vorhanden ist, wird ein Fehler in die Protokolldatei geschrieben. <br/>Wenn es keine Übereinstimmung mit einem **ETag** gibt, wird ein Fehler in die Protokolldatei geschrieben. |
| **createOrUpdateIfMatchETag** | Wenn ein Gerät mit der angegebenen **ID** nicht vorhanden ist, wird es neu registriert. <br/>Wenn das Gerät bereits vorhanden ist, werden vorhandene Informationen mit den bereitgestellten Eingabedaten nur überschrieben, wenn es eine Übereinstimmung mit einem **ETag** gibt. <br/>Wenn es keine Übereinstimmung mit einem **ETag** gibt, wird ein Fehler in die Protokolldatei geschrieben. |
| **delete** | Wenn ein Gerät mit der angegebenen **ID** bereits vorhanden ist, wird es ohne Bezug auf den **ETag**-Wert gelöscht. <br/>Wenn das Gerät nicht vorhanden ist, wird ein Fehler in die Protokolldatei geschrieben. |
| **deleteIfMatchETag** | Wenn ein Gerät mit der angegebenen **ID** bereits vorhanden ist, wird es nur gelöscht, wenn es eine Übereinstimmung mit einem **ETag** gibt. Wenn das Gerät nicht vorhanden ist, wird ein Fehler in die Protokolldatei geschrieben. <br/>Wenn es keine Übereinstimmung mit einem ETag gibt, wird ein Fehler in die Protokolldatei geschrieben. |

**Beispiel**:

```
{"id":"devA","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"delete"}
{"id":"devB","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"createOrUpdate"}
{"id":"devC","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"create"}
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

**Beispiel**. In einer normalen IoT-Lösung:
- Die Geräteverwaltungskomponente verwendet die Richtlinie *registryReadWrite*.
- Die Ereignisprozessorkomponente verwendet die Richtlinie *service*.
- Die Laufzeit-Geschäftslogikkomponente verwendet die Richtlinie *service*.
- Einzelne Geräte stellen unter Verwendung von Anmeldeinformationen eine Verbindung her, die in der IoT Hub-Identitätsregistrierung gespeichert sind.

Einen Leitfaden zu IoT Hub-Sicherheitsthemen finden Sie im Abschnitt zur Sicherheit unter [Entwerfen der Lösung][lnk-guidance-security].

### Authentifizierung

Azure IoT Hub gewährt Zugriff auf Endpunkte, indem ein Token zur Verifizierung mit gemeinsam genutzten Zugriffsrichtlinien und Sicherheitsanmeldeinformationen für die Geräteidentität verglichen wird.

Sicherheitsanmeldeinformationen, beispielsweise symmetrische Schlüssel, werden niemals über eine physische Verbindung gesendet.

> [AZURE.NOTE] Der Azure IoT Hub-Ressourcenanbieter wird über Ihr Azure-Abonnement geschützt, ebenso wie alle Anbieter im [Azure-Ressourcen-Manager][lnk-azure-resource-manager].

Weitere Informationen zur Erstellung und Verwendung von Sicherheitstoken finden Sie im Artikel zu den [IoT Hub-Sicherheitstoken][lnk-sas-tokens].

#### Protokolldetails

Jedes unterstützte Protokoll, z. B. AMQP, MQTT und HTTP, transportiert Token auf unterschiedliche Weise.

HTTP implementiert die Authentifizierung, indem ein gültiges Token in den Anforderungsheader **Authorization** eingeschlossen wird. Das Token kann auch über den Abfrageparameter **Authorization** transportiert werden.

Bei Verwendung von [AMQP][lnk-amqp] unterstützt IoT Hub [SASL PLAIN][lnk-sasl-plain] und die auf [AMQP-Ansprüchen basierte Sicherheit][lnk-cbs].

Im Falle der auf AMQP-Ansprüchen basierten Sicherheit gibt der Standard vor, wie die oben aufgeführten Token übertragen werden.

Für SASL PLAIN kann der **Benutzername** Folgendes sein:

* `{policyName}@sas.root.{iothubName}` im Fall von Token auf Hubebene.
* `{deviceId}` im Fall von Token für Geräte.

In beiden Fällen enthält das Kennwortfeld das Token gemäß der Beschreibung im Artikel zu den [IoT Hub-Sicherheitstoken][lnk-sas-tokens].

Bei Verwenden von MQTT enthält das CONNECT-Paket die Geräte-ID als „Client-ID, {Iot Hub-Hostname}/{Geräte-ID} im Feld „Benutzername“ und ein SAS-Token im Feld „Kennwort“. {Iot Hub-Hostname} muss der vollständigen CNAME des IoT Hubs (z. B. contoso.azure-devices.net) sein.

##### Beispiel: #####

Benutzername (bei „Geräte-ID“ wird die Groß-/Kleinschreibung berücksichtigt): `iothubname.azure-devices.net/DeviceId`

Kennwort (SAS mit dem Geräte-Explorer generieren): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] Die [Azure IoT Hub-SDKs][lnk-apis-sdks] generieren automatisch Token, wenn eine Verbindung mit dem Dienst hergestellt wird. In einigen Fällen unterstützen die SDKs nicht alle Protokolle oder Authentifizierungsmethoden.

#### SASL PLAIN und CBS im Vergleich

Bei Verwendung von SASL PLAIN kann ein Client, der eine Verbindung mit einem IoT Hub herstellt, ein einziges Token für jede TCP-Verbindung verwenden. Wenn das Token abläuft, wird die TCP-Verbindung mit dem Dienst getrennt, und es wird ein Versuch zur erneuten Verbindungsherstellung ausgelöst. Dieses Verhalten ist für eine Komponente im Anwendungs-Back-End unproblematisch, kann für eine geräteseitige Anwendung jedoch sehr schädlich sein. Dies hat folgende Gründe:

*  Gateways stellen eine Verbindung üblicherweise für eine Vielzahl von Geräten her. Bei Verwendung von SASL PLAIN muss für jedes Gerät, das eine Verbindung mit einem IoT Hub herstellen möchte, eine eigene TCP-Verbindung erstellt werden. Dies erhöht den Verbrauch von Rechen- und Netzwerkressourcen erheblich und führt zu einer höheren Latenz für jede Geräteverbindung.
* Der erhöhte Ressourcenverbrauch bei der erneuten Verbindungsherstellung nach jedem Tokenablauf wirkt sich negativ auf Geräte mit Ressourceneinschränkungen aus.

### Gültigkeitsbereich für Anmeldeinformationen auf Hubebene

Sie können den Bereich für Sicherheitsrichtlinien auf Hubebene festlegen, indem Sie Token mit eingeschränktem Ressourcen-URI erstellen. Beispielsweise lautet der Endpunkt zum Senden von D2C-Nachrichten von einem Gerät **/devices/{Geräte-ID}/messages/events**. Sie können eine SAS-Richtlinie (shared access policy) auf Hubebene mit **DeviceConnect**-Berechtigungen auch zum Signieren eines Tokens verwenden, dessen resourceURI-Element **/devices/{Geräte-ID}** lautet. So wird ein Token erstellt, das nur zum Senden von Gerätenachrichten im Namen des Geräts mit dieser **deviceId** verwendet werden kann.

Dieser Mechanismus ist mit einer [Event Hubs-Herausgeberrichtlinie][lnk-event-hubs-publisher-policy] vergleichbar und ermöglicht die Implementierung von benutzerdefinierten Authentifizierungsmethoden. Siehe hierzu den Abschnitt zur Sicherheit unter [Entwerfen der Lösung][lnk-guidance-security].

## Nachrichten

IoT Hub bietet ein einfaches Messaging für die Kommunikation:
- [Cloud-zu-Gerät (C2D):](#c2d) Von einem Anwendungs-Back-End (*Dienst* oder *Cloud*).
- [Gerät-zu-Cloud (D2C):](#d2c) Von einem Gerät zu einem Anwendungs-Back-End.

Die wichtigsten Eigenschaften beim IoT Hub-Messaging sind eine zuverlässige und stabile Übermittlung von Nachrichten. Dies bietet Ausfallsicherheit bei zeitweiligen Verbindungsproblemen auf Geräteseite und Lastspitzen bei der Ereignisverarbeitung auf Cloudseite. IoT Hub implementiert *mindestens einmalig* Übermittlungsgarantien für das D2C- und C2D-Messaging.

IoT Hub unterstützt mehrere geräteseitige Protokolle (z. B. AMQP und HTTP/1). Um eine nahtlose Interoperabilität zwischen Protokollen zu gewährleisten, definiert IoT Hub ein gemeinsames Nachrichtenformat, das von allen geräteseitigen Protokollen unterstützt wird.

### Nachrichtenformat <a id="messageformat"></a>

IoT Hub-Nachrichten umfassen:

* Einen Satz an *Systemeigenschaften*. Diese Eigenschaften werden von IoT Hub interpretiert oder festgelegt. Dieser Satz ist vordefiniert.
* Einen Satz an *Anwendungseigenschaften*. Dies ist ein Wörterbuch mit Zeichenfolgeneigenschaften, die die Anwendung definieren und auf die die Anwendung zugreifen kann, ohne den Nachrichtentext deserialisieren zu müssen. IoT Hub ändert diese Eigenschaften nie.
* Ein nicht lesbarer binärer Textkörper.

Weitere Informationen dazu, wie die Nachricht in verschiedenen Protokollen codiert wird, finden Sie unter [IoT Hub-APIs und -SDKs][lnk-apis-sdks].

Die folgende Tabelle zeigt den Satz an Systemeigenschaften in IoT Hub-Nachrichten.

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| MessageId | Eine vom Benutzer festgelegte Kennung für die Nachricht, wird üblicherweise für Anforderung-Antwort-Muster verwendet. Format: Eine Zeichenfolge mit Berücksichtigung von Klein-/Großschreibung (bis zu 128 Zeichen lang), die aus alphanumerischen ASCII-Zeichen (7 Bit) + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` besteht. |
| Sequenznummer | Eine Nummer (für jede Gerätewarteschlange eindeutig), die jeder C2D-Nachricht von IoT Hub zugewiesen wird |
| To | Gibt in [C2D](#c2d)-Nachrichten das Ziel an. |
| ExpiryTimeUtc | Datum und Uhrzeit des Nachrichtenablaufs. |
| EnqueuedTime | Datum und Uhrzeit des Empfangs der Nachricht durch IoT Hub. |
| CorrelationId | Zeichenfolgeneigenschaft in einer Antwortnachricht, die normalerweise die Nachrichten-ID der Anforderung im Anforderung-Antwort-Muster enthält. |
| UserId | Dient zum Festlegen des Ursprungs von Nachrichten. Wenn IoT Hub Nachrichten generiert, wird diese Eigenschaft auf `{iot hub name}` festgelegt. |
| Ack | Wird in C2D-Nachrichten verwendet, um IoT Hub anzuweisen, als Ergebnis der Nachrichtenverarbeitung durch das Gerät Feedback zu generieren. Mögliche Werte: **none** (Standardeinstellung): es wird keine Feedbacknachricht generiert, **positive**: Feedbacknachricht empfangen, wenn die Nachricht abgeschlossen wurde, **negative**: Feedbacknachricht empfangen, wenn die Nachricht ohne vollständige Verarbeitung durch das Gerät abgelaufen ist (oder die maximale Anzahl von Zustellversuchen erreicht wurde), **full**: Feedback wird sowohl bei erfolgreicher als auch nicht erfolgreicher Nachrichtenverarbeitung generiert. Weitere Informationen finden Sie unter [Nachrichtenfeedback](#feedback). |
| ConnectionDeviceId | Wird von IoT Hub für D2C-Nachrichten festgelegt. Diese Eigenschaft enthält die **deviceId** des Geräts, das die Nachricht sendet. |
| ConnectionDeviceGenerationId | Wird von IoT Hub für D2C-Nachrichten festgelegt. Diese Eigenschaft enthält die **generationId** (gemäß [Geräteidentitätseigenschaften](#deviceproperties)) des Geräts, das die Nachricht gesendet hat. |
| ConnectionAuthMethod | Wird von IoT Hub für D2C-Nachrichten festgelegt. Diese Eigenschaft enthält Informationen zur verwendeten Methode zum Authentifizieren des Geräts, das die Nachricht sendet. Weitere Informationen finden Sie unter [D2C-Anti-Spoofing](#antispoofing).|

### Auswählen des Kommunikationsprotokolls <a id="amqpvshttp"></a>

IoT Hub unterstützt für die geräteseitige Kommunikation die Protokolle [AMQP][lnk-amqp], AMQP über WebSockets, MQTT und HTTP/1. Bei der Verwendung dieser Protokolle müssen Sie Folgendes berücksichtigen:

* **C2D-Muster**. HTTP/1 verfügt über keine effiziente Methode zum Implementieren von Serverpushvorgängen. Daher fragen Geräte bei Verwendung von HTTP/1 IoT Hub für C2D-Nachrichten ab. Dies ist sowohl für das Gerät als auch für IoT Hub ineffizient. Die aktuellen Leitlinien bei Verwenden von HTTP/1 sehen eine Geräteabfrage mindestens alle 25 Minuten vor. AMQP und MQTT unterstützen dagegen Serverpushvorgänge beim Empfang von C2D-Nachrichten und ermöglichen es, Nachrichten von IoT Hub sofort per Push an das Gerät zu übertragen. Wenn die Übermittlungslatenz eine wichtige Rolle spielt, ist entweder AMQP oder MQTT das zu bevorzugende Protokoll. Für Geräte, die nur selten verbunden werden, kann durchaus HTTP/1 verwendet werden.
* **Bereichsgateways**. Bei Verwendung von HTTP/1 und MQTT ist es nicht möglich, mehrere Geräte (jedes mit eigenen Anmeldeinformationen pro Gerät) mithilfe der gleichen TLS-Verbindung zu verbinden. Daraus folgt, dass diese Protokolle für die Implementierung von [Bereichsgatewayszenarios][lnk-azure-gateway-guidance] nicht optimal sind, da sie eine TLS-Verbindung zwischen dem Bereichsgateway und IoT Hub für jedes Gerät benötigen, das mit dem Bereichsgateway verbunden ist.
* **Geräte mit eingeschränkten Ressourcen**. MQTT- und HTTP/1-Bibliotheken haben weniger Speicherbedarf als die AMQP-Bibliotheken. Wenn daher das Gerät über limitierte Ressourcen verfügt (beispielsweise weniger als 1 MB RAM), stehen möglicherweise nur diese Protokolle als Protokollimplementierung zur Verfügung.
* **Netzwerkausnahme**. MQTT lauscht standardmäßig an Port 8883. Dies kann Probleme in Netzwerken verursachen, die für Nicht-HTTP-Protokolle geschlossen sind. HTTP und AMQP (über WebSockets) können in diesem Szenario verwendet werden.
* **Größe der Nutzlast**. AMQP und MQTT sind binäre Protokolle, die erheblich kompakter als HTTP/1 sind.

Im Allgemeinen sollten Sie AMQP (oder AMQP über WebSockets) möglichst immer verwenden und MQTT nur dann nutzen, wenn Ressourceneinschränkungen den Einsatz von AMQP verhindern. HTTP/1 sollte nur verwendet werden, wenn Netzwerkausnahmen und die Netzwerkkonfiguration die Verwendung von MQTT und AMQP verhindern. Darüber hinaus muss bei Verwendung von HTTP/1 jedes Gerät mindestens alle 25 Minuten eine Abfrage auf C2D-Nachrichten durchführen.

> [AZURE.NOTE] In der Entwicklungsphase darf freilich häufiger als alle 25 Minuten eine Abfrage erfolgen.

#### Hinweise zur MQTT-Unterstützung
IoT Hub implementiert die MQTT-Protokollversion 3.1.1 mit den folgenden Einschränkungen und einem bestimmten Verhalten:

  * **QoS 2 wird nicht unterstützt:** Wenn ein Geräteclient eine Nachricht mit **QoS 2** veröffentlicht, schließt IoT Hub die Netzwerkverbindung. Wenn ein Geräteclient ein Thema mit **QoS 2** abonniert, gewährt IoT Hub im **SUBACK**-Paket maximal die QoS-Ebene 1.
  * **Beibehaltung:** Wenn ein Geräteclient eine Nachricht mit auf 1 festgelegtem RETAIN-Flag veröffentlicht, fügt IoT Hub der Nachricht die Anwendungseigenschaft **x-opt-retain** hinzu. Dies bedeutet, dass IoT Hub die Beibehaltungsnachricht nicht beständig speichert, sondern an die Back-End-Anwendung übergibt.

Schließlich sollte noch der Einsatz eines [Azure IoT-Protokollgateways][lnk-azure-protocol-gateway] erwogen werden, das die Bereitstellung eines hochleistungsfähigen benutzerdefinierten Protokollgateways mit einer direkten Schnittstelle zu IoT Hub ermöglicht. Das Azure IoT-Protokollgateway dient zum Anpassen des Geräteprotokolls zum Unterstützen von Brownfield MQTT-Bereitstellungen oder anderer benutzerdefinierter Protokolle. Der Nachteil bei diesem Ansatz ist die Anforderung, selbst ein Protokollgateway zu hosten und zu verwalten.

### Gerät an Cloud <a id="d2c"></a>

Wie im Abschnitt [Endpunkte](#endpoints) erläutert, werden D2C-Nachrichten über einen geräteseitigen Endpunkt (**/devices/{Geräte-ID}/messages/events**) gesendet und über einen dienstseitigen Endpunkt (**/messages/events**) empfangen, der mit [Event Hubs][lnk-event-hubs] kompatibel ist. Auf diese Weise können Sie standardmäßige Event Hubs-Integration und SDKs zum Empfangen von D2C-Nachrichten verwenden.

IoT Hub implementiert das D2C-Messaging ähnlich wie [Event Hubs][lnk-event-hubs], wobei das D2C-Messaging von IoT Hub eher mit *Ereignissen* in Event Hubs als mit [Service Bus][lnk-servicebus]-*Nachrichten* vergleichbar ist.

Diese Implementierung hat folgende Auswirkungen:

* Ähnlich wie *Ereignisse* in Event Hubs sind D2C-Nachrichten dauerhaft und werden in einem IoT Hub bis zu 7 Tage aufbewahrt (siehe [Optionen für die D2C-Konfiguration](#d2cconfiguration)).
* D2C-Nachrichten werden für einen festen Satz an Partitionen partitioniert, der zum Zeitpunkt der Erstellung festgelegt wird (siehe [Optionen für die D2C-Konfiguration](#d2cconfiguration)).
* Analog zu Event Hubs müssen Clients, die D2C-Nachrichten lesen, Partitionen und Prüfpunkte verarbeiten können. Siehe [Event Hubs: Ereignisconsumer][lnk-event-hubs-consuming-events].
* Wie Event Hubs-Ereignisse können D2C-Nachrichten maximal 256 KB umfassen und in Batches gruppiert werden, um Sendevorgänge zu optimieren. Batches können maximal 256 KB groß sein und maximal 500 Nachrichten enthalten.

Es gibt jedoch einige wichtige Unterschiede zwischen dem D2C-Messaging von IoT Hub und Event Hubs:

* Wie im Abschnitt [Sicherheit](#security) erläutert, ermöglicht IoT Hub eine Authentifizierung und Zugriffsteuerung auf Gerätebasis.
* IoT Hub unterstützt Millionen von gleichzeitig verbundenen Geräten (siehe [Kontingente und Drosselung](#throttling)), während Event Hubs auf 5.000 AMQP-Verbindungen pro Namespace beschränkt ist.
* IoT Hub ermöglicht keine zufällige Partitionierung mit **PartitionKey**. D2C-Nachrichten werden basierend auf ihrer ursprünglichen **Geräte-ID** partitioniert.
* Die Skalierung funktioniert in IoT Hub etwas anders als in Event Hubs. Weitere Informationen finden Sie unter [Skalieren von IoT Hub][lnk-guidance-scale].

Hinweis: Dies bedeutet nicht, dass Sie in allen Szenarien IoT Hub durch Event Hubs ersetzen können. In einigen Szenarien mit Ereignisverarbeitung kann es beispielsweise erforderlich sein, Ereignisse in Bezug auf eine andere Eigenschaft oder ein anderes Feld neu zu partitionieren, bevor die Datenströme analysiert werden können. In diesem Szenario können Sie mit einem Event Hub zwei Bestandteile der Pipeline zur Datenstromverarbeitung entkoppeln. Weitere Informationen finden Sie unter *Partitionen* in [Übersicht über Azure Event Hubs][lnk-eventhub-partitions].

Ausführliche Informationen zum Verwenden des D2C-Messaging finden Sie unter [IoT Hub-APIs und -SDKs][lnk-apis-sdks].

> [AZURE.NOTE] Bei Verwendung von HTTP zum Senden von D2C-Nachrichten dürfen diese Zeichenfolgen nur ASCII-Zeichen enthalten: Systemeigenschaftswerte sowie Namen und Werte von Anwendungseigenschaften.

#### Datenverkehr ohne Telemetrie

In vielen Fällen senden Geräte nicht nur Telemetriedatenpunkte, sondern auch Nachrichten und Anforderungen, die eine Ausführung und Verarbeitung auf der Anwendungsschicht mit der Geschäftslogik erfordern. Beispiele: Kritische Warnungen, die eine bestimmte Aktion im Back-End auslösen müssen, oder Geräteantworten auf vom Back-End gesendete Befehle.

Weitere Informationen zur besten Verarbeitungsmethode für diese Art von Nachrichten finden Sie im Abschnitt [D2C-Verarbeitung][lnk-guidance-d2c-processing].

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

Wie im Abschnitt [Endpunkte](#endpoints) beschrieben, können Sie C2D-Nachrichten über einen dienstseitigen Endpunkt (**/messages/devicebound**) senden, und ein Gerät kann diese Nachrichten über einen geräteseitigen Endpunkt (**/devices/{Geräte-ID}/messages/devicebound**) empfangen.

Jede C2D-Nachricht wird einem bestimmten Gerät zugeordnet, indem die **to**-Eigenschaft auf **/devices/{Geräte-ID}/messages/devicebound** festgelegt wird.

**Wichtig**: Jede Gerätewarteschlange kann maximal 50 C2D-Nachrichten enthalten. Der Versuch, eine größere Anzahl von Nachrichten an dasselbe Gerät zu senden, führt zu einem Fehler.

> [AZURE.NOTE] Beim Senden von C2D-Nachrichten dürfen diese Zeichenfolgen nur ASCII-Zeichen enthalten: Systemeigenschaftswerte sowie Namen und Werte von Anwendungseigenschaften.

#### Nachrichtenlebenszyklus <a id="message lifecycle"></a>

Um *mindestens eine* Übermittlungsgarantie zu implementieren, werden C2D-Nachrichten dauerhaft in Gerätewarteschlangen gespeichert, und Geräte müssen den *Abschluss* der Nachricht explizit bestätigen, damit IoT Hub eine Nachricht aus der Warteschlange entfernen kann. Auf diese Weise wird Ausfallsicherheit bei Verbindungs- und Gerätefehlern gewährleistet.

Die folgende Abbildung zeigt den Lebenszyklus einer C2D-Nachricht und ihren jeweiligen Status.

![Lebenszyklus von C2D-Nachrichten][img-lifecycle]

Wenn der Dienst eine Nachricht sendet, wird diese als *zur Warteschlange hinzugefügt* betrachtet. Wenn ein Gerät eine Nachricht *empfangen* möchte, *sperrt* IoT Hub die Nachricht (Status wird auf **Nicht sichtbar** gesetzt), um anderen Threads auf demselben Gerät das Empfangen anderer Nachrichten zu ermöglichen. Wenn ein Gerätethread die Verarbeitung einer Nachricht abschließt, wird IoT Hub hierüber durch den *Abschluss* der Nachricht benachrichtigt.

Ein Gerät kann auch Folgendes durchführen:
- *Ablehnen* der Nachricht: IoT Hub versetzt sie in den Status **Unzustellbar**.
- *Verwerfen* der Nachricht: IoT Hub platziert die Nachricht wieder in der Warteschlange mit dem Status **Zur Warteschlange hinzugefügt**.

Bei der Nachrichtenverarbeitung durch den Thread könnte ein Fehler auftreten, ohne dass IoT Hub hierüber benachrichtigt wird. In diesem Fall werden Nachrichten automatisch vom Status **Nicht sichtbar** zurück in den Status **Zur Warteschlange hinzugefügt** versetzt, wenn ein *Timeout für die Sichtbarkeit (oder Sperrung)* abgelaufen ist (Standardeinstellung: 1 Minute). Eine Nachricht kann zwischen den Statuswerten **Zur Warteschlange hinzugefügt** und **Nicht sichtbar** maximal so oft wechseln, wie in der Eigenschaft *Anzahl maximaler Zustellungen* in IoT Hub festgelegt wurde. Nachdem diese Anzahl überschritten wurde, legt IoT Hub den Status der Nachricht auf **Unzustellbar** fest. Ebenso kennzeichnet IoT Hub eine Nachricht als **Unzustellbar**, wenn ihre Gültigkeitsdauer abgelaufen ist (siehe [Gültigkeitsdauer](#ttl)).

Ein Tutorial zu C2D-Nachrichten finden Sie unter [Erste Schritte mit Azure IoT Hub-Nachrichten zwischen Cloud und Gerät][lnk-getstarted-c2d-tutorial]. Informationen dazu, wie verschiedene APIs und SDKs die C2D-Funktionalität verfügbar machen, finden Sie unter [IoT Hub-APIs und -SDKs][lnk-apis-sdks].

> [AZURE.NOTE] Typischerweise werden C2D-Nachrichten immer dann abgeschlossen, wenn der Verlust der Nachricht keine Auswirkung auf die Anwendungslogik hat. Dies kann in vielen verschiedenen Szenarien zutreffen. Es trifft beispielsweise dann zu, wenn der Nachrichteninhalt erfolgreich im lokalen Speicher abgelegt oder ein Vorgang erfolgreich ausgeführt wurde oder wenn die Nachricht kurzlebige Informationen enthält, deren Verlust sich nicht auf die Funktionalität der Anwendung auswirkt. Bei Tasks mit langer Ausführungsdauer können Sie die C2D-Nachricht abschließen, nachdem die Taskbeschreibung in den lokalen Speicher geschrieben wurde. Anschließend wird das Anwendungs-Back-End mit einer oder mehreren D2C-Nachrichten in verschiedenen Phasen über den Taskfortschritt benachrichtigt.

#### Gültigkeitsdauer <a id="ttl"></a>

Jede C2D-Nachricht verfügt über eine Gültigkeitsdauer. Diese kann (in der Eigenschaft **ExpiryTimeUtc**) explizit durch den Dienst oder durch IoT Hub mithilfe der standardmäßigen als IoT Hub-Eigenschaft gesetzten *Gültigkeitsdauer* festgelegt werden. Siehe [Optionen für die C2D-Konfiguration](#c2dconfiguration).

> [AZURE.NOTE] Eine gängige Methode, den Vorteil des Nachrichtenablaufs zu nutzen, ist die Festlegung einer kurzen Gültigkeitsdauer für Werte. So vermeiden Sie, dass Nachrichten an getrennte Geräte gesendet werden. Dasselbe, Ergebnis wird erzielt, wenn der Geräteverbindungsstatus aufrechterhalten wird; diese Variante ist aber deutlich ineffizienter. Durch die Anforderung von Nachrichtenbestätigungen ist es möglich, von IoT Hub darüber informiert zu werden, welche der Geräte in der Lage sind, Nachrichten zu empfangen und welche nicht online sind bzw. bei welchen bei der Zustellung ein Fehler auftrat.

#### Nachrichtenfeedback <a id="feedback"></a>

Beim Senden einer C2D-Nachricht kann der Dienst das Übermitteln von Feedback auf Nachrichtenbasis anfordern, um über den finalen Status dieser Nachricht informiert zu werden.

- Wenn Sie die **Ack**-Eigenschaft auf **positive** festlegen, generiert IoT Hub nur dann eine Feedbacknachricht, wenn die C2D-Nachricht den Status **Abgeschlossen** erreicht hat.
- Wenn Sie die **Ack**-Eigenschaft auf **negative** festlegen, generiert IoT Hub nur dann eine Feedbacknachricht, wenn die C2D-Nachricht den Status **Unzustellbar** erreicht.
- Bei Festlegung der **Ack**-Eigenschaft auf **full** generiert IoT Hub in beiden Fällen eine Feedbacknachricht.

> [AZURE.NOTE] Wenn **Ack** auf **full** festgelegt ist und keine Feedbacknachricht empfangen wird, bedeutet dies, dass die Feedbacknachricht abgelaufen ist. Der Dienst kann in diesem Fall nicht ermitteln, wo die ursprüngliche Nachricht abgeblieben ist. In der Praxis sollte ein Dienst sicherstellen, dass Feedback verarbeitet werden kann, bevor es abläuft. Die maximale Ablaufzeit beträgt zwei Tage, daher sollte ausreichend Zeit verbleiben, um den Dienst zu starten, wenn ein Fehler auftritt.

Wie im Abschnitt [Endpunkte](#endpoints) erläutert, übermittelt IoT Hub Feedback in Form von Nachrichten über einen dienstseitigen Endpunkt (**/messages/servicebound/feedback**). Die Semantik für den Empfang von Feedback stimmt mit der für C2D-Nachrichten überein und weist den gleichen [Nachrichtenlebenszyklus](#message lifecycle) auf. Nachrichtenfeedback wird nach Möglichkeit in einer einzigen Nachricht zusammengefasst, die das folgende Format aufweist.

Jede von einem Gerät vom Feedback-Endpunkt empfangene Nachricht umfasst die folgenden Eigenschaften:

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| EnqueuedTime | Zeitstempel, der die Erstellung der Nachricht angibt. |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

Der Nachrichtenkörper ist ein serialisiertes JSON-Array aus Datensätzen, von denen jeder die folgenden Eigenschaften aufweist:

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| EnqueuedTimeUtc | Zeitstempel, der den Zeitpunkt des Nachrichtenergebnisses angibt. Diese Eigenschaft kann beispielsweise angeben, wann das Gerät abgeschlossen wurde oder die Nachricht abgelaufen ist. |
| OriginalMessageId | **MessageId** der C2D-Nachricht, auf die sich das Feedback bezieht. |
| StatusCode | Erforderlicher Ganzzahlwert. In von IoT Hub erzeugten Feedbacknachrichten verwendet. <br/> 0 = Erfolg <br/> 1 = Nachricht abgelaufen <br/> 2 = max. Anzahl von Sendungen überschritten <br/> 3 = Nachricht abgelehnt |
| Beschreibung | Zeichenfolgenwerte für **StatusCode**. |
| DeviceId | **DeviceId** des Zielgeräts für die C2D-Nachricht, auf die sich das Feedback bezieht. |
| DeviceGenerationId | **DeviceGenerationId** des Zielgeräts für die C2D-Nachricht, auf die sich das Feedback bezieht. |


**Wichtig**: Der Dienst muss eine **MessageId** für die C2D-Nachricht angeben, damit das Feedback der ursprünglichen Nachricht zugeordnet werden kann.

**Beispiel**. Nachfolgend wird ein Beispiel für eine Feedbacknachricht gezeigt.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
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
| defaultTtlAsIso8601 | Standardmäßige Gültigkeitsdauer für C2D-Nachrichten. | ISO\_8601-Intervall bis 2D (mindestens 1 Minute). Standardwert: 1 Stunde. |
| maxDeliveryCount | Maximale Zustellungsanzahl für C2D-Gerätewarteschlangen pro Gerät. | 1 bis 100. Standardwert: 10 |
| feedback.ttlAsIso8601 | Aufbewahrungsdauer für dienstgebundene Feedbacknachrichten. | ISO\_8601-Intervall bis 2D (mindestens 1 Minute). Standardwert: 1 Stunde. |
| feedback.maxDeliveryCount | Maximale Zustellungsanzahl für Feedbackwarteschlangen. | 1 bis 100. Standardwert: 100. |

Weitere Informationen finden Sie unter [Verwalten von IoT Hub][lnk-manage].

## Kontingente und Drosselung <a id="throttling"></a>

Jedes Azure-Abonnement kann maximal 10 IoT Hubs enthalten.

Jeder IoT Hub wird mit einer bestimmten Anzahl von Einheiten in einer spezifischen SKU bereitgestellt (weitere Informationen finden Sie unter [Azure IoT Hub – Preise][lnk-pricing]). Die SKU und Anzahl der Einheiten bestimmen das maximale tägliche Kontingent von Nachrichten, die Sie senden können.

Die SKU legt auch die Drosselungslimits fest, die IoT Hub für alle Vorgänge erzwingt.

### Vorgangsdrosselung

Bei der Vorgangsdrosselung wird die Datenübertragungsrate pro Minute begrenzt, um einen Missbrauch zu verhindern. IoT Hub versucht, möglichst keine Fehler zurückzugeben, es werden jedoch Ausnahmen ausgelöst, wenn die Drosselungsgrenze zu lange überschritten wird.

Die nachfolgende Liste zeigt alle erzwungenen Werte für die Drosselung. Die Werte beziehen sich auf einen einzelnen Hub.

| Drosselung | Wert pro Hub |
| -------- | ------------- |
| Identitätsregistrierungsvorgänge (Erstellen, Abrufen, Aktualisieren, Löschen) | 100/Minute/Einheit, bis zu 5.000/Minute |
| Geräteverbindungen | 120/Sekunden/Einheit (für S2), 12/Sekunden/Einheit (für S1); <br/>Mindestens 100/Sekunde. <br/>Zwei S1-Einheiten entsprechen beispielsweise 2*12 = 24/Sekunde. Es sind jedoch mindestens 100/Sekunde auf die Einheiten verteilt vorhanden. Mit neun S1-Einheiten erhalten Sie 108/s (9*12) über alle Einheiten. |
| Senden von Nachrichten von Geräten an die Cloud | 120/Sekunden/Einheit (für S2), 12/Sekunden/Einheit (für S1); <br/>Mindestens 100/Sekunde. <br/>Zwei S1-Einheiten entsprechen beispielsweise 2*12 = 24/Sekunde. Es sind jedoch mindestens 100/Sekunde auf die Einheiten verteilt vorhanden. Mit neun S1-Einheiten erhalten Sie 108/s (9*12) über alle Einheiten. |
| C2D-Sendevorgänge | 100/Minute/Einheit |
| C2D-Empfangsvorgänge | 1000/Minuten/Einheit |

**Hinweis**: Die Kontingente oder Drosselungsgrenzwerte können jederzeit angehoben werden, indem die Anzahl von bereitgestellten Einheiten in einem IoT Hub erhöht wird.

**Wichtig:** Identitätsregistrierungsvorgänge sind für die Verwendung zur Laufzeit in Szenarios für die Geräteverwaltung und -bereitstellung vorgesehen. Das Lesen oder Aktualisieren einer großen Anzahl von Geräteidentitäten wird durch [Import-/Exportaufträge](#importexport) unterstützt.

## Nächste Schritte

Nachdem Sie in diesem Dokument einen Überblick über die Entwicklung für IoT Hub erhalten haben, können Sie die folgenden Links nutzen, um mehr über diese Themen zu erfahren:

- [Erste Schritte mit IoT Hubs (Tutorial)][lnk-get-started]
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

[lnk-sas-tokens]: iot-hub-sas-tokens.md
[lnk-azure-gateway-guidance]: iot-hub-guidance.md#field-gateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-guidance-heartbeat]: iot-hub-guidance.md#heartbeat

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
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
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-manage]: iot-hub-manage-through-portal.md

<!----HONumber=AcomDC_0323_2016-->