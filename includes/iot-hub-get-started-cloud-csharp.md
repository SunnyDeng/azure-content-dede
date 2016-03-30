## Erstellen einer Geräteidentität

In diesem Abschnitt erstellen Sie eine Windows-Konsolen-App, die eine neue Geräteidentität in der Identitätsregistrierung Ihres IoT Hubs erstellt. Ein Gerät kann nur eine Verbindung mit dem IoT Hub herstellen, wenn in der Geräteidentitätsregistrierung ein Eintrag für dieses Gerät vorhanden ist. Weitere Informationen finden Sie im Abschnitt **Geräteidentitätsregistrierung** des [Entwicklungsleitfadens für IoT Hub][lnk-devguide-identity]. Beim Ausführen dieser Konsolenanwendung werden eine eindeutige Geräte-ID und ein Schlüssel erstellt, mit denen sich das Gerät beim Senden von D2C-Nachrichten (Device-to-Cloud, Gerät-an-Cloud) beim IoT Hub identifizieren kann.

1. Fügen Sie in Visual Studio in der aktuellen Projektmappe mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Projekt für den klassischen Windows-Desktop hinzu. Nennen Sie das Projekt **CreateDeviceIdentity**.

	![][10]

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **CreateDeviceIdentity**, und klicken Sie dann auf **NuGet-Pakete verwalten**.

3. Wählen Sie im Fenster **NuGet-Paket-Manager** die Option **Durchsuchen**, suchen Sie nach **microsoft.azure.devices**, klicken Sie zum Installieren des Pakets **Microsoft.Azure.Devices** auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

	![][11]

4. Daraufhin wird das NuGet-Paket mit dem [Dienst-SDK für Microsoft Azure IoT][lnk-nuget-service-sdk] heruntergeladen, installiert und mit einem Verweis versehen.

4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Fügen Sie der **Program**-Klasse die folgenden Felder hinzu und ersetzen Sie dabei den Platzhalterwert durch die Verbindungszeichenfolge für den im vorherigen Abschnitt erstellten IoT Hub:

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

	Diese Methode erstellt eine neue Geräteidentität mit der ID **myFirstDevice** (wenn diese Geräte-ID bereits in der Registrierung vorhanden ist, ruft der Code einfach die vorhandenen Geräteinformationen ab). Anschließend zeigt die App den Primärschlüssel für diese Identität an. Sie verwenden diesen Schlüssel im simulierten Gerät, um eine Verbindung mit dem IoT Hub herzustellen.

7. Fügen Sie abschließend der **Main**-Methode die folgenden Zeilen hinzu:

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Führen Sie diese Anwendung aus, und notieren Sie den Geräteschlüssel.

    ![][12]

> [AZURE.NOTE] Die Identitätsregistrierung im IoT Hub speichert nur Geräteidentitäten, um einen sicheren Zugriff auf den Hub zu ermöglichen. Sie speichert Geräte-IDs und Schlüssel, die als Sicherheitsanmeldeinformationen verwendet werden, sowie ein Aktiviert/Deaktiviert-Kennzeichen, mit dem Sie den Zugriff für ein einzelnes Gerät deaktivieren können. Wenn Ihre Anwendung verlangt, dass weitere gerätespezifische Metadaten gespeichert werden, sollte dafür ein anwendungsspezifischer Speicher verwendet werden. Weitere Informationen finden Sie im [IoT Hub-Entwicklerhandbuch][lnk-devguide-identity].

## Empfangen von Gerät-an-Cloud-Nachrichten

In diesem Abschnitt erstellen Sie eine Windows-Konsolen-App, die D2C-Nachrichten (Device-to-Cloud) aus dem IoT Hub liest. Ein IoT Hub macht einen [Event Hubs][lnk-event-hubs-overview]-kompatiblen Endpunkt verfügbar, der Ihnen das Lesen von D2C-Nachrichten ermöglicht. Zur Vereinfachung wird in diesem Tutorial ein einfacher Reader erstellt, der für eine Bereitstellung mit hohem Durchsatz nicht geeignet ist. Im Tutorial [Verarbeiten von Gerät-zu-Cloud-Nachrichten][lnk-processd2c-tutorial] wird gezeigt, wie Sie D2C-Nachrichten in größerem Umfang verarbeiten. Das Tutorial [Erste Schritte mit Event Hubs][lnk-eventhubs-tutorial] enthält weitere Informationen zum Verarbeiten der Nachrichten von Event Hubs und gilt für Endpunkte, die mit IoT Hub Event Hubs kompatibel sind.

1. Fügen Sie in Visual Studio in der aktuellen Projektmappe mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Projekt für den klassischen Windows-Desktop hinzu. Nennen Sie das Projekt **ReadDeviceToCloudMessages**.

    ![][10]

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ReadDeviceToCloudMessages**, und klicken Sie dann auf **NuGet-Pakete verwalten**.

3. Suchen Sie im Fenster **NuGet-Paket-Manager** nach **WindowsAzure.ServiceBus**, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

    Daraufhin wird [Azure Service Bus][lnk-servicebus-nuget] mit allen Abhängigkeiten heruntergeladen, installiert und mit einem Verweis versehen.

4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

        using Microsoft.ServiceBus.Messaging;

5. Fügen Sie der **Program**-Klasse die folgenden Felder hinzu, und ersetzen Sie dabei den Platzhalterwert durch die Verbindungszeichenfolge für den IoT-Hub, den Sie im Abschnitt *Erstellen eines IoT Hubs* erstellt haben:

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Fügen Sie der **Program**-Klasse die folgende Methode hinzu:

        private async static Task ReceiveMessagesFromDeviceAsync(string partition)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine(string.Format("Message received. Partition: {0} Data: '{1}'", partition, data));
            }
        }

    Diese Methode verwendet eine **EventHubReceiver**-Instanz, um Nachrichten von allen D2C-Empfangspartitionen des IoT Hubs zu empfangen. Beachten Sie, wie beim Erstellen des **EventHubReceiver**-Objekts ein `DateTime.Now`-Parameter übergeben wird, damit nur die Nachrichten empfangen werden, die nach seinem Start gesendet wurden. Dies ist in einer Testumgebung nützlich, damit Sie den aktuellen Satz an Nachrichten anzeigen können. In einer Produktionsumgebung sollten Sie per Code sicherstellen, dass alle Nachrichten verarbeitet werden. Weitere Informationen finden Sie im Tutorial [Verarbeiten von D2C-Nachrichten mit IoT Hub][lnk-processd2c-tutorial].

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

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=AcomDC_0323_2016-->