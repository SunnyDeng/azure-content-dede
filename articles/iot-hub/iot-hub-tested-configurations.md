<properties
	pageTitle="Kompatibilität von Betriebssystemplattformen | Microsoft Azure"
	description="Hier finden Sie eine Zusammenfassung zur Kompatibilität des IoT-Geräte-SDKs mit Betriebssystemplattformen."
	services="iot-hub"
	documentationCenter=""
	authors="hegate"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/28/2016"
     ms.author="hegate"/>

# Kompatibilität von Betriebssystemplattformen mit Geräte-SDKs

In diesem Dokument wird die SDK-Kompatibilität mit verschiedenen Betriebssystemplattformen behandelt. Wenn Sie nicht genau wissen, welches Gerät Sie verwenden sollen, ziehen Sie den Abschnitt zur Kompatibilität von [Betriebssystemplattform und Bibliotheken](#os-platforms) in diesem Artikel zurate.

## Microsoft Azure Certified für IoT-Programm

Wenn Sie bereits über ein Gerät verfügen, suchen Sie in der Liste mit den im [Microsoft Azure Certified für IoT-Programm][lnk-certified] enthaltenen Geräten nach gerätespezifischen Informationen zur Kompatibilität. Microsoft Azure Certified for IoT ist das Partnerprogramm, das Microsoft Azure mit dem umfassenderen IoT-Ökosystem verknüpft, um Entwicklern und Architekten die Kompatibilitätsszenarien verständlich zu machen. Es bietet insbesondere eine Liste vertrauenswürdiger Betriebssystem-Geräte-Konfigurationen, damit Sie schnell mit einem IoT-Projekt beginnen können – in der Phase der Machbarkeitsstudie oder in der Pilotphase.

## Betriebssystemplattformen

Die Azure IoT-Bibliotheken wurden auf den folgenden Betriebssystemplattformen getestet:


|Linux-/UNIX-Betriebssystemplattformen | Version|
|:---------------|:------------:|
|Debian Linux| 7\.5|
|Fedora Linux|20|
|Raspbian Linux| 3\.18 |
|Ubuntu Linux| 14\.04 |
|Yocto Linux|2\.1 |

|Windows-Betriebssystemplattformen | Version|
|:---------------|:------------:|
|Windows Desktop| 10 |
|Windows IoT Core| 10 |
|Windows Server| 2012 R2|

|Andere Plattformen | Version|
|:---------------|:------------:|
|mbed | 2\.0 |
|TI-RTOS | 2\.x |



## C-Bibliotheken

Das [Microsoft Azure IoT-Geräte-SDK für C](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md) wurde unter den folgenden Konfigurationen getestet:

|Betriebssystemplattform| Version|Protokolle|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT, AMQP über WebSockets |
|Fedora Linux| 20 | HTTPS, AMQP, MQTT, AMQP über WebSockets |
|mbed OS| 2,0 | HTTPS, AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT, AMQP über WebSockets |
|Windows Desktop| 10 | HTTPS, AMQP, MQTT, AMQP über WebSockets |
|Yocto Linux|2\.1 | HTTPS, AMQP|



## Node.js-Bibliotheken

Das [Microsoft Azure IoT-Geräte-SDK für "Node.js"](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) wurde unter den folgenden Konfigurationen getestet:


|Laufzeit| Version|Protokolle|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS, AMQP, MQTT, AMQP über WebSockets |



## Java-Bibliotheken

Das [Microsoft Azure IoT-Geräte-SDK für Java](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) wurde unter den folgenden Konfigurationen getestet:

|Laufzeit| Version|Protokolle|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.8 | HTTPS, AMQP, MQTT |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP, MQTT|

Das Microsoft Azure IoT-Dienste-SDK für Java wurde unter den folgenden Konfigurationen getestet:

|Laufzeit| Version|Protokolle|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP, MQTT |


## CSharp

Das [Microsoft Azure IoT-Geräte-SDK für .NET](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md) wurde unter den folgenden Konfigurationen getestet:

|Betriebssystemplattform| Version|Protokolle|
|:---------|:----------:|:----------:|
|Windows Desktop| 10 | HTTPS, AMQP, MQTT, AMQP über WebSockets |
|Windows IoT Core|10 | HTTPS |

Verwalteter Agent-Code erfordert Microsoft .NET Framework 4.5


## Nächste Schritte

- Weitere Informationen zum Microsoft Azure Certified für IoT-Programm finden Sie [hier][lnk-certified].
- Erfahren Sie mehr zum Entwickeln von Lösungen mit [für IoT zertifizierten Geräten](http://azure.com/iotdev).


[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-certified]: iot-hub-certified-devices-linux-c.md

<!---HONumber=AcomDC_0323_2016-->