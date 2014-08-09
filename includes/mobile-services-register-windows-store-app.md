
1. Falls Sie Ihre Anwendung noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Anwendungen zu [Anwendungsseite senden], melden Sie sich mit Ihrem Microsoft-Konto an und klicken Sie dann auf **Anwendungsname**.

   	![][0]

2.  Geben Sie einen Namen für Ihre Anwendung unter **Anwendungsname** ein, klicken Sie auf **Anwendungsname reservieren** und dann auf **Speichern**.
    
    ![][1]
        
    Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3.  Öffnen Sie im Visual Studio 2012 Express für Windows 8 das Projekt, das Sie beim Abschluss des Lernprogramms [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started/#create-new-service) erstellt haben.

4.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Store**, und klicken Sie dann auf **Anwendung dem Store zuordnen**.

	 ![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)
 
	Auf diese Weise wird der Assistent zum **Zuordnen Ihrer Anwendung zum Windows Store** angezeigt.

1.  Klicken Sie im Assistenten auf **Anmelden**. Melden Sie sich dann mit Ihrem Microsoft-Konto ein.

2.  Wählen Sie die Anwendung aus, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter** und dann auf **Zuordnen**.
    
    ![][3]
        
    Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.    

3.  Klicken Sie auf der Windows-Entwicklungscenter-Seite für Ihre neue Anwendung auf **Dienste**.
    
    ![][4] 

4.  Klicken Sie auf der Seite "Dienste" auf **Live-Dienste-Website** unter **Azure Mobile Services**.
    
    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png)

5.  Klicken Sie auf **Authentifizieren des Diensts** und notieren Sie sich die Werte von **Geheimer Clientschlüssel** und **Paket-Sicherheits-ID (SID)**.
    
    ![][6]


   
    > [WACOM.NOTE]Der geheime Clientschlüssel und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Informationen nicht weiter und verteilen Sie sie nicht mit Ihrer Anwendung.


6.  Melden Sie sich beim [Azure-Verwaltungsportal][2] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre Anwendung.

 	![](./media/mobile-services-register-windows-store-app/mobile-services-selection.png)

1.  Klicken Sie auf die Registerkarte **Push**, geben Sie die Werte für **Geheimer Clientschlüssel** und **Paket-SID** ein, die Sie von WNS
    in Schritt 4 erhalten haben. Klicken Sie dann auf **Speichern**.

 	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png) 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->


<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-register-windows-store-app/mobile-services-store-association.png
[3]: ./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png
[4]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png
[5]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png
[6]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png
[7]: ./media/mobile-services-register-windows-store-app/mobile-services-selection.png
[8]: ./media/mobile-services-register-windows-store-app/mobile-push-tab.png

<!-- URLs. -->
[Get started with Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure Management Portal]: https://manage.windowsazure.com/