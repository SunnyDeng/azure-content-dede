<properties 
	pageTitle="Erste Schritte mit der Pushbenachrichtigungen (Android) | Mobile Dev Center" 
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Services zum Senden von Pushbenachrichtigungen an Ihre Android-.NET-App." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="ricksal"/>

# Hinzufügen von Pushbenachrichtigungen zur Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push-EC.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung an Ihre Android-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Google Cloud Messaging (GCM) zu dem Schnellstartprojekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung. 

Dieses Lernprogramm erläutert die grundlegenden Schritte:

1. [Aktivieren von Google Cloud Messaging](#register)
2. [Konfigurieren von mobilen Diensten zum Senden von Pushanforderungen](#configure)
5. [Aktualisieren des Servers zum Senden von Pushbenachrichtigungen](#update-server)
7. [Hinzufügen von Pushbenachrichtigungen zur App](#update-app)
8. [Aktivieren von Pushbenachrichtigungen für lokale Tests](#local-testing)
9. [Testen der App mit dem veröffentlichten mobilen Dienst](#test-app)


Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten] abschließen, um Ihr Projekt mit dem mobilen Dienst zu verbinden. Für dieses Lernprogramm benötigen Sie außerdem Visual Studio 2013. 

>[AZURE.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">"Kostenlose Azure-Testversion"</a>. 


##<a id="register"></a>Aktivieren von Google Cloud Messaging

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]


##<a id="configure"></a>Konfigurieren von Mobile Services zum Senden von Pushanforderungen

1. Melden Sie sich am [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre App.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Klicken Sie auf die Registerkarte **Push**, geben Sie den Wert **API-Schlüssel** ein, den Sie zuvor von GCM erhalten haben, und klicken Sie auf **Speichern**.

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

> [AZURE.IMPORTANT] Wenn Sie Ihre GCM-Anmeldeinformationen für erweiterte Pushbenachrichtigungen auf der Registerkarte "Push" im Portal festlegen, werden diese für Notification Hubs freigegeben, sodass der Benachrichtigungshub mit Ihrer App konfiguriert wird.


Ihr mobiler Dienst ist jetzt für GCM und Benachrichtigungshubs konfiguriert.


<h2><a name="download-the-service"></a>Herunterladen des Diensts auf den lokalen Computer</h2>

[AZURE.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

<h2><a name="test-the-service"></a>Testen des mobilen Diensts</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a id="update-server"></a>Aktualisieren des Servers zum Senden von Pushbenachrichtigungen

1. Erweitern Sie im Projektmappen-Explorer von Visual Studio im Projekt für den mobilen Dienst den Ordner **Controller**. Öffnen Sie "TodoItemController.cs". Fügen Sie am Anfang der Datei, die folgenden  `using`-Anweisungen hinzu:


		using System;
		using System.Collections.Generic;

2. Aktualisieren Sie die Definition der `PostTodoItem`-Methode mit dem folgenden Code:  

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

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="update-app"></a>Hinzufügen von Pushbenachrichtigungen zur App

###Prüfen der Version des Android-SDK

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version-EC.md)]


Als Nächstes installieren Sie Google Play Services. Google Cloud Messaging hat für Entwicklung und Testen bezüglich der API-Ebene einige Mindestanforderungen, denen die Eigenschaft **minSdkVersion** im Manifest entsprechen muss. 

Wenn Sie mit einem älteren Gerät testen, lesen Sie [Set Up Google Play Services SDK], um zu ermitteln, wie niedrig Sie diesen Wert einstellen können. Legen Sie den Wert anschließend entsprechend fest.

###Hinzufügen von Google Play Services zum Projekt

[AZURE.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services-EC.md)]

###Hinzufügen des Codes

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push-EC.md)]

<h2><a name="test-app"></a>Testen der App mit dem veröffentlichten mobilen Dienst</h2>

Sie können die App testen, indem Sie ein Android-Telefon direkt mit einem USB-Kabel verbinden oder ein virtuelles Gerät im Emulator verwenden.

###Falls Sie den Emulator für die Tests verwenden...

Stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google APIs unterstützt.

1. Wählen Sie unter **Fenster** zunächst **Android Virtual Device Manager** und anschließend Ihr Gerät aus, und klicken Sie auf **Bearbeiten** (oder auf **Neu**, falls Sie noch keine Geräte haben).

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. Wählen Sie **Google-APIs** (oder **Google-APIs X 86**)  in **Ziel** aus, und klicken Sie dann auf OK.

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	Dies gibt das AVD zur Verwendung von Google-APIs vor. Falls Sie mehrere Versionen des Android SDK installiert haben, stellen Sie sicher, dass die API-Ebene mit der Ebene übereinstimmt, die Sie zuvor in den Projekteigenschaften eingestellt haben.

###<a id="local-testing"></a> Aktivieren von Pushbenachrichtigungen für lokale Tests

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

###Ausführen des Tests

1. Klicken Sie in Eclipse im Menü **Ausführen** auf **Ausführen**, um die App zu starten.

2. Geben Sie in der App einen sinnvollen Text ein, beispielsweise _Eine neue Mobile Services-Aufgabe_, und klicken Sie dann auf die Schaltfläche **Hinzufügen**.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. Führen Sie eine Streifbewegung vom oberen Bildschirmrand aus, um das Notification Center des Geräts zu öffnen, sodass die Benachrichtigung angezeigt wird.


Sie haben dieses Lernprogramm erfolgreich abgeschlossen.


## <a name="next-steps"> </a>Nächste Schritte

<!---In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Android-App für die Arbeit mit Mobile Services und Notification Hubs zum Senden von Pushbenachrichtigungen aufgezeigt. Als Nächstes können Sie das Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer] ausführen, in dem die Verwendung von Tags zum Versand von Pushbenachrichtigungen von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer demonstriert wird.


+ [Senden von Pushbenachrichtigungen an authentifizierte Benutzer]
	<br/>Erfahren Sie, wie Sie Tags verwenden können, um Pushbenachrichtigungen aus einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer zu senden.

+ [Senden von Übertragungsbenachrichtigungen an Abonnenten]
	<br/>Erfahren Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien empfangen können, an denen sie interessiert sind.

+ [Senden von vorlagenbasierten Benachrichtigungen an Abonnenten]
	<br/>Erfahren Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen mit einem mobilen Dienst gesendet werden, ohne dass Sie in Ihrem Back-End auf plattformspezifische Nutzlasten zurückgreifen müssen.
-->
Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Erste Schritte mit Daten]
  <br/>Erfahren Sie mehr über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit Authentifizierung]
  <br/>Erfahren Sie, wie Sie Benutzer Ihrer App mit verschiedenen Kontotypen über Mobile Services authentifizieren können.

* [Was sind Notification Hubs?]
  <br/>Erfahren Sie, wie Notification Hubs funktioniert, um Benachrichtigungen über alle großen Clientplattformen an Ihre Apps zu senden.

* [Debuggen von Notification Hubs-Anwendungen](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Erhalten Sie Anweisungen zur Problembehandlung und zum Debuggen von Notification Hubs-Lösungen. 

* [Verwenden der Android-Clientbibliothek für Mobile Services]
  <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit Android.  
  
<!-- Anchors. -->

[Erstellen eines neuen mobilen Diensts]: #create-service
[Lokales Herunterladen des Diensts]: #download-the-service-locally
[Testen des mobilen Diensts]: #test-the-service
[Herunterladen des GetStartedWithData-Projekts]: #download-app
[Aktualisieren der App für den Datenzugriff über mobile Dienste]: #update-app
[Testen der Android-App mit dem lokal gehosteten Dienst]: #test-locally-hosted
[Veröffentlichen des mobilen Diensts in Azure]: #publish-mobile-service
[Testen der Android-App mit dem in Azure gehosteten Dienst]: #test-azure-hosted
[Testen der App mit dem veröffentlichten mobilen Dienst]: #test-app
[Nächste Schritte]:#next-steps

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
[Erste Schritte mit Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
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

[Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/
[Senden von Übertragungsbenachrichtigungen an Abonnenten]: /de-de/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Senden von vorlagenbasierten Benachrichtigungen an Abonnenten]: /de-de/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/

<!--HONumber=45--> 
