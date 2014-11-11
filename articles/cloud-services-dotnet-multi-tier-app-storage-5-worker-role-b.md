<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Erstellen der Workerrolle B (E-Mail-Versand) für die Anwendung Azure-E-Mail-Dienst - 5 von 5

Dies ist das fünfte von fünf Lernprogrammen, in denen die Erstellung und Bereitstellung der Azure-E-Mail-Beispielanwendung beschrieben wird. Weitere Informationen zur Anwendung und den Lernprogrammen finden Sie im [ersten Lernprogramm der Serie][ersten Lernprogramm der Serie].

In diesem Lernprogramm lernen Sie Folgendes:

-   Hinzufügen einer Workerrolle zu einem Cloud-Dienst-Projekt
-   Prüfen einer Warteschlange und Verarbeiten von Arbeitsaufgaben aus der Warteschlange
-   Senden von E-Mails mit SendGrid
-   Behandeln des geplanten Herunterfahrens durch Außerkraftsetzen der `OnStop`-Methode
-   Behandeln des ungeplanten Herunterfahrens und Sicherstellen, dass keine doppelten E-Mails gesendet werden

## Abschnitte dieses Lernprogramms

-   [Hinzufügen des Projekts für Workerrolle B zur Projektmappe][Hinzufügen des Projekts für Workerrolle B zur Projektmappe]
-   [Hinzufügen eines Verweises auf das Webprojekt][Hinzufügen eines Verweises auf das Webprojekt]
-   [Hinzufügen des SendGrid NuGet-Pakets zum Projekt][Hinzufügen des SendGrid NuGet-Pakets zum Projekt]
-   [Hinzufügen von Projekteinstellungen][Hinzufügen von Projekteinstellungen]
-   [Hinzufügen von Code, der beim Start der Workerrolle ausgeführt wird][Hinzufügen von Code, der beim Start der Workerrolle ausgeführt wird]
-   [Testen von Workerrolle B][Testen von Workerrolle B]
-   [Nächste Schritte][Nächste Schritte]

## <a name="addworkerrole"></a><span class="short-header">Hinzufügen von Workerrolle B</span>Hinzufügen des Projekts für Workerrolle B zur Projektmappe

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Cloud-Dienst-Projekt, und wählen Sie **Neues Workerrollenprojekt**.

    ![Menü für neues Workerrollenprojekt][Menü für neues Workerrollenprojekt]

2.  Wählen Sie im Dialogfeld **Neues Rollenprojekt hinzufügen** die Option **C#**, wählen Sie **Workerrolle**, nennen Sie das Projekt WorkerRoleB, und klicken Sie auf **Hinzufügen**.

    ![Dialogfeld für neues Rollenprojekt][Dialogfeld für neues Rollenprojekt]

## <a name="addreference"></a>Hinzufügen eines Verweises auf das Webprojekt

Sie benötigen einen Verweis auf das Webprojekt, da dort die Entitätsklassen definiert werden. Sie verwenden die Entitätsklassen in Workerrolle B, um Daten in den von der Anwendung verwendeten Azure-Tabellen zu lesen und zu schreiben.

1.  Klicken Sie mit der rechten Maustaste auf das Projekt WorkerRoleB, und wählen Sie **Hinzufügen – Verweis** aus.

2.  Fügen Sie im Verweis-Manager einen Verweis auf das Projekt MvcWebRole hinzu.

    ![Verweis auf MvcWebRole hinzufügen][Verweis auf MvcWebRole hinzufügen]

## <a name="addsendgrid"></a>Hinzufügen des SendGrid NuGet-Pakets zum Projekt

Um E-Mails mit SendGrid zu senden, müssen Sie das SendGrid NuGet-Paket installieren.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt WorkerRoleB, und wählen Sie dann **Manage NuGet Packages**.

    ![NuGet-Pakete verwalten][NuGet-Pakete verwalten]

2.  Wählen Sie im Dialogfeld **Manage NuGet Packages** die Registerkarte **Online** aus, geben Sie "sendgrid" in das Suchfeld ein, und drücken Sie die Eingabetaste.

3.  Klicken Sie im **SendGrid**-Paket auf **Installieren**.

    ![Das SendGrid-Paket installieren][Das SendGrid-Paket installieren]

4.  Schließen Sie das Dialogfeld.

## <a name="addsettings"></a>Hinzufügen von Projekteinstellungen

Ebenso wie Workerrolle A benötigt Workerrolle B Anmeldeinformationen für ein Speicherkonto, um mit Tabellen, Warteschlangen und Blobs zu arbeiten. Zum Senden von E-Mails benötigt die Workerrolle darüber hinaus Anmeldeinformationen, die in Aufrufe des SendGrid-Diensts eingebettet werden. Und um einen Link zum Abbestellen zu konstruieren, der in von der Workerrolle gesendete E-Mails eingefügt wird, muss die Workerrolle die URL der Anwendung kennen. Diese Werte werden in Projekteinstellungen gespeichert.

Bei Speicherkonto-Anmeldeinformationen führen Sie das gleiche Verfahren wie im [dritten Lernprogramm][dritten Lernprogramm] aus.

1.  Klicken Sie im **Projektmappen-Explorer** unter **Rollen** im Cloudprojekt mit der rechten Maustaste auf **WorkerRoleB**, und wählen Sie **Eigenschaften**.

2.  Wählen Sie die Registerkarte **Einstellungen** aus.

3.  Stellen Sie sicher, dass in der Dropdownliste **Dienstkonfiguration** der Eintrag **Alle Konfigurationen** ausgewählt ist.

4.  Wählen Sie die Registerkarte **Einstellungen**, und klicken Sie auf **Einstellung hinzufügen**.

5.  Geben Sie "StorageConnectionString" in der Spalte **Name** ein.

6.  Wählen Sie in der Dropdownliste **Typ** den Eintrag **Verbindungszeichenfolge** aus.

7.  Klicken Sie auf die Auslassungsschaltfläche (**...**) rechts in der Zeile, um das Dialogfeld **Verbindungszeichenfolge für das Speicherkonto** zu öffnen.

8.  Klicken Sie im Dialogfeld **Verbindungszeichenfolge für Speicherkonto erstellen** auf das Optionsfeld **Azure-Speicheremulator** und klicken Sie dann auf **OK**.

Anschließend erstellen und konfigurieren Sie die drei neuen Einstellungen, die nur von Workerrolle B verwendet werden.

1.  Klicken Sie auf der Registerkarte **Einstellungen** im Fenster **Eigenschaften** auf **Einstellung hinzufügen**, und fügen Sie anschließend drei neue Einstellungen vom Typ **Zeichenfolge** hinzu:

    -   **Name**: SendGridUserName, **Wert**: der SendGrid-Benutzername, den Sie im [zweiten Lernprogramm][zweiten Lernprogramm] festgelegt haben.

    -   **Name**: SendGridPassword, **Wert**: das SendGrid-Kennwort.

    -   **Name**: AzureMailServiceURL, **Wert**: die Basis-URL, die die Anwendung erhält, wenn Sie sie bereitstellen, z. B.: <http://sampleurl.cloudapp.net>.

    ![Neue Einstellungen im Projekt WorkerRoleB][Neue Einstellungen im Projekt WorkerRoleB]

## <a name="addcode"></a>Hinzufügen von Code, der beim Start der Workerrolle ausgeführt wird

1.  Löschen Sie im Projekt WorkerRoleB die Datei WorkerRole.cs.

2.  Fügen Sie im Projekt WorkerRoleB die Datei WorkerRoleB.cs aus dem heruntergeladenen Projekt hinzu.

3.  Erstellen Sie die Projektmappe.

    Falls ein Buildfehler auftritt, sind möglicherweise mehrere Versionen des Azure Storage NuGet-Pakets vorhanden. Wechseln Sie in diesem Fall zur Behebung des Problems in den NuGet-Paket-Manager für die Projektmappe, wählen Sie "Installierte Pakete" aus, und scrollen Sie nach unten, um zu prüfen, ob zwei Instanzen des Azure Storage-Pakets vorhanden sind. Markieren Sie den Eintrag für Azure Storage 4.0.0, und löschen Sie ihn aus den Projekten, in denen das Paket installiert ist. Markieren Sie dann den Eintrag für Azure Storage 3.0.x, und installieren Sie das Paket in den Projekten, in denen es fehlt. Schließen Sie Visual Studio, starten Sie das Programm wieder, und erstellen Sie dann die Projektmappe erneut.

4.  Stellen Sie sicher, dass das Cloud-Dienst-Projekt noch das Startprojekt für die Projektmappe ist.

### Die OnStart-Methode

Wie Sie bereits bei Workerrolle A gesehen haben, initialisiert die `OnStart`-Methode die Kontextklassen, die Sie benötigen, um mit Azure-Speicherentitäten zu arbeiten. Damit wird außerdem sichergestellt, dass alle Tabellen, Warteschlangen und Blob-Container, die Sie in der `Run`-Methode benötigen, vorhanden sind.

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;
            // Read storage account configuration settings
            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in worker role B");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
            // Initialize queue storage 
            Trace.TraceInformation("Creating queue client.");
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            this.sendEmailQueue = queueClient.GetQueueReference("azuremailqueue");
            this.subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");
            // Initialize blob storage
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
            this.blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
            // Initialize table storage
            var tableClient = storageAccount.CreateCloudTableClient();
            tableServiceContext = tableClient.GetDataServiceContext();
            Trace.TraceInformation("WorkerB: Creating blob container, queue, tables, if they don't exist.");
            this.blobContainer.CreateIfNotExists();
            this.sendEmailQueue.CreateIfNotExists();
            this.subscribeQueue.CreateIfNotExists();
            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();
            return base.OnStart();
        }

Der Unterschied zu Workerrolle A besteht darin, dass der Blob-Container und die Abonnement-Warteschlange zu den zu erstellenden Ressourcen hinzugefügt werden, wenn sie noch nicht vorhanden sind. Mit dem Blob-Container rufen Sie die Dateien ab, die den HTML-Code und den unformatierten Text der E-Mail enthalten. Die Abonnement-Warteschlange dient zum Senden von Bestätigungs-E-Mails für Abonnements.

### Die Run-Methode

Mit der `Run`-Methode werden Arbeitsaufgaben aus zwei Warteschlangen verarbeitet: die Warteschlange, die für Nachrichten verwendet wird, die an E-Mail-Listen gesendet werden (von Workerrolle A erstellte Arbeitsaufgaben), und die Warteschlange, die für Bestätigungs-E-Mails für Abonnements verwendet wird (von der Abonnement-API-Methode im Projekt MvcWebRole erstellte Arbeitsaufgaben).

        public override void Run()
        {
            CloudQueueMessage msg = null;
            Trace.TraceInformation("WorkerRoleB start of Run()");
            while (true)
            {
                try
                {
                    bool messageFound = false;
                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }
                    // Retrieve and process a new message from the subscribe queue.
                    msg = subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }
                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    if (msg != null)
                    {
                        err += " Last queue message retrieved: " + msg.AsString;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in either process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

Dieser Code wird in einer Endlosschleife ausgeführt, bis die Workerrolle heruntergefahren wird. Wenn in der Hauptwarteschlange eine Arbeitsaufgabe gefunden wird, wird sie vom Code verarbeitet, und anschließend wird die Abonnement-Warteschlange geprüft.

                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = this.sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }
                    // Retrieve and process a new message from the subscribe queue.
                    msg = this.subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

Wenn in keiner der Warteschlangen Elemente warten, geht der Code für 60 Sekunden in den Ruhezustand, bevor die Schleife fortgesetzt wird.

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }

Dieser Ruhezustand soll die Azure Storage-Transaktionskosten minimieren, wie im [vorherigen Lernprogramm][vorherigen Lernprogramm] erläutert wurde.

Wenn ein Warteschlangenelement mit der [GetMessage][GetMessage]-Methode aus der Warteschlange abgerufen wird, wird das betreffende Warteschlangenelement für alle anderen Worker- und Webrollen, die auf die Warteschlange zugreifen, für 30 Sekunden unsichtbar. Auf diese Weise wird sichergestellt, dass nur eine Workerrolleninstanz eine bestimmte Warteschlangennachricht zur Verarbeitung abrufen kann. Sie können diese *Exklusivitätszeit* (die Zeit, während der das Warteschlangenelement unsichtbar ist) explizit festlegen, indem Sie der `GetMessage`-Methode einen Parameter für das [Sichtbarkeits-Zeitlimit][Sichtbarkeits-Zeitlimit] übergeben. Wenn die Workerrolle zur Verarbeitung einer Warteschlangennachricht mehr als 30 Sekunden benötigt, müssen Sie die Exklusivitätszeit erhöhen, um andere Rolleninstanzen daran zu hindern, die gleiche Nachricht zu verarbeiten.

Sie sollten die Exklusivitätszeit allerdings nicht auf einen zu großen Wert festlegen. Wenn die Exklusivitätszeit z. B. auf 48 Stunden festgelegt ist und die Workerrolle nach dem Entfernen einer Nachricht aus der Warteschlange unerwartet heruntergefahren wird, kann die Nachricht für 48 Stunden von keiner anderen Workerrolle verarbeitet werden. Die maximale Exklusivitätszeit beträgt sieben Tage.

Mit der [GetMessages][GetMessages]-Methode (beachten Sie das "s" am Ende des Namens) können bis zu 32 Nachrichten in einem Aufruf aus der Warteschlange abgerufen werden. Bei jedem Warteschlangenzugriff fallen geringe Transaktionskosten an; die Transaktionskosten sind gleich, unabhängig davon, ob 32 oder null Nachrichten zurückgegeben werden. Mit dem folgenden Code werden bis zu 32 Nachrichten in einem Aufruf abgerufen und verarbeitet.

        foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))
        {
            ProcessQueueMessage(msg);
            messageFound = true;
        }

Wenn Sie `GetMessages` verwenden, um mehrere Nachrichten zu entfernen, stellen Sie sicher, dass das Sichtbarkeits-Zeitlimit Ihrer Anwendung genügend Zeit zur Verarbeitung aller Nachrichten gibt. Nach dem Ablauf des Sichtbarkeits-Zeitlimits können andere Rolleninstanzen auf die Nachricht zugreifen, und sobald dies geschieht, kann die erste Instanz die Nachricht nicht löschen, wenn sie die Verarbeitung der Arbeitsaufgabe beendet hat.

### Die ProcessQueueMessage-Methode

Mit der `Run`-Methode wird `ProcessQueueMessage` aufgerufen, wenn in der Hauptwarteschlange eine Arbeitsaufgabe gefunden wird:

        private void ProcessQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.           
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison message:    message {0} Role Instance {1}.",
                    msg.ToString(), GetRoleInstance());
                sendEmailQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue.
            // Example:  2012-01-01,0123456789email@domain.com,0
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];
            Trace.TraceInformation("ProcessQueueMessage start:  partitionKey {0} rowKey {1} Role Instance {2}.",
                partitionKey, rowKey, GetRoleInstance());
            // If this is a restart, verify that the email hasn't already been sent.
            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    // SendEmail row is in archive, so email is already sent. 
                    // If there's a SendEmail Row in message table, delete it,
                    // and delete the queue message.
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }
                        // Get the row in the Message table that has data we need to send the email.
            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }
            // Derive blob names from the MessageRef.
            var htmlMessageBodyRef = emailRowInMessageTable.MessageRef + ".htm";
            var textMessageBodyRef = emailRowInMessageTable.MessageRef + ".txt";
            // If the email hasn't already been sent, send email and archive the table row.
            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);
                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;
                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }
            // Delete the queue message.
            sendEmailQueue.DeleteMessage(msg);
            Trace.TraceInformation("ProcessQueueMessage complete:  partitionKey {0} rowKey {1} Role Instance {2}.",
               partitionKey, rowKey, GetRoleInstance());
        }

Nicht verarbeitbare Nachrichten bewirken, dass die Anwendung eine Ausnahme auslöst, wenn sie verarbeitet werden. Wenn eine Nachricht mehr als fünf Mal aus der Warteschlange abgerufen wurde, wird angenommen, dass sie nicht verarbeitet werden kann, und die Nachricht wird aus der Warteschlange entfernt, damit nicht weiter versucht wird, sie zu verarbeiten. Bei Produktionsanwendungen sollte die nicht verarbeitbare Nachricht zur Analyse in eine Warteschlange für unzustellbare Nachrichten verschoben werden, anstatt die Nachricht zu löschen.

Der Code analysiert die Warteschlangennachricht in den Partitionsschlüssel und den Zeilenschlüssel, der zum Abrufen der SendEmail-Zeile benötigt wird, sowie in ein Neustartkennzeichen.

            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];

Wenn die Verarbeitung dieser Nachricht nach einem unerwarteten Herunterfahren neu gestartet wurde, prüft der Code die Tabelle `messagearchive`, um festzustellen, ob diese E-Mail bereits gesendet wurde. Wenn sie bereits gesendet wurde, löscht der Code die `SendEmail`-Zeile, falls sie existiert, und löscht die Warteschlangennachricht.

            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }

Anschließend wird die `SendEmail`-Zeile aus der Tabelle `message` abgerufen. Diese Zeile enthält alle Informationen, die zum Senden der E-Mail benötigt werden, bis auf die Blobs, die den HTML-Code und den unformatierten Text der E-Mail enthalten.

            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }

Anschließend sendet der Code die E-Mail und archiviert die `SendEmail`-Zeile.

            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);
                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;
                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

Das Verschieben der Zeile in die Tabelle messagearchive kann nicht in einer Transaktion ausgeführt werden, da diese mehrere Tabellen betrifft.

Wenn alle übrigen Schritte erfolgreich sind, wird die Warteschlangennachricht gelöscht.

            sendEmailQueue.DeleteMessage(msg);

### Die SendEmailToList-Methode

Das eigentliche Senden der E-Mail mithilfe von SendGrid geschieht mit der `SendEmailToList`-Methode. Wenn Sie einen anderen Dienst als SendGrid verwenden möchten, müssen Sie lediglich den Code in dieser Methode ändern.

**Hinweis:** Wenn die Projekteinstellungen ungültige Anmeldeinformationen enthalten, schlägt der Aufruf von SendGrid fehl, jedoch gibt es in der Anwendung keinen Hinweis auf diesen Fehler. Wenn Sie SendGrid in einer Produktionsanwendung verwenden, sollten Sie eventuell separate Anmeldeinformationen für die Web-API einrichten, damit Fehler nicht unbemerkt bleiben, wenn ein Administrator das Kennwort für sein SendGrid-Benutzerkonto ändert. Weitere Informationen finden Sie unter [SendGrid MultiAuth - Multiple Account Credentials][SendGrid MultiAuth - Multiple Account Credentials] (SendGrid MultiAuth - Mehrere Kontoanmeldeinformationen, in englischer Sprache). Anmeldeinformationen können Sie unter [][]<https://sendgrid.com/credentials></a> einrichten.

        private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
            string htmlMessageBodyRef, string textMessageBodyRef)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(fromEmailAddress);
            email.AddTo(emailAddress);
            email.Html = GetBlobText(htmlMessageBodyRef);
            email.Text = GetBlobText(textMessageBodyRef);
            email.Subject = subjectLine;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }
        private string GetBlobText(string blogRef)
        {
            var blob = blobContainer.GetBlockBlobReference(blogRef);
            blob.FetchAttributes();
            var blobSize = blob.Properties.Length;
            using (var memoryStream = new MemoryStream((int)blobSize))
            {
                blob.DownloadToStream(memoryStream);
                return System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }
        }

In der `GetBlobText`-Methode ruft der Code die Blob-Größe ab und verwendet diesen Wert aus Leistungsgründen anschließend, um das `MemoryStream`-Objekt zu initialisieren. Wenn Sie die Größe nicht angeben, weist `MemoryStream` 256 Byte zu. Wird dieser Wert beim Download überschritten, werden weitere 512 Byte zugewiesen usw., wobei der zugewiesene Betrag jedes Mal verdoppelt wird. Bei einem großen Blob wäre dieser Prozess ineffizient verglichen mit der Zuweisung des richtigen Betrags zu Beginn des Downloads.

### Die ProcessSubscribeQueueMessage-Methode

Mit der `Run`-Methode wird `ProcessSubscribeQueueMessage` aufgerufen, wenn in der Abonnement-Warteschlange eine Arbeitsaufgabe gefunden wird:

        private void ProcessSubscribeQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.  
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison subscribe message:    message {0}.",
                    msg.AsString, GetRoleInstance());
                subscribeQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue. Message consists of
            // subscriber GUID and list name.
            // Example:  57ab4c4b-d564-40e3-9a3f-81835b3e102e,contoso1
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var subscriberGUID = messageParts[0];
            var listName = messageParts[1];
            Trace.TraceInformation("ProcessSubscribeQueueMessage start:    subscriber GUID {0} listName {1} Role Instance {2}.",
                subscriberGUID, listName, GetRoleInstance());
            // Get subscriber info. 
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
            // Get mailing list info.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(subscriber.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            SendSubscribeEmail(subscriberGUID, subscriber, mailingList);
            subscribeQueue.DeleteMessage(msg);
            Trace.TraceInformation("ProcessSubscribeQueueMessage complete: subscriber GUID {0} Role Instance {1}.",
                subscriberGUID, GetRoleInstance());
        }

Diese Methode führt die folgenden Aufgaben aus:

-   Wenn die Nachricht nicht zu verarbeiten ist, wird sie protokolliert und gelöscht.
-   Die Abonnenten-GUID wird aus der Warteschlangennachricht abgerufen.
-   Anhand der GUID werden Abonnenten-Informationen aus der Tabelle MailingList abgerufen.
-   Eine Bestätigungs-E-Mail wird an den neuen Abonnenten gesendet.
-   Die Warteschlangennachricht wird gelöscht.

Wie bei E-Mails, die an Listen gesendet werden, erfolgt das eigentliche Senden der E-Mail in einer separaten Methode. So können Sie ohne weiteres zu einem anderen E-Mail-Dienst wechseln, falls Sie dies wünschen.

        private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(mailingList.FromEmailAddress);
            email.AddTo(subscriber.EmailAddress);
            string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
            email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
                "<a href=\"{1}\">Confirm Subscription</a>", mailingList.Description, subscribeURL);
            email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
                "{1}", mailingList.Description, subscribeURL);
            email.Subject = "Subscribe to " + mailingList.Description;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

## <a name="testing"></a>Testen von Workerrolle B

1.  Drücken Sie F5, um die Anwendung auszuführen.

2.  Gehen Sie zur Seite **Nachrichten**, um die zum Testen von Workerrolle A erstellte Nachricht anzuzeigen. Wenn Sie die Webseite nach etwa einer Minute aktualisieren, sehen Sie, dass die Zeile aus der Liste verschwunden ist, da sie archiviert wurde.

3.  Prüfen Sie den E-Mail-Posteingang, in dem Sie die E-Mail erwarten. Unter Umständen kommt es beim Senden der E-Mails per SendGrid oder bei Zustellung an Ihren E-Mail-Client zu Verzögerungen, sodass sie eventuell eine Weile warten müssen, bis Sie die E-Mail sehen. Möglicherweise müssen Sie auch Ihren Ordner für Junkmail prüfen.

## <a name="nextsteps"></a>Nächste Schritte

Sie haben jetzt die Azure-E-Mail-Dienstanwendung von Grund auf erstellt und das Ergebnis entspricht dem fertigen Projekt, das Sie heruntergeladen haben. Um die Anwendung in der Cloud bereitzustellen und zu testen und um sie zur Produktion heraufzustufen, können Sie die gleichen Prozeduren verwenden wie im [zweiten Lernprogramm][zweiten Lernprogramm].

Eine Beispielanwendung für die Verwendung von LINQ in Abfragen für den Azure Storage-Tabellendienst finden Sie unter [PhluffyFotos][PhluffyFotos].

Weitere Informationen zu Azure Storage finden Sie in der folgenden Ressource:

-   [Essential Knowledge for Windows Azure Storage][Essential Knowledge for Windows Azure Storage] (Grundlegende Kenntnisse zu Windows Azure Storage, in englischer Sprache; Blog von Bruno Terkaly)

Weitere Informationen zum Azure-Tabellendienst finden Sie in den folgenden Ressourcen:

-   [Essential Knowledge for Azure Table Storage][Essential Knowledge for Azure Table Storage] (Grundlegende Kenntnisse zu Azure Table Storage, in englischer Sprache; Blog von Bruno Terkaly)
-   [How to get the most out of Windows Azure Tables][How to get the most out of Windows Azure Tables] (Optimale Nutzung von Windows Azure-Tabellen, in englischer Sprache; Blog des Azure Storage-Teams)
-   [How to use Table Storage from .NET (Verwenden des Tabellenspeicherdiensts in .NET, in englischer Sprache)][How to use Table Storage from .NET (Verwenden des Tabellenspeicherdiensts in .NET, in englischer Sprache)]
-   [Windows Azure Storage Client Library 2.0 Tables Deep Dive][Windows Azure Storage Client Library 2.0 Tables Deep Dive] (Arbeiten mit Tabellen der Azure-Speicherclientbibliothek 2.0, in englischer Sprache; Blog des Azure Storage-Teams)
-   [Real World: Designing a Scalable Partitioning Strategy for Azure Table Storage (Design einer skalierbaren Partitionierungsstrategie für Azure-Tabellenspeicher, in englischer Sprache)][Real World: Designing a Scalable Partitioning Strategy for Azure Table Storage (Design einer skalierbaren Partitionierungsstrategie für Azure-Tabellenspeicher, in englischer Sprache)]

Weitere Informationen zum Azure-Warteschlangendienst und zu Azure Service Bus-Warteschlangen finden Sie in den folgenden Ressourcen:

-   [Queue-Centric Work Pattern (Building Real-World Cloud Apps with Windows Azure) [Warteschlangenorientiertes Arbeitsmuster (Erstellen von Cloud-Anwendungen mit Azure), in englischer Sprache]][]
-   [Windows Azure-Warteschlangen und Windows Azure Service Bus-Warteschlangen - Vergleich und Gegenüberstellung][Windows Azure-Warteschlangen und Windows Azure Service Bus-Warteschlangen - Vergleich und Gegenüberstellung]
-   [How to use the Queue Storage Service in .NET (Verwenden des Warteschlangenspeicherdiensts in .NET, in englischer Sprache)][How to use the Queue Storage Service in .NET (Verwenden des Warteschlangenspeicherdiensts in .NET, in englischer Sprache)]

Weitere Informationen zum Azure-Blob-Dienst finden Sie in den folgenden Ressourcen:

-   [Unstructured Blob Storage (Building Real-World Cloud Apps with Windows Azure) [Unstrukturierter Blob-Speicher (Erstellen von Cloud-Anwendungen mit Azure), in englischer Sprache]][]
-   [How to use Blob Storage from .NET (Verwenden des Azure Blob-Speicherdiensts in .NET, in englischer Sprache)][How to use Blob Storage from .NET (Verwenden des Azure Blob-Speicherdiensts in .NET, in englischer Sprache)]

Weitere Informationen zur automatischen Skalierung von Azure-Cloud-Dienst-Rollen finden Sie in den folgenden Ressourcen:

-   [Verwenden des Anwendungsblocks für die automatische Skalierung][Verwenden des Anwendungsblocks für die automatische Skalierung]
-   [Autoscaling and Windows Azure (Automatische Skalierung und Azure, in englischer Sprache)][Autoscaling and Windows Azure (Automatische Skalierung und Azure, in englischer Sprache)]
-   [Building Elastic, Autoscalable Solutions with Azure][Building Elastic, Autoscalable Solutions with Azure] (Erstellen elastischer, automatisch skalierbarer Projektmappen mit Azure, Video auf MSDN Channel 9, in englischer Sprache)

## <a name="Acknowledgments"></a><span class="short-header">Danksagungen</span>Danksagungen

Diese Lernprogramme und die Beispielanwendung wurden von [Rick Anderson][Rick Anderson] und Tom Dykstra geschrieben. Wir möchten den folgenden Personen für ihre Unterstützung danken:

-   Barry Dorrans (Twitter [@blowdart][@blowdart])
-   [Cory Fowler][Cory Fowler] (Twitter [@SyntaxC4][@SyntaxC4] )
-   [Joe Giardino][Joe Giardino]
-   Don Glover
-   Jai Haridas
-   [Scott Hunter][Scott Hunter] (Twitter: [@coolcsh][@coolcsh])
-   [Brian Swan][Brian Swan]
-   [Daniel Wang][Daniel Wang]
-   Mitglieder des Developer Advisory Council, die Feedback beigetragen haben:
-   Damir Arh
-   Jean-Luc Boucho
-   Carlos dos Santos
-   Mike Douglas
-   Robert Fite
-   Gianni Rosa Gallina
-   Fabien Lavocat
-   Karl Ots
-   Carlos-Alejandro Perez
-   Sunao Tomita
-   Perez Jones Tsisah
-   Michiel van Otegem

  [ersten Lernprogramm der Serie]: /de-de/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Hinzufügen des Projekts für Workerrolle B zur Projektmappe]: #addworkerrole
  [Hinzufügen eines Verweises auf das Webprojekt]: #addreference
  [Hinzufügen des SendGrid NuGet-Pakets zum Projekt]: #addsendgrid
  [Hinzufügen von Projekteinstellungen]: #addsettings
  [Hinzufügen von Code, der beim Start der Workerrolle ausgeführt wird]: #addcode
  [Testen von Workerrolle B]: #testing
  [Nächste Schritte]: #nextsteps
  [Menü für neues Workerrollenprojekt]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png
  [Dialogfeld für neues Rollenprojekt]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png
  [Verweis auf MvcWebRole hinzufügen]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png
  [NuGet-Pakete verwalten]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png
  [Das SendGrid-Paket installieren]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png
  [dritten Lernprogramm]: /de-de/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage
  [zweiten Lernprogramm]: /de-de/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Neue Einstellungen im Projekt WorkerRoleB]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png
  [vorherigen Lernprogramm]: /de-de/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [GetMessage]: http://msdn.microsoft.com/de-de/library/windowsazure/ee741827.aspx
  [Sichtbarkeits-Zeitlimit]: http://msdn.microsoft.com/de-de/library/windowsazure/ee758454.aspx
  [GetMessages]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx
  [SendGrid MultiAuth - Multiple Account Credentials]: http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials
  []: https://sendgrid.com/credentials
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Essential Knowledge for Windows Azure Storage]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx
  [Essential Knowledge for Azure Table Storage]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx
  [How to get the most out of Windows Azure Tables]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [Windows Azure Storage Client Library 2.0 Tables Deep Dive]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [Queue-Centric Work Pattern (Building Real-World Cloud Apps with Windows Azure) [Warteschlangenorientiertes Arbeitsmuster (Erstellen von Cloud-Anwendungen mit Azure), in englischer Sprache]]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern
  [Windows Azure-Warteschlangen und Windows Azure Service Bus-Warteschlangen - Vergleich und Gegenüberstellung]: http://msdn.microsoft.com/de-de/library/windowsazure/hh767287.aspx
  [Unstructured Blob Storage (Building Real-World Cloud Apps with Windows Azure) [Unstrukturierter Blob-Speicher (Erstellen von Cloud-Anwendungen mit Azure), in englischer Sprache]]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage
  [Verwenden des Anwendungsblocks für die automatische Skalierung]: /de-de/develop/net/how-to-guides/autoscaling/
  [Building Elastic, Autoscalable Solutions with Azure]: http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04
  [Rick Anderson]: http://blogs.msdn.com/b/rickandy/
  [@blowdart]: https://twitter.com/blowdart
  [Cory Fowler]: http://blog.syntaxc4.net/
  [@SyntaxC4]: https://twitter.com/SyntaxC4
  [Joe Giardino]: http://blogs.msdn.com/b/windowsazurestorage/
  [Scott Hunter]: http://blogs.msdn.com/b/scothu/
  [@coolcsh]: http://twitter.com/coolcsh
  [Brian Swan]: http://blogs.msdn.com/b/brian_swan/
  [Daniel Wang]: http://blogs.msdn.com/b/daniwang/
