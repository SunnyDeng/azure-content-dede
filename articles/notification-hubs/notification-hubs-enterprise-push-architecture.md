<properties 
	pageTitle="Benachrichtigungshubs - Pusharchitektur für Unternehmen" 
	description="Anleitung zur Verwendung von Azure Notification Hubs (Benachrichtigungshubs) in einer Unternehmensumgebung" 
	services="notification-hubs" 
	documentationCenter="" 
	authors="piyushjo" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/15/2015" 
	ms.author="piyushjo"/>

# Anleitung für eine unternehmensbezogene Pusharchitektur

Unternehmen gehen mehr und mehr dazu über, mobile Anwendungen entweder für ihre Endbenutzer (extern) oder für ihre Mitarbeiter (intern) zu erstellen. Sie haben vorhandene Back-End-Systeme, seien es Mainframes oder einige Branchenanwendungen, die in die Architektur der mobilen Anwendungen integriert werden müssen. In dieser Anleitung wird gezeigt, wie sich diese Integration am besten umsetzen lässt, wozu mögliche Lösungen für allgemeine Szenarien vorgeschlagen werden. 

Eine häufige Anforderung besteht darin, Pushbenachrichtigung an die Benutzer über deren mobile Anwendung zu senden, wenn in den Back-End-Systemen ein interessierendes Ereignis auftritt. Beispielsweise möchte eine Bankkundin, die die Banking-App ihrer Bank auf ihrem iPhone hat, benachrichtigt werden, wenn ihr Konto mit einem Betrag belastet wird, der über einem bestimmten Betrag liegt. Oder es gibt ein Intranetszenario, in dem ein Mitarbeiter aus der Finanzabteilung, der auf seinem Windows Phone eine App zur Budgetgenehmigung hat, benachrichtigt werden möchte, wenn er eine Genehmigungsanforderung erhält.
 
Die Bankkonto- oder Genehmigungsverarbeitung wird wahrscheinlich in einem Back-End-System vorgenommen, das ein Pushbenachrichtigung an den Benutzer auslösen muss. Möglicherweise gibt es mehrere solcher Back-End-Systeme, für die Pushbenachrichtigungen gemäß derselben Logik implementiert sein müssen für den Fall, dass ein Ereignis eine Benachrichtigung auslöst. Die Komplexität liegt hier darin, mehrere Back-End-Systeme in ein einzelnes Pushsystem zu integrieren, bei dem die Endbenutzer möglicherweise unterschiedliche Benachrichtigungen abonniert haben. Und möglicherweise gibt es sogar mehrere mobile Anwendungen, z. B. bei mobilen Intranet-Apps, von denen eine mobile Anwendung Benachrichtigungen von mehreren solcher Back-End-Systeme empfangen möchte. Die Back-End-Systeme wissen üblicherweise nichts über die Semantik/Technologie von Pushbenachrichtigungen, also besteht eine herkömmliche allgemeine Lösung hierfür darin, eine Komponente bereitzustellen, die die Back-End-Systeme ständig auf alle interessierenden Ereignisse abfragt und dafür verantwortlich ist, die Pushnachrichten an die Clients zu senden.
Hier wird eine noch bessere Lösung vorgestellt, für die das Modell "Azure Service Bus - Thema/Abonnement" verwendet wird, durch das sich die Komplexität verringern und gleichzeitig die Lösung skalierbar gestalten lässt. 

Die allgemeine Architektur der Lösung (verallgemeinert mit mehreren mobilen Apps, jedoch gleichermaßen anwendbar, wenn es nur eine mobile App gibt) sieht wie folgt aus.

## Architektur

![][1]

Das Schlüsselelement in dieser Architekturabbildung ist der Dienst Azure Service Bus, der ein Themen/Abonnements-Programmiermodell bereitstellt (weitere Informationen hierzu finden Sie unter [Verwenden von Service Bus-Themen und -Abonnements]). Der Empfänger, der in diesem Fall der Mobil-Back-End ist (üblicherweise [Azure Mobile Service], der eine Pushbenachrichtigung an die mobile Apps auslöst), empfängt Nachrichten nicht direkt von den Back-End-Systemen, sondern von einer zwischenliegenden Abstraktionsebene, die von [Azure Service Bus] bereitgestellt wird und es Mobil-Back-End-Systemen ermöglicht, Nachrichten von einem oder mehreren Back-End-Systemen zu empfangen. Ein Service Bus-Thema muss für jedes der Back-End-Systeme, z. B. Abrechnung, Personal, Finanzen, erstellt werden, die im Prinzip "Themen" von Interesse sind, die bewirken, dass Nachrichten als Pushbenachrichtigungen gesendet werden. Die Back-End-Systeme senden Nachrichten an diese Themen. Ein Mobil-Back-End kann eines oder mehrere solcher Themen durch Erstellen eines Service Bus-Abonnements abonnieren. Dadurch erhält das Mobil-Back-End die Berechtigung, eine Benachrichtigung vom entsprechenden Back-End-System zu empfangen. Das Mobil-Back-End lauscht weiterhin auf Nachrichten in seinen Abonnements, und sobald eine Nachricht eingetroffen ist, sendet es diese als Benachrichtigung an seinen Benachrichtigungshub. Der Benachrichtigungshub sendet die Nachricht dann schließlich an die mobile App. Zusammengefasst liegen folgende Schlüsselkomponenten vor:

1. Back-End-System (Branchen- oder Legacysystem)
	- Erstellt Service Bus-Themen
	- Sendet Nachrichten
2. Mobil-Back-End
	- Erstellt dienstbezogene Abonnements
	- Empfängt Nachrichten (von Back-End-System)
	- Sendet Benachrichtigungen an Clients (über Azure-Benachrichtigungshub)
3. Mobile Anwendung
	- Empfängt Benachrichtigungen und zeigt diese an
		
###Vorteile:

1. Die Entkopplung von Empfänger (mobile App/mobiler Dienst über Benachrichtigunghub) und Sender (Back-End-Systeme) ermöglicht es, zusätzliche Back-End-Systeme bei minimalen Änderungen zu integrieren.
2. Hiermit lassen sich auch Szenarios umsetzen, in denen mehrere mobile Apps in der Lage sind, Ereignisse von mehreren Back-End-Systemen zu empfangen.  

## Beispiel:

###Voraussetzungen
Sie sollten die folgenden Lernprogramme durcharbeiten, um sich mit den Konzepten sowie den allgemeinen Erstellungs- und Konfigurationsschritten vertraut zu machen:

1. [Verwenden von Service Bus-Themen und -Abonnements] - Es werden die Details des Arbeitens mit Service Bus-Themen/Abonnements erläutert, und es wird erläutert, wie ein Namespace erstellt wird, der Themen/Abonnements enthalten soll, und wie Nachrichten von diesen gesendet bzw. empfangen werden.
2. [Erste Schritte mit Notification Hubs] - Hier wird erläutert, wie eine Windows Store-App eingerichtet und Benachrichtigungshubs (Notification Hubs) verwendet werden, um sich zu registrieren und dann Benachrichtigungen zu empfangen.

###Beispielcode

Der vollständige Beispielcode ist unter [Notification Hubs Samples][Notification Hub - Beispiele] verfügbar. Der Code ist in drei Komponenten aufgeteilt:

1. **EnterprisePushBackendSystem**
	
	a. In diesem Projekt, das auf [Verwenden von Service Bus-Themen und -Abonnements] basiert, wird das *WindowsAzure.ServiceBus*-Nuget-Paket verwendet. 

	b. Das Paket enthält eine einfache C#-Konsolenanwendung, um ein Branchensystem (LoB-System) zu simulieren, das ein Senden der Nachricht an die mobile APp veranlasst. 
	
		static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the topic where we will send notifications
            CreateTopic(connectionString);

            // Send message
            SendMessage(connectionString);
        }
	
	c. `CreateTopic` wird verwendet, um das Service Bus-Thema zu erstellen, in dem Nachrichten gesendet werden.

        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already

            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }

	d. `SendMessage` wird verwendet, um die Nachrichten an dieses Service Bus-Thema zu senden. Für das Beispiel wird hier einfach ein Satz von zufälligen Nachrichten regelmäßig an das Thema gesendet. Normalerweise gäbe es ein Back-End-System, das Nachrichten senden würde, wenn ein Ereignis aufgetreten ist. 

        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

            // Sends random messages every 10 seconds to the topic
            string[] messages = 
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };

            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);

                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }

2. **ReceiveAndSendNotification**

	a. In diesem Projekt, das auf [Verwenden von Service Bus-Themen und -Abonnements] basiert, werden die Nuget-Pakete *WindowsAzure.ServiceBus* und *Microsoft.Web.WebJobs.Publish* verwendet. 

	b. Dies ist eine weitere C#-Konsolenanwendung, die als ein [Azure WebJob] ausgeführt wird, weil sie kontinuierlich ausgeführt werden muss, um auf Nachrichten von den Branchen- bzw. Back-End-Systemen zu lauschen. Das Projekt ist Bestandteil Ihres Mobil-Back-Ends. 

	    static void Main(string[] args)
	    {
	        string connectionString =
	                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
	
	        // Create the subscription which will receive messages
	        CreateSubscription(connectionString);   
	
	        // Receive message
	        ReceiveMessageAndSendNotification(connectionString);
	    }

	c. `CreateSubscription` wird verwendet, um ein Service Bus-Abonnement für das Thema zu erstellen, in dem das Back-End-System Nachrichten sendet. Abhängig von dem Geschäftsszenario erstellt diese Komponente Abonnements für die entsprechenden Themen (z. B. empfangen einige Nachrichten vom Personalsystem, einige vom Finanzsystem usw.).

	    static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }

	d. "ReceiveMessageAndSendNotification" wird verwendet, um die Nachricht über sein Abonnement aus dem Thema zu lesen und, wenn der Lesevorgang erfolgreich war, eine Benachrichtigung zu erstellen (im Beispielszenario eine systemeigene Windows-Popupbenachrichtigung), die über Azure Notification Hubs an die mobile Anwendung gesendet werden soll. 

		static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");
            
            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);

            Client.Receive();

            // Continuously process messages received from the subscription 
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");

                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);

                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            } 
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }

	e. Um dieses Projekt als einen **WebJob** zu veröffentlichen, klicken Sie in Visual Studio mit der rechten Maustaste auf die Projektmappe, und wählen Sie **Als Azure WebJob veröffentlichen** aus.

	![][2]

	f. Wählen Sie Ihr Veröffentlichungsprofil aus, und erstellen Sie eine neue Azure-Website, sofern noch keine vorhanden ist, die diesen WebJob hostst. Sobald die Website vorhanden ist, klicken Sie auf **Veröffentlichen**. 
	
	![][3]

	g. Konfigurieren Sie den WebJob mit "Dauerhaft ausführen", sodass in etwa Folgendes angezeigt wird, wenn Sie sich beim Azure-Verwaltungsportal angemeldet haben:

	![][4]


3. **EnterprisePushMobileApp**

	a. Dies ist eine Windows Store-Anwendung, die Popupbenachrichtigungen von dem WebJob empfängt, der als Bestandteil Ihres Mobil-Back-Ends ausgeführt wird, und diese Benachrichtigungen anzeigt. Diese Anwendung basiert auf [Erste Schritte mit Notification Hubs].  
	
	b. Stellen Sie sicher, dass Ihre Anwendung so konfiguriert ist, dass sie Popupbenachrichtigungen empfangen kann. 

	c. Stellen Sie sicher, dass der folgende Notification Hub-Registrierungscode beim App-Start aufgerufen wird (nachdem *HubName* und *DefaultListenSharedAccessSignature*) ersetzt wurden:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### Ausführen des Beispiels:

1. Vergewissern Sie sich, dass Ihr WebJob erfolgreich ausgeführt wird und als "Dauerhaft ausführen" geplant ist. 
2. Führen Sie die **EnterprisePushMobileApp** aus, von der die Windows Store-App gestartet wird. 
3. Führen Sie die Konsolenanwendung **EnterprisePushBackendSystem** aus, die das Branchen-Back-End startet und damit beginnt, Nachrichten zu senden. Es sollten Popupbenachrichtigungen angezeigt werden, die wie die folgenden aussehen: 

	![][5]

4. Die Nachrichten wurden ursprünglich an Service Bus-Themen gesendet, die von Service Bus-Abonnements in Ihrem Webauftrag (WebJob) überwacht wurden. Sobald eine Nachricht empfangen wurde, wurde eine Benachrichtigung erstellt und an die mobile App gesendet. Sie können die WebJob-Protokolle durchsuchen, um die Verarbeitung zu bestätigen. Navigieren Sie dazu im Azure-Verwaltungsportal für Ihren Webauftrag zum Link "Protokolle":

	![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Notification Hub - Beispiele]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Service]: http://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Verwenden von Service Bus-Themen und -Abonnements]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/
[Erste Schritte mit Notification Hubs]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/

<!--HONumber=49--> 