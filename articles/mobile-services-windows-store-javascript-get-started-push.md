<properties 
	pageTitle="Erste Schritte mit Pushbenachrichtigungen (Legacypushvorgang) | Mobile Dev Center" 
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Services zum Senden von Pushbenachrichtigungen an Ihre Windows Store-JavaScript-App (Legacypush)." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="glenga"/>

# Hinzufügen von Pushbenachrichtigungen zur Mobile Services-App (Legacypushvorgang)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/de-de/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/de-de/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title=".NET backend">.NET-Back-End</a> | <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push/"  title="JavaScript backend" class="current">JavaScript-Back-End</a></div>		

In diesem Thema wird die Verwendung von Azure Mobile Services über Visual Studio 2013 zum Senden von Pushbenachrichtigungen an Ihre Windows Store-App erläutert. In diesem Lernprogramm verwenden Sie den Windows-Pushbenachrichtigungsdienst, um direkt in Visual Studio Pushbenachrichtigungen zum Schnellstartprojekt hinzuzufügen. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

>[AZURE.NOTE]Dieses Thema behandelt <em>vorhandene</em> mobile Dienste, für die <em>noch kein Upgrade</em> für den Einsatz der Notification Hubs-Integration durchgeführt wurde. Wenn Sie einen <em>neuen</em> mobilen Dienst erstellen, ist diese integrierte Funktionalität automatisch aktiviert. Informationen zu neuen mobilen Diensten finden Sie unter [Erste Schritte mit Pushbenachrichtigungen](/de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/)..
>
>Mobile Services werden in Azure Notification Hubs integriert, damit zusätzliche Pushbenachrichtigungsfunktionen wie Vorlagen, mehrere Plattformen und verbesserte Skalierung unterstützt werden können. <em>Sie sollten Ihre vorhandenen mobilen Dienste möglichst für die Verwendung von Notification Hubs aktualisieren</em>. Nach dem Upgrade finden Sie weitere Informationen in dieser Version von [Erste Schritte mit Pushbenachrichtigungen](/de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Registrieren Ihrer App für Pushbenachrichtigungen und Konfigurieren von Mobile Services]
2. [Aktualisieren des erzeugten Pushbenachrichtigungscodes]
3. [Einfügen von Daten zum Empfangen von Benachrichtigungen]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten] abschließen, um Ihr Projekt mit dem mobilen Dienst zu verbinden. Wenn kein mobiler Dienst verbunden wurde, stellt der Assistent für Pushbenachrichtigungen diese Verbindung für Sie her. 

<h2><a name="register"></a>Hinzufügen und Konfigurieren von Pushbenachrichtigungen in der App</h2>

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Öffnen Sie die generierte Codedatei "push.register.js", und überprüfen Sie den Code, der die Installations-ID und den Kanal für das Gerät abruft und diese Daten in die neue Tabelle <strong>Kanäle</strong> einfügt.</p> 

	<p>Diese Tabelle wurde in Ihrem mobilen Dienst vom Assistent für Pushbenachrichtigungen erstellt. Dieser Code stellt sicher, dass eine Registrierung des Gerätes versucht wird, sobald die App aktiviert wird.</p></li>
<li><p>Erweitern Sie im Server-Explorer <strong>Azure</strong>, <strong>Mobile Services</strong>, Ihren Dienstnamen und <strong>Kanäle</strong>, und öffnen Sie dann die Datei "insert.js".</p> 

<p>Diese Datei, die in Ihrem mobilen Dienst gespeichert wird, enthält JavaScript-Code, der ausgeführt wird, wenn ein Client eine Anfrage zur Registrierung eines Gerätes durch Einfügen von Daten in die Kanaltabelle sendet.</p> 

> [AZURE.NOTE] Die erste Version dieser Datei enthält Code, der das Gerät auf eine eventuell bereits vorhandene Registrierung prüft. Außerdem enthält sie Code, der eine Pushbenachrichtigung sendet, wenn eine neue Registrierung zur Kanaltabelle hinzugefügt wird. Der Code, der eine Pushbenachrichtigung sendet, kann in jede registrierte Skriptdatei eingefügt werden. Der Speicherort dieses Skripts hängt davon ab, wie die Benachrichtigung ausgelöst wird. Skripts können während eines Einfügungs-, Aktualisierungs-, Lösch- oder Lesevorgangs in einer Tabelle, einem geplanten Auftrag oder als benutzerdefinierte API registriert werden. Weitere Informationen finden Sie unter [Arbeiten mit Serverskripts in Mobile Services](http://go.microsoft.com/fwlink/p/?LinkID=287178).
</li> 
<li><p>Drücken Sie die Taste F5, um die App auszuführen und zu überprüfen, ob eine Benachrichtigung vom mobilen Dienst empfangen wird.</p>
<p>Die Benachrichtigung wurde durch Einfügen einer Zeile in die Tabelle neuer Kanäle erzeugt. Dies ist die Geräteregistrierung.</p>
</li>
</ol>

Während der erzeugte Code das Anzeigen einer Benachrichtigung erleichtert, wenn die App ausgeführt wird, ist dies im Allgemein kein sinnvolles Szenario. Als Nächstes entfernen Sie den Benachrichtigungscode aus der Kanaltabelle und ersetzen ihn, mit einigen Änderungen, in der Tabelle TodoItem. 

<h2><a name="update-scripts"></a>Aktualisieren des erzeugten Pushbenachrichtigungscodes</h2>

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

<h2><a name="test"></a>Testen von Pushbenachrichtigungen in der App</h2>

1. Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2. Geben Sie in der App Text in das Feld **TodoItem einfügen** ein, und klicken Sie dann auf **Speichern**.

   	![][13]

   	Bitte beachten Sie, dass die App nach dem Einfügen eine Pushbenachrichtigung von WNS erhält.

   	![][14]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der von Mobile Services bereitgestellten Pushbenachrichtigungsfunktion gezeigt. Wenn Sie Ihrer App fortgeschrittenere Funktionen, wie z. B. Senden von plattformübergreifenden Benachrichtigungen, abonnementbasiertes Routing oder sehr große Mengen von Benachrichtigungen, hinzufügen möchten, sollten Sie Azure Notification Hubs mit Ihrem mobilen Dienst verwenden. Weitere Informationen finden Sie in einem der folgenden Themen zu Notification Hubs:

+ [Erste Schritte mit Notification Hubs]
  <br/>Erfahren Sie, wie Sie Notification Hubs in Ihrer Windows Store-App einsetzen können.

+ [Senden von Benachrichtigungen an Abonnenten]
	<br/>Erfahren Sie, wie sich Benutzer registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

+ [Senden von Benachrichtigungen an Benutzer]
	<br/>Erfahren Sie, wie Sie Pushbenachrichtigungen von einem mobilen Dienst an bestimmte Benutzer auf beliebigen Geräten senden können.

+ [Senden von plattformübergreifenden Benachrichtigungen an Benutzer]
	<br/>Erfahren Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen mit einem mobilen Dienst gesendet werden, ohne dass Sie in Ihrem Back-End auf plattformspezifische Nutzlasten zurückgreifen müssen.

Weitere Informationen zu Mobile Services:

* [Erste Schritte mit Daten]
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Sie Benutzer der App mit einem Windows-Konto authentifizieren.

* [Mobile Services: Serverskriptreferenz]
  <br/>Erfahren Sie mehr über das Registrieren und Verwenden von Serverskripts.

* [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit HTML und JavaScript.  

<!-- Anchors. -->
[Registrieren Ihrer App für Pushbenachrichtigungen und Konfigurieren von Mobile Services]: #register
[Aktualisieren des erzeugten Pushbenachrichtigungscodes]: #update-scripts
[Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test
[Nächste Schritte]:#next-steps

<!-- Images. -->







[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-js/
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-js
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-js
[Senden von Pushbenachrichtigungen an App-Benutzer]: /de-de/develop/mobile/tutorials/push-notifications-to-users-js
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/get-started-with-push-js

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-html-js-client/
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Erste Schritte mit Notification Hubs]: /de-de/manage/services/notification-hubs/getting-started-windows-dotnet/
[Was sind Notification Hubs?]: /de-de/develop/net/how-to-guides/service-bus-notification-hubs/
[Senden von Benachrichtigungen an Abonnenten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet/
[Senden von Benachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users/
[Senden von plattformübergreifenden Benachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users-xplat-mobile-services/


<!--HONumber=42-->
