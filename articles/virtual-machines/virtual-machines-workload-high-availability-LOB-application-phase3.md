<properties 
	pageTitle="Branchenanwendung, Phase 3 | Microsoft Azure"
	description="In Phase 3 der Branchenanwendung erstellen Sie in Azure die Computer und den SQL Server-Cluster, und aktivieren Sie Verfügbarkeitsgruppen."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="josephd"/>

# Branchenanwendungs-Workload, Phase 3: Konfigurieren der SQL Server-Infrastruktur

In dieser Phase der Bereitstellung einer hochverfügbaren Branchenanwendung in den Azure-Infrastrukturdiensten konfigurieren Sie die beiden SQL Server-Computer und den Hauptknotencomputer des Clusters und fassen diese in einem Windows-Servercluster zusammen.

Diese Phase muss vor Beginn von [Phase 4](virtual-machines-workload-high-availability-LOB-application-phase4.md) ausgeführt worden sein. Eine Übersicht über alle Phasen finden Sie unter [Bereitstellen einer hochverfügbaren Branchenanwendung in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md).

> [AZURE.NOTE]In den vorliegenden Anweisungen wird ein SQL Server-Image aus dem Azure-Imagekatalog verwendet, und es fallen laufende Kosten für die Nutzung der SQL Server-Lizenz an. Es ist ebenfalls möglich, virtuelle Computer in Azure zu erstellen und eigene SQL Server-Lizenzen zu installieren, entsprechende Anweisungen sind hier jedoch nicht enthalten.

## Erstellen der virtuellen Computer des SQL Server-Clusters in Azure

Es werden zwei virtuelle Computer mit SQL Server verwendet. Ein virtueller Computer enthält das primäre Datenbankreplikat einer Verfügbarkeitsgruppe, der zweite virtuelle Computer enthält das sekundäre (Sicherungs-) Replikat. Mithilfe der Sicherung wird eine hohe Verfügbarkeit gewährleistet. Ein weiterer virtueller Computer wird für den Hauptknoten des Clusters benötigt.

Mit dem folgenden PowerShell-Befehlsblock erstellen Sie die virtuellen Computer für diese drei Server. Geben Sie die Werte für die Variablen ein, lassen Sie dabei aber die < and >-Zeichen weg. Die Werte für diesen PowerShell-Befehlsblock entnehmen Sie den folgenden Tabellen:

- Tabelle M für Ihre virtuellen Computer
- Tabelle V für die Einstellungen Ihres virtuellen Netzwerks
- Tabelle S für Ihr Subnetz
- Tabelle ST für Ihre Speicherkonten
- Tabelle A für Ihre Verfügbarkeitsgruppen

Die Tabelle M haben Sie in [Phase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md), die Tabellen V, S, ST und A haben Sie in [Phase 1](virtual-machines-workload-high-availability-LOB-application-phase1.md) ausgefüllt.

Führen Sie nach der Bereitstellung der richtigen Werte den daraus resultierenden Befehlsblock an der Azure PowerShell-Eingabeaufforderung aus.

	# Set up subscription and key variables
	$subscr="<name of the Azure subscription>"
	Set-AzureSubscription -SubscriptionName $subscr
	Switch-AzureMode AzureResourceManager
	$rgName="<your resource group name>"
	$locName="<Azure location of your resource group>"
	# Change to the premium storage account
	$saName="<Table ST – Item 1 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 2 – Availability set name column>"
	
	# Create the first SQL server
	$vmName="<Table M – Item 3 - Virtual machine name column>"
	$vmSize="<Table M – Item 3 - Minimum size column>"
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for SQL data in GB>
	$diskLabel="<the label on the disk>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-SQLDataDisk.vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first SQL Server computer." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second SQL Server virtual machine
	$vmName="<Table M – Item 4 - Virtual machine name column>"
	$vmSize="<Table M – Item 4 - Minimum size column>"
	$nic=New-AzureNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for SQL data in GB>
	$diskLabel="<the label on the disk>"
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second SQL Server computer." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Change to the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"
	
	# Create the cluster majority node server
	$vmName="<Table M – Item 5 - Virtual machine name column>"
	$vmSize="<Table M – Item 5 - Minimum size column>"
	$nic=New-AzureNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the cluster majority node server." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Konfigurieren der SQL Server-Computer

Erstellen Sie für jeden virtuellen Computer mit SQL Server unter Verwendung eines Remotedesktopclients Ihrer Wahl eine Remotedesktopverbindung mit dem virtuellen Computer, der als erster Domänencontroller fungiert. Verwenden Sie den zugehörigen Intranet-DNS- oder Computernamen und die Anmeldeinformationen des lokalen Administratorkontos.

Treten Sie für jeden virtuellen Computer mit SQL Server der geeigneten AD DS-Domäne bei, indem Sie an der Windows PowerShell-Eingabeaufforderung diese Befehle ausführen.

	$domName="<AD DS domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Beachten Sie, dass Sie Anmeldeinformationen für ein Domänenkonto angeben müssen, nachdem Sie den Add-Computer-Befehl eingegeben haben.

Stellen Sie nach dem Neustart unter Verwendung eines Kontos mit lokalen Administratorberechtigungen eine neue Verbindung her.


Gehen Sie jeweils für beide virtuellen SQL Server-Computer nach den Anweisungen der folgenden Prozedur vor, um den zusätzlichen Datenträger hinzuzufügen und Ordner auf dem neuen Volume zu erstellen.

### So initialisieren Sie einen leeren Datenträger und fügen Ordner hinzu

1. Klicken Sie im linken Bereich des Server-Managers auf **Datei- und Speicherdienste** und anschließend auf **Datenträger**.
2. Klicken Sie im Inhaltsbereich in der Gruppe **Datenträger** auf **Datenträger 2** (wobei **Partition** die Einstellung **Unbekannt** aufweisen muss).
3. Klicken Sie auf **Aufgaben** und anschließend auf **Neues Volume**.
4. Klicken Sie auf der Seite „Voraussetzungen“ des Assistenten für neue Volumes auf **Weiter**.
5. Klicken Sie auf der Seite „Server und Datenträger auswählen“ auf **Datenträger 2** und anschließend auf **Weiter**. Wenn Sie dazu aufgefordert werden, klicken Sie auf **OK**.
6. Klicken Sie auf der Seite „Geben Sie die Größe des Volumes an“ auf **Weiter**.
7. Klicken Sie auf der Seite „Einem Laufwerkbuchstaben oder Ordner zuweisen“ auf **Weiter**.
8. Klicken Sie auf der Seite „Dateisystemeinstellungen auswählen“ auf **Weiter**.
9. Klicken Sie auf der Seite „Auswahl bestätigen“ auf **Erstellen**.
10. Wenn Sie fertig sind, klicken Sie auf **Schließen**.
11. Führen Sie die folgenden Befehle an einer Windows PowerShell-Eingabeaufforderung aus:
	- md f:\\Data
	- md f:\\Log
	- md f:\\Backup

Gehen Sie jeweils für beide virtuellen SQL Server-Computer nach den Anweisungen der folgenden Prozedur vor, um diese so zu konfigurieren, dass sie Laufwerk F: für neue Datenbanken und für Konten und Berechtigungen verwenden.

1. Geben Sie auf dem Startbildschirm **SQL Studio** ein, und klicken Sie dann auf **SQL Server 2014 Management Studio**.
2. Klicken Sie unter **Mit Server verbinden** auf **Verbinden**.
3. Klicken Sie im linken Bereich mit der rechten Maustaste auf den obersten Knoten – die nach dem Computer benannte Standardinstanz –, und klicken Sie dann auf **Eigenschaften**.
4.	Klicken Sie unter **Servereigenschaften** auf **Datenbankeinstellungen**.
5.	Legen Sie unter **Standardspeicherorte für Datenbank** die folgenden Werte fest: 
	- Geben Sie als Pfad für **Daten** **f:\\Data** ein.
	- Geben Sie als Pfad für **Protokoll** **f:\\Log** ein.
	- Geben Sie als Pfad für **Sicherung** **f:\\Backup** ein.
	- Nur neue Datenbanken verwenden diese Speicherorte.
6.	Klicken Sie auf **OK**, um das Fenster zu schließen.
7.	Erweitern Sie im linken Bereich den **Ordner Sicherheit**.
8.	Klicken Sie mit der rechten Maustaste auf **Anmeldungen**, und klicken Sie dann auf **Neue Anmeldung**.
9.	Geben Sie unter **Anmeldename** die Zeichenfolge "*Domäne*\\sqladmin" ein (wobei *Domäne* der Name der Domäne ist, in der in [Phase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md) das Konto "\\sqladmin" erstellt wurde). 
10.	Klicken Sie unter **Seite auswählen** auf **Serverrollen**, klicken Sie auf **sysadmin**, und klicken Sie dann auf **OK**.
11.	Schließen Sie SQL Server 2014 Management Studio.

Gehen Sie jeweils für beide virtuellen SQL Server-Computer nach den Anweisungen der folgenden Prozedur vor, damit Remotedesktopverbindungen das Konto "sqladmin" verwenden können.

1.	Klicken Sie auf dem Startbildschirm mit der rechten Maustaste auf **Dieser PC**, und klicken Sie dann auf **Eigenschaften**.
2.	Klicken Sie im Fenster **System** auf **Remoteeinstellungen**.
3.	Klicken Sie im Abschnitt **Remotedesktop** auf **Benutzer auswählen**, und klicken Sie dann auf **Hinzufügen**.
4.	Geben Sie unter **Geben Sie die Namen der auszuwählenden Objekte ein** die Zeichenfolge "[Domäne]**\\sqladmin**" ein, und klicken Sie dann dreimal auf **OK**.

Für den SQL Server-Dienst ist ein Port erforderlich, über den die Clients auf den Datenbankserver zugreifen. Zwei weitere Ports sind erforderlich für die Verbindung mit SQL Server Management Studio und die Verwaltung der Hochverfügbarkeitsgruppe. Führen Sie als Nächstes jeweils für beide virtuellen SQL Server.-Computer den folgenden Befehl an einer Windows PowerShell-Eingabeaufforderung auf Administratorebene aus, um eine Firewallregel hinzuzufügen, die diese Art von eingehendem Datenverkehr zulässt.

	New-NetFirewallRule -DisplayName "SQL Server ports 1433, 4234, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

Melden Sie sich bei beiden virtuellen SQL Server-Computern als lokaler Administrator ab.

Informationen zum Optimieren der SQL Server-Leistung in Azure finden Sie unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-sql-server-performance-best-practices.md). Zur Optimierung der IOPs können Sie auch den georedundanten Speicher (GRS) für das Speicherkonto der Branchenanwendung deaktivieren und stattdessen Speicherplatz verwenden.

## Konfigurieren des Hauptknotenservers des Clusters

Verwenden Sie einen Remotedesktopclient Ihrer Wahl, und erstellen Sie eine Remotedesktopverbindung mit dem virtuellen Computer, der als Hauptknotenserver für den Cluster fungiert. Verwenden Sie den zugehörigen Intranet-DNS- oder Computernamen und die Anmeldeinformationen des lokalen Administratorkontos.

Fügen Sie den Hauptknotenserver des Clusters der geeigneten AD DS-Domäne hinzu, indem Sie an der Windows PowerShell-Eingabeaufforderung diese Befehle ausführen.

	$domName="<AD DS domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Beachten Sie, dass Sie Anmeldeinformationen für ein Domänenkonto angeben müssen, wenn Sie den **Add-Computer**-Befehl ausführen.

Stellen Sie nach dem Neustart unter Verwendung eines Kontos mit lokalen Administratorberechtigungen eine neue Verbindung her.

## Erstellen des WSFC-Clusters

SQL Server AlwaysOn-Verfügbarkeitsgruppen basieren auf dem Windows Server-Feature Failoverclustering (WSFC). Dieses Feature ermöglicht die Gruppierung mehrerer Computer zu einem Cluster. Bei Ausfall eines Computers kann ein zweiter Computer seine Funktion übernehmen. Daher ist die erste und vorrangige Aufgabe die Aktivierung des Failoverclustering auf allen beteiligten Computern. Diese sind:

- Der primäre virtuelle Computer mit SQL Server
- Der sekundäre virtuelle Computer mit SQL Server
- Der Hauptknoten des Clusters

Für einen Failovercluster sind mindestens drei virtuelle Computer erforderlich. Zwei Computer hosten SQL Server, wobei der sekundäre virtuelle Computer ein synchrones sekundäres Replikat ist, das bei einem Ausfall des primären Computers sicherstellt, dass keine Daten verloren gehen. Der dritte Computer muss kein SQL Server bereitstellen. Der Hauptknoten des Clusters fungiert innerhalb des WSFC als Quorumzeuge. Da der WSFC-Cluster zur Überwachung seines Zustands ein Quorum benötigt, muss immer eine Mehrheit vorhanden sein, die sicherstellt, dass der WSFC-Cluster online ist. Wenn ein Cluster nur aus zwei Computern besteht und einer von beiden ausfällt, gibt es keine Mehrheit mehr. Weitere Informationen finden Sie unter [WSFC-Quorummodi und Abstimmungskonfiguration (SQL Server)](http://msdn.microsoft.com/library/hh270280.aspx).

Führen Sie für beide virtuellen SQL Server-Computer und für den Hauptknoten des Clusters an einer Windows PowerShell-Eingabeaufforderung auf Administratorebene den folgenden Befehl aus.

	Install-WindowsFeature Failover-Clustering -IncludeManagementTools

Aufgrund des noch nicht RFC-konformen Verhaltens von DHCP in Azure kann die Erstellung eines Clusters mit Windows Server-Failoverclustering (WSFC) fehlschlagen. Nähere Informationen hierzu finden Sie im Artikel „Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern“ unter „WSFC-Clusterverhalten in Azure-Netzwerken“. Dieses Problem kann jedoch umgangen werden. Führen Sie zum Erstellen des Clusters die folgenden Schritte aus:

1.	Melden Sie sich beim primären virtuellen SQL Server-Computer mit dem Konto "sqladmin" an, das Sie in [Phase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md) erstellt haben.
2.	Geben Sie auf dem Startbildschirm **Failover** ein, und klicken Sie dann auf **Failovercluster-Manager**.
3.	Klicken Sie im linken Bereich mit der rechten Maustaste auf **Failovercluster-Manager**, und klicken Sie dann auf **Cluster erstellen**.
4.	Klicken Sie auf der Seite **Vorbereitungen** auf **Weiter**.
5.	Geben Sie auf der Seite **Server auswählen** den Namen des primären SQL Server-Computers ein, klicken Sie auf **Hinzufügen**, und klicken Sie dann auf **Weiter**.
6.	Klicken Sie auf der Seite **Validierungswarnung** auf **Nein. Microsoft-Support für diesen Cluster nicht nötig. Validierungstests nicht durchführen. Beim Klicken auf „Weiter“ Erstellung des Clusters fortsetzen.** Klicken Sie danach auf **Weiter**.
7.	Geben Sie auf der Seite **Zugriffspunkt für die Verwaltung des Clusters** im Textfeld **Clustername** den Namen Ihres Clusters ein, und klicken Sie dann auf **Weiter**.
8.	Klicken Sie auf der Bestätigungsseite auf **Weiter**, um den Cluster zu erstellen. 
9.	Klicken Sie auf der Seite **Zusammenfassung** auf **Fertig stellen**.
10.	Klicken Sie im linken Bereich auf den neuen Cluster. Öffnen Sie im Inhaltsbereich im Abschnitt **Hauptressourcen des Clusters** den Namen Ihres Serverclusters. Zur Ressource **IP-Adresse** wird der Status **Fehlgeschlagen** angezeigt. Die IP-Adressressource kann nicht online geschaltet werden, da dem Cluster die gleiche IP-Adresse wie dem Computer selbst zugewiesen ist. Damit ist die IP-Adresse nicht mehr eindeutig. 
11.	Klicken Sie mit der rechten Maustaste auf die fehlgeschlagene Ressource **IP-Adresse**, und klicken Sie dann auf **Eigenschaften**.
12.	Klicken Sie im Dialogfeld **Eigenschaften von IP-Adressen** auf **Statische IP-Adresse**.
13.	Geben Sie im Adressraum für das Subnetz, in dem sich der SQL-Server befindet, eine nicht verwendete IP-Adresse ein, und klicken Sie dann auf **OK**.
14.	Klicken Sie mit der rechten Maustaste auf die fehlgeschlagene IP-Adressressource, und klicken Sie dann auf **Online schalten**. Warten Sie, bis beide Ressourcen online sind. Beim Online-Schalten der Clusternamensressource wird für den Domänencontroller das Computerkonto in Active Directory (AD) aktualisiert. Dieses AD-Konto wird später zum Ausführen des Clusterdiensts der Verfügbarkeitsgruppe verwendet.
15.	Nachdem das AD-Konto erstellt wurde, können Sie den Clusternamen wieder offline schalten. Klicken Sie mit der rechten Maustaste im Bereich **Hauptressourcen des Clusters** auf den Clusternamen, und klicken Sie dann auf **Offline schalten**.
16.	Klicken Sie zum Entfernen der IP-Adresse des Clusters mit der rechten Maustaste auf **IP-Adresse**, klicken Sie dann auf **Entfernen** und auf Aufforderung auf **Ja**. Die Clusterressource kann nun nicht mehr online geschaltet werden, da sie von der IP-Adressressource abhängig ist. Die einwandfreie Funktion einer Verfügbarkeitsgruppe ist jedoch nicht vom Clusternamen oder der IP-Adresse abhängig. Daher kann der Clustername offline bleiben.
17.	Um die verbleibenden Knoten zum Cluster hinzuzufügen, klicken Sie im linken Bereich mit der rechten Maustaste auf den Clusternamen, und klicken Sie dann auf **Knoten hinzufügen**.
18.	Klicken Sie auf der Seite **Vorbereitungen** auf **Weiter**. 
19.	Geben Sie auf der Seite **Server auswählen** den Namen ein, und klicken Sie dann auf **Hinzufügen**, um sowohl den sekundären SQL Server-Computer als auch den Hauptknoten des Clusters zum Cluster hinzuzufügen. Klicken Sie nach dem Hinzufügen beider Computer auf **Weiter**. Wenn ein Computer nicht hinzugefügt werden kann und die Fehlermeldung darauf hinweist, dass die Remoteregistrierung nicht ausgeführt wird, führen Sie folgende Schritte aus. Melden Sie sich bei dem Computer an, öffnen Sie das Snap-in „Dienste“ (services.msc), und aktivieren Sie die Remoteregistrierung. Weitere Informationen finden Sie unter [Mit dem Remoteregistrierungsdienst kann keine Verbindung hergestellt werden](http://technet.microsoft.com/library/bb266998.aspx). 
20.	Klicken Sie auf der Seite **Validierungswarnung** auf **Nein. Microsoft-Support für diesen Cluster nicht nötig. Validierungstests nicht durchführen. Beim Klicken auf „Weiter“ Erstellung des Clusters fortsetzen.** Klicken Sie danach auf **Weiter**. 
21.	Klicken Sie auf der Bestätigungsseite auf **Weiter**.
22.	Klicken Sie auf der Seite **Zusammenfassung** auf **Fertig stellen**.
23.	Klicken Sie im linken Bereich auf **Knoten**. Alle drei Computer sollten nun aufgelistet werden.

## Aktivieren von AlwaysOn-Verfügbarkeitsgruppen

Als Nächstes aktivieren Sie AlwaysOn-Verfügbarkeitsgruppen mit dem SQL Server-Konfigurations-Manager. Beachten Sie, dass sich eine Verfügbarkeitsgruppe in SQL Server von einer Verfügbarkeitsgruppe in Azure unterscheidet. Eine SQL Server-Verfügbarkeitsgruppe enthält hochgradig verfügbare und wiederherstellbare Datenbanken. Eine Azure-Verfügbarkeitsgruppe ordnet virtuelle Computer verschiedenen Fehlerdomänen zu. Weitere Informationen zu Fehlerdomänen finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer](virtual-machines-manage-availability.md).

Führen Sie zur Aktivierung von Verfügbarkeitsgruppen in SQL Server folgende Schritte aus.

1.	Melden Sie sich beim primären virtuellen SQL Server-Computer mit dem Konto "sqladmin" an, das Sie in [Phase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md) erstellt haben.
2.	Geben Sie auf dem Startbildschirm **SQL Server-Konfiguration** ein, und klicken Sie dann auf **SQL Server-Konfigurations-Manager**.
3.	Klicken Sie im linken Bereich auf **SQL Server-Dienste**.
4.	Doppelklicken Sie im Inhaltsbereich auf **SQL Server (MSSQLSERVER)**.
5.	Klicken Sie im Fenster **Eigenschaften von SQL Server (MSSQLSERVER)** auf die Registerkarte **Hohe Verfügbarkeit mit AlwaysOn**, wählen Sie **AlwaysOn-Verfügbarkeitsgruppen aktivieren** aus, klicken Sie auf **Übernehmen**, und klicken Sie auf Aufforderung auf **OK**. Lassen Sie das Eigenschaftenfenster noch offen. 
6.	Klicken Sie auf die Registerkarte "virtual-machines-manage-availability", und geben Sie unter **Kontoname** [Domäne]**\\sqlservice** ein. Geben Sie unter **Kennwort** das Kennwort für das Konto "sqlservice" ein, **bestätigen Sie das Kennwort**, und klicken Sie dann auf **OK**.
7.	Klicken Sie im Meldungsfenster auf **Ja**, um den SQL Server-Dienst neu zu starten.
8.	Melden Sie sich beim sekundären virtuellen SQL Server-Computer mit dem Konto "sqladmin" an, und wiederholen Sie die Schritte 2 bis 7. 

Im folgenden Diagramm ist die aus dem erfolgreichen Abschluss dieser Phase resultierende Konfiguration mit Platzhaltern anstelle der Computernamen dargestellt.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase3/workload-lobapp-phase3.png)

## Nächster Schritt

Zum Fortsetzen der Konfiguration dieser Workload wechseln Sie zu [Phase 4: Konfigurieren der Webserver](virtual-machines-workload-high-availability-LOB-application-phase4.md).

## Zusätzliche Ressourcen

[Bereitstellen einer hochverfügbaren Branchenanwendung in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Architekturblaupause für Branchenanwendungen](http://msdn.microsoft.com/dn630664)

[Einrichten einer webbasierten Branchenanwendung in einer Hybrid Cloud zu Testzwecken](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure-Infrastrukturdienste-Workload: SharePoint Server 2013-Farm](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=August15_HO9-->