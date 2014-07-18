<properties linkid="manage-services-networking-active-directory-forest" urlDisplayName="Active Directory forest" pageTitle="Install an Active Directory forest on an Azure virtual network" metaKeywords="" description="A tutorial that explains how to create a new Active Directory forest on a virtual machine (VM) on an Azure Virtual Network." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />



Installieren einer neuen Active Directory-Gesamtstruktur auf einem virtuellen Azure-Netzwerk
============================================================================================

In diesem Thema wird das Erstellen einer neuen Windows Server Active Directory-Umgebung in einem virtuellen Azure-Netzwerk erläutert, das auf einem virtuellen Computer (Virtual Machine, VM) mit [Azure Virtual Network](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx) läuft. In diesem Fall ist das virtuelle Azure-Netzwerk nicht mit einem lokalen Netzwerk verbunden.

Folgende Themen könnten für Sie ebenfalls von Interesse sein:

-   Optional können Sie [mit dem Assistenten im Verwaltungsportal ein Standort-zu-Standort-VPN konfigurieren](http://msdn.microsoft.com/en-us/library/windowsazure/dn133795.aspx) und dann entweder eine neue Gesamtstruktur installieren oder eine lokale Gesamtstruktur zu einem virtuellen Azure-Netzwerk erweitern. Erläuterungen hierzu finden Sie unter [Installieren eines Active Directory-Replikatdomänencontrollers unter virtuellen Netzwerken in Windows Azure](http://www.windowsazure.com/en-us/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/).
-   Weitere Anleitungen zu den Konzepten für die Installation von Active Directory-Domänendiensten (AD DS) auf einem virtuellen Azure-Netzwerk finden Sie unter [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Windows Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx).
-   Schrittweise Anleitungen, wie Sie auf Azure eine Testumgebung mit AD DS erstellen, finden Sie unter [Test Lab Guide: Windows Server 2012 R2-Basiskonfiguration auf Windows Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41684).

Inhaltsverzeichnis
------------------

-   [Unterschiede zwischen der virtuellen und der lokalen Umgebung](#differ)
-   [Schritt 1: Erstellen eines virtuellen Azure-Netzwerks](#createvnet)
-   [Schritt 2: Erstellen eines virtuellen Computers (VM) für die Serverrollen Domänencontroller und DNS](#createvm)
-   [Schritt 3: Installieren von Windows Server Active Directory](#installad)
-   [Schritt 4: Einrichten des DNS-Servers für das virtuelle Azure-Netzwerk](#dns)
-   [Schritt 5: Erstellen von virtuellen Computern und Bereitstellen als Domänenmitglieder](#domainmembers)

Unterschiede zwischen der virtuellen und der lokalen Umgebung
-------------------------------------------------------------

Die Installation eines Domänencontrollers in Azure unterscheidet sich nicht wesentlich von der lokalen Installation. Die Hauptunterschiede sind in der folgenden Tabelle aufgeführt.

Konfigurieren | Lokal | Azure Virtual Network 
------------- | ------------- | ------------ 
**IP-Adresse des Domänencontrollers** | Statische IP-Adresse in den Netzwerkadaptereigenschaften zuweisen | IP-Adresse über DHCP abrufen und durch Ausführen des cmdlets "Set-AzureStaticVNetIP" als statische Adresse konfigurieren 
**DNS-Client-Resolver** | Bevorzugte und alternative Adresse des DNS-Servers in den Netzwerkadaptereigenschaften von Domänenmitgliedern eingeben | Adresse des DNS-Servers in den Eigenschaften des virtuellen Netzwerks eingeben 
**Speicherort der Active Directory-Datenbank** | Standardspeicherort C:\\ kann optional geändert werden | Standardspeicherort C:\\ muss geändert werden

Schritt 1: Erstellen eines virtuellen Azure-Netzwerks
-----------------------------------------------------

1.  Melden Sie sich bei dem [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2.  Erstellen Sie ein virtuelles Netzwerk. Klicken Sie auf **Netzwerke** \> **Create a virtual network**. Geben Sie bei Ausführen des Assistenten die Werte aus der folgenden Tabelle ein.

    Seite des Assistenten... | Einzugebende Werte 
	------------- | ------------- 
	**Angaben zum virtuellen Netzwerk** | <p>Name: Geben Sie einen Namen für Ihr virtuelles Netzwerk ein</p><p>Region: Wählen Sie die nächstgelegene Region aus</p><p>Affinitätsgruppe: **Erstellen Sie eine neue Affinitätsgruppe**</p><p>Affinitätsgruppenname: Geben Sie einen Namen für Ihre Affinitätsgruppe ein</p> 
	**DNS und VPN** | <p>Lassen Sie das Feld für den DNS-Server leer </p><p>Wählen Sie keine VPN-Option aus</p> 
	**Virtual network address spaces** | <p>Subnetzname: Geben Sie einen Namen für Ihr Subnetz ein</p><p>Start-IP-Adresse: **10.0.0.0**</p><p>CIDR: **/24 (256)**</p>

Schritt 2: Erstellen eines virtuellen Computers (VM) für die Serverrollen Domänencontroller und DNS
---------------------------------------------------------------------------------------------------

1.  Klicken Sie auf **Neu** \> **Berechnen** \> **Virtueller Computer** \> **Aus Katalog**.
2.  Geben Sie bei Ausführen des Assistenten die Werte aus der folgenden Tabelle ein.

    Seite des Assistenten... | Einzugebende Werte
	------------- | ------------- 
	**Betriebssystem** | Wählen Sie **Windows Server 2012 R2 Datacenter** aus 
	**Konfiguration des virtuellen Computers** | <p>Veröffentlichungsdatum: Das heutige Datum</p<p>Computername: Geben Sie einen eindeutigen Wert ein</p><p>Ebene: Standard</p><p>Größe: Wählen Sie eine beliebige Größe aus</p><p>Benutzername: Geben Sie einen Namen ein. Dieses Benutzerkonto wird ein Mitglied der integrierten Gruppe "Administratoren" sein. </p><p>Kennwort: Muss mindestens 8 Zeichen lang sein und 3 der folgenden Zeichentypen enthalten:</p><ul><li>einen Großbuchstaben</li><li>einen Kleinbuchstaben</li><li>eine Zahl</li><li>ein Sonderzeichen</li></ul> 
	**Clouddienst** | <p>Clouddienst: **Erstellen Sie einen neuen Clouddienst**</p><p>Clouddienstname: Übernehmen Sie den Standardwert</p><p>Region/Affinitätsgruppe/Virtuelles Netzwerk: Wählen Sie das virtuelle Netzwerk aus, das Sie erstellt haben</p><p>Virtual network subnet: Wählen Sie das Subnetz aus, das Sie erstellt haben. </p><p>Speicherkonto: **Verwenden Sie ein automatisch erzeugtes Speicherkonto**</p><p>Verfügbarkeitssatz: **Kein**</p><p>Endpunkte: Übernehmen Sie die Standardwerte</p>
	**VM Agent** | Wählen Sie **Install the VM Agent** aus

3.  Die dynamische IP-Adresse, die dem virtuellen Computer standardmäßig zugewiesen wird, ist für die Dauer des Clouddienstes gültig. Sie ändert sich jedoch, wenn der virtuelle Computer heruntergefahren wird. Sie können eine statische IP-Adresse zuweisen, indem Sie das [cmdlet "Set-AzureStaticVNetIP Azure PowerShell"](http://msdn.microsoft.com/en-us/library/windowsazure/dn630228.aspx) ausführen. Dann bleibt die IP-Adresse auch nach dem Herunterfahren des virtuellen Computers erhalten.
4.  Schließen Sie einen zusätzlichen Datenträger an den virtuellen Computer an, auf dem die Active Directory-Datenbank, Protokolle und SYSVOL gespeichert werden.
	5.  Klicken Sie auf **VM** \> **Anfügen** \> **Leeren Datenträger anfügen**.
	6.  Geben Sie eine Größe an (zum Beispiel 10 GB), und übernehmen Sie alle anderen Standardwerte.
7.  Melden Sie sich bei dem virtuellen Computer an, und formatieren Sie den zusätzlichen Datenträger.
	8.  Klicken Sie auf **Verbinden**, um sich bei dem virtuellen Computer anzumelden, klicken Sie auf **Öffnen**, um eine RDP-Sitzung zu erstellen, und klicken Sie nochmals auf **Verbinden**.
	9.  Geben Sie als Anmeldeinformationen den neuen Benutzernamen und das neue Kennwort ein.
	10. Klicken Sie im Server-Manager auf **Tools** \> **Computerverwaltung**.
	11. Klicken Sie auf **Datenträgerverwaltung** und dann auf **OK**, um den neuen Datenträger zu initialisieren.
	12. Klicken Sie mit der rechten Maustaste auf den Datenträgernamen und dann auf **Neues einfaches Volume**. Beenden Sie den Assistenten mit der Formatierung des neuen Laufwerks.

Schritt 3: Installieren von Windows Server Active Directory
-----------------------------------------------------------

[Installieren Sie AD DS](http://technet.microsoft.com/library/jj574166.aspx) genauso wie in einer lokalen Umgebung (also über die Benutzeroberfläche, eine Antwortdatei oder Windows PowerShell). Für die Installation einer neuen Gesamtstruktur brauchen Sie Administratoranmeldeinformationen. Damit die Active Directory-Datenbank, Protokolle und SYSVOL auf dem zusätzlich an den virtuellen Computer angeschlossenen Datenträger gespeichert werden können, müssen Sie den Standardspeicherort vom Betriebssystemlaufwerk auf den zusätzlichen Datenträger verlagern.

Wenn die DC-Installation beendet ist, schließen Sie den virtuellen Computer wieder an und melden sich beim DC an. Denken Sie daran, die Domänenanmeldeinformationen anzugeben.

Schritt 4: Einrichten des DNS-Servers für das virtuelle Azure-Netzwerk
----------------------------------------------------------------------

1.  Klicken Sie auf **Virtuelle Netzwerke**, doppelklicken Sie auf das virtuelle Netzwerk, das Sie erstellt haben, und klicken Sie dann auf **Konfigurieren**.
2.  Geben Sie unter **DNS-Server** den Namen und die DIP des DC ein, und klicken Sie auf **Speichern**.
3.  Wählen Sie den virtuellen Computer aus, und klicken Sie auf **Neu starten**, damit der virtuelle Computer die DNS-Resolver-Einstellungen mit der IP-Adresse des neuen DNS-Servers konfiguriert.

Schritt 5: Erstellen von virtuellen Computern und Bereitstellen als Domänenmitglieder
-------------------------------------------------------------------------------------

Erstellen Sie zusätzliche virtuelle Computer, und stellen Sie diese als Domänenmitglieder bereit. Sie können die Benutzeroberfläche oder Azure Power Shell verwenden. Wenn Sie die Benutzeroberfläche verwenden, führen Sie die gleichen Schritte aus wie zur Erstellung des ersten virtuellen Computers. Binden Sie die virtuellen Computer dann ebenso in die Domäne ein wie bei einer lokalen Installation. Wenn Sie Azure PowerShell verwenden, können Sie wie folgt virtuelle Computer bereitstellen und diese dann beim ersten Hochfahren in die Domäne aufnehmen. '

    cls

    Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
    Select-AzureSubscription -SubscriptionName "Free Trial"

    #Neuen virtuellen Computer bereitstellen und in Domäne einbinden
    #-------------------------------------------
    #DNS-IP meines DCs (10.0.0.4)spezifizieren
    $myDNS = New-AzureDNS -Name 'DC-1' -IPAddress '10.0.0.4'

    # Zu verwendendes Betriebssystemabbild
    $image = 'a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201310.01-en.us-127GB.vhd'
    $service = 'ConApp1'
    $AG = 'YourAffinityGroup'
    $vnet = 'YourVirtualNetwork'
    $pwd = 'P@ssw0rd'
    $size = 'Small'

    #VM-Konfiguration
    $vmname = 'ConApp1'
    $MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

    New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Wenn Sie das Skript erneut ausführen, müssen Sei für \$service einen eindeutigen Wert eingeben. Mit "Test-AzureName -Service *Dienstname*" können Sie feststellen, ob der Name bereits vergeben ist.

Siehe auch
----------

-   [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Windows Azure](http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx)

-   [Erstellen eines virtuellen Netzwerks in Windows Azure](http://www.windowsazure.com/en-us/manage/services/networking/create-a-virtual-network/)

-   [Erstellen eines virtuellen Netzwerk mit standortübergreifenden Verbindungen](http://www.windowsazure.com/en-us/documentation/articles/virtual-networks-create-site-to-site-cross-premises-connectivity/)

-   [Installieren eines Active Directory-Replikatdomänencontrollers in Windows Azure](http://www.windowsazure.com/en-us/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/)

-   [Azure Virtual Network](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)

-   [Installieren und Konfigurieren von Windows Azure PowerShell](http://www.windowsazure.com/en-us/documentation/articles/install-configure-powershell/)

-   [Windows Azure PowerShell](http://msdn.microsoft.com/en-us/library/windowsazure/jj156055.aspx)

-   [Windows Azure-Verwaltungs-Cmdlets](http://msdn.microsoft.com/en-us/library/windowsazure/jj152841)

-   [Einrichten einer statischen IP-Adresse für einen virtuellen Computer in Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)

-   [Installieren einer neuen Active Directory-Gesamtstruktur](http://technet.microsoft.com/library/jj574166.aspx)

-   [Einführung in die Virtualisierung von Active Directory-Domänendiensten (AD DS) (Ebene 100).](http://technet.microsoft.com/en-us/library/hh831734.aspx)

-   [Test Lab Guide: Windows Server 2012 R2-Basiskonfiguration auf Windows Azure.](http://www.microsoft.com/en-us/download/details.aspx?id=41684)


