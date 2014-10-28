<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

In diesem Lernprogramm erfahren Sie, wie Sie einen Cloud-basierten Backend-Service mithilfe von Azure Mobile Services zu einer Android-App hinzufügen. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache *To-Do-Listen*-App, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen erstellte mobile Dienst verwendet die unterstützten .NET-Sprachen unter Verwendung von Visual Studio für die serverseitige Geschäftslogik und zur Verwaltung des mobilen Diensts. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der [JavaScript-Backend-Version][JavaScript-Backend-Version] dieses Themas.

Unten sehen Sie einen Screenshot aus der fertigen App:

![][]

Sie benötigen die [Android Developer Tools][Android Developer Tools], um dieses Lernprogramm abzuschließen. Diese umfassen die integrierte Entwicklungsumgebung (IDE) von Eclipse, das Android Developer Tools (ADT)-Plug-In und die neueste Android-Plattform. Android 4.2 oder eine höhere Version ist erforderlich.

Das heruntergeladene Schnellstartprojekt enthält den Mobile Services SDK für Android. Dieses Projekt erfordert Android 4.2 oder eine höhere Version. Der Mobile Services SDK benötigt jedoch nur Android 2.2 oder eine höhere Version.

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie ben&ouml;tigen ein Azure-Konto, um dieses Lernprogramm auszuf&uuml;hren. Falls Sie kein Konto besitzen, k&ouml;nnen Sie sich f&uuml;r eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen k&ouml;nnen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## Herunterladen des mobilen Dienstes auf den lokalen Computer

Da Sie den mobilen Dienst jetzt erstellt haben, laden Sie das Projekt für den personalisierten mobilen Dienst herunter, den Sie auf dem lokalen oder dem virtuellen Computer ausführen können.

1.  Klicken Sie auf den mobilen Dienst, den Sie gerade erstellt haben, und dann in der Schnellstartregisterkarte unter **Plattform auswählen** auf **Android**, und erweitern Sie die Option **Neue Android-App erstellen**.

    ![][1]

2.  Wenn Sie dies noch nicht durchgeführt haben, laden Sie [Visual Studio Professional 2013][Visual Studio Professional 2013] oder eine neuere Version herunter, und installieren Sie diese.

3.  Klicken Sie auf **Download** unter **Download and publish your service to the cloud**.

    Dadurch wird das Visual Studio-Projekt, das den mobilen Dienst implementiert, heruntergeladen. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

4.  Laden Sie außerdem das Veröffentlichungsprofil herunter, speichern Sie die heruntergeladene Datei auf dem lokalen Computer, und notieren Sie sich den Speicherort.

## Testen des mobilen Dienstes

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## Veröffentlichen des mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## Erstellen einer neuen Android-App

In diesem Abschnitt erstellen Sie eine neue Android-App, die mit Ihrem Mobile Service verbunden ist.

1.  Klicken Sie im [Verwaltungsportal][Verwaltungsportal] auf **Mobile Dienste** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie in der Schnellstartregisterkarte unter **Plattform auswählen** auf **Android**, und erweitern Sie die Option **Neue Android-App erstellen**.

    ![][2]

3.  Wenn Sie dies nicht bereits getan haben, laden Sie die [Android Developer Tools][Android Developer Tools] auf Ihren lokalen oder virtuellen Computer herunter, und installieren Sie diese.

4.  Klicken Sie unter **Ihre App herunterladen und ausführen** auf **Download**.

    Das Projekt für die beispielhafte *To-Do-Listen*-Anwendung, die mit dem mobilen Dienst verbunden ist, wird heruntergeladen. Speichern Sie die komprimierte Projektdatei auf dem lokalen Computer und merken Sie sich, wo Sie sie gespeichert haben.

## Ausführen Ihrer Android-App

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1.  Navigieren Sie zum Speicherort der komprimierten Projektdateien, und entpacken Sie die Dateien auf Ihrem Computer.

2.  Klicken Sie in Eclipse auf **Datei**, dann auf **Importieren**, und erweitern Sie dann **Android**. Klicken Sie auf **Existing Android Code into Workspace** und dann auf **Weiter.**

    ![][3]

3.  Klicken Sie auf **Durchsuchen**, navigieren Sie zum Speicherort der entpackten Projektdateien, klicken Sie auf **OK**, stellen Sie sicher, dass das TodoActivity-Projekt ausgewählt ist, und klicken Sie dann auf **Fertig stellen**.

    ![][4]

    Daraufhin werden die Projektdateien in den aktuellen Arbeitsbereich importiert.

    ![][5]

4.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um das Projekt im Android-Emulator zu starten.

    <div class="dev-callout"><strong>Hinweis</strong> <p>Sie m&uuml;ssen mindestens ein Android Virtual Device (AVD) definieren, um das Projekt im Android-Emulator auszuf&uuml;hren. Verwenden Sie den AVD Manager, um diese Ger&auml;te zu erstellen und zu verwalten.</p></div>

5.  Geben Sie in der App einen sinnvollen Text ein, beispielsweise *Lernprogramm beenden*, und klicken Sie dann auf **Hinzufügen**.

    ![][6]

    Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

    <div class="dev-callout"><strong>Hinweis</strong> 
<p>Sie k&ouml;nnen den Code &uuml;berpr&uuml;fen, der auf Ihren Mobile Service zugreift, um Daten abzufragen und einzuf&uuml;gen. Sie finden ihn in der ToDoActivity.java-Datei.</p>
</div>

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](/de-de/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).-->

## <a name="next-steps"> </a>Nächste Schritte

Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App bei einem Identitätsanbieter.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Beheben von Problemen bei einem Mobile Services .NET-Back-End][Beheben von Problemen bei einem Mobile Services .NET-Back-End]
     Erfahren Sie, wie Sie Probleme diagnostizieren und beheben, die in Zusammenhang mit einem Mobile Services .NET-Back-End auftreten.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [JavaScript-Backend-Version]: /de-de/documentation/articles/mobile-services-android-get-started/
  []: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
  [Android Developer Tools]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs.png
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [Verwaltungsportal]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
  [3]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
  [4]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png
  [5]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png
  [6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
  [Beheben von Problemen bei einem Mobile Services .NET-Back-End]: /de-de/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
