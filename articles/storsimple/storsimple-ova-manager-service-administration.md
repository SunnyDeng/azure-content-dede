<properties 
   pageTitle="StorSimple Manager Virtual Array-Verwaltung | Microsoft Azure"
   description="Erfahren Sie, wie Sie Ihr lokales StorSimple Virtual Array mithilfe des StorSimple Manager-Diensts im klassischen Azure-Portal verwalten."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="v-sharos" />

# Verwalten des StorSimple Virtual Array mithilfe des StorSimple Manager-Diensts (Vorschau)

![Setup-Prozessablauf](./media/storsimple-ova-manager-service-administration/manage4.png)

## Übersicht

In diesem wird die Benutzeroberfläche des StorSimple Manager-Diensts beschrieben und erläutert, wie Sie eine Verbindung herstellen und welche Optionen verfügbar sind. Darüber hinaus enthält der Artikel Links zu den Workflows, die über diese Benutzeroberfläche ausgeführt werden können.

In diesem Artikel lernen Sie Folgendes:

- Herstellen einer Verbindung mit dem StorSimple Manager-Dienst
- Navigieren auf der Benutzeroberfläche von StorSimple Manager
- Verwalten Ihres StorSimple Virtual Array über den StorSimple Manager-Dienst

> [AZURE.NOTE] Die Verwaltungsoptionen, die für das Gerät der StorSimple 8000er Serie verfügbar sind, finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-manager-service-administration.md).

## Herstellen einer Verbindung mit dem StorSimple Manager-Dienst

Der StorSimple Manager-Dienst wird in Microsoft Azure ausgeführt. Er stellt Verbindungen mit mehreren StorSimple Virtual Arrays her. Diese Geräte werden über ein zentrales browserbasiertes klassisches Microsoft Azure-Portal verwaltet. Gehen Sie wie folgt vor, um eine Verbindung mit dem StorSimple Manager-Dienst herzustellen:

#### So stellen Sie eine Verbindung mit dem Dienst her

1. Navigieren Sie zu [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

2. Melden Sie sich mit den Anmeldeinformationen Ihres Microsoft-Kontos beim klassischen Microsoft Azure-Portal (rechts oben) an.

3. Führen Sie im linken Navigationsbereich einen Bildlauf nach unten durch, um auf den StorSimple Manager-Dienst zuzugreifen.

    ![Bildlauf zum Dienst](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## Navigieren auf der Benutzeroberfläche des StorSimple Manager-Diensts

Die folgende Tabelle gibt Aufschluss über die Navigationshierarchie der Benutzeroberfläche des StorSimple Manager-Diensts.

- Über die Zielseite von **StorSimple Manager** gelangen Sie zu den Dienstebenenseiten, die alle Virtual Arrays in einem Dienst betreffen.

- Über die Seite **Geräte** gelangen Sie zu den Geräteebenenseiten, die ein bestimmtes Virtual Array betreffen.

#### Navigationshierarchie des StorSimple Manager-Diensts

|Zielseite|Seiten auf Dienstebene|Seiten auf Geräteebene|
|---|---|---|
|StorSimple Manager-Dienst|Dashboard (Dienst)|Dashboard (Gerät)|
|Geräte →|Überwachen|
|Sicherungskatalog|Freigaben (Dateiserver) oder </br>Volumes (iSCSI-Server)|
|Konfigurieren (Dienst)|Konfigurieren (Gerät)|
|Aufträge|Wartung|
|Warnungen|

## Verwenden des StorSimple Manager-Diensts zum Durchführen von Verwaltungsaufgaben

Die folgende Tabelle zeigt eine Zusammenfassung der allgemeinen Verwaltungsaufgaben und komplexen Workflows, die auf der Benutzeroberfläche des StorSimple Manager-Diensts ausgeführt werden können. Diese Aufgaben sind nach der Benutzeroberflächenseite strukturiert, auf der sie jeweils initiiert werden.

Klicken Sie auf die Verfahren in der Tabelle, um weitere Informationen zu den einzelnen Workflows zu erhalten.

#### StorSimple Manager-Workflows

|Gewünschte Aktion|Seite auf der Benutzeroberfläche|Verfahren|
|---|---|---|
|Dienst erstellen</br>Dienst löschen</br>Dienstregistrierungsschlüssel abrufen</br>Dienstregistrierungsschlüssel erneut generieren|StorSimple Manager-Dienst|[Bereitstellen des StorSimple Manager-Diensts](storsimple-ova-manage-service.md)|
|Virtual Array deaktivieren</br>Virtual Array löschen|StorSimple Manager-Dienst → Geräte|[Deaktivieren oder Löschen eines Virtual Array](storsimple-ova-deactivate-and-delete-device.md)|
|Notfallwiederherstellung und Gerätefailover</br>Voraussetzungen für Failover</br>Failover auf ein virtuelles Gerät</br>Business Continuity Disaster Recovery (BCDR)</br>Fehler während der Notfallwiederherstellung|StorSimple Manager-Dienst → Geräte|[Notfallwiederherstellung und Gerätefailover für das StorSimple Virtual Array](storsimple-ova-failover-dr.md)|
|Freigaben und Volumes sichern</br>Manuelle Sicherung erstellen</br>Sicherungszeitplan ändern</br>Vorhandene Sicherungen anzeigen|StorSimple Manager-Dienst → Sicherungskatalog|[Sichern des StorSimple Virtual Array](storsimple-ova-backup.md)|
|Freigaben aus einem Sicherungssatz wiederherstellen</br>Volumes aus einem Sicherungssatz wiederherstellen</br>Wiederherstellung auf Elementebene (nur Dateiserver)|StorSimple Manager-Dienst → Sicherungskatalog|[Wiederherstellen aus einer Sicherung des StorSimple Virtual Array](storsimple-ova-restore.md)|
|Über Speicherkonten informieren</br>Speicherkonto hinzufügen</br>Speicherkonto bearbeiten</br>Speicherkonto löschen|StorSimple Manager-Dienst → Konfigurieren|[Verwalten von Speicherkonten für das StorSimple Virtual Array](storsimple-ova-manage-storage-accounts.md)|
|Über Access Control-Datensätze informieren</br>Access Control-Datensatz hinzufügen oder ändern</br>Access Control-Datensatz löschen|StorSimple Manager-Dienst → Konfigurieren|[Verwalten von Access Control-Datensätzen für das StorSimple Virtual Array](storsimple-ova-manage-acrs.md)|
|Warnungseinstellungen konfigurieren</br>Warnungsbenachrichtigungen empfangen</br>Warnungen verwalten</br>Warnungen überprüfen|StorSimple Manager-Dienst → Warnungen|[Anzeigen und Verwalten von Warnungen für das StorSimple Virtual Array](storsimple-ova-manage-alerts.md)
|Ändern des Geräteadministratorkennworts|StorSimple Manager-Dienst → Geräte → Konfigurieren|[Ändern des StorSimple Virtual Array-Geräteadministratorkennworts](storsimple-ova-change-device-admin-password.md)|
|Softwareupdates installieren|StorSimple Manager-Dienst → Geräte → Wartung|[Virtual Array aktualisieren](storsimple-ova-update.md)|

>[AZURE.NOTE] Verwenden Sie die [lokale Webbenutzeroberfläche](storsimple-ova-web-ui-admin.md) für die folgenden Aufgaben:
>
>- [Verschlüsselungsschlüssel für Dienstdaten abrufen](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [Unterstützungspaket erstellen](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [Virtual Array beenden und neu starten](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##Nächste Schritte
Informationen zur Webbenutzeroberfläche und ihrer Nutzung finden Sie unter [Verwalten des StorSimple Virtual Array über die StorSimple-Webbenutzeroberfläche](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0218_2016-->