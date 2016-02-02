<properties
	pageTitle="NoSQL-Tutorial: DocumentDB .NET SDK | Microsoft Azure"
	description="Ein NoSQL-Tutorial, das eine Onlinedatenbank und eine C#-Konsolenanwendung mit dem DocumentDB .NET SDK erstellt. DocumentDB ist eine NoSQL-Datenbank für JSON."
	keywords="nosql tutorial, online database, c# console application"
	services="documentdb"
	documentationCenter=".net"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article" 
	ms.date="11/18/2015"
	ms.author="anhoh"/>

# NoSQL-Tutorial: Erstellen einer DocumentDB-C#-Konsolenanwendung 

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Willkommen beim NoSQL-Tutorial für das DocumentDB .NET SDK! Im Rahmen dieses Lernprogramms erstellen Sie eine Konsolenanwendung, mit der DocumentDB-Ressourcen erstellt und abgefragt werden können.

Folgende Themen werden behandelt:

- Erstellen eines DocumentDB-Kontos und Verbindungsaufbau
- Konfigurieren Ihrer Visual Studio-Projektmappe
- Erstellen einer Onlinedatenbank
- Erstellen einer Sammlung
- Erstellen von JSON-Dokumenten
- Abfragen der Sammlung
- Löschen der Datenbank

Sie haben nicht genügend Zeit? Keine Sorge! Die vollständige Lösung ist auf [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) verfügbar. In [Abrufen der vollständigen Lösung](#GetSolution) finden Sie eine Kurzanleitung.

Bitte verwenden Sie nach Abschluss des Lernprogramms die Abstimmungsschaltflächen am Anfang oder Ende dieser Seite, um uns Ihre Meinung mitzuteilen. Wenn wir mit Ihnen Kontakt aufnehmen sollen, können Sie Ihre E-Mail-Adresse im Kommentar hinterlassen.

Lassen Sie uns anfangen.

## Voraussetzungen

Stellen Sie sicher, dass Sie über Folgendes verfügen:

- Ein aktives Azure-Konto. Wenn Sie keines besitzen, können Sie sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/) registrieren.
- [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com/).

## Schritt 1: Erstellen eines DocumentDB-Kontos

In diesem Schritt erstellen Sie ein DocumentDB-Konto. Wenn Sie bereits über ein Konto verfügen, das Sie verwenden möchten, können Sie diesen Schritt überspringen und mit [Einrichten der Visual Studio-Projektmappe](#SetupVS) fortfahren.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Schritt 2: Einrichten Ihrer Visual Studio-Projektmappe

1. Öffnen Sie **Visual Studio** auf Ihrem Computer.
2. Wählen Sie im Menü **Datei** die Option **Neu** und anschließend **Projekt** aus.
3. Wählen Sie im Dialogfeld **Neues Projekt** die Option **Vorlagen**/**Visual C#**/**Konsolenanwendung** aus, geben Sie Ihrem Projekt einen Namen, und klicken Sie dann auf **OK**.
4. Im **Projektmappen-Explorer** klicken Sie mit der rechten Maustaste auf die neue Konsolenanwendung, die sich unter Ihrer Visual Studio-Projektmappe befindet.
5. Klicken Sie im gleichen Menü auf **NuGet-Pakete verwalten...**.
6. Klicken Sie im Bereich ganz links des Fensters **NuGet-Pakete verwalten** auf **Online** / **nuget.org**.
7. Suchen Sie im Eingabefeld **Onlinesuche** nach **DocumentDB-Clientbibliothek**.
8. Suchen Sie in den Ergebnissen nach **Microsoft Azure DocumentDB-Clientbibliothek** und klicken Sie auf **Installieren**. Die Paket-ID für die DocumentDB-Clientbibliothek lautet [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).

Prima. Damit ist die Einrichtung abgeschlossen und wir können mit dem Schreiben von Code beginnen.

##<a id="Connect"></a> Schritt 3: Herstellen einer Verbindung mit einem DocumentDB-Konto

Fügen Sie zunächst in der Datei "Program.cs" am Anfang der C#-Anwendung folgende Verweise hinzu:

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT] Um dieses NoSQL-Tutorial abzuschließen, müssen Sie die oben genannten Abhängigkeiten hinzufügen.

Speichern Sie anschließend den DocumentDB-Kontoendpunkt und entweder den primären oder den sekundären Zugriffsschlüssel. Die Schlüssel finden Sie im [Azure-Portal](https://portal.azure.com).

![Screenshot des Azure-Portals, das vom NoSQL-Tutorial zum Erstellen einer C#-Konsolenanwendung verwendet wird. Zeigt ein DocumentDB-Konto, bei dem der ACTIVE-Hub, die Schaltfläche „SCHLÜSSEL“ auf dem Blatt „DocumentDB-Konto“ sowie auf dem Blatt „Schlüssel“ die Werte URI, PRIMÄRSCHLÜSSEL und SEKUNDÄRSCHLÜSSEL hervorgehoben sind.][keys]

    private const string EndpointUrl = "<your endpoint URI>";
    private const string AuthorizationKey = "<your key>";

Zu Beginn erstellen wir für die Demoanwendung eine neue Instanz von **DocumentClient**. Erstellen Sie eine neue asynchrone Aufgabe mit dem Namen **GetStartedDemo**, und erstellen Sie dann eine neue Instanz von **DocumentClient**.

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

Rufen Sie die asynchrone Aufgabe in der **Main**-Methode auf, wie im folgenden Codebeispiel gezeigt.

	public static void Main(string[] args)
    {
		try
    	{
        	GetStartedDemo().Wait();
		}
		catch (Exception e)
		{
			Exception baseException = e.GetBaseException();
			Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
		}
	}

> [AZURE.WARNING] Speichern Sie niemals Anmeldeinformationen im Quellcode. Die Anmeldeinformationen sind hier im Code dargestellt, um das Beispiel zu vereinfachen. Siehe [Azure-Websites: Funktionsweise von Anwendungs- und Verbindungszeichenfolgen](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) für Informationen zum Speichern von Anmeldeinformationen in einer Produktionsumgebung. Sehen Sie sich unsere Beispielanwendung auf [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs) an, als Beispiel für das Speichern von Anmeldeinformationen außerhalb des Quellcodes.

Nachdem Sie nun wissen, wie Sie eine Verbindung mit einem DocumentDB-Konto herstellen und eine Instanz der Klasse **DocumentClient** erstellen, lassen Sie uns einen Blick auf die Arbeit mit DocumentDB-Ressourcen werfen.

## Schritt 4: Erstellen einer Onlinedatenbank
Ihre DocumentDB-[Datenbank](documentdb-resources.md#databases) kann mithilfe der Methode [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) der **DocumentClient**-Klasse erstellt werden. Eine Datenbank ist ein logischer Container für JSON-Dokumentspeicher, der auf Sammlungen aufgeteilt ist. Erstellen Sie die neue Datenbank in Ihrer **GetStartedDemo**-Methode, nachdem Sie den **DocumentClient** erstellt haben.

	// Check to verify a database with the id=FamilyRegistry does not exist
	Database database = client.CreateDatabaseQuery().Where(db => db.Id == "FamilyRegistry").AsEnumerable().FirstOrDefault();

	// If the database does not exist, create a new database
    if (database == null)
    {
    	database = await client.CreateDatabaseAsync(
    		new Database
            {
            	Id = "FamilyRegistry"
            });

		// Write the new database's id to the console
		Console.WriteLine(database.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateColl"></a>Schritt 5: Erstellen einer Sammlung  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** erstellt eine neue S1-Sammlung, die hat Auswirkungen auf die Preise hat. Weitere Informationen finden Sie auf unserer [Preisseite](https://azure.microsoft.com/pricing/details/documentdb/).

Eine [Sammlung](documentdb-resources.md#collections) kann mithilfe der [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx)-Methode der **DocumentClient**-Klasse erstellt werden. Eine Sammlung ist ein Container für JSON-Dokumente und die zugehörige JavaScript-Anwendungslogik. Die neu erstellte Sammlung wird einer [S1-Leistungsebene](documentdb-performance-levels.md) zugeordnet. Erstellen Sie nach dem Erstellen der Datenbank in der **GetStartedDemo**-Methode eine neue Sammlung mit dem Namen **FamilyCollection**.

    // Check to verify a document collection with the id=FamilyCollection does not exist
    DocumentCollection documentCollection = client.CreateDocumentCollectionQuery("dbs/" + database.Id).Where(c => c.Id == "FamilyCollection").AsEnumerable().FirstOrDefault();

	// If the document collection does not exist, create a new collection
    if (documentCollection == null)
    {
    	documentCollection = await client.CreateDocumentCollectionAsync("dbs/" + database.Id,
        	new DocumentCollection
            {
            	Id = "FamilyCollection"
            });

		// Write the new collection's id to the console
		Console.WriteLine(documentCollection.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateDoc"></a>Schritt 6: Erstellen von JSON-Dokumenten
Ein [Dokument](documentdb-resources.md#documents) kann mithilfe der [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx)-Methode der **DocumentClient**-Klasse erstellt werden. Dokumente sind benutzerdefinierter (beliebiger) JSON-Inhalt. Wir können jetzt eines oder mehrere Dokumente einfügen. Wenn Sie bereits über Daten verfügen, die Sie in der Datenbank speichern möchten, können Sie das [Datenmigrationstool](documentdb-import-data.md) von DocumentDB verwenden.

Zunächst müssen wir eine **übergeordnete**, **untergeordnete**, **Pet-**, **Adress-** und **Familien-**Klasse erstellen. Erstellen Sie diese Klassen, indem Sie die folgenden internen Unterklassen nach der **GetStartedDemo**-Methode hinzufügen.

    internal sealed class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    internal sealed class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    internal sealed class Pet
    {
        public string GivenName { get; set; }
    }

    internal sealed class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

    internal sealed class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
    }

Als Nächstes erstellen Sie Ihre Dokumente innerhalb Ihrer asynchronen **GetStartedDemo**-Methode.

    // Check to verify a document with the id=AndersenFamily does not exist
    Document document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "AndersenFamily").AsEnumerable().FirstOrDefault();

	// If the document does not exist, create a new document
	if (document == null)
	{
	    // Create the Andersen Family document
	    Family andersonFamily = new Family
	    {
	        Id = "AndersenFamily",
	        LastName = "Andersen",
	        Parents = new Parent[] {
	            new Parent { FirstName = "Thomas" },
	            new Parent { FirstName = "Mary Kay"}
	        },
	        Children = new Child[] {
	            new Child
	            { 
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
	
	    // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
	    await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, andersonFamily);
	}

    // Check to verify a document with the id=AndersenFamily does not exist
    document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "WakefieldFamily").AsEnumerable().FirstOrDefault();

    if (document == null)
    {
        // Create the WakeField document
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

        // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
        await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, wakefieldFamily);
	}

Sie haben jetzt die folgende Datenbank, Sammlung und die folgenden Dokumente in Ihrem DocumentDB-Konto erstellt.

![Diagramm zur hierarchischen Beziehung zwischen dem Konto, der Onlinedatenbank, der Sammlung und den Dokumenten, die vom NoSQL-Tutorial zum Erstellen einer C#-Konsolenanwendung verwendet werden.](./media/documentdb-get-started/nosql-tutorial-account-database.png)

##<a id="Query"></a>Schritt 7: Abfragen von DocumentDB-Ressourcen

DocumentDB unterstützt umfassende [Abfragen](documentdb-sql-query.md) der in jeder Sammlung gespeicherten JSON-Dokumente. Der folgende Beispielcode zeigt die verschiedenen Abfragen - sowohl mithilfe der SQL-Syntax als auch LINQ -, die wir nun mit den Dokumenten ausführen können, die wir im vorherigen Schritt eingefügt haben. Fügen Sie diese Abfragen Ihrer asynchronen **GetStartedDemo**-Methode hinzu.

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = "AndersenFamily"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

Das folgende Diagramm veranschaulicht, wie die DocumentDB-SQL-Abfragesyntax gegen die erstellte Sammlung aufgerufen wird. Die gleiche Logik wird auf die LINQ-Abfrage angewendet.

![Diagramm zur Veranschaulichung des Bereichs und der Bedeutung der Abfrage, die vom NoSQL-Tutorial zum Erstellen einer C#-Konsolenanwendung verwendet wird.](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

Das [FROM](documentdb-sql-query.md#from-clause) -Schlüsselwort ist in der Abfrage optional, da DocumentDB Abfragen auf eine Sammlung begrenzt. Aus diesem Grund ist "FROM Familien f" austauschbar mit "FROM Stamm r" oder einem anderen variablen Namen, den Sie auswählen. DocumentDB wird ableiten, dass Familien, Stamm oder der variable Name, den Sie ausgewählt haben, standardmäßig auf die aktuelle Sammlung verweisen.

##<a id="DeleteDatabase"></a>Schritt 8: Löschen der Onlinedatenbank

Das Löschen der erstellten Datenbank entfernt die Datenbank und alle untergeordneten Ressourcen (Sammlungen, Dokumente usw.). Sie können die Datenbank und den Dokument-Client löschen, indem Sie folgenden Codeausschnitt an das Ende der asynchronen **GetStartedDemo**-Methode hängen.

    // Clean up/delete the database
    await client.DeleteDatabaseAsync("dbs/" + database.Id);
	client.Dispose();

##<a id="Run"></a>Schritt 9: Ausführen der C#-Konsolenanwendung

Sie können die Anwendung nun ausführen. Am Ende der **Main**-Methode fügen Sie die folgende Codezeile ein, sodass Sie die Konsolenausgabe lesen können, bevor die Anwendung beendet wird.

	Console.ReadLine();

Drücken Sie in Visual Studio nun F5, um die Anwendung im Debugmodus zu erstellen.

Die Ausgabe der GetStarted-Anwendung sollte jetzt angezeigt werden. Die Ausgabe zeigt die Ergebnisse der hinzugefügten Abfragen an. Sie sollte mit unten stehendem Beispieltext übereinstimmen.

	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
    	{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": "\"0000400c-0000-0000-0000-55233aed0000\"",
	  "_attachments": "attachments/"
	} from SQL
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": "\"0000400c-0000-0000-0000-55233aed0000\"",
	  "_attachments": "attachments/"
	} from LINQ
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": "\"0000400c-0000-0000-0000-55233aed0000\"",
	  "_attachments": "attachments/"
	} from LINQ query

Glückwunsch! Sie haben dieses NoSQL-Tutorial abgeschlossen und verfügen über eine funktionierende C#-Konsolenanwendung!

##<a id="GetSolution"></a> Abrufen der vollständigen Projektmappe für das NoSQL-Tutorial
Um die "GetStarted"-Lösung zu erstellen, die alle Beispiele dieses Artikels enthält, ist Folgendes erforderlich:

-   [DocumentDB-Konto][documentdb-create-account].
-   Die [GetStarted-Lösung](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) auf GitHub.

Um die Verweise auf das DocumentDB .NET SDK in Visual Studio wiederherzustellen, klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf die **GetStarted**-Lösung, und klicken Sie dann auf die Option **Aktivieren der NuGet-Paketwiederherstellung**. Aktualisieren Sie dann in der Datei "App.config" die Werte "EndpointUrl" und "AuthorizationKey" wie unter [Herstellen einer Verbindung mit einem DocumentDB-Konto](#Connect) beschrieben.

## Nächste Schritte

-   Möchten Sie ein komplexeres ASP.NET MVC-NoSQL-Tutorial ausführen? Siehe: [Erstellen einer Webanwendung mit ASP.NET MVC unter Verwendung von DocumentDB](documentdb-dotnet-application.md).
-	Sie erfahren, wie Sie ein [DocumentDB-Konto überwachen](documentdb-monitor-accounts.md).
-	Fragen Sie unser Beispieldataset im [Query Playground](https://www.documentdb.com/sql/demo) ab.
-	Weitere Informationen zum Programmiermodell finden Sie auf der [DocumentDB-Dokumentationsseite](https://azure.microsoft.com/documentation/services/documentdb/) im Abschnitt "Entwickeln".

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png
 

<!---HONumber=AcomDC_0128_2016-->