<properties 
	pageTitle="Verbinden eines lokalen SQL-Servers von einer API-App in Azure App Service über Hybridverbindungen" 
	description="Erstellen einer API-App in Microsoft Azure und Herstellen einer Verbindung von der API-App mit einer lokalen SQL Server-Datenbank"
	services="app-service\api" 
	documentationCenter="" 
	authors="tarcher" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="tarcher"/>

# Verbinden eines lokalen SQL-Servers von einer API-App in Azure App Service über Hybridverbindungen

Hybridverbindungen ermöglichen die Verbindung von [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-API-Apps mit lokalen Ressourcen, die einen statischen TCP-Port verwenden. Zu den unterstützten Ressourcen gehören Microsoft SQL Server, MySQL, HTTP Web APIs, Mobile Services und die meisten benutzerdefinierten Webdienste.

In diesem Tutorial erfahren Sie, wie eine App Service-API-App in der [Azure-Vorschau](http://go.microsoft.com/fwlink/?LinkId=529715) erstellt wird, die mithilfe der neuen Funktion für Hybridverbindungen eine Verbindung mit einer lokalen SQL Server-Datenbank herstellt. Bei diesem Tutorial wird davon ausgegangen, dass Sie noch keine Erfahrungen mit der Verwendung von Azure oder SQL Server haben.

[AZURE.INCLUDE app-service-web-try-app-service.md]

## Voraussetzungen

Zum Durchführen des Lernprogramms benötigen Sie folgende Produkte: Alle Produkte sind als kostenlose Versionen verfügbar, sodass Sie kostenlos mit der Entwicklung von Azure-Lösungen beginnen können.

- **Azure-Abonnement** - Informationen zu einem kostenlosen Abonnement finden Sie unter [Kostenlose Azure-Testversion](/pricing/free-trial/). 

- **Visual Studio** – Zum Herunterladen einer kostenlosen Testversion von Visual Studio 2013 und Visual Studio 2015 besuchen Sie [Visual Studio-Downloads](http://www.visualstudio.com/downloads/download-visual-studio-vs). Installieren Sie eines dieser Produkte, bevor Sie fortfahren. \(Die Screenshots in diesem Tutorial wurden mit Visual Studio 2013 erstellt.\)

- **Microsoft .NET Framework 3.5 Service Pack 1** - Wenn Sie das Betriebssystem Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 oder Windows Server 2008 R2 verwenden, können Sie diese Komponente in Systemsteuerung \> "Programme und Funktionen" bzw. "Programme und Features" \> "Windows-Funktionen ein- oder ausschalten" aktivieren. Andernfalls können Sie sie vom [Microsoft Download Center](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22) herunterladen.

- **SQL Server 2014 Express with Tools** - Laden Sie Microsoft SQL Server Express kostenlos von der [Microsoft Web Platform Datenbank-Seite](https://www.microsoft.com/en-us/download/details.aspx?id=42299) herunter. Später in diesem Tutorial erfahren Sie, wie [SQL Server installiert wird](#InstallSQLDB), sodass eine ordnungsgemäße Konfiguration sichergestellt ist.

- **SQL Server Management Studio Express** - Dieses Produkt ist im oben angegebenen Download "SQL Server 2014 Express with Tools" enthalten, Sie müssen es jedoch getrennt installieren. Sie können es von der [Download-Seite für SQL Server Express](https://www.microsoft.com/en-us/download/details.aspx?id=42299) herunterladen und installieren.

In diesem Lernprogramm wird davon ausgegangen, dass Sie ein Azure-Abonnement besitzen, Visual Studio 2013 installiert haben und .NET Framework 3.5 installiert oder aktiviert haben. In diesem Lernprogramm wird gezeigt, wie Sie SQL Server 2014 Express in einer Konfiguration installieren, die sich gut für die Azure-Funktion "Hybridverbindungen" eignet \(eine Standardinstanz mit einem statischen TCP-Port\). Bevor Sie mit dem Tutorial beginnen, laden Sie "SQL Server 2014 Express with Tools" vom oben angegebenen Speicherort herunter \(installieren Sie das Programm aber nicht\), sofern Sie SQL Server nicht installiert haben.

### Hinweise
Um eine lokale SQL Server- oder SQL Server Express-Datenbank mit einer Hybridverbindung verwenden zu können, muss TCP/IP an einem statischen Port aktiviert werden. Standardinstanzen von SQL Server verwenden den statischen Port 1433, benannte Instanzen dagegen nicht.

Der Computer, auf dem Sie den lokalen Hybridverbindungs-Manager-Agent installieren:

- Sie benötigen Outbound-Konnektivität zu Azure über:

> <table border="1">
    <tr>
       <th><strong>Port</strong></th>
        <th>Warum</th>
    </tr>
    <tr>
        <td>80</td>
        <td><strong>Erforderlich</strong> für HTTP-Port für Zertifikatüberprüfung und optional für Datenkonnektivität.</td>
    </tr>
    <tr>
        <td>443</td>
        <td><strong>Optional</strong> für Datenkonnektivität. Wenn keine ausgehende Konnektivität zu 443 verfügbar ist, wird TCP-Port 80 verwendet.</td>
    </tr>
	<tr>
        <td>5671 und 9352</td>
        <td><strong>Empfohlen</strong>, jedoch für Datenkonnektivität optional. Beachten Sie, dass dieser Modus normalerweise für mehr Durchsatz sorgt. Wenn Outbound-Konnektivität zu diesen Ports nicht verfügbar ist, wird TCP-Port 443 verwendet.</td>
	</tr>
</table>

- Muss in der Lage sein, *Hostname*:\*Portnummer\*Ihrer lokalen Ressource zu erreichen. 

Bei den Schritten in diesem Artikel wird davon ausgegangen, dass Sie den Browser auf dem Computer verwenden, auf dem der lokale Hybridverbindungs-Agent installiert ist.

Wenn Sie SQL Server bereits in einer Konfiguration und Umgebung installiert haben, welche die oben beschriebenen Kriterien erfüllen, können Sie gleich mit [Erstellen einer lokalen SQL Server-Datenbank](#CreateSQLDB) beginnen.

<a name="InstallSQL"></a>
## Installieren von SQL Server Express, Aktivieren von TCP/IP und Erstellen einer lokalen SQL Server-Datenbank

In diesem Abschnitt wird erläutert, wie Sie SQL Server Express installieren, TCP/IP aktivieren und eine Datenbank erstellen, damit Ihre API-App im [Azure-Vorschauportal](https://portal.azure.com) funktioniert.

<a name="InstallSQLDB"></a>
### SQL Server Express installieren

1. Um SQL Server Express zu installieren, laden Sie entweder die Datei **SQLEXPRWT\_x64\_ENU.exe** \(64-Bit-Version\) oder **SQLEXPR\_x86\_ENU.exe** \(32-Bit-Version\) herunter, und extrahieren Sie sie im gewünschten Ordner. 

2. Nachdem Sie die SQL Server Express-Installationsdateien extrahiert haben, führen Sie **setup.exe** aus.

3. Wenn das **SQL Server-Installationscenter** angezeigt wird, wählen Sie **Neue eigenständige SQL Server-Installation oder Hinzufügen von Features zu einer vorhandenen Installation** aus.

	![SQL Server-Installationscenter](./media/app-service-api-hybrid-on-premises-sql-server/sql-server-installation-center.png)

4. Befolgen Sie die Anweisungen, und übernehmen Sie die Standardeinstellungen, bis Sie zur Seite **Instanzkonfiguration** gelangen.
	
5. Wählen Sie auf der Seite **Instanzkonfiguration** die Option **Standardinstanz** aus, und klicken Sie auf **Weiter**.
	
	![Instanzkonfiguration](./media/app-service-api-hybrid-on-premises-sql-server/instance-configuration.png)
	
	Die Standardinstanz von SQL Server überwacht den statischen Port 1433 auf Anforderungen von SQL Server-Clients, und das ist das Verhalten, das von der Funktion "Hybridverbindungen" vorausgesetzt wird. Benannte Instanzen verwenden dynamische Ports und UDP, die von Hybridverbindungen nicht unterstützt werden.
	
6. Übernehmen Sie die Standardeinstellungen auf der Seite **Serverkonfiguration**, und klicken Sie auf **Weiter**.
	
7. Wählen Sie auf der Seite **Datenbankmodulkonfiguration** unter **Authentifizierungsmodus** die Option **Gemischter Modus \(SQL Server-Authentifizierung und Windows-Authentifizierung\)**, und geben Sie ein Kennwort an.
	
	![Konfiguration des Datenbankmoduls](./media/app-service-api-hybrid-on-premises-sql-server/database-engine-configuration.png)
	
	In diesem Lernprogramm verwenden Sie die SQLServer-Authentifizierung. Merken Sie sich unbedingt das Kennwort, das Sie angeben, weil Sie es später benötigen werden.
	
8. Bearbeiten Sie die restlichen Schritte des Assistenten, und übernehmen Sie dabei die Standardeinstellungen, um die Installation abzuschließen.

9. Schließen Sie das Dialogfeld **SQL Server-Installationscenter**.

### TCP/IP aktivieren
Zum Aktivieren von TCP/IP verwenden Sie den SQL Server-Konfigurations-Manager, der zusammen mit SQL Server Express installiert wurde. Führen Sie zunächst die in [Aktivieren des TCP/IP-Netzwerkprotokolls für SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) beschriebenen Schritte aus, bevor Sie fortfahren.

<a name="CreateSQLDB"></a>
### Eine lokale SQL Server-Datenbank erstellen

1. Stellen Sie in **SQL Server Management Studio** eine Verbindung mit der SQL Server-Instanz her, die Sie gerade installiert haben. Wählen Sie unter **Servertyp** die Option **Datenbankmodul**. Als **Servername** können Sie **localhost** oder den Namen des Computers, den Sie benutzen, verwenden. Wählen Sie **SQL Server-Authentifizierung** aus, und melden Sie sich dann mit dem Benutzernamen `sa` und dem Kennwort an, das Sie zuvor erstellt haben.

	![Mit Server verbinden](./media/app-service-api-hybrid-on-premises-sql-server/connect-to-server.png)
	
	Wenn das Dialogfeld **Verbindung mit Server herstellen** nicht automatisch angezeigt wird, navigieren Sie im linken Bereich zu **Objekt-Explorer**, und klicken Sie auf **Verbinden** und anschließend auf **Datenbankmodul**.
	
2. Um eine neue Datenbank mit SQL Server Management Studio zu erstellen, klicken Sie in Objekt-Explorer mit der rechten Maustaste auf **Datenbanken** und anschließend auf **Neue Datenbank**.
	
	![Menü "Neue Datenbank erstellen"](./media/app-service-api-hybrid-on-premises-sql-server/new-database-menu.png)
	
3. Geben Sie im Dialogfeld **Neue Datenbank** als Datenbankname `LocalDatabase` ein, und klicken Sie dann auf **OK**.
	
	![Erstellen einer neuen Datenbank](./media/app-service-api-hybrid-on-premises-sql-server/new-database.png)
	
### Erstellen und Auffüllen einer SQL Server-Tabelle

1. Erweitern Sie im **SQL Server Management Studio** im **Objekt-Explorer** den Eintrag `LocalDatabase`.

	![Erweiterte Datenbank](./media/app-service-api-hybrid-on-premises-sql-server/local-database-expanded.png)

2. Klicken Sie mit der rechten Maustaste auf **Tabellen**, und wählen Sie im Kontextmenü die Option **Tabelle...** aus.

	![Menü "Neue Tabelle"](./media/app-service-api-hybrid-on-premises-sql-server/new-table-menu.png)

3. Wenn das Raster des Tabellen-Designers angezeigt wird, geben Sie die Spalteninformationen ein, wie in der folgenden Abbildung dargestellt.

	![Neue Tabellenspalten](./media/app-service-api-hybrid-on-premises-sql-server/table-def.png)

4. Drücken Sie **STRG+S**, um die Definition der neuen Tabelle zu speichern. Sie werden aufgefordert, einen Tabellennamen einzugeben. Geben Sie `Speakers` ein, und klicken Sie auf **OK**.

	![Speichern der neuen Tabelle](./media/app-service-api-hybrid-on-premises-sql-server/save-new-table.png)

5. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf die neu erstellte Tabelle `Speakers`, und wählen Sie im Kontextmenü **Oberste 200 Zeilen bearbeiten** aus.

	![Bearbeiten der obersten 200 Zeilen](./media/app-service-api-hybrid-on-premises-sql-server/edit-top-200-rows.png)

6. Wenn das Raster angezeigt wird, damit Sie die Tabellendaten eingeben oder ändern können, geben Sie einige Testdaten ein, wie in der folgenden Abbildung dargestellt.

	![Testdaten](./media/app-service-api-hybrid-on-premises-sql-server/speakers-data.png)

## Erstellen und Bereitstellen der Demo-API-App in Azure 

In diesem Abschnitt wird das Erstellen der Demo-API-App erläutert.

1. Öffnen Sie Visual Studio 2013, und wählen Sie **Datei \> Neu \> Projekt** aus. 

2. Wählen Sie die Vorlage **Visual C\# \> Web \> ASP.NET-Webanwendung** aus, deaktivieren Sie die Option zum Hinzufügen von Application Insights zum Projekt, nennen Sie das Projekt *SpeakersList*, und klicken Sie dann auf **OK**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project.png)

3. Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Projektvorlage **Azure API-App** aus, und klicken Sie auf **OK**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-project-api-app.png)

4. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Modelle**, und wählen Sie dann im Kontextmenü **Hinzufügen \> Klasse...** aus.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-menu.png)

5. Nennen Sie die neue Datei *Speakers.cs*, und klicken Sie dann auf **Hinzufügen**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-model-class.png)

6. Ersetzen Sie den gesamten Inhalt der Datei `Speaker.cs` durch den folgenden Code:

		namespace SpeakersList.Models
		{
			public class Speaker
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

7. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Controller**, und wählen Sie dann im Kontextmenü **Hinzufügen \> Controller...** aus.

	![](./media/app-service-api-hybrid-on-premises-sql-server/new-controller.png)

8. Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **Web API 2-Controller – Leer** aus, und klicken Sie auf **Hinzufügen**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-scaffold.png)

9. Nennen Sie den Controller **SpeakersController**, und klicken Sie auf **Hinzufügen**.

	![](./media/app-service-api-hybrid-on-premises-sql-server/add-controller-name.png)

10. Ersetzen Sie den Code in der Datei `SpeakersController.cs` durch den folgenden Code. Geben Sie Ihre eigenen Werte für die Platzhalter &lt;serverName\> und &lt;password\> in `connectionString` an. Der Wert &lt;serverName\> ist der Name des Computers, auf dem sich SQL Server befindet, und der Wert &lt;password\> ist der, den Sie beim Installieren und Konfigurieren von SQL Server festgelegt haben.

	> [AZURE.NOTE]Der folgende Codeausschnitt enthält Kennwortinformationen. Dies dient dazu, die Demo einfach zu halten. In einer realen Produktionsumgebung sollten Sie Ihre Anmeldeinformationen nicht im Code speichern. Lesen Sie stattdessen die [bewährten Methoden für das Bereitstellen von Kennwörtern \(und anderen vertraulichen Daten\) auf ASP.NET und Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using SpeakersList.Models;
		using System.Data.Sql;
		using System.Data.SqlClient;
		
		namespace SpeakersList.Controllers
		{
		    public class SpeakersController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Speaker> Get()
		        {
					// Instantiate List object that will be populated and returned
					// from this method.
		            List<Speaker> speakers = new List<Speaker>();
	
					// Build database connection string.
		            string connectionString = "Server=<serverName>;" +
		                                      "Initial Catalog=LocalDatabase;" +
		                                      "User Id=sa;Password=<password>;" +
		                                      "Asynchronous Processing=true;";
		
					// Build query string to select all three columns from the Speakers table.
		            string queryString = "SELECT Id, EmailAddress, Name FROM dbo.Speakers;";

					// Instantiate the SqlConnection object using the connection string.
		            using (SqlConnection connection = new SqlConnection(connectionString))
		            {
						// Instantiate the SqlCommand object using the connection and query strings.
		                SqlCommand command = new SqlCommand(queryString, connection);

						// Open the connection to the database.
		                connection.Open();

						// Execute the command and return a SqlDataReader object
						// that can be iterated.
		                SqlDataReader reader = command.ExecuteReader();
		                try
		                {
							// Read a row from the SqlDataReader object
		                    while (reader.Read())
		                    {
								// For each row, use the returned data to instantiate
								// and add to the List a new Speaker object.
		                        speakers.Add(new Speaker 
		                            { 
		                                Id = Convert.ToInt32(reader[0]), 
		                                EmailAddress = reader[1].ToString(), 
		                                Name = reader[2].ToString() 
		                            }
		                        );
		                    }
		                }
		                finally
		                {
							// Close the SqlDataReader object.
		                    reader.Close();
		                }
		            }
					// Return the List of Speaker objects.
		            return speakers;
		        }
		    }
		}

### Aktivieren der Swagger-Benutzeroberfläche

Durch Aktivieren der Swagger-Benutzeroberfläche können Sie ganz einfach Ihre API-App testen, ohne Clientcode zu schreiben, um sie aufzurufen.

1. Öffnen Sie die Datei *App\_Start/SwaggerConfig.cs*, und suchen Sie nach **EnableSwaggerUI**:

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-disabled.png)

2. Entfernen Sie die Kommentare in den folgenden Codezeilen:

	        })
	    .EnableSwaggerUi(c =>
	        {

3. Wenn Sie fertig sind, überprüfen Sie, ob die Datei wie die folgende Abbildung aussieht.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui-enabled.png)

### Testen der API-App

1. Um die API-Testseite anzuzeigen, führen Sie die App lokal über **STRG+F5** aus. Es sollte ein Fehler angezeigt werden, der folgendem Bild ähnelt:

	![](./media/app-service-api-hybrid-on-premises-sql-server/error-forbidden.png)

2. Fügen Sie in der Adressleiste des Browsers am Ende der URL `/swagger` hinzu, und drücken Sie die **EINGABETASTE**. Dadurch wird die Swagger-Benutzeroberfläche angezeigt, die Sie im vorherigen Abschnitt aktiviert haben.

	![](./media/app-service-api-hybrid-on-premises-sql-server/swagger-ui.png)

3. Klicken Sie auf den Abschnitt **Speakers**, um ihn zu erweitern.

4. Klicken Sie auf **GET /api/speakers**, um den Abschnitt zu erweitern.

5. Klicken Sie auf **Ausprobieren**, um die Daten anzuzeigen, die Sie zuvor in die Datenbank eingegeben haben.

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out.png)

### Bereitstellen der API-App

Nachdem Sie die App lokal getestet haben, ist es Zeit, die App in Azure bereitzustellen.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt \(nicht die Projektmappe\), und klicken Sie auf **Veröffentlichen...**. 

	![Menüoption zur Projektveröffentlichung](./media/app-service-api-hybrid-on-premises-sql-server/publish-menu.png)

2. Klicken Sie auf die Registerkarte **Profil**, und klicken Sie auf **Microsoft Azure API-Apps \(Vorschau\)**.

	![Menüoption zur Projektveröffentlichung](./media/app-service-api-hybrid-on-premises-sql-server/publish-web.png)

3. Klicken Sie auf **Neu**, um eine neue API-App im Azure-Abonnement bereitzustellen.

	![Dialogfeld zur Auswahl vorhandener API-Dienste](./media/app-service-api-hybrid-on-premises-sql-server/publish-to-existing-api-app.png)

4. Geben Sie im Dialogfeld **API-App erstellen** Folgendes ein:

	- Geben Sie unter **Name der API-App** einen Namen für die App ein. 
	- Wenn Sie über mehrere Azure-Abonnements verfügen, wählen Sie dasjenige aus, das Sie verwenden möchten.
	- Treffen Sie unter **App Service-Plan** eine Auswahl aus Ihren vorhandenen App Service-Plänen, oder wählen Sie **Neuen App Service-Plan erstellen** aus, und geben Sie den Namen eines neuen Plans ein. 
	- Wählen Sie unter **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder wählen Sie **Neue Ressourcengruppe erstellen**, und geben Sie einen Namen ein. Der Name muss eindeutig sein. Verwenden Sie eventuell den App-Namen als Präfix, und hängen Sie persönliche Informationen wie z. B. Ihre Microsoft-ID \(ohne das @-Zeichen\) an.  
	- Wählen Sie unter **Zugriffsebene** die Option **Für alle Benutzer verfügbar** aus. Durch diese Option wird steht die gesamte API öffentlich zur Verfügung, was für dieses Lernprogramm in Ordnung ist. Sie können den Zugriff später über das [Azure-Vorschauportal](https://portal.azure.com) einschränken.
	- Wählen Sie eine Region aus.

	Klicken Sie auf **OK**, um die API-App in Ihrem Abonnement zu erstellen.

	![Dialogfeld zum Konfigurieren von Microsoft Azure Web-Apps](./media/app-service-api-hybrid-on-premises-sql-server/publish-new-api-app.png)

5. Der Vorgang kann einige Minuten dauern, daher werden Sie von Visual Studio in einem Dialogfeld darüber benachrichtigt, dass der Vorgang gestartet wurde. Klicken Sie im Bestätigungsdialogfeld auf **OK**.

	![Bestätigungsmeldung zum Start der Erstellung von API-Diensten](./media/app-service-api-hybrid-on-premises-sql-server/create-api-app-confirmation.png)

6. Während im Bereitstellungsprozess die Ressourcengruppe und die API-App in Ihrem Azure-Abonnement erstellt werden, zeigt Visual Studio den Fortschritt im Fenster **Azure App Service-Aktivität** an.

	![Statusbenachrichtigung über das Fenster "Azure App Service-Aktivität"](./media/app-service-api-hybrid-on-premises-sql-server/app-service-activity.png)

7. Sobald die API-App bereitgestellt wird, klicken Sie mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und wählen Sie **Veröffentlichen**, um das Dialogfeld für die Veröffentlichung erneut zu öffnen. Das Veröffentlichungsprofil, das Sie im vorherigen Schritt erstellt haben, sollte vorab ausgewählt sein. Klicken Sie auf **Veröffentlichen**, um den Bereitstellungsprozess zu beginnen.

	![Bereitstellen der API-App](./media/app-service-api-hybrid-on-premises-sql-server/publish2.png)

Im Fenster **Azure App Service-Aktivität** wird der Bereitstellungsfortschritt angezeigt, und es wird angegeben, wenn der Veröffentlichungsprozess abgeschlossen ist.

## Erstellen einer Hybridverbindung und eines BizTalk-Diensts ##

1. Navigieren Sie in Ihrem Browser zum [Azure-Vorschauportal](https://portal.azure.com). 

2. Klicken Sie auf der linken Seite auf die Option **Alle durchsuchen**.

3. Wählen Sie auf dem Blatt **Durchsuchen** die Option **API-Apps** aus.

4. Suchen Sie auf dem Blatt **API-Apps** nach Ihrer App, und klicken Sie darauf.

5. Klicken Sie auf dem Blatt Ihrer API-App auf den Wert unter **API-App-Host**.
 
	![Blatt "API-App"](./media/app-service-api-hybrid-on-premises-sql-server/api-app-blade-api-app-host.png)

6. Wenn das Blatt **API-App-Host** angezeigt wird, führen Sie einen Bildlauf nach unten zum Abschnitt **Netzwerk** aus, und klicken Sie auf **Hybridverbindungen**.
	
	![Hybridverbindungen](./media/app-service-api-hybrid-on-premises-sql-server/api-app-host-blade-hybrid-connections.png)
	
7. Klicken Sie im Blatt **Hybridverbindungen** auf **Hinzufügen** \> **Neue Hybridverbindung**.
	
8. Geben Sie im Fensterbereich **Create hybrid connection** Folgendes ein:
	- Geben Sie unter **Name** einen Namen für die Verbindung ein.
	- Geben Sie als **Hostname** den Computernamen Ihres SQL Server-Hostcomputers ein.
	- Geben Sie bei **Port** den Wert `1433` ein \(Standardport für SQL Server\).
	- Klicken Sie auf **Biz Talk Service**, und geben Sie einen Namen für den BizTalk-Dienst ein.
	
	![Hybridverbindung erstellen](./media/app-service-api-hybrid-on-premises-sql-server/create-biztalk-service.png)
		
9. Klicken Sie zweimal auf **OK**.

	Nach Abschluss des Prozesses leuchtet im Bereich **Benachrichtigungen** der Text **ERFOLG** in grüner Schrift, und das Blatt **Hybridverbindungen** zeigt die neue Hybridverbindung mit dem Status **Nicht verbunden** an.
	
	![Hybridverbindung wurde erstellt](./media/app-service-api-hybrid-on-premises-sql-server/hybrid-not-connected-yet.png)
	
An diesem Punkt haben Sie einen wichtigen Teil der Hybridverbindungsinfrastruktur der Cloud erstellt. Als Nächstes erstellen Sie das lokale Gegenstück.

<a name="InstallHCM"></a>
## Installieren des lokalen Hybridverbindungs-Managers zum Herstellen der Verbindung

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

Nachdem die Hybridverbindungsinfrastruktur jetzt vollständig ist, können Sie die Anwendung testen.

<a name="CreateASPNET"></a>
## Testen der fertigen API-App in Azure

1. Wechseln Sie im Azure-Vorschauportal zum Blatt "API-App-Host", und klicken Sie auf den Wert unter **URL**.
	
2. Sobald die Hostseite der API-App in Ihrem Browser angezeigt wird, fügen Sie `/swagger` an die URL in der Adressleiste Ihres Browsers an, und drücken Sie die **EINGABETASTE**.
	
3. Klicken Sie auf den Abschnitt **Speakers**, um ihn zu erweitern.

4. Klicken Sie auf **GET /api/speakers**, um den Abschnitt zu erweitern.

5. Klicken Sie auf **Ausprobieren**, um die Daten anzuzeigen, die Sie zuvor in die Datenbank eingegeben haben.

	![](./media/app-service-api-hybrid-on-premises-sql-server/try-it-out-azure.png)
	
**Glückwunsch!** Sie haben eine API-App erstellt, die in Azure ausgeführt wird und eine Hybridverbindung zum Herstellen einer Verbindung mit einer lokalen SQL Server-Datenbank verwendet.

## Weitere Informationen
[Überblick über Hybridverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Einführung in Hybridverbindungen von Josh Twist \(Channel 9-Video\)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Überblick über Hybridverbindungen](/services/biztalk-services/)

[BizTalk Services: Registerkarten "Dashboard", "Überwachen", "Skalieren", "Konfigurieren" und "Hybridverbindungen"](../biztalk-dashboard-monitor-scale-tabs/)

[Erstellen einer echten Hybrid-Cloud mit nahtloser Anwendungsportabilität \(Channel 9-Video\)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Verbinden mit einem lokalen SQL Server über einen mobilen Azure-Dienst mithilfe von Hybridverbindungen](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[Verbinden mit einem lokalen SQL Server über Azure Mobile Services mithilfe von Hybridverbindungen \(Channel 9-Video\)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[Übersicht über ASP.NET Identity](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

<!---HONumber=July15_HO5-->