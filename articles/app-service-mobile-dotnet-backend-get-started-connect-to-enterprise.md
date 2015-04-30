<properties 
	pageTitle="Verbinden einer mobilen App mit einer SaaS-Unternehmenslösung | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie Unternehmensressourcen wie beispielsweise SharePoint Online aufrufen." 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="na" 
	services="app-service\mobile"/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="mahender"/>

# Verbinden Ihrer mobilen App mit SaaS-APIs

In diesem Lernprogramm verbinden Sie Ihre mobile App mit einer SaaS-Unternehmenslösung (Software-as-a-Service). Sie aktualisieren die App mithilfe der Informationen unter [Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden], um immer dann ein Word-Dokument in SharePoint Online zu erstellen, wenn ein neues "TodoItem" hinzugefügt wird.

Für dieses Lernprogramm ist Folgendes erforderlich:

* Visual Studio 2013 unter Windows 8.1
* Ein aktives Abonnement von [SharePoint Online]
* Sie haben das Lernprogramm [Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden] durchlaufen. Sie sollten das Mandat verwenden, das von Ihrem SharePoint-Abonnement bereitgestellt wird.

## <a name="configure-permissions"></a>Konfigurieren Ihrer Anwendung für den delegierten Zugriff auf SharePoint
Standardmäßig hat der Token, den Sie von AAD erhalten, eingeschränkte Berechtigungen. Um auf eine Ressource von Dritten oder eine SaaS-Anwendung wie etwa SharePoint Online zugreifen zu können, müssen Sie dies explizit erlauben.

1. Wählen Sie im Abschnitt **Active Directory** des [Azure-Verwaltungsportals] Ihren Mandant. Navigieren Sie zur Webanwendung, die Sie für App Service erstellt haben.

2. Scrollen Sie auf der Registerkarte **Konfigurieren** nach unten zum Abschnitt "Berechtigungen für andere Anwendungen". Wählen Sie **Office 365 SharePoint Online**, und gewähren Sie die delegierte Berechtigung **Dateien der Benutzer bearbeiten oder löschen**. Klicken Sie dann auf **Speichern**.

    ![][1]

Sie haben jetzt AAD so konfiguriert, dass App Services ein SharePoint-Zugriffstoken ausgestellt wird.

## <a name="store-credentials"></a>Hinzufügen von SharePoint-Informationen zu Ihrer mobilen App

Um einen Aufruf an SharePoint zu richten, müssen Sie die Endpunkte festlegen, mit denen die mobile App kommunizieren soll. Sie müssen außerdem die Identität Ihrer mobilen App nachweisen können. Das wird mit einer Client-ID und einem geheimen Clientschlüsselpaar erreicht. Sie haben bereits während der Einrichtung der AAD-Anmeldung die Client-ID für die mobile App bezogen und gespeichert. Da dies sensible Anmeldeinformationen sind, sollten Sie sie nicht als Klartext im Code speichern. Stattdessen legen Sie diese Werte als Anwendungseinstellungen für die Mobile App-Codesite fest.

1. Wechseln Sie zurück zur Registerkarte "AAD-Anwendung" für Ihren Mandanten, und wählen Sie die Webanwendung für App Service aus.

2. Scrollen Sie unter "Konfigurieren" zu "Schlüssel". Sie erhalten einen geheimen Clientschlüssel, indem Sie einen neuen Schlüssel generieren. Nachdem Sie einen Schlüssel erzeugt und die Seite verlassen haben, gibt es keine Möglichkeit mehr, ihn aus dem Portal zu bekommen. Sie müssen eine Kopie dieses Werts an einem sicheren Ort speichern. Wählen Sie eine Dauer für den Schlüssel und klicken Sie dann auf "Speichern", und kopieren Sie den resultierenden Wert.

3. Navigieren Sie im Verwaltungsportal im Abschnitt für den Code der mobilen App zur Registerkarte "Konfiguration", und führen Sie einen Bildlauf zu den App-Einstellungen durch. Hier können Sie ein Schlüsselwertepaar bereitstellen, mit dem Sie auf die nötigen Anmeldeinformationen verweisen.

* Legen Sie SP_Authority als Autoritätsendpunkt für das AAD-Mandat fest. Das sollte derselbe Autoritätsendpunkt sein, den Sie auch für die Client-App verwendet haben. Er sollte das folgende Format aufweisen: `https://login.windows.net/contoso.onmicrosoft.com`

* Legen Sie bei SP_ClientSecret den geheimen Clientwert fest, den Sie vorhin bezogen haben.

* Legen Sie bei SP_SharePointURL die URL für Ihre SharePoint-Website fest. Sie sollte das folgende Format aufweisen: `https://contoso-my.sharepoint.com`

Sie können diese Werte mithilfe von "ApiServices.Settings" in Ihrem Code abrufen.

## <a name="obtain-token"></a>Beziehen eines Zugriffstokens und Aufrufen der SharePoint-API

Um auf SharePoint zugreifen zu können, benötigen Sie ein spezielles Zugriffstoken mit SharePoint als Zielgruppe. Um dieses Token zu erhalten, müssen Sie erneut einen Aufruf in AAD ausführen und dabei die App Service-Identität und das Token verwenden, das für den Benutzer ausgestellt wurde.

1. Öffnen Sie das Codeprojekt für die mobile App in Visual Studio.

[AZURE.INCLUDE [app-service-mobile-dotnet-adal-install-nuget](../includes/app-service-mobile-dotnet-adal-install-nuget.md)]

2. Erstellen Sie im Codeprojekt für die mobile App eine neue Klasse mit dem Namen "SharePointUploadContext". Fügen Sie dort Folgendes hinzu:

        private String accessToken;
        private String mySiteApiPath;
        private String clientId;
        private String clientSecret;
        private String sharepointURL;
        private String authority;
        private const string getFilesPath = "/getfolderbyserverrelativeurl('Documents')/Files";

        public static async Task<SharePointUploadContext> createContext(ServiceUser user, ServiceSettingsDictionary settings)
        {
            //Get the current access token
            AzureActiveDirectoryCredentials creds = (await user.GetIdentitiesAsync()).OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            string userToken = creds.AccessToken;
            return new SharePointUploadContext(userToken, settings);
        }

        private SharePointUploadContext(string userToken, ServiceSettingsDictionary settings)
        {
            //Call ADAL and request a token to SharePoint with the access token
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = ac.AcquireToken(sharepointURL, new ClientCredential(clientId, clientSecret), new UserAssertion(userToken));
            accessToken = ar.AccessToken;
            string upn = ar.UserInfo.UserId;
            mySiteApiPath = "/personal/" + upn.Replace('@','_').Replace('.','_') + "/_api/web"; 
            clientId = settings.AzureActiveDirectoryClientId;
            clientSecret = settings["SP_ClientSecret"];
            sharepointURL = settings["SP_SharePointURL"];
            authority = settings["SP_Authority"];
        }

3. Erstellen Sie jetzt eine Methode, mit der die Datei zur Dokumentenbibliothek des Benutzers hinzugefügt wird:

        public async Task<bool> UploadDocument(string docName, byte[] document)
        {
            string uploadUri = sharepointURL + mySiteApiPath + getFilesPath + string.Format(@"/Add(url='{0}.docx', overwrite=true)", docName);
            using (HttpClient client = new HttpClient())
            {
                Func<HttpRequestMessage> requestCreator = () =>
                {
                    HttpRequestMessage UploadRequest = new HttpRequestMessage(HttpMethod.Post, uploadUri);
                    UploadRequest.Content = new System.Net.Http.ByteArrayContent(document);
                    UploadRequest.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                    UploadRequest.Content.Headers.ContentType.Parameters.Add(new NameValueHeaderValue("odata", "verbose"));
                    return UploadRequest;
                };
                using (HttpRequestMessage uploadRequest = requestCreator.Invoke())
                {
                    uploadRequest.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
                    HttpResponseMessage uploadResponse = await client.SendAsync(uploadRequest);
                }
            }
            return true;
        }

## <a name="create-document"></a>Erstellen und Hochladen eines Word-Dokuments

Um ein Word-Dokument erstellen zu können, verwenden Sie das OpenXML NuGet-Paket. Installieren Sie dieses Paket, indem Sie den NuGet Manager öffnen und nach DocumentFormat.OpenXml suchen.

1. Fügen Sie folgenden Code zu TodoItemController hinzu. Damit wird ein Word-Dokument auf Grundlage eines TodoItem erstellt. Der Text des Dokuments ist der Name des Elements.

        private static byte[] CreateWordDocument(TodoItem todoItem)
        {
            byte[] document;
            using (MemoryStream generatedDocument = new MemoryStream())
            {
                using (WordprocessingDocument package = WordprocessingDocument.Create(generatedDocument, WordprocessingDocumentType.Document))
                {
                    MainDocumentPart mainPart = package.MainDocumentPart;
                    if (mainPart == null)
                    {
                        mainPart = package.AddMainDocumentPart();
                        new Document(new Body()).Save(mainPart);
                    }
                    Body body = mainPart.Document.Body;
                    Paragraph p =
                        new Paragraph(
                            new Run(
                                new Text(todoItem.Text)));
                    body.Append(p);
                    mainPart.Document.Save();
                }
                document = generatedDocument.ToArray();
            }
            return document;
        }

2. Ersetzen Sie PostTodoItem durch Folgendes:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            
            SharePointUploadContext context = await SharePointUploadContext.createContext((ServiceUser)this.User, Services.Settings);
            byte[] document = CreateWordDocument(item);
            bool uploadResult = await context.UploadDocument(item.Id, document);
            
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

## <a name="test-application"></a>Testen der Anwendung

1. Veröffentlichen Sie die Änderungen am Back-End, und führen Sie dann Ihre Clientanwendung aus. Melden Sie sich an, wenn Sie aufgefordert werden, und fügen Sie ein neues TodoItem ein.

2. Navigieren Sie zu Ihrer SharePoint-Website, und melden Sie sich mit demselben Benutzer an.

3. Wählen Sie die Registerkarte "OneDrive". Unter dem Dokumentenordner sollten Sie ein Word-Dokument mit einem GUID-Titel sehen. Wenn Sie es öffnen, sollten Sie den Text für Ihr TodoItem finden.

<!-- Images. -->

[1]: ./media/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise/aad-sharepoint-permissions.png

<!-- URLs. -->

[Vorschau auf das Azure-Verwaltungsportal]: https://portal.azure.com/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Azure-Verwaltungsportals]: https://manage.windowsazure.com/
[SharePoint Online]: http://office.microsoft.com/de-de/sharepoint/
[Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden]: app-service-mobile-dotnet-backend-ios-aad-sso-preview.md

<!--HONumber=49-->