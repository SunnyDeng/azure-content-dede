<properties
	pageTitle="Erste Schritte mit dem DocumentDB .NET SDK | Microsoft Azure"
	description="Erfahren Sie, wie Sie ein Azure DocumentDB-Konto erstellen und konfigurieren, Datenbanken erstellen, Sammlungen erstellen und JSON-Dokumente in Ihrem NoSQL-Dokumentdatenbankkonto speichern."
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
	ms.date="09/03/2015"
	ms.author="anhoh"/>

#Erste Schritte mit dem DocumentDB .NET SDK  

In diesem Lernprogramm werden die ersten Schritte bei der Verwendung von [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) und [DocumentDB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) beschrieben. Erstellen Sie eine Konsolenanwendung, die DocumentDB-Ressourcen erstellt und abfragt, und die Ausgabe in das Konsolenfenster schreibt.

DocumentDB ist ein NoSQL-Dokumentdatenbankdienst, der über eine [Reihe von APIs und SDKs](https://msdn.microsoft.com/library/dn781482.aspx) verfügt. Der Code in diesem Artikel ist in C# geschrieben und verwendet das DocumentDB .NET SDK, das als NuGet-Paket verpackt und verteilt wird.

In diesem Artikel werden die folgenden Szenarien behandelt:

- Erstellen eines DocumentDB-Kontos und Verbindungsaufbau
- Hinzufügen von DocumentDB zu Ihrer Visual Studio-Projektmappe
- Erstellen von Datenbanken
- Erstellen von Sammlungen
- Erstellen von JSON-Dokumenten
- Abfragen von Ressourcen
- Löschen von Datenbanken

Sie haben keine Zeit, um das Lernprogramm abzuschließen, und möchten nur die Arbeitslösung abrufen? Kein Problem. Die vollständige Lösung ist auf [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) verfügbar. In [Abrufen der vollständigen Lösung](#GetSolution) finden Sie eine Kurzanleitung.

Sobald Sie das Tutorial abgeschlossen haben, verwenden Sie bitte die Abstimmungsschaltflächen am Anfang oder Ende des Themas, um uns Ihre Meinung mitzuteilen. Dieses Thema wird aktiv aktualisiert, daher benötigen wir Ihr Feedback, um es stetig zu verbessern. Wenn wir mit Ihnen Kontakt aufnehmen sollen, können Sie Ihre E-Mail-Adresse im Kommentar hinterlassen.

## Voraussetzungen

Bevor Sie diesen Artikel durcharbeiten, sollten Sie sicherstellen, dass Folgendes vorhanden ist:

- Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenloses Azure-Testkonto](http://azure.microsoft.com/pricing/free-trial/).
- [Visual Studio 2013](http://www.visualstudio.com/), Update 4 oder höher

## Schritt 1: Erstellen eines DocumentDB-Kontos

Erstellen Sie zunächst ein DocumentDB-Konto. Wenn Sie bereits über ein Konto verfügen, können Sie fortfahren mit [Einrichten von Visual Studio-Projektmappe](#SetupVS).

[AZURE.INCLUDE [DocumentDB-Erstellen-DB-Account](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Schritt 2: Einrichten von Visual Studio-Projektmappe

1. Öffnen Sie **Visual Studio** auf Ihrem Computer.
2. Wählen Sie **Neu** im Menü **Datei** und wählen Sie dann **Projekt** aus.
3. Im **Dialogfeld "Neues Projekt"** wählen Sie **Vorlagen** / **Visual C#** / **Konsolenanwendung** aus, geben Sie Ihrem Projekt einen Namen, und klicken Sie dann auf **Hinzufügen**.
4. Im **Projektmappen-Explorer** klicken Sie mit der rechten Maustaste auf die neue Konsolenanwendung, die sich unter Ihrer Visual Studio-Projektmappe befindet.
5. Klicken Sie im gleichen Menü auf **NuGet-Pakete verwalten...**.
6. Klicken Sie im Bereich ganz links des Fensters **NuGet-Pakete verwalten** auf **Online** / **nuget.org**.
7. Suchen Sie im Eingabefeld **Onlinesuche** nach **DocumentDB-Clientbibliothek**.
8. Suchen Sie in den Ergebnissen nach **Microsoft Azure DocumentDB-Clientbibliothek** und klicken Sie auf **Installieren**. Die Paket-ID für die DocumentDB-Clientbibliothek lautet [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).

Prima. Sie können nun mit DocumentDB arbeiten.

##<a id="Connect"></a> Schritt 3: Herstellen einer Verbindung mit einem DocumentDB-Konto

Wir beginnen mit dem Erstellen einer neuen Instanz von [DocumentClient](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.aspx), um eine Verbindung mit unserem DocumentDB-Konto herzustellen. Dafür brauchen wir folgende Referenzen am Anfang unserer C#-Anwendung:

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

Ein **DocumentClient** kann mithilfe des DocumentDB-Kontoendpunkts und entweder dem primären oder sekundären Zugriffsschlüssel instanziiert werden, der mit dem Konto verknüpft ist. Fügen Sie diese Eigenschaften Ihrer Klasse hinzu.

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";

Jetzt erstellen wir eine neue asynchrone Aufgabe namens **GetStartedDemo** in Ihrer Klasse. Bei dieser neuen Aufgabe erstellen und richten Sie Ihren **DocumentClient** ein.

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient.
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

Rufen Sie die asynchrone Aufgabe von der Main-Methode ab, die dem folgenden Code ähnelt.

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

> [AZURE.WARNING]Speichern Sie niemals Anmeldeinformationen im Quellcode. Die Anmeldeinformationen sind hier im Code dargestellt, um das Beispiel zu vereinfachen. Siehe [Azure-Websites: Funktionsweise von Anwendungs- und Verbindungszeichenfolgen](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) für Informationen zum Speichern von Anmeldeinformationen in einer Produktionsumgebung. Sehen Sie sich unsere Beispielanwendung auf [GitHub](https://github.com/Azure/azure-documentdb-net/blob/master/tutorials/get-started/src/Program.cs) an, als Beispiel für das Speichern von Anmeldeinformationen außerhalb des Quellcodes.

Die Werte für EndpointUrl und AuthorizationKey sind der URI und der PRIMÄRSCHLÜSSEL für Ihr DocumentDB-Konto und können im Blatt [Schlüssel](https://portal.azure.com) für Ihr DocumentDB-Konto abgerufen werden.

![Screenshot des Azure-Vorschauportals mit einem DocumentDB-Konto, bei dem der ACTIVE-Hub, die Schaltfläche SCHLÜSSEL auf dem Blatt „DocumentDB-Konto“ und die Werte URI, PRIMÄRSCHLÜSSEL und SEKUNDÄRSCHLÜSSEL auf dem Blatt „Schlüssel“ hervorgehoben sind][keys]

Diese Schlüssel gewähren den Administratorzugriff auf Ihr DocumentDB-Konto und die darin enthaltenen Ressourcen. DocumentDB unterstützt auch die Verwendung von Ressourcenschlüsseln, mit denen Clients Ressourcen im DocumentDB-Konto entsprechend der von Ihnen eingeräumten Berechtigung lesen, schreiben und löschen können, ohne dass dafür ein Kontoschlüssel erforderlich ist. Weitere Informationen zu den Ressourcenschlüssel finden Sie unter [Berechtigungen](documentdb-resources.md#permissions) und [Anzeigen, Kopieren und erneutes Generieren von Zugriffsschlüsseln](documentdb-manage-account.md#keys).

Nachdem Sie nun wissen, wie Sie eine Verbindung mit einem DocumentDB-Konto herstellen und eine Instanz der Klasse **DocumentClient** erstellen, lassen Sie uns einen Blick auf die Arbeit mit DocumentDB-Ressourcen werfen.

## Schritt 4: Erstellen einer Datenbank
Eine [Datenbank](documentdb-resources.md#databases) kann mithilfe der [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx)-Methode der **DocumentClient**-Klasse erstellt werden. Eine Datenbank ist ein logischer Container für Dokumentspeicher, der auf Sammlungen aufgeteilt ist. Erstellen Sie die neue Datenbank in Ihrer **GetStartedDemo**-Methode, nachdem Sie den **DocumentClient** erstellt haben.

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Schritt 5: Erstellen einer Sammlung  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** erstellt eine neue S1-Sammlung, die hat Auswirkungen auf die Preise hat. Weitere Informationen finden Sie auf unserer [Preisseite](https://azure.microsoft.com/pricing/details/documentdb/).

Eine [Sammlung](documentdb-resources.md#collections) kann mithilfe der [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx)-Methode der **DocumentClient**-Klasse erstellt werden. Eine Sammlung ist ein Container für JSON-Dokumente und die zugehörige JavaScript-Anwendungslogik. Die neu erstellte Sammlung wird einer [S1-Leistungsebene](documentdb-performance-levels.md) zugeordnet. Die im vorherigen Schritt erstellte Datenbank besitzt eine Reihe von Eigenschaften, darunter die [CollectionsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.database.collectionslink.aspx)-Eigenschaft. Mit dieser Information können wir nach der Datenbank jetzt eine Sammlung erstellen.

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });

##<a id="CreateDoc"></a>Schritt 6: Erstellen von Dokumenten
Ein [Dokument](documentdb-resources.md#documents) kann mithilfe der [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx)-Methode der **DocumentClient**-Klasse erstellt werden. Dokumente sind benutzerdefinierter (beliebiger) JSON-Inhalt. Die im vorherigen Schritt erstellte Sammlung besitzt eine Reihe von Eigenschaften, darunter die [DocumentsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.documentcollection.documentslink.aspx)-Eigenschaft. Mit dieser Information können wir jetzt eines oder mehrere Dokumente einfügen. Wenn Sie bereits über Daten verfügen, die Sie in der Datenbank speichern möchten, können Sie das [Datenmigrationstool](documentdb-import-data.md) von DocumentDB verwenden.

Zunächst müssen wir eine **übergeordnete**, **untergeordnete**, **Pet-**, **Adress-** und **Familien-**Klasse erstellen. Erstellen Sie diese Klassen, indem Sie die folgenden internen Unterklassen hinzufügen.

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

Sie haben jetzt die folgende Datenbank, Sammlung und die folgenden Dokumente in Ihrem DocumentDB-Konto erstellt.

![Diagramm zur Veranschaulichung der hierarchischen Beziehung zwischen dem Konto, der Datenbank, der Sammlung und den Dokumenten](./media/documentdb-get-started/account-database.png)

##<a id="Query"></a>Schritt 7: Abfragen von DocumentDB-Ressourcen

DocumentDB unterstützt umfassende [Abfragen](documentdb-sql-query.md) der in jeder Sammlung gespeicherten JSON-Dokumente. Der folgende Beispielcode zeigt die verschiedenen Abfragen - sowohl mithilfe der SQL-Syntax als auch LINQ -, die wir nun mit den Dokumenten ausführen können, die wir im vorherigen Schritt eingefügt haben. Fügen Sie diese Abfragen Ihrer asynchronen **GetStartedDemo**-Methode hinzu.

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = "AndersenFamily"");

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

Das folgende Diagramm veranschaulicht, wie die DocumentDB-SQL-Abfragesyntax gegen die erstellte Sammlung aufgerufen wird. Die gleiche Logik wird auf die LINQ-Abfrage angewendet.

![Diagramm zur Veranschaulichung des Bereichs und der Bedeutung der Abfrage](./media/documentdb-get-started/collection-documents.png)

Das [FROM](documentdb-sql-query.md/#from-clause) -Schlüsselwort ist in der Abfrage optional, da DocumentDB Abfragen auf eine Sammlung begrenzt. Aus diesem Grund ist "FROM Familien f" austauschbar mit "FROM Stamm r" oder einem anderen variablen Namen, den Sie auswählen. DocumentDB wird ableiten, dass Familien, Stamm oder der variable Name, den Sie ausgewählt haben, standardmäßig auf die aktuelle Sammlung verweisen.

##<a id="DeleteDatabase"></a>Schritt 8: Löschen der Datenbank

Das Löschen der erstellten Datenbank entfernt die Datenbank und alle untergeordneten Ressourcen (Sammlungen, Dokumente usw.). Sie können die Datenbank und den Dokument-Client löschen, indem Sie folgenden Codeausschnitt an das Ende der asynchronen **GetStartedDemo**-Methode hängen.

    // Clean up/delete the database
    await client.DeleteDatabaseAsync(database.SelfLink);
	client.Dispose();

##<a id="Run"></a>Schritt 9: Anwendung ausführen

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
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
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
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
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
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ query
	{
	  "id": "AndersenFamily",
 	  "child": "Henriette Thaulow"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Jesse"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Lisa"
	}
	{ family = AndersenFamily, child = Henriette Thaulow }
	{ family = WakefieldFamily, child = Jesse }
	{ family = WakefieldFamily, child = Lisa }


> [AZURE.NOTE]Wenn Sie die Anwendung mehrmals ausführen, ohne die Datenbank zu entfernen, kommt es möglicherweise zu dem Problem, dass eine neue Datenbank mit einer ID erstellt wird, die bereits in Gebrauch ist. Um dies zu vermeiden, können Sie überprüfen, ob eine Datenbank, eine Sammlung oder ein Dokument mit der gleichen ID bereits vorhanden ist. Um zu sehen, wie dies erreicht werden kann, besuchen Sie unsere [GitHub-Seite](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started).

Herzlichen Glückwunsch! Sie haben Ihre erste DocumentDB-App erstellt!

##<a id="GetSolution"></a> Abrufen der vollständigen Lösung
Um die "GetStarted"-Lösung zu erstellen, die alle Beispiele dieses Artikels enthält, ist Folgendes erforderlich:

-   [DocumentDB-Konto][documentdb-create-account].
-   Die [GetStarted-Lösung](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) auf GitHub.

Um die Verweise auf das DocumentDB .NET SDK in Visual Studio 2013 wiederherzustellen, klicken Sie mit der rechten Maustaste im Projektmappen-Explorer auf die **GetStarted**-Lösung, und klicken Sie dann auf die Option **Aktivieren der NuGet-Paketwiederherstellung**. Aktualisieren Sie dann in der Datei "App.config" die Werte "EndpointUrl" und "AuthorizationKey" wie unter [Herstellen einer Verbindung mit einem DocumentDB-Konto](#Connect) beschrieben.

## Nächste Schritte

-   Wollen Sie ein komplexeres ASP.NET MVC-Beispiel? Siehe: [Erstellen einer Webanwendung mit ASP.NET MVC unter Verwendung von DocumentDB](documentdb-dotnet-application.md).
-	Sie erfahren, wie Sie ein [DocumentDB-Konto überwachen](documentdb-monitor-accounts.md).
-	Fragen Sie unser Beispieldataset im [Query Playground](https://www.documentdb.com/sql/demo) ab.
-	Weitere Informationen zum Programmiermodell finden Sie auf der [DocumentDB-Dokumentationsseite](../../services/documentdb/) im Abschnitt "Entwickeln".

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/keys.png
 

<!---HONumber=September15_HO1-->