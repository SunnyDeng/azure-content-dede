<properties
 pageTitle="Erste Schritte mit einem HPC Pack-Cluster zum Ausführen von Excel- und SOA-Workloads | Microsoft Azure"
	description="."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>
<tags
ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="08/18/2015"
	ms.author="danlep"/>

# Erste Schritte mit einem HPC Pack-Cluster in Azure zum Ausführen von Excel- und SOA-Workloads

In diesem Artikel erfahren Sie, wie Sie einen HPC Pack-Cluster mithilfe einer Azure-Schnellstartvorlage oder eines Azure PowerShell-Bereitstellungsskripts für Azure-Infrastrukturdienste (IaaS) bereitstellen. Hierzu werden VM-Images aus Azure Marketplace verwendet, die für die Ausführung von Microsoft Excel- oder SOA-Workloads mit HPC Pack konzipiert sind. Mit dem Cluster können Sie einfache Excel-HPC- und SOA-Dienste über einen lokalen Clientcomputer ausführen. Zu den Excel-HPC-Diensten zählen die Abladung von Excel-Arbeitsmappen sowie benutzerdefinierte Funktionen (User-Defined Functions, UDFs) von Excel.

Das folgende Diagramm gibt einen allgemeinen Überblick über den zu erstellenden HPC Pack-Cluster:

![HPC-Cluster mit Knoten, auf denen Excel-Workloads ausgeführt werden][scenario]

## Voraussetzungen

* **Clientcomputer**: Sie benötigen einen Windows-basierten Clientcomputer, um das Azure PowerShell-Clusterbereitstellungsskript auszuführen (sofern Sie diese Bereitstellungsmethode verwenden) und Excel- und SOA-Beispielaufträge an den Cluster zu übermitteln.

* **Azure-Abonnement**: Falls Sie noch kein Konto haben, können Sie in wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/).

* **Kernkontingent**: Unter Umständen muss das Kontingent für die Kerne erhöht werden. Dies gilt insbesondere, wenn Sie mehrere Clusterknoten mit Multicore-VM-Größen bereitstellen. Beachten Sie bei Verwendung einer Azure-Schnellstartvorlage, dass das Kernkontingent im Ressourcen-Manager pro Azure-Region gilt und gegebenenfalls in einer bestimmten Region erhöht werden muss. Weitere Informationen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md). Wenn Sie ein Kontingent erhöhen möchten, können Sie kostenlos eine [Anfrage an den Onlinekundensupport richten](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).


## Schritt 1: Einrichten eines HPC Pack-Clusters in Azure

Wir zeigen Ihnen zwei Methoden für die Clustereinrichtung. Bei der ersten kommen eine Azure-Schnellstartvorlage und das Azure-Vorschauportal zum Einsatz, bei der zweiten wird ein Azure PowerShell-Bereitstellungsskript verwendet.


### Mit einer Schnellstartvorlage
Verwenden Sie eine Azure-Schnellstartvorlage, um schnell und einfach einen HPC Pack-Cluster im Azure-Vorschauportal bereitzustellen. Wenn Sie die Vorlage im Portal öffnen, erscheint eine einfache Benutzeroberfläche, über die Sie die Einstellungen für Ihren Cluster angeben können. Gehen Sie wie folgt vor:

1. Besuchen Sie bei GitHub die [Seite mit der Vorlage für die HPC-Clustererstellung](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Machen Sie sich ggf. mit den Informationen zur Vorlage sowie mit dem Quellcode vertraut.

2. Klicken Sie auf **Deploy to Azure**, um im Azure-Vorschauportal eine Bereitstellung mit der Vorlage zu beginnen.

    ![Vorlage für Azure bereitstellen][github]

3. Führen Sie im Portal die folgenden Schritte aus, um die Parameter für die HPC-Clustervorlage einzugeben:

    a. Klicken Sie auf der Seite **Vorlage bearbeiten** auf **Speichern**.

    ![Vorlage speichern][template]

    b. Geben Sie auf der Seite **Parameter** Werte für die Vorlagenparameter ein. (Klicken Sie auf das Symbol neben einer Einstellung, um Hilfeinformationen anzuzeigen.) Der Bildschirm weiter unten zeigt Beispielwerte. In diesem Beispiel erstellen wir einen neuen HPC Pack-Cluster namens *hpc01* in der Domäne *hpc.local* mit einem Haupt- und zwei Serverknoten. Die Serverknoten werden auf der Grundlage eines HPC Pack-VM-Image erstellt, das auch Microsoft Excel beinhaltet.

    ![Parameter eingeben][parameters]

    >[AZURE.NOTE]Der virtuelle Computer für den Hauptknoten wird automatisch auf der Grundlage des [neuesten Marketplace-Image](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) von HPC Pack 2012 R2 für Windows Server 2012 R2 erstellt. Aktuell basiert das Image auf HPC Pack 2012 R2 Update 2.
    >
    >Die virtuellen Computer für die Serverknoten werden auf der Grundlage des neuesten Image der ausgewählten Serverknotenfamilie erstellt. Wählen Sie die Option **ComputeNode** aus, um das neueste HPC Pack 2012 R2 Update 2-Serverimage für allgemeine Anwendungen zu erhalten. Wählen Sie die Option **ComputeNodeWithExcel** aus, um das neueste HPC Pack-Serverknotenimage mit einer Evaluierungsversion von Microsoft Excel Professional Plus 2013 zu erhalten. Wenn Sie einen Cluster für allgemeine SOA-Sitzungen oder für die Abladung von Excel-UDFs bereitstellen möchten, wählen Sie die Option **ComputeNode** (ohne Installation von Excel) aus.
    >
    >Wenn Sie **ComputeNodeWithExcel** für Produktionsworkloads verwenden, müssen Sie eine gültige Excel-Lizenz angeben, um Excel auf den Computeknoten zu aktivieren. Andernfalls läuft die Evaluierungsversion von Excel möglicherweise innerhalb von 30 Tagen ab, und bei der Ausführung der Excel-Arbeitsmappe würde ständig die Ausnahme „COMExeption“ (0x800AC472) auftreten. In diesem Fall können Sie über die HPC Cluster Manager-Konsole für alle Excel-Computeknoten den Hauptknoten bei „clusrun“ (%ProgramFiles(x86)%\\Microsoft Office\\Office15\\OSPPREARM.exe) anmelden, um den Evaluierungszeitraum von 30 Tagen für Excel zurückzusetzen. Die Kulanzfrist kann höchstens zweimal zurückgesetzt werden. Anschließend müssen Sie eine gültige Excel-Lizenz bereitstellen.

    c. Wählen Sie das Abonnement aus.

    d. Erstellen Sie eine neue Ressourcengruppe für den Cluster (beispielsweise *hpc01RG*).

    e. Wählen Sie einen Ort für die Ressourcengruppe (beispielsweise den Osten der USA).

    f. Lesen Sie die rechtlichen Bedingungen. Klicken Sie auf **Kaufen**, sofern Sie den Bedingungen zustimmen.

    g. Legen Sie die Werte für die Vorlage fest, und klicken Sie anschließend auf **Erstellen**.

    ![Cluster erstellen][create]

3.	Wenn die Bereitstellung nach etwa 30 Minuten abgeschlossen ist, exportieren Sie die Clusterzertifikatsdatei aus dem Clusterhauptknoten. Dieses öffentliche Zertifikat wird in einem späteren Schritt zur serverseitigen Authentifizierung für eine sichere HTTP-Bindung auf dem Clientcomputer importiert.

    a. Stellen Sie über das Azure-Vorschauportal eine Remotedesktopverbindung mit dem Hauptknoten her.

     ![Verbindung mit dem Hauptknoten herstellen][connect]

    b. Exportieren Sie das Hauptknotenzertifikat (zu finden unter „Cert:\\LocalMachine\\My“) mithilfe von Standardverfahren und dem Zertifikat-Manager ohne den privaten Schlüssel. In diesem Beispiel wird *CN = hpc01.eastus.cloudapp.azure.com* exportiert.

    ![Zertifikat exportieren][cert]

### Mit dem HPC Pack-IaaS-Bereitstellungsskript

Das HPC Pack-IaaS-Bereitstellungsskript ist eine weitere vielseitige Bereitstellungsmethode für HPC Pack-Cluster. Es wird im Azure-Service-Management (ASM)-Modus ausgeführt. Die Vorlage wird dagegen im Azure-Ressourcen-Manager (ARM)-Modus ausgeführt. Das Skript ist außerdem mit einem Abonnement im Azure Global- oder Azure China-Dienst kompatibel.

**Zusätzliche Voraussetzungen**

* **Azure PowerShell**: [Installieren und konfigurieren Sie Azure PowerShell](../powershell-install-configure.md) (ab Version 0.8.10) auf Ihrem Clientcomputer.

* **HPC Pack-IaaS-Bereitstellungsskript**: Laden Sie die neueste Version des Skripts aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949) herunter, und entpacken Sie sie. Führen Sie `New-HPCIaaSCluster.ps1 –Version` aus, um die Version des Skripts zu überprüfen. Dieser Artikel basiert auf der Skriptversion 4.4.0 oder höher.

**Erstellen der Konfigurationsdatei**

 Das HPC Pack-IaaS-Bereitstellungsskript verwendet als Eingabe eine XML-Konfigurationsdatei mit der Beschreibung der Infrastruktur des HPC-Clusters. Geben Sie in der folgenden Beispielkonfigurationsdatei Werte für Ihre Umgebung ein, um einen Cluster mit einem Haupt- und 18 Serverknoten bereitzustellen, die auf der Grundlage des Serverknotenimage mit Microsoft Excel erstellt werden. Weitere Informationen zur Konfigurationsdatei finden Sie in der Datei „Manual.rtf“ (im Skriptordner) oder in der [Skriptdokumentation](https://msdn.microsoft.com/library/azure/dn864734.aspx).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
<PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName HPCPackInstalled="true">96316178b0644ae08bc4e037635ce104__HPC-Pack-2012R2-Update2-CN-Excel-4.4.4864.0-WS2012R2-ENU</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Hinweise zur Konfigurationsdatei**

* Der Wert für **VMName** des Hauptknotens muss exakt dem Wert für **ServiceName** entsprechen.

* Geben Sie unbedingt **EnableWebPortal** an, damit das Hauptknotenzertifikat generiert und exportiert wird.

* Das PowerShell-Konfigurationsskript „PostConfig.ps1“ konfiguriert bestimmte Einstellungen des Hauptknotens. Es richtet beispielsweise die Verbindungszeichenfolge für den Azure-Speicher ein, entfernt die Serverknotenrolle aus dem Hauptknoten und schaltet alle Knoten bei der Bereitstellung online. Beispielskript:

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Ausführen des Skripts**

1. Öffnen Sie die PowerShell-Konsole auf dem Clientcomputer als Administrator.

2. Navigieren Sie zum Skriptordner (in diesem Beispiel „E:\\IaaSClusterScript“).

    ```
    cd E:\IaaSClusterScript
```

4. Führen Sie den folgenden Befehl aus, um den HPC Pack-Cluster bereitzustellen. In diesem Beispiel wird davon ausgegangen, dass sich die Konfigurationsdatei unter „E:\\HPCDemoConfig.xml“ befindet.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
```

Das HPC Pack-Bereitstellungsskript wird ausgeführt. Dieser Vorgang kann eine Weile dauern. Durch das Skript wird unter anderem das Clusterzertifikat exportiert, heruntergeladen und auf dem Clientcomputer im Ordner „Dokumente“ des aktuellen Benutzers gespeichert. Das Skript generiert eine Meldung wie die folgende: In einem anderen Schritt wird das Zertifikat dann in den entsprechenden Zertifikatspeicher importiert.

```
You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
 C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_2015070716
2011.cer
```

## Schritt 2. Abladen von Excel-Arbeitsmappen und Ausführen von UDFs über einen lokalen Client

### Abladen von Excel-Arbeitsmappen

Führen Sie die folgenden Schritte durch, um eine Excel-Arbeitsmappe für die Ausführung auf dem HPC Pack-Cluster in Azure abzuladen. Hierzu muss Excel 2010 oder 2013 bereits auf dem Clientcomputer installiert sein.

1. Verwenden Sie eine der Methoden aus Schritt 1, um einen HPC Pack-Cluster mit dem Excel-Serverknotenimage bereitzustellen. Besorgen Sie sich die Clusterzertifikatsdatei (CER-Datei) sowie den Clusterbenutzernamen und das Kennwort.

2. Importieren Sie auf dem Clientcomputer das Clusterzertifikat unter „Cert:\\<Aktueller Benutzer>\\Root“.

3. Vergewissern Sie sich, dass Excel installiert ist. Erstellen Sie auf dem Clientcomputer in dem Verzeichnis, in dem sich auch die Datei „Excel.exe“ befindet, eine Datei mit der Bezeichnung „Excel.exe.config“ und dem folgenden Inhalt. Dadurch wird sichergestellt, dass das Excel-COM-Add-In für HPC Pack 2012 R2 erfolgreich geladen wird.

    ```
<?xml version="1.0"?>
<configuration>
  <startup useLegacyV2RuntimeActivationPolicy="true">
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
  </startup>
</configuration>
```
4.	Laden Sie die gesamte [HPC Pack 2012 R2 Update 2-Installation](http://www.microsoft.com/download/details.aspx?id=47755) herunter, und installieren Sie den HPC Pack-Client. Alternativ können Sie auch die [HPC Pack 2012 R2 Update 2-Clienthilfsprogramme](https://www.microsoft.com/download/details.aspx?id=47754) und die entsprechende weitervertreibbare Visual C++ 2010-Komponente für Ihren Computer ([x64](http://www.microsoft.com/download/details.aspx?id=14632) oder [x86](https://www.microsoft.com/download/details.aspx?id=5555)) herunterladen und installieren.

5.	In diesem Beispiel verwenden wir eine Excel-Beispielarbeitsmappe namens „ConvertiblePricing\_Complete.xlsb“, die Sie [hier](https://www.microsoft.com/de-DE/download/details.aspx?id=2939) herunterladen können.

6.	Kopieren Sie die Excel-Arbeitsmappe in einen Ordner (beispielsweise „D:\\Excel\\Run“).

7.	Öffnen Sie die Excel-Arbeitsmappe. Klicken Sie auf dem Menüband **Entwickeln** auf **COM-Add-Ins**, und vergewissern Sie sich, dass das HPC Pack-Excel-COM-Add-In erfolgreich geladen wurde (siehe folgende Abbildung).

    ![Excel-Add-In für HPC Pack][addin]

8.	Bearbeiten Sie das VBA-Makro „HPCControlMacros“ in Excel. Ändern Sie hierzu die kommentierten Zeilen, wie im folgenden Skript zu sehen. Ersetzen Sie die Werte durch entsprechende Werte für Ihre Umgebung.

    ![Excel-Makro für HPC Pack][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.	Kopieren Sie die Excel-Arbeitsmappe in ein Uploadverzeichnis (beispielsweise „D:\\Excel\\Upload“). Orientieren Sie sich dabei an der Angabe in der Konstanten „HPC\_DependsFiles“ aus dem VBA-Makro.

10.	Klicken Sie auf dem Arbeitsblatt auf die Schaltfläche **Cluster**, um die Arbeitsmappe auf dem Azure-IaaS-Cluster auszuführen.

### Ausführen von Excel-UDFs

Wenn Sie Excel-UDFs verwenden möchten, führen Sie zur Einrichtung des Clientcomputers die weiter oben angegebenen Schritte 1 bis 3 durch. Für Excel-UDFs muss die Excel-Anwendung nicht auf den Serverknoten installiert sein. Sie können in Schritt 1 also anstelle des Serverknotenimage mit Excel ein normales Serverknotenimage auswählen.

>[AZURE.NOTE]Im Clusterconnector-Dialogfeld von Excel 2010 und 2013 sind maximal 34 Zeichen zulässig. Längere Clusternamen wie etwa „hpcexcelhn01.southeastasia.cloudapp.azure.com“ passen nicht in das Dialogfeld. Dies kann folgendermaßen umgangen werden: Wenden Sie QFE-Update 2 (KB3085833, [hier](http://www.microsoft.com/de-DE/download/details.aspx?id=48725) als Download verfügbar) für die SOA-Sitzungs-API auf dem Clientcomputer an, und legen Sie dann im Dialogfeld als Name des Clusterhauptknotens eine computerweite Variable wie etwa *CCP\_IAASHN* mit dem Wert des langen Clusternamens und der Eingabe *%CCP\_IAASHN%* fest.

Führen Sie nach erfolgreicher Bereitstellung des Clusters die folgenden Schritte durch, um eine integrierte Beispiel-Excel-UDF auszuführen. Wenn Sie angepasste Excel-UDFs benötigen, sehen Sie sich [diese Ressourcen](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) an, um die XLLs zu erstellen und auf dem IaaS-Cluster bereitzustellen.

1.	Öffnen Sie eine neue Excel-Arbeitsmappe. Klicken Sie auf dem Menüband **Entwickeln** auf **Add-Ins**. Klicken Sie im Dialogfeld auf **Durchsuchen**, navigieren Sie zum Ordner „%CCP\_HOME%Bin\\XLL32“, und wählen Sie das Beispiel „ClusterUDF32.xll“ aus.

    ![UDF auswählen][udf]

2.	Klicken Sie auf **Datei** > **Optionen** > **Erweitert**. Aktivieren Sie unter **Formeln** das Kontrollkästchen **Ausführung von benutzerdefinierten XLL-Funktionen auf einem Computecluster zulassen**. Klicken Sie dann auf **Optionen**, und geben Sie den vollständigen Clusternamen des Clusterhauptknotens ein. (In diesem Eingabefeld sind wie bereits erwähnt maximal 34 Zeichen zulässig. Sie können einen kürzeren Namen konfigurieren, wenn Sie einen Cluster über das IaaS-Bereitstellungsskript bereitstellen.)

    ![UDF konfigurieren][options]

3.	Klicken Sie auf die Zelle mit dem Wert „=XllGetComputerNameC()“, und drücken Sie die EINGABETASTE, um die UDF-Berechnung auf dem IaaS-Cluster auszuführen. Die Funktion ruft den Namen des Hauptknotens ab, auf dem die UDF ausgeführt wird. Bei der erstmaligen Ausführung erscheint ein Dialogfeld, in dem der Benutzer zur Eingabe von Benutzername und Kennwort für die IaaS-Clusterverbindung aufgefordert wird.

    ![UDF ausführen][run]

    Wenn Berechnungen für eine große Anzahl von Zellen durchzuführen sind, drücken Sie STRG+ALT+UMSCHALT+F9, um die Berechnung für alle Zellen durchzuführen.

## Schritt 3. Ausführen einer SOA-Workload über einen lokalen Client

Wenn Sie auf dem HPC Pack-IaaS-Cluster allgemeine SOA-Anwendungen ausführen möchten, stellen Sie zunächst mit einer der Methoden aus Schritt 1 den IaaS-Cluster bereit, und verwenden Sie dabei ein generisches Serverknotenimage. (Excel wird auf den Serverknoten nicht benötigt.) Führen Sie dann die folgenden Schritte durch:

1. Rufen Sie das Clusterzertifikat ab, und importieren Sie es anschließend auf dem Clientcomputer unter „Cert:\\<Aktueller Benutzer>\\Root“.

2. Installieren Sie das [HPC Pack 2012 R2 Update 2 SDK](http://www.microsoft.com/download/details.aspx?id=47756) und die [HPC Pack 2012 R2 Update 2-Clienthilfsprogramme](https://www.microsoft.com/download/details.aspx?id=47754), um SOA-Clientanwendungen entwickeln und ausführen zu können.

3. Laden Sie den Beispielcode [HelloWorldR2](https://www.microsoft.com/download/details.aspx?id=41633) herunter. Öffnen Sie „HelloWorldR2.sln“ in Visual Studio 2010 oder 2012.

4. Erstellen Sie zuerst das EchoService-Projekt, und stellen Sie den Dienst auf dem IaaS-Cluster bereit. Gehen Sie dabei auf die gleiche Weise vor wie bei der Bereitstellung auf einem lokalen Cluster. Ausführliche Schritte finden Sie in der Infodatei in „HelloWordR2“. Ändern Sie „HellowWorldR2“ und andere Projekte wie weiter unten beschrieben, und stellen Sie sie bereit, um die SOA-Clientanwendungen zu erstellen, die über einen lokalen Clientcomputer auf einem Azure IaaS-Cluster ausgeführt werden.

### Verwenden der HTTP-Bindung mit einer Azure-Speicherwarteschlange

Wenn Sie die HTTP-Bindung mit einer Azure-Speicherwarteschlange verwenden möchten, müssen Sie den Beispielcode etwas verändern.

* Aktualisieren Sie den Clusternamen.

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* Optional: Verwenden Sie für „SessionStartInfo“ das standardmäßige Transportschema, oder legen Sie es explizit auf „Http“ fest.

```
    info.TransportScheme = TransportScheme.Http;
```

* Verwenden Sie für „BrokerClient“ die Standardbindung.

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    Oder legen Sie den Wert mit „basicHttpBinding“ explizit fest.

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* Optional: Legen Sie das Flag „UseAzureQueue“ in „SessionStartInfo“ auf „true“ fest. Ist das Flag nicht festgelegt, wird es standardmäßig auf „true“ festgelegt, wenn der Clustername Azure-Domänensuffixe enthält und das Transportschema „Http“ lautet.

    ```
    info.UseAzureQueue = true;
```

###Verwenden der HTTP-Bindung ohne Azure-Speicherwarteschlange

Legen Sie das Flag „UseAzureQueue“ in „SessionStartInfo“ explizit auf „false“ fest.

```
    info.UseAzureQueue = false;
```

### Verwenden der NetTcp-Bindung

Bei Verwendung der NetTcp-Bindung wird eine ähnliche Konfiguration verwendet wie bei der Verbindungsherstellung mit einem lokalen Cluster. Auf dem virtuellen Computer für den Hauptknoten müssen einige Endpunkte geöffnet werden. Gehen Sie im Azure-Verwaltungsportal wie folgt vor:


1. Beenden Sie den virtuellen Computer.

2. Fügen Sie die TCP-Ports 9090, 9087, 9091, 9094 für die Sitzung, für den Broker, für den Brokerworkerdienst bzw. für den Brokerdatendienst hinzu.

    ![Endpunkte konfigurieren][endpoint]

3. Starten Sie den virtuellen Computer.

Für die SOA-Clientanwendung muss lediglich der Hauptname auf den vollständigen Namen des IaaS-Clusters festgelegt werden.

## Nächste Schritte

* Weitere Informationen zum Ausführen von Excel-Workloads mit HPC Pack finden Sie [hier](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx).

* Weitere Informationen zum Bereitstellen und Verwalten von SOA-Diensten mit HPC Pack finden Sie unter [Verwalten von SOA-Diensten in Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx).

<!--Image references-->
[scenario]: ./media/virtual-machines-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-excel-cluster-hpcpack/udf.png

<!---HONumber=September15_HO1-->