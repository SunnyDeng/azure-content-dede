### Abrufen der Verbindungszeichenfolge
Sie können Ihre Speicherkontoinformationen mit dem Typ **CloudStorageAccount** darstellen. Falls Sie eine Azure-Projektvorlage verwenden und/oder einen Verweis auf den Namespace "Microsoft.WindowsAzure.CloudConfigurationManager" haben, können Sie den Typ **CloudConfigurationManager** verwenden,um Ihre Speicherverbindungszeichenfolge und Speicherkontodaten aus der Azure-Dienstkonfiguration abzurufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Wenn Sie eine Anwendung ohne einen Verweis auf "Microsoft.WindowsAzure.CloudConfigurationManager" erstellen und die Verbindungszeichenfolge sich (wie oben gezeigt) in `web.config` oder `app.config` befindet, können Sie die Verbindungszeichenfolge mit **ConfigurationManager** abrufen. Sie müssen einen Verweis auf "System.Configuration.dll" zu Ihrem Projekt und eine andere Namespace-Deklaration dafür hinzufügen:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"]);

<!---HONumber=July15_HO4-->