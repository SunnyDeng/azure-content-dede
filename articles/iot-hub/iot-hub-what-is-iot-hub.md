<properties
 pageTitle="Was ist Azure IoT Hub? | Microsoft Azure"
 description="Enthält eine Übersicht über den Azure IoT Hub-Dienst, z. B. Gerätekonnektivität, Kommunikationsmuster und dienstgestütztes Kommunikationsmuster."
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

# Was ist Azure IoT Hub?

 Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von IoT-Geräten und einem Anwendungs-Back-End ermöglicht. Azure IoT Hub bietet zuverlässiges D2C- und C2D-Messaging mit Hyper-Skalierung, ermöglicht die sichere Kommunikation durch gerätespezifische Sicherheitsanmeldeinformationen und Zugriffssteuerung und umfasst Gerätebibliotheken für die am häufigsten verwendeten Sprachen und Plattformen.

![IoT Hub als Cloudgateway?][img-architecture]

## IoT-Gerätekonnektivität

Eines der größten Probleme im Zusammenhang mit IoT-Projekten ist die sichere und zuverlässige Verbindung von Geräten mit dem Back-End der Anwendung. IoT-Geräte weisen gegenüber anderen Clients, z. B. Browsern, anderen Servern und mobilen Apps, häufig andere Merkmale auf:

-   Es handelt sich häufig um eingebettete Systeme ohne menschlichen Bediener.

-   Sie können sich an Remotestandorten befinden, an denen der physische Zugriff sehr teuer ist.

-   Die Verbindung mit dem Anwendungs-Back-End ist unter Umständen der einzige Weg zum Zugreifen auf das Gerät.

-   Sie verfügen ggf. über eingeschränkte Stromversorgungs- und Verarbeitungsressourcen.

-   Die Netzwerkkonnektivität für das Gerät kann beispielsweise nicht durchgängig, kostenintensiv oder nur selten verfügbar sein.

-   Sie müssen unter Umständen proprietäre, benutzerdefinierte oder branchenspezifische Anwendungsprotokolle verwenden.

-   Es sind viele gängige Hardware- und Softwareplattformen zum Erstellen von Geräten erhältlich.

Zusätzlich zu den obigen Anforderungen muss jede IoT-Lösung auch Skalierbarkeit, Sicherheit und Zuverlässigkeit bieten. All diese Aspekte werden zu einer Gruppe von Verbindungsanforderungen zusammengefasst, die schwierig und zeitaufwändig zu implementieren sind, wenn herkömmliche Technologie verwendet wird, z. B. Webcontainer und Nachrichtenbroker.

## Gründe für die Verwendung von Azure IoT Hub

Mit Azure IoT Hub werden die Verbindungsanforderungen für Geräte wie folgt erfüllt:

-   **Authentifizierung pro Gerät und sichere Verbindung:** Jedes Gerät kann seinen eigenen Sicherheitsschlüssel verwenden, um eine Verbindung mit IoT Hub herzustellen. Das Anwendungs-Back-End kann dann jedes Gerät einzeln einer Whitelist oder Blacklist hinzufügen, um die vollständige Kontrolle über den Gerätezugriff zu haben.

-   **Umfassender Satz von Gerätebibliotheken:** Azure IoT-Geräte-SDKs sind für die unterschiedlichsten Sprachen und Plattformen verfügbar und werden dafür unterstützt: C für viele Linux-Distributionen, Windows und RTOS. Azure IoT-Geräte-SDKs unterstützen auch verwaltete Sprachen wie C#, Java und JavaScript.

-   **IoT-Protokolle und -Erweiterbarkeit:** Falls Ihre Lösung die Gerätebibliotheken nicht nutzen kann, macht IoT Hub ein öffentliches Protokoll verfügbar, mit dem Geräte die HTTP 1.1- und AMQP 1.0-Protokolle auf systemeigene Weise nutzen können. Sie können Azure IoT Hub auch erweitern, um Unterstützung für MQTT v3.1.1 mit der Open Source-Komponente "Azure IoT-Protokollgateway" bereitzustellen. Sie können das Azure IoT-Protokollgateway in der Cloud oder lokal ausführen und erweitern, um benutzerdefinierte Protokolle zu unterstützen.

-   **Skalierung:** Azure IoT Hub kann auf Millionen von gleichzeitig verbundenen Geräten und Millionen von Ereignissen pro Sekunde skaliert werden.

Zusätzlich zu diesen Vorteilen, die für viele Kommunikationsmuster generisch sind, können Sie mit IoT Hub derzeit die folgenden Kommunikationsmuster implementieren:

-   **Ereignisbasierte Device-to-Cloud (D2C)-Erfassung:** Geräte können pro Sekunde zuverlässig Millionen von Ereignissen für die Verarbeitung durch Ihr Hot-Path-Ereignisverarbeitungsmodul oder für die Speicherung zur Cold-Path-Analyse senden. IoT Hub behält die Ereignisdaten bis zu sieben Tage bei, um eine zuverlässige Verarbeitung zu garantieren und Auslastungsspitzen auszugleichen.

-   **Zuverlässiges Cloud-to-Device (C2D)-Messaging (bzw. *Befehle*):** Das Anwendungs-Back-End kann zuverlässige Nachrichten (mit Garantie für eine mindestens einmalige Zustellung) an einzelne Geräte senden. Jede Nachricht verfügt über eine individuelle Einstellung für die Lebensdauer, und das Back-End kann sowohl Zustellungs- als auch Ablaufnachweise anfordern, um die vollständige Transparenz des Lebenszyklus einer C2D-Nachricht sicherzustellen.

Zusätzlich zu diesen Kommunikationsmustern können Sie andere gängige Muster implementieren, z. B. Dateiupload und -download, indem Sie die speziellen IoT-Features in IoT Hub nutzen. Beispiele hierfür sind die einheitliche Identitätsverwaltung für Geräte, Verbindungsüberwachung und Skalierung.

## Dienstgestütztes Kommunikationsmuster

Azure IoT Hub vermittelt die Interaktionen zwischen Ihren Geräten und Ihrem Anwendungs-Back-End in einer Implementierung des Musters für die [dienstgestützte Kommunikation][lnk-service-assisted-pattern]. Das Ziel der dienstgestützten Kommunikation ist die Einrichtung von vertrauenswürdigen, bidirektionalen Kommunikationspfaden zwischen Steuersystemen (z. B. IoT Hub) und speziellen Geräten, die im nicht vertrauenswürdigen physischen Raum bereitgestellt werden. Daher gelten für das Muster die folgenden Grundsätze:

- Die Sicherheit hat Vorrang vor allen anderen Funktionen.
- Geräte akzeptieren keine unerwünschten Netzwerkinformationen. Alle Verbindungen und Routen werden von einem Gerät nur in ausgehender Richtung hergestellt. Damit ein Gerät einen Befehl vom Back-End erhalten kann, muss das Gerät regelmäßig eine Verbindung initiieren, um eine Prüfung auf ausstehende Befehle durchzuführen, die verarbeitet werden müssen.
- Geräte stellen normalerweise nur Verbindungen mit bekannten Peerdiensten her bzw. richten Routen dafür ein, z. B. mit einer IoT Hub-Dienstinstanz.
- Der Kommunikationspfad zwischen Gerät und Dienst oder Gerät und Gateway wird auf Anwendungsprotokollebene geschützt.
- Die Autorisierung und Authentifizierung auf Systemebene muss auf gerätebezogenen Identitäten basieren, und Anmeldeinformationen und Berechtigungen für den Zugriff müssen nahezu sofort widerrufbar sein.
- Die bidirektionale Kommunikation für Geräte, die aufgrund von Stromversorgungs- oder Konnektivitätsaspekten nur sporadisch verbunden sind, kann über Haltebefehle und Benachrichtigungen für die Geräte durchgeführt werden, die nach dem Herstellen der Verbindung abgerufen werden. IoT Hub verfügt über spezielle Gerätewarteschlangen für die Befehle, die an Geräte gesendet werden.
- Die Nutzlastdaten der Anwendung können für den geschützten Transit durch Gateways zu einem bestimmten Dienst separat geschützt werden.

Das dienstgestützte Kommunikationsmuster wurde in großem Umfang erfolgreich in der Mobilgerätebranche eingesetzt, um Pushbenachrichtigungsdienste wie [Windows-Pushbenachrichtigungsdienst](https://msdn.microsoft.com/library/windows/apps/mt187203.aspx), [Google Cloud Messaging](https://developers.google.com/cloud-messaging/) und [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?linkid=272584&clcid=0x409) zu implementieren.

## Nächste Schritte

Weitere Informationen zu Azure IoT Hub finden Sie unter den folgenden Links:

* [Entwicklungsleitfaden für IoT Hub]
* [Anleitungen zu IoT Hub]
* [Unterstützte Geräteplattformen und Sprachen]
* [Azure IoT Developer Center]
* [Erste Schritte mit IoT Hub]

[IoT Hub Overview]: iot-hub-what-is-iot-hub.md
[Anleitungen zu IoT Hub]: iot-hub-guidance.md
[Entwicklungsleitfaden für IoT Hub]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Erste Schritte mit IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Unterstützte Geräteplattformen und Sprachen]: iot-hub-sdks-summary.md#os-platforms-and-hardware-compatibility
[Azure IoT Developer Center]: http://www.azure.com/iotdev

[img-why-use]: media/iot-hub-what-is-iot-hub/image1.png
[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Dienstgestützte Kommunikation, Blogbeitrag von Clemens Vasters"

<!---HONumber=Oct15_HO2-->