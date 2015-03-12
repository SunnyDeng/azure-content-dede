

1. Öffnen Sie den Android SDK Manager, indem Sie in der oberen Eclipse-Symbolleiste auf **Window** klicken. Suchen Sie die in den Projekteigenschaften angegebene Android SDK-Zielversion, öffnen Sie sie, und wählen Sie **Google APIs**.

2. Scrollen Sie nach unten zu **Extras**, erweitern Sie diese, und wählen Sie **Google Play Services**, wie unten gezeigt. Klicken Sie auf **Install Packages**. Beachten Sie den SDK-Pfad für den folgenden Schritt. Starten Sie Eclipse neu.

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Installieren Sie das Google Play Services SDK in Ihrem Projekt. Klicken Sie in Eclipse auf **File**, dann auf **Import**. Wählen Sie **Android**, dann **Vorhandenen Android-Code im Arbeitsbereich**, und klicken Sie auf **Next**. Klicken Sie auf **Browse**, navigieren Sie zum Android-SDK-Pfad (normalerweise in einem Ordner namens `adt-bundle-windows-x86_64` inside the folder that contains Eclipse), then go to the `\extras\google\google_play_services\libproject`; wählen Sie dort den Ordner "google-play-services-lib", und klicken Sie auf **OK**. Aktivieren Sie das Kontrollkästchen **Copy projects into workspace**, und klicken Sie dann auf **Finish**.

	![](./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png)

4. Nun müssen Sie einen Bezug von Ihrem Projekt zur soeben importierten Google Play Services SDK-Bibliothek herstellen. 

5. Klicken Sie im **Paket-Explorer** mit der rechten Maustaste auf Ihr Projekt, und wählen Sie *Properties*.
 
6. Im Eigenschaften-Fenster wählen Sie Android auf der linken Seite.

	![](./media/mobile-services-android-get-started-push/mobile-google-set-project-properties.png)


7. Stellen Sie sicher, dass für **Project Build Target** (je nach Ihrer Entwicklungsplattform) `Google APIs x86` (or `Google APIs` für die geeignete SDK-Version ausgewählt ist.

 
8. Im **Library**-Abschnitt wählen Sie **Add**, wählen Sie dann das Google Play Services-Projekt (*google-play-services-lib*) aus, und klicken Sie auf **OK**.

9. Klicken Sie auf **Apply**, dann auf **OK**.




<!--HONumber=45--> 
