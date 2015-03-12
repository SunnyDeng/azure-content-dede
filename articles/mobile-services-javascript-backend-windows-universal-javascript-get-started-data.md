<properties 
	pageTitle="Erste Schritte mit Daten (Windows Store JavaScript) | Mobile Dev Center" 
	description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Ihrer Windows Store-JavaScript-App. 
	services="mobile-services" 
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
	ms.date="09/26/2014" 
	ms.author="glenga"/>


# Hinzufügen von Mobile Services zu einer vorhandenen App
[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Dieses Thema erläutert den Einsatz von Azure Mobile Services für die Nutzung von Daten in universellen Windows-Apps. Universelle Windows-App-Lösungen beinhalten Projekte für Windows Store 8.1, Windows Phone Store 8.1-Apps und ein gemeinsames, geteiltes Projekt. Weitere Informationen finden Sie unter [Erstellen universeller Windows-Apps, die Windows und Windows Phone als Ziel verwenden](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

In diesem Lernprogramm laden Sie ein Visual Studio 2013-Projekt für eine universelle Windows-App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in die App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

>[AZURE.NOTE]In diesem Thema wird erläutert, wie Sie mithilfe der in Visual Studio Express 2013 für Windows mit Update 3 enthaltenen Tools einen mobilen Dienst mit einer universellen Windows-App verbinden. Mit denselben Schritten können Sie einen mobilen Dienst mit einer Windows Store- oder einer Windows Phone Store 8.1-App verbinden. Informationen zum Verbinden eines mobilen Diensts mit einer Windows Phone 8.0- oder Windows Phone Silverlight 8.1-App finden Sie unter [Erste Schritte mit Daten für Windows Phone](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data).

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des Windows Store-App-Projekts][Abrufen der Windows Store-App] 
2. [Erstellen des mobilen Diensts]
3. [Hinzufügen einer Datentabelle als Speicher]
4. [Aktualisieren der App zur Verwendung von Mobile Services]
5. [Testen der App mit Mobile Services]

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Detaillierte Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-javascript-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Express 2013 für Windows</a> (Update 2 oder eine höhere Version). 

##<a name="download-app"></a>Download des GetStartedWithData-Projekts

[AZURE.INCLUDE [mobile-services-windows-universal-javascript-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##<a name="create-service"></a>Erstellen eines mobilen Diensts in Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="8">
<li><p>Navigieren Sie im Projektmappen-Explorer zum Unterordner <strong>services\mobileService\scripts</strong>, öffnen Sie die Skriptdatei "service.js", und beachten Sie die neue globale Variable, die wie folgt aussieht:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>In diesem Code wird der Zugriff auf den neuen mobilen Dienst in Ihrer App über globale Variable bereitgestellt. Der Client wird durch Angabe des URI und des Anwendungsschlüssels des neuen mobilen Diensts erstellt. Da Sie einen Verweis auf das Skript zur Datei default.html hinzugefügt haben, ist diese Variable in allen Skriptdateien verfügbar, die von dieser Seite aus referenziert werden.</p>
</li>
<li><p>Öffnen Sie die Projektdatei "default.html", suchen Sie den Verweis auf die neue Skriptdatei "service.js", und stellen Sie sicher, dass der im Verweis angegebene Pfad folgendermaßen lautet:</p>
<pre><code>&lt;script src="/services/mobileServices/scripts/todolist.js"&gt;</script></code></pre>
<p>Gegenwärtig gibt es einen Fehler in Visual Studio, der dazu führt, dass im Pfad ein falscher Ordnername erzeugt wird.</p></li>
<li><p>Klicken Sie mit der rechten Maustaste auf das Windows Phone-App-Projekt, klicken Sie auf <strong>Hinzufügen</strong> und dann auf <strong>Verbundener Dienst...</strong>. Wählen Sie anschließend den soeben erstellten mobilen Dienst aus, und klicken Sie auf <strong>OK</strong>. </p>
<p>Dieselbe neue Codedatei wird dem Windows Phone Store-App-Projekt hinzugefügt. Überprüfen und korrigieren Sie auch den Pfad, der in dem Verweis angegeben ist, welcher der Datei "default.html" hinzugefügt wurde.</p></li>
</ol>

##<a name="add-table"></a>Hinzufügen einer neuen Tabelle zum mobilen Dienst

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[AZURE.NOTE]Neue Tabellen werden mit den Spalten "Id", "__createdAt", "__updatedAt" und "__version" erstellt. Wenn das dynamische Schema aktiviert ist, generieren Mobile Services automatisch neue Spalten auf Grundlage des JSON-Objekts in der Einfüge- oder Aktualisierungsanforderung. Weitere Informationen finden Sie unter [Dynamisches Schema](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx).

#<a name="update-app"></a>Aktualisieren der App zur Verwendung des mobilen Diensts

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="test-azure-hosted"></a>Testen des in Azure gehosteten mobilen Diensts

Nun können wir beide Versionen der universellen Windows-App mit dem in Azure gehosteten mobilen Dienst testen.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p>Klicken Sie im <a href="https://manage.windowsazure.com/" target="_blank">Verwaltungsportal</a> auf <strong>Mobile Services</strong> und dann auf Ihren mobilen Dienst.<p></li>
<li><p>Klicken Sie auf die Registerkarte <strong>Daten</strong> und dann auf <strong>Durchsuchen</strong>.</p>
<p>Beachten Sie, dass die <strong>TodoItem</strong>-Tabelle nun Daten mit von Mobile Services generierten ID-Werten enthält, und dass der Tabelle automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt wurden.</p></li>
</ol>

![](./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png)
     	
Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung eines universellen Windows-App-Projekts für die Arbeit mit Daten in Mobile Services behandelt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

* [Überprüfen und Ändern von Daten mit Skripts]
  <br/>Erfahren Sie mehr über die Verwendung von Serverskripts in Mobile Services, um die von Ihrer App gesendeten Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mit Paging]
  <br/>Erfahren Sie, wie Sie Paging in Abfragen zum Steuern der in einer einzelnen Anforderung verarbeiteten Datenmengen verwenden können.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie eines der folgenden Lernprogramme ausprobieren:

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Benutzer in Ihrer App authentifiziert werden.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

* [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit HTML und JavaScript.

<!-- Anchors. -->

[Abrufen der Windows Store-App]: #download-app
[Erstellen des mobilen Diensts]: #create-service
[Hinzufügen einer Datentabelle als Speicher]: #add-table
[Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
[Testen der App mit Mobile Services]: #test-app
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Optimieren von Abfragen mit Paging]: /de-de/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Website mit Codebeispielen für Entwickler]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library/


<!--HONumber=42-->
