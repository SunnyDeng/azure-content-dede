<properties urlDisplayName="Get Started with Push" pageTitle="Erste Schritte mit der Pushbenachrichtigungen (Android) | Mobile Dev Center" metaKeywords="" description="Erfahren Sie mehr über die Verwendung von Azure Mobile Services zum Senden von Pushbenachrichtigungen an Ihre Android-.NET-App." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/26/2014" ms.author="ricksal" />

# Hinzufügen von Pushbenachrichtigungen zur Mobile Services-App

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung an Ihre Android-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Google Cloud Messaging (GCM) zu dem Schnellstartprojekt hinzu. Nach dem Abschluss sendet der mobile Dienst bei jedem Einfügen eines Datensatzes eine Pushbenachrichtigung. 

Dieses Lernprogramm erläutert die grundlegenden Schritte:

1. [Aktivieren von Google Cloud Messaging](#register)
2. [Konfigurieren von mobilen Diensten zum Senden von Pushanforderungen](#configure)
5. [Aktualisieren des Servers zum Senden von Pushbenachrichtigungen](#update-server)
7. [Hinzufügen von Pushbenachrichtigungen zur App](#update-app)
8. [Aktivieren von Pushbenachrichtigungen für lokale Tests](#local-testing)
9. [Testen der App mit dem veröffentlichten mobilen Dienst]


Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten] abschließen, um Ihr Projekt mit Mobile Services zu verbinden. Für dieses Lernprogramm benötigen Sie außerdem Visual Studio 2013. 

>[WACOM.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Kostenlose Azure-Testversion</a>. 


##<a id="register"></a>Aktivieren von Google Cloud Messaging

[WACOM.INCLUDE [Aktivieren von GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]


##<a id="configure"></a>Konfigurieren von Mobile Services zum Senden von Pushanforderungen

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihre App.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Klicken Sie auf die Registerkarte **Push**, geben Sie den **API-Schlüssel** ein, den Sie zuvor von GCM erhalten haben, und klicken Sie auf **Speichern**.

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

    <div class="dev-callout"><b>Wichtig</b>
	<p>Wenn Sie Ihre GCM-Anmeldeinformationen für erweiterte Pushbenachrichtigungen auf der Registerkarte "Push" im Portal festlegen, werden diese für Notification Hubs freigegeben, sodass der Benachrichtigungshub mit Ihrer App konfiguriert wird.</p>
    </div>


Ihr mobiler Dienst ist jetzt für GCM und Benachrichtigungshubs konfiguriert.


<h2><a name="download-the-service"></a>Herunterladen des Diensts auf den lokalen Computer</h2>

[WACOM.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

<h2><a name="test-the-service"></a>Testen des mobilen Dienstes</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a id="update-server"></a>Aktualisieren des Servers zum Senden von Pushbenachrichtigungen

1. Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **Controllers** im mobilen Dienstprojekt. Öffnen Sie TodoItemController.cs. Fügen Sie am Anfang der Datei die folgenden `using`-Anweisungen hinzu:


		using System;
		using System.Collections.Generic;

2. Aktualisieren Sie die `PostTodoItem`-Methode erneut mit folgendem Code:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Dieser Code sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags), nachdem ein todo-Eintrag eingefügt wurde. Falls ein Fehler auftritt, wird vom Code ein Fehlerprotokolleintrag hinzugefügt, der auf der Registerkarte **Protokolle** des mobilen Diensts im Verwaltungsportal angezeigt werden kann.


<h2><a name="publish-the-service"></a>Veröffentlichen des mobilen Diensts in Azure</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="update-app"></a>Hinzufügen von Pushbenachrichtigungen zur App

###Prüfen der Version des Android-SDK

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]


Als Nächstes installieren Sie Google Play Services. Google Cloud Messaging verfügt über einige Mindestanforderungen an den API-Level für Entwicklung und Tests, denen die Eigenschaft **minSdkVersion** im Manifest entsprechen muss. 

	Wenn Sie den Testvorgang mit einem älteren Gerät durchführen, konsultieren Sie [Einrichten des Google Play Services SDK], um zu ermitteln, wie niedrig Sie diesen Wert einstellen können. Legen Sie den Wert anschließend entsprechend fest.

###Hinzufügen von Google Play Services zum Projekt

[WACOM.INCLUDE [Hinzufügen von Play-Diensten](../includes/mobile-services-add-Google-play-services.md)]

###Hinzufügen des Codes

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]

<h2><a name="test-app"></a>Testen der App mit dem veröffentlichten mobilen Dienst</h2>

Sie können die App testen, indem Sie ein Android-Telefon direkt mit einem USB-Kabel verbinden oder ein virtuelles Gerät im Emulator verwenden.

###Falls Sie den Emulator für die Tests verwenden...

Stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google APIs unterstützt.

1. Wählen Sie unter **Fenster** zunächst **Android Virtual Device Manager** und anschließend Ihr Gerät aus, und klicken Sie auf **Bearbeiten** (oder auf **Neu**, falls Sie noch keine Geräte haben).

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. Wählen Sie**Google-APIs** (oder **Google-APIs x86**)  unter **Ziel** aus, und klicken Sie auf "OK".

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	Dies gibt das AVD zur Verwendung von Google-APIs vor. Falls Sie mehrere Versionen des Android SDK installiert haben, stellen Sie sicher, dass die API-Ebene mit der Ebene übereinstimmt, die Se zuvor in den Projekteigenschaften eingestellt haben.

###<a id="local-testing"></a> Aktivieren von Pushbenachrichtigungen für lokale Tests

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

###Ausführen des Tests

1. Klicken Sie in Eclipse im Menü **Ausführen** auf **Ausführen**, um die App zu starten.

2. Geben Sie in der App einen aussagekräftigen Text ein, beispielsweise _Eine neue Mobile Services-Aufgabe_, und klicken Sie dann auf die Schaltfläche **Hinzufügen**.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. Führen Sie eine Streifbewegung vom oberen Bildschirmrand aus, um das Notification Center des Geräts zu öffnen, sodass die Benachrichtigung angezeigt wird.


Sie haben dieses Lernprogramm erfolgreich abgeschlossen.


## <a name="next-steps"> </a>Nächste Schritte

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.


+ [Send push notifications to authenticated users]
	<br/>Erfahren Sie, wie Sie Tags verwenden können, um Pushbenachrichtigungen aus einem Mobile Service nur an einen authentifizierten Benutzer zu senden.

+ [Senden von Übertragungsbenachrichtigungen an Abonnenten]
	<br/>Erfahren Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien empfangen können, die sie interessieren.

+ [Senden von vorlagenbasierten Benachrichtigungen an Abonnenten]
	<br/>Erfahren Sie, wie Sie Vorlagen verwenden können, um Pushbenachrichtigungen von einem Mobile Service zu senden, ohne plattformspezifische Nutzdaten in Ihrem Back-End erstellen zu müssen.
-->
Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Erste Schritte mit Daten]
  <br/>Erfahren Sie mehr zum Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie mehr darüber, wie sich Benutzer Ihrer App mit verschiedenen Kontotypen mit Mobile Services authentifizieren können.

* [Was sind Notification Hubs?]
  <br/>Erfahren Sie, wie Sie mit Notification Hubs arbeiten, um Benachrichtigungen auf allen großen Clientplattformen an Ihre Apps zu senden.

* [Debuggen von Notification Hubs-Anwendungen](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Erhalten Sie Anleitungen zur Problembehandlung und zum Debuggen von Notification Hubs-Lösungen. 

* [Verwenden der Android-Clientbibliothek für Mobile Services]
  <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit Android.  
  
<!-- Anchors. -->

[Erstellen eines neuen mobilen Dienstes]: #create-service
[Lokaler Download des Diensts]: #download-the-service-locally
[Testen des mobilen Dienstes]: #test-the-service
[Download des GetStartedWithData-Projekts]: #download-app
[Aktualisieren der App für den Datenzugriff über mobile Dienste]: #update-app
[Testen der Android Phone-App mit dem lokal gehosteten Dienst]: #test-locally-hosted
[Veröffentlichen des mobilen Diensts in Azure]: #publish-mobile-service
[Testen der Android Phone-App mit dem lokal gehosteten Dienst]: #test-azure-hosted
[Testen der App mit dem veröffentlichten mobilen Dienst]: #test-app
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-dotnet
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/get-started-with-data-js
[JavaScript-Back-End-Version]: /de-de/develop/mobile/tutorials/get-started-with-data-android
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Website mit Codebeispielen für Entwickler]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
[MobileServiceClient-Klasse]: http://go.microsoft.com/fwlink/p/?LinkId=302030

[Verwenden der Android-Clientbibliothek für Mobile Services]: /de-de/documentation/articles/mobile-services-android-how-to-use-client-library

[Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/

[ Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/
[Senden von Übertragungsbenachrichtigungen an Abonnenten]: /de-de/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Senden von vorlagenbasierten Benachrichtigungen an Abonnenten]: /de-de/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
