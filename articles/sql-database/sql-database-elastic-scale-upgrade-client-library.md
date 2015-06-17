<properties 
	title="Upgrade to the Latest Elastic Scale Client Library" 
	pageTitle="Upgrade auf die neueste Elastic Scale-Clientbibliothek" 
	description="Upgradeanweisungen für PowerShell und C#" 
	metaKeywords="sharding,elastic scale, Azure SQL DB sharding" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="stuarto", "sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="stuarto" />

# Upgrade auf die neueste Elastic Scale-Clientbibliothek

Neue Versionen der Elastic Scale-Clientbibliothek stehen über [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) und die NuGetPackage Manager-Benutzeroberfläche in Visual Studio zur Verfügung. Upgrades bieten Fehlerkorrekturen und Unterstützung für die neuen Elastic Scale-Funktionen.

## Upgradeschritte

Für ein Upgrade müssen Sie die Anwendung mit der neuen Bibliothek neu erstellen und Ihre vorhandenen Shard Map Manager-Metadaten ändern, die in Ihren Azure SQL-Datenbanken gespeichert sind, damit neue Features unterstützt werden.

Befolgen Sie die nachstehenden Anweisungen zum Aktualisieren Ihrer Anwendungen, der Shard Map Manager-Datenbank und der lokalen Shard Map Manager-Metadaten für jeden Shard.  Durch Upgradeschritte in dieser Reihenfolge wird sichergestellt, dass alte Versionen der Clientbibliothek nicht mehr in Ihrer Umgebung vorhanden sind, wenn Metadatenobjekte aktualisiert werden. Dies bedeutet, dass Metadatenobjekte der alten Version nach dem Upgrade nicht mehr erstellt werden.   

**1. Aktualisieren Sie Ihre Anwendungen.** Laden Sie in Visual Studio die die neueste Version der Clientbibliothek herunter, und verweisen Sie in allen Ihren Elastic Scale-Projekten darauf. Führen Sie dann die Schritte zur Neuerstellung und Bereitstellung aus. 

 * Wählen Sie in Ihrer Visual Studio-Projektmappe **Extras** --> **NuGet Package Manager** -->**NuGet-Pakete für die Lösung verwalten** aus. 
 * Wählen Sie im linken Bereich **Updates** aus, und klicken Sie dann auf Schaltfläche **Aktualisieren** für das Paket **Elastic Scale-Clientbibliothek für Azure SQL-Datenbank**, das im Fenster angezeigt wird.
	![Upgrade Nuget Pacakges][1]
 
 * Führen Sie die Schritte zur Erstellung und Bereitstellung aus. 

**2. Aktualisieren Sie Ihre Skripts.** Laden Sie bei Verwenden von **PowerShell**-Skripts zum Verwalten von Shards [die neue Bibliotheksversion herunter](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), und kopieren Sie sie in das Verzeichnis, in dem Sie Skripts ausführen. 

**3. Aktualisieren Sie den Split-Merge-Dienst.** Laden Sie bei Verwenden des Elastic Scale Split-Merge-Diensts zum Reorganisieren von Shardingdaten [die neueste Version des Diensts herunter, und stellen Sie sie bereit](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Detaillierte Upgradeschritte für den Dienst finden Sie [hier](http://azure.microsoft.com/documentation/articles/sql-database-elastic-scale-overview-split-and-merge/). 

**4. Aktualisieren Sie Ihre Shard Map Manager-Datenbanken**. Aktualisieren Sie die Metadaten, die Ihre Shard Maps in der Azure SQL-Datenbank unterstützen.  Es gibt hierfür zwei Möglichkeiten: PowerShell oder C#. Beide Optionen werden nachstehend vorgestellt.

***Option 1: Aktualisieren von Metadaten mithilfe von PowerShell***

1. Laden Sie das neueste Clusterbefehlszeilentool für NuGet [hier](http://nuget.org/nuget.exe) herunter, und speichern Sie es in einem Ordner. 

2. Öffnen Sie eine Eingabeaufforderung, navigieren Sie zum selben Ordner, und geben Sie folgenden Befehl ein:
`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. Navigieren Sie zum Unterordner mit der neuen Client-DLL-Version, die Sie zuvor heruntergeladen haben. Beispiel:
`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.0.8.0\lib\net45`

4. Laden Sie das Scriptlet für das Elastic Scale-Clientupgrade aus dem [Script Center](http://go.microsoft.com/?linkid=9876343) herunter, und speichern Sie es im Ordner, der bereits die DLL enthält.

5. Führen Sie in diesem Ordner an der Befehlszeile "PowerShell.\upgrade.ps1" aus, und folgen Sie den Anweisungen.
 
***Option 2: Aktualisieren von Metadaten mithilfe von C#***

Erstellen Sie alternativ eine Visual Studio-Anwendung, die Ihren Shard Map Manager öffnet, alle Shards durchläuft und die Aktualisierung der Metadaten durch Aufrufen der Methoden [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) und [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) wie im folgenden Beispiel ausführt: 

	ShardMapManager smm =
	   ShardMapManagerFactory.GetSqlShardMapManager
	   (connStr, ShardMapManagerLoadPolicy.Lazy); 
	smm.UpgradeGlobalStore(); 
	
	foreach (ShardLocation loc in
	 smm.GetDistinctShardLocations()) 
	{   
	   smm.UpgradeLocalStore(loc); 
	} 

Diese Techniken für die Metadatenaktualisierung können ohne Probleme mehrmals angewendet werden. Wenn beispielsweise eine ältere Clientversion versehentlich einen Shard erstellt, den Sie bereits aktualisiert haben, können Sie die Aktualisierung für alle Shards wiederholen, um sicherzustellen, dass die neueste Metadatenversion in der gesamten Infrastruktur vorhanden ist. 

**Hinweis:** Bis dato veröffentlichte neue Versionen der Clientbibliothek funktionieren weiterhin mit früheren Versionen der Shard Map Manager-Metadaten für Azure SQL-Datenbank und umgekehrt. Doch um einige der neuen Features im neuesten Client nutzen zu können, müssen Metadaten aktualisiert werden muss. Beachten Sie, dass Aktualisierungen von Metadaten keine benutzer- oder anwendungsspezifische Daten betreffen, sondern nur Objekte, die von Shard Map Manager erstellt und verwendet werden. Zudem setzen Anwendungen ihren Betrieb während der zuvor beschriebenen Upgradesequenz fort.

## Elastic Scale-Client - Versionsverlauf 

**Version 0.8 - März 2015**

* Unterstützung von Asynchronität für datenabhängiges Routing mit den neuen "ShardMap.OpenConnectionForKeyAsync"-Methoden hinzugefügt 
* "Public KeyType"-Eigenschaft zu ShardMap hinzugefügt 
* Verbesserungen zur Unterstützung von Szenarien für Datenbank- und Notfallwiederherstellung hinzugefügt 

**Version 0.7 - Oktober 2014**

Erste Vorschauversion 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]  


<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png

<!--HONumber=47-->
 