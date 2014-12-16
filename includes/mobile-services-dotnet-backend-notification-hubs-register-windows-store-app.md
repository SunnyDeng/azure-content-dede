

1. Falls Sie Ihre Anwendung noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Anwendungen zu [Anwendungsseite senden], melden Sie sich mit Ihrem Microsoft-Konto an, und klicken Sie dann auf **Anwendungsname**.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Geben Sie einen Namen für Ihre Anwendung unter **Anwendungsname** ein, klicken Sie auf **Anwendungsname reservieren** und dann auf **Speichern**.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

   	Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3. Öffnen Sie in Visual Studio das Windows Store-App-Projekt, das Sie während der Bearbeitung des Lernprogramms **Erste Schritte mit Mobile Services** erstellt haben.

4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Store**, und klicken Sie dann auf **Anwendung dem Store zuordnen**. 

  	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

   	Daraufhin wird der Assistent zum **Zuordnen Ihrer Anwendung zum Windows Store** angezeigt.

5. Klicken Sie im Assistenten auf **Anmelden**. Melden Sie sich dann mit Ihrem Microsoft-Konto ein.

6. Wählen Sie die Anwendung aus, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter** und dann auf **Zuordnen**.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.    

7. (Optional) Wiederholen Sie die Schritte 4 bis 6, um auch das Windows Phone Store-Projekt mit der universellen Windows-App zu registrieren.

8. Klicken Sie auf der Windows-Entwicklungscenter-Seite für Ihre neue Anwendung auf**Dienste**. 

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png) 

9. Klicken Sie auf der Seite "Dienste" auf **Live-Dienste-Website** unter **Azure Mobile Services**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. Klicken Sie auf **Authentifizieren des Diensts** und notieren Sie sich die Werte von **Geheimer Clientschlüssel** und **Paket-Sicherheits-ID (SID)**. 

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    <div class="dev-callout"><b>Sicherheitshinweis</b>
	<p>Der geheime Clientschlüssel und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Informationen nicht weiter, und verteilen Sie sie nicht mit Ihrer Anwendung.</p>
    </div> 

11. 	Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und anschließend auf Ihre App.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. Klicken Sie auf die Registerkarte **Push**, und geben Sie die Werte für **Geheimer Clientschlüssel** und **Paket-SID** ein, die Sie von WNS in Schritt 4 erhalten haben. Klicken Sie dann auf **Speichern**.	

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-push-tab.png)

	>[WACOM.NOTE]Wenn Sie Ihre WNS-Anmeldeinformationen für erweiterte Pushbenachrichtigungen auf der Registerkarte **Push** im Portal festlegen, werden diese für Notification Hubs freigegeben, um den Benachrichtigungshub für Ihre App zu konfigurieren.

<!-- URLs. -->
[Übermitteln einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
