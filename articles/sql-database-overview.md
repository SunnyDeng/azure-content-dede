

Vorgehensweise: Verwalten von SQL Database in Azure
===================================================

Dieser Leitfaden beschreibt, wie Sie administrative Aufgaben für logische Server und Datenbankinstanzen in Azure SQL Database durchführen können.

Was ist SQL Database?
---------------------

SQL Database bietet ein Verwaltungssystem für relationale Datenbanken in Azure und basiert auf der SQL Server-Technologie. Mit SQL Database können Sie Datenbankinstanzen bereitstellen und implementieren, und Sie profitieren von einem verteilten Datencenter, das Verfügbarkeit, Skalierbarkeit und Sicherheit für Unternehmen mit den Vorteilen von integriertem Datenschutz und Selbstreparatur bietet.

Inhaltsverzeichnis
------------------

-   [Anmelden bei Azure](#PreReq1)
-   [Konfigurieren von SQL Database](#PreReq2)
-   [Verbinden über Management Studio](#PreReq3)
-   [Bereitstellen einer Datenbank in Azure](#HowTo1)
-   [Hinzufügen von Anmeldungen und Benutzern](#HowTo2)
-   [Skalieren einer SQL Database-Lösung](#HowTo4)
-   [Überwachen von logischen Servern und Datenbankinstanzen](#HowTo3)
-   [Nächste Schritte](#NextSteps)

Anmelden bei Azure
------------------

SQL Database bietet relationalen Datenspeicher sowie Zugriff und Verwaltungsdienste in Azure. Sie benötigen ein Azure-Abonnement, um SQL Database zu verwenden.

1.  Öffnen Sie einen Webbrowser, und navigieren Sie zu <http://www.windowsazure.com>. Um ein kostenloses Konto zu erstellen, klicken Sie auf die kostenlose Testversion oben rechts, und folgen Sie den Anweisungen.

2.  Ihr Konto wurde erstellt. Sie können jetzt loslegen.

Erstellen und konfigurieren von SQL Database.
---------------------------------------------

Es folgt die Erstellung und Konfiguration logischer Server. Im neuen Azure-Verwaltungsportal (Vorschau) können Sie mit überarbeiteten Workflows eine Datenbank und anschließend einen Server erstellen.

In diesem Thema erstellen Sie zunächst den Server. Dieser Ansatz ist besser geeignet, wenn Sie bereits SQL Server-Datenbanken haben, die Sie hochladen möchten.

### Erstellen eines logischen Servers

1.  Melden Sie sich bei <http://www.windowsazure.com> an.

2.  Klicken Sie auf **SQL Database** und anschließend auf **SERVERS** in der SQL Database-Startseite.

3.  Klicken Sie unten auf der Seite auf **Add**.

4.  Geben Sie in den Servereinstellungen einen Administratornamen (in einem Wort ohne Leerzeichen) ein.

    SQL Database verwendet SQL-Authentifizierung über eine verschlüsselte Verbindung. Es wird ein neuer SQL Server-Authentifizierungsanmeldename mit dem von Ihnen angegebenen Namen erstellt, welcher der festen Serverrolle sysadmin zugewiesen ist.

    Der Anmeldename darf keine E-Mail-Adresse, kein Windows-Benutzerkonto und auch keine Windows Live ID sein. Ansprüche oder Windows-Authentifizierung wird in SQL Database nicht unterstützt.

5.  Geben Sie ein sicheres Kennwort mit mindestens acht Zeichen an, das eine Kombination aus Groß- und Kleinbuchstaben und Zahlen oder Symbolen enthält.

6.  Wählen Sie eine Region aus. Die Region bestimmt den geografischen Standort des Servers. Die Region kann nicht einfach geändert werden, wählen Sie also einen sinnvollen Standort für den Server aus. Wählen Sie den nächstgelegenen Standort aus. Wenn Sie Ihre Azure-Anwendung und -Datenbank in derselben Region platzieren, sparen Sie Zugangskosten für Bandbreite und Datenlatenz.

7.  Lassen Sie **Allow Services** aktiviert, damit Sie über das Verwaltungsportal für SQL Database, Speicherdienste und andere Dienste in Azure eine Verbindung zur Datenbank herstellen können.

8.  Klicken Sie abschließend auf das Häkchen unten auf der Seite.

Beachten Sie, dass Sie keinen Servernamen angegeben haben. SQL Database generiert automatisch einen Servernamen, um doppelte DNS-Einträge zu verhindern. Der Servername ist eine alphanumerische Zeichenfolge aus zehn Zeichen. Sie können den Namen Ihres SQL Database-Servers nicht ändern.

Im nächsten Schritt konfigurieren Sie die Firewall, sodass Verbindungen von Anwendungen, die auf Ihrem Netzwerk ausgeführt werden, Zugriff erhalten.

### Konfigurieren der Firewall für den logischen Server

1.  Klicken Sie auf **SQL Database**, dann auf **Servers** und anschließend auf den gerade erstellten Server.

2.  Klicken Sie auf **Configure**.

3.  Kopieren Sie die aktuelle Client-IP-Adresse. Wenn Sie eine Verbindung von einem Netzwerk aus herstellen, ist dies die IP-Adresse, die Ihr Router oder Proxyserver abhört. SQL Database erkennt die IP-Adresse, die von der aktuellen Verbindung verwendet wird, sodass Sie eine Firewall-Regel zur Genehmigung der Verbindungsanforderungen von diesem Gerät erstellen können.

4.  Fügen Sie die IP-Adresse in Bereichsanfang und -ende ein. Wenn später Verbindungsfehler auftreten, die angeben, dass der Bereich zu eng ist, können Sie diese Regel bearbeiten und den Bereich erweitern.

    Wenn Clientcomputer dynamisch zugewiesene IP-Adressen verwenden, müssen Sie einen Bereich angeben, der breit genug für die IP-Adressen ist, die den Computern im Netzwerk zugewiesen wurden. Beginnen Sie mit einem schmalen Bereich, und erweitern Sie diesen nur im Bedarfsfall.

5.  Geben Sie einen Namen für die Firewall-Regel ein, z. B. den Namen Ihres Computers oder der Firma.

6.  Klicken Sie auf das Häkchen, um die Regel zu speichern.

7.  Klicken Sie unten auf der Seite auf **Save**, um den Schritt abzuschließen. Falls **Save** nicht angezeigt wird, aktualisieren Sie die Browserseite.

Sie haben jetzt einen logischen Server, eine Firewall-Regel, die eingehende Verbindungen von Ihrer IP-Adresse erlaubt, sowie einen Administratoranmeldenamen. Im nächsten Schritt wechseln Sie zum lokalen Computer, um die verbleibenden Konfigurationsschritte abzuschließen.

**Hinweis:** Der logische Server, den Sie gerade erstellt haben, ist vorübergehend, und wird dynamisch auf physikalischen Servern in einem Datencenter gehostet. Wenn Sie den Server löschen, kann dies nicht mehr rückgängig gemacht werden. Sichern Sie alle Datenbanken, die Sie nachträglich zum Server hochladen.

Verbinden über Management Studio
--------------------------------

Management Studio ist ein Administrations-Tool, mit dem Sie mehrere SQL Server-Instanzen und Server in einer einzigen Arbeitsumgebung verwalten können. Falls Sie bereits eine lokale SQL Server-Instanz haben, können Sie sich sowohl mit der lokalen Instanz als auch mit dem logischen Server in Azure verbinden und Aufgaben nebeneinander erledigen.

Management Studio enthält Features, die momentan im Verwaltungsportal nicht verfügbar sind, wie z. B. Syntaxprüfung und die Möglichkeit, Skripts und benannte Abfragen zu speichern und wiederzuverwenden. SQL Database ist ein einfacher Tabular Data Stream (TDS)-Endpunkt. Alle mit TDS kompatiblen Tools inklusive Management Studio können Operationen in SQL Database ausführen. Ihre für den lokalen Server entwickelten Skripts funktionieren auch auf einem logischen SQL Database-Server.

Im nächsten Schritt werden Sie Management Studio verwenden, um sich mit einem logischen Server in Azure zu verbinden. Für diesen Schritt brauchen Sie SQL Server Management Studio Version 2008 R2 oder 2012. Hilfe zum Download oder Verbindungen mit Management Studio finden Sie unter [Verwalten von SQL Database mit Management Studio](http://www.windowsazure.com/de-de/develop/net/common-tasks/sql-azure-management/) auf dieser Website.

Bevor Sie sich verbinden können, müssen Sie unter Umständen eine Firewall-Ausnahme erstellen, die ausgehende Anfragen auf Port 1433 für Ihr lokales System erlaubt. Auf sicheren Computern ist der Port 1433 standardmäßig nicht geöffnet.

### Konfigurieren der Firewall für einen lokalen Server

1.  Erstellen Sie eine neue ausgehende Regel in der Windows-Firewall mit erweiterter Sicherheit.

2.  Klicken Sie auf **Port**, geben Sie TCP 1433 und **Allow the connection** an und vergewissern Sie sich, dass Sie das Profil **Public** ausgewählt haben.

3.  Geben Sie einen aussagekräftigen Namen ein, z. B. *WindowsAzureSQLDatabase (tcp-out) Port 1433*.

### Verbinden mit einem logischen Server

1.  Vergewissern Sie sich in Management Studio unter Connect to Server, dass Database Engine ausgewählt ist, und geben Sie den Namen des logischen Servers im folgenden Format ein: *servername*.database.widnows.net

    Sie können den vollqualifizierten Servernamen im Verwaltungsportal im Server-Dashboard unter MANAGE URL abrufen.

2.  Wählen Sie **SQL Server Authentication** unter Authentication und geben Sie die Administrator-Anmeldedaten ein, die Sie beim Konfigurieren des logischen Servers erstellt haben.

3.  Klicken Sie auf **Options**.

4.  Geben Sie unter Connect to database den Wert **master** ein.

### Verbinden mit einem lokalen Server

1.  Vergewissern Sie sich in Management Studio unter Connect to Server, dass Database Engine ausgewählt ist, und geben Sie den Namen der lokalen Instanz im folgenden Format ein: *servername*\\*instancename*. Für eine Standardinstanz auf dem lokalen Server können Sie *localhost* eingeben.

2.  Wählen Sie **Windows Authentication** unter Authentication aus und geben Sie ein Windows-Konto ein, das Mitglied der sysadmin-Rolle ist.

Bereitstellen einer Datenbank in Azure
--------------------------------------

Es gibt zahlreiche Ansätze, um eine lokale SQL Server-Datenbank nach Azure zu verschieben. In dieser Aufgabe verwenden Sie den Assistenten zum Bereitstellen einer Datenbank für SQL Database, um eine Beispieldatenbank hochzuladen.

Die Schul-Beispieldatenbank ist dank ihrer Einfachheit gut geeignet; alle Objekte sind mit SQL Database kompatibel, sodass Sie keine Datenbank für die Migration ändern oder vorbereiten müssen. Stellen Sie als neuer Administrator zuerst eine einfache Datenbank bereit, um die einzelnen Schritte zu verstehen, bevor Sie Ihre eigenen Datenbanken verwenden.

**Hinweis:** Im Migrationshandbuch für SQL Database finden Sie detaillierte Anweisungen zum Vorbereiten einer lokalen Datenbank für die Migration auf Azure. Laden Sie gegebenenfalls auch das Azure-Trainingskit herunter. Es enthält eine Übung, in der Sie einen alternativen Ansatz für die Migration einer lokalen Datenbank kennenlernen.

### Erstellen der Schuldatenbank auf einem lokalen Server

Skripte für die Erstellung dieser Datenbank finden Sie unter [Erste Schritte mit der Administration von SQL Database](http://www.windowsazure.com/de-de/manage/tutorials/sql-azure-management/). In dieser Anleitung führen Sie diese Skripte in Management Studio aus, um eine lokale Version der Schuldatenbank zu erstellen.

1.  Stellen Sie in Management Studio eine Verbindung zu einem lokalen Server her. Klicken Sie mit der rechten Maustaste auf **Databases**, klicken Sie anschließend auf **New Database** und geben Sie *school* ein.

2.  Klicken Sie mit der rechten Maustaste auf *school* und klicken Sie auf **New Query**.

3.  Kopieren Sie das Skript "Create Schema" aus dem Lernprogramm, und führen Sie es aus.

``` {}
    -- Create the Department table.
    IF NOT EXISTS (SELECT * FROM sys.objects ?      WHERE object_id = OBJECT_ID(N'[dbo].[Department]') ?        AND type in (N'U'))?    BEGIN?    CREATE TABLE [dbo].[Department](?     [DepartmentID] [int] NOT NULL,?     [Name] [nvarchar](50) NOT NULL,?        [Budget] [money] NOT NULL,?     [StartDate] [datetime] NOT NULL,?       [Administrator] [int] NULL,?     CONSTRAINT [PK_Department] PRIMARY KEY CLUSTERED ?    (?   [DepartmentID] ASC?    )WITH (IGNORE_DUP_KEY = OFF)?    )?    END;? GO??    -- Erstellt die Tabelle Person.
    IF NOT EXISTS (SELECT * FROM sys.objects ?      WHERE object_id = OBJECT_ID(N'[dbo].[Person]') ?        AND type in (N'U'))?    BEGIN?  CREATE TABLE [dbo].[Person](?       [PersonID] [int] IDENTITY(1,1) NOT NULL,?       [LastName] [nvarchar](50) NOT NULL,?        [FirstName] [nvarchar](50) NOT NULL,?       [HireDate] [datetime] NULL,?        [EnrollmentDate] [datetime] NULL,?   CONSTRAINT [PK_School.Student] PRIMARY KEY CLUSTERED   ?   (?  [PersonID] ASC? )WITH (IGNORE_DUP_KEY = OFF)?   ) ? END;?   GO??    -- Erstellt die Tabelle OnsiteCourse.
    IF NOT EXISTS (SELECT * FROM sys.objects ?      WHERE object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]') ?      AND type in (N'U'))?    BEGIN?  CREATE TABLE [dbo].[OnsiteCourse](?     [CourseID] [int] NOT NULL,?     [Location] [nvarchar](50) NOT NULL,?        [Days] [nvarchar](50) NOT NULL,?        [Time] [smalldatetime] NOT NULL,?    CONSTRAINT [PK_OnsiteCourse] PRIMARY KEY CLUSTERED ?   (?      [CourseID] ASC? )WITH (IGNORE_DUP_KEY = OFF)?   ) ? END;?   GO??    -- Erstellt die Tabelle OnlineCourse.
    IF NOT EXISTS (SELECT * FROM sys.objects ?      WHERE object_id = OBJECT_ID(N'[dbo].[OnlineCourse]') ?      AND type in (N'U'))?    BEGIN?  CREATE TABLE [dbo].[OnlineCourse](?     [CourseID] [int] NOT NULL,?     [URL] [nvarchar](100) NOT NULL,?     CONSTRAINT [PK_OnlineCourse] PRIMARY KEY CLUSTERED ?   (?      [CourseID] ASC? )WITH (IGNORE_DUP_KEY = OFF)?   ) ? END;?   GO??    --Erstellt die Tabelle StudentGrade.
    IF NOT EXISTS (SELECT * FROM sys.objects ?      WHERE object_id = OBJECT_ID(N'[dbo].[StudentGrade]') ?      AND type in (N'U'))?    BEGIN?  CREATE TABLE [dbo].[StudentGrade](?     [EnrollmentID] [int] IDENTITY(1,1) NOT NULL,?       [CourseID] [int] NOT NULL,?     [StudentID] [int] NOT NULL,?        [Grade] [decimal](3, 2) NULL,?   CONSTRAINT [PK_StudentGrade] PRIMARY KEY CLUSTERED ?   (?      [EnrollmentID] ASC? )WITH (IGNORE_DUP_KEY = OFF)?   ) ? END;?   GO??    -- Erstellt die Tabelle CourseInstructor.
    IF NOT EXISTS (SELECT * FROM sys.objects ?      WHERE object_id = OBJECT_ID(N'[dbo].[CourseInstructor]') ?      AND type in (N'U'))?    BEGIN?  CREATE TABLE [dbo].[CourseInstructor](?     [CourseID] [int] NOT NULL,?     [PersonID] [int] NOT NULL,?  CONSTRAINT [PK_CourseInstructor] PRIMARY KEY CLUSTERED ?   (?      [CourseID] ASC,?        [PersonID] ASC? )WITH (IGNORE_DUP_KEY = OFF)?   ) ? END;?   GO??    -- Erstellt die Tabelle Course.
    IF NOT EXISTS (SELECT * FROM sys.objects ?      WHERE object_id = OBJECT_ID(N'[dbo].[Course]') ?        AND type in (N'U'))?    BEGIN?  CREATE TABLE [dbo].[Course](?       [CourseID] [int] NOT NULL,?     [Title] [nvarchar](100) NOT NULL,?      [Credits] [int] NOT NULL,?      [DepartmentID] [int] NOT NULL,?      CONSTRAINT [PK_School.Course] PRIMARY KEY CLUSTERED ?  (?      [CourseID] ASC? )WITH (IGNORE_DUP_KEY = OFF)?   )?  END;?   GO??    -- Erstellt die Tabelle OfficeAssignment.
    IF NOT EXISTS (SELECT * FROM sys.objects ?      WHERE object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]')?       AND type in (N'U'))?    BEGIN?  CREATE TABLE [dbo].[OfficeAssignment](?     [InstructorID] [int] NOT NULL,?     [Location] [nvarchar](50) NOT NULL,?        [Timestamp] [timestamp] NOT NULL,?   CONSTRAINT [PK_OfficeAssignment] PRIMARY KEY CLUSTERED ?   (?      [InstructorID] ASC? )WITH (IGNORE_DUP_KEY = OFF)?   )?  END;?   GO??    -- Definiert die Beziehung zwischen OnsiteCourse und Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys ?       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnsiteCourse_Course]')?       AND parent_object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]'))? ALTER TABLE [dbo].[OnsiteCourse]  WITH CHECK ADD  ?       CONSTRAINT [FK_OnsiteCourse_Course] FOREIGN KEY([CourseID])?  REFERENCES [dbo].[Course] ([CourseID]);?    GO? ALTER TABLE [dbo].[OnsiteCourse] CHECK ?       CONSTRAINT [FK_OnsiteCourse_Course];?    GO??    -- Definiert die Beziehung zwischen OnlineCourse und Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys ?       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnlineCourse_Course]')?       AND parent_object_id = OBJECT_ID(N'[dbo].[OnlineCourse]'))? ALTER TABLE [dbo].[OnlineCourse]  WITH CHECK ADD  ?       CONSTRAINT [FK_OnlineCourse_Course] FOREIGN KEY([CourseID])?  REFERENCES [dbo].[Course] ([CourseID]);?    GO? ALTER TABLE [dbo].[OnlineCourse] CHECK ?       CONSTRAINT [FK_OnlineCourse_Course];?    GO? -- Definiert die Beziehung zwischen StudentGrade und Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys ?       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Course]')?       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))? ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  ?       CONSTRAINT [FK_StudentGrade_Course] FOREIGN KEY([CourseID])?  REFERENCES [dbo].[Course] ([CourseID]);?    GO? ALTER TABLE [dbo].[StudentGrade] CHECK ?       CONSTRAINT [FK_StudentGrade_Course];?    GO??    --Definiert die Beziehung zwischen StudentGrade und Student.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys ?       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Student]')?       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]')) ?   ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  ?       CONSTRAINT [FK_StudentGrade_Student] FOREIGN KEY([StudentID])?    REFERENCES [dbo].[Person] ([PersonID]);?    GO? ALTER TABLE [dbo].[StudentGrade] CHECK ?       CONSTRAINT [FK_StudentGrade_Student];?   GO??    -- Definiert die Beziehung zwischen CourseInstructor und Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys ?      WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Course]')?     AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))?    ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  ?      CONSTRAINT [FK_CourseInstructor_Course] FOREIGN KEY([CourseID])?   REFERENCES [dbo].[Course] ([CourseID]);?    GO? ALTER TABLE [dbo].[CourseInstructor] CHECK ?      CONSTRAINT [FK_CourseInstructor_Course];? GO??    -- Definiert die Beziehung zwischen CourseInstructor und Person.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys ?       WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Person]')?      AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))?  ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  ?       CONSTRAINT [FK_CourseInstructor_Person] FOREIGN KEY([PersonID])?  REFERENCES [dbo].[Person] ([PersonID]);?    GO? ALTER TABLE [dbo].[CourseInstructor] CHECK ?     CONSTRAINT [FK_CourseInstructor_Person];?  GO??    -- Definiert die Beziehung zwischen Course und Department.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys ?       WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')?       AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))? ALTER TABLE [dbo].[Course]  WITH CHECK ADD  ?       CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])?  REFERENCES [dbo].[Department] ([DepartmentID]);?    GO? ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];?    GO??    --Definiert die Beziehung zwischen OfficeAssignment und Person.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys ?   WHERE object_id = OBJECT_ID(N'[dbo].[FK_OfficeAssignment_Person]')?     AND parent_object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]'))?   ALTER TABLE [dbo].[OfficeAssignment]  WITH CHECK ADD  ?       CONSTRAINT [FK_OfficeAssignment_Person] FOREIGN KEY([InstructorID])?  REFERENCES [dbo].[Person] ([PersonID]);?    GO? ALTER TABLE [dbo].[OfficeAssignment] CHECK ?     CONSTRAINT [FK_OfficeAssignment_Person];?  GO
```

Kopieren Sie dann das Skript "Insert Data", und führen Sie es aus.

``` {}
    -- Insert data into the Person table.
    SET IDENTITY_INSERT dbo.Person ON;? GO? INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (1, 'Abercrombie', 'Kim', '1995-03-11', null);?  INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (2, 'Barzdukas', 'Gytis', null, '2005-09-01');?  INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (3, 'Justice', 'Peggy', null, '2001-09-01');?    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (4, 'Fakhouri', 'Fadi', '2002-08-06', null);?    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (5, 'Harui', 'Roger', '1998-07-01', null);?  INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (6, 'Li', 'Yan', null, '2002-09-01');?   INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (7, 'Norman', 'Laura', null, '2003-09-01');? INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (8, 'Olivotto', 'Nino', null, '2005-09-01');?    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (9, 'Tang', 'Wayne', null, '2005-09-01');?   INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (10, 'Alonso', 'Meredith', null, '2002-09-01');? INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (11, 'Lopez', 'Sophia', null, '2004-09-01');?    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (12, 'Browning', 'Meredith', null, '2000-09-01');?   INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (13, 'Anand', 'Arturo', null, '2003-09-01');?    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (14, 'Walker', 'Alexandra', null, '2000-09-01');?    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (15, 'Powell', 'Carson', null, '2004-09-01');?   INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (16, 'Jai', 'Damien', null, '2001-09-01');?  INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (17, 'Carlson', 'Robyn', null, '2005-09-01');?   INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (18, 'Zheng', 'Roger', '2004-02-12', null);? INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (19, 'Bryant', 'Carson', null, '2001-09-01');?   INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (20, 'Suarez', 'Robyn', null, '2004-09-01');?    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (21, 'Holt', 'Roger', null, '2004-09-01');?  INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (22, 'Alexander', 'Carson', null, '2005-09-01');?    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (23, 'Morgan', 'Isaiah', null, '2001-09-01');?   INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (24, 'Martin', 'Randall', null, '2005-09-01');?  INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (25, 'Kapoor', 'Candace', '2001-01-15', null);?  INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (26, 'Rogers', 'Cody', null, '2002-09-01');? INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (27, 'Serrano', 'Stacy', '1999-06-01', null);?   INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (28, 'White', 'Anthony', null, '2001-09-01');?   INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (29, 'Griffin', 'Rachel', null, '2004-09-01');?  INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (30, 'Shan', 'Alicia', null, '2003-09-01');? INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (31, 'Stewart', 'Jasmine', '1997-10-12', null);? INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (32, 'Xu', 'Kristen', '2001-7-23', null);?   INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (33, 'Gao', 'Erica', null, '2003-01-30');?   INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)?   VALUES (34, 'Van Houten', 'Roger', '2000-12-07', null);?    GO? SET IDENTITY_INSERT dbo.Person OFF;?    GO? -- Einfügen von Daten in die Tabelle Department.
    INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)?    VALUES (1, 'Engineering', 350000.00, '2007-09-01', 2);? INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)?    VALUES (2, 'English', 120000.00, '2007-09-01', 6);? INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)?    VALUES (4, 'Economics', 200000.00, '2007-09-01', 4);?   INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)?    VALUES (7, 'Mathematics', 250000.00, '2007-09-01', 3);? GO? -- Einfügen von Daten in die Tabelle Course.
    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)?    VALUES (1050, 'Chemistry', 4, 1);?  INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)?    VALUES (1061, 'Physics', 4, 1);?    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)?    VALUES (1045, 'Calculus', 4, 7);?   INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)?    VALUES (2030, 'Poetry', 2, 2);? INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)?    VALUES (2021, 'Composition', 3, 2);?    INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)?    VALUES (2042, 'Literature', 4, 2);? INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)?    VALUES (4022, 'Microeconomics', 3, 4);? INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)?    VALUES (4041, 'Macroeconomics', 3, 4);? INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)?    VALUES (4061, 'Quantitative', 2, 4);?   INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)?    VALUES (3141, 'Trigonometry', 4, 7);?   GO? -- Einfügen von Daten in die Tabelle OnlineCourse.
    INSERT INTO dbo.OnlineCourse (CourseID, URL)?   VALUES (2030, 'http://www.fineartschool.net/Poetry');?  INSERT INTO dbo.OnlineCourse (CourseID, URL)?   VALUES (2021, 'http://www.fineartschool.net/Composition');? INSERT INTO dbo.OnlineCourse (CourseID, URL)?   VALUES (4041, 'http://www.fineartschool.net/Macroeconomics');?  INSERT INTO dbo.OnlineCourse (CourseID, URL)?   VALUES (3141, 'http://www.fineartschool.net/Trigonometry');?    --Einfügen von Daten in die Tabelle OnsiteCourse.
    INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])?    VALUES (1050, '123 Smith', 'MTWH', '11:30');?   INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])?    VALUES (1061, '234 Smith', 'TWHF', '13:15');?   INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])?    VALUES (1045, '121 Smith','MWHF', '15:30');?    INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])?    VALUES (4061, '22 Williams', 'TH', '11:15');?   INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])?    VALUES (2042, '225 Adams', 'MTWH', '11:00');?   INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])?    VALUES (4022, '23 Williams', 'MWF', '9:00');?   -- Einfügen von Daten in die Tabelle CourseInstructor.
    INSERT INTO dbo.CourseInstructor(CourseID, PersonID)?   VALUES (1050, 1);?  INSERT INTO dbo.CourseInstructor(CourseID, PersonID)?   VALUES (1061, 31);? INSERT INTO dbo.CourseInstructor(CourseID, PersonID)?   VALUES (1045, 5);?  INSERT INTO dbo.CourseInstructor(CourseID, PersonID)?   VALUES (2030, 4);?  INSERT INTO dbo.CourseInstructor(CourseID, PersonID)?   VALUES (2021, 27);? INSERT INTO dbo.CourseInstructor(CourseID, PersonID)?   VALUES (2042, 25);? INSERT INTO dbo.CourseInstructor(CourseID, PersonID)?   VALUES (4022, 18);? INSERT INTO dbo.CourseInstructor(CourseID, PersonID)?   VALUES (4041, 32);? INSERT INTO dbo.CourseInstructor(CourseID, PersonID)?   VALUES (4061, 34);? GO? --Einfügen von Daten in die Tabelle OfficeAssignment.
    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)?   VALUES (1, '17 Smith');?    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)?   VALUES (4, '29 Adams');?    INSERT INTO dbo.OfficeAssignment(InstructorID, Location)?   VALUES (5, '37 Williams');? INSERT INTO dbo.OfficeAssignment(InstructorID, Location)?   VALUES (18, '143 Smith');?  INSERT INTO dbo.OfficeAssignment(InstructorID, Location)?   VALUES (25, '57 Adams');?   INSERT INTO dbo.OfficeAssignment(InstructorID, Location)?   VALUES (27, '271 Williams');?   INSERT INTO dbo.OfficeAssignment(InstructorID, Location)?   VALUES (31, '131 Smith');?  INSERT INTO dbo.OfficeAssignment(InstructorID, Location)?   VALUES (32, '203 Williams');?   INSERT INTO dbo.OfficeAssignment(InstructorID, Location)?   VALUES (34, '213 Smith');?  -- Einfügen von Daten in die Tabelle StudentGrade.
    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (2021, 2, 4);?   INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (2030, 2, 3.5);? INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (2021, 3, 3);?   INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (2030, 3, 4);?   INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (2021, 6, 2.5);? INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (2042, 6, 3.5);? INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (2021, 7, 3.5);? INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (2042, 7, 4);?   INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (2021, 8, 3);?   INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (2042, 8, 3);?   INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4041, 9, 3.5);? INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4041, 10, null);?   INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4041, 11, 2.5);?    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4041, 12, null);?   INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4061, 12, null);?   INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4022, 14, 3);?  INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4022, 13, 4);?  INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4061, 13, 4);?  INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4041, 14, 3);?  INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4022, 15, 2.5);?    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4022, 16, 2);?  INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4022, 17, null);?   INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4022, 19, 3.5);?    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4061, 20, 4);?  INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4061, 21, 2);?  INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4022, 22, 3);?  INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4041, 22, 3.5);?    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4061, 22, 2.5);?    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (4022, 23, 3);?  INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (1045, 23, 1.5);?    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (1061, 24, 4);?  INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (1061, 25, 3);?  INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (1050, 26, 3.5);?    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (1061, 26, 3);?  INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (1061, 27, 3);?  INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (1045, 28, 2.5);?    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (1050, 28, 3.5);?    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (1061, 29, 4);?  INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (1050, 30, 3.5);?    INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)?  VALUES (1061, 30, 4);?  GO
```

Jetzt haben Sie eine lokale Datenbank, die Sie nach Azure exportieren können. Führen Sie als Nächstes einen Assistenten aus, mit dem eine BACPAC-Datei erstellt wird, die dann in Azure geladen und in SQL Database importiert wird.

### Bereitstellen in SQL Database

1.  Stellen Sie in Management Studio eine Verbindung zu einer lokalen SQL Server-Instanz her, die über eine Datenbank verfügt, die Sie migrieren möchten.

2.  Klicken Sie mit der rechten Maustaste auf die Schuldatenbank, die Sie gerade erstellt haben, zeigen Sie auf **Tasks**, und klicken Sie auf **Deploy Database to SQL Database**.

3.  Geben Sie unter Deployment Settings einen Namen für die Datenbank ein, z. B. *Schule*.

4.  Klicken Sie auf **Connect**.

5.  Geben Sie unter Server Name einen Servernamen bestehend aus 10 Zeichen ein, gefolgt von ".database.windows.net".

6.  Wählen Sie unter Authentication die Option **SQL Server Authentication** aus.

7.  Geben Sie den Anmeldenamen und das Kennwort des Administrators ein, die Sie beim Erstellen des logischen SQL Database-Servers angegeben haben.

8.  Klicken Sie auf **Options**.

9.  Geben Sie in Connection Properties unter Connect to database den Wert **master** ein.

10. Klicken Sie auf **Connect**. Mit diesem Schritt schließen Sie die Verbindungsspezifikation ab und kehren zurück zum Assistenten.

11. Klicken Sie auf **Next** und dann auf **Finish**, um den Assistenten auszuführen.

### Überprüfen der Datenbankbereitstellung

1.  Verbinden Sie sich in Management Studio mit dem logischen Server. Falls Sie bereits eine Verbindung geöffnet haben, können Sie diese schließen und eine neue Verbindung öffnen. Die existierende Verbindung zeigt nur diejenigen Datenbanken an, die beim Verbindungsaufbau aktiv waren.

    Hinweise zum Verbinden mit einem logischen Server finden Sie unter [Verbinden über Management Studio](#PreReq3) in diesem Dokument.

2.  Erweitern Sie den Ordner "Datenbanken". In der Liste sollte die Schuldatenbank aufgeführt werden.

3.  Klicken Sie mit der rechten Maustaste auf die Schuldatenbank, und klicken Sie dann auf **New Query**.

4.  Führen Sie die folgende Abfrage aus, um den Zugriff auf die Daten zu überprüfen.

``` {}
    SELECT?     Course.Title as "Course Title"?       ,Department.Name as "Department"?         ,Person.LastName as "Instructor"?         ,OnsiteCourse.Location as "Location"?         ,OnsiteCourse.Days as "Days"?         ,OnsiteCourse.Time as "Time"? FROM?    Course?     INNER JOIN Department?       ON Course.DepartmentID = Department.DepartmentID?      INNER JOIN CourseInstructor?      ON Course.CourseID = CourseInstructor.CourseID?   INNER JOIN Person?        ON CourseInstructor.PersonID = Person.PersonID?   INNER JOIN OnsiteCourse?       ON OnsiteCourse.CourseID = CourseInstructor.CourseID;
```

Hinzufügen von Anmeldungen und Benutzern
----------------------------------------

Nachdem Sie eine Datenbank bereitgestellt haben, müssen Sie Anmeldungen konfigurieren und Berechtigungen zuweisen. In diesem Schritt führen Sie zwei Skripte aus.

Zunächst verbinden Sie sich mit einem Master und führen ein Skript zur Erstellung von Anmeldungen aus. Die Anmeldungen werden für Lese- und Schreiboperationen und zum Delegieren von Betriebsaufgaben verwendet, wie z. B. der Möglichkeit zum Ausführen von Systemabfragen ohne Berechtigungen.

Sie müssen Anmeldenamen mit SQL Server-Authentifizierung erstellen. Falls Sie bereits vorgefertigte Skripts haben, die Windows-Benutzeridentitäten oder Anspruchsidentitäten verwenden, werden diese Skripts unter SQL Database nicht funktionieren.

Das zweite Skript vergibt Berechtigungen an Datenbankbenutzer. Für dieses Skript verbinden Sie sich mit einer bereits in Azure geladenen Datenbank.

### Anmeldungen erstellen

1.  Verbinden Sie sich in Management Studio mit einem logischen Server in Azure, erweitern Sie den Ordner Databases, klicken Sie mit der rechten Maustaste auf **master** und wählen Sie **New Query** aus.

2.  Verwenden Sie die folgenden Transact-SQL-Anweisungen zur Erstellung von Anmeldungen. Ersetzen Sie das Kennwort durch ein gültiges Kennwort. Wählen Sie die Anweisungen einzeln aus und klicken Sie auf **Execute**. Wiederholen Sie den Vorgang für die verbleibenden Anmeldungen.

``` {}
    -- Auf Master ausführen, jede Zeile einzeln ausführen?    -- Verwenden Sie diese Anmeldung zur Verwaltung anderer Anmeldungen auf diesem Server?    CREATE LOGIN sqladmin WITH password='<ProvidePassword>'; ?    CREATE USER sqladmin FROM LOGIN sqladmin;?    EXEC sp_addrolemember 'loginmanager', 'sqladmin';??    -- Verwenden Sie diese Anmeldung zum Erstellen und Kopieren von Datenbanken?    CREATE LOGIN sqlops WITH password='<ProvidePassword>';?    CREATE USER sqlops FROM LOGIN sqlops;?    EXEC sp_addrolemember 'dbmanager', 'sqlops';
```

### Erstellen von Datenbankbenutzern

1.  Erweitern Sie den Ordner Databases, klicken Sie mit der rechten Maustaste auf **school** und wählen Sie **New Query** aus.

2.  Verwenden Sie die folgenden Transact-SQL-Anweisungen, um Datenbankbenutzer hinzuzufügen. Ersetzen Sie das Kennwort durch ein gültiges Kennwort.

``` {}
    -- Auf normaler Datenbank ausführen, jede Zeile einzeln ausführen?    -- Verwenden Sie diese Anmeldung für Leseoperationen?      CREATE LOGIN sqlreader WITH password='<ProvidePassword>';?    CREATE USER sqlreader FROM LOGIN sqlreader;?    EXEC sp_addrolemember 'db_datareader', 'sqlreader';??    -- Verwenden Sie diese Anmeldung für Schreiboperationen?    CREATE LOGIN sqlwriter WITH password='<ProvidePassword>';?    CREATE USER sqlwriter FROM LOGIN sqlwriter;?    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';??    -- DMV-Berechtigungen für die Datenbank school an 'sqlops' vergeben?    GRANT VIEW DATABASE STATE to 'sqlops';
```

### Anmeldungen anzeigen und testen

1.  Verbinden Sie sich in einem neuen Abfragefenster mit **master** und führen Sie die folgende Anweisung aus:

         SELECT * from sys.sql_logins;

2.  Klicken Sie in Management Studio mit der rechten Maustaste auf die Datenbank **school** und wählen Sie **New Query** aus.

3.  Zeigen Sie im Menü Query auf **Connection** und klicken Sie auf **Change Connection**.

4.  Melden Sie sich als *sqlreader* an.

5.  Kopieren Sie die folgende Anweisung und versuchen Sie, diese auszuführen. Sie sollten einen Fehler und eine Meldung erhalten, dass das Objekt nicht existiert.

         INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
         VALUES (1061, 30, 9);

6.  Öffnen Sie ein zweites Abfragefenster und ändern Sie den Verbindungskontext zu *sqlwriter*. Die gleiche Abfrage sollte sich nun ohne Fehler ausführen lassen.

Sie haben nun mehrere Anmeldungen erstellt und getestet. Weitere Informationen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in SQL Database](http://msdn.microsoft.com/de-de/library/windowsazure/ee336235.aspx) und [Überwachen von Azure SQL Database mit dynamischen Verwaltungssichten](http://msdn.microsoft.com/de-de/library/windowsazure/ff394114.aspx).

Überwachen von logischen Servern und Datenbankinstanzen
-------------------------------------------------------

Ihre gewohnten Überwachungs-Tools und Techniken, die Sie für lokale Server verwenden, wie z. B. Auditing-Anmeldungen, Ablaufverfolgungen und Leistungsindikatoren sind für SQL Database nicht verfügbar. In Azure können Sie dynamische Verwaltungsansichten (Data Management Views DMVs) verwenden, um Datenkapazität, Abfrageprobleme und aktuelle Verbindungen zu überwachen.

Weitere Informationen finden Sie unter [Überwachung von SQL Database mit dynamischen Verwaltungssichten](http://msdn.microsoft.com/de-de/library/windowsazure/ff394114.aspx).

Skalieren einer SQL Database-Lösung
-----------------------------------

Auf Azure ist Datenbankskalierbarkeit gleichbedeutend mit horizontaler Skalierung; hierbei wird eine Arbeitsauslastung neu auf mehrere Commodity-Server in einem Datencenter verteilt. Die horizontale Skalierung ist eine Strategie zur Behebung von Problemen mit Datenkapazität oder Leistung. Für eine sehr große und stark wachsende Datenbank wird irgendwann eine horizontale Skalierung erforderlich, unabhängig davon, ob nur einige wenige oder sehr viele Benutzer darauf zugreifen.

Auf Azure erfolgt die horizontale Skalierung am besten durch Erstellung eines Verbunds. Der SQL Database-Verbund basiert auf horizontaler Partitionierung (Sharding), wobei eine oder mehrere Tabellen nach Zeile geteilt und auf mehrere Verbundmitglieder aufgeteilt werden.

Ein Verbund ist jedoch nicht die einzige Lösung für Skalierbarkeitsprobleme. Manchmal legen die Eigenschaften von Daten oder Anwendungsanforderungen auch einfachere Methoden nahe. In der folgenden Liste werden mögliche Lösungen in der Reihenfolge ihrer Komplexität vorgestellt.

**Vergrößern der Datenbank**

Datenbanken werden mit einer festen Größe erstellt, die von einem editionsspezifischen Maximalwert abhängt. Für die Web-Edition kann eine Datenbank auf maximal 5 GB vergrößert werden. Für die Business-Edition beträgt die maximale Datenbankgröße 150 GB. Am einfachsten kann die Datenkapazität durch Ändern der Edition und der maximalen Größe erhöht werden:

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

**Verwenden mehrerer Datenbanken und Zuordnen von Benutzern**

In eingeschränkten Szenarien können Kopien einer Datenbank erstellt und dann Anmeldungen und Benutzer für die einzelnen Datenbanken zugewiesen werden. Vor der Einführung der Verbundfunktion war dies eine gängige Vorgehensweise zur Neuverteilung einer Arbeitsbelastung. Dieses Verfahren ist für Datenbanken geeignet, die Sie kurzzeitig verwenden und später in einer primären Datenbank zusammenführen, die Sie lokal verwalten, und für Lösungen, die schreibgeschützte Daten bereitstellen.

**Verwenden von Verbunden**

Mit SQL Database-Verbunden können Sie eine bessere Skalierbarkeit und eine höhere Leistung erzielen. Eine oder mehrere Tabellen in einer Datenbank werden nach Zeilen unterteilt und auf mehrere Datenbanken (Verbundmitglieder) aufgeteilt. Diese Art der horizontalen Partitionierung wird häufig als "Sharding" bezeichnet. Dies ist vor allem in Szenarien hilfreich, in denen Sie eine höhere Skalierung oder Leistung erzielen oder Kapazität verwalten müssen.

Verbunde werden nur in der Business-Edition unterstützt. Weitere Informationen finden Sie unter [Verbunde in Azure SQL Database](http://msdn.microsoft.com/de-de/library/windowsazure/hh597452.aspx) und [Lernprogramm für Verbunde in SQL Database – DBA](http://msdn.microsoft.com/de-de/library/windowsazure/hh778416.aspx).

**Erwägen anderer Speicherformen**

Denken Sie daran, dass Azure mehrere Datenspeicherformen unterstützt, darunter Tabellenspeicher und Blob-Speicher. Sofern Sie keine relationalen Funktionen benötigen, ist Tabellen- oder Blob-Speicher eventuell die wirtschaftlichere Lösung.

Nächste Schritte
----------------

Nachdem Sie sich nun mit den Grundlagen der Administration von SQL Database vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie Sie komplexere Administrationsaufgaben ausführen können.

-   Siehe [SQL Database](http://msdn.microsoft.com/de-de/library/windowsazure/gg619386) auf MSDN
-   Besuchen Sie das [SQL Database TechNet WIKI](http://social.technet.microsoft.com/wiki/contents/articles/2267.sql-azure-technet-wiki-articles-index-en-us.aspx)

