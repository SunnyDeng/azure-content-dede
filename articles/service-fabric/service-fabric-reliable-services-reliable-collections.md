<properties
   pageTitle="Zuverlässige Auflistungen"
   description="Mithilfe von zuverlässigen Auflistungen (Reliable Collections) können Sie hoch verfügbare, skalierbare Cloudanwendungen mit geringer Latenz schreiben."
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="08/05/2015"
   ms.author="mcoskun"/>

# Zuverlässige Auflistungen

Zuverlässige Auflistungen (Reliable Collections) ermöglichen es Ihnen, hoch verfügbare, skalierbare Cloudanwendungen mit geringer Latenz auf die gleiche Weise wie Anwendungen für einen einzelnen Computer zu schreiben. Die Klassen im Namespace `Microsoft.ServiceFabric.Data.Collections` bieten eine Reihe sofort verwendbarer Auflistungen, die automatisch einen hochverfügbaren Zustand ermöglichen. Als Entwickler programmieren Sie lediglich die APIs für zuverlässige Auflistungen. Die zuverlässigen Auflistungen verwalten anschließend den replizierten lokalen Zustand automatisch.

Der Hauptunterschied zwischen zuverlässigen Auflistungen und anderen Hochverfügbarkeitstechnologien (z. B. Redis, Azure-Tabellendienst und Azure-Warteschlangendienst) ist, dass der Zustand lokal in der Dienstinstanz gespeichert wird und gleichzeitig hoch verfügbar ist. Dies bedeutet Folgendes:

1. Alle Lesevorgänge erfolgen lokal, was eine geringe Latenz und einen hohen Lesedurchsatz zur Folge hat.
2. Alle Schreibvorgänge lösen die Mindestanzahl von Netzwerk-E/As aus, was eine geringe Latenz und einen hohen Schreibdurchsatz zur Folge hat.

![Bild der Weiterentwicklung von Auflistungen.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Zuverlässige Auflistungen können als natürliche Weiterentwicklung der `System.Collections`-Klassen betrachtet werden: Der neue Satz von Auflistungen wurde für Cloudanwendungen und Umgebungen mit mehreren Computern konzipiert, ohne die Komplexität für den Entwickler zu erhöhen. Sie sind:

1. Repliziert: Zustandsänderungen werden für hohe Verfügbarkeit repliziert.
2. Persistent gespeichert: Daten werden persistent auf Datenträgern gespeichert, um sie vor größeren Ausfällen (z. B. einem Stromausfall im Rechenzentrum) zu schützen.
3. Asynchron: APIs sind asynchron, um sicherzustellen, dass Threads bei einem E/A nicht blockiert werden.
4. Transaktional: APIs nutzen die Abstraktion von Transaktionen, damit Sie mehrere zuverlässige Auflistungen auf einfache Weise in einem Dienst verwalten können.

Zuverlässige Auflistungen zeichnen sich durch von Beginn an starke Konsistenzgarantien aus, was die Argumentation hinsichtlich Anwendungszuständen erleichtert. Eine starke Konsistenz wird erreicht, indem Transaktionscommits erst abgeschlossen werden, nachdem die gesamte Transaktion auf ein Quorum von Replikaten (einschließlich des primären Replikats) angewendet wurde. Um eine schwächere Konsistenz zu erreichen, kann die Anwendung eine Bestätigung an den Client/Antragsteller zurück senden, bevor der asynchrone Commit zurückgegeben wird.

Die APIs für zuverlässige Auflistungen sind eine Weiterentwicklung der APIs für gleichzeitige Auflistungen (im Namespace `System.Collections.Concurrent`):

1. Asynchron: Gibt eine Aufgabe zurück, da die Vorgänge im Gegensatz zu zuverlässigen Auflistungen repliziert und persistent gespeichert werden.
2. Keine out-Parameter: Gibt mithilfe von `ConditionalResult<T>` anstelle von out-Parametern "Bool" und einen Wert zurück. `ConditionalResult<T>` entspricht `Nullable<T>`, erfordert aber für "struct" nicht "T".
3. Transaktionen: Verwendet ein Transaktionsobjekt, um dem Benutzer Gruppenaktionen für mehrere zuverlässige Auflistungen in einer Transaktion zu ermöglichen.

`Microsoft.ServiceFabric.Data.Collections` beinhaltet jetzt zwei Auflistungen:

1. [Zuverlässiges Wörterbuch](https://msdn.microsoft.com/library/azure/dn971511.aspx): stellt eine replizierte, transaktionale und asynchrone Auflistung von Schlüsselwertpaaren dar. Ähnlich wie bei `ConcurrentDictionary` können der Schlüssel und der Wert ein beliebiger Typ sein.
2. [Zuverlässige Warteschlange](https://msdn.microsoft.com/library/azure/dn971527.aspx): stellt eine replizierte, transaktionale und asynchrone strenge FIFO-Warteschlange (First-in-First-out) dar. Ähnlich wie bei `ConcurrentQueue` kann der Wert ein beliebiger Typ sein.

## Isolationsgrade
Der Isolationsgrad ist ein Maß für den erzielten Grad an Isolation. Isolation bedeutet, dass sich eine Transaktion wie in einem System verhält, das jeweils nur eine Transaktion erlaubt.

Zuverlässige Auflistungen wählen automatisch je nach Vorgang und Rolle des Replikats den Isolationsgrad für einen gegebenen Lesevorgang.

Es gibt zwei Isolationsstufen, die von zuverlässigen Auflistungen unterstützt werden:

- **Wiederholbarer Lesevorgang**: "Gibt an, dass Anweisungen keine Daten lesen können, die geändert wurden, für die aber noch kein Commit ausgeführt wurde. Darüber hinaus können von der aktuellen Transaktion gelesene Daten erst nach Abschluss dieser von anderen Transaktionen geändert werden. (https://msdn.microsoft.com/de-DE/library/ms173763.aspx)"
- **Momentaufnahme**: "Gibt an, dass von Anweisungen in einer Transaktion gelesene Daten der im Hinblick auf Transaktionen konsistenten Version der Daten entsprechen, die zu Beginn der Transaktion vorhanden waren." Die Transaktion kann nur Datenänderungen erkennen, die vor dem Start der Transaktion festgeschrieben wurden. Nach dem Start der aktuellen Transaktion von anderen Transaktionen vorgenommene Datenänderungen sind für Anweisungen, die in der aktuellen Transaktion ausgeführt werden, nicht sichtbar. Es erscheint daher, als ob die Anweisungen in einer Transaktion eine Momentaufnahme der festgeschriebenen Daten erhalten, die zu Beginn der Transaktion vorhanden waren. (https://msdn.microsoft.com/de-DE/library/ms173763.aspx)"

Das zuverlässige Wörterbuch und die zuverlässige Warteschlange unterstützen beide "Read Your Writes". Mit anderen Worten sind jegliche Schreibvorgänge innerhalb einer Transaktion für den nachfolgenden Lesevorgang sichtbar, wenn dieser derselben Transaktion angehört.

### Zuverlässiges Wörterbuch
| Vorgang\\Rolle | Primär | Sekundär |
| --------------------- | :--------------- | :--------------- |
| Lesevorgang für eine einzelne Entität | Wiederholbarer Lesevorgang | Momentaufnahme |
| Aufzählung\\Anzahl | Momentaufnahme | Momentaufnahme |

### Zuverlässige Warteschlange
| Vorgang\\Rolle | Primär | Sekundär |
| --------------------- | :--------------- | :--------------- |
| Lesevorgang für eine einzelne Entität | Momentaufnahme | Momentaufnahme |
| Aufzählung\\Anzahl | Momentaufnahme | Momentaufnahme |

## Persistenzmodell:
Der zuverlässige Zustands-Manager und die zuverlässigen Auflistungen basieren auf einem Persistenzmodell, das als Protokoll und Prüfpunkt bezeichnet wird. Bei diesem Modell wird jede Zustandsänderung auf dem Datenträger protokolliert und nur im Arbeitsspeicher angewendet. Der vollständige Zustand selbst wird nur gelegentlich persistent gespeichert (auch als Prüfpunkt bezeichnet). Der Vorteil ist:

- Deltas werden zur Leistungsverbesserung in sequenzielle Nur-Anhängen-Schreibvorgänge auf dem Datenträger umgewandelt.

Zum besseren Verständnis des Protokoll- und Prüfpunktmodells sehen wir uns zunächst das Szenario des Endlosdatenträgers an. Der zuverlässige Zustands-Manager protokolliert jeden Vorgang, bevor dieser repliziert wird. Dies ermöglicht es der zuverlässigen Auflistung, den Vorgang nur im Arbeitsspeicher anzuwenden. Da Protokolle persistent sind, verfügt der zuverlässige Zustands-Manager selbst dann, wenn das Replikat aufgrund eines Fehlers neu gestartet werden muss, über ausreichend Informationen in seinen Protokollen, um alle im Replikat verloren gegangenen Vorgänge zu wiederholen. Da es sich um einen Endlosdatenträger handelt, müssen Protokolleinträge nie entfernt werden. Die zuverlässige Auflistung verwaltet lediglich den Zustand im Speicher.

Sehen wir uns jetzt das Szenario mit begrenzten Datenträgern an. Irgendwann ist der gesamte Speicherplatz auf dem Datenträger des zuverlässigen Zustands-Managers belegt. Bevor dies geschieht, muss der zuverlässige Zustands-Manager sein Protokoll kürzen, um Platz für neuere Datensätze zu schaffen. Er fordert die zuverlässigen Auflistungen auf, ihren Zustand im Arbeitsspeicher zu prüfen. Es obliegt den zuverlässigen Auflistungen, den Zustand bis zu diesem Punkt persistent zu speichern. Sobald die zuverlässigen Auflistungen ihre Prüfpunkte abgeschlossen haben, kann der zuverlässige Zustands-Manager das Protokoll kürzen, um Speicherplatz freizugeben. Wenn das Replikat neu gestartet werden muss, stellen zuverlässige Auflistungen den Zustand ihres Prüfpunkts wieder her. Zudem stellt der zuverlässige Zustands-Manager alle seit dem Prüfpunkt vorgenommenen Zustandsänderungen wieder her.

## Sperren
In zuverlässigen Auflistungen bestehen alle Transaktionen aus zwei Phasen: Die von einer Transaktion angeforderten Sperren werden erst aufgehoben, wenn die Transaktion durch einen Abbruch oder Commit beendet wird.

Zuverlässige Auflistungen verwenden immer exklusive Sperren. Für Lesevorgänge hängt die Sperrung von verschiedenen Faktoren ab. Jegliche Lesevorgänge, die mithilfe der Momentaufnahmeisolierung ausgeführt wurden, sind frei von Sperren. Bei allen wiederholbaren Lesevorgängen werden standardmäßig freigegebene Sperren angewendet. Bei Lesevorgängen, die wiederholbare Lesevorgänge unterstützen, können Benutzer anstelle der freigegebenen Sperre eine Aktualisierungssperre anfordern. Eine Aktualisierungssperre ist eine asymmetrische Sperre, mit der eine häufig auftretende Form von Deadlock verhindert wird. Der Deadlock tritt auf, wenn mehrere Transaktionen Ressourcen für potenzielle Updates zu einem späteren Zeitpunkt sperren.

Die Kompatibilitätsmatrix für Sperren finden Sie unten:

| Anforderung\\Gewährt | Keine | Shared  
 | Aktualisieren | Exklusiv |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| Freigegeben | Kein Konflikt | Kein Konflikt | Konflikt: | Konflikt: |
| Aktualisieren | Kein Konflikt | Kein Konflikt | Konflikt: | Konflikt: |
| Exklusiv | Kein Konflikt | Konflikt: | Konflikt: | Konflikt: |

Hinweis: Zum Erkennen von Deadlocks werden in den APIs für zuverlässige Auflistungen Timeoutargumente verwendet. Angenommen, zwei Transaktionen (T1 und T2) versuchen, K1 zu lesen und zu aktualisieren. Beide können ein Deadlock durchführen, da im Endeffekt beide die freigegebene Sperre haben. In diesem Fall tritt bei einem oder beiden Vorgängen ein Timeout auf.

Das vorangegangene Deadlockszenario ist ein hervorragendes Beispiel, wie Aktualisierungssperren Deadlocks verhindern können.

## Recommendations

- **NICHT EMPFOHLEN**: Ändern Sie kein benutzerdefiniertes Objekt, das von Lesevorgängen zurückgegeben wurde (z. B. `TryPeekAsync` oder `TryGetAsync`). Zuverlässige Auflistungen geben ebenso wie gleichzeitige Auflistungen anstelle einer Kopie einen Verweis auf die Objekte zurück.
- **EMPFOHLEN**: Tiefenkopieren Sie zurückgegebene benutzerdefinierte Objekte, bevor Sie diese ändern. Da bei Strukturen und integrierten Typen eine Wertübergabe erfolgt, ist hier keine Tiefenkopie erforderlich.
- **NICHT EMPFOHLEN**: Verwenden Sie `TimeSpan.MaxValue` nicht für Timeouts. Timeouts sollten verwendet werden, um Deadlocks zu erkennen.
- **NICHT EMPFOHLEN**: Erstellen Sie keine Transaktion innerhalb der Anweisung `using` einer anderen Transaktion, da dies zu Deadlocks führen kann.

hier folgen einige Punkte, die es zu beachten gilt:

- Das Standardtimeout beträgt 4 Sekunden für alle APIs für zuverlässige Auflistungen. Die meisten Benutzer sollten diesen Wert nicht überschreiben.
- Das Standardabbruchtoken ist `CancellationToken.None` in allen APIs für zuverlässige Auflistungen.
- Aufzählungen haben innerhalb einer Auflistung konsistente Momentaufnahmen. Aufzählungen mehrerer Auflistungen sind jedoch nicht über Auflistungen hinweg konsistent.
- Zum Erreichen einer hohen Verfügbarkeit der zuverlässigen Auflistungen sollte jeder Dienst mindestens ein Ziel und eine Mindestgröße von 3 bei der Replikatgruppe haben.

## Nächste Schritte

- [Reliable Services – Schnellstart](service-fabric-reliable-services-quick-start.md)
- [Erste Schritte mit Web-API-Diensten von Service Fabric](service-fabric-reliable-services-communication-webapi.md)
- [Erweiterte Verwendung des Reliable Services-Programmiermodells](../Service-Fabric/service-fabric-reliable-services-advanced-usage.md)
- [Entwicklerreferenz für zuverlässige Auflistungen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

<!---HONumber=Oct15_HO3-->