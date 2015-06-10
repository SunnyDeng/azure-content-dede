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

- [TVM](#taskvm)

- [Pool](#pool)

- [WorkItem](#workitem)

- [Auftrag](#job)

- [Task](#task)

	- [Aufgabe starten](#starttask)
	
	- [Auftrag ManagerTask](#jobmanagertask)

### <a name="account"></a>Konto

Ein Batch-Konto ist eine eindeutig identifizierte Entität innerhalb des Batch-Diensts. Die gesamte Verarbeitung erfolgt über ein Batch-Konto. Beim Ausführen von Operationen mit dem Batch-Dienst benötigen Sie den Kontonamen und den Schlüssel für das Konto. Zum Erstellen einer Batch-Kontos finden Sie im Batch Kontoabschnitt [Übersicht über die Azure-Batch][].


### <a name="taskvm"></a>TVM

Ein virtueller Aufgabencomputer (Virtual Task Machine, TVM) ist ein virtueller Azure-Computer, der für Ihre Anwendung für eine bestimmte Arbeitsauslastung vorgesehen ist. Die Größe eines virtuellen Aufgabencomputers bestimmt die Anzahl der CPU-Kerne, die Speicherkapazität und die lokale Dateisystemgröße, die dem virtuellen Aufgabencomputer zugeordnet werden. Eine TVM kann klein und Groß oder extralarge VM sein [Größen virtueller Computer und Cloud-Dienst für Azure](http://msdn.microsoft.com/library/dn197896.aspx).

Die von einem virtuellen Aufgabencomputer ausführbaren Programme umfassen ausführbare Dateien (EXE), Befehlsdateien (CMD), Batchdateien (BAT) und Skriptdateien. Ein virtueller Aufgabencomputer verfügt außerdem über die folgenden Attribute:

- Dateisystemordner aufgabenspezifischen und freigegeben sind. Eine Ordner-Struktur und die Umgebungsvariablen werden auf jeder Pool virtueller Computer erstellt. Die folgende Ordnerstruktur wird mit "Freigabe" für Anwendungen und Daten, die gemeinsam von Aufgaben sowie einen Ordner für jede Aufgabe erstellt.

![][1]

- Stdout.txt- und stderr.txt-Dateien, die in einen aufgabenspezifischen Ordner geschrieben werden

- Umgebungsvariablen für die Verarbeitung

- Für die Zugriffssteuerung konfigurierte Firewall-Einstellungen

>Zugriff auf virtuelle Computer
>
>Wenn Zugriff auf einen virtuellen Computer z. B. Debuggen erforderlich ist, kann die RDP-Datei der dann verwendet werden kann, auf den virtuellen Computer über Remotedesktop abgerufen werden.


### <a name="pool"></a>Pool

Ein Pool besteht aus einer Sammlung von virtuellen Aufgabencomputern, auf denen Ihre Anwendung ausgeführt wird. Der Pool kann von Ihnen oder automatisch vom Batch-Dienst erstellt werden, wenn Sie die zu erbringende Arbeitsleistung angeben. Sie können einen Pool erstellen und verwalten, der den Anforderungen Ihrer Anwendung entspricht. Ein Pool kann nur von dem Batch-Konto verwendet werden, in dem er erstellt wurde. Ein Batch-Konto kann über mehrere Pools verfügen.

Neben der Azure Compute Azure Batch-Pools zu erstellen; Batch-Pools bieten umfangreiche Zuordnung, Anwendung und Installation von Data, Datenverschiebung, Systemüberwachung und flexible Skalierung des virtuellen Computers.

Jedem virtuellen Aufgabencomputer (TVM), der einem Pool hinzugefügt wird, ist ein eindeutiger Name und eine zugehörige IP-Adresse zugewiesen. Wenn ein virtueller Aufgabencomputer aus einem Pool entfernt wird, gehen die am Betriebssystem, an den lokalen Dateien, seinem Namen und seiner IP-Adresse vorgenommenen Änderungen verloren. Wenn eine TVM einen Pool verlässt, endet ihre Lebensdauer.

Sie können einen Pool derart konfigurieren, dass die Kommunikation zwischen den darin enthaltenen virtuellen Aufgabencomputern gestattet ist. Wenn die Kommunikation innerhalb eines Pools für einen Pool angefordert wird, aktiviert der Batch-Dienst für jeden virtuellen Aufgabencomputer im Pool Ports, die über 1100 liegen. Jeder virtuelle Aufgabencomputer im Pool ist so konfiguriert, dass eingehende Verbindungen nur für diesen Portbereich und ausschließlich von anderen virtuellen Aufgabencomputern im Pool zugelassen oder auf diesen beschränkt werden. Wenn für Ihre Anwendung keine Kommunikation zwischen virtuellen Aufgabencomputern erforderlich ist, kann der Batch-Dienst potenziell eine große Anzahl von virtuellen Aufgabencomputern in verschiedenen Clustern oder Rechenzentren für den Pool reservieren, um die Möglichkeit zur parallelen Verarbeitung zu erhöhen.

Wenn Sie einen Pool erstellen, können Sie die folgenden Attribute angeben:

- Die **von VMs** im Pool.
	- Die entsprechende VM-Größe muss ausgewählt werden, abhängig von den Eigenschaften und die Anforderungen der Anwendung oder Anwendungen, die auf dem virtuellen Computer verwendet werden. Normalerweise wird die VM-Größe vorausgesetzt, dass eine Aufgabe gleichzeitig auf dem virtuellen Computer ausgeführt wird, entnommen werden; beispielsweise, ob die Anwendung Multithread ist und wie viel Arbeitsspeicher dafür erforderlichen Größe des virtuellen Computers am geeignete und kostengünstige bestimmt wird. Es ist möglich, mehrere Vorgänge zugewiesen sind und mehrere Anwendungsinstanzen, werden parallel ausgeführt werden, in diesem Fall eine umfangreichere VM in der Regel ausgewählt werden – finden Sie weiter unten auf "maximale Vorgänge pro VM". 
	- Aller virtuellen Computer in einem Pool müssen die gleiche Größe aufweisen. Wenn andere Anwendungen sind mit anderen Systemanforderungen und/oder verschiedene Load ausgeführt werden, sollten separate Pools erstellt werden.
	- Alle Größen für Cloud-Dienst können für einen Pool, mit Ausnahme von A0 konfiguriert werden.

- Die Betriebssystemfamilie und die Version, die auf den virtuellen Computern ausgeführt wird.
	- Wie bei der Worker-Rollen können die Betriebssystemfamilie und die Version des Betriebssystems konfiguriert werden.
	- Die Betriebssystemfamilie bestimmt auch, welche Versionen von .NET mit dem Betriebssystem installiert werden.
	- Wie mit Worker-Rollen für die Betriebssystemversion, sollten "*" verwendet werden, damit der VMs werden automatisch aktualisiert und es keine Arbeit erforderlich, um neue Versionen gerecht wird. Für eine bestimmte Betriebssystemversion Entnahme der wichtigsten Anwendungsfall wird sichergestellt, dass die Anwendungskompatibilität geführte so Abwärtskompatibilität Tests ausgeführt werden, bevor Sie die Version aktualisiert werden. Sobald überprüft die Version des Betriebssystems für den Pool aktualisiert werden kann und das neue Betriebssystemabbild installiert – alle ausgeführten Tasks unterbrochen und wieder in die Warteschlange gestellt.

- Die vorgegebene Anzahl virtueller Computer, die für den Pool verfügbar sein sollen.

- Die Skalierungsrichtlinie für den Pool. Neben der Anzahl der virtuellen Computer können Sie auch eine automatische Skalierung Formel für jeden Pool angeben. Batch-Dienst führt die Formel ein, um die Anzahl der virtuellen Computer basierend auf den Pool und das Workitem-Statistiken anzupassen.

- Planen der Konfiguration
	- Die Standardkonfiguration ist für einen Vorgang zu einem beliebigen Zeitpunkt auf einen Pool virtueller Computer ausgeführt werden, aber es gibt Szenarien, in denen es vorteilhaft ist, haben mehrere Tasks gleichzeitig auf einem virtuellen Computer ausgeführt werden kann. Ein Beispiel ist die VM-Nutzung zu erhöhen, wenn eine Anwendung für e/a warten muss. mehr als eine Anwendung ausführen, erhöhen Sie CPU-Auslastung. Ein weiteres Beispiel ist zum Reduzieren der Anzahl der VM im Pool. Dies könnte für große Verweis Datasets erforderlichen Datenkopien reduzieren. Wenn ein A1 die richtige Größe für die Anwendung würde, könnte ein A4 ausgewählt werden und legen Sie dann die Konfiguration bis zu 8 Aufgaben gleichzeitig ausführen jeder Nutzung einen Kern.
	- Die Konfiguration "Max. Aufgaben pro virtueller Maschine" bestimmt die maximale Anzahl von Tasks, die parallel ausgeführt werden können.
	- "Fill Richtlinie" können auch angegeben werden die bestimmt, ob Batch füllt erste des virtuellen Computers oder gibt an, ob Aufgaben auf alle virtuellen Computer verteilt sind.
 
- Der Kommunikationsstatus der virtuellen Computer im Pool.
 	- In ein großer Teil des Szenarios Aufgaben unabhängig voneinander betrieben werden und müssen nicht für die Kommunikation mit anderen Aufgaben, aber es sind einige Anwendungen, in denen Aufgaben (z. B. Anwendungen, die mit MPI) kommuniziert.
	- Dient zum Konfigurieren der zugrunde liegenden Infrastruktur und Auswirkungen Platzierung von VM Konfiguration, die steuert, ob der VMs zu kommunizieren, können ist.

- Die Startaufgabe für virtuelle Aufgabencomputer im Pool.

Wenn Sie einen Pool erstellen, können Sie das Speicherkonto angeben, das dem Pool zugewiesen werden soll. Der Batch-Dienst ordnet virtuelle Aufgabencomputer der Rechenzentren mit besserer Netzwerkkonnektivität und Bandbreitenkapazität dem angegebenen Speicherkonto zu. Dadurch können Arbeitslasten effizienter auf Daten zugreifen.

### <a name="workitem"></a>WorkItem

Ein Arbeitselement gibt an, wie die Berechnung auf den virtuellen Aufgabencomputern in einem Pool ausgeführt wird.

- Eine Arbeitsaufgabe kann einen oder mehrere Aufträge zugeordnet haben. Ein optionaler Zeitplan kann für eine Arbeitsaufgabe angegeben werden, in diesem Fall ein Auftrag erstellt wird, für jedes Vorkommen des Zeitplans. Wenn kein Zeitplan, für die Arbeit auf Anforderung angegeben ist wird ein Auftrag sofort erstellt.
- Die Arbeitsaufgabe gibt den Pool, auf dem die Arbeit ausgeführt wird. Der Pool kann eine vorhandene, bereits erstellte Pool, die viele Arbeitsaufgaben verwendet jedoch ein Pool kann auch erstellt werden, für jeden Auftrag, der die Arbeitsaufgabe zugeordnet oder für alle Aufträge, die der Arbeitsaufgabe zugeordnet.
- Eine optionale Priorität kann angegeben werden. Wenn eine Arbeitsaufgabe mit einer höheren Priorität als anderen Arbeitsaufgaben noch in Bearbeitung gesendet wird, erhalten die höheren Priorität Arbeitsaufgaben in die Warteschlange vor der niedrigeren Priorität Arbeitsaufgaben eingefügt. Bereits ausgeführte Tasks mit niedrigerer Priorität werden nicht erlaubt.
- Einschränkungen können angegeben werden, die auf den zugeordneten Auftrag oder die Aufträge angewendet werden.
	- Für die Aufträge kann eine maximale Wallclock Zeit festgelegt werden. Wenn die Aufträge, die länger als die angegebene maximale Wallclock Zeit ausgeführt wird, werden das Projekt und alle zugehörigen Aufgaben beendet werden.
	- Azure-Batch kann Aufgaben erkennen, die fehlschlagen und die Tasks zu wiederholen. Die Standardeinstellung, die maximale Anzahl von Wiederholungen der Aufgabe als Einschränkung angegeben werden kann, wie z. B. angeben, der eine Aufgabe immer wiederholt oder nie wiederholt. Wiederholen einer Aufgabe bedeutet, dass die Aufgabe erneut in der Warteschlange und erneut ausgeführt werden wird.
- Aufgaben für die Arbeit Element Auftrag ausgeführt werden können angegeben werden vom Client auf die gleiche Weise, die die Arbeitsaufgabe erstellt wurde, aber eine Auftrags-Manager-Aufgabe kann auch angegeben werden. Der Task eine Auftrags-Manager verwendet die Batch-API und enthält den Code zum Erstellen der erforderlichen Aufgaben für einen Auftrag mit der Aufgabe eines des Pools virtueller Computer ausgeführt wird. Die Auftrags-Manager-Aufgaben behandelt, insbesondere durch Batch – es in die Warteschlange gestellt werden, sobald der Auftrag erstellt wird und neu gestartet wird, wenn aus irgendeinem Grund ein Fehler auftritt. Auftrags-Manager ist für Arbeitsaufgaben mit einer zugeordneten Zeitplan erforderlich, wie es ist die einzige Möglichkeit, die Tasks zu definieren, bevor der Auftrag instanziiert wird.

### <a name="job"></a>Auftrag

Ein Auftrag ist eine aktive Instanz eines Arbeitselements und besteht aus einer Sammlung von Aufgaben. Der Batch-Dienst instanziiert einen Auftrag auf Basis der Konfiguration des Arbeitselements. Der Auftrag verwendet virtuelle Aufgabencomputer aus dem Pool, der dem Arbeitselement zugeordnet ist.

### <a name="task"></a>Aufgabe

Eine Aufgabe ist eine Einheit der Berechnung, die einem Auftrag zugeordnet ist und auf einem virtuellen Aufgabencomputer ausgeführt wird. Aufgaben, die mit einem virtuellen Computer für die Ausführung zugewiesen sind, oder werden in Warteschlangen eingereiht, bis ein virtueller Computer wieder verfügbar ist. Eine Aufgabe verwendet die folgenden Ressourcen:

- Das Programm, das im Arbeitselement angegeben wurde.

- Die Ressourcendateien, die die zu verarbeitenden Daten enthalten. Diese Dateien werden automatisch aus dem BLOB-Speicher auf den virtuellen Aufgabencomputer kopiert. Weitere Informationen finden Sie unter Dateien und Verzeichnisse.

- Die Umgebungseinstellungen, die von dem Programm benötigt werden. Weitere Informationen finden Sie unter den Umgebungseinstellungen für Aufgaben.

- Die Einschränkungen, unter denen die Berechnung erfolgen soll. Beispielsweise die maximale Zeit, in der die Aufgabe ausgeführt werden darf, die maximale Anzahl, mit der eine Aufgabe bei der fehlerhaften Ausführung wiederholt werden sollte und die maximal zulässige Dauer, die Dateien im Arbeitsverzeichnis beibehalten werden.

Zusätzlich zu Aufgaben, die Sie zur Berechnung auf einem virtuellen Aufgabencomputer definieren können, stehen die folgenden speziellen Aufgaben zur Verfügung, die vom Batch-Dienst bereitgestellt werden:

- [Aufgabe starten](#starttask)

- [Auftrags-Manager-Aufgabe](#jobmanagertask)

#### <a name="starttask"></a>Aufgabe starten

Der Pool eine Startaufgabe zuordnen, um das Betriebssystem der virtuellen Computer in einem Pool zu konfigurieren. Das Installieren der Software und das Starten von Hintergrundprozessen sind einige der Aktionen, die von Startaufgaben ausgeführt werden können. Die Start-Aufgabe ausgeführt wird, jedes Mal, wenn ein virtueller Computer startet, solange es im Pool befindet.

Als kann bei jeder Aufgabe im Batch eine Liste der Dateien im Azure-Speicher zusätzlich zu einer Befehlszeile angegeben werden, die als Batch ausgeführt wird. Azure-Batch wird zuerst kopieren Sie die Dateien aus dem Azure-Speicher, und führen Sie die Befehlszeile. Für einen Pool Start Task kann die Dateiliste enthält i. d. r. die Anwendungsdateien oder das Paket, jedoch auch auf Verweisdaten, die durch alle Aufgaben, die auf des Pools virtueller Computer verwendet werden. Die Befehlszeile konnte PowerShell-Skript oder Robocopy, z. B. zum Kopieren von Anwendungsdateien in den Ordner "freigegeben" ausgeführt werden; Es kann auch eine MSI-Datei ausgeführt.

Normalerweise ist es wünschenswert, dass Batch an, dass der Start Task abgeschlossen, und gehen Sie dann den virtuellen Computer kann Aufgaben zugewiesen werden, dies ist jedoch konfigurierbar.

Fällt eine Startaufgabe für einen Pool virtueller Computer, der Status des virtuellen Computers aktualisiert den Fehler an, und der virtuelle Computer wird nicht für Aufgaben zugewiesen werden. Ein Start-Task kann fehlschlagen, wenn es ein Problem mit dem Kopieren der Dateien für die Startaufgabe angegeben oder des Task-Startvorgangs ungleich NULL zurück.

Die Tatsache, dass alle Informationen zum Konfigurieren des virtuellen Computers und installieren Sie die Anwendungen deklariert ist bedeutet, dass es sich bei Erhöhen der Anzahl von virtuellen Computern in einem Pool so einfach wie die Angabe der neuen erforderlich ist. Stapel enthält alle Informationen, die erforderlich sind, konfigurieren Sie die VM und Aufgaben zu akzeptieren.

Start-Aufgabe wird durch eine JSON-Abschnitt der Anforderungstext für den Pool hinzufügen-Vorgang hinzufügen definiert. Das folgende Beispiel zeigt eine Basisdefinition einer Startaufgabe:

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

1. Hochladen der Dateien, die Sie in Ihrem verteilten rechenszenario auf Azure-Speicherkonten verwenden möchten. Diese Dateien müssen im Speicherkonto sein, damit der Batch-Dienst darauf zugreifen kann. Der Batch-Dienst lädt sie beim Ausführen der Aufgabe in eine TVM.

2. Laden die abhängigen Binärdateien in das Speicherkonto hoch. Die Binärdateien enthalten das Programm, das durch die Aufgabe und die abhängigen Assemblys ausgeführt wird. Auf diese Dateien muss auch aus dem Speicher zugegriffen werden und sie werden auf die TVM geladen.

3. erstellen Sie 3. einen Pool von TVMs. Sie können die Größe des zu verwendenden virtuellen Aufgabencomputers bei der Erstellung eines Pools zuweisen. Wenn eine Aufgabe ausgeführt wird, wird ihr eine TVM aus diesem Pool zugewiesen.

4. Erstellen eines WorkItems. Beim Erstellen eines Arbeitselements wird automatisch ein Auftrag erstellt. Mit einem Arbeitselement können Sie einen Auftrag von Aufgaben verwalten.

5. das Workitem Aufgaben hinzugefügt. Jede Aufgabe verwendet das von Ihnen hochgeladene Programm, um Informationen aus einer Datei zu verarbeiten, die Sie ebenfalls hochgeladen haben.

6. überwachen Sie 6. die Ergebnisse der Ausgabe.

## <a name="files"></a>Dateien und Verzeichnisse

Jede Aufgabe verfügt über ein Arbeitsverzeichnis, unter dem null oder mehr Verzeichnisse und Dateien für das Speichern des von einer Aufgabe ausgeführten Programms, der von einer Aufgabe verarbeiteten Daten und der Ausgabe der von einer Aufgabe ausgeführten Verarbeitung erstellt werden. Diese Verzeichnisse und Dateien sind dann während der Ausführung eines Auftrags für andere Aufgaben verfügbar. Alle Aufgaben, Dateien und Verzeichnisse auf einer TVM gehören einem einzelnen Benutzerkonto.

Der Batch-Dienst stellt einen Teil des Dateisystems auf einem virtuellen Aufgabencomputer als Stammverzeichnis bereit. Das Stammverzeichnis der TVM ist Aufgabe über die Umgebungsvariable WATASK_TVM_ROOT_DIR verfügbar. Weitere Informationen zur Verwendung von Umgebungsvariablen finden Sie unter den Umgebungseinstellungen für Aufgaben.

Das Stammverzeichnis enthält die folgenden Unterverzeichnisse:

- **Aufgaben** – Hier werden alle Dateien gespeichert, die zu Aufgaben gehören, die auf die TVM ausgeführt. Für jede Aufgabe erstellt der Batch-Dienst ein Arbeitsverzeichnis, mit der eindeutige Pfad in Form von % WATASK_TVM_ROOT_DIR%/tasks/workitemName/jobName/taskName/. Dieses Verzeichnis bietet Lese-/Schreibzugriff auf die Aufgabe. Die Aufgabe kann Dateien unter diesem Verzeichnis erstellen, lesen, aktualisieren und löschen, und dieses Verzeichnis bleibt auf Basis der für diese Aufgabe angegebenen "RetentionTime"-Einschränkung erhalten.

- **Shared** – hier ist ein freigegebenes Verzeichnis für alle Aufgaben unter dem Konto. Auf die TVM ist das freigegebene Verzeichnis % WATASK_TVM_ROOT_DIR%/shared. Dieses Verzeichnis bietet Lese-/Schreibzugriff auf die Aufgabe. Die Aufgabe kann Dateien unter diesem Verzeichnis erstellen, lesen, aktualisieren und löschen.

- **Start** – dieser Speicherort wird von einem Start Task als Arbeitsverzeichnis verwendet. Alle Dateien, die vom Batch-Dienst heruntergeladen werden, um die Startaufgabe zu starten, werden ebenfalls unter diesem Verzeichnis gespeichert. Auf die TVM ist das Startverzeichnis % WATASK_TVM_ROOT_DIR%/start. Die Aufgabe kann Dateien unter diesem Verzeichnis erstellen, lesen, aktualisieren und löschen, und dieses Verzeichnis kann von Startaufgaben dazu verwendet werden, das Betriebssystem zu konfigurieren.

Wenn ein virtueller Aufgabencomputer aus einem Pool entfernt wird, werden alle auf dem virtuellen Aufgabencomputer gespeicherten Dateien entfernt.

## <a name="lifetime"></a>Pool und VM-Lebensdauer

Eine grundlegende Entwurfsentscheidung ist bei der Erstellung von Pools und wie lange die VMs verfügbar gehalten werden.

Eine extrem konnte ein Pool für jeden Auftrag erstellt werden, wenn der Auftrag gesendet wird, und des virtuellen Computers entfernt, während der Ausführung von Aufgaben. Dies maximiert Auslastung des virtuellen Computers werden nur zugeordnet, wenn absolut notwendig und Herunterfahren, sobald diese werden im Leerlauf befindet. Es bedeutet, dass der Auftrag warten muss, belegt der VMs zugeordnet werden, es ist wichtig zu beachten, dass Aufgaben an die VMs geplant werden, sobald sie einzeln zur Verfügung stehen, und die Start-Aufgabe abgeschlossen wurde. d. h. wartet Batch nicht, bis alle virtuellen Computer in einem Pool verfügbar sind, wie die, die zu einer schlechten Auslastung führen würde.

Wenn Aufträge starten ist sofort ausführen die Priorität, ein Pool erstellt werden soll, und vor dem Senden des Auftrags, der VM zur Verfügung. Die Aufgaben können sofort starten jedoch des virtuellen Computers im Leerlauf, warten auf Aufgaben, je nach Auslastung werden konnte.

Eine gemeinsame Muster für, wenn vorhanden ist, dass eine Variable Menge an laufenden Auslastungstest besteht darin, einen Pool, der mehrere Aufträge, aber Skalierung nach oben oder unten die Anzahl der virtuellen Computer entsprechend gesendet werden; dazu können reaktiv oder proaktiv Load vorausgesagt werden kann.

## <a name="scaling"></a>Skalieren von Anwendungen

Die Größe Ihrer Anwendung kann problemlos automatisch erhöht oder verringert werden, um der erforderlichen Berechnung zu entsprechen. Sie können die Anzahl der virtuellen Aufgabencomputer in einem Pool entsprechend der aktuellen Statistik zur Arbeitsauslastung und Ressourcennutzung dynamisch anpassen. Sie können auch die Gesamtkosten für die Ausführung Ihrer Anwendung optimieren, indem Sie für sie die automatische Skalierung festlegen. Sie können die Skalierungseinstellungen für einen Pool bei dessen Erstellung angeben und die Konfiguration jederzeit aktualisieren.

Eine Verringerung der Anzahl von VMs möglicherweise Tasks auf VMs, die berücksichtigt werden müssen. Eine Richtlinie für die Aufhebung der Zuordnung wird angegeben, die bestimmt, ob die ausgeführte Aufgaben beendet wurden, entfernen Sie sofort die VM oder die Zulässigkeit Aufgaben abgeschlossen sind, bevor der VMs entfernt werden. Festlegen des virtuellen Computers nach unten, um der vorgegebene Anzahl 0 (null) am Ende eines Auftrags jedoch ermöglicht ausgeführten Tasks zu beenden, Maximierung der Nutzung.

Sie geben die automatische Skalierung einer Anwendung mithilfe einer Reihe von Skalierungsformeln an. Die Formeln, die zum Ermitteln der Anzahl der virtuellen Aufgabencomputer verwendet werden können, die sich für das nächste Skalierungsintervall im Pool befinden. Sie müssen z. B. eine große Anzahl von Aufgaben übermitteln, die für einen Pool geplant werden. Sie können dem Pool eine Skalierungsformel zuweisen, die die Größe des Pools auf Basis der aktuellen Anzahl der ausstehenden Aufgaben und der Beendigungsrate der Aufgaben angibt. Der Batch-Dienst wertet die Formel in regelmäßigen Abständen aus und ändert die Größe des Pools auf Basis der Arbeitsauslastung.

Eine Formel kann auf den folgenden Metriken basieren:

- **Zeitmetriken** – basierend auf fünf Minuten in die angegebene Anzahl von Stunden erfasst.

- **Ressourcenmetriken** – basierend auf der CPU-Nutzung, Bandbreitennutzung, Speicherauslastung und Anzahl von TVMs.

- **Aufgabe Metriken** – basierend auf den Status von Aufgaben, wie z. B. aktiv, Ausstehend, und abgeschlossen.

Weitere Informationen zum automatischen Skalieren einer Anwendung finden Sie unter "Konfigurieren der automatischen Skalierung von virtuellen Aufgabencomputern".

>Löschen des virtuellen Computers
>
>Es ist nicht oft erforderlich, aber es ist möglich, einzelne VM aus einem Pool entfernen angeben. Es ist ein virtueller Computer, der Verdacht auf kleiner konnte zuverlässige es, z. B. entfernt werden.

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

###Task-Fehlerbehandlung
Taskfehler fallen in die folgenden Kategorien:

- Planen von Fehlern:
	- Wenn Dateien für den Vorgang angegeben werden, kann die Kopie von einer oder mehreren Dateien fehlschlagen. Mögliche Gründe hierfür sind die Dateien verschoben haben, das Speicherkonto, das nicht mehr usw. zur Verfügung.
	- In diesem Fall wird "Scheduling Fehler" für den Vorgang festgelegt.
- Anwendungsfehler:
	- In der Befehlszeile angegebene Task Prozess kann auch fehlschlagen. Der Prozess gilt als fehlgeschlagen gilt, wenn ein NULL-Exitcode zurückgegeben wird.
	- Für Anwendungsfehler ist es möglich, Batch, um den Vorgang bis zu einer angegebenen Anzahl fehlgeschlagener automatisch erneut zu konfigurieren. 
- Fehler bei der Einschränkung:
	- Eine Einschränkung kann für die maximale Zeitspanne angegeben werden, die für ein Projekt oder eine Aufgabe ausführen kann. Das kann hilfreich sein, eine Aufgabe zu beenden, die nicht reagiert hat.
	- Wenn die maximale Zeitspanne wurde überschritten, die Aufgabe als abgeschlossen gekennzeichnet aber die Exit-Code wird wird als markiert `0xC000013A` und SchedulingError-Feld wird als gekennzeichnet `{ category:“ServerError”, code=“TaskEnded”}`.

###Debuggen von Anwendungsfehlern

Diagnose der Problembehandlung verwendet werden kann, kann eine Anwendung generieren. Häufig Anwendungen werden Informationen in Stdout und Stderr Dateien oder Ausgabe für benutzerdefinierte Dateien schreiben. In diesen Fällen ist eine API bereitgestellt, um die Dateien, durch Angeben der Task oder die VM erhalten.

Es ist auch möglich, an den Pool virtueller Computer anmelden. Eine API gibt die RDP-Datei für eine virtuelle Maschine, die dann verwendet werden, können zur Anmeldung auf dem virtuellen Computer.

###Taskfehler und Probleme damit problemlos

Vorgänge können fehlschlagen oder Gründen unterbrochen werden. Die Taskanwendung selbst schlägt möglicherweise fehl, der virtuellen Computer, auf dem der Task ausgeführt wird, ruft gestartet oder des virtuellen Computers wird durch einen Pool-Größe mit der Richtlinieneinstellung Aufhebung der Zuordnung des virtuellen Computers sofort zu entfernen, ohne warten auf den Abschluss der Aufgabe entfernt. In allen Fällen wird der Vorgang kann automatisch erneut in der Warteschlange als Batch und auf einem anderen virtuellen Computer ausführen.

Es ist auch möglich, für ein intermittierender Fehler verursachen eine Aufgabe Einfrieren bzw. deren Ausführung zu lange dauern. Die maximale Ausführungszeit kann für einen Vorgang festgelegt und überschrittenen Batch die Aufgabe Anwendung unterbrochen wird. Derzeit, automatische erneute queuing ist nicht möglich, für diesen Fall, aber die Groß-/Kleinschreibung vom Client eine neue Aufgabe vor kann erkannt werden kann.

###Damit problemlos "Schlechten" VM

Jeder virtuelle Computer in einem Pool erhält einen eindeutigen Namen und die VM auf dem eine Aufgabe ausgeführt wird, die in den Task-Metadaten enthalten. In dem Fall, in dem es eine virtuellen Maschine, die aus irgendeinem Grund Aufgaben Fehler verursacht, dann ist dies kann bestimmt werden, vom Client und verdächtigen VM aus dem Pool gelöscht. Wenn ein Task auf dem virtuellen Computer ausgeführt wurde, das gelöscht wurde, wird dann automatisch wieder in die Warteschlange gestellt und auf einem anderen virtuellen Computer ausgeführt.


<!--Image references-->
[1]: ./media/batch-api-basics/batch-api-basics-01.png

[Übersicht über die Azure-Batch]: batch-technical-overview.md

<!---HONumber=GIT-SubDir-->