
1. Klicken Sie im [Azure-Portal](https://azure.portal.com/) auf **Durchsuchen** > **App-Dienste**, suchen Sie Ihr Mobile App-Back-End > **Alle Einstellungen** und klicken Sie darauf. Wählen Sie anschließend **Mobil** aus und klicken Sie auf **Push**.

2. Klicken Sie im Pushbenachrichtigungsdienst auf **Windows (WNS)**, geben Sie den **Sicherheitsschlüssel** (geheimer Clientschlüssel) und die **Paket-SID** ein, die Sie auf der Website für die Live-Dienste erhalten haben. Klicken Sie anschließend auf **Speichern**.

    ![Festlegen des GCM-API-Schlüssels im Portal](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Ihr mobiles App-Back-End ist jetzt konfiguriert, um WNS zum Senden von Pushbenachrichtigungen an Ihre Windows-app mittels des zugehörigen Benachrichtigungs-Hubs zu verwenden.

<!---HONumber=AcomDC_1203_2015-->