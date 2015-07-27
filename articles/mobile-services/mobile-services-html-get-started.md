<properties
	pageTitle="Erste Schritte mit Azure Mobile Services für HTML 5-Apps"
	description="In diesem Lernprogramm erhalten Sie Informationen zu den ersten Schritten mit Azure Mobile Services für die HTML-Entwicklung."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html5"
	ms.devlang="javascript"
	ms.topic="article" 
	ms.date="04/24/2015"
	ms.author="ggailey777"/>


# <a name="getting-started"></a>Erste Schritte mit Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In diesem Lernprogramm erfahren Sie, wie Sie mit den Azure Mobile Services einen cloudbasierten Backend-Dienst zu einer HTML-App hinzufügen können. In diesem Lernprogramm erstellen Sie einen neuen mobilen Dienst und eine einfache <em>To-Do-Listen</em>-App, die App-Daten im neuen mobilen Dienst speichert. Sie können sich eine Videoversion dieses Lernprogramms ansehen, indem Sie rechts auf den Clip klicken.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a> <span class="time">3:51</span></div>
</div>

Unten sehen Sie einen Screenshot aus der fertigen App:

![][0]

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Mobile Services-Lernprogramme für HTML-Apps.

> [AZURE.IMPORTANT]Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F"%20target="_blank).

###Weitere Anforderungen

+ Für dieses Lernprogramm muss einer der folgenden Webserver auf Ihrem lokalen Computer laufen:

	+  **Unter Windows**: IIS Express. IIS Express wird mit dem [Microsoft Web Platform Installer] installiert.
	+  **Unter Mac OS X**: Python, das bereits installiert sein sollte.
	+  **Unter Linux**: Python. Die [neueste Version von Python] muss installiert werden.

	Die App kann zwar auf jedem Webserver gehostet werden, aber dies sind die von den heruntergeladenen Skripts unterstützen Webserver.

+ Ein Webbrowser, der HTML5 unterstützt.


## <a name="create-new-service"> </a>Erstellen eines neuen mobilen Dienstes

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Erstellen einer neuen HTML-App

Sobald Sie den mobilen Dienst erstellt haben, können Sie einem einfachen Schnellstart im Verwaltungsportal folgen, um eine neue App zu erstellen oder eine vorhandene App für die Verbindung zum mobilen Dienst zu ändern.

In diesem Abschnitt erstellen Sie eine neue HTML-App, die mit dem mobilen Dienst verbunden ist.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.


2. Klicken Sie auf der Schnellstartregisterkarte unter **Plattform auswählen** auf **Windows**. Erweitern Sie dann **Neue HTML-App erstellen**.

   	![][6]

   	Hierdurch werden die drei einfachen Schritte zum Erstellen und Hosten einer mit dem mobilen Dienst verbundenen HTML-App angezeigt.

  	![][7]

3. Klicken Sie auf **TodoItems-Tabelle erstellen**, um eine Tabelle zum Speichern der App-Daten zu erstellen.

4. Klicken Sie unter **Ihre App herunterladen und ausführen** auf **Download**.

  	Hierdurch werden die Websitedateien für die _To-Do-Listen_-Beispielanwendung, die mit dem mobilen Dienst verbunden ist, heruntergeladen. Speichern Sie die komprimierte Datei auf dem lokalen Computer, und merken Sie sich, wo Sie sie gespeichert haben.

5. Vergewissern Sie sich auf der Registerkarte **Konfigurieren**, dass `localhost` bereits im Feld **Anforderungen von Hostnamen zulassen** unter **Cross-Origin Resource Sharing (CORS)** eingetragen ist. Falls nicht, geben Sie `localhost` im Feld **Hostname** ein, und klicken Sie dann auf **Speichern**.

  	![][9]

	> [AZURE.IMPORTANT]Wenn Sie die Schnellstart-App auf einem anderen Webserver als "localhost" bereitstellen, müssen Sie den Hostnamen des Webservers der Liste **Anforderungen von Hostnamen zulassen** hinzufügen. Weitere Informationen finden Sie unter [Cross-Origin Resource Sharing (CORS)](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx"%20target="_blank).

## Hosten und Ausführen der HTML-App

Der letzte Schritt dieses Lernprogramms besteht im Hosten und Ausführen der neuen App auf dem lokalen Computer.

1. Navigieren Sie zum Speicherort der komprimierten Projektdateien, erweitern Sie die Dateien auf Ihrem Computer, und starten Sie eine der folgenden Befehlsdateien im Unterordner **server**.

	+ **launch-windows** (Windows-Computer)
	+ **launch-mac.command** (Mac OS X-Computer)
	+ **launch-linux.sh** (Linux-Computer)

	> [AZURE.NOTE]Bei einem Windows-Computer geben Sie `R` ein, wenn Sie von PowerShell zur Bestätigung aufgefordert werden, dass Sie das Skript ausführen möchten. Ihr Webbrowser rät Ihnen unter Umständen von einer Ausführung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall müssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortfährt.

	Hierdurch wird ein Webserver auf Ihrem lokalen Computer zum Hosten der neuen App gestartet.

2. Öffnen Sie die URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> in einem Webbrowser, um die App zu starten.

3. Geben Sie in der App in _Neue Aufgabe eingeben_ eine Beschreibung ein, wie zum Beispiel **Lernprogramm ausführen**, und klicken Sie dann auf **Hinzufügen**.

   	![][10]

   	Dadurch wird eine POST-Anforderung an den neuen, in Azure gehosteten mobilen Dienst gesendet. Daten von der Anforderung werden in die TodoItem-Tabelle eingefügt. In der Tabelle gespeicherte Werte werden vom mobilen Dienst zurückgegeben, und die Daten werden in der zweiten Spalte der App angezeigt.

	> [AZURE.NOTE]Sie können den Code, der zur Abfrage und zum Einfügen von Daten auf Ihren mobilen Dienst zugreift, in der Datei app.js finden und prüfen.

4. Zurück im Verwaltungsportal klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

   	![][11]

   	Nun können Sie die von der App in die Tabelle eingefügten Daten durchsuchen.

   	![][12]

## <a name="next-steps"> </a>Nächste Schritte
Da Sie den Schnellstart jetzt abgeschlossen haben, erfahren Sie, wie zusätzliche wichtige Aufgaben in Mobile Services ausgeführt werden:

* **[Erste Schritte mit Daten]** <br/>Lernen Sie mehr über das Speichern und Abfragen von Daten mit Mobile Services.

* **[Aufrufen einer benutzerdefinierten API aus einer HTML-Anwendung]** <br/>Verbinden Sie Ihre HTML-Anwendung mit einer benutzerdefinierten API, die von Mobile Services gehostet wird.

* **[Erste Schritte mit der Authentifizierung]** <br/>Informationen zur Authentifizierung von Benutzern Ihrer App bei einem Identitätsanbieter.

* **[Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]** <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit HTML/JavaScript.

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png





[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[Erste Schritte mit Daten]: /develop/mobile/tutorials/get-started-with-data-html
[Erste Schritte mit der Authentifizierung]: /develop/mobile/tutorials/get-started-with-users-html
[Aufrufen einer benutzerdefinierten API aus einer HTML-Anwendung]: mobile-services-html-call-custom-api.md

[Management Portal]: https://manage.windowsazure.com/
[Microsoft Web Platform Installer]: http://go.microsoft.com/fwlink/p/?LinkId=286333
[neueste Version von Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]: /develop/mobile/how-to-guides/work-with-html-js-client
[Cross-origin resource sharing]: http://msdn.microsoft.com/library/windowsazure/dn155871.aspx
 

<!---HONumber=July15_HO3-->