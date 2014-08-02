

1.  Falls Sie Ihre App noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Apps zu [Submit an app page](http://go.microsoft.com/fwlink/p/?LinkID=266582), melden Sie sich mit Ihrem Microsoft-Konto an, und klicken Sie dann auf **App-Name**.

      ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-submit-win8-app.png)

2.  Geben Sie einen Namen für die App unter **App-Name** ein, klicken Sie auf **App-Name reservieren** und dann auf **Speichern**.

      ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-name.png)

      Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre App erstellt.

3.  Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie erstellt haben, als Sie das Lernprogramm [Erste Schritte mit Mobile Services](/en-us/documentation/articles/mobile-services-windows-store-get-started/) abgeschlossen haben.

4.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie auf **Speichern**. Klicken Sie dann auf **App mit Store verknüpfen**.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-store-association.png)
 
    Daraufhin wird der Assistent **App mit Windows Store verknüpfen** angezeigt.

1.  Klicken Sie im Assistenten auf **Anmelden**. Melden Sie sich dann mit Ihrem Microsoft-Konto an.

2.  Wählen Sie die App aus, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter** und dann auf **Zuordnen**.

      ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-select-app-name.png)

      Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.    

3.  Klicken Sie auf der Windows-Entwicklungscenter-Seite für die neue App auf **Dienste**.

      ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit-app.png) 

4.  Klicken Sie auf der Seite "Dienste" auf **Live-Dienste-Website** unter **Azure Mobile Services**.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

5.  Klicken Sie auf **Authentifizieren des Diensts**, und notieren Sie sich die Werte von **Geheimer Clientschlüssel** und **Paket-Sicherheits-ID (SID)**.

      ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    **Sicherheitshinweis**

    Der geheime Clientschlüssel und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Informationen nicht weiter, und verteilen Sie sie nicht mit Ihrer Anwendung.

6.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihre App.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-selection.png)

1.  Klicken Sie auf die Registerkarte **Push** und dann auf **Erweiterten Push aktivieren**. Wählen Sie anschließend **Ja** aus, um die Konfigurationsänderung zu übernehmen.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-enable-enhanced-push.png)

    Daraufhin wird die Konfiguration Ihres mobilen Diensts aktualisiert, sodass erweiterte Pushbenachrichtigungsfunktionen, die von Notification Hubs zur Verfügung gestellt werden, genutzt werden können. Die Nutzung einiger Notification Hubs ist bei Ihrem kostenpflichtigen mobilen Dienst kostenlos. Weitere Informationen finden Sie unter [Mobile Services – Preisdetails](http://go.microsoft.com/fwlink/p/?LinkID=311786).

    **Wichtig**

    Durch diesen Vorgang werden Ihre Pushanmeldeinformationen zurückgesetzt, und darüber hinaus wird das Verhalten der Pushmethoden in Ihren Skripts geändert. Diese Änderungen können nicht mehr rückgängig gemacht werden. Verwenden Sie diese Methode nicht, wenn Sie einer Produktionsumgebung im mobilen Dienst einen Benachrichtigungshub hinzufügen möchten. Anweisungen zum Aktivieren der erweiterten Pushbenachrichtigungen in einer Produktionsumgebung im mobilen Dienst finden Sie in [dieser Anleitung](http://go.microsoft.com/fwlink/p/?LinkId=391951).

2.  Geben Sie die Werte für **Geheimer Clientschlüssel** und **Paket-SID** ein, die Sie von WNS in Schritt 4 erhalten haben. Klicken Sie dann auf **Speichern**.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-push-tab.png)

    >[WACOM.NOTE]Wenn Sie Ihre WNS-Anmeldeinformationen für erweiterte Pushbenachrichtigungen auf der Registerkarte **Push** im Portal festlegen, werden diese für Notification Hubs freigegeben, sodass der Benachrichtigungshub mit Ihrer App konfiguriert wird.
