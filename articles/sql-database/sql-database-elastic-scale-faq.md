<properties 
	pageTitle="FAQ zu elastischen Skalierung von Azure SQL" 
	description="Häufig gestellte Fragen zur Elastic Scale-Funktion der Azure SQL-Datenbank." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="ddove" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="ddove@microsoft.com"/>

# FAQ zu elastischen Skalierung der Azure SQL-Datenbank 

#### Wie wird der Shardingschlüssel für die Schemainformationen mit Daten aufgefüllt, wenn eine Einzelinstanz pro Shard und kein Shardingschlüssel verwendet wird?
Das Schemainformationenobjekt wird zum Aufteilen von Zusammenführungsszenarien verwendet. Wenn eine Anwendung prinzipiell eine Einzelinstanz darstellt, ist der Aufteilungs-/Zusammenführungsdienst nicht erforderlich. Daher besteht auch keine Anforderung, das Schemainformationsobjekt mit Daten aufzufüllen.

#### Ich habe eine Datenbank bereitgestellt und verfüge bereits über einen Shard-Zuordnungs-Manager. Wie registriere ich diese neue Datenbank als Shard?
Bitte lesen Sie **[Hinzufügen eines Shards zu einer Elastic Scale-Anwendung](http://azure.microsoft.com/documentation/articles/sql-database-elastic-scale-add-a-shard/)**. 

#### Wie viel kostet elastische Skalierung?
Für die Verwendung der Bibliothek für elastische Skalierung fallen keine Kosten an. Es entstehen nur Kosten für die Azure SQL-Datenbanken, die Sie für Shards verwenden, für den Shard-Zuordnungs-Manager sowie für die Web-/Workerrollen, die für den Aufteilungs-/Zusammenführungsdienst bereitgestellt werden.

#### Warum funktionieren meine Anmeldeinformationen nicht, wenn ich einen Shard von einem anderen Server hinzufüge?
Verwenden Sie keine Anmeldeinformationen im Format "Benutzer-ID=benutzername@servername", sondern einfach "Benutzer-ID = benutzername".  Stellen Sie außerdem sicher, dass die Anmeldung "benutzername" über Berechtigungen für den Shard verfügt.

#### Muss ich bei jedem Starten meiner Anwendung einen Shard-Zuordnungs-Manager erstellen und Shards mit Daten auffüllen?
Nein. Das Erstellen eines Shard-Zuordnungs-Managers (Beispiel: **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) ist ein einmaliger Vorgang.  Ihre Anwendung sollte den Aufruf **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** beim Start der Anwendung verwenden.  Pro Anwendungsdomäne sollte nur ein solcher Aufruf erfolgen.

#### Ich habe Fragen zur Verwendung von elastischer Skalierung. Wie erhalte ich Antworten? 
Wenden Sie sich bitte im [Azure SQL-Datenbankforum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) an uns.

#### Wenn ich eine Datenbankverbindung mithilfe eines Shardingschlüssels abrufe, kann ich weiterhin Daten für andere Shardingschlüssel auf dem gleichen Shard abfragen.  Ist dies beabsichtigt?
Die APIs für elastische Skalierung stellen Ihnen eine Verbindung mit der richtigen Datenbank für Ihren Shardingschlüssel zur Verfügung, jedoch keine Filterung von Shardingschlüsseln.  Führen Sie Ihrer Abfrage bei Bedarf **WHERE**-Klauseln hinzu, um den Bereich für den bereitgestellten Shardingschlüssel einzuschränken.

#### Kann ich eine andere Azure-Datenbankedition für jeden Shard in meiner Shardsammlung verwenden?
Ja, das ist möglich. Ein Shard ist eine einzelne Datenbank, daher kann ein Shard z. B. eine Premium-Edition verwenden, ein anderer eine Standard-Edition. Außerdem kann die Edition eines Shards während der Lebensdauer eines Shards mehrmals horizontal oder vertikal skaliert werden.

#### Stellt der Aufteilungs-/Zusammenführungsdienst eine Datenbank während eines Aufteilungs- oder Zusammenführungsvorgangs bereit bzw. löscht sie? 
Nein. Bei **split**-Vorgängen muss die Zieldatenbank mit dem entsprechenden Schema vorhanden und beim Shard-Zuordnungs-Manager registriert sein.  Für **merge**-Vorgänge müssen Sie den Shard aus dem Shard-Zuordnungs-Manager löschen und dann die Datenbank löschen.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--HONumber=47-->
 