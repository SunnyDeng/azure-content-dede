<properties 
	pageTitle="Zugreifen auf Azure Mobile Engagement-Dienst-APIs mit dem .NET SDK" 
	description="Beschreibt, wie das Mobile Engagement .NET SDK für den Zugriff auf Azure Mobile Engagement-Dienst-APIs verwendet werden kann."		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/01/2016" 
	ms.author="piyushjo" />

#Zugreifen auf Azure Mobile Engagement-Dienst-APIs mit dem .NET SDK

Azure Mobile Engagement stellt eine Reihe von APIs zur Verfügung, mit denen Sie Geräte, Reichweiten-/Pushkampagnen usw. verwalten können. Für die Interaktion mit diesen APIs stellen wir Ihnen auch eine [Swagger-Datei](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) bereit, die Sie mit Tools zum Generieren von SDKs für Ihre bevorzugte Sprache verwenden können. Wir empfehlen die Verwendung des Tools [AutoRest](https://github.com/Azure/AutoRest), um Ihr SDK aus unserer Swagger-Datei zu generieren.

Auf ähnliche Weise haben wir ein .NET SDK erstellt, das Ihnen die Interaktion mit diesen APIs mit einem C#-Wrapper erlaubt, ohne dass Sie das Authentifizierungstoken selbst aushandeln und aktualisieren müssen.

Dieses Beispiel führt die Reihe von Schritten auf, die zum Verwenden des .NET SDK befolgt werden müssen:

1. Sie müssen zunächst mithilfe von Azure Active Directory die Authentifizierung für Ihre APIs einrichten, wie [hier](mobile-engagement-api-authentication.md#authentication) beschrieben. Nach Abschluss dieser Schritte verfügen Sie über gültige Werte für **SubscriptionId**, **TenantId**, **ApplicationId** und **Secret**. 

2. Wir verwenden eine einfache Windows-Konsolen-App, um die Arbeit mit dem .NET SDK anhand des Szenarios zum Erstellen einer Ankündigungskampagne zu demonstrieren. Öffnen Sie daher Visual Studio, und erstellen Sie eine **Konsolenanwendung**.

3. Als Nächstes müssen Sie das .NET SDK herunterladen, das als **Microsoft Azure Engagement Management Library** im Nuget-Katalog [hier](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/) zur Verfügung steht. Wenn Sie Nuget von Visual Studio aus installieren, müssen Sie sicherstellen, dass Sie bei der Suche nach dem Paket das Kontrollkästchen **Vorabversion einbeziehen** aktiviert haben:

	![][1]

4. Fügen Sie in der Datei `Program.cs` die folgenden Namespaces hinzu:

		using Microsoft.Rest.Azure.Authentication;
		using Microsoft.Azure.Management.Engagement;
		using Microsoft.Azure.Management.Engagement.Models;

5. Als Nächstes müssen Sie die folgenden Konstanten definieren, die wir für die Authentifizierung und die Interaktion mit der Mobile Engagement-App verwenden, in der Sie die Ankündigungskampagne erstellen:

        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";

        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/de-DE/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";

        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";

6. Definieren Sie die Variable `EngagementManagementClient`, mit der die Mobile Engagement SDK-Methoden aufgerufen werden sollen:

		static EngagementManagementClient engagementClient; 

7. Fügen Sie der `Main`-Methode Folgendes hinzu:

		try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();

                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }

8. Definieren Sie die folgende Methode, die die Initialisierung des `EngagementManagementClient` übernimmt, indem sie sich zuerst authentifiziert und dann der Mobile Engagement-App zuordnet, in der Sie die Ankündigungskampagne erstellen möchten:

        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
            
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;

            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }

	> [AZURE.IMPORTANT] Beachten Sie, dass Sie den im Azure-Verwaltungsportal für den AppName-Parameter definierten **App-Ressourcennamen** verwenden müssen.

9. Abschließend definieren Sie die CreateCampaign-Methode, die mithilfe des zuvor initialisierten EngagementClient eine einfache **AnyTime** & **Notification-only**-Kampagne mit einem Titel und einer Meldung erstellt:

        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/de-DE/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );

            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/de-DE/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }

10. Führen Sie die Konsolen-App aus. Wenn die Kampagne erfolgreich erstellt wurde, wird Folgendes angezeigt:

	**Campaign Id '159' was created successfully and it is in 'draft' state**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png

<!---HONumber=AcomDC_0302_2016-->