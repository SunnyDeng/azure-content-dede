<properties 
	pageTitle="Shard-Elastizität" 
	description="Erklärt Konzepte und stellt Beispiele für die Shard-Elastizität bereit, der Möglichkeit des einfachen horizontalen Hochskalierens von Azure SQL-Datenbanken." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="sidneyh"/>

# Shard-Elastizität 

**Shard-Elastizität** ermöglicht es Anwendungsentwicklern, Datenbankressourcen dem Bedarf entsprechend dynamisch zu erweitern oder zu reduzieren, sodass die Anwendungsleistung optimiert und die Kosten minimiert werden können. Die Kombination aus **Tools für elastische Datenbanken** für die Azure SQL-Datenbank und den [Dienstebenen „Basic“, „Standard“ und „Premium“](sql-database-service-tiers) liefert sehr bestechende Elastizitätsszenarios. Die Tools für elastische Datenbanken ermöglichen die horizontale Skalierung, ein Entwurfsmuster, bei dem Datenbanken (sogenannte [Shards](sql-database-elastic-scale-glossary.md)) einer Shard-Gruppe hinzugefügt oder daraus entfernt werden, um die Kapazität zu vergrößern oder zu verringern. Auf ähnliche Weise stellen die Dienstebenen von SQL-Datenbank Funktionen zur **vertikalen Skalierung** bereit, sodass die Ressourcen einer einzigen Datenbank dem Bedarf entsprechend skaliert werden können. Die Kombination von vertikaler Skalierung einzelner Shards und horizontaler Skalierung vieler Shards bietet Anwendungsentwicklern eine sehr flexible Umgebung, die sich den Anforderungen an Leistung, Kapazität und Kostenoptimierung entsprechend skalieren lässt.

Mit den neu eingeführten **Pools für elastische Datenbanken** ist die vertikale Skalierung sogar noch einfacher zu erreichen. Pools ermöglichen es, den Ressourcenverbrauch einer einzelnen Datenbank *automatisch* innerhalb eines Budgets für den gesamten Pool zu vergrößern oder zu verkleinern. Für Anwendungen, die nicht die Vorteile von Pools für elastische Datenbanken nutzen, werden in diesem Artikel andere Techniken zum Implementieren von richtlinienbasierten Mechanismen für die Verwaltung der vertikalen Skalierung sowie einige allgemeine Szenarios für die Automatisierung von Vorgängen zur horizontalen Skalierung beschrieben.

## Beispiele für die horizontale Skalierung: Concert Spike

Ein kanonisches Szenario für die horizontale Skalierung ist eine Anwendung, die Transaktionen für Konzertkarten verarbeitet. Bei einem normalen Kundenaufkommen nutzt die Anwendung minimale Datenbankressourcen zum Verarbeiten von Einkaufstransaktionen. Wenn allerdings der Verkauf von Karten für ein beliebtes Konzert beginnt, lässt sich der starke Anstieg in der Kundennachfrage nicht mit einer einzigen Datenbank handhaben.

Um den enormen Zuwachs an Transaktionen verarbeiten zu können, wird die Anwendung horizontal skaliert. Die Anwendung kann dann die Transaktionslast auf viele Shards verteilen. Wenn die zusätzlichen Ressourcen nicht mehr benötigt werden, wird die Datenbankebene auf die normale Nutzung reduziert. Hier ermöglicht es die horizontale Skalierung, dass Anwendungen der Kundennachfrage entsprechend skaliert werden.

## Beispiel für die vertikale Skalierung: Telemetrie

Ein kanonisches Szenario für die vertikale Skalierung ist eine Anwendung, die eine **Shard-Gruppe** zum Speichern geschäftlicher Telemetrie verwendet. In diesem Szenario ist es besser, alle Telemetriedaten für einen Tag in einer einzigen Shard unterzubringen. In dieser Anwendung werden die Daten für den aktuellen Tag in eine Shard aufgenommen, und für die nachfolgenden Tage werden jeweils neue Shards bereitgestellt. Die Betriebsdaten können dann nach Bedarf archiviert oder abgefragt werden.

Um die Telemetriedaten bei hohen Auslastungen aufnehmen zu können, ist es besser, eine höhere Dienstebene zu verwenden. Mit anderen Worten, eine Premium-Datenbank ist besser als eine Basic-Datenbank. Sobald die Datenbank ihre Kapazität erreicht, wird von der Erfassung auf Analyse und Berichtserstellung umgestellt. Die Leistung der Standard-Ebene entspricht daher der Aufgabe. Man kann die Dienstebene für Shards (außer für die aktuell erstellte Shard) vertikal herunterskalieren, um den geringeren Leistungsanforderungen älterer Daten Rechnung zu tragen.

Die vertikale Skalierung kann auch zur Steigerung der Leistung einer einzelnen Datenbank genutzt werden, um eine höhere Leistung zu erzielen. Ein Beispiel hierfür ist eine Anwendung für Steuererklärungen. Wenn die Steuererklärungen erstellt werden müssen, ist es besser, alle Daten eines Kunden in derselben Datenbank zu verarbeiten und die Leistung dieser Shard zu steigern. Je nach Anwendung, kann das vertikale Hoch- oder Herunterskalieren von Ressourcen sowohl zur Kosten- als auch zur Leistungsoptimierung vorteilhaft sein.

Horizontale und vertikale Skalierung der Datenbankebene bilden den Kern der Anwendungsskalierbarkeit und von Shard-Elastizität.

Dieses Dokument liefert den Kontext für Shard-Elastizitätsszenarien sowie Verweise auf Beispielimplementierungen von vertikaler und horizontaler Skalierung.

## Funktionsweise der Shard-Elastizität 

Vertikale und horizontale Skalierung sind eine Funktion von drei grundlegenden Komponenten:

1. **Telemetrie**
2. **Regel**
3. **Aktion**   

## Telemetrie

**Datengesteuerte Elastizität** bildet das Kernstück einer elastisch skalierten Anwendung. Abhängig von den Leistungsanforderungen, verwenden Sie Telemetrie zur datengesteuerten Entscheidung über den Einsatz vertikaler oder horizontaler Skalierung.

#### Telemetriedatenquellen
Im Kontext von Azure SQL-Datenbank gibt es eine Handvoll wichtiger Quellen, die als Datenquellen für die Shard-Elastizität genutzt werden können.

1. **Leistungstelemetrie** wird jeweils für die Dauer von fünf Minuten in der Sicht **sys.resource\_stats** angezeigt. 
2. Die stündliche **Datenbankkapazitätstelemetrie** ist in der Sicht **sys.resource\_usage** verfügbar.  

Die Nutzung der Leistungsressourcen kann mit der folgenden Abfrage von der master-Datenbank abgefragt werden, wobei ‘Shard\_20140623’ für den Namen der Zieldatenbank steht.

    SELECT TOP 10 *  
    FROM sys.resource_stats  
    WHERE database_name = 'Shard_20140623'  
    ORDER BY start_time DESC 

Die **Leistungstelemetrie** kann über einen angegebenen Zeitraum (in der Abfrage unten sieben Tage) zusammengefasst werden, um abweichendes Verhalten zu entfernen.

    SELECT  
    avg(avg_cpu_percent) AS 'Average CPU Utilization In Percent', 
        max(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent', 
        avg(avg_physical_data_read_percent) AS 'Average Physical Data Read Utilization In Percent', 
        max(avg_physical_data_read_percent) AS 'Maximum Physical Data Read Utilization In Percent', 
        avg(avg_log_write_percent) AS 'Average Log Write Utilization In Percent', 
        max(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent', 
        avg(active_session_count) AS 'Average # of Sessions', 
        max(active_session_count) AS 'Maximum # of Sessions', 
        avg(active_worker_count) AS 'Average # of Workers', 
        max(active_worker_count) AS 'Maximum # of Workers' 
    FROM sys.resource_stats  
    WHERE database_name = ' Shard_20140623' AND start_time > DATEADD(day, -7, GETDATE()); 

Die **Datenbankkapazität** kann mit einer ähnlichen Abfrage der Sicht **sys.resource\_usage** gemessen werden. Der Höchstwert der Spalte **storage\_in\_megabytes** ergibt die aktuelle Datenbankgröße. Diese Telemetrie ist zur horizontalen Skalierung von Anwendungen hilfreich, wenn eine bestimmte Shard ihre Kapazität erreicht.

    SELECT TOP 10 * 
    FROM [sys].[resource_usage] 
    WHERE database_name = 'Shard_20140623'  
    ORDER BY time DESC 

Da Daten in einer bestimmten Shard erfasst werden, ist es sinnvoll, eine Vorhersage für einen Tag zu erstellen und zu ermitteln, ob die Shard über ausreichend Kapazität zum Verarbeiten der kommenden Workload hat. Die folgende Abfrage ist zwar keine echte Implementierung linearer Regression, gibt aber das maximale Delta der Datenbankkapazität zwischen zwei aufeinander folgenden Tagen zurück. Telemetrie dieser Art kann auf eine Regel angewendet werden, die dann zur Ausführung (oder Unterbindung) einer Aktion führen könnte.

    WITH MaxDatabaseDailySize AS( 
        SELECT 
            ROW_NUMBER() OVER (ORDER BY convert(date, [time]) DESC) as [Order], 
            CONVERT(date,[time]) as [date],  
            MAX(storage_in_megabytes) as [MaxSizeDay] 
        FROM [sys].[resource_usage] 
        WHERE  
            database_name = 'Shard_20140623' 
        GROUP BY CONVERT(date,[time]) 
        ) 
    
    SELECT 
        MAX(ISNULL(Size.[MaxSizeDay] - PreviousDaySize.[MaxSizeDay], 0)) 
    FROM  
        MaxDatabaseDailySize Size INNER JOIN 
        MaxDatabaseDailySize PreviousDaySize ON Size.[order]+1 = PreviousDaySize.[order] 
    WHERE 
        Size.[order] < 8 

## Regel  

Die Regel ist das Entscheidungsmodul, das bestimmt, ob eine Aktion ausgeführt werden soll. Einige Regeln sind sehr einfach, andere sind komplizierter. Wie im folgenden Codeausschnitt gezeigt, kann eine auf die Kapazität ausgerichtete Regel so konfiguriert werden, dass eine neue Shard bereitgestellt wird, sobald eine Shard den in $SafetyMargin angegebenen Wert, z. B. 80 % ihrer maximalen Kapazität, erreicht.

    # Determine if the current DB size plus the maximum daily delta size is greater than the threshold 
    if( ($CurrentDbSizeMb + $MaxDbDeltaMb) -gt ($MaxDbSizeMb * $SafetyMargin))  
    {#provision new shard} 

Mit den oben angegebenen Datenquellen können einige Regeln für eine Vielzahl von Szenarien für den Einsatz der Shard-Elastizität formuliert werden.

## Aktion  

Abhängig vom Ergebnis der Regel wird eine Aktion ausgeführt (oder nicht ausgeführt). Die zwei häufigsten Aktionen sind:

* Das Anheben oder Senken der Dienstebene oder Leistungsstufe der Shard 
* Das Hinzufügen oder Entfernen einer Shard aus einer Shard-Gruppe.

Beachten Sie, dass sowohl in horizontalen als auch vertikalen Skalierungslösungen das Ergebnis einer Aktion nicht sofort sichtbar ist. Beim vertikalen Skalieren kann der Aufruf des ALTER DATABASE-Befehls zur Anhebung der Dienstebene einer Basic-Datenbank auf eine Premium-Datenbank beispielsweise unterschiedlich lange dauern. Die Dauer hängt größtenteils von der Datenbankgröße ab (weitere Informationen finden Sie unter [Ändern von Dienstebenen und Leistungsstufen](http://msdn.microsoft.com/library/azure/dn369872.aspx). Ebenso erfolgt auch die Zuweisung oder Bereitstellung einer neuen Shard nicht sofort. Daher sollte bei der vertikalen und horizontalen Skalierung von Anwendungen berücksichtigt werden, dass die Änderung oder Bereitstellung einer neuen Datenbank eine gewisse Zeit in Anspruch nimmt.

## Beispielszenario für Shard-Elastizität 

Das in der folgenden Abbildung dargestellte Beispiel hebt zwei Szenarios für den Einsatz der Elastic Scale-Funktion hervor: 1. horizontale Skalierung einer Shard-Zuordnung, 2. vertikale Skalierung eines einzelnen Shards.

![Operationale Datenerfassung][1]

Zur horizontalen Skalierung wird eine Regel (die auf dem Datum oder der Datenbankgröße basiert) zum Bereitstellen einer neuen Shard und Registrieren bei der Shard-Zuordnung verwendet, sodass die Datenbankebene horizontal wächst. Zur vertikalen Skalierung wird eine zweite Regel implementiert, die bewirkt, dass jede Shard, die älter als einen Tag ist, von der Premium Edition auf die Standard oder Basic Edition herabgestuft wird.

Betrachten Sie das Telemetrieszenario erneut: Die Anwendung führt das Sharding nach Datum aus. Sie sammelt fortwährend Telemetriedaten, wodurch beim Laden der Anwendung eine hochleistungsfähige Edition erforderlich ist. Die Leistungsanforderungen sinken jedoch, je älter die Daten sind. Die Daten des aktuellen Tages [Tnow] werden in eine Hochleistungsdatenbank (Premium) geschrieben. Um Mitternacht wird der Shard des Vortages (jetzt [T-1]) nicht mehr zur Erfassung verwendet. Die aktuellen Daten werden im aktuellen [Tnow]-Shard erfasst. Vor Beginn des nächsten Tages muss ein neuer Shard bereitgestellt und in der Shard-Zuordnung registriert werden ([T+1]).

Dazu kann ein neuer Shard entweder jeweils vor Beginn eines neuen Tages oder immer dann bereitgestellt werden, wenn der aktuelle Shard ([Tnow]) seine maximale Kapazität nahezu erreicht hat. Beim Einsatz dieser beiden Methoden bleibt die Datenlokalität für alle Telemetriedaten, die für einen bestimmten Tag erfasst wurden, gewahrt. Zudem könnte auch stündliches Sharding angewendet werden, um eine feinere Unterteilung zu erhalten. Sobald ein neuer Shard bereitgestellt worden ist und da [T-1] zum Abfragen und Erstellen von Berichten verwendet wird, sollte die Leistungsstufe der Datenbank herabgesetzt werden, um die Kosten zu senken. Mit zunehmendem Alter des Datenbankinhalts und je nach Anwendung kann die Leistungsstufe weiter gesenkt und/oder der Inhalt der Datenbanken in Azure Storage archiviert oder gelöscht werden.

## Ausführen von Szenarios für Shard-Elastizität  

Um die tatsächliche Umsetzung von Szenarios für die horizontale und vertikale Skalierung zu erleichtern, wurden einige [Beispielskripts für Shard-Elastizität](http://go.microsoft.com/?linkid=9862617) erstellt und im Script Center veröffentlicht. Diese PowerShell-Runbooks, die zur Ausführung im Azure Automation-Dienst vorgesehen sind, stellen einige Methoden bereit, die mit der Clientbibliothek für elastische Datenbanken und Azure SQL-Datenbank zusammenarbeiten. Diese Codebeispiele können als Grundlage zur Erstellung eigener Skripts zur Automatisierung der horizontalen und/oder vertikalen Skalierung von Anwendungen genutzt werden.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->


<!--anchors-->
[Telemetry]: #telemetry
[Rule]: #rule
[Action]: #action
 

<!---HONumber=Oct15_HO1-->