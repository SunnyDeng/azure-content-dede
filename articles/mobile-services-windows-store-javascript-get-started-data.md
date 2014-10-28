<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/19/2014" ms.author="glenga"></tags>

# Erste Schritte mit Daten in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy][mobile-services-selector-get-started-data-legacy]]

<div class="dev-center-tutorial-subselector">
    <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title=".NET-Back-End">.NET-Back-End</a> | 
    <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="JavaScript-Back-End" class="current">JavaScript-Back-End</a>
</div>

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Daten in einer Windows Store-App nutzen können. In diesem Lernprogramm laden Sie ein Visual Studio 2013-Projekt für eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

> [WACOM.NOTE]Dieses Thema veranschaulicht, wie Azure Mobile Services einem Windows Store-Projekt mithilfe von Visual Studio 2013 hinzugefügt werden. Sie können denselben mobilen JavaScript-Back-End-Dienst einem Projekt für eine universelle Windows-App hinzufügen. Weitere Informationen finden Sie in der Lernprogrammversion zu [universellen Windows-Apps][universellen Windows-Apps].

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Herunterladen des Windows Store-App-Projekts][Herunterladen des Windows Store-App-Projekts]
2.  [Erstellen des mobilen Dienstes][Erstellen des mobilen Dienstes]
3.  [Erstellen einer Datentabelle als Datenspeicher][Erstellen einer Datentabelle als Datenspeicher]
4.  [Aktualisieren der App zur Verwendung von Mobile Services][Aktualisieren der App zur Verwendung von Mobile Services]
5.  [Testen der App mit Mobile Services][Testen der App mit Mobile Services]

Für dieses Lernprogramm benötigen Sie Folgendes:

-   Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].
-   Visual Studio 2013, mit dem einfacher eine Verbindung zwischen Ihrer Windows Store-App und Mobile Services hergestellt werden kann. Um dasselbe grundlegende Verfahren mit Visual Studio 2012 auszuführen, befolgen Sie die Schritte im Thema [Erste Schritte mit Daten in Mobile Services mit Visual Studio 2012][Erste Schritte mit Daten in Mobile Services mit Visual Studio 2012].

## <a name="download-app"></a><span class="short-header">Herunterladen des Projekts</span>Herunterladen des GetStartedWithData-Projekts

Dieses Lernprogramm basiert auf der [GetStartedWithMobileServices-App][GetStartedWithMobileServices-App], bei der es sich um ein Windows Store-App-Projekt in Visual Studio 2013 handelt. Die Benutzeroberfläche dieser App und die durch den Mobile Services-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1.  Laden Sie die JavaScript-Version der GetStartedWithData-Beispiel-App von der Website mit den [Codebeispielen für Entwickler][GetStartedWithMobileServices-App] herunter.

2.  Öffnen Sie das heruntergeladene Projekt in Visual Studio 2012 Express für Windows 8, erweitern Sie den Ordner **js** und sehen Sie sich die Datei "default.js" an.

    Beachten Sie, dass die hinzugefügten **TodoItem**-Objekte in einem speicherinternen **List**-Objekt gespeichert werden.

3.  Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

4.  Geben Sie in der App in **Insert a TodoItem** Text ein, und klicken Sie dann auf **Save**.

    ![][]

    Der gespeicherte Text wird in der zweiten Spalte unter **Query and update data** angezeigt.

## <a name="create-service"></a>Erstellen eines neuen mobilen Diensts in Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013][mobile-services-create-new-service-vs2013]]

1.  Erweitern Sie die Ordner \*\*services\*\*, \*\*mobile services\*\*, \*\*\<your\_service\>\*\* im Projektmappen-Explorer, öffnen Sie die Skriptdatei service.js und beachten Sie die neue globale Variable, die wie folgt aussieht:

        var todolistClient = new WindowsAzure.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

    In diesem Code wird der Zugriff auf den neuen mobilen Dienst in Ihrer App über globale Variable bereitgestellt. Der Client wird durch Angabe des URI und des Anwendungsschlüssels des neuen mobilen Diensts erstellt. Da Sie einen Verweis auf das Skript zur Datei default.html hinzugefügt haben, ist diese Variable in allen Skriptdateien verfügbar, die von dieser Seite aus referenziert werden.

## <a name="add-table"></a>Hinzufügen einer neuen Tabelle für die Datenspeicherung

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013][mobile-services-create-new-table-vs2013]]

> [WACOM.NOTE]Neue Tabellen werden mit den Spalten "Id", "\_\_createdAt", "\_\_updatedAt" und "\_\_version" erstellt. Wenn das dynamische Schema aktiviert ist, generieren Mobile Services automatisch neue Spalten auf Grundlage des JSON-Objekts in der Einfüge- oder Aktualisierungsanforderung. Weitere Informationen finden Sie unter [Dynamisches Schema][Dynamisches Schema].

# <a name="update-app"></a>Aktualisieren der App zur Verwendung des mobilen Diensts

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app][mobile-services-windows-javascript-update-data-app]]

## <a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie wie zuvor Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

    Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3.  Klicken Sie im [Verwaltungsportal][Verwaltungsportal] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

    ![][1]

    Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

5.  Markieren Sie in der App einen Eintrag in der Liste, kehren Sie dann zurück zur Registerkarte "Durchsuchen" des Portals, und klicken Sie auf **Aktualisieren**.

    Beachten Sie, dass der Wert "complete" von **false** in **true** geändert wurde.

6.  Ersetzen Sie in der Projektdatei "default.js" die vorhandene **RefreshTodoItems**-Funktion durch den folgenden Code, mit dem abgeschlossene Einträge herausgefiltert werden:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7.  Markieren Sie in der App einen anderen Eintrag in der Liste, und klicken Sie dann auf die Schaltfläche **Refresh**.

    Der markierte Eintrag erscheint jetzt nicht mehr in der Liste. Jede Aktualisierung führt zu einem Roundtrip zum mobilen Dienst, der jetzt gefilterte Daten zurückgibt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Arbeit mit Daten in Mobile Services gezeigt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten][Prüfen und Ändern von Daten mit Skripten]
    Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging][Optimieren von Abfragen mittels Paging]
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie eines der folgenden Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz][Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]
    Lernen Sie mehr über die Verwendung von Mobile Services mit HTML und JavaScript.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-data-legacy]: ../includes/mobile-services-selector-get-started-data-legacy.md
  [.NET-Back-End]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ ".NET-Back-End"
  [JavaScript-Back-End]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/ "JavaScript-Back-End"
  [universellen Windows-Apps]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
  [Herunterladen des Windows Store-App-Projekts]: #download-app
  [Erstellen des mobilen Dienstes]: #create-service
  [Erstellen einer Datentabelle als Datenspeicher]: #add-table
  [Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
  [Testen der App mit Mobile Services]: #test-app
  [Kostenlose Azure-Testversion]: http://azure.microsoft.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F
  [Erste Schritte mit Daten in Mobile Services mit Visual Studio 2012]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data-vs2012
  [GetStartedWithMobileServices-App]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  []: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [Dynamisches Schema]: http://msdn.microsoft.com/de-de/library/windowsazure/jj193175.aspx
  [mobile-services-windows-javascript-update-data-app]: ../includes/mobile-services-windows-javascript-update-data-app.md
  [Verwaltungsportal]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
  [Prüfen und Ändern von Daten mit Skripten]: /de-de/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
  [Optimieren von Abfragen mittels Paging]: /de-de/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/
  [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library/
