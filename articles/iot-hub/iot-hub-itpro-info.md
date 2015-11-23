<properties
 pageTitle="Informationen zu Azure IoT Hub für IT-Experten | Microsoft Azure"
 description="Informationen, die IT-Experten bei der Verwendung von Azure IoT Hub unterstützen, beispielsweise zu Portanforderungen und zum Sicherheitshintergrund."
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
 ms.date="09/28/2015"
 ms.author="dobett"/>

# Konfigurieren und Verwalten des Zugriffs auf IoT Hub

Die Informationen in diesem Artikel sollen IT-Experten beim Konfigurieren einer Umgebung unterstützen, in der IoT-Geräte über eine Netzwerkinfrastruktur mit IoT Hub kommunizieren.

## Netzwerkverbindung

Geräte kommunizieren in Azure über das AMQP- oder HTTPS-Protokoll mit IoT Hub. In der Regel richtet sich die Wahl des Protokolls nach den spezifischen Anforderungen der Lösung. Die folgende Tabelle enthält die ausgehenden Ports, die geöffnet sein müssen, damit ein Gerät ein bestimmtes Protokoll verwenden kann:

| Protokoll | Port(s) |
| -------- | ------- |
| HTTPS | 443 |
| AMQP | 5671 |

Nachdem Sie einen IoT Hub in einer Azure-Region erstellt haben, behält er seine IP-Adresse für die gesamte Lebensdauer bei. Wenn der IoT Hub in einem Notfallwiederherstellungsszenario von Microsoft jedoch zu einer anderen Skalierungseinheit verschoben wird, wird ihm eine neue IP-Adresse zugewiesen.

## IoT Hub und Sicherheit

Nur Geräte, die bei einem IoT Hub registriert sind, dürfen mit diesem kommunizieren. Einem registrierten Gerät muss die *DeviceConnect*-Berechtigung gewährt werden. Ein Gerät identifiziert sich durch ein Token, das die eindeutige Geräte-ID in jeder ausgeführten Anforderung kapselt. Der Hub überprüft die Gültigkeit des Tokens und stellt sicher, dass das Gerät nicht gesperrt ist (die *DeviceConnect*-Berechtigung widerrufen wurde).

Der Zugriff auf andere Verwaltungsendpunkte in einem IoT Hub wird auch über einen Berechtigungssatz gesteuert: *iothubowner*, *service*, *registryRead* und *registryReadWrite*. Jede Clientverwaltungsanwendung, die eine Verbindung mit einem IoT Hub herstellt, muss ein Token mit den entsprechenden Berechtigungen enthalten.

## Nächste Schritte

Dieser Artikel enthält spezifische Informationen für IT-Experten und Entwickler, die ihre Entwicklungs- und Testumgebung konfigurieren. Folgen Sie diesen Links, um mehr über den Azure IoT Hub-Dienst zu erfahren:

- [Was ist Azure IoT Hub?][lnk-iothub]
- Der Abschnitt ["Sicherheit" im Entwicklungsleitfaden für Azure IoT Hub][lnk-devguide] bietet zusätzliche Informationen zu den Token und dem Berechtigungssystem in IoT Hub.
- Unter [Verwalten von IoT Hub über das Azure-Vorschauportal][lnk-manage-portal] wird beschrieben, wie IoT Hub mithilfe des Azure-Vorschauportals verwaltet wird.

[lnk-iothub]: iot-hub-what-is-iot-hub.md
[lnk-devguide]: iot-hub-devguide.md#security
[lnk-manage-portal]: iot-hub-manage-through-portal.md

<!---HONumber=Nov15_HO3-->