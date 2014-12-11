
<properties title="API basics for Azure Batch" pageTitle="API-Grundlagen für Azure Batch" description="Concepts to introduce developers to the Azure Batch APIs and Batch service" metaKeywords="" services="batch" solutions="" documentationCenter=".NET" authors="yidingz, karran.batta" videoId="" scriptId="" manager="timlt" />

<tags ms.service="batch" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="big-compute" ms.date="10/27/2014" ms.author="yidingz, karran.batta" />


<!--The next line, with one pound sign at the beginning, is the page title--> 
# API-Grundlagen für Azure Batch

Der Azure Batch-Dienst bietet ein Framework für die Auftragsplanung zur skalierbaren und verteilten Berechnung. Der Batch-Dienst verwaltet einen Satz virtueller Computer, die über verschiedene Cluster und Rechenzentren in Azure verteilt sind. Der Batch-Dienst erreicht die verteilte Berechnung durch Ausführen eines oder mehrerer Programme. Die Ausführung erfolgt entweder nach Bedarf oder zu einem geplanten Zeitpunkt für eine bestimmte Sammlung dieser virtuellen Computer. Der Batch-Dienst verwaltet diese virtuellen Computer, damit Ihre Berechnungsaufgaben gemäß den Ressourcenanforderungen, den Spezifikationen und den von Ihnen bereitgestellten Einschränkungen ausgeführt werden.

Der Batch-Dienst sorgt dafür, dass Sie für Message Queuing, Planung, Zuweisung und Verwaltung von Computerressourcen keinen Code mehr schreiben müssen. Auf diese Weise können Sie sich auf die jeweilige Anwendung konzentrieren und müssen sich nicht um die Komplexität der Auftragsplanung und Ressourcenverwaltung für die zugrunde liegende Plattform kümmern. Dadurch kann der Batch-Dienst auch den Speicherort dieser Aufträge sowie deren Zugriff auf die zu verarbeitenden Daten optimieren.

Im folgenden werden einige der Szenarien aufgeführt, die Sie mithilfe des Batch-Diensts ermöglichen können:

- Rechenintensive parallele Verarbeitung

- Tägliche Bereinigung von Dateien

- Batchverarbeitung

Weitere Informationen zum Batch-Dienst finden Sie in den folgenden Abschnitten:

- [Ressourcen des Batch-Diensts](#resource)

- [Workflow des Batch-Diensts](#workflow)

- [Dateien und Verzeichnisse](#file)

- [Skalieren von Anwendungen](#scaling)

- [Zertifikate für Anwendungen](#cert)

- [Zeitplanungspriorität](#scheduling)

- [Umgebungseinstellungen für Aufgaben](#environment)

## <a name="resource"></a> Ressourcen des Batch-Diensts

Wenn Sie den Batch-Dienst verwenden, nutzen Sie die folgenden Ressourcen:

- [Konto](#account)

- [Virtueller Aufgabencomputer](#taskvm)

- [Pool](#pool)

- [Arbeitselement](#workitem)

- [Job](#job)

- [Aufgabe](#task)

### <a name="account"></a>Konto

Ein Batch-Konto ist eine eindeutig identifizierte Entität innerhalb des Batch-Diensts. Die gesamte Verarbeitung erfolgt über ein Batch-Konto. Beim Ausführen von Operationen mit dem Batch-Dienst benötigen Sie den Kontonamen und den Schlüssel für das Konto. Derzeit müssen Sie sich an das Azure Batch-Team wenden, um ein neues Konto erstellen zu lassen. Nachdem das Konto erstellt wurde, erhalten Sie einen Schlüssel für das Konto.

### <a name="taskvm"></a>Virtueller Aufgabencomputer

Ein virtueller Aufgabencomputer (Virtual Task Machine, TVM) ist ein virtueller Azure-Computer, der für Ihre Anwendung für eine bestimmte Arbeitsauslastung vorgesehen ist. Die Größe eines virtuellen Aufgabencomputers bestimmt die Anzahl der CPU-Kerne, die Speicherkapazität und die lokale Dateisystemgröße, die dem virtuellen Aufgabencomputer zugeordnet werden. Ein virtueller Aufgabencomputer kann ein kleiner, großer oder sehr großer virtueller Computer sein, wie unter [Größen virtueller Computer und Cloud-Dienste für Azure](http://msdn.microsoft.com/de-de/library/dn197896.aspx) beschrieben.

Die von einem virtuellen Aufgabencomputer ausführbaren Programme umfassen ausführbare Dateien (EXE), Befehlsdateien (CMD), Batchdateien (BAT) und Skriptdateien. Ein virtueller Aufgabencomputer verfügt außerdem über die folgenden Attribute:

- Dateisystemordner, die sowohl aufgabenspezifisch als auch freigegeben sind

- Stdout.txt- und stderr.txt-Dateien, die in einen aufgabenspezifischen Ordner geschrieben werden

- Umgebungsvariablen für die Verarbeitung

- Für die Zugriffssteuerung konfigurierte Firewall-Einstellungen

### <a name="pool"></a>Pool

Ein Pool besteht aus einer Sammlung von virtuellen Aufgabencomputern, auf denen Ihre Anwendung ausgeführt wird. Der Pool kann von Ihnen oder automatisch vom Batch-Dienst erstellt werden, wenn Sie die zu erbringende Arbeitsleistung angeben. Sie können einen Pool erstellen und verwalten, der den Anforderungen Ihrer Anwendung entspricht. Ein Pool kann nur von dem Batch-Konto verwendet werden, in dem er erstellt wurde. Ein Batch-Konto kann über mehrere Pools verfügen.

Jedem virtuellen Aufgabencomputer, der einem Pool hinzugefügt wird, ist ein eindeutiger Name und eine zugehörige IP-Adresse zugewiesen. Wenn ein virtueller Aufgabencomputer aus einem Pool entfernt wird, gehen die am Betriebssystem, an den lokalen Dateien, seinem Namen und seiner IP-Adresse vorgenommenen Änderungen verloren. Wenn ein virtueller Aufgabencomputer einen Pool verlässt, endet seine Gültigkeitsdauer.

Sie können einen Pool derart konfigurieren, dass die Kommunikation zwischen den darin enthaltenen virtuellen Aufgabencomputern gestattet ist. Wenn die Kommunikation innerhalb eines Pools für einen Pool angefordert wird, aktiviert der Batch-Dienst für jeden virtuellen Aufgabencomputer im Pool Ports, die über 1100 liegen. Jeder virtuelle Aufgabencomputer im Pool ist so konfiguriert, dass eingehende Verbindungen nur für diesen Portbereich und ausschließlich von anderen virtuellen Aufgabencomputern im Pool zugelassen oder auf diesen beschränkt werden. Wenn für Ihre Anwendung keine Kommunikation zwischen virtuellen Aufgabencomputern erforderlich ist, kann der Batch-Dienst potenziell eine große Anzahl von virtuellen Aufgabencomputern in verschiedenen Clustern oder Rechenzentren für den Pool reservieren, um die Möglichkeit zur parallelen Verarbeitung zu erhöhen.

Wenn Sie einen Pool erstellen, können Sie die folgenden Attribute angeben:

- Die Größe der virtuellen Aufgabencomputer im Pool.

- Die Betriebssystemfamilie und -version, die auf den virtuellen Aufgabencomputern ausgeführt werden.

- Die vorgegebene Anzahl virtueller Aufgabencomputer, die für den Pool verfügbar sein sollte.

- Die Skalierungsrichtlinie für den Pool.

- Der Kommunikationsstatus der virtuellen Aufgabencomputer im Pool.

- Die Startaufgabe für virtuelle Aufgabencomputer im Pool.

Wenn Sie einen Pool erstellen, können Sie das Speicherkonto angeben, das dem Pool zugewiesen werden soll. Der Batch-Dienst ordnet virtuelle Aufgabencomputer der Rechenzentren mit besserer Netzwerkkonnektivität und Bandbreitenkapazität dem angegebenen Speicherkonto zu. Dadurch können Arbeitslasten effizienter auf Daten zugreifen.

### <a name="workitem"></a>Arbeitselement

Ein Arbeitselement gibt an, wie die Berechnung auf den virtuellen Aufgabencomputern in einem Pool ausgeführt wird. Ein Arbeitselement umfasst die folgenden Konfigurationselemente:

1.Das Programm, das die Verarbeitung auf dem virtuellen Aufgabencomputer ausführt.

2.Die Befehlszeilenparameter für das Programm.

3.Die Priorität der Berechnung.

4.Der Zeitplan der Berechnung, einschließlich der Definition, ob die Berechnung erneut auftreten soll.

Ein Arbeitselement wird immer einem Pool zugewiesen und der Pool kann bereits vorhanden sein oder automatisch bei der Erstellung des Arbeitselements erstellt werden.

### <a name="job"></a>Job

Ein Auftrag ist eine aktive Instanz eines Arbeitselements und besteht aus einer Sammlung von Aufgaben. Der Batch-Dienst instanziiert einen Auftrag auf Basis der Konfiguration des Arbeitselements. Der Auftrag verwendet virtuelle Aufgabencomputer aus dem Pool, der dem Arbeitselement zugeordnet ist.

### <a name="task"></a>Aufgabe

Eine Aufgabe ist eine Einheit der Berechnung, die einem Auftrag zugeordnet ist und auf einem virtuellen Aufgabencomputer ausgeführt wird. Eine Aufgabe verwendet die folgenden Ressourcen:

- Das Programm, das im Arbeitselement angegeben wurde.

- Die Ressourcendateien, die die zu verarbeitenden Daten enthalten. Diese Dateien werden automatisch aus dem BLOB-Speicher auf den virtuellen Aufgabencomputer kopiert. Weitere Informationen finden Sie unter Dateien und Verzeichnisse.

- Die Umgebungseinstellungen, die von dem Programm benötigt werden. Weitere Informationen finden Sie unter den Umgebungseinstellungen für Aufgaben.

- Die Einschränkungen, unter denen die Berechnung erfolgen soll. Beispielsweise die maximale Zeit, in der die Aufgabe ausgeführt werden darf, die maximale Anzahl, mit der eine Aufgabe bei der fehlerhaften Ausführung wiederholt werden sollte und die maximal zulässige Dauer, die Dateien im Arbeitsverzeichnis beibehalten werden.

Zusätzlich zu Aufgaben, die Sie zur Berechnung auf einem virtuellen Aufgabencomputer definieren können, stehen die folgenden speziellen Aufgaben zur Verfügung, die vom Batch-Dienst bereitgestellt werden:

- [Startaufgabe](#starttask)

- [Auftrags-Manager-Aufgabe](#jobmanagertask)

#### <a name="starttask"></a>Startaufgabe

Sie können das Betriebssystem von virtuellen Aufgabencomputern in einem Pool durch Zuordnen einer Startaufgabe mit dem Pool konfigurieren. Das Installieren der Software und das Starten von Hintergrundprozessen sind einige der Aktionen, die von Startaufgaben ausgeführt werden können. Die Startaufgabe wird jedes Mal ausgeführt, sobald ein virtueller Aufgabencomputer gestartet wird, solange er sich im Pool befindet.

Eine Startaufgabe wird durch Hinzufügen eines XML-Abschnitts zum Anforderungstext für den Vorgang "Pool hinzufügen" definiert. Das folgende Beispiel zeigt eine Basisdefinition einer Startaufgabe:

	{
		"commandLine":"mypoolsetup.exe",
		"resourceFiles":
		[
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"mypoolsetup.exe"
			},
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp2.exe"
			}
		],
		"maxTaskRetryCount":0
	}


#### <a name="jobmanagertask"></a>Auftrags-Manager-Aufgabe

Eine Auftrags-Manager-Aufgabe wird vor allen anderen Aufgaben gestartet. Die Auftrags-Manager-Aufgabe bietet die folgenden Vorteile:

- Er wird automatisch vom Batch-Dienst erstellt, wenn der Auftrag erstellt wird.

- Es ist vor anderen Aufgaben im Auftrag geplant.

- Sein zugeordneter virtueller Aufgabencomputer wird zuletzt aus einem Pool entfernt, wenn der Pool verkleinert wird.

- Es wird die höchste Priorität zugewiesen, wenn er neu gestartet werden muss. Wenn ein virtueller Aufgabencomputer im Leerlauf nicht verfügbar ist, kann der Batch-Dienst eine der ausgeführten Aufgaben im Pool beenden, um Platz für die Ausführung zu schaffen.

- Die Beendigung kann an die Beendigung aller Aufgaben im Auftrag gebunden sein.

Eine Auftrags-Manager-Aufgabe in einem Auftrag besitzt keine höhere Priorität als Aufgaben in anderen Aufträgen. Auftragsübergreifend werden nur Prioritäten auf Auftragsebene beobachtet. Eine Auftrags-Manager-Aufgabe wird durch Hinzufügen eines XML-Abschnitts zum Anforderungstext für den Vorgang "Arbeitselement hinzufügen" definiert. Das folgende Beispiel zeigt eine Basisdefinition einer Auftrags-Manager-Aufgabe:

	{
		"name":"jmTask",
		"commandLine":"myapp1.exe",
		"resourceFiles":
		[
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp1.exe"
			},
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp2.exe"
			}
		],
		"taskConstraints":
		{
			"maxWallClockTime":"PT1H",
			"maxTaskRetryCount":0,
			"retentionTime":"PT1H"
		},
		"killJobOnCompletion":true,
		"runElevated":false,
		"runExclusive":true
	}




## <a name="workflow"></a>Workflow des Batch-Diensts

Sie benötigen ein Batch-Konto, um den Batch-Dienst verwenden zu können, und Sie verwenden mehrere Ressourcen des Diensts zur Planung der Berechnungen. Sie verwenden den folgenden einfachen Workflow bei der Erstellung eines verteilten Rechenszenarios mit dem Batch-Dienst:

1.Laden Sie die Dateien, die Sie in Ihrem verteilten Rechenszenario verwenden möchten, auf ein Azure-Speicherkonto hoch. Diese Dateien müssen sich im Speicherkonto befinden, damit der Batch-Dienst darauf zugreifen kann. Der Batch-Dienst lädt sie beim Ausführen der Aufgabe auf einem virtuellen Aufgabencomputer.

2.Laden Sie die abhängigen Binärdateien in das Speicherkonto hoch. Die Binärdateien enthalten das von der Aufgabe ausgeführte Programm und die abhängigen Assemblys. Auf diese Dateien muss auch vom Speicher aus zugegriffen werden. Sie werden auf den virtuellen Aufgabencomputer geladen.

3.Erstellen Sie einen Pool von virtuellen Aufgabencomputern. Sie können die Größe des zu verwendenden virtuellen Aufgabencomputers bei der Erstellung eines Pools zuweisen. Bei der Ausführung einer Aufgabe wird ihr ein virtueller Aufgabencomputer aus diesem Pool zugewiesen.

4.Erstellen Sie ein Arbeitselement. Beim Erstellen eines Arbeitselements wird automatisch ein Auftrag erstellt. Mit einem Arbeitselement können Sie einen Auftrag von Aufgaben verwalten.

5.Fügen Sie Aufgaben zum Arbeitselement hinzu. Jede Aufgabe verwendet das von Ihnen hochgeladene Programm, um Informationen aus einer Datei zu verarbeiten, die Sie ebenfalls hochgeladen haben.

6.Überwachen Sie die Ergebnisse der Ausgabe.

## <a name="files"></a>Dateien und Verzeichnisse

Jede Aufgabe verfügt über ein Arbeitsverzeichnis, unter dem null oder mehr Verzeichnisse und Dateien für das Speichern des von einer Aufgabe ausgeführten Programms, der von einer Aufgabe verarbeiteten Daten und der Ausgabe der von einer Aufgabe ausgeführten Verarbeitung erstellt werden. Diese Verzeichnisse und Dateien sind dann während der Ausführung eines Auftrags für andere Aufgaben verfügbar. Alle Aufgaben, Dateien und Verzeichnisse auf einem virtuellen Aufgabencomputer gehören zu einem einzelnen Benutzerkonto.

Der Batch-Dienst stellt einen Teil des Dateisystems auf einem virtuellen Aufgabencomputer als Stammverzeichnis bereit. Das Stammverzeichnis des virtuellen Aufgabencomputers steht einer Aufgabe über die Umgebungsvariable "WATASK\_TVM\_ROOT\_DIR" zur Verfügung. Weitere Informationen zur Verwendung von Umgebungsvariablen finden Sie unter den Umgebungseinstellungen für Aufgaben.

Das Stammverzeichnis enthält die folgenden Unterverzeichnisse:

- **Aufgaben** - Hier werden alle Dateien gespeichert, die zu den auf dem virtuellen Aufgabencomputer ausgeführten Aufgaben gehören. Für jede Aufgabe erstellt der Batch-Dienst ein Arbeitsverzeichnis mit einem eindeutigen Pfad in der Form "%WATASK\_TVM\_ROOT\_DIR%/tasks/Arbeitselementname/Auftragsname/Aufgabenname/". Dieses Verzeichnis bietet Lese-/Schreibzugriff auf die Aufgabe. Die Aufgabe kann Dateien unter diesem Verzeichnis erstellen, lesen, aktualisieren und löschen, und dieses Verzeichnis bleibt auf Basis der für diese Aufgabe angegebenen "RetentionTime"-Einschränkung erhalten.

- **Shared** - Dieser Speicherort ist ein freigegebenes Verzeichnis für alle Aufgaben des Kontos. Auf dem virtuellen Aufgabencomputer ist das freigegebene Verzeichnis "%WATASK\_TVM\_ROOT\_DIR%/shared". Dieses Verzeichnis bietet Lese-/Schreibzugriff auf die Aufgabe. Die Aufgabe kann Dateien unter diesem Verzeichnis erstellen, lesen, aktualisieren und löschen.

- **Start** - Dieser Speicherort wird von einer Startaufgabe als Arbeitsverzeichnis verwendet. Alle Dateien, die vom Batch-Dienst heruntergeladen werden, um die Startaufgabe zu starten, werden ebenfalls unter diesem Verzeichnis gespeichert. Auf dem virtuellen Aufgabencomputer ist das Startverzeichnis "%WATASK\_TVM\_ROOT\_DIR%/start". Die Aufgabe kann Dateien unter diesem Verzeichnis erstellen, lesen, aktualisieren und löschen, und dieses Verzeichnis kann von Startaufgaben dazu verwendet werden, das Betriebssystem zu konfigurieren.

Wenn ein virtueller Aufgabencomputer aus einem Pool entfernt wird, werden alle auf dem virtuellen Aufgabencomputer gespeicherten Dateien entfernt.

## <a name="scaling"></a>Skalieren von Anwendungen

Die Größe Ihrer Anwendung kann problemlos automatisch erhöht oder verringert werden, um der erforderlichen Berechnung zu entsprechen. Sie können die Anzahl der virtuellen Aufgabencomputer in einem Pool entsprechend der aktuellen Statistik zur Arbeitsauslastung und Ressourcennutzung dynamisch anpassen. Sie können auch die Gesamtkosten für die Ausführung Ihrer Anwendung optimieren, indem Sie für sie die automatische Skalierung festlegen. Sie können die Skalierungseinstellungen für einen Pool bei dessen Erstellung angeben und die Konfiguration jederzeit aktualisieren.

Sie geben die automatische Skalierung einer Anwendung mithilfe einer Reihe von Skalierungsformeln an. Die Formeln, die zum Ermitteln der Anzahl der virtuellen Aufgabencomputer verwendet werden können, die sich für das nächste Skalierungsintervall im Pool befinden. Sie müssen z. B. eine große Anzahl von Aufgaben übermitteln, die für einen Pool geplant werden. Sie können dem Pool eine Skalierungsformel zuweisen, die die Größe des Pools auf Basis der aktuellen Anzahl der ausstehenden Aufgaben und der Beendigungsrate der Aufgaben angibt. Der Batch-Dienst wertet die Formel in regelmäßigen Abständen aus und ändert die Größe des Pools auf Basis der Arbeitsauslastung.

Eine Formel kann auf den folgenden Metriken basieren:

- **Zeitmetriken** - Auf Basis von Statistiken, die alle fünf Minuten für die angegebene Anzahl von Stunden erfasst werden.

- **Ressourcenmetriken** - Auf Basis von CPU-Auslastung, Bandbreitenauslastung, Speicherauslastung und der Anzahl der virtuellen Aufgabencomputer.

- **Aufgabenmetriken** - Auf Basis des Status von Aufgaben wie "Aktiv", "Ausstehend" und "Abgeschlossen".

Weitere Informationen zum automatischen Skalieren einer Anwendung finden Sie unter "Konfigurieren der automatischen Skalierung von virtuellen Aufgabencomputern".

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
  WATASK_PORTRANGE_LOW
  WATASK_PORTRANGE_HIGH
</td>
<td>
  Der Portbereich (einschließlich der beiden Enden), den die Aufgabe für die Kommunikation verwenden kann, wenn die Kommunikation innerhalb eines Pools aktiviert ist.
</td>
</table>


**Hinweis** 

Diese vom System definierten Variablen können nicht überschrieben werden.

Sie können den Wert von Umgebungseinstellungen mithilfe des Vorgangs "Aufgabe abrufen" abrufen.
