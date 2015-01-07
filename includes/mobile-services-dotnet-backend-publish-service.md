

Nachdem Sie die Client-App anhand des lokalen mobilen Diensts getestet haben, besteht der letzte Schritt dieses Lernprogramms im Veröffentlichen des mobilen Diensts in Azure und Ausführen der App mit einem Live-Dienst.

>[WACOM.NOTE]Dieses Verfahren zeigt, wie Sie Ihren mobilen Dienst mithilfe der Visual Studio-Tools veröffentlichen können. Sie können auch Ihren mobilen .NET-Back-End-Dienst mithilfe der Quellcodeverwaltung veröffentlichen. Weitere Informationen finden Sie unter [Speichern von Projektcode in der Quellcodeverwaltung](/de-de/documentation/articles/mobile-services-dotnet-backend-store-code-source-control/).

1. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das Projekt für den mobilen Dienst, klicken Sie auf **Veröffentlichen**, und klicken Sie dann im Dialogfeld **Web veröffentlichen** auf **Azure Mobile Services**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish.png)
	
2. Melden Sie sich mit Ihren Anmeldeinformationen bei Ihrem Azure-Konto an, wählen Sie Ihren Dienst in **Vorhandene Mobile Services** aus, und klicken Sie auf **OK**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-select-service.png)

	Visual Studio lädt die Veröffentlichungseinstellungen direkt von Azure herunter.

	>[WACOM.NOTE]Visual Studio speichert Ihre Azure-Anmeldeinformationen, bis Sie sich explizit abmelden.

3. Klicken Sie auf **Verbindung prüfen**, um sicherzustellen, dass die Veröffentlichung richtig konfiguriert ist, und klicken Sie dann auf **Veröffentlichen**.

	![](./media/mobile-services-dotnet-backend-publish-service/mobile-quickstart-publish-2.png)

	Nachdem die Veröffentlichung erfolgreich durchgeführt wurde, wird erneut die Seite zur Bestätigung angezeigt, dass der Dienst ausgeführt wird, diesmal in Azure.


<!--HONumber=35.1-->
