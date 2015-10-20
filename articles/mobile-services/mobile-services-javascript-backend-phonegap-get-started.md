<properties
	pageTitle="Erste Schritte mit Azure Mobile Services für PhoneGap/Cordova-Apps | Microsoft Azure"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Mobile Services für die PhoneGap-Entwicklung für iOS, Android und Windows Phone."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-phonegap"
	ms.devlang="multiple"
	ms.topic="get-started-article" 
	ms.date="10/05/2015"
	ms.author="ggailey777"/>

# Erste Schritte mit Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

In diesem Lernprogramm erfahren Sie, wie Sie einen cloudbasierten Backend-Dienst mithilfe von Azure Mobile Services zu einer App hinzufügen. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache _To-Do-Listen_-App, die App-Daten im neuen mobilen Dienst speichert.

Unten sehen Sie einen Screenshot aus der fertigen App:

![][3]

### Zusätzliche Anforderungen

Zum Abschluss dieses Lernprogramms ist Folgendes erforderlich:

+ PhoneGap-Tools (V3.2 oder höher für Windows Phone 8-Projekte erforderlich).

+ Ein aktives Microsoft Azure-Konto.

+ PhoneGap unterstützt die Entwicklung für verschiedene Plattformen. Zusätzlich zu den eigentlichen PhoneGap-Tools müssen Sie die Tools für jede geplante Plattform installieren:

	- Windows Phone: Installieren Sie [Visual Studio 2012 Express für Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374)
	- iOS: Installieren Sie [Xcode] (V4.4 oder höher erforderlich)
	- Android: Installieren Sie die [Android Developer Tools][Android SDK] <br/> (das Mobile Services SDK für Android unterstützt Apps für Android 2.2 oder höhere Versionen. Android 4.2 oder höher ist zum Ausführen der Schnellstart-App erforderlich.)

## Erstellen eines neuen mobilen Diensts

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Erstellen einer neuen PhoneGap-App

In diesem Abschnitt erstellen Sie eine neue PhoneGap-App, die mit Ihrem Mobile Service verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf der Schnellstartregisterkarte auf **PhoneGap** unter **Plattform auswählen**, und erweitern Sie die Option **Neue PhoneGap-App erstellen**.

   	![][0]

   	Dadurch werden drei einfache Schritte zum Erstellen einer PhoneGap-App angezeigt, die mit dem mobilen Dienst verbunden wird.

  	![][1]

3. Falls Sie es nicht bereits getan haben, laden Sie PhoneGap sowie mindestens eines der Plattform-Entwicklungstools (Windows Phone, iOS oder Android) herunter und installieren Sie sie.

4. Klicken Sie auf **TodoItem-Tabelle erstellen**, um eine Tabelle zum Speichern von App-Daten zu erstellen.

5. Klicken Sie unter **Ihre App herunterladen und ausführen** auf **Download**.

	Dadurch wird das Projekt für die Beispielanwendung der _To-do-Liste_, die mit dem mobilen Dienst verbunden ist, zusammen mit dem Mobile Services JavaScript SDK heruntergeladen. Speichern Sie die komprimierte Projektdatei auf Ihrem lokalen Computer, und notieren Sie sich den Speicherort.

## Ausführen der neuen PhoneGap-App

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1.	Navigieren Sie zum Speicherort der komprimierten Projektdateien, und entpacken Sie die Dateien auf Ihrem Computer.

2.	Öffnen Sie das Projekt und führen Sie es entsprechend den unten angegebenen Anweisungen für jede Plattform aus.

	+ **Windows Phone 8**

		1. Windows Phone 8: Öffnen Sie die SLN-Datei im Ordner **platforms\\wp8** in Visual Studio 2012 Express für Windows Phone.

		2. Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

	  	![][2]

	+ **iOS**

		1. Öffnen Sie das Projekt im Ordner **platforms/ios** in Xcode.

		2. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App im iPhone-Emulator zu starten, was die Voreinstellung bei diesem Projekt darstellt.

	  	![][3]

	+ **Android**

		1. Klicken Sie in Eclipse auf **Datei**, dann auf **Importieren**, und erweitern Sie dann **Android**. Klicken Sie auf **Existing Android Code into Workspace** und dann auf **Weiter.**

		2. Klicken Sie auf **Durchsuchen**, navigieren Sie zum Speicherort der entpackten Projektdateien, klicken Sie auf **OK**, stellen Sie sicher, dass das TodoActivity-Projekt ausgewählt ist, und klicken Sie dann auf **Fertig stellen**. <p>Daraufhin werden die Projektdateien in den aktuellen Arbeitsbereich importiert.</p>

		3. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um das Projekt im Android-Emulator zu starten.

			![][4]

		>[AZURE.NOTE]Sie müssen mindestens ein Android Virtual Device (AVD) definieren, um das Projekt im Android-Emulator auszuführen. Verwenden Sie den AVD Manager, um diese Geräte zu erstellen und zu verwalten.


3. Nachdem Sie die App in einem der oben angegebenen mobilen Emulatoren gestartet haben, geben Sie Text in das Textfeld ein und klicken auf **Hinzufügen**.

	Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die **TodoItem**-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

	> [AZURE.IMPORTANT]Änderungen an diesem Plattformprojekt werden überschrieben, falls das Hauptprojekt mit den PhoneGap-Tools neu erstellt wird. Nehmen Sie die Änderungen stattdessen im www-Stammverzeichnis des Projekts vor, wie im Abschnitt unten dargestellt.

4. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

	![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png)

	Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

	![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png)


## Erstellen von App-Aktualisierungen und Neuerstellung von Projekten für jede Plattform

1. Nehmen Sie Änderungen an den Codedateien im Verzeichnis "www" vor, in diesem Fall "todolist/www".

2. Stellen Sie sicher, dass alle Tools der Zielplattform im Systempfad verfügbar sind.

2. Öffnen Sie im Stammverzeichnis des Projekts eine Eingabeaufforderung, und führen Sie einen der folgenden plattformspezifischen Befehle aus:

	+ **Windows Phone**

		Geben Sie an der Eingabeaufforderung von Visual Studio Developer Folgendes ein:

    		phonegap local build wp8

	+ **iOS**

		Öffnen Sie das Terminal, und führen Sie den folgenden Befehl aus:

    		phonegap local build ios

	+ **Android**

		Öffnen Sie eine Eingabeaufforderung oder ein Terminalfenster, und führen Sie den folgenden Befehl aus:

		    phonegap local build android

4. Öffnen Sie jedes Projekt in der entsprechenden Entwicklungsumgebung, wie im vorhergehenden Abschnitt beschrieben.

>[AZURE.NOTE]Sie können den Code überprüfen, der auf Ihren mobilen Dienst zugreift, um Daten abzufragen und einzufügen. Der Code befindet sich in der js/index.js-Datei.

## Nächste Schritte
Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

* **[Hinzufügen von Authentifizierung zur App]** Informationen über die Authentifizierung von Benutzern der App mit einem Identitätsanbieter.  

* **[Hinzufügen von Pushbenachrichtigungen zur App](https://msdn.microsoft.com/magazine/dn879353.aspx)** Erfahren Sie, wie Sie die Registrierung für Pushbenachrichtigungen durchführen und wie Sie Pushbenachrichtigungen an Ihre App senden.

* **[Mobile Services HTML/JavaScript: Konzeptionelle Referenz](mobile-services-html-how-to-use-client-library.md)** Erfahren Sie mehr über die Verwendung der JavaScript-Clientbibliothek für den Zugriff auf Daten, das Aufrufen benutzerdefinierter APIs und das Ausführen der Authentifizierung.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Images. -->
[0]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
[1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
[2]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
[3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
[4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png

<!-- URLs. -->
[Hinzufügen von Authentifizierung zur App]: mobile-services-html-get-started-users.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Management Portal]: https://manage.windowsazure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
 

<!---HONumber=Oct15_HO3-->