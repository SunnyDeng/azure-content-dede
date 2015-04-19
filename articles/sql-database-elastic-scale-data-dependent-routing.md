<properties 
	pageTitle="Datenabhängiges Routing:" 
	description="So verwenden Sie ShardMapManager für das datenabhängige Routing, einem Feature von Elastic Scale für Azure SQL-Datenbank" 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="stuartozer" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="stuarto"/>

# Datenabhängiges Routing

Die **ShardMapManager**-Klasse ermöglicht ADO.NET-Anwendungen die einfache Weiterleitung von Datenbankabfragen und Befehlen an die entsprechende physische Datenbank in einer Sharding-Umgebung. Hierbei spricht man von **datenabhängigem Routing**. Es stellt ein grundlegendes Muster bei der Arbeit mit Sharding Datenbanken dar. Jede spezifische Abfrage oder Transaktion in einer Anwendung, die datenabhängiges Routing verwendet, ist auf den Zugriff auf eine einzige Datenbank pro Anforderung beschränkt.  

Wenn datenabhängiges Routing verwendet wird, ist es nicht erforderlich, dass die Anwendung die einzelnen Verbindungszeichenfolgen oder Datenbankspeicherorte verfolgt, die den Slices von Daten in der Sharding-Umgebung zugeordnet sind. Stattdessen ist der [Shard Map Manager](sql-database-elastic-scale-shard-map-management.md) bei Bedarf für die Ausgabe offener Verbindungen an die richtige Datenbank verantwortlich, basierend auf den Daten in der Shard Map und dem Wert des Shardingschlüssels, der das Ziel der Anwendungsanforderung ist. (Dieser Schlüssel entspricht in der Regel der *customer_id*, *tenant_id*, *date_key* oder einer anderen bestimmten ID, die einen wesentlichen Parameter der Datenbankanforderung darstellt). 

## Verwenden eines ShardMapManager in einer datenabhängigen Routing-Anwendung 

Für Anwendungen, die datenabhängiges Routing verwenden, muss ein **ShardMapManager** einmal pro Anwendungsdomäne während der Initialisierung instanziiert werden. Dabei wird der Factoryaufruf **GetSQLShardMapManager** verwendet.

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

In diesem Beispiel werden sowohl ein **ShardMapManager** als auch eine spezifische, darin enthaltene **ShardMap** initialisiert. 

Für eine Anwendung, die die Shard Map nicht selbst bearbeitet, müssen die in der Factory-Methode verwendeten Anmeldeinformationen zum Abrufen von **ShardMapManager** (im obigen Beispiel *smmConnectionString*) den Anmeldeinformationen entsprechen, die nur schreibgeschützten Zugriff auf die **Global Shard Map**-Datenbank haben, auf die durch die Verbindungszeichenfolge verwiesen wird. Diese Anmeldeinformationen unterscheiden sich in der Regel von Anmeldeinformationen, die für offene Verbindungen zum Shard-Zuordnungs-Manager verwendet werden. Siehe auch [Verwalten von Anmeldeinformationen für Elastic Scale](sql-database-elastic-scale-manage-credentials.md). 

## Aufrufen von datenabhängigem Routing 

Die Methode **ShardMap.OpenConnectionForKey(key, connectionString, connectionOptions)** gibt eine ADO.Net-Verbindung zurück, die bereit für die Befehlsausgabe an die entsprechende Datenbank basierend auf dem Wert des **key**-Parameters ist. Sharding-Informationen werden in der Anwendung vom **ShardMapManager** zwischengespeichert. Daher erfordern diese Anforderungen in der Regel keine Datenbanksuche in der Datenbank **Global Shard Map**. 

* Der **key**-Parameter wird als Suchschlüssel in der Shard-Zuordnung verwendet, um die passende Datenbank für die Anforderung zu bestimmen. 

* **connectionString** wird verwendet, um nur die Benutzeranmeldeinformationen für die gewünschte Verbindung zu übergeben. In dieser *connectionString* ist kein Datenbankname oder Servername enthalten, da die Datenbank und der Server von der Methode anhand der **ShardMap** bestimmt werden. 

* Die **connectionOptions**-Enumeration wird verwendet, um anzugeben, ob beim Bereitstellen der offenen Verbindung eine Validierung erfolgt oder nicht. **ConnectionOptions.Validate** wird empfohlen. In Umgebungen, in denen Shard-Zuordnungen geändert werden und Zeilen infolge von Teilungs- oder Zusammenführungsvorgängen in andere Datenbanken verschoben werden können, gewährleistet die Validierung, dass die zwischengespeicherte Datenbanksuche, die auf einem Schlüsselwert basiert, noch richtig ist. Die Validierung umfasst eine kurze Abfrage der lokalen Shard-Zuordnung in der Zieldatenbank (nicht der globalen Shard-Zuordnung), bevor die Verbindung an die Anwendung bereitgestellt wird. 

Wenn die Validierung der lokalen Shard-Zuordnung fehlschlägt (was angibt, dass der Zwischenspeicher nicht korrekt ist), fragt der Shard-Zuordnungs-Manager die globale Shard-Zuordnung ab, um den neuen, richtigen Wert für die Suche zu erhalten, den Zwischenspeicher zu aktualisieren und die passende Datenbankverbindung zu erhalten und zurückzugeben. 

**ConnectionOptions.None** (nicht validieren) ist nur dann akzeptabel, wenn keine Shard-Zuordnungsänderungen erwartet werden, während eine Anwendung online ist. In diesem Fall wird davon ausgegangen, dass die zwischengespeicherten Werte immer korrekt sind, und der zusätzliche Lauf des Validierungsaufrufs der Zieldatenbank kann problemlos übersprungen werden. Dadurch lassen sich Transaktionswartezeiten und Datenbankverkehr reduzieren. Die **connectionOptions** können auch über einen Wert in einer Konfigurationsdatei festgelegt werden, um anzugeben, ob während eines Zeitraums Sharding-Änderungen erwartet werden oder nicht.  

Dies ist ein Beispiel für einen Code, der den Shard-Zuordnungs-Manager zum Durchführen von datenabhängigem Routing basierend auf dem Wert eines Ganzzahlschlüssels **CustomerID** verwendet. Dabei wird ein **ShardMap**-Objekt mit dem Namen **customerShardMap** verwendet.  

## Beispiel: Datenabhängiges Routing 

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connection to the shard for that customer ID
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

Beachten Sie, dass anstelle eines Konstruktors für eine **SqlConnection**, gefolgt von einem **Open()**-Aufruf an das Verbindungsobjekt, die **OpenConnectionForKey**-Methode verwendet wird. Diese stellt eine neue, bereits offene Verbindung an die richtige Datenbank bereit. Verbindungen, die auf diese Weise verwendet werden, nutzen weiterhin alle Vorteile des Verbindungspooling in ADO.Net. Solange Transaktionen und Anforderungen jeweils durch ein einzelnes Shard erfüllt werden können, sollte dies die einzige Änderung sein, die für eine Anwendung, die bereits ADO.Net verwendet, erforderlich ist. 

Die Methode **OpenConnectionForKeyAsync** ist auch verfügbar, wenn die Anwendung die asynchrone Programmierung mit ADO.Net verwendet.  Das Verhalten entspricht dem Äquivalent für das datenabhängige Routing der Methode **Connection.OpenAsync** von ADO.NET.

## Integration in die Behandlung vorübergehender Fehler 

Eine bewährte Methode bei der Entwicklung von Anwendungen für den Datenzugriff in der Cloud besteht darin, sicherzustellen, dass vorübergehende Fehler beim Verbinden mit oder Abfragen der Datenbank von der Anwendung abgefangen werden, und dass die Vorgänge mehrmals wiederholt werden, bevor ein Fehler ausgelöst wird. Die Behandlung vorübergehender Fehler in Cloudanwendungen wird in [Transient Fault Handling](http://msdn.microsoft.com/library/dn440719\(v=pandp.60\).aspx) besprochen. 
 
Die Behandlung vorübergehender Fehler kann natürlich zusammen mit dem datenabhängigen Routing verwendet werden. Die wichtigste Anforderung besteht in einer Wiederholung der gesamten Datenzugriffsanforderung, darunter des **using**-Blocks, der die datenabhängige Routingverbindung abgerufen hat. Das obige Beispiel könnte wie folgt umgeschrieben werden (beachten Sie die hervorgehobenen Änderungen). 

### Beispiel - Datenabhängiges Routing mit Behandlung vorübergehender Fehler 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() =&gt; </span> 
<span style="background-color:  #FFFF00">    { </span>
        // Connection to the shard for that customer ID 
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // Execute a simple command 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @&quot;UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID&quot;; 

            cmd.Parameters.AddWithValue(&quot;@customerID&quot;, customerId); 
            cmd.Parameters.AddWithValue(&quot;@newPersonID&quot;, newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine(&quot;Update completed&quot;); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


Pakete, die zum Implementierung der Behandlung vorübergehender Fehler erforderlich sind, werden automatisch heruntergeladen, wenn Sie die Elastic Scale Starter Kit-Anwendung erstellen. Die Pakete sind auch getrennt unter [Enterprise Library - Transient Fault Handling Application Block](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/) erhältlich. Verwenden Sie Version 6.0 oder höher. 

## Transaktionskonsistenz 

Transaktionseigenschaften werden für alle Vorgänge lokal auf einem Shard garantiert. So werden z. B. Transaktionen, die über datenabhängiges Routing gesandt wurden, innerhalb des Bereichs des Ziel-Shards für die Verbindung ausgeführt. Zu diesem Zeitpunkt sind keine Funktionen für mehrere Verbindungen in einer Transaktion vorgesehen. Daher besteht keine Transaktionsgarantie für Vorgänge, die über mehrere Shards hinweg ausgeführt werden.  

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--HONumber=47-->
