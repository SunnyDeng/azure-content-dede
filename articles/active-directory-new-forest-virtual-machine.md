<properties urlDisplayName="Active Directory forest" pageTitle="Installieren einer Active Directory-Gesamtstruktur auf einem virtuellen Azure-Netzwerk" metaKeywords="" description="A tutorial that explains how to create a new Active Directory forest on a virtual machine (VM) on an Azure Virtual Network." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="Justinha" />




#Installieren einer neuen Active Directory-Gesamtstruktur auf einem virtuellen Azure-Netzwerk

In diesem Thema wird das Erstellen einer neuen Windows Server Active Directory-Umgebung in einem virtuellen Azure-Netzwerk erläutert, das auf einem virtuellen Computer (Virtual Machine, VM) mit [Azure virtual network](http://msdn.microsoft.com/de-de/library/windowsazure/jj156007.aspx) läuft. In diesem Fall ist das virtuelle Azure-Netzwerk nicht mit einem lokalen Netzwerk verbunden. 

Folgende Themen könnten für Sie ebenfalls von Interesse sein:

- Optional können Sie [mit dem Assistenten im Verwaltungsportal ein Standort-zu-Standort-VPN konfigurieren](http://msdn.microsoft.com/de-de/library/windowsazure/dn133795.aspx) und dann entweder eine neue Gesamtstruktur installieren oder eine lokale Gesamtstruktur zu einem virtuellen Azure-Netzwerk erweitern. Erläuterungen hierzu finden Sie unter [Installieren eines Active Directory-Replikatdomänencontrollers unter virtuellen Netzwerken in Windows Azure](http://www.windowsazure.com/de-de/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/).
-  Weitere Anleitungen zu den Konzepten für die Installation von Active Directory-Domänendiensten (AD DS) auf einem virtuellen Azure-Netzwerk finden Sie unter [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Windows Azure](http://msdn.microsoft.com/de-de/library/windowsazure/jj156090.aspx).
-  Schrittweise Anleitungen zum Erstellen einer Testumgebung mit AD DS auf Azure finden Sie unter [Test Lab Guide: Windows Server 2012 R2-Basiskonfiguration auf Windows Azure](http://www.microsoft.com/de-de/download/details.aspx?id=41684).



##Inhaltsverzeichnis##

* [Unterschiede zwischen der virtuellen und der lokalen Umgebung](#differ)
* [Schritt 1: Erstellen eines virtuellen Netzwerks in Azure](#createvnet)
* [Schritt 2: Erstellen eines virtuellen Computers (VM) für die Serverrollen Domänencontroller und DNS](#createvm)
* [Schritt 3: Installieren von Windows Server Active Directory](#installad)
* [Schritt 4: Einrichten des DNS-Servers für das virtuelle Azure-Netzwerk](#dns)
* [Schritt 5: Erstellen von virtuellen Computern und Bereitstellen als Domänenmitglieder](#domainmembers)


<h2><a id="differ"></a>Unterschiede zwischen der virtuellen und der lokalen Umgebung</h2>
Die Installation eines Domänencontrollers in Azure unterscheidet sich nicht wesentlich von der lokalen Installation. Die Hauptunterschiede sind in der folgenden Tabelle aufgeführt. 

So konfigurieren Sie ...  | vor Ort  | ein virtuelles Azure-Netzwerk	
------------- | -------------  | ------------
**IP-Adresse für den Domänencontroller**  | Zuweisen einer statischen IP-Adresse in den Netzwerkadaptereigenschaften   | Ausführen des Set-AzureStaticVNetIP-Cmdlet zum Zuweisen einer statischen IP-Adresse
**DNS Client Resolver**  | Festlegen bevorzugter und alternativer DNS-Serveradressen in den Netzwerkadaptereigenschaften von Domänenmitgliedern   | Festlegen einer DNS-Serveradresse in den Eigenschaften des virtuellen Netzwerks
**Active Directory-Datenbankspeicher**  | Optionales Ändern des Standardspeicherorts von C:\  | Sie müssen den Standardspeicherort von C:\ ändern



<h2><a id="createvnet"></a>Schritt 1: Erstellen eines virtuellen Azure-Netzwerks</h2>
1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
2. Erstellen Sie ein virtuelles Netzwerk. Klicken Sie auf <b>Netzwerke</b> > <b>Create a virtual network</b>. Geben Sie beim Ausführen des Assistenten die Werte aus der folgenden Tabelle ein. 

	Auf dieser Seite des Assistenten ...  | Geben Sie diese Werte an
	------------- | -------------
	**Details zum virtuellen Netzwerk**  | <p>Name: Geben Sie einen Namen für Ihr virtuelles Netzwerk ein.</p><p>Region: Wählen Sie die nächstgelegene Region aus.</p>
	**DNS und VPN**  | <p>DNS-Server leer lassen</p><p>Keine VPN-Option auswählen</p>
	**Adressbereiche des virtuellen Netzwerks**  | <p>Subnetzname: Geben Sie einen Namen für das Subnetz ein.</p><p>Start-IP: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



<h2><a id="createvm"></a>Schritt 2: Erstellen eines virtuellen Computers (VM) für die Serverrollen Domänencontroller und DNS</h2>
 
1. Klicken Sie auf <b>Neu</b> > <b>Computer</b> > <b>Virtueller Computer</b> > <b>Aus Galerie</b>. 
2. Geben Sie bei Ausführen des Assistenten die Werte aus der folgenden Tabelle ein.

	Auf dieser Seite des Assistenten ...  | Geben Sie diese Werte an
	------------- | -------------
	**Betriebssystem**  | Wählen Sie **Windows Server 2012 R2 Datacenter** aus.
	**Konfiguration des virtuellen ComputersV**  | <p>Veröffentlichungsdatum: Heutiges Datum</p><p>Name des Computers: Geben Sie einen eindeutigen Wert ein.</p><p>Ebene: Standard</p><p>Größe: Wählen Sie eine beliebige Größe aus.</p><p>Benutzername: Geben Sie einen Namen ein. Dieses Benutzerkonto wird ein Mitglied der integrierten Gruppe "Administratoren" sein. </p><p>Kennwort: Muss mindestens 8 Zeichen lang sein und 3 der folgenden Zeichen enthalten: </p><ul><li>einen Großbuchstaben</li><li>einen Kleinbuchstaben</li><li>eine Zahl</li><li>ein Sonderzeichen</li></ul>
	**Cloud-Dienst**  | <p>Cloud-Dienst: <b>Erstellen Sie einen neuen Cloud-Dienst</b></p><p>Cloud-Dienst-Name: Übernehmen Sie den Standardwert</p><p>Region/AffinityGroup/VirtualNetwork: Wählen Sie das virtuelle Netzwerk aus, das Sie erstellt haben.</p><p>Subnetz des virtuellen Netzwerks: Wählen Sie das Subnetz aus, das Sie erstellt haben. </p><p>Speicherkonto: <b>Verwenden Sie ein automatisch generiertes Speicherkonto.</b></p><p>Verfügbarkeitsset: <b>Keins</b></p><p>Endpunkte: Übernehmen Sie die Standardwerte.</p>
	**VM-Agent**  | Wählen Sie **VM-Agent installieren** aus.

1. Die dynamische IP-Adresse, die dem virtuellen Computer standardmäßig zugewiesen wird, ist für die Dauer des Cloud-Dienstes gültig. Sie ändert sich jedoch, wenn der virtuelle Computer heruntergefahren wird. Sie können eine statische IP-Adresse zuweisen, indem Sie das [cmdlet "Set-AzureStaticVNetIP Azure PowerShell"](http://msdn.microsoft.com/de-de/library/windowsazure/dn630228.aspx) ausführen. Dann bleibt die IP-Adresse auch nach dem Herunterfahren des virtuellen Computers erhalten. 
2. Schließen Sie einen zusätzlichen Datenträger an den virtuellen Computer an, auf dem die Active Directory-Datenbank, Protokolle und SYSVOL gespeichert werden. 
  3. Klicken Sie auf <b>VM</b> > <b>Anfügen</b> > <b>Leeren Datenträger anfügen</b>. 
  4. Geben Sie eine Größe an (zum Beispiel 10 GB), und übernehmen Sie alle anderen Standardwerte.
3. Melden Sie sich bei dem virtuellen Computer an, und formatieren Sie den zusätzlichen Datenträger. 
  4. Klicken Sie auf <b>Verbinden</b>, um sich bei dem virtuellen Computer anzumelden, klicken Sie auf <b>Öffnen</b>, um eine RDP-Sitzung zu erstellen, und klicken Sie nochmals auf <b>Verbinden</b>.
  4. Geben Sie als Anmeldeinformationen den neuen Benutzernamen und das neue Kennwort ein.
  5. Klicken Sie im Server-Manager auf   5. Tools<b> > </b>Computerverwaltung<b>. 
  6. Klicken Sie auf   6. Datenträgerverwaltung<b> und dann auf </b>OK<b>, um den neuen Datenträger zu initialisieren. 
  6. Klicken Sie mit der rechten Maustaste auf den Datenträgernamen und dann auf   6. Neues einfaches Volume</b>. Beenden Sie den Assistenten mit der Formatierung des neuen Laufwerks. 

<h2><a id="installad"></a>Schritt 3: Installieren von Windows Server Active Directory</h2>
[Installieren Sie AD DS](http://technet.microsoft.com/library/jj574166.aspx) genauso wie in einer lokalen Umgebung (also über die Benutzeroberfläche, eine Antwortdatei oder Windows PowerShell). Für die Installation einer neuen Gesamtstruktur brauchen Sie Administratoranmeldeinformationen. Damit die Active Directory-Datenbank, Protokolle und SYSVOL auf dem zusätzlich an den virtuellen Computer angeschlossenen Datenträger gespeichert werden können, müssen Sie den Standardspeicherort vom Betriebssystemlaufwerk auf den zusätzlichen Datenträger verlagern. 
<p>Wenn die DC-Installation beendet ist, schließen Sie den virtuellen Computer wieder an und melden sich beim DC an. Denken Sie daran, die Domänenanmeldeinformationen anzugeben.</p>

<h2><a id="dns"></a>Schritt 4: Einrichten des DNS-Servers für das virtuelle Azure-Netzwerk</h2>
1. Klicken Sie auf 1. Virtuelle Netzwerke<b>, doppelklicken Sie auf das virtuelle Netzwerk, das Sie erstellt haben, und klicken Sie dann auf <b>Konfigurieren</b>. 
2. Geben Sie unter 2. DNS-Server<b> den Namen und die DIP des DC ein, und klicken Sie auf <b>Speichern</B>. 
3. Wählen Sie den virtuellen Computer aus und klicken Sie auf <b>Neu starten</b>, damit der virtuelle Computer die DNS-Resolver-Einstellungen mit der IP-Adresse des neuen DNS-Servers konfiguriert. 


<h2><a id="domainmembers"></a>Schritt 5: Erstellen von virtuellen Computern und Bereitstellen als Domänenmitglieder</h2>
<p>Erstellen Sie zusätzliche virtuelle Computer, und stellen Sie diese als Domänenmitglieder bereit. Sie können die Benutzeroberfläche oder Azure PowerShell verwenden. Wenn Sie die Benutzeroberfläche verwenden, führen Sie die gleichen Schritte aus wie zur Erstellung des ersten virtuellen Computers. Binden Sie die virtuellen Computer dann ebenso in die Domäne ein wie bei einer lokalen Installation. Wenn Sie Azure PowerShell verwenden, können Sie virtuelle Computer bereitstellen und diese dann beim ersten Hochfahren in die Domäne aufnehmen. </p><p>In diesem Beispiel wird ein in die Domäne eingebundener virtueller Computer mit dem Namen DC2 erstellt, auf dem Windows Server 2012 R2 Datacenter läuft. Melden Sie sich nach der Bereitstellung von DC2 als Domänenadministrator an diesem virtuellen Computer an, und stufen Sie ihn zum Replikatdomänencontroller herauf. </p><p>Sie können "Get-AzureVMImage" ausführen, um Imagenamen abzurufen. Wenn beispielsweise eine Liste von Images für Windows Server 2012 R2 zurückgegeben werden soll, führen Sie Folgendes aus: Get-AzureVMImage | where-object {$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}.</p>
	'

	cls

	Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
	Select-AzureSubscription -SubscriptionName "Free Trial"

	#Deploy a new VM and join it to the domain
	#-------------------------------------------
	#Specify my DC's DNS IP (10.0.0.4)
	$myDNS = New-AzureDNS -Name 'DC1' -IPAddress '10.0.0.4'
	
	# OS Image to Use
	$image = 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd'
	$service = 'DC2'
	$vnet = 'YourVirtualNetwork'
	$pwd = 'P@ssw0rd'
	$size = 'Small'

	#VM Configuration
	$vmname = 'DC2'
	$MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

	New-AzureVM -ServiceName $service -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Wenn Sie das Skript erneut ausführen, müssen Sei für $service einen eindeutigen Wert eingeben. Mit "Test-AzureName -Service <i>Dienstname</i>" können Sie feststellen, ob der Name bereits vergeben ist. 	

## Weitere Informationen

-  [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Windows Azure](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Konfigurieren eines virtuellen Nur-Cloud-Netzwerks im Verwaltungsportal](http://msdn.microsoft.com/de-de/library/dn631643.aspx)

-  [Konfigurieren eines Standort-zu-Standort-VPNs im Verwaltungsportal](http://msdn.microsoft.com/de-de/library/dn133795.aspx)

-  [Installieren eines Active Directory-Replikatdomänencontrollers in Windows Azure](http://azure.microsoft.com/de-de/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/)

-  [Windows Azure IT Pro IaaS: (01) Grundlagen zu virtuellen Computern](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Windows Azure IT Pro IaaS: (05) Erstellen virtueller Netzwerke und Herstellen standortübergreifender Verbindungen](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Azure Virtual Network](http://msdn.microsoft.com/de-de/library/windowsazure/jj156007.aspx)

-  [Installieren und Konfigurieren von Azure PowerShell](http://www.windowsazure.com/de-de/documentation/articles/install-configure-powershell/)

-  [Windows Azure PowerShell](http://msdn.microsoft.com/de-de/library/windowsazure/jj156055.aspx)

-  [Windows Azure-Verwaltungs-Cmdlets](http://msdn.microsoft.com/de-de/library/windowsazure/jj152841)

-  [Festlegen einer statischen IP-Adresse für einen virtuellen Computer in Azure ](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)

-  [How to assign Static IP to Azure VM (in englischer Sprache)](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)

-  [Installieren einer neuen Active Directory-Gesamtstruktur](http://technet.microsoft.com/library/jj574166.aspx)

-  [Einführung in die Virtualisierung von Active Directory-Domänendiensten (AD DS) (Ebene 100).](http://technet.microsoft.com/de-de/library/hh831734.aspx)

-  [Test Lab Guide: Windows Server 2012 R2-Basiskonfiguration auf Windows Azure](http://www.microsoft.com/de-de/download/details.aspx?id=41684)

