<properties 
	pageTitle="Verbinden von Azure SQL-Datenbank mit Azure Search mithilfe von Indexern | Microsoft Azure | Gehosteter Cloudsuchdienst" 
	description="Erfahren Sie, wie Sie Daten aus SQL Azure-Datenbank mithilfe von Indexern in einen Azure Search-Index abrufen." 
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
	ms.date="02/08/2016" 
	ms.author="eugenesh"/>

#Verbinden von Azure SQL-Datenbank mit Azure Search mithilfe von Indexern

Azure Search ist ein gehosteter Cloudsuchdienst, mit dem Sie eine überzeugende Suchumgebung einfach bereitstellen können. Bevor Sie suchen können, müssen Sie einen Azure Search-Index mit Daten auffüllen. Wenn sich die Daten in einer Azure SQL-Datenbank befinden, können Sie mit dem neuen Feature **Azure Search-Indexer für Azure SQL-Datenbank** (oder kurz **Azure SQL-Indexer**) in Azure Search den Indizierungsprozess automatisieren. Dies bedeutet, dass Sie weniger Code schreiben und weniger Infrastruktur berücksichtigen müssen.

Derzeit funktioniert der Indexer nur mit Azure SQL-Datenbank, SQL Server auf Azure-VMs und [Azure-DocumentDB](../documentdb/documentdb-search-indexer.md). In diesem Artikel konzentrieren wir uns auf Indexer, die mit Azure SQL-Datenbank arbeiten. Wenn Sie sich Unterstützung für zusätzliche Datenquellen wünschen, geben Sie dazu im [Feedback-Forum für Azure Search Feedback](https://feedback.azure.com/forums/263029-azure-search/).

In diesem Artikel wird die Verwendung von Indexern behandelt, aber wir werden auch Funktionen und Verhaltensweisen genauer untersuchen, die nur bei SQL-Datenbanken (z. B. integrierte Änderungsnachverfolgung) verfügbar sind.

## Indexer und Datenquellen

Um einen Azure SQL-Indexer einzurichten und zu konfigurieren, rufen Sie die [Azure Search-REST-API](http://go.microsoft.com/fwlink/p/?LinkID=528173) zum Erstellen und Verwalten mehrerer **Indexer** und **Datenquellen** auf.

Sie können zum Erstellen und Planen eines Indexers auch die [Indexer-Klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) im [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) oder den Datenimport-Assistenten im [klassischen Azure-Portal](https://portal.azure.com) verwenden.

Eine **Datenquelle** gibt an, welche Daten indiziert werden müssen. Sie legt außerdem die Anmeldeinformationen für den Zugriff auf die Daten sowie die Richtlinien zur Aktivierung von Azure Search fest, um Änderungen an den Daten effizient identifizieren zu können (wie z. B. neue, geänderte oder gelöschte Zeilen). Die Datenquelle wird als unabhängige Ressource definiert, sodass sie von mehreren Indexern verwendet werden kann.

Ein **Indexer** ist die Ressource, die Datenquellen mit Zielsuchindizes verbindet. Ein Indexer kann folgendermaßen verwendet werden:
 
- Eine einmalige Kopie der Daten zum Auffüllen eines Indexes ausführen.
- Einen Index mit Änderungen an der Datenquelle nach einem Zeitplan aktualisieren.
- Ausführung bei Bedarf, um den Index je nach Notwendigkeit zu aktualisieren. 

## Gründe für die Verwendung von Azure SQL-Indexern

Abhängig von verschiedenen Faktoren, die mit den Daten zusammenhängen, kann die Verwendung von Azure SQL-Indexern angebracht oder nicht angebracht sein. Wenn Ihre Daten die folgenden Anforderungen erfüllen, können Sie Azure SQL-Indexer verwenden:

- Alle Daten stammen aus einer einzelnen Tabelle oder Sicht.
	- Wenn die Daten auf mehrere Tabellen verteilt sind, können Sie eine Sicht erstellen und diese Ansicht dann mit dem Indexer verwenden. Bedenken Sie jedoch, dass Sie bei Verwendung einer Sicht nicht die in SQL Server integrierte Änderungerkennung nutzen können. Weitere Informationen finden Sie in diesem Abschnitt. 
- Die in der Datenquelle verwendeten Datentypen werden vom Indexer unterstützt. Es werden die meisten, aber nicht alle SQL-Typen unterstützt. Weitere Informationen finden Sie unter [Zuordnen von Datentypen in Azure Search](http://go.microsoft.com/fwlink/p/?LinkID=528105). 
- Bei Zeilenänderungen werden keine sehr zeitnahen Aktualisierungen des Index benötigt. 
	- Der Indexer kann die Tabelle höchstens alle 5 Minuten erneut indizieren. Wenn sich Ihre Daten häufig ändern und die Änderungen innerhalb von Sekunden oder weniger Minuten im Index widergespiegelt werden müssen, sollten Sie direkt die [Index-API von Azure Search](https://msdn.microsoft.com/library/azure/dn798930.aspx) verwenden. 
- Wenn Sie ein großes Dataset haben und den Indexer nach einem Zeitplan ausführen möchten, können wir anhand Ihres Schemas effizient geänderte (und ggf. gelöschte) Zeilen identifizieren. Weitere Informationen finden Sie unter "Erfassen geänderter und gelöschter Zeilen" weiter unten. 
- Die Größe der indizierten Felder in einer Zeile überschreitet nicht die maximale Größe einer Azure Search-Indizierungsanforderung, die 16 MB beträgt . 

## Erstellen und Verwenden eines Azure SQL-Indexers

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


Sie können die Verbindungszeichenfolge mit der Option `ADO.NET connection string` aus dem [klassischen Azure-Portal](https://portal.azure.com) abrufen:

Dann erstellen Sie den Azure Search-Zielindex, sofern Sie bislang über keinen Index verfügen. Sie können dies von der Azure-[Portal-Benutzeroberfläche](https://portal.azure.com) aus tun oder indem Sie die [Create-Index-API](https://msdn.microsoft.com/library/azure/dn798941.aspx) verwenden. Stellen Sie sicher, dass das Schema des Zielindexes mit dem Schema der Quelltabelle kompatibel ist. Sie finden in der folgende Tabelle Informationen zum Zuordnen der SQL-Datentypen zu Azure Search-Datentypen.

****Zuordnung zwischen SQL-Datentypen und Azure Search-Datentypen

|SQL-Datentyp | Zulässige Ziel-Index-Feldtypen |Hinweise 
|------|-----|----|
|Bit|Edm.Boolean, Edm.String| |
|int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String| |
| bigint | Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| Smallmoney, money decimal numeric | Edm.String| Azure Search unterstützt nicht die Konvertierung von Dezimaltypen in Edm.Double-Typen, da es hierbei zu Genauigkeitsverlusten kommt. |
| char, nchar, varchar, nvarchar | Edm.String<br/>Collection(Edm.String)|Die Umwandlung einer Zeichenfolgenspalte in Collection(Edm.String) erfordert die Verwendung der Version 2015-02-28-Preview der Vorschau-API. Nähere Einzelheiten finden Sie [in diesem Artikel](search-api-indexers-2015-02-28-Preview.md#create-indexer).| 
|smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String| |
|uniqueidentifer | Edm.String | |
|geography | Edm.GeographyPoint | Es werden nur Geography-Instanzen vom Typ POINT mit SRID 4326 (Standard) unterstützt. | | 
|rowversion| N/V |rowversion-Spalten können nicht im Suchindex gespeichert werden, sie können jedoch für die Änderungsnachverfolgung verwendet werden. | |
| time, timespan, binary, varbinary, image, xml, geometry, CLR types | N/V |Nicht unterstützt |

Erstellen Sie schließlich den Indexer, indem Sie ihm einen Namen geben und einen Verweis auf die Datenquelle und den Zielindex hinzufügen:

	POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key
	
	{ 
	    "name" : "myindexer",
	    "dataSourceName" : "myazuresqldatasource",
	    "targetIndexName" : "target index name"
	}

Ein auf diese Weise erstellter Indexer verfügt über keinen Zeitplan. Er wird automatisch einmal ausgeführt, sobald er erstellt worden ist. Sie können ihn jederzeit mithilfe der Anforderung **run indexer** erneut ausführen:

	POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
	api-key: admin-key
 
Möglicherweise müssen Sie Azure Services erlauben, eine Verbindung mit der Datenbank herzustellen. Anleitungen dazu finden Sie unter [Verbinden von Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure).

Verwenden Sie zum Überwachen des Indexerstatus und Ausführungsverlaufs (Anzahl der indizierten Elemente, Fehler usw.) die Anforderung **indexer status**:

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

Der Ausführungsverlauf enthält bis zu 50 der zuletzt abgeschlossenen Ausführungen. Diese sind in umgekehrter chronologischer Reihenfolge sortiert (somit wird die neueste Ausführung als Erstes in der Antwort aufgelistet). Weitere Informationen zur Antwort finden Sie unter [Abrufen des Indexerstatus](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## Ausführen von Indexern nach einem Zeitplan

Sie können den Indexer auch so konfigurieren, dass er regelmäßig nach einem Zeitplan ausgeführt wird. Zu diesem Zweck fügen Sie einfach die **schedule**-Eigenschaft beim Erstellen oder Aktualisieren des Indexers hinzu. Das folgende Beispiel zeigt eine PUT-Anforderung den Indexer, um den zu aktualisieren:

	PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
	Content-Type: application/json
	api-key: admin-key 

	{ 
	    "dataSourceName" : "myazuresqldatasource",
	    "targetIndexName" : "target index name",
	    "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
	}

Der Parameter **interval** ist erforderlich. Das Intervall bezieht sich auf den Zeitraum zwischen dem Start von zwei aufeinander folgenden Indexerausführungen. Das kleinste zulässige Intervall beträgt 5 Minuten. Das längste ist ein Tag. Es muss als XSD-Wert "dayTimeDuration" formatiert sein (eine eingeschränkte Teilmenge eines [ISO 8601-Zeitdauerwerts](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). Das Muster hierfür lautet wie folgt: `P(nD)(T(nH)(nM))`. Beispiele: `PT15M` = alle 15 Minuten, `PT2H` = alle 2 Stunden.

Der optionale Parameter **startTime** gibt an, wann die geplanten Ausführungen beginnen sollen. Wenn er weggelassen wird, wird die aktuelle UTC-Zeit verwendet. Diese Uhrzeitangabe kann in der Vergangenheit liegen. In diesem Fall wird die erste Ausführung so geplant, als wäre der Indexer kontinuierlich seit der mit startTime angegebenen Startzeit ausgeführt worden.

Nur eine Ausführung eines bestimmten Indexers kann zu einem gegebenen Zeitpunkt ausgeführt werden. Wenn ein Indexer bereits ausgeführt wird, wenn der nächste Indexer starten soll, wird die Ausführung übersprungen und beim nächsten Intervall fortgesetzt, sofern kein anderer Indexerauftrag ausgeführt wird.

Betrachten wir ein Beispiel, um dies zu konkreter veranschaulichen. Angenommen Sie, wir haben den folgenden stündlichen Zeitplan konfiguriert:

	"schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Hier geschieht Folgendes:

1. Die erste Indexerausführung beginnt am 1. März 2015 um oder gegen 12:00 Uhr UTC.
1. Angenommen, diese Ausführung dauert 20 Minuten (oder hat eine andere Dauer unter 1 Stunde).
1. Die zweite Ausführung beginnt am 1. März 2015 um oder gegen 1:00 Uhr. 
1. Nehmen wir nun an, dass diese Ausführung länger als eine Stunde dauert (in der Realität wäre dazu eine riesige Anzahl von Dokumenten erforderlich, aber es ist ein hilfreiches Beispiel), z. B. 70 Minuten, sodass sie etwa um 2:10 Uhr abgeschlossen wird.
1. Es ist jetzt 2:00 Uhr und an der Zeit, die dritte Ausführung zu starten. Da die zweite Ausführung von 1: 00 Uhr noch ausgeführt wird, wird die dritte Ausführung allerdings übersprungen. Die dritte Ausführung beginnt um 3 Uhr morgens.

Sie können mithilfe einer **PUT Indexer**-Anforderung einen Zeitplan für einen vorhandenen Indexer hinzufügen, ändern oder löschen.

## Erfassen neuer, geänderter und gelöschter Zeilen

Wenn Sie einen Zeitplan verwenden und die Tabelle eine nicht triviale Anzahl von Zeilen enthält, sollten Sie eine Richtlinie zur Erkennung von Datenänderungen verwenden, damit der Indexer nur die neuen oder geänderten Zeilen abrufen kann, ohne die gesamte Tabelle neu indizieren zu müssen.

### Richtlinie für die integrierte SQL-Änderungsnachverfolgung

Wenn die SQL-Datenbank die [Änderungsnachverfolgung](https://msdn.microsoft.com/library/bb933875.aspx) unterstützt, wird empfohlen, die **Richtlinie für die integrierte SQL-Änderungsnachverfolgung** zu verwenden. Diese Richtlinie ermöglicht die effizienteste Änderungsnachverfolgung und sorgt dafür, dass Azure Search gelöschte Zeilen identifiziert, ohne dass Sie eine explizite "Vorläufig löschen"-Spalte in Ihrer Tabelle angeben müssen.

Die integrierte Änderungsverfolgung wird , ab den folgenden SQL Server-Datenbankversionen unterstützt:
 
- SQL Server 2008 R2 und höher bei Verwendung von SQL Server auf Azure-VMs. 
- Azure SQL-Datenbank V12 bei Verwendung von Azure SQL-Datenbank.

Wenn Sie die Richtlinie für die integrierte SQL-Änderungsnachverfolgung verwenden, geben Sie keine separate Richtlinie für das Erkennen gelöschter Daten an. Die Identifizierung gelöschter Zeilen wird von der Richtlinie bereits unterstützt.

Diese Richtlinie kann nur mit Tabellen verwendet werden; sie kann nicht mit Ansichten verwendet werden. Sie müssen die Änderungsnachverfolgung für die verwendete Tabelle aktivieren, bevor Sie diese Richtlinie verwenden können. Anweisungen finden Sie unter [Aktivieren und Deaktivieren der Änderungsnachverfolgung](https://msdn.microsoft.com/library/bb964713.aspx).

Um diese Richtlinie zu verwenden, erstellen oder aktualisieren Sie die Datenquelle wie folgt:
 
	{ 
	    "name" : "myazuresqldatasource",
	    "type" : "azuresql",
	    "credentials" : { "connectionString" : "connection string" },
	    "container" : { "name" : "table or view name" }, 
	    "dataChangeDetectionPolicy" : {
	       "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
	  }
	}

### Richtlinie zum Erkennen von Änderungen mit oberem Grenzwert

Die Richtlinie für die integrierte SQ- Änderungsnachverfolgung wird zwar empfohlen, Sie können sie jedoch nicht verwendet werden, wenn die Daten in einer Sicht enthalten sind oder wenn Sie mit einer älteren Version von Azure SQL-Datenbank arbeiten. In einem solchen Fall sollten Sie die Richtlinie zum Erkennen von Änderungen mit oberem Grenzwert einsetzen. Diese Richtlinie kann verwendet werden, wenn die Tabelle eine Spalte enthält, die die folgenden Kriterien erfüllt:

- Alle Einfügungen geben einen Wert für die Spalte an. 
- Alle Updates für ein Element ändern auch den Wert der Spalte. 
- Der Wert dieser Spalte wird bei jeder Änderung erhöht.
- Abfragen, die eine `WHERE`-Klausel verwenden, die `WHERE [High Water Mark Column] > [Current High Water Mark Value]` ähnelt, können effizient ausgeführt werden.

Beispielsweise ist eine indizierte **rowversion**-Spalte ein idealer Kandidat für die Spalte mit dem oberen Grenzwert. Um diese Richtlinie zu verwenden, erstellen oder aktualisieren Sie die Datenquelle wie folgt:

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

### Richtlinie zum Erkennen von Löschungen anhand der Spalte „Vorläufig löschen“

Wenn Zeilen aus der Quelltabelle gelöscht werden, sollten Sie diese Zeilen auch aus dem Suchindex löschen. Wenn Sie die Richtlinie für die integrierte SQL-Änderungsnachverfolgung verwenden, müssen Sie sich nicht darum kümmern, weil es automatisch geschieht. Die Richtlinie zum Erkennen von Änderungen mit oberem Grenzwert hilft Ihnen allerdings nicht beim Löschen von Zeilen. Vorgehensweise

Wenn Zeilen physisch aus der Tabelle entfernt werden, haben Sie leider Pech. Es gibt keine Möglichkeit, das Vorhandensein von Datensätzen, die nicht mehr vorhanden sind, durch Schlussfolgerungen abzuleiten. Mit der Technik des "vorläufigen Löschens" können Sie jedoch Zeilen als logisch gelöschte Zeilen markieren, ohne sie aus der Tabelle zu entfernen, indem Sie eine Spalte hinzufügen und Zeilen mithilfe eines Markerwerts in dieser Spalte als gelöscht markieren.

Wenn Sie die Methode des "vorläufigen Löschens" verwenden, können Sie die Richtlinie zum Erkennen von Löschungen anhand der "Vorläufig löschen"-Spalte wie folgt beim Erstellen oder Aktualisieren der Datenquelle angeben:

	{ 
	    …, 
	    "dataDeletionDetectionPolicy" : { 
	       "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
	       "softDeleteColumnName" : "[a column name]", 
	       "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
	    }
	}

Beachten Sie, dass **softDeleteMarkerValue** eine Zeichenfolge sein muss. Verwenden Sie die Zeichenfolgendarstellung des tatsächlichen Werts. Wenn Sie z. B. über eine "integer"-Spalte verfügen, in der gelöschte Zeilen durch den Wert 1 gekennzeichnet sind, verwenden Sie `"1"`; wenn Sie über eine "BIT"-Spalte verfügen, in der gelöschte Zeilen durch den booleschen Wert "true" markiert werden, verwenden Sie `"True"`.

## Anpassen von Azure SQL-Indexer
 
Sie können bestimmte Aspekte des Indexerverhaltens anpassen (z. B. die Batchgröße, wie viele Dokumente übersprungen werden können, bevor eine Indexerausführung fehlschlägt, usw.). Weitere Details finden Sie unter [Anpassen von Azure Search-Indexern](search-indexers-customization.md)

## Häufig gestellte Fragen

**F:** Kann ich Azure SQL-Indexer mit SQL-Datenbanken verwenden, die auf IaaS-VMs in Azure ausgeführt werden?

A: Ja, solange Sie durch Öffnen der entsprechenden Ports zulassen, dass Azure-Dienste eine Verbindung mit der Datenbank herstellen.

**F:** Kann ich Azure SQL-Indexer mit SQL-Datenbanken verwenden, die lokal ausgeführt werden?

A: Dies wird von uns weder empfohlen noch unterstützt, da Sie die Datenbanken dann für den Internetverkehr öffnen müssten.

**F:** Kann ich Azure SQL-Indexer mit anderen Datenbanken als SQL Server in IaaS auf Azure verwenden?

A: Wir unterstützen dieses Szenario nicht, da wir den Indexer mit keinen anderen Datenbanken als SQL Server getestet haben.

**F:** Kann ich mehrere Indexer erstellen, die nach einem Zeitplan ausgeführt werden?

A: Ja. Allerdings kann zu einem gegebenen Zeitpunkt nur ein Indexer auf einem Knoten ausgeführt werden. Wenn mehrere Indexer gleichzeitig ausgeführt werden sollen, sollten Sie den Suchdienst skalieren und mehrere Sucheinheiten einsetzen.

**F:** Wirkt sich die Ausführung eines Indexers auf meine Abfragearbeitsauslastung aus?

A: Ja. Indexer werden auf einem der Knoten im Suchdienst ausgeführt, und die Ressourcen dieses Knotens werden für die Indizierung und das Bearbeiten des Datenverkehrs für Abfragen und andere API-Anforderungen gemeinsam genutzt. Wenn Sie intensive Indizierungs- und Abfragearbeitsauslastungen ausführen und viele 503-Fehler oder zunehmend längere Antwortzeiten auftreten, sollten Sie Ihren Suchdienst skalieren.

<!---HONumber=AcomDC_0211_2016-->