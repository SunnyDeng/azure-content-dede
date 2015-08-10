<properties
   pageTitle="Dienstebenen der SQL-Datenbank"
   description="Vergleichen Sie die Features für Leistung und Geschäftskontinuität der verschiedenen Dienstebenen der Azure SQL-Datenbank, um das richtige Gleichgewicht zwischen Kosten und Funktionalität für eine bedarfsgerechte Skalierung ohne Ausfallzeiten zu finden."
   services="sql-database"
   documentationCenter=""
   authors="shontnew"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="07/24/2015"
   ms.author="shkurhek"/>

# Dienstebenen

Für die [SQ-Datenbank](sql-database-technical-overview.md) sind die Dienstebenen Basic, Standard und Premium verfügbar. Alle drei Dienstebenen haben eine Betriebszeit-[SLA](http://azure.microsoft.com/support/legal/sla/) von 99,99 % und bieten vorhersagbare Leistung, flexible Optionen für Geschäftskontinuität, Sicherheitsfeatures und stündliche Abrechnung. Mit mehreren Leistungsstufen innerhalb jeder Dienstebene haben Sie die Flexibilität, die Ebene auszuwählen, die Ihren Arbeitsauslastungsanforderungen am besten entspricht. Wenn Sie nach oben oder unten skalieren müssen, können Sie die Ebenen Ihrer Datenbank im Azure-Portal problemlos ändern, und zwar ohne Ausfallzeiten der Anwendung. Ausführlichere Informationen finden Sie unter [Ändern der Dienstebenen und -Leistungsstufen von Datenbanken](https://msdn.microsoft.com/library/azure/dn369872.aspx).

> [AZURE.IMPORTANT]Web- und Business-Editionen werden eingestellt. Erfahren Sie, wie Sie ein [Upgrade von Web- und Business-Editionen](sql-database-upgrade-new-service-tiers.md) durchführen. Bitte informieren Sie sich unter [Häufig gestellte Fragen zur Einstellung von Web und Business Edition](http://azure.microsoft.com/pricing/details/sql-database/web-business/), wenn Sie Web- und Business-Editionen weiterhin verwenden möchten.

## Basic

Die Dienstebene "Basic" ist für Anwendungen mit einer leichten Transaktionsarbeitsauslastung vorgesehen. Ein typischer Anwendungsfall ist eine einfache Anwendung, die eine kleine Datenbank mit einem einzelnen Vorgang zu jedem beliebigen Zeitpunkt benötigt.

**Leistung, Größe und Features**


| Dienstebene | Basic |
| :-- | :-- |
| Datenbankdurchsatzeinheiten \(DTUs\) | 5 |
| Maximale Datenbankgröße | 2 GB |
| Zeitpunktwiederherstellung \(PITR\) | Bis zu Millisekunde innerhalb der letzten 7 Tage |
| Notfallwiederherstellung. | Geowiederherstellung, Wiederherstellung für jede beliebige Azure-Region |
| Leistungsziele | Transaktionsrate pro Stunde |


## Standard

Die Dienstebene "Standard" ist die richtige Wahl für erste Schritte mit Transaktionsarbeitsauslastungen. Sie bietet gegenüber der Ebene "Basic" eine bessere Leistung und bessere integrierte Funktionen für Geschäftskontinuität. Ein typischer Anwendungsfalle ist eine Anwendung mit mehreren gleichzeitigen Transaktionen.

**Leistung und Größe**


| Dienstebene | Standard S0 | Standard S1 | Standard S2 | Standard S3 |
| :-- | :-- | :-- | :-- | :-- |
| Datenbankdurchsatzeinheiten \(DTUs\) | 10 | 20 | 50 | 100 |
| Maximale Datenbankgröße | 250 GB | 250 GB | 250 GB | 250 GB |


**Merkmale**


| Dienstebene | Standard \(S0, S1, S2, S3\) |
| :-- | :-- |
| Zeitpunktwiederherstellung \(PITR\) | Bis zu Millisekunde innerhalb der letzten 14 Tage |
| Notfallwiederherstellung. | Geografische Standardreplikation, 1 sekundäre offline |
| Leistungsziele | Transaktionsrate pro Minute |


## Premium

Die Dienstebene "Premium" ist für unternehmenskritische Anwendungen ausgelegt. Sie bietet das höchste Maß an Leistung und den Zugriff auf erweiterte Funktionen für Geschäftskontinuität, darunter Aktive geografische Replikation in bis zu 4 Azure-Regionen Ihrer Wahl. Ein typischer Anwendungsfall ist eine betriebsnotwendige Anwendung mit hohem Transaktionsvolumen und vielen gleichzeitigen Benutzern.

**Leistung und Größe**


| Dienstebene | Premium P1 | Premium P2 | Premium P6 \(früher P3\) |
| :-- | :-- | :-- | :-- |
| Datenbankdurchsatzeinheiten \(DTUs\) | 125 | 250 | 1000 |
| Maximale Datenbankgröße | 500 GB | 500 GB | 500 GB |


**Merkmale**


| Dienstebene | Premium \(P1, P2, P6\) |
| :-- | :-- |
| Zeitpunktwiederherstellung \(PITR\) | Bis zu Millisekunde innerhalb der letzten 35 Tage |
| Notfallwiederherstellung. | Aktive geografische Replikation, bis zu 4 lesbare sekundäre Onlinedatenbanken |
| Leistungsziele | Transaktionsrate pro Sekunde |


Erfahren Sie mehr über die Preise für diese Ebenen unter [SQL-Datenbank Preise](http://azure.microsoft.com/pricing/details/sql-database/).

Nachdem Sie jetzt die Ebenen der SQL-Datenbank kennen, können Sie sie mit einer [kostenlosen Testversion](http://azure.microsoft.com/pricing/free-trial/) ausprobieren und sich mit der [Erstellung Ihrer ersten SQL-Datenbank](sql-database-get-started.md) befassen!
 

<!---HONumber=July15_HO5-->