<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" urlDisplayName="Step 5: Worker Role B" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 5: Worker role B" metaKeywords="Azure tutorial, adding working role cloud service, C# worker role" description="The fifth tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building worker role B (email sender) for the Azure Email Service application - 5 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Erstellen der Workerrolle B (E-Mail-Versand) für die Anwendung Azure-E-Mail-Dienst - 5 von 5
============================================================================================

Dies ist das fünfte von fünf Lernprogrammen, in denen die Erstellung und Bereitstellung der Azure-E-Mail-Beispielanwendung beschrieben wird. Weitere Informationen zur Anwendung und den Lernprogrammen finden Sie im [ersten Lernprogramm der Serie](/de-de/develop/net/tutorials/multi-tier-web-site/1-overview/).

In diesem Lernprogramm lernen Sie Folgendes:

-   Hinzufügen einer Workerrolle zu einem Cloud-Dienst-Projekt
-   Prüfen einer Warteschlange und Verarbeiten von Arbeitsaufgaben aus der Warteschlange
-   Senden von E-Mails mit SendGrid
-   Behandeln des geplanten Herunterfahrens durch Außerkraftsetzen der `OnStop`-Methode
-   Behandeln des ungeplanten Herunterfahrens und Sicherstellen, dass keine doppelten E-Mails gesendet werden

Hinzufügen von Workerrrolle BHinzufügen des Projekts für Workerolle B zur Projektmappe
--------------------------------------------------------------------------------------

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Cloud-Dienst-Projekt, und wählen Sie **Neues Workerrollenprojekt**.

    ![Menü für neues Workerrollenprojekt](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png)

2.  Wählen Sie im Dialogfeld **Neues Rollenprojekt hinzufügen** die Option **C\#**, wählen Sie **Workerrolle**, nennen Sie das Projekt WorkerRoleB, und klicken Sie auf **Hinzufügen**.

    ![Dialogfeld für neues Rollenprojekt](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png)

Hinzufügen eines VerweisesHinzufügen eines Verweises auf das Webprojekt
-----------------------------------------------------------------------

Sie benötigen einen Verweis auf das Webprojekt, da dort die Entitätsklassen definiert werden. Sie verwenden die Entitätsklassen in Workerrolle B, um Daten in den von der Anwendung verwendeten Azure-Tabellen zu lesen und zu schreiben.

1.  Klicken Sie mit der rechten Maustaste auf das Projekt WorkerRoleB, und wählen Sie **Verweis hinzufügen**.

    ![Verweis in Projekt WorkerRoleB hinzufügen](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-reference-menu.png)

2.  Fügen Sie im **Verweis-Manager** einen Verweis auf das Projekt MvcWebRole hinzu (oder auf das Projekt der Webanwendung, wenn Sie die Webbenutzeroberfläche in einer Azure-Website ausführen).

    ![Verweis auf MvcWebRole hinzufügen](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png)

Hinzufügen eines SCL 2.0-PaketsHinzufügen des NuGet-Pakets für die Speicherclientbibliothek 2.0 zu dem Projekt
--------------------------------------------------------------------------------------------------------------

> [WACOM.NOTE] Mit Visual Studio 2013 können Sie diesen Abschnitt überspringen, da das aktuelle Azure Storage-Paket im neuen Workerrollenprojekt installiert wird.

Als Sie das Projekt hinzugefügt haben, erhielt es nicht automatisch die aktualisierte Version des NuGet-Pakets für die Speicherclientbibliothek. Stattdessen erhielt es die alte Version 1.7 des Pakets, da diese in der Projektvorlage enthalten ist. Die Projektmappe verfügt jetzt über zwei Versionen des Azure Storage NuGet-Pakets: die Version 2.0 in den Projekten MvcWebRole und WorkerRoleA sowie die Version 1.7 im Projekt WorkerRoleB. Sie müssen die Version 1.7 deinstallieren und im Projekt WorkerRoleB die Version 2.0 installieren.

1.  Wählen Sie im Menü **Extras** den Eintrag **Library Package Manager** und danach **Manage NuGet Packages for Solution**.

2.  Wählen Sie im linken Bereich **Installierte Pakete** aus, und führen Sie einen Bildlauf nach unten aus, bis Sie zum Azure Storage-Paket gelangen.

    Das Paket wird zweimal aufgeführt, einmal für die Version 1.7 und einmal für die Version 2.0.

3.  Wählen Sie die Version 1.7 des Pakets aus, und klicken Sie auf **Verwalten**.

    Die Kontrollkästchen für MvcWebRole und WorkerRoleB sind deaktiviert, und das Kontrollkästchen für WorkerRoleB ist aktiviert.

4.  Deaktivieren Sie das Kontrollkästchen für WorkerRoleB, und klicken Sie auf **OK**.

5.  Wenn Sie gefragt werden, ob Sie abhängige Pakete deinstallieren möchten, klicken Sie auf **Nein**.

    Nach Abschluss der Deinstallation sehen Sie im NuGet-Dialogfeld nur die Version 2.0 des Pakets.

6.  Klicken Sie auf **Verwalten** für Version 2.0 des Pakets.

    Die Kontrollkästchen für MvcWebRole und WorkerRoleA sind aktiviert, und das Kontrollkästchen für WorkerRoleA ist deaktiviert.

7.  Aktivieren Sie das Kontrollkästchen für WorkerRoleA, und klicken Sie auf **OK**.

Hinzufügen eines SCL 1.7-VerweisesHinzufügen eines Verweises auf eine SCL 1.7-Assembly
--------------------------------------------------------------------------------------

> [WACOM.NOTE] Überspringen Sie diesen Abschnitt, wenn Sie das neueste SDK installiert haben und Visual Studio 2013 verwenden.

Da Version 2.0 der Speicherclientbibliothek (SCL) nicht über alle Funktionen verfügt, die für eine Diagnose benötigt werden, müssen Sie wie bereits zuvor bei den beiden anderen Projekten einen Verweis auf eine der 1.7-Assemblys hinzufügen.

1.  Klicken Sie mit der rechten Maustaste auf das Projekt WorkerRoleB, und wählen Sie **Verweis hinzufügen**.

2.  Klicken Sie unten im Dialogfeld auf **Durchsuchen...**.

3.  Navigieren Sie zum folgenden Ordner:

         C:\Programme\Microsoft SDKs\Windows Azure\.NET SDK012-10\ref

4.  Wählen Sie *Microsoft.WindowsAzure.StorageClient.dll* aus, und klicken Sie auf **Hinzufügen**.

5.  Klicken Sie im Dialogfeld **Verweis-Manager** auf **OK**.

Hinzufügen des SendGrid-PaketsHinzufügen des SendGrid NuGet-Pakets zum Projekt
------------------------------------------------------------------------------

Um E-Mails mit SendGrid zu senden, müssen Sie das SendGrid NuGet-Paket installieren.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt WorkerRoleB, und wählen Sie dann **Manage NuGet Packages**.

    ![NuGet-Pakete verwalten](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png)

2.  Wählen Sie im Dialogfeld **Manage NuGet Packages** die Registerkarte **Online** aus, geben Sie "sendgrid" in das Suchfeld ein, und drücken Sie die Eingabetaste.

3.  Klicken Sie im **SendGrid**-Paket auf **Installieren**.

    ![Das SendGrid-Paket installieren](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png)

4.  Schließen Sie das Dialogfeld.

Hinzufügen von ProjekteinstellungenHinzufügen von Projekteinstellungen
----------------------------------------------------------------------

Ebenso wie Workerrolle A benötigt Workerrolle B Anmeldeinformationen für ein Speicherkonto, um mit Tabellen, Warteschlangen und Blobs zu arbeiten. Zum Senden von E-Mails benötigt die Workerrolle darüber hinaus Anmeldeinformationen, die in Aufrufe des SendGrid-Diensts eingebettet werden. Und um einen Link zum Abbestellen zu konstruieren, der in von der Workerrolle gesendete E-Mails eingefügt wird, muss die Workerrolle die URL der Anwendung kennen. Diese Werte werden in Projekteinstellungen gespeichert.

Bei Speicherkonto-Anmeldeinformationen führen Sie das gleiche Verfahren wie im [dritten Lernprogramm](/de-de/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage) aus.

1.  Klicken Sie im **Projektmappen-Explorer** unter **Rollen** im Cloudprojekt mit der rechten Maustaste auf **WorkerRoleB**, und wählen Sie **Eigenschaften**.

2.  Wählen Sie die Registerkarte **Einstellungen** aus.

3.  Stellen Sie sicher, dass in der Dropdownliste **Dienstkonfiguration** der Eintrag **Alle Konfigurationen** ausgewählt ist.

4.  Wählen Sie die Registerkarte **Einstellungen**, und klicken Sie auf **Einstellung hinzufügen**.

5.  Geben Sie "StorageConnectionString" in der Spalte **Name** ein.

6.  Wählen Sie in der Dropdownliste **Typ** den Eintrag **Verbindungszeichenfolge** aus.

7.  Klicken Sie auf die Auslassungsschaltfläche (**...**) rechts in der Zeile, um das Dialogfeld **Verbindungszeichenfolge für das Speicherkonto** zu öffnen.

8.  Klicken Sie im Dialogfeld **Create Storage Connection String** auf das Optionsfeld **Your Subscription**.

9.  Wählen Sie das gleiche **Abonnement** und den gleichen **Kontonamen** wie für die Webrolle und die Workerrolle A.

10. Führen Sie die gleichen Schritte aus, um Einstellungen für die Verbindungszeichenfolge **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString** zu konfigurieren.

    Anschließend erstellen und konfigurieren Sie die drei neuen Einstellungen, die nur von Workerrolle B verwendet werden.

11. Klicken Sie auf der Registerkarte **Einstellungen** im Fenster **Eigenschaften** auf **Einstellung hinzufügen**, und fügen Sie anschließend drei neue Einstellungen vom Typ **Zeichenfolge** hinzu:

    -   **Name**: SendGridUserName, **Wert**: der SendGrid-Benutzername, den Sie im [zweiten Lernprogramm](/de-de/develop/net/tutorials/multi-tier-web-site/2-download-and-run/) festgelegt haben.

    -   **Name**: SendGridPassword, **Wert**: das SendGrid-Kennwort.

    -   **Name**: AzureMailServiceURL, **Wert**: die Basis-URL, die die Anwendung erhält, wenn Sie sie bereitstellen, z. B.: http://sampleurl.cloudapp.net.

    ![Neue Einstellungen im Projekt WorkerRoleB](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png)

### Hinzufügen von Code, der beim Start der Workerrolle ausgeführt wird

1.  Löschen Sie im Projekt WorkerRoleB die Datei WorkerRole.cs.

2.  Klicken Sie mit der rechten Maustaste auf das Projekt WorkerRoleB, und wählen Sie **Vorhandenes Element hinzufügen**.

    ![Vorhandenes Element zu Workerrolle B hinzufügen](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-existing.png)

3.  Navigieren Sie zu dem Ordner, in den Sie die Beispielanwendung heruntergeladen haben, wählen Sie die Datei WorkerRoleB.cs im Projekt WorkerRoleB aus, und klicken Sie auf **Hinzufügen**.

> [WACOM.NOTE] Öffnen Sie bei Visual Studio 2013 mit dem neuesten SDK und dem neuesten SendGrid NuGet-Paket die Datei *WorkerRoleB.cs*, und ändern Sie den Code folgendermaßen: (1) Löschen Sie die `using`-Anweisung für `SendGridMail.Transport`. (2) Ändern Sie beide Instanzen von `SendGrid.GenerateInstance` in `SendGrid.GetInstance`. (3) Ändern Sie beide Instanzen von `REST.GetInstance` in `Web.GetInstance`.

1.  Öffnen Sie WorkerRoleB.cs, und prüfen Sie den Code.

    Wie Sie bereits bei Workerrolle A gesehen haben, initialisiert die `OnStart`-Methode die Kontextklassen, die Sie benötigen, um mit Azure-Speicherentitäten zu arbeiten. Damit wird außerdem sichergestellt, dass alle Tabellen, Warteschlangen und Blob-Container, die Sie in der `Run`-Methode benötigen, vorhanden sind.

    Der Unterschied zu Workerrolle A besteht darin, dass der Blob-Container und die Abonnement-Warteschlange zu den zu erstellenden Ressourcen hinzugefügt werden, wenn sie noch nicht vorhanden sind. Mit dem Blob-Container rufen Sie die Dateien ab, die den HTML-Code und den unformatierten Text der E-Mail enthalten. Die Abonnement-Warteschlange dient zum Senden von Bestätigungs-E-Mails für Abonnements.

         public override bool OnStart()
         {
             ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount;

             // Speicherkonto-Konfigurationseinstellungen lesen
             ConfigureDiagnostics();
             Trace.TraceInformation("Speicherkonto in Workerrolle B wird initialisiert");
             var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

             // Warteschlangenspeicher initialisieren 
             Trace.TraceInformation("Warteschlangenclient wird erstellt.");
             CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
             this.sendEmailQueue = queueClient.GetQueueReference("azuremailqueue");
             this.subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");

             // Blob-Speicher initialisieren
             CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
             this.blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

             // Tabellenspeicher initialisieren
             var tableClient = storageAccount.CreateCloudTableClient();
             tableServiceContext = tableClient.GetDataServiceContext();

             Trace.TraceInformation("WorkerB: Blob-Container, Warteschlange, Tabellen erstellen, falls nicht vorhanden.");
             this.blobContainer.CreateIfNotExists();
             this.sendEmailQueue.CreateIfNotExists();
             this.subscribeQueue.CreateIfNotExists();
             var messageTable = tableClient.GetTableReference("Message");
             messageTable.CreateIfNotExists();
             var mailingListTable = tableClient.GetTableReference("MailingList");
             mailingListTable.CreateIfNotExists();

             return base.OnStart();
         }

    Mit der `Run`-Methode werden Arbeitsaufgaben aus zwei Warteschlangen verarbeitet: die Warteschlange, die für Nachrichten verwendet wird, die an E-Mail-Listen gesendet werden (von Workerrolle A erstellte Arbeitsaufgaben), und die Warteschlange, die für Bestätigungs-E-Mails für Abonnements verwendet wird (von der Abonnement-API-Methode im Projekt MvcWebRole erstellte Arbeitsaufgaben).

         public override void Run()
         {
             CloudQueueMessage msg = null;

             Trace.TraceInformation("WorkerRoleB: Start von Run()");
             while (true)
             {
                 try
                 {
                     bool messageFound = false;

                     // Wenn OnStop aufgerufen wurde, Rückkehr, um ein ordnungsgemäßes Herunterfahren auszuführen.
                     if (onStopCalled == true)
                     {
                         Trace.TraceInformation("onStopCalled WorkerRoleB");
                         returnedFromRunMethod = true;
                         return;
                     }
                     // Eine neue Nachricht aus der Warteschlange für an Listen zu sendende E-Mails abrufen und verarbeiten.
                     msg = sendEmailQueue.GetMessage();
                     if (msg != null)
                     {
                         ProcessQueueMessage(msg);
                         messageFound = true;
                     }

                     // Eine neue Nachricht aus der Abonnement-Warteschlange abrufen und verarbeiten.
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
                         err += " Letzte abgerufene Warteschlangennachricht: " + msg.AsString;
                     }
                     Trace.TraceError(err);
                     // Ablaufverfolgungsspeicher nicht auffüllen, wenn Prozessschleife einen Fehler enthält.
                     System.Threading.Thread.Sleep(1000 * 60);
                 }
             }
         }

    Dieser Code wird in einer Endlosschleife ausgeführt, bis die Workerrolle heruntergefahren wird. Wenn in der Hauptwarteschlange eine Arbeitsaufgabe gefunden wird, wird sie vom Code verarbeitet, und anschließend wird die Abonnement-Warteschlange geprüft.

                     // Eine neue Nachricht aus der Warteschlange für an Listen zu sendende E-Mails abrufen und verarbeiten.
                     msg = this.sendEmailQueue.GetMessage();
                     if (msg != null)
                     {
                         ProcessQueueMessage(msg);
                         messageFound = true;
                     }

                     // Eine neue Nachricht aus der Abonnement-Warteschlange abrufen und verarbeiten.
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

    Dieser Ruhezustand soll die Azure Storage-Transaktionskosten minimieren, wie im [vorherigen Lernprogramm](/de-de/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/) erläutert wurde.

    Wenn ein Warteschlangenelement mit der [GetMessage](http://msdn.microsoft.com/de-de/library/windowsazure/ee741827.aspx)-Methode aus der Warteschlange abgerufen wird, wird das betreffende Warteschlangenelement für alle anderen Worker- und Webrollen, die auf die Warteschlange zugreifen, für 30 Sekunden unsichtbar. Auf diese Weise wird sichergestellt, dass nur eine Workerrolleninstanz eine bestimmte Warteschlangennachricht zur Verarbeitung abrufen kann. Sie können diese *Exklusivitätszeit* (die Zeit, während der das Warteschlangenelement unsichtbar ist) explizit festlegen, indem Sie der `GetMessage`-Methode einen Parameter für das [Sichtbarkeits-Zeitlimit](http://msdn.microsoft.com/de-de/library/windowsazure/ee758454.aspx) übergeben. Wenn die Workerrolle zur Verarbeitung einer Warteschlangennachricht mehr als 30 Sekunden benötigt, müssen Sie die Exklusivitätszeit erhöhen, um andere Rolleninstanzen daran zu hindern, die gleiche Nachricht zu verarbeiten.

    Sie sollten die Exklusivitätszeit allerdings nicht auf einen zu großen Wert festlegen. Wenn die Exklusivitätszeit z. B. auf 48 Stunden festgelegt ist und die Workerrolle nach dem Entfernen einer Nachricht aus der Warteschlange unerwartet heruntergefahren wird, kann die Nachricht für 48 Stunden von keiner anderen Workerrolle verarbeitet werden. Die maximale Exklusivitätszeit beträgt sieben Tage.

    Mit der [GetMessages](http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx)-Methode (beachten Sie das "s" am Ende des Namens) können bis zu 32 Nachrichten in einem Aufruf aus der Warteschlange abgerufen werden. Bei jedem Warteschlangenzugriff fallen geringe Transaktionskosten an; die Transaktionskosten sind gleich, unabhängig davon, ob 32 oder null Nachrichten zurückgegeben werden. Mit dem folgenden Code werden bis zu 32 Nachrichten in einem Aufruf abgerufen und verarbeitet.

    	 foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))
         {
             ProcessQueueMessage(msg);
             messageFound = true;
         }

    Wenn Sie `GetMessages` verwenden, um mehrere Nachrichten zu entfernen, stellen Sie sicher, dass das Sichtbarkeits-Zeitlimit Ihrer Anwendung genügend Zeit zur Verarbeitung aller Nachrichten gibt. Nach dem Ablauf des Sichtbarkeits-Zeitlimits können andere Rolleninstanzen auf die Nachricht zugreifen, und sobald dies geschieht, kann die erste Instanz die Nachricht nicht löschen, wenn sie die Verarbeitung der Arbeitsaufgabe beendet hat.

    Mit der `Run`-Methode wird `ProcessQueueMessage` aufgerufen, wenn in der Hauptwarteschlange eine Arbeitsaufgabe gefunden wird:

         private void ProcessQueueMessage(CloudQueueMessage msg)
         {
             // Protokollieren und löschen, wenn dies eine nicht verarbeitbare Warteschlangennachricht ist (wiederholt verarbeitet
             // und stets einen Fehler verursachend, der den Abschluss der Verarbeitung verhindert).
             // Produktionsanwendungen müssen die nicht verarbeitbare Nachricht zur Analyse in eine Warteschlange
             // für unzustellbare Nachrichten verschieben, anstatt die Nachricht zu löschen.           
             if (msg.DequeueCount > 5)
             {
                 Trace.TraceError("Nicht verarbeitbare Nachricht wird gelöscht:    Nachricht {0} Rolleninstanz {1}.",
                     msg.ToString(), GetRoleInstance());
                 sendEmailQueue.DeleteMessage(msg);
                 return;
             }
             // Aus der Warteschlange abgerufene Nachricht analysieren.
             // Beispiel:  2012-01-01,0123456789email@domain.com,0
             var messageParts = msg.AsString.Split(new char[] { ',' });
             var partitionKey = messageParts[0];
             var rowKey = messageParts[1];
             var restartFlag = messageParts[2];
             Trace.TraceInformation("ProcessQueueMessage starten:  partitionKey {0} rowKey {1} Rolleninstanz {2}.",
                 partitionKey, rowKey, GetRoleInstance());
             // Ist dies ein Neustart, überprüfen, ob die E-Mail bereits gesendet wurde.
             if (restartFlag == "1")
             {
                 var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                 var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                 var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                 if (messagearchiveRow != null)
                 {
                     // SendEmail-Zeile befindet sich in Archiv, also wurde E-Mail bereits gesendet. 
                     // Falls eine SendEmail-Zeile in Nachrichtentabelle vorhanden ist, Zeile löschen
                     // und die Warteschlangennachricht löschen.
                     Trace.TraceInformation("E-Mail bereits gesendet: partitionKey=" + partitionKey + " rowKey= " + rowKey);
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
                         // Die Zeile in der Tabelle message abrufen, die die zum Senden der E-Mail benötigten Daten enthält.
             var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
             var retrievedResult = messageTable.Execute(retrieveOperation);
             var emailRowInMessageTable = retrievedResult.Result as SendEmail;
             if (emailRowInMessageTable == null)
             {
                 Trace.TraceError("SendEmail-Zeile nicht gefunden:  partitionKey {0} rowKey {1} Rolleninstanz {2}.",
                     partitionKey, rowKey, GetRoleInstance());
                 return;
             }
             // Blob-Namen aus dem MessageRef ableiten.
             var htmlMessageBodyRef = emailRowInMessageTable.MessageRef + ".htm";
             var textMessageBodyRef = emailRowInMessageTable.MessageRef + ".txt";
             // Wenn die E-Mail noch nicht gesendet wurde, E-Mail senden und Tabellenzeile archivieren.
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

             // Warteschlangennachricht löschen.
             sendEmailQueue.DeleteMessage(msg);

             Trace.TraceInformation("ProcessQueueMessage abgeschlossen:  partitionKey {0} rowKey {1} Rolleninstanz {2}.",
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
                     Trace.TraceInformation("E-Mail bereits gesendet: partitionKey=" + partitionKey + " rowKey= " + rowKey);
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
                 Trace.TraceError("SendEmail-Zeile nicht gefunden:  partitionKey {0} rowKey {1} Rolleninstanz {2}.",
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

    Das eigentliche Senden der E-Mail mithilfe von SendGrid geschieht mit der `SendEmailToList`-Methode. Wenn Sie einen anderen Dienst als SendGrid verwenden möchten, müssen Sie lediglich den Code in dieser Methode ändern.

    **Hinweis:** Wenn die Projekteinstellungen ungültige Anmeldeinformationen enthalten, schlägt der Aufruf von SendGrid fehl, jedoch gibt es in der Anwendung keinen Hinweis auf diesen Fehler. Wenn Sie SendGrid in einer Produktionsanwendung verwenden, sollten Sie eventuell separate Anmeldeinformationen für die Web-API einrichten, damit Fehler nicht unbemerkt bleiben, wenn ein Administrator das Kennwort für sein SendGrid-Benutzerkonto ändert. Weitere Informationen finden Sie unter [SendGrid MultiAuth - Multiple Account Credentials](http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials) (SendGrid MultiAuth - Mehrere Kontoanmeldeinformationen, in englischer Sprache). Anmeldeinformationen können Sie unter <https://sendgrid.com/credentials> einrichten.

         private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
             string htmlMessageBodyRef, string textMessageBodyRef)
         {
             var email = SendGrid.GenerateInstance();
             email.From = new MailAddress(fromEmailAddress);
             email.AddTo(emailAddress);
             email.Html = GetBlobText(htmlMessageBodyRef);
             email.Text = GetBlobText(textMessageBodyRef);
             email.Subject = subjectLine;
             var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                 RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
             var transportREST = REST.GetInstance(credentials);
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

    Mit der `Run`-Methode wird `ProcessSubscribeQueueMessage` aufgerufen, wenn in der Abonnement-Warteschlange eine Arbeitsaufgabe gefunden wird:

         private void ProcessSubscribeQueueMessage(CloudQueueMessage msg)
         {
             // Protokollieren und löschen, wenn dies eine nicht verarbeitbare Warteschlangennachricht ist (wiederholt verarbeitet
             // und stets einen Fehler verursachend, der den Abschluss der Verarbeitung verhindert).
             // Produktionsanwendungen müssen die nicht verarbeitbare Nachricht zur Analyse in eine Warteschlange
             // für unzustellbare Nachrichten verschieben, anstatt die Nachricht zu löschen.  
             if (msg.DequeueCount > 5)
             {
                 Trace.TraceError("Nicht verarbeitbare Abonnement-Nachricht wird gelöscht:    Nachricht {0}.",
                     msg.AsString, GetRoleInstance());
                 subscribeQueue.DeleteMessage(msg);
                 return;
             }
             // Aus der Warteschlange abgerufene Nachricht analysieren. Nachricht besteht aus
             // Abonnenten-GUID und Listenname.
             // Beispiel:  57ab4c4b-d564-40e3-9a3f-81835b3e102e,contoso1
             var messageParts = msg.AsString.Split(new char[] { ',' });
             var subscriberGUID = messageParts[0];
             var listName = messageParts[1];
             Trace.TraceInformation("ProcessSubscribeQueueMessage starten:    Abonnenten-GUID {0} Listenname {1} Rolleninstanz {2}.",
                 subscriberGUID, listName, GetRoleInstance());
             // Abonnenten-Info abrufen. 
             string filter = TableQuery.CombineFilters(
                 TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                 TableOperators.And,
                 TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
             var query = new TableQuery<Subscriber>().Where(filter);
             var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
             // Mailinglist-Info abrufen.
             var retrieveOperation = TableOperation.Retrieve<MailingList>(subscriber.ListName, "mailinglist");
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var mailingList = retrievedResult.Result as MailingList;

             SendSubscribeEmail(subscriberGUID, subscriber, mailingList);

             subscribeQueue.DeleteMessage(msg);

             Trace.TraceInformation("ProcessSubscribeQueueMessage abgeschlossen: Abonnenten-GUID {0} Rolleninstanz {1}.",
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
             var email = SendGrid.GenerateInstance();
             email.From = new MailAddress(mailingList.FromEmailAddress);
             email.AddTo(subscriber.EmailAddress);
             string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                 "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
             email.Html = String.Format("<p>Klicken Sie auf den Link unten, um {0} zu abonnieren. " +
                 "Wenn Sie Ihr Abonnement nicht bestätigen, wird die Liste von Ihnen nicht abonniert.</p>" +
                 "<a href=\"{1}\">Abonnement bestätigen</a>", mailingList.Description, subscribeURL);
             email.Text = String.Format("Kopieren Sie die folgende URL, und fügen Sie sie in den Browser ein, um {0} zu abonnieren. " +
                 "Wenn Sie Ihr Abonnement nicht bestätigen, wird die Liste von Ihnen nicht abonniert.\n" +
                 "{1}", mailingList.Description, subscribeURL);
             email.Subject = mailingList.Description + " abonnieren";
             var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
             var transportREST = REST.GetInstance(credentials);
             transportREST.Deliver(email);
         }
        

TestenTesten der Workerrolle B
------------------------------

1.  Drücken Sie F5, um die Anwendung auszuführen.

2.  Gehen Sie zur Seite **Nachrichten**, um die zum Testen von Workerrolle A erstellte Nachricht anzuzeigen. Wenn Sie die Webseite nach etwa einer Minute aktualisieren, sehen Sie, dass die Zeile aus der Liste verschwunden ist, da sie archiviert wurde.

3.  Prüfen Sie den E-Mail-Posteingang, in dem Sie die E-Mail erwarten. Unter Umständen kommt es beim Senden der E-Mails per SendGrid oder bei Zustellung an Ihren E-Mail-Client zu Verzögerungen, sodass sie eventuell eine Weile warten müssen, bis Sie die E-Mail sehen. Möglicherweise müssen Sie auch Ihren Ordner für Junkmail prüfen.

Nächste SchritteNächste Schritte
--------------------------------

Sie haben jetzt die Azure-E-Mail-Dienstanwendung von Grund auf erstellt und das Ergebnis entspricht dem fertigen Projekt, das Sie heruntergeladen haben. Um die Anwendung in der Cloud bereitzustellen und zu testen und um sie zur Produktion heraufzustufen, können Sie die gleichen Prozeduren verwenden wie im [zweiten Lernprogramm](/de-de/develop/net/tutorials/multi-tier-web-site/2-download-and-run/). Wenn Sie die alternative Architektur erstellen möchten, finden Sie im [Lernprogramm Erste Schritte mit Azure Web Sites](/de-de/develop/net/tutorials/get-started) Informationen über das Bereitstellen des MVC-Projekts auf einer Azure-Website.

Weitere Informationen zu Azure Storage finden Sie in der folgenden Ressource:

-   [Essential Knowledge for Windows Azure Storage](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx) (Grundlegende Kenntnisse zu Windows Azure Storage, in englischer Sprache; Blog von Bruno Terkaly)

Weitere Informationen zum Azure-Tabellendienst finden Sie in den folgenden Ressourcen:

-   [Essential Knowledge for Azure Table Storage](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx) (Grundlegende Kenntnisse zu Azure Table Storage, in englischer Sprache; Blog von Bruno Terkaly)
-   [How to get the most out of Windows Azure Tables](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx) (Optimale Nutzung von Windows Azure-Tabellen, in englischer Sprache; Blog des Azure Storage-Teams)
-   [How to use Table Storage from .NET (Verwenden des Tabellenspeicherdiensts in .NET, in englischer Sprache)](http://www.windowsazure.com/de-de/develop/net/how-to-guides/table-services/)
-   [Windows Azure Storage Client Library 2.0 Tables Deep Dive](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx) (Arbeiten mit Tabellen der Azure-Speicherclientbibliothek 2.0, in englischer Sprache; Blog des Azure Storage-Teams)
-   [Real World: Designing a Scalable Partitioning Strategy for Azure Table Storage (Design einer skalierbaren Partitionierungsstrategie für Azure-Tabellenspeicher, in englischer Sprache)](http://msdn.microsoft.com/de-de/library/windowsazure/hh508997.aspx)

Weitere Informationen zum Azure-Warteschlangendienst und zu Azure Service Bus-Warteschlangen finden Sie in den folgenden Ressourcen:

-   [Queue-Centric Work Pattern (Building Real-World Cloud Apps with Windows Azure) [Warteschlangenorientiertes Arbeitsmuster (Erstellen von Cloud-Anwendungen mit Azure), in englischer Sprache]](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern)
-   [Windows Azure-Warteschlangen und Windows Azure Service Bus-Warteschlangen - Vergleich und Gegenüberstellung](http://msdn.microsoft.com/de-de/library/windowsazure/hh767287.aspx)
-   [How to use the Queue Storage Service in .NET (Verwenden des Warteschlangenspeicherdiensts in .NET, in englischer Sprache)](/de-de/develop/net/how-to-guides/queue-service/)

Weitere Informationen zum Azure-Blob-Dienst finden Sie in den folgenden Ressourcen:

-   [Unstructured Blob Storage (Building Real-World Cloud Apps with Windows Azure) [Unstrukturierter Blob-Speicher (Erstellen von Cloud-Anwendungen mit Azure), in englischer Sprache]](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)
-   [How to use Blob Storage from .NET (Verwenden des Azure Blob-Speicherdiensts in .NET, in englischer Sprache)](/de-de/develop/net/how-to-guides/blob-storage/)

Weitere Informationen zur automatischen Skalierung von Azure-Cloud-Dienst-Rollen finden Sie in den folgenden Ressourcen:

-   [Verwenden des Anwendungsblocks für die automatische Skalierung](/de-de/develop/net/how-to-guides/autoscaling/)
-   [Autoscaling and Windows Azure (Automatische Skalierung und Azure, in englischer Sprache)](http://msdn.microsoft.com/de-de/library/hh680945(v=PandP.50).aspx)
-   [Building Elastic, Autoscalable Solutions with Azure](http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04) (Erstellen elastischer, automatisch skalierbarer Projektmappen mit Azure, Video auf MSDN Channel 9, in englischer Sprache)

DanksagungenDanksagungen
------------------------

Diese Lernprogramme und die Beispielanwendung wurden von [Rick Anderson](http://blogs.msdn.com/b/rickandy/) und Tom Dykstra geschrieben. Wir möchten den folgenden Personen für ihre Unterstützung danken:

-   Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart))
-   [Cory Fowler](http://blog.syntaxc4.net/) (Twitter [@SyntaxC4](https://twitter.com/SyntaxC4) )
-   [Joe Giardino](http://blogs.msdn.com/b/windowsazurestorage/)
-   Don Glover
-   Jai Haridas
-   [Scott Hunter](http://blogs.msdn.com/b/scothu/) (Twitter: [@coolcsh](http://twitter.com/coolcsh))
-   [Brian Swan](http://blogs.msdn.com/b/brian_swan/)
-   [Daniel Wang](http://blogs.msdn.com/b/daniwang/)
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



[firsttutorial]: /de-de/develop/net/tutorials/multi-tier-web-site/1-overview/
[tut2]: /de-de/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
[tut3configstorage]: /de-de/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage
[tut4]: /de-de/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
[queuehowto]: /de-de/develop/net/how-to-guides/queue-service/

[blobhowto]: /de-de/develop/net/how-to-guides/blob-storage/
[GetMessage]: http://msdn.microsoft.com/de-de/library/windowsazure/ee741827.aspx
[getstartedtutorial]: /de-de/develop/net/tutorials/get-started
[sbqueuecomparison]: http://msdn.microsoft.com/de-de/library/windowsazure/hh767287.aspx
[autoscalingappblock]: /de-de/develop/net/how-to-guides/autoscaling/


[mtas-new-worker-role-project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png
[mtas-add-new-role-project-dialog]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png
[mtas-worker-b-add-existing]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-existing.png
[mtas-worker-b-add-reference-menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-reference-menu.png
[mtas-worker-b-reference-manager]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png
[mtas-worker-b-manage-nuget]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png
[mtas-worker-b-install-sendgrid]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png


[mtas-worker-b-settings]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png
[autoscaling-and-windows-azure]: http://msdn.microsoft.com/de-de/library/hh680945(v=PandP.50).aspx
