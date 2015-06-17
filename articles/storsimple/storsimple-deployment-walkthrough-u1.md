<properties 
   pageTitle="Bereitstellen lokaler StorSimple-Geräte"
   description="Schritte und bewährte Methoden für die Bereitstellung von Update 1 für StorSimple-Gerät und -Dienst."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/27/2015"
   ms.author="v-sharos" />

# Bereitstellen lokaler StorSimple-Geräte

[AZURE.INCLUDE [storsimple-version-selector](../../includes/storsimple-version-selector.md)]

## Übersicht

Willkommen bei der exemplarischen Vorgehensweise für die Bereitstellung von Microsoft Azure StorSimple-Geräten.

Diese Bereitstellungslernprogramme beziehen sich auf Update 1.0 der StorSimple 8000 Serie.

Diese Reihe von Lernprogrammen beschreibt die Konfiguration Ihrer StorSimple-Geräte. Sie enthält darüber hinaus eine Installationsvorbereitungsprüfliste, Konfigurationsvoraussetzungen und detaillierte Konfigurationsschritte.

> [AZURE.NOTE]Die StorSimple-Bereitstellungsinformationen, die auf der Microsoft Azure-Website und in der MSDN Library veröffentlicht werden, gelten nur für die Geräte der StorSimple-Serie 8000. Vollständige Informationen zu Geräten der Serie 7000 finden Sie unter: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Bereitstellungsinformationen zur Serie 7000 finden Sie in der [Schnellstartanleitung zum StorSimple-System](http://onlinehelp.storsimple.com/111_Appliance/) (in englischer Sprache).

Bei den Informationen in diesen Lernprogrammen wird davon ausgegangen, dass Sie die Sicherheitsvorkehrungen und die Konfigurationsprüfliste bearbeitet und Ihr StorSimple-Gerät ausgepackt, installiert und verkabelt haben. Wenn Sie weiterhin solche Aufgaben ausführen müssen, fahren Sie ggf. mit [Sicherheitsmaßnahmen](https://msdn.microsoft.com/library/azure/dn772366.aspx), [Konfigurationsprüfliste](https://msdn.microsoft.com/library/azure/dn757787.aspx) und [Hardwareinstallation Ihres Geräts](https://msdn.microsoft.com/library/azure/dn772375.aspx) fort.

Sie benötigen Administratorrechte, um die Einrichtung und Konfiguration abzuschließen. Es wird empfohlen, dass Sie die Vorinstallationsprüfliste überprüfen, bevor Sie beginnen. Die Bereitstellung und Konfiguration kann einige Zeit in Anspruch nehmen.

## Vorinstallationsprüfliste

Die folgende Vorinstallationsprüfliste enthält die Informationen, die Sie vor der Konfiguration der Software auf dem StorSimple-Gerät erfassen müssen. Wenn Sie diese Informationen im Voraus sammeln, beschleunigt dies die Bereitstellung des StorSimple-Geräts in Ihrer Umgebung.

| | Anforderungen | Details | Werte |
|---| --------------------- | ---------------------- | ------------- |
| 1 | Netzwerkeinstellungen <ol><li>Geräte-IP-Adresse</li><li>Netzwerkschnittstellen, 4 x 1 GbE, 2 x 10 GbE</li><li>Feste Controller-IP-Adresse</li><li>Subnetzmasken</li><li>Gateway</li></ol> | Erforderliche Gesamtanzahl von IP-Adressen: 8 <ol><li>Eine pro Gerät</li><li>Eine pro aktivierter Netzwerkschnittstelle, insgesamt 6</li><li>Eine pro Controller, insgesamt 2, für die Verbindung mit dem Internet für Dienstupdates</li><li>Eine für jede IP-Adresse</li><li>Eine pro Gerät</li></ol> | |
| 2 | Serieller Zugriff | Anfängliche Gerätekonfiguration | Ja/Nein |
| 3 | IP-Adressen der DNS-Server | Erforderlich für die Verbindung mit Microsoft Azure: insgesamt 2 erforderlich für hohe Verfügbarkeit | |
| 4 | IP-Adressen der NTP-Server | Erforderlich zur Zeitsynchronisierung mit Azure: 1 erforderlich, 1 optional | |
| 5 | Proxyserver (optional) | IP-Adresse/vollqualifizierter Domänenname des Proxyservers, zu verwendender Port | |
| 6 | Azure-Speicherkonto | Anmeldeinformationen für den Zugriff, z. B. Kontoname, Zugriffsschlüssel, pro Speicherkonto | |
| 7 | Verschlüsselungsschlüssel für Cloud-Speicher (empfohlen) | Pro Volumecontainer | |
| 8 | IQN des Hosts | Pro Host | |

## Voraussetzungen für die Bereitstellung

In den folgenden Abschnitten werden die Konfigurationsvoraussetzungen für den StorSimple-Manager-Dienst und das StorSimple-Gerät beschrieben.

### Für den StorSimple-Manager-Dienst

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Sie besitzen ein Microsoft-Konto mit Anmeldeinformationen für den Zugriff.

- Sie besitzen ein Microsoft Azure-Speicherkonto mit Anmeldeinformationen für den Zugriff.

- Ihr Microsoft Azure-Abonnement ist für den StorSimple-Manager-Dienst aktiviert. Ihr Abonnement sollten Sie über die [Lizenzierung für Unternehmen](http://azure.microsoft.com/pricing/enterprise-agreement/) erwerben.

- Sie haben Zugriff auf Terminalemulationssoftware wie PuTTY.

### Für das Gerät im Datencenter

Stellen Sie vor der Konfiguration des Geräts Folgendes sicher:

- Ihr Gerät wurde wie unter [Auspacken des Geräts vom Typ 8100](https://msdn.microsoft.com/library/azure/dn772327.aspx) oder [Auspacken des Geräts vom Typ 8600](https://msdn.microsoft.com/library/azure/dn772418.aspx) beschrieben vollständig ausgepackt.

- Ihr Gerät wurde wie unter [Einbauen eines Geräts vom Typ 8100 [StorSimple 8100] in ein Gestell](https://msdn.microsoft.com/library/azure/dn757749.aspx) oder [Einbauen eines Geräts vom Typ 8600 [StorSimple 8600] in ein Gestell](https://msdn.microsoft.com/library/azure/dn757745.aspx) beschrieben vollständig installiert.

- Ihr Gerät wurde wie unter [Verkabeln eines Geräts vom Typ 8100](https://msdn.microsoft.com/library/azure/dn757738.aspx) oder [Verkabeln eines Geräts vom Typ 8600](https://msdn.microsoft.com/library/azure/dn757762.aspx) beschrieben vollständig für Stromversorgung, Netzwerk- und seriellen Zugriff verkabelt.

- Die Ports in der Datencenter-Firewall werden geöffnet, um iSCSI- und Cloud-Datenverkehr zu ermöglichen, wie in [Netzwerkanforderungen für das StorSimple-Gerät](https://msdn.microsoft.com/library/dn772371.aspx) beschrieben.

## Bereitstellungsschritte

Führen Sie die folgenden erforderlichen Schritte zum Konfigurieren Ihres StorSimple-Geräts und zum Verbinden des Geräts mit dem StorSimple-Manager-Dienst durch:

- Schritt 1: Erstellen eines neuen Diensts 
- Schritt 2: Abrufen des Dienstregistrierungsschlüssels
- Schritt 3: Konfigurieren und Registrieren des Geräts über Windows PowerShell für StorSimple 
- Schritt 4: Abschließen der minimalen Geräteinstallation
- Schritt 5: Erstellen eines Volumecontainers 
- Schritt 6: Erstellen eines Volumes
- Schritt 7: Bereitstellen, Initialisieren und Formatieren eines Volumes 
- Schritt 8: Erstellen einer Sicherung

Neben den erforderlichen Schritte gibt es auch einige optionale Schritte, die Sie eventuell durchführen müssen, während Sie Ihre Lösung bereitstellen. Diese optionalen Schritte erklären Folgendes:

- Konfigurieren eines neuen Speicherkontos für den Dienst
- Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole
- Abrufen des IQN eines Windows Server-Hosts
- Erstellen einer manuellen Sicherung
- Konfigurieren von MPIO

Die schrittweise Bereitstellungsanleitung gibt auch an, wann Sie die folgenden optionalen Schritte jeweils ausführen sollten.

## Schritt 1: Erstellen eines neuen Diensts

Ein StorSimple-Manager-Dienst kann mehrere StorSimple-Geräte verwalten. Führen Sie die folgenden Schritte aus, um einen neuen StorSimple-Manager-Dienst zu erstellen.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT]Wenn Sie nicht die automatische Erstellung eines Speicherkontos mit Ihrem Dienst aktiviert haben, müssen Sie mindestens ein Speicherkonto erstellen, nachdem Sie einen Dienst erstellt haben. Dieses Speicherkonto wird beim Erstellen eines Volumecontainers verwendet.
>
> * Wenn Sie nicht automatisch ein Speicherkonto erstellt haben, finden Sie unter [Konfigurieren eines neuen Speicherkontos für den Dienst](#configure-a-new-storage-account-for-the-service) ausführliche Anweisungen. 
> * Wenn Sie die automatische Erstellung eines Speicherkontos aktiviert haben, fahren Sie mit [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](#step-2:-get-the-service-registration-key) fort.

## Schritt 2: Abrufen des Dienstregistrierungsschlüssels

Nachdem der StorSimple-Manager-Dienst eingerichtet wurde und ausgeführt wird, müssen Sie den Dienstregistrierungsschlüssel abrufen. Dieser Schlüssel wird zum Registrieren Ihres StorSimple-Geräts sowie zum Herstellen einer Verbindung mit dem Dienst verwendet.

Führen Sie die folgenden Schritte im Verwaltungsportal aus.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## Schritt 3: Konfigurieren und Registrieren des Geräts über Windows PowerShell für StorSimple

Verwenden Sie Windows PowerShell für StorSimple zum Abschließen der anfänglichen Installation Ihres StorSimple-Geräts wie im folgenden Verfahren erläutert. Sie müssen eine Terminalemulationssoftware verwenden, um diesen Schritt abzuschließen. Weitere Informationen finden Sie unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-Configure-and-Register-Device-U1](../../includes/storsimple-configure-and-register-device-u1.md)]

## Schritt 4: Abschließen der minimalen Geräteinstallation

Sie müssen für die Gerätemindestkonfiguration des StorSimple-Geräts die folgenden Aufgaben ausführen:

- Einrichten des sekundären DNS-Servers
- Aktivieren von iSCSI an mindestens einer Netzwerkschnittstelle
- Zuweisen fester IP-Adressen an beide Controller

Führen Sie die folgenden Schritte im Verwaltungsportal aus, um das mindestens erforderliche Setup für das Gerät auszuführen:

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## Schritt 5: Erstellen eines Volumecontainers

Ein Volumecontainer verfügt über Einstellungen für das Speicherkonto, die Bandbreite und die Verschlüsselung für alle darin enthaltenen Volumes. Sie müssen einen Volumecontainer erstellen, bevor Sie mit der Bereitstellung von Volumes auf Ihrem StorSimple-Gerät beginnen können.

Führen Sie die folgenden Schritte im Verwaltungsportal aus, um einen Volumecontainer zu erstellen.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## Schritt 6: Erstellen eines Volumes

Nach dem Erstellen eines Volumecontainers können Sie ein Speichervolume auf dem StorSimple-Gerät für Ihre Server bereitstellen. Führen Sie die folgenden Schritte im Verwaltungsportal aus, um ein Volume zu erstellen:

> [AZURE.IMPORTANT]Mit Azure StorSimple können nur nach Bedarf bereitgestellte Volumes erstellt werden. In einem Azure StorSimple-System können keine vollständig oder teilweise bereitgestellten Volumes erstellt werden.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## Schritt 7: Bereitstellen, Initialisieren und Formatieren eines Volumes

Führen Sie die folgenden Schritte auf Ihrem Windows Server-Host aus.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## Schritt 8: Erstellen einer Sicherung

Sicherungen stellen Zeitpunktschutz für Volumes zur Verfügung und verbessern die Wiederherstellbarkeit bei gleichzeitiger Minimierung der Wiederherstellungszeiten. Für Ihr StorSimple-Gerät können zwei Arten von Sicherungen angefertigt werden: lokale Momentaufnahmen und Cloud-Momentaufnahmen. Jeder dieser Sicherungstypen kann **geplant** sein oder **manuell** erfolgen.

Führen Sie die folgenden Schritte im Verwaltungsportal aus, um eine geplante Sicherung zu erstellen.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Sie können jederzeit eine manuelle Sicherung vornehmen. Anweisungen dazu finden Sie unter [Erstellen einer manuellen Sicherung](#create-a-manual-backup).

## Konfigurieren eines neuen Speicherkontos für den Dienst

Dies ist ein optionaler Schritt, den Sie nur dann ausführen müssen, wenn Sie nicht die automatische Erstellung eines Speicherkontos mit Ihrem Dienst aktiviert haben. Ein Microsoft Azure-Speicherkonto ist erforderlich, um einen StorSimple-Volumecontainer zu erstellen.

Wenn Sie ein Azure-Speicherkonto in einer anderen Region erstellen müssen, finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/storage-create-storage-account.md) schrittweise Anweisungen.

Führen Sie die folgenden Schritte im Verwaltungsportal auf der Seite für den **StorSimple-Manager-Dienst** aus.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole

Zum Herstellen einer Verbindung mit Windows PowerShell für StorSimple benötigen Sie eine Terminalemulationssoftware wie etwa PuTTY. Sie können PuTTY verwenden, wenn Sie über die serielle Konsole direkt auf das Gerät zugreifen oder eine Telnet-Sitzung von einem Remotecomputer aus öffnen.

[AZURE.INCLUDE [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](../../includes/storsimple-use-putty.md)]

## Abrufen des IQN eines Windows Server-Hosts

Führen Sie die folgenden Schritte aus, um den IQN (iSCSI Qualified Name) eines Windows-Hosts abzurufen, auf dem Windows Server® 2012 ausgeführt wird.

[AZURE.INCLUDE [Erstellen einer manuellen Sicherung](../../includes/storsimple-get-iqn.md)]

## Erstellen einer manuellen Sicherung

Führen Sie die folgenden Schritte im Verwaltungsportal aus, um nach Bedarf eine manuelle Sicherung eines einzelnen Volumes auf dem StorSimple-Gerät zu erstellen.

[AZURE.INCLUDE [Erstellen einer manuellen Sicherung](../../includes/storsimple-create-manual-backup.md)]

## Konfigurieren von MPIO

Multipfad-E/A (Multipath I/O, MPIO) ist ein optionales Feature und wird unter Windows Server nicht standardmäßig installiert. Diese Funktion sollte als Feature über den Server-Manager installiert werden.

> [AZURE.NOTE]MPIO wird auf einem virtuellen StorSimple-Gerät nicht unterstützt.

Installationsanweisungen für MPIO finden Sie unter [Konfigurieren von MPIO für Ihr StorSimple-Gerät](storsimple-configure-mpio-windows-server.md).

## Nächste Schritte

Konfigurieren eines [virtuellen Geräts](storsimple-virtual-device.md)

Verwenden des [StorSimple-Manager-Diensts](https://msdn.microsoft.com/library/azure/dn772396.aspx) für das Verwalten Ihres StorSimple-Geräts

<!---HONumber=58--> 