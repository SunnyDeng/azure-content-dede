<properties
   pageTitle="Anleitung zur Überwachung und Diagnose | Microsoft Azure"
	description="Bewährte Methoden für die Überwachung verteilter Anwendungen in der Cloud."
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
	ms.date="04/28/2015"
	ms.author="masashin"/>

# Anleitung zur Überwachung und Diagnose

![](media/best-practices-monitoring/pnp-logo.png)

## Übersicht
Verteilte Anwendungen und Dienste in der Cloud sind in Ihrem Wesen komplexe Stück Software, die viele bewegliche Teile umfassen. In einer Produktionsumgebung ist es wichtig, dass nachzuvollziehen ist, auf welche Weise Benutzer Ihr System verwenden, die Ressourcennutzung nachzuverfolgen und allgemein die Integrität und Leistung des Systems zu überwachen. Diese Informationen dienen als Diagnosehilfe, um Probleme zu erkennen und zu korrigieren, sowie potentielle Probleme frühzeitig zu erkennen und sie zu verhindern.

## Überwachung- und Diagnoseszenarien
Die Überwachung gibt Ihnen einen Einblick darin, wie gut ein System funktioniert, und ist ein wichtiger Teil beim Erreichen von Quality-of-Service-Zielen. Einige häufige Szenarien für das Sammeln von Überwachungsdaten umfassen:

- Sicherstellen, dass das System fehlerfrei bleibt.
- Nachverfolgen der Verfügbarkeit des Systems und seiner Komponenten.
- Verwalten von Leistung, um sicherzustellen, dass sich der Durchsatz des Systems nicht unerwartet verschlechtert, während die Menge an Arbeit sich erhöht.
- Garantieren können, dass das System alle SLAs erfüllt werden, die mit dem Kunden vereinbart wurden.
- Schutz der Privatsphäre und der Sicherheit des Systems, der Benutzer und ihrer Daten.
- Verfolgen der Vorgänge, die zu Überwachungs- oder rechtlichen Zwecken ausgeführt werden.
- Überwachung der täglichen Nutzung des Systems und Hilfe bei er Ermittlung von Trends, die zu Problemen führen könnten, wenn sie nicht behandelt werden.
- Nachverfolgen von auftretenden Problemen, vom ersten Bericht bis zur Analyse der möglichen Ursachen, Berichtigung, folgende Softwareupdates und Bereitstellung.
- Ablaufverfolgung von Vorgängen und das Debuggen von Software-Versionen.

> [AZURE.NOTE]Diese Liste erhebt keinen Anspruch auf Vollständigkeit. Dieses Dokument behandelt diese Szenarien als häufigste Situationen bei der Überwachung, aber möglicherweise gibt es auch andere, weniger häufige oder für Ihre eigene Umgebung spezifische Szenarien.

In den folgenden Abschnitten werden diese Szenarien ausführlich beschrieben. Die Informationen für jedes Szenario werden im folgenden Format behandelt:

- Eine kurze Übersicht über das Szenario.
- Die typischen Anforderungen dieses Szenarios.
- Die unformatierten Instrumentationsdaten, die zur Unterstützung des Szenarios erforderlich sind, und mögliche Quellen für diese Informationen.
- Wie Rohdaten analysiert und kombiniert werden können, um sinnvolle Diagnoseinformationen zu generieren.

## Systemüberwachung
Ein System ist fehlerfrei, wenn es ausgeführt wird und Anforderungen verarbeiten kann. Die Systemüberwachung dient zum Generieren einer Momentaufnahme des aktuellen Zustands des Systems, damit Sie überprüfen können, ob alle Komponenten des Systems wie erwartet funktionieren.

### Anforderungen für die Systemüberwachung
Ein Operator sollte schnell (innerhalb weniger Sekunden) benachrichtigt werden, wenn ein Teil des Systems als fehlerhaft eingestuft wird. Der Operator sollte ermitteln können, welche Teile des Systems ordnungsgemäß ausgeführt werden und in welchen Teile Probleme auftreten. Die Systemintegrität kann mit einem Ampelsymbol hervorgehoben werden; Rot für fehlerhaft (das System wurde beendet), Gelb für teilweise fehlerfrei (System wird mit eingeschränkter Funktionalität ausgeführt), und Grün für völlig fehlerfrei.

Eine umfassende Systemüberwachung ermöglicht es einem Operator, der einen Drilldown durch das System zum Anzeigen des Integritätsstatus von Subsystemen und Komponenten ausführen. Wenn z. B. das gesamte System als teilweise fehlerfrei dargestellt ist, sollte der Operator in der Lage sein, es zu vergrößern und zu bestimmen, welche Funktionalität zurzeit nicht verfügbar ist.

### Datenquellen, Instrumentation und Anforderungen für die Datensammlung
Die Rohdaten, die zur Unterstützung der Systemüberwachung erforderlich sind, können als Ergebnis können als Ergebnis hiervon generiert werden:

- Nachverfolgen der Ausführung von Benutzeranforderungen. Diese Informationen können verwendet werden, um zu bestimmen, welche Anforderungen erfolgreich ausgeführt wurden, welche fehlgeschlagen sind und wie lange jede Anforderung dauert.
- Synthetische Benutzerüberwachung. Dieser Vorgang simuliert die Schritte, die von einem Benutzer durchgeführt werden, und folgt einer vordefinierten Reihe von Schritten. Die Ergebnisse der einzelnen Schritte sollten erfasst werden.
- Protokollieren von Ausnahmen, Fehlern und Warnungen. Diese Informationen können als Ergebnis von Trace-Anweisungen erfasst werden, die im Anwendungscode eingebettet sind, sowie vom Abrufen von Informationen aus den Ereignisprotokollen der Dienste, auf die das System verweist.
- Überwachen des Zustands von Drittanbieterdiensten, die vom System verwendet werden. Dies erfordert möglicherweise das Abrufen und Analysieren von Zustandsdaten, die von diesen Diensten bereitgestellt werden. Diese Informationen können in einer Vielzahl von Formaten vorliegen.
- Endpunktüberwachung. Dieser Mechanismus wird im Abschnitt für die Verfügbarkeitsüberwachung ausführlicher beschrieben.
- Sammeln von umgebenden Leistungsinformationen, z. B. CPU-Auslastung im Hintergrund oder E/A-Aktivität (einschließlich Netzwerk).

### Analysieren von Zustandsdaten
Die Systemüberwachung soll hauptsächlich rasch angeben können, ob das System ausgeführt wird. Eine sofortige Analyse der heißen Daten kann eine Warnung auslösen, wenn eine kritische Komponente als fehlerhaft erkannt wird (keine Reaktion auf aufeinanderfolgende Pings z. B.). Der Operator kann dann die entsprechenden Korrekturmaßnahmen einleiten.

Ein erweitertes System enthält möglicherweise ein vorausschauendes Element für eine kalte Analyse für kürzliche und aktuelle Workloads, um Trends zu erkennen und zu bestimmen, ob das System wahrscheinlich fehlerfrei bleibt oder ob zusätzliche Ressourcen erforderlich sind. Dieses vorausschauende Element sollte auf wichtigen Leistungsmetriken basieren, wie z. B. der Anforderungsrate an jeden Dienst oder ein Subsystem, die Antwortzeiten dieser Anforderungen und die Menge des Datenflusses in und aus jedem Dienst. Wenn der Wert einer Metrik einen festgelegten Schwellenwert überschreitet, kann das System eine Warnung auslösen, um einen Operator oder eine automatische Skalierung (falls verfügbar) zu aktivieren und vorbeugende Maßnahmen für das Aufrechterhalten der Systemintegrität zu ergreifen. Diese Aktionen beinhalten möglicherweise das Hinzufügen von Ressourcen, den Neustart eines oder mehrerer fehlschlagender Dienste oder die Drosselung auf Anforderungen mit niedrigerer Priorität.

## Verfügbarkeitsüberwachung
Ein wirklich fehlerfreies System erfordert, dass die Komponenten und Subsysteme, aus denen das System besteht, verfügbar sind. Die Verfügbarkeitsüberwachung ist eng mit der Systemüberwachung verknüpft, aber während die Systemüberwachung eine sofortige Ansicht des aktuellen Zustands des Systems zur Verfügung stellt, betrifft Erstere das Nachverfolgen der Verfügbarkeit des Systems und seiner Komponenten zum Generieren von Statistiken für die Betriebszeit des Systems.

In vielen Systemen sind einige Komponenten (z. B. eine Datenbank) mit integrierten Redundanz konfiguriert, um schnelles Failover im Falle eines schwerwiegenden Fehlers oder eines Verlusts der Verbindung zuzulassen. Im Idealfall sollten die Benutzer nicht mitbekommen, dass solch ein Fehler passiert ist. Aus der Perspektive der Verfügbarkeitsüberwachung ist es jedoch notwendig, so viele Informationen wie möglich über solche Fehler zu sammeln, um die Ursache zu bestimmen und Korrekturmaßnahmen zu ergreifen, damit sie sich nicht wiederholen.

Die Daten, die zum Nachverfolgen der Verfügbarkeit erforderlich sind, hängen möglicherweise von einer Reihe an Faktoren auf niedrigerer Ebene ab. Viele davon können spezifisch für die Anwendung, das System und die Umgebung sein. Ein effektives Überwachungssystem erfasst die Verfügbarkeitsdaten, die diesen Faktoren auf niedriger Ebene entsprechen, und aggregiert diese anschließend, um einen allgemeinen Überblick über das System zu erhalten. Beispielsweise können in einem e-Commerce-System die Geschäftsfunktionen, mit denen der Kunde Bestellungen aufgeben kann, vom Repository abhängen, in dem Bestelldetails gespeichert sind, und vom Zahlungssystem, das die finanziellen Transaktionen für die Zahlung dieser Aufträge verarbeitet. Die Verfügbarkeit des Systemteils, der für die Platzierung von Bestellungen verantwortlich ist, ist daher eine Funktion der Verfügbarkeit des Repositorys und der Zahlungs-Teilsystems.

### Anforderungen für die Verfügbarkeitsüberwachung
Ein Operator sollte auch die historische Verfügbarkeit jedes Systems und Subsystems anzeigen können und mit diesen Informationen Trends erkennen, die in einem oder mehreren Subsystemen in regelmäßigen Abständen Fehler verursachen (schlagen Dienste zu einer bestimmten Tageszeit fehl, die Spitzenzeiten bei der Bearbeitung entspricht?).

Außerdem bietet er eine unmittelbare und historische Ansicht der Verfügbarkeit oder andernfalls jedes Subsystems, eine Überwachungslösung sollte einen Operator schnell warnen können, wenn ein oder mehr Dienste fehlschlagen oder Benutzer keine Verbindung zu Diensten herstellen können. Dies ist nicht einfach eine Frage der Überwachung eines jeden Dienstes, sondern auch der Untersuchung der Aktionen, die von jedem Benutzer ausgeführt werden, wenn diese Aktionen beim Versuch fehlschlagen, mit einem Dienst zu kommunizieren. Bis zu einem gewissen Grad sind Verbindungsfehler normal und werden möglicherweise von vorübergehenden Fehlern ausgelöst, aber es kann hilfreich sein, dass das System eine Warnung ausgibt mit der Anzahl der Verbindungsfehler auf ein festgelegtes Subsystem während eines bestimmten Zeitraums.

### Datenquellen, Instrumentation und Anforderungen für die Datensammlung
Wie bei der Überwachung der Integrität können die Rohdaten zur Unterstützung der Verfügbarkeitsüberwachung als Ergebnis synthetischer Benutzerüberwachung generiert werden und alle Ausnahmen, Fehler und Warnungen protokollieren, die auftreten können. Darüber hinaus können Verfügbarkeitsdaten durch mithilfe einer Endpunkt-Überwachung abgerufen werden. Die Anwendung kann einen oder mehrere Integritätsendpunkte offenlegen, von dem jeder den Zugriff auf den Funktionsbereich innerhalb des Systems prüft. Das Überwachungssystem kann jeden Endpunkt nach einem definierten Zeitplan pingen und die Ergebnisse erfassen (erfolgreich oder fehlgeschlagen).

Alle Timeouts und Fehler der Netzwerkkonnektivität sowie Wiederholungsversuche beim Verbinden müssen aufgezeichnet werden. Alle Daten sollten mit einem Zeitstempel versehen sein.

<a name="analyzing-availablity-data"></a>
### Analysieren von Verfügbarkeitsdaten
Die Instrumentationsdaten müssen zusammengefasst und korreliert werden, um die folgenden Arten der Analyse zu unterstützen:

- Die sofortige Verfügbarkeit des Systems und der Subsysteme.
- Die Fehlerraten für die Verfügbarkeit des Systems und der Subsysteme. Im Idealfall sollte ein Operator Fehler bei bestimmten Aktivitäten in Beziehung setzen können: Was ist passiert, als das System fehlschlug?
- Eine Verlaufsansicht mit den Fehlerraten des Systems oder der Subsysteme in einem angegeben Zeitraum und das Laden auf dem System (z. B. Anzahl der Benutzeranforderungen), wenn ein Fehler aufgetreten ist.
- Die Gründe für die Nichtverfügbarkeit des Systems oder eines Subsystems. Wenn beispielsweise ein Dienst nicht ausgeführt wird, bei Verlust der Konnektivität, wenn ein Timeout trotz Verbindung auftritt und zurückgegebene Fehler trotz Verbindung.

Sie können den Prozentsatz der Verfügbarkeit eines Diensts über einen Zeitraum mit folgender Formel berechnen:

```
%Availability =  ((Total Time – Total Downtime) / Total Time ) * 100
```

Dies ist nützlich für die Vereinbarung zum Servicelevel ([SLA-Überwachung](#SLA-monitoring) wird weiter unten in diesem Handbuch ausführlicher beschrieben). Die Definition der _Ausfallzeiten_ hängt vom Dienst ab. Visual Studio Online definiert Ausfallzeiten z. B. als den Zeitraum, in dem ein Kunde mit dem Dienst länger als 120 Sekunden eine Verbindung herstellen möchte und alle grundlegenden Lese- und Schreibvorgänge fehlschlagen, nachdem das Herstellen der Verbindung innerhalb dieses Zeitraums erfolgreich war.

## Leistungsüberwachung
Während das System von mehr und mehr Benutzern und der Größe der Datensätze, auf die diese Benutzer verstärkt zugreifen können, unter Belastung steht, wird ein mögliches Fehlschlagen einer oder mehrerer Komponenten wahrscheinlich. Häufig ist eine Abnahme der Leistung Komponentenfehlern vorangestellt. Wenn Sie in der Lage sind, eine solche Leistungsabnahme zu erkennen, können Sie proaktive Schritte zur Abhilfe ergreifen.

Die Systemleistung ist von einer Reihe von Faktoren abhängig. Jeder dieser Faktoren wird in der Regel mithilfe von Key Performance Indicators (KPIs) gemessen, z. B. die Anzahl der Datenbanktransaktionen pro Sekunde oder die Menge der Netzwerkanforderungen, die in einem bestimmten Zeitrahmen erfolgreich verwaltet werden. KPIs können als spezifische Leistungskennzahlen zur Verfügung stehen oder aus einer Kombination von Metriken abgeleitet werden.

> [AZURE.NOTE]Das Bestimmen einer schlechten oder guten Leistung erfordert, dass Sie das Leistungsniveau verstehen, mit der das System betrieben werden können sollte. Dies erfordert die Beobachtung des Systems, während es bei einer durchschnittlichen Belastung arbeitet, und das Erfassen der Daten für jeden KPI über einen Zeitraum. Dabei wird z. B. die Auslastung des laufenden Systems in einer Testumgebung simuliert und die entsprechenden Daten gesammelt, und zwar vor der Bereitstellung des Systems in einer Produktionsumgebung.

> Sie sollten auch sicherstellen, dass die Überwachung zu Leistungszwecken keine ungerechtfertigte Belastung für das System wird. Sie können möglicherweise die Detailebene für die in der Leistungsüberwachung gesammelten Daten dynamisch anpassen.

### Anforderungen für die Leistungsüberwachung
Um die Systemleistung zu untersuchen, muss ein Operator in der Regel Informationen wie diese erhalten:

- Die Antwortrate bei Benutzeranforderungen.
- Die Anzahl gleichzeitiger Benutzeranfragen.
- Das Volumen des Netzwerkverkehrs.
- Die Sätze, zu denen geschäftliche Transaktionen vorgenommen werden.
- Die durchschnittliche Verarbeitungszeit für Anforderungen.

Es kann auch hilfreich sein, Tools zur Verfügung zu stellen, die einem Operator das Erkennen von Korrelationen ermöglichen, z. B.:

- Die Anzahl der gleichzeitigen Benutzer im Vergleich zu den Anforderungs-Latenzzeiten (wie lange es bis zum Starten der Anforderungsverarbeitung dauert, nachdem der Benutzer sie gesendet hat).
- Die Anzahl der gleichzeitigen Benutzer im Vergleich zu der durchschnittlichen Antwortzeit (wie lange es bis zum Abschluss einer Anforderung dauert, nachdem die Verarbeitung begonnen hat).
- Das Anforderungsvolumen im Vergleich zur Anzahl von Fehlern bei der Verarbeitung.

Neben diesen funktionalen High-Level-Informationen sollte ein Operator auch eine detaillierte Ansicht der Leistung für die einzelnen Komponenten im System erhalten. Diese Daten werden in der Regel mithilfe von Low-Level-Leistungsindikatoren wie Überwachungsinformationen bereitgestellt:

- Speicherauslastung,
- Anzahl der Threads,
- CPU-Verarbeitungszeit,
- Abrufen der Warteschlangenlänge,
- Datenträger- oder Netzwerk-E/A-Raten und -Fehler,
- Anzahl der Bytes, die gelesen oder geschrieben werden,
- Middleware-Indikatoren, z. B. die Warteschlangenlänge.

Bei allen Visualisierungen sollte ein Operator einen Zeitraum angeben können. Die angezeigten Daten können eine Momentaufnahme der aktuellen Situation und/oder eine Verlaufsansicht der Leistung sein.

Ein Operator sollte eine Warnung für jedes Leistungsmaß und für jeden bestimmten Wert während jeden angegebenen Zeitintervalls ausgeben können.

### Datenquellen, Instrumentation und Anforderungen für die Datensammlung
Die Leistungsdaten der höheren Ebene (Durchsatz, Anzahl der gleichzeitigen Benutzer, Anzahl der Geschäftstransaktionen, Fehlerraten und so weiter) können durch Überwachen der Fortschritte von eingehenden und das System passierenden Benutzeranforderungen gesammelt werden. Dies beinhaltet das Einbinden von Ablaufverfolgungsanweisungen an wichtigen Punkten im Anwendungscode zusammen mit Informationen zur zeitlichen Steuerung. Alle Fehler, Ausnahmen und Warnungen sollten mit genügend Daten erfasst werden, damit sie mit den Anforderungen korreliert werden können, die sie verursacht haben. Das IIS-Protokoll ist eine andere nützliche Datenquelle.

Wenn möglich, sollten Sie Leistungsdaten für alle externen Systeme erfassen, die die Anwendung verwendet. Diese externen Systeme bieten möglicherweise eigene Leistungsindikatoren oder andere Features für die Anforderung von Leistungsdaten. Wenn dies nicht möglich ist, sollten Sie z. B. die Start- und Endzeit der einzelnen Anforderung an ein externes System erfassen, zusammen mit dem Status des Vorgangs (Erfolg, Fehler oder Warnung). Beispielsweise können Sie eine Stoppuhr-Vorgehensweise für Zeitanforderungen anwenden. Starten Sie einen Zeitgeber, wenn die Anforderung gestartet wird, und beenden Sie den Zeitgeber, wenn die Anforderung abgeschlossen ist.

Low-Level-Leistungsdaten für einzelne Komponenten in einem System sind möglicherweise über Features wie Windows-Leistungsindikatoren und Azure-Diagnose verfügbar.

### Analysieren von Leistungsdaten
Ein Großteil der Analysearbeit besteht aus dem Aggregieren von Leistungsdaten nach Benutzeranforderungstyp (z. B. Hinzufügen eines Elements zu einem Einkaufswagen oder Ausführen des Auscheckvorgangs in einem e-Commerce-System) und/oder nach Subsystem oder Dienst, an das/den jede Anforderung gesendet wird.

Eine weitere häufige Anforderung ist die Zusammenfassung von Leistungsdaten in ausgewählten Prozentsätzen. Beispielsweise das Ermitteln der Antwortzeiten für 99 % der Anforderungen, 95 % der Anforderungen und 70 % der Anforderungen. Möglicherweise sind SLA-Ziele oder andere Ziele für jeden Prozentwert festgelegt. Fortlaufende Ergebnisse sollten in beiden nahezu in Echtzeit angegeben werden, um Probleme sofort zu erkennen, sowie über die längere Zeit für statistische Zwecke aggregiert werden.

Im Fall von leistungsbeeinträchtigenden Latenzproblemen sollte ein Operator schnell in der Lage sein, die Ursache für den Engpass zu identifizieren, indem die Latenzzeit der einzelnen Schritte jeder Anforderung untersucht wird. Die Leistungsdaten müssen daher ein Mittel zum Korrelieren von Leistungsindikatoren für jeden Schritt bereitstellen, um sie mit einer bestimmten Anforderung zu verknüpfen.

Je nach den Anforderungen der Visualisierung kann es hilfreich sein, einen Datencube mit Ansichten der Rohdaten zu generieren und zu speichern, um komplexe Ad-hoc-Abfragen und die Analyse von Leistungsdaten zu ermöglichen.

## Sicherheitsüberwachung
Alle kommerziellen Systeme, die sensible Daten enthalten, müssen eine Sicherheitsstruktur implementieren. Die Komplexität des Sicherheitsmechanismus ist in der Regel eine Funktion der Sensibilität der Daten. In Systemen, in denen Benutzer authentifiziert werden müssen, sollten Sie alle Anmeldeversuche aufzeichnen, fehlgeschlagene und erfolgreiche. Darüber hinaus sollten alle ausgeführten Vorgänge und die Details aller Ressourcen, auf die ein authentifizierter Benutzer zugreift, protokolliert werden. Wenn der Benutzer die Sitzung beendet und sich abmeldet, sollten diese Informationen ebenfalls gespeichert werden.

Die Überwachung kann möglicherweise helfen, Angriffe auf das System zu erkennen. Z. B. könnte eine große Anzahl fehlgeschlagener Anmeldeversuche möglicherweise auf einen Brute-Force-Angriff hindeuten und ein unerwarteter Anstieg der Anforderungen auf DDoS-Angriffe zurückzuführen sein. Sie müssen darauf vorbereitet sein, alle Anforderungen für alle Ressourcen unabhängig von der Quelle dieser Anforderungen zu überwachen; ein System mit einer Sicherheitsanfälligkeit bei der Anmeldung stellt möglicherweise versehentlich nach außen Ressourcen bereit,für die ein Benutzer sich nicht anmelden muss.

### Anforderungen für die Sicherheitsüberwachung
Die wichtigsten Aspekte der Sicherheitsüberwachung sollten es einem Operator ermöglichen, schnell:

- versuchte Angriffe von einer nicht authentifizierten Entität zu erkennen,
- Versuche von Entitäten zu identifizieren, Operationen für Daten auszuführen, für die kein Zugriff erteilt wurde,
- zu bestimmen, ob das System oder ein Teil des Systems von außen oder innen angegriffen wird (z. B. könnte ein böswilliger authentifizierter Benutzer möglicherweise das System abstürzen lassen wollen).

Um diese Anforderungen zu unterstützen, sollte ein Operator über Folgendes benachrichtigt werden:

- Fehler bei wiederholten Anmeldeversuchen vom gleichen Konto innerhalb eines angegebenen Zeitraums aus.
- Wenn das gleiche authentifizierte Konto wiederholt versucht, auf eine verbotene Ressource während eines angegebenen Zeitraums zuzugreifen.
- Wenn eine große Anzahl nicht authentifizierter oder nicht autorisierter Anforderungen während eines angegebenen Zeitraums auftreten.

Die Informationen für einen Operator sollten die Adresse des Quellhosts für jede Anforderung beinhalten. Wenn Sicherheitsverletzungen regelmäßig aus einem bestimmten Bereich von Adressen auftreten, könnten diese Hosts blockiert werden.

Eine wichtige Rolle bei der Wahrung der Sicherheit eines Systems ist das schnelle Erkennen von Aktionen, die vom üblichen Muster abweichen. Informationen wie z. B. die Anzahl der fehlgeschlagenen und/oder erfolgreichen Anforderungen können visuell angezeigt werden, um zu erkennen, ob eine Spitze bei der Aktivität zu einer ungewöhnlichen Zeit festzustellen ist (z. B. Benutzeranmeldung um 3 Uhr nachts und Ausführung einer große Anzahl von Vorgängen, wenn der Arbeitstag erst um 9 Uhr beginnt). Diese Informationen kann auch dazu verwendet werden, um eine zeitbasierte automatische Skalierung zu konfigurieren. Wenn z. B. ein Operator beobachtet, dass sich eine große Anzahl von Benutzern regelmäßig zu einer bestimmten Tageszeit anmeldet, kann er zusätzliche Authentifizierungsdienste bereitstellen, um das Volumen der Arbeit bearbeiten zu können. Er kann diese zusätzlichen Dienste wieder deaktivieren, wenn die Spitze vorbei ist.

### Datenquellen, Instrumentation und Anforderungen für die Datensammlung
Sicherheit ist ein allumfassender Aspekt der meisten verteilten Systeme, und die relevanten Daten werden wahrscheinlich an mehreren Punkten in einem System generiert. Sie sollten die Übernahme eines Ansatzes zu Sicherheitsinformationen und Ereignismanagement (SIEM) erwägen, um die Sicherheitsinformationen aus Ereignissen zu sammeln, die von der Anwendung, Netzwerkgeräten, Servern, Firewalls, Antivirus-Software und anderen Elemente des Eindringschutzes ausgelöst wurden.

Die Sicherheitsüberwachung kann Daten aus Tools integrieren, die nicht Teil der Anwendung sind, wie z. B. Dienstprogramme, die Port-Scan-Aktivitäten durch externe Einrichtungen erkennen, oder Netzwerkfilter, die Zugriffsversuche für nicht authentifizierten Zugriff auf die Anwendung und Daten erkennen.

In allen Fällen müssen die erfassten Daten einem Administrator ermöglichen, die Art des Angriffs zu bestimmen und die entsprechende Gegenmaßnahme einzuleiten.

### Analysieren von Sicherheitsdaten
Ein Feature der Sicherheitsüberwachung ist die Vielzahl von Quellen, aus denen die Daten entstehen. Die verschiedenen Formate und Detailebenen erfordern häufig eine komplexe Analyse der erfassten Daten, um sie zu einem Thread zusammenhängender Informationen zusammenzufassen. Abgesehen von den einfachsten Fällen (z. B. dem Erkennen einer große Anzahl fehlgeschlagener Anmeldungen oder wiederholter Versuche für nicht autorisierten Zugriff auf wichtige Ressourcen), ist ein komplexes automatisiertes Verarbeiten der Sicherheitsdaten eventuell nicht möglich, und stattdessen ist es vorzuziehen, diese Daten mit einem Zeitstempel (ansonsten aber in seiner ursprünglichen Form) in ein sicheres Repository zu schreiben, um eine manuelle Expertenanalyse sicherzustellen.

<a name="SLA-monitoring"></a>

## SLA-Überwachung
Viele kommerzielle Systeme, die zahlende Kunden unterstützen, bieten Garantien in Bezug auf die Leistung des Systems in Form von SLAs. Im Wesentlichen erklären SLAs, dass das System eine definierte Menge Arbeit innerhalb eines vereinbarten Zeitrahmens und ohne Verlust wichtiger Informationen verarbeiten kann. Die SLA-Überwachung soll sicherstellen, dass das System messbare SLAs erfüllen kann.

> [AZURE.NOTE]Die SLA-Überwachung ist eng mit der Leistungsüberwachung verbunden, wobei die Leistungsüberwachung sicherstellen soll, dass das System _optimal_ funktioniert, die SLA-Überwachung dagegen einer vertraglichen Verpflichtung unterliegt, die definiert, was _optimal_ eigentlich bedeutet.

SLAs werden häufig wie folgt definiert:

- Allgemeine Systemverfügbarkeit Beispielsweise kann eine Organisation garantieren, dass das System zu 99,9 % der Zeit verfügbar sind; Dies entspricht nicht mehr als 9 Stunden Ausfallzeit pro Jahr oder ungefähr 10 Minuten pro Woche.
- Operativer Durchsatz. Dieser Aspekt wird oft als ein oder mehrere Hochwassermarken genannt z. B. die Garantie, dass das System bis zu 100.000 Benutzeranfragen gleichzeitig unterstützt oder 10.000 Geschäftstransaktionen gleichzeitig vonstatten gehen können.
- Operative Reaktionszeit. Das System kann auch Garantien hinsichtlich der Rate geben, mit der Anforderungen verarbeitet werden, wie z. B. 99 % aller Geschäftstransaktionen werden innerhalb von 2 Sekunden abgeschlossen und keine einzelne Transaktion dauert länger als 10 Sekunden.

> [AZURE.NOTE]Einige Verträge für kommerzielle Systeme können auch SLAs über Customer Support beinhalten, z. B. auf alle Helpdeskanfragen wird innerhalb von fünf Minuten eine Antwort gesendet und 99 % aller Probleme werden vollständig innerhalb eines Tages bearbeitet. Effektive [Problemverfolgung](#issue-tracking) (weiter unten in diesem Abschnitt beschrieben) ist der Schlüssel für die Einhaltung von SLAs wie diesen.

### Anforderungen für die SLA-Überwachung
Auf der höchsten Ebene sollte ein Operator auf einen Blick feststellen können, ob das System die vereinbarten SLAs erfüllt oder nicht, und falls nicht, die zugrunde liegenden Faktoren näher untersuchen, um die Gründe für eine unter dem Standard liegende Leistung zu ermitteln.

Typische Indikatoren höherer Ebene, die visuell dargestellt werden können, umfassen:

- Der Prozentsatz der Betriebszeit des Dienstes.
- Der Anwendungsdurchsatz (gemessen in Bezug auf erfolgreiche Transaktionen und/oder Vorgänge pro Sekunde).
- Die Anzahl der Anforderungen für die erfolgreiche/fehlgeschlagene Anwendung.
- Die Anzahl der Anwendungs- und Systemfehler, Ausnahmen und Warnungen.

All diese Indikatoren sollten nach einem bestimmten Zeitraum gefiltert werden können.

Eine Cloud-Anwendung umfasst wahrscheinlich eine Anzahl von Subsystemen und Komponenten. Ein Operator sollte einen High-Level-Indikator auswählen und sehen können, wie er aus der Integrität der zugrunde liegenden Elementen zusammengesetzt ist. Wenn z. B. die Verfügbarkeit des gesamten Systems einen zulässigen Wert unterschreitet, sollte ein Operator in der Lage sein, ihn zu vergrößern und zu bestimmen, welche Elemente zu diesem Fehler beitragen.

> [AZURE.NOTE]Die Verfügbarkeit der Systeme muss sorgfältig definiert werden. In Systemen, die Redundanz verwenden, um eine maximale Verfügbarkeit zu gewährleisten, können einzelne Instanzen der Elemente fehlschlagen, aber das System bleibt dennoch funktionsfähig. Die durch die Systemüberwachung dargestellte Systemverfügbarkeit sollte die aggregierte Betriebszeit von jedem Element angeben und nicht unbedingt, ob das System tatsächlich angehalten wurde. Darüber kommen Fehler möglicherweise isoliert vor. Auch, wenn ein bestimmtes System nicht verfügbar ist, kann der Rest des Systems verfügbar bleiben, wenn auch mit eingeschränkter Funktionalität (in einem e-Commerce-System kann ein Fehler im System verhindern, dass ein Kunde Bestellungen aufgibt, aber der Kunde kann möglicherweise immer noch den Produktkatalog durchsuchen.)

Zu Warnzwecken sollte das System möglicherweise ein Ereignis auslösen, wenn einer der allgemeinen Indikatoren den angegebenen Schwellenwert überschreitet. Die Low-Level-Details der verschiedenen Faktoren, die die Anzeige auf hoher Ebene umfassen, sollten als Kontextdaten für das Warnsystem verfügbar sein.

### Datenquellen, Instrumentation und Anforderungen für die Datensammlung
Die Rohdaten, die zur Unterstützung der SLA-Überwachung erforderlich sind, sind ähnlich wie die bei der Leistungsüberwachung zusammen mit einigen Aspekten der Verfügbarkeits- und Integritätsüberwachung (siehe die betreffenden Abschnitte für weitere Informationen). Sie können diese Daten erfassen durch:

- Durchführen von Endpunkt-Überwachung
- Protokollieren von Ausnahmen, Fehlern und Warnungen.
- Nachverfolgen der Ausführung von Benutzeranforderungen.
- Überwachen der Verfügbarkeit von Drittanbieterdiensten, die vom System verwendet werden.
- Verwenden von Leistungsmetriken und -indikatoren.

Alle Daten müssen zeitlich festgelegt und mit einem Zeitstempel versehen werden.

### Analysieren von SLA-Daten
Die Instrumentationsdaten müssen aggregiert werden, um einen Überblick über die Gesamtleistung des Systems zu generieren und Detailinformationen zum Aktivieren der Prüfung der Leistung der zugrunde liegenden Subsysteme zu unterstützen. Sie sollten z. B. in der Lage sein:

- Die Gesamtzahl der Benutzeranforderungen in einem bestimmten Zeitraum zu berechnen und die Erfolgs- und Fehlerereignisrate dieser Anforderungen zu bestimmen.
- Die Antwortzeiten von Benutzeranforderungen zu kombinieren, um einen allgemeinen Überblick über die Reaktionszeiten zu generieren.
- Den Status der Benutzeranforderungen zu analysieren, die gesamte Antwortzeit einer angegebenen Anforderung nach unten in die Reaktionszeiten der einzelnen Arbeitsaufgaben in dieser Anforderung zu brechen.  
- Die Gesamtverfügbarkeit des Systems als eine Betriebszeit in Prozent für einen bestimmten Zeitraum zu bestimmen.
- Die zeitliche Verfügbarkeit in Prozent für jede einzelne Komponenten und jeden einzelnen Dienst im System zu analysieren. Das kann bedeuten, von Drittanbieter-Diensten generierte Protokolle zu analysieren.

Viele kommerzielle Systeme sind erforderlich, um reale Leistungsangaben gegen vereinbarte SLAs für einen bestimmten Zeitraum zu melden, in der Regel einen Monat. Diese Informationen können verwendet werden, um Guthaben oder andere Formen der Rückzahlung für Kunden zu berechnen, wenn während dieses Zeitraums die SLAs nicht erfüllt werden. Sie können die Verfügbarkeit für einen Dienst berechnen, indem Sie die im Abschnitt [Analysieren von Verfügbarkeitsdaten](#analyzing-availability-data) beschriebene Technik verwenden.

Für interne Zwecke kann eine Organisation auch die Anzahl und Art der Vorfälle nachverfolgen, die die Ursache für das Fehlschlagen von Diensten waren. Erfahren Sie, wie Sie diese Probleme schnell beheben oder vollständig beseitigen. Dies trägt zur Reduzierung von Ausfallzeiten bei und dient der Erfüllung der SLAs.

## Überwachung
Je nach Art der Anwendung gibt es möglicherweise gesetzliche Bestimmungen, die Anforderungen für die Überwachung von Operationen festlegen, die von Benutzern durchgeführt werden, und für das Aufzeichnen des Datenzugriffs. Die Überwachung kann nachweisen, dass Kunden mit bestimmten Anforderungen verknüpft sind; Nichtabstreitbarkeit ist ein wichtiger Faktor bei vielen e-Business-Systemen zur Erhaltung der Vertrauensstellung zwischen einem Kunden und der Organisation, die für die Anwendung oder den Dienst zuständig ist.

### Anforderungen für das Überwachen
Analysten müssen in der Lage sein, die Abfolge der von den Benutzern durchgeführten Geschäftsvorgänge zu verfolgen, damit Sie die Aktionen des Benutzers nachvollziehen können. Dies kann einfach eine Frage der Aufzeichnungen sein oder als Teil einer forensische Untersuchung.

Überwachungsinformationen sind sehr empfindlich, da wahrscheinlich Daten eingeschlossen werden, die die Benutzer des Systems sowie die Aufgaben identifizieren, die sie ausführen. Aus diesem Grund ist es sehr wahrscheinlich, dass die Überwachungsinformationen in Form von Berichten visualisiert werden, die nur für vertrauenswürdige Analysten verfügbar sind, anstatt ein interaktives System zu verwenden, das grafische Drilldown-Vorgänge unterstützt. Ein Analyst sollte eine Zahl von Berichten generieren können, z. B. eine Auflistung aller Benutzeraktivitäten während eines angegebenen Zeitraums, eine Beschreibung der Abfolge der Aktivitäten für einen einzelnen Benutzer oder die Auflistung der aufeinanderfolgenden Operationen für eine oder mehrere Ressourcen.

### Datenquellen, Instrumentation und Anforderungen für die Datensammlung
Die primären Informationsquellen für die Überwachung können umfassen:

- Das Sicherheitssystem, das die Benutzerauthentifizierung verwaltet.
- Ablaufverfolgungsprotokolle zur Aufzeichnung von Benutzeraktivitäten.
- Sicherheitsprotokolle zur Nachverfolgung aller identifizierbarer und nicht identifizierbarer Netzwerkanforderungen.

Das Format für die Überwachungsdaten und die Form, in der sie gespeichert sind, unterliegt möglicherweise gesetzlichen Anforderungen. Angenommen, es ist nicht möglich, die Daten irgendwie zu säubern (im ursprünglichen Format aufgezeichnet), und der Zugriff auf das Repository, in dem es gespeichert wird, muss zum Verhindern von Manipulationen geschützt werden.

### Analysieren von Überwachungsdaten
Analysten müssen auf die Rohdaten in ihrer Gesamtheit und ursprünglichen Form zugreifen können. Abgesehen von der Anforderung zum Generieren von allgemeinen Überwachungsberichten, werden die Tools zum Analysieren der Daten wahrscheinlich spezialisiert und außerhalb des Systems gehalten.

## Nutzungsüberwachung
Die Nutzungsüberwachung verfolgt, wie die Features und Komponenten einer Anwendung verwendet werden. Die gesammelten Daten können verwendet werden, um:

- zu bestimmen, welche Funktionen sehr häufig verwendet werden und um alle potenziellen Hotspots im System zu erkennen. Elemente mit hohem Datenverkehr könnten von funktioneller Partitionierung oder sogar der Replikation profitieren, um die Last gleichmäßig zu verteilen. Mit diesen Informationen kann auch bestimmt werden, welche Funktionen selten verwendet werden und mögliche Kandidaten für die Stilllegung sind oder in einer zukünftigen Version des Systems ersetzt werden können.
- Informationen über die operationellen Ereignisse im System unter normalen Gebrauch zu erhalten. Bei einer e-Commerce-Website könnten Sie z. B. die statistischen Informationen über die Anzahl der Transaktionen und die Menge an Kunden aufzeichnen, die dafür verantwortlich sind. Diese Informationen könnten zur Kapazitätsplanung mit steigender Anzahl von Kunden verwendet werden.
- die Benutzerzufriedenheit mit der Leistung oder der Funktionalität des Systems zu erkennen (möglicherweise indirekt). Wenn eine große Anzahl von Kunden in einem e-Commerce-System regelmäßig ihre Einkaufswagen verwirft, könnte dies z. B. wegen eines Problems mit der Checkout-Funktionalität sein.
- Generieren Sie Abrechnungsinformationen. Eine kommerzielle Anwendung oder ein Dienst mit mehreren Mandanten kann Kunden die verwendeten Ressourcen in Rechnung stellen.
- Erzwingen von Kontingenten. Wenn ein Benutzer in einem System mit mehreren Mandanten das kostenpflichtige Kontingent an Verarbeitungszeit oder Ressourcenverbrauch innerhalb eines bestimmten Zeitraums überschreitet, kann der Zugriff begrenzt oder die Verarbeitung gedrosselt werden.

### Anforderungen für die Nutzungsüberwachung
Um die Systemleistung zu untersuchen, muss ein Operator in der Regel Informationen wie diese erhalten:

- Die Anzahl der Anforderungen, die von jedem Subsystem verarbeitet und an jede Ressource verwiesen wird.
- Die Arbeit, die von jedem Benutzer durchgeführt wird.
- Die Größe des Datenspeichers, der von jedem Benutzer belegt wird.
- Die Ressourcen, auf die jeder einzelne Benutzer zugreift.

Ein Operator sollte auch Diagramme generieren können, z. B. zur Anzeige von Benutzern, die am meisten Ressourcen verschlingen, oder von den am häufigsten verwendeten Ressourcen.

### Datenquellen, Instrumentation und Anforderungen für die Datensammlung
Die Nachverfolgung der Nutzung kann auf einer relativ hohen Ebene durchgeführt werden, wobei Anfangs- und Endzeit jeder Anforderung und die Art der Anforderung erfasst werden (Lesen, Schreiben usw., abhängig von der betreffenden Ressource). Sie erhalten diese Informationen durch:

- Ablaufverfolgung der Benutzeraktivität.
- Erfassen von Leistungsindikatoren, die die Auslastung für jede Ressource messen.
- Überwachung der CPU- und E/A-Auslastung für Vorgänge, die von jedem Benutzer ausgeführt werden.

Zu Zwecken der Messung müssen Sie auch erkennen können, welcher Benutzer für das Durchführen welcher Vorgänge verantwortlich ist, und Ressourcen, die diese Vorgänge verwenden. Die gesammelten Informationen sollten ausreichen, um eine genaue Abrechnung zu ermöglichen.

<a name="issue-tracking"></a>
## Problemverfolgung
Kunden und anderen Benutzern könnten möglicherweise Probleme bei unerwarteten Ereignissen oder unerwartetem Verhalten im System melden. Die Problemverfolgung betrifft das Verwalten dieser Probleme, das Zuordnen zu Lösungen für alle zugrunde liegenden Probleme im System und dem Informieren der Kunden über mögliche Lösungen.

### Anforderungen für die Problemverfolgung
Die Problemverfolgung erfolgt häufig über ein separates System, mit dem Operatoren die von den Benutzern gemeldeten Problemdetails aufzeichnen und berichten können. Zu diesen Details zählen Informationen wie z. B. die Aufgaben, die der Benutzer durchführen wollte, die Symptome des Problems, die Abfolge der Ereignisse und alle Fehler oder Warnmeldungen, die ausgestellt wurden.

### Datenquellen, Instrumentation und Anforderungen für die Datensammlung
Die ursprüngliche Datenquelle für die Problemverfolgung von Daten ist der Benutzer, der das Problem überhaupt gemeldet hat. Der Benutzer kann möglicherweise zusätzliche Daten bereitstellen wie z. B. ein Absturzabbild (wenn die Anwendung eine Komponente enthält, die auf dem Desktop des Benutzers läuft), eine Momentaufnahme des Bildschirms sowie Datum und Uhrzeit, an dem der Fehler aufgetreten ist, zusammen mit anderen Umgebungsinformationen wie z. B. ihrem Speicherort.

Diese Informationen können beim Debuggen und beim Erstellen eines Backlogs für zukünftige Versionen der Software helfen.

### Analysieren von Daten für die Problemverfolgung
Verschiedene Benutzer könnten möglicherweise dasselbe Problem melden, und das Überwachungssystem für Probleme sollte gemeinsame Berichte zuordnen können.

Der Fortschritt des Debugging-Aufwands sollte für jeden Problembericht aufgezeichnet werden, und bei Behebung des Problems kann der Kunde über die Lösung informiert werden.

Wenn ein Benutzer ein Problem erkannt hat, zu dem es eine bekannte Lösung im Überwachungssystem für Probleme gibt, sollte der Operator den Benutzer umgehend über die Lösung informieren können.

## Ablaufverfolgung von Vorgängen und das Debuggen von Software-Versionen.
Wenn ein Benutzer ein Problem meldet, erkennt er häufig nur die unmittelbaren Auswirkungen, das es auf seine Vorgänge hat, und kann einem für die Wartung des Systems zuständigen Operator nur die Ergebnisse der eigenen Erfahrungen berichten. Diese Erfahrungen sind in der Regel nur sichtbare Symptome für ein grundlegendes Problem oder mehrere. In vielen Fällen müssen Analysten die Abfolge der zugrunde liegenden Vorgänge prüfen, um die eigentliche Ursache des Problems zu rekonstruieren (dieser Prozess wird als _Fehlerursachenanalyse_ bezeichnet).

> [AZURE.NOTE]Die Fehlerursachenanalyse kann Ineffizienzen bei der Entwicklung einer Anwendung aufdecken. In diesen Fällen kann es möglich sein, die betroffenen Elemente zu überarbeiten und als Teil einer späteren Version bereitzustellen. Dieser Vorgang erfordert eine sorgfältige Steuerung und die aktualisierten Komponenten sollten genau überwacht werden.

### Anforderungen für Ablaufverfolgung und Debuggen
Für die Ablaufverfolgung unerwarteter Ereignisse und anderer Probleme ist es wichtig, dass die Überwachungsdaten aussagekräftig sind, nicht nur bei Problemen auf oberster Ebene, und ausreichend Details beinhalten, damit ein Analyst die Ursprünge dieser Probleme nachverfolgen und die Abfolge der aufgetretenen Ereignisse rekonstruieren kann. Diese Informationen müssen ausreichend sein, damit ein Analyst die Ursache von Problemen diagnostizieren und ein Entwickler dann die erforderlichen Änderungen vornehmen kann, um wiederkehrende Probleme zu verhindern.

### Datenquellen, Instrumentation und Anforderungen für die Datensammlung
Die Problembehandlung kann die Ablaufverfolgung aller Methoden (und ihrer Parameter) beinhalten, die als Teil eines Vorgangs aufgerufen werden, um eine Struktur zu erstellen, die den logischen Ablauf des Systems darstellt, wenn ein Kunde eine bestimmte Anforderung sendet. Vom System als Ergebnis dieses Ablaufs generierte Ausnahmen und Warnungen müssen erfasst und protokolliert werden.

Um das Debuggen zu unterstützen, kann das System Hooks bereitstellen, mit denen einen Operator Zustandsinformationen an wichtigen Stellen im System erfassen kann oder detaillierte Schritt-für-Schritt-Informationen liefern kann, während die ausgewählten Vorgänge fortschreiten. Das Sammeln von Daten auf dieser Detailebene kann eine zusätzliche Belastung für das System bedeuten, sodass dies ein temporärer Vorgang sein sollte, der hauptsächlich verwendet wird, wenn eine ungewöhnliche Serie an Ereignissen auftritt, die schwer zu replizieren ist, oder wenn eine neue Version eines oder mehrerer Element in einem System eine sorgfältige Überwachung erfordert, um das erwartungsgemäße Funktionieren sicherzustellen.

## Überwachungs- und Diagnosepipeline
Die Überwachung von einem umfassenden verteilten System stellt eine erhebliche Herausforderung dar, und jedes der im vorherigen Abschnitt erläuterten Szenarios sollte nicht unbedingt als isoliert betrachtet werden. Es gibt wahrscheinlich erhebliche Überlappungen bei der Überwachung und Diagnose von Daten, die für jede Situation erforderlich sind, auch wenn diese Daten auf verschiedene Weise verarbeitet und dargestellt werden. Aus diesen Gründen sollten Sie die Überwachung und Diagnose ganzheitlich betrachten.

Sie können sich die gesamte Überwachung und den Diagnoseprozess als eine Pipeline vorstellen, die die in Abbildung 1 dargestellten Phasen umfasst.

![](media/best-practices-monitoring/Pipeline.png)

_Abbildung 1: Die Phasen in der Überwachungs- und Diagnosepipeline_

Abbildung 1 zeigt, wie die Daten für die Überwachung und Diagnose aus einer Vielzahl von Datenquellen stammen können. Die Instrumentations-/Auflistungsphase betrifft die Instrumentation. Es wird bestimmt, welche Daten erfasst werden, wie das geschieht und in welchem Format, damit sie leicht überprüft werden können. Die Analyse-/Diagnosephase verwendet die Rohdaten, um sinnvolle Informationen zu generieren, der zur Ermittlung des Systemzustands verwendet werden können. Diese Informationen können Entscheidungen über mögliche Aktionen unterstützen, und die Ergebnisse können in die Instrumentations-/Auflistungsphase zurückgeführt werden. Die Visualisierungs-/Warnphase stellt eine brauchbare Ansicht des Systemstatus dar. Sie kann Informationen nahezu in Echtzeit anzeigen, mit einer Reihe von Dashboards, und könnte Berichte, Diagramme und Graphiken generieren, um eine historische Ansicht der Daten bereitzustellen, mit denen langfristige Trends erkannt werden können. Wenn Informationen anzeigen, dass ein KPI wahrscheinlich zulässige Begrenzungen überschreitet, kann in dieser Phase eine Warnung an einen Operator ausgelöst werden. In einigen Fällen kann eine Warnung auch verwendet werden, um einen automatisierten Prozess auszulösen, der Korrekturmaßnahmen wie die automatische Skalierung ergreift.

Beachten Sie, dass diese Schritte den fortlaufenden Prozessablauf bilden, in dem die Phasen parallel erfolgen. Im Idealfall sollten alle Phasen dynamisch konfiguriert werden. Insbesondere an einigen Stellen, wenn ein System neu bereitgestellt wurde oder ein Fehler aufgetreten ist, kann das häufigere Sammeln erweiterter Daten erforderlich sein. In anderen Fällen sollte es möglich sein, nur die Aufzeichnung grundlegender Informationen sicherzustellen, um zu prüfen, dass das System ordnungsgemäß funktioniert.

Darüber hinaus sollte der gesamte Überwachungsprozess als lebende, fortlaufende Lösung angesehen werden, die der Feinabstimmung und Verbesserungen durch Feedback unterliegt. Sie beginnen beispielsweise mit der Messung vieler Faktoren, um den Systemstatus zu ermitteln, aber eine Analyse über den Zeitverlauf könnte zu einer Verfeinerung führen, da nicht relevante Maßnahmen verworfen werden und Sie sich dadurch genauer auf die wirklich benötigten Daten konzentrieren können, während Hintergrundgeräusche minimiert werden.

## Datenquellen der Überwachung und Diagnose
Die vom Überwachungsprozess verwendeten Informationen können aus verschiedenen Quellen stammen, wie in Abbildung 1 dargestellt. Auf der Anwendungsebene stammen Informationen aus Protokollen der Ablaufverfolgung, die im Code des Systems integriert sind. Entwickler sollten einen standardisierten Ansatz für die Nachverfolgung der Ablaufsteuerung durch ihren Code befolgen (z. B. beim Einstieg in eine Methode eine Ablaufverfolgungsmeldung ausgeben, die den Namen der Methode, die aktuelle Uhrzeit, den Wert der einzelnen Parameter und alle anderen relevanten Informationen angibt. Aufzeichnen der Einstiegs- und Ausgangszeiten könnten sich ebenso als nützlich erweisen.) Sie sollten alle Ausnahmen und Warnungen protokollieren und sicherstellen, dass Sie eine vollständige Ablaufverfolgung von geschachtelten Ausnahmen und Warnungen beibehalten. Im Idealfall sollten Sie auch Informationen zur Identifizierung des Benutzer erfassen, indem der Code zusammen mit den Korrelationsinformationen für die Aktivität ausgeführt wird (um Anforderungen nachzuverfolgen, während sie das System passieren), und Versuche eines Zugriffs auf alle Ressourcen protokollieren, wie Nachrichtenwarteschlangen, Datenbanken, Dateien und andere abhängige Dienste. Diese Informationen können für Mess- und Überwachungszwecke verwendet werden.

Viele Anwendungen verwenden Bibliotheken und Frameworks für allgemeine Aufgaben wie den Zugriff auf einen Datenspeicher oder die Kommunikation über ein Netzwerk. Diese Frameworks können möglicherweise so konfiguriert werden, dass sie Ihre eigenen Meldungen zur Ablaufverfolgung und rohe Diagnoseinformationen ausgeben, z. B. Transaktionsraten, Datenübertragungserfolge und -fehlschläge und so weiter.

> [AZURE.NOTE]Viele moderne Frameworks veröffentlichen automatisch die Leistung und Ablaufverfolgungsereignisse. Das Erfassen dieser Informationen ist einfach eine Frage der Bereitstellung von Möglichkeiten zum Abrufen und Speichern, wo sie verarbeitet und analysiert werden können.

Das Betriebssystem, auf dem die Anwendung ausgeführt wird, kann als systemweite Informationsquelle auf niedriger Ebene dienen, z. B. mit Leistungsindikatoren, die E/A-Raten, Speicherauslastung und CPU-Auslastung angeben. Betriebssystemfehler (z. B. Fehler, eine Datei ordnungsgemäß zu öffnen) können auch gemeldet werden.

Sie sollten auch die zugrunde liegende Infrastruktur und Komponenten berücksichtigen, mit denen das System ausgeführt wird. Virtuelle Computer, virtuelle Netzwerke und Speicherdienste können alle Quellen für wichtige Leistungsindikatoren auf Infrastrukturebene und andere Diagnosedaten sein.

Wenn die Anwendung andere externe Dienste verwendet, wie z. B. einen Webserver oder ein Datenverwaltungssystem, veröffentlichen diese Dienste möglicherweise ihre eigenen Ablaufverfolgungsinformationen, Protokolle und Leistungsindikatoren. Beispiele sind SQL Server Dynamic Management Views zum Nachverfolgen von Operationen in einer SQL Server-Datenbank und Protokolle der Internet Information Server-Ablaufverfolgung zum Aufzeichnen von Anforderungen an einen Webserver.

Wenn die Komponenten eines Systems geändert oder neue Versionen bereitgestellt werden, ist es wichtig, Probleme, Ereignisse und Metriken zu jeder Version zuordnen zu können. Diese Informationen sollten an die Versionspipeline gebunden sein, damit Probleme mit einer bestimmten Version einer Komponente schnell nachverfolgt und behoben werden können.

Sicherheitsprobleme können an jedem Punkt im System auftreten. Beispielsweise kann ein Benutzer versuchen, sich mit einer ungültigen Benutzer-ID oder ungültigem Kennwort anzumelden. Ein authentifizierter Benutzer könnte versuchen, nicht autorisierten Zugriff auf eine Ressource zu erhalten, und ein anderer Benutzer könnte einen ungültigen oder überholten Schlüssel für den Zugriff auf verschlüsselte Daten eingeben. Sicherheitsinformationen für erfolgreiche und fehlgeschlagene Anforderungen sollten immer protokolliert werden.

Der Abschnitt [Instrumentieren einer Anwendung](#instrumenting-an-application) enthält weitere Anleitungen für die Informationen, die Sie erfassen sollten. Es gibt jedoch eine Vielzahl von Strategien, mit denen Sie diese Informationen überhaupt sammeln können:

- **Anwendungs-/Systemüberwachung**. Diese Strategie verwendet interne Quellen innerhalb der Anwendung, der Anwendungsframeworks, des Betriebssystems und der Infrastruktur. Der Anwendungscode selbst kann eigene Überwachungsdaten an wesentlichen Punkten während des Lebenszyklus einer Clientanforderung generieren. Die Anwendung kann Anweisungen für die Ablaufverfolgung enthalten, die je nach Umständen selektiv aktiviert oder deaktiviert werden können. Es könnte auch möglich sein, die Diagnose mithilfe des Diagnoseframeworks dynamisch einzufügen. Diese Frameworks stellen normalerweise Plug-Ins bereit, die sich an verschiedenen Punkten der Instrumentation in Ihrem Code anfügen und Ablaufverfolgungsdaten an diesen Punkten erfassen.

Darüber hinaus kann Ihr Code und/oder die zugrunde liegende Infrastruktur an kritischen Punkten Ereignisse auslösen. Überwachungsagenten, die für die Überwachung dieser Ereignisse konfiguriert sind, können die Ereignisinformationen erfassen.

- **Real User Monitoring**. Dieser Ansatz zeichnet die Interaktionen zwischen einem Benutzer und der Anwendung auf und beobachtet den Fluss der einzelnen Anforderungen und Antworten. Diese Informationen können einen zweifachen Zweck haben: sie dienen der Messung der Nutzung durch jeden Benutzer und können feststellen, ob Benutzer die geeignete Quality of Service erhält (z. B. schnelle Antwortzeiten, niedrige Latenz und minimales Auftreten von Fehlern). Die erfassten Daten können verwendet werden, um Bereiche zu erkennen, in denen Fehler am häufigsten auftreten, und Elemente, bei denen das System verlangsamt, möglicherweise aufgrund von Hotspots in der Anwendung oder einer anderen Form des Engpasses. Wenn dieser Ansatz sorgfältig implementiert ist, ist es möglich, Benutzerabläufe durch die Anwendung zum Debuggen und zu Testzwecken zu rekonstruieren.

	> [AZURE.IMPORTANT]Die durch Überwachung von realen Benutzern aufgezeichneten Daten sollten als hochsensibel eingestuft werden, da sie vertrauliches Material enthalten können. Wenn die aufgezeichneten Daten gespeichert werden, muss dies an einem sicheren Ort geschehen. Wenn die Daten für die Leistungsüberwachung und das Debuggen verwendet werden, sollten alle persönlich identifizierbaren Informationen zunächst entfernt werden.

- **Synthetische Benutzerüberwachung**. Bei dieser Vorgehensweise schreiben Sie Ihren eigenen Testclient, der einen Benutzer simuliert und eine konfigurierbare, aber typische Reihe von Vorgängen ausführt. Sie können die Leistung des Testclients verfolgen, um den Zustand des Systems zu ermitteln. Sie können auch mehrere Instanzen des Testclients als Teil eines Belastungstests einrichten, um festzustellen, wie das System unter Stress reagiert und welche Art von Überwachungsausgabe unter diesen Bedingungen generiert wird.

	> [AZURE.NOTE]Sie können die tatsächliche und synthetische Benutzerüberwachung durch Einschließen von Code implementieren, der die Ausführung von Methodenaufrufen und anderen kritischen Teilen einer Anwendung verfolgt und zeitlich einordnet.

- **Profilerstellung**. Dieser Ansatz zielt in erster Linie auf das Überwachen und Verbessern der Anwendungsleistung ab. Anstatt auf der Funktionsebene zu arbeiten, die von der realen und synthetischen Benutzerüberwachung eingesetzt wird, erfasst er Informationen auf niedrigerer Ebene, während die Anwendung ausgeführt wird. Die Profilerstellung kann implementiert werden, indem periodisch Stichroben zum Ausführungsstatus einer Anwendung genommen werden (festlegen, welcher Teil des Codes zu einem bestimmten Zeitpunkt ausgeführt wird) oder mithilfe der Instrumentationsmethode, bei der Prüfpunkte an wichtigen Stellen in den Code eingefügt werden (z. B. am Start und Ende eines Methodenaufrufs) und aufzeichnet, welche Methoden zu welchem Zeitpunkt aufgerufen wurden und wie lange jeder Aufruf gedauert hat. Diese Daten können dann analysiert werden, um zu bestimmen, welche Teile der Anwendung zu Leistungsproblemen führen können.

- **Endpunktüberwachung**. Diese Technik verwendet einen oder mehrere diagnostische Endpunkte, die von der Anwendung speziell für die Überwachung verfügbar gemacht werden. Ein Endpunkt stellt einen Pfad in den Anwendungscode bereit und kann Informationen über den Zustand des Systems zurückgeben. Verschiedene Endpunkte können sich auf verschiedene Aspekte der Funktionalität konzentrieren. Sie können Ihren eigenen Diagnose-Client schreiben, der periodisch Anforderungen an diese Endpunkte sendet, und die Antworten anpassen. Dieser Ansatz wird in [Überwachungsmuster für den Integritätsendpunkt](https://msdn.microsoft.com/library/dn589789.aspx) auf der Microsoft-Website ausführlicher beschrieben.

Für maximale Absicherung sollten Sie diese Techniken in Kombination mit anderen verwenden.

<a name="instrumenting-an-application"></a>
## Instrumentieren einer Anwendung
Die Instrumentation ist ein wichtiger Bestandteil des Überwachungsprozesses. Sie können nur sinnvolle Entscheidungen zur Leistung und Integrität des Systems treffen, wenn Sie die Daten erfassen, mit denen Sie diese Entscheidungen in überhaupt können. Die Informationen, die Sie mit der Instrumentationsmethode sammeln, sollten zur Bewertung der Leistung, Diagnoseproblemen und zum Treffen von Entscheidungen ausreichen, ohne dass eine Anmeldung auf einen Remote-Produktionsserver zum manuellen Ausführen der Ablaufverfolgung (und des Debuggings) erforderlich ist.

Instrumentationsdaten umfassen normalerweise Informationen, die in die Ablaufverfolgung geschrieben werden, und Metriken:

- Der Inhalt eines Protokolls der Ablaufverfolgung kann das Ergebnis von Textdaten sein, die von der Anwendung geschrieben wurden, Binärdaten, die als Ergebnis eines Ablaufverfolgungsereignisses erstellt wurden (wenn die Anwendung Event Tracing for Windows, kurz ETW verwendet), oder sie können aus Systemprotokollen generiert werden, die durch die Teile der Infrastruktur ausgelöste Ereignisse aufzeichnen, wie z. B. einen Webserver. Protokollmeldungen in Textform sind häufig von Menschen lesbar, aber sie sollten auch in einem Format geschrieben sein, das eine einfache Analyse durch ein automatisiertes System ermöglicht. Sie sollten Protokolle außerdem kategorisieren. Schreiben Sie nicht alle Ablaufverfolgungsdaten in ein einzelnes Protokoll, sondern verwenden Sie separate Protokolle zum Aufzeichnen der Ablaufverfolgungsausgabe von verschiedenen betrieblichen Aspekten des Systems. Dies ermöglicht Ihnen, Protokollmeldungen schnell zu filtern, indem aus dem entsprechenden Protokoll gelesen wird, anstatt eine einzelne lange Datei verarbeiten zu müssen. Schreiben Sie nie Informationen, die unterschiedlichen Sicherheitsanforderungen unterliegen, in dasselbe Protokoll (z. B. Überwachungsinformationen und Debugging-Daten).

	> [AZURE.NOTE]Ein Protokoll kann als Datei im Dateisystem implementiert werden oder in einem anderen Format wie einem Blob im Blob-Speicher gehalten werden. Protokollinformationen können auch in strukturierteren Speichern gehalten werden, z. B. Zeilen in einer Tabelle.

- Metriken sind generell einfach ein Maß oder eine Anzahl eines Aspekts oder einer Ressource im System zu einem bestimmten Zeitpunkt, mit einem oder mehren zugeordneten Tags oder Dimensionen (manchmal auch als _Stichproben_ bezeichnet). Eine einzelne Instanz einer Metrik eignet sich in der Regel nicht isoliert. Stattdessen müssen Metriken im Zeitverlauf erfasst werden. Die wichtigste zu berücksichtigende Frage ist, welche Metriken erfasst werden sollten und wie häufig. Datengenerierung für Metriken kann oft eine erhebliche zusätzliche Last für das System bedeuten, während unregelmäßiges Erfassen der Metriken dazu führen kann, dass Sie die Umstände verpassen, die zu einem wichtigen Ereignis führen. Die Überlegungen variieren von Metrik zu Metrik. Z. B. kann die CPU-Auslastung auf einem Server von Sekunde zu Sekunde erheblich variieren, aber eine hohe Auslastung wird nur zu einem Problem, wenn sie über mehrere Minuten dauert.

<a name="information-for-correlating-data"></a>
### Informationen zum Korrelieren von Daten
Sie können einfach einzelne Leistungsindikatoren auf Systemebene überwachen, Metriken für Ressourcen erfassen und Ablaufverfolgungsinformationen für die Anwendung aus verschiedenen Protokolldateien erhalten, aber einige Formen der Überwachung erfordern die Analyse- und Diagnosephase in der Überwachungspipeline, um die aus verschiedenen Quellen aufgerufenen Daten zu korrelieren. Diese Daten können in den Rohdaten verschiedene Formen annehmen, und der Analyseprozess muss mit ausreichenden Instrumentationsdaten bereitgestellt werden, um die verschiedenen Formen abzubilden. Beispielsweise kann auf der Ebene des Anwendungs-Frameworks eine Aufgabe durch einen Thread-ID identifiziert werden, aber innerhalb einer Anwendung kann die gleiche Arbeit möglicherweise der Benutzer-ID für den Benutzer zugeordnet werden, der diese Aufgabe durchführt. Darüber hinaus ist es unwahrscheinlich, dass es eine 1:1-Zuordnung zwischen Threads und Benutzeranforderungen gibt, da asynchrone Vorgänge die gleichen Threads verwenden, um Vorgänge für mehr als einen Benutzer durchzuführen. Um das ganze noch komplizierter zu machen, kann eine einzelne Anforderung von mehr als einem Thread gehandhabt werden, während die Ausführung durch das System läuft. Wenn möglich, ordnen Sie jeder Anforderung eine eindeutige Aktivitäts-ID zu, die durch das System als Teil des Anforderungskontexts weitergegeben wird (das Verfahren zum Generieren und Einschließen der Aktivitäts-IDs in Ablaufverfolgungsinformationen ist abhängig von der Technologie, die zum Erfassen von Ablaufverfolgungsdaten verwendet wird).

Alle Überwachungsdaten sollten auf gleiche Weise mit einem Zeitstempel versehen werden. Notieren Sie aus Gründen der Konsistenz alle Datums- und Uhrzeitangaben mithilfe der Coordinated Universal Time. Dadurch können Sie leichter Sequenzen von Ereignissen nachverfolgen.

> [AZURE.NOTE]Computer in verschiedenen Zeitzonen und Netzwerken sind möglicherweise nicht synchronisiert, also sollten Sie sich nicht nur auf Zeitstempel verlassen, um Instrumentationsdaten zu korrelieren, die mehrere Computer umfassen.

### Welche Informationen sollten die Instrumentationsdaten enthalten?
Beachten Sie die folgenden Punkte, wenn Sie sich entscheiden, welche Instrumentationsdaten gesammelt werden sollen:

- Informationen, die von Ablaufverfolgungsereignissen erfasst werden, sollten für Computer und Menschen lesbar sein. Sie sollten gut definierte Schemata für diese Informationen übernehmen, um die automatisierte Verarbeitung von Protokolldaten über Systeme hinweg zu vereinfachen und eine Konsistenz für Vorgänge und technische Mitarbeiter sicherzustellen, die die Protokolle lesen. Schließen Sie die Umgebungsinformationen mit ein, wie z. B. die Bereitstellungsumgebung, den Computer, auf dem der Prozess ausgeführt wird, die Details des Prozesses und die Aufrufliste.  
- Eine Profilerstellung kann eine erhebliche Belastung für das System darstellen und sollte nur bei Bedarf aktiviert werden. Die Profilerstellung mithilfe der Instrumentationsmethode zeichnet ein Ereignis (z. B. einen Methodenaufruf) jedes Mal auf, wenn es auftritt, während bei Stichproben nur ausgewählte Ereignisse aufgezeichnet werden. Die Auswahl ist möglicherweise zeitbasiert – alle N Sekunden, oder auf Häufigkeit basiert – alle N Anforderungen. Wenn Ereignisse sehr häufig auftreten, kann die Profilerstellung mit der Instrumentationsmethode eine zu große Belastung darstellen und selbst die Gesamtleistung beeinträchtigen. In diesem Fall könnte der Stichprobenansatz vorgezogen werden. Wenn jedoch die Häufigkeit von Ereignissen niedrig ist, dann können Stichproben diese möglicherweise übersehen und in diesem Fall wäre die Instrumentation möglicherweise der bessere Ansatz.
- Geben Sie genügend Kontext an, damit ein Entwickler oder Administrator die Quelle jeder einzelnen Anforderung ermitteln kann. Dazu zählen beispielsweise auch Formen der Aktivitäts-ID zum Identifizieren einer bestimmten Anforderungsinstanz, und die Informationen, die zum Korrelieren dieser Aktivität mit der durchgeführten Computerarbeit und den verwendeten Ressourcen genutzt werden können. Beachten Sie, dass diese Arbeit Prozess- und Computergrenzen überschreiten kann. Für die Messung sollte der Kontext auch einen Verweis auf den Kunden beinhalten, der die Anforderung verursacht hat (entweder direkt oder indirekt über andere korrelierte Informationen). Dieser Kontext stellt wertvolle Informationen über den Anwendungszustand zum Zeitpunkt der Aufzeichnung der Überwachungsdaten bereit.
- Notieren Sie alle Anforderungen und die Standorte oder Regionen, aus denen solche Anforderungen gesendet werden. Mit diesen Informationen können Sie feststellen, ob es standortspezifische Hotspots gibt, und Daten bereitstellen, mit denen Sie bestimmen können, ob eine genutzte Anwendung oder die genutzten Daten neu partitioniert werden.
- Die Details der Ausnahmen sollten Sie sorgfältig aufzeichnen und erfassen. Oftmals gehen sehr wichtige Debuginformationen aufgrund einer mangelhaften Ausnahmebehandlung verloren. Erfassen Sie alle Details zu Ausnahmen, die von der Anwendung zurückgegeben werden, einschließlich aller innerer Ausnahmen und anderer Kontextinformationen, falls möglich mit Aufrufliste.
- Seien Sie konsistent bei den Daten, die die verschiedenen Elemente Ihrer Anwendung erfassen, da dies bei der Analyse von Ereignissen und der Korrelation zu Benutzeranfragen helfen kann. Erwägen Sie die Nutzung eines umfassenden und konfigurierbaren Protokollierungspakets, um Informationen zu sammeln, anstatt sich darauf zu verlassen, dass Entwickler die verschiedenen Teile des Systems nach dem gleichen Ansatz implementieren. Das Sammeln von Daten zu wesentlichen Leistungsindikatoren, wie z. B. ausgeführtes E/A-Volumen, Netzwerkauslastung, Anzahl der Anforderungen, Speichernutzung und CPU-Auslastung. Einige Infrastrukturdienste stellen ihre eigenen Leistungsindikatoren bereit, wie z. B. die Anzahl der Verbindungen zu einer Datenbank, die Rate, mit der Transaktionen ausgeführt werden, und die Anzahl der Transaktionen, die gelingen oder fehlschlagen. Anwendungen können auch ihre eigenen Leistungsindikatoren definieren.
- Protokollieren Sie alle Aufrufe an externe Dienste wie Datenbanksysteme, Webdienste oder andere Dienste auf Systemebene, die als Teil der Infrastruktur bereitgestellt werden (z. B. der Azure-Caching). Zeichnen Sie Informationen über die Verarbeitungszeit für jeden Aufruf auf, und ob der Aufruf erfolgreich war oder fehlgeschlagen ist. Wenn möglich, erfassen Sie Informationen über alle Wiederholungsversuche und Fehlversuche für vorübergehende Fehler, die auftreten.

### Sicherstellen der Kompatibilität mit Telemetrie-Systemen
In vielen Fällen werden mit der Instrumentationsmethode produzierte Informationen als eine Reihe von Ereignissen generiert und zur Verarbeitung und Analyse an ein separates Telemetriesystem übergeben. Ein Telemetriesystem ist in der Regel unabhängig von einer bestimmten Anwendung oder Technologie, erwartet jedoch Informationen zu einem bestimmten Format, das in der Regel durch ein Schema definiert ist. Das Schema gibt effektiv einen Vertrag an, in dem Datenfelder und Typen definiert werden, die das Telemetriesystem erfassen kann. Das Schema sollte generalisiert sein, damit Daten aus einer Reihe von Plattformen und Geräten stammen können.

Ein allgemeines Schema sollte Felder enthalten, die auf alle Instrumentationsereignisse zutreffen, wie z. B. den Namen und die Uhrzeit des Ereignisses, die IP-Adresse des Absenders und die für die Korrelation mit anderen Ereignissen erforderlichen Details (z. B. eine Benutzer-ID, Geräte-ID und eine Anwendungs-ID). Denken Sie daran, dass Ereignisse von einer beliebigen Anzahl von Geräten ausgelöst werden können, also sollte das Schema nicht vom Gerätetyp abhängig sein. Darüber hinaus können Ereignisse für dieselbe Anwendung durch eine Reihe von verschiedenen Geräten ausgelöst werden. Die Anwendung unterstützt womöglich Roaming oder eine andere Form der geräteübergreifenden Verteilung. Im Idealfall sollten die meisten dieser Felder der Ausgabe der Protokollierungsbibliothek zugeordnet werden, um die Ereignisse zu erfassen.

Das Schema kann auch Domänenfelder enthalten, die für ein bestimmtes Szenario relevant sind, das für verschiedene Anwendungen gilt. Dabei kann es sich um Informationen zu Ausnahmen, Ereignissen bei Anwendungsstart und -ende und Web Services-API-Aufruf erfolgreich und/oder fehlerhaft handeln. Alle Anwendungen, die den gleichen Satz von Feldern für die Domäne verwenden, sollten den gleichen Satz von Ereignissen ausgeben, was einen Satz gemeinsamer Berichte und Analysen ermöglicht, die zu erstellen sind.

Schließlich kann ein Schema benutzerdefinierte Felder zum Erfassen von Details von anwendungsspezifischen Ereignissen enthalten.

### Empfehlungen zum Instrumentieren von Anwendungen
Die folgende Liste enthält Empfehlungen zum Instrumentieren einer verteilten Anwendung, die in der Cloud ausgeführt wird.

- Erstellen Sie Protokolle so, dass sie einfach zu lesen und leicht zu analysieren sind. Verwenden Sie die strukturierte Protokollierung, wo möglich. Seien Sie in Protokollmeldungen prägnant und anschaulich.
- Identifizieren Sie in allen Protokollen die Quelle und stellen Sie Kontext- und Zeitinformationen bereit, während jeder Protokolldatensatz geschrieben wird.
- Verwenden Sie die gleiche Zeitzone und das gleiche Format für alle Zeitstempel. Dadurch können Sie Ereignisse für Vorgänge korrelieren, die Hardware und Dienste in unterschiedlichen geografischen Regionen umfassen.
- Kategorisieren Sie Protokolle und schreiben Sie Meldungen in die entsprechende Protokolldatei.
- Legen Sie keine vertraulichen Informationen zum System oder persönliche Informationen über Benutzer offen. Bereinigen Sie diese Informationen, bevor sie protokolliert werden, aber stellen Sie sicher, dass alle relevanten Details beibehalten werden. Entfernen Sie z. B. die ID und das Kennwort von allen Datenbank-Verbindungszeichenfolgen, aber schreiben Sie die restlichen Daten in das Protokoll, sodass ein Analyst bestimmen kann, ob das System auf die richtige Datenbank zugreift. Protokollieren Sie alle kritische Ausnahmen, aber ermöglichen Sie es dem Administrator, Ausnahmen und Warnungen der unteren Ebene zu aktivieren und deaktivieren. Erfassen und protokollieren Sie darüber hinaus alle logischen Wiederholungsinformationen. Diese Daten können bei der Überwachung des vorübergehenden Systemzustands nützlich sein.
- Verfolgen Sie Aufrufe außerhalb des Prozesses, z. B. Anforderungen an externe Webdienste oder Datenbanken.
- Vermischen Sie keine Protokollmeldungen mit verschiedenen Sicherheitsanforderungen in derselben Protokolldatei. Schreiben Sie z. B. keine Informationen zum Debuggen und zum Überwachen in das gleiche Protokoll.
- Mit Ausnahme von Überwachungsereignissen sollten alle Aufrufe der Protokollierung "Fire-and-Forget"-Vorgänge sein, die den Geschäftsbetrieb nicht blockieren dürfen. Überwachungsereignisse sind außergewöhnlich, da sie für das Unternehmen wichtig sind und als grundlegender Bestandteil der Geschäftsvorgänge klassifiziert werden können.
- Protokollierung sollte erweiterbar sein und keine direkten Abhängigkeiten auf konkreten Zielen haben. Anstatt z. B. Daten mithilfe von _System.Diagnostics.Trace_ zu schreiben, definieren Sie eine abstrakte Schnittstelle (z. B. _ILogger_), die Protokollierungsmethoden bereitstellt und mithilfe angemessener Maßnahmen implementiert werden kann.
- Alle Protokollierungen müssen ausfallsicher sein und dürfen niemals kaskadierende Fehler auslösen. Protokollierungen dürfen keine Ausnahmen auslösen.
- Behandeln Sie die Instrumentation als fortlaufenden, iterativen Prozess und überprüfen Sie Protokolle regelmäßig, nicht nur, wenn ein Problem vorliegt.

## Erfassen und Speichern von Daten
Die Phase des Erfassens des Überwachungsprozesses betrifft das Abrufen der Informationen, die mit der Instrumentationsmethode generiert wurden, das Formatieren dieser Daten, um die Analyse-/Berechnungsphase zu erleichtern, und das Speichern der transformierten Daten in zuverlässigen und zugänglichen Speichern. Die Instrumentationsdaten, die Sie in verschiedenen Teilen eines verteilten Systems sammeln, können in einer Vielzahl von Speicherorten und in unterschiedlichen Formaten gehalten werden. Ihr Anwendungscode kann z. B. Ablaufverfolgungsprotokolle und Ereignisprotokolldaten der Anwendung generieren, während die Leistungsindikatoren, die die wichtigsten Aspekte der von der Anwendung genutzten Infrastruktur überwachen, mithilfe anderer Technologien erfasst werden können. Alle Drittanbieter-Komponenten und -Dienste, die Ihre Anwendung verwendet, können Instrumentationsinformationen in unterschiedlichen Formaten bereitstellen, mithilfe von separaten Ablaufverfolgungsdateien, Blob-Speicher oder sogar einen benutzerdefinierten Datenspeicher.

Die Datensammlung erfolgt häufig durch die Implementierung eines Erfassungsdienstes, der unabhängig von der Anwendung ausgeführt werden kann, die die Instrumentationsdaten generiert. Abbildung 2 zeigt ein Beispiel für diese Architektur, in der das Subsystem der Instrumentationsdatensammlung hervorgehoben ist.

![](media/best-practices-monitoring/TelemetryService.png)

_Abbildung 2: Erfassen von Instrumentationsdaten_

Bitte beachten Sie, dass dies eine vereinfachte Ansicht ist. Der Erfassungsdienst ist nicht unbedingt ein einzelner Prozess und umfasst möglicherweise viele Bestandteile, die auf verschiedenen Computern ausgeführt werden, wie in den folgenden Abschnitten beschrieben. Wenn die Analyse von einigen Telemetriedaten darüber hinaus schnell ausgeführt werden muss (heiße Analyse, wie im Abschnitt [Unterstützung von heißer, warmer und kalter Analyse](#supporting-hot-warm-and-cold-analysis) weiter unten in diesem Dokument beschrieben), können lokale Komponenten außerhalb des Erfassungsdienstes die Analyseaufgaben sofort durchführen. Abbildung 2 zeigt diese Situation für ausgewählte Ereignisse. Nach der analytischen Verarbeitung können die Ergebnisse direkt an das Visualisierungs- und Warnsubsystem gesendet werden. Daten zur warmen oder kalten Analyse werden im Speicher gehalten, während sie auf die Verarbeitung warten.

Für Azure-Anwendungen und -Dienste bietet Azure Diagnostics (WAD) eine mögliche Lösung für das Erfassen von Daten. WAD sammelt Daten aus den folgenden Quellen für jeden Computeknoten, verdichtet sie und lädt sie dann in den Azure-Speicher hoch:

- Azure-Protokolle
- IIS-Protokolle
- Protokolle zu IIS-Anforderungsfehlern
- Windows-Ereignisprotokolle
- Leistungsindikatoren
- Absturzabbilder
- Infrastrukturprotokolle der Azure-Diagnose  
- Benutzerdefinierte Fehlerprotokolle

Weitere Informationen finden Sie im Artikel [Azure: Telemetrie-Grundlagen und Problembehandlung](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) auf der Microsoft-Website.

### Strategien zum Erfassen von Instrumentationsdaten
Wenn man die Elastizität der Cloud bedenkt und um zu vermeiden, dass Telemetriedaten manuell von jedem Knoten im System abgerufen werden müssen, sollten Daten so angeordnet sein, dass sie an einen zentralen Standort übertragen und dort konsolidiert werden können. In einem System, das mehrere Rechenzentren umfasst, kann es hilfreich sein, Daten zunächst zu sammeln, zu konsolidieren und je nach Region zu speichern, und dann die regionalen Daten in einem einzigen zentralen System zu aggregieren.

Um die Verwendung der Bandbreite zu optimieren, können Sie festlegen, dass weniger dringende Daten in Segmenten als Batches übertragen werden. Jedoch dürfen die Daten nicht unbegrenzt verzögert werden, insbesondere, wenn sie zeitgebundene Informationen beinhalten.

#### _Pullen und Pushen von Instrumentationsdaten_
Das Subsystem für die Instrumentationsdatensammlung kann Instrumentationsdaten entweder aktiv aus verschiedenen Protokollen und anderen Quellen für jede Instanz der Anwendung abrufen (das _Pullmodell_), oder es kann als passiver Empfänger auf Daten warten, die aus den Komponenten gesendet werden, die jede Instanz der Anwendung bilden (das _Pushmodell_).

Ein Ansatz zum Implementieren des Pullmodells ist die Verwendung von Überwachungsagents, die lokal mit jeder Instanz der Anwendung ausgeführt werden. Ein Überwachungsagent ist ein separater Prozess, der in regelmäßigen Abständen (Pulls) Telemetriedaten abruft, die vom lokalen Knoten gesammelt werden, und diese Informationen direkt in den zentralen Speicher schreibt, der von allen Instanzen der Anwendung gemeinsam verwendet wird. Dies ist der Mechanismus, der von WAD implementiert wird. Jede Instanz einer Azure-Web- oder Workerrolle kann so konfiguriert werden, dass Diagnose- und andere lokal gespeicherte Ablaufverfolgungsinformationen erfasst werden. Der Überwachungsagent, der nebenbei ausgeführt wird, kopiert jeweils die angegebenen Daten in den Azure-Speicher. Auf der Seite [Konfigurieren von Diagnose für Azure Cloud Services und Virtual Machines](https://msdn.microsoft.com/library/azure/dn186185.aspx) auf der Microsoft-Website finden Sie nähere Einzelheiten. Einige Elemente, wie z. B. IIS-Protokolle, Absturzabbilder und benutzerdefinierte Fehlerprotokolle werden in Blob-Speicher geschrieben, während Daten aus dem Windows-Ereignisprotokoll, ETW-Ereignisse und Leistungsindikatoren im Tabellenspeicher erfasst werden. Dieser Mechanismus ist in Abbildung 3 dargestellt:

![](media/best-practices-monitoring/PullModel.png)

_Abbildung 3: Verwenden eines Überwachungs-Agents zum Abrufen von Informationen und Schreiben in einen freigegebenen Speicher_

> [AZURE.NOTE]Überwachungsagents sind ideal dazu geeignet, Instrumentationsdaten zu erfassen, die naturgemäß aus einer Datenquelle abgerufen werden, z. B. Informationen aus SQL Server Management Views oder die Länge einer Azure Service Bus-Warteschlange.

Informationen zum Konfigurieren und Verwenden von Azure Diagnostics finden Sie auf der Seite [Sammeln von Protokollierungsdaten mit Azure Diagnostics](https://msdn.microsoft.com/library/azure/gg433048.aspx) auf der Microsoft-Website.

Telemetriedaten für eine kleine Anwendung, die auf einer begrenzten Anzahl von Knoten ausgeführt wird, können an einem einzigen Ort mithilfe des Ansatzes gespeichert werden, der gerade beschrieben wurde. Eine komplexe, hochgradig skalierbare, globale Cloud-Anwendung kann jedoch problemlos große Mengen von Daten aus Hunderten von Web-und Workerrollen, Datenbankshards und anderen Diensten generieren. Diese Datenflut kann die E/A-Bandbreite mit einfachem, zentralem Speicher schnell überlasten. Daher muss Ihre Telemetrielösung skalierbar sein, damit sie bei erweitertem System nicht zum Engpass wird, und im Idealfall ein Maß an Redundanz integrieren, um das Risiko zu reduzieren, wichtige Überwachungsinformationen zu verlieren (wie Überwachungs- und Abrechnungsdaten), wenn ein Teil des Systems fehlschlägt.

Um diese Probleme zu beheben, können Sie Warteschlangen implementieren. Abbildung 4 zeigt eine solche Struktur. In dieser Architektur sendet der lokale Überwachungsagent (sofern er entsprechend konfiguriert werden kann) oder benutzerdefinierte Datensammlungsdienst (sofern dies nicht der Fall ist) Daten an eine Warteschlange, und ein separater, asynchron ausgeführter Prozess (der Speicherschreibdienst in Abbildung 4) nimmt die Daten aus dieser Warteschlange und schreibt sie in den freigegebenen Speicher. Eine Nachrichten-Warteschlange ist für dieses Szenario geeignet, da sie mindestens einmal die Semantik bereitstellt, um sicherzugehen dass einmal in die Warteschlange eingebundene Daten nicht verloren gehen. Der Speicherdienst kann mithilfe einer separaten Workerrolle implementiert werden.

![](media/best-practices-monitoring/BufferedQueue.png)

_Abbildung 4. Mithilfe einer Warteschlange Instrumentationsdaten puffern_

Der lokale Datensammlungsdienst kann Daten direkt nach Empfang zu einer Warteschlange hinzufügen. Die Warteschlange fungiert als Puffer und der Dienst, der in den Speicher schreibt, kann Daten in seinem eigenen Tempo abrufen und schreiben. Standardmäßig arbeitet eine Warteschlange auf der Basis von First-In-First-Out, aber Sie können Nachrichten priorisieren, um den Weg durch die Warteschlange zu beschleunigen, wenn sie Daten enthalten, die schneller verarbeitet werden müssen. Weitere Informationen finden Sie unter [Prioritätswarteschlange](https://msdn.microsoft.com/library/dn589794.aspx). Alternativ können Sie verschiedene Kanäle (z. B. Service Bus-Themen) verwenden, um Daten an verschiedene Ziele zu leiten, abhängig von der Form der erforderlichen analytischen Verarbeitung.

Aus Gründen der Skalierbarkeit könnten Sie mehrere Instanzen des Speicherschreibdienstes ausführen. Gibt es eine große Anzahl von Ereignissen, könnten Sie einen Ereignis-Hub verwenden, um die Daten an verschiedene Serverressourcen für die Verarbeitung und Speicherung zu senden.

<a name="consolidating-instrumentation-data"></a>
#### _Konsolidierung von Instrumentationsdaten_
Die vom Datensammlungsdienst aus einer einzelnen Instanz einer Anwendung abgerufenen Instrumentationsdaten bietet eine lokalisierte Ansicht der Integrität und Leistung von dieser Instanz. Um die allgemeine Integrität des Systems zu bewerten, ist es erforderlich, einige Aspekte der Daten in den lokalen Ansichten zusammen zu konsolidieren. Dies kann durchgeführt werden, nachdem die Daten gespeichert wurden. In einigen Fällen ist es auch möglich, dies während der Sammlung zu erreichen. Anstatt direkt auf den freigegebenen Speicher geschrieben zu werden, könnten die Instrumentationsdaten an einen separaten Konsolidierungsdatendienst übergeben werden, der Daten kombiniert und die Rolle eines Filters und Bereinigungsprozesses übernimmt. Z. B. können Instrumentationsdaten mit den gleichen Korrelationsinformationen wie beispielsweise eine Aktivitäts-ID zusammengelegt werden (es ist möglich, dass ein Benutzer einen Geschäftsvorgang auf einem Knoten beginnt und dann auf einen anderen Knoten verwiesen wird, falls der Knoten fehlschlägt oder abhängig davon, wie der Lastenausgleich konfiguriert ist). Dieser Prozess kann auch alle doppelt vorhandenen Daten erkennen und entfernen (immer eine Möglichkeit, wenn der Telemetriedienst Nachrichtenwarteschlangen verwendet, um Instrumentationsdaten aus dem Speicher zu pushen). Abbildung 5 zeigt ein Beispiel für diese Struktur.

![](media/best-practices-monitoring/Consolidation.png)

_Abbildung 5: Verwenden eines separaten Dienstes zum Konsolidieren und Bereinigen von Instrumentationsdaten_

### Speichern von Instrumentationsdaten
Die vorherigen Diskussionen haben auf eher simple Weise dargestellt, wie Instrumentationsdaten gespeichert werden. In der Realität kann es sinnvoll sein, die verschiedenen Arten von Informationen mithilfe von Technologien zu speichern, die am besten für die Art der Verwendung geeignet sind. Azure-Blob- und Tabellenspeicher haben beispielsweise einige Ähnlichkeiten, was den Zugriff darauf angeht, sind jedoch im Hinblick auf Vorgänge eingeschränkt, die Sie damit ausführen können. Außerdem ist die Granularität der enthaltenen Daten ganz unterschiedlich. Wenn Sie weitere analytische Vorgänge durchführen müssen oder Volltext-Suchfunktionen für die Daten erforderlich sind, ist möglicherweise die Nutzung von Datenspeicherung besser geeignet, die für bestimmte Typen von Abfragen und Datenzugriff optimierte Funktionen bietet. Beispielsweise könnten Daten von Leistungsindikatoren in einer SQL-Datenbank gespeichert werden, um eine Ad-hoc-Analyse zu ermöglichen; Ablaufverfolgungsprotokolle werden möglicherweise besser in Azure DocumentDB gespeichert; Sicherheitsinformationen könnten in HDFS geschrieben werden; Informationen, die eine die Volltextsuche erfordern, könnten mit Elastic Search gespeichert werden (mithilfe der umfassenden Indexierung werden Suchvorgänge zusätzlich beschleunigt). Sie können einen zusätzlichen Dienst implementieren, der die Daten in regelmäßigen Abständen aus dem freigegebenen Speicher abruft, sie partitioniert und gemäß ihrem Zweck filtert, und sie anschließend in einen entsprechenden Satz von Datenspeichern schreibt, wie in Abbildung 6 dargestellt. Ein alternativer Ansatz ist die Berücksichtigung dieser Funktionalität bei der Konsolidierung und der Bereinigung, sodass die Daten direkt beim Abrufen in diese Speicher geschrieben werden, anstatt sie in einem freigegebenen Zwischenspeicherbereich zu speichern. Jeder Ansatz hat Vor- und Nachteile. Das Implementieren eines separaten Partitionierungsdiensts verringert die Belastung auf den Konsolidierungs- und Bereinigungsdienst und ermöglicht, dass mindestens einige der partitionierten Daten neu generiert werden, falls erforderlich (abhängig davon, wie viele Daten im freigegebenen Speicher beibehalten werden). Allerdings werden zusätzliche Ressourcen belegt und es kommt möglicherweise zu einer Verzögerung zwischen den Instrumentationsdaten, die von jeder Anwendungsinstanz empfangen werden, und den Daten, die in verwertbare Informationen umgewandelt werden.

![](media/best-practices-monitoring/DataStorage.png)

_Abbildung 6: Partitionierung von Daten anhand analytischer Anforderungen und Speicheranforderungen_

Die gleichen Instrumentationsdaten können für mehrere Zwecke erforderlich sein. Z. B. können Leistungsindikatoren dazu verwendet werden, um eine historische Ansicht der Systemleistung zu liefern, aber diese Informationen können möglicherweise auch mit anderen Verwendungsdaten kombiniert werden, um Abrechnungsinformationen für Kunden zu generieren. In diesen Situationen können möglicherweise dieselben Daten an mehrere Ziele gesendet werden, z. B. eine Dokumentendatenbank, die als langfristiger Speicher zum Speichern von Rechnungsinformationen dient, und einen mehrdimensionalen Speicher für die Verarbeitung komplexer Leistungsanalysen.

Sie sollten auch berücksichtigen, wie dringend die Daten benötigt werden. Auf Daten, die Informationen zu Warnzwecken bereitstellen, muss es schnellen Zugriff geben, daher sollten sie in einem schnellen Datenspeicher gehalten und so indiziert und strukturiert werden, dass die vom Warnsystem durchgeführten Abfragen optimiert sind. In einigen Fällen kann es für den Telemetriedienst, der die Daten auf jedem Knoten sammelt, notwendig sein, diese zu formatieren und lokal zu speichern, sodass eine lokale Instanz des Warnsystems schnell Probleme weitergeben kann. Die gleichen Daten können an den Speicherschreibdienst verteilt erden, der in den vorherigen Diagrammen gezeigt wurde und zentral gespeichert wird, wenn er für weitere Zwecke benötigt wird.

Informationen, die für eine durchdachtere Analyse verwendet werden, für die Berichterstellung und für das Erkennen historischer Trends, sind weniger dringend und können so gespeichert werden, dass Datamining und Ad-hoc-Abfragen unterstützt werden. Weitere Informationen finden Sie im Abschnitt [Unterstützung von heißer, warmer und kalter Analyse](#supporting-hot-warm-and-cold-analysis) weiter unten in diesem Dokument.

#### _Protokollrotation und Aufbewahrung von Daten_
Die Instrumentation kann erhebliche Datenmengen generieren. Diese Daten können an mehreren Stellen gehalten werden, von den rohen Protokolldateien, den Ablaufverfolgungsdateien und anderen Informationen, die in jedem Knoten erfasst werden, zur konsolidierten, bereinigten und partitionierten Ansicht der Daten im freigegebenen Speicher. In einigen Fällen können die ursprünglichen, rohen Quelldaten aus jedem Knoten entfernt werden, sobald die Daten verarbeitet und übertragen wurden. In anderen Fällen kann es notwendig oder einfach nützlich sein, die unformatierten Informationen zu speichern. Z. B. kann es möglicherweise besser sein, dass zu Debugzwecken generierte Daten in unformatierter Form vorliegen bleiben, sodass sie schnell verworfen werden können, sobald alle Fehler behoben wurden. Leistungsdaten haben häufig eine längere Lebensdauer, damit sie Leistungstrends erkennen und für die Kapazitätsplanung verwendet werden können. Die konsolidierte Ansicht dieser Daten bleibt in der Regel für einen begrenzten Zeitraum online, um schnellen Zugriff zu ermöglichen. Danach können diese Daten archiviert oder verworfen werden. Für die Messung und die Abrechnung des Kunden gesammelte Daten müssen möglicherweise dauerhaft gespeichert werden. Darüber hinaus kann es gesetzlich vorgeschrieben sein, dass für Überwachungs- und Sicherheitszwecke gesammelte Daten ebenfalls archiviert und gespeichert werden müssen. Diese Daten sind dazu vertraulich und müssen möglicherweise verschlüsselt oder auf andere Art geschützt werden, um Manipulationen zu vermeiden. Sie sollten niemals Informationen wie Benutzerkennwörter oder andere Informationen aufzeichnen, die verwendet werden können, um Identitätsdiebstahl zu begehen. Die Daten sollten von diesen Angaben bereinigt werden, bevor sie gespeichert werden.

#### _Downsampling_
Häufig ist dies zum Speichern von Verlaufsdaten sinnvoll, um langfristige Trends zu ermitteln zu können. Statt alte Daten in ihrer Gesamtheit zu speichern, kann ein Down-Sampling der Daten möglich sein, um die Auflösung zu reduzieren und Speicherkosten zu sparen. Anstatt beispielsweise Leistungsindikatoren minütlich zu speichern, können Daten, die über einen Monat alt sind, so konsolidiert werden, dass sie einen stündlichen Überblick geben.

### Bewährte Methoden für das Sammeln und Speichern von Protokollierungsinformationen
Die folgende Liste enthält Empfehlungen zum Erfassen und Speichern von Protokollierungsinformationen.

- Der Überwachungsagent oder der Datensammlungsdienst sollte als prozessunabhängiger Dienst ausgeführt werden können und einfach bereitzustellen sein.
- Die gesamte Ausgabe vom Überwachungsagent oder Datensammlungsdienst sollte ein agnostisches Format besitzen, das unabhängig vom Computer, Betriebssystem oder Netzwerkprotokoll ist. Geben Sie z. B. Informationen in einem selbstbeschreibenden Format wie JSON, MessagePack oder Protobuf anstelle von ETL/ETW aus. Mithilfe standardisierter Formate kann das System Verarbeitungspipelines erstellen: Komponenten, die Daten im vereinbarten Format lesen, transformieren und ausgeben, können einfach integriert werden.
- Der Prozess der Datenüberwachung und -sammlung muss ausfallsicher sein und darf keine kaskadierenden Fehlerbedingungen auslösen.
- Bei einem vorübergehenden Fehler beim Senden von Informationen an eine Datensenke sollte der Agent oder Datensammlungsdienst darauf vorbereitet sein, Telemetriedaten neu anzuordnen, sodass die neuesten Informationen zuerst gesendet werden (der Überwachungsagent/Datensammlungsdienst löscht ältere Daten möglicherweise oder speichert sie lokal und überträgt sie später zum Abfangen, je nach Ermessen).

## Analysieren von Daten und Diagnose von Problemen
Ein wichtiger Teil des Überwachungs- und Diagnoseprozesses ist die Analyse der gesammelten Daten, um einen Überblick über das Wohlbefinden des Systems zu erhalten. Sie sollten eigene KPIs und Leistungsindikatoren definiert haben und verstehen, wie Sie die Daten strukturieren können, die für Ihre Analysezwecke gesammelt wurden. Sie sollten auch verstehen, wie die in verschiedenen Metriken und Protokolldateien erfassten Daten korrelieren, da diese Informationen entscheidend für die Überwachung einer Sequenz von Ereignissen und bei der Diagnose auftretender Probleme sein können.

Wie im Abschnitt [Konsolidierung von Instrumentationsdaten](#consolidating-instrumentation-data) beschrieben, werden die Daten für jeden Teil des Systems i. d. R. lokal erfasst, müssen aber im Allgemeinen mit Daten kombiniert werden, die an anderen Standorten im System beteiligt sind. Diese Informationen erfordern eine sorgfältige Korrelation, um sicherzustellen, dass die Daten genau kombiniert werden. Die Verwendungsdaten für einen Vorgang können zum Beispiel einen Knoten zum Hosten einer Website umfassen, zu der ein Benutzer eine Verbindung herstellt, einen Knoten unter einem separaten Dienst, auf den als Teil des Vorgangs zugegriffen wird, und der Datenspeicher auf einem weiteren Knoten. Diese Informationen müssen miteinander verknüpft werden, um einen allgemeinen Überblick über die Verwendung von Ressourcen und der Verarbeitung für den Vorgang zu bieten. Eine Vorverarbeitung und Filterung von Daten kann auf dem Knoten auftreten, auf dem die Daten erfasst werden, während die Aggregation und Formatierung eher auf einem zentralen Knoten ausgeführt werden.

<a name="supporting-hot-warm-and-cold-analysis"></a>
### Unterstützung von heißer, warmer und kalter Analyse
Das Analysieren und Umformatieren von Daten zu Visualisierungs-, Berichterstellungs- und Alarmierungszwecken kann ein komplexer Prozess sein, der einen eigenen Satz von Ressourcen nutzt. Einige Formen der Überwachung sind zeitkritisch und erfordern eine sofortige Analyse der Daten, um wirksam zu sein. Dies bezeichnet man als _heiße Analysis_. Beispiele hierfür sind die für Warnungen und einige Aspekte der Sicherheitsüberwachung erforderlichen Analysen (z. B. Erkennen eines Angriffs auf das System). Erforderliche Daten für diese Zwecke müssen schnell verfügbar und für die effiziente Verarbeitung strukturiert sein. Manchmal es kann erforderlich sein, die Analyseverarbeitung auf die einzelnen Knoten zu verschieben, auf denen die Daten gehalten werden.

Andere Arten der Analyse sind weniger zeitkritisch und erfordern möglicherweise einige Berechnungen und eine Aggregation, sobald die Rohdaten empfangen wurde. Dies bezeichnet man als _warme Analysis_. Die Leistungsanalyse wird oft zu dieser Kategorie gezählt. In diesem Fall ist es unwahrscheinlich, dass ein isoliertes, einzelnes Leistungsereignis statistisch signifikant ist (es könnte auf einen plötzlichen Anstieg oder eine Störung zurückzuführen sein), während die Daten aus einer Reihe von Ereignissen ein zuverlässigeres Bild der Systemleistung darstellen sollten. Die warme Analyse kann auch zur Diagnose von Integritätsproblemen verwendet werden. Ein Integritätsereignis wird in der Regel von einer heißen Analyse verarbeitet und kann sofort eine Warnung auslösen. Ein Operator sollte den Gründen für das Integritätsereignis nachgehen, indem er die Daten vom warmen Pfad untersucht. Diese Daten sollten Informationen über die Ereignisse enthalten, die zum Problem führen, das das Integritätsereignis verursacht hat.

Einige Arten der Überwachung generieren eher langfristige Daten, und die Analyse kann zu einem späteren Zeitpunkt stattfinden, möglicherweise nach einem vordefinierten Zeitplan. In einigen Fällen müssen bei der Analyse komplexe Filterungen großer Datenmengen durchgeführt werden, die über einen Zeitraum erfasst wurden. Dies bezeichnet man als _kalte Analysis_. Die wichtigste Anforderung ist, dass die Daten sicher gespeichert werden, nachdem sie erfasst wurden. Z. B. erfordert die Nutzungsüberwachung ein genaues Bild des Systemzustands zu regelmäßigen Zeiten, allerdings müssen diese Statusinformationen nicht umgehend für die Verarbeitung bereitstehen, nachdem sie gesammelt wurden. Die kalte Analyse kann auch zum Bereitstellen von Daten für die vorausschauende Integritätsanalyse genutzt werden. Die historischen Informationen, die über einen angegebenen Zeitraum gesammelt wurden, können in Verbindung mit den aktuellen Integritätsdaten verwendet werden (abgerufen vom heißen Pfad), um Trends zu ermitteln, die zukünftig Integritätsprobleme verursachen könnten. In diesen Fällen kann es erforderlich sein, eine Warnung auszulösen, um Korrekturmaßnahmen einzuleiten.

### Korrelieren von Daten
Die von der Instrumentation erfassten Daten können eine Momentaufnahme des Systemstatus bereitstellen, allerdings ist der Zweck der Analyse, die Daten umsetzbar zu machen. Was hat z. B. ein ressourcenintensives E/A-Laden auf Systemebene zu einem bestimmten Zeitpunkt verursacht? Ist sie das Ergebnis einer großen Anzahl von Datenbankvorgängen? Schlägt sich dies in den Antwortzeiten der Datenbank nieder, der Anzahl der Transaktionen pro Sekunde und den Antwortzeiten der Anwendung am gleichen Verbindungspunkt? In diesem Fall könnte eine korrigierende Maßnahme sein, die Last zu reduzieren, indem Daten auf mehreren Servern partitioniert werden. Darüber hinaus können Ausnahmen durch einen Fehler in jeder beliebigen Ebene des Systems entstehen und oft eine Ausnahme in einer höheren Ebene auslösen. Aus diesen Gründen müssen Sie in der Lage sein, die verschiedenen Typen von Überwachungsdaten auf jeder Ebene zu korrelieren, um einen allgemeinen Überblick über den Systemzustand zu generieren sowie den Anwendungen, die darauf ausgeführt werden. Sie können dann anhand dieser Informationen Entscheidungen darüber fällen, ob das System akzeptabel funktioniert oder nicht, und bestimmen, was zur Verbesserung der Systemqualität getan werden muss.

Wie im Abschnitt [Informationen zum Korrelieren von Daten](#information-for-correlating-data) beschrieben, müssen Sie sicherstellen, dass die unformatierten Instrumentationsdaten genügend Kontext und Aktivitäts-ID-Informationen enthalten, um die erforderlichen Aggregationen für korrelierende Ereignisse zu unterstützen. Darüber hinaus werden diese Daten möglicherweise in verschiedenen Formaten gespeichert, und es kann erforderlich sein, die Informationen zu analysieren, um sie in ein standardisiertes Format zu Analysezwecken umzuwandeln.

### Problembehandlung und Diagnose von Problemen
Die Diagnose erfordert die Fähigkeit, die Ursache für Fehler oder unerwartetes Verhalten zu bestimmen, einschließlich der Durchführung der Ursachenanalyse. In der Regel erforderliche Informationen umfassen:

- Ausführliche Informationen aus Ereignisprotokollen und Ablaufverfolgungen, entweder für das gesamte System oder ein bestimmtes Subsystem in einem angegebenen Zeitraum.
- Vollständige Stapelüberwachungen, die aus Ausnahmen und Fehlern auf einer angegebenen Ebene resultieren, die entweder im System oder einem festgelegten Subsystem während einer angegebenen Zeitspanne auftreten.
- Absturzabbilder für fehlerhafte Prozesse an einer beliebigen Stelle im System oder für ein angegebenes Subsystem innerhalb eines angegebenen Zeitraums.
- Aktivitätsprotokolle mit den Aufzeichnungen der Vorgänge, die von allen Benutzern oder ausgewählten Benutzern während einer angegebenen Zeitspanne ausgeführt werden.

Das Analysieren von Daten für Problembehandlungszwecke erfordert häufig vertiefte technische Kenntnisse der Systemarchitektur und der verschiedenen Komponenten der Lösung. Demzufolge ist häufig ein hohes Maß manuellen Eingreifens erforderlich, um die Daten zu interpretieren, die Ursache der Probleme festzustellen und eine geeignete Strategie zu deren Korrektur vorzuschlagen. Möglicherweise ist es zweckdienlich, eine Kopie dieser Informationen im ursprünglichen Format zu speichern und sie für die kalte Analyse durch einen Experten verfügbar zu machen.

## Visualisieren von Daten und Auslösen von Warnungen
Ein wichtiger Aspekt eines jeden Überwachungssystems ist die Fähigkeit, die Daten so darzustellen, dass ein Operator Trends oder Probleme schnell erkennen kann und schnell informiert werden kann, wenn ein wichtiges Ereignis eingetreten ist, dass möglicherweise seine Aufmerksamkeit erfordert.

Die Darstellung von Daten kann unterschiedliche Formen annehmen, einschließlich der Visualisierung mithilfe von Dashboards, Warnungen und Berichtserstellung.

### Visualisierung mit Dashboards
Die gängigste Methode zum Anzeigen von Daten ist die Verwendung von Dashboards, die Informationen als eine Reihe von Diagrammen, Graphiken oder auf andere bildliche Weise darstellen können. Diese Elemente könnten parametrisiert werden, und ein Analyst sollte die wichtigen Parameter (z. B. den Zeitraum) für jede beliebige Situation auswählen können. Dashboards können hierarchisch organisiert werden. Dashboards der oberen Ebene bieten einen allgemeinen Überblick über die einzelnen Aspekte des Systems, aber mit der Möglichkeit, dass ein Operator die Einzelheiten untersuchen kann. Ein Dashboard, das z. B. den gesamten E/A-Datenträger für das System darstellt, sollte es einem Analytiker erlauben, die Daten eingehend zu untersuchen und die E/A-Raten für jeden einzelnen Datenträger zu ermitteln, um zu ermitteln, ob ein Gerät oder mehrere für eine unverhältnismäßig große Menge an Datenverkehr verantwortlich ist. Im Idealfall sollte das Dashboard auch verknüpfte Informationen anzeigen, wie z. B. die Quelle der jeweiligen Anforderung (der Benutzer oder die Aktivität), die dieses E/A erzeugt. Diese Informationen können dann verwendet werden, um zu bestimmen, ob (und wie) die Last gleichmäßiger über die Geräte verteilt werden soll und ob das System eine bessere Leistung hätte, wenn mehr Geräte hinzugefügt werden. Ein Dashboard könnte auch eine Farbcodierung oder andere visuelle Markierungen verwenden, um Werte anzuzeigen, die anomal sind oder außerhalb des erwarteten Bereichs stehen. Im vorherigen Beispiel könnte ein Datenträger mit einer E/A-Rate, die sich über längere Zeit der maximalen Kapazität nähert (ein heißer Datenträger) in rot hervorgehoben werden, ein Datenträger mit einer E/A-Rate, die zwischenzeitlich an der maximalen Grenze ausgeführt wird (ein warmer Datenträger) in gelb, und ein Datenträger mit normaler Nutzung in grün.

Beachten Sie, dass ein effektiv funktionierendes Dashboard über Rohdaten verfügen muss, mit dem es arbeiten kann. Wenn Sie Ihr eigenes Dashboardsystem erstellen oder ein Dashboard nutzen, das von einer anderen Organisation entwickelt wurde, müssen Sie wissen, welche Instrumentationsdaten Sie sammeln müssen, in welchem Granularitätsmaß und wie sie für die Nutzung durch das Dashboard formatiert werden sollten.

Ein gutes Dashboard zeigt nicht nur Informationen an, es bietet einem Analysten auch die Möglichkeit, Ad-hoc-Fragen darüber zu stellen. Einige Systeme bieten Verwaltungstools, mit denen ein Operator diese Aufgaben ausführen und die zugrunde liegenden Daten untersuchen kann. Alternativ kann es je nach verwendetem Repository, das die Informationen enthält, möglich sein, die Daten direkt abzufragen oder zur weiteren Analyse und Berichterstellung in Tools wie Microsoft Excel zu importieren.

> [AZURE.NOTE]Sie sollten den Zugriff auf Dashboards auf autorisierte Mitarbeiter beschränken. Die Informationen könnten kommerziell vertraulich sein. Sie sollten auch die zugrunde liegenden Daten schützen, die durch das Dashboard dargestellt werden, um Benutzer an deren Änderung zu hindern.

### Warnungen auslösen
Unter Warnungen versteht man den Prozess der Analyse der Überwachungs- und Instrumentationsdaten und dem Generieren einer Benachrichtigung, wenn ein wichtiges Ereignis erkannt wird.

Mit Warnungen kann man sicherstellen, dass das System fehlerfrei, reaktionsfähig und sicher bleibt. Sie sind ein wichtiger Teil jedes Systems, die den Benutzern Leistungs-, Verfügbarkeits- und Datenschutzgarantien zusichern. Auf die Daten muss möglicherweise sofort reagiert werden. Ein Operator muss unter Umständen über das Ereignis informiert werden, das die Warnung ausgelöst hat. Warnungen können auch zum Aufrufen von Systemfunktionen wie für die automatische Skalierung verwendet werden.

Warnungen hängen in der Regel von den folgenden Instrumentationsdaten ab:

- Sicherheitsereignisse. Ergeben die Ereignisprotokolle, dass wiederholte Authentifizierungs- und/oder Autorisierungsfehler auftreten, kann das System angegriffen werden, und ein Operator sollte informiert werden.
- Leistungsmetriken. Das System muss schnell reagieren, wenn eine bestimmte Leistungsmetrik einen angegebenen Schwellenwert überschreitet.
- Verfügbarkeitsinformationen. Wenn ein Fehler gefunden wird, kann es erforderlich sein, dass ein oder mehrere Subsysteme schnell neu gestartet werden, oder dass ein Failover zu einer Backup-Ressource geschieht. Wiederholte Fehler in einem Subsystem können auf schwerwiegendere Probleme hinweisen.

Operatoren können Warnungsinformationen über viele Übermittlungskanäle erhalten, wie z. B. E-Mail, Pager-Gerät oder SMS. Eine Warnung kann auch einen Hinweis darauf enthalten, wie kritisch die entstandene Situation ist. Viele Warnsysteme unterstützen Abonnentengruppen, und alle Operatoren, die Mitglieder derselben Gruppe sind, können dieselben Warnungen erhalten.

Ein Warnsystem sollten anpassbar sein, und die entsprechenden Werte aus den zugrunde liegenden Instrumentationsdaten könnten als Parameter angegeben werden. Dieser Ansatz ermöglicht es einem Operator, Daten zu filtern und sich auf die Schwellenwerte oder Kombinationen von Werten zu konzentrieren, die von Interesse sind. Beachten Sie, dass in einigen Fällen die unformatierten Instrumentationsdaten dem Warnsystem bereitgestellt werden könnten, während es in anderen Situationen günstiger wäre, die aggregierte Daten bereitzustellen (z. B. könnte eine Warnung ausgelöst werden, wenn die CPU-Auslastung für einen Knoten in den letzten zehn Minuten 90 % überschritten). Die dem Warnsystem zur Verfügung gestellten Einzelheiten sollten auch alle entsprechenden Zusammenfassungs- und Kontextinformationen enthalten. Diese Daten können die Möglichkeit vermeiden, dass falsch gemeldete Ereignisse eine Warnung auslösen.

### Berichterstellung
Die Berichterstellung wird verwendet, um eine allgemeine Übersicht über das System zu generieren, und kann historische Daten sowie aktuellen Informationen beinhalten. Anforderungen an die Berichterstellung selbst fallen in zwei breite Kategorien: die operative Berichterstellung für die Sicherheitsberichterstellung.

Die operative Berichterstellung umfasst in der Regel die folgenden Aspekte:

- Das Aggregieren von Statistiken, mit denen die Ressourcennutzung des gesamten Systems oder angegebener Subsysteme innerhalb eines angegebenen Zeitraums nachvollzogen werden kann.
- Das Identifizieren von Trends bei der Ressourcenverwendung für die Gesamtleistung des Systems oder die angegebenen Subsysteme während einer bestimmten Zeitspanne.
- Die Überwachung von Ausnahmen, die im gesamten System oder in bestimmten Subsystemen während einer angegebenen Zeitspanne aufgetreten sind.
- Die Bestimmung der Effizienz der Anwendung im Hinblick auf die bereitgestellten Ressourcen und das Verständnis, ob die Menge der Ressourcen (und die damit verbundenen Kosten) reduziert werden können, ohne die Leistung unnötig zu beeinträchtigen.

Die Sicherheitsberichterstellung befasst sich damit, die Verwendung des Systems durch den Kunden nachzuverfolgen. Dazu gehört:

- Die Überwachung von Benutzeraktionen. Dies erfordert das Aufzeichnen der einzelnen Anforderungen mit Datumsangaben und Uhrzeiten, die von jedem Benutzer ausgeführt wurden. Die Daten sollten strukturiert werden, damit ein Administrator die Reihenfolge der Vorgänge durch einen bestimmten Benutzer über einen bestimmten Zeitraum schnell rekonstruieren kann.
- Die Nachverfolgung der Ressourcennutzung durch den Benutzer. Dies erfordert eine Aufzeichnung davon, wie jede Benutzeranforderung auf die verschiedenen Ressourcen zugreift, aus denen das System besteht, und wie lange. Ein Administrator muss diese Daten nutzen können, um einen Verwendungsbericht pro Benutzer über einen angegebenen Zeitraum zu generieren, möglicherweise zu Abrechnungszwecken.

In vielen Fällen können Berichte in Batchprozessen nach einem bestimmten Zeitplan generiert werden (die Latenz ist normalerweise unproblematisch), aber sie sollten auch bei Bedarf auf Ad-hoc-Basis generiert werden können. Wenn Sie beispielsweise Daten in einer relationalen Datenbank speichern, wie z. B. der Azure SQL-Datenbank, können Sie ein Tool wie SQL Server Reporting Services zum Extrahieren und Formatieren der Daten verwenden und diese als eine Reihe von Berichten darstellen.

## Zugehörige Muster und Anleitungen
- Die Anleitung zur automatischen Skalierung beschreibt, wie der Verwaltungsaufwand gesenkt wird, da kein Operator mehr erforderlich ist, der die Leistung des Systems ständig überwacht und Entscheidungen über das Hinzufügen und Entfernen von Ressourcen trifft.
- Die [Health Endpunkt Überwachung Muster](https://msdn.microsoft.com/library/dn589789.aspx) beschreibt, wie Sie die funktionale Prüfungen innerhalb einer Anwendung implementiert wird, die verfügbar gemachten Endpunkte in regelmäßigen Abständen externe Tools zugreifen können.
- Das [Prioritätswarteschlangen](https://msdn.microsoft.com/library/dn589794.aspx)-Muster zeigt, wie Nachrichten in der Warteschlange priorisiert werden, sodass dringende Anforderungen empfangen und vor weniger dringenden Nachrichten verarbeitet werden.

## Weitere Informationen
- Der Artikel [Microsoft Azure Storage: Überwachung, Diagnose und Problembehandlung](storage-monitoring-diagnosing-troubleshooting.md) auf der Microsoft-Website.
- Der Artikel [Azure: Telemetrie-Grundlagen und Problembehandlung](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) auf der Microsoft-Website.
- Die Seite [Sammeln von Protokollierungsdaten mit Azure Diagnostics](https://msdn.microsoft.com/library/azure/gg433048.aspx) auf der Microsoft-Website.
- Die Seite [Konfigurieren von Diagnose für Azure Cloud Services und Virtual Machines](https://msdn.microsoft.com/library/azure/dn186185.aspx) auf der Microsoft-Website.
- Die Seiten [Azure Redis Cache](http://azure.microsoft.com/services/cache/), [Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), und [HDInsight](http://azure.microsoft.com/services/hdinsight/) auf der Microsoft-Website.
- Die Seite [Verwenden von Service Bus-Warteschlangen](http://azure.microsoft.com/) auf der Microsoft-Website.
- Der Artikel [SQL Server Business Intelligence auf virtuellen Azure-Computern](./virtual-machines/virtual-machines-sql-server-business-intelligence.md) auf der Microsoft-Website.
- Die Seite [Grundlegendes zur Überwachung von Warnungen und Benachrichtigungen in Azure](https://msdn.microsoft.com/library/azure/dn306639.aspx) auf der Microsoft-Website.
- Die Seite [Application Insights](app-insights-get-started/) auf der Microsoft-Website.

<!---HONumber=August15_HO9-->