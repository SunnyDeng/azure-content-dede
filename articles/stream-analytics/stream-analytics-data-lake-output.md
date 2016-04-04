<properties
	pageTitle="Stream Analytics Data Lake-Speicherausgabe | Microsoft Azure"
	description="Konfigurieren der Authentifizierung und Autorisierung eines Azure Data Lake-Speichers in einem Stream Analytics-Auftrag"
	keywords=""
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="03/18/2016"
	ms.author="jeffstok"
/>

# Stream Analytics Data Lake-Speicherausgabe

Stream Analytics-Aufträge unterstützen mehrere Ausgabemethoden, darunter auch [Azure Data Lake-Speicher](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake-Speicher ist ein unternehmensweites riesiges Repository für Big Data-Analyseworkloads. Data Lake-Speicher ermöglicht es Ihnen, Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit zur Durchführung operativer und explorativer Analysen zu speichern. Dieser Artikel befasst sich mit der Autorisierung, Konfiguration und Autorisierungserneuerung eines Azure Data Lake-Speichers in Stream Analytics im klassischen Azure-Portal.

> [AZURE.NOTE] Derzeit wird die Erstellung und Konfiguration von Data Lake-Speicherausgaben **nur** im klassischen Azure-Portal unterstützt.

## Autorisieren eines Data Lake-Speicherkontos

1.  Wenn Data Lake-Speicher als Ausgabe im Azure-Verwaltungsportal ausgewählt ist, werden Sie aufgefordert, die Verwendung Ihres vorhandenen Data Lake-Speichers zu autorisieren oder über das klassische Azure-Portal Zugriff auf die Data Lake-Speicher-Vorschau anzufordern.

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.jpg)

2.  Falls Sie bereits Zugriff auf Data Lake-Speicher haben, klicken Sie auf „Jetzt autorisieren“, und für einen kurzen Moment wird eine Seite mit „Umleitung an die Autorisierung...“ angezeigt. Diese Seite wird automatisch geschlossen, und Ihnen wird die Seite zum Konfigurieren der Data Lake-Speicherausgabe angezeigt.

Falls Sie sich nicht für die Data Lake-Speicher-Vorschau angemeldet haben, können Sie dem Link „Jetzt registrieren“ folgen, um die Anfrage zu schicken. Oder Sie folgen den Anweisungen für [erste Schritte](../data-lake-store/data-lake-store-get-started-portal.md).

## Konfigurieren der Data Lake-Speicherausgabeeigenschaften

Sobald Sie das Data Lake-Speicherkonto authentifiziert haben, können Sie die Eigenschaften für die Data Lake-Speicherausgabe konfigurieren. Die folgende Tabelle enthält eine Liste von Eigenschaftennamen und deren Beschreibung zum Konfigurieren der Data Lake-Speicherausgabe.

<table>
<tbody>
<tr>
<td><B>EIGENSCHAFTENNAME</B></td>
<td><B>BESCHREIBUNG</B></td>
</tr>
<tr>
<td>Ausgabealias</td>
<td>Dies ist ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Data Lake-Speicher weiterzuleiten.</td>
</tr>
<tr>
<td>Data Lake-Speicherkonto</td>
<td>Der Name des Speicherkontos, an das Sie die Ausgabe senden. Ihnen wird eine Dropdown-Liste der Data Lake-Speicherkonten angezeigt, auf die der im Portal angemeldete Benutzer Zugriff hat.</td>
</tr>
<tr>
<td>Präfixmuster des Pfads [<I>optional</I>]</td>
<td>Der Dateipfad, mit dem Ihre Dateien im angegebenen Data Lake-Speicherkonto geschrieben werden. <BR>{date}, {time}<BR>Beispiel 1: folder1/logs / {date} / {time}<BR>Beispiel 2: folder1/logs / {date}</td>
</tr>
<tr>
<td>Datumsformat [<I>optional</I>]</td>
<td>Wenn das date-Token im Pfadpräfix verwendet wird, können Sie das Datumsformat auswählen, unter dem die Dateien gespeichert werden. Beispiel: YYYY/MM/TT</td>
</tr>
<tr>
<td>Zeitformat [<I>optional</I>]</td>
<td>Wenn das time-Token im Pfadpräfix verwendet wird, können Sie das Zeitformat auswählen, unter dem die Dateien gespeichert werden. Der einzige derzeit unterstützte Wert ist HH</td>
</tr>
<tr>
<td>Ereignisserialisierungsformat</td>
<td>Das Serialisierungsformat für Ausgabedaten. Es werden JSON, CSV und Avro unterstützt.</td>
</tr>
<tr>
<td>Codieren</td>
<td>Beim CSV- oder JSON-Format muss eine Codierung angegeben werden. Das einzige derzeit unterstützte Codierungsformat ist UTF-8.</td>
</tr>
<tr>
<td>Trennzeichen</td>
<td>Gilt nur für die CSV-Serialisierung. Stream Analytics unterstützt eine Reihe von üblichen Trennzeichen zum Serialisieren der CSV-Daten. Unterstützte Werte sind Komma, Semikolon, Leerzeichen, Tabulator und senkrechter Strich.</td>
</tr>
<tr>
<td>Format</td>
<td>Gilt nur für die JSON-Serialisierung. "Separate Zeile" gibt an, dass die Ausgabe so formatiert wird, dass jedes JSON-Objekt in einer neuen Zeile enthalten ist. "Array" gibt an, dass die Ausgabe als Array aus JSON-Objekten formatiert wird.</td>
</tr>
</tbody>
</table>

## Erneuern der Data Lake-Speicherautorisierung

Derzeit besteht die Einschränkung, dass das Authentifizierungstoken alle 90 Tage manuell für sämtliche Aufträge mit der Data Lake-Speicherausgabe aktualisiert werden muss. Darüber hinaus muss Ihr Data Lake-Speicherkonto erneut authentifiziert werden, wenn Sie Ihr Kennwort seit der Erstellung oder letzten Authentifizierung Ihres Auftrags geändert haben. Ein Symptom dieses Problems ist, dass es keine Auftragsausgabe gibt, und ein Fehler in den Vorgangsprotokollen auftritt, der die erforderliche Neuautorisierung anzeigt.

Um dieses Problem zu beheben, halten Sie den laufenden Auftrag an, und wechseln Sie zu Ihrer Data Lake-Speicherausgabe. Klicken Sie auf „Autorisierung erneuern“, und für einen kurzen Zeitraum wird die Seite „Umleitung an die Autorisierung...“ angezeigt. Die Seite wird automatisch geschlossen und im Erfolgsfall wird „Autorisierung wurde erfolgreich erneuert“ angezeigt. Sie müssen dann unten auf der Seite auf „Speichern“ klicken und können fortfahren, indem Sie Ihren Auftrag von der letzten Beendigungszeit aus neu starten, um Datenverlust zu vermeiden.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.jpg)

<!---HONumber=AcomDC_0323_2016-->