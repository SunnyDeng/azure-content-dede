<properties
   pageTitle="Hinzufügen von Service Fabric-Integritätsberichten | Microsoft Azure"
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
   ms.date="01/26/2016"
   ms.author="oanapl"/>

# Hinzufügen von benutzerdefinierten Service Fabric-Integritätsberichten
Azure Service Fabric bietet ein [Integritätsmodell](service-fabric-health-introduction.md), das fehlerhafte Cluster- und Anwendungsbedingungen auf bestimmten Entitäten kennzeichnet. Dies erfolgt mithilfe von **Integritäts-Reportern** (Systemkomponenten und Watchdogs). Das Ziel ist die einfache und schnelle Diagnose und Reparatur. Dienstautoren müssen im Vorfeld an die Integrität denken. Jede Bedingung, die zur Beeinträchtigung der Integrität führen kann, sollte gemeldet werden, insbesondere wenn damit die Ursachen von Problemen ermittelt werden können. Dies erspart viel Zeit und Aufwand beim Debuggen und Untersuchen, sobald der Dienst ordnungsgemäß in der Cloud (privat oder Azure) ausgeführt wird.

Die Service Fabric-Berichterstatter überwachen Bedingungen, die von Bedeutung sind. Sie erstatten basierend auf ihrer lokalen Anzeige Bericht über diese Bedingungen. Der [Integritätsspeicher](service-fabric-health-introduction.md#Health-Store) aggregiert die von allen Reportern gesendeten Integritätsdaten, um die globale Integrität der Entitäten zu ermitteln. Das Modell ist umfassend, flexibel und einfach anzuwenden. Die Qualität der Integritätsberichte bestimmt die Genauigkeit der Integritätsanzeige des Clusters. Falsch positive Ergebnisse, die irrtümlicherweise Fehler anzeigen, können Upgrades oder andere Dienste beeinträchtigen, die Integritätsdaten verwenden. Dazu zählen Reparaturdienste oder Warnmechanismen. Daher sind einige Überlegungen erforderlich, um Bericht bereitzustellen, die wichtige Bedingungen auf bestmögliche Weise erfassen.

Beim Entwerfen und Implementieren der Integritätsberichterstellung haben Watchdogs und Systemkomponenten folgende Aufgaben:

- Sie definieren, welche Bedingung für sie von Bedeutung ist, wie sie überwacht wird und welche Auswirkungen sie auf die Cluster- oder Anwendungsfunktionalität hat. Auf diese Weise werden die Eigenschaft des Integritätsberichts und der Integritätszustand definiert.

- Sie bestimmen die [Entität](service-fabric-health-introduction.md#health-entities-and-hierarchy), für die der Bericht gilt.

- Sie bestimmen, wo die Berichterstellung erfolgen soll, entweder innerhalb des Diensts oder über einen internen oder externen Watchdog.

- Sie definieren eine Quelle zum Identifizieren des Berichterstatters.

- Sie wählen eine Strategie für die Berichterstellung (in regelmäßigen Abständen oder bei Übergängen). Empfohlen wird eine Berichterstellung in regelmäßigen Abständen, da sie einen einfacheren Code erfordert und weniger fehleranfällig ist.

- Sie bestimmen, wie lange der Bericht über fehlerhafte Bedingungen im Integritätsspeicher bleibt und wie er gelöscht wird. Auf diese Weise werden für Berichte die Gültigkeitsdauer und das Verhalten beim Entfernen nach der Ablaufzeit definiert.

Wie bereits erwähnt, kann die Berichterstellung von folgenden Orten aus erfolgen:

- Vom überwachten Service Fabric-Dienstreplikat.

- Von internen Watchdogs, die als Service Fabric-Dienste bereitgestellt werden (z. B. ein zustandsloser Service Fabric-Dienst, der Bedingungen überwacht und Berichte ausgibt). Watchdogs können auf allen Knoten bereitgestellt oder dem überwachten Dienst zugeordnet werden.

- Von internen Watchdogs, die auf den Service Fabric-Knoten ausgeführt werden, jedoch *nicht* als Service Fabric-Dienste implementiert werden.

- Von externen Watchdogs, welche die Ressource von *außerhalb* des Service Fabric-Clusters überwachen (z. B. ein Überwachungsdienst wie Gomez).

> [AZURE.NOTE] Standardmäßig enthält das Cluster von den Systemkomponenten gesendete Integritätsberichte. Weitere Informationen finden Sie unter [Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Die Benutzerberichte müssen für bereits vom System erstellte [Integritätsentitäten](service-fabric-health-introduction.md#health-entities-and-hierarchy) gesendet werden.

Sobald das Design für die Integritätsberichterstellung feststeht, können Integritätsberichte problemlos gesendet werden. Dies kann über die API mit **FabricClient.HealthManager.ReportHealth**, über PowerShell oder über REST erfolgen. Intern verwenden alle Methoden einen Integritätsclient, der in einem Fabric-Client enthalten ist. Zur Verbesserung der Leistung fassen Konfigurationselemente Berichte zusammen.

> [AZURE.NOTE] Integritätsberichte sind synchronisiert und repräsentieren nur die auf der Clientseite durchgeführten Überprüfungen. Die Tatsache, dass der Bericht vom Integritätsclient akzeptiert wird, bedeutet nicht, dass er in den Speicher übernommen wird. Er wird asynchron gesendet und möglicherweise mit anderen Berichten zusammengefasst. Die Verarbeitung auf dem Server kann möglicherweise trotzdem fehlschlagen (weil z. B. eine Sequenznummer veraltet ist, die Entität, auf die der Bericht angewendet werden muss, gelöscht wurde usw.).

## Integritätsclient
Die Integritätsberichte werden über einen Integritätsclient innerhalb des Fabric-Clients an den Integritätsspeicher gesendet. Der Integritätsclient kann mit folgenden Optionen konfiguriert werden:

- **HealthReportSendInterval**: Die Verzögerung zwischen dem Hinzufügen des Berichts zum Client und dem Senden des Berichts an den Integritätsspeicher. Dadurch können Berichte in einer Nachricht zusammengefasst werden, anstatt für jeden Bericht eine eigene Nachricht zu senden. So wird die Leistung verbessert. Standardwert: 30 Sekunden.

- **HealthReportRetrySendInterval**: Das Intervall, in dem der Integritätsclient kumulierte Integritätsberichte erneut an den Integritätsspeicher sendet. Standardwert: 30 Sekunden.

- **HealthOperationTimeout**: Das Zeitlimit für eine Berichtsnachricht, die an den Integritätsspeicher gesendet wurde. Wenn bei einer Nachricht das Zeitlimit überschritten wird, wiederholt der Integritätsclient den Sendevorgang, bis der Integritätsspeicher die Verarbeitung des Berichts bestätigt. Standardwert: 2 Minuten.

> [AZURE.NOTE] Wenn die Berichte zusammengefasst werden, muss der Fabric-Client mindestens für die Dauer von „HealthReportSendInterval“ aktiv bleiben, um sicherzustellen, dass sie gesendet werden. Wenn die Nachricht verloren geht oder der Integritätsspeicher die Berichte aufgrund von vorübergehenden Fehlern nicht anwenden kann, muss der Fabric-Client länger aktiv bleiben, um einen erneuten Versuch zu ermöglichen.

Bei der Pufferung auf dem Client wird auf die Eindeutigkeit der Berichte geachtet. Beispiel: Wenn ein besonders schlechter Reporter pro Sekunde 100 Berichte bezüglich derselben Eigenschaft einer Entität sendet, werden die Berichte jeweils durch die letzte Version ersetzt. In der Clientwarteschlange ist stets höchstens ein solcher Bericht vorhanden. Wenn die Batchverarbeitung konfiguriert ist, wird pro Sendeintervall nur ein Bericht an den Integritätsspeicher gesendet. Dies ist der zuletzt hinzugefügte Bericht, der den aktuellen Zustand der Entität angibt. Beim Erstellen von **FabricClient** können alle Konfigurationsparameter durch Übergabe von **FabricClientSettings** mit den gewünschten Werten für integritätsbezogene Einträge angegeben werden.

Im Folgenden wird ein Fabric-Client erstellt und festgelegt, dass die Berichte unmittelbar nach dem Hinzufügen gesendet werden sollen. Bei Zeitüberschreitungen und Fehlern, bei denen Wiederholungsversuche möglich sind, wird alle 40 Sekunden ein Wiederholungsversuch ausgeführt.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Beim Erstellen einer Verbindung mit einem Cluster über PowerShell können dieselben Parameter angegeben werden. Folgendes startet eine Verbindung mit einem lokalen Cluster:

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

> [AZURE.NOTE] Um sicherzustellen, dass nicht autorisierte Dienste keine Integritätsberichte für die im Cluster enthaltenen Entitäten ausgeben, kann der Server so konfiguriert werden, dass er Anforderungen nur von sicheren Clients akzeptiert. Da die Berichterstellung über FabricClient erfolgt, müssen für FabricClient Sicherheitsfunktionen aktiviert sein, um mit dem Cluster (z. B. mit Kerberos oder der Zertifikatauthentifizierung) kommunizieren zu können.

## Entwerfen der Integritätsberichterstellung
Der erste Schritt zum Generieren hochwertiger Berichte ist das Identifizieren der Bedingungen, welche die Dienstintegrität beeinträchtigen können. Jegliche Bedingung, die beim – oder noch besser vor dem – Auftreten von Problemen im Dienst oder Cluster zu deren Erkennung beiträgt, kann Milliarden Euro einsparen. Zu den Vorteilen gehören weniger Ausfallzeiten, weniger nächtliche Untersuchungen und Behebungen von Problemen sowie eine höhere Kundenzufriedenheit.

Nachdem die Bedingungen identifiziert wurden, müssen Watchdog-Autoren die beste Möglichkeit für deren Überwachung ermitteln, um ein ausgeglichenes Verhältnis zwischen Aufwand und Nützlichkeit zu erzielen. Betrachten Sie beispielsweise einen Dienst, der mithilfe verschiedener temporärer Dateien auf einer Freigabe komplexe Berechnungen durchführt. Ein Watchdog kann die Freigabe überwachen, um sicherzustellen, dass genügend Speicherplatz verfügbar ist. Er könnte auf Benachrichtigungen bezüglich Datei- bzw. Verzeichnisänderungen lauschen. Er könnte eine Warnung ausgeben, wenn ein anfänglicher Schwellenwert erreicht wird, bzw. einen Fehler, wenn die Freigabe voll ist. Bei einer Warnung könnte ein Reparatursystem mit dem Bereinigen von älteren Dateien auf der Freigabe beginnen. Bei einem Fehler könnte ein Reparatursystem das Dienstreplikat auf einen anderen Knoten verschieben. Beachten Sie, wie die Bedingungszustände hinsichtlich der Integrität beschrieben werden: Zustand einer Bedingung, die als fehlerfrei oder fehlerhaft (Warnung oder Fehler) angesehen werden kann.

Nachdem die Überwachungsdetails eingerichtet wurden, muss ein Watchdog-Autor ermitteln, wie er den Watchdog implementiert. Wenn die Bedingungen von innerhalb des Diensts bestimmt werden können, kann der Watchdog Teil der überwachten Diensts an sich sein. Der Dienstcode kann beispielsweise die Freigabenutzung prüfen und dann bei jedem Schreibversuch in eine Datei mithilfe eines lokalen Fabric-Clients einen Bericht erstellen. Der Vorteil dieses Ansatzes ist die einfache Berichterstellung. Es muss jedoch darauf geachtet werden, dass Watchdog-Probleme nicht die Funktionalität des Diensts beeinträchtigen.

Die Berichterstellung von innerhalb des überwachten Diensts ist nicht immer möglich. Ein Watchdog innerhalb des Diensts kann möglicherweise die Bedingungen nicht erkennen. Er verfügt möglicherweise nicht über die Logik oder die Daten, um sie zu bestimmen. Der Aufwand zur Überwachung der Bedingungen kann hoch sein. Auch gelten die Bedingungen möglicherweise nicht für einen Dienst, sondern beeinträchtigen stattdessen die Interaktion zwischen Diensten. Als weitere Option können Watchdogs im Cluster als separate Prozesse ausgeführt werden. Die Watchdogs überwachen einfach die Bedingungen und erstellen Berichte, ohne die Hauptdienste in irgendeiner Weise zu beeinträchtigen. Diese Watchdogs können beispielsweise als zustandslose Dienste in dieselbe Anwendung implementiert und auf allen Knoten oder auf denselben Knoten wie der Dienst bereitgestellt werden.

Mitunter ist es jedoch auch nicht möglich, einen Watchdog im Cluster auszuführen. Wenn es sich bei den überwachten Bedingungen um die Verfügbarkeit oder die Funktionalität des Diensts aus der Sicht des Benutzers handelt, empfiehlt es sich, die Watchdogs am selben Ort wie die Benutzerclients auszuführen. Dort können sie die Vorgänge so testen, wie Benutzer sie aufrufen. Sie können z. B. einen Watchdog verwenden, der sich außerhalb des Clusters befindet, Anforderungen an den Dienst ausgibt und anschließend die Latenz und die Richtigkeit des Ergebnisses überprüft. (Beispiel für einen Rechnerdienst: Gibt 2 + 2 in einem angemessenen Zeitraum 4 zurück?)

Nachdem Sie die Watchdog-Details festgelegt haben, sollten Sie eine Quell-ID auswählen, die den Watchdog eindeutig identifiziert. Wenn im Cluster mehrere Watchdogs mit dem gleichen Typ enthalten sind, müssen sie entweder Berichte zu unterschiedlichen Entitäten erstellen oder, falls es dieselbe Entität ist, sicherstellen, dass sich die Quell-ID oder die Eigenschaft unterscheiden. Auf diese Weise können ihre Berichte gleichzeitig vorhanden sein. Die Eigenschaft des Integritätsberichts sollte die überwachte Bedingung erfassen. (Im vorangegangenen Beispiel könnte die Eigenschaft **ShareSize** sein.) Wenn mehrere Berichte für die gleiche Bedingung gelten, sollte die Eigenschaft einige dynamische Informationen beinhalten, sodass mehrere Berichte gleichzeitig vorhanden sein können. Beispiel: Wenn mehrere Freigaben überwacht werden müssen, kann der Name der Eigenschaft **ShareSize-Freigabename** lauten.

> [AZURE.NOTE] Der Integritätsspeicher sollte *nicht* zum Speichern von Zustandsinformationen verwendet werden. Hinsichtlich der Integrität sollten nur integritätsbezogene Informationen gemeldet werden, da sie sich auf die Integritätsevaluierung einer Entität auswirken. Der Integritätsspeicher wurde nicht als Allzweckspeicher entwickelt. Er aggregiert mithilfe von Integritätsevaluierungslogik alle Daten im Integritätszustand. Das Senden von Informationen, die sich nicht auf die Integrität beziehen (z. B. Statusberichte mit dem Integritätszustand „OK“), wirkt sich nicht auf den aggregierten Integritätszustand aus, kann jedoch die Leistung des Integritätsspeichers beeinträchtigen.

Als Nächstes legen Sie fest, für welche Entität Berichte erstellt werden sollen. In den meisten Fällen ist dies aufgrund der Bedingung offensichtlich. Sie sollten die Entität mit der bestmöglichen Granularität auswählen. Wenn sich eine Bedingung auf alle Replikate in einer Partition auswirkt, erstellen Sie Berichte nicht über den Dienst, sonder über die Partition. Es gibt jedoch Ausnahmefälle, in denen weitere Überlegungen erforderlich sind. Wenn sich die Bedingung auf eine Entität wie ein Replikat auswirkt, jedoch der Wunsch besteht, sie für mehr als nur die Replikatgültigkeitsdauer zu kennzeichnen, sollte die Berichterstellung auf der Partition erfolgen. Andernfalls werden beim Löschen des Replikats alle damit verbundenen Berichte aus dem Speicher bereinigt. Dies bedeutet, dass Watchdog-Autoren auch die Lebensdauer der Entität und des Berichts berücksichtigen müssen. Es muss klar sein, wann ein Bericht aus einem Speicher bereinigt werden soll (z. B. wenn ein für eine Entität gemeldeter Fehler nicht mehr zutrifft).

Sehen wir uns ein Beispiel an, in dem die oben genannten Punkte verknüpft werden. Stellen Sie sich eine Service Fabric-Anwendung vor, die aus einem zustandsbehafteten, persistenten Masterdienst und zustandslosen Slavediensten besteht, die auf allen Knoten bereitgestellt sind (je ein Slavediensttyp für einen Aufgabentyp). Der Master beinhaltet eine Verarbeitungswarteschlange, die Befehle enthält, die von Slaves ausgeführt werden sollen. Die Slaves führen die eingehenden Anforderungen aus und senden Bestätigungssignale zurück. Eine überwachbare Bedingung ist die Länge der Verarbeitungswarteschlange des Masters. Wenn die Länge der Masterwarteschlange einen Schwellenwert erreicht, wird eine Warnung gemeldet. Damit wird angegeben, dass die Slaves die Last nicht verarbeiten können. Wenn die Warteschlange die maximale Länge erreicht hat und Befehle abgebrochen werden, wird ein Fehler gemeldet, da der Dienst nicht wiederhergestellt werden kann. Die Berichte können für die Eigenschaft **QueueStatus** erstellt werden. Der Watchdog befindet sich innerhalb des Diensts. Er hat regelmäßig Berichte über das primäre Masterreplikat gesendet. Die Gültigkeitsdauer beträgt 2 Minuten, und der Bericht wird regelmäßig alle 30 Sekunden gesendet. Wenn das primäre Replikat ausfällt, wird der Bericht automatisch aus dem Speicher bereinigt. Wenn das Dienstreplikat aktiv ist, aber blockiert ist oder andere Probleme aufweist, läuft der Bericht im Integritätsspeicher ab. In diesem Fall wird die Entität als fehlerhaft bewertet.

Eine weitere Bedingung, die überwacht werden kann, ist die Ausführungszeit der Aufgabe. Der Master verteilt Aufgaben entsprechend des Aufgabentyps an die Slaves. Je nach Design könnte der Master den Aufgabenstatus von den Slaves abrufen. Er könnte auch warten, bis die Slaves Bestätigungssignale zurücksenden, wenn sie fertig sind. In letzterem Fall ist darauf zu achten, dass Situationen erkannt werden, in denen Slaves ausfallen oder Nachrichten verloren gehen. Eine Option ist, dass der Master eine Ping-Anforderung an denselben Slave sendet, der den Status zurücksendet. Wenn kein Status empfangen wird, wertet der Master dies als Fehler und plant die Aufgabe neu. Dabei wird davon ausgegangen, dass die Aufgaben idempotent sind.

Wir können die überwachte Bedingung als Warnung übersetzen, wenn die Aufgabe nicht innerhalb eines bestimmten Zeitraums (**t1**, z. B. 10 Minuten) ausgeführt wird. Sie kann auch als Fehler gewertet werden, wenn die Aufgabe nicht im vorgesehenen Zeitraum (**t2**, z. B. 20 Minuten) abgeschlossen wird. Die Berichterstellung kann auf verschiedene Arten erfolgen:

- Das primäre Masterreplikat gibt regelmäßig Berichte über sich selbst aus. Sie können eine Eigenschaft für alle ausstehenden Aufgaben in der Warteschlange verwenden. Wenn mindestens eine Aufgabe länger dauert, ist der Berichtsstatus für die Eigenschaft **PendingTasks** eine Warnung bzw. ein Fehler. Wenn keine ausstehenden Aufgaben vorhanden sind oder alle Aufgaben gerade gestartet wurden, ist der Berichtsstatus „OK“. Die Aufgaben sind persistent. Wenn das primäre Replikat ausfällt, kann das darauf hin höher gestufte neue primäre Replikat weiterhin ordnungsgemäß Berichte erstellen.

- Ein anderer Watchdog-Prozess (in der Cloud oder extern) überprüft von außerhalb basierend auf dem gewünschten Aufgabenergebnis, ob die Aufgaben abgeschlossen wurden. Wenn die Schwellenwerte nicht beachtet werden, wird ein Bericht über den Masterdienst gesendet. Ein Bericht wird auch über jede Aufgabe gesendet, die die Aufgabenkennung enthält (z. B. **PendingTask+Aufgabenkennung**). Berichte sollten nur für Fehlerzustände gesendet werden. Die Gültigkeitsdauer sollte auf wenige Minuten festgelegt werden, und die Berichte sollten zum Entfernen vorgemerkt werden, wenn sie ablaufen, um eine Bereinigung sicherzustellen.

- Der Slave, der eine Aufgabe ausführt, meldet, wenn die Ausführung länger als erwartet dauert. Er erstellt Berichte für die Dienstinstanz über die Eigenschaft **PendingTasks**. Auf diese Weise wird die fehlerhafte Dienstinstanz ermittelt, aber nicht die Situation erfasst, in der die Instanz ausfällt. Die Berichte werden dabei bereinigt. Es können Berichte über den Slavedienst erstellt werden. Wenn der Slave die Aufgabe abschließt, löscht die Slave-Instanz den Bericht aus dem Speicher. Dabei wird nicht die Situation erfasst, in der die Bestätigungsnachricht verloren gegangen ist, und die Aufgabe ist aus Mastersicht nicht abgeschlossen.

In den oben beschriebenen Fällen werden jedoch Berichte erstellt. Die Erfassung der Berichte erfolgt während der Integritätsevaluierung in der Anwendungsintegrität.

## Regelmäßig oder bei einem Übergang erstellte Berichte
Mithilfe des Integritätsberichterstellungsmodells können Watchdogs Berichte in regelmäßigen Abständen oder bei Übergängen senden. Empfohlen wird die regelmäßige Methode, da der Code erheblich einfacher und weniger fehleranfällig ist. Die Watchdogs sollten so einfach wie möglich gehalten werden, um Fehler zu vermeiden, die falsche Berichte auslösen. Falsche *Fehlerberichte* wirken sich auf Integritätsevaluierungen und auf Szenarien in Verbindung mit der Integrität aus, dazu gehören auch Upgrades. Durch falsche Berichte bezüglich der *Fehlerfreiheit* werden Probleme im Cluster ausgeblendet, was nicht erwünscht ist.

Für die regelmäßige Berichterstellung kann der Watchdog mit einem Timer implementiert werden. Bei einem Timerrückruf kann der Watchdog den Zustand überprüfen und basierend auf dem aktuellen Zustand einen Bericht senden. Sie müssen nicht sehen, welcher Bericht zuvor gesendet wurde. Auch das Messaging muss nicht optimiert werden. Der Integritätsclient verfügt über eine hilfreiche Batchverarbeitungslogik. Solange der Integritätsclient aktiviert bleibt, führt er intern Wiederholversuche durch, bis der Bericht vom Integritätsspeicher bestätigt wurde oder der Watchdog einen neueren Bericht mit derselben Entität, Eigenschaft und Quelle generiert.

Die Berichterstellung bei Übergängen erfordert eine sorgfältige Zustandsbehandlung. Der Watchdog überwacht bestimmte Bedingungen und erstellt nur Berichte, wenn sich diese ändern. Der Vorteil dieses Ansatzes besteht darin, dass weniger Berichte erforderlich sind. Nachteilig ist die komplexe Logik des Watchdogs. Die Bedingungen oder die Berichte müssen zudem verwaltet werden, damit sie auf Zustandsänderungen überprüft werden können. Bei einem Failover muss darauf geachtet werden, wann ein zuvor möglicherweise noch nicht gesendeter Bericht gesendet wird (der in der Warteschlange vorhanden ist, aber noch nicht an den Integritätsspeicher gesendet wurde). Die Sequenznummer muss immer zunehmen. Wenn dies nicht der Fall ist, werden die Berichte als veraltet abgelehnt. In den seltenen Fällen von Datenverlusten müssen möglicherweise der Zustand des Reporters und des Integritätsspeichers synchronisiert werden.

## Implementieren der Integritätsberichterstellung
Sobald die Entitäts- und Berichtdetails geklärt sind, können Integritätsberichte über die API, PowerShell oder REST gesendet werden.

### API
Für die Berichterstellung über die API müssen Benutzer einen Integritätsbericht für den Entitätstyp erstellen, für den die Berichterstellung durchgeführt werden soll. Diesen Bericht übergeben sie anschließend an einen Integritätsclient.

Das folgende Beispiel zeigt die regelmäßige Berichterstellung durch einen Watchdog innerhalb des Clusters. Der Watchdog überprüft, ob über einen Knoten auf eine externe Ressource zugegriffen werden kann. Die Ressource wird von einem Dienstmanifest innerhalb der Anwendung benötigt. Wenn die Ressource nicht verfügbar ist, können die anderen Dienste innerhalb der Anwendung trotzdem ordnungsgemäß funktionieren. Aus diesem Grund wird der Bericht über die bereitgestellte Dienstpaketentität alle 30 Sekunden gesendet.

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

    // TODO: handle exception. Code omitted for snippet brevity.
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### PowerShell
Benutzer können Integritätsberichte mit **Send-ServiceFabric*EntityType*HealthReport** senden.

Das folgende Beispiel zeigt die regelmäßige Berichterstellung für CPU-Werte auf einem Knoten. Die Berichte sollten alle 30 Sekunden gesendet werden, und sie haben eine Gültigkeitsdauer von 2 Minuten. Wenn sie ablaufen, weist der Reporter Probleme auf. Daher wird der Knoten als fehlerhaft bewertet. Wenn der CPU-Wert über einem Schwellenwert liegt, enthält der Bericht als Integritätszustand eine Warnung. Wenn der CPU-Wert länger als die konfigurierte Zeit über dem Schwellenwert bleibt, wird dies als Fehler gemeldet. Andernfalls sendet der Reporter als Integritätszustand „OK“.

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

Im folgenden Beispiel wird eine vorübergehende Warnung für ein Replikat gemeldet. Dabei werden zunächst die Partitions-ID und die Replikat-ID für den gewünschten Dienst abgerufen. Anschließend wird ein Bericht von **PowershellWatcher** über die Eigenschaft **ResourceDependency** gesendet. Der Bericht ist nur 2 Minuten lang von Bedeutung, und er wird automatisch aus dem Speicher entfernt.

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

Basierend auf den Integritätsdaten können Dienstautoren und Cluster- bzw. Anwendungsadministratoren Möglichkeiten für die Nutzung der Informationen erwägen. Sie können beispielsweise Warnungen auf Grundlage des Integritätszustands einrichten, um schwerwiegende Probleme abzufangen, bevor Ausfälle provoziert werden. Administratoren können auch Reparatursysteme einrichten, um Probleme automatisch zu beheben.

[Einführung in die Service Fabric-Integritätsüberwachung](service-fabric-health-introduction.md)

[Anzeigen von Service Fabric-Integritätsberichten](service-fabric-view-entities-aggregated-health.md)

[Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Lokales Überwachen und Diagnostizieren von Diensten](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0128_2016-->