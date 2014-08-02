<properties linkid="manage-services-how-to-configure-websites" urlDisplayName="How to configure" pageTitle="How to configure web sites - Azure service management" metaKeywords="Azure websites, configuring Azure websites, Azure SQL database, Azure MySQL" description="Learn how to configure web sites in Azure, including how to configure a web site to use a SQL Database or MySQL database." metaCanonical="" services="web-sites" documentationCenter="" title="How to Configure Web Sites" authors="timamm" solutions="" manager="" editor="mollybos" />

Konfigurieren von Websites
==========================

Im Azure-Verwaltungsportal können Sie die Konfigurationsoptionen für Websites ändern und eine Website mit anderen Azure-Ressourcen verknüpfen. Beispielsweise können Sie Websites mit einer SQL-Datenbank verknüpfen, um zusätzliche Funktionen bereitzustellen. Außerdem können Sie Websites zur Verwendung einer neuen oder vorhandenen MySQL-Datenbank konfigurieren.

Inhaltsverzeichnis
------------------

-   [Vorgehensweise: Ändern der Konfigurationsoptionen für eine Website](#howtochangeconfig)
-   [Vorgehensweise: Konfigurieren einer Website zur Verwendung einer SQL-Datenbank](#howtoconfigSQL)
-   [Vorgehensweise: Konfigurieren einer Website zur Verwendung einer MySQL-Datenbank](#howtoconfigMySQL)
-   [Vorgehensweise: Konfigurieren eines benutzerdefinierten Domänennamens](#howtodomain)
-   [Vorgehensweise: Konfigurieren einer Website zur Verwendung von SSL](#howtoconfigSSL)
-   [Nächste Schritte](#next)

Vorgehensweise: Ändern der Konfigurationsoptionen für eine Website
------------------------------------------------------------------

Befolgen Sie diese Schritte, um die Konfigurationsoptionen für eine Website zu ändern.

1.  Öffnen Sie im Verwaltungsportal die Verwaltungsseiten der Website.
2.  Klicken Sie auf **Configure**, um die Verwaltungsseite **Configure** zu öffnen.
3.  Legen Sie die folgenden Konfigurationsoptionen für die Website nach Bedarf fest:
    -   **general**
        -   **.NET Framework Version** - Falls Ihre Webanwendung .NET Framework verwendet, legen Sie die Framework-Version fest, die für die Webanwendung erforderlich ist.
        -   **PHP Version** - Falls Ihre Webanwendung PHP verwendet, legen Sie die PHP-Version fest, die für die Webanwendung erforderlich ist.
        -   **Java Version** - Wählen Sie die angezeigte Java-Version aus, um sie für Ihre Webanwendung zu aktivieren, oder verwenden Sie **OFF**, um Java zu deaktivieren. Wenn Sie Java für Ihre Webanwendung aktivieren, können Sie mit der Option **Web Container** zwischen Tomcat und Jetty auswählen.

            **Hinweis**: Aus technischen Gründen werden durch Aktivierung von Java für Ihre Website die Optionen für .NET, PHP und Python deaktiviert.

        -   **Python Version** - Zeigt die Python-Version an (nicht konfigurierbar).
        -   **Managed Pipeline Mode** - Die beiden Wahlmöglichkeiten sind **Classic** und **Integrated**, wobei Integrated der Standard ist. Verwenden Sie die Classic-Option nur, wenn Sie über ältere Websites verfügen, die nur auf älteren Versionen von IIS ausgeführt werden können.
        -   **Platform** - Für Websites im Standardmodus können Sie festlegen, ob die Anwendung in einer 32-Bit- oder 64-Bit-Umgebung ausgeführt werden soll. Websites im Free- und Shared-Modus werden immer in einer 32-Bit-Umgebung ausgeführt.
        -   **Web Sockets** - Verwenden Sie **On**, um auf der Website Echtzeit-Anfragemusteranwendungen wie Chats zu ermöglichen.
        -   **Always On** - Standardmäßig werden Websites entladen, wenn sie einige Zeit im Leerlauf waren. Dadurch spart das System Ressourcen. Sie können die Einstellung **Always On** im Standardmodus aktivieren, wenn die Website immer geladen bleiben soll. Da fortlaufende Webjobs nicht zuverlässig ausgeführt werden, wenn **Always On** deaktiviert ist, sollten Sie **Always On** aktivieren, wenn Sie fortlaufende Webjobs auf der Website ausführen.
        -   **Edit in Visual Studio Online** - Wenn Sie den Code direkt mit Visual Studio Online bearbeiten möchten, setzen Sie diese Option auf **On**. Nach dem Speichern dieser Konfigurationsänderung wird auf der Registerkarte "Dashboard" im Abschnitt **Quick Glance** der Link **Edit in Visual Studio Online** angezeigt. Klicken Sie auf diesen Link, um Ihre Website direkt online zu bearbeiten. Falls Sie sich authentifizieren müssen, können Sie Ihre Bereitstellungsanmeldung verwenden.

            **Hinweis**: Falls Sie die Bereitstellung über die Quellcodeverwaltung aktiviert haben, kann eine Bereitstellung die Änderungen, die Sie im Visual Studio Online-Editor vornehmen, überschreiben. Verwenden Sie daher nicht die Option "deployment from source control", wenn Sie den Inhalt der Website direkt mit Visual Studio Online bearbeiten möchten.

    -   **certificates** - Im Standardmodus können Sie auf **upload** klicken, um ein SSL-Zertifikat für eine benutzerdefinierte Domäne hochzuladen. Die von Ihnen hochgeladenen Zertifikate werden hier aufgelistet. Platzhalterzertifikate (mit einem Sternchen markierte Zertifikate) werden unterstützt. Nachdem Sie ein Zertifikat hochgeladen haben, können Sie es einer beliebigen Website in Ihrem Abonnement bzw. an Ihrem Standort zuweisen. Ein Zertifikat mit Sternchen muss nur einmal hochgeladen werden, kann jedoch für jede Website innerhalb der Domäne, für die es gültig ist, verwendet werden. Ein Zertifikat kann gelöscht werden, wenn keine Verbindungen mit einer Website mehr für das Zertifikat aktiv sind.
        **Hinweis:** Benutzerdefinierte Domänen sind nur im Shared- oder Standardmodus verfügbar, und SSL für benutzerdefinierte Domänen wird nur im Standardmodus unterstützt. Informationen zur Konfiguration von SSL für eine benutzerdefinierte Domäne auf Azure finden Sie unter [Aktivieren von HTTPS für eine Azure-Website](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/).
    -   **domain names** - Hier können Sie zusätzliche Domänennamen für eine Website anzeigen oder hinzufügen. Sie können benutzerdefinierte Domänen hinzufügen, indem Sie auf **Manage Domains** klicken. Benutzerdefinierte Domänen sind nur im Modus **Shared** und **Standard** verfügbar. Sie können den Website-Modus auf der Verwaltungsseite **Scale** ändern. Benutzerdefinierte Domänen sind im Modus "Free" nicht verfügbar Weitere Informationen zum Konfigurieren von benutzerdefinierten Domänen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website](http://www.windowsazure.com/en-us/documentation/articles/web-sites-custom-domain-name/).
    -   **SSL Bindings** - SSL-Bindungen für benutzerdefinierte Domänen sind nur im Standardmodus verfügbar. Wählen Sie einen SSL-Modus (**SNI**, **IP** oder **No SSL**) für einen bestimmten Domänennamen aus. Bei Auswahl von SNI oder IP können Sie ein Zertifikat für die Domäne aus den hochgeladenen Zertifikaten angeben. Informationen zur Konfiguration von SSL für eine benutzerdefinierte Domäne auf Azure finden Sie unter [Aktivieren von HTTPS für eine Azure-Website](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/). Weitere Informationen zu SNI finden Sie unter [Servernamensanzeige](http://en.wikipedia.org/wiki/Server_Name_Indication).
    -   **deployments** - Verwenden Sie diese Einstellungen zum Konfigurieren der Bereitstellungen.
        -   **Git URL** - Falls Sie ein Git-Repository für Ihre Azure-Website erstellt haben, ist dies die URL - das Verzeichnis, in den Sie den Inhalt übertragen.
        -   **Deployment Trigger URL** - Diese URL kann für ein GitHub, CodePlex, Bitbucket oder ein anderes Repository festgelegt werden, um die Bereitstellung auszulösen, wenn eine Übergabe an das Repository erfolgt.
        -   **Branch to Deploy** - Hier können Sie die Verzweigung angeben, die bereitgestellt wird, wenn Sie den Inhalt übertragen.
    -   **application diagnostics** - Legen Sie die Optionen zum Erfassen von Diagnoseablaufverfolgungen aus einer Webanwendung fest, deren Code mit Ablaufverfolgungen instrumentiert wurde. Zu den Protokolloptionen für Anwendungsdiagnosen gehören:
        -   **Application Logging (File System)** - Verwenden Sie **On**, um die Anwendungsprotokolle in das Dateisystem der Website zu schreiben. Bei Aktivierung dauert die Dateisystemprotokollierung 12 Stunden an. Sie können die Protokolle über die FTP-Freigabe der Website abrufen. Den Link zur FTP-Freigabe finden Sie unter **Dashboard**. Wählen Sie unter **Quick Glance** die Option **FTP Diagnostic Logs** oder **FTPS Diagnostic Logs** aus.
        -   **Application Logging (Storage)** - Verwenden Sie **On**, um die Anwendungsprotokolle in ein Azure-Speicherkonto zu schreiben. Die Protokollierung in einem Speicherkonto weist kein Zeitlimit auf und bleibt aktiviert, bis Sie die Option deaktivieren. Standardmäßig werden die Protokolle in der Tabelle "WAWSAppLogTable" gespeichert.
        -   **Logging Level** - Wenn Protokollierung aktiviert ist, bestimmt diese Option, ob Fehler, Warnmeldungen, Infos oder ausführliche Informationen protokolliert werden.
        -   **Diagnostic Storage** - Wenn Sie auf **Manage Connection** klicken, wird das Dialogfeld **Manage diagnostic storage** mit den folgenden Speicheroptionen für Protokolle im Azure-Speicherkonto angezeigt:
            -   **Storage Account Name** - Wählen Sie das Speicherkonto aus, in dem die Protokolle gespeichert werden sollen.
            -   **Storage Access Key** - Zeigt den Schlüssel für das ausgewählte Speicherkonto an. Falls Sie den Schlüssel für das Speicherkonto neu generiert haben, geben Sie den Schlüssel hier manuell ein, oder verwenden Sie eine der **Synchronize**-Schaltflächen. Die Synchronisierungsschaltflächen sind nur verfügbar, wenn der derzeit angemeldete Benutzer Zugriff auf das ausgewählte Speicherkonto hat.
            -   **Synchronize Primary Key** - Ruft den aktuellen Primärschlüssel Ihres Azure-Speicherkontos ab.
            -   **Synchronize Secondary Key** - Ruft den aktuellen Sekundärschlüssel Ihres Azure-Speicherkontos ab.
                **Hinweis:** Weitere Informationen zu Azure-Speicherzugriffsschlüsseln finden Sie unter [Vorgehensweise: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](http://www.windowsazure.com/en-us/documentation/articles/storage-manage-storage-account/#regeneratestoragekeys).
    -   **site diagnostics** - Legen Sie Optionen zum Erfassen von Diagnoseinformationen für Ihre Website fest, darunter:
        -   **Web Server Logging** - Geben Sie an, ob Webserverprotokollierung für die Website aktiviert werden soll. Webserverprotokolle werden im erweiterten W3C-Protokolldateiformat gespeichert. Sie können die Protokolle im Azure-Speicher oder im Dateisystem speichern.
            **Tipp**: Die maximale Größe des Protokollspeichers im Dateisystem ist 100 Megabytes. Wenn Sie mehr Aufzeichnungen beibehalten wollen, verwenden Sie den Azure-Speicher, der eine sehr viel größere Speicherkapazität hat.
            -   Um die Webserverprotokolle in einem Azure-Speicherkonto zu speichern, wählen Sie **Storage** und dann **manage storage**. Verwenden Sie im Dialogfeld **Manage Storage for Site Diagnostics** die Option **Storage Account**, um das Azure-Speicherkonto für den Container auszuwählen, der die Protokolle enthalten soll. Verwenden Sie die Option **Azure Blob Container**, um den Container auszuwählen, der die Protokolle enthalten soll, oder **Create a new blob container**, um das Feld **Blob Name** zu aktivieren, in dem Sie einen Namen für den neuen Container angeben können.
                **Hinweis**: Falls Sie noch kein Speicherkonto haben, rufen Sie den Abschnitt **Storage** im Azure-Portal auf, wo Sie auf **New** klicken, um ein neues Konto zu erstellen.
            -   Wenn Sie **File System** auswählen, werden die Protokolle auf der FTP-Site gespeichert, die unter **FTP Diagnostic Logs** auf der Dashboard-Verwaltungsseite aufgeführt ist. Sobald Sie Dateisystemspeicherung auswählen, wird auch das Feld **Quota** aktiviert, in dem Sie den maximalen Speicherplatz für die Protokolldateien angeben können. Die Mindestgröße beträgt 25 MB, das Maximum 100 MB. Die Standardgröße ist 35 MB. Sobald das Kontingent erreicht ist, werden die älteren Dateien nach und nach durch die neueren Dateien überschrieben.
            -   Standardmäßig werden Webserverprotokolle in einem Azure-Speicherkonto nie gelöscht. Wenn Sie einen Zeitraum angeben möchten, nach dem Protokolle automatisch gelöscht werden, wählen Sie **Set Retention** aus, und geben Sie unter **Retention Period** die Anzahl der Tage ein, für die Protokolle aufbewahrt werden sollen. Sie können auch die Option **Set Retention** für den Dateisystemspeicher verwenden.
        -   **Detailed Error Messages** - Gibt an, ob Sie detaillierte Fehlermeldungen für die Website protokollieren möchten. Wenn Sie diese Option aktivieren, werden detaillierte Fehlermeldungen als HTM-Dateien auf der FTP-Site gespeichert, die unter "FTP Diagnostic Logs" auf der Dashboard-Verwaltungsseite aufgeführt ist. Nachdem Sie eine Verbindung zur angegebenen FTP-Site hergestellt haben, rufen Sie unter "/LogFiles/DetailedErrors/" die HTM-Dateien mit den detaillierten Fehlermeldungen ab.
        -   **Failed Request Tracing** - Aktiviert die Verfolgung fehlgeschlagener Anforderungen. Wenn Sie diese Option aktivieren, werden Ausgaben zum Verfolgen fehlgeschlagener Anforderungen in XML-Dateien geschrieben und auf der FTP-Site gespeichert, die unter "FTP Diagnostic Logs" auf der Dashboard-Verwaltungsseite aufgeführt ist. Nachdem Sie eine Verbindung zur angegebenen FTP-Site hergestellt haben, rufen Sie "/LogFiles/W3SVC########\#" auf (wobei ########\# für die eindeutige ID der Website steht), um die XML-Dateien abzurufen, die die Ausgaben zum Verfolgen fehlgeschlagener Anforderungen enthalten.
            **Wichtig**
            Der Ordner "/LogFiles/W3SVC########\#/" enthält eine XSL-Datei und eine oder mehrere XML-Dateien. Vergewissern Sie sich, dass Sie die XSL-Datei in dasselbe Verzeichnis wie die XML-Datei(en) herunterladen, da die XSL-Datei die Funktionalität zum Formatieren und Filtern des Inhalts der XML-Datei(en) zur Anzeige in Internet Explorer zur Verfügung stellt.
        -   **Remote Debugging** - Stellen Sie diese Option auf **On**, um Remotedebuggen in Visual Studio 2012 oder Visual Studio 2013 zu aktivieren. Wenn Sie diese Option aktivieren, können Sie Remotedebuggen in Visual Studio verwenden, um direkt eine Verbindung zur Azure-Website herzustellen.
             **Hinweis**: Remotedebuggen wird nur für 48 Stunden aktiviert und funktioniert nicht für Websitenamen oder Benutzernamen, die mehr als 20 Zeichen enthalten.
    -   **monitoring** - Testen Sie für Websites im Standardmodus die Verfügbarkeit von HTTP- oder HTTPS-Endpunkten. Sie können einen Endpunkt von bis zu drei geografisch verteilten Standorten aus testen. Der Überwachungstest schlägt fehl, wenn der HTTP-Antwortcode größer als oder gleich 400 ist und die Antwort länger als 30 Sekunden benötigt. Der Endpunkt wird als verfügbar betrachtet, wenn die Überwachungstests von allen angegebenen Standorten aus erfolgreich waren.
    -   **developer analytics** - Verwenden Sie **Add-on**, um ein Analyse-Add-On aus einer Liste auszuwählen, oder wählen Sie eines im Azure Store aus. Verwenden Sie **Custom**, um einen Analyseanbieter wie New Relic aus der Liste auszuwählen. Bei Verwendung eines benutzerdefinierten Anbieters müssen Sie den Lizenzschlüssel in das Feld **Provider Key** eingeben.
         **Hinweis**: Weitere Informationen zur Verwendung von New Relic mit Azure-Websites finden Sie unter [Anwendungsleistungsverwaltung mit New Relic auf Azure-Websites](http://www.windowsazure.com/en-us/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/).
    -   **app settings** - Geben Sie Name-Wert-Paare an, die beim Start von der Webanwendung geladen werden. Bei .NET-Websites werden diese Einstellungen zur Laufzeit in die AppSettings der .NET-Konfiguration eingeführt, wobei vorhandene Einstellungen überschrieben werden. Bei PHP- und Node-Websites sind diese Einstellungen als Umgebungsvariablen zur Laufzeit verfügbar.
    -   **connection strings** - Zeigen Sie die Verbindungszeichenfolgen zu verknüpften Ressourcen an. Bei .NET-Websites werden diese Verbindungszeichenfolgen zur Laufzeit in die connectionStrings-Einstellung der .NET-Konfiguration eingeführt, wobei vorhandene Einträge überschrieben werden, wenn der Schlüssel dem verknüpften Datenbanknamen entspricht. Bei PHP- und Node-Websites sind diese Einstellungen als Umgebungsvariablen zur Laufzeit verfügbar, mit dem Verbindungstyp als Präfix. Die Präfixe der Umgebungsvariable lauten wie folgt:
        -   SQL Server: SQLCONNSTR\_
        -   MySQL: MYSQLCONNSTR\_
        -   SQL-Datenbank: SQLAZURECONNSTR\_
        -   Benutzerdefiniert: CUSTOMCONNSTR\_

        Wenn beispielsweise eine MySql-Verbindungszeichenfolge "connectionstring1" heißt, wird sie über die Umgebungsvariable `MYSQLCONNSTR_connectionString1` aufgerufen.
        **Hinweis**: Verbindungszeichenfolgen werden erstellt, wenn Sie eine Datenbankressource mit einer Website verknüpfen. Sie werden auf der Konfigurationsverwaltungsseite schreibgeschützt angezeigt.
    -   **default documents** - Fügen Sie das Standarddokument Ihrer Website dieser Liste hinzu, wenn es noch nicht darauf vorhanden ist. Das Standarddokument einer Website ist die Seite, die standardmäßig angezeigt wird, wenn ein Benutzer zu einer Website navigiert und keine bestimmte Seite auf der Website angibt. Wenn beispielsweise auf der Website http://contoso.com das Standarddokument auf "default.htm" festgelegt ist, wird der Benutzer zu http://www.contoso.com/default.htm weitergeleitet, wenn er im Browser http://www.contoso.com eingibt. Falls die Website mehrere Dateien in der Liste enthält, stellen Sie sicher, dass das Standarddokument Ihrer Website oben in der Liste steht (ändern Sie dazu die Reihenfolge der Dateien in der Liste).
    -   **handler mappings** - Fügen Sie benutzerdefinierte Skriptprozessoren hinzu, die Anforderungen für bestimmte Dateitypen verarbeiten. Geben Sie die zu verarbeitende Dateierweiterung im Feld **Extension** ein (z. B. \*.php oder handler.fcgi). Anforderungen für Dateien, die diesem Muster entsprechen, werden von dem unter **Script Processor Path** angegebenen Skriptprozessor verarbeitet. Für den Skriptprozessor ist ein absoluter Pfad erforderlich (der Pfad D:\\home\\site\\wwwroot kann verwendet werden, um auf das Stammverzeichnis Ihrer Website zu verweisen). Optionale Befehlszeilenargumente für den Skriptprozessor können im Feld **Additional Arguments (Optional)** angegeben werden.
    -   **virtual applications and directories** - Um die mit Ihrer Website verknüpften virtuellen Anwendungen und Verzeichnisse zu konfigurieren, geben Sie jedes virtuelle Verzeichnis und den zugehörigen physischen Pfad relativ zum Stammverzeichnis der Website an. Sie haben außerdem die Möglichkeit, mit dem Kontrollkästchen **Application** ein virtuelles Verzeichnis als Anwendung in der Website-Konfiguration zu markieren.

4 . Klicken Sie unten auf der Verwaltungsseite **Configure** auf **Save**, um die Konfigurationsänderungen zu speichern.

## Vorgehensweise: Konfigurieren einer Website zur Verwendung einer SQL-Datenbank

Befolgen Sie die Schritte, um eine Website mit einer SQL-Datenbank zu verknüpfen:

1.  Wählen Sie im [Verwaltungsportal](http://manage.windowsazure.com) die Option **Web Sites** aus, um eine Liste der Websites anzuzeigen, die über das derzeit angemeldete Konto erstellt wurden.

2.  Wählen Sie eine Website aus der Liste der Websites aus, um die **Management**-Seiten der Website zu öffnen.

3.  Klicken Sie auf die Registerkarte **Linked Resources**. Daraufhin wird die Seite **Linked Resources** mit einer Meldung angezeigt, dass Sie ****über keine verknüpften Ressourcen verfügen.

4.  Klicken Sie auf **Link a Resource**, um den Assistenten zum Verknüpfen von Ressourcen ****aufzurufen.

5.  Wenn Sie auf **Create a new resource** klicken, wird eine Liste mit Ressourcentypen angezeigt, die mit Ihrer Website verknüpft werden können.

6.  Klicken Sie auf **SQL Database**, um den Assistenten zum Verknüpfen von Datenbanken ****anzuzeigen.

7.  Füllen Sie die erforderlichen Felder auf den Seiten 3 und 4 des Assistenten für **Link Database** aus, und klicken Sie zum Abschluss auf das Häkchen **Finish** auf Seite 4.

Azure erstellt eine SQL-Datenbank mit den angegebenen Parametern und verknüpft die Datenbank mit der Website.

## Vorgehensweise: Konfigurieren einer Website zur Verwendung einer MySQL-Datenbank
Um eine Website zur Verwendung einer MySQL-Datenbank zu konfigurieren, befolgen Sie dieselben Schritte wie bei der SQL-Datenbank, aber wählen Sie im Assistenten **Link a Resource** die Option **MySQL Database** statt **SQL Database** aus.

Alternativ können Sie die Website mit der Option **Custom Create** erstellen. Wählen Sie in der Dropdown-Liste **Database** entweder **Create a new MySQL database** oder **Use an existing MySQL database** aus.

## Vorgehensweise: Konfigurieren eines benutzerdefinierten Domänennamens
---------------------------------------------------------------------

Informationen zum Konfigurieren einer Website mit einem benutzerdefinierten Domänennamen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website](http://www.windowsazure.com/en-us/documentation/articles/web-sites-custom-domain-name/).

Vorgehensweise: Konfigurieren einer Website zur Verwendung von SSL
------------------------------------------------------------------

Informationen zur Konfiguration von SSL für eine benutzerdefinierte Domäne auf Azure finden Sie unter [Aktivieren von HTTPS für eine Azure-Website](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/).

Nächste Schritte
----------------

-   [Skalieren von Websites](http://www.windowsazure.com/en-us/documentation/articles/web-sites-scale/)

-   [Überwachen von Websites](http://www.windowsazure.com/en-us/documentation/articles/web-sites-monitor/)


