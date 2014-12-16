
# Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio 

Sie könnnen das Azure SQL-Datenbank-Verwaltunsportal oder die SQL Server Management Studio (SSMS)-Clientanwendung zur Verwaltung Ihrer SQL-Datenbankabonnements verwenden und zugeordnete logische Server und Datenbanken erstellen und verwalten. Die folgende Anleitung beschreibt, wie Sie mit Management Studio die logischen Server und Datenbanken der SQL-Datenbank verwalten.

<div class="dev-callout-new-collapsed">
<strong>Hinweis <span>Click to collapse</span></strong>
<div class="dev-callout-content">
<p>Sie können die Version SQL Server 2014, SQL Server 2012 oder SQL Server 2008 R2 von SSMS verwenden. Frühere Versionen werden nicht unterstützt.</p>
</div>
</div>

Dieses Thema umfasst die folgenden Schritte:

-   [Schritt 1: Installieren von SQL Server Management Studio][]
-   [Schritt 2: Verbinden mit der SQL-Datenbank][]
-   [Schritt 3: Erstellen und Verwalten von Datenbanken][]
-   [Schritt 4: Erstellen und Verwalten von Anmeldungen][]
-   [Schritt 5: Überwachen von SQL-Datenbanken mit dynamischen Verwaltungssichten][]

<h2><a id="Step1" name="Step1"> </a>Schritt 1: Installieren von Management Studio</h2>

Management Studio ist eine integrierte Umgebung für
die Verwaltung von SQL-Datenbank. Bei der Verwaltung von 
Datenbanken in Azure können Sie die Management Studio-Anwendung verwenden, die mit
SQL Server installiert wird, oder die kostenlose SQL Server 2012 Management Studio Express (SSMSE)-Version verwenden. Die folgenden Schritte beschreiben,
wie SSMSE installiert wird.

1.  Wählen Sie auf der Seite [Microsoft SQL Server 2012 Express][] die x86-Version von Management Studio, wenn Sie ein 32-Bit-Betriebssystem ausführen, bzw. die x64-Version für ein 64-Bit-Betriebssystem. Klicken Sie auf **Download**, und führen Sie nach der entsprechenden Aufforderung das Setup aus.

2.  Klicken Sie auf **Neue eigenständige SQL Server-Installation oder Hinzufügen von Features zu einer
    vorhandenen Installation**, und klicken Sie dann auf **OK**.

3.  Akzeptieren Sie die Lizenzbedingungen, und klicken Sie auf **Weiter**.

4. Klicken Sie auf **Installieren**, um die vom SQL Server-Setup benötigten Dateien zu installieren.

5.  Auf dem Bildschirm **Funktionsauswahl** ist **Verwaltungstools -
    Einfach** bereits ausgewählt. Grund hierfür ist die Tatsache, dass Sie den Installationsassistenten für Management Studio ausführen. Wenn Sie Setup für alle Komponenten von SQL Server Express ausführen, wählen Sie die Option **Verwaltungstools - Einfach**, und klicken Sie auf **Weiter**.

6.  Im Bildschirm **Fehlerberichterstattung** können Sie optional angeben, dass
    Fehlerberichte an Microsoft gesendet werden sollen. Dies ist zur Verwendung von SSMSE nicht erforderlich. Klicken Sie auf
    **Weiter**, um die Installation zu starten.

7.  Nach Abschluss der Installation wird die Seite **Abgeschlossen**
    angezeigt. Klicken Sie auf **Schließen**. 


<h2><a id="Step2" name="Step2"> </a>Schritt 2: Verbinden mit der SQL-Datenbank</h2>

Um eine Verbindung mit der SQL-Datenbank herstellen zu können, müssen Sie den Servernamen auf Azure kennen. Eventuell müssen Sie sich beim Portal anmelden, um diese Information zu erhalten.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][] an.

2.  Klicken Sie im linken Bereich auf **SQL-Datenbanken**.

3.  Klicken Sie oben auf der Startseite der SQL-Datenbanken auf **SERVER**, um eine Liste aller Server anzuzeigen, die Ihrem Abonnement zugeordnet sind. Suchen Sie den Namen des Servers, mit dem Sie eine Verbindung herstellen möchten, und kopieren Sie ihn in die Zwischenablage.

	Als Nächstes konfigurieren Sie die SQL-Datenbank-Firewall,
sodass Verbindungen von Ihrem lokalen Computer zugelassen werden. Geben Sie hierzu die IP-Adresse der lokalen Computer in die Ausnahmeliste der Firewall ein.

1.  Klicken Sie auf der Startseite der SQL-Datenbanken auf **SERVERS** und dann auf den Server, mit dem Sie eine Verbindung herstellen möchten.

2.  Klicken Sie oben auf der Seite auf  **Konfigurieren**.

3.  Kopieren Sie die IP-Adresse in das Feld CURRENT CLIENT IP ADDRESS.

4.  Auf der Seite "Konfigurieren" sind für **Zugelassene IP-Adressen** drei Felder verfügbar, in denen Sie einen Regelnamen und die Start- und Endwerte eines IP-Adressbereichs eingeben können. Als Regelnamen können Sie den Namen Ihres Computers wählen. Fügen Sie für Anfangs- und Endbereich die IP-Adresse Ihres Computers in beide Felder ein, und aktivieren Sie das Kontrollkästchen, das anschließend angezeigt wird.

	Der Regelname muss eindeutig sein. Wenn es sich um Ihren Entwicklungscomputer handelt, können Sie seine IP-Adresse in beide Felder eingeben und als Anfangs- und Endwert des IP-Adressbereichs nutzen. Andernfalls kann es erforderlich sein, einen größeren IP-Adressbereich zu wählen, um Verbindungen von weiteren Computern in Ihrer Organisation zu ermöglichen.
 
5. Klicken Sie unten auf der Seite auf **SPEICHERN**.

    **Hinweis:** Es kann bis zu fünf Minuten dauern, bis Änderungen
    der Firewall-Einstellungen wirksam werden.

	Sie können nun über Management Studio eine Verbindung zur SQL-Datenbank aufbauen.

1.  Klicken Sie auf der Taskleiste auf **Start**, zeigen Sie auf **Alle Programme**, zeigen Sie auf
    **Microsoft SQL Server 2012**, und klicken Sie auf **SQL Server
    Management Studio**.

2.  Geben Sie in **Verbindung mit dem Server herstellen** den vollqualifizierten
    Servernamen in der Form "*serverName*.database.windows.net" ein. In Azure ist der Servername eine automatisch generierte Zeichenfolge aus alphanumerischen Zeichen.

3.  Wählen Sie **SQL Server-Authentifizierung** aus.

4.  Geben Sie im Feld **Anmeldung** den SQL Server-Administratoranmeldenamen ein, den Sie
    im Portal beim Erstellen des Servers im Format
    *login*@*yourServerName* angegeben haben.

5.  Geben Sie im Feld **Kennwort** das Kennwort ein, das Sie beim
    Erstellen des Servers im Portal angegeben haben.

8.  Klicken Sie auf **Verbinden**, um die Verbindung herzustellen.

In Azure ist jeder logische SQL-Datenbankserver eine Abstraktion, die eine Gruppierung von Datenbanken definiert. Der physische Speicherort der einzelnen Datenbanken kann sich auf jedem Computer des Datencenters befinden. 

In Vorgängerversionen mussten Sie beim Einrichten einer Verbindung in Management Studio eine direkte Verbindung mit **master** herstellen. Dieser Schritt ist nicht mehr erforderlich. Verbindungen können nun anhand von Servername, Authentifizierungstyp und Anmeldedaten des Administrators erfolgreich hergestellt werden.

Viele der SSMS-Assistenten, die Sie für Aufgaben verwenden können, wie z. B.
Erstellen und Ändern von Anmeldungen und Datenbanken in einer SQL Server-Datenbank, sind
für SQL-Datenbanken in Azure nicht verfügbar. Daher müssen Sie diese
Aufgaben unter Verwendung von Transact-SQL-Anweisungen erledigen. Die nachfolgenden Schritte
enthalten Beispiele für diese Anweisungen. Weitere Informationen zur Verwendung von
Transact-SQL mit SQL-Datenbanken und Einzelheiten dazu, welche Befehle
unterstützt werden, finden Sie in der [Transact-SQL-Referenz (SQL-Datenbank)][].

<h2><a id="Step3" name="Step3"> </a>Schritt 3: Erstellen und Verwalten von Datenbanken</h2>

Während Sie mit der **master**-Datenbank verbunden sind, können Sie neue
Datenbanken auf dem Server erstellen oder vorhandene Datenbanken ändern oder löschen. In den nachfolgenden Schritten
wird beschrieben, wie Sie einige allgemeine Datenbankverwaltungsaufgaben
in Management Studio ausführen. Um diese Aufgaben auszuführen, stellen Sie sicher, dass Sie mit der
**master**-Datenbank unter Verwendung der Prinzipalanmeldung auf Serverebene verbunden sind,
die Sie bei der Einrichtung des Servers erstellt haben.

Um ein Abfragefenster in Management  zu öffnen, öffnen Sie den Ordner "Datenbanken", erweitern Sie den Ordner **System Databsaes**, klicken Sie mit der rechten Maustaste auf **master**, und klicken Sie dann mit der linken Maustaste auf **Neue Abfrage**.

-   Verwenden Sie die Anweisung **CREATE DATABASE**, um eine neue Datenbank zu erstellen. Weitere
    Informationen finden Sie unter [CREATE DATABASE (SQL-Datenbank)][]. Die folgende Anweisung erstellt eine neue Datenbank namens
    **myTestDB** und gibt an, dass es sich um eine Web Edition-Datenbank
    mit einer maximalen Größe von 64 KB handelt.

        CREATE DATABASE myTestDB
        (MAXSIZE=1GB,
         EDITION='web');

Klicken Sie auf **Ausführen**, um die Abfrage auszuführen.

-   Verwenden Sie die Anweisung **ALTER DATABASE**, um eine vorhandene Datenbank zu ändern,
    z. B., wenn Sie den Namen, die maximale Größe oder die Edition
    (Business oder Web) der Datenbank ändern möchten. Weitere Informationen finden Sie unter [ALTER DATABASE (SQL-Datenbank)][]. Die
    folgende Anweisung ändert die maximale Größe der im vorherigen Schritt
    erstellten Datenbank in 5 GB.

        ALTER DATABASE myTestDB
        MODIFY
        (MAXSIZE=5GB,
         EDITION='web');

-   Verwenden Sie die **Anweisung DROP DATABASE**, um eine vorhandene Datenbank zu löschen.
    Weitere Informationen finden Sie unter [DROP DATABASE (SQL-Datenbank)][]. Mit der folgenden Anweisung wird die Datenbank **myTestDB**
    gelöscht. Löschen Sie sie aber nicht sofort, weil Sie sie im nächsten Schritt zum Erstellen der Anmeldungen verwenden.

        DROP DATABASE myTestBase;

-   Die master-Datenbank besitzt die **sys.databases**-Ansicht, die Sie
    zum Anzeigen von Details zu allen Datenbanken verwenden können. Um alle vorhandenen Datenbanken anzuzeigen,
    führen Sie die folgende Anweisung aus:

        SELECT * FROM sys.databases;

-   In SQL-Datenbank kann die **USE**-Anweisung nicht zum Wechseln
    zwischen Datenbanken verwendet werden. Stattdessen müssen Sie eine direkte Verbindung
    mit der Datenbank herstellen.

<div class="dev-callout-new">
 <strong>Hinweis <span>Click to collapse</span></strong>
 <div class="dev-callout-content">
   <p>Viele der Transact-SQL-Anweisungen zum Erstellen oder Ändern einer
Datenbank müssen in ihrem eigenen Batch ausgeführt werden und können nicht mit
anderen Transact-SQL-Anweisungen zusammengefasst werden. Weitere Informationen finden Sie in den anweisungsspezifischen Artikeln, die über die oben aufgeführten Links verfügbar sind.</p>
</div>
</div>

<h2><a id="Step4" name="Step4"> </a>Schritt 4: Erstellen und Verwalten von Anmeldungen</h2>

In der **master**-Datenbank werden Anmeldungen verzeichnet und verfolgt, welche Anmeldungen
zum Erstellen von Datenbanken oder anderer Anmeldungen berechtigt sind. Verwalten Sie Anmeldungen, indem
Sie eine Verbindung mit der **master**-Datenbank mit der Prinzipalanmeldung auf
Serverebene herstellen, die Sie beim Einrichten des Servers erstellt haben. Sie können mit den Anweisungen
**CREATE LOGIN**, **ALTER LOGIN** oder **DROP LOGIN**
Abfragen für die master-Datenbank ausführen, mit denen Anmeldungen
serverweit verwaltet werden. Weitere Informationen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank][]. 


-   Verwenden Sie die Anweisung **CREATE LOGIN**, um eine neue Anwendung auf Serverebene
    zu erstellen. Weitere Informationen finden Sie unter [CREATE LOGIN (SQL-Datenbank)][]. Die folgende Anweisung erstellt eine neue Anmeldung
    namens **login1**. Ersetzen Sie **password1** durch ein Kennwort Ihrer
    Wahl.

        CREATE LOGIN login1 WITH password='password1';

-   Verwenden Sie die Anweisung **CREATE USER**, um Berechtigungen auf Datenbankebene
    zu erteilen. Alle Anmeldungen müssen in der **master**-Datenbank erstellt werden,
    damit mit einem Anmeldenamen eine Verbindung mit einer anderen Datenbank hergestellt werden kann, müssen
    Sie diesem jedoch mit der Anweisung **CREATE USER**
    Berechtigungen auf Datenbankebene für die betreffende Datenbank erteilen. Weitere Informationen finden Sie unter [CREATE USER (SQL-Datenbank)][]. 

-   Um login1
    Berechtigungen für eine Datenbank namens **myTestDB** zu erteilen, gehen Sie wie
    folgt vor:

 1.  Um den Objekt-Explorer zum Anzeigen der gerade erstellten Datenbank **myTestDB** dzu aktualisieren, klicken Sie im Objekt-Explorer mit der rechten Maustaste auf den Servernamen und dann mit der linken Maustaste auf **Aktualisieren**.  

     Wenn Sie die Verbindung getrennt haben, können Sie sie im Menü "Datei" mit dem Befehl **Objekt-Explorer verbinden** erneut herstellen. Wiederholen Sie die Anweisungen aus [Schritt 2: Verbinden mit SQL-Datenbank][], um eine Verbindung mit der Datenbank herzustellen.

 2. Klicken Sie mit der rechten Maustaste auf die Datenbank **myTestDB**, und wählen Sie **Neue Abfrage** aus.

    3. Führen Sie die folgende Anweisung für die Datenbank "myTestDB" aus,
        um einen Datenbankbenutzer namens **login1User** zu erstellen, welcher der
        Anmeldung auf Serverebene namens **login1** entspricht.

            CREATE USER login1User FROM LOGIN login1;

-   Verwenden Sie die gespeicherte Prozedur **sp\_addrolemember**, um dem Benutzerkonto
    die erforderlichen Berechtigungen für die Datenbank zu erteilen. Weitere
    Informationen finden Sie unter [sp_addrolemember (Transact-SQL)][]. Mit der nachfolgenden Anweisung erhält **login1User**
    Berechtigungen für einen schreibgeschützten Zugriff auf die Datenbank, indem **login1User** der
    Rolle **db\_datareader** hinzugefügt wird.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Verwenden Sie die Anweisung **ALTER LOGIN**, um eine vorhandene Anmeldung zu ändern, z. B.
    wenn Sie das Kennwort für die Anmeldung ändern möchten. Weitere
    Informationen finden Sie unter [ALTER LOGIN (SQL Database)][]. Die Anweisung **ALTER LOGIN** sollte für die
    **master**-Datenbank ausgeführt werden. Wechseln Sie wieder zurück zu dem Abfragefenster, das mit dieser Datenbank verbunden ist. 

    Mit der nachfolgenden Anweisung wird die Anmeldung **login1** geändert, sodass das Kennwort zurückgesetzt wird.
    Ersetzen Sie **newPassword** durch ein Kennwort Ihrer Wahl, und
    **oldPassword** durch das aktuelle Kennwort der Anmeldung.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Verwenden Sie die **Anweisung DROP LOGIN**, um eine vorhandene Anmeldung zu löschen.
    Wenn Sie eine Anmeldung auf Serverebene löschen, werden auch alle zugehörigen
    Datenbankbenutzerkonten gelöscht. Weitere Informationen
     finden Sie unter [DROP DATABASE (SQL-Datenbank)][]. Die Anweisung **DROP LOGIN**
     sollte für die **master**-Datenbank ausgeführt werden. Die
    nachfolgende Anweisung löscht die Anmeldung**login1**.

        DROP LOGIN login1;

-   Die master-Datenbank verfügt über die Sicht**sys.sql\_logins**, die Sie
    zum Anzeigen der Anmeldungen nutzen können. Führen Sie zum Anzeigen aller vorhandenen Anmeldungen die
    folgende Anweisung aus:

        SELECT * FROM sys.sql_logins;

<h2><a id="Step5" name="Step5"> </a>Schritt 5: Überwachen von SQL-Datenbanken mit dynamischen Verwaltungssichten</h2>

SQL-Datenbank unterstützt einige dynamische Verwaltungssichten, mit
denen Sie einzelne Datenbanken überwachen können. Nachfolgend finden Sie einige Beispiele für
die Art von Überwachungsdaten, die Sie mit diesen Sichten abrufen können. Alle
Details und weitere Anwendungsbeispiele finden Sie unter [Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten][].

-   Zum Abfragen einer dynamischen Verwaltungssicht ist die Berechtigung **VIEW DATABASE STATE**
    erforderlich. Um einem bestimmten Datenbankbenutzer die Berechtigung **VIEW DATABASE STATE**
    zu gewähren, stellen Sie eine Verbindung mit der Datenbank her, die Sie
    mit der Prinzipalanmeldung auf Serverebene verwalten möchten, und führen Sie die
    Anweisung für die Datenbank aus:

        GRANT VIEW DATABASE STATE TO login1User;

-   Berechnen Sie die Datenbankgröße mit der **sys.dm\_db\_partition\_stats**-
    -Sicht. Die Sicht **sys.dm\_db\_partition\_stats** gibt Informationen zur Seiten-
    Zeilenanzahl jeder Partition der Datenbank zurück, die Sie
    zum Berechnen der Datenbankgröße verwenden können. Die folgende Abfrage gibt
    die Größe der Datenbank in MB zurück:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Die Sichten **sys.dm\_exec\_connections** und **sys.dm\_exec\_sessions**
    dienen zum Abrufen von Informationen über aktuelle Benutzerverbindungen und
    interne im Zusammenhang mit der Datenbank stehende Aufgaben. Die folgende Abfrage
    liefert Informationen über die aktuelle Verbindung:

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

-   Die Sicht **sys.dm\_exec\_query\_stats** dient zum Abrufen aggregierter
    Leistungsstatistiken für zwischengespeicherte Abfragepläne. Die folgende Abfrage
    gibt Informationen über die "Top-Fünf"-Abfragen gemessen an durchschnittlicher CPU-Zeit
    zurück.

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

<h2>Zusätzliche Ressourcen</h2>

* [Einführung in SQL-Datenbank][]   
* [erwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank][]   
* [Überwachen der SQL-Datenbank mit dynamischen Verwaltungssichten][]   
* [SQL-Datenbank-Bereitstellungsmodell][]   
* [Hinzufügen von Benutzern zu Ihrer SQL-Datenbank][]   
* [Transact-SQL-Referenz (SQL-Datenbank)][]

  [Verwenden von Azure SQL-Datenbank]: http://www.windowsazure.com/de-de/develop/net/how-to-guides/sql-azure/
  [Schritt 1: Installieren von SQL Server Management Studio]: #Step1
  [Schritt 2: Verbinden mit der SQL-Datenbank]: #Step2
  [Schritt 3: Erstellen und Verwalten von Datenbanken]: #Step3
  [Schritt 4: Erstellen und Verwalten von Anmeldungen]: #Step4
  [Schritt 5: Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten]:
    #Step5
  [Microsoft SQL Server 2012 Express]: http://www.microsoft.com/de-de/download/details.aspx?id=29062
  [SSMS-Installer - Installationstyp wählen]: /media/installer_installation_type.png
  [SSMS-Installer - Funktionen wählen:]: /media/installer_feature_selection.png
  [SSMS-Installer - Installation abgeschlossen]: /media/installer_completed.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com/
  [Abrufen des SQL-Datenbankservernamens aus dem Verwaltungsportal]: /media/portal_get_database_name.png
  [Verbindung mit SSMS herstellen]: /media/ssms_connect.png
  [Verbindung mit SSMS herstellen -- Eigenschaften]: /media/ssms_connect_properties.png
  [Transact-SQL-Referenz (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/bb510741(v=sql.120).aspx
  [CREATE DATABASE (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336274.aspx
  [ALTER DATABASE (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ff394109.aspx
  [DROP DATABASE (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336259.aspx
  [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336235.aspx
  [CREATE LOGIN (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336268.aspx
  [CREATE USER (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/ee336277.aspx
  [sp_addrolemember (Transact-SQL)]: http://msdn.microsoft.com/de-de/library/ms187750.aspx
  [ALTER LOGIN (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336254.aspx
  [Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten]: http://msdn.microsoft.com/de-de/library/windowsazure/ff394114.aspx
  [Einführung in die SQL-Datenbank]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336230.aspx
  [SQL-Datenbank-Bereitstellungsmodell]: http://msdn.microsoft.com/de-de/library/ee336227.aspx
  [Hinzufügen von Benutzern zu Ihrer SQL-Datenbank]: http://blogs.msdn.com/b/sqlazure/archive/2010/06/21/10028038.aspx
