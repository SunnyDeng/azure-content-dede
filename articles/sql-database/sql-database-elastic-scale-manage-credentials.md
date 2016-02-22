<properties 
	pageTitle="Verwalten von Anmeldeinformationen in der Clientbibliothek für elastische Datenbanken | Microsoft Azure" 
	description="So richten Sie Anmeldeinformationen mit den passenden Berechtigungen (von Administrator- bis Leseberechtigungen) für Apps für elastische Datenbanken ein" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="ddove" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/04/2016" 
	ms.author="ddove;sidneyh"/>

# Anmeldeinformationen für den Zugriff auf die Clientbibliothek für elastische Datenbanken

Die [Clientbibliothek für elastische Datenbanken](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) verwendet drei verschiedene Arten von Anmeldeinformationen für den Zugriff auf den [Shardzuordnungs-Manager](sql-database-elastic-scale-shard-map-management.md). Verwenden Sie jeweils die Anmeldeinformationen mit den geringstmöglichen Zugriffsrechten.

* **Verwaltungsanmeldeinformationen**: Zum Erstellen oder Bearbeiten eines Shard-Zuordnungs-Managers. (Siehe [Glossar](sql-database-elastic-scale-glossary.md)) 
* **Zugriffsanmeldeinformationen**: Zum Zugreifen auf einen vorhandenen Shard-Zuordnungs-Manager zum Abrufen von Informationen zu Shards
* **Verbindungsanmeldeinformationen**: Zum Herstellen einer Verbindung mit Shards 

Informationen finden Sie auch unter [Verwalten von Datenbanken und Anmeldungen in Azure SQL-Datenbank](sql-database-manage-logins.md).
 
## Informationen zu Anmeldeinformationen

Die Verwaltungsanmeldeinformationen werden zum Erstellen eines [**ShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)-Objekts für Anwendungen verwendet, die Shard-Zuordnungen ändern. (Ein Beispiel finden Sie unter [Hinzufügen von Shards mit den Tools für elastische Datenbanken](sql-database-elastic-scale-add-a-shard.md) und [Datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md).) Der Benutzer der Clientbibliothek für elastische Datenbanken erstellt die SQL-Benutzer und SQL-Anmeldungen und stellt sicher, dass diesen Lese-/Schreibberechtigungen für die globale Shardzuordnungs-Datenbank und auch alle Sharddatenbanken gewährt werden. Diese Anmeldeinformationen werden zum Verwalten der globalen und der lokalen Shard-Maps verwendet, wenn Änderungen an der Shard-Map vorgenommen werden. Verwenden Sie beispielsweise die Verwaltungsanmeldeinformationen, um das Objekt für den Shard-Zuordnungs-Manager zu erstellen (mit [**GetSqlShardMapManager**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)):

	// Obtain a shard map manager. 
	ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
	        smmAdminConnectionString, 
	        ShardMapManagerLoadPolicy.Lazy 
	); 

Die Variable **smmAdminConnectionString** ist eine Verbindungszeichenfolge, die die Verwaltungsanmeldeinformationen enthält. Die Benutzer-ID und das Kennwort liefern Lese-/Schreibzugriff auf die Shared-Map-Datenbank sowie auf einzelne Shards. Die Verbindungszeichenfolge für die Verwaltung umfasst auch den Servernamen und den Datenbanknamen, um die globale Shard-Map-Datenbank zu identifizieren. Nachfolgend sehen Sie eine typische Verbindungszeichenfolge für diesen Zweck:

	 "Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;” 

Verwenden Sie die Werte nicht im Format „Benutzername@Server“, sondern stattdessen einfach nur den Wert „Benutzername“. Der Hintergrund ist, dass die Anmeldeinformationen für den Zugriff sowohl auf die Shard-Zuordnungs-Manager-Datenbank als auch auf einzelne Shards verwendet werden, die sich auf unterschiedlichen Servern befinden können.

## Zugriffsanmeldeinformationen
  
Beim Erstellen eines Shard-Zuordnungs-Managers in einer Anwendung, die keine Shard-Zuordnungen verwaltet, verwenden Sie Anmeldeinformationen, die Leseberechtigungen für die globale Shard-Zuordnung besitzen. Die aus der globalen Shard-Zuordnung unter diesen Anmeldeinformationen abgerufenen Informationen werden für [datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md) und zur Auffüllung des Shard-Zuordnungscaches auf dem Client verwendet. Die Anmeldeinformationen werden über das gleiche Aufrufmuster wie für **GetSqlShardMapManager** bereitgestellt, das oben dargestellt ist:

    // Obtain shard map manager. 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager( 
            smmReadOnlyConnectionString, 
            ShardMapManagerLoadPolicy.Lazy
    );  

Beachten Sie, dass **smmReadOnlyConnectionString** verwendet wird, um die Verwendung unterschiedlicher Anmeldeinformationen für diesen Zugriff für Benutzer **ohne Administratorrechte** darzustellen. Diese Anmeldeinformationen dürfen keine Schreibberechtigungen für die globale Shard-Zuordnung bereitstellen.

## Verbindungsanmeldeinformationen 

Bei Verwendung der [**OpenConnectionForKey**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)-Methode sind zusätzliche Anmeldeinformationen erforderlich, um auf ein mit einem Sharding-Schlüssel verbundenes Shard zuzugreifen. Diese Anmeldeinformationen müssen Berechtigungen für schreibgeschützten Zugriff auf die lokalen Shard-Map-Tabellen bereitstellen, die sich im Shard befinden. Dies ist erforderlich, um eine Verbindungsüberprüfung für datenabhängiges Routing im Shard durchzuführen. Dieser Codeausschnitt ermöglicht den Datenzugriff im Kontext des datenabhängigen Routings:
 
	using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>( 
	targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate)) 

In diesem Beispiel enthält **smmUserConnectionString** die Verbindungszeichenfolge für die Benutzeranmeldeinformationen. Für die Azure SQL-Datenbank finden Sie nachfolgend eine typische Verbindungszeichenfolge für Benutzeranmeldeinformationen:

	"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  

Genau wie bei den Administratoranmeldeinformationen verwenden Sie keine Werte im Format „Benutzername@Server“. Verwenden Sie stattdessen einfach „Benutzername“. Beachten Sie außerdem, dass die Verbindungszeichenfolge keinen Server- und Datenbanknamen enthält. Dies liegt daran, dass der **OpenConnectionForKey**-Aufruf die Verbindung basierend auf dem Schlüssel automatisch an den richtigen Shard leitet. Daher werden der Datenbankname und der Servername nicht bereitgestellt.

## Weitere Informationen
[Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank](sql-database-manage-logins.md)

[Sichern der SQL-Datenbank](sql-database-security.md)

[Erste Schritte mit Aufträgen für die elastische Datenbank](sql-database-elastic-jobs-getting-started.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0211_2016-->