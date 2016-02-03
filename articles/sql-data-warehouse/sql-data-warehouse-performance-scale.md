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
   ms.date="01/07/2016"
   ms.author="nicw;jrj;mausher;barbkess;sonyama"/>

# Flexible Leistung und Skalierbarkeit mit SQL Data Warehouse
Um die Rechenleistung flexibel zu erhöhen oder zu verringern, müssen Sie nur die Anzahl der Data Warehouse-Einheiten (Data Warehouse Unit, DWU) anpassen, die Ihrem SQL Data Warehouse zugeordnet sind. Data Warehouse-Einheiten sind ein neues Konzept des SQL Data Warehouse, damit Sie einfach und effektiv verwalten können. Dieses Thema dient als Einführung in Data Warehouse-Einheiten. Es erläutert, wie Sie damit Ihre Rechenleistung flexibel skalieren können. Der Artikel enthält auch eine erste Anleitung zum Festlegen eines sinnvollen DWU-Werts für Ihre Umgebung.

## Was ist eine Data Warehouse-Einheit?
Microsoft führt im Hintergrund eine Reihe von Leistungsbenchmarktests durch, um zu bestimmen, mit wie viel Hardware und mit welcher Konfiguration wir unseren Kunden ein konkurrenzfähiges Angebot bereitstellen können. Das Aufwärts- und Abwärtsskalieren der Rechenleistung geschieht in Blöcken zu je 100 DWUs, aber nicht jedes Vielfache von 100 DWUs wird angeboten.

## Wie viele DWUs sollte ich verwenden?
Anstatt feste DWU-Ausgangspunkte bereitzustellen, die möglicherweise gut für eine Kategorie von Kunden funktionieren, sollen wir uns dieser Frage mit einem praktischen Ansatz nähern. Die Leistung in SQL Data Warehouse wird linear skaliert, und das Ändern von einer Compute-Skalierung in eine andere (z. B. von 100 DWUs auf 2000 DWUs) erfolgt in Sekunden. Dies gibt Ihnen die Flexibilität, Dinge auszuprobieren und zu bestimmen, was am besten für Ihr Szenario geeignet ist.

1. Beginnen Sie bei einer Data Warehouse-Entwicklung, indem Sie eine geringe Anzahl von DWUs auswählen.
2. Überwachen Sie die Anwendungsleistung, und beobachten Sie dabei die Anzahl der ausgewählten DWUs im Vergleich zur beobachteten Leistung.
3. Bestimmen Sie durch Annahme einer linearen Skalierung, wie viel schneller oder langsamer die Leistung für Sie sein muss, um die optimale Leistungsstufe für Ihre Anforderungen zu erreichen. 
4. Erhöhen oder verringern Sie die Anzahl der ausgewählten DWUs. Der Dienst reagiert schnell und passt die Computeressourcen gemäß den DWU-Anforderungen an.
5. Nehmen Sie weitere Anpassungen vor, bis Sie die optimale Leistungsstufe für Ihre geschäftlichen Anforderungen erreichen.

Wenn Sie eine Anwendung mit wechselnder Workload haben, verschieben Sie die Leistungsstufen nach oben oder unten, um Spitzen und Tiefpunkte zu berücksichtigen. Wenn z. B. eine Workload in der Regel am Ende des Monats einen Spitzenwert aufweist, fügen Sie während dieser Spitzenzeiten weitere DWUs hinzu, und entfernen Sie sie wieder, wenn diese Spitzenzeiten vorbei sind.
 
## Aufwärts- und Abwärtsskalieren von Compute-Ressourcen
Unabhängig vom Cloudspeicher ermöglicht Ihnen die Flexibilität von SQL Data Warehouse das Vergrößern, Verkleinern oder Anhalten der Rechenleistung mithilfe eines Schiebereglers für Data Warehouse-Einheiten (DWUs). Dies bietet Ihnen die Flexibilität, die Rechenleistung auf einen Idealwert für Ihr Unternehmen zu optimieren.

Um die Rechenleistung zu erhöhen, können Sie mithilfe des Schiebereglers für die Skalierung im klassischen Azure-Portal weitere DWUs hinzufügen. Sie können auch mithilfe von T-SQL, REST-APIs oder Powershell-Cmdlets DWUs hinzufügen. Durch Aufwärts- und Abwärtsskalieren werden alle laufenden oder sich in einer Warteschlange befindenden Aktivitäten abgebrochen, aber in wenigen Sekunden abgeschlossen, sodass Sie mit mehr oder weniger Rechenleistung fortfahren können.

Im [klassischen Azure-Portal][] können Sie oben auf der SQL Data Warehouse-Seite auf das Symbol zum Skalieren klicken und anschließend mithilfe des Schiebereglers die Anzahl der DWUs, die auf Data Warehouse angewendet werden, erhöhen oder verringern. Klicken Sie anschließend auf „Speichern“. Wenn Sie die Skalierung lieber programmgesteuert ändern, zeigt der folgende T-SQL-Code, wie Sie die DWU-Zuordnung für Ihr SQL Data Warehouse anpassen:

```
ALTER DATABASE MySQLDW 
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```
Dieser T-SQL-Code sollte für den logischen Server und nicht für die SQL Data Warehouse-Instanz selbst ausgeführt werden.

Sie können dasselbe Ergebnis mithilfe von Powershell und dem folgenden Code erreichen:

```
Set-AzureSQLDatabase -DatabaseName "MySQLDW" -ServerName "MyServer.database.windows.net" -ServiceObjective "DW1000"
```

## Pausieren von Compute-Ressourcen
SQL Data Warehouse bietet die einzigartige Möglichkeit zum Pausieren und Fortsetzen von Compute-Ressourcen bei Bedarf. Wenn das Team die Data Warehouse-Instanz für eine bestimmte Zeitdauer nicht verwendet, wie zum Beispiel nachts, an Wochenenden, an bestimmten Feiertagen oder aus anderen Gründen, können Sie die Data Warehouse-Instanz für diesen Zeitraum anhalten und bei der Rückkehr genau an diesem Punkt fortsetzen.

Durch das Pausieren werden die Serverressourcen zurück an den Pool der verfügbaren Ressourcen im Rechenzentrum gegeben, und das Fortsetzen ruft die erforderlichen Serverressourcen für die DWUs ab, die Sie festgelegt haben, und weist sie der Data Warehouse-Instanz zu.

Das Pausieren und Fortsetzen der Rechenleistung erfolgt über das [klassische Azure-Portal][], über REST-APIs oder über Powershell. Durch das Pausieren werden alle ausgeführten oder sich in einer Warteschlange befindenden Aktivitäten abgebrochen. Wenn Sie zurückkehren, können Sie die Compute-Ressourcen innerhalb weniger Sekunden wieder in Betrieb nehmen.

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

[klassische Azure-Portal]: http://portal.azure.com/
[klassischen Azure-Portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0114_2016-->