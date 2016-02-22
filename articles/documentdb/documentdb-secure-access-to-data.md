<properties 
	pageTitle="Informationen zum sicheren Zugriff auf Daten in DocumentDB | Microsoft Azure" 
	description="Informationen zu Zugriffssteuerungskonzepten in DocumentDB, darunter Hauptschlüssel, Schlüssel mit Leseberechtigung, Benutzer und Berechtigungen." 
	services="documentdb" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="ryancraw"/>

# Sicherer Zugriff auf Daten in DocumentDB #

Dieser Artikel bietet eine Übersicht über den sicheren Zugriff auf in [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) gespeicherte Daten.

Wenn Sie diesen Artikel gelesen haben, können Sie folgende Fragen beantworten:

-	Was sind Hauptschlüssel in DocumentDB?
-	Was sind Schlüssel mit Leseberechtigung in DocumentDB?
-	Was sind Ressourcentoken in DocumentDB?
-	Wie kann ich mithilfe von DocumentDB-Benutzern und -Berechtigungen sicheren Zugriff auf DocumentDB-Daten gewährleisten?

##<a id="Sub1"></a>Zugriffssteuerungskonzepte in DocumentDB##

DocumentDB bietet erstklassige Zugriffsteuerungskonzepte für DocumentDB-Ressourcen. Für die Zwecke dieses Artikels werden DocumentDB-Ressourcen in zwei Kategorien unterteilt:

- Verwaltungsressourcen
	- Konto
	- Datenbank
	- Benutzer
	- Berechtigung
- Anwendungsressourcen
	- Sammlung
	- Dokument
	- Anhang
	- Gespeicherte Prozedur
	- Trigger
	- Benutzerdefinierte Funktionen

DocumentDB unterstützt im Rahmen dieser zwei Kategorien die folgenden drei Typen von Zugriffssteuerungsrollen: Kontoadministrator, Administrator mit Leseberechtigung und Datenbankbenutzer. Die Zugriffssteuerungsrollen verfügen über folgende Rechte:
 
- Kontoadministrator: Vollzugriff auf alle Ressourcen (Verwaltung und Anwendung) in einem bestimmten DocumentDB-Konto.
- Administrator mit Leseberechtigung: Schreibgeschützter Zugriff auf alle Ressourcen (Verwaltung und Anwendung) in einem bestimmten DocumentDB-Konto. 
- Datenbankbenutzer: Die einem bestimmten Satz an DocumentDB-Datenbankressourcen zugeordnete DocumentDB-Benutzerressource (z. B. Sammlungen, Dokumente, Skripts). Es können mehrere Benutzerressourcen einer bestimmten Datenbank zugeordnet werden, und jede Benutzerressource kann über mehrere Berechtigungen verfügen.

Das DocumentDB-Zugriffssteuerungsmodell definiert mit Berücksichtigung der oben genannten Kategorien drei Typen von Zugriffskonstrukten:

- Hauptschlüssel: Bei Erstellung eines DocumentDB-Kontos werden zwei Hauptschlüssel (primärer und sekundärer Schlüssel) erstellt. Diese Schlüssel ermöglichen vollen Administratorzugriff auf alle Ressourcen in einem DocumentDB-Konto.

![Abbildung der DocumentDB-Hauptschlüssel](./media/documentdb-secure-access-to-data/masterkeys.png)

- Schlüssel mit Leseberechtigung: Bei Erstellung eines DocumentDB-Kontos werden zwei Schlüssel mit Leseberechtigung (primärer und sekundärer Schlüssel) erstellt. Diese Schlüssel ermöglichen schreibgeschützten Zugriff auf alle Ressourcen in einem DocumentDB-Konto.

![Abbildung der schreibgeschützten DocumentDB-Schlüssel](./media/documentdb-secure-access-to-data/readonlykeys.png)

- Ressourcentoken: Ein Ressourcentoken ist einer DocumentDB-Berechtigungsressource zugeordnet, und erfasst die Beziehung zwischen dem Benutzer einer Datenbank und der Berechtigung, über die der Benutzer für eine bestimmte DocumentDB-Anwendungsressource (z. B. Sammlung, Dokument) verfügt.

![Abbildung der DocumentDB-Ressourcentoken](./media/documentdb-secure-access-to-data/resourcekeys.png)

##<a id="Sub2"></a>Arbeiten mit DocumentDB-Hauptschlüsseln und Schlüsseln mit Leseberechtigung ##
Wie bereits erwähnt, bieten DocumentDB-Hauptschlüssel vollen Administratorzugriff auf alle Ressourcen in einem DocumentDB-Konto, während Schlüssel mit Leseberechtigung schreibgeschützten Zugriff auf alle Ressourcen in einem Konto ermöglichen. Der folgende Codeausschnitt veranschaulicht, wie mit einem DocumentDB-Kontoendpunkt und einem Hauptschlüssel DocumentClient instanziiert und eine neue Datenbank erstellt werden kann.

    //Read the DocumentDB endpointUrl and authorization keys from config.
    //These values are available from the Azure Classic Portal on the DocumentDB Account Blade under "Keys".
    //NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.
    
	private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
    private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);
        
    client = new DocumentClient(new Uri(endpointUrl), authorizationKey);
    
	//Create Database
    Database database = await client.CreateDatabaseAsync(
        new Database
        {
            Id = databaseName
        });


##<a id="Sub3"></a>Übersicht über DocumentDB-Ressourcentoken ##
Mit einem Ressourcentoken (durch Erstellung von DocumentDB-Benutzern und -Berechtigungen) können Sie einem Client, dem Sie den Hauptschlüssel nicht anvertrauen können, Zugriff auf Ressourcen in Ihrem DocumentDB-Konto gewähren. Ihre DocumentDB-Hauptschlüssel bestehen aus einem Primärschlüssel und einem sekundären Schlüssel. Beide gewähren Administratorzugriff auf Ihr Konto und alle enthaltenen Ressourcen. Wenn Sie Ihre Hauptschlüssel weitergeben, besteht die Gefahr von böswilliger oder fahrlässiger Nutzung.

DocumentDB-Schlüssel mit Lesezugriff gewähren schreibgeschützen Zugriff auf alle Ressourcen, mit Ausnahme von Berechtigungsressourcen, in einem DocumentDB-Konto und können nicht zum differenzierteren Zugriff auf bestimmte DocumentDB-Ressourcen verwendet werden.

DocumentDB-Ressourcentoken stellen eine sichere Alternative dazu, Clients entsprechend den gewährten Berechtigungen das Lesen, Schreiben und Löschen von Ressourcen in einem DocumentDB-Konto zu ermöglichen, ohne die Notwendigkeit eines Hauptschlüssels oder eines Schlüssels mit Leseberechtigung.

Im Folgenden finden Sie ein typisches Entwurfsmuster, bei dem Ressourcentoken angefordert, generiert und an Clients übermittelt werden können:

1. Ein Mid-Tier-Dienst dient zum Freigeben von Benutzerfotos für eine mobile Anwendung.
2. Der Mid-Tier-Dienst verfügt über den Hauptschlüssel des DocumentDB-Kontos.
3. Die Fotoapp ist auf mobilen Geräten des Endbenutzers installiert. 
4. Bei der Anmeldung richtet die Fotoapp die Identität des Benutzers mit dem Mid-Tier-Dienst ein. Dieser Mechanismus der Identitätseinrichtung hängt nur von der Anwendung ab.
5. Nachdem die Identität eingerichtet wurde, fordert der Mid-Tier-Dienst Berechtigungen auf Grundlage der Identität an.
6. Der Mid-Tier-Dienst sendet einen Ressourcentoken an die Phoneapp zurück.
7. Die Phoneapp kann weiterhin den Ressourcentoken für direkten Zugriff auf DocumentDB-Ressourcen mit den Berechtigungen verwenden, die durch das Ressourcentoken für einen bestimmten Zeitraum definiert sind. 
8. Wenn das Ressourcentoken abläuft, tritt bei nachfolgenden Anforderungen eine Ausnahme 401 auf, die nichtautorisierten Zugriff meldet. An dieser Stelle richtet die Phoneapp die Identität erneut ein und fordert ein neues Ressourcentoken an.

![Workflow der DocumentDB-Ressourcentoken](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

##<a id="Sub4"></a>Arbeiten mit DocumentDB-Benutzern und -Berechtigungen ##
Eine DocumentDB-Benutzerressource ist einer DocumentDB-Datenbank zugeordnet. Jede Datenbank kann null oder mehr DocumentDB-Benutzer enthalten. Der folgende Codeausschnitt zeigt, wie eine DocumentDB-Benutzerressourcen erstellt werden kann.

	//Create a user.
    User docUser = new User
    {
        Id = "mobileuser"
    };

    docUser = await client.CreateUserAsync(database.SelfLink, docUser);

> [AZURE.NOTE] Jeder DocumentDB-Benutzer hat eine PermissionsLink-Eigenschaft, mit der die Liste der dem Benutzer zugeordneten Berechtigungen abgerufen werden kann.

Eine DocumentDB-Berechtigungsressource ist einem DocumentDB-Benutzer zugeordnet. Jeder Benutzer kann null oder mehr DocumentDB-Berechtigungen enthalten. Eine Berechtigungsressource ermöglicht den Zugriff auf ein Sicherheitstoken, das der Benutzer beim Zugriff auf eine bestimmte Anwendungsressource benötigt. Es gibt zwei verfügbare Zugriffsebenen, die von einer Berechtigungsressource bereitgestellt werden können:

- Alle: Der Benutzer verfügt über Vollzugriff auf die Ressource
- Lesen: Der Benutzer kann die Inhalte der Ressource nur lesen und keine Schreib-, Aktualisierungs- oder Löschvorgänge für die Ressource vornehmen.


> [AZURE.NOTE] Zum Ausführen von gespeicherten DocumentDB-Prozeduren muss der Benutzer über uneingeschränkte Berechtigung für die Sammlung verfügen, in dem die gespeicherte Prozedur ausgeführt wird.


Der folgende Codeausschnitt zeigt, wie eine Berechtigungsressource erstellt, das Ressourcentoken (Token) der Berechtigungsressource gelesen und die Berechtigungen dem oben erstellen Benutzer zugeordnet werden können.

	//Create a permission.

    Permission docPermission = new Permission
    {
        PermissionMode = PermissionMode.Read,
        ResourceLink = documentCollection.SelfLink,
        Id = "readperm"
    };
            
	docPermission = await client.CreatePermissionAsync(docUser.SelfLink, docPermission);
	Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);

Um alle einem bestimmten Benutzer zugeordneten Berechtigungsressourcen abzurufen, stellt DocumentDB ein Berechtigungsfeed für jedes Benutzerobjekt zur Verfügung. Der folgende Codeausschnitt zeigt, wie die dem oben erstellten Benutzer zugeordnete Berechtigung abgerufen, eine Berechtigungsliste erstellt und ein neuer DocumentClient für den Benutzer instanziiert werden kann.

	//Read a permission feed.
    FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(docUser.SelfLink);
	
	List<Permission> permList = new List<Permission>();
    
	foreach (Permission perm in permFeed)
    {
        permList.Add(perm);
    }
            
    DocumentClient userClient = new DocumentClient(new Uri(endpointUrl),permList);

> [AZURE.TIP] Ressourcentoken verfügen über einen gültigen Zeitspannenwert von einer Stunde. Die Gültigkeitsdauer des Tokens kann bis maximal fünf Stunden angegeben werden.

##<a name="NextSteps"></a>Nächste Schritte

- Um weitere Informationen zu DocumentDB zu erhalten, klicken Sie [hier](http://azure.com/docdb).
- Weitere Informationen zum Verwalten von Hauptschlüsseln und Schlüsseln mit Leseberechtigungen finden Sie [hier](documentdb-manage-account.md).
- Informationen zum Erstellen von DocumentDB-Autorisierungstoken finden klicken Sie [hier](https://msdn.microsoft.com/library/azure/dn783368.aspx)
 

<!---HONumber=AcomDC_0211_2016-->