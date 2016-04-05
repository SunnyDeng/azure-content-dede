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
	ms.date="03/30/2016"
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

Prima. Damit ist die Einrichtung abgeschlossen und wir können mit dem Schreiben von Code beginnen. Ein vollständiges Codeprojekt dieses Tutorials finden Sie auf [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

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
		DocumentClient client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
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

Nachdem Sie nun wissen, wie Sie eine Verbindung mit einem DocumentDB-Konto herstellen und eine Instanz der Klasse **DocumentClient** erstellen, lassen Sie uns einen Blick auf die Arbeit mit DocumentDB-Ressourcen werfen.

## Schritt 4: Erstellen einer Onlinedatenbank
Ihre DocumentDB-[Datenbank](documentdb-resources.md#databases) kann mithilfe der Methode [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) der **DocumentClient**-Klasse erstellt werden. Eine Datenbank ist ein logischer Container für JSON-Dokumentspeicher, der auf Sammlungen aufgeteilt ist. Erstellen Sie die neue Datenbank in Ihrer **GetStartedDemo**-Methode, nachdem Sie den **DocumentClient** erstellt haben.

	// Check if the database FamilyDB does not exist
	string databaseName = "FamilyDB";
	
	try
	{
		await this.client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(familyName));
	}
	catch (DocumentClientException de)
	{
		// If the database does not exist, create a new database
		if (de.StatusCode == HttpStatusCode.NotFound)
		{
			await this.client.CreateDatabaseAsync(new Database { Id = familyName });
			this.WriteToConsoleAndPromptToContinue("Created {0}", databaseName);
		}
		else
		{
			throw;
		}
	}
	
##<a id="CreateColl"></a>Schritt 5: Erstellen einer Sammlung  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** erstellt eine neue Sammlung mit reserviertem Durchsatz, die Auswirkungen auf die Preise hat. Weitere Informationen finden Sie auf unserer [Preisseite](https://azure.microsoft.com/pricing/details/documentdb/).

Eine [Sammlung](documentdb-resources.md#collections) kann mithilfe der [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx)-Methode der **DocumentClient**-Klasse erstellt werden. Eine Sammlung ist ein Container für JSON-Dokumente und die zugehörige JavaScript-Anwendungslogik. Erstellen Sie nach dem Erstellen der Datenbank in der **GetStartedDemo**-Methode eine neue Sammlung mit dem Namen **FamilyCollection**.

	string collectionName = "FamilyCollection";
	try
	{
		await this.client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName));
	}
	catch (DocumentClientException de)
	{
		// If the document collection does not exist, create a new collection
		if (de.StatusCode == HttpStatusCode.NotFound)
		{
			// Configure the collection. Optionally, you can configure partitioning and indexing behavior of the collection here.
			DocumentCollection collectionInfo = new DocumentCollection();
			collectionInfo.Id = collectionName;

			// DocumentDB collections can be reserved with throughput specified in request units/second. 1 RU is a normalized request equivalent to the read
			// of a 1KB document.  Here we create a collection with 400 RU/s. 
			await this.client.CreateDocumentCollectionAsync(
				UriFactory.CreateDatabaseUri(databaseName),
				new DocumentCollection { Id = collectionName },
				new RequestOptions { OfferThroughput = 400 });

			this.WriteToConsoleAndPromptToContinue("Created {0}", collectionName);
		}
		else
		{
			throw;
		}
	}

##<a id="CreateDoc"></a>Schritt 6: Erstellen von JSON-Dokumenten
Ein [Dokument](documentdb-resources.md#documents) kann mithilfe der [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx)-Methode der **DocumentClient**-Klasse erstellt werden. Dokumente sind benutzerdefinierter (beliebiger) JSON-Inhalt. Wir können jetzt eines oder mehrere Dokumente einfügen. Wenn Sie bereits über Daten verfügen, die Sie in der Datenbank speichern möchten, können Sie das [Datenmigrationstool](documentdb-import-data.md) von DocumentDB verwenden.

Zunächst müssen wir eine **Family**-Klasse erstellen, die in diesem Beispiel in DocumentDB gespeicherte Objekte darstellt. Außerdem erstellen wir die Unterklassen **Parent**, **Child**, **Pet** und **Address**, die in **Family** verwendet werden. Beachten Sie, dass Dokumente eine **Id**-Eigenschaft enthalten müssen, die in JSON als **id** serialisiert wird. Erstellen Sie diese Klassen, indem Sie die folgenden internen Unterklassen nach der **GetStartedDemo**-Methode hinzufügen.

	public class Family
	{
		[JsonProperty(PropertyName = "id")]
		public string Id { get; set; }
		public string LastName { get; set; }
		string string District { get; set; }
		public Parent[] Parents { get; set; }
		public Child[] Children { get; set; }
		public Address Address { get; set; }
		public bool IsRegistered { get; set; }
	}
	
	public class Parent
	{
		public string FamilyName { get; set; }
		public string FirstName { get; set; }
	}

	public class Child
	{
		public string FamilyName { get; set; }
		public string FirstName { get; set; }
		public string Gender { get; set; }
		public int Grade { get; set; }
		public Pet[] Pets { get; set; }
	}

	public class Pet
	{
		public string GivenName { get; set; }
	}

	public class Address
	{
		public string State { get; set; }
		public string County { get; set; }
		public string City { get; set; }
	}


Als Nächstes erstellen Sie Ihre Dokumente innerhalb Ihrer asynchronen **GetStartedDemo**-Methode. Erstellen Sie zunächst eine **CreateFamilyDocumentIfNotExists**-Methode wie unten gezeigt:

	private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
	{
		try
		{
			await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
		}
		catch (DocumentClientException de)
		{
			if (de.StatusCode == HttpStatusCode.NotFound)
			{
				await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
				this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
			}
			else
			{
				throw;
			}
		}
	}

Fügen Sie zwei Dokumente ein, eines für die Familie Andersen und eines für die Familie Wakefield, wie im Folgenden dargestellt:

	// Insert a document, here we create a Family object
	Family andersenFamily = new Family
	{
		Id = "Andersen.1",
		LastName = "Andersen",
		Parents = new Parent[] 
		{
			new Parent { FirstName = "Thomas" },
			new Parent { FirstName = "Mary Kay" }
		},
		Children = new Child[] 
		{
			new Child
			{
				FirstName = "Henriette Thaulow",
				Gender = "female",
				Grade = 5,
				Pets = new Pet[] 
				{
					new Pet { GivenName = "Fluffy" }
				}
			}
		},
		District = "WA5",
		Address = new Address { State = "WA", County = "King", City = "Seattle" },
		IsRegistered = true
	};

	await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

	Family wakefieldFamily = new Family
	{
		Id = "Wakefield.7",
		LastName = "Wakefield",
		Parents = new Parent[]
		{
			new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
			new Parent { FamilyName = "Miller", FirstName = "Ben" }
		},
		Children = new Child[]
		{
			new Child
			{
				FamilyName = "Merriam",
				FirstName = "Jesse",
				Gender = "female",
				Grade = 8,
				Pets = new Pet[]
				{
					new Pet { GivenName = "Goofy" },
					new Pet { GivenName = "Shadow" }
				}
			},
			new Child
			{
				FamilyName = "Miller",
				FirstName = "Lisa",
				Gender = "female",
				Grade = 1
			}
		},
		District = "NY23",
		Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
		IsRegistered = false
	};

	await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

Sie haben jetzt die folgende Datenbank, Sammlung und die folgenden Dokumente in Ihrem DocumentDB-Konto erstellt.

![Diagramm zur hierarchischen Beziehung zwischen dem Konto, der Onlinedatenbank, der Sammlung und den Dokumenten, die vom NoSQL-Tutorial zum Erstellen einer C#-Konsolenanwendung verwendet werden.](./media/documentdb-get-started/nosql-tutorial-account-database.png)

##<a id="Query"></a>Schritt 7: Abfragen von DocumentDB-Ressourcen

DocumentDB unterstützt umfassende [Abfragen](documentdb-sql-query.md) der in jeder Sammlung gespeicherten JSON-Dokumente. Der folgende Beispielcode zeigt die verschiedenen Abfragen - sowohl mithilfe der SQL-Syntax als auch LINQ -, die wir nun mit den Dokumenten ausführen können, die wir im vorherigen Schritt eingefügt haben. Fügen Sie diese Abfragen Ihrer asynchronen **GetStartedDemo**-Methode hinzu.

	// Run a simple query via LINQ. DocumentDB indexes all properties, so queries can be completed efficiently and with low latency.
	// Here we find the Andersen family via its LastName
	IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
		UriFactory.CreateDocumentCollectionUri(databaseName, collectionName))
		.Where(f => f.LastName == "Andersen");

	// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
	Console.WriteLine("Running LINQ query...");
	foreach (Family family in familyQuery)
	{
			Console.WriteLine("\tRead {0}", family);
	}

	// Now execute the same query via direct SQL
	IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
		UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
		"SELECT * FROM Family WHERE Family.lastName = 'Andersen'");

	Console.WriteLine("Running direct SQL query...");
	foreach (Family family in familyQuery)
	{
		Console.WriteLine("\tRead {0}", family);
	}

Das folgende Diagramm veranschaulicht, wie die DocumentDB-SQL-Abfragesyntax gegen die erstellte Sammlung aufgerufen wird. Die gleiche Logik wird auf die LINQ-Abfrage angewendet.

![Diagramm zur Veranschaulichung des Bereichs und der Bedeutung der Abfrage, die vom NoSQL-Tutorial zum Erstellen einer C#-Konsolenanwendung verwendet wird.](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

Das [FROM](documentdb-sql-query.md#from-clause) -Schlüsselwort ist in der Abfrage optional, da DocumentDB Abfragen auf eine Sammlung begrenzt. Aus diesem Grund ist "FROM Familien f" austauschbar mit "FROM Stamm r" oder einem anderen variablen Namen, den Sie auswählen. DocumentDB wird ableiten, dass Familien, Stamm oder der variable Name, den Sie ausgewählt haben, standardmäßig auf die aktuelle Sammlung verweisen.

##<a id="DeleteDatabase"></a>Schritt 8: Löschen der Onlinedatenbank

Das Löschen der erstellten Datenbank entfernt die Datenbank und alle untergeordneten Ressourcen (Sammlungen, Dokumente usw.). Sie können die Datenbank und den Dokument-Client löschen, indem Sie folgenden Codeausschnitt an das Ende der asynchronen **GetStartedDemo**-Methode hängen.

	// Clean up/delete the database
	await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));

##<a id="Run"></a>Schritt 9: Ausführen der C#-Konsolenanwendung

Sie können die Anwendung nun ausführen. Am Ende der **Main**-Methode fügen Sie die folgende Codezeile ein, sodass Sie die Konsolenausgabe lesen können, bevor die Anwendung beendet wird.

	Console.ReadLine();

Drücken Sie in Visual Studio nun F5, um die Anwendung im Debugmodus zu erstellen.

Die Ausgabe der GetStarted-Anwendung sollte jetzt angezeigt werden. Die Ausgabe zeigt die Ergebnisse der hinzugefügten Abfragen an. Sie sollte mit unten stehendem Beispieltext übereinstimmen.

	Created FamilyDB
	Press any key to continue ...
	Created FamilyCollection
	Press any key to continue ...
	Created Family Andersen.1
	Press any key to continue ...
	Created Family Wakefield.7
	Press any key to continue ...
	Running LINQ query...
		Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
	Running direct SQL query...
		Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
	End of demo, press any key to exit.

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

<!---HONumber=AcomDC_0330_2016-->