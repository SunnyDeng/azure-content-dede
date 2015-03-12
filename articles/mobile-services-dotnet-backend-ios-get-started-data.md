<properties 
	pageTitle="Erste Schritte mit Daten (iOS) | Mobile Dev Center" 
	description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Ihrer iOS-App." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer iOS-App nutzen können. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und zeigen dann die Datenänderungen beim Ausführen der App an.

Der mobile Dienst, den Sie in diesem Lernprogramm erstellen, unterstützt die .NET Runtime in Mobile Services. Auf diese Weise können Sie .NET-Sprachen und Visual Studio für die serverseitige Geschäftslogik im mobilen Dienst verwenden. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der [JavaScript-Back-End-Version] dieses Themas.

>[AZURE.NOTE]Anhand dieses Lernprogramms soll Ihnen ein besseres Verständnis über die Mobile Services dahingehend vermittelt werden, wie Sie mit deren Hilfe Azure verwenden können, um Daten von einer iOS-App abzurufen und zu speichern. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie eventuell zuerst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.
</div>

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des iOS-App-Projekts]
2. [Erstellen des mobilen Diensts]
3. [Lokales Herunterladen des Diensts]
4. [Testen des mobilen Dienstes]
5. [Veröffentlichen des mobilen Diensts in Azure]
7. [Aktualisieren der App zur Verwendung von Mobile Services]
8. [Testen der App mit Mobile Services]

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Mobile Services iOS SDK] und [XCode 4.5][Installieren von Xcode] sowie iOS 5.0 oder neuere Versionen.
+ Visual Studio 2013 (Sie erhalten [Visual Studio Express für das Web](http://go.microsoft.com/p/?linkid=9832232) kostenlos).
+ Ein Microsoft Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Kostenlose Azure-Testversion</a>.

##<a name="download-app"></a>Download des GetStartedWithData-Projekts

Dieses Lernprogramm baut auf der [GetStartedWithData-App][GitHub] auf, bei der es sich um eine iOS-App handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-iOS-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1. Laden Sie die GetStartedWithData-[Beispiel-App][GitHub] herunter.

2. Öffnen Sie in Xcode das heruntergeladene Projekt, und prüfen Sie die Datei "TodoService.m".

   	Beachten Sie, dass acht **// TODO**-Kommentare vorhanden sind. Diese geben die von Ihnen durchzuführenden Schritte an, damit diese App mit dem mobilen Dienst funktionieren kann.

3. Klicken Sie auf die Schaltfläche **Ausführen** (oder drücken Sie die Taste Command+R), um das Projekt neu zu erstellen und die App zu starten.

4. Geben Sie in der App einen Text in das Textfeld ein, und klicken Sie anschließend auf die Schaltfläche **+**.

   	![][0]  

   	Beachten Sie, dass der gespeicherte Text in der unteren Liste angezeigt wird.

##<a name="create-service"></a>Erstellen eines neuen mobilen Diensts im Verwaltungsportal

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service-locally"></a>Herunterladen des Diensts auf den lokalen Computer

[AZURE.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

##<a name="test-the-service"></a>Testen des mobilen Dienstes

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a name="publish-mobile-service"></a>Veröffentlichen des mobilen Dienstes in Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst

1. Wählen Sie unter Xcode einen Emulator, den Sie bereitstellen (entweder iPhone oder iPad). Klicken Sie auf die Schaltfläche **Ausführen** (oder drücken Sie die Taste Command+R), um das Projekt neu zu erstellen und die App zu starten.

   	Hierdurch wird der mit dem iOS SDK erstellte Azure Mobile Services-Client ausgeführt, der Elemente vom mobilen Dienst abfragt.

2. Geben Sie wie zuvor Text in das Textfeld ein, und klicken Sie danach auf die Schaltfläche **+**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet. Jedes neue todoItem wird in der SQL-Datenbank, die Sie zuvor für Ihren mobilen Dienst im Azure-Verwaltungsportal konfiguriert haben, gespeichert und aktualisiert.

3. Starten Sie die App neu um zu sehen, dass die Änderungen in die Datenbank in Azure übernommen wurden.

	Außerdem können Sie die Datenbank im Azure-Verwaltungsportal oder im Visual Studio SQL Server-Objekt-Explorer überprüfen. In den nächsten beiden Schritten wird das [Azure-Verwaltungsportal] verwendet, um die Änderungen in der Datenbank anzuzeigen.

4. Klicken Sie im Azure-Verwaltungsportal auf die Option zum Verwalten der Datenbank, die mit Ihrem mobilen Dienst verknüpft ist.

    ![][17]

5. Führen Sie im Verwaltungsportal eine Abfrage aus, um die von der App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage sieht wie folgt aus, nur dass Sie Ihren Datenbanknamen anstelle von  `todolist` verwenden.

        SELECT * FROM [todolist].[todoitems]	

    ![][18]

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

##<a name="next-steps"></a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer iOS-App für die Arbeit mit Daten in Mobile Services aufgezeigt.

Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

* [Überprüfen und Ändern von Daten mit Skripts]
  <br/>Erfahren Sie mehr über die Verwendung von Serverskripts in Mobile Services, um die von Ihrer App gesendeten Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mit Paging]
  <br/>Erfahren Sie, wie Sie Paging in Abfragen zum Steuern der in einer einzelnen Anforderung verarbeiteten Datenmengen verwenden können.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie die folgenden weiteren iOS-Lernprogramme ausprobieren:

* [Erste Schritte mit der Authentifizierung]
	<br/>Erfahren Sie, wie Benutzer in Ihrer App authentifiziert werden.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Erfahren Sie, wie Sie eine ganz einfache Pushbenachrichtigung mit Mobile Services an Ihre App senden können.

<!-- Anchors. -->
[Herunterladen des iOS-App-Projekts]: #download-app
[Erstellen des mobilen Diensts]: #create-service
[Hinzufügen einer Datentabelle als Speicher]: #add-table
[Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
[Testen der App mit Mobile Services]: #test-app
[Nächste Schritte]:#next-steps
[Lokales Herunterladen des Diensts]: #download-the-service-locally
[Testen des mobilen Dienstes]: #test-the-service
[Veröffentlichen des mobilen Diensts in Azure]: #publish-mobile-service


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-todoitem-data-browse.png
[17]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png


<!-- URLs. -->
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-ios
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-ios
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-ios
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-ios
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-ios
[JavaScript-Back-End-Version]: /de-de/develop/mobile/tutorials/get-started-with-data-ios

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Installieren von Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub-Repository]: http://go.microsoft.com/fwlink/p/?LinkId=268784


<!--HONumber=42-->
