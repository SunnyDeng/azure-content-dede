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
   ms.date="01/11/2016"
   ms.author="chackdan"/>

# Herauf- oder Herunterskalieren eines Service Fabric-Clusters durch Hinzufügen oder Entfernen von virtuellen Computern (VMs)

Sie können Service Fabric-Cluster bei Bedarf herauf- oder herunterskalieren, indem Sie virtuelle Computer hinzufügen oder entfernen.

>[AZURE.NOTE]Hier wird davon ausgegangen, dass Ihr Abonnement ausreichend Kerne zum Hinzufügen der neuen VMs umfasst, aus denen dieser Cluster besteht.


## Manuelles Skalieren eines Service Fabric-Clusters

Wenn Ihr Cluster mehrere Typen von Knoten enthält, müssen Sie VMs bestimmten Knotentypen hinzufügen oder aus diesen entfernen. Sie können in der gleichen Bereitstellung VMs einem Knotentyp hinzufügen oder aus einem anderen entfernen.

### Aktualisieren eines Clusters, den Sie mithilfe des Portals bereitgestellt haben

Da dies ein komplexer Vorgang ist, haben wir ein PowerShell-Modul in ein Git-Repository hochgeladen, mit dem dieser Vorgang für Sie durchgeführt wird.

**Schritt 1**: Kopieren Sie diesen Ordner aus [diesem Git-Repository](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers) auf Ihren Computer.

**Schritt 2**: Stellen Sie sicher, dass Azure SDK 1.0+ auf Ihrem Computer installiert ist.

**Schritt 3**: Öffnen Sie ein PowerShell-Fenster, und importieren Sie die Datei „ServiceFabricRPHelpers.psm“.

```
Remove-Module ServiceFabricRPHelpers
```

Importieren Sie das PowerShell-Modul, das Sie gerade kopiert haben. Sie können die folgenden Cmd kopieren und den Pfad zur .psm1 ändern, sodass er Ihrem Computer entspricht.

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

 **Schritt 4**: Melden Sie sich bei Ihrem Azure-Konto an.

```
Login-AzureRmAccount
```

Führen Sie den Befehl „Invoke-ServiceFabricRPClusterScaleUpgrade“ aus und stellen Sie dabei sicher, dass der Name der Ressourcengruppe und des Abonnements richtig geschrieben sind.

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName <string> -SubscriptionId <string>
```

Nachstehend finden Sie ein ausgefülltes Beispiel für den gleichen PS-Befehl.

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName chackod02 -SubscriptionId 18ad2z84-84fa-4798-ad71-e70c07af879f
```

  **Schritt 5**: der Befehl ruft jetzt die Clusterinformationen ab und führt Sie durch alle Knotentypen. Zuerst werden Sie über die aktuelle Anzahl der VM-Knoten für diesen Knotentyp informiert und dann um die Angabe der Anzahl der neuen VM-Knoten gebeten.

 **Um diesen Knotentyp herauf zu skalieren**, geben Sie eine größere Zahl als die aktuelle Zahl virtueller Computer an.

**Um diesen Knotentyp herunter zu skalieren**, geben Sie eine kleinere Zahl als die aktuelle Zahl virtueller Computer an.

Diese Aufforderung erfolgt nacheinander für alle Knotentypen. Wenn Ihr Cluster nur einen Knotentyp aufweist, werden Sie nur einmal dazu aufgefordert.
 
  **Schritt 6**: Wenn Sie neue virtuelle Computer hinzufügen, erhalten Sie jetzt eine Aufforderung zur Eingabe der Remotedesktop-Benutzer-ID und des Kennworts für die virtuellen Computer, die Sie hinzufügen.
 
**Schritt 7**: im Ausgabefenster sehen Sie jetzt Meldungen, die Sie über den Fortschritt der Bereitstellung informieren. Nach Abschluss der Bereitstellung sollten Ihre Cluster die Anzahl der Knoten aufweisen, die Sie in Schritt 5 angegeben haben.


![ScaleupDownPSOut][ScaleupDownPSOut]


**Schritt 8**: Wenn dies eine Anforderung zum Herunterskalieren war, müssen Sie einen weiteren Schritt durchführen, um die VMs zu löschen Das Skript deaktiviert alle virtuellen Computer, die Sie entfernen wollen, d. h., auf diesen Knoten/VMs sind keine Anwendungen oder Systemreplikate mehr vorhanden. Daher ist es jetzt sicher, diese virtuellen Computer zu löschen.

**HINWEIS** - obwohl die deaktivierten Knoten nicht mehr von Ihrem SF-Cluster verwendet werden, müssen Sie die deaktivierten VMs löschen, sodass diese Ihnen nicht in Rechnung gestellt werden.

**Schritt 8.1**: Melden Sie sich beim Azure-Portal [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal) an.

**Schritt 8.2**: Navigieren Sie zu der Clusterressource, die Sie herunterskaliert haben und klicken Sie auf „Alle Einstellungen“ auf dem Essentials-Webpart.

**Schritt 8.3**: Klicken Sie auf die Knotentypen. Jetzt sehen Sie eine Liste der Knoten, die deaktiviert sind. In diesem Bild sind chackodnt15, chackodnt24, chackodnt25 und chackodnt26 die VMs, die jetzt gelöscht werden müssen.

![DeactivatedNodeList][DeactivatedNodeList]

**Schritt 8.4**: Löschen Sie diese VMs über PS oder -Portal. Nachdem Sie diese gelöscht haben, ist die Herunterskalierung Ihres Clusters abgeschlossen.

```
Remove-AzureRmResource -ResourceName <Deactivated Node name without the understore> -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName <Resource Group name> -ApiVersion <Api version>
```
Nachstehend finden Sie ein ausgefülltes Beispiel für diesen Befehl.

```
Remove-AzureRmResource -ResourceName chackodnt26 -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName chackod02 -ApiVersion 2015-05-01-preview
```

### Aktualisieren eines Clusters, den Sie mithilfe von ARM PowerShell/CLI bereitgestellt haben

Wenn Sie zunächst den Cluster mithilfe der ARM-Vorlage bereitgestellt haben, müssen Sie die Anzahl der virtuellen Computer für einen bestimmten Knotentyp und alle Ressourcen, die den virtuellen Computer unterstützen, ändern. Die minimale Anzahl von VMs, die für den primären Knotentyp zulässig ist, ist „5“ (für nicht-Test-Cluster), für Test-Cluster beträgt die minimale Anzahl „3“.

Nachdem Sie die neue Vorlage haben, können Sie diese via ARM bereitstellen, indem Sie die gleiche Ressourcengruppe benutzen, wie die des Clusters, den Sie aktualisieren.


## Automatisches Skalieren eines Service Fabric-Clusters

Derzeit unterstützen Service Fabric-Cluster keine automatische Skalierung. In naher Zukunft werden Cluster aufbauend auf Skalierungssätzen für virtuelle Computer (VMSS) erstellt. Ab diesem Zeitpunkt wird auch automatische Skalierung möglich sein und sich ähnlich verhalten wie die automatische Skalierung für Cloud Services.


## Nächste Schritte

- [Weitere Informationen über Clusterupgrades](service-fabric-cluster-upgrade.md)
- [Weitere Informationen über die Partitionierung statusbehafteter Dienste für maximale Skalierung](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ScaleupDownPSOut]: ./media/service-fabric-cluster-scale-up-down/ScaleupDownPSOut.png
[DeactivatedNodeList]: ./media/service-fabric-cluster-scale-up-down/DeactivatedNodeList.png

<!---HONumber=AcomDC_0114_2016-->