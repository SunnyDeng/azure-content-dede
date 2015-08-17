<properties
	pageTitle="Testumgebung für die Basiskonfiguration"
	description="Erfahren Sie, wie Sie eine einfache Entwicklungs-/Testumgebung erstellen können, von der ein vereinfachtes Intranet in Microsoft Azure simuliert wird."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2015"
	ms.author="josephd"/>

# Testumgebung für die Basiskonfiguration

Dieser Artikel enthält schrittweise Anweisungen zum Erstellen der Testumgebung für die Basiskonfiguration in einem Azure Virtual Network mit virtuellen Computern, die in der Dienstverwaltung erstellt wurden.

Sie können die Testumgebung zu folgenden Zwecken verwenden:

- Zum Entwickeln und Testen von Anwendungen
- Für eine [simulierte Hybridcloudumgebung](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)
- Zur Erweiterung mit zusätzlichen virtuellen Computern und Azure-Diensten in einer individuellen Testumgebung

Die Testumgebung für die Basiskonfiguration besteht aus dem Corpnet-Subnetz in einem reinen cloudbasierten Virtual Network namens TestLab, von welchem ein vereinfachtes privates Intranet simuliert wird, das mit dem Internet verbunden ist.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

Sie enthält folgende Elemente:

- Einen virtuellen Azure-Computer mit Windows Server 2012 R2 mit dem Namen „DC1“, der als Intranetdomänencontroller und DNS-Server (Domain Name System) konfiguriert ist
- Einen virtuellen Azure-Computer mit Windows Server 2012 R2 und dem Namen „APP1“, der als allgemeine Anwendung und Webserver konfiguriert ist
- Einem virtuellen Azure-Computer mit Windows Server 2012 R2 und dem Namen „CLIENT1“, der als Intranetclient verwendet wird

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

[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Testumgebung für die Basiskonfiguration mit Azure-Ressourcen-Manager](virtual-machines-base-configuration-test-environment-resource-manager.md)


## Phase 1: Erstellen des virtuellen Netzwerks

Zuerst erstellen Sie das Azure Virtual Network „TestLab“, in dem das Corpnet-Subnetz der Basiskonfiguration gehostet wird.

1.	Klicken Sie in der Taskleiste des Azure-Verwaltungsportals auf **Neu > Network Services > Virtual Network > Benutzerdefiniert erstellen**.
2.	Geben Sie auf der Seite „Details zum virtuellen Netzwerk“ unter **Name** den Namen **TestLab** ein.
3.	Wählen Sie unter **Location** (Standort) die entsprechende Region aus.
4.	Klicken Sie auf den „Weiter“-Pfeil.
5.	Geben Sie auf der Seite „DNS-Server und VPN-Konnektivität“ unter **DNS-Server** im Bereich **Namen eingeben oder auswählen** den Namen **DC1** ein, geben Sie unter **IP-Adresse** die Zeichenfolge **10.0.0.4** ein, und klicken Sie dann auf den „Weiter“-Pfeil.
6.	Klicken Sie auf der Seite „Virtuelle Netzwerkadressräume“ unter **Subnetze** auf **Subnetz-1**, und ersetzen Sie den Namen durch **Corpnet**.
7.	Klicken Sie in der Spalte **CIDR (Anzahl Adressen)** für das Corpnet-Subnetz auf **/24 (256)**.
8.	Klicken Sie auf das Symbol „Abgeschlossen“. Warten Sie, bis das virtuelle Netzwerk erstellt ist, bevor Sie fortfahren.

Befolgen Sie anschließend die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md), um Azure PowerShell auf dem lokalen Computer zu installieren. Öffnen Sie eine Azure PowerShell-Eingabeaufforderung.

Wählen Sie zunächst das richtige Azure-Abonnement für diese Befehle aus. Ersetzen Sie alles innerhalb der Anführungszeichen einschließlich der Zeichen < and > durch den korrekten Namen.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Sie erhalten den Abonnementnamen aus der Eigenschaft **SubscriptionName** in der Anzeige des Befehls **Get-AzureSubscription**.

Dann erstellen Sie einen Azure-Clouddienst. Der Clouddienst fungiert als Sicherheitsbegrenzung und logischer Container für die virtuellen Computer im virtuellen Netzwerk. Außerdem bietet er die Möglichkeit, mit den virtuellen Computern im Corpnet-Subnetz Remoteverbindungen herzustellen und Remoteverwaltungen auszuführen.

Wählen Sie einen eindeutigen Namen für den Clouddienst aus. *Der Name des Clouddiensts darf nur Buchstaben, Zahlen und Bindestriche enthalten. Das erste und das letzte Zeichen im Feld müssen Buchstaben oder Zahlen sein.*

Sie könnten den Clouddienst beispielsweise „TestLab-*UniqueSequence*“ nennen, wobei *UniqueSequence* eine Abkürzung Ihrer Organisation ist. Wenn Ihre Organisation beispielsweise „Tailspin Toys“ heißt, könnten Sie den Clouddienst „TestLab-Tailspin“ nennen.

Die Eindeutigkeit des Namens können Sie mit dem folgenden Azure PowerShell-Befehl auf dem lokalen Computer überprüfen:

	Test-AzureName -Service <Proposed cloud service name>

Wird von diesem Befehl „False“ zurückgegeben, ist der vorgeschlagene Name eindeutig. Erstellen Sie dann mit den folgenden Befehlen den Clouddienst:

	$loc="<the same location (region) as your TestLab virtual network>"
	New-AzureService -Service <Unique cloud service name> -Location $loc

Zeichnen Sie den tatsächlichen Namen des Clouddiensts auf.

Nun konfigurieren Sie ein Speicherkonto, das die Datenträger für die virtuellen Computer sowie zusätzliche Datenträger enthält. *Sie müssen einen eindeutigen Namen auswählen, der nur Kleinbuchstaben und Zahlen enthält.* Die Eindeutigkeit des Speicherkontonamens können Sie mit dem folgenden Azure PowerShell-Befehl überprüfen:

	Test-AzureName -Storage <Proposed storage account name>

Wird von diesem Befehl „False“ zurückgegeben, ist der vorgeschlagene Name eindeutig. Anschließend erstellen Sie das Speicherkonto und legen im Abonnement fest, dass es mit den folgenden Befehlen verwendet werden soll:

	$stAccount="<your storage account name>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $loc
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $stAccount

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG01.png)

## Phase 2: Konfigurieren von DC1

Bei DC1 handelt es sich um einen Domänencontroller für die AD DS-Domäne (Active Directory Domain Services) „corp.contoso.com“ und zudem um einen DNS-Server für die virtuellen Computer im virtuellen Netzwerk „TestLab“.

Zunächst geben Sie den Namen des Clouddiensts an und führen mithilfe der Azure PowerShell-Eingabeaufforderung auf dem lokalen Computer die folgenden Befehle aus, um für DC1 einen virtuellen Computer in Azure zu erstellen:

	$serviceName="<your cloud service name>"
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for DC1."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel "AD" -LUN 0 -HostCaching None
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Stellen Sie dann eine Verbindung mit dem virtuellen Computer DC1 her.

1.	Klicken Sie im Azure-Verwaltungsportal im linken Bereich auf **Virtuelle Computer** und dann in der Spalte **Status** des virtuellen Computers DC1 auf **Gestartet**.  
2.	Klicken Sie in der Taskleiste auf **Verbinden**.
3.	Wenn Sie zum Öffnen von „DC1.rdp“ aufgefordert werden, klicken Sie auf **Öffnen**.
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

1.	Klicken Sie im Azure-Verwaltungsportal auf der Seite „Virtuelle Computer“ beim virtuellen Computer DC1 in der Spalte **Status** auf **Ausführen**.
2.	Klicken Sie in der Taskleiste auf **Verbinden**.
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

Stellen Sie erneut eine Verbindung mit dem virtuellen Computer DC1 her. Verwenden Sie dabei das Konto CORP\\User1.

Führen Sie anschließend mithilfe einer Windows PowerShell-Eingabeaufforderung mit Administratorrechten den folgenden Befehl aus, um Datenverkehr für das Ping-Tool zuzulassen:

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG02.png)

## Phase 3: Konfigurieren von APP1

App1 bietet Web- und Dateifreigabedienste.

Zunächst geben Sie den Namen des Clouddiensts an und führen mithilfe einer Azure PowerShell-Eingabeaufforderung auf dem lokalen Computer die folgenden Befehle aus, um für APP1 einen virtuellen Computer in Azure zu erstellen:

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for APP1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name APP1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Dann stellen Sie eine Verbindung mit dem virtuellen Computer APP1 her (verwenden Sie dabei die Anmeldeinformationen für CORP\\User1) und öffnen eine Windows PowerShell-Eingabeaufforderung auf Administratorebene.

Zum Überprüfen der Namensauflösung und der Netzwerkkommunikation zwischen APP1 und DC1 führen Sie den Befehl **ping dc1.corp.contoso.com** aus und vergewissern sich, dass Sie vier Antworten erhalten.

Dann machen Sie aus APP1 einen Webserver, indem Sie mithilfe der Windows PowerShell-Eingabeaufforderung den folgenden Befehl ausführen:

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

Als Nächstes erstellen Sie mit den folgenden Befehlen auf APP1 einen freigegebenen Ordner und darin eine Textdatei:

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

Die aktuelle Konfiguration sieht folgendermaßen aus.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG03.png)

## Phase 4: Konfigurieren von CLIENT1

CLIENT1 fungiert als typischer Laptop-, Tablet- oder Desktopcomputer im Contoso-Intranet.

Zunächst geben Sie den Namen des Clouddiensts an und führen mithilfe der Azure PowerShell-Eingabeaufforderung auf dem lokalen Computer die folgenden Befehle aus, um für CLIENT1 einen virtuellen Computer in Azure zu erstellen:

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for CLIENT1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name CLIENT1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Stellen Sie anschließend eine Verbindung mit dem virtuellen Computer CLIENT1 her. Verwenden Sie dabei die Anmeldeinformationen für CORP\\User1.

Zum Überprüfen der Namensauflösung und der Netzwerkkommunikation zwischen CLIENT1 und DC1 führen Sie mithilfe der Azure PowerShell-Eingabeaufforderung den Befehl **ping dc1.corp.contoso.com** aus und vergewissern sich, dass Sie vier Antworten erhalten.

Als Nächstes überprüfen Sie, ob Sie mit CLIENT1 auf die Web- und Dateifreigaberessourcen auf APP1 zugreifen können.

1.	Klicken Sie im Server-Manager im Strukturbereich auf **Lokaler Server**.
2.	Klicken Sie unter **Eigenschaften von CLIENT1** neben **Verstärkte Sicherheitskonfiguration für IE** auf **Ein** .
3.	Klicken Sie unter **Verstärkte Sicherheitskonfiguration für IE** bei **Administratoren** und **Benutzern** auf **Aus**, und klicken Sie dann auf **OK**.
4.	Klicken Sie auf der Startseite auf **Internet Explorer** und dann auf **OK**.
5.	Geben Sie in die Adressleiste ****http://app1.corp.contoso.com/** ein, und drücken Sie dann die EINGABETASTE. Nun sollte die IIS-Standardwebseite (Internet-Informationsdienste) für APP1 angezeigt werden.
6.	Klicken Sie in der Desktopsymbolleiste auf das Symbol des Datei-Explorers.
7.	Geben Sie in der Adressleiste **\\\\app1\\Files** ein, und drücken Sie dann die EINGABETASTE.
8.	Es wird ein Fenster mit dem Inhalt des freigegebenen Ordners geöffnet.
9.	Doppelklicken Sie im Fenster **Dateien** des freigegebenen Ordners auf die Datei **Example.txt**. Nun wird der Inhalt der Datei „Example.txt“ angezeigt.
10.	Schließen Sie die Fenster **example.txt – Editor** und **Dateien** des freigegebenen Ordners.

Dies ist die endgültige Konfiguration.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

Die Basiskonfiguration in Azure kann nun zur Anwendungsentwicklung, zu Testzwecken oder für zusätzliche Testumgebungen wie eine [simulierte Hybridcloudumgebung](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) verwendet werden.

## Zusätzliche Ressourcen

[Hybrid cloud test environments](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md) (Testumgebungen für Hybridclouds)

[Testumgebung für die Basiskonfiguration mit Azure-Ressourcen-Manager](virtual-machines-base-configuration-test-environment-resource-manager.md)

## <a id="costs"></a>Minimizing the costs of test environment virtual machines in Azure (Minimieren der Kosten von Testumgebungen für virtuelle Computer in Azure)

Zum Minimieren der Kosten ausgeführter virtueller Computer in Testumgebungen haben Sie folgende Möglichkeiten:

- Erstellen Sie die Testumgebung, und führen Sie die erforderlichen Tests und Demonstrationen möglichst rasch aus. Löschen Sie nach Abschluss des Vorgangs die virtuellen Computer der Testumgebung.
- Fahren Sie die virtuellen Computer der Testumgebung herunter, deren Zuordnung aufgehoben ist.

Zum Herunterfahren der virtuellen Computer mit Azure PowerShell geben Sie den Namen des Clouddiensts an und führen die folgenden Befehle aus:

	$serviceName="<your cloud service name>"
	Stop-AzureVM -ServiceName $serviceName -Name "CLIENT1"
	Stop-AzureVM -ServiceName $serviceName -Name "APP1"
	Stop-AzureVM -ServiceName $serviceName -Name "DC1" -Force -StayProvisioned


Um sicherzustellen, dass die virtuellen Computer ordnungsgemäß funktionieren, wenn sie aus dem Status „Beendet (Zuordnung aufgehoben)“ gestartet werden, starten Sie sie in folgender Reihenfolge:

1.	DC1
2.	APP1
3.	CLIENT1

Zum Starten der virtuellen Computer in der angegebenen Reihenfolge mit Azure PowerShell geben Sie den Namen des Clouddiensts an und führen die folgenden Befehle aus:

	$serviceName="<your cloud service name>"
	Start-AzureVM -ServiceName $serviceName -Name "DC1"
	Start-AzureVM -ServiceName $serviceName -Name "APP1"
	Start-AzureVM -ServiceName $serviceName -Name "CLIENT1"

<!---HONumber=August15_HO6-->