<properties 
	pageTitle="Importieren der Datei mit Veröffentlichungseinstellungen in Visual Studio 2013 | Mobile Services" 
	description="Erfahren Sie, wie Sie eine Datei mit Abonnementveröffentlichungseinstellungen für Ihre Azure Mobile Services-Anwendung in Visual Studio 2013 importieren." 
	documentationCenter="" 
	services="mobile-services" 
	manager="dwrede" 
	editor="" 
	authors="ggailey777"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# Importieren Ihrer Abonnementdatei mit Veröffentlichungseinstellungen in Visual Studio 2013

Bevor Sie den mobilen Dienst erstellen können, müssen Sie die .publishsettings-Datei aus Ihrem Azure-Abonnement in Visual Studio importieren. Auf diese Weise kann Visual Studio in Ihrem Auftrag eine Verbindung mit Azure herstellen.

>[AZURE.NOTE]Ab Visual Studio 2013 Update 2 müssen Sie keine Datei mit Veröffentlichungseinstellungen mehr verwenden. Visual Studio kann über die von Ihnen bereitgestellten Anmeldeinformationen eine direkte Verbindung mit Azure herstellen.

1. Öffnen Sie den Projektmappen-Explorer in Visual Studio 2013, klicken Sie mit der rechten Maustaste auf das Projekt, klicken Sie auf **Hinzufügen** und dann auf **Verbundener Dienst**. 

	![verbundenen Dienst hinzufügen](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2. Klicken Sie im Dialogfeld "Dienste-Manager" auf **Dienst erstellen...**, und wählen Sie im Dialogfeld "Mobilen Dienst erstellen" unter **Abonnement** die Option **&lt;Importieren...&gt;** aus.

	![neuen mobilen Dienst aus VS 2013 erstellen](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. Klicken Sie unter "Azure-Abonnements abonnieren" auf **Abonnementdatei herunterladen**, melden Sie sich ggf. mit Ihrem Azure-Konto an, klicken Sie auf **Speichern**, wenn der Browser Sie zum Speichern der Datei auffordert.

	![Abonnementdatei in VS herunterladen](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

	> [AZURE.NOTE]Das Anmeldefenster wird im Browser angezeigt, der sich hinter dem Visual Studio-Fenster befinden kann. Denken Sie daran, sich zu notieren, wo Sie die heruntergeladene ".publishsettings"-Datei gespeichert haben. Wenn das Projekt bereits mit Ihrem Azure-Abonnement verbunden ist, können Sie diesen Schritt überspringen.

4. Klicken Sie auf **Durchsuchen**, navigieren Sie zum Speicherort der ".publishsettings"-Datei, wählen Sie die Datei aus, klicken Sie auf **Öffnen** und dann auf **Importieren**.

	![Abonnementdatei in VS importieren](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

	Visual Studio importiert die Daten, die für die Verbindung mit Ihrem Azure-Abonnement erforderlich sind. Wenn Ihr Abonnement bereits über mindestens einen mobilen Dienst verfügt, werden die Dienstnamen angezeigt.

	> [AZURE.NOTE]Nach dem Importieren der Veröffentlichungseinstellungen sollten Sie die heruntergeladene ".publishsettings"-Datei löschen, da sie Informationen enthält, die von anderen Personen für den Zugriff auf Ihr Konto verwendet werden können. Sichern Sie die Datei, wenn Sie sie in anderen Projekten mit verbundenen Anwendungen verwenden möchten.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/how-to-guides/register-for-single-sign-on/
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-users-js/

[Azure Management Portal]: https://manage.windowsazure.com/

<!--HONumber=54-->