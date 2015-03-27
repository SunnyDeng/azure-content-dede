<properties 
	pageTitle="Verwenden von Elastic Scale mit Dapper" 
	description="Verwenden von Elastic Scale mit Dapper" 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="torsteng"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/01/2015" 
	ms.author="torsteng@microsoft.com"/>

# Verwenden von Elastic Scale mit Dapper 

Dieses Dokument richtet sich an Entwickler, die Anwendungen mithilfe von Dapper erstellen, gleichzeitig aber auch [Elastic Scale von Azure SQL-Datenbank](http://azure.microsoft.com/documentation/articles/sql-database-elastic-scale-introduction/) nutzen möchten, um die Kapazität durch Sharding und das horizontale Skalieren ihrer Anwendungen flexibel zu vergrößern und verringern. Dieses Dokument veranschaulicht, welche Änderungen in Dapper-basierten Anwendungen erforderlich sind, um aktuelle Elastic Scale-Funktionen zu integrieren. Wir konzentrieren uns darauf, die Elastic Scale-Shardverwaltung und das datenabhängige Routing in Dapper zu integrieren. 

**Beispielcode**: [Elastic Scale mit Azure SQL-Datenbank - Integration in Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).
 
Die Integration von **Dapper** und **DapperExtensions** mit Elastic Scale für die Azure SQL-Datenbank ist denkbar einfach. Für Ihre Anwendungen können Sie das datenabhängige Routing von Elastic Scale verwenden, indem Sie die Erstellung und das Öffnen neuer [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx)-Objekte so modifizieren, dass der [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx)-Aufruf aus der [Elastic Scale- Bibliothek](http://msdn.microsoft.com/library/azure/dn765902.aspx) verwendet wird. Dadurch werden die Änderungen in der Anwendung auf Situationen beschränkt, in denen neue Verbindungen erstellt und geöffnet werden. 

## Übersicht über Dapper
**Dapper** ist ein objektrelationaler Mapper. Er ordnet .NET-Objekte aus Ihrer Anwendung einer relationalen Datenbank (und umgekehrt) zu. Der erste Teil des Beispielcodes veranschaulicht, wie Sie die Elastic Scale-Funktion von Azure SQL-Datenbank in Dapper-basierte Anwendungen integrieren können. Im zweiten Teil des Beispielcodes erfahren Sie dann, wie Elastic Scale integriert wird, wenn Sie sowohl Dapper als auch DapperExtensions verwenden. 

Die Mapper-Funktion in Dapper stellt Erweiterungsmethoden für Datenbankverbindungen bereit, die die Übermittlung von T-SQL-Anweisungen zur Ausführung oder Abfrage der Datenbank vereinfacht. Dapper erleichtert z. B. die Zuordnung zwischen .NET-Objekten und den SQL-Anweisungsparametern für **Execute**-Aufrufe. Außerdem können Sie **Query**-Aufrufe in Dapper verwenden, um die Ergebnisse Ihrer SQL-Abfragen in .NET-Objekte zu übernehmen. 

Bei Verwendung von DapperExtensions müssen keine SQL-Anweisungen mehr angegeben werden. Durch die Übermittlung von Erweiterungsmethoden wie **GetList** oder **Insert** über die Datenbankverbindung werden SQL-Anweisungen im Hintergrund erstellt.
 
Ein weiterer Vorteil von Dapper und DapperExtensions besteht darin, dass die Anwendung die Herstellung der Datenbankverbindung steuert. Dies erleichtert die Interaktion mit der Elastic Scale-Funktion, die Datenbankverbindungen basierend auf der Zuordnung von Shardlets und Datenbanken vermittelt.

Wie Sie die Dapper-Assemblys erhalten, erfahren Sie unter [Dapper .NET](http://www.nuget.org/packages/Dapper/). Informationen zu Dapper-Erweiterungen finden Sie unter [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## Ein kurzer Blick auf Elastic Scale

Mit Elastic Scale für Azure SQL-Datenbank definieren Sie Partitionen Ihrer Anwendungsdaten, die so genannten *shardlets*, ordnen sie Datenbanken zu und identifizieren sie nach *sharding keys*. Sie können über beliebig viele Datenbanken verfügen und die Shardlets auf diese Datenbanken verteilen. Die Zuordnung der Sharding-Schlüsselwerte zu den Datenbanken wird in einer Shard-Zuordnung gespeichert, die durch die Elastic Scale-APIs bereitgestellt wird. Diese Funktion wird als **Shard Map Management** bezeichnet. Die Shard-Zuordnung fungiert auch als Broker von Datenbankverbindungen für Anforderungen, die einen Sharding-Schlüssel enthalten. Wir bezeichnen diese Funktion als **datenabhängiges Routing**.

![Shard maps and data dependent routing][1]

Der Shard Map Manager in Elastic Scale schützt den Benutzer vor inkonsistenten Einblicken in Shardlet-Daten, die auftreten können, wenn gleichzeitige Shardlet-Verwaltungsvorgänge für die Datenbanken ausgeführt werden. Dazu fungieren die Shard-Zuordnungen in Elastic Scale als Broker der Datenbankverbindungen für eine Elastic Scale-Anwendung. Wenn sich Shard-Verwaltungsvorgänge auf das Shardlet auswirken, kann es passieren, dass Datenbankverbindungen durch die Shard Map-Funktionalität automatisch beendet werden. 

Anstatt Dapper-Verbindungen auf die herkömmliche Weise zu erstellen, müssen wir die [OpenConnectionForKey-Methode](http://msdn.microsoft.com/library/azure/dn824099.aspx) verwenden. Dadurch wird sichergestellt, dass alle Überprüfungen stattfinden und Verbindungen bei Datenverschiebungen zwischen Shards ordnungsgemäß verwaltet werden.

### Anforderungen an die Dapper-Integration

Bei Verwendung sowohl von Elastic Scale- als auch von Dapper-APIs sollten die folgenden Eigenschaften beibehalten werden:

* **Horizontale Skalierung**: Das Hinzufügen oder Entfernen von Datenbanken zu bzw. aus der Datenbankebene der partitionierten Anwendung entsprechend den Kapazitätsanforderungen der Anwendung. 
-    **Konsistenz**: Da unsere Anwendung mithilfe von Sharding horizontal skaliert wird, müssen wir datenabhängiges Routing verwenden. Wir verwenden zu diesem Zweck das datenabhängige Routing von Elastic Scale. Vor allem möchten wir die Überprüfungs- und Konsistenzzusicherungen der Verbindungen beibehalten, die über den Shard Map Manager von Elastic Scale vermittelt werden. Dies hilft uns, Datenverfälschung oder falsche Abfrageergebnisse zu vermeiden. Dadurch wird sichergestellt, dass Verbindungen mit einem bestimmten Shardlet zurückgewiesen oder unterbrochen werden, wenn das Shardlet z. B. gerade mithilfe von Split/Merge-APIs auf einen anderen Shard verschoben wird.
-    **Objektzuordnung**: Wir möchten die programmierfreundlichen Dapper-Zuordnungen beibehalten, um Code zwischen Klassen in der Anwendung und den zugrunde liegenden Datenbankstrukturen zu übersetzen. 

Der folgende Abschnitt enthält eine Anleitung, wie Sie diese Anforderungen für Anwendungen auf Grundlage von **Dapper** und **DapperExtensions** erfüllen.

## Technische Anleitung
### Datenabhängiges Routing mit Dapper 

Bei Dapper ist die Anwendung normalerweise dafür verantwortlich, die Verbindungen mit der zugrunde liegenden Datenbank zu erstellen und zu öffnen. Wenn von der Anwendung ein T-Typ angegeben wird, gibt Dapper Abfrageergebnisse als .NET-Auflistungen des T-Typs zurück. Dapper ordnet die T-SQL-Ergebniszeilen Objekten des T-Typs zu. Bei DML (Data Manipulation Language)-Anweisungen geht Dapper ähnlich vor, indem er .NET-Objekte den SQL-Werten oder -Parametern zuordnet. Dapper stellt diese Funktionalität über Erweiterungsmethoden des herkömmlichen [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) -Objekts aus den ADO .NET SQL-Clientbibliotheken bereit. Die von den Elastic Scale-APIs für DDR zurückgegebene SQL-Verbindung entspricht ebenfalls einem herkömmlichen [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx)-Objekt. Dadurch können wir Dapper- Erweiterungen direkt über den von der Elastic Scale-DDR-API zurückgegebenen Typ verwenden, da es sich ebenfalls um eine einfache SQL-Clientverbindung handelt.

Diese Beobachtungen erleichtern die Verwendung von Verbindungen, die von Elastic Scale für Dapper vermittelt werden. 

Dieses Codebeispiel (aus dem zugehörigen Beispiel) veranschaulicht eine Vorgehensweise, bei der der Shardingschlüssel der Elastic Scale-Bibliothek von der Anwendung bereitgestellt wird, um die Verbindung an den richtigen Shard zu vermitteln.  

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

Der Aufruf der [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx)-API ersetzt das standardmäßige Erstellen und Öffnen einer SQL-Clientverbindung. Der [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx)-Aufruf akzeptiert die Argumente, die für das datenabhängige Routing über Elastic Scale erforderlich sind: 

-    Die Shard Map für den Zugriff auf Schnittstellen für das datenabhängige Routing
-    Der Shardingschlüssel zum Identifizieren des Shardlets
-    Die Anmeldeinformationen (Benutzername und Kennwort) für die Verbindung mit dem Shard

Das Shard Map-Objekt erstellt eine Verbindung mit dem Shard, der das Shardlet für den jeweiligen Shardingschlüssel enthält. Die Verbindung wird von den Elastic Scale-APIs außerdem markiert, um die Konsistenzzusicherungen zu implementieren. Da der Aufruf von [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) ein normales SQL- Clientverbindungsobjekt zurückgibt, folgt der nachfolgende Aufruf der **Execute**-Erweiterungsmethode von Dapper der üblichen Verfahrensweise in Dapper.

Abfragen funktionieren ganz ähnlich. Sie öffnen zuerst die Verbindung mit [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) aus der Elastic Scale-API. Anschließend verwenden Sie die regulären Dapper-Erweiterungsmethoden, um den .NET-Objekten die Ergebnisse Ihrer SQL-Abfrage zuzuordnen:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Der **using**-Block mit der DDR-Verbindung aus Elastic Scale ordnet alle Datenbankvorgänge innerhalb des Blocks dem Shard zu, in dem tenantId1 gespeichert ist. Die Abfrage gibt nur die im aktuellen Shard gespeicherten Blogs, aber nicht die in anderen Shards gespeicherten Blogs zurück. 

## Datenabhängiges Routing mit Dapper und DapperExtensions

Dapper wird mit einem ganzen Ökosystem zusätzlicher Erweiterungen bereitgestellt, die eine komfortablere und von der Datenbank abstrahierte Entwicklung von Datenbankanwendungen ermöglichen. DapperExtensions ist ein Beispiel dafür. 

Die Verwendung von DapperExtensions in der Anwendung ändert nichts daran, wie Datenbankverbindungen erstellt und verwaltet werden. Es liegt immer noch in der Verantwortung der Anwendung, Verbindungen zu öffnen, und die Erweiterungsmethoden erwarten reguläre SQL-Clientverbindungsobjekte. Wie oben beschrieben, können wir den [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) verwenden. Wie die folgenden Codebeispiele zeigen, besteht die einzige Änderung darin, dass keine T-SQL-Anweisungen mehr geschrieben werden müssen:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

Im Folgenden finden Sie das Codebeispiel für die Abfrage: 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### Behandeln vorübergehender Fehler

Das Microsoft Patterns & Practices-Team hat den [Transient Fault Handling Application Block](http://msdn.microsoft.com/library/hh680934.aspx) veröffentlicht, um Anwendungsentwicklern die Reduzierung gängiger vorübergehender Fehler zu ermöglichen, die bei der Ausführung in der Cloud auftreten. Weitere Informationen finden Sie unter [Perseverance, Secret of All Triumphs: Using the Transient Fault Handling Application Block](http://msdn.microsoft.com/library/dn440719.aspx).

Zum Schutz vor vorübergehenden Fehlern verwendet das Codebeispiel die entsprechende TFH (Transient Fault Handling)-Bibliothek. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

Im Code oben ist **SqlDatabaseUtils.SqlRetryPolicy** als **SqlDatabaseTransientErrorDetectionStrategy** mit 10 Wiederholungen und einer Wartezeit von 5 Sekunden zwischen den Wiederholungen definiert. Bei Verwendung von Transaktionen sollten Sie sicherstellen, dass Ihr Wiederholungsbereich im Fall eines vorübergehenden Fehlers an den Anfang der Transaktion zurückkehrt.

## Einschränkungen

Für die in diesem Dokument beschriebenen Vorgehensweisen gelten einige Einschränkungen:

* Im Beispielcode dieses Dokuments wird nicht auf die die shardübergreifende Schemaverwaltung eingegangen.
* Bei einer Anforderung wird davon ausgegangen, dass die damit verbundene Datenbankverarbeitung innerhalb eines einzelnen Shards erfolgt, der durch den in der Anforderung angegebenen Shardingschlüssel identifiziert wird. Diese Annahme trifft jedoch nicht immer zu, z. B. wenn kein Shardingschlüssel verfügbar gemacht werden kann. Zur Behebung dieses Problems bieten die Elastic Scale-Bibliotheken die [MultiShardQuery-Klasse](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). Die Klasse implementiert eine Verbindungsabstraktion für Abfragen über mehrere Shards. Die Verwendung von MultiShardQuery in Kombination mit Dapper geht über den Rahmen dieses Dokuments hinaus.

## Zusammenfassung

Anwendungen, die **Dapper** und **DapperExtensions** verwenden, können auch leicht von Elastic Scale für Azure SQL-Datenbank profitieren. Mithilfe der in diesem Dokument beschriebenen Schritte können diese Anwendungen die Elastic Scale-Funktion für das datenabhängige Routing verwenden, indem sie die Erstellung und das Öffnen neuer [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) -Objekte dahingehend ändern, dass der [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx)-Aufruf der Elastic Scale-Bibliothek verwendet wird. Dies beschränkt Änderungen in Anwendungen auf Situationen, in denen neue Verbindungen erstellt und geöffnet werden. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png

<!--HONumber=47-->
