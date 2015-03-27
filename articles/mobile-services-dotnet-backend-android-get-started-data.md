<properties 
	pageTitle="Erste Schritte mit Daten (Android) | Mobile Dev Center" 
	description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Ihrer Android-App." 
	services="mobile-services" 
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
	ms.date="02/03/2015" 
	ms.author="ricksal"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

In diesem Thema erfahren Sie, wie Sie Azure Mobile Services als Back-End-Datenquelle für Android-Apps nutzen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst, laden ein Eclipse Android-Projekt für eine App herunter, die Daten im Arbeitsspeicher ablegt, integrieren den mobilen Dienst mit der App und zeigen die Datenänderungen beim Ausführen der App an.

Der mobile Dienst, den Sie in diesem Lernprogramm erstellen, unterstützt die .NET Runtime in Mobile Services. Auf diese Weise können Sie .NET-Sprachen und Visual Studio für die serverseitige Geschäftslogik im mobilen Dienst verwenden. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der [JavaScript-Back-End-Version] dieses Themas.

> [AZURE.IMPORTANT] Für dieses Lernprogramm ist Visual Studio 2013 erforderlich.

<!-- -->

> [AZURE.NOTE] Die Eclipse-Version dieses Lernprogramms finden Sie unter: [Erste Schritte mit Daten (Eclipse)].

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:


1. [Erstellen eines neuen mobilen Diensts]
2. [Lokales Herunterladen des Diensts]
3. [Testen des mobilen Diensts]
4. [Veröffentlichen des mobilen Diensts in Azure]
5. [Download des GetStartedWithData-Projekts]
4. [Aktualisieren der App für den Datenzugriff über mobile Dienste]
5. [Testen der App mit dem veröffentlichten mobilen Dienst]


> [AZURE.IMPORTANT] Sie benötigen ein Azure-Konto, um dieses Lernprogramm durchführen zu können. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F"%20target="_blank). 


<h2><a name="create-service"></a>Erstellen Sie einen neuen mobilen Diensts</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]


<h2><a name="download-the-service"></a>Herunterladen des Diensts auf den lokalen Computer</h2>

[AZURE.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

<h2><a name="test-the-service"></a>Testen des mobilen Diensts</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

<h2><a name="publish-the-service"></a>Veröffentlichen des mobilen Diensts in Azure</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

<h2><a name="download-app"></a>Download des GetStartedWithData-Projekts</h2>

### Laden Sie den Beispielcode herunter

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/download-android-sample-code.md)]

### Prüfen der Version des Android-SDK

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]


### Prüfen und Ausführen des Beispielcodes

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

<h2><a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste</h2>

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]

<h2><a name="test-app"></a>Testen der App mit dem veröffentlichten mobilen Dienst</h2>


Die App verwendet nun mobile Dienste als Back-End-Speicher, und Sie können sie entweder im Android-Emulator oder auf einem Android-Telefon gegen den mobilen Dienst testen.

1. Klicken Sie im Menü **Ausführen** auf **App ausführen**, um das Projekt zu starten.

	Dadurch wird Ihre mit dem Android-SDK erstellte App ausgeführt, die mithilfe der Clientbibliothek Elemente aus Ihrem mobilen Dienst abfragt.

5. Geben Sie wie zuvor einen sinnvollen Text ein, und klicken Sie auf **Hinzufügen**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

    Sie können die App neu starten, um zu sehen, dass die Änderungen in die Datenbank in Azure übernommen wurden. Außerdem können Sie die Datenbank im Azure-Verwaltungsportal überprüfen:  in den nächsten zwei Schritten werden auf diese Weise die Änderungen in Ihrer Datenbank angezeigt.


4. Klicken Sie im Azure-Verwaltungsportal auf die Option zum Verwalten der Datenbank, die mit Ihrem mobilen Dienst verknüpft ist.

    ![](./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png)

5. Führen Sie im Verwaltungsportal eine Abfrage aus, um die von der Windows Store-App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage sieht wie folgt aus, nur dass Sie Ihren Datenbanknamen anstelle von `todolist` verwenden.

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png)

Damit ist das Android-Lernprogramm **Erste Schritte mit Daten** abgeschlossen.



## <a name="next-steps"> </a>Nächste Schritte

Dieses Lernprogramm zeigt die Grundlagen für die Integration von Daten in mobilen Diensten in Android-Apps. 

<!--Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

* [Überprüfen und Ändern von Daten mit Skripts]
  <br/>Erfahren Sie mehr über die Verwendung von Serverskripts in Mobile Services, um die von Ihrer App gesendeten Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mit Paging]
  <br/>Erfahren Sie, wie Sie Paging in Abfragen zum Steuern der in einer einzelnen Anforderung verarbeiteten Datenmengen verwenden können.

Nachdem Sie die Datenreihe abgeschlossen haben, versuchen Sie
-->

Probieren Sie die folgenden Lernprogramme aus:

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Benutzer in Ihrer App authentifiziert werden.

* [Erste Schritte mit Pushbenachrichtigungen] 
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

* [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET.
  
<!-- Anchors. -->

[Erstellen eines neuen mobilen Diensts]: #create-service
[Lokales Herunterladen des Diensts]: #download-the-service-locally
[Lokaler Download des Diensts]: #download-the-service-locally
[Testen des mobilen Diensts]: #test-the-service
[Download des GetStartedWithData-Projekts]: #download-app
[Aktualisieren der App für den Datenzugriff über mobile Dienste]: #update-app
[Testen der Android Phone-App mit dem lokal gehosteten Dienst]: #test-locally-hosted
[Veröffentlichen des mobilen Diensts in Azure]: #publish-mobile-service
[Testen der Android Phone-App mit dem lokal gehosteten Dienst]: #test-azure-hosted
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
[Erste Schritte mit Daten (Eclipse)]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-data-EC/
[Überprüfen und Ändern von Daten mit Skripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Optimieren von Abfragen mit Paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Erste Schritte mit Mobile Services]: /documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Erste Schritte mit der Authentifizierung]: /develop/mobile/tutorials/get-started-with-users-android
[Erste Schritte mit Authentifizierung]: /develop/mobile/tutorials/get-started-with-users-android
[Erste Schritte mit Pushbenachrichtigungen]: /develop/mobile/tutorials/get-started-with-push-android
[JavaScript und HTML]: /develop/mobile/tutorials/get-started-with-data-js
[JavaScript-Back-End-Version]: /develop/mobile/tutorials/get-started-with-data-android

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Website mit Codebeispielen für Entwickler]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /develop/mobile/how-to-guides/work-with-net-client-library
[MobileServiceClient-Klasse]: http://go.microsoft.com/fwlink/p/?LinkId=302030
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library  
<!--HONumber=47-->
