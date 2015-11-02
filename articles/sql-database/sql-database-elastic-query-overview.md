<properties
    pageTitle="Übersicht über elastische Datenbankabfragen in Azure SQL-Datenbank | Microsoft Azure"
    description="Übersicht über das Abfragefeature für elastische Datenbanken"    
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="sidneyh"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/15/2015"
    ms.author="torsteng" />

# Übersicht über elastische Datenbankenabfragen in Azure SQL-Datenbank (Vorschau)

Mithilfe des Features „Elastische Datenbankabfrage“ (in der Vorschauphase) können Sie eine Transact-SQL-Abfrage ausführen, die mehrere Datenbanken in Azure SQL-Datenbank (SQLDB) umfasst. Es ermöglicht das Ausführen datenbankübergreifender Abfragen für den Zugriff auf Remotetabellen und das Verbinden von Microsoft- und Drittanbietertools (Excel, PowerBI, Tableau usw.), um Datenebenen mit mehreren Datenbanken abzufragen. Mit dieser Funktion können Sie Abfragen horizontal auf große Datenebenen in SQL-Datenbanken skalieren und die Abfrageergebnisse in Berichten für Business Intelligence (BI) darstellen. Informationen zum Erstellen einer Anwendung für elastische Datenbankabfragen finden Sie unter [Erste Schritte mit elastischen Datenbankabfragen](sql-database-elastic-query-getting-started.md).

## Neuerungen bei elastischen Datenbankabfragen

* Datenbankübergreifende Abfrageszenarien mit einzelnen Remotedatenbanken können jetzt vollständig in T-SQL definiert werden. Dadurch wird ein schreibgeschütztes Abfragen von Remotedatenbanken ermöglicht. Dies bietet derzeitigen Kunden von lokalem SQL Server eine Option zum Migrieren von Anwendungen unter Verwendung von Namen mit drei oder vier Teilen oder eines Verbindungsservers zu SQLDB. 
* Elastische Abfragen werden nun zusätzlich zum Premium-Tarif auch vom Standard-Tarif unterstützt. Informationen zu Leistungseinschränkungen bei niedrigeren Tarifen finden Sie nachstehend im Abschnitt „Einschränkungen der Vorschau“.
* Elastische Abfragen können nun SQL-Parameter zur Ausführung an die Remotedatenbank übertragen.
* Aufrufe von remote gespeicherten Prozeduren oder Remotefunktionen, die „sp\_execute\_fanout“ verwenden, können jetzt Parameter wie [sp\_executesql](https://msdn.microsoft.com/library/ms188001.aspx) nutzen.
* Die Leistung beim Abrufen großer Resultsets aus Remotedatenbanken wurde verbessert.
* Externe Tabellen mit elastischer Abfrage können jetzt auf Remotetabellen mit einem anderen Schema- oder Tabellennamen verweisen.

## Szenarien mit elastischen Datenbankabfragen

Ziel ist es, Berichterstellungsszenarien zu vereinfachen, in denen mehrere Datenbanken Zeilen zu einem einzelnen Gesamtergebnis beitragen. Die Abfrage kann entweder vom Benutzer oder durch die Anwendung direkt oder indirekt über Tools erstellt werden, die mit der Datenbank verbunden sind. Dies ist besonders hilfreich beim Erstellen von Berichten mit kommerziellen BI- oder Datenintegrationstools bzw. jeglicher Anwendung, die nicht geändert werden können. Mithilfe einer elastischen Abfrage können Sie mehrere Datenbanken mithilfe der üblichen SQL Server-Verbindungsumgebung in Tools wie Excel, PowerBI, Tableau oder Cognos abfragen. Außerdem ermöglicht eine elastische Abfrage den einfachen Zugriff auf eine ganze Sammlung von Datenbanken, die von SQL Server Management Studio oder Visual Studio ausgegeben werden, und sie vereinfacht datenbankübergreifende Abfragen aus Entity Framework oder anderen ORM-Umgebungen. Abbildung 1 zeigt ein Szenario, in dem eine vorhandene Cloudanwendung (die die [Clientbibliothek für elastische Datenbanken](sql-database-elastic-database-client-library.md) verwendet) auf einer horizontal skalierten Datenebene aufbaut und eine elastische Abfrage für datenbankübergreifende Berichte verwendet wird.

**Abbildung 1** Elastische Datenbankabfrage auf horizontal skalierter Datenebene

![Abfrage für elastische Datenbanken in der horizontal skalierten Datenebene][1]

Kundenszenarien für elastische Abfragen zeichnen sich durch die folgenden Topologien aus:

* **Vertikale Partitionierung – Datenbankübergreifende Abfragen** (Topologie 1): Die Daten sind zwischen mehreren Datenbanken auf einer Datenebene vertikal partitioniert. In der Regel befinden sich verschiedene Sätze von Tabellen in verschiedenen Datenbanken. Das bedeutet, dass das Schema bei verschiedenen Datenbanken unterschiedlich ist. Beispielsweise befinden sich alle bestandsbezogenen Tabellen in einer Datenbank, während alle Buchhaltungstabellen in einer zweiten Datenbank enthalten sind. Gängige Anwendungsfälle mit dieser Topologie erfordern zum Erstellen von Berichten das Abfragen von Tabellen in mehreren Datenbanken.
* **Horizontale Partitionierung – Sharding** (Topologie 2): Daten werden horizontal partitioniert, um Zeilen auf einer horizontal hochskalierten Datenebene zu verteilen. Bei diesem Ansatz ist das Schema für alle teilnehmenden Datenbanken identisch. Dieser Ansatz wird auch „Sharding“ genannt. Sharding kann (1.) mithilfe der Bibliothek für elastische Datenbanktools oder (2.) eines eigenständigen Shardings ausgeführt und verwaltet werden. Eine elastische Abfrage dient zum viele Shards übergreifenden Abfragen oder Erstellen von Berichten. 

> [AZURE.NOTE]Die elastisch Datenbankabfrage funktioniert am besten bei gelegentlichen Berichterstellungsszenarien, bei denen der Großteil der Verarbeitung auf der Datenebene ausgeführt werden kann. Bei hohem Berichtsaufkommen oder Data Warehouse-Szenarien mit komplexeren Abfragen sollten Sie auch die Verwendung von [Azure SQL Data Warehouse](http://azure.microsoft.com/services/sql-data-warehouse/) in Betracht ziehen.


## Topologien für elastische Datenbankabfragen

### Topologie 1: Vertikale Partitionierung – Datenbankübergreifende Abfragen

Lesen Sie zum Einstieg in die Programmierung [Erste Schritte mit datenbankübergreifenden Abfragen (vertikale Partitionierung)](sql-database-elastic-query-getting-started-vertical.md).

Eine elastische Abfrage kann verwendet werden, um Daten in einer SQLDB-Datenbank anderen SQLDB-Datenbanken zur Verfügung zu stellen. Dadurch können Abfragen aus einer Datenbank auf Tabellen in einer beliebigen anderen SQLDB-Remotedatenbank verweisen. Der erste Schritt ist das Definieren einer externen Datenquelle für jede Remotedatenbank. Die externe Datenquelle wird in der lokalen Datenbank definiert, aus der Sie auf Tabellen zugreifen möchten, die sich in der Remotedatenbank befinden. Es sind keine Änderungen an der Remotedatenbank erforderlich. Für normale vertikale Partitionierungsszenarien, in denen verschiedene Datenbanken unterschiedliche Schemas haben, können elastische Abfragen verwendet werden, um gängige Anwendungsfälle zu implementieren, z. B. den Zugriff auf Verweisdaten und datenbankübergreifende Abfragen.

**Verweisdaten**: Topologie 1 wird zur Verwaltung von Verweisdaten verwendet. In der folgenden Abbildung sind die beiden Tabellen mit Verweisdaten (T1 und T2) in einer dedizierten Datenbank gespeichert. Über eine elastische Abfrage können Sie nun aus anderen Datenbanken remote auf die Tabellen T1 und T2 zugreifen (siehe die Abbildung). Wählen Sie Topologie 1, wenn Verweistabellen klein sind oder Remoteabfragen von Verweistabellen selektive Prädikate haben.

**Abbildung 2** Vertikale Partitionierung – Verwenden einer elastischen Abfrage zum Abfragen von Verweisdaten

![Vertikale Partitionierung – Verwenden einer elastischen Abfrage zum Abfragen von Verweisdaten][3]

**Datenbankübergreifende Abfragen**: Elastische Abfragen ermöglichen Anwendungsfälle, die das Abfragen mehrerer SQLDB-Datenbanken erfordern. Abbildung 3 zeigt vier Datenbanken: CRM, Inventory, HR und Products. Abfragen, die auf eine der Datenbanken angewendet werden, benötigen außerdem Zugriff auf eine oder alle anderen Datenbanken. Bei Verwenden einer elastischen Abfrage können Sie Ihre Datenbank für diesen Fall konfigurieren, indem Sie einige einfache DDL-Anweisungen auf jede der vier Datenbanken anwenden. Nach dieser einmaligen Konfiguration ist der Zugriff auf eine Remotetabelle so einfach wie das Verweisen auf eine lokale Tabelle in Ihren T-SQL-Abfragen oder in Ihren BI-Tools. Dieser Ansatz wird empfohlen, wenn von den Remoteabfragen keine umfangreichen Ergebnisse zurückgegeben werden.

**Abbildung 3** Vertikale Partitionierung – Verwenden einer elastischen Abfrage zum Abfragen verschiedener Datenbanken

![Vertikale Partitionierung – Verwenden einer elastischen Abfrage zum Abfragen verschiedener Datenbanken][4]

### Topologie 2: Horizontale Partitionierung – Sharding

Lesen Sie zum Einstieg in die Programmierung [Erste Schritte mit elastischen Datenbankabfragen für horizontale Partitionierung (Sharding)](sql-database-elastic-query-getting-started.md).

Das Verwenden einer elastischen Abfrage für Berichtsaufgaben auf einer Datenebene mit Sharding, d. h. horizontaler Partitionierung, erfordert, dass eine [elastische Datenbankshardzuordung](sql-database-elastic-scale-shard-map-management.md) die Datenbanken der Datenebene darstellt. Normalerweise wird nur eine Shardzuordnung in diesem Szenario verwendet, und eine dedizierte Datenbank mit elastischen Abfragefunktionen dient als Einstiegspunkt für Berichtsabfragen. Nur diese dedizierte Datenbank benötigt Zugriff auf die Shardzuordnung. Abbildung 2 zeigt diese Topologie und ihre Konfiguration mit der elastischen Abfragedatenbank und Shardzuordung. Beachten Sie, dass nur die elastische Abfragedatenbank eine Azure SQL-Datenbank-V12-Datenbank sein muss. Die Datenbanken der Datenebene können eine beliebige Version oder Edition von Azure SQL-Datenbank haben. Weitere Informationen zur Clientbibliothek für elastische Datenbanken und zum Erstellen von Shardzuordungen finden Sie unter [Verwaltung von Shardzuordungen](sql-database-elastic-scale-shard-map-management.md).

**Abbildung 4** Horizontale Partitionierung – Verwenden einer elastischen Abfrage von Datenebenen mit Sharding für die Berichterstellung

![Horizontale Partitionierung – Verwenden einer elastischen Abfrage von Datenebenen mit Sharding für die Berichterstellung][5]

> [AZURE.NOTE]Die dedizierte Abfragedatenbank für elastische Datenbanken muss eine SQLDB-V12-Datenbank sein. Es gibt keine Einschränkungen für die Shards selbst.


## Implementieren elastischer Datenbankabfragen

In den folgenden Abschnitten werden die Schritte zum Implementieren einer elastischen Abfrage für die vertikalen und horizontalen Partitionierungsszenarien erläutert. Zudem finden Sie Verweise auf detaillierte Dokumentation zu den verschiedenen Partitionierungsszenarien.

### Vertikale Partitionierung – Datenbankübergreifende Abfragen

Die folgenden Schritte dienen zum Konfigurieren elastischer Datenbankabfragen für vertikale Partitionierungsszenarien, die Zugriff auf eine Tabelle in einer SQLDB-Remotedatenbank benötigen:

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey 
*    [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource of type **RDBMS**
*    [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Nach Ausführen der DDL-Anweisungen können Sie auf die Remotetabelle „mytable“ wie auf eine lokale Tabelle zugreifen. Azure SQL-Datenbank öffnet automatisch eine Verbindung mit der Remotedatenbank, verarbeitet Ihre Anforderung an die Remotedatenbank und gibt die Ergebnisse zurück. Weitere Informationen zu den Schritten, die für das vertikale Partitionierungsszenario erforderlich sind, finden Sie unter [Elastische Abfrage für die vertikale Partitionierung](sql-database-elastic-query-vertical-partitioning.md).

### Horizontale Partitionierung – Sharding 

Die folgenden Schritte dienen zum Konfigurieren elastischer Datenbankabfragen für horizontale Partitionierungsszenarien, die Zugriff auf eine Gruppe von Tabelle in (üblicherweise) mehreren SQLDB-Remotedatenbanken benötigen:

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential 
*    Erstellen Sie eine [Shardzuordnung](sql-database-elastic-scale-shard-map-management.md), die Ihre Datenebene darstellt, mithilfe der Clientbibliothek für elastische Datenbanken.   
*    [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource of type **SHARD\_MAP\_MANAGER**
*    [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Nachdem Sie diese Schritte ausgeführt haben können Sie auf die horizontal partitionierte Tabelle „mytable“ wie auf eine lokale Tabelle zugreifen. Azure SQL-Datenbank öffnet automatisch mehrere parallele Verbindungen mit den Remotedatenbanken, in denen die Tabellen physisch gespeichert sind, verarbeitet die Anforderungen an die Remotedatenbanken und gibt die Ergebnisse zurück. Weitere Informationen zu den Schritten, die für das horizontale Partitionierungsszenario erforderlich sind, finden Sie unter [Elastische Abfrage für die horizontale Partitionierung](sql-database-elastic-query-horizontal-partitioning.md).

## T-SQL-Abfragen
Sobald Sie Ihre externen Datenquellen und externen Tabellen definiert haben, können Sie herkömmliche SQL Server-Verbindungszeichenfolgen zum Verbinden mit den Datenbanken verwenden, in denen Sie Ihre externen Tabellen definiert haben. Sie können dann bei den nachstehend beschriebenen Einschränkungen über diese Verbindung T-SQL-Anweisungen auf Ihre externen Tabellen anwenden. Weitere Informationen und Beispiele für T-SQL-Abfragen finden Sie in der Dokumentation in den Themen zur [horizontalen Partitionierung](sql-database-elastic-query-horizontal-partitioning.md) und [vertikalen Partitionierung](sql-database-elastic-query-vertical-partitioning.md).

## Konnektivität für Tools
Sie können herkömmliche SQL Server-Verbindungszeichenfolgen zum Verbinden Ihrer Anwendungen bzw. BI- oder Datenintegrationstools mit Datenbanken verwenden, die externe Tabellen enthalten. Stellen Sie sicher, dass SQL Server als Datenquelle für das Tool unterstützt wird. Nach dem Herstellen der Verbindung können Sie auf die elastische Abfragedatenbank und externen Tabellen wie auf jede andere SQL Server-Datenbank zugreifen, mit der Sie sich über Ihr Tool verbinden.

## Kosten

Elastische Abfragen sind in den Kosten für Azure SQL-Datenbanken enthalten. Beachten Sie, dass Topologien, bei denen sich Ihre Remotedatenbanken in einem anderen Datencenter als der Endpunkt der elastischen Abfrage befinden, unterstützt werden. Der Datenausgang aus Remotedatenbanken wird hingegen zu den üblichen [Azure-Gebühren](https://azure.microsoft.com/pricing/details/data-transfers/) in Rechnung gestellt.

## Einschränkungen der Vorschau
* Beim Standard-Tarif kann das Ausführen Ihrer ersten elastischen Abfrage einige Minuten dauern. Dieser Zeitraum wird für das Laden der elastischen Abfragefunktionalität benötigt. Je höher der Tarif, desto besser die Ladeleistung.
* Das Erstellen von Skripts für externe Datenquellen oder externe Tabellen in SSMS oder SSDT wird noch nicht unterstützt.
* Import/Export für SQLDB unterstützt noch keine externen Datenquellen und externen Tabellen. Wenn Sie Import/Export verwenden müssen, löschen Sie diese Objekte vor dem Exportieren, und erstellen Sie sie nach dem Importieren neu. 
* Elastische Datenbankabfragen unterstützen derzeit nur den schreibgeschützten Zugriff auf externe Tabellen. Sie können jedoch die vollständige T-SQL-Funktionalität für die Datenbank nutzen, in der die externe Tabelle definiert ist. Dies kann z. B. hilfreich sein, um temporäre Ergebnisse mithilfe von beispielsweise SELECT <column_list> INTO <local_table> dauerhaft zu speichern oder um gespeicherte Prozeduren für die elastische Abfragedatenbank zu definieren, die auf externe Tabellen verweisen.
* Mit Ausnahme von „nvarchar(max)“ werden LOB-Typen in externen Tabellendefinitionen nicht unterstützt. Um dieses Problem zu umgehen, können Sie eine Sicht für die Remotedatenbank erstellen, die den LOB-Typ in „nvarchar(max)“ umwandelt, ihre externe Tabelle für die Sicht anstatt für die Basistabelle definieren und sie in Ihren Abfragen in den ursprünglichen LOB-Typ rückumwandeln.
* Spaltenstatistiken werden für externe Tabellen derzeit nicht unterstützt. Tabellenstatistiken werden unterstützt, müssen aber manuell erstellt werden.

## Feedback
Geben Sie uns bitte unten im Disqus-Kommentar, in den MSDN-Foren oder auf StackOverflow Feedback zu Ihrer Erfahrung mit elastischen Datenbankabfragen. Für uns sind alle Arten von Feedback zum Dienst (Fehler, Designprobleme und fehlende Features) interessant.

## Weitere Informationen

Weitere Informationen zu datenbankübergreifenden Abfragen und vertikalen Partitionierungsszenarien finden Sie in den folgenden Dokumenten:

* [Datenbankübergreifende Abfragen und die vertikale Partitionierung (Übersicht)](sql-database-elastic-query-vertical-partitioning.md)
* Führen Sie unser schrittweises Tutorial aus, um innerhalb von Minuten zu einem umfassenden Arbeitsbeispiel zu gelangen: [Erste Schritte mit datenbankübergreifenden Abfragen (vertikale Partitionierung)](sql-database-elastic-query-getting-started-vertical.md).


Weitere Informationen zu horizontalen Partitionierungs- und Shardingszenarien finden Sie hier:

* [Horizontale Partitionierung und Sharding – Übersicht](sql-database-elastic-query-horizontal-partitioning.md) 
* Führen Sie unser schrittweises Tutorial aus, um innerhalb von Minuten zu einem umfassenden Arbeitsbeispiel zu gelangen: [Erste Schritte mit elastischen Datenbankabfragen für horizontale Partitionierung (Sharding)](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->

<!---HONumber=Oct15_HO4-->