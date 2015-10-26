<properties 
   pageTitle="Query Performance Insight für Azure SQL-Datenbank" 
   description="Mit der Überwachung der Abfrageleistung werden für eine Azure SQL-Datenbank die Abfragen mit dem höchsten DTU-Verbrauch identifiziert." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="09/30/2015"
   ms.author="sstein"/>

# Query Performance Insight für Azure SQL-Datenbank


Das Verwalten und Abstimmen der Leistung von relationalen Datenbanken ist eine anspruchsvolle Aufgabe, die ein erhebliches Maß an Wissen und Zeit erfordert. Dank Query Performance Insight können Sie den Zeitaufwand für die Problembehandlung der Datenbankleistung reduzieren, weil Folgendes ermöglicht wird:

- Tiefere Einblicke in den Verbrauch von Datenbankressourcen (DTU). 
- Informationen zu Abfragen mit dem höchsten DTU-Verbrauch, für die unter Umständen eine Leistungssteigerung erzielt werden kann. 
- Die Fähigkeit zum Durchführen eines Drilldowns in die Details einer Abfrage.​

> [AZURE.NOTE]Query Performance Insight befindet sich derzeit in der Preview-Phase und steht nur im [Azure-Vorschauportal](https://portal.azure.com/) zur Verfügung.



## Voraussetzungen

- Query Performance Insight ist nur unter Azure SQL-Datenbank V12 verfügbar.
- Query Performance Insight erfordert, dass [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) ausgeführt wird. Query Store wird also automatisch aktiviert, wenn Sie sich für Query Performance Insight registrieren.
 
 
## Berechtigungen

Die folgenden Berechtigungen der [rollenbasierten Zugriffssteuerung](role-based-access-control-configure.md) sind für die Verwendung von Query Performance Insight erforderlich:

- Die Berechtigungen **Leser**, **Besitzer**, **Mitwirkender**, **Mitwirkender von SQL DB** oder **Mitwirkender von SQL Server** sind erforderlich, um die Abfragen und Diagramme mit dem höchsten Ressourcenverbrauch anzuzeigen. 
- Die Berechtigungen **Besitzer**, **Mitwirkender**, **Mitwirkender von SQL DB** oder **Mitwirkender von SQL Server** sind zum Anzeigen von Abfragetext erforderlich.



## Verwenden von Query Performance Insight

Query Performance Insight ist einfach zu verwenden:

- Überprüfen Sie die Liste mit den Abfragen, die den höchsten Ressourcenverbrauch aufweisen. 
- Wählen Sie eine einzelne Abfrage aus, um die Details anzuzeigen.
- Mit **Diagramm bearbeiten** können Sie anpassen, wie die DTU-Verbrauchsdaten angezeigt werden, oder einen anderen Zeitraum auswählen.



> [AZURE.NOTE]Einige Stunden mit Daten müssen von Query Store für SQL-Datenbank erfasst werden, um Query Performance Insight-Funktionen bereitstellen zu können. Wenn die Datenbank keine Aktivität aufweist oder Query Store während eines bestimmten Zeitraums nicht aktiv war, sind die Diagramme beim Anzeigen dieses Zeitraums leer. Sie können Query Store jederzeit aktivieren, wenn die Anwendung nicht ausgeführt wird.





## Überprüfen von Abfragen mit dem höchsten DTU-Verbrauch

Gehen Sie im [Vorschauportal](https://portal.azure.com) wie folgt vor:

1. Navigieren Sie zu einer SQL-Datenbank, und klicken Sie auf **Query Performance Insight**. 

    ![Query Performance Insight][1]

    Die Ansicht mit den Abfragen wird geöffnet, und die Liste mit den Abfragen mit dem höchsten DTU-Verbrauch wird angezeigt.

1. Klicken Sie an verschiedenen Stellen auf das Diagramm, um die Details anzuzeigen.<br>In der obersten Zeile wird der Gesamtwert für DTU% für die Datenbank angezeigt, und die Balken zeigen die Werte für den DTU%-Verbrauch der ausgewählten Abfragen an. Sie können einzelne Abfragen aktivieren oder deaktivieren, um sie in das Diagramm einzubeziehen oder davon auszuschließen.

    ![Abfragen mit höchstem Verbrauch][2]

1. Optional können Sie auf **Diagramm bearbeiten** klicken und anpassen, wie die DTU-Verbrauchsdaten angezeigt werden, oder einen anderen Zeitraum auswählen.

## Anzeigen von Details einzelner Abfragen

So zeigen Sie die Details von Abfragen an

1. Klicken Sie in der Liste mit den Abfragen mit dem höchsten Verbrauch auf eine beliebige Abfrage.<br>Die Detailansicht wird geöffnet, und der DTU-Verbrauch der Abfragen wird in Abhängigkeit der Zeit aufgeführt. 
3. Klicken Sie an verschiedenen Stellen auf das Diagramm, um die Details anzuzeigen.<br>In der obersten Zeile wird der Gesamtwert für DTU% angezeigt, und die Balken zeigen die Werte für den DTU%-Verbrauch der ausgewählten Abfragen an.
4. Überprüfen Sie die Daten, um ausführliche Metriken wie Dauer, Anzahl der Ausführungen und Ressourcennutzung in Prozent für jedes Intervall anzuzeigen, in dem die Abfrage ausgeführt wurde.
    
    ![Abfragedetails][3]

1. Optional können Sie auf **Skript anzeigen** klicken, um den Abfragetext anzuzeigen, und auf **Diagramm bearbeiten** klicken und anpassen, wie die DTU-Verbrauchsdaten angezeigt werden, oder einen anderen Zeitraum auswählen.




## Zusammenfassung

Dank Query Performance Insight können Sie die Auswirkungen der Abfragen-Workload und die Wechselwirkung mit dem Verbrauch von Datenbankressourcen besser verstehen. Mit diesem Feature erhalten Sie Informationen zu den Abfragen mit dem höchsten Verbrauch und können die Abfragen ermitteln, die angepasst werden müssen, bevor sie ein Problem verursachen. Klicken Sie auf einem Datenbankblatt auf die Kachel **Query Performance Insight**, um die Abfragen mit dem höchsten Ressourcenverbrauch (DTU) anzuzeigen.




## Nächste Schritte

Datenbankworkloads sind dynamisch und ändern sich ständig. Überwachen Sie Ihre Abfragen, und passen Sie sie weiter an, um die Leistung zu optimieren.

Sehen Sie sich den [Index Advisor](sql-database-index-advisor.md) an, um weitere Empfehlungen zur Verbesserung der Leistung Ihrer SQL-Datenbank zu erhalten.

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png

<!---HONumber=Oct15_HO3-->