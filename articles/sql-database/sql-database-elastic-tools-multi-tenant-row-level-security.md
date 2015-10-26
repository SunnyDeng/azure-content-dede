<properties 
	title="Multi-tenant applications with elastic database tools and row-level security" 
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
	ms.date="08/19/2015" 
	ms.author="thmullan;torsteng;sidneyh" />

# Mehrinstanzenfähige Anwendungen mit elastischen Datenbanktools und zeilenbasierter Sicherheit 

[Elastische Datenbanktools](sql-database-elastic-scale-get-started.md) und die [zeilenbasierte Sicherheit (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/dn765131) bieten leistungsstarke Funktionen für die flexible und effiziente Skalierung der Datenebene einer mehrinstanzenfähigen Anwendung mit Azure SQL-Datenbank. In diesem Artikel wird veranschaulicht, wie diese Technologien zusammen verwendet werden, um eine Anwendung mit einer hochgradig skalierbaren Datenebene zu erstellen, die mehrinstanzenfähige Shards mithilfe von **ADO.NET SqlClient** und/oder **Entity Framework** unterstützt.

* **Elastische Datenbanktools** ermöglichen Entwicklern eine Skalierung der Datenebene einer Anwendung über Sharding-Methoden nach Branchenstandard unter Verwendung von Bibliotheken für .NET und Azure-Dienstvorlagen. Die Verwaltung von Shards mit der elastischen Datenbank-Clientbibliothek hilft bei der Automatisierung und Optimierung zahlreicher infrastruktureller Aufgaben, die i. d. R. Sharding zugeordnet werden. 

* Die **zeilenbasierte Sicherheit (RLS)** ermöglicht Entwicklern das Speichern von Daten für mehrere Mandanten in derselben Datenbank mithilfe von Sicherheitsrichtlinien zum Filtern von Zeilen, die nicht zum Mandanten gehören, der eine Abfrage ausführt. Durch das Zentralisieren der Datenzugriffslogik mit RLS innerhalb der Datenbank statt in der Anwendung wird die Wartung vereinfacht und das Fehlerrisiko bei zunehmender Größe der Codebase einer Anwendung verringert. RLS erfordert das neueste [Azure SQL-Datenbank-Update (V12)](sql-database-preview-whats-new.md).

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

1. **Anwendungsebene:** Ändern Sie den Anwendungscode so, dass CONTEXT\_INFO immer nach dem Öffnen einer Verbindung auf die aktuelle Mandanten-ID festgelegt wird. Im Beispielprojekt wurde dies bereits vorgenommen. 
2. **Datenebene**: Erstellen Sie basierend auf dem Wert von CONTEXT\_INFO eine RLS-Sicherheitsrichtlinie zum Filtern von Zeilen in jeder Shard-Datenbank. Sie müssen dies für jede Shard-Datenbank durchführen, andernfalls werden die Zeilen in Shards mit mehreren Mandanten nicht gefiltert. 


## Schritt 1) Anwendungsebene: Legen Sie CONTEXT\_INFO auf die Mandanten-ID fest.

Nach dem Herstellen einer Verbindung mit einer Shard-Datenbank mithilfe der datenabhängigen Routing-APIs der Clientbibliothek für elastische Datenbanken muss die Anwendung noch die Datenbank anweisen, welche Mandanten-ID diese Verbindung verwendet, sodass eine RLS-Sicherheitsrichtlinie zu anderen Mandanten gehörende Zeilen herausfiltern kann. Die empfohlene Vorgehensweise zur Übergabe dieser Informationen besteht im Festlegen von [CONTEXT\_INFO](https://msdn.microsoft.com/library/ms180125) auf die aktuelle Mandanten-ID für diese Verbindung. Beachten Sie, dass CONTEXT\_INFO in Azure SQL-Datenbank mit einer sitzungsspezifischen GUID vorab aufgefüllt ist, sodass Sie CONTEXT\_INFO vor dem Ausführen von Abfragen für eine neue Verbindung auf die richtige Mandanten-ID festlegen *müssen*, um sicherzustellen, dass keine Zeilen unbeabsichtigt weitergegeben werden.

### Entity Framework

Für die Verwendung von Entity Framework-Anwendungen ist die einfachste Herangehensweise, CONTEXT\_INFO in der "ElasticScaleContext"-Überschreibung festzulegen, wie unter [Datenabhängiges Routing mit EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md/#data-dependent-routing-using-ef-dbcontext) beschrieben. Vor der Rückgabe der über datenabhängiges Routing vermittelten Verbindung erstellen Sie einfach einen SqlCommand, der CONTEXT\_INFO auf den "shardingkey" (die Mandanten-ID) festlegt, der für diese Verbindung angegeben wurde. Auf diese Weise müssen Sie nur einmal Code zum Festlegen von CONTEXT\_INFO schreiben.

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

        // Set CONTEXT_INFO to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SET CONTEXT_INFO @shardingKey";
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

CONTEXT\_INFO wird jetzt automatisch auf die angegebene Mandanten-ID festgelegt, wenn ElasticScaleContext aufgerufen wird:

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

Bei Anwendungen mit ADO.NET SqlClient ist die empfohlene Vorgehensweise, eine Wrapperfunktion um ShardMap.OpenConnectionForKey() zu erstellen, die CONTEXT\_INFO automatisch auf die richtige Mandanten-ID festlegt, bevor eine Verbindung zurückgegeben wird. Um sicherzustellen, dass CONTEXT\_INFO immer richtig konfiguriert ist, sollten Sie Verbindungen nur mit dieser Wrapperfunktion öffnen.

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets CONTEXT_INFO to the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that CONTEXT_INFO is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set CONTEXT_INFO to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SET CONTEXT_INFO @shardingKey";
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

## Schritt 2) Datenebene: Erstellen Sie zeilenbasierte Sicherheitsrichtlinien und Einschränkungen. 

### Erstellen Sie eine Sicherheitsrichtlinie zum Filtern von SELECT-, UPDATE- und DELETE- Abfragen. 

Da die Anwendung nun CONTEXT\_INFO vor dem Abfragen auf die aktuelle Mandanten-ID festlegt, kann eine RLS-Sicherheitsrichtlinie Abfragen filtern und Zeilen ausschließen, die eine andere Mandanten-ID aufweisen.

RLS ist in T-SQL implementiert: Eine benutzerdefinierte Prädikatfunktion definiert die Filterlogik und eine Sicherheitsrichtlinie bindet diese Funktion an eine beliebige Anzahl von Tabellen. Für dieses Projekt stellt die Prädikatfunktion einfach sicher, dass die Anwendung (statt eines anderen SQL-Benutzers) mit der Datenbank verbunden wird und die Mandanten-ID einer bestimmten Zeile mit dem Wert der CONTEXT\_INFO übereinstimmt. Zeilen, die diese Bedingungen erfüllen, werden durch den Filter für SELECT-, UPDATE- und DELETE-Abfragen zugelassen. Wenn CONTEXT\_INFO noch nicht festgelegt wurde, werden keine Zeilen zurückgegeben.

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
		AND CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) = @TenantId -- @TenantId (int) is 4 bytes 
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [AZURE.TIP]Für komplexere Projekte, die die Prädikatfunktion in Hunderten von Tabellen hinzufügen müssen, können Sie eine gespeicherte Hilfsprozedur verwenden, die automatisch eine Sicherheitsrichtlinie generiert, die allen Tabellen in einem Schema ein Prädikat hinzufügt. Weitere Informationen finden Sie unter [Apply Row-Level Security to all tables – helper script (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script) (in englischer Sprache).

Wenn Sie später eine neue Tabelle hinzufügen, ändern Sie einfach die Sicherheitsrichtlinie und fügen ein Filterprädikat zur neuen Tabelle hinzu:

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable 
GO 
```

Wenn Sie die Beispielanwendung jetzt erneut ausführen, werden den Mandanten keine Zeilen angezeigt, die diesen nicht gehören.

### Hinzufügen von Überprüfungseinschränkungen, um INSERTs und UPDATEs falscher Mandanten zu blockieren.

Zurzeit hindern RLS-Sicherheitsrichtlinien die Anwendung nicht daran, versehentlich Zeilen für die falsche Mandanten-ID einzufügen oder die Mandanten-ID einer sichtbaren Zeile auf einen neuen Wert zu aktualisieren. Für einige Anwendungen, z. B. schreibgeschützte Berichts-Apps, ist dies kein Problem. Da diese Anwendung es allerdings Mandanten ermöglicht, neue Blogs einzufügen, lohnt es sich, einen zusätzlichen Schutz zu erstellen, der einen Fehler auslöst, wenn der Anwendungscode versucht, versehentlich Zeilen einzufügen oder zu aktualisieren, die gegen das Filterprädikat verstoßen. Wie unter [Row-Level Security: Blocking unauthorized INSERTs (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/23/row-level-security-blocking-unauthorized-inserts) (in englischer Sprache) beschrieben, empfiehlt es sich, eine Überprüfungseinschränkung für jede Tabelle zu erstellen, um die gleichen RLS-Filterprädikate für INSERT- und UPDATE-Vorgänge zu erzwingen.

Führen Sie zum Hinzufügen von Überprüfungseinschränkungen das folgende T-SQL-Skripts auf allen Shards mit SSMS, SSDT oder dem enthalten PowerShell-Skript (bzw. elastischen Datenbankaufträgen) aus, wie oben beschrieben:

```
-- Create a scalar version of the predicate function for use in check constraints 
CREATE FUNCTION rls.fn_tenantAccessPredicateScalar(@TenantId int)     
	RETURNS bit 
AS     
	BEGIN     
		IF EXISTS( SELECT 1 FROM rls.fn_tenantAccessPredicate(@TenantId) )         
			RETURN 1     
		RETURN 0 
	END 
GO 

-- Add the function as a check constraint on all sharded tables 
ALTER TABLE Blogs     
	WITH NOCHECK -- don't check data already in table     
	ADD CONSTRAINT chk_blocking_Blogs -- needs a unique name     
	CHECK( rls.fn_tenantAccessPredicateScalar(TenantId) = 1 ) 
GO

ALTER TABLE Posts     
	WITH NOCHECK     
	ADD CONSTRAINT chk_blocking_Posts     
	CHECK( rls.fn_tenantAccessPredicateScalar(TenantId) = 1 ) 
GO 
```

Die Anwendung kann jetzt keine Zeilen einfügen, die Mandanten gehören, die nicht derzeit mit der Shard-Datenbank verbunden sind. Ebenso kann die Anwendung keine sichtbaren Zeilen mit einer anderen Mandanten-ID aktualisieren. Wenn die Anwendung versucht, eine dieser Aktionen auszuführen, wird eine "DbUpdateException" ausgelöst.


### Hinzufügen von Standardeinschränkungen zum automatischen Auffüllen der Mandanten-ID für INSERTs. 

Neben der Verwendung von Überprüfungseinschränkungen zum Blockieren von falschen Mandanteneinfügungen können Sie eine Standardeinschränkung für jede Tabelle einsetzen, mit der die Mandanten-ID beim Einfügen von Zeilen automatisch mit dem aktuellen Wert von CONTEXT\_INFO aufgefüllt wird. Beispiel:

```
-- Create default constraints to auto-populate TenantId with the value of CONTEXT_INFO for inserts 
ALTER TABLE Blogs     
	ADD CONSTRAINT df_TenantId_Blogs      
	DEFAULT CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) FOR TenantId 
GO

ALTER TABLE Posts     
	ADD CONSTRAINT df_TenantId_Posts      
	DEFAULT CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) FOR TenantId 
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

> [AZURE.NOTE]Bei der Verwendung von Standardeinschränkungen für ein Entity Framework-Projekt wird empfohlen, dass Sie NICHT die Mandanten-ID-Spalte in Ihr EF-Datenmodell einbeziehen. Dies liegt daran, dass Entity Framework-Abfragen automatisch Standardwerte angeben, die die in T-SQL erstellten Standardeinschränkungen mit CONTEXT\_INFO außer Kraft setzen. Um Standardeinschränkungen im Beispielprojekt zu verwenden, sollten Sie z. B. "TenantId" (die Mandanten-ID) aus "DataClasses.cs" entfernen (und "Add-Migration" in der Paket-Manager-Konsole ausführen) sowie T-SQL verwenden, um sicherzustellen, dass das Feld nur in den Datenbanktabellen vorhanden ist. Auf diese Weise wird EF beim Einfügen von Daten nicht automatisch falsche Standardwerte übergeben.

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
            AND CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### Wartung 

* **Neue Shards hinzufügen**: Sie müssen das T-SQL-Skript zum Aktivieren von RLS (und zum Hinzufügen von Überprüfungseinschränkungen) auf allen neuen Shards ausführen, andernfalls werden Abfragen für diese Shards nicht gefiltert.

* **Neue Tabellen hinzufügen**: Sie müssen ein Filterprädikat zu den Sicherheitsrichtlinien aller Shards hinzufügen, wenn eine neue Tabelle erstellt wird, andernfalls werden Abfragen für die neue Tabelle nicht gefiltert. Dies kann mithilfe eines DDL-Triggers automatisiert werden, wie unter [Apply Row-Level Security automatically to newly created tables (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx) (in englischer Sprache) beschrieben.


## Zusammenfassung 

Elastische Datenbanktools und zeilenbasierte Sicherheit können zusammen zum horizontalen Skalieren der Datenebene einer Anwendung mit Unterstützung für Shards mit sowohl mehreren als einzelnen Mandanten verwendet werden. Mehrinstanzenfähige Shards können zum effizienten Speichern von Daten (insbesondere in Fällen, in denen eine große Anzahl von Mandanten über wenige Datenzeilen verfügt) verwendet werden, während Shards mit einzelnen Instanzen zur Unterstützung von Premium-Mandanten mit strengeren Anforderungen an Leistung und Isolation verwendet werden können. Weitere Informationen finden Sie unter [Elastische Datenbanktools – Dokumentationsübersicht ](sql-database-elastic-scale-documentation-map.md) oder der Referenz zur [zeilenbasierten Sicherheit (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/dn765131) auf MSDN.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->

<!---HONumber=Oct15_HO3-->