<properties
	pageTitle="Kompatibilität von Azure IoT-Betriebssystemplattformen und Hardware | Microsoft Azure"
	description="Kompatibilität von Betriebssystemplattformen und Hardware"
	services="iot-hub"
	documentationCenter="na"
	authors="hegate"
	manager="jamesosb"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="hegate"/>

# Kompatibilität von Betriebssystemplattformen und Hardware

In diesem Dokument wird die SDK-Kompatibilität mit verschiedenen Betriebssystemplattformen sowie mit den speziellen Gerätekonfigurationen im [Microsoft Azure Certified for IoT-Programm](#certified) beschrieben. Wenn Sie bereits über ein Gerät verfügen, suchen Sie in der Liste der im Programm enthaltenen Geräte nach gerätespezifischen Informationen zur Kompatibilität. Wenn Sie nicht genau wissen, welches Gerät Sie verwenden sollen, ziehen Sie den Abschnitt zur Kompatibilität von [Betriebssystemplattform und Bibliotheken](#platforms) zurate.


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
|Windows Desktop| 7, 8, 10 |
|Windows IoT Core| 10 |
|Windows Server| 2012 R2|

|Andere Plattformen | Version|
|:---------------|:------------:|
|mbed | 2,0 |
|TI-RTOS | 2\.x |



## C-Bibliotheken

Das [Microsoft Azure IoT-Geräte-SDK für C](c/device/readme.md) wurde unter den folgenden Konfigurationen getestet:

|Betriebssystemplattform| Version|Protokolle|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT |
|Fedora Linux| 20 | HTTPS, AMQP, MQTT |
|mbed OS| 2,0 | HTTPS, AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT |
|Windows Desktop| 7, 8, 10 | HTTPS, AMPQ, MQTT |
|Yocto Linux|2\.1 | HTTPS, AMQP|



## Node.js-Bibliotheken
Das [Microsoft Azure IoT-Geräte-SDK für "Node.js"](node/device/readme.md) wurde unter den folgenden Konfigurationen getestet:


|Laufzeit| Version|Protokolle|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS|



## Java-Bibliotheken
Das [Microsoft Azure IoT-Geräte-SDK für Java](java/device/readme.md) wurde unter den folgenden Konfigurationen getestet:

|Laufzeit| Version|Protokolle|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.7 | HTTPS, AMQP |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP|

Das [Microsoft Azure IoT-Dienste-SDK für Java](java/service/readme.md) wurde unter den folgenden Konfigurationen getestet:

|Laufzeit| Version|Protokolle|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP |


## CSharp
Das [Microsoft Azure IoT-Geräte-SDK für .NET](csharp/readme.md) wurde unter den folgenden Konfigurationen getestet:

|Betriebssystemplattform| Version|Protokolle|
|:---------|:----------:|:----------:|
|Windows Desktop| 7, 8, 10 | HTTPS, AMPQ|
|Windows IoT Core|10 | HTTPS|

Verwalteter Agent-Code erfordert Microsoft .NET Framework 4.5


## Microsoft Azure Certified for IoT

Microsoft Azure Certified for IoT ist das Partnerprogramm, das Microsoft Azure mit dem umfassenderen IoT-Ökosystem verknüpft, um Entwicklern und Architekten die Kompatibilitätsszenarien verständlich zu machen. Es bietet insbesondere eine Liste vertrauenswürdiger Betriebssystem-Geräte-Konfigurationen, damit Sie schnell mit einem IoT-Projekt beginnen können – in der Phase der Machbarkeitsstudie oder in der Pilotphase. Mit zertifizierten Kombinationen von Gerät und Betriebssystem kann Ihr IoT-Projekt schnell gestartet werden, wobei sich mit weniger Aufwand an Arbeit und Anpassung sicherstellen lässt, dass Geräte mit Azure IoT Suite und Azure IoT Hub kompatibel sind.


## Für IoT zertifizierte Geräte

Die Kompatibilität der für IoT zertifizierten Geräte mit den Azure IoT-SDKs wurde getestet, und diese Geräte sind für Ihre IoT-Anwendung einsatzbereit. Wir bestimmen die Kompatibilität insbesondere anhand der Betriebssystemplattform und Codesprache.

#### Geräteliste

 Die Kompatibilität jedes Geräts mit unserem SDK unter dem Betriebssystem und in der Sprache, die vom Gerätehersteller gewählt wurden, wurde zertifiziert. Beispielsweise ist BeagleBone Black mit Debian und den Sprachen C, JavaScript und Java kompatibel. Dies bedeutet, dass Entwickler Anwendungen in jeder dieser Kombinationen von Sprache und Betriebssystem auf den jeweiligen Geräten erstellen können.

## Nächste Schritte

Weitere Informationen zum Entwickeln von Lösungen mit für IoT zertifizierten Geräten finden Sie [hier](http://azure.com/iotdev).

|Gerät| Getestetes Betriebssystem |Sprache|
|:---------|:----------|:----------|
|Raspberry Pi 2| Raspbian | C, JavaScript, Java |
|Raspberry Pi 2| Windows 10 IoT Core| C, JavaScript, C#|
|BeagleBone Black| Debian |C, JavaScript, Java|
|BeagleBone Green|Debian |C, JavaScript, Java|
|TI CC3200 | TI-RTOS 2.x|C|
|Intel Edison |Yocto |C, JavaScript|
|Minnowboard Max |Windows 7, 8, 10 |C#|
|Arrow DragonBoard 410c |Windows 10 IoT Core | C#|
|Freescale FRDM K64 |mbed 2.0 | C|

<!---HONumber=Oct15_HO2-->