<properties urlDisplayName="Import your subscription publish settings file in Visual Studio 2013" pageTitle="Importieren der Datei mit Veröffentlichungseinstellungen in Visual Studio 2013 | Mobile Services" metaKeywords="Azure import publishsettings, mobile services" description="Erfahren Sie, wie Sie eine Datei mit Abonnementveröffentlichungseinstellungen für Ihre Azure Mobile Services-Anwendung in Visual Studio 2013 importieren." title="Import your subscription publish settings file in Visual Studio 2013" documentationCenter="Mobile" services="mobile-services" solutions="" manager="dwrede" editor="" videoId="" scriptId="" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Importieren Ihrer Abonnementdatei mit Veröffentlichungseinstellungen in Visual Studio 2013

Bevor Sie den mobilen Dienst erstellen können, müssen Sie die .publishsettings-Datei aus Ihrem Azure-Abonnement in Visual Studio importieren. Auf diese Weise kann Visual Studio in Ihrem Auftrag eine Verbindung mit Azure herstellen.  

>[WACOM.NOTE]Ab Visual Studio 2013 Update 2 müssen Sie keine Datei mit Veröffentlichungseinstellungen mehr verwenden. Visual Studio kann über die von Ihnen bereitgestellten Anmeldeinformationen eine direkte Verbindung mit Azure herstellen.

1. Öffnen Sie in Visual Studio 2013 den Projektmappen-Explorer, klicken Sie mit der rechten Maustaste auf **Hinzufügen**, und klicken Sie dann auf **Verbundener Dienst...**. 

	![add connected service](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2. Klicken Sie im Dialogfeld "Dienst-Manager" auf **Dienst erstellen...**, und wählen Sie dann im Dialogfeld für das Erstellen mobiler Dienste **Importieren...** aus **Abonnement** aus.  

	![create a new mobile service from VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. Klicken Sie unter "Azure-Abonnements importieren" auf **Abonnementdatei herunterladen**, melden Sie sich ggf. bei Ihrem Azure-Konto an, und klicken Sie auf **Speichern**, wenn Ihr Browser Sie zum Speichern der Datei auffordert.

	![download subscription file in VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

	<div class="dev-callout"><strong>Hinweis</strong> <p>Das Anmeldefenster wird im Browser angezeigt, der sich hinter dem Visual Studio-Fenster befinden kann. Denken Sie daran, sich zu notieren, wo Sie die heruntergeladene ".publishsettings"-Datei gespeichert haben. Wenn das Projekt bereits mit Ihrem Azure-Abonnement verbunden ist, können Sie diesen Schritt überspringen.</p></div> 

4. Klicken Sie auf **Durchsuchen**, navigieren Sie zum Speicherort, an dem Sie die .publishsettings-Datei gespeichert haben, wählen Sie die Datei aus, und klicken Sie auf **Öffnen** und dann auf **Importieren**. 

	![import subscription in VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

	Visual Studio importiert die Daten, die für die Verbindung mit Ihrem Azure-Abonnement erforderlich sind. Wenn Ihr Abonnement bereits über mindestens einen mobilen Dienst verfügt, werden die Dienstnamen angezeigt. 

	<div class="dev-callout"><strong>Sicherheitshinweis</strong> <p>Nach dem Importieren der Veröffentlichungseinstellungen sollten Sie die heruntergeladene ".publishsettings"-Datei löschen, da sie Informationen enthält, die von anderen Personen für den Zugriff auf Ihr Konto verwendet werden können. Sichern Sie die Datei, wenn Sie sie in anderen Projekten mit verbundenen Anwendungen verwenden möchten.</p></div>

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Einmalige Anmeldung für Windows Store-Apps mithilfe von Live Connect]: /de-de/develop/mobile/how-to-guides/register-for-single-sign-on/
[Übermitteln einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-dotnet/
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/get-started-with-users-js/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
