<properties
	pageTitle="Verwenden Sie Azure-Sicherung auf Band-Infrastruktur ersetzen"
	description="Hier erfahren Sie, wie Azure-Sicherung Band-ähnliche Semantik können Sie zum Sichern und Wiederherstellen von Daten in Azure"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="03/27/2015"
	ms.author="prvijay"/>

# Verwenden Sie Azure-Sicherung auf Band-Infrastruktur ersetzen

Azure-Sicherung und System Center Data Protection Manager-Kunden können:

+ Backup-Daten in die Zeitpläne, die Anforderungen ihrer Organisation am besten entspricht.

+ Die Sicherungsdaten für längere Zeit beibehalten

+ Stellen Sie Azure ein Teil ihrer langfristigen Aufbewahrung (anstelle von Bändern benötigt).

In diesem Artikel wird erläutert, wie Kunden Sicherungs-und Beibehaltungsrichtlinie aktivieren können. Kunden, die Bänder zu verwenden, um ihre langfristigen-langfristige Speicherung von Adresse müssen jetzt eine leistungsstarke und geeignete Alternative durch die Verfügbarkeit dieses Features haben. Die Funktion in der neuesten Version der Azure-Sicherung aktiviert ist (das [hier](http://aka.ms/azurebackup_agent)). SCDPM Kunden müssten in UR5 verschieben, bevor Sie diese Funktion verwenden.

## Was ist der Zeitplan für die Sicherung?
Sicherungszeitplan gibt die Häufigkeit an (oder wie oft) des Sicherungsvorgangs z. B. die Einstellungen in der folgenden Bildschirm zeigt an, dass Sicherungen täglich um 18: 00 Uhr und um Mitternacht ausgeführt werden. <br/>

![Tagesplan][1]

Kunden können auch planen, eine wöchentliche Sicherung, z. B. die Einstellungen in der folgenden Bildschirm gibt an, dass Sicherungen alle alternativen Sonntag & Mittwoch 9:30 Uhr und 1 Uhr erstellt werden. <br/>

![Wöchentlicher Zeitplan][2]

## Was ist die Beibehaltungsrichtlinie?
Beibehaltungsrichtlinie gibt die Dauer, für die die Sicherung gespeichert werden muss. Anstatt nur "flat Policy" für alle backup-Punkte, können Kunden verschiedene Aufbewahrungsrichtlinien basierend darauf, wann die Sicherung angeben. Für z. B. am Ende jedes Quartals getroffen Sicherungsort möglicherweise beibehalten werden für eine längere Zeit zu Überwachungszwecken Sicherungsort täglich ausgeführt (zeigen Sie dient als eine betriebliche Recovery) 90 Tage lang beibehalten werden muss. <br/>

![Beibehaltungsrichtlinie][3]

Die Gesamtanzahl der "Retention Punkte" in dieser Richtlinie angegeben ist, 90 (täglich Punkte) + 40 (jeweils 10 Jahren Quartal) = 130.

## Beispiel – sowohl zusammenstellen
<br/> ![Beispielbildschirm][4]

1. **Tägliche Aufbewahrungsrichtlinie**: Sicherungen täglich, sieben Tage lang gespeichert sind.
2. **Wöchentlich Aufbewahrungsrichtlinie**: Sicherungen, die alle um Mitternacht und 18: 00 Uhr Samstag, werden vier Wochen beibehalten
3. **Monatliche Aufbewahrungsrichtlinie**: Sicherungen, die Uhr und 18: 00 Uhr am letzten Samstag jedes Monats werden für 12months beibehalten
4. **Jährlichen Aufbewahrungsrichtlinie**: Sicherungen, die um Mitternacht am letzten Samstag jeder März werden zehn Jahre lang beibehalten

Die Gesamtanzahl der "Retention Punkte" (Punkte, von dem ein Kunde kann Daten wiederherstellen) in der obigen Abbildung wird wie folgt berechnet:

+ 2 Punkte pro Tag und 7 Tage = 14 Recovery verweist.

+ 2 verweist pro Woche 4 Wochen = 8 Wiederherstellungspunkte

+ 2 verweist pro Monat für 12 Monate = 24 Wiederherstellungspunkte

+ 1 Punkt pro Jahr und Wiederherstellung von 10 Jahren = 10 Punkte

Die Gesamtzahl der Wiederherstellungspunkte beträgt 56.

## Erweiterte Konfiguration

Durch Klicken auf **Ändern** auf dem Bildschirm oben haben Kunden Flexibilität beim Angeben der Beibehaltungsdauer Zeitpläne noch weiter. <br/>

![Ändern][5]


<!--Image references-->
[1]: ./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png
[2]: ./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png
[3]: ./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png
[4]: ./media/backup-azure-backup-cloud-as-tape/samplescreen.png
[5]: ./media/backup-azure-backup-cloud-as-tape/modify.png

<!---HONumber=GIT-SubDir--> 