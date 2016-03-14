<properties 
	pageTitle="Azure Mobile Engagement – Back-End-Integration" 
	description="Verbinden Sie Azure Mobile Engagement mit einem SharePoint-Back-End, um in SharePoint Kampagnen zu erstellen." 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

# Azure Mobile Engagement – API-Integration

In einem automatisierten Marketingsystem erfolgen das Erstellen und Aktivieren der Marketingkampagnen ebenfalls automatisch. Zu diesem Zweck ermöglicht Azure Mobile Engagement auch das Erstellen solcher automatisierten Marketingkampagnen mithilfe von APIs.

Kunden verwenden in der Regel die Mobile Engagement-Front-End-Schnittstelle, um im Rahmen ihrer Marketingkampagnen Ankündigungen/Umfragen u. ä. zu erstellen. Sobald jedoch die Marketingkampagnen ausgereift sind, müssen die in den Back-End-Systemen (z. B. ein CRM-System oder ein CMS-System wie SharePoint) blockierten Daten genutzt werden. So kann eine vollständig automatische Pipeline erstellt werden, die anhand der Daten aus den Back-End-Systemen dynamisch Kampagnen in Mobile Engagement erzeugt.

![][5]

Dieses Lernprogramm behandelt ein Szenario, in dem ein geschäftlicher SharePoint-Benutzer eine SharePoint-Liste mit Marketingdaten füllt. Durch einen automatisierten Prozess werden Elemente aus der Liste ausgewählt und mithilfe der verfügbaren REST-APIS mit dem Mobile Engagement-System verbunden, um aus den SharePoint-Daten eine Marketingkampagne zu erstellen.
 
> [AZURE.IMPORTANT] Dieses Beispiel veranschaulicht das allgemeine Verfahren zum Aufrufen jeder Mobile Engagement-REST-API, da es die beiden Hauptaspekte des Aufrufs der APIs zeigt – Authentifizierung und Übergeben von Parametern.

## SharePoint-Integration
1. Die SharePoint-Beispielliste sieht wie folgt aus. Zum Erstellen der Ankündigung werden **Title**, **Category**, **NotificationTitle**, **Message** und **URL** verwendet. Die Spalte **IsProcessed** wird von dem Automatisierungsprozess im Beispiel in Form eines Konsolenprogramms verwendet. Sie können das Konsolenprogramm als Azure WebJob ausführen, um es nach Zeitplan auszuführen. Alternativ können Sie direkt mithilfe des SharePoint-Workflows ein Programm erstellen, mit dem die Ankündigung erstellt und aktiviert wird, wenn ein Element in die SharePoint-Liste eingefügt wird. In diesem Beispiel verwenden wir das Konsolenprogramm. Dieses durchläuft die Elemente in der SharePoint-Liste, erstellt in Azure Mobile Engagement die Ankündigung für jedes Element und markiert schließlich bei erfolgreicher Erstellung der Ankündigung das **IsProcessed**-Flag als true.

	![][1]

2. Für die Authentifizierung mit der SharePoint-Liste verwenden wir den Code aus dem Beispiel *Remoteauthentifizierung in SharePoint Online mit dem Clientobjektmodell*. Dieses finden Sie [hier](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c).
 
3. Nach der Authentifizierung werden die Listenelemente in einer Schleife durchlaufen, um neu erstellte Elemente zu ermitteln (für diese ist **IsProcessed** = false).

 		static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);

                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();

                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;

                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;

                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";

                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## Mobile Engagement-Integration
1.  Sobald ein Element gefunden wird, das verarbeitet werden muss, werden die zum Erstellen einer Ankündigung erforderlichen Informationen aus dem Listenelement extrahiert. `CreateAzMECampaign` wird aufgerufen, um die Ankündigung zu erstellen, und anschließend wird `ActivateAzMECampaign` aufgerufen, um sie zu aktivieren. Diese sind im Wesentlichen REST-API-Aufrufe des Mobile Engagement Back-Ends. 

2.  Die Mobile Engagement-REST-APIs erfordern einen **HTTP-Header für die Autorisierung des Basisauthentifizierungsschemas **. Dieser besteht aus der `ApplicationId` und dem `ApiKey`, die Sie im Azure-Portal erhalten. Stellen Sie sicher, dass Sie den Schlüssel aus dem Bereich **API-Schlüssel** und *nicht* aus dem Bereich **SDK-Schlüssel** verwenden.

	![][2]

        static string CreateAuthZHeader()
        {
            string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
            return BasicAuthzHeaderString;
        }

        static public string EncodeTo64(string toEncode)
        {
            byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
            string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
            return returnValue;
        }  

3. Informationen zum Erstellen der Kampagne vom Typ Ankündigung finden Sie in der [Dokumentation](https://msdn.microsoft.com/library/azure/mt683750.aspx). Sie müssen als `kind` der Kampagne *Ankündigung* angeben sowie die [Nutzlast](https://msdn.microsoft.com/library/azure/mt683751.aspx) angeben und diese als FormUrlEncodedContent übergeben.

		static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";

            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
                
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
                
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });

                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }

4. Nachdem Sie die Ankündigung erstellt haben, wird im Mobile Engagement-Portal Folgendes angezeigt (beachten Sie, dass "Status" = "Entwurf" und "Aktiviert" = "N/V" lautet):

	![][3]

5. `CreateAzMECampaign` erstellt eine Ankündigungskampagne und gibt ihre ID an den Aufrufer zurück. `ActivateAzMECampaign` benötigt zum Aktivieren der Kampagne diese ID als Parameter.

		static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());

                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });

                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }

6. Nachdem Sie die Ankündigung aktiviert haben, wird im Mobile Engagement-Portal Folgendes angezeigt:

	![][4]

7. Sobald die Kampagne aktiviert ist, werden auf allen Geräten, die das Kriterium für die Kampagne erfüllen, Benachrichtigungen angezeigt.

8. Sie werden außerdem feststellen, dass das mit IsProcessed = false markierte Listenelement nach dem Erstellen der Ankündigungskampagne auf true festgelegt wurde.

In diesem Beispiel wurde eine einfache Ankündigungskampagne erstellt, und es wurden im Wesentlichen die erforderlichen Eigenschaften angegeben. Mithilfe der Informationen [hier](https://msdn.microsoft.com/library/azure/mt683751.aspx) können Sie dieses Beispiel im Portal nach Bedarf anpassen.

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png


 

<!---HONumber=AcomDC_0302_2016-->