<properties
   pageTitle="Anleitungen für Hintergrundaufträge | Microsoft Azure"
   description="Anleitungen für Hintergrundaufgaben, die unabhängig von der Benutzeroberfläche ausgeführt werden."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/18/2015"
   ms.author="masashin"/>

# Anleitungen für Hintergrundaufträge

![](media/best-practices-background-jobs/pnp-logo.png)


## Übersicht

Viele Arten von Anwendungen erfordern Hintergrundaufgaben, die unabhängig von der Benutzeroberfläche (UI) ausgeführt werden. Beispiele hierfür sind Batchaufträge, rechenintensive Aufgaben und langwierige Prozesse, wie z. B. Workflows. Hintergrundaufträge können ohne Benutzerinteraktion ausgeführt werden. Die Anwendung kann den Auftrag starten und dann mit der Verarbeitung interaktiver Benutzeranforderungen fortfahren. Damit kann die Arbeitsauslastung der Anwendungsbenutzeroberfläche minimiert werden, was zu einer höheren Verfügbarkeit und zu kürzeren interaktiven Antwortzeiten beitragen kann.

Wenn eine Anwendung z. B. Miniaturansichten von Bildern, die von den Benutzern hochgeladen wurden, generieren muss, kann dies als Hintergrundauftrag ausgeführt werden. Die fertiggestellte Miniaturansicht kann dann im Speicher abgelegt werden, ohne dass der Benutzer auf den Abschluss des Prozesses warten muss. Auf die gleiche Weise kann ein Benutzer, der eine Bestellung aufgibt, einen Hintergrund-Workflow zur Verarbeitung der Bestellung initiieren, während die Benutzeroberfläche zulässt, dass der Benutzer die Web-App weiterhin durchsucht. Wenn der Hintergrundauftrag abgeschlossen ist, können die gespeicherten Bestelldaten aktualisiert und eine E-Mail zur Bestätigung der Bestellung an den Benutzer gesendet werden.

Bei der Überlegung, ob eine Aufgabe als Hintergrundauftrag implementiert werden soll, ist das Hauptkriterium, ob die Aufgabe ohne Benutzereingriff ausgeführt werden kann und ohne dass die Benutzeroberfläche auf den Abschluss des Auftrags warten muss. Aufgaben, die einen Benutzereingriff erfordern oder bei denen die Benutzeroberfläche warten muss, bis sie abgeschlossen sind, eignen sich unter Umständen nicht als Hintergrundaufträge.

## Typen von Hintergrundaufträgen

Hintergrundaufträge besitzen in der Regel mindestens eine der folgenden Eigenschaften:

- CPU-intensive Aufträge, z. B. mathematische Berechnungen, Strukturmodellanalysen und mehr.
- E/a-intensive Aufträge, z. B. die Ausführung einer Reihe von Speichertransaktionen oder die Indizierung von Dateien.
- Batchaufträge, z. B. nächtliche Datenaktualisierungen oder eine geplante Verarbeitung.
- Lang andauernde Workflows, z. B. Auftragserfüllung oder Bereitstellung von Diensten und Systemen.
- Vertrauliche Datenverarbeitung, wobei die Aufgabe zur Verarbeitung an einen sichereren Ort übergeben wird. Beispielsweise sollten vertrauliche Daten nicht in einer Web-App verarbeitet werden, sondern Sie sollten stattdessen ein Muster wie [Gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx) verwenden, um die Daten an einen isolierten Hintergrundprozess zu übertragen, der auf geschützten Speicher zugreifen kann.

## Trigger

Hintergrundaufträge können auf verschiedene Weise initiiert werden. Sie fallen im Grunde genommen in eine der folgenden Kategorien:

- [**Ereignisgesteuerte Trigger**](#event-driven-triggers). Die Aufgabe wird in Reaktion auf ein Ereignis, in der Regel eine Benutzeraktion oder ein Schritt in einem Workflow, gestartet.
- [**Zeitplangesteuerte Trigger**](#schedule-driven-triggers). Die Aufgabe wird nach einem Zeitplan basierend auf einem Timer aufgerufen. Es kann sich dabei um einen periodischen Zeitplan oder einen in der Zukunft geplanten einmaligen Aufruf handeln.

### Ereignisgesteuerte Trigger

Bei einem ereignisgesteuerten Aufruf wird die Hintergrundaufgabe über einen Trigger gestartet. Beispiele für die Verwendung ereignisgesteuerter Trigger:

- Die Benutzeroberfläche oder ein anderer Auftrag fügt eine Nachricht in eine Warteschlange ein. Die Nachricht enthält Daten über eine Aktion, die ausgeführt wurde, wie z. B. die Bestellung des Benutzers. Die Hintergrundaufgabe überwacht diese Warteschlange und erkennt den Eingang einer neuen Nachricht. Sie liest die Nachricht und verwendet die darin enthaltenen Daten als Eingabe für den Hintergrundauftrag.
- Die Benutzeroberfläche oder ein anderer Auftrag speichert oder aktualisiert einen Wert im Speicher. Die Hintergrundaufgabe überwacht den Speicher und erkennt Änderungen. Sie liest die Daten und verwendet sie als Eingabe für den Hintergrundauftrag.
- Die Benutzeroberfläche oder ein anderer Auftrag sendet eine Anforderung an einen Endpunkt, z. B. einen HTTPS-URI oder eine API, die als Webdienst verfügbar gemacht wurde. Anschließend werden die Daten, die zum Abschließen der Hintergrundaufgabe erforderlich sind, als Teil der Anforderung übergeben. Der Endpunkt oder Webdienst ruft die Hintergrundaufgabe auf, die die Daten als Eingabe verwendet.

Typische Beispiele für Aufgaben, die sich für einen ereignisgesteuerte Aufruf eignen, sind unter anderem Bildverarbeitung, Workflows, Senden von Informationen an Remotedienste, Senden von E-Mail-Nachrichten, Bereitstellen neuer Benutzer in mehrinstanzenfähigen Anwendungen.

### Zeitplangesteuerte Trigger

Bei einem zeitplangesteuerten Aufruf wird die Hintergrundaufgabe über einen Timer gestartet. Beispiele für die Verwendung zeitplangesteuerter Trigger:

- Ein Timer, der in der Anwendung oder als Teil des Betriebssystems der Anwendung lokal ausgeführt wird, ruft in regelmäßigen Abständen eine Hintergrundaufgabe auf.
- Ein Timer, der in einer anderen Anwendung ausgeführt wird, oder ein Timerdienst wie das Azure-Zeitplanungsmodul sendet in regelmäßigen Abständen eine Anforderung an eine API oder einen Webdienst. Die API oder der Webdienst ruft die Hintergrundaufgabe auf.
- Ein separater Prozess oder eine Anwendung startet einen Timer, der bewirkt, dass die Hintergrundaufgabe erst nach einer angegebenen Zeitverzögerung oder zu einem bestimmten Zeitpunkt aufgerufen wird.

Typische Beispiele für Aufgaben, die sich für einen zeitplangesteuerten Aufruf eignen, sind unter anderem Stapelverarbeitungsroutinen, z. B. das Aktualisieren von zu Benutzern gehörigen Produktlisten basierend auf dem aktuellen Verhalten der Benutzer, routinemäßige Datenverarbeitungsaufgaben wie das Aktualisieren von Indizes oder das Generieren von Ergebnissen, das Analysieren von Daten für tägliche Berichte, Bereinigung der Datenaufbewahrung und Datenkonsistenzprüfungen.

Bei Verwendung einer zeitplangesteuerten Aufgabe, die als einzelne Instanz ausgeführt werden muss, müssen Sie Folgendes beachten:

- Wenn die Serverinstanz, welche die Aufgabenplanung ausführt (z. B. virtuelle Computer mit Windows-Aufgabenplanung), skaliert wird, kann dies dazu führen, dass mehrere Instanzen der Aufgabenplanung ausgeführt werden und dass von diesen Instanzen mehrere Instanzen der Aufgabe gestartet werden.
- Falls die Ausführungszeit der Aufgaben länger als der Zeitraum zwischen geplanten Ereignissen ist, kann die Aufgabenplanung eine weitere Instanz der Aufgabe starten, während die vorherige Aufgabe noch ausgeführt wird.

## Rückgabe von Ergebnissen

Hintergrundaufträge werden asynchron in einem separaten Prozess oder sogar an einem anderen Ort als die Benutzeroberfläche oder der Prozess ausgeführt, die bzw. der die Hintergrundaufgabe aufgerufen hat. Im Idealfall sind Hintergrundaufgaben Vorgänge, deren Ausführungsstatus keine Auswirkungen auf die Benutzeroberfläche oder den aufrufenden Prozess hat. Dies bedeutet, dass der aufrufende Prozess nicht auf den Abschluss der Aufgaben wartet und daher nicht automatisch erkennen kann, wann eine Aufgabe endet. Wenn eine Hintergrundaufgabe mit der aufrufenden Aufgabe kommunizieren soll, um ihre Verarbeitung oder ihren Abschluss anzuzeigen, müssen Sie einen entsprechenden Mechanismus implementieren. Hier einige Beispiele:

- Schreiben Sie einen Statusindikatorwert in den Speicher, der für die Benutzeroberfläche oder die aufrufende Aufgabe zugänglich ist, um diesen Wert bei Bedarf überwachen oder überprüfen zu können. Andere Daten, die die Hintergrundaufgabe an den Aufrufer zurückgeben muss, können in den gleichen Speicher eingefügt werden.
- Richten Sie eine Antwortwarteschlange ein, die von der Benutzeroberfläche oder dem Aufrufer überwacht werden kann. Die Hintergrundaufgabe kann Nachrichten an die Warteschlange senden, um den Status und ihren Abschluss anzugeben. Daten, die die Hintergrundaufgabe an den Aufrufer zurückgeben muss, können in die Nachrichten eingefügt werden. Wenn Sie Azure Service Bus verwenden, können Sie die Eigenschaften **ReplyTo** und **CorrelationId** zur Implementierung dieser Funktion verwenden. Weitere Informationen finden Sie unter [Korrelation in Service Bus-Brokermessaging](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- Verfügbar machen Sie eine API oder einen Endpunkt der Hintergrundaufgabe verfügbar, auf die bzw. den die Benutzeroberfläche oder der Aufrufer zugreifen kann, um Statusinformationen abzurufen. Daten, die die Hintergrundaufgabe an den Aufrufer zurückgeben muss, können in die Antwort eingefügt werden.
- Lassen Sie die Hintergrundaufgabe über eine API die Benutzeroberfläche oder den Aufrufer zurückrufen, um an vordefinierten Punkten oder beim Abschluss den Status anzugeben. Dies kann durch lokal ausgelöste Ereignisse oder über einen Veröffentlichen-und-Abonnieren-Mechanismus geschehen. Die Daten, die die Hintergrundaufgabe an den Aufrufer zurückgeben muss, können in der Anforderungs- oder Ereignisnutzlast enthalten sein.

## Hosting-Umgebung

Hintergrundaufgaben können mithilfe einer Reihe von verschiedenen Azure-Plattformdiensten gehostet werden:

- [**Azure-Web-Apps und WebJobs**](#azure-web-apps-and-webjobs). Mit WebJobs können benutzerdefinierte Aufträge basierend auf einer Reihe verschiedener Typen von Skripts oder eines ausführbaren Programms im Kontext einer Web-App ausgeführt werden.
- [**Azure Cloud Services-Web- und Workerrollen**](#azure-cloud-services-web-and-worker-roles). Sie können Code in einer Rolle schreiben, der als Hintergrundaufgabe ausgeführt wird.
- [**Azure Virtual Machines**](#azure-virtual-machines). Wenn Sie einen Windows-Dienst haben oder die Windows-Aufgabenplanung verwenden möchten, ist es üblich, die Hintergrundaufgaben auf einem dedizierten virtuellen Computer zu hosten.

In den folgenden Abschnitten werden diese einzelnen Optionen und zu berücksichtigende Aspekte ausführlich beschrieben, um Ihnen die Auswahl der geeigneten Option zu erleichtern.

### Azure-Web-Apps und WebJobs

Mit Azure WebJobs können benutzerdefinierte Aufträge als Hintergrundaufgaben in einer Azure-Web-App ausgeführt werden. WebJobs können im Kontext Ihrer Web-App als fortlaufender Prozess oder in Reaktion auf ein Triggerereignis von Azure Scheduler oder in Reaktion auf externe Faktoren ausgeführt werden, wie z. B. Änderungen an Speicher-Blobs und Nachrichtenwarteschlangen. Aufträge können bei Bedarf gestartet und angehalten und ordnungsgemäß heruntergefahren werden. Wenn in einem fortlaufend ausgeführten WebJob ein Fehler auftritt, wird er automatisch neu gestartet. Wiederholungs- und Fehleraktionen sind konfigurierbar.

Hinweise zum Konfigurieren eines WebJobs:

- Wenn der Auftrag auf einen ereignisgesteuerten Trigger reagieren soll, sollte er mit der Option **Dauerhaft ausführen** konfiguriert werden. Das Skript oder Programm wird im Ordner "site/wwwroot/app\_data/jobs/continuous" gespeichert.
- Wenn der Auftrag auf einen zeitplangesteuerten Trigger reagieren soll, sollte er mit der Option **Gemäß einem Zeitplan ausführen** konfiguriert werden. Das Skript oder Programm wird im Ordner "site/wwwroot/app\_data/jobs/triggered" gespeichert.
- Wenn Sie zum Konfigurieren eines Auftrags die Option **Bedarfsgesteuert ausführen** wählen, wird beim Start des Auftrags der gleichen Code wie bei der Option **Gemäß einem Zeitplan ausführen** ausgeführt.

Azure WebJobs werden in der Sandbox der Web-App ausgeführt, d. h. dass sie auf Umgebungsvariablen zugreifen und Informationen wie Verbindungszeichenfolgen mit der Web-App gemeinsam nutzen können. Der Auftrag hat Zugriff auf den eindeutigen Bezeichner des Computers, auf dem der Auftrag ausgeführt wird. Die Verbindungszeichenfolge mit dem Namen **AzureWebJobsStorage** ermöglicht den Zugriff auf Azure-Speicherwarteschlangen, Blobs und Tabellen für Anwendungsdaten sowie auf Service Bus für Messaging und Kommunikation. Die Verbindungszeichenfolge mit dem Namen **AzureWebJobsDashboard** ermöglicht den Zugriff auf die Protokolldateien zu den Auftragsaktionen.

Azure WebJobs weisen folgende Merkmale auf:

- **Sicherheit**: WebJobs werden durch die Anmeldeinformationen für die Bereitstellung der Web-App geschützt.
- **Unterstützte Dateitypen**: WebJobs können als Befehlsskript (.cmd), Batchdatei (.bat), PowerShell-Skript (.ps1), Bash-Shellskript (.sh), PHP-Skript (.php), Python-Skript (.py), JavaScript-Code (.js) und ausführbares Programm (.exe, .jar usw.) definiert werden.
- **Bereitstellung**: Skripts und ausführbare Dateien können über das Azure-Portal bereitgestellt werden oder mit dem Add-In [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) für Visual Studio oder dem [Visual Studio 2013 Update 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs) oder mithilfe des [Azure WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md) erstellt und bereitgestellt werden. Zudem können sie durch direktes Kopieren an die folgenden Speicherorte bereitgestellt werden:
  - für die durch Trigger initiierte Ausführung: site/wwwroot/app\_data/jobs/triggered/{Auftragsname}
  - für die fortwährende Ausführung: site/wwwroot/app\_data/jobs/continuous/{Auftragsname}
- **Protokollierung**: Console.Out wird als INFO und Console.Error als ERROR behandelt (markiert). Über das Azure-Portal kann auf Überwachungs- und Diagnosedaten zugegriffen werden, und Protokolldaten können direkt von der Website heruntergeladen werden. Sie werden an den folgenden Speicherorten gespeichert:
  - für die durch Trigger initiierte Ausführung: Vfs/data/jobs/continuous/jobName
  - für die fortwährende Ausführung: Vfs/data/jobs/triggered/jobName
- **Konfiguration**: WebJobs können mit dem Portal, der REST API und PowerShell konfiguriert werden. Konfigurationsinformationen für einen Auftrag können in einer Konfigurationsdatei namens "settings.job", die sich im Stammverzeichnis des Jobskripts befinden muss, bereitgestellt werden. Beispiel:
  - { "stopping\_wait\_time": 60 }
  - { "is\_singleton": true }

### Überlegungen

- Standardmäßig werden WebJobs mit der Web-App skaliert. Allerdings können Aufträge für die Ausführung in einer einzelnen Instanz konfiguriert werden, indem die Konfigurationseigenschaft **is\_singleton** auf true festgelegt wird. Einzelinstanz-WebJobs sind für Aufgaben sinnvoll, die nicht skaliert oder gleichzeitig in mehreren Instanzen ausgeführt werden sollen, z. B. eine erneute Indizierung, Datenanalyse oder ähnliche Aufgaben.
- Damit die Leistung der Web-App durch die Aufträge möglichst minimal beeinträchtigt wird, sollten Sie in Betracht ziehen, eine leere Azure-Web-App-Instanz in einem neuen App Service-Plan zum Hosten von WebJobs zu erstellen, die langwierig oder ressourcenintensiv sind.

### Weitere Informationen

- [Empfohlene Ressourcen für Azure-Webaufträge](websites-webjobs-resources.md) enthält viele nützliche Ressourcen, Downloads und Beispiele für WebJobs.

## Azure Cloud Services-Web- und Workerrollen

Hintergrundaufgaben können in einer Webrolle oder in einer separaten Workerrolle ausgeführt werden. Bei der Entscheidung, ob eine Workerrolle verwendet sollte, sollten die Anforderungen an Skalierbarkeit und Elastizität, die Lebensdauer der Aufgabe, Freigabekadenz, Sicherheit, Fehlertoleranz, Konflikte, Komplexität und die logische Architektur berücksichtigt werden. Weitere Informationen finden Sie unter [Serverressourcen-Konsolidierungsmuster](http://msdn.microsoft.com/library/dn589778.aspx).

Hintergrundaufgaben können auf verschiedene Weise in einer Cloud Services-Rolle implementiert werden.

- Erstellen Sie eine Implementierung der **RoleEntryPoint**-Klasse in der Rolle, und verwenden Sie deren Methoden zum Ausführen der Hintergrundaufgaben. Die Aufgaben werden im Kontext von "WaIISHost.exe" ausgeführt und können die **GetSetting**-Methode der **CloudConfigurationManager**-Klasse zum Laden der Konfigurationseinstellungen verwenden. Weitere Informationen finden Sie unter [Lebenszyklus (Cloud Services)](#lifecycle-cloud-services).
- Verwenden Sie Startaufgaben, um Hintergrundaufgaben beim Starten der Anwendung auszuführen. Um die weitere Ausführung der Aufgaben im Hintergrund zu erzwingen, legen Sie die **taskType**-Eigenschaft auf **background** fest (falls Sie dies nicht tun, wird der Startprozess der Anwendung angehalten und wartet, bis die Aufgabe abgeschlossen ist). Weitere Informationen finden Sie unter [Ausführen von Startaufgaben in Azure](cloud-services-startup-tasks.md).
- Verwenden Sie das WebJobs SDK, um Hintergrundaufgaben als WebJobs zu implementieren, die als Startaufgabe initiiert werden. Weitere Informationen finden Sie unter [Erste Schritte mit dem Azure WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md).
- Verwenden Sie eine Startaufgabe, um einen Windows-Dienst zu installieren, der eine oder mehrere Hintergrundaufgaben ausführt. Sie müssen die **taskType**-Eigenschaft auf **background** festlegen, damit der Dienst im Hintergrund ausgeführt wird. Weitere Informationen finden Sie unter [Ausführen von Startaufgaben in Azure](cloud-services-startup-tasks.md).

### Ausführen von Hintergrundaufgaben in einer Webrolle

Der Hauptvorteil der Ausführung von Hintergrundaufgaben in der Webrolle besteht in der Einsparung von Hostingkosten, da keine weiteren Rollen bereitgestellt werden müssen.

### Ausführen von Hintergrundaufgaben in einer Workerrolle

Die Ausführung von Hintergrundaufgaben in einer Workerrolle bietet verschiedene Vorteile:

- Sie ermöglicht es, die Skalierung für jeden Rollentyp getrennt zu verwalten. Es kann beispielsweise möglich sein, dass Sie zusätzlich Instanzen einer Webrolle zur Unterstützung der aktuellen Last benötigen, jedoch weniger Instanzen der Workerrolle, die Hintergrundaufgaben ausführt. Durch die von den Benutzeroberflächenrollen getrennte Skalierung der Serverinstanzen für Hintergrundaufgaben können die Hostingkosten reduziert und gleichzeitig eine akzeptable Leistung aufrechterhalten werden.
- Die Webrolle wird dadurch von dem mit Hintergrundaufgaben verbundenen Verarbeitungsaufwand entlastet. Die Webrolle, die die Benutzeroberfläche bereitstellt, kann reaktionsfähig bleiben, und es kann bedeuten, dass weniger Instanzen erforderlich sind, um ein bestimmtes Volumen an Benutzeranforderungen zu unterstützen.
- Sie ermöglicht Ihnen die Implementierung einer Trennung von Zuständigkeiten. Jeder Rollentyp kann einen bestimmten Satz von klar definierten und verwandten Aufgaben implementieren. Dies erleichtert das Entwerfen und Verwalten des Codes, da es zwischen den einzelnen Rollen weniger wechselseitige Abhängigkeiten bezüglich des Codes und der Funktionalität gibt.
- Vertrauliche Prozesse und Daten können leichter isoliert werden. Beispielsweise müssen die Webrollen, die die Benutzeroberfläche implementieren, nicht auf Daten zugreifen, die von einer Workerrolle verwaltet und gesteuert werden. Dies kann bei der Verstärkung der Sicherheit nützlich sein, insbesondere bei Verwendung eines Musters, wie das [Gatekeeper-Muster](http://msdn.microsoft.com/library/dn589793.aspx).  

### Überlegungen

Beachten Sie die folgenden Punkte bei der Entscheidung, wie und wo Hintergrundaufgaben bei Verwendung von Cloud Services-Web- und Workerrollen bereitgestellt werden sollen:

- Durch das Hosten von Hintergrundaufgaben in einen vorhandenen Webrolle können die Kosten der Ausführung einer separaten Workerrolle eigens für diese Aufgaben eingespart werden, aber wahrscheinlich werden dadurch die Leistung und Verfügbarkeit der Anwendung beeinträchtigt, wenn es einen Wettstreit um Verarbeitungsressourcen und andere Ressourcen gibt. Durch die Verwendung einer separaten Workerrolle wird die Webrolle vor den Auswirkungen von langwierigen oder ressourcenintensiven Hintergrundaufgaben geschützt.
- Wenn Sie Hintergrundaufgaben mithilfe der **RoleEntryPoint**-Klasse hosten, können Sie dies problemlos einer anderen Rolle übertragen. Wenn Sie z. B. die Klasse in einer Webrolle erstellen und später entscheiden, dass die Aufgaben in einer Workerrolle ausgeführt werden müssen, können Sie die **RoleEntryPoint**-Klassenimplementierung in die Workerrolle verschieben.
- Startaufgaben sind auf die Ausführung eines Programms oder Skripts ausgelegt. Die Bereitstellung eines Hintergrundauftrags als ausführbares Programm kann u. U. schwieriger sein, insbesondere dann, wenn dazu auch abhängige Assemblys bereitgestellt werden müssen. Bei Verwendung von Startaufgaben ist es möglicherweise einfacher, ein Skript bereitzustellen und zu verwenden, um einen Hintergrundauftrag zu definieren.
- Ausnahmen, die zum Fehlschlagen einer Hintergrundaufgabe führen, haben unterschiedliche Auswirkungen, je nachdem, wie sie gehostet werden:
  - Bei Verwendung der **RoleEntryPoint**-Klasse führt eine fehlgeschlagene Aufgabe dazu, dass die Rolle neu gestartet wird, damit die Aufgabe automatisch neu gestartet wird. Dies kann sich auf die Verfügbarkeit der Anwendung auswirken. Um dies zu verhindern, müssen Sie eine robuste Ausnahmebehandlung innerhalb der **RoleEntryPoint**-Klasse und in allen Hintergrundaufgaben sicherstellen. Verwenden Sie dort, wo es zweckmäßig ist, Code, um fehlgeschlagene Aufgaben neu zu starten, und lösen Sie nur dann eine Ausnahme aus, um die Rolle neu zu starten, wenn sich der Fehler nicht innerhalb des Codes ordnungsgemäß beheben lässt.
  - Wenn Sie Startaufgaben verwenden, sind Sie für die Verwaltung der Aufgabenausführung und die Überprüfung auf Fehler verantwortlich.
- Das Verwalten und Überwachen von Startaufgaben ist schwieriger als die Verwendung der **RoleEntryPoint**-Klasse. Das Azure WebJobs SDK enthält jedoch ein Dashboard, das die Verwaltung von Webaufträgen, die über Startaufgaben gestartet werden, erleichtert.

### Weitere Informationen

- [Serverressourcen-Konsolidierungsmuster](http://msdn.microsoft.com/library/dn589778.aspx)
- [Erste Schritte mit dem Azure WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md)

## Azure Virtual Machines

Hintergrundaufgaben können so implementiert werden, dass sie nicht in Azure-Web-Apps oder Cloud Services bereitgestellt werden können oder sich dazu nicht eignen. Typische Beispiele sind Windows-Dienste und Hilfsprogrammen von Drittanbietern und ausführbare Programme. Hierzu können auch Programme gehören, die für eine Ausführungsumgebung geschrieben wurden, die sich von der Umgebung unterscheidet, in der die Anwendung gehostet wird. Beispielsweise kann dies ein UNIX- oder Linux-Programm sein, das von einer Windows- oder .NET-Anwendung ausgeführt werden soll. Sie können eines von verschiedenen Betriebssystemen für einen virtuellen Azure-Computer auswählen und den Dienst oder die ausführbare Datei dann auf diesem virtuellen Computer ausführen.

Informationen dazu, wann sich die Verwendung von Virtual Machines anbietet, finden Sie unter [Azure App Service, Cloud Services und Virtual Machines im Vergleich](choose-web-site-cloud-service-vm.md). Weitere Informationen zu den Optionen für virtuelle Computer finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Weitere Informationen zu den Betriebssystemen und verfügbaren vorgefertigten Images für virtuelle Computer finden Sie unter [Katalog für virtuelle Azure-Computer](https://azure.microsoft.com/gallery/virtual-machines/).

Zum Initiieren der Hintergrundaufgabe in einem separaten virtuellen Computer stehen verschiedene Optionen zur Auswahl:

- Sie können die Aufgabe bei Bedarf direkt von Ihrer Anwendung ausführen, indem Sie eine Anforderung an einen Endpunkt senden, den die Aufgabe verfügbar macht, und alle für die Aufgabe erforderlichen Daten übergeben. Der Endpunkt ruft die Aufgabe auf.
- Sie können die Aufgabe unter Verwendung eines im ausgewählten Betriebssystem verfügbaren Planungsmoduls oder Zeitgebers so konfigurieren, dass sie nach einem Zeitplan ausgeführt wird. Z. B. können Sie unter Windows die Windows-Aufgabenplanung zur Ausführung von Skripts und Aufgaben nutzen. Wenn SQL Server auf dem virtuellen Computer installiert ist, können Sie den SQL Server-Agent zum Ausführen von Skripts und Aufgaben verwenden.
- Mit Azure Scheduler können Sie die Aufgabe initiieren, indem Sie eine Nachricht in eine Warteschlange einfügen, die von der Aufgabe überwacht wird, oder indem Sie eine Anforderung an eine API senden, die von der Aufgabe verfügbar gemacht wird.

Weitere Informationen zum Initiieren von Hintergrundaufgaben finden Sie weiter oben im Abschnitt [Trigger](#triggers).

### Überlegungen

Berücksichtigen Sie die folgenden Punkte bei der Entscheidung, ob Hintergrundaufgaben in einem virtuellen Azure-Computer bereitgestellt werden sollen:

- Das Hosten von Hintergrundaufgaben in einem separaten virtuellen Azure-Computer ermöglicht Flexibilität und eine präzise Steuerung der Initiierung, Ausführung, Planung und Ressourcenzuweisung. Allerdings werden die Laufzeitkosten höher, wenn ein virtueller Computer eigens zur Ausführung von Hintergrundaufgaben bereitgestellt werden muss.
- Es ist keine Funktion zum Überwachen der Aufgaben im Azure-Portal verfügbar und auch keine Funktion zum automatischen Neustarten fehlgeschlagener Aufgaben. Sie können allerdings den grundlegenden Status des virtuellen Computers mithilfe der [Azure Service-Verwaltungs-Cmdlets](http://msdn.microsoft.com/library/azure/dn495240.aspx) überwachen und verwalten. Es gibt jedoch keine Möglichkeit, die Prozesse und Threads in Serverknoten zu steuern. In der Regel erfordert die Verwendung eines virtuellen Computers zusätzlichen Aufwand zur Implementierung eines Mechanismus, um Daten von der Instrumentierung der Aufgabe und vom Betriebssystem des virtuellen Computers zu sammeln. Eine möglicherweise geeignete Lösung besteht darin, das [System Center Management Pack für Azure](http://technet.microsoft.com/library/gg276383.aspx) zu verwenden.
- Sie sollten die Erstellung von Überwachungsprüfmodulen, die über HTTP-Endpunkte verfügbar gemacht werden, in Betracht ziehen. Der Code für diese Prüfmodule könnte Integritätsprüfungen durchführen, operative Informationen und Statistiken sammeln oder Fehlerinformationen sortieren und diese Daten an die Verwaltungsanwendung zurückgeben. Weitere Informationen finden Sie unter [Überwachungsmuster für den Integritätsendpunkt](http://msdn.microsoft.com/library/dn589789.aspx).

### Weitere Informationen

- [Virtuelle Computer in Azure](https://azure.microsoft.com/services/virtual-machines/)
- [Virtuelle Computer in Azure – häufig gestellte Fragen](virtual-machines-questions.md)

## Überlegungen zum Entwurf

Beim Entwerfen von Hintergrundaufgaben sind verschiedene grundlegende Faktoren zu berücksichtigen. In den folgenden Abschnitten werden die Partitionierung, Konflikte und die Koordination besprochen.

## Partitionierung

Wenn Sie Hintergrundaufgaben in eine vorhandene Compute-Instanz (z. B. eine Web-App, Webrolle, vorhandene Workerrolle oder einen virtuellen Computer) aufnehmen möchten, müssen Sie berücksichtigen, wie sich dies auf die Qualitätsattribute der Compute-Instanz und auf die Hintergrundaufgabe selbst auswirken wird. Anhand dieser Faktoren können Sie entscheiden, ob die Aufgaben in der vorhandenen Serverinstanz oder in einer separaten Serverinstanz ausgeführt werden sollen:

- **Verfügbarkeit**: Hintergrundaufgaben benötigen möglicherweise nicht dasselbe Maß an Verfügbarkeit wie andere Teile der Anwendung, insbesondere die Benutzeroberfläche und andere Teile, die direkt an der Interaktion mit dem Benutzer beteiligt sind. Hintergrundaufgaben können toleranter gegenüber Latenz, wiederholten Verbindungsfehlern und anderen Faktoren sein, die sich auf die Verfügbarkeit auswirken, da die Vorgänge in eine Warteschlange eingefügt werden können. Allerdings muss genügend Kapazität vorhanden sein, um zu verhindern, dass Anforderungen zurückgesetzt werden, wodurch Warteschlangen blockiert werden könnten und die Anwendung als Ganzes beeinträchtigt werden könnte.
- **Skalierbarkeit**: Hintergrundaufgaben haben wahrscheinlich andere Anforderungen an die Skalierbarkeit als die Benutzeroberfläche und die interaktiven Teile der Anwendung. Eine Skalierung der Benutzeroberfläche kann erforderlich sein, um Nachfragespitzen zu bedienen, während ausstehende Hintergrundaufgaben in Zeiten mit weniger starker Auslastung von einer geringeren Anzahl von Serverinstanzen abgeschlossen werden könnten.
- **Resilienz**: Der Ausfall einer Serverinstanz, die nur Hintergrundaufgaben hostet, wirkt sich möglicherweise nicht schwerwiegend auf die gesamte Anwendung aus, wenn die Anforderungen für diese Aufgaben in eine Warteschlange eingefügt oder zurückgestellt werden können, bis die Aufgabe wieder verfügbar ist. Wenn die Serverinstanz und/oder die Aufgaben in einem angemessenen Zeitraum neu gestartet werden können, sind Benutzer der Anwendung möglicherweise nicht davon betroffen.
- **Sicherheit**: Für Hintergrundaufgaben gelten möglicherweise andere Sicherheitsanforderungen oder Einschränkungen als für die Benutzeroberfläche oder andere Teile der Anwendung. Wenn Sie eine separate Serverinstanz verwenden, können Sie für diese Aufgaben eine andere Sicherheitsumgebung angeben. Sie können auch Muster wie Gatekeeper verwenden, um die Serverinstanzen für Hintergrundaufgaben von der Benutzeroberfläche zu isolieren und Sicherheit und Trennung zu maximieren.
- **Leistung**: Sie können den Serverinstanztyp für Hintergrundaufgaben wählen, der speziell den Leistungsanforderungen der Aufgaben entspricht. Dies kann bedeuten, dass eine kostengünstigere Serveroption ausreicht, wenn die Aufgaben nicht dieselben Verarbeitungsfunktionen wie die Benutzeroberfläche benötigen, oder dass eine größere Instanz verwendet werden muss, wenn sie zusätzliche Kapazität und Ressourcen erfordern.
- **Verwaltbarkeit**: Hintergrundaufgaben haben möglicherweise einen anderen Entwicklungs- und Bereitstellungsrhythmus als der Hauptanwendungscode oder die Benutzeroberfläche. Durch ihre Bereitstellung in einer separaten Serverinstanz können Aktualisierungen und die Versionsverwaltung vereinfacht werden.
- **Kosten**: Durch das Hinzufügen von Serverinstanzen zur Ausführung von Hintergrundaufgaben steigen die Hostingkosten. Sie sollten sorgfältig zwischen zusätzlicher Kapazität und zusätzlichen Kosten abwägen.

Weitere Informationen finden Sie unter [Muster für die Auswahl einer übergeordneten Instanz](http://msdn.microsoft.com/library/dn568104.aspx) und [Muster für konkurrierende Consumer](http://msdn.microsoft.com/library/dn568101.aspx).

## Konflikte

Wenn mehrere Instanzen eines Hintergrundauftrags vorhanden sind, ist es möglich, dass sie um den Zugriff auf Ressourcen und Dienste, wie Datenbanken und Speicher, konkurrieren. Dieser gleichzeitige Zugriff kann zu Ressourcenkonflikte führen, die Konflikte bei der Verfügbarkeit der Dienste und der Integrität der Daten im Speicher verursachen können. Ressourcenkonflikte können durch pessimistische Sperren gelöst werden, um zu verhindern, dass konkurrierende Instanzen einer Aufgabe gleichzeitig auf einen Dienst zugreifen oder Daten beschädigen.

Ein anderer Konfliktlösungsansatz ist die Definition der Hintergrundaufgaben als Singleton, sodass immer nur eine Instanz ausgeführt wird. Allerdings gehen dadurch die Vorteile hinsichtlich der Zuverlässigkeit und Leistung verloren, die eine Konfiguration mit mehreren Instanzen bieten kann, insbesondere dann, wenn die Benutzeroberfläche genügend Arbeit zur Auslastung mehrerer Hintergrundaufgaben liefert. Es muss unbedingt sichergestellt werden, dass die Hintergrundaufgabe automatisch neu gestartet werden kann und dass ausreichend Kapazität zur Verarbeitung von Nachfragespitzen verfügbar ist. Zu diesem Zweck können einer Serverinstanz ausreichend Ressourcen zugeordnet, ein Warteschlangenmechanismus, der Anforderungen für die spätere Ausführung in Zeiten geringerer Nachfrage speichert, implementiert oder eine Kombination dieser Techniken verwendet werden.

## Koordination

Die Hintergrundaufgaben können komplex sein und die Ausführung von mehreren Einzelaufgaben erfordern, um ein Ergebnis zu erhalten oder alle Anforderungen zu erfüllen. In diesen Szenarien wird die Aufgabe häufig in kleinere Einzelschritte oder Unteraufgaben aufgeteilt, die von mehreren Consumern ausgeführt werden können. Aus mehreren Schritten bestehende Aufträge können effizienter und flexibler sein, da einzelne Schritte möglicherweise in mehreren Aufträgen wiederverwendbar sind. Zudem können einzelne Schritte einfach hinzugefügt oder entfernt werden, oder die Reihenfolge der Schritte kann geändert werden.

Die Koordination mehrerer Aufgaben und Schritte kann schwierig sein, aber es gibt drei allgemeine Muster, an denen Sie sich bei der Implementierung einer Lösung orientieren können:

- **Zerlegen einer Aufgabe in mehrere wiederverwendbare Schritte**. Es kann von einer Anwendung verlangt werden, dass sie verschiedene Aufgaben unterschiedlicher Komplexität mit den von ihr verarbeiteten Informationen ausführt. Ein einfacher, aber unflexibler Ansatz zur Implementierung einer solchen Anwendung könnte darin bestehen, diese Verarbeitung in einem monolithischen Modul durchzuführen. Dieser Ansatz würde jedoch die Möglichkeiten zur Umgestaltung des Codes, zu seiner Optimierung und Wiederverwendung einschränken, wenn an anderer Stelle in der Anwendung Teile des gleichen Verarbeitungsmechanismus erforderlich sind. Weitere Informationen finden Sie unter [Muster für Pipes und Filter](http://msdn.microsoft.com/library/dn568100.aspx).
- **Verwalten der Ausführung der Schritte einer Aufgabe**. Eine Anwendung kann Aufgaben ausführen, die eine Reihe von Schritten umfassen, von denen einige möglicherweise Remotedienste aufrufen oder auf Remoteressourcen zugreifen. Die einzelnen Schritte können voneinander unabhängig sein, aber sie werden von der Anwendungslogik, die die Aufgabe implementiert, orchestriert. Weitere Informationen finden Sie unter [Muster für Scheduler-Agent-Supervisor](http://msdn.microsoft.com/library/dn589780.aspx).
- **Verwalten der Wiederherstellung von fehlgeschlagenen Schritten einer Aufgabe**. Eine Anwendung muss möglicherweise die Vorgänge rückgängig machen, die für eine Reihe von Schritten, die schlussendlich zusammen einen konsistenten Vorgang bilden, ausgeführt wurden, wenn einer oder mehrere dieser Schritte fehlschlägt bzw. fehlschlagen. Weitere Informationen finden Sie unter [Muster für eine ausgleichende Transaktion](http://msdn.microsoft.com/library/dn589804.aspx).

## Lebenszyklus (Cloud-Dienste)

 Wenn Sie sich dazu entscheiden, Hintergrundaufträge für Cloud Services-Anwendungen zu implementieren, die mithilfe der **RoleEntryPoint**-Klasse Web-und Workerrollen verwenden, müssen Sie den Lebenszyklus dieser Klasse kennen, um sie ordnungsgemäß verwenden zu können.

Web- und Workerrollen durchlaufen unterschiedliche Phasen, während sie gestartet, ausgeführt und beendet werden. Die **RoleEntryPoint**-Klasse stellt eine Reihe von Ereignissen zur Verfügung, die anzeigen, wann diese Phasen auftreten. Sie können diese Ereignisse zum Initialisieren, Ausführen und Beenden von benutzerdefinierten Hintergrundaufgaben verwenden. Der vollständige Zyklus sieht so aus:

- Azure lädt die Rollenassembly und sucht nach einer von **RoleEntryPoint** abgeleiteten Klasse.
- Wenn diese Klasse gefunden wird, wird **RoleEntryPoint.OnStart()** aufgerufen. Sie überschreiben diese Methode, um die Hintergrundaufgaben zu initialisieren.
- Nach Abschluss der **OnStart**-Methode ruft Azure in der globalen Datei der Anwendung **Application\_Start()** auf, sofern die Datei vorhanden ist (z. B. "Global.asax" in einer Webrolle unter ASP.NET).
- Azure ruft **RoleEntryPoint.Run()** in einem neuen Vordergrundthread auf, der parallel zu **OnStart()** ausgeführt wird. Sie überschreiben diese Methode, um die Hintergrundaufgaben zu starten.
- Wenn die Run-Methode beendet wird, ruft Azure zuerst **Application\_End()** in der globalen Datei der Anwendung, sofern diese vorhanden ist, und anschließend **RoleEntryPoint.OnStop()** auf. Sie überschreiben die **OnStop**-Methode, um die Hintergrundaufgaben zu beenden, Ressourcen zu bereinigen, Objekte zu löschen und die Verbindungen zu schließen, die von den Aufgaben möglicherweise verwendet wurden.
- Der Hostprozess für die Azure-Workerrolle wird beendet. An diesem Punkt wird die Rolle wiederverwendet und neu gestartet.

Weitere Einzelheiten und ein Beispiel für die Verwendung der Methoden der **RoleEntryPoint**-Klasse finden Sie unter [Muster für die Konsolidierung von Compute-Ressourcen](http://msdn.microsoft.com/library/dn589778.aspx).

## Überlegungen

Beachten Sie die folgenden Punkte, wenn Sie planen, wie Hintergrundaufgaben in einer Web- oder Workerrolle ausgeführt werden sollen:

- Die Standardimplementierung der **Run**-Methode in der **RoleEntryPoint**-Klasse enthält einen **Thread.Sleep(Timeout.Infinite)**-Aufruf, durch den die Rolle auf unbestimmte Zeit aktiv bleibt. Wenn Sie die **Run**-Methode überschreiben (die in der Regel zum Ausführen von Hintergrundaufgaben erforderlich ist), müssen Sie dem Code die Möglichkeit bieten, die Methode zu verlassen, sofern Sie die Rolleninstanz nicht wiederverwenden möchten.
- Eine typische Implementierung der **Run**-Methode enthält Code zum Starten der einzelnen Hintergrundaufgaben und ein Schleifenkonstrukt, in dem regelmäßig der Status aller Hintergrundaufgaben überprüft wird. Aufgaben, die fehlschlagen, können neu gestartet werden, oder die Implementierung kann Abbruchtoken überwachen, die darauf hinweisen, dass die Aufträge abgeschlossen wurden.
- Wenn eine Hintergrundaufgabe eine nicht behandelte Ausnahme auslöst, sollte diese Aufgabe wiederverwendet werden, während andere Hintergrundaufgaben in der Rolle weiter ausgeführt werden können. Wird die Ausnahme jedoch durch eine Beschädigung von Objekten außerhalb der Aufgabe, z. B. freigegebenen Speicher, verursacht, dann sollte die Ausnahme von der **RoleEntryPoint**-Klasse behandelt werden, alle Aufgaben sollten abgebrochen werden, und die **Run**-Methode sollte beendet werden können. Azure startet die Rolle dann neu.
- Verwenden Sie die **OnStop**-Methode zum Anhalten oder Beenden von Hintergrundaufgaben und zum Bereinigen von Ressourcen. Hierzu müssen u. U. langwierige oder aus mehreren Schritten bestehende Aufgaben beendet werden, und um Dateninkonsistenzen zu vermeiden, muss genau überlegt werden, wie sich dies erreichen lässt. Wenn eine Rolleninstanz aus einem anderen Grund als ein vom Benutzer eingeleitetes Herunterfahren beendet wird, muss die Ausführung des Codes, der in der **OnStop**-Methode ausgeführt wird, fünf Minuten vor seiner erzwungenen Beendigung abgeschlossen sein. Stellen Sie sicher, dass Ihr Code in diesem Zeitraum vollständig ausgeführt werden kann oder gefahrlos vorzeitig beendet werden kann.  
- Der Azure-Lastenausgleich beginnt, Datenverkehr an die Rolleninstanz weiterzuleiten, wenn die **RoleEntryPoint.OnStart**-Methode true zurückgibt. Daher sollten Sie den gesamten Initialisierungscode in die **OnStart**-Methode einfügen, damit Rolleninstanzen, die nicht erfolgreich initialisiert werden, keinen Datenverkehr empfangen.
- Sie können Startaufgaben zusätzlich zu den Methoden der **RoleEntryPoint**-Klasse verwenden. Sie sollten Startaufgaben verwenden, um Einstellungen zu initialisieren, die im Azure-Lastenausgleich geändert werden müssen, da diese Aufgaben ausgeführt werden, bevor die Rolle Anforderungen empfängt. Weitere Informationen finden Sie unter [Ausführen von Startaufgaben in Azure](cloud-services-startup-tasks.md).
- Wenn in einer Startaufgabe ein Fehler auftritt, kann die Rolle dadurch zu fortwährenden Neustarts gezwungen werden. Dies kann verhindern, dass ein VIP-Swap zu einer zuvor bereitgestellten Version ausgeführt wird, da hierfür exklusiv auf die Rolle zugegriffen werden muss und der exklusive Zugriff nicht gewährt werden kann, solange die Rolle neu gestartet wird. Problembehebung:
	-  Fügen Sie den folgenden Code am Anfang der **OnStart**- und **Run**-Methoden in die Rolle ein:

	```C#
	var freeze = CloudConfigurationManager.GetSetting("Freeze");
	if (freeze != null)
	{
		if (Boolean.Parse(freeze))
	  	{
		    Thread.Sleep(System.Threading.Timeout.Infinite);
		}
	}
	```

   - Fügen Sie die Definition der **Freeze**-Einstellung als booleschen Wert den Dateien "ServiceDefinition.csdef" und "ServiceConfiguration.*.cscfg" für die Rolle hinzu, und legen Sie sie auf **false** fest. Wenn die Rolle in einen wiederholten Neustartmodus wechselt, können Sie diese Einstellung in **true** ändern, um die Ausführung der Rolle anzuhalten und zuzulassen, dass sie gegen eine frühere Version ausgetauscht wird.

## Überlegungen zur Resilienz

Hintergrundaufgaben müssen stabil sein, um der Anwendung zuverlässige Dienste zur Verfügung stellen zu können. Berücksichtigen Sie beim Planen und Entwerfen von Hintergrundaufgaben die folgenden Punkte:

- Hintergrundaufgaben müssen in der Lage sein, Neustarts der Rolle oder des Dienstes ordnungsgemäß zu verarbeiten, ohne Daten zu beschädigen oder Inkonsistenzen in die Anwendung einzuführen. Bei langwierigen oder aus mehreren Schritten bestehenden Aufgaben sollten Sie die Verwendung von _Prüfpunkten_ in Betracht ziehen, um den Status von Aufträgen im beständigen Speicher oder als Nachrichten in einer Warteschlange zu speichern, wenn dies zweckmäßig ist. Beispielsweise können Sie Statusinformationen in einer Nachricht in einer Warteschlange speichern und diese Statusinformationen inkrementell mit dem Aufgabenstatus aktualisieren, sodass die Aufgabe ab dem letzten bekannten Prüfpunkt verarbeitet werden kann, statt ganz von vorn neu gestartet werden zu müssen. Bei Verwendung von Azure Service Bus-Warteschlangen können Sie dieses Szenario mithilfe von Nachrichtensitzungen ermöglichen. Sitzungen ermöglichen Ihnen, den Anwendungsverarbeitungsstatus mithilfe der Methoden [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) und [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx) zu speichern und abzurufen. Weitere Informationen zum Entwerfen von zuverlässigen mehrstufigen Prozessen und Workflows finden Sie unter [Muster für Scheduler-Agent-Supervisor](http://msdn.microsoft.com/library/dn589780.aspx).
- Wenn Sie Web- oder Workerrollen zum Hosten mehrerer Hintergrundaufgaben verwenden, entwerfen Sie Ihre Überschreibung der **Run**-Methode so, dass fehlgeschlagene oder angehaltene Aufgaben erkannt und neu gestartet werden. Falls dies nicht praktikabel ist und Sie eine Workerrolle verwenden, erzwingen Sie den Neustart der Workerrolle, indem Sie die **Run**-Methode beenden.
- Bei der Verwendung von Warteschlangen zur Kommunikation mit Hintergrundaufgaben können die Warteschlangen in Zeiten, in denen die Auslastung der Anwendung höher als üblich ist, als Puffer zum Speichern von Anforderungen, die an die Aufgaben gesendet werden, fungieren. Dadurch können die Aufgaben in Zeiträumen mit einer geringeren Auslastung mit der Benutzeroberfläche gleich ziehen. Es bedeutet auch, dass die Benutzeroberfläche nicht durch die Wiederverwendung der Rolle blockiert wird. Weitere Informationen finden Sie unter [Warteschlangenbasiertes Lastenausgleichsmuster](http://msdn.microsoft.com/library/dn589783.aspx). Wenn einige Aufgaben wichtiger als andere sind, ziehen Sie in Betracht, durch eine Implementierung des [Prioritätswarteschlangenmusters](http://msdn.microsoft.com/library/dn589794.aspx) sicherzustellen, dass diese Aufgaben vor den weniger wichtigen Aufgaben ausgeführt werden.
- Hintergrundaufgaben, die durch Nachrichten initiiert werden oder Nachrichten verarbeiten, müssen so entworfen werden, dass sie Inkonsistenzen behandeln können, z. B. Nachrichten, die nicht in der ordnungsgemäßen Reihenfolge eintreffen, oder Nachrichten, die wiederholt einen Fehler verursachen (so genannte _nicht verarbeitbare Nachrichten_), und Nachrichten, die mehrmals übermittelt werden. Beachten Sie Folgendes:
  - Nachrichten, die in einer bestimmten Reihenfolge verarbeitet werden müssen, z. B. Nachrichten, die Daten basierend auf dem vorhandenen Wert (z. B. durch Hinzufügen eines Werts zu einem vorhandenen Wert) ändern, treffen möglicherweise nicht in der ursprünglichen Reihenfolge ein, in der sie gesendet wurden. Stattdessen können sie auch von anderen Instanzen einer Hintergrundaufgabe in einer anderen Reihenfolge je nach Auslastung der verschiedenen Instanzen behandelt werden. Nachrichten, die in einer bestimmten Reihenfolge verarbeitet werden müssen, sollten eine Sequenznummer, einen Schlüssel oder einen anderen Indikator enthalten, mit dessen Hilfe in Hintergrundaufgaben sichergestellt werden kann, dass sie in der richtigen Reihenfolge verarbeitet werden. Bei Verwendung von Azure Service Bus können Sie mit Nachrichtensitzungen die Reihenfolge der Bereitstellung gewährleisten. Es ist in der Regel jedoch effizienter, den Prozess so zu entwerfen, dass die Reihenfolge der Nachrichten nicht von Bedeutung ist.
  - In der Regel nimmt eine Hintergrundaufgabe die Nachrichten in der Warteschlange kurz in Augenschein (Peek), wodurch diese vorübergehend vor anderen Nachrichten-Consumern verborgen werden, und löscht die Nachrichten, nachdem sie erfolgreich verarbeitet wurden. Fällt eine Hintergrundaufgabe während der Verarbeitung einer Nachricht aus, wird diese Nachricht nach Ablauf des Peek-Timouts wieder in der Warteschlange angezeigt und von einer anderen Instanz der Aufgabe oder während des nächsten Verarbeitungszyklus dieser Instanz verarbeitet. Wenn die Nachricht beständig einen Fehler im Consumer verursacht, blockiert sie die Aufgabe, die Warteschlange und schließlich die Anwendung selbst, wenn die Warteschlange voll ist. Daher ist es wichtig, dass nicht verarbeitbare Nachrichten erkannt und aus der Warteschlange entfernt werden. Bei Verwendung von Azure Service Bus können Nachrichten, die einen Fehler verursachen, automatisch oder manuell an eine zugeordnete Warteschlange für unzustellbare Nachrichten verschoben werden.
  - Warteschlangen verfügen garantiert über Übermittlungsmechanismen für eine _mindestens einmalige_ Zustellung, aber sie können die gleiche Nachricht mehrmals übermitteln. Wenn eine Hintergrundaufgabe ausfällt, nachdem eine Nachricht verarbeitet, aber bevor diese aus der Warteschlange gelöscht wurde, wird die Nachricht wieder zur Verarbeitung verfügbar. Hintergrundaufgaben sollte idempotent sein, d. h. dass die mehrmalige Verarbeitung derselben Nachricht keinen Fehler oder keine Inkonsistenzen in den Daten der Anwendung verursacht. Einige Vorgänge sind naturgemäß idempotent, z. B. das Festlegen einer gespeicherten Einstellung auf einen bestimmten neuen Wert. Jedoch führen andere Vorgänge zu Inkonsistenzen, z. B. das Hinzufügen eines Werts zu einem vorhandenen gespeicherten Wert, ohne zu überprüfen, ob der gespeicherte Wert dem Wert entspricht, der in der Nachricht ursprünglich gesendet wurde. Azure Service Bus-Warteschlangen können so konfiguriert werden, dass doppelte Nachrichten automatisch entfernt werden.
  - Einige Messagingsysteme, wie z. B. Azure-Speicherwarteschlangen und Azure Service Bus-Warteschlangen unterstützen eine Eigenschaft, die angibt, wie oft eine Nachricht aus der Warteschlange gelesen wurde. Dies kann bei der Verarbeitung wiederholter und nicht verarbeitbarer Nachrichten hilfreich sein. Weitere Informationen finden Sie unter [Einführung in asynchrone Nachrichten](http://msdn.microsoft.com/library/dn589781.aspx) und [Idempotenzmuster](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/).

## Überlegungen zur Skalierung und Leistung

Hintergrundaufgaben müssen ausreichend Leistung bieten, damit sichergestellt ist, dass sie nicht die Anwendung blockieren oder durch eine verzögerte Verarbeitung bei einem stark ausgelasteten System zu Inkonsistenzen führen. In der Regel wird die Leistung durch die Skalierung der Serverinstanzen, die die Hintergrundaufgaben hosten, verbessert. Berücksichtigen Sie beim Planen und Entwerfen von Hintergrundaufgaben die folgenden Punkte in Bezug auf Skalierbarkeit und Leistung:

- Azure unterstützt die automatische Skalierung (horizontales Skalieren und Rückgängigmachen der Skalierung) basierend auf der aktuellen Nachfrage und Last oder nach einem vordefinierten Zeitplan für Web-Apps, Cloud Services-Web- und Workerrollen sowie Bereitstellungen, die auf virtuellen Computern gehostet sind. Nutzen Sie diese Funktion, um sicherzustellen, dass die Anwendung als Ganzes über ausreichend Leistungsfähigkeit verfügt, und gleichzeitig die Laufzeitkosten zu minimieren.
- Wenn Hintergrundaufgaben eine andere Leistungsfähigkeit als andere Teile einer Cloud Services-Anwendung haben (z. B. die Benutzeroberfläche oder Komponenten wie die Datenzugriffsschicht), kann durch das gemeinsame Hosten der Hintergrundaufgaben in einer separaten Workerrolle erreicht werden, dass die Benutzeroberfläche und die Hintergrundaufgabenrollen unabhängig voneinander skaliert werden, um die Last zu verwalten. Wenn sich die Leistungsfähigkeit verschiedener Hintergrundaufgaben deutlich voneinander unterscheidet, sollten Sie in Erwägung ziehen, diese in verschiedene Workerrollen aufzuteilen und die verschiedenen Rollentypen voneinander getrennt zu skalieren. Verglichen mit der Kombination aller Aufgaben in einer kleineren Anzahl von Rollen kann dies jedoch zu höheren Laufzeitkosten führen.
- Eine einfache Skalierung der Rollen ist u. U. nicht ausreichend, um Leistungsverluste unter Last zu verhindern. Sie müssen möglicherweise auch Speicherwarteschlangen und andere Ressourcen skalieren, um zu vermeiden, dass ein einzelner Zugriffspunkt in der gesamten Verarbeitungskette zum Engpass wird. Berücksichtigen Sie auch andere Einschränkungen, z. B. den maximalen Durchsatz des Speichers sowie andere Dienste der Anwendung und die Hintergrundaufgaben, die sich darauf stützen.
- Hintergrundaufgaben müssen für die Skalierung konzipiert werden. Beispielsweise müssen sie dynamisch die Anzahl der verwendeten Speicherwarteschlangen erkennen können, um Nachrichten überwachen oder an die entsprechende Warteschlange senden zu können.
- In der Standardeinstellung werden WebJobs zusammen mit ihrer zugehörigen Azure-Web-Apps-Instanz skaliert. Wenn ein WebJob jedoch nur als Einzelinstanz ausgeführt werden soll, können Sie eine Datei namens "Settings.job" erstellen, welche die JSON-Daten **{"Is\_singleton": true}** enthält. Damit wird Azure gezwungen, nur eine WebJob-Instanz auszuführen, selbst wenn mehrere Instanzen der zugehörigen Web-App vorhanden sind. Diese Technik kann bei geplanten Aufträgen, die nur als einzelne Instanz ausgeführt werden müssen, hilfreich sein.

## Verwandte Muster

- [Einführung in asynchrone Nachrichten](http://msdn.microsoft.com/library/dn589781.aspx)
- [Anleitungen für die automatische Skalierung](http://msdn.microsoft.com/library/dn589774.aspx)
- [Muster für eine ausgleichende Transaktion](http://msdn.microsoft.com/library/dn589804.aspx)
- [Muster für konkurrierende Consumer](http://msdn.microsoft.com/library/dn568101.aspx)
- [Leitfaden zur Serverpartitionierung](http://msdn.microsoft.com/library/dn589773.aspx)
- [Serverressourcen-Konsolidierungsmuster](http://msdn.microsoft.com/library/dn589778.aspx)
- [Gatekeeper-Muster](http://msdn.microsoft.com/library/dn589793.aspx)
- [Muster für die Auswahl einer übergeordneten Instanz](http://msdn.microsoft.com/library/dn568104.aspx)
- [Muster für Pipes und Filter](http://msdn.microsoft.com/library/dn568100.aspx)
- [Prioritätswarteschlangenmuster](http://msdn.microsoft.com/library/dn589794.aspx)
- [Warteschlangenbasiertes Lastenausgleichsmuster](http://msdn.microsoft.com/library/dn589783.aspx)
- [Muster für Scheduler-Agent-Supervisor](http://msdn.microsoft.com/library/dn589780.aspx)

## Weitere Informationen

- [Skalieren von Azure-Anwendungen mit Workerrollen](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [Ausführen von Hintergrundaufgaben](http://msdn.microsoft.com/library/ff803365.aspx)
- [Azure Role Startup Life Cycle](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (Lebenszyklus eines Azure-Rollenstarts, Blogbeitrag)
- [Lebenszyklus von Azure Cloud Services-Rollen](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (Video)
- [Erste Schritte mit dem Azure WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md)
- [Azure-Warteschlangen und Service Bus-Warteschlangen – Vergleich und Gegenüberstellung](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Aktivieren der Diagnose in einem Clouddienst](cloud-services-dotnet-diagnostics.md)

<!---HONumber=AcomDC_0128_2016-->