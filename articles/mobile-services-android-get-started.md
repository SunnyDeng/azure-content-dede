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
	ms.topic="hero-article" 
	ms.date="04/09/2015" 
	ms.author="ricksal"/>

# Erste Schritte mit Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">


<p>In diesem Lernprogramm erfahren Sie, wie Sie einen Cloud-basierten Back-End-Service mithilfe von Azure Mobile Services zu einer Android-App hinzufügen. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache <em>To-Do-Listen</em>-App, die App-Daten im neuen mobilen Dienst speichert.</p>
<p>Unten sehen Sie einen Screenshot aus der fertigen App:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a><span class="time">07:26</span></div>
</div>

![](./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png)

## Voraussetzungen

Sie benötigen die [Android Developer Tools][Android Studio], die die integrierte Android Studio-Entwicklungsumgebung einbeziehen, sowie die neueste Android-Plattform. Android 4.2 oder eine höhere Version ist erforderlich. 

Das heruntergeladene Schnellstartprojekt enthält das Azure Mobile Services SDK für Android.

> [AZURE.IMPORTANT] Sie benötigen ein Azure-Konto, um dieses Lernprogramm durchführen zu können. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [1 Monat kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28).


## Erstellen eines neuen mobilen Diensts

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Erstellen einer neuen Android-App

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern. 

In diesem Abschnitt erstellen Sie eine neue Android-App, die mit Ihrem Mobile Service verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie in der Schnellstartregisterkarte unter **Plattform auswählen** auf **Android**, und erweitern Sie die Option **Neue Android-App erstellen**.

   	![](./media/mobile-services-android-get-started/mobile-portal-quickstart-android1.png)

   	Dadurch werden drei einfache Schritte zum Erstellen einer Android-App angezeigt, die mit dem mobilen Dienst verbunden wird.

  	![](./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png)

3. Wenn Sie dies nicht bereits getan haben, laden Sie [die Android Developer Tools][das Android SDK] auf Ihren lokalen oder virtuellen Computer herunter, und installieren Sie diese.

4. Klicken Sie auf **TodoItem-Tabelle erstellen**, um eine Tabelle zum Speichern von App-Daten zu erstellen.


5. Laden Sie jetzt Ihre App herunter:
	- Die neueste Version der App verwendet das Mobile Services Android SDK 2.0. Sie können diese Version <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">hier</a> herunterladen. Klicken Sie auf **ZIP herunterladen**, und entpacken Sie die Datei. Das Projekt befindet sich unter "GettingStarted" im Android-Ordner.
	 
	- Eine frühere Version verwendet die frühere Version des SDK. Um diese zu verwenden, klicken Sie unter **App herunterladen und ausführen** auf **Herunterladen**. Hierdurch wird das Projekt für die _To do list_-Beispielanwendung, die mit dem mobilen Dienst verbunden ist, heruntergeladen. Die Projektdateien sind komprimiert, navigieren Sie daher zu dem entsprechenden Speicherort, und erweitern Sie die Dateien auf Ihrem Computer.


## Ausführen Ihrer Android-App

[AZURE.INCLUDE [mobile-services-run-your-app](../includes/mobile-services-android-get-started.md)]

### Anzeigen des Codes (optional)

Den Quellcode der fertig gestellten App finden Sie [hier](https://github.com/RickSaling/mobile-services-samples/tree/androidStudio/GettingStarted/AndroidStudio).


Die Eclipse-Version dieses Lernprogramms finden Sie unter: [Erste Schritte (Eclipse)](mobile-services-android-get-started-EC.md).

## <a name="next-steps"> </a>Nächste Schritte
Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden: 

* [Erste Schritte mit Daten]
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung]
  <br/>Informationen über die Authentifizierung von Benutzern der App mit einem Identitätsanbieter.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.




<!-- Anchors. -->
[Erste Schritte mit Mobile Services]:#getting-started
[Erstellen eines neuen mobilen Diensts]:#create-new-service
[Definieren der mobilen Dienstinstanz]:#define-mobile-service-instance
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Erste Schritte (Eclipse)]: mobile-services-android-get-started-EC.md
[Erste Schritte mit Daten]: mobile-services-android-get-started-data.md
[Erste Schritte mit der Authentifizierung]: mobile-services-android-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-android-get-started-push.md
[das Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Verwaltungsportal]: https://manage.windowsazure.com/


<!--HONumber=52-->