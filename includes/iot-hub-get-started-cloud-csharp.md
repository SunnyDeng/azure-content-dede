## Erstellen einer Geräteidentität

In diesem Abschnitt schreiben Sie eine Windows-Konsolen-App, die eine neue Geräteidentität in IoT Hub erstellt. Weitere Informationen finden Sie im Abschnitt **Geräteidentitätsregistrierung** des [Entwicklungsleitfadens für IoT Hub][IoT Hub Developer Guide - Identity Registry]. Nachdem diese Konsolenanwendung ausgeführt wurde, verfügen Sie über eine ID und einen Schlüssel, die Sie als Geräteidentität zum Senden von Gerät-zu-Cloud-Nachrichten an IoT Hub verwenden können.

1. Erstellen Sie in Visual Studio mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Desktopanwendungsprojekt. Nennen Sie das Projekt **CreateDeviceIdentity**.

	![][10]

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten...**

	Das Fenster **NuGet-Paket-Manager** wird angezeigt.

3. Stellen Sie sicher, dass die Option **Vorversion einschließen** aktiviert ist. Suchen Sie dann nach `Microsoft Azure Devices`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

	![][11]

4. Daraufhin wird das [NuGet-Paket mit dem Geräte-SDK für Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) heruntergeladen und installiert und dem Projekt ein Verweis auf das Paket hinzugefügt.

4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Fügen Sie der **Program**-Klasse die folgenden Felder hinzu, und ersetzen Sie dabei die Platzhalterwerte durch den Namen des im vorherigen Abschnitt erstellten IoT Hubs und dessen Verbindungszeichenfolge:

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. Fügen Sie der **Program**-Klasse die folgende Methode hinzu:

		private async static Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

	Diese Methode erstellt eine neue Geräteidentität mit der ID **myFirstDevice** (falls bereits eine Identität mit der gleichen ID vorhanden ist, wird sie einfach abgerufen). Anschließend zeigt die App den Primärschlüssel für diese Identität an. Dieser Schlüssel wird vom simulierten Gerät zum Herstellen einer Verbindung mit IoT Hub verwendet.

7. Fügen Sie abschließend der **Main**-Methode die folgenden Zeilen hinzu:

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Führen Sie diese Anwendung aus, und notieren Sie den Geräteschlüssel.

    ![][12]

> [AZURE.NOTE]Es sollte beachtet werden, dass die IoT Hub-Identitätsregistrierung nur zum Speichern von Geräteidentitäten für den sicheren Zugriff verwendet wird, also zum Speichern von Sicherheitsanmeldeinformationen und Aktivieren/Deaktivieren des Zugriffs einzelner Geräte. Metadaten der Geräteanwendung sollten in einem anwendungsspezifischen Speicher gespeichert werden. Weitere Informationen finden Sie im [IoT Hub-Entwicklerhandbuch][IoT Hub Developer Guide - Identity Registry].

## Empfangen von Gerät-zu-Cloud-Nachrichten

In diesem Abschnitt erstellen Sie eine Windows-Konsolen-App, die Gerät-zu-Cloud-Nachrichten von IoT Hub liest. IoT Hub macht einen mit [Event Hubs][Event Hubs Overview] kompatiblen Endpunkt zum Lesen von D2C-Nachrichten verfügbar. Der Einfachheit halber wird in diesem Lernprogramms ein vereinfachter Leser erstellt und verwendet, der für Bereitstellungen mit hohem Durchsatz nicht geeignet ist. Weitere Informationen zum Verarbeiten von IoT Hub-Gerät-zu-Cloud-Nachrichten finden Sie im Lernprogramm [Verarbeiten von Gerät-zu-Cloud-Nachrichten]. Weitere Informationen zum Verarbeiten von Nachrichten von Event Hubs finden Sie im Tutorial [Erste Schritte mit Event Hubs].

1. Klicken Sie in der aktuellen Visual Studio-Projektmappe auf **Datei -> Hinzufügen -> Projekt**, um mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Desktopanwendungsprojekt zu erstellen. Nennen Sie das Projekt **ReadDeviceToCloudMessages**.

    ![][10]

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe und anschließend auf **NuGet-Pakete verwalten**.

    Das Dialogfeld **NuGet-Pakete verwalten** wird geöffnet.

3. Suchen Sie nach `WindowsAzure.ServiceBus`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

    Daraufhin wird [Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) mit allen Abhängigkeiten heruntergeladen, installiert und mit einem Verweis versehen.

4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

        using Microsoft.ServiceBus.Messaging;

5. Fügen Sie der **Program**-Klasse die folgenden Felder hinzu, und ersetzen Sie dabei die Platzhalterwerte durch den Namen des im vorherigen Abschnitt erstellten IoT Hubs und dessen Verbindungszeichenfolge:

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Fügen Sie der **Program**-Klasse die folgende Methode hinzu:

        private async static Task ReceiveMessagesFromDeviceAsync(string partition)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.Now);
            while (true)
            {
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine(string.Format("Message received. Partition: {0} Data: '{1}'", partition, data));
            }
        }

    Diese Methode verwendet einen EventHub-Client für den Empfang von allen Gerät-zu-Cloud-Empfangspartitionen von IoT Hub. Beachten Sie, wie beim Erstellen von EventHubReceiver ein `DateTime.Now`-Parameter übergeben wird. Dadurch wird ein Empfänger erstellt, der nur Nachrichten empfängt, die nach dessen Start gesendet werden.

7. Fügen Sie abschließend der **Main**-Methode die folgenden Zeilen hinzu:

        Console.WriteLine("Receive messages\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        foreach (string partition in d2cPartitions)
        {
           ReceiveMessagesFromDeviceAsync(partition);
        }  
        Console.ReadLine();


<!-- Links -->

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Erste Schritte mit Event Hubs]: event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Event Hubs Overview]: event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Azure Storage account]: storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Azure Preview Portal]: https://portal.azure.com/


<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=Oct15_HO3-->