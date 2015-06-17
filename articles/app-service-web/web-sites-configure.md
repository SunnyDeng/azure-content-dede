<properties 
	pageTitle="Konfigurieren von Web-Apps in Azure App Service" 
	description="Konfigurieren einer Web-App in Azure App Service" 
	services="app-service\web" 
	documentationCenter="" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="mwasson"/>


# Konfigurieren von Web-Apps in Azure App Service #

In diesem Thema wird erläutert, wie Sie eine Web-App mit dem [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) konfigurieren.

## Anwendungseinstellungen

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) das Blatt für die Web-App.
2. Klicken Sie auf **Alle Einstellungen**.
3. Klicken Sie auf **Anwendungseinstellungen**.

![](./media/web-sites-configure/configure01.png)

Das Blatt **Anwendungseinstellungen** enthält Einstellungen, die unter verschiedenen Kategorien gruppiert sind.

### Allgemeine Einstellungen

**Frameworkversionen**. Legen Sie diese Optionen fest, falls Ihre App von einem der folgenden Frameworks Gebrauch macht:

- **.NET Framework**: Legen Sie die .NET Framework-Version fest. 
- **PHP**: Legen Sie die PHP-Version fest, oder wählen Sie **AUS** aus, um PHP zu deaktivieren. - **Java**: Wählen Sie die Java-Version oder **AUS**, um Java zu deaktivieren. Über die Option **Webcontainer** können Sie zwischen Tomcat und Jetty wählen.
- **Python**: Legen Sie die Python-Version fest, oder wählen Sie **AUS** aus, um Python zu deaktivieren.

Aus technischen Gründen werden durch Aktivierung von Java für Ihre App die Optionen für .NET, PHP und Python deaktiviert.

<a name="platform"></a> **Plattform**. Bestimmt, ob Ihre Web-App in einer 32-Bit- oder 64-Bit-Umgebung ausgeführt wird. Für die 64-Bit-Umgebung ist der Modus "Basic" oder "Standard" erforderlich. Die Modi "Kostenlos" und "Freigegeben" werden immer in einer 32-Bit-Umgebung ausgeführt.

**Websockets**. Legen Sie **EIN** fest, um das WebSocket-Protokoll zu aktivieren, beispielsweise wenn Ihre Website [ASP.NET SignalR](http://www.asp.net/signalr) oder [socket.io](web-sites-nodejs-chat-app-socketio.md) verwendet.

<a name="alwayson"></a> **Immer auf**. Standardmäßig werden Web-Apps entladen, wenn sie einige Zeit im Leerlauf waren. Dadurch spart das System Ressourcen. Im Modus "Basic" oder "Standard" können Sie **Immer aktiviert** aktivieren, sodass die Web-App permanent geladen bleibt. Wenn Ihre App fortlaufende Webaufträge ausführt, sollten Sie **Immer aktiviert** aktivieren. Ansonsten werden fortlaufende Webaufträge unter Umständen nicht zuverlässig ausgeführt.

**Verwalteter Pipelinemodus**. Legt den IIS-[Pipelinemodus](http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application) fest. Lassen Sie diese Einstellung bei "Integriert" (der Standard), sofern Sie nicht eine ältere App haben, die eine ältere IIS-Version erfordert.

**Auto Swap**: Wenn Sie "Auto Swap" für einen Bereitstellungsslot aktivieren, ändert App Service den Status der Web-App automatisch in Produktion, wenn Sie eine Aktualisierung an diesen Slot übermitteln. Weitere Informationen finden Sie unter [Bereitstellen von Web-Apps in Azure App Service in Stagingslots](web-sites-staged-publishing.md).


### Debuggen

**Remotedebuggen**. Aktiviert das Remotedebugging. Wenn diese Option aktiviert ist, können Sie mit dem Remotedebugger in Visual Studio direkt eine Verbindung mit Ihrer Web-App herstellen. Das Remotedebuggen bleibt 48 Stunden aktiviert.


### App-Einstellungen

Dieser Abschnitt enthält Name-Wert-Paare, die Ihre Web-App beim Start lädt.

- Bei .NET-Apps werden diese Einstellungen zur Laufzeit in die `AppSettings` der .NET-Konfiguration eingefügt, wobei vorhandene Einstellungen überschrieben werden. 

- PHP-, Python-, Java- und Node-Anwendungen können auf diese Einstellungen als Umgebungsvariablen zur Laufzeit zugreifen. Für jede App-Einstellung werden zwei Umgebungsvariablen erstellt: eine mit dem Namen, der für die App-Einstellung eingegeben wurde, und eine weitere mit dem Präfix APPSETTING_. Beide enthalten denselben Wert.

### Verbindungszeichenfolgen

Verbindungszeichenfolgen für verknüpfte Ressourcen.

Bei .NET-Apps werden diese Verbindungszeichenfolgen zur Laufzeit in die `connectionStrings`-Einstellungen der .NET-Konfiguration eingefügt, wobei vorhandene Einträge überschrieben werden, wenn der Schlüssel dem verknüpften Datenbanknamen entspricht.

Bei PHP-, Python-, Java- und Node-Anwendungen sind diese Einstellungen als Umgebungsvariablen zur Laufzeit mit dem Verbindungstyp als Präfix verfügbar. Die Präfixe der Umgebungsvariable lauten wie folgt:

- SQL Server: SQLCONNSTR_
- MySQL: MYSQLCONNSTR_
- SQL-Datenbank:  SQLAZURECONNSTR_
- Benutzerdefiniert: CUSTOMCONNSTR_

Wenn beispielsweise eine MySQL-Verbindungszeichenfolge „`connectionstring1`“ heißt, wird sie über die Umgebungsvariable `MYSQLCONNSTR_connectionString1` aufgerufen.

### Standarddokumente

Das Standarddokument ist die Webseite, die an der Stamm-URL für eine Website angezeigt wird. Die erste übereinstimmende Datei in der Liste wird verwendet.

Web-Apps verwenden unter Umständen Module, die eine Weiterleitung auf Basis der URL ausführen und keinen statischen Inhalt bereitstellen. In diesem Fall gibt es kein Standarddokument als solches.

### Handlerzuordnungen

Fügen Sie in diesem Bereich benutzerdefinierte Skriptprozessoren hinzu, die Anforderungen für bestimmte Dateierweiterungen verarbeiten.

- **Erweiterung**. Die zu verarbeitende Dateierweiterung wie *.php oder handler.fcgi.- **Skriptprozessorpfad**. Der absolute Pfad des Skriptprozessors. Anforderungen für Dateien, die dieser Dateierweiterung entsprechen, werden vom Skriptprozessor verarbeitet. Verwenden Sie den Pfad `D:\home\site\wwwroot`, um auf das Stammverzeichnis Ihrer App zu verweisen.
- **Zusätzliche Argumente**. Optionale Befehlszeilenargumente für den Skriptprozessor. 


### Virtuelle Anwendungen und Verzeichnisse 
 
Um die virtuellen Anwendungen und Verzeichnisse zu konfigurieren, geben Sie jedes virtuelle Verzeichnis und den zugehörigen physischen Pfad relativ zum Stammverzeichnis der Website an. Sie haben außerdem die Möglichkeit, mit dem Kontrollkästchen **Anwendung** ein virtuelles Verzeichnis als Anwendung zu markieren.


## Aktivieren von Diagnoseprotokollen

So aktivieren Sie Diagnoseprotokolle

1. Klicken Sie auf dem Blatt für die Web-App auf **Alle Einstellungen**.
2. Klicken Sie auf **Diagnoseprotokolle**. 

Optionen zum Erstellen von Diagnoseprotokollen über eine Webanwendung, die die Protokollierung unterstützt:

- **Anwendungsprotokollierung**. Schreibt Anwendungsprotokolle in das Dateisystem. Die Protokollierung erfolgt über zwölf Stunden. 

**Ebene**. Wenn die Anwendungsprotokollierung aktiviert ist, bestimmt diese Option, ob Fehler, Warnmeldungen, Infos oder ausführliche Informationen protokolliert werden.

**Webserverprotokollierung**. Protokolle werden im erweiterten W3C-Protokolldateiformat gespeichert.

**Detaillierte Fehlermeldungen**. Speichert detaillierte Fehlermeldungen in HTM-Dateien. Die Dateien werden unter "/LogFiles/DetailedErrors" gespeichert.

**Verfolgung fehlgeschlagener Anforderungen**. Protokolliert fehlgeschlagene Anforderungen in XML-Dateien. Die Dateien werden unter "/LogFiles/W3SVC*xxx*" gespeichert, wobei xxx ein eindeutiger Bezeichner ist. Dieser Ordner enthält eine XSL-Datei und eine oder mehrere XML-Dateien. Stellen Sie sicher, dass Sie die XSL-Datei herunterladen, da sie die Funktionalität zum Formatieren und Filtern des Inhalts der XML-Dateien zur Verfügung stellt.

Zum Anzeigen der Protokolldateien müssen Sie die FTP-Anmeldeinformationen wie folgt bereitstellen:

1. Klicken Sie auf dem Blatt für die Web-App auf **Alle Einstellungen**.
2. Klicken Sie auf **Anmeldeinformationen für die Bereitstellung**.
3. Geben Sie einen Benutzernamen und ein Kennwort ein.
4. Klicken Sie auf **Speichern**.

![](./media/web-sites-configure/configure03.png)


Der vollständige FTP-Benutzername lautet „App\\Benutzername“. Dabei ist *App* der Name Ihrer Web-App. Den Benutzernamen finden Sie auf dem Blatt der Web-App unter **Essentials**.

![](./media/web-sites-configure/configure02.png)

## Weitere Konfigurationsaufgaben

### SSL 

Im Modus "Basic" oder "Standard" können Sie SSL-Zertifikate für benutzerdefinierte Domänen hochladen. Weitere Informationen finden Sie unter [Aktivieren von HTTPS für eine Web-App](web-sites-configure-ssl-certificate.md).

Klicken Sie zum Anzeigen der hochgeladenen Zertifikate auf **Alle Einstellungen** > **Benutzerdefinierte Domänen und SSL**.

### Domänennamen

Fügen Sie Ihrer Web-App benutzerdefinierte Domänennamen hinzu. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Web-App in Azure App Service](web-sites-custom-domain-name.md).

Klicken Sie zum Anzeigen des Domänennamens auf **Alle Einstellungen** > **Benutzerdefinierte Domänen und SSL**.

### Bereitstellungen

- Legen Sie die kontinuierliche Bereitstellung fest. Weitere Informationen finden Sie unter [Bereitstellen von Web-Apps in Azure App Service mit Git](web-sites-publish-source-control.md).
- Bereitstellungsslots. Weitere Informationen finden Sie unter [Bereitstellen von Web-Apps in Azure App Service in Stagingumgebungen](web-sites-staged-publishing.md).

Klicken Sie zum Anzeigen der Bereitstellungsslots auf **Alle Einstellungen** > **Bereitstellungsslots**.


### Überwachung

Im Modus "Basic" oder "Standard" können Sie die Verfügbarkeit von HTTP- oder HTTPS-Endpunkten von bis zu drei geografisch verteilten Standorten aus testen. Ein Überwachungstest verläuft nicht erfolgreich, wenn der HTTP-Antwortcode ein Fehler ist (4xx oder 5xx) oder die Antwort länger als 30 Sekunden benötigt. Der Endpunkt wird als verfügbar betrachtet, wenn die Überwachungstests von allen angegebenen Standorten aus erfolgreich waren.

Weitere Informationen finden Sie unter [Vorgehensweise: Überwachen des Web-Endpunktstatus](http://go.microsoft.com/fwLink/?LinkID=279906&clcid=0x409).

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Nächste Schritte

- [Konfigurieren eines benutzerdefinierten Domänennamens](web-sites-custom-domain-name.md)
- [Aktivieren von HTTPS](web-sites-configure-ssl-certificate.md)
- [Skalieren einer Web-App in Azure App Service](web-sites-scale.md)
- [Grundlagen der Überwachung von Web-Apps in Azure App Service](web-sites-monitor.md)

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->