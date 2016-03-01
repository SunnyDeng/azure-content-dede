<properties
   pageTitle="Merkmale des SQL Data Warehouse-Diensts | Microsoft Azure"
   description="Maximalwerte für Verbindungen, Abfragen, Transact-SQL-DDL und -DML und Systemsichten für SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/11/2016"
   ms.author="barbkess;jrj;sonyama"/>

# Merkmale des SQL Data Warehouse-Diensts

Zu den Merkmalen gehören Maximalwerte, die für Azure SQL Data Warehouse festgelegt wurden, um auch sehr anspruchsvolle Analyse-Workloads unterstützen zu können. Außerdem wird sichergestellt, dass jede einzelne Abfrage über die Ressourcen verfügt, die sie für eine optimale Leistung benötigt.

## Verbindungen

| Kategorie | Beschreibung | Maximum |
| :---------------- | :------------------------------------------- | :----------------- |
| Sitzung | Gleichzeitig geöffnete Sitzungen | 1024<br/><br/>Wir unterstützen 1024 gleichzeitige Verbindungen, die alle gleichzeitige Anforderungen an das Data Warehouse übermitteln können. Beachten Sie, dass die Anzahl der Abfragen begrenzt ist, die gleichzeitig ausgeführt werden können. Wenn ein Grenzwert überschritten wird, gelangt die Anforderung in eine interne Warteschlange, in der sie auf die Verarbeitung wartet.|
| Session | Maximaler Arbeitsspeicher für vorbereitete Anweisungen | 20 MB |
| Anmeldungen | Logins pro SQL Server-Instanz | 500\.000 |


## Verarbeitung von Abfragen

| Kategorie | Beschreibung | Maximum |
| :---------------- | :------------------------------------------- | :----------------- |
| Abfrage | Gleichzeitige Abfragen von Benutzertabellen. | 32<br/><br/>Zusätzliche Abfragen gelangen in eine interne Warteschlange, in der sie auf die Verarbeitung warten. Unabhängig von der Anzahl der Abfragen, die gleichzeitig ausgeführt werden, wird jede Abfrage optimiert, um die massive Parallelverarbeitungsarchitektur vollständig zu nutzen.|
| Abfrage | In Warteschlange gestellte Abfragen von Benutzertabellen. | 1000 |
| Abfrage | Gleichzeitige Abfragen von Systemsichten. | 100 |
| Abfrage | In Warteschlange gestellte Abfragen von Systemsichten | 1000 |
| Abfrage | Maximale Parameter | 2098 |
| Batch | Maximale Größe | 65\.536*4096 |


## Data Definition Language (DDL)

| Kategorie | Beschreibung | Maximum |
| :---------------- | :------------------------------------------- | :----------------- |
| Tabelle | Tabellen pro Datenbank | 2 Milliarden |
| Tabelle | Spalten pro Tabelle | 1024 Spalten |
| Tabelle | Bytes pro Spalte | 8000 Bytes |
| Tabelle | Bytes pro Zeile, definierte Größe | 8060 Bytes<br/><br/>Die Anzahl der Bytes pro Zeile wird auf gleiche Weise wie bei SQL Server mit aktivierter Seitenkomprimierung berechnet. Die maximale Anzahl ist 8060 Bytes. Sehen Sie sich die Berechnungen unter [Schätzen der Größe eines gruppierten Indexes](https://msdn.microsoft.com/library/ms178085.aspx) auf der MSDN-Website an, um die Zeilengröße zu schätzen.<br/><br/>Eine Liste der SQL Data Warehouse-Datentypgrößen finden Sie unter [CREATE TABLE (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt203953.aspx). |
| Tabelle | Partitionen pro Tabelle | 15\.000<br/><br/>Zur Erzielung einer hohen Leistung empfehlen wir das Minimieren der Anzahl der Partitionen, die Sie zum Erfüllen Ihrer Geschäftsanforderungen benötigen. Mit steigender Anzahl von Partitionen wächst der Verarbeitungsaufwand für DDL- und DML-Vorgänge, was zu Leistungseinbußen führt.|
| Tabelle | Zeichen pro Partitionsbegrenzungswert.| 4000 |
| Index | Nicht gruppierte Indizes pro Tabelle. | 999<br/><br/>Gilt nur für Rowstore-Tabellen.|
| Index | Gruppierte Indizes pro Tabelle. | 1<br><br/>Gilt für Rowstore- und Columnstore-Tabellen.|
| Index | Zeilen in einer Rowgroup eines Columnstore-Indexes | 1\.024<br/><br/>Jeder Columnstore-Index wird in Form von mehreren Columnstore-Indizes implementiert. Beachten Sie, dass beim Einfügen von 1.024 Zeilen in einen SQL Data Warehouse-Columnstore-Index die Zeilen nicht alle in dieselbe Rowgroup übertragen werden.|
| Index | Parallele Erstellung gruppierter Columnstore-Indizes. | 32<br/><br/>Gilt, wenn alle gruppierten Columnstore-Indizes für verschiedene Tabellen erstellt werden. Pro Tabelle ist nur das Erstellen eines gruppierten Columnstore-Indexes zulässig. Zusätzliche Anforderungen werden in eine Warteschlange gestellt.|
| Index | Größe des Indexschlüssels. | 900 Byte<br/><br/>Gilt nur für Rowstore-Indizes.<br/><br/>Indizes für „varchar“-Spalten mit einer maximalen Größe von mehr als 900 Byte können erstellt werden, wenn die vorhandenen Daten in den Spalten bei der Indexerstellung nicht größer als 900 Byte sind. Anschließende auf die Spalten angewendete INSERT- oder UPDATE-Anweisungen, die bewirken, dass die Gesamtgröße 900 Bytes überschreitet, haben allerdings keinen Erfolg.|
| Index | Schlüsselspalten pro Index. | 16<br/><br/>Gilt nur für Rowstore-Indizes. Gruppierte Columnstore-Indizes enthalten alle Spalten.|
| Statistiken | Größe der kombinierten Spaltenwerte. | 900 Bytes. |
| Statistiken | Spalten pro Statistikobjekt. | 32 |
| Statistiken | Für Spalten pro Tabelle erstellte Statistiken. | 30\.000 |
| Gespeicherte Prozeduren | Maximale Schachtelungsebenen. | 8 |
| Sicht | Spalten pro Sicht | 1024 |


## Data Manipulation Language (DML)

| Kategorie | Beschreibung | Maximum |
| :---------------- | :------------------------------------------- | :----------------- |
| SELECT-Ergebnisse | Spalten pro Zeile | 4\.096<br/><br/>Das Ergebnis einer SELECT-Anweisung kann nie mehr als 4096 Spalten pro Zeile enthalten. Es gibt keine Garantie, dass Sie stets über 4096 verfügen. Wenn der Abfrageplan eine temporäre Tabelle erfordert, gilt möglicherweise der Maximalwert von 1024 Spalten pro Tabelle.|
| SELECT-Ergebnisse | Bytes pro Zeile | >8.060<br/><br/>Die Anzahl der Byte pro Zeile im Ergebnis der SELECT-Anweisung kann den Maximalwert 8.060 überschreiten, der für Tabellenzeilen zulässig ist. Wenn der Abfrageplan für die SELECT-Anweisung eine temporäre Tabelle erfordert, gilt möglicherweise der Maximalwert von 8060 Bytes pro Tabelle.|
| SELECT-Ergebnisse | Bytes pro Spalte | >8.000<br/><br/>Die Anzahl der Byte pro Spalte im Ergebnis der SELECT-Anweisung kann den Maximalwert 8.000 überschreiten, der für Tabellenspalten zulässig ist. Wenn der Abfrageplan für die SELECT-Anweisung eine temporäre Tabelle erfordert, gilt möglicherweise der Maximalwert von 8000 Bytes pro Tabelle.|
| SELECT | Geschachtelte Unterabfragen | 32<br/><br/>In einer SELECT-Anweisung sind maximal 32 geschachtelte Unterabfragen zulässig. Es gibt keine Garantie, dass Sie stets über 32 verfügen. Ein JOIN-Befehl kann z. B. eine Unterabfrage in den Abfrageplan einführen. Die Anzahl der Unterabfragen kann auch durch den verfügbaren Speicher eingeschränkt werden.|
| SELECT | Spalten pro JOIN | 1\.024 Spalten<br/><br/>Für einen JOIN sind maximal 1.024 Spalten zulässig. Es gibt keine Garantie, dass Sie stets über 1024 verfügen. Wenn der JOIN-Plan eine temporäre Tabelle mit mehr Spalten als das JOIN-Ergebnis erfordert, gilt die Grenze von 1024 für die temporäre Tabelle. |
| SELECT | Bytes pro GROUP BY-Spalten. | 8\.060<br/><br/>Die Maximalgröße von Spalten in der GROUP BY-Klausel beträgt 8.060 Byte.|
| SELECT | Bytes pro ORDER BY-Spalten | 8\.060<br/><br/>Die Maximalgröße von Spalten in der ORDER BY-Klausel beträgt 8.060 Byte.|
| INSERT | Bytes pro Spalte, feste und variable Breite. | 8000 Bytes. Versuche, mehr Bytes einzufügen, als für die Spalte definiert sind, führen zu einem Fehler.|
| INSERT | Bytes pro Zeile, Spalten mit variabler Breite. | >8060 Bytes. Über 8060 Bytes hinausgehende Daten werden in einem Überlaufbereich abgelegt.|
| UPDATE | Bytes pro Spalte, feste und variable Breite. | 8000 Bytes. Versuche, eine Spalte auf einen Wert zu aktualisieren, der mehr Bytes aufweist, als für die Spalte definiert sind, führen zu einem Fehler.|
| Bezeichner und Konstanten pro Anweisung | Anzahl der Bezeichner und Konstanten, auf die verwiesen wird. | 65\.535<br/><br/>SQL Data Warehouse beschränkt die Anzahl von Bezeichnern und Konstanten, die in einem einzelnen Ausdruck einer Abfrage enthalten sein können. Dieser Grenzwert ist 65.535. Das Überschreiten dieses Werts führt zum SQL Server-Fehler 8632. Weitere Informationen finden Sie unter [Interner Fehler: ein Ausdrucksdienstelimit wurde erreicht](http://support.microsoft.com/kb/913050/).|

## Systemsichten

| Systemsicht | Maximale Anzahl von Zeilen |
| :--------------------------------- | :------------|
| sys.dm\_pdw\_component\_health\_alerts | 10\.000 |
| sys.dm\_pdw\_dms\_cores | 100 |
| sys.dm\_pdw\_dms\_workers | Gesamtanzahl der DMS-Worker für die letzten 1000 SQL-Anforderungen. |
| sys.dm\_pdw\_dms\_worker\_pairs | 10\.000 |
| sys.dm\_pdw\_errors | 10\.000 |
| sys.dm\_pdw\_exec\_requests | 10\.000 |
| sys.dm\_pdw\_exec\_sessions | 10\.000 |
| sys.dm\_pdw\_request\_steps | Die Gesamtzahl der Schritte für die letzten 1000 SQL-Anforderungen, die in „sys.dm\_pdw\_exec\_requests“ gespeichert sind. |
| sys.dm\_pdw\_os\_event\_logs | 10\.000 |
| sys.dm\_pdw\_sql\_requests | Die letzten 1000 SQL-Anforderungen, die in „sys.dm\_pdw\_exec\_requests“ gespeichert sind. |

## Nächste Schritte
Weitere Referenzinformationen finden Sie unter [SQL Data Warehouse-Referenz – Übersicht][].

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse-Referenz – Übersicht]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!---HONumber=AcomDC_0218_2016-->