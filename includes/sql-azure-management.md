# Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio

Sie könnnen das Azure SQL-Datenbank-Verwaltunsportal oder die SQL Server Management Studio (SSMS)-Clientanwendung zur Verwaltung Ihrer SQL-Datenbankabonnements verwenden und zugeordnete logische Server und Datenbanken erstellen und verwalten. Die folgende Anleitung beschreibt, wie Sie mit Management Studio die logischen Server und Datenbanken der SQL-Datenbank verwalten.

<div class="dev-callout-new-collapsed">
<strong>Hinweis <span>Klicken Sie, um zu reduzieren</span></strong>
<div class="dev-callout-content">
<p>Sie k&ouml;nnen die Version SQL Server 2014, SQL Server 2012 oder SQL Server 2008 R2 von SSMS verwenden. Fr&uuml;here Versionen werden nicht unterst&uuml;tzt.</p>
</div>

</div>

Dieses Thema umfasst die folgenden Schritte:

-   [Schritt 1: Installieren von SQL Server Management Studio][Schritt 1: Installieren von SQL Server Management Studio]
-   [Schritt 2: Verbinden mit der SQL-Datenbank][Schritt 2: Verbinden mit der SQL-Datenbank]
-   [Schritt 3: Erstellen und Verwalten von Datenbanken][Schritt 3: Erstellen und Verwalten von Datenbanken]
-   [Schritt 4: Erstellen und Verwalten von Anmeldungen][Schritt 4: Erstellen und Verwalten von Anmeldungen]
-   [Schritt 5: Überwachen von SQL-Datenbanken mit dynamischen Verwaltungssichten][Schritt 5: Überwachen von SQL-Datenbanken mit dynamischen Verwaltungssichten]

## <span id="Step1"></span> </a>Schritt 1: Installieren von Management Studio

Management Studio ist eine integrierte Umgebung zur
Verwaltung von SQL-Datenbanken. Wenn Sie Datenbanken
auf Azure verwalten, können Sie die mit SQL Server installierte Management
Studio-Anwendung verwenden oder die kostenlose Version SQL Server 2012 Management Studio Express (SSMSE) herunterladen. Die folgenden Schritte
beschreiben die Installation von SSMSE.

1.  Wählen Sie auf der Seite [Microsoft SQL Server 2012 Express][Microsoft SQL Server 2012 Express] die x86-Version von Management Studio, wenn Sie ein 32-Bit-Betriebssystem ausführen, oder x64 bei 64-Bit-Betriebssystemen. Klicken Sie auf **Download** und führen Sie dann nach Aufforderung das Setup aus.

2.  Klicken Sie auf **Neue eigenständige SQL Server-Installation oder
    Hinzufügen von Features zu einer vorhandenen Installation** und auf **OK**.

3.  Akzeptieren Sie die Lizenzbedingungen, und klicken Sie auf **Weiter**.

4.  Klicken Sie auf **Installieren**, um die vom SQL Server-Setup benötigten Dateien zu installieren.

5.  Auf dem Bildschirm **Featureauswahl** ist die Option **Verwaltungstools -
    Einfach** bereits ausgewählt. Grund hierfür ist die Tatsache, dass Sie den Installationsassistenten für Management Studio ausführen. Wenn Sie das Setup für das gesamte SQL Server-Express ausführen, wählen Sie die Option **Verwaltungstools - Einfach** und klicken Sie auf **Weiter**.

6.  Auf dem Bildschirm **Fehlerberichterstattung** können Sie optional
    wählen, Fehlerberichte an Microsoft zu senden. Dies ist zur Verwendung von SSMSE nicht erforderlich. Klicken Sie auf
    **Weiter**, um die Installation zu beginnen.

7.  Nach Abschluss der Installation wird die Seite **Abgeschlossen**
     angezeigt. Klicken Sie auf **Schließen**.

## <span id="Step2"></span> </a>Schritt 2: Verbinden mit SQL Database

Für eine Verbindung mit der SQL-Datenbank müssen Sie den Servernamen auf Azure kennen. Eventuell müssen Sie sich beim Portal anmelden, um diese Information zu erhalten.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.

2.  Klicken Sie im linken Bereich auf **SQL-Datenbanken**.

3.  Klicken Sie oben auf der Startseite der SQL-Datenbanken auf **SERVER**, um eine Liste aller Server anzuzeigen, die Ihrem Abonnement zugeordnet sind. Suchen Sie den Namen des gewünschten Servers und kopieren Sie ihn in die Zwischenablage.

    Konfigurieren Sie als Nächstes die Firewall der
    SQL-Datenbank, um Verbindungen von Ihrem lokalen Computer zu ermöglichen. Geben Sie hierzu die IP-Adresse Ihres lokalen Computers in die Ausnahmeliste der Firewall ein.

4.  Klicken Sie auf der Startseite der SQL-Datenbanken auf **SERVER** und dann auf den Server, mit dem Sie eine Verbindung herstellen möchten.

5.  Klicken Sie oben auf der Seite auf **Konfigurieren**.

6.  Kopieren Sie die IP-Adresse in das Feld **CURRENT CLIENT IP ADDRESS**.

7.  Auf der Seite **Konfigurieren** sind für **Zugelassene IP-Adressen** drei Felder verfügbar, in denen Sie einen Regelnamen und die Start- und Endwerte eines IP-Adressbereichs eingeben können. Als Regelnamen können Sie den Namen Ihres Computers wählen. Kopieren Sie für Bereichsanfang und -ende die IP-Adresse Ihres Computers in beide Felder und aktivieren Sie das Kontrollkästchen, das anschließend angezeigt wird.

    Der Regelname muss eindeutig sein. Wenn es sich um Ihren Entwicklungscomputer handelt, können Sie seine IP-Adresse in beide Felder eingeben und als Anfangs- und Endwert des IP-Adressbereichs nutzen. Andernfalls kann es erforderlich sein, einen größeren IP-Adressbereich zu wählen, um Verbindungen von weiteren Computern in Ihrer Organisation zu ermöglichen.

8.  Klicken Sie unten auf der Seite auf **Speichern**.

    **Hinweis:** Es kann bis zu fünf Minuten dauern, bis die Änderungen
    der Firewalleinstellungen wirksam werden.

    Sie können nun über Management Studio eine Verbindung zur SQL-Datenbank aufbauen.

9.  Klicken Sie in der Taskleiste auf **Start**, zeigen Sie auf **Alle Programme**
    und dann auf **Microsoft SQL Server 2012**. Klicken Sie
    anschließend auf **SQL Server Management Studio**.

10. Geben Sie unter **Verbindung mit Server herstellen**
    den vollqualifizierten Servernamen als *serverName*.database.windows.net ein. Auf Azure ist der Servername eine automatisch aus alphanumerischen Zeichen generierte Zeichenfolge.

11. Wählen Sie **SQL Server-Authentifizierung**.

12. Geben Sie im Feld **Anmeldung** den SQL Server-Administratornamen ein,
    den Sie im Portal beim Erstellen des Servers im Format
    [\*Anmeldung\*@\*IhrServerName\*][\*Anmeldung\*@\*IhrServerName\*] angegeben haben.

13. Geben Sie im Feld **Kennwort** das Kennwort ein, das Sie im Portal
    beim Erstellen des Servers angegeben haben.

14. Klicken Sie auf **Verbinden**, um die Verbindung herzustellen.

Auf Azure ist jeder logische SQL-Datenbankserver eine Abstraktion, die eine Gruppierung von Datenbanken definiert. Der physische Speicherort der einzelnen Datenbanken kann sich auf jedem Computer des Datencenters befinden.

In früheren Versionen mussten Sie beim Einrichten der Verbindung in Management Studio eine direkte Verbindung zum **Master** herstellen. Dieser Schritt ist nicht mehr erforderlich. Verbindungen können nun auf Basis von Servername, Authentifizierungstyp und Anmeldedaten des Administrators erfolgreich hergestellt werden.

Viele der SSMS-Assistenten, mit denen Sie Aufgaben
wie das Erstellen und Ändern von Anmeldungen und Datenbanken in einer SQL Server-
Datenbank erledigen können, stehen für SQL-Datenbanken auf
Azure nicht zur Verfügung. Sie müssen diese Aufgaben daher mit Transact-SQL-Anweisungen durchführen. Die folgenden
Schritte enthalten Beispiele für derartige Anweisungen. Weitere Informationen zur Verwendung
von Transact-SQL mit der SQL-Datenbank, einschließlich detaillierter
Angaben zu unterstützten Befehlen, finden Sie unter [Transact-SQL-Referenz (SQL-Datenbank)][Transact-SQL-Referenz (SQL-Datenbank)].

## <span id="Step3"></span> </a>Schritt 3: Erstellen und Verwalten von Datenbanken

Während Sie mit der **Masterdatenbank** verbunden sind, können
Sie neue Datenbanken auf dem Server erstellen und bestehende Datenbanken ändern oder löschen. Die folgenden
Schritte beschreiben, wie Sie eine Reihe gängiger Aufgaben der
Datenbankverwaltung über Management Studio erledigen können. Vergewissern Sie sich zur Durchführung dieser Aufgaben, dass
Sie mit der **Masterdatenbank** verbunden sind, und zwar mit der
Prinzipalanmeldung auf Serverebene, die Sie bei der Einrichtung des Servers erstellt haben.

Öffnen Sie ein Abfragefenster in Management Studio, indem Sie den Ordner "Datenbanken" öffnen, den Ordner **Systemdatenbanken** erweitern, mit der rechten Maustaste auf **Master** und dann mit der linken auf **Neue Abfrage** klicken.

-   Verwenden Sie die Anweisung **CREATE DATABASE**, um eine neue Datenbank zu erstellen. Weitere
    Informationen finden Sie unter [CREATE DATABASE (SQL-Datenbank)][CREATE DATABASE (SQL-Datenbank)]. Die folgende Anweisung erstellt eine neue Datenbank
    mit dem Namen **myTestDB** und legt fest, dass es sich um
    eine Web Edition-Datenbank mit einer maximalen Größe von 1 GB handelt.

        CREATE DATABASE myTestDB
        (MAXSIZE=1GB,
         EDITION='web');

Klicken Sie auf **Ausführen**, um die Abfrage durchzuführen.

-   Mit der Anweisung **ALTER DATABASE** können Sie eine bestehende
    Datenbank anpassen, wenn Sie etwa den Namen, die maximale Größe
    oder die Edition (Business oder Web) der Datenbank ändern möchten. Weitere Informationen finden Sie unter [ALTER DATABASE (SQL-Datenbank)][ALTER DATABASE (SQL-Datenbank)]. Die
    folgende Anweisung passt die im vorherigen Schritt erstellte
    Datenbank an und ändert die maximale Größe in 5 GB.

        ALTER DATABASE myTestDB
        MODIFY
        (MAXSIZE=5GB,
         EDITION='web');

-   Verwenden Sie die Anweisung **DROP DATABASE**, um eine vorhandene
    Datenbank zu löschen. Weitere Informationen finden Sie unter [DROP DATABASE (SQL-Datenbank)][DROP DATABASE (SQL-Datenbank)]. Mit der folgenden Anweisung löschen Sie die Datenbank **myTestDB**
    ; führen Sie dies jedoch jetzt noch nicht durch, da Sie für sie im nächsten Schritt Anmeldungen erstellen werden.

        DROP DATABASE myTestBase;

-   Die Masterdatenbank verfügt über die Ansicht **sys.databases**, in der
    Sie Details zu allen Datenbanken einsehen können. Führen Sie zur Anzeige aller
    vorhandenen Datenbanken die folgende Anweisung aus:

        SELECT * FROM sys.databases;

-   In der SQL-Datenbank wird die Anweisung **USE** nicht für einen Wechsel
    zwischen einzelnen Datenbanken unterstützt. Stattdessen müssen Sie direkt eine Verbindung
    zur Zieldatenbank herstellen.

<div class="dev-callout-new">
 <strong>Hinweis <span>Klicken Sie, um zu reduzieren</span></strong>
 <div class="dev-callout-content">
   <p>Viele der Transact-SQL-Anweisungen zum Erstellen oder &Auml;ndern einer
Datenbank m&uuml;ssen in ihrem eigenen Batch ausgef&uuml;hrt werden und k&ouml;nnen nicht mit
anderen Transact-SQL-Anweisungen gruppiert werden. Weitere Informationen finden Sie in den anweisungsspezifischen Artikeln, die &uuml;ber die oben aufgef&uuml;hrten Links verf&uuml;gbar sind.</p>
</div>

</div>

## <span id="Step4"></span> </a>Schritt 4: Erstellen und Verwalten von Anmeldungen

Die **Masterdatenbank** beobachtet alle Anmeldungen und verfolgt,
welche Anmeldungen zum Erstellen von Datenbanken oder anderen Anmeldungen berechtigt sind. Stellen Sie zur
Verwaltung von Anmeldungen eine Verbindung zur **Masterdatenbank** her,
und zwar mit der Prinzipalanmeldung auf Serverebene, die Sie beim Einrichten des Servers erstellt haben. Mit den
Anweisungen **CREATE LOGIN**, **ALTER LOGIN** oder **DROP LOGIN**
können Sie Abfragen für die Masterdatenbank ausführen, die
die Anmeldungen für den gesamten Server verwalten. Weitere Informationen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank][Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank].

-   Verwenden Sie die Anweisung **CREATE LOGIN**, um eine neue
    Anmeldung auf Serverebene zu erstellen. Weitere Informationen finden Sie unter [CREATE LOGIN (SQL-Datenbank)][CREATE LOGIN (SQL-Datenbank)]. Die folgende Anweisung erstellt eine neue
    Anmeldung mit dem Namen **login1**. Ersetzen Sie **password1** durch ein Kennwort
    Ihrer Wahl.

        CREATE LOGIN login1 WITH password='password1';

-   Mit der Anweisung **CREATE USER** erteilen Sie Berechtigungen
    auf Datenbankebene. Alle Anmeldungen müssen in der **Masterdatenbank**
    erstellt werden. Damit eine Anmeldung jedoch auf
    eine andere Datenbank zugreifen kann, müssen Sie ihr Berechtigungen auf Datenbankebene
    zuweisen, indem Sie die Anweisung **CREATE USER** für diese Datenbank ausführen. Weitere Informationen finden Sie unter [CREATE USER (SQL-Datenbank)][CREATE USER (SQL-Datenbank)].

-   Gehen Sie
    folgendermaßen vor, um "login1" die Zugriffsberechtigung auf eine Datenbank mit dem
    Namen **myTestDB** zu erteilen:

1.  Um den Objekt-Explorer zur Anzeige der gerade erstellten Datenbank **myTestDB** zu aktualisieren, klicken Sie im Objekt-Explorer mit der rechten Maustaste auf den Servernamen und dann mit der linken auf **Aktualisieren**.

    Wenn Sie die Verbindung geschlossen haben, können Sie sie im Menü **Datei** mit dem Befehl **Objekt-Explorer verbinden** wieder öffnen. Wiederholen Sie die Anweisungen aus [Schritt 2: Verbinden mit der SQL-Datenbank][Schritt 2: Verbinden mit der SQL-Datenbank], um eine Verbindung zur Datenbank herzustellen.

2.  Klicken Sie mit der rechten Maustaste auf die Datenbank **myTestDB** und wählen Sie **Neue Abfrage**.

    1.  Führen Sie die folgende Anweisung für die Datenbank "myTestDB"
        durch, um einen Datenbankbenutzer namens **login1User** zu erstellen,
        der der Anmeldung auf Serverebene **login1** zugeordnet ist.

            CREATE USER login1User FROM LOGIN login1;

-   Verwenden Sie die gespeicherte Prozedur **sp\_addrolemember**,
    um das Benutzerkonto mit geeigneten Berechtigungen für die Datenbank auszustatten. Weitere
    Informationen finden Sie unter [sp\_addrolemember (Transact-SQL)][sp\_addrolemember (Transact-SQL)]. Die folgende Anweisung erteilt **login1User**
     Lesezugriff auf die Datenbank, indem sie **login1User** der Rolle
    **db\_datareader** hinzufügt.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Verwenden Sie die Anweisung **ALTER LOGIN**, um eine bestehende
    Anmeldung anzupassen, etwa wenn Sie das zugehörige Kennwort ändern möchten. Weitere
    Informationen finden Sie unter [ALTER LOGIN (SQL-Datenbank)][ALTER LOGIN (SQL-Datenbank)]. Die Anweisung **ALTER LOGIN** sollte für die
    **Masterdatenbank** ausgeführt werden. Wechseln Sie wieder zurück zu dem Abfragefenster, das mit dieser Datenbank verbunden ist.

    Die folgende Anweisung ändert die Anmeldung **login1**, um das Kennwort
    zurückzusetzen. Ersetzen Sie **newPassword** durch das
    Kennwort Ihrer Wahl und **oldPassword** durch das aktuelle Kennwort für die Anmeldung.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Verwenden Sie die Anweisung **DROP LOGIN**, um eine
    vorhandene Anmeldung zu löschen. Beim Löschen einer Anmeldung auf
    Serverebene werden auch alle zugehörigen Datenbank-Benutzerkonten gelöscht. Weitere Informationen
    finden Sie unter [DROP DATABASE (SQL-Datenbank)][DROP DATABASE (SQL-Datenbank)]. Die Anweisung **DROP LOGIN**
     sollte bezogen auf die **Master**
    -Datenbank ausgeführt werden. Die
    folgende Anweisung löscht die Anmeldung **login1**.

        DROP LOGIN login1;

-   Die Masterdatenbank verfügt über die Ansicht **sys.sql\_logins**,
    in der Sie die Anmeldungen einsehen können. Führen Sie zur Anzeige aller vorhandenen
    Anmeldungen die folgende Anweisung aus:

        SELECT * FROM sys.sql_logins;

## <span id="Step5"></span> </a>Schritt 5: Überwachen von SQL-Datenbanken mit dynamischen Verwaltungssichten

Die SQL-Datenbank unterstützt mehrere dynamische Verwaltungssichten,
mit denen Sie einzelne Datenbanken überwachen können. Im Folgenden finden Sie einige
wenige Beispiele für die Art von Überwachungsdaten, die Sie mit diesen Verwaltungssichten abrufen können. Alle
Details und weitere Anwendungsbeispiele finden Sie unter [Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten][Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten].

-   Die Abfrage einer dynamischen Verwaltungssicht erfordert die Berechtigung **DATENBANKSTATUS ANZEIGEN**
    . Wenn Sie einem bestimmten Benutzer die Berechtigung **DATENBANKSTATUS
    ANZEIGEN** zuweisen möchten, stellen Sie
    mit Ihrer Prinzipalanmeldung auf Serverebene eine Verbindung
    mit der Datenbank her, die Sie verwalten möchten, und führen die folgende Anweisung für die Datenbank aus:

        GRANT VIEW DATABASE STATE TO login1User;

-   Berechnen Sie die Datenbankgröße mithilfe der Ansicht **sys.dm\_db\_partition\_stats**
    . Die Ansicht **sys.dm\_db\_partition\_stats** gibt die Anzahl
    von Seiten und Zeilen für jede Partition der Datenbank zurück, mit
    der Sie dann die Datenbankgröße errechnen können. Die folgende Abfrage gibt
    die Größe Ihrer Datenbank in Megabyte zurück:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   In den Ansichten **sys.dm\_exec\_connections** und **sys.dm\_exec\_sessions**
     können Sie Informationen über aktuelle Benutzerverbindungen
    und interne Aufgaben abrufen, die der Datenbank zugeordnet sind. Die folgende Abfrage
    gibt Informationen über die aktuelle Verbindung zurück:

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

-   In der Ansicht **sys.dm\_exec\_query\_stats** können Sie aggregierte
    Leistungsstatistiken für zwischengespeicherte Abfragepläne abrufen. Die folgende Abfrage
    gibt Informationen über die "Top-Fünf"-Abfragen gemessen an
    durchschnittlicher CPU-Zeit zurück.

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

## Zusätzliche Ressourcen

-   [Einführung in die SQL-Datenbank][Einführung in die SQL-Datenbank]
-   [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank][Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank]
-   [Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten][Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten]
-   [SQL-Datenbank-Bereitstellungsmodell][SQL-Datenbank-Bereitstellungsmodell]
-   [Hinzufügen von Benutzern zu Ihrer SQL-Datenbank][Hinzufügen von Benutzern zu Ihrer SQL-Datenbank]
-   [Transact-SQL-Referenz (SQL-Datenbank)][Transact-SQL-Referenz (SQL-Datenbank)]

  [Schritt 1: Installieren von SQL Server Management Studio]: #Step1
  [Schritt 2: Verbinden mit der SQL-Datenbank]: #Step2
  [Schritt 3: Erstellen und Verwalten von Datenbanken]: #Step3
  [Schritt 4: Erstellen und Verwalten von Anmeldungen]: #Step4
  [Schritt 5: Überwachen von SQL-Datenbanken mit dynamischen Verwaltungssichten]: #Step5
  [Microsoft SQL Server 2012 Express]: http://www.microsoft.com/de-de/download/details.aspx?id=29062
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com/
  [\*Anmeldung\*@\*IhrServerName\*]: mailto:*login*@*yourServerName*
  [Transact-SQL-Referenz (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/bb510741(v=sql.120).aspx
  [CREATE DATABASE (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336274.aspx
  [ALTER DATABASE (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ff394109.aspx
  [DROP DATABASE (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336259.aspx
  [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336235.aspx
  [CREATE LOGIN (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336268.aspx
  [CREATE USER (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/ee336277.aspx
  [sp\_addrolemember (Transact-SQL)]: http://msdn.microsoft.com/de-de/library/ms187750.aspx
  [ALTER LOGIN (SQL-Datenbank)]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336254.aspx
  [Überwachen der Azure SQL-Datenbank mit dynamischen Verwaltungssichten]: http://msdn.microsoft.com/de-de/library/windowsazure/ff394114.aspx
  [Einführung in die SQL-Datenbank]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336230.aspx
  [SQL-Datenbank-Bereitstellungsmodell]: http://msdn.microsoft.com/de-de/library/ee336227.aspx
  [Hinzufügen von Benutzern zu Ihrer SQL-Datenbank]: http://blogs.msdn.com/b/sqlazure/archive/2010/06/21/10028038.aspx
