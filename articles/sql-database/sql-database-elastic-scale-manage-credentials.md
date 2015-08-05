<properties 
	pageTitle="Verwalten von Anmeldeinformationen in der Clientbibliothek für elastische Datenbanken" 
	description="So richten Sie Anmeldeinformationen auf der passenden Stufe, von Administrator bis schreibgeschützt, für Anwendungen für elastische Datenbanken ein" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="sidneyh"/>

# Verwalten von Anmeldeinformationen in der Clientbibliothek für elastische Datenbanken

Die [Clientbibliothek für elastische Datenbanken](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) verwendet Anmeldeinformationen für unterschiedliche Vorgangstypen, insbesondere für das Erstellen oder Ändern eines [Shard-Zuordnungs-Managers](sql-database-elastic-scale-shard-map-management.md), wobei auf einen vorhandenen Shard-Zuordnungs-Manager verwiesen wird, um Informationen zu Shards abzurufen und eine Verbindung zu Shards herzustellen. Anmeldeinformationen für diese Art von Vorgängen werden nachfolgend beschrieben.


* **Verwaltungsanmeldeinformationen für den Zugriff auf Shard-Zuordnungen**: Die Verwaltungsanmeldeinformationen werden beim Instanziieren eines **ShardMapManager**-Objekts für Anwendungen verwendet, die Shard-Zuordnungen ändern. Der Benutzer der Clientbibliothek für elastische Datenbanken muss die entsprechenden SQL-Benutzer und SQL-Anmeldungen erstellen und sicherstellen, dass diesen Lese-/Schreibberechtigungen für die globale Shard-Zuordnungsdatenbank und auch alle Shard-Datenbanken gewährt werden. Diese Anmeldeinformationen werden zum Verwalten der globalen und der lokalen Shard-Maps verwendet, wenn Änderungen an der Shard-Map vorgenommen werden. Verwenden Sie z. B. die Verwaltungsanmeldeinformationen, um das ShardMapManager-Objekt zu instanziieren, wie im folgenden Code dargestellt: 

        // Obtain a shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmAdminConnectionString, 
                ShardMapManagerLoadPolicy.Lazy 
        ); 


     Die Variable **smmAdminConnectionString** ist eine Verbindungszeichenfolge, die die Verwaltungsanmeldeinformationen enthält. Die Benutzer-ID und das Kennwort liefern Lese-/Schreibzugriff auf die Shared-Map-Datenbank sowie auf einzelne Shards. Die Verbindungszeichenfolge für die Verwaltung umfasst auch den Servernamen und den Datenbanknamen, um die globale Shard-Map-Datenbank zu identifizieren. Nachfolgend sehen Sie eine typische Verbindungszeichenfolge für diesen Zweck:

        "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

     Verwenden Sie die Benutzer-ID-Werte nicht im Format „Benutzername@Server“, sondern stattdessen einfach nur „Benutzername“. Der Hintergrund ist, dass die Anmeldeinformationen für den Zugriff sowohl auf die Shard Map Manager-Datenbank als auch auf einzelne Shards verwendet werden, die sich auf unterschiedlichen Servern befinden können.
     
* **Benutzeranmeldeinformationen für den Zugriff auf den Shard-Zuordnungs-Manager:** Beim Instanziieren des Shard-Zuordnungs-Managers in einer Anwendung, die keine Shard-Zuordnungen verwaltet, verwenden Sie Anmeldeinformationen, die schreibgeschützten Zugriff auf die globale Shard-Zuordnung haben. Die aus der globalen Shard-Zuordnung unter diesen Anmeldeinformationen abgerufenen Informationen werden für [datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md) und zur Auffüllung des Shard-Zuordnungscaches auf dem Client verwendet. Die Anmeldeinformationen werden über das gleiche Aufrufmuster wie für **GetSqlShardMapManager** bereitgestellt, das oben dargestellt ist:
 
        // Obtain shard map manager. 
        ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
                smmReadOnlyConnectionString, 
                ShardMapManagerLoadPolicy.Lazy
        );  

     Beachten Sie, dass **smmReadOnlyConnectionString** verwendet wird, um die Verwendung unterschiedlicher Anmeldeinformationen für diesen Zugriff für Benutzer **ohne Administratorrechte** darzustellen; diese Anmeldeinformationen dürfen keine Schreibberechtigungen für die globale Shard-Zuordnung bereitstellen.

* **Benutzeranmeldeinformationen für den Zugriff auf Benutzerdaten:** Bei Verwendung der **OpenConnectionForKey**-Methode sind zusätzliche Anmeldeinformationen erforderlich, um auf einen mit einem Schlüssel verbundenen Shard zuzugreifen. Diese Anmeldeinformationen müssen Berechtigungen für schreibgeschützten Zugriff auf die lokalen Shard-Map-Tabellen bereitstellen, die sich im Shard befinden. Dies ist erforderlich, um eine Verbindungsüberprüfung für datenabhängiges Routing im Shard durchzuführen. Der folgende Codeausschnitt veranschaulicht die Verwendung der Benutzeranmeldeinformationen für Zugriff auf Benutzerdaten im Kontext des datenabhängigen Routings:
 
        using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
        targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

    In diesem Beispiel enthält **smmUserConnectionString** die Verbindungszeichenfolge für die Benutzeranmeldeinformationen. Für die Azure SQL-Datenbank finden Sie nachfolgend eine typische Verbindungszeichenfolge für Benutzeranmeldeinformationen:

        "User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

    Genau so wie bei den Administratoranmeldeinformationen verwenden Sie keine Benutzer-ID-Werte im Format „Benutzername@Server“, sondern einfach „Benutzername“. Beachten Sie außerdem, dass die Verbindungszeichenfolge keinen Server- und Datenbanknamen enthält. Dies liegt daran, dass der **OpenConnectionForKey**-Aufruf die Verbindung basierend auf dem Schlüssel automatisch an den richtigen Shard leitet. Daher müssen der Datenbankname und der Servername nicht bereitgestellt werden.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=July15_HO4-->