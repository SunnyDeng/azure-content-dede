###Erteilen Sie Ihrem Pushzertifikat den Zugriff auf Mobile Engagement.

Damit Mobile Engagement Pushbenachrichtigungen in Ihrem Namen senden darf, müssen Sie den Zugriff auf Ihr Zertifikat gestatten. Dies wird durch Konfigurieren und Eingeben Ihres Zertifikats im Mobile Engagement-Portal erreicht. Stellen Sie sicher, dass Sie Ihr P12-Zertifikat erhalten, wie in der [Apple-Dokumentation](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6) beschrieben.

1. Navigieren Sie zum Mobile Engagement-Portal. Vergewissern Sie sich, dass Sie sich in der richtigen App befinden, und klicken Sie dann unten auf die Schaltfläche **Einbinden**:

	![](./media/mobile-engagement-ios-send-push/engage-button.png)

2. Klicken Sie auf die Seite **Einstellungen** im Mobile Engagement-Portal. Klicken Sie hier auf den Abschnitt **Systemeigener Push**, um Ihr P12-Zertifikat hochzuladen:

	![](./media/mobile-engagement-ios-send-push/engagement-portal.png)

3. Wählen Sie Ihr P12-Zertifikat aus, laden Sie es hoch, und geben Sie anschließend Ihr Kennwort ein:

	![](./media/mobile-engagement-ios-send-push/native-push-settings.png)

##<a id="send"></a>Versenden von Benachrichtigungen an die App

Wir erstellen jetzt eine einfache Pushbenachrichtigungskampagne, die eine Pushbenachrichtigung an die App sendet:

1. Navigieren Sie zu der Registerkarte **Reach** in Ihrem Mobile Engagement-Portal.

2. Klicken Sie auf **Neue Ankündigung**, um die Push-Kampagne zu erstellen.

	![](./media/mobile-engagement-ios-send-push/new-announcement.png)

3. Richten Sie die ersten Felder der Kampagne ein:

	![](./media/mobile-engagement-ios-send-push/campaign-first-params.png)

	- 	Geben Sie einen **Name**n für die Kampagne an. 
	- 	Wählen Sie für **Übermittlungszeit** die Option **Nur außerhalb der App** aus: Dies ist der einfache Apple-Pushbenachrichtigungstyp, der Text unterstützt.
	- 	Geben Sie in den Benachrichtigungstext zunächst den **Titel** ein, der die erste Zeile in der Pushbenachrichtigung ist.
	- 	Geben Sie dann Ihre **Nachricht** ein, die die zweite Zeile darstellt.

4. Navigieren Sie nach unten, und wählen Sie im Inhaltsbereich die Option **Nur Benachrichtigung** aus.

	![](./media/mobile-engagement-ios-send-push/campaign-content.png)

5. Sie haben das Festlegen der einfachst möglichen Kampagne abgeschlossen. Scrollen Sie nun nach unten, und klicken Sie auf die Schaltfläche **Erstellen**, um Ihre Pushbenachrichtigungskampagne zu speichern.

6. Klicken Sie schließlich auf **Aktivieren**, um die Pushbenachrichtigung zu senden.

	![](./media/mobile-engagement-ios-send-push/campaign-activate.png)

7. Sie erhalten die Benachrichtigung auf Ihrem iOS-Gerät im Notification Center wie folgt:

	![](./media/mobile-engagement-ios-send-push/iphone-notification.png)

8. Wenn Sie eine Apple Watch zusammen mit diesem iOS-Gerät nutzen, wird die Benachrichtigung auf Ihrer Apple Watch angezeigt:

	![](./media/mobile-engagement-ios-send-push/apple-watch.png)


 

 

<!---HONumber=Nov15_HO2-->