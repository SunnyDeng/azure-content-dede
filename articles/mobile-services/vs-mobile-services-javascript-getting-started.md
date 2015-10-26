<properties 
	pageTitle="Erste Schritte mit einer mobilen Javascript-App, nachdem Mobile Services mithilfe von Visual Studio Verbundene Dienste hinzugefügt wurde | Microsoft Azure" 
	description="Beschreibt die ersten Schritte mit Mobile Services in einem JavaScript-Projekt in Visual Studio." 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="09/17/2015" 
	ms.author="patshea"/>

# Erste Schritte mit einer mobilen Javascript-App, nachdem Azure Mobile Services mithilfe von Visual Studio Verbundene Dienste hinzugefügt wurde

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-javascript-getting-started.md)
> - [What Happened](vs-mobile-services-javascript-what-happened.md)

Der erste Schritt, der ausgeführt werden muss, um den Code in diesen Beispielen verwenden zu können, hängt davon ab, mit welchem Typ von mobilem Dienst Sie eine Verbindung herstellen.

 - Für einen mobilen JavaScript-Back-End-Dienst erstellen Sie eine Tabelle namens TodoItem. Wenn Sie eine Tabelle erstellen möchten, suchen Sie den mobilen Dienst unter dem Knoten "Azure" im Server-Explorer, klicken mit der rechten Maustaste auf den Knoten des mobilen Diensts, um das Kontextmenü zu öffnen, und wählen dann **Create Table** aus. Geben Sie TodoItem als Tabellennamen ein.

 - Wenn Sie stattdessen einen mobilen .NET-Back-End-Dienst verwenden, ist bereits eine Tabelle TodoItem in der Standardprojektvorlage enthalten, die Visual Studio für Sie erstellt hat. Sie müssen diese jedoch noch in Azure veröffentlichen. Öffnen Sie im Projektmappen-Explorer das Kontextmenü für das Mobile Services-Projekt, und wählen Sie dann **Web veröffentlichen** aus. Übernehmen Sie die Standardwerte, und klicken Sie dann auf die Schaltfläche **Veröffentlichen**.

##Abrufen eines Verweises auf eine Tabelle

Das client-Objekt wurde Ihrem Projekt bereits hinzugefügt. Sein Name ist der Name Ihres mobilen Diensts mit der Anfügung "Client". Der folgende Code ruft einen Verweis auf eine Tabelle ab, die Daten für ein TodoItem enthält. Sie können ihn in nachfolgenden Vorgängen zum Lesen und Aktualisieren der Datentabelle verwenden.

	var todoTable = yourMobileServiceClient.getTable('TodoItem');

##Hinzufügen eines Eintrags 

Fügen Sie ein neues Element in eine Datentabelle ein. Eine ID (eine GUID vom Typ string) wird automatisch als primärer Schlüssel für die neue Zeile erstellt. Ändern Sie den Typ der ID-Spalte nicht, weil er von der Infrastruktur des mobilen Diensts verwendet wird.

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

##Lesen/Abfragen einer Tabelle

Der folgende Code fragt alle Elemente einer Tabelle ab, aktualisiert eine lokale Auflistung und bindet das Ergebnis an das Oberflächenelement listItems.

        // This code refreshes the entries in the list view 
        // by querying the TodoItems table.
        todoTable.where()
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });

Sie können die where-Methode zum Ändern der Abfrage verwenden. Das folgende Beispiel filtert abgeschlossene Elemente aus.

    todoTable.where(function () {
        return (this.complete === false && this.createdAt !== null);
    })
    .read()
    .done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
    });

Weitere Beispiele für Abfragen, die verwendet werden können, finden Sie unter dem [Objekt "query"](http://msdn.microsoft.com/library/azure/jj613353.aspx).

##Aktualisieren eines Eintrags

Aktualisieren Sie eine Zeile in einer Datentabelle. In diesem Beispiel ist *todoItem* das aktualisierte Element, und *item* ist dasselbe Element, das vom mobilen Dienst zurückgegeben wird. Wenn der mobile Dienst antwortet, wird das Element in der lokalen [todoItems](http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx)-Liste mithilfe der Methode splice aktualisiert. Rufen Sie die Methode **done** für das zurückgegebene [Promise](https://msdn.microsoft.com/library/dn802826.aspx)-Objekt auf, um eine Kopie des eingefügten Objekts abzurufen und ggf. Fehler zu behandeln.

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

#####Löschen eines Eintrags

Löschen Sie eine Zeile in einer Datentabelle. Rufen Sie die Methode [done]() für das zurückgegebene [Promise](https://msdn.microsoft.com/library/dn802826.aspx)-Objekt auf, um eine Kopie des eingefügten Objekts abzurufen und ggf. Fehler zu behandeln.

	todoTable.del(todoItem).done(function (item) {
	    todoItems.splice(todoItems.indexOf(item), 1);
    }



[Weitere Informationen zu mobilen Diensten](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=Oct15_HO3-->