<properties 
	pageTitle="Erste Schritte mit Azure Mobile Services für Android-Apps" 
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Mobile Services für die Android-Entwicklung." 
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
	ms.date="08/18/2015" 
	ms.author="ricksal"/>

# <a name="getting-started"></a>Erste Schritte mit Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started-ec.md)]

In diesem Lernprogramm erfahren Sie, wie Sie einen Cloud-basierten Backend-Service mithilfe von Azure Mobile Services zu einer Android-App hinzufügen. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache _To-Do-Listen_-App, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen erstellte mobile Dienst verwendet die unterstützten .NET-Sprachen unter Verwendung von Visual Studio für die serverseitige Geschäftslogik und zur Verwaltung des mobilen Diensts. Informationen zum Erstellen eines mobilen Dienstes, mit dem Sie serverseitige Geschäftslogik in JavaScript schreiben können, finden Sie in der [JavaScript-Back-End-Version](mobile-services-android-get-started-ec.md) dieses Themas.

Unten sehen Sie einen Screenshot aus der fertigen App:

![][0]

Sie benötigen die [Android Developer Tools][Android SDK], um dieses Lernprogramm abzuschließen. Diese umfassen die integrierte Entwicklungsumgebung (IDE) von Eclipse, das Android Developer Tools (ADT)-Plug-In und die neueste Android-Plattform. Android 4.2 oder eine höhere Version ist erforderlich.

Das heruntergeladene Schnellstartprojekt enthält den Mobile Services SDK für Android. Dieses Projekt erfordert Android 4.2 oder eine höhere Version. Der Mobile Services SDK benötigt jedoch nur Android 2.2 oder eine höhere Version.

> [AZURE.IMPORTANT]Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank).

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Dienstes

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Herunterladen des mobilen Dienstes auf den lokalen Computer

Da Sie nun den mobilen Dienst erstellt haben, können Sie Ihr personalisiertes Mobildienstprojekt herunterladen und auf Ihrem lokalen oder auch einem virtuellen Computer ausführen.

1. Klicken Sie auf den mobilen Dienst, den Sie gerade erstellt haben, und dann in der Schnellstartregisterkarte unter **Plattform auswählen** auf **Android**, und erweitern Sie die Option **Neue Android-App erstellen**.

	![][1]

2. Wenn Sie dies noch nicht durchgeführt haben, laden Sie [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) oder eine neuere Version herunter, und installieren Sie diese.

3. Klicken Sie auf **Download** unter **Download and publish your service to the cloud**.

	Dadurch wird das Visual Studio-Projekt, das den mobilen Dienst implementiert, heruntergeladen. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

4. Laden Sie außerdem das Veröffentlichungsprofil herunter, speichern Sie die heruntergeladene Datei auf dem lokalen Computer, und notieren Sie sich den Speicherort.

## Testen des mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Veröffentlichen des mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Erstellen einer neuen Android-App

In diesem Abschnitt erstellen Sie eine neue Android-App, die mit Ihrem Mobile Service verbunden ist.

1. Klicken Sie im [Verwaltungsportal] auf **Mobile Dienste** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie in der Schnellstartregisterkarte unter **Plattform auswählen** auf **Android**, und erweitern Sie die Option **Neue Android-App erstellen**.
 
	![][2]

3. Wenn Sie dies nicht bereits getan haben, laden Sie die [Android Developer Tools][Android SDK] auf Ihren lokalen oder virtuellen Computer herunter, und installieren Sie diese.

4. Klicken Sie unter **Ihre App herunterladen und ausführen** auf **Download**.

  	Das Projekt für die beispielhafte _To-Do-Listen_-Anwendung, die mit dem mobilen Dienst verbunden ist, wird heruntergeladen. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

## Ausführen Ihrer Android-App

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1. Navigieren Sie zum Speicherort der komprimierten Projektdateien, und entpacken Sie die Dateien auf Ihrem Computer.

2. Klicken Sie in Eclipse auf **Datei**, dann auf **Importieren**, und erweitern Sie dann **Android**. Klicken Sie auf **Existing Android Code into Workspace** und dann auf **Weiter.**

 	![][14]

3. Klicken Sie auf **Durchsuchen**, navigieren Sie zum Speicherort der entpackten Projektdateien, klicken Sie auf **OK**, stellen Sie sicher, dass das TodoActivity-Projekt ausgewählt ist, und klicken Sie dann auf **Fertig stellen**.

 	![][15]

	Daraufhin werden die Projektdateien in den aktuellen Arbeitsbereich importiert.

   	![][8]

4. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um das Projekt im Android-Emulator zu starten.

	> [AZURE.IMPORTANT]Sie müssen mindestens ein Android Virtual Device (AVD) definieren, um das Projekt im Android-Emulator auszuführen. Verwenden Sie den AVD Manager, um diese Geräte zu erstellen und zu verwalten.

5. Geben Sie in der App einen sinnvollen Text ein, beispielsweise _Lernprogramm beenden_, und klicken Sie dann auf **Hinzufügen**.

   	![][10]

   	Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

	> [AZURE.NOTE]Sie können den Code überprüfen, der auf Ihren Mobile Service zugreift, um Daten abzufragen und einzufügen. Sie finden ihn in der ToDoActivity.java-Datei.

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](mobile-services-dotnet-backend-how-to-configure-iis-express.md).-->

## <a name="next-steps"> </a>Nächste Schritte
Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

* [Erste Schritte mit der Authentifizierung] <br/>Informationen zur Authentifizierung von Benutzern Ihrer App bei einem Identitätsanbieter.

* [Erste Schritte mit Pushbenachrichtigungen] <br/>Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

* [Beheben von Problemen bei einem Mobile Services .NET-Back-End] <br/> Erfahren Sie, wie Sie Probleme diagnostizieren und beheben, die in Zusammenhang mit einem Mobile Services .NET-Back-End auftreten.

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started-EC/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started-EC/mobile-quickstart-steps-vs-EC.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started-EC/mobile-quickstart-steps-android-EC.png


[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started-EC/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started-EC/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started-EC/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started-EC/mobile-services-import-android-project.png

<!-- URLs. -->
[Get started with data]: mobile-services-dotnet-backend-android-get-started-data.md
[Erste Schritte mit der Authentifizierung]: mobile-services-dotnet-backend-android-get-started-users.md
[Erste Schritte mit Pushbenachrichtigungen]: mobile-services-dotnet-backend-android-get-started-push.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Beheben von Problemen bei einem Mobile Services .NET-Back-End]: mobile-services-dotnet-backend-how-to-troubleshoot.md

[Verwaltungsportal]: https://manage.windowsazure.com/
 

<!----HONumber=August15_HO8-->
