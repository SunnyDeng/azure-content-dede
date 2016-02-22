<properties 
	pageTitle="Datenabhängiges Routing | Microsoft Azure" 
	description="Erfahren Sie, wie Sie die ShardMapManager-Klasse in .NET-Apps für das datenabhängige Routing, einem Feature von elastischen Datenbanken für Azure SQL-Datenbank, verwenden können." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/04/2016" 
	ms.author="torsteng;sidneyh"/>

#Datenabhängiges Routing

**Datenabhängiges Routing** bezeichnet die Möglichkeit, die Daten in einer Abfrage dazu zu verwenden, die Anforderung an eine entsprechende Datenbank weiterzuleiten. Dieses stellt ein grundlegendes Muster bei der Arbeit mit horizontal partitionierten Datenbanken (Sharding) dar. Der Anforderungskontext kann auch zur Weiterleitung der Anforderung verwendet werden, insbesondere dann, wenn der Shardingschlüssel nicht Teil der Abfrage ist. Jede spezifische Abfrage oder Transaktion in einer Anwendung, die datenabhängiges Routing verwendet, ist auf den Zugriff auf eine Einzeldatenbank pro Anforderung beschränkt. Für die Tools für elastische SQL Azure-Datenbanken erfolgt dieses Routing mithilfe der **[ShardMapManager-Klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)** in ADO.NET-Anwendungen.

Es ist nicht erforderlich, dass die Anwendung die einzelnen Verbindungszeichenfolgen oder Datenbankspeicherorte verfolgt, die den Datenslices in der Shardingumgebung zugeordnet sind. Stattdessen öffnet bei Bedarf der [Shardzuordnungs-Manager](sql-database-elastic-scale-shard-map-management.md) basierend auf den Daten in der Shardzuordnung und dem Wert des Shardingschlüssels, der das Ziel der Anwendungsanforderung ist, Verbindungen zu den richtigen Datenbanken. (Bei diesem Schlüssel handelt es sich in der Regel um *customer\_id*, *tenant\_id*, *date\_key* oder einen anderen spezifischen Bezeichner, der ein grundlegender Parameter der Datenbankanforderung ist.)

Weitere Informationen finden Sie unter [Scaling Out SQL Server with Data Dependent Routing](https://technet.microsoft.com/library/cc966448.aspx) (Horizontales Hochskalieren von SQL Server mit datenabhängigem Routing).

## Herunterladen der Clientbibliothek

Installieren Sie zum Erhalt der Klasse die [Clientbibliothek für elastische Datenbanken](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## Verwenden eines ShardMapManager in einer datenabhängigen Routing-Anwendung 

Anwendungen sollten **ShardMapManager** während der Initialisierung instanziieren und dazu den Factoryaufruf **[GetSQLShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)** verwenden. In diesem Beispiel werden sowohl ein **ShardMapManager** als auch eine spezifische, darin enthaltene **ShardMap** initialisiert. Dieses Beispiel zeigt die Methoden GetSqlShardMapManager und [GetRangeShardMap](https://msdn.microsoft.com/library/azure/dn824173.aspx).

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

### Verwenden der Anmeldeinformationen mit den niedrigsten möglichen Rechten zum Abrufen der Shardzuordnung

Wenn eine Anwendung die Shardzuordnung selbst nicht ändert, sollten die in der Factorymethode verwendeten Anmeldeinformationen nur Leseberechtigungen für die Datenbank mit der **globalen Shardzuordnung** bereitstellen. Diese Anmeldeinformationen unterscheiden sich in der Regel von Anmeldeinformationen, die für offene Verbindungen zum Shard-Zuordnungs-Manager verwendet werden. Lesen Sie dazu auch [Anmeldeinformationen für den Zugriff auf die Clientbibliothek für elastische Datenbanken](sql-database-elastic-scale-manage-credentials.md).

## Aufrufen der OpenConnectionForKey-Methode

Die **[ShardMap.OpenConnectionForKey-Methode](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)** gibt eine ADO.NET-Verbindung zurück, die für die Befehlsausgabe an die entsprechende Datenbank basierend auf dem Wert des **key**-Parameters vorbereitet ist. Sharding-Informationen werden in der Anwendung vom **ShardMapManager** zwischengespeichert. Daher erfordern diese Anforderungen i. d. R. keine Datenbanksuche in der Datenbank mit der **globalen Shard-Zuordnung**.

	// Syntax: 
	public SqlConnection OpenConnectionForKey<TKey>(
		TKey key,
		string connectionString,
		ConnectionOptions options
	)


* Der **key**-Parameter wird als Suchschlüssel in der Shard-Zuordnung verwendet, um die passende Datenbank für die Anforderung zu bestimmen. 

* **connectionString** wird verwendet, um nur die Benutzeranmeldeinformationen für die gewünschte Verbindung zu übergeben. In dieser *connectionString* ist kein Datenbankname oder Servername enthalten, da die Datenbank und der Server von der Methode anhand der **ShardMap** bestimmt werden.

* Die **[connectionOptions](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx)** sollten auf **ConnectionOptions.Validate** festgelegt werden, wenn in der Umgebung die Shardzuordnungen geändert und Zeilen aufgrund von Teilungen oder Zusammenführungen in andere Datenbanken verschoben werden können. Dies umfasst eine kurze Abfrage der lokalen Shardzuordnung in der Zieldatenbank (nicht der globalen Shardzuordnung), bevor die Verbindung an die Anwendung bereitgestellt wird.

Wenn die Validierung der lokalen Shard-Zuordnung fehlschlägt (was angibt, dass der Zwischenspeicher nicht korrekt ist), fragt der Shard-Zuordnungs-Manager die globale Shard-Zuordnung ab, um den neuen, richtigen Wert für die Suche zu erhalten, den Zwischenspeicher zu aktualisieren und die passende Datenbankverbindung zu erhalten und zurückzugeben.

Verwenden Sie **ConnectionOptions.None** nur, wenn keine Shardzuordnungsänderungen erwartet werden, während eine Anwendung online ist. In diesem Fall wird davon ausgegangen, dass die zwischengespeicherten Werte immer korrekt sind, und der zusätzliche Lauf des Validierungsaufrufs der Zieldatenbank kann problemlos übersprungen werden. Dies reduziert den Datenverkehr der Datenbank. Die **connectionOptions** können auch über einen Wert in einer Konfigurationsdatei festgelegt werden, um anzugeben, ob während eines Zeitraums Sharding-Änderungen erwartet werden.

In diesem Beispiel wird der Wert des Ganzzahlschlüssels **CustomerID** zusammen mit dem **ShardMap**-Objekt **customerShardMap** verwendet.

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connect to the shard for that customer ID. No need to call a SqlConnection 
	// constructor followed by the Open method.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command. 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

Die **OpenConnectionForKey**-Methode gibt eine neue, bereits offene Verbindung mit der richtigen Datenbank zurück. Verbindungen, die auf diese Weise verwendet werden, nutzen weiterhin alle Vorteile des Verbindungspooling in ADO.Net. Solange Transaktionen und Anforderungen jeweils durch ein einzelnes Shard erfüllt werden können, sollte dies die einzige Änderung sein, die für eine Anwendung, die bereits ADO.Net verwendet, erforderlich ist.

Die **[OpenConnectionForKeyAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx)**-Methode ist auch verfügbar, wenn die Anwendung die asynchrone Programmierung mit ADO.NET verwendet. Das Verhalten entspricht dem datenabhängigen Routing der ****[Connection.OpenAsync](https://msdn.microsoft.com/library/hh223688(v=vs.110).aspx)**-Methode von ADO.NET.

## Integration in die Behandlung vorübergehender Fehler 

Eine bewährte Methode bei der Entwicklung von Anwendungen für den Datenzugriff in der Cloud besteht darin, sicherzustellen, dass vorübergehende Fehler von der App abgefangen werden und dass die Vorgänge mehrmals wiederholt werden, bevor ein Fehler ausgelöst wird. Die Behandlung vorübergehender Fehler in Cloudanwendungen wird in [Transient Fault Handling](https://msdn.microsoft.com/library/dn440719(v=pandp.60).aspx) (Behandeln vorübergehender Fehler) besprochen.
 
Die Behandlung vorübergehender Fehler kann natürlich zusammen mit dem datenabhängigen Routing verwendet werden. Die wichtigste Anforderung besteht in einer Wiederholung der gesamten Datenzugriffsanforderung, darunter des **using**-Blocks, der die datenabhängige Routingverbindung abgerufen hat. Das obige Beispiel könnte wie folgt umgeschrieben werden (beachten Sie die hervorgehobenen Änderungen).

### Beispiel – Datenabhängiges Routing mit Behandlung vorübergehender Fehler 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() => </span> 
<span style="background-color:  #FFFF00">    { </span>
        // Mit dem Shard für eine Kunden-ID verbinden 
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // Einen einfachen Befehl ausführen 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

            cmd.Parameters.AddWithValue("@customerID", customerId); 
            cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine("Update abgeschlossen"); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


Pakete, die zum Implementieren der Behandlung vorübergehender Fehler erforderlich sind, werden automatisch heruntergeladen, wenn Sie die Beispielanwendung für elastische Datenbanken erstellen. Die Pakete sind auch getrennt unter [Enterprise Library – Transient Fault Handling Application Block](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/) (in englischer Sprache) erhältlich. Verwenden Sie Version 6.0 oder höher.

## Transaktionskonsistenz 

Transaktionseigenschaften werden für alle Vorgänge lokal auf einem Shard garantiert. So werden z. B. Transaktionen, die über datenabhängiges Routing gesandt wurden, innerhalb des Bereichs des Ziel-Shards für die Verbindung ausgeführt. Zu diesem Zeitpunkt sind keine Funktionen für mehrere Verbindungen in einer Transaktion vorgesehen. Daher besteht keine Transaktionsgarantie für Vorgänge, die über mehrere Shards hinweg ausgeführt werden.

## Nächste Schritte
Weitere Informationen zum Trennen oder erneuten Anfügen eines Shards finden Sie unter [Verwenden der RecoveryManager-Klasse zur Behebung von Problemen mit der Shardzuordnung](sql-database-elastic-database-recovery-manager.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0211_2016-->