<properties 
   pageTitle="Dashboard des StorSimple Manager-Diensts | Microsoft Azure"
	description="Beschreibt das Dashboard des StorSimple Manager-Diensts und erläutert, wie Sie den Zustand Ihrer StorSimple-Lösung über dieses Dashboard verwalten."
	services="storsimple"
	documentationCenter=""
	authors="SharS"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="09/02/2015"
	ms.author="v-sharos"/>

# Verwenden des Dashboards des StorSimple Manager-Diensts

## Übersicht

Auf der Dashboard-Seite des StorSimple Manager-Diensts erhalten Sie einen Überblick über alle Geräte, die mit dem StorSimple Manager-Dienst verbunden sind. Dienste, die die Aufmerksamkeit des Systemadministrators erfordern, sind dabei markiert. Dieses Tutorial bietet eine Einführung zur Dashboardseite, erläutert die Dashboardinhalte und -funktion und beschreibt die Aufgaben, die Sie auf dieser Seite ausführen können.

![Dienstdashboard](./media/storsimple-service-dashboard/HCS_ServiceDashboard.png)

**Abbildung 1: Dashboard des StorSimple Manager-Diensts**

Das Dashboard des StorSimple Manager-Diensts zeigt die folgenden Informationen an:

- Im **Diagrammbereich** wird Ihnen das relevante Metrikdiagramm für Ihre Geräte präsentiert. Sie können den von allen Geräten über einen bestimmten Zeitraum verwendeten primären Speicher und Cloudspeicher anzeigen. Verwenden Sie die Steuerelemente oben rechts im Diagramm, um einen Zeitraum von einer Woche, einem Monat, drei Monaten oder einem Jahr anzugeben.

- Die **Nutzungsübersicht** zeigt den bereitgestellten primären Speicher, der von allen Geräten im Verhältnis zum Gesamtspeicher genutzt wird, der für alle Geräte zur Verfügung steht. **Bereitgestellt** bezieht sich auf die Menge an Speicherplatz, die für die Verwendung vorbereitet und zugewiesen wird. **Verwendet** bezieht sich auf die Verwendung von Volumes, wie sie von den Initiatoren angezeigt wird, die mit den Geräten verbunden sind.

- Der Bereich **Warnungen** stellt eine Momentaufnahme aller aktiven Warnungen auf sämtlichen Geräten bereit, gruppiert nach dem jeweiligen Schweregrad. Durch Klicken auf den Schweregrad wird die Seite mit den **Warnungen** geöffnet, auf der nur die betreffenden Warnungen angezeigt werden. Auf der Seite mit den **Warnungen** können Sie auf einzelne Warnungen klicken, um weitere Details zur jeweiligen Warnung anzuzeigen, einschließlich empfohlener Aktionen. Sie können die Warnung auch löschen, falls das Problem behoben wurde.

- Der Bereich **Aufträge** bietet eine Momentaufnahme der kürzlich ausgeführten Aufträge auf allen Geräten, die mit dem Dienst verbunden sind. Über die aufgeführten Links können Sie die Aufträge anzeigen, die derzeit ausgeführt werden, sowie Aufträge, bei denen innerhalb der letzten 24 Stunden Fehler aufgetreten sind, oder solche, die in den nächsten 24 Stunden ausgeführt werden sollen.

- Der **Schnellansichtsbereich** bietet nützliche Informationen wie den Dienststatus, die Anzahl der mit dem Dienst verbundenen Geräte, den Standort des Diensts und Details zum Abonnement, das dem Dienst zugeordnet ist. Es gibt auch ein Link zum Vorgangsprotokoll. Klicken Sie auf den Link, um eine Liste aller abgeschlossenen Vorgänge des StorSimple Manager-Diensts anzuzeigen.

Über das Dashboard des StorSimple Manager-Diensts können Sie die Ausführung der folgenden Aufgaben initiieren:

- Anzeigen oder Neugenerieren des Dienstregistrierungsschlüssels
- Ändern des Verschlüsselungsschlüssels für Dienstdaten
- Anzeigen der Vorgangsprotokolle

## Anzeigen oder Neugenerieren des Dienstregistrierungsschlüssels

Der Dienstregistrierungsschlüssel wird verwendet, um ein Microsoft Azure StorSimple-Gerät mit dem StorSimple Manager-Dienst zu registrieren, damit das Gerät im Microsoft Azure-Verwaltungsportal für weitere Verwaltungsvorgänge angezeigt wird. Der Schlüssel wird auf dem ersten Gerät erstellt und mit den übrigen Geräten gemeinsam genutzt.

Durch Klicken auf **Registrierungsschlüssel** (unten auf der Seite) wird das Dialogfeld **Dienstregistrierungsschlüssel** geöffnet, über das Sie den aktuellen Dienstregistrierungsschlüssel in die Zwischenablage kopieren oder den Dienstregistrierungsschlüssel neu generieren können.

Das erneute Generieren des Schlüssels wirkt sich nicht auf zuvor registrierte Geräte aus, sondern nur auf die Geräte, die nach der Neugenerierung des Schlüssels mit dem Dienst registriert werden.

Weitere Informationen über das Anzeigen und Generieren des Dienstregistrierungsschlüssel finden Sie unter [Abrufen des Dienstregistrierungsschlüssels](storsimple-manage-service.md#get-the-service-registration-key).

## Ändern des Verschlüsselungsschlüssels für Dienstdaten

Dienstdaten-Verschlüsselungsschlüssel werden verwendet, um vertrauliche Kundendaten zu verschlüsseln, beispielsweise Anmeldeinformationen für das Speicherkonto, die vom StorSimple Manager-Dienst zum StorSimple-Gerät gesendet werden. Sie müssen diese Schlüssel regelmäßig ändern, wenn Ihre IT-Organisation über eine Richtlinie zur Schlüsselrotation für Speichergeräte verfügt. Das Verfahren zur Schlüsseländerung kann variieren, je nachdem, ob ein einzelnes Gerät oder mehrere Geräte vom StorSimple Manager-Dienst verwaltet werden.

Das Ändern den Verschlüsselungsschlüssel für Dienstdaten wird in drei Schritten vollzogen:

1. Sie autorisieren ein Gerät über das Verwaltungsportal, um den Verschlüsselungsschlüssel für Dienstdaten zu ändern.
2. Sie verwenden Windows PowerShell für StorSimple, um die Änderung des Verschlüsselungsschlüssels für Dienstdaten zu initialisieren.
3. Wenn Sie über mehrere StorSimple-Geräte verfügen, aktualisieren Sie den Verschlüsselungsschlüssel für Dienstdaten auf den anderen Geräten.

Die folgenden Schritte beschreiben den Rolloverprozess für den Dienstdaten-Verschlüsselungsschlüssel.

[AZURE.INCLUDE [storsimple-change-data-encryption-key](../../includes/storsimple-change-data-encryption-key.md)]


## Anzeigen der Vorgangsprotokolle

Sie können die Vorgangsprotokolle anzeigen, indem Sie auf den entsprechenden Link im **Schnellansichtsbereich** des Dashboards klicken. Dadurch gelangen Sie zur Verwaltungsdienste-Seite. Dort können Sie die Protokolle filtern und die für den StorSimple Manager-Dienst spezifischen Protokolle anzeigen.

## Nächste Schritte

[Behandeln von Problemen bei StorSimple-Geräten](storsimple-troubleshoot-operational-device.md).

<!---HONumber=September15_HO1-->