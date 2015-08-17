<properties
   pageTitle="Hinzufügen von Service Fabric-Integritätsberichten"
   description="Beschreibt, wie benutzerdefinierte Integritätsberichte an Integritätsentitäten von Azure Service Fabric gesendet werden. Enthält Empfehlungen zum Entwerfen und Implementieren hochwertiger Integritätsberichte."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/16/2015"
   ms.author="oanapl"/>

# Hinzufügen von Service Fabric-Integritätsberichten
Service Fabric bietet ein [Integritätsmodell](service-fabric-health-introduction.md), das fehlerhafte Cluster- oder Anwendungsbedingungen auf bestimmten Entitäten kennzeichnet. Dies erfolgt mithilfe von **Integritätsberichterstattern** (Systemkomponenten und Watchdogs). Das Ziel ist die einfache und schnelle Diagnose und Reparatur. Dienstautoren müssen im Vorfeld an die Integrität denken. Jede Bedingung, die zur Beeinträchtigung der Integrität führen kann, sollte gemeldet werden, insbesondere wenn damit die Ursachen von Problemen ermittelt werden können. Dies erspart einiges an Debugging- und Untersuchungsaufwand, sobald der Dienst ordnungsgemäß in der Cloud ("privat" oder "Azure") ausgeführt wird.

Die Service Fabric-Berichterstatter überwachen Bedingungen, die von Bedeutung sind. Sie erstatten basierend auf ihrer lokalen Anzeige Bericht über diese Bedingungen. Der [Integritätsspeicher](service-fabric-health-introduction.md#Health-Store) aggregiert die von allen Berichterstattern gesendeten Integritätsdaten, um die globale Integrität der Entitäten zu ermitteln. Das Modell ist umfassende, flexibel und einfach anzuwenden. Die Qualität der Integritätsberichte bestimmt, wie genau die Integritätsanzeige des Clusters ist. Falsch positive Ergebnisse, die irrtümlicherweise Fehler anzeigen, können Aktualisierungen oder andere Dienste beeinträchtigen, die Integritätsdaten verwenden, wie etwa Reparaturdienste oder Warnmechanismen. Daher sind einige Überlegungen erforderlich, um Bericht bereitzustellen, die wichtige Bedingungen auf bestmögliche Weise erfassen.

Beim Entwerfen und Implementieren der Integritätsberichterstellung haben Watchdogs und Systemkomponenten folgende Aufgaben:

- Sie definieren, welche Bedingung für sie von Bedeutung ist, wie sie überwacht wird und welche Auswirkungen sie auf die Cluster-/Anwendungsfunktionalität hat. Auf diese Weise werden die Eigenschaft des Integritätsberichts und der Integritätszustand definiert.

- Sie bestimmen die [Entität](service-fabric-health-introduction.md#health-entities-and-hierarchy), auf die der Bericht angewendet wird.

- Sie bestimmen, von wo aus die Berichterstellung erfolgen soll, entweder innerhalb des Diensts oder über einen internen oder externen Watchdog.

- Sie definieren eine Quelle zum Identifizieren des Berichterstatters.

- Sie wählen eine Strategie für die Berichterstellung (in regelmäßigen Abständen oder bei Übergängen). Empfohlen wird die Berichterstellung in regelmäßigen Abständen, da sie einen einfacheren Code erfordert und daher weniger fehleranfällig ist.

- Sie bestimmen, wie lange der Bericht für fehlerhafte Bedingungen im Integritätsspeicher bleibt und wie er gelöscht wird. Auf diese Weise werden die Gültigkeitsdauer und das Verhalten beim Entfernen von Berichten nach der Ablaufzeit definiert.

Wie bereits erwähnt, kann die Berichterstellung von folgenden Orten aus erfolgen:

- Vom überwachten Service Fabric-Dienstreplikat.

- Von internen, als Service Fabric-Dienst bereitgestellten Watchdogs. Es kann beispielsweise ein zustandsloser Service Fabric-Dienst verwendet werden, der Bedingungen überwacht und Berichte ausgibt. Watchdogs können auf allen Knoten bereitgestellt oder dem überwachten Dienst zugeordnet werden.

- Von internen Watchdogs aus, die auf den Service Fabric-Knoten ausgeführt werden, jedoch **nicht** als Service Fabric-Dienste implementiert werden.

- Von externen Watchdogs aus, welche die Ressource von **außerhalb** des Service Fabric-Clusters überwachen. Hierfür eignen sich beispielsweise Gomez-artige Überwachungsdienste.

> [AZURE.NOTE]Standardmäßig enthält das Cluster von den Systemkomponenten gesendete Integritätsberichte. Weitere Informationen finden Sie unter [Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Die Benutzerberichte müssen für bereits vom System erstellte [Integritätsentitäten](service-fabric-health-introduction.md#health-entities-and-hierarchy) gesendet werden.

Sobald das Design für die Integritätsberichterstellung feststeht, ist das Senden von Integritätsberichten einfach. Es kann über die API mit "FabricClient.HealthManager.ReportHealth", über PowerShell oder über REST erfolgen. Intern verwenden alle Methoden einen Integritätsclient, der in einem Fabric-Client enthalten ist. Zur Verbesserung der Leistung sind Konfigurationselemente für Batchberichte vorhanden.

> [AZURE.NOTE]Integritätsberichte sind synchronisiert und repräsentieren nur die auf der Clientseite durchgeführten Überprüfungen. Die Tatsache, dass der Bericht vom Integritätsclient akzeptiert wird, bedeutet nicht, dass er im Speicher angewendet wird. Er wird asynchron gesendet, ist möglicherweise mit anderen Berichten zusammengefasst, und die Verarbeitung auf dem Server kann fehlschlagen (z. B. aufgrund einer veralteten Sequenznummer oder weil die Entität, auf die der Bericht anzuwenden ist, gelöscht wurde).

## Integritätsclient
Die Integritätsberichte werden mithilfe eines Integritätsclients an den Integritätsspeicher innerhalb des Fabric-Clients gesendet. Der Integritätsclient kann mit folgenden Optionen konfiguriert werden:

- HealthReportSendInterval: Die Verzögerung zwischen dem Hinzufügen des Bericht zum Client und dem Senden des Berichts an den Integritätsspeicher. Die Berichte werden dabei zur Leistungsoptimierung in einer Nachricht zusammengefasst, anstatt für jeden Bericht eine eigene Nachricht zu senden. Standardwert: 30 Sekunden.

- HealthReportRetrySendInterval: Das Intervall, in dem der Integritätsclient kumulierte Integritätsberichte erneut an den Integritätsspeicher sendet. Standardwert: 30 Sekunden.

- HealthOperationTimeout: Das Timeout für eine an den Integritätsspeicher gesendete Berichtsnachricht. Wenn bei einer Nachricht eine Zeitüberschreitung auftritt, wiederholt der Integritätsclient den Sendevorgang, bis der Integritätsspeicher die Verarbeitung des Berichts bestätigt. Standardwert: 2 Minuten.

> [AZURE.NOTE]Wenn die Berichte zusammengefasst werden, muss der Fabric-Client mindestens für die Dauer von "HealthReportSendInterval" aktiv bleiben, um sicherzustellen, dass sie gesendet werden. Wenn die Nachricht verloren geht oder der Integritätsspeicher die Berichte aufgrund von Übertragungsfehlern nicht anwenden kann, muss der Fabric-Client länger aktiv bleiben, um einen erneuten Versuch zu ermöglichen.

Bei der Pufferung auf dem Client wird auf die Eindeutigkeit der Berichte geachtet. Beispiel: Wenn ein besonders schlechter Berichterstatter pro Sekunde 100 Berichte bezüglich derselben Eigenschaft auf derselben Entität sendet, werden die Berichte jeweils durch die zuletzt erhaltene Version ersetzt. In der Clientwarteschlange existiert stets nur ein solcher Bericht. Die Batchverarbeitung wird so konfiguriert, dass nur ein Bericht pro Sendeintervall an den Integritätsspeicher gesendet wird. Dies ist jeweils der zuletzt hinzugefügte Bericht, der den aktuellen Zustand der Entität widerspiegelt. Beim Erstellen eines Fabric-Clients können alle Konfigurationsparameter durch Übergabe von "FabricClientSettings" mit den gewünschten Werten für integritätsbezogene Einträge festgelegt werden.

Im Folgenden wird ein Fabric-Client erstellt und festgelegt, dass die Berichte unmittelbar nach dem Hinzufügen gesendet werden sollen. Bei wiederholbaren Fehlern oder Timeouts wird alle 40 Sekunden eine Wiederholung ausgeführt.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Beim Erstellen einer Verbindung zu einem Cluster über PowerShell können dieselben Parameter festgelegt werden. Folgendes startet eine Verbindung mit einem lokalen Cluster:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

> [AZURE.NOTE]Um sicherzustellen, dass nicht autorisierte Dienste keine Integritätsberichte für die im Cluster enthaltenen Entitäten ausgeben, kann der Server so konfiguriert werden, dass er nur Anforderungen von sicheren Clients akzeptiert. Da die Berichterstellung über den Fabric-Client erfolgt, muss dieser Sicherheitsfunktionen aktiviert haben, um mit dem Cluster (z. B. mit Kerberos oder der Zertifikatauthentifizierung) kommunizieren zu können.

## Entwerfen der Integritätsberichterstellung
Der erste Schritt zum Generieren hochwertiger Berichte ist das Identifizieren von Bedingungen, welche die Dienstintegrität beeinträchtigen können. Jegliche Bedingung, die beim Auftreten von Problemen im Dienst oder Cluster – oder noch besser davor – zu deren Erkennung beiträgt, kann Milliarden Euro einsparen. Profitieren Sie von weniger Ausfallzeiten, weniger Untersuchungen und Behebungen von Problemen zu nächtlichen Stunden sowie einer hohen Kundenzufriedenheit.

Nachdem die Bedingungen identifiziert wurden, müssen Watchdog-Autoren die beste Möglichkeit für deren Überwachung ermitteln, um ein ausgeglichenes Verhältnis zwischen Verwaltung und Nützlichkeit zu erzielen. Betrachten Sie beispielsweise einen Dienst, der mithilfe verschiedener temporärer Dateien komplexe Berechnungen auf einer Freigabe durchführt. Ein Watchdog kann die Freigabe überwachen, um sicherzustellen, dass genügend Speicherplatz verfügbar ist. Er kann den Eingang von Benachrichtigungen bezüglich Datei- bzw. Verzeichnisänderungen überwachen. Er kann eine Warnung ausgeben, wenn ein anfänglicher Schwellenwert erreicht wird, bzw. einen Fehler, wenn die Freigabe voll ist. Bei einer Warnung kann ein Reparatursystem mit dem Bereinigen von älteren Dateien auf der Freigabe beginnen. Bei einem Fehler kann ein Reparatursystem das Dienstreplikat auf einen anderen Knoten verschieben. Beachten Sie, wie die Bedingungszustände hinsichtlich der Integrität beschrieben werden: Welchen Zustand hat eine Bedingung, die als fehlerfrei oder fehlerhaft (Warnung oder Fehler) angesehen werden kann.

Nachdem die Überwachungsdetails eingerichtet wurden, müssen Watchdog-Autoren ermitteln, wie sie den Watchdog implementieren. Wenn die Bedingungen von innerhalb des Diensts bestimmt werden können, kann der Watchdog Teil der überwachten Diensts an sich sein. Der Dienstcode kann beispielsweise bei jedem Schreiben einer Datei mithilfe eines lokalen Fabric-Clients die Freigabenutzung und den Bericht prüfen. Der Vorteil dieses Ansatzes ist die einfache Berichterstellung. Es muss darauf geachtet werden, dass Watchdog-Probleme nicht die Funktionalität des Diensts beeinträchtigen.

Die Berichterstellung von innerhalb des überwachten Diensts ist nicht immer möglich. Ein Watchdog innerhalb des Diensts erkennt möglicherweise die Bedingungen nicht: Er verfügt nicht über die erforderliche Logik oder die erforderlichen Daten für die Bestimmung, oder der Aufwand für die Überwachung der Bedingungen ist zu hoch. Es kann auch sein, dass nicht dienstspezifische Bedingungen die Interaktion zwischen Diensten beeinträchtigen. Als weitere Option können Watchdogs im Cluster als separate Prozesse ausgeführt werden. Die Watchdogs überwachen einfach die Bedingungen und erstellen Berichte, ohne die Hauptdienste in irgendeiner Weise zu beeinträchtigen. Diese Watchdogs können beispielsweise als zustandslose Dienste in dieselbe Anwendung implementiert, auf allen Knoten oder auf denselben Knoten wie der Dienst bereitgestellt werden.

Mitunter ist es jedoch nicht möglich, einen Watchdog im Cluster auszuführen. Wenn es sich bei den überwachten Bedingungen um die Verfügbarkeit oder die Funktionalität des Diensts auf der Benutzerseite handelt, empfiehlt es sich, die Watchdogs am selben Ort wie die Benutzerclients auszuführen. Auf diese Weise können die Vorgänge so getestet werden, wie Benutzer sie aufrufen. Beispiel: Ein Watchog kann außerhalb des Clusters ausgeführt werden, wo er Anforderungen an den Dienst ausgibt und die Latenz sowie die Richtigkeit des Ergebnisses prüft (z. B. ob bei einem Rechnerdienst 2 + 2 innerhalb eines angemessenen Zeitraums den Wert 4 zurückgibt).

Nachdem Sie die Watchdog-Details festgelegt haben, wählen Sie eine Quell-ID, die den Watchdog eindeutig identifiziert. Wenn im Cluster mehrere Watchdogs vom gleichen Typ enthalten sind, müssen sie entweder unterschiedliche Entitäten überwachen oder, bei der Überwachung derselben Entität, sicherstellen, dass die Quell-ID oder die Eigenschaft unterschiedlich ist, damit mehrere Berichte gleichzeitig vorhanden sein können. Die Eigenschaft des Integritätsberichts sollte die überwachte Bedingung erfassen. Im vorangegangenen Beispiel kann die Eigenschaft "ShareSize" sein. Wenn mehrere Daten auf die gleiche Bedingung angewendet werden, sollte die Eigenschaft einige dynamische Informationen beinhalten, sodass mehrere Berichte gleichzeitig vorhanden sein können. Beispiel: Wenn mehrere Freigaben überwacht werden müssen, kann der Name der Eigenschaft "ShareSize-Freigabename" lauten.

> [AZURE.NOTE]Der Integritätsspeicher sollte **nicht** zum Speichern von Zustandsinformationen verwendet werden. Hinsichtlich der Integrität sollten nur integritätsbezogene Informationen gemeldet werden, die sich auf die Integritätsevaluierung einer Entität auswirken. Der Integritätsspeicher wurde nicht als Allzweckspeicher entwickelt. Er aggregiert mithilfe von Integritätsevaluierungslogik alle Daten im Integritätszustand. Das Senden von Informationen, die sich nicht auf die Integrität beziehen (z. B. Zustandsberichte mit dem Integritätsstatus "Ok") wirkt sich nicht auf den aggregierten Integritätsstatus aus, kann jedoch die Leistung des Integritätsspeichers beeinträchtigen.

Als Nächstes legen Sie fest, für welche Entität Berichte erstellt werden sollen. In den meisten Fällen ist dies aufgrund der Bedingung offensichtlich. Wählen Sie die Entität mit der bestmöglichen Granularität. Wenn sich eine Bedingung auf alle Replikate in einer Partition auswirkt, erstellen Sie Berichte nicht über den Dienst, sonder über die Partition. Es gibt Ausnahmefällen, in denen weitere Überlegungen erforderlich sind. Wenn sich die Bedingung auf eine Entität (z. B. ein Replikat) auswirkt, jedoch der Wunsch besteht, sie für mehr als nur die Replikatgültigkeitsdauer zu kennzeichnen, sollte die Berichterstellung auf der Partition erfolgen. Andernfalls werden beim Löschen des Replikats alle damit verbundenen Berichte aus dem Speicher bereinigt. Dies bedeutet, dass Watchdog-Autoren auch die Lebensdauer der Entität und des Berichts berücksichtigen müssen. Es muss klar sein, wann ein Bericht aus einem Speicher bereinigt werden soll (z. B. wenn ein für eine Entität gemeldeter Fehler nicht mehr zutrifft).

Betrachten wir anhand eines Beispiels, wie die oben aufgeführten Punkte zusammengesetzt werden. Nehmen wir eine Service Fabric-Anwendung, besteht aus einem zustandsbehafteten, persistenten Masterdienst und zustandslosen Slavediensten, die auf allen Knoten bereitgestellt sind (je ein Slavediensttyp für einen Aufgabentyp). Der Master beinhaltet eine Verarbeitungswarteschlange mit Befehlen, die von Slaves ausgeführt werden sollen. Die Slaves führen die eingehenden Anforderungen aus und senden Bestätigungen ("Acks") zurück. Eine überwachbare Bedingung ist die Länge der Verarbeitungswarteschlange des Masters. Wenn die Länge der Masterwarteschlange einen Schwellenwert erreicht, wird eine Warnung ausgegeben, da die Slaves die Last nicht verarbeiten können. Wenn die Warteschlange die maximale Länge erreicht hat und Befehle abgebrochen werden, wird ein Fehler gemeldet, da der Dienst nicht wiederhergestellt werden kann. Die Berichte können für die Eigenschaft "QueueStatus" erstellt werden. Der Watchdog befindet sich innerhalb des Diensts. Er hat regelmäßig Berichte über das primäre Masterreplikat gesendet. Die Gültigkeitsdauer beträgt 2 Minuten, und der Bericht wird alle 30 Sekunden gesendet. Wenn das primäre Replikat ausfällt, wird der Bericht automatisch aus dem Speicher bereinigt. Wenn das Dienstreplikat aktiv aber blockiert ist oder andere Probleme hat, verliert der Bericht im Integritätsspeicher seine Gültigkeit und die Entität wird als fehlerhaft bewertet.

Eine weitere Bedingung, die überwacht werden kann, ist die Ausführungszeit der Aufgabe. Der Master verteilt die Aufgaben entsprechend des Aufgabentyps an die Slaves. Je nach Entwurf kann der Master von den Slaves den Aufgabenstatus abfragen oder darauf warten, dass Slaves nach Beenden der Aufgabe "Acks" zurück senden. In letzterem Fall ist darauf zu achten, dass Situationen erkannt werden, in denen Slaves ausfallen oder Nachrichten verloren gehen. Der Master kann eine Ping-Anforderung an denselben Slave senden, der den Status zurücksendet. Wenn kein Status empfangen wird, ist möglicherweise ein Fehler aufgetreten. Planen Sie den Task in diesem Fall neu. Dabei wird davon ausgegangen, dass die Aufgaben idempotent sind. Wir können die überwachte Bedingung als Warnung übersetzen, wenn die Aufgabe nicht innerhalb eines bestimmten Zeitraums "t1" (z. B. 10 Minuten) ausgeführt wird. Sie kann auch als Fehler gewertet werden, wenn die Aufgabe nicht innerhalb des Zeitraums "t2" (z. B. 20 Minuten) abgeschlossen wird. Die Berichterstellung kann auf verschiedene Arten erfolgen:

- Das primäre Masterreplikat gibt regelmäßig Berichte über sich selbst aus. Es kann eine gemeinsame Eigenschaft für alle ausstehenden Aufgaben in der Warteschlange vorhanden sein: Wenn mindestens eine Aufgabe länger dauert, wird für die Eigenschaft "PendingTasks" entsprechend eine Warnung oder ein Fehler gemeldet. Wenn keine ausstehenden Aufgaben vorhanden sind oder alle Aufgaben bereits gestartet wurden, melden Sie "Ok". Die Aufgaben sind persistent. Wenn das primäre Replikat ausfällt, kann das darauf hin neu ernannte primäre Replikat weiterhin ordnungsgemäß Berichte erstellen.

- Ein anderer Watchdog-Prozess (in der Cloud oder extern) überprüft extern basierend auf dem gewünschten Aufgabenergebnis, ob die Aufgaben abgeschlossen wurden. Wenn die Schwellenwerte nicht beachtet werden, soll ein Bericht über den Masterdienst erstellt werden. Erstellen Sie einen Bericht für jede Aufgabe, und beziehen Sie die Aufgabenkennung ein (z. B. "PendingTask+taskid"). Melden Sie nur Fehlerzustände. Setzen Sie die Gültigkeitsdauer auf wenige Minuten, und markieren Sie die bei Ablauf zu entfernenden Berichte, um die Bereinigung sicherzustellen.

- Der Slave, der eine Aufgabe ausführt, meldet, wenn die Ausführung länger als erwartet dauert. Er erstellt für die Dienstinstanz Berichte über die Eigenschaft "PendingTasks". Auf diese Weise wird die fehlerhafte Dienstinstanz ermittelt, aber nicht die Situation erfasst, in der die Instanz ausfällt. Die Berichte werden dabei bereinigt. Es können Berichte über den Slavedienst erstellt werden. Wenn der Slave die Aufgabe abschließt, löscht die Slave-Instanz den Bericht aus dem Speicher. Dabei wird nicht die Situation erfasst, in der die Bestätigungsnachricht verloren gegangen ist, und die Aufgabe ist aus Mastersicht nicht abgeschlossen.

In den oben beschriebenen Fällen werden jedoch Berichte erstellt. Die Erfassung erfolgt während der Integritätsevaluierung in der Anwendungsintegrität.

## Regelmäßig oder bei einem Übergang erstellte Berichte
Mithilfe des Integritätsberichterstellungsmodells können Watchdogs Berichte in regelmäßigen Abständen oder bei Übergängen senden. Empfohlen wird die regelmäßige Methode, da der Code erheblich einfacher und daher weniger fehleranfällig ist. Die Watchdogs sollten so einfach wie möglich gehalten werden, um Fehler zu vermeiden, die falsche Berichte auslösen. Ein falscher Fehlerbericht wirkt sich auf die Integritätsevaluierung und auf Szenarien in Verbindung mit der Integrität aus, wie etwa Aktualisierungen. Durch falsche Berichte bezüglich der Fehlerfreiheit werden Probleme im Cluster ausgeblendet, was nicht erwünscht ist.

Für die regelmäßige Berichterstellung kann der Watchdog mit einem Timer implementiert werden. Bei einem Timer-Rückruf kann der Watchdog den Zustand überprüfen und basierend auf den aktuellen Zustand einen Bericht senden. Sie brauchen nicht zu sehen, welcher Bericht zuvor gesendet wurde. Auch das Messaging muss nicht konfiguriert werden. Der Integritätsclient verfügt über eine hilfreiche Batchverarbeitungslogik. Solange der Integritätsclient aktiviert bleibt, führt er intern Wiederholversuche durch, bis der Bericht vom Integritätsspeicher bestätigt wurde oder der Watchdog einen neueren Bericht mit derselben Entität, Eigenschaft und Quelle generiert.

Die Berichterstellung bei Übergängen erfordert eine sorgfältige Zustandsbehandlung. Der Watchdog überwacht bestimmte Bedingungen und meldet nur, wenn sich diese ändern. Der Vorteil ist, dass weniger Berichte erforderlich sind. Nachteilig ist die komplexe Logik des Watchdogs. Die Bedingungen oder die Berichte müssen verwaltet werden, damit sie hinsichtlich Zustandsänderungen überprüft werden können. Bei einem Failover muss darauf geachtet werden, dass ein möglicherweise noch nicht gesendeter Bericht gesendet wird (der in der Warteschlange vorhanden ist, aber noch nicht an den Integritätsspeicher gesendet wurde). Die Sequenznummer muss sich stetig erhöhen, da die Berichte andernfalls aufgrund von Veraltung zurückgewiesen werden. In den seltenen Fällen von Datenverlusten müssen möglicherweise der Zustand des Berichterstatters und des Integritätsspeichers synchronisiert werden.

## Implementieren der Integritätsberichterstellung
Sobald die Entitäts- und Berichtdetails geklärt sind, können Integritätsberichte über die API, PowerShell oder REST gesendet werden.

### API
Für die Berichterstellung über die API müssen Benutzer einen Integritätsbericht für den Entitätstyp erstellen, für den die Berichterstellung durchgeführt werden soll. Diesen übergeben sie anschließend an einen Integritätsclient.

Das folgende Beispiel zeigt die regelmäßige Berichterstellung durch einen Watchdog innerhalb des Clusters. Der Watchdog prüft, ob auf eine externe Ressource innerhalb eines Knotens zugegriffen werden kann. Die Ressource wird von einem Dienstmanifest innerhalb der Anwendung benötigt. Wenn die Ressource nicht verfügbar ist, können die anderen Dienste innerhalb der Anwendung ordnungsgemäß funktionieren. Aus diesem Grund wird der Bericht über die bereitgestellte Dienstpaketentität regelmäßig alle 30 Sekunden gesendet.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snipet brevity.
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### PowerShell
Benutzer können Integritätsberichte mit "Send-ServiceFabric*EntityType*HealthReport" senden.

Das folgende Beispiel zeigt die regelmäßige Berichterstellung für CPU-Werte auf einem Knoten. Die Berichte sollten alle 30 Sekunden gesendet werden, da sie eine Gültigkeitsdauer von 2 Minuten haben. Wenn sie ablaufen, bedeutet dies, dass der Berichterstatter Probleme erkannt hat und der Knoten als fehlerhaft bewertet wird. Wenn die CPU den Schwellenwert überschreitet, enthält der Bericht Warnungen bezüglich des Integritätszustands. Überschreitet die CPU den Schwellenwert über einen konfigurierten Zeitraum hinaus, wird ein Fehler gemeldet. Andernfalls sendet der Berichterstatter "Ok".

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

Im folgenden Beispiel wird eine vorübergehende Warnung für ein Replikat gemeldet. Dabei werden zunächst die Partitions-ID und die Replikat-ID für den gewünschten Dienst abgerufen. Anschließen wird ein Bericht von PowershellWatcher über die Eigenschaft "ResourceDependency" gesendet. Der Bericht ist nur 2 Minuten lang von Bedeutung, wonach er automatisch aus dem Speicher entfernt wird.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

## Nächste Schritte

Basierend auf den Integritätsdaten können Dienstautoren und Cluster- bzw. Anwendungsadministratoren Möglichkeiten für die Nutzung der Informationen erwägen. Sie können beispielsweise Warnungen auf Grundlage des Integritätszustands einrichten, um schwerwiegende Probleme abzufangen, bevor Ausfälle provoziert werden. Sie können automatische Reparatursysteme einrichten, um Probleme automatisch zu beheben.

[Einführung in Service Fabric-Integritätsüberwachung](service-fabric-health-introduction.md)

[Gewusst wie: Anzeigen von Service Fabric-Integritätsberichten](service-fabric-view-entities-aggregated-health.md)

[Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Gewusst wie: Lokales Überwachen und Diagnostizieren von Diensten](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md)
 

<!---HONumber=August15_HO6-->