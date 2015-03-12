<properties 
	pageTitle="Verwenden von Beispieldatasets in Machine Learning Studio | Azure" 
	description="Beschreibungen der in Azure Machine Learning Studio enthaltenen Beispieldatasets" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/16/2014" 
	ms.author="garye"/>


# Verwenden von Beispieldatasets in Azure Machine Learning Studio

ML Studio beinhaltet einige Beispieldatasets, die Sie verwenden können. Dies sind Standarddatasets für das maschinelle Lernen von einer öffentlichen Domäne. 

Sie können diese Datasets in Ihren Experimenten verwenden, während Sie verschiedene Arten von Analysemodellen für maschinelles Lernen in ML Studio untersuchen. 

- Klicken Sie auf die Registerkarte **DATASETS**, um eine Liste dieser Standarddatasets anzuzeigen. Für jedes Dataset können Sie Informationen anzeigen, z. B. den Namen und den Übermittler des Datasets sowie eine kurze Beschreibung.
 
    Klicken Sie auf einen beliebige Spaltenüberschrift, um die Datasets zu sortieren. Klicken Sie z. B. auf **GESENDET VON**, um alle von der Microsoft Corporation bereitgestellten Beispieldatasets zu gruppieren. Dies ist außerdem eine einfache Methode, um Datasets anzuzeigen, die von Ihnen und anderen Benutzern in Ihrem Arbeitsbereich importiert wurden. 

- Um ein Dataset in einem Experiment zu verwenden, erweitern Sie den Abschnitt **Gespeicherte Datasets** der Modulpalette auf der linken Seite des Experimentbereichs, oder suchen Sie nach einem bestimmten Dataset, indem Sie den Namen in das Suchfeld über der Palette eingeben. Ziehen Sie das Dataset in den Bereich, um es zum Experiment hinzuzufügen. 

##Beispieldatasets

Die folgende Tabelle enthält zusätzliche Informationen zu Datasets, die Ihnen möglicherweise beim Untersuchen der Funktionen für maschinelles Lernen von ML Studio helfen können. Für jedes Dataset wird in der Tabelle Folgendes aufgelistet:
 
- Der Name und die Quelle des einzelnen Datasets.
- Eine Beschreibung zu möglichen Einsatzzwecken für das Dataset sowie Anführungen in Forschungen zum maschinellen Lernen, in denen das Dataset verwendet wurde.
- Eine Zusammenfassung wichtiger im Dataset enthaltener Spalten und weitere hilfreiche Attribute.

Einige Beschreibungen wurden aus der von der Quelle bereitgestellten Dokumentation übernommen (in der Regel [UC Irvine Machine Learning Repository](http://archive.ics.uci.edu/ml/ "UC Irvine Machine Learning Repository")), während andere Beschreibungen auf zusätzlichen Untersuchungen basieren oder Änderungen in diesen Beispielen widerspiegeln.

<table>
<tr valign=top>
<th>Datasetname</th>
<th>Datasetbeschreibung</th>
<th>Zugehörige Untersuchungen</th>
</tr>
<tr valign=top>
<td>Binäres Klassifizierungsdataset der Einkommenserhebung für Erwachsene</td>
<td>
<p>Eine Teilmenge der Volkszählungsdatenbank von 1994, die arbeitende Erwachsene (älter als 16 Jahre) mit einem bereinigten Einkommensindex verwendet, der über 100 liegt.</p>
<p><strong>Usage:</strong> Klassifizierung von Personen mithilfe von demografischen Daten für die Vorhersage, ob eine Person mehr als 50.000 pro Jahr verdient.</p>
</td>
<td>
<p>Kohavi, R., Becker, B., (1996). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Fahrzeugpreisdaten (unformatiert)</p>
</td>
<td>
<p>Informationen zu Automobilen nach Marke und Modell, einschließlich Preis und Merkmalen wie Zylinderanzahl und Verbrauch sowie einer Risikoeinstufung der Versicherung.</p>
<p>Die Risikoeinstufung wird anfänglich dem Fahrzeugpreis zugeordnet und dann dem tatsächlichen Risiko in einem Prozess <legacyItalic></legacyItalic>angepasst. Der Wert +3 weist auf ein Fahrzeug mit hohem Risiko hin, während der Wert -3 auf ein voraussichtlich sicheres Fahrzeug hinweist.</p>
<p><strong>Usage:</strong> Vorhersage der Risikoeinstufung nach Merkmalen unter Verwendung der Regression oder multivariaten Klassifizierung.</p>
</td>
<td>
<p>
Schlimmer, J.C. (1987). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Blutspendedaten</p>
</td>
<td>
<p>Eine Teilmenge der Daten aus der Blutspendedatenbank des Blood Transfusion Service Centers von Hsin-Chu City, Taiwan.</p>
  <p>Die Spenderdaten beinhalten die Anzahl der Monate seit der letzten Spende, die Häufigkeit oder Gesamtanzahl der Spenden, der seit der letzten Spende vergangenen Zeit sowie die Menge des gespendeten Bluts.</p>
  <p><strong>Usage:</strong> Das Ziel ist die Vorhersage über die Klassifizierung, ob der Spender im März 2007 Blut gespendet hat, wobei "1" auf einen Spender im Zielzeitraum und "0" auf eine Person hinweist, die nicht gespendet hat.</p>
</td>
<td>
<list class="bullet">
<listItem>
<p>
Yeh, I.C., (2008). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</listItem>
<listItem>
<p>
Yeh, I-Cheng, Yang, King-Jang und Ting, Tao-Ming, "Knowledge discovery on RFM model using Bernoulli sequence", Expert Systems with Applications, 2008, [<a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>]
</p>
</listItem>
</list>
</td>
</tr>

<tr valign=top>
<td>
<p>Brustkrebsdaten</p>
</td>
<td>
<p>Eines von drei krebsbezogenen Datasets, das vom Onkologieinstitut bereitgestellt wurde, das häufig in Dokumenten zum maschinellen Lernen verwendet wird. Kombiniert Diagnoseinformationen mit Merkmalen der Laboranalyse von über 300 Gewebeproben.</p>
<p><strong>Usage:</strong> Klassifizierung des Krebstyps auf Basis von neun Attributen, von denen einige linear, andere kategorisch sind.</p>
</td>
<td>
<p>
Wohlberg, W.H., Street, W.N., & Mangasarian, O.L. (1995). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Daten zur Energieeffizienzregression</p>
</td>
<td>
<p>Eine Sammlung von simulierten Energieprofilen, die auf 12 verschiedenen Gebäudeformen basieren. Die Gebäude werden anhand von acht Merkmalen wie Verglasungsfläche, Verglasungsflächenverteilung und -ausrichtung unterschieden.</p>
<p><strong>Usage:</strong> Verwendung der Regression oder Klassifizierung, um die Energieeffizienzbewertung vorherzusagen, die die Grundlage für eine von zwei Realwertantworten bildet. Für die Multiklassen-Klassifizierung wird die Antwortvariable auf die nächste ganze Zahl gerundet.</p>
</td>
<td>
<p>
Xifara, A. & Tsanas, A. (2012). UCI Machine Learning Repository  [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Planmäßige Durchführung von Flügen (unformatiert)</p>
</td>
<td>
<p>Datensätze zu Ankunft und Abflug von Flügen innerhalb der USA von Oktober 2011.</p>
<p><strong>Usage:</strong> Vorhersage von Verspätungen im Flugverkehr.</p>
</td>
<td>
<p>
Vom US Dept. of Transportation [<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&amp;DB_Short_Name=On-Time</a>]
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Waldbranddaten</p>
</td>
<td>
<p>Enthält Wetterdaten (wie Temperatur- und Luftfeuchtigkeitsindizes sowie Windgeschwindigkeit) aus einem Gebiet im Nordosten von Portugal, die mit Datensätzen von Waldbränden kombiniert wurden.</p>
<p><strong>Usage:</strong> Dies ist eine schwierige Regressionsaufgabe, bei der die verbrannte Fläche von Waldbränden vorhergesagt werden soll.</p>
</td>
<td>
<list class="bullet">
<listItem>
<p>
Cortez, P., & Morais, A. (2008). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</listItem>
<listItem>
<p>
[Cortez and Morais, 2007] P. Cortez und A. Morais. 
A Data Mining Approach to Predict Forest Fires using Meteorological Data. 
In J. Neves, M. F. Santos und J. Machado Eds., New Trends in Artificial Intelligence, 
Protokoll der 13. EPIA 2007 - Portugiesische Konferenz zur künstlichen Intelligenz, 
Dezember, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Verfügbar unter:
[<a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>]
</p>
</listItem>
</list>
</td>
</tr>

<tr valign=top>
<td>
<p>Zwei-Klassen-Daten zu Schwertlilien</p>
</td>
<td>
<p>Dies ist wahrscheinlich die bekannteste Datenbank, die in der Literatur zur Mustererkennung gefunden werden kann. Das Dataset ist relativ klein und enthält 50 Proben, die jeweils Blütenblatteinstufungen von drei Unterarten der Schwertlilie enthalten.</p>
<p><strong>Usage:</strong> Vorhersage der Schwertlilienart aus diesen Einstufungen. 
<!-- I believe the following doesn't apply anymore, but I'm not sure so I'll remove it for now.
One class is linearly separable from the other two; but the latter are not linearly separable from each other.
-->
</p>
</td>
<td>
<p>
Fisher, R.A. (1988). UCI Machine Learning Repository  [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Verbrauchsdaten für verschiedene Fahrzeuge</p>
</td>
<td>
<p>Dieses Dataset ist eine leicht abgewandelte Version des Datasets, das von der StatLib-Bibliothek der Carnegie Mellon University bereitgestellt wurde. Das Dataset wurde 1983 in der Ausführung der American Statistical Association verwendet.</p>
<p>Die Daten listen den Kraftstoffverbrauch für verschiedene Automobile zusammen mit anderen Informationen auf, z. B. Anzahl der Zylinder, Hubraum, Leistung, Gesamtgewicht und Beschleunigung.</p>
<p><strong>Usage:</strong> Vorhersage der Kraftstoffeinsparung auf Basis von drei mehrwertigen Attributen und fünf kontinuierlichen Attributen.</p>
</td>
<td>
<p>
StatLib, Carnegie Mellon University, (1993). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Dataset für binäre Klassifizierung der Diabetes bei Pima-Indianern</p>
</td>
<td>
<p>Eine Teilmenge der Daten der Datenbank des National Institute of Diabetes and Digestive and Kidney Diseases. Das Dataset wurde gefiltert, um sich auf weibliche Patienten zu konzentrieren, die von den Pima-Indianern abstammen. Die Daten umfassen medizinische Daten wie Zucker- und Insulinspiegel sowie Lebensstilfaktoren.</p>
<p><strong>Usage:</strong> Vorhersage, ob der Patient Diabetes hat (binäre Klassifikation).</p>
</td>
<td>
<p>
Sigillito, V. (1990). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Daten zu Restaurantkunden</p>
</td>
<td>
<p>Eine Sammlung von Metadaten zu Kunden, einschließlich Demografie und Präferenzen.</p>
<p><strong>Usage:</strong> Verwendung dieses Datasets in Kombination mit den anderen beiden Restaurantdatasets, um ein Empfehlungssystem zu trainieren und zu testen.</p>
</td>
<td>
<p>
Bache, K. und Lichman, M. (2013). UCI Machine Learning Repository 
[<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>].
Irvine, CA: University of California, School of Information and Computer Science.
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Daten zu Restaurantmerkmalen</p>
</td>
<td>
<p>Eine Sammlung von Metadaten zu Restaurants und ihren Merkmalen wie Speisetyp, Stil und Lage.</p>
<p><strong>Usage:</strong> Verwendung dieses Datasets in Kombination mit den anderen beiden Restaurantdatasets, um ein Empfehlungssystem zu trainieren und zu testen.</p>
</td>
<td>
<p>
Bache, K. und Lichman, M. (2013). UCI Machine Learning Repository 
[<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>].
Irvine, CA: University of California, School of Information and Computer Science.
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Restaurantbewertungen</p>
</td>
<td>
<p>Enthält von Benutzern abgegebene Bewertungen für Restaurants auf einer Skala von 0 bis 2.</p>
<p><strong>Usage:</strong> Verwendung dieses Datasets in Kombination mit den anderen beiden Restaurantdatasets, um ein Empfehlungssystem zu trainieren und zu testen.</p>
</td>
<td>
<p>
Bache, K. und Lichman, M. (2013). UCI Machine Learning Repository 
[<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>].
Irvine, CA: University of California, School of Information and Computer Science.
</p>
</td>
</tr>

<tr valign=top>
<td>
<p>Multiklassen-Dataset zur Stahlhärtung</p>
</td>
<td>
<p>Dieses Dataset enthält eine Reihe von Datensätzen aus Stahlhärtungsversuchen mit den physikalischen Attributen (Breite, Stärke, Typ) der sich ergebenden Stahltypen.</p>
<p><strong>Usage:</strong> Vorhersage eines von zwei numerischen Klassenattributen: Härte oder Belastbarkeit. Sie können auch Korrelationen zwischen den Attributen analysieren.</p>
<p>Die Stahlsorten folgen einem festgelegten Standard, der von SAE und anderen Organisationen definiert wurde. Sie suchen nach einer bestimmten "Sorte" (die Klassenvariable) und möchten die erforderlichen Werte verstehen.</p>
</td>
<td>
<p>
Sterling, D. und Buntine, W., (NA). UCI Machine Learning Repository  [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
<p>Eine hilfreiche Anleitung zu Stahlsorten:</p>
<p>[<a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf>]
  </p>
</td>
</tr>

<tr valign=top>
<td>
<p>Teleskopdaten</p>
</td>
<td>
<p>Datensätze von hochenergetischen Gammapartikelausbrüchen zusammen mit Hintergrundrauschen, die beide mithilfe eines Monte Carlo-Prozesses simuliert wurden.</p>
<p>Das Ziel der Simulation war die Optimierung der Genauigkeit von bodengestützten atmosphärischen Tscherenkow-Gamma-Teleskopen mithilfe statistischer Methoden, um zwischen gewünschtem Signal (Tscherenkow-Strahlungsschauer) und Hintergrundrauschen (hadronische Schauer, die durch kosmische Strahlung in der oberen Atmosphäre ausgelöst werden) zu unterscheiden.</p>
<p>Die Daten wurden vorverarbeitet, um ein gestrecktes Bündel zu erstellen, bei dem die Längsachse zur Kameramitte ausgerichtet ist. Die Merkmale dieser Ellipse (die häufig als Hillas-Parameter bezeichnet werden) gehören zu den Bildparametern, die zur Unterscheidung verwendet werden können.</p>
<p><strong>Usage:</strong> Vorhersage, ob das Bild eines Schauers ein Signal oder Hintergrundrauschen darstellt.</p>
<p><strong>Hinweise:</strong> Eine einfache Klassifizierungsgenauigkeit ist für diese Daten nicht aussagekräftig, da die Klassifizierung eines Hintergrundereignisses als Signal schlechter ist, als die Klassifizierung eines Signals als Hintergrundrauschen. Für den Vergleich der verschiedenen Klassifizierer sollte das ROC-Diagramm verwendet werden. Die Wahrscheinlichkeit der Annahme eines Hintergrundereignisses als Signal muss unter einem der folgenden Schwellenwerte liegen: 0,01 , 0,02 , 0,05 , 0,1 oder 0,2.</p>
<p>Beachten Sie zudem, dass die Anzahl der Hintergrundereignisse (h, für hadronische Schauer) unterbewertet ist, während bei realen Messungen der Wert "h" oder die Geräuschklasse die Mehrheit der Ereignisse ausmacht.</p>
</td>
<td>
<p>
Bock, R.K. (1995). UCI Machine Learning Repository [<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>]. Irvine, CA: University of California, School of Information and Computer Science
</p>
</td>
</tr>

</table>

Die Datasettypen, die Sie möglicherweise vorfinden, sind nachfolgend aufgelistet.

<table>
<tr>
<th>Datenformat</th>
<th>Beschreibung</th>
</tr>
<tr>
<td>
Mit Trennzeichen getrennte Werte (Comma Separated Values, CSV)
</td>
<td>
Bekanntes Austauschformat für alle Datenplattformen. ML Studio lädt dieses Daten und bezieht eine spaltenbezogene Ratefunktion für den Typ ein, die Metadaten auf Basis der aus den einzelnen Spalten abgefragten Daten anwendet.
</td>
</tr>
<tr>
<td>
Attributbeziehungsdateiformat (Attribute Relationship File Format, ARFF)
</td>
<td>
Von WEKA definiertes Datenformat für maschinelles Lernen. Enthält Metadaten (für nominale, numerische und Zeichenfolgentypen), die direkt in ML Studio-Datentabellen übersetzt werden.
</td>
</tr>
<tr>
<td>
Nur-Text
</td>
<td>
Nur-Text kann mithilfe von nachgelagerten Vorverarbeitungsmodulen gelesen und in Spalten aufgeteilt werden. Das hauptsächlich unterstützte Nur-Text-Format ist JSON.
</td>
</tr>
<tr>
<td>
DotNetTable
</td>
<td>
Die serialisierte Version des primären Containers, der zwischen Modulen in ML Studio übergeben wird (das <i>Dataset</i>).
</td>
</tr>
</table>

<!--HONumber=46--> 
