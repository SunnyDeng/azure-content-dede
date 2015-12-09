
1. Navigieren Sie zur [Google Developers Console](https://console.developers.google.com/project), und melden Sie sich mit den Anmeldeinformationen Ihres Google-Kontos an. 
 
2. Klicken Sie auf **Projekt erstellen**, geben Sie einen Projektnamen ein, und klicken Sie auf **Erstellen**. Führen, falls angefordert, die SMS-Verifizierung aus, und klicken Sie erneut auf **Create**.

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)

	 Geben Sie Ihren neuen **Projektnamen** ein und klicken Sie auf **Projekt erstellen**.

3. Notieren Sie sich die Projektnummer im Abschnitt **Projects**. Sie müssen diesen Wert im Client als *PROJECT\_ID*-Variable im Client festlegen.

4. Klicken Sie im Projektdashboard auf **Google-APIs verwenden** > **Cloud Messaging für Android** und dann auf der nächsten Seite auf **API aktivieren**.

5. Klicken Sie im API-Manager auf **Anmeldeinformationen** > **Anmeldeinformationen hinzufügen** > **API-Schlüssel**.

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)

6. Klicken Sie unter **Neuen Schlüssel erstellen** auf **Serverschlüssel**, geben Sie einen Namen für den Schlüssel ein, und klicken Sie dann auf **Erstellen**.

7. Notieren Sie sich den **API KEY**-Wert.

	Mit diesem API-Schlüsselwert geben Sie Azure die Möglichkeit, sich bei GCM zu authentifizieren und Pushbenachrichtigungen im Auftrag Ihrer App zu verschicken.

<!---HONumber=AcomDC_1203_2015-->