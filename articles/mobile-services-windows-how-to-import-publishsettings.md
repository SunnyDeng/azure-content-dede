<properties 
	pageTitle="Importieren der Datei mit Veröffentlichungseinstellungen in Visual Studio 2013 | Mobile Services" description="Erfahren Sie, wie Sie eine Datei mit Abonnementveröffentlichungseinstellungen für Ihre Azure Mobile Services-Anwendung in Visual Studio 2013 importieren." 
	documentationCenter="windows" 
	services="mobile-services" 
	manager="dwrede" 
	editor="" 
	authors="ggailey777"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Importieren Ihrer Abonnementdatei mit Veröffentlichungseinstellungen in Visual Studio 2013

Bevor Sie den mobilen Dienst erstellen können, müssen Sie die .publishsettings-Datei aus Ihrem Azure-Abonnement in Visual Studio importieren. Auf diese Weise kann Visual Studio in Ihrem Auftrag eine Verbindung mit Azure herstellen.  

>[AZURE.NOTE] Ab Visual Studio 2013 Update 2 müssen Sie keine Datei mit Veröffentlichungseinstellungen mehr verwenden. Visual Studio kann über die von Ihnen bereitgestellten Anmeldeinformationen eine direkte Verbindung mit Azure herstellen.

1. Öffnen Sie in Visual Studio 2013 den Projektmappen-Explorer, klicken Sie mit der rechten Maustaste auf **Hinzufügen**  und klicken Sie dann auf **Verbundener Dienst...**. 

	![Hinzufügen eines verbundenen Diensts](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2. Klicken Sie im Dialogfeld "Dienste-Manager" auf **Dienst erstellen...** und wählen Sie **&lt;Importieren...&gt;** aus **Abonnement** im Dialogfeld "Mobilen Dienst erstellen" aus.  

	![create a new mobile service from VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. Klicken Sie unter "Azure-Abonnements abonnieren" auf **Abonnementdatei herunterladen**, melden Sie sich ggf. mit Ihrem Azure-Konto an, klicken Sie auf **Speichern**, wenn der Browser Sie zum Speichern der Datei auffordert.

	![download subscription file in VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

	> [AZURE.NOTE] Das Anmeldefenster wird im Browser angezeigt, der sich hinter dem Visual Studio-Fenster befinden kann. Denken Sie daran, sich zu notieren, wo Sie die heruntergeladene ".publishsettings"-Datei gespeichert haben. Wenn das Projekt bereits mit Ihrem Azure-Abonnement verbunden ist, können Sie diesen Schritt überspringen.

4. Klicken Sie auf **Durchsuchen**, navigieren Sie zum Speicherort der ".publishsettings"-Datei, wählen Sie die Datei aus, klicken Sie auf **Öffnen** und dann auf **Importieren**. 

	![import subscription in VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

	Visual Studio importiert die Daten, die für die Verbindung mit Ihrem Azure-Abonnement erforderlich sind. Wenn Ihr Abonnement bereits über mindestens einen mobilen Dienst verfügt, werden die Dienstnamen angezeigt. 

	> [AZURE.NOTE] Nach dem Importieren der Veröffentlichungseinstellungen sollten Sie die heruntergeladene ".publishsettings"-Datei löschen, da sie Informationen enthält, die von anderen Personen für den Zugriff auf Ihr Konto verwendet werden können. Sichern Sie die Datei, wenn Sie sie in anderen Projekten mit verbundenen Anwendungen verwenden möchten.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Einmaliges Anmelden für Windows Store-Apps mithilfe von Live Connect]: /de-de/develop/mobile/how-to-guides/register-for-single-sign-on/
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-dotnet/
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/get-started-with-users-js/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/


<!--HONumber=42-->
