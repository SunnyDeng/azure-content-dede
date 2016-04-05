###Gewähren des Zugriffs für Mobile Engagement auf den GCM-API-Schlüssel

Damit Mobile Engagement Pushbenachrichtigungen in Ihrem Namen senden kann, müssen Sie Mobile Engagement den Zugriff auf Ihren API-Schlüssel gewähren. Dies wird durch Konfigurieren und Eingeben Ihres Schlüssels im Mobile Engagement-Portal erreicht.

1. Stellen Sie über das klassische Azure-Portal sicher, dass Sie sich in der App befinden, die wir für dieses Projekt verwenden, und klicken Sie dann am unteren Rand auf die Schaltfläche **Einbinden**:

	![](./media/mobile-engagement-android-send-push/engage-button.png)

2. Klicken Sie dann auf den Abschnitt **Einstellungen** -> **Systemeigener Push**, um Ihren GCM-Schlüssel einzugeben:

	![](./media/mobile-engagement-android-send-push/engagement-portal.png)

3. Klicken Sie im Abschnitt **GCM-Einstellungen** auf das Symbol **Bearbeiten** vor **API-Schlüssel**, wie unten gezeigt:

	![](./media/mobile-engagement-android-send-push/native-push-settings.png)

4. Fügen Sie im Popupfenster den GCM-Serverschlüssel ein, den Sie zuvor bezogen haben, und klicken Sie auf **OK**.

	![](./media/mobile-engagement-android-send-push/api-key.png)

##<a id="send"></a>Versenden von Benachrichtigungen an die App

Wir erstellen nun eine einfache Push-Benachrichtigungskampagne, die eine Push-Benachrichtigung an die App sendet.

1. Navigieren Sie zu der Registerkarte **REACH** in Ihrem Mobile Engagement-Portal.

2. Klicken Sie auf **Neue Ankündigung**, um die Push-Benachrichtigungskampagne zu erstellen.

	![](./media/mobile-engagement-android-send-push/new-announcement.png)

3. Richten Sie das erste Feld der Kampagne mit den folgenden Schritten ein:

	![](./media/mobile-engagement-android-send-push/campaign-first-params.png)

	a. Benennen der Kampagne.

	b. Wählen Sie für **Übermittlungstyp** *Systembenachrichtigung/Einfach*: Dies ist der einfache Android-Pushbenachrichtigungstyp, der einen Titel und eine kleine Textzeile unterstützt.

	c. Wählen Sie für **Lieferzeit** die Option *Jederzeit* aus, um eine Benachrichtigung darüber zu erhalten, ob die App gestartet wurde oder nicht.

	d. Geben Sie in den Benachrichtigungstext den **Titel** ein, der im Pushvorgang fett gedruckt erscheint.

	e. Geben Sie dann Ihre **Nachricht** ein.

4. Scrollen Sie nach unten, und wählen Sie im **Inhaltsbereich** die Option **Nur Benachrichtigung** aus.

	![](./media/mobile-engagement-android-send-push/campaign-content.png)

5. Sie haben das Festlegen der Basiskampagne abgeschlossen. Scrollen Sie nun wieder nach unten, und klicken Sie auf die Schaltfläche **Erstellen**, um Ihre Kampagne zu speichern.

6. Als letzten Schritt klicken Sie auf **Aktivieren**, um die Kampagne zu aktivieren, damit Pushbenachrichtigungen gesendet werden.

	![](./media/mobile-engagement-android-send-push/campaign-activate.png)

<!---HONumber=AcomDC_0330_2016-->