<properties linkid="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" urlDisplayName="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 4: Worker role A" metaKeywords="Azure tutorial, .NET multi-tier app, multi-tier architecture" description="The fourth tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building worker role A (email scheduler) for the Azure Email Service application - 4 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Erstellen der Workerrolle A (E-Mail-Planer) für die Anwendung Azure-E-Mail-Dienst - 4 von 5
===========================================================================================

Dies ist das vierte von fünf Lernprogrammen, die erläutern, wie die Beispielanwendung Azure-E-Mail-Dienst erstellt und bereitgestellt wird. Weitere Informationen zur Anwendung und den Lernprogrammen finden Sie im [ersten Lernprogramm der Serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

In diesem Lernprogramm lernen Sie Folgendes:

-   Abfragen und Aktualisieren von Azure-Speichertabellen
-   Hinzufügen von Arbeitsaufgaben zu einer Warteschlange zur Verarbeitung durch eine andere Workerrolle
-   Behandeln des geplanten Herunterfahrens durch Außerkraftsetzen der `OnStop`-Methode
-   Behandeln des geplanten Herunterfahrens und Sicherstellen, dass keine E-Mails verpasst werden und keine doppelten E-Mails gesendet werden
-   Prüfen einer Workerrolle, die Azure-Speichertabellen verwendet, mithilfe von Azure-Speicher-Explorer

Das Projekt Workerrolle A wurde bereits während der Erstellung des Clouddienst-Projekts erstellt. Jetzt müssen Sie die Workerrolle lediglich programmieren und so konfigurieren, dass Sie Ihr Azure-Speicherkonto verwendet.

Hinzufügen eines ProjektverweisesHinzufügen eines Verweises auf das Webprojekt
------------------------------------------------------------------------------

Sie benötigen einen Verweis auf das Webprojekt, da dort die Entitätsklassen definiert werden. In Workerrolle B verwenden Sie dieselben Entitätsklassen, um Daten in den von der Anwendung verwendeten Azure-Tabellen zu lesen und zu schreiben.

**Hinweis:** In einer Produktionsanwendung legen Sie keinen Verweis auf ein Webprojekt aus einem Workerrollenprojekt fest, da dies zur Referenzierung einer Reihe abhängiger Assemblys führt, die nicht in der Workerrolle enthalten sein sollen oder die Sie dort nicht benötigen. Normalerweise verwalten Sie gemeinsam genutzte Modellklassen in einem Klassenbibliotheksprojekt, und sowohl Web- als auch Workerrollenprojekte verweisen auf das Klassenbibliotheksprojekt. Damit die Lösungsstruktur einfach bleibt, werden Modellklassen im Zuge dieses Lernprogramms im Webprojekt gespeichert.

1.  Klicken Sie mit der rechten Maustaste auf das Projekt WorkerRoleA, und wählen Sie **Verweis hinzufügen**.

    ![Verweis in Projekt WorkerRoleA hinzufügen](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-add-reference-menu.png)

2.  Fügen Sie im **Verweis-Manager** einen Verweis auf das Projekt MvcWebRole hinzu (oder auf das Projekt der Webanwendung, wenn Sie die Webbenutzeroberfläche in einer Azure-Website ausführen), und klicken Sie dann auf **OK**.

    ![Verweis auf MvcWebRole hinzufügen](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-reference-manager.png)

Hinzufügen eines SCL 1.7-VerweisesHinzufügen eines Verweises auf eine SCL 1.7-Assembly
--------------------------------------------------------------------------------------

> [WACOM.NOTE] Überspringen Sie diesen Schritt, wenn Sie SDK 2.3 oder höher installiert haben.

Da Version 2.0 der Speicherclientbibliothek (SCL) nicht über alle Funktionen verfügt, die für eine Diagnose benötigt werden, müssen Sie einen Verweis auf eine der 1.7-Assemblys hinzufügen. Wenn Sie die Schritte im vorherigen Lernprogramm ausgeführt haben, ist dies bereits geschehen. Die Anweisungen hier sind für den Fall vorgesehen, dass Sie diesen Schritt übersprungen haben.

1.  Klicken Sie mit der rechten Maustaste auf das Projekt WorkerRoleA, und wählen Sie **Verweis hinzufügen**.

2.  Klicken Sie unten im Dialogfeld auf die Schaltfläche **Durchsuchen...**.

3.  Navigieren Sie zum folgenden Ordner:

         C:\Programme\Microsoft SDKs\Windows Azure\.NET SDK\2012-10\ref

4.  Wählen Sie *Microsoft.WindowsAzure.StorageClient.dll* aus, und klicken Sie auf **Hinzufügen**.

5.  Klicken Sie im Dialogfeld **Verweis-Manager** auf **OK**.

Hinzufügen des SendEmail-ModellsHinzufügen des SendEmail-Modells
----------------------------------------------------------------

Workerrolle A erstellt die `SendEmail`-Zeilen in der Tabelle `Message`, und Workerrolle B liest diese Zeilen, um die zum Senden von E-Mails erforderlichen Informationen abzurufen. Die folgende Abbildung zeigt einen Teil der Eigenschaften für zwei `Message`-Zeilen und drei `SendEmail`-Zeilen in der Tabelle `Message`.

    ![message table with sendmail][mtas-sendMailTbl]

Diese Zeilen in der Tabelle `Message` dienen verschiedenen Zwecken:

-   Sie stellen alle Informationen bereit, die Workerrolle B benötigt, um eine einzelne E-Mail zu senden.
-   Sie erfassen, ob eine E-Mail gesendet wurde, um das Senden doppelter E-Mails zu vermeiden, falls eine Workerrolle nach einem Fehler neu gestartet wird.
-   Sie ermögliche es Workerrolle A festzustellen, wann alle E-Mails für eine Nachricht gesendet wurden, sodass die Nachricht als `Abgeschlossen` markiert werden kann.

Zum Lesen und Schreiben der `SendEmail`-Zeilen wird eine Modellklasse benötigt. Da diese sowohl für Workerrolle A als auch Workerrolle B zugänglich sein muss und da alle übrigen Modellklassen im Webprojekt definiert sind, ist es sinnvoll, diese Klasse ebenfalls im Webprojekt zu definieren.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Modellordner im Webprojekt, und wählen Sie **Vorhandenes Element hinzufügen**.

    ![Vorhandenes Element zum Modellordner im Webprojekt hinzufügen](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-add-existing-for-sendemail-model.png)

2.  Navigieren Sie zu dem Ordner, in den Sie die Beispielanwendung heruntergeladen haben, wählen Sie die Datei *SendEmail.cs* im Modellordner des Webprojekts aus, und klicken Sie auf "Hinzufügen".

3.  Öffnen Sie *SendEmail.cs*, und prüfen Sie den Code.

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

    Der Code hier ähnelt dem anderer Modellklassen, allerdings fehlen hier DataAnnotations-Attribute, da mit diesem Modell keine Benutzeroberfläche verknüpft ist, denn sie wird in einem MVC-Controller nicht verwendet.

Hinzufügen von WorkerrollencodeHinzufügen von Code, der beim Start der Workerrolle ausgeführt wird
--------------------------------------------------------------------------------------------------

1.  Öffnen Sie im Projekt WorkerRoleA die Datei *WorkerRole.cs*, und prüfen Sie den Code.

         public class WorkerRole : RoleEntryPoint
         {
             public override void Run()
             {
                 // Dies ist ein Beispiel für eine Workerimplementierung. Ersetzen Sie sie durch Ihre Logik.
                 Trace.WriteLine("WorkerRole1-Einstiegspunkt aufgerufen", "Informationen");

                 while (true)
                 {
                     Thread.Sleep(10000);
                     Trace.WriteLine("Wird ausgeführt", "Informationen");
                 }
             }

             public override bool OnStart()
             {
                 // Legen Sie die maximale Anzahl gleichzeitiger Verbindungen fest 
                 ServicePointManager.DefaultConnectionLimit = 12;

                 // Informationen zum Behandeln von Konfigurationsänderungen
                 // finden Sie im MSDN-Thema unter http://go.microsoft.com/fwlink/?LinkId=166357.

                 return base.OnStart();
             }
         }

    Dies ist der Standardvorlagencode für die Workerrolle. Es gibt eine `OnStart`-Methode, in die Sie Initialisierungscode einfügen können, der nur ausgeführt wird, wenn eine Instanz der Workerrolle gestartet wird, und eine `Run`-Methode, die nach Abschluss der `OnStart`-Methode aufgerufen wird. Sie ersetzen diesen Code durch Ihren eigenen Initialisierungs- und Ausführungscode.

2.  Löschen Sie die Datei *WorkerRole.cs*, klicken Sie anschließend mit der rechten Maustaste auf das Projekt WorkerRoleA, und wählen Sie **Vorhandenes Element hinzufügen**.

    ![Vorhandenes Element zu Workerrolle A hinzufügen](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-add-existing.png)

3.  Navigieren Sie zu dem Ordner, in den Sie die Beispielanwendung heruntergeladen haben, wählen Sie die Datei *WorkerRoleA.cs* im Projekt WorkerRoleA aus, und klicken Sie auf **Hinzufügen**.

4.  Öffnen Sie *WorkerRoleA.cs*, und prüfen Sie den Code.

    Mit der `OnStart`-Methode werden die Kontextobjekte initialisiert, die Sie für das Arbeiten mit Azure-Speicherentitäten benötigen. Damit wird außerdem sichergestellt, dass alle Tabellen, Warteschlangen und Blob-Container, die Sie in der `Run`-Methode verwenden, vorhanden sind. Der Code, mit dem diese Aufgaben ausgeführt werden, ähnelt dem weiter oben vorgestellten Code für die MVC-Controller-Konstruktoren. Später konfigurieren Sie die Verbindungszeichenfolge, die diese Methode verwendet.

         public override bool OnStart()
         {
             ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount;

             ConfigureDiagnostics();
             Trace.TraceInformation("Speicherkonto in WorkerA wird initialisiert");
             var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

             CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient(); 
             sendEmailQueue = queueClient.GetQueueReference("azuremailqueue"); 
             var tableClient = storageAccount.CreateCloudTableClient(); 
             mailingListTable = tableClient.GetTableReference("mailinglist"); 
             messageTable = tableClient.GetTableReference("message"); 
             messagearchiveTable = tableClient.GetTableReference("messagearchive"); 

             // Erstellen, falls für Warteschlange, Blob-Container, SentEmail-Tabelle noch nicht vorhanden. 
             sendEmailQueue.CreateIfNotExists(); 
             messageTable.CreateIfNotExists(); 
             mailingListTable.CreateIfNotExists(); 
             messagearchiveTable.CreateIfNotExists(); 

             return base.OnStart();
         }

    Sie haben zuvor möglicherweise Dokumentation zum Arbeiten mit Azure-Speicher gesehen, die den Initialisierungscode in einer Schleife zeigt, mit dem Transportfehler festgestellt werden. Dieser Code wird nicht mehr benötigt, da die API jetzt über einen integrierten Wiederholungsmechanismus verfügt, der vorübergehende Netzwerkfehler für bis zu drei zusätzliche Versuche abfängt.

    Die `ConfigureDiagnostics`-Methode, die von der `OnStart`-Methode aufgerufen wird, richtet die Ablaufverfolgung ein, sodass Sie die Ausgabe der Methoden `Trace.Information` und `Trace.Error` sehen können. Diese Methode wird im [zweiten Lernprogramm](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/) erläutert.

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
    -   Sie haben Ihren Clouddienst mit der Schaltfläche **Beenden** im Azure-Verwaltungsportal angehalten.
    -   Sie haben eine Aktualisierung Ihres Clouddienstprojekts bereitgestellt.

    Die `Run`-Methode überwacht die Variable `onStopCalled` und lädt keine neuen zu verarbeitenden Arbeitsaufgaben mehr, wenn diese Variable den Wert `true` erhält. Diese Koordination der Methoden `OnStop` und `Run` ermöglicht ein ordnungsgemäßes Herunterfahren des Workerprozesses.

    Azure installiert von Zeit zu Zeit Updates des Betriebssystems, um sicherzustellen, dass die Plattform sicher und zuverlässig arbeitet. Für diese Updates müssen die Computer, die Ihren Clouddienst hosten, in der Regel heruntergefahren und neu gestartet werden. Weitere Informationen finden Sie unter [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (Neustart von Rolleninstanzen aufgrund von Betriebssystemupdates – in englischer Sprache).

    Die `Run`-Methode führt zwei Funktionen aus:

    -   Sie durchsucht die Tabelle `message` nach Nachrichten, die heute oder eher gesendet werden sollen und für die noch keine Arbeitsaufgaben in der Warteschlange erstellt wurden.

    -   Sie durchsucht die Tabelle `message` nach Nachrichten mit einem Status, der angibt, dass alle Arbeitsaufgaben in der Warteschlange erstellt wurden, dass jedoch noch nicht alle E-Mails gesendet wurden. Wird eine Nachricht gefunden, werden die `SendEmail`-Zeilen nach dieser Nachricht durchsucht, um festzustellen, ob alle E-Mails gesendet wurden. Ist dies der Fall, wird der Status auf `Abgeschlossen` aktualisiert und die `message`-Zeile archiviert.

    Die Methode prüft auch die globale Variable `onStopCalled`. Wenn die Variable den Wert `true` hat, lädt die Methode keine neuen zu verarbeitenden Arbeitsaufgaben mehr und gibt zurück, wann bereits gestartete Aufgaben abgeschlossen sind.

         public override void Run()
         {
             Trace.TraceInformation("WorkerRoleA entering Run()");
             while (true)
             {
                 try
                 {
                     var tomorrow = DateTime.Today.AddDays(1.0).ToString("jjjj-MM-tt");
                     // Wenn OnStop aufgerufen wurde, Rückkehr, um ein ordnungsgemäßes Herunterfahren auszuführen.
                     if (onStopCalled == true)
                     {
                         Trace.TraceInformation("onStopCalled WorkerRoleB");
                         returnedFromRunMethod = true;
                         return;
                     }
                     // Alle Nachrichten abrufen, die für morgen oder früher geplant sind
                     // und den Status Ausstehend oder Queuing haben.
                     string typeAndDateFilter = TableQuery.CombineFilters(
                         TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                         TableOperators.And,
                         TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                     var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                     var messagesToProcess = messageTable.ExecuteQuery(query).ToList();
                     TableOperation replaceOperation;
                     // Jede Nachricht verarbeiten (zu sendende E-Mails in Warteschlange stellen).
                     foreach (Message messageToProcess in messagesToProcess)
                     {
                         string restartFlag = "0";
                         // Wenn die Nachricht bereits den Status Queuing hat,
                         // Kennzeichen zum Anzeigen des Neustarts setzen.
                         if (messageToProcess.Status == "Queuing")
                         {
                             restartFlag = "1";
                         }

                         // Wenn die Nachricht den Status Ausstehend hat,
                         // Status in Queuing ändern.
                         if (messageToProcess.Status == "Ausstehend")
                         {
                             messageToProcess.Status = "Queuing";
                             replaceOperation = TableOperation.Replace(messageToProcess);
                             messageTable.Execute(replaceOperation);
                         }

                         // Wenn die Nachricht den Status Queuing hat, 
                         // Nachricht verarbeiten und in Status Verarbeitung ändern;
                         // andernfalls befindet sie sich bereits im Status Verarbeitung; 
                         // in diesem Fall prüfen, ob die Verarbeitung abgeschlossen ist.
                         if (messageToProcess.Status == "Queuing")
                         {
                             ProcessMessage(messageToProcess, restartFlag);

                             messageToProcess.Status = "Verarbeitung";
                             replaceOperation = TableOperation.Replace(messageToProcess);
                             messageTable.Execute(replaceOperation);
                         }
                         else
                         {
                             CheckAndArchiveIfComplete(messageToProcess);
                         }
                     }

                     // Ruhezustand für eine Minute, um Abfragekosten zu minimieren. 
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
                     // Ablaufverfolgungsspeicher nicht auffüllen, wenn Warteschlangenprozessschleife einen Fehler enthält.
                     System.Threading.Thread.Sleep(1000 * 60);
                 }
             }
         }

    Sämtliche Arbeitsschritte werden in einer unendlichen Schleife in einem `while`-Block ausgeführt, und der gesamte Code im `while`-Block wird von einem `try`-`catch`-Block umschlossen, um unbehandelte Ausnahmen zu verhindern. Wenn eine unbehandelte Ausnahme auftritt, löst Azure das Ereignis [UnhandledException](http://msdn.microsoft.com/en-us/library/system.appdomain.unhandledexception.aspx) aus, der Workerprozess wird beendet, und die Rolle wird offline geschaltet. Die Workerrolle wird von Azure neu gestartet, dies dauert jedoch einige Minuten. Der `try`-Block ruft `TraceError` auf, um den Fehler aufzuzeichnen, und geht dann für 60 Sekunden in den Ruhezustand, damit die Fehlermeldung nicht zu häufig wiederholt wird, wenn der Fehler dauerhaft ist. In einer Produktionsanwendung können Sie einem Administrator im `try`-Block eine E-Mail senden.

    Die `Run`-Methode verarbeitet eine Abfrage von `message`-Zeilen in der Tabelle `message`, deren geplantes Datum vor morgen liegt:

                     // Alle Nachrichten abrufen, die für morgen oder früher geplant sind
                     // und den Status Ausstehend oder Queuing haben.
                     string typeAndDateFilter = TableQuery.CombineFilters(
                         TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                         TableOperators.And,
                         TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                     var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                     var messagesToProcess = messageTable.ExecuteQuery(query).ToList();

    **Hinweis:** Nachrichtenzeilen nach ihrer Verarbeitung in die Tabelle `messagearchive` zu verschieben, bietet u. a. den Vorteil, dass bei der entsprechenden Abfrage nur `PartitionKey` und `RowKey` als Suchkriterien angegeben werden müssen. Wenn verarbeitete Zeilen nicht archiviert würden, müsste in der Abfrage außerdem ein Nichtschlüsselfeld (`Status`) angegeben werden, und es müssten mehr Zeilen durchsucht werden. Die Tabelle würde größer werden, die Abfrage würde länger dauern und möglicherweise Fortsetzungstokens erhalten.

    Wenn eine Nachricht den Status `Ausstehend` hat, hat die Verarbeitung noch nicht begonnen; wenn sie den Status `Queuing` hat, hat die Verarbeitung bereits begonnen, wurde jedoch unterbrochen, bevor alle Warteschlangennachrichten erstellt wurden. In diesem Fall muss in Workerrolle B eine zusätzliche Prüfung vorgenommen werden, wenn sie die einzelnen E-Mails sendet, um sicherzustellen, dass die E-Mail noch nicht gesendet wurde. Das ist der Zweck der Variable `restartFlag`.

                         string restartFlag = "0";
                         if (messageToProcess.Status == "Queuing")
                         {
                             restartFlag = "1";
                         }

    Anschließend legt der Code `message`-Zeilen mit dem Status `Ausstehend` auf `Queuing` fest. Für diese Zeilen sowie für alle Zeilen, die bereits den Status `Queuing` hatten, wird danach die `ProcessMessage`-Methode aufgerufen, um die Warteschlangenarbeitsaufgaben zum Senden von E-Mails für die Nachricht zu erstellen.

                         if (messageToProcess.Status == "Ausstehend")
                         {
                             messageToProcess.Status = "Queuing";
                             replaceOperation = TableOperation.Replace(messageToProcess);
                             messageTable.Execute(replaceOperation);
                         }

                         if (messageToProcess.Status == "Queuing")
                         {
                             ProcessMessage(messageToProcess, restartFlag);

                             messageToProcess.Status = "Verarbeitung";
                             replaceOperation = TableOperation.Replace(messageToProcess);
                             messageTable.Execute(replaceOperation);
                         }
                         else
                         {
                             CheckAndArchiveIfComplete(messageToProcess);
                         }

    Nach der Verarbeitung einer Nachricht im Status `Queuing` legt der Code den Status der `Message`-Zeile auf `Verarbeitung` fest. Zeilen in der Tabelle `message`, die nicht den Status `Ausstehend` oder `Queuing` haben, befinden sich bereits im Status `Verarbeitung`. Für diese Zeilen ruft der Code eine Methode auf, die prüft, ob alle E-Mails für die Nachricht gesendet wurden. Wenn alle E-Mails gesendet wurden, wird die `message`-Zeile archiviert.

    Nach der Verarbeitung aller von der Abfrage abgerufenen Datensätze geht der Code für eine Minute in den Ruhezustand.

                 // Ruhezustand für eine Minute, um Abfragekosten zu minimieren.
                 System.Threading.Thread.Sleep(1000 * 60);

    Für jede Azure-Speicherabfrage fallen minimale Gebühren an, auch wenn keine Daten zurückgegeben werden. Ein ständiges Neuscannen würde Ihre Azure-Kosten daher unnötig erhöhen. Zum Zeitpunkt der Erstellung dieses Lernprogramms betragen die Kosten 0,10 Dollar pro Million Transaktionen (eine Abfrage zählt als eine Transaktion). Der Ruhezustand könnte also auf wesentlich weniger als eine Minute verringert werden, und die Kosten für das Durchsuchen der Tabellen nach zu sendenden Nachrichten wären dennoch minimal. Weitere Informationen über die Preisgestaltung finden Sie im [ersten Lernprogramm](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

    **Hinweis zum Threading und zur optimalen CPU-Auslastung:** Die `Run`-Methode enthält zwei Aufgaben (Aufnehmen von E-Mail in eine Warteschlange und Prüfen auf abgeschlossene Nachrichten), die in einem Thread nacheinander ausgeführt werden. Ein kleiner virtueller Computer verfügt über 1,75 GB RAM und nur eine CPU, daher können diese Aufgaben wahrscheinlich ohne weiteres in einem einzelnen Thread nacheinander ausgeführt werden. Angenommen, Ihre Anwendung benötigt mehr Arbeitsspeicher, als auf dem kleinen virtuellen Computer zur Verfügung steht, um effizient ausgeführt zu werden. Ein mittlerer virtueller Computer verfügt über 3,5 GB RAM und zwei CPUs, diese Anwendung verwendet jedoch nur eine CPU, da sie nur einen Thread umfasst. Um alle CPUs zu nutzen, müssten Sie für jede CPU einen Arbeitsthread erstellen. Auch in diesem Fall wird eine einzelne CPU von einem Thread nicht voll ausgelastet. Wenn ein Thread Netzwerk- oder E/A-Aufrufe ausführt, muss er auf den Abschluss des E/A- oder Netzwerkaufrufs warten. Während dieser Zeit führt er keine nützliche Arbeit aus. Wenn die `Run`-Methode mithilfe von zwei Thread implementiert würde, könnte der eine Thread nutzbringend eingesetzt werden, während der andere auf den Abschluss einer Netzwerk- oder E/A-Operation wartet.

    Die `ProcessMessage`-Methode ruft alle E-Mail-Adressen für die E-Mail-Zielliste ab und erstellt eine Warteschlangenarbeitsaufgabe für jede E-Mail-Adresse. Beim Erstellen der Warteschlangenarbeitsaufgaben werden auf `SendEmail`-Zeilen in der Tabelle `Message` erstellt. Über diese Zeilen erhält Workerrolle B die Informationen, die zum Senden von E-Mails benötigt werden. Außerdem enthalten diese Zeilen eine `EmailSent`-Eigenschaft, mit der erfasst wird, ob die einzelnen E-Mails gesendet wurden.

         private void ProcessMessage(Message messageToProcess, string restartFlag)
         {
             // Adressenlisten-Info abrufen, um die "Von"-E-Mail-Adresse zu erhalten.
             var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var mailingList = retrievedResult.Result as MailingList;
             if (mailingList == null)
             {
                 Trace.TraceError("Adressenliste nicht gefunden: " + messageToProcess.ListName + " für Nachricht: " + messageToProcess.MessageRef);
                 return;
             }
             // E-Mail-Adressen für diese Adressenliste abrufen.
             string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
             var query = new TableQuery<Subscriber>().Where(filter);
             var subscribers = mailingListTable.ExecuteQuery(query).ToList();

             foreach (Subscriber subscriber in subscribers)
             {
                 // Prüfen, ob die E-Mail-Adresse des Abonnenten verifiziert wurde.
                 if (subscriber.Verified == false)
                 {
                     Trace.TraceInformation("Abonnent " + subscriber.EmailAddress + " nicht verifiziert, daher kein Queuing ");
                     continue;
                 }

                 // Für diese E-Mail eine SendEmail-Entität erstellen.              
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

                 // Beim Versuch, die Entität der SendEmail-Tabelle hinzuzufügen, 
                 // kann eine Ausnahme ausgelöst werden, wenn diese Workerrolle nach 
                 // der Verarbeitung einiger E-Mail-Adressen heruntergefahren und dann neu gestartet wird.
                 // In diesem Fall ist die Zeile eventuell bereits vorhanden, daher wird eine Upsert-Operation ausgeführt.
                 try
                 {
                     var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                     messageTable.Execute(upsertOperation);
                 }
                 catch (Exception ex)
                 {
                     string err = "Fehler beim Erstellen der SendEmail-Zeile:  " + ex.Message;
                     if (ex.InnerException != null)
                     {
                         err += " Inner Exception: " + ex.InnerException;
                     }
                     Trace.TraceError(err);
                 }

                 // Die Warteschlangennachricht erstellen.
                 string queueMessageString =
                     sendEmailRow.PartitionKey + "," +
                     sendEmailRow.RowKey + "," +
                     restartFlag;
                 var queueMessage = new CloudQueueMessage(queueMessageString);
                 sendEmailQueue.AddMessage(queueMessage);
             }

             Trace.TraceInformation("ProcessMessage-Ende PK: "
                 + messageToProcess.PartitionKey);
         }

    Der Code ruft zuerst die Adressenlistenzeile aus der Tabelle `mailinglist` für die Zieladressenliste ab. Diese Zeile enthält die "Von"-E-Mail-Adresse, die der Workerrolle B zum Senden von E-Mails zur Verfügung gestellt werden muss.

             // Adressenlisten-Info abrufen, um die "Von"-E-Mail-Adresse zu erhalten.
             var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var mailingList = retrievedResult.Result as MailingList;
             if (mailingList == null)
             {
                 Trace.TraceError("Adressenliste nicht gefunden: " + messageToProcess.ListName + " für Nachricht: " + messageToProcess.MessageRef);
                 return;
             }

    Anschließend wird die Tabelle `mailinglist` nach allen Abonnentenzeilen abgefragt, um die Zieladressenliste zu erstellen.

             // E-Mail-Adressen für diese Adressenliste abrufen.
             string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
             var query = new TableQuery<Subscriber>().Where(filter);
             var subscribers = mailingListTable.ExecuteQuery(query).ToList();

    In der Schleife, die die Abfrageergebnisse verarbeitet, wird mit dem Code geprüft, ob die E-Mail-Adresse des Abonnenten bestätigt ist. Wenn dies nicht der Fall ist, wird keine E-Mail in die Warteschlange aufgenommen.

                 // Prüfen, ob die E-Mail-Adresse des Abonnenten verifiziert wurde.
                 if (subscriber.Verified == false)
                 {
                     Trace.TraceInformation("Abonnent " + subscriber.EmailAddress + " nicht verifiziert, daher kein Queuing ");
                     continue;
                 }

    Anschließend erzeugt der Code eine `SendEmail`-Zeile in der Tabelle `message`. Diese Zeile enthält die Informationen, die Workerrolle B zum Senden einer E-Mail verwendet. Die Zeile wird erstellt, wobie die `EmailSent`-Eigenschaft auf `false` festgelegt ist.

                 // Für diese E-Mail eine SendEmail-Entität erstellen.              
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
                     string err = "Fehler beim Erstellen der SendEmail-Zeile:  " + ex.Message;
                     if (ex.InnerException != null)
                     {
                         err += " Inner Exception: " + ex.InnerException;
                     }
                     Trace.TraceError(err);
                 }

    Der Code verwendet eine "upsert"-Operation, da die Zeile eventuell bereits vorhanden ist, wenn Workerrolle A nach einem Fehler neu gestartet wird.

    Die letzte Aufgabe, die für jede E-Mail-Adresse ausgeführt werden muss, besteht darin, die Warteschlangearbeitsaufgabe zu erstellen, durch die Workerrolle B veranlasst wird, eine E-Mail zu senden. Die Warteschlangearbeitsaufgabe enthält den Partitionsschlüssel und den Zeilenschlüsselwert der soeben erstellten `SendEmail`-Zeile sowie das zuvor festgelegte Neustartkennzeichen. Die `SendEmail`-Zeile enthält alle Informationen, die Workerrolle B benötigt, um eine E-Mail zu senden.

                 // Die Warteschlangennachricht erstellen.
                 string queueMessageString =
                     sendEmailRow.PartitionKey + "," +
                     sendEmailRow.RowKey + "," +
                     restartFlag;
                 var queueMessage = new CloudQueueMessage(queueMessageString);
                 sendEmailQueue.AddMessage(queueMessage);

    Mit der `CheckAndUpdateStatusIfComplete`-Methode werden Nachrichten geprüft, die sich im Status "Verarbeitung" befinden, um festzustellen, ob alle E-Mails gesendet wurden. Werden keine nicht gesendeten E-Mails gefunden, wird der Zeilenstatus auf `Abgeschlossen` aktualisiert und die Zeile archiviert.

        private void CheckAndArchiveIfComplete(Message messageToCheck)
        {
            // Liste der für diese Nachricht zu sendenden E-Mails abrufen: all SendEmail rows
            // für diese Nachricht.  
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

            // Alle E-Mails wurden gesendet; Nachrichtenzeile in die Archivtabelle kopieren.

            // Nachrichtenzeile in die Tabelle messagearchive einfügen
            var messageToDelete = new Message { PartitionKey = messageToCheck.PartitionKey, RowKey = messageToCheck.RowKey, ETag = "*" };
            messageToCheck.Status = "Abgeschlossen";
            var insertOrReplaceOperation = TableOperation.InsertOrReplace(messageToCheck);
            messagearchiveTable.Execute(insertOrReplaceOperation);

            // Nachrichtenzeile aus der Tabelle message löschen.
            var deleteOperation = TableOperation.Delete(messageToDelete);
            messageTable.Execute(deleteOperation);
        }

Konfigurieren des SpeichersKonfigurieren der Verbindungszeichenfolge für den Speicher
-------------------------------------------------------------------------------------

Wenn Sie die Speicherkonto-Anmeldeinformationen für Workerrolle A beim Konfigurieren der Webrolle noch nicht konfiguriert haben, tun Sie dies jetzt.

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **WorkerRoleA** unter **Rollen** im Cloudprojekt **AzureEmailService**, und wählen Sie anschließend **Eigenschaften**.

2.  Stellen Sie sicher, dass in der Dropdownliste **Dienstkonfiguration** der Eintrag **Alle Konfigurationen** ausgewählt ist.

3.  Wählen Sie die Registerkarte **Einstellungen**, und klicken Sie auf **Einstellung hinzufügen**.

4.  Geben Sie in der Spalte **Name** die *Speicher-Verbindungszeichenfolge* ein.

5.  Wählen Sie in der Dropdownliste **Typ** den Eintrag **Verbindungszeichenfolge** aus.

6.  Klicken Sie auf die Auslassungszeichen (**...**) am rechten Ende der Zeile, um eine neue Verbindungszeichenfolge zu erstellen.

7.  Klicken Sie im Dialogfeld **Verbindungszeichenfolge für Speicherkonto** auf **Ihr Abonnement**.

8.  Wählen Sie das richtige **Abonnement** und den richtigen **Kontonamen**, und klicken Sie auf **OK**.

9.  Legen Sie die Diagnoseverbindungszeichenfolge fest. Sie können für die Diagnoseverbindungszeichenfolge das gleiche Speicherkonto verwenden. Es empfiehlt sich jedoch, für Ablaufverfolgungsinformationen (Diagnoseinformationen) ein anderes Speicherkonto zu verwenden.

TestenTesten der Workerrolle A
------------------------------

1.  Drücken Sie F5, um die Anwendung auszuführen.

> [WACOM.NOTE] Mit Visual Studio 2013 und dem neuesten SDK wird für den Verweis auf `LogLevel` eventuell der Fehler "mehrdeutiger Verweis" angezeigt. Klicken Sie mit der rechten Maustaste auf `LogLevel`, klicken Sie auf "Auflösen", und wählen Sie `Microsoft.WindowsAzure.Diagnostics.LogLevel`.

1.  Verwenden Sie die Administrator-Webseiten, um eine Adressenliste sowie Abonnenten für die Adressenliste zu erstellen. Legen Sie die Eigenschaft `Verified` für mindestens einen der Abonnenten auf `true` fest, und stellen Sie als E-Mail-Adresse eine Adresse ein, unter der Sie Mail empfangen können.

    E-Mails werden erst gesendet, wenn Sie Workerrolle B implementieren. Zum Testen von Workerrolle B verwenden Sie jedoch dieselben Testdaten.

2.  Erstellen Sie eine Nachricht, die an die von Ihnen erstellte Adressenliste gesendet werden soll, und legen Sie als geplantes Datum heute oder einen Tag in der Vergangenheit fest.

    ![Neue Nachricht mit dem Status "Ausstehend"](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-pending.png)

3.  Wenn Sie nach etwas mehr als einer Minute (wegen der einminütigen Ruhezeit der Run-Methode) die Webseite "Nachrichten" aktualisieren, sehen Sie, dass sich der Status in "Verarbeitung" ändert. (Eventuell sehen Sie zunächst einen Wechsel zu "Queuing", wahrscheinlich erfolgt jedoch der Wechsel von "Queuing" zu "Verarbeitung" so schnell, dass Sie "Queuing" gar nicht sehen.)

    ![Neue Nachricht mit dem Status "Verarbeitung"](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-processing.png)

4.  Öffnen Sie Azure-Speicher-Explorer, und wählen Sie Ihr Test-Speicherkonto aus.

5.  Wählen Sie in Azure-Speicher-Explorer unter **Speichertyp** die Option **Warteschlangen**, und wählen Sie **azuremailqueue** aus.

    Für jeden verifizierten Abonnenten in Ihrer E-Mail-Zielliste sehen Sie eine Warteschlangennachricht.

    ![Warteschlangennachricht in Azure-Speicher-Explorer](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue.png)

6.  Doppelklicken Sie auf eine Warteschlangennachricht, und wählen Sie dann im Dialogfeld **Nachrichtendetail** die Registerkarte **Nachricht** aus.

    Sie sehen den Inhalt der Warteschlangennachricht: den Partitionsschlüssel (Datum 2012-12-14), den Zeilenschlüssel (den MessageRef-Wert und die E-Mail-Adresse) und das Neustartkennzeichen, jeweils getrennt durch ein Komma.

    ![Inhalt der Warteschlangennachricht in Azure-Speicher-Explorer](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue-detail.png)

7.  Schließen Sie das Dialogfeld **Nachrichtendetail**.

8.  Wählen Sie unter **Speichertyp** den Eintrag **Tabellen**, und wählen Sie dann die Tabelle **Nachricht** aus.

9.  Klicken Sie auf **Abfrage**, um alle Zeilen der Tabelle anzuzeigen.

    Sie sehen die geplante Nachricht mit "Message" im Zeilenschlüssel und danach je eine Zeile für jeden verifizierten Abonnenten mit der E-Mail-Adresse im Zeilenschlüssel.

    ![Zeilen der Tabelle "Nachricht" in Azure-Speicher-Explorer](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-message-table.png)

10. Doppelklicken Sie auf eine Zeile, deren Zeilenschlüssel eine E-Mail-Adresse enthält, um den Inhalt der `SendEmail`-Zeile anzuzeigen, die von Workerrolle A erstellt wurde.

    ![SendEmail-Zeile in Tabelle "Nachricht"](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-sendemail-row.png)

Nächste SchritteNächste Schritte
--------------------------------

Sie haben jetzt Workerrolle A erstellt und überprüft, ob sie die Warteschlangennachrichten und Tabellenzeilen erstellt, die Workerrolle B benötigt, um E-Mails zu senden. Im [nächsten Lernprogramm](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/) erstellen und testen Sie Workerrolle B.

Links zu weiteren Ressourcen für die Arbeit mit Azure-Speichertabellen, Warteschlangen und Blobs finden Sie am Ende des [letzten Lernprogramms in dieser Serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).
[Lernprogramm 5](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/)

