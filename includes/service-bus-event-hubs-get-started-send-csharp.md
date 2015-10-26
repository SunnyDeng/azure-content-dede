## Senden von Nachrichten an Ereignis-Hubs

In diesem Abschnitt schreiben wir eine Windows-Konsolenanwendung, die Ereignisse an den Ereignis-Hub sendet.

1. Erstellen Sie in Visual Studio mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Desktopanwendungsprojekt. Geben Sie dem Projekt den Namen **Sender**.

   	![][7]

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten...**

	Daraufhin wird das Dialogfeld "NuGet-Pakete verwalten" angezeigt.

3. Suchen Sie nach `Microsoft Azure Service Bus`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

	![][8]

	Daraufhin wird das NuGet-Paket <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">Azure Service Bus</a> mit allen Abhängigkeiten heruntergeladen und installiert und dem Projekt ein Verweis auf das Paket hinzugefügt.

4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

		using System.Threading;
		using Microsoft.ServiceBus.Messaging;

5. Fügen Sie der **Program**-Klasse die folgenden Felder hinzu, und ersetzen Sie dabei die Platzhalterwerte durch den Namen des im vorigen Abschnitt erstellten Event Hubs und eine Verbindungszeichenfolge mit **Send**-Rechten.

		static string eventHubName = "{event hub name}";
        static string connectionString = "{send connection string}";

6. Fügen Sie der **Program**-Klasse die folgende Methode hinzu:

		static void SendingRandomMessages()
        {
            var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
            while (true)
            {
                try
                {
                    var message = Guid.NewGuid().ToString();
                    Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                    eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
                }
                catch (Exception exception)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                    Console.ResetColor();
                }

                Thread.Sleep(200);
            }
        }

	Diese Methode sendet kontinuierlich Ereignisse mit einer Verzögerung von 200 ms an den Ereignis-Hub.

7. Fügen Sie abschließend der **Main**-Methode die folgenden Zeilen hinzu:

		Console.WriteLine("Press Ctrl-C to stop the sender process");
        Console.WriteLine("Press Enter to start now");
        Console.ReadLine();
        SendingRandomMessages();


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png

<!---HONumber=Oct15_HO3-->