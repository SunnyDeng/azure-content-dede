<properties
   pageTitle="Vorgehensweise: Migrieren und Veröffentlichen einer Webanwendung in einem Azure-Clouddienst aus Visual Studio | Microsoft Azure"
   description="Sie erfahren, wie Sie Ihre Webanwendung mit Visual Studio zu einem Azure-Clouddienst migrieren und veröffentlichen."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="01/05/2016"
   ms.author="tarcher" />

# Vorgehensweise: Migrieren und Veröffentlichen einer Webanwendung in einem Azure-Clouddienst aus Visual Studio

Um die Vorteile der Hostingdienste und der Skalierbarkeit von Azure zu nutzen, kann es ratsam sein, Ihre Webanwendung zu einem Azure-Clouddienst zu migrieren und dort zu veröffentlichen. Sie können eine Webanwendung in Azure mit minimalen Änderungen an der vorhandenen Anwendung ausführen.

>[AZURE.NOTE]In diesem Thema wird die Bereitstellung in Clouddiensten und nicht auf Websites behandelt. Weitere Informationen zum Bereitstellen auf Websites finden Sie unter [Bereitstellen von Web-Apps in Azure App Service](web-sites-deploy.md).

Eine Liste der spezifischen Vorlagen, die sowohl für Visual C# als auch für Visual Basic unterstützt werden, finden Sie weiter unten in diesem Thema im Abschnitt **Unterstützte Projektvorlagen**.

Zuerst müssen Sie Ihre Webanwendung für Azure über Visual Studio aktivieren. Die folgende Abbildung zeigt die wichtigsten Schritte zum Veröffentlichen Ihrer vorhandenen Webanwendung, indem Sie für die Bereitstellung ein Azure-Projekt hinzufügen. Bei diesem Prozess wird Ihrer Projektmappe ein Azure-Projekt mit der erforderlichen Webrolle hinzugefügt. Je nach Typ Ihres Webprojekts werden auch die Projekteigenschaften für Assemblys aktualisiert, wenn das Dienstpaket zusätzliche Assemblys für die Bereitstellung erfordert.

![Veröffentlichen einer Webanwendung in Microsoft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

>[AZURE.NOTE]Der Befehl **Konvertieren**, **In Azure-Clouddienstprojekt konvertieren** wird nur für das Webprojekt in Ihrer Projektmappe angezeigt. Beispielsweise ist der Befehl nicht für ein Silverlight-Projekt in Ihrer Projektmappe verfügbar. Wenn Sie ein Dienstpaket erstellen oder Ihre Anwendung in Azure veröffentlichen, können Warnungen oder Fehler auftreten. Diese Warnungen und Fehler sind hilfreich zum Beheben von Problemen, bevor Sie die Bereitstellung unter Azure durchführen. Beispielsweise können Sie eine Warnung zu einer fehlenden Assembly erhalten. Weitere Informationen dazu, wie Sie alle Warnungen wie Fehler behandeln, finden Sie unter [Konfigurieren eines Azure-Clouddienstprojekts mit Visual Studio](vs-azure-tools-configuring-an-azure-project.md). Wenn Sie Ihre Anwendung erstellen, lokal mit dem Serveremulator ausführen oder unter Azure veröffentlichen, wird im Fenster **Fehlerliste** ggf. der folgende Fehler angezeigt: **Der angegebene Pfad oder Dateiname bzw. beide sind zu lang.** Dieser Fehler tritt auf, da der vollqualifizierte Name des Azure-Projekts zu lang ist. Der Projektname, einschließlich des vollständigen Pfads, darf nicht mehr als 146 Zeichen lang sein. Dies ist beispielsweise der vollständige Projektname mit dem Dateinamen für ein Azure-Projekt, das für eine Silverlight-Anwendung erstellt wurde: `c:\users<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Unter Umständen müssen Sie die Projektmappe in ein anderes Verzeichnis verschieben, das einen kürzeren Pfad hat, um die Länge des vollqualifizierten Projektnamens zu verringern.

Führen Sie die unten angegebenen Schritte aus, um eine Webanwendung aus Visual Studio zu Azure zu migrieren und dort zu veröffentlichen.

## Aktivieren einer Webanwendung für die Bereitstellung in Azure

### So aktivieren Sie eine Webanwendung für die Bereitstellung in Azure

1. Um Ihre Webanwendung für die Bereitstellung in Azure zu aktivieren, öffnen Sie das Kontextmenü für ein Webprojekt in Ihrer Projektmappe und wählen die Option "Azure-Bereitstellungsobjekt hinzufügen" aus.

    Die folgenden Aktionen werden ausgeführt:

    - Der Projektmappe für Ihre Anwendung wird ein Azure-Projekt mit dem Namen `<name of the web project>.Azure` hinzugefügt.

    - Eine Webrolle für das Webprojekt wird diesem Azure-Projekt hinzugefügt.

    - Die **Copy Local**-Eigenschaft wird für alle Assemblys, die für MVC 2-, MVC 3-, MVC 4- und Silverlight-Geschäftsanwendungen erforderlich sind, auf "true" festgelegt. So werden diese Assemblys dem Dienstpaket hinzugefügt, das für die Bereitstellung verwendet wird.

  >[AZURE.IMPORTANT]Wenn Sie über andere Assemblys oder Dateien verfügen, die für diese Webanwendung benötigt werden, müssen Sie die Eigenschaften für diese Dateien manuell festlegen. Informationen zum Festlegen dieser Eigenschaften finden Sie im Abschnitt **Einschließen von Dateien in das Dienstpaket** weiter unten in diesem Artikel.  


  >[AZURE.NOTE]Wenn eine Webrolle für ein bestimmtes Webprojekt in einem Azure-Projekt der Lösung bereits vorhanden ist, wird **Konvertieren**, **In Azure-Clouddienstprojekt konvertieren** im Kontextmenü für dieses Webprojekt nicht angezeigt.


  Falls Ihre Webanwendung mehrere Webprojekte umfasst und Sie Webrollen für jedes Webprojekt erstellen möchten, müssen Sie die Schritte in diesem Verfahren für jedes Webprojekt ausführen. Hierbei werden für jede Webrolle separate Azure-Projekte erstellt. Jedes Webprojekt kann separat veröffentlicht werden. Alternativ dazu können Sie einem vorhandenen Azure-Projekt in der Webanwendung eine weitere Webrolle hinzufügen. Öffnen Sie hierzu das Kontextmenü für den Ordner **Rollen** in Ihrem Azure-Projekt, wählen Sie **Hinzufügen** und **Webrollenprojekt in Lösung** und dann das Projekt aus, das als Webrolle hinzugefügt werden soll. Klicken Sie anschließend auf die Schaltfläche **OK**.

## Verwenden einer Azure SQL-Datenbank für die Anwendung

Wenn Sie eine Verbindungszeichenfolge für Ihre Webanwendung nutzen, für die eine lokale SQL Server-Datenbank verwendet wird, müssen Sie diese Verbindungszeichenfolge ändern, damit stattdessen eine von Azure gehostete Instanz der SQL-Datenbank verwendet wird.

>[AZURE.IMPORTANT]Sie müssen im Rahmen Ihres Abonnements zur Verwendung der SQL-Datenbank berechtigt sein. Wenn Sie auf Ihr Abonnement über das Azure-Verwaltungsportal zugreifen, können Sie bestimmen, welche Services Ihr Abonnement umfasst. Die folgenden Anweisungen gelten für das veröffentlichte Verwaltungsportal. Springen Sie zum nächsten Verfahren, wenn Sie die Preview-Version des Verwaltungsportals verwenden.|

### So verwenden Sie eine SQL-Datenbankinstanz in der Webrolle für Ihre Verbindungszeichenfolge

1. Führen Sie die Schritte im folgenden Artikel aus, um eine Instanz von SQL-Datenbank im Azure-Verwaltungsportal zu erstellen: [Erstellen eines SQL-Datenbank-Servers](http://go.microsoft.com/fwlink/?LinkId=225109).

    >[AZURE.NOTE]Wenn Sie die Firewallregeln für die Instanz von SQL-Datenbank einrichten, müssen Sie das Kontrollkästchen **Anderen Azure-Diensten Zugriff auf diesen Server gewähren** aktivieren.

1. Führen Sie die Schritte im nächsten Abschnitt des folgenden Artikels aus, um eine Instanz von SQL-Datenbank für Ihre Verbindungszeichenfolge zu erstellen: [Erstellen einer SQL-Datenbank](http://go.microsoft.com/fwlink/?LinkId=225110).

1. Führen Sie die unten angegebenen Schritte im Verwaltungsportal für Azure aus, um die ADO.NET-Verbindungszeichenfolge zu kopieren, die Sie als Ihre Verbindungszeichenfolge verwenden möchten.

  1. Wählen Sie die Schaltfläche **Datenbank** aus, und öffnen Sie den Knoten für das Abonnement, das Sie zum Erstellen Ihrer Instanz von SQL-Datenbank verwendet haben.

  1. Um die verfügbaren Instanzen von SQL-Datenbank anzuzeigen, wählen Sie den Knoten **SQL-Datenbanken** aus.

  1. Wählen Sie die Datenbank aus, um die Eigenschaften für die Datenbank anzuzeigen. Die Ansicht **Eigenschaften** wird angezeigt.

      >[AZURE.NOTE]Wenn die Ansicht **Eigenschaften** nicht angezeigt wird, müssen Sie sie ggf. mithilfe des Trennlinienelements öffnen.

  1. Wählen Sie die Schaltfläche mit den Auslassungspunkten (...) neben „Ansicht“, um die Verbindungszeichenfolgen anzuzeigen.

    Das Dialogfeld **Verbindungszeichenfolgen** wird angezeigt.

  1. Markieren Sie den Text, und drücken Sie STRG+C, um die ADO.NET-Verbindungszeichenfolge zu kopieren.

  1. Wählen Sie die Schaltfläche **Schließen** aus, um das Dialogfeld zu schließen.

1. Gehen Sie wie folgt vor, um die Verbindungszeichenfolge in der Datei „web.config“ zu ersetzen, damit diese Instanz von SQL-Datenbank verwendet wird: Öffnen Sie die Datei „web.config“, markieren Sie den vorhandenen Eintrag der Verbindungszeichenfolge, und drücken Sie STRG+V. Die vorhandene Verbindungszeichenfolge wird durch die ADO.NET-Verbindungszeichenfolge für die Instanz von SQL-Datenbank ersetzt.

1. Außerdem müssen Sie der Verbindungszeichenfolge den Parameter `MultipleActiveResultSets=True` hinzufügen. Die Verbindungszeichenfolge sollte das folgende Format haben:

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```

1. (Optional) Eine alternative Methode zum Ändern der Verbindungszeichenfolge direkt in der Datei „web.config“ ist das Hinzufügen eines Abschnitt in einer der web.config-Transformationsdateien. Dies hängt von der Buildkonfiguration ab, die Sie zum Erstellen des Dienstpakets verwenden. Öffnen Sie entweder die Datei „Web.Debug.Config“ oder die Datei „Web.Release.Config“. Fügen Sie den folgenden Abschnitt in diese Datei ein:

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```

1. Speichern Sie die Datei, die Sie geändert haben, und veröffentlichen Sie die Anwendung erneut.

### So verwenden Sie eine Instanz von SQL-Datenbank mithilfe des Azure-Verwaltungsportals

1. Wählen Sie im [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=213885) den Knoten "SQL-Datenbanken" aus.

  - Wenn die Instanz von SQL-Datenbank angezeigt wird, die Sie verwenden möchten, öffnen Sie sie.

  - Wenn Sie keine Instanzen erstellt haben, wählen Sie den entsprechenden Link aus, und erstellen Sie dann eine Instanz.

1. Wählen Sie nach dem Öffnen oder Erstellen einer Datenbankinstanz den Link **Verbindungszeichenfolgen** aus.

1. Wählen Sie unten auf der Seite den Link zum Konfigurieren der Firewalleinstellungen, und übernehmen Sie die Standardwerte, oder konfigurieren Sie die Werte nach Bedarf.

1. Kopieren Sie die ADO.NET-Verbindungszeichenfolge, fügen Sie sie in der Datei "web.config" anstelle der alten Verbindungszeichenfolge für die lokale Datenbank ein, und vergessen Sie nicht, `MultipleActiveResultSets=True` hinzuzufügen.

## Veröffentlichen einer Webanwendung in Azure

### So veröffentlichen Sie eine Webanwendung in Azure

1. Öffnen Sie zum Testen der Anwendung in der lokalen Entwicklungsumgebung mit dem Azure-Serveremulator das Kontextmenü für die Webrolle des Azure-Projekts, und wählen Sie die Option **Als Startprojekt festlegen** aus. Wählen Sie dann **Debuggen**, **Debuggen starten** aus (Tastatur: **F5**).

    Das Dialogfeld **Starten der Azure-Debugumgebung** wird geöffnet, und die Anwendung wird im Browser gestartet. Spezielle Details zum Starten der einzelnen Typen von Webanwendungen im Serveremulator finden Sie in der Tabelle in diesem Abschnitt.

1. Zum Einrichten der Dienste für Ihre Anwendung zur Veröffentlichung in Azure müssen Sie über ein Microsoft-Konto und ein Azure-Abonnement verfügen. Richten Sie mit den Schritten im folgenden Thema Ihre Dienste ein: [Veröffentlichen und Bereitstellen einer Azure-Anwendung in Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Um die Webanwendung in Azure zu veröffentlichen, öffnen Sie das Kontextmenü für das Webprojekt und wählen **In Azure veröffentlichen** aus.

    Das Dialogfeld **Azure-Anwendung veröffentlichen** wird geöffnet, und Visual Studio startet den Bereitstellungsprozess. Weitere Informationen zum Veröffentlichen der Anwendung finden Sie im Abschnitt **Veröffentlichen einer Azure-Anwendung aus Visual Studio** unter [Veröffentlichen eines Clouddiensts mit Azure Tools](vs-azure-tools-publishing-a-cloud-service.md).

    >[AZURE.NOTE]Sie können die Webanwendung auch über das Azure-Projekt veröffentlichen. Öffnen Sie hierzu das Kontextmenü für das Azure-Projekt, und klicken Sie auf **Veröffentlichen**.

1. Sie können das Fenster mit dem **Azure-Aktivitätsprotokoll** anzeigen, um den Status der Bereitstellung zu verfolgen. Dieses Protokoll wird automatisch angezeigt, wenn der Bereitstellungsprozess gestartet wird. Sie können Positionen im Aktivitätsprotokoll erweitern, um ausführliche Informationen anzuzeigen. Dies ist in der folgenden Abbildung dargestellt:

    ![VST\_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Optional) Um den Bereitstellungsprozess abzubrechen, öffnen Sie das Kontextmenü für die Position im Aktivitätsprotokoll und wählen **Abbrechen und entfernen** aus. Der Bereitstellungsprozess wird beendet, und die Bereitstellungsumgebung wird aus Azure gelöscht.

    >[AZURE.NOTE]Sie müssen Sie das Azure-Verwaltungsportal verwenden, um diese Umgebung nach der Bereitstellung zu entfernen.

1. (Optional) Nach dem Starten Ihrer Rolleninstanzen wird die Bereitstellungsumgebung in Visual Studio im **Cloud Explorer** oder **Server-Explorer** automatisch unter dem Knoten **Azure Compute** angezeigt. Hier können Sie den Status der einzelnen Rolleninstanzen anzeigen.

    Die folgende Abbildung zeigt die Rolleninstanzen im **Server-Explorer**, während diese sich noch im Status "Wird initialisiert" befinden:

    ![VST\_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)

1. Um nach der Bereitstellung auf Ihre Anwendung zuzugreifen, wählen Sie den Pfeil neben Ihrer Bereitstellung aus, wenn der Status **Abgeschlossen** im **Azure-Aktivitätsprotokoll** angezeigt wird. Die URL für Ihre Webanwendung in Azure wird angezeigt. Die folgende Tabelle enthält die Details zum Starten eines bestimmten Typs von Webanwendung unter Azure.

    In der folgenden Tabelle sind die Details zum Starten bestimmter Webanwendungen unter Azure bzw. zum lokalen Ausführen oder Debuggen einer Webanwendung mit dem Azure-Serveremulator aufgeführt:

    |Webanwendungstyp|Lokales Ausführen/Debuggen per Serveremulator|Ausführen in Azure|
    |---|---|---|
    |ASP.NET-Webanwendung|Wählen Sie in der Menüleiste die Option **Debuggen**, **Debuggen starten** aus (Tastatur: **F5**).|Wählen Sie den URL-Hyperlink aus, der auf der Registerkarte **Bereitstellung** für das **Azure-Aktivitätsprotokoll** angezeigt wird, um die Startseite in den Browser zu laden.|
    |ASP.NET MVC 2-Webanwendung|Wählen Sie in der Menüleiste die Option **Debuggen**, **Debuggen starten** aus (Tastatur: **F5**).|Wählen Sie den URL-Hyperlink aus, der auf der Registerkarte **Bereitstellung** für das **Azure-Aktivitätsprotokoll** angezeigt wird, um die Startseite in den Browser zu laden.|
    |ASP.NET MVC 3-Webanwendung|Wählen Sie in der Menüleiste die Option **Debuggen**, **Debuggen starten** aus (Tastatur: **F5**).|Wählen Sie den URL-Hyperlink aus, der auf der Registerkarte **Bereitstellung** für das **Azure-Aktivitätsprotokoll** angezeigt wird, um die Startseite in den Browser zu laden.|
    |ASP.NET MVC 4-Webanwendung|Wählen Sie in der Menüleiste die Option **Debuggen**, **Debuggen starten** aus (Tastatur: **F5**).|Wählen Sie den URL-Hyperlink aus, der auf der Registerkarte **Bereitstellung** für das **Azure-Aktivitätsprotokoll** angezeigt wird, um die Startseite in den Browser zu laden.|
    |Leere ASP.NET-Webanwendung|Sie müssen in Ihrer Anwendung eine ASPX-Seite hinzufügen, die Sie als Startseite für Ihr Webprojekt festlegen. Wählen Sie dann in der Menüleiste die Option **Debuggen**, **Debuggen starten** aus (Tastatur: **F5**).|Wenn Sie eine standardmäßige ASPX-Seite in der Anwendung verwenden, wählen Sie den URL-Hyperlink aus, der auf der Registerkarte **Bereitstellung** für das **Azure-Aktivitätsprotokoll** angezeigt wird. Diese Seite wird dann in den Browser geladen. Wenn Sie eine andere ASPX-Seite verwenden, müssen Sie zu dieser speziellen Seite navigieren, indem Sie für Ihre URL das folgende Format verwenden: `<url for deployment>/<name of page>.aspx`|
    |Silverlight-Anwendung|Wählen Sie in der Menüleiste die Option **Debuggen**, **Debuggen starten** aus (Tastatur: **F5**).|Sie müssen zur speziellen Seite für Ihre Anwendung navigieren, indem Sie das folgende Format für Ihre URL verwenden: `<url for deployment>/<name of page>.aspx`|
    |Silverlight-Geschäftsanwendung|Wählen Sie in der Menüleiste die Option **Debuggen**, **Debuggen starten** aus (Tastatur: **F5**).|Sie müssen zur speziellen Seite für Ihre Anwendung navigieren, indem Sie das folgende Format für Ihre URL verwenden: `<url for deployment>/<name of page>.aspx`|
    |Silverlight-Navigationsanwendung|Wählen Sie in der Menüleiste die Option **Debuggen**, **Debuggen starten** aus (Tastatur: **F5**).|Sie müssen zur speziellen Seite für Ihre Anwendung navigieren, indem Sie das folgende Format für Ihre URL verwenden: `<url for deployment>/<name of page>.aspx`|
    |WCF-Dienstanwendung|Sie müssen die SVC-Datei als Startseite für Ihr WCF-Dienstprojekt festlegen. Wählen Sie dann in der Menüleiste die Option **Debuggen**, **Debuggen starten** aus (Tastatur: **F5**).|Sie müssen zur SVC-Datei für Ihre Anwendung navigieren, indem Sie das folgende Format für Ihre URL verwenden: `<url for deployment>/<name of service file>.svc`|
    |Dienstanwendung für WCF-Workflows|Sie müssen die SVC-Datei als Startseite für Ihr WCF-Dienstprojekt festlegen. Wählen Sie dann in der Menüleiste die Option **Debuggen**, **Debuggen starten** aus (Tastatur: **F5**).|Sie müssen zur SVC-Datei für Ihre Anwendung navigieren, indem Sie das folgende Format für Ihre URL verwenden: `<url for deployment>/<name of service file>.svc`|
    |ASP.NET Dynamic Entities|Wählen Sie in der Menüleiste die Option **Debuggen**, **Debuggen starten** aus (Tastatur: **F5**).|Sie müssen die Verbindungszeichenfolge aktualisieren (siehe nächsten Abschnitt). Sie müssen auch zur speziellen Seite für Ihre Anwendung navigieren, indem Sie das folgende Format für Ihre URL verwenden: `<url for deployment>/<name of page>.aspx`|
    |ASP.NET Dynamic Data-LINQ to SQL|Wählen Sie in der Menüleiste die Option **Debuggen**, **Debuggen starten** aus (Tastatur: **F5**).|Führen Sie die Schritte in diesem Verfahren aus: Verwenden Sie eine SQL Azure-Datenbank für Ihre Anwendung (siehe Abschnitt weiter oben in diesem Thema). Sie müssen auch zur speziellen Seite für Ihre Anwendung navigieren, indem Sie das folgende Format für Ihre URL verwenden: `<url for deployment>/<name of page>.aspx`|

## Aktualisieren einer Verbindungszeichenfolge für ASP.NET Dynamic Entities

### So aktualisieren Sie eine Verbindungszeichenfolge für ASP.NET Dynamic Entities

1. Führen Sie die Schritte des Verfahrens unter **Verwenden einer SQL Azure-Datenbank für Ihre Anwendung** weiter oben in diesem Thema aus, um eine SQL Azure-Datenbank zu erstellen, die für eine ASP.NET Dynamic Entities-Webanwendung verwendet werden kann.

1. Fügen Sie die Tabellen und Felder, die Sie für diese Datenbank benötigen, über das Azure-Verwaltungsportal hinzu.

1. Die Verbindungszeichenfolge für diese Art von Anwendung hat in der Datei „web.config“ das folgende Format:

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=";data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework";"providerName="System.Data.EntityClient"/>
    ```

    Aktualisieren Sie den Wert *connectionString* mit der ADO.NET-Verbindungszeichenfolge wie folgt für Ihre SQL Azure-Datenbank:

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=";Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework";"providerName="System.Data.EntityClient"/>
    ```

1. Wählen Sie in der Menüleiste **Datei** und dann **web.config speichern** aus, um die Datei "web.config" mit den Änderungen zu speichern, die Sie an der Verbindungszeichenfolge vorgenommen haben.

## Unterstützte Projektvorlagen

Zum Veröffentlichen einer Webanwendung unter Azure muss für die Anwendung eine der Projektvorlagen für C# oder Visual Basic verwendet werden, die unten in der Tabelle enthalten ist.

|Projektvorlagengruppe|Projektvorlage|
|---|---|
|Web|ASP.NET-Webanwendung|
|Web|ASP.NET MVC 2-Webanwendung|
|Web|ASP.NET MVC 3-Webanwendung|
|Web|ASP.NET MVC 4-Webanwendung|
|Web|Leere ASP.NET-Webanwendung|
|Web|Leere ASP.NET MVC 2-Webanwendung|
|Web|Webanwendung für ASP.NET Dynamic Data Entities|
|Web|Webanwendung für ASP.NET Dynamic Data-LINQ to SQL|
|Silverlight|Silverlight-Anwendung|
|Silverlight|Silverlight-Geschäftsanwendung|
|Silverlight|Silverlight-Navigationsanwendung|
|WCF|WCF-Dienstanwendung|
|WCF|Dienstanwendung für WCF-Workflows|
|Workflow|Dienstanwendung für WCF-Workflows|

## Nächste Schritte
Weitere Informationen zur Veröffentlichung finden Sie unter [Veröffentlichen und Bereitstellen einer Azure-Anwendung in Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Sehen Sie sich auch [Einrichten benannter Authentifizierungsanmeldeinformationen](vs-azure-tools-setting-up-named-authentication-credentials.md) an.

<!---HONumber=AcomDC_0107_2016-->
