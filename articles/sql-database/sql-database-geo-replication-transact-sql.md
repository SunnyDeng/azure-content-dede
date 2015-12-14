<properties
    pageTitle="Konfigurieren der Georeplikation für Azure SQL-Datenbank mit Transact-SQL | Microsoft Azure"
    description="Konfigurieren der Georeplikation für Azure SQL-Datenbank mithilfe von Transact-SQL"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="11/10/2015"
    ms.author="carlrab"/>

# Konfigurieren der Georeplikation für Azure SQL-Datenbank mit Transact-SQL



> [AZURE.SELECTOR]
- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


Dieser Artikel beschreibt, wie Sie die Georeplikation für eine Azure SQL-Datenbank mithilfe von Transact-SQL konfigurieren.


Die Georeplikation ermöglicht das Erstellen von bis zu vier (sekundären) Replikatdatenbanken in verschiedenen Datencenterregionen. Sekundäre Datenbanken stehen zur Verfügung, wenn ein Datencenter ausgefallen ist oder keine Verbindung mit der primären Datenbank möglich ist.

Die Georeplikation ist nur für Standard- und Premium-Datenbanken verfügbar.

Standard-Datenbanken können über eine nicht lesbare sekundäre Datenbank verfügen und müssen die empfohlene Region verwenden. Premium-Datenbanken können bis zu vier lesbare sekundäre Datenbanken in beliebigen der verfügbaren Regionen aufweisen.


Zum Konfigurieren der Georeplikation benötigen Sie Folgendes:

- Ein Azure-Abonnement: Wenn Sie kein Azure-Abonnement haben, klicken Sie einfach oben auf dieser Seite auf den Link **KOSTENLOSE TESTVERSION** und kehren dann zu diesem Artikel zurück.
- Einen logische Azure SQL-Datenbankserver <MyLocalServer> und eine Azure SQL-Datenbank <MyDB>: Die primäre Datenbank, die in eine andere geografische Region repliziert werden soll.
- Ein oder mehrere logische Azure SQL-Datenbankserver <MySecondaryServer(n)>: Die logischen Server, die als Partnerserver fungieren, in denen Sie sekundäre Datenbanken für die Georeplikation erstellen.
- Eine Anmeldung mit der Berechtigung „DBManager“ für die primäre Datenbank, mit der Berechtigung „db\_ownership“ für die lokale Datenbank für die Georeplikation und der Berechtigung „DBManager“ für die Partnerserver, für die Sie die Georeplikation konfigurieren.
- Neueste Version von SQL Server Management Studio: Die neueste Version von SQL Server Management Studio (SSMS) finden Sie unter [Herunterladen von SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Informationen zur Verwendung von SQL Server Management Studio zum Verwalten von logischen Azure SQL-Datenbankservern und Datenbanken finden Sie unter [Verwalten von Azure SQL-Datenbank mit SQL Server Management Studio](sql-database-manage-azure-ssms.md).

## Hinzufügen einer sekundären Datenbank

Mithilfe der **ALTER DATABASE**-Anweisung können Sie auf einem Partnerserver eine georeplizierte sekundäre Datenbank erstellen. Sie führen diese Anweisung in der „master“-Datenbank des Servers mit der Datenbank aus, die repliziert werden soll. Die geografisch replizierte Datenbank (die „primäre Datenbank“) hat denselben Namen wie die zu replizierende Datenbank und standardmäßig dieselbe Dienstebene wie die primäre Datenbank. Die sekundäre Datenbank kann lesbar oder nicht lesbar und eine Einzel- oder elastische Datenbank sein. Weitere Informationen finden Sie unter [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) und [Tarife](https://azure.microsoft.com/documentation/articles/sql-database-service-tiers/). Nachdem die sekundäre Datenbank erstellt und das Seeding ausgeführt wurde, beginnt die asynchrone Replikation aus der primären Datenbank. In den nachfolgenden Schritten wird beschrieben, wie die Georeplikation mithilfe von Management Studio konfiguriert wird. Schritte zum Erstellen von nicht lesbaren und lesbaren sekundären Datenbanken, entweder mit einer einzelnen oder einer elastischen Datenbank, sind angegeben.

> [AZURE.NOTE]Wenn die sekundäre Datenbank auf dem angegebenen Partnerserver vorhanden ist (da z. B. eine Georeplikationsbeziehung derzeit besteht oder zuvor bestanden hat), misslingt der Befehl.


### Hinzufügen einer nicht lesbaren sekundären Datenbank (Einzeldatenbank)

Führen Sie zum Erstellen einer nicht lesbaren sekundären Datenbank als Einzeldatenbank die folgenden Schritte aus.

1. Verwenden von Version 13.0.600.65 oder höher von SQL Server Management Studio

 	 >[AZURE.IMPORTANT]Laden Sie die [aktuelle](https://msdn.microsoft.com/library/mt238290.aspx) Version von SQL Server Management Studio herunter. Es wird empfohlen, immer die neueste Version von Management Studio zu verwenden, damit Sie mit Updates des Azure-Portals synchron sind.


2. Öffnen Sie den Ordner „Datenbanken“, erweitern Sie den Ordner **Systemdatenbanken**, klicken Sie mit der rechten Maustaste auf **master**, und klicken Sie anschließend auf **Neue Abfrage**.

3. Verwenden Sie die folgende **ALTER DATABASE**-Anweisung, um eine lokale Datenbank als georeplizierte primäre Datenbank mit einer nicht lesbaren sekundären Datenbank auf <MySecondaryServer1> einzurichten.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. Klicken Sie auf **Ausführen**, um die Abfrage durchzuführen.


### Hinzufügen einer lesbaren sekundären Datenbank (Einzeldatenbank)
Führen Sie zum Erstellen einer lesbaren sekundären Datenbank als Einzeldatenbank die folgenden Schritte aus:

1. Verbinden Sie sich in Management Studio mit dem logischen Azure SQL-Datenbankserver.

2. Öffnen Sie den Ordner „Datenbanken“, erweitern Sie den Ordner **Systemdatenbanken**, klicken Sie mit der rechten Maustaste auf **master**, und klicken Sie anschließend auf **Neue Abfrage**.

3. Verwenden Sie die folgende **ALTER DATABASE**-Anweisung, um eine lokale Datenbank als georeplizierte primäre Datenbank mit einer lesbaren sekundären Datenbank auf einem sekundären Server einzurichten.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. Klicken Sie auf **Ausführen**, um die Abfrage durchzuführen.



### Hinzufügen einer nicht lesbaren sekundären Datenbank (elastische Datenbank)
Führen Sie zum Erstellen einer nicht lesbaren sekundären Datenbank als elastische Datenbank die folgenden Schritte aus:

1. Verbinden Sie sich in Management Studio mit dem logischen Azure SQL-Datenbankserver.

2. Öffnen Sie den Ordner „Datenbanken“, erweitern Sie den Ordner **Systemdatenbanken**, klicken Sie mit der rechten Maustaste auf **master**, und klicken Sie anschließend auf **Neue Abfrage**.

3. Verwenden Sie die folgende **ALTER DATABASE**-Anweisung, um eine lokale Datenbank als georeplizierte primäre Datenbank mit einer nicht lesbaren sekundären Datenbank auf einem sekundären Server in einem elastischen Pool einzurichten.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO)
           , ELASTIC_POOL (name = MyElasticPool1);

4. Klicken Sie auf **Ausführen**, um die Abfrage durchzuführen.



### Hinzufügen einer lesbaren sekundären Datenbank (elastische Datenbank)
Führen Sie zum Erstellen einer lesbaren sekundären Datenbank als elastische Datenbank die folgenden Schritte aus.

1. Verbinden Sie sich in Management Studio mit dem logischen Azure SQL-Datenbankserver.

2. Öffnen Sie den Ordner „Datenbanken“, erweitern Sie den Ordner **Systemdatenbanken**, klicken Sie mit der rechten Maustaste auf **master**, und klicken Sie anschließend auf **Neue Abfrage**.

3. Verwenden Sie die folgende **ALTER DATABASE**-Anweisung, um eine lokale Datenbank als georeplizierte primäre Datenbank mit einer lesbaren sekundären Datenbank auf einem sekundären Server in einem elastischen Pool einzurichten.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = NO)
           , ELASTIC_POOL (name = MyElasticPool2);

4. Klicken Sie auf **Ausführen**, um die Abfrage durchzuführen.



## Entfernen einer sekundären Datenbank

Sie können mithilfe der **ALTER DATABASE**-Anweisung die Replikationspartnerschaft zwischen einer sekundären Datenbank und ihrer primären Datenbank dauerhaft beenden. Diese Anweisung wird für die „master“-Datenbank ausgeführt, in der sich die primäre Datenbank befindet. Nach dem Beenden der Beziehung wird die sekundäre Datenbank eine reguläre Datenbank mit Lese-/ Schreibzugriff. Wenn die Verbindung mit der sekundären Datenbank unterbrochen wird, ist der Befehl zwar erfolgreich, aber die sekundäre Datenbank wird erst mit Lese-/ Schreibzugriff versehen, nachdem die Verbindung wiederhergestellt wurde. Weitere Informationen finden Sie unter [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) und [Tarife](https://azure.microsoft.com/documentation/articles/sql-database-service-tiers/).

Führen Sie zum Entfernen der sekundären Datenbank aus der Georeplikationsbeziehung folgende Schritte aus.

1. Verbinden Sie sich in Management Studio mit dem logischen Azure SQL-Datenbankserver.

2. Öffnen Sie den Ordner „Datenbanken“, erweitern Sie den Ordner **Systemdatenbanken**, klicken Sie mit der rechten Maustaste auf **master**, und klicken Sie anschließend auf **Neue Abfrage**.

3. Verwenden Sie die folgende **ALTER DATABASE** -Anweisung zum Entfernen einer georeplizierten sekundären Datenbank.

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. Klicken Sie auf **Ausführen**, um die Abfrage durchzuführen.


## Auslösen eines geplanten Failovers mit Heraufstufen einer sekundären Datenbank zur neuen primären Datenbank

Mit der **ALTER DATABASE**-Anweisung können Sie eine sekundäre Datenbank auf geplante Weise zur neuen primären Datenbank heraufstufen und die vorhandene primäre Datenbank zu einer sekundären Datenbank herabstufen. Diese Anweisung wird für die „master“-Datenbank auf dem logischen Azure SQL-Datenbankserver ausgeführt, in dem sich die geografisch replizierte Datenbank befindet, die heraufgestuft wird. Diese Funktionalität ist auf ein geplantes Failover ausgelegt, z. B. während der Notfallwiederherstellungsverfahren, und erfordert, dass die primäre Datenbank verfügbar ist.

Der Befehl hat den folgenden Workflow:

1. Die Replikation wird vorübergehend in den synchronen Modus umgeschaltet, was bewirkt, dass alle ausstehenden Transaktionen in die sekundäre Datenbank erfolgen und alle neuen Transaktionen blockiert werden.

2. Die Rollen der beiden Datenbanken in der Georeplikationspartnerschaft werden getauscht.

Durch diese Abfolge wird sichergestellt, dass kein Datenverlust auftritt. Es gibt einen kurzer Zeitraum, in dem beide Datenbanken während des Rollenwechsels (ca. 0 bis 25 Sekunden) nicht verfügbar sind. Unter normalen Umständen dauert der gesamte Vorgang nicht länger als 1 Minute. Weitere Informationen finden Sie unter [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) und [Tarife](https://azure.microsoft.com/documentation/articles/sql-database-service-tiers/).


> [AZURE.NOTE]Falls die primäre Datenbank bei Aufruf des Befehls nicht verfügbar ist, misslingt der Befehl mit der Fehlermeldung, dass der primäre Server nicht verfügbar ist. In seltenen Fällen ist es möglich, dass der Vorgang nicht abgeschlossen werden kann und festzustecken scheint. In diesem Fall kann der Benutzer den Befehl zum Erzwingen des Failovers aufrufen und den Datenverlust akzeptieren.

Führen Sie die folgenden Schritte aus, um ein geplantes Failover auszulösen.

1. Verbinden Sie sich in Management Studio mit dem logischen Azure SQL-Datenbankserver, in dem sich eine geografisch repliziert sekundäre Datenbank befindet.

2. Öffnen Sie den Ordner „Datenbanken“, erweitern Sie den Ordner **Systemdatenbanken**, klicken Sie mit der rechten Maustaste auf **master**, und klicken Sie anschließend auf **Neue Abfrage**.

3. Verwenden Sie die folgende **ALTER DATABASE**-Anweisung, um eine georeplizierte Datenbank als primäre Datenbank mit einer lesbaren sekundären Datenbank auf <MySecondaryServer4> in <ElasticPool2> einzurichten.

        ALTER DATABASE <MyDB> FAILOVER;

4. Klicken Sie auf **Ausführen**, um die Abfrage durchzuführen.



## Auslösen eines ungeplanten Failovers von der primären Datenbank zur sekundären Datenbank

Mit der **ALTER DATABASE**-Anweisung können Sie eine sekundäre Datenbank auf ungeplante Weise zur neuen primären Datenbank heraufstufen und das Herabstufen der vorhandenen primären Datenbank zu einer sekundären Datenbank erzwingen, sobald die primäre Datenbank nicht mehr verfügbar ist. Diese Anweisung wird für die „master“-Datenbank auf dem logischen Azure SQL-Datenbankserver ausgeführt, in dem sich die geografisch replizierte Datenbank befindet, die heraufgestuft wird.

Diese Funktion dient zur Notfallwiederherstellung, wenn das Wiederherstellen der Verfügbarkeit der Datenbank überaus wichtig und ein gewisser Datenverlust akzeptabel ist. Beim Auslösen eines erzwungenen Failovers wird die angegebene sekundäre Datenbank sofort zur primären Datenbank und beginnt mit dem Akzeptieren von Schreibtransaktionen. Sobald sich die ursprüngliche primäre Datenbank mit dieser neuen primären Datenbank verbinden kann, wird eine inkrementelle Sicherung der ursprünglichen Datenbank erstellt. Die alte primäre Datenbank wird zu einer sekundären Datenbank der neuen primären Datenbank. Anschließend ist sie lediglich ein sich synchronsierendes Replikat der neuen primären Datenbank.

Da jedoch die Point-in-Time-Wiederherstellung für sekundäre Datenbanken nicht unterstützt wird, muss der Benutzer, wenn Daten mit erfolgtem Commit in der alten primären Datenbank wiederhergestellt werden sollen, die nicht in die neue primäre Datenbank repliziert wurden, bevor das erzwungene Failover erfolgt ist, den Support bitten, diese verloren gegangenen Daten wiederherzustellen.

> [AZURE.NOTE]Falls der Befehl aufgerufen wird, wenn die primäre und sekundäre Datenbank online sind, wird die alte primäre Datenbank zur neuen sekundären Datenbank, ohne dass eine Synchronisierung der Daten versucht wird. Deshalb können Datenverluste auftreten.


Wenn die primäre Datenbank über mehrere sekundäre Datenbanken verfügt, ist der Befehl nur auf dem sekundären Server erfolgreich, auf dem der Befehl ausgeführt wurde. Den anderen sekundären Datenbanken ist jedoch nicht bekannt, dass das Erzwingen eines Failovers erfolgt ist. Der Benutzer muss diese Konfiguration mithilfe einer API vom Typ „remove secondary“ reparieren und dann die Georeplikation in diesen zusätzlichen sekundären Datenbanken neu konfigurieren.

Führen Sie zum Erzwingen des Entfernens der georeplizierten sekundären Datenbank aus der Georeplikationsbeziehung folgende Schritte aus:

1. Verbinden Sie sich in Management Studio mit dem logischen Azure SQL-Datenbankserver, in dem sich eine geografisch repliziert sekundäre Datenbank befindet.

2. Öffnen Sie den Ordner „Datenbanken“, erweitern Sie den Ordner **Systemdatenbanken**, klicken Sie mit der rechten Maustaste auf **master**, und klicken Sie anschließend auf **Neue Abfrage**.

3. Verwenden Sie die folgende **ALTER DATABASE**-Anweisung, um <MyLocalDB> als eine georeplizierte primäre Datenbank mit einer lesbaren sekundären Datenbank auf <MySecondaryServer4> in <ElasticPool2> einzurichten.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Klicken Sie auf **Ausführen**, um die Abfrage durchzuführen.

## Überprüfen der Konfiguration und Integrität der Georeplikation

Zu den Überwachungsaufgaben gehören die Überwachung der Konfiguration der Georeplikation und der Integrität der Datenreplikation. Sie können die dynamische Verwaltungssicht **sys.dm\_geo\_replication\_links** in der Masterdatenbank nutzen, um Informationen zu allen vorhandenen Replikationsverknüpfungen für alle Datenbanken auf dem logischen Azure SQL-Datenbankserver abzurufen. Diese Ansicht enthält eine Zeile für jede Replikationsverknüpfung zwischen primären und sekundären Datenbanken. Sie können die dynamische Verwaltungssicht **sys.dm\_replication\_status** verwenden, um eine Zeile für jede Azure SQL-Datenbank zurückzugeben, die derzeit an einer Replikationsverknüpfung beteiligt ist. Dies schließt primäre und sekundäre Datenbanken ein. Wenn für eine bestimmte primäre Datenbank mehrere Verknüpfungen für die fortlaufende Replikation vorhanden sind, enthält die Tabelle eine Zeile für jede der Beziehungen. Die Sicht wird in allen Datenbanken, einschließlich der logischen „master“-Datenbank, erstellt. Doch bei Abfragen dieser Sicht in der logischen „master“-Datenbank wird ein leeres Ergebnis zurückgegeben. Sie können mithilfe der Verwaltungssicht **sys.dm\_operation\_status** den Status aller Datenbankvorgänge anzeigen, einschließlich des Status der Replikationsverknüpfungen. Weitere Informationen finden Sie unter [sys.dm\_geo\_replication\_links (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm\_geo\_replication\_link\_status (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/mt575504.aspx) und [sys.dm\_operation\_status (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/dn270022.aspx).

Gehen Sie folgendermaßen vor, um eine Georeplikationspartnerschaft zu überwachen.

1. Verbinden Sie sich in Management Studio mit dem logischen Azure SQL-Datenbankserver.

2. Öffnen Sie den Ordner „Datenbanken“, erweitern Sie den Ordner **Systemdatenbanken**, klicken Sie mit der rechten Maustaste auf **master**, und klicken Sie anschließend auf **Neue Abfrage**.

3. Verwenden Sie die folgende Anweisung, um alle Datenbanken mit Georeplikationsverknüpfungen anzuzeigen.

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. Klicken Sie auf **Ausführen**, um die Abfrage durchzuführen.
5. Öffnen Sie den Ordner „Datenbanken“, erweitern Sie den Ordner **Systemdatenbanken**, klicken Sie mit der rechten Maustaste auf **MyDM**, und klicken Sie anschließend auf **Neue Abfrage**.
6. Zeigen Sie mithilfe der folgenden Anweisung die Replikationsverzögerungen und den Zeitpunkt der letzten Replikation Ihrer eigenen sekundären Datenbanken von MyDB an.

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. Klicken Sie auf **Ausführen**, um die Abfrage durchzuführen.
8. Verwenden Sie die folgende Anweisung, um die neuesten Georeplikationsvorgänge anzuzeigen, die der Datenbank MyDB zugeordnet sind.

        SELECT * FROM sys.dm_operation_status where major_resource_is = 'MyDB'
        ORDER BY start_time DESC

9. Klicken Sie auf **Ausführen**, um die Abfrage durchzuführen.


## Nächste Schritte

- [Warnungen zur Notfallwiederherstellung](sql-database-disaster-recovery-drills.md)


## Zusätzliche Ressourcen

- [Spotlight auf die neuen Georeplikationsfunktionen](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication)
- [Entwerfen von Cloudanwendungen zum Sicherstellen der Geschäftskontinuität mithilfe der Georeplikation](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md)
- [SQL-Datenbankdokumentation](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1203_2015-->