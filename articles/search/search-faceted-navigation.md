<properties 
	pageTitle="Implementieren von Facettennavigation in Azure Search" 
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
	ms.date="04/16/2015" 
	ms.author="heidist"/>

#Implementieren von Facettennavigation in Azure Search#

Facettennavigation ist ein Filtermechanismus, der selbstgesteuerte Drilldownnavigation in Suchanwendungen bereitstellt. Obwohl der Begriff "Facettennavigation" möglicherweise ungewohnt ist, ist es fast sicher, dass Sie eine bereits einmal verwendet haben. Wie das folgende Beispiel zeigt, ist Facettennavigation nichts anderes, als die zum Filtern der Ergebnisse verwendeten Kategorien.

## Wie es aussieht ##

 ![][1]
  
Facets können Ihnen dabei helfen, das Gesuchte zu finden und gleichzeitig sicherzustellen, dass Sie nicht null Ergebnisse erhalten. Als Entwickler können Sie mit Facets die nützlichsten Suchkriterien für die Navigation in Ihrem Suchkorpus verfügbar machen. Bei Onlinehandelsanwendungen wird die Facettennavigation häufig über Marken, Abteilungen (Kinderschuhe), Größe, Preis, Beliebtheit und Bewertungen aufgebaut. 

Das Implementieren der Facettennavigation unterscheidet sich je nach Suchtechnologie und kann sehr komplex sein. 
In Azure Search wird die Facettennavigation zur Abfragezeit erzeugt, wobei zuvor in Ihrem Schema angegebene attributierte Felder verwendet werden. In den Abfragen, die von Ihrer Anwendung erstellt werden, muss eine Abfrage  *facet query parameters* senden, um die für dieses Dokumentresultset verfügbaren Facetfilterwerte zu erhalten. Um das Dokumentresultset tatsächlich zu kürzen, muss die Anwendung einen Ausdruck `$filter`-Ausdruck anwenden.

Im Hinblick auf die Entwicklung von Anwendungen bildet das Schreiben von Code, der Abfragen erstellt, den größten Teil der Arbeit. Viele der Anwendungsverhalten, die Sie normalerweise von der Facettennavigation erwarten würden, wird von dem Dienst bereitgestellt, einschließlich integrierter Unterstützung für das Einrichten von Bereichen und das Abrufen der Anzahl von Facetergebnissen. Der Dienst umfasst außerdem vernünftige Standardwerte, dank derer Sie unhandliche Navigationsstrukturen vermeiden. 

Als praktische Einführung empfehlen wir dieses Beispiel auf CodePlex: [Azure Search AdventureWorks-Katalog](https://azuresearchadventureworksdemo.codeplex.com/)

Sie können sich auch [Azure Search Deep Dive](http://channel9.msdn.com/Events/TechEd/Europe/2014/DBI-B410) ansehen. Bei Stelle 45:25 finden Sie eine Demonstration der Implementierung von Facets.

Dieser Artikel enthält die folgenden Abschnitte:

- [Erstellen der Navigation](#howtobuildit)
- [Erstellen der Darstellungsschicht](#presentationlayer)
- [Erstellen des Indexes](#buildindex)
- [Überprüfen der Datenqualität](#checkdata)
- [Erstellen der Abfrage](#buildquery)
- [Tipps zur Steuerung der Facettennavigation](#tips)
- [Auf einem Wertebereich basierende Facettennavigation](#rangefacets)
- [Auf GeoPoints basierende Filternavigation](#geofacets)
- [Ausprobieren](#tryitout)

##Gründe für die Verwendung##
Die effektivsten Suchanwendungen besitzen neben einem Suchfeld mehrere Interaktionsmodelle. Facettennavigation ist ein alternativer Einstiegspunkt für die Suche, der eine bequeme Alternative zur manuellen Eingabe komplexer Suchausdrücke bietet.

##Grundlegende Kenntnisse##

Wenn Sie noch keine Erfahrung mit der Entwicklung von Suchen haben, stellen Sie sich die Facettennavigation am besten so vor, dass sie die Möglichkeiten für eine selbstgesteuerte Suche aufzeigt. Es handelt sich um eine Art von Drilldown-Suchfunktionalität, die auf vordefinierten Filtern basiert und für die schnelle Eingrenzung von Suchergebnissen durch Point-and-Click-Aktionen verwendet wird. 

**Interaktionsmodell**

Die Suchfunktion bei der Facettennavigation arbeitet iterativ. Lassen Sie uns also zunächst damit beginnen, dass wir sie als eine Abfolge von Abfragen begreifen, die als Reaktion auf Benutzeraktionen erfolgen.

Der Ausgangspunkt ist eine Anwendungsseite, die Facettennavigation bereitstellt, in der Regel in der Peripherie. Facettennavigation ist häufig eine Baumstruktur mit Kontrollkästchen für jeden Wert oder klickbarem Text. 

1.	Eine an Azure Search gesendete Abfrage gibt die Facettennavigationsstruktur über einen oder mehrere Facetabfrageparameter an. Beispielsweise könnte die Abfrage  `facet=Rating` enthalten, möglicherweise mit einer `:values`- oder `:sort`-Option, um die Darstellung weiter zu verfeinern.
2.	Die Darstellungsschicht stellt eine Suchseite dar, die Facettennavigation unter Verwendung der in der Anforderung angegebenen Facets bereitstellt.
3.	Bei Annahme einer Facettennavigationsstruktur, die Bewertungen umfasst, klickt der Benutzer auf "4", um anzugeben, dass nur Produkte mit einer Bewertung von 4 oder höher angezeigt werden sollen. 
4.	Als Antwort sendet die Anwendung eine Abfrage, die `$filter=Rating ge 4` enthält. 
5.	Die Darstellungsschicht aktualisiert die Seite, sodass ein reduziertes Resultset angezeigt wird, das nur die Elemente enthält, die die neuen Kriterien erfüllen (in diesem Fall mit 4 und höher bewertete Produkte).

Ein Facet ist ein Abfrageparameter, der aber nicht mit der Abfrageeingabe zu verwechseln ist. Er wird nie als Auswahlkriterium in einer Abfrage verwendet. Stellen Sie sich stattdessen Facetabfrageparameter als Eingaben an die Navigationsstruktur vor, die in der Antwort zurückgegeben wird. Für jeden von Ihnen angegebenen Facetabfrageparameter wertet Azure Search aus, wie viele Dokumente in den Teilergebnissen für jeden Facetwert vorhanden sind.

Beachten Sie den `$filter` in Schritt 4. Dies ist ein wichtiger Aspekt der Facettennavigation. Obwohl Facets und Filter in der API unabhängig sind, benötigen Sie beide, um die gewünschte Funktion bereitzustellen. 

**Entwurfsmuster**

Im Anwendungscode besteht das Muster darin, Facetabfrageparameter zu verwenden, um die Facettennavigationsstruktur zusammen mit Facetergebnissen sowie einem `$filter`-Ausdruck zurückzugeben, der das Click-Ereignis verarbeitet. Stellen Sie sich den `$filter`-Ausdruck als den Code hinter der tatsächlichen Kürzung der Suchergebnisse vor, die an die Darstellungsschicht zurückgegeben werden. Bei Annahme eines Colors-Facets wird das Klicken auf die Farbe Rot durch einen `$filter`-Ausdruck implementiert, der nur die Elemente auswählt, die die Farbe Rot besitzen. 

**Grundlagen von Abfragen in Azure Search**

In Azure Search wird eine Anforderung durch einen oder mehrere Abfrageparameter angegeben (eine Beschreibung der einzelnen Parameter finden Sie unter [Durchsuchen von Dokumenten](http://msdn.microsoft.com/library/azure/dn798927.aspx)). Keiner der Abfrageparameter ist erforderlich, aber Sie müssen mindestens einen verwenden, damit eine Abfrage gültig ist.

Genauigkeit, ganz allgemeinen als die Fähigkeit verstanden, irrelevante Treffer auszufiltern, wird durch einen oder beide dieser Ausdrücke erzielt:

- **search=**<br/>
Der Wert dieses Parameters bildet den Suchausdruck. Es kann sich um ein einzelnes Textstück oder um einen komplexen Suchausdruck handeln, der mehrere Ausdrücke und Operatoren enthält. Auf dem Server wird ein Suchausdruck für die Volltextsuche verwendet, wobei durchsuchbare Felder im Index nach übereinstimmenden Begriffen abgefragt und die Ergebnisse in Rangfolge zurückgegeben werden. Wenn Sie  `search` auf null festlegen, erfolgt die Abfrageausführung über den gesamten Index (d. h.  `search=*`). In diesem Fall sind andere Elemente der Abfrage, z. B. ein `$filter` oder ein Bewertungsprofil, die Hauptfaktoren, die sich darauf auswirken, welche Dokumente zurückgegeben werden (`$filter`) und in welcher Reihenfolge (`scoringProfile` oder `$orderby`).

- **$filter=**<br/>
Ein Filter ist ein leistungsfähiger Mechanismus zum Einschränken der Größe von Suchergebnissen, basierend auf den Werten bestimmter Dokumentattribute. Ein `$filter` wird zuerst ausgewertet, gefolgt von Facettenlogik, die die verfügbaren Werte und die entsprechenden Anzahlwerte für jeden Wert generiert.

Komplexe Suchausdrücke verringern die Leistung der Abfrage. Nutzen Sie, wann immer möglich, gut durchdacht konstruierte Filterausdrücke, um die Genauigkeit zu erhöhen und die Abfrageleistung zu verbessern.

Um besser zu verstehen, wie ein Filter die Genauigkeit steigern kann, vergleichen Sie einen komplexen Suchausdruck mit einem, der einen Filterausdruck enthält:

- `GET /indexes/hotel/docs?search=lodging budget +Seattle -motel +parking`

- `GET /indexes/hotel/docs?search=lodging&$filter=City eq 'Seattle' and Parking and Type ne 'motel'`

Obwohl beide Abfragen gültig sind, ist die zweite überlegen, wenn Sie nach Nicht-Motels mit Parkplatz in Seattle suchen. Die erste Abfrage basiert auf den spezifischen Wörtern, die in Zeichenfolgenfeldern erwähnt oder nicht erwähnt werden, z. B. Name, Beschreibung und andere Felder, die durchsuchbare Daten enthalten. Die zweite Abfrage sucht nach exakten Übereinstimmungen in strukturierten Daten und wird wahrscheinlich wesentlich genauer sein.

In Anwendungen, die Facettennavigation beinhalten, möchten Sie sicherstellen, dass jede über eine Facettennavigationsstruktur getätigte Benutzeraktion mit einer Einschränkung der Suchergebnisse einhergeht, die durch einen Filterausdruck erzielt wird.

<a name="howtobuildit"></a>
# Erstellen der Navigation #

Facettennavigation in Azure Search wird in Ihren Anwendungscode implementiert, der die Anforderung erstellt, beruht aber auf vordefinierten Elementen in Ihrem Schema.

In Ihrem Suchindex ist das Indexattribut  `Facetable [true|false]`  vordefiniert, das auf ausgewählte Felder festgelegt ist, um deren Verwendung in einer Facettennavigationsstruktur zu aktivieren bzw. deaktivieren. Ohne `"Facetable" = true` kann ein Feld nicht in der Facettennavigation verwendet werden.

Zur Abfragezeit erstellt Ihr Anwendungscode eine Anforderung, die  `facet=[string]` enthält, einen Anforderungsparameter, der das Feld bereitstellt, nach dem facettiert werden soll. Eine Abfrage kann mehrere Facets umfassen, wie z. B. `&facet=color&facet=category&facet=rating`, wobei jedes durch ein kaufmännisches Und-Zeichen (&) voneinander getrennt wird.

Anwendungscode muss auch einen `$filter`-Ausdruck erstellen, um Click-Ereignisse in der Facettennavigation zu verarbeiten. Ein `$filter` reduziert die Suchergebnisse, indem der Facetwert als Filterkriterium verwendet wird.

Azure Search gibt die Suchergebnisse pro vom Benutzer eingegebenen Begriffen zurück, zusammen mit Aktualisierungen an der Facettennavigationsstruktur. In Azure Search ist die Facettennavigation eine einstufige Konstruktion mit Facetwerten und Werten für die Anzahl der für jeden einzelnen Wert gefundenen Ergebnisse.

Die Darstellungsschicht in Ihrem Code stellt die Benutzeroberfläche bereit. Sie sollte die Bestandteile der Facettennavigation auflisten, wie z. B. die Bezeichnung, Werte, Kontrollkästchen und die Anzahl. Die REST-API von Azure Search ist plattformunabhängig, weshalb Sie eine beliebige, von Ihnen bevorzugte Sprache und Plattform verwenden können. Wichtig ist hierbei, dass Sie Benutzeroberflächenelemente (UI) aufnehmen, die eine inkrementelle Aktualisierung unterstützen, in deren Verlauf der UI-Zustand mit zunehmender Auswahl von Facets aktualisiert wird. 

In den folgenden Abschnitten sehen wir uns genauer an, wie jeder Teil erstellt wird, beginnend mit der Darstellungsschicht.

<a name="presentationlayer"></a>
# Erstellen der Darstellungsschicht #

Wenn wir uns von der Darstellungsschicht aus rückwärts arbeiten, kann dies hilfreich sein, um die Anforderungen offenzulegen, die Ihnen andernfalls entgehen könnten, und zu verstehen, welche Funktionen für die Suchoberfläche unverzichtbar sind.

Unter dem Aspekt der Facettennavigation zeigt Ihre Web- oder Anwendungsseite die Facettennavigationsstruktur an, erkennt Benutzereingaben auf der Seite und fügt die geänderten Elemente ein. 

Für Webanwendungen wird gängigerweise AJAX in der Darstellungsschicht verwendet, da es Ihnen die Aktualisierung inkrementeller Änderungen gestattet. Sie können auch ASP.NET MVC oder jede andere Visualisierungsplattform verwenden, die mit dem Azure Search-Dienst eine Verbindung über HTTP herstellen kann. Die Beispielanwendung, auf die im gesamten Artikel verwiesen wird, **AdventureWorks-Katalog**, ist zufälligerweise eine ASP.NET MVC-Anwendung.

Das folgende Beispiel, das aus der Datei **index.cshtml** der Beispielanwendung stammt, erzeugt eine dynamische HTML-Struktur für die Anzeige der Facettennavigation auf Ihrer Seite mit den Suchergebnissen. In dem Beispiel ist die Facettennavigation in die Seite mit den Suchergebnissen integriert und wird angezeigt, nachdem der Benutzer einen Suchbegriff abgesendet hat.

Beachten Sie, dass jedes Facet eine Bezeichnung besitzt (Colors, Categories, Prices), eine Bindung an ein Facettenfeld (Color, categoryName, listPrice) und einen  `.count`-Parameter, mit dem die Anzahl der für dieses Facetergebnis gefundenen Artikel zurückgegeben wird.

  ![][2]
 

> [AZURE.TIP] Denken Sie beim Entwerfen der Seite mit den Suchergebnissen daran, einen Mechanismus zum Löschen der Facets hinzuzufügen. Wenn Sie Kontrollkästchen verwenden, können Benutzer problemlos intuitiv erfassen, wie die Filter gelöscht werden. Bei anderen Layouts benötigen Sie möglicherweise ein Brotkrümelnavigations-Muster oder einen anderen kreativen Ansatz. Beispielsweise können Sie in der Beispielanwendung "AdventureWorks-Katalog" auf den Titel "AdventureWorks Catalog" klicken, um die Suchseite zurückzusetzen.

<a name="buildindex"></a>
# Erstellen des Indexes #

Facets werden im Index mithilfe des folgenden Indexattributs feldweise aktiviert: `"Facetable": true`.  
Alle Feldtypen, die möglicherweise in der Facettennavigation verwendet werden könnten, sind standardmäßig  `Facetable`. Diese Feldtypen umfassen  `Edm.String`,  `Edm.DateTimeOffset` und alle numerischen Feldtypen (im Wesentlichen sind alle Feldtypen facetfähig ("facetable"), außer  `Edm.GeographyPoint`, der nicht in der Facettennavigation verwendet werden kann). 

Beim Erstellen eines Indexes ist es bei der Facettennavigation eine bewährte Methode, die Facetfunktion für Felder explizit zu deaktivieren, die nie als Facet verwendet werden sollen.  Insbesondere sollten Zeichenfolgenfelder für Singleton-Werte, z. B. eine ID oder einen Produktnamen, auf `"Facetable": false` festgelegt werden, um ihre versehentliche (und ineffektive) Verwendung in einer Facettennavigation zu verhindern.

Im Folgenden finden Sie das Schema für die "AdventureWorks-Katalog"-Beispiel-App (um einige Attribute gekürzt, um die Gesamtgröße zu verringern):

 ![][3]
 
Beachten Sie, dass  `Facetable` für Zeichenfolgenfelder, die nicht als Facets verwendet werden sollen (z. B. eine ID oder ein Name), deaktiviert ist. Das Deaktivieren der Facetfunktion an Stellen, wo sie nicht benötigt wird, hilft dabei, die Größe des Indexes klein zu halten, und verbessert im Allgemeinen die Leistung.

> [AZURE.TIP] Als bewährte Methode nehmen Sie den vollständigen Satz von Indexattributen für jedes Feld auf. Zwar ist  `Facetable` standardmäßig für fast alle Felder aktiviert, doch kann das absichtliche Festlegen jedes Attributs Ihnen dabei helfen, die Auswirkungen jeder einzelnen Schemaentscheidung zu durchdenken. 

<a name="checkdata"></a>
# Überprüfen der Datenqualität 

Beim Entwickeln einer beliebigen datenzentrierten Anwendung nimmt die Vorbereitung der Daten häufig einen größeren Teil der zu erledigenden Arbeiten ein. Suchanwendungen sind dabei keine Ausnahme. Die Qualität Ihrer Daten hat direkte Auswirkungen darauf, ob sich die Facettennavigationsstruktur so darstellt, wie Sie es beabsichtigen, sowie darauf, ob Sie effektive Hilfe beim Erstellen von Filtern erhalten, die das Resultset verkleinern.

In Azure Search wird das Suchkorpus aus Dokumenten gebildet, die einen Index auffüllen. Dokumente stellen die Werte bereit, die zum Berechnen von Facets verwendet werden. Wenn Sie nach Marke oder Preis facettieren möchten, sollte jedes Dokument Werte für  *BrandName* und  *ProductPrice* enthalten, die gültig, konsistent und produktiv wie eine Filteroption sind.

Hier nun noch mal ein paar Hinweise daran, was Sie bedenken oder optimieren sollten:

- Fragen Sie sich bei jedem Feld, nach dem Sie facettieren möchten, ob es Werte enthält, die als Filter in einer selbstgesteuerten Suche geeignet sind. Die Werte sollten kurz, aussagekräftig und ausreichend unterscheidbar sein, um eine klare Auswahl zwischen gleichwertigen Optionen zu bieten.
- Schreibfehler und nahezu übereinstimmende Werte. Wenn Sie nach Farbe facettieren und die Feldwerte "Orange" und "Ornage" (ein Schreibfehler) umfassen, würde ein basierend auf dem Feld "Farbe" basierendes Facet beide erfassen.
- Text mit gemischter Groß-/Kleinschreibung kann ebenfalls beträchtliche Verwirrung in der Facettennavigation anrichten, indem "orange" und "Orange" als zwei unterschiedliche Werte vorkommen. 
- Singular- und Pluralformen desselben Werts können zu einem gesonderten Facet für jeden der beiden Werte führen.

Wie Sie sich vorstellen können, stellt Sorgfalt bei der Vorbereitung der Daten einen wesentlichen Aspekt einer effektiven Facettennavigation dar.

<a name="buildquery"></a>
# Erstellen der Abfrage #

Der Code, den Sie zum Erstellen von Abfragen schreiben, sollte alle Teile einer gültigen Abfrage angeben, einschließlich Suchausdrücken, Facets, Filtern und Bewertungsprofilen - alles, was zum Formulieren einer Anforderung verwendet wird. In diesem Abschnitt untersuchen wir, an welcher Stelle Facets sich in eine Abfrage eignen und wie Filter mit Facets verwendet werden, um ein verkleinertes Resultset zu liefern.

Ein Beispiel ist häufig ein guter Ausgangspunkt. Das folgende Beispiel, das aus der Datei **CatalogSearch.cs** stammt, erzeugt eine Anforderung, die eine Facettennavigation auf Grundlage von Color, Category und Price erstellt. 

Beachten Sie, dass Facets in dieser Beispielanwendung von grundlegender Bedeutung sind. Die Suchfunktion im AdventureWorks-Katalog wurde um Facettennavigation und Filter herum entworfen. Dies zeigt sich deutlich an der hervorstechenden Platzierungen der Facettennavigation auf der Seite. Die Beispielanwendung umfasst URI-Parameter für Facets (color, category, prices) als Eigenschaften in der Search-Methode (wie in der Beispielanwendung erstellt).

  ![][4]
 
Ein Facetabfrageparameter wird für ein Feld festgelegt und kann abhängig vom Datentyp durch eine mit Kommas getrennte Liste, die  `count:<integer>`,  `sort:<>`,  `intervals:<integer>` und  `values:<list>` enthält, noch weiter parametrisiert werden. Beim Einrichten von Bereichen wird eine Werteliste für numerische Daten unterstützt. Details zur Verwendung finden Sie unter [Durchsuchen von Dokumenten (Azure Search-API)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Zusammen mit Facets sollte die Anforderung, die von Ihrer Anwendung formuliert wird, auch Filter erstellen, um die Gruppe von Kandidatendokumenten auf Grundlage einer Facetwertauswahl einzuschränken. Für ein Fahrradgeschäft liefert die Facettennavigation Hinweise zu Fragen wie "Welche Farben, Hersteller und Typen von Fahrrädern sind verfügbar", während das Filtern Fragen wie "Exakt welche Fahrräder sind rote Mountainbikes in diesem Preissegment" beantwortet.

Klickt ein Benutzer auf "Red", um anzugeben, dass nur rote (Red) Produkte angezeigt werden sollen, würde die nächste von der Anwendung gesendete Abfrage `$filter=Color eq 'Red'`.

## Bewährte Methoden für die Facettennavigation ##

In der folgende Liste werden einige bewährte Methoden zusammengefasst.

- **Genauigkeit**<br/>
Verwenden Sie Filter. Wenn Sie sich nur auf Suchausdrücke verlassen, könnte die Wortstammerkennung dafür sorgen, dass ein Dokument zurückgegeben wird, das nicht den exakten Facetwert in einem seiner Felder aufweist. 

- **Zielfelder**<br/>
Beim Facettendrilldown möchten Sie normalerweise nur Dokumente aufnehmen, bei denen der Facetwert in einem bestimmten (facettierten) Feld vorkommt, also nicht irgendwo in einem aller durchsuchbaren Felder. Das Hinzufügen eines Filters verstärkt das Zielfeld, indem der Dienst angewiesen wird, nur in dem Facettenfeld nach einem übereinstimmenden Wert zu suchen.

- **Indexeffizienz**<br/>
Wenn Ihre Anwendung ausschließlich Facettennavigation verwendet (d. h. kein Suchfeld), können Sie das Feld als  `searchable=false`,  `facetable=true` markieren, um einen kompakteren Index zu erzeugen. Außerdem erfolgt die Indizierung nur auf vollständige Facetwerte, ohne Wörtertrennung oder Indizierung der Komponententeile eines aus mehreren Wörtern bestehenden Werts.

- **Leistung**<br/>
Filter grenzen den Satz von Kandidatendokumenten für die Suche ein und schließen Sie aus der Bewertung aus. Wenn Sie eine große Anzahl von Dokumenten haben, liefert Ihnen die Verwendung eines sehr selektiven Facetdrilldowns eine häufig bedeutend bessere Leistung.


<a name="tips"></a> 
# Tipps zur Steuerung der Facettennavigation #

Im folgenden finden Sie ein Blatt mit Tipps zur Anleitung bei bestimmten Problemen.

**Hinzufügen von Bezeichnungen zu jedem Feld in der Facettennavigation**

Bezeichnungen werden normalerweise in HTML oder im Formular definiert (**index.cshtml** in der Beispielanwendung). In Azure Search gibt es keine API für Facettennavigationsbezeichnungen oder eine andere Art von Metadaten.

**Definieren der als Facets zu verwendenden Felder**

Denken Sie daran, dass das Schema des Indexes bestimmt, welche Felder zur Verwendung als Facets zur Verfügung stehen. Angenommen, ein Feld ist facetfähig, dann gibt die Abfrage an, nach welchen Feldern facettiert werden soll. Das Feld, nach dem Sie facettieren, stellt die Werte bereit, die unter der Bezeichnung angezeigt werden. 

Die Werte, die unter jeder Bezeichnung angezeigt werden, werden aus dem Index abgerufen. Wenn das Facetfeld beispielsweise  *Color* ist, sind die für die zusätzliche Filterung verfügbaren Werte die Werte für dieses Feld (Red, Black usw.).

Nur für "Numeric"- und "DateTime"-Werte können Sie explizit Werte für das Facetfeld festlegen (z. B. 
`facet=Rating,values:1|2|3|4|5`). Eine Werteliste ist für diese Feldtypen zulässig, um die Trennung von Facetergebnisse in zusammenhängende Bereiche zu vereinfachen (entweder Bereiche auf Grundlage numerischer Werte oder Zeiträume). 

**Kürzen von Facetergebnissen**

Facetergebnisse sind in den Suchergebnissen gefundene Dokumente, die einem Facetbegriff entsprechen. Im folgenden Beispiel haben in den Suchergebnissen für  *cloud computing* 254 Artikel auch  *internal specification* als Inhaltstyp. Artikel schließen sich nicht notwendigerweise gegenseitig aus. Wenn ein Artikel die Kriterien beider Filter erfüllt, wird er in beiden gezählt. Dies ist bei der Facettierung auf  `Collection(Edm.String)`-Felder möglich, die häufig zur Implementierung der Dokumentenmarkierung verwendet werden.

		Search term: "cloud computing"
		Content type
		   Internal specification (254)
		   Video (10) 

Im Allgemeinen empfehlen wir, wenn Sie feststellen, dass Facet-Ergebnisse dauerhaft zu groß sind, dass Sie weitere Filter hinzufügen, wie in den vorherigen Abschnitten erläutert, um den Benutzern Ihrer Anwendung mehr Optionen zum Eingrenzen der Suche bereitzustellen.

**Begrenzen der Artikel in der Facettennavigation**

Für jedes Facettenfeld in der Navigationsstruktur gibt es einen standardmäßigen Grenzwert von 10 Werten. Diese Standardeinstellung ist sinnvoll für Navigationsstrukturen, da es die Werteliste in einer verwaltbaren Größe hält. Sie können den Standardwert durch Zuweisen eines Werts zu "count" außer Kraft setzen.

- `&facet=city,count:5` gibt an, dass nur die ersten fünf Städte, die in den bestbewerteten Ergebnissen gefunden werden, als Facetergebnis zurückgegeben werden. Angenommen, wir haben einen Suchbegriff "airport" und 32 Übereinstimmungen. Wenn die Abfrage `&facet=city,count:5` angibt, werden nur die ersten fünf eindeutigen Städte mit den meisten Dokumenten in den Suchergebnissen in die Facetergebnisse aufgenommen.

Beachten Sie die Unterscheidung zwischen Facetergebnissen und Suchergebnissen. Suchergebnisse sind alle Dokumente, die der Abfrage entsprechen. Facetergebnisse sind die Übereinstimmungen für jeden Facetwert. In dem Beispiel enthalten die Suchergebnisse Städtenamen, die nicht in der Facetklassifikationsliste enthalten sind (in unserem Beispiel 5). Ergebnisse, die mittels Facettennavigation ausgefiltert werden, werden dem Benutzer angezeigt, wenn er Facets deaktiviert oder neben "City" andere Facets auswählt. 

> [AZURE.NOTE] Die Behandlung von  `count` kann verwirrend sein, wenn mehr als ein Typ vorhanden ist. Die folgende Tabelle bietet eine kurze Zusammenfassung der Verwendungsarten des Begriffs in der Azure Search-API, in Beispielcode und in der Dokumentation. 

- `@colorFacet.count`<br/>
Im Darstellungscode sollte für das Facet ein "count"-Parameter angezeigt werden, der verwendet wird, um die Anzahl der Facetergebnisse anzuzeigen. In Facetergebnissen gibt "count" die Anzahl der Dokumente an, die mit dem Facetbegriff oder -bereich übereinstimmen.

- `&facet=City,count:12`<br/>
In einer Facetabfrage können Sie "count" auf einen Wert festlegen.  Der Standardwert ist 10, aber Sie können ihn auf einen höheren oder niedrigeren Wert festlegen. Durch das Festlegen von `count:12` werden die obersten 12 Übereinstimmungen in den Facetergebnissen nach Dokumentanzahl abgerufen.

- "`@odata.count`"<br/>
In der Abfrageantwort gibt dieser Wert die Anzahl der übereinstimmenden Artikel in den Suchergebnissen an. Im Durchschnitt ist er größer als die Summe aller kombinierten Facetergebnisse, was am Vorhandensein von Artikeln liegt, die zwar dem Suchbegriff entsprechen, aber keine Facetwertübereinstimmungen besitzen.


**Ebenen der Facettennavigation**

Wie bereits erwähnt, besteht keine direkte Unterstützung für das Schachteln von Facets in einer Hierarchie. Standardmäßig unterstützt die Facettennavigation nur eine Ebene von Filtern. Es gibt jedoch Problemumgehungen hierfür. Sie können eine hierarchische Facetstruktur in einer  `Collection(Edm.String)` mit einem Einstiegspunkt pro Hierarchie codieren. Die Implementierung dieser Problemumgehung ist nicht Gegenstand dieses Artikels, aber Sie erhalten Informationen über Auflistungen unter [OData anhand eines Beispiels](http://msdn.microsoft.com/library/ff478141.aspx). 

**Überprüfen von Feldern**

Wenn Sie die Liste der Facets dynamisch auf Grundlage von nicht vertrauenswürdigen Benutzereingaben erstellen, sollten Sie entweder überprüfen, ob die Namen der Facettenfelder gültig sind, oder die Namen beim Erstellen von URLs entweder mithilfe von  `Uri.EscapeDataString()` in .NET oder mit der Entsprechung auf der Plattform Ihrer Wahl mit Escapezeichen versehen.

**Anzahlwerte in Facetergebnissen**

Beim Hinzufügen eines Filters zu einer Facettenabfrage möchten Sie möglicherweise die Facetanweisung beibehalten (z. B. `facet=Rating&$filter=Rating ge 4`). Technisch gesehen ist "facet=Rating" nicht erforderlich, aber durch seine Beibehaltung werden die Anzahlwerte von Facetwerten für die Bewertungen 4 und höher zurückgegeben. Wenn ein Benutzer z. B. auf "4" klickt und die Abfrage einen Filter für größer als oder gleich "4" enthält, werden für jede Bewertung von 4 und höher Anzahlwerte zurückgegeben.  

**Auswirkungen von Sharding auf Anzahlwerte von Facets**

Unter bestimmten Umständen kann es vorkommen, dass die Anzahlwerte von Facets nicht mit den Resultsets übereinstimmen (siehe [Facettennavigation in Azure Search (Forumsbeitrag)](https://social.msdn.microsoft.com/Forums/azure/06461173-ea26-4e6a-9545-fbbd7ee61c8f/faceting-on-azure-search?forum=azuresearch)).

Anzahlwerte von Facets können aufgrund der Shardingarchitektur ungenau sein. Jeder Suchindex hat mehrere Shards, und jedes einzelne meldet die obersten N Facets nach Dokumentanzahl, die dann in einem einzigen Ergebnis kombiniert werden. Wenn einige Shards viele übereinstimmende Werte aufweisen, während andere weniger haben, stellen Sie möglicherweise fest, dass einige Facetwerte fehlen oder in den Ergebnissen mit zu niedrigen Werten ausgewiesen werden.

Obwohl sich dieses Verhalten jederzeit ändern kann, können Sie dieses, wenn es im Moment bei Ihnen auftritt, umgehen, indem Sie die "count:<Zahl>" künstlich auf einen sehr großen Wert erhöhen, um vollständige Meldungen aus jedem Shard zu erzwingen. Wenn der Wert von "count:" größer als oder gleich der Anzahl eindeutiger Werte im Feld ist, erhalten Sie garantiert genaue Ergebnisse. Wenn aber die Anzahl der Dokumente äußerst hoch ist, kommt es zu Leistungseinbußen, weshalb Sie diese Option sehr bedacht verwenden sollten.

<a name="rangefacets"></a>
# Auf einem Wertebereich basierende Facettennavigation #

Facettierung über Bereiche ist eine häufige Anforderung an Suchanwendungen. Bereiche werden für numerische Daten und DateTime-Werte unterstützt. Weitere Informationen zu den einzelnen Ansätzen finden Sie unter [Durchsuchen von Dokumenten (Azure Search-API)](http://msdn.microsoft.com/library/azure/dn798927.aspx).

Azure Search vereinfacht das Erstellen von Bereichen durch die Bereitstellung von zwei Methoden für die Berechnung eines Bereichs. Bei beiden Methoden erstellt Azure Search die entsprechenden Bereiche anhand der von Ihnen bereitgestellten Eingaben. Wenn Sie beispielsweise die Bereichswerte "10|20|30" angeben, werden automatisch Bereiche von 0-10, 10-20 und 20-30 erstellt. Die Beispielanwendung entfernt alle Intervalle, die leer sind. 

**Methode 1: Verwenden der Intervallparameter**<br/>
Um price-Facets in Schritten von 10 $ festzulegen, würden Sie Folgendes angeben: `&facet=price,interval:10`


**Methode 2: Verwenden einer Werteliste**<br/>
Für numerische Daten können Sie eine Werteliste verwenden.  Betrachten Sie den Facetbereich für listPrice, der wie folgt dargestellt wird:

  ![][5]

Der Bereich ist in der Datei **CatalogSearch.cs** mithilfe einer Werteliste angegeben:

    facet=listPrice,values:10|25|100|500|1000|2500

Jeder Bereich wird mit dem Wert 0 als Ausgangspunkt sowie einem Wert aus der Liste als Endpunkt erstellt und anschließend um den vorherigen Bereich gekürzt, um diskrete Intervalle zu erstellen. Azure Search erledigt dies als Teil der Facettennavigation. Sie müssen zur Strukturierung der einzelnen Intervalle keinen Code schreiben.

### Erstellen eines Filters für Facetbereiche ###

Um Dokumente basierend auf einem vom Benutzer ausgewählten Bereich zu filtern, können Sie die Filteroperatoren "`ge`" und "`lt`" in einem zweiteiligen Ausdruck verwenden, der die Endpunkte des Bereichs definiert. Wenn der Benutzer z. B. den Bereich 10-25 auswählt, wäre der Filter `$filter=listPrice ge 10 and listPrice lt 25`.

In der Beispielanwendung verwendet der Filterausdruck die Parameter **priceFrom** und **priceTo**, um die Endpunkte festzulegen. Die **BuildFilter**-Methode in **CatalogSearch.cs** enthält den Filterausdruck, der Ihnen die Dokumente innerhalb eines Bereichs liefert.

  ![][6]

<a name="geofacets"></a> 
# Auf GeoPoints basierende Filternavigation #

Sie treffen häufig auf Filter, die Ihnen bei der Auswahl eines Geschäfts, Restaurants oder Ziels behilflich sind, basierend auf der Nähe zu Ihrem aktuellen Standort. Während diese Art von Filter wie Facettennavigation aussehen könnte, handelt es sich aber tatsächlich lediglich um einen Filter. Wir erwähnen dies hier für diejenigen unter Ihnen, die speziell an Ratschlägen zur Implementierung dieses speziellen Designproblems interessiert sind.

Es gibt zwei Geofunktionen in Azure Search, nämlich **geo.distance** und **geo.intersects**.

- Die Funktion **geo.distance** gibt die Entfernung zwischen zwei Punkten in Kilometern zurück, wobei einer ein Feld und der andere eine Konstante ist, die als Teil des Filters übergeben wird. 

- Die Funktion **geo.intersects** gibt "true" zurück, wenn sich ein bestimmter Punkt innerhalb eines bestimmten Polygons befindet, wobei der Punkt ein Feld ist, und das Polygon als eine Konstantenliste aus Koordinaten angegeben wird, die als Teil des Filters übergeben wird.

Filterbeispiele finden Sie unter [OData-Ausdruckssyntax (Azure Search)](http://msdn.microsoft.com/library/azure/dn798921.aspx). Weitere Informationen zur Geosuche finden Sie unter [Erstellen einer Geosuchanwendung in Azure Search]search-create-geospatial.md

<a name="tryitout"></a>
# Ausprobieren #

Die AdventureWorks-Demo von Azure Search auf Codeplex enthält die Beispiele, auf die in diesem Artikel verwiesen wird. Achten Sie, wenn Sie mit Suchergebnissen arbeiten, bei der Abfrageerstellung auf Änderungen in der URL. Diese Anwendung fügt Facets an den URI an, während Sie diese einzeln auswählen.

1.	Konfigurieren Sie die Beispielanwendung (Anleitungen finden Sie unter [Erstellen Ihrer ersten Anwendung](search-create-first-solution.md)). 

	Beachten Sie das Schema, das in der Datei "Program.cs" des CatalogIndexer-Projekts definiert ist. Es gibt facetfähige Felder für color, listPrice, size, weight, categoryName und modelName an.  Nur einige davon (color, listPrice, categoryName) werden tatsächlich in der Facettennavigation implementiert.

3.	Führen Sie die Anwendung aus. 

	Zunächst wird nur das Suchfeld angezeigt. Sie können sofort auf die Schaltfläche "Suchen" klicken, um alle Ergebnisse zu erhalten, oder einen Suchbegriff eingeben.

	![][7]
 
4.	Geben Sie einen Suchbegriff ein, z. B. "bike", und klicken Sie auf **Suchen**. Die Abfrage wird schnell ausgeführt.
 
	Zusammen mit den Suchergebnissen wird außerdem eine Facettennavigationsstruktur zurückgegeben.  In der URL sind Facets für Colors, Categories und Prices null. Auf der Seite mit den Suchergebnissen enthält die Facettennavigationsstruktur Anzahlwerte für jedes Facetergebnis.

	 ![][8]
 
5.	Klicken Sie auf eine Farbe (color), Kategorie (category) und einen Preisbereich (price range). Facets sind bei einer anfänglichen Suche null, aber wenn sie Werte annehmen, werden die Suchergebnisse um Artikel gekürzt, die nicht mehr übereinstimmen. Beachten Sie, dass der URI die Änderungen an der Abfrage übernimmt.

	![][9]
 
6.	Um die Facettenabfrage zu löschen, damit Sie andere Abfrageverhaltensweisen testen können, klicken Sie am oberen Rand der Seite auf **AdventureWorks Catalog**.

	![][10]
 
<a name="nextstep"></a>
# Nächster Schritt #

Um Ihre Kenntnisse zu testen, können Sie ein Facetfeld für  *modelName* hinzufügen. Der Index ist bereits für dieses Facet eingerichtet, sodass keine Änderungen am Index erforderlich sind. Sie müssen allerdings den HTML-Code so ändern, dass er ein neues Facet für "Models" enthält, und das Facetfeld dem Abfragekonstruktor hinzufügen.

Um weitere Einblicke in die Entwurfsprinzipien der Facettennavigation zu erhalten, empfehlen wir die folgenden Links:

- [Entwerfen für Facettensuchen](http://www.uie.com/articles/faceted_search/)
- [Entwurfsmuster: Facettennavigation](http://alistapart.com/article/design-patterns-faceted-navigation)

<!--Anchors-->
[Erstellen der Navigation]: #howtobuildit
[Erstellen der Darstellungsschicht]: #presentationlayer
[Erstellen des Indexes]: #buildindex
[Überprüfen der Datenqualität]: #checkdata
[Erstellen der Abfrage]: #buildquery
[Tipps zur Steuerung der Facettennavigation]: #tips
[Auf einem Wertebereich basierende Facettennavigation]: #rangefacets
[Auf GeoPoints basierende Filternavigation]: #geofacets
[Ausprobieren]: #tryitout

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
[Entwerfen für Facettensuchen]: http://www.uie.com/articles/faceted_search/
[Entwurfsmuster: Facettennavigation]: http://alistapart.com/article/design-patterns-faceted-navigation
[Erstellen Ihrer ersten Anwendung]: search-create-first-solution.md
[OData-Ausdruckssyntax (Azure Search)]: http://msdn.microsoft.com/library/azure/dn798921.aspx
[Erstellen einer Geosuchanwendung in Azure Search]: search-create-geospatial.md
[AdventureWorks-Demo von Azure Search]: https://azuresearchadventureworksdemo.codeplex.com/
[http://www.odata.org/documentation/odata-version-2-0/overview/]: http://www.odata.org/documentation/odata-version-2-0/overview/ 
[Facettierung im Azure Search-Forumsbeitrag]: ../faceting-on-azure-search.md?forum=azuresearch
[Durchsuchen von Dokumenten (Azure Search-API)]: http://msdn.microsoft.com/library/azure/dn798927.aspx



<!--HONumber=54--> 