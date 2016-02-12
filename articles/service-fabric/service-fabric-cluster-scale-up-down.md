<properties
   pageTitle="Zentrales Hoch- oder Herunterskalieren eines Service Fabric-Clusters | Microsoft Azure"
   description="Sie können einen Service Fabric-Cluster bei Bedarf zentral hoch- oder herunterskalieren, indem Sie virtuelle Computerknoten hinzufügen oder entfernen."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/29/2016"
   ms.author="chackdan"/>


# Zentrales Hoch- oder Herunterskalieren von Service Fabric-Clustern durch Hinzufügen oder Entfernen virtueller Computer

Sie können Azure Service Fabric-Cluster bei Bedarf zentral hoch- oder herunterskalieren, indem Sie virtuelle Computer hinzufügen oder entfernen.

>[AZURE.NOTE] Ihr Abonnement muss ausreichend Kerne zum Hinzufügen der neuen virtuellen Computer umfassen, aus denen dieser Cluster bestehen soll.

## Manuelles Skalieren eines Service Fabric-Clusters

### Auswählen des zu skalierenden Knotentyps

Wenn Ihr Cluster mehrere Knotentypen enthält, müssen Sie bestimmten Knotentypen virtuelle Computer hinzufügen oder aus diesen entfernen. Das geht so:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Navigieren Sie zu **Service Fabric-Cluster**. ![Seite „Service Fabric-Cluster“ im Azure-Portal.][BrowseServiceFabricClusterResource]

3. Wählen Sie den Cluster aus, den Sie zentral hoch- oder herunterskalieren möchten.

4. Navigieren Sie im Clusterdashboard zum Blatt **Einstellungen**. Wenn das Blatt **Einstellungen** nicht angezeigt wird, klicken Sie im wesentlichen Teil des Clusterdashboards auf **Alle Einstellungen**.

5. Klicken Sie auf **NodeTypes**, um das Blatt mit der **NodeTypes-Liste** zu öffnen.

6. Klicken Sie auf den Knotentyp, der zentral hoch- oder herunterskaliert werden soll. Dadurch wird das Blatt mit **NodeType-Details** geöffnet.

### Zentrales Hochskalieren durch das Hinzufügen von Knoten

Passen Sie die Anzahl von virtuellen Computern an die gewünschte Anzahl an, und speichern Sie die Einstellungen. Die Knoten/virtuellen Computer werden hinzugefügt, nachdem die Bereitstellung abgeschlossen ist.

### Zentrales Herunterskalieren durch Entfernen von Knoten

Das Entfernen von Knoten ist ein zweistufiger Prozess:

1. Passen Sie die Anzahl von virtuellen Computern an die gewünschte Anzahl an, und speichern Sie die Einstellungen. Das untere Ende des Schiebereglers gibt die für diesen Knotentyp erforderliche Mindestanzahl von virtuellen Computern an.

    >[AZURE.NOTE] Sie müssen mindestens fünf virtuelle Computer für den primären Knotentyp verwenden.

2. Sobald die Bereitstellung abgeschlossen ist, werden Sie über die Namen der virtuellen Computer benachrichtigt, die gelöscht werden können. Sie müssen dann zu den VM-Ressourcen wechseln und sie löschen:

    a. Kehren Sie zum Clusterdashboard zurück, und klicken Sie auf **Ressourcengruppe**. Das Blatt **Ressourcengruppe** wird geöffnet.

    b. Suchen Sie unter **Zusammenfassung**, oder öffnen Sie die Ressourcenliste durch Klicken auf **...**.

    c. Klicken Sie auf den Namen des virtuellen Computers, der laut Systemhinweis gelöscht werden kann.

    d. Klicken Sie auf das Symbol **Löschen**, um den virtuellen Computer zu löschen.

## Automatisches Skalieren von Service Fabric-Clustern

Derzeit unterstützen Service Fabric-Cluster keine automatische Skalierung. In naher Zukunft werden Cluster aufbauend auf Skalierungssätzen für virtuelle Computer erstellt. Ab diesem Zeitpunkt wird auch automatische Skalierung möglich sein und sich ähnlich verhalten wie die automatische Skalierung, die in Clouddiensten verfügbar ist.

## Skalieren von Clustern mithilfe der PowerShell-/Befehlszeilenschnittstelle

In diesem Artikel wird die Skalierung von Clustern mithilfe des Portals behandelt. Allerdings können Sie dieselben Aktionen über die Befehlszeile mithilfe von Azure-Ressourcen-Manager-Befehlen auf der Clusterressource ausführen. Die GET-Antwort der Clusterressource stellt die Liste der Knoten bereit, die deaktiviert wurden.

## Nächste Schritte

- [Weitere Informationen über Clusterupgrades](service-fabric-cluster-upgrade.md)
- [Weitere Informationen über die Partitionierung statusbehafteter Dienste für maximale Skalierung](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=AcomDC_0204_2016-->