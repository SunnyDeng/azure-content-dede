## Erstellen einer simulierten Geräte-App

In diesem Abschnitt schreiben Sie eine Windows-Konsolenanwendung, die simuliert, wie ein Gerät Gerät-an-Cloud-Nachrichten an einen IoT-Hub sendet.

1. Klicken Sie in der aktuellen Visual Studio-Lösung auf **Datei -> Hinzufügen -> Projekt**, um mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Desktopanwendungsprojekt zu erstellen. Nennen Sie das Projekt **SimulatedDevice**.

   	![][30]

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten...**

	Daraufhin wird das Fenster „NuGet-Pakete verwalten“ angezeigt.

3. Suchen Sie nach `Microsoft Azure Devices Client`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

	Daraufhin wird das [NuGet-Paket mit dem Geräte-SDK für Azure IoT] heruntergeladen und installiert und dem Projekt ein Verweis auf das Paket hinzugefügt.

4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. Fügen Sie die folgenden Felder zur **Programm**-Klasse hinzu, wobei die Platzhalterwerte durch den IoT Hub-URI und den Geräteschlüssel ersetzt werden, die in den Abschnitten **Erstellen eines IoT Hubs** beziehungsweise **Erstellen einer Geräteidentität** abgerufen werden:

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub URI}";
        static string deviceKey = "{deviceKey}";

6. Fügen Sie der **Program**-Klasse die folgende Methode hinzu:

		private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Thread.Sleep(1000);
            }
        }

	Diese Methode sendet jede Sekunde eine neue Gerät-an-Cloud-Nachricht und enthält ein JSON-serialisiertes Objekt mit der Geräte-ID und eine zufällig generierte Zahl, die einen simulierte Windgeschwindigkeitssensor darstellt.

7. Fügen Sie abschließend der **Main**-Methode die folgenden Zeilen hinzu:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

> [AZURE.NOTE]Der Einfachheit halber wird in diesem Lernprogramm keine Wiederholungsrichtlinie implementiert. Es wird empfohlen, im Produktionscode Wiederholungsrichtlinien zu implementieren (z. B. einen exponentiellen Backoff), wie im MSDN-Artikel [Transient Fault Handling] (auf Englisch) beschrieben.

<!-- Links -->

[NuGet-Paket mit dem Geräte-SDK für Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[Transient Fault Handling]: https://msdn.microsoft.com/de-DE/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=Oct15_HO3-->