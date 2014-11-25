Der Apple Push Notification Service (APNS) verwendet Zertifikate zur Authentifizierung Ihres mobilen Dienstes. Folgen Sie diesen Anweisungen zum Erstellen der erforderlichen Zertifikate und zum Hochladen auf Ihren mobilen Dienst. Die offizielle Dokumentation der APNS-Merkmale finden Sie unter [Apple Push Notification Service][Apple Push Notification Service].

## Erstellen der Zertifikatsignieranforderungsdatei

Zunächst müssen Sie die Zertifikatsignieranforderungsdatei (CSR-Datei) erstellen, die von Apple zur Generierung eines signierten Zertifikats verwendet wird.

1.  Führen Sie das das Programm "Schlüsselbundverwaltung" im Ordner "Dienstprogramme" aus.

2.  Klicken Sie auf **Schlüsselbundverwaltung**, erweitern Sie **Zertifikatsassistent**, und klicken Sie dann auf **Zertifikat einer Zertifizierungsinstanz anfordern …**.

    ![][0]

3.  Machen Sie entsprechende Angaben für die Felder **E-Mail des Benutzers** sowie **Allgemeiner Name**, vergewissern Sie sich, dass **Auf der Festplatte sichern** gewählt ist, und klicken Sie dann auf **Fortfahren**. Lassen Sie das Feld **E-Mail der Zert.-Instanz** leer, da hier keine Eingabe benötigt wird.

    ![][1]

4.  Geben Sie einen Namen für die Zertifikatsignieranforderungsdatei (CSR-Datei) in **Sichern unter** ein, wählen Sie den Speicherort in **Ort**, und klicken Sie dann auf **Sichern**.

    ![][2]

    Hierdurch wird die CSR-Datei am gewählten Ort gespeichert, wobei Schreibtisch der Standardort ist. Merken Sie sich den für diese Datei gewählten Speicherort.

Anschließend werden Sie Ihre App bei Apple registrieren, Pushbenachrichtigungen aktivieren und diese exportierte CSR hochladen, um ein Pushzertifikat zu erstellen.

## Registrieren der App für Pushbenachrichtigungen

Um von Mobile Services Pushbenachrichtigungen an eine iOS-App senden zu können, muss diese selbst bei Apple registriert und auch für Pushbenachrichtigungen angemeldet werden.

1.  Falls Sie Ihre App noch nicht registriert haben, gehen Sie im Apple Developer Center zum [iOS Provisioning Portal][iOS Provisioning Portal], melden Sie sich mit Ihrer Apple-ID an, klicken Sie auf **Identifiers**, dann auf **App IDs** und schließlich auf das Zeichen **+**, um eine neue App zu registrieren.

    ![][3]

2.  Geben Sie in **Description** einen Namen für Ihre App ein, tragen Sie den Wert *MobileServices.Quickstart* in **Bundle Identifier** ein, markieren Sie die Option "Push Notifications" im Abschnitt "App Services", und klicken Sie dann auf **Continue**. In diesem Beispiel wird die ID **MobileServices.Quickstart** verwendet. Dieselbe ID kann jedoch nicht erneut benutzt werden, da App-IDs über alle Benutzer einzigartig sein müssen. Daher empfiehlt es sich, dass Sie Ihren vollen Namen oder Ihre Initialen an den App-Namen anhängen.

    ![][4]

    Hierdurch wird Ihre App-ID generiert, und Sie werden zum **Übermitteln** der Daten aufgefordert. Klicken Sie auf **Senden**

    ![][5]

    Nachdem Sie **Submit** angeklickt haben, wird der **Registration complete**-Bildschirm geöffnet, wie unten gezeigt. Klicken Sie auf **Done**.

    ![][6]

    > [WACOM.NOTE] Wenn Sie sich dazu entscheiden, einen anderen **Bundle Identifier**-Wert als *MobileServices.Quickstart* vorzugeben, müssen Sie auch die Bundle-ID in Ihrem Xcode-Projekt aktualisieren.

3.  Lokalisieren Sie die soeben erstellte App-ID und klicken Sie auf deren Zeile.

    ![][7]

    Durch Klicken auf die App-ID werden Einzelheiten zur App und die App-ID angezeigt. Klicken Sie auf die Schaltfläche **Einstellungen**.

    ![][8]

4.  Scrollen Sie bis zur Unterseite des Bildschirms und klicken Sie auf die Schaltfläche **Create Certificate...** unter dem Abschnitt **Development Push SSL Certificate**.

    ![][9]

    Hierdurch wird der Assistent "Add iOS Certificate" zur Anzeige gebracht.

    > [WACOM.NOTE] In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Vergewissern Sie sich nur, dass Sie denselben Zertifikattyp einstellen, wenn Sie das Zertifikat zu Mobile Services hochladen.

5.  Klicken Sie auf **Choose File**, browsen Sie zum Speicherort der in der ersten Aufgabe erstellten CSR-Datei und klicken Sie dann auf **Generate**.

    ![][10]

6.  Nach Erstellung des Zertifikats vom Portal klicken Sie auf die Schaltfläche **Download** und dann auf **Done**.

    ![][11]

    Hierdurch wird das Signierzertifikat heruntergeladen und auf Ihrem Computer in Ihrem Download-Ordner gespeichert.

    ![][12]

    > [WACOM.NOTE] Standardmäßig ist die heruntergeladene Datei ein Entwicklungszertifikat namens **aps\_development.cer**.

7.  Doppelklicken Sie auf dem heruntergeladenen Pushzertifikat **aps\_development.cer**.

    Hierdurch wird das neue Zertifikat im Schlüsselbund installiert, wie unten gezeigt:

    ![][13]

    > [WACOM.NOTE] Der Name in Ihrem Zertifikat kann verschieden sein, aber er wird den Vorsatz **Apple Development iOS Push Notification Services:** haben.

Später können Sie mit diesem Zertifikat eine .p12-Datei erstellen und zu Mobile Services hochladen, um Authentifizierung mit APNS zu ermöglichen.

## Erstellen eines Bereitstellungsprofils für die App

1.  Zurück im [iOS Provisioning Portal][iOS Provisioning Portal] wählen Sie **Provisioning Profiles**, dann **All**, und klicken Sie schließlich auf die Schaltfläche **+**, um ein neues Profil zu erstellen. Hierdurch wird der Assistent **Add iOS Provisiong Profile** gestartet

    ![][14]

2.  Unter **Development** wählen Sie **iOS App Development** als Bereitstellungsprofiltyp, und klicken Sie dann auf **Continue**

    ![][15]

3.  Anschließend wählen Sie die App-ID für die Mobile Services Quickstart-App aus der **App ID**-Dropdown-Liste, und klicken Sie auf **Continue**

    ![][16]

4.  Auf dem Bildschirm **Select certificates** wählen Sie das zuvor erstellte Zertifikat und klicken Sie auf **Continue**

    ![][17]

5.  Anschließend wählen Sie die zum Testen zu verwendenden **Devices** und klicken Sie auf **Continue**

    ![][18]

6.  Geben Sie schließlich einen Namen für das Profil im Feld **Profile Name** ein, klicken Sie auf **Generate**, dann auf **Done**

    ![][19]

    Dadurch wird ein neues Bereitstellungsprofil erstellt.

7.  In Xcode öffnen Sie den Organisator, wählen Sie die Geräteansicht, dann **Provisioning Profiles** im Abschnitt **Library** im linken Bereich, und klicken Sie schließlich auf die Schaltfläche **Refresh** an der Unterseite im mittleren Bereich.

    ![][20]

8.  Unter **Targets** klicken Sie auf **Quickstart**, erweitern Sie **Code Signing Identity**, und unter **Debug** wählen Sie dann das neue Profil.

    ![][21]

Hierdurch wird sichergestellt, dass das Xcode-Projekt das neue Profil für Codesignierung verwendet. Als Nächstes laden Sie das Zertifikat in Azure hoch.

  [Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
  [0]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step5.png
  [1]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step6.png
  [2]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step7.png
  [iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
  [3]: ./media/enable-apple-push-notifications/mobile-services-ios-push-02.png
  [4]: ./media/enable-apple-push-notifications/mobile-services-ios-push-03.png
  [5]: ./media/enable-apple-push-notifications/mobile-services-ios-push-04.png
  [6]: ./media/enable-apple-push-notifications/mobile-services-ios-push-05.png
  [7]: ./media/enable-apple-push-notifications/mobile-services-ios-push-06.png
  [8]: ./media/enable-apple-push-notifications/mobile-services-ios-push-07.png
  [9]: ./media/enable-apple-push-notifications/mobile-services-ios-push-08.png
  [10]: ./media/enable-apple-push-notifications/mobile-services-ios-push-10.png
  [11]: ./media/enable-apple-push-notifications/mobile-services-ios-push-11.png
  [12]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step9.png
  [13]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step10.png
  [14]: ./media/enable-apple-push-notifications/mobile-services-ios-push-12.png
  [15]: ./media/enable-apple-push-notifications/mobile-services-ios-push-13.png
  [16]: ./media/enable-apple-push-notifications/mobile-services-ios-push-14.png
  [17]: ./media/enable-apple-push-notifications/mobile-services-ios-push-15.png
  [18]: ./media/enable-apple-push-notifications/mobile-services-ios-push-16.png
  [19]: ./media/enable-apple-push-notifications/mobile-services-ios-push-17.png
  [20]: ./media/enable-apple-push-notifications/mobile-services-ios-push-01.png
  [21]: ./media/enable-apple-push-notifications/mobile-services-ios-push-step17.png
