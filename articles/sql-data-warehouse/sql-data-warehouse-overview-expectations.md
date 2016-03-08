<properties
   pageTitle="Erwartungen an SQL Data Warehouse Preview | Microsoft Azure"
   description="Übersicht über die Funktionen der öffentlichen Preview-Version und Ziele in Hinblick auf die allgemeine Verfügbarkeit von SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/25/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Erwartungen an SQL Data Warehouse Preview

Dieser Artikel beschreibt die Funktionen von SQL Data Warehouse Preview und erläutert, welche Ziele wir mit dem Dienst in Hinblick auf die allgemeine Verfügbarkeit verfolgen. Diese Informationen werden bei Funktionsverbesserungen der öffentlichen Preview-Version fortlaufend aktualisiert.

Unsere Ziele für SQL Data Warehouse:

- Vorhersagbare Leistung und lineare Skalierbarkeit für Daten bis zum Petabytebereich
- Hohe Zuverlässigkeit für alle Data Warehouse-Vorgänge, unterstützt durch SLA (Service Level Agreement)

Wir arbeiten unablässig an der Umsetzung dieser Ziele, bevor wir SQL Data Warehouse allgemein verfügbar machen.

## Vorhersagbare und skalierbare Leistung

Azure SQL Data Warehouse stellt erstmalig Data Warehouse-Einheiten (DWUs) als Möglichkeit zum Messen von Computerressourcen (CPUs, Arbeitsspeicher, Speicher-E/A) für das Data Warehouse bereit. Je höher die Anzahl der DWUs, desto größer die Ressourcen. Mit zunehmender Anzahl von DWUs führt SQL Data Warehouse Vorgänge parallel (z. B. Laden von Daten oder Abfrage) über stärker verteilte Ressourcen aus. Dadurch verringert sich die Latenz, und die Leistung verbessert sich.

Für jedes Data Warehouse gibt es zwei grundlegende Messwerte für die Leistung:

- Auslastungsrate. Die Anzahl der Datensätze, die pro Sekunde in das Data Warehouse geladen werden können. Wir messen speziell die Anzahl der Datensätze, die über PolyBase aus dem Azure-Blob-Speicher in eine Tabelle mit einem gruppierten Spaltenspeicherindex importiert werden können. 
- Scanrate. Die Anzahl der Datensätze, die pro Sekunde sequenziell aus dem Data Warehouse abgerufen werden können. Wir messen speziell die Anzahl der Datensätze, die durch die Abfrage eines gruppierten Spaltenspeicherindexes zurückgegeben werden.


Wir haben einige wichtige Leistungsverbesserungen gemessen und werden die voraussichtlichen Raten in Kürze mitteilen. Während der Preview nehmen wir fortlaufend Verbesserungen (z. B. an Komprimierung und Caching) vor, um diese Raten zu erhöhen und eine vorhersagbare Skalierung sicherzustellen.


## Hohe Zuverlässigkeit, unterstützt durch SLA

### Datenschutz 

SQL Data Warehouse speichert alle Daten mithilfe von georedundanten Blobs im Azure-Speicher. Drei synchrone Kopien der Daten werden in der lokalen Azure-Region behalten, um transparenten Datenschutz bei lokalen Ausfällen (z. B. Speicherlaufwerksausfälle) sicherzustellen. Darüber hinaus werden drei weitere asynchrone Kopien in einer Azure-Remoteregion zum Schutz von Daten bei einem regionalen Ausfall (Notfallwiederherstellung) beibehalten. Lokale und Remoteregionen werden zusammen verwendet, um akzeptable Synchronisierungslatenzen (z. B. Osten der USA und Westen der USA) zu erreichen.


### Backups

Azure SQL Data Warehouse sichert sämtliche Daten mindestens alle 8 Stunden mit Azure-Speichermomentaufnahmen. Diese Momentaufnahmen werden 7 Tage lang aufbewahrt. Dadurch können Daten an bis zu 21 Punkten innerhalb der letzten 7 Tage bis zu dem Zeitpunkt wiederhergestellt werden, zu dem die letzte Momentaufnahme erstellt wurde. Mithilfe von PowerShell oder REST-APIs können Sie die Daten aus einer Momentaufnahme wiederherstellen.

Momentaufnahmen werden asynchron in eine Azure-Remoteregion kopiert, um im Falle eines regionalen Ausfalls (Notfallwiederherstellung) eine noch bessere Wiederherstellbarkeit zu erzielen.


### Durchführen der Abfrage 

SQL Data Warehouse speichert die Daten auf einem oder mehreren Serverknoten, auf denen jeweils einige Benutzerdaten gespeichert sind, über die die Abfrageausführung für diese Daten gesteuert wird. Im Rahmen der massiven parallelen Verarbeitungsarchitektur (MPP-Architektur) werden die Abfragen parallel über die Serverknoten hinweg ausgeführt. SQL Data Warehouse erkennt und minimiert automatisch Ausfälle von Serverknoten. Während der Preview besteht jedoch die Möglichkeit, dass ein Vorgang (z. B. Laden von Daten oder Abfrage) aufgrund des Ausfalls einzelner Knoten fehlschlägt. Während der Preview nehmen wir fortlaufend Verbesserungen vor, damit Vorgänge trotz Knotenausfällen erfolgreich abgeschlossen werden können.


## Nächste Schritte

[Erste Schritte][] mit der öffentlichen Preview-Version.

<!--Image references-->

<!--Article references-->
[Erste Schritte]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0302_2016-->