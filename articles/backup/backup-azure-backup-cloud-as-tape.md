<properties
   pageTitle="Verwenden von Azure Backup als Ersatz für Ihre Bandinfrastruktur | Microsoft Azure"
   description="Hier erfahren Sie, wie Azure Backup eine Semantik ähnlich wie bei Bändern bereitstellt, damit Sie Ihre Daten in Azure sichern und wiederherstellen können."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""/>
<tags  ms.service="backup" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="storage-backup-recovery" ms.date="12/15/2015" ms.author="jimpark"; "aashishr"; "sammehta"/>

# Verwenden von Azure Backup als Ersatz für Ihre Bandinfrastruktur
Azure Backup- und System Center Data Protection Manager-Kunden haben folgende Möglichkeiten: - Sichern von Daten in Zeitplänen, welche am besten ihren Organisationsanforderungen entsprechen - Längeres Aufbewahren der Sicherungsdaten - Integrieren von Azure in ihre Anforderungen zur langfristigen Aufbewahrung (anstelle von Bändern)

In diesem Artikel wird erläutert, wie Kunden Sicherungs- und Aufbewahrungsrichtlinien aktivieren können. Kunden, die für ihre Anforderungen an eine langfristige Aufbewahrung Bänder verwenden, steht jetzt dank dieses Features eine leistungsstarke und geeignete Alternative zur Verfügung. Das Feature ist in der neuesten Version von Azure Backup aktiviert (die [hier](http://aka.ms/azurebackup_agent) erhältlich ist). SCDPM-Kunden müssen zunächst auf UR5 umstellen, bevor sie dieses Feature verwenden können.

## Was ist ein Sicherungszeitplan?
Der Sicherungszeitplan gibt die Häufigkeit des Sicherungsvorgangs an. Die Einstellungen auf dem folgenden Bildschirm geben beispielsweise an, dass Sicherungen täglich um 18: 00 Uhr und um Mitternacht ausgeführt werden.

![Täglicher Zeitplan](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Kunden können auch eine wöchentliche Sicherung planen. Die Einstellungen auf dem folgenden Bildschirm geben beispielsweise an, dass Sicherungen jeden zweiten Sonntag und Mittwoch um 9:30 Uhr und um 1 Uhr erstellt werden.

![Wöchentlicher Zeitplan](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## Worum handelt es sich bei der Aufbewahrungsrichtlinie?
Die Aufbewahrungsrichtlinie gibt an, wie lange die Sicherung gespeichert werden muss. Anstatt nur eine "flache Richtlinie" für alle Sicherungspunkte anzugeben, können Kunden je nach Sicherungszeitpunkt verschiedene Aufbewahrungsrichtlinien festlegen. Beispiel: Der Sicherungspunkt am Ende jedes Quartals muss für Audits möglicherweise länger aufbewahrt werden als der tägliche Sicherungspunkt (der als Wiederherstellungspunkt für den alltäglichen Betrieb dient), der 90 Tage lang aufbewahrt werden muss.

![Aufbewahrungsrichtlinie](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Die Gesamtanzahl der in dieser Richtlinie angegebenen "Aufbewahrungspunkte" ist 90 (tägliche Punkte) + 40 (einer pro Quartal für 10 Jahre) = 130.

## Beispiel – Kombination aus beidem

![Beispielbildschirm](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Tägliche Aufbewahrungsrichtlinie**: Täglich erstellte Sicherungen werden sieben Tage lang gespeichert.
2. **Wöchentliche Aufbewahrungsrichtlinie**: Sicherungen, die jeden Tag um Mitternacht und jeden Samstag um 18 Uhr erstellt werden, werden vier Wochen lang aufbewahrt.
3. **Monatliche Aufbewahrungsrichtlinie**: Sicherungen, die am letzten Samstag im Monat um Mitternacht und um 18:00 Uhr erstellt werden, werden zwölf Monate lang aufbewahrt.
4. **Jährliche Aufbewahrungsrichtlinie**: Sicherungen, die am letzten Samstag im März um Mitternacht erstellt werden, werden zehn Jahre lang aufbewahrt.

Die Gesamtanzahl der "Aufbewahrungspunkte" (Punkte, von denen ein Kunde Daten wiederherstellen kann) in der obigen Abbildung wird wie folgt berechnet:

- 2 Punkte pro Tag für 7 Tage = 14 Wiederherstellungspunkte
- 2 Punkte pro Woche für 4 Wochen = 8 Wiederherstellungspunkte
- 2 Punkte pro Monat für 12 Monate = 24 Wiederherstellungspunkte
- 1 Punkt pro Jahr für 10 Jahre = 10 Wiederherstellungspunkte

Die Gesamtzahl der Wiederherstellungspunkte beträgt 56.

> [AZURE.NOTE]In Azure Backup gibt es keine Beschränkung der Anzahl der Wiederherstellungspunkte.

## Erweiterte Konfiguration
Durch Klicken auf **Ändern** im Bildschirm oben können Kunden noch flexiblere Aufbewahrungszeitpläne angeben.

![Ändern](./media/backup-azure-backup-cloud-as-tape/modify.png)

## Nächste Schritte
Weitere Informationen zu Azure Backup finden Sie unter

- [Einführung in Azure Backup](backup-introduction-to-azure-backup.md)
- [Azure Backup testen](backup-try-azure-backup-in-10-mins)

<!---HONumber=AcomDC_1217_2015-->