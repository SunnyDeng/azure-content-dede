<properties
 pageTitle="Skalieren von Azure IoT Hub | Microsoft Azure"
 description="Beschreibt das Skalieren von Azure IoT Hub."
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
 ms.date="10/02/2015"
 ms.author="elioda"/>

# Skalieren von IoT Hub

IoT Hub kann bis zu einer Million gleichzeitig verbundene Geräte unterstützen, indem die Anzahl von IoT Hub S1- oder S2-Einheiten auf 2.000 erhöht wird. Weitere Informationen finden Sie unter [IoT Hub – Preise][lnk-pricing].

Jede IoT Hub-Einheit lässt eine bestimmte Anzahl von Geräteidentitäten in der Registrierung zu, die alle gleichzeitig verbunden sein können. Außerdem ist eine bestimmte Anzahl von täglichen Nachrichten zulässig.

Um Ihre Lösung richtig skalieren zu können, sollten Sie sich nach dem jeweiligen IoT Hub-Anwendungsfall richten. Achten Sie besonders auf den erforderlichen Spitzendurchsatz für die folgenden Kategorien von Vorgängen:

* Device-to-Cloud (D2C)-Nachrichten
* Cloud-to-Device (C2D)-Nachrichten
* Identitätsregistrierungsvorgänge

Sehen Sie sich zusätzlich zu diesen Durchsatzinformationen auch [IoT Hub-Kontingente und -Drosselungen][] an, und entwerfen Sie Ihre Lösung entsprechend.

## D2C- und C2D-Nachrichtendurchsatz

Die beste Methode zum Skalieren einer IoT Hub-Lösung ist die Auswertung des Datenverkehrs pro Gerät.

D2C-Nachrichten basieren auf diesen Richtlinien für den anhaltenden Durchsatz:

| Tarif | Anhaltender Durchsatz | Anhaltende Senderate |
| ---- | -------------------- | ------------------- |
| S1 | Bis zu 8 KBit/Stunde pro Gerät | Durchschnitt von 4 Nachrichten/Stunde pro Gerät |
| S2 | Bis zu 4 KBit/Min. pro Gerät | Durchschnitt von 2 Nachrichten/Min. pro Gerät |

Beim Empfangen von D2C-Nachrichten kann das Anwendungs-Back-End den folgenden maximalen Durchsatz erwarten (über alle Reader).

| Preisstufe | Anhaltender Durchsatz |
| ---- | -------------------- |
| S1 | Bis zu 120 KBit/Min. pro Einheit, Minimum von 2 MBit/s |
| S2 | Bis zu 4 MBit/Min. pro Einheit, Minimum von 2 MBit/s |

Die Leistung wird bei C2D-Nachrichten pro Gerät skaliert, und jedes Gerät empfängt bis zu 5 Nachrichten pro Minute.

## Durchsatz von Identitätsregistrierungsvorgängen

IoT Hub-Identitätsregistrierungsvorgänge sollten keine Laufzeitvorgänge sein, da sie sich größtenteils auf die Gerätebereitstellung beziehen.

Genaue Zahlen zur Burst-Leistung finden Sie unter [IoT Hub-Kontingente und -Drosselungen][].

## Sharding (Horizontales Partitionieren)

Eine einzelne IoT Hub-Einheit kann zwar auf Millionen von Geräten skaliert werden, aber es kann sein, dass Ihre Lösung bestimmte Leistungsmerkmale benötigt, die von einer einzelnen IoT Hub-Einheit nicht gewährleistet werden können. In diesem Fall wird empfohlen, Ihre Geräte auf mehrere IoT Hubs zu partitionieren, um Datenverkehrsbursts zu glätten und den erforderlichen Durchsatz oder die erforderlichen Vorgangsraten zu erzielen.

## Nächste Schritte

Folgen Sie diesen Links, um mehr über Azure IoT Hub zu erfahren:

- [Erste Schritte mit IoT Hubs (Tutorial)][lnk-get-started]
- [Was ist Azure IoT Hub?][]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub-Kontingente und -Drosselungen]: iot-hub-devguide.md#throttling

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Was ist Azure IoT Hub?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Nov15_HO3-->