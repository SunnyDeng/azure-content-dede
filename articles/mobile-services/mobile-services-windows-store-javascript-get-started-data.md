<properties 
	pageTitle="Hinzufügen von Mobile Services zu einer vorhandenen App (Windows Store JavaScript) | Mobile Dev Center" 
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
	ms.date="06/04/2015" 
	ms.author="glenga"/>


# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../../includes/mobile-services-selector-get-started-data-legacy.md)]

##Übersicht
In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer Windows Store-App nutzen können. In diesem Lernprogramm laden Sie ein Visual Studio 2013-Projekt für eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in die App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenloses Azure-Testkonto](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F).
* Visual Studio 2013, mit dem Sie Ihr Windows Store-App-Projekt mit Mobile Services verbinden.

##Download des GetStartedWithData-Projekts

Dieses Lernprogramm baut auf der [GetStartedWithMobileServices-App][Developer Code Samples site] auf. Dabei handelt es sich um ein Windows Store-App-Projekt in Visual Studio 2013. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1. Laden Sie die JavaScript-Version der GetStartedWithData-Beispiel-App von der Website mit den [Codebeispielen für Entwickler] herunter. 

2. Öffnen Sie das heruntergeladene Projekt in Visual Studio, erweitern Sie den Ordner **js**, und sehen Sie sich die Datei "default.js" an.

   	Beachten Sie, dass die hinzugefügten **TodoItem**-Objekte in einem speicherinternen **List**-Objekt gespeichert werden.

3. Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

4. Geben Sie in der App in **Insert a TodoItem** Text ein, und klicken Sie dann auf **Save**.

   	![][0]

   	Der gespeicherte Text wird in der zweiten Spalte unter **Query and update data** angezeigt.

##<a name="create-service"></a>Erstellen eines neuen mobilen Diensts in Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>Erweitern Sie die Ordner **services**, **mobile services**, **&lt;your_service>** im Projektmappen-Explorer, öffnen Sie die Skriptdatei service.js und beachten Sie die neue globale Variable, die wie folgt aussieht:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>In diesem Code wird der Zugriff auf den neuen mobilen Dienst in Ihrer App über globale Variable bereitgestellt. Der Client wird durch Angabe des URI und des Anwendungsschlüssels des neuen mobilen Diensts erstellt. Da Sie einen Verweis auf das Skript zur Datei default.html hinzugefügt haben, ist diese Variable in allen Skriptdateien verfügbar, die von dieser Seite aus referenziert werden.</p>
</li>
</ol>

##Hinzufügen einer neuen Tabelle für die Datenspeicherung

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../../includes/mobile-services-create-new-table-vs2013.md)]

##Aktualisieren der App zur Verwendung des mobilen Diensts

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../../includes/mobile-services-windows-javascript-update-data-app.md)]

##Testen der App mit dem neuen mobilen Dienst

1. Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2. Geben Sie wie zuvor Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3. Klicken Sie im [Verwaltungsportal] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4. Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.
  
   	Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

5. Markieren Sie in der App einen Eintrag in der Liste, kehren Sie dann zurück zur Registerkarte "Durchsuchen" des Portals, und klicken Sie auf **Aktualisieren**.

  	Beachten Sie, dass der Wert "complete" von **false** in **true** geändert wurde.

6. Ersetzen Sie in der Projektdatei "default.js" die vorhandene **RefreshTodoItems**-Funktion durch den folgenden Code, mit dem abgeschlossene Einträge herausgefiltert werden:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7. Markieren Sie in der App einen anderen Eintrag in der Liste, und klicken Sie dann auf die Schaltfläche **Refresh**.

   	Der markierte Eintrag erscheint jetzt nicht mehr in der Liste. Jede Aktualisierung führt zu einem Roundtrip zum mobilen Dienst, der jetzt gefilterte Daten zurückgibt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Arbeit mit Daten in Mobile Services gezeigt. Lesen Sie danach eines dieser anderen Themen:

* [Hinzufügen von Authentifizierung zur App](mobile-services-windows-store-javascript-get-started-users.md) <br/>Erfahren Sie, wie Sie Benutzer für Ihre App authentifizieren.

* [Hinzufügen von Pushbenachrichtigungen zur App](mobile-services-javascript-backend-windows-store-javascript-get-started-push.md) <br/>Erfahren Sie, wie Sie eine einfache Pushbenachrichtigung an Ihre App senden können.

* [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz](mobile-services-html-how-to-use-client-library.md) <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit HTML und JavaScript.

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Azure Management Portal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[Codebeispielen für Entwickler]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md
 

<!---HONumber=July15_HO3-->