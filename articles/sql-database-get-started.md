<properties linkid="manage-services-getting-started-with-sqldbs" urlDisplayName="How to create &amp; provision" pageTitle="Getting started with SQL Database - Azure" metaKeywords="" description="Get started creating and managing SQL Databases in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Database" authors="" solutions="" writer="" manager="" editor="" />

Erste Schritte mit der Azure SQL-Datenbank
==========================================

In diesem Lernprogramm lernen Sie die Grundlagen der Azure SQL-Datenbankverwaltung über das Azure-Verwaltungsportal kennen. Wenn Sie sich erstmals mit Datenbankverwaltung beschäftigen, können Sie anhand dieser Lektionen die grundlegenden Fertigkeiten in etwa 30 Minuten erlernen.

Für dieses Lernprogramm werden keine Erfahrungen mit SQL Server oder der Azure SQL-Datenbank vorausgesetzt. Nach Abschluss dieses Lernprogramms verfügen Sie über eine Beispieldatenbank für Azure und wissen, wie einfache Verwaltungsaufgaben im Verwaltungsportal ausgeführt werden.

Sie erstellen eine Beispieldatenbank für Azure, stellen diese bereit und fragen System- sowie Benutzerdaten mithilfe von Excel und anderen Anwendungen ab.

Inhaltsverzeichnis
------------------

-   [Schritt 1: Erstellen eines Azure-Kontos](#Subscribe)
-   [Schritt 2: Herstellen einer Verbindung mit Azure und Erstellen einer Datenbank](#Connect)
-   [Schritt 3: Konfigurieren der Firewall](#ConfigFirewall)
-   [Schritt 4: Hinzufügen von Daten und einem Schema mit dem Transact-SQL-Skript](#AddData)
-   [Schritt 5: Erstellen des Schemas](#createschema)
-   [Schritt 6: Einfügen von Daten](#insertData)
-   [Schritt 7: Abfragen von Beispiel- und Systemdaten im Verwaltungsportal für die SQL-Datenbank](#QueryDBSysData)
-   [Schritt 8: Erstellen einer Datenbankanmeldung und Zuweisen von Berechtigungen](#DBLogin)
-   [Schritt 9: Herstellen einer Verbindung von anderen Anwendungen](#ClientConnection)
-   [Schritt 10: Konfigurieren der SQL-Datensynchronisierung](#ConfigureDataSync)

Schritt 1: Erstellen eines Azure-Kontos
---------------------------------------

1.  Öffnen Sie einen Webbrowser, und navigieren Sie zu <http://www.windowsazure.com>. Um ein kostenloses Konto zu erstellen, klicken Sie auf die kostenlose Testversion oben rechts, und folgen Sie den Anweisungen.

2.  Ihr Konto wurde erstellt. Sie können jetzt loslegen.

Schritt 2: Herstellen einer Verbindung mit Azure und Erstellen einer Datenbank
------------------------------------------------------------------------------

1.  Melden Sie sich beim [Verwaltungsportal](http://manage.windowsazure.com) an. Sie sollten etwa folgenden Navigationsbereich sehen:

    ![Image1](./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png)

2.  Klicken Sie unten auf der Seite auf **New**. Sobald Sie auf **New** klicken, wird eine Liste mit Objekten, die Sie erstellen können, auf dem Bildschirm angezeigt.

3.  Klicken Sie auf **SQL Database** und dann auf **Custom Create**.

    ![Image2](./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png)

    Mit dieser Option können Sie gleichzeitig einen neuen Server mit Ihnen als Administrator erstellen. Als Systemadministrator können Sie mehr Aufgaben ausführen, wie das Herstellen einer Verbindung zum Verwaltungsportal für die SQL-Datenbank, was Sie später in diesem Lernprogramm durchführen werden.

4.  Die Seite mit den Datenbankeinstellungen wird angezeigt, wenn Sie auf **Custom Create** klicken. Auf dieser Seite geben Sie grundlegende Informationen an, mit denen eine leere Datenbank auf dem Server erstellt wird. Das Hinzufügen von Tabellen und Daten erfolgt in einem nachfolgenden Schritt.

    Füllen Sie die Datenbankeinstellungen wie folgt aus:

    ![Image3](./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG)

    -   Geben Sie als Datenbanknamen **School** ein.

    -   Verwenden Sie die Standardeinstellungen für Edition, Maximalgröße und Sammlung.

    -   Klicken Sie auf **New SQL Database Server**. Durch Auswahl eines neuen Servers wird eine zweite Seite hinzugefügt, auf der wir das Administratorkonto und die Region einrichten werden.

    -   Klicken Sie anschließend auf den Pfeil, um zur nächsten Seite zu gelangen.

5.  Füllen Sie die Servereinstellungen wie folgt aus:

    ![Image4](./media/sql-database-get-started/4ServerSettings_SQLTut.PNG)

    -   Geben Sie einen Administratornamen (in einem Wort ohne Leerzeichen) ein. Die SQL-Datenbank verwendet SQL-Authentifizierung über eine verschlüsselte Verbindung, um die Benutzeridentität zu überprüfen. Mit dem von Ihnen angegebenen Namen wird eine neue SQL Server-Authentifizierungsanmeldung mit Administratorberechtigungen erstellt. Der Administratorname darf kein Windows-Benutzer sein und sollte auch keine Windows Live ID sein. Windows-Authentifizierung wird in SQL-Datenbanken nicht unterstützt.

    -   Geben Sie ein sicheres Kennwort mit mindestens acht Zeichen an, das eine Kombination aus Groß- und Kleinbuchstaben und Zahlen oder Symbolen enthält.

    -   Wählen Sie eine Region aus. Die Region bestimmt den geografischen Standort des Servers. Die Region kann nicht einfach geändert werden, wählen Sie also einen sinnvollen Standort für den Server aus. Verwenden Sie einen Standort, der sich nahe bei Ihnen oder Ihren Benutzern befindet. Wenn Sie Ihre Azure-Anwendung und -Datenbank in derselben Region platzieren, sparen Sie Zugangskosten für Bandbreite und Datenlatenz.

    -   Lassen Sie das Kontrollkästchen **Allow Azure Services to access this server** aktiviert, damit Sie über das Verwaltungsportal für die SQL-Datenbank, Excel in Office 365 oder Azure SQL Reporting eine Verbindung zur Datenbank herstellen können.

    -   Klicken Sie abschließend auf das Häkchen unten auf der Seite.

    Beachten Sie, dass Sie keinen Servernamen angegeben haben. Da der SQL-Datenbankserver weltweit zugänglich sein muss, konfiguriert die SQL-Datenbank die entsprechenden DNS-Einträge, wenn der Server erstellt wird. Dieser generierte Name stellt sicher, dass es keine Namenskonflikte mit anderen DNS-Einträgen gibt. Sie können den Namen Ihres SQL-Datenbankservers nicht ändern.

    Im nächsten Schritt konfigurieren Sie die Firewall, sodass Verbindungen von Anwendungen, die auf Ihrem Computer ausgeführt werden, Zugriff auf die Datenbanken auf dem SQL-Datenbankserver erhalten.

Schritt 3: Konfigurieren der Firewall
-------------------------------------

Zur Konfiguration der Firewall, damit Verbindungen zulässig sind, geben Sie die Informationen auf der Serverseite ein.

**Hinweis:** Der SQL-Datenbankdienst ist nur mit dem vom TDS-Protokoll verwendeten TCP-Port 1433 verfügbar. Stellen Sie also sicher, dass die Firewall im Netzwerk und auf Ihrem lokalen Computer ausgehende TCP-Kommunikation auf Port 1433 zulässt. Weitere Informationen finden Sie unter [SQL-Datenbank-Firewall](http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-en-us.aspx).

1.  Klicken Sie im linken Navigationsbereich auf **SQL Databases**.

2.  Klicken Sie oben auf der Seite auf **Servers**. Klicken Sie anschließend auf den gerade erstellten Server, sodass rechts davon ein weißer Pfeil angezeigt wird. Klicken Sie auf den Pfeil, um die Serverseite zu öffnen.

    ![Image5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

3.  Klicken Sie auf der Serverseite auf **Configure**, um die Firewall-Konfigurationseinstellungen zu öffnen, und geben Sie folgende Regel an:

    ![Image6](./media/sql-database-get-started/6DBConfigFirewall_SQLTut.png)

    -   Kopieren Sie die aktuelle Client-IP-Adresse. Dies ist die IP-Adresse, die Ihr Router oder Proxyserver abhört. Die SQL-Datenbank erkennt die IP-Adresse, die von der aktuellen Verbindung verwendet wird, sodass Sie eine Firewall-Regel zur Genehmigung der Verbindungsanforderungen von diesem Gerät erstellen können.

    -   Fügen Sie die IP-Adresse in Bereichsanfang und -ende ein. Wenn später Verbindungsfehler auftreten, die angeben, dass der Bereich zu eng ist, können Sie diese Regel bearbeiten und den Bereich erweitern.

    -   Geben Sie einen Namen für die Firewall-Regel ein, z. B. den Namen Ihres Computers oder der Firma.

    -   Klicken Sie auf das Häkchen, um die Regel zu speichern.

    Nachdem Sie die Regel gespeichert haben, sieht Ihre Seite etwa wie im folgenden Screenshot aus.

    ![Image7](./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png)

4.  Klicken Sie unten auf der Seite auf **Save**, um den Schritt abzuschließen. Falls **Save** nicht angezeigt wird, aktualisieren Sie die Browserseite.

Sie haben jetzt einen SQL-Datenbankserver auf Azure, eine Firewall-Regel, die den Zugriff auf den Server erlaubt, sowie ein Datenbankobjekt und eine Administratoranmeldung. Anschließend verwenden Sie das Abfragefenster im SQL-Datenbank-Verwaltungsportal, um mit einem Transact-SQL-Skript eine vordefinierte Datenbank zu erstellen.

Mit zunehmenden Fertigkeiten können Sie weitere Vorgehensweisen zum Erstellen einer Datenbank erkunden, z. B. programmgesteuert oder mit dem Designer in SQL Server Data Tools. Falls Sie bereits über eine SQL-Serverdatenbank verfügen, die auf einem lokalen Server ausgeführt wird, können Sie die Datenbank ganz einfach auf den Azure-Server migrieren, den Sie gerade eingerichtet haben. Eine Anleitung finden Sie mithilfe der Links am Ende dieses Lernprogramms.

Schritt 4: Hinzufügen von Daten und einem Schema mit dem Transact-SQL-Skript
----------------------------------------------------------------------------

In diesem Schritt führen Sie zwei Skripte aus. Das erste erstellt ein Schema zur Definition von Tabellen, Spalten und Beziehungen. Das zweite Skript fügt die Daten hinzu. Jeder Schritt wird unabhängig in einer separaten Verbindung durchgeführt. Wenn Sie schon einmal Datenbanken in SQL Server erstellt haben, werden Sie den Unterschied feststellen, dass in der SQL-Datenbank die Befehle CREATE und INSERT separat ausgeführt werden müssen. Die SQL-Datenbank macht dies erforderlich, um Angriffe auf Daten während der Übertragung zu minimieren.

**Hinweis:** Die Schema- und Datenwerte sind aus diesem [MSDN-Artikel](http://msdn.microsoft.com/en-us/library/windowsazure/ee621790.aspx "MSDN-Artikel") übernommen und wurden für die SQL-Datenbank angepasst.

1.  Wechseln Sie zur Startseite. Im [Verwaltungsportal](http://manage.windowsazure.com) wird nun die Datenbank **School** in der Objektliste auf der Startseite angezeigt.

    ![Image8](./media/sql-database-get-started/8MainPageHome_SQLTut.png)

2.  Klicken Sie auf **School**, sodass rechts davon ein weißer Pfeil angezeigt wird. Klicken Sie auf den Pfeil, um die Datenbankseite zu öffnen.

    ![Image9](./media/sql-database-get-started/9DBListSchool_SQLTut.png)

3.  Klicken Sie unten auf der Seite auf **Manage**. Falls dies nicht angezeigt wird, aktualisieren Sie die Browserseite. Daraufhin wird das SQL-Datenbank-Verwaltungsportal geöffnet. Dieses Portal ist separat vom Azure-Verwaltungsportal. Sie verwenden dieses Portal zum Ausführen von Transact-SQL-Befehlen und -Abfragen.

    ![Image10](./media/sql-database-get-started/10DBPortalManageButton_SQLTut.png)

4.  Geben Sie den Anmeldenamen und das Kennwort des Administrators ein. Dies ist die Administratoranmeldung, die Sie beim Erstellen des Servers angegeben haben.

    ![Image11](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)

5.  Klicken Sie im Verwaltungsportal für die SQL-Datenbank auf **New Query**. Daraufhin wird im Arbeitsbereich ein neues Abfragefenster geöffnet. Im nächsten Schritt verwenden Sie dieses Fenster, um eine Reihe vordefinierter Skripte zu kopieren, welche die leere Datenbank mit einer Struktur und Daten versehen.

    ![Image12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)

Schritt 5: Erstellen des Schemas
--------------------------------

In diesem Schritt erstellen Sie das Schema mit dem folgenden Skript. Das Skript überprüft zuerst, ob eine Tabelle mit demselben Namen vorhanden ist, um sicherzustellen, dass es keine Namenskonflikte gibt, und erstellt dann die Tabelle mit der Anweisung [CREATE TABLE](http://msdn.microsoft.com/en-us/library/windowsazure/ee336258.aspx). Anschließend verwendet das Skript die Anweisung [ALTER TABLE](http://msdn.microsoft.com/en-us/library/windowsazure/ee336286.aspx), um den Primärschlüssel und die Tabellenbeziehungen anzugeben.

Kopieren Sie das Skript, und fügen Sie es in das Abfragefenster ein. Klicken Sie oben im Fenster auf **Run**, um das Skript auszuführen.

``` {data-morhtml="true"}
    -- Create the Department table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Department]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Department](
        [DepartmentID] [int] NOT NULL,
        [Name] [nvarchar](50) NOT NULL,
        [Budget] [money] NOT NULL,
        [StartDate] [datetime] NOT NULL,
        [Administrator] [int] NULL,
     CONSTRAINT [PK_Department] PRIMARY KEY CLUSTERED 
    (
    [DepartmentID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    GO

    -- Create the Person table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Person]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Person](
        [PersonID] [int] IDENTITY(1,1) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [HireDate] [datetime] NULL,
        [EnrollmentDate] [datetime] NULL,
     CONSTRAINT [PK_School.Student] PRIMARY KEY CLUSTERED   
    (
    [PersonID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Create the OnsiteCourse table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OnsiteCourse](
        [CourseID] [int] NOT NULL,
        [Location] [nvarchar](50) NOT NULL,
        [Days] [nvarchar](50) NOT NULL,
        [Time] [smalldatetime] NOT NULL,
     CONSTRAINT [PK_OnsiteCourse] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Create the OnlineCourse table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OnlineCourse]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OnlineCourse](
        [CourseID] [int] NOT NULL,
        [URL] [nvarchar](100) NOT NULL,
     CONSTRAINT [PK_OnlineCourse] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    --Create the StudentGrade table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[StudentGrade]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[StudentGrade](
        [EnrollmentID] [int] IDENTITY(1,1) NOT NULL,
        [CourseID] [int] NOT NULL,
        [StudentID] [int] NOT NULL,
        [Grade] [decimal](3, 2) NULL,
     CONSTRAINT [PK_StudentGrade] PRIMARY KEY CLUSTERED 
    (
        [EnrollmentID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Create the CourseInstructor table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[CourseInstructor]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[CourseInstructor](
        [CourseID] [int] NOT NULL,
        [PersonID] [int] NOT NULL,
     CONSTRAINT [PK_CourseInstructor] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC,
        [PersonID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Create the Course table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Course]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Course](
        [CourseID] [int] NOT NULL,
        [Title] [nvarchar](100) NOT NULL,
        [Credits] [int] NOT NULL,
        [DepartmentID] [int] NOT NULL,
     CONSTRAINT [PK_School.Course] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    GO

    -- Create the OfficeAssignment table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]')
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OfficeAssignment](
        [InstructorID] [int] NOT NULL,
        [Location] [nvarchar](50) NOT NULL,
        [Timestamp] [timestamp] NOT NULL,
     CONSTRAINT [PK_OfficeAssignment] PRIMARY KEY CLUSTERED 
    (
        [InstructorID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    GO

    -- Define the relationship between OnsiteCourse and Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnsiteCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]'))
    ALTER TABLE [dbo].[OnsiteCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnsiteCourse_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[OnsiteCourse] CHECK 
       CONSTRAINT [FK_OnsiteCourse_Course];
    GO

    -- Define the relationship between OnlineCourse and Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnlineCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnlineCourse]'))
    ALTER TABLE [dbo].[OnlineCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnlineCourse_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[OnlineCourse] CHECK 
       CONSTRAINT [FK_OnlineCourse_Course];
    GO
    -- Define the relationship between StudentGrade and Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))
    ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Course];
    GO

    --Define the relationship between StudentGrade and Student.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Student]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))   
    ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Student] FOREIGN KEY([StudentID])
    REFERENCES [dbo].[Person] ([PersonID]);
    GO
    ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Student];
    GO

    -- Define the relationship between CourseInstructor and Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
     WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Course]')
     AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
    ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
     CONSTRAINT [FK_CourseInstructor_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[CourseInstructor] CHECK 
      CONSTRAINT [FK_CourseInstructor_Course];
    GO

    -- Define the relationship between CourseInstructor and Person.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
      WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Person]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
    ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
      CONSTRAINT [FK_CourseInstructor_Person] FOREIGN KEY([PersonID])
    REFERENCES [dbo].[Person] ([PersonID]);
    GO
    ALTER TABLE [dbo].[CourseInstructor] CHECK 
     CONSTRAINT [FK_CourseInstructor_Person];
    GO

    -- Define the relationship between Course and Department.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
    ALTER TABLE [dbo].[Course]  WITH CHECK ADD  
       CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
    REFERENCES [dbo].[Department] ([DepartmentID]);
    GO
    ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];
    GO

    --Define the relationship between OfficeAssignment and Person.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
      WHERE object_id = OBJECT_ID(N'[dbo].[FK_OfficeAssignment_Person]')
      AND parent_object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]'))
    ALTER TABLE [dbo].[OfficeAssignment]  WITH CHECK ADD  
      CONSTRAINT [FK_OfficeAssignment_Person] FOREIGN KEY([InstructorID])
    REFERENCES [dbo].[Person] ([PersonID]);
    GO
    ALTER TABLE [dbo].[OfficeAssignment] CHECK 
     CONSTRAINT [FK_OfficeAssignment_Person];
    GO
```

Schritt 6: Einfügen von Daten
-----------------------------

Öffnen Sie ein neues Abfragefenster, und fügen Sie das folgende Skript ein. Führen Sie das Skript zum Einfügen von Daten aus. Dieses Skript verwendet die Anweisung [INSERT](http://msdn.microsoft.com/en-us/library/windowsazure/ee336284.aspx), um Werte in jede Spalte einzufügen.

``` {data-morhtml="true"}
    -- Insert data into the Person table.
    SET IDENTITY_INSERT dbo.Person ON;
    GO
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (1, 'Abercrombie', 'Kim', '1995-03-11', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (2, 'Barzdukas', 'Gytis', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (3, 'Justice', 'Peggy', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (4, 'Fakhouri', 'Fadi', '2002-08-06', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (5, 'Harui', 'Roger', '1998-07-01', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (6, 'Li', 'Yan', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (7, 'Norman', 'Laura', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (8, 'Olivotto', 'Nino', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (9, 'Tang', 'Wayne', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (10, 'Alonso', 'Meredith', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (11, 'Lopez', 'Sophia', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (12, 'Browning', 'Meredith', null, '2000-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (13, 'Anand', 'Arturo', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (14, 'Walker', 'Alexandra', null, '2000-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (15, 'Powell', 'Carson', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (16, 'Jai', 'Damien', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (17, 'Carlson', 'Robyn', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (18, 'Zheng', 'Roger', '2004-02-12', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (19, 'Bryant', 'Carson', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (20, 'Suarez', 'Robyn', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (21, 'Holt', 'Roger', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (22, 'Alexander', 'Carson', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (23, 'Morgan', 'Isaiah', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (24, 'Martin', 'Randall', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (25, 'Kapoor', 'Candace', '2001-01-15', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (26, 'Rogers', 'Cody', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (27, 'Serrano', 'Stacy', '1999-06-01', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (28, 'White', 'Anthony', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (29, 'Griffin', 'Rachel', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (30, 'Shan', 'Alicia', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (31, 'Stewart', 'Jasmine', '1997-10-12', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (32, 'Xu', 'Kristen', '2001-7-23', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (33, 'Gao', 'Erica', null, '2003-01-30');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (34, 'Van Houten', 'Roger', '2000-12-07', null);
    GO
    SET IDENTITY_INSERT dbo.Person OFF;
    GO
    -- Insert data into the Department table.
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (1, 'Engineering', 350000.00, '2007-09-01', 2);
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (2, 'English', 120000.00, '2007-09-01', 6);
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (4, 'Economics', 200000.00, '2007-09-01', 4);
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
    VALUES (7, 'Mathematics', 250000.00, '2007-09-01', 3);
    GO
    -- Insert data into the Course table.
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (1050, 'Chemistry', 4, 1);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (1061, 'Physics', 4, 1);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (1045, 'Calculus', 4, 7);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (2030, 'Poetry', 2, 2);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (2021, 'Composition', 3, 2);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (2042, 'Literature', 4, 2);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (4022, 'Microeconomics', 3, 4);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (4041, 'Macroeconomics', 3, 4);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (4061, 'Quantitative', 2, 4);
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
    VALUES (3141, 'Trigonometry', 4, 7);
    GO
    -- Insert data into the OnlineCourse table.
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (2030, 'http://www.fineartschool.net/Poetry');
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (2021, 'http://www.fineartschool.net/Composition');
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (4041, 'http://www.fineartschool.net/Macroeconomics');
    INSERT INTO dbo.OnlineCourse (CourseID, URL)
    VALUES (3141, 'http://www.fineartschool.net/Trigonometry');
    --Insert data into OnsiteCourse table.
    INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
    VALUES (1050, '123 Smith', 'MTWH', '11:30');
    INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
    VALUES (1061, '234 Smith', 'TWHF', '13:15');
    INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
    VALUES (1045, '121 Smith','MWHF', '15:30');
    INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
    VALUES (4061, '22 Williams', 'TH', '11:15');
    INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
    VALUES (2042, '225 Adams', 'MTWH', '11:00');
    INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
    VALUES (4022, '23 Williams', 'MWF', '9:00');
    -- Insert data into the CourseInstructor table.
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (1050, 1);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (1061, 31);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (1045, 5);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (2030, 4);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (2021, 27);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (2042, 25);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (4022, 18);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (4041, 32);
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
    VALUES (4061, 34);
    GO
    --Insert data into the OfficeAssignment table.
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (1, '17 Smith');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (4, '29 Adams');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (5, '37 Williams');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (18, '143 Smith');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (25, '57 Adams');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (27, '271 Williams');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (31, '131 Smith');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (32, '203 Williams');
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
    VALUES (34, '213 Smith');
    -- Insert data into the StudentGrade table.
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 2, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2030, 2, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 3, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2030, 3, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 6, 2,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2042, 6, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 7, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2042, 7, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2021, 8, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (2042, 8, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 9, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 10, null);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 11, 2,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 12, null);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4061, 12, null);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 14, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 13, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4061, 13, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 14, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 15, 2,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 16, 2);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 17, null);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 19, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4061, 20, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4061, 21, 2);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 22, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4041, 22, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4061, 22, 2,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (4022, 23, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1045, 23, 1,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 24, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 25, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1050, 26, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 26, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 27, 3);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1045, 28, 2,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1050, 28, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 29, 4);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1050, 30, 3,5);
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
    VALUES (1061, 30, 4);
    GO
```

Schritt 7: Abfragen von Beispiel- und Systemdaten im Verwaltungsportal für die SQL-Datenbank
--------------------------------------------------------------------------------------------

Um Ihre Arbeit zu überprüfen, führen Sie eine Abfrage aus, welche die soeben eingegebenen Daten zurückgibt. Sie können auch integrierte gespeicherte Prozeduren und Datenverwaltungsansichten ausführen, die Informationen zu den auf dem SQL-Datenbankserver vorhandenen Datenbanken liefern.

#### Abfragen von Beispieldaten

Um einige der soeben hinzugefügten Daten abzurufen, kopieren Sie das folgende Transact-SQL-Skript in ein neues Abfragefenster und führen es aus.

``` {data-morhtml="true"}
    SELECT
        Course.Title as "Course Title"
        ,Department.Name as "Department"
        ,Person.LastName as "Instructor"
        ,OnsiteCourse.Location as "Location"
        ,OnsiteCourse.Days as "Days"
        ,OnsiteCourse.Time as "Time"
    FROM
     Course
     INNER JOIN Department
      ON Course.DepartmentID = Department.DepartmentID
     INNER JOIN CourseInstructor
       ON Course.CourseID = CourseInstructor.CourseID
     INNER JOIN Person
       ON CourseInstructor.PersonID = Person.PersonID
     INNER JOIN OnsiteCourse
        ON OnsiteCourse.CourseID = CourseInstructor.CourseID;
```

Sie sollten etwa folgenden Ergebnissatz sehen:

![Image13](./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG)

#### Abfragen von Systemdaten

Sie können auch Systemansichten und integrierte gespeicherte Prozeduren verwenden, um Informationen vom Server abzurufen. Für Lernzwecke führen Sie einfach einige Befehle aus.

Führen Sie den folgenden Befehl aus, um herauszufinden, welche Datenbanken auf dem Server verfügbar sind.

    SELECT * FROM sys.databases  

Führen Sie diesen Befehl aus, um eine Liste der aktuell mit dem Server verbundenen Benutzer zurückzugeben.

    SELECT user_name(),suser_sname()

Führen Sie diese gespeicherte Prozedur aus, um eine Liste aller Objekte in der Datenbank **School** zurückzugeben.

    EXEC SP_help

Schließen Sie noch nicht die Portalverbindung zur Datenbank **School**. Sie werden sie in einigen Minuten erneut brauchen.

Schritt 8: Erstellen einer Datenbankanmeldung und Zuweisen von Berechtigungen
-----------------------------------------------------------------------------

In der SQL-Datenbank können Sie mit Transact-SQL Anmeldedaten erstellen und Berechtigungen zuweisen. In dieser Lektion führen Sie drei Vorgänge mit Transact-SQL aus: Erstellen einer SQL Server-Authentifizierungsanmeldung, Erstellen eines Datenbankbenutzers und Zuweisen von Berechtigungen per Rollenmitgliedschaft.

Für die Serververbindungen wird eine SQL Server-Authentifizierungsanmeldung verwendet. Alle Benutzer, die auf eine Datenbank auf dem SQL-Datenbankserver zugreifen, müssen dazu den SQL Server-Authentifizierungsanmeldenamen und das Kennwort angeben.

Um eine Anmeldung zu erstellen, stellen Sie zuerst eine Verbindung zur **master**-Datenbank her.

#### Erstellen einer SQL Server-Authentifizierungsanmeldung

1.  Wählen Sie im [Verwaltungsportal](http://manage.windowsazure.com) **SQL Databases** aus, klicken Sie auf **Servers**, wählen Sie den gewünschten Server aus, und klicken Sie dann auf den weißen Pfeil, um die Serverseite zu öffnen.

    ![Image5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

2.  Klicken Sie auf der Schnellstartseite auf **Manage Server**, um eine neue Verbindung zum Verwaltungsportal für die SQL-Datenbank zu öffnen.

3.  Geben Sie den Anmeldenamen und das Kennwort des Administrators ein. Dies ist die Adminstratoranmeldung, die Sie beim Erstellen des Servers angegeben haben.

    ![Image20](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)

4.  Das SQL-Datenbank-Verwaltungsportal wird in einem neuen Browserfenster geöffnet. Klicken Sie oben auf **Select a Database** und dann auf **master**.

    ![Image14](./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG)

5.  Falls folgender Fehler auf der Seite angezeigt wird, ignorieren Sie ihn. Klicken Sie auf **New Query**, um ein Abfragefenster zu öffnen, in dem Sie Transact-SQL-Befehle für die **master**-Datenbank ausführen können.

    ![Image15](./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG)

6.  Kopieren Sie den folgenden Befehl, und fügen Sie ihn in das Abfragefenster ein.

         CREATE LOGIN SQLDBLogin WITH password='Password1';

7.  Führen Sie den Befehl aus, um den neuen SQL Server-Anmeldenamen "SQLDBLogin" zu erstellen.

#### Erstellen eines Datenbankbenutzers und Zuweisen von Berechtigungen

Nachdem Sie eine SQL Server-Authentifizierungsanmeldung erstellt haben, weisen Sie im nächsten Schritt die Datenbank und die Berechtigungen für den Anmeldenamen zu. Dazu erstellen Sie für jede Datenbank einen **Datenbankbenutzer**.

1.  Wechseln Sie zurück zur SQL-Datenbank-Verwaltungsportalseite für die Verbindung zur Datenbank **School**. Falls Sie das Browserfenster geschlossen haben, starten Sie eine neue Verbindung zur Datenbank **School**. Verwenden Sie dazu die Schritte aus der vorhergehenden Lektion, "Hinzufügen von Daten und einem Schema mit dem Transact-SQL-Skript".

    Auf der SQL-Datenbank-Verwaltungsportalseite wird der Datenbankname **School** in der oberen linken Ecke angezeigt.

    ![Image12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)

2.  Klicken Sie auf **New Query**, um ein neues Abfragefenster zu öffnen und die folgende Anweisung hineinzukopieren:

         CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3.  Führen Sie das Skript aus. Dieses Skript erstellt einen neuen Datenbankbenutzer basierend auf dem Anmeldenamen.

    Anschließend weisen Sie Berechtigungen für die Rolle "db\_datareader" zu. Datenbankbenutzer, die dieser Rolle zugewiesen werden, können Daten aus allen Benutzertabellen in der Datenbank lesen.

4.  Öffnen Sie ein neues Abfragefenster, und führen Sie die nächste Anweisung aus. Diese Anweisung führt eine integrierte gespeicherte Prozedur aus, die dem soeben erstellten neuen Benutzer die Rolle "db\_datareader" zuweist.

         EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

    Sie haben nun eine neue SQL Server-Authentifizierungsanmeldung mit Leseberechtigung für die Datenbank **School**. Erstellen Sie anhand dieser Schritte weitere SQL Server-Authentifizierungsanmeldungen, um verschiedene Zugriffsmöglichkeiten auf die Daten zuzulassen.

Schritt 9: Herstellen einer Verbindung von anderen Anwendungen
--------------------------------------------------------------

Nachdem Sie nun eine funktionierende Datenbank haben, können Sie Verbindungen von einer Excel-Arbeitsmappe herstellen.

#### Verbindungen von Excel

Falls Excel 2010 auf Ihrem Computer installiert ist, können Sie die folgenden Schritte für eine Verbindung mit der Beispieldatenbank verwenden.

1.  Klicken Sie in Excel auf der Registerkarte "Daten" auf **Aus anderen Quellen** und anschließend auf **Von SQL Server**.

2.  Geben Sie im Datenverbindungs-Assistent den vollqualifizierten Domänennamen des SQL-Datenbankservers an, gefolgt von einem SQL Server-Authentifizierungsanmeldenamen mit Zugriffsberechtigung für die Datenbank.

Der Servername befindet sich auf der Seite **Database** unter **Quick Links**. Der Servername kann auch im Azure-Verwaltungsportal in der SQL-Datenbank auf der Serverseite im Dashboard unter **Manage URL** abgerufen werden.

Der Servername besteht aus mehreren Buchstaben und Zahlen, gefolgt von ".database.windows.net". Geben Sie diesen Namen im Datenverbindungs-Assistent an. Schließen Sie nicht das Präfix "http://" oder "https://" in den Namen ein.

Geben Sie eine SQL Server-Authentifizierungsanmeldung ein. Zu Testzwecken können Sie die Adminstratoranmeldung verwenden, die Sie beim Einrichten des Servers erstellt haben. Verwenden Sie für den regulären Datenzugriff eine Datenbank-Benutzeranmeldung ähnlich wie die soeben erstellte.

    ![Image16](./media/sql-database-get-started/16ExcelConnect_SQLTut.png)

1.  Wählen Sie auf der nächsten Seite die Datenbank **School** und anschließend **Course** aus. Klicken Sie auf **Finish**.

    ![Image17](./media/sql-database-get-started/17ExcelSelect_SQLTut.png)

2.  Das Dialogfeld "Import Data" wird angezeigt, in dem Sie angeben, wo und wie Ihre Daten importiert werden sollen. Lassen Sie die Standardoptionen ausgewählt, und klicken Sie auf **OK**.

    ![Image19](./media/sql-database-get-started/19ExcelImport_SQLTut.png)

3.  Auf dem Arbeitsblatt sollte in etwa folgende Tabelle angezeigt werden:

    ![Image18](./media/sql-database-get-started/18ExcelTable_SQLTut.PNG)

Wenn Sie nur Excel verwenden, können Sie jeweils nur eine Tabelle importieren. Eine bessere Methode ist das PowerPivot für Excel-Add-In, mit dem Sie mehrere Tabellen als einen Datensatz importieren und bearbeiten können. Die Arbeit mit PowerPivot geht über dieses Lernprogramm hinaus. Ausführliche Informationen dazu erhalten Sie auf der [Microsoft-Website](http://www.microsoft.com/en-us/bi/powerpivot.aspx).

Schritt 10: Konfigurieren der SQL-Datensynchronisierung
-------------------------------------------------------

#### SQL-Datensynchronisierung

Nachdem Sie nun die SQL-Datenbankinstanzen erstellt haben, können Sie SQL-Datensynchronisierung verwenden, um Ihre wertvollen Daten an mehreren Standorten zu synchronisieren.

SQL-Datensynchronisierung ist eine Funktion der SQL-Datenbank, mit der Sie ausgewählte Daten entweder nach Plan oder bei Bedarf synchronisieren können, ohne zusätzlichen Code oder Skripte schreiben zu müssen. Die SQL-Datensynchronisierung unterstützt die Synchronisierung zwischen SQL-Datenbankinstanzen oder hybriden Topologien, die sowohl SQL-Datenbanken als auch Instanzen von SQL Server unterstützen.

Weitere Informationen zur SQL-Datensynchronisierung finden Sie unter [Vorschau zur SQL-Datensynchronisierung](http://go.microsoft.com/fwlink/?LinkId=274959).

Nächste Schritte
----------------

Nachdem Sie nun mit der SQL-Datenbank und den Verwaltungsportalen vertraut sind, können Sie andere Werkzeuge und Techniken ausprobieren, die von den SQL Server-Datenbankadministratoren verwendet werden.

Zur aktiven Verwaltung der neuen Datenbank wird die Installation von SQL Server Management Studio empfohlen. Management Studio ist das primäre Datenbank-Administrationstool zur Verwaltung von SQL-Serverdatenbanken, einschließlich auf Azure. Mithilfe von Management Studio können Sie Abfragen zur künftigen Wiederverwendung speichern, neue Tabellen und gespeicherte Prozeduren hinzufügen und Ihre Transact-SQL-Fertigkeiten in einer funktionsreichen Skriptumgebung mit Syntaxprüfung, IntelliSense und Vorlagen ausbauen. Befolgen Sie dazu die Anweisungen unter [Verwalten von SQL-Datenbanken mit SQL Server Management Studio](http://www.windowsazure.com/en-us/develop/net/common-tasks/sql-azure-management/).

Kenntnisse der Transact-SQL-Abfrage- und Datendefinitionssprache sind für Datenbankadministratoren unumgänglich. Wenn Sie noch nicht mit Transact-SQL gearbeitet haben, beginnen Sie mit dem [Lernprogramm: Schreiben von Transact-SQL-Anweisungen](http://msdn.microsoft.com/en-us/library/ms365303.aspx), um sich einige grundlegende Kenntnisse anzueignen.

Es gibt noch andere Methoden zum Verschieben einer lokalen Datenbank zur SQL-Datenbank. Falls Sie eine vorhandene Datenbank haben oder Beispieldatenbanken zum Üben heruntergeladen haben, testen Sie folgende alternative Vorgehensweisen:

-   [Migration von Datenbanken zur SQL-Datenbank](http://msdn.microsoft.com/en-us/library/windowsazure/ee730904.aspx)
-   [Kopieren von Datenbanken in die SQL-Datenbank](http://msdn.microsoft.com/en-us/library/windowsazure/ff951624.aspx)

