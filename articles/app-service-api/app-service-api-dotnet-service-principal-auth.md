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
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Dienstprinzipalauthentifizierung für API-Apps in Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Übersicht

In diesem Tutorial wird veranschaulicht, wie Sie die Authentifizierungs- und Autorisierungsfunktionen von Azure App Service zum Schützen einer API-App verwenden und wie Sie eine geschützte API-App im Namen eines Dienstkontos nutzen. Der im Tutorial beschriebene Authentifizierungsanbieter ist Azure Active Directory, und sowohl Client als auch API sind ASP.NET-Web-APIs, die in API-Apps ausgeführt werden.

## Authentifizierung und Autorisierung in App Service

Eine Einführung in die Authentifizierungsfeatures, die in diesem Tutorial verwendet werden, finden Sie im vorherigen Tutorial dieser Reihe: [Authentifizierung und Autorisierung für API-Apps in Azure App Service](app-service-api-authentication.md).

## Inhalt dieses Tutorials

Dieses Tutorial baut auf einer Beispielanwendung auf, die Sie herunterladen und für die Sie im [ersten Tutorial der Reihe mit den ersten Schritten mit API-Apps und ASP.NET](app-service-api-dotnet-get-started.md) eine API-App erstellen.

## Das Beispielprojekt „CompanyUsers.API“

In der [Beispielanwendung „ContactsList“](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) ist das Projekt „CompanyUsers.API“ ein einfaches Web-API-Projekt mit einer GET-Methode, die eine hardcodierte Liste mit Kontakten zurückgibt. Zur Veranschaulichung eines Dienst-zu-Dienst-Szenarios ruft die GET-Methode in „ContactsList.API“ die GET-Methode in „CompanyContacts.API“ auf und fügt die abgerufenen Kontakte den Daten im eigenen Datenspeicher hinzu. Anschließend gibt sie die kombinierte Liste zurück.

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

Das im obigen Code von `CompanyContactsAPIClientWithAuth()` zurückgegebene Clientobjekt basiert auf dem generierten Clientcode, fügt jedoch ein Autorisierungstoken zu HTTP-Anforderungen hinzu.

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

4. Wählen Sie im Dialogfeld **App Service** das gewünschte Azure-**Abonnement** aus, und klicken Sie dann auf **Neu**.

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

	Wenn Sie die API-App nicht in der Liste sehen, besteht die Möglichkeit, dass Sie beim Erstellen der API-App versehentlich den Schritt ausgelassen haben, bei dem der Typ von „Web-App“ in „API-App“ geändert wird. In diesem Fall können Sie eine neue API-App erstellen, indem Sie die vorherigen Schritte wiederholen. Sie müssen einen anderen Namen für die API-App wählen, es sei denn, Sie besuchen Sie das Portal, um zuerst die Web-App zu löschen.

10. Klicken Sie auf **OK**.

9. Klicken Sie im Dialogfeld **REST-API-Client hinzufügen** auf **OK**.

	Visual Studio erstellt einen Ordner, der nach der API-App benannt ist, und generiert Clientklassen.

## Aktualisieren von Code in „ContactsList.API“ und Bereitstellen des Projekts

Der Code in „ContactsList.API“, der „CompanyContacts.API“ aufruft, wird für die vorherigen Tutorials auskommentiert. In diesem Abschnitt heben Sie die Auskommentierung dieses Codes auf und stellen die App bereit.

1. Öffnen Sie im Projekt „ContactsList.API“ die Datei *Controllers/ContactsController.cs*.

2. Ersetzen Sie oben in der `ContactsController`-Klasse im Code, der die generierte Clientklasse zum Hinzufügen eines Autorisierungstokens verwendet, den Klassennamen `CompanyContactsAPI` durch den Namen der Klasse, die von Ihrer API-App erstellt wurde.

	Heißt Ihre API-App beispielsweise „CompanyContactsAPI3“, würde der Code , wie folgt lauten:

		 private static CompanyContactsAPI3 CompanyContactsAPIClientWithAuth()
		 {
		     var client = new CompanyContactsAPI3(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		     client.HttpClient.DefaultRequestHeaders.Authorization =
		         new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		     return client;
		 }
 
4. Heben Sie in der `Get`-Methode die Kommentierung des Codeblocks auf, der „CompanyContacts.API“ aufruft.

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

	Visual Studio stellt das Projekt bereit und öffnet ein Browserfenster mit der Basis-URL der API-App. Schließen Sie dieses Browserfenster.

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

17. Lassen Sie diese Seite geöffnet, damit Sie Werte kopieren und einfügen und Werte auf der Seite in späteren Schritten des Tutorials aktualisieren können.

## Aktualisieren von Einstellungen in der API-App, die den Code des Projekts „ContactsList.API“ ausführt

3. Navigieren Sie in einem anderen Browserfenster zum [klassischen Azure-Portal](https://manage.windowsazure.com/) und dann zur Registerkarte **Bestätigen** der AAD-Anwendung, die Sie für die API-App „ContactsList.API“ erstellt haben.

5. Wählen Sie unter **Schlüssel** in der Dropdownliste **Dauer auswählen** die Option **1 Jahr** aus.

6. Klicken Sie auf **Speichern**.

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. Kopieren Sie den Schlüsselwert.

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

1. Navigieren Sie in einem anderen Browserfenster zum [Azure-Portal](https://portal.azure.com/) und dann zum Blatt „API-App“ der API-App, in der Sie das Projekt „ContactsList.API“ bereitgestellt haben. (Dies ist die aufrufende, nicht die aufgerufene API-App: „ContactsList.API“, nicht „CompanyContacts.API“.)

2. Klicken Sie auf **Einstellungen > Anwendungseinstellungen**.

3. Fügen Sie im Abschnitt **App-Einstellungen** einen Schlüssel namens „ida:ClientSecret“ hinzu, und fügen Sie im Feld für den Wert den soeben erstellten Schlüssel ein.

3. Fügen Sie einen Schlüssel mit dem Namen „ida: ClientId“ hinzu, und fügen Sie im Feld für den Wert die Client-ID von der gleichen AAD-Seite **Konfigurieren** ein.

4. Fügen Sie einen Schlüssel mit dem Namen „ida: Authority“ hinzu, und geben Sie im Feld für den Wert „https://login.windows.net/ {Ihr Mandant}“ ein, z. B. „https://login.windows.net/contoso.onmicrosoft.com“.

3. Navigieren Sie im klassischen Azure-Portal zur Registerkarte **Konfigurieren** der AAD-Anwendung, die Sie für die API-App „CompanyContacts.API“ erstellt haben.

4. Kopieren Sie die Client-ID.

3. Fügen Sie im Azure-Portal auf dem Blatt **Anwendungseinstellungen** im Abschnitt **App-Einstellungen** einen Schlüssel namens „ida:Resource“ hinzu, und fügen Sie im Feld für den Wert die soeben kopierte Client-ID ein.

4. Fügen Sie einen Schlüssel namens „CompanyContactsAPIUrl“ hinzu, und geben Sie im Feld für den Wert „https://{Name Ihrer API-App}.azurewebsites.net.azurewebsites.net“ ein. Beispiel: „https://companycontactsapi.azurewebsites.net“.

6. Klicken Sie auf Speichern.

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

## Testen in Azure

1. Öffnen Sie die URL der Web-App, in der Sie das Projekt „ContactsList.Angular.AAD“ bereitgestellt haben.

2. Klicken Sie auf die Registerkarte **Kontakte**, und melden Sie sich an.

	Die Seite „Kontakte“ wird mit den zusätzlichen Kontakten angezeigt, die aus der API-App „CompanyContacts.API“ abgerufen wurden.

	![](./media/app-service-api-dotnet-service-principal-auth/contactspagewithdavolio.png)

Wie auch schon im vorherigen Tutorial können Sie die Visual Studio-Projekte mit SSL-URLs für „localhost“ einrichten und die Anwendung lokal ausführen. In diesem Fall können Sie in der Datei „Web.config“ die Einstellungen speichern, die für die Ausführung in Azure (Client-ID, geheimer Schlüssel usw.) in Azure gespeichert haben. Checken Sie jedoch auf keinen Fall eine Datei „web.config“ in die Quellcodeverwaltung ein, die vertrauliche Informationen wie den geheimen Clientschlüssel enthält. Weitere Informationen finden Sie unter [Best Practices für das Bereitstellen von Kennwörtern und anderen vertraulichen Daten in ASP.NET und Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

## Schützen der API-App vor dem Browserzugriff

In diesem Tutorial haben Sie im Azure-Portal die Option „Express“ verwendet, um die AAD-Authentifizierung für die API-App einzurichten, auf die Sie über die Dienstprinzipalauthentifizierung zugreifen möchten. Standardmäßig konfiguriert App Service die neue AAD-Anwendung in einer Weise, die es einem Benutzer erlaubt, zur URL der API-App zu wechseln, um sich anzumelden. Das heißt, dass ein Endbenutzer und nicht nur ein Dienstprinzipal auf die API zugreifen kann. Wenn Sie möchten, dass nur ein Dienstprinzipal auf die API zugreifen darf, können Sie den Browserzugriff verhindern, indem die **Antwort-URL** in der AAD-Anwendung so ändern, dass sie sich von der Basis-URL der API-App unterscheidet.

### Überprüfen, ob der Browserzugriff funktioniert

1. Öffnen Sie in einem neuen Browserfenster die HTTPS-URL der API-App, die Sie für das „CompanyContacts.API“-Projekt erstellt haben.

	Der Browser wechselt zu einem Anmeldebildschirm.
	
2. Melden Sie sich mit den Anmeldeinformationen eines Benutzers in Ihrem AAD-Mandanten an.

3. Der Browser zeigt den Bildschirm „Erfolgreich erstellt“ der API-App an.

	Wenn die Swagger-Benutzeroberfläche aktiviert wäre, könnten Sie zur `/swagger`-URL wechseln und die API aufrufen. Sie können die API aus dem Browser aufrufen, indem Sie `/api/contacts/get` zur URL hinzufügen.

### Deaktivieren des Browserzugriffs

1. Ändern Sie im klassischen Portal auf der Registerkarte **Konfigurieren** der AAD-Anwendung, die für das Projekt „CompanyContacts.API“ erstellt wurde, den Wert im Feld **Antwort-URL** in eine gültige URL, die nicht die URL der API-App ist.
 
2. Klicken Sie auf **Speichern**.

### Prüfen, ob der Browserzugriff nicht mehr funktioniert

1. Öffnen Sie in einem neuen Browserfenster erneut die URL der API-App.

2. Melden Sie sich an, wenn Sie dazu aufgefordert werden.

3. Die Anmeldung ist erfolgreich, führt aber zu einer Seite mit einer Fehlermeldung.

	Sie können mithilfe eines Dienstprinzipaltokens weiter auf die API-App zugreifen, aber Benutzer im AAD-Mandanten können sich nicht anmelden und nicht in einem Browser auf die API zugreifen.

## Nächste Schritte

Dies ist das letzte Tutorial in der Serie für erste Schritte mit API-Apps. Dieser Abschnitt enthält weitere Informationsempfehlungen zum Arbeiten mit API-Apps.

* Weitere Methoden zum Bereitstellen einer App Service-App

	Informationen zu anderen Möglichkeiten der Bereitstellung von Webprojekten in Web-Apps mit Visual Studio oder durch die [Automatisierung der Bereitstellung](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) über ein [Quellcode-Verwaltungssystem](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control) finden Sie unter [Bereitstellen von Azure-Web-Apps](web-sites-deploy.md).

	Visual Studio kann auch Windows PowerShell-Skripte generieren, mit deren Hilfe Sie die Bereitstellung automatisieren können. Weitere Informationen finden Sie unter [Automate Everything (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything) (in englischer Sprache).

* Problembehandlung für App Service-Apps

	Visual Studio enthält Funktionen, die die Anzeige von Azure-Protokollen vereinfachen, da sie in Echtzeit erstellt werden. Darüber hinaus ist die Ausführung in Azure remote im Debugmodus möglich. Weitere Informationen finden Sie unter [Problembehandlung von Azure-Web-Apps in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* So fügen Sie einen benutzerdefinierten Domänennamen und SSL hinzu

	Informationen zur Verwendung von SSL und einer eigenen Domäne (z. B. www.contoso.com statt contoso.azurewebsites.net) finden Sie in den folgenden Ressourcen:

	* [Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service](web-sites-custom-domain-name.md)
	* [Aktivieren von HTTPS für Azure-Web-Apps](web-sites-configure-ssl-certificate.md)

<!---HONumber=AcomDC_0114_2016-->