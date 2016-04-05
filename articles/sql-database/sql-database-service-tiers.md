<properties
	pageTitle="SQL-Datenbank-Leistung und -optionen: Tarife | Microsoft Azure"
	description="Vergleich Sie die verschiedenen Leistungs- und Geschäftskontinuitätsoptionen der SQL-Datenbanktarife, um beim Skalieren Kosten und Funktionalität im Blick zu behalten."
	keywords="Datenbankoptionen, Datenbankleistung"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="jeffreyg"/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="03/25/2016"
	ms.author="jeffreyg"/>

# SQL-Datenbankoptionen und -leistung: Grundlegendes zum Angebot in den einzelnen Tarifen

[Azure SQL-Datenbank](sql-database-technical-overview.md) umfasst mehrere Dienstebenen für unterschiedliche Workloads. Sie können Dienstebenen jederzeit und ohne Ausfallzeiten der Anwendung ändern. Sie haben auch die Möglichkeit zum [Erstellen einer Einzeldatenbank](sql-database-get-started.md) mit definierten Merkmalen und Preisen. Oder Sie können mehrere Datenbanken verwalten, indem Sie einen [Pool für elastische Datenbanken erstellen](sql-database-elastic-pool-create-portal.md). In beiden Fällen sind die Dienstebenen **Basic**, **Standard** und **Premium** verfügbar. Die Datenbankoptionen dieser Ebenen sind bei Einzeldatenbanken und elastischen Pools ähnlich, bei elastischen Pools sind jedoch weitere Aspekte zu beachten. Dieser Artikel enthält ausführliche Informationen zu den Dienstebenen für Einzeldatenbanken und elastische Datenbanken.

## Tarife und Datenbankoptionen
Die Dienstebenen "Basic", "Standard" und "Premium" haben alle eine Betriebszeit-SLA von 99,99 % und bieten vorhersagbare Leistung, flexible Optionen für Geschäftskontinuität, Sicherheitsfeatures und stündliche Abrechnung. In der folgenden Tabelle sind Beispiele für Dienstebenen aufgeführt, die sich für unterschiedliche Anwendungsworkloads am besten eignen.

| Dienstebene | Zielworkloads |
|---|---|
| **Basic** | Eignet sich am besten für eine kleine Datenbank, in der normalerweise ein einzelner aktiver Vorgang zu einem bestimmten Zeitpunkt unterstützt wird. Beispiele hierfür sind Datenbanken, die für die Entwicklung, für Tests oder für kleine, selten verwendete Anwendungen verwendet werden. |
| **Standard** | Die richtige Wahl für die meisten Cloudanwendungen mit Unterstützung mehrerer gleichzeitiger Abfragen. Beispiele hierfür sind Arbeitsgruppen-oder Webanwendungen. |
| **Premium** | Konzipiert für hohe Transaktionsvolumen, unterstützt eine große Anzahl gleichzeitiger Benutzer und erfordert die höchste Stufe der Funktionen für Geschäftskontinuität. Beispiele hierfür sind Datenbanken für unternehmenskritische Anwendungen. |

>[AZURE.NOTE] Web Edition und Business Edition sind nicht mehr verfügbar. Informieren Sie sich unter [Häufig gestellte Fragen zur Einstellung von Web und Business Edition](https://azure.microsoft.com/pricing/details/sql-database/web-business/), wenn Sie Web- und Business-Editionen weiterhin verwenden möchten.

## Tarife und Leistungsstufen für Einzeldatenbanken
Bei Einzeldatenbanken gibt es mehrere Leistungsstufen auf den einzelnen Dienstebenen. Sie können die Ebene auswählen, die Ihren Anforderungen an Workloads am besten entspricht. Wenn Sie zentral hoch- oder herunterskalieren müssen, können Sie die Ebenen Ihrer Datenbank problemlos und **ohne Ausfallzeiten der Anwendung** ändern. Ausführlichere Informationen finden Sie unter [Ändern der Dienstebenen und -Leistungsstufen von Datenbanken](sql-database-scale-up.md).

Die hier aufgeführten Leistungsmerkmale gelten für Datenbanken, die mit [SQL-Datenbank V12](sql-database-v12-whats-new.md) erstellt wurden. In Situationen, in denen die zugrunde liegende Hardware in Azure mehrere Datenbanken hostet, erhält Ihre Datenbank immer noch einen garantierten Satz von Ressourcen, und die erwarteten Leistungsmerkmale Ihrer Datenbank sind nicht betroffen.

[AZURE.INCLUDE [Tarife für SQL-Datenbank](../../includes/sql-database-service-tiers-table.md)]

Weitere Informationen zu DTUs finden Sie in diesem Thema im Abschnitt [DTU](#understanding-dtus).

>[AZURE.NOTE] Eine ausführliche Beschreibung aller anderen Zeilen in dieser Dienstebenentabelle finden Sie unter [Service tier capabilities and limits](sql-database-performance-guidance.md#service-tier-capabilities-and-limits) (Funktionen und Limits von Dienstebenen; in englischer Sprache).

## Dienstebenen und Leistung für elastische Pools in eDTUs
Neben dem Erstellen und Skalieren einer Einzeldatenbank haben Sie die Möglichkeit, mehrere Datenbanken in einem [elastischen Pool](sql-database-elastic-pool.md) zu verwalten. Alle Datenbanken in einem elastischen Pool nutzen einen gemeinsamen Satz von Ressourcen. Die Leistungsmerkmale werden in *Transaktionseinheiten für elastische Datenbanken* (eDTUs) gemessen. Wie für Einzeldatenbanken verfügen Pools über drei Dienstebenen: **Basic**, **Standard** und **Premium**. Auch für Pools werden mit diesen drei Dienstebenen die Leistungsgrenzen und verschiedene Funktionen definiert.

Durch Pools können elastische Datenbanken DTU-Ressourcen teilen und nutzen, ohne dass den Datenbanken im Pool eine bestimmte Leistungsstufe zugewiesen werden muss. Beispielsweise kann eine Einzeldatenbank in einem Pool der Dienstebene „Standard“ 0 eDTU bis hin zum maximalen eDTU-Wert für Datenbanken nutzen, den Sie beim Konfigurieren des Pools festlegen. Dadurch können mehrere Datenbanken mit unterschiedlichen Workloads die für den gesamten Pool verfügbaren eDTU-Ressourcen effizient nutzen. Weitere Informationen finden Sie unter [Überlegungen zum Preis und zur Leistung eines elastischen Pools](sql-database-elastic-pool-guidance.md).

In der folgenden Tabelle sind die Merkmale der Dienstebenen eines Pools beschrieben.

[AZURE.INCLUDE [Tabelle der SQL-Datenbank-Dienstebenen für elastische Datenbanken](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Für jede Datenbank in einem Pool gelten auch die Merkmale für Einzeldatenbanken für die entsprechende Dienstebene. Beispielsweise liegt für einen Pool mit dem Tarif „Basic“ die maximale Grenze bei 2.400 - 28.800 Sitzungen, für eine Einzeldatenbank in diesem Pool ist jedoch ein Limit von 300 Sitzungen definiert (entsprechend der Angabe für eine Einzeldatenbank mit dem Tarif „Basic“ im vorhergehenden Abschnitt).

## Grundlegendes zu DTUs

[AZURE.INCLUDE [Beschreibung von SQL-Datenbank-DTUs](../../includes/sql-database-understanding-dtus.md)]

## Nächste Schritte
- Erfahren Sie mehr über die Preise für diese Ebenen unter [SQL-Datenbank Preise](https://azure.microsoft.com/pricing/details/sql-database/).
- Erfahren Sie mehr über [Pools für elastische Datenbanken](sql-database-elastic-pool-guidance.md) und [Überlegungen zum Preis und zur Leistung eines Pools für elastische Datenbanken](sql-database-elastic-pool-guidance.md).
- Erfahren Sie, wie Sie [einen elastischen Pool überwachen und verwalten und seine Größe ändern](sql-database-elastic-pool-manage-portal.md) und [die Leistung von Einzeldatenbanken überwachen](sql-database-single-database-monitor.md).
- Nachdem Sie jetzt die Ebenen der SQL-Datenbank kennen, können Sie sie mit einem [kostenlosen Konto](https://azure.microsoft.com/pricing/free-trial/) ausprobieren und sich mit der [Erstellung Ihrer ersten SQL-Datenbank](sql-database-get-started.md) befassen.

<!---HONumber=AcomDC_0330_2016-->