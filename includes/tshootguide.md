
# Problembehandlung in Azure

*Problembehandlung* bezeichnet die Suche und Analyse von unerwartetem Anwendungsverhalten und dessen Korrektur. Anwendungsentwickler müssen die entwickelten Anwendungen zunächst testen, ausführen und debuggen, bevor sie sie in der Produktionsumgebung bereitstellen. Dies ist unabhängig davon, ob die Anwendung auf einem Desktopcomputer oder auf einem Server in der Cloud ausgeführt wird. Das Debuggen einer dezentralen, skalierbaren Anwendung mit mehreren Instanzen ist jedoch nicht einfach und erfordert mehr als die üblichen Tools und Ansätze.

Aus diesem Grund sollte bereits bei der Entwicklung von Cloudanwendungen die Problembehandlung bedacht werden. Wie Sie die Problembehandlung in Ihre Anwendung einbinden, hängt zum einen davon ab, wo die Anwendung ausgeführt wird, und zum anderen davon, in welcher Sprache bzw. Laufzeitumgebung die Anwendung erstellt wurde.

Wenn Sie eine Anwendung erstellen, die auf einem virtuellen Computer von Azure ausgeführt wird, können Sie für Problembehandlung und Debugging meist den gleichen Ansatz wie auf dem Betriebssystem verwenden, ganz so als würde die Anwendung auf Ihren eigenen Servern ausgeführt.

Unter Azure werden Anwendungen verteilt und mit mehreren Instanzen ausgeführt, sodass sie sich unter Umständen schlecht debuggen lassen. Für diese Art von Anwendungen reichen die Standardtools und -ansätze bei der Problembehandlung nicht aus. In diesem Thema werden verschiedene bewährte Problembehandlungsmethoden besprochen. Es enthält außerdem Links zu ausführlichen Informationen über die beschriebenen Methoden.

**Hinweis**: Dieses Thema basiert auf der Annahme, dass Sie entweder eine Anwendung entwickeln oder eine Azure-Anwendung erfolgreich bereitgestellt haben und dass nun etwas Unerwartetes vorgefallen ist. Es wird nicht erläutert, wie Sie eine Anwendung auf Azure bereitstellen. Weitere Informationen zur Entwicklung und Bereitstellung Ihrer Azure-Anwendung finden Sie unter [https://www.windowsazure.com/de-de/develop/overview/][1].

In diesem Thema werden zunächst einige bewährte Methoden beschrieben, die Ihnen dabei helfen, Ihre Anwendung so zu entwickeln, dass im Falle eines Problems eine effektive Problembehandlung möglich ist. (Wenn Sie nicht bereits beim Entwurf der Anwendung darauf achten, dass Sie den Codeverlauf später nachvollziehen können, lässt sich im Falle eines Problems dessen Ursache nur schwer ermitteln.) Diese bewährten Methoden gelten für alle auf Azure ausgeführten Anwendungstypen, unabhängig vom Anwendungsmodell und von der verwendeten Sprache.

In den folgenden Abschnitten werden konkrete Ansätze für die Entwicklung unterstützter Azure-Anwendungen unabhängig vom Anwendungstyp beschrieben: Websites, Clouddienste oder virtuelle Computer.

Die einzelnen Abschnitte beschreiben bewährte Methoden auf einer übergeordneten Ebene und enthalten Verweise auf Ressourcen, in denen diese bewährten Methoden im Detail veranschaulicht werden oder deren Implementierung erläutert wird.

## Inhalt dieses Dokuments

* [Bewährte Methoden zur Problembehandlung in Azure](#BestPractices)
* [Azure-Websites](#Websites)
* [Azure Cloud Services](#CloudServices)
* [Azure Virtual Machines](#Vms)
* [Azure-Dienste](#PlatformServices)
* [Problembehandlung in SQL-Datenbanken](#SQLTroubleshooting)

<h2><a id="BestPractices" ></a>Bewährte Methoden zur Problembehandlung in Azure</h2>


In diesem Abschnitt werden bewährte Methoden für Azure-Anwendungen beschrieben, unabhängig vom verwendeten Hostingmodell und von der verwendeten Sprache. Er enthält außerdem Ressourcen mit vertiefenden Informationen zu diesen Methoden.

Um eine Grundlage für die effiziente Problembehandlung in Azure zu schaffen, konzentrieren Sie sich auf die drei folgenden Bereiche:

* Ordnungsgemäße Behebung von Fehlern -- *Jeder Komponentendienst muss
  den Ausfall abhängiger Dienste oder Infrastrukturen überstehen*.
* Ablaufverfolgung, Protokollierung und Überwachung -- *Jeder
  Komponentendienst muss über geeignete Methoden für Debugging,
  Ablaufverfolgung sowie Ereignis- und Fehlerprotokollierung verfügen.*
* Debugging -- *Beheben Sie Fehler, bevor Sie die Anwendung in die
  Produktionsumgebung einführen, sowie Fehler auf Komponenten- und
  Netzwerkebene während der Ausführung.*

Indem Sie diese Grundsätze bei der Anwendungsentwicklung beachten, kann die Anwendung Ihnen die zur Nachverfolgung von unerwartetem Verhalten notwendigen Informationen bereitstellen.

### Ordnungsgemäße Behebung von Fehlern

Anwendungen sollten Fehlerbedingungen nach Möglichkeit ordnungsgemäß beheben. Dies ist angesichts der dezentralen Natur von Azure besonders wichtig. Effektive Problembehandlung beginnt mit einem geeigneten Entwurf für die Behebung vorübergehender Fehler. Vorübergehende Fehler zählen zu den Bereichen, in denen Cloudanwendungen am häufigsten ein unerwartetes Verhalten zeigen. Dies liegt an für Internetanwendungen üblichen vorübergehenden Fehlerbedingungen.

Als vorübergehende Fehler werden Ausfälle aufgrund von Latenzzeiten und zeitweilig nicht verfügbaren Netzwerkverbindungen bezeichnet, wie sie für gemeinsam genutzte Ressourcen im Internet typisch sind. Hier einige Beispiele:

* Freigegebene Computerressourcen wie Azure Cloud Services und
  SQL-Datenbank (um nur zwei Beispiele zu nennen) können von einem
  Augenblick zum nächsten mehr oder weniger reaktionsfähig sein.
* Die Reaktionsfähigkeit verzögert sich, damit die Stabilität der
  Dienste gewährleistet ist. SQL Azure sorgt beispielsweise für mehrere
  konsistente Datenbankkopien, um deren Stabilität zu gewährleisten, was
  sich auf die Reaktionsfähigkeit auswirkt.
* Verzögerungen entstehen außerdem, wenn HTTP- oder andere Verbindungen
  vorzeitig unterbrochen werden. In diesem Fall werden
  HTTP-Anforderungen nicht bis zum Ende ausgeführt und vor Ablauf des
  Zeitlimits zurückgegeben.

Um die Auswirkungen vorübergehender Fehler zu minimieren, sollten Azure-Anwendungen folgende Bedingungen erfüllen:

* Eine lose Verbindung bewirkt, dass Komponenten nicht von Diensten
  abhängig sind, die öfter als in lokalen Umgebungen üblich ausfallen.
* Verwenden Sie nach Möglichkeit asynchrone Aufrufe, sodass Prozesse
  nicht von einer unmittelbaren Antwort abhängig sind.
* Nutzen Sie für die Handhabung vorübergehender Fehler einen Ansatz, der
  bestimmte Fehlerkategorien erkennt und für diese ein
  Wiederholungsverhalten basierend auf einer konfigurierbaren Richtlinie
  umsetzt.

Dienstaufrufe sollten einen Layer zur Handhabung vorübergehender Fehler erstellen oder nutzen, um allgemeine Fehlerszenarios zu erkennen und den Aufruf basierend auf einer Konfigurationseinstellung zu wiederholen. Für .NET-Entwickler wird unter anderem die Bibliothek [Microsoft Enterprise Library 5.0 Integration Pack for Azure][2] empfohlen. Microsoft Enterprise Library ist eine Sammlung wiederverwendbarer Anwendungsblöcke, die sich allgemeiner, bereichsübergreifender Bedenken bei der Entwicklung von Unternehmenssoftware annehmen. Das Microsoft Enterprise Library Integration Pack for Azure ist eine Erweiterung zu Microsoft Enterprise Library 5.0, die mit Azure verwendet werden kann. Sie beinhaltet einen Anwendungsblock für die automatische Skalierung, einen Anwendungsblock für die Handhabung vorübergehender Fehler, eine BLOB-Konfigurationsquelle, einen geschützten Konfigurationsanbieter und Schulungsunterlagen. Eine andere, einfacher aufgebaute .NET-Bibliothek mit geringerem Funktionsumfang ist das [Cloud Application Framework & Extensions (CloudFx)][3]. CloudFx bietet eine Reihe von Komponenten und Bausteinen in Produktionsqualität, die die Implementierung funktionsreicher, zuverlässiger und erweiterbarer Lösungen und Dienste auf der Grundlage von Azure erleichtern.

### Angemessene Ablaufverfolgung und Protokollierung

Aufgrund der Komplexität dezentraler, skalierbarer Anwendungen sind herkömmliche Debugger, die nur auf einen Prozess angewendet werden, von keinem großen Nutzen, um Probleme während der Ausführung Ihrer Anwendung zu untersuchen. Aus diesem Grund sind Ablaufverfolgung und Protokollierung von größter Bedeutung. Die Ausführung der Anwendung und der zugehörigen Daten erfolgt für mehrere Dienste gemeinsam, die auf vielen verschiedenen Computern gehostet werden. In einer großen, dezentralen Anwendung kann, wenn überhaupt, nur schlecht bestimmt werden, zu welcher Dienstinstanz eine Verbindung hergestellt werden muss, um einen aufgetretenen Fehler zu beheben. Mittels Ablaufverfolgung und Protokollierung können Sie die Anwendungsausführung und den Datenfluss nachvollziehen, was Ihnen ein besseres Bild vom Zustand der Anwendung vermittelt.

Bei erfolgreichen Azure-Anwendungen wird die Protokollierungs- und Ablaufverfolgungsstrategie bereits bei der Entwicklung in die Anwendung integriert. Dies minimiert Zeit und Aufwand für die Fehlersuche und -korrektur und macht Anrufe beim Microsoft-Support unnötig.

**Hinweis**: Ablaufverfolgung und Protokollierung beeinträchtigen die Leistung, vor allem wenn diese intensiv betrieben werden. Schließen Sie daher in das Anwendungsdesign eine konfigurierbare Ablaufverfolgungs- und Protokollierungsrichtlinie ein, die bei Bedarf angepasst werden kann. Idealerweise sollte die Protokollierungsebene über die Datei **ServiceConfiguration.cscfg** anpassbar sein, sodass eine Änderung ohne erneute Bereitstellung möglich ist.

Eine große Menge an Protokollen ist keine Garantie für eine schnelle Fehlererkennung und -korrektur. Vielmehr beansprucht die Entschlüsselung einer großen Datenmenge viel Zeit und deren Erfassung beeinträchtigt die Leistung Ihrer Anwendung. Bei einer anpassbaren Protokollierung sind sowohl Umfang als auch Speicherkosten der protokollierten Daten steuerbar.

Im Artikel [Protokollierung und Ablaufverfolgung in Azure][4] des MSDN Magazins beschreibt Mike Kelly vier Arten von Diagnoseausgaben, die zu berücksichtigen sind:

* Debugausgabe -- Nur in der Debugversion, inklusive Bestätigungen
* Ablaufverfolgung -- Zur Verfolgung des Steuerungsablaufs während der
  Ausführung
* Ereignisprotokollierung -- Größere Ereignisse bei der
  Programmausführung
* Fehlerprotokollierung -- Ausnahme- oder Gefahrensituationen

In anderen Sprachen, Anwendungsplattformen und Betriebssystemen werden ggf. andere Bezeichnungen für Ablaufverfolgung und Protokollierung verwendet. Wenn Sie eine dieser Entwicklungsplattformen auf Azure nutzen, verwenden Sie die entsprechenden Strategien und Tools für die jeweilige Sprache bzw. Plattform.

Anwendungen mit gemischtem Modus werden in einer Kombination aus virtuellen Computern, Websites und Clouddiensten von Azure ausgeführt. Anwendungen dieser Art sind weiter verteilt, weshalb Ablaufverfolgung und Protokollierung noch größere Bedeutung zukommt. Zur Problembehandlung in Anwendungen mit gemischtem Modus muss der gesamte Daten- und Ausführungsfluss nachverfolgt werden, um Probleme zu identifizieren. Die Funktionsweise der Ablaufverfolgung und Protokollierung in Anwendungen mit gemischtem Modus ist vom Hostingmodell der Komponente abhängig.

### Überwachen der Anwendung

Ablaufverfolgung und Protokollierung fügen sich in den Gesamtbereich der Überwachung ein. Überwachung beinhaltet Folgendes:

* Erkennen von Azure-Anwendungen
* Bestimmen des Status jeder Rolleninstanz
* Erfassen und Überwachen von Leistungsdaten, einschließlich Latenzzeit
  und Durchsatz
* Erfassen und Überwachen von Ereignissen
* Erfassen und Überwachen von Ablaufverfolgungsmeldungen
* Überwachen der Ressourcenauslastung
* Überwachen der Qualität von Dienstkennzahlen
* Durchführen von Kapazitätsplanung
* Durchführen von Datenverkehrsanalysen (Benutzer, Ansichten,
  Spitzenzeiten)
* Schätzen der Rechnungssumme
* Durchführen von Audits

Für die Überwachung wird ein Tool bzw. ein Toolsatz verwendet. Welches Tool Sie verwenden, hängt von der Plattform und/oder den Sprachen der Anwendung sowie von Ihren Überwachungszielen und -anforderungen ab.

**Microsoft System Center-Überwachungspaket für Azure-Anwendungen**

Dieses [Überwachungspaket][5] versetzt Sie in die Lage, [Microsoft System Center Operations Manager][6] zu verwenden, um die Verfügbarkeit und Leistung von Azure-Anwendungen zu überwachen.

Die Verwendung von Microsoft System Center Operations Manager 2007 ist die beste Methode zum Überwachen des Zustands Ihrer Azure-Anwendung.

**Azure Platform Management Tool**

Ein weiteres Tool ist das [Azure Platform Management Tool (MMC)][7], mit dem Sie unter Azure gehostete Dienste und Speicherkonten verwalten können. Das Tool wird als Beispiel zusammen mit dem vollständigen Quellcode bereitgestellt, sodass Sie sehen können, wie verschiedene Verwaltungs- und Konfigurationsaufgaben mithilfe der Verwaltungs- und Diagnose-APIs von Azure durchgeführt werden.

**Cerebrata-Tools**

Cerebrata bietet verschiedene Tools zum Überwachen und Verwalten Ihrer Azure-Anwendungen. Dazu zählen u. a. [Azure Diagnostics Manager][8], [Cloud Storage Studio][9] und [Azure Management Cmdlets][10].

Azure Diagnostics Manager 2 ist ein auf Windows (WPF) basierender Client zur Verwaltung der Azure-Diagnose. Hiermit können Sie von den unter Azure ausgeführten Anwendungen erfasste Diagnosedaten anzeigen, herunterladen und verwalten. Weitere Informationen zu diesen Produkten finden Sie unter [http://www.cerebrata.com][11].

Cloud Storage Studio 2 ist ein auf Windows (WPF) basierender Client zur Verwaltung von Azure Storage.

Azure Management Cmdlets ist ein Satz von Windows PowerShell-Cmdlets zur Verwaltung von Azure Storage, gehosteten Diensten, SQL-Datenbankinstanzen und Diagnose. Er beinhaltet außerdem Cmdlets zum Sichern und Wiederherstellen von Speicherkonten.

**Netzwerküberwachung: AlertBot, Gomez, Keynote, Pingdom**

Compuware [Gomez][12] Application Performance Management, [Keynote][13], [Pingdom][14] und [AlertBot][15] sind Lösungen für die Remoteüberwachung Ihrer Azure-Anwendung. Sie ermöglichen Ihnen, die Verfügbarkeit Ihrer Anwendung zu überwachen und die Leistung zu optimieren. Einige Dienste, wie z. B. Pingdom, unterstützen Benachrichtigungen per E-Mail, SMS oder Desktopanwendung, wenn ein Fehler erkannt wird. Diese Art der Überwachung simuliert, was Endbenutzer zur erfolgreichen Überwachung einer Anwendung durchführen.

**Apica-Tools**

Apica bietet Tools, mit denen Sie Ihre Azure-Webanwendung "von außen" überwachen können. Weitere Informationen finden Sie unter [http://www.apicasystem.com/integration-partners/][16].

**AVIcode**

Microsoft hat AVIcode gekauft, welches nun Teil von Microsoft System Center ist. [AVIcode][17] bietet eine umfassende Palette an Funktionen zur Überwachung der .NET-Anwendungsleistung.

**Leistungsprofilerstellung**

Ein [Profil][18] Ihrer Azure-Anwendung bei der Ausführung in Azure gibt Aufschluss über mögliche Leistungsprobleme. Wenn Sie Ihre Windows Azure-Anwendung über Visual Studio veröffentlichen, können Sie ein Profil der Anwendung erstellen und die erforderlichen Profileinstellungen auswählen.

**Azure VM Assistant**

Das Tool [VM Assistant][19] ist ein CodePlex-Projekt, das alle relevanten Problembehandlungsdaten an zentraler Stelle erfasst, wenn Sie mit einem Remotedesktop eine Verbindung zu einer virtuellen Computerinstanz herstellen. Über die Schaltfläche **VM Health** zeigen Sie den aktuellen Status der Instanz an.

### Debuggen von Fehlern (sofern möglich)

Bevor Sie eine Anwendung in Azure bereitstellen, empfiehlt es sich, die Anwendung lokal zu debuggen. Das Azure SDK enthält Emulatoren, die die Azure-Cloudumgebung simulieren. Auf diese Weise können Sie Ihre Anwendung ausführen und grundlegende Tests durchführen, ohne die Anwendung bereitzustellen. Die verwendeten Debugtools variieren je nach Programmiersprache und Entwicklungstools.

Nachdem Sie die Anwendung bereitgestellt haben, können Sie diese mit einem Debugger wie Visual Studio in der Cloud debuggen. Hierzu muss eine Debugversion erstellt und bereitgestellt werden. Stellen Sie zu diesem Zweck eine Verbindung zu einer bestimmten Rolleninstanz her. Wenn Sie eine komplexe Anwendung mit mehreren Rollen und Rolleninstanzen haben, kann es schwierig sein, diese Rolleninstanz zu bestimmen. Visual Studio Ultimate unterstützt IntelliTrace, über das .NET-Rollen Debuginformationen verfolgen können. Im Falle eines Problems können Sie diese Informationen herunterladen und in Visual Studio laden. Untersuchen Sie das IntelliTrace-Protokoll jeder Rolle, um zu bestimmen, wo das Problem aufgetreten ist. Auch wenn das Debuggen in der Cloud einige Nachteile hat, ist es unter gewissen Umständen erforderlich. Nicht alle Azure-Dienste verfügen über einen Emulator (z. B. Service Bus), und nicht alle unterstützten Entwicklungstools (z. B. Mac und Linux) beinhalten Emulatoren.

Nach dem lokalen Debuggen Ihrer Anwendung müssen Sie meist auf die integrierten Instrumente vertrauen, um zu bestimmen, wo Probleme auftreten.

**Debuggen von Node.js**

Zum Debuggen von Node.JS-Anwendungen können Sie das Tool "Node-Inspector" verwenden, das auf [GitHub][20] verfügbar ist. Node-Inspector kann mit dem Azure-Speicheremulator lokal auf dem Entwicklungscomputer ausgeführt werden. Weitere Informationen finden Sie im Blog von Jim Wang: [Debugging Node in the Azure Emulator (in englischer Sprache)][21].

Wenn Sie Ihre Anwendung unter Windows Azure debuggen, installieren Sie die Vollversion von IISNode über [GitHub][22] auf Ihrer Webrolle, Workerrolle oder VM-Instanz. Wie bereits gesagt, ist dies nicht die empfohlene Methode zum Debuggen Ihrer Anwendung, wenn diese in der Produktionsumgebung bereitgestellt und auf mehrere Instanzen skaliert ist, da möglicherweise nicht erkennbar ist, welche Rolleninstanz oder VM Sie debuggen müssen.

Um Node-Inspector in einer Webrolle zu verwenden, installieren Sie das Paket in der Webrolle, und verwenden Sie Node-Inspector wie gewohnt. Weitere Informationen über das Debuggen mit Node-Inspector finden Sie unter [Debugging with Node-Inspector][23].

**IntelliTrace**

Microsoft Visual Studio Ultimate beinhaltet [IntelliTrace][24], mit dem Sie Anwendung vor dem Bereitstellen in der Produktionsumgebung debuggen können. IntelliTrace unterstützt ASP.NET- und WCF-Anwendungen. Die Aktivierung von IntelliTrace in einem Produktionsdienst wird nicht unterstützt, Sie können hiermit jedoch Ausnahmen für eine Anwendung nach der Bereitstellung auf Azure ermitteln. Der Blogbeitrag von Jim Nakashima erläutert, wie Sie mit IntelliTrace Azure-Clouddienste debuggen.

**Fiddler**

[Fiddler][25] ist ein Web Debugging Proxy, der den gesamten HTTP(S)-Datenverkehr zwischen Ihrem Computer und dem Internet protokolliert. Mit Fiddler können Sie den Datenverkehr untersuchen, Haltepunkte festlegen sowie eingehende und ausgehende Daten manipulieren. Fiddler ist besonders hilfreich bei der Problembehandlung in Azure Storage.

Um Fiddler für die lokale Entwicklungsstruktur einzusetzen, verwenden Sie "ipv4.fiddler" anstelle von "127.0.0.1":

* Starten Sie Fiddler.
* Starten Sie Ihren Dienst in der Entwicklungsstruktur.
* Wechseln Sie zu "http://ipv4.fiddler:/". Fiddler sollte die
  Anforderung verfolgen.

Um Fiddler im lokalen Entwicklungsspeicher zu verwenden, ändern Sie die Dienstkonfigurationsdatei, sodass diese auf Fiddler verweist:

Öffnen Sie die Datei "ServiceConfiguration.cscfg", und ändern Sie die Verbindungszeichenfolge wie folgt:

    Value="UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://ipv4.fiddler"

* Starten Sie Fiddler.
* Starten Sie Ihren Dienst. Fiddler sollte alle Speicheranforderungen
  verfolgen.

## Problembehandlung und die Azure-Hostingmodelle

In diesem Abschnitt werden bewährte Methoden zum Debuggen von Anwendungen mit verschiedenen Azure-Hostingmodellen beschrieben.

<h2><a id="Websites" ></a>Azure-Websites</h2>


Beachten Sie bei der Entwicklung einer unterstützten Azure-Website nach Möglichkeit die Empfehlungen am Anfang dieses Dokuments. Hierzu zählen die Prüfung und Beseitigung von Fehlern, die Anwendung von Wiederholungslogik für vorübergehende Fehler, Ablaufverfolgung und Protokollierung. Um Probleme in Azure-Websites zu behandeln, konfigurieren Sie die Websites für die Anzeige von Anwendungsfehlern, konfigurieren Sie Fehlerdiagnosen für die Website, erfassen Sie Diagnosedaten und analysieren Sie anschließend die erfassten Daten, um mögliche Probleme zu ermitteln und zu beheben.

Azure-Websites ermöglichen die Konfiguration der folgenden Diagnoseoptionen:

* Webserverprotokollierung
* Detaillierte Fehlermeldungen
* Verfolgung fehlgeschlagener Anforderungen

Weitere Informationen zu diesen Themen finden Sie unter: [Problembehandlung in einer Azure-Website](/de-de/develop/net/best-practices/troubleshooting-web-sites/).

Wenn Webserverprotokolle für Azure-Websites aktiviert sind, zeichnet die Website alle HTTP-Transaktionen in einer Protokolldatei mit dem
[erweiterten W3C-Protokolldateiformat][26] auf. Für Abfragen in der Protokolldatei können Sie dann [Log Parser][27] verwenden. Beispiele für Log Parser-Abfragen finden Sie auf [Log Parser Plus][28] und [TechNet Log Parser Examples][29]. Wenn Sie Ausgaben vom Typ CHART auf einem Computer mit Office 2007/2010 generieren möchten, installieren Sie [Office 2003 Web Components][30] entsprechend den Anweisungen auf [Log
Parser Plus][31].

Azure-Websites verwenden die gleiche Funktion zum Verfolgen fehlgeschlagener Anforderungen, die in IIS 7.0 und höher verfügbar ist. Diese ist jedoch nicht konfigurierbar so wie die Verfolgung fehlgeschlagener Anforderungen in IIS. Wenn Sie die Verfolgung fehlgeschlagener Anforderungen für Azure-Websites aktivieren, werden **alle** fehlgeschlagenen Anforderungen erfasst.

<h2><a id="Cloudservices" ></a>Azure Cloud Services</h2>


Aufgrund der dezentralen Natur von Azure Cloud Services ist es wichtig, Ihre Anwendung durch asynchrone Aufrufe und Wiederholungslogik für vorübergehende Fehler zu schützen, wie zuvor bereits erläutert.

Die für Azure Cloud Services verwendete Debugtechnik ist von der Art des aufgetretenen Problems abhängig. Probleme, die eine bestimmte Rolle oder Rolleninstanz betreffen, z. B. ein Fehler beim Starten oder Wechseln einer Rolle, lassen sich am besten mittels Remotedesktop untersuchen. In diesen Fällen kennen Sie die problematische Rolle bzw. Rolleninstanz und können eine Verbindung zur betroffenen Rolle herstellen. Wenn ein Problem auftritt und Sie nicht sicher sind, welche Rolleninstanz das Problem verursacht, sind Ablaufverfolgung und Protokollierung die besseren Problembehandlungsmethoden. Die Azure-Diagnose verfügt über einen Mechanismus zum Erfassen und Verwalten von Ablaufverfolgungs- und Protokollinformationen.

Dem Azure SDK 1.7 wurden einige neue Debugfunktionen hinzugefügt, u. a. ist es nun einfacher, Stapelüberwachungen zu finden, wenn eine Ausnahme auftritt, und die Remotedesktopkonnektivität wurde verbessert. Stapelüberwachungen sind nun in das Windows-Ereignisprotokoll integriert, sodass die genaue Fehlerursache leichter erkennbar ist. Zudem wurde die Remotedesktopkonnektivität verbessert. Wenn Ihre Rolle wechselt oder beendet wird, können Sie mittels Remotedesktop eine Verbindung zu der problematischen Rolle herstellen und das Problem untersuchen.

Das Azure-Portal bietet Zugriff auf Überwachungsdaten, anhand derer IT-Experten und Entwickler Probleme in Azure Cloud Services vorhersehen und diagnostizieren können. Von der Host-VM werden im Regelfall Werte wie der CPU-Prozentsatz, die eingehenden und ausgehenden Daten oder der Durchsatz bei Lese- und Schreibvorgängen auf dem Datenträger erfasst. Die Aktivierung dieser Kennzahlen für Rolleninstanzen ist mit keinerlei Konfigurationsaufwand oder Kosten für die Kunden verbunden. Zudem können weitere Leistungsdaten erfasst werden. Um ausführliche Diagnoseinformationen zu erfassen, benötigen Sie eine gültige Diagnoseverbindungszeichenfolge, da diese Informationen im Azure-Speicher abgelegt werden. Sie verursachen daher zusätzliche Speicherkosten. Aktiviert ein Benutzer die ausführliche Überwachung, konfiguriert das Portal entfernt Rolleninstanzen, um die standardmäßigen Leistungszähler zu erfassen.

### Azure-Diagnose

Das ursprüngliche Azure SDK 1.0 beinhaltete Funktionen zum Erfassen und Speichern von Diagnosedaten im Azure-Speicher, die unter dem Namen Azure-Diagnose zusammengefasst wurden. Diese auf dem Event Tracing for Windows (ETW)-Framework basierende Software erfüllt zwei Entwicklungsanforderungen, die durch die skalierbare Azure-Architektur entstanden sind:

* Speichern von Diagnosedaten, die beim Reimaging der Instanz verloren
  gehen würden
* Bereitstellen eines zentralen Repositorys für Diagnosedaten aus
  mehreren Instanzen

Nachdem Sie die Diagnose in der Rolle konfiguriert haben, werden Diagnosedaten von allen Instanzen der jeweiligen Rolle erfasst. Die Diagnosedaten können zum Debuggen und Beheben von Fehlern, zum Messen der Leistung, zum Überwachen der Ressourcenauslastung, zum Analysieren des Datenverkehrs, zum Planen der Kapazität und zum Durchführen von Audits verwendet werden. Die Übertragung an das Azure-Speicherkonto zu Archivierungszwecken kann entweder geplant werden oder nach Bedarf erfolgen.

Die Azure-Diagnose ändert das Serverparadigma in vier wichtigen Aspekten:

* Diagnosen müssen zum Zeitpunkt der Anwendungserstellung aktiviert
  werden.
* Zum Darstellen der Diagnoseergebnisse sind spezielle Tools/Schritte
  erforderlich.
* Systemabstürze führen zu einem Verlust der Diagnosedaten, sofern diese
  nicht in den dauerhaften Speicher geschrieben werden (Azure-Speicher
  anstelle einer Speicherung auf Instanzebene).
* Für die Speicherung von Diagnosedaten im Windows Azure-Speicher fallen
  monatliche Kosten an.

Dem Thema Kosten kommt besondere Bedeutung zu, da einer der wesentlichen Vorteile von Azure die Kostensenkung ist. Derzeit besteht die einzige Möglichkeit, die mit der Diagnose verbunden Kosten zu umgehen, darin, die Daten auf dem virtuellen Computer zu speichern. Dies funktioniert in kleineren Bereitstellungen, ist aber bei vielen Instanzen nicht sinnvoll. Die folgenden Methoden helfen Ihnen, die finanziellen Auswirkungen zu minimieren:

* Stellen Sie sicher, dass sich das Speicherkonto im gleichen
  Datencenter wie Ihre Anwendung befindet. Sollten sich diese aus
  irgendeinem Grund in verschiedenen Datencentern befinden, wählen Sie
  das Intervall für geplante Übertragungen mit Bedacht aus. Kurze
  Übertragungsintervalle steigern die Datenrelevanz, der Vorteil ist
  jedoch möglicherweise nicht groß genug, um die zusätzlichen Kosten für
  Bandbreite und Verarbeitung auszugleichen.
* Kopieren und löschen Sie regelmäßig die Diagnosedaten aus dem
  Azure-Speicher. Die Diagnosedaten durchlaufen den Azure-Speicher,
  sollten dort jedoch nicht unnötig lange aufbewahrt werden. Hierfür
  stehen verschiedene Tools zur Verfügung: System
  Center-Überwachungspaket für Azure, Azure Diagnostics Manager von
  Cerebrata und Azure PowerShell-Cmdlets.
* Wählen Sie nur die Diagnosedaten aus, die Sie für die
  Problembehandlung und Überwachung Ihrer Anwendung benötigen. Die
  Erfassung einer zu großen Datenmenge erschwert die Problembehandlung,
  von den höheren Kosten ganz zu schweigen.
* Kontrollieren Sie die Erfassung und den Umfang der Diagnosedaten,
  indem Sie einen On-Demand-Schalter in Ihre Anwendung integrieren.
* Verwenden Sie die Protokollierungsebene (Ausführlich, Info, Warnung,
  Fehler), sodass alle Informationen verfügbar sind, und erfassen Sie
  dann mithilfe der Diagnosekonfiguration nach der Bereitstellung
  gezielt die benötigten Daten.

<h2><a id="Vms" ></a>Azure Virtual Machines</h2>


Bei der Behebung von Fehlern in Anwendungen, die auf Azure Virtual Machines ausgeführt werden, kommen im Allgemeinen die gleichen Problembehandlungstechniken zum Einsatz wie bei anderen Betriebssystemen und Plattformen. Zum Beispiel:

* Windows Server 2008 R2 x64 (RTM und SP1); Windows 8 Service x64 (Datacenter und Core). Zu diesem Thema gibt es zahlreiche Informationen und Tools, die Ihnen dabei helfen, diese Aufgabe schnell zu erledigen.

* Weitere Informationen zu den Ansätzen finden Sie unter [Gewusst wie: Debuggen von Windows-Dienstanwendungen][32].

* Ein Video, das die besten Ansätze veranschaulicht, finden Sie in der [Präsentation von Daniel Pearson "Windows Debugging and Troubleshooting"][33].

* Open Suse Linux. Es gibt umfangreiche Ressourcen zur Problembehandlung in Anwendungen unter Suse Linux, sowohl in der [Suse Linux-Dokumentation][34] als auch im Internet.
* Ubuntu Linux. Auch hierzu gibt es viele Informationen. Als erste Anlaufstelle empfiehlt sich die Produktdokumentation unter [https://help.ubuntu.com/][35].
* CentOS Linux. Weitere Informationen finden Sie unter [http://centos.org/][36].

<h2><a id="PlatformServices" ></a>Problembehandlung in Azure-Diensten</h2>


Für viele Azure-Dienste, wie Azure SQL-Datenbank, Azure Active Directory und Azure-Speicher, gibt es spezifische Problembehandlungsanleitungen, unabhängig davon, ob die Anwendung unter Azure ausgeführt wird, mit welcher Programmiersprache oder Bibliothek sie erstellt wurde oder ob sie auf einem Microsoft-fremden Betriebssystem ausgeführt wird. Im Folgenden werden bewährte Methoden für einige dieser Dienste erläutert.

Es gibt viele unterstützte Bibliotheken, die bewährte Methoden für asynchrone Aufrufe, Ablaufverfolgung und Ereignisprotokollierung implementieren, wie im Abschnitt zur Entwicklung in diesem Dokument bereits dargelegt wurde.

#### Problembehandlung in Azure Storage

Unter den nachfolgenden Links werden Entwürfe oder Verfahren zur Vermeidung von Problemen, die eine Problembehandlung erforderlich machen, sowie Positionen, an denen eine Ablaufverfolgung oder Protokollierung hinzugefügt werden sollte, besprochen.

* Speicherstatus und Fehlercodes: [http://msdn.microsoft.com/de-de/library/windowsazure/dd179382.aspx][37]

* Speicheranalyse: [http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx][38] (siehe die drei Links unter "Weitere Informationen" am Ende des Dokuments)

* Überblick über Wiederholungsrichtlinien in der Clientbibliothek von
  Azure Storage: [http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx][39]

* Optimale Nutzung von Azure-Tabellen:
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx][40]
  
* Siehe folgende Abschnitte: Bewährte Methoden, Tipps zur Programmierung von Azure, Zeitlimitüberschreitungen und ausgelastete Server -- Ist das normal?

* Schützen Ihrer Blogs vor Anwendungsfehlern:
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx][41]

**Windows Azure-Speicher-Explorer**

Es gibt verschiedene Möglichkeiten, Azure-Speicher zu untersuchen. Das für Windows Azure-Speicher zuständige Team hat eine ganze [Liste von Speicher-Explorern][42] zusammengestellt. Mit allen diesen Explorern können Sie Diagnosedateien und Analysedateien für Azure Storage anzeigen. Der [Explorer for Azure Blob Storage][43] von Cloudberry Lab bietet eine Benutzeroberfläche, über die Speicheranalysen direkt in der Anwendung erstellt werden können, indem Sie auf **Storage Settings** klicken.

<h2><a id="Misc" ></a>Problembehandlung in Azure-Service Bus</h2>


Dieser Abschnitt bietet eine allgemeine Anleitung für die Entwicklung von Anwendungen, die den Azure-Service Bus auf stabile und verwaltungsfreundliche Weise nutzen, sodass häufige Probleme vermieden werden. Zudem erfahren Sie, wie Sie allgemeine Servicebusfehler erkennen und beheben.

### Allgemeine Hinweise

Beim Servicebus handelt es sich um einen [Enterprise Service Bus][44] auf Internetebene, der Funktionen für Relay- und Brokermessaging unterstützt. Der Servicebus implementiert Kontingente und Grenzwerte auf Systemebene für beide Nachrichtentypen. Wenn Ihre Anwendung diese Kontingente überschreitet, wird diese entweder gedrosselt oder Ihre Anforderungen bzw. Nachrichten werden zurückgewiesen. Ausführliche Informationen über Service Bus-Kontingente und das Verhalten, wenn diese überschritten werden, finden Sie unter [Azure Service Bus-Kontingente][45]. Einige Kontingente sind benutzerdefiniert, wie beispielsweise die Größe einer Warteschlange oder eines Themas. Diese werden beim Erstellen des jeweiligen Elements festgelegt.

Um einen Einblick in die Daten in der Service Bus-Messagingentität und in deren Verarbeitung zu erhalten, können Sie den [Service Bus Explorer][46] oder den Server Explorer in den Azure-Tools für Visual Studio (Version 1.7 oder höher) zum Erstellen, Löschen und Testen von Warteschlangen, Themen, Abonnements und Regeln verwenden. Dies ist eine hervorragende Methode, um Fehler in einer Anwendung zu beheben, die zwar ausgeführt wird, aber Daten nicht erwartungsgemäß verarbeitet. Diese Tools bieten Funktionen, mit denen Sie Warteschlangen, Themen und Relayelemente testen, die von einzelnen Sende- und Empfangsaufgaben durchgeführten Vorgänge verfolgen, den Fortschritt und die Leistung eines aktuellen Testlaufs überwachen und detaillierte Ergebnisprotokolle, einschließlich Fehlermeldungen, generieren können.

### Service Bus Relay

Der "Service Bus Relay-Dienst" wird in der Cloud ausgeführt und unterstützt verschiedene Transportprotokolle und Webdienststandards, darunter SOAP, WS-\* und REST. Sie können das Service Bus Relay als Delegaten zum Überwachen auf eingehenden Sitzungen und Anforderungen an einem WCF-Dienst verwenden. Im Relaymessaging stellt ein lokaler oder cloudbasierter Dienst eine Verbindung zu dem Relaydienst über einen ausgehenden Port her und erstellt einen bidirektionalen Socket für die mit einer bestimmten Rendezvous-Adresse gekoppelte Kommunikation. Der Client muss nicht wissen, wo der Dienst angesiedelt ist, und für den lokalen Dienst sind keine geöffneten eingehenden Ports in der Firewall notwendig. Je nach Netzwerkkonfiguration kann es jedoch zu Problemen kommen, wenn Sie über eine Firewall oder einen Proxyserver eine Verbindung zum Service Bus Relay herstellen. Unter [Hosten hinter einer Firewall mit Servicebus][47] wird beschrieben, wie Sie derartige Verbindungsfehler beheben.

### Servicebus-Warteschlangen und -Themen

Servicebus-Warteschlangen und -Themen stellen Funktionen für Brokermessaging bereit. Nachrichten werden an die Servicebus-Warteschlange bzw. das Servicebus-Thema übertragen, wo sie sicher aufbewahrt werden, bis der Empfänger sie abrufen kann. Nachrichtensender und -empfänger müssen nicht gleichzeitig online sein. Die Nachrichteninfrastruktur speichert die Nachrichten zuverlässig, bis der Verbraucher diese empfangen kann. An der Nachrichten-API können diverse Fehler mit Auswirkungen auf Ihre Anwendung auftreten. Diese lassen sich grob in die folgenden Kategorien unterteilen:

* Benutzerfehler, z. B. ein Code, der auf ein ungültiges Argument
  hindeutet. Empfohlene Maßnahme: Korrigieren Sie den Code, bevor Sie
  fortfahren.
* Installations-/Konfigurationsfehler, z. B. eine mit dem Thema
  verbundene Warteschlange oder ein Thema ist nicht vorhanden oder wurde
  gelöscht. Empfohlene Maßnahme: Überprüfen Sie die Konfiguration, und
  ändern Sie diese, falls erforderlich.
* Vorübergehende Fehler, z. B. eine Antwort, die angibt, dass der Dienst
  die Anforderungen zum aktuellen Zeitpunkt nicht verarbeiten konnte.
  Empfohlene Maßnahme: Wiederholen Sie den Vorgang, oder benachrichtigen
  Sie die Benutzer. Weitere Informationen finden Sie unter [Behandeln
  vorübergehender Kommunikationsfehler][48].
* Sonstige Fehler, z. B. Zeitlimitüberschreitungen oder Fehler, die
  darauf hinweisen, dass eine Nachrichtensperre verloren gegangen ist.
  Empfohlene Maßnahme: Im Allgemeinen müssen Sie diese Ausnahmen nicht
  beheben, um eine Bereinigung oder einen Abbruch durchzuführen. Sie
  können für die Ablaufverfolgung genutzt werden.

Unter [Anhang: Messagingausnahmen][49] erhalten Sie einen Überblick über die Ausnahmen, mit denen Benutzer der Servicebus-Clientbibliotheken für .NET möglicherweise konfrontiert werden, sowie Empfehlungen für den Umgang mit jedem Ausnahmetyp. Da die Clientbibliotheken für .NET eng auf die Struktur der Servicebusbibliotheken für andere Sprachen abgestimmt sind, ist diese Anleitung möglicherweise auch dann hilfreich, wenn Sie nicht in einer .NET-Sprache programmieren. In einigen Fällen, z. B. bei vorübergehenden Fehlern, können Sie den Vorgang wiederholen. Wenden Sie zur Behebung vorübergehender Fehler die Problembehandlungsverfahren an, die zuvor in diesem Artikel beschrieben wurden. Weitere Informationen, bewährte Methoden und Beispielcode, der die Behebung vorübergehender Service Bus-Fehler in einer .NET-Anwendung veranschaulicht, finden Sie im Abschnitt [Behandeln vorübergehender Kommunikationsfehler][48] des Artikels [Best Practices zur Nutzung der Azure-Servicebus-API für Brokermessaging][50] im Azure-Entwicklerleitfaden.

Ein anderer wichtiger Bereich bei der Entwicklung einer Anwendung, die Brokermessaging verwendet, ist die Implementierung einer zuverlässigen Nachrichtenempfangslogik, die Anomalien in Nachrichten präzise und effizient verwaltet. Im Abschnitt "Implementieren zuverlässiger Nachrichtenempfangsschleifen" des Artikels [Best Practices zur Nutzung der Azure-Servicebus-API für Brokermessaging][50] werden verschiedene Techniken für die Nutzung des **PeekLock**-Empfangsmodus beschrieben. Dieser Modus unterstützt mehrere Nachrichtenübermittlungen, wenn die Nachricht beim ersten Versuch nicht erfolgreich übermittelt werden konnte. Der Artikel empfiehlt bewährte Methoden, die sicherstellen, dass Ihre Anwendung die Nachrichten nicht doppelt verarbeitet. Zudem hilft er Ihnen, Probleme aufgrund von Sperrtimeout zu vermeiden und die Leistung im **PeekLock**-Modus insgesamt zu verbessern, indem eine unmittelbare Verarbeitung der Nachrichten sichergestellt wird. Der Artikel enthält außerdem Beispielcode, der die Servicebus-Clientbibliotheken für .NET verwendet.

### Weitere Ressourcen zur Problembehandlung

Weitere Informationen über allgemeine Servicebusfehler und Methoden, wie Sie diese analysieren und beheben können, finden Sie unter
[Problembehandlung von Service Bus][51].

## Azure Active Directory Access Control Service (ACS)

Problembehandlung

* Fehlercodes:
  [http://msdn.microsoft.com/de-de/library/windowsazure/gg185949.aspx][52]
* Einschränkungen des ACS:
  [http://msdn.microsoft.com/de-de/library/windowsazure/gg185909.aspx][53]

<h2><a id="SQLTroubleshooting" ></a>Problembehandlung in Azure SQL-Datenbank</h2>


Bei der Arbeit mit einer Azure SQL-Datenbank ist aufgrund der dezentralen Natur der Azure SQL-Datenbankanwendungen besondere Sorgfalt geboten. In diesem Abschnitt werden verschiedene Bereiche besprochen, die Aufmerksamkeit verdienen. Hierbei handelt es sich nicht um eine umfassende Liste. Um unterstützen Azure SQL-Datenbankcode zu schreiben, müssen Sie die Rückgabecodes analysieren und sicherstellen, dass ein zuverlässiger Wiederholungscode für die Problembehandlung vorhanden ist.

Ihre Anwendung muss Anmeldefehler ordnungsgemäß verarbeiten. SQL-Datenbankinstanzen setzen die Verwendung von SQL-Authentifizierung voraus. Wenn Sie sich nicht anmelden können, sind entweder Ihre Anmeldeinformationen ungültig oder die angeforderte Datenbank ist nicht verfügbar.

Wenn kein Zugriff auf den Dienst besteht, muss Ihre Anwendung dies handhaben können. Wenn der Server bereits bereitgestellt wurde und der Azure SQL-Datenbankdienst verfügbar ist (Sie können dies auf der Seite [Azure Health Status][54] prüfen), ist die Ursache vermutlich ein Konfigurationsproblem in Ihrer lokalen Installation. Beispiele: Der Name kann nicht aufgelöst werden (was mithilfe von Tools wie "Tracert" geprüft werden kann), eine Firewall blockiert den von der SQL-Datenbank verwendeten Port 1433, oder Sie verwenden einen nicht ordnungsgemäß konfigurierten Proxyserver. Verwenden Sie zur Behebung dieser Fehler die gleichen Techniken wie für SQL Server. Weitere Informationen finden Sie unter [SQL Database Connectivity Troubleshooting Guide][55] und [Problembehandlung bei der Windows Azure SQL-Datenbank][56].

Ihre Anwendung muss allgemeine Netzwerkfehler beheben. Allgemeine Netzwerkfehler können auftreten, da die Azure SQL-Datenbank Benutzer in folgenden Situationen trennt:

* Wenn eine Verbindung für längere Zeit inaktiv ist
* Wenn eine Verbindung übermäßig viele Ressourcen beansprucht oder eine
  Transaktion für längere Zeit blockiert
* Wenn der Server zu stark ausgelastet ist

Verwenden Sie zum Optimieren der Leistung der Azure SQL-Datenbank die gleichen Techniken wie für SQL Server. Weitere Informationen finden Sie in den folgenden Themen:

* [Problembehandlung von Abfragen][57] in SQL Server Books Online
* [Troubleshoot and Optimize Queries with SQL Database][58]
* [SQL Database Performance Considerations and Troubleshooting][59]
* [Improving Your I/O Performance][60]
* [System Center Management Pack für SQL Server][61]

Die Azure SQL-Datenbank nutzt einen Teil der SQL Server-Fehlermeldungen. Weitere Informationen über SQL Server-Fehler finden Sie unter
[Fehler- und Ereignisreferenz (Datenbankmodul)][62] in SQL Server Books Online.

Wenn Sie Anmeldenamen oder Kennwörter wiederherstellen müssen, wenden Sie sich bitte an Ihren Dienstadministrator, der Ihnen den entsprechenden Zugriff auf den Server und die Datenbank erteilt. Dienstadministratoren können außerdem ihre eigenen Kennwörter über das Azure-Verwaltungsportal zurücksetzen.

SQL-Datenbankabfragen können aus verschiedenen Gründen fehlschlagen, z. B. eine falsch formulierte Abfrage oder Netzwerkfehler. Einige Fehler sind vorübergehender Natur, d. h. sie gehen von alleine weg. Für diese Fehlerkategorie ist es sinnvoll, die Abfrage nach kurzer Zeit zu wiederholen. Wenn die Abfrage auch nach mehreren Wiederholung weiterhin fehlschlägt, sollten Sie einen Fehler melden. Selbstverständlich sind nicht alle Fehler vorübergehend. SQL-Fehler 102 "Falsche Syntax" löst sich beispielsweise nicht von selbst, egal wie oft Sie die Abfrage wiederholen. Anders ausgedrückt, die Implementierung einer stabilen Wiederholungslogik erfordert gewisse Überlegungen. Bevor die Verbindung von Benutzern getrennt wird, wird nach Möglichkeit ein Tabular Data Stream (TDS)-Fehlertoken gesendet. Um die Anwendungserfahrung zu verbessern, empfehlen wir die Implementierung einer Wiederholungslogik in Ihren SQL-Datenbankanwendungen, durch die diese Fehler erfasst werden. Stellen Sie im Falle eines Fehlers die Verbindung wieder her, und führen Sie die fehlgeschlagenen Befehle oder Abfragen erneut aus. Weitere Informationen finden Sie unter den folgenden Links:

* [Retry Logic for Transient Failures in SQL Database][63]
* [SQL Database Retry Logic Sample][64]
* [The Transient Fault Handling Application Block][65]

### Azure SQL-Sicherungs- und -Wiederherstellungsstrategie

Azure SQL-Datenbank erfordert aufgrund der Umgebung und der verfügbaren Tools eine eigene Sicherungs- und Wiederherstellungsstrategie. In vielerlei Hinsicht wurden die Risiken dadurch minimiert, dass sich die Datenbank in den Microsoft-Datencentern befindet. Die heute verfügbaren Tools decken weitere Sicherheitsfaktoren ab. In Zukunft wird es jedoch noch bessere Tools geben, die uns diese Aufgabe weiter vereinfachen. Red-gate veröffentlichte kürzlich ein kostenloses Tool für die Sicherung und Wiederherstellung von SQL-Datenbanken, welches Sie unter diesem Link finden: [http://www.red-gate.com/products/dba/sql-azure-backup/][66].

Mit SQL Data Sync können Sie mühelos bidirektionale Synchronisierungen über die Data Sync-Website erstellen und planen, ohne eine einzige Codezeile schreiben zu müssen. Weitere Informationen finden Sie hier: [http://msdn.microsoft.com/de-de/library/windowsazure/hh456371.aspx][67].

Weiterer Informationen zu Sicherungs- und Wiederherstellungsstrategien für SQL-Datenbanken finden Sie in den folgenden Artikeln:

* In diesem Thema erhalten Sie einen Überblick über die Sicherungs- und
  Wiederherstellungsstrategien für SQL-Datenbanken:
  [http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx][68]
* In diesem Thema wird erklärt, wie Sie eine Datenbank in einer anderen
  Datenbank auf dem gleichen Server sichern:
  [http://msdn.microsoft.com/de-de/library/windowsazure/ff951631.aspx][69]
* In diesem Thema wird erklärt, wie Sie eine vorhandene
  SQL-Datenbankinstanz in einen BLOB auf einem bestimmten Speicherkonto
  exportieren:
  [http://msdn.microsoft.com/de-de/library/windowsazure/hh335292.aspx][70]
* In diesem Thema wird erklärt, wie Sie eine vorhandene
  SQL-Datenbankinstanz aus einer bacpac-Datei in einem BLOB importieren:
  [http://msdn.microsoft.com/de-de/library/windowsazure/hh335291.aspx][71]
* In diesem Thema werden die Funktionen für unterbrechungsfreie
  Geschäftsabläufe der SQL-Datenbank beschrieben:
  [http://msdn.microsoft.com/de-de/library/windowsazure/hh852669.aspx][72]

<h2><a id="Cache" ></a>Azure Caching</h2>


Azure Caching gibt es in zwei Ausführungen: Azure Shared Caching und rollenbasiertes Azure Caching (Preview). Shared Caching ist ein mehrinstanzenfähiger Azure-Dienst, der Zwischenspeicherungsdienste bereitstellt. Bei Azure Caching (Preview) wird die Zwischenspeicherung auf einer Rolle gehostet, wobei teilweise Speicher auf den virtuellen Computern verwendet wird, auf denen Ihre Rolleninstanzen gehostet werden. Um Fehler bei Azure Caching zu behandeln, untersuchen Sie das Verhalten des Caches, indem Sie Fehlercodes prüfen und Ausnahmen aufzeichnen. Beim rollenbasierten Caching (Preview) können Sie außerdem Leistungszähler nutzen. Zwischenspeicherungsfehler fallen normalerweise in eine der beiden folgenden Kategorien:

* Kontingentbezogene Fehler, d. h. ein Kontingent wurde überschritten
  (Shared Caching)
* Drosselung, d. h. es ist nicht genügend physischer Speicher vorhanden,
  um die Zwischenspeicherung von weiteren Elementen zu unterstützen
* Entfernung, d. h. Elementen werden zwangsweise entfernt, um Platz für
  neue Elemente zu schaffen, was die Anwendungsleistung beeinträchtigt
* Ablauf, d. h. es wurden zu kurze oder zu lange Ablauffristen
  festgelegt

Weitere Informationen zu kontingentbezogenen Fehlern finden Sie unter [Informationen zu Kontingenten für Windows Azure Shared Caching][73].



[1]: https://www.windowsazure.com/de-de/develop/overview/
[2]: http://msdn.microsoft.com/de-de/library/hh680918%28v=pandp.50%29.aspx
[3]: http://nuget.org/packages/Microsoft.Experience.CloudFx
[4]: http://msdn.microsoft.com/de-de/magazine/ff714589.aspx
[5]: http://www.microsoft.com/download/en/details.aspx?id=11324
[6]: http://www.microsoft.com/de-de/server-cloud/system-center/operations-manager.aspx
[7]: http://wapmmc.codeplex.com/
[8]: http://cerebrata.com/Products/AzureDiagnosticsManager/
[9]: http://cerebrata.com/Products/CloudStorageStudio/
[10]: http://cerebrata.com/Products/AzureManagementCmdlets/
[11]: http://www.cerebrata.com
[12]: http://www.compuware.com/application-performance-management/
[13]: http://www.keynote.com/solutions/
[14]: http://pingdom.com/
[15]: http://www.alertbot.com/products/website-monitoring/default.aspx
[16]: http://www.apicasystem.com/integration-partners/
[17]: http://www.microsoft.com/de-de/server-cloud/system-center/avicode.aspx
[18]: http://msdn.microsoft.com/de-de/library/windowsazure/hh369930.aspx
[19]: http://azurevmassist.codeplex.com/
[20]: https://github.com/dannycoates/node-inspector
[21]: http://weblogs.asp.net/jimwang/archive/2012/04/17/debugging-node-node-inspector-in-the-azure-emulator.aspx
[22]: https://github.com/windowsazure/iisnode/downloads
[23]: http://howtonode.org/debugging-with-node-inspector
[24]: http://msdn.microsoft.com/de-de/library/dd264915.aspx
[25]: http://www.fiddler2.com/fiddler2/
[26]: http://go.microsoft.com/fwlink/?LinkID=90561
[27]: http://go.microsoft.com/fwlink/?LinkId=246619
[28]: http://logparserplus.com/Examples
[29]: http://technet.microsoft.com/de-de/library/ee692659.aspx
[30]: http://www.microsoft.com/downloads/en/details.aspx?familyid=7287252C-402E-4F72-97A5-E0FD290D4B76&displaylang=enBlockquote
[31]: http://logparserplus.com/article/2
[32]: http://msdn.microsoft.com/de-de/library/7a50syb3%28v=vs.90%29.aspx
[33]: http://technet.microsoft.com/de-de/edge/Video/hh867800
[34]: https://www.suse.com/documentation/
[35]: https://help.ubuntu.com/
[36]: http://centos.org/
[37]: http://msdn.microsoft.com/de-de/library/windowsazure/dd179382.aspx
[38]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx
[39]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx
[40]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
[41]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx
[42]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/17/windows-azure-storage-explorers.aspx
[43]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
[44]: http://en.wikipedia.org/wiki/Enterprise_service_bus
[45]: http://msdn.microsoft.com/de-de/library/windowsazure/ee732538.aspx
[46]: http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a
[47]: http://msdn.microsoft.com/de-de/library/windowsazure/ee706729.aspx
[48]: http://msdn.microsoft.com/de-de/library/hh851746(VS.103).aspx
[49]: http://msdn.microsoft.com/de-de/library/hh418082.aspx
[50]: http://msdn.microsoft.com/de-de/library/windowsazure/hh545245.aspx
[51]: http://msdn.microsoft.com/de-de/library/windowsazure/ee706702.aspx
[52]: http://msdn.microsoft.com/de-de/library/windowsazure/gg185949.aspx
[53]: http://msdn.microsoft.com/de-de/library/windowsazure/gg185909.aspx
[54]: http://go.microsoft.com/fwlink/p/?LinkId=168847
[55]: http://social.technet.microsoft.com/wiki/contents/articles/sql-azure-connectivity-troubleshooting-guide.aspx
[56]: http://msdn.microsoft.com/de-de/library/ee730906.aspx
[57]: http://msdn.microsoft.com/de-de/library/ms186351(SQL.100).aspx
[58]: http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-sql-azure.aspx
[59]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI314
[60]: http://blogs.msdn.com/b/sqlazure/archive/2010/07/27/10043069.aspx?PageIndex=2#comments
[61]: http://www.microsoft.com/de-de/download/details.aspx?id=10631
[62]: http://go.microsoft.com/fwlink/p/?LinkId=166622
[63]: http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-sql-azure.aspx
[64]: http://code.msdn.microsoft.com/windowsazure/SQL-Azure-Retry-Logic-2d0a8401
[65]: http://msdn.microsoft.com/de-de/library/hh680934(PandP.50).aspx
[66]: http://www.red-gate.com/products/dba/sql-azure-backup/
[67]: http://msdn.microsoft.com/de-de/library/windowsazure/hh456371.aspx
[68]: http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx
[69]: http://msdn.microsoft.com/de-de/library/windowsazure/ff951631.aspx
[70]: http://msdn.microsoft.com/de-de/library/windowsazure/hh335292.aspx
[71]: http://msdn.microsoft.com/de-de/library/windowsazure/hh335291.aspx
[72]: http://msdn.microsoft.com/de-de/library/windowsazure/hh852669.aspx
[73]: http://msdn.microsoft.com/de-de/library/gg185683.aspx