
# Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio 

Sie könnnen das Azure SQL-Datenbank-Verwaltunsportal oder die SQL Server Management Studio (SSMS)-Clientanwendung zur Verwaltung Ihrer SQL-Datenbankabonnements verwenden und zugeordnete logische Server und Datenbanken erstellen und verwalten. Die folgende Anleitung beschreibt, wie Sie mit Management Studio die logischen Server und Datenbanken der SQL-Datenbank verwalten.

<div class="dev-callout-new-collapsed">
<strong>Hinweis <span>Klicken Sie, um zu reduzieren</span></strong>
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

Management Studio ist eine integrierte Umgebung zur Verwaltung von SQL-Datenbanken. Beim Verwalten von Datenbanken in Azure können Sie die Management Studio-Anwendung verwenden, die mit
SQL Server installiert wird, oder die kostenlose SQL Server 2012 Management Studio Express (SSMSE)-Version verwenden. Die folgenden Schritte beschreiben die Installation von SSMSE.

1.  Wählen Sie auf der Seite [Microsoft SQL Server 2012 Express][] die x86-Version von Management Studio, wenn Sie ein 32-Bit-Betriebssystem ausführen, oder x64 bei 64-Bit-Betriebssystemen. Klicken Sie auf **Download** und führen Sie dann nach Aufforderung das Setup aus.

2.  Klicken Sie auf **Neue eigenständige SQL Server-Installation oder Hinzufügen von Features zu einer vorhandenen Installation** und klicken Sie auf **OK**.

3.  Akzeptieren Sie die Lizenzbedingungen, und klicken Sie auf **Weiter**.

4. Klicken Sie auf **Installieren**, um die vom SQL Server-Setup benötigten Dateien zu installieren.

5.  Auf dem Bildschirm **Featureauswahl** ist die Option **Verwaltungstools - Einfach** vorgewählt. Grund hierfür ist die Tatsache, dass Sie den Installationsassistenten für Management Studio ausführen. Wenn Sie Setup für alle Komponenten von SQL Server Express ausführen, wählen Sie die Option **Verwaltungstools - Einfach**, und klicken Sie auf **Weiter**.

6.  Auf dem Bildschirm **Fehlerberichterstattung** können Sie optional wählen, Fehlerberichte an Microsoft zu senden. Dies ist zur Verwendung von SSMSE nicht erforderlich. Klicken Sie auf **Weiter**, um die Installation zu beginnen.

7.  Nach Abschluss der Installation wird die Seite **Abgeschlossen** angezeigt. Klicken Sie auf **Schließen**. 


<h2><a id="Step2" name="Step2"> </a>Schritt 2: Verbinden mit der SQL-Datenbank</h2>

Um eine Verbindung mit der SQL-Datenbank herstellen zu können, müssen Sie den Servernamen auf Azure kennen. Eventuell müssen Sie sich beim Portal anmelden, um diese Information zu erhalten.

1.  Melden Sie sich am [Azure-Verwaltungsportal][] an.

2.  Klicken Sie im linken Bereich auf **SQL-Datenbanken**.

3.  Klicken Sie oben auf der Startseite der SQL-Datenbanken auf **SERVER**, um eine Liste aller Server anzuzeigen, die Ihrem Abonnement zugeordnet sind. Suchen Sie den Namen des gewünschten Servers und kopieren Sie ihn in die Zwischenablage.

	Konfigurieren Sie als nächstes die SQL-Datenbank-Firewall, um Verbindungen von Ihrem lokalen Computer zu ermöglichen. Geben Sie hierzu die IP-Adresse Ihres lokalen Computers in die Ausnahmeliste der Firewall ein.

1.  Klicken Sie auf der Startseite der SQL-Datenbanken auf **SERVER** und dann auf den Server, mit dem Sie eine Verbindung herstellen möchten.

2.  Klicken Sie oben auf der Seite auf **Konfigurieren**.

3.  Kopieren Sie die IP-Adresse in das Feld CURRENT CLIENT IP ADDRESS.

4.  Auf der Seite **Konfigurieren** sind für Zugelassene IP-Adressen drei Felder verfügbar, in denen Sie einen Regelnamen und die Start- und Endwerte eines IP-Adressbereichs eingeben können. Als Regelnamen können Sie den Namen Ihres Computers wählen. Kopieren Sie für Bereichsanfang und -ende die IP-Adresse Ihres Computers in beide Felder und aktivieren Sie das Kontrollkästchen, das anschließend angezeigt wird.

	Der Regelname muss eindeutig sein. Wenn es sich um Ihren Entwicklungscomputer handelt, können Sie seine IP-Adresse in beide Felder eingeben und als Anfangs- und Endwert des IP-Adressbereichs nutzen. Andernfalls kann es erforderlich sein, einen größeren IP-Adressbereich zu wählen, um Verbindungen von weiteren Computern in Ihrer Organisation zu ermöglichen.
 
5. Klicken Sie unten auf der Seite auf **Speichern**.
 **Hinweis:** Es kann bis zu fünf Minuten dauern, bis die Änderungen der Firewalleinstellungen wirksam werden.

	Sie können nun über Management Studio eine Verbindung zur SQL-Datenbank aufbauen.

1.  Klicken Sie in der Taskleiste auf **Start**, deuten Sie auf **Alle Programme** und dann auf **Microsoft SQL Server 2012**, und klicken Sie auf **SQL Server Management Studio**.

2.  Geben Sie unter **Verbindung mit Server herstellen** den vollständigen Servernamen in der Form  *serverName*.database.windows.net ein. In Azure ist der Servername eine automatisch generierte Zeichenfolge aus alphanumerischen Zeichen.

3.  Wählen Sie **SQL Server-Authentifizierung**.

4.  Geben Sie im Feld **Anmeldung** den SQL Server-Administratornamen ein, den Sie im Portal beim Erstellen des Servers im Format
    *login*@*IhrServerName* angegeben haben.

5.  Geben Sie im Feld **Kennwort** das Kennwort ein, das Sie im Portal beim Erstellen des Servers angegeben haben.

8.  Klicken Sie auf **Verbinden**, um die Verbindung herzustellen.

In Azure ist jeder logische SQL-Datenbankserver eine Abstraktion, die eine Gruppierung von Datenbanken definiert. Der physische Speicherort der einzelnen Datenbanken kann sich auf jedem Computer des Datencenters befinden. 

In früheren Versionen mussten Sie beim Einrichten der Verbindung in Management Studio eine direkte Verbindung mit **master** herstellen. Dieser Schritt ist nicht mehr erforderlich. Verbindungen können nun auf Basis von Servername, Authentifizierungstyp und Anmeldedaten des Administrators erfolgreich hergestellt werden.

Viele der SSMS-Assistenten, mit denen Sie Aufgaben wie das Erstellen und Ändern von Benutzernamen und Datenbanken in einer SQL Server-Datenbank erledigen können, stehen für SQL-Datenbanken auf Azure nicht zur Verfügung. Sie müssen diese
Aufgaben unter Verwendung von Transact-SQL-Anweisungen erledigen. Die folgenden Schritte enthalten Beispiele für derartige Anweisungen. Weitere Informationen zur Verwendung von
Transact-SQL mit SQL-Datenbank einschließlich detaillierter Angaben zu unterstützten Befehlen finden Sie unter [Transact-SQL-Referenz (SQL-Datenbank)][].

<h2><a id="Step3" name="Step3"> </a>Schritt 3: Erstellen und Verwalten von Datenbanken</h2>

Während Sie mit der **Master**-Datenbank verbunden sind, können Sie neue Datenbanken auf dem Server erstellen und bestehende Datenbanken modifizieren oder löschen. Die folgenden Schritte beschreiben, wie Sie eine Reihe häufiger Aufgaben der Datenbankverwaltung über Management Studio erledigen können. Um diese Aufgaben auszuführen, stellen Sie eine Verbindung zur
**master**-Datenbank her, und zwar mit der Hauptanmeldung auf Serverebene, die Sie beim Einrichten des Servers erstellt haben.

Öffnen Sie ein Abfragefenster in Management Studio, indem Sie den Ordner "Datenbanken" öffnen, den Ordner **Systemdatenbanken** erweitern, mit der rechten Maustaste auf **Master** und dann mit der linken auf **Neue Abfrage** klicken.

-   Verwenden Sie die Anweisung **CREATE DATABASE**, um eine neue Datenbank zu erstellen. Weitere Informationen finden Sie unter [CREATE DATABASE (SQL-Datenbank)][]. Die folgende Anweisung erstellt eine neue Datenbank mit dem Namen **myTestDB** und legt fest, dass es sich um eine Web Edition-Datenbank mit einer maximalen Größe von 1 GB handelt.

        CREATE DATABASE myTestDB
        (MAXSIZE=1GB,
         EDITION='web');

Klicken Sie auf **Ausführen**, um die Abfrage auszuführen.

-   Mit der Anweisung **ALTER DATABASE** können Sie eine bestehende Datenbank anpassen, wenn Sie etwa den Namen, die maximale Größe oder die Edition (Business oder Web) der Datenbank ändern möchten. Weitere Informationen finden Sie unter [ALTER DATABASE (SQL-Datenbank)][]. Die folgende Anweisung modifiziert die im vorherigen Schritt erstellte Datenbank und ändert die Maximalgröße auf 5 GB.

        ALTER DATABASE myTestDB
        MODIFY
        (MAXSIZE=5GB,
         EDITION='web');

-   Mit der Anweisung **DROP DATABASE** können Sie eine bestehende Datenbank löschen. Weitere Informationen finden Sie unter [DROP DATABASE (SQL-Datenbank)][]. Mit der folgenden Anweisung löschen Sie die Datenbank **myTestDB**; führen Sie dies jedoch jetzt noch nicht durch, da Sie für sie im nächsten Schritt Anmeldungen erstellen werden.

        DROP DATABASE myTestBase;

-   Die Master-Datenbank verfügt über die Ansicht **sys.databases**, in der Sie Details zu allen Datenbanken einsehen können. Führen Sie zur Anzeige aller vorhandenen Datenbanken die folgende Anweisung aus:

        SELECT * FROM sys.databases;

-   In SQL-Datenbank wird die Anweisung **USE** nicht für einen Wechsel zwischen einzelnen Datenbanken unterstützt. Statt dessen müssen Sie direkt eine Verbindung zur Zieldatenbank herstellen.

<div class="dev-callout-new">
 <strong>Hinweis <span>Klicken Sie, um zu reduzieren</span></strong>
 <div class="dev-callout-content">
   <p>Viele der Transact-SQL-Anweisungen, die eine Datenbank erstellen oder modifizieren, müssen in ihrem eigenen Batch ausgeführt werden und können nicht mit anderen Transact-SQL-Anweisungen gruppiert werden. Weitere Informationen finden Sie in den anweisungsspezifischen Artikeln, die über die oben aufgeführten Links verfügbar sind.</p>
</div>
</div>

<h2><a id="Step4" name="Step4"> </a>Schritt 4: Erstellen und Verwalten von Anmeldungen</h2>

Die **Master**-Datenbank behält den Überblick über alle Anmeldungen und verfolgt, welche Anmeldungen zum Erstellen von Datenbanken oder anderen Anmeldungen berechtigt sind. Stellen Sie zur Verwaltung von Anmeldungen eine Verbindung zur **Master**-Datenbank her, und zwar mit der Hauptanmeldung auf Serverebene, die Sie beim Einrichten des Servers erstellt haben. Mit den Anweisungen
**CREATE LOGIN**, **ALTER LOGIN** oder **DROP LOGIN** können Sie Abfragen an die Master-Datenbank ausführen, die die Anmeldungen für den gesamten Server verwalten. Weitere Informationen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank][]. 


-   Verwenden Sie die Anweisung **CREATE LOGIN**, um eine neue Anmeldung auf Serverebene zu erstellen. Weitere Informationen finden Sie unter [CREATE LOGIN (SQL-Datenbank)][]. Die folgende Anweisung erstellt eine neue Anmeldung mit dem Namen **login1**. Ersetzen Sie **password1** durch ein Kennwort Ihrer Wahl.

        CREATE LOGIN login1 WITH password='password1';

-   Mit der Anweisung **CREATE USER** erteilen Sie Berechtigungen auf Datenbank-Ebene. Alle Anmeldungen müssen in der **Master**-Datenbank erstellt werden; damit eine Anmeldung jedoch auf eine andere Datenbank zugreifen kann, müssen Sie ihr Berechtigungen auf Datenbank-Ebene zuweisen, indem Sie die Anweisung **CREATE USER** für diese Datenbank ausführen. Weitere Informationen finden Sie unter [CREATE USER (SQL-Datenbank)][]. 

-   Gehen Sie folgendermaßen vor, um "login1" die Zugriffsberechtigung auf eine Datenbank mit dem Namen **myTestDB** zu erteilen:

 1.  Um den Objekt-Explorer zur Anzeige der gerade erstellten Datenbank **myTestDB** zu aktualisieren, klicken Sie im Objekt-Explorer mit der rechten Maustaste auf den Servernamen und dann mit der linken auf **Aktualisieren**.  
  Wenn Sie die Verbindung geschlossen haben, können Sie sie im Menü Datei mit dem Befehl **Objekt-Explorer verbinden** wieder öffnen. Wiederholen Sie die Anweisungen aus [Schritt 2: Verbinden mit der SQL-Datenbank][], um eine Verbindung zur Datenbank herzustellen.

 2. Klicken Sie mit der rechten Maustaste auf die Datenbank **myTestDB** und wählen Sie **Neue Abfrage**.

    3.  Führen Sie die folgende Anweisung für die Datenbank "myTestDB" durch, um einen Datenbankbenutzer namens **login1User** zu erstellen, der der Anmeldung auf Serverebene **login1** zugeordnet ist.
         CREATE USER login1User FROM LOGIN login1;

-   Nutzen Sie die gespeicherte Prozedur **sp\_addrolemember**, um das Benutzerkonto mit geeigneten Berechtigungen für die Datenbank auszustatten. Weitere Informationen finden Sie unter [sp_addrolemember (Transact-SQL)][]. Die folgende Anweisung erteilt **login1User** Lesezugriff auf die Datenbank, indem sie **login1User** der Rolle **db\_datareader** hinzufügt.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Verwenden Sie die Anweisung **ALTER LOGIN**, um eine bestehende Anmeldung zu modifizieren, etwa wenn sie das zugehörige Kennwort ändern möchten. Weitere Informationen finden Sie unter [ALTER LOGIN (SQL-Datenbank)][]. Die Anweisung **ALTER LOGIN** sollte bezogen auf die **Master**-Datenbank ausgeführt werden. Wechseln Sie wieder zurück zu dem Abfragefenster, das mit dieser Datenbank verbunden ist. 
 Die folgende Anweisung modifiziert die Anmeldung **login1**, um das Kennwort zurückzusetzen. Ersetzen Sie **newPassword** durch ein Kennwort Ihrer Wahl, und **oldPassword** durch das aktuelle Kennwort der Anmeldung.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Mit der Anweisung **DROP LOGIN** können Sie eine bestehende Anmeldung löschen. Wenn Sie eine Anmeldung auf Serverebene löschen, werden auch alle zugehörigen Konten von Datenbankbenutzern gelöscht. Weitere Informationen finden Sie unter [DROP DATABASE (SQL-Datenbank)][]. Die Anweisung **DROP LOGIN** sollte bezogen auf die **Master**-Datenbank ausgeführt werden. Die folgende Anweisung löscht die Anmeldung **login1**.
     DROP LOGIN login1;

-   Die Master-Datenbank verfügt über eine Ansicht **sys.sql\_logins**, in der Sie die Anmeldungen einsehen können. Führen Sie zur Anzeige aller vorhandenen Anmeldungen die folgende Anweisung aus:

        SELECT * FROM sys.sql_logins;

<h2><a id="Step5" name="Step5"> </a>Schritt 5: Überwachen von SQL-Datenbanken mit dynamischen Verwaltungssichten</h2>

SQL-Datenbank unterstützt mehrere Dynamic Management Views, mit denen Sie eine einzelne Datenbank überwachen können. Im Folgenden finden Sie einige wenige Beispiele für die Art Überwachungsdaten, die Sie mit diesen Ansichten abrufen können. Alle Details und weitere Anwendungsbeispiele finden Sie unter [Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten][].

-   Die Abfrage einer dynamischen Verwaltungssicht erfordert die Berechtigung **DATENBANKSTATUS ANZEIGEN**. Wenn Sie einem bestimmten Benutzer die Berechtigung **DATENBANKSTATUS ANZEIGEN** zuweisen möchten, stellen Sie mit Ihrer Prinzipalanmeldung auf Serverebene eine Verbindung mit der Datenbank her, die Sie verwalten möchten, und führen die folgende Anweisung für die Datenbank aus:

        GRANT VIEW DATABASE STATE TO login1User;

-   Berechnen Sie die Datenbankgröße mit der **sys.dm\_db\_partition\_stats**-Ansicht. Die Ansicht **sys.dm\_db\_partition\_stats** gibt die Anzahl von Seiten und Zeilen für jede Partition der Datenbank zurück, mit der Sie dann die Datenbankgröße errechnen können. Die folgende Abfrage gibt die Größe Ihrer Datenbank in Megabyte zurück:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   In den Ansichten **sys.dm\_exec\_connections** und **sys.dm\_exec\_sessions** können Sie Informationen über aktuelle Benutzerverbindungen und interne Aufgaben abrufen, die der Datenbank zugeordnet sind. Die folgende Abfrage gibt Informationen über die aktuelle Verbindung zurück:

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

-   In der Ansicht **sys.dm\_exec\_query\_stats** können Sie aggregierte Leistungsstatistiken für zwischengespeicherte Abfragepläne abrufen. Die folgende Abfrage gibt Informationen über die "Top-Fünf"-Abfragen gemessen an durchschnittlicher CPU-Zeit zurück.

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

* [Einführung in die SQL-Datenbank][]   
* [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank][]   
* [Überwachen der SQL-Datenbank mit dynamischen Verwaltungssichten][]   
* [SQL-Datenbank-Bereitstellungsmodell][]   
* [Hinzufügen von Benutzern zu Ihrer SQL-Datenbank][]   
* [Transact-SQL-Referenz (SQL-Datenbank)][]

  [Verwenden von Azure SQL-Datenbank]: http://www.windowsazure.com/de-de/develop/net/how-to-guides/sql-azure/
  [Schritt 1: Installieren von SQL Server Management Studio]: #Step1
  [Schritt 2: Verbinden mit der SQL-Datenbank]: #Step2
  [Schritt 3: Erstellen und Verwalten von Datenbanken]: #Step3
  [Schritt 4: Erstellen und Verwalten von Anmeldungen]: #Step4
  [Schritt 5: Überwachen von SQL-Datenbanken mit dynamischen Verwaltungssichten]: #Step5
  [Microsoft SQL Server 2012 Express]: http://www.microsoft.com/de-de/download/details.aspx?id=29062
  [SSMS Installer - Installationstyp wählen]: /media/installer_installation_type.png
  [SSMS Installer - Funktionen wählen]: /media/installer_feature_selection.png
  [SSMS Installer - Installation abgeschlossen]: /media/installer_completed.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com/
  [Abrufen des SQL-Datenbankservernamens aus dem Verwaltungsportal]: /media/portal_get_database_name.png
  [Verbindung mit SSMS herstellen]: /media/ssms_connect.png
  [Verbindung mit SSMS herstellen - Eigenschaften]: /media/ssms_connect_properties.png
  [Transact-SQL-Referenz (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/bb510741(v=sql.120).aspx
  [CREATE DATABASE (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336274.aspx
  [ALTER DATABASE (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ff394109.aspx
  [DROP DATABASE (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336259.aspx
  [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336235.aspx
  [CREATE LOGIN (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336268.aspx
  [CREATE USER (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/ee336277.aspx
  [sp_addrolemember (Transact-SQL)]: http://msdn.microsoft.com/de-de/library/ms187750.aspx
  [ALTER LOGIN (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336254.aspx
  [Überwachen der SQL-Datenbank mit dynamischen Verwaltungssichten]: http://msdn.microsoft.com/de-de/library/windowsazure/ff394114.aspx
  [Einführung in die SQL-Datenbank]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336230.aspx
  [SQL-Datenbank-Bereitstellungsmodell]: http://msdn.microsoft.com/de-de/library/ee336227.aspx
  [Hinzufügen von Benutzern zu Ihrer SQL-Datenbank]: http://blogs.msdn.com/b/sqlazure/archive/2010/06/21/10028038.aspx
<!--HONumber=42-->
