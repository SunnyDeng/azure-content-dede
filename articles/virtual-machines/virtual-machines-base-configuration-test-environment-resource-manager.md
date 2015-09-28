<properties
	pageTitle="Testumgebung für die Basiskonfiguration mit Azure-Ressourcen-Manager"
	description="Sie erfahren, wie Sie mit dem Ressourcen-Manager eine einfache Entwicklungs-/Testumgebung erstellen können, mit der ein vereinfachtes Intranet in Microsoft Azure simuliert wird."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/23/2015"
	ms.author="josephd"/>

# Testumgebung für die Basiskonfiguration mit Azure-Ressourcen-Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt das Erstellen von Ressourcen mit dem Ressourcen-Manager-Bereitstellungsmodell. Sie haben auch die Möglichkeit, diese Ressourcen mit dem [klassischen Bereitstellungsmodell](virtual-machines-base-configuration-test-environment.md) zu erstellen.

Dieser Artikel enthält Schritt-für-Schritt-Anleitungen zum Erstellen der Testumgebung „Basiskonfiguration“ in einem Microsoft Azure Virtual Network mit virtuellen Computern, die im Ressourcen-Manager erstellt wurden.

Sie können die Testumgebung zu folgenden Zwecken verwenden:

- Zum Entwickeln und Testen von Anwendungen
- Als Erstkonfiguration einer erweiterten Testumgebung nach Ihrem eigenen Design, die zusätzliche virtuelle Computer und Azure-Dienste enthält

Die Testumgebung für die Basiskonfiguration besteht aus dem Corpnet-Subnetz in einem reinen cloudbasierten Virtual Network namens TestLab, von dem ein vereinfachtes privates Intranet simuliert wird, das mit dem Internet verbunden ist.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG04.png)

Sie enthält folgende Elemente:

- Einen virtuellen Azure-Computer mit Windows Server 2012 R2 mit dem Namen „DC1“, der als Intranetdomänencontroller und DNS-Server (Domain Name System) konfiguriert ist
- Einen virtuellen Azure-Computer mit Windows Server 2012 R2 und dem Namen „APP1“, der als allgemeine Anwendung und Webserver konfiguriert ist
- Einen virtuellen Azure-Computer mit Windows Server 2012 R2 und dem Namen „CLIENT1“, der als Intranetclient verwendet wird

Diese Konfiguration ermöglicht „DC1“, „APP1“, „CLIENT1“ sowie zusätzlichen Corpnet-Subnetzcomputern Folgendes:

- Internetzugang zwecks Installation von Updates, Zugriff auf Ressourcen im Internet in Echtzeit, Teilnahme an öffentlichen Cloudtechnologien wie Microsoft Office 365 und weiteren Azure-Diensten
- Remoteverwaltung mithilfe von Remotedesktopverbindungen von Ihrem Computer aus, der mit dem Internet oder Ihrem Organisationsnetzwerk verbunden ist

Die Einrichtung des Corpnet-Subnetzes der Testumgebung für die Basiskonfiguration von Windows Server 2012 R2 in Azure besteht aus den folgenden vier Phasen:

1.	Erstellen des virtuellen Netzwerks
2.	Konfigurieren von DC1
3.	Konfigurieren von APP1
4.	Konfigurieren von CLIENT1

Wenn Sie noch nicht über ein Azure-Konto verfügen, erhalten Sie unter [Try Azure](http://azure.microsoft.com/pricing/free-trial/) (Azure testen) eine kostenlose Testversion. Wenn Sie über ein MSDN-Abonnement verfügen, lesen Sie [Azure-Vorteil für MSDN-Abonnenten](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

> [AZURE.NOTE]Durch virtuelle Computer, die in Azure ausgeführt werden, entstehen fortlaufend Kosten. Diese Kosten werden im Rahmen der kostenlosen Testversion, des MSDN-Abonnements oder des kostenpflichtigen Abonnements abgerechnet. Weitere Informationen zu den Kosten der in Azure ausgeführten virtuellen Computer finden Sie unter [Virtuelle Computer – Preisdetails](http://azure.microsoft.com/pricing/details/virtual-machines/) und [Azure-Preisrechner](http://azure.microsoft.com/pricing/calculator/). Informationen dazu, wie Sie die Kosten möglichst gering halten können, finden Sie unter [Minimizing the costs of test environment virtual machines in Azure](#costs) (Minimieren der Kosten von Testumgebungen für virtuelle Computer in Azure).

## Phase 1: Erstellen des virtuellen Netzwerks

Befolgen Sie bei Bedarf zuerst die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md) zum Installieren von Azure PowerShell auf dem lokalen Computer. Öffnen Sie eine Azure PowerShell-Eingabeaufforderung.

Wählen Sie als Nächstes das richtige Azure-Abonnement für diese Befehle aus. Ersetzen Sie alles in den Anführungszeichen, einschließlich der < and > Zeichen, durch die korrekten Namen.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Sie erhalten den Abonnementnamen aus der Eigenschaft „SubscriptionName“ in der Anzeige des Befehls **Get-AzureSubscription**.

Wechseln Sie in Azure PowerShell jetzt in den Ressourcen-Manager-Modus.

	Switch-AzureMode AzureResourceManager 

Erstellen Sie als Nächstes eine neue Ressourcengruppe für das Basiskonfiguration-Testlabor. Um einen eindeutigen Namen für die Ressourcengruppe zu finden, verwenden Sie diesen Befehl zum Auflisten der vorhandenen Ressourcengruppen.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Verwenden Sie diese Befehle, um die Azure-Speicherorte aufzulisten, in denen Sie virtuelle Computer auf Ressourcen-Manager-Basis erstellen können.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

Erstellen Sie die neue Ressourcengruppe mit diesen Befehlen. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < and >, durch die korrekten Namen.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

Virtuelle Computer auf Ressourcen-Manager-Basis benötigen ein Speicherkonto auf Ressourcen-Manager-Basis. Geben Sie einen global eindeutigen Namen für das Speicherkonto an, der nur aus Kleinbuchstaben und Zahlen besteht. Mit diesem Befehl können Sie die vorhandenen Speicherkonten auflisten.

	Get-AzureStorageAccount | Sort Name | Select Name

Um zu testen, ob ein gewählter Speicherkontenname global eindeutig ist, müssen Sie den Befehl **Test-AzureName** im Azure-Dienstverwaltungsmodus von PowerShell ausführen. Verwenden Sie diese Befehle.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Wenn der Befehl "Test-AzureName" als Ergebnis **False** zurückgibt, ist der vorgeschlagene Name eindeutig. Wenn Sie einen eindeutigen Namen ermittelt haben, wechseln Sie mit dem folgenden Befehl wieder in den Ressourcen-Manager-Modus von Azure PowerShell.

	Switch-AzureMode AzureResourceManager 

Erstellen Sie mit diesen Befehlen ein neues Speicherkonto für die neue Testumgebung.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<storage account name>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Erstellen Sie als Nächstes das Azure Virtual Network „TestLab“, in dem das Corpnet-Subnetz der Basiskonfiguration gehostet wird.

	$rgName="<name of your new resource group>"
	$locName="<Azure location name, such as West US>"
	$corpnetSubnet=New-AzureVirtualNetworkSubnetConfig -Name Corpnet -AddressPrefix 10.0.0.0/24
	New-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/8 -Subnet $corpnetSubnet –DNSServer 10.0.0.4

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG01.png)

## Phase 2: Konfigurieren von DC1

Bei DC1 handelt es sich um einen Domänencontroller für die AD DS-Domäne (Active Directory Domain Services) „corp.contoso.com“ und zudem um einen DNS-Server für die virtuellen Computer im virtuellen Netzwerk „TestLab“.

Geben Sie zuerst den Namen der Ressourcengruppe, den Azure-Standort und den Namen des Speicherkontos an, und führen Sie mithilfe der Azure PowerShell-Eingabeaufforderung auf dem lokalen Computer die folgenden Befehle aus, um für DC1 einen virtuellen Computer in Azure zu erstellen:

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 10.0.0.4
	$vm=New-AzureVMConfig -VMName DC1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-ADDSDisk.vhd"
	Add-AzureVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName DC1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name DC1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Stellen Sie dann eine Verbindung mit dem virtuellen Computer DC1 her.

1.	Klicken Sie im Azure-Vorschauportal im linken Bereich auf **Alle durchsuchen**, und klicken Sie in der Liste **Durchsuchen** auf **Virtuelle Computer** und dann auf den virtuellen Computer **DC1**.  
2.	Klicken Sie unter **DC1** auf **Verbinden**.
3.	Öffnen Sie die heruntergeladene Datei „DC1.rdp“, wenn Sie dazu aufgefordert werden.
4.	Wenn ein Meldungsfeld der Remotedesktopverbindung angezeigt wird, klicken Sie auf **Verbinden**.
5.	Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, geben Sie Folgendes ein:
- Name: **DC1\**[Name des lokalen Administratorkontos]
- Kennwort: [Kennwort des lokalen Administratorkontos]
6.	Wenn ein Meldungsfeld der Remotedesktopverbindung zu Zertifikaten angezeigt wird, klicken Sie auf **Ja**.

Fügen Sie anschließend einen zusätzlichen Datenträger als neues Volume mit dem Laufwerkbuchstaben F: hinzu.

1.	Klicken Sie im linken Bereich des Server-Managers auf **Datei- und Speicherdienste** und anschließend auf **Datenträger**.
2.	Klicken Sie im Inhaltsbereich in der Gruppe **Datenträger** auf **Datenträger 2** (wobei **Partition** die Einstellung **Unbekannt** aufweisen muss).
3.	Klicken Sie auf **Aufgaben** und anschließend auf **Neues Volume**.
4.	Klicken Sie auf der Seite „Voraussetzungen“ des Assistenten für neue Volumes auf **Weiter**.
5.	Klicken Sie auf der Seite „Server und Datenträger auswählen“ auf **Datenträger 2** und anschließend auf **Weiter**. Wenn Sie dazu aufgefordert werden, klicken Sie auf **OK**.
6.	Klicken Sie auf der Seite „Geben Sie die Größe des Volumes an“ auf **Weiter**.
7.	Klicken Sie auf der Seite „Einem Laufwerkbuchstaben oder Ordner zuweisen“ auf **Weiter**.
8.	Klicken Sie auf der Seite „Dateisystemeinstellungen auswählen“ auf **Weiter**.
9.	Klicken Sie auf der Seite „Auswahl bestätigen“ auf **Erstellen**.
10.	Wenn Sie fertig sind, klicken Sie auf **Schließen**.

Als Nächstes konfigurieren Sie DC1 als Domänencontroller und DNS-Server für die Domäne „corp.contoso.com“. Führen Sie mithilfe einer Windows PowerShell-Eingabeaufforderung auf Administratorebene die folgenden Befehle aus:

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSForest -DomainName corp.contoso.com -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Stellen Sie nach dem Neustart des virtuellen Computers DC1 erneut eine Verbindung mit DC1 her.

1.	Klicken Sie im Azure-Vorschauportal im linken Bereich auf „Alle durchsuchen“, und klicken Sie in der Liste „Durchsuchen“ auf „Virtuelle Computer“ und dann auf den virtuellen Computer „DC1“.
2.	Klicken Sie unter „DC1“ auf „Verbinden“.
3.	Wenn Sie zum Öffnen von „DC1.rdp“ aufgefordert werden, klicken Sie auf **Öffnen**.
4.	Wenn ein Meldungsfeld der Remotedesktopverbindung angezeigt wird, klicken Sie auf **Verbinden**.
5.	Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, geben Sie Folgendes ein:
- Name: **CORP\**[Name des lokalen Administratorkontos]
- Kennwort: [Kennwort des lokalen Administratorkontos]
6.	Wenn ein Meldungsfeld der Remotedesktopverbindung zu Zertifikaten angezeigt wird, klicken Sie auf **Ja**.

Als Nächstes erstellen Sie in Active Directory ein Benutzerkonto zum Anmelden bei Mitgliedscomputern der CORP-Domäne. Führen Sie mithilfe einer Windows PowerShell-Eingabeaufforderung auf Administratorebene nacheinander die folgenden Befehle aus:

	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

Beachten Sie, dass durch den ersten Befehl die Aufforderung ausgelöst wird, das Kontokennwort für User1 einzugeben. Wählen Sie ein sicheres Kennwort aus, da dieses Konto für Remotedesktopverbindungen sämtlicher Mitgliedscomputer der CORP-Domäne verwendet wird. Sie können die Sicherheit des Kennworts unter [Password Checker: Using Strong Passwords](https://www.microsoft.com/security/pc-security/password-checker.aspx) (Kennwortprüfung – Verwenden sicherer Kennwörter) überprüfen. Zeichnen Sie das User1-Kontokennwort auf, und speichern Sie es an einem sicheren Ort.

Schließen Sie die Remotedesktopsitzung mit DC1, und stellen Sie die Verbindung dann mit dem Konto „CORP\\User1“ wieder her.

Führen Sie anschließend mithilfe einer Windows PowerShell-Eingabeaufforderung mit Administratorrechten den folgenden Befehl aus, um Datenverkehr für das Ping-Tool zuzulassen:

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG02.png)

## Phase 3: Konfigurieren von APP1

App1 bietet Web- und Dateifreigabedienste.

Geben Sie zuerst den Namen der Ressourcengruppe, den Azure-Standort und den Namen des Speicherkontos an, und führen Sie mithilfe der Azure PowerShell-Eingabeaufforderung auf dem lokalen Computer die folgenden Befehle aus, um für APP1 einen virtuellen Computer in Azure zu erstellen:

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureVMConfig -VMName APP1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for APP1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName APP1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/APP1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name APP1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Stellen Sie als Nächstes eine Verbindung mit dem virtuellen Computer APP1 her, indem Sie den Namen und das Kennwort des lokalen Administratorkontos für APP1 verwenden, und öffnen Sie dann eine Windows PowerShell-Befehlseingabeaufforderung auf Administratorebene.

Zum Überprüfen der Namensauflösung und der Netzwerkkommunikation zwischen APP1 und DC1 führen Sie den Befehl **ping dc1.corp.contoso.com** aus und vergewissern sich, dass Sie vier Antworten erhalten.

Verknüpfen Sie den virtuellen Computer APP1 als Nächstes mit der CORP-Domäne, indem Sie diese Befehle an der Windows PowerShell-Eingabeaufforderung verwenden.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Beachten Sie, dass Sie Ihre Anmeldeinformationen für das Domänenkonto „CORP\\User1“ angeben müssen, nachdem Sie den Befehl „Add-Computer“ eingeben.

Stellen Sie nach dem Neustart eine Verbindung mit APP1 her, indem Sie den Benutzernamen und das Kennwort für das Konto „CORP\\User1“ verwenden, und öffnen Sie dann eine Windows PowerShell-Befehlseingabeaufforderung auf Administratorebene.

Dann machen Sie aus APP1 einen Webserver, indem Sie mithilfe der Windows PowerShell-Eingabeaufforderung den folgenden Befehl ausführen:

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

Als Nächstes erstellen Sie mit den folgenden Befehlen auf APP1 einen freigegebenen Ordner und darin eine Textdatei:

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG03.png)

## Phase 4: Konfigurieren von CLIENT1

CLIENT1 fungiert als typischer Laptop-, Tablet- oder Desktopcomputer im Contoso-Intranet.

Geben Sie zuerst den Namen der Ressourcengruppe, den Azure-Standort und den Namen des Speicherkontos an, und führen Sie mithilfe der Azure PowerShell-Eingabeaufforderung auf dem lokalen Computer die folgenden Befehle aus, um für CLIENT1 einen virtuellen Computer in Azure zu erstellen:

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureVMConfig -VMName CLIENT1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for CLIENT1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName CLIENT1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id	
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/CLIENT1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name CLIENT1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Stellen Sie als Nächstes eine Verbindung mit dem virtuellen Computer CLIENT1 her, indem Sie den Namen und das Kennwort des lokalen Administratorkontos für CLIENT1 verwenden, und öffnen Sie dann eine Windows PowerShell-Befehlseingabeaufforderung auf Administratorebene.

Zum Überprüfen der Namensauflösung und der Netzwerkkommunikation zwischen CLIENT1 und DC1 führen Sie mithilfe der Azure PowerShell-Eingabeaufforderung den Befehl **ping dc1.corp.contoso.com** aus und vergewissern sich, dass Sie vier Antworten erhalten.

Verknüpfen Sie den virtuellen Computer CLIENT1 als Nächstes mit der CORP-Domäne, indem Sie diese Befehle an der Windows PowerShell-Eingabeaufforderung verwenden.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Beachten Sie, dass Sie Ihre Anmeldeinformationen für das Domänenkonto „CORP\\User1“ angeben müssen, nachdem Sie den Befehl „Add-Computer“ eingeben.

Stellen Sie nach dem Neustart eine Verbindung mit CLIENT1 her, indem Sie den Benutzernamen und das Kennwort für das Konto „CORP\\User1“ verwenden, und öffnen Sie dann eine Windows PowerShell-Befehlseingabeaufforderung auf Administratorebene.

Als Nächstes überprüfen Sie, ob Sie mit CLIENT1 auf die Web- und Dateifreigaberessourcen auf APP1 zugreifen können.

1.	Klicken Sie im Server-Manager im Strukturbereich auf **Lokaler Server**.
2.	Klicken Sie unter **Eigenschaften von CLIENT1** neben **Verstärkte Sicherheitskonfiguration für IE** auf **Ein** .
3.	Klicken Sie unter **Verstärkte Sicherheitskonfiguration für IE** bei **Administratoren** und **Benutzern** auf **Aus**, und klicken Sie dann auf **OK**.
4.	Klicken Sie auf der Startseite auf **Internet Explorer** und dann auf **OK**.
5.	Geben Sie in die Adressleiste ****http://app1.corp.contoso.com/** ein, und drücken Sie dann die EINGABETASTE. Nun sollte die IIS-Standardwebseite (Internet-Informationsdienste) für APP1 angezeigt werden.
6.	Klicken Sie in der Desktopsymbolleiste auf das Symbol des Datei-Explorers.
7.	Geben Sie in der Adressleiste **\\\app1\\Files** ein, und drücken Sie dann die EINGABETASTE.
8.	Es wird ein Fenster mit dem Inhalt des freigegebenen Ordners geöffnet.
9.	Doppelklicken Sie im Fenster **Dateien** des freigegebenen Ordners auf die Datei **Example.txt**. Nun wird der Inhalt der Datei „Example.txt“ angezeigt.
10.	Schließen Sie die Fenster **example.txt – Editor** und **Dateien** des freigegebenen Ordners.

Dies ist die endgültige Konfiguration.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG04.png)

Die Basiskonfiguration in Azure kann nun zur Anwendungsentwicklung, zu Testzwecken oder für zusätzliche Testumgebungen verwendet werden.

## Zusätzliche Ressourcen

[Hybrid cloud test environments](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md) (Testumgebungen für Hybridclouds)

[Testumgebung für die Basiskonfiguration](virtual-machines-base-configuration-test-environment.md)


## <a id="costs"></a>Minimizing the costs of test environment virtual machines in Azure (Minimieren der Kosten von Testumgebungen für virtuelle Computer in Azure)

Zum Minimieren der Kosten ausgeführter virtueller Computer in Testumgebungen haben Sie folgende Möglichkeiten:

- Erstellen Sie die Testumgebung, und führen Sie die erforderlichen Tests und Demonstrationen möglichst rasch aus. Löschen Sie nach Abschluss des Vorgangs die virtuellen Computer der Testumgebung.
- Fahren Sie die virtuellen Computer der Testumgebung herunter, deren Zuordnung aufgehoben ist.

Zum Herunterfahren der virtuellen Computer mit Azure PowerShell geben Sie den Namen der Ressourcengruppe an und führen die folgenden Befehle aus:

	$rgName="<your resource group name>"
	Stop-AzureVM -ResourceGroupName $rgName -Name "CLIENT1"
	Stop-AzureVM -ResourceGroupName $rgName -Name "APP1"
	Stop-AzureVM -ResourceGroupName $rgName -Name "DC1" -Force -StayProvisioned

Um sicherzustellen, dass die virtuellen Computer ordnungsgemäß funktionieren, wenn sie aus dem Status „Beendet (Zuordnung aufgehoben)“ gestartet werden, starten Sie sie in folgender Reihenfolge:

1.	DC1
2.	APP1
3.	CLIENT1

Zum Starten der virtuellen Computer mit Azure PowerShell in der richtigen Reihenfolge geben Sie den Namen der Ressourcengruppe an und führen die folgenden Befehle aus:

	$rgName="<your resource group name>"
	Start-AzureVM -ResourceGroupName $rgName -Name "DC1"
	Start-AzureVM -ResourceGroupName $rgName -Name "APP1"
	Start-AzureVM -ResourceGroupName $rgName -Name "CLIENT1"

<!---HONumber=Sept15_HO3-->