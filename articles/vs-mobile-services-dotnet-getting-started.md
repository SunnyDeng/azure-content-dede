<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Erste Schritte](/documentation/articles/vs-mobile-services-dotnet-getting-started/)
> - [Was ist passiert?](/documentation/articles/vs-mobile-services-dotnet-what-happened/)

## Erste Schritte mit Mobile Services (.NET-Projekte)

Der erste Schritt, der ausgeführt werden muss, um den Code in diesen Beispielen verwenden zu können, hängt davon ab, mit welchem Typ von mobilem Dienst Sie eine Verbindung herstellen.

Für einen mobilen JavaScript-Back-End-Dienst erstellen Sie eine Tabelle namens "TodoItem".  Wenn Sie eine Tabelle erstellen möchten, suchen Sie den mobilen Dienst unter dem Azure-Knoten im Server-Explorer, klicken mit der rechten Maustaste auf den Knoten des mobilen Diensts, um das Kontextmenü zu öffnen, und wählen dann **Tabelle erstellen** aus. Geben Sie "TodoItem" als Tabellennamen ein.

Wenn Sie stattdessen einen mobilen .NET-Back-End-Dienst verwenden, ist bereits eine Tabelle TodoItem in der Standardprojektvorlage enthalten, die Visual Studio für Sie erstellt hat. Sie müssen diese jedoch noch in Azure veröffentlichen. Öffnen Sie im Projektmappen-Explorer das Kontextmenü für das Mobile Services-Projekt, und wählen Sie dann **Web veröffentlichen** aus. Übernehmen Sie die Standardwerte, und klicken Sie dann auf die Schaltfläche **Veröffentlichen**.

#####Abrufen des Verweises auf eine Tabelle

Der folgende Code ruft einen Verweis auf eine Tabelle ab, die Daten für ein TodoItem enthält. Sie können ihn in nachfolgenden Vorgängen zum Lesen und Aktualisieren der Datentabelle verwenden. Sie benötigen die Klasse "TodoItem" mit festgelegten Attributen, um das JSON zu interpretieren, das der mobile Dienst als Antwort auf Ihre Abfragen sendet.

	public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

	IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

Dieser Code funktioniert, wenn die Berechtigungen für Ihre Tabelle auf **Jeder mit dem Anwendungsschlüssel** festgelegt wurden. Wenn Sie die Berechtigungen ändern, um Ihren mobilen Dienst zu sichern, müssen Sie Unterstützung für Benutzerauthentifizierung hinzufügen. Weitere Informationen finden Sie unter [Erste Schritte mit Authentifizierung](http://azure.microsoft.com/de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/).

#####Hinzufügen eines Eintrags 

Fügen Sie ein neues Element in eine Datentabelle ein.

	TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
	await todoTable.InsertAsync(todoItem);

#####Lesen/Abfragen einer Tabelle 

Mit dem folgenden Code werden alle Elemente einer Tabelle abgefragt. Beachten Sie, dass nur die erste Seite der Daten zurückgegeben wird. Standardmäßig sind dies 50 Elemente. Sie können die gewünschte Seitengröße übergeben, da es sich um einen optionalen Parameter handelt.

    List<TodoItem> items;
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToListAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }


#####Aktualisieren eines Eintrags

Aktualisieren Sie eine Zeile in einer Datentabelle. Das Parameterelement ist das TodoItem-Objekt, das aktualisiert werden soll.

	await todoTable.UpdateAsync(item);

#####Löschen eines Eintrags

Löschen Sie eine Zeile in der Datenbank. Das Parameterelement ist das TodoItem-Objekt, das gelöscht werden soll.

	await todoTable.DeleteAsync(item);


[Weitere Informationen zu mobilen Diensten](http://azure.microsoft.com/documentation/services/mobile-services/)



<!--HONumber=42-->
