

##Generieren der Zertifikatsignieranforderungsdatei

Der Apple Push Notification Service (APNS) verwendet Zertifikate zur Authentifizierung Ihrer Pushbenachrichtigungen. Befolgen Sie diese Anweisungen, um das erforderliche Pushzertifikat zum Senden und Empfangen von Benachrichtigungen zu erstellen. Weitere Informationen zu diesen Konzepten finden Sie in der offiziellen Dokumentation zum [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).

Erstellen Sie die Zertifikatsignieranforderungsdatei (CSR-Datei), die von Apple zur Generierung eines signierten Pushzertifikats verwendet wird.

1. Führen Sie auf Ihrem Mac das Tool "Schlüsselbundverwaltung" aus. Es kann im Ordner **Dienstprogramme** oder im Ordner **Andere** auf dem Launchpad geöffnet werden.

2. Klicken Sie auf **Schlüsselbundverwaltung**, erweitern Sie **Zertifikatsassistent**, und klicken Sie dann auf **Zertifikat einer Zertifizierungsinstanz anfordern …**.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Machen Sie entsprechende Angaben für die Felder **E-Mail des Benutzers** sowie **Allgemeiner Name**, vergewissern Sie sich, dass **Auf der Festplatte sichern** gewählt ist, und klicken Sie dann auf **Fortfahren**. Lassen Sie das Feld **E-Mail der Zert.-Instanz** leer, da hier keine Eingabe benötigt wird.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Geben Sie einen Namen für die Zertifikatsignieranforderungsdatei (CSR-Datei) in **Sichern unter** ein, wählen Sie den Speicherort in **Ort**, und klicken Sie dann auf **Sichern**.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

  	Hierdurch wird die CSR-Datei am gewählten Ort gespeichert, wobei Schreibtisch der Standardort ist. Merken Sie sich den für diese Datei gewählten Speicherort.

Anschließend werden Sie Ihre App bei Apple registrieren, Pushbenachrichtigungen aktivieren und diese exportierte CSR hochladen, um ein Pushzertifikat zu erstellen.

##Registrieren der App für Pushbenachrichtigungen

Um Pushbenachrichtigungen an eine iOS-App senden zu können, muss diese bei Apple registriert und auch für Pushbenachrichtigungen angemeldet werden.

1. Falls Sie Ihre App noch nicht registriert haben, gehen Sie im Apple Developer Center zum <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>, melden Sie sich mit Ihrer Apple-ID an, klicken Sie auf **Identifiers**, dann auf **App IDs** und schließlich auf das **+**-Symbol, um eine neue App zu registrieren.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)


2. Aktualisieren Sie die folgenden drei Felder für Ihre neue App, und klicken Sie dann auf **Weiter**:

	* **Name**: Geben Sie im Abschnitt **App ID Description** in das Feld **Name** einen beschreibenden Namen für Ihre App ein.
	
	* **Bundle ID**: Geben Sie im Abschnitt **Explicit App ID** eine **Bundle ID** im Format `<Organization Identifier>.<Product Name>` (entsprechend den Angaben im [App Distribution Guide](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8)) ein. Die *Organisationskennung* und der *Produktname*, die Sie hier eingeben, müssen der Organisationskennung und dem Produktnamen entsprechen, die Sie beim Erstellen des XCode-Projekts verwenden. Im folgenden Screenshot werden *NotificationHubs* als Organisationskennung und *GetStarted* als Produktname verwendet. Wenn Sie sicherstellen, dass diese Werte mit den Werten übereinstimmen, die Sie in Ihrem XCode-Projekt verwenden, können Sie das richtige Veröffentlichungsprofil mit XCode verwenden.
	
	* **Pushbenachrichtigungen**: Aktivieren Sie die Option **Push Notifications** im Bereich **App Services**.

	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

   	Hierdurch wird Ihre App-ID generiert, und Sie werden zur Bestätigung der Daten aufgefordert. Klicken Sie auf **Senden**


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-confirm-new-appid.png)


   	Nachdem Sie **Submit** angeklickt haben, wird der **Registration complete**-Bildschirm geöffnet, wie unten gezeigt. Klicken Sie auf **Done**.


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


3. Bestimmen Sie im Developer Center unter "App IDs" die soeben erstellte App-ID, und klicken Sie auf deren Zeile.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

   	Durch Klicken auf die App-ID werden Einzelheiten zur App angezeigt. Klicken Sie unten auf dem Bildschirm auf **Bearbeiten**.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. Scrollen Sie bis zur Unterseite des Bildschirms und klicken Sie auf die Schaltfläche **Create Certificate...** unter dem Abschnitt **Development Push SSL Certificate**.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

   	Hierdurch wird der Assistent "Add iOS Certificate" zur Anzeige gebracht.

    > [AZURE.NOTE]In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Achten Sie darauf, dass Sie denselben Zertifikattyp beim Senden von Benachrichtigungen verwenden.

5. Klicken Sie auf **Choose File**, browsen Sie zum Speicherort der in der ersten Aufgabe erstellten CSR-Datei und klicken Sie dann auf **Generate**.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. Nach Erstellung des Zertifikats vom Portal klicken Sie auf die Schaltfläche **Download** und dann auf **Done**.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

   	Hierdurch wird das Zertifikat heruntergeladen und auf Ihrem Computer in Ihrem Downloadordner gespeichert.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE]Standardmäßig ist die heruntergeladene Datei ein Entwicklungszertifikat namens **aps\_development.cer**.

7. Doppelklicken Sie auf dem heruntergeladenen Pushzertifikat **aps\_development.cer**.

   	Hierdurch wird das neue Zertifikat im Schlüsselbund installiert, wie unten gezeigt:

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE]Der Name in Ihrem Zertifikat kann sich davon unterscheiden, er enthält jedoch das Präfix **Apple Development iOS Push Services:**.

8. Klicken Sie in der Kategorie **Zertifikate** in der Schlüsselbundverwaltung mit der rechten Maustaste auf das neu erstellte Pushzertifikat. Klicken Sie auf **Exportieren**, benennen Sie die Datei, wählen Sie das Format **.p12** aus, und klicken Sie dann auf **Speichern**.

	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

	Notieren Sie sich den Dateinamen und den Speicherort des exportierten P12-Zertifikats, das zum Aktivieren der Authentifizierung mit APNS verwendet wird.

	>[AZURE.NOTE]In diesem Lernprogramm wird eine QuickStart.p12-Datei erstellt. Name und Ort Ihrer Datei können verschieden sein.


##Erstellen eines Bereitstellungsprofils für die App

1. Zurück im <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> wählen Sie **Provisioning Profiles**, dann **All**, und klicken Sie schließlich auf die Schaltfläche **+**, um ein neues Profil zu erstellen. Hierdurch wird der Assistent **Add iOS Provisiong Profile** gestartet

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Wählen Sie unter **Development** die Option **iOS App Development** als Bereitstellungsprofiltyp aus, und klicken Sie dann auf **Continue**.


3. Wählen Sie anschließend die soeben erstellte App-ID in der Dropdownliste **App ID** aus, und klicken Sie auf **Continue**.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. Wählen Sie im Bildschirm **Select certificates** das normalerweise für die Codesignatur verwendete Entwicklungszertifikat aus, und klicken Sie auf **Continue**. Dabei handelt es sich nicht um das soeben erstellte Pushzertifikat.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. Anschließend wählen Sie die zum Testen zu verwendenden **Devices** und klicken Sie auf **Continue**

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Geben Sie schließlich einen Namen für das Profil im Feld **Profile Name** ein, und klicken Sie auf **Generate**.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)


7. Nachdem das neue Bereitstellungsprofil erstellt wurde, klicken Sie darauf, um es herunterzuladen und auf Ihrem Xcode-Entwicklungscomputer zu installieren. Klicken Sie anschließend auf **Fertig**.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

<!----HONumber=AcomDC_1203_2015-->