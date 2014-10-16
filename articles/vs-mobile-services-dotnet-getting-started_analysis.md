<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Was ist passiert?

###### Verweise wurden hinzugefügt

Das NuGet-Paket für Azure Mobile Services wurde Ihrem Projekt hinzugefügt. Die folgenden .NET-Verweise wurde Ihrem Projekt hinzugefügt: Microsoft.WindowsAzure.Mobile, Microsoft.WindowsAzure.Mobile.Ext, Newtonsoft.Json, System.Net.Http.Extensions, System.Net.Http.Primitives

###### Werte für die Verbindungszeichenfolge für Mobile Services

In Ihrer Datei App.xaml.cs wurde ein MobileServiceClient-Objekt mit der Anwendungs-URL des ausgewählten mobilen Diensts und seinem Anwendungsschlüssel erstellt.

### Erste Schritte mit Mobile Services

###### Abrufen des Verweises auf eine Tabelle

Der folgende Code ruft einen Verweis auf eine Tabelle ab, die Daten für ein TodoItem enthält. Sie können ihn in nachfolgenden Vorgängen zum Lesen und Aktualisieren der Datentabelle verwenden. Sie benötigen die Klasse TodoItem mit festgelegten Attributen, um das JSON zu interpretieren, das der mobile Dienst als Antwort auf Ihre Abfragen sendet.

	public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

	IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

Dieser Code funktioniert, wenn die Berechtigungen für Ihre Tabelle auf **Jeder mit dem Anwendungsschlüssel** festgelegt wurden. Wenn Sie die Berechtigungen ändern, um Ihren mobilen Dienst zu sichern, müssen Sie Unterstützung für Benutzerauthentifizierung hinzufügen. Weitere Informationen finden Sie unter [Erste Schritte mit Authentifizierung][Erste Schritte mit Authentifizierung].

###### Hinzufügen eines Eintrags

Fügen Sie ein neues Element in eine Datentabelle ein.

	TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
	await todoTable.InsertAsync(todoItem);

###### Lesen/Abfragen einer Tabelle

Mit dem folgenden Code werden alle Elemente einer Tabelle abgefragt. Beachten Sie, dass nur die erste Seite der Daten zurückgegeben wird. Standardmäßig sind dies 50 Elemente. Sie können die gewünschte Seitengröße übergeben, da es sich um einen optionalen Parameter handelt.

    List<TodoItem> items = await todoTable.ToListAsync();
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToCollectionAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }


###### Aktualisieren eines Eintrags

Aktualisieren Sie eine Zeile in einer Datentabelle. Das Parameterelement ist das TodoItem-Objekt, das aktualisiert werden soll.

    await todoTable.UpdateAsync(item);

###### Löschen eines Eintrags

Löschen Sie eine Zeile in der Datenbank. Das Parameterelement ist das **TodoItem**-Objekt, das gelöscht werden soll.

	await todoTable.UpdateAsync(item);

[Erste Schritte mit Authentifizierung]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/
