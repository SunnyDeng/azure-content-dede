<properties linkid="develop-mobile-how-to-guides-import-publish-settings" urlDisplayName="Import your subscription publish settings file in Visual Studio 2013" pageTitle="Import your publish settings file in Visual Studio 2013 | Mobile Services" metaKeywords="Azure import publishsettings, mobile services" description="Learn how to import a subscription publish settings file for your Azure Mobile Services application in Visual Studio 2013." title="Import your subscription publish settings file in Visual Studio 2013" documentationCenter="Mobile" services="" solutions="" manager="" editor="" videoId="" scriptId="" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Importieren Ihrer Abonnementdatei mit Veröffentlichungseinstellungen in Visual Studio 2013

Bevor Sie den mobilen Dienst erstellen können, müssen Sie die .publishsettings-Datei aus Ihrem Azure-Abonnement in Visual Studio importieren. Auf diese Weise kann Visual Studio in Ihrem Auftrag eine Verbindung mit Azure herstellen.

1.  Öffnen Sie den Projektmappen-Explorer in Visual Studio 2013, klicken Sie mit der rechten Maustaste auf das Projekt, klicken Sie auf **Hinzufügen** und dann auf **Verbundener Dienst**.

    ![verbundenen Dienst hinzufügen][verbundenen Dienst hinzufügen]

2.  Klicken Sie im Dienste-Manager-Dialogfeld auf **Dienst erstellen** und wählen Sie **Importieren** aus **Abonnement** im Dialogfeld "Mobilen Dienst erstellen" aus.

    ![neuen mobilen Dienst aus VS 2013 erstellen][neuen mobilen Dienst aus VS 2013 erstellen]

3.  Klicken Sie unter "Azure-Abonnements abonnieren" auf **Abonnementdatei herunterladen**, melden Sie sich ggf. mit Ihrem Azure-Konto an, klicken Sie auf **Speichern**, wenn der Browser Sie zum Speichern der Datei auffordert.

    ![Abonnementdatei in VS herunterladen][Abonnementdatei in VS herunterladen]

    <div class="dev-callout"><strong>Hinweis</strong> <p>Das Anmeldefenster wird im Browser angezeigt, der sich hinter dem Visual Studio-Fenster befinden kann. Denken Sie daran, sich zu notieren, wo Sie die heruntergeladene &quot;.publishsettings&quot;-Datei gespeichert haben. Wenn das Projekt bereits mit Ihrem Azure-Abonnement verbunden ist, k&ouml;nnen Sie diesen Schritt &uuml;berspringen.</p></div>

4.  Klicken Sie auf **Durchsuchen**, navigieren Sie zum Speicherort der ".publishsettings"-Datei, wählen Sie die Datei aus, klicken Sie auf **Öffnen** und dann auf **Importieren**.

    ![Abonnementdatei in VS importieren][Abonnementdatei in VS importieren]

    Visual Studio importiert die Daten, die für die Verbindung mit Ihrem Azure-Abonnement erforderlich sind. Wenn Ihr Abonnement bereits über mindestens einen mobilen Dienst verfügt, werden die Dienstnamen angezeigt.

    <div class="dev-callout"><strong>Sicherheitshinweis</strong> <p>Nach dem Importieren der Ver&ouml;ffentlichungseinstellungen sollten Sie die heruntergeladene &quot;.publishsettings&quot;-Datei l&ouml;schen, da sie Informationen enth&auml;lt, die von anderen Personen f&uuml;r den Zugriff auf Ihr Konto verwendet werden k&ouml;nnen. Sichern Sie die Datei, wenn Sie sie in anderen Projekten mit verbundenen Anwendungen verwenden m&ouml;chten.</p></div>

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [verbundenen Dienst hinzufügen]: ./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png
  [neuen mobilen Dienst aus VS 2013 erstellen]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png
  [Abonnementdatei in VS herunterladen]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png
  [Abonnementdatei in VS importieren]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png
