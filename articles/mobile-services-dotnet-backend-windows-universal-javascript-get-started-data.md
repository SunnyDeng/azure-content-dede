<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-javascript-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Erste Schritte mit Daten in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data][mobile-services-selector-get-started-data]]

In diesem Thema wird gezeigt, wie Sie Azure Mobile Services als Backend-Datenquelle für eine Windows Store-App nutzen können. In diesem Lernprogramm laden Sie ein Visual Studio 2013-Projekt für eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und zeigen dann die Datenänderungen beim Ausführen der App an.

Bei dem mobilen Dienst, den Sie in diesem Lernprogramm erstellen, handelt es sich um einen mobilen .NET-Backend-Dienst. Dank des .NET-Backends können Sie .NET-Sprachen und Visual Studio für die serverseitige Geschäftslogik im mobilen Dienst verwenden und Ihren mobilen Dienst auf Ihrem lokalen Computer ausführen und debuggen. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der JavaScript-Backend-Version dieses Themas.

> [WACOM.NOTE] In diesem Thema wird erläutert, wie Sie mithilfe der in Visual Studio Professional 2013 mit Update 3 enthaltenen Tools einen mobilen Dienst mit einer universellen Windows-App verbinden. Wenn Sie kein Upgrade auf Visual Studio Professional 2013 Update 3 durchführen können oder Ihr mobiles Dienstprojekt manuell einer Windows Store-App hinzufügen möchten, finden Sie in [dieser Version][dieser Version] des Themas weitere Informationen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Herunterladen des Windows Store-App-Projekts][Herunterladen des Windows Store-App-Projekts]
2.  [Erstellen eines mobilen Diensts in Visual Studio][Erstellen eines mobilen Diensts in Visual Studio]
3.  [Lokales Testen des mobilen Dienstprojekts][Lokales Testen des mobilen Dienstprojekts]
4.  [Aktualisieren der App für die Verwendung des mobilen Diensts][Aktualisieren der App für die Verwendung des mobilen Diensts]
5.  [Veröffentlichen des mobilen Diensts in Azure][Veröffentlichen des mobilen Diensts in Azure]
6.  [Testen der App mit dem in Azure gehosteten Dienst][Testen der App mit dem in Azure gehosteten Dienst]
7.  [Anzeigen der in der SQL-Datenbank gespeicherten Daten][Anzeigen der in der SQL-Datenbank gespeicherten Daten]

Für dieses Lernprogramm benötigen Sie Folgendes:

-   Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].
-   [Visual Studio Professional 2013][Visual Studio Professional 2013] Dazu ist eine kostenlose Testversion verfügbar.

## <a name="download-app"></a>Herunterladen des GetStartedWithData-Projekts

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project][mobile-services-windows-universal-javascript-download-project]]

## <a name="create-service"></a>Erstellen eines mobilen Diensts in Visual Studio

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013][mobile-services-dotnet-backend-create-new-service-vs2013]]

1.  Navigieren Sie im Projektmappen-Explorer zum Unterordner **services\\mobileService\\scripts**, öffnen Sie die Skriptdatei "service.js", und beachten Sie die neue globale Variable, die wie folgt aussieht:

        var todolistClient = new WindowsAzure.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

    In diesem Code wird der Zugriff auf den neuen mobilen Dienst in Ihrer App über globale Variable bereitgestellt. Der Client wird durch Angabe des URI und des Anwendungsschlüssels des neuen mobilen Diensts erstellt. Da Sie einen Verweis auf das Skript zur Datei default.html hinzugefügt haben, ist diese Variable in allen Skriptdateien verfügbar, die von dieser Seite aus referenziert werden.

2.  Öffnen Sie die Projektdatei "default.html", suchen Sie den Verweis auf die neue Skriptdatei "service.js", und stellen Sie sicher, dass der im Verweis angegebene Pfad folgendermaßen lautet:

        <script src="/services/mobileServices/scripts/todolist.js">

    Gegenwärtig gibt es einen Fehler in Visual Studio, der dazu führt, dass im Pfad ein falscher Ordnername erzeugt wird.

3.  Klicken Sie mit der rechten Maustaste auf das Windows Phone-App-Projekt, klicken Sie auf **Hinzufügen** und dann auf **Verbundener Dienst...**. Wählen Sie anschließend den soeben erstellten mobilen Dienst aus, und klicken Sie auf **OK**.

    Dieselbe neue Codedatei wird dem Windows Phone Store-App-Projekt hinzugefügt. Überprüfen und korrigieren Sie auch den Pfad, der in dem Verweis angegeben ist, welcher der Datei "default.html" hinzugefügt wurde.

Jetzt sind sowohl die Windows Store- als auch die Windows Phone Store-App mit dem neuen mobilen Dienst verbunden. Im nächsten Schritt testen Sie das neue mobile Dienstprojekt.

## <a name="test-the-service-locally"></a>Lokales Testen des mobilen Dienstprojekts

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation][mobile-services-dotnet-backend-test-local-service-api-documentation]]

## <a name="update-app"></a>Aktualisieren der App für die Verwendung des mobilen Diensts

In diesem Abschnitt aktualisieren Sie die universelle Windows-App, um den mobilen Dienst als Backend-Dienst für die Anwendung zu verwenden. Dazu müssen Sie lediglich die Projektdatei "default.js" im Projektordner "GetStartedWithData.Shared" anpassen.

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app][mobile-services-windows-javascript-update-data-app]]

## <a name="publish-mobile-service"></a>Veröffentlichen des mobilen Diensts in Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## <a name="test-azure-hosted"></a>Testen des in Azure gehosteten mobilen Diensts

Nun können wir beide Versionen der universellen Windows-App mit dem in Azure gehosteten mobilen Dienst testen.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app][mobile-services-windows-universal-test-app]]

## <a name="view-stored-data"></a>Anzeigen der in der SQL-Datenbank gespeicherten Daten

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data][mobile-services-dotnet-backend-view-sql-data]]

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer universellen Windows-App für die Arbeit mit Daten in Mobile Services behandelt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten][Prüfen und Ändern von Daten mit Skripten]
    Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging][Optimieren von Abfragen mittels Paging]
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie eines der folgenden Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz][Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
    Lernen Sie mehr über die Verwendung von Mobile Services mit HTML und JavaScript.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [dieser Version]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
  [Herunterladen des Windows Store-App-Projekts]: #download-app
  [Erstellen eines mobilen Diensts in Visual Studio]: #create-service
  [Lokales Testen des mobilen Dienstprojekts]: #test-the-service-locally
  [Aktualisieren der App für die Verwendung des mobilen Diensts]: #update-app
  [Veröffentlichen des mobilen Diensts in Azure]: #publish-mobile-service
  [Testen der App mit dem in Azure gehosteten Dienst]: #test-azure-hosted
  [Anzeigen der in der SQL-Datenbank gespeicherten Daten]: #view-stored-data
  [Kostenlose Azure-Testversion]: http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-javascript-get-started-data%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-windows-universal-javascript-download-project]: ../includes/mobile-services-windows-universal-javascript-download-project.md
  [mobile-services-dotnet-backend-create-new-service-vs2013]: ../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md
  [mobile-services-dotnet-backend-test-local-service-api-documentation]: ../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md
  [mobile-services-windows-javascript-update-data-app]: ../includes/mobile-services-windows-javascript-update-data-app.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [mobile-services-dotnet-backend-view-sql-data]: ../includes/mobile-services-dotnet-backend-view-sql-data.md
  [Prüfen und Ändern von Daten mit Skripten]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [Optimieren von Abfragen mittels Paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-js
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library/
