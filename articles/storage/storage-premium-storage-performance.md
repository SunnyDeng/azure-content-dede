<properties
    pageTitle="Azure Storage Premium: Entwurf für hohe Leistung | Microsoft Azure"
    description="Entwerfen Sie Hochleistungsanwendungen mithilfe von Azure Storage Premium. Storage Premium bietet Datenträgerunterstützung für hohe Leistung mit geringer Latenz für E/A-intensive Workloads, die auf virtuellen Azure-Computern ausgeführt werden."
    services="storage"
    documentationCenter="na"
    authors="ms-prkhad"
    manager=""
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/04/2015"
    ms.author="prkhad"/>

# Azure Storage Premium: Entwurf für hohe Leistung

## Übersicht  
Dieser Artikel bietet Leitlinien zum Erstellen leistungsstarker Anwendungen mit Azure Storage Premium. Sie können die Anweisungen in diesem Dokument kombiniert mit den bewährten Methoden für hohe Leistung befolgen, die für von Ihrer Anwendung verwendeten Technologien gelten. Um die Leitlinien zu veranschaulichen, haben wir SQL Server in Storage Premium im gesamten Dokument als Beispiel verwendet.

Während wir uns in diesem Artikel mit leistungsbezogenen Szenarien auf der Speicherebene beschäftigen, ist es Ihre Aufgabe, die Anwendungsebene zu optimieren. Wenn z. B. Sie eine SharePoint-Farm in Azure Storage Premium hosten, können die SQL Server-Beispiele in diesem Artikel zum Optimieren des Datenbankservers verwenden. Optimieren Sie darüber hinaus den Webserver und Anwendungsserver der SharePoint-Farm, um die beste Leistung zu erzielen.

In diesem Artikel werden häufig gestellte Fragen zum Optimieren der Anwendungsleistung in Azure Storage Premium beantwortet:

-   Wie kann die Leistung Ihrer Anwendung gemessen werden?  
-   Warum erzielen Sie nicht die erwartete hohe Leistung?  
-   Welche Faktoren beeinflussen die Anwendungsleistung in Storage Premium?  
-   Wie wirken sich diese Faktoren auf die Leistung Ihrer Anwendung in Storage Premium aus?  
-   Wie kann eine Optimierung hinsichtlich IOPS, Bandbreite und Latenz erfolgen?  

Wir stellen diese Leitlinien speziell für Storage Premium bereit, da in Storage Premium ausgeführte Workloads überaus leistungsabhängig sind. Sofern hilfreich, stellen wir Beispiele bereit. Einige dieser Leitlinien können auch für Anwendungen befolgt werden, die auf virtuellen IaaS-Computern mit Storage Standard-Datenträgern ausgeführt werden.

Falls Sie noch nicht mit Storage Premium vertraut sind, lesen Sie zunächst den Artikel [Einführung in Storage Premium](storage-premium-storage-preview-portal.md) und anschließend [Skalierbarkeits- und Leistungsziele für Azure Storage Premium](storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts).

## Anwendungsleistungsindikatoren  
Die Leistung einer Anwendung (ob gut oder schlecht) bewerten wir mithilfe von Leistungsindikatoren wie z. B. Geschwindigkeit der Verarbeitung einer Benutzeranforderung, Menge der verarbeiteten Daten pro Anforderung, Anzahl der in einem bestimmten Zeitraum verarbeiteten Anforderungen und Wartezeit des Benutzers auf eine Antwort nach Übermittlung einer Anforderung. Die technische Begriffe für diese Leistungsindikatoren sind IOPS, Durchsatz oder Bandbreite und Latenz (Wartezeit).

In diesem Abschnitt erörtern wir gängige Leistungsindikatoren im Zusammenhang mit Storage Premium. Im folgenden Abschnitt, „Erfassen von Anwendungsanforderungen“, erfahren Sie, wie Sie diese Leistungsindikatoren für Ihre Anwendung messen. Im Anschluss lernen Sie unter „Optimieren der Anwendungsleistung“ die Faktoren, die sich auf diese Leistungsindikatoren auswirken, und Empfehlungen zu ihrer Optimierung kennen.

## IOPS  
IOPS beschreibt die Anzahl von Anforderungen, die Ihre Anwendung pro Sekunde an Speicherdatenträger sendet. Ein E/A-Vorgang kann ein sequenzieller oder zufälliger Lese- oder Schreibvorgang sein. OLTP-Anwendungen wie die Website eines Onlinehändlers müssen viele gleichzeitige Benutzeranforderungen sofort verarbeiten. Die Benutzeranforderungen sind einfüge- und aktualisierungsintensive Datenbanktransaktionen, die die Anwendung rasch verarbeiten muss. Deshalb benötigen OLTP-Anwendungen eine sehr hohe IOPS-Leistung. Solche Anwendungen verarbeiten Millionen kleiner und zufälliger E/A-Anforderungen. Wenn Sie eine solche Anwendung haben, müssen Sie die Anwendungsinfrastruktur für die IOPS-Optimierung entwerfen. Im Abschnitt *Optimieren der Anwendungsleistung* weiter unten erörtert wir detailliert alle Faktoren, die Sie berücksichtigen müssen, um hohe IOPS-Raten zu erzielen.

Wenn Sie einen Storage Premium-Datenträger an Ihre Hochleistungs-VM anfügen, stellt Ihnen Azure gemäß der Datenträgerspezifikation eine garantierte IOPS-Anzahl bereit. Beispielsweise stellt ein Datenträger vom Typ „P30“ 5000 IOPS bereit. Jeder Typ von Hochleistungs-VM weist außerdem einen bestimmtes IOPS-Limit auf. Bei einer Standard-VM vom Typ GS5 ist das Limit beispielsweise 80.000 IOPS.

## Durchsatz  
Durchsatz oder Bandbreite ist die Menge der Daten, die Ihre Anwendung in einem angegebenen Intervall an die Speicherdatenträger überträgt. Wenn Ihre Anwendung E/A-Vorgänge mit hohen E/A-Einheitsgrößen durchführt, benötigt sie hohen Durchsatz. Data Warehouse-Anwendungen führen meist suchintensive Vorgänge, bei denen jeweils auf große Datenmengen zugegriffen wird, und üblicherweise Massenvorgänge durch. Aus diesem Grund erfordern solche Anwendungen einen höheren Durchsatz. Wenn Sie eine solche Anwendung haben, müssen Sie die Anwendungsinfrastruktur für eine Durchsatzoptimierung entwerfen. Im nächsten Abschnitt geht es im Detail um die Faktoren, die Sie optimieren müssen, um dies zu erreichen.

Wenn Sie einen Storage Premium-Datenträger an eine Hochleistungs-VM anfügen, bietet Azure Durchsatz gemäß der Spezifikation dieses Datenträgers. Ein P30-Datenträger stellt z. B. einen Datenträgerdurchsatz von 200 MB pro Sekunde bereit. Jeder Typ von Hochleistungs-VM weist außerdem ein bestimmtes Durchsatzlimit auf. Eine Standard-VM vom Typ GS5 bietet beispielsweise einen maximalen Durchsatz von 2.000 MB pro Sekunde.

Zwischen Durchsatz und IOPS gibt es, wie in der folgenden Formel dargestellt, eine Beziehung.

![](media/storage-premium-storage-performance/image1.png)

Aus diesem Grund ist es wichtig, die optimalen Durchsatz- und IOPS-Werte zu bestimmen, die Ihre Anwendung benötigt. Beim Versuch, einen der Faktoren zu optimieren, ist der andere ebenfalls betroffen. Im Abschnitt *Optimieren der Anwendungsleistung* weiter unten werden weitere Details zum Optimieren von IOPS und Durchsatz erläutert.

## Latenz  
Latenz ist die Zeit, die eine Anwendung zum Empfangen einer einzelnen Anforderung, deren Übertragung an die Speicherdatenträger und zum Zurücksenden der Antwort an den Client benötigt. Neben IOPS und Durchsatz ist dies ein weiterer wichtiger Messwert für die Leistung einer Anwendung. Die Latenz eines Storage Premium-Datenträgers ist die benötigte Zeit zum Abrufen der Informationen für eine Anforderung und deren Übermittlung zurück an die Anwendung. Storage Premium bietet durchgängig eine niedrige Latenz. Wenn Sie das Hostcache-Einstellung „ReadOnly“ für Storage Premium-Datenträger aktivieren, erhalten Sie bei Lesevorgängen eine wesentlich niedrigere Latenz. Der Datenträgercache wird weiter unten im Abschnitt *Optimieren der Anwendungsleistung* ausführlicher erläutert.

Wenn Sie Ihre Anwendung optimieren, um höhere IOPS- und Durchsatzwerte zu erzielen, wirkt sich dies auf die Latenz der Anwendung aus. Prüfen Sie nach einer Optimierung der Anwendungsleistung stets die Latenz, um unerwartet hohe Latenzen zu vermeiden.

## Erfassen von Anforderungen an die Anwendungsleistung  
Der erste Schritt beim Entwerfen hochleistungsfähiger Anwendungen in Azure Storage Premium besteht darin, sich mit den Leistungsanforderungen der Anwendung vertraut zu machen. Nach dem Erfassen von Anforderungen können Sie Ihre Anwendung optimieren, um eine optimale Leistung zu erzielen.

Im vorherigen Abschnitt wurden mit IOPS, Durchsatz und Latenz die gängigsten Leistungsindikatoren vorgestellt. Sie müssen bestimmen, welche dieser Leistungsindikatoren für Ihre Anwendung wichtig sind, um Benutzern die gewünschte Funktionalität zu bieten. Eine hohe IOPS-Leistung spielt z. B. bei OLTP-Anwendungen, die Millionen von Transaktionen pro Sekunde verarbeiten, die größte Rolle. Ein hoher Durchsatz ist hingegen für Data Warehouse-Anwendungen von Bedeutung, die pro Sekunde große Datenmengen verarbeiten. Eine überaus niedrige Latenz ist entscheidend für Echtzeitanwendungen wie Websites für das Livestreaming von Video.

Messen Sie als Nächstes die maximalen Leistungsanforderungen der Anwendung während ihrer Lebensdauer. Nutzen Sie als Ausgangspunkt die nachstehende Beispielprüfliste. Zeichnen Sie die maximalen Leistungsanforderungen von Workloads während des Normalbetriebs, zu Spitzenzeiten und außerhalb der Geschäftszeiten auf. Indem Sie Anforderungen für alle Workloadstufen bestimmen, können Sie die Gesamtleistungsanforderungen Ihrer Anwendung feststellen. Der normale Workload einer E-Commerce-Website besteht beispielsweise aus den Transaktionen, die während der meisten Tage eines Jahres verarbeitet werden. Den Spitzenworkload der Website bilden die Transaktionen, die in der Vorweihnachtszeit oder bei Sonderverkaufsaktionen verarbeitet werden. Der Spitzenworkload fällt meist nur in einem begrenzten Zeitraum an, kann allerdings erfordern, dass Ihre Anwendung im Vergleich zum Normalbetrieb um das Zwei- bis Dreifache skaliert werden muss. Finden Sie die Anforderungen für das 50. Perzentil, das 90. Perzentil und 99. Perzentil heraus. Dadurch können Sie Ausreißer in den Leistungsanforderungen herausfiltern und sich auf die Maßnahmen für eine Optimierung entsprechend den richtigen Werten konzentrieren.

**Prüfliste für das Erfassen von Anforderungen an die Anwendungsleistung**

| **Leistungsanforderungen** | **50. Perzentil** | **90. Perzentil** | **99. Perzentil** |
|---|---|---|---|
| Maximal Transaktionen pro Sekunde | | | |
| % Lesevorgänge | | | |
| % Schreibvorgänge | | | |
| % zufällige Vorgänge | | | |
| % sequenzielle Vorgänge | | | |
| Größe der E/A-Anforderung | | | |
| Durchschnittlicher Durchsatz | | | |
| Max. Durchsatz | | | |
| Min. Latenz | | | |
| Durchschnittliche Latenz | | | |
| Max. CPU | | | |
| Durchschnittliche CPU-Nutzung | | | |
| Max. Arbeitsspeicher | | | |
| Durchschnittliche Arbeitsspeichernutzung | | | |
| Warteschlangenlänge | | | |

>**Wichtiger Hinweis:**  
>Es empfiehlt sich, diese Zahlen basierend auf dem erwarteten zukünftigen Wachstum Ihrer Anwendung zu skalieren. Es ist stets eine gute Idee, für Wachstum im Voraus zu planen, da es schwieriger sein könnte, die Infrastruktur zur Verbesserung der Leistung später zu ändern.

Wenn Sie eine vorhandene Anwendung haben, die in Storage Premium verschoben werden soll, erstellen Sie für diese Anwendung zunächst die obige Prüfliste. Erstellen Sie anschließend einen Prototyp der Anwendung in Storage Premium, und entwerfen Sie die Anwendung basierend auf den Leitlinien unter *Optimieren der Anwendungsleistung* weiter unten in diesem Dokument. Im nächsten Abschnitt werden die Tools beschrieben, die Sie verwenden können, um die Leistungsindikatoren zu erfassen.

Erstellen Sie eine Prüfliste ähnlich derjenigen für die vorhandene Anwendung für den Prototyp. Mithilfe von Benchmarktools können Sie die Workloads simulieren und die Leistung der Prototypanwendung messen. Im Abschnitt *Benchmarktests* erfahren Sie mehr dazu. Auf diese Weise Sie bestimmen können, ob die Anforderungen Ihrer Anwendung mit Storage Premium erfüllt oder gar übertroffen werden. Anschließend können Sie dieselben Richtlinien für Ihre Produktionsanwendung implementieren.

### Indikatoren zum Messen der Leistungsanforderungen der Anwendung  
Die beste Methode zum Messen der Leistungsanforderungen Ihrer Anwendung ist die Verwendung von Leistungsüberwachungstools, die vom Betriebssystem des Servers bereitgestellt werden. Sie können Systemmonitor (PerfMon) für Windows und iostat für Linux verwenden. Diese Tools erfassen Indikatoren für jede im vorherigen Abschnitt erläuterte Messgröße. Sie müssen die Werte dieser Indikatoren erfassen, wenn in der Anwendung Workloads im Normalbetrieb, zu Spitzenzeiten und außerhalb der Geschäftszeiten ausgeführt werden.

Die Leistungsindikatoren im Systemmonitor sind für Prozessor, Arbeitsspeicher und alle logischen und physischen Datenträger Ihres Servers verfügbar. Bei Verwenden von Storage Premium-Datenträgern mit einem virtuellen Computer gelten die Indikatoren für physische Datenträger für jeden Storage Premium-Datenträger. Die Indikatoren für logische Datenträger gelten für jedes Volume, das auf den Storage Premium-Datenträgern erstellt wurde. Sie müssen die Werte für die Datenträger erfassen, die den Workload Ihrer Anwendung hosten. Wenn es eine 1: 1-Zuordnung zwischen logischen und physischen Datenträgern gilt, beziehen Sie sich auf die Leistungsindikatoren für physische Datenträger und andernfalls auf die Leistungsindikatoren für logische Datenträger. Unter Linux erzeugt der Befehl „iostat“ einen Bericht der CPU- und Festplattenauslastung. Der Bericht zur Datenträgerauslastung bietet Statistiken pro physischem Gerät bzw. pro Partition. Wenn Sie einen Datenbankserver mit Daten- und Protokolldateien auf getrennten Datenträgern nutzen, erfassen Sie diese Daten für beide Datenträger. In der folgenden Tabelle werden die Leistungsindikatoren für Datenträger, Prozessor und Arbeitsspeicher beschrieben:

| Indikator | Beschreibung | Systemmonitor | iostat |
|---|---|---|---|
| **IOPS oder Transaktionen pro Sekunde** | Anzahl der an den Speicherdatenträger pro Sekunde erfolgten E/A-Anforderungen. | Lesevorgänge/s <br> Schreibvorgänge/s | tps <br> r/s <br> w/s |
| **Lese- und Schreibvorgänge auf Datenträger** | % der auf dem Datenträger ausgeführten Lese- und Schreibvorgänge. | % Lesezeit <br> % Schreibzeit | r/s <br> w/s |
| **Durchsatz** | Menge der Daten, die pro Sekunde auf dem Datenträger gelesen oder geschrieben wurde. | Byte gelesen/s <br> Byte geschrieben/s | kB\_read/s <br> kB\_wrtn/s |
| **Latenz** | Zeitaufwand für das Ausführen einer E/A-Anforderung an den Datenträger. | Mittlere Sek./Lesevorgänge <br> Mittlere Sek./Schreibvorgänge | await <br> svctm |
| **E/A-Größe** | Größe der an die Speicherdatenträger gesendeten E/A-Anforderungen. | Mittlere Bytes/Lesevorgang <br> Mittlere Bytes/Schreibvorgang | avgrq-sz |
| **Warteschlangenlänge** | Anzahl der ausstehenden E/A-Anforderungen, die vom Speicherdatenträger gelesen oder auf diesen geschrieben werden sollen. | Aktuelle Warteschlangenlänge | avgqu-sz |
| **Max. Arbeitsspeicher** | Für die reibungslose Ausführung der Anwendung erforderlicher Arbeitsspeicher | Zugesicherte verwendete Bytes (%) | vmstat verwenden |
| **Max. CPU** | Für die reibungslose Ausführung der Anwendung erforderliche CPU-Größe | % Prozessorzeit | %util |

Weitere Informationen zu [iostat](http://linuxcommand.org/man_pages/iostat1.html) und Systemmonitor [PerfMon](https://msdn.microsoft.com/library/aa645516(v=vs.71).aspx).


## Optimieren der Anwendungsleistung  
Die wichtigsten Faktoren für die Leistung einer Anwendung, die in Storage Premium ausgeführt wird, sind die Art der E/A-Anforderungen, VM-Größe, Datenträgergröße, Anzahl der Datenträger, die Datenträgerzwischenspeicherung, Multithreading und Warteschlangenlänge. Sie können einige dieser Faktoren mithilfe vom System bereitgestellter Einstellungsmöglichkeiten steuern. Die meisten Anwendungen bieten jedoch möglicherweise keine Option, die E/A-Größe und Warteschlangenlänge direkt zu ändern. In SQL Server können Sie z. B. die E/A-Größe und Warteschlangenlänge nicht festlegen. SQL Server wählt optimale Einstellungen für E/A-Größe und Warteschlangenlänge, um die beste Leistung zu erzielen. Es ist wichtig, die Auswirkungen beider Arten von Faktoren auf die Leistung Ihrer Anwendung zu verstehen, damit Sie entsprechende Ressourcen zum Erfüllen der Leistungsanforderungen bereitstellen können.

Nehmen Sie in diesem gesamten Abschnitt Bezug auf die von Ihnen erstellte Prüfliste mit den Anwendungsanforderungen, um zu bestimmen, was für die Optimierung der Leistung Ihrer Anwendung erforderlich ist. Auf dieser Basis können Sie bestimmen, welche Faktoren in diesem Abschnitt Sie optimieren müssen. Führen Sie zum Überprüfen der Auswirkungen der einzelnen Faktoren auf die Anwendungsleistung Benchmarktools aus. Im Abschnitt [Benchmarktests](#_Benchmarking) am Ende dieses Artikels finden Sie Anweisungen zum Ausführen gängiger Benchmarktools für Windows- und Linux-VMs.

### Die Optimierung von IOPS, Durchsatz und Latenz auf einen Blick  
In der folgenden Tabelle sind sämtliche Leistungsfaktoren und die Schritte zum Optimieren von IOPS, Durchsatz und Latenz aufgeführt. In den Abschnitten im Anschluss an diese Übersicht werden die einzelnen Faktoren ausführlicher beschrieben.

| | **IOPS** | **Durchsatz** | **Latenz** |
|---|---|---|---|
| **Beispielszenario** | OLTP-Anwendung eines Großunternehmens, die eine sehr hohe Rate von Transaktionen pro Sekunde benötigt. | Data Warehouse-Anwendung eines Großunternehmens, die große Datenmengen verarbeitet. | Nahezu in Echtzeit arbeitende Anwendungen, die Antworten auf Benutzeranforderungen sofort benötigen, wie z. B. Onlinespiele. |
| Leistungsfaktoren | | | |
| **E/A-Größe** | Kleinere E/A-Größe führt zu mehr IOPS. | Höhere E/A-Größe führt zu höherem Durchsatz. | |
| **Größe des virtuellen Computers** | Wählen Sie eine VM-Größe, deren IOPS die Anforderung Ihrer Anwendung übersteigt. Siehe VM-Größen und ihre IOPS-Limits hier. | Wählen Sie eine VM-Größe, deren Durchsatzlimit die Anforderung Ihrer Anwendung übersteigt. Siehe VM-Größen und die zugehörigen Durchsatzlimits hier. | Wählen Sie eine VM-Größe, deren Skalierungslimits die Anforderung Ihrer Anwendung übersteigen. Siehe VM-Größen und die zugehörigen Limits hier. |
| **Datenträgergröße** | Wählen Sie eine Datenträgergröße, deren IOPS die Anforderung Ihrer Anwendung übersteigt. Siehe Datenträgergrößen und die zugehörigen IOPS-Limits hier. | Wählen Sie eine Datenträgergröße, deren Durchsatzlimit die Anforderung Ihrer Anwendung übersteigt. Siehe Datenträgergrößen und die zugehörigen Durchsatzlimits hier. | Wählen Sie eine Datenträgergröße, deren Skalierungslimits die Anforderung Ihrer Anwendung übersteigen. Siehe Datenträgergrößen und die zugehörigen Limits hier. |
| **Skalierungslimits für virtuelle Computer und Datenträger** | Das IOPS-Limit der gewählten VM-Größe muss größer als die IOPS-Gesamtkapazität von Storage Premium-Datenträgern sein, die angefügt sind. | Das Durchsatzlimit der gewählten VM-Größe muss größer als der Gesamtdurchsatz von Storage Premium-Datenträgern sein, die angefügt sind. | Die Skalierungslimits der gewählten VM-Größe müssen größer als die Gesamtskalierungslimits von Storage Premium-Datenträgern sein, die angefügt sind. |
| **Nutzung des Datenträgercaches** | Aktivieren Sie den „ReadOnly“-Cache auf Storage Premium-Datenträgern mit hohem Aufkommen von Lesevorgängen, um eine höhere IOPS-Leseleistung zu erhalten. | | Aktivieren Sie den „ReadOnly“-Cache auf Storage Premium-Datenträgern mit hohem Aufkommen von Lesevorgängen, um sehr niedrige Leselatenzen zu erzielen. |
| **Datenträgerstriping** | Verwenden Sie mehrere Datenträger, und verbinden Sie sie per Striping, um durch die Kombination ein höheres IOPS- und Durchsatzlimit zu erreichen. Beachten Sie, dass das kombinierte Limit pro VM höher als die kombinierten Limits angefügter Storage Premium-Datenträger sein muss. | |
| **Stripegröße** | Kleinere Stripegröße für zufällige kleine E/A-Muster in OLTP-Anwendungen. Wählen Sie z. B. für eine OLTP-Anwendung mit SQL Server eine Stripegröße von 64 KB. | Höhere Stripegröße für sequenzielle E/A-Muster in Data Warehouse-Anwendungen. Wählen Sie beispielsweise für Data Warehouse-Anwendungen mit SQL Server die Stripegröße 256 KB. | |
| **Multithreading** | Verwenden Sie Multithreading, um eine höhere Anzahl von Anforderungen in Storage Premium zu übertragen, was eine höhere IOPS- und Durchsatzrate zur Folge hat. Legen Sie z. B. für SQL Server einen hohen MAXDOP-Wert fest damit SQL Server mehr CPUs zugewiesen werden. | |
| **Warteschlangenlänge** | Höhere Warteschlangenlänge führt zu mehr IOPS. | Höhere Warteschlangenlänge führt zu höherem Durchsatz. | Niedrigere Warteschlangenlänge führt zu niedrigeren Latenzen. |

## Art der E/A-Anforderungen  
Eine E/A-Anforderung ist eine E/A-Vorgangseinheit, die Ihre Anwendung ausführt. Das Bestimmen der Art der E/A-Anforderungen – zufällig oder sequenziell, Lese- oder Schreibvorgang, klein oder groß – hilft beim Ermitteln der Leistungsanforderungen Ihrer Anwendung. Sie müssen sich unbedingt mit der Art der E/A-Anforderungen vertraut machen, um beim Entwurf der Infrastruktur Ihrer Anwendung die richtigen Entscheidungen zu treffen.

Die E/A-Größe ist einer der wichtigsten Faktoren. Die E/A-Größe ist die Größe des angeforderten E/A-Vorgangs, den Ihre Anwendung generiert hat. Die E/A-Größe hat einen erheblichen Einfluss auf die Leistung, insbesondere auf die IOPS und Bandbreite, die die Anwendung erzielen kann. Die folgende Formel zeigt die Beziehung zwischen IOPS, E/A-Größe und Bandbreite/Durchsatz.  
	![](media/storage-premium-storage-performance/image1.png)

Einige Anwendungen ermöglichen Ihnen das Ändern der E/A-Größe, andere hingegen nicht. Beispielsweise bestimmt SQL Server selbst die optimale E/A-Größe und bietet Benutzern keine Möglichkeiten, sie zu ändern. Oracle bietet dagegen den Parameter [DB\_BLOCK\_SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815), mit dem Sie die E/A-Anforderungsgröße der Datenbank konfigurieren können.

Wenn Sie eine Anwendung verwenden, die das Ändern der E/A-Größe nicht ermöglicht, befolgen Sie die Leitlinien in diesem Artikel, um die Leistungsindikatoren zu optimieren, die für Ihre Anwendung am relevantesten sind. Beispiel:

-   Eine OLTP-Anwendung generiert Millionen kleiner und zufälliger E/A-Anforderungen. Zum Verarbeiten diese Art von E/A-Anforderungen müssen Sie die Anwendungsinfrastruktur für das Erzielen von mehr IOPS entwerfen.  
-   Eine Data Warehouse-Anwendung generiert große und sequenzielle E/A-Anforderungen. Zum Verarbeiten dieser Art von E/A-Anforderungen müssen Sie die Anwendungsinfrastruktur für das Erzielen von mehr Bandbreite bzw. Durchsatz entwerfen.

Wenn Sie eine Anwendung verwenden, die das Ändern der E/A-Größe ermöglicht, sollten Sie neben den anderen Leitlinien für die Leistung diese Faustregel für die E/A-Größe befolgen.

-   Kleinere E/A-Größe führt zu mehr IOPS. Beispiel: 8 KB für eine OLTP-Anwendung.  
-   Höhere E/A-Größe führt zu mehr Bandbreite/Durchsatz. Beispiel: 1024 KB für eine Data Warehouse-Anwendung.

Es folgt ein Beispiel, wie Sie IOPS bzw. Durchsatz/Bandbreite für Ihre Anwendung berechnen können. Angenommen, eine Anwendung verwendet einen P30-Datenträger. Die maximale IOPS- bzw. Durchsatz-/Bandbreitenrate eines P30-Datenträgers ist 5000 IOPS bzw. 200 MB pro Sekunde. Wenn Ihre Anwendung nun die maximale IOPS-Rate des P30-Datenträgers benötigt und Sie eine kleinere E/A-Größe wie 8 KB verwenden, beträgt die resultierende Bandbreite 40 MB pro Sekunde. Wenn Ihre Anwendung dagegen die maximale Durchsatz-/Bandbreitenkapazität des P30-Datenträgers benötigt und Sie eine höhere E/A-Größe wie 1024 KB verwenden, erzielen Sie weniger als 200 IOPS. Optimieren Sie die E/A-Größe daher so, dass Ihre Anwendung die Anforderungen sowohl an IOPS als auch an Durchsatz/Bandbreite erfüllt. In der folgenden Tabelle sind die verschiedenen E/A-Größen und ihre entsprechenden IOPS- und Durchsatzraten für einen P30-Datenträger zusammengefasst.

| **Anwendungsanforderung** | **E/A-Größe** | **IOPS** | **Durchsatz/Bandbreite** |
|-----------------------------|--------------|----------|--------------------------|
| Max. IOPS | 8 KB | 5\.000 | 40 MB pro Sekunde |
| Max. Durchsatz | 1024 KB | 200 | 200 MB pro Sekunde |
| Max. Durchsatz + hohe IOPS-Rate | 64 KB | 3\.200 | 200 MB pro Sekunde |
| Max. IOPS + hoher Durchsatz | 32 KB | 5\.000 | 160 MB pro Sekunde |

Um eine IOPS-Rate und Bandbreite zu erzielen, die höher als der Maximalwert eines einzelnen Storage Premium-Datenträgers ist, verwenden Sie mehrere Premium-Datenträger in einem Stripeset. Fügen Sie z. B. zwei P30-Datenträger zu einem Stripeset zusammen, um in dieser Kombination eine IOPS-Rate von 10.000 oder einen Durchsatz von 400 MB pro Sekunde zu erzielen. Wie im nächsten Abschnitt erläutert, müssen Sie eine VM-Größe wählen, die die IOPS-Rate und den Durchsatz der kombinierten Datenträger unterstützt.

>**Hinweis:**  
>Wenn Sie entweder die IOPS-Rate oder den Durchsatz erhöhen, sollten Sie sicherstellen, dass Sie dabei nicht das Durchsatz- oder IOPS-Limit des Datenträgers oder der VM überschreiten.

Führen Sie zum Überprüfen der Auswirkungen der E/A-Größe auf die Anwendungsleistung in Ihrer VM und auf Ihren Datenträgern Benchmarktools aus. Erstellen Sie mehrere Testläufe, und wählen Sie für jeden Lauf eine andere E/A-Größe, um die Auswirkungen zu prüfen. Im Abschnitt [Benchmarktests](#_Benchmarking) am Ende dieses Artikels finden Sie weitere Details.

## Größen von Hochleistungs-VMs  
Beim Entwerfen einer Anwendung ist eine der ersten Aufgaben das Wählen einer VM zum Hosten Ihrer Anwendung. Storage Premium bietet verschiedene Größen von Hochleistungs-VMs zum Ausführen von Anwendungen, die eine höhere Rechenleistung und einen lokalen Datenträger mit hoher E/A-Leistung benötigen. Diese VMs bieten schnellere Prozessoren, ein höheres Arbeitsspeicher/Kern-Verhältnis und ein SSD-Laufwerk (Solid State Drive) als lokalen Datenträger. Beispiele für Hochleistungs-VMs mit Unterstützung von Storage Premium sind die VM-Serien DS und GS.

Hochleistungs-VMs stehen in mehreren Größen mit jeweils einer anderen Anzahl von CPU-Kernen, Arbeitsspeicher- und temporären Datenträgergröße und einem anderen Betriebssystem zur Verfügung. Jede VM-Größe weist außerdem eine maximale Anzahl von Datenträgern auf, die Sie an die VM anfügen können. Daher beeinflusst die gewählte VM-Größe die Verarbeitungs-, Arbeitsspeicher- und Speicherkapazität, die für Ihre Anwendung zur Verfügung steht. Sie hat außerdem Einfluss auf die Compute- und Speicherkosten. Es folgen die Spezifikationen der höchsten VM-Größe in der DS- und GS-Serie:

| VM-Größe | CPU-Kerne | Arbeitsspeicher | VM-Datenträgergrößen | Max. Anzahl Datenträger | Cachegröße | IOPS | Limits für Bandbreite, Cache und E/A |
|---|---|---|---|---|---|---|---|
| Standard\_DS14 | 16 | 112 GB | Betriebssystem = 1023 GB <br> Lokales SSD = 224 GB | 32 | 576 GB | 50\.000 IOPS <br> 512 MB pro Sekunde | 4\.000 IOPS und 33 MB pro Sekunde |
| Standard\_GS5 | 32 | 448 GB | Betriebssystem = 1023 GB <br> Lokales SSD = 896 GB | 64 | 4\.224 GB | 80\.000 IOPS <br> 2.000 MB pro Sekunde | 5\.000 IOPS und 50 MB pro Sekunde |

Eine vollständige Übersicht mit allen verfügbaren Azure-VM-Größen finden Sie unter [Größen für virtuelle Azure-Computer](virtual-machines-size-specs.md). Wählen Sie eine VM-Größe, die Ihre Anforderungen an die Anwendungsleistung erfüllen und entsprechend skaliert werden kann. Berücksichtigen Sie darüber hinaus folgende wichtige Aspekte bei der Wahl der VM-Größe.

*Skalierungslimits*  
Die maximalen IOPS-Limits pro VM und Datenträger sind unterschiedlich und voneinander unabhängig. Stellen Sie sicher, dass die Anwendung die IOPS innerhalb der Limits der VM sowie der angefügten Storage Premium-Datenträger unterstützt. Andernfalls wird die Anwendungsleistung gedrosselt.

Beispiel: Angenommen, eine Anwendungsanforderung ist ein IOPS-Wert von maximal 4.000. Um dies zu erreichen, stellen Sie einen P30-Datenträger in einer VM der DS1-Serie bereit. Der P30-Datenträger kann bis zu 5.000 IOPS bieten. Die VM der DS1-Serie ist jedoch auf 3.200 IOPS beschränkt. Daher wird die Anwendungsleistung gemäß dem VM-Limit auf 3.200 IOPS verringert. Um dies zu vermeiden, wählen eine VM- und Datenträgergröße, die beide Anwendungsanforderungen erfüllt.

*Betriebskosten*  
In vielen Fällen ist es möglich, dass die Gesamtbetriebskosten bei der Verwendung von Storage Premium niedriger als bei der Verwendung von Storage Standard sind.

Nehmen wir als Beispiel eine Anwendung, die 16.000 IOPS benötigt. Um diese Leistung zu erzielen, brauchen Sie eine Azure IaaS-VM vom Typ „Standard\_D14“, die bei Verwenden von 32 Storage Standard-Datenträgern mit je 1 TB eine maximale IOPS-Rate von 16.000 bietet. Jeder Storage Standard-Datenträger mit 1 TB kann maximal 500 IOPS erzielen. Die geschätzten Kosten für diese VM pro Monat liegen bei 1.570 $. Die monatliche Kosten der 32 Storage Standard-Datenträger betragen 1.638 $. Die geschätzten monatlichen Gesamtkosten betragen 3.208 $.

Wenn Sie jedoch dieselbe Anwendung in Storage Premium hosten, benötigen Sie eine kleinere VM-Größe und weniger Storage Premium-Datenträger, wodurch die Gesamtkosten gesenkt werden. Eine VM vom Typ Standard\_DS13 kann mithilfe von vier P30-Datenträgern die Anforderung von 16.000 IOPS erfüllen. Die VM vom Typ DS13 bietet maximal 25.600 IOPS, und jeder P30-Datenträger bietet maximal 5.000 IOPS. Diese Konfiguration kann insgesamt 5.000 x 4 = 20.000 IOPS bieten. Die geschätzten Kosten für diese VM pro Monat liegen bei 1.003 $. Die monatlichen Kosten der vier Storage Premium-Datenträger vom Typ P30 belaufen sich auf 544,34 $. Die geschätzten monatlichen Gesamtkosten betragen 1.544 $.

In der folgenden Tabelle wird die Aufschlüsselung der Kosten für Storage Standard und Storage Premium zusammengefasst.

| | **Standard** | **Premium** |
|---|---|---|
| **VM-Kosten pro Monat** | 1\.570.58 $ (Standard\_D14) | 1\.003,66 $ (Standard\_DS13) |
| **Datenträgerkosten pro Monat** | 1\.638,40 $ (32 1-TB-Festplatten) | 544,34 $ (4 P30-Datenträger) |
| **Gesamtkosten pro Monat** | 3\.208,98 $ | 1\.544,34 $ |

*Linux-Distributionen*  
Mit Azure Storage Premium erhalten Sie unter Windows und Linux das gleiche Maß an Leistung für virtuelle Computer. Wir unterstützen viele Arten von Linux-Distributionen. Eine vollständige Liste finden Sie [hier](virtual-machines-linux-endorsed-distributions.md). Es ist wichtig zu beachten, dass verschiedene Distributionen für verschiedene Arten von Workloads besser geeignet sind. Je nach Distribution, in der Ihr Workload ausgeführt wird, sind die Leistungsgrade unterschiedlich. Testen Sie die Linux-Distributionen mit Ihrer Anwendung, und wählen Sie diejenige, die am besten geeignet ist.

Überprüfen Sie bei Ausführen von Linux mit Storage Premium die neuesten Updates hinsichtlich erforderlicher Treiber, um eine hohe Leistung zu gewährleisten.

## Größen von Storage Premium-Datenträgern  
Für Azure Storage Premium werden derzeit drei Datenträgergrößen angeboten. Jede Datenträgergröße hat ein anderes Skalierungslimit hinsichtlich IOPS, Bandbreite und Speicher. Wählen Sie abhängig von den Anwendungsanforderungen und der Größe der Hochleistungs-VM die richtige Storage Premium-Datenträgergröße. Die folgende Tabelle zeigt die verschiedenen Datenträgergrößen und ihre Kapazitäten.

| **Datenträgertyp** | **P10** | **P20** | **P30** |
|---------------------|-------------------|-------------------|-------------------|
| Datenträgergröße | 128 GB | 512 GB | 1\.024 GiB (1 TB) |
| IOPS pro Datenträger | 500 | 2\.300 | 5\.000 |
| Durchsatz pro Datenträger | 100 MB pro Sekunde | 150 MB pro Sekunde | 200 MB pro Sekunde |

Die zu wählende Anzahl von Datenträgern hängt von der gewählten Datenträgergröße ab. Zum Erfüllen Ihrer Anwendungsanforderung können Sie entweder einen einzelnen P30- oder mehrere P10-Datenträger verwenden. Berücksichtigen Sie bei Ihrer Wahl die nachstehenden Aspekte.

*Skalierungslimits (IOPS und Durchsatz)*  
Die IOPS- und Durchsatzlimits der einzelnen Premium-Datenträgergrößen sind unterschiedlich und unabhängig von den VM-Skalierungslimits. Vergewissern Sie sich, dass die gesamte IOPS- und Durchsatzrate der Datenträger innerhalb der Skalierungslimits der gewählten VM-Größe liegen.

Beispiel: Angenommen, eine Anwendungsanforderung hat einen Durchsatz von maximal 250 MB/s, und Sie verwenden eine VM vom Typ DS4 mit einem einzelnen P30-Datenträger. Die VM vom Typ DS4 kann einen Durchsatz von bis zu 256 MB/s bieten. Ein einzelner P30-Datenträger hat jedoch ein Durchsatzlimit von 200 MB/s.Demzufolge wird die Anwendung aufgrund des Datenträgerlimits auf 200 MB/s beschränkt. Um diese Beschränkung zu umgehen, stellen Sie für die VM mehr als einen Datenträger bereit.

>**Hinweis:**  
>Leseanforderungen, die aus dem Cache erfüllt werden, bleiben bei der IOPS- und Durchsatzleistung des Datenträgers unberücksichtigt und unterliegen deshalb keinen Datenträgerlimits. Für den Cache gelten separate IOPS- und Durchsatzlimits pro VM.
>
>Beispielsweise erfolgen Ihre Lese- und Schreibvorgänge anfänglich mit 60 MB/s bzw. 40 MB/s. Mit der Zeit füllt sich der Cache auf, sodass immer mehr Leseanforderungen aus dem Cache erfüllt werden. Dadurch erhalten Sie einen höheren Schreibdurchsatz vom Datenträger.

*Anzahl der Datenträger*  
Bestimmen Sie die benötigte Anzahl der Datenträger, indem Sie die Anwendungsanforderungen prüfen. Für jede VM gilt auch ein Limit für die Anzahl der Datenträger, die an die VM angefügt werden können. In der Regel ist dies die doppelte Anzahl der Kerne. Stellen Sie sicher, dass die gewählte VM-Größe die erforderliche Anzahl von Datenträgern unterstützt.

Wie schon erwähnt, bieten Storage Premium-Datenträger eine höhere Leistung als Storage Standard-Datenträger. Wenn Sie also Ihre Anwendung aus einer Azure IaaS-VM mit Storage Standard zu Storage Premium migrieren, benötigen Sie wahrscheinlich weniger Premium-Datenträger, um für Ihre Anwendung dieselbe oder eine höhere Leistung zu erzielen.

## Nutzung des Datenträgercaches  
Hochleistungs-VMs mit Azure Storage Premium arbeiten mit einer mehrschichtigen Cachetechnologie mit dem Namen BlobCache. BlobCache verwendet für das Zwischenspeichern eine Kombination aus RAM des virtuellen Computers und lokalem SSD-Laufwerk. Dieser Cache ist für die permanenten Storage Premium-Datenträger und die lokalen VM-Datenträger verfügbar. Standardmäßig ist diese Cacheeinstellung auf „Read/Write“ für Betriebssystem-Datenträger und auf und „ReadOnly“ für in Storage Premium gehostete Datenträger festgelegt. Bei aktiviertem Cache auf den Storage Premium-Datenträgern können die Hochleistungs-VMs überaus hohe Leistungsgrade erreichen, die die zugrunde liegende Datenträgerleistung übersteigen.

Weitere Informationen zur Funktionsweise von BlobCache finden Sie im Blogbeitrag [Inside Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/).

Es ist wichtig, den Cache für die richtige Gruppe von Datenträgern zu aktivieren. Ob Sie den Cache auf einem Premium-Datenträger aktivieren oder nicht, hängt vom Workloadmuster ab, das der Datenträger verarbeiten muss. Die folgende Tabelle zeigt die standardmäßigen Cacheeinstellungen für Betriebssystem und Datenträger.

| **Datenträgertyp** | **Standardeinstellung für den Cache** |
|---|---|
| Betriebssystem-Datenträger | ReadWrite |
| Datenträger | Keine |

Es folgen die empfohlenen Cacheeinstellungen für Datenträger:

| **Cacheeinstellung für Datenträger** | **Empfehlung zur Verwendung dieser Einstellung** |
|---|---|
| Keine | Konfigurieren Sie „host-cache“ mit „None“ für Datenträger mit hohem oder ausschließlichem Schreibzugriff. |
| ReadOnly | Konfigurieren Sie „host-cache“ mit „ReadOnly“ für Datenträger mit reinem Lese- und Lese-/Schreibzugriff. |
| ReadWrite | Konfigurieren Sie „host-cache“ nur dann mit „ReadWrite“, wenn Ihre Anwendung zwischengespeicherte Daten bei Bedarf ordnungsgemäß auf beständige Datenträger schreiben kann. |

*ReadOnly*  
Durch Konfigurieren des „ReadOnly“-Caches für Storage Premium-Datenträger können Sie für Ihre Anwendung eine niedrige Leselatenz und einen sehr hohe Leserate hinsichtlich IOPS und Durchsatz erzielen. Hierfür gibt es zwei Gründe:

1.  Aus dem Cache erfüllte Leseanforderungen, der sich im Arbeitsspeicher der VM und auf dem lokalen SSD-Laufwerk befindet, sind wesentlich schneller als Lesevorgänge vom Datenträger, der sich in Azure-Blobspeicher befindet.  
2.  Storage Premium rechnet die aus dem Cache erfüllten Leseanforderungen nicht zur IOPS- und Durchsatzrate des Datenträgers. Aus diesem Grund kann Ihre Anwendung eine höhere Gesamtrate bei IOPS und Durchsatz erzielen.

*ReadWrite*  
Für die Betriebssystem-Datenträger ist der „ReadWrite“-Cache standardmäßig aktiviert. Wir haben vor Kurzem Unterstützung des „ReadWrite“-Caches auf Datenträgern hinzugefügt. Wenn Sie den „ReadWrite“-Cache nutzen, benötigen Sie eine ordnungsgemäße Möglichkeit zum Schreiben der Daten aus dem Cache auf beständige Datenträger. SQL Server übernimmt beispielsweise selbst das Schreiben von Daten im Cache auf beständige Speicherdatenträger. Das Verwenden eines „ReadWrite“-Caches mit einer Anwendung, die die benötigten Daten nicht beständig speichert, kann zu Datenverlusten führen, sollte die VM abstürzen.

Als Beispiel können Sie diese Leitlinien auf SQL Server in Storage Premium anwenden, indem Sie die folgenden Schritte ausführen:

1.  Aktivieren Sie den „ReadOnly“-Cache auf Storage Premium-Datenträgern, die Datendateien hosten. a. Die schnellen aus dem Cache erfolgenden Lesevorgänge verkürzen die Abfragezeit von SQL Server, da Daten wesentlich schneller aus dem Cache als direkt von den Datenträgern abgerufen werden. b. Das Erfüllen von Leseanforderungen aus dem Cache bedeutet, dass auf den Premium-Datenträgern zusätzlicher Durchsatz verfügbar ist. SQL Server kann diesen zusätzlichen Durchsatz für das Abrufen von mehr Datenseiten und andere Vorgänge wie Sichern/Wiederherstellen, Batchladevorgänge und Indexneuerstellungen nutzen.  
2.  Konfigurieren der Cacheeinstellung „None“ für Storage Premium-Datenträger, die Protokolldateien hosten. a. Protokolldateien zeichnen sich hauptsächlich durch schreibintensive Vorgänge aus. Aus diesem Grund profitieren sie nicht vom „ReadOnly“-Cache.

## Datenträgerstriping  
Wenn an eine Hochleistungs-VM mehrere beständige Storage Premium-Datenträger angefügt werden, können diese Datenträger ein Stripeset bilden, um ihre IOPS-, Bandbreiten- und Speicherkapazitäten zusammenzuführen.

Unter Windows können Sie das Feature „Speicherplätze“ verwenden, um Datenträger in einem Stripeset zu verbinden. Sie müssen für jeden Datenträger in einem Pool eine Spalte konfigurieren. Andernfalls kann die Gesamtleistung des Stripesetvolume aufgrund einer ungleichen Verteilung des Datenverkehrs auf die Datenträger niedriger sein als erwartet.

Wichtig: Im Server-Manager können Sie die Gesamtanzahl der Spalten auf bis zu 8 für ein Stripesetvolume festlegen. Bei Anfügen von mehr als 8 Datenträgern nutzen Sie PowerShell, um das Volume zu erstellen. Mithilfe von PowerShell können Sie die Anzahl der Spalten entsprechend der Anzahl der Datenträger festlegen. Wenn beispielsweise ein einzelnes Stripeset 16 Datenträger enthält, geben Sie im PowerShell-Cmdlet *New-VirtualDisk* den Wert „16“ für den *NumberOfColumns*-Parameter an.

Unter Linux können Sie hierfür das Hilfsprogramm MDADM verwenden. Detaillierte Anweisungen für das Erstellen von Stripesets unter Linux finden Sie im Artikel [Konfigurieren von Software-RAID unter Linux](virtual-machines-linux-configure-raid.md).

*Stripegröße*  
Eine wichtige Konfigurationseinstellung beim Datenträgerstriping ist die Stripegröße. Die Stripe- bzw. Blockgröße ist die kleinste Datenmenge, die eine Anwendung auf einem Stripesetvolume adressieren kann. Die Stripegröße, die Sie konfigurieren, hängt von der Art der Anwendung und ihrem Anforderungsmuster ab. Bei Wahl der falschen Stripegröße ist eine falsche E/A-Abstimmung möglich, durch die sich die Leistung Ihrer Anwendung verschlechtert.

Wenn beispielsweise eine von Ihrer Anwendung generierte E/A-Anweisung größer als die Stripegröße des Datenträgers ist, wird sie vom Speichersystem über Stripe-Einheitsgrenzen hinweg auf mehrere Datenträger geschrieben. Wenn auf diese Daten zugegriffen werden soll, muss zum Erfüllen der Anforderung mehr als eine Stripe-Einheit durchsucht werden. Die kumulative Wirkung eines solchen Verhaltens kann zu beträchtlichen Leistungseinbußen führen. Wenn hingegen die Größe der E/A-Anforderung kleiner als die Stripegröße ist und diese zufälliger Art ist, können sich die E/A-Anforderungen auf demselben Datenträger anhäufen, was zu einem Engpass und schließlich zu einer Verschlechterung der E/A-Leistung führt.

Wählen Sie abhängig vom Workload Ihrer Anwendung eine geeignete Stripegröße aus. Wählen Sie für zufällige kleine E/A-Anforderungen eine kleinere Stripegröße. Wählen Sie hingegen für große sequenzielle E/A-Anforderungen eine größere Stripegröße. Ermitteln Sie Empfehlungen für die Stripegröße für die Anwendung, die Sie in Storage Premium ausführen. Für SQL Server konfigurieren Sie die Stripegröße mit 64 KB für OLTP-Workloads und 256 KB für Data Warehouse-Workloads. Weitere Informationen finden Sie unter [Optimale Verfahren für die Leistung für SQL Server in Azure Virtual Machines](virtual-machines-sql-server-performance-best-practices.md#disks-and-performance-considerations).

>**Hinweis:**  
>Bei einer VM der DS-Serie können Sie ein Stripeset mit maximal 32 Storage Premium-Datenträgern und bei einer VM der GS-Serie mit maximal 64 Storage Premium-Datenträgern bilden.

## Multithreading  
Azure hat die Storage Premium-Plattform mit einem hohen Grad an Parallelität ausgelegt. Aus diesem Grund wird mit einer Multithreadanwendung eine wesentlich höhere Leistung als mit einer Singlethreadanwendung erreicht. Eine Multithreadanwendung teilt ihre Aufgaben auf mehrere Threads auf und steigert die Effizienz ihrer Ausführung durch den maximalen Einsatz der VM- und Datenträgerressourcen.

Wenn Ihre Anwendung in einer VM mit einem Kern mithilfe zweier Threads ausgeführt wird, kann die CPU zwischen den beiden Threads umschalten, um für Effizienz zu sorgen. Während ein Thread auf den Abschluss eines Datenträger-E/A-Vorgangs wartet, kann die CPU zum anderen Thread umschalten. Auf diese Weise können zwei Threads mehr erreichen als einem einzelnem Thread möglich ist. Wenn die VM mehr als einen Kern hat, verkürzt sich die Ausführungszeit weiter, da jeder Kern Aufgaben parallel ausführt.

Sie sind ggf. nicht in der Lage, die Weise zu ändern, in der eine Standardanwendung das Singlethreading oder Multithreading implementiert. SQL Server unterstützt beispielsweise das Arbeiten mit mehreren CPUs und Kernen. SQL Server entscheidet jedoch selbst, unter welchen Umständen ein oder mehrere Threads zum Verarbeiten einer Abfrage genutzt werden. SQL Server kann mithilfe von Multithreading Abfragen ausführen und Indizes erstellen. Für eine Abfrage, für die große Tabellen verknüpft und Daten sortiert werden, ehe die Rückgabe an den Benutzer erfolgt, verwendet SQL Server wahrscheinlich mehrere Threads. Ein Benutzer kann nicht jedoch steuern, ob SQL Server eine Abfrage mithilfe eines einzelnen oder mehrerer Threads ausführt.

Es gibt Konfigurationseinstellungen, die Sie ändern können, um dieses Multithreading bzw. die Parallelverarbeitung einer Anwendung zu beeinflussen. Bei SQL Server ist dies die Einstellung „Max. Grad an Parallelität“. Diese auch MAXDOP genannte Einstellung ermöglicht Ihnen das Konfigurieren der maximalen Anzahl von Prozessoren, die SQL Server bei der Parallelverarbeitung verwenden kann. Sie können MAXDOP für einzelne Abfragen oder Indexvorgänge konfigurieren. Dies ist nützlich, wenn Sie bei einer leistungsabhängigen Anwendung die Ressourcen Ihres Systems gleichmäßig auslasten möchten.

Angenommen, Ihre mit SQL Server arbeitende Anwendung führt gleichzeitig eine umfangreiche Abfrage und einen Indexvorgang aus. Wir nehmen einmal an, dass der Indexvorgang Priorität vor der umfangreichen Abfrage haben soll. In einem solchen Fall können Sie den MAXDOP-Wert des Indexvorgangs höher als den MAXDOP-Wert der Abfrage festlegen. Auf diese Weise verfügt SQL Server über eine größere Anzahl von Prozessoren, die für den Indexvorgang genutzt werden kann, im Vergleich zur Anzahl der Prozessoren, die ausschließlich für die umfangreiche Abfrage verwendet werden können. Beachten Sie, dass Sie nicht die Anzahl der Threads steuern können, die SQL Server für jeden Vorgang verwendet. Sie können die maximale Anzahl der Prozessoren steuern, die für das Multithreading reserviert werden.

Lesen Sie die weiteren Informationen zu [Graden an Parallelität](https://technet.microsoft.com/library/ms188611.aspx) in SQL Server. Hier erfahren Sie mehr zu Einstellungen, mit denen das Multithreading in Ihrer Anwendung und deren Konfiguration zum Optimieren der Leistung beeinflusst werden können.

## Warteschlangenlänge  
Die Warteschlangenlänge ist die Anzahl ausstehender E/A-Anforderungen im System. Der Wert der Warteschlangenlänge bestimmt, wie viele E/A-Vorgänge Ihre Anwendung in die Schlange stellen kann, die von den Speicherdatenträgern verarbeitet werden sollen. Dieser Wert betrifft alle drei Anwendungsleistungsindikatoren, die wir in diesem Artikel behandelt haben: IOPS, Durchsatz und Latenz.

Warteschlangenlänge und Multithreading stehen in enger Beziehung. Der Wert der Warteschlangenlänge gibt an, wie viel Multithreading von der Anwendung erreicht werden kann. Wenn die Warteschlangenlänge hoch ist, kann die Anwendung mehr Vorgänge gleichzeitig ausführen, also mehr Multithreading unterstützen. Wenn die Warteschlangenlänge niedrig ist, obwohl die Anwendung mit mehreren Threads arbeitet, stehen nicht genügend Anforderungen für die gleichzeitige Ausführung in der Schlange.

Standardanwendungen lassen meist nicht das Ändern der Warteschlangenlänge so, denn bei einer falschen Festlegung ist der Schaden größer als der Nutzen. Anwendungen legen in der Regel den richtigen Wert für die Warteschlangenlänge fest, um sich eine optimale Leistung zu sichern. Allerdings ist es wichtig, dieses Konzept zu verstehen, damit Sie Leistungsprobleme bei Ihrer Anwendung beheben können. Sie können die Auswirkungen der Warteschlangenlänge beobachten, indem Sie in Ihrem System Benchmarktools ausführen.

Einige Programme bieten Einstellungen zum Beeinflussen der Warteschlangenlänge. Die SQL Server-Einstellung „Max. Grad an Parallelität“ (MAXDOP) wurde beispielsweise im vorherigen Abschnitt vorgestellt. MAXDOP ist eine Möglichkeit, Warteschlangenlänge und Multithreading zu beeinflussen, obwohl dadurch der SQL Server-Wert der Warteschlangenlänge nicht direkt geändert wird.

*Hohe Warteschlangenlänge*  
Bei einer hohen Warteschlangenlänge werden mehr Vorgänge auf dem Datenträger in die Schlange gestellt. Der Datenträger kennt die nächste Anforderungen in seiner Warteschlange vorzeitig. Daher kann der Datenträger Vorgänge im Voraus planen und sie in einer optimalen Sequenz verarbeiten. Da die Anwendung mehr Anforderungen zum Datenträger überträgt, kann der Datenträger mehr parallele E/A-Vorgänge verarbeiten. Schlussendlich erzielt die Anwendung dadurch mehr IOPS. Da die Anwendung mehr Anforderungen verarbeitet, erhöht sich auch der Gesamtdurchsatz der Anwendung.

In der Regel erzielt eine Anwendung einen maximalen Durchsatz bei 8-16 und mehr ausstehenden E/A-Vorgängen pro angefügtem Datenträger. Wenn die Warteschlangenlänge den Wert 1 hat, überträgt die Anwendung nicht genügend E/A-Vorgänge an das System, sodass in einem gegebenen Zeitraum weniger davon verarbeitet werden. Das bedeutet weniger Durchsatz.

Beispiel: Wenn in SQL Server der MAXDOP-Wert für eine Abfrage auf 4 festgelegt wird, weiß SQL Server, dass bis zu vier Kerne verwendet werden können, um die Abfrage auszuführen. SQL Server bestimmt den besten Wert für die Warteschlangenlänge und die Anzahl der Kerne für die Abfrageausführung.

*Optimale Warteschlangenlänge*  
Eine sehr hohe Warteschlangenlänge hat jedoch auch Nachteile. Wenn der Wert zu hoch ist, versucht die Anwendung, eine sehr hohe IOPS-Rate zu erzielen. Außer wenn die Anwendung über beständige Datenträger mit ausreichend bereitgestellten IOPS verfügt, kann sich dies negativ auf die Anwendungslatenz auswirken. Die folgende Formel zeigt die Beziehung zwischen IOPS, Latenz und Warteschlangenlänge.  
	![](media/storage-premium-storage-performance/image6.png)

Die Warteschlangenlänge darf nicht auf einen beliebig hohen Wert festgelegt werden. Wichtig ist ein optimaler Wert, der der Anwendung genügend IOPS bieten kann, ohne für mehr Latenz zu sorgen. Wenn z. B. die Anwendungslatenz 1 Millisekunde sein muss, ist die erforderliche Warteschlangenlänge zum Erzielen von 5.000 IOPS wie folgt: WL = 5000 x 0,001 = 5.

*Warteschlangenlänge für Stripesetvolume*  
Für ein Stripesetvolume muss die Warteschlangenlänge so hoch sein, dass jeder Datenträger über eine individuelle Spitzenwarteschlangenlänge verfügt. Nehmen wir als Beispiel eine Anwendung mit dem Wert 2 für die Warteschlangenlänge und mit 4 Datenträgern im Stripeset. Die beiden E/A-Anforderungen werden an zwei Datenträger gerichtet, während die restlichen inaktiv bleiben. Konfigurieren Sie deshalb die Warteschlangenlänge so, dass alle Datenträger ausgelastet werden. Folgende Formel veranschaulicht, wie Sie die Warteschlangenlänge von Stripesetvolumes bestimmen.  
	![](media/storage-premium-storage-performance/image7.png)

## Drosselung  
Azure Storage Premium stellt abhängig von den gewählten VM- und Datenträgergrößen die angegebene IOPS- und Durchsatzrate bereit. Immer wenn die Anwendung versucht, eine IOPS- oder Durchsatzrate über diese Limits der VM oder des Datenträgers hinaus zu nutzen, erfolgt eine Drosselung durch Storage Premium. Dies zeigt sich in Form von Leistungseinbußen in Ihrer Anwendung. Folgen sind höhere Latenz, weniger Durchsatz oder weniger IOPS. Wenn Storage Premium keine Drosselung vornimmt, kann Ihre Anwendung vollständig ausfallen, sobald die zur Verfügung stehenden Ressourcen überschritten werden. Um also Leistungsprobleme aufgrund der Drosselung zu vermeiden, stellen Sie Ihrer Anwendung stets ausreichend Ressourcen zur Verfügung. Berücksichtigen Sie, was zuvor in den Abschnitt „VM-Größen“ und „Datenträgergrößen“ erörtert wurde. Benchmarktests sind die beste Möglichkeit, um herauszufinden, welche Ressourcen Sie zum Hosten der Anwendung benötigen.

## Benchmarktests  
Bei Benchmarktests werden verschiedene Workloads Ihrer Anwendung simuliert und die Anwendungsleistung für jeden Workload gemessen. Unter Befolgung der in einem vorherigen Abschnitt beschriebenen Schritte haben Sie die Leistungsanforderungen der Anwendung gesammelt. Durch Ausführen von Benchmarktools in den VMs, die die Anwendung hosten, können Sie die Leistungsgrade bestimmen, die Ihre Anwendung mit Storage Premium erreichen kann. In diesem Abschnitt bieten wir Beispiele für Benchmarktests einer Standard-VM der DS14-Serie, die mit Azure Storage Premium-Datenträgern bereitgestellt wurde.

Wir haben für Windows und Linux gängige Benchmarktools wie Iometer und FIO verwendet. Diese Tools erzeugen mehrere Threads zum Simulieren eines produktionsähnlichen Workloads und Messen der Systemleistung. Mithilfe der Tools können Sie auch Parameter wie Blockgröße und Warteschlangenlänge konfigurieren, die Sie normalerweise nicht für eine Anwendung ändern können. Dadurch können Sie flexibler die Maximalleistung einer Hochleistungs-VM ermitteln, die für verschiedene Typen von Anwendungsworkloads mit Premium-Datenträgern bereitgestellt ist. Weitere Informationen zu den einzelnen Benchmarktools finden Sie unter [Iometer](http://www.iometer.org/) und [FIO](http://freecode.com/projects/fio).

Erstellen Sie zum Befolgen der nachstehenden Beispiele eine Standard-VM vom Typ DS14, an die Sie 11 Storage Premium-Datenträger anfügen. Konfigurieren Sie 10 der 11 Datenträger mit der Hostcache-Einstellung „None“, und fügen Sie sie einem Stripeset mit dem Namen „NoCacheWrites“ hinzu. Konfigurieren Sie auf dem verbleibenden Datenträger die Hostcache-Einstellung „ReadOnly“, und erstellen Sie ein Volume mit dem Namen „CacheReads“ mit diesem Datenträger. Mithilfe dieser Einrichtung können Sie die maximale Lese- und Schreibleistung einer Standard-VM vom Typ DS14 ermitteln. Ausführliche Anweisungen zum Erstellen einer Standard-VM vom Typ DS14 mit Premium-Datenträgern finden Sie unter [Erstellen und Verwenden eines Storage Premium-Kontos für den Datenträger eines virtuellen Computers](storage-premium-storage-preview-portal.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

*Auffüllen des Caches*  
Der Datenträger mit der Hostcache-Einstellung „ReadOnly“ bietet eine IOPS-Rate, die höher als das Datenträgerlimit ist. Um diese maximale Leseleistung aus dem Hostcache zu erzielen, müssen zuerst Sie den Cache dieses Datenträgers mit gültigen Daten auffüllen. Dies stellt sicher, dass die Lese-E/As, die das Benchmarktool auf dem Volume „CacheReads“ erzeugt, tatsächlich den Cache und nicht direkt den Datenträger abfragen. Die Cachetreffer führen zu weiteren IOPS auf dem einzelnen Datenträger mit aktiviertem Cache.

>**Wichtig:**  
>Immer wenn die VM neu gestartet wird, müssen Sie den Cache vor dem Ausführen von Benchmarktests auffüllen.

#### Iometer   
Laden Sie in der VM das Tool Iometer über den folgenden Link herunter: [Iometer herunterladen](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download).

*Testdatei*  
Iometer verwendet eine Testdatei, die auf dem Volume gespeichert wird, auf dem Sie den Benchmarktest ausführen. Iometer erzeugt Lese- und Schreibvorgänge für diese Testdatei, um IOPS und Durchsatz des Datenträgers zu messen. Iometer erstellt diese Datei, falls Sie keine bereitgestellt haben. Erstellen Sie die 200 GB große Testdatei „iobw.tst“ auf den Volumes „CacheReads“ und „NoCacheWrites“.

*Zugriffsspezifikationen*  
Die Angaben „request IO size“, „% read/write“ und „% random/sequential“ werden in Iometer auf der Registerkarte „Access Specifications“ konfiguriert. Erstellen Sie eine Zugriffsspezifikation für jedes der nachstehend beschriebenen Szenarien. Erstellen Sie die Zugriffsspezifikationen, und speichern Sie sie unter einem geeigneten Namen wie „RandomWrites\_8K“, „RandomReads\_8K“. Wählen Sie beim Ausführen des Testszenarios die entsprechende Spezifikation aus.

Ein Beispiel für Zugriffsspezifikationen für das Szenario „Maximale Schreib-IOPS“ wird nachstehend gezeigt:  
	![](media/storage-premium-storage-performance/image8.png)

*Testspezifikationen für maximale IOPS*  
Wählen Sie zum Demonstrieren von maximalen IOPS eine kleinere Anforderungsgröße. Wählen Sie die Anforderungsgröße 8 KB, und erstellen Sie Spezifikationen für zufällige Schreib- und Lesevorgänge.

| Zugriffsspezifikation | Anforderungsgröße | Random % | Read % |
|----------------------|--------------|----------|--------|
| RandomWrites\_8K | 8 KB | 100 | 0 |
| RandomReads\_8K | 8 KB | 100 | 100 |

*Testspezifikationen für maximalen Durchsatz*  
Wählen Sie zum Demonstrieren eines maximalen Durchsatzes eine höhere Anforderungsgröße. Verwenden Sie die Anforderungsgröße 64 KB, und erstellen Sie Spezifikationen für zufällige Schreib- und Lesevorgänge.

| Zugriffsspezifikation | Anforderungsgröße | Random % | Read % |
|----------------------|--------------|----------|--------|
| RandomWrites\_64K | 64 KB | 100 | 0 |
| RandomReads\_64K | 64 KB | 100 | 100 |

*Ausführen des Iometer-Tests*  
Führen Sie die folgenden Schritte aus, um den Cache aufzufüllen.

1.  Erstellen Sie zwei Zugriffsspezifikationen mit unten aufgeführten Werten:

	| Name | Anforderungsgröße | Random % | Read % |
	|-------------------|--------------|----------|--------|
	| RandomWrites\_1MB | 1 MB | 100 | 0 |
	| RandomReads\_1MB | 1 MB | 100 | 100 |

2.  Führen Sie den Iometer-Test zum Initialisieren des Cachedatenträgers mit folgenden Parametern aus. Verwenden Sie drei Arbeitsthreads für das Zielvolume und die Warteschlangenlänge 128. Legen Sie auf der Registerkarte „Test Setup“ unter „Run time“ die Laufzeit des Tests auf 2 Stunden fest.

	| Szenario | Zielvolume | Name | Dauer |
	|-----------------------|---------------|-------------------|----------|
	| Cachedatenträger initialisieren | CacheReads | RandomWrites\_1MB | 2 Stunden |

3.  Führen Sie den Iometer-Test zum Auffüllen des Cachedatenträgers mit folgenden Parametern aus. Verwenden Sie drei Arbeitsthreads für das Zielvolume und die Warteschlangenlänge 128. Legen Sie auf der Registerkarte „Test Setup“ unter „Run time“ die Laufzeit des Tests auf 2 Stunden fest.

	| Szenario | Zielvolume | Name | Dauer |
	|--------------------|---------------|------------------|----------|
	| Auffüllen des Cachedatenträgers | CacheReads | RandomReads\_1MB | 2 Stunden |

Nachdem der Cachedatenträger aufgefüllt wurde, fahren Sie mit den nachstehenden Testszenarien fort. Verwenden Sie zum Ausführen des Iometer-Tests mindestens drei Arbeitsthreads für **jedes** Zielvolume. Wählen Sie für jeden Arbeitsthread das Zielvolume aus, legen Sie die Warteschlangenlänge fest, und wählen Sie eine der gespeicherten Testspezifikationen, wie in der folgenden Tabelle gezeigt, um das entsprechenden Testszenario auszuführen. Die Tabelle enthält auch erwartete Ergebnisse für IOPS und Durchsatz beim Ausführen dieser Tests. Bei allen Szenarien wird eine kleine E/A-Größe von 8 KB und die hohe Warteschlangenlänge 128 verwendet.

| Testszenario | Zielvolume | Name | Ergebnis |
|--------------------|---------------|-------------------|--------------|
| Max. Lese-IOPS | CacheReads | RandomWrites\_8K | 50\.000 IOPS |
| Max. Schreib-IOPS | NoCacheWrites | RandomReads\_8K | 64\.000 IOPS |
| Max. Kombinierte IOPS | CacheReads | RandomWrites\_8K | 100\.000 IOPS |
| | NoCacheWrites | RandomReads\_8K | |
| Max. Lesen – MB/s | CacheReads | RandomWrites\_64K | 524 MB/s |
| Max. Schreiben – MB/s | NoCacheWrites | RandomReads\_64K | 524 MB/s |
| Kombiniert – MB/s | CacheReads | RandomWrites\_64K | 1000 MB/s |
| | NoCacheWrites | RandomReads\_64K | |

Nachstehend sehen Sie Screenshots der Iometer-Testergebnisse für kombinierte IOPS- und Durchsatz-Szenarien

*Kombiniert Lese- und Schreibvorgänge – Maximale IOPS*  
![](media/storage-premium-storage-performance/image9.png)

*Kombiniert Lese- und Schreibvorgänge – Maximaler Durchsatz*  
![](media/storage-premium-storage-performance/image10.png)

### FIO  
FIO ist ein beliebtes Tool für Benchmarktests des Speichers von Linux-VMs. Es ermöglicht die flexible Auswahl unterschiedlicher E/A-Größen sowie sequenzieller oder zufälliger Lese- und Schreibvorgänge. FIO erzeugt Arbeitsthreads oder Prozesse zum Ausführen der angegebenen E/A-Vorgänge. Mithilfe von Auftragsdateien können Sie den Typ der E/A-Vorgänge angeben, den jeder Arbeitsthread ausführen soll. Wir haben eine Auftragsdatei pro Szenario erstellt, was in den folgenden Beispielen veranschaulicht wird. Sie können die Spezifikationen in diesen Auftragsdateien ändern, um Benchmarktests für verschiedene Workloads in Storage Premium auszuführen. In den Beispielen verwenden wir eine Standard-VM vom Typ DS 14 unter**Ubuntu**. Verwenden Sie dieselbe am Anfang des Abschnitts „Benchmarktests“ beschriebene Einrichtung, und füllen Sie den Cache vor dem Ausführen der Benchmarktests auf.

Installieren Sie FIO auf der virtuellen Maschine, bevor Sie beginnen. Laden Sie FIO von [GitHub](https://github.com/axboe/fio) herunter.

Führen Sie den folgenden Befehl für Ubuntu aus:

		apt-get install fio

Wir verwenden je vier Arbeitsthreads zum Erzeugen von Schreib- und Lesevorgängen auf den Datenträgern. Die Arbeitsthreads für Schreibvorgänge erzeugen Datenverkehr auf dem Volume „NoCache“, das 10 Datenträger mit der Cacheeinstellung „None“ aufweist. Die Arbeitsthreads für Lesevorgänge erzeugen Datenverkehr auf dem Volume „ReadCache“, das einen Datenträger mit der Cacheeinstellung „ReadOnly“ aufweist.

*Maximale Schreib-IOPS*  
Erstellen Sie die Auftragsdatei mit den folgenden Spezifikationen, um die maximale Schreib-IOPS zu erhalten. Nennen Sie die Datei „fiowrite.ini“.

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Beachten Sie, dass die folgenden wichtigen Aspekte in Einklang mit den in den vorherigen Abschnitten erörterten Entwurfsrichtlinien sind. Diese Spezifikationen sind zum Erzielen der maximalen IOPS wichtig:  
-   Eine hohe Warteschlangenlänge von 256  
-   Eine kleine Blockgröße von 8 KB  
-   Mehrere Threads, die sequentielle Lesevorgänge ausführen

Führen Sie den folgenden Befehl aus, um einen 30-sekündigen FIO-Test zu starten:
				
	sudo fio --runtime 30 fiowrite.ini

Während der Testausführung können Sie die Anzahl der Schreib-IOPS erkennen, die die VM und Premium-Datenträger zu bieten haben. Wie im folgenden Beispiel gezeigt, schöpft die DS14-VM ihre maximale IOPS-Schreibkapazität von 50.000 IOPS aus.  
	![](media/storage-premium-storage-performance/image11.png)

*Maximale Lese-IOPS*  
Erstellen Sie die Auftragsdatei mit den folgenden Spezifikationen, um die maximale Lese-IOPS zu erhalten. Nennen Sie die Datei „fioread.ini“.

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Beachten Sie, dass die folgenden wichtigen Aspekte in Einklang mit den in den vorherigen Abschnitten erörterten Entwurfsrichtlinien sind. Diese Spezifikationen sind zum Erzielen der maximalen IOPS wichtig:

-   Eine hohe Warteschlangenlänge von 256  
-   Eine kleine Blockgröße von 8 KB  
-   Mehrere Threads, die sequentielle Lesevorgänge ausführen

Führen Sie den folgenden Befehl aus, um einen 30-sekündigen FIO-Test zu starten:

	sudo fio --runtime 30 fioread.ini

Während der Testausführung können Sie die Anzahl der Lese-IOPS erkennen, die die VM und Premium-Datenträger zu bieten haben. Wie im folgenden Beispiel gezeigt, schöpft die DS14-VM ihre maximale IOPS-Lesekapazität von 64.000 IOPS aus. Dies ist eine Kombination aus Datenträger- und Cacheleistung.  
	![](media/storage-premium-storage-performance/image12.png)

*Maximale Lese- und Schreib-IOPS*  
Erstellen Sie die Auftragsdatei mit den folgenden Spezifikationen, um die maximale kombinierte Lese- und Schreib-IOPS zu erhalten. Nennen Sie die Datei „fioreadwrite.ini“.

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Beachten Sie, dass die folgenden wichtigen Aspekte in Einklang mit den in den vorherigen Abschnitten erörterten Entwurfsrichtlinien sind. Diese Spezifikationen sind zum Erzielen der maximalen IOPS wichtig:

-   Eine hohe Warteschlangenlänge von 128  
-   Eine kleine Blockgröße von 4 KB  
-   Mehrere Threads, die sequenzielle Lese- und Schreibvorgänge ausführen

Führen Sie den folgenden Befehl aus, um einen 30-sekündigen FIO-Test zu starten:

	sudo fio --runtime 30 fioreadwrite.ini

Während der Testausführung können Sie die Anzahl der kombinierten Lese- und Schreib-IOPS erkennen, die die VM und Premium-Datenträger zu bieten haben. Wie im folgenden Beispiel gezeigt, bietet die DS14-VM kombinierte Lese- und Schreib-IOPS von über 100.000. Dies ist eine Kombination aus Datenträger- und Cacheleistung.  
	![](media/storage-premium-storage-performance/image13.png)

*Maximierter kombinierter Durchsatz*  
Um den maximalen kombinierten Lese- und Schreibdurchsatz zu erhalten, wählen Sie eine höhere Blockgröße und Warteschlangenlänge mit mehreren Threads, die Lese- und Schreibvorgänge ausführen. Sie können eine Blockgröße von 64 KB und Warteschlangenlänge von 128 verwenden.

## Nächste Schritte  

Weitere Informationen zu Azure Storage Premium:

- [Premium-Speicher: Hochleistungsspeicher für Workloads in Azure Virtual Machine](storage-premium-storage-preview-portal.md)  

Für SQL Server-Benutzer bietet sich das Lesen von Artikeln zu den bewährten Methoden für die Leistung von SQL Server an:

- [Bewährte Methoden für die Leistung von SQL Server in
- Azure Virtual Machines](https://msdn.microsoft.com/library/azure/dn133149.aspx)
- [Azure Storage Premium bietet höchste Leistung für SQL Server in Azure-VM](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)  

<!---HONumber=AcomDC_0114_2016-->