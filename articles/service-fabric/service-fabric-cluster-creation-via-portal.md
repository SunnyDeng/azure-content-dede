<properties
   pageTitle="Erstellen eines Service Fabric-Clusters im Azure-Portal | Microsoft Azure"
   description="Erstellen Sie einen Service Fabric-Cluster im Azure-Portal."
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


# Erstellen eines Service Fabric-Clusters im Azure-Portal

Auf dieser Seite erhalten Sie Informationen zum Einrichten eines Service Fabric-Clusters. Ihr Abonnement muss ausreichend Kerne zum Bereitstellen der virtuellen IaaS-Computer umfassen, aus denen dieser Cluster bestehen soll.


## Cluster erstellen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Klicken Sie auf **+ Neu**, um eine neue Ressourcenvorlage hinzuzufügen. Suchen Sie Ihre Vorlage im **Marketplace** unter **Alles**. Sie heißt **Service Fabric Cluster**.

    a. Klicken Sie in der obersten Ebene auf **Marketplace**.

    b. Klicken Sie unter **Alles** auf **Fabric**, und drücken Sie die EINGABETASTE. Manchmal funktioniert der automatische Filter nicht. Drücken Sie daher in jedem Fall die EINGABETASTE. ![Screenshot der Suche nach der Service Fabric-Clustervorlage im Azure-Portal.][SearchforServiceFabricClusterTemplate]

3. Wählen Sie **Service Fabric Cluster** aus der Liste aus.

4. Navigieren Sie zum Blatt **Service Fabric Cluster**, klicken Sie auf **Erstellen**, und geben Sie Details zu Ihrem Cluster ein.

5. Geben Sie der Ressourcengruppe unter **Neue Ressourcengruppe erstellen** den gleichen Namen, den auch der Cluster besitzt. Dies ist hilfreich, um sie später wiederzufinden, insbesondere dann, wenn Sie Änderungen an Ihrer Bereitstellung vornehmen oder Ihren Cluster löschen möchten.

    >[AZURE.NOTE] Auch wenn Sie eine vorhandene Ressourcengruppe verwenden können, empfiehlt es sich, eine neue Ressourcengruppe zu erstellen. So können Sie leichter die Cluster löschen, die Sie nicht mehr benötigen.

 	![Screenshot der Erstellung einer neuen Ressourcengruppe.][CreateRG]

6. Stellen Sie sicher, dass Sie das gewünschte **Abonnement** auswählen, dem Ihr Cluster bereitgestellt werden soll – vor allem dann, wenn Sie über mehrere Abonnements verfügen.

7. Wählen Sie einen **Standort** aus der Dropdownliste aus. Der Standardwert ist **USA, Westen**.

8. Konfigurieren Sie Ihren **Knotentyp**. Der Knotentyp kann als Äquivalent zu Rollen in Clouddiensten betrachtet werden. Knotentypen definieren die Größe, die Anzahl und die Eigenschaften der virtuellen Computer. Der Cluster kann über mehrere Knotentypen verfügen. Der primäre Knotentyp (der erste, den Sie im Portal definieren), muss jedoch mindestens fünf VMs aufweisen. So konfigurieren Sie Ihren Knotentyp:

	a. Wählen Sie die benötigte Größe und den gewünschten Tarif für virtuelle Computer aus. Standardmäßig ist D4 Standard ausgewählt. Wenn Sie diesen Cluster jedoch nur zum Testen Ihrer Anwendung verwenden, können Sie auch D2 oder kleinere virtuelle Computer auswählen.

	b. Wählen Sie die Anzahl der virtuellen Computer. Sie können die Anzahl der virtuellen Computer in einen Knotentyp später zentral hoch- oder herunterskalieren, aber der erste Knotentyp muss mindestens fünf VMs umfassen.

	c. Wählen Sie einen Namen für Ihren Knotentyp aus (ein bis zwölf Zeichen, nur Buchstaben und Zahlen).

	d. Wählen Sie den **Benutzernamen** und das **Kennwort** für den VM-Remotedesktop.

	e. Wenn Sie im Cluster mehrere Knotentypen benötigen, sollten Sie die folgenden Aspekte beachten. (Wenn Sie einen Cluster mit einem einzigen Knotentyp bereitstellen möchten, fahren Sie mit Schritt 9 fort.)

	* Angenommen, Sie möchten eine Anwendung bereitstellen, die einen Front-End-Dienst und einen Back-End-Dienst enthält. Sie möchten den Front-End-Dienst auf kleinere virtuelle Computer (VM-Größen wie A2, D2 usw.) platzieren, die über geöffnete Ports für das Internet verfügen. Den rechenintensiven Back-End-Dienst möchten Sie hingegen auf größere virtuelle Computer (mit VM-Größen wie D4, D6, D12 usw.) platzieren, die nicht vom Internet aus zugänglich sind.

	* Auch wenn beide Dienste auf einem Knotentyp verwendet werden können, wird empfohlen, sie in einen Cluster mit zwei Knotentypen zu platzieren. Jeder Knotentyp kann unterschiedliche Eigenschaften aufweisen, wie Internetkonnektivität, VM-Größe und Anzahl der virtuellen Computer, die unabhängig voneinander skaliert werden können.

	* Definieren Sie zuerst einen Knotentyp, der mindestens fünf virtuelle Computer umfasst. Die anderen Knotentypen können über mindestens einen virtuellen Computer verfügen.

  	![Screenshot der Erstellung eines Knotentyps.][CreateNodeType]

9. Wenn Sie Ihre Anwendungen sofort im Cluster bereitstellen möchten, fügen Sie Ports, die Sie für Ihre Anwendungen öffnen möchten, auf einem Knotentyp **Anwendungsports** (oder auf den von Ihnen erstellten Knotentypen) hinzu. Sie können dem Knotentyp später Ports hinzufügen, indem Sie den Lastenausgleich ändern, der diesem Knoten zugeordnet ist. (Fügen Sie einen Test hinzu, und fügen Sie den Test dann den Lastenausgleichsregeln hinzu.) Das gestaltet sich nun es etwas einfacher, da die erforderlichen Tests und Regeln dem Lastenausgleich durch die Portalautomatisierung hinzugefügt werden.

	a. Sie finden die Anwendungsports in Ihren Dienstmanifesten, die im Anwendungspaket enthalten sind. Rufen Sie Ihre Anwendungen auf, öffnen Sie die Dienstmanifeste, und notieren Sie alle Eingabeendpunkte, die Ihre Anwendung zum Kommunizieren mit der Außenwelt benötigt.

	b. Fügen Sie dem Feld **Anwendungseingabe-Endpunkte** alle Ports (durch Kommas getrennt) hinzu. Der Endpunkt der TCP-Client-Verbindung ist standardmäßig 19000, sodass Sie keinen angegeben müssen. Für die Beispielanwendung „WordCount“ muss beispielsweise Port 83 geöffnet sein. Sie finden dies im Anwendungspaket in der Datei „servicemanifest.xml“. (Es gibt möglicherweise mehrere Dateien des Namens „servicemanifest.xml“.)

    c. Die meisten Beispielanwendungen verwenden Port 80 und 8081. Fügen Sie diese also später hinzu, wenn Sie Beispiele in diesem Cluster bereitstellen möchten. ![Ports][Ports]

10. Sie müssen keine **Platzierungseigenschaften** hinzufügen, da vom System eine Standard-Platzierungseigenschaft „NodeTypeName“ hinzugefügt wird. Wenn die Anwendung dies erfordert, können Sie weitere hinzufügen.

## Konfigurieren der Sicherheit

Zu diesem Zeitpunkt unterstützt Service Fabric die Sicherung von Clustern nur über ein X.509-Zertifikat. Bevor Sie diesen Vorgang starten, müssen Sie Ihr Zertifikat in Key Vault hochladen. Weitere Informationen zur Vorgehensweise finden Sie unter [Service Fabric-Clustersicherheit](service-fabric-cluster-security.md).

Die Cluster-Sicherung ist optional, wird jedoch dringend empfohlen. Wenn Sie Ihren Cluster nicht sichern, müssen Sie für **Sicherheitsmodus** die Option **Keine** auswählen.

Sicherheitsaspekte und Anweisungen finden Sie unter [Service Fabric-Clustersicherheit](service-fabric-cluster-security.md).

![Screenshot der Sicherheitskonfigurationen im Azure-Portal.][SecurityConfigs]

## Optional: Konfigurieren der Diagnose

Die Diagnose ist standardmäßig in Ihrem Cluster aktiviert, um die Behebung von Problemen in Ihrem Cluster zu vereinfachen. Wenn Sie die Diagnose deaktivieren möchten:

1. Navigieren Sie zum Blatt **Diagnose-Konfigurationen**.

2. Ändern Sie den **Status** in **Aus**.

## Optional: Festlegen der Service Fabric-Clustereinstellungen

Mit dieser fortgeschrittenen Option können Sie die Standardeinstellungen für den Service Fabric-Cluster ändern. Es wird empfohlen, die Standardeinstellungen unverändert zu lassen, wenn Ihre Anwendung oder Ihr Cluster nicht unbedingt eine Änderung erfordert.

## Abschließen der Clustererstellung

Um die Erstellung des Clusters abzuschließen, klicken Sie auf **Zusammenfassung**, um die bereitgestellten Konfigurationen anzuzeigen, oder laden Sie die Azure-Ressourcen-Manager-Vorlage herunter, die zur Bereitstellung des Clusters verwendet werden soll. Nachdem Sie die Pflichteinstellungen angegeben haben, wird die Schaltfläche **Erstellen** aktiviert, und Sie können darauf klicken, um mit der Clustererstellung zu beginnen.

Sie können den Verlauf der Erstellung in den Benachrichtigungen finden. (Klicken Sie auf das Glockensymbol in der Nähe der Statusleiste am oberen rechten Bildschirmrand.) Wenn Sie beim Erstellen des Clusters auf **An Startmenü anheften** geklickt haben, wird **Service Fabric-Cluster wird bereitgestellt** im **Startmenü** angezeigt.

![Screenshot des Startmenüs mit der Anzeige „Service Fabric-Cluster wird bereitgestellt“.][Notifications]

## Anzeigen des Clusterstatus

Wenn die Bereitstellung abgeschlossen ist, können Sie Ihren Cluster im Portal prüfen.

1. Wechseln Sie zu **Durchsuchen**, und klicken Sie auf **Service Fabric-Cluster**.

2. Suchen Sie Ihren Cluster, und klicken Sie darauf. ![Screenshot der Suche nach dem Cluster im Portal.][BrowseCluster]

3. Sie können jetzt die Details Ihres Clusters, einschließlich der öffentlichen IP-Adresse, im Dashboard anzeigen. Wenn Sie die Maus über **Öffentliche IP-Adresse des Clusters** bewegen, wird eine Zwischenablage angezeigt, in die Sie die Adresse durch Klicken kopieren können. ![Screenshot der Clusterdetails im Dashboard.][ClusterDashboard]

  Der Abschnitt **Knotenmonitor** auf dem Dashboardblatt des Clusters gibt die Anzahl der virtuellen Computer an, die fehlerfrei bzw. fehlerhaft sind. Weitere Informationen zur Clusterintegrität finden Sie unter [Einführung in die Service Fabric-Integritätsüberwachung](service-fabric-health-introduction.md).

>[AZURE.NOTE] Um Verfügbarkeit sicherzustellen und den Zustand beizubehalten, muss eine bestimmte Anzahl von Knoten in einem Service Fabric-Cluster stets in Betrieb sein. Dies wird auch als „Aufrechterhalten eines Quorums“ bezeichnet. Daher ist es üblicherweise nicht sicher, alle Computer innerhalb des Clusters herunterzufahren, sofern Sie nicht zunächst eine [vollständige Sicherung des Zustands](service-fabric-reliable-services-backup-restore.md) durchgeführt haben.

## Herstellen einer Verbindung mit dem Cluster und Bereitstellen einer Anwendung

Nach Abschluss der Clustereinrichtung können Sie jetzt eine Verbindung herstellen und mit dem Bereitstellen von Anwendungen beginnen. Starten Sie Windows PowerShell auf einem Computer, auf dem Sie das Service Fabric SDK installiert haben. Führen Sie zum Herstellen einer Verbindung mit dem Cluster einen der folgenden PowerShell-Befehle aus, je nachdem, ob sie einen sicheren oder einen unsicheren Cluster erstellt haben:

- Option 1: Herstellen einer Verbindung mit einem unsicheren Cluster.

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10
    ```

- Option 2: Herstellen einer Verbindung mit einem sicheren Cluster.

    1. Führen Sie die folgenden Befehle aus, um das Zertifikat auf dem Computer einzurichten, den Sie zum Ausführen des PowerShell-Befehls „Connect-serviceFabricCluster“ verwenden möchten.

        ```powershell
        Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
                -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
                -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
        ```

    2. Führen Sie den folgenden PowerShell-Befehl aus, um eine Verbindung mit einem sicheren Cluster herzustellen. Die Zertifikatdetails entsprechen Ihren Angaben im Portal.

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
                  -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
                  -StoreLocation CurrentUser -StoreName My
        ```

        Beispielsweise sollte der obige PowerShell-Befehl ähnlich wie der Folgende aussehen:

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
                  -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
                  -StoreLocation CurrentUser -StoreName My
        ```

Nachdem Sie die Verbindung hergestellt haben, führen Sie die folgenden Befehle zum Bereitstellen der Anwendung aus. Ersetzen Sie dabei die angezeigten Pfade durch die entsprechenden Pfade auf Ihrem Computer. Im folgenden Beispiel wird die Beispielanwendung zur Wortzählung bereitgestellt:

1. Kopieren Sie das Paket in den Cluster, mit dem Sie zuvor eine Verbindung hergestellt haben.

    ```powershell
    $applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
    ```

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
    ```
2. Registrieren Sie ihren Anwendungstyp bei Service Fabric.

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount"
    ```

3. Erstellen Sie eine neue Instanz auf dem soeben registrierten Anwendungstyp.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0
    ```

4. Öffnen Sie jetzt den gewünschten Browser, und stellen Sie eine Verbindung mit dem Endpunkt her, auf dem die Anwendung lauscht. Bei der Beispielanwendung WordCount sieht die URL wie folgt aus:

    http://sfcluster4doc.westus.cloudapp.azure.com:31000

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Nächste Schritte

- [Verwalten von Service Fabric-Anwendungen in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Service Fabric-Clustersicherheit](service-fabric-cluster-security.md)
- [Einführung in das Service Fabric-Integritätsmodell](service-fabric-health-introduction.md)

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[Ports]: ./media/service-fabric-cluster-creation-via-portal/ports.png
[SFConfigurations]: ./media/service-fabric-cluster-creation-via-portal/SFConfigurations.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[BrowseCluster]: ./media/service-fabric-cluster-creation-via-portal/browse.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[SecureConnection]: ./media/service-fabric-cluster-creation-via-portal/SecureConnection.png

<!---HONumber=AcomDC_0218_2016-->