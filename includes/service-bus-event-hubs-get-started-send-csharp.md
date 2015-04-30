## Senden von Nachrichten an Ereignis-Hubs
In diesem Abschnitt schreiben wir eine Windows-Konsolenanwendung, die Ereignisse an den Ereignis-Hub sendet.

1. Erstellen Sie in Visual Studio mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Desktopanwendungsprojekt. Geben Sie dem Projekt den Namen **Sender**.

   	![][7]

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten...**. 

	Daraufhin wird das Dialogfeld "NuGet-Pakete verwalten" angezeigt.

3. Suchen Sie nach `Microsoft Azure Service Bus`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen. 

	![][8]

	Daraufhin wird das <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">NuGet-Paket "Azure Service Bus library"</a> mit allen Abhängigkeiten heruntergeladen und installiert und dem Projekt ein Verweis auf das Paket hinzugefügt.

4. Fügen Sie die folgenden `using`-Anweisungen am Anfang der Datei **RegisterClient.cs** hinzu:

		using Microsoft.ServiceBus.Messaging;

5. Fügen Sie die folgenden-Felder der **Program**-Klasse hinzu, wobei Sie die Platzhalterwerte durch den Namen des im vorigen Abschnitt erstellten Ereignis-Hubs und die Verbindungszeichenfolge mit **Senden**-Rechten ersetzen:

		static string eventHubName = "{Ereignis-Hub-name}";
        static string connectionString = "{Verbindungszeichenfolge zum Senden}";

6. Fügen Sie die folgende Methode der **Program**-Klasse hinzu:

		static async Task SendingRandomMessages()
        {
            var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
            while (true)
            {
                try
                {
                    var message = Guid.NewGuid().ToString();
                    Console.WriteLine("{0} > Sende Nachricht: {1}", DateTime.Now.ToString(), message);
                    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                }
                catch (Exception exception)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine("{0} > Ausnahme: {1}", DateTime.Now.ToString(), exception.Message);
                    Console.ResetColor();
                }

                await Task.Delay(200);
            }
        }

	Diese Methode sendet kontinuierlich Ereignisse mit einer Verzögerung von 200 ms an den Ereignis-Hub.

7. Fügen Sie abschließend die folgenden Zeilen der **Main**-Methode hinzu:

		Console.WriteLine("Drücken Sie STRG+C, um das Senden abzubrechen.");
        Console.WriteLine("Drücken Sie zum Starten die EINGABETASTE.");
        Console.ReadLine();
        SendingRandomMessages().Wait();


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png

<!--HONumber=52--> 
