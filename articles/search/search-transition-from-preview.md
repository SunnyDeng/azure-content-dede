<properties 
	pageTitle="Umstellung von der Vorschau-API-Version = 2014* auf API-Version = 2015 *" 
	description="Erfahren Sie mehr über die wichtigen Änderungen und das Migrieren von Code, der für 2014-07-31-preview oder 2014-10-20-preview geschrieben wurde, auf Azure Search, api-version=2015-02-28." 
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

#Umstellung von der Vorschau-API-Version = 2014* auf API-Version = 2015 *#

Die folgende Anleitung ist für Kunden vorgesehen, die benutzerdefinierte Anwendungen mit den Vorschauversionen von Azure Search erstellt haben und jetzt auf die allgemein verfügbare Version 2015-02-28 migrieren.

Als Benutzer einer Vorschauversion haben Sie möglicherweise eine dieser älteren Vorschauversionen verwendet:

- [2014-07-31-Preview](search-api-2014-07-31-preview.md)
- [2014-10-20-Preview](search-api-2014-10-20-preview.md)

Da Azure Search jetzt allgemein verfügbar ist, empfehlen wir die Umstellung auf neuere Versionen: 2015-02-28 ist die offizielle API-Version der allgemein verfügbaren Version von Azure Search. Diese Version ist auf [MSDN](https://msdn.microsoft.com/library/azure/dn798933.aspx) dokumentiert.

Zudem führen wir gerade die nächste Vorschauversion [2015-02-28-Preview](search-api-2015-02-28-preview.md) mit Funktionen ein, die sich noch in der Entwicklungsphase befinden. Sie können uns entweder in den [Azure Search-Foren](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch) oder auf unserer [Feedback-Seite](http://feedback.azure.com/forums/263029-azure-search) Feedback zur Vorschauversion der API geben.

###Checkliste für die Migration###

- Überprüfen Sie die Hinweise zu wichtigen Änderungen, um zu ermitteln, ob Ihre Lösung betroffen ist.
- Ändern Sie die API-Version für die gesperrte Version in `2015-02-28`. Diese Version unterliegt einer Vereinbarung zum Servicelevel (SLA). Wenn Probleme auftreten, können Sie sie schneller beheben.
- Erstellen, Bereitstellen, Testen. Abgesehen von den wichtigen Änderungen, sollte das Suchverhalten zu 100 % gleichwertig sein.
- Einführen in die Produktionsumgebung.
- Bewerten Sie die neuen Features hinsichtlich der Einführung künftiger Features. Ändern Sie die Version wieder in 2015-02-28-Preview, wenn Sie die Microsoft Natural Language-Prozessoren oder `morelikethis` testen möchten.

##Wichtige Änderungen in api-version = 2015*##

Die erste Version der API enthielt ein Feature für die automatische Vervollständigung bzw. für Eingabevorschläge. Es war zwar nützlich, aber begrenzt auf den Präfixabgleich, wobei nur nach den ersten Zeichen im Suchbegriff gesucht und der Abgleich weiterer Zeichen im Feld nicht unterstützt wurde. Dies wurde durch eine boolesche Eigenschaft namens `suggestions` implementiert, die auf `true` festgelegt wurde, wenn der Präfixabgleich für ein bestimmtes Feld aktiviert werden sollte.

Diese ursprüngliche Implementierung ist jetzt veraltet, da sie durch ein neues `Suggesters`-Konstrukt ersetzt wurde, das in der [Index](https://msdn.microsoft.com/library/azure/dn798941.aspx)-Funktion definiert ist und die Infix- und Fuzzyabgleich bereitstellt. Wie die Namen bereits andeuten, bieten Infix- und Fuzzyabgleich eine viel breitere Palette von Vergleichsfunktionen. Der Infixabgleich umfasst das Präfix, weil auch hier die Anfangszeichen verglichen werden. Der Abgleich wird jedoch auf die restliche Zeichenfolge ausgeweitet.

Wir entschieden uns dafür, die vorherige Implementierung (die boolesche Eigenschaft) einzustellen. Das bedeutet, dass sie in beiden 2015-Versionen nicht mehr verfügbar ist und dass keine Abwärtskompatibilität besteht, damit sie von Kunden, die neuere Lösungen erstellen, nicht versehentlich verwendet werden kann. Bei Verwendung von `2015-02-28` oder `2015-02-28-Preview` müssen Sie das neue `Suggesters` -Konstrukt verwenden, um Abfragen zu Eingabevorschlägen zu aktivieren.

##Portieren vorhandenen Codes##

Die Suggestions-Eigenschaft ist die einzige bedeutende Änderung. Wenn Sie diese Eigenschaft nicht verwendet haben, können Sie die `api-version` von `2014-07-31-Preview` oder `2014-10-20-Preview` in `2015-02-28` ändern, und die Anwendung dann neu erstellen und erneut bereitstellen. Die Anwendung funktioniert dann wie zuvor.

Für benutzerdefinierte Anwendungen, die Vorschläge implementiert haben, sollten Sie Folgendes tun:

1. Aktualisieren Sie alle NuGet-Pakete.
1. Ändern Sie die API-Version in `2015-02-28`. Wenn Sie das folgende Codebeispiel verwenden, ist die API-Version in der **AzureSearchHelper**-Klasse enthalten.
1. Löschen Sie das `Suggestions={true | false}`-Attribut aus dem JSON-Schema, das den Index definiert.
1. Fügen Sie ein Konstrukt am Ende des Index für `Suggesters` ein (wie im Abschnitt [Nachher](#after) gezeigt).
1. Stellen Sie sicher, dass Sie den Dienst veröffentlichen können (möglicherweise müssen Sie den Index umbenennen, um Namenskonflikte zu vermeiden).
1. Erstellen Sie die Lösung neu, und stellen Sie sie in einer Testumgebung bereit.
1. Führen Sie alle Testfälle aus, um sicherzustellen, dass sich die Lösung erwartungsgemäß verhält.
1. Einführen in die Produktionsumgebung.

Im Codebeispiel aus dem [Adventure Works-Beispiel auf Codeplex](https://azuresearchadventureworksdemo.codeplex.com/) wird die ursprüngliche `Suggestions`-Implementierung verwendet. Sie sollten dieses Beispiel verwenden, um die Codemigration anhand von Beispielcode zu üben.

Im folgenden Abschnitt werden eine [Vorher](#before)- und eine [Nachher](#after)-Implementierung von Vorschlägen dargestellt. Sie können die **CreateCatalogIndex()**-Methode durch die Version im Abschnitt [Nachher](#after) ersetzen und die Lösung dann erstellen und bereitstellen, um die neuen Funktionen auszuprobieren.

<a name="before"></a>
###Vorher###

Wie Sie sehen, wird für jedes Feld eine boolesche Eigenschaft namens `Suggestions` festgelegt. Löschen Sie alle `Suggestions`-Attribute.

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false }
                }
            };

<a name="after"></a>
###Nach###

In der migrierten Schemadefinition wird die `Suggestions`-Eigenschaft weggelassen, und am Ende wird ein `Suggesters`-Konstrukt hinzugefügt.

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true }
                },
                suggesters = new[]
                {
                new {
                    name = "sg",
                    searchMode = "analyzingInfixMatching",
                    sourceFields = new []{"name", "productNumber", "categoryName", "modelName", "description"}
                    }
                 }
            };

##Bewerten der neuen Features und Konzepte##

Nachdem Sie die Lösung portiert und sichergestellt haben, dass sie wie erwartet ausgeführt wird, können Sie diese Links verwenden, um sich über die neuen Features zu informieren.

- [Azure Search ist allgemein verfügbar (Blogbeitrag)](http://go.microsoft.com/fwlink/p/?LinkId=528211)
- [Neuerungen im aktuellen Update für Azure Search](search-latest-updates.md)
- [Was ist Azure Search?](search-what-is-azure-search.md)

##Hier erhalten Sie Hilfe##

Die API-Version `2015-02-28` unterliegt einer Vereinbarung zum Servicelevel (SLA). Verwenden Sie die Support-Optionen und Links auf [dieser Seite](../support/options/), um ein Support-Ticket anzulegen.

 

<!---HONumber=August15_HO6-->