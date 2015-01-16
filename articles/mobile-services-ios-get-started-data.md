<properties urlDisplayName="Get Started with Data" pageTitle="Erste Schritte mit Daten (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Ihrer iOS-App." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Hinzufügen von Mobile Services zu einer vorhandenen App

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer iOS-App nutzen können. In diesem Lernprogramm laden Sie eine App herunter, speichern die Daten im Speicher, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in die App, und melden sich am Azure-Verwaltungsportal an, um die beim Ausführen der App an den Daten vorgenommenen Änderungen anzuzeigen.

<div class="dev-callout"><b>Hinweis</b>
<p>Anhand dieses Lernprogramms soll Ihnen ein besseres Verständnis über die Mobile Services dahingehend vermittelt werden, wie Sie mit deren Hilfe Azure verwenden können, um Daten von einer iOS-App abzurufen und zu speichern. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Wenn dies Ihre erste Erfahrung mit mobilen Diensten ist, sollten Sie abwägen, zunächst das Lernprogramm <a href="/de-de/develop/mobile/tutorials/get-started-ios">Erste Schritte mit Mobile Services</a> durchzuführen.</p>
</div>

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des iOS-App-Projekts]
2. [Erstellen des mobilen Dienstes]
3. [Erstellen einer Datentabelle als Datenspeicher]
4. [Aktualisieren der App zur Verwendung von Mobile Services]
5. [Testen der App mit Mobile Services]

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Mobile Services iOS SDK] und [XCode 4.5][Installieren von Xcode] sowie iOS 5.0 oder höhere Versionen.
+ Ein Microsoft Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

##<a name="download-app"></a>Download des GetStartedWithData-Projekts

Dieses Lernprogramm baut auf der [GetStartedWithData-App][GitHub] auf, bei der es sich um eine iOS-App handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-iOS-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1. Laden Sie die GetStartedWithData [Beispiel-App][GitHub] herunter.

2. Öffnen Sie in Xcode das heruntergeladene Projekt, und prüfen Sie die Datei "TodoService.m".

   	Beachten Sie, dass acht **// TODO**-Kommentare vorhanden sind. Diese geben die von Ihnen durchzuführenden Schritte an, damit diese App mit dem mobilen Dienst funktionieren kann.

3. Klicken Sie auf die Schaltfläche **Ausführen** (oder drücken Sie die Taste Command+R), um das Projekt neu zu erstellen und die App zu starten.

4. Geben Sie in der App einen Text in das Textfeld ein. Klicken Sie anschließend auf die Schaltfläche **+**.

   	![][0]  

   	Beachten Sie, dass der gespeicherte Text in der unteren Liste angezeigt wird.

##<a name="create-service"></a>Erstellen eines neuen mobilen Diensts im Verwaltungsportal

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Hinzufügen einer neuen Tabelle zum mobilen Dienst

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst

1. Wählen Sie unter Xcode einen Emulator, den Sie bereitstellen (entweder iPhone oder iPad). Klicken Sie auf die Schaltfläche **Ausführen** (oder drücken Sie die Taste Command+R), um das Projekt neu zu erstellen und die App zu starten.

   	Hierdurch wird der mit dem iOS SDK erstellte Azure Mobile Services-Client ausgeführt, der Elemente vom mobilen Dienst abfragt.

2. Geben Sie wie zuvor Text im Textfeld ein, und klicken Sie danach auf die Schaltfläche **+**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3. Klicken Sie im [Verwaltungsportal] auf **Mobile Services**, und anschließend auf Ihren mobilen Dienst.

4. Klicken Sie auf die Registerkarte **Daten**,und dann auf **Durchsuchen**.

   	![][9]

   	Beachten Sie, dass die **TodoItem**Tabelle nun Daten mit von Mobile Services generierten ID-Werten enthält. Der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

Damit wird dieses Lernprogramm für iOS **Erste Schritte mit Daten** beendet.

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
[Erstellen des mobilen Dienstes]: #create-service
[Erstellen einer Datentabelle als Datenspeicher]: #add-table
[Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
[Testen der App mit Mobile Services]: #test-app
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png







[8]: ./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-ios
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-ios
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-ios
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-ios
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-ios

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Installieren von Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub-Repository]: http://go.microsoft.com/fwlink/p/?LinkId=268784
