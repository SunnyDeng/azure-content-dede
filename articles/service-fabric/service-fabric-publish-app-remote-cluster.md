<properties
    pageTitle="Veröffentlichen einer App in einem Remotecluster mit VS | Microsoft Azure"
    description="Lernen Sie die Schritte kennen, die erforderlich sind, um eine Anwendung mithilfe von Visual Studio in einem Service Fabric-Remotecluster zu veröffentlichen."
    services="service-fabric"
    documentationCenter="na"
    authors="cawa"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# Veröffentlichen einer Anwendung in einem Remotecluster mit Visual Studio

Visual Studio Service Fabric-Tools bieten eine einfache, wiederholbare und skriptfähige Möglichkeit zum Veröffentlichen einer Anwendung in einem Service Fabric-Cluster. Dies erfolgt mithilfe eines PowerShell-Bereitstellungsskripts mit Veröffentlichungsprofilen. Veröffentlichungsprofile sind Dateien im XML-Format, in denen wichtige Veröffentlichungsinformationen gespeichert sind. Das Dialogfeld **Service Fabric-Anwendung veröffentlichen** wurde hinzugefügt, um den Benutzern die Veröffentlichung von Anwendungen in einem lokalen oder Remotecluster zu erleichtern. Alle in diesem Dialogfeld vorgenommenen Einstellungen werden in den Veröffentlichungsprofilen erfasst. Auf diese Weise können Sie Veröffentlichungseinstellungen später in einem Automatisierungsprozess manuell bearbeiten.

## Zur Veröffentlichung einer Anwendung in einem Service Fabric-Cluster benötigte Artefakte

### Deploy-FabricApplication.ps1

Dies ist ein PowerShell-Skript, das den Pfad zu einem Veröffentlichungsprofil als Parameter zum Veröffentlichen von Service Fabric-Anwendungen verwendet.

### Veröffentlichungsprofile

Der Ordner **PublishProfiles** im Service Fabric-Anwendungsprojekt enthält die Dateien **Cloud.XML** und **Local.XML**. Hierbei handelt es sich um „Veröffentlichungsprofile“, in denen wichtige Informationen zum Veröffentlichen einer Anwendung gespeichert sind, wie z. B.: Verbindungsparameter für den Service Fabric-Cluster, Pfad zu einer Anwendungsparameterdatei, Aktualisierungseinstellungen.

### Anwendungsparameterdateien

Der Ordner **ApplicationParameters** im Service Fabric-Anwendungsprojekt enthält XML-Dateien für benutzerdefinierte Werte für die Anwendungsmanifestparameter. Anwendungsmanifestdateien können parametrisiert werden, sodass Sie verschiedene Werte für Bereitstellungseinstellungen verwenden können.

Sie können beispielsweise die Anzahl von Partitionen ändern, um sie an die verschiedenen Umgebungen für jede Bereitstellung anzupassen. Wenn Sie ein Projekt erstellen, werden die Einstellungen im Anwendungsmanifest, z. B. **TargetReplicaSetSize** und **PartitionCount**, in Parameter konvertiert. Der Standardwert dieser Parameter ist im Abschnitt **Parameter** der Anwendungsmanifestdatei angegeben (ApplicationManifest.XML im Service Fabric-Anwendungsprojekt). Jegliche Werte, die Sie der Anwendungsparameterdatei hinzufügen, überschreiben die Standardwerte in der Anwendungsmanifestdatei.

>[AZURE.NOTE]Bei Actor-Diensten sollten Sie zuerst das Projekt erstellen, um die Parameter in der Manifestdatei zu generieren.

Hier ein Beispiel einer Anwendungsmanifestdatei:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application2Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Actor1ActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="Actor1ActorService_MinReplicaSetSize" DefaultValue="2" />
      <Parameter Name="Actor1ActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Actor1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Actor1ActorService">
         <StatefulService ServiceTypeName="Actor1ActorServiceType" TargetReplicaSetSize="[Actor1ActorService_TargetReplicaSetSize]" MinReplicaSetSize="[Actor1ActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[Actor1ActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## Veröffentlichen einer Anwendung in einem Service Fabric-Cluster über das Dialogfeld „Service Fabric-Anwendung veröffentlichen“

Folgende Schritte zeigen die Veröffentlichung einer Anwendung über das Dialogfeld **Service Fabric-Anwendung veröffentlichen** in den Visual Studio Service Fabric-Tools.

1. Wählen Sie im Kontextmenü des Service Fabric-Anwendungsprojekts die Option **Veröffentlichen...**, um das Dialogfeld **Service Fabric-Anwendung veröffentlichen** zu öffnen.

    ![][0]

    In der im Dropdownlistenfeld **Zielprofil** ausgewählten Datei sind alle Einstellungen gespeichert, mit Ausnahme der **Manifestversionen**. Sie können ein vorhandenes Profil verwenden oder ein neues Profil erstellen, indem Sie im Dropdownlistenfeld **Zielprofil** die Option **<Profile verwalten…>** auswählen. Wenn Sie ein Veröffentlichungsprofil auswählen, werden die Inhalte des Profils in den entsprechenden Feldern des Dialogfelds angezeigt. Sie können Ihre Änderungen jederzeit speichern, indem Sie den Link **Profil speichern** auswählen.

1. Im Abschnitt **Verbindungsendpunkt** können Sie den Veröffentlichungsendpunkt für einen lokalen Service Fabric-Cluster oder einen Service Fabric-Remotecluster festlegen. Zum Hinzufügen oder Ändern des Verbindungsendpunkts klicken Sie auf die Schaltfläche **Auswählen...**. Das Dialogfeld **Service Fabric-Cluster auswählen** zeigt die verfügbaren Verbindungsendpunkte des Service Fabric-Clusters an, in denen Sie veröffentlichen können. (Wenn Sie nicht bereits bei einem Azure-Abonnement angemeldet sind, werden Sie aufgefordert, sich bei einem Abonnement anzumelden.) Wählen Sie einen Verbindungsendpunkt aus.

    ![][1]

    Nachdem Sie einen Endpunkt ausgewählt haben, überprüft Visual Studio die Verbindung zum ausgewählten Service Fabric-Cluster. Wenn der Cluster nicht sicher ist, kann Visual Studio sofort eine Verbindung herstellen. Wenn der Cluster jedoch sicher ist, müssen Sie ein Zertifikat auf Ihrem lokalen Computer installieren, bevor Sie fortfahren können. Weitere Informationen finden Sie unter [Konfigurieren von sicheren Verbindungen](service-fabric-visualstudio-configure-secure-connections.md). Wenn Sie fertig sind, klicken Sie auf die Schaltfläche **OK**. Der ausgewählte Cluster wird im Dialogfeld **Service Fabric-Anwendung veröffentlichen** angezeigt.

1. Im Dropdownlistenfeld **Anwendungsparameterdatei** können Sie zu einer Datei mit Anwendungsparametern navigieren. Eine Anwendungsparameterdatei enthält benutzerdefinierte Werte für Parameter in der Anwendungsmanifestdatei. Klicken Sie auf die Schaltfläche **Bearbeiten**, um einen Parameter zu ändern oder hinzuzufügen. Im Raster **Parameter** geben Sie den Wert für einen Parameter ein oder ändern einen Wert. Klicken Sie dann auf die Schaltfläche **Speichern**.

    ![][2]

1. Mit dem Kontrollkästchen **Anwendung aktualisieren** können Sie angeben, ob es sich bei dieser Veröffentlichungsaktion um ein Upgrade handelt. Aktionen zum Upgrade von Anwendungen unterscheiden sich von normalen Veröffentlichungsaktionen. Unter [Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md) finden Sie eine Liste der Unterschiede. Zum Konfigurieren der Upgradeeinstellungen wählen Sie den Link **Upgradeeinstellungen konfigurieren** aus. Der Editor für Upgradeparameter wird angezeigt. Weitere Informationen zu Upgradeparametern finden Sie unter [Konfigurieren des Upgrades einer Service Fabric-Anwendung](service-fabric-visualstudio-configure-upgrade.md).

1. Klicken Sie auf die Schaltfläche **Manifestversionen...**, um das Dialogfeld **Versionen bearbeiten** anzuzeigen. Sie müssen die Anwendungs- und Dienstversionen aktualisieren, damit ein Upgrade erfolgen kann. Weitere Informationen dazu, wie sich Anwendungs- und Dienstmanifestversionen auf einen Upgradeprozess auswirken, finden Sie unter [Tutorial für Service Fabric-Anwendungsupgrades](service-fabric-application-upgrade-tutorial.md).

    ![][3]

    Wenn bei den Anwendungs- und Dienstversionen semantische Versionsbezeichnungen wie 1.0.0 oder numerische Werte im Format 1.0.0.0 verwendet werden, aktivieren Sie die Option **Anwendungs- und Dienstversionen automatisch aktualisieren**. Bei Auswahl dieser Option werden die Versionsnummern für Dienst und Anwendung automatisch aktualisiert, sobald eine Code-, Konfigurationsdatei- oder Datenpaketversion aktualisiert wird. Wenn Sie die Versionen lieber manuell bearbeiten möchten, deaktivieren Sie das Kontrollkästchen, um diese Funktion auszuschalten.

    >[AZURE.NOTE]Damit alle Paketeinträge für ein Actor-Projekt angezeigt werden, erstellen Sie das Projekt zuerst, um die Einträge in den Dienstmanifestdateien zu generieren.

1. Wenn Sie alle erforderlichen Einstellungen festgelegt haben, klicken Sie auf die Schaltfläche **Veröffentlichen**, um die Anwendung im ausgewählten Service Fabric-Cluster zu veröffentlichen. Die von Ihnen festgelegten Einstellungen werden auf den Veröffentlichungsprozess angewendet.

## Nächste Schritte

Informationen zur Automatisierung des Veröffentlichungsprozesses in einer Continuous Integration-Umgebung finden Sie unter [Einrichten von Continuous Integration für eine Service Fabric-Anwendung](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png

<!---HONumber=Nov15_HO4-->