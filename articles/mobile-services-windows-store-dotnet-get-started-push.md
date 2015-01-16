<properties pageTitle="Erste Schritte mit Pushbenachrichtigungen (Windows Store) | Mobile Dev Center" metaKeywords="" description="Erfahren Sie mehr über die Verwendung von Azure Mobile Services zum Senden von Pushbenachrichtigungen an Ihre Windows Store-App (Legacypush)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga" />

# Hinzufügen von Pushbenachrichtigungen zu Mobile Services-Apps (Legacy-Push)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" class="current">Windows Store C#</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/de-de/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/de-de/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title=".NET backend">.NET-Back-End</a> | <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/"  title="JavaScript backend" class="current">JavaScript-Back-End</a></div>	

In diesem Thema wird die Verwendung von Azure Mobile Services über Visual Studio 2013 zum Senden von Pushbenachrichtigungen an Ihre Windows Store-App erläutert. In diesem Lernprogramm verwenden Sie den Windows-Pushbenachrichtigungsdienst, um direkt in Visual Studio Pushbenachrichtigungen zum Schnellstartprojekt hinzuzufügen. Nach dem Abschluss sendet der mobile Dienst bei jedem Einfügen eines Datensatzes eine Pushbenachrichtigung.

>[WACOM.NOTE]In diesen Themen werden <em>vorhandene</em> mobile Dienste unterstützt, die <em>noch nicht</em> zum Verwenden der Notification-Hub-Integration aktualisiert wurden. Wenn Sie einen <em>neuen</em> mobilen Dienst erstellen, wird diese integrierte Funktionalität automatisch aktiviert. Weitere Informationen zu neuen mobilen Diensten finden Sie unter [Erste Schritte mit Pushbenachrichtigungen](/de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/).
>
>Mobile Dienste werden in Azure Notification-Hubs für die Unterstützung zusätzlicher Pushbenachrichtigungsfunktionen, wie z. B. Vorlagen, mehreren Plattformen und einer verbesserten Skalierung. <em>Sie sollten Ihre vorhandenen mobilen Dienste möglichst für die Verwendung von Notification Hubs aktualisieren</em>. Nach dem Upgrade finden Sie weitere Informationen unter [Erste Schritte mit Pushbenachrichtigungen](/de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Registrieren Ihrer App für Pushbenachrichtigungen und Konfigurieren von Mobile Services]
2. [Aktualisieren des erzeugten Pushbenachrichtigungscodes]
3. [Einfügen von Daten zum Empfangen von Benachrichtigungen]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten] abschließen, um Ihr Projekt mit Mobile Services zu verbinden. Wenn kein mobiler Dienst verbunden wurde, stellt der Assistent für Pushbenachrichtigungen diese Verbindung für Sie her. 

<h2><a name="register"></a>Hinzufügen und Konfigurieren von Pushbenachrichtigungen in der App</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Erweitern Sie <strong>services</strong>, <strong>mobile services</strong> und Ihren Dienstnamen, öffnen Sie die erzeugte Codedatei, und überprüfen Sie anschließend die Methode <strong>UploadChannel</strong>, in der die Installations-ID und der Kanal für das Gerät abgerufen und diese Daten in die Tabelle neuer Kanäle eingefügt werden.</p> 

<p>Ein Aufruf dieser Methode wurde vom Assistenten zum Ereignishandler <strong>OnLaunched</strong> in der Codedatei "App.xaml.cs" hinzugefügt. Dies stellt sicher, dass eine Registrierung des Gerätes versucht wird, sobald die App gestartet wird.</p></li> 
<li><p>Erweitern Sie im Server-Explorer <strong>Azure</strong>, <strong>Mobile Services</strong>, Ihren Dienstnamen und <strong>channels</strong>, und öffnen Sie dann die Datei "insert.js".</p> 

<p>Diese Datei, die in Ihrem mobilen Dienst gespeichert wird, enthält JavaScript-Code, der ausgeführt wird, wenn ein Client eine Anfrage zur Registrierung eines Gerätes durch Einfügen von Daten in die Kanaltabelle sendet.</p> 

<div class="dev-callout"><b>Hinweis</b>
	<p>Die erste Version dieser Datei enthält Code, der das Gerät auf eine eventuell bereits vorhandene Registrierung prüft. Außerdem enthält sie Code, der eine Pushbenachrichtigung sendet, wenn eine neue Registrierung zur Kanaltabelle hinzugefügt wird. Der Code, der eine Pushbenachrichtigung sendet, kann in jede registrierte Skriptdatei eingefügt werden. Der Speicherort dieses Skripts hängt davon ab, wie die Benachrichtigung ausgelöst wird. Skripts können während eines Einfügungs-, Aktualisierungs-, Lösch- oder Lesevorgangs in einer Tabelle, einem geplanten Auftrag oder als benutzerdefinierte API registriert werden. Weitere Informationen erhalten Sie unter <a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">Verwenden von Serverskripts in Mobile Services</a>.</p>
</div>
</li> 
<li><p>Drücken Sie die Taste F5, um die App auszuführen und zu überprüfen, ob eine Benachrichtigung vom mobilen Dienst empfangen wird.</p>
<p>Die Benachrichtigung wurde durch Einfügen einer Zeile in die Tabelle neuer Kanäle erzeugt. Dies ist die Geräteregistrierung.</p>
</li>
</ol>
Während der erzeugte Code das Anzeigen einer Benachrichtigung erleichtert, wenn die App ausgeführt wird, ist dies im Allgemeinen kein sinnvolles Szenario. Als Nächstes entfernen Sie den Benachrichtigungscode aus der Kanaltabelle und ersetzen ihn, mit einigen Änderungen, in der Tabelle TodoItem. 

<h2><a name="update-scripts"></a>Aktualisieren des erzeugten Pushbenachrichtigungscodes</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

<h2><a name="test"></a>Testen von Pushbenachrichtigungen in der App</h2>

1. Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2. Geben Sie in die App Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

   	![][13]

   	Bitte beachten Sie, dass die App nach dem Einfügen eine Pushbenachrichtigung von WNS erhält.

   	![][14]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Arbeit mit Daten in Mobile Services gezeigt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

+ [Erste Schritte mit Notification Hubs]
  <br/>Erfahren Sie, wie Sie Benachrichtigungshubs in Ihrer Windows Store-App einsetzen können.

+ [Senden von Benachrichtigungen an Abonnenten]
	<br/>Erfahren Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien empfangen können, die sie interessieren.

+ [Senden von Benachrichtigungen an Benutzer]
	<br/>Erfahren Sie, wie Sie Pushbenachrichtigungen aus einem Mobile Service an spezifische Benutzer auf einem beliebigen Gerät senden.

+ [Senden plattformübergreifender Benachrichtigungen an Benutzer]
	<br/>Erfahren Sie, wie Sie Vorlagen verwenden können, um Pushbenachrichtigungen von einem Mobile Service zu senden, ohne plattformspezifische Nutzdaten in Ihrem Back-End erstellen zu müssen.

Weitere Informationen zu Mobile Services:

* [Erste Schritte mit Daten]
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Sie Benutzer der App mit Windows-Konto authentifizieren.

* [Mobile Services: Serverskriptreferenz]
  <br/>Erfahren Sie mehr über das Registrieren und Verwenden von Serverskripts.

* [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET.

<!-- Anchors. -->
[Registrieren Ihrer App für Pushbenachrichtigungen und Konfigurieren von Mobile Services]: #register
[Aktualisieren des erzeugten Pushbenachrichtigungscodes]: #update-scripts
[Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test
[Nächste Schritte]:#next-steps

<!-- Images. -->











[13]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[Übermitteln einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-dotnet/
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-dotnet
[Senden von Pushbenachrichtigungen an App-Benutzer]: /de-de/develop/mobile/tutorials/push-notifications-to-users-dotnet
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/get-started-with-push-js

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[wns-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library/
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-dotnet
[Erste Schritte mit Notification Hubs]: /de-de/manage/services/notification-hubs/getting-started-windows-dotnet/
[Was sind Notification Hubs?]: /de-de/develop/net/how-to-guides/service-bus-notification-hubs/
[Senden von Benachrichtigungen an Abonnenten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet/
[Senden von Benachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users/
[Senden plattformübergreifender Benachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
