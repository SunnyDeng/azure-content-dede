>[AZURE.NOTE]Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um diesen Vorgang abzuschließen. Besuchen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>, um ein neues Google-Konto zu erstellen.

1. Navigieren Sie zur Website [Google Cloud Console](https://console.developers.google.com/project), melden Sie sich mit den Anmeldeinformationen für Ihr Google-Konto an, und klicken Sie dann auf **Create Project**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)

2. Geben Sie einen Projektnamen ein, akzeptieren Sie die Nutzungsbedingungen, und klicken Sie auf **Create**. Führen, falls angefordert, die SMS-Verifizierung aus, und klicken Sie erneut auf **Create**.

3. Notieren Sie sich die Projektnummer im Abschnitt **Projects**. Sie benötigen ihn später in diesem Tutorial, um die Android-Manifestdatei aufzufüllen.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

4. Erweitern Sie in der linken Spalte **APIs & auth**, klicken Sie auf **APIs**, scrollen Sie anschließend nach unten, und klicken Sie auf **Cloud Messaging for Android**. Klicken Sie auf der nächsten Seite auf **Enable API**, und akzeptieren Sie die Nutzungsbedingungen.

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)

5. Klicken Sie auf **Anmeldeinformationen** und dann auf **Anmeldeinformationen hinzufügen**->**API-Schlüssel**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

6. Klicken Sie in **Create a new key** auf **Server key**. Klicken Sie im nächsten Fenster auf **Create**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)


   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)

7. Notieren Sie sich den **API KEY**-Wert. Sie verwenden diesen API-Schlüsselwert später für die Konfiguration im Abschnitt „Systemeigener Push“.

<!---HONumber=Nov15_HO1-->