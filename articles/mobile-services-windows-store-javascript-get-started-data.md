<properties 
	pageTitle="Erste Schritte mit Daten (Windows Store JavaScript) | Mobile Dev Center" 
	description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Ihrer Windows Store-JavaScript-App." 
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
	ms.date="09/19/2014" 
	ms.author="glenga"/>


# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
	<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title=".NET backend">.NET-Back-End</a> | 
	<a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="JavaScript backend" class="current">JavaScript-Back-End</a>
</div>


In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer Windows Store-App nutzen können. In diesem Lernprogramm laden Sie ein Visual Studio 2013-Projekt für eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

>[AZURE.NOTE]Dieses Thema veranschaulicht, wie Azure Mobile Services einem Windows Store-Projekt mithilfe von Visual Studio 2013 hinzugefügt werden. Sie können denselben mobilen JavaScript-Back-End-Dienst einem Projekt für eine universelle Windows-App hinzufügen. Weitere Informationen finden Sie in der [Version für universelle Windows-Apps](/de-de/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data) dieses Lernprogram
	ms.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des Windows Store-App-Projekts][Abrufen der Windows Store-App] 
2. [Erstellen des mobilen Diensts]
3. [Hinzufügen einer Datentabelle als Speicher]
4. [Aktualisieren der App zur Verwendung von Mobile Services]
5. [Testen der App mit Mobile Services]

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F).
* Visual Studio 2013, mit dem einfacher eine Verbindung zwischen Ihrer Windows Store-App und Mobile Services hergestellt werden kann. Um dasselbe grundlegende Verfahren mit Visual Studio 2012 auszuführen, befolgen Sie die Schritte im Thema <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data-vs2012">Erste Schritte mit Daten in Mobile Services mit Visual Studio 2012</a>. 

<h2><a name="download-app"></a>Download des GetStartedWithData-Projekts</h2>

Dieses Lernprogramm baut auf der [GetStartedWithMobileServices-App][Website mit Codebeispielen für Entwickler] auf. Dabei handelt es sich um ein Windows Store-App-Projekt in Visual Studio 2013. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.  

1. Laden Sie die JavaScript-Version der GetStartedWithData-Beispiel-App von der [Website mit den Codebeispielen für Entwickler] herunter. 

2. Öffnen Sie das heruntergeladene Projekt in Visual Studio 2012 Express für Windows 8, erweitern Sie den Ordner **js**, und sehen Sie sich die Datei "default.js" an.

   	Beachten Sie, dass die hinzugefügten **TodoItem**-Objekte in einem speicherinternen **List**-Objekt gespeichert werden.

3. Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

4. Geben Sie in der App in **TodoItem einfügen** Text ein, und klicken Sie dann auf **Speichern**.

   	![][0]  

   	Beachten Sie, dass der gespeicherte Text in der zweiten Spalte unter **Query and update data** angezeigt wird.

##<a name="create-service"></a>Erstellen eines mobilen Diensts in Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>Erweitern Sie im Projektmappen-Explorer die Ordner <strong>Dienste</strong>, <strong>Mobile Services</strong>, <strong>&lt;Ihr_Dienst&gt;</strong>, öffnen Sie die Skriptdatei "service.js", und beachten Sie die neue globale Variable, die wie das folgende Beispiel aussieht:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>In diesem Code wird der Zugriff auf den neuen mobilen Dienst in Ihrer App über globale Variable bereitgestellt. Der Client wird durch Angabe des URI und des Anwendungsschlüssels des neuen mobilen Diensts erstellt. Da Sie einen Verweis auf das Skript zur Datei default.html hinzugefügt haben, ist diese Variable in allen Skriptdateien verfügbar, die von dieser Seite aus referenziert werden.</p>
</li>
</ol>

##<a name="add-table"></a>Hinzufügen einer neuen Tabelle für die Datenspeicherung

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[AZURE.NOTE]Neue Tabellen werden mit den Spalten "Id", "__createdAt", "__updatedAt" und "__version" erstellt. Wenn das dynamische Schema aktiviert ist, generieren Mobile Services automatisch neue Spalten auf Grundlage des JSON-Objekts in der Einfüge- oder Aktualisierungsanforderung. Weitere Informationen finden Sie unter [Dynamisches Schema](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx).

#<a name="update-app"></a>Aktualisieren der App zur Verwendung des mobilen Diensts

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst

1. Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2. Geben Sie wie zuvor Text in **TodoItem einfügen** ein, und klicken Sie dann auf **Speichern**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3. Klicken Sie im [Verwaltungsportal] auf **Mobile Services** und dann auf Ihren mobilen Dienst.

4. Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

   	![][9]
  
   	Beachten Sie, dass die **TodoItem**-Tabelle nun Daten mit von Mobile Services generierten ID-Werten enthält, und dass der Tabelle automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt wurden.

5. Markieren Sie in der App einen Eintrag in der Liste, kehren Sie dann zurück zur Registerkarte "Durchsuchen" des Portals, und klicken Sie auf **Aktualisieren**. 

  	Beachten Sie, dass der Wert "complete" von **false** in **true** geändert wurde.

6. Ersetzen Sie in der Projektdatei "default.js" die vorhandene **RefreshTodoItems**-Funktion durch den folgenden Code, mit dem abgeschlossene Einträge herausgefiltert werden:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed ite
	ms. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listIte
	ms.winControl.itemDataSource = todoIte
	ms.dataSource;
               });            
        };

7. Markieren Sie in der App einen anderen Eintrag in der Liste, und klicken Sie dann auf die Schaltfläche **Aktualisieren**.

   	Der markierte Eintrag erscheint jetzt nicht mehr in der Liste. Jede Aktualisierung führt zu einem Roundtrip zum mobilen Dienst, der jetzt gefilterte Daten zurückgibt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Arbeit mit Daten in Mobile Services gezeigt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

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
[Website mit den Codebeispielen für Entwickler]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library/


<!--HONumber=42-->
