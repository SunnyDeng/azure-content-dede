>[AZURE.NOTE]Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um diesen Vorgang abzuschließen. Besuchen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>, um ein neues Google-Konto zu erstellen.


1. Navigieren Sie zur Website <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a>, melden Sie sich mit den Anmeldeinformationen für Ihr Google-Konto an, und klicken Sie dann auf **Create Project**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)

	>[AZURE.NOTE]Wenn Sie bereits über ein Projekt verfügen, werden Sie nach der Anmeldung zur Seite <strong>Projects</strong> weitergeleitet. Wenn Sie über das Dashboard ein neues Projekt erstellen möchten, erweitern Sie <strong>API Project</strong>, klicken Sie unter <strong>Other projects</strong> auf <strong>Create...</strong>, geben Sie einen Projektnamen ein, und klicken Sie auf <strong>Create project</strong>.

2. Geben Sie einen Projektnamen ein, akzeptieren Sie die Nutzungsbedingungen, und klicken Sie auf **Create**. Führen, falls angefordert, die SMS-Verifizierung aus, und klicken Sie erneut auf **Create**.

3. Notieren Sie sich die Projektnummer im Abschnitt **Projects**.

	Zu einem späteren Zeitpunkt im Lernprogramm legen Sie diesen Wert im Client als PROJECT\_ID-Variable fest.

4. Erweitern Sie in der linken Spalte **APIs & auth**, klicken Sie auf **APIs**, scrollen Sie anschließend nach unten, und klicken Sie auf **Cloud Messaging for Android**. Klicken Sie auf der nächsten Seite auf **Enable API**, und akzeptieren Sie die Nutzungsbedingungen.

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Klicken Sie auf **Credentials** und anschließend auf **Create new Key**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. Klicken Sie in **Create a new key** auf **Server key**. Klicken Sie im nächsten Fenster auf **Create**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. Notieren Sie sich den **API KEY**-Wert.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png)

	Mit diesem API-Schlüsselwert geben Sie Azure die Möglichkeit, sich bei GCM zu authentifizieren und Pushbenachrichtigungen im Auftrag Ihrer App zu verschicken.

<!---HONumber=August15_HO6-->