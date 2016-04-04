<properties 
	pageTitle="Zugreifen auf Azure Mobile Engagement-Dienst-APIs mit der REST-API" 
	description="Beschreibt, wie die Mobile Engagement-REST-APIs für den Zugriff auf Azure Mobile Engagement-Dienst-APIs verwendet werden kann."		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="wesmc7777" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/10/2016" 
	ms.author="wesmc" />

#Zugreifen auf Azure Mobile Engagement-Dienst-APIs mit REST

Azure Mobile Engagement stellt die [Azure Mobile Engagement-REST-API](https://msdn.microsoft.com/library/azure/mt683754.aspx) zur Verfügung, mit der Sie Geräte, Reichweiten-/Pushkampagnen usw. verwalten können. In diesem Beispiel werden die REST-APIs direkt zum Erstellen einer Ankündigungskampagne verwendet. Anschließend wird diese aktiviert und per Push an eine Gruppe von Geräten übertragen.

Wenn Sie die REST-APIs nicht direkt verwenden möchten, stellen wir Ihnen auch eine [Swagger-Datei](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) bereit, die Sie mit Tools zum Generieren von SDKs für Ihre bevorzugte Sprache verwenden können. Wir empfehlen die Verwendung des Tools [AutoRest](https://github.com/Azure/AutoRest), um Ihr SDK aus unserer Swagger-Datei zu generieren. Auf ähnliche Weise haben wir ein .NET SDK erstellt, das Ihnen die Interaktion mit diesen APIs mit einem C#-Wrapper erlaubt, ohne dass Sie das Authentifizierungstoken selbst aushandeln und aktualisieren müssen. Ein ähnliches Beispiel mit diesem Wrapper finden Sie in diesem [Beispiel für eine Dienst-API mit dem .NET SDK](mobile-engagement-dotnet-sdk-service-api.md).

In diesem Beispiel werden die REST-APIs direkt zum Erstellen einer Ankündigungskampagne verwendet.

## Hinzufügen der appInfo „user\_name“ zur Mobile Engagement-App

Für dieses Beispiel muss der Mobile Engagement-App ein app-info-Tag hinzugefügt werden. Sie können das Tag im Engagement-Portal für Ihre App hinzufügen, indem Sie auf **Einstellungen** > **Tag (app-info)** > **Neues Tag (app-info)** klicken. Fügen Sie das neue Tag namens **user\_name** mit dem Typ **Zeichenfolge** hinzu.

![](./media/mobile-engagement-dotnet-rest-service-api/user-name-app-info.png)



Wenn Sie die Anleitung unter [Erste Schritte mit Azure Mobile Engagement für Windows Universal-Apps](mobile-engagement-windows-store-dotnet-get-started.md) befolgt haben, können Sie das Senden des Tags **user\_name** testen, indem Sie `OnNavigatedTo()` in Ihrer `MainPage`-Klasse so außer Kraft setzen, dass das app-info-Tag gesendet wird. Verwenden Sie dazu in etwa folgenden Code:

    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        base.OnNavigatedTo(e);

        String name = "Wesley"; // Prompt the user to provide this in your client app.

        Dictionary<object, object> info = new Dictionary<object, object>();
        info.Add("user_name", name);
        EngagementAgent.Instance.SendAppInfo(info);
    }
 


## Erstellen der Dienst-API-App

1. Zunächst einmal benötigen Sie vier Authentifizierungsparameter, die für dieses Beispiel verwendet werden. Diese Parameter sind **SubscriptionId**, **TenantId**, **ApplicationId** und **Secret**. Um diese Authentifizierungsparameter zu erhalten, wird empfohlen, dass Sie den PowerShell-Skriptansatz anwenden. Dieser wird im Tutorial [Authentifizierung](mobile-engagement-api-authentication.md#authentication) unter *Einmalige Einrichtung (per Skript)* beschrieben. 

2. Wir verwenden eine einfache Windows-Konsolen-App, um die Arbeit mit den REST-Dienst-APIs zum Erstellen und Aktivieren einer Ankündigungskampagne zu demonstrieren. Öffnen Sie daher Visual Studio, und erstellen Sie eine neue **Konsolenanwendung**.

3. Fügen Sie Ihrem Projekt das NuGet-Paket **Newtonsoft.Json** hinzu.

4. Fügen Sie in der Datei `Program.cs` die folgenden `using`-Anweisungen für die folgenden Namespaces hinzu:

		using System.IO;
		using System.Net;
		using Newtonsoft.Json.Linq;
		using Newtonsoft.Json;

5. Als Nächstes müssen Sie die folgenden Konstanten in der Klasse `Program` definieren. Diese werden für die Authentifizierung und die Interaktion mit der Mobile Engagement-App verwendet, in der Sie die Ankündigungskampagne erstellen:


        // Parameters needed for authentication of API calls.
		// These are returned from the PowerShell script in the authentication tutorial. 
		// https://azure.microsoft.com/documentation/articles/mobile-engagement-api-authentication/
        static String SubscriptionId = "<Your Subscription Id>";
        static String TenantId = "<Your TenantId>";
        static String ApplicationId = "<Your Application Id>";
        static String Secret = "<Your Secret>";

		// The token for authenticating with your Mobile Engagement app.
        static String Token = null;

        // This is the Azure Resource group concept for grouping together resources 
        // See: https://azure.microsoft.com/de-DE/documentation/articles/resource-group-portal/
        static String ResourceGroup = "MobileEngagement";

        // For Mobile Engagement operations
        // App Collection Name from the Azure portal 
        static String Collection = "<Your App Collection Name>";

        // Application Resource Name - make sure you are using the one as specified in the dashboard of the
		// Azure portal. (This is NOT the App Name)
        static String AppName = "WesmcRestTest-windows";

		// New campaign id returned from creating the new campaign and used to activate and push the campaign
		// a set of devices.
        static String NewCampaignID = null;

		// This list will hold the device Ids we choose to push the campaign to.
        static List<String> deviceList = new List<String>();


6. Fügen Sie der Klasse `Program` die folgenden beiden Methoden hinzu. Diese werden zum Ausführen von REST-APIs und zum Anzeigen von Antworten in der Konsole angezeigt.

        private static async Task<HttpWebResponse> ExecuteREST(string httpMethod, string uri, string authToken, WebHeaderCollection headers = null, string body = null, string contentType = "application/json")
        {
            //=== Execute the request 
            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uri);
            HttpWebResponse response = null;

            request.Method = httpMethod;
            request.ContentType = contentType;
            request.ContentLength = 0;

            if (authToken != null)
                request.Headers.Add("Authorization", authToken);

            if (headers != null)
            {
                request.Headers.Add(headers);
            }

            if (body != null)
            {
                byte[] bytes = Encoding.UTF8.GetBytes(body);

                try
                {
                    request.ContentLength = bytes.Length;
                    Stream requestStream = request.GetRequestStream();
                    requestStream.Write(bytes, 0, bytes.Length);
                    requestStream.Close();
                }
                catch (Exception e)
                {
                    Console.WriteLine(e.Message);
                }
            }

            try
            {
                response = (HttpWebResponse)await request.GetResponseAsync();
            }
            catch (WebException we)
            {
                if (we.Response != null)
                {
                    response = (HttpWebResponse)we.Response;
                }
                else
                    Console.WriteLine(we.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

            return response;
        }


        private static void DisplayResponse(dynamic data, HttpWebResponse response)
        {
            Console.WriteLine("HTTP Status " + (int)response.StatusCode + " : " + response.StatusDescription);
            Console.WriteLine(data + "\n");
        }

    }

7. Fügen Sie der `Main`-Methode den folgenden Code hinzu, um ein Authentifizierungstoken mit den Authentifizierungsparametern zu generieren, die Sie gesammelt haben:

        //***************************************************************************
        //*** Get a valid authorization token with your authentication parameters ***
        //***************************************************************************

        String methodUrl = "https://login.microsoftonline.com/" + TenantId + "/oauth2/token";
        String requestBody = "grant_type=client_credentials&client_id=" + ApplicationId +
                            "&client_secret=" + Secret +
                            "&resource=https://management.core.windows.net/";
        Console.WriteLine("Getting authorization token...\n");
        HttpWebResponse response = ExecuteREST("POST", methodUrl, null, null, requestBody, null).Result;
        Stream receiveStream = response.GetResponseStream();
        StreamReader readStream = new StreamReader(receiveStream, Encoding.UTF8);
        dynamic data = JObject.Parse(readStream.ReadToEnd());
        readStream.Close();
        receiveStream.Close();
        DisplayResponse(data, response);

        if (response.StatusCode == HttpStatusCode.OK)
        {
            Token = data.token_type + " " + data.access_token;
            Console.WriteLine("Got authorization token...");
            Console.WriteLine("Authorization : " + Token + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to get authorization token. Check your parameters for API calls.\n");
            return;
        }

8. Mit diesem gültigen Authentifizierungstoken können wir jetzt eine neue Reichweitenkampagne mithilfe der REST-API [Kampagne erstellen](https://msdn.microsoft.com/library/azure/mt683742.aspx) erstellen. Die neue Kampagne ist eine einfache **AnyTime**- und **Notification-only**-Ankündigungskampagne mit einem Titel und einer Nachricht. Wie im folgenden Screenshot gezeigt, handelt es sich um eine manuelle Pushkampagne. Das heißt, sie wird nur über die APIs per Push übertragen.


	![](./media/mobile-engagement-dotnet-rest-service-api/manual-push.png)

	Fügen Sie Ihrer `Main`-Methode den folgenden Code hinzu, um die Ankündigungskampagne zu erstellen:

        //*****************************************************************************
        //*** Create a campaign to send a notification using the user-name app-info ***
        //*****************************************************************************

        String newCampaignMethodUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
               "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
               Collection + "/apps/" + AppName + "/campaigns/announcements?api-version=2014-12-01";

        String campaignRequestBody = "{ "name": "BirthdayCoupon", " +
                                        ""type": "only_notif", " +
                                        ""deliveryTime": "any", " +
                                        ""notificationType": "popup", " +
                                        ""pushMode":"manual", " +
                                        ""notificationTitle": "Happy Birthday ${user_name}", " +
                                        ""notificationMessage": "Take extra 10% off on your orders today!"}";

        Console.WriteLine("Creating new campaign...\n");
        HttpWebResponse newCampaignResponse = ExecuteREST("POST", newCampaignMethodUrl, Token, null, campaignRequestBody).Result;
        Stream receiveCampaignStream = newCampaignResponse.GetResponseStream();
        StreamReader readCampaignStream = new StreamReader(receiveCampaignStream, Encoding.UTF8);
        dynamic newCampaignData = JObject.Parse(readCampaignStream.ReadToEnd());
        readCampaignStream.Close();
        receiveCampaignStream.Close();
        DisplayResponse(newCampaignData, newCampaignResponse);

        if (newCampaignResponse.StatusCode == HttpStatusCode.Created)
        {
            NewCampaignID = newCampaignData.id;
            Console.WriteLine("Created new campaign...");
            Console.WriteLine("New Campaign Id    : " + NewCampaignID + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to create birthday campaign.\n");
            return;
        }


9. Die Kampagne muss aktiviert werden, bevor sie per Push an Geräte übertragen werden kann. Die ID für die neue Kampagne wird in der `NewCampaignID`-Variablen gespeichert. Wir verwenden sie als URI-Pfadparameter, um die Kampagne mit der REST-API [Kampagne aktivieren](https://msdn.microsoft.com/library/azure/mt683745.aspx) zu aktivieren. Dadurch wird der Zustand der Kampagne in **Geplant** geändert, obwohl sie nur manuell mit den APIs per Push übertragen wird.

	Fügen Sie Ihrer `Main`-Methode den folgenden Code hinzu, um die Ankündigungskampagne zu aktivieren:

        //******************************************
        //*** Activate the new birthday campaign ***
        //******************************************

        String activateCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID +
                   "/activate?api-version=2014-12-01";

        Console.WriteLine("Activating new campaign (ID : " + NewCampaignID + ")...\n");
        HttpWebResponse activateCampaignResponse = ExecuteREST("POST", activateCampaignUrl, Token).Result;
        Stream activateCampaignStream = activateCampaignResponse.GetResponseStream();
        StreamReader activateCampaignReader = new StreamReader(activateCampaignStream, Encoding.UTF8);
        dynamic activateCampaignData = JObject.Parse(activateCampaignReader.ReadToEnd());
        activateCampaignReader.Close();
        activateCampaignStream.Close();
        DisplayResponse(activateCampaignData, activateCampaignResponse);

        if (activateCampaignResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Activated new campaign...");
            Console.WriteLine("New Campaign State : " + activateCampaignData.state + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to activate birthday campaign.\n");
            return;
        }

10. Um die Kampagne per Push zu übertragen, müssen wir die Geräte-IDs für die Benutzer bereitstellen, die die Benachrichtigung erhalten sollen. Wir verwenden die REST-API [Geräte abfragen](https://msdn.microsoft.com/library/azure/mt683826.aspx), um alle Geräte-IDs abzurufen. Wir fügen die einzelnen Geräte-IDs der Liste hinzu, wenn ihnen die appInfo **user\_name** zugeordnet ist.

	Fügen Sie der `Main`-Methode den folgenden Code hinzu, um alle Geräte-IDs abzurufen und die deviceList aufzufüllen:

        //************************************************************************
        //*** Now that the manualPush campaign is activated, get the deviceIds ***
        //*** that you want to trigger the push campaign on.                   ***
        //************************************************************************

        String getDevicesUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/devices?api-version=2014-12-01";

        Console.WriteLine("Getting device IDs...\n");
        HttpWebResponse devicesResponse = ExecuteREST("GET", getDevicesUrl, Token).Result;
        Stream devicesStream = devicesResponse.GetResponseStream();
        StreamReader devicesReader = new StreamReader(devicesStream, Encoding.UTF8);
        dynamic devicesData = JObject.Parse(devicesReader.ReadToEnd());
        devicesReader.Close();
        devicesStream.Close();
        DisplayResponse(devicesData, devicesResponse);

        if (devicesResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Got devices.");
            foreach (dynamic device in devicesData.value)
            {
                // Just adding all in this example
                if (device.appInfo.user_name != null)
                {
                    Console.WriteLine("Adding device for push campaign...");
                    Console.WriteLine("Device ID    : " + device.deviceId);
                    Console.WriteLine("user_name    : " + device.appInfo.user_name);
                    deviceList.Add((String)device.deviceId);
                }
            }
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine("*** Failed to get devices.\n");
			return;
        }


11. Abschließend übertragen wir die Kampagne mithilfe der [Pushkampagne](https://msdn.microsoft.com/library/azure/mt683734.aspx)-REST-API per Push an alle Geräte-IDs in unserer Liste. Dies ist ein **In-App**-Benachrichtigung. Daher muss die App auf dem Gerät ausgeführt werden, damit die Benachrichtigung vom Benutzer empfangen werden kann.

	Fügen Sie der `Main`-Methode den folgenden Code hinzu, um die Kampagne an die Geräte in der deviceList per Push zu übertragen:

        //**************************************************************
        //*** Trigger the manualPush campaign on the desired devices ***
        //**************************************************************

        String pushCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID + 
                   "/push?api-version=2014-12-01";

        Console.WriteLine("Triggering push for new campaign (ID : " + NewCampaignID + ")...\n");
        String deviceIds = "{"deviceIds":" + JsonConvert.SerializeObject(deviceList) + "}";
        Console.WriteLine("\n" + deviceIds + "\n");
        HttpWebResponse pushDevicesResponse = ExecuteREST("POST", pushCampaignUrl, Token, null, deviceIds).Result;
        Stream pushDevicesStream = pushDevicesResponse.GetResponseStream();
        StreamReader pushDevicesReader = new StreamReader(pushDevicesStream, Encoding.UTF8);
        dynamic pushDevicesData = JObject.Parse(pushDevicesReader.ReadToEnd());
        pushDevicesReader.Close();
        pushDevicesStream.Close();
        DisplayResponse(pushDevicesData, pushDevicesResponse);

        if (pushDevicesResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Triggered push on new campaign.\n");
        }
        else
        {
            Console.WriteLine("*** Failed to push campaign to the device list.\n");
        }


12. Erstellen Sie Ihre Konsolen-App, und führen Sie sie aus. Die Ausgabe sollte in etwa wie folgt aussehen:


		C:\Users\Wesley\AzME_Service_API_Rest\test.exe

		Getting authorization token...
		
		HTTP Status 200 : OK
		{
		  "token_type": "Bearer",
		  "expires_in": "3599",
		  "expires_on": "1458085162",
		  "not_before": "1458081262",
		  "resource": "https://management.core.windows.net/",
		  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPW
		b3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFh
		NzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0cy53
		MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoiNzJm
		F5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE3OAE
		hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-HASI8
		}
		
		Got authorization token...
		Authorization : Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNN
		aW5kb3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYt
		Zi1jNzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0
		OGU3MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoi
		iI-oF5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE
		vsf3hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-H
		
		Creating new campaign...
		
		HTTP Status 201 : Created
		{
		  "id": 24,
		  "state": "draft"
		}
		
		Created new campaign...
		New Campaign Id    : 24
		
		Activating new campaign (ID : 24)...
		
		HTTP Status 200 : OK
		{
		  "id": 24,
		  "state": "scheduled"
		}
		
		Activated new campaign...
		New Campaign State : scheduled
		
		Getting device IDs...
		
		HTTP Status 200 : OK
		{
		  "value": [
		    {
		      "meta": {
		        "lastSeen": 1458080534371,
		        "firstSeen": 1458080534371,
		        "lastLocation": 1458081389617,
		        "lastInfo": 1458080571603
		      },
		      "appInfo": {
		        "user_name": "Wesley"
		      },
		      "deviceId": "1d6208b8f281203ecb49431e2e5ce6b3"
		    },
		    {
		      "meta": {
		        "lastSeen": 1457990584698,
		        "firstSeen": 1457949384025,
		        "lastLocation": 1457990914895,
		        "lastInfo": 1457990584597
		      },
		      "appInfo": {
		        "user_name": "Wesley"
		      },
		      "deviceId": "302486644890e26045884ee5aa0619ec"
		    }
		  ]
		}
		
		Got devices.
		Adding device for push campaign...
		Device ID    : 1d6208b8f281203ecb49431e2e5ce6b3
		user_name    : Wesley
		Adding device for push campaign...
		Device ID    : 302486644890e26045884ee5aa0619ec
		user_name    : Wesley
		
		Triggering push for new campaign (ID : 24)...
		
		
		{"deviceIds":["1d6208b8f281203ecb49431e2e5ce6b3","302486644890e26045884ee5aa0619ec"]}
		
		HTTP Status 200 : OK
		{
		  "invalidDeviceIds": []
		}
		
		Triggered push on new campaign.
		


<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png

<!---HONumber=AcomDC_0323_2016-->