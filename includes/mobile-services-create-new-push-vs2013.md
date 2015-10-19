Mit den folgenden Schritten werden Ihre App im Windows Store registriert und Ihr mobiler Dienst für die Unterstützung von Pushbenachrichtigungen konfiguriert. Außerdem wird Ihrer App Code hinzugefügt, um einen Gerätekanal bei Ihrem Notification Hub zu registrieren. Visual Studio 2013 verwendet die von Ihnen angegebenen Anmeldeinformationen, um eine Verbindung mit Azure und dem Windows Store herzustellen.

1. Öffnen Sie in Visual Studio 2013 den Projektmappen-Explorer, klicken Sie mit der rechten Maustaste auf das Windows Store-App-Projekt, und klicken Sie auf **Hinzufügen** und dann auf **Pushbenachrichtigung...**. 

	![Assistent zum Hinzufügen von Pushbenachrichtigungen in Visual Studio 2013](./media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)

	Dadurch wird der Assistent zum Hinzufügen von Pushbenachrichtigungen gestartet.

2. Klicken Sie auf **Weiter**, melden Sie sich bei Ihrem Windows Store-Konto an, geben Sie einen Namen unter **Reserve a new name** ein, und klicken Sie dann auf **Reservieren**.

	Dadurch wird eine neue App-Registrierung erstellt.

3. Klicken Sie in der Liste **App-Name** auf die neue Registrierung, und klicken Sie dann auf **Weiter**.

4. Klicken Sie auf der Seite **Dienst auswählen** auf den Namen des mobilen Diensts, und klicken Sie dann auf **Weiter** und **Fertig stellen**.

	Der von Ihrem mobilen Dienst verwendete Notification Hub wird mit der Registrierung des Windows-Benachrichtigungsdiensts (WNS) aktualisiert. Jetzt können Sie Azure Notification Hubs zum Senden von Benachrichtigungen von Mobile Services an Ihre App unter Verwendung von WNS nutzen.

	>[AZURE.NOTE]Dieses Lernprogramm veranschaulicht das Senden von Benachrichtigungen von einem mobilen Dienst-Back-End. Sie können dieselbe Notification Hub-Registrierung verwenden, um Benachrichtigungen von einem beliebigen Back-End-Dienst zu senden. Weitere Informationen finden Sie unter [Übersicht über Notification Hubs](http://msdn.microsoft.com/library/azure/jj927170.aspx).

5. Nach Abschluss des Assistenten wird in Visual Studio die neue Seite **Einrichtung von Pushbenachrichtigungen fast abgeschlossen** geöffnet. Auf dieser Seite wird eine alternative Methode zum Konfigurieren des Projekts für Ihren mobilen Dienst für den Versand von Benachrichtigungen beschrieben, die von diesem Lernprogramm abweicht.

	Der Code, der Ihrer universellen Windows-App-Lösung vom Assistenten zum Hinzufügen von Pushbenachrichtigungen hinzugefügt wird, ist plattformspezifisch. Weiter unten in diesem Abschnitt beseitigen Sie diese Redundanz, indem Sie den Clientcode von Mobile Services freigeben, wodurch die Verwaltung der universellen App vereinfacht wird.

<!-- URLs. -->
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet/

<!---HONumber=Oct15_HO2-->