<properties
	pageTitle="Dienstprinzipalauthentifizierung für API-Apps in Azure App Service | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine API-App in Azure App Service für Dienst-zu-Dienst-Szenarien schützen."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/28/2015"
	ms.author="tdykstra"/>

# Dienstprinzipalauthentifizierung für API-Apps in Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Übersicht

In diesem Tutorial wird veranschaulicht, wie Sie die Authentifizierungs- und Autorisierungsfunktionen von Azure App Service zum Schützen einer API-App verwenden und wie Sie eine geschützte API-App im Namen eines Dienstkontos nutzen. Ein Dienstkonto wird auch als *Dienstprinzipal* bezeichnet. Die Authentifizierung mithilfe eines solchen Kontos ist auch als *Dienst-zu-Dienst*-Szenario bekannt. In diesem Tutorial schützen Sie in einem Dienst-zu-Dienst-Szenario eine API-App, indem Sie Azure Active Directory für die Authentifizierung verwenden und die API aus einem .NET-Client nutzen.

Im Tutorial wird das ASP.NET-Web-API für den aufrufenden Client und die aufgerufene API verwendet. Die vorgestellten Verfahren gelten jedoch auch für andere von Azure App Service unterstützte Sprachen und Frameworks. Der hier gezeigte Clientcode ist Azure Active Directory-Standardcode für das Abrufen und Übergeben eines Bearertokens für ein Dienstkonto. Es ist kein spezieller Azure-Code erforderlich, wie er beispielsweise für das Behandeln des Mobile Services-Zumo-Tokens benötigt wurde.

Hierbei handelt es sich um das vierte Tutorial einer Serie, in der die Verwendung von API-Apps in Azure App Service erläutert wird. Informationen zu dieser Serie finden Sie im ersten Tutorial unter [Erste Schritte mit API-Apps und ASP.NET in Azure App Service](app-service-api-dotnet-get-started.md). Informationen zur Authentifizierung und Autorisierung in Azure App Service finden Sie im vorherigen Tutorial der Serie: [Benutzerauthentifizierung für API-Apps in Azure App Service](app-service-api-dotnet-user-principal-auth.md).

## Weitere Optionen für die Dienst-zu-Dienst-Authentifizierung

Wenn ein Dienst-zu-Dienst-Szenario nicht mithilfe von App Service-Authentifizierung und -Autorisierung, sondern beispielsweise mit Clientzertifikaten behandelt werden soll, lesen Sie die Informationen im Abschnitt [Nächste Schritte](#next-steps).

## Das Beispielprojekt „CompanyUsers.API“

In diesem Tutorial verwenden Sie die Beispielprojekte, die Sie im [ersten Tutorial dieser Serie](app-service-api-dotnet-get-started.md) heruntergeladen haben, sowie die im vorherigen Tutorials erstellten Azure-Ressourcen (API-App und Web-App).

Das Projekt „CompanyUsers.API“ ist ein einfaches Web-API-Projekt mit einer Get-Methode, die eine hardcodierte Liste mit Kontakten zurückgibt. Zur Veranschaulichung eines Dienst-zu-Dienst-Szenarios ruft die Get-Methode in „ContactsList.API“ die Get-Methode von „CompanyContacts.API“ auf und fügt die abgerufenen Kontakte zu den Daten im eigenen Datenspeicher hinzu. Anschließend gibt sie die kombinierte Liste zurück.

Hier sehen Sie die Get-Methode in „CompanyUsers.API“:

		public async Task<IEnumerable<Contact>> Get()
		{
		    var contacts = new Contact[]{
		                new Contact { Id = 1, EmailAddress = "nancy@contoso.com", Name = "Nancy Davolio"},
		                new Contact { Id = 2, EmailAddress = "alexander@contoso.com", Name = "Alexander Carson"}
		            };
		
		    return contacts;
		}


Und hier wird gezeigt, wie die Get-Methode in „ContactsList.API“ das Projekt „CompanyContacts.API“ aufruft und die Ergebnisse zu den zurückgegebenen Daten hinzufügt. (Aus Gründen der Übersichtlichkeit wird hier ein Teil des Codes weggelassen.)

		private async Task<IEnumerable<Contact>> GetContacts()
		{
		    var contacts = await _storage.Get(FILENAME);
		
		    var contactsList = contacts.ToList<Contact>();
		    using (var client = CompanyContactsAPIClientWithAuth())
		    {
		        var results = await client.Contacts.GetAsync();
		        foreach (Contact c in results)
		        {
		            contactsList.Add(c);
		        }
		    }
		
		    return contactsList;
		}

Das Clientobjekt für „CompanyContacts.API“ ist eine Änderung des generierten API-App-Clientcodes, der ein Token zur HTTP-Anforderung hinzufügt.

		private static CompanyContactsAPI CompanyContactsAPIClientWithAuth()
		{
		    var client = new CompanyContactsAPI(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		    client.HttpClient.DefaultRequestHeaders.Authorization =
		        new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		    return client;
		}

## Erstellen einer API-App in Azure und Bereitstellen des Projekts „CompanyContacts.API“ für die API-App

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt „CompanyContacts.API“ und dann auf **Veröffentlichen**.

3.  Klicken Sie im Schritt **Profil** im Assistenten **Web veröffentlichen** auf **Microsoft Azure App Service**.

	![](./media/app-service-api-dotnet-service-principal-auth/selectappservice.png)

4. Melden Sie sich an Ihrem Azure-Konto an, falls Sie dies noch nicht getan haben, oder aktualisieren Sie Ihre Anmeldeinformationen, falls sie abgelaufen sind.

4. Wählen Sie im App Service-Dialogfeld das gewünschte Azure-**Abonnement** aus, und klicken Sie dann auf **Neu**.

	![](./media/app-service-api-dotnet-service-principal-auth/clicknew.png)

3. Klicken Sie im Dialogfeld **App Service erstellen** auf der Registerkarte **Hosting** auf **Typ ändern** und dann auf **API-App**.

4. Geben Sie einen **API-App-Namen** ein, der in der Domäne *azurewebsites.net* eindeutig ist.

6. Wählen Sie in der Dropdownliste **Ressourcengruppe** die für diese Tutorials verwendete Ressourcengruppe aus.

4. Wählen Sie in der Dropdownliste **App Service-Plan** den für diese Tutorials verwendeten Plan aus.

7. Klicken Sie auf **Erstellen**.

	![](./media/app-service-api-dotnet-service-principal-auth/createappservice.png)

	Visual Studio erstellt die API-App und ein Veröffentlichungsprofil, das alle erforderlichen Einstellungen für die neue API-App enthält.

8. Klicken Sie auf der Registerkarte **Verbindung** des Assistenten **Web veröffentlichen** auf **Veröffentlichen**.

	![](./media/app-service-api-dotnet-service-principal-auth/conntab.png)

	Visual Studio stellt das Projekt in der neuen API-App bereit und öffnet einen Browser mit der URL der API-App. Im Browser wird die Seite mit dem Hinweis angezeigt, dass die Erstellung erfolgreich war.

9. Schließen Sie den Browser.

## Aktualisieren des generierten Clientcodes im Projekt „ContactsList.API“

Das Projekt „ContactsList.API“ enthält bereits den generierten Clientcode, Sie löschen ihn jedoch und erstellen ihn aus Ihrer eigenen API-App neu.

1. Löschen Sie im **Projektmappen-Explorer** von Visual Studio im Projekt „ContactsList.API“ den Ordner *CompanyContactsAPI*.

2. Klicken Sie mit der rechten Maustaste auf das Projekt „ContactsList.API“, und klicken Sie dann auf **Hinzufügen > REST-API-Client**.

3. Klicken Sie im Dialogfeld **REST-API-Client hinzufügen** auf **Aus Microsoft Azure-API-App herunterladen** und dann auf **Durchsuchen**.

8. Erweitern Sie im Dialogfeld **App Service** die für dieses Tutorial verwendete Ressourcengruppe, und wählen Sie anschließend die eben erstellte API-App aus.

10. Klicken Sie auf **OK**.

9. Klicken Sie im Dialogfeld **REST-API-Client hinzufügen** auf **OK**.

	Visual Studio erstellt einen Ordner, der nach der API-App benannt ist, und generiert Clientklassen.

## Aktualisieren von Code in „ContactsList.API“ und Bereitstellen des Projekts

Der Code in „ContactsList.API“, der „CompanyContacts.API“ aufruft, wird für die vorherigen Tutorials auskommentiert. In diesem Abschnitt heben Sie die Auskommentierung dieses Codes auf und stellen die App bereit.

1. Öffnen Sie im Projekt „ContactsList.API“ die Datei *Controllers/ContactsController.cs*.

2. Ersetzen Sie oben in der Klasse `ContactsController` im Code, der die generierte Clientklasse zum Hinzufügen eines Autorisierungstokens verwendet, den Klassennamen `CompanyContactsAPI` durch den Namen der Klasse, die von Ihrer API-App erstellt wurde.

	Heißt Ihre API-App beispielsweise „CompanyContactsAPI3“, würde der Code , wie folgt lauten:

		 private static CompanyContactsAPI3 CompanyContactsAPIClientWithAuth()
		 {
		     var client = new CompanyContactsAPI3(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		     client.HttpClient.DefaultRequestHeaders.Authorization =
		         new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		     return client;
		 }
 
4. Heben Sie in der Get-Methode die Auskommentierung des Codeblocks auf, der das von `CompanyContactsAPIClientWithAuth` zurückgegebene Clientobjekt verwendet.

		using (var client = CompanyContactsAPIClientWithAuth())
		{
		    var results = await client.Contacts.GetAsync();
		    foreach (Contact c in results)
		    {
		        contactsList.Add(c);
		    }
		}

2. Klicken Sie mit der rechten Maustaste auf das Projekt „ContactsList.API“, und klicken Sie auf **Veröffentlichen**.

	Der Assistent **Web veröffentlichen** wird mit dem zuvor verwendeten Veröffentlichungsprofil geöffnet.

3. Klicken Sie im Assistenten **Web veröffentlichen** auf **Veröffentlichen**.

## Einrichten der Authentifizierung und Autorisierung in Azure für die neue API-App

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zum Blatt „API-App“ der API-App, die Sie in diesem Tutorial für das Projekt „CompanyContacts.API“ erstellt haben, und klicken Sie dann auf **Einstellungen**.

2. Suchen Sie nach dem Abschnitt mit den **Features**, und klicken Sie dann auf **Authentifizierung/Autorisierung**.

3. Klicken Sie auf dem Blatt **Authentifizierung/Autorisierung** auf **Ein**.

4. Wählen Sie in der Dropdownliste **Aktion, wenn die Anforderung nicht authentifiziert ist** die Option **Mit Azure Active Directory anmelden**.

5. Klicken Sie unter **Authentifizierungsanbieter** auf **Azure Active Directory**.

6. Klicken Sie auf dem Blatt **Azure Active Directory-Einstellungen** auf **Express**.

	Azure erstellt in Ihrem AAD-Mandanten automatisch eine AAD-Anwendung. Notieren Sie sich den Namen der neuen AAD-Anwendung. Sie wählen sie später aus, wenn Sie auf das klassische Azure-Portal zugreifen, um ihre Client-ID abzurufen.

7. Klicken Sie auf **OK**.

10. Klicken Sie auf dem Blatt **Authentifizierung/Autorisierung** auf **Speichern**.
 
11. Navigieren Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) zu **Azure Active Directory**.

12. Klicken Sie auf der Registerkarte **Verzeichnis** auf Ihren AAD-Mandanten.

14. Klicken Sie auf **Anwendungen > Anwendung im Besitz meines Unternehmens** und dann auf das Häkchen.

15. Klicken Sie in der Liste mit den Anwendungen auf den Namen der Anwendung, die von Azure für Sie erstellt wurde, als Sie die Authentifizierung für Ihre API-App aktiviert haben.

16. Klicken Sie auf **Konfigurieren**.

15. Klicken Sie unten auf der Seite auf **Manifest verwalten > Manifest herunterladen**, und speichern Sie die Datei an einem Ort, an dem Sie sie bearbeiten können.

16. Suchen Sie in der heruntergeladenen Manifestdatei nach der `oauth2AllowImplicitFlow`-Eigenschaft. Ändern Sie den Wert dieser Eigenschaft von `false` in `true`, und speichern Sie die Datei anschließend.

16. Klicken Sie auf **Manifest verwalten > Manifest hochladen**, und laden Sie die Datei hoch, die Sie im vorherigen Schritt aktualisiert haben.

17. Lassen Sie diese Seite geöffnet, damit Sie Werte kopieren und einfügen und Werte auf der Seite in späteren Schritten des Tutorials aktualisieren können.

## Aktualisieren von Einstellungen in der API-App, die den Code des Projekts „ContactsList.API“ ausführt

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zum Blatt „API-App“ der API-App, in der Sie das Projekt „ContactsList.API“ bereitgestellt haben. Hierbei handelt es sich um die aufrufende, in diesem Tutorial erstellte API-App (nicht um die aufgerufene API-App).

2. Klicken Sie auf **Einstellungen > Anwendungseinstellungen**.

	Sie fügen hier einige Einstellungen hinzu, diese müssen jedoch von einer anderen Seite des klassischen Azure-Portals abgerufen werden.

3. Navigieren Sie im [klassischen Azure-Portal](https://manage.windowsazure.com/) zur Registerkarte **Bestätigen** der AAD-Anwendung, die Sie für die API-App „ContactsList.API“ erstellt haben.

5. Wählen Sie unter **Schlüssel** in der Dropdownliste **Dauer auswählen** die Option **1 Jahr** aus.

6. Klicken Sie auf **Speichern**.

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)


7. Kopieren Sie den Schlüsselwert.

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

3. Fügen Sie im Azure-Portal auf dem Blatt **Anwendungseinstellungen** im Abschnitt **App-Einstellungen** einen Schlüssel namens „ida:ClientSecret“ hinzu, und fügen Sie im Feld für den Wert den soeben erstellten Schlüssel ein.

3. Navigieren Sie im klassischen Azure-Portal zur Registerkarte **Konfigurieren** der AAD-Anwendung, die Sie für die API-App „CompanyContacts.API“ erstellt haben.

4. Kopieren Sie die Client-ID.

3. Fügen Sie im Azure-Portal auf dem Blatt **Anwendungseinstellungen** im Abschnitt **App-Einstellungen** einen Schlüssel namens „ida:Resource“ hinzu, und fügen Sie im Feld für den Wert die soeben kopierte Client-ID ein.

4. Fügen Sie einen Schlüssel namens „CompanyContactsAPIUrl“ hinzu, und geben Sie im Feld für den Wert „https://{your api app name}.azurewebsites.net“ ein. Beispiel: https://companycontactsapi.azurewebsites.net.

6. Klicken Sie auf Speichern.

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

## Testen in Azure

1. Öffnen Sie die URL der Web-App, in der Sie das Projekt „ContactsList.Angular.AAD“ bereitgestellt haben.

2. Klicken Sie auf die Registerkarte **Kontakte**, und melden Sie sich an.

	Die Seite „Kontakte“ wird mit den zusätzlichen Kontakten angezeigt, die aus der API-App „CompanyContacts.API“ abgerufen wurden.

	![](./media/app-service-api-dotnet-service-principal-auth/contactspagewithdavolio.png)

## Nächste Schritte

Dies ist das letzte Tutorial in der Serie für erste Schritte mit API-Apps. Dieser Abschnitt enthält weitere Informationsempfehlungen zur Verwendung von API-Apps.

* Weitere Möglichkeiten zum Nutzen einer durch Azure App Service-Authentifizierung und -Autorisierung geschützte API-App

	In diesem Artikel wurde erläutert, wie Sie eine API-App schützen und aus Code aufrufen, der in einer anderen API-App ausgeführt wird. Weitere Informationen dazu, wie Sie eine geschützte API-App aus einer Logik-App aufrufen, finden Sie unter [Verwenden der in App Service gehosteten benutzerdefinierten API mit Logik-Apps](../app-service-logic/app-service-logic-custom-hosted-api.md).

* Weitere Methoden zum Schützen einer API-App für Dienst-zu-Dienst-Szenarien

	Als Alternative zur App Service-Authentifizierung und -Autorisierung können Sie eine API-App auch mithilfe von Clientzertifikaten oder Standardauthentifizierung schützen. Informationen zu Clientzertifikaten in Azure finden Sie unter [Konfigurieren von gegenseitiger TLS-Authentifizierung für Web-Apps](../app-service-web/app-service-web-configure-tls-mutual-auth.md).

* Weitere Methoden zum Bereitstellen einer App Service-App

	Informationen zu anderen Möglichkeiten der Bereitstellung von Webprojekten in Web-Apps mit Visual Studio oder durch die [Automatisierung der Bereitstellung](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) über ein [Quellcode-Verwaltungssystem](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) finden Sie unter [Bereitstellen von Azure-Web-Apps](web-sites-deploy.md).

	Visual Studio kann auch Windows PowerShell-Skripte generieren, mit deren Hilfe Sie die Bereitstellung automatisieren können. Weitere Informationen finden Sie unter [Automate Everything (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything) (in englischer Sprache).

* Problembehandlung für App Service-Apps

	Visual Studio enthält Funktionen, die die Anzeige von Azure-Protokollen vereinfachen, da sie in Echtzeit erstellt werden. Darüber hinaus ist die Ausführung in Azure remote im Debugmodus möglich. Weitere Informationen finden Sie unter [Problembehandlung von Azure-Web-Apps in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* So fügen Sie einen benutzerdefinierten Domänennamen und SSL hinzu

	Informationen zur Verwendung von SSL und einer eigenen Domäne (z. B. www.contoso.com statt contoso.azurewebsites.net) finden Sie in den folgenden Ressourcen:

	* [Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service](web-sites-custom-domain-name.md)
	* [Aktivieren von HTTPS für Azure-Web-Apps](web-sites-configure-ssl-certificate.md)

<!---HONumber=AcomDC_1203_2015-->