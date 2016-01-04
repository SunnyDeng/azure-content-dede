

Um Ihre App für Pushbenachrichtigungen über den Apple Push Notification Service (APNS) zu registrieren, müssen Sie ein neues Pushzertifikat, eine neue App-ID und ein neues Bereitstellungsprofil für das Projekt im Apple-Entwicklerportal erstellen. Die App-ID enthält die Konfigurationsinformationen, die es Ihrer App ermöglichen, Pushbenachrichtigungen zu senden und zu empfangen. Diese Einstellungen enthalten das Pushbenachrichtigungszertifikat zur Authentifizierung beim Apple Push Notification Service (APNS), wenn Pushbenachrichtigungen gesendet und empfangen werden. Weitere Informationen zu diesen Konzepten finden Sie in der offiziellen Dokumentation zum [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).


####Generieren der Zertifikatsignieranforderungsdatei für das Pushzertifikat

Diese Schritte führen Sie durch die Erstellung der Zertifikatsignieranforderung. Diese wird verwendet, um ein Pushzertifikat zu generieren, das mit APNS verwendet wird.

1. Führen Sie auf Ihrem Mac das Tool "Schlüsselbundverwaltung" aus. Es kann im Ordner **Dienstprogramme** oder im Ordner **Andere** auf dem Launchpad geöffnet werden.

2. Klicken Sie auf **Schlüsselbundverwaltung**, erweitern Sie **Zertifikatsassistent**, und klicken Sie dann auf **Zertifikat einer Zertifizierungsinstanz anfordern …**.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Machen Sie entsprechende Angaben für die Felder **E-Mail des Benutzers** sowie **Allgemeiner Name**, vergewissern Sie sich, dass **Auf der Festplatte sichern** gewählt ist, und klicken Sie dann auf **Fortfahren**. Lassen Sie das Feld **E-Mail der Zert.-Instanz** leer, da hier keine Eingabe benötigt wird.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Geben Sie einen Namen für die Zertifikatsignieranforderungsdatei (CSR-Datei) in **Sichern unter** ein, wählen Sie den Speicherort in **Ort**, und klicken Sie dann auf **Sichern**.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

  	Hierdurch wird die CSR-Datei am gewählten Ort gespeichert, wobei Schreibtisch der Standardort ist. Merken Sie sich den für diese Datei gewählten Speicherort.


####Registrieren der App für Pushbenachrichtigungen

Erstellen Sie eine neue explizite App-ID für Ihre Anwendung bei Apple, und konfigurieren Sie sie auch für Pushbenachrichtigungen.

1. Gehen Sie im Apple Developer Center zum [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456), melden Sie sich mit Ihrer Apple-ID an, klicken Sie auf **Identifiers**, dann auf **App IDs** und schließlich auf das **+**-Symbol, um eine neue App zu registrieren.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Aktualisieren Sie die folgenden drei Felder für Ihre neue App, und klicken Sie dann auf **Weiter**:

	* **Name**: Geben Sie im Abschnitt **App ID Description** in das Feld **Name** einen beschreibenden Namen für Ihre App ein.
	
	* **Bundle ID**: Geben Sie im Abschnitt **Explicit App ID** eine **Bundle ID** im Format `<Organization Identifier>.<Product Name>` (entsprechend den Angaben im [App Distribution Guide](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8)) ein. Dieser muss mit dem übereinstimmen, was auch im XCode- oder Xamarin-Projekt für Ihre App verwendet wird.
	 
	* **Pushbenachrichtigungen**: Aktivieren Sie die Option **Push Notifications** im Bereich **App Services**.

	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

3.	Überprüfen Sie die Einstellung im Bildschirm „Confirm your App ID“, und klicken Sie nach der Überprüfung auf **Submit**.

4. 	Sobald Sie die neue App-ID übermittelt haben, wird der **Registration complete**-Bildschirm geöffnet. Klicken Sie auf **Done**.

5. Bestimmen Sie im Developer Center unter "App IDs" die soeben erstellte App-ID, und klicken Sie auf deren Zeile. Durch Klicken auf die Zeile mit der App-ID werden Einzelheiten zur App angezeigt. Klicken Sie unten auf dem Bildschirm auf **Bearbeiten**.

6. Scrollen Sie bis zur Unterseite des Bildschirms und klicken Sie auf die Schaltfläche **Create Certificate...** unter dem Abschnitt **Development Push SSL Certificate**.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

   	Hierdurch wird der Assistent „Add iOS Certificate“ angezeigt.

    > [AZURE.NOTE]In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Achten Sie darauf, dass Sie denselben Zertifikattyp beim Senden von Benachrichtigungen verwenden.

7. Klicken Sie auf **Choose File**, navigieren Sie zum Speicherort der CSR-Datei für Ihr Pushzertifikat. Klicken Sie dann auf **Generate**.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

8. Nach Erstellung des Zertifikats durch das Portal klicken Sie auf die Schaltfläche **Download**.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

   	Hierdurch wird das Signierzertifikat heruntergeladen und auf Ihrem Computer in Ihrem Download-Ordner gespeichert.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE]Standardmäßig ist die heruntergeladene Datei ein Entwicklungszertifikat namens **aps\_development.cer**.

9. Doppelklicken Sie auf dem heruntergeladenen Pushzertifikat **aps\_development.cer**. Hierdurch wird das neue Zertifikat im Schlüsselbund installiert, wie unten gezeigt:

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE]Der Name in Ihrem Zertifikat kann sich davon unterscheiden, er enthält jedoch das Präfix **Apple Development iOS Push Services:**.

10. Klicken Sie in der Kategorie **Certificates** in der Schlüsselbundverwaltung mit der rechten Maustaste auf das gerade neu erstellte Pushzertifikat. Klicken Sie auf **Exportieren**, benennen Sie die Datei, wählen Sie das Format **.p12** aus, und klicken Sie dann auf **Speichern**.

	Notieren Sie sich den Dateinamen und den Speicherort des exportierten P12-Zertifikats. Damit wird die Authentifizierung mit APNS aktiviert, indem Sie es zum klassischen Azure-Portal hochladen.



####Erstellen eines Bereitstellungsprofils für die App

1. Zurück im <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> wählen Sie **Provisioning Profiles**, dann **All**, und klicken Sie schließlich auf die Schaltfläche **+**, um ein neues Profil zu erstellen. Hierdurch wird der Assistent **Add iOS Provisiong Profile** gestartet

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Wählen Sie unter **Development** die Option **iOS App Development** als Bereitstellungsprofiltyp aus, und klicken Sie dann auf **Continue**.


3. Wählen Sie anschließend die soeben erstellte App-ID in der Dropdownliste **App ID** aus, und klicken Sie auf **Continue**.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. Wählen Sie im Bildschirm **Select certificates** das für die Codesignatur verwendete Entwicklungszertifikat aus, und klicken Sie auf **Continue**. Dabei handelt es sich um ein Signaturzertifikat, nicht um das soeben erstellte Pushzertifikat.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. Anschließend wählen Sie die zum Testen zu verwendenden **Devices** und klicken Sie auf **Continue**

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Geben Sie schließlich einen Namen für das Profil im Feld **Profile Name** ein, und klicken Sie auf **Generate**.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

<!---HONumber=AcomDC_1210_2015-->