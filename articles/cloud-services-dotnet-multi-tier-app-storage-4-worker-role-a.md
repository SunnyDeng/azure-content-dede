<properties linkid="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande"></tags>

# Erstellen der Workerrolle A (E-Mail-Planer) für die Anwendung Azure-E-Mail-Dienst - 4 von 5

Dies ist das vierte von fünf Lernprogrammen, die erläutern, wie die Beispielanwendung Azure-E-Mail-Dienst erstellt und bereitgestellt wird. Weitere Informationen zur Anwendung und den Lernprogrammen finden Sie im [ersten Lernprogramm der Serie][ersten Lernprogramm der Serie].

In diesem Lernprogramm lernen Sie Folgendes:

-   Abfragen und Aktualisieren von Azure-Speichertabellen
-   Hinzufügen von Arbeitsaufgaben zu einer Warteschlange zur Verarbeitung durch eine andere Workerrolle
-   Behandeln des geplanten Herunterfahrens durch Außerkraftsetzen der `OnStop`-Methode
-   Behandeln des geplanten Herunterfahrens und Sicherstellen, dass keine E-Mails verpasst werden und keine doppelten E-Mails gesendet werden
-   Prüfen einer Workerrolle, die Azure-Speichertabellen verwendet, mit dem Server-Explorer

Das Projekt Workerrolle A wurde bereits während der Erstellung des Cloud-Dienst-Projekts erstellt. Jetzt müssen Sie lediglich die Workerrolle programmieren.

## Abschnitte dieses Lernprogramms

-   [Hinzufügen eines Verweises auf das Webprojekt][Hinzufügen eines Verweises auf das Webprojekt]
-   [Hinzufügen des SendEmail-Modells][Hinzufügen des SendEmail-Modells]
-   [Hinzufügen von Code, der beim Start der Workerrolle ausgeführt wird][Hinzufügen von Code, der beim Start der Workerrolle ausgeführt wird]
-   [Testen von Workerrolle A][Testen von Workerrolle A]
-   [Nächste Schritte][Nächste Schritte]

## <a name="addref"></a><span class="short-header">Hinzufügen eines Projektverweises</span>Hinzufügen eines Verweises auf das Webprojekt

Sie benötigen einen Verweis auf das Webprojekt, da dort die Entitätsklassen definiert werden. In Workerrolle B verwenden Sie dieselben Entitätsklassen, um Daten in den von der Anwendung verwendeten Azure-Tabellen zu lesen und zu schreiben.

**Hinweis:** In einer Produktionsanwendung legen Sie keinen Verweis auf ein Webprojekt aus einem Workerrollenprojekt fest, da dies zur Referenzierung einer Reihe abhängiger Assemblys führt, die nicht in der Workerrolle enthalten sein sollen oder die Sie dort nicht benötigen. Normalerweise verwalten Sie gemeinsam genutzte Modellklassen in einem Klassenbibliotheksprojekt, und sowohl Web- als auch Workerrollenprojekte verweisen auf das Klassenbibliotheksprojekt. Damit die Lösungsstruktur einfach bleibt, werden Modellklassen im Zuge dieses Lernprogramms im Webprojekt gespeichert.

1.  Klicken Sie mit der rechten Maustaste auf das Projekt WorkerRoleA, und wählen Sie **Hinzufügen** und dann **Verweis** aus.

2.  Fügen Sie im Verweis-Manager einen Verweis auf das Projekt MvcWebRole hinzu, und klicken Sie anschließend auf **OK**.

    ![Verweis auf MvcWebRole hinzufügen][Verweis auf MvcWebRole hinzufügen]

## <a name="addmodel"></a>Hinzufügen des SendEmail-Modells

Workerrolle A erstellt die `SendEmail`-Zeilen in der Tabelle `Message`, und Workerrolle B liest diese Zeilen, um die zum Senden von E-Mails erforderlichen Informationen abzurufen. Die folgende Abbildung zeigt einen Teil der Eigenschaften für zwei `Message`-Zeilen und drei `SendEmail`-Zeilen in der Tabelle `Message`.

![Tabelle Message mit SendEMail][Tabelle Message mit SendEMail]

Diese Zeilen in der Tabelle `Message` dienen verschiedenen Zwecken:

-   Sie stellen alle Informationen bereit, die Workerrolle B benötigt, um eine einzelne E-Mail zu senden.
-   Sie erfassen, ob eine E-Mail gesendet wurde, um das Senden doppelter E-Mails zu vermeiden, falls eine Workerrolle nach einem Fehler neu gestartet wird.
-   Sie ermöglichen es Workerrolle A festzustellen, wann alle E-Mails für eine Nachricht gesendet wurden, sodass die Nachricht als `Complete` markiert werden kann.

Zum Lesen und Schreiben der `SendEmail`-Zeilen wird eine Modellklasse benötigt. Da diese sowohl für Workerrolle A als auch Workerrolle B zugänglich sein muss und da alle übrigen Modellklassen im Webprojekt definiert sind, ist es sinnvoll, diese Klasse ebenfalls im Webprojekt zu definieren.

1.  Fügen Sie im Ordner *Models* im Webprojekt die Datei *SendEmail.cs* aus dem heruntergeladenen Projekt hinzu.

Der Code hier ähnelt dem anderer Modellklassen, allerdings fehlen hier DataAnnotations-Attribute, da mit diesem Modell keine Benutzeroberfläche verknüpft ist, denn sie wird in einem MVC-Controller nicht verwendet.

        public class SendEmail : TableEntity
        {
            public long MessageRef { get; set; }
            public string EmailAddress { get; set; }
            public DateTime? ScheduledDate { get; set; }
            public String FromEmailAddress { get; set; }
            public string SubjectLine { get; set; }
            public bool? EmailSent { get; set; }
            public string SubscriberGUID { get; set; }
            public string ListName { get; set; }
        }

## <a name="addcode"></a><span class="short-header">Hinzufügen von Workerrollencode</span>Hinzufügen von Code, der beim Start der Workerrolle ausgeführt wird

Im Projekt WorkerRoleA wurde mit der Projektvorlage *WorkerRole.cs* mit dem folgenden Code erstellt:

        public class WorkerRole : RoleEntryPoint
        {
            public override void Run()
            {
                // This is a sample worker implementation. Replace with your logic.
                Trace.WriteLine("WorkerRole1 entry point called", "Information");
                while (true)
                {
                    Thread.Sleep(10000);
                    Trace.WriteLine("Working", "Information");
                }
            }
            public override bool OnStart()
            {
                // Set the maximum number of concurrent connections 
                ServicePointManager.DefaultConnectionLimit = 12;
                // For information on handling configuration changes
                // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
                return base.OnStart();
            }
        }

Dies ist der Standardvorlagencode für die Workerrolle. Es gibt eine `OnStart`-Methode, in die Sie Initialisierungscode einfügen können, der nur ausgeführt wird, wenn eine Instanz der Workerrolle gestartet wird, und eine `Run`-Methode, die nach Abschluss der `OnStart`-Methode aufgerufen wird. Sie ersetzen diesen Code durch Ihren eigenen Initialisierungs- und Ausführungscode.

1.  Löschen Sie *WorkerRole.cs* im Projekt WorkerRoleA, und fügen Sie dann die Datei *WorkerRoleA.cs* aus dem heruntergeladenen Projekt hinzu.

### Die OnStart-Methode

Mit der `OnStart`-Methode werden die Kontextobjekte initialisiert, die Sie für das Arbeiten mit Azure-Speicherentitäten benötigen. Damit wird außerdem sichergestellt, dass alle Tabellen, Warteschlangen und Blob-Container, die Sie in der `Run`-Methode verwenden, vorhanden sind. Der Code, mit dem diese Aufgaben ausgeführt werden, ähnelt dem weiter oben vorgestellten Code für die MVC-Controller-Konstruktoren. Später konfigurieren Sie die Verbindungszeichenfolge, die diese Methode verwendet.

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;
            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in WorkerA");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient(); 
            sendEmailQueue = queueClient.GetQueueReference("azuremailqueue"); 
            var tableClient = storageAccount.CreateCloudTableClient(); 
            mailingListTable = tableClient.GetTableReference("mailinglist"); 
            messageTable = tableClient.GetTableReference("message"); 
            messagearchiveTable = tableClient.GetTableReference("messagearchive"); 
            // Create if not exists for queue, blob container, SentEmail table. 
            sendEmailQueue.CreateIfNotExists(); 
            messageTable.CreateIfNotExists(); 
            mailingListTable.CreateIfNotExists(); 
            messagearchiveTable.CreateIfNotExists(); 
            return base.OnStart();
        }

Sie haben zuvor möglicherweise Dokumentation zum Arbeiten mit Azure Storage gesehen, die den Initialisierungscode in einer Schleife zeigt, mit dem Transportfehler festgestellt werden. Dieser Code wird nicht mehr benötigt, da die API jetzt über einen integrierten Wiederholungsmechanismus verfügt, der vorübergehende Netzwerkfehler für bis zu drei zusätzliche Versuche abfängt.

Die `ConfigureDiagnostics`-Methode, die von der `OnStart`-Methode aufgerufen wird, richtet die Ablaufverfolgung ein, sodass Sie die Ausgabe der Methoden `Trace.Information` und `Trace.Error` sehen können. Diese Methode wird im [zweiten Lernprogramm][zweiten Lernprogramm] erläutert.

Mit der Einstellung [ServicePointManager.DefaultConnectionLimit][ServicePointManager.DefaultConnectionLimit] wird die maximale Anzahl gleichzeitiger TCP-Verbindungen angegeben, die in .NET geöffnet werden können. In der Dokumentation zur [ServicePointManager][ServicePointManager]-Klasse wird erläutert, dass jede Verbindung, die die Workerrolleninstanz mit einem eindeutigen Host herstellt, eine separate Verbindung ist. Für diese Workerrolle beispielsweise würden drei gleichzeitige Verbindungen bestehen: eine für Tabellen, eine für Blobs und eine für Warteschlangen. Für einige Verbindungen, z. B. für SQL Server, führt die Clientsoftware Verbindungspooling durch, wodurch die Anzahl gleichzeitiger Verbindungen, die von `ServicePointManager` verwaltet werden, reduziert werden kann. Der beste Wert für `DefaultConnectionLimit` hängt zum Teil von dem Back-End-Dienst ab, mit denen Sie eine Verbindung herstellen. Für einen Dienst können 500 offene Verbindungen in Ordnung sein, während ein anderer Dienst mit nur 5 Verbindungen überfordert ist. Ein allgemeiner Richtwert, der für viele Szenarien gut geeignet ist, sind 12 Verbindungen pro Prozessor.

### Die OnStop-Methode

Mit der `OnStop`-Methode wird die globale Variable `onStopCalled` auf True festgelegt. Danach wird darauf gewartet, dass die `Run`-Methode die globale Variable `returnedFromRunMethod` auf True festlegt. Damit wird signalisiert, dass ein sauberes Herunterfahren ausgeführt werden kann.

        public override void OnStop()
        {
            onStopCalled = true;
            while (returnedFromRunMethod == false)
            {
                System.Threading.Thread.Sleep(1000);
            }
        }

Die `OnStop`-Methode wird aufgerufen, wenn die Workerrolle aus einem der folgenden Gründe heruntergefahren wird:

-   Azure muss den virtuellen Computer (die Webrollen- oder Workerrolleninstanz) oder den physischen Computer neu starten, auf dem der virtuelle Computer gehostet wird.
-   Sie haben Ihren Cloud-Dienst mit der Schaltfläche **Beenden** im Azure-Verwaltungsportal angehalten.
-   Sie haben eine Aktualisierung Ihres Cloud-Dienst-Projekts bereitgestellt.

Die `Run`-Methode überwacht die Variable `onStopCalled` und lädt keine neuen zu verarbeitenden Arbeitsaufgaben mehr, wenn diese Variable den Wert `true` erhält. Diese Koordination der Methoden `OnStop` und `Run` ermöglicht ein ordnungsgemäßes Herunterfahren des Workerprozesses.

Azure installiert von Zeit zu Zeit Updates des Betriebssystems, um sicherzustellen, dass die Plattform sicher und zuverlässig arbeitet. Für diese Updates müssen die Computer, die Ihren Cloud-Dienst hosten, in der Regel heruntergefahren und neu gestartet werden. Weitere Informationen finden Sie unter [Role Instance Restarts Due to OS Upgrades][Role Instance Restarts Due to OS Upgrades] (Neustart von Rolleninstanzen aufgrund von Betriebssystemupdates – in englischer Sprache).

### Die Run-Methode

Die `Run`-Methode führt zwei Funktionen aus:

-   Sie durchsucht die Tabelle `message` nach Nachrichten, die heute oder eher gesendet werden sollen und für die noch keine Arbeitsaufgaben in der Warteschlange erstellt wurden.

-   Sie durchsucht die Tabelle `message` nach Nachrichten mit einem Status, der angibt, dass alle Arbeitsaufgaben in der Warteschlange erstellt wurden, dass jedoch noch nicht alle E-Mails gesendet wurden. Wird eine Nachricht gefunden, werden die `SendEmail`-Zeilen nach dieser Nachricht durchsucht, um festzustellen, ob alle E-Mails gesendet wurden. Ist dies der Fall, wird der Status auf `Completed` aktualisiert und die `message`-Zeile archiviert.

Die Methode prüft auch die globale Variable `onStopCalled`. Wenn die Variable den Wert `true` hat, lädt die Methode keine neuen zu verarbeitenden Arbeitsaufgaben mehr und gibt zurück, wann bereits gestartete Aufgaben abgeschlossen sind.

        public override void Run()
        {
            Trace.TraceInformation("WorkerRoleA entering Run()");
            while (true)
            {
                try
                {
                    var tomorrow = DateTime.Today.AddDays(1.0).ToString("yyyy-MM-dd");
                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();
                    TableOperation replaceOperation;
                    // Process each message (queue emails to be sent).
                    foreach (Message messageToProcess in messagesToProcess)
                    {
                        string restartFlag = "0";
                        // If the message is already in Queuing status,
                        // set flag to indicate this is a restart.
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }
                        // If the message is in Pending status, change
                        // it to Queuing.
                        if (messageToProcess.Status == "Pending")
                        {
                            messageToProcess.Status = "Queuing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        // If the message is in Queuing status, 
                        // process it and change it to Processing status;
                        // otherwise it's already in processing status, and 
                        // in that case check if processing is complete.
                        if (messageToProcess.Status == "Queuing")
                        {
                            ProcessMessage(messageToProcess, restartFlag);
                            messageToProcess.Status = "Processing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        else
                        {
                            CheckAndArchiveIfComplete(messageToProcess);
                        }
                    }
                    // Sleep for one minute to minimize query costs. 
                    System.Threading.Thread.Sleep(1000 * 60);
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in queue process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

Sämtliche Arbeitsschritte werden in einer unendlichen Schleife in einem `while`-Block ausgeführt, und der gesamte Code im `while`-Block wird von einem `try`-`catch`-Block umschlossen, um unbehandelte Ausnahmen zu verhindern. Wenn eine unbehandelte Ausnahme auftritt, löst Azure das Ereignis [UnhandledException][UnhandledException] aus, der Workerprozess wird beendet, und die Rolle wird offline geschaltet. Die Workerrolle wird von Azure neu gestartet, dies dauert jedoch einige Minuten. Der `try`-Block ruft `TraceError` auf, um den Fehler aufzuzeichnen, und geht dann für 60 Sekunden in den Ruhezustand, damit die Fehlermeldung nicht zu häufig wiederholt wird, wenn der Fehler dauerhaft ist. In einer Produktionsanwendung können Sie einem Administrator im `try`-Block eine E-Mail senden.

Die `Run`-Methode verarbeitet eine Abfrage von `message`-Zeilen in der Tabelle `message`, deren geplantes Datum vor morgen liegt:

                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();

**Hinweis:** Nachrichtenzeilen nach ihrer Verarbeitung in die Tabelle `messagearchive` zu verschieben, bietet u. a. den Vorteil, dass bei der entsprechenden Abfrage nur `PartitionKey` und `RowKey` als Suchkriterien angegeben werden müssen. Wenn verarbeitete Zeilen nicht archiviert würden, müsste in der Abfrage außerdem ein Nichtschlüsselfeld (`Status`) angegeben werden, und es müssten mehr Zeilen durchsucht werden. Die Tabelle würde größer werden, die Abfrage würde länger dauern und möglicherweise Fortsetzungstokens erhalten.

Wenn eine Nachricht den Status `Pending` hat, hat die Verarbeitung noch nicht begonnen; wenn sie den Status `Queuing` hat, hat die Verarbeitung bereits begonnen, wurde jedoch unterbrochen, bevor alle Warteschlangennachrichten erstellt wurden. In diesem Fall muss in Workerrolle B eine zusätzliche Prüfung vorgenommen werden, wenn sie die einzelnen E-Mails sendet, um sicherzustellen, dass die E-Mail noch nicht gesendet wurde. Das ist der Zweck der Variablen `restartFlag`.

                        string restartFlag = "0";
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }

Anschließend legt der Code `message`-Zeilen mit dem Status `Pending` auf `Queuing` fest. Für diese Zeilen sowie für alle Zeilen, die bereits den Status `Queuing` hatten, wird danach die `ProcessMessage`-Methode aufgerufen, um die Warteschlangenarbeitsaufgaben zum Senden von E-Mails für die Nachricht zu erstellen.

                        if (messageToProcess.Status == "Pending")
                        {
                            messageToProcess.Status = "Queuing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        if (messageToProcess.Status == "Queuing")
                        {
                            ProcessMessage(messageToProcess, restartFlag);
                            messageToProcess.Status = "Processing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        else
                        {
                            CheckAndArchiveIfComplete(messageToProcess);
                        }

Nach der Verarbeitung einer Nachricht im Status `Queuing` legt der Code den Status der `Message`-Zeile auf `Processing` fest. Zeilen in der Tabelle `message`, die nicht den Status `Pending` oder `Queuing` haben, befinden sich bereits im Status `Processing`. Für diese Zeilen ruft der Code eine Methode auf, die prüft, ob alle E-Mails für die Nachricht gesendet wurden. Wenn alle E-Mails gesendet wurden, wird die `message`-Zeile archiviert.

Nach der Verarbeitung aller von der Abfrage abgerufenen Datensätze geht der Code für eine Minute in den Ruhezustand.

                // Sleep for one minute to minimize query costs.
                System.Threading.Thread.Sleep(1000*60);

Für jede Azure Storage-Abfrage fallen minimale Gebühren an, auch wenn keine Daten zurückgegeben werden. Ein ständiges Neuscannen würde Ihre Azure-Kosten daher unnötig erhöhen. Zum Zeitpunkt der Erstellung dieses Lernprogramms betragen die Kosten 0,10 Dollar pro Million Transaktionen (eine Abfrage zählt als eine Transaktion). Der Ruhezustand könnte also auf wesentlich weniger als eine Minute verringert werden, und die Kosten für das Durchsuchen der Tabellen nach zu sendenden Nachrichten wären dennoch minimal. Weitere Informationen über die Preisgestaltung finden Sie im [ersten Lernprogramm][ersten Lernprogramm der Serie].

**Hinweis zum Threading und zur optimalen CPU-Auslastung:** Die `Run`-Methode enthält zwei Aufgaben (Aufnehmen von E-Mail in eine Warteschlange und Prüfen auf abgeschlossene Nachrichten), die in einem Thread nacheinander ausgeführt werden. Ein kleiner virtueller Computer verfügt über 1,75 GB RAM und nur eine CPU, daher können diese Aufgaben wahrscheinlich ohne weiteres in einem einzelnen Thread nacheinander ausgeführt werden. Angenommen, Ihre Anwendung benötigt mehr Arbeitsspeicher, als auf dem kleinen virtuellen Computer zur Verfügung steht, um effizient ausgeführt zu werden. Ein mittlerer virtueller Computer verfügt über 3,5 GB RAM und zwei CPUs, diese Anwendung verwendet jedoch nur eine CPU, da sie nur einen Thread umfasst. Um alle CPUs zu nutzen, müssten Sie für jede CPU einen Arbeitsthread erstellen. Auch in diesem Fall wird eine einzelne CPU von einem Thread nicht voll ausgelastet. Wenn ein Thread Netzwerk- oder E/A-Aufrufe ausführt, muss er auf den Abschluss des E/A- oder Netzwerkaufrufs warten. Während dieser Zeit führt er keine nützliche Arbeit aus. Wenn die `Run`-Methode mithilfe von zwei Threads implementiert würde, könnte der eine Thread nutzbringend eingesetzt werden, während der andere auf den Abschluss einer Netzwerk- oder E/A-Operation wartet.

### Die ProcessMessage-Methode

Die `ProcessMessage`-Methode ruft alle E-Mail-Adressen für die E-Mail-Zielliste ab und erstellt eine Warteschlangenarbeitsaufgabe für jede E-Mail-Adresse. Beim Erstellen der Warteschlangenarbeitsaufgaben werden auch `SendEmail`-Zeilen in der Tabelle `Message` erstellt. Über diese Zeilen erhält Workerrolle B die Informationen, die zum Senden von E-Mails benötigt werden. Außerdem enthalten diese Zeilen eine `EmailSent`-Eigenschaft, mit der erfasst wird, ob die einzelnen E-Mails gesendet wurden.

        private void ProcessMessage(Message messageToProcess, string restartFlag)
        {
            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;
            }
            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(
               TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
               TableOperators.And,
               TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();
            foreach (Subscriber subscriber in subscribers)
            {
                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)
                {
                    Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                    continue;
                }
                // Create a SendEmail entity for this email.              
                var sendEmailRow = new SendEmail
                {
                    PartitionKey = messageToProcess.PartitionKey,
                    RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                    EmailAddress = subscriber.EmailAddress,
                    EmailSent = false,
                    MessageRef = messageToProcess.MessageRef,
                    ScheduledDate = messageToProcess.ScheduledDate,
                    FromEmailAddress = mailingList.FromEmailAddress,
                    SubjectLine = messageToProcess.SubjectLine,
                    SubscriberGUID = subscriber.SubscriberGUID,
                    ListName = mailingList.ListName
                };
                // When we try to add the entity to the SendEmail table, 
                // an exception might happen if this worker role went 
                // down after processing some of the email addresses and then restarted.
                // In that case the row might already be present, so we do an Upsert operation.
                try
                {
                    var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                    messageTable.Execute(upsertOperation);
                }
                catch (Exception ex)
                {
                    string err = "Error creating SendEmail row:  " + ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException;
                    }
                    Trace.TraceError(err);
                }
                // Create the queue message.
                string queueMessageString =
                    sendEmailRow.PartitionKey + "," +
                    sendEmailRow.RowKey + "," +
                    restartFlag;
                var queueMessage = new CloudQueueMessage(queueMessageString);
                sendEmailQueue.AddMessage(queueMessage);
            }
            Trace.TraceInformation("ProcessMessage end PK: "
                + messageToProcess.PartitionKey);
        }

Der Code ruft zuerst die Adressenlistenzeile aus der Tabelle `mailinglist` für die Zieladressenliste ab. Diese Zeile enthält die "Von"-E-Mail-Adresse, die der Workerrolle B zum Senden von E-Mails zur Verfügung gestellt werden muss.

            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;
            }

Anschließend wird die Tabelle `mailinglist` nach allen Abonnentenzeilen abgefragt, um die Zieladressenliste zu erstellen.

            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(
               TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
               TableOperators.And,
               TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();

In der Schleife, die die Abfrageergebnisse verarbeitet, wird mit dem Code geprüft, ob die E-Mail-Adresse des Abonnenten bestätigt ist. Wenn dies nicht der Fall ist, wird keine E-Mail in die Warteschlange aufgenommen.

                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)
                {
                    Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                    continue;
                }

Anschließend erzeugt der Code eine `SendEmail`-Zeile in der Tabelle `message`. Diese Zeile enthält die Informationen, die Workerrolle B zum Senden einer E-Mail verwendet. Die Zeile wird erstellt, wobei die `EmailSent`-Eigenschaft auf `false` festgelegt ist.

                // Create a SendEmail entity for this email.              
                var sendEmailRow = new SendEmail
                {
                    PartitionKey = messageToProcess.PartitionKey,
                    RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                    EmailAddress = subscriber.EmailAddress,
                    EmailSent = false,
                    MessageRef = messageToProcess.MessageRef,
                    ScheduledDate = messageToProcess.ScheduledDate,
                    FromEmailAddress = mailingList.FromEmailAddress,
                    SubjectLine = messageToProcess.SubjectLine,
                    SubscriberGUID = subscriber.SubscriberGUID,
                    ListName = mailingList.ListName
                };
                try
                {
                    var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                    messageTable.Execute(upsertOperation);
                }
                catch (Exception ex)
                {
                    string err = "Error creating SendEmail row:  " + ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException;
                    }
                    Trace.TraceError(err);
                }

Der Code verwendet eine "upsert"-Operation, da die Zeile eventuell bereits vorhanden ist, wenn Workerrolle A nach einem Fehler neu gestartet wird.

Die letzte Aufgabe, die für jede E-Mail-Adresse ausgeführt werden muss, besteht darin, die Warteschlangearbeitsaufgabe zu erstellen, durch die Workerrolle B veranlasst wird, eine E-Mail zu senden. Die Warteschlangearbeitsaufgabe enthält den Partitionsschlüssel und den Zeilenschlüsselwert der soeben erstellten `SendEmail`-Zeile sowie das zuvor festgelegte Neustartkennzeichen. Die `SendEmail`-Zeile enthält alle Informationen, die Workerrolle B benötigt, um eine E-Mail zu senden.

                // Create the queue message.
                string queueMessageString =
                    sendEmailRow.PartitionKey + "," +
                    sendEmailRow.RowKey + "," +
                    restartFlag;
                var queueMessage = new CloudQueueMessage(queueMessageString);
                sendEmailQueue.AddMessage(queueMessage);

### Die CheckAndUpdateStatusIfComplete-Methode

Mit der `CheckAndUpdateStatusIfComplete`-Methode werden Nachrichten geprüft, die sich im Status "Verarbeitung" befinden, um festzustellen, ob alle E-Mails gesendet wurden. Werden keine nicht gesendeten E-Mails gefunden, wird der Zeilenstatus auf `Completed` aktualisiert und die Zeile archiviert.

        private void CheckAndArchiveIfComplete(Message messageToCheck)
        {
            // Get the list of emails to be sent for this message: all SendEmail rows
            // for this message.  
            string pkrkFilter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToCheck.PartitionKey),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "message"));
            var query = new TableQuery<SendEmail>().Where(pkrkFilter);
            var emailToBeSent = messageTable.ExecuteQuery(query).FirstOrDefault();
            if (emailToBeSent != null)
            {
                return;
            }
            // All emails have been sent; copy the message row to the archive table.
            // Insert the message row in the messagearchive table
            var messageToDelete = new Message { PartitionKey = messageToCheck.PartitionKey, RowKey = messageToCheck.RowKey, ETag = "*" };
            messageToCheck.Status = "Complete";
            var insertOrReplaceOperation = TableOperation.InsertOrReplace(messageToCheck);
            messagearchiveTable.Execute(insertOrReplaceOperation);
            // Delete the message row from the message table.
            var deleteOperation = TableOperation.Delete(messageToDelete);
            messageTable.Execute(deleteOperation);
        }

## <a name="testing"></a>Testen von Workerrolle A

1.  Drücken Sie F5, um die Anwendung auszuführen.

2.  Verwenden Sie die Administrator-Webseiten, um eine Adressenliste sowie Abonnenten für die Adressenliste zu erstellen. Legen Sie die Eigenschaft `Verified` für mindestens einen der Abonnenten auf `true` fest, und stellen Sie als E-Mail-Adresse eine Adresse ein, unter der Sie Mail empfangen können.

    E-Mails werden erst gesendet, wenn Sie Workerrolle B implementieren. Zum Testen von Workerrolle B verwenden Sie jedoch dieselben Testdaten.

3.  Erstellen Sie eine Nachricht, die an die von Ihnen erstellte Adressenliste gesendet werden soll, und legen Sie als geplantes Datum heute oder einen Tag in der Vergangenheit fest.

    ![Neue Nachricht mit dem Status "Ausstehend"][Neue Nachricht mit dem Status "Ausstehend"]

4.  Wenn Sie nach etwas mehr als einer Minute (wegen der einminütigen Ruhezeit der Run-Methode) die Webseite "Nachrichten" aktualisieren, sehen Sie, dass sich der Status in "Verarbeitung" ändert. (Eventuell sehen Sie zunächst einen Wechsel zu "Queuing", wahrscheinlich erfolgt jedoch der Wechsel von "Queuing" zu "Verarbeitung" so schnell, dass Sie "Queuing" gar nicht sehen.)

    ![Neue Nachricht mit dem Status "Verarbeitung"][Neue Nachricht mit dem Status "Verarbeitung"]

5.  Öffnen Sie den Server-Explorer, und öffnen Sie den Knoten für den Entwicklungsspeicher.

6.  Erweitern Sie **Warteschlangen**, klicken Sie mit der rechten Maustaste auf **azuremailqueue**, und klicken Sie dann auf **Warteschlange anzeigen**.

    Für jeden verifizierten Abonnenten in Ihrer E-Mail-Zielliste sehen Sie eine Warteschlangennachricht.

    ![Warteschlangennachricht in Azure-Speicher-Explorer][Warteschlangennachricht in Azure-Speicher-Explorer]

7.  Doppelklicken Sie auf eine Warteschlangennachricht.

    Sie sehen den Inhalt der Warteschlangennachricht: den Partitionsschlüssel (das Datum), den Zeilenschlüssel (den MessageRef-Wert und die E-Mail-Adresse) und das Neustartkennzeichen, jeweils getrennt durch ein Komma.

    ![Inhalt der Warteschlangennachricht in Azure-Speicher-Explorer][Inhalt der Warteschlangennachricht in Azure-Speicher-Explorer]

8.  Schließen Sie das Dialogfeld **Nachricht anzeigen**.

9.  Erweitern Sie den Knoten **Tabellen**, klicken Sie mit der rechten Maustaste auf die Tabelle **Message**, und klicken Sie auf **Tabelle anzeigen**.

    Sie sehen die geplante Nachricht mit "message" im Zeilenschlüssel und danach je eine Zeile für jeden verifizierten Abonnenten mit der E-Mail-Adresse im Zeilenschlüssel.

10. Doppelklicken Sie auf eine Zeile, deren Zeilenschlüssel "message" enthält, um den Inhalt der Zeile anzuzeigen, die von der Webrolle erstellt wurde.

    ![message-Zeile][message-Zeile]

11. Doppelklicken Sie auf eine Zeile, deren Zeilenschlüssel eine E-Mail-Adresse enthält, um den Inhalt der `SendEmail`-Zeile anzuzeigen, die von Workerrolle A erstellt wurde.

    ![SendEmail-Zeile in Tabelle "Nachricht"][SendEmail-Zeile in Tabelle "Nachricht"]

## <a name="nextsteps"></a>Nächste Schritte

Sie haben jetzt Workerrolle A erstellt und überprüft, ob sie die Warteschlangennachrichten und Tabellenzeilen erstellt, die Workerrolle B benötigt, um E-Mails zu senden. Im [nächsten Lernprogramm][nächsten Lernprogramm] erstellen und testen Sie Workerrolle B.

Links zu weiteren Ressourcen für die Arbeit mit Azure-Speichertabellen, Warteschlangen und Blobs finden Sie im [letzten Lernprogramms in dieser Serie][nächsten Lernprogramm].

<div><a href="/de-de/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/" class="site-arrowboxcta download-cta">Lernprogramm 5</a></div>

  [ersten Lernprogramm der Serie]: /de-de/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Hinzufügen eines Verweises auf das Webprojekt]: #addref
  [Hinzufügen des SendEmail-Modells]: #addmodel
  [Hinzufügen von Code, der beim Start der Workerrolle ausgeführt wird]: #addcode
  [Testen von Workerrolle A]: #testing
  [Nächste Schritte]: #nextsteps
  [Verweis auf MvcWebRole hinzufügen]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-reference-manager.png
  [Tabelle Message mit SendEMail]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-sendMailTbl.png
  [zweiten Lernprogramm]: /de-de/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [ServicePointManager.DefaultConnectionLimit]: http://msdn.microsoft.com/de-de/library/system.net.servicepointmanager.defaultconnectionlimit.aspx
  [ServicePointManager]: http://msdn.microsoft.com/de-de/library/system.net.servicepointmanager.aspx
  [Role Instance Restarts Due to OS Upgrades]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [UnhandledException]: http://msdn.microsoft.com/de-de/library/system.appdomain.unhandledexception.aspx
  [Neue Nachricht mit dem Status "Ausstehend"]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-pending.png
  [Neue Nachricht mit dem Status "Verarbeitung"]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-processing.png
  [Warteschlangennachricht in Azure-Speicher-Explorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue.png
  [Inhalt der Warteschlangennachricht in Azure-Speicher-Explorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue-detail.png
  [message-Zeile]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-message-table.png
  [SendEmail-Zeile in Tabelle "Nachricht"]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-sendemail-row.png
  [nächsten Lernprogramm]: /de-de/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
