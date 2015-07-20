
# Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio 

Sie können SQL Server Management Studio (SSMS) verwenden, um logische Server und Datenbanken der Azure SQL-Datenbank zu verwalten. Dieses Thema enthält Anleitungen für häufige Aufgaben mit SSMS. Sie sollten bereits einen logischen Server und eine logische Datenbank in der Azure SQL-Datenbank erstellt haben, bevor Sie beginnen. Um zu beginnen, lesen Sie [Erstellen einer ersten Azure SQL-Datenbank](sql-database-get-started.md) und kehren dann zurück.

Es wird empfohlen, dass Sie die neueste Version von SSMS verwenden, wenn Sie mit der Azure SQL-Datenbank arbeiten. Klicken Sie hierfür auf [Herunterladen von SQL Server Management Studio](https://msdn.microsoft.com/de-de/library/mt238290.aspx).


## Herstellen einer Verbindung mit einem logischen SQL-Datenbankserver

Um eine Verbindung mit der SQL-Datenbank herstellen zu können, müssen Sie den Servernamen auf Azure kennen. Eventuell müssen Sie sich beim Portal anmelden, um diese Information zu erhalten.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com) an.

2.  Klicken Sie im linken Bereich auf **SQL-Datenbanken**.

3.  Klicken Sie oben auf der Startseite der SQL-Datenbanken auf **SERVER**, um eine Liste aller Server anzuzeigen, die Ihrem Abonnement zugeordnet sind. Suchen Sie den Namen des gewünschten Servers und kopieren Sie ihn in die Zwischenablage.

	Konfigurieren Sie als nächstes die SQL-Datenbank-Firewall, um Verbindungen von Ihrem lokalen Computer zu ermöglichen. Geben Sie hierzu die IP-Adresse Ihres lokalen Computers in die Ausnahmeliste der Firewall ein.

1.  Klicken Sie auf der Startseite der SQL-Datenbanken auf **SERVER** und dann auf den Server, mit dem Sie eine Verbindung herstellen möchten.

2.  Klicken Sie oben auf der Seite auf **Konfigurieren**.

3.  Kopieren Sie die IP-Adresse in das Feld CURRENT CLIENT IP ADDRESS.

4.  Auf der Seite **Konfigurieren** sind für Zugelassene IP-Adressen drei Felder verfügbar, in denen Sie einen Regelnamen und die Start- und Endwerte eines IP-Adressbereichs eingeben können. Als Regelnamen können Sie den Namen Ihres Computers wählen. Kopieren Sie für Bereichsanfang und -ende die IP-Adresse Ihres Computers in beide Felder und aktivieren Sie das Kontrollkästchen, das anschließend angezeigt wird.

	Der Regelname muss eindeutig sein. Wenn es sich um Ihren Entwicklungscomputer handelt, können Sie seine IP-Adresse in beide Felder eingeben und als Anfangs- und Endwert des IP-Adressbereichs nutzen. Andernfalls kann es erforderlich sein, einen größeren IP-Adressbereich zu wählen, um Verbindungen von weiteren Computern in Ihrer Organisation zu ermöglichen.
 
5. Klicken Sie unten auf der Seite auf **Speichern**.

    **Hinweis:** Es kann bis zu fünf Minuten dauern, bis geänderten Firewalleinstellungen wirksam werden.

	Sie können nun über Management Studio eine Verbindung zur SQL-Datenbank aufbauen.

1.  Klicken Sie auf der Taskleiste auf **Start**, zeigen Sie auf **Alle Programme** > **Microsoft SQL Server 2014**, und klicken Sie anschließend auf **SQL Server Management Studio**.

2.  Geben Sie unter **Verbindung mit Server herstellen** den vollständigen Servernamen im Format „*Servername*.database.windows.net“ ein. In Azure ist der Servername eine automatisch generierte Zeichenfolge aus alphanumerischen Zeichen.

3.  Wählen Sie **SQL Server-Authentifizierung**.

4.  Geben Sie im Feld **Anmeldung** den SQL Server-Administratornamen ein, den Sie im Portal beim Erstellen des Servers angegeben haben.

5.  Geben Sie im Feld **Kennwort** das Kennwort ein, das Sie im Portal beim Erstellen des Servers angegeben haben.

8.  Klicken Sie auf **Verbinden**, um die Verbindung herzustellen.

SQL Server 2014 SSMS mit den neuesten Updates bietet erweiterte Unterstützung für Aufgaben wie etwa das Erstellen und Ändern von Azure SQL-Datenbanken. Darüber hinaus können Sie für diese Aufgaben auch Transact-SQL-Anweisungen verwenden. Die folgenden Schritte enthalten Beispiele für derartige Anweisungen. Weitere Informationen zur Verwendung von Transact-SQL mit SQL-Datenbank einschließlich detaillierter Angaben zu unterstützten Befehlen finden Sie unter [Transact-SQL-Referenz (SQL-Datenbank)](http://msdn.microsoft.com/library/bb510741.aspx).

## Erstellen und Verwalten von Azure SQL-Datenbanken

Während Sie mit der **Master**-Datenbank verbunden sind, können Sie neue Datenbanken auf dem Server erstellen und bestehende Datenbanken modifizieren oder löschen. Die folgenden Schritte beschreiben, wie Sie eine Reihe häufiger Aufgaben der Datenbankverwaltung über Management Studio erledigen können. Vergewissern Sie sich zur Durchführung dieser Aufgaben, dass Sie mit der **Master**-Datenbank verbunden sind, und zwar mit dem Hauptbenutzernamen auf Serverebene, den Sie bei der Einrichtung des Servers erstellt haben.

Öffnen Sie den Ordner „Datenbanken“, erweitern Sie den Ordner **System Databases**, klicken Sie mit der rechten Maustaste auf **master**, und klicken Sie anschließend auf **Neue Abfrage**, um ein Abfragefenster in Management Studio zu öffnen.

-   Verwenden Sie die Anweisung **CREATE DATABASE**, um eine neue Datenbank zu erstellen. Weitere Informationen finden Sie unter [CREATE DATABASE (SQL-Datenbank)](https://msdn.microsoft.com/library/dn268335.aspx). Die folgende Anweisung erstellt eine neue Datenbank mit dem Namen **myTestDB** und gibt an, dass es sich um eine Datenbank der Standard S0-Edition mit einer maximalen Standardgröße von 250 GB handelt.

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

Klicken Sie auf **Ausführen**, um die Abfrage durchzuführen.

-   Mit der Anweisung **ALTER DATABASE** können Sie eine vorhandene Datenbank anpassen und beispielsweise den Namen oder die Edition der Datenbank ändern. Weitere Informationen finden Sie unter [ALTER DATABASE (SQL-Datenbank)](https://msdn.microsoft.com/library/ms174269.aspx). Die folgende Anweisung ändert die im vorherigen Schritt erstellte Datenbank und legt die Edition auf „Standard S1“ fest.

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   Mit der Anweisung **DROP DATABASE** können Sie eine bestehende Datenbank löschen. Weitere Informationen finden Sie unter [DROP DATABASE (SQL-Datenbank)](https://msdn.microsoft.com/library/ms178613.aspx). Mit der folgenden Anweisung löschen Sie die Datenbank **myTestDB**; führen Sie dies jedoch jetzt noch nicht durch, da Sie für sie im nächsten Schritt Anmeldungen erstellen werden.

        DROP DATABASE myTestBase;

-   Die Master-Datenbank verfügt über die Ansicht **sys.databases**, in der Sie Details zu allen Datenbanken einsehen können. Führen Sie zur Anzeige aller vorhandenen Datenbanken die folgende Anweisung aus:

        SELECT * FROM sys.databases;

-   In SQL-Datenbank wird die Anweisung **USE** nicht für einen Wechsel zwischen einzelnen Datenbanken unterstützt. Statt dessen müssen Sie direkt eine Verbindung zur Zieldatenbank herstellen.

>[AZURE.NOTE]Viele der Transact-SQL-Anweisungen, die eine Datenbank erstellen oder modifizieren, müssen in ihrem eigenen Batch ausgeführt werden und können nicht mit anderen Transact-SQL-Anweisungen gruppiert werden. Weitere Informationen finden Sie in den anweisungsspezifischen Artikeln, die über die oben aufgeführten Links verfügbar sind.

## Erstellen und Verwalten von Anmeldungen

Die Datenbank vom Typ **master** überwacht alle Anmeldungen und verfolgt, welche Anmeldungen zum Erstellen von Datenbanken oder anderen Anmeldungen berechtigt sind. Stellen Sie zur Verwaltung von Anmeldungen eine Verbindung zur **Master**-Datenbank her, und zwar mit der Hauptanmeldung auf Serverebene, die Sie beim Einrichten des Servers erstellt haben. Mit den Anweisungen **CREATE LOGIN**, **ALTER LOGIN** oder **DROP LOGIN** können Sie Abfragen an die Master-Datenbank ausführen, die die Anmeldungen für den gesamten Server verwalten. Weitere Informationen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/ee336235.aspx).


-   Verwenden Sie die Anweisung **CREATE LOGIN**, um eine neue Anmeldung auf Serverebene zu erstellen. Weitere Informationen finden Sie unter [CREATE LOGIN (SQL-Datenbank)](https://msdn.microsoft.com/library/ms189751.aspx). Die folgende Anweisung erstellt eine neue Anmeldung mit dem Namen **login1**. Ersetzen Sie **password1** durch ein Kennwort Ihrer Wahl.

        CREATE LOGIN login1 WITH password='password1';

-   Mit der Anweisung **CREATE USER** erteilen Sie Berechtigungen auf Datenbank-Ebene. Alle Anmeldungen müssen in der **Master**-Datenbank erstellt werden; damit eine Anmeldung jedoch auf eine andere Datenbank zugreifen kann, müssen Sie ihr Berechtigungen auf Datenbank-Ebene zuweisen, indem Sie die Anweisung **CREATE USER** für diese Datenbank ausführen. Weitere Informationen finden Sie unter [CREATE USER (SQL-Datenbank)](https://msdn.microsoft.com/library/ms173463.aspx).

-   Gehen Sie folgendermaßen vor, um "login1" die Zugriffsberechtigung auf eine Datenbank mit dem Namen **myTestDB** zu erteilen:

 1.  Um den Objekt-Explorer zur Anzeige der gerade erstellten Datenbank **myTestDB** zu aktualisieren, klicken Sie im Objekt-Explorer mit der rechten Maustaste auf den Servernamen und dann mit der linken auf **Aktualisieren**.  

     Wenn Sie die Verbindung geschlossen haben, können Sie sie im Menü **Datei** mit dem Befehl Objekt-Explorer verbinden wieder öffnen.

 2. Klicken Sie mit der rechten Maustaste auf die Datenbank **myTestDB** und wählen Sie **Neue Abfrage**.

    3.  Führen Sie die folgende Anweisung für die Datenbank "myTestDB" durch, um einen Datenbankbenutzer namens **login1User** zu erstellen, der der Anmeldung auf Serverebene **login1** zugeordnet ist.

            CREATE USER login1User FROM LOGIN login1;

-   Nutzen Sie die gespeicherte Prozedur **sp_addrolemember**, um das Benutzerkonto mit geeigneten Berechtigungen für die Datenbank auszustatten. Weitere Informationen finden Sie unter [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). Die folgende Anweisung erteilt **login1User** Lesezugriff auf die Datenbank, indem sie **login1User** der Rolle **db_datareader** hinzufügt.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Verwenden Sie die Anweisung **ALTER LOGIN**, um eine bestehende Anmeldung zu modifizieren, etwa wenn sie das zugehörige Kennwort ändern möchten. Weitere Informationen finden Sie unter [ALTER LOGIN (SQL-Datenbank)](https://msdn.microsoft.com/library/ms189828.aspx). Die Anweisung **ALTER LOGIN** sollte bezogen auf die **Master**-Datenbank ausgeführt werden. Wechseln Sie wieder zurück zu dem Abfragefenster, das mit dieser Datenbank verbunden ist.

    Die folgende Anweisung modifiziert die Anmeldung **login1**, um das Kennwort zurückzusetzen. Ersetzen Sie **newPassword** durch ein Kennwort Ihrer Wahl, und **oldPassword** durch das aktuelle Kennwort der Anmeldung.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Mit der Anweisung **DROP LOGIN** können Sie eine bestehende Anmeldung löschen. Wenn Sie eine Anmeldung auf Serverebene löschen, werden auch alle zugehörigen Konten von Datenbankbenutzern gelöscht. Weitere Informationen finden Sie unter [DROP DATABASE (SQL-Datenbank)](https://msdn.microsoft.com/library/ms178613.aspx). Die Anweisung **DROP LOGIN** sollte bezogen auf die **Master**-Datenbank ausgeführt werden. Die folgende Anweisung löscht die Anmeldung **login1**.

        DROP LOGIN login1;

-   Die Master-Datenbank verfügt über eine Ansicht **sys.sql_logins**, in der Sie die Anmeldungen einsehen können. Führen Sie zur Anzeige aller vorhandenen Anmeldungen die folgende Anweisung aus:

        SELECT * FROM sys.sql_logins;

## Überwachen von SQL-Datenbanken mit dynamischen Verwaltungssichten</h2>

SQL-Datenbank unterstützt mehrere Dynamic Management Views, mit denen Sie eine einzelne Datenbank überwachen können. Im Folgenden finden Sie einige wenige Beispiele für die Art Überwachungsdaten, die Sie mit diesen Ansichten abrufen können. Umfassende Details und weitere Anwendungsbeispiele finden Sie unter [Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten](https://msdn.microsoft.com/library/azure/ff394114.aspx).

-   Die Abfrage einer dynamischen Verwaltungsansicht erfordert die Berechtigung **VIEW DATABASE STATE**. Wenn Sie einem bestimmten Benutzer die Berechtigung **VIEW DATABASE STATE** zuweisen möchten, stellen Sie mit Ihrer Hauptanmeldung für die Serverebene eine Verbindung zu der Datenbank her, die Sie verwalten möchten, und führen Sie bezogen auf sie die folgende Anweisung aus:

        GRANT VIEW DATABASE STATE TO login1User;

-   Berechnen Sie die Datenbankgröße mithilfe der Ansicht **sys.dm_db_partition_stats**. Die Ansicht **sys.dm_db_partition_stats** gibt die Anzahl von Seiten und Zeilen für jede Partition der Datenbank zurück, aus denen Sie dann die Datenbankgröße errechnen können. Die folgende Abfrage gibt die Größe Ihrer Datenbank in Megabyte zurück:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   In den Ansichten **sys.dm_exec_connections** und **sys.dm_exec_sessions** können Sie Informationen über aktuelle Benutzerverbindungen und der Datenbank zugeordnete interne Aufgaben abrufen. Die folgende Abfrage gibt Informationen über die aktuelle Verbindung zurück:

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   In der Ansicht **sys.dm_exec_query_stats** können Sie aggregierte Leistungsstatistiken für zwischengespeicherte Abfragepläne abrufen. Die folgende Abfrage gibt Informationen über die "Top-Fünf"-Abfragen gemessen an durchschnittlicher CPU-Zeit zurück.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;

<!---HONumber=July15_HO2-->