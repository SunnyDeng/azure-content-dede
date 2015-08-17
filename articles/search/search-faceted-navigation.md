<properties 
	pageTitle="Implementieren der Facettennavigation in Azure Search" 
	description="Facettennavigation in Azure Search" 
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
	ms.date="07/07/2015" 
	ms.author="heidist"/>

#Implementieren der Facettennavigation in Azure Search

Bei der Facettennavigation handelt es sich um einen Filtermechanismus für die selbstständige Drilldownnavigation in Suchanwendungen. Auch wenn Sie den Begriff „Facettennavigation“ vielleicht bislang nicht kannten, haben Sie sie mit sehr hoher Wahrscheinlichkeit bereits verwendet. Die Facettennavigation ist im Prinzip nichts weiter als die Kategorien, die zum Filtern der Ergebnisse verwendet werden.

## Beispiel:

 ![][1]
  
Facetten unterstützen Sie bei der Suche und stellen sicher, dass Sie Ergebnisse erhalten. Als Entwickler können Sie mithilfe von Facetten besonders hilfreiche Suchkriterien für die Navigation in Ihrem Suchkorpus verfügbar machen. Bei Onlinehändlern basiert die Facettennavigation häufig auf Marken, Abteilungen (Kinderschuhe), Größe, Preis, Beliebtheit und Bewertungen.

Die Implementierung der Facettennavigation ist abhängig von der jeweiligen Suchtechnologie und kann sich als überaus komplex erweisen. In Azure Search wird die Facettennavigation zur Abfragezeit mithilfe von attributierten Feldern erstellt, die zuvor in Ihrem Schema angegeben wurden. In den von Ihrer Anwendung erstellten Abfragen müssen *Facettenabfrageparameter* gesendet werden, um die verfügbaren Facettenfilterwerte für das Dokumentresultset zu erhalten. Zur Eingrenzung des Dokumentresultsets muss die Anwendung einen `$filter`-Ausdruck anwenden.

Bei der Anwendungsentwicklung entfällt ein Großteil der Arbeit auf die Erstellung von Code für Abfragen. Der Dienst stellt viele der Verhaltensweisen der Anwendung bereit, die Sie von der Facettennavigation erwarten würden – einschließlich integrierter Unterstützung für das Einrichten von Bereichen und das Abrufen der Anzahl von Facettenergebnissen. Darüber hinaus bietet der Dienst sinnvolle Standardwerte zur Vermeidung umständlicher Navigationsstrukturen.

Das folgende Beispiel bei CodePlex vermittelt einen guten praktischen Eindruck: [Azure Search AdventureWorks Catalog](https://azuresearchadventureworksdemo.codeplex.com/)

Auch [Azure Search Deep Dive](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410) ist in diesem Zusammenhang interessant. Ab 45:25 wird die Implementierung von Facetten vorgeführt.

Dieser Artikel enthält folgende Abschnitte:

- [Vorgehensweise bei der Erstellung](#howtobuildit)
- [Erstellen der Darstellungsschicht](#presentationlayer)
- [Erstellen des Index](#buildindex)
- [Überprüfen der Datenqualität](#checkdata)
- [Erstellen der Abfrage](#buildquery)
- [Steuerungstipps für die Facettennavigation](#tips)
- [Facettennavigation auf der Grundlage von Bereichswerten](#rangefacets)
- [Facettennavigation auf der Grundlage von Geopunkten](#geofacets)
- [Ausprobieren](#tryitout)

##Argumente für die Verwendung
Effektive Suchanwendungen verfügen neben einem Suchfeld auch noch über andere Interaktionsmodelle. Die Facettennavigation ist ein alternativer Einstiegspunkt für die Suche und stellt eine praktische Alternative zur manuellen Eingabe komplexer Suchausdrücke dar.

##Grundlagen

Wenn Sie noch keine Erfahrung mit der Suchentwicklung gesammelt haben, können Sie sich die Facettennavigation am besten als eine Lösung vorstellen, die die Möglichkeiten für die selbstständige Suche aufzeigt. Die Facettennavigation ist eine Art Drilldownsuche, die auf vordefinierten Filtern basiert, und dient zur schnellen Eingrenzung der Suchergebnisse durch Point-and-Click-Aktionen.

**Interaktionsmodell**

Die Suchumgebung für die Facettennavigation ist iterativ und kann als Sequenz von Abfragen betrachtet werden, die infolge von Benutzeraktionen ausgeführt werden.

Der Ausgangspunkt ist eine Anwendungsseite mit Facettennavigation (üblicherweise in einem Randbereich). Die Facettennavigation wird häufig als Baumstruktur mit Kontrollkästchen für die jeweiligen Werte oder mit anklickbarem Text dargestellt.

1.	Die Facettennavigationsstruktur wird in einer an Azure Search gesendeten Abfrage mithilfe entsprechender Abfrageparameter angegeben. So kann die Abfrage beispielsweise `facet=Rating` enthalten und ggf. mit einer `:values`- oder `:sort`-Option versehen werden, um die Darstellung weiter zu verfeinern.
2.	Die Darstellungsschicht rendert unter Verwendung der in der Anforderung angegebenen Facetten eine Suchseite mit Facettennavigation.
3.	Bei Verwendung einer bewertungsbasierten Facettennavigationsstruktur klickt der Benutzer auf „4“, um anzugeben, dass nur Produkte mit der Bewertung 4 oder höher angezeigt werden sollen. 
4.	Im Gegenzug sendet die Anwendung eine Abfrage mit `$filter=Rating ge 4`. 
5.	Die Darstellungsschicht aktualisiert die Seite und zeigt ein reduziertes Resultset an, das nur Elemente enthält, die den neuen Kriterien entsprechen (in diesem Fall Produkte mit der Bewertung 4 oder höher).

Eine Facette ist ein Abfrageparameter, darf aber nicht mit einer Abfrageeingabe verwechselt werden. Sie wird nie als Auswahlkriterium in einer Abfrage verwendet. Stellen Sie sich Facettenabfrageparameter stattdessen als Eingaben für die Navigationsstruktur vor, die in der Antwort zurückgegeben wird. Azure Search prüft für jeden angegebenen Facettenabfrageparameter, wie viele Dokumente in den Teilergebnissen für die einzelnen Facettenwerte enthalten sind.

Beachten Sie `$filter` in Schritt 4. Dies ist ein wichtiger Aspekt der Facettennavigation. Facetten und Filter sind in der API zwar unabhängig, werden aber beide benötigt, um das gewünschte Ergebnis zu erzielen.

**Entwurfsmuster**

Im Anwendungscode werden Facettenabfrageparameter für die Rückgabe der Facettennavigationsstruktur und der Facettenergebnisse sowie ein $filter-Ausdruck zur Behandlung des Klickereignisses verwendet. Stellen Sie sich den `$filter`-Ausdruck als CodeBehind für die tatsächliche Eingrenzung der an die Darstellungsschicht zurückgegebenen Suchergebnisse vor. Bei Verwendung einer farbbasierten Facette wird das Klicken auf die Farbe Rot mithilfe eines `$filter`-Ausdrucks implementiert, der nur Elemente mit der Farbe Rot auswählt.

**Abfragegrundlagen in Azure Search**

In Azure Search wird eine Anforderung mithilfe von Abfrageparametern angegeben. (Eine Beschreibung der einzelnen Parameter finden Sie unter [Dokumente durchsuchen](http://msdn.microsoft.com/library/azure/dn798927.aspx).) Keiner der Abfrageparameter ist erforderlich, Sie benötigen jedoch mindestens einen, damit die Abfrage gültig ist.

Genauigkeit (also im Allgemeinen als Herausfiltern irrelevanter Treffer), wird durch mindestens einen der folgenden Ausdrücke erreicht:

- **search=**<br/> Der Wert dieses Parameters stellt den Suchausdruck dar. Hierbei kann es sich um ein einfaches Textelement, aber auch um einen komplexen Suchausdruck mit mehreren Begriffen und Operatoren handeln. Auf dem Server wird ein Suchbegriff für die Volltextsuche verwendet. Dabei werden durchsuchbare Felder im Index nach passenden Begriffen durchsucht und nach Rang sortierte Ergebnisse zurückgegeben. Wenn Sie `search` auf NULL festlegen, wird die Abfrage für den gesamten Index (`search=*`) ausgeführt. In diesem Fall werden die zurückzugebenden Dokumente `($filter`) und deren Reihenfolge (`scoringProfile` oder `$orderb`) in erster Linie durch andere Elemente der Abfrage (etwa ein `$filter`-Element oder ein Bewertungsprofil) beeinflusst.

- **$filter=**<br/> Ein Filter ist ein leistungsstarker Mechanismus für die Einschränkung von Suchergebnissen auf der Grundlage der Werte bestimmter Dokumentattribute. Ein `$filter`-Element wird ganz zu Beginn ausgewertet. Anschließend werden mithilfe einer Facettenlogik die verfügbaren Werte und die entsprechende Anzahl für den jeweiligen Wert generiert.

Komplexe Suchausdrücke wirken sich nachteilig auf die Abfrageleistung aus. Nutzen Sie möglichst durchdachte Filterausdrücke, um die Genauigkeit und die Abfrageleistung zu verbessern.

Die folgende Gegenüberstellung eines komplexen Suchausdrucks und eines Suchausdrucks mit Filterausdruck veranschaulicht, wie ein Filter für mehr Genauigkeit sorgt:

- `GET /indexes/hotel/docs?search=lodging budget +Seattle –motel +parking`

- `GET /indexes/hotel/docs?search=lodging&$filter=City eq ‘Seattle’ and Parking and Type ne ‘motel’`

Zwar handelt es sich in beiden Fällen um eine gültige Abfrage, mit der zweiten kann jedoch besser nach Unterkünften mit Parkmöglichkeiten in Seattle gesucht werden, bei denen es sich nicht um ein Motel handelt. Die erste Abfrage basiert darauf, dass in Zeichenfolgenfeldern wie „Name“, „Beschreibung“ oder in anderen Feldern mit durchsuchbaren Daten bestimmte Wörter verwendet oder nicht verwendet werden. Die zweite Abfrage sucht nach exakten Übereinstimmungen für strukturierte Daten und ist wahrscheinlich deutlich genauer.

In Anwendungen mit Facettennavigation sollte jede Benutzeraktion mit einer Facettennavigationsstruktur eine Einschränkung der Suchergebnisse auf der Grundlage eines Filterausdrucks zur Folge haben.

<a name="howtobuildit"></a>
##Vorgehensweise bei der Erstellung

Die Facettennavigation in Azure Search wird zwar in Ihrem Anwendungscode implementiert, der die Anforderung erstellt, basiert aber auf vordefinierten Elementen Ihres Schemas.

In Ihrem Suchindex ist das `Facetable [true|false]`-Indexattribut vordefiniert. Dieses wird auf bestimmte Felder festgelegt, um deren Verwendung in einer Facettennavigationsstruktur zu aktivieren oder zu deaktivieren. Ohne `"Facetable" = true` kann ein Feld nicht für die Facettennavigation verwendet werden.

Zur Abfragezeit erstellt Ihr Anwendungscode eine Anforderung mit `facet=[string]`. Dieser Anforderungsparameter gibt das Feld an, das der Facette zugrunde liegen soll. Eine Abfrage kann mehrere Facetten (Beispiel: `&facet=color&facet=category&facet=rating`) umfassen. Diese müssen jeweils durch ein kaufmännisches Und-Zeichen (&) voneinander getrennt werden.

Der Anwendungscode muss auch einen `$filter`-Ausdruck konstruieren, um Klickereignisse in einer Facettennavigation zu behandeln. Der `$filter`-Ausdruck verwendet die Facettenwerte als Filterkriterien und grenzt so die Suchergebnisse ein.

Azure Search gibt die Suchergebnisse für die vom Benutzer eingegebenen Begriffe sowie Aktualisierungen für die Facettennavigationsstruktur zurück. Bei der Facettennavigation in Azure Search handelt es sich um eine einstufige Konstruktion mit Facettenwerten und der jeweiligen Anzahl gefundener Ergebnisse.

Die Darstellungsschicht in Ihrem Code stellt die Benutzeroberfläche bereit. Sie führt die Bestandteile der Facettennavigation (wie Beschriftung, Werte, Kontrollkästchen und die Anzahl) auf. Die REST-API von Azure Search ist plattformunabhängig und kann somit mit jeder beliebigen Programmiersprache und Plattform verwendet werden. Wichtig: Verwenden Sie Benutzeroberflächenelemente, die eine inkrementelle Aktualisierung unterstützen und bei denen jeweils der Benutzeroberflächenzustand aktualisiert wird, wenn weitere Facetten ausgewählt werden.

In den folgenden Abschnitten wird ausführlicher auf die Erstellung der einzelnen Komponenten eingegangen. Den Anfang macht die Darstellungsschicht.

<a name="presentationlayer"></a>
##Erstellen der Darstellungsschicht

Auf der Grundlage der Darstellungsschicht können Sie Anforderungen ermitteln, die andernfalls womöglich übersehen werden, sowie Funktionen nachvollziehen, die für Suchvorgänge unverzichtbar sind.

Für die Facettennavigation gilt: Ihre Web- oder Anwendungsseite zeigt die Facettennavigationsstruktur an, erkennt Benutzereingaben auf der Seite und fügt die geänderten Elemente ein.

Bei Webanwendungen wird in der Darstellungsschicht üblicherweise AJAX verwendet, da damit inkrementelle Änderungen aktualisiert werden können. Alternativ können Sie auch ASP.NET MVC oder eine andere Visualisierungsplattform verwenden, die eine HTTP-Verbindung mit einem Azure Search-Dienst herstellen kann. Bei der in diesem Artikel verwendeten Beispielanwendung **AdventureWorks Catalog** handelt es sich um eine ASP.NET MVC-Anwendung.

Das folgende Beispiel stammt aus der Datei **index.cshtml** der Beispielanwendung und erstellt eine dynamische HTML-Struktur zum Anzeigen einer Facettennavigation auf Ihrer Suchergebnisseite. In dem Beispiel wird die Facettennavigation in die Suchergebnisseite integriert und angezeigt, nachdem der Benutzer einen Suchbegriff übermittelt hat.

Beachten Sie, dass jede Facette eine Beschriftung („Colors“, „Categories“, „Prices“), eine Bindung an ein Facettenfeld („color“, „categoryName“, „listPrice“) und einen `.count`-Parameter besitzt, um die Anzahl der für dieses Facettenergebnis gefundenen Elemente zurückzugeben.

  ![][2]
 

> [AZURE.TIP]Denken Sie bei der Entwicklung der Suchergebnisseite daran, einen Mechanismus zum Deaktivieren von Facetten hinzuzufügen. Bei Verwendung von Kontrollkästchen weiß der Benutzer intuitiv, wie er die Filter deaktivieren kann. Bei einem anderen Layout benötigen Sie unter Umständen eine Breadcrumb-Struktur oder einer anderen kreativen Ansatz. In der Beispielanwendung „AdventureWorks Catalog“ können Sie beispielsweise auf den Titel klicken, um die Suchseite zurückzusetzen.

<a name="buildindex"></a>
##Erstellen des Index

Die Facettenerstellung wird mithilfe des folgenden Indexattributs auf Feldbasis im Index aktiviert: `"Facetable": true`. Alle Feldtypen, die ggf. für die Facettennavigation verwendet werden können, sind standardmäßig als `Facetable` konfiguriert. Zu diesen Feldtypen zählen `Edm.String`, `Edm.DateTimeOffset` sowie alle numerischen Feldtypen. (Mit Ausnahme von `Edm.GeographyPoint` sind eigentlich alle Feldtypen für die Facettennavigation geeignet.)

Bei der Indexerstellung empfiehlt es sich, die Facettenerstellung für Felder, die nicht als Facetten verwendet werden sollen, ausdrücklich zu deaktivieren. Insbesondere Zeichenfolgenfelder für Singleton-Werte (wie etwa eine ID oder ein Produktname) sollten auf `"Facetable": false` festgelegt werden, damit sie nicht versehentlich für die Facettennavigation verwendet werden.

Die folgende Abbildung zeigt das (um einige Attribute gekürzte) Schema für die Beispiel-App „AdventureWorks Catalog“:

 ![][3]
 
Beachten Sie, dass `Facetable` für Zeichenfolgenfelder, die nicht als Facetten verwendet werden sollen (wie etwa eine ID oder ein Name), deaktiviert ist. Die Deaktivierung der Facettenerstellung für Elemente, die nicht als Facetten verwendet werden, verringert den Umfang des Index und verbessert insgesamt die Leistung.

> [AZURE.TIP]Es empfiehlt sich, für jedes Feld den vollständigen Indexattributsatz einzubeziehen. `Facetable` ist zwar standardmäßig für fast alle Felder aktiviert, das bewusste Festlegen der einzelnen Attribute kann jedoch beim Nachvollziehen der einzelnen Schemaentscheidungen hilfreich sein.

<a name="checkdata"></a>
##Überprüfen der Datenqualität 

Bei der Entwicklung einer datenorientierten Anwendung entfällt häufig ein großer Teil der Arbeit auf die Vorbereitung der Daten. Suchanwendungen sind da keine Ausnahme. Die Qualität Ihrer Daten hat direkten Einfluss darauf, ob die Facettennavigationsstruktur erwartungsgemäß funktioniert, und beeinflusst zudem die Effektivität bei der Erstellung von Filtern zur Eingrenzung des Resultsets.

In Azure Search basiert der Suchkorpus auf Dokumenten, die einen Index ergeben. Dokumente enthalten die Werte, die für die Facettenberechnung verwendet werden. Wenn die Facette auf der Marke oder auf dem Preis basieren soll, muss jedes Dokument gültige, einheitliche und produktive Filteroptionswerte für *BrandName* und *ProductPrice* enthalten.

Im Anschluss finden Sie einige hilfreiche Anmerkungen:

- Überlegen Sie sich bei jedem Feld, das als Facette fungieren soll, ob es Werte enthält, die als Filter für eine selbstständige Suche geeignet sind. Die Werte müssen kurz, aussagekräftig und ausreichend unterschiedlich sein, um eine eindeutige Entscheidung zwischen verschiedenen Optionen treffen zu können.
- Tippfehler oder fast übereinstimmende Werte: Wenn die Facette auf der Farbe basiert und die Feldwerte „Orange“ und „Ornage“ (Tippfehler) umfasst, werden bei einer Facette, die auf dem Feld „Color“ basiert, beide Optionen verwendet.
- Auch gemischte Groß-/Kleinschreibung kann die Facettennavigation durcheinander bringen, da „orange“ und „Orange“ als zwei unterschiedliche Werte angezeigt werden. 
- Singular- und Pluralformen des gleichen Werts können ebenfalls zu separaten Facetten führen.

Für eine effektive Facettennavigation ist daher eine sorgfältige Datenvorbereitung unerlässlich.

<a name="buildquery"></a>
##Erstellen der Abfrage

Der Code, den Sie zum Erstellen von Abfragen schreiben, muss alle Bestandteile einer gültigen Abfrage umfassen. Hierzu zählen unter anderem Suchausdrücke, Facetten, Filter und Bewertungsprofile (also alles, was für die Erstellung einer Anforderung benötigt wird). In diesem Abschnitt erfahren Sie, wo Facetten in eine Abfrage eingefügt und wie Filter mit Facetten kombiniert werden, um Resultsets einzugrenzen.

Ein Beispiel ist häufig ein guter Ausgangspunkt. Das folgende Beispiel stammt aus der Datei **CatalogSearch.cs** und erstellt eine Anforderung mit einer Facettennavigation auf der Grundlage von Farbe (Color), Kategorie (Category) und Preis (Price).

Beachten Sie, dass Facetten ein integraler Bestandteil dieser Beispielanwendung sind. Die Suchfunktion in AdventureWorks Catalog basiert auf Facettennavigation und Filtern. Dies wird durch die auffällige Platzierung der Facettennavigation auf der Seite deutlich. Die Beispielanwendung enthält URI-Parameter für Facetten (Farbe, Kategorie, Preise) als Eigenschaften für die Search-Methode (wie in der Beispielanwendung konstruiert):

  ![][4]
 
Ein Facettenabfrageparameter wird auf ein Feld festgelegt und kann abhängig vom Datentyp durch eine kommagetrennte Liste mit `count:<integer>`, `sort:<>`, `intervals:<integer>` und `values:<list>` weiter parametrisiert werden. Für numerische Daten wird beim Einrichten von Bereichen eine Werteliste unterstützt. Ausführlichere Informationen zur Verwendung finden Sie unter [Dokumente durchsuchen (Azure Search-API)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Neben Facetten muss die von Ihrer Anwendung erstellte Anforderung auch Filter zum Einschränken der auf der Grundlage der Facettenwertauswahl infrage kommenden Dokumente erstellen. Im Falle eines Radgeschäfts lässt die Facettennavigation Rückschlüsse auf die verfügbaren Farben, Hersteller und Radtypen zu. Die Filterung gibt dagegen beispielsweise Aufschluss darüber, welche roten Mountainbikes im angegebenen Preissegment verfügbar sind.

Wenn ein Benutzer durch Klicken auf „Rot“ angibt, dass nur rote Produkte angezeigt werden sollen, enthält die nächste von der Anwendung gesendete Abfrage Folgendes: `$filter=Color eq ‘Red’`.

## Bewährte Methoden für die Facettennavigation

Im Anschluss werden einige bewährte Methoden zusammengefasst.

- **Genauigkeit**<br/> Verwenden Sie Filter. Wenn Sie sich ausschließlich auf Suchausdrücke verlassen, wird aufgrund der Wortstammerkennung unter Umständen ein Dokument zurückgegeben, bei dem keines der Felder den exakten Facettenwert enthält. 

- **Zielfelder**<br/> Bei einem facettenbasierten Drilldownvorgang sollen in der Regel nur Dokumente enthalten sein, die den Facettenwert in einem bestimmten Feld enthalten (und nicht in einem beliebigen durchsuchbaren Feld). Durch Hinzufügen eines Filters wird der Dienst angewiesen, nur im als Facette fungierenden Feld nach einem übereinstimmenden Wert zu suchen.

- **Indexeffizienz**<br/> Wenn Ihre Anwendung kein Suchfeld, sondern ausschließlich eine Facettennavigation besitzt, können Sie das Feld als `searchable=false`, `facetable=true` markieren, um einen kompakteren Index zu erhalten. Die Indizierung wird außerdem nur für vollständige Facettenwerte durchgeführt – ohne Wortumbruch oder Indizierung der Komponenten eines Werts mit mehreren Wörtern.

- **Leistung**<br/> Filter grenzen die für die Suche infrage kommenden Dokumente ein und schließen sie von der Sortierung aus. Wenn Sie über eine große Anzahl von Dokumenten verfügen, können Sie die Leistung mit einem sehr selektiven Facettendrilldownelement in der Regel deutlich steigern.


<a name="tips"></a>
##Steuerungstipps für die Facettennavigation

Im Anschluss folgen einige Tipps zu bestimmten Bereichen:

**Hinzufügen von Beschriftungen für die einzelnen Felder in der Facettennavigation**

Beschriftungen werden üblicherweise im HTML-Code oder im Formular definiert (in der Beispielanwendung: **index.cshtml**). Azure Search verfügt über keine API für Facettennavigationsbeschriftungen oder andere Metadaten.

**Definieren der Felder, die als Facette verwendet werden können**

Denken Sie daran, dass das Schema des Index bestimmt, welche Felder als Facette zur Verfügung stehen. Wenn ein Feld als Facette verwendet werden kann, gibt die Abfrage an, welches Feld als Facettengrundlage fungieren soll. Das Feld, das als Facettengrundlage fungiert, liefert die Werte, die unter der Beschriftung erscheinen.

Die Werte unter den einzelnen Beschriftungen stammen aus dem Index. Ein Beispiel: Wenn das Feld *Farbe* als Facette fungiert, basieren die für weitere Filtervorgänge verfügbaren Werte auf den Werten für dieses Feld (Rot, Schwarz usw.).

Bei numerischen Werten und DateTime-Werten können explizit Werte für das Facettenfeld festgelegt werden (etwa `facet=Rating,values:1|2|3|4|5`). Zur einfacheren Unterteilung von Facettenergebnissen in zusammenhängende Bereiche (entweder auf der Grundlage numerischer Werte oder auf der Grundlage von Zeiträumen) kann für diese Feldtypen eine Werteliste verwendet werden.

**Kürzen von Facettenergebnissen**

Facettenergebnisse sind Dokumente in den Suchergebnissen, die einem Facettenbegriff entsprechen. Im folgenden Beispiel besitzen 254 Elemente der Suchergebnisse für *Cloud-Computing* auch den Inhaltstyp *interne Spezifikation*. Die Elemente schließen sich nicht zwingend gegenseitig aus. Erfüllt ein Element die Kriterien beider Filter, wird es in jedem Filter gezählt. Dies ist möglich, wenn die Facette auf Feldern vom Typ `Collection(Edm.String)` basiert, die häufig zur Implementierung von Dokumententags verwendet werden.

		Search term: "cloud computing"
		Content type
		   Internal specification (254)
		   Video (10) 

Allgemein gilt: Wenn die Facettenergebnisse ständig zu umfangreich ausfallen, empfiehlt es sich, weitere Filter hinzuzufügen (wie weiter oben beschrieben), um den Benutzern der Anwendung mehr Optionen zum Eingrenzen der Suche zu geben.

**Begrenzen der Elemente in der Facettennavigation**

Jedes Facettenfeld in der Navigationsstruktur ist standardmäßig auf maximal zehn Werte beschränkt. Dieser Standardwert ist für Navigationsstrukturen sinnvoll, da die Werteliste dadurch überschaubar bleibt. Sie können den Standardwert überschreiben, indem Sie einen Zählwert zuweisen.

- `&facet=city,count:5` gibt an, dass als Facettenergebnis nur die ersten fünf Städte aus den Topergebnissen zurückgegeben werden sollen. Wenn der Suchbegriff „Flughafen“ 32 Treffer ergibt und für die Abfrage `&facet=city,count:5` angegeben ist, werden in den Facettenergebnissen nur die ersten fünf eindeutigen Städte mit den meisten Dokumenten in den Suchergebnissen berücksichtigt.

Beachten Sie die Unterscheidung zwischen Facettenergebnissen und Suchergebnissen: Suchergebnisse umfassen alle Dokumente, die der Abfrage entsprechen. Facettenergebnisse sind die Übereinstimmungen für die einzelnen Facettenwerte. In unserem Beispiel umfassen die Suchergebnisse Städtenamen, die sich nicht in der Facettenklassifizierungsliste (in unserem Beispiel: fünf) befinden. Durch die Facettennavigation herausgefilterte Ergebnisse werden dem Benutzer angezeigt, wenn er Facetten löscht oder zusätzliche Facetten auswählt.

> [AZURE.NOTE]Die Erläuterung von `count` kann verwirrend sein, wenn mehrere Typen vorhanden sind. Die folgende Tabelle bietet einen kurzen Überblick über die Verwendung des Begriffs in Azure Search-API, Beispielcode und Dokumentation:

- `@colorFacet.count`<br/> Im Darstellungscode wird die Facette mit einem count-Parameter versehen, um die Anzahl der Facettenergebnisse anzuzeigen. In den Facettenergebnissen gibt „count“ die Anzahl von Dokumenten an, die dem Facettenbegriff oder -bereich entsprechen.

- `&facet=City,count:12`<br/> In einer Facettenabfrage können Sie „count“ auf einen Wert festlegen. Der Standardwert lautet „10“, Sie können jedoch einen größeren oder kleineren Wert angeben. Mit `count:12` werden in den Facettenergebnissen die 12 relevantesten Übereinstimmungen (auf der Grundlage der Dokumentanzahl) zurückgegeben.

- "`@odata.count`"<br/> In der Abfrageantwort gibt dieser Wert die Anzahl übereinstimmender Elemente in den Suchergebnissen an. Dieser ist häufig größer als die Summe aller Facettenergebnisse, da hier auch Elemente enthalten sind, die zwar dem Suchbegriff entsprechen, für die aber keine übereinstimmenden Facettenwerte vorhanden sind.


**Ebenen der Facettennavigation**

Wie bereits erwähnt wird die hierarchische Schachtelung von Facetten nicht direkt unterstützt. Standardmäßig unterstützt die Facettennavigation lediglich eine Ebene von Filtern. Diese Beschränkung lässt sich jedoch umgehen. Sie können eine hierarchische Facettenstruktur in einem Element vom Typ `Collection(Edm.String)` codieren, indem Sie pro Hierarchie jeweils einen Eintrag angeben. Die Implementierung dieser Umgehung ist nicht Gegenstand dieses Artikels. Informationen zu Auflistungen finden Sie unter [OData nach Beispiel](http://msdn.microsoft.com/library/ff478141.aspx).

**Überprüfen von Feldern**

Wenn Sie die Facettenliste dynamisch auf der Grundlage nicht vertrauenswürdiger Benutzereingaben erstellen, müssen Sie entweder sicherstellen, dass die Namen der Facettenfelder gültig sind, oder die Namen bei der URL-Erstellung mit Escapezeichen versehen – entweder mithilfe von `Uri.EscapeDataString()` in .NET oder mithilfe einer Entsprechung für die gewählte Plattform.

**Anzahl in Facettenergebnissen**

Beim Hinzufügen eines Filters zu einer Facettenabfrage empfiehlt es sich unter Umständen, die Facettenanweisung beizubehalten (Beispiel: `facet=Rating&$filter=Rating ge 4`). Aus technischer Sicht wird „facet=Rating“ zwar nicht benötigt, die Angabe liefert jedoch die Anzahl von Facettenwerten für Bewertungen mit dem Wert „4“ und höher. Wenn beispielsweise ein Benutzer auf „4“ klickt und die Abfrage einen Filter für „größer oder gleich 4“ enthält, wird die Anzahl für jede Bewertung ab dem Wert „4“ zurückgegeben.

**Auswirkungen von Sharding auf die Facettenanzahl**

Unter bestimmten Umständen kann es vorkommen, dass die Facettenanzahl nicht den Resultsets entspricht (siehe Forumsbeitrag [Facettennavigation in Azure Search](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Die Abweichung der Facettenanzahl kann auf die Sharding-Architektur zurückzuführen sein. Jeder Suchindex besitzt mehrere Shards, von denen jeweils die x relevantesten Facetten (auf der Grundlage der Dokumentanzahl) zurückgegeben werden. Diese werden dann zu einem einzelnen Ergebnis zusammengefasst. Falls nun für einige Shards besonders viele und für andere Shards besonders wenige Werte vorhanden sind, kann es vorkommen, dass einige Facettenwerte fehlen oder in den Ergebnissen nicht korrekt erfasst wurden.

Dieses Verhalten kann sich zwar jederzeit ändern, wenn Sie jedoch aktuell davon betroffen sind, können Sie das Problem umgehen, indem Sie „count:<number>“ künstlich auf eine sehr große Zahl erhöhen, um vollständige Berichte aus jeder Shard zu erzwingen. Wenn der Wert von „count:“ größer oder gleich der Anzahl eindeutiger Werte im Feld ist, erhalten Sie garantiert exakte Ergebnisse. Bei einer besonders hohen Anzahl von Dokumenten ist jedoch mit Leistungseinbußen zu rechnen. Verwenden Sie die Option daher mit Bedacht.

<a name="rangefacets"></a>
##Facettennavigation auf der Grundlage von Bereichswerten

Die Verwendung von Facettenbereichen ist eine verbreitete Anforderung für Suchanwendungen. Bereiche werden für numerische Daten und für DateTime-Werte unterstützt. Weitere Informationen zu den einzelnen Ansätzen finden Sie unter [Dokumente durchsuchen (Azure Search-API)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Azure Search vereinfacht die Bereichserstellung durch zwei Bereichsberechnungsansätze. Bei beiden Ansätzen erstellt Azure Search die entsprechenden Bereiche auf der Grundlage Ihrer Eingaben. Wenn Sie also beispielsweise die Bereichswerte „10|20|30“ angeben, werden automatisch folgende Bereiche erstellt: 0 bis 10, 10 bis 20 und 20 bis 30. Die Beispielanwendung entfernt alle leeren Intervalle.

**Ansatz 1: Verwenden des interval-Parameters**<br/> Mit der folgenden Angabe können Sie Preisfacetten in 10-Dollar-Schritten festlegen: `&facet=price,interval:10`


**Ansatz 2: Verwenden einer Werteliste**<br/> Bei numerischen Daten können Sie eine Werteliste verwenden. Sehen Sie sich den folgenden Facettenbereich für „ListPrice“ an:

  ![][5]

Der Bereich wird in der Datei **CatalogSearch.cs** mithilfe einer Werteliste angegeben:

    facet=listPrice,values:10|25|100|500|1000|2500

Jeder Bereich wird mit „0“ als Ausgangspunkt und einem Wert aus der Liste als Endpunkt erstellt und anschließend um den vorherigen Bereich gekürzt, um eigenständige Intervalle zu erhalten. Dies wird im Rahmen der Facettennavigation von Azure Search durchgeführt. Sie müssen keinen Code schreiben, um die einzelnen Intervalle zu strukturieren.

### Erstellen eines Filters für Facettenbereiche ###

Wenn Sie Dokumente auf der Grundlage eines benutzerdefinierten Bereichs filtern möchten, können Sie die Filteroperatoren `"ge"` und `"lt"` in einem zweiteiligen Ausdruck verwenden und so die Endpunkte des Bereichs definieren. Wählt der Benutzer beispielsweise den Bereich „10 bis 25“ aus, lautet der Filter `$filter=listPrice ge 10 and listPrice lt 25`.

In der Beispielanwendung verwendet der Filterausdruck die Parameter **priceFrom** und **PriceTo**, um die Endpunkte festzulegen. Die Methode **BuildFilter** in der Datei **CatalogSearch.cs** enthält den Filterausdruck, der die Dokumente innerhalb eines Bereichs zurückgibt.

  ![][6]

<a name="geofacets"></a>
##Facettennavigation auf der Grundlage von Geopunkten

Filter, die den Benutzer bei der Wahl eines Geschäfts, Restaurants oder eines Ziels in Abhängigkeit von der Nähe zum aktuellen Standort unterstützen, sind weit verbreitet. Diese Art von Filter sieht zwar vielleicht wie eine Facettennavigation aus, ist in Wahrheit aber nur ein Filter. Diese Option wird hier nur erwähnt, falls Sie nach Implementierungstipps für dieses spezielle Designproblem suchen.

In Azure Search stehen zwei räumliche Funktionen zur Verfügung: **geo.distance** und **geo.intersects**.

- **geo.distance** gibt die Entfernung zwischen zwei Punkten (in Kilometern) zurück, wobei es sich bei einem Punkt um ein Feld und bei dem anderen Punkt um eine Konstante handelt, die als Teil des Filters übergeben wird. 

- **geo.intersects** gibt „true“ zurück, wenn ein bestimmter Punkt innerhalb eines bestimmten Polygons liegt. Der Punkt ist hierbei ein Feld, das Polygon eine Konstantenliste mit Koordinaten, die als Teil des Filters übergeben werden.

Filterbeispiele finden Sie unter [OData-Ausdruckssyntax für Azure Search](http://msdn.microsoft.com/library/azure/dn798921.aspx). Weitere Informationen zur räumlichen Suche finden Sie unter [Erstellen einer Anwendung für räumliche Suche in Azure Search](search-create-geospatial.md).

<a name="tryitout"></a>
##Ausprobieren

Die bei Codeplex verfügbare Adventure Works-Demo für Azure Search enthält die Beispiele, auf die in diesem Artikel Bezug genommen wird. Achten Sie bei der Arbeit mit Suchergebnissen auf die Veränderung der Abfragekonstruktion in der URL. Diese Anwendung fügt die ausgewählten Facetten dem URI hinzu.

1.	Konfigurieren Sie die Beispielanwendung (siehe [Erstellen Ihrer ersten Anwendung für Anweisungen](search-create-first-solution.md)). 

	Beachten Sie das Schema, das in der Datei „Program.cs“ des CatalogIndexer-Projekts definiert ist. Es gibt facettenfähige Felder für „color“, „listPrice“, „size“, „weight“, „categoryName“ und „modelName“ an. In der Facettennavigation sind jedoch nur einige dieser Felder („color“, „listPrice“ und „categoryName“) tatsächlich implementiert.

3.	Führen Sie die Anwendung aus.

	Zu Beginn ist nur Suchfeld zu sehen. Sie können sofort auf die Suchschaltfläche klicken, um alle Ergebnisse zu erhalten, oder einen Suchbegriff eingeben.

	![][7]
 
4.	Geben Sie einen Suchbegriff (beispielsweise „bike“) ein, und klicken Sie auf **Search**. Die Abfrage wird schnell ausgeführt.
 
	Zusammen mit den Suchergebnissen wird auch eine Facettennavigationsstruktur zurückgegeben. Die Facetten für Farben, Kategorien und Preise in der URL sind NULL. Die Facettennavigationsstruktur auf der Suchergebnisseite enthält jeweils die Anzahl für die einzelnen Facettenergebnisse.

	 ![][8]
 
5.	Klicken Sie auf eine Farbe, auf eine Kategorie und auf einen Preisbereich. Die Facetten sind beim ersten Suchvorgang immer NULL. Wenn sie jedoch Werte erhalten, werden Elemente, die den Kriterien nicht mehr entsprechen, aus den Suchergebnissen entfernt. Beachten Sie, dass die Änderungen an Ihrer Abfrage vom URI übernommen werden.

	![][9]
 
6.	Klicken Sie am oberen Seitenrand auf **AdventureWorks Catalog**, um die Facettenabfrage zu verwerfen und ein anderes Abfrageverhalten zu testen.

	![][10]
 
<a name="nextstep"></a>
##Nächster Schritt

Zum Testen Ihrer Kenntnisse können Sie ein Facettenfeld für *modelName* hinzufügen. Da der Index für diese Facette bereits eingerichtet ist, sind keine Änderungen am Index erforderlich. Sie müssen jedoch den HTML-Code mit einer neuen Facette für Modelle versehen und das Facettenfeld dem Abfragekonstruktor hinzufügen.

Weitere Einblicke in die Entwurfsprinzipien der Facettennavigation erhalten Sie unter folgenden Links:

- [Designing for Faceted Search](http://www.uie.com/articles/faceted_search/)
- [Design Patterns: Faceted Navigation](http://alistapart.com/article/design-patterns-faceted-navigation)

<!--Anchors-->
[How to build it]: #howtobuildit
[Build the presentation layer]: #presentationlayer
[Build the index]: #buildindex
[Check for data quality]: #checkdata
[Build the query]: #buildquery
[Tips on how to control faceted navigation]: #tips
[Faceted navigation based on range values]: #rangefacets
[Faceted navigation based on GeoPoints]: #geofacets
[Try it out]: #tryitout

<!--Image references-->
[1]: ./media/search-faceted-navigation/Facet-1-slide.PNG
[2]: ./media/search-faceted-navigation/Facet-2-CSHTML.PNG
[3]: ./media/search-faceted-navigation/Facet-3-schema.PNG
[4]: ./media/search-faceted-navigation/Facet-4-SearchMethod.PNG
[5]: ./media/search-faceted-navigation/Facet-5-Prices.PNG
[6]: ./media/search-faceted-navigation/Facet-6-buildfilter.PNG
[7]: ./media/search-faceted-navigation/Facet-7-appstart.png
[8]: ./media/search-faceted-navigation/Facet-8-appbike.png
[9]: ./media/search-faceted-navigation/Facet-9-appbikefaceted.png
[10]: ./media/search-faceted-navigation/Facet-10-appTitle.png

<!--Link references-->
[Designing for Faceted Search]: http://www.uie.com/articles/faceted_search/
[Design Patterns: Faceted Navigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Create your first application]: search-create-first-solution.md
[OData expression syntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Create a geospatial search application in Azure Search]: search-create-geospatial.md
[Azure Search Adventure Works Demo]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/
[Faceting on Azure Search forum post]: ../faceting-on-azure-search.md?forum=azuresearch
[Search Documents (Azure Search API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx

 

<!---HONumber=August15_HO6-->