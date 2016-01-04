<properties
	pageTitle="Kompatibilität von Betriebssystemplattformen und Hardware | Microsoft Azure"
	description="Hier finden Sie eine Zusammenfassung zur Kompatibilität des IoT-Geräte-SDKs mit Betriebssystemplattformen und Gerätehardware."
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
     ms.date="10/09/2015"
     ms.author="hegate"/>

# Betriebssystemplattformen und Hardwarekompatibilität mit Geräte-SDKs

In diesem Dokument wird die SDK-Kompatibilität mit verschiedenen Betriebssystemplattformen sowie mit den speziellen Gerätekonfigurationen im [Microsoft Azure Certified for IoT-Programm](#microsoft-azure-certified-for-iot) beschrieben. Wenn Sie bereits über ein Gerät verfügen, suchen Sie in der Liste der im Programm enthaltenen Geräte nach gerätespezifischen Informationen zur Kompatibilität. Wenn Sie nicht genau wissen, welches Gerät Sie verwenden sollen, ziehen Sie den Abschnitt zur Kompatibilität von [Betriebssystemplattform und Bibliotheken](#os-platforms) zurate.


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

Das [Microsoft Azure IoT-Geräte-SDK für C](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md) wurde unter den folgenden Konfigurationen getestet:

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

Das [Microsoft Azure IoT-Geräte-SDK für "Node.js"](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) wurde unter den folgenden Konfigurationen getestet:


|Laufzeit| Version|Protokolle|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS|



## Java-Bibliotheken

Das [Microsoft Azure IoT-Geräte-SDK für Java](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) wurde unter den folgenden Konfigurationen getestet:

|Laufzeit| Version|Protokolle|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.7 | HTTPS, AMQP |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP|

Das Microsoft Azure IoT-Dienste-SDK für Java wurde unter den folgenden Konfigurationen getestet:

|Laufzeit| Version|Protokolle|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP |


## CSharp

Das [Microsoft Azure IoT-Geräte-SDK für .NET](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md) wurde unter den folgenden Konfigurationen getestet:

|Betriebssystemplattform| Version|Protokolle|
|:---------|:----------:|:----------:|
|Windows Desktop| 7, 8, 10 | HTTPS, AMPQ|
|Windows IoT Core|10 | HTTPS|

Verwalteter Agent-Code erfordert Microsoft .NET Framework 4.5


## Microsoft Azure Certified for IoT

**Microsoft Azure Certified for IoT** ist das Partnerprogramm, das Microsoft Azure mit dem umfassenderen IoT-Ökosystem verknüpft, um Entwicklern und Architekten die Kompatibilitätsszenarien verständlich zu machen. Es bietet insbesondere eine Liste vertrauenswürdiger Betriebssystem-Geräte-Konfigurationen, damit Sie schnell mit einem IoT-Projekt beginnen können – in der Phase der Machbarkeitsstudie oder in der Pilotphase. Mit zertifizierten Kombinationen von Gerät und Betriebssystem kann Ihr IoT-Projekt schnell gestartet werden, wobei sich mit weniger Aufwand an Arbeit und Anpassung sicherstellen lässt, dass Geräte mit Azure IoT Suite und Azure IoT Hub kompatibel sind.

## Für IoT zertifizierte Geräte

Die Kompatibilität der **für IoT zertifizierten Geräte** mit den Azure IoT-SDKs wurde getestet, und diese Geräte sind für Ihre IoT-Anwendung einsatzbereit. Wir bestimmen die Kompatibilität insbesondere anhand der Betriebssystemplattform und Codesprache.

#### Geräteliste

Die Kompatibilität jedes Geräts mit unserem SDK unter dem Betriebssystem und in der Sprache, die vom Gerätehersteller gewählt wurden, wurde zertifiziert. Beispielsweise ist BeagleBone Black mit Debian und den Sprachen C, JavaScript und Java kompatibel. Dies bedeutet, dass Entwickler Anwendungen in jeder dieser Kombinationen von Sprache und Betriebssystem auf den jeweiligen Geräten erstellen können.

|Gerät| Getestetes Betriebssystem |Sprache|
|:---------|:----------|:----------|
|[ADLINK MXE-202i](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1505&seq=&id=&sid=&category=Fanless-Embedded-Computer_Integrated-Embedded-Computer&utm_source=) |Wind River | JavaScript|
|[Ankaa](http://www.e-consystems.com/iMX6-development-board.asp) |Ubuntu | C|
|[Apalis iMX6](https://www.toradex.com/computer-on-modules/apalis-arm-family/freescale-imx-6) |Linux Angstrom(Yocto) | JavaScript, Java|
|[Apalis T30](https://www.toradex.com/computer-on-modules/apalis-arm-family/nvidia-tegra-3) |Linux Angstrom(Yocto) | JavaScript, Java|
|[Arrow DragonBoard 410c](http://partners.arrow.com/campaigns-na/qualcomm/dragonboard-410c/) |Windows 10 IoT Core | C#|
|[ARTIK](http://developer.samsung.com/artik) |Fedora | C|
|[BeagleBone Black](http://beagleboard.org/black) | Debian | C, JavaScript, Java|
|[BeagleBone Green](http://beagleboard.org/green) |Debian | C, JavaScript, Java|
|[Toradex Colibri iMX6](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-imx6) |Linux Angstrom(Yocto) | JavaScript, Java|
|[Toradex Colibri T20](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-2) |Linux Angstrom(Yocto) | Java|
|[Toradex Colibri T30](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-3) |Windows 10 IoT Core | C#|
|[Toradex Colibri VF61](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-vybrid-vf6xx) |Linux Angstrom(Yocto) | JavaScript, Java|
|[Freescale FRDM K64](http://www.freescale.com/products/arm-processors/kinetis-cortex-m/k-series/k6x-ethernet-mcus/freescale-freedom-development-platform-for-kinetis-k64-k63-and-k24-mcus:FRDM-K64F) |mbed 2.0 | C|
|[Intel Edison](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html) |Yocto | C, JavaScript|
|[LogicMachine-Serie](http://openrb.com/products/) |Benutzerdefiniertes Linux | C|
|[Minnowboard Max](http://www.minnowboard.org/meet-minnowboard-max/) |Windows 7, 8, 10 | C#|
|[NEXCOM NISE 50C](http://www.nexcom.com/Products/industrial-computing-solutions/industrial-fanless-computer/atom-compact/fanless-computer-nise-50c) |Windows 10 IoT Core | C#|
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Raspbian | C, JavaScript, Java |
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Windows 10 IoT Core| C, JavaScript, C#|
|[STM32 Nucleo](http://www.st.com/stm32nucleo) |STM32Cube | C|
|[TI CC3200](http://www.ti.com/product/cc3200) |TI-RTOS 2.x | C|

[Verbinden Sie Ihr Gerät mit Azure IoT Hub](https://azure.microsoft.com/develop/iot/get-started/), oder besuchen Sie unser GitHub-[Repository](https://github.com/Azure/azure-iot-sdks) (in englischer Sprache), und durchsuchen Sie die Gerätedokumentation nach Sprache.

## Nächste Schritte

Erfahren Sie mehr zum Entwickeln von Lösungen mit [für IoT zertifizierten Geräten](http://azure.com/iotdev).

<!---HONumber=AcomDC_1217_2015-->