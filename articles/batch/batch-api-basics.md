<properties 
	pageTitle="API-Grundlagen für Azure Batch" 
	description="Konzepte für die Einführung von Entwicklern in die Azure Batch-APIs und den Batch-Dienst" 
	services="batch" 
	documentationCenter=".net" 
	authors="yidingzhou" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="batch" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-compute" 
	ms.date="03/19/2015" 
	ms.author="yidingz"/>

<!--The next line, with one pound sign at the beginning, is the page title-->
# API-Grundlagen für Azure Batch

Der Azure Batch-Dienst bietet ein Framework für die Auftragsplanung zur skalierbaren und verteilten Berechnung. Der Batch-Dienst verwaltet einen Satz virtueller Computer, die über verschiedene Cluster und Rechenzentren in Azure verteilt sind. Der Batch-Dienst erreicht die verteilte Berechnung durch Ausführen eines oder mehrerer Programme. Die Ausführung erfolgt entweder nach Bedarf oder zu einem geplanten Zeitpunkt für eine bestimmte Sammlung dieser virtuellen Computer. Der Batch-Dienst verwaltet diese virtuellen Computer, damit Ihre Berechnungsaufgaben gemäß den Ressourcenanforderungen, den Spezifikationen und den von Ihnen bereitgestellten Einschränkungen ausgeführt werden.

Der Batch-Dienst sorgt dafür, dass Sie für Message Queuing, Planung, Zuweisung und Verwaltung von Computerressourcen keinen Code mehr schreiben müssen. Auf diese Weise können Sie sich auf die jeweilige Anwendung konzentrieren und müssen sich nicht um die Komplexität der Auftragsplanung und Ressourcenverwaltung für die zugrunde liegende Plattform kümmern. Dadurch kann der Batch-Dienst auch den Speicherort dieser Aufträge sowie deren Zugriff auf die zu verarbeitenden Daten optimieren.

Im folgenden werden einige der Szenarien aufgeführt, die Sie mithilfe des Batch-Diensts ermöglichen können:

- Rechenintensive parallele Verarbeitung

- Tägliche Bereinigung von Dateien

- Batchverarbeitung

## <a name="resource"></a> Ressourcen des Batch-Diensts

Wenn Sie den Batch-Dienst verwenden, nutzen Sie die folgenden Ressourcen:

- [Konto](#account)

- [Virtueller Aufgabencomputer](#taskvm)

- [Pool](#pool)

- [Arbeitselement](#workitem)

- [Job](#job)

- [Aufgabe](#task)

	- [Startaufgabe](#starttask)
	
	- [Auftrags-Manager-Aufgabe](#jobmanagertask)

### <a name="account"></a>Konto

Ein Batch-Konto ist eine eindeutig identifizierte Entität innerhalb des Batch-Diensts. Die gesamte Verarbeitung erfolgt über ein Batch-Konto. Beim Ausführen von Operationen mit dem Batch-Dienst benötigen Sie den Kontonamen und den Schlüssel für das Konto. Informationen zum Erstellen eines Batch-Kontos finden Sie unter [Azure Batch – Technische Übersicht][] im Abschnitt „Batch-Konto“.


### <a name="taskvm"></a>Virtueller Aufgabencomputer

Ein virtueller Aufgabencomputer (Virtual Task Machine, TVM) ist ein virtueller Azure-Computer, der für Ihre Anwendung für eine bestimmte Workload vorgesehen ist. Die Größe eines virtuellen Aufgabencomputers bestimmt die Anzahl der CPU-Kerne, die Speicherkapazität und die lokale Dateisystemgröße, die dem virtuellen Aufgabencomputer zugeordnet werden. Ein virtueller Aufgabencomputer kann ein kleiner, großer oder sehr großer virtueller Computer sein, wie unter [Größen virtueller Computer und Cloud-Dienste für Azure](http://msdn.microsoft.com/library/dn197896.aspx) beschrieben.

Die von einem virtuellen Aufgabencomputer ausführbaren Programme umfassen ausführbare Dateien (EXE), Befehlsdateien (CMD), Batchdateien (BAT) und Skriptdateien. Ein virtueller Aufgabencomputer verfügt außerdem über die folgenden Attribute:

- Aufgabenspezifische und freigegebene Dateisystemordner. Ordnerstruktur und Umgebungsvariablen werden auf jedem virtuellen Poolcomputer erstellt. Die folgende Ordnerstruktur wird mit einem freigegebenen Ordner für gemeinsam genutzte Anwendungen und Daten sowie mit jeweils einem Ordner pro Aufgabe erstellt:

![][1]

- Stdout.txt- und stderr.txt-Dateien, die in einen aufgabenspezifischen Ordner geschrieben werden

- Umgebungsvariablen für die Verarbeitung

- Für die Zugriffssteuerung konfigurierte Firewall-Einstellungen

>Zugriff auf virtuelle Computer
>
>Wenn beispielsweise zum Debuggen auf einen virtuellen Computer zugegriffen werden muss, kann die RDP-Datei abgerufen werden, um per Remotedesktop auf den virtuellen Computer zuzugreifen.


### <a name="pool"></a>Pool

Ein Pool besteht aus einer Sammlung von virtuellen Aufgabencomputern, auf denen Ihre Anwendung ausgeführt wird. Der Pool kann von Ihnen oder automatisch vom Batch-Dienst erstellt werden, wenn Sie die zu erbringende Arbeitsleistung angeben. Sie können einen Pool erstellen und verwalten, der den Anforderungen Ihrer Anwendung entspricht. Ein Pool kann nur von dem Batch-Konto verwendet werden, in dem er erstellt wurde. Ein Batch-Konto kann über mehrere Pools verfügen.

Azure Batch-Pools basieren auf der grundlegenden Azure-Computingplattform und bieten Funktionen für umfangreiche Zuordnungen, für die Anwendungs- und Dateninstallation, für die Datenverschiebung sowie für die Systemüberwachung und die flexible Skalierung virtueller Computer.

Jedem virtuellen Aufgabencomputer (TVM), der einem Pool hinzugefügt wird, ist ein eindeutiger Name und eine zugehörige IP-Adresse zugewiesen. Wenn ein virtueller Aufgabencomputer aus einem Pool entfernt wird, gehen die am Betriebssystem, an den lokalen Dateien, seinem Namen und seiner IP-Adresse vorgenommenen Änderungen verloren. Wenn eine TVM einen Pool verlässt, endet ihre Lebensdauer.

Sie können einen Pool derart konfigurieren, dass die Kommunikation zwischen den darin enthaltenen virtuellen Aufgabencomputern gestattet ist. Wenn die Kommunikation innerhalb eines Pools für einen Pool angefordert wird, aktiviert der Batch-Dienst für jeden virtuellen Aufgabencomputer im Pool Ports, die über 1100 liegen. Jeder virtuelle Aufgabencomputer im Pool ist so konfiguriert, dass eingehende Verbindungen nur für diesen Portbereich und ausschließlich von anderen virtuellen Aufgabencomputern im Pool zugelassen oder auf diesen beschränkt werden. Wenn für Ihre Anwendung keine Kommunikation zwischen virtuellen Aufgabencomputern erforderlich ist, kann der Batch-Dienst potenziell eine große Anzahl von virtuellen Aufgabencomputern in verschiedenen Clustern oder Rechenzentren für den Pool reservieren, um die Möglichkeit zur parallelen Verarbeitung zu erhöhen.

Wenn Sie einen Pool erstellen, können Sie die folgenden Attribute angeben:

- Die **Größe der virtuellen Computer** im Pool.
	- Bei der Wahl der entsprechenden Größe des virtuellen Computers müssen die Eigenschaften und Anforderungen der Anwendungen berücksichtigt werden, die auf dem virtuellen Computer verwendet werden sollen. Normalerweise wird bei der Wahl der Größe des virtuellen Computers davon ausgegangen, dass auf dem virtuellen Computer jeweils eine Aufgabe ausgeführt wird. Die optimale und kostengünstigste Größe des virtuellen Computers ist beispielsweise abhängig davon, ob es sich um eine Multithread-Anwendung handelt und wie viel Arbeitsspeicher die Anwendung beansprucht. Es ist möglich, mehrere Aufgaben zuzuweisen und mehrere Anwendungsinstanzen parallel auszuführen. In diesem Fall wird üblicherweise ein größerer virtueller Computer verwendet. Weitere Informationen finden Sie weiter unten. 
	- Aller virtuellen Computer in einem Pool müssen die gleiche Größe besitzen. Wenn verschiedene Anwendungen mit unterschiedlichen Systemanforderungen und/oder unterschiedlicher Last ausgeführt werden, erstellen Sie separate Pools.
	- Alle Clouddienst-VM-Größen können für einen Pool konfiguriert werden. Einzige Ausnahme: A0.

- Die Betriebssystemfamilie und -version, die auf den virtuellen Computern ausgeführt wird.
	- Betriebssystemfamilie und -version können genau wie bei Workerrollen konfiguriert werden.
	- Die Betriebssystemfamilie bestimmt auch, welche Versionen von .NET mit dem Betriebssystem installiert sind.
	- Genau wie bei Workerrollen empfiehlt sich auch bei der Betriebssystemversion die Verwendung von „*“, damit die virtuellen Computer automatisch aktualisiert werden und für neue Versionen kein Zusatzaufwand entsteht. Mit der Wahl einer bestimmten Betriebssystemversion wird in erster Linie die Anwendungskompatibilität sichergestellt. Hierzu wird die Überprüfung der Abwärtskompatibilität vor der Versionsaktualisierung ermöglicht. Nach der Überprüfung kann die Betriebssystemversion für den Pool aktualisiert und das neue Betriebssystemimage installiert werden. Dabei werden alle ausgeführten Aufgaben unterbrochen und wieder der Warteschlange hinzugefügt.

- Die vorgegebene Anzahl virtueller Computer, die für den Pool verfügbar sein soll.

- Die Skalierungsrichtlinie für den Pool. Neben der Anzahl virtueller Computer können Sie für jeden Pool auch eine Formel für die automatische Skalierung angeben. Der Batch-Dienst führt die Formel aus, um die Anzahl der virtuellen Computer auf der Grundlage der Pool- und Arbeitselementstatistik anzupassen.

- Konfigurieren des Zeitplans
	- In der Standardkonfiguration werden auf einem virtuellen Computer immer nur einzelne Aufgaben ausgeführt. Es sind aber auch Szenarien denkbar, in denen die gleichzeitige Ausführung mehrerer Aufgaben auf einem virtuellen Computer von Vorteil ist. Ein Beispiel wäre etwa die Steigerung der Auslastung des virtuellen Computers, wenn eine Anwendung auf E/A warten muss: Durch die Ausführung mehrerer Anwendungen erhöht sich die CPU-Auslastung. Ein weiteres Beispiel wäre die Verringerung der Anzahl von virtuellen Computern im Pool: Dadurch lässt sich ggf. die Menge an erforderlichen Datenkopien für umfangreiche Datasets verringern. Wäre also „A1“ die korrekte Größe für die Anwendung, könnte „A4“ ausgewählt und für die gleichzeitige Ausführung von acht Aufgaben konfiguriert werden, wobei jeweils ein Kern genutzt würde.
	- Die Konfiguration der maximalen Aufgaben pro virtuellem Computer bestimmt die maximale Anzahl von Aufgaben, die parallel ausgeführt werden können.
	- Darüber hinaus können Sie durch Angabe einer Füllrichtlinie bestimmen, ob virtuelle Computer gefüllt oder ob die Aufgaben auf alle virtuellen Computer verteilt werden sollen.
 
- Der Kommunikationsstatus der virtuellen Computer im Pool.
 	- In vielen Szenarien werden Aufgaben unabhängig ausgeführt und müssen nicht mit anderen Aufgaben kommunizieren. Es gibt jedoch auch Anwendungen mit Aufgabenkommunikation (beispielsweise Anwendungen mit MPI).
	- Sie können konfigurieren, ob die virtuellen Computer kommunizieren können. Dies hat Auswirkungen auf die zugrunde liegende Infrastruktur sowie auf die Platzierung der virtuellen Computer.

- Die Startaufgabe für virtuelle Aufgabencomputer im Pool.

Wenn Sie einen Pool erstellen, können Sie das Speicherkonto angeben, das dem Pool zugewiesen werden soll. Der Batch-Dienst ordnet virtuelle Aufgabencomputer der Rechenzentren mit besserer Netzwerkkonnektivität und Bandbreitenkapazität dem angegebenen Speicherkonto zu. Dadurch können Workloads effizienter auf Daten zugreifen.

### <a name="workitem"></a>Arbeitselement

Ein Arbeitselement gibt an, wie die Berechnung auf den virtuellen Aufgabencomputern in einem Pool ausgeführt wird.

- Einem Arbeitselement können einzelne oder mehrere Aufträge zugeordnet sein. Für Arbeitselemente kann ein optionaler Zeitplan angegeben werden. In diesem Fall wird dann für jedes Vorkommen des Zeitplans ein Auftrag erstellt. Ohne Angabe eines Zeitplans (also bei bedarfsbasierten Aufgaben) wird sofort ein Auftrag erstellt.
- Das Arbeitselement gibt den Pool an, für den die Aufgabe ausgeführt wird. Der Pool kann ein vorhandener, bereits erstellter Pool sein, der von vielen Arbeitselementen verwendet wird. Alternativ kann auch ein Pool erstellt werden – entweder für jeden dem Arbeitselement zugeordneten Auftrag oder für alle dem Arbeitselement zugeordnete Aufträge.
- Eine optionale Priorität kann angegeben werden. Wenn ein Arbeitselement übermittelt wird, das eine höhere Priorität besitzt als andere, bereits in Bearbeitung befindliche Arbeitselemente, werden die Aufgaben des Arbeitselements mit der höheren Priorität vor den Aufgaben der Arbeitselemente mit niedrigerer Priorität in die Warteschlange eingefügt. Bereits ausgeführte Aufgaben mit niedrigerer Priorität werden nicht nach hinten verschoben.
- Sie können Einschränkungen angeben, die dann auf die zugeordneten Aufträge angewendet werden.
	- Für die Aufträge kann eine maximale Wanduhrzeit festgelegt werden. Wenn die Ausführungsdauer der Aufträge die angegebene maximale Wanduhrzeit übersteigt, werden der Auftrag und alle dazugehörigen Aufgaben beendet.
	- Azure Batch kann nicht erfolgreich ausgeführte Aufgaben erkennen und wiederholen. Der Standardwert für die maximale Anzahl von Aufgabenwiederholungen kann als Einschränkung angegeben werden. Hierbei können Sie auch angeben, ob eine Aufgabe immer oder niemals wiederholt werden soll. Bei der Wiederholung einer Aufgabe wird diese nochmals der Warteschlange hinzugefügt und erneut ausgeführt.
- Die Aufgaben, die für den Arbeitselementauftrag ausgeführt werden sollen, können vom Client auf die gleiche Weise angegeben werden, mit der auch das Arbeitselement erstellt wurde. Alternativ kann aber auch eine Auftrags-Manager-Aufgabe angegeben werden. Eine Auftrags-Manager-Aufgabe verwendet die Batch-API und enthält den Code zum Erstellen der erforderlichen Aufgaben für einen Auftrag, wobei die Aufgabe auf einem der virtueller Poolcomputer ausgeführt wird. Die Auftrags-Manager-Aufgabe wird speziell von Batch behandelt: Sie wird sofort nach der Auftragserstellung der Warteschlange hinzugefügt und erneut gestartet, falls sie nicht erfolgreich ausgeführt werden konnte. Ein Auftrags-Manager wird für Arbeitselemente mit zugeordnetem Zeitplan benötigt, da sich nur so Aufgaben vor der Auftragsinstanziierung definieren lassen.

### <a name="job"></a>Auftrag

Ein Auftrag ist eine aktive Instanz eines Arbeitselements und besteht aus einer Sammlung von Aufgaben. Der Batch-Dienst instanziiert einen Auftrag auf Basis der Konfiguration des Arbeitselements. Der Auftrag verwendet virtuelle Aufgabencomputer aus dem Pool, der dem Arbeitselement zugeordnet ist.

### <a name="task"></a>Aufgabe

Eine Aufgabe ist eine Einheit der Berechnung, die einem Auftrag zugeordnet ist und auf einem virtuellen Aufgabencomputer ausgeführt wird. Aufgaben werden einem virtuellen Computer zur Ausführung zugewiesen oder der Warteschlange hinzugefügt, bis ein virtueller Computer verfügbar wird. Eine Aufgabe verwendet die folgenden Ressourcen:

- Das Programm, das im Arbeitselement angegeben wurde.

- Die Ressourcendateien, die die zu verarbeitenden Daten enthalten. Diese Dateien werden automatisch aus dem BLOB-Speicher auf den virtuellen Aufgabencomputer kopiert. Weitere Informationen finden Sie unter Dateien und Verzeichnisse.

- Die Umgebungseinstellungen, die von dem Programm benötigt werden. Weitere Informationen finden Sie unter den Umgebungseinstellungen für Aufgaben.

- Die Einschränkungen, unter denen die Berechnung erfolgen soll. Beispielsweise die maximale Zeit, in der die Aufgabe ausgeführt werden darf, die maximale Anzahl, mit der eine Aufgabe bei der fehlerhaften Ausführung wiederholt werden sollte und die maximal zulässige Dauer, die Dateien im Arbeitsverzeichnis beibehalten werden.

Zusätzlich zu Aufgaben, die Sie zur Berechnung auf einem virtuellen Aufgabencomputer definieren können, stehen die folgenden speziellen Aufgaben zur Verfügung, die vom Batch-Dienst bereitgestellt werden:

- [Startaufgabe](#starttask)

- [Auftrags-Manager-Aufgabe](#jobmanagertask)

#### <a name="starttask"></a>Startaufgabe

Sie können das Betriebssystem von virtuellen Computern in einem Pool konfigurieren, indem Sie dem Pool eine Startaufgabe zuordnen. Das Installieren der Software und das Starten von Hintergrundprozessen sind einige der Aktionen, die von Startaufgaben ausgeführt werden können. Die Startaufgabe wird jedes Mal ausgeführt, wenn ein virtueller Computer gestartet wird und sich im Pool befindet.

Wie bei jeder anderen Batch-Aufgabe kann zusätzlich zu einer Befehlszeile, die von Batch ausgeführt wird, eine Liste mit Dateien in Azure Storage angegeben werden. Azure Batch kopiert zunächst die Dateien aus Azure Storage und führt dann die Befehlszeile aus. Bei einer poolbezogenen Startaufgabe enthält die Dateiliste üblicherweise die Anwendungsdateien oder das Anwendungspaket. Sie kann aber auch Referenzdaten für alle Aufgaben enthalten, die auf den virtuellen Poolcomputern ausgeführt werden. Die Befehlszeile kann ein PowerShell-Skript oder einen Robocopy-Vorgang ausführen, um beispielsweise Anwendungsdateien in den freigegebenen Ordner zu kopieren. Aber auch eine MSI-Datei kann ausgeführt werden.

In der Regel ist es wünschenswert, dass Batch auf den Abschluss der Startaufgabe wartet und dann davon ausgeht, dass der virtuelle Computer nun für die Aufgabenzuweisung bereit ist. Dieses Verhalten ist jedoch konfigurierbar.

Kann eine Startaufgabe für einen virtuellen Poolcomputer nicht erfolgreich ausgeführt werden, wird der Zustand des virtuellen Computers entsprechend aktualisiert, und der virtuelle Computer steht für die Aufgabenzuweisung nicht zur Verfügung. Bei einer Startaufgabe kann ein Fehler auftreten, wenn die für die Startaufgabe angegebenen Dateien nicht kopiert werden können oder für den Startaufgabenprozess ein anderer Wert als Null zurückgegeben wird.

Da alle Informationen zum Konfigurieren des virtuellen Computers und zum Installieren der Anwendungen deklariert werden, muss zum Erhöhen der Anzahl virtueller Computer in einem Pool lediglich die benötigte Anzahl angegeben werden. Batch verfügt über sämtliche Informationen, die erforderlich sind, um die virtuellen Computer zu konfigurieren und für die Aufgabenannahme vorzubereiten.

Eine Startaufgabe wird durch Hinzufügen eines JSON-Abschnitts zum Anforderungstext für den Vorgang „Pool hinzufügen“ definiert. Das folgende Beispiel zeigt eine Basisdefinition einer Startaufgabe:

	{
		“commandLine”:”mypoolsetup.exe”,
		“resourceFiles”:
		[
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”mypoolsetup.exe”
			},
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp2.exe”
			}
		],
		“maxTaskRetryCount”:0
	}

Eine C#-Schnittstelle sieht folgendermaßen aus:

	ICloudPool pool = pm.CreatePool(poolName, targetDedicated: 3, vmSize: "small", osFamily: "3");
	pool.StartTask = new StartTask();
	pool.StartTask.CommandLine = "mypoolsetup.exe";
	pool.StartTask.ResourceFiles = new List<IResourceFile>();
	pool.StartTask.ResourceFiles.Add(new ResourceFile("http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d", "mypoolsetup.exe"));
	pool.Commit();


#### <a name="jobmanagertask"></a>Auftrags-Manager-Aufgabe

Eine Auftrags-Manager-Aufgabe wird vor allen anderen Aufgaben gestartet. Die Auftrags-Manager-Aufgabe bietet die folgenden Vorteile:

- Er wird automatisch vom Batch-Dienst erstellt, wenn der Auftrag erstellt wird.

- Es ist vor anderen Aufgaben im Auftrag geplant.

- Sein zugeordneter virtueller Aufgabencomputer wird zuletzt aus einem Pool entfernt, wenn der Pool verkleinert wird.

- Es wird die höchste Priorität zugewiesen, wenn er neu gestartet werden muss. Wenn ein virtueller Aufgabencomputer im Leerlauf nicht verfügbar ist, kann der Batch-Dienst eine der ausgeführten Aufgaben im Pool beenden, um Platz für die Ausführung zu schaffen.

- Die Beendigung kann an die Beendigung aller Aufgaben im Auftrag gebunden sein.

Eine Auftrags-Manager-Aufgabe in einem Auftrag besitzt keine höhere Priorität als Aufgaben in anderen Aufträgen. Auftragsübergreifend werden nur Prioritäten auf Auftragsebene beobachtet. Eine Auftrags-Manager-Aufgabe wird durch Hinzufügen eines XML-Abschnitts zum Anforderungstext für den Vorgang "Arbeitselement hinzufügen" definiert. Das folgende Beispiel zeigt eine Basisdefinition einer Auftrags-Manager-Aufgabe:

	{
		“name”:”jmTask”,
		“commandLine”:”myapp1.exe”,
		“resourceFiles”:
		[
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp1.exe”
			},
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp2.exe”
			}
		],
		“taskConstraints”:
		{
			“maxWallClockTime”:”PT1H”,
			“maxTaskRetryCount”:0,
			“retentionTime”:”PT1H”
		},
		“killJobOnCompletion”:true,
		“runElevated”:false,
		“runExclusive”:true
	}




## <a name="workflow"></a>Workflow des Batch-Diensts

Sie benötigen ein Batch-Konto, um den Batch-Dienst verwenden zu können, und Sie verwenden mehrere Ressourcen des Diensts zur Planung der Berechnungen. Sie verwenden den folgenden einfachen Workflow bei der Erstellung eines verteilten Rechenszenarios mit dem Batch-Dienst:

1\. Laden Sie die Dateien, die Sie in Ihrem verteilten Rechenszenario verwenden möchten, an ein Azure-Speicherkonto hoch. Diese Dateien müssen im Speicherkonto sein, damit der Batch-Dienst darauf zugreifen kann. Der Batch-Dienst lädt sie beim Ausführen der Aufgabe in eine TVM.

2\. Laden Sie die abhängigen Binärdateien an das Speicherkonto hoch. Die Binärdateien enthalten das Programm, das durch die Aufgabe und die abhängigen Assemblys ausgeführt wird. Auf diese Dateien muss auch aus dem Speicher zugegriffen werden und sie werden auf die TVM geladen.

3\. Erstellen Sie einen Pool mit virtuellen Aufgabencomputern. Sie können die Größe des zu verwendenden virtuellen Aufgabencomputers bei der Erstellung eines Pools zuweisen. Wenn eine Aufgabe ausgeführt wird, wird ihr eine TVM aus diesem Pool zugewiesen.

4\. Erstellen Sie ein Arbeitselement. Beim Erstellen eines Arbeitselements wird automatisch ein Auftrag erstellt. Mit einem Arbeitselement können Sie einen Auftrag von Aufgaben verwalten.

5\. Fügen Sie dem Arbeitselement Aufgaben hinzu. Jede Aufgabe verwendet das von Ihnen hochgeladene Programm, um Informationen aus einer Datei zu verarbeiten, die Sie ebenfalls hochgeladen haben.

6\. Überwachen Sie die Ergebnisse der Ausgabe.

## <a name="files"></a>Dateien und Verzeichnisse

Jede Aufgabe verfügt über ein Arbeitsverzeichnis, unter dem null oder mehr Verzeichnisse und Dateien für das Speichern des von einer Aufgabe ausgeführten Programms, der von einer Aufgabe verarbeiteten Daten und der Ausgabe der von einer Aufgabe ausgeführten Verarbeitung erstellt werden. Diese Verzeichnisse und Dateien sind dann während der Ausführung eines Auftrags für andere Aufgaben verfügbar. Alle Aufgaben, Dateien und Verzeichnisse auf einer TVM gehören einem einzelnen Benutzerkonto.

Der Batch-Dienst stellt einen Teil des Dateisystems auf einem virtuellen Aufgabencomputer als Stammverzeichnis bereit. Das Stammverzeichnis des virtuellen Aufgabencomputers steht einer Aufgabe über die Umgebungsvariable „WATASK_TVM_ROOT_DIR“ zur Verfügung. Weitere Informationen zur Verwendung von Umgebungsvariablen finden Sie unter den Umgebungseinstellungen für Aufgaben.

Das Stammverzeichnis enthält die folgenden Unterverzeichnisse:

- **Aufgaben**: Hier werden alle Dateien gespeichert, die zu den auf dem virtuellen Aufgabencomputer ausgeführten Aufgaben gehören. Der Batch-Dienst erstellt für jede Aufgabe ein Arbeitsverzeichnis mit einem eindeutigen Pfad im folgenden Format: %WATASK_TVM_ROOT_DIR%/tasks/Arbeitselementname/Auftragsname/Aufgabenname/. Dieses Verzeichnis bietet Lese-/Schreibzugriff auf die Aufgabe. Die Aufgabe kann Dateien unter diesem Verzeichnis erstellen, lesen, aktualisieren und löschen, und dieses Verzeichnis bleibt auf Basis der für diese Aufgabe angegebenen "RetentionTime"-Einschränkung erhalten.

- **Shared**: Bei diesem Speicherort handelt es sich um ein freigegebenes Verzeichnis für alle Aufgaben des Kontos. Auf dem virtuellen Aufgabencomputer befindet sich das freigegebene Verzeichnis unter „%WATASK_TVM_ROOT_DIR%/shared“. Dieses Verzeichnis bietet Lese-/Schreibzugriff auf die Aufgabe. Die Aufgabe kann Dateien unter diesem Verzeichnis erstellen, lesen, aktualisieren und löschen.

- **Start**: Dieser Speicherort wird von einer Startaufgabe als Arbeitsverzeichnis verwendet. Alle Dateien, die vom Batch-Dienst heruntergeladen werden, um die Startaufgabe zu starten, werden ebenfalls unter diesem Verzeichnis gespeichert. Auf dem virtuellen Aufgabencomputer befindet sich das Startverzeichnis unter „%WATASK_TVM_ROOT_DIR%/start“. Die Aufgabe kann Dateien unter diesem Verzeichnis erstellen, lesen, aktualisieren und löschen, und dieses Verzeichnis kann von Startaufgaben dazu verwendet werden, das Betriebssystem zu konfigurieren.

Wenn ein virtueller Aufgabencomputer aus einem Pool entfernt wird, werden alle auf dem virtuellen Aufgabencomputer gespeicherten Dateien entfernt.

## <a name="lifetime"></a>Pool und Lebensdauer virtueller Computer

Zu den grundlegenden Entscheidung zählt die Frage, wann Pools erstellt und wie lange virtuelle Computer vorgehalten werden sollen.

Im Extremfall könnte beispielsweise für jeden Auftrag ein Pool erstellt werden, wenn der Auftrag übermittelt wird, und die virtuellen Computers könnten nach jeder abgeschlossenen Aufgabe wieder entfernt werden. Dadurch maximiert sich die Auslastung: Die virtuellen Computer werden nur genutzt, wenn dies unbedingt notwendig ist, und sofort heruntergefahren, wenn sie sich im Leerlauf befinden. Das bedeutet, dass der Auftrag bis zur Zuteilung der virtuellen Computer warten muss. Dabei ist aber wichtig zu erwähnen, dass Aufgaben jeweils für virtuelle Computer geplant werden, sobald diese verfügbar sind, zugeteilt wurden und die Startaufgabe abgeschlossen ist. Mit anderen Worten: Batch wartet NICHT, bis alle virtuellen Computer in einem Pool verfügbar sind, da dies eine schlechte Auslastung zur Folge hätte.

Wenn die sofortige Ausführung von Aufträgen Priorität hat, muss vor der Übermittlung des Auftrags ein Pool erstellt werden, und die virtuellen Computer müssen verfügbar sein. Die Aufgaben können zwar umgehend starten, je nach Auslastung befinden sich dann aber virtuelle Computer im Leerlauf und warten auf Auftragsaufgaben.

Bei variabler Last wird häufig ein Pool verwendet, an den mehrere Aufträge übermittelt werden, wobei die Anzahl der virtuellen Computer lastabhängig skaliert wird. Hierzu kann ein reaktives Modell verwendet werden (oder auch ein proaktives, sofern die Auslastung vorhersehbar ist).

## <a name="scaling"></a>Skalieren von Anwendungen

Die Größe Ihrer Anwendung kann problemlos automatisch erhöht oder verringert werden, um der erforderlichen Berechnung zu entsprechen. Sie können die Anzahl der virtuellen Aufgabencomputer in einem Pool entsprechend der aktuellen Statistik zur Workload und Ressourcennutzung dynamisch anpassen. Sie können auch die Gesamtkosten für die Ausführung Ihrer Anwendung optimieren, indem Sie für sie die automatische Skalierung festlegen. Sie können die Skalierungseinstellungen für einen Pool bei dessen Erstellung angeben und die Konfiguration jederzeit aktualisieren.

Bei einer Verringerung der Anzahl virtueller Computer müssen unter Umständen auf virtuellen Computern ausgeführte Aufgaben berücksichtigt werden. Mittels einer Richtlinie für die Aufhebung der Zuordnung wird festgelegt, ob ausgeführte Aufgaben beendet werden sollen, um den virtuellen Computer umgehend zu entfernen, oder ob die Aufgaben vor dem Entfernen des virtuellen Computers abgeschlossen werden sollen. Legen Sie die vorgegebene Anzahl virtueller Computer am Ende eines Auftrags auf Null fest, und lassen Sie das Abschließen ausgeführter Aufgaben zu, um die Auslastung zu maximieren.

Sie geben die automatische Skalierung einer Anwendung mithilfe einer Reihe von Skalierungsformeln an. Die Formeln, die zum Ermitteln der Anzahl der virtuellen Aufgabencomputer verwendet werden können, die sich für das nächste Skalierungsintervall im Pool befinden. Sie müssen z. B. eine große Anzahl von Aufgaben übermitteln, die für einen Pool geplant werden. Sie können dem Pool eine Skalierungsformel zuweisen, die die Größe des Pools auf Basis der aktuellen Anzahl der ausstehenden Aufgaben und der Beendigungsrate der Aufgaben angibt. Der Batch-Dienst wertet die Formel in regelmäßigen Abständen aus und ändert die Größe des Pools auf Basis der Workload.

Eine Formel kann auf den folgenden Metriken basieren:

- **Zeitmetriken**: Basieren auf Statistiken, die alle fünf Minuten für die angegebene Anzahl von Stunden erfasst werden.

- **Ressourcenmetriken**: Basieren auf CPU-Auslastung, Bandbreitenauslastung, Speicherauslastung und der Anzahl virtueller Aufgabencomputer.

- **Aufgabenmetriken**: Basieren auf dem Aufgabenstatus (wie etwa „Aktiv“, „Ausstehend“ oder „Abgeschlossen“).

Weitere Informationen zum automatischen Skalieren einer Anwendung finden Sie unter "Konfigurieren der automatischen Skalierung von virtuellen Aufgabencomputern".

>Löschen virtueller Computer
>
>Einzelne virtuelle Computer können aus einem Pool entfernt werden, dies ist jedoch nur sehr selten erforderlich. So können Sie beispielsweise einen virtuellen Computer entfernen, der nicht mehr zuverlässig funktioniert.

## <a name="cert"></a>Zertifikate für Anwendungen

In der Regel müssen Sie Zertifikate verwenden, wenn Sie vertrauliche Informationen verschlüsseln. Zertifikate können auf virtuellen Aufgabencomputern installiert werden. Die verschlüsselten Daten werden in Befehlszeilenparametern an Aufgaben übergeben oder in einer der Ressourcen eingebettet. Zum Entschlüsseln können dann installierte Zertifikate verwendet werden. Ein Beispiel für vertrauliche Informationen ist der Schlüssel für ein Speicherkonto.

Sie verwenden den Vorgang "Zertifikat hinzufügen", um einem Batch-Konto ein Zertifikat hinzuzufügen. Sie können das Zertifikat dann einem neuen oder vorhandenen Pool zuordnen. Wenn ein Zertifikat einem Pool zugeordnet ist, installiert der Batch-Dienst das Zertifikat auf jeden virtuellen Aufgabencomputer im Pool. Der Batch-Dienst installiert die entsprechenden Zertifikate beim Start des virtuellen Aufgabencomputers, bevor jegliche Aufgaben gestartet werden, einschließlich Start- und Auftrags-Manager-Aufgaben.

## <a name="scheduling"></a>Zeitplanungspriorität

Beim Erstellen eines Arbeitselements können Sie diesem eine Priorität zuweisen. Jeder Auftrag unter diesem Arbeitselement wird mit dieser Priorität erstellt. Der Batch-Dienst verwendet die Prioritätswerte des Auftrags, um die Reihenfolge der Auftragsplanung innerhalb eines Kontos zu bestimmen. Die Prioritätswerte reichen von "-1.000" bis "1.000", wobei "-1.000" die niedrigste und "1.000" die höchste Priorität darstellt. Sie können die Priorität eines Auftrags mithilfe des "UpdateJob"-Vorgangs aktualisieren.

Innerhalb desselben Kontos haben Aufträge mit höherer Priorität bei der Planung Vorrang vor Aufträgen mit niedrigerer Priorität. Ein Auftrag mit einem höheren Prioritätswert in einem Konto hat bei der Planung keinen Vorrang vor einem anderen Auftrag mit einem niedrigeren Prioritätswert in einem anderen Konto.

Die Auftragsplanung für die verschiedenen Pools erfolgt unabhängig voneinander. Poolübergreifend wird nicht sichergestellt, dass ein Auftrag mit einer höheren Priorität zuerst geplant wird, wenn der ihm zugeordnete Pool nicht über ausreichend virtuelle Aufgabencomputer verfügt, die im Leerlauf sind. Innerhalb eines Pools verfügen Aufträge mit gleicher Prioritätsstufe über eine identische Planungswahrscheinlichkeit.

## <a name="environment"></a>Umgebungseinstellungen für Aufgaben

Sie können Umgebungseinstellungen angeben, die im Kontext einer Aufgabe verwendet werden können. Umgebungseinstellungen für eine Startaufgabe und unter einem Auftrag ausgeführte Aufgaben werden durch Hinzufügen eines XML-Abschnitts zum Anforderungstext der Vorgänge "Aufgabe hinzufügen" oder "Aufgabe aktualisieren" definiert.

Das folgende Beispiel zeigt die Definition einer Umgebungseinstellung:

Für jede Aufgabe, die unter einem Auftrag geplant ist, wird vom Batch-Dienst eine bestimmte Gruppe von Umgebungsvariablen festgelegt. Die folgende Tabelle enthält die Umgebungsvariablen, die vom Batch-Dienst für alle Aufgaben festgelegt werden.

<table>
  <tr>
   <th>Name der Umgebungsvariablen
   </th>
   <th>
   Beschreibung
   </th>
  </tr>
 <tr>
  <td>
  WATASK_ACCOUNT_NAME
  </td>
  <td>
  Der Name des Kontos, zu dem die Aufgabe gehört.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_WORKITEM_NAME
  </td>
  <td >Der Name des Arbeitselements, zu dem die Aufgabe gehört.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_JOB_NAME
  </td>
  <td >Der Name des Auftrags, zu dem die Aufgabe gehört.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TASK_NAME
  </td>
  <td >Der Name der aktuellen Aufgabe.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_POOL_NAME
  </td>
  <td >Der Name des Pools, in dem die Aufgabe ausgeführt wird.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TVM_NAME
  </td>
  <td >Der Name des virtuellen Aufgabencomputers, auf dem die Aufgabe ausgeführt wird.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TVM_ROOT_DIR
  </td>
  <td >Der vollständige Pfad des Stammverzeichnisses auf dem virtuellen Aufgabencomputer.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_PORTRANGE_LOW WATASK_PORTRANGE_HIGH
</td>
<td>
  Der Portbereich (einschließlich der beiden Enden), den die Aufgabe für die Kommunikation verwenden kann, wenn die Kommunikation innerhalb eines Pools aktiviert ist.
</td>
</table>

**Hinweis**

Diese vom System definierten Variablen können nicht überschrieben werden.

Sie können den Wert von Umgebungseinstellungen mithilfe des Vorgangs "Aufgabe abrufen" abrufen.

## <a name="errorhandling"></a>Fehlerbehandlung

###Behandeln von Aufgabenfehlern
Bei Aufgabenfehlern wird zwischen folgenden Kategorien unterschieden:

- Planungsfehler:
	- Wenn Dateien für die Aufgabe angegeben werden, kann beim Kopieren der Dateien ein Fehler auftreten. Mögliche Gründe: Die Dateien wurden verschoben, das Speicherkonto ist nicht mehr verfügbar, und Ähnliches.
	- In diesem Fall wird für die Aufgabe ein Planungsfehler ausgegeben.
- Anwendungsfehler:
	- Ein Fehler kann auch beim in der Befehlszeile angegebenen Aufgabenprozess auftreten. Der Prozess gilt als nicht erfolgreich, wenn der zurückgegebene Exitcode nicht Null ist.
	- Batch kann so konfiguriert werden, dass die Aufgabe im Falle eines Anwendungsfehlers automatisch mit einer bestimmten Häufigkeit wiederholt wird. 
- Einschränkungsfehler:
	- Eine Einschränkung kann für die maximal zulässige Ausführungsdauer eines Auftrags oder einer Aufgabe angegeben werden. Dies kann hilfreich sein, um eine nicht reagierende Aufgabe zu beenden.
	- Wenn die maximal zulässige Zeitspanne überschritten wurde, wird die Aufgabe als abgeschlossen gekennzeichnet. Als Exitcode wird jedoch `0xC000013A` zurückgegeben, und das Feld „SchedulingError“ wird mit `{ category:“ServerError”, code=“TaskEnded”}` markiert.

###Debuggen von Anwendungsfehlern

Eine Anwendung generiert unter Umständen Diagnosedaten für die Problembehandlung. Oftmals werden Informationen in StdOut- und StdErr-Dateien oder in benutzerdefinierte Dateien geschrieben. In diesen Fällen steht eine API zur Verfügung, um die Dateien unter Angabe der Aufgabe oder des virtuellen Computers abzurufen.

Auch eine Anmeldung bei virtuellen Poolcomputern ist möglich. Eine API gibt die RDP-Datei für einen virtuellen Computer zurück, die dann für die Anmeldung verwendet werden kann.

###Umgang mit Aufgabenfehlern und Problemen

Aufgaben können aus verschiedenen Gründen nicht erfolgreich sein oder unterbrochen werden: Es tritt ein Fehler in der Anwendung auf, der virtuelle Computer, auf dem die Aufgabe ausgeführt wird, wird neu gestartet, oder der virtuelle Computer wird aufgrund einer Änderung der Poolgröße entfernt, und die Richtlinie für die Aufhebung der Zuordnung des virtuellen Computers ist so konfiguriert, dass der virtuelle Computer sofort entfernt wird, ohne auf den Abschluss der Aufgabe zu warten. In diesen Fällen kann der Vorgang automatisch wieder der Warteschlange hinzugefügt und auf einem anderen virtuellen Computer ausgeführt werden.

Außerdem können zeitweilig Probleme auftreten, die dazu führen, dass eine Aufgabe nicht mehr reagiert oder zu lange dauert. Hierzu kann für die Aufgabe eine maximal zulässige Ausführungszeit festgelegt werden. Wird dieser Wert überschritten, unterbricht Batch die Aufgabeanwendung. In diesem Szenario kann die Aufgabe derzeit nicht automatisch erneut der Warteschlange hinzugefügt werden. Das Problem wird aber vom Client erkannt, der dann eine neue Aufgabe übermitteln kann.

###Umgang mit fehlerhaften virtuellen Computern

Jeder virtuelle Computer in einem Pool erhält einen eindeutigen Namen, und der virtuelle Computer, auf dem eine Aufgabe ausgeführt wird, ist in den Metadaten der Aufgabe angegeben. Falls es bei Aufgaben aufgrund eines virtuellen Computers zu Problemen kommt, kann der Client dies erkennen und verdächtige virtuelle Computer aus dem Pool löschen. Wurde auf dem gelöschten virtuellen Computer eine Aufgabe ausgeführt, wird diese automatisch wieder der Warteschlange hinzugefügt und auf einem anderen virtuellen Computer ausgeführt.


<!--Image references-->
[1]: ./media/batch-api-basics/batch-api-basics-01.png

[Azure Batch – Technische Übersicht]: batch-technical-overview.md

<!---HONumber=July15_HO3-->