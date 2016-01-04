## Erstellen einer simulierten Geräte-App

In diesem Abschnitt erstellen Sie eine Windows-Konsolenanwendung, die ein Gerät simuliert, das D2C-Nachrichten (Device-to-Cloud, Gerät-an-Cloud) an einen IoT Hub sendet.

1. Fügen Sie in Visual Studio in der aktuellen Projektmappe mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Projekt für den klassischen Windows-Desktop hinzu. Nennen Sie das Projekt **SimulatedDevice**.

   	![][30]

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **SimulatedDevice**, und klicken Sie dann auf **NuGet-Pakete verwalten**.

3. Stellen Sie im Fenster **NuGet-Paket-Manager** sicher, dass die Option **Vorabversion einbeziehen** aktiviert ist. Suchen Sie anschließend nach **Microsoft Azure-Geräteclient**, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

	Daraufhin wird das [NuGet-Paket mit dem Geräte-SDK für Azure IoT][lnk-device-nuget] heruntergeladen und installiert und dem Projekt ein Verweis auf das Paket hinzugefügt.

4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. Fügen Sie der **Program**-Klasse die folgenden Felder hinzu, und ersetzen Sie dabei die Platzhalterwerte durch den IoT-Hostnamen, den Sie im Abschnitt *Erstellen eines IoT Hubs* abgerufen haben, und durch den Geräteschlüssel, den Sie im Abschnitt *Erstellen einer Geräteidentität* abgerufen haben:

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

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

	Diese Methode sendet jede Sekunde eine neue D2C-Nachricht. Die Nachricht enthält ein JSON-serialisiertes Objekt mit der Geräte-ID sowie eine zufällig generierte Zahl, um einen Windgeschwindigkeitssensor zu simulieren.

7. Fügen Sie abschließend der **Main**-Methode die folgenden Zeilen hinzu:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  In der Standardeinstellung erstellt die **Create** -Methode einen **DeviceClient**, der das AMQP-Protokoll für die Kommunikation mit dem IoT Hub verwendet. Um das HTTPS-Protokoll zu verwenden, nutzen Sie die Überschreibung der **Create**-Methode, bei der Sie das Protokoll angeben können. Wenn Sie das HTTPS-Protokoll verwenden möchten, müssen Sie Ihrem Projekt auch das NuGet-Paket **Microsoft.AspNet.WebApi.Client** hinzufügen, um den **System.Net.Http.Formatting**-Namespace einzuschließen.


> [AZURE.NOTE]Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (z. B. einen exponentiellen Backoff), wie im MSDN-Artikel zum [Behandeln vorübergehender Fehler][lnk-transient-faults] beschrieben.

<!-- Links -->

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/de-DE/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_1217_2015-->