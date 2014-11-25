<properties title="Erste Schritte mit Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Erste Schritte][Erste Schritte]
> -   [Was ist passiert?][Was ist passiert?]

## Erste Schritte mit Mobile Services

Der erste Schritt, der ausgeführt werden muss, um den Code in diesen Beispielen verwenden zu können, hängt davon ab, mit welchem Typ von mobilem Dienst Sie eine Verbindung herstellen.

Für einen mobilen JavaScript-Back-End-Dienst erstellen Sie eine Tabelle namens **TodoItem**. Wenn Sie eine Tabelle erstellen möchten, suchen Sie den mobilen Dienst unter dem Knoten **Azure** im Server-Explorer, klicken mit der rechten Maustaste auf den Knoten des mobilen Diensts, um das Kontextmenü zu öffnen, und wählen dann **Tabelle erstellen** aus. Geben Sie **TodoItem** als Tabellennamen ein.

Wenn Sie stattdessen einen mobilen .NET-Back-End-Dienst verwenden, ist bereits eine Tabelle **TodoItem** in der Standardprojektvorlage enthalten, die Visual Studio für Sie erstellt hat. Sie müssen diese jedoch noch in Azure veröffentlichen. Öffnen Sie im Projektmappen-Explorer das Kontextmenü für das Mobile Services-Projekt, und wählen Sie dann **Web veröffentlichen** aus. Übernehmen Sie die Standardwerte, und klicken Sie dann auf die Schaltfläche **Veröffentlichen**.

##### Abrufen des Verweises auf eine Tabelle

Das **client**-Objekt wurde Ihrem Projekt bereits hinzugefügt. Sein Name ist der Name Ihres mobilen Diensts mit der Anfügung "Client". Der folgende Code ruft einen Verweis auf eine Tabelle ab, die Daten für ein **TodoItem** enthält. Sie können ihn in nachfolgenden Vorgängen zum Lesen und Aktualisieren der Datentabelle verwenden.

    var todoTable = yourMobileServiceClient.getTable('TodoItem');

##### Hinzufügen eines Eintrags

Fügen Sie ein neues Element in eine Datentabelle ein. Eine ID (eine GUID vom Typ **string**) wird automatisch als primärer Schlüssel für die neue Zeile erstellt. Ändern Sie den Typ der ID-Spalte nicht, weil er von der Infrastruktur des mobilen Diensts verwendet wird.

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

##### Lesen/Abfragen einer Tabelle

Der folgende Code fragt alle Elemente einer Tabelle ab, aktualisiert eine lokale Auflistung und bindet das Ergebnis an das Oberflächenelement **listItems**.

        // This code refreshes the entries in the list view 
        // by querying the TodoItems table.
        todoTable.where()
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });

Sie können die **where**-Methode zum Ändern der Abfrage verwenden. Das folgende Beispiel filtert abgeschlossene Elemente aus.

    todoTable.where(function () {
        return (this.complete === false && this.createdAt !== null);
    })
    .read()
    .done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
    });

Weitere Beispiele für Abfragen, die verwendet werden können, finden Sie unter dem [Objekt "query"][Objekt "query"].

##### Aktualisieren eines Eintrags

Aktualisieren Sie eine Zeile in einer Datentabelle. In diesem Beispiel ist **todoItem** das aktualisierte Element, und **item** ist das Element, das vom mobilen Dienst zurückgegeben wird. Wenn der mobile Dienst antwortet, wird das Element in der lokalen **todoItems**-Liste mithilfe der Methode [splice][splice] aktualisiert. Rufen Sie die Methode [done][done] für das zurückgegebene [Promise][done]-Objekt auf, um eine Kopie des eingefügten Objekts abzurufen und ggf. Fehler zu behandeln.

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

##### Löschen eines Eintrags

Löschen Sie eine Zeile in einer Datentabelle. Rufen Sie die Methode [done][done] für das zurückgegebene [Promise][done]-Objekt auf, um eine Kopie des eingefügten Objekts abzurufen und ggf. Fehler zu behandeln.

    todoTable.delete(todoItem).done(function (item) {
        todoItems.splice(todoItems.indexOf(item), 1);
    }

[Weitere Informationen zu mobilen Diensten][Weitere Informationen zu mobilen Diensten]

  [Erste Schritte]: /documentation/articles/vs-mobile-services-javascript-getting-started/
  [Was ist passiert?]: /documentation/articles/vs-mobile-services-javascript-what-happened/
  [Objekt "query"]: http://msdn.microsoft.com/library/azure/jj613353.aspx
  [splice]: http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx
  [done]: 
  [Weitere Informationen zu mobilen Diensten]: http://azure.microsoft.com/documentation/services/mobile-services/
