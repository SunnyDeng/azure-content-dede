<properties pageTitle="Überwachen der Datenbankleistung in Azure SQL-Datenbank" | Microsoft Azure" description="Hier erfahren Sie mehr über die Optionen zur Überwachung Ihrer Datenbank mit Azure-Tools und dynamischen Verwaltungssichten." keywords="Datenbanküberwachung, Clouddatenbankleistung" services="sql-database" documentationCenter="" authors="jeffgoll" manager="jeffreyg" editor="jeffreyg"/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="03/25/2016"
	ms.author="jeffreyg"/>

# Überwachen der Datenbankleistung mithilfe von Azure SQL-Datenbank
Die Überwachung der Leistung einer SQL-Datenbank in Azure beginnt mit der Überwachung der Ressourcennutzung relativ zur gewählten Datenbankleistung. Dank der Überwachung können Sie ermitteln, ob Ihre Datenbank über Zusatzkapazität verfügt oder ob Probleme infolge ausgeschöpfter Kapazität auftreten. Anschließend können Sie entscheiden, ob die Leistungsstufe und [Dienstebene](sql-database-service-tiers.md) Ihrer Datenbank angepasst werden müssen. Sie können die Datenbank mithilfe grafischer Tools im [Azure-Portal](https://portal.azure.com) oder mithilfe [dynamischer Verwaltungssichten](https://msdn.microsoft.com/library/ms188754.aspx) in SQL überwachen.

## Überwachen von Datenbanken über das Azure-Portal

Im [Azure-Portal](https://portal.azure.com/) können Sie die Nutzung einer Einzeldatenbank überwachen, indem Sie die Datenbank auswählen und auf das Diagramm **Überwachung** klicken. Dadurch wird das Fenster **Metrik** geöffnet, in dem Sie durch Klicken auf die Schaltfläche **Diagramm bearbeiten** Änderungen vornehmen können. Fügen Sie die folgenden Metriken hinzu:

- CPU-Prozentsatz
- DTU Percentage
- Data IO Percentage
- Storage Percentage

Nachdem Sie diese Metriken hinzugefügt haben, können Sie sie im Diagramm **Überwachung** mit weiteren Details im Fenster **Metrik** anzeigen. Alle vier Metriken geben die durchschnittliche prozentuale Nutzung relativ zur **DTU** der Datenbank an. Ausführliche Informationen zu DTUs finden Sie im Artikel [Dienstebenen](sql-database-service-tiers.md).

![Tarifbezogenes Überwachen der Datenbankleistung.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

Sie können zudem Benachrichtigungen für die Leistungsmetriken konfigurieren. Klicken Sie im Fenster **Metrik** auf die Schaltfläche **Warnung hinzufügen**. Befolgen Sie die Anweisungen des Assistenten, um die Benachrichtigung zu konfigurieren. Sie haben die Möglichkeit, Benachrichtigungen für den Fall zu konfigurieren, dass Metriken einen Schwellenwert überschreiten oder unterschreiten.

Wenn Sie beispielsweise einen Anstieg der Workload Ihrer Datenbank erwarten, können Sie eine E-Mail-Benachrichtigung konfigurieren, die immer dann gesendet wird, wenn eine der Leistungsmetriken der Datenbank 80 % erreicht. Sie können dies als Frühwarnung verwenden, um zu ermitteln, wann Sie eventuell zur nächsthöheren Leistungsstufe wechseln müssen.

Anhand der Leistungsmetriken können Sie auch ermitteln, ob Sie möglicherweise eine Herabstufung auf eine niedrigere Leistungsstufe vornehmen können. Angenommen, Sie verwenden eine Datenbank der Dienstebene "Standard S2", und alle Leistungsmetriken zeigen, dass die Datenbank zu jedem Zeitpunkt durchschnittlich nicht mehr als 10 % nutzt. Hier ist es wahrscheinlich, dass sich die Datenbank auch mit der Dienstebene "Standard S1" verwenden lässt. Bevor Sie sich für einen Wechsel zu einer niedrigeren Leistungsstufe entscheiden, müssen Sie aber auch Workloads berücksichtigen, die Spitzen oder Schwankungen aufweisen können.

## Überwachen von Datenbanken mithilfe von DMVs

Dieselben Metriken, die im Portal bereitgestellt werden, sind auch über Systemsichten verfügbar: [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) in der logischen **master-Datenbank** Ihres Servers und [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) in der Benutzerdatenbank. Verwenden Sie **sys.resource\_stats**, wenn Sie weniger differenzierte Daten über einen längeren Zeitraum überwachen möchten. Verwenden Sie **sys.dm\_db\_resource\_stats**, wenn Sie differenzierte Daten in einem kürzeren Zeitraum überwachen möchten. Weitere Informationen finden Sie unter [Leitfaden zur Leistung für Azure SQL-Datenbanken](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats).

>[AZURE.NOTE] **dm\_db\_resource\_stats** gibt bei Verwendung der eingestellten Web- und Business Edition-Datenbanken ein leeres Resultset zurück.

In Pools für elastische Datenbanken können Sie Einzeldatenbanken im Pool mit den in diesem Abschnitt beschriebenen Methoden überwachen. Sie können den Pool jedoch auch als Ganzes überwachen. Informationen dazu finden Sie unter [Erstellen und Verwalten eines elastischen Azure SQL-Datenbankpools](sql-database-elastic-pool-manage-portal.md).

<!---HONumber=AcomDC_0330_2016-->