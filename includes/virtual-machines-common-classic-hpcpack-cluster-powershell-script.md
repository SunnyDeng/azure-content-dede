



Führen Sie das PowerShell-Skript für die HPC Pack-IaaS-Bereitstellung auf einem Clientcomputer aus, um einen vollständigen HPC Pack-Cluster in Azure-Infrastrukturdiensten (IaaS) bereitzustellen. Das Skript bietet mehrere Bereitstellungsoptionen. Sie können Cluster-Computeknoten hinzufügen, auf denen unterstützte Linux-Distributionen oder Windows Server-Betriebssysteme ausgeführt werden.

Je nach Umgebung und Auswahlmöglichkeiten kann mit dem Skript die gesamte Clusterinfrastruktur erstellt werden, z. B. das virtuelle Azure-Netzwerk, Speicherkonten, Clouddienste, Domänencontroller, SQL-Datenbanken (remote oder lokal), Hauptknoten, Brokerknoten, Computeknoten und Azure-Clouddienstknoten („Burst“ oder PaaS). Alternativ dazu kann für das Skript auch bereits vorhandene Azure-Infrastruktur mit anschließender Erstellung des HPC-Clusterhauptknotens und der Brokerknoten, Computeknoten und Azure-Burstknoten verwendet werden.


Hintergrundinformationen zur Planung eines HPC Pack-Clusters finden Sie unter [Produkttest und Planung](https://technet.microsoft.com/library/jj899596.aspx) und [Erste Schritte](https://technet.microsoft.com/library/jj899590.aspx) in der HPC Pack-TechNet-Bibliothek.

>[AZURE.NOTE]Sie können auch eine Azure-Ressourcen-Manager-Vorlage verwenden, um einen HPC Pack-Cluster bereitzustellen. Ein Beispiel hierfür finden Sie unter [Erstellen eines HPC-Clusters](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/), [Erstellen eines HPC-Clusters mit einem benutzerdefinierten Computeknotenimage](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/) oder [Erstellen eines HPC-Clusters mit Linux-Computeknoten](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

## Voraussetzungen

* **Azure-Abonnement:** Sie können ein Abonnement entweder im Azure Global- oder Azure China-Dienst nutzen. Ihre Abonnementlimits haben Einfluss darauf, wie viele und welche Arten von Knoten Sie bereitstellen können. Informationen finden Sie unter [Grenzwerte, Kontingente und Einschränkungen für Azure-Abonnements und -Dienste](../azure-subscription-service-limits.md).


* **Windows-Clientcomputer mit Azure PowerShell 0.8.7 oder höher (installiert und konfiguriert):** Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md). Das Skript wird unter Azure Service Management ausgeführt.


* **HPC Pack-IaaS-Bereitstellungsskript:** Laden Sie die neueste Version des Skripts aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949) herunter, und entpacken Sie sie. Führen Sie `New-HPCIaaSCluster.ps1 –Version` aus, um die Version des Skripts zu überprüfen. Dieser Artikel basiert auf der Skriptversion 4.4.0.

* **Skriptkonfigurationsdatei:** Sie müssen eine XML-Datei erstellen, die vom Skript zum Konfigurieren des HPC-Clusters verwendet wird. Informationen und Beispiele finden Sie in den Abschnitten weiter unten in diesem Artikel.


## Syntax

```
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
>[AZURE.NOTE]Sie müssen das Skript als Administrator ausführen.

### Parameter

* **ConfigFile:** Gibt den Dateipfad der Konfigurationsdatei zum Beschreiben des HPC-Clusters an. Weitere Informationen finden Sie unter [Konfigurationsdatei](#Configuration-file) in diesem Thema oder in der Datei „Manual.rtf“ im Ordner mit dem Skript.

* **AdminUserName:** Gibt den Benutzernamen an. Wenn die Domänengesamtstruktur vom Skript erstellt wird, wird er zum lokalen Administratorbenutzernamen für alle virtuellen Computer und zum Namen des Domänenadministrators. Wenn die Domänengesamtstruktur bereits vorhanden ist, gibt dieser Parameter den Domänenbenutzer als lokalen Administratorbenutzernamen zum Installieren von HPC Pack an.

* **AdminPassword:** Gibt das Kennwort des Administrators an. Wenn der Parameter in der Befehlszeile nicht angegeben ist, werden Sie vom Skript zum Eingeben des Kennworts aufgefordert.

* **HPCImageName** (optional): Gibt den Namen des Images für den virtuellen HPC Pack-Computer an, das zum Bereitstellen des HPC-Clusters verwendet wird. Es muss sich um ein von Microsoft bereitgestelltes HPC Pack-Image aus dem Azure Marketplace handeln. Wenn der Parameter nicht angegeben wird (in den meisten Fällen zu empfehlen), wählt das Skript das zuletzt veröffentlichte HPC Pack-Image aus.

    >[AZURE.NOTE] Für die Bereitstellung tritt ein Fehler auf, wenn Sie kein gültiges HPC Pack-Image angeben.

* **LogFile** (optional): Gibt den Pfad der Bereitstellungsprotokolldatei an. Wenn der Parameter nicht angegeben ist, erstellt das Skript eine Protokolldatei im Verzeichnis „temp“ des Computers, von dem das Skript ausgeführt wird.

* **Force** (optional): Unterdrückt alle Bestätigungsaufforderungen.

* **NoCleanOnFailure** (optional): Gibt an, dass die virtuellen Azure-Computer, deren Bereitstellung nicht erfolgreich ist, nicht entfernt werden. Sie müssen diese virtuellen Computer manuell entfernen, bevor Sie das Skript zum Fortsetzen der Bereitstellung erneut ausführen. Andernfalls schlägt die Bereitstellung fehl.

* **PSSessionSkipCACheck** (optional): Für jeden Clouddienst mit von diesem Skript bereitgestellten virtuellen Computern wird von Azure automatisch ein selbst signiertes Zertifikat generiert, und alle virtuellen Computer im Clouddienst verwenden dieses Zertifikat als standardmäßiges Zertifikat für die Windows-Remoteverwaltung (WinRM). Zum Bereitstellen von HPC-Funktionen in diesen virtuellen Azure-Computern installiert das Skript standardmäßig vorübergehend diese Zertifikate im Speicher „Lokaler Computer\\Vertrauenswürdige Stammzertifizierungsstellen“ des Clientcomputers, um den Sicherheitsfehler „Keine vertrauenswürdige Zertifizierungsstelle“ während der Ausführung des Skripts zu unterdrücken. Die Zertifikate werden entfernt, wenn das Skript beendet wird. Wenn dieser Parameter angegeben wird, werden die Zertifikate nicht auf dem Clientcomputer installiert, und die Sicherheitswarnung wird unterdrückt.

    >[AZURE.IMPORTANT] Dieser Parameter wird für Bereitstellungen in der Produktion nicht empfohlen.

### Beispiel

Im folgenden Beispiel wird mit der Konfigurationsdatei *MyConfigFile.xml* ein neuer HPC Pack-Cluster erstellt, und es werden administrative Anmeldeinformationen zum Installieren des Clusters angegeben.

```
New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### Zusätzliche Überlegungen

* Das Skript verwendet das HPC Pack-VM-Image des Azure Marketplace zum Erstellen des Clusterhauptknotens. Das neueste Image basiert auf Windows Server 2012 R2 Datacenter mit installiertem HPC Pack 2012 R2 Update 3.

* Das Skript kann die Auftragsübermittlung optional über das HPC Pack-Webportal oder die HPC Pack-REST-API aktivieren.

* Das Skript kann optional benutzerdefinierte Skripts für Schritte vor und nach der Konfiguration auf dem Hauptknoten ausführen, falls Sie zusätzliche Software installieren oder andere Einstellungen konfigurieren möchten.


## Konfigurationsdatei

Die Konfigurationsdatei für das Bereitstellungsskript ist eine XML-Datei. Die Schemadatei „HPCIaaSClusterConfig.xsd“ befindet sich im Ordner des HPC Pack-IaaS-Bereitstellungsskripts. **IaaSClusterConfig** ist das Stammelement der Konfigurationsdatei mit den untergeordneten Elementen. Dies ist in der Datei „Manual.rtf“ im Ordner des Bereitstellungsskripts ausführlich beschrieben. Beispieldateien für unterschiedliche Szenarien finden Sie unter [Beispielkonfigurationsdateien](#Example-configuration-files) in diesem Artikel.

## Beispielkonfigurationsdateien

### Beispiel 1

Mit der folgenden Konfigurationsdatei wird ein HPC Pack-Cluster in einer vorhandenen Domänengesamtstruktur bereitgestellt. Der Cluster verfügt über einen Hauptknoten mit lokalen Datenbanken und zwölf Computeknoten mit angewendeter BGInfo-VM-Erweiterung. Die automatische Installation von Windows-Updates ist für alle virtuellen Computer in der Domänengesamtstruktur deaktiviert. Alle Clouddienste werden direkt am Standort in Ostasien erstellt. Die Computeknoten werden in drei Clouddiensten und drei Speicherkonten erstellt (_MyHPCCN-0001_ bis _MyHPCCN-0005_ in _MyHPCCNService01_ und _mycnstorage01_, _MyHPCCN-0006_ bis _MyHPCCN0010_ in _MyHPCCNService02_ und _mycnstorage02_ sowie _MyHPCCN-0011_ bis _MyHPCCN-0012_ in _MyHPCCNService03_ und _mycnstorage03_). Die Computeknoten werden aus einem vorhandenen privaten Image erstellt, das über einen Computeknoten erfasst wird. Der Dienst zum automatischen Vergrößern und Verkleinern ist mit standardmäßigen Vergrößerungs- und Verkleinerungsintervallen aktiviert.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### Beispiel 2

Mit der folgenden Konfigurationsdatei wird ein HPC Pack-Cluster in einer vorhandenen Domänengesamtstruktur bereitgestellt. Der Cluster enthält einen Hauptknoten, einen Datenbankserver mit einem 500-GB-Datenträger, zwei Brokerknoten, auf denen das Betriebssystem Windows Server 2012 R2 ausgeführt wird, und fünf Computeknoten, auf denen das Betriebssystem Windows Server 2012 R2 ausgeführt wird. Der MyHPCCNService-Clouddienst wird in der Affinitätsgruppe *MyIBAffinityGroup* erstellt, und alle anderen Clouddienste werden in der Affinitätsgruppe *MyAffinityGroup* erstellt. Die HPC-Auftragsplaner-REST-API und das HPC-Webportal sind auf dem Hauptknoten aktiviert.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```

### Beispiel 3

Die folgende Konfigurationsdatei erstellt eine neue Domänengesamtstruktur und stellt einen HPC Pack-Cluster bereit, der über einen Hauptknoten mit lokalen Datenbanken und 20 Linux-Computeknoten verfügt. Alle Clouddienste werden direkt am Standort in Ostasien erstellt. Die Linux-Computeknoten werden in vier Clouddiensten und vier Speicherkonten erstellt (_MyLnxCN-0001_ bis _MyHPCCN-0005_ in _MyLnxCNService01_ und _mylnxstorage01_, _MyLnxCN-0006_ bis _MyLnxCN-0010_ in _MyLnxCNService02_ und _mylnxstorage02_, _MyLnxCN-0011_ bis _MyLnxCN-0015_ in _MyLnxCNService03_ und _mylnxstorage03_ sowie _MyLnxCN-0016_ bis _MyLnxCN-0020_ in _MyLnxCNService04_ und _mylnxstorage04_). Die Computeknoten werden aus einem Linux-Image (OpenLogic CentOS Version 7.0) erstellt.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>20</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
    <SSHKeyPairForRoot>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </SSHKeyPairForRoot>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```


### Beispiel 4

Die folgende Konfigurationsdatei stellt einen HPC Pack-Cluster bereit, der über einen Hauptknoten mit lokalen Datenbanken und fünf Computeknoten verfügt, auf denen das Betriebssystem Windows Server 2008 R2 ausgeführt wird. Alle Clouddienste werden direkt am Standort in Ostasien erstellt. Der Hauptknoten fungiert als Domänencontroller der Domänengesamtstruktur.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2008R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### Beispiel 5

Mit der folgenden Konfigurationsdatei wird ein HPC Pack-Cluster in einer vorhandenen Domänengesamtstruktur bereitgestellt. Der Cluster verfügt über einen Hauptknoten mit lokalen Datenbanken, es werden zwei Vorlagen für Azure-Knoten erstellt, und drei mittelgroße Azure-Knoten werden für die Azure-Knotenvorlage _AzureTemplate1_ erstellt. Eine Skriptdatei wird auf dem Hauptknoten ausgeführt, nachdem der Hauptknoten konfiguriert wurde.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```
## Bekannte Probleme


* **Fehler „VNet nicht vorhanden“:** Wenn Sie das HPC Pack-IaaS-Bereitstellungsskript ausführen, um in Azure gleichzeitig mehrere Cluster unter einem Abonnement bereitzustellen, schlagen eine oder mehrere Bereitstellungen unter Umständen mit einem Fehler der Art „VNet *VNet-Name* nicht vorhanden“ fehl. Führen Sie das Skript für die fehlgeschlagene Bereitstellung erneut aus, wenn dieser Fehler auftritt.

* **Problem beim Zugreifen auf das Internet über das virtuelle Azure-Netzwerk:** Wenn Sie einen HPC Pack-Cluster mit einem neuen Domänencontroller erstellen, indem Sie das Bereitstellungsskript verwenden, oder wenn Sie einen virtuellen Computer manuell zu einem Domänencontroller heraufstufen, können Probleme beim Herstellen der Internetverbindung für die virtuellen Computer im virtuellen Azure-Netzwerk auftreten. Dies kann passieren, wenn auf dem Domänencontroller automatisch ein DNS-Weiterleitungsserver konfiguriert wird und dieser Server die Auflösung nicht richtig durchführt.

    Sie können dieses Problem umgehen, indem Sie sich am Domänencontroller anmelden und entweder die Konfigurationseinstellung für die Weiterleitung entfernen oder einen gültigen DNS-Weiterleitungsserver konfigurieren. Klicken Sie hierzu im Server-Manager auf **Extras** > **DNS**, um den DNS-Manager zu öffnen, und doppelklicken Sie dann auf **Weiterleitungen**.

* **Problem beim Zugreifen auf das RDMA-Netzwerk über virtuelle Computer der Größe A8 oder A9:** Wenn Sie Windows Server-Computeknoten- oder -Brokerknoten-VMs der Größe A8 oder A9 mit dem Bereitstellungsskript hinzufügen, kann es beim Verbinden dieser virtuellen Computer mit dem RDMA-Anwendungsnetzwerk zu Problemen kommen. Ein möglicher Grund hierfür ist die falsche Installation der HpcVmDrivers-Erweiterung, wenn dem Cluster die virtuellen Computer der Größe A8 oder A9 hinzugefügt werden. Beispielsweise kann es vorkommen, dass die Erweiterung im Installationsstatus verharrt.

    Um dieses Problem zu umgehen, überprüfen Sie auf den virtuellen Computern zunächst den Status der Erweiterung. Wenn die Erweiterung nicht richtig installiert ist, können Sie versuchen, die Knoten aus dem HPC-Cluster zu entfernen und sie dann neu hinzuzufügen. Beispielsweise können Sie Computeknoten-VMs hinzufügen, indem Sie das Skript „Add-HpcIaaSNode.ps1“ auf dem Hauptknoten ausführen.

<!---HONumber=AcomDC_0323_2016-->