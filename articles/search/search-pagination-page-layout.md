<properties 
	pageTitle="Anordnen von Suchergebnissen auf Seiten in Azure Search" 
	description="Paginierung in Azure Search" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="07/08/2015" 
	ms.author="heidist"/>

#Anordnen von Suchergebnissen auf Seiten in Azure Search#

Dieser Artikel enthält Anleitungen dazu, wie die REST-API für den Azure-Suchdienst zum Implementieren von Standardelementen einer Seite mit Suchergebnissen, z. B. Gesamtanzahl, Dokumentabruf, Sortierreihenfolge und Navigation, verwendet wird.
 
In jedem der unten genannten Fälle werden die seitenbezogenen Optionen, die Daten oder Informationen zu der Seite mit den Suchergebnissen beitragen, über die [Dokument durchsuchen](http://msdn.microsoft.com/library/azure/dn798927.aspx)-Anforderungen angegeben, die an den Azure-Suchdienst gesendet werden. Anforderungen enthalten einen GET-Befehl, Pfad- und Abfrageparameter, denen der Dienst entnimmt, was angefordert wird und wie die Antwort zu formulieren ist.

> [AZURE.NOTE]Eine gültige Anforderung umfasst eine Reihe von Elementen, z. B. Dienst-URL und Pfad, HTTP-Verb, `api-version` und so weiter. Aus Platzgründen wurden die Beispiele verkürzt, um nur die Syntax hervorzuheben, die für die Paginierung wichtig sind. Weitere Einzelheiten zur Anforderungssyntax finden Sie in der Dokumentation zur [REST-API für Azure-Suchdienst](http://msdn.microsoft.com/library/azure/dn798935.aspx) .

## Gesamtanzahl der Treffer und die Seitenanzahl ##

Grundsätzlich werden praktisch auf allen Suchseiten die Gesamtanzahl der von einer Abfrage zurückgegebenen Ergebnisse angezeigt und anschließend die Ergebnisse in kleineren Segmenten zurückgegeben.

![][1]
 
In Azure Search verwenden Sie die Parameter `$count`, `$top` und `$skip` zur Rückgabe dieser Werte. Das folgende Beispiel enthält eine Beispielanforderung für die Gesamtanzahl der Treffer, die als `@OData.count` zurückgegeben wird:

    	GET /indexes/onlineCatalog/docs?$count=true

Dokumenten in Gruppen von jeweils 15 abrufen und ab der ersten Seite auch die Gesamtanzahl der Treffer anzeigen:

		GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

Zur Paginierung der Ergebnisse sind sowohl `$top` als auch `$skip` erforderlich, wobei `$top` angibt, wie viele Elemente in einer Gruppe zurückgegeben werden, und `$skip` angibt, wie viele Elemente übersprungen werden sollen. Im folgenden Beispiel werden auf jeder Seite jeweils die nächsten 15 Elemente angezeigt, wobei die Inkrementschritte durch den Parameter `$skip` angegeben werden.

    	GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

    	GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

    	GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## Layout  ##

Auf einer Seite mit Suchergebnissen empfiehlt es sich, eine Miniaturansicht, eine Teilmenge von Feldern und einen Link zu einer vollständigen Produktseite anzuzeigen.

 ![][2]
 
In Azure Search verwenden Sie `$select` und einen Lookup-Befehl, um diese Umgebung zu implementieren.

So wird eine Teilmenge von Feldern für ein gekacheltes Layout zurückzugeben:

    	GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Bild- und Mediendateien können nicht direkt durchsucht werden und sollten auf einer anderen Speicherplattform gespeichert werden, z. B. Azure Blob-Speicher, um die Kosten zu senken. Definieren Sie im Index und in den Dokumenten ein Feld, das die URL-Adresse des externen Inhalts enthält. Sie können das Feld dann als Bildverweis verwenden. Die URL zum Bild sollte im Dokument enthalten sein.

Zum Abrufen der Seite mit einer Produktbeschreibung für ein **onClick** -Ereignis verwenden Sie [Dokument suchen](http://msdn.microsoft.com/library/azure/dn798929.aspx), um den Schlüssel des abzurufenden Dokuments zu übergeben. Der Schlüssel hat den Datentyp `Edm.String`. In diesem Beispiel lautet er *246810*.
   
    	GET /indexes/onlineCatalog/docs/246810

## Sortieren nach Relevanz, Bewertung oder Preis ##

Oft wird für die Sortierreihenfolge standardmäßig Relevanz festgelegt, aber es ist üblich, alternative Sortierreihenfolgen einfach zur Verfügung zu stellen, damit die Benutzer die vorhandenen Ergebnisse schnell in einer anderen Reihenfolge sortieren können.

 ![][3]

In Azure Search basiert die Sortierung auf dem `$orderby`-Ausdruck für alle Felder, die als `"Sortable": true.` indiziert werden.

Die Relevanz ist eng mit Bewertungsprofilen verknüpft. Sie können die Standardbewertung verwenden, bei der die Reihenfolge der Ergebnisse anhand von Textanalyse und Statistiken festgelegt wird, wobei Dokumente mit mehr oder höheren Übereinstimmungen mit einem Suchbegriff eine höhere Punktzahl erhalten.

Alternative Sortierreihenfolgen werden in der Regel **onClick**-Ereignissen zugeordnet, die eine Methode aufrufen, welche die Sortierreihenfolge erstellt. Angenommen, das folgende Seitenelement sei gegeben:

 ![][4]

Sie erstellen dann eine Methode, die die ausgewählten Sortieroption als Eingabe akzeptiert und eine sortierte Liste für die Kriterien zurückgibt, die mit dieser Option verknüpft sind.

 ![][5]
 
> [AZURE.NOTE]Die Standardbewertung ist zwar für viele Szenarien ausreichend ist, aber es wird empfohlen, stattdessen die Relevanz anhand eines benutzerdefinierten Bewertungsprofil zu ermitteln. Ein benutzerdefiniertes Bewertungsprofil bietet Ihnen eine Möglichkeit, Elementen, die für Ihr Unternehmen sinnvoller sind, eine höhere Priorität zuzuordnen. Weitere Informationen finden Sie unter [Hinzufügen eines Bewertungsprofil](http://msdn.microsoft.com/library/azure/dn798928.aspx).

## Facettennavigation ##

Ergebnisseiten bieten üblicherweise eine Suchnavigation, die sich häufig am seitlichen oder oberen Rand der Seite befindet. In Azure Search ermöglicht die Facettennavigation eine selbstgesteuerte Suche, die auf vordefinierten Filtern basiert. Nähere Informationen finden Sie unter [Facettennavigation in Azure Search](search-faceted-navigation.md).

## Filter auf Seitenebene ##

Wenn Ihr Lösungsentwurf dedizierte Suchseiten für bestimmte Inhaltstypen enthält (z. B. eine Anwendung für den Online-Einzelhandel mit einer Liste von Abteilungen am oberen Rand der Seite), dann können Sie einen Filterausdruck zusammen mit einem **onClick**-Ereignis einfügen, um eine Seite in einem ungefilterten Zustand zu öffnen.

Sie können einen Filter mit oder ohne Suchbegriff senden. Beispielsweise wird mit der folgenden Anforderung nach Markenname gefiltert, wobei nur die dem Filter entsprechenden Dokumente zurückgegeben werden.

    	GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Weitere Informationen zu `$filter`-Ausdrücken finden Sie unter [Dokumente durchsuchen (Azure Search-API)](http://msdn.microsoft.com/library/azure/dn798927.aspx) .

## Weitere Informationen ##

- [REST-API für Azure Suchdienst](http://msdn.microsoft.com/library/azure/dn798935.aspx)
- [Indexvorgänge](http://msdn.microsoft.com/library/azure/dn798918.aspx)
- [Dokumentvorgänge](http://msdn.microsoft.com/library/azure/dn800962.aspx)
- [Videos und Lernprogramme zu Azure Search](http://msdn.microsoft.com/library/azure/dn818681.aspx)
- [Facettennavigation in Azure Search](search-faceted-navigation.md)


<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png

<!---HONumber=July15_HO2-->