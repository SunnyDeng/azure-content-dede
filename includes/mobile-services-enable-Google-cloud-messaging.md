1.  Navigieren Sie zur Website <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a>, melden Sie sich mit den Anmeldeinformationen für Ihr Google-Konto an, und klicken Sie dann auf **CREATE PROJECT**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png) 

    > [WACOM.NOTE]Wenn Sie bereits über ein Projekt verfügen, werden Sie nach der Anmeldung zur Seite **Projects** weitergeleitet. Wenn Sie über das Dashboard ein neues Projekt erstellen möchten, erweitern Sie **API Project**, klicken Sie unter **Other projects** auf **Create...**, geben Sie einen Projektnamen ein, und klicken Sie auf **Create project**.

2.  Geben Sie einen Projektnamen ein, akzeptieren Sie die Nutzungsbedingungen, und klicken Sie auf **Create**. Führen Sie die geforderte Verifizierung über SMS aus, und klicken Sie erneut auf **Create**.

3.  Notieren Sie sich die Projektnummer im Abschnitt **Projects**.

    Später im Lernprogramm legen Sie diesen Wert als die Variable "PROJECT\_ID" im Client fest.

4.  Klicken Sie in der linken Spalte auf **APIs & auth**, scrollen Sie anschließend nach unten, und klicken Sie auf den Schalter, um **Google Cloud Messaging for Android** zu aktivieren. Akzeptieren Sie die Nutzungsbedingungen.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5.  Klicken Sie auf **Credentials** und anschließend auf **CREATE NEW KEY**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

6.  Klicken Sie in **Create a new key** auf **Server key**. Klicken Sie im nächsten Fenster auf **Create**.

  	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

7.  Notieren Sie sich den Wert **API key**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

    Als Nächstes verwenden Sie diesen API-Schlüsselwert, um Mobile Services für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

  