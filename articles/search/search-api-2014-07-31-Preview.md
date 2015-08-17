<properties pageTitle="Azure Search-Dienst-REST-API: Version 2014-07-31-Preview" description="Azure Search-Dienst-REST-API: Version 2014-07-31-Preview" services="search" documentationCenter="" authors="HeidiSteen" manager="mblythe" editor=""/>

<tags ms.service="search" ms.devlang="rest-api" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="na" ms.date="07/22/2015" ms.author="heidist" />

# Azure Search-Dienst-REST-API: Version 2014-07-31-Preview

Dieses Dokument beschreibt die ältere Version **2014-07-31-Preview** der Azure Search-Dienst-REST-API, die für die öffentliche Vorschau von Azure Search im August 2014 veröffentlicht wurde. Da diese Version in Kürze ausgemustert wird, empfehlen wir dringend, die Version der allgemein verfügbaren Veröffentlichung zu verwenden. Eine Anleitung zur Codemigration finden Sie unter [Umstellung von der Vorschau-API-Version auf die allgemein verfügbare API-Version](search-transition-from-preview.md).

Zu den weiteren API-Inhalten in Bezug auf **2014-07-31-Preview** gehören die folgenden:

- [Bewertungsprofile (Azure Search-Dienst-REST-API: Version 2014-07-31-Preview)](search-api-scoring-profiles-2014-07-31-preview.md)

Eine Dokumentation der aktuellen, allgemein verfügbaren Version der Azure Search-Dienst-REST-API finden Sie auf MSDN. Weitere Informationen finden Sie unter [Azure Search-Dienst-REST-API](http://msdn.microsoft.com/library/azure/dn798935.aspx).

##Grundlegendes zur Suchdienst-REST-API

Azure Search ist ein cloudbasierter Dienst, mit dem Sie benutzerdefinierte Suchanwendungen erstellen können. Azure Search verwendet das Konzept der *Suchdienste* und *Indizes*, wobei ein Suchdienst einen Index oder mehrere Indizes umfasst. Ihr Suchdienst wird durch einen vollqualifizierten Domänennamen eindeutig identifiziert (z. B. `mysearchservice.search.windows.net`). Beim Bereitstellen des Diensts wird ein API-Schlüssel generiert. Dieser wird zum Authentifizieren von Anforderungen für Ihren Azure Search-Dienst verwendet.

Es gibt zwei Arten von Aktionen, die für den Azure Search-Dienst ausgeführt werden können:

- **Indexverwaltung**: Dies schließt Verwaltungsaufgaben ein, die für einen Suchdienst oder Suchindex ausgeführt werden.

- **Dokumentaktionen**: Diese Aktionen fragen den Korpus auf einen bestimmten Index ab und verwalten ihn.

Die in diesem Abschnitt dokumentierten APIs bieten Zugriff auf Vorgänge für Suchdaten, beispielsweise das Erstellen und Auffüllen des Indexes, das Hochladen von Dokumenten und das Ausführen von Abfragen. Beachten Sie beim Aufruf der API folgende Punkte:

- Alle APIs sind als HTTP-Anforderungen und -Antworten im OData-JSON-Format definiert.

- Alle APIs müssen im Header oder der Abfragezeichenfolge mit einem `api-key` versehen sein, wie in den nachstehenden Hinweisen beschrieben.

- Alle APIs müssen über HTTPS (auf Standardport 443) ausgegeben werden.

- Alle API-Anforderungen müssen den `api-version`-Parameter für Abfragezeichenfolgen enthalten. Dessen Wert muss auf die aktuelle Dienstversion festgelegt werden, wie im folgenden Beispiel gezeigt wird:

GET /indexes?api-version=2014-07-31-Preview

- Für alle API-Anforderungen kann optional der `Accept`-HTTP-Header festgelegt werden. Wird dieser nicht festgelegt, wird als Standardwert `application/json` verwendet.

Für die Dienstverwaltung steht eine separate API zur Verfügung. Beispiele von Vorgängen zur Dienstverwaltung sind etwa die Bereitstellung des Diensts oder das Ändern seiner Kapazität. Weitere Informationen zu dieser API finden Sie unter "Azure Search-Verwaltungs-REST-API".

### Endpunkt

Der Endpunkt für Dienstvorgänge ist die URL des von Ihnen bereitgestellten Azure Search-Diensts: https://<yourService>.search.windows.net.


### Versionen

Es gibt verschiedene API-Versionen für Azure Search. Eine Liste der verfügbaren Versionen finden Sie unter [Versionsverwaltung für den Suchdienst](http://msdn.microsoft.com/library/azure/dn864560.aspx).


<a name="Authentication"></a>
### Authentifizierung und Zugriffssteuerung

Für die Authentifizierung bei einem Azure Search-Dienst sind zwei Angaben erforderlich: die URL des Suchdiensts und ein `api-key`. Die `api-keys` werden bei der Erstellung des Diensts generiert und können nach der Bereitstellung des Diensts bei Bedarf neu generiert werden. `api-key` sind in zwei Varianten verfügbar:

- als Administratorschlüssel, der Zugriff auf alle Vorgänge (einschließlich Schreibvorgänge wie Erstellen und Löschen von Indizes) gewährt
- als Abfrageschlüssel, der schreibgeschützte Anforderungen authentifiziert

Pro Dienst sind zwei Administratorschlüssel und bis zu 50 Abfrageschlüssel zulässig. Zwei Administratorschlüssel sind zu empfehlen, wenn für einen der Schlüssel ein Rollover ausgeführt werden muss.

Die Zugriffssteuerung ist auf die Dienstverwaltung über die rollenbasierte Zugriffssteuerung (RBAC) im Azure-Vorschauportal beschränkt. Rollen werden verwendet, um Zugriffsebenen für die Dienstverwaltung festzulegen. Beispielsweise ist die Anzeige des Administratorschlüssels auf die Rollen "Besitzer" und "Mitwirkender" beschränkt, wohingegen die Anzeige des Dienststatus für Mitglieder aller Rollen möglich ist.

Der Zugriff auf die für einen Suchdienstendpunkt durchgeführten Datenvorgänge, einschließlich Indexverwaltung, Indexauffüllung und Abfragen, erfolgt ausschließlich über `api-keys`. RBAC betrifft weder index- noch dokumentbezogene Vorgänge. Weitere Informationen zu `api-keys` oder RBAC in Azure Search finden Sie unter [Verwalten Ihres Suchdiensts in Microsoft Azure](search-manage.md).

**Hinweis**: Im Allgemeinen wird es aus Sicherheitsgründen als schlechte Praxis angesehen, vertrauliche Daten wie z. B. `api-key` im Anforderungs-URI zu übergeben. Aus diesem Grund akzeptiert Azure Search einen Abfrageschlüssel nur als `api-key` in der Abfragezeichenfolge, und Sie sollten von der beschriebenen Vorgehensweise absehen – es sei denn, Sie möchten den Inhalt Ihres Index öffentlich verfügbar machen Stattdessen sollten Sie Ihren `api-key` als Anforderungsheader übergeben.

###Übersicht über die APIs

Die Azure Search-Dienst-API unterstützt für die Entitätssuche zwei Syntaxarten: die [einfache](https://msdn.microsoft.com/library/dn798920.aspx) und die alternative OData-Syntax (weitere Informationen finden Sie unter [Unterstützung für OData (Azure Search-API)](http://msdn.microsoft.com/library/azure/dn798932.aspx)). Die folgende Liste zeigt die einfache Syntax.

[Index erstellen](#CreateIndex)

    POST /indexes?api-version=2014-07-31-Preview

[Index aktualisieren](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2014-07-31-Preview

[Index abrufen](#GetIndex)

    GET /indexes/[index name]?api-version=2014-07-31-Preview

[Indizes auflisten](#ListIndexes)

    GET /indexes?api-version=2014-07-31-Preview

[Indexstatistiken abrufen](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2014-07-31-Preview

[Index löschen](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2014-07-31-Preview

[Daten innerhalb eines Index hinzufügen, löschen und aktualisieren](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2014-07-31-Preview

[Dokumente durchsuchen](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]

[Dokument suchen](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Dokumentenanzahl](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2014-07-31-Preview

[Vorschläge](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]

________________________________________
<a name="IndexOps"></a>
## Indexvorgänge

Das Erstellen und Verwalten von Indizes in Azure Search erfolgt über einfache HTTP-Anforderungen (POST, GET, PUT, DELETE) für einen bestimmten Index, der als Ressource vorliegt. Um einen Index zu erstellen, stellen Sie zunächst mit POST ein JSON-Dokument bereit, in dem das Indexschema beschrieben wird. Das Schema definiert die Indexfelder sowie deren Datentype und Verwendungsweise (z. B. in Volltextsuchen, zum Filtern, Sortieren, Facettieren oder in Vorschlägen). Zudem definiert es Bewertungsprofile und andere Attribute, um das Verhalten des Index zu konfigurieren.

Das folgende Beispiel veranschaulicht ein Schema für die Suche von Hotelinformationen, bei dem das Feld "Beschreibung" in zwei Sprachen definiert ist. Beachten Sie, wie die Verwendung des Felds mithilfe von Attributen gesteuert wird. `hotelId` dient beispielsweise als Dokumentschlüssel (`"key": true`) und wird bei Volltextsuchen ausgeschlossen (`"searchable": false`).

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

Nachdem Sie den Index erstellt haben, laden Sie Dokumente hoch, um den Index zu füllen. Diesen nächsten Schritt finden Sie unter [Hinzufügen oder Aktualisieren von Dokumenten](#AddOrUpdateDocuments).

Eine Videoeinführung zur Indizierung in Azure Search erhalten Sie in der [Channel 9 Cloud Cover-Folge zu Azure Search](http://go.microsoft.com/fwlink/p/?LinkId=511509) (in englischer Sprache).


<a name="CreateIndex"></a>
## Index erstellen

Sie können einen neuen Index innerhalb des Azure Search-Diensts mithilfe einer HTTP POST-Anforderung erstellen. Der Text der Anforderung ist ein JSON-Dokument, das Indexname, Felder, Attribute zum Steuern des Abfrageverhaltens, eine Bewertung der Ergebnisse sowie CORS-Optionen angibt.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Alternativ können Sie mit PUT den Indexnamen für den URI angeben. Wenn der Index nicht vorhanden ist, wird er erstellt.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

**Hinweis**: Die maximal zulässige Anzahl von Indizes variiert je nach Tarif. Im Free-Tarif sind bis zu drei Indizes möglich. Im Standard-Tarif sind 50 Indizes pro Suchdienst zulässig. Weitere Details finden Sie im Abschnitt [Limits und Einschränkungen](http://msdn.microsoft.com/library/azure/dn798934.aspx).

**Anforderung**

HTTPS ist für alle Dienstanforderungen erforderlich. Die Anforderung **Index erstellen** kann mit der POST- oder PUT-Methode konstruiert werden. Wenn POST verwendet wird, müssen Sie im Anforderungstext einen Namen und eine Schemadefinition für den Index bereitstellen. Bei Verwendung von PUT ist der Indexname Teil der URL. Wenn der Index nicht vorhanden ist, wird er erstellt. Wenn er bereits vorhanden ist, wird er mit der neuen Definition aktualisiert.

Der Indexname muss in Kleinbuchstaben angegeben werden und mit einem Buchstaben oder einer Zahl beginnen. Er darf keine Schrägstriche oder Punkte enthalten und muss weniger als 128 Zeichen lang sein. Der Rest des Indexnamens (nach dem Buchstaben bzw. der Zahl zu Beginn des Namens) kann beliebige Buchstaben, Zahlen und Bindestriche enthalten, solange die Bindestriche nicht aufeinanderfolgen.

`api-version` ist erforderlich. Gültige Werte sind `2014-07-31-Preview` oder `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen. Im Allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu beurteilen, wie etwa die Unterstützung für Sprachanalyse, die sich im Analyse-Indexattribut ausdrückt. Ausführliche Informationen zu den API-Versionen finden Sie unter [Versionsverwaltung für den Suchdienst](http://msdn.microsoft.com/library/azure/dn864560.aspx). Unter [Sprachunterstützung](#LanguageSupport) finden Sie ausführliche Informationen zu den Sprachanalysemodulen.

**Anforderungsheader**

In der folgenden Liste werden die erforderlichen und optionalen Anforderungsheader beschrieben.

- `Content-Type`: Erforderlich. Auf `application/json` festlegen.
- `api-key`: Erforderlich. Mit dem `api-key` wird
- die Anforderung für den Suchdienst authentifiziert. Es handelt sich um einen für Ihren Dienst eindeutigen Zeichenfolgewert. Die Anforderung **Index erstellen** muss einen `api-key`-Header enthalten, der auf Ihren Administratorschlüssel (keinen Abfrageschlüssel) festgelegt ist.

Sie benötigen außerdem den Dienstnamen, um die URL der Anforderung zu erstellen. Sie finden den Dienstnamen und den `api-key` in Ihrem Dienst-Dashboard im Azure-Vorschauportal. Hilfe bei der Seitennavigation finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service.portal.md).

<a name="RequestData"></a> **Syntax des Anforderungstexts**

Der Anforderungstext enthält eine Schemadefinition. Diese umfasst eine Liste der in Dokumenten enthaltenen Datenfelder, die in diesen Index eingelesen werden, Datentypen, Attributen sowie eine optionale Liste der Bewertungsprofile, mit denen zur Abfragezeit übereinstimmende Dokumente bewertet werden.

Beachten Sie, dass für eine POST-Anforderung der Indexname im Anforderungstext angegeben sein muss.

Es kann nur ein Schlüsselfeld im Index vorhanden sein. Dieses muss ein Zeichenfolgefeld sein. Dieses Feld repräsentiert den eindeutigen Bezeichner für jedes Dokument, das im Index gespeichert ist.

Die Syntax für die Strukturierung der Anforderungsnutzlast ist wie folgt. Eine Beispielanforderung wird weiter unten in diesem Thema bereitgestellt.

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

Hinweis: Der Datentyp `Edm.Int64` wird ab API-Version 2014-10-20-Preview unterstützt.

**Indexattribute**

Beim Erstellen eines Indexes können die folgenden Attribute festgelegt werden. Ausführliche Informationen zum Bewerten und zu Bewertungsprofilen finden Sie unter [Hinzufügen von Bewertungsprofilen zu einem Suchindex](http://msdn.microsoft.com/library/azure/dn798928.aspx).

`name`: Legt den Feldnamen fest.

`type`: Legt den Datentyp des Feldes fest. Eine Liste der unterstützten Typen finden Sie unter [Unterstützte Datentypen](#DataTypes).

`searchable`: Markiert das Feld als in die Volltextsuche einbeziehbar. Es wird während der Indizierung somit Analysen wie etwa der Worttrennung unterzogen. Wenn Sie ein Feld vom Typ `searchable` auf einen Wert wie "sonniger Tag" setzen, wird es zunächst in die einzelnen Token "sonniger" und "Tag" unterteilt. Dies ermöglicht die Volltextsuche nach diesen Begriffen. Felder vom Typ `Edm.String` oder `Collection(Edm.String)` haben standardmäßig das Attribut `searchable`. Andere Feldtypen können das Attribut `searchable` nicht haben.

  - **Hinweis**: `searchable` Felder belegen in Ihrem Index zusätzlichen Platz, da Azure Search eine zusätzliche, mit Token versehene Version des Feldwerts für Volltextsuchen speichert. Wenn Sie Speicherplatz im Index sparen möchten und ein Feld in Suchvorgängen nicht benötigen, setzen Sie `searchable` auf `false`.

`filterable`: Hiermit kann das Feld in `$filter`-Abfragen referenziert werden. Mit `filterable` werden Zeichenfolgen anders als mit `searchable` behandelt. Felder vom Typ `Edm.String` oder `Collection(Edm.String)` mit dem Attribut `filterable` werden keiner Worttrennung unterzogen, sodass nur nach exakten Übereinstimmungen gesucht wird. Beispiel: Wenn Sie ein solches Feld `f` auf "sonniger Tag" setzen, werden mit `$filter=f eq 'sunny'` keine Übereinstimmungen gefunden, während Sie mit `$filter=f eq 'sunny day'` Suchergebnisse erhalten. Standardmäßig haben alle Felder das Attribut `filterable`.

`sortable`: Ergebnisse werden standardmäßig nach Bewertung sortiert. Häufig möchten Benutzer die Ergebnisse jedoch nach in den Dokumenten enthaltenen Feldern sortieren. Felder vom Typ `Collection(Edm.String)` können nicht das Attribut `sortable` haben. Alle anderen Felder haben standardmäßig das Attribut `sortable`.

`facetable`: Wird in der Regel in Anzeigen von Suchergebnissen verwendet, die eine nach Kategorie sortierte Trefferanzahl beinhalten (z. B. werden bei der Suche nach Digitalkameras Treffer nach Marke, Megapixel, Preis usw. angezeigt). Diese Option kann nicht für Felder vom Typ `Edm.GeographyPoint` verwendet werden. Alle anderen Felder haben standardmäßig das Attribut `facetable`.

  - **Hinweis**: Felder vom Typ `Edm.String` mit dem Attribut `filterable`, `sortable` oder `facetable` können maximal 32 KB lang sein. Dies liegt daran, dass diese Felder als ein Suchbegriff behandelt werden und dafür in Azure Search eine maximale Länge von 32 KB gilt. Wenn Sie in einem Zeichenfolgefeld mehr Text speichern möchten, setzen Sie `filterable`, `sortable` und `facetable` in Ihrer Indexdefinition explizit auf `false`.

`suggestions`: Legt fest, ob das Feld für die automatische Vervollständigung bei der Eingabe verwendet werden kann. Diese kann nur für Felder vom Typ `Edm.String` oder `Collection(Edm.String)` festgelegt werden. `suggestions` ist standardmäßig `false`, da hierfür zusätzlicher Platz im Index erforderlich ist.

  - **Hinweis**: Wenn keines der oben genannten Attribute für ein Feld auf `true` (`searchable`, `filterable`, `sortable`, `facetable` oder `suggestions`) festgelegt ist, ist das Feld effektiv vom invertierten Index ausgeschlossen. Diese Option ist für Felder nützlich, die nicht in Abfragen verwendet werden, aber in den Suchergebnissen erforderlich sind. Durch das Ausschließen dieser Felder aus dem Index verbessern Sie die Suchleistung.

`key`: Gibt an, dass das Feld eindeutige Bezeichner für Dokumente innerhalb des Index enthält. Es kann genau nur ein Feld als `key`-Feld gewählt werden und es muss vom Typ `Edm.String` sein. Mit Schlüsselfeldern können Dokumente direkt über die [Lookup-API](#LookupAPI) gesucht werden.

`retrievable`: Legt fest, ob das Feld in einem Suchergebnis zurückgegeben werden kann. Dies ist hilfreich, wenn Sie ein Feld (z. B. die Gewinnspanne) zum Filtern, Sortieren oder Bewerten verwenden möchten, das Feld jedoch für den Endbenutzer nicht sichtbar sein soll. Dieses Attribut muss für `key`-Felder auf `true` gesetzt sein.

`scoringProfiles`: Definiert benutzerdefinierte Bewertungsverhalten, mit denen Sie beeinflussen können, welche Elemente weiter oben in den Suchergebnissen angezeigt werden. Bewertungsprofile bestehen aus gewichteten Feldern und Funktionen. Weitere Informationen zu den in einem Bewertungsprofil verwendeten Attributen finden Sie unter [Hinzufügen von Bewertungsprofilen zu einem Suchindex](http://msdn.microsoft.com/library/azure/dn798928.aspx).

`analyzer`: Legt den Namen der für das Feld zu verwendenden Textanalyse fest. Die zulässigen Werte finden Sie unter [Sprachunterstützung](#LanguageSupport). Diese Option kann nur mit Feldern vom Typ `searchable` verwendet werden. Eine einmal für ein Feld gewählte Analysemethode kann nicht mehr geändert werden.


<a name="LanguageSupport"></a> **Sprachunterstützung**

Durchsuchbare Felder werden einer Analyse unterzogen, die häufig Wörtertrennungen, Textnormalisierungen und das Herausfiltern von Begriffen beinhalten. Standardmäßig werden durchsuchbare Felder in Azure Search mit dem [Standardanalyseprogramm von Apache Lucene](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html) analysiert. Dieses unterteilt Text gemäß den Regeln der [Unicode-Textsegmentierung](http://unicode.org/reports/tr29/) in einzelne Elemente. Darüber hinaus konvertiert das Standardanalyseprogramm alle Zeichen in Kleinbuchstaben. Während der Indizierung und der Abfrageverarbeitung durchlaufen sowohl indizierte Dokumente als auch Suchbegriffe die Analyse.

Azure Search ermöglicht die Indizierung von Feldern in einer Vielzahl von Sprachen. Für jede dieser Sprachen ist ein nicht standardmäßiges Textanalysemodul erforderlich, das den Charakteristika einer bestimmten Sprache Rechnung trägt. Beispielsweise verwendet das Analysemodul für Französisch eine [einfache französische Wortstammerkennung](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/fr/FrenchLightStemmer.html), um Wörter auf ihre [Wortstämme](http://en.wikipedia.org/wiki/Stemming) zurückführen zu können Außerdem werden [Auslassungen](http://en.wikipedia.org/wiki/Elision) und französische Stoppwörter aus dem analysierten Text entfernt. Das Analysemodul für Englisch erweitert das Standardanalysemodul. Es entfernt Possessivformen (nachgestelltes 's) von Wörtern, wendet die Rückführung auf den Stamm nach dem [Porter Stemming-Algorithmus](http://tartarus.org/~martin/PorterStemmer/) an und entfernt englische [Stoppwörter](http://en.wikipedia.org/wiki/Stop_words).

Das Analysemodul kann für jedes Feld in der Indexdefinition durch Festlegen der `analyzer`-Eigenschaft unabhängig konfiguriert werden. Beispiel: Sie können im selben Index separate Felder für englische, französische und spanische Hotelbeschreibungen verwenden. Die Abfrage gibt an, welches sprachspezifische Feld in Ihren Suchabfragen zurückgegeben werden soll.

Nachfolgend sind die unterstützten Analyseprogramme mit einer kurzen Beschreibung ihrer Funktionen aufgelistet:

<table style="font-size:12">
    <tr>
		<th>Sprache</th>
		<th>Name des Analyseprogramms</th>
		<th>Beschreibung</th>
	</tr>
    <tr>
		<td>Arabisch</td>
		<td>ar.lucene</td>
		<td>
		<ul>
			<li>Normalisiert die arabische Orthografie</li>
			<li>Wendet eine einfache algorithmische Wortstammerkennung an</li>
			<li>Filtert arabische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Brasilianisch</td>
		<td>pt-Br.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert brasilianische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Chinesisch (vereinfacht)</td>
		<td>zh-Hans.lucene</td>
		<td>
		<ul>
			<li>Ermittelt die optimale Wortsegmentierung anhand von probabilistischen Wissensmodellen</li>
			<li>Filtert chinesische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Chinesisch (traditionell)</td>
		<td>zh-Hant.lucene</td>
		<td>
		<ul>
			<li>Indiziert Bigramme (überlappenden Gruppen von zwei angrenzenden chinesischen Zeichen)</li>
			<li>Normalisiert unterschiedliche Zeichenbreiten</li>
		</ul>
		</td>
	<tr>
    <tr>
		<td>Tschechisch</td>
		<td>cs.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert tschechische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Dänisch</td>
		<td>da.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert dänische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Niederländisch</td>
		<td>nl.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert niederländische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Deutsch</td>
		<td>de.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert deutsche Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Griechisch</td>
		<td>el.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert griechische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Deutsch</td>
		<td>en.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert englische Stoppwörter heraus</li>
			<li>Entfernt Possessivformen</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Finnisch</td>
		<td>fi.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert finnische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Französisch</td>
		<td>fr.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert französische Stoppwörter heraus</li>
			<li>Entfernt Auslassungen (Elisionen)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Hindi</td>
		<td>hi.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert indische Stoppwörter heraus</li>
			<li>Entfernt diverse unterschiedliche Schreibweisen</li>
			<li>Normalisiert die Unicode-Darstellung von Text in indischen Sprachen</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Ungarisch</td>
		<td>hu.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert ungarische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Indonesisch (Bahasa)</td>
		<td>id.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert indonesische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Italienisch</td>
		<td>it.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert italienische Stoppwörter heraus</li>
			<li>Entfernt Auslassungen (Elisionen)</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Japanisch</td>
		<td>ja.lucene</td>
		<td>
		<ul>
			<li>Verwendet eine morphologische Analyse</li>
			<li>Normalisiert allgemeine Katakana-Schreibweisen</li>
			<li>Entfernt einfache Stoppwörter/Stopptags</li>
			<li>Normalisiert die Zeichenbreite</li>
			<li>Lemmatisierung - reduziert gebeugte Adjektive und Verben auf ihre Grundform</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Koreanisch</td>
		<td>ko.lucene</td>
		<td>
		<ul>
			<li>Indiziert Bigramme (überlappenden Gruppen von zwei angrenzenden chinesischen Zeichen)</li>
			<li>Normalisiert unterschiedliche Zeichenbreiten</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Lettisch</td>
		<td>lv.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert lettische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>

    <tr>
		<td>Norwegisch</td>
		<td>no.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert norwegische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Polnisch</td>
		<td>pl.lucene</td>
		<td>
		<ul>
			<li>Wendet eine algorithmische Wortstammerkennung an (Stempel)</li>
			<li>Filtert polnische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Portugiesisch</td>
		<td>pt-Pt.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert portugiesische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>

    <tr>
		<td>Rumänisch</td>
		<td>ro.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert rumänische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Russisch</td>
		<td>ru.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert russische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Spanisch</td>
		<td>es.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert spanische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Schwedisch</td>
		<td>sv.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert schwedische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Türkisch</td>
		<td>tr.lucene</td>
		<td>
		<ul>
			<li>Entfernt alle Zeichen nach dem Apostroph (einschließlich des Apostrophs)</li>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert türkische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Thailändisch</td>
		<td>th.lucene</td>
		<td>
		<ul>
			<li>Wendet eine einfache Wortstammerkennung an</li>
			<li>Filtert thailändische Stoppwörter heraus</li>
		</ul>
		</td>
	</tr>
</table>

Alle Analyseprogramme mit <i>lucene</i> im Namen werden von den [Sprachanalyseprogrammen von Apache Lucene](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html) unterstützt.

**CORS-Optionen**

Clientseitiger JavaScript-Code kann standardmäßig keine APIs aufrufen, da der Browser jegliche ursprungsübergreifenden Anforderungen verhindert. Aktivieren Sie CORS (Cross-Origin Resource Sharing), indem Sie mit dem Attribut `corsOptions` festlegen, dass ursprungsübergreifende Abfragen für den Index zulässig sind. Beachten Sie, dass CORS aus Sicherheitsgründen nur von Abfrage-APIs unterstützt wird. Die folgenden Optionen können für CORS festgelegt werden:

- `allowedOrigins` (erforderlich): Dies ist eine Liste der Ursprünge, denen Zugriff auf Ihren Index gewährt wird. Dies bedeutet, dass jeglicher von diesen Ursprüngen bereitgestellte Javascript-Code Indexabfragen durchführen kann (sofern er den richtigen API-Schlüssel angibt). Ursprünge werden in der Regel im Format `protocol://fully-qualified-domain-name:port` angegeben, wobei der Port häufig weggelassen wird. Weitere Informationen finden Sie [in diesem Artikel](http://go.microsoft.com/fwlink/?LinkId=330822).
 - Wenn Sie den Zugriff auf alle Ursprünge zulassen möchten, beziehen Sie `*` als ein einzelnes Element in das Array `allowedOrigins` ein. **Bei Suchdiensten in der Produktion wird von dieser Methode abgeraten**. Für die Entwicklung und das Debuggen kann sie jedoch nützlich sein.
- `maxAgeInSeconds` (optional): Von Browsern wird dieser Wert verwendet, um die Dauer (in Sekunden) des Zwischenspeicherns von CORS-Preflight-Antworten zu ermitteln. Dies muss eine positive ganze Zahl sein. Mit dem Wert steigt auch die Leistung, aber es dauert auch länger, bis CORS-Richtlinienänderungen in Kraft treten. Wenn diese Einstellung nicht festgelegt ist, gilt die Standarddauer von 5 Minuten.

<a name="CreateUpdateIndexExample"></a> **Beispiel für Anforderungstext**

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

Bei erfolgreicher Anforderung: "201 – Erstellt".

Standardmäßig enthält der Antworttext die JSON für die erstellte Indexdefinition. Wenn der Anforderungsheader `Prefer` auf `return=minimal` gesetzt wird, bleibt der Antworttext leer und als Erfolgsstatuscode wird anstelle von "201 – Erstellt" der Code "204 Kein Inhalt" ausgegeben. Dies gilt unabhängig davon, ob der Index mit PUT oder POST erstellt wurde.

<a name="UpdateIndex"></a>
## Index aktualisieren

Sie können einen vorhandenen Index in Azure Search mit einer HTTP PUT-Anforderung aktualisieren. In der öffentlichen Vorschauversion können Aktualisierungen im Hinzufügen neuer Felder zum vorhandenen Schema, im Ändern der CORS-Optionen und im Verändern von Bewertungsprofilen bestehen (siehe [Hinzufügen von Bewertungsprofilen zu einem Suchindex](http://msdn.microsoft.com/library/azure/dn798928.aspx)). Geben Sie den Namen des Index an, der mit dem Anforderungs-URI aktualisiert werden soll:

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Wichtig:** Die öffentliche Vorschauversion von Azure Search bietet nur eine eingeschränkte Unterstützung für Aktualisierungen des Indexschemas. Nicht unterstützt werden Schema-Aktualisierungen, die eine erneute Indizierung nach sich ziehen (z. B. Änderungen der Feldtypen). Während sich bestehende Felder nicht ändern oder löschen lassen, können Sie jederzeit neue Felder hinzufügen.

Wenn Sie einem Index ein neues Feld hinzufügen, wird allen im Index enthaltenen Dokumenten für dieses Feld automatisch ein Nullwert zugewiesen. Zusätzlicher Speicherplatz wird erst belegt, wenn Sie dem Index neue Dokumente hinzufügen.

**Anforderung**

HTTPS ist für alle Dienstanforderungen erforderlich. Die Anforderung **Index aktualisieren** wird mit HTTP PUT erstellt. Bei Verwendung von PUT ist der Indexname Teil der URL. Wenn der Index nicht vorhanden ist, wird er erstellt. Ist der Index bereits vorhanden, wird er auf die neue Definition aktualisiert.

Der Indexname muss in Kleinbuchstaben angegeben werden und mit einem Buchstaben oder einer Zahl beginnen. Er darf keine Schrägstriche oder Punkte enthalten und muss weniger als 128 Zeichen lang sein. Der Rest des Indexnamens (nach dem Buchstaben bzw. der Zahl zu Beginn des Namens) kann beliebige Buchstaben, Zahlen und Bindestriche enthalten, solange die Bindestriche nicht aufeinanderfolgen.

Der Parameter `api-version` ist erforderlich. Gültige Werte sind `2014-07-31-Preview` oder `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen. Im Allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Ausführliche Informationen finden Sie unter [Versionsverwaltung für den Suchdienst](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Anforderungsheader**

In der folgenden Liste werden die erforderlichen und optionalen Anforderungsheader beschrieben.

- `Content-Type`: Erforderlich. Auf `application/json` festlegen.
- `api-key`: Erforderlich. `api-key` wird zum Authentifizieren der Anforderung beim Search-Dienst verwendet. Es handelt sich um einen für Ihren Dienst eindeutigen Zeichenfolgewert. Die Anforderung **Index aktualisieren** muss einen `api-key`-Header enthalten, der auf Ihren Administratorschlüssel (keinen Abfrageschlüssel) festgelegt ist.

Sie benötigen außerdem den Dienstnamen, um die URL der Anforderung zu erstellen. Sie finden den Dienstnamen und den `api-key` im Dashboard zu Ihrem Dienst im Azure-Vorschauportal. Hilfe bei der Seitennavigation finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service.portal.md).

**Syntax des Anforderungstexts**

Wenn Sie einen vorhandenen Index aktualisieren, muss der Textkörper die ursprüngliche Schemadefinition und die neu hinzuzufügenden Felder sowie ggf. die geänderten Bewertungsprofile und CORS-Optionen enthalten. Wenn Sie die Bewertungsprofile und CORS-Optionen nicht ändern, geben Sie die bei der Indexerstellung verwendeten Originale an. Optimalerweise sollte bei Aktualisierungen die Indexdefinition zunächst mit GET abgerufen, geändert und anschließend mit PUT aktualisiert werden.

Die für die Indexerstellung verwendete Schemasyntax ist der Einfachheit halber hier dargestellt. Weitere Details finden Sie unter [Index erstellen](#CreateIndex).

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

Hinweis: Der Datentyp `Edm.Int64` wird ab API-Version 2014-10-20-Preview unterstützt.

**Antwort**

Bei erfolgreicher Anforderung: "204 Kein Inhalt".

Standardmäßig ist der Antworttext leer. Wenn der Anforderungsheader `Prefer` jedoch auf `return=representation` gesetzt ist, enthält der Antworttext die JSON für die aktualisierte Indexdefinition. In diesem Fall wird der Erfolgsstatuscode "200 – OK" ausgegeben.

<a name="ListIndexes"></a>
## Indizes auflisten

Mit dem Vorgang **Indizes auflisten** wird eine Liste der aktuell in Ihrem Azure Search-Dienst enthaltenen Indizes zurückgegeben.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Anforderung**

HTTPS ist für alle Dienstanforderungen erforderlich. Die Anforderung **Index auflisten** kann mit der GET-Methode erstellt werden.

Der Parameter `api-version` ist erforderlich. Gültige Werte sind `2014-07-31-Preview` oder `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen. Im Allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Ausführliche Informationen finden Sie unter [Versionsverwaltung für den Suchdienst](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Anforderungsheader**

In der folgenden Liste werden die erforderlichen und optionalen Anforderungsheader beschrieben.

- `api-key`: Erforderlich. `api-key` wird zum Authentifizieren der Anforderung beim Search-Dienst verwendet. Es handelt sich um einen für Ihren Dienst eindeutigen Zeichenfolgewert. Die Anforderung **Index auflisten** muss einen `api-key` enthalten, der auf einen Administratorschlüssel (keinen Abfrageschlüssel) festgelegt ist.

Sie benötigen außerdem den Dienstnamen, um die URL der Anforderung zu erstellen. Sie finden den Dienstnamen und den `api-key` im Dashboard zu Ihrem Dienst im Azure-Vorschauportal. Hilfe bei der Seitennavigation finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service.portal.md).

**Anforderungstext**

Keine.

**Antwort**

Bei erfolgreicher Antwort wird der Statuscode "200 OK" zurückgegeben.

Hier ist ein Beispiel-Antworttext:

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

Beachten Sie, dass Sie die Antwort filtern können, um nur die Eigenschaften, die Sie interessieren, zu erhalten. Wenn Sie beispielsweise nur eine Liste der Indexnamen erhalten möchten, verwenden Sie die OData-Abfrageoption `$select`:

    GET /indexes?api-version=2014-07-31-Preview&$select=name

In diesem Fall würde die Antwort aus dem obigen Beispiel wie folgt aussehen:

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Dies ist ein nützliches Verfahren, um Bandbreite zu sparen, wenn Ihr Suchdienst zahlreiche Indizes beinhaltet.

<a name="GetIndex"></a>
## Index abrufen

Mit dem Vorgang **Index abrufen** wird die Indexdefinition in Azure Search abgerufen.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Anforderung**

Für Dienstanforderungen ist HTTPS erforderlich. Die Anforderung **Index abrufen** kann mit der GET-Methode erstellt werden.

Der [Indexname] im Anforderungs-URI gibt an, welcher Index aus der Indexsammlung zurückgegeben werden soll.

Der Parameter `api-version` ist erforderlich. Gültige Werte sind `2014-07-31-Preview` oder `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen. Im Allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Ausführliche Informationen finden Sie unter [Versionsverwaltung für den Suchdienst](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Anforderungsheader**

In der folgenden Liste werden die erforderlichen und optionalen Anforderungsheader beschrieben.

- `api-key`: Mit `api-key` wird die Anforderung bei Ihrem Suchdienst authentifiziert. Es handelt sich um einen für Ihren Dienst eindeutigen Zeichenfolgewert. Die Anforderung **Index abrufen** muss einen `api-key` enthalten, der auf einen Administratorschlüssel (keinen Abfrageschlüssel) festgelegt ist.

Sie benötigen außerdem den Dienstnamen, um die URL der Anforderung zu erstellen. Sie finden den Dienstnamen und den `api-key` im Dashboard zu Ihrem Dienst im Azure-Vorschauportal. Hilfe bei der Seitennavigation finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service.portal.md).

**Anforderungstext**

Keine.

**Antwort**

Bei erfolgreicher Antwort wird der Statuscode "200 OK" zurückgegeben.

Unter [Erstellen und Aktualisieren eines Index](#CreateUpdateIndexExample) wird JSON als Beispiel für die Antwortnutzlast angegeben.

<a name="DeleteIndex"></a>
## Index löschen

Mit dem Vorgang **Index löschen** wird ein Index samt den damit verknüpften Dokumenten aus Ihrem Azure Search-Dienst entfernt. Sie können den Indexnamen über das Dashboard für Ihren Dienst im Azure-Vorschauportal oder über die API abrufen. Details finden Sie unter [Indizes auflisten](#ListIndexes).

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Anforderung**

Für Dienstanforderungen ist HTTPS erforderlich. Die Anforderung **Index löschen** kann mit der DELETE-Methode erstellt werden.

Der [Indexname] im Anforderungs-URI gibt an, welcher Index aus der Indexsammlung gelöscht werden soll.

Der Parameter `api-version` ist erforderlich. Gültige Werte sind `2014-07-31-Preview` oder `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen. Im Allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Ausführliche Informationen finden Sie unter [Versionsverwaltung für den Suchdienst](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Anforderungsheader**

In der folgenden Liste werden die erforderlichen und optionalen Anforderungsheader beschrieben.

- `api-key`: Erforderlich. `api-key` wird zum Authentifizieren der Anforderung beim Search-Dienst verwendet. Es handelt sich um einen für Ihre Dienst-URL eindeutigen Zeichenfolgewert. Die Anforderung **Index löschen** muss einen `api-key`-Header enthalten, der auf Ihren Administratorschlüssel (keinen Abfrageschlüssel) festgelegt ist.

Sie benötigen außerdem den Dienstnamen, um die URL der Anforderung zu erstellen. Sie finden den Dienstnamen und den `api-key` im Dashboard zu Ihrem Dienst im Azure-Vorschauportal. Hilfe bei der Seitennavigation finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service.portal.md).

**Anforderungstext**

Keine.

**Antwort**

Bei erfolgreicher Antwort wird der Statuscode "204 Kein Inhalt" zurückgegeben.

<a name="GetIndexStats"></a>
## Abrufen der Index-Statistiken

Mit dem Vorgang **Indexstatistik abrufen** werden von Azure Search Informationen über die Dokumentanzahl für den aktuellen Index sowie die Speicherauslastung zurückgegeben.

	GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

**Anforderung**

HTTPS ist für alle Dienstanforderungen erforderlich. Die Anforderung **Indexstatistiken abrufen** kann mit der GET-Methode erstellt werden.

Der [Indexname] im Anforderungs-URI weist den Dienst an, Indexstatistiken für den festgelegten Index zurückzugeben.

Der Parameter `api-version` ist erforderlich. Gültige Werte sind `2014-07-31-Preview` oder `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen. Im Allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Ausführliche Informationen finden Sie unter [Versionsverwaltung für den Suchdienst](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Anforderungsheader**

In der folgenden Liste werden die erforderlichen und optionalen Anforderungsheader beschrieben.

- `api-key`: Mit `api-key` wird die Anforderung bei Ihrem Suchdienst authentifiziert. Es handelt sich um einen für Ihren Dienst eindeutigen Zeichenfolgewert. Die Anforderung **Indexstatistik abrufen** muss einen `api-key` enthalten, der auf einen Administratorschlüssel (keinen Abfrageschlüssel) festgelegt ist.

Sie benötigen außerdem den Dienstnamen, um die URL der Anforderung zu erstellen. Sie finden den Dienstnamen und den `api-key` im Dashboard zu Ihrem Dienst im Azure-Vorschauportal. Hilfe bei der Seitennavigation finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service.portal.md).

**Anforderungstext**

Keine.

**Antwort**

Bei erfolgreicher Antwort wird der Statuscode "200 OK" zurückgegeben.

Der Antworttext hat folgendes Format:

    {
      "documentCount": number,
	  "storageSize": number (size of the index in bytes)
    }

________________________________________
<a name="DocOps"></a>
## Dokumentvorgänge

In Azure Search wird ein Index in der Cloud gespeichert und mit JSON-Dokumenten gefüllt, die Sie in den Dienst hochladen. Die gesamten hochgeladenen Dokumente stellen den Korpus Ihrer Suchdaten dar. Dokumente enthalten Felder, von denen einige beim Hochladen als Suchbegriffe mit Token versehen werden. Das URL-Segment `/docs` in der Azure Search-API stellt die Sammlung von Dokumenten in einem Index dar. Alle an der Sammlung durchgeführten Vorgänge wie das Hochladen, Zusammenführen, Löschen oder Abfragen von Dokumenten findet im Kontext eines einzigen Index statt. Die URLs für diese Vorgänge beginnen daher für einen gegebenen Indexnamen immer mit `/indexes/[index name]/docs`.

Der Anwendungscode muss JSON-Dokumente zum Hochladen in Azure Search generieren. In der Regel werden Indizes aus einem einzelnen Dataset aufgefüllt, das Sie bereitstellen.

Es empfiehlt sich, für jedes zu durchsuchende Element ein Dokument zu haben. Eine Filmverleihanwendung könnte ein Dokument pro Film haben, eine Ladenanwendung ein Dokument pro Artikel, eine Anwendung für Online-Kurse ein Dokument pro Kurs, eine Forschungsfirma ein Dokument pro akademisches Papier in ihrem Repository usw.

Dokumente bestehen aus einem oder mehreren Feldern. Felder können Text enthalten, der von Azure Search mit Token als Suchbegriffe gekennzeichnet wurde, ebenso wie Werte ohne Token oder Nicht-Text-Werte, die in Filtern oder Bewertungsprofilen verwendet werden können. Die Namen, Datentypen und Suchfunktionen für jedes Feld werden durch das Indexschema bestimmt. Eines der Felder in jedem Indexschema muss als ID ausgewiesen sein. Zudem muss jedes Dokument einen Wert für das ID-Feld haben, der das Dokument im Index eindeutig identifiziert. Alle anderen Felder des Dokuments sind optional und erhalten standardmäßig einen Nullwert, wenn nichts angegeben wird. Nullwerte belegen im Suchindex keinen Speicherplatz.

Bevor Sie Dokumente hochladen können, müssen Sie bereits den Index für den Dienst erstellt haben. Details zu diesem ersten Schritt finden Sie unter [Index erstellen](#CreateIndex).

**Hinweis**: In dieser Version der API steht die Volltextsuche nur in englischer Sprache zur Verfügung.

<a name="AddOrUpdateDocuments"></a>
## Hinzufügen, Aktualisieren oder Löschen von Dokumenten

Sie können Dokumente in einem gegebenen Index mit HTTP POST hochladen, zusammenführen, zusammenführen oder hochladen oder löschen. Bei einer großen Anzahl von Updates empfiehlt sich die Batchverarbeitung von Dokumenten (bis zu 1000 Dokumente oder ca. 16 MB pro Batch).

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Anforderung**

HTTPS ist für alle Dienstanforderungen erforderlich. Sie können Dokumente in einem gegebenen Index mit HTTP POST hochladen, zusammenführen, zusammenführen oder hochladen oder löschen.

Der Anforderungs-URI enthält den [Indexnamen]. Dieser gibt an, in welchem Index Dokumente bereitgestellt werden sollen. Sie können Dokumente jeweils nur in einem Index bereitstellen.

Der Parameter `api-version` ist erforderlich. Gültige Werte sind `2014-07-31-Preview` oder `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen. Im Allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Ausführliche Informationen finden Sie unter [Versionsverwaltung für den Suchdienst](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Anforderungsheader**

In der folgenden Liste werden die erforderlichen und optionalen Anforderungsheader beschrieben.

- `Content-Type`: Erforderlich. Auf `application/json` festlegen.
- `api-key`: Erforderlich. `api-key` wird zum Authentifizieren der Anforderung beim Search-Dienst verwendet. Es handelt sich um einen für Ihren Dienst eindeutigen Zeichenfolgewert. Die Anforderung **Index hinzufügen** muss einen `api-key`-Header enthalten, der auf Ihren Administratorschlüssel (keinen Abfrageschlüssel) festgelegt ist.

Sie benötigen außerdem den Dienstnamen, um die URL der Anforderung zu erstellen. Sie finden den Dienstnamen und den `api-key` im Dashboard zu Ihrem Dienst im Azure-Vorschauportal. Hilfe bei der Seitennavigation finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service.portal.md).

**Anforderungstext**

Der Anforderungstext enthält ein oder mehrere zu indizierende Dokumente. Dokumente werden durch einen eindeutigen Schlüssel identifiziert. Jedes Dokument ist mit einer Aktion verknüpft: Hochladen, Zusammenführen, Zusammenführen-oder-Hochladen oder Löschen. Anforderungen zum Hochladen müssen die Dokumentdaten als einen Satz mit Schlüssel-Wert-Paare enthalten.

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

- `upload`: Das Hochladen entspricht einer "upsert"-Aktion, bei der neue Dokumente eingefügt und bestehende Dokumente aktualisiert/ersetzt werden. Beachten Sie, dass bei einer Aktualisierung alle Felder ersetzt werden.
- `merge`: Beim Zusammenführen wird ein bestehendes Dokument mit den angegebenen Feldern aktualisiert. Wenn das Dokument nicht vorhanden ist, schlägt die Zusammenführung fehl. Jedes Feld, das Sie in einer Zusammenführung angeben, ersetzt das vorhandene Feld im Dokument. Dies beinhaltet auch Felder vom Typ `Collection(Edm.String)`. Beispiel: Wenn das Dokument ein Feld namens "tags" mit dem Wert `["budget"]` enthält, und Sie eine Zusammenführung mit dem Wert `["economy", "pool"]` für "tags" durchführen, hat das Feld "tags" am Ende den Wert `["economy", "pool"]`. Der Wert lautet **nicht** `["budget", "economy", "pool"]`.
- `mergeOrUpload`: Verhält sich wie beim `merge`-Vorgang, wenn im Index bereits ein Dokument mit dem gegebenen Schlüssel vorhanden ist. Wenn das Dokument noch nicht vorhanden ist, verhält es sich wie beim `upload`-Vorgang eines neuen Dokuments.
- `delete`: Hiermit wird das angegebenen Dokument aus dem Index gelöscht. Sie können für den `delete`-Vorgang nur den Wert für das Schlüsselfeld angeben. Wenn Sie versuchen, andere Felder anzugeben, wird der Fehler "HTTP 400" ausgegeben. Wenn Sie ein einzelnes Feld aus einem Dokument entfernen möchten, verwenden Sie stattdessen `merge` und setzen das Feld explizit auf `null`.

**Antwort**

Für eine erfolgreiche Antwort wird als Statuscode "200 OK" zurückgeben. Dies bedeutet, dass alle Elemente erfolgreich indiziert wurden (dies ist auch am Feld "status" zu erkennen, das für alle Elemente auf "true" gesetzt ist):

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": true,
          "errorMessage": null
        }
      ]
    }  

Wenn mindestens ein Element erfolgreich indiziert wurde, wird der Statuscode "207" zurückgegeben (dies ist auch am Feld "status" zu erkennen, das für alle nicht indizierten Elemente auf "false" gesetzt ist):

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later."
        }
      ]
    }  

Die Eigenschaft `errorMessage` gibt nach Möglichkeit den Grund für den Indizierfehler an.

**Hinweis**: Wenn der Clientcode als Antwort häufig "207" erhält, kann es daran liegen, dass das System überlastet ist. Sie können dies anhand der Eigenschaft `errorMessage` überprüfen. Wenn dies der Fall ist, sollten ***Indexanforderungen eingeschränkt*** werden. Nimmt der Indizierdatenverkehr nicht ab, kann dies dazu führen, dass alle Anforderungen mit dem Fehler "503" abgelehnt werden.

Der Statuscode "429" gibt an, dass Sie Ihr Kontingent hinsichtlich der Anzahl der Dokumente pro Index überschritten haben. Erstellen Sie in diesem Fall entweder einen neuen Index oder aktualisieren Sie auf höhere Kapazitätsgrenzen.

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
## Dokumente durchsuchen

Ein **Suchvorgang** wird als GET-Anforderung ausgegeben. Er gibt Abfrageparameter an, die als Kriterien für die Auswahl übereinstimmender Dokumente dienen.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin key]

**Anforderung**

Für Dienstanforderungen ist HTTPS erforderlich. Die **Suchanforderung** kann mit der GET-Methode erstellt werden.

Der Anforderungs-URI gibt für alle mit den Abfrageparametern übereinstimmenden Dokumente an, welcher Index abgefragt werden soll.

**Abfrageparameter**

`search=[string]` (optional): Der zu suchenden Text. Alle Felder mit dem Attribut `searchable` werden standardmäßig durchsucht, es sei denn, es wurde `searchFields` angegeben. Beim Durchsuchen von Feldern mit dem Attribut `searchable` wird der zu durchsuchende Text mit Token versehen. Auf diese Weise können mehrere Begriffe durch Leerzeichen getrennt werden (Beispiel: `search=hello world`). Verwenden Sie für die Übereinstimmung mit einem beliebigen Begriff `*` (dies kann bei booleschen Filterabfragen nützlich sein). Das Auslassen dieses Parameters hat dieselbe Wirkung wie das Festlegen auf `*`. Einzelheiten zur Suchsyntax finden Sie unter [Einfache Abfragesyntax](https://msdn.microsoft.com/library/dn798920.aspx).

  - **Hinweis**: Bei Abfragen über Felder mit dem Attribut `searchable` können die Ergebnisse mitunter überraschend sein. Der Tokenizer enthält die Logik zum Behandeln von gängigen Fällen bei englischem Text, wie Apostrophe, Kommas in Zahlen usw. Beispiel: `search=123,456` entspricht dem Begriff "123,456" und nicht zwei separaten Begriffen "123" und "456", da im Englischen bei großen Zahlen Kommas als Tausendertrennzeichen verwendet werden. Aus diesem Grund empfiehlt es sich, Begriffe im Parameter `search` anstatt mit Satzzeichen mit Leerzeichen zu trennen.

`searchMode=any|all` (optional, Standardwert ist `any`): Gibt an, ob Suchbegriffe teilweise oder vollständig übereinstimmen müssen, damit das Dokument in den Ergebnissen zurückgegeben wird.

`searchFields=[string]` (optional): Die Liste der kommagetrennten Feldnamen, die für den angegebenen Text durchsucht werden. Zielfelder müssen als `searchable` gekennzeichnet sein.

`$skip=#` (optional): Die Anzahl der zu überspringenden Suchergebnisse. Darf nicht größer als 100.000 sein. Wenn Sie Dokumente der Reihe nach scannen müssen, `$skip` aber aufgrund dieser Einschränkung nicht zulässig ist, können Sie alternativ für einen Schlüssel für die Gesamtreihenfolge `$orderby` und für eine Bereichsabfrage `$filter` verwenden.

`$top=#` (optional): Die Anzahl der abzurufenden Suchergebnisse. Der Standardwert ist 50. Wenn Sie einen Wert über 1000 angeben und mehr als 1000 Ergebnisse vorliegen, werden nur die ersten 1000 Ergebnisse zusammen mit einem Link zur nächsten Ergebnisseite zurückgegeben (siehe `@odata.nextLink` im [nachfolgenden Beispiel](#SearchResponse)).

`$count=true|false` (optional, Standardwert ist `false`): Gibt an, ob alle Ergebnisse abgerufen werden sollen. Wenn Sie diesen Wert auf `true` setzen, kann sich dies auf die Leistung auswirken. Beachten Sie, dass die zurückgegebene Anzahl ein Näherungswert ist.

`$orderby=[string]` (optional): Eine Liste mit kommagetrennten Ausdrücken, nach denen die Ergebnisse sortiert werden. Jeder Ausdruck kann ein Feldname oder ein Aufruf der Funktion `geo.distance()` sein. Jedem Ausdruck kann für eine aufsteigende Reihenfolge `asc` und für eine absteigende Reihenfolge `desc` nachgestellt sein. Standardmäßig wird in aufsteigender Reihenfolge sortiert. Verknüpfungen werden durch die Ergebnisstände von Dokumenten getrennt. Wenn `$orderby` nicht angegeben ist, werden Dokumente absteigend nach Ergebnisstand sortiert. `$orderby` ist auf 32 Klauseln beschränkt.

`$select=[string]` (optional): Eine Liste mit kommagetrennten Feldern, die abgerufen werden sollen. Wenn nicht anders angegeben, werden alle im Schema als abrufbar gekennzeichnete Felder einbezogen. Sie können auch alle Felder explizit anfordern, indem Sie diesen Parameter auf `*` setzen.

`facet=[string]` (null oder höher): Ein Feld für die Facettensuche. Optional kann die Zeichenfolge Parameter zum Anpassen der Facettensuche enthalten, die als kommagetrennte `name:value`-Paare angegeben sind. Gültige Parameter sind:

- `count` (max. Anzahl von Facettenbegriffen; Standardwert ist 10). Es gibt keine Obergrenze, höhere Werte führen jedoch zu einem entsprechenden Leistungsabzug, insbesondere wenn das Facettenfeld eine große Anzahl eindeutiger Begriffe enthält.
  - Beispiel: `facet=category,count:5` ruft die ersten fünf Kategorien der Facettenergebnisse ab.  
  - **Hinweis**: Wenn der Parameter `count` unter der Anzahl der eindeutigen Begriffe liegt, sind die Ergebnisse möglicherweise nicht exakt. Dies liegt an der Art, wie Facettenabfragen über Shards hinweg verteilt werden. Durch ein Erhöhen von `count` erhöht sich im Allgemeinen auch die Genauigkeit der Begriffsanzahl, jedoch zulasten der Leistung.
- `sort` (`count` zum *absteigenden* Sortieren nach Anzahl, `-count` zum *aufsteigenden* Sortieren nach Anzahl, `value` zum *aufsteigenden* Sortieren nach Wert oder `-value` zum *absteigenden* Sortieren nach Wert)
  - Beispiel: `facet=category,count:3,sort:count` ruft die ersten drei Kategorien in Facettenergebnisse in absteigender Reihenfolge nach der Anzahl der Dokumente mit jedem Ortsname ab. Wenn beispielsweise die ersten drei Kategorien "Budget", "Motel" und "Luxus" sind und für Budget 5 Treffer, für Motel 6 Treffer und für Luxus 4 Treffer vorhanden sind, werden die Bereiche (Buckets) in der Reihenfolge Motel, Budget, Luxus sortiert.
  - Beispiel: `facet=rating,sort:-value` erstellt Buckets für alle möglichen Bewertungen in absteigender Reihenfolge nach Wert. Wenn die Bewertungen von 1 bis 5 reichen, werden die Buckets ungeachtet der mit jeder Bewertung übereinstimmenden Dokumente in der Reihenfolge 5, 4, 3, 2, 1 sortiert.
- `values` (durch einen senkrechten Strich (Pipe) getrennte numerische oder `Edm.DateTimeOffset`-Werte, die einen dynamischen Satz von Facettenwerten angeben)
  - Beispiel: `facet=baseRate,values:10|20` erstellt drei Buckets: einen für den Basistarif 0 bis unter 10, einen für 10 bis unter 20 und einen für 20 und höher.
  - Beispiel: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` erstellt zwei Buckets: einen für Hotels, die vor Februar 2010 renoviert wurden, und einen für Hotels die ab dem 1. Februar 2010 renoviert wurden.
- `interval` (ganzzahliges Intervall größer als 0 für Zahlen oder `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` für Datum-Uhrzeit-Werte)
  - Beispiel: `facet=baseRate,interval:100` erstellt Buckets basierend auf Basistarifbereichen von 100. Wenn die Basistarife beispielsweise alle zwischen 60 $ und 600 $ liegen, werden Buckets für 0 – 100, 100 – 200, 200 – 300, 300 – 400, 400 – 500 und 500 – 600 erstellt.
  - Beispiel: `facet=lastRenovationDate,interval:year` erstellt ein Bucket für jedes Jahr, in dem Hotels renoviert wurden.
- **Hinweis**: `count` und `sort` können in derselben Facettenspezifikation kombiniert werden. Eine Kombination mit `interval` oder `values` ist jedoch nicht möglich. Ebenso wenig können `interval` und `values` kombiniert werden.

`$filter=[string]` (optional): Ein strukturierter Suchbegriff in standardmäßiger OData-Syntax. Details zur Teilmenge der von Azure Search unterstützten Grammatik von OData-Ausdrücken finden Sie unter [OData-Ausdruckssyntax](#ODataExpressionSyntax).

`highlight=[string]` (optional): Ein Satz kommagetrennter Feldnamen für wichtige Treffer. Für wichtige Treffer können nur Felder mit dem Attribut `searchable` verwendet werden.

`scoringProfile=[string]` (optional): Der Name des Bewertungsprofils, mit dem Ergebnisstände übereinstimmender Dokumente zum Sortieren der Ergebnisse bewertet werden.

`scoringParameter=[string]` (Null oder höher): Gibt den Wert für jeden in einer Bewertungsfunktion definierten Parameter (z. B. `referencePointParameter`) im Format "Name:Wert" an. Beispiel: Wenn das Bewertungsprofil eine Funktion mit einem Parameter namens "mylocation" definiert, lautet die Option für die Abfragezeichenfolge "&scoringParameter=mylocation:-122.2,44.8"

`api-version=[string]` (erforderlich). Gültige Werte sind `2014-07-31-Preview` oder `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen. Im Allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Ausführliche Informationen finden Sie unter [Versionsverwaltung für den Suchdienst](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Hinweis: Bei diesem Vorgang wird die `api-version` als Abfrageparameter angegeben.

**Anforderungsheader**

In der folgenden Liste werden die erforderlichen und optionalen Anforderungsheader beschrieben.

- `api-key`: Mit `api-key` wird die Anforderung bei Ihrem Suchdienst authentifiziert. Es handelt sich um einen für Ihre Dienst-URL eindeutigen Zeichenfolgewert. Für die **Suchanforderung** kann ein Administratorschlüssel oder ein Abfrageschlüssel für `api-key` festgelegt werden.

Sie benötigen außerdem den Dienstnamen, um die URL der Anforderung zu erstellen. Sie finden den Dienstnamen und den `api-key` im Dashboard zu Ihrem Dienst im Azure-Vorschauportal. Hilfe bei der Seitennavigation finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service.portal.md).

**Anforderungstext**

Keine.

<a name="SearchResponse"></a> **Antwort**

Bei erfolgreicher Antwort wird der Statuscode "200 OK" zurückgegeben.

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

Weitere Beispiele erhalten Sie auf der Seite [OData-Ausdruckssyntax für Azure Search](https://msdn.microsoft.com/library/azure/dn798921.aspx).

1) Durchsuchen Sie den absteigend sortierten Index nach Datum.

    GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-07-31-Preview

HINWEIS: Die Genauigkeit von DateTime-Feldern ist auf Millisekunden beschränkt. Wenn Sie einen Zeitstempel mit kleineren Werten (sehen Sie sich beispielsweise den Sekundenteil dieses Zeitstempels an: 10:30:09,7552052) übergeben, wird der Rückgabewert aufgerundet (in diesem Fall auf 10:30:09,7550000).

2) Durchsuchen Sie in einer Facettensuche den Index nach Facetten für Kategorien, Bewertungen, Tags sowie Elemente mit "baseRate" in spezifischen Bereichen:

    GET /indexes/hotels/docs?search=test&facet=category&facet=rating&facet=tags&facet=baseRate,values:80|150|220&api-version=2014-07-31-Preview

3) Grenzen Sie die zurückgegebenen Facettenabfrageergebnisse mit einem Filter ein, wenn der Benutzer auf die Bewertung 3 und die Kategorie "Motel" geklickt hat:

    GET /indexes/hotels/docs?search=test&facet=tags&facet=baseRate,values:80|150|220&$filter=rating eq 3 and category eq 'Motel'&api-version=2014-07-31-Preview

4) Legen Sie bei einer Facettensuche eine Obergrenze für die in einer Abfrage zurückgegebenen eindeutigen Begriffe fest. Der Standardwert ist 10, wobei Sie diesen Wert mithilfe des Parameters `count` im Attribut `facet` erhöhen oder verringern können:

    GET /indexes/hotels/docs?search=test&facet=city,count:5&api-version=2014-07-31-Preview

5) Durchsuchen Sie den Index innerhalb spezieller Felder, wie etwa einem sprachspezifischen Feld:

    GET /indexes/hotels/docs?search=hôtel&searchFields=description_fr&api-version=2014-07-31-Preview

6) Durchsuchen Sie den Index innerhalb mehrerer Felder. Beispiel: Sie können durchsuchbare Felder innerhalb desselben Index in mehreren Sprachen speichern und abfragen. Wenn ein Dokument sowohl englische als auch französische Beschreibungen enthält, können Sie die Abfrageergebnisse teilweise oder vollständig zurückgeben:

	GET /indexes/hotels/docs?search=hotel&searchFields=description,description_fr&api-version=2014-07-31-Preview

Beachten Sie, dass jeweils nur ein Index abgefragt werden kann. Erstellen Sie für jede Sprache nur einen Index, es sei denn, Sie fragen bei mehreren Indizes jeden einzeln ab.

7) Seitenverwaltung: Rufen Sie die erste Seite mit Elementen ab (Seitengröße ist 10):

    GET /indexes/hotels/docs?search=*&$skip=0&$top=10&api-version=2014-07-31-Preview

8) Seitenverwaltung: Rufen Sie die zweite Seite mit Elementen ab (Seitengröße ist 10):

    GET /indexes/hotels/docs?search=*&$skip=10&$top=10&api-version=2014-07-31-Preview

9) Rufen Sie einen speziellen Satz von Feldern ab:

    GET /indexes/hotels/docs?search=*&$select=hotelName,description&api-version=2014-07-31-Preview

10) Rufen Sie Dokumente ab, die einem speziellen Abfrageausdruck entsprechen.

    GET /indexes/hotels/docs?$filter=(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'&api-version=2014-07-31-Preview

11) Durchsuchen Sie den Index, um Fragmente mit wichtigen Treffern zurückzugeben.

    GET /indexes/hotels/docs?search=something&highlight=description&api-version=2014-07-31-Preview

12) Durchsuchen Sie den Index, um nach der Entfernung zu einem Referenzstandort sortierte Dokumente zurückzugeben.

    GET /indexes/hotels/docs?search=something&$orderby=geo.distance(location, geography'POINT(-122.12315 47.88121)')&api-version=2014-07-31-Preview

13) Durchsuchen Sie den Index in der Annahme, dass ein Bewertungsprofil namens "geo" mit zwei Bewertungsfunktionen für die Entfernung vorhanden ist. Dabei definiert die eine Funktion einen Parameter namens "currentLocation" und die andere Funktion einen Parameter namens "lastLocation".

    GET /indexes/hotels/docs?search=something&scoringProfile=geo&scoringParameter=currentLocation:-122.123,44.77233&scoringParameter=lastLocation:-121.499,44.2113&api-version=2014-07-31-Preview


<a name="LookupAPI"></a>
##Dokument suchen

Mit dem Vorgang **Dokument suchen** wird ein Dokument aus Azure Search abgerufen. Dies ist nützlich, wenn ein Benutzer auf ein bestimmtes Suchergebnis klickt und Sie spezifische Details zu diesem Dokument prüfen möchten.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin key]

**Anforderung**

Für Dienstanforderungen ist HTTPS erforderlich. Die Anforderung **Dokument suchen** kann wie folgt erstellt werden.

    GET /indexes/[index name]/docs/key?[query parameters]

Alternativ können Sie für die Schlüsselsuche die herkömmliche OData-Syntax verwenden:

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

Der Anforderungs-URI enthält einen [Indexnamen] und einen [Schlüssel]. Diese Angaben legen fest, welches Dokument aus welchem Index abgerufen wird. Sie können jeweils nur ein Dokument abrufen. Mit dem **Suchvorgang** können Sie mehrere Dokumente in einer Anforderung abfragen.

**Abfrageparameter**

`$select=[string]` (optional): Eine Liste mit kommagetrennten Feldern, die abgerufen werden sollen. Wenn nicht anders angegeben oder auf `*` gesetzt, werden alle im Schema als abrufbar gekennzeichnete Felder in die Projektion einbezogen.

`api-version=[string]` (erforderlich). Gültige Werte sind `2014-07-31-Preview` oder `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen. Im Allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Ausführliche Informationen finden Sie unter [Versionsverwaltung für den Suchdienst](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Hinweis: Bei diesem Vorgang wird die `api-version` als Abfrageparameter angegeben.

**Anforderungsheader**

In der folgenden Liste werden die erforderlichen und optionalen Anforderungsheader beschrieben.

- `api-key`: Mit `api-key` wird die Anforderung bei Ihrem Suchdienst authentifiziert. Es handelt sich um einen für Ihre Dienst-URL eindeutigen Zeichenfolgewert. Für die Anforderung **Dokument suchen** kann ein Administratorschlüssel oder ein Abfrageschlüssel für `api-key` festgelegt werden.

Sie benötigen außerdem den Dienstnamen, um die URL der Anforderung zu erstellen. Sie finden den Dienstnamen und den `api-key` im Dashboard zu Ihrem Dienst im Azure-Vorschauportal. Hilfe bei der Seitennavigation finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service.portal.md).

**Anforderungstext**

Keine.

**Antwort**

Bei erfolgreicher Antwort wird der Statuscode "200 OK" zurückgegeben.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Beispiel**

Suchen Sie das Dokument mit dem Schlüssel "2".

    GET /indexes/hotels/docs/2?api-version=2014-07-31-Preview

Suchen Sie das Dokument mit dem Schlüssel "3" mithilfe der OData-Syntax:

    GET /indexes('hotels')/docs('3')?api-version=2014-07-31-Preview

<a name="CountDocs"></a>
##Dokumentenanzahl

Mit dem Vorgang **Dokumentenanzahl** wird die Anzahl der in einem Suchindex enthaltenen Dokumente abgerufen. Die Syntax für `$count` ist Teil des OData-Protokolls.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin key]

**Anforderung**

Für Dienstanforderungen ist HTTPS erforderlich. Die Anforderung **Dokumentanzahl** kann mit der GET-Methode erstellt werden.

Der [Indexname] im Anforderungs-URI weist den Dienst an, die Anzahl aller Elemente in der Dokumentensammlung des angegebenen Index zurückzugeben.

Der Parameter `api-version` ist erforderlich. Gültige Werte sind `2014-07-31-Preview` oder `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen. Im Allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Ausführliche Informationen finden Sie unter [Versionsverwaltung für den Suchdienst](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Anforderungsheader**

In der folgenden Liste werden die erforderlichen und optionalen Anforderungsheader beschrieben.

- `Accept`: Dieser Wert muss auf `text/plain` gesetzt werden.
- `api-key`: Mit `api-key` wird die Anforderung bei Ihrem Suchdienst authentifiziert. Es handelt sich um einen für Ihre Dienst-URL eindeutigen Zeichenfolgewert. Für die Anforderung **Dokumentanzahl** kann ein Administratorschlüssel oder ein Abfrageschlüssel für `api-key` festgelegt werden.

Sie benötigen außerdem den Dienstnamen, um die URL der Anforderung zu erstellen. Sie finden den Dienstnamen und den `api-key` in Ihrem Dienst-Dashboard im Azure-Vorschauportal. Hilfe bei der Seitennavigation finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service.portal.md).

**Anforderungstext**

Keine.

**Antwort**

Bei erfolgreicher Antwort wird der Statuscode "200 OK" zurückgegeben.

Die Anzahl ist im Antworttext als ganze Zahl im Nur-Text-Format angegeben.

<a name="Suggestions"></a>
##Vorschläge

Mit dem Vorgang **Vorschläge** werden Vorschläge basierend auf einer Teilsuche abgerufen. Er wird in der Regel in Suchfeldern verwendet, um während der Eingabe von Suchbegriffen Eingabevorschläge bereitzustellen.

Der vorgeschlagene Text kann wiederholt werden, wenn mehrere Kandidatendokumente mit der gleichen Sucheingabe übereinstimmen Sie können mit `$select` weitere Dokumentfelder (einschließlich des Dokumentschlüssels) abrufen, um festzustellen, aus welchem Dokument die einzelnen Vorschläge stammen.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin key]

**Anforderung**

Für Dienstanforderungen ist HTTPS erforderlich. Die Anforderung **Vorschläge** kann mit der GET-Methode erstellt werden.

Der Anforderungs-URI gibt den Namen des abzufragenden Index an. Zudem bezieht er den teilweise eingegebenen Suchbegriff in die Abfragezeichenfolge ein.

**Abfrageparameter**

`search=[string]`: Der zum Vorschlagen von Abfragen zu verwendende Suchtext. Er muss zwischen 3 und 25 Zeichen lang sein.

`fuzzy=[boolean]` (optional, Standardwert = false): Wenn dieser Wert auf "true" gesetzt ist, findet diese API Vorschläge auch dann, wenn im Suchtext ein Zeichen ersetzt wurde oder fehlt. Dies führt in einigen Szenarien zwar zu besseren Ergebnissen, geht jedoch zulasten der Leistung, da Fuzzysuchen von Vorschlägen langsamer sind und mehr Ressourcen belegen.

`searchFields=[string]` (optional): Die Liste der kommagetrennten Feldnamen, die für den angegebenen Suchtext durchsucht werden. Zielfelder müssen für Vorschläge aktiviert sein.

`$top=#` (optional, Standardwert = 5): Die Anzahl der abzurufenden Vorschläge. Dies muss eine Zahl zwischen 1 und 10 sein.

`$filter=[string]` (optional): Ein Ausdruck, der die für Vorschläge in Betracht kommenden Dokumente filtert.

`$orderby=[string]` (optional): Eine Liste mit kommagetrennten Ausdrücken, nach denen die Ergebnisse sortiert werden. Jeder Ausdruck kann ein Feldname oder ein Aufruf der Funktion `geo.distance()` sein. Jedem Ausdruck kann für eine aufsteigende Reihenfolge `asc` und für eine absteigende Reihenfolge `desc` nachgestellt sein. Standardmäßig wird in aufsteigender Reihenfolge sortiert. `$orderby` ist auf 32 Klauseln beschränkt.

`$select=[string]` (optional): Eine Liste mit kommagetrennten Feldern, die abgerufen werden sollen. Wenn nicht anders angegeben, werden nur der Dokumentschlüssel und der Vorschlagstext zurückgegeben.

`api-version=[string]` (erforderlich). Gültige Werte sind `2014-07-31-Preview` oder `2014-10-20-Preview`. Sie können angeben, welche Version für die einzelnen Anforderungen verwendet wird, um ein versionsspezifisches Verhalten zu nutzen. Im Allgemeinen empfiehlt es sich aber, im gesamten Code die gleiche Version zu verwenden. Die empfohlene Version für den allgemeinen Einsatz ist `2014-07-31-Preview`. Alternativ können Sie `2014-10-20-Preview` verwenden, um experimentelle Funktionen zu bewerten. Ausführliche Informationen finden Sie unter [Versionsverwaltung für den Suchdienst](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Hinweis: Bei diesem Vorgang wird die `api-version` als Abfrageparameter angegeben.

**Anforderungsheader**

In der folgenden Liste werden die erforderlichen und optionalen Anforderungsheader beschrieben.

- `api-key`: Mit `api-key` wird die Anforderung bei Ihrem Suchdienst authentifiziert. Es handelt sich um einen für Ihre Dienst-URL eindeutigen Zeichenfolgewert. Für die Anforderung **Vorschläge** kann ein Administratorschlüssel oder ein Abfrageschlüssel für `api-key` festgelegt werden.

Sie benötigen außerdem den Dienstnamen, um die URL der Anforderung zu erstellen. Sie finden den Dienstnamen und den `api-key` in Ihrem Dienst-Dashboard im Azure-Vorschauportal. Hilfe bei der Seitennavigation finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service.portal.md).

**Anforderungstext**

Keine.

**Antwort**

Bei erfolgreicher Antwort wird der Statuscode "200 OK" zurückgegeben.

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Wenn zum Abrufen der in jedem Element des Arrays enthaltenen Felder die Projektionsoption verwendet wird:

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

Rufen Sie 5 Vorschläge mit der Teilsuche nach "lux" ab.

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&api-version=2014-07-31-Preview

<!---HONumber=August15_HO6-->