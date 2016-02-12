<properties 
	pageTitle="Mehrinstanzenfähige Anwendungen mit elastischen Datenbanktools und zeilenbasierter Sicherheit" 
	description="Erfahren Sie, wie Sie mithilfe von elastischen Datenbanktools in Verbindung mit zeilenbasierter Sicherheit eine Anwendung mit einer hochgradig skalierbaren Datenebene in einer Azure SQL-Datenbank erstellen, die mehrinstanzenfähige Shards unterstützt." 
	metaKeywords="azure sql database elastic tools multi tenant row level security rls" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="tmullaney"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2016" 
	ms.author="thmullan;torsteng;sidneyh" />

# Mehrinstanzenfähige Anwendungen mit elastischen Datenbanktools und zeilenbasierter Sicherheit 

[Elastische Datenbanktools](sql-database-elastic-scale-get-started.md) und die [zeilenbasierte Sicherheit (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/dn765131) bieten leistungsstarke Funktionen für die flexible und effiziente Skalierung der Datenebene einer mehrinstanzenfähigen Anwendung mit Azure SQL-Datenbank. In diesem Artikel wird veranschaulicht, wie diese Technologien zusammen verwendet werden, um eine Anwendung mit einer hochgradig skalierbaren Datenebene zu erstellen, die mehrinstanzenfähige Shards mithilfe von **ADO.NET SqlClient** und/oder **Entity Framework** unterstützt.

* **Elastische Datenbanktools** ermöglichen Entwicklern eine Skalierung der Datenebene einer Anwendung über Sharding-Methoden nach Branchenstandard unter Verwendung von Bibliotheken für .NET und Azure-Dienstvorlagen. Die Verwaltung von Shards mit der elastischen Datenbank-Clientbibliothek hilft bei der Automatisierung und Optimierung zahlreicher infrastruktureller Aufgaben, die i. d. R. Sharding zugeordnet werden. 

* Die **zeilenbasierte Sicherheit (RLS)** ermöglicht Entwicklern das Speichern von Daten für mehrere Mandanten in derselben Datenbank mithilfe von Sicherheitsrichtlinien zum Filtern von Zeilen, die nicht zum Mandanten gehören, der eine Abfrage ausführt. Durch das Zentralisieren der Datenzugriffslogik mit RLS innerhalb der Datenbank statt in der Anwendung wird die Wartung vereinfacht und das Fehlerrisiko bei zunehmender Größe der Codebase einer Anwendung verringert. RLS erfordert das neueste [Azure SQL-Datenbank-Update (V12)](../sql-database-v12-whats-new.md).

Durch die gemeinsame Verwendung dieser Funktionen kann eine Anwendung von Kosteneinsparungen und Effizienz profitieren, da Daten für mehrere Mandanten in derselben Shard-Datenbank gespeichert werden. Gleichzeitig verfügt eine Anwendung weiterhin über die Flexibilität, isolierte Einzelinstanzen-Shards für "Premium"-Mandanten anzubieten, die zuverlässigere Leistungsgarantien erfordern, da mehrinstanzenfähige Shards keine gleichmäßige Ressourcenverteilung zwischen Mandanten gewährleisten.

Kurz gesagt, stellen die [datenabhängigen Routing-APIs](sql-database-elastic-scale-data-dependent-routing.md) der elastischen Datenbank-Clientbibliothek automatisch eine Verbindung zwischen den Mandanten und der richtigen Shard-Datenbank her, die die Sharding-Schlüssel (i. d. R. eine "Mandanten-ID") enthalten. Nachdem die Verbindung hergestellt wurde, stellt eine RLS-Sicherheitsrichtlinie innerhalb der Datenbank sicher, dass Mandanten nur auf Zeilen zugreifen können, die ihre Mandanten-ID enthalten. Es wird vorausgesetzt, dass alle Tabellen eine Mandanten-ID-Spalte enthalten, um anzugeben, welche Zeilen zu jedem Mandanten gehören.

![Blogging-App-Architektur][1]

## Herunterladen des Beispielprojekts

### Voraussetzungen
* Visual Studio (2012 oder neuere Version) 
* Drei Azure SQL-Datenbanken 
* Folgendes Beispielprojekt: [Elastic DB Tools for Azure SQL - Multi-Tenant Shards](http://go.microsoft.com/?linkid=9888163) (in englischer Sprache)
  * Geben Sie die Informationen für Ihre Datenbanken am Anfang der Datei **Program.cs** ein. 

Dieses Projekt stellt eine Erweiterung des unter [Elastische DB-Tools für Azure SQL – Entity Framework-Integration](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) beschriebenen Projekts dar, indem Unterstützung für Shard-Datenbanken mit mehreren Mandanten hinzugefügt wird. Es erstellt eine einfache Konsolenanwendung zum Erstellen von Blogs und Beiträgen mit vier Mandanten und zwei Shard-Datenbanken für mehrere Mandanten, wie in der obigen Abbildung dargestellt.

Erstellen Sie die Anwendung, und führen Sie sie aus. Dadurch werden ein Bootstrap für den Shard-Zuordnungs-Manager der elastischen Datenbanktools und die folgenden Tests ausgeführt:

1. Erstellen Sie mithilfe von Entity Framework und LINQ einen neuen Blog und zeigen Sie dann alle Blogs für jeden Mandanten an.
2. Zeigen Sie mithilfe von ADO.NET SqlClient alle Blogs für einen Mandanten an.
3. Versuchen Sie, einen Blog für den falschen Mandanten einzufügen, um zu überprüfen, ob ein Fehler ausgelöst wird.  

Beachten Sie, dass diese gesamten Tests ein Problem aufzeigen, da RLS noch nicht in den Shard-Datenbanken aktiviert wurde: Mandanten können Blogs anzeigen, die Ihnen nicht gehören, und die Anwendung, wird nicht daran gehindert, einen Blog für den falschen Mandanten einzufügen. Im restlichen Artikel wird beschrieben, wie Sie diese Probleme beheben, indem Sie die Mandantenisolierung mit RLS erzwingen. Dies umfasst zwei Schritte:

1. **Anwendungsebene:** Ändern Sie den Anwendungscode so, dass die aktuelle „TenantId“ im SESSION\_CONTEXT immer nach dem Öffnen einer Verbindung festgelegt wird. Im Beispielprojekt wurde dies bereits vorgenommen. 
2. **Datenebene**: Erstellen Sie basierend auf der „TenantId“, die in SESSION\_CONTEXT gespeichert ist, in jeder Sharddatenbank eine RLS-Sicherheitsrichtlinie zum Filtern von Zeilen. Sie müssen dies für jede Shard-Datenbank durchführen, andernfalls werden die Zeilen in Shards mit mehreren Mandanten nicht gefiltert. 


## Schritt 1) Anwendungsebene: Legen Sie die TenantId im SESSION\_CONTEXT fest.

Nach dem Herstellen einer Verbindung mit einer Shard-Datenbank mithilfe der datenabhängigen Routing-APIs der Clientbibliothek für elastische Datenbanken muss die Anwendung noch die Datenbank anweisen, welche Mandanten-ID diese Verbindung verwendet, sodass eine RLS-Sicherheitsrichtlinie zu anderen Mandanten gehörende Zeilen herausfiltern kann. Die empfohlene Vorgehensweise zur Übergabe dieser Informationen besteht im Speichern der aktuellen „TenantId“ für diese Verbindung im [SESSION\_CONTEXT](https://msdn.microsoft.com/library/mt590806.aspx). (Hinweis: Sie können auch [CONTEXT\_INFO](https://msdn.microsoft.com/library/ms180125.aspx) verwenden, SESSION\_CONTEXT ist jedoch eine bessere Option, da sie einfacher zu verwenden ist, standardmäßig NULL zurückgibt und Schlüssel-Wert-Paare unterstützt.)

### Entity Framework

Für Anwendungen, die Entity Framework verwenden, ist die einfachste Herangehensweise, SESSION\_CONTEXT in der „ElasticScaleContext“-Überschreibung festzulegen, wie unter [Datenabhängiges Routing mit EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md/#data-dependent-routing-using-ef-dbcontext) beschrieben. Vor der Rückgabe der über datenabhängiges Routing vermittelten Verbindung erstellen Sie einfach einen SqlCommand, der die „TenantId“ im SESSION\_CONTEXT auf den „shardingKey“ festlegt, der für diese Verbindung angegeben wurde, und führen ihn aus. Auf diese Weise müssen Sie nur einmal Code zum Festlegen von SESSION\_CONTEXT schreiben.

```
// ElasticScaleContext.cs 
// ... 
// C'tor for data dependent routing. This call will open a validated connection routed to the proper 
// shard by the shard map manager. Note that the base class c'tor call will fail for an open connection 
// if migrations need to be done and SQL credentials are used. This is the reason for the  
// separation of c'tors into the DDR case (this c'tor) and the internal c'tor for new shards. 
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(OpenDDRConnection(shardMap, shardingKey, connectionStr), true /* contextOwnsConnection */)
{
}

public static SqlConnection OpenDDRConnection(ShardMap shardMap, T shardingKey, string connectionStr)
{
    // No initialization
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(shardingKey, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
} 
// ... 
```

Der SESSION\_CONTEXT wird jetzt automatisch auf die angegebene „TenantId“ festgelegt, wenn „ElasticScaleContext“ aufgerufen wird:

```
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
	using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
	{     
		var query = from b in db.Blogs
	                orderby b.Name
	                select b;
		
		Console.WriteLine("All blogs for TenantId {0}:", tenantId);     
		foreach (var item in query)     
		{       
			Console.WriteLine(item.Name);     
		}   
	} 
}); 
```

### ADO.NET SqlClient 

Bei Anwendungen mit ADO.NET SqlClient besteht die empfohlene Vorgehensweise darin, eine Wrapperfunktion um ShardMap.OpenConnectionForKey() zu erstellen, die die „TenantId“ im SESSION\_CONTEXT automatisch auf die richtige „TenantId“ festlegt, bevor eine Verbindung zurückgegeben wird. Um sicherzustellen, dass SESSION\_CONTEXT immer richtig konfiguriert ist, sollten Sie Verbindungen nur mit dieser Wrapperfunktion öffnen.

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that SESSION_CONTEXT is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient
// If row-level security is enabled, only Tenant 4's blogs will be listed
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine("--\nAll blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);
        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## Schritt 2) Datenebene: Erstellen Sie eine zeilenbasierte Sicherheitsrichtlinie

### Erstellen einer Sicherheitsrichtlinie, um die Zeilen zu filtern, auf die jeder Mandant zugreifen kann

Da die Anwendung nun SESSION\_CONTEXT vor dem Abfragen auf die aktuelle „TenantId“ festlegt, kann eine RLS-Sicherheitsrichtlinie Abfragen filtern und Zeilen ausschließen, die eine andere TenantId aufweisen.

RLS ist in T-SQL implementiert: Eine benutzerdefinierte Funktion definiert die Zugriffslogik, und eine Sicherheitsrichtlinie bindet diese Funktion an eine beliebige Anzahl von Tabellen. Für dieses Projekt stellt die Funktion einfach sicher, dass die Anwendung (statt eines anderen SQL-Benutzers) mit der Datenbank verbunden wird, und die im SESSION\_CONTEXT gespeicherte „TenantId“ mit der „TenantId“ einer bestimmten Zeile übereinstimmt. Durch ein Filterprädikat können Zeilen, die diese Bedingungen erfüllen, durch den Filter für SELECT-, UPDATE- und DELETE-Abfragen übergeben werden. Ein Blockprädikat verhindert, dass Zeilen, die diese Bedingungen verletzen, EINGEFÜGT oder AKTUALISIERT werden. Wenn SESSION\_CONTEXT nicht festgelegt wurde, wird NULL zurückgegeben. Darüber hinaus sind Zeilen nicht sichtbar oder können nicht eingefügt werden.

Um RLS zu aktivieren, führen Sie auf allen Shards das folgende T-SQL-Skript mithilfe von Visual Studio (SSDT), SSMS oder dem PowerShell-Skript aus, das im Projekt enthalten ist. (Bei Verwendung von [elastischen Datenbankaufträgen](sql-database-elastic-jobs-overview.md) können Sie es zur Automatisierung der Ausführung dieses T-SQL-Skripts auf allen Shards verwenden):

```
CREATE SCHEMA rls -- separate schema to organize RLS objects 
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
	RETURNS TABLE     
	WITH SCHEMABINDING
AS
	RETURN SELECT 1 AS fn_accessResult          
		WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- the user in your application’s connection string (dbo is only for demo purposes!)         
		AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
	ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
	ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [AZURE.TIP] Für komplexere Projekte, die das Prädikat in Hunderten von Tabellen hinzufügen müssen, können Sie eine gespeicherte Hilfsprozedur verwenden, die automatisch eine Sicherheitsrichtlinie generiert, die allen Tabellen in einem Schema ein Prädikat hinzufügt. Weitere Informationen finden Sie unter [Apply Row-Level Security to all tables – helper script (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script) (in englischer Sprache).

Wenn Sie die Beispielanwendung jetzt erneut ausführen, können Mandanten nur Zeilen anzeigen, die zu ihnen gehören. Darüber hinaus kann die Anwendung keine Zeilen einfügen, die Mandanten gehören, die nicht derzeit mit der Sharddatenbank verbunden sind. Zudem können sichtbare Zeilen nicht mit einer anderen „TenantId“ aktualisiert werden. Wenn die Anwendung versucht, eine dieser Aktionen auszuführen, wird eine "DbUpdateException" ausgelöst.

Wenn Sie später eine neue Tabelle hinzufügen, ÄNDERN Sie einfach die Sicherheitsrichtlinie und fügen Filter- und Blockprädikate der neuen Tabelle hinzu:

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
	ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable
GO 
```

### Hinzufügen von Standardeinschränkungen zum automatischen Auffüllen der Mandanten-ID für INSERTs. 

Sie können eine Standardeinschränkung für jede Tabelle festlegen, um die TenantId beim Einfügen von Zeilen automatisch mit dem derzeit in SESSION\_CONTEXT gespeicherten Wert aufzufüllen. Beispiel:

```
-- Create default constraints to auto-populate TenantId with the value of SESSION_CONTEXT for inserts 
ALTER TABLE Blogs     
	ADD CONSTRAINT df_TenantId_Blogs      
	DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO

ALTER TABLE Posts     
	ADD CONSTRAINT df_TenantId_Posts      
	DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO 
```

Jetzt muss die Anwendung beim Einfügen von Zeilen keine Mandanten-ID angeben:

```
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
	using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
	{
		var blog = new Blog { Name = name }; // default constraint sets TenantId automatically     
		db.Blogs.Add(blog);     
		db.SaveChanges();   
	} 
}); 
```

> [AZURE.NOTE] Bei der Verwendung von Standardeinschränkungen für ein Entity Framework-Projekt wird empfohlen, dass Sie NICHT die Mandanten-ID-Spalte in Ihr EF-Datenmodell einbeziehen. Dies liegt daran, dass Entity Framework-Abfragen automatisch Standardwerte bereitstellen, die die in T-SQL erstellten Standardeinschränkungen mit SESSION\_CONTEXT außer Kraft setzen. Um Standardeinschränkungen im Beispielprojekt zu verwenden, sollten Sie z. B. "TenantId" (die Mandanten-ID) aus "DataClasses.cs" entfernen (und "Add-Migration" in der Paket-Manager-Konsole ausführen) sowie T-SQL verwenden, um sicherzustellen, dass das Feld nur in den Datenbanktabellen vorhanden ist. Auf diese Weise wird EF beim Einfügen von Daten nicht automatisch falsche Standardwerte übergeben.

### (Optional:) Aktivieren Sie einen "superuser", der auf alle Zeilen zugreifen kann.
Einige Anwendungen sollten einen "superuser" erstellen, der auf alle Zeilen zugreifen kann, um Berichterstellung für alle Mandanten auf allen Shards zu aktivieren oder um Teilungs-/Zusammenführungsoperationen für Shards auszuführen, bei denen Mandantenzeilen zwischen Datenbanken verschoben werden. Um dies zu ermöglichen, sollten Sie einen neuen SQL-Benutzer (in diesem Beispiel "superuser") in den einzelnen Shard-Datenbanken erstellen. Ändern Sie dann die Sicherheitsrichtlinie mit einer neuen Prädikatfunktion, durch die dieser Benutzer Zugriff auf alle Zeilen erhält:

```
-- New predicate function that adds superuser logic
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- note, should not be dbo!
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### Wartung 

* **Neue Shards hinzufügen**: Sie müssen das T-SQL-Skript zum Aktivieren von RLS auf allen neuen Shards ausführen, andernfalls werden Abfragen für diese Shards nicht gefiltert.

* **Neue Tabellen hinzufügen**: Sie müssen ein Filter- und Blockprädikat den Sicherheitsrichtlinien aller Shards hinzufügen, wenn eine neue Tabelle erstellt wird, andernfalls werden Abfragen für die neue Tabelle nicht gefiltert. Dies kann mithilfe eines DDL-Triggers automatisiert werden, wie unter [Apply Row-Level Security automatically to newly created tables (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx) (in englischer Sprache) beschrieben.


## Zusammenfassung 

Elastische Datenbanktools und zeilenbasierte Sicherheit können zusammen zum horizontalen Skalieren der Datenebene einer Anwendung mit Unterstützung für Shards mit sowohl mehreren als einzelnen Mandanten verwendet werden. Mehrinstanzenfähige Shards können zum effizienten Speichern von Daten (insbesondere in Fällen, in denen eine große Anzahl von Mandanten über wenige Datenzeilen verfügt) verwendet werden, während Shards mit einzelnen Instanzen zur Unterstützung von Premium-Mandanten mit strengeren Anforderungen an Leistung und Isolation verwendet werden können. Weitere Informationen finden Sie unter [Sicherheit auf Zeilenebene](https://msdn.microsoft.com/library/dn765131).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->

<!---HONumber=AcomDC_0204_2016-->