<properties
   pageTitle="Systemanforderungen für StorSimple Virtual Array"
   description="Erfahren Sie mehr über die Software- und Netzwerkanforderungen für StorSimple Virtual Array."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/07/2015"
   ms.author="alkohli"/>

# Systemanforderungen für StorSimple Virtual Array (Vorschau)

## Übersicht

In diesem Artikel werden wichtige Systemanforderungen für Microsoft Azure StorSimple Virtual Array (auch als lokales virtuelles StorSimple-Gerät oder virtuelles StorSimple-Gerät bezeichnet) und für die Speicherclients beschrieben, die auf das Array zugreifen. Sie sollten die Informationen sorgfältig überprüfen, bevor Sie Ihr StorSimple-System bereitstellen. Auch später sollten Sie während der Bereitstellung und beim nachfolgenden Betrieb bei Bedarf als Referenz darauf zurückgreifen.

Systemanforderungen:

-   **Softwareanforderungen für Speicherclients:** Hier werden die unterstützten Virtualisierungsplattformen, Webbrowser, iSCSI-Initiatoren, SMB-Clients, Mindestanforderungen für virtuelle Geräte und alle sonstigen Anforderungen für diese Betriebssysteme beschrieben.

-   **Netzwerkanforderungen für das StorSimple-Gerät:** bietet Informationen zu den Ports, die in der Firewall für Datenverkehr von iSCSI, Cloud oder Verwaltung geöffnet sein müssen.

Die in diesem Artikel veröffentlichten Informationen zu den StorSimple-Systemanforderungen gelten nur für StorSimple Virtual Arrays.

- Informationen zu Geräten der 8000-Serie finden Sie unter [Systemanforderungen für StorSimple-Geräte der 8000-Serie](storsimple-system-requirements.md).
 
- Informationen zu Geräten der 7000-Serie finden Sie unter [Systemanforderungen für StorSimple-Geräte der 5000-7000-Serie](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

> [AZURE.IMPORTANT]
> 
> - Diese öffentliche Vorschauversion ist nur für Testzwecke bestimmt. Die Installation dieser Vorschauversion in einer Produktionsumgebung wird nicht unterstützt.
> - Wenn Probleme mit StorSimple Virtual Array auftreten, posten Sie sie im [StorSimple-MSDN-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=StorSimple).  

## Softwareanforderungen

Die Softwareanforderungen umfassen Informationen zu den unterstützten Webbrowsern, SMB-Versionen, Virtualisierungsplattformen und die Mindestanforderungen für virtuelle Geräte.

### Unterstützte Virtualisierungsplattformen


| **Hypervisor** | **Version** |
|----------------|--------------------------------------|
| Hyper-V | Windows Server 2008 R2 SP1 und höher |
| VMware ESXi | 5\.5 und höher |

### Anforderungen für virtuelle Geräte

| **Komponente** | **Anforderung** |
|----------------------------------------------|----------------------------|
| Mindestanzahl virtueller Prozessoren (Kerne) | 4 |
| Minimaler Arbeitsspeicher (RAM) | 8 GB |
| Festplattenspeicher<sup>1</sup> | Betriebssystemdatenträger: 80 GB <br></br>Datenträger: 500 GB bis 8 TB|
| Mindestanzahl von Netzwerkschnittstellen | 1 |
| Minimale Internetbandbreite<sup>2</sup> | 5 MBit/s |

<sup>1</sup>: Bereitgestelltes Thin-Gerät

<sup>2</sup>: Die Netzwerkanforderungen können abhängig von der täglichen Datenänderungsrate variieren. Beispiel: Wenn von einem Gerät Änderungen mit einem Umfang von mindestens 10 GB pro Tag gesichert werden müssen, kann die tägliche Sicherung bei einer Verbindung mit 5 MBit/s bis zu 4,25 Stunden dauern (falls die Daten nicht komprimiert oder dedupliziert werden konnten).

### Unterstützte Webbrowser

| **Komponente** | **Version** | **Weitere Anforderungen/Hinweise** |
|-------------------|-----------------|-----------------------------------|
| Microsoft Edge | Aktuelle Version | |
| Internet Explorer | Aktuelle Version | Getestet mit Internet Explorer 11 |
| Google Chrome | Aktuelle Version | Getestet mit Chrome 46 |

### Unterstützte SMB-Versionen

| **Version** |
|-------------|
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

## Netzwerkanforderungen 

In der folgenden Tabelle sind die Ports aufgeführt, die in der Firewall für iSCSI-, SMB-, Cloud- oder Verwaltungsdatenverkehr geöffnet werden müssen. In dieser Tabelle bezieht sich *ein* oder *eingehend* auf die Richtung, aus der eingehende Clientanforderungen auf das Gerät zugreifen. Entsprechend bezieht sich *aus* oder *ausgehend* auf die Richtung, in der das StorSimple-Gerät Daten über die Bereitstellung hinaus an externe Ziele sendet: z. B. ausgehende Verbindungen mit dem Internet.

| **Portnr.<sup>1</sup>** | **ein oder aus** | **Portbereich** | **Erforderlich** | **Hinweise** |
|--------------------------|---------------|----------------|---------------------------|----------------------------------------------------------------------------------------------------------------------|
| TCP 80 (HTTP) | aus | WAN | Nein | Der ausgehende Port wird für den Internetzugriff zum Abrufen von Updates verwendet. <br></br>Der ausgehende Webproxy kann vom Benutzer konfiguriert werden. |
| TCP 443 (HTTPS) | aus | WAN | Ja | Der ausgehende Port wird für den Zugriff auf Daten in der Cloud verwendet. <br></br>Der ausgehende Webproxy kann vom Benutzer konfiguriert werden. |
| UDP 53 (DNS) | aus | WAN | In einigen Fällen; siehe Hinweise. | Dieser Port ist nur dann erforderlich, wenn Sie einen internetbasierten DNS-Server verwenden. <br></br> **Hinweis:** Bei der Bereitstellung eines Dateiservers wird die Verwendung eines lokalen DNS-Servers empfohlen.|
| UDP 123 (NTP) | aus | WAN | In einigen Fällen; siehe Hinweise. | Dieser Port ist nur dann erforderlich, wenn Sie einen internetbasierten NTP-Server verwenden.<br></br> **Hinweis:** Bei der Bereitstellung eines Dateiservers wird empfohlen, die Zeit mit Ihren Active Directory-Domänencontrollern zu synchronisieren. |
|TCP 9354 | aus | WAN | Ja | Der ausgehende Port wird vom StorSimple-Gerät für die Kommunikation mit dem StorSimple Manager-Dienst verwendet.|
| TCP 80 (HTTP) | Geben Sie in | LAN | Ja | Dies ist der eingehende Port für die lokale Benutzeroberfläche auf dem StorSimple-Gerät für die lokale Verwaltung. <br></br> **Hinweis:** Beim Zugriff auf die lokale Benutzeroberfläche über HTTP erfolgt automatisch eine Umleitung an HTTPS.|
| TCP 443 (HTTPS) | Geben Sie in | LAN | Ja | Dies ist der eingehende Port für die lokale Benutzeroberfläche auf dem StorSimple-Gerät für die lokale Verwaltung.|
| TCP 3260 (iSCSI) | Geben Sie in | LAN | Nein | Dieser Port wird für den Datenzugriff über iSCSI verwendet.|

<sup>1</sup> Es müssen keine eingehenden Ports für das öffentliche Internet geöffnet werden.

## Nächster Schritt

-   [Vorbereiten des Portals zum Bereitstellen von StorSimple Virtual Array](storsimple-ova-deploy1-portal-prep.md)

<!---HONumber=AcomDC_0121_2016-->