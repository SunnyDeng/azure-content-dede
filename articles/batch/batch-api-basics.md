<properties
	pageTitle="Übersicht über Azure Batch-Features | Microsoft Azure"
	description="Lernen Sie die Features des Batch-Diensts und seiner APIs aus der Sicht eines Entwicklers kennen."
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="01/21/2016"
	ms.author="yidingz;v-marsma"/>

# Übersicht über Azure Batch-Features

Dieser Artikel bietet eine grundlegende Übersicht über die wichtigsten API-Features des Azure Batch-Diensts. Unabhängig davon, ob Sie eine verteilte rechnerische Lösung mit der [Batch REST][batch_rest_api]- oder der [Batch .NET][batch_net_api]-API entwickeln, werden Sie viele der unten erläuterten Entitäten und Features verwenden.

> [AZURE.TIP] Eine allgemeinere technische Übersicht über Batch finden Sie unter [Azure Batch – Grundlagen](batch-technical-overview.md).

## <a name="workflow"></a>Workflow des Batch-Diensts

Der folgende allgemeine Workflow wird typischerweise in nahezu allen verteilten rechnerischen Szenarien angewendet, die innerhalb des Batch-Diensts entwickelt werden:

1. Laden Sie die *Datendateien*, die Sie in Ihrem verteilten Rechenszenario verwenden möchten, in ein [Azure Storage][azure_storage]-Konto hoch. Diese Dateien müssen sich im Storage-Konto befinden, damit der Batch-Dienst darauf zugreifen kann. Diese Dateien werden über Tasks in [Computeknoten](#computenode) herunterladen, wenn die Tasks ausgeführt werden.

2. Laden Sie die abhängigen *Binärdateien* in das Storage-Konto hoch. Diese Binärdateien enthalten das Programm, das durch die Tasks und die davon abhängigen Assemblys ausgeführt wird. Auf diese Dateien muss auch von Ihrem Storage-Konto aus zugegriffen werden, damit sie von den Tasks auf die Computeknoten heruntergeladen werden können.

3. Erstellen Sie einen [Pool](#pool) aus Computeknoten. Sie geben die [Größe der Computeknoten][cloud_service_sizes] an, die beim Erstellen des Pools verwendet werden soll. Wenn ein Task ausgeführt wird, wird ihm ein Knoten in diesem Pool zugewiesen.

4. Erstellen Sie einen [Auftrag](#job). Ein Auftrag ermöglicht Ihnen die Verwaltung von Sammlungstasks.

5. Fügen Sie dem Auftrag [Tasks](#task) hinzu. Jeder Task verwendet das von Ihnen hochgeladene Programm, um Informationen in den Datendateien zu verarbeiten, die Sie ebenfalls in Ihr Storage-Konto hochgeladen haben.

6. Überwachen Sie den Auftragsfortschritt, und rufen Sie die Ergebnisse ab.

> [AZURE.NOTE] Sie benötigen ein [Batch-Konto](batch-account-create-portal.md) zum Verwenden des Batch-Diensts, und fast alle Lösungen verwenden ein [Azure Storage][azure_storage]-Konto zum Speichern und Abrufen von Dateien.

In den folgenden Abschnitten erfahren Sie mehr zu den einzelnen Ressourcen, die im obigen Workflow genannt werden, sowie zu vielen weiteren Features von Batch, die Ihr verteiltes Rechenszenario unterstützen.

## <a name="resource"></a> Ressourcen des Batch-Diensts

Wenn Sie den Azure Batch-Dienst verwenden, nutzen Sie die folgenden Ressourcen:

- [Konto](#account)

- [Computeknoten](#computenode)

- [Pool](#pool)

- [Job](#job)

- [Aufgabe](#task)

	- [Startaufgabe](#starttask)

	- [Auftrags-Manager-Aufgabe](#jobmanagertask)

	- [Tasks zur Auftragsvorbereitung und -freigabe](#jobpreprelease)

	- [Tasks mit mehreren Instanzen](#multiinstance)

- [Auftragszeitplan](#jobschedule)

### <a name="account"></a>Konto

Ein Batch-Konto ist eine eindeutig identifizierte Entität innerhalb des Batch-Diensts. Die gesamte Verarbeitung ist einem Batch-Konto zugeordnet. Beim Ausführen von Vorgängen mit dem Batch-Dienst benötigen Sie sowohl den Kontonamen als auch den Kontoschlüssel. Weitere Informationen zum Erstellen eines Batch-Kontos finden Sie unter [Erstellen und Verwalten eines Azure Batch-Kontos im Azure-Portal](batch-account-create-portal.md).

### <a name="computenode"></a>Computeknoten

Ein Computeknoten ist ein virtueller Azure-Computer, der für eine bestimmte Workload für Ihre Anwendung vorgesehen ist. Die Größe eines Knotens bestimmt die Anzahl der CPU-Kerne, die Speicherkapazität und die lokale Dateisystemgröße, die dem Knoten zugeordnet werden. Ein Knoten kann einer beliebigen [Clouddienst-Knotengröße][cloud_service_sizes] entsprechen, mit Ausnahme von A0.

Knoten können ausführbare Dateien und Skripts ausführen, z. B. ausführbare Dateien (EXE), Befehlsdateien (CMD), Batchdateien (BAT) und PowerShell-Skripts. Ein Knoten verfügt außerdem über die folgenden Attribute:

- Eine standardmäßige **Ordnerstruktur** und zugehörige **Umgebungsvariablen** mit ausführlichen Pfadinformationen werden auf jedem Computeknoten erstellt. Weitere Informationen finden Sie unter [Dateien und Verzeichnisse](#files).
- **Umgebungsvariablen**, die zur Referenzierung durch Tasks zur Verfügung stehen.
- Für die Zugriffssteuerung konfigurierte **Firewalleinstellungen**.
- Wenn (beispielsweise zum Debuggen) ein **Remotezugriff** auf einen Computeknoten erforderlich ist, kann eine RDP-Datei abgerufen werden, um per *Remotedesktop* auf den Knoten zuzugreifen.

### <a name="pool"></a>Pool

Ein Pool besteht aus einer Sammlung von Knoten, auf denen Ihre Anwendung ausgeführt wird. Der Pool kann manuell von Ihnen oder automatisch vom Batch-Dienst erstellt werden, wenn Sie die zu erbringende Arbeitsleistung angeben. Sie können einen Pool erstellen und verwalten, der den Anforderungen Ihrer Anwendung entspricht. Pools können nur von dem Batch-Konto verwendet werden, in dem sie erstellt wurden. Ein Batch-Konto kann über mehrere Pools verfügen.

Azure Batch-Pools basieren auf der grundlegenden Azure-Computeplattform und bieten Funktionen für umfangreiche Zuordnungen, für die Anwendungsinstallation, die Datenverteilung und die Systemüberwachung sowie für die flexible Anpassung der Computeknotenanzahl innerhalb eines Pools (Skalierung).

Jedem Knoten, der einem Pool hinzugefügt wird, werden ein eindeutiger Name und eine IP-Adresse zugewiesen. Beim Entfernen eines Knotens aus einem Pool gehen alle Änderungen am Betriebssystem oder an den Dateien verloren, und sein Name und die IP-Adresse werden zur zukünftigen Verwendung freigegeben. Wenn ein Knoten einen Pool verlässt, endet seine Lebensdauer.

Sie können einen Pool derart konfigurieren, dass die Kommunikation zwischen den darin enthaltenen Knoten zugelassen wird. Wenn die Kommunikation innerhalb eines Pools für einen Pool angefordert wird, aktiviert der Batch-Dienst für jeden Knoten im Pool Ports, die über 1100 liegen. Jeder Knoten im Pool ist so konfiguriert, dass eingehende Verbindungen nur für diesen Portbereich und ausschließlich von anderen Knoten im Pool zugelassen werden. Wenn für Ihre Anwendung keine Kommunikation zwischen Knoten erforderlich ist, kann der Batch-Dienst potenziell eine große Anzahl von Knoten in vielen verschiedenen Clustern oder Datencentern für den Pool reservieren, um die Möglichkeit zur parallelen Verarbeitung zu erhöhen.

Wenn Sie einen Pool erstellen, können Sie die folgenden Attribute angeben:

- **Größe der Knoten** im Pool
	- Bei der Wahl der angemessenen Knotengröße müssen die Eigenschaften und Anforderungen der Anwendungen berücksichtigt werden, die auf dem Knoten ausgeführt werden sollen. Die Knotengröße wird normalerweise unter der Annahme ausgewählt, dass jederzeit immer nur ein Task auf dem Knoten ausgeführt wird. Aspekte wie z. B., ob es sich um eine Multithreadanwendung handelt und wie viel Arbeitsspeicher sie beansprucht, helfen Ihnen, die am besten geeignete und kostengünstigste Knotengröße zu bestimmen. Es ist möglich, mehrere Tasks zuzuweisen und mehrere Anwendungsinstanzen parallel auszuführen. In diesem Fall wird üblicherweise ein größerer Knoten ausgewählt. Weitere Informationen finden Sie weiter unten im Abschnitt „Richtlinie zur Taskplanung“.
	- Alle Knoten in einem Pool müssen dieselbe Größe aufweisen. Wenn verschiedene Anwendungen mit unterschiedlichen Systemanforderungen und/oder unterschiedlichen Auslastungsstufen ausgeführt werden, erstellen Sie separate Pools.
	- Für einen Pool können alle [Clouddienst-Knotengrößen][cloud_service_sizes] konfiguriert werden, mit Ausnahme von A0.

- Die **Betriebssystemfamilie** und **-version**, die auf den Knoten ausgeführt wird
	- Wie bei Workerrollen innerhalb von Clouddiensten können die *Betriebssystemfamilie* und die *Betriebssystemversion* angegeben werden. (Informationen zu Workerrollen finden Sie unter *Computehostingoptionen in Azure* im Abschnitt [Informationen zu Cloud Services][about_cloud_services].)
	- Die Betriebssystemfamilie bestimmt auch, welche Versionen von .NET mit dem Betriebssystem installiert sind.
	- Genau wie bei Workerrollen empfiehlt sich auch bei der Betriebssystemversion die Verwendung von `*`, damit die Knoten automatisch per Upgrade aktualisiert werden und für neue Versionen kein Zusatzaufwand entsteht. Mit der Wahl einer bestimmten Betriebssystemversion wird in erster Linie die Anwendungskompatibilität sichergestellt. Hierzu wird die Überprüfung der Abwärtskompatibilität vor der Versionsaktualisierung ermöglicht. Nach der Überprüfung kann die Betriebssystemversion für den Pool aktualisiert und das neue Betriebssystemimage installiert werden. Dabei werden alle ausgeführten Tasks unterbrochen und wieder der Warteschlange hinzugefügt.

- Die **vorgegebene Anzahl von Knoten**, die für den Pool verfügbar sein soll

- Die **Skalierungsrichtlinie** für den Pool
	- Neben der Anzahl von Knoten können Sie für einen Pool auch eine [Formel für die automatische Skalierung](batch-automatic-scaling.md) angeben. Der Batch-Dienst führt die Formel aus und passt die Anzahl von Knoten innerhalb des Pools basierend auf verschiedenen Parametern an, die Sie für Pools, Aufträge und Tasks angeben können.

- Richtlinie zur **Taskplanung**
	- Die Konfiguration der [maximalen Tasks pro Knoten](batch-parallel-node-tasks.md) bestimmt die maximale Anzahl von Tasks, die in den einzelnen Knoten im Pool parallel ausgeführt werden können.
	- In der Standardkonfiguration wird auf einem Computeknoten immer nur ein einziger Task ausgeführt. Es sind aber auch Szenarien denkbar, in denen die gleichzeitige Ausführung mehrerer Tasks auf einem Knoten von Vorteil ist. Ein Beispiel ist die Erhöhung der Knotenauslastung, wenn eine Anwendung auf E/A-Vorgänge warten muss. Durch die gleichzeitige Ausführung mehrerer Anwendungen wird die CPU-Auslastung erhöht. Ein weiteres Beispiel ist die Verringerung der Knotenanzahl im Pool. Dadurch kann die Menge der für große Verweisdatasets erforderlichen Datenübertragungen reduziert werden. Wenn die Knotengröße A1 für eine Anwendung ausreichend ist, könnte stattdessen die Knotengröße A4 ausgewählt und der Pool für acht parallel ausgeführte Tasks mit jeweils einem Kern konfiguriert werden.
	- Ein „Füllungstyp“ kann ebenfalls angegeben werden. Dieser legt fest, ob Batch die Tasks gleichmäßig über alle Knoten verteilt oder jeden Knoten mit der maximalen Anzahl von Tasks auffüllt, bevor Tasks einem weiteren Knoten im Pool zugewiesen werden.

- Der **Kommunikationsstatus** der Knoten im Pool
	- Ein Pool kann so konfiguriert werden, dass die Kommunikation zwischen den Knoten im Pool zulässig ist. Hierdurch wird die zugrunde liegende Netzwerkinfrastruktur bestimmt. Beachten Sie, dass sich diese Einstellung auch die Platzierung der einzelnen Knoten im Cluster auswirkt.
	- In den meisten Szenarien werden Tasks unabhängig voneinander ausgeführt und müssen nicht miteinander kommunizieren. Es sind jedoch auch einige Anwendungen denkbar, in denen Tasks kommunizieren müssen.

- Der **Starttask** für Knoten im Pool
	- Ein *Starttask* kann angegeben werden. Dieser wird jedes Mal ausgeführt, wenn dem Pool ein Computeknoten hinzugefügt und wenn ein Knoten neu gestartet wird. Diese Option wird häufig zum Installieren einer Anwendung verwendet, die von den im Knoten ausgeführten Tasks verwendet werden soll.

### <a name="job"></a>Auftrag

Ein Auftrag ist eine Sammlung von Tasks, die festlegt, wie die Berechnung auf Computeknoten in einem Pool ausgeführt wird.

- Der Auftrag gibt den **Pool** an, in dem der Task ausgeführt wird. Der Pool kann ein vorhandener, bereits erstellter Pool sein, der von vielen Aufträgen verwendet wird. Der Pool kann auch bedarfsweise für jeden Auftrag, der einem Auftragszeitplan zugeordnet ist, oder für alle einem Auftragszeitplan zugeordneten Aufträge erstellt werden.
- Eine optionale **Auftragspriorität** kann angegeben werden. Wenn ein Auftrag übermittelt wird, der eine höhere Priorität besitzt als bereits in Bearbeitung befindliche Aufträge, werden die Tasks des Auftrags mit der höheren Priorität vor den Tasks der Aufträge mit niedrigerer Priorität in die Warteschlange eingefügt. Bereits ausgeführte Tasks mit niedrigerer Priorität werden nicht nach hinten verschoben.
- **Einschränkungen** für Aufträge geben bestimmte Grenzwerte für Ihre Aufträge an.
	- Für die Aufträge kann eine **maximale Gesamtbetrachtungszeit** festgelegt werden. Wenn die Ausführungsdauer der Aufträge die angegebene maximale Wanduhrzeit übersteigt, werden der Auftrag und alle dazugehörigen Aufgaben beendet.
	- Azure Batch kann nicht erfolgreich ausgeführte Aufgaben erkennen und wiederholen. Die **maximale Anzahl von Taskwiederholungen** kann als Einschränkung angegeben werden. Hierzu gehört auch, ob ein Task immer oder niemals wiederholt werden soll. Bei der Wiederholung eines Tasks wird dieser nochmals der Warteschlange hinzugefügt und erneut ausgeführt.
- Tasks können dem Auftrag von Ihrer Clientanwendung hinzugefügt werden, oder es kann ein [Auftrags-Manager-Task](#jobmanagertask) angegeben werden. Ein Auftrags-Manager-Task verwendet die Batch-API und enthält die Informationen, die zum Erstellen der erforderlichen Tasks für einen Auftrag benötigt werden, wobei der Task auf einem der Computeknoten innerhalb des Pools ausgeführt wird. Der Auftrags-Manager-Task wird von Batch speziell behandelt: Er wird sofort nach der Auftragserstellung der Warteschlange hinzugefügt und erneut gestartet, falls er nicht erfolgreich ausgeführt werden konnte. Ein Auftrags-Manager-Task wird für Aufträge benötigt, die von einem Auftragszeitplan erstellt werden, da sich Tasks nur so vor der Auftragsinstanziierung definieren lassen. Weitere Informationen zu Auftrags-Manager-Tasks werden unten aufgeführt.


### <a name="task"></a>Aufgabe

Ein Task ist eine Berechnungseinheit, die einem Auftrag zugeordnet ist und auf einem Knoten ausgeführt wird. Tasks werden einem Knoten zur Ausführung zugewiesen oder der Warteschlange hinzugefügt, bis ein Knoten verfügbar wird. Eine Aufgabe verwendet die folgenden Ressourcen:

- Die in der **Befehlszeile** des Tasks angegebene Anwendung.

- Die **Ressourcendateien**, die die zu verarbeitenden Daten enthalten. Diese Dateien werden automatisch aus dem Blobspeicher in einem Azure Storage-Konto auf den Knoten kopiert. Weitere Informationen finden Sie unten im Abschnitt [Dateien und Verzeichnisse](#files).

- Die von der Anwendung benötigten **Umgebungsvariablen**. Weitere Informationen finden Sie unten im Abschnitt [Umgebungseinstellungen für Tasks](#environment).

- Die **Einschränkungen**, unter denen die Berechnung erfolgen soll. Beispielsweise die maximale Zeit, in der der Task ausgeführt werden darf, die maximale Anzahl der Wiederholungen für einen Task, der nicht erfolgreich ausgeführt wurde, und die maximal zulässige Aufbewahrungsdauer für Dateien im Arbeitsverzeichnis.

Zusätzlich zu Tasks, die Sie zur Berechnung auf einem Knoten definieren können, werden vom Batch-Dienst die folgenden speziellen Tasks bereitgestellt:

- [Startaufgabe](#starttask)
- [Auftrags-Manager-Aufgabe](#jobmanagertask)
- [Tasks zur Auftragsvorbereitung und -freigabe](#jobmanagertask)
- [Tasks mit mehreren Instanzen](#multiinstance)

#### <a name="starttask"></a>Startaufgabe

Durch das Zuordnen eines **Starttasks** zu einem Pool können Sie die Betriebssystemumgebung der zugehörigen Knoten konfigurieren und Aktionen wie das Installieren von Software oder Hintergrundprozesse ausführen. Der Starttask wird jedes Mal ausgeführt, wenn ein Knoten gestartet wird. Dies gilt, solange sich der Knoten im Pool befindet, auch beim ersten Hinzufügen des Knotens zum Pool. Ein wesentlicher Vorteil von Starttasks besteht darin, dass sie alle Informationen enthalten, die zum Konfigurieren von Computeknoten und zum Installieren der für die Ausführung des Auftragstasks nötigen Anwendungen erforderlich sind. Daher kann zum Erhöhen der Anzahl von Knoten in einem Pool einfach die neue Zielknotenanzahl angegeben werden. Batch enthält bereits alle Informationen, die zum Konfigurieren der neuen Knoten und zum Vorbereiten der Knoten für die Annahme von Tasks erforderlich sind.

Wie bei jedem anderen Batch-Task kann zusätzlich zu einer auszuführenden **Befehlszeile** eine Liste mit **Ressourcendateien** in [Azure Storage][azure_storage] angegeben werden. Azure Batch kopiert zunächst die Dateien aus Azure Storage und führt dann die Befehlszeile aus. Bei einem poolbezogenen Starttask enthält die Dateiliste üblicherweise das Anwendungspaket oder die Anwendungsdateien. Sie kann aber auch Referenzdaten für alle Tasks enthalten, die auf den Computeknoten ausgeführt werden. Die Befehlszeile des Starttasks könnte ein PowerShell-Skript ausführen oder einen `robocopy`-Vorgang durchführen, um beispielsweise Anwendungsdateien in den Ordner „shared“ zu kopieren, und anschließend eine MSI-Datei oder `setup.exe` ausführen.

In der Regel ist es wünschenswert, dass der Batch-Dienst auf den Abschluss des Starttasks wartet und erst dann davon ausgeht, dass der Knoten nun für die Taskzuweisung bereit ist. Dieses Verhalten ist jedoch konfigurierbar.

Kann ein Starttask für einen Computeknoten nicht erfolgreich ausgeführt werden, wird der Status des Knotens entsprechend aktualisiert, und der Knoten steht für die Taskzuweisung nicht zur Verfügung. Ein Starttask wird nicht erfolgreich durchgeführt, wenn ein Problem beim Kopieren der Ressourcendateien aus dem Speicher auftritt oder wenn der Prozess, der von der Befehlszeile ausgeführt wird, einen Exitcode ungleich NULL zurückgibt.

#### <a name="jobmanagertask"></a>Auftrags-Manager-Aufgabe

Ein **Auftrags-Manager-Task** wird in der Regel zum Steuern und/oder Überwachen der Auftragsausführung verwendet. Dazu gehören beispielsweise das Erstellen und Übermitteln der Tasks für einen Auftrag, das Bestimmen zusätzlich auszuführender Tasks sowie das Festlegen, wann die Arbeit abgeschlossen ist. Ein Auftrags-Manager-Task ist jedoch nicht auf diese Aktivitäten beschränkt. Vielmehr handelt es sich um einen Task mit vollem Funktionsumfang, der alle für den Auftrag erforderlichen Aktionen durchführen kann. Z. B. kann ein Auftrags-Manager-Task eine Datei herunterladen, die als Parameter angegeben ist, den Inhalt der Datei analysieren und basierend auf diesem Inhalt zusätzliche Tasks übermitteln.

Ein Auftrags-Manager-Task wird vor allen anderen Tasks gestartet und bietet die folgenden Features:

- Er wird automatisch vom Batch-Dienst als Task übermittelt, wenn der Auftrag erstellt wird.

- Seine Ausführung wird so geplant, dass er vor den anderen Tasks in einem Auftrag ausgeführt wird.

- Der zugeordnete Knoten wird als letzter aus einem Pool entfernt, wenn der Pool verkleinert wird.

- Die Beendigung kann an die Beendigung aller Aufgaben im Auftrag gebunden sein.

- Dem Auftrags-Manager-Task wird die höchste Priorität zugewiesen, wenn er neu gestartet werden muss. Wenn ein Knoten im Leerlauf nicht verfügbar ist, kann der Batch-Dienst einen der anderen ausgeführten Tasks im Pool beenden, um Platz für die Ausführung des Auftrags-Manager-Tasks zu schaffen.

- Ein Auftrags-Manager-Task in einem Auftrag besitzt keine höhere Priorität als die Tasks in anderen Aufträgen. Auftragsübergreifend werden nur Prioritäten auf Auftragsebene beachtet.

#### <a name="jobpreprelease"></a>Tasks zur Auftragsvorbereitung und -freigabe

Batch stellt den Auftragsvorbereitungstask für das Setup vor der Auftragsausführung und den Auftragsfreigabetask für die Wartung oder Bereinigung nach dem Auftrag bereit.

- **Auftragsvorbereitungstask**: Der Auftragsvorbereitungstask wird vor der Ausführung der anderen Tasks eines Auftrags auf allen zum Ausführen von Tasks geplanten Computeknoten ausgeführt. Verwenden Sie Jobvorbereitungstasks beispielsweise zum Kopieren von Daten, die von allen Tasks gemeinsam genutzt werden, aber für den Auftrag einzigartig sind.
- **Jobfreigabetask**: Nach Abschluss des Auftrags wird der Auftragsfreigabetask auf jedem Knoten im Pool ausgeführt, der mindestens einen Task ausgeführt hat. Verwenden Sie den Auftragsfreigabetask z. B. zum Löschen von Daten, die vom Auftragsvorbereitungstask kopiert wurden, oder komprimieren Sie Diagnoseprotokolldaten, und laden Sie sie hoch.

Sowohl Auftragsvorbereitungs- als auch Auftragsfreigabeaufgaben bieten die Möglichkeit zur Angabe einer Befehlszeile, die beim Aufrufen der Aufgabe ausgeführt wird, sowie Funktionen wie Dateidownload, Ausführung mit erhöhten Rechten, benutzerdefinierte Umgebungsvariablen, maximale Ausführungsdauer, Wiederholungsanzahl und Dateiaufbewahrungsdauer.

Weitere Informationen zu Auftragsvorbereitungs- und -freigabetasks finden Sie unter [Ausführen von Auftragsvorbereitungs- und Auftragsabschlusstasks auf Azure Batch-Computeknoten](batch-job-prep-release.md).

#### <a name="multiinstance"></a>Tasks mit mehreren Instanzen

Ein [Task mit mehreren Instanzen](batch-mpi.md) ist ein Task, der für die gleichzeitige Ausführung auf mehreren Computeknoten konfiguriert ist. Tasks mit mehreren Instanzen ermöglichen Szenarien, die eine hohe Leistung benötigen, wie z. B. das Message Passing Interface (MPI), und erfordern, dass Computeknoten zu einer Gruppe gebündelt werden, um einen einzelnen Workload zu bearbeiteten.

Ausführliche Informationen zum Ausführen von MPI-Tasks in Batch mithilfe der Batch .NET-Bibliothek finden Sie unter [Use multi-instance tasks to run Message Passing Interface (MPI) applications in Azure Batch](batch-mpi.md) (Ausführen von Message Passing Interface-Anwendungen (MPI) in Azure Batch mithilfe von Tasks mit mehreren Instanzen).

### <a name="jobschedule"></a>Geplante Aufträge

Mit Auftragszeitplänen können Sie wiederkehrende Aufträge im Batch-Dienst erstellen. Ein Auftragszeitplan gibt an, wann Aufträge ausgeführt werden, und enthält die Spezifikationen für die auszuführenden Aufträge. Ein Auftragszeitplan ermöglicht die Angabe der Dauer des Zeitplans – für wie lange und wann der Zeitplan in Kraft tritt – und wie oft während dieses Zeitraums Aufträge erstellt werden sollen.

## <a name="files"></a>Dateien und Verzeichnisse

Jeder Task verfügt über ein Arbeitsverzeichnis, unter dem null oder mehr Dateien und Verzeichnisse für das Speichern des von einem Task ausgeführten Programms, der von einem Task verarbeiteten Daten und der Ausgabe der von einem Task ausgeführten Verarbeitung erstellt werden. Diese Dateien und Verzeichnisse sind dann während der Ausführung eines Auftrags für andere Tasks verfügbar. Alle Tasks, Dateien und Verzeichnisse auf einem Knoten gehören einem einzelnen Benutzerkonto.

Der Batch-Dienst stellt einen Teil des Dateisystems auf einem Knoten als Stammverzeichnis bereit. Das Stammverzeichnis ist für einen Task durch den Zugriff auf die `%AZ_BATCH_NODE_ROOT_DIR%`-Umgebungsvariable verfügbar. Weitere Informationen zur Verwendung von Umgebungsvariablen finden Sie unter [Umgebungseinstellungen für Tasks](#environment).

![Computeknoten-Verzeichnisstruktur][1]

Das Stammverzeichnis enthält die folgenden Verzeichnisstruktur:

- **Shared**: Dies ist ein freigegebenes Verzeichnis für alle Tasks, die auf einem Knoten unabhängig vom Auftrag ausgeführt werden. Auf dem Knoten erfolgt der Zugriff auf das freigegebene Verzeichnis über `%AZ_BATCH_NODE_SHARED_DIR%`. Dieses Verzeichnis bietet Lese-/Schreibzugriff auf alle Tasks, die auf dem Knoten ausgeführt werden. Tasks können Dateien in diesem Verzeichnis erstellen, lesen, aktualisieren und löschen.

- **Start**: Dieser Speicherort wird von einem Starttask als Arbeitsverzeichnis verwendet. Alle Dateien, die vom Batch-Dienst heruntergeladen werden, um die Startaufgabe zu starten, werden ebenfalls unter diesem Verzeichnis gespeichert. Auf dem Knoten steht das Startverzeichnis über die `%AZ_BATCH_NODE_START_DIR%`-Umgebungsvariable zur Verfügung. Der Starttask kann Dateien in diesem Verzeichnis erstellen, lesen, aktualisieren und löschen, und dieses Verzeichnis kann von Starttasks zum Konfigurieren des Betriebssystems verwendet werden.

- **Tasks**: Ein Verzeichnis wird für jeden Task erstellt, der auf dem Knoten ausgeführt wird. Der Zugriff erfolgt über `%AZ_BATCH_TASK_DIR%`. Innerhalb jedes Taskverzeichnisses erstellt der Batch-Dienst ein Arbeitsverzeichnis (`wd`), dessen eindeutiger Pfad durch die `%AZ_BATCH_TASK_WORKING_DIR%`-Umgebungsvariable angegeben wird. Dieses Verzeichnis bietet Lese-/Schreibzugriff auf den Task. Der Task kann Dateien in diesem Verzeichnis erstellen, lesen, aktualisieren und löschen, und dieses Verzeichnis bleibt auf Basis der für diesen Task angegebenen *RetentionTime*-Einschränkung erhalten.
  - `stdout.txt` und `stderr.txt`: Diese Dateien werden während der Ausführung des Tasks in den Taskordner geschrieben.

Wenn ein Knoten aus einem Pool entfernt wird, werden alle auf dem Knoten gespeicherten Dateien entfernt.

## <a name="lifetime"></a>Gültigkeitsdauer für Pool- und Computeknoten

Beim Entwerfen Ihrer Azure Batch-Lösung müssen Sie entscheiden, wie und wann Pools erstellt werden und wie lange Computeknoten innerhalb dieser Pools verfügbar bleiben.

Auf der einen Seite könnte beispielsweise für jeden Auftrag ein Pool erstellt werden, wenn der Auftrag übermittelt wird, und die zugehörigen Knoten könnten gleich nach Abschluss der Tasks wieder entfernt werden. Dadurch maximiert sich die Auslastung: Die Knoten werden nur zugewiesen, wenn dies unbedingt notwendig ist, und sofort heruntergefahren, wenn sie sich im Leerlauf befinden. Das bedeutet, dass der Auftrag bis zur Zuteilung der Knoten warten muss. Dabei ist aber wichtig zu erwähnen, dass Tasks jeweils für Knoten geplant werden, sobald diese einzeln verfügbar sind, zugeteilt wurden und der Starttask abgeschlossen ist. Batch wartet mit dem Zuweisen von Tasks *nicht*, bis alle Knoten in einem Pool verfügbar sind, um eine maximale Auslastung aller verfügbaren Knoten zu gewährleisten.

Auf der anderen Seite, wenn der sofortige Start von Aufträgen die höchste Priorität besitzt, wird ein Pool möglicherweise zu früh erstellt, und die zugehörigen Knoten werden zur Verfügung gestellt, bevor Aufträge übermittelt werden. In diesem Szenario können Auftragstasks sofort starten, aber Knoten befinden sich möglicherweise noch im Leerlauf und warten darauf, dass Tasks zugewiesen werden.

Ein kombinierter Ansatz, der in der Regel zum Behandeln einer variablen, aber kontinuierlichen Auslastung eingesetzt wird, besteht darin, einen Pool zu verwenden, an den mehrere Aufträge übermittelt werden, aber die Anzahl der Knoten je nach Auftragslast nach oben oder nach unten zu skalieren. (Informationen hierzu finden Sie unten im Abschnitt *Skalieren von Anwendungen*.) Dies kann reaktiv, basierend auf der aktuellen Auslastung, oder proaktiv erfolgen, wenn die Auslastung vorausgesagt werden kann.

## <a name="scaling"></a>Skalieren von Anwendungen

Mit der [automatischen Skalierung](batch-automatic-scaling.md) kann die Größe Ihrer Anwendung problemlos automatisch erhöht oder verringert werden, um der erforderlichen Berechnung zu entsprechen. Sie können die Anzahl der Knoten in einem Pool gemäß dem aktuellen Workload und den Statistiken zur Ressourcennutzung dynamisch anpassen. So können Sie die Gesamtkosten für die Ausführung Ihrer Anwendung senken, indem Sie nur die erforderlichen Ressourcen verwenden. Sie können die Skalierungseinstellungen für einen Pool bei dessen Erstellung angeben und diese Konfiguration jederzeit aktualisieren.

Wenn Sie die Anzahl der Knoten automatisch verringern, müssen gleichzeitig ausgeführte Tasks berücksichtigt werden. Mittels einer Richtlinie für die Aufhebung der Zuordnung wird festgelegt, ob aktuell ausgeführte Tasks beendet werden sollen, um den Knoten umgehend zu entfernen, oder ob die Tasks vor dem Entfernen des Knotens abgeschlossen werden sollen. Legen Sie die vorgegebene Anzahl von Knoten am Ende eines Auftrags auf Null fest, und lassen Sie das Abschließen ausgeführter Tasks zu, um die Auslastung zu maximieren.

Die automatische Skalierung einer Anwendung erfolgt durch die Angabe einer Reihe von Skalierungsformeln. Anhand dieser wird die Zielanzahl von Knoten ermittelt, die sich für das nächste Skalierungsintervall im Pool befinden. Beispielsweise ist es für einen Auftrag erforderlich, eine große Anzahl von Tasks zu übermitteln, die zur Ausführung geplant werden sollen. Sie können dem Pool eine Skalierungsformel zuweisen, die die Größe des Pools (die Anzahl der Knoten) auf Basis der aktuellen Anzahl der ausstehenden Tasks und der Abschlussrate dieser Tasks anpasst. Der Batch-Dienst wertet die Formel in regelmäßigen Abständen aus und ändert dabei die Größe des Pools je nach Workload.

Eine Skalierungsformel kann auf den folgenden Metriken basieren:

- **Zeitmetriken**: Basieren auf Statistiken, die alle fünf Minuten für die angegebene Anzahl von Stunden erfasst werden.

- **Ressourcenmetriken**: Basieren auf CPU-Auslastung, Bandbreitenauslastung, Speicherauslastung und der Anzahl von Knoten.

- **Aufgabenmetriken**: Basieren auf dem Aufgabenstatus (wie etwa „Aktiv“, „Ausstehend“ oder „Abgeschlossen“).

Weitere Informationen zur automatischen Skalierung einer Anwendung finden Sie unter [Automatisches Skalieren von Computeknoten in einem Azure Batch-Pool](batch-automatic-scaling.md).

> [AZURE.TIP]
 Einzelne Knoten können aus einem Pool entfernt werden, dies ist jedoch nur sehr selten erforderlich. Wenn beispielsweise ein Knoten weniger zuverlässig erscheint, kann er aus dem Pool entfernt werden, damit keine weiteren Tasks zugewiesen werden.

## <a name="cert"></a>Sicherheit mit Zertifikaten

In der Regel müssen Sie Zertifikate beim Ver- und Entschlüsseln vertraulicher Informationen für Tasks verwenden, z. B. Schlüssel für ein [Azure Storage-Konto][azure_storage]. Zu diesem Zweck können Zertifikate auf Knoten installiert werden. Verschlüsselte geheime Schlüssel werden über Befehlszeilenparameter an Tasks übergeben oder in einer der Taskressourcen eingebettet. Zum Entschlüsseln können dann installierte Zertifikate verwendet werden.

Sie verwenden den Vorgang [Zertifikat hinzufügen][rest_add_cert] (Batch REST-API) bzw. die Methode [CertificateOperations.CreateCertificate][net_create_cert] (Batch .NET-API) zum Hinzufügen eines Zertifikats zu einem Batch-Konto. Sie können das Zertifikat dann einem neuen oder vorhandenen Pool zuordnen. Wenn ein Zertifikat einem Pool zugeordnet ist, installiert der Batch-Dienst das Zertifikat auf jedem Knoten im Pool. Der Batch-Dienst installiert die entsprechenden Zertifikate beim Start des Knotens, bevor jegliche Tasks gestartet werden, einschließlich Start- und Auftrags-Manager-Tasks.

## <a name="scheduling"></a>Zeitplanungspriorität

Aufträgen, die Sie in Batch erstellen, können Sie eine Priorität zuweisen. Der Batch-Dienst verwendet den Prioritätswert des Auftrags, um die Reihenfolge der Auftragsplanung innerhalb eines Kontos zu bestimmen. Die Prioritätswerte reichen von -1000 bis 1000, wobei -1000 die niedrigste und 1000 die höchste Priorität darstellt. Mit dem Vorgang [Eigenschaften eines Auftrags aktualisieren][rest_update_job] (Batch REST-API) oder durch Ändern der Eigenschaft [CloudJob.Priority][net_cloudjob_priority] (Batch .NET-API) können Sie die Priorität eines Auftrags aktualisieren.

Innerhalb desselben Kontos haben Aufträge mit höherer Priorität bei der Planung Vorrang vor Aufträgen mit niedrigerer Priorität. Ein Auftrag mit einem höheren Prioritätswert in einem Konto hat bei der Planung keinen Vorrang vor einem anderen Auftrag mit einem niedrigeren Prioritätswert in einem anderen Konto.

Die Auftragsplanung erfolgt über Pools hinweg unabhängig. Zwischen verschiedenen Pools wird nicht sichergestellt, dass ein Auftrag mit einer höheren Priorität zuerst geplant wird, wenn der ihm zugeordnete Pool nicht über ausreichend Knoten verfügt, die sich im Leerlauf befinden. Innerhalb eines Pools verfügen Aufträge mit gleicher Prioritätsstufe über eine identische Planungswahrscheinlichkeit.

## <a name="environment"></a>Umgebungseinstellungen für Aufgaben

Jeder Task, der in einem Batch-Auftrag ausgeführt wird, hat Zugriff auf Umgebungsvariablen, die sowohl durch den Batch-Dienst (systemdefiniert, siehe Tabelle unten) sowie durch benutzerdefinierte Umgebungsvariablen festgelegt werden. Anwendungen und Skripts, die von Tasks auf Computeknoten ausgeführt werden, können während der Ausführung auf dem Knoten auf diese Umgebungsvariablen zugreifen.

Legen Sie benutzerdefinierte Umgebungsvariablen mit dem Vorgang [Task einem Auftrag hinzufügen][rest_add_task] (Batch REST-API) oder durch Ändern der Eigenschaft [CloudTask.EnvironmentSettings][net_cloudtask_env] (Batch .NET-API) fest, wenn Sie einem Auftrag Tasks hinzufügen.

Rufen Sie die Umgebungsvariablen (sowohl system- als auch benutzerdefiniert) für einen Task ab, indem Sie den Vorgang [Informationen zu einem Task abrufen][rest_get_task_info] verwenden (Batch REST-API) oder auf die Eigenschaft [CloudTask.EnvironmentSettings][net_cloudtask_env] zugreifen (Batch .NET-API). Wie bereits erwähnt, können Prozesse, die auf einem Computeknoten ausgeführt werden, auf alle Umgebungsvariablen zugreifen, z. B. über die bekannte `%VARIABLE_NAME%`-Syntax.

Für jeden Task, der innerhalb eines Auftrags geplant ist, wird vom Dienst-Batch der folgende Satz von systemdefinierten Umgebungsvariablen festgelegt:

| Name der Umgebungsvariablen | Beschreibung |
|---------------------------------|--------------------------------------------------------------------------|
| `AZ_BATCH_ACCOUNT_NAME` | Der Name des Kontos, zu dem die Aufgabe gehört. |
| `AZ_BATCH_JOB_ID` | Die ID des Auftrags, zu dem der Task gehört |
| `AZ_BATCH_JOB_PREP_DIR` | Der vollständige Pfad des Aufgabenverzeichnisses für die Auftragsvorbereitung auf dem Knoten. |
| `AZ_BATCH_JOB_PREP_WORKING_DIR` | Der vollständige Pfad des Aufgabenarbeitsverzeichnisses für die Auftragsvorbereitung auf dem Knoten. |
| `AZ_BATCH_NODE_ID` | Die ID des Knotens, auf dem der Task ausgeführt wird |
| `AZ_BATCH_NODE_ROOT_DIR` | Der vollständige Pfad des Stammverzeichnisses auf dem Knoten. |
| `AZ_BATCH_NODE_SHARED_DIR` | Der vollständige Pfad des freigegebenen Verzeichnisses auf dem Knoten. |
| `AZ_BATCH_NODE_STARTUP_DIR` | Der vollständige Pfad des Starttaskverzeichnisses des Computeknotens auf dem Knoten |
| `AZ_BATCH_POOL_ID` | Die ID des Pools, in dem der Task ausgeführt wird |
| `AZ_BATCH_TASK_DIR` | Der vollständige Pfad des Aufgabenverzeichnisses auf dem Knoten. |
| `AZ_BATCH_TASK_ID` | Die ID des aktuellen Tasks |
| `AZ_BATCH_TASK_WORKING_DIR` | Der vollständige Pfad des Aufgabenarbeitsverzeichnisses auf dem Knoten. |

>[AZURE.NOTE] Keine der oben beschriebenen systemdefinierten Variablen kann überschrieben werden – sie sind schreibgeschützt.

## <a name="errorhandling"></a>Fehlerbehandlung

Möglicherweise ist es für Sie notwendig, sowohl Task- als auch Anwendungsfehler innerhalb Ihrer Batch-Lösung zu behandeln.

### Behandeln von Taskfehlern
Bei Taskfehlern wird zwischen folgenden Kategorien unterschieden:

- **Planungsfehler**
	- Wenn die Übertragung von Dateien, die für einen Task angegeben sind, aus irgendeinem Grund nicht erfolgreich durchgeführt werden kann, wird für den Task ein „Planungsfehler“ festgelegt.
	- Ursachen für Planungsfehler liegen eventuell darin, dass die Dateien verschoben wurden, das Storage-Konto nicht mehr zur Verfügung steht oder ein anderes Problem aufgetreten ist, das ein erfolgreiches Kopieren von Dateien auf den Knoten verhindert hat.
- **Anwendungsfehler**
	- Ein Fehler kann auch beim in der Befehlszeile des Tasks angegebenen Prozess auftreten. Der Prozess gilt als nicht erfolgreich, wenn der Exitcode, der vom durch den Task ausgeführten Prozess zurückgegeben wird, ungleich Null ist.
	- Batch kann so konfiguriert werden, dass der Task im Falle eines Anwendungsfehlers automatisch mit einer bestimmten Häufigkeit wiederholt wird.
- **Einschränkungsfehler**
	- Eine Einschränkung kann festgelegt werden, in der die maximale Ausführungsdauer für einen Auftrag oder Task angegeben wird, die *maxWallClockTime*. Dies kann nützlich für das Beenden stillstehender Tasks sein.
	- Wenn die maximal zulässige Zeitspanne überschritten wurde, wird der Task als *abgeschlossen* gekennzeichnet. Als Exitcode wird jedoch `0xC000013A` zurückgegeben, und das Feld *schedulingError* wird als `{ category:"ServerError", code="TaskEnded"}` markiert.

### Debuggen von Anwendungsfehlern

Während der Ausführung generiert eine Anwendung unter Umständen eine Diagnoseausgabe für die Problembehandlung. Wie oben unter [Dateien und Verzeichnisse](#files) bereits erwähnt, sendet der Batch-Dienst stdout- und stderr-Ausgabe an `stdout.txt`- und `stderr.txt`-Dateien im Taskverzeichnis auf dem Computeknoten. Mithilfe von [ComputeNode.GetNodeFile][net_getfile_node] und [CloudTask.GetNodeFile][net_getfile_task] in der .NET-API von Batch können Sie diese und andere Dateien zu Problembehandlungszwecken abrufen.

Noch umfangreicheres Debuggen kann durchgeführt werden, indem Sie sich mit *Remotedesktop* bei einem Computeknoten anmelden. Für die Remoteanmeldung können Sie [eine Remotedesktop-Protokolldatei von einem Knoten abrufen][rest_rdp] (Batch REST-API) oder die Methode [ComputeNode.GetRDPFile][net_rdp] einsetzen (Batch .NET API).

>[AZURE.NOTE] Um über RDP eine Verbindung mit einem Knoten herzustellen, müssen Sie zuerst einen Benutzer auf dem Knoten erstellen. [Fügen Sie in der Batch REST-API einem Knoten ein Benutzerkonto hinzu][rest_create_user], oder verwenden Sie die Methode [ComputeNode.CreateComputeNodeUser][net_create_user] in Batch .NET.

### Erläuterung zu Taskfehlern oder Unterbrechungen

Tasks werden gelegentlich nicht erfolgreich durchgeführt oder unterbrochen. Es tritt ein Fehler in der Taskanwendung selbst auf, der Knoten, auf dem der Task ausgeführt wird, wird neu gestartet, oder der Knoten wird während einer Änderung der Poolgröße entfernt, wenn die Richtlinie für die Aufhebung der Zuordnung des Knotens so konfiguriert ist, dass der Knoten sofort entfernt wird, ohne auf den Abschluss des Tasks zu warten. In diesen Fällen kann der Task automatisch per Batch wieder der Warteschlange hinzugefügt und auf einem anderen Knoten ausgeführt werden.

Außerdem können zeitweilig Probleme auftreten, die dazu führen, dass eine Aufgabe nicht mehr reagiert oder zu lange dauert. Hierzu kann für den Task eine maximal zulässige Ausführungszeit festgelegt werden. Wird dieser Wert überschritten, unterbricht Batch die Taskanwendung.

### Erläuterung zu fehlerhaften Knoten

Jeder Knoten in einem Pool erhält eine eindeutige ID, und der Knoten, auf dem ein Task ausgeführt wird, ist in den Metadaten des Tasks angegeben. In Situationen, in denen Tasks auf einem bestimmten Knoten nicht erfolgreich durchgeführt werden, kann dieser von Ihrer Batch-Clientanwendung ermittelt und der fehlerverdächtige Knoten aus dem Pool entfernt werden. Wenn Tasks auf einem Knoten ausgeführt werden, der gelöscht wird, werden die Tasks automatisch wieder in die Warteschlange zur Ausführung auf anderen Knoten eingereiht.

## Nächste Schritte

- Erstellen Sie Ihre erste Batch-Anwendung mithilfe der Schritte unter [Erste Schritte mit der Azure-Batch-Bibliothek für .NET](batch-dotnet-get-started.md)
- Laden Sie das [Batch-Explorer][batch_explorer_project]-Beispielprojekt, und erstellen Sie es, um es während der Entwicklung Ihrer Batch-Lösungen zu verwenden. Mit dem Batch-Explorer können Sie die folgenden Aktionen und viele mehr ausführen:
  - Überwachen und Bearbeiten von Pools, Aufträgen und Tasks in Ihrem Batch-Konto
  - Herunterladen von `stdout.txt`, `stderr.txt` und anderen Dateien von Knoten
  - Erstellen von Benutzern auf Knoten und Herunterladen von RDP-Dateien für die Remoteanmeldung

[1]: ./media/batch-api-basics/node-folder-structure.png

[about_cloud_services]: https://azure.microsoft.com/documentation/articles/fundamentals-application-models/#tell-me-about-cloud-services
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloud_service_sizes]: https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx

[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_multiinstance]: https://msdn.microsoft.com/de-DE/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx

<!---HONumber=AcomDC_0224_2016-->