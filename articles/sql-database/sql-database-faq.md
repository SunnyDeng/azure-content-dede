<properties 
   pageTitle="Azure SQL-Datenbank – Häufig gestellte Fragen" 
   description="Antworten auf häufig gestellte Fragen von Kunden zu Cloud-Datenbanken, zur Azure SQL-Datenbank, das Microsoft Managementsystem für relationale Datenbanken (RDBMS) und Database as a Service in der Cloud." 
   services="sql-database" 
   documentationCenter="" 
   authors="jeffgoll" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="11/11/2015"
   ms.author="jeffreyg"/>

# SQL-Datenbank – Häufig gestellte Fragen

## Wie wird die Nutzung der SQL-Datenbank auf meiner Rechnung ausgewiesen? 
Die Abrechnung der SQL-Datenbank erfolgt nach einer vorhersehbaren Stundenpauschale, basierend auf Dienst- und Leistungsebene für Einzeldatenbanken oder edTUs pro elastischen Datenbankpool. Der tatsächliche Verbrauch wird stündlich berechnet und anteilig aufgeführt, sodass auf Ihrer Rechnung auch Bruchteile einer Stunde auftauchen können. Wenn eine Datenbank z. B. innerhalb eines Monats 12 Stunden existiert, wird auf Ihrer Rechnung eine Nutzung von 0,5 Tagen aufgeführt. Zudem sind die Dienst- und Leistungsebenen sowie eDTUs pro Pool in der Rechnung aufgeschlüsselt, um die Anzahl der Datenbanktage einfacher zu erkennen, die jeweils in einem Monat angefallen sind.

## Was geschieht, wenn eine Einzeldatenbank weniger als eine Stunde aktiv ist oder für weniger als eine Stunde eine höhere Dienstebene verwendet?
Die Abrechnung erfolgt für jede Stunde, in der eine Datenbank die höchste in dieser Stunde angewandte Dienst- und Leistungsebene nutzt – unabhängig von der Verwendung der Datenbank und ob sie weniger als eine Stunde aktiv war. Falls Sie beispielsweise eine Einzeldatenbank erstellen und sie 5 Minuten später löschen, wird Ihnen eine volle Datenbankstunde in Rechnung gestellt.

Beispiele
	
- Wenn Sie eine Basic-Datenbank erstellen und unmittelbar danach auf Standard S1 aktualisieren, wird Ihnen für die erste Stunde der Standard S1-Satz berechnet.

- Wenn Sie um 22:00 Uhr für eine Datenbank ein Upgrade von Basic auf Premium durchführen und das Upgrade um 1:35 Uhr am nächsten Tag abgeschlossen ist, wird Ihnen der Premium-Satz erst ab 1:00 Uhr an dem Tag in Rechnung gestellt, an dem das Upgrade abgeschlossen ist.

- Wenn Sie um 11:00 Uhr für eine Datenbank ein Downgrade von Premium auf Basic durchführen und das Downgrade um 14:15 Uhr abgeschlossen ist, wird Ihnen der Premium-Satz bis 15:00 Uhr berechnet. Danach erfolgt die Abrechnung zum Basic-Satz.

## Wie wird die Nutzung des elastischen Datenbankpools auf meiner Rechnung ausgewiesen, und was passiert, wenn ich eDTUs pro Pool ändere?
Die Abrechnung der Gebühren für den elastischen Datenbankpool erfolgt auf Ihrer Rechnung als elastische DTUs (eDTUs) in den Schritten, die unter den eDTUs pro Pool auf [der Preisseite](https://azure.microsoft.com/pricing/details/sql-database/) ausgewiesen sind. Für elastische Datenbankpools erfolgt keine Abrechnung pro Datenbank. Die Abrechnung erfolgt für jede Stunde, in der ein Pool im höchsten eDTU existiert, unabhängig von der Verwendung oder ob der Pool weniger als eine Stunde aktiv war.

Beispiele

- Wenn Sie um 11:18 Uhr einen elastischen Datenbankpool mit 200 eDTUs erstellen und dann fünf Datenbanken zum Pool hinzufügen, werden Ihnen ab 11:00 Uhr für den restlichen Tag 200 eDTUs berechnet.
- An Tag 2 nutzt Datenbank 1 ab 5:05 Uhr den ganzen Tag über 50 eDTUs. Die Nutzung der Datenbanken 2-5 schwankt zwischen 0 und 80 eDTUs. Im Laufe des Tages fügen Sie fünf weitere Datenbanken hinzu, die im Laufe des Tages verschiedene eDTUs nutzen. An Tag 2 werden den ganzen Tag über 200 eDTU berechnet. 
- An Tag 3 fügen Sie um 5 Uhr weitere 15 Datenbanken hinzu. Die Nutzung der Datenbank steigt im Laufe des Tages, bis Sie um 20:05 Uhr entscheiden, die eDTUs für den Pool von 200 auf 400 aufzustocken. Bis 20.00 Uhr werden Ihnen die Gebühren für 200 eDTUs berechnet. Für die verbleibenden 4 Stunden des Tages erfolgt die Abrechnung für 400 eDTUs. 

## Wie wird die Nutzung der Georeplikation in einem elastischen Datenbankpool auf meiner Rechnung ausgewiesen?
Im Gegensatz zu Einzeldatenbanken hat die Nutzung der GEO-DR mit elastischen Datenbanken keine direkte Auswirkung auf die Abrechnung. Ihnen werden nur die für die einzelnen Pools (primärer Pool und sekundärer Pool) bereitgestellten eDTUs in Rechnung gestellt.

## Wie wirkt sich die Nutzung der Überwachungsfunktion auf meine Rechnung aus? 
Die Überwachungsfunktion ist kostenfrei in den SQL-Datenbankdienst integriert und für Basic-, Standard- und Premium-Datenbanken verfügbar. Um die Überwachungsprotokolle zu speichern, nutzt die Überwachungsfunktion ein Azure-Speicherkonto, und die Sätze für Tabellen und Warteschlangen im Azure-Speicher werden abhängig von der Größe Ihres Überwachungsprotokolls angewandt.

## Wie finde ich die richtige Dienst- und Leistungsebene für einzelne Datenbanken und elastische Datenbankpools? 
Ihnen stehen einige Tools zur Verfügung.

- Verwenden Sie für lokale Datenbanken den [DTU Sizing Advisor](http://dtucalculator.azurewebsites.net/), der erforderliche Datenbanken und DTUs empfiehlt und mehrere Datenbanken für flexible Datenbankpools bewertet.
- Wenn eine Einzeldatenbank von einem Pool profitieren würde, empfiehlt das intelligente Modul von Azure einen elastischen Datenbankpool, wenn es ein passendes Verwendungsmuster erkennt. Ausführliche Informationen finden Sie unter [Empfohlene Pools für elastische Datenbanken](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools). Weitere Informationen für eigene Berechnungen finden Sie unter [Überlegungen zum Preis und zur Leistung für einen elastischen Datenbankpool](sql-database-elastic-pool-guidance.md).
- Ob ein Upgrade oder Downgrade für Ihre Einzeldatenbank erforderlich ist, erfahren Sie im [Leitfaden zur Leistung für einzelne Datenbanken](sql-database-performance-guidance.md).

## Wie oft kann ich die Dienst- oder Leistungsebene einer Einzeldatenbank ändern? 
Sie können die Dienstebene (zwischen Basic, Standard und Premium) oder die Leistungsebene innerhalb einer Dienstebene (z. B. S1 in S2) über einen Zeitraum von 24 Stunden insgesamt viermal ändern.

##Wie oft kann ich die eDTUs pro Pool anpassen? 
Einmal pro Tag.

## Wie lange dauert es, die Dienst- oder Leistungsebene einer Einzeldatenbank zu ändern oder eine Datenbank in einen elastischen Datenbankpool oder aus solch einem Pool heraus zu verschieben? 
Um die Dienstebene einer Datenbank zu ändern und sie in einen Pool und aus einem Pool heraus zu verschieben, muss die Datenbank als Hintergrundvorgang auf die Plattform kopiert werden. Je nach Größe der Datenbanken kann dieser Vorgang wenige Minuten oder mehrere Stunden dauern. In beiden Fällen bleiben die Datenbanken während des Verschiebens online und verfügbar. Weitere Informationen zum Ändern von Einzeldatenbanken finden Sie unter [Ändern der Dienstebene einer Datenbank](sql-database-scale-up.md). Informationen zu elastischen Datenbanken finden Sie in der [Referenz für elastische Datenbankpools](sql-database-elastic-pool-reference.md#latency-of-elastic-pool-operations)

##Wann sollten Einzeldatenbanken elastischen Datenbanken vorgezogen werden? 
Elastische Datenbankpools sind generell auf herkömmliche SaaS-Anwendungsmuster (Software-as-a-Service, SaaS) ausgelegt, wenn eine Datenbank pro Kunde oder Mandant vorhanden ist. Das Beschaffen einzelner Datenbanken und eine Überversorgung zum Erfüllen von variierende Anforderungen und Spitzenanforderungen jeder Datenbank ist häufig nicht wirtschaftlich. Mit Pools verwalten Sie die gesamte Leistung des Pools, und die Datenbanken werden automatisch nach oben und unten skaliert.

Das intelligente Modul von Azure empfiehlt einen Pool für Datenbanken, wenn es ein entsprechendes Nutzungsmuster erkennt. Ausführliche Informationen finden Sie unter [Empfohlene Pools für elastische Datenbanken](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools). Ausführliche Informationen zur Auswahl zwischen Einzeldatenbanken und elastischen Datenbanken finden Sie unter [Überlegungen zum Preis und zur Leistung eines Pools für elastische Datenbanken.](sql-database-elastic-pool-guidance.md).

## Was bedeutet es, bis zu 200 % des maximal bereitgestellten Datenbankspeichers zur Sicherung zur Verfügung zu haben? 
Der Sicherungsspeicher ist der Speicher, der mit Ihren automatisierten Datenbanksicherungen assoziiert ist, die für Zeitpunktwiederherstellung und Geowiederherstellung verwendet werden. Microsoft Azure SQL-Datenbanken bieten bis zu 200 % Ihres maximal bereitgestellten Sicherungsdatenbankspeichers ohne zusätzliche Kosten. Wenn Sie z. B. über eine Standard-DB-Instanz mit einer bereitgestellten Größe von 250 GB verfügen, werden Ihnen ohne zusätzliche Kosten 500 GB Sicherungsspeicher bereitgestellt. Wenn die maximale Größe Ihres bereitgestellten Sicherungsspeichers überschritten wird, können Sie sich entweder an den Azure-Support wenden, um den Aufbewahrungszeitraum zu verkürzen, oder zusätzlichen Sicherungsspeicher erwerben, für den die standardmäßigen RA-GRS-Gebühren (Read-Access Geographically Redundant Storage) anfallen. Weitere Informationen zur RA-GRS-Abrechnung finden Sie in der Preisübersicht für Speicher.

## Was muss ich beim Umstieg von Web/Business auf die neuen Dienstebenen beachten?
Azure SQL Web und Business-Datenbanken wurden eingestellt. Die Dienstebenen Basic, Standard, Premium und Elastic ersetzen die eingestellten Web- und Business-Datenbanken. Zu Ihrer Unterstützung im Übergangszeitraum haben wir die häufig gestellten Fragen ergänzt. [Häufig gestellte Fragen zur Einstellung von Web Edition und Business Edition](sql-database-web-business-faq.md)

<!---HONumber=Nov15_HO3-->