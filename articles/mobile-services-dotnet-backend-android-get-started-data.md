<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Erste Schritte mit Daten in Mobile Services

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Windows Store C#">Windows Store C#</a>
    <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a>
    <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS">iOS</a>
    <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title=".NET-Back-End" class="current">.NET-Back-End</a> | 
    <a href="/en-us/develop/mobile/tutorials/get-started-with-data-android/"  title="JavaScript-Back-End">JavaScript-Back-End</a>
</div>

In diesem Thema erfahren Sie, wie Sie Azure Mobile Services als Back-End-Datenquelle für Android-Apps nutzen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst, laden ein Eclipse Android-Projekt für eine App herunter, die Daten im Arbeitsspeicher ablegt, integrieren den mobilen Dienst mit der App und zeigen die Datenänderungen beim Ausführen der App an.

Der mobile Dienst, den Sie in diesem Lernprogramm erstellen, unterstützt die .NET Runtime in Mobile Services. Auf diese Weise können Sie .NET-Sprachen und Visual Studio für die serverseitige Geschäftslogik im mobilen Dienst verwenden. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der [JavaScript-Backend-Version][] dieses Themas.

<div class="dev-callout"><b>Hinweis</b>
<p>F&uuml;r dieses Lernprogramm ist Visual Studio 2013 erforderlich.</p>
</div>

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Erstellen eines neuen mobilen Dienstes][]
2.  [Lokaler Download des Diensts][]
3.  [Testen des mobilen Dienstes][]
4.  [Veröffentlichen des mobilen Diensts in Azure][]
5.  [Download des GetStartedWithData-Projekts][]
6.  [Aktualisieren der App für den Datenzugriff über mobile Dienste][]
7.  [Testen der App mit dem veröffentlichten mobilen Dienst][]

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie ben&ouml;tigen ein Azure-Konto, um dieses Lernprogramm auszuf&uuml;hren. Wenn Sie noch kein Konto haben, k&ouml;nnen Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

## <a name="create-service"></a><span class="short-header">Erstellen eines neuen mobilen Diensts</span>Erstellen eines neuen mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][]]

## <a name="download-the-service"></a><span class="short-header">Herunterladen des Diensts</span>Herunterladen des Diensts auf Ihren lokalen Computer

[WACOM.INCLUDE [mobile-services-download-service-locally][]]

## <a name="test-the-service"></a><span class="short-header">Testen des Diensts</span>Testen des mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][]]

## <a name="publish-the-service"></a><span class="short-header">Veröffentlichen des Diensts</span>Veröffentlichen des mobilen Diensts in Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][]]

## <a name="download-app"></a><span class="short-header">Herunterladen des Projekts</span>Herunterladen des GetStartedWithData-Projekts

### Laden Sie den Beispielcode herunter

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][1]]

### Prüfen der Version des Android-SDK

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version][]]

### Prüfen und Ausführen des Beispielcodes

[WACOM.INCLUDE [mobile-services-android-run-sample-code][]]

## <a name="update-app"></a><span class="short-header">Aktualisieren der App</span>Aktualisieren der App für den Datenzugriff über mobile Dienste

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data][]]

## <a name="test-app"></a><span class="short-header">Testen der App</span>Testen der App mit dem veröffentlichten mobilen Dienst

Die App verwendet nun mobile Dienste als Back-End-Speicher, und Sie können sie entweder im Android-Emulator oder auf einem Android-Telefon gegen den mobilen Dienst testen.

1.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um das Projekt zu starten.

    Dadurch wird Ihre mit dem Android-SDK erstellte App ausgeführt, die mithilfe der Clientbibliothek Elemente aus Ihrem mobilen Dienst abfragt.

2.  Geben Sie wie zuvor einen lesbaren Text ein und klicken Sie auf **Hinzufügen**.

    Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

    Sie können die App neu starten, um zu sehen, dass die Änderungen in die Datenbank in Azure übernommen wurden. Außerdem können Sie die Datenbank im Azure-Verwaltungsportal überprüfen: in den nächsten zwei Schritten werden auf diese Weise die Änderungen in Ihrer Datenbank angezeigt.

3.  Klicken Sie im Azure-Verwaltungsportal auf die Option zum Verwalten der Datenbank, die mit Ihrem mobilen Dienst verknüpft ist.

    ![][]

4.  Führen Sie im Verwaltungsportal eine Abfrage aus, um die von der Windows Store-App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage sieht wie folgt aus, nur dass Sie Ihren Datenbanknamen anstelle von `todolist` verwenden.

        SELECT * FROM [todolist].[todoitems]

    ![][2]

Damit ist das Android-Lernprogramm **Erste Schritte mit Daten** abgeschlossen.

## <a name="next-steps"> </a>Nächste Schritte

Dieses Lernprogramm zeigt die Grundlagen für die Integration von Daten in mobilen Diensten in Android-Apps.

<!--Next, consider completing one of the following tutorials that is based on the GetStartedWithData app that you created in this tutorial:  * [Validate and modify data with scripts]   <br/>Learn more about using server scripts in Mobile Services to validate and change data sent from your app.  * [Refine queries with paging]   <br/>Learn how to use paging in queries to control the amount of data handled in a single request.  Once you have completed the data series, try -->

Probieren Sie die folgenden Lernprogramme aus:

-   [Erste Schritte mit der Authentifizierung][]
    Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen][]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz][]
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.

<!-- Anchors. --> 

<!-- Images. --> 

<!-- URLs. -->

  [Windows Store C\#]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ "Windows Store C#"
  [Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ "Windows Store JavaScript"
  [Windows Phone]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/ "Windows Phone"
  [iOS]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ "iOS"
  [Android]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/ "Android"
  [.NET-Back-End]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/ ".NET-Back-End"
  [JavaScript-Back-End]: /en-us/develop/mobile/tutorials/get-started-with-data-android/ "JavaScript-Back-End"
  [JavaScript-Backend-Version]: /en-us/develop/mobile/tutorials/get-started-with-data-android
  [Erstellen eines neuen mobilen Dienstes]: #create-service
  [Lokaler Download des Diensts]: #download-the-service-locally
  [Testen des mobilen Dienstes]: #test-the-service
  [Veröffentlichen des mobilen Diensts in Azure]: #publish-mobile-service
  [Download des GetStartedWithData-Projekts]: #download-app
  [Aktualisieren der App für den Datenzugriff über mobile Dienste]: #update-app
  [Testen der App mit dem veröffentlichten mobilen Dienst]: #test-app
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [mobile-services-download-service-locally]: ../includes/mobile-services-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [1]: ../includes/download-android-sample-code.md
  [mobile-services-verify-android-sdk-version]: ../includes/mobile-services-verify-android-sdk-version.md
  [mobile-services-android-run-sample-code]: ../includes/mobile-services-android-run-sample-code.md
  [mobile-services-android-getting-started-with-data]: ../includes/mobile-services-android-getting-started-with-data.md
  []: ./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png
  [Erste Schritte mit der Authentifizierung]: /en-us/develop/mobile/tutorials/get-started-with-users-android
  [Erste Schritte mit Pushbenachrichtigungen]: /en-us/develop/mobile/tutorials/get-started-with-push-android
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
