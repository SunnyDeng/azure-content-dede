<properties
	pageTitle="Kontextabhängige Beispiele bewährter Methoden für die Implementierung von Vorlagen"
	description="Beispiele für Azure-Ressourcen-Manager-Vorlagen, die bewährte Methoden veranschaulichen."
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="mmercuri"/>

# Kontextabhängige Beispiele bewährter Methoden für die Implementierung von Vorlagen

Dieses Thema enthält 7 kontextbezogene Beispiele für die Implementierung Ihrer Azure-Ressourcen-Manager-Vorlagen. Eine Übersicht über die Prinzipien, die in diesen Beispielen veranschaulicht werden, finden Sie unter [Bewährte Methoden für das Entwerfen von Azure-Ressourcen-Manager-Vorlagen](best-practices-resource-manager-design-templates.md).

Dieses Thema ist Teil eines umfangreicheren Whitepapers. Um den vollständigen Artikel zu lesen, laden Sie [World Class ARM Templates Considerations and Proven Practices](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf) herunter.

## Verschieben einer Vorlage aus dem Bereich "Funktionalität" in eine Vorlage aus dem Bereich "Komplettlösung"

Das Muster für die Entwicklung einer Vorlage aus dem Bereich "Funktionalität" wurde bereits zuvor erläutert. Möglicherweise stellen Sie sich die Frage, ob es Unterschiede gibt, wenn Sie diese Vorlage aus dem Bereich "Funktionalität" eigenständig oder als Teil einer Vorlage aus dem Bereich "Komplettlösung" verwenden.

Angenommen, es gibt eine Vorlage mit Technologiefokus für die Bereitstellung von SQL Server als Funktionalität. Worin bestehen die Unterschiede, sofern vorhanden, wenn diese Vorlage unabhängig oder als Teil einer Vorlage aus dem umfassenderen Bereich der Komplettlösungen verwendet wird, bei der SQL Server zur Unterstützung einer Webanwendung genutzt wird?

Bei der Untersuchung dieses Szenarios muss ein Blick auf die Anzahl der beteiligten Ressourcen geworfen werden. Für eine stabile Implementierung enthält Ihre Vorlage aus dem Bereich "Funktionalität" nicht bloß ein Speicherkonto und eine einzelne VM mit einer Installation von SQL Server. Mit einer stabilen Vorlage aus dem Bereich "Funktionalität" werden mehrere VMs mit SQL Server für hohe Verfügbarkeit bereitstellt. Für einige Funktionen, z. B. Analysis Services, verfügt Ihre Topologie wahrscheinlich über Active Directory, das ebenfalls bereitgestellt wurde.

Zwei wichtige Aspekte bei diesem Szenario sind der Lebenszyklus der Nutzung von SQL Server und die rollenbasierte Zugriffssteuerung, die dafür gelten soll. Wird insbesondere SQL Server mit dem Rest der Lösung aktualisiert oder gelöscht oder unterscheidet sich dessen Lebenszyklus von der Lösung oder von anderen Teilen der Lösung? Wenn sich der Lebenszyklus unterscheidet, sollten Sie erwägen, SQL Server in einer anderen Ressourcengruppe zu platzieren.

Ein weiterer Aspekt ist, wie Sie die rollenbasierte Zugriffssteuerung Ihrer Lösungsvorlage für die Funktion "SQL Server" zuweisen möchten. Basierend darauf, wie Sie die rollenbasierte Zugriffssteuerung auf Ihre Topologie anwenden möchten, können Sie sich je nach Anforderungen für unterschiedliche Ressourcengruppen entscheiden. Sie können die rollenbasierte Zugriffssteuerung auf Ressourcenebene anwenden. Doch angesichts der Anzahl von Ressourcen für die Lösungsvorlage für die Funktion "SQL Server" sollten Sie eine gesonderte Ressourcengruppe mit angewendeter rollenbasierter Zugriffssteuerung in Erwägung ziehen.

Ein weiterer Aspekt ist eine Überprüfung der Lösungsvorlage für die Funktion "SQL Server" dahingehend, ob diese derzeit bestimmte Ressourcen selbst erstellt oder das BYOR-Modell (Bring Your Own Resources, eigene Ressourcen hinzufügen) unterstützt. Beim BYOR-Modell erlaubt die Vorlage aus dem Bereich "Funktionalität" Ihrer Vorlage das Wiederverwenden bereits vorhandener Ressourcen. Typische Beispiele sind ein Speicherkonto, virtuelles Netzwerk oder eine Verfügbarkeitsgruppe. Wenn ein BYOR-Ansatz in Ihrer Vorlage aus dem Bereich "Funktionalität" nicht vorgesehen ist, können Sie sie mithilfe des Ansatzes ändern, der weiter oben in diesem Dokument für Vorlagen für optionale Ressourcen definiert wurde. In diesem Fall weist Ihre Vorlage aus dem Bereich "End-to-End"-Lösung eine Vorlage für freigegebene Ressource mit diesen allgemeinen Ressourcen auf. Die Vorlage aus dem Bereich "Funktionalität" wird anschließend so erweitert, dass diese Ressourcen optional unterstützt werden. Dadurch ergibt sich eine bessere Vorlage aus dem Bereich "Funktionalität", da diese nun entweder unabhängig oder als Teil einer Zusammensetzung verwendet werden kann.

Bei der Prüfung, ob das Speicherkonto in die Vorlage aus dem Bereich "Komplettlösung" einbezogen werden sollte, muss auch die rollenbasierte Zugriffssteuerung erneut überprüft werden. Müssen Sie insbesondere sicherstellen, dass die rollenbasierte Zugriffssteuerung auf diese bestimmte Ressource angewendet wird? Falls ja und erwartet wird, dass dies gilt, wenn die Vorlage übergeben wird, dann wird eine Vertrauensebene nicht nur in den Lösungsblock, sondern für alle Benutzer eingeführt, die diese optional der Vorlage aus dem Bereich "Funktionalität" hinzufügen möchten, wenn diese unabhängig genutzt wird. Wenn die rollenbasierte Zugriffssteuerung wichtig ist, sollten Sie erwägen, ob Sie aus dieser Vorlage eine optionale Vorlage innerhalb der Lösungsvorlage aus dem Bereich "Funktionalität" machen oder ihre Erstellung mit der erforderlichen rollenbasierten Zugriffssteuerung innerhalb der Vorlage aus dem Bereich "Funktionalität" anfordern.

Wenn Sie sich für das Platzieren dieser Ressourcen in verschiedenen Ressourcengruppen entscheiden, können Sie auch mit Ressourcenlinks arbeiten, um die Beziehungen zwischen den Ressourcen zu bestimmen, selbst wenn sich die Ressourcen in mehreren Ressourcengruppen befinden.

## Erstellen einer Vorlage aus dem Bereich "Komplettlösung" mit mehreren Vorlagen aus dem Bereich "Funktionalität"

Dies ist größtenteils eine Obermenge des vorherigen Beispiels. In diesem Szenario verfügt eine Organisation über mehrere Vorlagen aus dem Bereich "Funktionalität" für eine Gruppe von Datentechnologien, wie z. B. Kafka, Apache Hadoop, Apache Spark und Apache Storm, die einem Lösungsblock zusammengefasst werden sollen. Der so erzeugte Lösungsblock verwendet diese Vorlagen aus dem Bereich "Funktionalität" sowie einen freigegebenen Speicher und ein virtuelles Netzwerk mit spezifischen Subnetzzuweisungen.

Über die erforderlichen Vorlagen aus dem Bereich "Funktionalität" hinaus sind für die Lösung weitere Ressourcen erforderlich, und seien es nur Skripts, mit denen die Vorlagen aus dem Bereich "Funktionalität" zusammengefügt und konfiguriert werden.

Für diesen Fall gilt, dass ein freigegebenes virtuelles Netzwerk und ein freigegebenes Speicherkonto vorhanden sind. Um dies zu unterstützen, müssen Sie diese Elemente in Ihrer Vorlage aus dem Bereich "End-to-End"-Lösung einer Vorlage für freigegebene Ressourcen hinzufügen und sicherstellen, dass ein BYOR-Ansatz in den Vorlagen aus dem Bereich "Funktionalität" unterstützt wird. Wenn dies nicht der Fall ist, können Sie Ihre Vorlagen aus dem Bereich "Funktionalität" wie im vorherigen Beispiel beschrieben entsprechend anpassen.

Für die zusätzlichen Ressourcen, die Sie hinzufügen möchten, befolgen Sie eine Obermenge des Musters für die Erstellung einer einzelnen Vorlage aus dem Bereich "Funktionalität". In diesem Fall fügen Sie eine Vorlage für freigegebene Ressourcen, Vorlagen für optionale Ressourcen, Vorlagen für Memberknoten und Konfigurationen des gewünschten Status (Skripts, Chef, Puppet, PowerShell DSC) für die neuen Ressourcen hinzu. Sofern es Abhängigkeiten gibt, verwenden Sie zur Optimierung nach Möglichkeit implizite Verweise anstelle von "dependsOn"-Elementen, um potenzielle vereinzelte Abhängigkeiten zu vermeiden, die sich auf die Parallelität (und das Tempo) Ihrer Bereitstellung auswirken können. Außerdem müssen Sie den Lebenszyklus dieser Ressourcen, die Überlegungen zur rollenbasierten Zugriffssteuerung und Abhängigkeiten berücksichtigen, um zu bestimmen, ob diese in verschiedenen Ressourcengruppen platziert werden sollten.

Beim Hinzufügen freigegebener Ressourcen, wie z. B. dem freigegebenen Speicherkonto, sollten Sie auch überprüfen, ob eine Ressourcensperre dafür erforderlich ist, was dazu beitragen kann, unbeabsichtigte Löschvorgänge zu vermeiden.

Wenn Sie neue Ressourcen hinzufügen, sollten Sie auch prüfen, ob Ressourcen, die der Vorlage aus dem Bereich "End-to-End"-Lösung hinzugefügt werden, da eigene Vorlagen aus dem Bereich "Funktionalität" isoliert werden könnten. Ist dies der Fall, sollte dies unbedingt für eine weitere Aufgliederung erwägt werden, die Vorteile für sowohl die Wiederverwendung als auch Tests bringen kann.

Bei einer Integration in Ihre Lösungsblöcke bestehen Ihre nächsten Aufgaben (wie im vorherigen Beispiel angegeben) im Prüfen, ob sich der Lebenszyklus der einzelnen Vorlagen aus dem Bereich "Funktionalität" sich von dem der übergeordneten Lösung unterscheidet. Ferner ist zu prüfen, ob Anforderungen an die rollenbasierte Zugriffssteuerung es erfordern, diese auf getrennte Ressourcengruppen zu verteilen.

Schließlich sollten Sie prüfen, ob Sie Links zwischen den Ressourcen definieren und abfragen können möchten. Falls ja, ermöglichen Ressourcenlinks Ihnen dies für Ihre gesamte Vorlage aus dem Bereich "Komplettlösung", auch wenn diese sich über mehrere Ressourcengruppen erstreckt.

## Erstellen einer Vorlage aus dem Bereich "End-to-End"-Lösung mit unterstütztem Ein/Aus-Muster

Dieses Szenario ist eine Variante des vorherigen. In diesem Fall extrahiert der Kunden im Laufe des Tages in festen Abständen Daten aus einem lokalen System. Der Kunde hat eine Datenpipeline zum Verarbeiten dieser eingehenden Daten und einen relationalen Datenspeicher, in dem die Daten für Abfragen stets verfügbar sind. Da die Cloud ein Modell mit nutzungsbasierter Bezahlung bietet, möchte der Kunde die Datenpipeline nur in den Intervallen betreiben, in denen Daten zur Verarbeitung eingehen.

Zur Datenpipeline gehört eine SQL Server-Instanz, die die verarbeiteten Daten empfängt und für Abfragen verfügbar macht. Der Kunde möchte die Erfassungs- und Verarbeitungskomponenten der Pipeline gemäß einem festen Zeitplan ein- und ausschalten. Die SQL Server-Instanz soll hingegen stets verfügbar sein.

In diesem Szenario scheint es wesentliche Unterschiede beim Lebenszyklus zu geben. Möglicherweise gibt es einige weitere Aspekte, die der Kunde nicht angesprochen hat, die aber geprüft werden sollten.

Wie beschrieben bleibt die SQL Server-Bereitstellung aktiv, während andere Ressourcen erstellt und wieder gelöscht werden. Diese Komponenten werden anfänglich zusammen bereitgestellt. Doch anschließend werden einige Elemente der Vorlage gelöscht und gemäß einem anderen Lebenszyklus erstellt. Diese können in verschiedenen Ressourcengruppen isoliert werden oder in derselben Ressourcengruppe verbleiben, wobei eine Ressourcensperre für SQL Server-Ressourcen aktiviert wird. Da speziell SQL Server (wie in den vorherigen Beispielen beschrieben) wahrscheinlich als eine größere Gruppe von Ressourcen dargestellt wird, empfiehlt sich eine Herausnahme in eine eigene Ressourcengruppe.

Der andere Aspekt ist, dass der Kunde zwar gesagt hat, dass der Rest der Datenpipeline gemäß einem Zeitplan ein- und ausgeschaltet werden soll, aber ggf. nicht das inkonsistente Verhalten von Berichterstellungssystemen berücksichtigt hat. Die geplante Übermittlung von Daten von Drittanbietern erfolgt nicht immer pünktlich, denn möglicherweise ist keine Netzwerkverbindung für einen bestimmten Zeitraum verfügbar, weichen die Uhren auf lokalen oder cloudbasierten Servern voneinander ab, erfolgen Uhrzeitänderungen nicht wie erwartet usw. Es sollte geprüft werden, ob Ihr Erfassungsmechanismus auch mit einem Ein/Aus-Muster arbeiten sollte und, falls ja, ob der dazugehörige Lebenszyklus größer als der der Verarbeitungskomponenten sein sollte.

Bei Verwendung eines verwalteten Diensts wie Azure Data Factory oder Event Hub ist dies weniger problematisch, da deren Betriebs- und dazugehörige Abrechnungsmodelle sie bequem für die Erfassung Ihrer Daten und deren Ablage im Speicher verfügbar machen. Bei Verwendung einer anderen Technologie wie z. B. Kafka, die Sie auf einem virtuellen Computer bereitgestellt haben, möchten Sie sich den Lebenszyklus dahingehend ansehen, wie Sie diese für die Erfassung erforderliche Technologie und die dazugehörigen Speicherkonten zur Verfügung stellen. Dies kann dazu führen, dass die Erfassungs- und Verarbeitungsressourcen basierend auf ihrem Lebenszyklus in unterschiedlichen Ressourcengruppen platziert werden.

## Unterstützen unterschiedlicher Umgebungen innerhalb eines Abonnements

Um Dienste effektiv bereitzustellen, gelten in vielen Unternehmen verschiedene Anforderungen hinsichtlich Skalierung sowie an die abrechnungs- und zuständigkeitsbezogene und die geografische Isolierung. Beim Entwurf von Diensten für Azure haben Unternehmen zum Erfüllen dieser Anforderungen bislang mit der Abonnementpartitionierung gearbeitet.

Der Ressourcen-Manager lockert die Einschränkungen der Anzahl von Ressourcen eines bestimmten Typs, die innerhalb eines Abonnements bereitgestellt werden können, und führt außerdem Ressourcengruppen, rollenbasierte Zugriffssteuerung und Überwachung ein. Durch eine Kombination dieser Faktoren können Unternehmen Ressourcengruppen für die Partitionierung nutzen. Dadurch können sie ihre Anforderungen erfüllen und das Ausmaß der ggf. erforderlichen Abonnementpartitionierung verringern.

In diesem Abschnitt werden die Anforderungen für diese Art von Umgebungen untersucht. Außerdem wird erklärt, wie Umgebungen bereitgestellt werden, die diese Anforderungen mithilfe von Azure-Ressourcen-Manager (ARM) erfüllen.

### Überlegungen zur Isolierung

In diesem Abschnitt werden allgemeine Kundenmotive für die abrechnungs- und zuständigkeitsbezogene sowie geografische Isolierung ausführlicher behandelt.

#### Isolierung der Umgebung

Dienstbesitzer haben den Wunsch, ihre unterschiedlichen Umgebungen zu isolieren. Durch eine Isolierung der einzelnen Umgebungen haben Teams die Möglichkeit einer präzisere Steuerung des Zugriffs auf die Umgebungen. Entwicklungsumgebungen sind, was den Zugriff angeht, ggf. offener. Doch wenn sich die Entwicklung der Produktionsreife nähert, wird die Anzahl der Benutzer (und der für die Automatisierung verwendeten Systemkonten) verringert, um die Einhaltung von Vorschriften zu unterstützen und Risiken zu minimieren.

#### Isolierung für Abrechnungszwecke – Vergleich der Entwicklung und Ausführung eines Diensts

Um die Umsatz- und Betriebskosten genau zu ermitteln, möchten Geschäftsinhaber die Kosten für die Entwicklung und Erstellung eines Diensts und die Kosten für den Betrieb von Diensten voneinander trennen.

Als Obermenge der zuvor beschriebenen Isolierung von Umgebungen ist die Absicht die Konsolidierung von Entwicklung und Tests für eine individuelle und/oder aggregierte Abrechnung für ersteres, während die Produktion für letzteres unabhängig bleibt.

#### Isolierung zu Abrechnungszwecken – Mehr Transparenz und Verantwortlichkeit bei den Kosten für die Nutzung von Diensten

Die Isolierung zu Abrechnungszwecken dient auch zum Erzielen von Transparenz bei den Kosten der Plattformnutzung durch bestimmte Teams und zum Ermöglichen geeigneter Zuständigkeitsebenen.

Wenngleich die Cloud flexibel ist und ein nutzungsbasiertes Zahlungsmodell bietet, sind einige Entwickler, die bislang mit einem Nicht-Cloudmodell gearbeitet haben, bei dem Hardware beschafft und in Firmenbesitz übergeht, weniger damit vertraut. Beim Nicht-Cloudmodell gab es physische Einschränkungen in Bezug auf die Anzahl der "Computer", die eingeschaltet werden konnten, und nur beschränkte Anreize, Ressourcen zu verringern oder auszuschalten, wenn diese nicht aktiv waren. Die Beschaffung dieser dedizierten Hardware erfolgte in vielen Fällen nicht durch die Entwickler, die diese nutzten.

Dadurch, dass Abonnements isoliert und die Zuständigkeit für diese Abonnements bestimmten Teams zugewiesen werden kann, können Dienstbesitzer diese Art von Abonnementpartitionierung zum Fördern und Erzwingen gewünschter Verhalten zu ihrem Vorteil nutzen.

#### Gebietsspezifische Isolierung – Bereitstellungen, die den Gesetzen eines bestimmten geografischen Gebiets unterliegen

In bestimmten Kontexten gelten Anforderungen, die für ein bestimmtes Gebiet vorgesehene bereitzustellende Dienste berücksichtigen müssen, und zwar hinsichtlich der Einhaltung geltender Vorschriften.

Während ein Dienst globaler Natur sein mag, können Bereitstellungen, die sich in bestimmten Gebieten befinden oder diesen Dienste zur Verfügung stellen, Vorgaben für das Betriebspersonal unterliegen. Beispielsweise können zum Betrieb dieser Dienste Personen vorgeschrieben sein, die Bürger eines bestimmten Lands oder eines geopolitischen Raums (z. B. EU) sind und/oder bestimmte Überprüfungen ihres Hintergrunds bestanden haben.

Eine geografische Isolierung bietet auch Vorteile in Bezug auf die Nutzung neuer Plattformdienste und -funktionen. In einigen Ländern, wie z. B. China, steht möglicherweise nur eine Teilmenge der Plattformdienste zur Verfügung und/oder wurde die Bereitstellung von Plattformdiensten verzögert.

Dank der geografische Isolierung haben Teams die Möglichkeit, ihre Dienste so weiterzuentwickeln, dass neue oder verbesserte Plattformdienste und -funktionen genutzt werden, wo sie zur Verfügung stehen.

### Überlegungen zur Vorschrifteneinhaltung

Dienste können in mehreren Regionen und für mehrere Branchen zur Verfügung gestellt werden. Diese Zielgruppen verfügen häufig über vertrauliche Daten oder Prozesse, die in ihren Anwendungen enthalten sind, und es gelten dazugehörige Vorschriften, die auf deren Schutz und die Überwachung ihrer Nutzung ausgelegt sind.

#### Trennung von Rollen und Aufgaben

Die Trennung von Rollen und Aufgaben ist eine wesentliche Voraussetzung dafür, dass interne Dienste interne Richtlinien befolgen. Viele kommerzielle Dienste verlangen dies auch, um gesetzliche und Branchenvorschriften einzuhalten. Dienste müssen den Zugriff auf Dienste und deren zugrunde liegenden Ressourcen unter bestimmten Umständen auf autorisierte Rollen beschränken. Viele Dienste verfügen über ein integriertes Gerüst für die Bereitstellung zweier Funktionen: rollenbasierte Zugriffssteuerung und Überwachung.

#### Anwendungsfälle für die rollenbasierte Zugriffssteuerung

Wenn die Einhaltung von Vorschriften wichtig ist, muss der Zugriff auf bestimmte Ressourcen beschränkt werden können.

Wenn Sie sich beispielsweise sensible Daten in vielen Szenarien ansehen, in denen die Einhaltung von Vorschriften eine Rolle spielt, z. B. Patientenakten, Finanzdaten, Steuerunterlagen usw., ist es wichtig, die Anzahl der Personen mit Zugriffs-, Anzeige- oder Bearbeitungsrechten für die Daten auf diejenigen zu begrenzen, die den Zugriff für ihre Aufgaben in der Organisation benötigen.

Die rollenbasierte Zugriffssteuerung bietet einer Person, einem System oder einer Gruppe Zugriff auf bestimmte Ressourcen unter angegebenen Bedingungen.

#### Überwachung

Zusätzlich zur von der rollenbasierten Zugriffssteuerung ermöglichten Einschränkung des Zugriff müssen Organisationen auch den Zugriff auf die Ressourcen und die Interaktion mit diesen überwachen.

### Implementierung mit Azure-Ressourcen-Manager

Zuvor mussten Organisationen mit der Abonnementpartitionierung arbeiten, um diese Ziele zu erreichen. Wenngleich möglich, war dies nicht ideal. Da die Erstellung eines Abonnements gewissermaßen eine gewerbliche Aktivität ist, stellte die Dienstverwaltungs-API keinen Mechanismus zur Verfügung, gemäß dem neue Abonnements automatisch erstellt oder gelöscht werden, weshalb Abonnements manuell erstellt werden mussten. Die resultierende Anzahl von Abonnements kann erheblich anwachsen. Für sehr große Dienste, wie z. B. die kommerziellen Dienste von Microsoft selbst, kann diese Anzahl über 1000 Abonnements betragen. Dies führt häufig zur Erstellung eines benutzerdefinierten Gerüsts zum Erstellen und Verwalten von Abonnements für eine Organisation.

Mit dem Azure-Ressourcen-Manager ist die Bereitstellung mehrerer Umgebungen innerhalb eines Abonnements wesentlich einfacher. Der ARM lockert die vorherigen festen Obergrenzen für Ressourcen, die im vorherigen Modell galten, wurde der Bedarf an Partitionierung aufgrund von Ressourceneinschränkungen erheblich reduziert wird.

Umgebungen können in Ressourcengruppen platziert werden, für die eine bestimmte rollenbasierte Zugriffssteuerung gelten kann, sodass Sie für eine Isolierung von Umgebungen sorgen können. In Szenarien, in denen die geografische Isolierung erforderlich ist, kann dies auch mithilfe von Ressourcengruppen erfolgen. Da sich Ressourcengruppen über mehrere geografische Gebiete erstrecken können, kann eine spezifische Isolierung für ein oder mehrere Gebiete erzielt werden.

Sie können Ressourcen und Ressourcengruppen Kategorien zuweisen, die für Abrechnungs- und Zusammenfassungsübersichten zum Ermöglichen einer abrechnungsbezogenen Isolierung genutzt werden können. Sie können Kategorien verwenden, um den Umgebungstyp (Forschung, Bildung, Entwicklung, Test, Produktion), die verantwortliche Abteilung oder die Person (Personalabteilung, Finanzabteilung, Hans Schmitz, Johanna Müller) zu bestimmen.

Die Überwachungsfunktion ist Teil der zugrunde liegenden Standardfunktionen von Azure-Ressourcen-Manager und kann an zentraler Stelle eingesehen werden.

Endkunden benötigen in Azure Active Directory registrierte Konten, die für die Authentifizierung und die rollenbasierte Steuerung des Zugriffs auf die Umgebung und die Ressourcen verwendet werden.

#### Optimieren der Dichte

Während die Ressourcengrenzen in Azure-Ressourcen-Manager gelockert wurden, gelten dennoch weiter Einschränkungen. Über das Erstellen der Umgebungen selbst hinaus sollten Sie versuchen, innerhalb von Abonnements eine hohe Dichte von Umgebungen zu erzielen. Die Bereitstellung einer Umgebung entspricht der Bereitstellung von Kapazität für eine Person oder Organisation, und Sie sollten prüfen, welche "T-Shirt-Größe(n)" Sie zur Verfügung stellen möchten. Bestimmen Sie insbesondere die Varianten zwischen Kunden der Typen S, M, L und XL hinsichtlich der benötigten Ressourcen.

Sie können auch verschiedene Abonnements für unterschiedliche T-Shirt-Größen verwenden, um eine höhere Dichte zu erreichen. So können z. B. von einem angegebenen Abonnement 1000 Umgebungen der Größe S, 500 Bereitstellungen der Größe M, 100 Bereitstellungen der Größe L und 10 Bereitstellungen der Größe XL unterstützt werden. Da für mehrere Abonnements nicht mehr abgerechnete Kosten anfallen, können Sie die verschiedenen Größen in unterschiedlichen Abonnements isolieren, um eine maximale Dichte zu erreichen. Dies kann erfolgen, während die Anzahl von Abonnements relativ moderat und einfach verwaltbar gehalten wird.

Eine wichtige Überlegung ist die Festlegung, ob Sie bereit sind, einem Kunden das Erhöhen oder Ändern seiner Bereitstellungsgröße zu erlauben, und falls ja, wie Sie dies unterstützen möchten.

Ein möglicher Ansatz ist, einem Kunden den Erwerb zusätzlicher Kapazität in seiner vorhandenen Ressourcengruppe zu ermöglichen. Dies ist technisch leicht realisierbar, wirkt sich aber auf die Dichte aus. Anstelle klar definierter Größen für alle Kunden sorgt dies für ein Maß an Veränderlichkeit, das bei der Optimierung der Dichte mehr Verwaltungsaufwand verursacht. Wenn jede Umgebung vom Typ S die Größe X hat, können Sie problemlos vorab berechnen, wie viele Umgebungen vom Typ S für eine optimale Dichte in ein Abonnement passen. Wenn Kunden das Anpassen der Umgebung ermöglicht wird, ist das Ergebnis eine unvorhersehbare Anzahl von Varianten und Mengen von Umgebungen, die X, X+1, X+2 usw. sein kann. Bei diesem Grad von Veränderlichkeit erzielen Sie eine geringe Dichte, da Sie innerhalb eines Abonnements Kapazität reservieren müssten, um diese Varianten zu unterstützen.

Wenngleich möglich, ist dies kein idealer allgemeiner Ansatz, da dadurch weniger Dichte erzielt wird und ein höherer Verwaltungsaufwand erforderlich ist. Für größere Umgebungen kann dies eine besser geeignete Option sein. Da weniger dieser Umgebungen vom Typ L und XL in einem Abonnement abgelegt werden, können Sie in einem Abonnement Wachstum besser unterstützen.

Ein anderer Ansatz ist, dass die Umgebung des Kunden mit der aktuellen Größe gelöscht und eine neue Umgebung mit einer anderen Größe erstellt wird. Wenngleich dies für einige Szenarien nicht geeignet ist, funktioniert es gut für Umgebungen, die vorübergehend genutzt werden, z. B. Entwicklungs- und Testumgebungen.

Als Nächstes empfiehlt es sich, dem Kunden die Möglichkeit zu bieten, eine größere Umgebung einzurichten und die Migration in diese Umgebung anschließend selbst zu verwalten. Beispielsweise kann ein Kunde, der über eine SQL Server-Bereitstellung in einer kleinen Umgebung verfügt, eine Umgebung der Größe M erwerben und ist anschließend selbst für die Übertragung von Daten und den benutzerdefinierten Zustand verantwortlich.

Eine andere Möglichkeit ist das Bereitstellen eines verwalteten Diensts, in dem dieser Übergang von einer Größe zu einer anderen erfolgt. Dies ist freilich komplizierter, doch je nach Workloads und Kunden kommt dieser Ansatz für Ihre Organisation ggf. in Frage.

## Bereitstellen von Umgebungen bei zusätzlichen Einschränkungen durch Kundenrichtlinien

In bestimmten Organisationen gelten zusätzliche Anforderungen und Richtlinien für die Umgebungen, die bereitgestellt werden. Dies sind insbesondere Richtlinien zum Einschränken der Ports, die extern verfügbar gemacht werden, und möglicherweise Richtlinien, die eine Überwachung des ein- und ausgehenden Datenverkehrs in der Umgebung erfordern. Hinsichtlich Unterstützbarkeit und Kosten gelten möglicherweise weitere Einschränkungen für die Ressourcen, die Endkunden erstellen, aktualisieren oder löschen können. Für die Organisation, die die Umgebung bereitstellt, benötigen sie für Unterstützung normalerweise auch Zugriff auf das Abonnement.

In Anlehnung an das vorherige Szenario erfordert dies zudem das Hinzufügen bestimmter Ressourcen, für die zusätzliche Einschränkungen dahingehend gelten, wer Ressourcen eines bestimmten Typs erstellen darf und wer nicht.

Ob ein Benutzer bestimmte Ressourcen erstellen, aktualisieren oder löschen darf, kann mithilfe der rollenbasierten Zugriffssteuerung eingeschränkt werden. Zu den Beispielen zählt eine Organisation, die ein bestimmtes Netz aus VNETs und möglicherweise Subnetze benötigt, die vom Endkunden nicht aktualisiert oder gelöscht werden können.

Ressourcensperren können aktiviert werden, um festzulegen, dass Ressourcen schreibgeschützt sind oder nicht gelöscht werden können. Die rollenbasierte Zugriffssteuerung kann verwendet werden, um Benutzern oder Dienstprinzipalen das Anwenden bestimmter Aktivitäten auf eine Ressource oder Ressourcengruppe zu erlauben.

Wenn die Organisation verlangt, dass bestimmter Datenverkehr, z. B. zwischen den Ebenen der Anwendung, zunächst einen Vermittler wie ein virtuelles Netzwerkgerät durchläuft, müssen benutzerdefinierte Routen verwendet werden.

Ein virtuelles Gerät ist letztlich nur ein virtueller Computer, der eine Anwendung zur Verarbeitung des Netzwerkverkehrs ausführt, z. B. eine Firewall oder ein NAT-Gerät. Verschiedene Drittanbieter stellen virtuelle Netzwerkgeräte in Azure bereit. Zudem können Organisationen ihre eigenen einsetzen.

Bei Verwenden eines eigenen virtuellen Geräts kann eine Organisation vorhandenen Code wiederverwenden, der ggf. bereits in ihrer lokalen Umgebungen verwendet wird. Dieser virtuelle Computer muss eingehenden Datenverkehr empfangen können, der nicht an ihn selbst adressiert ist. Damit ein virtueller Computer an andere Ziele gerichteten Datenverkehr empfangen kann, müssen Sie auf dem virtuellen Computer die IP-Weiterleitung aktivieren.

Wie bei den vorherigen Beispielen müssen Einschränkungen hinsichtlich Ressourcenlebenszyklus und rollenbasierter Zugriffssteuerung überprüft und als Teil Ihrer Strategie für Ressourcengruppen berücksichtigt werden.

## Schützen von Ressourcen gegen interne Angreifer

Ein Anliegen einer Organisation ist möglicherweise der Schutz ihrer Ressourcen und Vorlagen vor internen Angreifern.

Ein Beispiel hierfür ist eine Bank, die sicherstellen möchte, dass ein böswilliger Softwareentwickler oder Mitarbeiter der IT-Abteilung keine Änderungen vornehmen oder Schlüsselinformationen extrahieren darf, um zu verhindern, dass Daten für kriminelle Zwecke genutzt werden.

Ein typisches Unternehmensszenario ist das Einrichten einer kleinen vertrauenswürdigen Gruppe mit Zugriff auf wichtige geheime Informationen in den bereitgestellten Workloads und einer größeren Gruppe von Dev/Ops-Mitarbeitern, die VM-Bereitstellungen erstellen oder aktualisieren können.

Azure Key Vault kann mit ARM genutzt werden, um geheime VM-Schlüssel und -Zertifikate zu orchestrieren und zu speichern.

Eine bewährte Methode ist das Vorhalten separater ARM-Vorlagen für die Erstellung von Tresoren (mit den geheimen Schlüsseln) und Bereitstellung der virtuellen Computer (mit URI-Verweisen auf die in den Tresoren enthaltenen Schlüssel).

Im Schlüsseltresor gespeicherte geheime Schlüssel unterliegen vollständig der rollenbasierten Zugriffssteuerung durch einen vertrauenswürdigen Mitarbeiter. Wenn der vertrauenswürdige Mitarbeiter das Unternehmen verlässt oder innerhalb des Unternehmens in eine neue Abteilung wechselt, hat er keinen Zugriff mehr auf die Schlüssel, die er im Tresor erstellt hat.

Die ARM-Vorlagen für die Bereitstellung enthalten nur URI-Verweise auf die geheimen Schlüssel, was bedeutet, dass die tatsächlichen geheimen Schlüssel nicht in Code-, Konfigurations- oder Quellcoderepositorys enthalten sind. Dies reduziert die Chancen für das Phishing geheimer Schlüssel und hindert böswillige Angreifer am Vornehmen von Änderungen.

Wie in diesem Dokument bereits erwähnt, gibt es keine globalen Schlüsseltresore. Da Schlüsseltresore stets regional sind, sind die geheimen Schlüssel in den VMs stets ortsbezogen.

Ein Beispiel einer Implementierung dieses Ansatzes ist im Abschnitt "Geheime Schlüssel und Zertifikate" weiter oben in diesem Dokument zu finden.

## Aktivieren des Modells "Bring Your Own Subscription" (BYOS, Nutzen des eigenen Abonnements)

IT-Abteilungen von Unternehmen, Systemintegratoren und Clouddienstanbieter können bei ihren Kunden ein BYOS-Modell einsetzen. Dabei stellt die Organisation einem Endkunden einen Dienst bereit und nutzt dessen Azure-Abonnement auf beliebige Weise.

Es gibt wie nachstehend beschrieben mehrere Varianten mit leicht abweichenden Anforderungen.

### Aktivieren des Drittanbieterzugriffs zur Überwachung von Ressourcen innerhalb eines Kontos

Eine Organisation mit einer Überwachungsanwendung benötigt ggf. einen schreibgeschützten Zugriff auf das Abonnement eines Kunden zum Abrufen von Daten für die Nutzung in dieser Anwendung. Dies erfordert einen schreibgeschützten Zugriff für einen laufenden Zeitraum. Der Zugriff muss vom Kunden kontrollieren werden, der die Möglichkeit haben muss, den Zugriff zu beenden, nachdem die Beziehung mit dem Anbieter des Überwachungsdiensts beendet wurde.

#### Implementierung mit Azure-Ressourcen-Manager

Ausführliche Informationen zur entsprechenden Implementierung finden Sie [hier](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/) in einer Anleitung für Entwickler zur Authentifizierung mit der Azure-Ressourcen-Manager-API. Dieses Dokument bietet schrittweise Implementierungsanweisungen sowie Beispielcode.

### Aktivieren des Drittanbieterzugriffs für die einmalige Bereitstellung von Software

Ein weiteres Beispiel ist eine Organisation, die eine Version ihrer Software im Konto eines Kunden bereitstellt und konfiguriert und für die Dauer der Bereitstellung einen Schreibzugriff benötigt.

#### Implementierung mit Azure-Ressourcen-Manager

Der Ansatz hierfür ist vergleichbar mit dem im vorherigen Beispiel.

Je nach Anforderungen der Installation sollte die dem Dienstprinzipal zugewiesene spezifische Rolle nur den Mindestgrad an Zugriff zulassen, der erforderlich ist, um die Installation durchzuführen. Nach Abschluss der Installation muss der Zugriff sofort aufgehoben werden.

### Aktivieren des Drittanbieterzugriffs für das Verwenden von Kundenabonnements für die Datenspeicherung

Ein weiteres Beispiel ist eine Organisation, die Software in ihrer eigenen Umgebung ausführen, aber das Kundenkonto für die Speicherung nutzen möchte. Dadurch hat der Kunde die ständige Kontrolle über seine Daten und kann andere Technologien auf der Plattform, z. B. Azure Machine Learning oder HDInsight, im eigenen Ermessen nutzen. Dabei entfallen die Kosten bzw. der Abrechnungsaufwand für die IT-Abteilung, den Systemintegrator oder Clouddienstanbieter, die/der die Funktionalität bereitstellt. Dies erfordert einen ständigen Zugriff auf das Speicherkonto für die Organisation, wobei der Kunde die Kontrolle behält und Zugang zu Überwachungsinformationen zum Zugriff auf diese Informationen hat.

#### Implementierung mit Azure-Ressourcen-Manager

Diese Lösung wird auf dieselbe Weise wie bei den anderen Beispielen implementiert. Ein Dienstprinzipal erhält Zugriff auf die Speicherressource. Da bei diesem Szenario die Rolle einen Lese- und Schreibzugriff auf das Speicherkonto benötigt, wird dem Dienstprinzipal die integrierte Rolle "Mitwirkender" zugewiesen, um diesen Zugriffsgrad zu erreichen.

Da bei diesem Szenario beide Parteien ein Speicherkonto gemeinsam nutzen, muss sichergestellt werden, dass das Speicherkonto nicht versehentlich gelöscht wird. Zu diesem Zweck muss das Speicherkonto mit einer Ressourcensperre versehen werden.

### Aktivieren der Dienstverwaltung durch einen Drittanbieter

In einem weiteren Beispiel möchte eine Organisation Software im Abonnement des Kunden bereitstellen, überwachen und verwalten. Möglicherweise gelten Einschränkungen für den Kunden im Hinblick auf Änderungen, die er in an einer Umgebung, in der Software bereitgestellt wird, vornehmen (bzw. explizit nicht vornehmen) kann.

#### Implementierung mit Azure-Ressourcen-Manager

Dies entspricht dem am Anfang dieses Abschnitts beschriebenen Muster. Insbesondere erhält ein von einem Drittanbieter verwendeter Dienstprinzipal Vollzugriff auf die Ressourcen in der Ressourcengruppe.

Wenn darüber hinaus seitens des Kunden Einschränkungen vorliegen, werden Benutzern oder Gruppen des Kunden die entsprechenden Rechte zur Nutzung der Umgebung gewährt. Dies kann über Vorlagen erfolgen, was weiter oben in diesem Abschnitt beschrieben wurde.

Schließlich kann es wünschenswert sein, sicherzustellen, dass bestimmte Ressourcen nicht versehentlich gelöscht werden. Ist dies der Fall, sollten Ressourcensperren für Ressourcen erwägt werden, die einen solchen Schutz benötigen.

## Nächste Schritte

- Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Vorlagen](resource-group-authoring-templates.md).
- Empfehlungen für die Sicherheitseinstellungen in Azure-Ressourcen-Manager finden Sie unter [Sicherheitsaspekte für Azure-Ressourcen-Manager](best-practices-resource-manager-security.md).
- Informationen zur Freigabe des Status in Vorlagen finden Sie unter [Freigeben des Status in Azure-Ressourcen-Manager-Vorlagen](best-practices-resource-manager-state.md).

<!---HONumber=AcomDC_1223_2015-->