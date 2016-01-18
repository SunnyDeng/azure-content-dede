## Senden interaktiver Nachrichten vom simulierten Gerät

In diesem Abschnitt ändern Sie die simulierte Geräteanwendung, die Sie im Tutorial [Erste Schritte mit IoT Hub] erstellt haben, sodass sie interaktive Gerät-zu-Cloud-Nachrichten an IoT Hub sendet.

1. Fügen Sie in Visual Studio im **SimulatedDevice**-Projekt der **Program**-Klasse folgende Methode hinzu.

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Thread.Sleep(10000);
      }
    }
    ```

    Diese Methode ähnelt der **SendDeviceToCloudMessagesAsync**-Methode im Projekt **SimulatedDevice**. Die einzigen Unterschiede bestehen darin, dass Sie jetzt die Systemeigenschaft **MessageId** und eine Benutzereigenschaft **messageType** festlegen. Der Code weist der Eigenschaft **MessageId** eine GUID (Globally Unique Identifier) zu, die von Service Bus zum Deduplizieren der empfangenen Nachrichten verwendet werden kann. Im Beispiel wird die Eigenschaft **messageType** verwendet, um interaktive von Datenpunktnachrichten zu unterscheiden. Die Anwendung übergibt diese Informationen in Nachrichteneigenschaften anstatt im Nachrichtentext. Auf diese Weise muss der Ereignisprozessor zum Nachrichtenrouting nicht die gesamte Nachricht deserialisieren.

    > [AZURE.NOTE]Die zum Deduplizieren interaktiver Nachrichten verwendete **MessageId** muss unbedingt im Gerätecode erstellt werden, da Unterbrechungen der Netzwerkverbindung (oder andere Fehler) dazu führen können, dass dieselbe Nachricht vom Gerät mehrmals neu übertragen wird. Sie können auch eine semantische Nachrichten-ID, z. B. einen Hash der relevanten Datenfelder der Nachricht, anstelle einer GUID verwenden.

2. Fügen Sie in der **Main**-Methode unmittelbar vor der Zeile `Console.ReadLine()` folgende Methode hinzu:

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE]Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie eine Wiederholungsrichtlinie implementieren (z. B. Exponential Backoff), wie im MSDN-Artikel zum [Behandeln vorübergehender Fehler] beschrieben.

<!-- Links -->
[Behandeln vorübergehender Fehler]: https://msdn.microsoft.com/de-DE/library/hh675232.aspx
[Erste Schritte mit IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!---HONumber=AcomDC_0107_2016-->