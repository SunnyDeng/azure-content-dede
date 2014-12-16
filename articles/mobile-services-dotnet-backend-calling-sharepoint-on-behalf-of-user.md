<properties urlDisplayName="Access SharePoint on behalf of the user" pageTitle="Zugreifen auf SharePoint im Namen eines Benutzers | Mobile Dev Center" metaKeywords="" description="Learn how to make calls to SharePoint on behalf of the user" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Access SharePoint on behalf of the user" authors="mahender" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="mahender" />

# Zugreifen auf SharePoint im Namen eines Benutzers

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In diesem Thema erfahren Sie, wie Sie im Namen des aktuell angemeldeten Benutzers auf SharePoint-APIs zugreifen.</p>
<p>Wenn Sie lieber ein Video zu diesem Thema ansehen möchten, können Sie den Clip auf der rechten Seite auswählen. In diesem werden dieselben Schritte behandelt wie in diesem Lernprogramm. Im Video zeigt Ihnen Mat Velloso, wie man eine Windows Store-App so ändert, dass sie mit SharePoint Online interagiert.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Azure-Mobile-Services-AAD-O365-Authentication-identity-across-services" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('http://media.ch9.ms/ch9/f217/3f8cbf94-f36b-4162-b3da-1c00339ff217/AzureMobileServicesAADO365AuthenticationIdentityA_960.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Azure-Mobile-Services-AAD-O365-Authentication-identity-across-services" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a> <span class="time">12:51:00</span></div>
</div>

In diesem Lernprogramm ändern Sie die App vom Lernprogramm Active Directory "Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden" so, dass ein Word-Dokument in SharePoint Online erstellt wird, wenn ein neues TodoItem hinzugefügt wird.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung des SharePoint-Zugriff Im-Namen-von:

1. [Registrieren Ihrer Anwendung für den delegierten Zugriff auf SharePoint]
2. [Hinzufügen von SharePoint-Informationen zu Ihrem mobilen Dienst]
3. [Beziehen eines Zugriffstokens und Aufrufen der SharePoint-API]
4. [Erstellen und Hochladen eines Word-Dokuments]
5. [Testen der Anwendung]

Für dieses Lernprogramm ist Folgendes erforderlich:

* Visual Studio 2013 unter Windows 8.1
* Ein aktives Abonnement von [SharePoint Online]
* 	Sie haben das Lernprogramm  [Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden] durchlaufen. Sie sollten das Mandat verwenden, das von Ihrem SharePoint-Abonnement bereitgestellt wird.

## <a name="configure-permissions"></a>Konfigurieren Ihrer Anwendung für den delegierten Zugriff auf SharePoint
Standardmäßig hat das Token, das Sie von AAD erhalten, eingeschränkte Berechtigungen. Um auf eine Ressource von Dritten oder eine SaaS-Anwendung wie etwa SharePoint Online zugreifen zu können, müssen Sie dies explizit erlauben.

1. Wählen Sie im Abschnitt **Active Directory** des [Azure-Verwaltungsportals] Ihr Mandat. Navigieren Sie zur Webanwendung, die Sie für den mobilen Dienst erstellt haben.

    ![][0]

2. Scrollen Sie auf der Registerkarte **Konfigurieren** nach unten zum Abschnitt "Berechtigungen für andere Anwendungen". Wählen Sie **Office 365 SharePoint Online**, und gewähren Sie die Berechtigung **Dateien des Benutzer bearbeiten oder löschen**. Klicken Sie anschließend auf **Speichern**.

    ![][1]

Sie haben jetzt AAD so konfiguriert, dass dem mobilen Dienst ein SharePoint-Zugriffstoken ausgestellt wird.

## <a name="store-credentials"></a>Hinzufügen von SharePoint-Informationen zu Ihrem mobilen Dienst

Um einen Aufruf an SharePoint zu richten, müssen Sie die Endpunkte festlegen, mit denen der mobile Dienst kommunizieren soll. Sie müssen auch die Identität Ihres mobilen Diensts nachweisen können. Das wird mit einer Client-ID und einem geheimen Clientschlüsselpaar erreicht. Sie haben bereits während der Einrichtung der AAD-Anmeldung die Client-ID für den mobilen Dienst bezogen und gespeichert. Da dies sensible Anmeldeinformationen sind, sollten Sie sie nicht als Klartext im Code speichern. Vielmehr legen Sie diese Werte als Anwendungseinstellungen für den mobilen Dienst fest.

1. Gehen Sie zurück zur Registerkarte AAD-Anwendung für Ihr Mandat und wählen Sie die Webanwendung für ihren mobilen Dienst aus.

2. Scrollen Sie unter "Konfigurieren" zu "Schlüssel". Sie erhalten einen geheimen Clientschlüssel, indem Sie einen neuen Schlüssel generieren. Nachdem Sie einen Schlüssel erzeugt und die Seite verlassen haben, gibt es keine Möglichkeit mehr, ihn aus dem Portal zu bekommen. Sie müssen eine Kopie dieses Werts an einem sicheren Ort speichern. Wählen Sie eine Dauer für den Schlüssel und klicken Sie dann auf "Speichern", und kopieren Sie den resultierenden Wert.

    ![][2]

3. Navigieren Sie im Abschnitt "Mobile Services" des Verwaltungsportals zur Registerkarte "Konfigurieren" und scrollen Sie zu "App-Einstellungen". Hier können Sie ein Schlüsselwertepaar bereitstellen, mit dem Sie auf die nötigen Anmeldeinformationen verweisen.

    ![][3]

4. Legen Sie "SP_Authority" als Autoritätsendpunkt für das AAD-Mandat fest. Das sollte derselbe Autoritätsendpunkt sein, den Sie auch für die Client-App verwendet haben. Er hat das Format https://login.windows.net/contoso.onmicrosoft.com

5. Legen Sie bei SP_ClientSecret den geheimen Clientwert fest, den Sie vorhin bezogen haben.

6. Legen Sie bei "SP_SharePointURL" die URL für Ihre SharePoint-Website fest. Sie hat das Format https://contoso-my.sharepoint.com

Sie können diese Wert mithilfe von ApiServices.Settings diese Werte in unserem Code wieder beziehen.

## <a name="obtain-token"></a>Beziehen eines Zugriffstokens und Aufrufen der SharePoint-API

Um auf SharePoint zugreifen zu können, benötigen Sie ein spezielles Zugriffstoken mit SharePoint als Zielgruppe. Dieses Token erhalten Sie, wenn Sie einen Abruf bei AAD mit der Identität des mobilen Diensts und dem Token machen, der für den Benutzer ausgestellt worden ist.

1. Öffnen Sie Ihr Mobile Services Back-End-Projekt in Visual Studio.

[WACOM.INCLUDE [mobile-services-dotnet-adal-install-nuget](../includes/mobile-services-dotnet-adal-install-nuget.md)]

2. Erstellen Sie im Mobile Services Back-End-Projekt eine neue Klasse namens "SharePointUploadContext". Fügen Sie dort Folgendes hinzu:

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
            AuthenticationResult ar = ac.AcquireToken(sharepointURL, new UserAssertion(userToken), new ClientCredential(clientId, clientSecret));
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

Um ein Word-Dokument erstellen zu können, verwenden Sie das OpenXML NuGet-Paket. Installieren Sie dieses Paket, indem Sie den NuGet Manager öffnen und nach "DocumentFormat.OpenXml" suchen.

1. Fügen Sie folgenden Code zu "TodoItemController" hinzu. Damit wird ein Word-Dokument auf Grundlage eines TodoItem erstellt. Der Text des Dokuments ist der Name des Elements.

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

1. 	Veröffentlichen Sie die Änderungen am Back-End, und führen Sie dann Ihre Clientanwendung aus. Melden Sie sich an, wenn Sie aufgefordert werden, und fügen Sie ein neues TodoItem ein.

2. Navigieren Sie zu Ihrer SharePoint-Website, und melden Sie sich mit demselben Benutzer an.

3. Wählen Sie die Registerkarte "OneDrive" aus. Unter dem Dokumentenordner sollten Sie ein Word-Dokument mit einem GUID-Titel sehen. Wenn Sie es öffnen, sollten Sie den Text für Ihr TodoItem finden.

    ![][4]


<!-- Images. -->

[0]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-web-application.png
[1]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-sharepoint-permissions.png
[2]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-manage-secret-key.png
[3]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/mobile-services-app-settings-sharepoint.png
[4]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/sharepoint-document-created.png

<!-- Anchors. -->

[Registrieren Ihrer Anwendung für den delegierten Zugriff auf SharePoint]: #configure-permissionss
[Hinzufügen von SharePoint-Informationen zu Ihrem mobilen Dienst]: #store-credentials
[Beziehen eines Zugriffstokens und Aufrufen der SharePoint-API]: #obtain-token
[Erstellen und Hochladen eines Word-Dokuments]: #create-document
[Testen der Anwendung]: #test-application

<!-- URLs. -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[SharePoint Online]: http://office.microsoft.com/de-de/sharepoint/
[Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/
