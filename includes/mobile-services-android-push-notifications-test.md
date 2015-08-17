
###Einrichten des Android-Emulators für den Testvorgang
Wenn Sie diese App im Emulator ausführen, stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google-APIs unterstützt.

> [AZURE.IMPORTANT]Damit Sie Pushbenachrichtigungen erhalten, müssen Sie ein Google-Konto auf Ihrem Android Virtual Device einrichten (Navigieren Sie im Emulator zu **Einstellungen**, und klicken Sie auf **Konto hinzufügen**). Überprüfen Sie zudem, dass der Emulator mit dem Internet verbunden ist.

1. Klicken Sie unter **Tools** auf **Open Android Emulator Manager**, wählen Sie Ihr Gerät aus, und klicken Sie auf **Edit**.

   	![Android Virtual Device Manager](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app7.png)

2. Wählen Sie **Google APIs** unter **Ziel** aus, und klicken Sie auf **OK**.

   	![Bearbeiten von Android Virtual Device](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app8.png)

3. Klicken Sie in der oberen Symbolleiste auf **Run**, und wählen Sie Ihre App aus. Daraufhin wird der Emulator gestartet und die App ausgeführt.

  Die App ruft die *registrationId* von GCM ab, und registriert sich beim Notification Hub.

###Durch das Einfügen eines neuen Elements wird eine Benachrichtigung generiert.

1. Geben Sie in der App einen sinnvollen Text ein, beispielsweise _Eine neue Mobile Services-Aufgabe hinzufügen_, und klicken Sie dann auf die Schaltfläche **Hinzufügen**.

2. Führen Sie eine Streifbewegung vom oberen Bildschirmrand aus, um das Notification Center des Geräts zu öffnen, sodass die Benachrichtigung angezeigt wird.

	![Anzeigen der Benachrichtigung im Notification Center](./media/mobile-services-android-push-notifications-test/notification-area-received.png)

<!---HONumber=August15_HO6-->