<properties 
	pageTitle="Erste Schritte mit dem DocumentDB .NET SDK | Azure" 
	description="Erfahren Sie, wie Sie ein Azure DocumentDB-Konto erstellen und konfigurieren, Datenbanken erstellen, Sammlungen erstellen und JSON-Dokumente in Ihrem NoSQL-Dokumentdatenbankkonto speichern." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/12/2014" 
	ms.author="mimig"/>

#Erste Schritte mit dem DocumentDB .NET SDK  

In diesem Leitfaden werden die ersten Schritte bei der Verwendung von [**Microsoft Azure DocumentDB (Vorschau)**](https://portal.azure.com/#gallery/Microsoft.DocumentDB) und des DocumentDB .NET SDK beschrieben. DocumentDB ist ein NoSQL-Dokumentdatenbankdienst, der über eine Reihe von APIs und SDKs verfügt. Die Codebeispiele in diesem Artikel sind in C# geschrieben und verwenden das [DocumentDB .NET SDK](http://go.microsoft.com/fwlink/p/?linkid=402989), das als NuGet-Paket verpackt und verteilt wird. 

In den Szenarien dieses Artikels wird das Erstellen und Konfigurieren eines DocumentDB-Kontos behandelt, das Anlegen von Datenbanken und Sammlungen sowie das Speichern von JSON-Dokumenten im Konto. Jedes dieser Beispiele ist Teil einer vollständigen Lösung, die auf [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) verfügbar ist. Sie können die [Lösung herunterladen],(#GetSolution) um die Beispielcodes im Kontext anzuzeigen, oder Sie können einfach die Beispiele in diesem Artikel überprüfen.

##Inhaltsverzeichnis

-	[Herstellen einer Verbindung mit einem DocumentDB-Konto](#Connect)
-	[Erstellen einer Datenbank](#CreateDB)
-	[Erstellen einer Sammlung](#CreateColl)
-	[Erstellen eines Dokuments](#CreateDoc)
-	[Abfragen von DocumentDB-Ressourcen](#Query)
-	[Abrufen der vollständigen Lösung](#GetSolution)
-	[Nächste Schritte](#NextSteps)

##<a id="Connect"></a>Herstellen einer Verbindung mit einem DocumentDB-Konto

Wir beginnen mit dem Erstellen einer neuen Instanz von **DocumentClient**, um eine Verbindung mit unserem DocumentDB-Konto herzustellen.   Dafür brauchen wir folgende Referenzen in unserer C#-Anwendung:  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
Ein **DocumentClient** kann mithilfe des DocumentDB-Kontoendpunkts und entweder dem primären oder sekundären Zugriffsschlüssel instanziiert werden, der mit dem Konto verknüpft ist.  

DocumentDB-Kontoendpunkte und Schlüssel finden Sie im [Azure-Vorschauverwaltungsportal](https://portal.azure.com/#gallery/Microsoft.DocumentDB) im Fenster für Ihr DocumentDB-Konto. 

![][1]
 
>Beachten Sie, dass die im Fenster "Schlüssel" verfügbaren DocumentDB-Zugriffsschlüssel auf Ihr DocumentDB-Konto zugreifen und die darin befindlichen Ressourcen.  DocumentDB unterstützt auch die Verwendung von Ressourcenschlüsseln, mit denen Clients Ressourcen im DocumentDB-Konto entsprechend der von Ihnen eingeräumten Berechtigung lesen, schreiben und löschen können, ohne dass dafür ein Kontoschlüssel erforderlich ist.    

Legen Sie den Client mithilfe von Code wie im folgenden Beispiel an.  

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

**Warnung:** Speichern Sie niemals Anmeldeinformationen im Quellcode. Um diesem Beispiel zu folgen, werden die Anmeldeinformationen im Quellcode angezeigt. Weitere Informationen zum Speichern von Anmeldeinformationen finden Sie unter [Microsoft Azure-Websites: Funktionsweise von Anwendungs- und Verbindungszeichenfolgen](http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

Nachdem Sie nun wissen, wie Sie eine Verbindung mit einem DocumentDB-Konto herstellen und eine Instanz von **DocumentClient** erstellen, lassen Sie uns einen Blick auf die Arbeit mit DocumentDB-Ressourcen werfen.  

##<a id="CreateDB"></a>Erstellen einer Datenbank
Mithilfe des DocumentDB .NET SDK kann eine DocumentDB-Datenbank anhand der **CreateDatabaseAsync**-Methode eines "DocumentClient" erstellt werden.  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Erstellen einer Sammlung  

Mithilfe des DocumentDB .NET SDK kann eine DocumentDB-Sammlung anhand der **CreateDocumentCollectionAsync**-Methode eines "DocumentClient" erstellt werden.  Die im vorherigen Schritt erstellte Datenbank besitzt eine Reihe von Eigenschaften, darunter die **CollectionsLink**-Eigenschaft.  Mit dieser Information können wir jetzt eine Sammlung erstellen.  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
##<a id="CreateDoc"></a>Erstellen eines Dokuments	
Mithilfe des DocumentDB .NET SDK kann ein DocumentDB-Dokument anhand der **CreateDocumentAsync**-Methode eines "DocumentClient" erstellt werden.  Die im vorherigen Schritt erstellte Sammlung besitzt eine Reihe von Eigenschaften, darunter die **DocumentsLink**-Eigenschaft.  Mit dieser Information können wir jetzt eines oder mehrere Dokumente einfügen.  Wir nehmen für dieses Beispiel an, dass wir eine Family-Klasse haben, die die Attribute einer Familie wie beispielsweise Name, Geschlecht und Alter beschreibt.  

    // Create the Andersen family document.
	Family AndersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents =  new Parent[] {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay"}
        },
        Children = new Child[] {
            new Child { 
                FirstName = "Henriette Thaulow", 
                Gender = "female", 
                Grade = 5, 
                Pets = new Pet[] {
                    new Pet { GivenName = "Fluffy" } 
                }
            } 
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
    };

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, AndersenFamily);
    
    // Create the WakeField family document.
    Family WakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        Parents = new Parent[] {
            new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
            new Parent { FamilyName= "Miller", FirstName= "Ben" }
        },
        Children = new Child[] {
            new Child {
                FamilyName= "Merriam", 
                FirstName= "Jesse", 
                Gender= "female", 
                Grade= 8,
                Pets= new Pet[] {
                    new Pet { GivenName= "Goofy" },
                    new Pet { GivenName= "Shadow" }
                }
            },
            new Child {
                FamilyName= "Miller", 
                FirstName= "Lisa", 
                Gender= "female", 
                Grade= 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, WakefieldFamily);
 

##<a id="Query"></a>Abfragen von DocumentDB-Ressourcen
DocumentDB unterstützt umfassende Anfragen der in jeder Sammlung gespeicherten JSON-Dokumente.  Der folgende Beispielcode zeigt die verschiedenen Abfragen - sowohl mithilfe der SQL-Syntax als auch LINQ -, die wir nun mit den Dokumenten ausführen können, die wir im vorherigen Schritt eingefügt haben.  

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = \"AndersenFamily\"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery(documentCollection.DocumentsLink)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery(documentCollection.DocumentsLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    // Query the documents using DocumentSQL with one join.
    var items = client.CreateDocumentQuery<dynamic>(documentCollection.DocumentsLink,
        "SELECT f.id, c.FirstName AS child " +
        "FROM Families f " +
        "JOIN c IN f.Children");

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

    // Query the documents using LINQ with one join.
    items = client.CreateDocumentQuery<Family>(documentCollection.DocumentsLink)
        .SelectMany(family => family.Children
            .Select(children => new
            {
                family = family.Id,
                child = children.FirstName
            }));

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

##<a id="GetSolution"></a>Abrufen der vollständigen Lösung
Um die "GetStarted"-Lösung zu erstellen, die alle Beispiele dieses Artikels enthält, ist Folgendes erforderlich:

-   [DocumentDB-Konto][documentdb-create-account].
-   Die [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started)-Lösung auf GitHub. 

Um die Verweise auf das DocumentDB .NET SDK in Visual Studio 2013 wiederherzustellen, klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf die "GetStarted"-Lösung, und klicken Sie dann auf die Option zum Aktivieren der NuGet-Paketwiederherstellung, um die Verweise wiederherzustellen. 

##<a id="NextSteps"></a>Nächste Schritte
-	Erfahren Sie, wie Sie ein [DocumentDB-Konto überwachen](http://go.microsoft.com/fwlink/p/?LinkId=402378).
-	Informationen zum Programmierungsmodell finden Sie auf der [DocumentDB-Dokumentationsseite](http://go.microsoft.com/fwlink/p/?LinkID=402319) im Abschnitt "Entwickeln".


[Herstellen einer Verbindung mit einem DocumentDB-Konto]: #Connect
[Erstellen einer Datenbank]: #CreateDB
[Erstellen einer Sammlung]: #CreateColl
[Erstellen eines Dokuments]: #CreateDoc
[Abfragen von DocumentDB-Ressourcen]: #Query
[Nächste Schritte]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: ../documentdb-create-account/
[documentdb-manage]: ../documentdb-manage/

[1]: ./media/documentdb-get-started/gs1.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
