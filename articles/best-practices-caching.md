<properties
   pageTitle="Anleitungen zum Caching | Microsoft Azure"
   description="Anleitungen zum Caching zur Verbesserung von Leistung und Skalierbarkeit."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/18/2015"
   ms.author="masashin"/>

# Anleitungen zum Caching

![](media/best-practices-caching/pnp-logo.png)

Caching ist eine gängige Methode zur Verbesserung von Leistung und Skalierbarkeit eines Systems durch vorübergehendes Zwischenspeichern häufig verwendeter Daten in einem schnellen Speicher nahe der Anwendung. Wenn sich dieser schnelle Datenspeicher näher an der Anwendung befindet als die ursprüngliche Datenquelle, kann Caching die Antwortzeiten für Clientanwendungen erheblich verbessern und die Daten schneller bereitstellen. Den größten Leistungsgewinn bringt Caching, wenn eine Clientinstanz dieselben Daten wiederholt einliest, insbesondere dann, wenn die Daten relativ statisch sind und der ursprüngliche Datenspeicher relativ zur Geschwindigkeit des Cache langsam oder bereits übermäßig ausgelastet ist oder sich im Netzwerk so weit entfernt von der Anwendung befindet, dass sich der Zugriff darauf durch die Netzwerklatenz verlangsamt.

## Caching in verteilten Anwendungen

Verteilte Anwendungen stellen für das Zwischenspeichern von Daten meist eine oder beide der folgenden Strategien bereit:

- Verwenden eines privaten Cache, in dem Daten lokal auf dem Computer gespeichert werden, auf dem eine Instanz der Anwendung oder des Diensts ausgeführt wird.
- Verwenden eines freigegebenen Cache, der als gemeinsame Datenquelle für mehrere Prozesse und/oder Computer dient.

In beiden Fällen kann das Caching clientseitig (durch den Prozess, der die Benutzeroberfläche für ein System bereitstellt, beispielsweise einen Webbrowser oder eine Desktopanwendung) und/oder serverseitig (durch den Prozess, der die remote ausgeführten Business-Dienste bereitstellt) ausgeführt werden.

### Privates Caching

Die einfachste Art eines Cache ist ein arbeitsspeicherinterner Speicher im Adressbereich eines einzelnen Prozesses, auf den der in diesem Prozess ausgeführte Code direkt zugreift. Der Zugriff auf einen solchen Cache ist sehr schnell, so dass dies eine äußerst effektive Strategie zum Speichern geringer Mengen statischer Daten sein kann, da die Größe eines Cache in der Regel durch den auf dem Hostcomputer zur Verfügung stehenden Arbeitsspeicher beschränkt ist. Wenn im Cache mehr Informationen gespeichert werden müssen, als physisch im Arbeitsspeicher möglich, können Sie zwischengespeicherte Daten auf das lokale Dateisystem schreiben. Der Zugriff auf diese Daten ist langsamer als der Zugriff auf die Daten im Arbeitsspeicher, jedoch schneller und zuverlässiger als der Datenabruf aus einem Netzwerk.

Wenn dieses Modell bei Ihnen von mehreren Instanzen einer Anwendung gleichzeitig verwendet wird, verwendet jede dieser Anwendungsinstanzen einen eigenen unabhängigen Cache mit jeweils eigenen Daten.

Einen Cache können Sie sich als Momentaufnahme der Originaldaten zu einem bestimmten Zeitpunkt in der Vergangenheit vorstellen. Wenn diese Daten nicht statisch sind, steht es zu vermuten, dass die verschiedenen Anwendungsinstanzen jeweils verschiedene Versionen dieser Daten in ihren Caches aufbewahren. Daher kann dieselbe Abfrage in jeder dieser Instanzen unterschiedliche Ergebnisse zurückgeben (siehe Abbildung 1).

![Verwenden eines arbeitsspeicherinternen Cache in verschiedenen Instanzen einer Anwendung](media/best-practices-caching/Figure1.png)

_Abbildung 1: Verwenden eines arbeitsspeicherinternen Cache in verschiedenen Instanzen einer Anwendung_

### Freigegebenes Caching

Ein gemeinsam genutzter bzw. freigegebener Cache verhindert die Diversität der Daten in den einzelnen Instanzencaches, wie es beim arbeitsspeicherinternen Caching der Fall sein kann. Freigegebenes Caching stellt durch das Speichern des Cache an einem dedizierten Ort, in der Regel gehostet durch einen separaten Dienst, sicher, dass verschiedene Anwendungsinstanzen die gleichen zwischengespeicherten Daten sehen (siehe Abbildung 2).

![Verwenden eines freigegebenen Cache\_](media/best-practices-caching/Figure2.png)

_Abbildung 2: Verwenden eines freigegebenen Cache_

Ein wichtiger Vorteil des freigegebenen Cache ist seine Skalierbarkeit. Viele Dienste, die freigegebenen Cache bereitstellen, werden durch einen Servercluster implementiert und verwenden Software, die die Daten innerhalb des Clusters transparent verteilen. Eine Anwendungsinstanz sendet dem Cachedienst eine Anforderung, und die ihm zugrunde liegende Infrastruktur ermittelt den Speicherort innerhalb des Clusters, an dem sich die zwischengespeicherten Daten befinden. Durch weitere Server lässt sich der Cache problemlos skalieren.

Die Nachteile des freigegebenen Cache sind der langsamere Zugriff auf die Daten, da sich der Cache nicht lokal in der Nähe der einzelnen Anwendungsinstanzen befindet, sowie die Notwendigkeit der Implementierung eines separaten Cachediensts, durch die die Lösung ein wenig komplizierter wird.

## Überlegungen zur Verwendung von Caching

Die folgenden Abschnitte gehen näher auf die einzelnen Überlegungen zum Entwurf und der Verwendung eines Cache ein.

### Wann empfiehlt sich das Zwischenspeichern von Daten in einem Cache?

Caching kann die Leistung, Skalierbarkeit und Verfügbarkeit erheblich verbessern. Je größer Ihr Datenvolumen und je mehr Benutzer darauf zugreifen, desto mehr machen sich die Vorteile des Caching durch Reduzierung von Latenzzeiten und Überlastung bemerkbar, die ansonsten zwangsläufig bei sehr vielen gleichzeitigen Anforderungen an ein- und demselben Originaldatenspeicher auftreten. Dies ist beispielsweise der Fall, wenn eine Datenbank nur eine begrenzte Anzahl gleichzeitiger Verbindungen zulässt. Werden die Daten in diesem Fall aber nicht aus der zugrunde liegenden Datenbank, sondern aus einem freigegebenen Cache abgerufen, so kann die Clientanwendung selbst dann noch auf diese Daten zugreifen, wenn die Anzahl der verfügbaren Verbindungen erschöpft ist. Außerdem können die Clientanwendungen, sollte die Datenbank aus welchem Grund auch immer nicht mehr zur Verfügung stehen, nach wie vor auf die im Cache gespeicherten Daten zugreifen.

Caching empfiehlt sich besonders für Daten, die häufig eingelesen werden, sich aber nur selten ändern (der Anteil der Lesezugriffe auf diese Daten überwiegt bei Weitem den Anteil der Schreibzugriffe). Allerdings sollten Sie den Cache nicht als autorisierenden Speicher wichtiger Informationen verwenden. Änderungen, die auf keinen Fall verloren gehen dürfen, sollten immer in einem persistenten Datenspeicher gespeichert werden. Sollte der Cache einmal nicht verfügbar sein, kann Ihre Anwendung über den Datenspeicher weiterhin verwendet werden, ohne dass wichtige Informationen verloren gehen.

### Datentypen und Strategien zum Füllen des Cache

Der Schlüssel für die effektive Verwendung eines Cache liegt darin, möglichst geeignete Daten für eine Zwischenspeicherung zu finden und diese zum richtigen Zeitpunkt zwischenzuspeichern. Die Daten können dem Cache zum Beispiel bei Bedarf beim ersten Abruf durch eine Anwendung hinzugefügt werden, so dass die Anwendung die Daten nur einmal aus dem Datenspeicher abrufen muss. Alle weiteren Zugriffe erfolgen dann über den Cache.

Alternativ kann ein Cache im Voraus teilweise oder vollständig mit Daten gefüllt werden, in der Regel beim Start der Anwendung (diese Vorgehensweise wird als Seeding bezeichnet). Allerdings ist Seeding bei einem großen Cache nicht ratsam, da diese Strategie beim Anwendungsstart eine plötzliche, hohe Last für den ursprünglichen Datenspeicher bedeutet.

Oft hilft eine Analyse der Verwendungsmuster bei der Entscheidung, ob eine vollständige oder teilweise Vorabfüllung des Cache sinnvoll ist und welche Daten zwischengespeichert werden sollen. Vermutlich wäre es zum Beispiel sinnvoll, den Cache mit den statischen Benutzerprofildaten derjenigen Benutzer zu füllen, die die Anwendung regelmäßig (z. B. täglich) verwenden, aber nicht für Benutzer, die die Anwendung nur einmal pro Woche verwenden.

Caching funktioniert in der Regel gut mit Daten, die sich nicht oder nur selten ändern. Beispiele hierfür sind Referenzinformationen wie Produkt- und Preisinformationen einer E-Commerce-Anwendung oder freigegebene statische Ressourcen, deren Erstellung sehr aufwändig ist. Einige oder alle dieser Daten können beim Anwendungsstart in den Cache geladen werden, um die Ressourcenbelastung zu minimieren und die Leistung zu steigern. Auch sind Hintergrundprozesse denkbar, durch die Referenzdaten im Cache regelmäßig aktualisiert werden, um deren Aktualität sicherzustellen, oder durch die der Cache nach einer Änderung der Referenzdaten aktualisiert wird.

Für dynamische Daten ist Caching weniger geeignet, auch wenn es hier Ausnahmen gibt (siehe „Caching hochgradig dynamischer Daten“ weiter unten in diesem Dokument). Wenn sich die Originaldaten häufig ändern, sind die Daten im Cache entweder sehr schnell überholt oder der Aufwand der Synchronisierung des Cache mit dem Originaldatenspeicher steht in keinem Verhältnis zum Nutzen des Cache. Beachten Sie auch, dass ein Cache nicht alle Daten einer Entität vollständig enthalten muss. Stellt ein Datenelement beispielsweise ein Objekt mit mehreren Werten dar, zum Beispiel einen Bankkunden mit Name, Adresse und Kontostand, können einige dieser Elemente statisch sein (Name und Adresse), während sich andere dynamisch ändern (Kontostand). In einer solchen Situation ist es vermutlich sinnvoll, den statischen Anteil der Daten zwischenzuspeichern und die übrigen Informationen nur bei Bedarf abzurufen bzw. neu zu berechnen.

Ob ein Vorabfüllen des Cache, ein On-demand-Laden der Daten oder eine Kombination aus beidem sinnvoll ist, sollte durch Leistungs- und Nutzungsanalysen ermittelt werden. Bei der Entscheidung sollten sowohl die Flüchtigkeit als auch das Nutzungsmuster der Daten eine Rolle spielen. Eine Nutzungs- und Leistungsanalyse für den Cache ist besonders bei stark frequentierten und ausgelasteten Anwendungen wichtig, die hochgradig skalierbar sein müssen. Gerade in hochgradig skalierbaren Szenarien kann es sinnvoll sein, den Cache per Seeding vorab zu füllen, um die Belastung des Datenspeichers zu Spitzenzeiten zu verringern.

Durch Caching lassen sich auch wiederholte Berechnungen während der Laufzeit einer Anwendung vermeiden. Wenn durch eine Operation Daten umgewandelt oder komplizierte Berechnungen durchgeführt werden, können die Ergebnisse der Operation im Cache gespeichert werden. Falls die gleiche Berechnung später erneut erforderlich ist, kann die Anwendung das Ergebnis einfach aus dem Cache abrufen.

Eine Anwendung kann die im Cache gespeicherten Daten ändern, jedoch sollten Sie den Cache als flüchtigen Datenspeicher betrachten, dessen Inhalt jederzeit wieder verloren gehen kann. Wichtige Daten sollten Sie daher nicht nur im Cache, sondern auch im ursprünglichen Datenspeicher speichern. Sollte die Verfügbarkeit des Cache einmal nicht gegeben sein, vermindern Sie auf diese Weise das Risiko eines Datenverlusts.

### Caching hochgradig dynamischer Daten

Wenn Informationen, die sich sehr schnell ändern, in einem persistenten Datenspeicher abgelegt werden, kann dies das System enorm belasten. Denken Sie hier zum Beispiel an ein Gerät, das kontinuierlich einen Status oder ein anderes Messergebnis meldet. Wenn diese Daten in einer Anwendung nicht zwischengespeichert werden, weil die zwischengespeicherten Informationen nahezu sofort schon wieder veraltet sind, gilt diese Überlegung ebenso aber auch für das Speichern der Informationen im persistenten Datenspeicher. Die Daten haben sich zwischen dem Speichern und dem Abrufen eventuell bereits schon wieder geändert. In einer Situation wie dieser sollten Sie überlegen, welche Vorteile das Speichern der dynamischen Informationen im Cache gegenüber dem Speichern im persistenten Datenspeicher hat. Wenn die Daten nicht so wichtig sind und nicht überprüft werden müssen, spielt der gelegentliche Verlust eines Werts sicherlich keine Rolle.

### Verwalten des Datenablaufs in einem Cache

In den meisten Fällen handelt es sich bei den Daten in einem Cache um eine Kopie der Daten im ursprünglichen Datenspeicher. Die Daten im ursprünglichen Datenspeicher können sich jedoch nach dem Zwischenspeichern ändern, wodurch die Daten im Cache veralten. Viele Cachesysteme bieten daher die Möglichkeit, für die Daten im Cache ein Ablaufdatum festzulegen, wodurch sich der Zeitraum reduziert, in dem Daten veraltet vorliegen.

Beim Ablauf zwischengespeicherter Daten werden die Daten aus dem Cache entfernt, und die Anwendung muss die Daten bei Bedarf wieder aus dem ursprünglichen Datenspeicher abrufen (die neu abgerufenen Informationen können dann wieder im Cache gespeichert werden). Zu diesem Zweck können Sie bei der Konfiguration des Cache eine Standardrichtlinie für den Kennwortablauf festlegen. Bei vielen Cachediensten können Sie sogar das Ablaufdatum einzelner Elemente angeben, wenn diese programmgesteuert im Cache gespeichert werden. Dabei ist vorgegeben, ob das Ablaufdatum als absoluter Wert oder auch als gleitender Wert angegeben werden muss. In letzterem Fall wird das Element aus dem Cache entfernt, wenn es innerhalb des angegebenen Zeitraums nicht erneut abgerufen wurde. Durch diese Einstellung wird jede Cache-weite Ablaufrichtlinie überschrieben, allerdings nur für die angegebenen Elemente.

> [AZURE.NOTE]Das Ablaufdatum für den Cache insgesamt sowie für einzelne Elemente muss sorgfältig bedacht werden. Wenn es zu kurz ist, veralten die Elemente zu schnell und werden entfernt, wodurch sich die Vorteile des Cache aufheben. Wenn es zu lang ist, kann es passieren, dass die im Cache vorliegenden Daten nicht mehr aktuell sind.

Wenn die Zeitspanne bis zum Ablauf der Daten sehr lange ist, kann der Cache auch überlaufen. In diesem Fall müssen bei jeder erneuten Anfrage zum Hinzufügen von Daten ältere Elemente meist zwangsweise aus dem Cache entfernt werden. In der Regel entfernen Cachedienste die Daten auf Basis der am längsten zurückliegenden Verwendung (Least Recently Used, LRU), aber meist können Sie diese Richtlinie überschreiben und dadurch verhindern, dass Elemente entfernt werden. Allerdings riskieren Sie bei dieser Einstellung, dass der Cache die Größe des verfügbaren Arbeitsspeichers überschreitet, so dass eine Anwendung, die versucht, ein Element zum Cache hinzufügen, mit einer Ausnahme fehlschlägt.

Einige Cache-Implementierungen bieten auch zusätzliche Entfernungsrichtlinien. Diese schließen in der Regel die MRU-Richtlinie der letzten Verwendung (in der Annahme, dass die Daten nicht mehr benötigt werden), die FIFO-Richtlinie des First-in-First-out (die ältesten Daten werden zuerst entfernt) oder die explizite Entfernung auf Basis eines Auslöseereignisses (z. B. die Änderung der Daten) ein.

### Ungültigmachen der Daten eines clientseitigen Cache

Die Daten in einem clientseitigen Cache liegen in der Regel außerhalb des Einflussbereichs des Diensts, der die Daten auf dem Client bereitgestellt hat. Ein Dienst kann einen Client nicht direkt dazu zwingen, Informationen zu einem clientseitigen Cache hinzuzufügen oder daraus zu entfernen. Dies bedeutet, dass ein Client, der einen schlecht konfigurierten Cache verwendet (z. B. einen Cache mit fehlerhaft implementierten Ablaufrichtlinien), lokal zwischengespeicherte Informationen durchaus auch dann noch verwendet, wenn sie bereits veraltet sind, weil sich die entsprechenden Informationen in der ursprünglichen Datenquelle geändert haben.

Wenn Sie eine Webanwendung entwickeln, die Daten über eine HTTP-Verbindung bereitstellt, können Sie einen Webclient (z. B. einen Browser oder Webproxy) bei der Änderung einer Ressource implizit zwingen, die neuesten Informationen abzurufen, indem Sie die URI dieser Ressource ändern. Webclients verwenden die URI einer Ressource in der Regel als Schlüssel für den clientseitigen Cache, so dass die Änderung der URI bewirkt, dass der Webclient eine zuvor zwischengespeicherte Version der Ressource ignoriert und stattdessen die neue Version abruft.

## Verwalten der Parallelität in einem Cache

Caches sind häufig für mehrere Instanzen einer Anwendung freigegeben. Dabei kann jede Anwendungsinstanz die Daten im Cache lesen und ändern. Die Parallelitätsprobleme eines jeden freigegebenen Datenspeichers gelten daher auch für einen freigegebenen Cache. In Situationen, in denen Anwendungen die im Cache gespeicherten Daten ändern, müssen Sie sicherstellen, dass die Änderungen einer Anwendungsinstanz die Änderungen einer anderen Instanz nicht blind überschreiben.

Je nach Art der Daten und der Wahrscheinlichkeit von Konflikten sollten Sie eine der beiden folgenden Parallelitätsstrategien verfolgen:

- __Optimistisch (vollständig):__ Die Anwendung prüft unmittelbar vor der Aktualisierung, ob sich die Daten im Cache seit deren Abruf geändert haben. Haben sich die Daten nicht geändert, kann die Aktualisierung vorgenommen werden. Andernfalls muss die Anwendung entscheiden, ob die Daten nun aktualisiert werden sollen (die Geschäftslogik hinter dieser Entscheidung ist anwendungsspezifisch). Diese Strategie eignet sich für Situationen, in denen nur selten Änderungen vorgenommen werden bzw. in denen kaum mit Konflikten zu rechnen ist.
- __Pessimistisch (eingeschränkt):__ Beim Abrufen von Daten sperrt die Anwendung die Daten im Cache, um zu verhindern, dass diese durch eine andere Anwendungsinstanz geändert werden. Dadurch werden Konflikte umgangen, allerdings werden andere Anwendungsinstanzen, die die gleichen Daten verarbeitet müssen, dabei unter Umständen blockiert. Die pessimistische Parallelität kann die Skalierbarkeit der Lösung beeinträchtigen und sollte nur für kurzlebige Prozesse verwendet werden. Diese Strategie ist eher geeignet für Situationen, in denen Konflikte wahrscheinlich sind, insbesondere dann, wenn eine Anwendung mehrere Elemente im Cache aktualisiert und sicherstellen muss, dass diese Änderungen konsistent angewendet werden.

### Implementieren einer hohen Verfügbarkeit und Skalierbarkeit mit Verbesserung der Leistung

Ein Cache sollte nicht das primäre Datenrepository sein. Dies sollte der ursprüngliche Datenspeicher bleiben, aus dem der Cache gefüllt wird. Der ursprüngliche Datenspeicher ist für die Persistenz der Daten verantwortlich.

Achten Sie darauf, Ihre Lösungen nicht abhängig von der Verfügbarkeit eines gemeinsam genutzten Cachediensts zu machen. Eine Anwendung sollte auch dann noch funktionsfähig sein, wenn der Dienst, der den freigegebenen Cache bereitstellt, nicht verfügbar ist, d. h., die Anwendung sollte nicht hängen bleiben oder fehlschlagen, solange sie darauf wartet, dass der Cachedienst seinen Betrieb wieder aufnimmt. Die Anwendung muss also in der Lage sein, die Verfügbarkeit des Cachediensts zu erkennen, und bei Bedarf auf den ursprünglichen Datenspeicher zurückgreifen können, wenn der Cache nicht verfügbar ist. Diesem Szenario kommt besonders das [Circuit-Breaker-Muster](http://msdn.microsoft.com/library/dn589784.aspx) entgegen. Der Dienst, der den Cache bereitstellt, kann wiederhergestellt werden, und sobald er verfügbar ist, kann der Cache wieder nach einer Strategie wie dem [Cache-Aside-Muster](http://msdn.microsoft.com/library/dn589799.aspx) aus dem ursprünglichen Datenspeicher gefüllt werden.

Ein Zurückgreifen auf den ursprünglichen Datenspeicher bei einem vorübergehenden Ausfall des Cache zieht aber womöglich ein Skalierbarkeitsproblem für das System nach sich. Während der Wiederherstellung des Cache wird der ursprüngliche Datenspeicher derart mit Datenanforderungen überschwemmt, dass es zu Zeitlimitüberschreitungen und Verbindungsfehlern kommt. In diesem Sinne ist es durchaus sinnvoll, eine Strategie zu überdenken, bei der zusätzlich zum freigegebenen Cache, auf den alle Anwendungsinstanzen zugreifen, für jede Anwendungsinstanz ein lokaler privater Cache implementiert wird. Beim Abrufen eines Elements kann die Anwendung zunächst in ihrem lokalen Cache, dann im freigegebenen Cache und schließlich im ursprünglichen Datenspeicher nachsehen. Der lokale Cache kann mit den Daten des freigegebenen Cache bzw., sollte dieser nicht verfügbar sein, mit den Daten aus der ursprünglichen Datenbank gefüllt werden. Bei dieser Strategie ist eine sorgfältige Konfiguration erforderlich. Es sollte verhindert werden, dass der lokale Cache gegenüber dem freigegebenen Cache zu sehr veraltet, gleichzeitig sollte er aber für den Fall, dass der freigegebene Cache nicht verfügbar ist, als Puffer fungieren. Eine solche Struktur sehen Sie in Abbildung 3.

![Verwenden eines lokalen privaten Cache mit einem freigegebenen Cache\_](media/best-practices-caching/Caching3.png) _Abbildung 3: Verwenden eines lokalen privaten Cache mit einem gemeinsam genutzten Cache_

Zur Unterstützung großer Caches, die relativ langlebige Daten enthalten, bieten einige Cachedienste eine Option für hohe Verfügbarkeit, die für den Fall, dass der Cache nicht verfügbar ist, automatisches Failover implementiert. Diese Strategie schließt in der Regel die Replikation der auf einem primären Cacheserver zwischengespeicherten Daten auf einem sekundären Cacheserver ein, so dass bei einem Ausfall des primären Cacheservers bzw. einem Verlust der Konnektivität sofort zum sekundären Server gewechselt werden kann. Zur Verkürzung der Latenzzeit, die das Schreiben an mehrere Ziele mit sich bringt, kann die Replikation der Daten auf den sekundären Server auch asynchron zum Caching der Daten auf dem primären Server erfolgen. Diese Strategie birgt zwar das Risiko, dass einige der zwischengespeicherten Informationen bei einem Fehler während dieses Prozesses verloren gehen, der Verlustanteil sollte jedoch gegenüber der Gesamtgröße des Cache vernachlässigbar sein.

Zur Verhinderung von Überlastung und Verbesserung der Skalierbarkeit kann es bei einem sehr großen freigegebenen Cache von Vorteil sein, die zwischengespeicherten Daten in Partitionen auf mehrere Knoten aufzuteilen. Viele freigegebene Caches unterstützen die Möglichkeit des dynamischen Hinzufügens (und Entfernens) von Knoten und die Neuverteilung der Daten auf diese Partitionen. Diese Strategie kann Clustering einbeziehen, wobei jedoch die einzelnen Knoten den Clientanwendungen als nahtloser einheitlicher Cache dargestellt werden. Intern aber werden die Daten nach einem vordefinierten Verteilungsmechanismus auf die einzelnen Knoten aufgeteilt, so dass die Last gleichmäßig verteilt wird. Im Dokument [Data Partitioning Guidance](http://msdn.microsoft.com/library/dn589795.aspx) (in englischer Sprache) auf der Microsoft-Website finden Sie weitere Informationen zu möglichen Partitionierungsstrategien.

Clustering kann die Verfügbarkeit des Cache zusätzlich verbessern. Selbst wenn ein Knoten ausfällt, steht der Rest des Cache noch zur Verfügung. Clustering wird häufig in Verbindung mit Replikation und Failover verwendet. Jeder Knoten kann repliziert und das Replikat schnell online geschaltet werden, wenn ein Knoten ausfällt.

Vermutlich beinhalten viele Lese- und Schreibvorgänge nur einzelne Werte oder Elemente. Mitunter wird es aber auch erforderlich sein, große Datenmengen schnell zu speichern oder abzurufen. Das Seeding eines Cache kann beispielsweise Hunderte oder gar Tausende von Elementen umfassen, die alle in den Cache geschrieben werden müssen, oder eine Anwendung muss im Zuge einer Anforderung eine große Anzahl zusammengehöriger Elemente aus dem Cache abrufen. Viele große Caches bieten für diese Zwecke die Möglichkeit eines Batchvorgangs. Dadurch kann eine Clientanwendung eine große Menge an Elementen in eine einzige Anforderung packen und so den durch unzählige kleine Anforderungen verursachten Mehraufwand verringern.

## Caching und letztendliche Konsistenz

Das Cache-Aside-Muster ist davon abhängig, dass die Anwendungsinstanz, die den Cache füllt, Zugang zur aktuellsten und konsistentesten Version der Daten hat. In einem System, das letztendlich Konsistenz bereitstellt (z. B. ein replizierter Datenspeicher), ist dies unter Umständen nicht der Fall. Es kann durchaus passieren, dass eine Anwendungsinstanz ein Datenelement ändert und damit die Version dieses Elements im Cache schon wieder ungültig macht. Eine andere Instanz der Anwendung versucht nun möglicherweise, dieses Element aus dem Cache einzulesen. Dabei kommt es zu einem Cachefehler, so dass die Instanz die Daten aus dem Datenspeicher einliest und dem Cache hinzufügt. Wurde der Datenspeicher allerdings nicht vollständig mit den anderen Replikaten synchronisiert, kann es passieren, dass die Anwendungsinstanz einen alten Wert einliest und diesen veralteten Wert in den Cache speichert.

Weitere Informationen zur Aufrechterhaltung der Datenkonsistenz finden Sie auf der Seite [Einführung in die Datenkonsistenz](http://msdn.microsoft.com/library/dn589800.aspx) auf der Microsoft-Website.

### Schutz der Daten im Cache

Unabhängig vom verwendeten Cachedienst sollten Sie überlegen, wie Sie die Daten im Cache vor nicht autorisiertem Zugriff schützen. Dabei müssen zwei Aspekte vorrangig bedacht werden:

- Der Schutz der Daten im Cache.
- Der Schutz der Daten während der Übertragung zwischen dem Cache und der Anwendung, die den Cache verwendet.

Zum Schutz der Daten im Cache kann der Cachedienst einen Authentifizierungsmechanismus implementieren, der die Identifizierung der Anwendungen erfordert, sowie ein Authentifizierungsschema, das festlegt, welche Identitäten auf die Daten im Cache mit welchen Operationen (Lese- und Schreibzugriff) zugreifen dürfen. Zur Reduzierung des damit einhergehenden Mehraufwands beim Lesen und Schreiben der Daten kann eine Identität, nachdem sie Lese- und/oder Schreibzugriff auf den Cache erhalten hat, auf alle Daten im Cache zugreifen. Wie folgt können Sie den Zugriff aber auch auf eine Teilmenge der Daten im Cache beschränken:

- Teilen Sie den Cache in Partitionen auf (mithilfe mehrerer Cacheserver), und gewähren Sie den Identitäten nur Zugriff auf die Partitionen, die sie verwenden dürfen.
- Oder verschlüsseln Sie die Daten jeder Teilmenge mit verschiedenen Schlüsseln, und stellen Sie die Verschlüsselungsschlüssel nur den Identitäten bereit, die Zugriff auf die jeweilige Teilmenge haben dürfen. Bei der Verschlüsselungsmethode kann eine Clientanwendung nach wie vor auf alle Daten im Cache zugreifen, jedoch nur die Daten entschlüsseln, für die sie den Schlüssel hat.

Beim Schutz der Daten während der Übertragung in und aus dem Cache sind Sie von den Sicherheitsfeatures der Netzwerkinfrastruktur abhängig, über die sich die Clientanwendungen mit dem Cache verbinden. Wenn der Cache über einen lokalen Server innerhalb derselben Organisation bereitgestellt wird, der auch die Clientanwendungen hostet, sind durch die isolierte Natur des Netzwerks vermutlich keine weiteren Schritte erforderlich. Wenn der Cache hingegen remote bereitgestellt wird und eine TCP- oder HTTP-Verbindung über ein öffentliches Netzwerk (z. B. das Internet) erfordert, empfiehlt sich die Implementierung von SSL.

## Überlegungen zur Implementierung von Caching in Microsoft Azure

Azure stellt den Redis-Cache von Azure bereit. Dies ist eine Implementierung des Open-Source-Redis-Cache, der als Dienst im Azure-Datencenter ausgeführt wird. Er bietet einen Cachedienst, auf den jede Azure-Anwendung zugreifen kann, und zwar unabhängig davon, ob die Anwendung als Clouddienst, als Website oder auf einem virtuellen Azure-Computer implementiert ist. Caches können von Clientanwendungen, die über den entsprechenden Zugriffsschlüssel verfügen, gemeinsam verwendet werden.

Redis ist eine hochleistungsfähige Cachinglösung, die Verfügbarkeit, Skalierbarkeit und Sicherheit bietet. In der Regel wird Redis als Dienst ausgeführt, der auf einen oder mehrere dedizierte Computer verteilt ist und der zur Sicherstellung eines schnellen Zugriffs versucht, so viele Informationen wie möglich im Arbeitsspeicher zu speichern. Diese Architektur ist für geringe Latenzzeiten und hohen Durchsatz konzipiert, indem langsame E/A-Vorgänge möglichst vermieden werden.

Der Azure Redis-Cache ist mit zahlreichen der von Clientanwendungen verwendeten APIs kompatibel. Wenn Sie bereits über Anwendungen verfügen, die Redis lokal verwenden, stellt der Azure Redis-Cache einen schnellen Migrationspfad für den Übergang zum Caching in der Cloud bereit.

> [AZURE.NOTE]Darüber hinaus bietet Azure den Managed Cache Service (Dienst für verwalteten Cache). Dieser Dienst basiert auf der Microsoft AppFabric Cache-Engine. Es ermöglicht die Erstellung eines verteilten Cache, der von lose verbundenen Anwendungen gemeinsam genutzt werden kann. Der Cache wird auf Hochleistungsservern im Azure-Datencenter bereitgestellt. Allerdings wird diese Option nicht mehr empfohlen und dient nur noch der Unterstützung vorhandener Anwendungen, die mit diesem Cachedienst entwickelt wurden. Für alle Neuentwicklungen sollten Sie stattdessen den Azure Redis-Cache verwenden.
>
> Außerdem unterstützt Azure In-Role-Caching. Mit diesem Feature können Sie einen Cache speziell für einen Clouddienst erstellen. Der Cache wird von Instanzen einer Web- oder Workerrolle gehostet, und der Zugriff darauf ist nur über Rollen möglich, die als Teil der gleichen Clouddienst-Bereitstellungseinheit implementiert sind (eine Bereitstellungseinheit ist eine Gruppe von Rolleninstanzen, die als Clouddienst in einer bestimmten Region bereitgestellt werden). Der Cache ist geclustert, und alle Instanzen der Rolle innerhalb der gleichen Bereitstellungseinheit, die den Cache hostet, werden Teil des gleichen Cacheclusters. Allerdings wird diese Option nicht mehr empfohlen und dient nur noch der Unterstützung vorhandener Anwendungen, die mit diesem Cachedienst entwickelt wurden. Für alle Neuentwicklungen sollten Sie stattdessen den Azure Redis-Cache verwenden.
>
> Sowohl Azure Managed Cache Service also auch Azure In-Role Cache sind derzeit für die Deaktivierung am 16. November 2016 vorgesehen. Sie sollten zur Vorbereitung für die Einstellung dieser Dienste zu Azure Redis Cache migrieren. Weitere Informationen finden Sie auf der Seite [Welches Redis Cache-Angebot und welche Redis Cache-Größe sollte ich verwenden?](redis-cache/cache-faq.md#what-redis-cache-offering-and-size-should-i-use) auf der Microsoft-Website.


### Features von Redis

Redis ist mehr als nur ein einfacher Cacheserver. Es bietet eine verteilte arbeitsspeicherinterne Datenbank mit einem umfassenden Befehlssatz, der zahlreiche allgemein gültige Szenarios unterstützt, wie im Abschnitt „Anwendungsfälle für das Caching mit Redis“ weiter unten in diesem Dokument beschrieben. In diesem Abschnitt werden einige der wichtigsten Features von Redis vorgestellt.

### Redis als arbeitsspeicherinterne Datenbank

Redis unterstützt sowohl Lese- als auch Schreibvorgänge. Gegenüber zahlreichen anderen Caches (die als transiente bzw. flüchtige Datenspeicher betrachtet werden sollten) können Schreibvorgänge in Redis bei Systemausfällen geschützt werden, indem sie regelmäßig in einer lokalen Snapshot-Datei oder in einer Append-only-Protokolldatei gespeichert werden. Alle Schreibvorgänge erfolgen asynchron und blockieren keine Clients, die gerade Daten lesen oder schreiben. Beim Start von Redis liest es die Daten aus der Snapshot- oder Protokolldatei ein und erstellt daraus den arbeitsspeicherinternen Cache. Weitere Informationen finden Sie unter [Persistenz Redis](http://redis.io/topics/persistence) (in englischer Sprache) auf der Redis-Website.

> [AZURE.NOTE]Im Falle eines schwerwiegenden Fehlers garantiert Redis nicht, dass alle Schreibvorgänge gespeichert werden, im schlimmsten Fall verlieren Sie aber nur die aktualisierten Daten weniger Sekunden. Denken Sie jedoch daran, dass ein Cache keineswegs als autorisierende Datenquelle eingesetzt werden sollte und es den Anwendungen, die den Cache verwenden, obliegt, sicherzustellen, dass wichtige Daten erfolgreich in einem persistenten Datenspeicher abgelegt werden. Weitere Informationen finden Sie unter „Cache-Aside-Muster“.

#### Von Redis unterstützte Datentypen

Redis ist ein Schlüssel-/Wertspeicher, dessen Werte einfache Typen oder komplexe Datenstrukturen wie Hashes, Listen und Sätze sein können. Redis unterstützt für diese Datentypen einen Satz atomarer Operationen. Schlüssel können permanent oder zeitlich begrenzt sein. In letzterem Fall werden die Schlüssel mit ihren Werten zum entsprechenden Zeitpunkt automatisch aus dem Cache entfernt. Weitere Informationen zu den Schlüsseln und Werten in Redis finden Sie auf der Seite [An Introduction to Redis data types and abstractions](http://redis.io/topics/data-types-intro) (in englischer Sprache) auf der Redis-Website.

#### Replikation und Clustering mit Redis

Zur Sicherstellung einer hohen Verfügbarkeit und eines hohen Durchsatzes unterstützt Redis eine hierarchische Master/Slave-Replikation. Schreibvorgänge an einem Redis-Masterknoten werden an einen oder mehrere untergeordnete Knoten repliziert, und Lesevorgänge können vom Masterknoten oder einem der untergeordnete Knoten bedient werden. Im Falle einer Netzwerkpartition können untergeordnete Knoten weiterhin Daten bereitstellen und sich später transparent mit dem Master synchronisieren, sobald die Verbindung wiederhergestellt ist. Weitere Informationen finden Sie auf der Seite [Replication](http://redis.io/topics/replication) (in englischer Sprache) auf der Redis-Website.

Darüber hinaus bietet Redis Clustering-Features, mit denen Daten auf Servern transparent in Shards partitioniert werden können, um so die Last zu verteilen. Dieses Feature verbessert die Skalierbarkeit, da neue Redis-Server hinzugefügt und die Daten neu partitioniert werden können, während der Cache anwächst. Darüber hinaus kann jeder Server im Cluster mithilfe der Master/Slave-Replikation repliziert werden, so dass die Daten auf jedem Knoten im Cluster verfügbar sind. Weitere Informationen zu Clustering und Sharding finden Sie auf der Seite [Redis Cluster Tutorial](http://redis.io/topics/cluster-tutorial) (in englischer Sprache) auf der Redis-Website.

### Arbeitsspeichernutzung in Redis

Die Größe eines Redis-Cache ist abhängig von den auf dem Hostcomputer verfügbaren Ressourcen begrenzt. Bei der Konfiguration eines Redis-Servers können Sie seine maximale Arbeitsspeichergröße angeben. Für die Schlüssel in einem Redis-Cache können Ablaufzeiten konfiguriert werden, nach denen die Schlüssel automatisch aus dem Cache entfernt werden. Durch dieses Feature kann ein Überlauf des arbeitsspeicherinternen Cache durch veraltete Daten weitgehend verhindert werden.

Während sich der Arbeitsspeicher füllt, kann Redis automatisch – unter Berücksichtigung einer Reihe von Richtlinien – Schlüssel und deren Werte daraus entfernen. Die Standardrichtlinie ist LRU (Least Recently Used, d. h. am längsten zurückliegende Verwendung), aber Sie können auch andere Richtlinien auswählen, zum Beispiel die Entfernung von Schlüsseln nach dem Zufallsprinzip oder gar keine Entfernung von Schlüsseln (in diesem Fall schlägt jeder Versuch fehl, einem bereits vollen Cache weitere Elemente hinzuzufügen). Weitere Informationen finden Sie auf der Seite [Using Redis as an LRU Cache](http://redis.io/topics/lru-cache) (in englischer Sprache).

### Transaktionen und Batches in Redis

Redis ermöglicht einer Clientanwendung die Übermittlung einer Reihe von Vorgängen, die Daten im Cache als atomare Transaktion lesen und schreiben. Alle Befehle einer solchen Transaktion werden garantiert sequenziell ausgeführt und kein Befehl eines anderen parallel ausgeführten Clients wird in diese Sequenz eingefügt. Es handelt sich hier jedoch nicht um echte Transaktionen im Sinne einer relationalen Datenbanktransaktion. Die Transaktionsverarbeitung besteht hier aus zwei Phasen: die Einreihung der Befehle in eine Warteschlange und die Ausführung der Befehle. Während der Einreihungsphase werden die Befehle der Transaktion vom Client an die Warteschlange übertragen. Falls während dieses Vorgangs ein Fehler auftritt (z. B. ein Syntaxfehler oder eine falsche Anzahl von Parametern), verweigert Redis die Verarbeitung der gesamten Transaktion und verwirft sie. Während der Ausführungsphase führt Redis die in die Warteschlange eingereihten Befehle in der richtigen Reihenfolge aus. Wenn ein Befehl während dieser Phase fehlschlägt, fährt Redis schlicht mit dem nächsten Befehl in der Warteschlange fort. Ein Rollback der bereits ausgeführten Befehle findet nicht statt. Diese vereinfachte Transaktionsform trägt zur Aufrechterhaltung der Leistung bei und verhindert Leistungsprobleme durch Überlastung. Redis implementiert keine Form der optimistischen Sperre zur Aufrechterhaltung der Konsistenz. Detaillierte Informationen zu Transaktionen und Sperren in Redis finden Sie auf der Seite [Transactions](http://redis.io/topics/transactions) (in englischer Sprache) auf der Redis-Website.

Darüber hinaus unterstützt Redis auch die nicht-transaktionale Batchverarbeitung von Anforderungen. Das Redis-Protokoll, mit dem Clients Befehle an einen Redis-Server senden, ermöglicht dem Client auch die Übertragung mehrerer Vorgänge innerhalb einer Anforderung. Dadurch reduziert sich die Paketfragmentierung im Netzwerk. Die einzelnen Befehle werden bei der Verarbeitung des Batch ausgeführt. Im Gegensatz zu einer Transaktion wird ein falsch formulierter Befehl bei dieser Art der Verarbeitung zwar abgelehnt, die restlichen Befehle werden aber ausgeführt. Außerdem gibt es keine Garantie dafür, in welcher Reihenfolge die Befehle des Batch verarbeitet werden.

### Sicherheit in Redis

Bei Redis geht es ausschließlich um den schnellen Datenzugriff. Es stützt sich dabei auf die Ausführung in einer vertrauenswürdigen Umgebung und verlässt sich darauf, dass nur vertrauenswürdige Clients darauf zugreifen. Deswegen unterstützt Redis auch nur ein begrenztes Sicherheitsmodell mit Kennwortauthentifizierung (wobei die Authentifizierung auch völlig deaktiviert werden kann, was jedoch nicht empfohlen wird). Alle authentifizierten Clients verwenden das gleiche globale Kennwort und haben Zugriff auf dieselben Ressourcen. Wenn Sie bei der Anmeldung mehr Sicherheit benötigen, müssen Sie vor dem Redis-Server einen eigenen Sicherheitslayer implementieren, den alle Clientanforderungen passieren müssen. An Redis sollten nicht vertrauenswürdige bzw. nicht authentifizierte Clients gar nicht erst herankommen.

Sie können den Zugriff auf Befehle einschränken, indem Sie sie deaktivieren oder umbenennen (und die neuen Namen nur privilegierten Clients bereitstellen).

Redis unterstützt keine Form der Datenverschlüsselung direkt, d. h., eine Verschlüsselung muss immer auf Clientseite erfolgen. Auch bietet Redis keinerlei Sicherheit während der Übertragung. Wenn Sie Ihre Daten während der Übertragung über das Netzwerk schützen wollen, müssen Sie einen SSL-Proxy implementieren.

Weitere Informationen finden Sie auf der Seite [Redis Security](http://redis.io/topics/security) (in englischer Sprache) auf der Redis-Website.

> [AZURE.NOTE]Der Redis-Cache von Azure bietet einen eigenen Sicherheitslayer, über den Clients die Verbindung herstellen. Die Redis-Server selbst sind dem öffentlichen Netzwerk nicht verfügbar.

### Verwenden des Redis-Cache von Azure

Der Redis-Cache von Azure ermöglicht Zugriff auf Redis-Server, die auf Servern in einem Azure-Datencenter ausgeführt werden. Er fungiert als Fassade, die Zugriffssteuerung und Sicherheit bietet. Einen Cache können Sie mithilfe des Azure-Verwaltungsportals bereitstellen. Das Portal bietet eine Anzahl vordefinierter Konfigurationen von einem 53 GB großen Cache, der als dedizierter Dienst ausgeführt wird, zum Datenschutz SSL-Kommunikation unterstützt und eine Master/Slave-Replikation mit einer SLA von 99,9 % Verfügbarkeit garantiert, bis hin zu einem lediglich 250 MB großen Cache ohne Replikation (und somit keiner Verfügbarkeitsgarantie), der auf gemeinsam genutzter Hardware ausgeführt wird.

Auch die Entfernungsrichtlinie für den Cache können Sie über das Azure-Verwaltungsportal konfigurieren, ebenso, wie Sie dort über die bereitgestellten Rollen (Owner, Contributor, Reader) den Zugriff auf den Cache steuern können. Rollen definieren die Operationen, die deren Mitglieder ausführen können. So haben Mitglieder der Rolle „Owner“ (Eigentümer) vollständige Kontrolle über den Cache und seinen Inhalt (einschließlich dessen Sicherheit), Mitglieder der Rolle „Contributor“ (Mitwirkender) können Informationen im Cache lesen und schreiben und Mitglieder der Rolle „Reader“ (Leser) können die Daten aus dem Cache nur abrufen.

Die meisten Verwaltungsaufgaben werden über das Azure-Verwaltungsportal ausgeführt, weshalb viele Verwaltungsbefehle aus der Standardversion von Redis wie die Möglichkeit der programmgesteuerten Änderung der Konfiguration, das Herunterfahren des Servers, die Konfiguration weiterer Slaves oder das zwangsweise Speichern von Daten auf der Festplatte nicht verfügbar sind.

Das Azure-Verwaltungsportal bietet eine praktische grafische Übersicht, mit der sich die Leistung des Cache gut überwachen lässt. Beispielsweise können Sie die Anzahl der aktiven Verbindungen, die Anzahl der ausgeführten Anforderungen, die Menge der Lese- und Schreibzugriffe und die Anzahl der Cachetreffer gegenüber der Cachefehler anzeigen. Diesen Informationen können Sie die Effizienz des Cache entnehmen und daraufhin gegebenenfalls die Konfiguration oder die Entfernungsrichtlinie ändern. Sie können auch Warnungen erstellen, über die Administratoren E-Mail-Benachrichtigungen erhalten, wenn eine oder mehrere wichtige Metriken außerhalb des erwarteten Bereichs fallen. Ein Administrator könnte zum Beispiel benachrichtigt werden, wenn die Anzahl der Cachefehler innerhalb der letzten Stunde einen angegebenen Wert überschritten hat, da es in diesem Fall naheliegt, dass der Cache zu klein ist oder die zwischengespeicherten Daten zu früh entfernt werden.

Sie können auch die CPU-, Arbeitsspeicher- und Netzwerkauslastung des Cache überwachen.

Weitere Informationen und Beispiele zum Erstellen und Konfigurieren eines Azure Redis-Cache finden Sie auf der Seite [Lap around Azure Redis Cache](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) (in englischer Sprache) im Azure-Blog.

## Caching von Sitzungsstatus und HTML-Ausgabe

Wenn Sie ASP.NET-Webanwendungen erstellen, deren Berechtigungen durch Azure-Webrollen gesteuert werden, können Sie die Statusinformationen von Sitzungen und HTML-Ausgaben in einem Azure Redis-Cache speichern. Der Sitzungsstatusanbieter für den Azure Redis-Cache ermöglicht die Freigabe der Informationen zwischen verschiedenen Instanzen einer ASP.NET-Webanwendung und ist somit sehr nützlich in Webfarmumgebungen, in denen keine Client-/Serveraffinität besteht und ein Caching der Sitzungsdaten im Arbeitsspeicher inadäquat ist.

Die Verwendung des Sitzungsstatusanbieters mit dem Azure Redis-Cache bietet verschiedene Vorteile:

- Viele Instanzen einer ASP.NET-Webanwendung können auf den Sitzungsstatus zugreifen, wodurch sich die Skalierbarkeit verbessert.
- Ein kontrollierter gleichzeitiger Zugriff auf die gleichen Sitzungsstatusdaten für mehrere Reader und einen einzigen Writer wird unterstützt.
- Zur Einsparung von Arbeitsspeicher und Verbesserung der Netzwerkleistung kann Komprimierung verwendet werden.

Weitere Informationen finden Sie auf der Seite [ASP.NET-Sitzungsstatusbieter für den Azure Redis-Cache](redis-cache/cache-asp.net-session-state-provider.md) auf der Microsoft-Website.

> [AZURE.NOTE]Für ASP.NET-Anwendungen, die außerhalb der Azure-Umgebung ausgeführt werden, sollte der Sitzungsstatusanbieter für den Azure Redis-Cache nicht verwendet werden. Die Latenzzeiten für den Zugriff auf den Cache außerhalb von Azure können die Leistungsvorteile des Caching zunichte machen.

Auf ähnliche Weise ermöglicht Ihnen der Ausgabecacheanbieter für den Azure Redis-Cache das Speichern der von einer ASP.NET-Webanwendung generierten HTTP-Antworten. Der Ausgabecacheanbieter kann in Verbindung mit dem Azure Redis-Cache die Antwortzeiten von Anwendungen mit komplexen HTML-Ausgaben verbessern. Anwendungsinstanzen, die ähnliche Antworten generieren, können auf freigegebene Ausgabefragmente im Cache zurückgreifen, anstatt die HTML-Ausgabe völlig neu generieren zu müssen. Weitere Informationen finden Sie auf der Seite [ASP.NET-Ausgabecacheanbieter für den Azure Redis-Cache](redis-cache/cache-asp.net-output-cache-provider.md) auf der Microsoft-Website.

## Erstellen eines benutzerdefinierten Redis-Cache

Der Azure Redis-Cache fungiert als Fassade für die zugrunde liegenden Redis-Server. Derzeit unterstützt er einen festen Satz an Konfigurationen, jedoch kein Redis-Clustering. Wenn Sie eine erweiterte Konfiguration benötigen, die nicht vom Azure Redis-Cache bereitgestellt wird (z. B. einen Cache mit mehr als 53 GB), können Sie mit virtuellen Azure-Computern eigene Redis-Server erstellen und bereitstellen. Das hierzu erforderliche Verfahren ist unter Umständen recht komplex, da Sie eventuell mehrere virtuelle Computer als Master- und Slave-Knoten erstellen müssen, wenn Sie auch Replikation implementieren möchten. Für einen Cluster benötigen Sie außerdem mehrere Master- und Slave-Server; für die Mindestversion einer Clustertopologie mit Replikation, die ein hohes Maß an Verfügbarkeit und Skalierbarkeit bietet, sind mindestens sechs virtuelle Computer erforderlich, die in drei Paare mit jeweils einem Master- und einem Slave-Server organisiert sind (ein Cluster muss mindestens drei Masterknoten enthalten). Master und Slave eines Paars sollten zur Minimierung der Latenzzeiten nahe beieinander liegen, die einzelnen Paare können aber in separaten Azure-Datencentern in verschiedenen Regionen bereitgestellt werden, wenn sich die zwischengespeicherten Daten möglichst nahe der Anwendungen befinden sollen, die sie am wahrscheinlichsten nutzen. Ein Beispiel für die Erstellung und Konfiguration eines Redis-Knoten, der als virtueller Azure-Computer ausgeführt wird, finden Sie auf der Seite [Running Redis on a CentOS Linux VM in Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) (in englischer Sprache) auf der Microsoft-Website.

Beachten Sie, dass Sie, wenn Sie auf diese Weise Ihren eigenen Redis-Cache implementieren, selbst verantwortlich für die Überwachung, Verwaltung und den Schutz des Diensts sind.

## Partitionierung eines Redis-Cache

Einen Cache zu partitionieren bedeutet, ihn auf mehrere Computer aufzuteilen. Diese Struktur hat gegenüber einem einzigen Cacheserver verschiedene Vorteile:

- Der Cache kann weit über die Kapazität eines einzelnen Servers hinausgehen.
- Die Daten werden zugunsten einer höheren Verfügbarkeit auf mehrere Server verteilt. Wenn ein Server ausfällt oder nicht zugänglich ist, stehen nur dessen Daten nicht mehr zur Verfügung. Auf die Daten der verbleibenden Server kann weiterhin zugegriffen werden. Für einen Cache ist dies nicht so wichtig, da zwischengespeicherte Daten nur eine vorübergehende Kopie der in einer Datenbank gespeicherten Daten sind und die auf einem ausgefallenen Server zwischengespeicherten Daten auch auf einem anderen Server zwischengespeichert werden können.
- Die Last wird auf die Server verteilt, wodurch sich Leistung und Skalierbarkeit verbessern.
- Das lokalere Speichern der Daten in der Nähe der Benutzer (Geolocating), die darauf zugreifen, verringert die Latenz.

Für einen Cache ist die häufigste Form der Partitionierung das Sharding. Bei dieser Strategie ist jede Partition (bzw. jedes Shard) ein separates Redis-Cache. Die Daten werden mittels der Sharding-Logik, die verschiedene Strategien einsetzen kann, auf bestimmte Partitionen verteilt. Nähere Informationen zur Implementierung von Sharding finden Sie unter [Sharding Pattern](http://msdn.microsoft.com/library/dn589797.aspx) (in englischer Sprache).

Für die Implementierung der Partitionierung in einem Redis-Cache können Sie nach einer der folgenden beiden Strategien vorgehen:

- _Serverseitige Abfrageweiterleitung:_ Bei diesem Verfahren sendet eine Clientanwendung eine Anforderung an einen der Redis-Server im Cache (in der Regel an den geografisch nächstliegenden Server). Jeder Redis-Server speichert Metadaten, die die Partition auf diesem Server beschreiben. Zudem enthält er aber auch Informationen zu den Partitionen auf den anderen Servern. Der Redis-Server überprüft die Clientanforderung, und wenn sie lokal gelöst werden kann, führt er den angeforderten Vorgang durch. Andernfalls leitet er die Anforderung einen geeigneten Server weiter. Dieses Modell wird durch das Clustering-Feature von Redis implementiert und auf der Seite [Redis Cluster Tutorial](http://redis.io/topics/cluster-tutorial) (in englischer Sprache) auf der Redis-Website ausführlich beschrieben. Redis-Clustering ist für Clientanwendungen transparent und dem Cluster können zusätzliche Redis-Server hinzugefügt (und die Daten neu partitioniert) werden, ohne die Clients neu zu konfigurieren.

- _Clientseitige Partitionierung:_ In diesem Modell enthält die Client-Anwendung Logik (möglicherweise in Form einer Bibliothek), die Anforderungen an einen geeigneten Redis-Server weiterleitet. Diese Strategie kann mit dem Redis-Cache von Azure verwendet werden. Erstellen Sie mehrere Azure Redis-Cache (einen für jede Datenpartition), und implementieren Sie die clientbasierte Logik, die die Anforderungen an den richtigen Cache weiterleitet. Wenn sich das Partitionierungsschema ändert (z. B. weil weitere Azure Redis-Cache hinzugefügt werden), müssen die Clientanwendungen unter Umständen neu konfiguriert werden.

- _Proxy-unterstützte Partitionierung:_ Bei diesem Schema senden Clientanwendungen Anforderungen an einen zwischengeschalteten Proxydienst, der weiß, wie die Daten partitioniert sind, und die Anforderung an einen geeigneten Redis-Server weiterleitet. Auch diese Strategie kann mit dem Redis-Cache von Azure verwendet werden. Der Proxydienst kann zum Beispiel als Azure-Clouddienst implementiert werden. Diese Strategie setzt eine zusätzliche Ebene der Komplexität für die Implementierung des Diensts voraus, weshalb die Ausführung der Anforderungen im Vergleich zur clientseitigen Partitionierung länger dauern kann.

Auf der Seite [Partitioning: how to split data among multiple Redis instances](http://redis.io/topics/partitioning) (in englischer Sprache) der Redis-Website finden Sie weitere Informationen zur Implementierung der Partitionierung mit Redis.

### Implementieren von Clientanwendungen für den Redis-Cache

Redis unterstützt in den verschiedensten Programmiersprachen geschriebene Clientanwendungen. Wenn Sie neue Anwendungen mit .NET Framework erstellen, wird jedoch die Verwendung der Clientbibliothek StackExchange.Redis empfohlen. Diese Bibliothek bietet ein .NET Framework-Objektmodell, das die Details des Verbindungsaufbaus mit einem Redis-Server sowie des Sendens von Befehlen und Empfangens von Antworten abstrahiert. Diese Bibliothek ist als NuGet-Paket in Visual Studio verfügbar. Mit der gleichen Bibliothek können Sie eine Verbindung mit einem Azure Redis-Cache oder einem benutzerdefinierten Redis-Cache auf einem virtuellen Computer herstellen.

Zum Herstellen der Verbindung mit einem Redis-Server verwenden Sie die statische Methode `Connect` der `ConnectionMultiplexer`-Klasse. Die durch diese Methode erstellte Verbindung kann für die gesamte Lebensdauer der Clientanwendung, auch von mehreren Threads gleichzeitig, verwendet werden. Sie sollten die Verbindung daher nicht nach jedem Redis-Vorgang trennen und neu aufbauen, da dies die Leistung beeinträchtigen kann. Sie können die Verbindungsparameter, z. B. die Adresse des Redis-Hosts und das benötigte Kennwort, in der Methode angeben. Bei Verwendung des Azure Redis-Cache ist das Kennwort der primäre oder sekundäre Schlüssel, der vom Azure-Verwaltungsportal für den Azure Redis-Cache generiert wurde.

Nach dem Verbindungsaufbau mit dem Redis-Server können Sie ein Handle für die Redis-Datenbank abrufen, die als Cache fungiert. Die hierfür benötigte `GetDatabase`-Methode wird von der Redis-Verbindung bereitgestellt. Danach können Sie mit den Methoden `StringGet` und `StringSet` Elemente aus dem Cache abrufen und Daten im Cache speichern. Diese Methoden erwarten als Parameter einen Schlüssel und geben entweder das Element aus dem Cache zurück, dessen Wert mit dem Schlüssel übereinstimmt (`StringGet`), oder fügen das Element mit diesem Schlüssel zum Cache hinzu (`StringSet`).

Je nach Speicherort des Redis-Servers können bei vielen Vorgängen zwischen der Übertragung der Anforderung an den Server und der Rückgabe der Antwort an den Client auch Latenzzeiten entstehen. Durch asynchrone Versionen zahlreicher ihrer Methoden sorgt die StackExchange-Bibliothek jedoch dafür, dass die Clientanwendungen reaktionsfähig bleiben. Diese Methoden unterstützen die [aufgabenbasierten asynchronen Muster](http://msdn.microsoft.com/library/hh873175.aspx) von .NET Framework.

Der folgende Codeausschnitt zeigt eine Methode namens `RetrieveItem`. Er veranschaulicht ein Beispiel für die Implementierung des Cache-Aside-Musters mit Redis und der StackExchange-Bibliothek. Die Methode akzeptiert einen Zeichenfolgeschlüsselwert und versucht durch Aufrufen der Methode `StringGetAsync` (die asynchrone Version von `StringGet`), das entsprechende Element aus dem Redis-Cache abzurufen. Wird das Element nicht gefunden, so wird es mit der Methode `GetItemFromDataSourceAsync` (einer lokalen Methode, die nicht Teil der StackExchange-Bibliothek ist) aus der zugrunde liegenden Datenquelle abgerufen und mit der Methode `StringSetAsync` zum Cache hinzugefügt, so dass es beim nächsten Mal schneller abgerufen werden kann.

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

Die Methoden `StringGet` und `StringSet` sind nicht auf das Abrufen oder Speichern von Zeichenfolgewerten beschränkt. Sie akzeptieren jedes Element, das als Byte-Array serialisiert ist. Wenn Sie ein .NET-Objekt speichern müssen, können Sie es als Byte-Stream serialisieren und mit der Methode StringSet in den Cache schreiben. Auf ähnliche Weise können Sie ein Objekt mit der Methode StringGet aus dem Cache lesen und in ein .NET-Objekt deserialisieren. Der folgende Code zeigt verschiedene Erweiterungsmethoden für die IDatabase-Schnittstelle (die GetDatabase-Methode einer Redis-Verbindung gibt ein `IDatabase`-Objekt zurück) sowie Beispielcode, der diese Methoden zum Lesen und Schreiben eines BlogPost-Objekts, in den Cache verwendet:

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

Der folgende Codeausschnitt veranschaulicht eine Methode namens `RetrieveBlogPost`, die diese Erweiterungsmethoden nach dem Cache-Aside-Muster zum Lesen und Schreiben eines serialisierbaren `BlogPost`-Objekts im Cache verwendet:

```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

Wenn eine Clientanwendung mehrere asynchrone Anforderungen sendet, unterstützt Redis auch Befehls-Pipelining. Redis kann Anforderungen nach dem Multiplexer-Verfahren über die gleiche Verbindung verarbeiten, statt Befehle in strenger Reihenfolge zu empfangen und auszuführen. Auch dadurch werden Latenzzeiten durch eine effizientere Nutzung des Netzwerks verringert. Der folgende Codeausschnitt zeigt ein Beispiel, das die Daten zweier Kunden gleichzeitig abruft. Der Code sendet zwei Anforderungen und führt dann weitere Verarbeitungsschritte aus (die hier nicht dargestellt werden), bevor er auf die Ergebnisse wartet. Die Wait-Methode des Cache-Objekts ist vergleichbar mit der Methode Task.Wait von .NET Framework:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

Weitere Informationen zum Schreiben von Clientanwendungen, die den Redis-Cache von Azure verwenden können, finden Sie auf der Seite [Azure Redis Cache-Dokumentation](http://azure.microsoft.com/documentation/services/cache/) auf der Microsoft-Website. Zusätzliche Informationen finden Sie auf der Seite [Basic Usage](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) der StackExchange.Redis-Website, und die Seite [Pipelines und Multiplexers](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) auf derselben Website bietet darüber hinaus Informationen zu asynchronen Vorgängen und Pipelining mit Redis und der StackExchange-Bibliothek (beide Seiten in englischer Sprache). Im Abschnitt „Anwendungsfälle für das Caching mit Redis“ weiter unten in diesem Dokument finden Sie Beispiele für erweiterte Techniken, die Sie für die Daten in einem Redis-Cache verwenden können.

## Anwendungsfälle für das Caching mit Redis

Die einfachste Verwendung von Redis für das Caching beinhaltet das Speichern von Schlüssel-/Wertpaaren, wobei der Wert eine nicht interpretierte Zeichenfolge von beliebiger Länge ist, die beliebige binäre Daten enthalten kann (im Prinzip handelt es sich hier um ein Byte-Array, das als Zeichenfolge behandelt werden kann). Dieses Szenario wurde im Abschnitt „Implementieren von Clientanwendungen für den Redis-Cache“ weiter oben in diesem Dokument dargestellt. Beachten Sie, dass auch Schlüssel nicht interpretierte Daten enthalten können. Sie können daher beliebige binäre Informationen als Schlüssel verwenden, sollten aber beachten, dass mit der Länge des Schlüssels auch der Speicherbedarf und die für Suchvorgänge benötigte Zeit zunehmen. Aus Gründen der Leistung und Verwaltbarkeit sollten Sie Ihren Keyspace sorgfältig planen und aussagekräftige (aber nicht allzu lange) Schlüssel verwenden. Verwenden Sie zum Beispiel strukturierte Schlüssel wie „customer:100“ zur Darstellung des Schlüssels für den Kunden mit der ID 100, anstatt einfach nur „100“. Mit einem solchen Schema können Sie problemlos zwischen Werten unterscheiden, die unterschiedliche Datentypen speichern. Nach diesem Schema würden Sie zum Beispiel zur Darstellung des Schlüssels für den Auftrag mit der ID 100 den Schlüssel „orders:100“ verwenden.

Abgesehen von eindimensionalen Binärzeichenfolgen kann ein Wert eines Schlüssel-/Wertpaars in Redis auch strukturiertere Informationen wie Listen, Sätze (sortierte und unsortierte) und Hashes enthalten. Redis bietet einen umfassenden Befehlssatz, mit dem diese Datentypen verarbeitet werden können, wobei viele dieser Befehle über eine Clientbibliothek, z. B. StackExchange, auch .NET Framework-Anwendungen zur Verfügung stehen. Eine detailliertere Übersicht über diese Datentypen und die Befehle zu deren Verarbeitung finden Sie auf der Seite [An introduction to Redis data types and abstractions](http://redis.io/topics/data-types-intro) (in englischer Sprache) auf der Redis-Website.

In diesem Abschnitt werden einige gängige Anwendungsbeispiele für diese Datentypen und Befehle vorgestellt.

### Ausführen von atomaren und Batchvorgängen

Redis unterstützt eine Reihe von atomaren Get- und Set-Vorgängen für Zeichenfolgewerte. Durch diese Vorgänge lässt sich die Konkurrenzsituation, die zwischen separaten `GET`- und `SET`-Befehlen auftreten kann, entschärfen. Die verfügbaren Vorgänge umfassen:

- `INCR`, `INCRBY`, `DECR` und `DECRBY`, die atomare Inkrement- und Dekrementvorgänge an ganzzahligen numerischen Datenwerten ausführen. Die StackExchange-Bibliothek bietet zur Ausführung dieser Vorgänge und zur Rückgabe der Ergebniswerte aus dem Cache erweiterte Versionen der `IDatabase.StringIncrementAsync`- und `IDatabase.StringDecrementAsync`-Methoden. Folgender Codeausschnitt veranschaulicht die Verwendung dieser Methoden:

  ```csharp ConnectionMultiplexer redisHostConnection = ...; IDatabase cache = redisHostConnection.GetDatabase(); ... await cache.StringSetAsync("data:counter", 99); ... long oldValue = await cache.StringIncrementAsync("data:counter"); // Increment by 1 (the default) // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50); // Decrement by 50 // newValue should be 50 ```

- `GETSET`, das den Wert eines Schlüssels abruft und ihn in einen neuen Wert ändert. Die StackExchange-Bibliothek stellt diesen Vorgang über die Methode `IDatabase.StringGetSetAsync` bereit. Nachfolgender Codeausschnitt zeigt ein Beispiel für diese Methode. Dieser Code gibt den aktuellen Wert des Schlüssels „data:counter“ aus dem vorherigen Beispiel zurück und setzt den Wert dieses Schlüssels im selben Vorgang auf 0 (null) zurück:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET` und `MSET`, die einen Satz von Zeichenfolgewerten in einem Vorgang zurückgeben oder ändern können. Diese Funktionalität wird, wie das folgende Beispiel zeigt, auch von den erweiterten Methoden `IDatabase.StringGetAsync` und `IDatabase.StringSetAsync` unterstützt:

  ```csharp ConnectionMultiplexer redisHostConnection = ...; IDatabase cache = redisHostConnection.GetDatabase(); ... // Create a list of key/value pairs var keysAndValues = new List<KeyValuePair<RedisKey  RedisValue>>() { new KeyValuePair<RedisKey  RedisValue>("data:key1", "value1"), new KeyValuePair<RedisKey  RedisValue>("data:key99", "value2"), new KeyValuePair<RedisKey  RedisValue>("data:key322", "value3") };

  // Store the list of key/value pairs in the cache cache.StringSet(keysAndValues.ToArray()); ... // Find all values that match a list of keys RedisKey keys = { "data:key1", "data:key99", "data:key322"}; RedisValue values = null; values = cache.StringGet(keys); // values should contain { "value1", "value2", "value3" } ```

Sie können auch mehrere Vorgänge zu einer einzigen Redis-Transaktion zusammenfassen, wie weiter vorne im Abschnitt „Transaktionen und Batches in Redis“ beschrieben. Die StackExchange-Bibliothek bietet über die `ITransaction`-Schnittstelle Unterstützung für Transaktionen. Mithilfe der Methode IDatabase.CreateTransaction können Sie ein ITransaction-Objekt erstellen und mit dem durch die Methoden bereitgestellten `ITransaction`-Objekt Befehle für die Transaktion aufrufen. Die `ITransaction`-Schnittstelle bietet auf die gleichen Methoden Zugriff wie die `IDatabase`-Schnittstelle, allerdings sind diese Methoden asynchron. Sie werden nur bei Aufruf der Methode `ITransaction.Execute` ausgeführt. Der von der Execute-Methode zurückgegebene Wert gibt an, ob die Transaktion erfolgreich erstellt wurde (true) oder fehlgeschlagen ist (false).

Der folgende Codeausschnitt zeigt ein Beispiel, das zwei Leistungsindikatoren innerhalb derselben Transaktion inkrementiert bzw. dekrementiert:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

Denken Sie daran, dass es sich bei Redis-Transaktionen nicht um Transaktionen im Sinne von relationalen Datenbanktransaktionen handelt. Die Execute-Methode reiht lediglich alle Befehle der Transaktion zur Ausführung in eine Warteschlange ein, verwirft dabei aber jede fehlerhaft formulierte Transaktion. Nach der erfolgreichen Einreihung aller Befehle in die Warteschlange werden die einzelnen Befehle asynchron ausgeführt. Auch wenn ein Befehl fehlschlägt, wird die Verarbeitung der anderen fortgesetzt. Falls die erfolgreiche Ausführung eines Befehls überprüft werden muss, können Sie das Befehlsergebnis mit der Eigenschaft „Result“ der entsprechenden Task abrufen, wie das vorangegangene Beispiel zeigt. Das Lesen der Eigenschaft „Result“ blockiert die Transaktion so lange, bis die Task abgeschlossen ist.

Weitere Informationen finden Sie auf der Seite [Transactions in Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) auf der StackExchange.Redis-Website.

Zum Durchführen von Batchvorgängen können Sie die IBatch-Schnittstelle der StackExchange-Bibliothek verwenden. Diese Schnittstelle bietet auf die gleichen Methoden Zugriff wie die IDatabase-Schnittstelle, allerdings sind diese Methoden asynchron. Sie erstellen mit der Methode IDatabase.CreateBatch ein IBatch-Objekt und führen den Batch dann mit der Methode IBatch.Execute aus, wie das folgende Beispiel zeigt. Dieser Code legt lediglich einen Zeichenfolgewert fest, inkrementiert bzw. dekrementiert die Leistungsindikatoren aus dem vorherigen Beispiel und zeigt das Ergebnis an:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

An dieser Stelle muss ein wichtiger Unterschied zwischen der Ausführung eines Batch und einer Transaktion hervorgehoben werden: Falls ein Befehl, der Teil eines Batch ist, fehlschlägt, weil er fehlerhaft formuliert wurde, werden die verbleibenden Befehle nach wie vor ausgeführt, da die Methode IBatch.Execute keinen Hinweis auf einen Erfolg oder Fehler zurückgibt.

### Ausführen von Fire-and-Forget-Cache-Vorgängen

Zur Unterstützung von Fire-and-Forget-Vorgängen (Auslösen und Vergessen) verwendet Redis Befehlsflags. Bei dieser Art von Vorgängen initiiert der Client den Vorgang lediglich, hat aber kein Interesse am Ergebnis und wartet die Befehlsausführung auch nicht ab. Das folgende Beispiel zeigt, wie der Befehl INCR als Fire-and-Forget-Vorgang ausgeführt wird:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### Automatisches Ablaufen von Schlüsseln

Wenn Sie ein Element in einem Redis-Cache speichern, können Sie ein Zeitlimit angeben, nach dem das Element automatisch aus dem Cache entfernt wird. Mit dem Befehl `TTL` können Sie auch abfragen, wie viel Zeit bis zum Ablauf eines Schlüssels verbleibt. Dieser Befehl steht StackExchange-Anwendungen über die Methode IDatabase.KeyTimeToLive zur Verfügung.

Der folgende Codeausschnitt zeigt, wie für einen Schlüssel eine Ablaufzeit von 20 Sekunden festgelegt und seine verbleibende Lebensdauer abgefragt wird:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

Mit dem Befehl EXPIRE, der in der StackExchange-Bibliothek unter der Methode KeyExpireAsync zur Verfügung steht, können Sie als Ablaufzeit auch einen genauen Zeitpunkt (Datum und Uhrzeit) festlegen:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _Tipp:_ Mit dem Befehl DEL, der in der StackExchange-Bibliothek unter der Methode IDatabase.KeyDeleteAsync verfügbar ist, können Sie ein Element auch manuell aus dem Cache entfernen.

### Verwenden von Tags zum übergreifenden Korrelieren der Elemente im Cache

Ein Redis-Satz ist eine Sammlung mehrerer Elemente, die einen gemeinsamen Schlüssel verwenden. Einen solchen Satz können Sie mit dem Befehl SADD erstellen. Die Elemente eines Satzes können Sie mit dem Befehl SMEMBERS abrufen. In der StackExchange-Bibliothek ist der Befehl SADD in der Methode IDatabase.SetAddAsync und der Befehl SMEMBERS in der Methode IDatabase.SetMembersAsync implementiert. Mit den Befehlen SDIFF (set difference = Unterschiedsmenge), SINTER (set intersection = Schnittmenge) und SUNION (set union = Vereinigungsmenge) können bestehende Sätze auch zu neuen Sätzen zusammengefasst werden. In der StackExchange-Bibliothek werden diese Befehle in der Methode IDatabase.SetCombineAsync bereitgestellt. Der erste Parameter dieser Methode legt den auszuführenden Set-Vorgang fest.

Die folgenden Codeausschnitte veranschaulichen, wie praktisch Sätze zum schnellen Speichern und Abrufen ähnlicher Elemente sind. Dieser Code verwendet den im Abschnitt „Implementieren von Clientanwendungen für den Redis-Cache“ beschriebenen Typ „BlogPost“. Ein BlogPost-Objekt enthält vier Felder – eine ID, einen Titel, eine Bewertung für die Rangfolge und eine Sammlung von Tags. Der erste der folgenden Codeausschnitte zeigt Beispieldaten zum Füllen einer C#-Liste mit BlogPost-Objekten:

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags – assigned from a collection
                                  // in the tags list
}
```

Sie können die Tags der einzelnen BlogPost-Objekte als Satz in einem Redis-Cache speichern und jeden Satz der BlogPost-ID zuordnen. Auf diese Weise findet eine Anwendung die Tags eines bestimmten Blogbeitrags sehr schnell. Wenn Sie die Suchrichtung umkehren und alle Blogbeiträge mit einem bestimmten Tag finden möchten, können Sie einen weiteren Satz mit Blogbeiträgen erstellen, deren Schlüssel die Tag-ID referenziert:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag.
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

Mithilfe dieser Strukturen lassen sich viele allgemeine Abfragen sehr effizient ausführen. Sie können zum Beispiel wie folgt alle von Blogbeitrag 1 verwendeten Tags suchen und anzeigen:

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

Mit dem Befehl SINTER (set intersection) können Sie wie folgt alle Tags suchen, die sowohl Blogbeitrag 1 als auch Blogbeitrag 2 zugeordnet sind:

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

Oder Sie können alle Blogbeiträge suchen, die ein bestimmtes Tag enthalten:

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### Suchen der zuletzt verwendeten Elemente

Eine vielen Anwendungen gemeinsame Aufgabe ist es, die zuletzt verwendeten Elemente zu finden. So sollen auf einer Blogsite beispielsweise Informationen über die zuletzt gelesenen Blogbeiträge angezeigt werden. Diese Funktionalität kann durch eine Redis-Liste implementiert werden. Eine Redis-Liste enthält mehrere Elemente, die den gleichen Schlüssel verwenden, wobei die Liste jedoch als doppelseitige Warteschlange fungiert. Mit den Befehlen LPUSH (Links-Push) und RPUSH (Rechts-Push) können Sie Elemente mittels Push an beiden Enden der Liste einreihen. Mit den Befehlen LPOP (Links-Pop) und RPOP (Rechts-Pop) können Sie Elemente mittels Pop von beiden Enden der Liste abrufen. Mit den Befehlen LRANGE (linker Bereich) und RRANGE (rechter Bereich) können Sie auch einen Elementebereich von beiden Enden der Liste zurückgeben. Die nachfolgenden Codeausschnitte zeigen, wie diese Vorgänge mit den von der StackExchange-Bibliothek bereitgestellten Befehlen ausgeführt werden. Dieser Code verwendet den BlogPost-Typ aus den vorherigen Beispielen. Während ein Benutzer einen Blogbeitrag liest, wird der Titel des Blogbeitrags mit der Methode IDatabase.ListLeftPushAsync in eine Liste mit dem Schlüssel „blog:recent\_posts“ in den Redis-Cache verschoben:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // push the blog post onto the list
```

Auch die Titel aller nachfolgend gelesenen Blogbeiträge werden in diese Liste verschoben. Die Titel werden der Liste in der Reihenfolge hinzugefügt, in der sie gelesen werden, wobei sich die zuletzt gelesenen Blogbeiträge am linken Ende der Liste befinden. (Falls der gleiche Blogbeitrag mehrmals gelesen wird, enthält die Liste mehrere Einträge zu diesem Beitrag.) Mit der Methode IDatabase.ListRange können Sie die Titel der zuletzt gelesenen Beiträge anzeigen. Diese Methode akzeptiert als Parameter den Schlüssel mit der Liste, einen Startpunkt und einen Endpunkt. Der folgende Code ruft die Titel der 10 Blogbeiträge ab, die sich in der Liste am weitesten links befinden (Elemente 0 bis 9):

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

Beachten Sie, dass die Methode ListRangeAsync keine Elemente aus der Liste entfernt. Hierzu müssten Sie die Methoden IDatabase.ListLeftPopAsync (am linken Ende entfernen) bzw. IDatabase.ListRightPopAsync (am rechten Ende entfernen) verwenden.

Um zu verhindern, dass die Liste ins Unermessliche anwächst, können Sie die Liste mit ListTrimAsync in regelmäßigen Abständen kürzen. Der nachfolgende Codeausschnitt entfernt alle Elemente aus der Liste mit Ausnahme der letzten fünf am linken Ende:

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### Implementieren einer Rangliste

Standardmäßig werden die Elemente eines Satzes in keiner bestimmten Reihenfolge aufbewahrt. Mit dem Befehl ZADD, der in der StackExchange-Bibliothek unter der Methode IDatabase.SortedSetAdd zur Verfügung steht, können Sie einen Satz jedoch sortieren. Die Elemente werden nach einem numerischen Wert, dem Score (Bewertung), der dem Befehl als Parameter übergeben wird, sortiert. Der folgende Codeausschnitt fügt einer sortierten Liste den Titel eines Blogbeitrags hinzu. In diesem Beispiel hat jeder Blogbeitrag auch ein Score-Feld mit der Rangfolge des Blogbeitrags.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

Mit der Methode IDatabase.SortedSetRangeByRankWithScores können Sie die Titel und Scores der Blogbeiträge in aufsteigender Score-Reihenfolge abrufen:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURE.NOTE]Darüber hinaus stellt die StackExchange-Bibliothek die Methode IDatabase.SortedSetRangeByRankAsync bereit, die die Daten in Score-Reihenfolge, jedoch ohne Angabe des Scores zurückgibt.

Mit zusätzlichen Parametern, die Sie der Methode IDatabase.SortedSetRangeByRankWithScoresAsync übergeben, können Sie die Elemente auch in absteigender Score-Reihenfolge abrufen oder die Anzahl der abgerufenen Elemente begrenzen. Das nächste Beispiel zeigt die Titel und Scores der Blogbeiträge mit den zehn höchsten Scores:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

Im nächsten Beispiel wird die Methode IDatabase.SortedSetRangeByScoreWithScoresAsync verwendet. Damit können Sie die zurückgegebenen Elemente auf diejenigen eines bestimmten Score-Bereichs beschränken:

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### Messaging über Kanäle

Abgesehen von seiner Funktion als Datencache bietet ein Redis-Server über einen hochleistungsfähigen Publish/Subscribe-Mechanismus auch Messaging-Funktionalität. Clientanwendungen können einen Kanal abonnieren (subskribieren), und andere Anwendungen oder Dienste können Nachrichten für diesen Kanal veröffentlichen (publizieren). Die subskribierenden Anwendungen erhalten die veröffentlichten Nachrichten und können diese verarbeiten.

Zum Abonnieren eines Kanals bietet Redis den Befehl SUBSCRIBE. Dieser Befehl erwartet als Parameter den Namen eines oder mehrerer Kanäle, über die die Anwendung Nachrichten akzeptiert. Die StackExchange-Bibliothek bietet darüber hinaus die Schnittstelle ISubscription, über die eine .NET Framework-Anwendung Kanäle abonnieren und Nachrichten über Kanäle veröffentlichen kann. Ein ISubscription-Objekt erstellen Sie mit der GetSubscriber-Methode der Verbindung zum Redis-Server. Danach überwachen Sie den Kanal mit der SubscribeAsync-Methode dieses Objekts auf Nachrichten, die über diesen Kanal veröffentlicht werden. Das folgende Codebeispiel zeigt, wie ein Kanal mit dem Namen „messages:blogPosts“ abonniert wird:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

Der erste Parameter der Subscribe-Methode ist der Name des Kanals. Für diesen Namen gelten die gleichen Konventionen wie für die Schlüssel im Cache. Er kann beliebige Binärdaten enthalten, obwohl aus Gründen der Leistung und Verwaltbarkeit kurze, aussagekräftige Zeichenfolgen empfohlen werden. Beachten Sie zudem, dass für Kanäle und Schlüssel verschiedene Namespaces verwendet werden. Kanal- und Schlüsselnamen können daher identisch sein, allerdings würde Ihnen dies nicht gerade die Verwaltung Ihres Anwendungscodes erleichtern.

Der zweite Parameter ist ein Aktionsdelegat. Dieser Delegat wird bei jeder im Kanal neu veröffentlichten Nachricht asynchron ausgeführt. Im nächsten Beispiel wird lediglich eine Nachricht auf der Konsole angezeigt (die Nachricht enthält den Titel eines Blogbeitrags).

Für die Veröffentlichung über einen Kanal kann eine Anwendung den Redis-Befehl PUBLISH verwenden. Außerdem stellt die StackExchange-Bibliothek zu diesem Zweck die Methode IServer.PublishAsync bereit. Der nächste Codeausschnitt zeigt, wie eine Nachricht über den Kanal „messages:blogPosts“ veröffentlicht wird:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

Über den Publish/Subscribe-Mechanismus sollten Sie verschiedene Dinge wissen:

- Mehrere Abonnenten können denselben Kanal abonnieren. Sie alle erhalten dann die über diesen Kanal veröffentlichten Nachrichten.
- Abonnenten empfangen nur Nachrichten, die nach dem Abschluss ihres Abonnements veröffentlicht wurden. Die Kanäle sind nicht gepuffert. Nach der Veröffentlichung einer Nachricht überträgt die Redis-Infrastruktur die Nachricht sofort an jeden Abonnenten und entfernt die Nachricht anschließend wieder aus dem Kanal.
- Die Abonnenten erhalten die Nachrichten standardmäßig in der Reihenfolge, in der sie gesendet werden. Innerhalb eines sehr aktiven Systems mit sehr vielen Nachrichten und vielen Abonnenten und Publishern würde die garantierte Zustellung der Nachrichten in der sequenziell richtigen Reihenfolge die Leistung des Systems beeinträchtigen. Wenn die Nachrichten unabhängig voneinander sind und die Reihenfolge ihrer Zustellung unerheblich ist, können Sie die Parallelverarbeitung durch das Redis-System aktivieren, wodurch sich die Reaktionsfähigkeit verbessert. Dazu setzen Sie die Einstellung PreserveAsyncOrder der vom Abonnenten verwendeten Verbindung auf dem StackExchange-Client auf „false“: ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  redisHostConnection.PreserveAsyncOrder = false;
  ISubscriber subscriber = redisHostConnection.GetSubscriber();
  ```

## Zugehörige Muster und Anleitungen

Eventuell sind für Sie auch folgende Muster interessant, wenn Sie in den Anwendungen Ihres Szenarios Caching implementieren:

- [Cache-Aside-Muster](http://msdn.microsoft.com/library/dn589799.aspx): Dieses Muster beschreibt, wie Daten bei Bedarf aus einem Datenspeicher in einen Cache geladen werden. Dieses Muster trägt auch dazu bei, die Konsistenz zwischen den im Cache gespeicherten Daten und den Daten im ursprünglichen Datenspeicher zu bewahren.
- [Sharding-Muster](http://msdn.microsoft.com/library/dn589797.aspx): Dieses Muster beschreibt die Implementierung einer horizontalen Partitionierung, die beim Speichern und Abrufen großer Datenvolumen zur Verbesserung der Skalierbarkeit beiträgt.

## Weitere Informationen

- Seite [MemoryCache-Klasse](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) auf der Microsoft-Website.
- Seite [Azure Redis Cache-Dokumentation](http://azure.microsoft.com/documentation/services/cache/) auf der Microsoft-Website.
- Seite [Azure Redis Cache – häufig gestellte Fragen](redis-cache/cache-faq.md) auf der Microsoft-Website.
- Seite [Konfigurationsmodell](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) auf der Microsoft-Website.
- Seite [Aufgabenbasiertes asynchrones Muster](http://msdn.microsoft.com/library/hh873175.aspx) auf der Microsoft-Website.
- Seite [Pipelines and Multiplexers](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) (in englischer Sprache) im GitHub-Repository StackExchange.Redis.
- Seite [Redis Persistence](http://redis.io/topics/persistence) (in englischer Sprache) auf der Redis-Website.
- Seite [Replication](http://redis.io/topics/replication) (in englischer Sprache) auf der Redis-Website.
- Seite [Redis Cluster Tutorial](http://redis.io/topics/cluster-tutorial) (in englischer Sprache) auf der Redis-Website.
- Seite [Partitioning: how to split data among multiple Redis instances](http://redis.io/topics/partitioning) (in englischer Sprache) auf der Redis-Website.
- Seite [Using Redis as an LRU Cache](http://redis.io/topics/lru-cache) (in englischer Sprache) auf der Redis-Website.
- Seite [Transactions](http://redis.io/topics/transactions) (in englischer Sprache) auf der Redis-Website.
- Seite [Redis Security](http://redis.io/topics/security) (in englischer Sprache) auf der Redis-Website.
- Seite [Lap around Azure Redis Cache](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) (in englischer Sprache) im Azure-Blog.
- Seite [Running Redis on a CentOS Linux VM in Windows Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) (in englischer Sprache) auf der Microsoft-Website.
- Seite [ASP.NET-Sitzungsstatusbieter für den Azure Redis-Cache](redis-cache/cache-asp.net-session-state-provider.md) auf der Microsoft-Website.
- Seite [ASP.NET-Ausgabecacheanbieter für den Azure Redis-Cache](redis-cache/cache-asp.net-output-cache-provider.md) auf der Microsoft-Website.
- Die Seite [An Introduction to Redis data types and abstractions](http://redis.io/topics/data-types-intro) (in englischer Sprache) auf der Redis-Website.
- Seite [Basic Usage](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) (in englischer Sprache) auf der StackExchange.Redis-Website.
- Seite [Transactions in Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) (in englischer Sprache) im GitHub-Repository StackExchange.Redis.
- Seite [Data Partitioning Guidance](http://msdn.microsoft.com/library/dn589795.aspx) (in englischer Sprache) auf der Microsoft-Website.

<!---HONumber=AcomDC_1223_2015-->