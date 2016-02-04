<properties
	pageTitle="Aktive Georeplikation für Azure SQL-Datenbank"
	description="In diesem Thema werden die aktive Georeplikation für Azure SQL-Datenbank und ihre Zwecke erklärt."
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
	ms.date="10/21/2015"
	ms.author="jroth" />

# Aktive Georeplikation für Azure SQL-Datenbank

## Übersicht
Das Feature „Aktive Georeplikation“ implementiert einen Mechanismus zum Bereitstellen von Datenbankredundanz innerhalb derselben Microsoft Azure-Region bzw. verschiedenen Regionen (Georedundanz). Bei aktiver Georeplikation werden Transaktionen mit ausgeführtem Commit aus einer Datenbank bis zu vier Kopien der Datenbank auf verschiedene Server asynchron repliziert. Die ursprüngliche Datenbank wird die primäre Datenbank der fortlaufenden Kopie. Jede fortlaufende Kopie wird als sekundäre Onlinedatenbank bezeichnet. Die primäre Datenbank repliziert Transaktionen mit ausgeführtem Commit asynchron in alle sekundären Onlinedatenbanken. Wenngleich die sekundären Onlinedaten stets ein wenig der primären Datenbank hinterherhinken, wird garantiert, dass die sekundären Onlinedaten hinsichtlich Transaktionsstatus stets konsistent mit Änderungen sind, für die in der primären Datenbank ein Commit erfolgt ist. Für die aktive Georeplikation werden bis zu vier sekundäre Onlinedatenbanken bzw. bis zu drei sekundäre Online- und eine Offlinedatenbank unterstützt.

Einer der Hauptvorteile der aktiven Georeplikation ist, dass es sich um eine Lösung für die Notfallwiederherstellung auf Datenbankebene handelt. Bei Verwenden der aktiven Georeplikation können Sie eine Benutzerdatenbank im Premium-Tarif so konfigurieren, dass Transaktionen auf verschiedene Microsoft Azure SQL-Datenbankserver innerhalb derselben oder in anderen Regionen repliziert werden. Regionsübergreifende Redundanz ermöglicht Anwendungen die Wiederherstellung nach einem dauerhaften Ausfall eines Datencenters aufgrund von Naturkatastrophen, schwerwiegendem menschlichen Versagen oder böswilligen Handlungen.

Ein weiterer zentraler Vorteil ist, dass die sekundären Onlinedatenbanken lesbar sind. Aus diesem Grund kann eine sekundäre Onlinedatenbank als Load Balancer für Leseworkloads, z. B. zur Berichterstellung, fungieren. Sie können für die Notfallwiederherstellung einerseits eine sekundäre Onlinedatenbank in einer anderen Regionen erstellen, andererseits aber auch eine sekundäre Onlinedatenbank in derselben Region auf einem anderen Server betreiben. Beide sekundären Onlinedatenbanken können verwendet werden, um reine Schreibworkloads von Clients gleichmäßig auf mehrere Regionen zu verteilen.

Es folgen weitere Szenarien, in denen die aktive Georeplikation verwendet werden kann:

- **Datenbankmigration**: Sie können die aktive Georeplikation zur Onlinemigration einer Datenbank von einem Server auf einen anderen bei minimalen Ausfallzeiten nutzen.
- **Anwendungsupgrades**: Sie können die sekundäre Onlinedatenbank als Failbackoption verwenden.

Um Geschäftskontinuität praktisch sicherzustellen, ist das Hinzufügen von Redundanz zwischen Datencentern zum relationalen Speicher nur ein Teil der Lösung. Für eine komplette Wiederherstellung einer Anwendung bzw. eines Diensts nach einem katastrophenbedingten Ausfall ist das Wiederherstellen aller Komponenten erforderlich, die den Dienst und alle abhängigen Dienste ausmachen. Beispiele dieser Komponenten sind die Clientsoftware (z. B. ein Browser mit benutzerdefiniertem JavaScript), Web-Front-Ends, Speicher und DNS. Es ist wichtig, dass alle Komponenten hinsichtlich derselben Fehler gegen Ausfälle geschützt und innerhalb des RTO (Recovery Time Objective) der Anwendung wieder verfügbar sind. Daher müssen Sie alle abhängigen Dienste bestimmen und mit dem Leistungsumfang und den Funktionen vertraut sein, die sie bieten. Dann müssen Sie entsprechende Maßnahmen ergreifen, um sicherzustellen, dass Ihr Dienst während des Failovers der Dienste funktioniert, von denen er abhängig ist. Weitere Informationen zum Entwerfen von Lösungen für die Notfallwiederherstellung finden Sie unter [Entwerfen von Cloudlösungen für die Notfallwiederherstellung mithilfe der aktiven Georeplikation](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## Möglichkeiten der aktiven Georeplikation
Das Feature „Aktive Georeplikation“ bietet die folgenden wichtigen Möglichkeiten:

- **Automatische asynchrone Replikation**: Nachdem für eine sekundäre Onlinedatenbank das Seeding erfolgt ist, werden Aktualisierungen an der primären Datenbank automatisch in die sekundäre Onlinedatenbank kopiert. Dies bedeutet, dass für Transaktionen in der primären Datenbank ein Commit erfolgt, ehe sie in die sekundäre Onlinedatenbank kopiert werden. Nach dem Seeding ist die sekundäre Onlinedatenbank jedoch jederzeit hinsichtlich Transaktionen konsistent. 

	>[AZURE.NOTE]Die asynchrone Replikation trägt der Wartezeit Rechnung, die für WANs typisch ist, mit denen Remotedatencenter verbunden werden.

- **Mehrere sekundäre Onlinedatenbanken**: Zwei oder mehr sekundäre Onlinedatenbanken sorgen für mehr Redundanz und Schutz für die primäre Datenbank und Anwendung. Wenn mehrere sekundäre Onlinedatenbanken vorhanden sind, bleibt die Anwendung auch bei Ausfall einer der sekundären Onlinedatenbanken geschützt. Wenn es nur eine sekundäre Onlinedatenbank gibt und diese ausfällt, ist die Anwendung bis zur Erstellung einer neuen sekundären Onlinedatenbank einem höheren Risiko ausgesetzt.

- **Lesbare sekundäre Onlinedatenbanken**: Eine Anwendung kann für schreibgeschützte Vorgänge mithilfe derselben Sicherheitsprinzipale auf eine sekundäre Onlinedatenbank zugreifen, die für den Zugriff auf die primäre Datenbank verwendet werden. Fortlaufende Kopiervorgänge in die sekundäre Onlinedatenbank haben Vorrang vor dem Anwendungszugriff. Wenn außerdem die Abfragen der sekundären Onlinedatenbank für längere Tabellensperren sorgen, können Transaktionen in der primären Datenbank letztlich misslingen.

- **Vom Benutzer gesteuerte Beendigung für Failover**: Bevor Sie für eine Anwendung ein Failover in eine sekundäre Onlinedatenbank ausführen können, muss die fortlaufende Kopierbeziehung mit der primären Datenbank beendet werden. Das Beenden der fortlaufenden Kopierbeziehung erfordert eine explizite Aktion durch die Anwendung oder ein administratives Skript oder manuell über das Portal. Nach der Beendigung wird die sekundäre Onlinedatenbank zu einer eigenständigen Datenbank. Sie wird eine Datenbank mit Lese-/ Schreibzugriff, es sein denn, die primäre Datenbank war eine schreibgeschützte Datenbank. Zwei Arten der [Beendigung einer fortlaufenden Kopierbeziehung](#termination-of-a-continuous-copy-relationship) werden weiter unten in diesem Thema beschrieben.

>[AZURE.NOTE]Die aktive Georeplikation wird nur vom Premium-Tarif unterstützt. Dies gilt sowohl für die primäre Datenbank und als auch sekundäre Onlinedatenbanken. Die sekundäre Onlinedatenbank muss so konfiguriert werden, dass sie dieselbe oder eine höhere Leistungsstufe als die primäre Datenbank bietet. Änderungen an Leistungsstufen der primären Datenbank werden nicht automatisch in die sekundären Datenbanken repliziert. Upgrades müssen zunächst in den sekundären Datenbanken und zuletzt in der primären Datenbank erfolgen. Weitere Informationen zum Ändern von Leistungsstufen finden Sie unter [Ändern von Leistungsstufen](sql-database-scale-up.md). Es gibt zwei Hauptgründe, warum die sekundäre Onlinedatenbank mindestens dieselbe Größe wie die primäre Datenbank haben sollte. Die sekundäre Datenbank muss über genügend Kapazität verfügen, um die replizierten Transaktionen mit derselben Geschwindigkeit wie die primäre zu verarbeiten. Wenn die sekundäre Datenbank nicht mindestens dieselbe Kapazität zum Verarbeiten der eingehenden Transaktionen hat, kann sie hinterherhinken und letztlich die Verfügbarkeit der primären Datenbank beeinträchtigen. Wenn die sekundäre Datenbank nicht über die gleiche Kapazität wie die primäre Datenbank verfügt, kann das Failover die Leistung und Verfügbarkeit der Anwendung verschlechtern.

## Fortlaufende Kopierbeziehung – Konzepte
Die Redundanz lokaler Daten und Wiederherstellung des Betriebs sind Standardmerkmale von Azure SQL-Datenbank. Jede Datenbank verfügt über eine primäre und zwei lokale Replikatdatenbanken, die sich im selben Datencenter befinden, sodass dieses Datencenter eine hohe Verfügbarkeit aufweist. Dies bedeutet, dass die Datenbanken für die aktive Georeplikation auch über redundante Replikate verfügen. Sowohl die primäre als auch die sekundäre Onlinedatenbank haben zwei sekundäre Replikate. Das primäre Replikat der sekundären Datenbank wird jedoch direkt vom fortlaufende Kopiermechanismus aktualisiert und kann keine von Anwendungen ausgelösten Aktualisierungen akzeptieren. Die folgende Abbildung veranschaulicht, wie die aktive Georeplikation die Datenbankredundanz auf zwei Azure-Regionen ausdehnt. Die Region, in der die primäre Datenbank gehostet wird, heißt primäre Region. Die Region, in der die sekundäre Onlinedatenbank gehostet wird, heißt sekundäre Region. In dieser Abbildung ist Nordeuropa die primäre Region. Westeuropa ist die sekundäre Region.

![Fortlaufende Kopierbeziehung](./media/sql-database-active-geo-replication/continuous-copy-relationships.gif)

Wenn die primäre Datenbank nicht verfügbar ist, wird durch Beenden der fortlaufenden Kopierbeziehung mit einer angegebenen sekundären Onlinedatenbank diese Onlinedatenbank zu einer eigenständigen Datenbank. Die sekundäre Onlinedatenbank übernimmt den Modus (Schreibgeschützt bzw. Lese-/Schreibzugriff) der primären Datenbank, der durch die Beendigung unverändert bleibt. Wenn die primäre Datenbank beispielsweise eine schreibgeschützte Datenbank ist, wird die sekundäre Onlinedatenbank nach der Beendigung zu einer schreibgeschützten Datenbank. An diesem Punkt kann ein Failover für die Anwendung erfolgen, die weiter die sekundäre Onlinedatenbank verwenden kann. Um bei einem katastrophenbedingten Ausfall des Datencenters oder einem längeren Ausfall der primären Region für Stabilität zu sorgen, muss sich mindestens eine sekundäre Onlinedatenbank in einer anderen Region befinden.

## Erstellen einer fortlaufenden Kopie
Sie können nur von einer vorhandenen Datenbank eine fortlaufende Kopie erstellen. Das Erstellen einer fortlaufenden Kopie einer vorhandenen Datenbank eignet sich zum Hinzufügen von Georedundanz. Eine fortlaufende Kopie kann auch erstellt werden, um eine vorhandene Datenbank auf einen anderen Server mit SQL Azure-Datenbank zu kopieren. Nach der Erstellung wird die sekundäre Datenbank mit den Daten aufgefüllt, die aus der primären Datenbank kopiert wurden. Dieser Prozess wird als Seeding bezeichnet. Nach Abschluss des Seedings wird jede neue Transaktion repliziert, nachdem ein Commit in der primären Datenbank erfolgt ist.

Informationen zum Erstellen einer fortlaufenden Kopie einer vorhandenen Datenbank finden Sie unter [Aktivieren der Georeplikation](sql-database-business-continuity-design.md#how-to-enable-geo-replication).

## Verhindern des Verlusts wichtiger Daten
Aufgrund der hohen Latenz von WANs wird für die fortlaufende Kopie ein asynchroner Replikationsmechanismus verwendet. Dadurch wird Datenverlust unvermeidlich, sobald ein Ausfall auftritt. Bei einigen Anwendung dürfen jedoch ggf. keine Daten verloren gehen. Um diese kritischen Aktualisierungen zu schützen, kann ein Anwendungsentwickler die Systemprozedur [sp\_wait\_for\_database\_copy\_sync](https://msdn.microsoft.com/library/dn467644.aspx) aufrufen, unmittelbar nachdem der Commit für die Transaktion erfolgt ist. Das Aufrufen von **sp\_wait\_for\_database\_copy\_sync** blockiert den aufrufenden Thread solange, bis die letzte Transaktion mit erfolgtem Commit in die sekundäre Onlinedatenbank repliziert wurde. Die Prozedur wartet, bis alle in der Warteschlange befindlichen Transaktionen von der sekundären Onlinedatenbank bestätigt wurden. **sp\_wait\_for\_database\_copy\_sync** ist auf eine bestimmte fortlaufende Kopierverknüpfung begrenzt. Jeder Benutzer mit den Rechten zum Herstellen der Verbindung mit der primären Datenbank kann diese Prozedur aufrufen.

>[AZURE.NOTE]Die Verzögerung durch einen Aufruf der Prozedur **sp\_wait\_for\_database\_copy\_sync** kann erheblich sein. Die Verzögerung hängt von der Länge der Warteschlange und der verfügbaren Bandbreite ab. Vermeiden Sie den Aufruf dieser Prozedur, es sei denn, er ist unbedingt erforderlich.

## Beenden einer fortlaufenden Kopierbeziehung
Die fortlaufende Kopierbeziehung kann jederzeit beendet werden. Durch Beenden einer fortlaufenden Kopierbeziehung wird die sekundäre Datenbank entfernt. Es gibt zwei Methoden zum Beenden einer fortlaufenden Kopierbeziehung:

- Eine **geplante Beendigung** eignet sich für geplante Vorgänge, bei denen Datenverluste nicht akzeptabel sind. Eine geplante Beendigung kann für die primäre Datenbank erst erfolgen, nachdem für die sekundäre Onlinedatenbank ein Seeding erfolgt ist. Bei einer geplanten Beendigung werden alle Transaktionen, für die in der primären Datenbank ein Commit erfolgt ist, zuerst in die sekundäre Onlinedatenbank repliziert. Danach wird die fortlaufende Kopierbeziehung beendet. Dadurch wird der Verlust von Daten in der sekundären Datenbank vermieden.
- Eine **ungeplante (erzwungene) Beendigung** dient zum Reagieren auf den Verlust von entweder der primären Datenbank oder einer ihrer sekundären Onlinedatenbanken. Eine erzwungene Beendigung kann für die primäre Datenbank oder die sekundäre Datenbank erfolgen. Jede erzwungene Beendigung führt zum unwiderruflichen Verlust der Replikationsbeziehung zwischen der primären Datenbank und der zugehörigen sekundären Onlinedatenbank. Darüber hinaus bewirkt eine erzwungene Beendigung den Verlust sämtlicher Transaktionen, die nicht aus der primären Datenbank repliziert wurden. Bei einer erzwungenen Beendigung wird die fortlaufende Kopierbeziehung sofort beendet. Laufende Transaktionen werden nicht in die sekundäre Onlinedatenbank repliziert. Aus diesem Grund kann eine erzwungene Beendigung zum unwiderruflichen Verlust von Transaktionen führen, die nicht aus der primären Datenbank repliziert wurden.

>[AZURE.NOTE]Wenn die primäre Datenbank nur eine fortlaufende Kopierbeziehung hat, sind nach der Beendigung Aktualisierungen an der primären Datenbank nicht mehr geschützt.

Weitere Informationen zum Beenden einer fortlaufenden Kopierbeziehung finden Sie unter [Wiederherstellen einer Azure SQL-Datenbank nach einem Ausfall](sql-database-disaster-recovery.md).

## Nächste Schritte
Weitere Informationen zur aktiven Georeplikation und zu zusätzlichen Geschäftskontinuitätsfeatures der SQL-Datenbank finden Sie unter [Geschäftskontinuität – Übersicht](sql-database-business-continuity.md).

<!---HONumber=AcomDC_0121_2016-->
