<properties
	pageTitle="SharePoint Server 2013-Farm, Phase 3 | Microsoft Azure"
	description="In Phase 3 der SharePoint Server 2013-Farm erstellen Sie in Azure die Computer und den SQL Server-Cluster, und aktivieren Sie Verfügbarkeitsgruppen."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# SharePoint-Intranetfarm-Workload Phase 3: Konfigurieren der SQL Server-Infrastruktur

[AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Bereitstellungsmodell

In dieser Phase der Intranet-Bereitstellung einer SharePoint 2013-Farm mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in den Azure-Infrastrukturdiensten erstellen und konfigurieren Sie die beiden SQL Server-Computer und den Mehrheitsknotencomputer des Clusters in der Dienstverwaltung und fassen diese in einem Windows-Servercluster zusammen.

Diese Phase muss vor Beginn von [Phase 4](virtual-machines-workload-intranet-sharepoint-phase4.md) ausgeführt worden sein. Eine Übersicht über alle Phasen finden Sie unter [Bereitstellen von SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-workload-intranet-sharepoint-overview.md).

> [AZURE.NOTE]In den vorliegenden Anweisungen wird ein SQL Server-Image aus dem Azure-Imagekatalog verwendet, und es werden Ihnen laufende Kosten für die Nutzung der SQL Server-Lizenz berechnet. Es ist auch möglich, virtuelle Computer in Azure zu erstellen und eigene SQL Server-Lizenzen zu installieren. Allerdings müssen Sie in dem Fall über Software Assurance und Lizenzmobilität verfügen, um die SQL Server-Lizenz auf einem virtuellen Computer einschließlich des virtuellen Computers in Azure verwenden zu können. Weitere Informationen zum Installieren von SQL Server auf einem virtuellen Computer finden Sie unter [Installation für SQL Server](https://msdn.microsoft.com/library/bb500469.aspx).

## Erstellen der virtuellen Computer des SQL Server-Clusters in Azure

Wir verwenden zwei virtuelle SQL Server-Computer. Ein SQL-Server speichert das primäre Datenbankreplikat einer Verfügbarkeitsgruppe. Der zweite SQL-Server speichert das sekundäre Sicherungsreplikat. Die Sicherung wird zur Gewährleistung einer hohen Verfügbarkeit bereitgestellt. Ein weiterer virtueller Computer wird für den Mehrheitsknoten des Clusters benötigt.

Mit dem folgenden PowerShell-Befehlsblock erstellen Sie die virtuellen Computer für diese drei Server. Geben Sie die Werte für die Variablen ein, lassen Sie dabei aber die < and >-Zeichen weg. Die Werte für diesen PowerShell-Befehlsblock entnehmen Sie den folgenden Tabellen:

- Tabelle M für Ihre virtuellen Computer.
- Tabelle V für die Einstellungen Ihres virtuellen Netzwerks.
- Tabelle S für Ihr Subnetz.
- Tabelle A für Ihre Verfügbarkeitsgruppen.
- Tabelle C für Ihre Clouddienste.

Tabelle M haben Sie in [Phase 2: Konfigurieren der Domänencontroller](virtual-machines-workload-intranet-sharepoint-phase2.md) ausgefüllt und die Tabellen V, S, A und C in [Phase 1: Konfigurieren von Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Führen Sie nach der Bereitstellung der richtigen Werte den daraus resultierenden Befehlsblock an der Azure PowerShell-Eingabeaufforderung aus.

	# Create the first SQL server
	$vmName="<Table M – Item 3 - Virtual machine name column>"
	$vmSize="<Table M – Item 3 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 2 – Availability set name column>"

	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Enterprise on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SQL Server computer."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$serviceName="<Table C – Item 2 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second SQL server
	$vmName="<Table M – Item 4 - Virtual machine name column>"
	$vmSize="<Table M – Item 4 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SQL Server computer."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the cluster majority node server
	$vmName="<Table M – Item 5 - Virtual machine name column>"
	$vmSize="<Table M – Item 5 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the cluster majority node server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## Konfigurieren der SQL Server-Computer

Führen Sie für jeden SQL-Server folgende Schritte aus:

1. Melden Sie sich nach den Anweisungen der Prozedur [Anmelden bei einem virtuellen Computer über eine Remotedesktopverbindung](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) mit den Anmeldeinformationen des lokalen Administratorkontos an.

2. Gehen Sie jeweils für beide SQL-Server nach den Anweisungen der Prozedur [Initialisieren eines leeren Datenträgers](virtual-machines-workload-intranet-sharepoint-phase2.md#datadisk) vor, um den zusätzlichen Datenträger hinzufügen.

3. Führen Sie die folgenden Befehle an einer Windows PowerShell-Eingabeaufforderung aus.

		md f:\Data
		md f:\Log
		md f:\Backup

4. Gehen Sie nach den Anweisungen der Prozedur [Testen der Konnektivität](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) vor, um die Konnektivität der Standorte Ihres Unternehmensnetzwerks zu testen. Dadurch stellen Sie sicher, dass die DNS-Namensauflösung ordnungsgemäß funktioniert (d. h., dass der virtuelle Computer korrekt für die DNS-Server des virtuellen Netzwerks konfiguriert ist) und Pakete zum und vom standortübergreifenden virtuellen Netzwerk gesendet werden können.

Gehen Sie jeweils für beide SQL-Server nach den Anweisungen der folgenden Prozedur vor, um die SQL-Server so zu konfigurieren, dass sie Laufwerk F: für neue Datenbanken und für Konten und Berechtigungen verwenden.


1.	Geben Sie auf dem Startbildschirm **SQL Studio** ein, und klicken Sie dann auf **SQL Server 2014 Management Studio**.
2.	Klicken Sie unter **Mit Server verbinden** auf **Verbinden**.
3.	Klicken Sie im linken Bereich mit der rechten Maustaste auf den obersten Knoten – die nach dem Computer benannte Standardinstanz –, und klicken Sie dann auf **Eigenschaften**.
4.	Klicken Sie unter **Servereigenschaften** auf **Datenbankeinstellungen**.
5.	Legen Sie unter **Standardspeicherorte für Datenbank** die folgenden Werte fest:
- Geben Sie als Pfad für **Daten** **f:\\Data** ein.
- Geben Sie als Pfad für **Protokoll** **f:\\Log** ein.
- Geben Sie als Pfad für **Sicherung** **f:\\Backup** ein.
- Nur neue Datenbanken verwenden diese Speicherorte.
6.	Klicken Sie auf **OK**, um das Fenster zu schließen.
7.	Erweitern Sie im linken Bereich den **Ordner Sicherheit**.
8.	Klicken Sie mit der rechten Maustaste auf **Anmeldungen**, und klicken Sie dann auf **Neue Anmeldung**.
9.	Geben Sie unter **Anmeldename** die Zeichenfolge „*Domäne*\\sp\_farm\_db“ ein (wobei *Domäne* der Name der Domäne ist, in der das Konto „sp\_farm\_db“ erstellt wurde).
10.	Klicken Sie unter **Seite auswählen** auf **Serverrollen**, klicken Sie auf **sysadmin**, und klicken Sie dann auf **OK**.
11.	Schließen Sie SQL Server 2014 Management Studio.

Gehen Sie jeweils für beide SQL-Server nach den Anweisungen der folgenden Prozedur vor, damit Remotedesktopverbindungen das Konto „sp\_farm\_db“ verwenden können.

1.	Klicken Sie auf dem Startbildschirm mit der rechten Maustaste auf **Dieser PC**, und klicken Sie dann auf **Eigenschaften**.
2.	Klicken Sie im Fenster **System** auf **Remoteeinstellungen**.
3.	Klicken Sie im Abschnitt **Remotedesktop** auf **Benutzer auswählen**, und klicken Sie dann auf **Hinzufügen**.
4.	Geben Sie unter **Geben Sie die Namen der auszuwählenden Objekte ein** die Zeichenfolge "[Domäne]**\\sp\_farm\_db**" ein, und klicken Sie dann dreimal auf **OK**.

Für SQL Server ist ein Port erforderlich, über den die Clients auf den Datenbankserver zugreifen. Zwei weitere Ports sind erforderlich für die Verbindung mit SQL Server Management Studio und die Verwaltung der Hochverfügbarkeitsgruppe. Führen Sie als Nächstes jeweils für beide SQL-Server den folgenden Befehl an einer Windows PowerShell-Eingabeaufforderung auf Administratorebene aus, um eine Firewallregel hinzuzufügen, die eingehenden Datenverkehr zum SQL-Server erlaubt.

	New-NetFirewallRule -DisplayName "SQL Server ports 1433, 1434, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

Melden Sie sich bei beiden virtuellen SQL Server-Computern als lokaler Administrator ab.

Informationen zum Optimieren der SQL Server-Leistung in Azure finden Sie unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-sql-server-performance-best-practices.md). Zur Optimierung der IOPs können Sie auch den georedundanten Speicher (GRS) für das Speicherkonto der SharePoint-Farm deaktivieren und stattdessen Speicherplatz verwenden.

## Konfigurieren des Mehrheitsknotenservers des Clusters

Gehen Sie nach den Anweisungen der Prozedur [Anmelden bei einem virtuellen Computer über eine Remotedesktopverbindung](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) vor, um den Mehrheitsknoten des Clusters mit den Anmeldeinformationen eines Domänenkontos anzumelden.

Gehen Sie auf dem Mehrheitsknoten des Clusters nach den Anweisungen der Prozedur [Testen der Konnektivität](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) vor, um die Konnektivität zu den Standorten in Ihrem Unternehmensnetzwerk zu testen.

## Erstellen des Windows-Serverclusters

SQL Server AlwaysOn-Verfügbarkeitsgruppen basieren auf dem Windows Server-Feature Failoverclustering (WSFC). Dieses Feature ermöglicht die Gruppierung mehrerer Computer zu einem Cluster. Bei Ausfall eines Computers kann ein zweiter Computer seine Funktion übernehmen. Daher ist die erste und vorrangige Aufgabe die Aktivierung des Failoverclustering auf allen beteiligten Computern. Diese sind:

- Der primäre SQL-Server
- Der sekundäre SQL-Server
- Der Mehrheitsknoten des Clusters

Für einen Failovercluster sind mindestens drei virtuelle Computer erforderlich. Auf zwei Computern wird SQL Server bereitgestellt. Der zweite virtuelle Computer mit SQL Server ist ein synchrones sekundäres Replikat, das bei einem Ausfall des primären Computers sicherstellt, dass keine Daten verloren gehen. Der dritte Computer muss kein SQL Server bereitstellen. Der Mehrheitsknoten des Clusters bietet ein Quorum im WSFC. Da der WSFC-Cluster zur Überwachung seines Zustands ein Quorum benötigt, muss immer eine Mehrheit vorhanden sein, die sicherstellt, dass der WSFC-Cluster online ist. Wenn ein Cluster nur aus zwei Computern besteht und einer von beiden ausfällt, gibt es keine Mehrheit mehr. Weitere Informationen finden Sie unter [WSFC-Quorummodi und Abstimmungskonfiguration (SQL Server)](http://msdn.microsoft.com/library/hh270280.aspx).

Führen Sie für beide SQL Server-Computer und für den Mehrheitsknoten des Clusters an einer Windows PowerShell-Eingabeaufforderung auf Administratorebene den folgenden Befehl aus.

	Install-WindowsFeature Failover-Clustering -IncludeManagementTools

Aufgrund des noch nicht RFC-konformen Verhaltens von DHCP in Azure kann die Erstellung eines Clusters mit Windows Server-Failoverclustering (WSFC) fehlschlagen. Nähere Informationen hierzu finden Sie im Artikel „Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern“ unter „WSFC-Clusterverhalten in Azure-Netzwerken“. Dieses Problem kann jedoch umgangen werden. Führen Sie zum Erstellen des Clusters die folgenden Schritte aus:

1.	Melden Sie sich beim primären virtuellen SQL Server-Computer unter dem Konto **sp\_install** an.
2.	Geben Sie auf dem Startbildschirm **Failover** ein, und klicken Sie dann auf **Failovercluster-Manager**.
3.	Klicken Sie im linken Bereich mit der rechten Maustaste auf **Failovercluster-Manager**, und klicken Sie dann auf **Cluster erstellen**.
4.	Klicken Sie auf der Seite „Voraussetzungen“ auf **Weiter**.
5.	Geben Sie auf der Seite „Server auswählen“ den Namen des primären SQL Server-Computers ein, klicken Sie auf **Hinzufügen**, und klicken Sie dann auf **Weiter**.
6.	Klicken Sie auf der Seite „Validierungswarnung“ auf **Nein. Microsoft-Support für diesen Cluster nicht nötig. Validierungstests nicht durchführen. Beim Klicken auf "Weiter" Erstellung des Clusters fortsetzen.** Klicken Sie danach auf **Weiter**.
7.	Geben Sie auf der Seite "Zugriffspunkt für die Verwaltung des Clusters" im Textfeld **Clustername** den Namen Ihres Clusters ein, und klicken Sie dann auf **Weiter**.
8.	Klicken Sie auf der Bestätigungsseite auf **Weiter**, um den Cluster zu erstellen.
9.	Klicken Sie auf der Zusammenfassungsseite auf **Fertig stellen**.
10.	Klicken Sie im linken Bereich auf den neuen Cluster. Öffnen Sie im Inhaltsbereich im Abschnitt **Hauptressourcen des Clusters** den Namen Ihres Serverclusters. Zur Ressource **IP-Adresse** wird der Status **Fehlgeschlagen** angezeigt. Die IP-Adressressource kann nicht online geschaltet werden, da dem Cluster die gleiche IP-Adresse wie dem Computer selbst zugewiesen ist. Damit ist die IP-Adresse nicht mehr eindeutig.
11.	Klicken Sie mit der rechten Maustaste auf die fehlgeschlagene Ressource **IP-Adresse**, und klicken Sie dann auf **Eigenschaften**.
12.	Klicken Sie im Dialogfeld **Eigenschaften von IP-Adressen** auf **Statische IP-Adresse**.
13.	Geben Sie im Adressraum für das Subnetz, in dem sich der SQL-Server befindet, eine nicht verwendete IP-Adresse ein, und klicken Sie dann auf **OK**.
14.	Klicken Sie mit der rechten Maustaste auf die fehlgeschlagene IP-Adressressource, und klicken Sie dann auf **Online schalten**. Warten Sie, bis beide Ressourcen online sind. Beim Online-Schalten der Clusternamensressource wird für den Domänencontroller das Computerkonto in Active Directory (AD) aktualisiert. Dieses AD-Konto wird später zum Ausführen des Clusterdiensts der Verfügbarkeitsgruppe verwendet.
15.	Nachdem das AD-Konto erstellt wurde, können Sie den Clusternamen wieder offline schalten. Klicken Sie mit der rechten Maustaste im Bereich **Hauptressourcen des Clusters** auf den Clusternamen, und klicken Sie dann auf **Offline schalten**.
16.	Klicken Sie zum Entfernen der IP-Adresse des Clusters mit der rechten Maustaste auf **IP-Adresse**, klicken Sie dann auf **Entfernen** und auf Aufforderung auf **Ja**. Die Clusterressource kann nun nicht mehr online geschaltet werden, da sie von der IP-Adressressource abhängig ist. Die einwandfreie Funktion einer Verfügbarkeitsgruppe ist jedoch nicht vom Clusternamen oder der IP-Adresse abhängig. Daher kann der Clustername offline bleiben.
17.	Um die verbleibenden Knoten zum Cluster hinzuzufügen, klicken Sie im linken Bereich mit der rechten Maustaste auf den Clusternamen, und klicken Sie dann auf **Knoten hinzufügen**.
18.	Klicken Sie auf der Seite „Voraussetzungen“ auf **Weiter**.
19.	Geben Sie auf der Seite „Server auswählen“ den Namen ein, und klicken Sie dann auf **Hinzufügen**, um sowohl den sekundären SQL-Server als auch den Mehrheitsknoten des Clusters zum Cluster hinzuzufügen. Klicken Sie nach dem Hinzufügen beider Computer auf **Weiter**. Wenn ein Computer nicht hinzugefügt werden kann und die Fehlermeldung darauf hinweist, dass die Remoteregistrierung nicht ausgeführt wird, führen Sie folgende Schritte aus. Melden Sie sich bei dem Computer an, öffnen Sie das Snap-in „Dienste“ (services.msc), und aktivieren Sie die Remoteregistrierung. Weitere Informationen finden Sie unter [Mit dem Remoteregistrierungsdienst kann keine Verbindung hergestellt werden](http://technet.microsoft.com/library/bb266998.aspx).
20.	Klicken Sie auf der Seite „Validierungswarnung“ auf **Nein. Microsoft-Support für diesen Cluster nicht nötig. Validierungstests nicht durchführen. Beim Klicken auf "Weiter" Erstellung des Clusters fortsetzen.** Klicken Sie danach auf **Weiter**.
21.	Klicken Sie auf der Bestätigungsseite auf **Weiter**.
22.	Klicken Sie auf der Zusammenfassungsseite auf **Fertig stellen**.
23.	Klicken Sie im linken Bereich auf **Knoten**. Alle drei Computer sollten nun aufgelistet werden.

## Aktivieren von AlwaysOn-Verfügbarkeitsgruppen

Als Nächstes aktivieren Sie AlwaysOn-Verfügbarkeitsgruppen mit dem SQL Server-Konfigurations-Manager. Beachten Sie, dass sich eine Verfügbarkeitsgruppe in SQL Server von einer Verfügbarkeitsgruppe in Azure unterscheidet. Eine SQL Server-Verfügbarkeitsgruppe enthält hochgradig verfügbare und wiederherstellbare Datenbanken. Eine Azure-Verfügbarkeitsgruppe ordnet virtuelle Computer verschiedenen Fehlerdomänen zu. Weitere Informationen zu Fehlerdomänen finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer](virtual-machines-manage-availability.md).

Führen Sie zur Aktivierung von Verfügbarkeitsgruppen in SQL Server folgende Schritte aus.

1.	Melden Sie sich unter dem Konto **sp\_farm\_db** oder einem anderen Konto, das auf dem SQL-Server über die Serverrolle „sysadmin“ verfügt, beim primären SQL-Server an.
2.	Geben Sie auf dem Startbildschirm **SQL Server-Konfiguration** ein, und klicken Sie dann auf **SQL Server-Konfigurations-Manager**.
3.	Klicken Sie im linken Bereich auf **SQL Server-Dienste**.
4.	Doppelklicken Sie im Inhaltsbereich auf **SQL Server (MSSQLSERVER)**.
5.	Klicken Sie im Fenster **Eigenschaften von SQL Server (MSSQLSERVER)** auf die Registerkarte **Hohe Verfügbarkeit mit AlwaysOn**, wählen Sie **AlwaysOn-Verfügbarkeitsgruppen aktivieren** aus, klicken Sie auf **Übernehmen**, und klicken Sie dann, wenn Sie dazu aufgefordert werden, auf **OK**. Lassen Sie das Eigenschaftenfenster noch offen.
6.	Klicken Sie auf die Registerkarte "virtual-machines-manage-availability", und geben Sie unter **Kontoname** [Domäne]**\\sqlservice** ein. Geben Sie unter **Kennwort** das Kennwort für das Konto "sqlservice" ein, **bestätigen Sie das Kennwort**, und klicken Sie dann auf **OK**.
7.	Klicken Sie im Meldungsfenster auf **Ja**, um den SQL Server-Dienst neu zu starten.
8.	Melden Sie sich beim sekundären SQL-Server an, und wiederholen Sie diesen Vorgang.

Im folgenden Diagramm ist die aus dem erfolgreichen Abschluss dieser Phase resultierende Konfiguration mit Platzhaltern anstelle der Computernamen dargestellt.

![](./media/virtual-machines-workload-intranet-sharepoint-phase3/workload-spsqlao_03.png)

## Nächster Schritt

Zum Fortsetzen der Konfiguration dieses Workloads gehen Sie zu [Phase 4: Konfigurieren der SharePoint-Server](virtual-machines-workload-intranet-sharepoint-phase4.md).

## Zusätzliche Ressourcen

[Bereitstellen von SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen](virtual-machines-sharepoint-infrastructure-services.md)

[Infografik zu SharePoint mit SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure-Infrastrukturdienste-Workload: Branchenanwendung mit hoher Verfügbarkeit](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Oct15_HO4-->