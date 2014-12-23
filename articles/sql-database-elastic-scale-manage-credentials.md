<properties title="Managing Elastic Scale Credentials" pageTitle="Verwalten von Anmeldeinformationen für Elastic Scale" description="How to set the right level of credentials, admin to read-only, for Elastic Scale apps." metaKeywords="Azure SQL Database, elastic scale, about user credentials in elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Verwalten von Anmeldeinformationen für Elastic Scale  

Die [Elastic Scale-Client-APIs](http://go.microsoft.com/?linkid=9862605) verwenden Anmeldeinformationen für unterschiedliche Vorgangstypen, insbesondere für das Erstellen oder Ändern eines [Shard-Map-Managers](http://go.microsoft.com/?linkid=9862595), wobei auf einen vorhandenen Shard-Map-Manager verwiesen wird, um Informationen zu Shards abzurufen und eine Verbindung zu Shards herzustellen. Anmeldeinformationen für diese Art von Vorgängen werden nachfolgend beschrieben. 


* **Verwaltungsanmeldeinformationen für Zugriff auf Shard-Maps**: Die Verwaltungsanmeldeinformationen werden beim Instanziieren eines **ShardMapManager**-Objekts für Anwendungen verwendet, die Shard-Maps ändern. Der Benutzer der Elastic Scale-APIs muss die entsprechenden SQL-Benutzer und SQL-Anmeldungen erstellen und sicherstellen, dass diesen Lese-/Schreibberechtigungen für die globale Shard-Map-Datenbank und auch alle Shard-Datenbanken gewährt werden. Diese Anmeldeinformationen werden zum Verwalten der globalen und der lokalen Shard-Maps verwendet, wenn Änderungen an der Shard-Map vorgenommen werden. Verwenden Sie z. B. die Verwaltungsanmeldeinformationen, um das ShardMapManager-Objekt zu instanziieren, wie im folgenden Code dargestellt: 

        // Obtain a shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmAdminConnectionString, 
                ShardMapManagerLoadPolicy.Lazy 
        ); 


Die Variable **smmAdminConnectionString** ist eine Verbindungszeichenfolge, die die Verwaltungsanmeldeinformationen enthält. Die Benutzer-ID und das Kennwort liefern Lese-/Schreibzugriff auf die Shared-Map-Datenbank sowie auf einzelne Shards. Die Verbindungszeichenfolge für die Verwaltung umfasst auch den Servernamen und den Datenbanknamen, um die globale Shard-Map-Datenbank zu identifizieren. Nachfolgend sehen Sie eine typische Verbindungszeichenfolge für diesen Zweck:

        "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" 

* **Benutzeranmeldeinformationen für Zugriff auf Shard-Map-Manager**:  Beim Instanziieren des Shard-Map-Managers in einer Anwendung, die keine Shard-Maps verwaltet, verwenden Sie Anmeldeinformationen, die schreibgeschützten Zugriff auf die globale Shard-Map haben. Die aus der globalen Shard-Map unter diesen Anmeldeinformationen abgerufenen Informationen werden für [datenabhängiges Routing](./sql-database-elastic-scale-data-dependent-routing.md) und zur Auffüllung des Shard-Map-Caches auf dem Client verwendet. Die Anmeldeinformationen werden über das gleiche Aufrufmuster wie für **GetSqlShardMapManager** bereitgestellt, wie oben dargestellt: 
 
        // Obtain shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmReadOnlyConnectionString, 
                ShardMapManagerLoadPolicy.Lazy
        );  

Beachten Sie, dass **smmReadOnlyConnectionString** verwendet wird, um die Verwendung unterschiedlicher Anmeldeinformationen für diesen Zugriff für Benutzer **ohne Administratorrechte** darzustellen; diese Anmeldeinformationen dürfen keine Schreibberechtigungen für die globale Shard-Map bereitstellen. 

* **Benutzeranmeldeinformationen für Zugriff auf Benutzerdaten**: Bei Verwendung der **OpenConnectionForKey**-Methode sind zusätzliche Anmeldeinformationen erforderlich, um auf ein mit einem Schlüssel verbundenes Shard zuzugreifen. Diese Anmeldeinformationen müssen Berechtigungen für schreibgeschützten Zugriff auf die lokalen Shard-Map-Tabellen bereitstellen, die sich im Shard befinden. Dies ist erforderlich, um eine Verbindungsüberprüfung für datenabhängiges Routing im Shard durchzuführen. Der folgende Codeausschnitt veranschaulicht die Verwendung der Benutzeranmeldeinformationen für Zugriff auf Benutzerdaten im Kontext des datenabhängigen Routings: 
 
        using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
        targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

In diesem Beispiel enthält **smmUserConnectionString** die Verbindungszeichenfolge für die Benutzeranmeldeinformationen. Für die Azure SQL-Datenbank finden Sie nachfolgend eine typische Verbindungszeichenfolge für Benutzeranmeldeinformationen: 

        "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;"  

Beachten Sie, dass die Verbindungszeichenfolge keinen Server- und Datenbanknamen enthält. Dies liegt daran, dass der **OpenConnectionForKey**-Aufruf die Verbindung basierend auf dem Schlüssel automatisch an das richtige Shard leitet. Daher müssen der Datenbankname und der Servername nicht bereitgestellt werden. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
