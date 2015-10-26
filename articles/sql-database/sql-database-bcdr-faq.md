<properties 
   pageTitle="Häufig gestellte Fragen zur Geschäftskontinuität in SQL-Datenbanken" 
   description="Allgemeine Fragen und Antworten, die Kunden über integrierte und optionale Funktionen für Geschäftskontinuität und Notfallwiederherstellung mit Azure SQL-Datenbank stellen." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/14/2015"
   ms.author="elfish"/>

# Häufig gestellte Fragen zur Geschäftskontinuität

## 1\. Was geschieht mit meiner Aufbewahrungsdauer für den Wiederherstellungspunkt, wenn ich einen Downgrade/Upgrade auf Dienstebene durchführe?
Nach einem Downgrade auf eine niedrigere Leistungsebene wird die Aufbewahrungsdauer für den Wiederherstellungspunkt unverzüglich auf die Aufbewahrungsdauer der Leistungsebene der aktuellen Datenbank verkürzt.

Wenn ein Upgrade der DB-Dienstebene durchgeführt wird, beginnt die Verlängerung der Aufbewahrungsdauer erst nach dem Upgrade der Datenbank.

Wenn die DB beispielsweise von P1 auf S3 herabgestuft wird, wird die Aufbewahrungsdauer sofort von 35 Tagen auf 14 Tage geändert und alle Wiederherstellungspunkte mit einem Alter über 14 Tagen sind nicht mehr verfügbar. Wenn in der Folge wieder ein Upgrade auf P1 erfolgt, beginnt die Aufbewahrungsdauer bei 14 Tagen und erweitert sich nach und nach auf 35 Tage.

## 2\. Wie lang ist die Aufbewahrungsdauer für eine gelöschte Datenbank? 
Die Aufbewahrungsdauer wird anhand der Dienstebene der Datenbank, während sie vorhanden war, oder der Anzahl der Tage, die sie vorhanden war, berechnet, je nachdem, welcher Wert kleiner ist.

## 3\. Wie kann ich einen gelöschten Server wiederherstellen?

Derzeit wird das Wiederherstellen gelöschter Server nicht unterstützt.

## 4\. Wie lange dauert es, eine Datenbank wiederherzustellen?

Die benötigte Dauer zum Wiederherstellen einer DB hängt von mehreren Faktoren wie z. B. der Größe der Datenbank, der Anzahl der Transaktionsprotokolle, der Netzwerkbandbreite usw. ab. Der Großteil der Datenbankwiederherstellungen erfolgt innerhalb von 12 Stunden.

## 5\. Kann ich die Aufbewahrungsdauer für den Wiederherstellungspunkt meiner Datenbank ändern?

Nein.

## 6\. Wie finde ich den verfügbaren Wiederherstellungspunkt für meine Datenbank heraus?

Für die Wiederherstellung nach Benutzerfehlern ist die aktuelle Zeit der neueste verfügbare Wiederherstellungspunkt. Um den ältesten verfügbaren Wiederherstellungspunkt herauszufinden, rufen Sie mit [Get Database](https://msdn.microsoft.com/library/dn505708.aspx) (*RecoveryPeriodStartDate*) den ältesten Wiederherstellungspunkt (nicht geografisch replizierter Wiederherstellungspunkt) ab.

Verwenden Sie für die Wiederherstellung nach Ausfällen [Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) zum Abrufen des neuesten geografisch replizierten Wiederherstellungspunkts.

## 7\. Wie kann ich eine Massenwiederherstellung von Datenbanken unter meinem Server durchführen?

Es gibt keine integrierte Funktion für Massenwiederherstellungen. Sie können das Skript [Azure SQL Database: Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) zum Ausführen dieser Aufgabe verwenden.

## 8\. Was ist der Unterschied zwischen standardmäßiger Georeplikation und aktiver Georeplikation?

Für die standardmäßige Georeplikation ist die sekundäre Datenbank nicht lesbar. Sie ist nur für Failover bei Ausfällen verfügbar.

Für aktive Georeplikation können alle sekundären Datenbanken (bis zu vier sekundäre Datenbanken) gelesen werden.

## 9\. Was ist die Replikationsverzögerung bei der standardmäßigen Georeplikation bzw. der aktiven Georeplikation?

Die Georeplikation verwendet fortlaufende Kopien. Verwenden Sie daher die dynamische Verwaltungsansicht (DMV) [sys.dm\_continuous\_copy\_status](https://msdn.microsoft.com/library/azure/dn741329.aspx), um den Zeitpunkt der letzten Replikation und andere Informationen zu erhalten.




 

<!---HONumber=Oct15_HO3-->