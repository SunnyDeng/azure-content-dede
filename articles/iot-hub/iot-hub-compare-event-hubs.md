<properties
 pageTitle="Vergleich zwischen von Azure IoT Hub und Event Hubs | Microsoft Azure"
 description="Hier finden Sie einen Vergleich der Azure IoT Hub- und Azure Event Hubs-Dienste, wobei besonders auf Unterschiede bei den Funktionen und Anwendungsbeispiele eingegangen wird."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="01/20/2016"
 ms.author="elioda"/>

# Vergleich von IoT Hub und Event Hubs

Eine der wichtigsten Anwendungsmöglichkeiten für Azure IoT Hub ist die Erfassung der Telemetriedaten von Geräten. Aus diesem Grund wird IoT Hub häufig mit [Azure Event Hubs][] verglichen. Ebenso wie IoT Hub ist Event Hubs ein Dienst zur Ereignisverarbeitung, der riesige Mengen von Ereignis- und Telemetriedaten erfassen kann und gleichzeitig eine niedrige Latenz und hohe Zuverlässigkeit bietet.

Allerdings bestehen bei den Diensten viele Unterschiede, die in der folgenden Tabelle beschrieben werden.

| Bereich | IoT Hub | Event Hubs |
| ---- | ------- | ---------- |
| Kommunikationsmuster | Ermöglicht Device-to-Cloud (D2C)-Ereigniseingang und Cloud-to-Device (C2D)-Messaging. | Ermöglicht nur Ereigniseingang (meist für D2C-Szenarien). |
| Sicherheit | Ermöglicht Identität pro Gerät und widerrufbare Zugriffssteuerung. Siehe [Abschnitt über Sicherheit im IoT Hub-Entwicklerleitfaden]. | Ermöglicht Event Hub-weite [SAS-Richtlinien][Event Hub - security] mit begrenzter Widerrufsunterstützung über [Richtlinien des Herausgebers][Event Hub publisher policies]. Im Kontext von IoT-Lösungen ist es häufig erforderlich, eine benutzerdefinierte Lösung zur Unterstützung von Anmeldeinformationen pro Gerät und Maßnahmen zum Schutz vor Spoofing zu implementieren. |
| Skalieren | Ist für die Unterstützung von Millionen von gleichzeitig verbundenen Geräten optimiert. | Kann eine eingeschränktere Anzahl von gleichzeitigen Verbindungen unterstützen – bis zu 5.000 AMQP-Verbindungen gemäß [Azure Service Bus-Kontingenten][]. Jedoch können Sie mit Event Hubs die Partition für jede gesendete Nachricht angeben. |
| Geräte-SDKs | Stellt [Geräte-SDKs][Azure IoT Hub SDKs] für viele verschiedene Plattformen und Sprachen bereit. | Wird unter .NET und C unterstützt und verfügt außerdem über AMQP- und HTTP-Sendeschnittstellen. |

Zusammengefasst bedeutet dies: Auch wenn nur der D2C-Telemetrieeingang als Anwendungsfall vorhanden ist, bietet IoT Hub einen Dienst, der speziell für die Verbindung von IoT-Geräten ausgelegt ist. Außerdem werden die Wertbeiträge für diese Szenarien mit IoT-spezifischen Features weiter ausgebaut. Event Hubs sind für den Ereigniseingang in großem Umfang ausgelegt, sowohl im Kontext von Szenarien im Rechenzentrum als auch außerhalb davon.

Es ist nicht ungewöhnlich, dass IoT Hub und Event Hubs in derselben Lösung verwendet werden. Dabei wird IoT Hub für die D2C-Kommunikation eingesetzt und mit Event Hubs der nachgelagerte Ereigniseingang in Echtzeitverarbeitungsmodule geregelt.

## Nächste Schritte

Folgen Sie diesen Links, um mehr über Azure IoT Hub zu erfahren:

- [Erste Schritte mit Azure IoT Hub (Tutorial)][lnk-get-started]
- [Was ist Azure IoT Hub?][]

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[Abschnitt über Sicherheit im IoT Hub-Entwicklerleitfaden]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Azure Service Bus-Kontingenten]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Was ist Azure IoT Hub?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_0121_2016-->