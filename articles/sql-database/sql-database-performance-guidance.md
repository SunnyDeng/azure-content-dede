<properties
	pageTitle="Leitfaden zur Azure SQL-Datenbankleistung für Einzeldatenbanken"
	description="Dieses Thema enthält eine Anleitung, mit deren Hilfe Sie ermitteln können, welche Dienstebene für Ihre Anwendung geeignet ist. Außerdem enthält es Empfehlungen zur Optimierung der Anwendung, damit Sie alle Vorteile von Azure SQL-Datenbank ausnutzen können."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="10/13/2015"
	ms.author="jroth" />

# Leitfaden zur Azure SQL-Datenbankleistung für Einzeldatenbanken

## Übersicht

Microsoft Azure SQL-Datenbank verfügt über drei [Dienstebenen](sql-database-service-tiers.md): Basic, Standard und Premium. Durch eine strikte Kontrolle des Ressourcenumfangs von Azure SQL-Datenbank und der zugehörigen sekundären Replikate bietet die Dienstebene Premium für Cloudanwendungen eine besser vorhersagbare Leistung. Azure SQL-Datenbank erweitert dieses Konzept auf die Dienstebene Standard, um Vorhersagbarkeit mit hoher Leistung für Datenbanken zu bieten, die geringere Leistungsanforderungen als bei der Dienstebene Premium aufweisen. Die Dienstebene Basic ist dafür ausgelegt, die Leistungsanforderungen für kostengünstigere Datenbanken zu erfüllen.

>[AZURE.NOTE]Die Dienstebenen „Business“ und „Web“ werden im September 2015 eingestellt. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zur Einstellung von Web Edition und Business Edition](https://msdn.microsoft.com/library/azure/dn741330.aspx). Ausführliche Informationen zum Aktualisieren vorhandener Web- und Business-Datenbanken auf die neuen Dienstebenen finden Sie unter [Upgrade von Web-/Business-Datenbanken der SQL-Datenbank auf neue Dienstebenen](sql-database-upgrade-new-service-tiers.md).

Dieses Dokument enthält eine Anleitung, mit deren Hilfe Sie ermitteln können, welche Dienstebene für Ihre Anwendung geeignet ist. Außerdem enthält es Empfehlungen zur Optimierung der Anwendung, damit Sie alle Vorteile von Azure SQL-Datenbank ausnutzen können.

>[AZURE.NOTE]In diesem Artikel geht es schwerpunktmäßig um die Verbesserung der Leistung für Einzeldatenbanken in SQL-Datenbank. Informationen zur Verbesserung der Leistung für elastische Datenbankpools finden Sie unter [Überlegungen zum Preis und zur Leistung eines elastischen Datenbankpools](sql-database-elastic-pool-guidance.md). Beachten Sie jedoch, dass viele Optimierungsempfehlungen in diesem Artikel für eine Einzeldatenbank auf Datenbanken in einem elastischen Pool mit ähnlichen Leistungsvorteilen angewendet werden können.

**Autoren:** Conor Cunningham, Kun Cheng, Jan Engelsberg

**Technische Bearbeiter:** Morgan Oslake, Joanne Marone, Keith Elmore, José Batista-Neto, Rohit Nayak

## Hintergrundinformationen zu Azure SQL-Datenbank

Um zu verstehen, wie die Dienstebenen Basic, Standard und Premium den Azure SQL-Datenbank-Dienst verbessern, ist es hilfreich, über ein gutes allgemeines Verständnis von Azure SQL-Datenbank zu verfügen. Sie können sich aus mehreren Gründen für Azure SQL-Datenbank entscheiden. Ein Grund ist die Vermeidung des langwierigen Zyklus, der mit dem Kaufen und Installieren von Hardware verbunden ist. Azure SQL-Datenbank ermöglicht Ihnen das flexible Erstellen und Verwerfen von Datenbanken, ohne dass Sie warten müssen, bis eine Bestellung genehmigt wird, Computer eintreffen, Stromversorgung und Kühlung angepasst werden oder die Installation durchgeführt wird. Microsoft übernimmt diese Aufgaben und sorgt so für eine erhebliche Verkürzung der Zeit, die von der Idee bis zur Lösung vergeht, indem Hardware basierend auf der zusammengefassten Nachfrage in den einzelnen Rechenzentren vorab bereitgestellt wird. So spart Ihr Unternehmen im Vergleich zum Kauf von Hardware und der manuellen Bereitstellung Wochen oder sogar Monate.

Microsoft stellt außerdem viele automatische Verwaltungsfunktionen in Azure SQL-Datenbank bereit, z. B. automatische hohe Verfügbarkeit, Lastenausgleich und integrierte Verwaltung.

### Automatische hohe Verfügbarkeit 
 Azure SQL-Datenbank hält mindestens drei Replikate für jede Benutzerdatenbank vor und verfügt über Logik zum automatischen synchronen Anwenden der Änderungen auf ein Quorum der Replikate per Commit. Dadurch wird sichergestellt, dass der Ausfall eines einzelnen Computers nicht zu Datenverlust führt. Außerdem wird jedes Replikat in einem anderen Hardware-Rack angeordnet, damit Stromausfälle oder der Ausfall von Netzwerkswitches keine Auswirkung auf Ihre Datenbank haben. Schließlich ist auch Logik zum automatischen Neuerstellen von Replikaten vorhanden, wenn ein Computer nicht mehr zu retten ist. Das System behält die gewünschten Integritätseigenschaften auch dann automatisch bei, wenn die Integrität eines Computers nicht mehr gewährleistet ist. Mit diesen Verfahren wird der zeitaufwändige Prozess vermieden, der heutzutage für das Installieren und Konfigurieren von Lösungen mit hoher Verfügbarkeit erforderlich ist. Wenn Sie für Ihre Daten über eine vorkonfigurierte Lösung mit hoher Verfügbarkeit verfügen, ist noch ein weiteres wichtiges Problem gelöst, das sich beim Erstellen einer geschäftskritischen Datenbanklösung mit herkömmlichen Methoden ergibt.

### Lastenausgleich 
 Im Gegensatz zu herkömmlichen virtuellen Computern enthält Azure SQL-Datenbank auch ein Verfahren, mit dem die Last automatisch auf mehrere Computer verteilt wird. Der Load Balancer überwacht die Ressourcenverwendung für einen Cluster dynamisch und verlagert Datenbankreplikate auf Computer im Cluster, um die Last dynamisch und auf faire Weise auf viele Benutzer zu verteilen. So wird die Funktion „On-Demand-Datenbankkapazität“ erweitert, und Benutzer können die Kapazitätsanforderungen für jede Datenbank unabhängig voneinander behandeln, da der Load Balancer Datenbanken mit hoher Auslastung per Migration an einen anderen Ort trennen kann. Beim Erstellen von Lösungen, die sich über viele Datenbanken erstrecken, bietet diese Logik eine Abstraktionsschicht, mit der Sie sich auf die Kapazitätsanforderungen der einzelnen Datenbanken konzentrieren können, anstatt auf die spezifischen Größenbeschränkungen eines virtuellen Computers.

### Integrierte Verwaltung 
 Azure SQL-Datenbank wird als Dienst ausgeführt. Dies bedeutet, dass es definierte Betriebszeitvorgaben für jede Datenbank gibt und Ausfallzeiten mit längeren Wartungsfenstern vermieden werden. Microsoft bietet für den Dienst eine Einzelanbieterlösung an, damit Sie sich bei Problemen nur an ein Unternehmen wenden müssen. Außerdem wird der Dienst von Microsoft ständig aktualisiert. Es werden Features und Kapazität hinzugefügt, und bei jedem Update wird nach Wegen gesucht, wie wir die Nutzung für Sie verbessern können. Updates werden transparent und ohne Ausfallzeitfenster durchgeführt. Dies bedeutet, dass Updates in unser normales Failoververfahren für hohe Verfügbarkeit integriert sind. So können Sie neue Features sofort nutzen, wenn sie verfügbar sind, und müssen nicht warten, bis während des nächsten Ausfallzeitfensters ein Serverupgrade durchgeführt wird.

All diese Funktionen werden für alle Dienstebenen bereitgestellt, und die Einstiegspreispunkte betragen nur einige US-Dollar pro Monat. Dies ist weitaus günstiger als das Kaufen und Ausführen eigener Server. Auch für sehr kleine Projekte können also die Vorteile von Azure genutzt werden, ohne dass hohe Kosten anfallen.

## Worin unterscheiden sich die Dienstebenen?

Microsoft hat während der Einführung von Azure SQL-Datenbank bei einigen Kunden eng mit diesen Kunden zusammengearbeitet, um herauszufinden, wie der Dienst genutzt wird, und Erkenntnisse für das Engineering Team in Bezug auf die zukünftige Planung von Features zu gewinnen. Während dieser Phasen konnten wir ermitteln, dass der Featuresatz für einige Arten von Kunden und deren Anforderungen gut geeignet war. Bei Startup-Unternehmen, die neue Clouddienste entwickeln, war es beispielsweise häufig der Fall, dass die Kombination aus On-Demand-Kapazität und verringertem Verwaltungsaufwand für eine Vereinfachung gesorgt und die bessere Konzentration auf das Kerngeschäft ermöglicht hat. Bei anderen Kunden ergaben sich in einigen Bereichen Probleme mit hohen Leistungsanforderungen, z. B. beim Warten einer zentralen API in einer großen Datenbanklösung mit mehreren Ebenen, die vom Azure SQL-Datenbank-Dienst noch nicht abgedeckt wurden. Das Feedback bestand darin, dass einige Kunden bereit waren, zur Erzielung eines sehr niedrigen Preispunkts eine höhere Varianz bei der Leistung zu akzeptieren. Andere Kunden waren jedoch eher an einer spezifischen Leistungsgarantie interessiert, damit sie basierend auf diesen Datenbanken leichter einen höheren Nutzen schaffen konnten.

Microsoft bietet drei Dienstebenen an, um diese unterschiedlichen Anforderungen zu erfüllen: Basic, Standard und Premium. Jede Dienstebene besitzt eine oder mehrere Leistungsebenen mit der entsprechenden Leistung, damit Sie Ihre Datenbanken auf vorhersagbare Weise ausführen können. Diese Leistung ist unter [Database Transaction Units (DTUs)](sql-database-technical-overview.md#understand-dtus) beschrieben.

Die Dienstebene Basic ist auf gute Vorhersagbarkeit der Leistung für jede Datenbankstunde ausgelegt. Die DTU einer Basic-Datenbank ist so konzipiert, dass sie für kleine Datenbanken ohne mehrere gleichzeitige Anforderungen ausreichende Ressourcen und eine gute Leistung bietet.

Die Dienstebene Standard bietet eine verbesserte Leistungsvorhersagbarkeit und ist für Datenbanken mit mehreren gleichzeitigen Anforderungen konzipiert, z. B. Arbeitsgruppen und Webanwendungen. Mit einer Datenbank der Dienstebene Standard können Sie die Größe Ihrer Datenbankanwendung basierend auf einer minutengenauen vorhersagbaren Leistung festlegen.

Die herausragende Funktion in Bezug auf die Leistung ist bei der Dienstebene Premium die sekundengenaue vorhersagbare Leistung für jede Premium-Datenbank. Mit der Dienstebene Premium können Sie die Größe Ihrer Datenbankanwendung basierend auf der Spitzenlast für die Datenbank festlegen und Fälle vermeiden, in denen die Leistungsvarianz dazu führen kann, dass kleine Abfragen bei latenzempfindlichen Vorgängen länger als erwartet dauern. Dieses Modell kann für eine starke Vereinfachung bei den Entwicklungs- und Produktprüfungszyklen für Anwendungen sorgen, bei denen in Bezug auf Ressourcenspitzenlast, Leistungsvarianz oder Abfragewartezeit hohe Anforderungen bestehen. Außerdem wird damit ermöglicht, einige lokale Anwendungen ohne größere Änderungen zu migrieren, da es sich um eine Umgebung handelt, die näher an der herkömmlichen isolierten Umgebung dieser Anwendungen zum Zeitpunkt ihrer ursprünglichen Erstellung liegt.

Ähnlich wie bei der Dienstebene Standard haben Sie bei der Dienstebene Premium die Wahl zwischen verschiedenen Leistungsebenen, und zwar basierend auf der gewünschten Isolation für einen Kunden.

Dank der Einstellungen für die Leistungsebene bei den Dienstebenen Standard und Premium müssen Sie nur für die Kapazität zahlen, die Sie benötigen, und Sie können die Kapazität leicht nach oben oder unten anpassen, wenn sich die Workload ändert. Falls Ihre Datenbankworkload beispielsweise während des Schulanfangs sehr hoch ist, können Sie die Leistungsebene der Datenbank für diesen Zeitraum erhöhen und nach Ende dieses Spitzenlast-Zeitfensters wieder verringern. Sie können die zu zahlenden Kosten reduzieren, indem Sie die Cloudumgebung an die Saisongebundenheit Ihres Unternehmens anpassen. Dieses Modell eignet sich auch gut für die Veröffentlichungszyklen von Softwareprodukten. Ein Testteam kann beispielsweise Kapazität zuordnen, während Testläufe durchgeführt werden, und die Kapazität freigeben, nachdem die Tests beendet sind. Diese Kapazitätsanforderungen passen gut zu dem Modell, bei dem Sie für Kapazität bezahlen, wenn Sie sie benötigen, und Ausgaben für dedizierte Ressourcen vermeiden, die selten genutzt werden. So erhalten Sie eine Leistung, die viel näher an dem herkömmlichen dedizierten Hardwaremodell liegt, das viele Microsoft-Kunden in Verbindung mit SQL Server genutzt haben. Auf diese Weise sollte für mehr Anwendungen eine einfachere Ausführung unter Azure SQL-Datenbank möglich sein.

Weitere Informationen zu Dienstebenen, Leistungsebenen und DTUs finden Sie unter [Dienst- und Leistungsebenen für Azure SQL-Datenbanken](sql-database-service-tiers.md).

## Gründe für die Verwendung der Dienstebenen

Jede Workload ist unterschiedlich, und der Zweck der Dienstebenen besteht darin, für Vorhersagbarkeit mit hoher Leistung für verschiedene Leistungsebenen zu sorgen. Kunden mit hohen Ressourcenanforderungen können ihre Datenbanken in einer dedizierteren Computingumgebung betreiben.

### Anwendungsfälle für die Dienstebene Basic:

- **Erste Schritte mit der Azure SQL-Datenbank**: Anwendungen, die sich in der Entwicklung befinden, benötigen häufig keine hohen Leistungsebenen. Basic-Datenbanken stellen eine ideale Umgebung für die Datenbankentwicklung zu einem niedrigen Preispunkt dar.
- **Datenbank mit einem einzelnen Benutzer**: Anwendungen, bei denen ein einzelner Benutzer einer Datenbank zugeordnet wird, verfügen normalerweise nicht über hohe Anforderungen in Bezug auf Parallelität und Leistung. Anwendungen mit diesen Anforderungen sind Kandidaten für die Dienstebene Basic.

### Anwendungsfälle für die Dienstebene Standard:

- **Datenbank mit mehreren gleichzeitigen Anforderungen**: Anwendungen, bei denen mehr als ein Benutzer zur gleichen Zeit verarbeitet wird, z. B. Websites mit relativ wenig Datenverkehr oder Anwendungen in Abteilungen, die eine größere Menge an Ressourcen benötigen, sind gute Kandidaten für die Dienstebene Standard.

### Anwendungsfälle für die Dienstebene Premium:

- **Hohe Spitzenlast**: Eine Anwendung, die zum Durchführen ihrer Vorgänge hohe Anforderungen in Bezug auf CPU, Arbeitsspeicher oder E/A aufweist. Falls ein Datenbankvorgang über einen längeren Zeitraum mehrere CPU-Kerne nutzt, ist dies ein Kandidat für die Verwendung von Premium-Datenbanken.
- **Viele gleichzeitige Anforderungen**: Einige Datenbankanwendungen verarbeiten viele gleichzeitige Anforderungen, z. B. bei einer Website mit hohem Datenverkehrsaufkommen. Für die Dienstebenen Basic und Standard gelten bei der Anzahl gleichzeitiger Anforderungen bestimmte Einschränkungen. Für Anwendungen, die mehr Verbindungen erfordern, muss eine angemessene Reservierungsgröße gewählt werden, um die maximale Anzahl von erforderlichen Anforderungen verarbeiten zu können.
- **Niedrige Latenz**: Für einige Anwendungen muss eine Reaktion der Datenbank in kürzester Zeit garantiert werden. Wenn eine bestimmte gespeicherte Prozedur im Rahmen eines größeren Kundenvorgangs aufgerufen wird, kann unter Umständen die Anforderung bestehen, dass die Rückgabe für diesen Aufruf in 99 % der Fälle innerhalb von maximal 20 Millisekunden erfolgt. Diese Art von Anwendung profitiert von Premium-Datenbanken, um sicherzustellen, dass Rechenleistung verfügbar ist.

Die genaue Ebene, die Sie benötigen, richtet sich nach den Spitzenlastanforderungen für jede Ressourcendimension. Bei einigen Anwendungen kann es so sein, dass sie nur einen geringen Anteil einer Ressource nutzen, dafür aber erhebliche Anforderungen in Bezug auf eine andere Ressource bestehen.

Weitere Informationen zu den Dienstebenen finden Sie unter [Dienst- und Leistungsebenen für Azure SQL-Datenbanken](sql-database-service-tiers.md).

## Grundlegendes zur Ressourcenverwendung

Der folgende Graph zeigt die CPU-Ressourcenverwendung für eine Premium-Datenbank mit Leistungsebene P2 für jede Stunde einer Woche. Dieser Graph beginnt mit einem Montag und zeigt fünf Arbeitstage und dann das Wochenende, an dem die Anwendung deutlich weniger gefragt ist.

![SQL-Datenbank-Ressourcenverwendung](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Die Daten verdeutlichen, dass diese Datenbank derzeit über eine CPU-Spitzenlast von etwas mehr als 50 % CPU-Auslastung relativ zur Leistungsebene P2 verfügt (Dienstagmittag). Falls die CPU der entscheidende Faktor im Ressourcenprofil der Anwendung ist, können Sie sich dafür entscheiden, dass P2 die richtige Leistungsebene ist, um die Bewältigung der Workload stets garantieren zu können. Wenn eine Anwendung im Laufe der Zeit voraussichtlich wachsen wird, ist es sinnvoll, für zusätzlichen Ressourcenpuffer zu sorgen, damit die Anwendung niemals die Obergrenze erreicht. Dies trägt zur Vermeidung von für Kunden sichtbaren Fehlern der Datenbank bei, die dadurch verursacht werden, dass nicht genügend Leistung zum effektiven Verarbeiten von Anforderungen vorhanden ist. Dies gilt besonders für latenzempfindliche Umgebungen (z. B. eine Datenbank zur Unterstützung einer Anwendung, bei der Webseiten basierend auf den Ergebnissen von Datenbankaufrufen farbig gestaltet werden).

Hierbei muss darauf hingewiesen werden, dass andere Anwendungstypen denselben Graph unter Umständen anders interpretieren. Wenn eine Anwendung beispielsweise jeden Tag versucht, Gehaltsabrechnungsdaten zu verarbeiten, und dasselbe Diagramm gilt, wird diese Art von „Batchauftrag“-Modell bei Leistungsebene P1 ggf. zufriedenstellend ausgeführt. Die Leistungsebene P1 verfügt über 100 DTUs, während Leistungsebene P2 über 200 DTUs verfügt. Dies bedeutet, dass die Leistungsebene P1 die Hälfte der Leistung von Leistungsebene P2 bietet. Also entsprechen 50 % CPU-Auslastung bei P2 einer CPU-Auslastung von 100 % bei P1. Sofern für die Anwendung keine Timeouts auftreten, spielt es unter Umständen keine Rolle, ob ein großer Auftrag 2 Stunden oder 2,5 Stunden dauert, solange er noch am selben Tag erledigt wird. Für eine Anwendung in dieser Kategorie reicht wahrscheinlich die Leistungsebene P1 aus. Sie können die Tatsache nutzen, dass es am Tag Zeiten gibt, in denen die Ressourcenverwendung niedriger ist. Dies bedeutet, dass „Spitzen“ ggf. in einen der Zeiträume später am Tag verlagert werden können. Die Leistungsebene P1 kann für eine Anwendung dieser Art gut geeignet sein (und Kosten sparen), solange die Aufträge jeden Tag rechtzeitig abgeschlossen werden können.

Azure SQL-Datenbank macht die verbrauchten Ressourceninformationen für jede aktive Datenbank in der Ansicht **sys.resource\_stats** der Masterdatenbank jedes Servers verfügbar. Die Daten in der Tabelle werden zu Intervallen von fünf Minuten zusammengefasst. Bei den Dienstebenen Basic, Standard und Premium kann es länger als fünf Minuten dauern, bis sie in der Tabelle angezeigt werden. Dies bedeutet, dass diese Daten besser für Verlaufsanalysen geeignet sind als für Analysen nahezu in Echtzeit. Beim Abfragen der Ansicht **sys.resource\_stats** wird der kürzliche Verlauf einer Datenbank angezeigt, um zu überprüfen, ob mit der gewählten Reservierung bei Bedarf die gewünschte Leistung erzielt wurde. Im folgenden Beispiel wird veranschaulicht, wie die Daten in dieser Ansicht verfügbar gemacht werden:

	SELECT TOP 10 * 
	FROM sys.resource_stats 
	WHERE database_name = 'resource1' 
	ORDER BY start_time DESC

![sys resource stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

>[AZURE.NOTE]Einige Spalten der Tabelle wurden aus Platzgründen abgeschnitten. Das Thema [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) enthält eine vollständige Beschreibung der Ausgabe.

## Überwachen der Ressourcenauslastung

In diesem Abschnitt werden Wege zum Überwachen der Ressourcenverwendung Ihrer Azure SQL-Datenbank und zum Vergleichen der aktuellen Ressourcenauslastung für verschiedene Leistungsebenen beschrieben.

1. Die Katalogansicht **sys.resource\_stats** wurde um weitere Verlaufsinformationen zur Ressourcenverwendung auf Datenbankebene erweitert. Wenn Sie sich beispielsweise die Ressourcenverwendung der Datenbank „userdb1“ für die letzte Woche ansehen möchten, können Sie die folgende Abfrage ausführen.
	
		SELECT * 
		FROM sys.resource_stats 
		WHERE database_name = 'userdb1' AND 
		      start_time > DATEADD(day, -7, GETDATE())
		ORDER BY start_time DESC;
	
2. Um auszuwerten, wie gut Ihre Workload zur Leistungsebene passt, müssen Sie die verschiedenen Aspekte der Ressourcenmetriken untersuchen: CPU, Lesevorgänge, Schreibvorgänge, Anzahl von Workern und Anzahl von Sitzungen. Hier wird von einer überarbeiteten Abfrage „sys.resource\_stats“ verwendet, um den Durchschnitt und die Höchstwerte dieser Ressourcenmetriken zu melden.
	
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
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
3. Mit den obigen Informationen zu den Durchschnitts- und Höchstwerten der Ressourcenmetriken können Sie bewerten, wie gut Ihre Workload zur gewählten Leistungsebene passt. In den meisten Fällen erhalten Sie mit den Durchschnittswerten aus „sys.resource\_stats“ eine gute Grundlage gegenüber der Zielgröße. Dies sollte Ihre primäre Messlatte sein. Wenn Sie beispielsweise die Dienstebene Standard mit Leistungsebene S2 verwenden, die durchschnittlichen Auslastungsprozentsätze für CPU, Lesevorgänge und Schreibvorgänge unter 40 % liegen, die durchschnittliche Anzahl von Workern unter 50 und die durchschnittliche Anzahl von Sitzungen unter 200 liegt, ist Ihre Workload unter Umständen gut für die Leistungsebene S1 geeignet. Es ist leicht zu erkennen, ob Ihre Datenbank die Grenzen für Worker und Sitzungen einhält. Um zu ermitteln, ob eine Datenbank in Bezug auf CPU, Lesevorgänge und Schreibvorgänge zu einer niedrigeren Leistungsebene passt, dividieren Sie die DTU-Anzahl der niedrigeren Leistungsebene durch die DTU-Anzahl Ihrer aktuellen Leistungsebene und multiplizieren das Ergebnis mit 100:
	
	**S1 DTU/S2 DTU*100 = 20/50*100 = 40**
	
	Das Ergebnis ist der relative Leistungsunterschied zwischen den beiden Leistungsebenen in Prozent. Wenn Ihre Auslastung diesen Prozentsatz nicht überschreitet, passt Ihre Workload ggf. in die niedrigere Leistungsebene. Sie sollten sich jedoch auch alle Bereiche der Ressourcenverwendung ansehen und in Prozent ermitteln, wie oft Ihre Datenbankworkload in die niedrigere Leistungsebene passt. Mit der folgenden Abfrage wird der Prozentsatz für die Eignung pro Ressourcendimension basierend auf dem oben berechneten Schwellenwert von 40 % ausgegeben.
	
		SELECT 
		    (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_physical_data_read_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data Read Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
	In Abhängigkeit Ihres Servicelevelziels (Service Level Objective, SLO) für die Datenbank können Sie entscheiden, ob Ihre Workload in die niedrigere Leistungsebene passt. Wenn der SLO-Wert für die Datenbankworkload 99,9 % beträgt und die obige Abfrage höhere Werte als 99,9 für all drei Ressourcendimensionen zurückgibt, ist die Wahrscheinlichkeit sehr hoch, dass Ihre Workload für die niedrigere Leistungsebene geeignet ist.
	
	Wenn Sie sich den Prozentsatz für die Eignung ansehen, erhalten Sie auch Informationen dazu, ob Sie zur nächsthöheren Leistungsebene wechseln müssen, um das Servicelevelziel zu erreichen. „userdb1“ weist beispielsweise die folgende Auslastung für die letzte Woche auf.
	
	| Durchschnittlicher CPU-Prozentwert | Maximaler CPU-Prozentwert |
	|---|---|
	| 24,5 | 100,00 |
	
	Der durchschnittliche CPU-Wert beträgt ca. ein Viertel der Obergrenze der Leistungsebene. Dies würde also gut zur Leistungsebene der Datenbank passen. Der Höchstwert zeigt jedoch, dass die Datenbank die Obergrenze der Leistungsebene erreicht. Müssen Sie also zur nächsthöheren Leistungsebene wechseln? Hierbei sollten Sie sich wieder ansehen, wie häufig Ihre Workload 100 % erreicht, und dies mit Ihrem Servicelevelziel für die Datenbankworkload vergleichen.
	
		SELECT 
		(COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent’
		,(COUNT(database_name) - SUM(CASE WHEN avg_physical_data_read_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data Read Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
	Wenn die obige Abfrage für eine der drei Ressourcendimensionen einen Wert unterhalb von 99,9 zurückgibt, sollten Sie entweder die Umstellung auf die nächsthöhere Leistungsebene erwägen oder Verfahren zur Anwendungsoptimierung nutzen, um die Last für die Azure SQL-Datenbank zu reduzieren.
	
4. Bei der obigen Vorgehensweise sollten Sie außerdem berücksichtigen, dass sich die Workload in Zukunft voraussichtlich erhöhen wird.

## Optimieren der Anwendung

Beim herkömmlichen lokalen Einsatz von SQL Server ist die anfängliche Kapazitätsplanung häufig von der Ausführung einer Anwendung in der Produktion getrennt. Anders ausgedrückt: Der Kauf von Hardware und der dazugehörigen Lizenzen für die Ausführung von SQL Server erfolgt vorher, und die Leistungsoptimierung wird erst danach durchgeführt. Bei der Verwendung von Azure SQL-Datenbank wird im Allgemeinen empfohlen, die Ausführung und Optimierung einer Anwendung parallel durchzuführen (dies ist auch vorteilhaft, da die Abrechnung monatlich erfolgt). Beim Modell des Zahlens für On-Demand-Kapazität können Sie Ihre Anwendung so optimieren, dass sie jeweils nicht mehr als die benötigten Ressourcen verbraucht, anstatt basierend auf Voraussagen zum zukünftigem Wachstum deutlich mehr Hardware als nötig bereitzustellen (wobei die Voraussagen häufig falsch sind, weil weit in die Zukunft geblickt werden muss). Beachten Sie, dass einige Kunden sich unter Umständen gegen die Optimierung einer Anwendung und stattdessen für die Mehrbereitstellung von Hardwareressourcen entscheiden. Dieser Ansatz kann sinnvoll sein, wenn eine wichtige Anwendung während einer Phase mit hoher Auslastung nicht geändert werden soll. Durch das Optimieren einer Anwendung können Ressourcenanforderungen minimiert und monatliche Rechnungen reduziert werden, wenn die Dienstebenen in Azure SQL-Datenbank verwendet werden.

### Anwendungsmerkmale

Die Dienstebenen sind für die Verbesserung der Leistungsstabilität und Vorhersagbarkeit für eine Anwendung ausgelegt, und es gibt einige bewährte Methoden, mit denen Sie Ihre Anwendung so optimieren können, dass dieses Feature besser genutzt wird. Für viele Anwendungen ergeben sich erhebliche Leistungssteigerungen, indem einfach auf eine höhere Leistungsebene oder Dienstebene umgestellt wird, aber die Vorteile sind ohne zusätzliche Optimierung nicht für alle Anwendungen gegeben. Für Anwendungen mit den folgenden Merkmalen sollte ebenfalls eine weitere Optimierung erwogen werden, um die Leistung beim Einsatz von Azure SQL-Datenbank weiter zu verbessern.

- **Anwendungen mit langsamer Leistung aufgrund von vielen Einzelaufrufen**: Hierzu gehören Anwendungen mit übermäßig vielen Datenzugriffsvorgängen, die empfindlich auf Netzwerklatenz reagieren. Anwendungen dieser Art erfordern unter Umständen eine Modifizierung, um die Anzahl von Datenzugriffsvorgängen für Azure SQL-Datenbank zu reduzieren. Beispielsweise kann die Anwendung verbessert werden, indem Verfahren wie das Zusammenfassen von Ad-hoc-Abfragen zu Batches oder das Verschieben von Abfragen in gespeicherte Prozeduren verwendet werden. Weitere Informationen finden Sie im folgenden Abschnitt „Zusammenfassen von Abfragen“.
- **Datenbanken mit intensiver Workload, die nicht von einem einzelnen Computer unterstützt werden können**: Datenbanken, die die Ressourcen der höchsten Premium-Leistungsebene übersteigen, sind keine guten Kandidaten. Diese Datenbanken profitieren unter Umständen vom horizontalen Hochskalieren der Workload. Weitere Informationen finden Sie in den folgenden Abschnitten „Datenbankübergreifendes Sharding“ und „Funktionale Partitionierung“.
- **Anwendungen, die suboptimale Abfragen enthalten**: Anwendungen, vor allem auf der Datenzugriffsschicht, die über unzureichend optimierte Abfragen verfügen, profitieren ggf. nicht wie erwartet von der Wahl einer höheren Leistungsebene. Dies umfasst auch Abfragen, die keine WHERE-Klausel aufweisen, bei denen Indizes fehlen oder die über veraltete Statistiken verfügen. Diese Anwendungen profitieren von standardmäßigen Verfahren zur Optimierung der Abfragenleistung. Weitere Informationen finden Sie in den folgenden Abschnitten „Fehlende Indizes“ und „Abfrageoptimierung/Abfragehinweise“.
- **Anwendungen mit suboptimalem Datenzugriffsdesign**: Anwendungen, die über inhärente Parallelitätsprobleme in Bezug auf den Datenzugriff verfügen, z. B. „Deadlocking“, profitieren ggf. nicht von der Wahl einer höheren Leistungsebene. Anwendungsentwickler sollten erwägen, Roundtrips für Azure SQL-Datenbank zu reduzieren, indem die Daten auf Clientseite zwischengespeichert werden (per Azure-Cachedienst oder mit anderen Cachetechnologien). Weitere Informationen finden Sie im folgenden Abschnitt zur Zwischenspeicherung auf Anwendungsebene.

## Optimierungsverfahren

In diesem Abschnitt werden einige Verfahren beschrieben, mit denen Sie Azure SQL-Datenbank so optimieren können, dass Sie für Ihre Anwendung die beste Leistung erzielen und für die Ausführung die kleinstmögliche Leistungsebene wählen können. Einige Verfahren sind mit herkömmlichen bewährten Methoden zum Optimieren von SQL Server identisch, aber die anderen Verfahren gelten speziell für Azure SQL-Datenbank. In einigen Fällen können herkömmliche SQL Server-Verfahren so erweitert werden, dass sie auch für Azure SQL-Datenbank funktionieren. Hierzu werden die verbrauchten Ressourcen für eine Datenbank untersucht, um Bereiche zu ermitteln, in denen eine weitere Optimierung möglich ist.

### Fehlende Indizes
Ein häufiges Problem in Bezug auf die OLTP-Datenbankleistung ist der physische Datenbankentwurf. Datenbankschemas werden häufig entworfen und bereitgestellt, ohne dass Skalierungstests durchgeführt werden (entweder in Bezug auf die Auslastung oder das Datenvolumen). Leider kann es passieren, dass die Leistung eines Abfrageplans auf niedriger Ebene akzeptabel ist, dann jedoch erheblich nachlassen kann, wenn Datenvolumina auf Produktionsebene verarbeitet werden müssen. Die häufigste Ursache dieses Problems ist das Fehlen geeigneter Indizes für Filter oder andere Einschränkungen in einer Abfrage. Dies manifestiert sich häufig als Tabellenscan, wenn eine Indexsuche ausreichend wäre.

Im folgenden Beispiel wird ein Fall erstellt, in dem der ausgewählte Abfrageplan einen Scan enthält, obwohl eine Suche ausreichen würde.

	DROP TABLE dbo.missingindex;
	CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
	DECLARE @a int = 0;
	SET NOCOUNT ON;
	BEGIN TRANSACTION
	WHILE @a < 20000
	BEGIN
	    INSERT INTO dbo.missingindex(col2) VALUES (@a);
	    SET @a += 1;
	END
	COMMIT TRANSACTION;
	GO
	SELECT m1.col1 
	FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1) 
	WHERE m1.col2 = 4;

![Abfrageplan mit fehlenden Indizes](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL-Datenbank enthält Funktionen, mit denen Datenbankadministratoren Hinweise dazu erhalten können, wie sie allgemeine fehlende Indexbedingungen finden und dies beheben können. Mit in Azure SQL-Datenbank integrierten dynamischen Verwaltungssichten (DMVs) wird die Abfragenkompilierung daraufhin untersucht, ob ein Index die geschätzten Kosten zum Ausführen einer Abfrage erheblich reduzieren würde. Während der Abfragenausführung wird nachverfolgt, wie häufig jeder Abfrageplan ausgeführt wird. Außerdem wird die geschätzte Differenz zwischen dem ausgeführten Abfrageplan und dem imaginären Abfrageplan mit dem Index ermittelt. So kann ein Datenbankadministrator schnell abschätzen, welche Änderungen am physischen Datenbankdesign zu einer Verbesserung der Workload-Gesamtkosten für eine bestimmte Datenbank und der tatsächlichen Workload führen können.

Die folgende Abfrage kann verwendet werden, um potenzielle fehlende Indizes zu ermitteln.

	SELECT CONVERT (varchar, getdate(), 126) AS runtime, 
	    mig.index_group_handle, mid.index_handle, 
	    CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact * 
	            (migs.user_seeks + migs.user_scans)) AS improvement_measure, 
	    'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' + 
	              CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + ' 
	              (' + ISNULL (mid.equality_columns,'') 
	              + CASE WHEN mid.equality_columns IS NOT NULL 
	                          AND mid.inequality_columns IS NOT NULL 
	                     THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
	              + ')' 
	              + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement, 
	    migs.*, 
	    mid.database_id, 
	    mid.[object_id]
	FROM sys.dm_db_missing_index_groups AS mig
	INNER JOIN sys.dm_db_missing_index_group_stats AS migs 
	    ON migs.group_handle = mig.index_group_handle
	INNER JOIN sys.dm_db_missing_index_details AS mid 
	    ON mig.index_handle = mid.index_handle
	ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

In diesem Beispiel wird der folgende Index empfohlen.

	CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Nach der Erstellung wird mit derselben SELECT-Anweisung jetzt ein anderer Plan ausgewählt, bei dem anstelle eines Scans eine Suche verwendet wird. Die Ausführung der Suche ist effizienter, wie im folgenden Abfrageplan zu sehen ist.

![Abfrageplan mit korrigierten Indizes](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

Die wichtigste Erkenntnis besteht darin, dass die E/A-Kapazität eines freigegebenen Warensystems im Allgemeinen mit mehr Einschränkungen als ein dedizierter Servercomputer versehen ist. Daher ist es wichtig, unnötige E/A-Vorgänge zu reduzieren, um das System innerhalb des DTU-Werts jeder Leistungsebene der Dienstebenen in Azure SQL-Datenbank bestmöglich zu nutzen. Die Wahl des richtigen Designs der physischen Datenbank kann die Latenz für einzelne Abfragen und den Durchsatz gleichzeitiger Anforderungen pro Skalierungseinheit erheblich verbessern und die erforderlichen Kosten zur Erfüllung der Abfrage reduzieren. Weitere Informationen zu den fehlenden Index-DMVs finden Sie unter [sys.dm\_db\_missing\_index\_details](https://msdn.microsoft.com/library/ms345434.aspx).

### Abfrageoptimierung/Abfragehinweise
Der Abfrageoptimierer in Azure SQL-Datenbank ähnelt dem herkömmlichen SQL Server-Abfrageoptimierer stark. Viele bewährte Methoden zum Optimieren von Abfragen und Verstehen der Einschränkungen des Argumentationsmodells für den Abfrageoptimierer gelten auch für Azure SQL-Datenbank. Das Optimieren von Abfragen in Azure SQL-Datenbank kann den zusätzlichen Vorteil haben, dass die zusammengefassten Ressourcenanforderungen reduziert werden können und eine Anwendung zu geringeren Kosten als eine nicht optimierte Version ausgeführt werden kann, weil eine niedrigere Leistungsebene möglich ist.

Ein häufiges Beispiel in SQL Server, das auch für Azure SQL-Datenbank gilt, bezieht sich auf die Art der Untersuchung („Sniffing“) von Parametern während der Kompilierung, um zu versuchen, einen noch besser optimierten Plan zu erstellen. Diese Untersuchung der Parameter ist ein Prozess, mit dem Abfrageoptimierer beim Kompilieren einer Abfrage den aktuellen Wert eines Parameters ermitteln, um nach Möglichkeit einen besseren Abfrageplan zu generieren. Diese Strategie kann zwar häufig zu einem Abfrageplan führen, der deutlich schneller als ein Plan ist, der ohne Kenntnis von Parameterwerten kompiliert wurde, aber das derzeitige SQL Server/Azure SQL-Datenbank-Verhalten ist nicht perfekt. Es gibt Fälle, in denen der Parameter nicht ermittelt wird, und es gibt Fälle, in denen der Parameter zwar ermittelt wird, der generierte Plan aber suboptimal für den gesamten Satz der Parameterwerte in einer Workload ist. Microsoft bindet Abfragehinweise (Direktiven) ein, damit Sie Ihre Absichten besser angeben und das Standardverhalten für die Parameterermittlung außer Kraft setzen können. Häufig können mit der Verwendung von Hinweisen Fälle behoben werden, in denen das standardmäßige Verhalten von SQL Server/Azure SQL-Datenbank für eine bestimmte Kundenworkload nicht perfekt ist.

Im folgenden Beispiel wird veranschaulicht, wie der Abfrageprozessor einen Plan generieren kann, der für Leistungs- und Ressourcenanforderungen suboptimal ist, und wie durch die Verwendung eines Abfragehinweises die Abfragelaufzeit und Ressourcenanforderungen unter Azure SQL-Datenbank reduziert werden können.

Unten folgt ein Beispiel für eine Einrichtung.

	DROP TABLE psptest1;
	CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
	
	DECLARE @a int = 0;
	SET NOCOUNT ON;
	BEGIN TRANSACTION
	WHILE @a < 20000
	BEGIN
	    INSERT INTO psptest1(col2) values (1);
	    INSERT INTO psptest1(col2) values (@a);
	    SET @a += 1;
	END
	COMMIT TRANSACTION
	CREATE INDEX i1 on psptest1(col2);
	GO
	
	CREATE PROCEDURE psp1 (@param1 int)
	AS
	BEGIN
	    INSERT INTO t1 SELECT * FROM psptest1 
	    WHERE col2 = @param1
	    ORDER BY col2;
	END
	GO
	
	CREATE PROCEDURE psp2 (@param2 int)
	AS
	BEGIN
	    INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
	    ORDER BY col2
	    OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
	END
	GO
	
	CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
	GO

Mit dem Einrichtungscode wird eine Tabelle erstellt, die eine „schiefe“ Datenverteilung enthält. Der optimale Abfrageplan variiert in Abhängigkeit davon, welcher Parameter ausgewählt wird. Leider wird die Abfrage mit dem Verhalten für die Zwischenspeicherung des Plans nicht immer basierend auf dem häufigsten Parameterwert neu kompiliert. Dies bedeutet, dass ein suboptimaler Plan auch dann zwischengespeichert und für viele Werte verwendet wird, wenn ein anderer Plan eine bessere Wahl eines durchschnittlichen Plans darstellen würde. Anschließend werden zwei gespeicherte Prozeduren erstellt, die identisch sind, jedoch mit der Ausnahme, dass eine Prozedur einen speziellen Abfragehinweis enthält (Erläuterung siehe unten).

**Beispiel (Teil 1):**

	-- Prime Procedure Cache with scan plan
	EXEC psp1 @param1=1;
	TRUNCATE TABLE t1;
	
	-- Iterate multiple times to show the performance difference
	DECLARE @i int = 0;
	WHILE @i < 1000
	BEGIN
	    EXEC psp1 @param1=2;
	    TRUNCATE TABLE t1;
	    SET @i += 1;
	END

**Beispiel (Teil 2: Bitte warten Sie 10 Minuten, bevor Sie diesen Teil ausprobieren, damit sich andere Telemetriedaten ergeben):**

	EXEC psp2 @param2=1;
	TRUNCATE TABLE t1;
	
	DECLARE @i int = 0;
	WHILE @i < 1000
	BEGIN
	    EXEC psp2 @param2=2;
	    TRUNCATE TABLE t1;
	    SET @i += 1;
	END

In jedem Teil dieses Beispiels wird versucht, eine parametrisierte Einfügeanweisung 1000-mal auszuführen (zum Generieren einer ausreichenden Last für einen Testdatensatz). Beim Ausführen von gespeicherten Prozeduren untersucht der Abfrageprozessor den Parameterwert, der während der ersten Kompilierung an die Prozedur übergeben wird (als „Parameterermittlung“ bezeichnet). Der sich ergebende Plan wird zwischengespeichert und auch dann für spätere Aufrufe verwendet, wenn der Parameterwert anders ist. Daher wird der optimale Plan unter Umständen nicht in allen Fällen verwendet. In einigen Fällen müssen Kunden den Optimierer zum Auswählen eines Plans leiten, der für den Durchschnittsfall besser geeignet ist, anstatt der spezielle Fall, für den die Abfrage kompiliert wurde. In diesem Beispiel wird vom anfänglichen Plan ein „Scanplan“ generiert. Dieser liest alle Zeilen, um die einzelnen Werte zu ermitteln, die mit dem Parameter übereinstimmen.

![Abfragenoptimierung](./media/sql-database-performance-guidance/query_tuning_1.png)

Da die Prozedur mit dem Wert 1 ausgeführt wurde, war der sich ergebende Plan für 1 optimal, aber für alle anderen Werte der Tabelle suboptimal. Das resultierende Verhalten ist voraussichtlich nicht das gewünschte Verhalten, wenn Sie jeden Plan zufällig auswählen, da der Plan dann eine langsamere Leistung aufweist und mehr Ressourcen für die Ausführung benötigt.

Das Ausführen des Tests mit „SET STATISTICS IO ON“ zeigt, welche logischen Scanvorgänge in diesem Beispiel im Hintergrund durchgeführt werden. Sie sehen, dass für den Plan 1148 Lesevorgänge erfolgen (dies ist ineffizient, wenn in den meisten Fällen nur eine Zeile zurückgegeben werden soll).

![Abfragenoptimierung](./media/sql-database-performance-guidance/query_tuning_2.png)

Im zweiten Teil des Beispiels wird ein Abfragehinweis verwendet, um den Optimierer anzuweisen, während des Kompilierungsprozesses einen bestimmten Wert zu verwenden. In diesem Fall wird der Abfrageprozessor gezwungen, den als Parameter übergebenen Wert zu ignorieren und stattdessen einen unbekannten Wert („UNKNOWN“) anzunehmen. Dies bedeutet, dass ein Wert mit der durchschnittlichen Häufigkeit in der Tabelle verwendet wird (unter Ignorierung der „Datenschiefe“). Der sich ergebende Plan ist ein suchbasierter Plan, der im Durchschnitt schneller und ressourcenschonender als der Plan aus Teil 1 des Beispiels ist.

![Abfragenoptimierung](./media/sql-database-performance-guidance/query_tuning_3.png)

Die Auswirkungen können ermittelt werden, indem die Tabelle **sys.resource\_stats** untersucht wird. (Hinweis: Es kommt zu einer Verzögerung ab dem Zeitpunkt, an dem Sie den Test ausführen, bis zum dem Zeitpunkt, an dem die Tabelle mit den Daten gefüllt wird.) In diesem Beispiel wurde Teil 1 während des Zeitfensters 22:25:00 und Teil 2 während des Zeitfensters 22:35:00 ausgeführt. Beachten Sie, dass für das frühere Zeitfenster mehr Ressourcen als für das spätere Zeitfenster verwendet wurden (aufgrund von Verbesserungen der Planeffizienz).

	SELECT TOP 1000 * 
	FROM sys.resource_stats 
	WHERE database_name = 'resource1' 
	ORDER BY start_time DESC

![Abfragenoptimierung](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE]Das hier verwendete Beispiel hat zwar absichtlich nur einen kleineren Umfang, aber die Auswirkungen von suboptimalen Parametern können beträchtlich sein, besonders für größere Datenbanken. Der Unterschied kann für die langsame und die schnelle Variante in Extremfällen zwischen dem Sekundenbereich und dem Stundenbereich liegen.

Sie können **sys.resource\_stats** überprüfen, um zu ermitteln, ob die Ressource für einen bestimmten Test mehr oder weniger Ressourcen als für einen anderen Test verwendet. Beim Vergleichen von Daten sollten Sie Tests zeitlich ausreichend trennen, damit sie sich in der Ansicht **sys.resource\_stats** nicht in demselben 5-Minuten-Zeitfenster bewegen. Beachten Sie außerdem, dass das Ziel dieser Übung die Minimierung der insgesamt verwendeten Ressourcen ist, und nicht die reine Minimierung der Ressourcen bei Spitzenlast. Im Allgemeinen führt eine Optimierung eines Codeabschnitts in Bezug auf die Latenz auch zu einem verringerten Ressourcenverbrauch. Stellen Sie sicher, dass die für eine Anwendung vorgesehenen Änderungen wirklich erforderlich sind und sich nicht negativ auf die Kundenerfahrung beim Nutzen von Anwendungen auswirken, wenn Abfragehinweise verwendet werden.

Wenn eine Workload eine Gruppe von sich wiederholenden Abfragen enthält, ist es häufig sinnvoll, den Optimierungsgrad dieser Planentscheidungen zu erfassen und zu überprüfen, da dies häufig Auswirkungen auf die Mindestgrößeneinheit für Ressourcen hat, die zum Hosten der Datenbank erforderlich ist. Nach der Überprüfung kann durch eine gelegentliche erneute Überprüfung dieser Pläne sichergestellt werden, dass keine Verschlechterung eingetreten ist. Weitere Informationen zu Abfragehinweisen finden Sie unter [Abfragehinweise (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### Datenbankübergreifendes Sharding
Da Azure SQL-Datenbank auf normaler Hardware ausgeführt wird, gelten für eine Einzeldatenbank meist niedrigere Kapazitätsgrenzen als für eine herkömmliche lokale SQL Server-Installation. Es gibt eine Reihe von Kunden, die Sharding-Verfahren (also das horizontale Partitionieren) verwenden, um Datenbankvorgänge auf mehrere Datenbanken zu verteilen, wenn diese nicht in die Grenzen für eine Einzeldatenbank in Azure SQL-Datenbank passen. Die meisten Kunden, die heutzutage Sharding-Verfahren für Azure SQL-Datenbank verwenden, teilen ihre Daten einer Dimension auf mehrere Datenbanken auf. Bei diesem Ansatz muss verinnerlicht werden, dass von OLTP-Anwendungen häufig Transaktionen durchgeführt werden, die nur für eine Zeile oder eine kleine Gruppe von Zeilen im Schema gelten. Wenn eine Datenbank beispielsweise Kunden, Bestellungen und Bestelldetails enthält (wie in der herkömmlichen Northwind-Beispieldatenbank in SQL Server), können diese Daten auf mehrere Datenbanken aufgeteilt werden. Hierzu wird ein Kunde mit den zugehörigen Bestellinformationen und -details gruppiert und dafür gesorgt, dass diese Daten in einer Einzeldatenbank verbleiben. Bei dieser Anwendung würden unterschiedliche Kunden auf verschiedene Datenbanken verteilt werden, um eine effektive Verteilung der Last auf mehrere Datenbanken zu erreichen. So können Kunden nicht nur die Erreichung der Obergrenze bei der Datenbankgröße vermeiden, sondern für Azure SQL-Datenbank auch die Verarbeitung von Workloads ermöglichen, die die Obergrenzen der unterschiedlichen Leistungsebenen deutlich überschreiten, solange jede einzelne Datenbank in die DTU passt.

Per Datenbank-Sharding wird die zusammengefasste Ressourcenkapazität für eine Lösung zwar nicht reduziert, aber dieses Verfahren ist hoch effektiv, was die Unterstützung sehr großer Lösungen über mehrere Datenbanken hinweg betrifft. Jede Datenbank kann mit einer anderen Leistungsebene ausgeführt werden, um sehr große „effektive“ Datenbanken mit hohen Ressourcenanforderungen zu unterstützen.

Beachten Sie, dass SQL-Datenbank jetzt über eine Bibliothek als Hilfe für das Sharding verfügt. Weitere Informationen finden Sie unter [Übersicht über die Clientbibliothek für elastische Datenbanken](sql-database-elastic-database-client-library.md).

### Funktionale Partitionierung
Es kommt häufig vor, dass SQL Server-Benutzer viele Funktionen in einer Einzeldatenbank kombinieren. Wenn eine Anwendung beispielsweise Logik zum Verwalten des Bestands für einen Store enthält, kann die Datenbank Logik für die Bereiche Bestand, Nachverfolgung von Bestellungen, gespeicherte Prozeduren und indizierte/materialisierte Sichten zum Verwalten der Berichterstellung für den Monatsabschluss sowie andere Funktionen enthalten. Dieses Verfahren hat den Vorteil, dass die Datenbank für Vorgänge wie BACKUP leicht verwaltet werden kann. Es erfordert aber auch, dass Sie die Größe der Hardware so bemessen, dass die Spitzenlast über alle Funktionen einer Anwendung hinweg bewältigt werden kann.

Innerhalb einer Architektur mit horizontaler Hochskalierung, die in Azure SQL-Datenbank verwendet wird, ist es häufig vorteilhaft, die Funktionen einer Anwendung auf unterschiedliche Datenbanken zu verteilen. So können die einzelnen Datenbanken unabhängig voneinander skaliert werden. Wenn die Auslastung einer Anwendung steigt (und somit auch die Auslastung der Datenbank), kann der Administrator die Leistungsebenen für jede Funktion in einer Anwendung unabhängig auswählen. Unter Berücksichtigung der Obergrenze kann eine Anwendung bei dieser Architektur eine Größe erreichen, die von einem einzelnen normalen Computer nicht mehr bewältigt werden kann, indem die Last auf mehrere Computer verteilt wird.

### Zusammenfassen von Abfragen
Für Anwendungen, bei denen mit sehr häufigen Ad-hoc-Abfragen auf Daten zugegriffen wird, wird ein Großteil der Reaktionszeit für die Netzwerkkommunikation zwischen der Anwendungsebene und der Azure SQL-Datenbankebene verbraucht. Auch wenn sowohl die Anwendung als auch Azure SQL-Datenbank in demselben Rechenzentrum angeordnet sind, kann die Netzwerklatenz zwischen den beiden Komponenten durch eine hohe Anzahl von Datenzugriffsvorgängen verstärkt werden. Um die Netzwerk-Roundtrips für diese Datenbankzugriffsvorgänge zu reduzieren, sollten Anwendungsentwickler erwägen, die Ad-hoc-Abfragen zusammenzufassen oder zu gespeicherten Prozeduren zu kompilieren. Das Zusammenfassen von Ad-hoc-Abfragen kann mehrere Abfragen in einem großen Batch innerhalb eines Vorgangs an Azure SQL-Datenbank senden. Mit dem Kompilieren von Ad-hoc-Abfragen in einer gespeicherten Prozedur kann das gleiche Ergebnis wie beim Zusammenfassen zu Batches erzielt werden. Ein weiterer Vorteil der Verwendung einer gespeicherten Prozedur ist die Erhöhung der Wahrscheinlichkeit, dass die Abfragepläne in Azure SQL-Datenbank für nachfolgende Ausführungen derselben gespeicherten Prozedur zwischengespeichert werden können.

Einige Anwendungen sind mit vielen Schreibvorgängen verbunden. In einigen Fällen ist es auch möglich, die E/A-Gesamtlast einer Datenbank zu reduzieren, indem geprüft wird, wie Schreibvorgänge zu Batches zusammengefasst werden können. Dies ist häufig so einfach wie die Verwendung von expliziten Transaktionen anstelle von Transaktionen mit automatischem Commit innerhalb von gespeicherten Prozeduren und Ad-hoc-Batches. Eine Auswertung unterschiedlicher Verfahren, die verwendet werden können, finden Sie unter [Stapelverarbeitungsverfahren für SQL-Datenbankanwendungen in Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimentieren Sie mit Ihrer eigenen Workload, um das richtige Modell für die Batcherstellung zu finden, und machen Sie sich klar, dass ein bestimmtes Modell unter Umständen über etwas andere Garantien in Bezug auf die Transaktionskonsistenz verfügen kann. Die Ermittlung der richtigen Workload, bei der die Ressourcenverwendung reduziert wird, erfordert die richtige Kombination aus Konsistenz und Abstrichen bei der Leistung.

### Zwischenspeichern auf Anwendungsebene
Einige Datenbankanwendungen enthalten Workloads mit einer hohen Zahl von Lesevorgängen. Sie können Caching-Schichten verwenden, um die Auslastung für die Datenbank zu reduzieren und ggf. die Leistungsebene zu reduzieren, die zum Unterstützen einer Datenbank mit Azure SQL-Datenbank erforderlich ist. [Azure Redis Cache](https://azure.microsoft.com/services/cache) ermöglicht Kunden, die über Workloads mit vielen Lesevorgängen verfügen, das einmalige Lesen der Daten (oder je nach Konfiguration ggf. einmal pro Computer auf Anwendungsebene) und das Speichern dieser Daten außerhalb von Azure SQL-Datenbank. Dies ermöglicht die Reduzierung der Datenbanklast (CPU und Lesevorgang-E/A), aber es kommt zu einer Auswirkung auf die Transaktionskonsistenz, da die aus dem Cache ausgelesenen Daten gegenüber den Daten in der Datenbank veraltet sein können. Es gibt zwar viele Anwendungen, bei denen eine gewisse Inkonsistenz akzeptabel ist, aber dies gilt nicht für alle Workloads. Machen Sie sich daher vollständig mit den Anwendungsanforderungen vertraut, bevor Sie eine Caching-Strategie auf Anwendungsebene anwenden.

## Zusammenfassung

Dank der Dienstebenen in Azure SQL-Datenbank verfügen Sie in Bezug auf die Typen von Anwendungen, die Sie in der Cloud erstellen, über mehr Flexibilität. In Kombination mit einer sorgfältigen Anwendungsoptimierung können Sie für Ihre Anwendung eine hohe und vorhersagbare Leistung erzielen. In diesem Dokument werden empfohlene Verfahren zum Optimieren des Ressourcenverbrauchs einer Datenbank und Ermitteln der Eignung für eine der Leistungsebenen beschrieben. Die Optimierung ist beim Cloudmodell ein fortlaufender Prozess, und die Dienstebenen und ihre Leistungsebenen ermöglichen Administratoren die Steigerung der Leistung, während die Kosten auf der Microsoft Azure Platform gesenkt werden.

<!---HONumber=Oct15_HO3-->