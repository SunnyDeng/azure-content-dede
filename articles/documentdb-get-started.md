<properties title="Erste Schritte mit einem DocumentDB-Konto" pageTitle="Erste Schritte mit einem DocumentDB-Konto | Azure" description="Hier erfahren Sie, wie Sie ein Azure DocumentDB-Konto erstellen und konfigurieren, Datenbanken erstellen, Sammlungen erstellen und JSON-Dokumenten im Konto speichern." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

# Erste Schritte mit einem DocumentDB-Konto

In diesem Leitfaden werden die ersten Schritte bei der Verwendung von **Azure DocumentDB (Vorschau)** beschrieben. Die Beispiele sind in C#-Code geschrieben und es wird das DocumentDB .NET SDK verwendet. In den Szenarien wird das Erstellen und Konfigurieren eines DocumentDB-Kontos behandelt, das Anlegen von Datenbanken und Sammlungen sowie das Speichern von JSON-Dokumenten im Konto. Weitere Informationen zum Verwenden von Azure DocumentDB finden Sie im Abschnitt „Nächste Schritte".

Damit Sie den ersten Schritten dieser Anleitung folgen können, benötigen Sie ein DocumentDB-Konto und den Zugriffsschlüssel (primär oder sekundär) des Kontos. Weitere Informationen finden Sie unter:

-   [Erstellen eines DocumentDB-Kontos][Erstellen eines DocumentDB-Kontos]

## Inhaltsverzeichnis

-   [Herstellen einer Verbindung mit einem DocumentDB-Konto][Herstellen einer Verbindung mit einem DocumentDB-Konto]
-   [Erstellen einer Datenbank][Erstellen einer Datenbank]
-   [Erstellen einer Sammlung][Erstellen einer Sammlung]
-   [Erstellen eines Dokuments][Erstellen eines Dokuments]
-   [Abfragen von DocumentDB-Ressourcen][Abfragen von DocumentDB-Ressourcen]
-   [Nächste Schritte][Nächste Schritte]

## <span id="Connect"></span></a>Herstellen einer Verbindung mit einem DocumentDB-Konto

Es gibt verschiedene SDKs und APIs, mit denen programmgesteuert mit DocumentDB gearbeitet werden kann. Die Beispiele unten sind in C#-Code dargestellt und es wird das DocumentDB .NET SDK verwendet.

Wir beginnen mit dem Erstellen eines DocumentClient, um eine Verbindung mit unserem DocumentDB-Konto herzustellen. Dafür brauchen wir folgende Referenzen in unserer C#-Anwendung:

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;

Ein DocumentClient kann mithilfe des DocumentDB-Kontoendpunkts und entweder dem primären oder sekundären Zugriffsschlüssel instanziiert werden, der mit dem Konto verknüpft ist.

DocumentDB-Kontoendpunkte und Schlüssel finden Sie im Azure-Verwaltungsportal (Vorschau) im Fenster für Ihr DocumentDB-Konto.

![][0]

> Beachten Sie, dass die auf der Blade „Schlüssel” verfügbaren DocumentDB-Zugriffsschlüssel auf Ihr DocumentDB-Konto zugreifen und die darin befindlichen Ressourcen. DocumentDB unterstützt auch die Verwendung von Ressourcenschlüsseln, mit denen Clients Ressourcen im DocumentDB-Konto entsprechend der von Ihnen eingeräumten Berechtigung lesen, schreiben und löschen können, ohne dass dafür ein Kontoschlüssel erforderlich ist.

Legen Sie den Client mithilfe von Code wie im folgenden Beispiel an.

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";

    // Create a new instance of the DocumentClient
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

**Warnung:** Speichern Sie niemals Anmeldeinformationen im Quellcode. Die Daten sind hier im Code dargestellt, um dieses Beispiel zu vereinfachen. Siehe [Microsoft Azure-Websites: Funktionsweise von Anwendungs- und Verbindungszeichenfolgen][Microsoft Azure-Websites: Funktionsweise von Anwendungs- und Verbindungszeichenfolgen] finden Sie Informationen zum Speichern von Anmeldeinformationen.

Nachdem Sie nun wissen, wie Sie eine Verbindung mit einem DocumentDB-Konto herstellen und eine Instanz von DocumentClient erstellen, lassen Sie uns einen Blick auf die Arbeit mit DocumentDB-Ressourcen werfen.

## <span id="CreateDB"></span></a>Erstellen einer Datenbank

Mithilfe des .NET SDK kann eine DocumentDB-Datenbank anhand der CreateDatabaseAsync-Methode eines DocumentClient erstellt werden.

    // Create a Database
    Database database = await client.CreateDatabaseAsync(
        new Database
            {
                Id = "FamilyRegistry"
            });

## <span id="CreateColl"></span></a>Erstellen einer Sammlung

Mithilfe des .NET SDK kann eine DocumentDB-Sammlung anhand der CreateDocumentCollectionAsync-Methode eines DocumentClient erstellt werden. Die im vorherigen Schritt erstellte Datenbank besitzt eine Reihe von Eigenschaften, darunter die CollectionsLink-Eigenschaft. Mit dieser Information können wir jetzt eine Sammlung erstellen.

    // Create a document collection
    DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
        new DocumentCollection
            {
                Id = "FamilyCollection"
            });

## <span id="CreateDoc"></span></a>Erstellen eines Dokuments

Mithilfe des .NET SDK kann ein DocumentDB-Dokument anhand der CreateDocumentAsync-Methode eines DocumentClient erstellt werden. Die im vorherigen Schritt erstellte Sammlung besitzt eine Reihe von Eigenschaften, darunter die DocumentsLink-Eigenschaft. Mit dieser Information können wir jetzt eines oder mehrere Dokumente einfügen. Wir nehmen für dieses Beispiel an, dass wir eine Family-Klasse haben, die die Attribute einer Familie wie beispielsweise Name, Geschlecht und Alter beschreibt.

    // Create the Andersen Family document
    Family andersenFamily = new Family
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

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, andersenFamily);

    // Create the WakeField Family document
    Family wakefieldFamily = new Family
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

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, wakefieldFamily);

## <span id="Query"></span></a>Abfragen von DocumentDB-Ressourcen

DocumentDB unterstützt umfassende Anfragen der in jeder Sammlung gespeicherten JSON-Dokumente. Der Beispielcode unten zeigt die verschiedenen Abfragen - sowohl mithilfe der SQL-Syntax als auch LINQ -, die wir nun mit den Dokumenten ausführen können, die wir im vorherigen Schritt eingefügt haben.

    // Query the documents using DocumentDB SQL for the Andersen family
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = \"AndersenFamily\"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family
    families =
        from f in client.CreateDocumentQuery(documentCollection.DocumentsLink)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family
    families = client.CreateDocumentQuery(documentCollection.DocumentsLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    // Query the documents using DocumentSQl with one join
    var items = client.CreateDocumentQuery<dynamic>(documentCollection.DocumentsLink,
        "SELECT f.id, c.FirstName AS child " +
        "FROM Families f " +
        "JOIN c IN f.Children");

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

    // Query the documents using LINQ with one join
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

Um das vollständige Einstiegsbeispiel zu erhalten, klicken Sie [hier][hier].

## <span id="NextSteps"></span></a>Nächste Schritte

-   Sie erfahren, wie Sie ein [DocumentDB-Konto überwachen][DocumentDB-Konto überwachen].
-   Informationen zum Programmierungsmodell finden Sie auf der [DocumentDB-Dokumentationsseite][DocumentDB-Dokumentationsseite] im Abschnitt „Entwickeln“.

  [Erstellen eines DocumentDB-Kontos]: ../documentdb-create-account/
  [Herstellen einer Verbindung mit einem DocumentDB-Konto]: #Connect
  [Erstellen einer Datenbank]: #CreateDB
  [Erstellen einer Sammlung]: #CreateColl
  [Erstellen eines Dokuments]: #CreateDoc
  [Abfragen von DocumentDB-Ressourcen]: #Query
  [Nächste Schritte]: #NextSteps
  [0]: ./media/documentdb-get-started/gs1.png
  [Microsoft Azure-Websites: Funktionsweise von Anwendungs- und Verbindungszeichenfolgen]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [hier]: https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started
  [DocumentDB-Konto überwachen]: http://go.microsoft.com/fwlink/p/?LinkId=402378
  [DocumentDB-Dokumentationsseite]: http://go.microsoft.com/fwlink/p/?LinkID=402319
