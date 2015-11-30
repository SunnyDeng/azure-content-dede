<properties
   pageTitle="Kapazitätsplanung für Service Fabric-Anwendungen | Microsoft Azure"
   description="Informationen zum Bestimmen der Anzahl von Computeknoten, die für eine Service Fabric-Anwendung erforderlich sind."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/12/2015"
   ms.author="subramar"/>


# Kapazitätsplanung für Service Fabric-Anwendungen


In diesem Dokument erfahren Sie, wie die Anzahl der Ressourcen (CPU, Arbeitsspeicher, Datenträgerspeicher) bestimmen, die Sie zum Ausführen von Service Fabric-Anwendungen benötigen. Es ist üblich, dass sich die erforderlichen Ressourcen mit der Zeit ändern. In der Regel brauchen Sie für das Entwickeln und Testen Ihres Diensts ein bestimmtes Maß an Ressourcen, das Sie erhöhen müssen, sobald Sie zur Produktionsumgebung wechseln und die Beliebtheit Ihrer Anwendung zunimmt. Beim Entwurf einer Anwendung ist es am besten, sich schon heute die langfristigen Anforderungen zu vergegenwärtigen und Entscheidungen zu treffen, die Ihrem Dienst ein einfaches Skalieren zum Erfüllen einer hohen Kundennachfrage ermöglichen. Wenn Sie einen Service Fabric-Cluster erstellen, entscheiden Sie, welche Arten von virtuellen Computern (VMs) Sie zum Bilden des Clusters einsetzen möchten. Jede VM verfügt über eine begrenzte Menge an Ressourcen in Form von CPU (Kernen und -Geschwindigkeit), Netzwerkbandbreite, Arbeitsspeicher (RAM) und Datenträgerspeicher. Wenn der Dienst mit der Zeit wächst, können Sie ein Upgrade auf VMs mit mehr Ressourcen vornehmen und/oder Ihrem Cluster weitere VMs hinzufügen. Für den letzteren Schritt müssen Sie allerdings Ihren Dienst anfänglich so planen, dass er neue VMs nutzen kann, die dem Cluster dynamisch hinzugefügt werden.

Einige Dienste verwalten wenig bis keine Daten auf den VMs selbst, weshalb sich die Kapazitätsplanung hauptsächlich auf Leistung konzentrieren sollte. Dies bedeutet, dass Sie die Leistung der Algorithmen des Codes und der CPU (Kerne und Geschwindigkeit) der VMs für die Ausführung Ihrer Algorithmen sorgfältig prüfen müssen. Darüber hinaus müssen Sie auch die Netzwerkbandbreite und insbesondere die Häufigkeit von Netzwerkübertragungen sowie die übertragene Datenmenge prüfen. Wenn Ihr Dienst bei steigender Nutzung weiter eine gute Leistung bieten soll, können Sie dem Cluster weitere VMs hinzufügen und unter alle VMs für einen Lastenausgleich der Netzwerkanforderungen sorgen.

Bei Diensten, die sehr viele Daten in den VMs verwalten, sollte der Fokus der Kapazitätsplanung auf der Größe liegen. Dies bedeutet, dass Sie die Kapazität des Arbeits- und Datenträgerspeichers sorgfältig prüfen müssen. Das Verwaltungssystem für virtuellen Arbeitsspeicher von Windows sorgt dafür, dass Speicherplatz auf dem Datenträger für den Anwendungscode wie RAM aussieht. Dadurch können Anwendungen mehr Arbeitsspeicher nutzen, als auf dem virtuellen Computer physisch verfügbar ist. Durch den zusätzlichen RAM wird die Leistung gesteigert, da die VM mehr Datenträgerspeicher im RAM belassen kann. Wenn Sie eine VM wählen, entscheiden Sie sich für eine VM, deren Speicherplatz auf dem Datenträger alle in der VM gewünschten Daten aufnehmen kann. Wählen Sie zudem eine RAM-Größe, die Ihnen den Zugriff auf diese Daten im gewünschten Tempo erlaubt. Wenn die Daten Ihres Diensts mit der Zeit anwachsen, können Sie dem Cluster weitere VMs hinzufügen und die Daten auf alle VMs verteilen.

## Wie viele Knoten sind erforderlich?

Das Partitionieren Ihres Diensts ermöglicht das horizontale Hochskalieren der Daten Ihres Diensts (weitere Details zur Partitionierung finden Sie unter [Partitionieren von Service Fabric](service-fabric-concepts-partitioning.md)). Jede Partition muss in eine einzige VM passen, doch mehrere (kleine) Partitionen können in einer einzelnen VM platziert werden. Daher gibt Ihnen eine große Anzahl kleiner Partitionen mehr Flexibilität als eine kleine Anzahl größerer Partitionen. Der Nachteil besteht darin, dass viele Partitionen den Verarbeitungsaufwand für Service Fabric erhöhen und Transaktionsvorgänge nicht partitionsübergreifend erfolgen können. Außerdem fällt potenziell mehr Netzwerkdatenverkehr an, wenn Ihr Dienst häufig auf Datenelemente zugreifen muss, die sich in verschiedenen Partitionen befinden. Wenn Sie Ihren Dienst entwerfen, müssen Sie diese Vor- und Nachteile sorgfältig prüfen, um zu einer effektiven Partitionierungsstrategie zu gelangen.

Angenommen, Ihre Anwendung hat einen einzelnen zustandsbehafteten Dienst mit einer Speichergröße, von der Sie in einem Jahr ein Anwachsen auf „DB\_Size“ (DB-Größe) in GB erwarten. Außerdem möchten Sie weitere Anwendungen (und Partitionen) hinzufügen, da Sie nach diesem Jahr von Wachstum ausgehen. Um die gesamte Datenbankgröße (DB\_Size) in allen Replikaten zu ermitteln, müssen wir auch den Replikationsfaktor (RF) berücksichtigen, der die Anzahl der Replikate für Ihren Dienst bestimmt (die gesamte DB-Größe aller Replikate ist der mit der DB-Größe multiplizierte Replikationsfaktor). „Node\_Size“ (Knotengröße) stellt den Datenträgerspeicherplatz/RAM pro Knoten dar, den Sie für Ihren Dienst verwenden möchten. Für eine optimale Leistung sollte die Datenbank in den Arbeitsspeicher des gesamten Clusters passen, weshalb Sie eine Knotengröße wählen, die ungefähr der RAM-Kapazität der gewählten VM entspricht. Wenn Sie eine Knotengröße zuordnen, die höher ist als die RAM-Kapazität ist, müssen Sie mit Auslagerungsvorgängen des Betriebssystems arbeiten, wodurch die Leistung ggf. nicht optimal, aber für Ihren Dienst immer noch ausreichend sein kann.

Daher kann die Anzahl der Knoten, die für eine maximale Leistung erforderlich ist, wie folgt berechnet werden:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## Berücksichtigen von Wachstum

Sie können die Anzahl der Knoten basierend auf der Datenbankgröße berechnen, auf die Ihr Dienst anwachsen soll, zusätzlich zur Datenbankgröße, mit der Sie beginnen. Bei Wachstum Ihres Diensts können Sie dann die Anzahl der Knoten steigern, um eine anfängliche übermäßige Bereitstellung von Knoten zu vermeiden. Jedoch sollte die Anzahl der Partitionen auf der erforderlichen Anzahl von Knoten basieren, wenn Ihr Dienst bei maximalem Wachstum ausgeführt wird. Es empfiehlt sich, einige zusätzliche VMs (mit Zusatzkapazität) jederzeit vorzuhalten, damit Sie mit unerwarteten Spitzen oder Ausfällen in der Infrastruktur zurechtkommen (z. B. wenn einige VMs ausfallen sollten). Die Grundlage für die Berechnung dieser Zusatzkapazität sollten Ihre erwarteten Spitzen sein. Als Ausgangspunkt empfiehlt sich ein Wert von 5-10 %.

Das beschriebene Szenario geht von einem zustandsbehafteten Dienst aus. Wenn Sie mehrere davon haben, müssen Sie die mit den anderen Diensten verknüpfte Datenbankgröße in die Gleichung einbeziehen. Oder Sie müssen die Anzahl der Knoten für jeden zustandsbehafteten Dienst einzeln berechnen. Der Dienst hat möglicherweise Replikate oder Partitionen ohne Lastenausgleich (ggf. weisen einige Partitionen möglicherweise mehr Daten als andere auf. Die hierfür bewährten Methoden finden Sie im Artikel zur Partitionierung.). Die oben aufgeführte Gleichung ist jedoch unabhängig von der Anzahl der Partitionen oder Replikate, da Service Fabric dafür sorgt, dass die Replikate auf optimierte Weise auf die Knoten verteilt werden.


## Kalkulationstabelle zur Berechnung der Kosten

Nun wollen wir die zuvor genannte Formel mit echten Zahlen füllen. Im [hier bereitgestellten](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) Beispielarbeitsblatt wird gezeigt, wie die Kapazität für eine Anwendung mit drei Arten von Datenobjekten geplant wird. Für jedes Objekt wählen wir einen Schätzwert für seine Größe und die erwartete Anzahl. Wir haben auch ausgewählt, wie viele Replikate von jedem Objekttyp wir wünschen. Das Arbeitsblatt berechnet die Gesamtmenge des Arbeitsspeichers, der im Cluster gespeichert werden soll. Dann geben wir eine VM-Größe und die monatlichen Kosten ein. Basierend auf der VM-Größe weist die Tabelle die Mindestanzahl von Partitionen aus, auf die Sie Ihre Daten verteilen müssen, damit diese physisch auf die Knoten passen. Sie benötigen ggf. mehr Partitionen, um die besonderen Rechnen- und Netzwerkverkehrsanforderungen Ihrer Anwendung zu erfüllen. Im Arbeitsblatt wir das Erhöhen der Anzahl der Partitionen zum Verwalten der Benutzerobjekte von 1 auf 6 gezeigt.

Basierend auf all diesen Informationen zeigt das Arbeitsblatt nun, dass physisch für alle Daten mit den gewünschten Partitionen und Replikaten ein Cluster mit 26 Knoten erforderlich ist. Allerdings ist dieser Cluster dicht gepackt, weshalb einige zusätzliche Knoten für etwaige Knotenausfälle und Upgrades hinzugefügt werden sollten. Das Arbeitsblatt zeigt auch, dass sich bei mit mehr als 57 Knoten kein zusätzlicher Nutzen ergibt, da es leere Knoten gäbe. Doch für etwaige Knotenausfälle und Upgrades können Sie sich für mehr als 57 Knoten entscheiden. Sie können die Kalkulationstabelle an die speziellen Bedürfnisse Ihrer Anwendung anpassen.

![][Image1]



## Nächste Schritte

Unter [Partitionieren von Service Fabric-Diensten][10] erfahren Sie mehr über die Partitionierung von Diensten.



<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md

<!---HONumber=Nov15_HO4-->