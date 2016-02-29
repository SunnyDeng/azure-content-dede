<properties
 pageTitle="IoT Hub MQTT-Unterstützung | Microsoft Azure"
 description="Beschreibung der MQTT-Unterstützung auf IoT Hub-Ebene"
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

# IoT Hub MQTT-Unterstützung

IoT Hub ermöglicht Geräten mithilfe des Protokolls [MQTT v3.1.1][lnk-mqtt-org] das Kommunizieren mit den IoT Hub-Geräteendpunkten auf Port 8883. IoT Hub setzt voraus, dass die gesamte Gerätekommunikation mithilfe von TLS/SSL gesichert wird.

## Herstellen einer Verbindung mit einem IoT Hub

Ein Gerät kann sich entweder über die Bibliotheken in den [Microsoft Azure IoT SDKs][lnk-device-sdks] oder direkt über das Protokoll MQTT mit einem IoT Hub verbinden.

## Verwenden der SDKs für Geräteclients

[SDKs für Geräteclients][lnk-device-sdks] mit Unterstützung des Protokolls MQTT stehen für Java, Node.js, C und C# zur Verfügung. Die SDKs für Geräteclients verwenden die standardmäßige IoT Hub-Verbindungszeichenfolge zum Herstellen einer Verbindung mit einem IoT Hub. Um das MQTT-Protokoll verwenden zu können, muss der Clientprotokollparameter auf **MQTT** festgelegt werden. Standardmäßig verbinden sich die SDKs von Geräteclients mit einem IoT Hub, indem das **CleanSession**-Flag auf **0** festgelegt und **QoS-1** für den Nachrichtenaustausch mit dem IoT Hub verwendet wird.

Wenn ein Gerät mit einem IoT Hub verbunden ist, bieten die SDKs von Geräteclients Methoden, die dem Gerät das Senden von Nachrichten an und Empfangen von Nachrichten von einem IoT Hub ermöglichen.

Die folgende Tabelle enthält Links zu Codebeispielen für jede unterstützte Sprache und gibt die Parameter zum Herstellen einer Verbindung mit einem IoT Hub über das Protokoll MQTT an.

| Sprache | „Protocol“-Parameter |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | azure-iot-device-mqtt |
| [Java][lnk-sample-java] | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] | MQTT\_Protocol |
| [C#][lnk-sample-csharp] | TransportType.Mqtt |

## Direktes Verwenden des Protokolls MQTT

Wenn ein Gerät die SDKs von Geräteclients nicht verwenden kann, lässt es sich dennoch mithilfe des Protokolls MQTT mit den öffentlichen Geräteendpunkten verbinden. Das Gerät muss im **CONNECT**-Paket die folgenden Werte verwenden:

- Die **Geräte-ID** als **Client-ID**
- `{iothubhostname}/{device_id}` im Feld **Benutzername**, wobei {iothubhostname} der vollständige CNAME des IoT Hubs (Beispiel: contoso.azure-devices.net ist) ist.
- Ein SAS-Token im Feld **Kennwort**. Das [Format des SAS-Tokens][lnk-iothub-security] entspricht dem beschriebenen Format für die Protokolle HTTP und AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`.

Für die MQTT-Pakete CONNECT und DISCONNECT löst der IoT Hub ein Ereignis im Kanal **Vorgangsüberwachung** aus.

### Senden von Nachrichten an einen IoT Hub

Nachdem Sie eine Verbindung hergestellt haben, kann ein Gerät Nachrichten mithilfe von `devices/{did}/messages/events/` oder `devices/{did}/messages/events/{property_bag}` als **Themenname** an den IoT Hub senden. Das `{property_bag}`-Element ermöglicht dem Gerät das Senden von Nachrichten mit zusätzlichen Eigenschaften in einem URL-codierten Format. Beispiel:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```
 
> [AZURE.NOTE] Dies ist die gleiche Codierung wie bei Abfragezeichenfolgen im HTTP-Protokoll.

Die Geräteclientanwendung kann auch `devices/{did}/messages/events/{property_bag}` als **WILLTOPIC-Namen** verwenden, um festzulegen,dass *WILL-Nachrichten* als Telemetrienachricht weitergeleitet werden.

### Empfangen von Nachrichten

Zum Empfangen von Nachrichten von einem IoT Hub muss ein Gerät ein Abonnement unter Verwendung von `devices/{did}/messages/devicebound/#”` als **Themenfilter** einrichten. IoT Hub liefert Nachrichten mit dem **Themennamen** `devices/{did}/messages/devicebound/` oder `devices/{did}/messages/devicebound/{property_bag}`, wenn Nachrichteneigenschaften vorhanden sind. `{property_bag}` enthält URL-codierte Schlüssel-Wert-Paare von Nachrichteneigenschaften. Nur Anwendungseigenschaften und vom Benutzer festlegbare Systemeigenschaften (z. B. **messageId** oder **correlationId**) sind im Eigenschaftenbehälter enthalten. Systemeigenschaftennamen haben das Präfix **$**, Anwendungseigenschaften verwenden den ursprünglichen Eigenschaftennamen ohne Präfix.

## Nächste Schritte

Weitere Informationen zum Verwenden der SDKs von Geräteclients zum Kommunizieren mit einem IoT Hub finden Sie unter [Erste Schritte mit Azure IoT Hub][lnk-iot-get-stated].

Weitere Informationen zum Protokoll MQTT finden Sie in der [MQTT-Dokumentation][lnk-mqtt-docs].

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-iot-get-stated]: iot-hub-csharp-csharp-getstarted.md
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-iothub-security]: iot-hub-devguide.md#security
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples

<!---HONumber=AcomDC_0218_2016-->