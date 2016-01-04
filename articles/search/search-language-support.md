<properties
   pageTitle="Erstellen eines Index für Dokumente in mehreren Sprachen in Azure Search | Microsoft Azure | Gehosteter Cloudsuchdienst"
   description="Azure Search unterstützt 56 Sprachen und nutzt Sprachanalysen mit Lucene- und Natural Language Processing-Technologie von Microsoft."
   services="search"
   documentationCenter=""
   authors="yahnoosh"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="10/19/2015"
   ms.author="jlembicz"/>

# Erstellen eines Index für Dokumente in mehreren Sprachen in Azure Search
> [AZURE.SELECTOR]
- [Portal](search-language-support.md)
- [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
- [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)

Sie können die Vorteile von Sprachanalysen einfach nutzen, indem Sie eine Eigenschaft für ein durchsuchbares Feld in der Indexdefinition festlegen. Diesen Schritt können Sie jetzt im Portal ausführen.

Nachfolgend finden Sie Screenshots der Blätter des klassischen Azure-Portals für Azure Search, über die Benutzer ein Indexschema definieren können. Auf diesem Blatt können Benutzer alle Felder erstellen und die Analyseeigenschaften für die einzelnen Felder festlegen.

> [AZURE.NOTE]Eine Sprachanalyse kann nur während der Felddefinition festgelegt werden, also nur beim Erstellen eines neuen Index und beim Hinzufügen eines neuen Felds zu einem vorhandenen Index. Stellen Sie sicher, dass Sie beim Erstellen des Felds alle Attribute, einschließlich der Analyse, vollständig angeben. Sie können die Attribute nicht mehr bearbeiten und den Analysetyp nicht mehr ändern, sobald das Feld festgelegt wurde.

1. Melden Sie sich beim [klassischen Azure-Portal](https://portal.azure.com) an, und öffnen Sie das Dienstblatt für Ihren Suchdienst.
2. Klicken Sie oben im Dienstdashboard auf **Index hinzufügen**, um einen neuen Index zu beginnen, oder öffnen Sie einen vorhandenen Index, um eine Analyse für neue Felder festzulegen, die Sie einem vorhandenen Index hinzufügen.
3. Das Blatt „Felder“ mit Optionen zum Festlegen des Indexschemas wird angezeigt. Hier sehen Sie auch die Registerkarte für die Analyse, über die Sie eine Sprachanalyse auswählen.
4. Beginnen Sie unter „Felder“ mit einer Felddefinition, indem Sie einen Namen angeben, den Datentyp auswählen und Attribute festlegen. Mit diesen Attributen geben Sie u. a. an, dass das Feld für die Volltextsuche verwendet, in Suchergebnissen abgerufen, in Facettennavigationsstrukturen verwendet und sortiert werden kann. 
5. Bevor Sie mit dem nächsten Feld fortfahren, öffnen Sie die Registerkarte **Analyse**. 
6. Führen Sie einen Bildlauf zu dem Feld durch, das Sie definieren. 
7. Wenn Sie das Feld noch nicht für die Suche gekennzeichnet haben, aktivieren Sie jetzt das Kontrollkästchen, um es als durchsuchbar zu kennzeichnen.
8. Klicken Sie auf den Bereich „Analyse“, um die Liste der verfügbaren Analysen anzuzeigen.
9. Wählen Sie die zu verwendende Analyse aus.

![][1] *Klicken Sie zum Auswählen einer Analyse auf dem Blatt „Felder“ auf die Registerkarte „Analyse“.*

![][2] *Wählen Sie für jedes Feld eine der unterstützten Analysen aus.*

Standardmäßig verwenden alle durchsuchbaren Felder die sprachunabhängige [Lucene-Standardanalyse](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html). Die vollständige Liste der unterstützten Analysen finden Sie unter [Sprachunterstützung in Azure Search](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Sobald die Sprachanalyse für ein Feld ausgewählt wurde, wird sie für jede Indizierungs- und Suchanfrage für dieses Feld verwendet. Wenn eine Abfrage mithilfe verschiedener Analysen für mehrere Felder ausgegeben wird, wird sie von der richtigen Analyse unabhängig für das jeweilige Feld verarbeitet.

Viele webbasierte und mobile Anwendungen werden von Benutzern auf der ganzen Welt in unterschiedlichen Sprachen genutzt. Es ist möglich, einen Index für ein derartiges Szenario festzulegen. Hierzu muss ein Feld für jede unterstützte Sprache erstellt werden.

![][3] *Indexdefinition mit einem Beschreibungsfeld für jede unterstützte Sprache*

Wenn die Sprache des Agents, der eine Abfrage ausgibt, bekannt ist, kann eine Suchabfrage mit dem **SearchFields**-Abfrageparameter auf ein bestimmtes Feld beschränkt werden. Die folgende Abfrage wird nur für die Beschreibung in polnischer Sprache ausgegeben:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2015-02-28`

Manchmal ist die Sprache des Agents, der eine Abfrage ausgibt, nicht bekannt. In diesem Fall kann die Abfrage für alle Felder gleichzeitig ausgegeben werden. Bei Bedarf kann die Ausgabe der Ergebnisse in einer bestimmten Sprache mithilfe von [Bewertungsprofilen](https://msdn.microsoft.com/library/azure/dn798928.aspx) festgelegt werden. Im folgenden Beispiel erhalten in der Beschreibung in englischer Sprache gefundene Übereinstimmungen eine höhere Bewertung als Übereinstimmungen in polnischer und französischer Sprache:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2015-02-28`

Wenn Sie .NET-Entwickler sind, können Sie Sprachanalysen mit dem [Azure Search .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Search/0.13.0-preview) konfigurieren. Die neueste Version unterstützt auch die Microsoft-Sprachanalysen.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png

<!---HONumber=AcomDC_1203_2015-->