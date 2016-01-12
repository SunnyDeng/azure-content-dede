<properties
   pageTitle="Azure IoT-Protokollgateway | Microsoft Azure"
   description="Beschreibt, wie die Funktionalität und Protokollunterstützung von Azure IoT Hub mithilfe des Azure IoT-Protokollgateways erweitert wird."
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/29/2015"
   ms.author="kdotchko"/>

# Unterstützung zusätzlicher Protokolle für IoT Hub

Azure IoT Hub bietet systemeigene Unterstützung für die Kommunikation über das HTTPS- und das AMQP-Protokoll. In einigen Fällen können Geräte oder Bereichsgateways möglicherweise keines dieser Standardprotokolle verwenden und erfordern eine Protokollanpassung. In solchen Fällen können Sie ein benutzerdefiniertes Gateway verwenden. Ein benutzerdefiniertes Gateway kann die Protokollanpassung für IoT Hub-Endpunkte ermöglichen und für den IoT Hub-Datenverkehr als Brücke fungieren. Ein Azure IoT-Protokollgateway (Internet of Things, Internet der Dinge) ermöglicht die Protokollanpassung für IoT Hub. Darüber hinaus wird damit ein MQTT-Protokolladapter implementiert, der die Kommunikation zwischen einem IoT-Gerät und IoT Hub über das MQTT-Protokoll ermöglicht.

## Azure IoT-Protokollgateway

Das Azure IoT-Protokollgateway ist ein Framework zur Protokollanpassung für die hoch skalierbare, bidirektionale Gerätekommunikation mit IoT Hub. Das Protokollgateway ist eine Passthrough-Komponente, die Geräteverbindungen über ein bestimmtes Protokoll akzeptiert. Es fungiert als Brücke für den Datenverkehr zum IoT Hub über AMQP 1.0. Das IoT-Protokollgateway steht als Open-Source-Softwareprojekt zur Verfügung und ermöglicht eine flexible Unterstützung für eine Vielzahl von Protokollen und Protokollversionen.

Sie können das Protokollgateway in Azure auf hochgradig skalierbare Weise mithilfe von Azure Cloud Services-Workerrollen bereitstellen. Darüber hinaus kann das Protokollgateway in lokalen Umgebungen wie z. B. Bereichsgateways bereitgestellt werden.

Das Azure IoT-Protokollgateway enthält einen MQTT-Adapter, um die Kommunikation mit Geräten über das MQTT v3.1.1-Protokoll zu ermöglichen. Das Protokollgateway und die MQTT-Implementierung werden als Open-Source-Softwareprojekt bereitgestellt und bieten so eine hohe Flexibilität. Auf diese Weise können Sie die Implementierung nach Bedarf anpassen.

Der MQTT-Adapter veranschaulicht außerdem das Programmiermodell zum Erstellen von Protokolladaptern für andere Protokolle. Darüber hinaus unterstützt das Programmiermodell des IoT-Protokollgateways die Integration benutzerdefinierter Komponenten für spezielle Verarbeitungsaufgaben – zum Beispiel benutzerdefinierte Authentifizierung, Nachrichtentransformationen, Komprimierung/Dekomprimierung oder Verschlüsselung/Entschlüsselung des Datenverkehrs zwischen Geräten und IoT Hub.

## Nächste Schritte

Weitere Informationen über das Azure IoT-Protokollgateway sowie dessen Verwendung und Bereitstellung im Rahmen Ihrer IoT-Lösung finden Sie unter:

* [Repository für das Azure IoT-Protokollgateway auf GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Entwicklungsleitfaden für das Azure IoT-Protokollgateway](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

<!---HONumber=AcomDC_1223_2015-->