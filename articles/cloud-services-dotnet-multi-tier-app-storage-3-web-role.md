<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" pageTitle="Azure Cloud Service Tutorial: ASP.NET Web Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, ASP.NET MVC tutorial, Azure web role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Erstellen der Webrolle für die Azure-E-Mail-Dienstanwendung - 3 von 5.

Dies ist das dritte von fünf Lernprogrammen, in denen die Erstellung und Bereitstellung der Azure-E-Mail-Dienstanwendung beschrieben wird. Weitere Informationen zur Anwendung und den Lernprogrammen finden Sie im [ersten Lernprogramm der Serie][ersten Lernprogramm der Serie].

In diesem Lernprogramm lernen Sie Folgendes:

-   Erstellen einer Lösung bestehend aus einem Cloud-Dienst-Projekt mit einer Webrolle und einer Workerrolle.
-   Arbeiten mit Azure-Tabellen, Blobs und Warteschlangen in MVC 5-Controllern und Ansichten.
-   Umgang mit Parallelitätskonflikten bei der Arbeit mit Azure-Tabellen.

## Abschnitte dieses Lernprogramms

-   [Erstellen der Visual Studio-Lösung][Erstellen der Visual Studio-Lösung]
-   [Aktualisieren des NuGet-Pakets für die Speicherclientbibliothek][Aktualisieren des NuGet-Pakets für die Speicherclientbibliothek]
-   [Konfigurieren des Projekts zur Verwendung des Speicheremulators][Konfigurieren des Projekts zur Verwendung des Speicheremulators]
-   [Konfigurieren der Ablaufverfolgung und Behandlung von Neustarts][Konfigurieren der Ablaufverfolgung und Behandlung von Neustarts]
-   [Hinzufügen von Code zum Erstellen von Tabellen, Warteschlange und Blob-Container zur Methode Application\_Start][Hinzufügen von Code zum Erstellen von Tabellen, Warteschlange und Blob-Container zur Methode Application\_Start]
-   [Erstellen und Testen der Mailingliste][Erstellen und Testen der Mailingliste]
-   [Erstellen und Testen der Abonnenten-Controller und -Ansichten][Erstellen und Testen der Abonnenten-Controller und -Ansichten]
-   [Erstellen und Testen der Nachrichten-Controller und -Ansichten][Erstellen und Testen der Nachrichten-Controller und -Ansichten]
-   [Erstellen und Testen der Abbestellen-Controller und -Ansichten][Erstellen und Testen der Abbestellen-Controller und -Ansichten]
-   [Nächste Schritte][Nächste Schritte]

## <a name="cloudproject"></a>Erstellen der Visual Studio-Lösung

Zunächst erstellen Sie eine Visual Studio-Lösung mit einem Projekt für das Web-Front-End und einem Projekt für eine der Back-End-Azure-Workerrollen. Die zweite Workerrolle wird später hinzugefügt.

### Erstellen eines Cloud-Dienst-Projekts mit einer Webrolle und einer Workerrolle

1.  Starten Sie Visual Studio.

2.  Klicken Sie im Menü **Datei** auf **Neues Projekt**.

    ![Menü Neues Projekt][Menü Neues Projekt]

3.  Erweitern Sie den Knoten **C\#**, wählen Sie **Cloud** unter **Installierte Vorlagen** aus und klicken Sie auf **Azure-Cloud-Dienst**.

4.  Benennen Sie die Anwendung **AzureEmailService** und klicken Sie auf **OK**.

    ![Dialogfeld "Neues Projekt"][Dialogfeld "Neues Projekt"]

5.  Wählen Sie im Dialogfeld **Neuer Azure-Cloud-Dienst** die Option **ASP.NET-Webrolle** aus und klicken Sie auf den Pfeil nach rechts.

    ![Dialogfeld Neues Azure-Cloud-Projekt][Dialogfeld Neues Azure-Cloud-Projekt]

6.  Zeigen Sie in der rechten Spalte mit der Maus auf **WebRole1** und klicken Sie auf das Stiftsymbol, um den Namen der Webrolle zu ändern.

7.  Geben Sie MvcWebRole als neuen Namen ein und drücken Sie die Eingabetaste.

    ![Dialogfeld Neues Azure-Cloud-Projekt - Umbenennen der Webrolle][Dialogfeld Neues Azure-Cloud-Projekt - Umbenennen der Webrolle]

8.  Wiederholen Sie die Prozedur, um eine **Workerrolle** mit dem Namen WorkerRoleA hinzuzufügen und klicken Sie auf **OK**.

    ![Dialogfeld Neues Azure-Cloud-Projekt - Hinzufügen einer Workerrolle][Dialogfeld Neues Azure-Cloud-Projekt - Hinzufügen einer Workerrolle]

9.  Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Vorlage **MVC** aus, markieren Sie das Kontrollkästchen **Web API** und klicken Sie auf **Authentifizierung ändern**.

    ![Dialogfeld "Neues Projekt"][1]

10. Klicken Sie im Dialogfeld **Authentifizierung ändern** auf **Keine Authentifizierung** und dann auf **OK**.

    ![Keine Authentifizierung][Keine Authentifizierung]

11. Klicken Sie im Dialogfeld **Neues ASP.Net-Projekt** auf **OK**.

### Erstellen von Seitenkopf, Menü und Fußzeile

In diesem Abschnitt werden Sie den Seitenkopf, die Fußzeile und die Menüelemente aktualisieren, die auf allen Seiten der Administrator-Web-GUI angezeigt werden. Die Anwendung enthält drei Sätze von Administrator-Webseiten: eine für Mailinglisten, eine für Abonnenten von Mailinglisten und eine für Nachrichten.

1.  Falls Sie die [komplette Lösung][komplette Lösung] noch nicht heruntergeladen haben, tun Sie dies jetzt, bevor Sie mit dem nächsten Schritt fortfahren.

    Im Rest des Lernprogramms können Sie die Dateien aus dem heruntergeladenen Projekt in das neue Projekt kopieren, anstatt Codeausschnitte zu kopieren und einzufügen. Dieses Lernprogramm beschreibt die wichtigsten Teile des Codes, den Sie kopieren.

    Um eine Datei aus dem heruntergeladenen Projekt hinzuzufügen, klicken Sie mit der rechten Maustaste auf das Projekt, zu dem Sie die Datei hinzufügen möchten und wählen Sie **Hinzufügen -Vorhandenes Element** im Kontextmenü aus. Navigieren Sie anschließend zu dem Ordner, in den Sie das fertige Projekt heruntergeladen haben, wählen Sie die gewünschten Dateien aus und klicken Sie auf **Hinzufügen**. Falls ein Dialogfeld **Zieldatei existiert bereits** angezeigt wird, klicken Sie auf **Ja**.

2.  Fügen Sie im Projekt MvcWebRole die Datei *Views\\Shared\_Layout.cshtml* aus dem heruntergeladenen Projekt hinzu (klicken Sie mit der rechten Maustaste auf den Ordner *Shared* unter *Views*, um die Datei hinzuzufügen).

    Daraufhin werden Kopf- und Fußzeile sowie Menüeinträge für die Seiten Mailingliste, Nachricht und Abonnent hinzugefügt:

        <ul class="nav navbar-nav">
            <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
            <li>@Html.ActionLink("Messages", "Index", "Message")</li>
            <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
        </ul>

### Lokales Ausführen der Anwendung

1.  Drücken Sie STRG+F5, um die Anwendung auszuführen.

    Falls Sie normalerweise Webprojekte ohne Azure-Clouddienste erstellen, werden Sie feststellen, dass der Aufruf der Startseite in Ihrem Browser etwas länger dauert als gewöhnlich.

    ![Startseite][Startseite]

    Dies liegt daran, dass Visual Studio den Azure-Serveremulator und den Azure-Speicheremulator startet. In der Windows-Systemleiste wird das Serveremulatorsymbol angezeigt:

    ![Serveremulator in der Systemleiste][Serveremulator in der Systemleiste]

2.  Schließen Sie den Browser.

## <a name="updatescl"></a>Aktualisieren des NuGet-Pakets für die Speicherclientbibliothek

Das API-Framework für die Arbeit mit Azure-Speichertabellen, Warteschlangen und Blobs ist die sogenannte Speicherclientbibliothek (Storage Client Library SCL). Diese API ist in einem NuGet-Paket in der Cloud-Dienst-Projektvorlage enthalten. Änderungen an der SCL erfolgen jedoch oft nach der Erstellung der Projektvorlagen, daher sollten Sie immer prüfen, ob ein Update für Ihr SCL-NuGet-Paket verfügbar ist.

1.  Zeigen Sie im Menü **Tools** in Visual Studio mit der Maus auf **Library Package Manager** und klicken Sie auf **Manage NuGet Packages for Solution**.

    ![Manage NuGet Packages for Solution im Menü][Manage NuGet Packages for Solution im Menü]

2.  Wählen Sie im linken Bereich des Dialogfelds **Manage NuGet Packages** die Option **Updates** aus, blättern Sie nach unten bis zum Paket **Azure Storage** und klicken Sie auf **Update**.

    ![Azure Storage-Paket im Dialogfeld Manage NuGet Packages][Azure Storage-Paket im Dialogfeld Manage NuGet Packages]

3.  Stellen Sie sicher, dass im Dialogfeld **Select Projects** beide Projekte ausgewählt sind und klicken Sie auf **OK**.

    ![Auswählen beider Projekte im Dialogfeld Select Projects][Auswählen beider Projekte im Dialogfeld Select Projects]

4.  Stimmen Sie den Lizenzbedingungen zu, um die Installation des Pakets abzuschließen, und schließen Sie anschließend das Dialogfeld **Manage NuGet Packages**.

## <a name="configurestorage"></a>Konfigurieren des Projekts zur Verwendung des Speicheremulators

Der Code für Web- und Workerrolle, den Sie später hinzufügen werden, verwendet eine Verbindungszeichenfolge mit dem Namen StorageConnectionString, um sich mit dem Azure-Speicher zu verbinden. In diesem Abschnitt fügen Sie die Einstellung zu den Rolleneigenschaften hinzu und konfigurieren die Einstellung für den Speicheremulator. Im zweiten Lernprogramm in dieser Reihe erfahren Sie, wie Sie die Verbindungszeichenfolge für ein Azure-Speicherkonto konfigurieren können.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **MvcWebRole** unter **Roles** im **AzureEmailService**-Cloudprojekt und wählen Sie **Eigenschaften** aus.

    ![Webrollen-Eigenschaften][Webrollen-Eigenschaften]

2.  Stellen Sie sicher, dass in der Dropdownliste **Dienstkonfiguration** der Eintrag **Alle Konfigurationen** ausgewählt ist.

3.  Wählen Sie die Registerkarte **Einstellungen**, und klicken Sie auf **Einstellung hinzufügen**.

4.  Geben Sie "StorageConnectionString" in der Spalte **Name** ein.

5.  Wählen Sie in der Dropdownliste **Typ** den Eintrag **Verbindungszeichenfolge** aus.

6.  Klicken Sie auf die Auslassungsschaltfläche (**...**) rechts in der Zeile, um das Dialogfeld **Verbindungszeichenfolge für das Speicherkonto** zu öffnen.

    ![Rechtsklick Eigenschaften][Rechtsklick Eigenschaften]

7.  Klicken Sie im Dialogfeld **Verbindungszeichenfolge für Speicherkonto erstellen** auf das Optionsfeld **Azure-Speicheremulator** und klicken Sie dann auf **OK**.

    Die `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`-Verbindungszeichenfolge zeigt standardmäßig auf den Speicheremulator und muss daher nicht geändert werden.

8.  Wiederholen Sie die Prozedur, die Sie für MvcWebRole verwendet haben, um die Verbindungszeichenfolge für die WorkerRoleA-Rolle zu setzen.

Als Sie eine neue Einstellung über die Schaltfläche **Einstellungen hinzufügen** hinzugefügt haben, wurde die neue Einstellung zum XML in der Datei *ServiceDefinition.csdf* und in den beiden *.cscfg*-Konfigurationsdateien hinzugefügt. Das folgende XML wird von Visual Studio zur Datei *ServiceDefinition.csdf* hinzugefügt.

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

Das folgende XML wird zu den beiden *.cscfg*-Dateien hinzugefügt.

       <Setting name="StorageConnectionString"
       value="UseDevelopmentStorage=true" />

Sie können Einstellungen manuell zur Datei *ServiceDefinition.csdf* und den beiden *.cscfg*-Konfigurationsdateien hinzufügen. Allerdings bietet der Eigenschaften-Editor die folgenden Vorteile für Verbindungszeichenfolgen:

-   Sie müssen die neue Einstellung nur an einem Ort anlegen, und das korrekte XML wird zu allen drei Dateien hinzugefügt.
-   Das korrekte XML wird für alle drei Einstellungsdateien generiert. Die Datei *ServiceDefinition.csdf* definiert Einstellungen, die in beiden *.cscfg*-Konfigurationsdateien vorhanden sein müssen. Falls die Datei *ServiceDefinition.csdf* und die beiden *.cscfg*-Konfigurationsdateien inkonsistent sind, kann es passieren, dass Sie die folgende Fehlermeldung in Visual Studio erhalten: *The current service model is out of sync. Make sure both the service configuration and definition files are valid.*

    ![Fehler: ungültige Dienstkonfiguration und Definitionsdateien][Fehler: ungültige Dienstkonfiguration und Definitionsdateien]

Wenn dieser Fehler angezeigt wird, können Sie den Eigenschaften-Editor erst nach Behebung der Inkonsistenz durch manuelles Editieren der Dateien wieder verwenden.

## <a name="tracing"></a>Konfigurieren der Ablaufverfolgung und Behandlung von Neustarts

1.  Fügen Sie im Projekt MvcWebRole die Datei *WebRole.cs* aus dem heruntergeladenen Projekt hinzu.

Dabei wird eine Methode zum Konfigurieren der Protokollierung hinzugefügt und aus der `OnStart`-Methode heraus aufgerufen, wenn die Webrolle gestartet wird. Der Code in der neuen `ConfigureDiagnostics`-Methode wird im [zweiten Lernprogramm][zweiten Lernprogramm] erläutert.

Außerdem wird Code hinzugefügt, der ausgeführt wird, wenn die Webrolle darüber benachrichtigt wird, dass sie gleich heruntergefahren wird. Azure-Cloud-Dienstanwendungen werden ca. zwei mal pro Monat für Betriebssystemupdates neu gestartet. (Weitere Informationen zu OS-Updates finden Sie unter [Role Instance Restarts Due to OS Upgrades][Role Instance Restarts Due to OS Upgrades] (Neustarts von Rolleninstanzen für OS-Upgrades, in englischer Sprache).) Vor dem Herunterfahren einer Webanwendung wird ein `OnStop`-Ereignis ausgelöst. Die von Visual Studio erstellte Webrollen-Codevorlage überschreibt die `OnStop`-Methode nicht. Daher hat die Anwendung nur wenige Sekunden, um die Verarbeitung von HTTP-Anforderungen abzuschließen, bevor sie heruntergefahren wird. Sie können die `OnStop`-Methode überschreiben, um sicherzustellen, dass die Anwendung beim Herunterfahren ordnungsgemäß beendet wird.

Die neu hinzugefügte Datei enthält die folgende Überschreibung der `OnStop`-Methode.

        public override void OnStop()
        {
            Trace.TraceInformation("OnStop called from WebRole");
            var rcCounter = new PerformanceCounter("ASP.NET", "Requests Current", "");
            while (rcCounter.NextValue() > 0)
            {
                Trace.TraceInformation("ASP.NET Requests Current = " + rcCounter.NextValue().ToString());
                System.Threading.Thread.Sleep(1000);
            }
        }

Die `OnStop`-Methode hat bis zu 5 Minuten Zeit, bevor die Anwendung heruntergefahren wird. Sie könnten in Ihrer `OnStop`-Methode einen 5-minütigen Sleep-Aufruf einfügen, um Ihrer Anwendung die maximale Zeit für die Verarbeitung der aktuellen Anforderungen zu geben. Wenn Ihre Anwendung jedoch korrekt skaliert ist, sollte sie die verbleibenden Anforderungen in viel kürzerer Zeit bearbeiten können. Die Anwendung sollte schnellstmöglich angehalten werden, um möglichst zeitnah neu starten zu können und mit der Verarbeitung von Anforderungen fortzufahren.

Sobald Azure eine Rolle vom Netz nimmt, hört das Lastenausgleichsmodul auf, Anforderungen an die Rolleninstanz zu schicken, und erst dann wird die `OnStop`-Methode aufgerufen. Falls dies die einzige Instanz Ihrer Rolle ist, werden keine Anforderungen bearbeitet, bis Ihre Rolle heruntergefahren und neu gestartet wurde (dieser Vorgang dauert normalerweise mehrere Minuten). Aus diesem Grund müssen Sie für die Azure-Servicelevel-Vereinbarung mindestens zwei Instanzen von jeder Rolle haben, um die garantierte Betriebszeit nutzen zu können.

Der gezeigte Code für die `OnStop`-Methode erstellt einen ASP.NET-Leistungsindikator für `Requests Current`. Der Wert des `Requests Current`-Zählers enthält die aktuelle Anzahl der Anforderungen, inklusive solcher, die sich momentan in der Warteschlange befinden, ausgeführt werden oder darauf warten, zum Client geschrieben zu werden. Der Wert `Requests Current` wird einmal pro Sekunde geprüft, und sobald der Wert null erreicht, wird die `OnStop`-Methode verlassen. Nach Verlassen der `OnStop`-Methode wird die Rolle heruntergefahren.

Ablaufverfolgungsdaten aus der `OnStop`-Methode werden nur gespeichert, wenn eine [bedarfsgesteuerte Übertragung][bedarfsgesteuerte Übertragung] durchgeführt wird. Sie können die `OnStop`-Ablaufverfolgungsinformationen in Echtzeit mithilfe des [dbgview][dbgview]-Hilfsprogramms über eine Remotedesktopverbindung anzeigen.

## <a name="createifnotexists"></a>Hinzufügen von Code zum Erstellen von Tabellen, Warteschlange und Blob-Container zur Methode Application\_Start

Die Webanwendung verwendet die Tabellen `MailingList` und `Message`, die Warteschlange `azuremailsubscribequeue` und den Blob-Container `azuremailblobcontainer`. Sie könnten diese Elemente manuell mithilfe eines Tools wie dem Azure-Speicher-Explorer erstellen. Allerdings müssten Sie diesen Vorgang jedes mal neu ausführen, wenn Sie die Anwendung mit einem neuen Speicherkonto verwenden. Der Code im folgenden Abschnitt wird beim Anwendungsstart ausgeführt, prüft ob die benötigten Tabellen, Warteschlangen und Blob-Container existieren und erstellt sie gegebenenfalls.

Sie können diesen Code für den Anwendungsstart entweder zur `OnStart`-Methode in der Datei *WebRole.cs* oder zur Datei *Global.asax* hinzufügen. In diesem Lernprogramm initialisieren Sie den Azure-Speicher in der Datei *Global.asax*.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt MvcWebRole und fügen Sie die Datei *Global.asax.cs* aus dem heruntergeladenen Projekt hinzu.

Sie haben eine neue Methode hinzugefügt, die aus der `Application_Start`-Methode abgerufen wird:

        private static void CreateTablesQueuesBlobContainers()
        {
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            var tableClient = storageAccount.CreateCloudTableClient();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();

            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
            blobContainer.CreateIfNotExists();

            var queueClient = storageAccount.CreateCloudQueueClient();
            var subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");
            subscribeQueue.CreateIfNotExists();
        }

In den folgenden Abschnitten erstellen Sie die Komponenten der Webanwendung. Sie können diese entweder im Entwicklungsspeicher oder in Ihrem Speicherkonto testen, ohne zuvor Tabellen, Warteschlangen oder Blob-Container manuell erstellen zu müssen.

## <a name="mailinglist"></a>Erstellen und Testen der Mailinglisten-Controller und -Ansichten

Administratoren können in der **Mailinglisten**-Web-GUI ihre Mailinglisten erstellen, bearbeiten und anzeigen, wie z. B. "Ankündigungen der Fakultät für Geschichte an der Contoso University" und "Technische Stellenausschreibungen von Fabrikam".

### Fügen Sie die MailingList-Entitätsklasse zum Ordner Models hinzu

Die `MailingList`-Entitätsklasse bildet die Zeilen in der Tabelle `MailingList` ab, die Informationen über die Liste enthalten, wie z. B. deren Beschreibung und die Absenderadresse für E-Mails, die an die Liste verschickt werden.

1.  Fügen Sie im Ordner `Models` im MVC-Projekt die Datei *MailingList.cs* aus dem heruntergeladenen Projekt hinzu.

    Sie werden die `MailingList`-Entitätsklasse verwenden, um Mailinglistenzeilen aus der Tabelle zu lesen und in die Tabelle zu schreiben.

        public class MailingList : TableEntity
        {
            public MailingList()
            {
                this.RowKey = "mailinglist";
            }

            [Required]
            [RegularExpression(@"[\w]+",
             ErrorMessage = @"Only alphanumeric characters and underscore (_) are allowed.")]
            [Display(Name = "List Name")]
            public string ListName
            {
                get
                {
                    return this.PartitionKey;
                }
                set
                {
                    this.PartitionKey = value;
                }
            }

            [Required]
            [Display(Name = "'From' Email Address")]
            public string FromEmailAddress { get; set; }

            public string Description { get; set; }
        }

    Die Azure-Speicher-API verlangt, dass die Entitätsklassen für Ihre Tabellenoperationen von [TableEntity][TableEntity] abgeleitet werden. `TableEntity` definiert die Felder `PartitionKey`, `RowKey`, `TimeStamp` und `ETag`. Die `TimeStamp`- und `ETag`-Eigenschaften werden vom System verwendet. Später in diesem Lernprogramm werden Sie sehen, wie die `ETag`-Eigenschaft für die Parallelitätsbehandlung verwendet wird.

    (Mit der Klasse [DynamicTableEntity][DynamicTableEntity] können Sie mit Tabellenzeilen als Wörterbuchauflistungen von Schlüssel-Wert-Paaren arbeiten, anstatt vordefinierte Modellklassen zu verwenden. Weitere Informationen finden Sie unter [Azure Storage Client Library 2.0 Tables Deep Dive][Azure Storage Client Library 2.0 Tables Deep Dive] (Arbeiten mit Tabellen der Azure-Speicherclientbibliothek 2.0, in englischer Sprache).

    Der Partitionsschlüssel der Tabelle `mailinglist` ist der Listenname. Der Zugriff auf den Partitionsschlüssel in dieser Entitätsklasse erfolgt entweder über die `PartitionKey`-Eigenschaft (definiert in der Klasse `TableEntity`) oder über die `ListName`-Eigenschaft (definiert in der Klasse `MailingList`). Die `ListName`-Eigenschaft verwendet `PartitionKey` als Unterstützungsvariable. Dank der `ListName`-Eigenschaft können Sie einen anschaulicheren Variablennamen im Code verwenden und erleichtern sich die Programmierung der Web-GUI, da die DataAnnotations-Attribute für Formatierung und Prüfung zur `ListName`-Eigenschaft hinzugefügt werden können, jedoch nicht direkt zur `PartitionKey`-Eigenschaft.

    Das `RegularExpression`-Attribut in der `ListName`-Eigenschaft weist MVC an, die Benutzereingaben zu prüfen, um sicherzustellen, dass der eingegebene Listenname nur alphanumerische Zeichen oder Unterstriche enthält. Mit dieser Einschränkung werden einfache Listennamen erzwungen, die problemlos in Abfragezeichenfolgen in URLs verwendet werden können.

    > [WACOM.NOTE] Falls Sie ein weniger restriktives Format für die Listennamen verwenden möchten, können Sie weitere Zeichen zulassen und die Listennamen vor der Verwendung in Abfragezeichenfolgen per URL-Codierung umwandeln. Bestimmte Zeichen sind jedoch in Partitions- und Zeilenschlüsseln von Azure-Tabellen nicht erlaubt, und Sie müssen zumindest diese Zeichen ausschließen. Weitere Informationen zu problematischen Zeichen in Partitions- und Zeilenschlüsseln finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell][Grundlegendes zum Tabellendienst-Datenmodell] und [% Character in PartitionKey or RowKey][% Character in PartitionKey or RowKey] (%-Zeichen in Partitions- und Zeilenschlüsseln, in englischer Sprache).

    Die `MailingList` definiert einen Standardkonstruktor, der für `RowKey` den fest einprogrammierten Wert "mailinglist" setzt, da alle Mailinglisten-Zeilen in der Tabelle diesen Wert als Zeilenschlüssel verwenden. (Eine Erklärung der Tabellenstruktur finden Sie im [ersten Lernprogramm in dieser Serie][ersten Lernprogramm der Serie].) An dieser Stelle könnte jeder beliebige konstante Wert stehen, vorausgesetzt dieser Wert wird nie als E-Mail-Adresse verwendet (der Zeilenschlüssel für Abonnentenzeilen in dieser Tabelle).

    Listenname und Absender-E-Mail-Adresse müssen bei der Erstellung neuer `MailingList`-Entitäten immer angegeben werden. Daher haben diese Werte ein `Required`-Attribut.

    Die `Display`-Attribute geben die Standardbeschriftung der jeweiligen Felder in der MVC-GUI an.

### Fügen Sie den MailingList MVC-Controller hinzu

1.  Fügen Sie im Ordner *Controllers* im MVC-Projekt die Datei *MailingListController.cs* aus dem heruntergeladenen Projekt hinzu.

    Der Standardkonstruktor des Controllers erstellt ein `CloudTable`-Objekt für die Arbeit mit der Tabelle `mailinglist`.

        public class MailingListController : Controller
        {
            private CloudTable mailingListTable;

            public MailingListController()
            {
                var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

                var tableClient = storageAccount.CreateCloudTableClient();
                mailingListTable = tableClient.GetTableReference("mailinglist");

    Der Code liest die Anmeldeinformationen für Ihr Azure-Speicherkonto aus der Einstellungsdatei des Cloud-Dienst-Projekts, um sich mit Ihrem Speicherkonto zu verbinden. (Sie werden diese Einstellungen im weiteren Verlauf des Lernprogramms konfigurieren, bevor Sie den Controller testen.)

    Anschließend folgt die Methode `FindRowAsync`. Diese Methode wird immer dann aufgerufen, wenn der Controller einen bestimmten Mailinglisteneintrag in der Tabelle `MailingList` nachschlagen muss, z. B. beim Bearbeiten eines Mailinglisteneintrags. Der Code ruft eine einzige `MailingList`-Entität ab, indem er die übergebenen Werte für Partitions- und Zeilenschlüssel verwendet. Dieser Controller bearbeitet nur Zeilen, die "MailingList" als Zeilenschlüssel haben. Sie könnten daher "MailingList" als Zeilenschlüssel fest einprogrammieren, aber die Übergabe von Partitions- und Zeilenschlüssel ist ein Muster für die `FindRow`-Methoden in allen Controllern.

    > [WACOM.NOTE] Diese Anwendung verwendet asynchronen ASP.NET 4.5-Code für E/A-Operationen in der Webrolle, um die Serverressourcen effizient zu nutzen. Weitere Informationen zu asynchronem Code in Webanwendungen finden Sie unter [Verwenden der asynchronen Funktionen in .NET 4.5 zum Vermeiden von blockierenden Aufrufen][Verwenden der asynchronen Funktionen in .NET 4.5 zum Vermeiden von blockierenden Aufrufen].

        private async Task<MailingList> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                throw new Exception("No mailing list found for: " + partitionKey);
            }
            return mailingList;
        }

    Der Code in dieser `FindRow`-Methode gibt eine Mailinglistenzeile zurück. Der Code in der entsprechenden `FindRow`-Methode im `Subscriber`-Controller gibt eine Abonnentenzeile aus derselben `mailinglist`-Tabelle zurück. Der Code der beiden Methoden ist identisch mit Ausnahme des Modelltyps, der für die [TableOperation.Retrieve][TableOperation.Retrieve]-Methode verwendet wird.

        private async Task<Subscriber> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var subscriber = retrievedResult.Result as Subscriber;
            if (subscriber == null)
            {
                throw new Exception("No subscriber found for: " + partitionKey + ", " + rowKey);
            }
            return subscriber;
        }

    Das `TableOperation`-Objekt, das von der `TableOperation.Retrieve`-Methode zurückgegeben wird, gibt das Schema (die Eigenschaften) der Zeile(n) an, die Sie als Ergebnis der Abfrage erwarten. Die Zeilen in einer einzigen Tabelle können unterschiedliche Schemata haben. Normalerweise verwenden Sie beim Auslesen einer Zeile denselben Modelltyp wie bei deren Erstellung.

    Auf der **Index**-Seite werden alle Mailinglistenzeilen angezeigt. Daher gibt die Abfrage in der `Index`-Methode alle `MailingList`-Entitäten zurück, die den Zeilenschlüssel "mailinglist" haben (die restlichen Zeilen in der Tabelle haben E-Mail-Adressen als Zeilenschlüssel und enthalten Informationen zu Abonnenten).

        var query = new TableQuery<MailingList>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.Equal, "mailinglist"));
        TableContinuationToken token = null;
        OperationContext ctx = new OperationContext();
        TableQuerySegment<MailingList> currentSegment = null;
        while (currentSegment == null || currentSegment.ContinuationToken != null)
        {
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync
                (query, token, webUIRetryPolicy, ctx);
            lists.AddRange(currentSegment.Results);
            token = currentSegment.ContinuationToken;
        }

    Die `ExecuteQuerySegmentedAsync`-Methode teilt große Ergebnissätze in Segmente auf. Sie gibt bis zu 1.000 Zeilen zurück. Wenn Sie eine Abfrage ausführen, die mehr als 1.000 Zeilen zurückliefern würde, erhalten Sie stattdessen 1.000 Zeilen und ein Fortsetzungstoken. Mit dem Fortsetzungstoken können Sie eine weitere Abfrage ab dem Ende der vorherigen Abfrage ausführen. Der gezeigte Code wurde für die Beispielanwendung vereinfacht: er aggregiert alle Segmente in eine Liste. In einer Produktionsanwendung würden Sie stattdessen eine Seitenverwaltung implementieren. Weitere Informationen zu großen Resultsets und Fortsetzungstoken finden Sie unter [How to get most out of Azure Tables][How to get most out of Azure Tables] (Optimale Nutzung von Azure-Tabellen, in englischer Sprache) und [Azure Tables: Expect Continuation Tokens, Seriously][Azure Tables: Expect Continuation Tokens, Seriously] (Azure-Tabellen, erwarten Sie Fortsetzungstoken, im Ernst, in englischer Sprache).

    Bei der Erstellung des `OperationContext`-Objekts können Sie den `ClientID`-Eigenschaftswert setzen, um einen eindeutigen Bezeichner anzugeben, der in die vom Azure-Speicher geschriebenen Protokolle integriert wird. Mit diesem Bezeichner können Sie Protokolle von Speicheroperationen auf den Code zurückverfolgen, der die Speicheraktivität ausgelöst hat. Weitere Informationen zu r Protokollierung in Azure finden Sie unter [Azure-Speicher-Protokollierung: Verfolgen von Speicheranfragen mithilfe von Protokollen][Azure-Speicher-Protokollierung: Verfolgen von Speicheranfragen mithilfe von Protokollen].

    Mit der SCL API 2.1 und neueren Versionen können Sie auch LINQ für Ihre Tabellenabfragen verwenden. Ein Codebeispiel für die Verwendung von LINQ finden Sie unter [PhluffyFotos][PhluffyFotos].

    Wenn Sie keine Wiederholungsrichtlinie angeben, wiederholt die API den Vorgang drei mal mit exponentiell ansteigendem Zeitlimit. Für Weboberflächen, in denen ein Benutzer auf die Anzeige der nächsten Seite warten, kann dies zu inakzeptablen Wartezeiten führen. Dieser Code verwendet daher lineare Wiederholungsversuche (mit jeweils dem gleichen Zeitlimit) und einen Wert für die Zeitüberschreitung, der für Benutzer akzeptabel ist. Die Wiederholungsrichtlinie wird im `webUIRetryPolicy`-Objekt angegeben, das an die `ExecuteQuerySegmentedAsync`-Methode übergeben wird. Das `webUIRetryPolicy`-Objekt ist im Konstruktor des Controllers definiert:

        private TableRequestOptions webUIRetryPolicy;

        public MailingListController()
        {
            // Other constructor code not shown.

            webUIRetryPolicy = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
        }

    Die `Index`-Methode behandelt Zeitüberschreitungen in einem try-catch-Block.

        try
        {
            // Code not shown for retrieving MailingList rows.
        }
        catch (StorageException se)
        {
            ViewBag.errorMessage = "Timeout error, try again. ";
            Trace.TraceError(se.Message);
            return View("Error");
        }

    Wenn der Benutzer auf die Schaltfläche **Create** auf der Seite **Create** klickt, erstellt die MVC-Modellbindung eine `MailingList`-Entität mit den Eingaben aus der Ansicht, und die `HttpPost Create`-Methode fügt die Entität in die Tabelle ein.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(MailingList mailingList)
        {
            if (ModelState.IsValid)
            {
                var insertOperation = TableOperation.Insert(mailingList);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }

            return View(mailingList);
        }

    Auf der Seite **Edit** wird die Zeile in der `HttpPost Edit`-Methode aktualisiert.

        var replaceOperation = TableOperation.Replace(mailingList);
        await mailingListTable.ExecuteAsync(replaceOperation);

    In der `HttpPost Edit`-Methode werden Parallelitätsfehler in einem try-catch-Block behandelt.

        catch (StorageException ex)
        {
            if (ex.RequestInformation.HttpStatusCode == 412)
            {
                // Concurrency error
                var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
                var retrievedResult = mailingListTable.Execute(retrieveOperation);
                var currentMailingList = retrievedResult.Result as MailingList;
                if (currentMailingList == null)
                {
                    ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                        + "was deleted by another user after you got the original value. The "
                        + "edit operation was canceled. Click the Back to List hyperlink.");
                }
                if (currentMailingList.FromEmailAddress != editedMailingList.FromEmailAddress)
                {
                    ModelState.AddModelError("FromEmailAddress", "Current value: " + currentMailingList.FromEmailAddress);
                }
                if (currentMailingList.Description != editedMailingList.Description)
                {
                    ModelState.AddModelError("Description", "Current value: " + currentMailingList.Description);
                }
                ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                    + "was modified by another user after you got the original value. The "
                    + "edit operation was canceled and the current values in the database "
                    + "have been displayed. If you still want to edit this record, click "
                    + "the Save button again. Otherwise click the Back to List hyperlink.");
                 ModelState.SetModelValue("ETag", new ValueProviderResult(currentMailingList.ETag, currentMailingList.ETag, null));
            }
            else
            {
                throw; 
            }
        }

    Parallelitätsausnahmen werden ausgelöst, wenn ein Benutzer eine Mailingliste zum Bearbeiten auswählt und ein anderer Benutzer dieselbe Mailingliste bearbeitet, während die **Edit**-Seite im Browser angezeigt wird. In einem solchen Fall zeigt der Code eine Warnmeldung an und gibt an, welche Felder von dem anderen Benutzer geändert wurden. Die TSL-API verwendet `ETag` zur Erkennung von Parallelitätsproblemen. Der `ETag`-Wert wird bei jeder Änderung an einer Tabellenzeile geändert. Beim Abrufen einer zu bearbeitenden Zeile speichern Sie den `ETag`-Wert, und beim Ausführen der update- oder delete-Operation übergeben Sie den gespeicherten `ETag`-Wert. (Die `Edit`-Ansicht enthält ein verborgenes Feld für den ETag-Wert.) Falls eine update-Operation feststellt, dass sich der `ETag`-Wert eines Eintrags vom `ETag`-Wert unterscheidet, den Sie der update-Operation übergeben haben, wird eine Parallelitäts-Ausnahme ausgelöst. Falls Ihnen Parallelitätsprobleme egal sind, können Sie das ETag-Feld in der Entität, die Sie an die update-Operation übergeben, mit einem Sternchen ("\*") ausfüllen. Dies führt dazu, dass alle Konflikte ignoriert werden.

    Hinweis: Der HTTP-Fehlercode 412 wird nicht ausschließlich für Parallelitätsprobleme verwendet. Die SCL-API kann diesen Code auch aus anderen Gründen auslösen.

    Für die **Delete**-Seite löscht die `HttpPost Delete`-Methode die `MailingList`-Zeile zusammen mit allen `Subscriber`-Zeilen, die der entsprechenden Mailingliste in der Tabelle `MailingList` zugeordnet sind.

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> DeleteConfirmed(string partitionKey)
        {
            // Delete all rows for this mailing list, that is, 
            // Subscriber rows as well as MailingList rows.
            // Therefore, no need to specify row key.

            // Get all rows for this mailing list. For a production app where this 
            // could return too many split the work up into segments.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            List<MailingList> listRows = new List<MailingList>();
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                listRows.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }

            // Delete the rows in batches of 100.
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    await mailingListTable.ExecuteBatchAsync(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
            return RedirectToAction("Index");
        }

    Falls eine große Anzahl von Abonnenten zu löschen ist, löscht der Code die Einträge stapelweise. Die Transaktionskosten für das Löschen einer Zeile sind gleich wie beim stapelweisen Löschen von 100 Zeilen. Pro Stapel können maximal 100 Operationen ausgeführt werden.

    Obwohl die Schleife sowohl `MailingList`-Zeilen als auch `Subscriber`-Zeilen verarbeitet, werden alle Zeilen in die `MailingList`-Entitätsklasse gelesen, da für die `Delete`-Operation nur die Felder `PartitionKey`, `RowKey` und `ETag` benötigt werden.

### Fügen Sie die MailingList MVC-Ansichten hinzu

1.  Erstellen Sie im Ordner *Views* im MVC-Projekt einen neuen Ordner mit dem Namen *MailingList*.

2.  Fügen Sie im Ordner *Views\\MailingList* alle vier *.cshtml*-Dateien aus dem heruntergeladenen Projekt hinzu.

Beachten Sie das verborgene Feld in der Datei *Edit.cshtml* zum Speichern des `ETag`-Werts, der zur Behandlung von Parallelitätsproblemen verwendet wird.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
        
            @Html.HiddenFor(model => model.ETag)

Beachten Sie außerdem, dass das Feld `ListName` eine `DisplayFor`-Hilfe anstatt einer `EditorFor`-Hilfe hat.

        <div class="form-group">
            @Html.LabelFor(model => model.ListName, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.DisplayFor(model => model.ListName, new { htmlAttributes = new { @class = "form-control" } })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.FromEmailAddress, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.FromEmailAddress, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.FromEmailAddress, "", new { @class = "text-danger" })
            </div>
        </div>

Wir erlauben keine Änderungen am Listennamen in der **Edit**-Seite, da der Code im Controller in diesem Fall wesentlich komplexer sein müsste: Die `HttpPost Edit`-Methode müsste die existierende Mailinglist-Zeile und alle zugehörigen Abonnentenzeilen löschen und anschließend alle Zeilen mit dem neuen Schlüsselwert wieder einfügen. In einer Produktionsanwendung kann diese zusätzliche Komplexität durchaus Sinn machen. Wie Sie später sehen werden, sind Änderungen am Listennamen im `Subscriber`-Controller erlaubt, da in diesem Fall nur jeweils eine Zeile betroffen ist.

Die *Edit*- und **Delete**-Links in der Datei **Index.cshtml** geben die Parameter Partitions- und Zeilenschlüssel für die Abfragezeichenfolge an, um eine bestimmte Zeile identifizieren zu können. Für `MailingList`-Entitäten wird nur ein Partitionsschlüssel benötigt, da der Zeilenschlüssel immer "MailingList" ist. Beide Werte werden jedoch gespeichert, um den Code der MVC-Ansicht in allen Controllern und Ansichten einheitlich zu halten.

        <td>
            @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
            @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
        </td>

### Setzen von MailingList als Standardcontroller

1.  Öffnen Sie die Datei *Route.config.cs* im Ordner *App\_Start*.

2.  Ändern Sie in der Zeile mit den Standardwerten den Standardcontroller von "Home" zu "MailingList".

         routes.MapRoute(
             name: "Default",
             url: "{controller}/{action}/{id}",
             defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }

### Testen der Anwendung

1.  Drücken Sie STRG+F5, um das Projekt auszuführen.

    ![Leere Indexseite der Mailingliste][Leere Indexseite der Mailingliste]

2.  Erstellen Sie einige Mailinglisten mit der **Create**-Funktion und testen Sie, ob die **Edit**- und **Delete**-Funktionen wie gewünscht funktionieren.

    ![Indexseite der Mailingliste mit Einträgen][Indexseite der Mailingliste mit Einträgen]

## <a name="subscriber"></a><span class="short-header">Abonnenten</span>Erstellen und Testen der Abonnenten-Controller und -Ansichten

Administratoren können in der **Abonnenten**-Web-GUI neue Abonnenten zu Mailinglisten hinzufügen und bestehende Abonnenten bearbeiten, anzeigen und löschen.

### Fügen Sie die Subscriber-Entitätsklasse zum Ordner Models hinzu

Die `Subscriber`-Entitätsklasse bildet die Zeilen in der Tabelle `MailingList` ab, die Informationen über Abonnenten von Listen enthalten. Diese Zeilen enthalten Daten wie z. B. die E-Mail-Adresse der Person und eine Angabe, ob die Adresse bestätigt wurde.

1.  Fügen Sie im Ordner *Models* im MVC-Projekt die Datei *Subscriber.cs* aus dem heruntergeladenen Projekt hinzu.

Ebenso wie die `MailingList`-Entitätsklasse liest und schreibt die `Subscriber`-Entitätsklasse Zeilen in der Tabelle `mailinglist`.

            public class Subscriber : TableEntity
            {
                [Required]
                public string ListName
                {
                    get
                    {
                        return this.PartitionKey;
                    }
                    set
                    {
                        this.PartitionKey = value;
                    }
                }
        
                [Required]
                [Display(Name = "Email Address")]
                public string EmailAddress
                {
                    get
                    {
                        return this.RowKey;
                    }
                    set
                    {
                        this.RowKey = value;
                    }
                }
        
                public string SubscriberGUID { get; set; }
        
                public bool? Verified { get; set; }
            }
        

`Subscriber`-Zeilen verwenden die E-Mail-Adresse anstelle der Konstante "mailinglist" als Zeilenschlüssel. (Eine Erklärung der Tabellenstruktur finden Sie im [ersten Lernprogramm in dieser Serie][ersten Lernprogramm der Serie].) Aus diesem Grund wird eine `EmailAddress`-Eigenschaft definiert, welche die `RowKey`-Eigenschaft als Unterstützungsfeld verwendet, ebenso wie `ListName` die `PartitionKey`-Eigenschaft als Unterstützungsfeld verwendet. Wie bereits erklärt versetzt Sie dies in die Lage, DataAnnotations-Attribute für Formatierung und Prüfung für die Eigenschaften zu verwenden.

Der `SubscriberGUID`-Wert wird bei der Erstellung der `Subscriber`-Entität generiert. Dieser Wert wird in Links zum Abonnieren/Abbestellen verwendet, um sicherzustellen, dass nur autorisierte Personen Abonnements für E-Mail-Adressen hinzufügen und entfernen können.

Wenn eine Zeile zum ersten Mal für einen neuen Abonnenten erstellt wird, ist der `Verified`-Wert `false`. Der Wert für `Verified` wird erst auf `true` gesetzt, nachdem der neue Abonnent auf den **Bestätigungs**-Link in der Willkommens-E-Mail klickt. Beim Versand von Nachrichten an Listen erhalten diejenigen Abonnenten, bei denen `Verified` = `false` ist, keine E-Mail.

Die `Verified`-Eigenschaft in der `Subscriber`-Entität lässt Nullwerte zu. Wenn Sie angeben, dass eine Abfrage `Subscriber`-Entitäten zurückgeben soll, kann es sein, dass manche der Zeilen keine `Verified`-Eigenschaft enthalten. Daher ist die `Subscriber`-Eigenschaft in der `Verified`-Entität als nullbar definiert, um den tatsächlichen Inhalt von Tabellenzeilen genauer abbilden zu können, die keine *Verified*-Eigenschaft haben. Möglicherweise sind Sie es gewöhnt, mit SQL Server-Tabellen zu arbeiten, in denen jede Zeile einer Tabelle dasselbe Schema hat. In Azure-Speichertabellen ist jede Zeile nur eine Sammlung von Eigenschaften, und jede Zeile kann unterschiedliche Eigenschaften enthalten. Zeilen mit "MailingList" als Zeilenschlüssel haben in der Azure-E-Mail-Beispielanwendung z. B. keine `Verified`-Eigenschaft. Wenn eine Abfrage eine Zeile ohne `Verified`-Eigenschaft zurückgibt, dann hat die `Verified`-Eigenschaft bei der Instantiierung der `Subscriber`-Entitätsklasse den Wert null. Wenn die Eigenschaft nicht nullbar wäre, würden Sie den Wert `false` sowohl für Zeilen mit `Verified` = `false` als auch für Zeilen ohne `Verified`-Eigenschaft erhalten. Daher sollten bei der Arbeit mit Azure-Tabellen nach Möglichkeit alle Eigenschaften von Entitätsklassen nullbar sein, um auch solche Zeilen korrekt auslesen zu können, die von anderen Entitätsklassen oder von anderen Versionen der aktuellen Entitätsklasse erstellt wurden.

### Fügen Sie den Subscriber MVC-Controller hinzu

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Controllers* im MVC-Projekt, und klicken Sie anschließend auf **Vorhandenes Element hinzufügen**.

2.  Navigieren Sie zum Ordner, in dem Sie die heruntergeladene Beispielanwendung gespeichert haben, wählen Sie die Datei *SubscriberController.cs* im Ordner *Controllers* aus und klicken Sie auf **Hinzufügen**. (Stellen Sie sicher, dass Sie *Subscriber.cs* öffnen und nicht *Subscribe.cs*; Sie werden *Subscribe.cs* später hinzufügen.)

Ein Großteil des Codes in diesem Controller ähnelt dem Code im `MailingList`-Controller. Selbst der Tabellenname ist derselbe, da die Abonnenteninformationen in der Tabelle `MailingList` gespeichert werden.

Zusätzlich zur `FindRowAsync`-Methode existiert hier eine `FindRow`-Methode, da diese aus einem catch-Block heraus aufgerufen werden muss. Asynchrone Methoden können nicht aus catch-Blöcken heraus aufgerufen werden.

Nach den `FindRow`-Methoden sehen Sie eine `GetListNamesAsync`-Methode. Diese Methode liest die Daten für eine Dropdownliste in den **Create-** und **Edit**-Seiten, aus der Sie die Mailingliste auswählen, zu der eine E-Mail-Adresse hinzugefügt werden soll.

        private async Task<List<MailingList>> GetListNamesAsync()
        {
            List<MailingList> lists = new List<MailingList>();
            var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                lists.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }
            return lists;
        }

Dies ist dieselbe Abfrage, die Sie bereits im `MailingList`-Controller gesehen haben. Für die Dropdownliste benötigen Sie nur Zeilen mit Informationen über Mailinglisten, daher wählen Sie nur Zeilen mit RowKey = "mailinglist" aus.

Die Methode, die Daten für die **Index**-Seite abruft, benötigt Zeilen mit Abonnenteninformationen, daher wählen Sie alle Zeilen aus, für die RowKey = "MailingList" nicht gilt.

        var query = (new TableQuery<Subscriber>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.NotEqual, "mailinglist")));

Die Abfrage gibt an, dass die Daten in `Subscriber`-Objekte gelesen werden (durch die Angabe von `<Subscriber>`), aber die Daten stammen aus der Tabelle `mailinglist`.

Die Anzahl der Abonnenten kann unter Umständen zu groß werden, um auf diese Weise in einer einzigen Abfrage verarbeitet zu werden. Wie bereits angemerkt würden Sie für eine Produktionsanwendung eine Seitenverwaltung mithilfe von Fortsetzungstoken implementieren.

In der `HttpGet Create`-Methode werden die Daten für die Dropdownliste vorbereitet, und in der `HttpPost`-Methode setzen Sie die Standardwerte vor dem Speichern der neuen Entität.

        public async Task<ActionResult> Create()
        {
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            var model = new Subscriber() { Verified = false };
            return View(model);
        }
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Subscriber subscriber)
        {
            if (ModelState.IsValid)
            {
                subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                if (subscriber.Verified.HasValue == false)
                {
                    subscriber.Verified = false;
                }
        
                var insertOperation = TableOperation.Insert(subscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);
        
            return View(subscriber);
        }

Die `HttpPost Edit`-Seite ist komplexer als der `MailingList`-Controller, da Sie auf der `Subscriber`-Seite die Schlüsselfelder Listenname und E-Mail-Adresse ändern können. Wenn ein Benutzer eines dieser Felder ändert, müssen Sie den existierenden Eintrag löschen und einen neuen Eintrag hinzufügen, anstatt den bestehenden Eintrag zu ändern. Der folgende Code zeigt einen Teil der Bearbeitungsmethode, welche die unterschiedlichen Prozeduren für Schlüsseländerungen und Nicht-Schlüsseländerungen verarbeitet:

        UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
        if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
        {
            //Keys didn't change -- Update the row
            var replaceOperation = TableOperation.Replace(editedSubscriber);
            await mailingListTable.ExecuteAsync(replaceOperation);
        }
        else
        {
            // Keys changed, delete the old record and insert the new one.
            if (editedSubscriber.PartitionKey != partitionKey)
            {
                // PartitionKey changed, can't do delete/insert in a batch.
                var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                await mailingListTable.ExecuteAsync(deleteOperation);
                var insertOperation = TableOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
            }
            else
            {
                // RowKey changed, do delete/insert in a batch.
                var batchOperation = new TableBatchOperation();
                batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                batchOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
        }

Die von der MVC-Modellbindung an die `Edit`-Methode übergebenen Parameter enthalten den Original-Listennamen und die E-Mail-Adresse (in den Parametern `partitionKey` und `rowKey`) sowie die vom Benutzer eingegebenen Werte (in den Parametern `listName` und `emailAddress`):

        public async Task<ActionResult> Edit(string partitionKey, string rowKey, string listName, string emailAddress, Subscriber editedSubscriber)

Die an die `UpdateModel`-Methode übergebenen Parameter schließen die `PartitionKey`- und `RowKey`-Eigenschaften von der Modellbindung aus:

        var excludeProperties = new string[] { "PartitionKey", "RowKey" };
            

Dies liegt daran, dass die `ListName`- und `EmailAddress`-Eigenschaften `PartitionKey` und `RowKey` als Unterstützungseigenschaften verwenden und der Benutzer möglicherweise einen dieser Werte geändert haben könnte. Wenn die Modellbindung das Modell durch Setzen der `ListName`-Eigenschaft aktualisiert, wird die `PartitionKey`-Eigenschaft automatisch aktualisiert. Falls die Modellbindung die `PartitionKey`-Eigenschaft nach der Aktualisierung der `ListName`-Eigenschaft ihrem eigenen Originalwert aktualisieren würde, müsste sie dabei den neuen, von der `ListName`-Eigenschaft gesetzten Wert überschreiben. Die `EmailAddress`-Eigenschaft aktualisiert die `RowKey`-Eigenschaft automatisch auf dieselbe Weise.

Nach der Aktualisierung des `editedSubscriber`-Modellobjekts prüft der Code, ob Partitions- oder Zeilenschlüssel geändert wurde. Falls einer dieser Werte geändert wurde, muss die existierende Abonnentenzeile gelöscht und eine neue Zeile eingefügt werden. Falls nur der Zeilenschlüssel geändert wurde, können Löschung und Einfügung in einer atomischen Stapeltransaktion ausgeführt werden.

Beachten Sie, dass der Code eine neue Entität zur Übergabe in der `Delete`-Operation erstellt:

            // RowKey changed, do delete/insert in a batch.
            var batchOperation = new TableBatchOperation();
            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
            batchOperation.Insert(editedSubscriber);
            await mailingListTable.ExecuteBatchAsync(batchOperation);

Die an Stapeloperationen übergebenen Entitäten müssen immer eindeutig sein. Sie können z. B. keine `Subscriber`-Entität erstellen, diese an eine `Delete`-Operation übergeben, anschließend einen Wert in derselben `Subscriber`-Entität ändern und diese an eine `Insert`-Operation übergeben. In diesem Fall wäre der Status der Entität nach der Eigenschaftsänderung sowohl für die Delete- als auch für die Insert-Operation gültig.

Operationen in einem Stapel müssen alle auf derselben Partition ausgeführt werden. Änderungen an Listennamen wirken sich auf den Partitionsschlüssel aus und können daher nicht in einer Transaktion ausgeführt werden.

### Fügen Sie die Subscriber MVC-Ansichten hinzu

1.  Erstellen Sie im **Projektmappen-Explorer** einen neuen Ordner unter dem Ordner *Views* im MVC-Projekt und nennen Sie den Ordner *Subscriber*.

2.  Klicken Sie mit der rechten Maustaste auf den neuen *Views\\Subscriber*-Ordner und wählen Sie **Vorhandenes Element hinzufügen**.

3.  Navigieren Sie zum Ordner, in dem Sie die heruntergeladene Beispielanwendung gespeichert haben, wählen Sie alle vier .cshtml-Dateien im Ordner *Views\\Subscriber* aus und klicken Sie auf **Hinzufügen**.

Die Datei *Edit.cshtml* enthält ein verborgenes Feld zum Speichern des `SubscriberGUID`-Werts, da dieser Wert nicht angezeigt wird und daher nicht automatisch in einem Formularfeld verfügbar ist.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.SubscriberGUID)
            @Html.HiddenFor(model => model.ETag)
                        

### Testen der Anwendung

1.  Drücken Sie STRG+F5, um das Projekt auszuführen, und klicken Sie auf **Subscribers**.

    ![Leere Indexseite für Abonnenten][Leere Indexseite für Abonnenten]

2.  Erstellen Sie einige Mailinglisten mit der **Create**-Funktion und testen Sie, ob die **Edit**- und **Delete**-Funktionen wie gewünscht funktionieren.

    ![Indexseite für Abonnenten mit Einträgen][Indexseite für Abonnenten mit Einträgen]

## <a name="message"></a>Erstellen und Testen der Nachrichten-Controller und -Ansichten

Administratoren können in der **Message**-Web-GUI Informationen über Nachrichten, deren Versand an eine Mailingliste geplant wurde, erstellen, bearbeiten und anzeigen.

### Fügen Sie die Message-Entitätsklasse zum Ordner Models hinzu

1.  Fügen Sie im Ordner *Models* im MVC-Projekt die Datei *Message.cs* aus dem heruntergeladenen Projekt hinzu.

Die `Message`-Entitätsklasse bildet die Zeilen in der Tabelle `Message` ab, die Informationen über Nachrichten enthalten, deren Versand an eine Liste geplant wurde. Diese Zeilen enthalten Informationen wie z. B. die Betreffzeile, die Liste, an die eine Nachricht verschickt werden soll und das geplante Versanddatum.

        public class Message : TableEntity
        {
            private DateTime? _scheduledDate;
            private long _messageRef;

            public Message()
            {
                this.MessageRef = DateTime.Now.Ticks;
                this.Status = "Pending";
            }

            [Required]
            [Display(Name = "Scheduled Date")]
            // DataType.Date shows Date only (not time) and allows easy hook-up of jQuery DatePicker
            [DataType(DataType.Date)]
            public DateTime? ScheduledDate 
            {
                get
                {
                    return _scheduledDate;
                }
                set
                {
                    _scheduledDate = value;
                    this.PartitionKey = value.Value.ToString("yyyy-MM-dd");
                }
            }
            
            public long MessageRef 
            {
                get
                {
                    return _messageRef;
                }
                set
                {
                    _messageRef = value;
                    this.RowKey = "message" + value.ToString();
                }
            }

            [Required]
            [Display(Name = "List Name")]
            public string ListName { get; set; }

            [Required]
            [Display(Name = "Subject Line")]
            public string SubjectLine { get; set; }

            // Pending, Queuing, Processing, Complete
            public string Status { get; set; }
        }
        

Die `Message`-Klasse definiert einen Standardkonstruktor, der die `MessageRef`-Eigenschaft auf einen eindeutigen Wert für die Nachricht setzt. Da dieser Wert Teil des Zeilenschlüssels ist, setzt der Setter für die `MessageRef`-Eigenschaft automatisch ebenfalls die `RowKey`-Eigenschaft. Der Setter der `MessageRef`-Eigenschaft verkettet die Zeichenfolge "message" mit dem `MessageRef`-Wert und schreibt das Ergebnis in die `RowKey`-Eigenschaft.

Der `MessageRef`-Wert wird aus dem `Ticks`-Wert von `DateTime.Now` erstellt. Damit wird sichergestellt, dass die Nachrichten in der Web-GUI standardmäßig immer in der Erstellungsreihenfolge für ein gegebenes geplantes Datum angezeigt werden (`ScheduledDate` ist der Partitionsschlüssel). Sie könnten die Zeilen auch mithilfe einer GUID eindeutig machen, dann wäre aber die Standardreihenfolge beim Auslesen zufällig.

Der Standardkonstruktor setzt außerdem den Standardstatus Pending (ausstehend) für neue `message`-Zeilen.

Weitere Informationen zur Struktur der Tabelle `Message` finden Sie im [ersten Lernprogramm in dieser Serie][ersten Lernprogramm der Serie].

### Fügen Sie den Message MVC-Controller hinzu

1.  Fügen Sie im Ordner *Controllers* im MVC-Projekt die Datei *MessageController.cs* aus dem heruntergeladenen Projekt hinzu.

Ein Großteil des Codes in diesem Controller ähnelt dem Code im `Subscriber`-Controller. Neu hinzugekommen ist der Code für den Umgang mit Blobs. Für jede Nachricht wird der Inhalt der E-Mail als HTML und als Nur-Text in Form von .htm- bzw. .txt-Dateien hochgeladen und in Blobs gespeichert.

Blobs werden in Blob-Containern gespeichert. Die Azure-E-Mail-Dienstanwendung speichert sämtliche Blobs in einem einzigen Blob-Container namens "azuremailblobcontainer", und der Code im Konstruktor des Controllers ruft einen Verweis auf diesen Blob-Container ab:

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

Für jede Datei, die ein Benutzer zum Hochladen auswählt, liefert die MVC-Ansicht ein `HttpPostedFile`-Objekt, das Informationen über die Datei enthält. Wenn ein Benutzer eine neue Nachricht erstellt, wird das `HttpPostedFile`-Objekt verwendet, um die Datei in ein Blob zu speichern. Beim Ändern von Nachrichten können Benutzer entweder eine Ersatzdatei hochladen oder das Blob unverändert übernehmen.

Der Controller enthält eine Methode, die von `HttpPost Create` und `HttpPost Edit` aufgerufen wird, um das Blob zu speichern:

        private async Task SaveBlobAsync(string blobName, HttpPostedFileBase httpPostedFile)
        {
            // Retrieve reference to a blob.
            var blob = blobContainer.GetBlockBlobReference(blobName);
            // Create the blob or overwrite the existing blob by uploading a local file.
            using (var fileStream = httpPostedFile.InputStream)
            {
                await blob.UploadFromStreamAsync(fileStream);
            }
        }

Die `HttpPost Create`-Methode speichert die beiden Blobs und fügt anschließend die `Message`-Tabellenzeile ein. Der Name des Blobs entsteht durch die Verkettung des `MessageRef`-Werts mit der Dateierweiterung ".htm" bzw. ".txt".

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
        {
            if (file == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide an HTML file path");
            }
        
            if (txtFile == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide a Text file path");
            }
        
            if (ModelState.IsValid)
            {
                await SaveBlobAsync(message.MessageRef + ".htm", file);
                await SaveBlobAsync(message.MessageRef + ".txt", txtFile);
        
                var insertOperation = TableOperation.Insert(message);
                await messageTable.ExecuteAsync(insertOperation);
        
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            return View(message);
        }

Die `HttpGet Edit`-Methode prüft, ob die abgerufene Nachricht den Status `Pending` hat. So wird verhindert, dass ein Benutzer Nachrichten ändert, die bereits von Workerrolle B verarbeitet werden. Die Methoden `HttpPost Edit`, `Delete` und `DeleteConfirmed` enthalten ähnlichen Code.

        if (message.Status != "Pending")
        {
            throw new Exception("Message can't be edited because it isn't in Pending status.");
        }

Der Code in der `HttpPost Edit`-Methode speichert nur dann ein neues Blob, wenn der Benutzer eine neue Datei hochlädt.

        if (httpFile == null)
        {
            // They didn't enter a path or navigate to a file, so don't update the file.
            excludePropLst.Add("HtmlPath");
        }
        else
        {
            // They DID enter a path or navigate to a file, assume it's changed.
            await SaveBlobAsync(editedMsg.MessageRef + ".htm", httpFile);
        }
        
        if (txtFile == null)
        {
            excludePropLst.Add("TextPath");
        }
        else
        {
            await SaveBlobAsync(editedMsg.MessageRef + ".txt", txtFile);
        }

Wenn das Versanddatum geändert wird, ändert sich der Partitionsschlüssel, und die Zeile muss gelöscht und neu eingefügt werden. Dies betrifft mehr als eine Partition und kann daher nicht in einer einzigen Transaktion ausgeführt werden.

        var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
        await messageTable.ExecuteAsync(deleteOperation);
        var insertOperation = TableOperation.Insert(editedMsg);
        await messageTable.ExecuteAsync(insertOperation);

Die `HttpPost Delete`-Methode löscht die Blobs beim Löschen der Tabellenzeile:

        [HttpPost, ActionName("Delete")]
        public async Task<ActionResult> DeleteConfirmed(String partitionKey, string rowKey)
        {
            // Get the row again to make sure it's still in Pending status.
            var message = await FindRowAsync(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be deleted because it isn't in Pending status.");
            }
        
            await DeleteBlobAsync(message.MessageRef + ".htm");
            await DeleteBlobAsync(message.MessageRef + ".txt");
            var deleteOperation = TableOperation.Delete(message);
            messageTable.Execute(deleteOperation);
            return RedirectToAction("Index");
        }
        
        private async Task DeleteBlobAsync(string blobName)
        {
            var blob = blobContainer.GetBlockBlobReference(blobName);
            await blob.DeleteAsync();
        }

### Fügen Sie die Message MVC-Ansichten hinzu

1.  Erstellen Sie im MVC-Projekt einen neuen Ordner unter dem Ordner *Views* mit dem Namen `Message`.

2.  Fügen Sie im Ordner *Views\\Message* alle fünf *.cshtml*-Dateien aus dem heruntergeladenen Projekt hinzu.

Die `HttpPost Edit`-Methode benötigt Partitions- und Zeilenschlüssel, daher werden diese Werte vom Code in der Datei *Edit.cshtml* in verborgenen Feldern bereitgestellt.

        @using (Html.BeginForm("Edit", "Message", FormMethod.Post, new { enctype = "multipart/form-data" }))
        {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.ETag)
            <fieldset>
                <legend>Message</legend>
                @Html.HiddenFor(model => model.MessageRef)
                @Html.HiddenFor(model => model.PartitionKey)
                @Html.HiddenFor(model => model.RowKey)
                

Die verborgenen Felder wurden im `Subscriber`-Controller nicht benötigt, weil: (a) die `ListName`- und `EmailAddress`-Eigenschaften im `Subscriber`-Modell die `PartitionKey`- und `RowKey`-Eigenschaften aktualisieren, und (b) die `ListName`- und `EmailAddress`-Eigenschaften in der Edit-Ansicht durch `EditorFor`-Hilfen enthalten waren. Wenn die MVC-Modellbindung für das `Subscriber`-Modell die `ListName`-Eigenschaft aktualisiert, wird auch die `PartitionKey`-Eigenschaft automatisch aktualisiert, und wenn die MVC-Modellbindung die `EmailAddress`-Eigenschaft im `Subscriber`-Modell aktualisiert, wird auch die `RowKey`-Eigenschaft automatisch aktualisiert. Die Felder, die im `Message`-Modell auf Partitions- und Zeilenschlüssel abgebildet werden, sind nicht bearbeitbar und werden daher nicht auf diese Weise gesetzt.

Für die `MessageRef`-Eigenschaft ist ebenfalls ein verborgenes Feld vorhanden. Dieses enthält denselben Wert wie der Partitionsschlüssel, wird jedoch zur Verbesserung der Übersichtlichkeit in die `HttpPost Edit`-Methode eingeschlossen. Durch das verborgene Feld `MessageRef` ist der Code der `HttpPost Edit`-Methode in der Lage, bei der Erstellung der Dateinamen für die Blobs mit diesem Namen auf den `MessageRef`-Wert zu verweisen.

In der **Index**-Ansicht für Nachrichten in der Datei *Index.cshtml* werden im Gegensatz zu den anderen **Index**-Ansichten werden die **Edit**- und **Delete**-Links hier nur für Nachrichten mit dem Status `Pending` angezeigt:

        <td>
            @if (item.Status == "Pending")
            {
                @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
                @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
            }
            @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
        </td>
                

Damit wird verhindert, dass Benutzer Änderungen an Nachrichten vornehmen, die bereits von Workerrolle A verarbeitet werden.

### Testen der Anwendung

1.  Drücken Sie STRG+F5, um das Projekt auszuführen, und klicken Sie auf **Messages**.

    ![Leere Indexseite für Nachrichten][Leere Indexseite für Nachrichten]

2.  Erstellen Sie einige Mailinglisten mit der **Create**-Funktion und testen Sie, ob die **Edit**- und **Delete**-Funktionen wie gewünscht funktionieren.

    ![Indexseite für Abonnenten mit Einträgen][2]

## <a name="unsubscribe"></a>Erstellen und Testen der Abbestellen-Controller und -Ansichten

Als Nächstes werden Sie die GUI für den Abbestellungsprozess implementieren.

Dieses Lernprogramm behandelt lediglich den Controller für den Abbestellungsprozess, und nicht für den Abonnierungsprozess. Wie bereits im [ersten Lernprogramm][ersten Lernprogramm der Serie] erklärt, sind GUI und Dienstmethode für den Abonnierungsprozess zwar im Download enthalten, werden aber nicht in den Anweisungen des Lernprogramms besprochen, da keine geeigneten Sicherheitsmaßnahmen für die Dienstmethode implementiert werden. Zu Testzwecken können Sie E-Mail-Adressen in den **Subscriber**-Administratorseiten zu Ihren Listen hinzufügen.

### Fügen Sie das Unsubscribe-Anzeigemodell zum Ordner Models hinzu

1.  Fügen Sie im Ordner `Models` im MVC-Projekt die Datei `UnsubscribeVM.cs` aus dem heruntergeladenen Projekt hinzu.

Das `UnsubscribeVM`-Anzeigemodell übergibt Daten zwischen dem `Unsubscribe`-Controller und dessen Ansicht.

        public class UnsubscribeVM
        {
            public string EmailAddress { get; set; }
            public string ListName { get; set; }
            public string ListDescription { get; set; }
            public string SubscriberGUID { get; set; }
            public bool? Confirmed { get; set; }
        }

Die Unsubscribe-Links enthalten die `SubscriberGUID`. Dieser Wert wird verwendet, um E-Mail-Adresse, Listenname und Listenbeschreibung aus der Tabelle `MailingList` abzurufen. Die Ansicht enthält die E-Mail-Adresse und eine Beschreibung der Liste, deren Abonnement abbestellt werden soll, sowie eine **Confirm**-Schaltfläche, die der Benutzer anklicken muss, um die Abbestellung zu bestätigen.

### Fügen Sie den Unsubscribe-Controller hinzu

1.  Fügen Sie im Ordner `Controllers` im MVC-Projekt die Datei *UnsubscribeController.cs* aus dem heruntergeladenen Projekt hinzu.

Dieser Controller enthält eine `HttpGet Index`-Methode für die Anzeige der ausgänglichen Unsubscribe-Seite, sowie eine `HttpPost Index`-Methode zum Verarbeiten der **Confirm**- und **Cancel**-Schaltflächen.

Die `HttpGet Index`-Methode verwendet GUID und Listenname in der Abfragezeichenfolge zum Abrufen der `MailingList`-Tabellenzeile für den Abonnenten. Anschließend werden alle von der Ansicht benötigten Informationen in das Anzeigemodell übergeben, und die **Unsubscribe**-Seite wird angezeigt. Die `Confirmed`-Eigenschaft wird auf null gesetzt, um der Ansicht mitzuteilen, dass die ausgängliche Version der **Unsubscribe**-Seite angezeigt werden soll.

        public async Task<ActionResult> Index(string id, string listName)
        {
            if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
            {
                ViewBag.errorMessage = "Empty subscriber ID or list name.";
                return View("Error");
            }
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            if (subscriber == null)
            {
                ViewBag.Message = "You are already unsubscribed";
                return View("Message");
            }
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = id;
            unsubscribeVM.Confirmed = null;
            return View(unsubscribeVM);
        }

Hinweis: Die SubscriberGUID ist nicht in Partitions- oder Zeilenschlüssel enthalten, daher nimmt die Leistung dieser Abfrage mit zunehmender Partitionsgröße (Anzahl der E-Mail-Adressen in einer Mailingliste) ab. Weitere Informationen zu Verbesserung der Skalierbarkeit dieser Abfrage finden Sie im [ersten Lernprogramm in dieser Serie][ersten Lernprogramm der Serie].

Die `HttpPost Index`-Methode verwendet erneut GUID und Listenname, um Abonnenteninformationen abzurufen, und füllt die Eigenschaften des Anzeigemodells aus. Falls die **Confirm**-Schaltfläche angeklickt wurde, wird anschließend die Abonnentenzeile aus der Tabelle `MailingList` gelöscht. Falls die **Confirm**-Schaltfläche angeklickt wurde, wird die `Confirm`-Eigenschaft auf `true` gesetzt, andernfalls wird die `Confirm`-Eigenschaft auf `false` gesetzt. Der Wert der `Confirm`-Eigenschaft teilt der Ansicht mit, ob die bestätigte oder abgebrochene Version der **Unsubscribe**-Seite angezeigt werden soll.

        [HttpPost] 
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Index(string subscriberGUID, string listName, string action)
        {
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = subscriberGUID;
            unsubscribeVM.Confirmed = false;
        
            if (action == "Confirm")
            {
                unsubscribeVM.Confirmed = true;
                var deleteOperation = TableOperation.Delete(subscriber);
                mailingListTable.Execute(deleteOperation);
            }
        
            return View(unsubscribeVM);
        }

### Erstellen Sie die MVC-Ansichten

1.  Erstellen Sie im Ordner *Views* im MVC-Projekt einen neuen Ordner mit dem Namen *Unsubscribe*.

2.  Fügen Sie im Ordner *Views\\Unsubscribe* die Datei *Index.cshtml* aus dem heruntergeladenen Projekt hinzu.

Die Zeile `Layout = null` in der Datei *Index.cshtml* gibt an, dass die Datei \_Layout.cshtml nicht für die Anzeige dieser Seite verwendet werden soll. Die **Unsubscribe**-Seite enthält eine einfache GUI ohne die Kopf- und Fußzeilen der Administratorseiten.

        @model MvcWebRole.Models.UnsubscribeVM
        
        @{
            ViewBag.Title = "Unsubscribe";
            Layout = null;
        }

Die `Confirmed`-Eigenschaft im Text der Seite bestimmt, was auf der Seite angezeigt wird: **Confirm**- und **Cancel**-Schaltflächen, falls die Eigenschaft null ist, Nachricht "Abbestellung bestätigt", falls die Eigenschaft wahr ist, Nachricht "Abbestellung abgebrochen", falls die Eigenschaft falsch ist.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>Unsubscribe from Mailing List</legend>
                @Html.HiddenFor(model => model.SubscriberGUID)
                @Html.HiddenFor(model => model.EmailAddress)
                @Html.HiddenFor(model => model.ListName)
                @if (Model.Confirmed == null) {
                    <p>
                        Do you want to unsubscribe  @Html.DisplayFor(model => model.EmailAddress) from:  @Html.DisplayFor(model => model.ListDescription)?
                   </p>
                    <br />
                    <p>
                        <input type="submit" value="Confirm" name="action"/> 
                        &nbsp; &nbsp;
                        <input type="submit" value="Cancel" name="action"/>
                    </p>
                }
                @if (Model.Confirmed == false) {
                    <p>
                        @Html.DisplayFor(model => model.EmailAddress)  will NOT be unsubscribed from: @Html.DisplayFor(model => model.ListDescription).
                    </p>
                }
                @if (Model.Confirmed == true) {
                    <p>
                        @Html.DisplayFor(model => model.EmailAddress)  has been unsubscribed from:  @Html.DisplayFor(model => model.ListDescription).
                    </p>
                }
            </fieldset>
        }

### Testen der Anwendung

1.  Drücken Sie STRG+F5, um das Projekt auszuführen, und klicken Sie auf **Subscribers**.

2.  Klicken Sie auf **Create** und erstellen Sie einen neuen Abonnenten für eine Ihrer zuvor erstellten Mailinglisten.

    Lassen Sie das Browserfenster in der **Indexseite** für **Abonnenten** geöffnet.

3.  Öffnen Sie den **Server-Explorer** und wählen Sie den Knoten **Azure / Speicher / (Entwicklung)** aus.

4.  Erweitern Sie **Tabellen**, klicken Sie mit der rechten Maustaste auf die Tabelle **MailingList** und klicken Sie auf **Tabelle anzeigen**.

5.  Doppelklicken Sie auf die neu hinzugefügte Abonnentenzeile.

6.  Wählen Sie im Dialogfeld **Entität bearbeiten** den `SubscriberGUID`-Wert aus, und kopieren Sie den Wert.

    ![Azure-Speicher-Explorer][Azure-Speicher-Explorer]

7.  Wechseln Sie zurück zu Ihrem Browserfenster. Ändern Sie in der Adressleiste des Browser "Subscriber" in der URL zu "unsubscribe?ID=[GUID]&listName=[Listenname]", wobei [GUID] die aus dem Azure-Speicher-Explorer kopierte GUID ist und [Listenname] der Name der Mailingliste ist. Beispiel:

        http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

    Daraufhin wird die Version der **Unsubscribe** angezeigt, die zur Bestätigung auffordert:

    ![Abbestellen-Seite][Abbestellen-Seite]

8.  Wenn Sie auf **Confirm** klicken, wird eine Bestätigung für die Abbestellung der E-Mail-Adresse angezeigt.

    ![Abbestellung bestätigt-Seite][Abbestellung bestätigt-Seite]

9.  Wechseln Sie zurück zur **Indexseite** für **Abonnenten** und vergewissern Sie sich, dass die Abonnentenzeile nicht mehr vorhanden ist.

## <a name="nextsteps"></a>Nächste Schritte

Wie bereits im [ersten Lernprogramm in dieser Reihe][ersten Lernprogramm der Serie] erklärt, behandelt dieses Lernprogramm nicht die Komponenten des Abonnierungsprozesses, da die ASP.NET-Web-API-Dienstmethode keine Sicherheit mit gemeinsamem geheimem Schlüssel implementiert. Sie können die Dienstmethode jedoch auch mit den IP-Einschränkungen schützen, die im zweiten Lernprogramm behandelt werden. Anschließend können Sie Abonnementfunktion mit den folgenden Dateien aus dem heruntergeladenen Projekt hinzufügen.

Für die ASP.NET Web-API-Dienstmethode:

-   Controllers\\SubscribeAPI.cs

Für die Webseite, die Abonnenten angezeigt wird, nachdem Sie auf den **Bestätigungs**-Link in der von der Dienstmethode generierten E-Mail klicken:

-   Models\\SubscribeVM.cs
-   Controllers\\SubscribeController.cs
-   Views\\Subscribe\\Index.cshtml

Im [nächsten Lernprogramm][nächsten Lernprogramm] werden Sie die Workerrolle A konfigurieren und programmieren, die für die Planung von E-Mails verantwortlich ist.

Links zu weiteren Ressourcen für die Arbeit mit Azure-Speichertabellen, Warteschlangen und Blobs finden Sie im [letzten Lernprogramms in dieser Serie][letzten Lernprogramms in dieser Serie].

<div><a href="/de-de/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/" class="site-arrowboxcta download-cta">Lernprogramm 4</a></div>
</div>

  [ersten Lernprogramm der Serie]: /de-de/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Erstellen der Visual Studio-Lösung]: #cloudproject
  [Aktualisieren des NuGet-Pakets für die Speicherclientbibliothek]: #updatescl
  [Konfigurieren des Projekts zur Verwendung des Speicheremulators]: #configurestorage
  [Konfigurieren der Ablaufverfolgung und Behandlung von Neustarts]: #tracing
  [Hinzufügen von Code zum Erstellen von Tabellen, Warteschlange und Blob-Container zur Methode Application\_Start]: #createifnotexists
  [Erstellen und Testen der Mailingliste]: #mailinglist
  [Erstellen und Testen der Abonnenten-Controller und -Ansichten]: #subscriber
  [Erstellen und Testen der Nachrichten-Controller und -Ansichten]: #message
  [Erstellen und Testen der Abbestellen-Controller und -Ansichten]: #unsubscribe
  [Nächste Schritte]: #nextsteps
  [Menü Neues Projekt]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png
  [Dialogfeld "Neues Projekt"]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png
  [Dialogfeld Neues Azure-Cloud-Projekt]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png
  [Dialogfeld Neues Azure-Cloud-Projekt - Umbenennen der Webrolle]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png
  [Dialogfeld Neues Azure-Cloud-Projekt - Hinzufügen einer Workerrolle]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png
  [Keine Authentifizierung]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/noauth.png
  [komplette Lösung]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [Startseite]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png
  [Serveremulator in der Systemleiste]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png
  [Manage NuGet Packages for Solution im Menü]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png
  [Azure Storage-Paket im Dialogfeld Manage NuGet Packages]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png
  [Auswählen beider Projekte im Dialogfeld Select Projects]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png
  [Webrollen-Eigenschaften]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png
  [Rechtsklick Eigenschaften]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png
  [Fehler: ungültige Dienstkonfiguration und Definitionsdateien]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png
  [zweiten Lernprogramm]: /de-de/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Role Instance Restarts Due to OS Upgrades]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [bedarfsgesteuerte Übertragung]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433075.aspx
  [dbgview]: http://technet.microsoft.com/de-de/sysinternals/bb896647.aspx
  [TableEntity]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx
  [DynamicTableEntity]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx
  [Azure Storage Client Library 2.0 Tables Deep Dive]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [Grundlegendes zum Tabellendienst-Datenmodell]: http://msdn.microsoft.com/de-de/library/windowsazure/dd179338.aspx
  [% Character in PartitionKey or RowKey]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx
  [Verwenden der asynchronen Funktionen in .NET 4.5 zum Vermeiden von blockierenden Aufrufen]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async
  [TableOperation.Retrieve]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx
  [How to get most out of Azure Tables]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [Azure Tables: Expect Continuation Tokens, Seriously]: http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously
  [Azure-Speicher-Protokollierung: Verfolgen von Speicheranfragen mithilfe von Protokollen]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Leere Indexseite der Mailingliste]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png
  [Indexseite der Mailingliste mit Einträgen]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png
  [Leere Indexseite für Abonnenten]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png
  [Indexseite für Abonnenten mit Einträgen]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png
  [Leere Indexseite für Nachrichten]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png
  [Azure-Speicher-Explorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png
  [Abbestellen-Seite]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png
  [Abbestellung bestätigt-Seite]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png
  [nächsten Lernprogramm]: /de-de/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [letzten Lernprogramms in dieser Serie]: /de-de/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
