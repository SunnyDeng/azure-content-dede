<properties
    pageTitle="Veröffentlichen einer App in einem Remotecluster mit VS | Microsoft Azure"
    description="Lernen Sie die Schritte kennen, die erforderlich sind, um eine Anwendung mithilfe von Visual Studio in einem Service Fabric-Remotecluster zu veröffentlichen."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="12/06/2015"
    ms.author="cawa" />

# Veröffentlichen einer Anwendung in einem Remotecluster mit Visual Studio

Die Service Fabric-Erweiterung für Visual Studio Visual Studio bietet eine einfache, wiederholbare und skriptfähige Möglichkeit zum Veröffentlichen einer Anwendung in einem Service Fabric-Cluster.

## Grundlegendes zu den Artefakten, die für die Veröffentlichung erforderlich sind

### Deploy-FabricApplication.ps1

Dies ist ein PowerShell-Skript, das den Pfad zu einem Veröffentlichungsprofil als Parameter zum Veröffentlichen von Service Fabric-Anwendungen verwendet. Da dieses Skript Teil Ihrer Anwendung ist, können Sie es für Ihre Anwendung gern je nach Bedarf ändern.

### Veröffentlichungsprofile

Ein Ordner im Service Fabric-Anwendungsprojekt mit dem Namen **PublishProfiles** enthält XML-Dateien, in denen wichtige Informationen zum Veröffentlichen einer Anwendung gespeichert sind, z. B.: - Verbindungsparameter für Service Fabric-Cluster - Pfad zu einer Anwendungsparameterdatei - Upgradeeinstellungen

Standardmäßig enthält Ihre Anwendung zwei Veröffentlichungsprofile: „Local.xml“ und „Cloud.xml“. Sie können weitere Profile hinzufügen, indem Sie eine der Standarddateien kopieren und einfügen.

### Anwendungsparameterdateien

Der Ordner **ApplicationParameters** im Service Fabric-Anwendungsprojekt enthält XML-Dateien für benutzerdefinierte Werte für die Anwendungsmanifestparameter. Anwendungsmanifestdateien können parametrisiert werden, sodass Sie verschiedene Werte für Bereitstellungseinstellungen verwenden können. Weitere Informationen zur Parametrisierung Ihrer Anwendung finden Sie unter [Verwalten mehrerer Umgebungen in Service Fabric](service-fabric-manage-multiple-environment-app-configuration.md).

>[AZURE.NOTE]Bei Actor-Diensten sollten Sie zuerst das Projekt erstellen, um die Parameter in der Manifestdatei zu generieren.

## Veröffentlichen einer Anwendung mit dem Dialogfeld „Service Fabric-Anwendung veröffentlichen“

Folgende Schritte zeigen die Veröffentlichung einer Anwendung über das Dialogfeld **Service Fabric-Anwendung veröffentlichen** in den Visual Studio Service Fabric-Tools.

1. Wählen Sie im Kontextmenü des Service Fabric-Anwendungsprojekts die Option **Veröffentlichen...**, um das Dialogfeld **Service Fabric-Anwendung veröffentlichen** zu öffnen.

    ![][0]

    In der im Dropdownlistenfeld **Zielprofil** ausgewählten Datei sind alle Einstellungen gespeichert, mit Ausnahme der **Manifestversionen**. Sie können ein vorhandenes Profil verwenden oder ein neues Profil erstellen, indem Sie im Dropdownlistenfeld **Zielprofil** die Option **<Profile verwalten…>** auswählen. Wenn Sie ein Veröffentlichungsprofil auswählen, werden die Inhalte des Profils in den entsprechenden Feldern des Dialogfelds angezeigt. Sie können Ihre Änderungen jederzeit speichern, indem Sie den Link **Profil speichern** auswählen.

2. Im Abschnitt **Verbindungsendpunkt** können Sie den Veröffentlichungsendpunkt für einen lokalen Service Fabric-Cluster oder einen Service Fabric-Remotecluster festlegen. Zum Hinzufügen oder Ändern des Verbindungsendpunkts klicken Sie auf die Schaltfläche **Auswählen...**. Im Dialogfeld **Service Fabric-Cluster auswählen** werden die verfügbaren Verbindungsendpunkte des Service Fabric-Clusters angezeigt, in denen Sie die Veröffentlichung basierend auf Ihren Azure-Abonnements durchführen können. Beachten Sie Folgendes: Wenn Sie nicht bereits an Visual Studio angemeldet sind, werden Sie dazu aufgefordert.

    Im Dialogfeld zum Auswählen der Cluster können Sie eine Auswahl aus den verfügbaren Abonnements und Clustern treffen.

    ![][1]

    >[AZURE.NOTE]Wenn Sie die Veröffentlichung auf einem beliebigen Endpunkt durchführen möchten (z. B. einem Party Cluster), helfen Ihnen die Informationen unter **Veröffentlichen auf einem beliebigen Clusterendpunkt** weiter.

    Nachdem Sie einen Endpunkt ausgewählt haben, überprüft Visual Studio die Verbindung zum ausgewählten Service Fabric-Cluster. Wenn der Cluster nicht sicher ist, kann Visual Studio sofort eine Verbindung herstellen. Wenn der Cluster jedoch sicher ist, müssen Sie ein Zertifikat auf Ihrem lokalen Computer installieren, bevor Sie fortfahren können. Weitere Informationen finden Sie unter [Konfigurieren von sicheren Verbindungen](service-fabric-visualstudio-configure-secure-connections.md). Wenn Sie fertig sind, klicken Sie auf die Schaltfläche **OK**. Der ausgewählte Cluster wird im Dialogfeld **Service Fabric-Anwendung veröffentlichen** angezeigt.

3. Im Dropdownlistenfeld **Anwendungsparameterdatei** können Sie zu einer Datei mit Anwendungsparametern navigieren. Eine Anwendungsparameterdatei enthält benutzerdefinierte Werte für Parameter in der Anwendungsmanifestdatei. Klicken Sie auf die Schaltfläche **Bearbeiten**, um einen Parameter zu ändern oder hinzuzufügen. Im Raster **Parameter** geben Sie den Wert für einen Parameter ein oder ändern einen Wert. Klicken Sie dann auf die Schaltfläche **Speichern**.

    ![][2]

4. Mit dem Kontrollkästchen **Anwendung aktualisieren** können Sie angeben, ob es sich bei dieser Veröffentlichungsaktion um ein Upgrade handelt. Aktionen zum Upgrade von Anwendungen unterscheiden sich von normalen Veröffentlichungsaktionen. Unter [Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md) finden Sie eine Liste der Unterschiede. Zum Konfigurieren der Upgradeeinstellungen wählen Sie den Link **Upgradeeinstellungen konfigurieren** aus. Der Editor für Upgradeparameter wird angezeigt. Weitere Informationen zu Upgradeparametern finden Sie unter [Konfigurieren des Upgrades einer Service Fabric-Anwendung](service-fabric-visualstudio-configure-upgrade.md).

5. Klicken Sie auf die Schaltfläche **Manifestversionen...**, um das Dialogfeld **Versionen bearbeiten** anzuzeigen. Sie müssen die Anwendungs- und Dienstversionen aktualisieren, damit ein Upgrade erfolgen kann. Weitere Informationen dazu, wie sich Anwendungs- und Dienstmanifestversionen auf einen Upgradeprozess auswirken, finden Sie unter [Tutorial für Service Fabric-Anwendungsupgrades](service-fabric-application-upgrade-tutorial.md).

    ![][3]

    Wenn bei den Anwendungs- und Dienstversionen semantische Versionsbezeichnungen wie 1.0.0 oder numerische Werte im Format 1.0.0.0 verwendet werden, aktivieren Sie die Option **Anwendungs- und Dienstversionen automatisch aktualisieren**. Bei Auswahl dieser Option werden die Versionsnummern für Dienst und Anwendung automatisch aktualisiert, sobald eine Code-, Konfigurationsdatei- oder Datenpaketversion aktualisiert wird. Wenn Sie die Versionen lieber manuell bearbeiten möchten, deaktivieren Sie das Kontrollkästchen, um diese Funktion auszuschalten.

    >[AZURE.NOTE]Damit alle Paketeinträge für ein Actor-Projekt angezeigt werden, erstellen Sie das Projekt zuerst, um die Einträge in den Dienstmanifestdateien zu generieren.

6. Wenn Sie alle erforderlichen Einstellungen festgelegt haben, klicken Sie auf die Schaltfläche **Veröffentlichen**, um die Anwendung im ausgewählten Service Fabric-Cluster zu veröffentlichen. Die von Ihnen festgelegten Einstellungen werden auf den Veröffentlichungsprozess angewendet.

## Veröffentlichen auf einem beliebigen Clusterendpunkt (einschließlich Party Cluster)

Die Veröffentlichungsoberfläche von Visual Studio ist zum Veröffentlichen auf Remoteclustern optimiert, das einem Ihrer Azure-Abonnements zugeordnet ist. Es ist aber möglich, die Veröffentlichung auf einem beliebigen Endpunkt durchzuführen (z. B. Service Fabric-Party Clustern), indem die XML-Datei mit dem Veröffentlichungsprofil direkt bearbeitet wird. Wie oben beschrieben werden standardmäßig zwei Veröffentlichungsprofile bereitgestellt: **Local.xml** und **Cloud.xml**. Sie können aber gern weitere Profile für unterschiedliche Umgebungen erstellen. Es kann beispielsweise sein, dass Sie ein Profil für die Veröffentlichung auf Party Clustern erstellen möchten, z. B. **Party.xml**.

Wenn Sie eine Verbindung mit einem nicht geschützten Cluster herstellen, benötigen Sie lediglich den Cluster-Verbindungsendpunkt, z. B. `partycluster1.eastus.cloudapp.azure.com:19000`. In diesem Fall sieht der Verbindungsendpunkt im Veröffentlichungsprofil etwa wie folgt aus:

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Wenn Sie eine Verbindung mit einem geschützten Cluster herstellen, müssen Sie auch die Details des Clientzertifikats aus dem lokalen Speicher angeben, das für die Authentifizierung verwendet wird. Weitere Informationen finden Sie unter [Konfigurieren sicherer Verbindungen mit einem Service Fabric-Cluster](service-fabric-visualstudio-configure-secure-connections.md).

  Nachdem Sie das Veröffentlichungsprofil eingerichtet haben, können Sie im Veröffentlichungsdialogfeld wie unten dargestellt darauf verweisen.

  ![Neues Veröffentlichungsprofil im Dialogfeld „Veröffentlichen“][4]

  Beachten Sie in diesem Fall, dass das neue Veröffentlichungsprofil auf eine der standardmäßigen Anwendungsparameterdateien verweist. Dies ist richtig, wenn Sie eine Anwendungskonfiguration in mehreren Umgebungen veröffentlichen möchten. Falls Sie für jede Umgebung, für die die Veröffentlichung durchgeführt werden soll, eine andere Konfiguration verwenden möchten, sollten Sie eine entsprechende Anwendungsparameterdatei erstellen.

## Nächste Schritte

Informationen zur Automatisierung des Veröffentlichungsprozesses in einer Continuous Integration-Umgebung finden Sie unter [Einrichten von Continuous Integration für eine Service Fabric-Anwendung](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png

<!---HONumber=AcomDC_1210_2015-->