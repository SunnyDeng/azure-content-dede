<properties
   pageTitle="Grundlegendes zu Microservices | Microsoft Azure"
   description="Sie erhalten einen Überblick, warum das Erstellen von Cloudanwendungen mit einem Microservice-Ansatz für die moderne Anwendungsentwicklung wichtig ist und wie Azure Service Fabric dafür eine Plattform bereitstellt."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/18/2015"
   ms.author="mfussell"/>

# Gründe für einen Microservice-Ansatz zum Erstellen von Anwendungen
Für Softwareentwickler gibt es nichts Neues, was das Einbeziehen einer Anwendung in Komponententeile betrifft. Es ist ein zentrales Paradigma der Objektorientierung, von Softwareabstrahierungen und der „Komponentisierung“. Heutzutage erfolgt diese Einbeziehung meist in Form von Klassen und Schnittstellen zwischen gemeinsam genutzten Bibliotheken und Technologieschichten. In der Regel wird ein abgestufter Ansatz mit Back-End-Speicher, Geschäftslogik auf mittlerer Ebene und Front-End-UI genutzt. Was sich im Laufe der letzten Jahre geändert hat, ist die Tatsache, dass wir als Entwickler verteilte Anwendungen für die Cloud erstellen, weil dies geschäftlich geboten ist.

Veränderte geschäftliche Anforderungen sind:

- Dienste müssen in größerem Umfang erstellt und betrieben werden, um eine höhere Kundenreichweite zu erzielen, z. B. in neuen geografischen Regionen oder ohne erforderliche Bereitstellung an Kundenstandorten.
- Schnellere Bereitstellung von Features und Funktionen, damit flexibel auf Kundenanforderungen reagiert werden kann.
- Verbesserte Ressourcenauslastung zur Kostenreduzierung

Es sind diese geschäftlichen Anforderungen, die beeinflussen, *wie* wir Anwendungen erstellen.

## Vergleich des monolithischen Entwurfs und Microservice-Entwurfs
Alle Anwendungen entwickeln sich im Laufe der Zeit weiter. Erfolgreiche Anwendungen entwickeln sich weiter, indem Sie für die Benutzer nützlich sind. Nicht erfolgreiche Anwendungen werden nicht weiterentwickelt und schließlich ausgesondert. Es stellt sich nur die Frage: Wie viel wissen Sie über Ihre heutigen Anforderungen und deren zukünftige Entwicklung? Wenn Sie beispielsweise eine Berichterstellungsanwendung für eine Abteilung erstellen und sicher sind, dass sie nur für Ihr Unternehmen bestimmt ist und die Berichte nur eine kurze Lebensdauer haben, wählen Sie einen anderen Ansatz als bei der Erstellung eines Diensts für die Bereitstellung von Videoinhalten für Millionen von Kunden. In einigen Fällen ist der treibende Faktor, dass ein Projekt als Machbarkeitsstudie in dem Wissen erstellt werden kann, das der Entwurf der Anwendung später geändert werden kann. Es ist nicht sehr sinnvoll, zu viel Engineeringaufwand für etwas zu betreiben, was niemals verwendet wird. Dies ist der übliche Kompromiss, der beim Engineering häufig eingegangen wird. Wenn Unternehmen von der Erstellung für die Cloud sprechen, wird aber auch Wachstum und gute Auslastung erwartet. Das Problem besteht darin, dass Wachstum und Skalierung unvorhersagbar sind. Daher möchten wir einerseits die Prototypphase schnell erreichen und andererseits sichergehen können, dass wir uns auf dem richtigen Weg zu zukünftigem Erfolg befinden. Dies entspricht dem schlanken Startup-Ansatz: Erstellen, Messen, Lernen, Wiederholen.

Während der Client/Server-Ära haben wir eher Anwendungen mit mehreren Ebenen erstellt und auf jeder Ebene spezielle Technologie verwendet. Hierfür hat sich der Begriff „monolithische Anwendung“ etabliert, da sich die Schnittstellen meist zwischen den Ebenen befanden und innerhalb einer Ebene normalerweise ein Design mit engerer Kopplung zwischen den Komponenten genutzt wurde. Die Entwickler haben für den Entwurf und die Einbindung also Klassen verwendet, die zu Bibliotheken kompiliert wurden, und diese über einige EXE- und DLL-Dateien verknüpft. Dieser monolithische Entwurfsansatz hat auch Vorteile. Das Entwerfen kann einfacher sein, und Aufrufe zwischen Komponenten können schneller durchgeführt werden, da sie häufig über IPC ablaufen. Es wird jeweils ein einzelnes Produkt getestet, was in der Regel zu einer höheren Effizienz beim Mitarbeitereinsatz führt. Der Nachteil besteht darin, dass die Anwendung in den mehrstufigen Schichten eng gekoppelt ist. Es ist nicht möglich, einzelne Komponenten zu skalieren. Wenn Sie Fehlerbehebungen oder Upgrades durchführen möchten, müssen Sie warten, bis andere Benutzer ihre Tests beendet haben. Hierdurch werden flexible Reaktionen erschwert.

Microservices stellen die Antwort auf diese Nachteile dar, und sie sind enger an die oben beschriebenen geschäftlichen Anforderungen angelehnt. Aber auch sie haben sowohl Vor- als auch Nachteile. Die Vorteile von Microservices sind: Jeder Microservice kapselt normalerweise einfachere Geschäftsfunktionen, und sie können einzeln hoch- und herunterskaliert, getestet, bereitgestellt und verwaltet werden. Folgendes ist wichtig: Die Vorteile eines Microservice-Ansatzes sorgen dafür, dass Teams eher auf das Geschäftsszenario ausgerichtet sind, anstatt auf die Technologie (was durch den Ansatz mit mehreren Ebenen gefördert wurde). Für die Praxis bedeutet dies, dass kleinere Teams den Microservice basierend auf einem Kundenszenario entwickeln und die Technologie ihrer Wahl einsetzen. Anders ausgedrückt: Das Unternehmen muss in Bezug auf die Technologie keine Standardisierung durchführen, um seine monolithische Struktur zu verwalten. Einzelne Teams im Besitz von Diensten können das tun, was jeweils sinnvoll ist – entweder basierend auf den Kenntnissen des Teams oder der besten Lösung für das Problem, das mit dem Dienst gelöst werden soll. In der Praxis ist es natürlich vorzuziehen, über eine Gruppe empfohlener Technologiekomponenten zu verfügen, z. B. per Auswahl eines bestimmten NoSql-Speichers oder eines Webanwendungs-Frameworks. Die Nachteile von Microservices sind die Verwaltung der höheren Anzahl von separaten Entitäten, die komplexeren Bereitstellungen, die Versionsverwaltung, mehr Netzwerkdatenverkehr zwischen den Microservices und die entsprechende Netzwerklatenz. Wenn eine hohe Zahl von sehr präzisen Diensten mit vielen Einzelaufrufen vorhanden ist, kann dies in Bezug auf die Leistung zu einem Alptraum werden. Und ohne Tools zum Anzeigen dieser Abhängigkeiten ist es nicht einfach, das gesamte System zu „sehen“. Letztendlich funktioniert der Microservice-Ansatz aufgrund von Standards. Es wird vereinbart, wie kommuniziert wird und dass nur die Dinge toleriert werden, die Sie von einem Dienst benötigen, anstatt starre Verträge zu nutzen. Es ist wichtig, diese Kontakte im Voraus im Design festzulegen, da die Dienste unabhängig voneinander aktualisiert werden. Eine weitere Beschreibung, die für das Entwerfen mit einem Microservices-Ansatz gilt, ist „SOA mit feiner Abstufung“ (fine-grained SOA).


***Einfach ausgedrückt geht es beim Microservice-Entwurfsansatz um einen entkoppelten Verbund von Diensten mit unabhängigen Änderungen und vereinbarten Kommunikationsstandards.***


Mit der steigenden Zahl von Cloud-Apps entdecken immer mehr Entwickler, dass diese Unterteilung der Gesamt-App in unabhängige Dienste für bestimmte Szenarien den besseren langfristigen Ansatz darstellt.
## Vergleich zwischen Ansätzen zur Anwendungsentwicklung

![Service Fabric-Plattform][Image1]

(1) Eine monolithische App enthält domänenspezifische Funktionen und ist normalerweise in Funktionsebenen unterteilt, z. B. Web, Business und Daten.

(2) Sie skalieren monolithische Apps, indem Sie sie über mehrere Server/VMs/Container hinweg klonen.

(3) Bei einer Microservice-Anwendung werden die Funktionen auf separate kleinere Dienste aufgeteilt.

(4) Bei diesem Ansatz erfolgt das horizontale Hochskalieren, indem jeder Dienst unabhängig bereitgestellt wird und Instanzen dieser Dienste über Server/VMs/Container hinweg erstellt werden.


Das Entwerfen eines Microservice-Ansatzes ist kein Patentrezept für alle Projekte. Dieser Ansatz ist jedoch enger an die oben beschriebenen geschäftlichen Ziele angelehnt. Die anfängliche Verwendung eines monolithischen Ansatzes kann auch akzeptabel sein, wenn Sie wissen, dass Sie später die Gelegenheit haben, bei Bedarf den Code zu überarbeiten und zu einem Micoservice-Entwurf zu wechseln. Es ist aber häufiger der Fall, dass Sie über eine monolithische App verfügen und diese nach und nach langsam aufbrechen können, indem Sie mit den Funktionsbereichen beginnen, die besser skalierbar oder flexibler gestaltet werden müssen.

Zusammenfassung: Der Microservice-Ansatz besteht darin, Ihre Anwendung aus vielen kleineren Diensten mit einer Ausführung in Containern zusammenzusetzen, die für einen Cluster mit Computern bereitgestellt werden. Jeder Dienst wird von einem kleineren Team für bestimmte Szenarien entwickelt. Zudem wird jeder Dienst einzeln getestet, versioniert, bereitgestellt und skaliert, um die Anwendung als Ganzes weiterzuentwickeln.

## Was ist ein Microservice?

Es gibt unterschiedliche Definitionen von Microservices, und im Internet finden Sie viele gute Ressourcen mit eigener Sichtweise und Definition. Die meisten der folgenden Merkmale werden aber immer genannt:

- Kapseln eines Kunden- oder Geschäftsszenarios Welches Problem möchten Sie lösen?
- Durchführen der Entwicklung in einem kleinen Engineering-Team
- Verwenden einer beliebigen Programmiersprache und eines beliebigen Frameworks 
- Besteht aus Code (optional: wird einzeln versioniert, bereitgestellt und skaliert)
- Interagiert mit anderen Microservices über gut definierte Schnittstellen und Protokolle 
- Verfügt über einen eindeutigen Namen (URL), der zum Auflösen des Speicherorts verwendet werden kann
- Bleibt beim Auftreten von Fehlern konsistent und verfügbar

Sie können dies wie folgt zusammenfassen:

***Microservice-Anwendungen bestehen aus kleinen, einzeln versionierten und skalierbaren Diensten für Kunden, die miteinander über Standardprotokolle mit gut definierten Schnittstellen kommunizieren.***


Wir haben die ersten beiden Punkte oben im vorherigen Abschnitt behandelt, und jetzt führen wir dies weiter und klären die restlichen Punkte.

### Verwenden einer beliebigen Programmiersprache und eines beliebigen Frameworks
Als Entwickler sollten Sie frei wählen können, welche Sprache oder welches Framework wir verwenden möchten, und zwar je nach Ihren Fähigkeiten bzw. den Anforderungen des Diensts. Bei einigen Diensten kann es sein, dass die Leistungsvorteile von C++ im Vordergrund stehen, während für andere Dienste die Einfachheit der verwalteten Entwicklung in C# oder Java am wichtigsten sein kann. In einigen Fällen müssen Sie unter Umständen eine bestimmte Drittanbieterbibliothek, Datenspeichertechnologie oder Einrichtung zum Verfügbarmachen des Diensts für Clients verwenden.

Nachdem wir uns für eine Technologie entschieden haben, geht es mit der Betriebs- oder Lebenszyklusverwaltung und der Skalierung des Diensts weiter.

### Code und Zustand mit unabhängiger Versionierung, Bereitstellung und Skalierung  

Wie immer Sie Ihre Microservices auch schreiben: Jeder Microservice sollte einzeln bereitgestellt, aktualisiert und skaliert werden, und zwar in Bezug auf den Code und optional auch in Bezug auf den Zustand. Dies ist eines der schwierigeren Probleme, die gelöst werden müssen. Hierbei geht es nämlich um die Wahl der Technologie und bei der Skalierung um das Wissen, wie die Partitionierung (Sharding) sowohl für den Code als auch den Zustand gestaltet werden soll. Wenn für den Code und den Zustand separate Technologien verwendet werden (was heutzutage häufiger der Fall ist), müssen die Bereitstellungsskripts für Ihren Microservice die Skalierung von beidem bewältigen können. Es geht auch um die Flexibilität, nur einige Microservices aktualisieren zu können, ohne dass alle auf einmal aktualisiert werden müssen. Wir kehren noch einmal kurz zum Vergleich des monolithischen Ansatzes mit dem Microservice-Ansatz zurück. Im Diagramm unten sind die Unterschiede beim Speichern des Zustands dargestellt.

### Speichern des Zustands für unterschiedliche Anwendungsstile
![Service Fabric-Plattform][Image2]

***Auf der linken Seite ist der monolithische Ansatz mit Einzeldatenbank und den speziellen Technologieebenen zu sehen.***

***Auf der rechten Seite ist der Microservice-Ansatz dargestellt. Es ist ein Graph mit verbundenen Microservices, wobei der Zustand meist auf den Microservice beschränkt ist und verschiedene Technologien verwendet werden.***

Bei einem monolithischen Ansatz wird von der Anwendung in der Regel eine Einzeldatenbank verwendet. Der Vorteil besteht darin, dass ein einzelner Speicherort verwendet und die Bereitstellung vereinfacht wird. Jede Komponente kann eine einzelne Tabelle zum Speichern des Zustands enthalten. Der schwierigste Teil ist, dass Teams beim Trennen der Zustände streng vorgehen müssen. Es besteht natürlich die Gefahr, dass einer vorhandenen Kundentabelle einfach eine neue Spalte hinzugefügt wird, Tabellen verknüpft werden und Abhängigkeiten generell auf Speicherebene erstellt werden. Wenn dies passiert, können Sie die Skalierung einzelner Komponenten vergessen. Beim Microservices-Ansatz verwaltet und speichert jeder Dienst seinen eigenen Zustand. Dies bedeutet, dass er den Code und den Zustand zusammen skalieren muss, um die Anforderungen des Diensts zu erfüllen. Dies hat aber einen Nachteil, wenn Sie Sichten oder Abfragen für die Daten Ihrer Anwendung erstellen müssen. Sie müssen dann nämlich Abfragen über diese verteilten Zustandsspeicher durchführen. Normalerweise wird dies mit einem separaten Microservice gelöst, mit dem eine Sicht für eine Sammlung von Microservices erstellt wird. Falls Sie für die Daten mehrere Ad-hoc-Abfragen durchführen müssen, sollten Sie für jeden Microservice erwägen, dass dessen Daten für die Offlineanalyse in einen Data Warehousing-Dienst geschrieben werden.


Die Versionsverwaltung gilt speziell für die bereitgestellte Version des Microservice und ist erforderlich, damit mehrere unterschiedliche Versionen eingeführt und parallel ausgeführt werden können. Mit der Versionsverwaltung werden die Szenarien gelöst, in denen für eine neuere Version eines Microservice während des Upgrades ein Fehler auftritt und ein Rollback zur vorherigen Version durchgeführt werden muss. Das andere Szenario für die Versionsverwaltung ist die Durchführung von A/B-Tests, bei denen unterschiedliche Benutzer unterschiedliche Versionen des Diensts erhalten. Es ist beispielsweise üblich, einen Microservice für eine bestimmte Gruppe von Kunden zu aktualisieren, um neue Funktionen vor dem größeren Rollout zu testen. Nach der Lebenszyklusverwaltung von Microservices geht es nun mit der Kommunikation weiter.


### Interagiert mit anderen Microservices über gut definierte Schnittstellen und Protokolle

Zu diesem Thema gibt es nicht viel zu tun, bis auf das Lesen der umfangreichen Literatur zur SOA, die in den letzten zehn Jahren veröffentlicht wurde. Darin ging es hauptsächlich um Kommunikationsmuster. Im Allgemeinen ist dies heutzutage auf die Wahl eines REST-Ansatzes mit HTTP- und TCP-Protokollen sowie XML oder JSON als Serialisierungsformat beschränkt. Aus Sicht der Benutzeroberfläche geht es hierbei um den Webdesignansatz. Es hindert Sie aber nichts daran, binäre Protokolle oder eigene Datenformate zu verwenden. Seien Sie aber darauf vorbereitet, dass die Verwendung Ihrer Microservices für Benutzer schwieriger ist, wenn diese öffentlich verfügbar sind.

### Verfügt über einen eindeutigen Namen (URL), der zum Auflösen des Speicherorts verwendet werden kann

Wir erwähnen immer wieder, dass der Microservice-Ansatz Ähnlichkeiten mit dem Web aufweist. Wie das Web auch, muss Ihr Microservice unabhängig vom Ort der Ausführung adressierbar sein. Wenn Sie anfangen, sich Gedanken über Computer und den Ort der Ausführung bestimmter Microservices zu machen, nimmt dies kein gutes Ende. Ebenso wie der DNS eine bestimmte URL für einen bestimmten Computer auflöst, muss Ihr Microservice über einen eindeutigen Namen verfügen, damit ermittelt werden kann, wo er sich gerade befindet. Daher sind für Microservices adressierbare Namen erforderlich, damit sie unabhängig von der Infrastruktur sind, auf der sie ausgeführt werden. Dies impliziert natürlich, dass eine Interaktion zwischen der Bereitstellung und der Ermittlung Ihres Diensts besteht, weil eine Dienstregistrierung vorhanden sein muss. Ebenso muss eine Interaktion zwischen dem Zeitpunkt des Auftretens von Computerfehlern und den Auswirkungen auf Ihren Microservice sichergestellt sein, damit die Registrierung Sie informieren kann, wo der Dienst ausgeführt wird. Dies bringt uns zum nächsten Thema: Resilienz (Ausfallsicherheit) und Konsistenz.
 
### Bleibt beim Auftreten von Fehlern konsistent und verfügbar

Die Behandlung unerwarteter Fehler ist eines der schwierigsten Probleme, die in einem verteilten System gelöst werden müssen. Ein Großteil des Codes, den wir als Entwickler schreiben, dient dem Behandeln von Ausnahmen. Hierfür fällt auch der meiste Testaufwand an. Das Problem ist aber komplexer als das Schreiben von Code zum Behandeln von Fehlern. Was passiert, wenn der Computer ausfällt, auf dem der Microservice ausgeführt wird? Sie müssen diesen Microservice-Fehler nicht nur entdecken (was schon allein ein Problem darstellt), sondern Sie benötigen auch einen Mechanismus, mit dem der Microservice neu gestartet wird. Ein Microservice muss ausfallsicher sein, und zur Sicherstellung der Verfügbarkeit muss es möglich sein, ihn häufig auf einem anderen Computer neu zu starten. Hierbei ist auch von Belang, welcher Zustand für den Microservice gespeichert wurde. Wo kann dieser Zustand wiederhergestellt werden, und ist der Neustart erfolgreich? Anders ausgedrückt: Die Ausfallsicherheit muss sowohl für den Compute-Bereich (der Prozess wird neu gestartet) als auch für den Zustand oder die Daten (kein Datenverlust und Wahrung der Datenkonsistenz) gelten.

Die mit der Ausfallsicherheit verbundenen Probleme werden bei anderen Szenarien noch verstärkt, z. B. beim Auftreten von Fehlern während eines Anwendungsupgrades. Der Microservice, der zusammen mit dem Bereitstellungssystem eingesetzt wird, muss nicht nur wiederhergestellt werden können. Es muss auch entschieden werden, ob der Schritt zur neueren Version möglich ist oder stattdessen ein Rollback auf die vorherige Version durchgeführt werden muss, um die Konsistenz sicherzustellen. Beispielsweise müssen folgende Fragen beantwortet werden: Sind genügend Computer verfügbar, um die neue Version einzuführen, und wie werden vorherige Versionen des Microservice wiederhergestellt? Vom Microservice müssen Informationen zur Integrität bereitgestellt werden, damit diese Entscheidungen getroffen werden können.

### Melden der Integrität und Diagnose

Es ist eigentlich offensichtlich und wird trotzdem oft übersehen, dass der Microservice unbedingt seine Integrität und den Diagnosezustand melden muss. Andernfalls sind aus betrieblicher Sicht nur wenig Einblicke möglich. Die Herausforderung besteht darin, die Korrelation von Diagnoseereignissen über eine Gruppe von unabhängigen Diensten hinweg zu erkennen. Für jeden wird eine unabhängige Protokollierung durchgeführt, und Abweichungen bei den Computeruhren müssen berücksichtigt werden, um die richtige Ereignisreihenfolge zu ermitteln. Genauso wie Sie mit einem Microservice über vereinbarte Protokolle und Datenformate interagieren, ist für Folgendes eine Standardisierung erforderlich: die Protokollierung von Integritäts- und Diagnosereignissen, die letztendlich in einem Ereignisspeicher landen, der abgefragt und angezeigt werden kann. Bei einem Microservice-Ansatz ist es wichtig, dass sich die unterschiedlichen Teams auf ein gemeinsames Protokollierformat einigen. Zum Anzeigen von Diagnoseereignissen in der gesamten Anwendung muss ein einheitlicher Ansatz gewählt werden.

Die Integrität unterscheidet sich von der Diagnose. Bei der Integrität geht es darum, dass der Microservice seinen aktuellen Zustand meldet, damit Maßnahmen ergriffen werden können. Am offensichtlichsten ist hierbei die Verwendung der Upgrade- und Bereitstellungsmechanismen, um die Verfügbarkeit zu wahren. Beispielsweise kann ein Dienst aufgrund eines Prozessabsturzes oder eines Computerneustarts derzeit unter Umständen keine volle Integrität aufweisen, aber trotzdem noch betriebsbereit sein. Sie sollten auf jeden Fall vermeiden, diesen Zustand zu verschlimmern, indem Sie ein Upgrade durchführen. Die beste Vorgehensweise besteht darin, zuerst eine Untersuchung durchzuführen oder dem Microservice Zeit zu lassen, den Fehler zu beheben. Anhand von Integritätsereignissen eines Microservice können wir daher fundierte Entscheidungen treffen und quasi selbstheilende Dienste erstellen.

## Service Fabric als Microservice-Plattform

Service Fabric ist entstanden, als Microsoft die Umstellung von „Kartonprodukten“, die meist monolithisch waren, auf Dienste vollzogen hat. Grundlage war hauptsächlich die Erfahrung mit der Erstellung und dem Betrieb großer Dienste, z. B. Azure SQL-Datenbanken, DocumentDB und anderen wichtigen Azure-Diensten. Wir haben die geschäftlichen Anforderungen in Bezug auf Skalierung, Flexibilität und unabhängige Teams vollständig erfüllt und die Entwicklung der Plattform im Laufe der Zeit begleitet, während sie von immer mehr Diensten übernommen wurde. Es war wichtig, dass Service Fabric überall ausgeführt werden konnte, nicht nur in Azure, sondern auch in eigenständigen Windows Server-Bereitstellungen.

***Ziel von Service Fabric ist das Lösen der schwierigen Probleme, die mit der Erstellung und Ausführung eines Diensts verbunden sind. Beispiele hierfür sind Fehler, Upgrades und die effiziente Auslastung der Infrastrukturressourcen, damit die Teams geschäftliche Probleme mit einem Microservice-Ansatz lösen können.***

Service Fabric verfügt über zwei grobe Bereiche für die Unterstützung der Anwendungserstellung mit einem Microservice-Ansatz.

- Eine Plattform, die eine Gruppe von Systemdiensten umfasst, mit denen folgende Aufgaben ausgeführt werden: Bereitstellungen, Upgrades, Erkennung und Neustart ausgefallener Dienste, Ermittlung des derzeitigen Ausführungsorts von Diensten, Zustandsverwaltung, Integritätsüberwachung usw. Die verwendeten Systemdienste machen viele der oben beschriebenen Microservice-Merkmale möglich.

-  Die Programmierung von APIs oder Frameworks, damit Sie Anwendungen als Microservices erstellen können. Die bereitgestellten APIs für die Programmierung heißen [Reliable Actors und Reliable Services](service-fabric-choose-framework.md). Sie können natürlich beliebigen Code Ihrer Wahl verwenden, um Ihren Microservice zu erstellen. Aber wenn Sie diese Hilfsmittel verwenden, wird die Aufgabe nicht nur vereinfacht, sondern es findet auch eine tiefere Integration in die Plattform statt. Beispielsweise erhalten Sie entweder Integritäts- und Diagnoseinformationen, oder Sie können die integrierte hohe Verfügbarkeit nutzen.

***Service Fabric ist agnostisch, was die Erstellung des Diensts betrifft, und Sie können eine beliebige Technologie verwenden. Es werden aber integrierte Programmierungs-APIs bereitgestellt, mit denen die Erstellung von Microservices stark vereinfacht wird.***

### Sind Microservices für meine Anwendung geeignet?

Vielleicht. Als immer mehr Teams bei Microsoft gebeten wurden, aus geschäftlichen Gründen Anwendungen für die Cloud zu entwickeln, haben viele die Vorteile eines Microservice-Ansatzes erkannt. Für Bing wird dies für die Suche beispielsweise schon seit Jahren eingesetzt. Für andere Teams war der Ansatz ganz neu. Sie haben gemerkt, dass schwierige Probleme gelöst werden mussten, was nicht zu ihren Stärken zählte. Dies sind die Gründe, warum Service Fabric immer häufiger als Technologie für die Erstellung von Diensten gewählt wird.

Das Ziel von Service Fabric ist die Reduzierung der komplexen Anwendungserstellung durch einen Microservice-Ansatz, damit bei Ihnen nicht mehr so viele teure Überarbeitungen anfallen. Der Ansatz ist wie folgt: Beginnen Sie klein, führen Sie bei Bedarf eine Skalierung durch, mustern Sie Dienste aus, fügen Sie neue hinzu, und entwickeln Sie die Lösung während der Nutzung durch die Kunden weiter. Uns ist auch bewusst, dass in der Praxis noch viele andere Probleme gelöst werden müssen, damit Microservices für die Mehrzahl der Entwickler noch besser zugänglich werden. Container und das Actor-Programmiermodell sind Beispiele für kleine Schritte in diese Richtung, und wir sind uns sicher, dass weitere Innovationen diesen Prozess noch stärker vereinfachen werden. <!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

* Weitere Informationen: 
	* [Übersicht über Service Fabric](service-fabric-overview.md)
	* [Technische Übersicht](service-fabric-technical-overview.md)
* Einrichten der Service Fabric-[Entwicklungsumgebung](service-fabric-get-started.md)
* Auswählen eines [Programmiermodell-Frameworks](service-fabric-choose-framework.md) für Ihren Dienst.

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png

<!---HONumber=Nov15_HO4-->