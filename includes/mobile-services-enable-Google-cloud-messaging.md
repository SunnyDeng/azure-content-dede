

>[AZURE.NOTE]Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um diesen Vorgang abzuschließen. Um ein neues Google-Konto zu erstellen, wechseln Sie zu <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.


1. Navigieren Sie zur <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a>-Website, melden Sie sich mit Ihren Google-Konto-Anmeldedaten an, und klicken Sie anschließend auf **Projekt erstellen**.

   ![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	>[AZURE.NOTE]Wenn Sie bereits über ein Projekt verfügen, werden Sie nach der Anmeldung zur Seite <strong>Projects</strong> weitergeleitet. Um ein neues Projekt über das Dashboard zu erstellen, erweitern Sie <strong>API Project</strong>, klicken Sie auf <strong>Create...</strong> unter <strong>Other projects</strong>, geben Sie dann einen Projektnamen ein, und klicken Sie auf <strong>Create project</strong>.

2. Geben Sie einen Projektnamen ein, akzeptieren Sie die Nutzungsbedingungen, und klicken Sie auf **Erstellen**. Führen, falls angefordert, die SMS-Verifizierung aus, und klicken Sie erneut auf **Erstellen**.

3. Notieren Sie sich die Projektnummer im Abschnitt **Projekte**. 

	Zu einem späteren Zeitpunkt im Lernprogramm legen Sie diesen Wert im Client als PROJECT_ID-Variable fest.

4. Erweitern Sie in der linken Spalte **APIs & auth**, klicken Sie auf **APIs**, scrollen Sie anschließend nach unten, und klicken Sie auf die Umschaltfläche, um **Google-Cloud-Messaging für Android** zu aktivieren, und akzeptieren Sie die Nutzungsbedingungen. 

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Klicken Sie auf **Anmeldedaten**, und klicken Sie dann auf **Einen neuen Schlüssel erstellen** 

   ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. Klicken Sie unter **Einen neuen Schlüssel erstellen** auf **Serverschlüssel**. Klicken Sie im nächsten Fenster auf **Erstellen**.

   ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. Notieren Sie den **API-Schlüssel**-Wert.

  	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 

	Mit diesem API-Schlüsselwert geben Sie Azure die Möglichkeit, sich mit GCM zu authentifizieren und Pushbenachrichtigungen im Auftrag Ihrer App zu verschicken.


<!--HONumber=49-->