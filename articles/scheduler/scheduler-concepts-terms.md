<properties
 pageTitle="Konzepte, Terminologie und Entitätshierarchie für Scheduler"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article" 
 ms.date="05/12/2015"
 ms.author="krisragh"/>

# Konzepte, Terminologie und Entitätshierarchie für Scheduler

## Entitätshierarchie für Scheduler

Die folgende Tabelle beschreibt die wichtigsten Ressourcen, die von der Scheduler-API verfügbar gemacht oder verwendet werden:

|Ressource | Beschreibung |
|---|---|
|**Clouddienst**|Ein Clouddienst stellt im Prinzip eine Anwendung dar. Ein Abonnement kann mehrere Clouddienste enthalten.|
|**Auftragssammlung**|Eine Auftragssammlung enthält eine Gruppe von Aufträgen und dient zum Verwalten von Einstellungen, Kontingenten und Drosselungen für die Aufträge in der Sammlung. Eine Auftragssammlung wird von einem Abonnementbesitzer erstellt und fasst Aufträge auf der Grundlage von Verwendungs- oder Anwendungsgrenzen zusammen. Sie ist auf eine einzelne Region beschränkt. Außerdem ermöglicht sie die Erzwingung von Kontingenten (wie etwa „MaxJobs“ und „MaxRecurrence“), um die Verwendung aller Aufträge in der Sammlung zu beschränken.|
|**Auftrag**|Ein Auftrag definiert eine einzelne wiederkehrende Aktion mit einfachen oder komplexen Ausführungsstrategien. Zu diesen Aktionen zählen beispielsweise HTTP-Anforderungen oder Anforderungen für die Speicherwarteschlange.|
|**Auftragsverlauf**|Ein Auftragsverlauf liefert Details zur Ausführung eines Auftrags. Er gibt Aufschluss darüber, ob die Ausführung erfolgreich war, und enthält Details zur Antwort.|

## Entitätsverwaltung für Scheduler

Die Scheduler- und die Service Management-API machen für die Ressourcen allgemein folgende Vorgänge verfügbar:

|Funktion|Beschreibung und URI-Adresse|
|---|---|
|**Clouddienstverwaltung**|GET-, PUT- und DELETE-Unterstützung zum Erstellen und Ändern von Clouddiensten <p>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}`</p>|
|**Auftragssammlungsverwaltung**|GET-, PUT- und DELETE-Unterstützung zum Erstellen und Ändern von Auftragssammlungen und der darin enthaltenen Aufträge. Bei einer Auftragssammlung handelt es sich um einen Container für Aufträge, der mit Kontingenten und gemeinsamen Einstellungen verknüpft ist. Beispiele für Kontingente (siehe Erläuterung weiter unten) wären die maximale Anzahl von Aufträgen sowie das kleinste Wiederholungsintervall. <p>PUT und DELETE: `https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/jobcollections/{jobCollectionName}`</p><p>GET: `https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}`</p>
|**Auftragsverwaltung**|GET-, PUT-, POST-, PATCH- und DELETE-Unterstützung zum Erstellen und Ändern von Aufträgen. Alle Aufträge müssen einer bereits vorhandenen Auftragssammlung angehören. Es gibt also keine implizite Erstellung. <p>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}/jobs/{jobId}`</p>|
|**Auftragsverlaufsverwaltung**|GET-Unterstützung zum Abrufen des Auftragsausführungsverlaufs für 60 Tage. Dieser enthält unter anderem Informationen zur verstrichenen Zeit sowie zu den Ergebnissen der Auftragsausführung. Bietet Unterstützung für Abfragezeichenfolgenparameter zur Filterung auf der Grundlage von Zustand und Status. <P>`https://management.core.windows.net/{subscriptionId}/cloudservices/{cloudServiceName}/resources/scheduler/~/jobcollections/{jobCollectionName}/jobs/{jobId}/history`</p>|

## Auftragstypen

Es gibt zwei Arten von Aufträgen: HTTP-Aufträge (einschließlich HTTPS-Aufträge mit SSL-Unterstützung) und Speicherwarteschlangenaufträge. HTTP-Aufträge sind ideal, wenn Sie über einen Endpunkt einer vorhandenen Workload oder eines vorhandenen Diensts verfügen. Mit Speicherwarteschlangenaufträgen können Sie Nachrichten in Speicherwarteschlangen veröffentlichen. Daher eignen sich diese Aufträge perfekt für Workloads, die Speicherwarteschlangen verwenden.

## Die Auftragsentität im Detail

Ein geplanter Auftrag setzt sich grundsätzlich aus mehreren Komponenten zusammen:

1.  Die Aktion, die ausgeführt werden soll, wenn der Zeitgeber die Aktion auslöst.  

2.  (Optional) Die Zeit, zu der der Auftrag ausgeführt werden soll.

3.  (Optional) Die Angabe, wann und wie oft den Auftrag wiederholt werden soll.

4.  (Optional) Eine Aktion, die ausgelöst werden soll, wenn bei der primären Aktion ein Fehler auftritt.

Intern enthält ein geplanter Auftrag auch vom System bereitgestellte Daten wie etwa die Zeit für die nächste geplante Ausführung.

Im Anschluss sehen Sie ein umfassendes Beispiel für einen Scheduler-Auftrag. Ausführliche Informationen finden Sie in den folgenden Abschnitten.

	{
		"startTime": "2012-08-04T00:00Z",               // optional
		"action":
		{
			"type": "http",
			"retryPolicy": { "retryType":"none" },
			"request":
			{
				"uri": "http://contoso.com/foo",        // required
				"method": "PUT",                        // required
				"body": "Posting from a timer",         // optional
				"headers":                              // optional

				{
					"Content-Type": "application/json"
				},
			},
		   "errorAction":
		   {
			   "type": "http",
			   "request":
			   {
				   "uri": "http://contoso.com/notifyError",
				   "method": "POST",
			   },
		   },
		},
		"recurrence":                                   // optional
		{
			"frequency": "week",                        // can be "year" "month" "day" "week" "minute"
			"interval": 1,                              // optional, how often to fire (default to 1)
			"schedule":                                 // optional (advanced scheduling specifics)
			{
				"weekDays": ["monday", "wednesday", "friday"],
				"hours": [10, 22]
			},
			"count": 10,                                 // optional (default to recur infinitely)
			"endTime": "2012-11-04",                     // optional (default to recur infinitely)
		},
		"state": "disabled",                           // enabled or disabled
		"status":                                       // controlled by Scheduler service
		{
			"lastExecutionTime": "2007-03-01T13:00:00Z",
			"nextExecutionTime": "2007-03-01T14:00:00Z ",
			"executionCount": 3,
											    "failureCount": 0,
												"faultedCount": 0
		},
	}

Wie im obigen Scheduler-Beispielauftrag zu sehen, besteht eine Auftragsdefinition aus mehreren Komponenten:

1.  Startzeit („startTime“)  

2.  Aktion („action“), einschließlich Fehleraktion („errorAction“)

3.  Wiederholung („recurrence“)

4.  Zustand („State“)

5.  Status („status“)

6.  Wiederholungsrichtlinie („retryPolicy“)

Sehen wir uns die einzelnen Elemente im Detail an:

## startTime

"startTime" ist die Startzeit und ermöglicht dem Aufrufer die Angabe eines Zeitzonenoffsets bei der Übertragung im [ISO-8601-Format](http://en.wikipedia.org/wiki/ISO_8601).

## action und errorAction

„action“ ist die Aktion, die bei jedem Vorkommen aufgerufen wird, und beschreibt eine Art von Dienstaufruf. Die Aktion wird gemäß dem angegebenen Zeitplan ausgeführt. Derzeit werden von Scheduler HTTP-Aktionen und Speicherwarteschlangenaktionen unterstützt.

Die Aktion im obigen Beispiel ist eine HTTP-Aktion. Im folgenden Beispiel wird eine Speicherwarteschlangenaktion verwendet:

	{
			"type": "storageQueue",
			"queueMessage":
			{
				"storageAccount": "myStorageAccount",  // required
				"queueName": "myqueue",                // required
				"sasToken": "TOKEN",                   // required
				"message":                             // required
					"My message body",
			},
	}

„errorAction“ ist der Fehlerhandler (also die Aktion, die aufgerufen wird, wenn bei der primären Aktion ein Fehler auftritt). Mit dieser Variablen können Sie einen Endpunkt für die Fehlerbehandlung aufrufen oder eine Benutzerbenachrichtigung senden. So können Sie einen sekundären Endpunkt erreichen, falls der primäre Endpunkt nicht verfügbar ist (beispielsweise bei einem Notfall am Standort des Endpunkts), oder einen Fehlerbehandlungsendpunkt benachrichtigen. Auch bei der Fehleraktion kann es sich um eine einfache oder um eine komplexe Logik handeln, die auf anderen Aktionen basiert. Informationen zum Erstellen eines SAS-Tokens finden Sie unter [Erstellen und Verwenden einer SAS (Shared Access Signature)](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## recurrence

Die Wiederholung besteht aus mehreren Teilen:

1.  Häufigkeit: in Minuten, Stunden, Tagen, Wochen, Monaten oder Jahren  

2.  Intervall: Intervall mit der angegebenen Häufigkeit für die Wiederholung

3.  Vorgegebener Zeitplan: Angabe in Minuten, Stunden, Wochentagen, Monaten und Monatstagen für die Wiederholung

4.  Anzahl: Anzahl der Vorkommen

5.  Endzeit: Zeitangabe, nach der keine Aufträge mehr ausgeführt werden

Ein Auftrag wird wiederholt, wenn in der JSON-Definition ein Wiederholungsobjekt angegeben ist. Bei gleichzeitiger Angabe einer Anzahl und einer Endzeit gilt die zuerst auftretende Abschlussregel.

## state

Der Auftrag kann einen von vier Zuständen haben: aktiviert, deaktiviert, abgeschlossen oder fehlerhaft. Aufträge können mittels „PUT“ oder „PATCH“ aktiviert oder deaktiviert werden. Bei einem abgeschlossenen oder fehlerhaften Auftrag ist der Zustand endgültig und kann nicht aktualisiert werden. (Der Auftrag kann allerdings weiterhin gelöscht werden). Im Anschluss sehen Sie ein Beispiel für die state-Eigenschaft:


    	"state": "disabled", // enabled, disabled, completed, or faulted
Abgeschlossene und fehlerhafte Aufträge werden nach 60 Tagen gelöscht.

## status

Sobald ein Scheduler-Auftrag gestartet wurde, werden Informationen zum aktuellen Status des Auftrags zurückgegeben. Dieses Objekt wird vom System festgelegt und kann nicht vom Benutzer festgelegt werden. Es ist jedoch in das Auftragsobjekt integriert (und nicht als separate verknüpfte Ressource vorhanden), um den Status eines Auftrags problemlos abrufen zu können.

Der Auftragsstatus enthält den Zeitpunkt der vorherigen Ausführung (sofern zutreffend), den Zeitpunkt der nächsten geplanten Ausführung (bei laufenden Aufträgen) und die Anzahl der Auftragsausführungen.

## retryPolicy

Für den Fall, dass bei einem Scheduler-Auftrag ein Fehler auftritt, kann durch Angabe einer Wiederholungsrichtlinie bestimmt werden, ob und auf welche Weise die Aktion wiederholt werden soll. Hierzu wird das Objekt **retryType** verwendet. Ist keine Wiederholungsrichtlinie vorhanden, wird das Objekt auf **none** festgelegt (wie weiter oben zu sehen). Ist eine Wiederholungsrichtlinie vorhanden, legen Sie es auf **fixed** fest.

Für eine Wiederholungsrichtlinie können zwei zusätzliche Einstellungen angegeben werden: ein Wiederholungsintervall (**retryInterval**) und die Anzahl von Wiederholungen (**retryCount**).

Das Wiederholungsintervall (**retryInterval**) ist das Intervall zwischen den Wiederholungen. Es ist standardmäßig auf den Mindestwert von einer Minute festgelegt. Der maximal zulässige Wert beträgt 18 Monate. Es wird im ISO-8601-Format definiert. Mit dem Wert für die Anzahl von Wiederholungen (Objekt **retryCount**) wird angegeben, wie oft versucht wird, den Vorgang zu wiederholen. Der Standardwert ist 5, der maximal zulässige Wert ist 20. Die Angabe von **retryInterval** und **retryCount** ist jeweils optional. Wenn **retryType** auf **fixed** festgelegt ist und keine expliziten Werte angegeben werden, werden die Standardwerte verwendet.

## Siehe auch

 [Was ist Azure Scheduler?](scheduler-intro.md)

 [Get Started Using Scheduler in the Management Portal](scheduler-get-started-portal.md) (in englischer Sprache)

 [Plans and Billing in Azure Scheduler](scheduler-plans-billing.md) (in englischer Sprache)

 [How to Build Complex Schedules and Advanced Recurrence with Azure Scheduler](scheduler-advanced-complexity.md) (in englischer Sprache)

 [Zeitplanungsmodul-REST-API – Referenz](https://msdn.microsoft.com/library/dn528946)

 [Scheduler – PowerShell-Cmdlets-Referenz](scheduler-powershell-reference.md)

 [Scheduler High-Availability and Reliability](scheduler-high-availability-reliability.md) (in englischer Sprache)

 [Scheduler Limits, Defaults, and Error Codes](scheduler-limits-defaults-errors.md) (in englischer Sprache)

 [Scheduler Outbound Authentication](scheduler-outbound-authentication.md) (in englischer Sprache)
 

<!---HONumber=July15_HO4-->