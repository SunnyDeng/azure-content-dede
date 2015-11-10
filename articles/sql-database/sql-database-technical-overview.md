<properties
   pageTitle="Was ist SQL Database | Microsoft Azure"
   description="Entdecken Sie die technischen Details und Funktionen der Azure SQL-Datenbank, das Microsoft Managementsystem für relationale Datenbanken (RDBMS) und die PaaS-Lösung in der Cloud."
   services="sql-database"
   documentationCenter=""
   authors="shontnew"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2015"
   ms.author="shkurhek"/>

# Einführung in SQL-Datenbank

SQL-Datenbank ist ein relationaler Datenbankdienst in der Cloud, der auf der marktführenden Microsoft SQL Server Engine basiert und betriebsnotwendige Funktionen beinhaltet. SQL-Datenbank bietet vorhersagbare Leistung, Skalierbarkeit ohne Ausfallzeiten, Geschäftskontinuität, Datenschutz und nahezu keinerlei Verwaltungsaufwand. Anstatt auf die Verwaltung virtueller Computer und der Infrastruktur können Sie sich auf die schnelle Entwicklung von Apps und eine beschleunigte Markteinführung konzentrieren. Da SQL-Datenbank auf dem [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx)-Modul basiert, werden vorhandene SQL Server-Tools, -Bibliotheken und -APIs unterstützt, sodass diese einfacher in die Cloud verschoben und auf die Cloud ausgedehnt werden können.

In diesem Artikel werden die grundlegenden Konzepte und Funktionen von SQL-Datenbank im Hinblick auf Leistung, Skalierbarkeit und Verwaltbarkeit mit Links zu detaillierten Informationen eingeführt. Wenn Sie sich damit vertraut gemacht haben, können Sie binnen Minuten [eine erste SQL-Datenbank erstellen](sql-database-get-started.md) oder [einen Pool für elastische Datenbanken erstellen und verwalten](sql-database-elastic-pool-portal.md). Wenn Sie eingehendere Informationen benötigen, finden Sie diese in diesem 30 Minuten langen Video.


> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]


## Anpassen von Leistung und Skalierung ohne Ausfallzeiten
Für SQL-Datenbanken sind die *Dienstebenen* Basic, Standard und Premium verfügbar. Jede Dienstebene bietet [verschiedene Leistungsstufen und Funktionen](sql-database-service-tiers.md) zur Unterstützung geringer und hoher Datenbankworkloads. Sie können Ihre erste App mit einer kleinen Datenbank zu einem geringen Preis pro Monat erstellen und dann jederzeit manuell oder programmgesteuert [die Dienstebene ändern](sql-database-scale-up.md), wenn Ihre App weltweit genutzt wird, ohne dass Ausfallzeiten für die App oder Ihre Kunden entstehen.

Für viele Unternehmen und Apps genügt es, wenn Datenbanken erstellt werden können und sich die Leistung von Einzeldatenbanken nach oben oder unten anpassen lässt, insbesondere, wenn die Nutzungsmuster relativ vorhersagbar sind. Bei unvorhersagbaren Nutzungsmustern kann es jedoch schwer sein, die Kosten und Ihr Geschäftsmodell zu verwalten.

Dieses Problem lässt sich mit [Pools für elastische Datenbanken](sql-database-elastic-pool.md) in SQL-Datenbank beheben. Das Konzept ist denkbar einfach. Sie weisen einem Pool eine bestimmte Leistung zu und zahlen für die gesamte Leistung des Pools und nicht für die Leistung der Einzeldatenbanken. Sie müssen die Datenbankleistung nicht nach oben oder unten anpassen. Die Datenbanken in dem Pool, die sogenannten *elastischen Datenbanken*, werden automatisch nach Bedarf zentral hoch- und herunterskaliert. Elastische Datenbanken nutzen die Grenzwerte des Pools, überschreiten sie jedoch nicht, sodass Ihre Kosten vorhersagbar bleiben, selbst wenn sich die Nutzung der Datenbanken nicht prognostizieren lässt. Darüber hinaus können Sie [Datenbanken im Pool hinzufügen und entfernen](sql-database-elastic-pool-portal.md) und so Ihre App von einigen wenigen auf Tausende Datenbanken skalieren, und das in einem kontrollierten Kostenrahmen.

Ob Sie sich nun für Einzeldatenbanken oder elastische Datenbanken entscheiden, ist dies keine endgültige Entscheidung. Sie können Einzeldatenbanken mit Pools für elastische Datenbanken kombinieren und die Dienstebenen von Einzeldatenbanken und Pools ändern, um innovative Anwendungsdesigns zu erhalten. Außerdem können Sie mit der Leistungsstärke und Reichweite von Azure einzelne Azure-Dienste mit der SQL-Datenbank kombinieren, um Ihre individuellen Anforderungen an modernes App-Design zu erfüllen, Kosten- und Ressourceneffizienz zu fördern und neue Geschäftsmöglichkeiten zu erschließen.

Aber wie können Sie nun die relative Leistung von Datenbanken und Datenbankpools vergleichen? Wie entscheiden Sie sich beim Anpassen für die richtige Lösung? Die Antwort liegt in den Datenbank-Transaktionseinheiten (Database Transaction Unit, DTU) für Einzeldatenbanken und in den elastischen DTUs (eDTUs) für elastische Datenbanken und Datenbankpools.

## Grundlegendes zu DTUs

[AZURE.INCLUDE [Beschreibung von SQL-Datenbank-DTUs](../../includes/sql-database-understanding-dtus.md)]

## Aufrechterhalten von App-Ausführung und Geschäftskontinuität

Auf der Basis eines globalen Netzwerks von Microsoft-verwalteten Rechenzentren können wir für Azure eine branchenführende Verfügbarkeit von 99,99 % mit einer entsprechenden Vereinbarung zum Servicelevel [(SLA)](http://azure.microsoft.com/support/legal/sla/) sicherstellen. Ihre Apps werden also wirklich rund um die Uhr ausgeführt. Mit jeder SQL-Datenbank nutzen Sie den integrierten Datenschutz und die Fehlertoleranz, die Sie andernfalls entwerfen, kaufen, erstellen und verwalten müssten. Abhängig von Ihren Geschäftsanforderungen benötigen Sie möglicherweise dennoch weitere Schutzebenen, um sicherzustellen, dass Ihre App und Ihr Geschäftsbetrieb bei einem Notfall, einem Fehler o. Ä. schnell wiederhergestellt werden kann. Mit SQL-Datenbank bietet jede Dienstebene ein unterschiedliches Menü an Funktionen, über die Sie die Wiederherstellung durchführen können. Über die Point-in-Time-Wiederherstellung können Sie den früheren Status einer Datenbank für bis zu 35 Tage wiederherstellen. Bei einem Ausfall des Rechenzentrums, das Ihre Datenbanken hostet, können Sie darüber hinaus Datenbankreplikate in einer anderen Region nutzen. Zudem können Sie Replikate für Aktualisierungen oder zur Verlegung in andere Regionen verwenden.

![SQL-Datenbank-Georeplikation](./media/sql-database-technical-overview/azure_sqldb_map.png)


Ausführliche Informationen zu den unterschiedlichen Funktionen der Geschäftskontinuität bei den verschiedenen Dienstebenen finden Sie unter [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md).

## Sichern der Daten
SQL Server bietet traditionell eine solide Datensicherheit, die SQL-Datenbank mit Funktionen zur Zugriffseinschränkung, zum Datenschutz und zur Überwachung von Aktivitäten fortführt. Eine kurze Übersicht über die Sicherheitsoptionen in der SQL-Datenbank finden Sie unter [Sichern der SQL-Datenbank](sql-database-security.md). Ausführlichere Informationen zu den Sicherheitsfunktionen finden Sie unter [Sicherheitscenter für SQL Server-Datenbankmodul und Azure SQL-Datenbank](https://msdn.microsoft.com/library/bb510589). Schließlich finden Sie im [Azure Trust Center](http://azure.microsoft.com/support/trust-center/security/) Informationen zur Sicherheit der Azure-Plattform.

## Nächste Schritte

- Unter [Preise](http://azure.microsoft.com/pricing/details/sql-database/) finden Sie Preisangaben und Messgrößen für Einzeldatenbanken und elastische Datenbanken.

- Beginnen Sie mit dem [Erstellen einer ersten Azure SQL-Datenbank](sql-database-get-started.md). Erstellen Sie dann Ihre erste App in [C#](sql-database-connect-query.md), [Java](sql-database-develop-java-simple-windows.md), [Node.js](sql-database-develop-nodejs-simple-windows.md), [PHP](sql-database-develop-php-retry-windows.md), [Python](sql-database-develop-python-simple-windows.md) oder [Ruby](sql-database-develop-ruby-simple-linux).

<!---HONumber=Nov15_HO2-->