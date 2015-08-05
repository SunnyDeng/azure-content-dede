<properties 
	pageTitle="Nutzen einer API-App in Azure App Service aus einem .NET-Client" 
	description="Finden Sie heraus, wie Sie mithilfe des App Service-SDK eine API-App aus einem .NET-Client nutzen." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="tdykstra"/>

# Nutzen einer API-App in Azure App Service aus einem .NET-Client 

## Übersicht

In diesem Lernprogramm wird gezeigt, wie Sie das App Service-SDK zum Schreiben von Code verwenden, mit dem eine [API-App](app-service-api-apps-why-best-platform.md) aufgerufen wird, die für die Zugriffsebene **Öffentlich (anonym)** oder **Öffentlich (authentifiziert)** konfiguriert wurde. In diesem Artikel werden die folgenden Beispielszenarien behandelt:

- Aufruf einer API-App mit der Zugriffsebene **Öffentlich (anonym)** aus einer Konsolenanwendung
- Aufruf einer API-App mit der Zugriffsebene **Öffentlich (authentifiziert)** aus einer Windows Desktop-Anwendung 

Die einzelnen Abschnitte dieses Lernprogramms können unabhängig voneinander bearbeitet werden. Sie können die Anweisungen des zweiten Szenarios befolgen, ohne die Schritte des ersten Szenarios ausgeführt zu haben.

Informationen zum Aufrufen einer **internen** API-App finden Sie unter [Nutzen einer internen API-App aus einem .NET-Client](app-service-api-dotnet-consume-internal.md).

## Voraussetzungen

Für dieses Lernprogramm wird davon ausgegangen, dass Sie mit dem Erstellen von Projekten und Hinzufügen von Code zu diesen Projekten in Visual Studio vertraut sind. Außerdem wird vorausgesetzt, dass Sie mit den Schritten zum [Verwalten von API-Apps im Azure-Vorschauportal](app-service-api-apps-manage-in-portal.md) vertraut sind.

Die Projekt- und Codebeispiele in diesem Artikel basieren auf dem API-App-Projekt, das in den folgenden Artikeln erstellt, bereitgestellt und geschützt wird:

- [Erstellen einer API-App](app-service-dotnet-create-api-app.md)
- [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md)
- [Schützen einer API-App](../app-service-dotnet-add-authentication.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Für dieses Lernprogramm ist die Version 2.6 des Azure-SDK für .NET erforderlich.

## Nicht authentifizierter Aufruf aus einer Konsolenanwendung

In diesem Abschnitt erstellen Sie ein Konsolenanwendungsprojekt und fügen Code hinzu, um eine API-App aufzurufen, für die keine Authentifizierung erforderlich ist.

### Einrichten der API-App und Erstellen des Projekts

1. Wenn Sie die erforderlichen Schritte noch nicht ausgeführt haben, befolgen Sie die Anweisungen unter [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md), um das Beispielprojekt "ContactsList" für eine API-App in Ihrem Azure-Abonnement bereitzustellen.

	In diesem Lernprogramm werden Sie aufgefordert, die Zugriffsebene im Visual Studio-Dialogfeld zur Veröffentlichung auf **Für alle Benutzer verfügbar** zu setzen. Diese Zugriffsebene entspricht der Ebene **Öffentlich (anonym)** im Portal. Wenn Sie anschließend jedoch die Schritte im Lernprogramm [Schützen einer API-App](../app-service-dotnet-add-authentication.md) ausgeführt haben, wurde die Zugriffsebene auf **Öffentlich (authentifiziert)** gesetzt. In diesem Fall müssen Sie die Einstellung wie im folgenden Schritt beschrieben ändern.

2. Wechseln Sie im [Azure-Vorschauportal](https://portal.azure.com/)auf dem Blatt **API-App** für die API-App, die aufgerufen werden soll, zu **Einstellungen > Anwendungseinstellungen**, und legen Sie für **Zugriffsebene** die Option **Öffentlich (anonym)** fest.

	![](./media/app-service-api-dotnet-consume/setpublicanon.png)
 
2. Erstellen Sie in Visual Studio ein Projekt für eine Konsolenanwendung.
 
### <a id="addclient"></a>Hinzufügen von Clientcode, der mit dem App Service SDK erstellt wurde

[AZURE.INCLUDE [app-service-api-dotnet-add-generated-client](../../includes/app-service-api-dotnet-add-generated-client.md)]

### Hinzufügen von Code zum Aufrufen der API-App

Um die API-App aufzurufen, müssen Sie lediglich ein Clientobjekt erstellen und Methoden für dieses Objekt aufrufen. Beispiel:

        var client = new ContactList();
        var contacts = client.Contacts.Get();

1. Öffnen Sie die Datei *Program.cs*, und fügen Sie in der Methode `Main` den folgenden Code hinzu.

		var client = new ContactsList();
		
		// Send GET request.
		var contacts = client.Contacts.Get();
		foreach (var c in contacts)
		{
		    Console.WriteLine("{0}: {1} {2}",
		        c.Id, c.Name, c.EmailAddress);
		}
		
		// Send POST request.
		client.Contacts.Post(new Models.Contact
		{
		    EmailAddress = "lkahn@contoso.com",
		    Name = "Loretta Kahn",
		    Id = 4
		});
		Console.WriteLine("Finished");
		Console.ReadLine();

3. Drücken Sie STRG+F5, um die Anwendung auszuführen.

	![Generierung abgeschlossen](./media/app-service-api-dotnet-consume/consoleappoutput.png)

## Authentifizierter Aufruf aus einer Windows Desktop-Anwendung

In diesem Abschnitt erstellen Sie ein Windows Desktop-Anwendungsprojekt und fügen Code hinzu, um eine API-App aufzurufen, für die eine Authentifizierung erforderlich ist.

### Einrichten der API-App und Erstellen des Projekts

1. Führen Sie die Schritte im Lernprogramm [Schützen einer API-App](../app-service-dotnet-add-authentication.md) aus, um eine API-App mit Zugriffsebene **Öffentlich (authentifiziert)** einzurichten.

1. Erstellen Sie in Visual Studio ein Windows Forms-Desktopprojekt.

2. Fügen Sie im Formular-Designer die folgenden Steuerelemente hinzu:

	* Ein Schaltflächen-Steuerelement
	* Ein Textfeld-Steuerelement
	* Ein Webbrowser-Steuerelement

3. Legen Sie das Textfeld-Steuerelement als mehrzeilig fest.

	Ihr Formular sollte nun wie im folgenden Beispiel gezeigt aussehen:

	![](./media/app-service-api-dotnet-consume/form.png)

### Hinzufügen von Clientcode, der über das App Service-SDK erstellt wurde

3. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt (nicht auf die Projektmappe), und wählen Sie **Hinzufügen > Azure-API-App-Client**. 

3. Klicken Sie im Dialogfeld **Azure-API-App-Client hinzufügen** auf **Aus Azure-API-App herunterladen**.

5. Wählen Sie in der Dropdownliste die API-App aus, die aufgerufen werden soll, und klicken Sie auf **OK**.

### Hinzufügen von Code zum Aufrufen der API-App

5. Kopieren Sie im Azure-Vorschauportal die URL für das Gateway Ihrer API-App. Dieser Wert wird im nächsten Schritt verwendet.

	![](./media/app-service-api-dotnet-consume/gatewayurl.png)

4. Fügen Sie im Quellcode *Form1.cs* den folgenden Code vor dem Konstruktor `Form1()` hinzu, und ersetzen Sie dabei den Wert für GATEWAY_URL durch den Wert, den Sie im vorherigen Schritt kopiert haben. Stellen Sie sicher, dass Sie den nachstehenden Schrägstrich (/) hinzufügen.

		private const string GATEWAY_URL = "https://resourcegroupnameb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string URL_TOKEN = "#token=";

4. Doppelklicken Sie im Formular-Designer auf die Schaltfläche zum Hinzufügen eines Klickhandlers, und fügen Sie anschließend in der Handlermethode Code hinzu, um zur Anmelde-URL für das Gateway zu wechseln. Beispiel:

		webBrowser1.Navigate(string.Format(@"{0}login/[authprovider]", GATEWAY_URL));

	Ersetzen Sie "[authprovider]" durch den Code für den Identitätsdienstanbieter, den Sie im Gateway konfiguriert haben. Beispiele sind "aad", "twitter", "google", "microsoftaccount" oder "facebook". Beispiel:

		webBrowser1.Navigate(string.Format(@"{0}login/aad", GATEWAY_URL));

7. Fügen Sie einen Ereignishandler `DocumentCompleted` für das Webbrowser-Steuerelement und anschließend den folgenden Code zur Handlermethode hinzu.

		if (e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		{
		    var encodedJson = e.Url.AbsoluteUri.Substring(e.Url.AbsoluteUri.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		    var decodedJson = Uri.UnescapeDataString(encodedJson);
		    var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		    string userId = result.user.userId;
		    string userToken = result.authenticationToken;
		
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    appServiceClient.SetCurrentUser(userId, userToken);
		
		    var contactsListClient = appServiceClient.CreateContactsList();
		    var contacts = contactsListClient.Contacts.Get();
		    foreach (Contact contact in contacts)
		    {
		        textBox1.Text += contact.Name + " " + contact.EmailAddress + System.Environment.NewLine;
		    }
		    //appServiceClient.Logout();
		    //webBrowser1.Navigate(string.Format(@"{0}login/aad", GW_URL));
		}

	Der soeben hinzugefügte Code wird ausgeführt, nachdem sich der Benutzer über das Webbrowser-Steuerelement angemeldet hat. Nach erfolgreicher Anmeldung enthält die Antwort-URL die Benutzer-ID und das Kennwort. Der Code extrahiert diese Werte aus der URL, gibt sie an das App Service-Clientobjekt weiter und verwendet das Objekt dann zum Erstellen eines API-App-Clientobjekts. Anschließend können Sie die API aufrufen, indem Sie Methoden für dieses API-App-Clientobjekt aufrufen.

8. Drücken Sie STRG+F5, um die Anwendung auszuführen.

9. Klicken Sie auf die Schaltfläche, und geben Sie auf der vom Browser-Steuerelement angezeigten Anmeldeseite die Anmeldeinformationen eines Benutzers ein, der über die erforderliche Berechtigung zum Aufrufen der API-App verfügt.

	Azure führt eine Authentifizierung durch. Die Anwendung ruft die API-App auf und zeigt die Antwort an.

	![](./media/app-service-api-dotnet-consume/formaftercall.png)

### <a id="client-flow"></a> Serverfluss und Clientfluss im Vergleich

Die Beispielanwendung veranschaulicht den [Serverfluss](../app-service/app-service-authentication-overview.md#server-flow), was bedeutet, dass das Gateway das Zugriffstoken des Identitätsanbieters erhält. Für den [Clientfluss](../app-service/app-service-authentication-overview.md#client-flow), bei dem Ihre Clientanwendung das Zugriffstoken direkt vom Identitätsanbieter abruft und es an das Gateway sendet, rufen Sie `LoginAsync` statt `SetCurrentUser` auf.

Im folgenden Codebeispiel wird davon ausgegangen, dass das Zugriffstoken des Identitätsanbieters in einer Zeichenfolgenvariablen namens `providerAccessToken` und der Identitätsanbieterindikator ("aad", "microsoftaccount", "google", "twitter" oder "facebook") in einer Zeichenfolgenvariablen mit dem Namen `idProvider` enthalten ist:

		var appServiceClient = new AppServiceClient(GATEWAY_URL);
		var providerAccessTokenJSON = new JObject();
		providerAccessTokenJSON["access_token"] = providerAccessToken;
		var appServiceUser = await appServiceClient.LoginAsync(idProvider, providerAccessTokenJSON);

		var contactsListClient = appServiceClient.CreateContactsList();
		var contacts = contactsListClient.Contacts.Get();
		foreach (Contact contact in contacts)
		{
		    textBox1.Text += contact.Name + " " + contact.EmailAddress + System.Environment.NewLine;
		}

## Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie API-Apps mit der Zugriffsebene **Öffentlich (authentifiziert)** oder **Öffentlich (anonym)** von einem .NET-Client aus aufrufen und nutzen.

Um weitere Codebeispiele für den Aufruf einer API-App aus einem .NET-Client zu erhalten, laden Sie die [Azure Cards](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample)-Beispielanwendung herunter.

Informationen zur Authentifizierung in API-Apps finden Sie unter [Authentifizierung für API-Apps und mobile Apps in Azure App Service](../app-service/app-service-authentication-overview.md).
 

<!---HONumber=July15_HO4-->