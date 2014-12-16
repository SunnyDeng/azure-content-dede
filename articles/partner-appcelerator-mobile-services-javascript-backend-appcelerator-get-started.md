<properties pageTitle="Erste Schritte mit Azure Mobile Services für Appcelerator Titanium" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Appcelerator development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="Appcelerator team;mahender" />

# <a name="getting-started"> </a>Erste Schritte mit Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

In diesem Lernprogramm erfahren Sie, wie Sie Azure Mobile Services in Ihren mit Appcelerator erstellten Anwendungen nutzen können.

In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache To-Do-Listen-App, die App-Daten im neuen mobilen Dienst speichert. Der von Ihnen zu erstellende mobile Dienst verwendet JavaScript für die serverseitige Geschäftslogik.

Um dieses Lernprogramm absolvieren zu können, benötigen Sie [Appcelerator Titanium].

Wenn Sie für iOS entwickeln, brauchen Sie außerdem Xcode 5.1 und iOS 7.1 SDK oder neuer. 

Wenn Sie für Android entwickeln, benötigen Sie außerdem Android 4.3 SDK oder neuer.

## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Dienstes

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Erstellen einer neuen Appcelerator-App

1. Wählen Sie im Mobile Services-Portal die Registerkarte **Daten** für Ihren mobilen Dienst.

2. Klicken Sie auf **Tabelle hinzufügen**, und erstellen Sie eine neue Tabelle namens **TodoItem**.

3. Laden Sie eine neue Appcelerator [Todolist application] herunter und entpacken Sie das Projekt.

4. Wenn Sie dies noch nicht durchgeführt haben, laden Sie [Appcelerator Titanium Studio][Appcelerator Titanium] und SDK V. 3.2.1 oder neuer herunter, und installieren Sie diese. Außerdem benötigen Sie Xcode (V. 5.0 +) und/oder Android SDK (V. 4.3+), um das Projekt ausführen zu können.

5. Kehren Sie zum Mobile Services-Portal zurück und wählen Sie unter **Dashboard** den Punkt **Schlüssel verwalten**, und kopieren Sie den **Anwendungsschlüssel** heraus.

5. Ersetzen Sie in der Datei "index.js" der App `<---App-Name---->` und `<------------APP-SCHLÜSSEL------------>` durch die Werte aus dem mobilen Dienst.

## Ausführen der neuen Appcelerator-App ##

Der letzte Schritt dieses Lernprogramms besteht im Erstellen und Ausführen der neuen App.

1. Öffnen Sie Titanium Studio, gehen Sie zu **File -> Import**, und importieren Sie das Projekt, das Sie zuvor heruntergeladen haben.

    ![][0]

2.	Wählen Sie im nächsten Bildschirm **Existing Projects into Workspace**, und klicken Sie dann auf **Next**.

    ![][1]

3.	Verwenden Sie im Bildschirm zur Projektauswahl die Option für das Durchsuchen und lokalisieren Sie das Azure Titanium-Projekt, das Sie vom Azure-Verwaltungsportal heruntergeladen haben.

    ![][2]

4.	Nun wird schließlich das Projekt angezeigt, das sie im Abschnitt "Projects" ausgewählt haben. Optional können Sie die Option &quot;Copy projects into workspace&quot; aktivieren, wodurch das heruntergeladene Projekt in den Arbeitsbereich kopiert wird. Klicken Sie schließlich auf "Finish", um das Projekt in Titanium Studio zu öffnen.

    ![][3]

5.	Wählen Sie die Plattform, auf der das Projekt laufen soll (iOS/Android).

    ![][4]

6.	Klicken Sie auf die Schaltfläche "Run", um das Projekt zu erstellen und die App im iPhone-Simulator oder dem Android-Emulator zu starten.

7.	Treffen Sie Ihre Wahl entsprechend der Einstellungen im Azure-Verwaltungsportal.

    ![][5]

8.	Klicken Sie im nächsten Bildschirm auf das (+)-Symbol, geben Sie einen aussagekräftigen Text ein, wie etwa ;Lernprogramm beenden&quot;, und klicken Sie auf die Schaltfläche "Speichern".<br />

    ![][6]

    ![][7]

Dadurch wird eine POST-Anforderung an den neuen, in Windows Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Einträge werden von dem mobilen Dienst zurückgegeben, und die Daten werden in der Liste angezeigt.

![][8]


>[WACOM.NOTE] Sie können den Code, der zur Abfrage und zum Einfügen von Daten auf Ihren mobilen Dienst zugreift, in der Datei "TodoService.m" finden und prüfen.

4. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItems**.

   	![][9]

   	Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

   	![][10]


## <a name="next-steps"> </a>Nächste Schritte
Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden: 

* [Erste Schritte mit der Authentifizierung]
  <br/>Informationen über die Authentifizierung von Benutzern der App mit einem Identitätsanbieter.

* [Erste Schritte mit Pushbenachrichtigungen] 
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.



<!-- Images. -->
[0]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image007.png
[1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image008.png
[2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image009.png
[3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image010.png
[4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image011.png
[5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image012.png
[6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image013.png
[7]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image014.png
[8]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image015.png
[9]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-tab.png
[10]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-browse.png

<!-- URLs. -->
[Todolist-Anwendung]: http://go.microsoft.com/fwlink/p/?LinkId=506859
[Appcelerator Titanium]: http://go.microsoft.com/fwlink/p/?LinkID=509987
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push
