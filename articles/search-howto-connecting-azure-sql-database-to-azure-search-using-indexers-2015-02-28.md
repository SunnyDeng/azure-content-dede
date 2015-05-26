<properties 
	pageTitle="Verbinden von Azure SQL-Datenbank mit Azure Search mithilfe von Indexern" 
	description="Erfahren Sie, wie Sie mithilfe von Indexern Daten aus einer SQL Azure-Datenbank in einen Azure Search-Index abrufen." 
	services="search" 
	documentationCenter="" 
	authors="chaosrealm" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="04/18/2015" 
	ms.author="eugenesh"/>

#Verbinden von Azure SQL-Datenbank mit Azure Search mithilfe von Indexern#

Der Azure Search-Dienst macht es einfach, eine hervorragende Suchfunktion bereitzustellen, aber bevor Sie suchen können, müssen Sie einen Azure Search-Index mit Ihren Daten auffüllen. Wenn sich die Daten in einer Azure SQL-Datenbank befinden, lässt sich der Indizierungsprozess mithilfe der neuen Funktion **Azure Search-Indexer für Azure SQL-Datenbank** (oder kurz **Azure SQL-Indexer**) in Azure Search automatisieren. Dies bedeutet, dass Sie weniger Code schreiben und weniger Infrastruktur bereitstellen müssen.

Derzeit funktionieren Indexer nur mit Azure SQL-Datenbank, SQL Server auf virtuellen Azure-Computern und Azure DocumentDB. In diesem Artikel konzentrieren wir uns auf Indexer, die mit Azure SQL-Datenbank arbeiten. Wenn Sie Unterstützung für zusätzliche Datenquellen wünschen, teilen Sie Ihr Feedback bitte im [Azure Search-Feedbackforum](http://feedback.azure.com/forums/263029-azure-search) mit.

Dieser Artikel behandelt die Mechanismen der Verwendung von Indexern, aber wir werden auch detailliert auf Funktionen und Verhaltensweisen eingehen, die nur mit SQL-Datenbanken verfügbar sind (z. B. integrierte Änderungsnachverfolgung).

## Indexer und Datenquellen ##

Zum Einrichten und Konfigurieren eines Azure SQL-Indexers können Sie die [REST-API von Azure Search](http://go.microsoft.com/fwlink/p/?LinkID=528173) aufrufen, um **Indexer** und **Datenquellen** zu erstellen und zu verwalten. Diese Funktionalität wird in Zukunft auch im Azure-Verwaltungsportal und in Azure Search .NET SDK verfügbar sein. 

Eine **Datenquelle** gibt an, welche Daten indiziert werden sollen, legt die für den Zugriff auf die Daten notwendigen Anmeldeinformationen fest sowie Richtlinien, die es Azure Search ermöglichen, Änderungen an den Daten effizient zu identifizieren (neue, geänderte oder gelöschte Zeilen). Sie ist als unabhängige Ressource definiert, sodass sie von mehreren Indexern verwendet werden kann.

Ein **Indexer** ist eine Ressource, die eine Verbindung zwischen Datenquellen und Zielsuchindizes herstellt. Ein Indexer wird folgendermaßen verwendet:
 
- Eine einmalige Kopie der Daten zum Auffüllen eines Indexes ausführen.
- Einen Index mit Änderungen an der Datenquelle nach einem Zeitplan aktualisieren.
- Ausführung bei Bedarf, um einen Index nach Bedarf zu aktualisieren. 

## Verwendungsmöglichkeiten für den Azure SQL-Indexer ##

Abhängig von verschiedenen Faktoren im Zusammenhang mit Ihren Daten kann der Einsatz des Azure SQL-Indexers angebracht sein oder nicht. Wenn Ihre Daten die folgenden Anforderungen erfüllen, können Sie den Azure SQL-Indexer verwenden: 

- Alle Daten stammen aus einer einzelnen Tabelle oder Sicht.
	- Wenn die Daten auf mehrere Tabellen verteilt sind, können Sie eine Sicht erstellen und diese Ansicht mit dem Indexer verwenden. Bedenken Sie jedoch, dass Sie, wenn Sie eine Sicht verwenden, nicht die in SQL Server integrierte Änderungserkennung verwenden können. Weitere Details finden Sie in diesem Abschnitt. 
- Die in der Datenquelle verwendeten Datentypen werden vom Indexer unterstützt. Die meisten, aber nicht alle SQL-Typen werden unterstützt. Details hierzu finden Sie unter [Zuordnen von Datentypen in Azure Search](http://go.microsoft.com/fwlink/p/?LinkID=528105). 
- Sie benötigen keine Aktualisierungen des Indexes in beinahe Echtzeit, wenn sich eine Zeile ändert. 
	- Der Indexer kann Ihre Tabelle höchstens alle 5 Minuten erneut indizieren. Wenn sich Ihre Daten häufig ändern und die Änderungen innerhalb von Sekunden oder wenigen Minuten im Index auftauchen müssen, empfehlen wir die direkte Verwendung der [Index-API von Azure Search](https://msdn.microsoft.com/library/azure/dn798930.aspx). 
- Wenn Sie ein großes Dataset haben und planen, den Indexer nach einem Zeitplan auszuführen, ermöglicht Ihr Schema es uns, um geänderte Zeilen (und gelöschte, falls vorhanden) effizient zu identifizieren. Weitere Details finden Sie weiter unten unter "Aufzeichnen geänderter und gelöschter Zeilen". 
- Die Größe der indizierten Felder in einer Zeile überschreitet nicht die maximale Größe einer Azure Search-Indizierungsanforderung, die 16 MB beträgt. 

## Erstellen und Verwenden eines Azure SQL-Indexers ##

Erstellen Sie zunächst die Datenquelle: 

	POST https://myservice.search.windows.net/datasources?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key
	
	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
	    "container" : { "name" : "name of the table or view that you want to index" }
	}


Sie können die Verbindungszeichenfolge aus dem [Azure-Portal](https://portal.azure.com) abrufen. Verwenden Sie die Option `ADO.NET connection string`.

Erstellen Sie dann den Azure Search-Zielindex, wenn Sie bislang noch über keinen verfügen. Sie können dies von der [Portal-Benutzeroberfläche](https://portal.azure.com) aus tun, oder indem Sie die [API zum Erstellen von Indizes](https://msdn.microsoft.com/library/azure/dn798941.aspx) verwenden.  Stellen Sie sicher, dass das Schema Ihres Zielindexes mit dem Schema der Quelltabelle kompatibel ist. Die Zuordnung zwischen SQL- und Azure Search-Datentypen finden Sie in der folgende Tabelle.

**Zuordnung zwischen SQL-Datentypen und Azure Search-Datentypen**
<table style="font-size:12">
<tr>
<td>SQL-Datentyp</td>	
<td>Zulässige Zielindex-Feldtypen</td>
<td>Hinweise</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>real, float</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney, money<br/>decimal<br/>numeric
</td>
<td>Edm.String</td>
<td>Azure Search unterstützt keine Umwandlung von Dezimaltypen in Edm.Double, weil hierbei die Genauigkeit sinken würde.
</td>
</tr>
<tr>
<td>char, nchar, varchar, nvarchar</td>
<td>Edm.String<br/>Collection(Edm.String)</td>
<td>Das Transformieren einer Zeichenfolgenspalte in "Collection(Edm.String)" erfordert die Verwendung der API-Version 2015-02-28-Preview. Details hierzu finden Sie [in diesem Artikel](search-api-indexers-2015-02-28-Preview.md#create-indexer).</td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, date, datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>geography</td>
<td>Edm.GeographyPoint</td>
<td>Nur geography-Instanzen vom Typ POINT mit SRID 4326 (der Standardeinstellung) werden unterstützt.</td>
</tr>
<tr>
<td>rowversion</td>
<td>-</td>
<td>rowversion-Spalten können nicht im Search-Index gespeichert werden, können aber zur Änderungsnachverfolgung verwendet werden.</td>
</tr>
<tr>
<td>time, timespan<br>binary, varbinary, image,<br>xml, geometry, CLR-Typen</td>
<td>-</td>
<td>Nicht unterstützt</td>
</tr>
</table>

Erstellen Sie schließlich den Indexer, indem Sie ihm einen Namen geben und auf die Datenquelle und den Zielindex verweisen:

	POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key
	
	{ 
	    "name" : "myindexer",
	    "dataSourceName" : "myazuresqldatasource",
	    "targetIndexName" : "target index name"
	}

Ein auf diese Weise erstellter Indexer verfügt über keinen Zeitplan. Er wird automatisch einmalig ausgeführt, sobald er erstellt ist. Sie können ihn jederzeit mit einer **run indexer**-Anforderung erneut ausführen:

	POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
	api-key: admin-key
 
Möglicherweise müssen Sie Azure Services gestatten, eine Verbindung mit Ihrer Datenbank herzustellen. Anleitungen zur Vorgehensweise hierfür finden Sie unter [Verbinden von Azure aus](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure).

Verwenden Sie zum Überwachen des Indexerstatus und des Ausführungsverlaufs (Anzahl der indizierten Elemente, Fehler usw.) eine **indexer status**-Anforderung: 

	GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2015-02-28 
	api-key: admin-key

Die Antwort sollte etwa wie folgt aussehen: 

	{
		"@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
		"status":"running",
		"lastResult": {
			"status":"success",
			"errorMessage":null,
			"startTime":"2015-02-21T00:23:24.957Z",
			"endTime":"2015-02-21T00:36:47.752Z",
			"errors":[],
			"itemsProcessed":1599501,
			"itemsFailed":0,
			"initialTrackingState":null,
			"finalTrackingState":null 
        },
		"executionHistory":
		[
			{
				"status":"success",
				"errorMessage":null,
				"startTime":"2015-02-21T00:23:24.957Z",
				"endTime":"2015-02-21T00:36:47.752Z",
				"errors":[],
				"itemsProcessed":1599501,
				"itemsFailed":0,
				"initialTrackingState":null,
				"finalTrackingState":null 
			},
			... earlier history items 
		]
	}

Der Ausführungsverlauf enthält bis zu 50 der jüngsten abgeschlossenen Ausführungen. Diese sind in umgekehrter chronologischer Reihenfolge sortiert (somit ist die neueste Ausführung als Erstes in der Antwort aufgelistet). 
Zusätzliche Informationen zu der Antwort finden Sie unter [Abrufen des Indexerstatus](http://go.microsoft.com/fwlink/p/?LinkId=528198).

## Ausführen von Indexern nach einem Zeitplan ##

Sie können den Indexer auch so einrichten, dass er regelmäßig nach einem Zeitplan ausgeführt wird. Hierzu fügen Sie einfach die Eigenschaft **schedule** hinzu, wenn Sie den Indexer erstellen oder aktualisieren. Das folgende Beispiel zeigt eine PUT-Anforderung, um den Indexer zu aktualisieren:

	PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key 

	{ 
	    "dataSourceName" : "myazuresqldatasource",
	    "targetIndexName" : "target index name",
	    "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
	}

Der Parameter **interval** ist erforderlich. Das Intervall bezieht sich auf den Zeitraum zwischen dem Start zweier aufeinander folgender Indexerausführungen. Das kleinste zulässige Intervall beträgt 5 Minuten. Das längste ist ein Tag. Es muss als XSD-Wert "dayTimeDuration" formatiert sein (eine eingeschränkte Teilmenge eines [ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)-Zeitdauerwerts). Das Muster hierfür lautet:  `P(nD)(T(nH)(nM))`. Beispiele:  `PT15M` für alle 15 Minuten,  `PT2H` für alle 2 Stunden.

Die optionale **startTime** gibt an, wann die geplanten Ausführungen beginnen sollen. Wird der Parameter ausgelassen, wird die aktuelle UTC-Zeit verwendet. Diese Zeit kann in der Vergangenheit liegen - in diesem Fall würde dann die erste Ausführung so geplant, als ob der Indexer seit "startTime" fortwährend ausgeführt worden wäre.  

Nur eine Ausführung eines bestimmten Indexers kann gleichzeitig ausgeführt werden. Wird ein Indexer bereits ausgeführt, wenn die nächste Ausführung eigentlich starten sollte, wird diese Ausführung übersprungen und mit dem nächsten Intervall fortgefahren, sofern kein anderer Indexerauftrag ausgeführt wird.

Betrachten wir ein Beispiel, um dies konkreter zu veranschaulichen. Angenommen, wir haben den folgenden stündlichen Zeitplan konfiguriert: 

	"schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Folgendes geschieht: 

1. Die erste Ausführung des Indexers beginnt am oder ungefähr am 1. März 2015 um 0:00 Uhr UTC.
1. Wir nehmen an, dass diese Ausführung 20 Minuten (oder einen beliebigen Zeitraum unter 1 Stunde) dauert.
1. Die zweite Ausführung beginnt am oder ungefähr am 1. März 2015 um 1:00 Uhr. 
1. Gehen wir nun davon aus, dass diese Ausführung länger als eine Stunde dauert (hierfür wäre eine riesige Menge an Dokumenten erforderlich, damit dies tatsächlich einträte, aber es ist eine nützliche Illustrierung) - z. B. 70 Minuten - sodass er etwa um 2:10 Uhr abgeschlossen würde.
1. Jetzt ist es 2:00 Uhr, und die dritte Ausführung müsste starten. Da aber die zweite Ausführung ab 1:00 Uhr noch ausgeführt wird, wird die dritte Ausführung übersprungen. Die dritte Ausführung beginnt um 3 Uhr.

Sie können einen Zeitplan für einen vorhandenen Indexer hinzufügen, ändern oder löschen, indem Sie eine **PUT indexer**-Anforderung verwenden. 

## Erfassen neuer, geänderter und gelöschter Zeilen ##

Wenn Sie einen Zeitplan verwenden und Ihre Tabelle eine nicht triviale Anzahl von Zeilen enthält, sollten Sie eine Richtlinie zur Erkennung von Datenänderungen verwenden, damit der Indexer effizient nur die neuen und geänderten Zeilen abrufen kann, ohne die gesamte Tabelle neu indizieren zu müssen.

### SQL-integrierte Richtlinie für die Änderungsnachverfolgung ###

Wenn Ihre SQL-Datenbank [Änderungsnachverfolgung](https://msdn.microsoft.com/library/bb933875.aspx) unterstützt, empfehlen wir die Verwendung einer **SQL-integrierten Richtlinie für die Änderungsnachverfolgung**. Diese Richtlinie ermöglicht die effizienteste Änderungsnachverfolgung und gestattet außerdem Azure Search die Identifizierung gelöschter Zeilen, ohne dass Sie Ihrer Tabelle eine explizite Spalte für "vorläufiges Löschen" hinzufügen müssten.

Integrierte Änderungsnachverfolgung wird ab den folgenden Versionen der SQL Server-Datenbank unterstützt:
 
- SQL Server 2008 R2 und höher, wenn Sie SQL Server auf virtuellen Azure-Computern verwenden. 
- Azure SQL-Datenbank V12, wenn Sie Azure SQL-Datenbank verwenden.

Bei Verwendung der SQL-integrierten Richtlinie für die Änderungsnachverfolgung müssen Sie keine gesonderte Richtlinie für die Erkennung von Datenlöschungen angeben, da diese Richtlinie über integrierte Unterstützung zur Identifizierung von gelöschten Zeilen verfügt.

Diese Richtlinie kann nur mit Tabellen verwendet werden, nicht mit Sichten. Sie müssen zuerst die Änderungsnachverfolgung für die verwendete Tabelle aktivieren, bevor Sie diese Richtlinie verwenden können. Anleitungen finden Sie unter [Aktivieren und Deaktivieren der Änderungsnachverfolgung](https://msdn.microsoft.com/library/bb964713.aspx). 

Um diese Richtlinie zu verwenden, erstellen oder aktualisieren Sie Ihre Datenquelle wie folgt:
 
	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "connection string" },
	    "container" : { "name" : "table or view name" }, 
	    "dataChangeDetectionPolicy" : {
	       "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
	  }
	}

### Richtlinie für die Erkennung von Obergrenzenänderungen ###

Zwar empfiehlt sich die SQL-integrierte Richtlinie für die Änderungsnachverfolgung, doch können Sie sie nicht verwenden, wenn sich Ihre Daten in einer Sicht befinden, oder wenn Sie eine ältere Version von Azure SQL-Datenbank verwenden. In einem solchen Fall sollten Sie erwägen, die Richtlinie für Erkennung von Obergrenzen zu verwenden. Diese Richtlinie kann verwendet werden, wenn Ihre Tabelle eine Spalte enthält, die die folgenden Kriterien erfüllt:

- Alle Einfügungen geben einen Wert für die Spalte an. 
- Alle Aktualisierungen eines Elements ändern ebenfalls den Wert der Spalte. 
- Der Wert dieser Spalte erhöht sich bei jeder Änderung.
- Abfragen, die eine  `WHERE`-Klausel ähnlich wie `WHERE [Obergrenzenspalte] > [Aktueller Obergrenzenwert]` verwenden, können effizient ausgeführt werden.

Eine indizierte **rowversion**-Spalte beispielsweise ist ein idealer Kandidat für die Obergrenzenspalte. 
Um diese Richtlinie zu verwenden, erstellen oder aktualisieren Sie Ihre Datenquelle wie folgt: 

	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "connection string" },
	    "container" : { "name" : "table or view name" }, 
	    "dataChangeDetectionPolicy" : {
	       "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
	       "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
	  }
	}

### Richtlinie für die Löschungserkennung mittels "Vorläufiges Löschen"-Spalte ###

Wenn Zeilen aus der Quelltabelle gelöscht werden, sollen diese Zeilen vermutlich auch im Suchindex gelöscht werden. Wenn Sie die SQL-integrierte Richtlinie für die Änderungsnachverfolgung verwenden, wird dies für Sie erledigt. Die Richtlinie für die Nachverfolgung von Obergrenzenänderungen hilft Ihnen allerdings bei gelöschten Zeilen nicht. Was tun? 

Wenn die Zeilen physisch aus der Tabelle entfernt werden, haben Sie leider Pech - es gibt keine Möglichkeit, die Existenz von Datensätzen abzuleiten, die nicht mehr vorhanden sind.  Sie können aber das Verfahren "vorübergehendes Löschen" verwenden, um Zeilen als logisch gelöscht zu markieren, ohne sie aus der Tabelle zu entfernen, indem Sie eine Spalte hinzufügen und Zeilen mithilfe eines Markierungswerts in dieser Spalte als gelöscht markieren.

Wenn Sie da "vorübergehendes Löschen"-Verfahren verwenden, können Sie die Richtlinie für vorübergehendes Löschen wie folgt angeben, wenn Sie die Datenquelle erstellen oder aktualisieren: 

	{ 
	    ..., 
	    "dataDeletionDetectionPolicy" : { 
	       "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
	       "softDeleteColumnName" : "[a column name]", 
	       "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
	    }
	}

Beachten Sie, dass der **SoftDeleteMarkerValue** eine Zeichenfolge sein muss - verwenden Sie die Zeichenfolgendarstellung Ihres tatsächlichen Werts. Wenn Sie beispielsweise eine Integer-Spalte haben, in der gelöschte Zeilen mit dem Wert 1 gekennzeichnet werden, verwenden Sie `"1"`. Wenn Sie eine BIT-Spalte haben, in der gelöschte Zeilen mit dem booleschen Wert "true" markiert werden, verwenden Sie `"True"`. 

## Anpassen des Azure SQL-Indexers ##
 
Sie können bestimmte Aspekte des Indexerverhaltens anpassen (z. B. die Batchgröße, wie viele Dokumente übersprungen werden können, bevor eine Indexerausführung als Fehler bewertet wird, usw.). Weitere Details finden Sie unter [Indexer-API-Dokumentation](http://go.microsoft.com/fwlink/p/?LinkId=528173). 

## Häufig gestellte Fragen ##

**F:** Kann ich Azure SQL-Indexer mit SQL-Datenbanken verwenden, die auf virtuellen IaaS-Computern in Azure ausgeführt werden?

A: Ja, solange Sie Azure Services gestatten, eine Verbindung mit Ihrer Datenbank herzustellen, indem Sie die entsprechenden Ports öffnen.

**F:** Kann ich Azure SQL-Indexer mit SQL-Datenbanken verwenden, die lokal ausgeführt werden?

A: Dies wird nicht empfohlen oder unterstützt, da es sonst erforderlich wäre, dass Sie Ihre Datenbanken für den Internetverkehr öffneten. 

**F:** Kann ich Azure SQL-Indexer mit anderen Datenbanken als SQL Server verwenden, die als IaaS in Azure ausgeführt werden?

A: Dieses Szenario wird nicht unterstützt, da wir den Indexer mit keinen anderen Datenbanken als SQL Server getestet haben.  

**F:** Kann ich mehrere Indexer erstellen, die nach einem Zeitplan ausgeführt werden?

A: Ja. Jedoch kann immer nur ein Indexer gleichzeitig auf einem Knoten ausgeführt werden. Wenn Sie mehrere Indexer gleichzeitig ausführen müssen, sollten Sie erwägen, Ihren Suchdienst auf mehr als eine Sucheinheit zentral hochzuskalieren. 

**F:** Wirkt sich die Ausführung eines Indexers auf meine Abfragearbeitsauslastung aus?

A: Ja. Indexer wird auf einem der Knoten in Ihrem Suchdienst ausgeführt, und die Ressourcen dieses Knotens werden gemeinsam für die Indizierung und die Verarbeitung von Abfragedatenverkehr sowie für andere API-Anforderungen genutzt. Wenn Sie intensive Indizierungs- und Abfragearbeitsauslastungen ausführen und es zu einer hohen Rate von 503-Fehlern oder zunehmend längeren Reaktionszeiten kommt, sollten Sie erwägen, Ihren Suchdienst zentral hochzuskalieren. 




<!--HONumber=54-->