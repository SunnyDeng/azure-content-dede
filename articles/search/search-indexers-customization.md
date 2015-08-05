<properties 
	pageTitle="Anpassen von Azure Search-Indexern" 
	description="Erfahren Sie, wie Sie die Einstellungen und Richtlinien von Azure Search-Indexern anpassen können." 
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
	ms.date="07/08/2015" 
	ms.author="eugenesh"/>

#Anpassen von Azure Search-Indexern#

In diesem Artikel erfahren Sie, wie Sie mit Azure Search-Indexern die folgenden Szenarios implementieren können:

- Umbenennen von Feldern zwischen einer Datenquelle und einem Zielindex 
- Transformieren von Zeichenfolgen aus einer Datenbanktabelle in Zeichenfolgenauflistungen
- Wechseln der Änderungserkennungsrichtlinie in einer Datenquelle 
- Durchführen der URL-Codierung von Dokumentschlüsseln, die URL-unsichere Zeichen enthalten 
- Tolerieren von Fehlern beim Indizieren von Dokumenten 

Wenn Sie nicht mit Azure Search-Indexern vertraut sind, sollten Sie zunächst die folgenden Artikel lesen:

- [Verbinden von Azure SQL-Datenbank mit Azure Search mithilfe von Indexern](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Herstellen einer Verbindung zwischen DocumentDB und Azure Search unter Verwendung von Indexern](../documentdb/documentdb-search-indexer.md)
- [.NET SDK mit Unterstützung für Indexer](https://msdn.microsoft.com/library/dn951165.aspx) oder 
- [Indexervorgänge (Azure-Suchdienst-REST-API)](https://msdn.microsoft.com/library/azure/dn946891.aspx)

##Umbenennen von Feldern zwischen einer Datenquelle und einem Zielindex##

**Feldzuordnungen** sind Eigenschaften, die Unterschiede zwischen Felddefinitionen ausgleichen. Die häufigsten Beispiele finden sich in Feldnamen, die gegen Azure Search-Benennungsregeln verstoßen. Nehmen wir als Beispiel eine Quelltabelle, in der ein oder mehrere Feldnamen mit einem führenden Unterstrich beginnen (z. B. `_id`). Da Feldnamen in Azure Search nicht mit einem Unterstrich beginnen dürfen, muss das Feld umbenannt werden.

Im folgenden Beispiel wird veranschaulicht, wie ein Indexer so geändert wird, dass mit einer Feldzuordnung das Feld `_id` der Datenquelle in das Feld `id` im Zielindex "umbenannt" wird:

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
    } 

**HINWEIS**: Um Feldzuordnungen nutzen zu können, müssen Sie Version 2015-02-28-Preview der Vorschau-API verwenden.

Sie können mehrere Feldzuordnungen angeben.

	"fieldMappings" : [ 
		{ "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
	 ]

Beachten Sie bei Quell- und Zielfeldnamen die Groß-und Kleinschreibung.

##Transformieren von Zeichenfolgen aus einer Datenbanktabelle in Zeichenfolgenauflistungen##

Feldzuordnungen können auch verwendet werden, um Quellfeldwerte mithilfe von *Zuordnungsfunktionen* umzuwandeln.

Mit einer solchen Funktion (`jsonArrayToStringCollection`) wird eine als JSON-Array formatierte Zeichenfolge in ein "Collection(Edm.String)"-Feld im Zielindex transformiert. Die Funktion ist insbesondere zur Verwendung mit dem Azure SQL-Indexer vorgesehen, da SQL über keinen nativen Datentyp "Sammlung" verfügt. Sie können die Funktion wie folgt verwenden:

	"fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Wenn das Quellfeld beispielsweise die Zeichenfolge `["red", "white", "blue"]` enthält, wird das Zielfeld vom Typ `Collection(Edm.String)` mit drei Werten gefüllt: `"red"`, `"white"` und `"blue"`.

Beachten Sie, dass die `targetFieldName`Eigenschaft optional ist. Wenn Sie keinen Wert angeben, wird der `sourceFieldName`-Wert verwendet.

##Wechseln der Änderungserkennungsrichtlinie in einer Datenquelle##
  
In Azure Search wird die Auswahl der verwendeten Änderungserkennungsrichtlinie weitgehend dadurch bestimmt, welche Richtlinie durch die Datenquelle und das Datenschema unterstützt wird. Im Laufe der Zeit, insbesondere wenn Sie Datenbanken aktualisieren oder migrieren, möchten Sie möglicherweise zu einer anderen Änderungserkennungsrichtlinie wechseln. Wenn Sie beispielsweise gerade Ihre Azure SQL-Datenbank auf eine neuere Version aktualisiert haben, die die integrierte Änderungsnachverfolgung unterstützt, möchten Sie jetzt von der Richtlinie für die Obergrenzenmarkierung zur Richtlinie für die integrierte Änderungsnachverfolgung wechseln. Oder vielleicht möchten Sie eine andere Spalte als Obergrenzenmarkierung verwenden.

Wenn Sie einfach den REST-API-Vorgang "PUT datasource" aufrufen, um Ihre Datenbank zu aktualisieren, wird möglicherweise eine Antwort mit dem Fehlercode 400 und einer Fehlermeldung ähnlich der folgenden zurückgegeben:


	"Change detection policy cannot be changed for data source '…' because indexer '…' references this data source and has a non-empty change tracking state. You can use Reset API to reset the indexer's change tracking state, and retry this call."

 Sie würden sich wahrscheinlich fragen, was dies bedeutet und wie Sie den Fehler umgehen können. Dieser Fehler tritt auf, weil Azure Search einen internen Zustand verwaltet, der mit der Änderungserkennungsrichtlinie verknüpft ist. Bei Änderungen an der Richtlinie wird der vorhandene Zustand ungültig, da er für die neue Richtlinie nicht gilt. Das bedeutet, dass der Indexer die Datenquelle mithilfe der neuen Richtlinie von Grund auf neu indizieren muss. Dies hat möglicherweise auch Auswirkungen auf Sie (z. B. zusätzliche Auslastung der Datenbank oder zusätzliche Bandbreitengebühren für das Netzwerk). Daher werden Sie in Azure Search aufgefordert, den [API-Vorgang "Indexer zurücksetzen"](https://msdn.microsoft.com/library/azure/dn946897.aspx) aufzurufen, um den mit der aktuellen Änderungserkennungsrichtlinie verknüpften Zustand zurückzusetzen. Anschließend kann die Richtlinie mit einem regulären "PUT datasource"-Aufruf geändert werden. Natürlich könnte die Zurücksetzung in Azure Search automatisch erfolgen, wir fanden es jedoch wichtig, dass Sie sich ausdrücklich mit den Auswirkungen vertraut machen, die sich aus dem Aufruf des API-Vorgangs "Indexer zurücksetzen" ergeben.

##Durchführen der URL-Codierung von Dokumentschlüsseln, die URL-unsichere Zeichen enthalten##

Azure Search schränkt die Zeichen in einem Dokumentschlüsselfeld auf URL-sichere Zeichen ein, damit Benutzer Dokumente nach den zugehörigen Schlüsseln suchen können. Was geschieht also, wenn die zu indizierenden Dokumente URL-unsichere Zeichen im Schlüsselfeld enthalten? Wenn Sie Dokumente mit einem Client-SDK oder einer REST-API selbst indizieren, können Sie die URL-Codierung der Schlüssel durchführen. Mithilfe von Indexern können Sie Azure Search anweisen, die URL-Codierung der Schlüssel durchzuführen. Legen Sie dazu beim Erstellen oder Aktualisieren des Indexers den **base64EncodeKeys**-Parameter auf `true` fest:

    PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "base64EncodeKeys": true }
    }

Ausführliche Informationen zur Codierung finden Sie in diesem [MSDN-Artikel](http://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx).

HINWEIS: Wenn Sie eine Suche oder Filterung nach Schlüsselfeldern durchführen möchten, müssen Sie die in den Anforderungen verwendeten Schlüssel auf ähnliche Weise codieren, damit die Anforderungen jeweils den im Suchindex gespeicherten codierten Werten entsprechen.


##Tolerieren von Fehlern beim Indizieren von Dokumenten##

In der Standardeinstellung beendet ein Azure Search-Indexer die Indizierung, sobald beim Indizieren bei nur einem Dokument Fehler auftreten. Abhängig von Ihrem Szenario können Sie festlegen, dass einige Fehler toleriert werden (wenn Sie z. B. die gesamte Datenquelle wiederholt neu indizieren). Azure Search umfasst zwei Indexerparameter zum Feinabstimmen dieses Verhaltens:

- **maxFailedItems**: Die maximale Anzahl der Elemente, deren Indizierung fehlschlagen darf. Wenn diese Anzahl überschritten wird, gilt die Ausführung des Indexers als fehlgeschlagen. Der Standardwert ist 0.
- **maxFailedItemsPerBatch**: Die maximale Anzahl der Elemente, deren Indizierung in einem einzelnen Batch fehlschlagen darf. Wenn diese Anzahl überschritten wird, gilt die Ausführung des Indexers als fehlgeschlagen. Der Standardwert ist 0.

Sie können diese Werte jederzeit ändern, indem Sie einen oder beide Parameter beim Erstellen oder Aktualisieren des Indexers angeben:

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
	Content-Type: application/json
	api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }
    }

Selbst wenn Sie die Tolerierung einiger Fehler festlegen, werden durch den [API-Vorgang "Indexerstatus abrufen"](https://msdn.microsoft.com/library/azure/dn946884.aspx) dennoch Informationen zu fehlgeschlagenen Dokumenten zurückgeben.

Damit sind wir am Ende angelangt. Wenn Sie Ideen oder Vorschläge für zukünftige Artikel haben, senden Sie uns einen Tweet mit dem Hashtag #AzureSearch, oder geben Sie Ihre Anregungen auf unserer [UserVoice-Seite](http://feedback.azure.com/forums/263029-azure-search) ein.
 

<!---HONumber=July15_HO4-->