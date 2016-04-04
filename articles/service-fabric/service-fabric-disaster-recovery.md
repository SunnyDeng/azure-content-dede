<properties
   pageTitle="Notfallwiederherstellung in Azure Service Fabric | Microsoft Azure"
   description="Azure Service Fabric bietet Funktionalität für den Umgang mit allen Arten von Notfällen. In diesem Artikel werden die Arten von Notfällen, die auftreten können, und entsprechende Vorgehensweisen beschrieben."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="seanmck"/>

# Notfallwiederherstellung in Azure Service Fabric

Ein wichtiger Aspekt der Bereitstellung einer Cloudanwendung mit hoher Verfügbarkeit ist das Sicherstellen, dass ihr sämtliche Fehlertypen nichts anhaben können, was auch für die gilt, auf die Sie keinerlei Einfluss haben. In diesem Artikel wird das physische Layout eines Azure Service Fabric-Clusters im Kontext potenzieller Notfälle beschrieben. Außerdem finden Sie eine Anleitung zum Umgang mit diesen Notfällen, um das Risiko von Ausfallzeiten oder Datenverlusten zu begrenzen bzw. zu vermeiden.

## Physisches Layout von Service Fabric-Clustern in Azure

Um das Risiko zu verstehen, dass verschiedene Arten von Ausfällen darstellen, ist es hilfreich zu wissen, wie Cluster in Azure physisch angeordnet werden.

Wenn Sie in Azure einen Service Fabric-Cluster erstellen, müssen Sie eine Region auswählen, in der dieser gehostet wird. Die Azure-Infrastruktur stellt dann die Ressourcen für diesen Cluster innerhalb der Region bereit, insbesondere die angeforderte Anzahl virtueller Computer (VMs). Nun wollen wir uns genauer ansehen, wie und wo diese VMs bereitgestellt werden.

### Fehlerdomänen

Standardmäßig werden die VMs im Cluster gleichmäßig auf logische Gruppen verteilt, die „Fehlerdomänen“ genannt werden. Diese segmentieren die VMs basierend auf potenziellen Ausfällen bei der Hosthardware. Wenn sich zwei virtuelle Computer in zwei verschiedenen Fehlerdomänen befinden, können Sie insbesondere sicher sein, dass sie sich nicht dieselbe Stromversorgung bzw. denselben Netzwerkswitch teilen. Daher wirkt sich ein lokaler Netzwerk- oder Stromausfall nur auf die eine und nicht auf die andere VM aus, sodass Service Fabric den Workload der nicht reagierenden VM im Cluster neu verteilen kann.

Sie können das Layout Ihres Clustern in Fehlerdomänen mithilfe der von [Service Fabric-Explorer](service-fabric-visualizing-your-cluster.md) bereitgestellten Clusterzuweisung visualisieren:

![Auf Fehlerdomänen verteilte Knoten in Service Fabric-Explorer][sfx-cluster-map]

>[AZURE.NOTE] Die andere Achse in der Clusterzuweisung zeigt Upgradedomänen, in denen Knoten basierend auf geplanten Wartungsaktivitäten logisch gruppiert werden. Service Fabric-Cluster in Azure werden stets in fünf Upgradedomänen angeordnet.

### Geografische Verteilung

Es gibt derzeit weltweit 22 Azure-Regionen. 5 weitere sind bereits angekündigt. Eine einzelne Region kann ein oder mehrere physische Rechenzentren enthalten, was u. a. von der Nachfrage und der Verfügbarkeit geeigneter Standorte abhängt. Beachten Sie jedoch, dass auch in Regionen mit mehreren physische Rechenzentren es keine Garantie gibt, dass die VMs in Ihrem Cluster gleichmäßig auf diese physischen Standorte verteilt sind. Derzeit werden sogar alle VMs für einen bestimmten Cluster an einem einzelnen physischen Standort bereitgestellt.

## Umgang mit Ausfällen

Es gibt verschiedene Arten von Ausfällen, die sich auf Ihren Cluster auswirken können. Für jeden gibt es eine eigene Abhilfemöglichkeit. Wir sehen uns diese in der Reihenfolge der Wahrscheinlichkeit ihres Auftretens an.

### Ausfall einzelner VMs

Wie bereits erwähnt, stellen Ausfälle einzelner Computer, entweder in der VM oder bei Hardware oder Software, die diese in einer Fehlerdomäne hostet, an sich kein Risiko dar. Service Fabric erkennt den Ausfall in der Regel binnen Sekunden und reagiert basierend auf dem Status des Clusters entsprechend. Wenn z. B. der Knoten die primären Replikate einer Partition gehostet hat, wird aus den sekundären Replikaten der Partition ein neues primäres ausgewählt. Wenn Azure die ausgefallene VM reaktiviert, tritt diese automatisch dem Cluster bei und übernimmt dann gleich ihren Anteil am Workload.

### Mehrere gleichzeitige Computerausfälle

Wenngleich Fehlerdomänen das Risiko gleichzeitiger Computerausfälle deutlich reduzieren, ist es stets möglich, dass verschiedene zufällige Ausfälle mehrere Computer in einem Cluster gleichzeitig ausfallen lassen.

Solange die Mehrheit der Knoten verfügbar bleibt, setzt der Cluster im Allgemeinen den Betrieb fort, allerdings mit geringerer Kapazität, da zustandsbehaftete Replikate auf eine kleinere Menge von VMs verteilt werden und weniger zustandslose Instanzen zum Verteilen der Last zur Verfügung stehen.

#### Quorumverlust

Wenn eine Mehrheit der Replikate für die Partition eines zustandsbehafteten Diensts ausfällt, wechselt die Partition in einen Zustand, der als „Quorumverlust“ bezeichnet wird. An diesem Punkt beendet Service Fabric das Zulassen von Schreibvorgängen in dieser Partition, um sicherzustellen, dass ihr Status konsistent und zuverlässig bleibt. Tatsächlich nehmen wir einen Zeitraum der Nichtverfügbarkeit in Kauf, um dafür zu sorgen, dass Clients nicht informiert werden, dass ihre Daten gespeichert wurden, wenn das gar nicht der Fall ist. Wenn Sie sich für das Zulassen von Lesevorgängen aus sekundären Replikaten für diesen zustandsbehafteten Dienst entschieden haben, können Sie diese Lesevorgänge in diesem Zustand fortsetzen. Eine Partition behält den Zustand „Quorumverlust“ so lange, bis eine ausreichende Anzahl von Replikaten reaktiviert ist oder der Clusteradministrator das System mithilfe des Cmdlets [Repair-ServiceFabricPartition API](repair-partition-ps) zum Fortsetzen zwingt. Das Ausführen dieser Aktion, solange das primäre Replikat ausgefallen ist, führt zu Datenverlust.

Systemdienste können auch einen Quorumverlust erleiden, wobei die Auswirkung spezifisch für den jeweiligen Dienst ist. Beispielsweise wirkt sich der Quorumverlust im Naming Service auf die Namensauflösung aus. Im Failover-Manager-Dienst verhindert der Quorumverlust hingegen das Erstellen neuer Dienste und Failover. Beachten Sie, dass im Gegensatz zu Ihren eigenen Diensten das Reparieren von Systemdiensten *nicht* empfohlen wird. Stattdessen empfiehlt es sich, einfach zu warten, bis die ausgefallenen Replikate wieder aktiv sind.

#### Minimieren des Risikos eines Quorumverlusts

Sie können das Risiko eines Quorumverlusts minimieren, indem Sie die Zielgröße des Replikatsatzes für Ihren Dienst erhöhen. Hilfreich ist es, sich die Anzahl der benötigten Replikate als Anzahl nicht verfügbarer Knoten vorzustellen, die Sie auf einmal tolerieren können, während diese für Schreibvorgänge verfügbar bleiben. Dabei ist zu bedenken, dass Knoten zusätzlich zu Hardwareausfällen durch Anwendungs- oder Clusterupgrades vorübergehend nicht verfügbar sein können.

Sehen Sie sich die folgenden Beispiele unter der Annahme an, dass Sie Ihre Dienste mit dem „MinReplicaSetSize“-Wert 3 konfiguriert haben, dem niedrigsten für Produktionsdienste empfohlenen Wert. Beim „TargetReplicaSetSize“-Wert 3 (ein primäres und zwei sekundäre Replikate) führt ein Hardwarefehler während eines Upgrades (zwei Replikate ausgefallen) zum Quorumverlust, woraufhin Ihr Dienst schreibgeschützt wird. Wenn Sie alternativ über 5 Replikate verfügen, können Sie zwei Ausfälle während des Upgrades überstehen (drei Replikate ausgefallen), da die verbleibenden 2 Replikate weiter mit dem Mindestreplikatsatz ein Quorum bilden können.

### Rechenzentrumsausfälle oder -zerstörung

In seltenen Fällen können physische Rechenzentren aufgrund eines Ausfalls des Stroms oder der Netzwerkverbindung vorübergehend nicht verfügbar sein. In diesen Fällen sind auch Ihre Service Fabric-Cluster und -Anwendungen nicht verfügbar, aber Ihre Daten bleiben erhalten. Für in Azure ausgeführte Cluster können Sie neueste Informationen zu Ausfällen auf der [Azure-Statusseite](azure-status-dashboard) nachlesen.

Im äußerst unwahrscheinlichen Fall der Zerstörung eines gesamten physischen Rechenzentrums gehen sämtliche Service Fabric-Cluster samt Zustand verloren.

Um dagegen gewappnet zu sein, ist es überaus wichtig, den [Zustand regelmäßig in einem georedundanten Speicher zu sichern](service-fabric-reliable-services-backup-restore.md) und dafür zu sorgen, dass Sie die Möglichkeit der Wiederherstellung geprüft haben. Die Frequenz der Sicherung hängt von Ihrem Recovery Point Objective (RPO) ab. Auch wenn Sie die Sicherung und Wiederherstellung noch nicht implementiert haben, müssen Sie einen Handler für das `OnDataLoss`-Ereignis implementieren, damit Sie sein Auftreten wie folgt protokollieren können:

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(true);
}
```

>[AZURE.NOTE] Sicherung und Wiederherstellung sind derzeit nur für die Reliable Services-API verfügbar. Sicherung und Wiederherstellung für Reliable Actors werden in einer künftigen Version verfügbar sein.

### Softwareausfälle und andere Möglichkeiten von Datenverlust

Als Ursache von Datenverlust sind Codefehler in Diensten, Benutzerfehler und Sicherheitslücken weitaus gängiger als Rechenzentrumsausfälle. Doch in allen Fällen ist die Wiederherstellungsstrategie gleich: erstellen Sie regelmäßige Sicherungen aller zustandsbehafteten Dienste, und prüfen Sie die Fähigkeit zum Wiederherstellen des jeweiligen Zustands.

## Nächste Schritte

- Informationen zum Simulieren verschiedener Ausfälle mit dem [Testability-Framework](service-fabric-testability-overview.md).
- Lesen Sie weitere Artikel zu Wiederherstellung und hoher Verfügbarkeit. Microsoft hat sehr umfassende Anleitungen zu diesen Themen veröffentlicht. Während sich einige dieser Dokumente auf bestimmte Techniken für die Verwendung in anderen Produkten beziehen, enthalten viele allgemeine bewährte Methoden, die auch im Service Fabric-Kontext befolgt werden können:
 - [Checkliste für die Verfügbarkeit](azure-availability-checklist)
 - [Ausführen von Notfallwiederherstellungsverfahren](disaster-recovery-drill)
 - [Notfallwiederherstellung und hohe Verfügbarkeit für Azure-Anwendungen](dr-ha-guide)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/de-DE/library/mt163522.aspx
[azure-status-dashboard]: https://azure.microsoft.com/de-DE/status/
[azure-availability-checklist]: https://azure.microsoft.com/de-DE/documentation/articles/best-practices-availability-checklist/
[disaster-recovery-drill]: https://azure.microsoft.com/de-DE/documentation/articles/sql-database-disaster-recovery-drills/

<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png

<!---HONumber=AcomDC_0323_2016-->