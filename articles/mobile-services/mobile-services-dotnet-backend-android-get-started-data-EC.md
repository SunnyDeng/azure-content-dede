<properties 
	pageTitle="Erste Schritte mit Daten (Android) | Microsoft Azure" 
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
	ms.date="08/08/2015" 
	ms.author="ricksal"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data-ec.md)]

##Übersicht

In diesem Thema erfahren Sie, wie Sie Azure Mobile Services als Back-End-Datenquelle für Android-Apps nutzen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst, laden ein Eclipse Android-Projekt für eine App herunter, die Daten im Arbeitsspeicher ablegt, integrieren den mobilen Dienst mit der App und zeigen die Datenänderungen beim Ausführen der App an.

Der mobile Dienst, den Sie in diesem Lernprogramm erstellen, unterstützt die .NET Runtime in Mobile Services. Auf diese Weise können Sie .NET-Sprachen und Visual Studio für die serverseitige Geschäftslogik im mobilen Dienst verwenden. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der [JavaScript-Backend-Version](mobile-services-android-get-started-data-ec.md) dieses Themas.

Für dieses Lernprogramm benötigen Sie Folgendes:

+ <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio 2013</a> (Update 3 oder eine höhere Version). 

+ Ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenloses Azure-Testkonto](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-android-get-started-data-EC%2F).

##<a name="create-service"></a>Erstellen eines neuen mobilen Dienstes

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]


##<a name="download-the-service"></a>Herunterladen des Diensts auf den lokalen Computer

[AZURE.INCLUDE [mobile-services-download-service-locally](../../includes/mobile-services-download-service-locally.md)]

##<a name="test-the-service"></a>Testen des mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a name="publish-the-service"></a>Veröffentlichen des mobilen Diensts in Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="download-app"></a>Herunterladen des GetStartedWithData-Projekts

###Laden Sie den Beispielcode herunter

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/download-android-sample-code-ec.md)]

###Prüfen der Version des Android-SDK

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version-ec.md)]


###Prüfen und Ausführen des Beispielcodes

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code-ec.md)]

##<a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data-ec.md)]

##<a name="test-app"></a>Testen der App mit dem veröffentlichten mobilen Dienst

Die App verwendet nun mobile Dienste als Back-End-Speicher, und Sie können sie entweder im Android-Emulator oder auf einem Android-Telefon gegen den mobilen Dienst testen.

1. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um das Projekt zu starten.

	Dadurch wird Ihre mit dem Android-SDK erstellte App ausgeführt, die mithilfe der Clientbibliothek Elemente aus Ihrem mobilen Dienst abfragt.

5. Geben Sie wie zuvor einen lesbaren Text ein und klicken Sie auf **Hinzufügen**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

    Sie können die App neu starten, um zu sehen, dass die Änderungen in die Datenbank in Azure übernommen wurden. Außerdem können Sie die Datenbank im Azure-Verwaltungsportal überprüfen: in den nächsten zwei Schritten werden auf diese Weise die Änderungen in Ihrer Datenbank angezeigt.


4. Klicken Sie im Azure-Verwaltungsportal auf die Option zum Verwalten der Datenbank, die mit Ihrem mobilen Dienst verknüpft ist.

    ![](./media/mobile-services-dotnet-backend-android-get-started-data-EC/manage-sql-azure-database.png)

5. Führen Sie im Verwaltungsportal eine Abfrage aus, um die von der Windows Store-App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage sieht wie folgt aus, nur dass Sie Ihren Datenbanknamen anstelle von `todolist` verwenden.

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-android-get-started-data-EC/sql-azure-query.png)

Damit ist das Android-Lernprogramm **Erste Schritte mit Daten** abgeschlossen.


## <a name="next-steps"> </a>Nächste Schritte

Dieses Lernprogramm zeigt die Grundlagen für die Integration von Daten in mobilen Diensten in Android-Apps.

Probieren Sie die folgenden Lernprogramme aus:

* [Erste Schritte mit der Authentifizierung] <br/>Informationen zur Authentifizierung von Benutzern Ihrer App.

* [Erste Schritte mit Pushbenachrichtigungen] <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

* [Mobile Services Android-Anleitungen: Konzeptionelle Referenz](mobile-services-android-how-to-use-client-library.md) <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit Android.
  
<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Erste Schritte mit der Authentifizierung]: mobile-services-dotnet-backend-android-get-started-users.md
[Erste Schritte mit Pushbenachrichtigungen]: mobile-services-dotnet-backend-android-get-started-push-ec.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030

<!----HONumber=August15_HO8-->