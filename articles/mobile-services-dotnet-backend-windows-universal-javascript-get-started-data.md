<properties pageTitle="Erste Schritte mit Daten (Windows Store) | Mobile Dev Center" description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Ihrer Windows Store-App." services="mobile-services" documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

In diesem Thema wird gezeigt, wie Sie Azure Mobile Services als Back-End-Datenquelle für eine Windows Store-App nutzen können. In diesem Lernprogramm laden Sie ein Visual Studio 2013-Projekt für eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und zeigen dann die Datenänderungen beim Ausführen der App an.

Bei dem mobilen Dienst, den Sie in diesem Lernprogramm erstellen, handelt es sich um einen mobilen .NET-Back-End-Dienst. Dank des .NET-Back-Ends können Sie .NET-Sprachen und Visual Studio für die serverseitige Geschäftslogik im mobilen Dienst verwenden und Ihren mobilen Dienst auf Ihrem lokalen Computer ausführen und debuggen. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der JavaScript-Backend-Version dieses Themas.

>[AZURE.NOTE]In diesem Thema wird erläutert, wie Sie mit den in Visual Studio Professional 2013 mit Update 3 enthaltenen Tools einen mobilen Dienst mit einer universellen Windows-App verbinden. Wenn Sie kein Upgrade auf Visual Studio Professional 2013 Update 3 durchführen können oder Ihr mobiles Dienstprojekt manuell einer Windows Store-App hinzufügen möchten, finden Sie in [dieser Version](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data) des Themas weitere Informationen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des Windows Store-App-Projekts]
2. [Erstellen eines mobilen Diensts in Visual Studio]
3. [Lokales Testen des mobilen Dienstprojekts]
4. [Aktualisieren der App zur Verwendung des mobilen Diensts]
5. [Veröffentlichen des mobilen Dienstes in Azure]
6. [Testen der App mit dem in Azure gehosteten Dienst]
7. [Anzeigen der in der SQL-Datenbank gespeicherten Daten]

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-javascript-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Dazu ist eine kostenlose Testversion verfügbar.

##<a name="download-app"></a>Herunterladen des GetStartedWithData-Projekts

[AZURE.INCLUDE [mobile-services-windows-universal-javascript-download-project](../includes/mobile-services-windows-universal-javascript-download-project.md)]

##<a name="create-service"></a>Erstellen eines neuen mobilen Diensts in Visual Studio

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="8"><li><p>Navigieren Sie im Projektmappen-Explorer zum Unterordner <strong>services\mobileService\scripts</strong>, öffnen Sie die Skriptdatei "service.js", und beachten Sie die neue globale Variable, die wie folgt aussieht:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>In diesem Code wird der Zugriff auf den neuen mobilen Dienst in Ihrer App über globale Variable bereitgestellt. Der Client wird durch Angabe des URI und des Anwendungsschlüssels des neuen mobilen Diensts erstellt. Da Sie einen Verweis auf das Skript zur Datei default.html hinzugefügt haben, ist diese Variable in allen Skriptdateien verfügbar, die von dieser Seite aus referenziert werden.</p>
</li>
<li><p>Öffnen Sie die Projektdatei "default.html", suchen Sie den Verweis auf die neue Skriptdatei "service.js", und stellen Sie sicher, dass der im Verweis angegebene Pfad folgendermaßen lautet:</p>
<pre><code>&lt;script src="/services/mobileServices/scripts/todolist.js"&gt;</script></code></pre>
<p>Gegenwärtig gibt es einen Fehler in Visual Studio, der dazu führt, dass im Pfad ein falscher Ordnername erzeugt wird.</p></li>
<li><p>Klicken Sie mit der rechten Maustaste auf das Windows Phone-App-Projekt, klicken Sie auf <strong>Hinzufügen</strong> und dann auf <strong>Verbundener Dienst....</strong> Wählen Sie anschließend den soeben erstellten mobilen Dienst aus, und klicken Sie auf <strong>OK</strong>. </p>
<p>Dieselbe neue Codedatei wird dem Windows Phone Store-App-Projekt hinzugefügt. Überprüfen und korrigieren Sie auch den Pfad, der in dem Verweis angegeben ist, welcher der Datei "default.html" hinzugefügt wurde.</p></li>
</ol>

Jetzt sind sowohl die Windows Store- als auch die Windows Phone Store-App mit dem neuen mobilen Dienst verbunden. Im nächsten Schritt testen Sie das neue mobile Dienstprojekt.

##<a name="test-the-service-locally"></a>Lokales Testen des mobilen Dienstprojekts

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

##<a name="update-app"></a>Aktualisieren der App zur Verwendung des mobilen Diensts

In diesem Abschnitt aktualisieren Sie die universelle Windows-App, um den mobilen Dienst als Back-End-Dienst für die Anwendung zu verwenden. Dazu müssen Sie lediglich die Projektdatei "default.js" im Projektordner GetStartedWithData.Shared anpassen. 

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="publish-mobile-service"></a>Veröffentlichen des mobilen Dienstes in Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="test-azure-hosted"></a>Testen des in Azure gehosteten mobilen Diensts

Nun können wir beide Versionen der universellen Windows-App mit dem in Azure gehosteten mobilen Dienst testen.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

##<a name="view-stored-data"></a>Anzeigen der in der SQL-Datenbank gespeicherten Daten

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung eines universellen Windows-App-Projekts für die Arbeit mit Daten in Mobile Services behandelt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

* [Überprüfen und Ändern von Daten mit Skripts]
  <br/>Erfahren Sie mehr über die Verwendung von Serverskripts in Mobile Services, um die von Ihrer App gesendeten Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mit Paging]
  <br/>Erfahren Sie, wie Sie Paging in Abfragen zum Steuern der in einer einzelnen Anforderung verarbeiteten Datenmengen verwenden können.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie eines der folgenden Lernprogramme ausprobieren:

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Benutzer in Ihrer App authentifiziert werden.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

* [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit HTML und JavaScript.
  
<!-- Anchors. -->

[Herunterladen des Windows Store-App-Projekts]: #download-app
[Erstellen eines mobilen Diensts in Visual Studio]: #create-service
[Lokales Testen des mobilen Dienstprojekts]: #test-the-service-locally
[Aktualisieren der App zur Verwendung des mobilen Diensts]: #update-app
[Testen der Android Phone-App mit dem lokal gehosteten Dienst]: #test-locally-hosted
[Veröffentlichen des mobilen Dienstes in Azure]: #publish-mobile-service
[Testen der App mit dem in Azure gehosteten Dienst]: #test-azure-hosted
[Anzeigen der in der SQL-Datenbank gespeicherten Daten]: #view-stored-data
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-data-sample-download-javascript-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png

[16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png
[19]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png
[20]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png

<!-- URLs. -->
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-js
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/


[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Website mit Codebeispielen für Entwickler]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library/
[MobileServiceClient-Klasse]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  

<!--HONumber=42-->
