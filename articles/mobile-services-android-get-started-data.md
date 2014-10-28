<properties linkid="develop-mobile-tutorials-get-started-with-data-android" urlDisplayName="Get Started with Data - Android" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="Azure android data, Azure mobile services data, windows droid, windows android, microsoft droid, microsoft android" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal,glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/29/2014" ms.author="ricksal,glenga"></tags>

# Erste Schritte mit Daten in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data][mobile-services-selector-get-started-data]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Dieses Thema beschreibt die Nutzung von mobilen Azure-Diensten f&uuml;r die Nutzung von Daten in einer Android-App. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Daten&auml;nderungen beim Ausf&uuml;hren der App anzuzeigen.</p>

</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a><span class="time">15:32:00</span></div>

</div>

<div class="dev-callout"><b>Hinweis</b>
<p>Dieses Lernprogramm vermittelt ein besseres Verst&auml;ndnis davon, wie Sie mit mobilen Diensten in Azure Daten aus einer Android-App speichern und abrufen k&ouml;nnen. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart f&uuml;r mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie eventuell zuerst das Lernprogramm <a href="/de-de/develop/mobile/tutorials/get-started-android">Erste Schritte mit Mobile Services</a> abschlie&szlig;en.</p>
</div>

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Herunterladen des Android-App-Projekts][Herunterladen des Android-App-Projekts]
2.  [Erstellen des mobilen Dienstes][Erstellen des mobilen Dienstes]
3.  [Erstellen einer Datentabelle als Datenspeicher][Erstellen einer Datentabelle als Datenspeicher]
4.  [Aktualisieren der App zur Verwendung von Mobile Services][Aktualisieren der App zur Verwendung von Mobile Services]
5.  [Testen der App mit Mobile Services][Testen der App mit Mobile Services]

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie ben&ouml;tigen ein Azure-Konto, um dieses Lernprogramm auszuf&uuml;hren. Wenn Sie noch kein Konto haben, k&ouml;nnen Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

Für dieses Lernprogramm benötigen Sie das [Android-SDK für mobile Dienste][Android-SDK für mobile Dienste]. Dieses [Android-SDK][Android-SDK] enthält die integrierte Eclipse-Entwicklungsumgebung (IDE) und das Android Developer Tools (ADT)-Plugin und Android 4.2 oder eine neuere Version.

<div class="dev-callout"><b>Hinweis</b>
<p>Dieses Lernprogramm enth&auml;lt Anleitungen f&uuml;r die Installation des Android-SDK und des Android-SDK f&uuml;r mobile Dienste. Das heruntergeladene GetStartedWithData-Projekt ben&ouml;tigt Android 4.2 oder eine neuere Version. Das SDK f&uuml;r mobile Dienste ben&ouml;tigt dagegen nur Android 2.2 oder eine neuere Version.</p>
</div>

## <a name="download-app"></a><span class="short-header">Herunterladen des Projekts</span>Herunterladen des GetStartedWithData-Projekts

### Laden Sie den Beispielcode herunter

[WACOM.INCLUDE [download-android-sample-code][download-android-sample-code]]

### Prüfen der Version des Android-SDK

[WACOM.INCLUDE [Verify SDK][Verify SDK]]

### Prüfen und Ausführen des Beispielcodes

[WACOM.INCLUDE [mobile-services-android-run-sample-code][mobile-services-android-run-sample-code]]

## <a name="create-service"></a><span class="short-header">Erstellen eines mobilen Dienstes</span>Erstellen eines neuen mobilen Dienstes im Verwaltungsportal

[WACOM.INCLUDE [mobile-services-create-new-service-data][mobile-services-create-new-service-data]]

## <a name="add-table"></a><span class="short-header">Erstellen einer neuen Tabelle</span>Hinzufügen einer neuen Tabelle zum mobilen Dienst

[WACOM.INCLUDE [mobile-services-create-new-service-data-2][mobile-services-create-new-service-data-2]]

## <a name="update-app"></a><span class="short-header">Aktualisieren der App</span>Aktualisieren der App für den Datenzugriff über mobile Dienste

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data][mobile-services-android-getting-started-with-data]]

## <a name="test-app"></a><span class="short-header">Testen der App</span>Testen der App mit Ihrem neuen mobilen Dienst

Die App verwendet nun mobile Dienste als Back-End-Speicher, und Sie können sie entweder im Android-Emulator oder auf einem Android-Telefon gegen den mobilen Dienst testen.

1.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um das Projekt zu starten.

    Dadurch wird Ihre mit dem Android-SDK erstellte App ausgeführt, die mithilfe der Clientbibliothek Elemente aus Ihrem mobilen Dienst abfragt.

2.  Geben Sie wie zuvor einen lesbaren Text ein und klicken Sie auf **Hinzufügen**.

    Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3.  Klicken Sie im [Verwaltungsportal][Verwaltungsportal] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

    ![][]

    Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

Damit ist das Android-Lernprogramm **Erste Schritte mit Daten** abgeschlossen.

## <a name="next-steps"> </a>Nächste Schritte

Dieses Lernprogramm zeigt die Grundlagen für die Integration von Daten in mobilen Diensten in Android-Apps.

Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten][Prüfen und Ändern von Daten mit Skripten]
    Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging][Optimieren von Abfragen mittels Paging]
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Nach Abschluss der Daten-Lernprogramme können Sie diese anderen Android-Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Verschicken Sie mit Mobile Services eine einfache Pushbenachrichtigung an Ihre App.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Lernprogramm ansehen]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-android
  [Herunterladen des Android-App-Projekts]: #download-app
  [Erstellen des mobilen Dienstes]: #create-service
  [Erstellen einer Datentabelle als Datenspeicher]: #add-table
  [Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
  [Testen der App mit Mobile Services]: #test-app
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AED8DE357
  [Android-SDK für mobile Dienste]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  [Android-SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
  [download-android-sample-code]: ../includes/download-android-sample-code.md
  [Verify SDK]: ../includes/mobile-services-verify-android-sdk-version.md
  [mobile-services-android-run-sample-code]: ../includes/mobile-services-android-run-sample-code.md
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [mobile-services-android-getting-started-with-data]: ../includes/mobile-services-android-getting-started-with-data.md
  [Verwaltungsportal]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
  [Prüfen und Ändern von Daten mit Skripten]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Optimieren von Abfragen mittels Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-android
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-android
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-android
