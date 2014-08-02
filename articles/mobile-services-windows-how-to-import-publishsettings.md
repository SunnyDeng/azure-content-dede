<properties linkid="develop-mobile-how-to-guides-import-publish-settings" urlDisplayName="Import your subscription publish settings file in Visual Studio 2013" pageTitle="Import your publish settings file in Visual Studio 2013 | Mobile Services" metaKeywords="Azure import publishsettings, mobile services" description="Learn how to import a subscription publish settings file for your Azure Mobile Services application in Visual Studio 2013." title="Import your subscription publish settings file in Visual Studio 2013" documentationCenter="Mobile" services="" solutions="" manager="" editor="" videoId="" scriptId="" authors="" />

Importieren Ihrer Abonnementdatei mit Veröffentlichungseinstellungen in Visual Studio 2013
==========================================================================================

Bevor Sie den mobilen Dienst erstellen können, müssen Sie die .publishsettings-Datei aus Ihrem Azure-Abonnement in Visual Studio importieren. Auf diese Weise kann Visual Studio für Sie eine Verbindung zu Azure herstellen.

1.  Öffnen Sie den Projektmappen-Explorer in Visual Studio 2013, klicken Sie mit der rechten Maustaste auf das Projekt, klicken Sie auf **Add** und dann auf **Connected Service...**.

    ![verbundenen Dienst hinzufügen](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2.  Klicken Sie im Service Manager-Dialogfeld auf **Create Service...** und wählen Sie **&lt;Import...\>** aus **Subscription** im Dialogfeld "Create Mobile Service" aus.

    ![neuen mobilen Dienst aus VS 2013 erstellen](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3.  Klicken Sie unter Import Azure Subscriptions auf **Download subscription file**, melden Sie sich ggf. mit Ihrem Azure-Konto an und klicken Sie auf **Save**, wenn der Browser Sie zum Speichern der Datei auffordert.

    ![Abonnementdatei in VS herunterladen](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

    **Hinweis**

    Das Anmeldefenster wird im Browser angezeigt, der sich hinter dem Visual Studio-Fenster befinden kann. Denken Sie daran, sich zu notieren, wo Sie die heruntergeladene ".publishsettings"-Datei gespeichert haben. Wenn das Projekt bereits mit Ihrem Azure-Abonnement verbunden ist, können Sie diesen Schritt überspringen.

4.  Klicken Sie auf **Browse**, navigieren Sie zum Speicherort der ".publishsettings"-Datei, wählen Sie die Datei aus, klicken Sie auf **Open** und dann auf **Import**.

    ![Abonnementdatei in VS importieren](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

    Visual Studio importiert die Daten, die für die Verbindung zu Ihrem Azure-Abonnement erforderlich sind. Wenn Ihr Abonnement bereits über mindestens einen mobilen Dienst verfügt, werden die Dienstnamen angezeigt.

    **Sicherheitshinweis**

    Nach dem Importieren der Veröffentlichungseinstellungen sollten Sie die heruntergeladene ".publishsettings"-Datei löschen, da sie Informationen enthält, die von anderen Personen für den Zugriff auf Ihr Konto verwendet werden können. Sichern Sie die Datei, wenn Sie sie in anderen Projekten mit verbundenen Anwendungen verwenden möchten.


