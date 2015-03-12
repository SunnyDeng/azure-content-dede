

1. Wenn Sie Ihre App noch nicht registriert haben, navigieren Sie zu [Eine App-Seite abschicken] im "Dev-Center" für Windows Store-Apps, melden Sie sich mit Ihrem Microsoft-Konto an, und klicken Sie dann auf **App-Name**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Geben Sie in **App-Name** einen Namen für Ihre App ein, klicken Sie auf **App-Name reservieren**, und klicken Sie dann auf **Speichern**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

   	Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3. Öffnen Sie in Visual Studio das Projekt, das Sie nach Beenden des Lernprogramms **Erste Schritte mit Mobile Services** erstellt haben.

4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Speichern**, und klicken Sie dann auf **App mit Store verknüpfen...**. 

  	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

   	Der Assistent **App mit Windows Store verknüpfen** wird angezeigt.

5. Klicken Sie im Assistenten auf **Anmelden**, und melden Sie sich anschließend mit Ihrem Microsoft-Konto an.

6. Wählen Sie die App, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter**, und klicken Sie dann auf **Zuordnen**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

   	Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.    

7. (Optional) Wiederholen Sie die Schritte 4 bis 6, um auch das Windows Phone Store-Projekt mit der universellen Windows-App zu registrieren.

8. Klicken Sie in der Seite von Windows Dev Center für Ihre neue App auf **Dienste**. 

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png) 

9. Klicken Sie auf der Seite "Dienste" auf **Live Services-Website** unter**Azure Mobile Services**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. Klicken Sie auf **Authentifizieren des Diensts**, und notieren Sie die Werte von **Clientschlüssel** und **Paket-Sicherheits-ID (SID)**. 

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    > [AZURE.NOTE] Der geheime Clientschlüssel und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Informationen nicht weiter und verteilen Sie sie nicht mit Ihrer Anwendung.

11. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre App.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. Klicken Sie auf die Registerkarte **Push**, und geben Sie die Werte für **Geheimer Clientschlüssel** und **Paket-SID** an, die Sie aus WNS erhalten haben, klicken Sie anschließend auf **Speichern**.

	>[AZURE.NOTE]Wenn Sie dieses Lernprogramm mit einem älteren mobilen Dienst durchführen, finden Sie am unteren Rand der Registerkarte **Push** möglicherweise eine Verknüpfung namens **Erweiterten Push aktivieren**. Klicken Sie jetzt darauf, um Ihren mobilen Dienst für die Integration mit Notification Hubs zu aktualisieren. Diese Änderung kann nicht mehr rückgängig gemacht werden. Einzelheiten zur Aktivierung der erweiterten Pushbenachrichtigungen in einem mobilen Produktionsservice finden Sie in <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">diesem Leitfaden</a>. 

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-push-tab.png)

	>[AZURE.NOTE]Wenn Sie Ihre WNS-Anmeldedaten für die erweiterte Pushbenachrichtigungen auf der Registerkarte **Push** im Portal einstellen, werden diese mit Benachrichtigungs-Hubs geteilt, um das Benachrichtigungs-Hub für Ihre App zu konfigurieren.

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-get-started/
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
<!--HONumber=42-->
