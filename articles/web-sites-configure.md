<properties urlDisplayName="How to configure" pageTitle="Konfigurieren von Websites - Azure-Dienstverwaltung" metaKeywords="Azure-Websites, Konfigurieren von Azure-Websites, Azure SQL-Datenbank, Azure MySQL" description="Learn how to configure websites in Azure, including how to configure a website to use a SQL Database or MySQL database." metaCanonical="" services="web-sites" documentationCenter="" title="How to Configure Websites" authors="mwasson" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/18/2014" ms.author="mwasson" />


# Konfigurieren von Websites #
Im Azure-Verwaltungsportal können Sie die Konfigurationsoptionen für Websites ändern und eine Website mit anderen Azure-Ressourcen, beispielsweise einer Datenbank, verknüpfen.

## Inhaltsverzeichnis
- [Gewusst wie: Ändern der Konfigurationsoptionen für eine Website](#howtochangeconfig)
- [Gewusst wie: Konfigurieren einer Website zur Verwendung einer SQL-Datenbank](#howtoconfigSQL)
- [Gewusst wie: Konfigurieren einer Website zur Verwendung einer MySQL-Datenbank](#howtoconfigMySQL)
- [Gewusst wie: Konfigurieren eines benutzerdefinierten Domänennamens](#howtodomain)
- [Gewusst wie: Konfigurieren einer Website zur Verwendung von SSL](#howtoconfigSSL)
- [Nächste Schritte](#next)


##<a name="howtochangeconfig"></a>Gewusst wie: Ändern der Konfigurationsoptionen für eine Website

<!-- HOW TO: CHANGE CONFIGURATION OPTIONS FOR A WEBSITE -->

So legen Sie die Konfigurationsoptionen für eine Website fest

1. Öffnen Sie im [Verwaltungsportal](https://manage.windowsazure.com/) die Verwaltungsseiten der Website.
1. Klicken Sie auf die Registerkarte <strong>Konfigurieren</strong>.

Die Registerkarte **Konfigurieren** gliedert sich in folgende Bereiche:

### Allgemein

**Frameworkversionen**. Legen Sie diese Optionen fest, falls Ihre App von einem der folgenden Frameworks Gebrauch macht: 

- **.NET Framework**: Legen Sie die .NET Framework-Version fest. 
- **PHP**: Legen Sie die PHP-Version fest, oder wählen Sie **AUS** aus, um PHP zu deaktivieren. 
- **Java**: Wählen Sie die angezeigte Version aus, um Java zu aktivieren, oder wählen Sie <strong>AUS</strong> aus, um Java zu deaktivieren. 
- Bei Aktivierung von Java wählen Sie mithilfe der Option <strong>Webcontainer</strong> entweder die Tomcat- oder Jetty-Version aus.
- **Python**: Legen Sie die Python-Version fest, oder wählen Sie **AUS** aus, um Python zu deaktivieren.

Aus technischen Gründen werden durch Aktivierung von Java für Ihre Website die Optionen für .NET, PHP und Python deaktiviert.

<strong>Verwalteter Pipelinemodus</strong>. Legt den IIS-[Pipelinemodus](http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application) fest. Lassen Sie diese Einstellung bei "Integriert" (der Standard), sofern Sie nicht eine ältere Website haben, die eine ältere IIS-Version erfordert.

<strong>Plattform</strong>. Bestimmt, ob Ihre Anwendung in einer 32-Bit- oder 64-Bit-Umgebung ausgeführt wird. Für die 64-Bit-Umgebung ist der Modus "Basic" oder "Standard" erforderlich. Die Modi "Kostenlos" und "Freigegeben" werden immer in einer 32-Bit-Umgebung ausgeführt.

<strong>Websockets</strong>. Legen Sie **EIN** fest, um das WebSocket-Protokoll zu aktivieren, beispielsweise wenn Ihre Website [ASP.NET SignalR](http://www.asp.net/signalr) oder [socket.io](http://azure.microsoft.com/en-us/documentation/articles/web-sites-nodejs-chat-app-socketio/) verwendet.

<strong>Immer bereit</strong>. Standardmäßig werden Websites entladen, wenn sie einige Zeit im Leerlauf waren. Dadurch spart das System Ressourcen. Im Modus "Basic" oder "Standard" können Sie <strong>Immer bereit</strong> aktivieren, sodass die Website permanent geladen bleibt. Wenn Ihre Website fortlaufende Webjobs ausführt, sollten Sie **Immer bereit** aktivieren. Ansonsten werden fortlaufende Webjobs unter Umständen nicht zuverlässig ausgeführt.

<strong>In Visual Studio Online bearbeiten</strong>. Aktiviert die Live-Codebearbeitung mit Visual Studio Online. Wenn diese Option aktiviert ist, enthält die Registerkarte "Dashboard" im Bereich <strong>Auf einen Blick</strong> einen Link namens <strong>In Visual Studio Online bearbeiten</strong>. Klicken Sie auf diesen Link, um Ihre Website direkt online zu bearbeiten. Falls Sie sich authentifizieren müssen, können Sie Ihre Bereitstellungsanmeldung verwenden.

Hinweis: Falls Sie die Bereitstellung über die Quellcodeverwaltung aktiviert haben, kann eine Bereitstellung die Änderungen, die Sie im Visual Studio Online Editor vornehmen, überschreiben. 


### Zertifikate

Im Modus "Basic" oder "Standard" können Sie SSL-Zertifikate für benutzerdefinierte Domänen hochladen. Weitere Informationen finden Sie unter [Aktivieren von HTTPS für eine Azure-Website](href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-configure-ssl-certificate/). 

Die hochgeladenen Zertifikate werden hier aufgelistet. Nach dem Hochladen eines Zertifikats können Sie es einer beliebigen Website in Ihrem Abonnement und Ihrer Region zuweisen. Platzhalterzertifikate lassen sich für jede Website innerhalb der Domäne, für die sie gültig sind, verwenden. Ein Zertifikat kann nur gelöscht werden, wenn dafür keine aktiven Bindungen vorliegen.

### Domänennamen

Sie können Domänennamen für die Website anzeigen oder zusätzliche hinzufügen. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website](http://www.windowsazure.com/en-us/documentation/articles/web-sites-custom-domain-name/).

### SSL-Bindungen

Wenn Sie SSL-Zertifikate hochgeladen haben, können Sie diese an benutzerdefinierte Domänennamen binden. Weitere Informationen finden Sie unter [Aktivieren von HTTPS für eine Azure-Website](href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-configure-ssl-certificate/)

### Bereitstellungen

Dieser Bereich wird nur angezeigt, wenn Sie die Bereitstellung über die Quellcodeverwaltung aktiviert haben. Verwenden Sie diese Einstellungen zum Konfigurieren der Bereitstellungen.

- <strong>Git-URL</strong>. Falls Sie ein Git-Repository für Ihre Azure-Website erstellt haben, ist dies die URL, über die Sie den Inhalt mithilfe von Push übertragen.
- <strong>Bereitstellungsauslöser-URL</strong>. Diese URL kann für ein GitHub, CodePlex, Bitbucket oder ein anderes Repository festgelegt werden, um die Bereitstellung auszulösen, wenn ein Commit-Push an das Repository erfolgt.
- <strong>Bereitzustellende Verzweigung</strong>. Gibt die Verzweigung an, die bereitgestellt wird, wenn Sie Inhalt mithilfe von Push übertragen.

Um die Bereitstellung über die Quellcodeverwaltung einzurichten, zeigen Sie die Registerkarte **Dashboard** an und klicken auf **Bereitstellung über Quellcodeverwaltung einrichten**. 

### Anwendungsdiagnose

Optionen zum Erstellen von Diagnoseprotokollen über eine Webanwendung, die die Protokollierung unterstützt: 

- <strong>Dateisystem</strong>. Schreibt Protokolle in das Dateisystem der Website. Die Dateisystemprotokollierung geht über zwölf Stunden. Sie können die Protokolle über die FTP-Freigabe der Website abrufen. (Siehe [FTP-Anmeldeinformationen](http://azure.microsoft.com/en-us/documentation/articles/web-sites-manage#ftp-credentials)).
- <strong>Tabellenspeicher</strong>. Schreibt Protokolle in den Azure-Tabellenspeicher. Es gibt kein Zeitlimit. Die Protokollierung bleibt aktiviert, bis Sie sie deaktivieren. 
- <strong>BLOB-Speicher</strong>. Schreibt Protokolle in den Azure-BLOB-Speicher. Es gibt kein Zeitlimit. Die Protokollierung bleibt aktiviert, bis Sie sie deaktivieren.

<strong>Protokollierungsstufe</strong>. Wenn Protokollierung aktiviert ist, bestimmt diese Option, ob Fehler, Warnmeldungen, Infos oder ausführliche Informationen protokolliert werden.

**Tabellenspeicher verwalten**. Klicken Sie bei aktiviertem Tabellenspeicher auf diese Schaltfläche, um das Speicherkonto und den Tabellennamen festzulegen.

**BLOB-Speicher verwalten.** Klicken Sie bei aktiviertem BLOB-Speicher auf diese Schaltfläche, um das Speicherkonto und den BLOB-Speichernamen festzulegen.

### Site-Diagnose

Optionen zum Erfassen von Diagnoseinformationen für Ihre Website.

<strong>Webserverprotokollierung</strong>. Aktiviert die Webserverprotokollierung. Protokolle werden im erweiterten W3C-Protokolldateiformat gespeichert. Sie können die Protokolle im Azure-Speicher oder im Dateisystem der Website speichern.
 
- Wenn Sie <strong>Dateisystem</strong> auswählen, werden die Protokolle auf der FTP-Website gespeichert, die auf der Seite "Dashboard" unter "FTP-Diagnoseprotokolle" aufgeführt ist. (Siehe [FTP-Anmeldeinformationen](http://azure.microsoft.com/en-us/documentation/articles/web-sites-manage#ftp-credentials).) 
- Verwenden Sie bei Wahl von **Dateisystem** das Feld <strong>Kontingent</strong>, um den maximalen Speicherplatz für die Protokolldateien festzulegen. Die Mindestgröße beträgt 25 MB, das Maximum 100 MB. Die Standardgröße ist 35 MB. Sobald das Kontingent erreicht ist, werden die älteren Dateien nach und nach durch die neueren Dateien überschrieben. Wenn Sie mehr Aufzeichnungen als 100 MB beibehalten wollen, verwenden Sie den Azure-Speicher, der eine sehr viel größere Speicherkapazität hat.
- Klicken Sie optional auf <strong>Aufbewahrung festlegen</strong>, um Dateien nach einem Zeitraum automatisch zu löschen. Standardmäßig werden Protokolle nie gelöscht.   

<strong>Detaillierte Fehlermeldungen</strong>. Wenn diese Option aktiviert ist, werden detaillierte Fehlermeldungen als HTM-Dateien gespeichert. Navigieren Sie zur Anzeige der Dateien zur FTP-Website, die auf der Seite "Dashboard" unter "FTP-Diagnoseprotokolle" aufgeführt ist. Die Dateien werden auf der FTP-Website unter "/LogFiles/DetailedErrors" gespeichert. (Siehe [FTP-Anmeldeinformationen](http://azure.microsoft.com/en-us/documentation/articles/web-sites-manage#ftp-credentials).)

<strong>Ablaufverfolgung für Anforderungsfehler</strong>. Wenn diese Option aktiviert ist, werden fehlerhafte Anforderungen in XML-Dateien protokolliert. Navigieren Sie zur Anzeige der Dateien zur FTP-Website, die auf der Seite "Dashboard" unter "FTP-Diagnoseprotokolle" aufgeführt ist. (Siehe [FTP-Anmeldeinformationen](http://azure.microsoft.com/en-us/documentation/articles/web-sites-manage#ftp-credentials).) Die Dateien werden unter "/LogFiles/W3SVC*xxx*" gespeichert, wobei xxx ein eindeutiger Bezeichner ist. Dieser Ordner enthält eine XSL-Datei und eine oder mehrere XML-Dateien. Stellen Sie sicher, dass Sie die XSL-Datei herunterladen, da sie die Funktionalität zum Formatieren und Filtern des Inhalts der XML-Dateien zur Verfügung stellt.

<strong>Remotedebuggen</strong>. Aktiviert das Remotedebuggen. Wenn diese Option aktiviert ist, können Sie mit dem Remotedebugger in Visual Studio direkt eine Verbindung mit Ihrer Azure-Website herstellen. Das Remotedebuggen bleibt 48 Stunden aktiviert.

**Hinweis**: Das Remotedebuggen funktioniert nicht für Websitenamen oder Benutzernamen, die mehr als 20 Zeichen umfassen. 

### Überwachung

Im Modus "Basic" oder "Standard" können Sie die Verfügbarkeit von HTTP- oder HTTPS-Endpunkten von bis zu drei geografisch verteilten Standorten aus testen. Ein Überwachungstest verläuft nicht erfolgreich, wenn der HTTP-Antwortcode ein Fehler ist (4xx oder 5xx) oder die Antwort länger als 30 Sekunden benötigt. Der Endpunkt wird als verfügbar betrachtet, wenn die Überwachungstests von allen angegebenen Standorten aus erfolgreich waren. 

 Weitere Informationen finden Sie unter [Gewusst wie: Überwachen des Web-Endpunktstatus](http://go.microsoft.com/fwLink/?LinkID=279906&clcid=0x409).


### Entwickleranalysen

Wählen Sie <strong>Add-On</strong> aus, um ein Analyse-Add-On aus einer Liste auszuwählen, oder wählen Sie eines im Azure-Store aus. Verwenden Sie <strong>Benutzerdefiniert</strong>, um einen Analyseanbieter wie New Relic aus der Liste auszuwählen. Bei Verwendung eines benutzerdefinierten Anbieters müssen Sie den Lizenzschlüssel in das Feld <strong>Anbieterschlüssel</strong> eingeben. 

Weitere Informationen zur Verwendung von New Relic mit Azure-Websites finden Sie unter <a href="http://www.windowsazure.com/en-us/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/">Anwendungsleistungsverwaltung mit New Relic auf Azure-Websites</a>.

### App-Einstellungen

Geben Sie Name-Wert-Paare an, die beim Start der Webanwendung geladen werden. 

- Bei .NET-Websites werden diese Einstellungen zur Laufzeit in die AppSettings der .NET-Konfiguration eingeführt, wobei vorhandene Einstellungen überschrieben werden. 

- PHP-, Python-, Java- und Node-Anwendungen können auf diese Einstellungen als Umgebungsvariablen zur Laufzeit zugreifen. Für jede App-Einstellung werden zwei Umgebungsvariablen erstellt: eine mit dem Namen, der für die App-Einstellung eingegeben wurde, und eine weitere mit dem Präfix APPSETTING_. Beide enthalten denselben Wert.

### Verbindungszeichenfolgen

Verbindungszeichenfolgen für verknüpfte Ressourcen. 

Bei .NET-Websites werden diese Verbindungszeichenfolgen zur Laufzeit in die connectionStrings-Einstellung der .NET-Konfiguration eingeführt, wobei vorhandene Einträge überschrieben werden, wenn der Schlüssel dem verknüpften Datenbanknamen entspricht. 

Bei PHP-, Python-, Java- und Node-Anwendungen sind diese Einstellungen als Umgebungsvariablen zur Laufzeit mit dem Verbindungstyp als Präfix verfügbar. Die Präfixe der Umgebungsvariable lauten wie folgt: 

- SQL Server: SQLCONNSTR_
- MySQL: MYSQLCONNSTR_
- SQL-Datenbank: SQLAZURECONNSTR_
- Benutzerdefiniert: CUSTOMCONNSTR_

Wenn beispielsweise eine MySql-Verbindungszeichenfolge "connectionstring1" heißt, wird sie über die Umgebungsvariable <code>MYSQLCONNSTR_connectionString1</code> aufgerufen.

<strong>Hinweis</strong>: Verbindungszeichenfolgen werden auch erstellt, wenn Sie eine Datenbankressource mit einer Website verknüpfen. Auf diese Weise erstellte Verbindungszeichenfolgen werden auf der 
Konfigurationsverwaltungsseite schreibgeschützt angezeigt.

### Standarddokumente

Das Standarddokument einer Website ist die Seite, die angezeigt wird, wenn vom Besucher keine bestimmte Seite auf der Website angegeben wird. Falls Ihre Website mehrere Dateien in der Liste enthält, stellen Sie sicher, dass das Standarddokument oben in der Liste steht.

Webanwendungen verwenden unter Umständen Module, die eine Weiterleitung auf Basis der URL ausführen und keinen statischen Inhalt bereitstellen. In diesem Fall gibt es kein Standarddokument als solches.   

### Handlerzuordnungen

Fügen Sie in diesem Bereich benutzerdefinierte Skriptprozessoren hinzu, die Anforderungen für bestimmte Dateierweiterungen verarbeiten. 

- **Erweiterung**. Die zu verarbeitende Dateierweiterung wie *.php oder handler.fcgi.
- **Skriptprozessorpfad**. Der absolute Pfad des Skriptprozessors. Anforderungen für Dateien, die dieser Dateierweiterung entsprechen, werden vom Skriptprozessor verarbeitet. Verwenden Sie den Pfad <code>D:\home\site\wwwroot</code>, um auf das Stammverzeichnis Ihrer Website zu verweisen.
- **Zusätzliche Argumente**. Optionale Befehlszeilenargumente für den Skriptprozessor.


### Virtuelle Anwendungen und Verzeichnisse 

Um die mit Ihrer Website verknüpften virtuellen Anwendungen und Verzeichnisse zu konfigurieren, geben Sie jedes virtuelle Verzeichnis und den zugehörigen physischen Pfad relativ zum Stammverzeichnis der Website an. Sie haben außerdem die Möglichkeit, mit dem Kontrollkästchen <strong>Anwendung</strong> ein virtuelles Verzeichnis als Anwendung in der Websitekonfiguration zu markieren.

	

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A SQL DATABASE -->
##<a name="howtoconfigSQL"></a>Gewusst wie: Konfigurieren einer Website zur Verwendung einer SQL-Datenbank

Befolgen Sie die folgenden Schritte, um eine Website mit einer SQL-Datenbank zu verknüpfen:

1. Wählen Sie im [Verwaltungsportal](http://manage.windowsazure.com) die Option **Websites** aus, um eine Liste der Websites anzuzeigen, die über das derzeit angemeldete Konto erstellt wurden.

2. Wählen Sie eine Website aus der Liste der Websites aus, um die **Verwaltungsseiten** der Website zu öffnen.

3. Klicken Sie auf die Registerkarte **Verknüpfte Ressourcen**. Daraufhin wird die Seite **Verknüpfte Ressourcen** mit der Meldung **Sie haben keine verknüpften Ressourcen** angezeigt.

4. Klicken Sie auf **Eine Ressource verknüpfen**, um den Assistenten zum **Verknüpfen von Ressourcen** aufzurufen.

5. Klicken Sie auf **Neue Ressource erstellen**, um eine Liste mit Ressourcentypen anzuzeigen, die mit Ihrer Website verknüpft werden können.

6. Klicken Sie auf **SQL-Datenbank**, um den Assistenten zum **Verknüpfen von Datenbanken** aufzurufen.

7. Füllen Sie die erforderlichen Felder auf den Seiten 3 und 4 des Assistenten zum **Verknüpfen von Datenbanken** aus, und klicken Sie zum Abschluss auf das Häkchen für **Fertig stellen** auf Seite 4.

Azure erstellt eine SQL-Datenbank mit den angegebenen Parametern und verknüpft die Datenbank mit der Website.

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A MYSQL DATABASE -->
##<a name="howtoconfigMySQL"></a>Gewusst wie: Konfigurieren einer Website zur Verwendung einer MySQL-Datenbank##
Um eine Website zur Verwendung einer MySQL-Datenbank zu konfigurieren, befolgen Sie dieselben Schritte wie bei der SQL-Datenbank, wählen jedoch im Assistenten zum **Verknüpfen von Ressourcen** die Option **MySQL-Datenbank** statt **SQL-Datenbank** aus. 

Alternativ können Sie die Website mit der Option **Benutzerdefiniert erstellen** erstellen. Wählen Sie in der Dropdown-Liste **Datenbank** entweder **Neue MySQL-Datenbank erstellen** oder **Vorhandene MySQL-Datenbank verwenden** aus. 

##<a name="howtodomain"></a>Gewusst wie: Konfigurieren eines benutzerdefinierten Domänennamens

Informationen zum Konfigurieren Ihrer Website mit einem benutzerdefinierten Domänennamen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website](http://www.windowsazure.com/en-us/documentation/articles/web-sites-custom-domain-name/).

##<a name="howtoconfigSSL"></a>Gewusst wie: Konfigurieren einer Website zur Verwendung von SSL##

Informationen zur Konfiguration von SSL für eine benutzerdefinierte Domäne auf Azure finden Sie unter [Aktivieren von HTTPS für eine Azure-Website](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/). 

##<a name="next"></a>Nächste Schritte

* [Skalieren von Websites](http://www.windowsazure.com/en-us/documentation/articles/web-sites-scale/)

* [Überwachen von Websites](http://www.windowsazure.com/en-us/documentation/articles/web-sites-monitor/)

