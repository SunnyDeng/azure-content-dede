<properties linkid="manage-services-getting-started-with-sqldbs" urlDisplayName="How to create and provision" pageTitle="Getting started with SQL Database - Azure" metaKeywords="" description="Get started creating and managing SQL Databases in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Database" authors="loclar"  solutions="" writer="" manager="jeffreyg" editor="tysonn"  />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="loclar"></tags>

# Erste Schritte mit Microsoft Azure SQL-Datenbank

In diesem Lernprogramm lernen Sie die Grundlagen der Microsoft Azure SQL-Datenbankverwaltung über das Azure-Verwaltungsportal kennen. Wenn Sie sich erstmals mit Datenbankverwaltung beschäftigen, können Sie anhand dieser Lektionen die grundlegenden Fertigkeiten in etwa 30 Minuten erlernen.

Für dieses Lernprogramm werden keine Erfahrungen mit SQL Server oder der Azure SQL-Datenbank vorausgesetzt. Nach Abschluss dieses Lernprogramms verfügen Sie über eine Beispieldatenbank für Azure und wissen, wie einfache Verwaltungsaufgaben im Verwaltungsportal ausgeführt werden.

Sie erstellen eine Beispieldatenbank auf der Azure Platform, stellen diese bereit und fragen System- sowie Benutzerdaten mithilfe von Excel ab.

## Inhaltsverzeichnis

-   [Schritt 1: Erstellen eines Microsoft Azure-Kontos](#Subscribe)
-   [Schritt 2: Herstellen einer Verbindung mit Azure und Erstellen einer Datenbank](#Subscribe)
-   [Schritt 3: Konfigurieren der Firewall](#ConfigFirewall)
-   [Schritt 4: Hinzufügen von Daten und einem Schema mit dem Transact-SQL-Skript](#AddData)
-   [Schritt 5: Erstellen des Schemas](#createschema)
-   [Schritt 6: Einfügen von Daten](#insertData)
-   [Schritt 7: Abfragen von Beispiel- und Systemdaten im Verwaltungsportal für die SQL-Datenbank](#QueryDBSysData)
-   [Schritt 8: Erstellen einer Datenbankanmeldung und Zuweisen von Berechtigungen](#DBLogin)
-   [Schritt 9: Herstellen einer Verbindung von anderen Anwendungen](#ClientConnection)

## Schritt 1: Erstellen eines Microsoft Azure-Kontos

1.  Öffnen Sie einen Webbrowser, und navigieren Sie zu [http://azure.microsoft.com](http://azure.microsoft.com).
    Um ein kostenloses Konto zu erstellen, klicken Sie auf die kostenlose Testversion oben rechts, und folgen Sie den Anweisungen.

2.  Ihr Konto wurde erstellt. Sie können jetzt loslegen.

## Schritt 2: Herstellen einer Verbindung mit Azure und Erstellen einer Datenbank

1.  Melden Sie sich im [Verwaltungsportal](http://manage.windowsazure.com) an. Sie sollten etwa folgenden Navigationsbereich sehen:

    ![Navigationsbereich][Image1]

2.  Klicken Sie unten auf der Seite auf **New**. Sobald Sie auf **New** klicken, wird eine Liste mit Objekten, die Sie erstellen können, auf dem Bildschirm angezeigt.

3.  Klicken Sie auf **SQL Database** und dann auf **Custom Create**.

    ![Navigationsbereich][Image2]

Mit dieser Option können Sie gleichzeitig einen neuen Server und eine SQL-Datenbank mit Ihnen als Administrator erstellen. Als Systemadministrator können Sie mehr Aufgaben ausführen, wie das Herstellen einer Verbindung zum Verwaltungsportal für die SQL-Datenbank, was Sie später in diesem Lernprogramm durchführen werden.

1.  Die Seite mit den Datenbankeinstellungen wird angezeigt, wenn Sie auf **Custom Create** klicken. Auf dieser Seite geben Sie grundlegende Informationen an, mit denen eine leere SQL-Datenbank auf dem Server erstellt wird. Das Hinzufügen von Tabellen und Daten erfolgt in einem nachfolgenden Schritt.

    Füllen Sie die Datenbankeinstellungen wie folgt aus:

    ![Navigationsbereich][Image3]

-   Geben Sie als Datenbanknamen **School** ein.

-   Verwenden Sie die Standardeinstellungen für Edition, Maximalgröße und Sammlung.

-   Klicken Sie auf **New SQL Database Server**. Durch Auswahl eines neuen Servers wird eine zweite Seite hinzugefügt, auf der wir das Administratorkonto und die Region einrichten werden.

-   Klicken Sie anschließend auf den Pfeil, um zur nächsten Seite zu gelangen.

1.  Füllen Sie die Servereinstellungen wie folgt aus:

    ![Navigationsbereich][Image4]

-   Geben Sie einen Administratornamen (in einem Wort ohne Leerzeichen) ein. Die SQL-Datenbank verwendet SQL-Authentifizierung über eine verschlüsselte Verbindung, um die Benutzeridentität zu überprüfen. Mit dem von Ihnen angegebenen Namen wird eine neue SQL Server-Authentifizierungsanmeldung mit Administratorberechtigungen erstellt. Der Administratorname darf kein Windows-Benutzer und kein Live ID-Benutzername sein. Windows-Authentifizierung wird in SQL-Datenbanken nicht unterstützt.

-   Geben Sie ein sicheres Kennwort mit mindestens acht Zeichen an, das eine Kombination aus Groß- und Kleinbuchstaben und Zahlen oder Symbolen enthält. Über die Hilfe-Sprechblase erhalten Sie ausführliche Informationen zur Kennwortkomplexität.

-   Wählen Sie eine Region aus. Die Region bestimmt den geografischen Standort des Servers. Die Region kann nicht einfach geändert werden, wählen Sie also einen sinnvollen Standort für den Server aus. Wählen Sie den nächstgelegenen Standort aus. Wenn Sie Ihre Azure-Anwendung und -Datenbank in derselben Region platzieren, sparen Sie Zugangskosten für Bandbreite und Datenlatenz.

-   Lassen Sie das Kontrollkästchen **Allow Azure Services to access this server** aktiviert, damit Sie über das Verwaltungsportal für die SQL-Datenbank, Excel in Office 365 oder Azure SQL Reporting eine Verbindung zur Datenbank herstellen können.

-   Klicken Sie abschließend auf das Häkchen unten auf der Seite.

Beachten Sie, dass Sie keinen Servernamen angegeben haben. Da der SQL-Datenbankserver weltweit zugänglich sein muss, konfiguriert die SQL-Datenbank die entsprechenden DNS-Einträge, wenn der Server erstellt wird. Dieser generierte Name stellt sicher, dass es keine Namenskonflikte mit anderen DNS-Einträgen gibt. Sie können den Namen Ihres SQL Database-Servers nicht ändern.

Um den Namen des Servers anzuzeigen, der die gerade von Ihnen erstellte **School**-Datenbank hostet, klicken Sie im linken Navigationsbereich auf **SQL-Datenbanken**. Klicken Sie dann in der Listenansicht **SQL-Datenbanken** auf die **School**-Datenbank. Führen Sie auf der Seite **Schnellstart** einen Bildlauf nach unten durch, um den Servernamen anzuzeigen.

Im nächsten Schritt konfigurieren Sie die Firewall, sodass Verbindungen von Anwendungen, die auf Ihrem Computer ausgeführt werden, Zugriff auf die Datenbanken auf dem SQL-Datenbankserver erhalten.

## Schritt 3: Konfigurieren der Firewall

Zur Konfiguration der Firewall, damit Verbindungen zulässig sind, geben Sie die Informationen auf der Serverseite ein.

**Hinweis:** Der SQL-Datenbankdienst ist nur mit dem vom TDS-Protokoll verwendeten TCP-Port 1433 verfügbar. Stellen Sie also sicher, dass die Firewall im Netzwerk und auf Ihrem lokalen Computer ausgehende TCP-Kommunikation auf Port 1433 zulässt. Weitere Informationen finden Sie unter [SQL-Datenbank-Firewall](http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-de-de.aspx).

1.  Klicken Sie im linken Navigationsbereich auf **SQL Databases**.

2.  Klicken Sie oben auf der Seite auf **Servers**. Klicken Sie als Nächstes auf den gerade erstellten Server, um die Serverseite zu öffnen.

3.  Klicken Sie auf der Serverseite auf **Konfigurieren**, um die Einstellungen **Zulässige IP-Adressen** zu öffnen, und klicken Sie dann auf den Link **Zu den zulässigen IP-Adressen hinzufügen**. Dadurch wird eine neue Firewallregel erstellt, die Verbindungsanforderungen vom Router oder Proxyserver zulässt, auf den Ihr Gerät lauscht.

4.  Sie können zusätzliche Firewallregeln erstellen, indem Sie einen Regelnamen und den Anfangs- und Endwert des IP-Adressbereichs angeben.

5.  Um Interaktionen zwischen diesem Server und anderen Azure-Diensten zu ermöglichen, legen Sie für die Option **Microsoft Azure Services** den Wert **Ja** fest.

6.  Klicken Sie unten auf der Seite auf **Speichern**, um die Änderungen zu speichern.

7.  Nachdem Sie die Regel gespeichert haben, sieht Ihre Seite etwa wie im folgenden Screenshot aus.

    ![Navigationsbereich][Image7]

Sie haben jetzt einen SQL-Datenbankserver auf Azure, eine Firewall-Regel, die den Zugriff auf den Server erlaubt, sowie ein Datenbankobjekt und eine Administratoranmeldung. Sie haben jedoch noch keine lauffähige Datenbank, an die Sie Abfragen senden können. Zu diesem Zweck benötigt Ihre Datenbank ein Schema und aktuelle Daten.

Da in diesem Lernprogramm nur die direkt verfügbaren Tools eingesetzt werden, verwenden Sie das Abfragefenster im Verwaltungsportal für die SQL-Datenbank zum Ausführen eines Transact-SQL-Skripts, durch das eine vordefinierte Datenbank erstellt wird.

Mit zunehmenden Fertigkeiten können Sie weitere Vorgehensweisen zum Erstellen einer Datenbank erkunden, z. B. programmgesteuert oder mit der Entwurfsoberfläche in SQL Server Data Tools. Falls Sie bereits über eine SQL-Serverdatenbank verfügen, die auf einem lokalen Server ausgeführt wird, können Sie die Datenbank ganz einfach auf den Azure-Server migrieren, den Sie gerade eingerichtet haben. Eine Anleitung finden Sie mithilfe der Links am Ende dieses Lernprogramms.

## Schritt 4: Hinzufügen von Daten und einem Schema mit dem Transact-SQL-Skript

In diesem Schritt führen Sie zwei Skripte aus. Das erste erstellt ein Schema zur Definition von Tabellen, Spalten und Beziehungen. Das zweite Skript fügt die Daten hinzu. Jeder Schritt wird unabhängig in einer separaten Verbindung durchgeführt. Wenn Sie schon einmal Datenbanken in SQL Server erstellt haben, werden Sie den Unterschied feststellen, dass in der SQL-Datenbank die Befehle CREATE und INSERT separat ausgeführt werden müssen. Die SQL-Datenbank macht dies erforderlich, um Angriffe auf Daten während der Übertragung zu minimieren.

**Hinweis:** Die Schema- und Datenwerte sind aus diesem [MSDN-Artikel](http://msdn.microsoft.com/de-de/library/windowsazure/ee621790.aspx "MSDN-Artikel") übernommen und wurden für die SQL-Datenbank angepasst.

1.  Wechseln Sie zur Startseite. Im [Verwaltungsportal](http://manage.windowsazure.com) wird nun die Datenbank **School** in der Objektliste auf der Startseite angezeigt.

    ![Navigationsbereich][Image8]

2.  Klicken Sie auf **School**, um sie auszuwählen, und klicken Sie dann unten auf der Seite auf **Verwalten**. Daraufhin wird das SQL-Datenbank-Verwaltungsportal geöffnet. Dieses Portal ist separat vom Azure-Verwaltungsportal. Sie verwenden dieses Portal zum Ausführen von Transact-SQL-Befehlen und -Abfragen.

3.  Geben Sie den Anmeldenamen und das Kennwort des Administrators ein, um sich bei der **School**-Datenbank anzumelden. Dies ist die Adminstratoranmeldung, die Sie beim Erstellen des Servers angegeben haben.

4.  Klicken Sie im Verwaltungsportal für die SQL-Datenbank im Menüband auf **Neue Abfrage**. Daraufhin wird im Arbeitsbereich ein leeres Abfragefenster geöffnet. Im nächsten Schritt verwenden Sie dieses Fenster, um eine Reihe vordefinierter Skripte zu kopieren, welche die leere Datenbank mit einer Struktur und Daten versehen.

## Schritt 5: Erstellen des Schemas

In diesem Schritt erstellen Sie das Schema mit dem folgenden Skript. Das Skript überprüft zuerst, ob eine Tabelle mit demselben Namen vorhanden ist, um sicherzustellen, dass es keine Namenskonflikte gibt, und erstellt dann die Tabelle mit der Anweisung [CREATE TABLE](http://msdn.microsoft.com/de-de/library/windowsazure/ee336258.aspx). Anschließend verwendet das Skript die Anweisung [ALTER TABLE](http://msdn.microsoft.com/de-de/library/windowsazure/ee336286.aspx), um den Primärschlüssel und die Tabellenbeziehungen anzugeben.

Kopieren Sie das Skript, und fügen Sie es in das Abfragefenster ein. Klicken Sie oben im Fenster auf **Run**, um das Skript auszuführen.

<div style="width:auto; height:600px; overflow:auto"><pre>
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
</pre></div>

## Schritt 6: Einfügen von Daten

Öffnen Sie ein neues Abfragefenster, und fügen Sie das folgende Skript ein. Führen Sie das Skript zum Einfügen von Daten aus. Dieses Skript verwendet die Anweisung [INSERT](http://msdn.microsoft.com/de-de/library/windowsazure/ee336284.aspx), um Werte in jede Spalte einzufügen.

<div style="width:auto; height:600px; overflow:auto"><pre>
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
    
</pre></div>

## Schritt 7: Abfragen von Beispiel- und Systemdaten im Verwaltungsportal für die SQL-Datenbank

Um Ihre Arbeit zu überprüfen, führen Sie eine Abfrage aus, welche die soeben eingegebenen Daten zurückgibt. Sie können auch integrierte gespeicherte Prozeduren und Datenverwaltungsansichten ausführen, die Informationen zu den auf dem SQL-Datenbankserver vorhandenen Datenbanken liefern.

#### Abfragen von Beispieldaten

Um einige der soeben hinzugefügten Daten abzurufen, kopieren Sie das folgende Transact-SQL-Skript in ein neues Abfragefenster und führen es aus.

<div style="width:auto; height:auto; overflow:auto"><pre>
    SELECT * From Person
</pre></div>

Sie sollten ein Resultset mit 34 Zeilen aus der Tabelle "person" sehen, einschließlich PersonID, LastName, FirstName, HireDate und EnrollmentDate.

#### Abfragen von Systemdaten

Sie können auch Systemansichten und integrierte gespeicherte Prozeduren verwenden, um Informationen vom Server abzurufen. Für Lernzwecke führen Sie einfach einige Befehle aus.

Führen Sie den folgenden Befehl aus, um herauszufinden, welche Datenbanken auf dem Server verfügbar sind.

    SELECT * FROM sys.databases  

Führen Sie diesen Befehl aus, um eine Liste der aktuell mit dem Server verbundenen Benutzer zurückzugeben.

    SELECT user_name(),suser_sname()

Führen Sie diese gespeicherte Prozedur aus, um eine Liste aller Objekte in der Datenbank **School** zurückzugeben.

    EXEC SP_help

Schließen Sie noch nicht die Portalverbindung zur Datenbank **School**. Sie werden sie in einigen Minuten erneut brauchen.

## Schritt 8: Erstellen einer Datenbankanmeldung und Zuweisen von Berechtigungen

In der SQL-Datenbank können Sie mit Transact-SQL Anmeldedaten erstellen und Berechtigungen zuweisen. In dieser Lektion führen Sie drei Vorgänge mit Transact-SQL aus:

1.  Erstellen eines Anmeldenamens für die SQL Server-Authentifizierung
2.  Erstellen eines Datenbankbenutzers
3.  Erteilen von Berechtigungen über eine Rollenmitgliedschaft

Für die Serververbindungen wird eine SQL Server-Authentifizierungsanmeldung verwendet. Alle Benutzer, die auf eine Datenbank auf dem SQL-Datenbankserver zugreifen, müssen dazu den SQL Server-Authentifizierungsanmeldenamen und das Kennwort angeben.

Um eine Anmeldung zu erstellen, stellen Sie zuerst eine Verbindung zur **master**-Datenbank her.

#### Erstellen eines Anmeldenamens für die SQL Server-Authentifizierung

1.  Wählen Sie im [Verwaltungsportal](http://manage.windowsazure.com) **SQL-Datenbanken** aus, klicken Sie auf **Server**, wählen Sie den Server aus, und klicken Sie dann auf den weißen Pfeil, um die
    Serverseite zu öffnen.

2.  Klicken Sie auf der Schnellstartseite auf **Manage Server**, um eine neue Verbindung zum Verwaltungsportal für die SQL-Datenbank zu öffnen.

3.  Geben Sie als Datenbank, mit der eine Verbindung hergestellt werden soll, **master** an, und melden Sie sich mit Ihrem Benutzernamen und Kennwort an. Dies ist die Adminstratoranmeldung, die Sie beim Erstellen des Servers angegeben haben.

4.  Das SQL-Datenbank-Verwaltungsportal wird in einem neuen Browserfenster geöffnet. Sie werden mit **master** verbunden.

5.  Falls folgender Fehler auf der Seite angezeigt wird, ignorieren Sie ihn. Klicken Sie auf **New Query**, um ein Abfragefenster zu öffnen, in dem Sie Transact-SQL-Befehle für die **master**-Datenbank ausführen können.

    ![Navigationsbereich][Image15]

6.  Kopieren Sie den folgenden Befehl, und fügen Sie ihn in das Abfragefenster ein.

        CREATE LOGIN SQLDBLogin WITH password='Password1';

7.  Führen Sie den Befehl aus, um den neuen SQL Server-Anmeldenamen "SQLDBLogin" zu erstellen.

#### Erstellen eines Datenbankbenutzers und Zuweisen von Berechtigungen

Nachdem Sie eine SQL Server-Authentifizierungsanmeldung erstellt haben, weisen Sie im nächsten Schritt die Datenbank und die Berechtigungen für den Anmeldenamen zu. Dazu erstellen Sie für jede Datenbank einen **Datenbankbenutzer**.

1.  Wechseln Sie zurück zur SQL-Datenbank-Verwaltungsportalseite für die Verbindung zur Datenbank **School**. Falls Sie das Browserfenster geschlossen haben, starten Sie eine neue Verbindung zur Datenbank **School**. Verwenden Sie dazu die Schritte aus der vorhergehenden Lektion, "Hinzufügen von Daten und einem Schema mit dem Transact-SQL-Skript".

    Auf der SQL-Datenbank-Verwaltungsportalseite wird der Datenbankname **School** in der oberen linken Ecke angezeigt.

    ![Navigationsbereich][Image12]

2.  Klicken Sie auf **New Query**, um ein neues Abfragefenster zu öffnen und die folgende Anweisung hineinzukopieren:

        CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3.  Führen Sie das Skript aus. Dieses Skript erstellt einen neuen Datenbankbenutzer basierend auf dem Anmeldenamen.

   Anschließend weisen Sie Berechtigungen für die Rolle "db\_datareader" zu. Datenbankbenutzer, die dieser Rolle zugewiesen werden, können Daten aus allen Benutzertabellen in der Datenbank lesen.

1.  Öffnen Sie ein neues Abfragefenster, und führen Sie die nächste Anweisung aus. Diese Anweisung führt eine integrierte gespeicherte Prozedur aus, die dem soeben erstellten neuen Benutzer die Rolle "db\_datareader" zuweist.

        EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

Sie haben nun eine neue SQL Server-Authentifizierungsanmeldung mit Leseberechtigung für die Datenbank **School**. Erstellen Sie anhand dieser Schritte weitere SQL Server-Authentifizierungsanmeldungen, um verschiedene Zugriffsmöglichkeiten auf die Daten zuzulassen.

## Schritt 9: Herstellen einer Verbindung von anderen Anwendungen

Nachdem Sie nun eine funktionierende Datenbank haben, können Sie Verbindungen von einer Excel-Arbeitsmappe herstellen.

#### Verbindungen von Excel

Falls Microsoft Excel auf Ihrem Computer installiert ist, können Sie die folgenden Schritte für eine Verbindung mit der Beispieldatenbank verwenden.

1.  Klicken Sie in Excel auf der Registerkarte "Daten" auf **Aus anderen Quellen** und anschließend auf **Von SQL Server**.

2.  Geben Sie im Datenverbindungs-Assistent den vollqualifizierten Domänennamen des SQL-Datenbankservers an, gefolgt von einem SQL Server-Authentifizierungsanmeldenamen mit Zugriffsberechtigung für die Datenbank.

   Der Servername befindet sich im Azure-Verwaltungsportal in der SQL-Datenbank auf der Serverseite im Dashboard unter **URL verwalten**. Der Servername besteht aus mehreren Buchstaben und Zahlen, gefolgt von ".database.windows.net". Geben Sie diesen Namen im Datenverbindungs-Assistent an. Schließen Sie nicht das Präfix http:// oder https:// in den Namen ein.

   Geben Sie eine SQL Server-Authentifizierungsanmeldung ein. Zu Testzwecken können Sie die Adminstratoranmeldung verwenden, die Sie beim Einrichten des Servers erstellt haben. Verwenden Sie für den regulären Datenzugriff eine Datenbank-Benutzeranmeldung ähnlich wie die soeben erstellte.

![Navigationsbereich][Image16]

1.  Wählen Sie auf der nächsten Seite die **School**-Datenbank und anschließend **Person** aus. Klicken Sie auf **Fertig stellen**. Wenn Ihre Anmeldeinformationen angefordert werden, geben Sie sie ein, und klicken Sie auf **OK**.

2.  Das Dialogfeld "Import Data" wird angezeigt, in dem Sie angeben, wo und wie Ihre Daten importiert werden sollen. Lassen Sie die Standardoptionen ausgewählt, und klicken Sie auf **OK**.

    ![Navigationsbereich][Image19]

3.  Im Arbeitsblatt sollte eine Tabelle mit einem Resultset angezeigt werden, das 34 Zeilen aus der Tabelle "person" enthält, einschließlich PersonID, LastName, FirstName, HireDate und EnrollmentDate, wie die Abfrageergebnisse aus Schritt 7

Wenn Sie nur Excel verwenden, können Sie jeweils nur eine Tabelle importieren. Eine bessere Methode ist das PowerPivot für Excel-Add-In, mit dem Sie mehrere Tabellen als einen Datensatz importieren und bearbeiten können. Die Arbeit mit PowerPivot geht über dieses Lernprogramm hinaus. Weitere Informationen dazu erhalten Sie in diesem Thema zu [PowerPivot für Excel](http://go.microsoft.com/fwlink/?LinkId=396969).

## Nächste Schritte

Nachdem Sie nun mit der SQL-Datenbank und den Verwaltungsportalen vertraut sind, können Sie andere Werkzeuge und Techniken ausprobieren, die von den SQL Server-Datenbankadministratoren verwendet werden.

Zur aktiven Verwaltung der neuen Datenbank wird die Installation von SQL Server Management Studio empfohlen. Management Studio ist das primäre Datenbank-Administrationstool zur Verwaltung von SQL-Serverdatenbanken, einschließlich auf Azure. Mithilfe von Management Studio können Sie Abfragen zur künftigen Wiederverwendung speichern, neue Tabellen und gespeicherte Prozeduren hinzufügen und Ihre Transact-SQL-Fertigkeiten in einer funktionsreichen Skriptumgebung mit Syntaxprüfung, IntelliSense und Vorlagen ausbauen. Befolgen Sie dazu die Anweisungen unter [Verwalten von SQL-Datenbanken mit SQL Server Management Studio](http://www.azure.microsoft.com/de-de/documentation/articles/sql-database-manage-azure-ssms/).

Kenntnisse der Transact-SQL-Abfrage- und Datendefinitionssprache sind für Datenbankadministratoren unumgänglich. Wenn Sie noch nicht mit Transact-SQL gearbeitet haben, beginnen Sie mit dem [Lernprogramm: Schreiben von Transact-SQL-Anweisungen](http://msdn.microsoft.com/de-de/library/ms365303.aspx), um sich einige grundlegende Kenntnisse anzueignen.

Es gibt noch andere Methoden zum Verschieben einer lokalen Datenbank zur SQL-Datenbank. Falls Sie eine vorhandene Datenbank haben oder Beispieldatenbanken zum Üben heruntergeladen haben, testen Sie folgende alternative Vorgehensweisen:

-   [Migration von Datenbanken zur SQL-Datenbank](http://msdn.microsoft.com/de-de/library/windowsazure/ee730904.aspx)
-   [Kopieren von Datenbanken in die SQL-Datenbank](http://msdn.microsoft.com/de-de/library/windowsazure/ff951624.aspx)
-   [Bereitstellen einer SQL Server-Datenbank für einen virtuellen Azure-Computer](http://msdn.microsoft.com/de-de/library/dn195938(v=sql.120).aspx)







[Image1]: ./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png
[Image2]: ./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png
[Image3]: ./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG
[Image4]: ./media/sql-database-get-started/4ServerSettings_SQLTut.PNG
[Image5]: ./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG
[Image6]: ./media/sql-database-get-started/6DBConfigFirewall_SQLTut.PNG
[Image7]: ./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png
[Image8]: ./media/sql-database-get-started/20MainPageHome_SQLTut.PNG
[Image9]: ./media/sql-database-get-started/9dblistschool_SQLTut.PNG
[Image10]: ./media/sql-database-get-started/10dbportalmanagebutton_SQLTut.PNG
[Image11]: ./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.PNG
[Image12]: ./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG
[Image13]: ./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG
[Image14]: ./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG
[Image15]: ./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG
[Image16]: ./media/sql-database-get-started/16ExcelConnect_SQLTut.png
[Image17]: ./media/sql-database-get-started/17ExcelSelect_SQLTut.PNG
[Image18]: ./media/sql-database-get-started/18ExcelTable_SQLTut.PNG
[Image19]: ./media/sql-database-get-started/19ExcelImport_SQLTut.png
[Image20]: ./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.PNG