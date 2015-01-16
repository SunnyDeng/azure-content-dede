<properties pageTitle="Erste Schritte mit Azure Mobile Services für Android-Apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Mobile Services für die Android-Entwicklung." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="ricksal,glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="hero-article" ms.date="10/16/2014" ms.author="ricksal,glenga" />

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In diesem Lernprogramm erfahren Sie, wie Sie einen cloudbasierten Back-End-Dienst mithilfe von Azure Mobile Services zu einer Android-App hinzufügen. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache <em>To-Do-Listen</em>-App, die App-Daten im neuen mobilen Dienst speichert.</p>
<p>Unten sehen Sie einen Screenshot aus der fertigen App:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a><span class="time">07:26:00</span></div>
</div>

![][0]

Sie benötigen die [Android Developer Tools][Android SDK], um dieses Lernprogramm abzuschließen. Diese umfassen die integrierte Entwicklungsumgebung (IDE) von Eclipse, das Android Developer Tools (ADT)-Plug-In und die neueste Android-Plattform. Android 4.2 oder eine höhere Version ist erforderlich. 

Das heruntergeladene Schnellstartprojekt enthält das Mobile Services SDK für Android. Dieses Projekt erfordert Android 4.2 oder eine höhere Version. Das Mobile Services SDK benötigt jedoch nur Android 2.2 oder eine höhere Version.

<div class="dev-callout"><strong>Hinweis:</strong> <p>Sie benötigen ein Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

>[AZURE.NOTE] Den Quellcode der fertig gestellten App finden Sie <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStarted/Android" target="_blank">hier</a>.

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Dienstes

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>Erstellen einer neuen Android-App</h2>

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern. 

In diesem Abschnitt erstellen Sie eine neue Android-App, die mit Ihrem Mobile Service verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf der Schnellstartregisterkarte unter **Plattform auswählen** auf **Android**. Erweitern Sie dann **Neue Android-App erstellen**.

   	![][6]

   	Dadurch werden drei einfache Schritte zum Erstellen einer Android-App angezeigt, die mit dem mobilen Dienst verbunden wird.

  	![][7]

3. Wenn Sie dies nicht bereits getan haben, laden Sie die [Android Developer Tools][Android SDK] auf Ihren lokalen oder virtuellen Computer herunter, und installieren Sie diese.

4. Klicken Sie auf **TodoItems-Tabelle erstellen**, um eine Tabelle zum Speichern der App-Daten zu erstellen.


5. Laden Sie jetzt Ihre App herunter:
	- Die neueste Version der App verwendet das Mobile Services Android SDK 2.0. Sie können diese Version <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">hier</a> herunterladen. Klicken Sie auf **ZIP herunterladen**, entzippen Sie die Datei; das Projekt befindet sich unter "GettingStarted" im Android-Ordner.
	 
	- Eine frühere Version verwendet die frühere Version des SDK. Um diese zu verwenden, klicken Sie unter **Ihre App herunterladen und ausführen** auf **Download**. Hierdurch wird das Projekt für die _To do list_-Beispielanwendung, die mit dem mobilen Dienst verbunden ist, heruntergeladen. Die Projektdateien sind komprimiert, navigieren Sie daher zu dem entsprechenden Speicherort, und erweitern Sie die Dateien auf Ihrem Computer.


## Ausführen Ihrer Android-App

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

2. Klicken Sie in Eclipse auf **Datei**, dann auf **Importieren**, und erweitern Sie **Android**. Klicken Sie auf **Vorhandenen Android-Code im Arbeitsbereich**, und dann auf **Weiter.** 

 	![][14]

3. Klicken Sie auf**Durchsuchen**, navigieren Sie zum Speicherort der entpackten Projektdateien, klicken Sie auf **OK**, stellen Sie sicher, dass das TodoActivity-Projekt ausgewählt ist, und klicken Sie dann auf  **Fertig stellen**. 

 	![][15]

	Daraufhin werden die Projektdateien in den aktuellen Arbeitsbereich importiert.

   	![][8]

4. Wenn Sie Version 2.0 des SDK heruntergeladen haben, müssen Sie den Code mit der URL und dem Schlüssel des mobilen Diensts aktualisieren:
	- 	Suchen Sie die **OnCreate**-Methode in **TodoActivity.java**, und suchen Sie den Code, der den Mobile Services-Client instanziiert. Der Code ist in der Abbildung oben dargestellt.
	- 	Ersetzen Sie "MobileServiceUrl" durch den tatsächlichen URL des mobilen Diensts.
	- 	Ersetzen Sie "AppKey" durch den Schlüssel des mobilen Diensts.
	- 	Weitere Informationen finden Sie Lernprogramm <a href="http://azure.microsoft.com/de-de/documentation/articles/mobile-services-android-get-started-data/">Hinzufügen von Mobile Services zu einer vorhandenen App</a>. 



4. Klicken Sie im Menü **Ausführen** auf **Ausführen** um das Projekt im Android-Emulator zu starten.

	<div class="dev-callout"><strong>Hinweis:</strong> <p>Sie müssen mindestens ein Android Virtual Device (AVD) definieren, um das Projekt im Android-Emulator auszuführen. Verwenden Sie den AVD Manager, um diese Geräte zu erstellen und zu verwalten.</p></div>

5. Geben Sie in der App einen aussagekräftigen Text ein, beispielsweise _Lernprogramm beenden_, und klicken Sie dann auf **Hinzufügen**.

   	![][10]

   	Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

	<div class="dev-callout"><strong>Hinweis</strong> 
   	<p>Sie können den Code überprüfen, der auf Ihren Mobile Service zugreift, um Daten abzufragen und einzufügen. Sie finden ihn in der ToDoActivity.java-Datei.</p>
 	</div>

6. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItems**.

   	![][11]

   	Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

   	![][12]

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
[Erstellen eines neuen Mobile Services]::#create-new-service
[Definieren der mobilen Dienstinstanz]::#define-mobile-service-instance
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png





[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-android-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-android-get-started-data/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Verwaltungsportal]: https://manage.windowsazure.com/
