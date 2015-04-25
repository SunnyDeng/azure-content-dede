
Der Apple Push Notification Service (APNS) verwendet Zertifikate zur Authentifizierung Ihres mobilen Diensts. Folgen Sie diesen Anweisungen zum Erstellen der erforderlichen Zertifikate und zum Hochladen auf Ihren mobilen Dienst. Die offizielle Dokumentation der APNS-Funktion finden Sie unter [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).

## <a id="certificates"></a>Generieren der Zertifikatsignieranforderungsdatei

Zunächst müssen Sie die Zertifikatsignieranforderungsdatei (CSR-Datei) erstellen, die von Apple zur Generierung eines signierten Zertifikats verwendet wird.

1. Führen Sie das Programm "Schlüsselbundverwaltung" im Ordner "Dienstprogramme" aus.

2. Klicken Sie auf **Schlüsselbundverwaltung**, erweitern Sie **Zertifikatsassistent**, und klicken Sie dann auf **Zertifikat von einer Zertifizierungsinstanz anfordern ...**.

  ![](./media/enable-apple-push-notifications/mobile-services-ios-push-step5.png)

3. Machen Sie entsprechende Angaben für die Felder **E-Mail des Benutzers** sowie **Allgemeiner Name**, vergewissern Sie sich, dass **Auf der Festplatte sichern** ausgewählt ist, und klicken Sie dann auf **Weiter**. Lassen Sie das Feld **E-Mail der Zert.-Instanz** leer, da hier keine Eingabe benötigt wird.

  ![](./media/enable-apple-push-notifications/mobile-services-ios-push-step6.png)

4. Geben Sie einen Namen für die Zertifikatsignieranforderungsdatei (CSR-Datei) in **Sichern unter** ein, wählen Sie den Speicherort in **Ort**, und klicken Sie dann auf **Sichern**.

  ![](./media/enable-apple-push-notifications/mobile-services-ios-push-step7.png)

  	Hierdurch wird die CSR-Datei am gewählten Ort gespeichert, wobei Schreibtisch der Standardort ist. Merken Sie sich den für diese Datei gewählten Speicherort.

Anschließend werden Sie Ihre App bei Apple registrieren, Pushbenachrichtigungen aktivieren und diese exportierte CSR hochladen, um ein Pushzertifikat zu erstellen.

## <a id="register"></a>Registrieren der App für Pushbenachrichtigungen

Um von Mobile Services Pushbenachrichtigungen an eine iOS-App senden zu können, muss diese selbst bei Apple registriert und auch für Pushbenachrichtigungen angemeldet werden.  

1. Wenn Sie Ihre App noch nicht registriert haben, navigieren Sie im Apple Developer Center zum <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>, melden Sie sich mit Ihrer Apple-ID an, klicken Sie auf **Identifiers**, dann auf **App IDs** und schließlich auf das Pluszeichen (**+**), um eine neue App zu registrieren.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-02.png)



> [AZURE.NOTE] Wenn Sie sich dafür entscheiden, einen anderen <strong>Bundle Identifier</strong>-Wert als <i>MobileServices.Quickstart</i> anzugeben, müssen Sie auch den Bundle-ID-Wert in Ihrem Xcode-Projekt aktualisieren. Es wird empfohlen, den genauen Bundle-ID-Wert zu verwenden, den Sie bereits in Ihrem Schnellstartprojekt verwendet haben.

2. Geben Sie unter **Beschreibung** einen Namen für Ihre App ein, tragen Sie den Wert _MobileServices.Quickstart_ unter **Bündelkennung** ein, markieren Sie die Option "Push Notifications" im Abschnitt "App-Dienste" und klicken Sie dann auf **Weiter**. In diesem Beispiel wird die ID **MobileServices.Quickstart** verwendet. Dieselbe ID kann jedoch nicht erneut benutzt werden, da App-IDs über alle Benutzer einzigartig sein müssen. Daher empfiehlt es sich, dass Sie Ihren vollen Namen oder Ihre Initialen an den App-Namen anhängen.


   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-03.png)

   Hierdurch wird Ihre App-ID generiert und Sie werden aufgefordert, die Daten per **Senden** zu übermitteln. Klicken Sie auf **Senden**.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-04.png)

   Nach dem Klicken auf **Senden** wird der Bildschirm **Registrierung abgeschlossen** geöffnet, wie nachstehend gezeigt. Klicken Sie auf **Done**.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-05.png)

3. Lokalisieren Sie die soeben erstellte App-ID und klicken Sie auf deren Zeile.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-06.png)

   Durch Klicken auf die App-ID werden Einzelheiten zur App und die App-ID angezeigt. Klicken Sie auf die Schaltfläche **Einstellungen**.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-07.png)

4. Führen Sie einen Bildlauf bis zum unteren Bildschirmbereich durch, und klicken Sie auf die Schaltfläche **Create Certificate...** unterhalb des Abschnitts **Development Push SSL Certificate**.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-08.png)

   Hierdurch wird der Assistent "Add iOS Certificate" angezeigt.

    > [AZURE.NOTE] In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Vergewissern Sie sich nur, dass Sie denselben Zertifikattyp einstellen, wenn Sie das Zertifikat zu Mobile Services hochladen.

5. Klicken Sie auf **Choose File**, navigieren Sie zum Speicherort der in der ersten Aufgabe erstellten CSR-Datei, und klicken Sie dann auf **Generate**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-10.png)

6. Nach Erstellung des Zertifikats durch das Portal klicken Sie auf die Schaltfläche **Download** und dann auf **Done**.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-11.png)

   Hierdurch wird das Signierzertifikat heruntergeladen und auf Ihrem Computer in Ihrem Download-Ordner gespeichert.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step9.png)

    > [AZURE.NOTE] Standardmäßig ist die heruntergeladene Datei ein Entwicklungszertifikat namens **aps_development.cer**.

7. Doppelklicken Sie auf das heruntergeladene Pushzertifikat **aps_development.cer**.

   Hierdurch wird das neue Zertifikat im Schlüsselbund installiert, wie unten gezeigt:

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-step10.png)

    > [AZURE.NOTE] Der Name in Ihrem Zertifikat kann zwar ein anderer sein, er enthält jedoch das Präfix **Apple Development iOS Push Services:**.

Später können Sie mit diesem Zertifikat eine .p12-Datei erstellen und zu Mobile Services hochladen, um Authentifizierung mit APNS zu ermöglichen.

## <a id="profile"></a>Erstellen eines Bereitstellungsprofils für die App

1. Wählen Sie im <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> die Option **Provisioning Profiles** aus, wählen Sie **Alle** aus, und klicken Sie dann auf das **+**, um ein neues Profil zu erstellen. Hierdurch wird der Assistent **Add iOS Provisiong Profile** gestartet.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-12.png)

2. Wählen Sie unter **Entwicklung** die Option **iOS App-Entwicklung** als Bereitstellungsprofiltyp aus und klicken Sie dann auf **Weiter**.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-13.png)

3. Wählen Sie anschließend die App-ID für die Mobile Services Quickstart-App aus der Dropdownliste **App ID** aus, und klicken Sie dann auf **Continue**.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-14.png)

4. Wählen Sie im Bildschirm **Select certificates** das zuvor erstellte Zertifikat aus, und klicken Sie auf **Continue**.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-15.png)

5. Wählen Sie anschließend zum Testen **Geräte** aus und klicken auf **Weiter**.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-16.png)

6. Geben Sie schließlich einen Namen für das Profil im Feld **Profilname** ein, klicken Sie auf **Generieren**, dann auf **Fertig**.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-17.png)

  	Dadurch wird ein neues Bereitstellungsprofil erstellt.

7. Öffnen Sie in Xcode den Organisator, wählen Sie die Geräteansicht, dann **Provisioning Profiles** im Abschnitt **Library** im linken Bereich, und klicken Sie schließlich auf die Schaltfläche **Refresh** an der Unterseite im mittleren Bereich.

8. Wählen Sie alternativ im Xcode-Menü **Preferences** und dann **Accounts** aus. Wählen Sie im linken Bereich Ihre Apple Developer ID. Klicken Sie auf die Schaltfläche **View Details** auf der rechten Seite. Klicken Sie im Popoverfenster auf die gerundete Schaltfläche **Refresh**. Dies aktualisiert die Liste der Provisioning Profiles (Bereitstellungsprofile). Dies kann einige Minuten dauern. Es wird empfohlen, zwei- bis dreimal auf **Refresh** zu klicken, bis Ihr neues Profil für die Bereitstellung angezeigt wird. Vergewissern Sie sich außerdem, dass die Bundle-ID dieses Xcode-Projekts der Bundle-ID entspricht, die der App-ID und dem von Ihnen bisher erstellten Bereitstellungsprofil zugeordnet ist.

    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-01.png)

9. Klicken Sie unter **Targets** auf **Quickstart**, erweitern Sie **Code Signing Identity**, und wählen Sie dann unter **Debug** das neue Profil. Hierdurch wird sichergestellt, dass das Xcode-Projekt das neue Profil für Codesignierung verwendet. Als Nächstes laden Sie das Zertifikat in Azure hoch.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-step17.png)

<!--HONumber=49-->