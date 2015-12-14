## Abrufen der IoT Hub-Schlüssel

Zeigen Sie den Authentifizierungsschlüssel für den neuen IoT Hub an.

1. Fügen Sie "Program.cs" die folgende Methode hinzu:

    ```
    static void ShowIoTHubKeys(ResourceManagementClient client, string token)
    {
    
    }
    ```

2. Fügen Sie den folgenden Code zur **ShowIoTHubKeys**-Methode hinzu, um die Authentifizierungsschlüssel an der Konsole auszugeben:

    ```
    client.HttpClient.DefaultRequestHeaders.Authorization = 
        new AuthenticationHeaderValue("Bearer", token);
    var httpsRepsonse = client.HttpClient.PostAsync(
        string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}/listKeys?api-version=2015-08-15-preview", 
        subscriptionId, rgName, iotHubName),
        null).Result;
    
    Console.WriteLine("Keys: {0}, 
        httpsRepsonse.Content.ReadAsStringAsync().Result);
    ```

<!---HONumber=AcomDC_1203_2015-->