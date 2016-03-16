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
     ms.date="02/28/2016"
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
|mbed | 2\.0 |
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
|Windows Desktop| 7, 8, 10 | HTTPS, AMQP, MQTT |
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
|Windows Desktop| 7, 8, 10 | HTTPS, AMQP|
|Windows IoT Core|10 | HTTPS|

Verwalteter Agent-Code erfordert Microsoft .NET Framework 4.5


## Microsoft Azure Certified for IoT

**Microsoft Azure Certified for IoT** ist das Partnerprogramm, das Microsoft Azure mit dem umfassenderen IoT-Ökosystem verknüpft, um Entwicklern und Architekten die Kompatibilitätsszenarien verständlich zu machen. Es bietet insbesondere eine Liste vertrauenswürdiger Betriebssystem-Geräte-Konfigurationen, damit Sie schnell mit einem IoT-Projekt beginnen können – in der Phase der Machbarkeitsstudie oder in der Pilotphase. Mit zertifizierten Kombinationen von Gerät und Betriebssystem kann Ihr IoT-Projekt schnell gestartet werden, wobei sich mit weniger Aufwand an Arbeit und Anpassung sicherstellen lässt, dass Geräte mit [Azure IoT Suite][lnk-iot-suite] und Azure IoT Hub kompatibel sind.

## Für IoT zertifizierte Geräte

Die Kompatibilität der **für IoT zertifizierten Geräte** mit den Azure IoT-SDKs wurde getestet, und diese Geräte sind für Ihre IoT-Anwendung einsatzbereit. Wir bestimmen die Kompatibilität insbesondere anhand der Betriebssystemplattform und Codesprache.

#### Geräteliste

Die Kompatibilität jedes Geräts mit unserem SDK unter dem Betriebssystem und in der Sprache, die vom Gerätehersteller gewählt wurden, wurde zertifiziert. Beispielsweise ist BeagleBone Black mit Debian und den Sprachen C, JavaScript und Java kompatibel. Dies bedeutet, dass Entwickler Anwendungen in jeder dieser Kombinationen von Sprache und Betriebssystem auf den jeweiligen Geräten erstellen können.

|Gerät| Getestetes Betriebssystem |Sprache|
|:---------|:----------|:----------|
|[AAEON ACP-1104](http://www.aaeon.com/en/p/infotainment-multi-touch-panel-solutions-1104/) |Windows 10 | C#|
|[AAEON BOXER-6614](http://www.aaeon.com/en/p/fanless-embedded-computers-boxer-6614/) |Windows 10 | C#|
|[AAEON GENE-BT05](http://www.aaeon.com/en/p/3-and-half-inches-subcompact-boards-gene-bt05/) |Windows 10 | C#|
|[AAEON PICO-BT01](http://www.aaeon.com/en/p/pico-itx-boards-pico-bt01) |Windows 10 | C#|
|[AAEON UP](http://www.up-board.org/) |ubilinux | C|
|[AAEON UP](http://www.up-board.org/) |Windows 10 | C#|
|[Acme Systems Arietta G25](http://www.acmesystems.it/arietta) |Debian | C|
|[ADLINK IMB-M43](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1600&seq=&id=&sid=&category=Industrial-Motherboards-&-SBC_ ATX&utm\_source=#) |Windows 10 | C#|
|[ADLINK MXE-200](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1505&seq=&id=&sid=&category=Fanless-Embedded-Computer_Integrated-Embedded-Computer&utm_source=) |Windows 10 | C#|
|[ADLINK MXE-202i](http://www.adlinktech.com/PD/web/PD_detail.php?pid=1589) |Wind River | JavaScript|
|[ADLINK MXE-5400](http://www.adlinktech.com/PD/web/PD_detail.php?pid=1318) |Windows 10 | C#|
|[Advantech Co., ARK-2121L](http://www.advantech.com/products/ark-2000_series_embedded_box_pcs/ark-2121l/mod_dd092808-0832-44bc-b38a-945eb7e016bd) |Windows 10 | C#|
|[Advantech Co., ARK-1123C](http://www.advantech.com/products/92d96fda-cdd3-409d-aae5-2e516c0f1b01/ark-1123c/mod_0b91165c-aa8c-485d-8d25-fde6f88f4873) |Windows 10 | C#|
|[Advantech Co., LTD UNO-1372G](http://www.advantech.com/products/gf-bvl2/uno-1372g/mod_8e63b3c9-b606-4725-a1af-94fccb98bb1a) |Windows 10 | C#|
|[Advantech Co., TREK-674](http://www.advantech.com/products/1-2jsj5t/trek-674/mod_88a737dd-819b-4c8e-8f2e-2bb75b04619b) |Windows 10 | C#|
|[Advantech Co., UTX-3115](http://www.advantech.com/products/bda911fe-28bc-4171-aed3-67f76f6a12c8/utx-3115/mod_fa00d5cd-7d2b-430b-8983-c232bfb9f315) |Windows 10 | C#|
|[Arduino MKR1000](https://www.arduino.cc/en/Main/ArduinoMKR1000) |Arduino-IDE | C|
|[Arduino Zero](https://www.arduino.cc/en/Guide/ArduinoZero) |Arduino-IDE | C|
|[Arrow DragonBoard 410c](http://partners.arrow.com/campaigns-na/qualcomm/dragonboard-410c/) |Windows 10 IoT Core | C#|
|[Avalue RIPAC-10P1](http://www.avalue.com.tw/) |Windows 10 | C#|
|[Avalue RITAB-10T1](http://www.avalue.com.tw/product/Intelligent-Systems/Mobile-Solution/Mobile-Solution/RiTab-10T1_2384) |Windows 10 | C#|
|[Axiomtek ICO300](http://www.axiomtek.com/Default.aspx?MenuId=Products&FunctionId=ProductView&ItemId=1151) |Windows 10 | C#|
|[BeagleBone Black](http://beagleboard.org/black) | Debian | C, JavaScript, Java|
|[BeagleBone Green](http://beagleboard.org/green) |Debian | C, JavaScript, Java|
|[ComponentSoft RFID Tunnel](http://www.componentsoft.com/) |Windows 10 | C#|
|[Dell Edge Gateway 5000-Serie](http://www.dell.com/IoTgateway) |Ubuntu | Java|
|[DFI EC700-BT](http://www.dfi.com.tw/Upload/Product/Documents/BT700.pdf) |Windows 10 | C|
|[e-con Systems Almach](http://www.e-consystems.com/DM3730-development-board.asp) |Linux Yocto | C|
|[e-con Systems Ankaa](http://www.e-consystems.com/iMX6-development-board.asp) |Ubuntu | C|
|[Embedded Systems LogicMachine-Serie](http://openrb.com/products/) |Benutzerdefiniertes Linux | C|
|[Freescale FRDM K64](http://www.freescale.com/products/arm-processors/kinetis-cortex-m/k-series/k6x-ethernet-mcus/freescale-freedom-development-platform-for-kinetis-k64-k63-and-k24-mcus:FRDM-K64F) |mbed 2.0 | C|
|[HPE Edgeline EL10](http://www8.hp.com/h20195/v2/GetPDF.aspx/c04884747.pdf) |Windows 10 | C#|
|[HPE Edgeline EL20](http://www8.hp.com/h20195/v2/GetPDF.aspx/c04884769.pdf) |Windows 10 | C#|
|[IEI ICECARE-10W](http://www.ieimobile.com/index.php?option=com_content&view=article&id=222&Itemid=21) |Windows 10 | C#|
|[IEI DRPC-120](http://www.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0D182494345754583862&id=0E318374091597499543#.VqW3Q_l97Dd) |Windows 10 | C#|
|[IEI IVS-100-BT](http://tw.ieiworld.com/product_groups/industrial/content.aspx?gid=09049552811981014603&cid=0F202412454715193114&id=0F202496627608256517#.VqH1hvl97Dc) |Windows 10 | C#|
|[Ilevia Eve Raspberry](http://www.ilevia.com/overview/) |Debian | C|
|[Intel Edison](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html) |Yocto | C, JavaScript|
|[Inventec Corp-Avatar](http://www.inventec.com/indexEN.htm) |Windows 10 IoT Core | C#|
|[Libelium Meshlium Xtreme](http://www.libelium.com/products/meshlium/) |Debian | Java|
|[MechaTracks 3GPI](http://www.mechatrax.com/products/3gpi) |Debian | C|
|[Minnowboard Max](http://www.minnowboard.org/meet-minnowboard-max/) |Windows 7, 8, 10 | C#|
|[MiTAC Computing Technologie Pluto E220](http://client.mitac.com/products-Embedded-Box-PC-PlutoE220.html) |Windows 10 | C#|
|[NEXCOM NISE 50C](http://www.nexcom.com/Products/industrial-computing-solutions/industrial-fanless-computer/atom-compact/fanless-computer-nise-50c) |Windows 10 IoT Core | C#|
|[Pacific Control Systems G2021ES](http://www.pacificcontrols.net/products/G2021ES-Gateway.html) |Ubuntu | C|
|[PANASONIC Toughpad FZ + F1](http://www.panasonic.com/global/home.html) |Windows 10 IoT Mobile Enterprise | C#|
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Raspbian | C, JavaScript, Java |
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Windows 10 IoT Core| C, JavaScript, C#|
|[Samsung ARTIK](http://developer.samsung.com/artik) |Fedora | C|
|[SOTEC CloudPlug](http://cloudplug.info/) |YOCTO | C|
|[TI CC3200](http://www.ti.com/product/cc3200) |TI-RTOS 2.x | C|
|[Toradex Apalis iMX6](https://www.toradex.com/computer-on-modules/apalis-arm-family/freescale-imx-6) |Linux Angstrom(Yocto) | JavaScript, Java|
|[Toradex Apalis T30](https://www.toradex.com/computer-on-modules/apalis-arm-family/nvidia-tegra-3) |Linux Angstrom(Yocto) | JavaScript, Java|
|[Toradex Colibri iMX6](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-imx6) |Linux Angstrom(Yocto) | JavaScript, Java|
|[Toradex Colibri T20](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-2) |Linux Angstrom(Yocto) | Java|
|[Toradex Colibri T30](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-3) |Windows 10 IoT Core | C#|
|[Toradex Colibri VF61](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-vybrid-vf6xx) |Linux Angstrom(Yocto) | JavaScript, Java|
|[Trex NGP](http://www.trex.com.tr/en/donanim_dcasngp8739_73.php) |Windows 10 | C#|
|[Trueverit V4](http://www.trueverit.com/) |Benutzerdefiniertes Linux | C|
|[USISH EDA8909](http://www.usish.com/) |Windows 10 | C#|

[Verbinden Sie Ihr Gerät mit Azure IoT Hub](https://azure.microsoft.com/develop/iot/get-started/), oder besuchen Sie unser GitHub-[Repository](https://github.com/Azure/azure-iot-sdks) (in englischer Sprache), und durchsuchen Sie die Gerätedokumentation nach Sprache.

## Nächste Schritte

Erfahren Sie mehr zum Entwickeln von Lösungen mit [für IoT zertifizierten Geräten](http://azure.com/iotdev).


[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0302_2016-->