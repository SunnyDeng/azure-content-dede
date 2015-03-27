
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
	ms.date="02/13/2015" 
	ms.author="ricksal,glenga"/>


# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

In diesem Lernprogramm erfahren Sie, wie Sie einen Cloud-basierten Backend-Service mithilfe von Azure Mobile Services zu einer Android-App hinzufügen. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache To-Do-Listen-App, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen erstellte mobile Dienst verwendet die unterstützten .NET-Sprachen unter Verwendung von Visual Studio für die serverseitige Geschäftslogik und zur Verwaltung des mobilen Diensts. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der [JavaScript-Back-End-Version](/documentation/articles/mobile-services-android-get-started/) dieses Themas.

Unten sehen Sie einen Screenshot aus der fertigen App:

![][0]

Sie benötigen die [Android Developer Tools][Android Studio], die die integrierte Android Studio-Entwicklungsumgebung einbeziehen, sowie die neueste Android-Plattform. Android 4.2 oder eine höhere Version ist erforderlich.  

Das heruntergeladene Schnellstartprojekt enthält das Mobile Services SDK für Android. 

> [AZURE.IMPORTANT] Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank).

<!-- -->

> [AZURE.NOTE] Die Eclipse-Version dieses Lernprogramms finden Sie unter: [Erste Schritte (Eclipse)].

## <a name="create-new-service"> </a>Erstellen Sie einen neuen mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Herunterladen des mobilen Dienstes auf den lokalen Computer

Da Sie den mobilen Dienst jetzt erstellt haben, laden Sie das Projekt für den personalisierten mobilen Dienst herunter, den Sie auf dem lokalen oder dem virtuellen Computer ausführen können.

1. Klicken Sie auf den mobilen Dienst, den Sie gerade erstellt haben. Klicken Sie dann Schnellstartregisterkarte unter **Plattform auswählen** auf **Android**, und erweitern Sie **Neue Android-App erstellen**.

	![][1]  

2. Downloaden und installieren Sie [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) oder eine neuere Version, falls Sie dies nicht bereits getan haben.

3. Klicken Sie in Schritt 2 auf **Download** unter **Herunterladen und Veröffentlichen des Diensts in der Cloud**.

	Dadurch wird das Visual Studio-Projekt, das den mobilen Dienst implementiert, heruntergeladen. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

<!--
4. Laden Sie außerdem das Veröffentlichungsprofil herunter, speichern Sie die heruntergeladene Datei auf dem lokalen Computer, und notieren Sie sich den Speicherort.
-->

## Testen des mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Veröffentlichen des mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Erstellen einer neuen Android-App

In diesem Abschnitt erstellen Sie eine neue Android-App, die mit Ihrem Mobile Service verbunden ist.

1. Klicken Sie im [Verwaltungsportal] auf **Mobile Services** und dann auf den mobilen Dienst, den Sie soeben erstellt haben.

2. Klicken Sie in der Schnellstartregisterkarte unter **Plattform auswählen** auf **Android**, und erweitern Sie die Option **Neue Android-App erstellen**. 
 
	![][2]  

3. Wenn Sie dies nicht bereits getan haben, laden Sie [die Android Developer Tools][das Android SDK] auf Ihren lokalen oder virtuellen Computer herunter, und installieren Sie diese.

4. Klicken Sie unter **App herunterladen und ausführen** auf **Herunterladen**. 

  	Hierdurch wird das Projekt für die _To do list_-Beispielanwendung, die mit dem mobilen Dienst verbunden ist, heruntergeladen. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

## Ausführen Ihrer Android-App

[WACOM.INCLUDE [mobile-services-run-your-app](../includes/mobile-services-android-get-started.md)]

## <a name="next-steps"> </a>Nächste Schritte
Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden: 

* [Erste Schritte mit Daten]
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit Authentifizierung]
  <br/>Informationen über die Authentifizierung von Benutzern der App mit einem Identitätsanbieter.

* [Erste Schritte mit Pushbenachrichtigungen] 
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

* [Problembehandlung beim Mobile Services .NET-Back-End]
  <br/> Erfahren Sie mehr zur Diagnose und zum Beheben von Problemen, die mit einem Mobile Services .NET-Back-End auftreten können. 

<!-- Anchors. -->
[Erste Schritte mit Mobile Services]:#getting-started
[Erstellen eines neuen mobilen Diensts]:#create-new-service
[Definieren der mobilen Dienstinstanz]:#define-mobile-service-instance
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs-AS.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android-AS.png


[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Erste Schritte (Eclipse)]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-EC/
[Erste Schritte mit Daten]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Erste Schritte mit Authentifizierung]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[Erste Schritte mit Pushbenachrichtigungen]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-push
[das Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Problembehandlung beim Mobile Services .NET-Back-End]: /documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/

[Verwaltungsportal]: https://manage.windowsazure.com/

<!--HONumber=47-->
