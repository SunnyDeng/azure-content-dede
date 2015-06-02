Nachdem Sie die Client-App anhand der lokalen mobilen App getestet haben, besteht der letzte Schritt dieses Lernprogramms im Veröffentlichen des mobilen App-Back-Ends in Azure und Ausführen der App mit einem Live-Dienst.

> [AZURE.NOTE]Dieses Verfahren zeigt, wie Sie Ihr mobiles App-Back-End mithilfe der Visual Studio-Tools veröffentlichen können. Sie können auch Ihr .NET-Back-End mithilfe der Quellcodeverwaltung veröffentlichen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Code der mobilen App (es handelt sich um den Namen der App, an den "Service" angefügt ist), und wählen Sie **Veröffentlichen**. 

	![Wählen Sie im Codeprojekt der App "Veröffentlichen"](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-project-publish.png)

2. Wählen Sie im Dialogfeld **Webveröffentlichung** als Veröffentlichungsziel **Azure Mobile App** aus. Wählen Sie im angezeigten Dialogfeld Ihre vorhandene mobile App aus. Dies ist Ihre mobile App, an deren Namen "Code" angefügt ist.

    ![Wählen Sie eine vorhandene Web-App aus, um diese zu veröffentlichen](./media/app-service-mobile-dotnet-backend-publish-service-preview/mobile-quickstart-publish-select-service.png)

3. Klicken Sie auf **Verbindung prüfen**, um sicherzustellen, dass die Veröffentlichung richtig konfiguriert ist, und klicken Sie dann auf **Veröffentlichen**.

	![Letzte Seite des Assistenten für die Veröffentlichungseinstellungen](./media/app-service-mobile-dotnet-backend-publish-service-preview/dotnet-publish-settings.png)

   Nachdem die Veröffentlichung erfolgreich durchgeführt wurde, wird die Seite mit der Bestätigung angezeigt, dass die mobile App in Azure ausgeführt wird. Im Visual Studio-Ausgabefenster wird ebenfalls die erfolgreiche Ausführung angezeigt.

<!--HONumber=54-->