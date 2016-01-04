1. Navigieren Sie zur Website [Google Cloud Console](https://console.developers.google.com/project), melden Sie sich mit den Anmeldeinformationen für Ihr Google-Konto an, klicken Sie auf **Projekt auswählen** und dann auf **Projekt erstellen**.

2. Geben Sie einen Projektnamen ein, akzeptieren Sie die Nutzungsbedingungen, und klicken Sie auf **Create**. Führen, falls angefordert, die SMS-Verifizierung aus, und klicken Sie erneut auf **Create**.

3. Notieren Sie sich die Projektnummer im Abschnitt **Projects**.

	Zu einem späteren Zeitpunkt im Lernprogramm legen Sie diesen Wert im Client als PROJECT\_ID-Variable fest.

4. Klicken Sie unter **Google-APIs der Benutzer** auf **Aktivieren und Verwalten von APIs** und dann auf **Cloud Messaging für Android**. Klicken Sie anschließend auf der nächsten Seite auf **API aktivieren**.

5. Klicken Sie auf **Anmeldeinformationen** und dann auf **Anmeldeinformationen hinzufügen**->**API-Schlüssel**.

6. Klicken Sie in **Create a new key** auf **Server key**. Klicken Sie im nächsten Fenster auf **Create**.

7. Notieren Sie sich den **API KEY**-Wert.

	Mit diesem API-Schlüsselwert geben Sie Azure die Möglichkeit, sich bei GCM zu authentifizieren und Pushbenachrichtigungen im Auftrag Ihrer App zu verschicken.

<!---HONumber=AcomDC_1203_2015-->