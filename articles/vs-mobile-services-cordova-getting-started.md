<properties title="Erste Schritte mit Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Erste Schritte][Erste Schritte]
> -   [Was ist passiert?][Was ist passiert?]

## Erste Schritte mit Mobile Services (Cordova-Projekte)

Der erste Schritt, der ausgeführt werden muss, um den Code in diesen Beispielen verwenden zu können, hängt davon ab, mit welchem Typ von mobilem Dienst Sie eine Verbindung herstellen.

Für einen mobilen JavaScript-Back-End-Dienst erstellen Sie eine Tabelle namens **TodoItem**. Wenn Sie eine Tabelle erstellen möchten, suchen Sie den mobilen Dienst unter dem Knoten **Azure** im Server-Explorer, klicken mit der rechten Maustaste auf den Knoten des mobilen Diensts, um das Kontextmenü zu öffnen, und wählen dann **Tabelle erstellen** aus. Geben Sie **TodoItem** als Tabellennamen ein.

Wenn Sie stattdessen einen mobilen .NET-Back-End-Dienst verwenden, ist bereits eine Tabelle **TodoItem** in der Standardprojektvorlage enthalten, die Visual Studio für Sie erstellt hat. Sie müssen diese jedoch noch in Azure veröffentlichen. Öffnen Sie im Projektmappen-Explorer das Kontextmenü für das Mobile Services-Projekt, und wählen Sie dann **Web veröffentlichen** aus. Übernehmen Sie die Standardwerte, und klicken Sie dann auf die Schaltfläche **Veröffentlichen**.

##### Abrufen des Verweises auf eine Tabelle

Der folgende Code ruft einen Verweis auf eine Tabelle ab, die Daten für ein **TodoItem** enthält. Sie können ihn in nachfolgenden Vorgängen zum Lesen und Aktualisieren der Datentabelle verwenden. Die **TodoItem**-Tabelle wird automatisch erstellt, wenn Sie einen mobilen Dienst erstellen.

    var todoTable = mobileServiceClient.getTable('TodoItem');

Damit diese Beispiele funktionieren, müssen die Berechtigungen für die Tabelle auf **Jeder mit dem Anwendungsschlüssel** festgelegt werden. Später können Sie Authentifizierung einrichten. Weitere Informationen finden Sie unter [Erste Schritte mit Authentifizierung][Erste Schritte mit Authentifizierung].

##### Hinzufügen eines Eintrags

Fügen Sie ein neues Element in eine Datentabelle ein. Eine ID (eine GUID vom Typ **string**) wird automatisch als primärer Schlüssel für die neue Zeile erstellt. Rufen Sie die Methode [done][done] für das zurückgegebene [Promise][done]-Objekt auf, um eine Kopie des eingefügten Objekts abzurufen und ggf. Fehler zu behandeln.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    todoTable.insert(todoItem).done(function (item) {
        items.push(item)
        });
    };

##### Lesen/Abfragen einer Tabelle

Der folgende Code fragt eine Tabelle sortiert nach dem **text**-Feld nach allen Elementen ab. Sie können Code zum Verarbeiten der Abfrageergebnisse im Erfolgshandler hinzufügen. In diesem Fall wird das lokale Array der Elemente aktualisiert.

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
            });
        });

Sie können die **where**-Methode zum Ändern der Abfrage verwenden. Das folgende Beispiel filtert abgeschlossene Elemente aus.

    todoTable.where(function () {
                 return (this.complete === false);
              })
             .read().done(function (results) {
                items = results.slice();
             });

Weitere Beispiele für Abfragen, die verwendet werden können, finden Sie unter dem Objekt [query][query].

##### Aktualisieren eines Eintrags

Aktualisieren Sie eine Zeile in einer Datentabelle. In diesem Code wird das Element aus der Liste entfernt, wenn der mobile Dienst antwortet. Rufen Sie die Methode [done][done] für das zurückgegebene [Promise][done]-Objekt auf, um eine Kopie des eingefügten Objekts abzurufen und ggf. Fehler zu behandeln.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

##### Löschen eines Eintrags

Löschen Sie eine Zeile aus einer Datentabelle mithilfe der Methode **del**. Rufen Sie die Methode [done][done] für das zurückgegebene [Promise][done]-Objekt auf, um eine Kopie des eingefügten Objekts abzurufen und ggf. Fehler zu behandeln.

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

[Weitere Informationen zu mobilen Diensten][Weitere Informationen zu mobilen Diensten]

  [Erste Schritte]: /documentation/articles/vs-mobile-services-cordova-getting-started/
  [Was ist passiert?]: /documentation/articles/vs-mobile-services-cordova-what-happened/
  [Erste Schritte mit Authentifizierung]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-html-get-started-users/
  [done]: 
  [query]: (http://msdn.microsoft.com/library/azure/jj613353.aspx)
  [Weitere Informationen zu mobilen Diensten]: http://azure.microsoft.com/documentation/services/mobile-services/
