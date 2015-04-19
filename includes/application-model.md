# Compute

Azure ermöglicht es Ihnen, Anwendungscode bereitzustellen und zu überwachen,
der in einem Microsoft-Rechenzentrum ausgeführt wird. Wenn Sie eine Anwendung erstellen
und auf Azure ausführen, wird der Code und die Konfiguration zusammen als
gehosteter Azure-Dienst bezeichnet. Gehostete Dienste können einfach
verwaltet, aufwärts und abwärts skaliert, neu konfiguriert und mit neuen Versionen des
Anwendungscodes aktualisiert werden. Dieser Artikel konzentriert sich auf das Anwendungsmodell mit gehosteten Azure-
Diensten.<a id="compare" name="compare"></a>

## Inhaltsverzeichnis<a id="_GoBack" name="_GoBack"></a>

-   [Vorteile des Azure-Anwendungsmodells][]
-   [Wichtige Konzepte zu gehosteten Diensten][]
-   [Entwurfsüberlegungen zu gehosteten Diensten][]
-   [Entwerfen Ihrer Anwendung im Hinblick auf Skalierbarkeit][]
-   [Definition und Konfiguration von gehosteten Diensten][]
-   [Die Dienstdefinitionsdatei][]
-   [Die Dienstkonfigurationsdatei][]
-   [Erstellen und Bereitstellen eines gehosteten Diensts][]
-   [Referenzen][]

## <a id="benefits"> </a>Vorteile des Azure-Anwendungsmodells

Wenn Sie Ihre Anwendung als gehosteten Dienst bereitstellen,
erstellt Azure mindestens einen virtuellen Computer (VM) mit Ihrem
Anwendungscode und startet die virtuellen Computer auf physischen Computern, die sich in
einem der Azure-Rechenzentren befinden. Wenn Clientanforderungen an Ihre gehostete
Anwendung das Rechenzentrum erreichen, verteilt ein Lastenausgleich diese
Anforderungen gleichmäßig auf die virtuellen Computer. Wenn Ihre Anwendung in
Azure gehostet wird, ergeben sich drei wichtige Vorteile:

-   **Hohe Verfügbarkeit.** Hohe Verfügbarkeit bedeutet, dass Azure sicherstellt,
    dass Ihre Anwendung so häufig wie möglich ausgeführt wird und
auf Clientanforderungen reagieren kann. Wenn Ihre Anwendung (z. B. aufgrund einer
    nicht behandelten Ausnahme) beendet wird, erkennt Azure dass
und startet die Anwendung automatisch neu. Wenn der
    Computer auf dem Computer, auf der die Anwendung ausgeführt wird,
    ein Hardwarefehler auftritt, erkennt das Azure ebenfalls und
    erstellt automatisch einen neuen virtuellen Computer auf einem anderen, funktionierenden physischen Computer
und führt den Code von dort aus. HINWEIS: Damit die Anwendung
    die Microsoft-Servicelevelvereinbarung von 99,95 % Verfügbarkeit einhalten kann,
müssen mindestens zwei virtuelle Computer den Anwendungscode ausführen. Dadurch
    kann ein virtueller Computer Clientanforderungen verarbeiten, während Azure
    den Code von einem fehlerhaften virtuellen Computer auf einen neuen, funktionierenden virtuellen Computer verschiebt.

-   **Skalierbarkeit.** Azure ermöglicht Ihnen das problemlose und dynamische
    Ändern der Anzahl der virtuellen Computer, die den Anwendungscode ausführen, um
die tatsächliche Last Ihrer Anwendung verarbeiten zu können. Auf diese Weise können Sie
    die Anwendung auf die Arbeitslast, mit der Ihre Kunden
    die Anwendung nutzen, anpassen, während Sie nur für die virtuellen Computer bezahlen,
wenn Sie sie benötigen. Wenn Sie die Anzahl der virtuellen Computer ändern möchten,
    reagiert Azure innerhalb von Minuten, sodass es möglich ist, die Anzahl der
    ausgeführten virtuellen Computer so oft wie gewünscht dynamisch zu ändern.

-   **Verwaltbarkeit.** Da Azure ein PaaS-Angebot (Platform as a Service)
    ist, verwaltet es die Infrastruktur (die Hardware selbst,
    Elektrizität und Netzwerk), die zum Ausführen dieser Computer erforderlich ist,
selbst. Azure verwaltet auch die Plattform und stellt so ein
    aktuelles Betriebssystem mit allen erforderlichen Patches und
    Sicherheitsupdates sowie Updates von Komponenten wie .NET
Framework und Internet Information Server sicher. Da auf allen virtuellen Computern
    Windows Server 2008 ausgeführt wird, stellt Azure zusätzliche
    Funktionen, wie z. B. Diagnoseüberwachung, Unterstützung von Remotedesktop,
Firewalls und Konfiguration des Zertifikatspeichers, bereit. Alle diese Funktionen
werden ohne zusätzliche Kosten bereitgestellt. Wenn Sie Ihre
    Anwendung in Azure ausführen, ist die Windows Server 2008-Betriebssystemlizenz
enthalten. Da auf allen virtuellen Computern
    Windows Server 2008 ausgeführt wird, funktioniert jeder Code, der auf Windows Server 2008 ausgeführt wird,
    genau so gut auch in Azure.

## <a id="concepts"> </a>Wichtige Konzepte zu gehosteten Diensten

Wenn Ihre Anwendung als gehosteter Dienst in Azure bereitgestellt wird,
wird er als eine oder mehrere *Rollen* ausgeführt. Eine *Rolle* bezieht sich auf Anwendungsdateien
und die Konfiguration. Sie können eine oder mehrere Rollen für die
Anwendung definieren, jede mit einem eigenen Satz Anwendungsdateien und
einer eigenen Konfiguration. Sie können für jede Rolle in der Anwendung die
Anzahl der virtuellen Computern oder auszuführenden *Rolleninstanzen* angeben. Die Abbildung unten zeigt zwei
einfache Beispiele für eine Anwendung, die als gehosteter Dienst mithilfe
von Rollen und Rolleninstanzen verwendet wird.

##### Abbildung 1: Eine einfache Rolle mit drei Instanzen (virtuelle Computer), die in einem Azure-Rechenzentrum ausgeführt wird

![image][0]

##### Abbildung 2: Zwei Rollen mit jeweils zwei Instanzen (virtuelle Computer), die in einem Azure-Rechenzentrum ausgeführt werden

![image][1]

Rolleninstanzen verarbeiten in der Regel Internetclientanforderungen, die im
Rechenzentrum durch einen sogenannten eine *Eingabeendpunkt* eingehen. Eine einzelne Rolle
kann 0 oder mehr Eingabeendpunkte haben. Jeder Endpunkt gibt ein Protokoll
(HTTP, HTTPS oder TCP) und einen Port an. Es ist üblich, eine Rolle mit zwei
Eingabeendpunkten zu konfigurieren: HTTP-Überwachung auf Port 80 und HTTPS-Überwachung
auf Port 443. Die folgende Abbildung zeigt ein Beispiel für zwei unterschiedliche Rollen
mit unterschiedlichen Eingabeendpunkten, an die Clientanforderungen geleitet werden.

![image][2]

Beim Erstellen eines gehosteten Diensts in Azure wird ihm eine
öffentlich adressierbare IP-Adresse zugewiesen, die Clients verwenden können, um darauf zuzugreifen. Beim
Erstellen des gehosteten Diensts müssen Sie außerdem ein URL-Präfix auswählen, das
dieser IP-Adresse zugeordnet ist. Dieses Präfix muss eindeutig sein, da Sie
im Prinzip die *Präfix*.cloudapp.net-URL reservieren, damit sie von niemand
anderem verwendet werden kann. Clients kommunizieren mit Ihren Rolleninstanzen mithilfe der
URL. In der Regel verteilen oder veröffentlichen Sie die Azure-
*Präfix*.cloudapp.net-URL nicht. Stattdessen kaufen Sie einen DNS-Namen von.
der DNS-Registrierung Ihrer Wahl und konfigurieren Ihren DNS-Namen zum Umleiten von
Clientanforderungen an die Azure-URL. Weitere Informationen finden Sie unter
[Konfigurieren eines benutzerdefinierten Domänennamens in Azure][].

## <a id="considerations"> </a>Entwurfsüberlegungen zu gehosteten Diensten

Beim Entwerfen einer Anwendung für die Ausführung in einer Cloudumgebung müssen Sie
verschiedene Aspekte bedenken, etwa Latenz,
hohe Verfügbarkeit und Skalierbarkeit.

Die Entscheidung, wo sich Ihr Anwendungscode befinden soll, ist ein wichtiger
Aspekt beim Ausführen eines gehosteten Diensts in Azure. Normalerweise
wird die Anwendung in Rechenzentren bereitgestellt, die sich möglichst Nahe
bei Ihren Kunden befinden, um Latenz zu reduzieren und die optimale Leistung zu erreichen.
Sie können jedoch ein Rechenzentrum näher an Ihrem Unternehmen oder näher
an Ihren Daten auswählen, wenn Sie rechtliche Bedenken bezüglich
Ihrer Daten und den Aufbewahrungsort haben. Es gibt weltweit sechs Rechenzentren, die
den Anwendungscode hosten können. Die folgende Tabelle zeigt
die verfügbaren Standorte:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**Land/Region**

</td>
<td style="width: 200px;">
**Unterregionen**

</td>
</tr>
<tr>
<td>
USA

</td>
<td>
Südlich zentral und nördlich zentral

</td>
</tr>
<tr>
<td>
Europa

</td>
<td>
Nördlich und westlich

</td>
</tr>
<tr>
<td>
Asien

</td>
<td>
Südöstlich und östlich

</td>
</tr>
</tbody>
</table>
Wenn Sie einen gehosteten Dienst erstellen, wählen Sie eine Unterregion aus, die
den Standort angibt, an dem Ihr Code ausgeführt werden soll.

Um hohe Verfügbarkeit und Skalierbarkeit zu erreichen, ist es von entscheidender Bedeutung,
dass die Daten der Anwendung in einem zentralen Repository so abgelegt werden,
dass mehrere Rolleninstanzen darauf zugreifen können. Um diese Aufgabe zu erleichtern, bietet Azure
verschiedene Speicheroptionen, wie z. B. BLOBs, Tabellen und SQL-Datenbanken. Informationen finden Sie im Artikel
[Datenspeicherlösungen in Azure][].
Dort erhalten Sie detaillierte Informationen zu diesen Speichertechnologien. Die folgende Abbildung zeigt, wie
der Lastenausgleich im Azure-Rechenzentrum Clientanforderungen
an unterschiedliche Rolleninstanzen verteilt, die alle auf den
gleichen Datenspeicher zugreifen können.

![image][3]

In der Regel sollte der Anwendungscode und die Daten im
gleichen Rechenzentrum abgelegt werden, da dies eine niedrige Latenz (bessere Leistung) ermöglicht,
wenn der Anwendungscode auf die Daten zugreift. Darüber hinaus wird Ihnen keine
Bandbreite in Rechnung gestellt, wenn Daten innerhalb des gleichen Rechenzentrums
verschoben werden.

## <a id="scale"> </a>Entwerfen Ihrer Anwendung im Hinblick auf Skalierbarkeit

In einigen Fällen sollten Sie eine einzelne Anwendung (wie eine einfache Website)
in Azure hosten. Aber häufig kann Ihre
Anwendung aus verschiedenen Rollen bestehen, die zusammenarbeiten. In der Abbildung
unten gibt es beispielsweise zwei Instanzen der Websiterolle,
drei Instanzen der Auftragsbearbeitungsrolle und eine Instanz der
Berichts-Generator-Rolle. Diese Rollen arbeiten zusammen und der
Code für alle kann zusammengefasst und als ein einzelne
Einheit in Azure bereitgestellt werden.

![image][4]

Der Hauptgrund für das Aufteilen einer Anwendung in verschiedene Rollen, die
jeweils auf einem eigenen Satz von Rolleninstanzen (also virtuelle Computer) ausgeführt werden, ist das unabhängige Skalieren der
Rollen. Während der Urlaubszeit kaufen zum Beispiel viele
Kunden Produkte von Ihrem Unternehmen, sodass Sie möglicherweise
die Anzahl der Rolleninstanzen, auf denen die Websiterolle ausgeführt wird, und
die Anzahl der Rolleninstanzen, auf denen Auftragsbearbeitungsrolle ausgeführt wird,
erhöhen sollten. Nachdem die Urlaubszeit erhalten Sie möglicherweise viele Produkte zurück,
sodass Sie weiterhin viele Websiteinstanzen, aber weniger Auftragsbearbeitungsinstanzen
benötigen. Während des restlichen Jahres benötigen Sie möglicherweise nur wenige
Website- und Auftragsbearbeitungsinstanzen. In der gesamten Zeit benötigen Sie
möglicherweise nur eine Berichts-Generator-Instanz. Die Flexibilität von
rollenbasierten Bereitstellungen in Azure ermöglicht Ihnen das problemlose Anpassen Ihrer
Anwendung auf die Geschäftsanforderungen.

Es ist üblich, dass die Rolleninstanzen in Ihrem gehosteten Dienst
miteinander kommunizieren. Beispielsweise akzeptiert die Websiterolle eine
Kundenbestellung, übergibt dann die Auftragsbearbeitung aber an die
Auftragsbearbeitungs-Rolleninstanzen. Die beste Methode zum Übergeben von Arbeit von einen Satz von
Rolleninstanzen an einen anderen Satz von Instanzen ist das Verwenden der Warteschlangen-
technologie von Azure, entweder den Warteschlangendienst oder
Servicebus-Warteschlangen. Die Verwendung einer Warteschlange ist in diesem Zusammenhang
entscheidend. Die Warteschlange ermöglicht dem gehosteten Dienst das unabhängige Skalieren seiner Rollen,
sodass Sie zwischen Arbeitslast und Kosten ein Gleichgewicht herstellen können. Wenn die
Anzahl der Nachrichten in der Warteschlange mit der Zeit steigt, können Sie
die Anzahl der Auftragsbearbeitungs-Rolleninstanzen aufwärts skalieren. Wenn die Anzahl der
Nachrichten in der Warteschlange mit der Zeit abnimmt, dann können Sie
die Anzahl der Auftragsbearbeitungs-Rolleninstanzen abwärts skalieren. Auf diese Weise zahlen Sie nur
für die Instanzen, die für die tatsächliche Arbeitsauslastung erforderlich sind.

Außerdem sorgt die Warteschlange für Zuverlässigkeit. Wenn Sie die Anzahl der
Auftragsbearbeitungs-Rolleninstanzen abwärts skalieren, entscheidet Azure, welche Instanzen
beendet werden. Möglicherweise wird so eine Instanz beendet, die gerade
eine Warteschlangennachricht verarbeitet. Da die Nachrichtenverarbeitung aber
nicht erfolgreich abgeschlossen wird, geht die Nachricht zu einer
anderen Auftragsbearbeitungs-Rolleninstanz über, die sie übernimmt und
verarbeitet. Aufgrund der Sichtbarkeit von Warteschlangennachrichten werden Nachrichten
garantiert verarbeitet. Die Warteschlange fungiert außerdem als
Lastenausgleich durch das effektive Verteilen der Nachrichten an alle
Rolleninstanzen, die Nachrichten anfordern.

Für die Website-Rolleninstanzen können Sie den eingehenden Datenverkehr überwachen
und beschließen, die Anzahl zu erhöhen oder zu reduzieren. Die
Warteschlange ermöglicht Ihnen das Skalieren der Anzahl der Rolleninstanzen für die Website - 
unabhängig von der Auftragsbearbeitungs-Rolleninstanzen. Diese Funktion ist sehr
leistungsfähig und bietet Ihnen ein hohes Maß an Flexibilität. Wenn Ihre
Anwendung aus zusätzliche Rollen besteht, können Sie natürlich auch zusätzliche
Warteschlangen zur Kommunikation zwischen ihnen hinzufügen, um die
gleichen Skalierungs- und Kostenvorteile zu nutzen.

## <a id="defandcfg"> </a>Definition und Konfiguration von gehosteten Diensten

Für das Bereitstellen eines gehosteten Diensts in Azure benötigen Sie
eine Dienstdefinitionsdatei und eine Dienstkonfigurationsdatei. Diese beiden
Dateien sind XML-Dateien und ermöglichen Ihnen das deklarative Angeben
von Bereitstellungsoptionen für den gehosteten Dienst. Die Dienstdefinitionsdatei
Beschreibt alle Rollen, aus denen der gehostete Dienst besteht, und wie sie
miteinander kommunizieren. Die Dienstkonfigurationsdatei beschreibt die Anzahl der
Instanzen für jede Rolle und die Einstellungen zum Konfigurieren der einzelnen
Rolleninstanzen.

## <a id="def"> </a>Die Dienstdefinitionsdatei

Wie bereit erwähnt, ist die Dienstdefinitionsdatei (CSDEF) eine XML-
Datei, die die verschiedenen Rollen beschreibt, aus denen die gesamte
Anwendung besteht. Das vollständige Schema für die XML-Datei ist unter folgendem Link zu finden:
[http://msdn.microsoft.com/library/windowsazure/ee758711.aspx][].
Die CSDEF-Datei enthält ein WebRole- oder WorkerRole-Element für jede Rolle,
die Sie in der Anwendung verwenden möchten. Das Bereitstellen einer Rolle als Webrolle (mit
dem WebRole-Element) bedeutet, dass der Code auf einer Rolleninstanz ausgeführt wird,
die Windows Server 2008 und Internet Information Server (IIS) enthält.
Das Bereitstellen einer Rolle als Workerrolle (mit dem WorkerRole-Element)
bedeutet, dass die Rolleninstanz Windows Server 2008 verwendet (IIS wird nicht
installiert sein).

Sie können sicherlich eine Worker-Rolle erstellen und bereitstellen, die einen anderen
Mechanismus zum Überwachen eingehender Webanforderungen verwendet (Der Code könnte z. B.
einen .NET-HttpListener erstellen und verwenden). Da die Rolleninstanzen alle
unter Windows Server 2008 ausgeführt werden, kann Ihr Code alle Vorgänge ausführen,
die normalerweise einer Anwendung unter Windows Server
2008.

zur Verfügung stehen. Für jede Rolle geben Sie die gewünschte Größe des virtuellen Computers an, die Instanzen dieser
Rolle verwenden sollen. Die folgende Tabelle zeigt die verschiedenen
heute verfügbaren Größen von virtuellen Computern und die jeweiligen Attribute:

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**Größe des virtuellen Computers**

</td>
<td>
**CPU**

</td>
<td>
**RAM**

</td>
<td>
**Datenträger**

</td>
<td>
**Spitzenzeiten  
Netzwerk-E/A**

</td>
</tr>
<tr align="left" valign="top">
<td>
**Sehr klein**

</td>
<td>
1 x 1,0 GHz

</td>
<td>
768 MB

</td>
<td>
20 GB

</td>
<td>
\~5 MBit/s

</td>
</tr>
<tr align="left" valign="top">
<td>
**Klein**

</td>
<td>
1 x 1,6 GHz

</td>
<td>
1,75 GB

</td>
<td>
225 GB

</td>
<td>
\~100 MBit/s

</td>
</tr>
<tr align="left" valign="top">
<td>
**Mittel**

</td>
<td>
2 x 1,6 GHz

</td>
<td>
3,5 GB

</td>
<td>
490 GB

</td>
<td>
\~200 MBit/s

</td>
</tr>
<tr align="left" valign="top">
<td>
**Groß**

</td>
<td>
4 x 1,6 GHz

</td>
<td>
7 GB

</td>
<td>
1 TB

</td>
<td>
\~400 MBit/s

</td>
</tr>
<tr align="left" valign="top">
<td>
**Sehr groß**

</td>
<td>
8 x 1,6 GHz

</td>
<td>
14 GB

</td>
<td>
2 TB

</td>
<td>
\~800 MBit/s

</td>
</tr>
</tbody>
</table>
Sie müssen stündlich für jeden virtuellen Computer bezahlen, den Sie als Rolleninstanz verwenden, und Sie müssen
auch für alle Daten bezahlen, die die Rolleninstanzen außerhalb eines Rechenzentrums
senden. In das Rechenzentrum eingehende Daten werden Ihnen nicht in Rechnung gestellt. Weitere
Informationen finden Sie in der [Azure-Preisübersicht][]. Im Allgemeinen ist es
ratsam, viele kleine Rolleninstanzen anstelle weniger großer
Instanzen verwenden, damit Ihre Anwendung weniger fehleranfällig ist. Im Prinzip gilt:
Je weniger Rolleninstanzen Sie haben, desto stärker beeinträchtigt ein Fehler in
einer davon die gesamte Anwendung. Außerdem müssen Sie, wie bereits erwähnt,
mindestens zwei Instanzen für jede Rolle bereitstellen, um die
Vereinbarung zum Servicelevel von 99,95 % zu erreichen, die Microsoft anbietet.

In der Dienstdefinitionsdatei (CSDEF) geben Sie außerdem viele
Attribute zu den einzelnen Rollen in der Anwendung an. Im folgenden werden einige der
nützlichen Elemente, die Ihnen zur Verfügung stehen, aufgeführt:

-   **Zertifikate**. Sie verwenden Zertifikate zum Verschlüsseln von Daten oder wenn
der Webdienst SSL unterstützt. Alle Zertifikate müssen zu Azure hochgeladen
 werden. Weitere Informationen finden Sie unter [Verwalten von Zertifikaten.
in Azure][]. Diese XML-Einstellung installiert zuvor hochgeladene
    Zertifikate in den Zertifikatspeicher der Rolleninstanz, sodass sie
    vom Anwendungscode verwendet werden können.

-   **Konfigurationseinstellungsnamen**. Für Werte, die Ihre
Anwendungen während der Ausführung auf einer Rolleninstanz lesen sollen. Der tatsächliche
    Wert der Konfigurationseinstellungen wird in der Dienstkonfigurationsdatei
    (CSCFG) festgelegt, die jederzeit und ohne erneute Bereitstellung
des Codes aktualisiert werden kann. Sie können sogar Ihre
    Anwendungen auf eine Weise codieren, dass die geänderten Konfigurationswerte
    ohne Ausfallzeiten erkannt werden.

-   **Eingabeendpunkte**. Hier geben Sie HTTP-, HTTPS- oder TCP-Endpunkte
    (mit Ports) an, die nach außen
über die *Präfix*.cloadapp.net-URL verfügbar gemacht werden sollen. Wenn Azure Ihre
    Rolle bereitstellt, wird die Firewall auf der Rolleninstanz automatisch
    konfiguriert.

-   **Interne Endpunkte**. Hier geben Sie HTTP- oder TCP-Endpunkte an,
    die anderen Rolleninstanzen verfügbar gemachten sollen, die als
Teil der Anwendung bereitgestellt werden. Interne Endpunkte ermöglichen allen Rolleninstanzen
    innerhalb der Anwendung die Kommunikation untereinander, aber der Zugriff
    auf Rolleninstanzen, die sich außerhalb der Anwendung befinden, ist nicht möglich.

-   **Importmodule**. Diese installieren optional nützliche Komponenten auf
den Rolleninstanzen. Es gibt Komponenten für Diagnoseüberwachung,
    Remotedesktop und Azure Connect (dies ermöglicht
    Ihrer Rolleninstanz den Zugriff auf lokale Ressourcen über einen sicheren Kanal).

-   **Lokaler Speicher**. Dies weist ein Unterverzeichnis in der Rolleninstanz
zur Verwendung durch Ihre Anwendung zu. Weitere Informationen dazu finden Sie
    in den [Datenspeicherlösungen im Azure-][]Artikel.

-   **Startaufgaben**. Startaufgaben ermöglichen Ihnen die Installation von
erforderlichen Komponenten für eine Rolleninstanz, wenn sie gestartet wird. Die Aufgaben
    können bei Bedarf mit erhöhten Rechten als Administrator ausgeführt werden.

## <a id="cfg"> </a>Die Dienstkonfigurationsdatei

Die Dienstkonfigurationsdatei (CSCFG) ist eine XML-Datei, die Einstellungen
beschreibt, die geändert werden können, ohne die Anwendung erneut bereitzustellen. Das
vollständige Schema für die XML-Datei ist unter folgendem Link zu finden:
[http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][].
Die CSCFG-Datei enthält ein Rollenelement für jede Rolle in Ihrer
Anwendung Einige der Elemente, die Sie in der CSCFG-Datei angeben können,
werden im Folgenden beschrieben:

-   **Betriebssystemversion**. Dieses Attribut ermöglicht Ihnen die Auswahl der Betriebssystemversion,
    die für alle Rolleninstanzen verwendet werden soll, die
Ihren Anwendungscode ausführen. Dieses Betriebssystem wird als *Gastbetriebssystem* bezeichnet, und jede
    neue Version enthält die neuesten Sicherheitspatches und -updates,
die zum Zeitpunkt den Veröffentlichung des Gastbetriebssystems verfügbar sind. Wenn Sie den
    OsVersion-Attributwert auf "\*" festlegen, aktualisiert Azure automatisch
    das Gastbetriebssystem auf jedem der Rolleninstanzen, wenn neue
Gastbetriebssystemversionen verfügbar werden. Allerdings können Sie automatische Updates
deaktivieren, indem Sie eine bestimmte Gastbetriebssystemversion auswählen. Beispiel: 
    Das Festlegen des OsVersion-Attributs auf den Wert
    "WA-GUEST-OS-2.8\_201109-01" bewirkt, dass alle Rolleninstanzen das abrufen,
    was auf dieser Webseite beschrieben wird:
    [http://msdn.microsoft.com/library/hh560567.aspx][]. Weitere
Informationen zum Gastbetriebssystemversionen finden Sie unter [Verwalten von Aktualisierungen des
Azure-Gastbetriebssystems].

-   **Instanzen**. Der Wert dieses Elements gibt die Anzahl der Rolleninstanzen
    an, die für die Ausführung des Codes für eine bestimmte Rolle bereitgestellt werden
sollen. Da Sie eine neue CSCFG-Datei zu Azure hochladen können
    (ohne die Anwendung erneut bereitzustellen), ist es problemlos möglich,
    den Wert für dieses Element zu ändern und eine neue CSCFG-Datei hochzuladen, um
    die Anzahl der Rolleninstanzen, die Ihren Anwendungscode ausführen,
dynamisch zu erhöhen oder verringern. Dies ermöglicht Ihnen die einfache Aufwärts- und Abwärtsskalierung Ihrer
    Anwendung, um den tatsächlichen Arbeitsauslastungsanforderungen zu entsprechen, während auch
    gesteuert wird, wie viel Ihnen für die Ausführung der Rolleninstanzen in Rechnung gestellt wird.

-   **Konfigurationseinstellungswerte**. Dieses Element gibt Werte für
Einstellungen an (wie sie in der CSDEF-Datei definiert werden). Die Rolle kann diese
Werte während der Ausführung lesen. Diese Konfigurationseinstellungswerte werden
    normalerweise für Verbindungszeichenfolgen zur SQL-Datenbank oder zum 
    Azure-Speicher verwendet, aber sie können für einen beliebigen Zweck verwendet werden.

## <a id="hostedservices"> </a>Erstellen und Bereitstellen eines gehosteten Diensts

Das Erstellen eines gehosteten Diensts erfordert, dass Sie  zunächst im der [Azure-Verwaltungsportal] einen gehosteten Dienst bereitstellen, indem Sie
ein DNS-Präfix und das Rechenzentrum angeben, in dem Ihr Code im Endeffekt
ausgeführt werden soll. Erstellen Sie dann in Ihrer Entwicklungsumgebung die
Dienstdefinitionsdatei (CSDEF), erstellen Sie den Anwendungscode und verpacken (Zip)
Sie alle diese Dateien in eine Dienstpaketdatei (CSPKG). Sie müssen auch
die Dienstkonfigurationsdatei (CSCFG) vorbereiten. Zum Bereitstellen Ihrer Rolle
laden Sie die CSPKG- und CSCFG-Dateien mit der Azure-
Dienstverwaltungs-API hoch. Nach der Bereitstellung stellt Azure Rolleninstanzen
im Rechenzentrum bereit (je nach den Konfigurationsdaten),
extrahiert den Anwendungscode aus dem Paket extrahiert, kopiert ihn in die Rolleninstanzen
und startet die Instanzen. Jetzt wird Ihr Code ausgeführt.

Die Abbildung unten zeigt die CSPKG-Datei und die CSCFG-Datei, die Sie auf Ihrem
Entwicklungscomputer erstellen. Die CSPKG-Datei enthält die CSDEF-Datei und den
Code für zwei Rollen. Nach dem Hochladen der CSPKG- und CSCFG-Dateien mit der
Azure-Dienstverwaltungs-API erstellt Azure die Rolleninstanzen
im Rechenzentrum. In diesem Beispiel hat die CSCFG-Datei angegeben,
dass Azure drei Instanzen der Rolle \#1 und zwei
Instanzen der Rolle \#2 erstellen soll.

![image][5]

Weitere Informationen zum Bereitstellen, Aktualisieren und Neukonfigurieren der
Rollen finden Sie im Artikel [Bereitstellen und Aktualisieren von Azure-Anwendungen][]
.<a id="Ref" name="Ref"></a>

## <a id="references"> </a>Referenzen

-   [Erstellen eines gehosteten Diensts für Azure][]

-   [Verwalten von gehosteten Diensten in Azure][]

-   [Migrieren von Anwendungen zu Azure][]

-   [Konfigurieren einer Azure-Anwendung][]

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
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configuring a Custom Domain Name in Azure]: http://www.windowsazure.com/de-de/develop/net/common-tasks/custom-dns/
  [Data Storage Offerings in Azure]: http://www.windowsazure.com/de-de/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Azure-Preise]: http://www.windowsazure.com/de-de/pricing/calculator/
  [Verwalten von Zertifikaten in Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Verwalten von Aktualisierungen des Azure-Gastbetriebssystems]: http://msdn.microsoft.com/library/ee924680.aspx
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Bereitstellen und Aktualisieren von Azure-Anwendungen]: http://www.windowsazure.com/de-de/develop/net/fundamentals/deploying-applications/
  [Erstellen eines gehosteten Diensts für Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Verwalten von gehosteten Diensten in Azure]: http://msdn.microsoft.com/library/gg433038.aspx
  [Migrieren von Anwendungen zu Azure]: http://msdn.microsoft.com/library/gg186051.aspx
  [Konfigurieren einer Azure-Anwendung]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
