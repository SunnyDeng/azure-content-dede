<properties 
   pageTitle="Service Fabric-Testability-Szenarien: Dienstkommunikation"
	description="Die Kommunikation von Dienst zu Dienst ist ein wichtiger Integrationspunkt einer Service Fabric-Anwendung. In diesem Artikel werden Entwurfsaspekte und Testverfahren beschrieben."
	services="service-fabric"
	documentationCenter=".net"
	authors="vturecek"
	manager="timlt"
	editor=""/>

<tags
   ms.service="service-fabric"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="NA"
	ms.date="08/25/2015"
	ms.author="vturecek"/>

# Service Fabric-Testability-Szenarien: Dienstkommunikation

Microservices und dienstorientierte Architekturstile können in Service Fabric auf natürliche Weise genutzt werden. Bei diesen Arten von verteilten Architekturen setzen sich komponentenbasierte Microserviceanwendungen in der Regel aus mehreren Diensten zusammen, die miteinander kommunizieren müssen. Auch im einfachsten Fall verfügen Sie im Allgemeinen mindestens über einen zustandslosen Webdienst und einen zustandsbehafteten Datenspeicherdienst, die miteinander kommunizieren müssen.

Die Kommunikation von Dienst zu Dienst ist ein entscheidender Integrationspunkt einer Anwendung, da von jedem Dienst eine Remote-API für andere Dienste verfügbar gemacht wird. Die Verwendung einer Gruppe von API-Grenzen mit E/A erfordert häufig besondere Sorgfalt und einen relativ hohen Test- und Überprüfungsaufwand.

Es gibt viele Punkte, die beachtet werden müssen, wenn diese Dienstgrenzen in einem verteilten System miteinander verknüpft sind:

 - **Transportprotokoll**: Wird HTTP genutzt, um für eine bessere Interoperabilität zu sorgen, oder ein benutzerdefiniertes binäres Protokoll, um einen maximalen Durchsatz zu erzielen?
 - **Fehlerbehandlung**: Wie werden dauerhafte und vorübergehende Fehler behandelt, und was geschieht, wenn ein Dienst auf einen anderen Knoten verschoben wird?
 - **Timeouts und Wartezeit**: Wie behandeln die einzelnen Dienstschichten in Anwendungen mit mehreren Ebenen die Wartezeit über den Stapel und bis zum Benutzer?

Das Testen der Interaktion zwischen Ihren Diensten ist wichtig, um die Resilienz Ihrer Anwendung sicherzustellen. Dabei spielt es keine Rolle, ob Sie eine der integrierten Komponenten für die Dienstkommunikation von Service Fabric verwenden oder eine eigene Komponente erstellen.

## Wo ist mein Dienst?

Dienstinstanzen können im Laufe der Zeit verschoben werden, besonders wenn sie mit Auslastungsmetriken für einen maßgeschneiderten optimalen Lastenausgleich für Ressourcen konfiguriert werden. Upgrades, Failovers, horizontales Hochskalieren und verschiedene andere Situationen, die während der Lebensdauer eines verteilten Systems eintreten, sind die Fälle, in denen Service Fabric Ihre Dienstinstanzen zur Maximierung der Verfügbarkeit verschiebt.

Wenn Dienste im Cluster verschoben werden, gibt es zwei Szenarien, auf deren Behandlung Ihre Clients und Dienste bei der Kommunikation mit einem Dienst vorbereitet sein sollten:

 + Die Dienstinstanz oder das Partitionsreplikat wurden verschoben, seitdem Sie das letzte Mal damit kommuniziert haben. Dies ist ein normaler Bestandteil eines Dienstlebenszyklus, und es ist zu erwarten, dass dies während der Lebensdauer Ihrer Anwendung vorkommt.
 + Die Dienstinstanz oder das Partitionsreplikat wird gerade verschoben. Obwohl der Failovervorgang eines Diensts von einem Knoten auf einen anderen Knoten in Service Fabric sehr schnell erfolgt, kann es bei der Verfügbarkeit zu einer Verzögerung kommen, falls die Kommunikationskomponente Ihrer Dienste langsam gestartet werden.

Die ordnungsgemäße Behandlung dieser Szenarien ist wichtig dafür, dass das System reibungslos ausgeführt werden kann. Hierzu müssen einige Punkte beachtet werden:

+ Jeder Dienst, mit dem eine Verbindung hergestellt werden kann, verfügt über eine *Adresse*, die er überwacht (HTTP, WebSockets usw.). Wenn eine Dienstinstanz oder Partition verschoben wurde, ändert sich der dazugehörige Adressendpunkt (er wurde auf einen anderen Knoten mit einer anderen IP-Adresse verschoben). Wenn Sie die integrierten Kommunikationskomponenten verwenden, werden darüber die Dienstadressen neu für Sie aufgelöst. 
+ Unter Umständen kann es zu einem vorübergehenden Anstieg der Dienstwartezeit kommen, während die Dienstinstanz ihren Listener neu startet. Dies hängt davon ab, wie schnell der Dienst ihn nach dem Verschieben öffnet.
+ Alle vorhandenen Verbindungen müssen geschlossen und wieder geöffnet werden, wenn der Dienst einen neuen Knoten öffnet. Beim ordnungsgemäßen Herunterfahren oder Neustart eines Knotens wird Zeit dafür veranschlagt, die für das ordnungsgemäße Herunterfahren vorhandener Verbindungen anfällt.

### Testen: Verschieben von Dienstinstanzen

Mit den Testability-Tools von Service Fabric können wir ein Testszenario zum Testen dieser Situationen auf unterschiedliche Weise erstellen.

1. Verschieben Sie das primäre Replikat eines zustandsbehafteten Diensts.
 
    Das primäre Replikat einer zustandsbehafteten Dienstpartition kann aus verschiedenen Gründen verschoben werden. Nutzen Sie dies für das primäre Replikat einer bestimmten Partition, um auf kontrollierte Weise zu ermitteln, wie Ihre Dienste auf die Verschiebung reagieren.

    ```powershell

    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService

    ```

2. Beenden Sie einen Knoten.

    Wenn ein Knoten beendet wird, verschiebt Service Fabric alle Dienstinstanzen oder -partitionen, die sich auf dem Knoten befunden haben, auf einen der anderen verfügbaren Knoten im Cluster. Nutzen Sie dies zum Testen einer Situation, in der ein Knoten in Ihrem Cluster verloren geht und alle Dienstinstanzen und Replikate auf dem Knoten verschoben werden müssen.

    Ein Knoten kann mit dem Stop-ServiceFabricNode PowerShell-Cmdlet beendet werden:

    ```powershell

    PS > Restart-ServiceFabricNode -NodeName Node.1

    ```

    
    


### Dienstverfügbarkeit

Service Fabric ist eine Plattform, mit der für Ihre Dienste eine hohe Verfügbarkeit erzielt werden soll. In Extremfällen können zugrunde liegende Infrastrukturprobleme trotzdem noch zum Ausfall der Verfügbarkeit führen. Daher ist es wichtig, auch Tests für ein Szenario dieser Art durchzuführen.

Für zustandsbehaftete Dienste wird ein quorumbasiertes System zum Replizieren des Zustands mit dem Ziel einer hohen Verfügbarkeit verwendet. Dies bedeutet, dass ein Quorum von Replikaten verfügbar sein muss, um Schreibvorgänge ausführen zu können. In seltenen Fällen, z. B. bei einem umfangreicheren Hardwarefehler, ist unter Umständen kein Quorum von Replikaten verfügbar. Sie können dann keine Schreibvorgänge ausführen, während Lesevorgänge weiterhin möglich sind.

### Testen: Nichtverfügbarkeit von Schreibvorgängen

Mit den Testability-Tools von Service Fabric können Sie einen Fehler injizieren, mit dem ein Quorumverlust ausgelöst wird, um diese Art von Szenario zu testen. Dieser Fall ist selten. Trotzdem ist es wichtig, dass Clients und Dienste, die von einem zustandsbehafteten Dienst abhängig sind, auf die Behandlung von Situationen vorbereitet sind, in denen keine Schreibanforderungen an den zustandsbehafteten Dienst möglich sind. Gleichzeitig ist es auch wichtig, dass der zustandsbehaftete Dienst selbst über diese Möglichkeit informiert ist und mit Aufrufern ordnungsgemäß kommunizieren kann.

Der Quorumverlust kann mit dem Invoke-ServiceFabricPartitionQuorumLoss PowerShell-Cmdlet ausgelöst werden:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode PartialQuorumLoss -QuorumLossDurationInSeconds 20

```

In diesem Beispiel wird `QuorumLossMode` auf `PartialQuorumLoss` festgelegt. So wird angegeben, dass ein Quorumverlust ausgelöst werden soll, ohne alle Replikate herunterzufahren, damit Lesevorgänge weiterhin möglich sind. Um ein Szenario zu testen, in dem eine gesamte Partition nicht verfügbar ist, können Sie diesen Schalter auf `FullQuorumLoss` festlegen.

## Nächste Schritte

[Weitere Informationen zu Testability-Aktionen](service-fabric-testability-actions.md)

[Weitere Informationen zu Testability-Szenarien](service-fabric-testability-scenarios.md)

<!---HONumber=August15_HO9-->