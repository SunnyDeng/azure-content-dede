3. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt (nicht auf die Projektmappe), und wählen Sie **Hinzufügen > Azure-API-App-Client**. 

	![](./media/app-service-api-dotnet-add-generated-client/03-add-azure-api-client-v3.png)
	
3. Klicken Sie im Dialogfeld **Azure-API-App-Client hinzufügen** auf **Aus Azure-API-App herunterladen**.

5. Wählen Sie in der Dropdownliste die API-App aus, die aufgerufen werden soll.

7. Klicken Sie auf **OK**.

	![Bildschirm zur Generierung](./media/app-service-api-dotnet-add-generated-client/04-select-the-api-v3.png)

	Der Assistent lädt die API-Metadatendatei herunter und generiert eine typisierte Schnittstelle für den Aufruf der API-App.

	![Generierungsvorgang wird ausgeführt](./media/app-service-api-dotnet-add-generated-client/05-metadata-downloading-v3.png)

	Sobald der Code generiert wurde, wird im **Projektmappen-Explorer** ein neuer Ordner mit dem Namen der API-App angezeigt. Dieser Ordner enthält den Code zur Implementierung der Clientklassen und Datenmodelle.

	![Generierung abgeschlossen](./media/app-service-api-dotnet-add-generated-client/06-code-gen-output-v3.png)

<!---HONumber=August15_HO6-->