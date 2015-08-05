<properties
   pageTitle="Flexible Leistung und Skalierbarkeit mit SQL Data Warehouse | Microsoft Azure"
   description="Grundlegende Informationen zur SQL Data Warehouse-Flexibilität mithilfe von Data Warehouse-Einheiten für die Aufwärts- und Abwärtsskalierung von Compute-Ressourcen. Codebeispiele werden bereitgestellt."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/25/2015"
   ms.author="nicw;JRJ@BigBangData.co.uk;mausher"/>

# Flexible Leistung und Skalierbarkeit mit SQL Data Warehouse
Um die Rechenleistung flexibel zu erhöhen oder zu verringern, müssen Sie nur die Anzahl der Data Warehouse-Einheiten (Data Warehouse Unit, DWU) anpassen, die Ihrem SQL Data Warehouse zugeordnet sind. Data Warehouse-Einheiten sind ein neues Konzept des SQL Data Warehouse, damit Sie einfach und effektiv verwalten können. Dieses Thema dient als Einführung in Data Warehouse-Einheiten. Es erläutert, wie Sie damit Ihre Rechenleistung flexibel skalieren können. Der Artikel enthält auch eine erste Anleitung zum Festlegen eines sinnvollen DWU-Werts für Ihre Umgebung.

## Was ist eine Data Warehouse-Einheit?
Microsoft führt im Hintergrund eine Reihe von Leistungsbenchmarktests durch, um zu bestimmen, mit wie viel Hardware und mit welcher Konfiguration wir unseren Kunden ein konkurrenzfähiges Angebot bereitstellen können. Das Aufwärts- und Abwärtsskalieren der Rechenleistung geschieht in Blöcken zu je 100 DWUs, aber nicht jedes Vielfache von 100 DWUs wird angeboten.

## Wie viele DWUs sollte ich verwenden?
Es gibt viele verschiedene Lösungen, die SQL Data Warehouse für Kunden entsperren kann. Es gibt viele verschiedene Typen von Abfragen, die Kunden ausführen können, sowie eine große zu verarbeitende Datenmenge. Auch die Architektur des Schemas, die Verteilung der Daten und zum Beispiel die Frage, wie viele Benutzer wie oft auf die Daten zugreifen, spielen eine wichtige Rolle.

Anstatt feste DWU-Ausgangspunkte bereitzustellen, die möglicherweise gut für eine Kategorie von Kunden funktionieren, sollen wir uns dieser Frage mit einem praktischen Ansatz nähern. Die Leistung in SQL Data Warehouse wird linear skaliert, und das Ändern von einer Compute-Skalierung in eine andere (z. B. von 100 DWUs auf 2000 DWUs) erfolgt in Sekunden. Dies gibt Ihnen die Freiheit, Dinge auszuprobieren und zu bestimmen, was am besten für Ihr Szenario geeignet ist.


1. Beginnen Sie für eine Data Warehouse-Entwicklung mit einer kleinen Anzahl von DWUs.
2. Überwachen Sie die Leistung Ihrer Anwendung so, dass Sie sich mit DWUs und der beobachteten Leistung vertraut machen können.
3. Bestimmen Sie durch Annahme einer linearen Skalierung, wie viel schneller oder langsamer die Leistung für Sie sein muss, um die optimale Leistungsstufe für Ihre Geschäftsanforderungen zu erreichen. 
4. Erhöhen oder reduzieren Sie je nach Bedarf die Menge an verwendeten DWUs. Der Dienst wird schnell reagieren, um die Compute-Ressourcen gemäß den DWU-Anforderungen anzupassen.
5. Nehmen Sie Anpassungen vor, bis sie die optimale Leistungsstufe für Ihre geschäftlichen Anforderungen erreichen.

Wenn Sie eine Anwendung mit wechselndem Workload haben, können Sie die Leistungsstufen nach oben oder unten verschieben, um Spitzen und Tiefpunkte zu berücksichtigen. Wenn z. B. ein Workload in der Regel am Ende des Monats einen Spitzenwert aufweist, können Sie planen, weitere DWUs während dieser Spitzenzeiten hinzufügen und dann wieder zu entfernen, wenn diese Spitzenzeiten vorbei sind.
 
## Aufwärts- und Abwärtsskalieren von Compute-Ressourcen
Unabhängig vom Cloudspeicher ermöglicht Ihnen die Flexibilität von SQL Data Warehouse das Vergrößern, Verkleinern oder Anhalten der Rechenleistung mithilfe eines Schiebereglers für Data Warehouse-Einheiten (DWUs). Dies bietet Ihnen die Flexibilität, die Rechenleistung auf einen Idealwert für Ihr Unternehmen zu optimieren.

Das Erhöhen der Rechenleistung erfolgt über das [Azure-Portal][] mithilfe von T-SQL, über REST-APIs oder über Powershell. Durch Aufwärts- und Abwärtsskalieren werden alle laufenden oder sich in einer Warteschlange befindenden Aktivitäten abgebrochen, aber in wenigen Sekunden abgeschlossen, sodass Sie mit mehr oder weniger Rechenleistung fortfahren können.

Der folgende T-SQL-Code zeigt, wie Sie die DWU-Zuordnung für Ihr SQL Data Warehouse anpassen:

```
ALTER DATABASE MySQLDW 
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

Sie können dasselbe Ergebnis mithilfe von Powershell und dem folgenden Code erreichen:

```
Set-AzureSQLDatabase -DatabaseName "MySQLDW" -ServerName "MyServer.database.windows.net" -ServiceObjective "DW1000"
```

## Pausieren von Compute-Ressourcen
SQL Data Warehouse bietet die einzigartige Möglichkeit zum Pausieren und Fortsetzen von Compute-Ressourcen bei Bedarf. Wenn das Team die Data Warehouse-Instanz für eine bestimmte Zeitdauer nicht verwendet, wie zum Beispiel nachts, an Wochenenden, an bestimmten Feiertagen oder aus anderen Gründen, können Sie die Data Warehouse-Instanz für diesen Zeitraum anhalten und bei der Rückkehr genau an diesem Punkt fortsetzen.

Durch das Pausieren werden die Serverressourcen zurück an den Pool der verfügbaren Ressourcen im Rechenzentrum gegeben, und das Fortsetzen ruft die erforderlichen Serverressourcen für die DWUs ab, die Sie festgelegt haben, und weist sie der Data Warehouse-Instanz zu.

Das Pausieren und Fortsetzen der Rechenleistung erfolgt über das [Azure-Portal][], über REST-APIs oder über Powershell. Durch das Pausieren werden alle ausgeführten oder sich in einer Warteschlange befindenden Aktivitäten abgebrochen. Wenn Sie zurückkehren, können Sie die Compute-Ressourcen innerhalb weniger Sekunden wieder in Betrieb nehmen.

Der folgende Code zeigt, wie Sie eine Pause mithilfe von PowerShell ausführen:

```
Suspend-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName
"Server01" –DatabaseName "Database02"
```

Das Fortsetzen des Diensts ist mit PowerShell ebenfalls sehr einfach:

```
Resume-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
```

Weitere Informationen zur Verwendung von PowerShell finden Sie im Artikel [Einführung in PowerShell-Cmdlets][].

> [Azure.Note]Da der Speicher vom Server getrennt ist, wird der Speicher von der Pause nicht beeinträchtigt.

## Nächste Schritte
Die Leistungsübersicht finden Sie unter [Leistungsübersicht][].

<!--Image references-->

<!--Article references-->
[Leistungsübersicht]: sql-data-warehouse-overview-performance.md
[Einführung in PowerShell-Cmdlets]: sql-data-warehouse-get-started-powershell-cmdlets.md

<!--MSDN references-->


<!--Other Web references-->

[Azure-Portal]: http://portal.azure.com/

<!---HONumber=July15_HO4-->