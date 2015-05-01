

Nachdem Sie die Client-App anhand des lokalen mobilen Diensts getestet haben, besteht der letzte Schritt dieses Lernprogramms im Veröffentlichen des mobilen Diensts in Azure und Ausführen der App mit einem Live-Dienst.

>[AZURE.NOTE] Dieses Verfahren zeigt, wie Sie Ihren mobilen Dienst mithilfe der Visual Studio-Tools veröffentlichen können. Sie können auch Ihren mobilen .NET-Back-End-Dienst mithilfe der Quellcodeverwaltung veröffentlichen. Weitere Informationen finden Sie unter [Speichern des Projektcodes in der Quellcodeverwaltung](../articles/mobile-services-dotnet-backend-store-code-source-control.md).

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt für den mobilen Dienst und dann auf**Veröffentlichen**, und klicken Sie anschließend im Dialogfeld**Web veröffentlichen**auf **Azure Mobile Services**.

2. Melden Sie sich mit Ihren Anmeldeinformationen bei Ihrem Azure-Konto an, wählen Sie Ihren Dienst unter **Vorhandene Mobile Services** aus, und klicken Sie auf **OK**. Visual Studio lädt die Veröffentlichungseinstellungen direkt von Azure herunter.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-select-service.png)

3. Klicken Sie auf **Verbindung prüfen**, um sicherzustellen, dass die Veröffentlichung richtig konfiguriert ist, und klicken Sie dann auf **Veröffentlichen**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png)

	Nachdem die Veröffentlichung erfolgreich durchgeführt wurde, wird erneut die Seite zur Bestätigung angezeigt, dass der Dienst ausgeführt wird, diesmal in Azure.

<!--HONumber=52-->
