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

IoT Hub bietet systemeigene Unterstützung für die Kommunikation über das HTTPS- und AMQP-Protokoll. Zeitweise sind Geräte oder Feldgateways möglicherweise nicht in der Lage, eines dieser Standardprotokolle zu verwenden und erfordern eine Protokollanpassung. In solchen Fällen kann ein benutzerdefiniertes Gateway die Protokollanpassung für IoT Hub-Endpunkte ermöglichen und für den IoT Hub-Datenverkehr als Brücke fungieren. Das Azure IoT-Protokollgateway ermöglicht die Protokollanpassung für IoT Hub und implementiert einen MQTT-Protokolladapter, um die Kommunikation zwischen IoT-Geräten und IoT Hub über das MQTT-Protokoll zu unterstützen.

## Azure IoT-Protokollgateway

Das Azure IoT-Protokollgateway ist ein Framework zur Protokollanpassung für die hoch skalierbare, bidirektionale Gerätekommunikation mit IoT Hub. Das Protokollgateway ist eine Pass-Through-Komponente, die Geräteverbindungen über ein bestimmtes Protokoll akzeptiert und für den Datenverkehr mit IoT Hub über AMQP 1.0 als Brücke fungiert. Das IoT-Protokollgateway ist als OSS (Open Source Software)-Projekt verfügbar, das durch die Unterstützung verschiedener Protokolle und Protokollversionen höhere Flexibilität bietet.

Das Protokollgateway kann in Azure auf hochgradig skalierbare Weise mit Cloud Services-Workerrollen bereitgestellt werden. Darüber hinaus kann das Protokollgateway in lokalen Umgebungen wie Feldgateways bereitgestellt werden.

Das Azure IoT-Protokollgateway enthält einen MQTT-Adapter, um die Kommunikation mit Geräten über das MQTT v3.1.1-Protokoll zu ermöglichen. Das Protokollgateway und die MQTT-Implementierung werden als OSS-Projekt bereitgestellt und ermöglichen bei Bedarf die flexible Anpassung der Implementierung.

Der MQTT-Adapter veranschaulicht außerdem das Programmiermodell zum Erstellen von Protokolladaptern für andere Protokolle. Darüber hinaus unterstützt das Programmiermodell des IoT-Protokollgateways die Integration benutzerdefinierter Komponenten für spezialisierte Verarbeitungsaufgaben, wie benutzerdefinierte Authentifizierung, Nachrichtentransformationen, Komprimierung/Dekomprimierung oder Verschlüsselung/Entschlüsselung des Datenverkehrs zwischen Geräten und IoT Hub.

## Nächste Schritte

Weitere Informationen über das Azure IoT-Protokollgateway und zu dessen Verwendung und Bereitstellung als Teil Ihrer IoT-Lösung finden Sie unter:

* [Repository für das Azure IoT-Protokollgateway auf GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Entwicklungsleitfaden für das Azure IoT-Protokollgateway](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

<!---HONumber=Nov15_HO1-->