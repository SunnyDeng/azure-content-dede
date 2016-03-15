### Erstellen der Konsolenanwendung und Abrufen der Assembly

So erstellen Sie eine neue Konsolenanwendung in Visual Studio und installieren das NuGet-Paket mit der Clientbibliothek von Azure Storage:

1. Wählen Sie in Visual Studio **Datei -> Neues Projekt**, und wählen Sie dann **Windows -> Konsolenanwendung** aus der Liste der Visual C#-Vorlagen aus.
2. Stellen Sie einen Namen für die Konsolenanwendung bereit, und klicken Sie dann auf **OK**.
3. Nachdem Ihr Projekt erstellt wurde, klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf das Projekt und wählen **NuGet-Pakete verwalten** aus. Suchen Sie online nach „WindowsAzure.Storage“, und klicken Sie auf **Installieren**, um das Azure Storage-Clientbibliothek für .NET-Paket und die zugehörigen Abhängigkeiten zu installieren.

Die Codebeispiele in diesem Artikel verwenden auch die [Microsoft Azure Configuration Manage-Bibliothek](https://msdn.microsoft.com/library/azure/mt634646.aspx) zum Abrufen der Speicherverbindungszeichenfolge von einer app.config-Datei in der Konsolenanwendung. Mit Azure Configuration Manager können Sie die Verbindungszeichenfolge zur Laufzeit abrufen, unabhängig davon, ob Ihre Anwendung in Microsoft Azure oder von einer Desktop-, Mobil- oder Webanwendung ausgeführt wird.

Klicken Sie zum Installieren des Azure Configuration Manager-Pakets im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. Suchen Sie online nach „ConfigurationManager“, und klicken Sie zum Installieren des Pakets auf **Installieren**.

Die Verwendung von Azure Configuration Manager ist optional. Sie können auch eine API wie die [ConfigurationManager-Klasse](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) von .NET Framework verwenden.

<!---HONumber=AcomDC_0309_2016-->