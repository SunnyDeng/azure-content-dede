
1. Navigieren Sie zur <a href="http://cloud.google.com/console" target="_blank">Google-Cloudkonsole</a> Website, melden Sie sich mit Ihren Google-Anmeldeinformationen an, und klicken Sie auf **Projekt erstellen**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	>[AZURE.NOTE]Wenn Sie bereits über ein Projekt verfügen, werden Sie nach der Anmeldung zur Seite <strong>Projekte</strong> weitergeleitet. Wenn Sie über das Dashboard ein neues Projekt erstellen möchten, erweitern Sie <strong>API-Projekt</strong>, klicken Sie unter <strong>Andere Projekte</strong> auf <strong>Erstellen...</strong>, geben Sie einen Projektnamen ein, und klicken Sie auf <strong>Projekt erstellen</strong>.

2. Geben Sie einen Projektnamen ein, akzeptieren Sie die Nutzungsbedingungen, und klicken Sie auf **Erstellen**. Führen, falls angefordert, die SMS-Verifizierung aus, und klicken Sie erneut auf **Erstellen**.

3. Notieren Sie sich die Projektnummer im Abschnitt **Projekte**. 

	Zu einem späteren Zeitpunkt im Lernprogramm legen Sie diesen Wert im Client als die Variable "PROJECT_ID" fest.

4. Erweitern Sie in der linken Spalte **APIs & auth**, klicken Sie auf **APIs**, scrollen Sie anschließend nach unten, und klicken Sie auf die Umschaltfläche, um **Google-Cloud-Messaging für Android** zu aktivieren, und akzeptieren Sie die Nutzungsbedingungen. 

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Klicken Sie auf **Anmeldedaten**, und klicken Sie dann auf **Einen neuen Schlüssel erstellen** 

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. Klicken Sie unter **Einen neuen Schlüssel erstellen** auf **Serverschlüssel**. Klicken Sie im nächsten Fenster auf **Erstellen**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. Notieren Sie den **API-Schlüssel**-Wert.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 

	Diesen API-Schlüsselwert verwenden Sie dazu, Mobile Services zu befähigen, sich mit GCM zu authentifizieren und Pushbenachrichtigungen im Auftrag Ihrer App zu verschicken.


<!--HONumber=45--> 
