1.  Falls Sie Ihre Anwendung noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Anwendungen zu [Anwendungsseite senden][Anwendungsseite senden], melden Sie sich mit Ihrem Microsoft-Konto an und klicken Sie dann auf **Anwendungsname**.

    ![][0]

2.  Geben Sie einen Namen für Ihre Anwendung unter **Anwendungsname** ein, klicken Sie auf **Anwendungsname reservieren** und dann auf **Speichern**.

    ![][1]

    Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3.  Öffnen Sie in Visual Studio das Projekt, das Sie bei Ausführung des Lernprogramms **Erste Schritte mit Mobile Services** erstellt haben.

4.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Store**, und klicken Sie dann auf **Anwendung dem Store zuordnen**.

    ![][2]

    Auf diese Weise wird der Assistent zum **Zuordnen Ihrer Anwendung zum Windows Store** angezeigt.

5.  Klicken Sie im Assistenten auf **Anmelden**. Melden Sie sich dann mit Ihrem Microsoft-Konto ein.

6.  Wählen Sie die Anwendung aus, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter** und dann auf **Zuordnen**.

    ![][3]

    Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.

7.  (Optional) Wiederholen Sie die Schritte 4 bis 6, um auch das Windows Phone Store-Projekt mit der universellen Windows-App zu registrieren.

8.  Klicken Sie auf der Windows-Entwicklungscenter-Seite für Ihre neue Anwendung auf **Dienste**.

    ![][4]

9.  Klicken Sie auf der Seite "Dienste" auf **Live-Dienste-Website** unter **Azure Mobile Services**.

    ![][5]

10. Klicken Sie auf **Authentifizieren des Diensts** und notieren Sie sich die Werte von **Geheimer Clientschlüssel** und **Paket-Sicherheits-ID (SID)**.

    ![][6]

    <div class="dev-callout"><b>Sicherheitshinweis</b>
<p>Der geheime Clientschl&uuml;ssel und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Informationen nicht weiter und verteilen Sie sie nicht mit Ihrer Anwendung.</p>
</div>

11. Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre Anwendung.

    ![][7]

12. Klicken Sie auf die Registerkarte **Push**, und geben Sie die Werte für **Geheimer Clientschlüssel** und **Paket-SID** ein, die Sie von WNS erhalten haben. Klicken Sie dann auf **Speichern**.

    > [WACOM.NOTE]Wenn Sie dieses Lernprogamm mit einem älteren mobilen Dienst ausführen, wird unten auf der Registerkarte **Push** unter Umständen ein Link mit dem Text **Erweiterten Push aktivieren** angezeigt. Klicken Sie jetzt darauf, um Ihren mobilen Dienst für die Integration mit Notification Hubs zu aktualisieren. Diese Änderung kann nicht mehr rückgängig gemacht werden. Einzelheiten zum Aktivieren der erweiterten Pushbenachrichtigungen in einem mobilen Dienst einer Produktionsumgebung finden Sie in [dieser Anleitung][dieser Anleitung].

    ![][8]

    > [WACOM.NOTE]Wenn Sie Ihre WNS-Anmeldeinformationen für erweiterte Pushbenachrichtigungen auf der Registerkarte **Push** im Portal festlegen, werden diese für Notification Hubs freigegeben, um den Benachrichtigungshub für Ihre App zu konfigurieren.



  [Anwendungsseite senden]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  [0]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png
  [2]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-store-association.png
  [3]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png
  [4]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png
  [5]: ./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png
  [6]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [7]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-selection.png
  [dieser Anleitung]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [8]: ./media/mobile-services-notification-hubs-register-windows-store-app/mobile-push-tab.png
