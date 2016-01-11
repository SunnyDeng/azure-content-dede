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
 ms.date="11/05/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## Nächste Schritte

Azure IoT Hub ist ein Azure-Dienst, mit dem umfangreiche Telemetriedaten von Ihren Geräten empfangen und an einen Prozessor für Datenstromereignisse weitergeleitet werden. Sie können IoT Hub zum Implementieren Ihrer eigenen Back-End-Lösung verwenden. IoT Hub kann auch Cloud-zu-Gerät-Befehle an bestimmte Geräte senden. Darüber hinaus umfasst IoT Hub eine Geräteidentitätsregistrierung, mit der Sie Geräte bereitstellen und festlegen können, welche Geräte eine Verbindung mit dem Hub herstellen dürfen. Weitere Informationen finden Sie unter:

- [Was ist IoT Hub?][lnk-iot-hub]
- [Erste Schritte mit IoT Hub][lnk-getstarted]

Mit den IoT-Geräte-SDKs können Sie Clientanwendungen auf einer Vielzahl von Gerätehardwareplattformen und Betriebssystemen implementieren. Die IoT-Geräte-SDKs enthalten Bibliotheken, die das Senden von Telemetriedaten an einen IoT Hub und das Empfangen von Cloud-zu-Gerät-Befehlen ermöglichen. Wenn Sie die SDKs verwenden, können Sie für die Kommunikation mit IoT Hub unter verschiedenen Netzwerkprotokollen auswählen. Erfahren Sie mehr in den [Informationen zu Geräte-SDKs][lnk-device-sdks].

Möglicherweise interessieren Sie sich auch für [Azure IoT Suite][lnk-iot-suite], eine Sammlung vorkonfigurierter Lösungen. IoT Suite ermöglicht den schnellen Einstieg und das Skalieren von IoT-Projekten und eignet sich damit für allgemeine IoT-Szenarien wie Remoteüberwachung, Asset Management und vorbeugende Wartung.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_1223_2015-->