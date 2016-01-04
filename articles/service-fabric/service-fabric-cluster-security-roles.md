
<properties
   pageTitle="Service Fabric-Clustersicherheit: Clientrollen | Microsoft Azure"
   description="Dieser Artikel beschreibt die zwei Clientrollen und die für die Rollen bereitgestellten Berechtigungen." 
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
   ms.date="11/13/2015"
   ms.author="subramar"/>



# Rollenbasierte Zugriffssteuerung (für Service Fabric-Clients)

Service Fabric unterstützt zwei unterschiedliche Zugriffsberechtigungstypen für Clients, die mit einem Service Fabric-Cluster verbunden sind: Administrator und Benutzer. Zugriffssteuerung ermöglicht es dem Clusteradministrator, den Zugriff auf bestimmte Clustervorgänge für verschiedene Gruppen von Benutzern einzuschränken, wodurch die Sicherheit des Clusters erhöht wird. Administratoren haben vollen Zugriff auf Verwaltungsfunktionen (einschließlich Schreib-/Lesezugriff) und Benutzer haben standardmäßig nur Lesezugriff auf Verwaltungsfunktionen (z. B. Abfragen) sowie die Möglichkeit, Anwendungen und Dienste aufzulösen.

Die beiden Clientrollen (Administrator oder Client) erhalten zum Zeitpunkt der Erstellung des Clusters separate Zertifikate. Ausführliche Informationen zum Einrichten eines sicheren Service Fabric-Clusters finden Sie unter [Service Fabric-Clustersicherheit](service-fabric-cluster-security.md).


## Standardeinstellungen für die Zugriffssteuerung


Der Administrator-Zugriffssteuerungstyp hat vollen Zugriff auf die FabricClient-APIs. Mit ihm können alle Lese- und Schreibvorgänge für den Service Fabric-Cluster ausgeführt werden, einschließlich der folgenden:

### Anwendungs- und Dienstvorgänge 
* **CreateService**: Diensterstellung 							
* **CreateServiceFromTemplate**: Diensterstellung aus Vorlage 							
* **UpdateService**: Dienstaktualisierungen 							
* **DeleteService**: Löschen des Diensts 							
* **ProvisionApplicationType**: Bereitstellung von Anwendungstypen 							
* **CreateApplication**: Anwendungserstellung 							
* **DeleteApplication**: Löschen der Anwendung 							
* **UpgradeApplication**: Starten oder Unterbrechen von Anwendungsupgrades 							
* **UnprovisionApplicationType**: Aufheben der Bereitstellung von Anwendungstypen 							
* **MoveNextUpgradeDomain**: Fortsetzen von Anwendungsupgrades mit einer expliziten Upgradedomäne 							
* **ReportUpgradeHealth**: Fortsetzen von Anwendungsupgrades beim aktuellen Aktualisierungsvorgang 							
* **ReportHealth**: Integritätsbericht 							
* **PredeployPackageToNode**: API vor der Bereitstellung 							
* **CodePackageControl**: Neustarten von Codepaketen 							
* **RecoverPartition**: Wiederherstellen einer Partition 							
* **RecoverPartitions**: Wiederherstellen von Partitionen 							
* **RecoverServicePartitions**: Wiederherstellen von Dienstpartitionen 							
* **RecoverSystemPartitions**: Wiederherstellen von Systempartitionen 							


### Clustervorgänge
* **ProvisionFabric**: MSI- und/oder Clustermanifest-Bereitstellung 							
* **UpgradeFabric**: Starten von Clusterupgrades 							
* **UnprovisionFabric**: Aufheben der MSI- und/oder Clustermanifest-Bereitstellung 							
* **MoveNextFabricUpgradeDomain**: Fortsetzen von Clusterupgrades mit einer expliziten Upgradedomäne 							
* **ReportFabricUpgradeHealth**: Fortsetzen von Clusterupgrades beim aktuellen Aktualisierungsvorgang 							
* **StartInfrastructureTask**: Starten von Infrastrukturaufgaben 							
* **FinishInfrastructureTask**: Abschließen von Infrastrukturaufgaben 							
* **InvokeInfrastructureCommand**: Befehle zur Infrastrukturaufgabenverwaltung 							
* **ActivateNode**: Aktivierung eines Knotens 							
* **DeactivateNode**: Deaktivierung eines Knotens 							
* **DeactivateNodesBatch**: Deaktivierung mehrerer Knoten 							
* **RemoveNodeDeactivations**: Zurücksetzen der Deaktivierung mehrerer Knoten 							
* **GetNodeDeactivationStatus**: Überprüfung des Deaktivierungsstatus 							
* **NodeStateRemoved**: Bericht zur Entfernung des Knotenstatus 							
* **ReportFault**: Berichtsfehler 							
* **FileContent**: Image-Speicherclient – Dateiübertragung (außerhalb des Clusters) 							
* **FileDownload**: Image-Speicherclient – Dateidownloadinitiierung (außerhalb des Clusters) 							
* **InternalList**: Image-Speicherclient – Dateiauflistungsvorgang (intern) 							
* **Delete**: Image-Speicherclient – Löschvorgang 							
* **Upload**: Image-Speicherclient – Uploadvorgang 							
* **NodeControl**: Starten, Stoppen und Neustarten von Knoten 							
* **MoveReplicaControl**: Verschieben von Replikaten von einem Knoten auf einen anderen 							

### Verschiedene Vorgänge
* **Ping**: Clientpingvorgänge 							
* **Query**: Alle Abfragen zulässig
* **NameExists**: Überprüfung auf vorhandene URI-Namen 							



Der Benutzerzugriffssteuerungs-Typ ist standardmäßig auf die folgenden Vorgänge beschränkt (Admin-Zugriffssteuerung ermöglicht auch Zugriff auf diese Vorgänge):

* **EnumerateSubnames**: Benennen der URI-Enumeration 							
* **EnumerateProperties**: Benennen der Eigenschaften-Enumeration 							
* **PropertyReadBatch**: Benennen der Lesevorgänge für Eigenschaften 							
* ****GetServiceDescription: long-poll-Dienstbenachrichtigungen und Lesen von Dienstbeschreibungen
* **ResolveService**: Dienstauflösungen auf Konfliktbasis 							
* **ResolveNameOwner**: Auflösen des Namens-URI-Besitzers 							
* **ResolvePartition**: Auflösen von Systemdiensten 							
* **ServiceNotifications**: Dienstbenachrichtigungen auf Ereignisbasis 							
* **GetUpgradeStatus**: Abrufen des Anwendungsupgradestatus 							
* **GetFabricUpgradeStatus**: Abrufen des Clusterupgradestatus 							
* **InvokeInfrastructureQuery**: Abfragen von Infrastrukturaufgaben 							
* **List**: Image-Speicherclient – Dateiauflistungsvorgang 							
* **ResetPartitionLoad**: Zurücksetzen der failoverUnit-Auslastung 							
* ****ToggleVerboseServicePlacementHealthReporting: Umschalten zu ausführlichen Dienstplatzierungs-Integritätsberichten

## Ändern der Standardeinstellungen für Clientrollen

In der Manifestdatei des Clusters können bei Bedarf Admin-Funktionen für den Client bereitgestellt werden. Sie können die Standardeinstellungen ändern, indem Sie die Option **Fabric Settings* während der [Clustererstellung](service-fabric-cluster-creation-via-portal.md) auswählen und die Einstellungen mit den obigen Namen im Feld **Name** und in das Wertfeld **Admin, Benutzer** eingeben.

## Nächste Schritte

[Service Fabric-Clustersicherheit](service-fabric-cluster-security.md)

[Service Fabric-Clustererstellung](service-fabric-cluster-creation-via-portal.md)

<!---HONumber=Nov15_HO4-->