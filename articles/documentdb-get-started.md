<properties 
	pageTitle="Erste Schritte mit dem DocumentDB .NET SDK | Azure" 
	description="Erfahren Sie, wie Sie ein Azure DocumentDB-Konto erstellen und konfigurieren, Datenbanken erstellen, Sammlungen erstellen und JSON-Dokumente in Ihrem NoSQL-Dokumentdatenbankkonto speichern." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="anhoh"/>

# Erste Schritte mit dem DocumentDB .NET SDK  

In diesem Leitfaden werden die ersten Schritte bei der Verwendung von [Microsoft Azure DocumentDB (Vorschau)](https://portal.azure.com/#gallery/Microsoft.DocumentDB) und des [DocumentDB .NET SDK](https://go.microsoft.com/fwlink/p/?linkid=402989) beschrieben. DocumentDB ist ein NoSQL-Dokumentdatenbankdienst, der über [eine Reihe von APIs und SDKs verfügt](https://go.microsoft.com/fwlink/p/?linkid=522476). Die Codebeispiele in diesem Artikel sind in C# geschrieben und verwenden das DocumentDB .NET SDK, das als NuGet-Paket verpackt und verteilt wird. 

In den Szenarien dieses Artikels wird das Erstellen und Konfigurieren eines DocumentDB-Kontos behandelt, das Anlegen von Datenbanken und Sammlungen sowie das Speichern von JSON-Dokumenten im Konto. Jedes dieser Beispiele ist Teil einer vollständigen Lösung, die auf [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) verfügbar ist. Sie können [die Lösung herunterladen](#GetSolution), um die Beispielcodes im Kontext anzuzeigen, oder Sie können einfach die Beispiele in diesem Artikel überprüfen.

## <a id="CreateAccount"></a>Erstellen eines DocumentDB-Kontos

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

## <a id="Connect"></a>Herstellen einer Verbindung mit einem DocumentDB-Konto

Wir beginnen mit dem Erstellen einer neuen Instanz von [DocumentClient](https://go.microsoft.com/fwlink/p/?linkid=522477), um eine Verbindung mit unserem DocumentDB-Konto herzustellen. Dafür brauchen wir folgende Referenzen in unserer C#-Anwendung:  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
Als Nächstes kann ein **DocumentClient** mithilfe des DocumentDB-Kontoendpunkts und entweder dem primären oder sekundären Zugriffsschlüssel instanziiert werden, der mit dem Konto verknüpft ist. Erstellen Sie den Client mithilfe von Code wie dem folgenden:   

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

> [AZURE.WARNING] Speichern Sie niemals Anmeldeinformationen im Quellcode. Die Anmeldeinformationen sind hier im Code dargestellt, um das Beispiel zu vereinfachen. Informationen zum Speichern von Anmeldeinformationen in einer Produktionsumgebung Sie unter [Azure-Websites: Funktionsweise von Anwendungs- und Verbindungszeichenfolgen](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). 

Die Werte für EndpointUrl und AuthorizationKey sind der URI und der PRIMÄRSCHLÜSSEL für Ihr DocumentDB-Konto und können im Bereich für Ihr DocumentDB-Konto des [Azure-Vorschauverwaltungsportals](https://portal.azure.com) abgerufen werden. 

![Screen shot of the Azure Preview portal, showing a DocumentDB account, with the ACTIVE hub highlighted, the KEYS button highlighted on the DocumentDB account blade, and the URI, PRIMARY KEY and SECONDARY KEY values highlighted on the Keys blade][1]
 
Diese Schlüssel gewähren den Administratorzugriffe auf Ihr DocumentDB-Konto und die darin enthaltenen Ressourcen. DocumentDB unterstützt auch die Verwendung von Ressourcenschlüsseln, mit denen Clients Ressourcen im DocumentDB-Konto entsprechend der von Ihnen eingeräumten Berechtigung lesen, schreiben und löschen können, ohne dass dafür ein Kontoschlüssel erforderlich ist. Weitere Informationen zu Ressourcenschlüsseln finden Sie im Artikel [Ressourcenmodell und Konzepte von DocumentDB](documentdb-resources.md) im Abschnitt "Berechtigungen".

Nachdem Sie nun wissen, wie Sie eine Verbindung mit einem DocumentDB-Konto herstellen und eine Instanz der Klasse **DocumentClient** erstellen, lassen Sie uns einen Blick auf die Arbeit mit DocumentDB-Ressourcen werfen.  

## <a id="CreateDB"></a>Erstellen einer Datenbank
Eine Datenbank kann mithilfe der [CreateDatabaseAsync](https://go.microsoft.com/fwlink/p/?linkid=522478)-Methode der **DocumentClient**-Klasse erstellt werden.  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

## <a id="CreateColl"></a>Erstellen einer Sammlung  

Eine Sammlung kann mithilfe der [CreateDocumentCollectionAsync](https://go.microsoft.com/fwlink/p/?linkid=522479)-Methode der  **DocumentClient**-Klasse erstellt werden.  Die im vorherigen Schritt erstellte Datenbank besitzt eine Reihe von Eigenschaften, darunter die [CollectionsLink](https://go.microsoft.com/fwlink/p/?linkid=522481)-Eigenschaft.  Mit dieser Information können wir jetzt eine Sammlung erstellen.  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
## <a id="CreateDoc"></a>Erstellen eines Dokuments	
Ein Dokument kann mithilfe der [CreateDocumentAsync](https://go.microsoft.com/fwlink/p/?linkid=522482)-Methode der **DocumentClient**-Klasse erstellt werden.  Die im vorherigen Schritt erstellte Sammlung besitzt eine Reihe von Eigenschaften, darunter die [DocumentsLink](https://go.microsoft.com/fwlink/p/?linkid=522483)-Eigenschaft.  Mit dieser Information können wir jetzt eines oder mehrere Dokumente einfügen.  Wir nehmen für dieses Beispiel an, dass wir eine Family-Klasse haben, die die Attribute einer Familie wie beispielsweise Name, Geschlecht und Alter beschreibt. Wenn Sie die Family-Beispielklasse anzeigen möchten, besuchen Sie unser [GitHub-Repository](https://github.com/Azure/azure-documentdb-net/blob/master/tutorials/get-started/src/Program.cs). 

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
 

## <a id="Query"></a>Abfragen von DocumentDB-Ressourcen

DocumentDB unterstützt umfassende Abfragen der in jeder Sammlung gespeicherten JSON-Dokumente.  Der folgende Beispielcode zeigt die verschiedenen Abfragen - sowohl mithilfe der SQL-Syntax als auch LINQ -, die wir nun mit den Dokumenten ausführen können, die wir im vorherigen Schritt eingefügt haben. 

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
	
## <a id="GetSolution"></a>Abrufen der vollständigen Lösung
Um die "GetStarted"-Lösung zu erstellen, die alle Beispiele dieses Artikels enthält, ist Folgendes erforderlich:

-   [DocumentDB-Konto][documentdb-create-account].
-   Die [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started)-Lösung auf GitHub 

Um die Verweise auf das DocumentDB .NET SDK in Visual Studio 2013 wiederherzustellen, klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf die "GetStarted"-Lösung, und klicken Sie dann auf die Option zum Aktivieren der NuGet-Paketwiederherstellung. Aktualisieren Sie dann in der Datei "App.config" die Werte "EndpointUrl" und "AuthorizationKey" wie unter [Herstellen einer Verbindung mit einem DocumentDB-Konto](#Connect) beschrieben. 

## <a id="NextSteps"></a>Nächste Schritte
-	Erfahren Sie, wie Sie ein [DocumentDB-Konto überwachen](https://go.microsoft.com/fwlink/p/?LinkId=402378).
-	Fragen Sie unser Beispieldataset im [Query Playground](http://www.documentdb.com/sql/demo) ab.
-	Weitere Informationen zum Programmiermodell finden Sie auf der [DocumentDB-Dokumentationsseite](../documentation/services/documentdb/) im Abschnitt "Entwickeln".


[Herstellen einer Verbindung mit einem DocumentDB-Konto]: #Connect
[Erstellen einer Datenbank]: #CreateDB
[Erstellen einer Sammlung]: #CreateColl
[Erstellen eines Dokuments]: #CreateDoc
[Abfragen von DocumentDB-Ressourcen]: #Query
[Nächste Schritte]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[1]: ../includes/media/documentdb-keys/keys.png


<!--HONumber=49-->