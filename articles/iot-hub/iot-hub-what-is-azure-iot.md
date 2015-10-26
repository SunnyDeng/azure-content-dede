<properties
 pageTitle="Azure-Lösungen für das Internet der Dinge | Microsoft Azure"
 description="Eine Übersicht über IoT in Azure einschließlich einer beispielhaften Lösungsarchitektur und Informationen zur Beziehung zu Azure IoT Hub, Geräte-SDKs und vorkonfigurierten Lösungen."
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
 ms.date="09/29/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## Umfang dieser Dokumentation

Diese Artikel zu Azure und IoT konzentrieren sich auf zwei Ressourcengruppen, die Sie bei der Implementierung eigener IoT-Lösungen auf Grundlage der Microsoft IoT-Plattform unterstützen können.

- Azure IoT Hub
- Azure IoT-Geräte-SDKs

Möglicherweise interessieren Sie sich auch für [Azure IoT Suite][lnk-iot-suite], eine Sammlung vorkonfigurierter Lösungen, die den schnellen Einstieg ermöglichen. Sie lassen sich für IoT-Projekte skalieren und eignen sich für allgemeine IoT-Szenarien wie Remoteüberwachung, Asset Management und vorbeugende Wartung.

### Azure IoT Hub

IoT Hub ist ein Azure-Dienst, mit dem umfangreiche D2C-Daten (Gerät-zu-Cloud) von Ihren Geräten empfangen und an einen Prozessor für Datenstromereignisse weitergeleitet werden. IoT Hub kann unter Verwendung gerätespezifischer Warteschlangen auch Cloud-zu-Gerät-Befehle an bestimmte Geräte senden.

Darüber hinaus umfasst der IoT Hub-Dienst eine Geräteidentitätsregistrierung, mit der Sie Geräte bereitstellen und festlegen können, welche Geräte eine Verbindung mit IoT Hub herstellen dürfen.

### Azure IoT-Geräte-SDKs

Microsoft bietet IoT-Geräte-SDKs, mit denen Sie Clientanwendungen implementieren können, die auf zahlreichen Gerätehardwareplattformen und Betriebssystemen ausgeführt werden können. Die IoT-Geräte-SDKs enthalten Bibliotheken, die das Senden von Gerät-zu-Cloud-Telemetriedaten an IoT Hub und das Empfangen von Cloud-zu-Gerät-Befehlen von IoT Hub ermöglichen. Wenn Sie die SDKs verwenden, können Sie zur Kommunikation mit Azure IoT Hub unter verschiedenen Netzwerkprotokollen auswählen.

## Nächste Schritte

Informationen zu den ersten Schritten mit IoT in Azure finden Sie in den folgenden Ressourcen:

- [Erste Schritte mit IoT Hub][lnk-getstarted]
- [Azure IoT Developer Center][lnk-iotdev]
- [Azure IoT Hub][lnk-iot-hub]
- [Azure IoT Suite][lnk-iot-suite]  


[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=Oct15_HO3-->