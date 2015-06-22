<properties 
	pageTitle="Azure-Suchdienst-REST-API, Version 2014-07-31-Preview" 
	description="Azure-Suchdienst-REST-API, Version 2014-07-31-Preview" 
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
	ms.date="02/11/2015" 
	ms.author="heidist"/>

# Azure-Suchdienst-REST-API, Version 2014-07-31-Preview-Nr.

Dieses Dokument beschreibt die Version **2014-07-31-Preview** der Azure-Suchdienst-REST-API. Dies ist die REST-API für die Vorschau von Azure Search. 

Zu den anderen API-Inhalten, die sich auf diese Version beziehen, gehören die folgenden:

- [Bewertungsprofile (Azure-Suchdienst-REST-API: 2014-07-31-Preview)](../search-api-scoring-profiles-2014-07-31-preview/)

Die Dokumentation für die veröffentlichte Version der REST-API von Azure Search finden Sie auf MSDN. Weitere Informationen finden Sie unter [Azure-Suchdienst-REST-API](http://msdn.microsoft.com/library/azure/dn798935.aspx).

##Info zur Dienst-REST-API##

Azure Search ist ein cloudbasierter Dienst, der zum Erstellen benutzerdefinierter Suchanwendungen verwendet werden kann. 
Azure Search verfügt über die Konzepte von  *search services* und  *indexes*, wobei ein Suchdienst einen Index oder mehrere Indizes enthält. Ihr Suchdienst wird eindeutig durch einen vollqualifizierten Domänennamen bezeichnet (z. B.: `mysearchservice.search.windows.net`). Bei der Bereitstellung des Diensts wird ein API-Schlüssel generiert, der zum Authentifizieren von Anforderungen für Ihren Azure-Suchdienst verwendet wird.

Zwei Arten von Aktionen können mit dem Azure-Suchdienst ausgeführt werden:

- **Indexverwaltung**: Dies schließt Verwaltungsaufgaben ein, die für einen Suchdienst oder Suchindex ausgeführt werden. 

- **Dokumentaktionen**: Diese Aktionen fragen den Korpus auf einen bestimmten Index ab und verwalten ihn. 

Die in diesem Abschnitt dokumentierten APIs bieten Zugriff auf Vorgänge für Suchdaten, wie etwa Erstellung und Auffüllung von Indizes, Hochladen von Dokumenten und Abfragen. Beachten Sie beim Aufrufen der API die folgenden Punkte:

- Alle APIs sind als HTTP-Anforderungen und -Antworten im OData-JSON-Format definiert.

- Alle APIs müssen im Header oder der Abfragezeichenfolge mit einem  `api-key` versehen sein, wie in den Hinweisen unten beschrieben.

- Alle APIs müssen über HTTPS (am Standardport 443) ausgegeben werden.

- Alle API-Anforderungen müssen den Parameter  `api-version` für Abfragezeichenfolgen beinhalten. Sein Wert muss auf die Version des aktuellen Dienstrelease festgelegt sein, wie im folgenden Beispiel dargestellt:

    GET /indexes?api-version=2014-07-31-Preview

- Alle API-Anforderungen können optional den HTTP-Header  `Accept` festlegen. Wenn kein Wert festgelegt ist, wird  `application/json` als Standardwert verwendet.

Für die Dienstverwaltung steht eine separate API zur Verfügung. Beispiele von Vorgängen zur Dienstverwaltung sind etwa die Bereitstellung des Diensts oder das Ändern seiner Kapazität. Weitere Informationen zu dieser API finden Sie unter "Azure Search Management-REST-API".

### Endpunkt ###

Der Endpunkt für Dienstvorgänge ist die URL des von Ihnen bereitgestellten Azure-Suchdiensts.: https://<yourService>.search.windows.net.


### Versionen ###

Es gibt mehrere API-Versionen für Azure Search. Wenn Sie Azure Search für die Verwendung mit einer Produktionsanwendung bewerten, sollten Sie die für jeden API-Satz veröffentlichten Versionsinformationen durchlesen. Hinweise zur Auswahl einer bestimmten Version finden Sie unter [Search Service-Versionsverwaltung](http://msdn.microsoft.com/library/azure/dn864560.aspx).


<a name="Authentication"></a>
### Authentifizierung und Zugriffskontrolle###

Die Authentifizierung bei einem Azure-Suchdienst erfordert zwei Informationen: eine Suchdienst-URL und einen  `api-key`. Die  `api-keys` werden beim Erstellen des Diensts generiert und können bei Bedarf nach der Bereitstellung des Diensts erneut generiert werden. Ein  `api-key` ist entweder ein Administratorschlüssel, der Zugriff auf alle Vorgänge erteilt, oder ein Abfrageschlüssel, der nur Abfrageanforderungen authentifiziert. Pro Dienst werden 2 Administratorschlüssel und bis zu 50 Abfrageschlüssel verwendet.

Die Zugriffssteuerung ist auf die Dienstverwaltung mithilfe der rollenbasierten Zugriffssteuerung (RBAC) beschränkt, die im Azure-Vorschauportal bereitsteht. Rollen werden verwendet, um Zugriffsebenen für die Dienstverwaltung festzulegen. Beispielsweise ist das Anzeigen des Administratorschlüssels auf die Rollen "Besitzer" und "Mitwirkender" beschränkt, während das Anzeigen des Dienststatus für Mitglieder jeder Rolle möglich ist.

Der Zugriff auf die auf einem Suchdienstendpunkt durchgeführten Datenvorgänge, einschließlich Indexverwaltung, Indexauffüllung und Abfragen erfolgt ausschließlich über  `api-keys`. RBAC betrifft weder index- noch dokumentbezogene Vorgänge. Weitere Informationen über  `api-keys` oder RBAC in Azure Search finden Sie unter [Verwalten Ihres Suchdiensts auf Microsoft Azure](search-manage.md).

**Hinweis**: Im allgemeinen wird es aus Sicherheitsgründen als schlechte Praxis angesehen, sensible Daten, wie den  `api-key`, im Anforderungs-URI zu übergeben. Aus diesem Grund akzeptiert Azure Search nur einen Abfrageschlüssel als  `api-key` in der Abfragezeichenfolge, und Sie sollten das geschilderte Vorgehen vermeiden, es sei denn, Sie möchten den Inhalt Ihres Index öffentlich verfügbar machen. Stattdessen sollten Sie Ihren  `api-key` als Anforderungsheader übergeben.

###Zusammenfassung der APIs###

Die API des Azure-Suchdiensts unterstützt zwei Syntaxen für die Entitätssuche. In der folgenden Liste sind sowohl die einfache als auch die alternative OData-Syntax dargestellt.

[Index erstellen](#CreateIndex)

    POST /indexes?api-version=2014-07-31-Preview

[Index aktualisieren](#UpdateIndex)

    PUT /indexes/[indexname]?api-version=2014-07-31-Preview

[Index abrufen](#GetIndex)

    GET /indexes/[indexname]?api-version=2014-07-31-Preview

[Indizes auflisten](#ListIndexes)

    GET /indexes?api-version=2014-07-31-Preview

[Indexstatistik abrufen](#GetIndexStats)

    GET /indexes/[indexname]/stats?api-version=2014-07-31-Preview

[Index löschen](#DeleteIndex)

    DELETE /indexes/[indexname]?api-version=2014-07-31-Preview

[Daten in einem Index hinzufügen, löschen und aktualisieren](#AddOrUpdateDocuments)

    POST /indexes/[indexname]/docs/index?api-version=2014-07-31-Preview 

[In Dokumenten suchen](#SearchDocs)

    GET /indexes/[indexname]/docs?[abfrageparameter]

[Dokument nachschlagen](#LookupAPI)

     GET /indexes/[indexname]/docs/[key]?[abfrageparameter]

[Dokumente zählen](#CountDocs)

    GET /indexes/[indexname]/docs/$count?api-version=2014-07-31-Preview

[Vorschläge](#Suggestions)

    GET /indexes/[indexname]/docs/suggest?[abfrageparameter]

________________________________________
<a name="IndexOps"></a>
# Indexvorgänge #

Sie können Indizes im Azure-Suchdienst für eine bestehende Indexressource mithilfe einfacher HTTP-Anforderungen (POST, GET, PUT, DELETE) erstellen und verwalten. Zum Erstellen eines Index veröffentlichen Sie mit POST ein JSON-Dokument, in dem das Indexschema beschrieben ist. Das Schema definiert die Felder des Index, ihre Datentypen und ihre Verwendung (z. B. für Volltext-Suchvorgänge, Filterung, Sortierung, Facettensuche oder Vorschläge). Ebenso definiert es Bewertungsprofile und weitere Attribute, mit denen das Verhalten des Index konfiguriert wird. 

Das folgende Beispiel veranschaulicht ein Schema, das zum Suchen nach Hotelinformationen verwendet wird, und dessen Feld "Beschreibung" in zwei Sprachen definiert ist. Beachten Sie, wie die Verwendung des Felds mithilfe von Attributen gesteuert wird. Beispielsweise wird die  `hotelId` als Dokumentschlüssel verwendet (`"key": true`) und aus Volltext-Suchvorgängen ausgeschlossen (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true},
	  {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true, analyzer="fr.lucene"},
      {"name": "hotelName", "type": "Edm.String", "suggestions": true},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ] 
    }
 
Nach der Erstellung des Index laden Sie Dokumente hoch, mit denen der Index aufgefüllt wird. Unter [Dokumente hinzufügen oder aktualisieren](#AddOrUpdateDocuments) finden Sie weitere Informationen zu diesem anschließenden Schritt.

Eine Videoeinführung zur Indizierung in Azure Search finden Sie in der [Channel 9 Cloud Cover-Folge zu Azure Search](http://go.microsoft.com/fwlink/p/?LinkId=511509).


<a name="CreateIndex"></a>
## Index erstellen ##

Sie können einen neuen Index innerhalb eines Azure-Suchdiensts mithilfe einer HTTP-POST-Anforderung erstellen. Der Textteil der Anforderung ist ein JSON-Dokument, in dem der Indexname, die Felder, die Attribute zur Steuerung des Abfrageverhaltens, die Bewertung der Ergebnisse und CORS-Optionen angegeben sind.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Alternativ können Sie PUT verwenden und den Indexnamen im URI angeben. Wenn der Index noch nicht vorhanden ist, wird er erstellt.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

**Hinweis**: Die maximale Anzahl der zulässigen Indizes unterscheidet sich je nach Preisstufe. Der kostenlose Dienst erlaubt bis zu 3 Indizes. Der Standarddienst erlaubt 50 Indizes pro Suchdienst. Details finden Sie unter [Limits und Einschränkungen](http://msdn.microsoft.com/library/azure/dn798934.aspx). 

**Anforderung**

HTTPS ist für alle Dienstanforderungen erforderlich. Die Anforderung **Create Index** kann entweder mithilfe einer POST- oder einer PUT-Methode dargestellt werden. Bei der Verwendung von POST muss der Indexname im Text der Anforderung zusammen mit der Indexschemadefinition angegeben werden. Bei PUT bildet der Indexname einen Teil der URL. Wenn der Index nicht vorhanden ist, wird er erstellt. Wenn er bereits vorhanden ist, wird er auf die neue Definition aktualisiert. 

Der Indexname muss aus Kleinbuchstaben bestehen, mit einem Buchstaben oder einer Ziffer beginnen, darf keine Schrägstriche oder Punkte aufweisen und muss kürzer als 128 Zeichen sein. Nach dem Anfangsbuchstaben bzw. der Anfangsziffer kann der Rest des Indexnamens beliebige Buchstaben, Ziffern und Bindestriche enthalten, vorausgesetzt, es folgen keine zwei Bindestriche aufeinander.

Die  `api-version` ist erforderlich. Zu den gültigen Werten gehören `2014-07-31-Preview` und `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen, im allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu beurteilen, wie etwa die Unterstützung für Sprachanalyse, die sich im Analyse-Indexattribut ausdrückt. Details zu den API-Versionen finden Sie unter [Search Service-Versionsverwaltung](http://msdn.microsoft.com/library/azure/dn864560.aspx). Unter [Sprachunterstützung](#LanguageSupport) finden Sie Details zu den Sprachanalysemodulen.

**Anforderungsheader**

In der folgenden Liste sind die erforderlichen und optionalen Anforderungsheader beschrieben. 

- `Content-Type`: Erforderlich. Legen Sie diesen auf  `application/json` fest.
- `api-key`: Erforderlich. Der  `api-key` wird verwendet, um  
- die Anforderung bei Ihrem Suchdienst zu authentifizieren. Es handelt sich um einen Zeichenfolgenwert, der für Ihren Dienst eindeutig ist. Die **Create Index**-Anforderung muss einen  `api-key`-Header enthalten, der auf Ihren Administratorschlüssel (im Gegensatz zu einem Anforderungsschlüssel) festgelegt ist. 
 
Ferner benötigen Sie den Dienstnamen, um die Anforderungs-URL zu erstellen. Sie können den Dienstnamen und den  `api-key` bei Ihrem Dienstdashboard im Azure-Vorschauportal abrufen. Hilfe zur Seitennavigation finden Sie unter [Konfigurieren von Azure Search im Vorschauportal](../search-configure.md).

<a name="RequestData"></a>
**Syntax des Anforderungstextkörpers**

Der Textkörper der Anforderung enthält eine Schemadefinition, die die Liste von Datenfeldern in Dokumenten beinhaltet, mit denen dieser Index gespeist wird, ferner Datentypen, Attribute sowie eine optionale Liste mit Bewertungsprofilen, die zum Bewerten passender Dokumente zur Abfragezeit verwendet werden. 

Beachten Sie, dass Sie bei einer POST-Anforderung den Indexnamen im Anforderungstextkörper angeben müssen.

Es kann nur ein Schlüsselfeld im Index vorhanden sein. Dabei muss es sich um ein Zeichenfolgenfeld handeln. Dieses Feld stellt die eindeutige ID für jedes im Index gespeicherte Dokument dar. 

Die Syntax zur Strukturierung des Nutztexts der Anforderung folgt hier. Eine Beispielanforderung steht weiter unten in diesem Thema zur Verfügung.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "suggestions": true | false (default, only Edm.String and Collection(Edm.String) fields can be used for suggestions),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,
		  "analyzer": "name of text analyzer"
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive #'s),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified) 
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }    

Hinweis: Der Datentyp `Edm.Int64` wird von API-Version 2014-10-20-Preview an unterstützt.
    
**Indexattribute**

Beim Erstellen eines Index können die folgenden Attribute festgelegt werden. Details zu Bewertung und Bewertungsprofilen finden Sie unter [Hinzufügen von Bewertungsprofilen zu einem Suchindex](http://msdn.microsoft.com/library/azure/dn798928.aspx).

`name` - Legt den Namen des Felds fest.

`type` - Legt den Datentyp des Felds fest. Unter [Unterstützte Datentypen](#DataTypes) finden Sie eine Liste der unterstützten Typen.

`searchable` - Kennzeichnet das Feld als bei der Volltextsuche durchsuchbar. Das bedeutet, es wird bei der Indizierung Analyseverfahren wie der Wörtertrennung unterzogen. Wenn Sie ein Feld  `searchable` auf einen Wert wie "schöner Tag" festlegen, wird es intern in die Einzeltoken "schöner" und "Tag" aufgeteilt. Dies ermöglicht die Volltextsuche nach diesen Begriffen. Felder vom Typ  `Edm.String` oder  `Collection(Edm.String)` sind standardmäßig  `searchable`. Felder anderer Typen können nicht  `searchable` sein.

  - **Hinweis**:  `searchable`-Felder belegen zusätzlichen Platz in Ihrem Index, da Azure Search eine zusätzliche Version des Feldwerts mit Token für die Volltextsuche speichert. Wenn Sie Platz im Index sparen möchten, und die Aufnahme eines Felds in Suchvorgänge nicht benötigen, legen Sie  `searchable` auf  `false`fest.

`filterable` - Ermöglicht den Verweis auf das Feld in `$filter`-Abfragen.  `filterable` unterscheidet sich von  `searchable` in der Art, wie Zeichenfolgen verarbeitet werden. Felder vom Typ  `Edm.String` oder  `Collection(Edm.String)`, die  `filterable` sind, werden keiner Wörtertrennung unterzogen, daher sind Vergleiche auf genaue Übereinstimmungen beschränkt. Wenn Sie ein solches Feld auf  `f` to "sunny day", `$filter=f  festlegen, eq 'sunny'` will find no matches, but `$filter=f  standardmäßig eq 'sunny day'` will. All fields are `filterable`. 

`sortable` - Standardmäßig sortiert das System Ergebnisse nach der Punktzahl, in vielen Bereichen möchten die Benutzer jedoch nach Feldern im Dokument sortieren. Felder vom Typ  `Collection(Edm.String)` können nicht  `sortable` sein. Alle anderen Felder sind standardmäßig  `sortable`.

`facetable`- Normalerweise in einer Darstellung von Suchergebnissen verwendet, die die Trefferanzahl nach Kategorie beinhaltet (z. B. bei einer der Suche nach Digitalkameras, die Treffer nach Marke, Megapixeln, Preis usw. ausweist). Diese Option kann nicht für Felder vom Typ  `Edm.GeographyPoint` verwendet werden. Alle anderen Felder sind standardmäßig  `facetable`.

  - **Hinweis**: Felder vom Typ  `Edm.String`, die  `filterable`  `sortable` oder  `facetable` sind, dürfen höchstens 32 KB lang sein. Dies hat den Grund, dass solche Felder als einzelner Suchausdruck behandelt werden und die maximale Länge eines Ausdrucks in Azure Search 32 KB beträgt. Wenn Sie mehr Text in einem einzelnen Zeichenfolgenfeld speichern müssen, müssen Sie  `filterable`, `sortable` und  `facetable` in Ihrer Indexdefinition explizit auf  `false` festlegen.

`suggestions` - Legt fest, ob das Feld für das AutoAusfüllen bei der Eingabe verwendet werden kann. Dies kann nur für Felder vom Typ  `Edm.String` oder  `Collection(Edm.String)` festgelegt werden.  `suggestions` ist standardmäßig  `false`, da dafür zusätzlicher Platz im Index erforderlich ist. 

  - **Hinweis**: Wenn für ein Feld keins der oben angegebenen Attribute (`searchable`, `filterable`, `sortable`, `facetable`, oder  `suggestions`) auf  `true` festgelegt ist, ist das Feld effektiv vom invertierten Index ausgeschlossen. Diese Option ist für Felder nützlich, die nicht in Abfragen verwendet werden, aber in Suchergebnissen erforderlich sind. Das Ausschließen solcher Felder aus dem Index verbessert die Leistung.

`key` - Markiert das Feld als eindeutige IDs für im Index enthaltene Dokumente. Es muss genau ein Feld als  `key`-Feld ausgewählt werden, und es muss den Typ  `Edm.String` aufweisen. Schlüsselfelder können für das direkte Nachschlagen von Dokumenten mithilfe der [Nachschlage-API] verwendet werden(#LookupAPI).

`retrievable` - Legt fest, ob das Feld in einem Suchergebnis zurückgegeben werden kann.  Dies ist nützlich, wenn Sie ein Feld (z. B. "Rand") als Mechanismus zum Filtern, Sortieren oder Bewerten verwenden möchten, das Feld für den Endbenutzer aber nicht sichtbar sein soll. Dieses Attribut muss für  `key`-Felder  `true` sein.

`scoringProfiles` - Definiert benutzerdefinierte Bewertungsverhalten, mit deren Hilfe Sie Einfluss darauf nehmen können, welche Elemente in Suchergebnissen weiter oben angezeigt werden. Bewertungsprofile bestehen aus gewichteten Feldern und Funktionen. Weitere Informationen über die in Bewertungsprofilen verwendeten Attribute finden Sie unter [Hinzufügen von Bewertungsprofilen zu einem Suchindex](http://msdn.microsoft.com/library/azure/dn798928.aspx).

`analyzer` - Legt den Namen des Textanalysemoduls fest, das für das Feld verwendet werden soll. Informationen zur zulässigen Wertemenge finden Sie unter [Sprachunterstützung](#LanguageSupport). Diese Optionen können nur für  `searchable`-Felder verwendet werden. Sobald das Analysemodul ausgewählt wurde, kann es für das Feld nicht mehr geändert werden. 


<a name="LanguageSupport"></a>
**Sprachunterstützung**

Durchsuchbare Felder werden Analyseverfahren unterzogen, die meistens Wörtertrennung, Textnormalisierung und Ausfilterung von Ausdrücken beinhalten. Standardmäßig werden durchsuchbare Felder in Azure Search mit dem [Apache Lucene-Standardanalysemodul](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html) analysiert, das Text nach den Regeln der ["Unicode-Textsegmentierung"](http://unicode.org/reports/tr29/) in Elemente aufteilt. Darüber hinaus konvertiert das Standardanalysemodul alle Zeichen in Kleinbuchstaben. Sowohl indizierte Dokumente als auch Suchausdrücke werden während der Indizierung und der Abfrageverarbeitung der Analyse unterzogen.

Azure Search ermöglicht die Indizierung von Feldern in einer Vielzahl von Sprachen. Für jede dieser Sprachen ist ein nicht standardmäßiges Textanalysemodul erforderlich, das den Charakteristika einer bestimmten Sprache Rechnung trägt. Beispielsweise verwendet das Analysemodul für Französisch einen [Light French Stemmer](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/fr/FrenchLightStemmer.html), um Wörter auf ihre [Wortstämme](http://en.wikipedia.org/wiki/Stemming) zurückführen zu können. Außerdem werden [Auslassungen](http://en.wikipedia.org/wiki/Elision) und französische Stoppwörter aus dem analysierten Text entfernt.
Das Analysemodul für Englisch erweitert das Standardanalysemodul. Es entfernt Possessivformen (schließendes "'s") von Wörtern, wendet die Rückführung auf den Stamm nach dem [Porter Stemming-Algorithmus](http://tartarus.org/~martin/PorterStemmer/) an und entfernt englische [Stoppwörter](http://en.wikipedia.org/wiki/Stop_words).
 
Das Analysemodul kann für jedes Feld in der Indexdefinition durch Festlegen der  `analyzer`-Eigenschaft unabhängig konfiguriert werden. Beispielsweise können Sie separate Felder für englische, französische und spanische Hotelbeschreibungen verwenden, die nebeneinander im gleichen Index vorhanden sind. Die Abfrage gibt an, welche sprachspezifischen Felder in den Suchabfragen zurückgegeben werden sollen.

Unten finden Sie die Liste der unterstützten Analysemodule zusammen mit einer kurzen Beschreibung ihrer Funktionen:

<table style="font-size:12">
    <tr>
		<th>Sprache</th>
		<th>Name des Analysemoduls</th>
		<th>Beschreibung</th>
	</tr>
    <tr>
		<td>Arabisch</td>
		<td>ar.lucene</td>
		<td>
		<ul>
			<li>Implementiert arabische Rechtschreibnormalisierung</li>
			<li>Wendet einfache algorithmische Stammreduzierung an</li>
			<li>Filtert arabische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Brasilianisch</td>
		<td>pt-Br.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert brasilianische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Chinesisch (vereinfacht)</td>
		<td>zh-Hans.lucene</td>
		<td>
		<ul>
			<li>Verwendet probabilistische Wissensmodelle, um die optimale Wortsegmentierung zu ermitteln</li>
			<li>Filtert chinesische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Chinesisch (traditionell)</td>
		<td>zh-Hant.lucene</td>
		<td>
		<ul>
			<li>Indiziert Bigramme (überlappende Gruppen zweier benachbarter chinesischer Zeichen)</li>
			<li>Normalisiert Unterschiede in der Zeichenbreite</li>
		</ul>
		</td>
	<tr>
    <tr>
		<td>Tschechisch</td>
		<td>cs.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert tschechische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Dänisch</td>
		<td>da.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert dänische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Niederländisch</td>
		<td>nl.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert niederländische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Deutsch</td>
		<td>de.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert deutsche Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Griechisch</td>
		<td>el.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert griechische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Englisch</td>
		<td>en.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert englische Stoppwörter aus</li>
			<li>Entfernt Possessivformen</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Finnisch</td>
		<td>fi.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert finnische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Französisch</td>
		<td>fr.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert französische Stoppwörter aus</li>
			<li>Entfernt Auslassungen</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Hindi</td>
		<td>hi.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert Hindi-Stoppwörter aus</li>
			<li>Entfernt einige Abweichungen durch Rechtschreibvarianten</li>
			<li>Normalisiert die Unicode-Darstellung von Text in indischen Sprachen.</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Ungarisch</td>
		<td>hu.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert ungarische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Indonesisch (Bahasa)</td>
		<td>id.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert indonesische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Italienisch</td>
		<td>it.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert italienische Stoppwörter aus</li>
			<li>Entfernt Auslassungen</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Japanisch</td>
		<td>ja.lucene</td>
		<td>
		<ul>
			<li>Verwendet morphologische Analyse</li>
			<li>Normalisiert häufige Katakana-Rechtschreibvarianten</li>
			<li>Einfache Entfernung von Stoppwörtern/Stoppzeichen</li>
			<li>Normalisierung der Zeichenbreite</li>
			<li>Lemmatisierung - führt gebeugte Adjektive und Verben auf ihre Stammform zurück</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Koreanisch</td>
		<td>ko.lucene</td>
		<td>
		<ul>
			<li>Indiziert Bigramme (überlappende Gruppen zweier benachbarter chinesischer Zeichen)</li>
			<li>Normalisiert Unterschiede in der Zeichenbreite</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Lettisch</td>
		<td>lv.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert lettische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>

    <tr>
		<td>Norwegisch</td>
		<td>no.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert norwegische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Polnisch</td>
		<td>pl.lucene</td>
		<td>
		<ul>
			<li>Wendet algorithmische Wortstammerkennung (Stempel) an</li>
			<li>Filtert polnische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Portugiesisch</td>
		<td>pt-Pt.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert portugiesische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>

    <tr>
		<td>Rumänisch</td>
		<td>ro.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert rumänische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Russisch</td>
		<td>ru.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert russische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Spanisch</td>
		<td>es.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert spanische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Schwedisch</td>
		<td>sv.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert schwedische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Türkisch</td>
		<td>tr.lucene</td>
		<td>
		<ul>
			<li>Entfernt alle Zeichen nach einem Apostroph (einschließlich des Apostrophs selbst)</li>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert türkische Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Thai</td>
		<td>th.lucene</td>
		<td>
		<ul>
			<li>Wendet einfache Stammreduzierung an</li>
			<li>Filtert Thai-Stoppwörter aus</li>
		</ul>
		</td>
	</tr>
</table>

Alle Analysemodule, deren Namen <i>lucene</i> enthalten, basieren auf den [Sprachanalysemodulen von Apache Lucene](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html).

**CORS-Optionen**

Clientseitig können mit Javascript standardmäßig keine APIs aufgerufen werden, da der Browser alle ursprungsübergreifenden Anforderungen verhindert. Aktivieren Sie CORS (Cross-Origin Resource Sharing), indem Sie das  `corsOptions`-Attribut so festlegen, dass ursprungsübergreifende Abfragen für Ihren Index zugelassen werden. Beachten Sie, dass aus Sicherheitsgründen CORS nur von Abfrage-APIs unterstützt wird. Die folgenden Optionen können für CORS festgelegt werden:

- `allowedOrigins` (erforderlich): Dies ist eine Liste der Ursprünge, denen Zugriff auf Ihren Index erteilt werden soll. Dies bedeutet, dass von solchen Ursprüngen aus bedientem Javascript-Code das Abfragen Ihres Index gestattet wird (vorausgesetzt, er übergibt den korrekten API-Schlüssel). Jeder Ursprung weist typischerweise die Form  `protocol://fully-qualified-domain-name:port` auf, obwohl der Port oft ausgelassen wird. Weitere Details finden Sie in [diesem Artikel](http://go.microsoft.com/fwlink/?LinkId=330822).
 - Wenn Sie den Zugriff für alle Ursprünge zulassen möchten, schließen Sie `*` als einziges Element in das  `allowedOrigins`-Array ein. Beachten Sie, dass dies ** kein empfohlenes Vorgehen für Suchdienste im produktiven Einsatz ist.** Es kann jedoch in der Entwicklung oder für Debugzwecke nützlich sein.
- `maxAgeInSeconds` (optional): Browser verwenden diesen Wert, um die Dauer (in Sekunden) zum Zwischenspeichern von Preflight-Reaktionen von CORS zu bestimmen. Es muss sich um eine nicht negative ganze Zahl handeln. Je größer dieser Wert ist, desto besser ist die Leistung, es dauert jedoch länger, bis Änderungen der CORS-Richtlinie wirksam werden. Wenn kein Wert festgelegt ist , wird ein Standardwert von 5 Minuten verwendet.

<a name="CreateUpdateIndexExample"></a>
**Beispiel für einen Anforderungstextkörper**
 
    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true},
	    {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true, analyzer="fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "suggestions": true},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ] 
    }

**Antwort**

Für eine erfolgreiche Anforderung: "201 Created". 

Standardmäßig enthält der Textkörper der Antwort den JSON-Code für die erstellte Indexdefinition. Wenn der  `Prefer`-Anforderungsheader auf  `return=minimal` festgelegt ist, ist der Textkörper der Anforderung leer, und der Erfolgsstatuscode "204 No Content" wird anstelle von "201 Created" zurückgegeben. Dies trifft unabhängig davon zu, ob PUT oder POST zum Erstellen des Index verwendet worden war.

<a name="UpdateIndex"></a>
## Index aktualisieren ##

Sie können einen vorhandenen Index in Azure Search mithilfe einer HTTP-PUT-Anforderung aktualisieren. In der öffentlichen Vorschauversion können Aktualisierungen im Hinzufügen neuer Felder zum vorhandenen Schema, im Ändern der CORS-Optionen und im Verändern von Bewertungsprofilen bestehen (siehe dazu [Hinzufügen von Bewertungsprofilen zu einem Suchindex](http://msdn.microsoft.com/library/azure/dn798928.aspx)). Sie geben den Namen des zu aktualisierenden Index im Anforderungs-URI an:

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Wichtig:** In der öffentlichen Vorschauversion von Azure Search werden Aktualisierungen des Indexschemas in eingeschränktem Umfang unterstützt. Alle Schemaaktualisierungen, für die eine Neuindizierung erforderlich ist, wie etwa das Ändern von Feldtypen, werden aktuell nicht unterstützt. Neue Felder können jederzeit hinzugefügt werden, vorhandene Felder können jedoch nicht geändert oder gelöscht werden.

Beim Hinzufügen eines neuen Felds zu einem Index weisen alle vorhandenen Dokumente im Index für das betreffende Feld automatisch den Wert null auf. Es wird kein zusätzlicher Speicherplatz beansprucht, sofern dem Index keine neuen Dokumente hinzugefügt werden.

**Anforderung**

HTTPS ist für alle Dienstanforderungen erforderlich. Die **Update Index**-Anforderung wird mithilfe von HTTP PUT erstellt. Bei PUT bildet der Indexnamen einen Teil der URL. Wenn der Index nicht vorhanden ist, wird er erstellt. Wenn der Index bereits vorhanden ist, wird er auf die neue Definition aktualisiert. 

Der Indexname muss aus Kleinbuchstaben bestehen, mit einem Buchstaben oder einer Ziffer beginnen, darf keine Schrägstriche oder Punkte aufweisen und muss kürzer als 128 Zeichen sein. Nach dem Anfangsbuchstaben bzw. der Anfangsziffer kann der Rest des Indexnamens beliebige Buchstaben, Ziffern und Bindestriche enthalten, vorausgesetzt, es folgen keine zwei Bindestriche aufeinander.

Der Parameter  `api-version` ist erforderlich. Zu den gültigen Werten gehören `2014-07-31-Preview` und `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen, im allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Details finden Sie unter [Search Service-Versionsverwaltung](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Anforderungsheader**

In der folgenden Liste sind die erforderlichen und optionalen Anforderungsheader beschrieben.

- `Content-Type`: Erforderlich. Legen Sie diesen auf  `application/json` fest.
- `api-key`: Erforderlich. Der  `api-key` wird verwendet, um die Anforderung bei Ihrem Suchdienst zu authentifizieren. Es handelt sich um einen Zeichenfolgenwert, der für Ihren Dienst eindeutig ist. Die Anforderung **Update Index** muss einen  `api-key`-Header beinhalten, der auf Ihren Administratorschlüssel festgelegt ist (statt auf einen Abfrageschlüssel).
 
Ferner benötigen Sie den Dienstnamen, um die Anforderungs-URL zu erstellen. Sie können den Dienstnamen und den  `api-key` bei Ihrem Dienstdashboard im Azure-Vorschauportal abrufen. Hilfe zur Seitennavigation finden Sie unter [Erste Schritte mit Azure Search](search-get-started.md).

**Syntax des Anforderungstextkörpers**

Beim Aktualisieren eines vorhandenen Index muss der Textkörper die ursprüngliche Schemadefinition und die neu hinzuzufügenden Felder sowie ggf. die geänderten Bewertungsprofile und CORS-Optionen enthalten. Wenn Sie die Bewertungsprofile und CORS-Optionen nicht ändern, müssen Sie die ursprünglich bei der Erstellung des Index verwendeten Werte einschließen. Im allgemeinen besteht das beste Muster für Aktualisierungen im Abrufen der Indexdefinition mit einem GET, die dann bearbeitet und mit PUT aktualisiert wird.

Die zum Erstellen eines Index verwendete Schemasyntax wird hier zur besseren Übersicht noch einmal angegeben. Unter [Index erstellen](#CreateIndex) finden Sie weitere Details.

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "suggestions": true | false (default, only Edm.String and Collection(Edm.String) fields can be used for suggestions),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,
		  "analyzer": "name of text analyzer"
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive #'s),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified) 
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }    

Hinweis: Der Datentyp `Edm.Int64` wird von API-Version 2014-10-20-Preview an unterstützt.

**Antwort**

Für eine erfolgreiche Anforderung: "204 No Content".

Standardmäßig ist der Textkörper der Antwort leer. Wenn der  `Prefer`-Anforderungsheader jedoch auf  `return=representation` festgelegt ist, enthält der Textkörper der Antwort den JSON-Code für die aktualisierte Indexdefinition. In diesem Fall ist der Erfolgsstatuscode "200 OK".

<a name="ListIndexes"></a>
## Indizes auflisten ##

Der Vorgang **List Indexes** gibt eine Liste der Indizes zurück, die aktuell in Ihrem Azure-Suchdienst vorhanden sind. 

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Anforderung**

HTTPS ist für alle Dienstanforderungen erforderlich. Die **List Indexes**-Anforderung kann mithilfe der GET-Methode erstellt werden.

Der Parameter  `api-version` ist erforderlich. Zu den gültigen Werten gehören `2014-07-31-Preview` und `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen, im allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Details finden Sie unter [Search Service-Versionsverwaltung](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Anforderungsheader**

In der folgenden Liste sind die erforderlichen und optionalen Anforderungsheader beschrieben.
 
- `api-key`: Erforderlich. Der  `api-key` wird verwendet, um die Anforderung bei Ihrem Suchdienst zu authentifizieren. Es handelt sich um einen Zeichenfolgenwert, der für Ihren Dienst eindeutig ist. Die **List Indexes**-Anforderung muss einen  `api-key` enthalten, der auf einen Administratorschlüssel (statt auf einen Abfrageschlüssel) festgelegt ist.
 
Ferner benötigen Sie den Dienstnamen, um die Anforderungs-URL zu erstellen. Sie können den Dienstnamen und den  `api-key` bei Ihrem Dienstdashboard im Azure-Vorschauportal abrufen. Hilfe zur Seitennavigation finden Sie unter [Erste Schritte mit Azure Search](search-get-started.md).

**Anforderungstextkörper**

Keiner.

**Antwort**

Status Code: "200 OK" wird als Antwort bei Erfolg zurückgegeben.

Hier folgt ein Beispiel für einen Antworttextkörper:

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Beachten Sie, dass Sie die Antwort durch Filtern bis auf die interessierenden Eigenschaften eingrenzen können. Wenn Sie beispielsweise nur eine Liste der Indexnamen wünschen, verwenden Sie die OData-Abfrageoption `$select`:

    GET /indexes?api-version=2014-07-31-Preview&$select=name

In diesem Fall würde die Antwort aus dem Beispiel oben in folgender Weise angezeigt: 

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Dies ist eine nützliche Technik, die viel Bandbreite einspart, wenn Ihr Suchdienst über viele Indizes verfügt.

<a name="GetIndex"></a>
## Index abrufen##

Der Vorgang **Get Index** ruft die Indexdefinition aus Azure Search ab.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Anforderung**

HTTPS ist für Dienstanforderungen erforderlich. Die **Get Index**-Anforderung kann mithilfe der GET-Methode erstellt werden. 
 
Der [indexname] im Anforderungs-URI gibt an, welcher Index aus der Auflistung der Indizes zurückgegeben werden soll.

Der Parameter  `api-version` ist erforderlich. Zu den gültigen Werten gehören `2014-07-31-Preview` und `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen, im allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Details finden Sie unter [Search Service-Versionsverwaltung](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Anforderungsheader**

In der folgenden Liste sind die erforderlichen und optionalen Anforderungsheader beschrieben.
 
- `api-key`: Der  `api-key` wird verwendet, um die Anforderung bei Ihrem Suchdienst zu authentifizieren. Es handelt sich um einen Zeichenfolgenwert, der für Ihren Dienst eindeutig ist. Die **Get Index**-Anforderung muss einen  `api-key` enthalten, der auf einen Administratorschlüssel (statt auf einen Abfrageschlüssel) festgelegt ist.

Ferner benötigen Sie den Dienstnamen, um die Anforderungs-URL zu erstellen. Sie können den Dienstnamen und den  `api-key` bei Ihrem Dienstdashboard im Azure-Vorschauportal abrufen. Hilfe zur Seitennavigation finden Sie unter [Erste Schritte mit Azure Search](search-get-started.md).

**Anforderungstextkörper**

Keiner.

**Antwort**

Status Code: "200 OK" wird als Antwort bei Erfolg zurückgegeben.

Unter [Erstellen und Aktualisieren eines Index](#CreateUpdateIndexExample) finden Sie JSON-Code als Beispiel für die Nutzdaten einer Antwort.

<a name="DeleteIndex"></a>
## Index löschen ##

Der Vorgang **Delete Index** entfernt einen Index und zugeordnete Dokumente aus Ihrem Azure-Suchdienst. Sie können den Indexnamen im Dienstdashboard im Azure-Vorschauportal oder über die API abrufen. Unter [Indizes auflisten](#ListIndexes) finden Sie Details.

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]
    
**Anforderung**

HTTPS ist für Dienstanforderungen erforderlich. Die **Delete Index**-Anforderung  kann mithilfe der DELETE-Methode erstellt werden.
 
Der [indexname] im Anforderungs-URI gibt an, welcher Index aus der Auflistung der Indizes gelöscht werden soll. 

Der Parameter  `api-version` ist erforderlich. Zu den gültigen Werten gehören `2014-07-31-Preview` und `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen, im allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Details finden Sie unter [Search Service-Versionsverwaltung](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Anforderungsheader**

In der folgenden Liste sind die erforderlichen und optionalen Anforderungsheader beschrieben. 
 
- `api-key`: Erforderlich. Der  `api-key` wird verwendet, um die Anforderung bei Ihrem Suchdienst zu authentifizieren. Es handelt sich um einen Zeichenfolgenwert, der für Ihre Dienst-URL eindeutig ist. Die Anforderung **Delete Index** muss einen  `api-key`-Header beinhalten, der auf Ihren Administratorschlüssel (statt auf einen Abfrageschlüssel) festgelegt ist.
 
Ferner benötigen Sie den Dienstnamen, um die Anforderungs-URL zu erstellen. Sie können den Dienstnamen und den  `api-key` bei Ihrem Dienstdashboard im Azure-Vorschauportal abrufen. Hilfe zur Seitennavigation finden Sie unter [Konfigurieren von Azure Search im Vorschauportal](../search-configure.md).

**Anforderungstextkörper**

Keiner.

**Antwort**

Status Code: "204 No Content" wird als Antwort bei Erfolg zurückgegeben.

<a name="GetIndexStats"></a>
## Indexstatistik abrufen ##

Der Vorgang **Get Index Statistics** gibt eine Dokumentanzahl für den aktuellen Index aus Azure Search sowie die Speichernutzung zurück. 

	GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

**Anforderung**

HTTPS ist für alle Dienstanforderungen erforderlich. Die Anforderung **Get Index Statistics** kann mithilfe der GET-Methode erstellt werden.

Der [indexname] im Anforderungs-URI weist den Dienst an, Indexstatistiken für den angegebenen Index zurückzuliefern.

Der Parameter  `api-version` ist erforderlich. Zu den gültigen Werten gehören `2014-07-31-Preview` und `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen, im allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Details finden Sie unter [Search Service-Versionsverwaltung](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Anforderungsheader**

In der folgenden Liste sind die erforderlichen und optionalen Anforderungsheader beschrieben.
 
- `api-key`: Der  `api-key` wird verwendet, um die Anforderung bei Ihrem Suchdienst zu authentifizieren. Es handelt sich um einen Zeichenfolgenwert, der für Ihren Dienst eindeutig ist. Die **Get Index Statistics**-Anforderung muss einen  `api-key` enthalten, der auf einen Administratorschlüssel (statt auf einen Anforderungsschlüssel) festgelegt ist.
 
Ferner benötigen Sie den Dienstnamen, um die Anforderungs-URL zu erstellen. Sie können den Dienstnamen und den  `api-key` bei Ihrem Dienstdashboard im Azure-Vorschauportal abrufen. Hilfe zur Seitennavigation finden Sie unter [Erste Schritte mit Azure Search](search-get-started.md).

**Anforderungstextkörper**

Keiner.

**Antwort**

Status Code: "200 OK" wird als Antwort bei Erfolg zurückgegeben.

Der Textkörper der Antwort liegt im folgenden Format vor:

    {
      "documentCount": number,
	  "storageSize": number (size of the index in bytes)
    }

________________________________________
<a name="DocOps"></a>
# Dokumentvorgänge #

In Azure Search wird ein Index mithilfe von JSON-Dokumenten aufgefüllt, die Sie auf den Dienst hochladen. Alle Dokumente, die Sie hochladen, bilden den Korpus der Suchdaten. Dokumente enthalten Felder, von denen einige mithilfe von Token beim Hochladen als Suchbegriffe gekennzeichnet werden. Das  `/docs`-URL-Segment in der Azure Search-API stellt die Auflistung der Dokumente in einem Index dar. Alle mit der Auflistung durchgeführten Vorgänge, wie Hochladen, Zusammenführen, Löschen oder Abfragen von Dokumenten, erfolgt im Kontext eines einzelnen Index, sodass die URLs für diese Vorgänge immer mit  `/indexes/[index name]/docs` für einen bestimmten Indexnamen beginnen.

Der Code Ihrer Anwendung kann JSON-Dokumente für das Hochladen auf Azure generieren und dazu die Ergebnismenge aus einer relationalen Datenbank oder aus einer beliebigen anderen strukturierten Datenquelle verwenden. Die Adventure Works Demo-Beispielanwendung von Azure Search auf Codeplex enthält Code zur Erstellung von JSON-Dokumenten unter Verwendung einer Ergebnismenge aus der Adventure Works-Beispieldatenbank. Weitere Informationen zur Beispielanwendung finden Sie [hier](search-create-first-solution.md).

In der Mehrzahl der Szenarien bei der Anwendungsentwicklung sind Suchdaten getrennt von der Anwendungsdatenschicht und zu dieser extern. Wenn Ihre Anwendung eine lokale Datenbank für das Nachverfolgen des Inventarstatus verwendet, enthalten die dauerhaft in Azure Search vorhandenen Dokumente ähnliche oder sogar identische Datenwerte im Hinblick auf Produktname, Preis und Verfügbarkeit, diese sind jedoch in einem invertierten Index gespeichert, um die Suchvorgänge zu optimieren. 

Sie sollten darauf hinzielen, ein Dokument für jedes Element zu unterhalten, nach dem Sie suchen möchten. Beispielsweise kann eine Anwendung für den Filmverleih ein Dokument pro Film aufweisen, eine Storefrontanwendung kann ein Dokument pro SKU verwenden, eine Anwendung für Onlinecourseware kann ein Dokument pro Kurs verwenden, ein Forschungsunternehmen verwendet vielleicht ein Dokument für jede wissenschaftliche Veröffentlichung in ihrem Repertoire usw.

Dokumente bestehen aus einem oder mehreren Feldern. Felder können Text enthalten, der mithilfe von Token als Suchbegriffe definiert wurde, sowie nicht mit Token versehene oder nicht aus Text bestehende Werte, die in Filtern oder Bewertungsprofilen verwendet werden können. Die Namen, Datentypen und Suchfunktionen, die für die einzelnen Felder unterstützt werden, werden durch das Indexschema bestimmt. Eins der Felder in jedem Indexschema muss als ID ausgewiesen sein, und jedes Dokument muss einen Wert für das ID-Feld aufweisen, die das betreffende Dokument eindeutig im Index identifiziert. Alle anderen Dokumentfelder sind optional und haben null als Standardwert, wenn für sie keine Angabe gemacht wird. Beachten Sie, dass Nullwerte im invertierten Index keinen Platz in Anspruch nehmen. 

Vor dem Hochladen von Dokumenten müssen Sie den Index bereits im Dienst erstellt haben. Unter [Index erstellen](#CreateIndex) finden Sie Details zu diesem ersten Schritt.

**Hinweis**: Die öffentliche Vorschau von Azure Search unterstützt bei der Volltextsuche nur Englisch.

<a name="AddOrUpdateDocuments"></a>
## Hinzufügen, Aktualisieren oder Löschen von Dokumenten ##

Sie können Dokumente aus einem angegebenen Index mithilfe von HTTP POST hochladen, zusammenführen, zusammenführen-oder-hochladen oder löschen. Bei einer großen Zahl von Aktualisierungsvorgängen wird das Zusammenfassen von Dokumenten in Batches (bis zu 1000 Dokumente oder ca. 16 MB pro Batch) empfohlen.

    POST https://[dienstname].search.windows.net/indexes/[indexname]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [Administratorschlüssel]
    
**Anforderung**

HTTPS ist für alle Dienstanforderungen erforderlich. Sie können Dokumente aus einem angegebenen Index mithilfe von HTTP POST hochladen, zusammenführen, zusammenführen-oder-hochladen oder löschen. 

Der Anforderungs-URI beinhaltet [indexname], der angibt, für welchen Index die Dokumente veröffentlicht werden sollen. Dokumente können jeweils nur in einem Index veröffentlicht werden.

Der Parameter  `api-version` ist erforderlich. Zu den gültigen Werten gehören `2014-07-31-Preview` und `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen, im allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Details finden Sie unter [Search Service-Versionsverwaltung](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Anforderungsheader**

In der folgenden Liste sind die erforderlichen und optionalen Anforderungsheader beschrieben.

- `Content-Type`: Erforderlich. Legen Sie diesen auf  `application/json` fest.
- `api-key`: Erforderlich. Der  `api-key` wird verwendet, um die Anforderung bei Ihrem Suchdienst zu authentifizieren. Es handelt sich um einen Zeichenfolgenwert, der für Ihren Dienst eindeutig ist. Die Anforderung **Add Documents** muss einen  `api-key`-Header enthalten, der auf Ihren Administratorschlüssel (statt auf einen Abfrageschlüssel) festgelegt ist.
 
Ferner benötigen Sie den Dienstnamen, um die Anforderungs-URL zu erstellen. Sie können den Dienstnamen und den  `api-key` bei Ihrem Dienstdashboard im Azure-Vorschauportal abrufen. Hilfe zur Seitennavigation finden Sie unter [Erste Schritte mit Azure Search](search-get-started.md).

**Anforderungstextkörper**

Der Textkörper der Anforderung enthält ein oder mehrere Dokument(e) für die Indizierung. Dokumente werden anhand eines eindeutigen Schlüssels identifiziert. Jedem Dokument ist eine Aktion zugeordnet: Hochladen, Zusammenführen, ZusammenführenOderHochladen oder Löschen. Anforderungen zum Hochladen müssen die Dokumentdaten in Form einer Menge von Schlüssel-/Wertpaaren enthalten. 

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }
    
**Dokumentaktionen**

- `upload`: Ein Hochladevorgang ist ähnlich einem "upsert", bei dem das Dokument eingefügt wird, wenn es neu ist, und aktualisiert/ersetzt, wenn es bereits vorhanden ist. Beachten Sie, dass im Fall der Aktualisierung alle Felder ersetzt werden.
- `merge`: Beim Zusammenführen wird ein vorhandenes Dokument mit den angegebenen Feldern aktualisiert. Wenn das Dokument nicht vorhanden ist, tritt bei der Zusammenführung ein Fehler auf. Jedes Feld, das Sie bei einer Zusammenführung angeben, ersetzt das vorhandene Feld im Dokument. Dies schließt Felder vom Typ  `Collection(Edm.String)` ein. Wenn das Dokument beispielsweise ein Feld "tags" mit dem Wert `["budget"]` enthält und Sie eine Zusammenführung mit dem Wert `["economy", "pool"]` für "tags" ausführen, ist der endgültige Wert des Felds "tags" `["economy", "pool"]`. Er lautet **nicht** `["budget", "economy", "pool"]`.
- `mergeOrUpload`: verhält sich wie  `merge`, wenn ein Dokument mit dem angegebenen Indexschlüssel bereits im Index vorhanden ist. Wenn das Dokument nicht vorhanden ist, verhält es sich wie  `upload` bei einem neuen Dokument.
- `delete`: "Delete" entfernt das angegebene Dokument aus dem Index. Beachten Sie, dass Sie bei einem  `delete`-Vorgang nur den Wert des Schlüsselfelds angeben können. Der Versuch, andere Felder anzugeben, führt zu einem HTTP 400-Fehler. Wenn Sie ein einzelnes Feld aus einem Dokument entfernen möchten, verwenden Sie stattdessen  `merge`, und legen Sie das Feld einfach explizit auf  `null` fest. 

**Antwort**

Statuscode: "200 OK" wird als Antwort bei Erfolg zurückgegeben, und das bedeutet, dass alle Elemente erfolgreich indiziert wurden (wie durch den Wert "true" für das  'status'-Feld aller Elemente angegeben):

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": true,
          "errorMessage": null
        }
      ]
    }  

Statuscode: "207" wird zurückgegeben, wenn wenigstens ein Element nicht erfolgreich indiziert wurde (was auch durch den Wert "false" für das  'status'-Feld von Elementen angegeben wird, die nicht indiziert wurden):
 
    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later."
        }
      ]
    }  

Die Eigenschaft  `errorMessage` gibt nach Möglichkeit die Ursache für den Indizierungsfehler an.

**Hinweis**: Wenn bei Ihrem Clientcode häufig die Antwort 207 auftritt, kann eine mögliche Ursache die Auslastung des Systems sein. Sie können dies durch Überprüfen der Eigenschaft  `errorMessage` bestätigen. Wenn dies der Fall ist, empfehlen wir das ***Einschränken von Indizierungsanforderungen***. Andernfalls kann, wenn der Datenverkehr für die Indizierung nicht nachlässt, das System beginnen, alle Anforderungen mit 503-Fehlern abzulehnen.

Statuscode: 429 zeigt an, dass Sie Ihr Kontingent hinsichtlich der Anzahl der Dokumente pro Index überschritten haben. Sie müssen entweder einen neuen Index erstellen oder die Kapazitätsgrenzen durch ein Upgrade heraufsetzen.

**Beispiel:**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
		  "description_fr": "Meilleur hôtel en ville", 
          "hotelName": "Fancy Stay",
		  "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
		  "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
	      "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
		  "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
		  "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## In Dokumenten suchen ##

Ein **Suchvorgang** wird als GET-Anforderung ausgegeben, und gibt Abfrageparameter an, die die Kriterien für die Auswahl übereinstimmender Dokumente enthalten. 

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin key]

**Anforderung**

HTTPS ist für Dienstanforderungen erforderlich. Die **Suchanforderung** kann mithilfe der Methode GET erstellt werden.

Der Anforderungs-URI gibt den abzufragenden Index für alle Dokumente an, die mit den Abfrageparametern übereinstimmen.

**Abfrageparameter**

`search=[zeichenfolge]` (optional) - der zu suchende Text. Standardmäßig werden alle  `searchable` Felder durchsuch, sofern nicht  `searchFields` angegeben ist. Beim Durchsuchen von  `searchable`-Feldern liegt der Suchtext selbst in Form von Token vor, sodass mehrere Ausdrücke durch Leerzeichen getrennt werden können (Beispiel: `search=hello world`). Um Übereinstimmungen mit einem der Ausdrücke anzugeben, verwenden Sie `*` (das kann für Boolesche Filterabfragen nützlich sein). Das Auslassen dieses Parameters hat die gleiche Wirkung wie seine Festlegung auf `*`. Einzelheiten zur Suchsyntax finden Sie unten unter "Einfache Abfragesyntax".

  - **Hinweis**: Die Ergebnisse können manchmal überraschend sein, wenn  `searchable` Felder abgefragt werden. Der Tokenizer enthält Logik zum Behandeln von Fällen, die im Englischen häufig sind, wie etwa Apostrophe, Kommas in Zahlen usw. So entspricht z. B.  `search=123,456`  dem Einzelausdruck "123,456" anstelle der einzelnen Ausdrücke "123" und "456", da Kommas im Englischen bei großen Zahlen als Tausendertrennzeichen verwendet werden. Daher empfehlen wir die Verwendung von Leerzeichen anstelle von Satzzeichen, um Ausdrücke im  `search`-Parameter zu trennen.

`searchMode=any|all` (optional, Standardwert  `any`) - ob einer oder alle Suchbegriffe übereinstimmen müssen, um das Dokument als Treffer zu werten.

`searchFields=[zeichenfolge]` (optional) - die kommagetrennte Liste der Feldnamen, die nach dem angegebenen Text durchsucht werden sollen. Zielfelder müssen als  `searchable` gekennzeichnet sein.

`$skip=#` (optional) - die Anzahl der zu überspringenden Suchergebnisse; kann nicht größer als 100.000 sein. Wenn Sie Dokumente der Reihe nach durchsuchen müssen und aufgrund dieser Einschränkung `$skip` nicht verwenden können, erwägen Sie stattdessen die Verwendung von `$orderby` für einen Schlüssel mit totaler Ordnung und `$filter` für eine Bereichsabfrage. 

`$top=#` (optional) - die Anzahl der anzurufenden Suchergebnisse. Der Standardwert ist 50. Wenn Sie einen Wert größer als 1000 angeben und mehr als 1000 Ergebnisse vorhanden sind, werden nur die ersten 1000 Ergebnisse zurückgegeben, zusammen mit einem Link zur nächsten Ergebnisseite (siehe auch `@odata.nextLink` im [Beispiel unten](#SearchResponse)).

`$count=true|false` (optional, Standardwert  `false`) - ob die Gesamtzahl der Ergebnisse abgerufen werden soll. Das Festlegen dieses Werts auf  `true` kann die Leistung beeinträchtigen. Beachten Sie, dass der zurückgegebene Wert nur ein Näherungswert ist.

`$orderby=[zeichenfolge]` (optional) - eine kommagetrennte Liste von Ausdrücken, nach denen die Ergebnisse sortiert werden sollen. Jeder Ausdruck kann entweder ein Feldname oder ein Aufruf der Funktion  `geo.distance()` sein. Auf jeden Ausdruck kann  `asc`, um aufsteigende Sortierung, oder  `desc`, um absteigende Sortierung anzugeben. Der Standardwert ist die absteigende Sortierung. Bindungen werden durch die Trefferzahlen von Dokumenten durchbrochen. Wenn `$orderby` nicht angegeben wird, ist die standardmäßige Sortierreihenfolge absteigend nach der Trefferzahl der Dokumente. Es gibt ein Limit von 32 Klauseln für `$orderby`.

`$select=[zeichenfolge]` (optional) - eine kommagetrennte Liste der abzurufenden Felder. Wenn der Wert nicht angegeben wird, werden alle im Schema als abrufbar gekennzeichneten Felder eingeschlossen. Außerdem können Sie explizit alle Felder anfordern, indem Sie diesen Parameter auf `*` festlegen.

`facet=[zeichenfolge]` (null oder mehr) - ein Feld für die Facettensuche. Optional kann die Zeichenfolge Parameter zum Anpassen der Facettensuche enthalten, die als kommagetrennte  `name:value`-Paare angegeben sind. Dies sind die gültigen Parameter:

- `count` (maximale Anzahl der Facettenausdrücke; der Standardwert ist 10). Es gibt keinen Maximalwert, höhere Werte bringen jedoch eine entsprechende Verschlechterung der Leistung mit sich, insbesondere, wenn die Facettenfelder eine große Anzahl eindeutiger Ausdrücke enthalten.
  - Beispiel: `facet=category,count:5` ruft die Top 5 der Kategorien bei den Facettenergebnissen ab.  
  - **Hinweis**: Wenn der  `count`-Parameter kleiner als die Anzahl der eindeutigen Ausdrücke ist, sind die Ergebnisse möglicherweise nicht genau. Dies hängt mit der Weise zusammen, wie Facettenabfragen über Shards verteilt werden. Das Erhöhen von  `count` steigert im allgemeinen die Genauigkeit der Ausdrucksanzahl, jedoch um den Preis eingeschränkter Leistung. 
- `sort` (ein Element von  `count`, die  *descending* nach der Anzahl sortiert werden, `-count`, um  *ascending* nach der Anzahl zu sortieren,  `value`, um  *ascending* nach dem Wert, oder `-value`, um  *descending* nach dem Wert zu sortieren)
  - Beispiel:  `facet=category,count:3,sort:count` ruft die drei Top-Kategorien in den Facettenergebnissen in absteigender Reihenfolge nach der Anzahl der Dokumente für die einzelnen Städtenamen ab. Wenn die drei Top-Kategorien z. B. "Budget", "Motel" und "Luxus" sind , und für "Budget" 5, "Motel" 6 und "Luxus" 4 Treffer gefunden wurden, werden die Buckets in der Reihenfolge "Motel, Budget, Luxus" ausgegeben.
  - Beispiel:  `facet=rating,sort:-value` ergibt Buckets für alle möglichen Bewertungen nach dem Wert in absteigender Reihenfolge. Wenn die Bewertungen beispielsweise von 1 bis 5 reichen, sind die Buckets als "5, 4, 3, 2, 1" sortiert, unabhängig von der Anzahl der Dokumente, die für jede Bewertung vorhanden sind.
- `values` (durch Pipesymbole getrennte numerische oder  `Edm.DateTimeOffset`-Werte, die eine dynamische Menge von Facetteneintragswerten angeben)
  - Beispiel: `facet=baseRate,values:10|20` ergibt drei Buckets: Einen für den Basistarif von 0 bis 10 (10 aber ausschließend), einen für Werte von 10 bis 20 (20 ausschließend) und einen für 20 und höhere Werte.
  - Beispiel: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` ergibt zwei Buckets: Einen für Hotels, die vor dem Februar 2010 und einen für Hotels, die ab dem 1. Februar 2010 renoviert wurden.
- `interval` (ganzzahliges Intervall größer als 0 für Zahlen oder  `minute`,  `hour`,  `day`,  `week`, `month`,  `quarter` und  `year` für Datums-/Uhrzeitwerte)
  - Beispiel: `facet=baseRate,interval:100` ergibt Buckets für Basistarifbereiche mit der Größe 100. Wenn die Basistarife z. B. alle zwischen 60 € und 600 € liegen, gibt es Buckets für "0-100", "100-200", "200-300", "300-400", "400-500" und "500-600".
  - Beispiel:  `facet=lastRenovationDate,interval:year` ergibt einen Bucket für jedes Jahr, in dem Hotels renoviert wurden.
- **Hinweis**:  `count` und  `sort` können in der gleichen Facettenspezifikation kombiniert werden, sie können jedoch nicht mit  `interval` oder  `values` zusammen verwendet werden, und  `interval` und  `values` können nicht miteinander kombiniert werden.

`$filter=[zeichenfolge]` (optional) - ein strukturierter Suchausdruck in standardmäßiger OData-Syntax. Unter [OData-Ausdruckssyntax](#ODataExpressionSyntax) finden Sie Details zur Teilmenge der OData-Ausdrucksgrammatik, die von Azure Search unterstützt wird.

`highlight=[zeichenfolge]` (optional) - eine Menge durch Kommas getrennter Feldnamen, die für die Hervorhebung von Treffern verwendet wird. Nur  `searchable`-Felder können für die Hervorhebung von Treffern verwendet werden.

`scoringProfile=[zeichenfolge]` (optional) - der Name eines Bewertungsprofils zum Bewerten von Übereinstimmungspunktzahlen für übereinstimmende Dokumente zum Sortieren der Ergebnisse.

`scoringParameter=[zeichenfolge]` (null oder mehr) - zeigt den Wert für jeden in einer Bewertungsfunktion definierten Parameter (z. B.  `referencePointParameter`) im Format Name:Wert an. Wenn z. B. das Bewertungsprofil eine Funktion mit einem Parameter mit dem Namen "meinstandort" definiert, lautet die entsprechende Abfragezeichenfolgenoption "&scoringParameter=meinstandort:-122.2,44.8".

`api-version=[zeichenfolge]` (erforderlich). Zu den gültigen Werten gehören `2014-07-31-Preview` und `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen, im allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Details finden Sie unter [Search Service-Versionsverwaltung](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Hinweis: Für diesen Vorgang wird die  `api-version` als Abfrageparameter angegeben.

**Anforderungsheader**

In der folgenden Liste sind die erforderlichen und optionalen Anforderungsheader beschrieben. 

- `api-key`: Der  `api-key` wird verwendet, um die Anforderung bei Ihrem Suchdienst zu authentifizieren. Es handelt sich um einen Zeichenfolgenwert, der für Ihre Dienst-URL eindeutig ist. Die **Suchanforderung** kann für  `api-key` wahlweise einen Administratorschlüssel oder einen Abfrageschlüssel angeben.
 
Ferner benötigen Sie den Dienstnamen, um die Anforderungs-URL zu erstellen. Sie können den Dienstnamen und den  `api-key` bei Ihrem Dienstdashboard im Azure-Vorschauportal abrufen. Hilfe zur Seitennavigation finden Sie unter [Erste Schritte mit Azure Search](search-get-started.md).

**Anforderungstextkörper**

Keiner.

<a name="SearchResponse"></a>
**Antwort**

Status Code: "200 OK" wird als Antwort bei Erfolg zurückgegeben.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if $top is greater than 1000)
    }
    
**Beispiele:**

Sie finden weitere Beispiele auf der Seite [OData-Ausdruckssyntax für Azure Search](https://msdn.microsoft.com/library/azure/dn798921.aspx). 

1)	Durchsuchen des Index mit absteigender Sortierung nach Datum. 

    GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-07-31-Preview

HINWEIS: Die Genauigkeit von DateTime-Feldern ist auf Millisekunden beschränkt. Wenn Sie einen Zeitstempel mit kleineren Werten (sehen Sie sich beispielsweise den Sekundenteil dieses Zeitstempels an: 10:30:09,7552052) übergeben, wird der Rückgabewert aufgerundet (in diesem Fall auf 10:30:09,7550000).

2)	Bei einer Facettensuche wird der Index durchsucht, und es werden Facetten für Kategorien, Bewertungen, Tags sowie Elemente mit "baseRate" in bestimmten Bereichen abgerufen:

    GET /indexes/hotels/docs?search=test&facet=category&facet=rating&facet=tags&facet=baseRate,values:80|150|220&api-version=2014-07-31-Preview

3)	Mithilfe eines Filters werden die vorhergehenden Facettenabfrageergebnisse eingeschränkt, wenn der Benutzer auf die Bewertung "3" und die Kategorie "Motel" klickt:

    GET /indexes/hotels/docs?search=test&facet=tags&facet=baseRate,values:80|150|220&$filter=rating eq 3 and category eq 'Motel'&api-version=2014-07-31-Preview

4) Legen Sie in einer Facettensuche einen oberen Grenzwert für die in einer Abfrage zurückgegebenen eindeutigen Ausdrücke fest. Der Standardwert ist 10, Sie können diesen Wert jedoch mithilfe des Parameters  `count` für das  `facet`-Attribut herauf- oder heruntersetzen:

    GET /indexes/hotels/docs?search=test&facet=city,count:5&api-version=2014-07-31-Preview

5)	Durchsuchen Sie den Index innerhalb bestimmter Felder; z. B. ein sprachspezifisches Feld:

    GET /indexes/hotels/docs?search=hôtel&searchFields=description_fr&api-version=2014-07-31-Preview

6) Durchsuchen Sie den Index über mehrere Felder hinweg. Beispielsweise können Sie durchsuchbare Felder in mehreren Sprachen speichern und abrufen, alles innerhalb des gleichen Index.  Wenn englische und französische Beschreibungen zugleich im gleichen Dokument vorhanden sind, können Sie in den Abfrageergebnissen alle oder nur bestimmte zurückgeben:

	GET /indexes/hotels/docs?search=hotel&searchFields=description,description_fr&api-version=2014-07-31-Preview
	
Beachten Sie, dass Sie jeweils nur einen Index abfragen können. Erstellen Sie nicht mehrere Indizes für jede Sprache, sofern Sie nicht vorhaben, sie jeweils einzeln abzufragen.

7)	Paging - Abrufen der ersten Seite von Elementen (die Seitengröße beträgt 10):

    GET /indexes/hotels/docs?search=*&$skip=0&$top=10&api-version=2014-07-31-Preview

8)	Paging - Abrufen der zweiten Seite von Elementen (die Seitengröße beträgt 10):

    GET /indexes/hotels/docs?search=*&$skip=10&$top=10&api-version=2014-07-31-Preview

9)	Abrufen einer bestimmten Zusammenstellung von Feldern:

    GET /indexes/hotels/docs?search=*&$select=hotelName,description&api-version=2014-07-31-Preview

10)  Abrufen von Dokumenten, die einem bestimmten Abfrageausdruck entsprechen

    GET /indexes/hotels/docs?$filter=(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'&api-version=2014-07-31-Preview

11) Durchsuchen des Index und Rückgabe von Fragmenten mit hervorgehobenen Treffern

    GET /indexes/hotels/docs?search=something&highlight=description&api-version=2014-07-31-Preview
    
12) Durchsuchen des Index und Rückgabe von Dokumenten, die nach der Entfernung (von näher nach weiter entfernt) von einem Referenzstandort sortiert sind

    GET /indexes/hotels/docs?search=something&$orderby=geo.distance(location, geography'POINT(-122.12315 47.88121)')&api-version=2014-07-31-Preview

13) Durchsuchen des Index unter der Voraussetzung, dass ein Bewertungsprofil mit dem Namen "geo" vorhanden ist, das zwei Bewertungsfunktionen enthält; eine definiert einen Parameter "currentLocation", die andere einen Parameter "lastLocation"

    GET /indexes/hotels/docs?search=something&scoringProfile=geo&scoringParameter=currentLocation:-122.123,44.77233&scoringParameter=lastLocation:-121.499,44.2113&api-version=2014-07-31-Preview


<a name="LookupAPI"></a>
##Dokumentsuche ##

Der Vorgang **Lookup Document** ruft ein Dokument bei Azure Search ab. Dies ist nützlich, wenn ein Benutzer auf ein bestimmtes Suchergebnis klickt und Sie spezifische Details zum Dokument nachschlagen möchten.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters] 
    api-key: [admin key]
    
**Anforderung**

HTTPS ist für Dienstanforderungen erforderlich. Die Anforderung **Lookup Document** kann wie folgt erstellt werden. 

    GET /indexes/[index name]/docs/key?[query parameters] 

Alternativ können Sie die traditionelle OData-Syntax für die Schlüsselsuche verwenden:

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

Der Anforderungs-URI enthält einen [indexname] und einen [key], die angeben, welches Dokument beim Index abgerufen werden soll. Es kann immer nur jeweils ein Dokument abgerufen werden. Verwenden Sie **Search**, um mehrere Dokumente in einer einzelnen Anforderung abzurufen. 

**Abfrageparameter**

`$select=[zeichenfolge]` (optional) - eine kommagetrennte Liste der abzurufenden Felder. Wenn keine Angabe oder die Angabe `*` vorliegt, werden alle im Schema als abrufbar gekennzeichneten Felder in die Projektion aufgenommen.

`api-version=[zeichenfolge]` (erforderlich). Zu den gültigen Werten gehören `2014-07-31-Preview` und `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen, im allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Details finden Sie unter [Search Service-Versionsverwaltung](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Hinweis: Für diesen Vorgang wird die  `api-version` als Abfrageparameter angegeben.

**Anforderungsheader**

In der folgenden Liste sind die erforderlichen und optionalen Anforderungsheader beschrieben.

- `api-key`: Der  `api-key` wird verwendet, um die Anforderung bei Ihrem Suchdienst zu authentifizieren. Es handelt sich um einen Zeichenfolgenwert, der für Ihre Dienst-URL eindeutig ist. Die **Lookup Document**-Anforderung kann entweder einen Administratorschlüssel oder einen Abfrageschlüssel für  `api-key` angeben.
 
Ferner benötigen Sie den Dienstnamen, um die Anforderungs-URL zu erstellen. Sie können den Dienstnamen und den  `api-key` bei Ihrem Dienstdashboard im Azure-Vorschauportal abrufen. Hilfe zur Seitennavigation finden Sie unter [Erste Schritte mit Azure Search](search-get-started.md).

**Anforderungstextkörper**

Keiner.

**Antwort**

Status Code: "200 OK" wird als Antwort bei Erfolg zurückgegeben.

    {
      field_name: field_value (fields matching the default or specified projection)
    }
    
**Beispiel**

Suchen Sie das Dokument mit dem Schlüssel '2'

    GET /indexes/hotels/docs/2?api-version=2014-07-31-Preview

Suchen Sie das Dokument mit dem Schlüssel '3', und verwenden Sie dazu OData-Syntax:

    GET /indexes('hotels')/docs('3')?api-version=2014-07-31-Preview

<a name="CountDocs"></a>
##Count Documents##

Der Vorgang **Count Documents** ruft eine Zahl für die Anzahl der Dokumente in einem Suchindex ab. Die `$count`-Syntax ist Bestandteil des OData-Protokolls.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin key]
    
**Anforderung**

HTTPS ist für Dienstanforderungen erforderlich. Die **Count Documents**-Anforderung kann mithilfe der GET-Methode erstellt werden.

Der [indexname] im Anforderungs-URI weist den Dienst an, die Anzahl aller Elemente in der Dokumentauflistung des angegebenen Index zurückzugeben. 

Der Parameter  `api-version` ist erforderlich. Zu den gültigen Werten gehören `2014-07-31-Preview` und `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen, im allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Details finden Sie unter [Search Service-Versionsverwaltung](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Anforderungsheader**

In der folgenden Liste sind die erforderlichen und optionalen Anforderungsheader beschrieben.

- `Accept`: Dieser Wert muss auf  `text/plain` festgelegt sein.
- `api-key`: Der  `api-key` wird verwendet, um die Anforderung bei Ihrem Suchdienst zu authentifizieren. Es handelt sich um einen Zeichenfolgenwert, der für Ihre Dienst-URL eindeutig ist. Die **Count Documents**Anforderung kann wahlweise einen Administratorschlüssel oder einen Abfrageschlüssel für  `api-key` angeben.
 
Ferner benötigen Sie den Dienstnamen, um die Anforderungs-URL zu erstellen. Sie können den Dienstnamen und den  `api-key` bei Ihrem Dienstdashboard im Azure-Vorschauportal abrufen. Hilfe zur Seitennavigation finden Sie unter [Erste Schritte mit Azure Search](search-get-started.md).

**Anforderungstextkörper**

Keiner.

**Antwort**

Status Code: "200 OK" wird als Antwort bei Erfolg zurückgegeben.

Der Antworttextkörper enthält den Wert für die Anzahl als ganze Zahl im Format "unformatierter Text".

<a name="Suggestions"></a>
##Suggestions##

Der Vorgang **Suggestions** ruft Vorschläge auf der Grundlage teilweiser Sucheingaben ab. Er wird normalerweise in Suchfeldern verwendet, um Vorschläge bei der Eingabe zu machen, wenn Benutzer Suchausdrücke eingeben.

Textvorschläge können wiederholt werden, wenn mehrere Kandidaten mit der gleichen Sucheingabe übereinstimmen. Sie können `$select` verwenden, um andere Dokumentfelder (einschließlich des Dokumentschlüssels) abzurufen, sodass Sie unterscheiden können, aus welchen Dokumenten die einzelnen Vorschläge stammen.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin key]

**Anforderung**

HTTPS ist für Dienstanforderungen erforderlich. Die **Suggestions**-Anforderung kann mithilfe der GET-Methode erstellt werden.

Der Anforderungs-URI gibt den Namen des abzufragenden Index an. Er enthält darüber hinaus den teilweise eingegebenen Suchausdruck in der Abfragezeichenfolge.

**Abfrageparameter**

`search=[zeichenfolge]` - der Suchtext für das Vorschlagen von Abfragen. Muss mindestens 3 Zeichen und darf höchstens 25 Zeichen lang sein.

`fuzzy=[boolean]` (optional, Standardwert = false) - bei der Einstellung auf "true" findet diese API auch dann Vorschläge, wenn ein Zeichen im Suchtext vertauscht wurde oder fehlt. Dies liefert zwar in vielen Szenarien eine bessere Benutzererfahrung, wird aber mit erhöhtem Leistungsaufwand erkauft, da die Suche mit unscharfen Vorschlägen langsamer ist und mehr Ressourcen benötigt.

`searchFields=[zeichenfolge]` (optional) - die Liste der durch Kommas getrennter Feldnamen für den angegebenen Suchtext. Für Vorschläge müssen Zielfelder aktiviert sein.

`$top=#` (optional, Standardwert = 5) - die Anzahl der abzurufenden Vorschläge. Es muss sich um eine Zahl zwischen 1 und 10 handeln.

`$filter=[zeichenfolge]` (optional) - ein Ausdruck, der die für Vorschläge berücksichtigten Dokumente filtert.

`$orderby=[zeichenfolge]` (optional) - eine kommagetrennte Liste von Ausdrücken, nach denen die Ergebnisse sortiert werden sollen. Jeder Ausdruck kann entweder ein Feldname oder ein Aufruf der Funktion  `geo.distance()` sein. Auf jeden Ausdruck kann  `asc`, um aufsteigende Sortierung, oder  `desc`, um absteigende Sortierung anzugeben. Der Standardwert ist die absteigende Sortierung. Es gibt ein Limit von 32 Klauseln für `$orderby`.

`$select=[zeichenfolge]` (optional) - eine kommagetrennte Liste der abzurufenden Felder. Ohne Angabe werden nur der Dokumentschlüssel und der Vorschlagstext zurückgegeben.

`api-version=[zeichenfolge]` (erforderlich). Zu den gültigen Werten gehören `2014-07-31-Preview` und `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen, im allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Details finden Sie unter [Search Service-Versionsverwaltung](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Hinweis: Für diesen Vorgang wird die  `api-version` als Abfrageparameter angegeben.

**Anforderungsheader**

In der folgenden Liste sind die erforderlichen und optionalen Anforderungsheader beschrieben.

- `api-key`: Der  `api-key` wird verwendet, um die Anforderung bei Ihrem Suchdienst zu authentifizieren. Es handelt sich um einen Zeichenfolgenwert, der für Ihre Dienst-URL eindeutig ist. Die **Suggestions**-Anforderung kann wahlweise einen Administratorschlüssel oder einen Abfrageschlüssel als  `api-key` angeben.

Ferner benötigen Sie den Dienstnamen, um die Anforderungs-URL zu erstellen. Sie können den Dienstnamen und den  `api-key` bei Ihrem Dienstdashboard im Azure-Vorschauportal abrufen. Hilfe zur Seitennagivation finden Sie unter [Erste Schritte mit Azure Search](search-get-started.md).

**Anforderungstextkörper**

Keiner.

**Antwort**

Status Code: "200 OK" wird als Antwort bei Erfolg zurückgegeben.

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Wenn die Projektionsoption zum Abrufen von Feldern verwendet wird, sind sie in jedem Element des Arrays enthalten:

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

 **Beispiel**

Frage 5 Vorschläge ab, die diese teilweise Sucheingabe aufweisen:  'lux'.

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&api-version=2014-07-31-Preview





<!--HONumber=54--> 