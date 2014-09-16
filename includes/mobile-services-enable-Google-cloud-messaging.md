

<div class="dev-callout"><b>Hinweis</b>
<p>
Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Besuchen Sie die Seite [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302), um ein neues Google-Konto zu erstellen.</p></div>

1.  Navigieren Sie zur [Google Cloud Console](http://cloud.google.com/console)-Webseite, melden Sie sich mit Ihren Google-Anmeldeinformationen an, und klicken Sie auf **CREATE PROJECT**.

      ![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

    <div class="dev-callout"><b>Hinweis</b>
	<p>
    Wenn Sie bereits über ein Projekt verfügen, werden Sie nach der Anmeldung zur Seite **Projects** weitergeleitet. Wenn Sie über das Dashboard ein neues Projekt erstellen möchten, erweitern Sie **API Project**, klicken Sie unter **Other projects** auf **Create...**, geben Sie einen Projektnamen ein, und klicken Sie auf **Create project**.</p></div>

2.  Geben Sie einen Projektnamen ein, akzeptieren Sie die Nutzungsbedingungen, und klicken Sie auf **Create**. Führen Sie die geforderte Verifizierung über SMS aus, und klicken Sie erneut auf **Create**.

3.  Notieren Sie sich die Projektnummer im Abschnitt **Projects**.

    Später im Lernprogramm legen Sie diesen Wert als die Variable "PROJECT\_ID" im Client fest.

4.  Klicken Sie in der linken Spalte auf **APIs & auth**, scrollen Sie anschließend nach unten, und klicken Sie auf den Schalter, um **Google Cloud Messaging for Android** zu aktivieren. Akzeptieren Sie die Nutzungsbedingungen.

    ![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5.  Klicken Sie auf **Credentials** und anschließend auf **CREATE NEW KEY**.

      ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6.  Klicken Sie in **Create a new key** auf **Server key**. Klicken Sie im nächsten Fenster auf **Create**.

      ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7.  Notieren Sie sich den Wert **API key**.

      ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 


