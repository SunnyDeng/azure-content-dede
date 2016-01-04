<properties
   pageTitle="Herauf- oder Herunterskalieren eines Service Fabric-Clusters | Microsoft Azure"
   description="Sie können Service Fabric-Cluster bei Bedarf herauf- oder herunterskalieren, indem Sie virtuelle Computerknoten hinzufügen oder entfernen."
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
   ms.date="11/03/2015"
   ms.author="chackdan"/>

# Herauf- oder Herunterskalieren eines Service Fabric-Clusters durch Hinzufügen oder Entfernen von virtuellen Computern (VMs)

Sie können Service Fabric-Cluster bei Bedarf herauf- oder herunterskalieren, indem Sie virtuelle Computer hinzufügen oder entfernen.

>[AZURE.NOTE]Hier wird davon ausgegangen, dass Ihr Abonnement ausreichend Kerne zum Hinzufügen der neuen VMs umfasst, aus denen dieser Cluster besteht.


## Manuelles Skalieren eines Service Fabric-Clusters

### Auswählen des zu skalierenden Knotentyps

Wenn Ihr Cluster mehrere Knotentypen aufweist, müssen Sie jetzt VMs bestimmten Knotentypen hinzufügen oder aus diesen entfernen.

1. Melden Sie sich beim Azure-Portal [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal) an.

2. Navigieren Sie zu den Service Fabric-Clustern ![BrowseServiceFabricClusterResource][BrowseServiceFabricClusterResource]

3. Wählen Sie den Cluster aus, den Sie zental hoch- oder herunterskalieren möchten.

4. Navigieren Sie im Clusterdashboard zum Blatt „Einstellungen“. Wenn das Blatt „Einstellungen“ nicht angezeigt wird, klicken Sie im wesentlichen Teil des Clusterdashboards auf „Alle Einstellungen“.

5. Klicken Sie auf die Knotentypen, um das Listenblatt „NodeTypes“ zu öffnen.

7. Klicken Sie auf den Knoten, der zentral hoch- oder herunterskaliert werden soll. Dieser wird anschließend auf dem Detailblatt „Knotentyp“ geöffnet.

### Aufwärtsskalieren durch das Hinzufügen von Knoten

Passen Sie die Anzahl der VMs bis zur gewünschten Anzahl an, und speichern Sie. Die Knoten/VMs werden nun nach Abschluss der Bereitstellung hinzugefügt.

### Herunterskalieren durch Entfernen von Knoten

Das Entfernen von Knoten ist ein zweistufiger Prozess:

1. Passen Sie die Anzahl der VMs wie gewünscht an, und speichern Sie. Am unteren Ende des Schiebereglers werden die VM-Mindestanforderungen für diesen Knotentyp angezeigt.

  >[AZURE.NOTE]Sie müssen mindestens 5 VMs für den primären Knotentyp verwenden.

	Once that deployment is complete, you will get notified of the VM names that can now be deleted. You now need to navigate to the VM resource and delete it.

2. Kehren Sie Clusterdashboard zurück, und klicken Sie auf die Ressourcengruppe. Das Blatt „Ressourcengruppe“ wird geöffnet.

3. Suchen Sie unter Zusammenfassung, oder öffnen Sie die Ressourcenliste durch Klicken auf „...“.

4. Klicken Sie auf den VM-Namen, bei dem das System angegeben hat, dass er gelöscht werden kann.

5. Klicken Sie auf das Symbol zum Löschen des virtuellen Computers.

## Automatisches Skalieren eines Service Fabric-Clusters

Derzeit unterstützen Service Fabric-Cluster keine automatische Skalierung. In naher Zukunft werden Cluster aufbauend auf Skalierungssätzen für virtuelle Computer (VMSS) erstellt. Ab diesem Zeitpunkt wird auch automatische Skalierung möglich sein und sich ähnlich verhalten wie die automatische Skalierung für Cloud Services.

## Skalieren mit PowerShell/CLI

In diesem Artikel wurde die Skalierung von Clustern mithilfe des Portals behandelt. Allerdings können Sie dieselben Aktionen über die Befehlszeile mithilfe von ARM-Befehlen auf der Clusterressource ausführen. Die GET-Antwort der ClusterResource stellt die Liste der Knoten bereit, die deaktiviert wurden.

## Nächste Schritte

- [Weitere Informationen über Clusterupgrades](service-fabric-cluster-upgrade.md)
- [Weitere Informationen über die Partitionierung statusbehafteter Dienste für maximale Skalierung](service-fabric-concepts-partitioning.md)


<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=Nov15_HO4-->