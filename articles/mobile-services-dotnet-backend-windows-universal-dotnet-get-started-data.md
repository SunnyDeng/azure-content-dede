<properties 
	pageTitle="Erste Schritte mit Daten (Windows Store) | Mobile Dev Center" 
	description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Ihrer Windows Store-App." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/27/2014" 
	ms.author="wesmc"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

In diesem Thema wird gezeigt, wie Sie Azure Mobile Services als Back-End-Datenquelle für eine Windows Store-App nutzen können. In diesem Lernprogramm laden Sie ein Visual Studio 2013-Projekt für eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und zeigen dann die Datenänderungen beim Ausführen der App an.

Bei dem mobilen Dienst, den Sie in diesem Lernprogramm erstellen, handelt es sich um einen mobilen .NET-Back-End-Dienst. Dank des .NET-Back-Ends können Sie .NET-Sprachen und Visual Studio für die serverseitige Geschäftslogik im mobilen Dienst verwenden und Ihren mobilen Dienst auf Ihrem lokalen Computer ausführen und debuggen. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der JavaScript-Backend-Version dieses Themas.

>[AZURE.NOTE]In diesem Thema wird erläutert, wie Sie mit den in Visual Studio Professional 2013 mit Update 3 enthaltenen Tools einen mobilen Dienst mit einer universellen Windows-App verbinden. Mit denselben Schritten können Sie einen mobilen Dienst mit einer Windows Store- oder einer Windows Phone Store 8.1-App verbinden. Informationen zum Verbinden eines mobilen Diensts mit einer Windows Phone 8.0- oder Windows Phone Silverlight 8.1-App finden Sie unter [Erste Schritte mit Daten für Windows Phone](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data).

> Wenn Sie kein Upgrade auf Visual Studio Professional 2013 Update 3 durchführen können oder Ihr mobiles Dienstprojekt manuell einer Windows Store-App hinzufügen möchten, finden Sie in [dieser Version](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data) des Themas weitere Informationen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des Windows Store-App-Projekts]
2. [Erstellen eines mobilen Diensts in Visual Studio]
3. [Lokales Testen des mobilen Dienstprojekts]
4. [Aktualisieren der App zur Verwendung des mobilen Diensts]
5. [Veröffentlichen des mobilen Dienstes in Azure]
6. [Testen der App mit dem in Azure gehosteten Dienst]
7. [Anzeigen der in der SQL-Datenbank gespeicherten Daten]

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Update 3 oder eine höhere Version). <br/>Dazu ist eine kostenlose Testversion verfügbar. 

##<a name="download-app"></a>Download des GetStartedWithData-Projekts

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##<a name="create-service"></a>Erstellen eines mobilen Diensts in Visual Studio

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="7"><li><p>Öffnen Sie im Projektmappen-Explorer im Projektordner GetStartedWithData.Shared die Codedatei App.xaml.cs. Im Block für die bedingte Kompilierung einer Windows Store-App wurde der <strong>App</strong>-Klasse ein neues statisches Feld hinzugefügt, das dem im folgenden Beispiel entspricht:</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>Dieser Code ermöglicht den Zugriff auf den neuen mobilen Dienst in Ihrer App über eine Instanz der <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient class</a>. Der Client wird durch Angabe des URI und des Anwendungsschlüssels des neuen mobilen Diensts erstellt. Dieses statische Feld steht allen Seiten in Ihrer App zur Verfügung.</p>
</li>
<li><p>Klicken Sie mit der rechten Maustaste auf das Windows Phone-App-Projekt, klicken Sie auf <strong>Hinzufügen</strong> und dann auf <strong>Verbundener Dienst....</strong> Wählen Sie anschließend den soeben erstellten mobilen Dienst aus, und klicken Sie auf <strong>OK</strong>. </p>
<p>Derselbe Code wird der gemeinsam verwendeten Datei App.xaml.cs hinzugefügt, diesmal jedoch innerhalb eines Blocks für die bedingte Kompilierung einer Windows Phone-App.</p></li>
</ol>

Jetzt sind sowohl die Windows Store- als auch die Windows Phone Store-App mit dem neuen mobilen Dienst verbunden. Im nächsten Schritt testen Sie das neue mobile Dienstprojekt.


##<a name="test-the-service-locally"></a>Lokales Testen des mobilen Dienstprojekts

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##<a name="update-app"></a>Aktualisieren der App zur Verwendung des mobilen Diensts

In diesem Abschnitt aktualisieren Sie die universelle Windows-App, um den mobilen Dienst als Back-End-Dienst für die Anwendung zu verwenden. Dazu müssen Sie lediglich die Projektdatei MainPage.xaml.cs im Projektordner GetStartedWithData.Shared anpassen. 

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]


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
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET.
  
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



<!-- URLs. -->
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-dotnet
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/

[Erste Schritte mit der Offline-Datensynchronisierung]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Website mit Codebeispielen für Entwickler]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 

<!--HONumber=42-->
