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
 ms.date="02/03/2016"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## Nächste Schritte

Azure IoT Hub ist ein Azure-Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Ihrem Anwendungs-Back-End und Millionen von Geräten ermöglicht. Der Dienst ermöglicht dem Anwendungs-Back-End nach Maß das Empfangen von Telemetriedaten von Ihren Geräten, das Weiterleiten dieser Daten an einen Datenstromereignis-Prozessor und auch das Senden von C2D-Befehlen an bestimmte Geräte. Sie können IoT Hub zum Implementieren Ihrer eigenen Back-End-Lösung verwenden. Darüber hinaus umfasst IoT Hub eine Geräte-Identitätsregistrierung, die zum Bereitstellen von Geräten, ihren sicherheitsbezogenen Anmeldeinformationen und ihren Rechten zum Herstellen einer Verbindung mit dem Hub dient. Weitere Informationen finden Sie unter:

- [Was ist IoT Hub?][lnk-iot-hub]
- [Erste Schritte mit IoT Hub][lnk-getstarted]

Mit den IoT-Geräte-SDKs können Sie Clientanwendungen auf einer Vielzahl von Gerätehardwareplattformen und Betriebssystemen implementieren. Die IoT-Geräte-SDKs enthalten Bibliotheken, die das Senden von Telemetriedaten an einen IoT Hub und das Empfangen von Cloud-zu-Gerät-Befehlen ermöglichen. Wenn Sie die SDKs verwenden, können Sie für die Kommunikation mit IoT Hub unter verschiedenen Netzwerkprotokollen auswählen. Erfahren Sie mehr in den [Informationen zu Geräte-SDKs][lnk-device-sdks].

Möglicherweise interessieren Sie sich auch für [Azure IoT Suite][lnk-iot-suite], eine Sammlung vorkonfigurierter Lösungen. IoT Suite ermöglicht den schnellen Einstieg und das Skalieren von IoT-Projekten und eignet sich damit für allgemeine IoT-Szenarien wie Remoteüberwachung, Asset Management und vorbeugende Wartung.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_0211_2016-->