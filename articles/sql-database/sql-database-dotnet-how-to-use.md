<properties 
	pageTitle="Verwenden von SQL-Datenbank (.NET) – Azure-Featureleitfaden" 
	description="Erste Schritte mit SQL-Datenbank. Erfahren Sie, wie Sie eine SQL-Datenbankinstanz erstellen und über ADO.NET, ODBC und EntityClient Provider eine Verbindung zu dieser herstellen." 
	services="sql-database" 
	documentationCenter=".net" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="jeffreyg"/>


# Verwenden von Azure SQL-Datenbank in .NET-Anwendungen

Diese Anleitung beschreibt, wie Sie einen logischen Server und eine Datenbankinstanz in Azure SQL-Datenbank erstellen und eine Verbindung zu der Datenbank mit den folgenden .NET Framework-Dienstanbieter-Technologien herstellen: ADO.NET, ODBC und EntityClient Provider.


## Was ist SQL-Datenbank?

SQL Database bietet ein Verwaltungssystem für relationale Datenbanken für Azure und basiert auf SQL Server-Technologie. Mit einer SQL-Datenbankinstanz können Sie relationale Datenbanklösungen für die Cloud bereitstellen und implementieren, und Sie profitieren von einem verteilten Datencenter, das Verfügbarkeit, Skalierbarkeit und Sicherheit für Unternehmen mit den Vorteilen von integriertem Datenschutz und Selbstreparatur bietet.



## Anmelden bei Azure

SQL Database bietet relationalen Datenspeicher sowie Zugriff und Verwaltungsdienste in Azure. Sie benötigen ein Azure-Abonnement, um SQL-Datenbank zu verwenden.

1. Öffnen Sie einen Webbrowser, und navigieren Sie zu [http://azure.microsoft.com/](http://azure.microsoft.com). Wenn Sie ein kostenloses Konto erstellen möchten, klicken Sie auf die kostenlose Testversion oben rechts, und befolgen Sie dann die Anweisungen.

2. Ihr Konto wurde erstellt. Sie können jetzt loslegen.


## Erstellen und Konfigurieren von SQL-Datenbank.

Als Nächstes erstellen und konfigurieren Sie eine Datenbank und einen Server. Im Azure-Verwaltungsportal können Sie mit überarbeiteten Workflows zunächst eine Datenbank erstellen und anschließend einen Server einrichten.

**Erstellen von Datenbank und logischem Server**

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com) an.

2. Klicken Sie unten auf der Seite auf **New**.

3. Klicken Sie auf **Data Services**.

4. Klicken Sie auf **SQL Database**.

5. Klicken Sie auf **Custom Create**.

6. Geben Sie unter Name einen Datenbanknamen ein.

7. Wählen Sie Edition, Maximalgröße und Sortierung aus. Für dieses Lernprogramm können Sie die Standardwerte verwenden.

	SQL Database bietet drei Datenbankeditionen, Basic, Standard und Premium.

	Die maximale Größe wird bei der erstmaligen Erstellung der Datenbank mithilfe von MAXSIZE angegeben und kann später mithilfe von ALTER DATABASE geändert werden. Mithilfe von MAXSIZE lässt sich die Größe der Datenbank einschränken.

	Für jede in Azure erstellte SQL-Datenbank existieren eigentlich drei Replikate dieser Datenbank. Auf diese Weise wird hohe Verfügbarkeit sichergestellt. Das Failover erfolgt transparent und ist Teil des Diensts.

8. Wählen Sie unter Server die Option **New SQL Database Server** aus.

9. Klicken Sie auf den Pfeil, um nächsten Seite zu gelangen.

10. Geben Sie im Dialogfeld Server Settings einen Anmeldenamen für den SQL Server an.

	SQL Database verwendet SQL-Authentifizierung über eine verschlüsselte Verbindung. Es wird ein neuer SQL Server-Authentifizierungsanmeldename mit dem von Ihnen angegebenen Namen erstellt, welcher der festen Serverrolle sysadmin zugewiesen ist.

	Der Anmeldename darf keine E-Mail-Adresse, kein Windows-Benutzerkonto und auch keine Windows Live ID sein. Ansprüche oder Windows-Authentifizierung wird in SQL Database nicht unterstützt.

11. Geben Sie ein sicheres Kennwort mit mindestens acht Zeichen an, das eine Kombination aus Groß- und Kleinbuchstaben und Zahlen oder Symbolen enthält.

12. Wählen Sie eine Region aus. Die Region bestimmt den geografischen Standort des Servers. Die Region kann nicht einfach geändert werden, wählen Sie also einen sinnvollen Standort für den Server aus. Wählen Sie den nächstgelegenen Standort aus. Wenn Sie Ihre Azure-Anwendung und -Datenbank in derselben Region platzieren, sparen Sie Zugangskosten für Bandbreite und Datenlatenz.

13. Lassen Sie **Allow Azure Services to access the server** aktiviert, damit Sie über das Verwaltungsportal für SQL Database, Speicherdienste und andere Dienste in Azure eine Verbindung zur Datenbank herstellen können.

14. Klicken Sie abschließend auf das Häkchen unten auf der Seite.

Beachten Sie, dass Sie keinen Servernamen angegeben haben. SQL Database generiert automatisch einen Servernamen, um doppelte DNS-Einträge zu verhindern. Der Servername ist eine alphanumerische Zeichenfolge aus zehn Zeichen. Sie können den Namen Ihres SQL Database-Servers nicht ändern.

Klicken Sie nach der Erstellung auf die Datenbank, um deren Dashboard zu öffnen. Im Dashboard können Sie die Verbindungszeichenfolgen kopieren, die Sie später in Ihrem Anwendungscode verwenden. Dort finden Sie außerdem die Verwaltungs-URL, die Sie für die Verbindung mit der Datenbank in Management Studio oder anderen Verwaltungstools eingeben müssen.


![Das SQL-Datenbank-Dashboard](./media/sql-database-dotnet-how-to-use/SQLDbDashboard.PNG)


Im nächsten Schritt konfigurieren Sie die Firewall, sodass Verbindungen von Anwendungen, die in Ihrem Netzwerk ausgeführt werden, Zugriff erhalten.

**Konfigurieren der Firewall für den logischen Server**

1. Klicken Sie auf **SQL-Datenbank**, dann oben auf der Seite auf **Server** und anschließend auf den gerade erstellten Server.

	![Einrichten einer Firewall](./media/sql-database-dotnet-how-to-use/SQLDBFirewall.PNG)

2. Klicken Sie auf **Konfigurieren**.

3. Kopieren Sie die aktuelle Client-IP-Adresse. Wenn Sie eine Verbindung von einem Netzwerk aus herstellen, ist dies die IP-Adresse, die Ihr Router oder Proxyserver abhört. SQL Database erkennt die IP-Adresse, die von der aktuellen Verbindung verwendet wird, sodass Sie eine Firewall-Regel zur Genehmigung der Verbindungsanforderungen von diesem Gerät erstellen können.

4. Fügen Sie die IP-Adresse unter START IP ADDRESS und END IP ADDRESS ein, um den Adressenbereich einzurichten, der sich mit dem Server verbinden darf. Wenn später Verbindungsfehler auftreten, die angeben, dass der Bereich zu eng ist, können Sie diese Regel bearbeiten und den Bereich erweitern.

	Wenn Clientcomputer dynamisch zugewiesene IP-Adressen verwenden, müssen Sie einen Bereich angeben, der breit genug für die IP-Adressen ist, die den Computern im Netzwerk zugewiesen wurden. Beginnen Sie mit einem schmalen Bereich, und erweitern Sie diesen nur im Bedarfsfall.

5. Geben Sie einen Namen für die Firewall-Regel ein, z. B. den Namen Ihres Computers oder der Firma.

6. Klicken Sie auf das Häkchen neben der Regel, um diese zu speichern.

	![IP-Adressbereich für die Firewall-Einstellungen](./media/sql-database-dotnet-how-to-use/SQLDBIPRange.PNG)

7. Klicken Sie unten auf der Seite auf **Save**, um den Schritt abzuschließen. Falls **Save** nicht angezeigt wird, aktualisieren Sie die Browserseite.

Sie verfügen nun über eine Datenbankinstanz, einen logischen Server, eine Firewallregel, die eingehende Verbindungen von Ihrer IP-Adresse erlaubt, sowie eine Administratoranmeldung. Sie können nun eine programmgesteuerte Verbindung zur Datenbank aufbauen.


## Verbinden mit SQL Database

Dieser Abschnitt beschreibt, wie Sie sich über die unterschiedlichen .NET Framework-Datenanbieter mit der SQL Database-Instanz verbinden können. Zentrale Empfehlungen zum Herstellen einer Verbindung zu einem SQL-Datenbankserver und der Datenbank finden Sie unter:


- [Verbindungen mit SQL-Datenbank: Zentrale Empfehlungen](../sql-database-connect-central-recommendations.md).


Falls Sie Visual Studio verwenden und Ihre Konfiguration keine Azure-Webanwendung als Front-End enthält, müssen Sie keine weiteren Tools oder SDKs auf dem Entwicklungscomputer installieren. Sie können direkt mit der Entwicklung Ihrer Anwendung beginnen.

Sie können in Visual Studio für SQL Database dieselben Designer-Tools verwenden, mit denen Sie auch unter SQL Server arbeiten. Im Server Explorer können Sie Datenbankobjekte anzeigen (jedoch nicht bearbeiten). Der Visual Studio Entity Data Model-Designer ist voll funktionstüchtig und kann zur Erstellung von Modellen in SQL Database für die Arbeit mit dem Entity Framework verwendet werden.

## Verwenden des .NET Framework-Datenanbieters für SQL Server

Der **System.Data.SqlClient**-Namespace ist der .NET Framework-Datenanbieter für SQL Server.

Die Standard-Verbindungszeichenfolge sieht folgendermaßen aus:

    Server=tcp:.database.windows.net;
    Database=;
    User ID=@;
    Password=;
    Trusted_Connection=False;
    Encrypt=True;

Sie können die **SQLConnectionStringBuilder**-Klasse zum Erstellen einer Verbindungszeichenfolge verwenden, wie im folgenden Codebeispiel gezeigt:

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder();
    csBuilder.DataSource = xxxxxxxxxx.database.windows.net;
    csBuilder.InitialCatalog = testDB;
    csBuilder.Encrypt = true;
    csBuilder.TrustServerCertificate = false;
    csBuilder.UserID = MyAdmin;
    csBuilder.Password = pass@word1;

Wenn Sie die Elemente der Verbindungszeichenfolge vorab kennen, können Sie diese in einer Konfigurationsdatei speichern und zur Laufzeit abrufen, um die Verbindungszeichenfolge zu erstellen. Hier sehen Sie eine Beispiel-Verbindungszeichenfolge in einer Konfigurationsdatei:

    <connectionStrings>
      <add name="ConnectionString" 
           connectionString ="Server=tcp:xxxxxxxxxx.database.windows.net;Database=testDB;User ID=MyAdmin@xxxxxxxxxx;Password=pass@word1;Trusted_Connection=False;Encrypt=True;" />
    </connectionStrings>

Verwenden Sie die **ConfigurationManager**-Klasse, um die Verbindungszeichenfolge aus der Konfigurationsdatei abzurufen:

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder(ConfigurationManager.ConnectionStrings["ConnectionString"].ConnectionString);
    After you have built your connection string, you can use the SQLConnection class to connect the SQL Database server:
    SqlConnection conn = new SqlConnection(csBuilder.ToString());
    conn.Open();

## Verwenden des .NET Framework-Datenanbieters für ODBC

Der **System.Data.Odbc**-Namespace ist der .NET Framework-Datenanbieter für ODBC. Hier sehen Sie ein Beispiel für eine ODBC-Verbindungszeichenfolge:

    Driver={SQL Server Native Client 10.0};
    Server=tcp:.database.windows.net;
    Database=;
    Uid=@;
    Pwd=;
    Encrypt=yes;

Die **OdbcConnection**-Klasse stellt eine offene Verbindung zu einer Datenquelle dar. Dieses Codebeispiel zeigt, wie Sie eine Verbindung öffnen können:

    string cs = "Driver={SQL Server Native Client 10.0};" +
                "Server=tcp:xxxxxxxxxx.database.windows.net;" +
                "Database=testDB;"+
                "Uid=MyAdmin@xxxxxxxxxx;" +
                "Pwd=pass@word1;"+
                "Encrypt=yes;";

    OdbcConnection conn = new OdbcConnection(cs);
    conn.Open();

Falls Sie die Verbindungszeichenfolge zur Laufzeit erstellen möchten, können Sie die **OdbcConnectionStringBuilder**-Klasse verwenden.

## Verwenden des EntityClient-Anbieters

Der **System.Data.EntityClient**-Namespace ist der .NET Framework-Datenanbieter für das Entity Framework.

Mit dem Entity Framework müssen Entwickler zum Erstellen von Datenzugriffsanwendungen nicht direkt für ein relationales Speicherschema programmieren, sondern können sich an einem konzeptionellen Anwendungsmodell orientieren. Das Entity Framework baut auf den speicherspezifischen ADO.NET-Datenanbietern auf und bietet eine **EntityConnection** zu einem zugrunde liegenden Datenanbieter und einer relationalen Datenbank an.

Um ein **EntityConnection**-Objekt zu erstellen, benötigen Sie eine Sammlung von Metadaten mit den erforderlichen Modellen und Zuordnungen sowie Name und Verbindungszeichenfolge eines speicherspezifischen Datenanbieters. Nach der Erstellung der **EntityConnection** können Sie über die Klassen aus dem konzeptionellen Modell auf die Entitäten zugreifen.

Hier sehen Sie eine Beispiel-Verbindungszeichenfolge:

    metadata=res://*/SchoolModel.csdl|res://*/SchoolModel.ssdl|res://*/SchoolModel.msl;provider=System.Data.SqlClient;provider connection string="Data Source=xxxxxxxxxx.database.windows.net;Initial Catalog=School;Persist Security Info=True;User ID=MyAdmin;Password=***********"

Weitere Informationen finden Sie unter [EntityClient-Anbieter für das Entity Framework](http://msdn.microsoft.com/library/bb738561.aspx).

## Nächste Schritte

Nachdem Sie nun die Grundlagen der Verbindung mit der SQL-Datenbank kennengelernt haben, informieren Sie sich weiter unter [Entwicklung: Gewusst-wie-Themen (SQL-Datenbank)](http://msdn.microsoft.com/library/windowsazure/ee621787.aspx)
 

<!---HONumber=July15_HO4-->