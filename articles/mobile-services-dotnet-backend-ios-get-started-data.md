<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Erste Schritte mit Daten in Mobile Services

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Windows Store C#">Windows Store C#</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS" class="current">iOS</a><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title=".NET-Backend" class="current">.NET-Backend</a> | <a href="/de-de/develop/mobile/tutorials/get-started-with-data-ios/"  title="JavaScript-Backend">JavaScript-Backend</a></div>

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer iOS-App nutzen können. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und zeigen dann die Datenänderungen beim Ausführen der App an.

Der mobile Dienst, den Sie in diesem Lernprogramm erstellen, unterstützt die .NET Runtime in Mobile Services. Auf diese Weise können Sie .NET-Sprachen und Visual Studio für die serverseitige Geschäftslogik im mobilen Dienst verwenden. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der [JavaScript-Backend-Version][JavaScript-Backend-Version] dieses Themas.

<div class="dev-callout"><b>Hinweis</b>
<p>F&uuml;r dieses Lernprogramm ist Visual Studio 2013 erforderlich.</p>
</div>

<div class="dev-callout"><b>Hinweis</b>
<p>Anhand dieses Lernprogramms soll Ihnen ein besseres Verst&auml;ndnis &uuml;ber die Mobile Services dahingehend vermittelt werden, wie Sie mit deren Hilfe Azure verwenden k&ouml;nnen, um Daten von einer iOS-App abzurufen und zu speichern. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart f&uuml;r mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie eventuell zuerst das Lernprogramm <a href="/de-de/develop/mobile/tutorials/get-started-ios">Erste Schritte mit Mobile Services</a> abschlie&szlig;en.</p>
</div>

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des iOS-App-Projekts][Herunterladen des iOS-App-Projekts]
2. [Erstellen des mobilen Diensts][Erstellen des mobilen Diensts]
3. [Lokaler Download des Diensts][Lokaler Download des Diensts]
4. [Testen des mobilen Diensts][Testen des mobilen Diensts]
5. [Veröffentlichen des mobilen Diensts in Azure][Veröffentlichen des mobilen Diensts in Azure]
6. [Aktualisieren der App zur Verwendung von Mobile Services][Aktualisieren der App zur Verwendung von Mobile Services]
7. [Testen der App mit Mobile Services][Testen der App mit Mobile Services]

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Mobile Services iOS SDK][Mobile Services iOS SDK], [Xcode 4.5][Xcode 4.5] und iOS 5.0 oder höhere Versionen.
+ Visual Studio 2013 (Sie erhalten [Visual Studio Express für das Web][Visual Studio Express für das Web] kostenlos).
+ Ein Microsoft Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].

## <a name="download-app"></a>Herunterladen des GetStartedWithData-Projekts

Dieses Lernprogramm baut auf der [GetStartedWithData-App][GetStartedWithData-App] auf, bei der es sich um eine iOS-App handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-iOS-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1. Laden Sie die GetStartedWithData-[Beispiel-App][GetStartedWithData-App] herunter.

2. Öffnen Sie in Xcode das heruntergeladene Projekt, und prüfen Sie die Datei „TodoService.m“.

   	Beachten Sie, dass es acht **// TODO**-Kommentare gibt. Diese geben die von Ihnen durchzuführenden Schritte an, damit diese App mit dem mobilen Dienst funktionieren kann.

3. Klicken Sie auf die Schaltfläche **Ausführen** (oder drücken Sie die Taste Command+R), um das Projekt neu zu erstellen und die App zu starten.

4. Geben Sie in der App einen Text in das Textfeld ein. Klicken Sie anschließend auf die Schaltfläche **+**.

    ![][0]

   	Beachten Sie, dass der gespeicherte Text in der unteren Liste angezeigt wird.

## <a name="create-service"></a>Erstellen eines neuen mobilen Diensts im Verwaltungsportal

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service-locally"></a>Herunterladen des Diensts auf den lokalen Computer

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

## <a name="test-the-service"></a>Testen des mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <a name="publish-mobile-service"></a>Veröffentlichen des mobilen Diensts in Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

## <a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst

1. Wählen Sie unter Xcode einen Emulator, den Sie bereitstellen (entweder iPhone oder iPad). Klicken Sie auf die Schaltfläche **Ausführen** (oder drücken Sie die Taste Command+R), um das Projekt neu zu erstellen und die App zu starten.

   	Hierdurch wird der mit dem iOS SDK erstellte Azure Mobile Services-Client ausgeführt, der Einträge von Ihrem mobilen Service abfragt.

2. Geben Sie wie zuvor Text im Textfeld ein, und klicken Sie danach auf die Schaltfläche **+**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet. Jedes neue todoItem wird in der SQL-Datenbank, die Sie zuvor für Ihren mobilen Dienst im Azure-Verwaltungsportal konfiguriert haben, gespeichert und aktualisiert.

3. Starten Sie die App neu um zu sehen, dass die Änderungen in die Datenbank in Azure übernommen wurden.

    Außerdem können Sie die Datenbank im Azure-Verwaltungsportal oder im Visual Studio SQL Server-Objekt-Explorer überprüfen. In den nächsten beiden Schritten wird das [Azure-Verwaltungsportal][Azure-Verwaltungsportal] verwendet, um die Änderungen in der Datenbank anzuzeigen.

4. Klicken Sie im Azure-Verwaltungsportal auf die Option zum Verwalten der Datenbank, die mit Ihrem mobilen Dienst verknüpft ist.

    ![][1]

5. Führen Sie im Verwaltungsportal eine Abfrage aus, um die von der App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage sieht wie folgt aus, nur dass Sie Ihren Datenbanknamen anstelle von `todolist` verwenden.

        SELECT * FROM [todolist].[todoitems]

    ![][2]

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"></a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer iOS-App für die Arbeit mit Daten in Mobile Services aufgezeigt.

Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

* [Prüfen und Ändern von Daten mit Skripten][Prüfen und Ändern von Daten mit Skripten]
  <br/>Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mittels Paging][Optimieren von Abfragen mittels Paging]
  <br/>Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie die folgenden weiteren iOS-Lernprogramme ausprobieren:

* [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
  <br/>Informationen zur Authentifizierung von Benutzern Ihrer App.

* [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
  <br/>Verschicken Sie mit Mobile Services eine einfache Pushbenachrichtigung an Ihre App.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

[Windows Store C#]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ "Windows Store C#"
[Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ "Windows Store JavaScript"
[Windows Phone]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/ "Windows Phone"
[iOS]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ "iOS"
[Android]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/ "Android"
[.NET-Backend]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ ".NET-Backend"
[JavaScript-Backend]: /de-de/develop/mobile/tutorials/get-started-with-data-ios/ "JavaScript-Backend"
[JavaScript-Backend-Version]: /de-de/develop/mobile/tutorials/get-started-with-data-ios
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-ios
[Herunterladen des iOS-App-Projekts]: #download-app
[Erstellen des mobilen Diensts]: #create-service
[Lokaler Download des Diensts]: #download-the-service-locally
[Testen des mobilen Diensts]: #test-the-service
[Veröffentlichen des mobilen Diensts in Azure]: #publish-mobile-service
[Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
[Testen der App mit Mobile Services]: #test-app
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Xcode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Express für das Web]: http://go.microsoft.com/p/?linkid=9832232
[Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
[GetStartedWithData-App]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
[mobile-services-ios-download-service-locally]: ../includes/mobile-services-ios-download-service-locally.md
[mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
[mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
[mobile-services-ios-enable-mobile-service-access]: ../includes/mobile-services-ios-enable-mobile-service-access.md
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[1]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[2]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png
[Prüfen und Ändern von Daten mit Skripten]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Optimieren von Abfragen mittels Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-ios
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-ios
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-ios
