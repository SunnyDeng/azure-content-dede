<properties title="Checkliste zu Leistung und Skalierbarkeit von Microsoft Azure Storage" pageTitle="Checkliste zu Leistung und Skalierbarkeit von Microsoft Azure Storage" description="Erforderlich" metaKeywords="Optional" services="Optional" solutions="Optional" documentationCenter="Optional" authors="Tamra Myers" videoId="Optional" scriptId="Optional" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/08/2014" ms.author="tamram" />

# Checkliste zu Leistung und Skalierbarkeit von Microsoft Azure Storage

## Einführung

Seit der Veröffentlichung des Microsoft Azure-Speicherdiensts hat Microsoft eine Reihe bewährter Vorgehensweisen zur effektiven Verwendung dieser Dienste entwickelt. In diesem Artikel werden die wichtigsten davon in einer „Checkliste“ zusammengefasst. Dieser Artikel soll Anwendungsentwickler bei der Verwendung bewährter Vorgehensweisen für Azure Storage unterstützen und ihnen helfen, weitere bewährte Vorgehensweisen kennen zu lernen, die sie in Erwägung ziehen sollten. Es wird jedoch nicht jede mögliche Leistungs- und Skalierbarkeitsoptimierung in diesem Artikel abgedeckt; begrenzt anwendbare Methoden oder solche mit geringer Auswirkung sind ausgeschlossen. Insofern sich das Anwendungsverhalten während des Entwurfs vorhersagen lässt, sollten diese Vorgehensweisen von Anfang an berücksichtigt werden, um spätere Leistungsprobleme zu vermeiden.

Jeder Anwendungsentwickler, der Azure Storage verwendet, sollte sich Zeit für diesen Artikel nehmen und überprüfen, ob seine Anwendung den unten aufgeführten bewährten Vorgehensweisen entspricht.

## Checkliste

Dieser Artikel unterteilt die bewährten Vorgehensweisen in Gruppen, die wie folgt anwendbar sind:

-   Alle Azure-Speicherdienste (Blobs, Tabellen, Warteschlangen und Dateien)
-   Blobs
-   Tabellen
-   Warteschlangen

Der Azure-Dateidienst befindet sich derzeit in der Vorschauphase; die bewährten Vorgehensweisen werden künftig hier hinzugefügt.

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Vorgehensweise</th>
<th align="left">Bereich</th>
<th align="left">Kategorie</th>
<th align="left">Frage</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">Skalierbarkeitsziele</td>
<td align="left"><a href="#subheading1">Ist Ihre Anwendung so konzipiert, dass die Skalierbarkeitsziele nicht überschritten werden?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">Netzwerk</td>
<td align="left"><a href="#subheading2">Haben die clientseitigen Geräte genügend Bandbreite und niedrige Latenz, um die erforderliche Leistung zu erzielen?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">Netzwerk</td>
<td align="left"><a href="#subheading3">Haben die clientseitigen Geräte eine ausreichend hohe Verbindungsqualität?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">Netzwerk</td>
<td align="left"><a href="#subheading4">Befindet sich die Clientanwendung in der Nähe des Speicherkontos?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">Inhaltsverteilung</td>
<td align="left"><a href="#subheadin5">Verwenden Sie ein CDN für die Inhaltsverteilung?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">Direkter Clientzugriff</td>
<td align="left"><a href="#subheading6">Verwenden Sie SAS und CORS statt eines Proxys, um direkten Zugriff auf den Speicher zu erlauben?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">Cachedienst</td>
<td align="left"><a href="#subheading7">Speichert Ihre Anwendung Daten im Cache, die häufig verwendet werden und sich selten ändern?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">Cachedienst</td>
<td align="left"><a href="#subheading8">Führt Ihre Anwendung Batchaktualisierungen durch (durch clientseitige Zwischenspeicherung und anschließendes Hochladen in größeren Mengen)?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">.NET-Konfiguration</td>
<td align="left"><a href="#subheading9">Haben Sie Ihren Client zur Verwendung einer ausreichenden Anzahl gleichzeitiger Verbindungen konfiguriert?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">.NET-Konfiguration</td>
<td align="left"><a href="#subheading10">Haben Sie .NET zur Verwendung einer ausreichenden Anzahl von Threads konfiguriert?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">.NET-Konfiguration</td>
<td align="left"><a href="#subheading11">Verwenden Sie .NET 4.5 oder höher mit verbesserter Garbage Collection?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">Parallelität</td>
<td align="left"><a href="#subheading12">Haben Sie sichergestellt, dass die Parallelität entsprechend gebunden ist, sodass weder die Clientkapazitäten noch die Skalierbarkeitsziele überlastet werden?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">Tools</td>
<td align="left"><a href="#subheading13">Verwenden Sie die aktuellste Version der von Microsoft bereitgestellten Clientbibliotheken und Tools?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">Wiederholungsversuche</td>
<td align="left"><a href="#subheading14">Verwenden Sie exponentiell ansteigende Wartezeiten für Wiederholungsversuche, um Fehler und Zeitüberschreitungen zu vermeiden?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Alle Dienste</td>
<td align="left">Wiederholungsversuche</td>
<td align="left"><a href="#subheading15">Vermeidet Ihre Anwendung Wiederholungsversuche für nicht wiederholbare Fehler?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Skalierbarkeitsziele</td>
<td align="left"><a href="#subheading16">Bleibt Ihre Anwendung innerhalb der Bandbreiten- oder Vorgangs-Skalierbarkeitsziele für einzelne Blobs?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Kopieren von Blobs</td>
<td align="left"><a href="#subheading17">Kopieren Sie Blobs effizient?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Kopieren von Blobs</td>
<td align="left"><a href="#subheading18">Verwenden Sie AzCopy für Massenkopien von Blobs?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Kopieren von Blobs</td>
<td align="left"><a href="#subheading19">Verwenden Sie Azure Import/Export zum Übertragen von großen Datenmengen?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Verwenden von Metadaten</td>
<td align="left"><a href="#subheading20">Speichern Sie häufig verwendete Metadaten für Blobs in ihren Metadaten?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Schnelles Hochladen</td>
<td align="left"><a href="#subheading21">Wenn Sie versuchen, einen Blob schnell hochzuladen, laden Sie dann Blöcke parallel hoch?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Schnelles Hochladen</td>
<td align="left"><a href="#subheading22">Wenn Sie versuchen, mehrere Blobs schnell hochzuladen, laden Sie dann Blobs parallel hoch?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Richtiger Blobtyp</td>
<td align="left"><a href="#subheading23">Verwenden Sie jeweils Seiten-Blobs oder Block-Blobs?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Skalierbarkeitsziele</td>
<td align="left"><a href="#subheading24">Nähern Sie sich den Skalierbarkeitszielen für Entitäten pro Sekunde?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Konfiguration</td>
<td align="left"><a href="#subheading25">Verwenden Sie JSON für Ihre Tabellenanforderungen?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Konfiguration</td>
<td align="left"><a href="#subheading26">Haben Sie Nagle deaktiviert, um die Leistung kleiner Anfragen zu verbessern?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Tabellen und Partitionen</td>
<td align="left"><a href="#subheading27">Haben Sie Ihre Daten richtig partitioniert?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Hot Partition</td>
<td align="left"><a href="#subheading28">Vermeiden Sie Nur-Anhängen/Nur-Voranstellen-Muster?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Hot Partition</td>
<td align="left"><a href="#subheading29">Verteilen sich Ihre Einfügungen/Aktualisierungen auf mehrere Partitionen?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Abfragebereich</td>
<td align="left"><a href="#subheading30">Haben Sie Ihr Schema so konzipiert, dass in den meisten Fällen Punktabfragen verwendet werden können und Tabellenabfragen nur sparsam erfolgen?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Abfragedichte</td>
<td align="left"><a href="#subheading31">Erfassen Ihre Abfragen normalerweise nur Zeilen für die Rückgabe, die Ihre Anwendung auch verwenden wird?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Einschränken der zurückgegebenen Daten</td>
<td align="left"><a href="#subheading32">Verwenden Sie Filterung, um die Rückgabe nicht benötigter Entitäten zu vermeiden?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Einschränken der zurückgegebenen Daten</td>
<td align="left"><a href="#subheading33">Verwenden Sie Projektion, um die Rückgabe nicht benötigter Eigenschaften zu vermeiden?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Denormalisierung</td>
<td align="left"><a href="#subheading34">Haben Sie Ihre Daten so normalisiert, dass Sie ineffiziente Abfragen oder mehrere Leseanforderungen beim Abrufen von Daten vermeiden?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Einfügen/Aktualisieren/Löschen</td>
<td align="left"><a href="#subheading35">Verwenden Sie Batchverarbeitung für Anforderungen, die transaktional oder gleichzeitig erfolgen, um Roundtrips zu reduzieren?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Einfügen/Aktualisieren/Löschen</td>
<td align="left"><a href="#subheading36">Vermeiden Sie das Abrufen von Entitäten, nur um zu bestimmen, ob Einfügen oder Aktualisieren erforderlich ist?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Einfügen/Aktualisieren/Löschen</td>
<td align="left"><a href="#subheading37">Haben Sie überlegt, Datenreihen, die häufig zusammen abgerufen werden, in einer einzigen Entität als Eigenschaften zu speichern statt in mehreren Entitäten?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabellen</td>
<td align="left">Einfügen/Aktualisieren/Löschen</td>
<td align="left"><a href="#subheading38">Haben Sie überlegt, für Entitäten, die immer zusammen abgerufen werden und in Batches geschrieben werden können (z. B. Zeitreihendaten), Blobs statt Tabellen zu verwenden?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Warteschlangen</td>
<td align="left">Skalierbarkeitsziele</td>
<td align="left"><a href="#subheading39">Nähern Sie sich den Skalierbarkeitszielen für Nachrichten pro Sekunde?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Warteschlangen</td>
<td align="left">Konfiguration</td>
<td align="left"><a href="#subheading40">Haben Sie Nagle deaktiviert, um die Leistung kleiner Anfragen zu verbessern?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Warteschlangen</td>
<td align="left">Nachrichtengröße</td>
<td align="left"><a href="#subheading41">Sind Ihre Nachrichten kompakt, um die Leistung der Warteschlange zu verbessern?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Warteschlangen</td>
<td align="left">Massenabruf</td>
<td align="left"><a href="#subheading41">Rufen Sie mehrere Nachrichten mit einem einzigen „Get“-Vorgang ab?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Warteschlangen</td>
<td align="left">Abrufhäufigkeit</td>
<td align="left"><a href="#subheading42">Rufen Sie häufig genug ab, um die gefühlte Latenz der Anwendung zu reduzieren?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Warteschlangen</td>
<td align="left">Aktualisieren von Nachrichten</td>
<td align="left"><a href="#subheading43">Verwenden Sie UpdateMessage, um den Fortschritt bei der Nachrichtenverarbeitung zu speichern und so nicht die gesamten Nachrichten erneut verarbeiten zu müssen, falls ein Fehler auftritt?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Warteschlangen</td>
<td align="left">Architektur</td>
<td align="left"><a href="#subheading44">Verwenden Sie Warteschlangen, um die gesamte Anwendung besser skalierbar zu machen, indem Sie Arbeitsauslastungen mit langer Laufzeit aus dem kritischen Pfad heraushalten und unabhängig skalieren?</a></td>
</tr>
</tbody>
</table>

## <a name="allservices"></a>Alle Dienste

In diesem Abschnitt sind bewährte Vorgehensweisen für die Verwendung von allen Azure-Speicherdiensten (Blobs, Tabellen, Warteschlangen und Dateien) aufgelistet.

### <a name="subheading1"></a>Skalierbarkeitsziele

Jeder Azure-Speicherdienst hat Skalierbarkeitsziele für Kapazität (GB), Transaktionsrate und Bandbreite. Wenn Ihre Anwendung eines der Skalierbarkeitsziele erreicht oder überschreitet, kann es zu erhöhter Transaktionslatenz oder Drosselung kommen. Sobald ein Speicherdienst Ihre Anwendung drosselt, gibt der Dienst den Fehlercode „503 Server Busy“ oder „500 Operation Timeout“ für manche Speichertransaktionen zurück. In diesem Abschnitt werden die allgemeine Vorgehensweise zur Handhabung von Skalierbarkeitszielen sowie speziell die Bandbreiten-Skalierbarkeitsziele behandelt. In späteren Abschnitten zu den einzelnen Speicherdiensten werden die Skalierbarkeitsziele im jeweiligen Dienstkontext erläutert:

-   [Blob-Bandbreite und Anfragen pro Sekunde][Bleibt Ihre Anwendung innerhalb der Bandbreiten- oder Vorgangs-Skalierbarkeitsziele für einzelne Blobs?]
-   [Tabellenentitäten pro Sekunde][Nähern Sie sich den Skalierbarkeitszielen für Entitäten pro Sekunde?]
-   [Warteschlangennachrichten pro Sekunde][Nähern Sie sich den Skalierbarkeitszielen für Nachrichten pro Sekunde?]

#### <a name="sub1bandwidth"></a>Bandbreiten-Skalierbarkeitsziele für alle Dienste

Zum Redaktionszeitpunkt betrugen die Bandbreitenziele in den USA für georedundante Speicherkonten (GRS) 10 Gigabit pro Sekunde (GBit/s) für eingehende Daten (an das Speicherkonto gesendete Daten) und 20 Gigabit pro Sekunde (GBit/s) für ausgehende Daten (vom Speicherkonto aus gesendete Daten). Für lokale redundante Speicherkonten (LRS) liegen die Grenzen höher – 20 GBit/s für eingehende und 30 GBit/s für ausgehende Daten. Internationale Bandbreitengrenzen können niedriger sein. Informationen dazu finden Sie auf der Seite [Skalierbarkeitsziele][Skalierbarkeitsziele]. Weitere Informationen zu den Speicherredundanzoptionen finden Sie unter den Links zu [Nützliche Ressourcen][Nützliche Ressourcen] weiter unten.

#### Vorgehensweise bei Annäherung an das Skalierbarkeitsziel

Wenn sich Ihre Anwendung den Skalierbarkeitszielen für ein Speicherkonto nähert, sollten Sie eine der folgenden Vorgehensweisen wählen:

-   Berücksichtigen Sie die Arbeitsauslastung, aufgrund derer Ihre Anwendung das Skalierbarkeitsziel erreicht oder überschreitet. Können Sie diese anders konzipieren, um weniger Bandbreite bzw. Kapazität oder weniger Transaktionen zu verwenden?
-   Wenn eine Anwendung eines der Skalierbarkeitsziele überschreiten muss, sollten Sie mehrere Speicherkonten erstellen und die Anwendungsdaten auf mehrere Speicherkonten aufteilen. Konzipieren Sie in diesem Fall die Anwendung so, dass Sie künftig weitere Speicherkonten für den Lastenausgleich hinzufügen können. Zum Redaktionszeitpunkt konnte jedes Azure-Abonnement über bis zu 100 Speicherkonten verfügen. Speicherkonten verursachen keine Kosten außer die Nutzung in Bezug auf gespeicherte Daten, durchgeführte Transaktionen und übertragene Daten.
-   Wenn Ihre Anwendung das Bandbreitenziel erreicht, versuchen Sie, die Daten im Client zu komprimieren, um die erforderliche Bandbreite zum Senden der Daten an den Speicherdienst zu reduzieren. Obwohl dies Bandbreite spart und die Netzwerkleistung erhöhen kann, kann es auch negative Auswirkungen haben. Sie sollten die Leistungsauswirkung in Bezug auf die zusätzliche Verarbeitung beim Komprimieren und Dekomprimieren der Daten im Client beobachten. Außerdem kann das Speichern komprimierter Daten die Fehlerbehebung erschweren, da es schwieriger sein kann, gespeicherte Daten mithilfe von Standardtools anzuzeigen.
-   Wenn Ihre Anwendung die Skalierbarkeitsziele erreicht, stellen Sie sicher, dass Sie exponentiell ansteigende Wartezeiten für Wiederholungsversuche verwenden (siehe [Wiederholungsversuche][Verwenden Sie exponentiell ansteigende Wartezeiten für Wiederholungsversuche, um Fehler und Zeitüberschreitungen zu vermeiden?]). Sie sollten besser sicherstellen, dass Sie sich nicht an die Skalierbarkeitsziele annähern (mit einer der oben angegebenen Methoden), aber dies sorgt ebenfalls dafür, dass die Anwendung keine schnellen Wiederholungen versucht, sodass die Drosselung schlimmer wird.

#### Nützliche Ressourcen

Die folgenden Links enthalten zusätzliche Details zu den Skalierbarkeitszielen:

-   Sie können die aktuellen Skalierbarkeitsziele auf der Seite [Ziele für Skalierbarkeit und Leistung des Azure-Speichers][Skalierbarkeitsziele] auf MSDN anzeigen.
-   Weitere Informationen zu Speicherredundanzoptionen erhalten Sie im Blogeintrag [Windows Azure-Speicher: Redundanzoptionen und Lesezugriff georedundanter Speicher][Windows Azure-Speicher: Redundanzoptionen und Lesezugriff georedundanter Speicher]. Weitere Details hierzu finden Sie auch im nächsten Abschnitt.
-   Aktuelle Informationen zu den Preisdetails für Azure-Dienste finden Sie auf der Seite [Azure-Preise auf einen Blick][Azure-Preise auf einen Blick].

### Netzwerk

Auch wenn die API-Aufrufe eine Rolle spielen, haben häufig die physischen Netzwerkeinschränkungen deutliche Auswirkungen auf die Leistung. Nachfolgend sind einige der Einschränkungen beschrieben, auf die Benutzer treffen können.

#### Client-Netzwerkkapazität

##### <a name="subheading2"></a>Durchsatz

Bei der Bandbreite liegt das Problem häufig in der Clientkapazität. Während beispielsweise ein Speicherkonto 10 GBit/s oder mehr an eingehenden Daten verarbeiten kann (siehe [Bandbreiten-Skalierbarkeitsziele][Bandbreiten-Skalierbarkeitsziele]), beträgt die Netzwerkgeschwindigkeit in einer „kleinen“ Azure-Workerrolleninstanz maximal 100 MBit/s. Größere Azure-Instanzen verfügen über NICs mit höherer Kapazität, daher sollten Sie erwägen, eine größere Instanz oder mehr VMs zu verwenden, wenn Sie höhere Netzwerkbegrenzungen auf einem einzelnen Computer benötigen. Wenn Sie von einer lokalen Anwendung aus auf einen Speicherdienst zugreifen, gilt dieselbe Regel: Informieren Sie sich über die Netzwerkkapazitäten des Clientgeräts und die Netzwerkverbindung zum Azure-Speicherstandort, und optimieren Sie diese entweder oder entwerfen Sie die Anwendung so, dass sie diesen Kapazitäten entspricht.

##### <a name="subheading3"></a>Verbindungsqualität

Seien Sie sich wie bei jeder Netzwerknutzung bewusst, dass Netzwerkbedingungen, die zu Fehlern und Paketverlusten führen, den effektiven Durchsatz verlangsamen. Die Verwendung von WireShark oder NetMon kann bei der Diagnose dieses Problems helfen.

##### Nützliche Ressourcen

Informationen zur Größe virtueller Computer und zur zugewiesenen Bandbreite finden Sie unter [Bewährte Vorgehensweisen für den Entwurf umfangreicher Dienste auf Azure Cloud Services][Bewährte Vorgehensweisen für den Entwurf umfangreicher Dienste auf Azure Cloud Services] auf MSDN.

#### <a name="subheading4"></a>Speicherort

In jeder verteilten Umgebung wird die beste Leistung erzielt, indem der Client in der Nähe des Servers platziert wird. Zum Zugriff auf den Azure-Speicher mit der niedrigsten Latenz befindet sich der beste Standort für den Client innerhalb derselben Azure-Region. Wenn Sie beispielsweise eine Azure-Website haben, die den Azure-Speicher verwendet, sollten Sie beide innerhalb derselben Region platzieren (z. B. Westeuropa oder Südostasien). Dadurch werden Latenz und Kosten verringert – zum Redaktionszeitpunkt war die Bandbreitennutzung innerhalb einer Region kostenlos.

Wenn Ihre Clientanwendungen nicht in Azure gehostet werden (beispielsweise Apps für Mobilgeräte oder lokale Enterprise Services), wird die Latenz ebenfalls durch Platzierung des Speicherkontos in einer Region in der Nähe der Geräte, die darauf zugreifen, reduziert. Falls Ihre Clients weit verteilt sind (z. B. einige in Nordamerika und andere in Europa), sollten Sie die Verwendung mehrerer Speicherkonten in Betracht ziehen: eines in der Region Nordamerika und eines in der Region Europa. Dadurch wird die Latenz für Benutzer in beiden Regionen reduziert. Diese Vorgehensweise ist meist einfacher zu implementieren, wenn die in der Anwendung gespeicherten Daten speziell für bestimmte Benutzer gelten und keine Datenreplikation zwischen den Speicherkonten erforderlich ist. Für eine verbreitete Inhaltsverteilung wird ein CDN empfohlen. Weitere Details dazu finden Sie im nächsten Abschnitt.

### <a name="subheading5"></a>Inhaltsverteilung

Manchmal muss eine Anwendung denselben Inhalt für mehrere Benutzer bereitstellen (z. B. ein Produkt-Demovideo auf der Startseite einer Website), die sich entweder in derselben oder in verschiedenen Regionen befinden. In diesem Fall sollten Sie ein Content Delivery Network (CDN) wie das Azure CDN verwenden, und das CDN nutzt den Azure-Speicher als Datenursprung. Im Gegensatz zum Azure-Speicherkonto, das in genau einer Region vorhanden ist und Inhalte nicht mit niedriger Latenz an andere Regionen liefern kann, verwendet das Azure CDN Server in mehreren Rechenzentren weltweit. Darüber hinaus unterstützt ein CDN in der Regel höhere Ausgangsgrenzen als ein einzelnes Speicherkonto.

Weitere Informationen zum Azure CDN finden Sie unter [Azure CDN][Azure CDN].

### <a name="subheading6"></a>Verwenden von SAS und CORS

Wenn Sie Code JavaScript im Webbrowser eines Benutzers oder eine mobile App zum Zugriff auf Daten im Azure-Speicher autorisieren müssen, besteht eine Möglichkeit in der Verwendung einer Anwendung in der Webrolle als Proxy: das Gerät des Benutzers authentifiziert sich bei der Webrolle, die sich wiederum beim Speicherdienst authentifiziert. Auf diese Weise müssen Sie den Speicherkontoschlüssel nicht gegenüber unsicheren Geräten offenbaren. Dies sorgt jedoch für eine deutliche Belastung der Webrolle, da alle zwischen dem Benutzergerät und dem Speicherdienst übertragenen Daten über die Webrolle weitergeleitet werden. Sie können die Verwendung einer Webrolle als Proxy für den Speicherdienst durch die Verwendung von Shared Access Signatures (SAS) vermeiden, manchmal im Zusammenhang mit Cross-Origin Resource Sharing-Headers (CORS). Durch SAS kann das Benutzergerät Anfragen mithilfe eines beschränkten Zugriffstokens direkt an den Speicherdienst stellen. Wenn beispielsweise ein Benutzer ein Foto in die Anwendung hochladen möchte, kann Ihre Webrolle ein SAS-Token generieren und an das Benutzergerät senden, um für 30 Minuten Schreibzugriff auf einen bestimmten Blob oder Container zu gewähren (danach läuft das SAS-Token ab).

Normalerweise lässt der Browser kein JavaScript auf einer Seite zu, die von einer Website auf einer Domäne gehostet wird, um bestimmte Vorgänge wie „PUT“ auf einer anderen Domäne auszuführen. Wenn Sie beispielsweise eine Webrolle unter „contosomarketing.cloudapp.net“ hosten und clientseitiges JavaScript verwenden möchten, um einen Blob in das Speicherkonto unter „contosoproducts.blob.core.windows.net“ hochzuladen, verhindert die Ursprungsrichtlinie des Browsers diesen Vorgang. CORS ist eine Browserfunktion, mit deren Hilfe die Zieldomäne (in diesem Fall das Speicherkonto) dem Browser mitteilen kann, dass sie Anfragen aus der Quelldomäne (in diesem Fall von der Webrolle) vertraut.

Beide dieser Technologien können dabei helfen, unnötige Lasten (und Engpässe) in der Webanwendung zu vermeiden.

#### Nützliche Ressourcen

Weitere Informationen zu SAS finden Sie unter [Shared Access Signatures, Teil 1: Grundlagen zum SAS-Modell][Shared Access Signatures, Teil 1: Grundlagen zum SAS-Modell].

Weitere Informationen zu CORS finden Sie unter [Cross-Origin Resource Sharing (CORS)-Support für den Azure-Speicherdienst][Cross-Origin Resource Sharing (CORS)-Support für den Azure-Speicherdienst] auf MSDN.

### Cachedienst

#### <a name="subheading7"></a>Abrufen von Daten

In der Regel sollte das Abrufen von Daten von einem Dienst nur einmal erfolgen, nicht zweimal. Denken Sie an das Beispiel einer MVC-Webanwendung, die in einer Webrolle ausgeführt wird, die bereits einen Blob von 50 MB aus dem Speicherdienst abgerufen hat, der als Inhalt für einen Benutzer bereitgestellt wird. Die Anwendung kann anschließend entweder denselben Blob bei jeder Benutzeranforderung erneut abrufen oder ihn lokal auf der Festplatte zwischenspeichern und die zwischengespeicherte Version für nachfolgende Benutzeranforderungen erneut verwenden. Außerdem könnte die Anwendung immer, wenn ein Benutzer die Daten anfordert, eine GET-Anforderung mit einem Bedingungsheader für die Änderungszeit ausgeben, um nicht jedes Mal den gesamten Blob abzurufen, wenn er nicht geändert wurde. Dies funktioniert auch für Tabellenentitäten.

In einigen Fällen können Sie festlegen, dass die Anwendung davon ausgehen kann, dass der Blob nach dem Abrufen für eine bestimmte Zeit gültig bleibt und dass die Anwendung während dieses Zeitraums nicht prüfen muss, ob der Blob geändert wurde.

Konfigurations-, Such- und andere Daten, die immer von der Anwendung verwendet werden, sind hervorragend für die Zwischenspeicherung geeignet.

Ein Beispiel für das Abrufen der Blobeigenschaften mithilfe von .NET, um das letzte Änderungsdatum zu ermitteln, finden Sie unter [Festlegen und Abrufen von Eigenschaften und Metadaten][Festlegen und Abrufen von Eigenschaften und Metadaten] auf MSDN. Informationen zu bedingten Downloads finden Sie unter [Angeben von bedingten Headern für Vorgänge des Blob-Diensts][Angeben von bedingten Headern für Vorgänge des Blob-Diensts] auf MSDN.

#### <a name="subheading8"></a>Stapelweises Hochladen von Daten

In einigen Anwendungsszenarien können Sie Daten lokal aggregieren und dann regelmäßig als Batch hochladen, statt die einzelnen Daten sofort hochzuladen. Angenommen, eine Webanwendung speichert eine Protokolldatei der Aktivitäten: die Anwendung kann entweder die Details jeder Aktivität sofort als Tabellenentität hochladen (was viele Speichervorgänge erforderlich macht) oder die Aktivitätsdetails in einer lokalen Protokolldatei speichern und dann regelmäßig alle Aktivitätsdetails als Datei mit Trennzeichen in den Blob hochladen. Wenn jeder Protokolleintrag eine Größe von 1 KB hat, können Sie Tausende davon in einer einzigen „Put Blob“-Transaktion übertragen (Sie können bis zu 64 MB in einer einzigen Blob-Transaktion hochladen). Wenn der Rechner allerdings vor dem Hochladen abstürzt, können eventuell einige Protokolldaten verloren gehen; der Anwendungsentwickler muss die Möglichkeit von Fehlern auf dem Clientgerät oder beim Hochladen berücksichtigen. Wenn die Aktivitätsdaten für bestimmte Zeitspannen heruntergeladen werden sollen (nicht nur für eine Aktivität), dann sind Blobs gegenüber Tabellen vorzuziehen.

### .NET-Konfiguration

Für die Verwendung des .NET Frameworks sind in diesem Abschnitt einige Schnellkonfigurationseinstellungen aufgelistet, die eine deutliche Leistungsoptimierung bewirken. Bei Verwendung anderer Sprachen sollten Sie sich informieren, ob es ähnliche Konzepte für die jeweilige Sprache gibt.

#### <a name="subheading9"></a>Erhöhen des Standardverbindungslimits

In .NET erhöht der folgende Code das Standardverbindungslimit (das normalerweise 2 in einer Clientumgebung oder 10 in einer Serverumgebung beträgt) auf 100. In der Regel sollten Sie den Wert auf die ungefähre Anzahl der Threads, die von der Anwendung verwendet werden, setzen.

    ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  

Sie müssen das Verbindungslimit festlegen, bevor Sie die Verbindungen öffnen.

Für andere Programmiersprachen erfahren Sie das Verbindungslimit aus der zugehörigen Dokumentation.

Weitere Informationen finden Sie im Eintrag [Webdienste: Gleichzeitige Verbindungen][Webdienste: Gleichzeitige Verbindungen] auf MSDN.

#### <a name="subheading10"></a>Erhöhen der Mindestanzahl von Threadpool-Threads bei Verwendung von synchronem Code mit asynchronen Aufgaben

Dieser Code erhöht die Mindestanzahl von Threadpool-Threads:

    ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  

Weitere Informationen finden Sie unter [ThreadPool.SetMinThreads-Methode][ThreadPool.SetMinThreads-Methode] auf MSDN.

#### <a name="subheading11"></a>Nutzung der .NET 4.5 Garbage Collection

Verwenden Sie .NET 4.5 oder höher, damit die Clientanwendung die Leistungsverbesserungen bei der Server Garbage Collection nutzen kann.

Weitere Informationen finden Sie im Artikel [Eine Übersicht der Leistungsverbesserungen in .NET 4.5][Eine Übersicht der Leistungsverbesserungen in .NET 4.5] auf MSDN.

### <a name="subheading12"></a>Uneingeschränkte Parallelität

Während Parallelität großartig für die Leistung sein kann, verwenden Sie uneingeschränkte Parallelität (ohne Beschränkung der Anzahl von Threads und/oder parallelen Anforderungen) zum Hochladen oder Herunterladen von Daten mit Vorsicht. Das gilt auch für mehrere Worker zum Zugriff auf mehrere Partitionen (Container, Warteschlangen oder Tabellenpartitionen) im selben Speicherkonto oder für den Zugriff auf mehrere Elemente in derselben Partition. Bei uneingeschränkter Parallelität kann die Kapazität des Clientgeräts oder das Skalierbarkeitsziel des Speicherkontos überschritten werden, sodass es zu höherer Latenz und Drosselung kommt.

### <a name="subheading13"></a>Speicherclientbibliotheken und Tools

Verwenden Sie immer die neuesten von Microsoft bereitgestellten Clientbibliotheken und Tools. Zum Redaktionszeitpunkt sind RTM-Bibliotheken für .NET, Windows Phone, Windows Runtime und Java sowie Vorschaubibliotheken in anderen Sprachen wie node.js und C++ verfügbar. Darüber hinaus hat Microsoft PowerShell-Cmdlets und plattformübergreifende Befehlszeilentools in Node.js für die Verwendung mit dem Azure-Speicher entwickelt. Microsoft entwickelt diese Tools aktiv im Hinblick auf die Leistung, hält sie auf dem aktuellen Stand der Dienstversionen und stellt sicher, dass sie viele der bewährten Leistungsmethoden intern abwickeln.

### Wiederholungsversuche

#### <a name="subheading14"></a>Drosselung/Server ausgelastet

In einigen Fällen kann der Speicherdienst Ihre Anwendung drosseln oder einfach aufgrund einer vorübergehenden Bedingung nicht in der Lage sein, die Anforderung auszuführen, sodass ein Fehlercode „503 Server Busy“ oder „500 Operation Timeout“ zurückgegeben wird. Dies kann passieren, wenn Ihre Anwendung sich an die Skalierbarkeitsziele annähert oder wenn das System Ihre partitionierten Daten neu ausgleicht, um einen höheren Durchsatz zu ermöglichen. Die Clientanwendung sollte normalerweise den Vorgang erneut versuchen, der diesen Fehler verursacht hat; ein späterer Versuch derselben Anforderung kann erfolgreich sein. Wenn der Speicherdienst jedoch Ihre Anwendung drosselt, weil die Skalierbarkeitsziele überschritten wurden, oder wenn der Dienst aus einem anderen Grund nicht in der Lage war, die Anforderung auszuführen, verschlimmern aggressive Wiederholungsversuche meist das Problem. Aus diesem Grund sollten Sie exponentiell ansteigende Wartezeiten verwenden (dieses Verhalten wird standardmäßig von den Clientbibliotheken verwendet). Beispielsweise kann Ihre Anwendung nach 2 Sekunden, dann nach 4 Sekunden, nach 10 Sekunden und nach 30 Sekunden einen Wiederholungsversuch starten und dann komplett aufgeben. Dieses Verhalten führt dazu, dass Ihre Anwendung die Dienstbelastung deutlich reduziert, statt Probleme weiter zu verschärfen.

Beachten Sie, dass Verbindungsfehler sofort wiederholt werden können, da sie kein Ergebnis einer Drosselung sind und nur vorübergehend bestehen sollten.

#### <a name="subheading15"></a>Nicht wiederholbare Fehler

Die Clientbibliotheken erkennen, welche Fehler Wiederholungsversuche nach sich ziehen und welche nicht. Wenn Sie jedoch Ihren eigenen Code für die Speicher-REST-API schreiben, beachten Sie, auf welche Fehler keine Wiederholungsversuche folgen sollten: Beispiel: Die Antwort 400 (unzulässige Anforderung) gibt an, dass die Clientanwendung eine Anforderung gesendet hat, die nicht verarbeitet werden konnte, da sie nicht in der erwarteten Form vorlag. Das erneute Senden dieser Anforderung führt jedes Mal zur selben Antwort, daher ist es nicht sinnvoll, es wiederholt zu versuchen. Wenn Sie Ihren eigenen Code für die Speicher-REST-API schreiben, beachten Sie, was die Fehlercodes bedeuten und ob sie Wiederholungsversuche nach sich ziehen sollten.

#### Nützliche Ressourcen

Weitere Informationen zu Speicherfehlercodes finden Sie unter [Status- und Fehlercodes][Status- und Fehlercodes] auf der Microsoft Azure-Website.

## Blobs

Zusätzlich zu den zuvor beschriebenen bewährten Vorgehensweisen für [Alle Dienste][Alle Dienste] können die folgenden bewährten Vorgehensweisen speziell für den Blobdienst angewendet werden.

### Blob-spezifische Skalierbarkeitsziele

#### <a name="subheading16"></a>Bandbreite und Vorgänge pro Blob

Sie können in einem einzelnen Blob bis zu maximal 60 MB/Sekunde lesen oder schreiben (dies entspricht etwa 480 MBit/s), was die Kapazität vieler clientseitigen Netzwerke (einschließlich der physischen NIC auf dem Clientgerät) übersteigt. Außerdem unterstützt ein einzelner Blob bis zu 500 Anforderungen pro Sekunde. Wenn Sie über mehrere Clients verfügen, die denselben Blob lesen und Sie dieses Limit wahrscheinlich überschreiten, sollten Sie ein CDN für die Blob-Verteilung verwenden.

Weitere Informationen zum Zieldurchsatz für Blobs finden Sie unter [Skalierbarkeits- und Leistungsziele für Windows Azure-Speicher][Skalierbarkeitsziele] auf MSDN.

### Kopieren und Verschieben von Blobs

#### <a name="subheading17"></a>Kopieren von Blobs

Mit der Version 2012-02-12 der Speicher-REST-API wurde die hilfreiche Möglichkeit eingeführt, Blobs über Konten hinweg zu kopieren. Eine Clientanwendung kann den Speicherdienst anweisen, einen Blob aus einer anderen Quelle (ggf. aus einem anderen Speicherkonto) zu kopieren und dann den Dienst den Kopiervorgang asynchron durchführen lassen. Dies kann die für die Anwendung bei der Migration von Daten aus anderen Speicherkonten erforderliche Bandbreite deutlich reduzieren, da Sie die Daten nicht herunter- und wieder hochladen müssen.

Es sollte jedoch berücksichtigt werden, dass es beim Kopieren zwischen Speicherkonten keine Zeitgarantie gibt, wann der Kopiervorgang abgeschlossen ist. Wenn Ihre Anwendung das Kopieren des Blobs schnell und kontrolliert abschließen soll, kann es besser sein, den Blob zu kopieren, indem Sie ihn auf einen virtuellen Computer herunterladen und dann auf das Ziel hochladen. Stellen Sie für genaue Vorhersehbarkeit in dieser Situation sicher, dass der Kopiervorgang von einem virtuellen Computer in derselben Azure-Region durchgeführt wird, andernfalls kann sich die Netzwerkleistung auf die Kopierleistung auswirken. Darüber hinaus können Sie den Fortschritt eines asynchronen Kopiervorgangs programmgesteuert überwachen.

Beachten Sie, dass Kopien im selben Speicherkonto normalerweise sehr schnell abgeschlossen sind.

Weitere Informationen finden Sie unter [Copy BLOB][Copy BLOB] auf MSDN.

#### <a name="subheading18"></a>Verwenden von AzCopy

Das Azure-Speicherteam hat ein Befehlszeilentool namens „AzCopy“ veröffentlicht, das die Massenübertragung von vielen Blobs von, an und über Speicherkonten hinweg unterstützen soll. Dieses Tool ist für dieses Szenario optimiert und kann hohe Übertragungsraten erzielen. Die Verwendung wird für Massenszenarien beim Hochladen, Herunterladen und Kopieren empfohlen. Weitere Informationen und einen Download finden Sie [hier][hier].

#### <a name="subheading19"></a>Azure Import-/Export-Dienst

Für größere Datenmengen (mehr als 1 TB) bietet der Azure-Speicher einen Import-/Export-Dienst, der das Hochladen in und Herunterladen aus dem Blob-Speicher durch den Versand von Festplatten ermöglicht. Sie können Ihre Daten auf eine Festplatte speichern und zum Hochladen an Microsoft senden oder eine leere Festplatte zum Herunterladen der Daten an Microsoft schicken. Weitere Informationen dazu finden Sie [hier][2]. Dies kann sehr viel effizienter als das Hochladen/Herunterladen einer solchen Datenmenge über das Netzwerk sein.

### <a name="subheading20"></a>Verwenden von Metadaten

Der Blobdienst unterstützt Header-Anforderungen, die Metadaten zum Blob enthalten können. Wenn Ihre Anwendung beispielsweise die EXIF-Daten eines Fotos benötigt, kann sie das Foto herunterladen und diese extrahieren. Um Bandbreite zu sparen und die Leistung zu verbessern, kann Ihre Anwendung aber auch die EXIF-Daten in den Blob-Metadaten speichern, wenn die Anwendung das Foto hochlädt. Dann können Sie die EXIF-Daten aus den Metadaten mit der HEAD-Anforderung abrufen und so Bandbreite und die Verarbeitungszeit sparen, die bei jedem Lesen des Blobs erforderlich wäre, um die EXIF-Daten zu extrahieren. Dies kann in Fällen nützlich sein, wenn Sie nur die Metadaten benötigen und nicht den vollständigen Inhalt eines Blobs. Beachten Sie, dass pro Blob nur 8 KB Metadaten gespeichert werden können (der Dienst akzeptiert keine Anfrage, mit der mehr als das gespeichert werden soll); wenn die Daten also diese Größe überschreiten, können Sie diese Vorgehensweise nicht anwenden.

Ein Beispiel für das Abrufen der Blob-Metadaten mithilfe von .NET finden Sie unter [Festlegen und Abrufen von Eigenschaften und Metadaten][Festlegen und Abrufen von Eigenschaften und Metadaten] auf MSDN.

### Schnelles Hochladen

Um Blobs schnell hochzuladen, beantworten Sie sich zuerst folgende Frage: Laden Sie einen oder mehrere Blobs hoch? Befolgen Sie die Anleitung unten, um die richtige Methode für Ihr Szenario auszuwählen.

#### <a name="subheading21"></a>Schnelles Hochladen eines großen Blobs

Um einen großen Blob schnell hochzuladen, sollte Ihre Clientanwendung ihre Blöcke oder Seiten parallel hochladen (beachten Sie dabei die Skalierbarkeitsziele für einzelne Blobs sowie für das gesamte Speicherkonto). Die offiziell von Microsoft bereitgestellten RTM-Speicherclientbibliotheken (.NET, Java) haben die Möglichkeiten dafür. Verwenden Sie für die einzelnen Bibliotheken die unten angegebenen Objekte/Eigenschaften, um den Grad an Parallelität festzulegen.

-   .NET: Stellen Sie ParallelOperationThreadCount für ein zu verwendendes BlobRequestOptions-Objekt ein.
-   Java/Android: Verwenden Sie BlobRequestOptions.setConcurrentRequestCount()
-   Node.js: Verwenden Sie parallelOperationThreadCount entweder für die Anforderungsoptionen oder für den Blobdienst.
-   C++: Verwenden Sie die Methode blob\_request\_options::set\_parallelism\_factor.

#### <a name="subheading22"></a>Schnelles Hochladen von mehreren Blobs

Um viele Blobs schnell hochzuladen, laden Sie die Blobs parallel hoch. Dies funktioniert schneller als das Hochladen von jeweils einem Blob, dessen Blöcke parallel hochgeladen werden, da der Hochladevorgang auf mehrere Partitionen des Speicherdiensts verteilt wird. Ein einzelner Blob unterstützt einen Durchsatz von 60 MB/Sekunde (etwa 480 MBit/s). Zum Redaktionszeitpunkt unterstützten georedundante Speicherkonten (GRS) bis zu 20 GBit/s für eingehende Daten, was einem viel höheren Durchsatz als bei einem einzelnen Blob entspricht. [AzCopy][Verwenden Sie AzCopy für Massenkopien von Blobs?] führt das Hochladen standardmäßig parallel aus, was für dieses Szenario ebenfalls empfohlen wird.

### <a name="subheading23"></a>Auswählen des richtigen Blobtyps

Der Azure-Speicher unterstützt zwei Typen von Blobs: *Seiten*-Blobs und *Block*-Blobs. Je nach Nutzungsszenario wirkt sich die Auswahl des Blobtyps auf die Leistung und Skalierbarkeit der Lösung aus. Blockblobs sind gut für das effiziente Hochladen großer Datenmengen geeignet; wenn beispielsweise eine Clientanwendung Fotos oder Videos in den Blob-Speicher hochladen soll. Seitenblobs sind für den Schreibzugriff auf Daten optimiert; Azure-VHDs werden beispielsweise als Seitenblobs gespeichert.

Weitere Informationen finden Sie unter [Grundlegendes zu Block-BLOBs und Seiten-BLOBs][Grundlegendes zu Block-BLOBs und Seiten-BLOBs] auf MSDN.

## Tabellen

Zusätzlich zu den zuvor beschriebenen bewährten Vorgehensweisen für [Alle Dienste][Alle Dienste] können die folgenden bewährten Vorgehensweisen speziell für den Tabellendienst angewendet werden.

### <a name="subheading24"></a>Tabellenspezifische Skalierbarkeitsziele

Zusätzlich zur Bandbreiteneinschränkung des gesamten Speicherkontos haben Tabellen die folgende spezifische Skalierbarkeitsgrenze. Beachten Sie, dass das System den ansteigenden Datenverkehr ausbalanciert, bei plötzlichen Verkehrsspitzen wird jedoch der komplette Durchsatz nicht sofort verfügbar. Falls Ihr Datenmuster Spitzenlasten enthält, müssen Sie mit Drosselung und/oder Timeouts rechnen, während der Speicherdienst den automatischen Lastenausgleich für die Tabelle ausführt. Eine langsame Steigerung zeigt in der Regel bessere Ergebnisse, da das System genügend Zeit für den Lastenausgleich hat.

#### Entitäten pro Sekunde (Konto)

Die Skalierbarkeitsgrenze für den Tabellenzugriff liegt bei 20.000 Entitäten (je 1 KB) pro Sekunde für ein Konto. Im Allgemeinen zählt jede Entität, die eingefügt, aktualisiert, gelöscht oder gescannt wird, für dieses Ziel. Daher zählt eine Batcheinfügung, die 100 Entitäten enthält, als 100 Entitäten. Eine Abfrage, die 1.000 Entitäten scannt und 5 zurückgibt, zählt als 1.000 Entitäten.

#### Entitäten pro Sekunde (Partition)

Innerhalb einer Partition liegt das Skalierbarkeitsziel für den Tabellenzugriff bei 2.000 Entitäten (je 1 KB) pro Sekunde, mit derselben Zählweise wie im vorherigen Abschnitt beschrieben.

### Konfiguration

In diesem Abschnitt sind verschiedene Schnellkonfigurationseinstellungen aufgelistet, die eine deutliche Leistungsoptimierung im Tabellendienst bewirken.

#### <a name="subheading25"></a>Verwenden von JSON

Ab Speicherdienstversion 2013-08-15 unterstützt der Tabellendienst die Verwendung von JSON statt des XML-basierten AtomPub-Formats für die Übertragung von Tabellendaten. Dies kann die Nutzlast um bis zu 75 % verringern und die Anwendungsleistung deutlich verbessern.

Weitere Informationen finden Sie im Blogeintrag [Windows Azure-Tabellen: Einführung in JSON][Windows Azure-Tabellen: Einführung in JSON] und [Nutzlastformat für Tabellendienstvorgänge][Nutzlastformat für Tabellendienstvorgänge] auf MSDN.

#### <a name="subheading26"></a>Deaktivieren von Nagle

Der Nagle-Algorithmus ist in TCP/IP-Netzwerken weit verbreitet, um die Netzwerkleistung zu verbessern. Er ist jedoch nicht unter allen Umständen optimal (z. B. hoch interaktive Umgebungen). Für den Azure-Speicher hat der Nagle-Algorithmus negative Auswirkungen auf Anforderungen an Tabellen- und Warteschlangendienste, daher sollten Sie ihn möglichst deaktivieren.

Weitere Informationen finden Sie im Blogeintrag [Nagle-Algorithmus geht nicht freundlich mit kleinen Anfragen um][Nagle-Algorithmus geht nicht freundlich mit kleinen Anfragen um], in dem erläutert wird, warum der Nagle-Algorithmus unzureichend mit Tabellen- und Warteschlangenanforderungen interagiert und wie Sie ihn in der Clientanwendung deaktivieren.

### Schema

Wie Sie Ihre Daten präsentieren und abfragen, ist der größte Faktor mit Auswirkung auf die Leistung des Tabellendiensts. Während jede Anwendung anders ist, sind in diesem Abschnitt einige allgemeine bewährte Vorgehensweisen für folgende Bereiche beschrieben:

-   Tabellenentwurf
-   Effiziente Abfragen
-   Effiziente Datenaktualisierungen

#### <a name="subheading27"></a>Tabellen und Partitionen

Tabellen sind in Partitionen unterteilt. Jede in einer Partition gespeicherte Entität verwendet denselben Partitionsschlüssel und hat einen eindeutigen Zeilenschlüssel, damit sie innerhalb der Partition identifiziert werden kann. Partitionen bieten Vorteile, führen jedoch auch zu Skalierbarkeitsgrenzen.

-   Vorteile: Sie können die Entitäten innerhalb derselben Partition in einer einzigen, unteilbaren Batchtransaktion aktualisieren, die bis zu 100 separate Speichervorgänge enthalten kann (Gesamtgrößenlimit 4 MB). Bei derselben Menge abzurufender Entitäten können Sie auch die Daten innerhalb einer Partition effizienter abfragen als Daten, die sich über mehrere Partitionen verteilen (lesen Sie für noch mehr Empfehlungen zur Abfrage von Tabellendaten weiter).
-   Skalierbarkeitsgrenze: Für den Zugriff auf Entitäten, die in einer einzigen Partition gespeichert sind, kann kein Lastenausgleich erfolgen, da die Partitionen unteilbare Batchtransaktionen unterstützen. Aus diesem Grund ist das Skalierbarkeitsziel für eine einzelne Tabellenpartition niedriger als für den gesamten Tabellendienst.

Aufgrund dieser Eigenschaften von Tabellen und Partitionen sollten Sie die folgenden Entwurfsprinzipien beachten:

-   Daten, die Ihre Clientanwendung häufig in derselben logischen Arbeitseinheit aktualisiert oder abfragt, sollten sich in derselben Partition befinden. Der Grund dafür kann sein, dass Ihre Anwendung Schreibvorgänge aggregiert oder dass Sie unteilbare Batchtransaktionen nutzen möchten. Außerdem können Daten innerhalb einer Partition effizienter mit nur einer Abfrage abgefragt werden als Daten, die sich über mehrere Partitionen verteilen.
-   Daten, die Ihre Clientanwendung nicht in derselben logischen Arbeitseinheit einfügt/aktualisiert (Einzelabfrage oder Batchaktualisierung), sollten sich in separaten Partitionen befinden. Ein wichtiger Hinweis ist hier, dass es keine Begrenzung der Anzahl der Partitionsschlüssel in einer Tabelle gibt, daher sind selbst Millionen von Partitionsschlüsseln kein Problem und wirken sich nicht auf die Leistung aus. Wenn es sich bei Ihrer Anwendung beispielsweise um eine beliebte Website mit Benutzeranmeldung handelt, kann die Benutzer-ID als Partitionsschlüssel verwendet werden.

#### Hot Partition

Eine Hot Partition ist eine Partition, die einen überproportionalen Anteil des Datenverkehrs an ein Konto empfängt und kein Lastenausgleich erfolgen kann, da es sich um eine einzelne Partition handelt. Im Allgemeinen wird eine Hot Partition mit einer der folgenden Möglichkeiten erstellt:

##### <a name="subheading28"></a>Nur-Anhängen/Nur-Voranstellen-Muster

Das Muster „Nur anhängen“ bezieht sich darauf, dass (fast) der gesamte Datenverkehr an einen bestimmten PS abhängig von der aktuellen Zeit ansteigt oder absinkt. Ein Beispiel ist eine Anwendung, in der das aktuelle Datum als Partitionsschlüssel für Protokolldaten verwendet wird. Das führt dazu, dass alle Einfügungen in die letzte Partition der Tabelle eingefügt werden und das System keinen Lastenausgleich durchführen kann, da alle Schreibvorgänge am Ende der Tabelle erfolgen. Wenn das Datenvolumen in dieser Partition das Skalierbarkeitsziel auf Partitionsebene überschreitet, kommt es zu einer Drosselung. Es ist besser sicherzustellen, dass der Datenverkehr an mehrere Partitionen geleitet wird, um den Lastenausgleich für Anfragen in der Tabelle zu verteilen.

##### <a name="subheading29"></a>Daten mit hohem Verkehrsaufkommen

Wenn Ihr Partitionsschema dazu führt, dass in einer einzigen Partition Daten enthalten sind, die deutlich häufiger als in anderen Partitionen verwendet werden, kann ebenfalls eine Drosselung erfolgen, wenn diese Partition das Skalierbarkeitsziel für eine einzelne Partition erreicht. Sie sollten besser sicherstellen, dass Ihr Partitionsschema so angelegt ist, dass sich keine einzelne Partition an die Skalierbarkeitsziele annähert.

#### Abfragen

In diesem Abschnitt werden bewährte Vorgehensweisen für Tabellendienstabfragen erläutert.

##### <a name="subheading30"></a>Abfragebereich

Es gibt mehrere Möglichkeiten, den Entitätsbereich für die Abfrage anzugeben. Im Folgenden werden diese einzeln erläutert.

In der Regel sollten Sie Scans vermeiden (Abfragen, die sich auf mehr als eine Entität beziehen), wenn Sie jedoch scannen müssen, organisieren Sie Ihre Daten so, dass der Scan die benötigten Daten abruft, ohne eine übermäßige Menge an nicht benötigten Entitäten zurückzugeben.

###### Punktabfragen

Eine Punktabfrage ruft genau eine Entität ab. Dazu wird sowohl der Partitionsschlüssel als auch der Zeilenschlüssel der abzurufenden Entität angegeben. Diese Abfragen sind sehr effizient und Sie sollten diese so oft wie möglich verwenden.

###### Partitionsabfragen

Eine Partitionsabfrage ist eine Abfrage, die eine Gruppe von Daten mit einem gemeinsamen Partitionsschlüssel abruft. In der Regel gibt die Abfrage einen Wertebereich für den Zeilenschlüssel oder für eine Entitätseigenschaft zusätzlich zum Partitionsschlüssel an. Diese sind weniger effizient als Punktabfragen und ihr Einsatz sollte sorgfältig überlegt werden.

###### Tabellenabfragen

Eine Tabellenabfrage ist eine Abfrage, die eine Gruppe von Entitäten ohne gemeinsamen Partitionsschlüssel abruft. Diese Abfragen sind nicht effizient und Sie sollten diese möglichst vermeiden.

##### <a name="subheading31"></a>Abfragedichte

Ein weiterer Schlüsselfaktor bei der Abfrageeffizienz ist die Anzahl der zurückgegebenen Entitäten im Vergleich zur Anzahl der gescannten Entitäten, um den zurückgegebenen Satz zu suchen. Wenn Ihre Anwendung eine Tabellenabfrage mit einem Filter für einen Eigenschaftswert durchführt, den nur 1 % der Daten teilen, scannt die Abfrage 100 Entitäten für jede zurückgegebene Entität. Die zuvor erläuterten Skalierbarkeitsziele für Tabellen beziehen sich alle auf die Anzahl der gescannten Entitäten, nicht auf die Anzahl der zurückgegebenen Entitäten; auch eine niedrige Abfragedichte kann dazu führen, dass der Tabellendienst die Anwendung drosselt, da so viele Entitäten gescannt werden müssen, um die gesuchte Entität zurückzugeben. Hinweise, wie Sie dies vermeiden können, finden Sie unten im Abschnitt [Denormalisierung][Haben Sie Ihre Daten so normalisiert, dass Sie ineffiziente Abfragen oder mehrere Leseanforderungen beim Abrufen von Daten vermeiden?].

##### Einschränken der Menge der zurückgegebenen Daten

###### <a name="subheading32"></a>Filterung

Wenn Sie wissen, dass eine Abfrage Entitäten zurückgibt, die Sie in der Clientanwendung nicht benötigen, sollten Sie einen Filter verwenden, um die Größe des zurückgegebenen Sets zu reduzieren. Während die nicht an den Client zurückgegebenen Entitäten trotzdem für die Skalierbarkeitsgrenzen zählen, wird sich Ihre Anwendungsleistung aufgrund der reduzierten Netzwerknutzlast und der reduzierten Entitätenanzahl, die von der Clientanwendung verarbeitet werden müssen, verbessern. Lesen Sie trotzdem oben den Hinweis zur [Abfragedichte][Erfassen Ihre Abfragen normalerweise nur Zeilen für die Rückgabe, die Ihre Anwendung auch verwenden wird?] – die Skalierbarkeitsziele beziehen sich auf die Anzahl der gescannten Entitäten, daher kann eine Abfrage, bei der viele Entitäten herausgefiltert werden, trotzdem zur Drosselung führen, auch wenn nur wenige Entitäten zurückgegeben werden.

###### <a name="subheading33"></a>Projektion

Wenn Ihre Clientanwendung nur bestimmte Eigenschaften der Entitäten in der Tabelle benötigt, können Sie Projektion verwenden, um die Größe des zurückgegebenen Datensatzes einzuschränken. Wie bei der Filterung hilft auch dies bei der Reduzierung der Netzwerklast und der Clientverarbeitung.

##### <a name="subheading34"></a>Denormalisierung

Im Gegensatz zum Arbeiten mit relationalen Datenbanken führen die bewährten Vorgehensweisen für das effiziente Abfragen von Tabellendaten zur Denormalisierung Ihrer Daten. Das heißt, dieselben Daten werden in mehreren Entitäten dupliziert (eine für jeden Schlüssel, den Sie zum Suchen der Daten verwenden könnten), um die Anzahl der Entitäten zu minimieren, die eine Abfrage scannen muss, um die vom Client benötigten Daten zu finden, statt eine große Anzahl von Entitäten zu scannen, um die für die Anwendung benötigten Daten zu finden. Beispielsweise kann auf einer E-Commerce-Website eine Bestellung sowohl nach Kunden-ID (zur Abfrage der Kundenbestellungen) als auch nach Datum (zur Abfrage der Bestellungen für ein bestimmtes Datum) gefunden werden. Bei der Tabellenspeicherung ist es am besten, die Entität zweimal zu speichern (bzw. darauf zu verweisen) – einmal mit Tabellenname, PS und ZS für die Suche nach der Kunden-ID und einmal für die Suche nach dem Datum.

#### Einfügen/Aktualisieren/Löschen

In diesem Abschnitt werden bewährte Vorgehensweise für das Ändern von im Tabellendienst gespeicherte Entitäten erläutert.

##### <a name="subheading35"></a>Batchverarbeitung

Batchtransaktionen werden in Azure Storage als Entitätsgruppentransaktionen (ETG) bezeichnet; alle Vorgänge innerhalb einer ETG müssen sich auf eine einzige Partition in einer einzigen Tabelle beziehen. Wenn möglich, verwenden Sie ETGs für die Durchführung von Einfüge-, Aktualisierungs- und Löschvorgängen in Batches. Dadurch reduziert sich die Anzahl der Roundtrips von Ihrer Clientanwendung zum Server und somit die Anzahl der kostenpflichtigen Transaktionen (eine ETG zählt zu Abrechnungszwecken als eine Transaktion und kann bis zu 100 Speichervorgänge umfassen); außerdem werden unteilbare Aktualisierungen ermöglicht (alle Vorgänge in einer ETG sind erfolgreich oder schlagen fehl). Umgebungen mit hoher Latenz wie beispielsweise Mobilgeräte profitieren stark von der Verwendung von ETGs.

##### <a name="subheading36"></a>Upsert

Verwenden Sie wenn möglich den Tabellenvorgang **Upsert** zum Einfügen und Aktualisieren. Es gibt zwei **Upsert**-Typen, die beide effizienter als herkömmliche **Insert**- und **Update**-Vorgänge sind:

-   **InsertOrMerge**: Verwenden Sie dies, wenn Sie eine Teilmenge der Entitätseigenschaften hochladen möchten, aber nicht sicher sind, ob die Entität bereits vorhanden ist. Falls die Entität vorhanden ist, aktualisiert dieser Aufruf die in **Upsert** enthaltenen Eigenschaften und lässt alle vorhandenen Eigenschaften unverändert – falls die Entität nicht vorhanden ist, wird die neue Entität eingefügt. Dies funktioniert ähnlich wie die Projektion in einer Abfrage, da Sie nur die geänderten Eigenschaften hochladen müssen.
-   **InsertOrReplace**: Verwenden Sie dies, wenn Sie eine neue Entität hochladen möchten, aber nicht sicher sind, ob diese bereits vorhanden ist. Sie sollten diese Option nur verwenden, wenn Sie wissen, dass die neu hochgeladene Entität komplett richtig ist, da sie die alte Entität vollständig überschreibt. Wenn Sie beispielsweise die Entität aktualisieren möchten, die den aktuellen Standort eines Benutzers speichert, unabhängig davon, ob die Anwendung zuvor Standortdaten für den Benutzer gespeichert hatte, ist die neue Entität komplett, da Sie keine Informationen aus der vorherigen Entität benötigen.

##### <a name="subheading37"></a>Speichern von Datenreihen in einer einzigen Entität

Manchmal speichert eine Anwendung eine Reihe von Daten, die sie häufig alle zusammen abgerufen werden. Beispielsweise kann eine Anwendung die CPU-Auslastung über einen bestimmten Zeitraum erfassen, um ein fortlaufendes Diagramm der Daten aus den letzten 24 Stunden zu plotten. Eine Möglichkeit ist es, eine Tabellenentität pro Stunde anzulegen, sodass jede Entität eine bestimmte Stunde darstellt und die CPU-Auslastung für diese Stunde speichert. Um diese Daten zu plotten, muss die Anwendung die Entitäten abrufen, welche die Daten aus den letzten 24 Stunden enthalten.

Alternativ kann die Anwendung auch die CPU-Auslastung jeder Stunde als separate Eigenschaft einer einzelnen Entität speichern; um die jeweilige Stunde zu aktualisieren, kann die Anwendung einen einzelnen **InsertOrMerge Upsert**-Aufruf verwenden, um den Wert für die letzte Stunde zu aktualisieren. Um die Daten zu plotten, muss die Anwendung nur eine einzige Entität statt 24 abrufen, sodass die Abfrage sehr effizient verläuft (siehe Erläuterungen oben zum [Abfragebereich][Haben Sie Ihr Schema so konzipiert, dass in den meisten Fällen Punktabfragen verwendet werden können und Tabellenabfragen nur sparsam erfolgen?]).

##### <a name="subheading38"></a>Speichern strukturierter Daten in Blobs

Manchmal hat man das Gefühl, strukturierte Daten sollten immer in Tabellen gespeichert werden, aber Entitätsbereiche werden immer zusammen abgerufen und können als Batch eingefügt werden. Ein gutes Beispiel dafür ist eine Protokolldatei. In diesem Fall können Sie Protokolle für mehrere Minuten als Batch zusammenfassen, einfügen und dann immer mehrere Protokollminuten gleichzeitig abrufen. Aus Leistungsgründen ist es in diesem Fall besser, Blobs statt Tabellen zu verwenden, da Sie damit die Anzahl der geschriebenen/zurückgegebenen Objekte sowie normalerweise auch die Anzahl der gestellten Anfragen deutlich reduzieren können.

## Warteschlangen

### <a name="subheading39&quot;"></a>Scalability Limits

A single queue can process approximately 2,000 messages (1KB each) per second (each AddMessage, GetMessage, and DeleteMessage count as a message here). If this is insufficient for your application, you should use multiple queues and spread the messages across them.

You can view the current scalability targets on the page [Azure Storage Scalability and Performance Targets][Skalierbarkeitsziele] on MSDN.

### <a name="subheading40&quot;"></a>Deaktivieren von Nagle

Lesen Sie den Abschnitt über den Nagle-Algorithmus in der Tabellenkonfiguration – der Nagle-Algorithmus wirkt sich meist negativ auf die Leistung von Warteschlangenanforderungen aus, daher sollten Sie ihn deaktivieren.

### <a name="subheading41&quot;"></a>Message Size

Queue performance and scalability decreases as message size increases. You should place only the information the receiver needs in a message.

### <a name="subheading42&quot;"></a>Batchabruf

Sie können bis zu 32 Nachrichten aus einer Warteschlange in einem einzigen Vorgang abrufen. Dies kann die Anzahl der Roundtrips von der Clientanwendung reduzieren, was besonders für Umgebungen mit hoher Latenz wie beispielsweise Mobilgeräte nützlich ist.

### <a name="subheading43&quot;"></a>Queue Polling Interval

Most applications poll for messages from a queue, which can be one of the largest sources of transactions for that application. Select your polling interval wisely: polling too frequently could cause your application to approach the scalability targets for the queue. However, at 200,000 transactions for $0.01 (at the time of writing), a single processor polling once every second for a month would cost less than 15 cents so cost is not typically a factor that affects your choice of polling interval.

For up to date cost information, see [Storage Pricing Details][Storage Pricing Details].

### <a name="subheading44&quot;"></a>UpdateMessage

Sie können **UpdateMessage** verwenden, um das Unsichtbarkeits-Zeitlimit zu erhöhen oder die Statusinformationen einer Nachricht zu aktualisieren. Trotz der Möglichkeiten dieser Option sollten Sie beachten, dass jeder **UpdateMessage**-Vorgang für das Skalierbarkeitsziel zählt. Dies kann jedoch sehr viel effizienter sein als ein Workflow, der einen Auftrag von einer Warteschlange zur nächsten verschiebt, wenn der jeweilige Schritt abgeschlossen ist. Mithilfe des Vorgangs **UpdateMessage** kann Ihre Anwendung den Auftragsstatus in der Nachricht speichern und dann weiterarbeiten, statt die Nachricht jedes Mal für den nächsten Schritt erneut in die Warteschlange zu stellen.

Weitere Informationen finden Sie im Artikel [Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange][Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange].

<h3> <a name=subheading45"></a>Anwendungsarchitektur</h3> 
<p>Verwenden Sie Warteschlangen, um die Anwendungsarchitektur skalierbar zu machen. Nachfolgend sind einige Möglichkeiten aufgelistet, wie Sie Warteschlangen verwenden können, um Ihre Anwendung besser skalierbar zu gestalten:</p> 
<ul>
<li>Sie können Warteschlangen verwenden, um Arbeits-Backlogs zur Verarbeitung zu erstellen und die Arbeitsauslastung in der Anwendung gleichmäßig zu verteilen. Beispielsweise können Sie Benutzeranfragen in die Warteschlange stellen, um prozessorintensive Arbeiten wie die Größenänderung von hochgeladenen Bildern durchzuführen.</li> 

<li>Sie können Warteschlangen verwenden, um Teile Ihrer Anwendung zu entkoppeln, sodass diese unabhängig skaliert werden können. Beispielsweise kann ein Web-Front-End Umfrageergebnisse von Benutzern zur späteren Analyse und Speicherung in eine Warteschlange stellen. Sie können mehr Workerrolleninstanzen hinzufügen, um die Warteschlangendaten nach Bedarf zu verarbeiten.</li>
</ul>

<h2 id="conclusion">Zusammenfassung</h2>
<p>In diesem Artikel wurden einige der häufigsten bewährten Vorgehensweisen zur Leistungsoptimierung bei der Verwendung des Azure-Speichers erläutert. Wir empfehlen jedem Anwendungsentwickler, seine Anwendung anhand dieser Vorgehensweisen zu überprüfen und ggf. die Empfehlungen umzusetzen, um bessere Leistung für ihre Anwendungen zu erzielen, die Azure-Speicher verwenden.</p>

  [Verwenden Sie exponentiell ansteigende Wartezeiten für Wiederholungsversuche, um Fehler und Zeitüberschreitungen zu vermeiden?]: #subheading14
  [Bleibt Ihre Anwendung innerhalb der Bandbreiten- oder Vorgangs-Skalierbarkeitsziele für einzelne Blobs?]: #subheading16
  [Verwenden Sie AzCopy für Massenkopien von Blobs?]: #subheading18
  [Nähern Sie sich den Skalierbarkeitszielen für Entitäten pro Sekunde?]: #subheading24
  [Haben Sie Ihr Schema so konzipiert, dass in den meisten Fällen Punktabfragen verwendet werden können und Tabellenabfragen nur sparsam erfolgen?]: #subheading30
  [Erfassen Ihre Abfragen normalerweise nur Zeilen für die Rückgabe, die Ihre Anwendung auch verwenden wird?]: #subheading31
  [Haben Sie Ihre Daten so normalisiert, dass Sie ineffiziente Abfragen oder mehrere Leseanforderungen beim Abrufen von Daten vermeiden?]: #subheading34
  [Nähern Sie sich den Skalierbarkeitszielen für Nachrichten pro Sekunde?]: #subheading39
  [Skalierbarkeitsziele]: http://msdn.microsoft.com/de-de/library/azure/dn249410.aspx
  [Nützliche Ressourcen]: #sub1useful
  [Windows Azure-Speicher: Redundanzoptionen und Lesezugriff georedundanter Speicher]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx
  [Azure-Preise auf einen Blick]: http://azure.microsoft.com/de-de/pricing/overview/
  [Bandbreiten-Skalierbarkeitsziele]: #sub1bandwidth
  [Bewährte Vorgehensweisen für den Entwurf umfangreicher Dienste auf Azure Cloud Services]: http://msdn.microsoft.com/de-de/library/dn197896.aspx
  [Azure CDN]: http://azure.microsoft.com/de-de/services/cdn/
  [Shared Access Signatures, Teil 1: Grundlagen zum SAS-Modell]: http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-shared-access-signature-part-1/
  [Cross-Origin Resource Sharing (CORS)-Support für den Azure-Speicherdienst]: http://msdn.microsoft.com/library/azure/dn535601.aspx
  [Festlegen und Abrufen von Eigenschaften und Metadaten]: http://msdn.microsoft.com/de-de/library/hh225342.aspx
  [Angeben von bedingten Headern für Vorgänge des Blob-Diensts]: http://msdn.microsoft.com/de-de/library/azure/dd179371.aspx
  [Webdienste: Gleichzeitige Verbindungen]: http://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx
  [ThreadPool.SetMinThreads-Methode]: http://msdn.microsoft.com/de-de/library/system.threading.threadpool.setminthreads(v=vs.110).aspx
  [Eine Übersicht der Leistungsverbesserungen in .NET 4.5]: http://msdn.microsoft.com/de-de/magazine/hh882452.aspx
  [Status- und Fehlercodes]: http://msdn.microsoft.com/de-de/library/azure/dd179382.aspx
  [Alle Dienste]: #allservices
  [Copy BLOB]: http://msdn.microsoft.com/de-de/library/dd894037.aspx
  [hier]: http://azure.microsoft.com/de-de/documentation/articles/storage-use-azcopy/
  [2]: http://azure.microsoft.com/de-de/documentation/articles/storage-import-export-service/
  [Grundlegendes zu Block-BLOBs und Seiten-BLOBs]: http://msdn.microsoft.com/de-de/library/azure/ee691964.aspx
  [Windows Azure-Tabellen: Einführung in JSON]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx
  [Nutzlastformat für Tabellendienstvorgänge]: http://msdn.microsoft.com/de-de/library/azure/dn535600.aspx
  [Nagle-Algorithmus geht nicht freundlich mit kleinen Anfragen um]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx
  [Storage Pricing Details]: http://azure.microsoft.com/de-de/pricing/details/storage/
  [Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange]: http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-queues/#change-contents
