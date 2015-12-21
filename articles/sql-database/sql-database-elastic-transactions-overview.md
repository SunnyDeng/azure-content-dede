<properties
   pageTitle="Übersicht über elastische Datenbanktransaktionen mit Azure SQL-Datenbank (Vorschau)"
   description="Übersicht über elastische Datenbanktransaktionen mit Azure SQL-Datenbank (Vorschau)"
   services="sql-database"
   documentationCenter=""
   authors="torsteng"
   manager="jeffreyg"
   editor="sidneyh"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="sql-database"
   ms.date="11/02/2015"
   ms.author="torsteng"/>

# Übersicht über elastische Datenbanktransaktionen mit Azure SQL-Datenbank (Vorschau)

Mithilfe elastischer Datenbanktransaktionen für Azure SQL-Datenbank (SQL-DB) können Sie übergreifende Transaktionen für mehrere Datenbanken in SQL-DB ausführen. Elastische Datenbanktransaktionen für SQL-DB stehen für .NET-Anwendungen über ADO.NET zur Verfügung und lassen sich mithilfe der Klassen vom Typ [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) in die vertraute Programmierumgebung integrieren.

Lokal musste für ein solches Szenario normalerweise MSDTC (Microsoft Distributed Transaction Coordinator) ausgeführt werden. Da MSDTC in Azure nicht als Platform-as-a-Service-Anwendung verfügbar ist, wurde die Koordinierung verteilter Transaktionen nun direkt in SQL-DB integriert. Anwendungen können eine Verbindung mit einer beliebigen SQL-Datenbank herstellen, um verteilte Transaktionen zu starten, und eine der Datenbanken koordiniert auf transparente Weise die verteilte Transaktion, wie in der folgenden Abbildung zu sehen:

  ![Verteilte Transaktionen mit Azure SQL-Datenbank unter Verwendung elastischer Datenbanktransaktionen][1]

## Häufige Szenarios

Mit elastischen Datenbanktransaktionen für SQL-DB können Anwendungen unteilbare Änderungen an Daten vornehmen, die in mehreren verschiedenen SQL-Datenbanken gespeichert sind. Die Vorschau konzentriert sich auf clientseitige Entwicklungsoptionen in C# und .NET. Eine serverseitige Option mit T-SQL ist für einen späteren Zeitpunkt geplant. Elastische Datenbanktransaktionen eignen sich für folgende Szenarien:

* Multidatenbankanwendungen in Azure: In diesem Szenario werden die Daten vertikal so auf mehrere Datenbanken in SQL-DB verteilt, dass sich unterschiedliche Datentypen in unterschiedlichen Datenbanken befinden. Einige Vorgänge erfordern Änderungen an Daten in mehreren Datenbanken. Die Anwendung verwendet elastische Datenbanktransaktionen, um die datenbankübergreifenden Änderungen zu koordinieren und die Unteilbarkeit sicherzustellen.

* Sharding-Datenbankanwendungen in Azure: In diesem Szenario werden die Daten mithilfe der Clientbibliothek für elastische Datenbanken oder mittels [Selbst-Sharding](http://social.technet.microsoft.com/wiki/contents/articles/17987.cloud-service-fundamentals.aspx) horizontal für mehrere Datenbanken in SQL-DB partitioniert. Ein naheliegender Anwendungsfall ist das Durchführen unteilbarer Änderungen für eine mehrinstanzenfähige Sharding-Anwendung, wenn die Änderungen mehrere Mandanten betreffen. Stellen Sie sich beispielsweise eine Übertragung zwischen zwei Mandanten in unterschiedlichen Datenbanken vor. Ein zweiter Fall wäre differenziertes Sharding zur Erfüllung der Kapazitätsanforderungen eines großen Mandanten. Dies impliziert in der Regel, dass sich einige unteilbare Vorgänge über mehrere, für den gleichen Mandanten verwendete Datenbanken erstrecken. Ein dritter Fall wären unteilbare Aktualisierungen für Referenzdaten mit datenbankübergreifender Replizierung. Unteilbare (transaktionsorientierte) Vorgänge dieser Art können jetzt mithilfe der Vorschau über mehrere Datenbanken hinweg koordiniert werden. Elastische Datenbanktransaktionen verwenden ein Zweiphasencommit, um die datenbankübergreifende Unteilbarkeit von Transaktionen zu gewährleisten. Dies eignet sich gut für Transaktionen mit einer Beteiligung von weniger als 100 Datenbanken pro einzelner Transaktion. Diese Limits werden nicht erzwungen. Eine Überschreitung beeinträchtigt jedoch die Leistung und Erfolgsraten von elastischen Datenbanktransaktionen.


## Installation und Migration

Die Funktionen für elastische Datenbanktransaktionen in SQL-DB werden mittels Aktualisierung der .NET-Bibliotheken „System.Data.dll“ und „System.Transactions.dll“ bereitgestellt. Die DLLs gewährleisten, dass bei Bedarf das Zweiphasencommit verwendet wird, um die Unteilbarkeit sicherzustellen. Installieren Sie [.NET 4.6.1](https://www.microsoft.com/de-DE/download/details.aspx?id=49981) oder eine höhere Version von .NET Framework, um mit dem Entwickeln von Anwendungen mit elastischen Datenbanktransaktionen zu beginnen. Bei der Ausführung unter einer älteren Version von .NET Framework lassen sich Transaktionen nicht zu einer verteilten Transaktion höher stufen, was eine Ausnahme zur Folge hat.

Nach der Installation können Sie die APIs für verteilte Transaktionen in „System.Transactions“ für Verbindungen mit SQL-DB verwenden. Wenn Sie über MSDTC-Anwendungen verfügen, die diese APIs verwenden, erstellen Sie nach der Installation von .NET Framework 4.6.1 einfach Ihre vorhandenen Anwendungen für .NET Framework 4.6 neu. Falls Ihre Projekte auf .NET Framework 4.6 ausgerichtet sind, verwenden sie automatisch die aktualisierten DLLs der neuen .NET Framework-Version. Aufrufe der API für verteilte Transaktion können in Kombination mit Verbindungen zu SQL-DB erfolgreich ausgeführt werden.

Beachten Sie, dass für elastische Datenbanktransaktionen keine Installation von MSDTC erforderlich ist. Elastische Datenbanktransaktionen werden stattdessen direkt durch (und innerhalb von) SQL-DB verwaltet. Dies vereinfacht Cloudszenarien erheblich, da die Verwendung verteilter Transaktionen mit SQL-DB keine MSDTC-Bereitstellung erfordert. Ausführlichere Informationen zum gemeinsamen Bereitstellen von elastischen Datenbanktransaktionen, .NET Framework und Ihren Cloudanwendungen in Azure finden Sie in Abschnitt 4.

## Entwicklungsumgebung

###	Multidatenbankanwendungen

Der folgende Beispielcode verwendet die vertraute Programmierung mit „System.Transactions“ aus .NET. Die TransactionScope-Klasse erstellt eine Ambient-Transaktion in .NET. (Eine Ambient-Transaktion ist eine Transaktion aus dem aktuellen Thread.) Alle hergestellten Verbindungen innerhalb des Transaktionsbereichs sind an der Transaktion beteiligt. Bei Beteiligung verschiedener Datenbanken wird die Transaktion automatisch zu einer verteilten Transaktion höher gestuft. Zum Steuern des Transaktionsergebnisses wird der Bereich auf „Complete“ festgelegt, um einen Commit anzugeben.

	using (var scope = new TransactionScope())
	{
		using (var conn1 = new SqlConnection(connStrDb1))
		{
			conn1.Open();
			SqlCommand cmd1 = conn1.CreateCommand();
			cmd1.CommandText = string.Format("insert into T1 values(1)");
			cmd1.ExecuteNonQuery();
		}

		using (var conn2 = new SqlConnection(connStrDb2))
		{
			conn2.Open();
			var cmd2 = conn2.CreateCommand();
			cmd2.CommandText = string.Format("insert into T2 values(2)");
			cmd2.ExecuteNonQuery();
		}

		scope.Complete();
	}

### Sharding-Datenbankanwendungen
 
Elastische Datenbanktransaktionen für SQL-DB unterstützen auch die Koordinierung verteilter Transaktionen, bei denen Sie mithilfe der OpenConnectionForKey-Methode der Clientbibliothek für elastische Datenbanken Verbindungen für eine horizontal hochskalierte Datenschicht herstellen. Beispiele wären etwa Fälle, in denen Sie bei Änderungen Transaktionskonsistenz über verschiedene Sharding-Schlüsselwerte hinweg sicherstellen müssen. Verbindungen mit den Shards, die die verschiedenen Sharding-Schlüsselwerte hosten, werden mithilfe von „OpenConnectionForKey“ vermittelt. Grundsätzlich können die Verbindungen mit unterschiedlichen Shards hergestellt werden, sodass zur Gewährleistung von Transaktionsgarantien eine verteilte Transaktion erforderlich ist. Das folgende Codebeispiel veranschaulicht diese Vorgehensweise. Hierbei wird davon ausgegangen, dass eine Variable namens „shardmap“ eine Shardzuordnung aus der Clientbibliothek für elastische Datenbanken dargestellt:

	using (var scope = new TransactionScope())
	{
		using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
		{
			conn1.Open();
			SqlCommand cmd1 = conn1.CreateCommand();
			cmd1.CommandText = string.Format("insert into T1 values(1)");
			cmd1.ExecuteNonQuery();
		}
		
		using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
		{
			conn2.Open();
			var cmd2 = conn2.CreateCommand();
			cmd2.CommandText = string.Format("insert into T1 values(2)");
			cmd2.ExecuteNonQuery();
		}

		scope.Complete();
	}


## Setup für Azure-Workerrollen

Sie können die Installation und Bereitstellung der .NET-Version und Bibliotheken automatisieren, die für elastische Datenbanktransaktionen an Azure benötigt werden. Ziel ist das Gast-BS des Clouddiensts. Verwenden Sie für Azure-Workerrollen die Startaufgaben. Informationen zu den Konzepten und Schritten finden Sie unter [Installieren von .NET in einer Clouddienstrolle](../cloud-services/cloud-services-dotnet-install-dotnet.md).

Beachten Sie, dass das Installationsprogramm für .NET 4.6.1 während des Bootstrappingprozesses für Azure-Clouddienste mehr temporären Speicherplatz benötigt, als für .NET 4.6. Um eine erfolgreiche Installation sicherzustellen, erhöhen Sie den temporären Speicher für Ihren Azure-Clouddienst in der Datei "ServiceDefinition.csdef" im Abschnitt "LocalResources". Ändern Sie außerdem die Umgebungseinstellungen der Startaufgabe wie im folgenden Beispiel gezeigt:

	<LocalResources>
	...
		<LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
		<LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
	</LocalResources>
	<Startup>
		<Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
			<Environment>
		...
				<Variable name="TEMP">
					<RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
				</Variable>
				<Variable name="TMP">
					<RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
				</Variable>
			</Environment>
		</Task>
	</Startup>

## Überwachen des Transaktionsstatus

Verwenden Sie DMVs (Dynamic Management Views) in SQL-SB, um Status und Fortschritt laufender elastischer Datenbanktransaktionen zu überwachen. Für verteilte Transaktionen in SQL-DB sind alle transaktionsbezogenen DMVs relevant. Die entsprechende DMV-Liste finden Sie hier: [Dynamische Verwaltungssichten und -funktionen im Zusammenhang mit Transaktionen (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).
 
Folgende DMVs sind besonders hilfreich:

* **sys.dm\_tran\_active\_transactions**: Führt die derzeit aktiven Transaktionen und deren Status auf. Die UOW-Spalte (Arbeitseinheit) gibt die verschiedenen untergeordneten Transaktionen an, die zur gleichen verteilten Transaktion gehören. Alle Transaktionen innerhalb einer verteilten Transaktion haben den gleichen UOW-Wert. Weitere Informationen finden Sie in der [DMV-Dokumentation](https://msdn.microsoft.com/library/ms174302.aspx).
* **sys.dm\_tran\_database\_transactions**: Liefert zusätzliche Informationen zu Transaktionen (wie etwa die Position der Transaktion im Protokoll). Weitere Informationen finden Sie in der [DMV-Dokumentation](https://msdn.microsoft.com/library/ms186957.aspx).
* **sys.dm\_tran\_locks**: Enthält Informationen zu den Sperren, die derzeit für laufende Transaktionen aktiv sind. Weitere Informationen finden Sie in der [DMV-Dokumentation](https://msdn.microsoft.com/library/ms190345.aspx).

## Einschränkungen 

Derzeit gelten für elastische Datenbanktransaktionen in SQL-DB folgende Einschränkungen:

* Es werden nur datenbankübergreifende Transaktionen in SQL-DB unterstützt. Andere Ressourcenanbieter für [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) sowie Datenbanken außerhalb von SQL-DB können nicht an elastischen Datenbanktransaktionen beteiligt werden. Das bedeutet, dass sich elastische Datenbanktransaktionen nicht über lokale SQL Server- und Azure SQL-Datenbanken erstrecken können. Für lokale verteilte Transaktionen muss weiterhin MSDTC verwendet werden. 
* Nur clientkoordinierte Transaktionen einer .NET-Anwendung werden unterstützt. Die serverseitige Unterstützung für T-SQL (etwa „BEGIN DISTRIBUTED TRANSACTION“) ist zwar geplant, aber noch nicht verfügbar. 
* Nur Datenbanken für Azure SQL DB V12 werden unterstützt.
* Nur Datenbanken, die dem gleichen logischen Server in SQL-DB angehören, werden unterstützt.

## Weitere Informationen

Sie verwenden noch keine elastischen Datenbankfunktionen für Ihre Azure-Anwendungen? Sehen Sie sich unsere [Dokumentationsübersicht](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) an. Bei Fragen erreichen Sie uns im [SQL-Datenbankforum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted), Featureanforderungen können Sie im [SQL-Datenbank-Feedbackforum](http://feedback.azure.com/forums/217321-sql-database) einreichen.

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png

<!---HONumber=AcomDC_1210_2015-->