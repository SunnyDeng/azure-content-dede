<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga" />

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services (Legacypushvorgang)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" class="current">Windows Store C#</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/de-de/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/de-de/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
    <a href="/de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title=".NET-Backend">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/"  title="JavaScript-Backend" class="current">JavaScript-Backend</a></div>

In diesem Thema wird die Verwendung von Azure Mobile Services über Visual Studio 2013 zum Senden von Pushbenachrichtigungen an Ihre Windows Store-App erläutert. In diesem Lernprogramm verwenden Sie den Windows-Pushbenachrichtigungsdienst, um direkt in Visual Studio Pushbenachrichtigungen zum Schnellstartprojekt hinzuzufügen. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

> [WACOM.NOTE]In diesem Thema werden *vorhandene* mobile Dienste behandelt, die noch nicht für die Verwendung der Notification Hubs-Integration *aktualisiert* wurden. Wenn Sie einen *neuen* mobilen Dienst erstellen, wird diese integrierte Funktionalität automatisch aktiviert. Informationen zu neuen mobilen Diensten finden Sie unter [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen].
>
> Mobile Services werden in Azure Notification Hubs integriert, damit zusätzliche Pushbenachrichtigungsfunktionen wie Vorlagen, mehrere Plattformen und verbesserte Skalierung unterstützt werden können. *Sie sollten Ihre vorhandenen mobilen Dienste möglichst für die Verwendung von Notification Hubs aktualisieren*. Nach dem Upgrade finden Sie weitere Informationen in dieser Version von [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen].

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Registrieren Ihrer App für Pushbenachrichtigungen und Konfigurieren von Mobile Services][Registrieren Ihrer App für Pushbenachrichtigungen und Konfigurieren von Mobile Services]
2.  [Aktualisieren des erzeugten Pushbenachrichtigungscodes][Aktualisieren des erzeugten Pushbenachrichtigungscodes]
3.  [Einfügen von Daten zum Empfangen von Benachrichtigungen][Einfügen von Daten zum Empfangen von Benachrichtigungen]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] abschließen, um Ihr Projekt mit Mobile Services zu verbinden. Wenn kein mobiler Dienst verbunden wurde, stellt der Assistent für Pushbenachrichtigungen diese Verbindung für Sie her.

## <a name="register"></a><span class="short-header">Registrierung Ihrer App</span>Hinzufügen und Konfigurieren von Pushbenachrichtigungen in Ihrer App

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Erweitern Sie **services**, **mobile services** und Ihren Dienstnamen, öffnen Sie die erzeugte Codedatei, und überprüfen Sie anschließend die Methode **UploadChannel**, in der die Installations-ID und der Kanal für das Gerät abgerufen und diese Daten in die Tabelle neuer Kanäle eingefügt werden.

    Ein Aufruf dieser Methode wurde vom Assistenten zum Ereignishandler **OnLaunched** in der Codedatei App.xaml.cs hinzugefügt. Dies stellt sicher, dass eine Registrierung des Gerätes versucht wird, sobald die App gestartet wird.

2.  Erweitern Sie im Server-Explorer **Azure**, **Mobile Services**, Ihren Dienstnamen und **channels**, und öffnen Sie dann die Datei insert.js.

    Diese Datei, die in Ihrem mobilen Dienst gespeichert wird, enthält JavaScript-Code, der ausgeführt wird, wenn ein Client eine Anfrage zur Registrierung eines Gerätes durch Einfügen von Daten in die Kanaltabelle sendet.

    <div class="dev-callout"><b>Hinweis</b>
    <p>Die erste Version dieser Datei enth&auml;lt Code, der das Ger&auml;t auf eine eventuell bereits vorhandene Registrierung pr&uuml;ft. Au&szlig;erdem enth&auml;lt sie Code, der eine Pushbenachrichtigung sendet, wenn eine neue Registrierung zur Kanaltabelle hinzugef&uuml;gt wird. Der Code, der eine Pushbenachrichtigung sendet, kann in jede registrierte Skriptdatei eingef&uuml;gt werden. Der Speicherort dieses Skripts h&auml;ngt davon ab, wie die Benachrichtigung ausgel&ouml;st wird. Skripts k&ouml;nnen w&auml;hrend eines Einf&uuml;gungs-, Aktualisierungs-, L&ouml;sch- oder Lesevorgangs in einer Tabelle, einem geplanten Auftrag oder als benutzerdefinierte API registriert werden. Weitere Informationen erhalten Sie unter <a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">Verwenden von Serverskripts in Mobile Services.</a>.</p>
</div>

3.  Drücken Sie die Taste F5, um die App auszuführen und zu überprüfen, ob eine Benachrichtigung vom mobilen Dienst empfangen wird.

    Die Benachrichtigung wurde durch Einfügen einer Zeile in die Tabelle neuer Kanäle erzeugt. Dies ist die Geräteregistrierung.

Während der erzeugte Code das Anzeigen einer Benachrichtigung erleichtert, wenn die App ausgeführt wird, ist dies im Allgemein kein sinnvolles Szenario. Als Nächstes entfernen Sie den Benachrichtigungscode aus der Kanaltabelle und ersetzen ihn, mit einigen Änderungen, in der Tabelle TodoItem.

## <a name="update-scripts"></a><span class="short-header">Aktualisieren des Codes</span>Aktualisieren des erzeugten Pushbenachrichtigungscodes

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

## <a name="test"></a><span class="short-header">Testen der App</span>Testen von Pushbenachrichtigungen in Ihrer App

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

    ![][0]

    Bitte beachten Sie, dass die App nach dem Einfügen eine Pushbenachrichtigung von WNS erhält.

    ![][1]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Arbeit mit Daten in Mobile Services gezeigt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Erste Schritte mit Benachrichtigungshubs][Erste Schritte mit Benachrichtigungshubs]
    Lernen Sie, wie Sie Benachrichtigungshubs in Ihrer Windows Store-App einsetzen können.

-   [Senden von Benachrichtigungen an Abonnenten][Senden von Benachrichtigungen an Abonnenten]
    Erfahren Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

-   [Senden von Benachrichtigungen an Benutzer][Senden von Benachrichtigungen an Benutzer]
    Lernen Sie, wie Sie Pushbenachrichtigungen von einem mobilen Dienst an bestimmte Benutzer auf beliebigen Geräten senden können.

-   [Senden plattformübergreifender Benachrichtigungen an Benutzer][Senden plattformübergreifender Benachrichtigungen an Benutzer]
    Lernen Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen mit einem mobilen Dienst gesendet werden, ohne dass Sie in Ihrem Back-End auf plattformspezifische Nutzlasten zurückgreifen müssen.

Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]
    Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz][Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.



  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Registrieren Ihrer App für Pushbenachrichtigungen und Konfigurieren von Mobile Services]: #register
  [Aktualisieren des erzeugten Pushbenachrichtigungscodes]: #update-scripts
  [Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/
  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-dotnet/
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [mobile-services-create-new-push-vs2013-2]: ../includes/mobile-services-create-new-push-vs2013-2.md
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png
  [Erste Schritte mit Benachrichtigungshubs]: /de-de/manage/services/notification-hubs/getting-started-windows-dotnet/
  [Senden von Benachrichtigungen an Abonnenten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet/
  [Senden von Benachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users/
  [Senden plattformübergreifender Benachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library/
