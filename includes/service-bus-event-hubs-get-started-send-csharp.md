## Senden von Nachrichten an Ereignis-Hubs
In diesem Abschnitt schreiben wir eine Windows-Konsolenanwendung, um Ereignisse an den Ereignis-Hub zu senden.

1. Erstellen Sie in Visual Studio mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Desktopanwendungsprojekt. Geben Sie dem Projekt den Namen **Sender**.

   	![][7]

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten...**. 

	Daraufhin wird das Dialogfeld "NuGet-Pakete verwalten" angezeigt.

3. Suchen Sie nach `Microsoft Azure Service Bus`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen. 

	![][8]

	Daraufhin wird das <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">NuGet-Paket Azure Service Bus library</a> mit allen Abhängigkeiten heruntergeladen und installiert und dem Projekt ein Verweis auf das Paket hinzugefügt.

4. Fügen Sie die folgende `using`-Anweisung am Anfang der Datei **Program.cs** hinzu:

		using Microsoft.ServiceBus.Messaging;

5. Fügen Sie die folgenden `static`-Felder der **Program**-Klasse hinzu, wobei Sie die Werte durch den Namen des im vorigen Abschnitt erstellten Ereignis-Hubs und die Verbindungszeichenfolge durch **send**-Rechte ersetzen:

		static string eventHubName = "{event hub name}";
        static string connectionString = "{send connection string}";

6. Fügen Sie die folgende Methode der **Program**-Klasse hinzu:

		static async Task SendingRandomMessages()
        {
            var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
            while (true)
            {
                try
                {
                    var message = Guid.NewGuid().ToString();
                    Console.WriteLine("{0} > Sending message: {1}", DateTime.Now.ToString(), message);
                    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                }
                catch (Exception exception)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine("{0} > Exception: {1}", DateTime.Now.ToString(), exception.Message);
                    Console.ResetColor();
                }

                await Task.Delay(200);
            }
        }

	Diese Methode sendet kontinuierlich Ereignisse mit einer Verzögerung von 200 ms an den Ereignis-Hub.

7. Fügen Sie abschließend die folgenden Zeilen der **Main**-Methode hinzu:

		Console.WriteLine("Press Ctrl-C to stop the sender process");
        Console.WriteLine("Press Enter to start now");
        Console.ReadLine();
        SendingRandomMessages().Wait();


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png
