<properties
	pageTitle="Preis- und Leistungsaspekte von elastischen Datenbankpools für Azure SQL-Datenbank"
	description="Ein elastischer Datenbankpool stellt eine Sammlung der verfügbaren Ressourcen dar, die von einer Gruppe von elastischen Datenbanken gemeinsam verwendet werden. Dieses Dokument enthält Anleitungen, die Ihnen helfen sollen, die Eignung eines elastischen Datenbankpools für eine Gruppe von Datenbanken einzuschätzen."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/16/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Überlegungen zum Preis und zur Leistung eines elastischen Datenbankpools

Schätzen Sie ein, ob ein Pool für elastische Datenbanken für eine Datenbankgruppe im Hinblick auf die Datenbankverwendungsmuster und die Preisunterschiede zwischen einem Pool für elastische Datenbanken und einzelnen Datenbanken für Sie kostengünstiger ist. Darüber hinaus sind weitere Anleitungen enthalten, die Sie bei der Ermittlung der aktuellen Poolgröße für eine vorhandene Reihe von SQL-Datenbanken unterstützen.

- Eine Übersicht zu Pools finden Sie unter [SQL-Datenbank – Pool für elastische Datenbanken](sql-database-elastic-pool.md).
- Detaillierte Informationen zu elastischen Datenbankpools finden Sie unter [Referenz: Elastische Datenbankpools für SQL-Datenbanken](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE] Pools für elastische Datenbanken sind als Vorschauversion ausschließlich für Server mit SQL-Datenbank V12 verfügbar.

## Elastische Datenbankpools

SaaS-Entwickler erstellen Anwendungen, die auf der obersten von umfangreichen Datenebenen aufsetzen, die wiederum aus zahlreichen Datenbanken bestehen können. In einem üblichen Anwendungsmuster wird jedem Kunden eine eigene Datenbank zur Verfügung gestellt. Verschiedene Kunden weisen jedoch unterschiedliche und unvorhersehbare Verwendungsmuster auf, und es ist schwierig, den Ressourcenbedarf der einzelnen Datenbankbenutzer vorherzusagen. Daher stellt der Entwickler möglicherweise zu viele Ressourcen bereit, um den bestmöglichen Datendurchsatz und die optimale Antwortzeit für alle Datenbanken zu gewährleisten. Das ist jedoch mit erheblichen Kosten verbunden. Alternativ stellt der Entwickler weniger Ressourcen bereit und riskiert eine schlechtere Leistung für seine Kunden.

Mit Pools in Azure SQL-Datenbanken sind SaaS-Entwickler in der Lage, das Preis-Leistungs-Verhältnis einer Gruppe von Datenbanken im Rahmen eines vorgegebenen Budgets zu optimieren und gleichzeitig eine flexible Leistung für jede Datenbank sicherzustellen. Mit Pools können ISV elastische Datenbanktransaktionseinheiten (Elastic Database Transaction Units, eDTUs) für einen Pool erwerben, der von mehreren Datenbanken genutzt wird, um unvorhersehbare Auslastungszeiten von einzelnen Datenbanken abzufedern. Die eDTU-Anforderungen eines Pools werden anhand der zusammengefassten Auslastung der hierin befindlichen Datenbanken ermittelt. Die Anzahl der für den Pool verfügbaren eDTUs wird vom Budget des Entwicklers gesteuert. Pools vereinfachen es dem Entwickler, die Auswirkungen des Budgets auf die Leistung des Pools und umgekehrt abzuwägen. Der Entwickler fügt dem Pool einfach Datenbanken hinzu, legt höchstmögliche eDTU-Garantien für die Datenbanken fest und bestimmt dann basierend auf dem vorhandenen Budget die eDTUs des Pools. Mithilfe von Pools können Entwickler ihre Dienste problemlos und kontinuierlich ausbauen, von schlanken Startups bis hin zu etablierten Unternehmen.
## Wann sollten Sie einen Pool in Betracht ziehen?

Pools eignen sich hervorragend für eine große Anzahl an Datenbanken mit spezifischen Nutzungsmustern. Im Hinblick auf eine einzelne Datenbank wird dieses Muster durch eine geringe durchschnittliche Auslastung mit relativ wenigen Nutzungslastspitzen gekennzeichnet.

Je mehr Datenbanken Sie einem Pool hinzufügen können, desto größer werden Ihre Ersparnisse. Abhängig vom Auslastungsmuster Ihrer Anwendung können Sie bereits mit nur zwei S3-Datenbanken Ersparnisse erzielen.

Die folgenden Abschnitte helfen Ihnen einzuschätzen, ob Ihre Datenbanksammlung von einem Pool profitieren kann. In den Beispielen wird von Standardpools ausgegangen. Die Grundlagen gelten jedoch auch für Basic- und Premiumpools.

### Bewerten der Datenbankauslastungsmuster

In der folgenden Abbildung finden Sie ein Beispiel für eine Datenbank, die sich in der Regel im Leerlauf befindet, bei der es allerdings auch regelmäßig zu Auslastungsspitzen kommt. Dieses Auslastungsmuster eignet sich hervorragend für einen Pool:

   ![eine einzelne, für einen Pool geeignete Datenbank](./media/sql-database-elastic-pool-guidance/one-database.png)

In dem vorstehend dargestellten Zeitraum von fünf Minuten schnellt DB1 auf bis zu 90 DTUs. Die mittlere Auslastung liegt jedoch bei unter 5 DTUs. Zum Ausführen dieser Workload in einer einzelnen Datenbank ist eine S3-Leistungsstufe erforderlich. Allerdings bewirkt dies, dass die meisten Ressourcen in Zeiten geringer Aktivität nicht verwendet werden.

Mit einem Pool können diese ungenutzten DTUs auf mehrere Datenbanken verteilt werden, wodurch sich die Gesamtmenge der erforderlichen DTUs und damit auch die Gesamtkosten verringern.

Ausgehend vom vorherigen Beispiel nehmen wir einmal an, dass es weitere Datenbanken mit ähnlichen Auslastungsmustern wie DB1 gibt. In den beiden folgenden Abbildungen wird die Auslastung von einmal vier, und von weiteren 20 Datenbanken in jeweils einem Graphen dargestellt. Dies soll veranschaulichen, dass sich die Auslastung der Datenbanken zeitlich nicht überlappt:

   ![vier Datenbanken mit einem für einen Pool geeigneten Auslastungsmuster](./media/sql-database-elastic-pool-guidance/four-databases.png)

   ![20 Datenbanken mit einem für einen Pool geeigneten Auslastungsmuster](./media/sql-database-elastic-pool-guidance/twenty-databases.png)

Die zusammengefasste DTU-Auslastung aller 20 Datenbanken wird in der vorstehenden Abbildung durch die schwarze Linie dargestellt. Diese zeigt, dass die zusammengefasste DTU-Auslastung niemals 100 DTUs übersteigt, und gibt an, dass die 20 Datenbanken in diesem Zeitraum 100 eDTUs gemeinsam nutzen können. Dies führt zu einer 20-fachen Verringerung im Hinblick auf die DTUs und einer 13-fachen Preisreduzierung im Vergleich zur Positionierung jeder Datenbank in S3-Leistungsstufen für Einzeldatenbanken.


Dieses Beispiel ist aus den folgenden Gründen ideal:

- Es gibt große Unterschiede zwischen der Spitzenauslastung und der mittleren Auslastung pro Datenbank.  
- Die Spitzenauslastung jeder Datenbank ergibt sich zu jeweils unterschiedlichen Zeitpunkten.
- Die eDTUs werden von einer großen Anzahl Datenbanken gemeinsam genutzt.

Der Preis eines Pools ergibt sich aus den eDTUs des Pools. Während der Preis pro eDTU für einen Pool 1,5 Mal höher als der Preis pro DTU für eine einzelne Datenbank ist, können hierbei **Pool-eDTUs von vielen Datenbanken gemeinsam genutzt werden, daher liegt der Preis in vielen Fällen unter der Gesamtanzahl der benötigten eDTUs**. Die Unterschiede bei der Preisgestaltung und die gemeinsame Nutzung von eDTUs sind die Basis des Einsparungspotenzials, das von Pools geboten wird.

Die folgenden Faustregeln hinsichtlich der Anzahl der Datenbanken und ihrer Auslastung sollen Ihnen helfen, sicherzustellen, dass Pools kosteneffizienter als Leistungsebenen für einzelne Datenbanken sind.

### Minimale Anzahl Datenbanken

Wenn die Summe der DTUs für Leistungsstufen für einzelne Datenbanken mehr als das 1,5-fache der für den Pool benötigten eDTUs entspricht, ist ein elastischer Pool kostengünstiger. Informationen zu den verfügbaren Größen finden Sie unter [eDTUs und Speicherbeschränkungen für elastische Datenbankpools und elastische Datenbanken](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).

***Beispiel***<br> Es werden mindestens zwei S3-Datenbanken oder mindestens 15 S0-Datenbanken benötigt, damit ein Pool mit 100 eDTUs kosteneffizienter als die Verwendung von Leistungsebenen für einzelne Datenbanken ist.

### Maximale Anzahl von gleichzeitig unter Spitzenlast laufenden Datenbanken

Bei der gemeinsamen Nutzung von eDTUs können nicht alle Datenbanken in einem Pool gleichzeitig eDTUs bis zum verfügbaren Grenzwert verwenden, wenn Leistungsstufen für einzelne Datenbanken verwendet werden. Je weniger Datenbanken gleichzeitig auf Spitzenlast genutzt werden, desto niedriger kann die Pool-eDTU festgelegt werden und desto kosteneffizienter wird der Pool. Im Allgemeinen sollten nicht mehr als zwei Drittel der Datenbanken (67 %) im Pool gleichzeitig den eDTU-Grenzwert erreichen.

***Beispiel***<br> Um die Kosten für drei S3-Datenbanken in einem Pool mit 200 eDTUs zu senken, können höchstens zwei dieser Datenbanken gleichzeitig mit Spitzenauslastung ausgeführt werden. Andernfalls müsste der Pool auf mehr als 200 eDTUs ausgelegt werden, wenn mehr als zwei dieser vier S3-Datenbanken gleichzeitig mit Spitzenauslastung ausgeführt werden. Wenn die Größe des Pools auf mehr als 200 eDTUs geändert wird, müssten weitere S3-Datenbanken zum Pool hinzugefügt werden, damit die Kosten unterhalb der Leistungsstufe für einzelne Datenbanken bleiben.

Beachten Sie, dass in diesem Beispiel die Auslastung anderer Datenbanken im Pool nicht berücksichtigt wird. Wenn alle Datenbanken zu einem gegebenen Zeitpunkt eine Spitzenauslastung aufweisen, können weniger als zwei Drittel (67 %) der Datenbanken gleichzeitig eine Spitzenauslastung aufweisen.

### DTU-Auslastung pro Datenbank

Ein großer Unterschied zwischen der mittleren und der Spitzenauslastung einer Datenbank weist darauf hin, dass es längere Zeiträume mit geringer Auslastung und kurze Zeiträume mit hoher Auslastung gibt. Dieses Auslastungsmuster eignet sich ideal für die gemeinsame, datenbankübergreifende Nutzung von Ressourcen. Eine Datenbank eignet sich für die Aufnahme in einen Pool, wenn die Spitzenauslastung etwa um das 1,5-fache höher als die mittlere Auslastung ist.

***Beispiel***<br> Eine S3-Datenbank, die zu Spitzenzeiten 100 DTUs und im Durchschnitt 67 DTUs oder weniger benötigt, ist ein guter Kandidat für die gemeinsame Nutzung von eDTUs in einem Pool. Alternativ ist auch eine S1-Datenbank, die zu Spitzenzeiten 20 DTUs und im Durchschnitt 13 DTUs oder weniger benötigt, ein guter Kandidat für einen Pool.

## Größenanpassung eines elastischen Pools

Die optimale Größe eines Pools hängt von den zusammengefassten eDTUs und den Speicherressourcen ab, die für alle Datenbanken im Pool benötigt werden. Dies setzt voraus, dass Sie die größere der beiden folgenden Mengen ermitteln:

* Maximale Anzahl DTUs, die von allen Datenbanken im Pool verwendet werden.
* Maximale Speicherbytes, die von allen Datenbanken im Pool verwendet werden.

Informationen zu den verfügbaren Größen finden Sie unter [eDTUs und Speicherbeschränkungen für elastische Datenbankpools und elastische Datenbanken](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).

Die SQL-Datenbank ermittelt automatisch den Verlauf der Ressourcennutzung von Datenbanken auf einem vorhandenen SQL-Datenbankserver und empfiehlt eine geeignete Poolkonfiguration im Azure-Portal. Zusätzlich zu den Empfehlungen enthält die Datenbank eine integrierte Funktion, die die eDTU-Nutzung einer benutzerdefinierten Gruppe von Datenbanken auf dem Server schätzt. Dadurch können Sie eine Was-wäre-wenn-Analyse durchführen, indem Sie interaktiv Datenbanken zum Pool hinzufügen und entfernen. So können Sie die Ressourcennutzung ermitteln und eine Orientierungshilfe für die Größenanpassung erhalten, bevor Sie Ihre Veränderungen committen. Eine Anleitung finden Sie unter [Monitor, manage, and size an elastic pool ](sql-database-elastic-pool-manage-portal.md) (Einen elastischen Pool überwachen, verwalten und skalieren).

Tipps für eine flexiblere Ressourcennutzung, die Ad-hoc-Schätzungen der Servergröße für V12-Vorgängerversionen erleichtern, sowie Tipps für die Einschätzung von Datenbanken auf verschiedenen Servern finden Sie unter [PowerShell script for identifying databases suitable for an elastic database pool](sql-database-elastic-pool-database-assessment-powershell.md) (Für einen Pool geeignete elastische Datenbanken mithilfe eines PowerShell-Skripts ermitteln).

| Funktion | Portalfunktion|	PowerShell-Skript|
|:---------------|:----------|:----------|
| Granularität | 15 Sekunden | 15 Sekunden
| Berücksichtigt Preisunterschiede zwischen einem Pool und Leistungsebenen für einzelne Datenbanken| Ja| Nein
| Ermöglicht die Anpassung der Liste der analysierten Datenbanken| Ja| Ja
| Ermöglicht die Anpassung des Zeitraums der Analyse| Nein| Ja
| Ermöglicht die Anpassung der Liste der Datenbanken, die auf verschiedenen Servern analysiert wurden| Nein| Ja
| Ermöglicht die Anpassung der Liste der Datenbanken, die auf V11-Servern analysiert wurden| Nein| Ja

Wenn Sie keine Tools verwenden können, kann Ihnen die folgende Anleitung dabei helfen, einzuschätzen, ob ein Pool kostengünstiger als eine einzelne Datenbank ist:

1.	Schätzen Sie wie folgt die Menge der für den Pool benötigten eDTUs: MAX(<*Gesamtanzahl Datenbanken* X *durchschnittliche DTU-Auslastung pro Datenbank*>, <*Anzahl von gleichzeitig unter Spitzenlast ausgeführten Datenbanken* X *Spitzen-DTU-Auslastung pro Datenbank*)
2.	Schätzen Sie den für den Pool benötigten Speicherplatz, indem Sie die Menge der für alle Datenbanken im Pool benötigten Bytes addieren. Ermitteln Sie dann die eDTU-Poolgröße, die diese Menge an Speicher bietet. Informationen zu den durch die eDTU-Poolgröße bedingten Speicherbegrenzung des Pools finden Sie unter [eDTUs und Speicherbeschränkungen für elastische Pools und elastische Datenbanken](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).
3.	Nehmen Sie die größere der eDTU-Schätzungen aus Schritt 1 und Schritt 2.
4.	Auf der [SQL-Datenbank Preisseite](https://azure.microsoft.com/pricing/details/sql-database/) finden Sie die kleinste eDTU-Poolgröße, die größer ist als die Schätzung aus Schritt 3.
5.	Vergleichen Sie den Poolpreis aus Schritt 5 mit dem Preis der geeigneten Leistungsstufen für einzelne Datenbanken.

## Zusammenfassung

Nicht alle einzelnen Datenbanken sind gute Kandidaten für Pools. Datenbanken mit Auslastungsmustern, die sich durch geringe durchschnittliche Auslastung und relativ seltene Auslastungsspitzen auszeichnen, sind herausragende Kandidaten. Die Auslastungsmuster von Anwendungen sind dynamisch. Nutzen Sie also die Informationen und Tools aus diesem Artikel, um grob einzuschätzen, ob ein Pool für einige oder alle Ihre Datenbanken die richtige Wahl ist. Dieser Artikel ist lediglich ein erster Schritt, um Sie bei der Entscheidung, inwiefern ein elastischer Pool eine geeignete Lösung ist, zu unterstützen. Denken Sie daran, den Verlauf der Ressourcennutzung kontinuierlich zu überwachen, und dass auch die Leistungsebenen aller Ihrer Datenbanken stetig neu bewertet werden müssen. Vergessen Sie nicht, dass Sie Datenbanken ganz einfach in oder aus elastischen Pools verschieben können, und wenn Sie über eine sehr große Anzahl von Datenbanken verfügen, können Sie auch unterschiedliche Pools mit unterschiedlichen Größen erstellen, auf die Sie Ihre Datenbanken verteilen.

## Nächste Schritte

- [Erstellen eines elastischen Datenbankpools](sql-database-elastic-pool-create-portal.md)
- [Einen Pool für elastische Datenbanken überwachen, verwalten und skalieren](sql-database-elastic-pool-manage-portal.md)
- [Referenz zu Pools für elastische Datenbanken](sql-database-elastic-pool-reference.md)
- [SQL-Datenbankoptionen und -leistung: Grundlegendes zum Angebot in den einzelnen Tarifen](sql-database-service-tiers.md)
- [PowerShell script for identifying databases suitable for an elastic database pool](sql-database-elastic-pool-database-assessment-powershell.md) (PowerShell-Skript zum Ermitteln von Datenbanken mit Eignung für einen Pool für elastische Datenbanken)

<!---HONumber=AcomDC_0316_2016-->