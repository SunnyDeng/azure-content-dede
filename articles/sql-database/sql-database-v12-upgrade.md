<properties 
	pageTitle="Durchführen eines Upgrades auf SQL-Datenbank V12 mithilfe des Azure-Portals | Microsoft Azure" 
	description="Erläutert das Upgrade auf Azure SQL-Datenbank V12, einschließlich der Aktualisierung von Web- und Business-Datenbanken, sowie das Upgrade eines V11-Servers mit direkter Migration der Datenbanken in einen Pool für elastische Datenbanken mithilfe des Azure-Portals." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg"
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="11/11/2015" 
	ms.author="sstein"/>


# Upgrade auf Azure SQL-Datenbank V12 mithilfe des Azure-Portals


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


SQL-Datenbank V12 ist die neueste Version, sodass eine Aktualisierung auf SQL-Datenbank V12 empfohlen wird. SQL-Datenbank V12 verfügt über viele [Vorteile gegenüber der vorherigen Version](sql-database-v12-whats-new.md), einschließlich:

- Höhere Kompatibilität mit SQL Server.
- Verbesserte Premium-Leistung und neue Leistungsstufen.
- [Pools für elastische Datenbanken](sql-database-elastic-pool.md).

Dieser Artikel enthält Anweisungen zum Aktualisieren von vorhandenen SQL-Datenbank V11-Servern und -Datenbanken auf SQL-Datenbank V12.

Bei der Aktualisierung auf V12 führen Sie ein Upgrade aller Web- und Business-Datenbanken auf eine neue Dienstebene durch, daher stehen Anleitungen für das Upgrade von Web- und Business-Datenbanken zur Verfügung.

Zudem kann eine Migration zu einem [Pool für elastische Datenbanken](sql-database-elastic-pool.md) kostengünstiger sein als die Aktualisierung auf einzelne Leistungsstufen (Tarife) für einzelne Datenbanken. Pools vereinfachen zudem das Datenbankmanagement, da Sie nur die Leistungseinstellungen für den Pool verwalten müssen, und nicht die Leistungsmerkmale der einzelnen Datenbanken. Wenn Sie über Datenbanken auf mehreren Servern verfügen, sollten Sie sie auf einen gemeinsamen Server verschieben und die Vorteile aus dem Zusammenfassen in einem Pool nutzen. Sie können Datenbanken [mit PowerShell direkt auf einfache Weise automatisiert von V11-Servern in Pool für elastische Datenbanken migrieren](sql-database-upgrade-server.md). Die Migration von V11-Datenbanken in Pools kann auch mithilfe des Portals durchgeführt werden, dort muss jedoch bereits ein V12-Server vorhanden sein, um den Pool zu erstellen. Weiter unten in diesem Artikel finden Sie eine Anleitung für das Erstellen des Pools nach dem Serverupgrade, falls [Datenbanken vorhanden sind, die von einem Pool profitieren können](sql-database-elastic-pool-guidance.md).

Beachten Sie, dass Ihre Datenbanken während des gesamten Upgradevorgangs online bleiben und weiterhin funktionieren. Zum Zeitpunkt des tatsächlichen Übergangs auf die neue Leistungsstufe können die Datenbankverbindungen ggf. für einen kurzen Zeitraum – i. d. R. 90 Sekunden, eventuell jedoch bis zu 5 Minuten – unterbrochen werden. Wenn eine Anwendung über [Maßnahmen zur Behandlung vorübergehender Fehler bei Verbindungsunterbrechungen](sql-database-connect-central-recommendations.md) verfügt, ist es ausreichend, am Ende des Upgrades Schutz vor unterbrochenen Verbindungen vorzusehen.

Das Upgrade auf SQL-Datenbank V12 kann nicht rückgängig gemacht werden. Nach dem Upgrade kann der Server nicht auf V11 zurückgesetzt werden.

Nach dem Upgrade auf V12 stehen [Empfehlungen zur Dienstebene](sql-database-service-tier-advisor.md) und [Empfehlungen zu elastischen Pools](sql-database-elastic-pool-portal.md#step-2-choose-a-pricing-tier) erst dann zur Verfügung, wenn der Dienst Zeit für die Evaluierung der Workloads auf dem neuen Server hat. Der Empfehlungsverlauf für V11-Server gilt nicht für V12-Server und wird daher nicht beibehalten.


## Vorbereiten des Upgrades

- **Aktualisieren aller Web- und Business-Datenbanken**: Informationen hierzu finden Sie im Abschnitt [Durchführen eines Upgrades für alle Web- und Business-Datenbanken](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases) weiter unten, oder verwenden Sie [PowerShell zum Aktualisieren von Datenbanken und Server](sql-database-upgrade-server-powershell.md).
- **Überprüfen und Beenden der Georeplikation**: Wenn Ihre Azure SQL-Datenbank für Georeplikation konfiguriert ist, sollten Sie ihre aktuelle Konfiguration dokumentieren und die [Georeplikation beenden](sql-database-geo-replication-portal.md#remove-secondary-database). Nachdem das Upgrade abgeschlossen ist, können Sie Ihre Datenbank erneut für die Georeplikation konfigurieren.
- **Öffnen Sie folgende Ports, falls Sie Clients auf einer Azure-VM verwenden**: Wenn Ihr Clientprogramm eine Verbindung mit SQL-Datenbank V12 herstellt und der Client auf einem virtuellen Azure-Computer ausgeführt wird, müssen Sie die Portbereiche 11000–11999 und 14000–14999 auf dem virtuellen Computer öffnen. Weitere Informationen finden Sie unter [Ports für SQL-Datenbank V12](sql-database-develop-direct-route-ports-adonet-v12.md).



## Starten des Upgrades

1. Navigieren Sie im [Azure-Portal](http://portal.azure.com/) zu dem Server, den Sie aktualisieren möchten. Wählen Sie dazu **ALLE DURCHSUCHEN** > **SQL-Server** und dann den gewünschten Server aus.
2. Wählen Sie **Letzte Aktualisierung der SQL-Datenbank** aus, und wählen Sie dann **Upgrade für diesen Server ausführen** aus.

      ![Server upgraden][1]

## Durchführen eines Upgrades für alle Web- und Business-Datenbanken

Wenn Ihr Server Web- oder Business-Datenbanken hat, müssen Sie für diese ein Upgrade durchführen. Im Verlauf der Aktualisierung auf SQL-Datenbank V12 werden Sie alle Web- und Business-Datenbanken auf eine neue Dienstebene aktualisieren.

Um Sie beim Durchführen des Upgrades zu unterstützen, empfiehlt der SQL-Datenbank-Dienst eine geeignete Dienstebene und Leistungsstufe (Tarif) für jede Datenbank. Der Dienst empfiehlt die beste Dienstebene für das Ausführen der vorhandenen Workload der Datenbank durch Analyse ihres Nutzungsverlaufs.
    
3. Wählen Sie auf dem Blatt **Upgrade für diesen Server ausführen** jede zu überprüfende Datenbank und den empfohlenen Tarif aus, auf den aktualisiert werden soll. Sie können immer die verfügbaren Tarife durchsuchen und den Tarif auswählen, der für Ihre Umgebung am besten geeignet ist.


     ![Datenbanken][2]


7. Nachdem Sie auf die empfohlene Dienstebene geklickt haben, wird das Blatt **Preisstufe auswählen** angezeigt. Dort können Sie einen Tarif auswählen und dann auf die Schaltfläche **Auswählen** klicken, um zu diesem Tarif zu wechseln. Wählen Sie für jede Web- oder Business-Datenbank einen neuen Tarif aus.

    Ein wichtiger Faktor dabei ist, dass Ihre SQL-Datenbank nicht auf eine bestimmte Dienstebene oder Leistungsstufe beschränkt ist. Wenn sich die Anforderungen Ihrer Datenbank ändern, können Sie also einfach zwischen den verfügbaren Dienstebenen und Leistungsstufen wechseln. SQL-Datenbanken der Editionen Basic, Standard und Premium werden nach Stunden berechnet. Sie haben die Möglichkeit, jede Datenbank innerhalb eines 24-Stunden-Intervalls viermal auf eine höhere oder niedrigere Stufe zu skalieren.

    ![empfehlungen][6]


Nachdem alle Datenbanken auf dem Server qualifiziert sind, können Sie das Upgrade starten.

## Bestätigen des Upgrades

3. Wenn alle Datenbanken auf dem Server zum Upgrade qualifiziert sind, müssen Sie in **GEBEN SIE DEN SERVERNAMEN EIN** den Servernamen eingeben, um zu bestätigen, dass Sie das Upgrade durchführen möchten. Klicken Sie dann auf **OK**. 

    ![Upgrade bestätigen][3]


4. Das Upgrade wird gestartet, und die Benachrichtigung zum Vorgangsfortschritt wird angezeigt. Der Upgradevorgang wird eingeleitet. Abhängig von den Details Ihrer speziellen Datenbanken kann das Durchführen des Upgrades auf V12 einige Zeit dauern. Während dieser Zeitspanne bleiben alle Datenbanken auf dem Server online, Server- und Datenbankverwaltungsaktionen sind jedoch eingeschränkt.

    ![Upgrade wird ausgeführt][4]

    Zum Zeitpunkt des tatsächlichen Übergangs auf die neue Leistungsstufe können die Datenbankverbindungen ggf. für einen sehr kurzen Zeitraum (normalerweise im Sekundenbereich) vorübergehend unterbrochen werden. Wenn eine Anwendung über Maßnahmen zur Behandlung vorübergehender Fehler (Wiederholungslogik) bei Verbindungsunterbrechungen verfügt, ist es ausreichend, am Ende des Upgrades Schutz vor unterbrochenen Verbindungen vorzusehen.

5. Nach Abschluss des Upgradevorgangs wird auf dem Blatt **Letztes Update** der Status **Aktiviert** angezeigt.

    ![V12-aktiviert][5]

## Verschieben der Datenbanken in einen Pool für elastische Datenbanken

Wechseln Sie im [Azure-Portal](https://ms.portal.azure.com/) zum V12-Server und klicken Sie auf **Pool hinzufügen**.

Oder

Wenn die Meldung **Zum Anzeigen der empfohlenen elastischen Datenbankpools für diesen Server hier klicken** angezeigt wird, klicken Sie darauf, um auf einfache Weise einen Pool zu erstellen, der für die Datenbanken Ihres Servers optimiert ist. Ausführliche Informationen finden Sie unter [Empfohlene Pools für elastische Datenbanken](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools).

![Hinzufügen eines Pools zu einem Server][7]
   
Befolgen Sie die Anleitung im Artikel [Erstellen eines Pools für elastische Datenbanken](sql-database-elastic-pool.md), um die Erstellung des Pools abzuschließen.

## Überwachen von Datenbanken nach dem Upgrade auf SQL-Datenbank V12


Nach dem Upgrade wird empfohlen, die Datenbank aktiv zu überwachen, um sicherzustellen, dass Anwendungen mit der gewünschten Leistung ausgeführt werden. Außerdem sollte die Nutzung optimiert werden.

Zusätzlich zur Überwachung einzelner Datenbanken können Sie [mithilfe des Portals](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool) oder mit [PowerShell](sql-database-elastic-pool-powershell.md#monitoring-elastic-databases-and-elastic-database-pools) auch Pools für elastische Datenbanken überwachen.


**Ressourcenverbrauchsdaten**: Für Basic-, Standard- und Premium-Datenbanken sind Ressourcenverbrauchsdaten über die dynamische Verwaltungssicht (DMV) [sys.dm\_ db\_ resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) in der Benutzerdatenbank verfügbar. Diese dynamische Verwaltungssicht stellt beinahe in Echtzeit Informationen zum Ressourcenverbrauch mit einer Genauigkeit von 15 Sekunden für die vorhergehende Stunde des Betriebs zur Verfügung. Der prozentuale DTU-Verbrauch für ein Intervall wird als maximaler prozentualer Verbrauch von CPU-, E/A- und Protokollressourcen berechnet. Die folgende Abfrage berechnet den durchschnittlichen prozentualen DTU-Verbrauch während der letzten Stunde:

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Zusätzliche Überwachungsinformationen:

- [Leitfaden zur Azure SQL-Datenbankleistung für einzelne Datenbanken](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Überlegungen zum Preis und zur Leistung eines Pools für elastische Datenbanken](sql-database=elastic-pool-guidance.md).
- [Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten](sql-database-monitoring-with-dmvs.md)




**Warnungen**: Richten Sie im Azure-Portal „Warnungen“ ein, damit Sie benachrichtigt werden, wenn der DTU-Verbrauch für eine aktualisierte Datenbank eine bestimmte hohe Ebene erreicht. Datenbankwarnungen können im Azure-Portal für verschiedene Leistungsmetriken wie DTU, CPU, E/A und die Protokollierung eingerichtet werden. Navigieren Sie zu der Datenbank, und wählen Sie **Warnungsregeln** auf dem Blatt **Einstellungen** aus.

Sie können z. B. eine E-Mail-Benachrichtigung für den "DTU Prozentsatz" festlegen, wenn der durchschnittliche prozentuale DTU-Wert 75 % innerhalb der letzten 5 Minuten überschreitet. Weitere Informationen zur Konfiguration von Warnungsbenachrichtigungen finden Sie unter [Empfangen von Warnungsbenachrichtigungen](insights-receive-alert-notifications.md).





## Nächste Schritte

- [Prüfen von Empfehlungen für Pools für elastische Datenbanken](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools).
- [Erstellen eines Pools für elastische Datenbanken](sql-database-elastic-pool-portal.md) und Hinzufügen einiger oder aller Datenbanken zu einem Pool.
- [Ändern der Dienstebene und Leistungsstufe Ihrer Datenbank](sql-database-scale-up.md).



## Verwandte Links

- [Neuerungen in SQL-Datenbank V12](sql-database-v12-whats-new.md)
- [Plan and prepare to upgrade to SQL Database V12 (Planen und Vorbereiten des Upgrades auf die SQL-Datenbank V12; in englischer Sprache)](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png

<!---HONumber=AcomDC_1203_2015-->