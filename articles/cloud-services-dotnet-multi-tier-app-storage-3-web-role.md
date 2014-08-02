<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" urlDisplayName="Step 3: Web Role" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 3: Web role" metaKeywords="Azure tutorial, Email Service application, ASP.NET MVC web role, MVC controllers, Web API controller, Cloud Service project" description="The third tutorial in a series that teaches how to create and deploy the Email Service app in an Azure cloud service." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building the web role for the Azure Email Service application - 3 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Erstellen der Webrolle für die Azure-E-Mail-Dienstanwendung - 3 von 5.
======================================================================

Dies ist das dritte von fünf Lernprogrammen, in denen die Erstellung und Bereitstellung der Azure-E-Mail-Dienstanwendung beschrieben wird. Weitere Informationen zur Anwendung und den Lernprogrammen finden Sie im [ersten Lernprogramm der Serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

In diesem Lernprogramm lernen Sie Folgendes:

-   Erstellen einer Lösung bestehend aus einem Cloud-Dienst-Projekt mit einer Webrolle und einer Workerrolle.
-   Arbeiten mit Azure-Tabellen, Blobs und Warteschlangen in MVC 4-Controllern und Ansichten.
-   Umgang mit Parallelitätskonflikten bei der Arbeit mit Azure-Tabellen.
-   Konfigurieren einer Webrolle oder eines Webprojekts mit Ihrem Azure-Speicherkonto.

Erstellen der LösungErstellen der Visual Studio-Lösung
------------------------------------------------------

Zunächst erstellen Sie eine Visual Studio-Lösung mit einem Projekt für das Web-Front-End und einem Projekt für eine der Back-End-Azure-Workerrollen. Die zweite Workerrolle wird später hinzugefügt.

(Informationen zu den notwendigen Änderungen für die Ausführung der Web-GUI in einer Azure-Website anstelle eines Azure-Cloud-Dienstes finden Sie im Abschnitt [Alternative Architektur](#alternativearchitecture) gegen Ende dieses Lernprogramms.)

### Erstellen eines Cloud-Dienst-Projekts mit einer Webrolle und einer Workerrolle

1.  Starten Sie Visual Studio mit erhöhten Rechten.

    > [WACOM.NOTE] In Visual Studio 2013 benötigen Sie keine erhöhten Rechte, da neue Projekte standardmäßig den Serveremulator verwenden.

2.  Klicken Sie im Menü **Datei** auf **Neues Projekt**.

    ![Menü Neues Projekt](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png)

3.  Erweitern Sie den Knoten **C\#**, wählen Sie **Cloud** unter **Installierte Vorlagen** aus und klicken Sie auf **Azure-Cloud-Dienst**.

4.  Benennen Sie die Anwendung **AzureEmailService** und klicken Sie auf **OK**.

    ![Dialogfeld "Neues Projekt"](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png)

5.  Wählen Sie im Dialogfeld **Neuer Azure-Cloud-Dienst** die Option **ASP.NET-Webrolle** aus und klicken Sie auf den Pfeil nach rechts.

    > [WACOM.NOTE] Der heruntergeladene Code für dieses Lernprogramm verwendet MVC 4. Sie können jedoch in Visual Studio 2013 anhand der für Visual Studio 2012 geschriebenen Anweisungen keine MVC 4-Webrolle erstellen. Für Visual Studio 2013 gilt Folgendes: (1) Überspringen Sie die Schritte zum Erstellen der Webrolle und führen Sie den Schritt für die Workerrolle aus. (2) Klicken Sie nach der Erstellung der Workerrolle mit der rechten Maustaste in den **Projektmappen-Explorer** und klicken Sie auf **Hinzufügen** -- **Neues Projekt**. Erweitern Sie im linken Bereich des Dialogfelds **Neues Projekt hinzufügen** den Knoten **Web** und klicken Sie auf **Visual Studio 2012**. (3) Wählen Sie **ASP.NET MVC 4-Webanwendung** aus, nennen Sie das Projekt **MvcWebRole** und klicken Sie auf **OK**. (4) Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Vorlage **Internetanwendung** aus. (5) Klicken Sie auf **Rollen** unter **AzureEmailService** im **Projektmappen-Explorer** und klicken Sie auf **Hinzufügen** - **Webrollenprojekt in Projektmappe**. (6) Wählen Sie im Feld **Dem Rollenprojekt zuordnen** das Projekt **MvcWebRole** aus und klicken Sie auf **OK**.

    ![Dialogfeld Neues Azure-Cloud-Projekt](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png)

6.  Zeigen Sie in der rechten Spalte mit der Maus auf **MvcWebRole1** und klicken Sie auf das Stiftsymbol, um den Namen der Webrolle zu ändern.

7.  Geben Sie MvcWebRole als neuen Namen ein und drücken Sie die Eingabetaste.

    ![Dialogfeld Neues Azure-Cloud-Projekt - Umbenennen der Webrolle](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png)

8.  Wiederholen Sie die Prozedur, um eine **Workerrolle** mit dem Namen WorkerRoleA hinzuzufügen und klicken Sie auf **OK**.

    ![Dialogfeld Neues Azure-Cloud-Projekt - Hinzufügen einer Workerrolle](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png)

9.  Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Vorlage **Internetanwendung** aus.

10. Stellen Sie sicher, dass in der Dropdownliste **Ansichtsmodul** die Option **Razor** ausgewählt ist und klicken Sie auf **OK**.

    ![Dialogfeld "Neues Projekt"](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png)

### Erstellen von Seitenkopf, Menü und Fußzeile

In diesem Abschnitt werden Sie den Seitenkopf, die Fußzeile und die Menüelemente aktualisieren, die auf allen Seiten der Administrator-Web-GUI angezeigt werden. Die Anwendung enthält drei Sätze von Administrator-Webseiten: eine für Mailinglisten, eine für Abonnenten von Mailinglisten und eine für Nachrichten.

1.  Erweitern Sie im **Projektmappen-Explorer** den Ordnet Views\\Shared und öffnen Sie die Datei \_Layout.cshtml.

    ![\_Layout.cshtml im Projektmappen-Explorer](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-opening-layout-cshtml.png)

2.  Ändern Sie im **&lt;title\>**-Element den Text "My ASP.NET MVC Application" zu "Azure-E-Mail-Dienst".

3.  Ändern Sie im **&lt;p\>**-Element mit der Klasse "site-title" den Text "your logo here" zu "Azure-E-Mail-Dienst" und ändern Sie "Home" zu "MailingList".

    ![Titel und Seitenkopf in \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-title-and-logo-in-layout.png)

4.  Löschen des Menübereichs:

    ![Menü in \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-menu-in-layout.png)

5.  Fügen Sie anstelle des gelöschten Menübereichs den folgenden Code ein:

         <ul id="menu">
             <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
             <li>@Html.ActionLink("Messages", "Index", "Message")</li>
             <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
         </ul>

6.  Ändern Sie im **&lt;footer\>**-Element den Text "My ASP.NET MVC Application" zu "Azure-E-Mail-Dienst".

    ![Fußzeile in \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-footer-in-layout.png)

### Lokales Ausführen der Anwendung

1.  Drücken Sie STRG+F5, um die Anwendung auszuführen.

    Die Startseite der Anwendung wird im Standardbrowser angezeigt.

    ![Startseite](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png)

    Die Anwendung wird im Azure-Serveremulator ausgeführt. In der Windows-Systemleiste wird das Serveremulatorsymbol angezeigt:

    ![Serveremulator in der Systemleiste](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png)

Konfigurieren von AblaufverfolgungKonfigurieren von Ablaufverfolgung
--------------------------------------------------------------------

Um die Speicherung von Ablaufverfolgungsdaten zu aktivieren, öffnen Sie die Datei *WebRole.cs* und fügen Sie die folgende `ConfigureDiagnostics`-Methode hinzu. Fügen Sie den Code, der die neue Methode aufruft, zur `OnStart`-Methode hinzu.

> [WACOM.NOTE] In Visual Studio 2013 können Sie anstelle der folgenden Schritte zum manuellen Ändern von Code in *WebRole.cs* mit der rechten Maustaste auf das Projekt MvcWebRole und anschließend auf **Vorhandenes Element hinzufügen** klicken und die Datei *WebRole.cs* aus dem heruntergeladenen Projekt hinzufügen.

    private void ConfigureDiagnostics()
    {
        DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
        config.Logs.BufferQuotaInMB = 500;
        config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
        config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

        DiagnosticMonitor.Start(
            "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
            config);
    }

    public override bool OnStart()
    {
        ConfigureDiagnostics();
        return base.OnStart();
    }

Die Methode `ConfigureDiagnostics` wird im [zweiten Lernprogramm](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/) genauer erklärt.

NeustartsFügen Sie Code für den effizienten Umgang mit Neustarts hinzu.
-----------------------------------------------------------------------

Azure-Cloud-Dienstanwendungen werden ca. zwei mal pro Monat für Betriebssystemupdates neu gestartet. (Weitere Informationen zu OS-Updates finden Sie unter [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (Neustarts von Rolleninstanzen für OS-Upgrades, in englischer Sprache).) Vor dem Herunterfahren einer Webanwendung wird ein `OnStop`-Ereignis ausgelöst. Die von Visual Studio erstellte Webrollen-Codevorlage überschreibt die `OnStop`-Methode nicht. Daher hat die Anwendung nur wenige Sekunden, um die Verarbeitung von HTTP-Anforderungen abzuschließen, bevor sie heruntergefahren wird. Sie können die `OnStop`-Methode überschreiben, um sicherzustellen, dass die Anwendung beim Herunterfahren ordnungsgemäß beendet wird.

Für das Verhalten beim Herunterfahren und Neustarten öffnen Sie die Datei *WebRole.cs* und überschreiben Sie die Methode `OnStop` mit dem folgenden Code.

      public override void OnStop()
      {
          Trace.TraceInformation("OnStop aus Webrolle aufgerufen");
          var rcCounter = new PerformanceCounter("ASP.NET", "Requests Current", "");
          while (rcCounter.NextValue() > 0)
          {
              Trace.TraceInformation("ASP.NET Anforderungen aktuell = " + rcCounter.NextValue().ToString());
              System.Threading.Thread.Sleep(1000);
          }           
      }

Für diesen Code ist eine zusätzliche `using`-Anweisung erforderlich:

      using System.Diagnostics;

Die `OnStop`-Methode hat bis zu 5 Minuten Zeit, bevor die Anwendung heruntergefahren wird. Sie könnten in Ihrer `OnStop`-Methode einen 5-minütigen Sleep-Aufruf einfügen, um Ihrer Anwendung die maximale Zeit für die Verarbeitung der aktuellen Anforderungen zu geben. Wenn Ihre Anwendung jedoch korrekt skaliert ist, sollte sie die verbleibenden Anforderungen in viel kürzerer Zeit bearbeiten können. Die Anwendung sollte schnellstmöglich angehalten werden, um möglichst zeitnah neu starten zu können und mit der Verarbeitung von Anforderungen fortzufahren.

Sobald Azure eine Rolle vom Netz nimmt, hört das Lastenausgleichsmodul auf, Anforderungen an die Rolleninstanz zu schicken, und erst dann wird die `OnStop`-Methode aufgerufen. Falls dies die einzige Instanz Ihrer Rolle ist, werden keine Anforderungen bearbeitet, bis Ihre Rolle heruntergefahren und neu gestartet wurde (dieser Vorgang dauert normalerweise mehrere Minuten). Aus diesem Grund müssen Sie für die Azure-Servicelevel-Vereinbarung mindestens zwei Instanzen von jeder Rolle haben, um die garantierte Betriebszeit nutzen zu können.

Der gezeigte Code für die `OnStop`-Methode erstellt einen ASP.NET-Leistungsindikator für `Requests Current` (aktuelle Anforderungen). Der Wert des `Requests Current`-Zählers enthält die aktuelle Anzahl der Anforderungen, inklusive solcher, die sich momentan in der Warteschlange befinden, ausgeführt werden oder darauf warten, zum Client geschrieben zu werden. Der Wert von `Requests Current` wird einmal pro Sekunde geprüft, und sobald der Wert null erreicht, wird die `OnStop`-Methode verlassen. Nach Verlassen der `OnStop`-Methode wird die Rolle heruntergefahren.

Ablaufverfolgungsdaten aus der `OnStop`-Methode werden nur gespeichert, wenn eine [bedarfsgesteuerte Übertragung](http://msdn.microsoft.com/en-us/library/windowsazure/gg433075.aspx) durchgeführt wird. Sie können die `OnStop`-Ablaufverfolgungsinformationen in Echtzeit mithilfe des [dbgview](http://technet.microsoft.com/en-us/sysinternals/bb896647.aspx)-Hilfsprogramms über eine Remotedesktopverbindung anzeigen.

Aktualisieren der SpeicherclientbibliothekAktualisieren des NuGet-Pakets für die Speicherclientbibliothek
---------------------------------------------------------------------------------------------------------

> [WACOM.NOTE] Dieser Schritt kann unter Umständen ausgelassen werden. Falls das NuGet-Paket für den Azure-Speicher nicht in der Updateliste angezeigt wird, ist die installierte Version bereits aktualisiert.

Das API-Framework für die Arbeit mit Azure-Speichertabellen, Warteschlangen und Blobs ist die sogenannte Speicherclientbibliothek (Storage Client Library SCL). Diese API ist in einem NuGet-Paket in der Cloud-Dienst-Projektvorlage enthalten. Zum Zeitpunkt der Erstellung dieses Lernprogramms enthalten die Projektvorlagen allerdings die Version 1.7 der SCL und nicht die aktuelle Version 2.0. Aus diesem Grund müssen Sie das NuGet-Paket aktualisieren, bevor Sie Ihren Code schreiben.

1.  Zeigen Sie im Menü **Tools** in Visual Studio mit der Maus auf **Library Package Manager** und klicken Sie auf **Manage NuGet Packages for Solution**.

    ![Manage NuGet Packages for Solution im Menü](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png)

2.  Wählen Sie im linken Bereich des Dialogfelds **Manage NuGet Packages** die Option **Updates** aus, blättern Sie nach unten bis zum Paket **Azure Storage** und klicken Sie auf **Update**.

    ![Azure Storage-Paket im Dialogfeld Manage NuGet Packages](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png)

3.  Stellen Sie sicher, dass im Dialogfeld **Select Projects** beide Projekte ausgewählt sind und klicken Sie auf **OK**.

    ![Auswählen beider Projekte im Dialogfeld Select Projects](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png)

4.  Stimmen Sie den Lizenzbedingungen zu, um die Installation des Pakets abzuschließen, und schließen Sie anschließend das Dialogfeld **Manage NuGet Packages**.

5.  Falls das Projekt WorkerRoleA in der Datei *WorkerRole.cs* die folgende `using`-Anweisung enthält, können Sie diese löschen, da sie nicht mehr benötigt wird:

         using Microsoft.WindowsAzure.StorageClient;

Die Version 1.7 der SCL enthält einen LINQ-Anbieter, der die Programmierung von Tabellenabfragen erleichtert. Zum Zeitpunkt der Erstellung dieses Lernprogramms enthält die Version 2.0 der Tabellendienstebene (Table Service Layer TSL) noch keinen LINQ-Anbieter. Falls Sie LINQ verwenden möchten, steht Ihnen der LINQ-Anbieter aus SCL 1.7 im Namespace [Microsoft.WindowsAzure.Storage.Table.DataServices](http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.storage.table.dataservices.aspx) zur Verfügung. TSL 2.0 wurde zur Verbesserung der Leistung entwickelt, und der 1.7-LINQ-Anbieter zieht keinen Nutzen aus diesen Verbesserungen. Die Beispielanwendung verwendet TSL 2.0 und nutzt daher kein LINQ für Abfragen. Weitere Informationen zu SCL und TSL 2.0 finden Sie in den Ressourcen am Ende des [letzten Lernprogramms in dieser Serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

> [WACOM.NOTE] In SCL 2.1 wurde die LINQ-Unterstützung wieder eingeführt. Dieses Lernprogramm verwendet allerdings kein LINQ für Speichertabellenabfragen. Die aktuelle SCL-Version unterstützt asynchrone Programmierung. Dieses Lernprogramm enthält allerdings keinen asynchronen Code. Weitere Informationen zu asynchroner Programmierung und ein Codebeispiel für die Verwendung der Azure-SCL finden Sie im folgenden E-Book-Kapitel und dem zugehörigen Downloadprojekt: [Use .NET 4.5’s async support to avoid blocking calls](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async) (Verwenden der asynchronen Funktionen in .NET 4.5 zum Vermeiden von blockierenden Aufrufen, in englischer Sprache).

Hinzufügen eines SCL 1.7-VerweisesHinzufügen eines Verweises auf eine SCL 1.7-Assembly
--------------------------------------------------------------------------------------

> [WACOM.NOTE] Falls Sie SCL 2.1 oder eine neuere Version verwenden (Visual Studio 2013 mit dem neuesten SDK), können Sie diesen Schritt überspringen.

Version 2.0 der Speicherclientbibliothek enthält nicht alle benötigten Elemente für die Diagnose, daher müssen Sie einen Verweis zu einer 1.7-Assembly hinzufügen.

1.  Klicken Sie mit der rechten Maustaste auf das Projekt MvcWebRole und wählen Sie **Verweis hinzufügen**.

2.  Klicken Sie unten im Dialogfeld auf die Schaltfläche **Durchsuchen...**.

3.  Navigieren Sie zum folgenden Ordner:

         C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK012-10\ref

4.  Wählen Sie *Microsoft.WindowsAzure.StorageClient.dll* aus, und klicken Sie auf **Hinzufügen**.

5.  Klicken Sie im Dialogfeld **Verweis-Manager** auf **OK**.

6.  Wiederholen Sie den Vorgang für das Prozess WorkerRoleA.

App\_Start-CodeHinzufügen von Code zum Erstellen von Tabellen, Warteschlange und Blob-Container zur Methode Application\_Start
------------------------------------------------------------------------------------------------------------------------------

Die Webanwendung verwendet die Tabellen `MailingList` und `Message`, die Warteschlange `azuremailsubscribequeue` und den Blob-Container `azuremailblobcontainer`. Sie könnten diese Elemente manuell mithilfe eines Tools wie dem Azure-Speicher-Explorer erstellen. Allerdings müssten Sie diesen Vorgang jedes mal neu ausführen, wenn Sie die Anwendung mit einem neuen Speicherkonto verwenden. Der Code im folgenden Abschnitt wird beim Anwendungsstart ausgeführt, prüft ob die benötigten Tabellen, Warteschlangen und Blob-Container existieren und erstellt sie gegebenenfalls.

Sie können diesen Code für den Anwendungsstart entweder zur `OnStart`-Methode in der Datei *WebRole.cs* oder zur Datei *Global.asax* hinzufügen. In diesem Lernprogramm initialisieren Sie den Azure-Speicher in der Datei *Global.asax*, da dieses Verfahren sowohl für Azure-Websites als auch für Azure-Cloud-Dienst-Webrollen funktioniert.

1.  Erweitern Sie im **Projektmappen-Explorer** den Knoten *Global.asax* und öffnen Sie *Global.asax.cs*.

2.  Fügen Sie eine neue `CreateTablesQueuesBlobContainers`-Methode im Anschluss an die`Application_Start`-Methode hinzu und rufen Sie die neue Methode aus der `Application_Start`-Methode auf, wie im folgenden Beispiel gezeigt:

         protected void Application_Start()
         {
             AreaRegistration.RegisterAllAreas();
             WebApiConfig.Register(GlobalConfiguration.Configuration);
             FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
             RouteConfig.RegisterRoutes(RouteTable.Routes);
             BundleConfig.RegisterBundles(BundleTable.Bundles);
             AuthConfig.RegisterAuth();
             // Prüfen, ob alle benötigten Tabellen, Warteschlangen und
             // Blob-Container existieren, und ggf. erstellen.
             CreateTablesQueuesBlobContainers();
         }

         private static void CreateTablesQueuesBlobContainers()
         {
             var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
             // Für die Ausführung in einer Azure-Website (anstelle eines Cloud-Dienstes) verwenden Sie die Datei Web.config:
             //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
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

3.  Klicken Sie mit der rechten Maustaste auf die blau unterstrichene Zeile unterhalb von `RoleEnvironment`, klicken Sie auf **Auflösen** und wählen Sie **using Microsoft.WindowsAzure.ServiceRuntime** aus.

    ![Rechtsklick](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-4.png)

4.  Klicken Sie mit der rechten Maustaste auf die blau unterstrichene Zeile unterhalb von `CloudStorageAccount`, klicken Sie auf **Auflösen** und wählen Sie **using Microsoft.WindowsAzure.Storage** aus.

5.  Alternativ können Sie die folgenden Anweisungen manuell hinzufügen:

         using Microsoft.WindowsAzure.ServiceRuntime;
         using Microsoft.WindowsAzure.Storage;

6.  Erstellen Sie die Anwendung, um die Datei zu speichern und sicherzustellen, dass keine Kompilierungsfehler vorliegen.

In den folgenden Abschnitten erstellen Sie die Komponenten der Webanwendung. Sie können diese entweder im Entwicklungsspeicher oder in Ihrem Speicherkonto testen, ohne zuvor Tabellen, Warteschlangen oder Blob-Container manuell erstellen zu müssen.

MailinglisteErstellen und Testen der Mailinglisten-Controller und -Ansichten
----------------------------------------------------------------------------

Administratoren können in der **Mailinglisten**-Web-GUI ihre Mailinglisten erstellen, bearbeiten und anzeigen, wie z. B. "Ankündigungen der Fakultät für Geschichte an der Contoso University" und "Technische Stellenausschreibungen von Fabrikam".

### Fügen Sie die MailingList-Entitätsklasse zum Ordner Models hinzu

Die `MailingList`-Entitätsklasse bildet die Zeilen in der Tabelle `MailingList` ab, die Informationen über die Liste enthalten, wie z. B. deren Beschreibung und die Absenderadresse für E-Mails, die an die Liste verschickt werden.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner `Models` im MVC-Projekt, und klicken Sie anschließend auf **Vorhandenes Element hinzufügen**.

    ![Bestehendes Element zum Ordner Models hinzufügen](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-models.png)

2.  Navigieren Sie zum Ordner, in dem Sie die heruntergeladene Beispielanwendung gespeichert haben, wählen Sie die Datei *MailingList.cs* im Ordner `Models` aus und klicken Sie auf **Hinzufügen**.

3.  Öffnen Sie die Datei *MailingList.cs* und betrachten Sie den Code.

         public class MailingList : TableEntity
         {
             public MailingList()
             {
                 this.RowKey = "mailinglist";
             }

             [Required]
             [RegularExpression(@"[\w]+",
              ErrorMessage = @"Nur alphanumerische Zeichen und Unterstrich (_) sind erlaubt.")]
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

    Die TSL 2.0 API für Azure-Speicher verlangt, dass die Entitätsklassen, die Sie für Ihre Tabellenoperationen verwenden, von [TableEntity](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx) abgeleitet werden. Diese Klasse definiert die Felder `PartitionKey`, `RowKey`, `TimeStamp` und `ETag`. Die Eigenschaften `TimeStamp` und `ETag` werden vom System verwendet. Später in diesem Lernprogramm werden Sie sehen, wie die `ETag`-Eigenschaft für die Parallelitätsbehandlung verwendet wird.

    (Mit der Klasse [DynamicTableEntity](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) können Sie mit Tabellenzeilen als Wörterbuchauflistungen von Schlüssel-Wert-Paaren arbeiten, anstatt vordefinierte Modellklassen zu verwenden. Weitere Informationen finden Sie unter [Azure Storage Client Library 2.0 Tables Deep Dive](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx) (Arbeiten mit Tabellen der Azure-Speicherclientbibliothek 2.0, in englischer Sprache).

    Der Partitionsschlüssel der Tabelle `mailinglist` ist der Listenname. Der Zugriff auf den Partitionsschlüssel in dieser Entitätsklasse erfolgt entweder über die `PartitionKey`-Eigenschaft (definiert in der Klasse `TableEntity`) oder über die `ListName`-Eigenschaft (definiert in der Klasse `MailingList`). Die `ListName`-Eigenschaft verwendet `PartitionKey` als Unterstützungsvariable. Dank der `ListName`-Eigenschaft können Sie einen anschaulicheren Variablennamen im Code verwenden und erleichtern sich die Programmierung der Web-GUI, da die DataAnnotations-Attribute für Formatierung und Prüfung zur `ListName`-Eigenschaft hinzugefügt werden können, jedoch nicht direkt zur `PartitionKey`-Eigenschaft.

    Das `RegularExpression`-Attribut in der `ListName`-Eigenschaft weist MVC an, die Benutzereingaben zu prüfen, um sicherzustellen, dass der eingegebene Listenname nur alphanumerische Zeichen oder Unterstriche enthält. Mit dieser Einschränkung werden einfache Listennamen erzwungen, die problemlos in Abfragezeichenfolgen in URLs verwendet werden können.

    **Hinweis:** Falls Sie ein weniger restriktives Format für die Listennamen verwenden möchten, können Sie weitere Zeichen zulassen und die Listennamen vor der Verwendung in Abfragezeichenfolgen per URL-Codierung umwandeln. Bestimmte Zeichen sind jedoch in Partitions- und Zeilenschlüsseln von Azure-Tabellen nicht erlaubt, und Sie müssen zumindest diese Zeichen ausschließen. Weitere Informationen zu problematischen Zeichen in Partitions- und Zeilenschlüsseln finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell](http://msdn.microsoft.com/en-us/library/windowsazure/dd179338.aspx) und [% Character in PartitionKey or RowKey](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx) (%-Zeichen in Partitions- und Zeilenschlüsseln, in englischer Sprache).

    Die Klasse `MailingList` definiert einen Standardkonstruktor, der für `RowKey` den fest einprogrammierten Wert "mailinglist", da alle Mailinglisten-Zeilen in der Tabelle diesen Wert als Zeilenschlüssel verwenden. (Eine Erklärung der Tabellenstruktur finden Sie im [ersten Lernprogramm in dieser Serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).) An dieser Stelle könnte jeder beliebige konstante Wert stehen, vorausgesetzt dieser Wert wird nie als E-Mail-Adresse verwendet (der Zeilenschlüssel für Abonnentenzeilen in dieser Tabelle).

    Listenname und Absender-E-Mail-Adresse müssen bei der Erstellung neuer `MailingList`-Entitäten immer angegeben werden. Daher haben diese Werte ein `Required`-Attribut.

    Die `Display`-Attribute geben die Standardbeschriftung der jeweiligen Felder in der MVC-GUI an.

### Fügen Sie den MailingList MVC-Controller hinzu

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner "Controllers" im MVC-Projekt, und klicken Sie anschließend auf **Vorhandenes Element hinzufügen**.

    ![Bestehendes Element zum Ordner Controllers hinzufügen](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-controllers.png)

2.  Navigieren Sie zum Ordner, in dem Sie die heruntergeladene Beispielanwendung gespeichert haben, wählen Sie die Datei *MailingListController.cs* im Ordner `Controllers` aus und klicken Sie auf **Hinzufügen**.

3.  Öffnen Sie die Datei *MailingListController.cs* und betrachten Sie den Code.

    Der Standardkonstruktor erstellt ein `CloudTable`-Objekt für die Arbeit mit der Tabelle `mailinglist`.

         public class MailingListController : Controller
         {
             private CloudTable mailingListTable;

             public MailingListController()
             {
                 var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
                 //  Für die Ausführung in einer Azure-Website (anstelle eines Cloud-Dienstes) verwenden Sie die Datei Web.config:
                 //    var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

                 var tableClient = storageAccount.CreateCloudTableClient();
                 mailingListTable = tableClient.GetTableReference("mailinglist");
             }

    Der Code liest die Anmeldeinformationen für Ihr Azure-Speicherkonto aus der Einstellungsdatei des Cloud-Dienst-Projekts, um sich mit Ihrem Speicherkonto zu verbinden. (Sie werden diese Einstellungen im weiteren Verlauf des Lernprogramms konfigurieren, bevor Sie den Controller testen.) Falls Sie das MVC-Projekt in einer Azure-Website ausführen, können Sie die Verbindungszeichenfolge stattdessen aus der Datei Web.config auslesen.

    Anschließend folgt die Methode `FindRow`. Diese Methode wird immer dann aufgerufen, wenn der Controller einen bestimmten Mailinglisteneintrag in der Tabelle `MailingList` nachschlagen muss, z. B. beim Bearbeiten eines Mailinglisteneintrags. Der Code ruft eine einzige `MailingList`-Entität ab, indem er die übergebenen Werte für Partitions- und Zeilenschlüssel verwendet. Dieser Controller bearbeitet nur Zeilen, die "MailingList" als Zeilenschlüssel haben. Sie könnten daher "MailingList" als Zeilenschlüssel fest einprogrammieren, aber die Übergabe von Partitions- und Zeilenschlüssel ist ein Muster für die `FindRow`-Methoden in allen Controllern.

         private MailingList FindRow(string partitionKey, string rowKey)
         {
             var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var mailingList = retrievedResult.Result as MailingList;
             if (mailingList == null)
             {
                 throw new Exception("Keine Mailingliste gefunden für: " + partitionKey);
             }

             return mailingList;
         }

    Vergleichen Sie die `FindRow`-Methode im `MailingList`-Controller, die eine Mailinglistzeile zurückgibt, mit der `FindRow`-Methode im `Subscriber`-Controller, die eine Abonnentenzeile aus derselben `mailinglist`-Tabelle zurückgibt.

         private Subscriber FindRow(string partitionKey, string rowKey)
         {
             var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var subscriber = retrievedResult.Result as Subscriber;
             if (subscriber == null)
             {
                 throw new Exception("Kein Abonnent gefunden für: " + partitionKey + ", " + rowKey);
             }
             return subscriber;
         }

    Der einzige Unterschied zwischen den beiden Abfragen ist der Modelltyp, der an die [TableOperation.Retrieve](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx)-Methode übergeben wird. Der Modelltyp gibt das Schema (die Eigenschaften) der Zeile(n) an, die Sie als Ergebnis der Abfrage erwarten. Die Zeilen in einer einzigen Tabelle können unterschiedliche Schemata haben. Normalerweise verwenden Sie beim Auslesen einer Zeile denselben Modelltyp wie bei deren Erstellung.

    Auf der **Index**-Seite werden alle Mailinglistenzeilen angezeigt. Daher gibt die Abfrage in der `Index`-Methode alle `MailingList`-Entitäten zurück, die den Zeilenschlüssel "mailinglist" haben (die restlichen Zeilen in der Tabelle haben E-Mail-Adressen als Zeilenschlüssel und enthalten Informationen zu Abonnenten).

                 var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
                 lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();

    Die `Index`-Methode umschließt diese Abfrage mit Code für die Behandlung von Zeitüberschreitungen.

         public ActionResult Index()
         {
             TableRequestOptions reqOptions = new TableRequestOptions()
             {
                 MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                 RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
             };
             List<MailingList> lists;
             try
             {
                 var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
                 lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();
             }
             catch (StorageException se)
             {
                 ViewBag.errorMessage = "Zeitüberschreitung, bitte wiederholen. ";
                 Trace.TraceError(se.Message);
                 return View("Error");
             }

             return View(lists);
         }

    Wenn Sie keine Zeitüberschreitungsparameter angeben, wiederholt die API den Vorgang drei mal mit exponentiell ansteigendem Zeitlimit. Für Weboberflächen, in denen ein Benutzer auf die Anzeige der nächsten Seite warten, kann dies zu inakzeptablen Wartezeiten führen. Dieser Code verwendet daher lineare Wiederholungsversuche (mit jeweils dem gleichen Zeitlimit) und einen Wert für die Zeitüberschreitung, der für Benutzer akzeptabel ist.

    Wenn der Benutzer auf die Schaltfläche **Create** auf der Seite **Create** klickt, erstellt die MVC-Modellbindung eine `MailingList`-Entität mit den Eingaben aus der Ansicht, und die `HttpPost Create`-Methode fügt die Entität in die Tabelle ein.

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Create(MailingList mailingList)
         {
             if (ModelState.IsValid)
             {
                 var insertOperation = TableOperation.Insert(mailingList);
                 mailingListTable.Execute(insertOperation);
                 return RedirectToAction("Index");
             }

             return View(mailingList);
         }

Auf der **Edit**-Seite ruft die `HttpGet Edit`-Methode die richtige Zeile ab, und die `HttpPost`-Methode aktualisiert die Zeile.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit(string partitionKey, string rowKey, MailingList editedMailingList)
        {
            if (ModelState.IsValid)
            {
                var mailingList = new MailingList();
                UpdateModel(mailingList);
                try
                {
                    var replaceOperation = TableOperation.Replace(mailingList);
                    mailingListTable.Execute(replaceOperation);
                    return RedirectToAction("Index");
                }
                catch (StorageException ex)
                {
                    if (ex.RequestInformation.HttpStatusCode == 412)
                    {
                        // Parallelitätsproblem
                        var currentMailingList = FindRow(partitionKey, rowKey);
                        if (currentMailingList.FromEmailAddress != editedMailingList.FromEmailAddress)
                        {
                            ModelState.AddModelError("FromEmailAddress", "Current value: " + currentMailingList.FromEmailAddress);
                        }
                        if (currentMailingList.Description != editedMailingList.Description)
                        {
                            ModelState.AddModelError("Description", "Current value: " + currentMailingList.Description);
                        }
                        ModelState.AddModelError(string.Empty, "Dieser Eintrag wurde von einem anderen Benutzer "
                            + "geändert, nachdem Sie den Ausgangswert abgerufen haben. Der "
                            + "Bearbeitungsvorgang wurde abgebrochen und die aktuellen Werte in der Datenbank "
                            + "wurden angezeigt. Klicken Sie erneut auf die Schaltfläche "
                            + "Save, um diesen Eintrag dennoch zu bearbeiten. Klicken Sie andernfalls auf den Link Back to List.");
                         ModelState.SetModelValue("ETag", new ValueProviderResult(currentMailingList.ETag, currentMailingList.ETag, null));
                    }
                    else
                    {
                        throw; 
                    }
                }
            }
            return View(editedMailingList);
        }

   Der try-catch-Block behandelt Parallelitätsprobleme. Parallelitätsausnahmen werden ausgelöst, wenn ein Benutzer eine Mailingliste zum Bearbeiten auswählt und ein anderer Benutzer dieselbe Mailingliste bearbeitet, während die **Edit**-Seite im Browser angezeigt wird. In einem solchen Fall zeigt der Code eine Warnmeldung an und gibt an, welche Felder von dem anderen Benutzer geändert wurden.  Die TSL-API verwendet `ETag` für die Erkennung von Parallelitätsproblemen. Der `ETag`-Wert wird bei jeder Änderung an einer Tabellenzeile geändert.  Beim Abrufen einer zu bearbeitenden Zeile speichern Sie den `ETag`-Wert, und beim Ausführen der update- oder delete-Operation übergeben Sie den gespeicherten `ETag`-Wert. (Die `Edit`-Ansicht enthält ein verborgenes Feld für den ETag-Wert.) Falls eine update-Operation feststellt, dass sich der `ETag`-Wert eines Eintrags vom `ETag`-Wert unterscheidet, den Sie der update-Operation übergeben haben, wird eine Parallelitäts-Ausnahme ausgelöst. Falls Ihnen Parallelitätsprobleme egal sind, können Sie das ETag-Feld in der Entität, die Sie an die update-Operation übergeben, mit einem Sternchen ("*") ausfüllen. Dies führt dazu, dass alle Konflikte ignoriert werden.  

   Hinweis: Der HTTP-Fehlercode 412 wird nicht ausschließlich für Parallelitätsprobleme verwendet. Die SCL-API kann diesen Code auch aus anderen Gründen auslösen.

   Für die **Delete**-Seite ruft die `HttpGet Delete`-Methode die jeweilige Zeile ab, um deren Inhalte anzuzeigen, und die `HttpPost`-Methode löscht die `MailingList`-Zeile zusammen mit allen `Subscriber`-Zeilen, die der entsprechenden Mailingliste in der Tabelle `MailingList` zugeordnet sind.

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(string partitionKey)
        {
            // Löschen aller Zeilen für diese Mailingliste, d. h.
            // Mailinglistenzeilen und Abonnentenzeilen.
            // Aus diesem Grund muss kein Zeilenschlüssel angegeben werden.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            var listRows = mailingListTable.ExecuteQuery(query).ToList();
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    mailingListTable.ExecuteBatch(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                mailingListTable.ExecuteBatch(batchOperation);
            }
            return RedirectToAction("Index");
        }

 Der Code löscht die Einträge stapelweise, für den Fall, dass eine große Anzahl von Abonnenten gelöscht werden muss. Die Transaktionskosten für das Löschen einer Zeile sind gleich wie beim stapelweisen Löschen von 100 Zeilen. Pro Stapel können maximal 100 Operationen ausgeführt werden. 

 Obwohl die Schleife sowohl `MailingList`-Zeilen als auch `Subscriber`-Zeilen verarbeitet, werden alle Zeilen in die `MailingList`-Entitätsklasse gelesen, da für die `Delete`-Operation nur die Felder `PartitionKey`, `RowKey` und `ETag` benötigt werden.

### Fügen Sie die MailingList MVC-Ansichten hinzu

1.  Erstellen Sie im **Projektmappen-Explorer** einen neuen Ordner unter dem Ordner *Views* im MVC-Projekt und nennen Sie den Ordner *MailingList*.

2.  Klicken Sie mit der rechten Maustaste auf den neuen *Views\\MailingList*-Ordner und wählen Sie **Vorhandenes Element hinzufügen**.

    ![Vorhandenes Element zum Ordner Views hinzufügen](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-views.png)

3.  Navigieren Sie zum Ordner, in dem Sie die heruntergeladene Beispielanwendung gespeichert haben, wählen Sie alle vier .cshtml-Dateien im Ordner *Views\\MailingList* aus und klicken Sie auf **Hinzufügen**.

4.  Öffnen Sie die Datei *Edit.cshtml* und betrachten Sie den Code.

         @model MvcWebRole.Models.MailingList
                 @{
             ViewBag.Title = "Edit Mailing List";
         }
                 <h2>Edit Mailing List</h2>
               @using (Html.BeginForm()) {
               @Html.AntiForgeryToken()
               @Html.ValidationSummary(true)
               @Html.HiddenFor(model => model.ETag)
               <fieldset>
                 <legend>MailingList</legend>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.ListName)
                 </div>
                 <div class="editor-field">
                     @Html.DisplayFor(model => model.ListName)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.Description)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.Description)
                     @Html.ValidationMessageFor(model => model.Description)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.FromEmailAddress)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.FromEmailAddress)
                     @Html.ValidationMessageFor(model => model.FromEmailAddress)
                 </div>
                 <p>
                     <input type="submit" value="Save" />
                 </p>
             </fieldset>
         }
         <div>
             @Html.ActionLink("Back to List", "Index")
         </div>       
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    Dieser Code ist typisch für MVC-Ansichten. Beachten Sie das verborgene Feld zum Speichern des `ETag`-Werts, der für den Umgang mit Parallelitätsproblemen verwendet wird. Beachten Sie außerdem, dass das Feld `ListName` eine `DisplayFor`-Hilfe anstatt einer `EditorFor`-Hilfe hat. Wir erlauben keine Änderungen am Listennamen in der **Edit**-Seite, da der Code im Controller in diesem Fall wesentlich komplexer sein müsste: Die `HttpPost Edit`-Methode müsste die existierende Mailinglist-Zeile und alle zugehörigen Abonnentenzeilen löschen und anschließend alle Zeilen mit dem neuen Schlüsselwert wieder einfügen. In einer Produktionsanwendung kann diese zusätzliche Komplexität durchaus Sinn machen. Wie Sie später sehen werden, sind Änderungen am Listennamen im `Subscriber`-Controller erlaubt, da in diesem Fall nur jeweils eine Zeile betroffen ist.

    Der Code in *Create.cshtml* und *Delete.cshtml* ähnelt dem Code in *Edit.cshtml*.

5.  Öffnen Sie die Datei *Index.cshtml* und betrachten Sie den Code.

         @model IEnumerable<MvcWebRole.Models.MailingList>
         @{
             ViewBag.Title = "Mailing Lists";
         }
         <h2>Mailing Lists</h2>
         <p>
             @Html.ActionLink("Create New", "Create")
         </p>
         <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.ListName)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.Description)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.FromEmailAddress)
                 </th>
                 <th></th>
             </tr>
         @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.ListName)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Description)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.FromEmailAddress)
                 </td>
                 <td>
                     @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
                     @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
                 </td>
             </tr>
         }
         </table>

    Dieser Code ist ebenfalls typisch für MVC-Ansichten. Die **Edit-** und **Delete-** Links geben die Parameter Partitions- und Zeilenschlüssel für die Abfragezeichenfolge an, um eine bestimmte Zeile identifizieren zu können. Für `MailingList`-Entitäten wird nur ein Partitionsschlüssel benötigt, da der Zeilenschlüssel immer "MailingList" ist. Beide Werte werden jedoch gespeichert, um den Code der MVC-Ansicht in allen Controllern und Ansichten einheitlich zu halten.

### Setzen von MailingList als Standardcontroller

1.  Öffnen Sie die Datei *Route.config.cs* im Ordner *App\_Start*.

2.  Ändern Sie in der Zeile mit den Standardwerten den Standardcontroller von "Home" zu "MailingList".

          routes.MapRoute(
              name: "Default",
              url: "{controller}/{action}/{id}",
              defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }

SpeicherkonfigurationKonfigurieren Sie Ihre Webrolle, um Ihr Test-Azure-Speicherkonto zu verwenden
--------------------------------------------------------------------------------------------------

Sie werden die Einstellungen für Ihr Test-Speicherkonto eingeben, das Sie für die lokale Ausführung des Projekts verwenden. Neue Einstellungen müssen immer für Cloud- und lokale Ausführung hinzugefügt werden, aber Sie können den Cloudwert später ändern. Dieselbe Einstellung wird später für Workerrolle A hinzugefügt.

(Informationen zu den notwendigen Änderungen für die Ausführung der Web-GUI in einer Azure-Website anstelle eines Azure-Cloud-Dienstes finden Sie im Abschnitt [Alternative Architektur](#alternativearchitecture) gegen Ende dieses Lernprogramms.)

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **MvcWebRole** unter **Roles** im **AzureEmailService**-Cloudprojekt und wählen Sie **Eigenschaften** aus.

    ![Webrollen-Eigenschaften](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png)

2.  Stellen Sie sicher, dass in der Dropdownliste **Dienstkonfiguration** der Eintrag **Alle Konfigurationen** ausgewählt ist.

3.  Wählen Sie die Registerkarte **Einstellungen**, und klicken Sie auf **Einstellung hinzufügen**.

4.  Geben Sie "StorageConnectionString" in der Spalte **Name** ein.

5.  Wählen Sie in der Dropdownliste **Typ** den Eintrag **Verbindungszeichenfolge** aus.

6.  Klicken Sie auf die elliptische (**...**) Schaltfläche rechts in der Zeile, um das Dialogfeld **Verbindungszeichenfolge für Speicherkonto** zu öffnen.

    ![Rechtsklick Eigenschaften](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png)

7.  Klicken Sie im Dialogfeld **Verbindungszeichenfolge für Speicherkonto** auf das Optionsfeld **Ihr Abonnement** und anschließend auf den Link **Veröffentlichungseinstellungen herunterladen**.

    > [WACOM.NOTE] Mit dem neuesten SDK müssen Sie nichts herunterladen, sondern wählen zwischen verfügbaren Speicherkonten in einer Dropdownliste aus.

    **Hinweis:** Falls Sie die Speichereinstellungen bereits für das Lernprogramm 2 konfiguriert haben und dieses Lernprogramm auf demselben Computer ausführen, müssen Sie die Einstellungen nicht erneut herunterladen, sondern können auf **Ihr Abonnement** klicken und das entsprechende **Abonnement** und Ihren **Kontonamen auswählen**.

    ![Rechtsklick Eigenschaften](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-enter.png)

    Wenn Sie auf den Link **Veröffentlichungseinstellungen herunterladen** klicken, startet Visual Studio eine neue Instanz Ihres Standardbrowsers mit der Seite zum Herunterladen von Veröffentlichungseinstellungen im Azure-Verwaltungsportal. Falls Sie nicht am Portal angemeldet sind, werden Sie zur Anmeldung aufgefordert. Nach der Anmeldung können Sie die Veröffentlichungseinstellungen mit Ihrem Browser speichern. Notieren Sie sich den Speicherort der Datei.

    ![Veröffentlichungseinstellungen](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-3.png)

8.  Klicken Sie im Dialog **Verbindungszeichenfolge für Speicherkonto** auf **Importieren** und navigieren Sie zu den Veröffentlichungseinstellungen, die Sie im vorherigen Schritt gespeichert haben.

9.  Wählen Sie Abonnement und Speicherkonto aus und klicken Sie auf **OK**.

    ![Speicherkonto auswählen](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-5.png)

10. Wiederholen Sie die Prozedur, mit der Sie die `StorageConnectionString`-Verbindungszeichenfolge gesetzt haben, für die `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`-Verbindungszeichenfolge.

    Sie müssen die Einstellungsveröffentlichungsdatei nicht erneut herunterladen. Wenn Sie auf die Ellipse für die `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`-Verbindungszeichenfolge klicken, werden Sie feststellen, dass sich das Dialogfeld **Verbindungszeichenfolge für Speicherkonto** Ihre Abonnementdaten gemerkt hat. Wenn Sie auf das Optionsfeld **Ihr Abonnement** klicken, brauchen Sie nur noch die bereits zuvor ausgewählten Werte für **Abonnement** und **Kontoname** auswählen und auf **OK** klicken.

11. Wiederholen Sie die Prozedur, mit der Sie die beiden Verbindungszeichenfolgen für die MvcWebRole-Rolle gesetzt haben, um die Verbindungszeichenfolgen für die Workerrolle A zu setzen.

Als Sie eine neue Einstellung über die Schaltfläche **Einstellungen hinzufügen** hinzugefügt haben, wurde die neue Einstellung zum XML in der Datei *ServiceDefinition.csdf* und in den beiden *.cscfg*-Konfigurationsdateien hinzugefügt. Das folgende XML wird von Visual Studio zur Datei *ServiceDefinition.csdf* hinzugefügt.

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

Das folgende XML wird zu den beiden *.cscfg*-Dateien hinzugefügt.

       <Setting name="StorageConnectionString"
       value="DefaultEndpointsProtocol=https;
       AccountName=azuremailstorage;
       AccountKey=[Ihr Kontoschlüssel]" />

Sie können Einstellungen manuell zur Datei *ServiceDefinition.csdf* und den beiden *.cscfg*-Konfigurationsdateien hinzufügen. Allerdings bietet der Eigenschaften-Editor die folgenden Vorteile für Verbindungszeichenfolgen:

-   Sie müssen die neue Einstellung nur an einem Ort anlegen, und das korrekte XML wird zu allen drei Dateien hinzugefügt.
-   Das korrekte XML wird für alle drei Einstellungsdateien generiert. Die Datei *ServiceDefinition.csdf* definiert Einstellungen, die in beiden *.cscfg*-Konfigurationsdateien vorhanden sein müssen. Falls die Datei *ServiceDefinition.csdf* und die beiden *.cscfg*-Konfigurationsdateien inkonsistent sind, kann es passieren, dass Sie die folgende Fehlermeldung in Visual Studio erhalten: "The current service model is out of sync. Make sure both the service configuration and definition files are valid."

    ![Fehler: ungültige Dienstkonfiguration und Definitionsdateien](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png)

Wenn dieser Fehler angezeigt wird, können Sie den Eigenschaften-Editor erst nach Behebung der Inkonsistenz wieder verwenden.

### Testen der Anwendung

1.  Drücken Sie STRG+F5, um das Projekt auszuführen.

    ![Leere Indexseite der Mailingliste](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png)

2.  Erstellen Sie einige Mailinglisten mit der **Create**-Funktion und testen Sie, ob die **Edit**- und **Delete**-Funktionen wie gewünscht funktionieren.

    ![Indexseite der Mailingliste mit Einträgen](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png)

AbonnentenErstellen und Testen der Abonnenten-Controller und -Ansichten
-----------------------------------------------------------------------

Administratoren können in der **Abonnenten**-Web-GUI neue Abonnenten zu Mailinglisten hinzufügen und bestehende Abonnenten bearbeiten, anzeigen und löschen.

### Fügen Sie die Subscriber-Entitätsklasse zum Ordner Models hinzu

Die `Subscriber`-Entitätsklasse bildet die Zeilen in der Tabelle `MailingList` ab, die Informationen über Abonnenten von Listen enthalten. Diese Zeilen enthalten Daten wie z. B. die E-Mail-Adresse der Person und eine Angabe, ob die Adresse bestätigt wurde.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Models* im MVC-Projekt, und klicken Sie anschließend auf **Vorhandenes Element hinzufügen**.

2.  Navigieren Sie zum Ordner, in dem Sie die heruntergeladene Beispielanwendung gespeichert haben, wählen Sie die Datei *Subscriber.cs* im Ordner *Models* aus und klicken Sie auf **Hinzufügen**.

3.  Öffnen Sie die Datei *Subscriber.cs* und betrachten Sie den Code.

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
            
                 public bool
         Verified { get; set; }
             }

    Ebenso wie die `MailingList`-Entitätsklasse liest und schreibt die `Subscriber`-Entitätsklasse Zeilen in der Tabelle `mailinglist`. `Subscriber`-Zeilen verwenden die E-Mail-Adresse anstelle der Konstante "mailinglist" als Zeilenschlüssel. (Eine Erklärung der Tabellenstruktur finden Sie im [ersten Lernprogramm in dieser Serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).) Aus diesem Grund wird eine `EmailAddress`-Eigenschaft definiert, welche die `RowKey`-Eigenschaft als Unterstützungsfeld verwendet, ebenso wie `ListName` die `PartitionKey`-Eigenschaft als Unterstützungsfeld verwendet. Wie bereits erklärt versetzt Sie dies in die Lage, DataAnnotations-Attribute für Formatierung und Prüfung für die Eigenschaften zu verwenden.

    Der `SubscriberGUID`-Wert wird bei der Erstellung der `Subscriber`-Entität generiert. Dieser Wert wird in Links zum Abonnieren/Abbestellen verwendet, um sicherzustellen, dass nur autorisierte Personen Abonnements für E-Mail-Adressen hinzufügen und entfernen können. Der Wert für `Verified` ist `Falsch`, wenn eine neue Zeile für einen neuen Abonnenten angelegt wird. Der Wert für `Verified` wird erst auf `Wahr` gesetzt, nachdem der neue Abonnent auf den **Bestätigungs**-Link in der Willkommens-E-Mail klickt. Beim Versand von Nachrichten an Listen erhalten diejenigen Abonnenten, bei denen `Verified` = `Falsch` ist, keine E-Mail.

    Die `Verified`-Eigenschaft in der `Subscriber`-Entität lässt Nullwerte zu. Wenn Sie angeben, dass eine Abfrage `Subscriber`-Entitäten zurückgeben soll, kann es sein, dass manche der Zeilen keine `Verified`-Eigenschaft enthalten. Daher ist die `Verified`-Eigenschaft in der `Subscriber`-Entität als nullbar definiert, um den tatsächlichen Inhalt von Tabellenzeilen genauer abbilden zu können, die keine *Verified*-Eigenschaft haben. Möglicherweise sind Sie es gewöhnt, mit SQL Server-Tabellen zu arbeiten, in denen jede Zeile einer Tabelle dasselbe Schema hat. In Azure-Speichertabellen ist jede Zeile nur eine Sammlung von Eigenschaften, und jede Zeile kann unterschiedliche Eigenschaften enthalten. Zeilen mit "MailingList" als Zeilenschlüssel haben in der Azure-E-Mail-Beispielanwendung z. B. keine `Verified`-Eigenschaft. Wenn eine Abfrage eine Zeile ohne `Verified`-Eigenschaft zurückgibt, dann hat die `Verified`-Eigenschaft bei der Instantiierung der `Subscriber`-Entitätsklasse den Wert null. Wenn die Eigenschaft nicht nullbar wäre, würden Sie den Wert `Falsch` sowohl für Zeilen mit `Verified` = `Falsch` als auch für Zeilen ohne `Verified`-Eigenschaft erhalten. Daher sollten bei der Arbeit mit Azure-Tabellen nach Möglichkeit alle Eigenschaften von Entitätsklassen nullbar sein, um auch solche Zeilen korrekt auslesen zu können, die von anderen Entitätsklassen oder von anderen Versionen der aktuellen Entitätsklasse erstellt wurden.

### Fügen Sie den Subscriber MVC-Controller hinzu

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Controllers* im MVC-Projekt, und klicken Sie anschließend auf **Vorhandenes Element hinzufügen**.

2.  Navigieren Sie zum Ordner, in dem Sie die heruntergeladene Beispielanwendung gespeichert haben, wählen Sie die Datei *SubscriberController.cs* im Ordner *Controllers* aus und klicken Sie auf **Hinzufügen**. (Stellen Sie sicher, dass Sie *Subscriber.cs* öffnen und nicht *Subscribe.cs*; Sie werden *Subscribe.cs* später hinzufügen.)

3.  Öffnen Sie die Datei *SubscriberController.cs* und betrachten Sie den Code.

    Ein Großteil des Codes in diesem Controller ähnelt dem Code im `MailingList`-Controller. Selbst der Tabellenname ist derselbe, da die Abonnenteninformationen in der Tabelle `MailingList` gespeichert werden. Im Anschluss an die `FindRow`-Methode sehen Sie die `GetListNames`-Methode. Diese Methode liest die Daten für eine Dropdownliste in den **Create-** und **Edit**-Seiten, aus der Sie die Mailingliste auswählen, zu der eine E-Mail-Adresse hinzugefügt werden soll.

         private List<MailingList> GetListNames()
         {
             var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
             var lists = mailingListTable.ExecuteQuery(query).ToList();
             return lists;
         }

    Dies ist dieselbe Abfrage, die Sie bereits im `MailingList`-Controller gesehen haben. Für die Dropdownliste benötigen Sie nur Zeilen mit Informationen über Mailinglisten, daher wählen Sie nur Zeilen mit RowKey = "mailinglist" aus.

    Die Methode, die Daten für die **Index**-Seite abruft, benötigt Zeilen mit Abonnenteninformationen, daher wählen Sie alle Zeilen aus, für die RowKey = "MailingList" nicht gilt.

         public ActionResult Index()
         {
             var query = (new TableQuery<Subscriber>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist")));
             var subscribers = mailingListTable.ExecuteQuery(query).ToList();
             return View(subscribers);
         }

    Die Abfrage gibt an, dass die Daten in `Subscriber`-Objekte gelesen werden (durch die Angabe von `<Subscriber>`), aber die Daten stammen aus der Tabelle `mailinglist`.

    **Hinweis:** Die Anzahl der Abonnenten kann unter Umständen zu groß werden, um auf diese Weise in einer einzigen Abfrage verarbeitet zu werden. In einer späteren Version dieses Lernprogramms werden wir versuchen, eine Pagingfunktion zu implementieren und den Einsatz von Fortsetzungstoken zu demonstrieren. Sie benötigen Fortsetzungstoken für die Ausführung von Abfragen, die mehr als 1.000 Zeilen zurückgeben: Azure gibt 1.000 Zeilen und ein Fortsetzungstoken zurück, mit dem Sie eine weitere Abfrage ab dem Ende der vorherigen Abfrage ausführen können. (Der Azure-Speicher-Explorer kann nicht mit Fortsetzungstoken umgehen, daher geben diese Abfragen maximal 1.000 Zeilen zurück.) Weitere Informationen zu großen Resultsets und Fortsetzungstoken finden Sie unter [How to get most out of Azure Tables](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx) (Optimale Nutzung von Azure-Tabellen, in englischer Sprache) und [Azure Tables: Expect Continuation Tokens, Seriously](http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously) (Azure-Tabellen, erwarten Sie Fortsetzungstoken, im Ernst, in englischer Sprache).

    In der `HttpGet Create`-Methode werden die Daten für die Dropdownliste vorbereitet, und in der `HttpPost`-Methode setzen Sie die Standardwerte vor dem Speichern der neuen Entität.

         public ActionResult Create()
         {
             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description");
             var model = new Subscriber() { Verified = false };
             return View(model);
         }

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Create(Subscriber subscriber)
         {
             if (ModelState.IsValid)
             {
                 subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                 if (subscriber.Verified.HasValue == false)
                 {
                     subscriber.Verified = false;
                 }

                 var insertOperation = TableOperation.Insert(subscriber);
                 mailingListTable.Execute(insertOperation);
                 return RedirectToAction("Index");
             }

             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);

             return View(subscriber);
         }

    Die `HttpPost Edit`-Seite ist komplexer als der `MailingList`-Controller, da Sie auf der `Subscriber`-Seite die Schlüsselfelder Listenname und E-Mail-Adresse ändern können. Wenn ein Benutzer eines dieser Felder ändert, müssen Sie den existierenden Eintrag löschen und einen neuen Eintrag hinzufügen, anstatt den bestehenden Eintrag zu ändern. Der folgende Code zeigt einen Teil der Bearbeitungsmethode, welche die unterschiedlichen Prozeduren für Schlüsseländerungen und Nicht-Schlüsseländerungen verarbeitet:

             if (ModelState.IsValid)
             {
                 try
                 {
                     UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
                     if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
                     {
                         //Schlüssel nicht geändert -- Zeile wird aktualisiert
                         var replaceOperation = TableOperation.Replace(editedSubscriber);
                         mailingListTable.Execute(replaceOperation);
                     }
                     else
                     {
                         // Schlüssel geändert, alte Zeile löschen und neue einfügen.
                         if (editedSubscriber.PartitionKey != partitionKey)
                         {
                             // Partitionsschlüssel geändert, löschen/einfügen kann nicht in einem Stapel ausgeführt werden.
                             var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                             mailingListTable.Execute(deleteOperation);
                             var insertOperation = TableOperation.Insert(editedSubscriber);
                             mailingListTable.Execute(insertOperation);
                         }
                         else
                         {
                             // Zeilenschlüssel geändert, löschen/einfügen in einem Stapel.
                             var batchOperation = new TableBatchOperation();
                             batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                             batchOperation.Insert(editedSubscriber);
                             mailingListTable.ExecuteBatch(batchOperation);
                         }
                     }
                     return RedirectToAction("Index");

    Die von der MVC-Modellbindung an die `Edit`-Methode übergebenen Parameter enthalten den Original-Listennamen und die E-Mail-Adresse (in den Parametern `partitionKey` und `rowKey`) sowie die vom Benutzer eingegebenen Werte (in den Parametern `listName` und `emailAddress`):

         public ActionResult Edit(string partitionKey, string rowKey, string listName, string emailAddress)

    Die an die `UpdateModel`-Methode übergebenen Parameter schließen die `PartitionKey-` und `RowKey`-Eigenschaften von der Modellbindung aus:

             var excludeProperties = new string[] { "PartitionKey", "RowKey" };

    Dies liegt daran, dass die `ListName-` und `EmailAddress`-Eigenschaften `PartitionKey` und `RowKey` als Unterstützungseigenschaften verwenden und der Benutzer möglicherweise einen dieser Werte geändert haben könnte. Wenn die Modellbindung das Modell durch Setzen der `ListName`-Eigenschaft aktualisiert, wird die `PartitionKey`-Eigenschaft automatisch aktualisiert. Falls die Modellbindung die `PartitionKey`-Eigenschaft nach der Aktualisierung der `ListName`-Eigenschaft ihrem eigenen Originalwert aktualisieren würde, müsste sie dabei den neuen, von der `ListName`-Eigenschaft gesetzten Wert überschreiben. Die `EmailAddress`-Eigenschaft aktualisiert die `RowKey`-Eigenschaft automatisch auf dieselbe Weise.

    Nach der Aktualisierung des `editedSubscriber`-Modellobjekts prüft der Code, ob Partitions- oder Zeilenschlüssel geändert wurde. Falls einer dieser Werte geändert wurde, muss die existierende Abonnentenzeile gelöscht und eine neue Zeile eingefügt werden. Falls nur der Zeilenschlüssel geändert wurde, können Löschung und Einfügung in einer atomischen Stapeltransaktion ausgeführt werden.

    Beachten Sie, dass der Code eine neue Entität zur Übergabe in der `Delete`-Operation erstellt:

             // Zeilenschlüssel geändert, löschen/einfügen in einem Stapel.
             var batchOperation = new TableBatchOperation();
             batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
             batchOperation.Insert(editedSubscriber);
             mailingListTable.ExecuteBatch(batchOperation);

    Die an Stapeloperationen übergebenen Entitäten müssen immer eindeutig sein. Sie können z. B. keine `Subscriber`-Entität erstellen, diese an eine `Delete`-Operation übergeben, anschließend einen Wert in derselben `Subscriber`-Entität ändern und diese an eine `Insert`-Operation übergeben. In diesem Fall wäre der Status der Entität nach der Eigenschaftsänderung sowohl für die Delete- als auch für die Insert-Operation gültig.

    **Hinweis:** Operationen in einem Stapel müssen alle auf derselben Partition ausgeführt werden. Änderungen an Listennamen wirken sich auf den Partitionsschlüssel aus und können daher nicht in einer Transaktion ausgeführt werden.

### Fügen Sie die Subscriber MVC-Ansichten hinzu

1.  Erstellen Sie im **Projektmappen-Explorer** einen neuen Ordner unter dem Ordner *Views* im MVC-Projekt und nennen Sie den Ordner *Subscriber*.

2.  Klicken Sie mit der rechten Maustaste auf den neuen *Views\\Subscriber*-Ordner und wählen Sie **Vorhandenes Element hinzufügen**.

3.  Navigieren Sie zum Ordner, in dem Sie die heruntergeladene Beispielanwendung gespeichert haben, wählen Sie alle vier .cshtml-Dateien im Ordner *Views\\Subscriber* aus und klicken Sie auf **Hinzufügen**.

4.  Öffnen Sie die Datei *Edit.cshtml* und betrachten Sie den Code.

         @model MvcWebRole.Models.Subscriber
            
         @{
             ViewBag.Title = "Edit Subscriber";
         }
            
         <h2>Edit Subscriber</h2>
            
         @using (Html.BeginForm()) {
             @Html.AntiForgeryToken()
             @Html.ValidationSummary(true)
              @Html.HiddenFor(model => model.SubscriberGUID)
              @Html.HiddenFor(model => model.ETag)
              <fieldset>
                 <legend>Subscriber</legend>
                 <div class="display-label">
                      @Html.DisplayNameFor(model => model.ListName)
                 </div>
                 <div class="editor-field">
                     @Html.DropDownList("ListName", String.Empty)
                     @Html.ValidationMessageFor(model => model.ListName)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.EmailAddress)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.EmailAddress)
                     @Html.ValidationMessageFor(model => model.EmailAddress)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.Verified)
                 </div>
                 <div class="display-field">
                     @Html.EditorFor(model => model.Verified)
                 </div>
                 <p>
                     <input type="submit" value="Save" />
                 </p>
             </fieldset>
         }
            
         <div>
             @Html.ActionLink("Back to List", "Index")
         </div>
            
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    Der Code ähnelt der bereits gezeigten **Edit**-Ansicht für `MailingList`. Der `SubscriberGUID`-Wert wird nicht angezeigt, daher wird der Wert nicht automatisch in einem Formularfeld für die `HttpPost`-Controllermethode geliefert. Aus diesem Grund wird ein verborgenes Feld für diesen Wert verwendet.

    Der Code der weiteren Ansichten ähnelt dem bereits gezeigten Code des `MailingList`-Controllers.

### Testen der Anwendung

1.  Drücken Sie STRG+F5, um das Projekt auszuführen, und klicken Sie auf **Subscribers**.

    ![Leere Indexseite für Abonnenten](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png)

2.  Erstellen Sie einige Mailinglisten mit der **Create**-Funktion und testen Sie, ob die **Edit**- und **Delete**-Funktionen wie gewünscht funktionieren.

    ![Indexseite für Abonnenten mit Einträgen](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png)

NachrichtErstellen und Testen der Nachrichten-Controller und -Ansichten
-----------------------------------------------------------------------

Administratoren können in der **Message**-Web-GUI Informationen über Nachrichten, deren Versand an eine Mailingliste geplant wurde, erstellen, bearbeiten und anzeigen.

### Fügen Sie die Message-Entitätsklasse zum Ordner Models hinzu

Die `Message`-Entitätsklasse bildet die Zeilen in der Tabelle `Message` ab, die Informationen über Nachrichten enthalten, deren Versand an eine Liste geplant wurde. Diese Zeilen enthalten Informationen wie z. B. die Betreffzeile, die Liste, an die eine Nachricht verschickt werden soll und das geplante Versanddatum.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Models* im MVC-Projekt, und klicken Sie anschließend auf **Vorhandenes Element hinzufügen**.

2.  Navigieren Sie zum Ordner, in dem Sie die heruntergeladene Beispielanwendung gespeichert haben, wählen Sie die Datei *Message.cs* im Ordner Models aus und klicken Sie auf **Hinzufügen**.

3.  Öffnen Sie die Datei *Message.cs* und betrachten Sie den Code.

         public class Message : TableEntity
         {
             private DateTime
         _scheduledDate;
             private long _messageRef;

             public Message()
             {
                 this.MessageRef = DateTime.Now.Ticks;
                 this.Status = "Pending";
             }

             [Required]
             [Display(Name = "Scheduled Date")]
             // DataType.Date zeigt nur Datum (ohne Uhrzeit) an und lässt sich leicht mit jQuery DatePicker verbinden
             [DataType(DataType.Date)]
             public DateTime
         ScheduledDate 
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

             // Ausstehend, Queuing, In Verarbeitung, Abgeschlossen
             public string Status { get; set; }
         }

   Die `Message`-Klasse definiert einen Standardkonstruktor, der die `MessageRef`-Eigenschaft auf einen eindeutigen Wert für die Nachricht setzt. Da dieser Wert Teil des Zeilenschlüssels ist, setzt der Setter für die `MessageRef`-Eigenschaft automatisch ebenfalls die `RowKey`-Eigenschaft. Der Setter der `MessageRef`-Eigenschaft verkettet die Zeichenfolge "message" mit dem `MessageRef`-Wert und schreibt das Ergebnis in die `RowKey`-Eigenschaft.

   Der `MessageRef`-Wert wird aus dem `Ticks`-Wert von `DateTime.Now` generiert. Damit wird sichergestellt, dass die Nachrichten in der Web-GUI standardmäßig immer in der Erstellungsreihenfolge für ein gegebenes geplantes Datum angezeigt werden (`ScheduledDate`ist der Partitionsschlüssel). Sie könnten die Zeilen auch mithilfe einer GUID eindeutig machen, dann wäre aber die Standardreihenfolge beim Auslesen zufällig.

   Der Standardkonstruktor setzt außerdem den Standardstatus Pending (ausstehend) für neue `message`-Zeilen.

   Weitere Informationen zur Struktur der Tabelle `message` finden Sie im [ersten Lernprogramm in dieser Serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

### Fügen Sie den Message MVC-Controller hinzu

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner "Controllers" im MVC-Projekt, und klicken Sie anschließend auf **Vorhandenes Element hinzufügen**.

2.  Navigieren Sie zum Ordner, in dem Sie die heruntergeladene Beispielanwendung gespeichert haben, wählen Sie die Datei *MessageController.cs* im Ordner *Controllers* aus und klicken Sie auf **Hinzufügen**.

3.  Öffnen Sie die Datei *MessageController.cs* und betrachten Sie den Code.

    Ein Großteil des Codes in diesem Controller ähnelt dem Code im `Subscriber`-Controller. Neu hinzugekommen ist der Code für den Umgang mit Blobs. Für jede Nachricht wird der Inhalt der E-Mail als HTML und als Nur-Text in Form von .htm- bzw. .txt-Dateien hochgeladen und in Blobs gespeichert.

    Blobs werden in Blob-Containern gespeichert. Die Azure-E-Mail-Dienstanwendung speichert sämtliche Blobs in einem einzigen Blob-Container namens "azuremailblobcontainer", und der Code im Konstruktor des Controllers ruft einen Verweis auf diesen Blob-Container ab:

         public class MessageController : Controller
         {
             private TableServiceContext serviceContext;
             private static CloudBlobContainer blobContainer;

               public MessageController()
             {
                 var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
                 // Für die Ausführung in einer Azure-Website (anstelle eines Cloud-Dienstes) verwenden Sie die Datei Web.config:
                 //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

                 // Kontextobjekt für den Umgang mit Tabellen und ein Verweis auf den Blob-Container.
                 var tableClient = storageAccount.CreateCloudTableClient();
             serviceContext = tableClient.GetTableServiceContext();
                 var blobClient = storageAccount.CreateCloudBlobClient();
                 blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
             }

    Für jede Datei, die ein Benutzer zum Hochladen auswählt, liefert die MVC-Ansicht ein `HttpPostedFile`-Objekt, das Informationen über die Datei enthält. Wenn ein Benutzer eine neue Nachricht erstellt, wird das `HttpPostedFile`-Objekt verwendet, um die Datei in ein Blob geschrieben. Beim Ändern von Nachrichten können Benutzer entweder eine Ersatzdatei hochladen oder das Blob unverändert übernehmen.

    Der Controller enthält eine Methode, die von `HttpPost Create` und `HttpPost Edit` aufgerufen wird, um das Blob zu speichern:

         private void SaveBlob(string blobName, HttpPostedFileBase httpPostedFile)
         {
             // Verweis auf ein Blob abrufen. 
             CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);
             // Blob erstellen bzw. existierendes Blob überschreiben, indem eine lokale Datei hochgeladen wird.
             using (var fileStream = httpPostedFile.InputStream)
             {
                 blob.UploadFromStream(fileStream);
             }
         }

    Die `HttpPost Create`-Methode speichert die beiden Blobs und fügt anschließend die `Message`-Tabellenzeile ein. Der Name de Blobs entsteht durch die Verkettung des `MessageRef`-Werts mit der Dateierweiterung ".htm" bzw. ".txt".

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
         {
             if (file == null)
             {
                 ModelState.AddModelError(string.Empty, "Bitte geben Sie einen Pfad zur HTML-Datei an");
             }

             if (txtFile == null)
             {
                 ModelState.AddModelError(string.Empty, "Bitte geben Sie einen Pfad zur Textdatei an");
             }

             if (ModelState.IsValid)
             {
                 SaveBlob(message.MessageRef + ".htm", file);
                 SaveBlob(message.MessageRef + ".txt", txtFile);

                 var insertOperation = TableOperation.Insert(message);
                 messageTable.Execute(insertOperation);

                 return RedirectToAction("Index");
             }

             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description");
             return View(message);
         }

    Die `HttpGet Edit`-Methode prüft, ob die abgerufene Nachricht den Status `Pending` hat. So wird verhindert, dass ein Benutzer Nachrichten ändert, die bereits von Workerrolle B verarbeitet werden. Die Methoden `HttpPost Edit`, `Delete` und `DeleteConfirmed` enthalten ähnlichen Code.

         public ActionResult Edit(string partitionKey, string rowKey)
         {
             var message = FindRow(partitionKey, rowKey);
             if (message.Status != "Pending")
             {
                 throw new Exception("Nachricht ist nicht ausstehend und kann daher nicht bearbeitet werden.");
             }

             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description", message.ListName);
             return View(message);
         }

    Der Code in der `HttpPost Edit`-Methode speichert nur dann ein neues Blob, wenn der Bearbeitung eine neue Datei hochlädt. Der folgende Code übergeht die Parallelitätsbehandlung. Diese erfolgt analog zum bereits gezeigten `MailingList`-Controller.

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Edit(string partitionKey, string rowKey, Message editedMsg,
             DateTime scheduledDate, HttpPostedFileBase httpFile, HttpPostedFileBase txtFile)
         {
             if (ModelState.IsValid)
             {
                 var excludePropLst = new List<string>();
                 excludePropLst.Add("PartitionKey");
                 excludePropLst.Add("RowKey");

                 if (httpFile == null)
                 {
                     // Kein Pfad bzw. keine Datei angegeben, Datei wird daher nicht aktualisiert.
                     excludePropLst.Add("HtmlPath");
                 }
                 else
                 {
                     // Pfad oder Datei wurde angegeben, Datei muss also aktualisiert werden.
                     SaveBlob(editedMsg.MessageRef + ".htm", httpFile);
                 }

                 if (txtFile == null)
                 {
                     excludePropLst.Add("TextPath");
                 }
                 else
                 {
                     SaveBlob(editedMsg.MessageRef + ".txt", txtFile);
                 }

                 string[] excludeProperties = excludePropLst.ToArray();

                 try
                 {
                     UpdateModel(editedMsg, string.Empty, null, excludeProperties);
                     if (editedMsg.PartitionKey == partitionKey)
                     {
                         // Schlüssel nicht geändert -- Zeile wird aktualisiert.
                         var replaceOperation = TableOperation.Replace(editedMsg);
                         messageTable.Execute(replaceOperation);
                     }
                     else
                     {
                         // Partitionsschlüssel geändert -- Zeile löschen und neu einfügen.
                         // (Partitionsschlüssel enthält evtl. geändertes Versanddatum;
                         // Zeilenschlüssel enthält unveränderte MessageRef.)
                         var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
                         messageTable.Execute(deleteOperation);
                         var insertOperation = TableOperation.Insert(editedMsg);
                         messageTable.Execute(insertOperation);
                     }
                     return RedirectToAction("Index");
                 }

    Wenn das Versanddatum geändert wird, ändert sich der Partitionsschlüssel, und die Zeile muss gelöscht und neu eingefügt werden. Dies betrifft mehr als eine Partition und kann daher nicht in einer Transaktion ausgeführt werden.

    Die `HttpPost Delete`-Methode löscht die Blobs beim Löschen der Tabellenzeile:

         [HttpPost, ActionName("Delete")]
         public ActionResult DeleteConfirmed(String partitionKey, string rowKey)
         {
             // Zeile erneut abrufen, um sicherzustellen, dass der Status immer noch ausstehend ist.
             var message = FindRow(partitionKey, rowKey);
             if (message.Status != "Pending")
             {
                 throw new Exception("Nachricht ist nicht ausstehend und kann daher nicht gelöscht werden.");
             }

             DeleteBlob(message.MessageRef + ".htm");
             DeleteBlob(message.MessageRef + ".txt");
             var deleteOperation = TableOperation.Delete(message);
             messageTable.Execute(deleteOperation);
             return RedirectToAction("Index");
         }

         private void DeleteBlob(string blobName)
         {
             var blob = blobContainer.GetBlockBlobReference(blobName);
             blob.Delete();
         }

### Fügen Sie die Message MVC-Ansichten hinzu

1.  Erstellen Sie im **Projektmappen-Explorer** einen neuen Ordner unter dem Ordner *Views* im MVC-Projekt und nennen Sie den Ordner `Message`.

2.  Klicken Sie mit der rechten Maustaste auf den neuen *Views\\Message*-Ordner und wählen Sie **Vorhandenes Element hinzufügen**.

3.  Navigieren Sie zum Ordner, in dem Sie die heruntergeladene Beispielanwendung gespeichert haben, wählen Sie alle vier .cshtml-Dateien im Ordner *Views\\Message* aus und klicken Sie auf **Hinzufügen**.

4.  Öffnen Sie die Datei *Edit.cshtml* und betrachten Sie den Code.

         @model MvcWebRole.Models.Message
            
         @{
             ViewBag.Title = "Edit Message";
         }
            
         <h2>Edit Message</h2>
            
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
                 <div class="editor-label">
                     @Html.LabelFor(model => model.ListName, "MailingList")
                 </div>
                 <div class="editor-field">
                     @Html.DropDownList("ListName", String.Empty)
                     @Html.ValidationMessageFor(model => model.ListName)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.SubjectLine)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.SubjectLine)
                     @Html.ValidationMessageFor(model => model.SubjectLine)
                 </div>
                 <div class="editor-label">
                     HTML-Pfad: Leer lassen, um die aktuelle HTML-Datei zu behalten.
                 </div>
                 <div class="editor-field">
                     <input type="file" name="file" />
                 </div>
                 <div class="editor-label">
                     Textpfad: Leer lassen, um die aktuelle Textdatei zu behalten.
                 </div>
                 <div class="editor-field">
                     <input type="file" name="TxtFile" />
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.ScheduledDate)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.ScheduledDate)
                     @Html.ValidationMessageFor(model => model.ScheduledDate)
                 </div>
                 <div class="display-label">
                     @Html.DisplayNameFor(model => model.Status)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.Status)
                 </div>
                <p>
                     <input type="submit" value="Save" />
                 </p>
             </fieldset>
         }
            
         <div>
             @Html.ActionLink("Back to List", "Index")
         </div>
            
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    Die `HttpPost Edit`-Methode benötigt Partitions- und Zeilenschlüssel, daher werden diese Werte vom Code in verborgenen Feldern bereitgestellt. Die verborgenen Felder wurden im `Subscriber`-Controller nicht benötigt, weil: (a) die `PartitionKey-` und `RowKey-`Eigenschaften im `Subscriber`-Modell die `ListName-` und `EmailAddress-` Eigenschaften aktualisieren, und (b) die `ListName-` und `EmailAddress-`Eigenschaften in der Edit-Ansicht durch `EditorFor`-Hilfen enthalten waren. Wenn die MVC-Modellbindung für das `Subscriber`-Modell die `ListName`-Eigenschaft aktualisiert, wird auch die `PartitionKey`-Eigenschaft automatisch aktualisiert, und wenn die MVC-Modellbindung die `EmailAddress`-Eigenschaft im `Subscriber`-Modell aktualisiert, wird auch die `RowKey`-Eigenschaft automatisch aktualisiert. Die Felder, die im `Message`-Modell auf Partitions- und Zeilenschlüssel abgebildet werden, sind nicht bearbeitbar und werden daher nicht auf diese Weise gesetzt.

    Für die `MessageRef`-Eigenschaft ist ebenfalls ein verborgenes Feld vorhanden. Dieses enthält denselben Wert wie der Partitionsschlüssel, wird jedoch zur Verbesserung der Übersichtlichkeit in der `HttpPost Edit`-Methode verwendet. Durch das verborgene Feld `MessageRef` ist der Code der `HttpPost Edit`-Methode in der Lage, bei der Erstellung der Dateinamen für die Blobs mit diesem Namen auf den `MessageRef`-Wert zu verweisen.

5.  Öffnen Sie die Datei *Index.cshtml* und betrachten Sie den Code.

         @model IEnumerable<MvcWebRole.Models.Message>
            
         @{
             ViewBag.Title = "Messages";
         }
            
         <h2>Messages</h2>
            
         <p>
             @Html.ActionLink("Create New", "Create")
         </p>
         <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.ListName)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.SubjectLine)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.ScheduledDate)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.Status)
                 </th>
                 <th></th>
             </tr>
             @foreach (var item in Model)
             {
                 <tr>
                     <td>
                         @Html.DisplayFor(modelItem => item.ListName)
                     </td>
                     <td>
                         @Html.DisplayFor(modelItem => item.SubjectLine)
                     </td>
                     <td>
                         @Html.DisplayFor(modelItem => item.ScheduledDate)
                     </td>
                     <td>
                         @item.Status
                     </td>
                     <td>
                         @if (item.Status == "Pending")
                         {
                             @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
                             @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
                         }
                         @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
                     </td>
                 </tr>
             }
            
         </table>

    Im Gegensatz zu den anderen **Index**-Ansichten werden die **Edit-** und **Delete-**-Links hier nur für Nachrichten mit dem Status `Pending` angezeigt:

         @if (item.Status == "Pending")
         {
             @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
             @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
         }

    Damit wird verhindert, dass Benutzer Änderungen an Nachrichten vornehmen, die bereits von Workerrolle A verarbeitet werden.

    Der Code der weiteren Ansichten ähnelt dem bereits gezeigten Code der **Edit**-Ansicht und der Ansichten für die anderen Controller.

### Testen der Anwendung

1.  Drücken Sie STRG+F5, um das Projekt auszuführen, und klicken Sie auf **Messages**.

    ![Leere Indexseite für Nachrichten](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png)

2.  Erstellen Sie einige Mailinglisten mit der **Create**-Funktion und testen Sie, ob die **Edit**- und **Delete**-Funktionen wie gewünscht funktionieren.

    ![Indexseite für Abonnenten mit Einträgen](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png)

AbbestellenErstellen und Testen der Abbestellen-Controller und -Ansichten
-------------------------------------------------------------------------

Als Nächstes werden Sie die GUI für den Abbestellungsprozess implementieren.

**Hinweis:** In diesem Lernprogramm wird lediglich der Controller für den Abbestellungsprozess implementiert, und nicht für den Abonnierungsprozess. Wie bereits im [ersten Lernprogramm](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/) erklärt, werden GUI und Dienstmethode für den Abonnierungsprozess erst dann implementiert, wenn geeignete Sicherheitsmaßnahmen für die Dienstmethode vorhanden sind. Bis dahin können Sie E-Mail-Adressen in den **Subscriber**-Administratorseiten zu Ihren Listen hinzufügen.

### Fügen Sie das Unsubscribe-Anzeigemodell zum Ordner Models hinzu

Das `UnsubscribeVM`-Anzeigemodell übergibt Daten zwischen dem `Unsubscribe`-Controller und dessen Ansicht.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner `Models` im MVC-Projekt, und klicken Sie anschließend auf **Vorhandenes Element hinzufügen**.

2.  Navigieren Sie zum Ordner, in dem Sie die heruntergeladene Beispielanwendung gespeichert haben, wählen Sie die Datei `UnsubscribeVM.cs` im Ordner Models aus und klicken Sie auf **Hinzufügen**.

3.  Öffnen Sie die Datei `UnsubscribeVM.cs` und betrachten Sie den Code.

         public class UnsubscribeVM
         {
             public string EmailAddress { get; set; }
             public string ListName { get; set; }
             public string ListDescription { get; set; }
             public string SubscriberGUID { get; set; }
             public bool
             Confirmed { get; set; }
         }

    Die Unsubscribe-Links enthalten die `SubscriberGUID`. Dieser Wert wird verwendet, um E-Mail-Adresse, Listenname und Listenbeschreibung aus der Tabelle `MailingList` abzurufen. Die Ansicht enthält die E-Mail-Adresse und eine Beschreibung der Liste, deren Abonnement abbestellt werden soll, sowie eine **Confirm**-Schaltfläche, die der Benutzer anklicken muss, um die Abbestellung zu bestätigen.

### Fügen Sie den Unsubscribe-Controller hinzu

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner `Controllers` im MVC-Projekt, und klicken Sie anschließend auf **Vorhandenes Element hinzufügen**.

2.  Navigieren Sie zum Ordner, in dem Sie die heruntergeladene Beispielanwendung gespeichert haben, wählen Sie die Datei *UnsubscribeController.cs* im Ordner *Controllers* aus und klicken Sie auf **Hinzufügen**.

3.  Öffnen Sie die Datei *UnsubscribeController.cs* und betrachten Sie den Code.

    Dieser Controller enthält eine `HttpGet Index`-Methode für die Anzeige der ausgänglichen Unsubscribe-Seite, sowie eine `HttpPost Index`-Methode zum Verarbeiten der **Confirm-** und **Cancel-** Schaltflächen.

    Die `HttpGet Index`-Methode verwendet GUID und Listenname in der Abfragezeichenfolge zum Abrufen der `MailingList`-Tabellenzeile für den Abonnenten. Anschließend werden alle von der Ansicht benötigten Informationen in das Anzeigemodell übergeben, und die **Unsubscribe**-Seite wird angezeigt. Die `Confirmed`-Eigenschaft wird auf null gesetzt, um der Ansicht mitzuteilen, dass die ausgängliche Version der **Unsubscribe**-Seite angezeigt werden soll.

          public ActionResult Index(string id, string listName)
          {
              if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
              {
                  ViewBag.errorMessage = "Subscriber-ID oder Listenname leer.";
                  return View("Error");
              }
              string filter = TableQuery.CombineFilters(
                  TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                  TableOperators.And,
                  TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
              var query = new TableQuery<Subscriber>().Where(filter);
              var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
              if (subscriber == null)
              {
                  ViewBag.Message = "Ihr Abonnement wurde bereits abbestellt";
                  return View("Message");
              }
              var unsubscribeVM = new UnsubscribeVM();
              unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
              unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
              unsubscribeVM.SubscriberGUID = id;
              unsubscribeVM.Confirmed = null;
              return View(unsubscribeVM);
          }

    Hinweis: Die SubscriberGUID ist nicht in Partitions- oder Zeilenschlüssel enthalten, daher nimmt die Leistung dieser Abfrage mit zunehmender Partitionsgröße (Anzahl der E-Mail-Adressen in einer Mailingliste) ab. Weitere Informationen zu Verbesserung der Skalierbarkeit dieser Abfrage finden Sie im [ersten Lernprogramm in dieser Serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

    Die `HttpPost Index`-Methode verwendet erneut GUID und Listenname, um Abonnenteninformationen abzurufen, und füllt die Eigenschaften des Anzeigemodells aus. Falls die **Confirm-**-Schaltfläche angeklickt wurde, wird anschließend die Abonnentenzeile aus der Tabelle `MailingList` gelöscht. Falls die **Confirm-**-Schaltfläche angeklickt wurde, wird die `Confirm-`-Eigenschaft auf `Wahr` gesetzt, andernfalls wird die `Confirm-`-Eigenschaft auf `Falsch` gesetzt. Der Wert der `Confirm-`Eigenschaft teilt der Ansicht mit, ob die bestätigte oder abgebrochene Version der **Unsubscribe**-Seite angezeigt werden soll.

         [HttpPost] 
         [ValidateAntiForgeryToken]
         public ActionResult Index(string subscriberGUID, string listName, string action)
         {
             string filter = TableQuery.CombineFilters(
                 TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                 TableOperators.And,
                 TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
             var query = new TableQuery<Subscriber>().Where(filter);
             var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();

             var unsubscribeVM = new UnsubscribeVM();
             unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
             unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
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

1.  Erstellen Sie im **Projektmappen-Explorer** einen neuen Ordner unter dem Ordner *Views* im MVC-Projekt und nennen Sie den Ordner *Unsubscribe*.

2.  Klicken Sie mit der rechten Maustaste auf den neuen *Views\\Unsubscribe*-Ordner und wählen Sie **Vorhandenes Element hinzufügen**.

3.  Navigieren Sie zum Ordner, in dem Sie die heruntergeladene Beispielanwendung gespeichert haben, wählen Sie die Datei *Index.cshtml* im Ordner *Views\\Unsubscribe* aus und klicken Sie auf **Hinzufügen**.

4.  Öffnen Sie die Datei *Index.cshtml* und betrachten Sie den Code.

         @model MvcWebRole.Models.UnsubscribeVM
            
         @{
             ViewBag.Title = "Unsubscribe";
             Layout = null;
         }
            
         <h2>Email List Subscription Service</h2>
            
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
                         Möchten Sie @Html.DisplayFor(model => model.EmailAddress) aus der folgenden Liste löschen:  @Html.DisplayFor(model => model.ListDescription)

                    </p>
                     <br />
                     <p>
                         <input type="submit" value="Confirm" name="action"/> 
                            
                         <input type="submit" value="Cancel" name="action"/>
                     </p>
                 }
                 @if (Model.Confirmed == false) {
                     <p>
                         @Html.DisplayFor(model => model.EmailAddress)  wird NICHT gelöscht aus: @Html.DisplayFor(model => model.ListDescription).
                     </p>
                 }
                 @if (Model.Confirmed == true) {
                     <p>
                         @Html.DisplayFor(model => model.EmailAddress)  wurde gelöscht aus:  @Html.DisplayFor(model => model.ListDescription).
                     </p>
                 }
             </fieldset>
         }
            
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    Die Zeile `Layout = null` gibt an, dass die Datei \_Layout.cshtml nicht für die Anzeige dieser Seite verwendet werden soll. Die **Unsubscribe**-Seite enthält eine sehr einfache GUI ohne die Kopf- und Fußzeilen der Administratorseiten.

    Die `Confirmed`-Eigenschaft im Text der Seite bestimmt, was auf der Seite angezeigt wird: **Confirm**- und **Cancel**-Schaltflächen, falls die Eigenschaft null ist, Nachricht "Abbestellung bestätigt", falls die Eigenschaft wahr ist, Nachricht "Abbestellung abgebrochen", falls die Eigenschaft falsch ist.

### Testen der Anwendung

1.  Drücken Sie STRG+F5, um das Projekt auszuführen, und klicken Sie auf **Subscribers**.

2.  Klicken Sie auf **Create** und erstellen Sie einen neuen Abonnenten für eine Ihrer zuvor erstellten Mailinglisten.

    Lassen Sie das Browserfenster in der **Indexseite** für **Abonnenten** geöffnet.

3.  Öffnen Sie den Azure-Speicher-Explorer und wählen Sie Ihr Test-Speicherkonto aus.

4.  Klicken Sie auf **Tabellen** unter **Speichertyp**, wählen Sie die Tabelle **MailingList** aus und klicken Sie auf **Abfrage**.

5.  Doppelklicken Sie auf die neu hinzugefügte Abonnentenzeile.

    ![Azure-Speicher-Explorer](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-unsubscribe.png)

6.  Wählen Sie im Dialogfeld **Entität bearbeiten** den `SubscriberGUID`-Wert aus und kopieren Sie den Wert.

    ![Azure-Speicher-Explorer](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png)

7.  Wechseln Sie zurück zu Ihrem Browserfenster. Ändern Sie in der Adressleiste des Browser "Subscriber" in der URL zu "unsubscribe?ID=[GUID]&listName=[Listenname]", wobei [GUID] die aus dem Azure-Speicher-Explorer kopierte GUID ist und [Listenname] der Name der Mailingliste ist. Beispiel:

         http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

    Daraufhin wird die Version der **Unsubscribe** angezeigt, die zur Bestätigung auffordert:

    ![Abbestellen-Seite](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png)

8.  Wenn Sie auf **Confirm** klicken, wird eine Bestätigung für die Abbestellung der E-Mail-Adresse angezeigt.

    ![Abbestellung bestätigt-Seite](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png)

9.  Wechseln Sie zurück zur **Indexseite** für **Abonnenten** und vergewissern Sie sich, dass die Abonnentenzeile nicht mehr vorhanden ist.

Alternative Architektur(Optional) Erstellen Sie die alternative Architektur
---------------------------------------------------------------------------

Die folgenden Änderungen an den Anweisungen gelten für die alternative Architektur, d. h. für die Ausführung der Web-GUI in einer Azure-Website anstelle einer Azure-Cloud-Dienst-Webrolle.

-   Erstellen Sie bei der Projekterstellung zunächst das **ASP.NET MVC 4-Webanwendungs**-Projekt und fügen Sie die Projektmappe zu einem **Azure-Cloud-Dienst**-Projekt mit einer Workerrolle hinzu.

-   Speichern Sie die Azure-Verbindungszeichenfolge in der Datei Web.config anstatt in der Einstellungsdatei des Cloud-Dienstes. (Dies gilt nur für Azure-Websites. Falls Sie die Speicher-Verbindungszeichenfolge für eine Azure-Cloud-Dienst-Webrolle in der Datei Web.config speichern, erhalten Sie einen HTTP-Fehler 500.)

Fügen Sie eine neue Verbindungszeichenfolge mit dem Namen `StorageConnectionString` zur Datei *Web.config* hinzu, wie im folgenden Beispiel gezeigt:

           <connectionStrings>
              <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-MvcWebRole-20121010185535;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-MvcWebRole-20121010185535.mdf" providerName="System.Data.SqlClient" />
              <add name="StorageConnectionString" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[primarykey]" />
           </connectionStrings>

Abrufen der Werte für die Verbindungszeichenfolge aus dem [Azure-Verwaltungsportal](http://manage.windowsazure.com): Wählen Sie die Registerkarte **Speicher** und Ihr Speicherkonto aus und klicken Sie im unteren Bereich der Seite auf **Schlüssel verwalten**.

-   Ersetzen Sie alle Vorkommnisse von `RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString")` im Code durch `ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString`.

Nächste SchritteNächste Schritte
--------------------------------

Wie bereits im [ersten Lernprogramm dieser Serie](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/) beschrieben, wird die Erstellung des Abonnementprozesses in diesem Lernprogramm erst dann behandelt, wenn ein gemeinsamer geheimer Schlüssel zur Sicherung der ASP.NET Web-API-Dienstmethode implementiert ist. Sie können die Dienstmethode jedoch auch mit IP-Einschränkungen schützen. Anschließend können Sie Abonnementfunktion mit den folgenden Dateien aus dem heruntergeladenen Projekt hinzufügen.

Für die ASP.NET Web-API-Dienstmethode:

-   Controllers\\SubscribeAPI.cs

Für die Webseite, die Abonnenten angezeigt wird, nachdem Sie auf den **Bestätigungs**-Link in der von der Dienstmethode generierten E-Mail klicken:

-   Models\\SubscribeVM.cs
-   Controllers\\SubscribeController.cs
-   Views\\Subscribe\\Index.cshtml

Im [nächsten Lernprogramm](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/) werden Sie die Workerrolle A konfigurieren und programmieren, die für die Planung von E-Mails verantwortlich ist.

Links zu weiteren Ressourcen für die Arbeit mit Azure-Speichertabellen, Warteschlangen und Blobs finden Sie am Ende des [letzten Lernprogramms in dieser Serie](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).
[Lernprogramm 4](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/)

