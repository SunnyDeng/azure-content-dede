# Compute

Azure ermöglicht die Bereitstellung und Überwachung Ihres in
einem Microsoft-Rechenzentrum ausgeführten Anwendungscodes. Wenn Sie eine Anwendung
erstellen und in Azure ausführen, werden der Code und die
Konfiguration gemeinsam als gehosteter Azure-Dienst bezeichnet. Gehostete Dienste können
problemlos verwaltet, hoch- und herunterskaliert, neu konfiguriert sowie mit neuen
Versionen des Codes Ihrer Anwendung aktualisiert werden. Dieser Artikel behandelt das
Anwendungsmodell von gehosteten Azure-Diensten.<span id="compare"></span></a>

## Inhaltsverzeichnis<span id="_GoBack"></span></a>

-   [Vorteile des Azure-Anwendungsmodells][Vorteile des Azure-Anwendungsmodells]
-   [Wichtige Konzepte zu gehosteten Diensten][Wichtige Konzepte zu gehosteten Diensten]
-   [Entwurfsüberlegungen zu gehosteten Diensten][Entwurfsüberlegungen zu gehosteten Diensten]
-   [Entwerfen Ihrer Anwendung im Hinblick auf Skalierbarkeit][Entwerfen Ihrer Anwendung im Hinblick auf Skalierbarkeit]
-   [Definition und Konfiguration von gehosteten Diensten][Definition und Konfiguration von gehosteten Diensten]
-   [Die Dienstdefinitionsdatei][Die Dienstdefinitionsdatei]
-   [Die Dienstkonfigurationsdatei][Die Dienstkonfigurationsdatei]
-   [Erstellen und Bereitstellen eines gehosteten Diensts][Erstellen und Bereitstellen eines gehosteten Diensts]
-   [Referenzen][Referenzen]

## <span id="benefits"></span> </a>Vorteile des Azure-Anwendungsmodells

Wenn Sie Ihre Anwendung als gehosteten Dienst bereitstellen,
sorgt Azure für die Erstellung eines oder mehrerer virtueller
Computer (Virtual Machines, VMs), die den Code Ihrer Anwendung enthalten,
und für das Starten der virtuellen Computer auf physischen Computern, die sich in einem der Azure-Rechenzentren befinden. Wenn Clientanforderungen an
Ihre gehostete Anwendung das Rechenzentrum erreichen, werden
diese Anforderungen mithilfe eines Lastenausgleichs gleichmäßig auf die virtuellen Computer verteilt. Das Hosten Ihrer Anwendung auf
Azure bietet drei wichtige Vorteile:

-   **Hohe Verfügbarkeit.** Hohe Verfügbarkeit bedeutet, dass
    Azure sicherstellt, dass Ihre Anwendung möglichst
    ohne Unterbrechung ausgeführt wird und auf Clientanforderungen reagieren kann. Wenn Ihre Anwendung beendet wird
    (beispielsweise aufgrund eines Ausnahmefehlers), erkennt
    Azure dies und startet Ihre Anwendung automatisch neu. Wenn
    bei dem Computer, auf dem Ihre Anwendung ausgeführt wird,
    ein Hardwarefehler auftritt, erkennt Azure
    dies ebenfalls und erstellt automatisch einen neuen virtuellen
    Computer auf einem anderen, funktionierenden physischen Computer, von dem Ihr Code dann ausgeführt wird. HINWEIS: Damit für Ihre Anwendung die
    Vereinbarung zum Servicelevel von Microsoft bezüglich einer
    Verfügbarkeit von 99,95 % gilt, muss Ihr Anwendungscode von mindestens zwei virtuellen Computern ausgeführt werden. Damit
    kann ein virtueller Computer Clientanforderungen
    verarbeiten, während Azure Ihren Code von einem fehlerhaften virtuellen Computer zu einem neuen, funktionierenden virtuellen Computer verschiebt.

-   **Skalierbarkeit.** Mit Azure können Sie die Anzahl der
    virtuellen Computer, auf denen Ihr Anwendungscode ausgeführt
    wird, problemlos und dynamisch ändern, damit die tatsächliche Last Ihrer Anwendung bewältigt werden kann. Damit können Sie
    Ihre Anwendung an die Arbeitsauslastung durch Ihre
    Kunden anpassen. Gleichzeitig bezahlen Sie nur für die
    virtuellen Computer, die Sie benötigen, und nur dann, wenn Sie diese benötigen. Wenn Sie die Anzahl der virtuellen Computer
    ändern möchten, erfolgt die Anpassung durch Azure innerhalb von
    Minuten. Sie können die Anzahl der virtuellen Computer so oft wie Sie möchten dynamisch ändern.

-   **Verwaltbarkeit.** Da es sich bei Azure um ein Platform as
    a Service (PaaS)-Angebot handelt, wird die zur Ausführung
    dieser Computer erforderliche Infrastruktur (die Hardware
    selbst, die Stromversorgung und das Netzwerk) verwaltet. Auch die Plattform wird von Azure verwaltet.
    Dadurch werden ein aktuelles Betriebssystem mit allen
    erforderlichen Patches und Sicherheitsupdates sowie
    Updates von Komponenten wie .NET Framework und Internet Information Server gewährleistet. Da alle virtuellen Computer
    Windows Server 2008 ausführen, bietet Azure
    zusätzliche Funktionen wie Diagnoseüberwachung, Unterstützung
    von Remotedesktop, Firewalls und die Konfiguration des Zertifikatspeichers. Diese Funktionen
    werden ohne Zusatzkosten bereitgestellt. Tatsächlich ist die
    Lizenz für das Windows Server 2008-Betriebssystem
    eingeschlossen, wenn Sie Ihre Anwendung in Azure ausführen. Da alle virtuellen Computer
    Windows Server 2008 ausführen, kann jeglicher Code, der mit Windows
    Server 2008 ausgeführt werden kann, auch in Azure ausgeführt werden.

## <span id="concepts"></span> </a>Wichtige Konzepte zu gehosteten Diensten

Wenn Sie Ihre Anwendung als gehosteter Dienst in Azure bereitstellen
, wird sie als eine oder mehrere *Rollen* ausgeführt. Mit einer *Rolle* werden die
Anwendungsdateien und die Konfiguration bezeichnet. Sie können eine oder mehrere Rollen für
Ihre Anwendung definieren. Jede dieser Rollen verfügt
über einen eigenen Satz von Anwendungsdateien sowie eine eigene Konfiguration. Für jede Rolle Ihrer Anwendung können Sie die Anzahl
der auszuführenden virtuellen Computer oder *Rolleninstanzen* festlegen. In der Abbildung unten sind
zwei einfache Beispiele für eine als gehosteter Dienst mit Rollen und
Rolleninstanzen modellierte Anwendung dargestellt.

##### Abbildung 1: Eine einfache Rolle mit drei Instanzen (virtuelle Computer), die in einem Azure-Rechenzentrum ausgeführt wird.

![image][image]

##### Abbildung 2: Zwei Rollen mit jeweils zwei Instanzen (virtuelle Computer), die in einem Azure-Rechenzentrum ausgeführt werden.

![image][1]

Rolleninstanzen verarbeiten in der Regel Internet-Clientanforderungen,
die das Rechenzentrum durch einen sogenannten *Eingabeendpunkt* erreichen. Eine Rolle
kann mit 0 oder mehr Eingabeendpunkten ausgestattet sein. Jeder Endpunkt weist ein Protokoll
(HTTP, HTTPS oder TCP) und einen Port auf. Üblicherweise wird eine Rolle mit
zwei Eingabeendpunkten konfiguriert: HTTP lauscht an Port 80, und HTTPS lauscht
an Port 443. In der Abbildung unten ist ein Beispiel für zwei
unterschiedliche Rollen mit unterschiedlichen Eingabeendpunkten dargestellt, die Clientanforderungen an die Rollen weiterleiten.

![image][2]

Wenn Sie einen gehosteten Dienst in Azure erstellen, wird
dieser einer öffentlich adressierbaren IP-Adresse zugewiesen, über die Clients auf den Dienst zugreifen können. Beim
Erstellen des gehosteten Diensts müssen Sie auch ein URL-Präfix
auswählen, das dieser IP-Adresse zugeordnet wird. Dieses Präfix muss eindeutig sein, da
Sie damit die URL "*Präfix*.cloudapp.net" reservieren, die dann nur von
Ihnen verwendet werden kann. Clients kommunizieren mit Ihren Rolleninstanzen mithilfe
der URL. In der Regel verteilen oder veröffentlichen Sie die
Azure-URL "*Präfix*.cloudapp.net" nicht. Stattdessen erwerben Sie einen DNS-Namen
von einer DNS-Registrierungsstelle Ihrer Wahl und konfigurieren
diesen, sodass Clientanforderungen an die Azure-URL umgeleitet werden. Weitere Informationen finden Sie unter
[Configuring a Custom Domain Name in Azure][Configuring a Custom Domain Name in Azure] (Konfigurieren eines benutzerdefinierten Domänennamens in Azure, in englischer Sprache).

## <span id="considerations"></span> </a>Entwurfsüberlegungen zu gehosteten Diensten

Beim Entwerfen einer Anwendung, die in einer Cloudumgebung ausgeführt
werden soll, müssen verschiedene Punkte wie
Latenz, hohe Verfügbarkeit und Skalierbarkeit beachtet werden.

Die Entscheidung, wo sich Ihr Anwendungscode befinden soll,
ist ein wichtiger Punkt beim Ausführen eines gehosteten Diensts in Azure. Üblicherweise
wird Ihre Anwendung in Rechenzentren bereitgestellt, die sich möglichst
nah an Ihren Kunden befinden, um die Latenz zu reduzieren
und eine optimale Leistung zu erreichen. Sie können jedoch auch ein
Rechenzentrum auswählen, das sich näher an Ihrem Unternehmen
oder Ihren Daten befindet, wenn Sie gerichtliche oder rechtliche Bedenken bezüglich Ihrer Daten oder deren Speicherort haben. Ihr Anwendungscode kann in sechs
Rechenzentren rund um die Welt gehostet werden. In der Tabelle unten sind
die verfügbaren Orte angegeben:

|---------------------|--------------------------------------|
| \*\*Land/Region\*\* | \*\*Unterregionen\*\*                |
| USA                 | Südlich zentral und nördlich zentral |
| Europa              | Nördlich und westlich                |
| Asien               | Südöstlich und östlich               |

Wenn Sie einen gehosteten Dienst erstellen, wählen Sie eine Unterregion
aus, mit der der Ort angegeben wird, an dem Ihr Code ausgeführt werden soll.

Damit eine hohe Verfügbarkeit und Skalierbarkeit gewährleistet ist,
müssen die Daten Ihrer Anwendung unbedingt in einem zentralen Repository
gespeichert werden, auf das mehrere Rolleninstanzen zugreifen können. Dazu bietet Azure verschiedene
Speicheroptionen wie Blobs, Tabellen und SQL-Datenbanken an. Weitere
Informationen zu diesen Speichertechnologien finden Sie im Artikel
[Data Storage Offerings in Azure][Data Storage Offerings in Azure] (Datenspeicherangebote in Azure, in englischer Sprache). In der Abbildung unten ist
dargestellt, wie der Lastenausgleich im Azure-Rechenzentrum
Clientanforderungen an unterschiedliche Rolleninstanzen verteilt,
die alle auf denselben Datenspeicher zugreifen.

![image][3]

In der Regel befinden sich Ihr Anwendungscode und Ihre Daten im
selben Rechenzentrum, da dies zu einer niedrigen Latenz (besseren Leistung)
führt, wenn Ihr Anwendungscode auf die Daten zugreift. Darüber hinaus müssen
Sie nicht für die Bandbreite bezahlen, wenn Daten im selben Rechenzentrum
verschoben werden.

## <span id="scale"></span> </a>Entwerfen Ihrer Anwendung im Hinblick auf Skalierbarkeit

Gelegentlich ist es wünschenswert, eine einzelne Anwendung (wie eine
einfache Website) in Azure zu hosten. Häufig bestehen
Anwendungen jedoch aus verschiedenen Rollen, die alle zusammenarbeiten. In
der Abbildung unten sind beispielsweise zwei Instanzen der Rolle
"Website", drei Instanzen der Rolle "Order Processing" und eine
Instanz der Rolle "Report Generator" dargestellt. Diese Rollen arbeiten zusammen, und der
Code für alle Rollen kann zusammengefasst und als eine Einheit in Azure
bereitgestellt werden.

![image][4]

Der Hauptgrund für das Aufteilen einer Anwendung in
verschiedene Rollen, die jeweils ihren eigenen Satz von Rolleninstanzen
(also virtuellen Computern) ausführen, ist die unabhängige Skalierung der Rollen. Beispielsweise kaufen möglicherweise viele
Kunden in der Weihnachtszeit Produkte von Ihrem Unternehmen. Daher möchten
Sie vielleicht die Anzahl der Rolleninstanzen, die Ihre Rolle
"Website" ausführen, und die Anzahl der Rolleninstanzen, die Ihre
Rolle "Order Processing" ausführen, erhöhen. Nach der Weihnachtszeit werden unter Umständen viele Produkte
zurückgeschickt. Daher benötigen Sie zwar weiterhin viele Instanzen
der Rolle "Website", jedoch weniger Instanzen der Rolle "Order Processing". In der übrigen Zeit benötigen Sie möglicherweise
nur wenige Instanzen der Rollen "Website" und "Order Processing". Und über den gesamten
Zeitraum ist eventuell nur eine Instanz der Rolle "Report Generator" erforderlich. Aufgrund der
Flexibilität der rollenbasierten Bereitstellung in Azure können Sie Ihre
Anwendung problemlos Ihren Geschäftsanforderungen anpassen.

In der Regel kommunizieren die Rolleninstanzen in Ihrem
gehosteten Dienst miteinander. Die Rolle "Website" nimmt beispielsweise
die Bestellung eines Kunden an, lagert die Verarbeitung der Bestellung
jedoch dann an die Instanzen der Rolle "Order Processing" aus. Die beste Methode zum Übergeben von
Arbeit von einem Satz von Rolleninstanzen an einen anderen Satz
von Instanzen ist die Verwendung der von Azure
bereitgestellten Queuing-Technologie, d. h. entweder des Warteschlangendiensts oder der Service Bus-Warteschlangen. Die Verwendung einer Warteschlange ist in
diesem Zusammenhang entscheidend. Mithilfe der Warteschlange kann der gehostete Dienst
seine Rollen unabhängig voneinander skalieren. Damit können Sie die Kosten in Abhängigkeit von der Arbeitsauslastung kontrollieren. Wenn
die Anzahl der Nachrichten in der Warteschlange mit der Zeit ansteigt,
können Sie die Anzahl der Rolleninstanzen "Order Processing" hochskalieren. Sinkt die Anzahl
der Nachrichten in der Warteschlange mit der Zeit, lässt sich die Anzahl
der Rolleninstanzen "Order Processing" entsprechend herunterskalieren. Damit bezahlen Sie nur für die
Instanzen, die zur Verarbeitung der tatsächlichen Arbeitsauslastung erforderlich sind.

Außerdem sorgt die Warteschlange für Zuverlässigkeit. Wenn die Anzahl der Rolleninstanzen
"Order Processing" herunterskaliert wird, entscheidet Azure,
welche Instanzen beendet werden. Möglicherweise wird eine Instanz beendet, die
gerade eine Warteschlangennachricht verarbeitet. Da damit die Verarbeitung der
Nachricht nicht erfolgreich abgeschlossen wurde, wird die Nachricht für eine
andere Instanz der Rolle "Order Processing" sichtbar, die die
Nachricht dann aufnimmt und verarbeitet. Aufgrund der Sichtbarkeit von Warteschlangennachrichten
werden Nachrichten garantiert verarbeitet. Die Warteschlange fungiert
außerdem als Lastenausgleich, indem sie ihre Nachrichten effektiv an
alle Rolleninstanzen verteilt, die Nachrichten von ihr anfordern.

Für die Instanzen der Rolle "Website" können Sie den eingehenden
Datenverkehr überwachen und ebenfalls entscheiden, ob die Anzahl dieser Rolleninstanzen hoch- oder herunterskaliert werden soll. Mithilfe
der Warteschlange lässt sich die Anzahl der Instanzen der
Rolle "Website" unabhängig von den Instanzen der Rolle "Order Processing" skalieren. Diese Funktion
ist sehr nützlich und ermöglicht ein hohes Maß an Flexibilität. Wenn Ihre Anwendung
weitere Rollen umfasst, können Sie selbstverständlich weitere
Warteschlangen zur Kommunikation zwischen diesen hinzufügen, um dieselben
Skalierungs- und Kostenvorteile zu nutzen.

## <span id="defandcfg"></span> </a>Definition und Konfiguration von gehosteten Diensten

Für die Bereitstellung eines gehosteten Diensts in Azure müssen
Sie auch eine Dienstdefinitionsdatei und eine Dienstkonfigurationsdatei bereitstellen. Bei beiden dieser
Dateien handelt es sich um XML-Dateien. Sie ermöglichen
die deklarative Angabe von Bereitstellungsoptionen für Ihren gehosteten Dienst. In der Dienstdefinitionsdatei
werden alle Rollen, die Ihr gehosteter Dienst umfasst, sowie deren
Kommunikation untereinander beschrieben. In der Dienstkonfigurationsdatei werden die Anzahl
der Instanzen für die einzelnen Rollen sowie die Einstellungen zur
Konfiguration der einzelnen Rolleninstanzen beschrieben.

## <span id="def"></span> </a>Die Dienstdefinitionsdatei

Wie bereits erwähnt handelt es sich bei der Dienstdefinitionsdatei
(CSDEF-Datei) um eine XML-Datei, in der die verschiedenen
Rollen beschrieben werden, aus denen Ihre Gesamtanwendung besteht. Das vollständige Schema für die XML-Datei ist unter folgendem Link zu finden:
[<http://msdn.microsoft.com/de-de/library/windowsazure/ee758711.aspx>][<http://msdn.microsoft.com/de-de/library/windowsazure/ee758711.aspx>].
Die CSDEF-Datei enthält ein WebRole- oder ein WorkerRole-Element für jede
Rolle, die Sie in Ihrer Anwendung verwenden möchten. Wenn Sie eine Rolle als Webrolle (mit dem
WebRole-Element) bereitstellen, wird der Code mit einer
Rolleninstanz ausgeführt, die Windows Server 2008 und Internet Information
Server (IIS) enthält. Wenn eine Rolle als Workerrolle (mit dem
WorkerRole-Element) bereitgestellt wird, enthält die Rolleninstanz ebenfalls
Windows Server 2008 (IIS wird nicht installiert).

Sie können selbstverständlich eine Workerrolle erstellen und bereitstellen,
die einen anderen Mechanismus zum Lauschen von eingehenden
Webanforderungen verwendet (beispielsweise kann Ihr Code einen .NET HttpListener erstellen und verwenden). Da alle Rolleninstanzen
Windows Server 2008 ausführen, kann Ihr Code alle Vorgänge ausführen, die
üblicherweise für eine unter Windows Server 2008 ausgeführte Anwendung zur Verfügung stehen.

1.  

Für jede Rolle geben Sie die gewünschte Größe des virtuellen Computers
an, die Instanzen dieser Rolle verwenden sollen. In der Tabelle unten sind die verschiedenen verfügbaren
Größen von virtuellen Computern und die jeweiligen Attribute angegeben:

|----------------------------------------|-------------|-------------|---------------------|-------------------------------------|
| \*\*Größe des virtuellen Computers\*\* | \*\*CPU\*\* | \*\*RAM\*\* | \*\*Datenträger\*\* | \*\*Höchstwert der Netzwerk-E/A\*\* |
| \*\*Sehr klein\*\*                     | 1 x 1,0 GHz | 768 MB      | 20GB                | \\~5 MBit/s                         |
| \*\*Klein\*\*                          | 1 x 1,6 GHz | 1,75 GB     | 225GB               | \\~100 MBit/s                       |
| \*\*Mittel\*\*                         | 2 x 1,6 GHz | 3,5 GB      | 490GB               | \\~200 MBit/s                       |
| \*\*Groß\*\*                           | 4 x 1,6 GHz | 7 GB        | 1 TB                | \\~400 MBit/s                       |
| \*\*Sehr groß\*\*                      | 8 x 1,6 GHz | 14 GB       | 2 TB                | \\~800 MBit/s                       |

Ihnen wird die Nutzung jedes als Rolleninstanz verwendeten virtuellen
Computers stundenweise in Rechnung gestellt. Außerdem werden Daten in
Rechnung gestellt, die Ihre Rolleninstanzen aus dem Rechenzentrum senden. In das Rechenzentrum eingehende Daten werden Ihnen nicht in Rechnung gestellt. Weitere
Informationen finden Sie in der [Azure-Preisübersicht][Azure-Preisübersicht]. Im Allgemeinen
ist die Verwendung vieler kleiner Rolleninstanzen anstelle von wenigen
großen Instanzen empfehlenswert, damit Ihre Anwendung eine größere Robustheit gegenüber Fehlern aufweist. Denn
über je weniger Rolleninstanzen Sie verfügen, desto stärker beeinträchtigt
ein Fehler in einer dieser Instanzen Ihre Gesamtanwendung. Außerdem müssen Sie wie
zuvor erwähnt mindestens zwei Instanzen für jede Rolle bereitstellen,
damit die von Microsoft angebotene Vereinbarung zum Servicelevel bezüglich einer Verfügbarkeit von 99,95 % gilt.

In der Dienstdefinitionsdatei (CSDEF-Datei) geben Sie außerdem viele
Attribute zu den einzelnen Rollen in Ihrer Anwendung an. Einige der nützlichsten
Elemente werden im Folgenden beschrieben:

-   **Zertifikate**. Sie verwenden Zertifikate, wenn Ihr Webdienst
    SSL unterstützt, sowie zum Verschlüsseln von Daten. Sämtliche Zertifikate müssen zu Azure
    hochgeladen werden. Weitere Informationen finden Sie unter [Verwalten
    von Zertifikaten in Azure][Verwalten
    von Zertifikaten in Azure]. Mit dieser XML-Einstellung werden bereits
    hochgeladene Zertifikate im Zertifikatspeicher der Rolleninstanz
    installiert, sodass diese vom Anwendungscode verwendet werden können.

-   **Konfigurationseinstellungsnamen**. Dieses Element ist für Werte
    vorgesehen, die Ihre Anwendung(en) während der Ausführung einer Rolleninstanz lesen sollen. Der tatsächliche
    Wert der Konfigurationseinstellungen wird in der
    Dienstkonfigurationsdatei (CSCFG-Datei) festgelegt, die jederzeit
    aktualisiert werden kann, ohne dass der Code erneut bereitgestellt werden muss. Tatsächlich können Sie Ihre
    Anwendungen so programmieren, dass die geänderten
    Konfigurationswerte ohne Downtime erkannt werden.

-   **Eingabeendpunkte**. In diesem Element geben Sie HTTP-, HTTPS-
    oder TCP-Endpunkte (mit Ports) an, die über Ihre URL
    "*Präfix*.cloadapp.net" von außen erreichbar sein sollen. Wenn Azure Ihre Rolle
    bereitstellt, wird die Firewall der Rolleninstanz
    automatisch konfiguriert.

-   **Interne Endpunkte**. In diesem Element geben Sie HTTP- oder
    TCP-Endpunkte an, die von anderen als Teil Ihrer Anwendung
    bereitgestellten Rolleninstanzen erreichbar sein sollen. Interne Endpunkte ermöglichen die
    Kommunikation aller Rolleninstanzen innerhalb Ihrer Anwendung,
    auf sie kann jedoch nicht von Rolleninstanzen zugegriffen werden, die sich außerhalb Ihrer Anwendung befinden.

-   **Importmodule**. Mit diesen Elementen werden optional nützliche
    Komponenten für Ihre Rolleninstanzen installiert. Es gibt Komponenten für Diagnoseüberwachung,
    Remotedesktop und Azure Connect (ermöglicht Ihrer
    Rolleninstanz den Zugriff auf lokale Ressourcen über einen sicheren Kanal).

-   **Lokaler Speicher**. Mit diesem Element wird ein Unterverzeichnis
    der Rolleninstanz zugeordnet, das Ihre Anwendung verwenden kann. Es wird ausführlicher im
    Artikel [Data Storage Offerings in Azure][Data Storage Offerings in Azure] (Datenspeicherangebote in Azure, in englischer Sprache) beschrieben.

-   **Startaufgaben**. Mithilfe von Startaufgaben können Sie
    vorausgesetzte Komponenten für eine Rolleninstanz installieren, wenn diese gestartet wird. Die Aufgaben
    können bei Bedarf mit erhöhten Rechten als Administrator ausgeführt werden.

## <span id="cfg"></span> </a>Die Dienstkonfigurationsdatei

Bei der Dienstkonfigurationsdatei (CSCFG-Datei) handelt es sich um eine
XML-Datei, in der Einstellungen beschrieben werden, die ohne die erneute Bereitstellung Ihrer Anwendung geändert werden können. Das
vollständige Schema für die XML-Datei ist unter folgendem Link zu finden:
[][]<http://msdn.microsoft.com/de-de/library/windowsazure/ee758710.aspx></a>.
Die CSCFG-Datei enthält ein Role-Element für jede
Rolle in Ihrer Anwendung. Einige der Elemente, die Sie in der CSCFG-Datei angeben
können, werden im Folgenden beschrieben:

-   **Betriebssystemversion**. Mit diesem Attribut können Sie die Betriebssystemversion
    auswählen, die für alle Rolleninstanzen verwendet werden soll,
    die Ihren Anwendungscode ausführen. Dieses Betriebssystem wird als *Gastbetriebssystem*
    bezeichnet, und alle neuen Versionen umfassen die
    neuesten Sicherheitspatches und -updates, die zum Zeitpunkt der Veröffentlichung des Gastbetriebssystems verfügbar sind. Wenn Sie das
    Attribut "osVersion" auf "\*" festlegen, aktualisiert
    Azure automatisch das Gastbetriebssystem für Ihre Rolleninstanzen, wenn
    neue Versionen des Gastbetriebssystems verfügbar werden. Sie können sich jedoch auch gegen
    automatische Updates entscheiden, indem Sie eine bestimmte Version des Gastbetriebssystems auswählen. Wenn
    beispielsweise das Attribut "osVersion" auf den Wert
    "WA-GUEST-OS-2.8\_201109-01" festgelegt wird, erhalten Ihre
    Rolleninstanzen das Betriebssystem, das auf folgender Webseite beschrieben wird:
    [][5]<http://msdn.microsoft.com/de-de/library/hh560567.aspx></a>. Weitere
    Informationen zu Versionen von Gastbetriebssystemen finden Sie unter
    [Managing Upgrades to the Azure Guests OS][Managing Upgrades to the Azure Guests OS] (Verwalten von Upgrades des Azure-Gastbetriebssystems, in englischer Sprache).

-   **Instanzen**. Der Wert dieses Elements gibt die Anzahl von
    Rolleninstanzen an, die zum Ausführen des Codes für eine bestimmte
    Rolle bereitgestellt werden sollen. Da Sie eine neue CSCFG-Datei zu Azure
    hochladen können (ohne Ihre Anwendung neu bereitzustellen), ist es
    problemlos möglich, den Wert dieses Elements zu ändern und
    eine neue CSCFG-Datei hochzuladen, um die Anzahl von
    Rolleninstanzen, die Ihren Anwendungscode ausführen, dynamisch zu erhöhen oder zu verringern. Damit können Sie Ihre Anwendung
    leicht hoch- oder herunterskalieren, um die tatsächlichen Arbeitsauslastungsanforderungen
    zu erfüllen und gleichzeitig zu kontrollieren, wie viel Ihnen für die Ausführung der Rolleninstanzen in Rechnung gestellt wird.

-   **Konfigurationseinstellungswerte**. Mit diesem Element werden Werte
    für die Einstellungen (die in der CSDEF-Datei definiert wurden) angegeben. Ihre Rolle kann diese Werte
    während der Ausführung lesen. Diese Konfigurationseinstellungswerte
    werden üblicherweise für Verbindungszeichenfolgen für eine
    SQL-Datenbank oder den Azure-Speicher verwendet, Sie können sie jedoch für beliebige Zwecke einsetzen.

## <span id="hostedservices"></span> </a>Erstellen und Bereitstellen eines gehosteten Diensts

Zum Erstellen eines gehosteten Diensts müssen Sie zunächst das [Azure-Verwaltungsportal][Azure-Verwaltungsportal] besuchen und einen gehosteten Dienst
bereitstellen, indem Sie ein DNS-Präfix sowie das Rechenzentrum angeben,
in dem Ihr Code ausgeführt werden soll. In Ihrer Entwicklungsumgebung
erstellen Sie dann Ihre Dienstdefinitionsdatei (CSDEF-Datei)
und Ihren Anwendungscode. Dann fassen Sie sämtliche Dateien in einer Dienstpaketdatei (CSPKG-Datei) zusammen (zippen). Außerdem müssen
Sie Ihre Dienstkonfigurationsdatei (CSCFG-Datei) vorbereiten. Zum Bereitstellen Ihrer
Rolle laden Sie die CSPKG-Datei und die CSCFG-Datei mit
der Azure-Service-Verwaltungs-API hoch. Nach der Bereitstellung sorgt Azure dafür,
dass Rolleninstanzen im Rechenzentrum (je nach den
Konfigurationsdaten) bereitgestellt werden, Ihr Anwendungscode aus dem
Paket extrahiert und in die Rolleninstanzen kopiert wird und die Instanzen gestartet werden. Nun wird Ihr Code ausgeführt.

In der Abbildung unten sind die CSPKG-Datei und die CSCFG-Datei dargestellt,
die Sie mit Ihrem Entwicklungscomputer erstellen. Die CSPKG-Datei enthält die CSDEF-Datei und
den Code für zwei Rollen. Nach dem Hochladen der CSPKG-Datei und der
CSCFG-Datei mit der Azure-Service-Verwaltungs-API
erstellt Azure die Rolleninstanzen im Rechenzentrum. In diesem Beispiel wird in der CSCFG-Datei
angegeben, dass Azure drei Instanzen von Rolle Nr. 1 und
zwei Instanzen von Rolle Nr. 2 erstellen soll.

![image][6]

Weitere Informationen zum Bereitstellen, Aktualisieren und erneuten
Konfigurieren Ihrer Rollen finden Sie im Artikel [Bereitstellen und Aktualisieren von Azure-Anwendungen][Bereitstellen und Aktualisieren von Azure-Anwendungen]
.<span id="Ref"></span></a>

## <span id="references"></span> </a>Referenzen

-   [Erstellen eines gehosteten Diensts für Azure][Erstellen eines gehosteten Diensts für Azure]

-   [Verwalten von gehosteten Diensten in Azure][Verwalten von gehosteten Diensten in Azure]

-   [Migrieren von Anwendungen zu Azure][Migrieren von Anwendungen zu Azure]

-   [Configuring an Azure Application][Configuring an Azure Application]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Geschrieben von Jeffrey Richter (Wintellect)</p>

</div>

  [Vorteile des Azure-Anwendungsmodells]: #benefits
  [Wichtige Konzepte zu gehosteten Diensten]: #concepts
  [Entwurfsüberlegungen zu gehosteten Diensten]: #considerations
  [Entwerfen Ihrer Anwendung im Hinblick auf Skalierbarkeit]: #scale
  [Definition und Konfiguration von gehosteten Diensten]: #defandcfg
  [Die Dienstdefinitionsdatei]: #def
  [Die Dienstkonfigurationsdatei]: #cfg
  [Erstellen und Bereitstellen eines gehosteten Diensts]: #hostedservices
  [Referenzen]: #references
  [image]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configuring a Custom Domain Name in Azure]: http://www.windowsazure.com/de-de/develop/net/common-tasks/custom-dns/
  [Data Storage Offerings in Azure]: http://www.windowsazure.com/de-de/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  [Azure-Preisübersicht]: http://www.windowsazure.com/de-de/pricing/calculator/
  [Verwalten
  von Zertifikaten in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg981929.aspx
  []: http://msdn.microsoft.com/de-de/library/windowsazure/ee758710.aspx
  [5]: http://msdn.microsoft.com/de-de/library/hh560567.aspx
  [Managing Upgrades to the Azure Guests OS]: http://msdn.microsoft.com/de-de/library/ee924680.aspx
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com/
  [6]: ./media/application-model/application-model-8.jpg
  [Bereitstellen und Aktualisieren von Azure-Anwendungen]: http://www.windowsazure.com/de-de/develop/net/fundamentals/deploying-applications/
  [Erstellen eines gehosteten Diensts für Azure]: http://msdn.microsoft.com/de-de/library/gg432967.aspx
  [Verwalten von gehosteten Diensten in Azure]: http://msdn.microsoft.com/de-de/library/gg433038.aspx
  [Migrieren von Anwendungen zu Azure]: http://msdn.microsoft.com/de-de/library/gg186051.aspx
  [Configuring an Azure Application]: http://msdn.microsoft.com/de-de/library/windowsazure/ee405486.aspx
