<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />

# Erste Schritte mit Daten in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer iOS-App nutzen können. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

<div class="dev-callout"><b>Hinweis</b>
<p>Anhand dieses Lernprogramms soll Ihnen ein besseres Verst&auml;ndnis &uuml;ber die Mobile Services dahingehend vermittelt werden, wie Sie mit deren Hilfe Azure verwenden k&ouml;nnen, um Daten von einer iOS-App abzurufen und zu speichern. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart f&uuml;r mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie eventuell zuerst das Lernprogramm <a href="/de-de/develop/mobile/tutorials/get-started-ios">Erste Schritte mit Mobile Services</a> abschlie&szlig;en.</p>
</div>

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Herunterladen des iOS-App-Projekts][Herunterladen des iOS-App-Projekts]
2.  [Erstellen des mobilen Dienstes][Erstellen des mobilen Dienstes]
3.  [Erstellen einer Datentabelle als Datenspeicher][Erstellen einer Datentabelle als Datenspeicher]
4.  [Aktualisieren der App zur Verwendung von Mobile Services][Aktualisieren der App zur Verwendung von Mobile Services]
5.  [Testen der App mit Mobile Services][Testen der App mit Mobile Services]

Für dieses Lernprogramm ist Folgendes erforderlich:

-   [Mobile Services iOS SDK][Mobile Services iOS SDK] und [XCode 4.5][XCode 4.5] sowie iOS 5.0 oder höhere Versionen.
-   Ein Microsoft Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].
    </p>
    </div>

## <a name="download-app"></a>Download des GetStartedWithData-Projekts

Dieses Lernprogramm baut auf der [GetStartedWithData-App][GetStartedWithData-App] auf, bei der es sich um eine iOS-App handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-iOS-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1.  Laden Sie die GetStartedWithData-[Beispiel-App][GetStartedWithData-App] herunter.

2.  Öffnen Sie in Xcode das heruntergeladene Projekt, und prüfen Sie die Datei "TodoService.m".

    Beachten Sie, dass acht **// TODO**-Kommentare vorhanden sind. Diese geben die von Ihnen durchzuführenden Schritte an, damit diese App mit dem mobilen Dienst funktionieren kann.

3.  Klicken Sie auf die Schaltfläche **Ausführen** (oder drücken Sie die Taste Command+R), um das Projekt neu zu erstellen und die App zu starten.

4.  Geben Sie in der App einen Text in das Textfeld ein. Klicken Sie anschließend auf die Schaltfläche **+**.

    ![][0]

    Beachten Sie, dass der gespeicherte Text in der unteren Liste angezeigt wird.

## <a name="create-service"></a>Erstellen eines neuen mobilen Diensts im Verwaltungsportal

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a>Hinzufügen einer neuen Tabelle zum mobilen Dienst

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

## <a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst

1.  Wählen Sie unter Xcode einen Emulator, den Sie bereitstellen (entweder iPhone oder iPad). Klicken Sie auf die Schaltfläche **Ausführen** (oder drücken Sie die Taste Command+R), um das Projekt neu zu erstellen und die App zu starten.

    Hierdurch wird der mit dem iOS SDK erstellte Azure Mobile Services-Client ausgeführt, der Elemente vom mobilen Dienst abfragt.

2.  Geben Sie wie zuvor Text im Textfeld ein, und klicken Sie danach auf die Schaltfläche **+**.

    Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3.  Klicken Sie im [Verwaltungsportal][Verwaltungsportal] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

    ![][1]

    Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** für iOS beendet.

## <a name="next-steps"></a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer iOS-App für die Arbeit mit Daten in Mobile Services aufgezeigt.

Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten][Prüfen und Ändern von Daten mit Skripten]
    Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging][Optimieren von Abfragen mittels Paging]
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie die folgenden weiteren iOS-Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Verschicken Sie mit Mobile Services eine einfache Pushbenachrichtigung an Ihre App.



  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Herunterladen des iOS-App-Projekts]: #download-app
  [Erstellen des mobilen Dienstes]: #create-service
  [Erstellen einer Datentabelle als Datenspeicher]: #add-table
  [Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
  [Testen der App mit Mobile Services]: #test-app
  [Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
  [GetStartedWithData-App]: http://go.microsoft.com/fwlink/p/?LinkId=268622
  [0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [mobile-services-ios-enable-mobile-service-access]: ../includes/mobile-services-ios-enable-mobile-service-access.md
  [Verwaltungsportal]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png
  [Prüfen und Ändern von Daten mit Skripten]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Optimieren von Abfragen mittels Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-ios
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-ios
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-ios
