Mit den folgenden Schritten werden Ihre App im Windows Store registriert und Ihr mobiler Dienst für die Unterstützung von Pushbenachrichtigungen konfiguriert. Außerdem wird Ihrer App Code hinzugefügt, um einen Gerätekanal bei Ihrem Notification Hub zu registrieren. Visual Studio 2013 verwendet die von Ihnen angegebenen Anmeldeinformationen, um eine Verbindung mit Azure und dem Windows Store herzustellen. 

1. Öffnen Sie den Projektmappen-Explorer in Visual Studio 2013, klicken Sie dann mit der rechten Maustaste auf das Projekt für eine Windows Store-App, und klicken Sie auf **Hinzufügen** und anschließend auf **Pushbenachrichtigung...**. 

	![Add Push Notification wizard in Visual Studio 2013](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)

	Dadurch wird der Assistent zum Hinzufügen von Pushbenachrichtigungen gestartet.

2. Klicken Sie auf **Weiter**, melden Sie sich bei Ihrem Windows Store-Konto an, geben Sie einen Namen unter **Einen neuen Namen reservieren** an, und klicken Sie dann auf **Reservieren**.

	![Select an app name in the Add Push Notification wizard](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-2.png) 

	Dadurch wird eine neue App-Registrierung erstellt.

3. Klicken Sie in der Liste **App-Name** auf die neue Registrierung und anschließend auf **Weiter**.

	![mobile-add-push-notifications-vs2013-3](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-3.png)

4. Klicken Sie auf der Seite **Dienst auswählen** auf den Namen des mobilen Dienstes, und klicken Sie dann auf **Weiter** und **Fertig stellen**. 

	Der von Ihrem mobilen Dienst verwendete Notification Hub wird mit der Registrierung des Windows-Benachrichtigungsdiensts (WNS) aktualisiert. Jetzt können Sie Azure Notification Hubs zum Senden von Benachrichtigungen von Mobile Services an Ihre App unter Verwendung von WNS nutzen. 

	>[AZURE.NOTE]Dieses Lernprogramm veranschaulicht das Senden von Benachrichtigungen von einem mobilen Dienst-Back-End. Sie können dieselbe Notification Hub-Registrierung verwenden, um Benachrichtigungen von einem beliebigen Back-End-Dienst zu senden. Weitere Informationen finden Sie unter [Übersicht über Benachrichtigungs-Hubs](http://msdn.microsoft.com/library/azure/jj927170.aspx).

5. Nach Abschluss des Assistenten wird in Visual Studio die neue Seite **Push-Installation fast abgeschlossen** geöffnet. Auf dieser Seite wird eine alternative Methode zum Konfigurieren des Projekts für Ihren mobilen Dienst für den Versand von Benachrichtigungen beschrieben, die von diesem Lernprogramm abweicht. 

	>[AZURE.NOTE]Der Code, der Ihrer universellen Windows-App-Lösung vom Assistenten zum Hinzufügen von Pushbenachrichtigungen hinzugefügt wird, ist plattformspezifisch. Weiter unten in diesem Abschnitt beseitigen Sie diese Redundanz, indem Sie den Clientcode von Mobile Services freigeben, wodurch die Verwaltung der universellen App vereinfacht wird.  

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-dotnet/
[Importieren der publishsettings-Datei in Visual Studio 2013]: /de-de/documentation/articles/mobile-services-windows-how-to-import-publishsettings/
<!--HONumber=42-->
