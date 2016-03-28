<properties
	pageTitle="Cortana Analytics-Lösungsvorlage – Playbook für die Vorhersage des Energiebedarfs | Microsoft Azure"
	description="Lösungsvorlage mit Microsoft Cortana Analytics, mit deren Hilfe Versorgungsunternehmen im Energiesektor den Bedarf vorhersagen können."
	services="cortana-analytics"
	documentationCenter=""
	authors="ireiter"
	manager="ireiter"
	editor="cgronlun"/>

<tags
	ms.service="cortana-analytics"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/24/2016"
	ms.author="ireiter;yijichen;garye"/>

# Cortana Analytics-Lösungsvorlage – Playbook für die Vorhersage des Energiebedarfs  

## Kurzfassung  

Im Laufe der letzten Jahre wurden das Internet der Dinge (Internet of Things, IoT), alternative Energiequellen und Big Data zusammengeführt, um im Bereich der Versorgungsunternehmen und in der Energiebranche ein riesiges Potenzial für neue Entwicklungen zu schaffen. Gleichzeitig haben Versorger und der gesamte Energiesektor beobachten können, dass der Verbrauch zurückgegangen ist und dass die Konsumenten sich bessere Möglichkeiten zur Steuerung ihres Energieverbrauchs wünschen. Aus diesem Grund ist der Druck auf Versorger und Smart Grid-Unternehmen groß, was Innovationen und die Neuausrichtung des Unternehmens betrifft. Außerdem veralten viele Strom- und Versorgungsnetze immer mehr und sind mit hohen Kosten für die Wartung und Verwaltung verbunden. Im letzten Jahr war unser Team mit vielen Aktivitäten in der Energiebranche beschäftigt. Dabei kam es häufig vor, dass Versorger oder unabhängige Softwarehersteller (ISV, Independent Software Vendor) nach Möglichkeiten suchten, um den zukünftigen Energieverbrauch vorherzusagen. Diese Vorhersagen spielen eine wichtige Rolle für die derzeitige und zukünftige Geschäftstätigkeit dieser Unternehmen und sind zur Grundlage verschiedener Anwendungsfälle geworden. Hierzu gehören die Vorhersage der kurz- und langfristigen Stromlast, Handel, Lastenausgleich, Netzoptimierung usw. Big Data und Advanced Analytics-Methoden (AA), z. B. Machine Learning (ML), sind die wichtigsten Hilfsmittel zur Erstellung präziser und zuverlässiger Vorhersagen.

In diesem Playbook sind die geschäftlichen und analytischen Richtlinien zusammengefasst, die für eine erfolgreiche Entwicklung und Bereitstellung einer Lösung zum Vorhersagen des Energiebedarfs benötigt werden. Diese vorgeschlagenen Richtlinien können Versorger, Datenanalysten und Datentechniker bei der Erstellung von vollständig operationalisierten, cloudbasierten Lösungen für die Vorhersage des Bedarfs unterstützen. Für Unternehmen, die noch am Anfang ihres Big Data- und Advanced Analytics-Wegs stehen, kann eine Lösung dieser Art der Ausgangspunkt für eine langfristige Smart Grid-Strategie sein.

>[AZURE.TIP] Unter [Cortana Analytics Lösungsvorlagenarchitektur für die Energienachfrageprognose](cortana-analytics-architecture-demand-forecasting-energy.md) können Sie ein Diagramm herunterladen, das eine Architekturübersicht zu dieser Vorlage enthält.

## Übersicht  

In diesem Dokument werden die geschäftlichen, datenbezogenen und technischen Aspekte der Verwendung von Cortana Analytics und Azure Machine Learning (AML) zur Implementierung und Bereitstellung von Lösungen für die Vorhersage des Energiebedarfs (Energy Forecasting Solutions) beschrieben. Das Dokument besteht aus drei Hauptteilen:

1. Geschäftliche Aspekte  
2. Datenaspekte  
3. Technische Implementierung

Im Teil **Geschäftliche Aspekte** werden die geschäftlichen Aspekte beschrieben, die verinnerlicht und berücksichtigt werden müssen, bevor eine Investitionsentscheidung getroffen wird. Es wird erläutert, wie das vorliegende geschäftliche Problem qualifiziert werden kann, um sicherzustellen, dass Predictive Analytics- und Machine Learning-Maßnahmen wirklich effektiv und anwendbar sind. Im Dokument werden die Grundlagen von Machine Learning und die Nutzung zum Lösen von Problemen bei der Vorhersage des Energiebedarfs erläutert. Es werden die Voraussetzungen und Qualifizierungskriterien anhand eines Anwendungsfalls beschrieben. Als Beispiele werden auch einige Anwendungsfälle und Business Case-Szenarien geschildert.

Daten sind der Hauptbestandteil jeder Machine Learning-Lösung. Im Teil **Datenaspekte** dieses Dokuments werden einige wichtige Aspekte in Bezug auf die Daten behandelt. Es geht um die Art der Daten, die für Vorhersagen des Energiebedarfs benötigt werden, die Anforderungen an die Datenqualität und häufig vorhandene Datenquellen. Außerdem wird beschrieben, wie die Rohdaten genutzt werden, um Datenmerkmale vorzubereiten, die dann als Grundlage für die Modellerstellung dienen.

Im dritten Teil des Dokuments wird die **Technische Implementierung** einer Lösung behandelt. Featureentwicklung und -modellierung sind die Kernbestandteile des Data Science-Prozesses und werden daher ausführlicher behandelt. Es wird das Konzept von Webdiensten behandelt, wobei es sich um ein wichtiges Mittel für die Cloudbereitstellung von Predictive Analytics-Lösungen handelt. Außerdem wird eine typische Architektur einer operationalisierten End-to-End-Lösung beschrieben.

Darüber hinaus enthält das Dokument Referenzmaterial, das Sie nutzen können, um den gesamten Bereich und die Technologie besser zu verstehen.

Es ist wichtig zu beachten, dass in diesem Dokument nicht näher auf den Data Science-Prozess und seine mathematischen und technischen Aspekte eingegangen werden soll. Diese Informationen finden Sie unter [Azure ML-Dokumentation](http://azure.microsoft.com/services/machine-learning/) und in den [Blogs](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### Zielgruppe   
Dieses Dokument ist sowohl für Manager als auch für Techniker bestimmt, die sich Wissen in Bezug auf Machine Learning-basierte Lösungen und deren spezielle Verwendung im Bereich der Vorhersage des Energiebedarfs aneignen möchten.

Auch Datenanalysten (Data Scientists) können von der Lektüre dieses Dokuments profitieren, um ein besseres Verständnis des allgemeinen Prozesses zu entwickeln, der der Bereitstellung einer Lösung für die Vorhersage des Energiebedarfs zugrundeliegt. In diesem Zusammenhang können die Informationen auch genutzt werden, um sich eine gute Ausgangsbasis für detaillierteres und weiter gehendes Material zu verschaffen.

### Branchentrends  
Im Laufe der letzten Jahre wurden das Internet der Dinge, alternative Energiequellen und Big Data zusammengeführt, um im Bereich der Versorgungsunternehmen und in der Energiebranche ein riesiges Potenzial für neue Entwicklungen zu schaffen. Gleichzeitig haben Versorger und der gesamte Energiesektor beobachten können, dass der Verbrauch zurückgegangen ist und dass die Konsumenten sich bessere Möglichkeiten zur Steuerung ihres Energieverbrauchs wünschen.

Viele Versorger und Smart Energy-Unternehmen haben Pionierarbeit in Bezug auf das [Smart Grid](https://en.wikipedia.org/wiki/Smart_grid) (Intelligentes Stromnetz) geleistet, indem die vom Netz generierten Daten im Rahmen von Anwendungsfällen genutzt wurden. Bei vielen Anwendungsfällen geht es um die inhärenten Merkmale der Erzeugung von Elektrizität: Sie kann weder bequem gespeichert noch im Lager vorgehalten werden. Energie, die erzeugt wird, muss also genutzt werden. Versorgungsunternehmen, die effizienter werden möchten, müssen den Stromverbrauch schon allein deshalb vorhersagen, weil die **Balance zwischen Bereitstellung und Nachfrage** dann besser erreicht werden kann. So kann die Verschwendung von Energie verhindert und **der Ausstoß von klimaschädlichen Gasen reduziert** werden, und die Kosten können gesenkt werden.

Im Hinblick auf die Kosten ist noch ein weiterer Punkt wichtig, nämlich der Preis. Neue Möglichkeiten des Energiehandels zwischen Versorgungsunternehmen haben zu der dringenden Anforderung geführt, **den zukünftigen Verbrauch und den zukünftigen Preis von Elektrizität vorherzusagen**. Mit diesen Informationen können Unternehmen ihre Produktionsvolumina besser ermitteln.

Bei Verwendung des Begriffs „Smart Grid“ ist eigentlich ein Netz gemeint, das lernfähig ist und dann Vorhersagen treffen kann. Es kann saisonale Veränderungen des Verbrauchs antizipieren und **vorübergehende Überlastungssituationen voraussehen und automatisch entsprechende Anpassungen vornehmen**. Durch eine Remoteregulierung des Verbrauchs (per „Smart Meter“, also einem intelligenten Stromzähler) können punktuelle Überlastungssituationen gemeistert werden. **Indem zuerst vorhergesagt und dann reagiert wird**, wird das Netz im Laufe der Zeit „intelligenter“.

Im restlichen Teil des Dokuments liegt der Schwerpunkt dann auf einer bestimmten Gruppe von Anwendungsfällen, bei denen es um die Vorhersage des zukünftigen, kurzfristigen und langfristigen Energiebedarfs geht. Wir arbeiten schon seit einigen Monaten in diesen Bereichen und haben so Wissen und Fähigkeiten aufgebaut, um branchenrelevante Ergebnisse erzielen zu können. Andere Anwendungsfälle sollen in Kürze ebenfalls in diesem Dokument behandelt werden.

## Geschäftliche Aspekte

### Unternehmensziele
Beim Ziel **„Energy Demo“** geht es darum, eine typische Predictive Analytics- und Machine Learning-Lösung zu veranschaulichen, die innerhalb eines sehr kurzen Zeitraums bereitgestellt werden kann. Derzeit liegt der Schwerpunkt speziell auf der Umsetzung von Lösungen zum Vorhersagen des Energiebedarfs, damit der geschäftliche Nutzen schnell realisiert werden kann und zu Vorteilen führt. Die Informationen in diesem Playbook helfen Kunden bei der Erreichung folgender Ziele:
-   Kurze Amortisationsdauer für die auf Machine Learning basierende Lösung
-   Mögliche Erweiterung eines Pilotanwendungsfalls auf andere Anwendungsfälle oder einen größeren Umfang je nach geschäftlichen Anforderungen
-   Schneller Aufbau von Wissen in Bezug auf die Cortana Analytics Suite

Unter Berücksichtigung dieser Zielsetzung soll mit diesem Playbook das geschäftliche und technische Wissen vermittelt werden, das für die Erreichung der Ziele hilfreich ist.

### Vorhersage von Stromlast und -bedarf
Im Energiesektor gibt es viele Bereiche, in denen die Vorhersage des Bedarfs zur Lösung wichtiger geschäftlicher Probleme beitragen kann. Die Vorhersage des Bedarfs kann als Grundlage für viele Kernanwendungsfälle in der Branche angesehen werden. Im Allgemeinen geht es um zwei Arten von Vorhersagen des Energiebedarfs: kurzfristig und langfristig. Jeder dieser Prozesse kann einem anderen Zweck dienen und einen anderen Ansatz haben. Der Hauptunterschied zwischen den beiden Prozessen ist der Vorhersagehorizont. Dies ist der Zeitraum, wie weit die Vorhersage in die Zukunft reicht.

#### Kurzfristige Vorhersage der Last
In Bezug auf den Energiebedarf ist die kurzfristige Vorhersage der Last (Short Term Load Forecasting, STLF) als aggregierte Last definiert, die für die nahe Zukunft und verschiedene Teile des Netzes (oder das gesamte Netz) vorhergesagt wird. In diesem Zusammenhang wird „kurzfristig“ als ein Zeithorizont zwischen 1 Stunde und 24 Stunden definiert. In einigen Fällen ist auch ein Horizont von 48 Stunden möglich. Daher ist die kurzfristige Vorhersage der Last für einen betriebsbezogenen Anwendungsfall des Netzes sehr häufig. Beispiele für STLF-Anwendungsfälle:
-   Balance zwischen Angebot und Nachfrage
-   Unterstützung des Stromhandels
-   Market Making (Strompreisfestlegung)
-   Optimierung des Netzbetriebs
-   [Demand Response](https://en.wikipedia.org/wiki/Demand_response)
-   Vorhersage von Bedarfsspitzen
-   Management auf Nachfrageseite
-   Lastenausgleich und Verhinderung von Überlastung
-   Langfristige Vorhersage der Last
-   Fehler- und Anomalieerkennung
-   Verkürzung/Ausgleich von Spitzen 

STLF-Modelle basieren größtenteils auf den Verbrauchsdaten der unmittelbaren Vergangenheit (letzter Tag oder letzte Woche), und die vorhergesagten Temperaturen dienen als wichtige Größe für die Vorhersage. Die Beschaffung einer genauen Temperaturvorhersage für die nächste Stunde oder die nächsten 24 Stunden ist heutzutage kein großes Problem mehr. Diese Modelle sind weniger anfällig für saisonale Muster oder langfristige Verbrauchstrends.

Bei STLF-Lösungen wird häufig außerdem eine hohe Zahl von Vorhersageaufrufen (Dienstanforderungen) generiert, da diese stündlich oder in einigen Fällen noch häufiger ausgelöst werden. Auch Implantationen sind häufig zu beobachten, bei denen jede einzelne Unterstation oder jeder Transformator als eigenständiges Modell dargestellt wird und der Umfang der Vorhersageanforderungen somit noch weiter zunimmt.

#### Langfristige Vorhersage der Last
Das Ziel der langfristigen Vorhersage der Last (Long Term Load Forecasting, LTLF) ist das Vorhersagen des Strombedarfs mit einem Zeithorizont, der von einer Woche bis zu mehreren Monaten reicht (in einigen Fällen auch mehrere Jahre). Dieser Zeithorizont eignet sich am besten für Anwendungsfälle, bei denen es um die Planung und um Investitionen geht.

Für langfristige Szenarios ist es wichtig, qualitativ hochwertige Daten zu nutzen, mit denen ein Zeitraum von mehreren Jahren abgedeckt wird (mindestens drei Jahre). Bei diesen Modellen werden normalerweise saisonale Muster aus Verlaufsdaten extrahiert und externe Vorhersagen genutzt, z. B. Wetter- und Klimamuster.

Es sollte einleuchten, dass eine Vorhersage meist umso ungenauer ist, je länger der Zeithorizont ist. Daher ist es wichtig, zusätzlich zur eigentlichen Vorhersage einige Vertrauensbereichsgrenzen einzubauen, damit der Mensch mögliche Abweichungen in seinen Planungsprozess integrieren kann.

Da das Verbrauchsszenario für LTLF größtenteils die Planung umfasst, ist eine deutlich geringere Zahl von Vorhersageereignissen zu erwarten (im Vergleich zu STLF). Diese Vorhersagen sind in der Regel in Visualisierungstools wie Excel oder PowerBI eingebettet und können vom Benutzer manuell aufgerufen werden.

### Vergleich von kurzfristigen und langfristigen Vorhersagen
In der folgenden Tabelle werden die wichtigsten Attribute von STLF und LTLF verglichen:

|Attribut|Kurzfristige Vorhersage der Last|Langfristige Vorhersage der Last|
|---|---|---|
|Vorhersagehorizont|1 Stunde bis zu 48 Stunden|1 Woche bis zu 6 Monaten oder mehr|
|Granularität der Daten|Stündlich|Stündlich oder täglich|
|Typische Anwendungsfälle|<ul><li>Balance von Bedarf und Nachfrage</li><li>Vorhersage von Spitzenstunden</li><li>Demand Response</li></ul>|<ul><li>Langfristige Planung</li><li>Planung von Netzressourcen</li><li>Ressourcenplanung</li></ul>|
|Typische Prädiktoren|<ul><li>Tag oder Woche</li><li>Tageszeit</li><li>Stündliche Temperatur</li></ul>|<ul><li>Monat des Jahres</li><li>Tag des Monats</li><li>Langfristige Temperatur bzw. Klima</li></ul>|
|Verlaufsdatenbereich|Daten für zwei bis drei Jahre|Daten für fünf bis zehn Jahre|
|Typische Genauigkeit|MAPE* von mindestens 95 %|MAPE* von mindestens 75 %|
|Vorhersagehäufigkeit|Stündlich oder alle 24 Stunden|Wöchentlich, monatlich oder vierteljährlich|
*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) – Mean Absolute Percentage Error

Diese Tabelle verdeutlicht die Wichtigkeit der Unterscheidung zwischen kurzfristigen und langfristigen Vorhersageszenarien, da diese für unterschiedliche Geschäftsanforderungen bestimmt sind und ggf. über unterschiedliche Bereitstellungs- und Verbrauchsmuster verfügen.

### Anwendungsfallbeispiel 1: eSmart Systems – Überlastungsoptimierung
Eine wichtige Aufgabe für ein [Smart Grid](https://en.wikipedia.org/wiki/Smart_grid) ist die dynamische und fortlaufende Optimierung und Anpassung für die sich ändernden Verbrauchsmuster. Der Stromverbrauch kann durch kurzfristige Veränderungen beeinflusst werden, die hauptsächlich aufgrund von Temperaturschwankungen auftreten (z. B. ein höherer Stromverbrauch für Klimaanlagen oder Heizung). Gleichzeitig wirken sich auch langfristige Trends auf den Stromverbrauch aus. Dies können beispielsweise saisonale Effekte, Feiertage, ein langfristiger Anstieg des Verbrauchs und sogar ökonomische Faktoren sein, z. B. Verbraucherpreisindex, Ölpreis und BIP.

Bei diesem Anwendungsfall wollte [eSmart](http://www.esmartsystems.com/) eine cloudbasierte Lösung bereitstellen, die eine Vorhersage der Wahrscheinlichkeit einer Überlastungssituation in einer bestimmten Unterstation des Netzes ermöglicht. Es sollten vor allem Unterstationen identifiziert werden, für die innerhalb der nächsten Stunde eine Überlastung auftritt, damit diese Situation per Sofortmaßnahme verhindert oder gelöst werden kann.

Für eine präzise und schnelle Vorhersage ist die Implementierung von drei Modellen erforderlich:
-   Langfristiges Modell zur Vorhersage des Stromverbrauchs der einzelnen Unterstationen für die nächsten Wochen oder Monate
-   Kurzfristiges Modell zur Vorhersage einer Überlastungssituation für eine bestimmte Unterstation während der nächsten Stunde
-   Temperaturmodell für Vorhersagen der zukünftigen Temperatur für mehrere Szenarien

Das Ziel des langfristigen Modells besteht darin, die Unterstationen nach ihrer Anfälligkeit für eine Überlastung (je nach Kapazität der Stromübertragung) während der nächsten Woche oder des nächsten Monats einzustufen. Dies ermöglicht die Erstellung einer kurzen Liste mit Unterstationen, die als Eingabe für die kurzfristige Vorhersage dienen. Da die Temperatur für das langfristige Modell ein wichtiger Prädiktor ist, ist es erforderlich, ständig Temperaturvorhersagen für mehrere Szenarien zu produzieren und als Eingabe in das langfristige Modell zu verwenden. Anschließend wird die kurzfristige Vorhersage aufgerufen, um vorherzusagen, welche Unterstation innerhalb der nächsten Stunde voraussichtlich von einer Überlastung betroffen sein wird.

Das kurzfristige und das langfristige Modell werden pro Unterstation einzeln bereitgestellt. Für die praktische Ausführung dieser Modelle ist eine umfassende Orchestrierung erforderlich. Um kurzfristig eine höhere Vorhersagegenauigkeit zu erzielen, wird für jede Stunde des Tages ein Modell mit höherer Granularität genutzt. Alle diese Modelle werden einmal pro Stunde ausgeführt, und die Ausführung wird innerhalb von wenigen Minuten abgeschlossen, damit ausreichend Zeit ist, um bei Bedarf zu reagieren und vorbeugende Maßnahmen einzuleiten. Diese Sammlung der Modelle wird aktuell gehalten, indem regelmäßig mit den neuesten Daten eine Neujustierung durchgeführt wird.

Weitere Informationen zu diesem Anwendungsfall finden Sie [hier](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### Qualifikationskriterien für Anwendungsfälle – Voraussetzungen
Die wichtigste Stärke von Cortana Analytics liegt darin, auf Machine Learning basierende Lösungen mit hoher Leistung bereitzustellen und zu skalieren. Die Anwendung ist für die Unterstützung Tausender Vorhersagen ausgelegt, die gleichzeitig ausgeführt werden. Sie kann automatische Skalierungen durchführen, um auf veränderte Verbrauchsmuster zu reagieren. Der Schwerpunkt einer Lösung liegt daher auf der Genauigkeit und der Rechenleistung. Angenommen, ein Versorgungsunternehmen möchte eine präzise Vorhersage des Stromverbrauchs für die nächste Stunde und jede Stunde des Tages produzieren. Hierbei ist es weniger interessant, die Frage zu beantworten, warum der Verbrauch so und nicht anders vorhergesagt wird (dies wird vom Modell selbst erledigt).

Es ist daher wichtig zu wissen, dass nicht alle Anwendungsfälle und geschäftlichen Probleme per Machine Learning effektiv gelöst werden können.

Cortana Analytics und Machine Learning können beim Lösen eines bestimmten geschäftlichen Problems sehr effektiv sein, wenn die folgenden Kriterien erfüllt sind:
-   Grundlage des geschäftlichen Problems ist eine **Vorhersage**. Ein Beispiel für einen Anwendungsfall dieser Art ist ein Versorgungsunternehmen, das die Stromlast für eine bestimmte Unterstation während der nächsten Stunde vorhersagen möchte. Das Analysieren und Einstufen von Gründen für den bisherigen Verbrauchsverlauf ist dagegen eine **Beschreibung** und daher weniger geeignet.
-   Es muss ein eindeutiger **Aktionspfad** beschritten werden, nachdem die Vorhersage verfügbar ist. Durch das Vorhersagen einer Überlastung in einer Unterstation während der nächsten Stunde kann beispielsweise eine proaktive Aktion zur Reduzierung der Last ausgelöst werden, die der Unterstation zugeordnet ist, und die Überlastung kann unter Umständen verhindert werden.
-   Der Anwendungsfall ist eine **typische Art von Problem**, und wenn es gelöst wird, kann dies als Grundlage für die Lösung ähnlicher Anwendungsfälle dienen.
-   Der Kunde kann **quantitative und qualitative Ziele** festlegen, mit denen eine erfolgreiche Implementierung der Lösung demonstriert wird. Ein gutes quantitatives Ziel für die Vorhersage des Stromverbrauchs ist beispielsweise der erforderliche Schwellenwert für die Genauigkeit (z. B. ein zulässiger Fehler von bis zu 5 %) oder ein Ansatz, bei dem bei der Vorhersage der Überlastung einer Unterstation die Genauigkeit (Rate der richtig positiven Ergebnisse) und Sensitivität (Umfang der richtig positiven Ergebnisse) über einem bestimmten Schwellenwert liegen sollen. Diese Ziele sollten von den geschäftlichen Zielen des Kunden abgeleitet werden.
-   Es geht um ein eindeutiges **Szenario für die Integration** in den geschäftlichen Workflow des Unternehmens. Beispielsweise kann die Vorhersage der Last von Unterstationen in das Netzsteuerungszentrum integriert werden, um Aktivitäten zur Verhinderung von Überlastungen zu ermöglichen.
-   Der Kunde verfügt über fertige **Daten von ausreichender Qualität** zur Unterstützung des Anwendungsfalls (siehe nächsten Abschnitt zur **Datenqualität** in diesem Playbook).
-   Der Kunde nutzt die auf die Cloud ausgerichtete Datenarchitektur oder **cloudbasiertes Machine Learning**, z. B. Azure ML und andere Komponenten der Cortana Analytics Suite.
-   Der Kunde ist bereit, einen **End-to-End-Datenfluss** einzurichten, der die ständige Bereitstellung von Daten für die Cloud ermöglicht, und die Lösung zu **operationalisieren**.
-   Der Kunde ist bereit, **Mitarbeiter bereitzustellen**, die während der ersten Pilotimplementierung aktiv beteiligt sind. Nach dem erfolgreichen Abschluss können das Wissen und die Eigentümerschaft der Lösung dann an den Kunden übergeben werden.
-   Bei den Mitarbeitern des Kunden sollte es sich um **Datenprofis** handeln, vorzugsweise einen so genannten „Data Scientist“.

Die Qualifizierung eines Anwendungsfalls anhand der obigen Kriterien kann die Erfolgsraten eines Anwendungsfalls deutlich verbessern und eine gute Grundlage für die Implementierung zukünftiger Anwendungsfälle darstellen.

### Cloudbasierte Lösungen
Cortana Analytics Suite ist unter Azure eine integrierte Umgebung in der Cloud. Die Bereitstellung einer erweiterten Analyselösung in einer Cloudumgebung hat erhebliche Vorteile für Unternehmen und kann eine große Veränderung für Unternehmen bedeuten, die noch lokale IT-Lösungen verwenden. Im Energiesektor gibt es einen eindeutigen Trend zur allmählichen Umstellung des Betriebs auf die Cloud. Dieser Trend geht Hand in Hand mit der Smart Grid-Entwicklung, die oben unter **Branchentrends** beschrieben ist. Da der Schwerpunkt in diesem Playbook auf einer cloudbasierten Lösung im Energiesektor liegt, ist es wichtig, die Vorteile und anderen Aspekte der Bereitstellung einer cloudbasierten Lösung zu erläutern.

Als größter Vorteil einer cloudbasierten Lösung sind sicherlich die Kosten zu nennen. Da bei einer Lösung in der Cloud bereitgestellte Komponenten verwendet werden, fallen keine Vorabkosten oder Umsatzkosten (COGS, Cost of Goods Sold) an. Es muss also nicht in Hardware, Software und IT-Wartung investiert werden, und dies bedeutet, dass das geschäftliche Risiko deutlich reduziert wird.

Ein wichtiger Vorteil ist die nutzungsbasierte Bezahlung von cloudbasierten Lösungen. Cloudbasierte Server für Computing oder Speicherung können bedarfsabhängig bereitgestellt und skaliert werden. Dies ist der Kosteneffizienzvorteil einer cloudbasierten Lösung.

Es sind schließlich auch keine Investitionen in IT-Wartung oder die Entwicklung der zukünftigen Infrastruktur erforderlich, da all dies Teil des cloudbasierten Angebots ist. Die Cortana Analytics Suite bietet daher die besten Dienste und eine Roadmap, die ständig weiterentwickelt wird. Es werden ständig neue Features, Komponenten und Fähigkeiten eingeführt und weiterentwickelt.

Für ein Unternehmen, das noch am Anfang der Umstellung auf die Cloud steht, empfehlen wir dringend einen Schritt-für-Schritt-Ansatz und die Implementierung einer Roadmap für die Cloudmigration. Wir sind der Meinung, dass die in diesem Playbook beschriebenen Anwendungsfälle für Versorger und Unternehmen der Energiebranche eine hervorragende Möglichkeit zur Durchführung von Pilotprojekten für Predictive Analytics-Lösungen in der Cloud darstellen.

#### Aspekte der Business Case-Rechtfertigung
In vielen Fällen sind Kunden unter Umständen daran interessiert, eine Business Case-Rechtfertigung für einen bestimmten Anwendungsfall anzufertigen, bei dem eine cloudbasierte Lösung und Machine Learning wichtige Bestandteile sind. Im Gegensatz zu einer lokalen Lösung sind die Vorabkosten bei einer cloudbasierten Lösung minimal, und die meisten Kostenelemente richten sich nach der eigentlichen Nutzung. Bei der Bereitstellung einer Lösung zum Vorhersagen des Energiebedarfs über die Cortana Analytics Suite können mehrere Dienste in eine gemeinsame Kostenstruktur eingebunden werden. Datenbanken (z. B. SQL Azure) können beispielsweise zum Speichern von Rohdaten verwendet werden, und anschließend wird für die eigentlichen Vorhersagen Azure ML zum Hosten der Vorhersagedienste genutzt. In diesem Beispiel kann die Kostenstruktur zum Beispiel Speicher- und Transaktionskomponenten enthalten.

Andererseits ist ein gutes Verständnis des geschäftlichen Nutzens einer Vorhersage des Energiebedarfs (kurz- oder langfristig) unerlässlich. Es ist sogar wichtig, sich den geschäftlichen Nutzen jedes Vorhersagevorgangs klar zu machen. Durch das genaue Vorhersagen der Stromlast für die nächsten 24 Stunden kann beispielsweise eine Überproduktion verhindert werden, oder es können Überlastungen im Netz vermieden werden. Dies kann dann in Form von täglichen Kosteneinsparungen quantifiziert werden.

Eine einfache Formel zur Berechnung des finanziellen Vorteils einer Lösung für die Bedarfsvorhersage lautet wie folgt: ![Einfache Formel zur Berechnung des finanziellen Vorteils von Lösungen zur Bedarfsvorhersage](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Da die Cortana Analytics Suite über ein Preismodell mit nutzungsbasierter Bezahlung verfügt, ist es nicht erforderlich, in diese Formel eine Festkostenkomponente einzufügen. Diese Formel kann für die tägliche, monatliche oder jährliche Abrechnung verwendet werden.

Aktuelle Tarife für die Cortana Analytics Suite und Azure ML finden Sie [hier](http://azure.microsoft.com/pricing/details/machine-learning/).

### Prozess der Entwicklung von Lösungen
Der Entwicklungszyklus einer Lösung für die Vorhersage des Energiebedarfs besteht normalerweise aus vier Phasen, in denen jeweils cloudbasierte Technologien und Dienste der Cortana Analytics Suite verwendet werden.

Dies ist im folgenden Diagramm dargestellt:

![Smart Grid-Zyklus](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

Im folgenden Absatz wird dieser Prozess mit vier Schritten beschrieben:

1.  **Datensammlung:** Alle erweiterten Analyselösungen basieren auf Daten (siehe **Datenaspekte**). Vor allem bei Predictive Analytics und Vorhersagen ist ein fortlaufender, dynamischer Datenfluss unerlässlich. Bei der Vorhersage des Energiebedarfs können diese Daten direkt von Smart Metern stammen oder bereits in einer lokalen Datenbank aggregiert werden. Es werden auch andere externe Datenquellen verwendet, z. B. Wetter- und Temperaturdaten. Dieser ständige Datenfluss muss orchestriert, geplant und gespeichert werden. [Azure Data Factory](http://azure.microsoft.com/services/data-factory/) (ADF) ist das wichtigste Werkzeug bei der Erfüllung dieser Aufgabe.
2.  **Modellierung:** Um präzise und zuverlässige Vorhersagen des Energiebedarfs zu erhalten, muss ein gutes Modell entwickelt (trainiert) und gepflegt werden, bei dem die Verlaufsdaten genutzt und die aussagekräftigen Muster und Vorhersagemuster in den Daten extrahiert werden. Der Machine Learning-Bereich (ML) wächst ständig, und es werden regelmäßig immer anspruchsvollere Algorithmen entwickelt. Azure ML Studio verfügt über eine hervorragende Benutzeroberfläche, mit der auch die anspruchsvollsten ML-Algorithmen in einem Workflow genutzt werden können. Dieser Workflow ist in einem intuitiven Flussdiagramm dargestellt und enthält die Bereiche Datenvorbereitung, Featureextraktion, Modellierung und Modellauswertung. Benutzer können Hunderte von verschiedenen Modellen nutzen, die in dieser Umgebung enthalten sind. Am Ende dieser Phase verfügt ein Data Scientist über ein funktionierendes Modell, das vollständig evaluiert und fertig für die Bereitstellung ist.

	Das folgende Diagramm enthält eine Darstellung eines typischen Workflows:

	![Workflow der Modellierung](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)

3.  **Bereitstellung:** Nachdem Sie nun über ein funktionierendes Modell verfügen, ist der nächste Schritt die Bereitstellung. Hier wird das Modell in einen Webdienst konvertiert, mit dem eine RESTful-API verfügbar gemacht wird, die von verschiedenen Verbrauchsclients gleichzeitig über das Internet aufgerufen werden kann. Mit Azure ML ist es leicht möglich, ein Modell durch das Klicken auf eine Schaltfläche direkt aus Azure ML Studio bereitzustellen. Der gesamte Bereitstellungsprozess läuft im Hintergrund ab. Diese Lösung kann automatisch skaliert werden, um den erforderlichen Verbrauch zu decken.

4.  **Verbrauch:** In dieser Phase verwenden wir das Vorhersagemodell nun, um Vorhersagen zu produzieren. Der Verbrauch kann über eine Benutzeranwendung (z. B. ein Dashboard) oder direkt aus einem Betriebssystem bereitgestellt werden, z. B. einem System zum Abgleich von Bedarf und Versorgung oder einer Lösung zur Optimierung des Netzes. Mehrere Anwendungsfälle können über ein einzelnes Modell gesteuert werden.

## Datenaspekte
Nach der Behandlung der geschäftlichen Aspekte (siehe **Geschäftliche Aspekte**) einer Lösung zur Vorhersage des Energiebedarfs geht es als Nächstes um den Datenteil. Jede Predictive Analytics-Lösung basiert auf zuverlässigen Daten. Für Vorhersagen des Energiebedarfs werden Verlaufsdaten zum Verbrauch mit unterschiedlicher Granularität verwendet. Diese Verlaufsdaten werden als Rohmaterial verwendet. Sie werden einer sorgfältigen Analyse unterzogen, bei der der Data Scientist Prädiktoren ermittelt (auch als Features bezeichnet), die in ein Modell eingefügt werden können, mit dem später die erforderlichen Vorhersagen generiert werden.

Im restlichen Teil dieses Abschnitts beschreiben wir die verschiedenen Schritte und Aspekte, die das Verständnis der Daten und die Konvertierung in eine verwendbare Form betreffen.

### Modellentwicklungszyklus
Für die Erstellung guter Vorhersagemodelle ist eine sorgfältige Vorbereitung und Planung erforderlich. Das Ergebnis des gesamten Prozesses kann erheblich verbessert werden, wenn Sie den Modellierungsprozess in mehrere Schritte unterteilen und sich jeweils auf einen Schritt konzentrieren.

Im folgenden Diagramm ist dargestellt, wie Sie den Modellierungsprozess in mehrere Schritte unterteilen können:

![Modellentwicklungszyklus](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Sie sehen, dass der Zyklus aus sechs Schritten besteht:
-   Problemformulierung
-   Datenerfassung und Durchsuchen von Daten
-   Datenvorbereitung und Featureentwicklung
-   Modellierung
-   Modellauswertung
-   Entwicklung

Im restlichen Teil dieses Abschnitts werden die Einzelschritte und Elemente beschrieben, die bei jedem Schritt berücksichtigt werden müssen.

### Problemformulierung
Wir können die Formulierung des Problems als wichtigsten Schritt bezeichnen, der vor der Implementierung jeder Predictive Analytics-Lösung ausgeführt werden muss. In diesem Schritt transformieren wir das geschäftliche Problem und zerlegen es in einzelne Elemente, die mit Daten- und Modellierungsverfahren gelöst werden können. Es wird empfohlen, das Problem als eine Reihe von Fragen zu formulieren, die beantwortet werden sollen. Hier sind einige mögliche Fragen angegeben, die in Bezug auf die Vorhersage des Energiebedarfs hilfreich sein können:
-   Wie hoch ist die erwartete Last einer einzelnen Unterstation in der nächsten Stunde oder am nächsten Tag?
-   Zu welchem Zeitpunkt ist der Bedarf im Netz am höchsten?
-   Wie hoch ist die Wahrscheinlichkeit, dass das Netz die erwartete Spitzenlast bewältigen kann?
-   Welche Strommenge sollte das Kraftwerk für jede Stunde eines Tages erzeugen?

Die Formulierung dieser Fragen hilft uns dabei, die richtigen Daten auszuwählen und eine Lösung zu implementieren, die optimal für das vorliegende geschäftliche Problem geeignet ist. Außerdem können wir dann einige wichtige Metriken festlegen, mit denen wir die Leistung des Modells auswerten können. Wie genau sollte die Vorhersage beispielsweise sein, und wie groß ist der Fehlerbereich, der für das Unternehmen noch akzeptabel ist?

### Datenquellen
Im modernen Smart Grid werden Daten aus unterschiedlichen Teilen und Komponenten des Netzes erfasst. Diese Daten stellen verschiedene Aspekte des Betriebs und der Nutzung des Stromnetzes dar. In Bezug auf die Vorhersage des Energiebedarfs beschränken wir die Beschreibung auf Datenquellen, die den eigentlichen Energieverbrauch widerspiegeln. Eine wichtige Quelle für den Energieverbrauch sind Smart Meter. Immer mehr Versorgungsunternehmen weltweit stellen Smart Meter für ihre Verbraucher bereit. Mit Smart Metern wird der tatsächliche Stromverbrauch aufgezeichnet, und diese Daten werden ständig zurück an das Versorgungsunternehmen gemeldet. Daten werden erfasst und in festen Intervallen gesendet, die von fünf Minuten bis zu einer Stunde reichen können. Aufwändigere Smart Meter können per Remotezugriff programmiert werden, um den tatsächlichen Verbrauch in einem Haushalt zu steuern und abzustimmen. Smart Meter-Daten sind relativ zuverlässig und enthalten einen Zeitstempel. Somit sind sie ein wichtiger Bestandteil der Vorhersage des Energiebedarfs. Smart Meter-Daten können auf verschiedenen Ebenen der Netztopologie aggregiert (zusammengefasst) werden: Transformator, Unterstation, Region usw.. Anschließend können wir die erforderliche Aggregationsebene wählen, um dafür ein Vorhersagemodell zu erstellen. Wenn ein Versorgerunternehmen beispielsweise die zukünftige Last für die einzelnen Unterstationen des Netzes vorhersagen möchte, können die Daten aller Smart Meter für jede einzelne Unterstation aggregiert und als Eingabe für das Vorhersagemodell verwendet werden. Wir bezeichnen Smart Meter als interne Datenquelle.

Eine zuverlässige Vorhersage des Energiebedarfs basiert auch auf anderen externen Datenquellen. Ein wichtiger Faktor, der sich auf den Stromverbrauch auswirkt, ist das Wetter bzw. die Temperatur. Verlaufsdaten weisen auf eine starke Korrelation zwischen der Außentemperatur und dem Stromverbrauch hin. An heißen Sommertagen nutzen Verbraucher ihre Klimaanlagen, und im Winter kommen Heizsysteme vermehrt zum Einsatz. Eine zuverlässige Quelle für Verlaufsdaten zu den Temperaturen am Netzstandort ist daher sehr wichtig. Außerdem wird auch eine genaue Vorhersage der Temperatur als Prädiktor für den Stromverbrauch benötigt.

Andere externe Datenquellen können bei der Erstellung von Vorhersagemodellen für den Energieverbrauch ebenfalls hilfreich sein. Beispiele hierfür sind langfristige Klimaveränderungen, Wirtschaftsindizes (z. B. BIP) und andere. In diesem Dokument wird auf diese anderen Datenquellen nicht weiter eingegangen.

### Datenstruktur
Nach dem Ermitteln der erforderlichen Datenquellen möchten wir sicherstellen, dass die erfassten Rohdaten die richtigen Datenfeatures enthalten. Zur Erstellung eines zuverlässigen Vorhersagemodells für den Verbrauch müssen wir überprüfen, ob die erfassten Daten Datenelemente enthalten, mit denen der zukünftige Verbrauch vorhergesagt werden kann. Hier sind einige grundlegende Anforderungen aufgeführt, die die Datenstruktur (das Schema) der Rohdaten betreffen.

Die Rohdaten bestehen aus Zeilen und Spalten. Jede Messung wird als eine einzelne Datenzeile dargestellt. Jede Datenzeile enthält mehrere Spalten (auch als Features oder Felder bezeichnet).

1.  **Time stamp (Zeitstempel):** Im Zeitstempelfeld wird der Zeitpunkt angegeben, zu dem die Messung aufgezeichnet wurde. Für das Feld sollte ein gängiges Format für Datum und Uhrzeit verwendet werden. Es sollte sowohl der Datums- als auch der Uhrzeitteil einbezogen werden. In den meisten Fällen muss die Zeit erst auf der zweiten Ebene der Granularität aufgezeichnet werden. Es ist wichtig, die Zeitzone anzugeben, in der die Daten aufgezeichnet werden.
2.  **Meter ID (Meter-ID):** Mit diesem Feld wird das Smart Meter bzw. das Messgerät identifiziert. Es handelt sich um eine Kategorievariable, und es kann eine Kombination aus Zahlen und Buchstaben verwendet werden.
3.  **Consumption value (Verbrauchswert):** Dies ist der tatsächliche Verbrauch an einem bestimmten Datum bzw. zu einer bestimmten Uhrzeit. Der Verbrauch kann in kWh (Kilowattstunde) oder einer anderen gewünschten Einheit gemessen werden. Es ist wichtig zu beachten, dass die Maßeinheit für alle Messungen der Daten gleich sein muss. In einigen Fällen kann der Verbrauch unter Umständen über drei Stromphasen bereitgestellt werden. In diesem Fall müssen wir alle unabhängigen Verbrauchsphasen erfassen.
4.  **Temperature (Temperatur):** Die Temperatur wird normalerweise über eine unabhängige Quelle erfasst. Allerdings sollte diese mit den Verbrauchsdaten kompatibel sein. Sie sollte wie oben beschrieben über einen Zeitstempel verfügen, der eine Synchronisierung mit den tatsächlichen Verbrauchsdaten ermöglicht. Der Temperaturwert kann in Grad Celsius oder Fahrenheit angegeben werden, aber die Maßeinheit sollte über alle Messungen hinweg gleich bleiben.
5.  **Location (Standort):** Im Feld für den Standort wird normalerweise der Ort angegeben, an dem die Temperaturdaten erfasst wurden. Als Format kann hierfür die Postleitzahl oder der Breiten-/Längengrad verwendet werden.

Die folgende Tabelle enthält Beispiele für ein gut geeignetes Datenformat für Verbrauch und Temperatur:

|**Date**|**Time**|**Meter ID**|**Phase 1**|**Phase 2**|**Phase 3**|
|--------|--------|------------|-----------|-----------|-----------|
|01\.07.2015|10:00:00|ABC1234 |7,0 |2,1 |5,3 |
|01\.07.2015|10:00:01|ABC1234 |7,1 |2,2 |4,3 |
|01\.07.2015|10:00:02|ABC1234 |6,0 |2,1 |4,0 |

|**Date**|**Time**|**Location**|**Temperature**|
|--------|--------|-------------|---------------|
|01\.07.2015|10:00:00|11242 |24,4 |
|01\.07.2015|10:00:01|11242 |24,4 |
|01\.07.2015|10:00:02|11242 |24,5 |

Wie oben zu sehen, enthält dieses Beispiel drei unterschiedliche Verbrauchswerte für drei Stromphasen. Beachten Sie auch, dass separate Felder für das Datum und die Uhrzeit verwendet werden, die aber auch in einer Spalte kombiniert werden können. In diesem Fall wird für die Spalte mit dem Standort ein Format mit einer fünfstelligen Postleitzahl und für die Temperatur das Format „Grad Celsius“ verwendet.

### Datenformat
Die Cortana Analytics Suite unterstützt die gängigsten Datenformate wie CSV, TSV, JSON usw. Es ist wichtig, dass das Datenformat über den gesamten Lebenszyklus des Projekts gleich bleibt.

### Datenerfassung
Da die Vorhersage des Energiebedarfs ständig und sehr häufig erfolgt, müssen wir sicherstellen, dass die Rohdaten bereitgestellt werden. Hierfür verwenden wir einen robusten und zuverlässigen Datenerfassungsprozess. Für den Erfassungsprozess muss sichergestellt werden, dass die Rohdaten für den Vorhersageprozess zum erforderlichen Zeitpunkt verfügbar sind. Dies bedeutet, dass die Datenerfassungshäufigkeit höher als die Vorhersagehäufigkeit sein sollte.

Beispiel: Wenn von der Lösung für die Vorhersage des Bedarfs täglich um 8:00 Uhr eine neue Vorhersage generiert wird, müssen wir Folgendes sicherstellen: Alle Daten, die während der letzten 24 Stunden gesammelt wurden, wurden bis zu diesem Zeitpunkt vollständig erfasst, und auch die Daten der letzten Stunde müssen vollständig erfasst worden sein.

Hierfür verfügt die Cortana Analytics Suite über verschiedene Wege zur Unterstützung eines zuverlässigen Datenerfassungsprozesses. Dies wird im Abschnitt **Bereitstellung** dieses Dokuments näher erläutert.

### Datenqualität
Die Rohdatenquelle, die zur Durchführung einer zuverlässigen und genauen Bedarfsvorhersage erforderlich ist, muss einige grundlegende Kriterien in Bezug auf die Datenqualität erfüllen. Es können zwar erweiterte statistische Methoden verwendet werden, um mögliche Probleme mit der Datenqualität aufzufangen, aber wir müssen trotzdem sicherstellen, dass bei der Erfassung neuer Daten einige Mindestanforderungen an die Datenqualität erfüllt werden. Zu berücksichtigende Aspekte in Bezug auf die Qualität von Rohdaten:
-   **Fehlender Wert:** Dies bezieht sich auf eine Situation, in der eine bestimmte Messung nicht erfasst wurde. Die Grundvoraussetzung hierbei ist, dass die Rate der fehlenden Werte für einen bestimmten Zeitraum nicht größer als 10 % ist. Falls ein einzelner Wert fehlt, sollte dies mit einer vordefinierten Angabe (z. B. „9999“) angezeigt werden, und nicht mit dem Wert „0“, da es sich hierbei um einen gültigen Messwert handeln kann.
-   **Genauigkeit der Messung:** Der tatsächliche Verbrauchs- bzw. Temperaturwert sollte genau aufgezeichnet werden. Ungenaue Messungen führen zu ungenauen Vorhersagen. Der Messfehler sollte relativ zum richtigen Wert in einem Bereich von unter 1 % liegen.
-   **Zeitpunkt der Messung:** Der tatsächliche Zeitstempel der erfassten Daten darf relativ zum wirklichen Zeitpunkt der Messung nicht um mehr als zehn Sekunden abweichen.
-   **Synchronisierung:** Wenn mehrere Datenquellen verwendet werden (z. B. Verbrauch und Temperatur), müssen wir sicherstellen, dass hierfür keine Probleme mit der Zeitsynchronisierung auftreten. Dies bedeutet, dass der Zeitunterschied zwischen dem erfassten Zeitstempel von zwei unabhängigen Datenquellen nicht mehr als zehn Sekunden betragen sollte.
-   **Latenz:** Wie oben unter **Datenerfassung** beschrieben benötigen wir einen zuverlässigen Datenfluss und Erfassungsprozess. Um dies steuern zu können, müssen wir die Datenlatenz unter Kontrolle haben. Die Latenz wird als Zeitunterschied zwischen dem Zeitpunkt, zu dem die eigentliche Messung vorgenommen wurde, und dem Zeitpunkt angegeben, zu dem der Wert in den Cortana Analytics Suite-Speicher geladen wurde und bereit für die Verwendung ist. Für die kurzfristige Vorhersage der Last sollte die Latenz insgesamt nicht mehr als 30 Minuten betragen. Für die langfristige Vorhersage der Last sollte die Latenz insgesamt nicht mehr als einen Tag betragen.

### Datenvorbereitung und Featureentwicklung
Nachdem die Rohdaten erfasst (siehe **Datenerfassung**) und sicher gespeichert wurden, können sie verarbeitet werden. In der Phase der Datenvorbereitung werden die Rohdaten praktisch in ein Format für die Modellierungsphase konvertiert (transformiert, umgeformt). Dies können einfache Vorgänge, z. B. die Verwendung der Spalte mit den Rohdaten und dem gemessenen Wert in unveränderter Form, standardisierte Werte, komplexere Vorgänge, z. B. [Zeitverzögerung](https://en.wikipedia.org/wiki/Lag_operator), sowie andere Vorgänge sein. Die neu erstellten Datenspalten werden als Datenfeatures bezeichnet, und der dazugehörige Prozess der Erstellung hat die Bezeichnung „Featureentwicklung“. Am Ende dieses Prozesses verfügen wir über ein neues Dataset, das aus den Rohdaten abgeleitet wurde und für die Modellierung verwendet werden kann. Darüber hinaus müssen in der Phase der Datenvorbereitung auch fehlende Werte behandelt (siehe **Datenqualität**) und entsprechende Lösungen gefunden werden. In einigen Fällen müssen die Daten außerdem normalisiert werden, um sicherzustellen, dass sich alle Werte innerhalb des gleichen Skalierungsbereichs befinden.

In diesem Abschnitt sind einige allgemeine Datenfeatures aufgeführt, die in Vorhersagemodellen für den Energiebedarf verwendet werden.

**Zeitgesteuerte Features:** Diese Features werden aus den Datum/Zeitstempel-Daten abgeleitet. Diese werden extrahiert und in kategorische Features konvertiert, z. B.:
-   Tageszeit: Gibt die Stunde des Tages an und kann die Werte 0 bis 23 enthalten.
-   Wochentag: Gibt den Wochentag an und kann die Werte 1 (Sonntag) bis 7 (Samstag) enthalten.
-   Tag des Monats: Gibt das Datum an und kann Werte von 1 bis 31 enthalten.
-   Monat des Jahrs: Gibt den Monat an und kann Werte von 1 (Januar) bis 12 (Dezember) enthalten.
-   Wochenende: Dies ist ein Feature mit Binärwert, das den Wert 0 für Wochentage oder 1 für das Wochenende aufweisen kann.
-   Feiertag: Dies ein Feature mit Binärwert, das den Wert 0 für einen normalen Tag oder 1 für einen Feiertag aufweisen kann.
-   Fourier-Ausdrücke: Fourier-Ausdrücke sind Gewichtungen, die vom Zeitstempel abgeleitet und verwendet werden, um die Saisonabhängigkeit (Zyklen) von Daten zu erfassen. Da die Daten mehrere Zeitabschnitte umfassen, benötigen wir in der Regel auch mehrere Fourier-Ausdrücke. Bedarfswerte können beispielsweise über jährliche, wöchentliche und tägliche Zyklen verfügen, sodass sich drei Fourier-Ausdrücke ergeben.

**Unabhängige Features der Messung:** Die unabhängigen Features umfassen alle Datenelemente, die wir als Prädiktoren in unserem Modell verwenden möchten. Hierbei schließen wir das abhängige Feature aus, das wir vorhersagen müssen.
-   Verzögerungsfeature: Dies sind zeitversetzte Werte des tatsächlichen Bedarfs. Beim Feature vom Typ „lag 1“ wird der Bedarfswert beispielsweise für die vorherige Stunde (stündliche Daten vorausgesetzt) relativ zum aktuellen Zeitstempel vorgehalten. Auf ähnliche Weise können wir „lag 2“, „lag 3“ usw. hinzufügen. Die tatsächliche Kombination der Verzögerungsfeatures, die verwendet werden, wird während der Modellierungsphase per Auswertung der Modellergebnisse ermittelt.
-   Langfristiger Trend: Dieses Feature steht für den linearen Bedarfsanstieg von Jahr zu Jahr.

**Abhängiges Feature:** Das abhängige Feature ist die Datenspalte, die von unserem Modell vorhergesagt werden soll. Per [Supervised Machine Learning](https://en.wikipedia.org/wiki/Supervised_learning) (Überwachtes Lernen) müssen wir das Modell zuerst trainieren, indem wir die abhängigen Features verwenden (auch Bezeichnungen („Labels“) genannt). So kann das Modell die Muster in den Daten erlernen, die dem abhängigen Feature zugeordnet sind. Beim Vorhersagen des Energiebedarfs soll normalerweise der tatsächliche Bedarf vorhergesagt werden, und daher wird dies als abhängiges Feature verwendet.

**Behandlung von fehlenden Werten:** Während der Phase der Datenvorbereitung müssen wir die beste Strategie zur Behandlung der fehlenden Werte bestimmen. Hierfür werden größtenteils die verschiedenen statistischen [Methoden zur Datenimputation](https://en.wikipedia.org/wiki/Imputation_(statistics)) verwendet. Bei der Vorhersage des Energiebedarfs werden fehlende Werte normalerweise imputiert, indem der gleitende Durchschnitt der vorherigen verfügbaren Datenpunkte genutzt wird.

**Datennormalisierung:** Die Datennormalisierung ist eine andere Art der Transformation und wird verwendet, um alle numerischen Daten, z. B. die Bedarfsvorhersage, in einem ähnlichen Skalierungsbereich zusammenzufassen. Dies trägt normalerweise zur höheren Genauigkeit und Präzision des Modells bei. Hierfür dividieren wir in der Regel den tatsächlichen Wert durch den Datenbereich. So wird der Originalwert in einen kleineren Bereich herunterskaliert, meist zwischen -1 und 1.

## Modellierung
In der Modellierungsphase wird die Konvertierung der Daten in ein Modell durchgeführt. Kern dieses Prozesses sind erweiterte Algorithmen, mit denen die Verlaufsdaten (Trainingsdaten) durchsucht und Muster extrahiert werden und anschließend ein Modell erstellt wird. Dieses Modell kann später verwendet werden, um Vorhersagen für neue Daten zu treffen, die nicht zum Erstellen des Modells verwendet wurden.

Nachdem wir über ein funktionierendes zuverlässiges Modell verfügen, können wir es zum Bewerten neuer Daten verwenden, die für die erforderlichen Features (X) strukturiert sind. Für den Bewertungsprozess wird das dauerhafte Modell (Objekt aus der Trainingsphase) verwendet und die Zielvariable vorhergesagt, die mit „Ŷ“ angegeben wird.

### Modellierungsverfahren für die Bedarfsvorhersage
Bei der Bedarfsvorhersage nutzen wir Verlaufsdaten, die nach der Zeit sortiert sind. Im Allgemeinen bezeichnen wir Daten, die die Zeitdimension enthalten, als [Zeitreihe](https://en.wikipedia.org/wiki/Time_series). Das Ziel bei der Zeitreihenmodellierung besteht darin, zeitbezogene Trends, Saisonabhängigkeit und Autokorrelation (Korrelation in Abhängigkeit der Zeit) zu ermitteln und aus diesen Daten ein Modell zu formulieren.

In den letzten Jahren wurden erweiterte Algorithmen entwickelt, um die Zeitreihenvorhersage abzudecken und die Vorhersagegenauigkeit zu verbessern. Einige davon werden hier kurz beschrieben.

> [AZURE.NOTE] Dieser Abschnitt soll nicht als Übersicht über Machine Learning und Vorhersagen dienen, sondern als kurzer Überblick über Modellierungsverfahren, die für Bedarfsvorhersagen häufig eingesetzt werden. Falls Sie weitere Informationen und Lehrmittel zur Zeitreihenvorhersage benötigen, legen wir Ihnen die Onlinedokumentation [Forecasting: principles and practice](https://www.otexts.org/book/fpp) (Vorhersagen: Prinzipien und Vorgehensweisen) ans Herz.

#### [**MA (Moving Average)**](https://www.otexts.org/fpp/6/2) (Gleitender Durchschnitt)
Der gleitende Durchschnitt ist eines der ersten Analyseverfahren, das für die Zeitreihenvorhersage verwendet wurde, und ist immer noch eines der am häufigsten genutzten Verfahren. Außerdem ist es die Grundlage für anspruchsvollere Vorhersageverfahren. Mit dem gleitenden Durchschnitt sagen wir den nächsten Datenpunkt voraus, indem wir den Mittelwert der „K“ häufigsten Punkte bilden. „K“ steht hierbei für die Reihenfolge des gleitenden Durchschnitts.

Beim Verfahren des gleitenden Durchschnitts wird die Vorhersage geglättet, sodass es unter Umständen nicht gut geeignet ist, wenn die Daten eine hohe Volatilität aufweisen.

#### [**ETS (Exponential Smoothing)**](https://www.otexts.org/fpp/7/5) (Exponentielle Glättung)
Unter der exponentiellen Glättung sind verschiedene Methoden zusammengefasst, bei denen der gewichtete Durchschnitt der letzten Datenpunkte verwendet wird, um den nächsten Datenpunkt vorherzusagen. Die Idee hierbei ist, dass neueren Werten eine höhere Gewichtung zugewiesen wird und diese Gewichtung für ältere Messwerte allmählich verringert wird. Es werden verschiedene Methoden verwendet, von denen einige eine Behandlung der Saisonabhängigkeit in den Daten umfassen, z. B. die [Holt-Winters Seasonal Method](https://www.otexts.org/fpp/7/5).

Bei einem Teil dieser Methoden fließt die Saisonabhängigkeit der Daten ein.

#### [**ARIMA (Auto Regression Integrated Moving Average)**](https://www.otexts.org/fpp/8)
Bei „Auto Regression Integrated Moving Average“ (ARIMA) wird eine andere Gruppe von Methoden genutzt, die häufig für Zeitreihenvorhersagen eingesetzt wird. Hierbei wird die Autoregression quasi mit dem gleitenden Durchschnitt kombiniert. Für Autoregressionsmethoden werden Regressionsmodelle verwendet, indem vorherige Zeitreihenwerte zum Berechnen des nächsten Datumspunkts genutzt werden. Bei ARIMA-Methoden werden auch Differenzmethoden angewendet, bei denen die Differenz zwischen Datenpunkten berechnet und dann anstelle des ursprünglich gemessenen Werts verwendet wird. Schließlich werden bei ARIMA auch die Verfahren mit dem gleitenden Durchschnitt eingesetzt, die oben beschrieben sind. Die Kombination all dieser Methoden auf unterschiedliche Weise macht die Gruppe der ARIMA-Methoden aus.

ETS und ARIMA werden heutzutage häufig für Vorhersagen des Energiebedarfs und viele andere Vorhersageprobleme genutzt. In vielen Fällen wird eine Kombination verwendet, um Ergebnisse mit hoher Genauigkeit zu liefern.

**Allgemeine Mehrfachregression:** Regressionsmodelle sind vermutlich der wichtigste Modellierungsansatz im Bereich von Machine Learning und der Statistik. Im Zusammenhang mit Zeitreihen verwenden wir die Regression zum Vorhersagen zukünftiger Werte (z. B. des Bedarfs). Bei der Regression nutzen wir eine lineare Kombination der Prädiktoren und ermitteln die Gewichtungen (auch als Koeffizienten bezeichnet) dieser Prädiktoren während des Trainingsprozesses. Das Ziel ist die Erstellung einer Regressionsgeraden, mit der der Wert vorhergesagt werden kann. Regressionsmethoden sind gut geeignet, wenn die Zielvariable numerisch ist, also auch für die Zeitreihenvorhersage. Es gibt viele verschiedene Regressionsmethoden. Dies können sehr einfache Regressionsmodelle sein, z. B. [Lineare Regression](https://en.wikipedia.org/wiki/Linear_regression), oder anspruchsvollere Regressionsmodelle, z. B. Entscheidungsbäume, [Random Forests](https://en.wikipedia.org/wiki/Random_forest), [Neuronale Netze](https://en.wikipedia.org/wiki/Artificial_neural_network) und Boosted Decision Trees (verstärkte Entscheidungsbäume).

Mit der Erstellung einer Vorhersage des Energiebedarfs als Regressionsproblem erhalten wir ein hohes Maß an Flexibilität beim Auswählen der Datenfeatures, die aus den tatsächlichen Zeitreihendaten des Bedarfs und aus externen Faktoren, z. B. der Temperatur, kombiniert werden können. Weitere Informationen zu den ausgewählten Features werden im Abschnitt zur Featureentwicklung (siehe **Datenvorbereitung und Featureentwicklung**) dieses Playbooks beschrieben.

Aufgrund unserer Erfahrung in Bezug auf die Implementierung und Bereitstellung von Pilotprojekten für die Vorhersage des Energiebedarfs haben wir Folgendes herausgefunden: Die erweiterten Regressionsmodelle, die in Azure ML verfügbar sind, führen meistens zu den besten Ergebnissen, und wir setzen diese Modelle ein.

## Modellauswertung
Die Modellauswertung spielt im **Modellentwicklungszyklus** eine wichtige Rolle. In diesem Schritt geht es um die Überprüfung des Modells und seine Leistung in Bezug auf echte Daten. Während des Modellierungsschritts nutzen wir einen Teil der verfügbaren Daten zum Trainieren des Modells. Während der Auswertungsphase nutzen wir die restlichen Daten, um das Modell zu testen. In praktischer Hinsicht bedeutet dies, dass wir das Modell mit neuen Daten füttern, die neu strukturiert wurden und die gleichen Features wie das Trainingsdataset enthalten. Allerdings verwenden wir das Modell während des Überprüfungsvorgangs, um die Zielvariable vorherzusagen, anstatt die verfügbare Zielvariable bereitzustellen. Wir bezeichnen diesen Prozess häufig als Modellbewertung. Anschließend verwenden wir die richtigen Zielwerte und vergleichen diese mit den vorhergesagten Werten. Das Ziel hierbei ist die Messung und Reduzierung des Vorhersagefehlers, also des Unterschieds zwischen den Vorhersagen und dem richtigen Wert. Eine Quantifizierung der Fehlermessung ist wichtig, da wir das Modell feinabstimmen und überprüfen möchten, ob der Fehlerwert tatsächlich abnimmt. Die Feinabstimmung des Modells kann durchgeführt werden, indem Modellparameter geändert werden, mit denen der Lernprozess gesteuert wird, oder indem Datenfeatures (als [Parameters Sweep](https://channel9.msdn.com/Blogs/Windows-Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep) bezeichnet) hinzugefügt oder entfernt werden. In praktischer Hinsicht bedeutet dies, dass wir mehrfach zwischen den Phasen Featureentwicklung, Modellierung und Modellauswertung wechseln müssen, bis es möglich ist, den Fehler auf die erforderliche Ebene zu senken.

Es ist wichtig zu betonen, dass der Vorhersagefehler niemals null sein kann, da es kein Modell gibt, mit dem jedes Ergebnis perfekt vorhergesagt werden kann. Es gibt aber eine bestimmte Fehlergrößenordnung, die für ein Unternehmen jeweils akzeptabel ist. Während des Überprüfungsvorgangs möchten wir sicherstellen, dass der Vorhersagefehler unseres Modells der Toleranzebene des Unternehmens entspricht oder einen noch besseren Wert aufweist. Daher ist es wichtig, die Ebene des tolerierbaren Fehlers während der Phase der **Problemformulierung** am Anfang des Zyklus festzulegen.

### Häufige Evaluierungsverfahren
Es gibt verschiedene Möglichkeiten, den Vorhersagefehler zu messen und zu quantifizieren. In diesem Abschnitt geht es hauptsächlich um Evaluierungsverfahren, die für Zeitreihen und speziell für die Vorhersage des Energiebedarfs relevant sind.

#### [**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE steht für Mean Absolute Percentage Error. Per MAPE berechnen wir die Differenz zwischen den einzelnen vorhergesagten Punkten und dem tatsächlichen Wert eines Punkts. Anschließend quantifizieren wir den Fehler pro Punkt, indem wir den Anteil der Differenz relativ zum tatsächlichen Wert berechnen. Im letzten Schritt bilden wir den Mittelwert aus diesen Werten. Die für MAPE verwendete mathematische Formel lautet:

![MAPE-Formel](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png) *Hierbei ist A<sub>t</sub> der tatsächliche Wert, F<sub>t</sub> ist der vorhergesagte Wert, und n ist der Vorhersagehorizont.*

## Bereitstellung
Nachdem wir die Modellierungsphase abgeschlossen und die Modellleistung überprüft haben, sind wir bereit für die Bereitstellungsphase. Mit der Bereitstellung ist hierbei Folgendes gemeint: Für den Kunden wird die Möglichkeit geschaffen, das Modell zu nutzen, indem damit tatsächlich in größerem Umfang Vorhersagen erstellt werden. Das Konzept der Bereitstellung ist in Azure ML entscheidend, da unser Hauptziel darin besteht, ständig Vorhersagen auszulösen, und nicht nur Erkenntnisse aus den Daten zu gewinnen. Die Bereitstellungsphase ist der Teil, in dem das Modell für die Nutzung in großem Umfang bereitgestellt wird.

Im Zusammenhang mit der Vorhersage des Energiebedarfs besteht das Ziel darin, ständige und regelmäßige Vorhersagen auszulösen und gleichzeitig sicherzustellen, dass für das Modell neue Daten verfügbar sind und dass die vorhergesagten Daten zurück an den Verbraucher gesendet werden.

### Bereitstellung von Webdiensten
Der wichtigste Baustein in Azure ML für die Bereitstellung ist der Webdienst. Dies ist die effektivste Möglichkeit, die Nutzung eines Vorhersagemodells in der Cloud zu realisieren. Der Webdienst kapselt das Modell und umschließt es mit einer [RESTful](http://www.restapitutorial.com/)-API (Application Programming Interface). Die API kann als Teil von beliebigem Clientcode verwendet werden. Dies ist im Diagramm unten dargestellt.

![Webdienstbereitstellung und Verbrauch](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Sie sehen, dass der Webdienst in der Cortana Analytics Suite-Cloud bereitgestellt wird und über den verfügbar gemachten REST-API-Endpunkt aufgerufen werden kann. Mit anderen Arten von Clients aus unterschiedlichen Bereichen kann der Dienst gleichzeitig über die Web-API aufgerufen werden. Außerdem kann der Webdienst so skaliert werden, dass er Tausende von gleichzeitigen Aufrufen unterstützt.

### Typische Lösungsarchitektur
Beim Bereitstellen einer Lösung für die Vorhersage des Energiebedarfs sind wir an der Bereitstellung einer End-to-End-Lösung interessiert, die über den Vorhersage-Webdienst hinausgeht und den gesamten Datenfluss ermöglicht. Beim Auslösen einer neuen Vorhersage müssen wir sicherstellen, dass das Modell mit den aktuellen Datenfeatures gefüttert wird. Dies bedeutet, dass die neu erfassten Rohdaten ständig erfasst, verarbeitet und in den erforderlichen Featuresatz transformiert werden, auf dessen Grundlage das Modell erstellt wurde. Gleichzeitig möchten wir die vorhergesagten Daten für die Endverbraucher verfügbar machen. Ein Beispiel für einen Datenflusszyklus (bzw. eine Datenpipeline) ist im folgenden Diagramm dargestellt:

![Vorhersage des Energiebedarfs – End-to-End-Datenfluss](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Dies sind die Schritte, die im Rahmen des Vorhersagezyklus für den Energiebedarf ausgeführt werden:
1.  Mit Millionen von bereitgestellten Datenmessgeräten werden ständig in Echtzeit Daten zum Stromverbrauch generiert.
2.  Diese Daten werden gesammelt und in ein Cloudrepository (z. B. Azure Blob) hochgeladen.
3.  Vor der Verarbeitung werden die Rohdaten für eine Unterstation oder eine regionale Ebene aggregiert, die vom Unternehmen festgelegt wird.
4.  Anschließend wird die Featureverarbeitung (siehe **Datenvorbereitung und Featureentwicklung**) durchgeführt, und es werden die Daten erzeugt, die für das Training oder die Bewertung erforderlich sind. Die Daten des Featuresatzes werden in einer Datenbank gespeichert (z. B. SQL Azure).
5.  Der Dienst für das erneute Training wird aufgerufen, um das Vorhersagemodell erneut zu trainieren. Die aktualisierte Version des Modells wird zum dauerhaften Modell, damit es vom Webdienst für die Bewertung verwendet werden kann.
6.  Der Webdienst für die Bewertung wird gemäß einem Zeitplan aufgerufen, der zur erforderlichen Vorhersagehäufigkeit passt.
7.  Die vorhergesagten Daten werden in einer Datenbank gespeichert, auf die vom Verbraucherclient zugegriffen werden kann.
8.  Der Verbraucherclient ruft die Vorhersagen ab, wendet sie auf das Netz an und nutzt sie in Übereinstimmung mit dem erforderlichen Anwendungsfall.

Es ist wichtig zu beachten, dass dieser gesamte Zyklus vollständig automatisiert ist und nach einem Zeitplan ausgeführt wird. Die gesamte Orchestrierung dieses Datenzyklus kann mit Tools wie [Azure Data Factory](http://azure.microsoft.com/services/data-factory/) durchgeführt werden.

### End-to-End-Bereitstellungsarchitektur
Für die praktische Bereitstellung einer Lösung zum Vorhersagen des Energiebedarfs unter Cortana Analytics müssen wir sicherstellen, dass die erforderlichen Komponenten richtig eingerichtet und konfiguriert werden.

Im folgenden Diagramm ist eine typische Architektur auf Basis von Cortana Analytics dargestellt, bei der der oben beschriebene Datenflusszyklus implementiert und orchestriert wird:

![End-to-End-Bereitstellungsarchitektur](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Weitere Informationen zu den einzelnen Komponenten und zur gesamten Architektur finden Sie unter der „Vorlage für Energielösungen“.

<!---HONumber=AcomDC_0316_2016-->