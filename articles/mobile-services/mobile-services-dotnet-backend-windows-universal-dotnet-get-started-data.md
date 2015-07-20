<properties 
	pageTitle="Hinzufügen von Mobile Services zu einer vorhandenen App (Windows Store) | Mobile Dev Center" 
	description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Ihrer Windows Store-App." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/02/2015" 
	ms.author="wesmc"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Übersicht

In diesem Thema wird gezeigt, wie Sie Azure Mobile Services als Backend-Datenquelle für eine Windows Store-App nutzen können. In diesem Lernprogramm laden Sie ein Visual Studio 2013-Projekt für eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und zeigen dann die Datenänderungen beim Ausführen der App an.

Der mobile Dienst, der in diesem Lernprogramm erstellt wird, ist ein mobiler .NET Back-End-Dienst. Dank des .NET-Back-Ends können Sie .NET-Sprachen und Visual Studio für die serverseitige Geschäftslogik im mobilen Dienst verwenden und Ihren mobilen Dienst auf Ihrem lokalen Computer ausführen und debuggen. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der JavaScript-Backend-Version dieses Themas.

>[AZURE.NOTE]In diesem Thema wird erläutert, wie Sie mit den in Visual Studio Professional 2013 mit Update 3 enthaltenen Tools einen mobilen Dienst mit einer universellen Windows-App verbinden. Mit denselben Schritten können Sie einen mobilen Dienst mit einer Windows Store- oder einer Windows Phone Store 8.1-App verbinden. Informationen zum Verbinden eines mobilen Diensts mit einer Windows Phone 8.0- oder Windows Phone Silverlight 8.1-App finden Sie unter [Erste Schritte mit Daten für Windows Phone](mobile-services-dotnet-backend-windows-phone-get-started-data.md).

> Wenn Sie kein Upgrade auf Visual Studio Professional 2013 Update 3 durchführen können oder Ihr mobiles Dienstprojekt manuell einer Windows Store-App hinzufügen möchten, finden Sie in [dieser Version](../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md) des Themas weitere Informationen.

##Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenloses Azure-Testkonto](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio 2013</a> (Update 3 oder eine höhere Version). 

##Download des GetStartedWithData-Projekts

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##Erstellen eines mobilen Diensts in Visual Studio

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="7"><li><p>Öffnen Sie im Projektmappen-Explorer im Projektordner "GetStartedWithData.Shared" die Codedatei "App.xaml.cs". Im Block für die bedingte Kompilierung einer Windows Store-App wurde der <strong>App</strong>-Klasse ein neues statisches Feld hinzugefügt, das dem im folgenden Beispiel entspricht:</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>In diesem Code wird der Zugriff auf den neuen mobilen Dienst in Ihrer App über eine Instanz der <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient-Klasse</a> bereitgestellt. Der Client wird durch Angabe des URI und des Anwendungsschlüssels des neuen mobilen Diensts erstellt. Dieses statische Feld steht allen Seiten in Ihrer App zur Verfügung.</p>
</li>
<li><p>Klicken Sie mit der rechten Maustaste auf das Windows Phone-App-Projekt, klicken Sie auf <strong>Hinzufügen</strong> und dann auf <strong>Verbundener Dienst...</strong>. Wählen Sie anschließend den soeben erstellten mobilen Dienst aus, und klicken Sie auf <strong>OK</strong>. </p>
<p>Derselbe Code wird der gemeinsam verwendeten Datei "App.xaml.cs" hinzugefügt, diesmal jedoch innerhalb eines Blocks für die bedingte Kompilierung einer Windows Phone-App.</p></li>
</ol>

Jetzt sind sowohl die Windows Store- als auch die Windows Phone Store-App mit dem neuen mobilen Dienst verbunden. Im nächsten Schritt testen Sie das neue mobile Dienstprojekt.


##Lokales Testen des mobilen Dienstprojekts

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##Aktualisieren der App zur Verwendung des mobilen Diensts

In diesem Abschnitt aktualisieren Sie die universelle Windows-App, um den mobilen Dienst als Backend-Dienst für die Anwendung zu verwenden. Dazu müssen Sie lediglich die Projektdatei "MainPage.xaml.cs" im Projektordner "GetStartedWithData.Shared" anpassen.

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../../includes/mobile-services-windows-dotnet-update-data-app.md)]


##Veröffentlichen des mobilen Diensts in Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


##Testen des in Azure gehosteten mobilen Diensts

Nun können wir beide Versionen der universellen Windows-App mit dem in Azure gehosteten mobilen Dienst testen.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]

##Anzeigen der in der SQL-Datenbank gespeicherten Daten

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../../includes/mobile-services-dotnet-backend-view-sql-data.md)]
 
Damit ist das Lernprogramm beendet.

##Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer universellen Windows-App für die Arbeit mit Daten in Mobile Services behandelt. Lesen Sie danach eines dieser anderen Themen:

* [Erste Schritte mit der Authentifizierung] <br/>Informationen zur Authentifizierung von Benutzern Ihrer App.

* [Erste Schritte mit Pushbenachrichtigungen] <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

* [Mobile Services #C-Anleitungen: Konzeptionelle Referenz](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.


<!-- Images. -->



<!-- URLs. -->
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Erste Schritte mit der Authentifizierung]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Erste Schritte mit Pushbenachrichtigungen]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md

[Get started with offline data sync]: mobile-services-windows-store-dotnet-get-started-offline-data.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkID=510826
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  

<!---HONumber=July15_HO2-->