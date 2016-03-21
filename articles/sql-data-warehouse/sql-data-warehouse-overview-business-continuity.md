<properties
   pageTitle="Planen von Geschäftskontinuität in SQL Data Warehouse | Microsoft Azure"
   description="Übersicht über Geschäftskontinuität in SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="sahajs;barbkess;sonyama"/>


# Planen von Geschäftskontinuität in SQL Data Warehouse

Dieser Artikel erläutert die Grundlagen der Planung von Geschäftskontinuität und Notfallwiederherstellung mit SQL Data Warehouse. Data Warehouses sind das zentrale Informationsrepository für Unternehmen. Sie spielen eine wichtige Rolle in den täglichen Vorgängen für Analysen und Business Intelligence auf allen Ebenen der Organisation. Es ist daher wichtig, dass Ihr Data Warehouse zuverlässig ist und Wiederherstellbarkeit sowie fortlaufende Ausführung zulässt. Insbesondere erfahren Sie in diesem Artikel, wie Sie mit SQL Data Warehouse nach Benutzerfehlern und Notfällen Datenbankwiederherstellungen und geografische Wiederherstellungen ausführen können.

## Konzepte

Geschäftskontinuität- und Notfallwiederherstellungspläne müssen für folgende Parameter optimiert sein:

**Recovery Time Objective (RTO)**: die maximal akzeptable Zeitspanne, bevor die Datenbank nach einem Ausfall vollständig wiederhergestellt ist, d. h. der maximale Verlust von Verfügbarkeit bei Ausfällen.

**Recovery Point Objective (RPO)**: das maximal akzeptable Zeitfenster verlorener Updates, während die Datenbank nach einem Ausfall wiederhergestellt wird, d. h. der maximale Verlust von Daten bei Ausfällen.

**Estimated Recovery Time (ERT)**: die geschätzte Dauer bis zur vollständigen Funktion der Datenbank nach einer Wiederherstellungsanforderung.

## Szenarios für die Geschäftskontinuität

**Wiederherstellung bei Fehlern in der Infrastruktur**: Dieses Szenario bezieht sich auf das Wiederherstellen nach Infrastrukturproblemen wie Datenträgerfehlern usw. Ein Kunde möchte Geschäftskontinuität mit einer fehlertoleranten und hoch verfügbaren Infrastruktur gewährleisten.

**Wiederherstellung nach Benutzerfehlern**: Dieses Szenario bezieht sich auf die Wiederherstellung nach unbeabsichtigten oder beiläufigen Datenbeschädigungen oder -löschungen. Ein Kunde möchte für den Fall, dass ein Benutzer versehentlich oder beiläufig Daten ändert oder löscht, Geschäftskontinuität durch Wiederherstellen der Datenbank zu einem früheren Zeitpunkt sicherstellen.

**Wiederherstellung nach Datenverlusten (DR)**: Dieses Szenario bezieht sich auf das Wiederherstellen nach einer Katastrophe. In einem Szenario, in dem ein Ereignis wie z. B. eine Naturkatastrophe oder ein regionaler Ausfalls bewirkt, dass die Datenbank nicht mehr verfügbar ist, möchte ein Kunde die Datenbank in einer anderen Region wiederherstellen, um den Geschäftsbetrieb aufrecht erhalten zu können.


## Funktionen der Geschäftskontinuität

Wir untersuchen nun, wie SQL Data Warehouse in den oben genannten Szenarios die Zuverlässigkeit Ihrer Datenbank erhöht und die Wiederherstellbarkeit und die fortlaufende Ausführung des Systems ermöglicht.


### Datenredundanz

Da SQL Data Warehouse Computer und Speicher trennt, werden alle Ihre Daten direkt georedundant in Azure Storage (RA-GRS) geschrieben. Georedundante Speicher repliziert Ihre Daten in eine sekundäre Region, die Hunderte von Kilometern von der primären Region entfernt ist. In primären und sekundären Regionen werden Ihre Daten jeweils dreimal in separaten Fehler- und Upgradedomänen repliziert. Dadurch wird sichergestellt, dass Ihre Daten selbst bei vollständigen regionalen Ausfällen oder bei Notfällen, in denen eine Region nicht zur Verfügung steht, erhalten bleiben. Weitere Informationen zu georedundantem Speicher mit Lesezugriff finden Sie unter [Redundanzoptionen für Azure-Speicher][].

### Datenbankwiederherstellung

Mit der Datenbankwiederherstellung können Sie die Datenbank in den Zustand zu einem früheren Zeitpunkt wiederherstellen. Der Azure SQL Data Warehouse-Dienst schützt alle Datenbanken durch automatische Speichermomentaufnahmen mindestens alle acht Stunden, die sieben Tage lang aufbewahrt werden und einen diskreten Satz von Wiederherstellungspunkten für Sie bereitstellen. Diese Sicherungen werden in Azure Storage in RA-GRS gespeichert und sind daher standardmäßig georedundant. Die automatischen Funktionen zur Sicherung und Wiederherstellung sind nicht mit zusätzlichen Kosten verbunden und bieten eine kosten- und verwaltungsfreie Möglichkeit, Datenbanken vor versehentlicher Beschädigung oder Löschung zu schützen. Weitere Informationen zur Datenbankwiederherstellung finden Sie unter [Wiederherstellung nach Benutzerfehlern][].

### Geografische Wiederherstellung

Die geografische Wiederherstellung soll die Datenbank wiederherstellen, wenn sie aufgrund eines Ausfalls nicht mehr zur Verfügung steht. Sie können sich zum Wiederherstellen einer Datenbank von einer georedundanten Sicherung an den Support wenden, um eine neue Datenbank in einer beliebigen Azure-Region zu erstellen. Da die Sicherung georedundant ist, kann mit ihr eine Datenbank selbst dann wiederhergestellt werden, wenn sie aufgrund eines Ausfalls nicht mehr verfügbar ist. Das Feature zur geografischen Wiederherstellung ist nicht mit zusätzlichen Kosten verbunden.


## Nächste Schritte
Informationen zu den Geschäftskontinuitätsfeatures anderer SQL-Datenbank-Editionen finden Sie in der [SQL-Datenbank-Übersicht zu Geschäftskontinuität][].

<!--Image references-->

<!--Article references-->
[business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md
[Redundanzoptionen für Azure-Speicher]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[SQL-Datenbank-Übersicht zu Geschäftskontinuität]: ../sql-database/sql-database-business-continuity.md
[Wiederherstellung nach Benutzerfehlern]: sql-data-warehouse-business-continuity-recover-from-user-error.md

<!--MSDN references-->
[Create database restore request]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: http://msdn.microsoft.com/library/azure/dn509562.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0309_2016-->