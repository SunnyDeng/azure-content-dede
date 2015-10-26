<properties 
	pageTitle="Erste Schritte mit einem Cordova-Projekt für Mobile Services (Visual Studio Verbundene Dienste)" 
	description="Beschreibt die ersten Schritte, die Sie ausführen können, nachdem Sie Ihr Cordova-Projekt mithilfe von Visual Studio Verbundene Dienste mit Azure Mobile Services verbunden haben." 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/17/2015" 
	ms.author="patshea"/>

# Erste Schritte mit Mobile Services (Cordova-Projekte)

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-cordova-getting-started.md)
> - [What Happened](vs-mobile-services-cordova-what-happened.md)

##Erste Schritte
Der erste Schritt, der ausgeführt werden muss, um den Code in diesen Beispielen verwenden zu können, hängt davon ab, mit welchem Typ von mobilem Dienst Sie eine Verbindung herstellen.

- Für einen mobilen JavaScript-Back-End-Dienst erstellen Sie eine Tabelle namens TodoItem. Wenn Sie eine Tabelle erstellen möchten, suchen Sie den mobilen Dienst unter dem Knoten "Azure" im Server-Explorer, klicken mit der rechten Maustaste auf den Knoten des mobilen Diensts, um das Kontextmenü zu öffnen, und wählen dann **Create Table** aus. Geben Sie "TodoItem" als Tabellennamen ein.

- Wenn Sie einen mobilen .NET-Back-End-Dienst verwenden, ist bereits eine Tabelle "TodoItem" in der Standardprojektvorlage enthalten, die Visual Studio für Sie erstellt hat. Sie müssen diese jedoch noch in Azure veröffentlichen. Öffnen Sie im Projektmappen-Explorer das Kontextmenü für das Mobile Services-Projekt, und wählen Sie dann **Web veröffentlichen** aus. Übernehmen Sie die Standardwerte, und klicken Sie dann auf die Schaltfläche **Veröffentlichen**.



##Erstellen eines Verweises auf eine Tabelle

Der folgende Code ruft einen Verweis auf eine Tabelle ab, die Daten für ein TodoItem enthält. Sie können ihn in nachfolgenden Vorgängen zum Lesen und Aktualisieren der Datentabelle verwenden. Die TodoItem-Tabelle wird automatisch erstellt, wenn Sie einen mobilen Dienst erstellen.

    var todoTable = mobileServiceClient.getTable('TodoItem');

Damit diese Beispiele funktionieren, müssen die Berechtigungen für die Tabelle auf **Jeder mit dem Anwendungsschlüssel** festgelegt werden. Später können Sie Authentifizierung einrichten. Weitere Informationen finden Sie unter [Erste Schritte mit Authentifizierung](mobile-services-html-get-started-users.md).

##Hinzufügen eines Elements zu einer Tabelle

Fügen Sie ein neues Element in eine Datentabelle ein. Eine ID (eine GUID vom Typ string) wird automatisch als primärer Schlüssel für die neue Zeile erstellt. Rufen Sie die Methode **done** für das zurückgegebene [Promise](https://msdn.microsoft.com/library/dn802826.aspx)-Objekt auf, um eine Kopie des eingefügten Objekts abzurufen und ggf. Fehler zu behandeln.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }
    
    var items = new Array();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            items.push(item)
        });
    };

##Lesen oder Abfragen einer Tabelle

Der folgende Code fragt eine Tabelle sortiert nach dem text-Feld nach allen Elementen ab. Sie können Code zum Verarbeiten der Abfrageergebnisse im Erfolgshandler hinzufügen. In diesem Fall wird das lokale Array der Elemente aktualisiert.

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
        });

Sie können die where-Methode zum Ändern der Abfrage verwenden. Das folgende Beispiel filtert abgeschlossene Elemente aus.

    todoTable.where(function () {
            return (this.complete === false);
        })
        .read().done(function (results) {
            items = results.slice();
        });

Weitere Beispiele für Abfragen, die verwendet werden können, finden Sie unter dem Objekt [query](http://msdn.microsoft.com/library/azure/jj613353.aspx).

##Aktualisieren eines Tabellenelements

Aktualisieren Sie eine Zeile in einer Datentabelle. In diesem Code wird das Element aus der Liste entfernt, wenn der mobile Dienst antwortet. Rufen Sie die Methode **done** für das zurückgegebene [Promise](https://msdn.microsoft.com/library/dn802826.aspx)-Objekt auf, um eine Kopie des eingefügten Objekts abzurufen und ggf. Fehler zu behandeln.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

##Löschen eines Tabellenelements

Löschen Sie eine Zeile aus einer Datentabelle mithilfe der Methode **del**. Rufen Sie die Methode **done** für das zurückgegebene [Promise](https://msdn.microsoft.com/library/dn802826.aspx)-Objekt auf, um eine Kopie des eingefügten Objekts abzurufen und ggf. Fehler zu behandeln.

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

[Weitere Informationen zu mobilen Diensten](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=Oct15_HO3-->