<properties linkid="manage-services-networking-active-directory-forest" urlDisplayName="Active Directory forest" pageTitle="Install an Active Directory forest on an Azure virtual network" metaKeywords="" description="A tutorial that explains how to create a new Active Directory forest on a virtual machine (VM) on an Azure Virtual Network." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha"></tags>

# Installieren einer neuen Active Directory-Gesamtstruktur auf einem virtuellen Azure-Netzwerk

In diesem Thema wird das Erstellen einer neuen Windows Server Active Directory-Umgebung in einem virtuellen Azure-Netzwerk erläutert, das auf einem virtuellen Computer (Virtual Machine, VM) mit [Azure Virtual Network][Azure Virtual Network] läuft. In diesem Fall ist das virtuelle Azure-Netzwerk nicht mit einem lokalen Netzwerk verbunden.

Folgende Themen könnten für Sie ebenfalls von Interesse sein:

-   Optional können Sie [mit dem Assistenten im Verwaltungsportal ein Standort-zu-Standort-VPN konfigurieren][mit dem Assistenten im Verwaltungsportal ein Standort-zu-Standort-VPN konfigurieren] und dann entweder eine neue Gesamtstruktur installieren oder eine lokale Gesamtstruktur zu einem virtuellen Azure-Netzwerk erweitern. Erläuterungen hierzu finden Sie unter [Installieren eines Active Directory-Replikatdomänencontrollers unter virtuellen Netzwerken in Windows Azure][Installieren eines Active Directory-Replikatdomänencontrollers unter virtuellen Netzwerken in Windows Azure].
-   Weitere Anleitungen zu den Konzepten für die Installation von Active Directory-Domänendiensten (AD DS) auf einem virtuellen Azure-Netzwerk finden Sie unter [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Windows Azure][Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Windows Azure].
-   Schrittweise Anleitungen, wie Sie auf Azure eine Testumgebung mit AD DS erstellen, finden Sie unter [Test Lab Guide: Windows Server 2012 R2-Basiskonfiguration auf Windows Azure][Test Lab Guide: Windows Server 2012 R2-Basiskonfiguration auf Windows Azure].

## Inhaltsverzeichnis

-   [Unterschiede zwischen der virtuellen und der lokalen Umgebung][Unterschiede zwischen der virtuellen und der lokalen Umgebung]
-   [Schritt 1: Erstellen eines virtuellen Netzwerks in Azure][Schritt 1: Erstellen eines virtuellen Netzwerks in Azure]
-   [Schritt 2: Erstellen eines virtuellen Computers (VM) für die Serverrollen Domänencontroller und DNS][Schritt 2: Erstellen eines virtuellen Computers (VM) für die Serverrollen Domänencontroller und DNS]
-   [Schritt 3: Installieren von Windows Server Active Directory][Schritt 3: Installieren von Windows Server Active Directory]
-   [Schritt 4: Einrichten des DNS-Servers für das virtuelle Azure-Netzwerk][Schritt 4: Einrichten des DNS-Servers für das virtuelle Azure-Netzwerk]
-   [Schritt 5: Erstellen von virtuellen Computern und Bereitstellen als Domänenmitglieder][Schritt 5: Erstellen von virtuellen Computern und Bereitstellen als Domänenmitglieder]

## <span id="differ"></span></a>Unterschiede zwischen der virtuellen und der lokalen Umgebung

Die Installation eines Domänencontrollers in Azure unterscheidet sich nicht wesentlich von der lokalen Installation. Die Hauptunterschiede sind in der folgenden Tabelle aufgeführt.

| Konfigurieren                                  | Lokal                                                                                                                  | Virtuelles Azure-Netzwerk                                                                                                |
|------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|
| **IP-Adresse des Domänencontrollers**          | Statische IP-Adresse in den Netzwerkadaptereigenschaften zuweisen                                                      | IP-Adresse über DHCP abrufen und durch Ausführen des cmdlets "Set-AzureStaticVNetIP" als statische Adresse konfigurieren |
| **DNS-Client-Resolver**                        | Bevorzugte und alternative Adresse des DNS-Servers in den Netzwerkadaptereigenschaften von Domänenmitgliedern eingeben | Adresse des DNS-Servers in den Eigenschaften des virtuellen Netzwerks eingeben                                           |
| **Speicherort der Active Directory-Datenbank** | Standardspeicherort C:\\ kann optional geändert werden                                                                 | Standardspeicherort C:\\ muss geändert werden                                                                            |

## <span id="createvnet"></span></a>Schritt 1: Erstellen eines virtuellen Azure-Netzwerks

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Erstellen Sie ein virtuelles Netzwerk. Klicken Sie auf **Netzwerke** \> **Create a virtual network**. Geben Sie bei Ausführen des Assistenten die Werte aus der folgenden Tabelle ein.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Seite des Assistenten…</th>
    <th align="left">Einzugebende Werte</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Details zum virtuellen Netzwerk</strong></td>
    <td align="left"><p>Name: Geben Sie einen Namen für Ihr virtuelles Netzwerk ein.</p>
    <p>Region: Wählen Sie die nächstgelegene Region aus.</p>
    <p>Affinitätsgruppe: <strong>Neue Affinitätsgruppe erstellen</strong></p>
    <p>Affinitätsgruppenname: Geben Sie einen Namen für Ihre Affinitätsgruppe ein.</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>DNS und VPN</strong></td>
    <td align="left"><p>Lassen Sie das Feld für den DNS-Server leer.</p>
    <p>Wählen Sie keine VPN-Option aus.</p></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>Adressräume des virtuellen Netzwerks</strong></td>
    <td align="left"><p>Subnetzname: Geben Sie einen Namen für das Subnetz ein.</p>
    <p>Starting IP: <strong>10.0.0.0</strong></p>
    <p>CIDR: <strong>/24 (256)</strong></p></td>
    </tr>
    </tbody>
    </table>

## <span id="createvm"></span></a>Schritt 2: Erstellen eines virtuellen Computers (VM) für die Serverrollen Domänencontroller und DNS

1.  Klicken Sie auf **Neu** \> **Berechnen** \> **Virtueller Computer** \> **Aus Katalog**.
2.  Geben Sie bei Ausführen des Assistenten die Werte aus der folgenden Tabelle ein.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Seite des Assistenten…</th>
    <th align="left">Einzugebende Werte</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Betriebssystem</strong></td>
    <td align="left">Wählen Sie <strong>Windows Server 2012 R2 Datacenter</strong> aus.</td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Konfiguration des virtuellen Computers</strong></td>
    <td align="left"><p>Veröffentlichungsdatum: Das heutige Datum</p>
    <p>Computername: Geben Sie einen eindeutigen Wert ein.</p>
    <p>Ebene: Standard</p>
    <p>Größe: Wählen Sie eine beliebige Größe aus.</p>
    <p>Benutzername: Geben Sie einen Namen ein. Dieses Benutzerkonto wird ein Mitglied der integrierten Gruppe &quot;Administratoren&quot; sein.</p>
    <p>Kennwort: Muss mindestens 8 Zeichen lang sein und 3 der folgenden Zeichentypen enthalten:</p>
    <ul>
    <li>einen Großbuchstaben</li>
    <li>einen Kleinbuchstaben</li>
    <li>eine Zahl</li>
    <li>ein Sonderzeichen</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>Cloud-Dienst</strong></td>
    <td align="left"><p>Cloud-Dienst: <strong>Einen neuen Cloud-Dienst erstellen</strong></p>
    <p>Name des Cloud-Diensts: Übernehmen Sie den Standardwert.</p>
    <p>Region/Affinitätsgruppe/Virtuelles Netzwerk: Wählen Sie das virtuelle Netzwerk aus, das Sie erstellt haben.</p>
    <p>Subnetz des virtuellen Netzwerks: Wählen Sie das Subnetz aus, das Sie erstellt haben.</p>
    <p>Speicherkonto: <strong>Ein automatisch generiertes Speicherkonto verwenden</strong></p>
    <p>Verfügbarkeitsgruppe: <strong>None</strong></p>
    <p>Endpoints: Übernehmen Sie die Standardwerte.</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>VM-Agent</strong></td>
    <td align="left">Wählen Sie <strong>VM-Agent installieren</strong> aus.</td>
    </tr>
    </tbody>
    </table>

3.  Die dynamische IP-Adresse, die dem virtuellen Computer standardmäßig zugewiesen wird, ist für die Dauer des Cloud-Dienstes gültig. Sie ändert sich jedoch, wenn der virtuelle Computer heruntergefahren wird. Sie können eine statische IP-Adresse zuweisen, indem Sie das [cmdlet "Set-AzureStaticVNetIP Azure PowerShell"][cmdlet "Set-AzureStaticVNetIP Azure PowerShell"] ausführen. Dann bleibt die IP-Adresse auch nach dem Herunterfahren des virtuellen Computers erhalten.
4.  Schließen Sie einen zusätzlichen Datenträger an den virtuellen Computer an, auf dem die Active Directory-Datenbank, Protokolle und SYSVOL gespeichert werden.
5.  Klicken Sie auf **VM** \> **Anfügen** \> **Leeren Datenträger anfügen**.
6.  Geben Sie eine Größe an (zum Beispiel 10 GB), und übernehmen Sie alle anderen Standardwerte.
7.  Melden Sie sich bei dem virtuellen Computer an, und formatieren Sie den zusätzlichen Datenträger.
8.  Klicken Sie auf **Verbinden**, um sich bei dem virtuellen Computer anzumelden, klicken Sie auf **Öffnen**, um eine RDP-Sitzung zu erstellen, und klicken Sie nochmals auf **Verbinden**.
9.  Geben Sie als Anmeldeinformationen den neuen Benutzernamen und das neue Kennwort ein.
10. Klicken Sie im Server-Manager auf **Tools** \> **Computerverwaltung**.
11. Klicken Sie auf **Datenträgerverwaltung** und dann auf **OK**, um den neuen Datenträger zu initialisieren.
12. Klicken Sie mit der rechten Maustaste auf den Datenträgernamen und dann auf **Neues einfaches Volume**. Beenden Sie den Assistenten mit der Formatierung des neuen Laufwerks.

## <span id="installad"></span></a>Schritt 3: Installieren von Windows Server Active Directory

[Installieren Sie AD DS][Installieren Sie AD DS] genauso wie in einer lokalen Umgebung (also über die Benutzeroberfläche, eine Antwortdatei oder Windows PowerShell). Für die Installation einer neuen Gesamtstruktur brauchen Sie Administratoranmeldeinformationen. Damit die Active Directory-Datenbank, Protokolle und SYSVOL auf dem zusätzlich an den virtuellen Computer angeschlossenen Datenträger gespeichert werden können, müssen Sie den Standardspeicherort vom Betriebssystemlaufwerk auf den zusätzlichen Datenträger verlagern.

Wenn die DC-Installation beendet ist, schließen Sie den virtuellen Computer wieder an und melden sich beim DC an. Denken Sie daran, die Domänenanmeldeinformationen anzugeben.

</p>
## <span id="dns"></span></a>Schritt 4: Einrichten des DNS-Servers für das virtuelle Azure-Netzwerk

1.  Klicken Sie auf **Virtuelle Netzwerke**, doppelklicken Sie auf das virtuelle Netzwerk, das Sie erstellt haben, und klicken Sie dann auf **Konfigurieren**.
2.  Geben Sie unter **DNS-Server** den Namen und die DIP des DC ein, und klicken Sie auf **Speichern**.
3.  Wählen Sie den virtuellen Computer aus, und klicken Sie auf **Neu starten**, damit der virtuelle Computer die DNS-Resolver-Einstellungen mit der IP-Adresse des neuen DNS-Servers konfiguriert.

## <span id="domainmembers"></span></a>Schritt 5: Erstellen von virtuellen Computern und Bereitstellen als Domänenmitglieder

Erstellen Sie zusätzliche virtuelle Computer, und stellen Sie diese als Domänenmitglieder bereit. Sie können die Benutzeroberfläche oder Azure PowerShell verwenden. Wenn Sie die Benutzeroberfläche verwenden, führen Sie die gleichen Schritte aus wie zur Erstellung des ersten virtuellen Computers. Binden Sie die virtuellen Computer dann ebenso in die Domäne ein wie bei einer lokalen Installation. Wenn Sie Azure PowerShell verwenden, können Sie virtuelle Computer bereitstellen und diese dann beim ersten Hochfahren in die Domäne aufnehmen.

In diesem Beispiel wird ein in die Domäne eingebundener virtueller Computer mit dem Namen DC2 erstellt, auf dem Windows Server 2012 R2 Datacenter läuft. Melden Sie sich nach der Bereitstellung von DC2 als Domänenadministrator an diesem virtuellen Computer an, und stufen Sie ihn zum Replikatdomänencontroller herauf.

Sie können "Get-AzureVMImage" ausführen, um Imagenamen abzurufen. Wenn beispielsweise eine Liste von Images für Windows Server 2012 R2 zurückgegeben werden soll, führen Sie Folgendes aus: Get-AzureVMImage | where-object {$\_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}.

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
    $AG = 'YourAffinityGroup'
    $vnet = 'YourVirtualNetwork'
    $pwd = 'P@ssw0rd'
    $size = 'Small'
    #VM Configuration
    $vmname = 'DC2'
    $MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 
    New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

Wenn Sie das Skript erneut ausführen, müssen Sei für $service einen eindeutigen Wert eingeben. Mit "Test-AzureName -Service *Dienstname*" können Sie feststellen, ob der Name bereits vergeben ist.

## Weitere Informationen

-   [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Windows Azure][1]

-   [Konfigurieren eines virtuellen Nur-Cloud-Netzwerks im Verwaltungsportal][Konfigurieren eines virtuellen Nur-Cloud-Netzwerks im Verwaltungsportal]

-   [Konfigurieren eines Standort-zu-Standort-VPNs im Verwaltungsportal][Konfigurieren eines Standort-zu-Standort-VPNs im Verwaltungsportal]

-   [Installieren eines Active Directory-Replikatdomänencontrollers in Windows Azure][Installieren eines Active Directory-Replikatdomänencontrollers in Windows Azure]

-   [Windows Azure IT Pro IaaS: (01) Grundlagen zu virtuellen Computern][Windows Azure IT Pro IaaS: (01) Grundlagen zu virtuellen Computern]

-   [Windows Azure IT Pro IaaS: (05) Erstellen virtueller Netzwerke und Herstellen standortübergreifender Verbindungen][Windows Azure IT Pro IaaS: (05) Erstellen virtueller Netzwerke und Herstellen standortübergreifender Verbindungen]

-   [Azure Virtual Network][Azure Virtual Network]

-   [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell]

-   [Windows Azure PowerShell][Windows Azure PowerShell]

-   [Windows Azure-Verwaltungs-Cmdlets][Windows Azure-Verwaltungs-Cmdlets]

-   [Einrichten einer statischen IP-Adresse für einen virtuellen Computer in Azure][Einrichten einer statischen IP-Adresse für einen virtuellen Computer in Azure]

-   [How to assign Static IP to Azure VM (in englischer Sprache)][How to assign Static IP to Azure VM (in englischer Sprache)]

-   [Installieren einer neuen Active Directory-Gesamtstruktur][Installieren Sie AD DS]

-   [Einführung in die Virtualisierung von Active Directory-Domänendiensten (AD DS) (Ebene 100).][Einführung in die Virtualisierung von Active Directory-Domänendiensten (AD DS) (Ebene 100).]

-   [Test Lab Guide: Windows Server 2012 R2-Basiskonfiguration auf Windows Azure][Test Lab Guide: Windows Server 2012 R2-Basiskonfiguration auf Windows Azure]

  [Azure Virtual Network]: http://msdn.microsoft.com/de-de/library/windowsazure/jj156007.aspx
  [mit dem Assistenten im Verwaltungsportal ein Standort-zu-Standort-VPN konfigurieren]: http://msdn.microsoft.com/de-de/library/windowsazure/dn133795.aspx
  [Installieren eines Active Directory-Replikatdomänencontrollers unter virtuellen Netzwerken in Windows Azure]: http://www.windowsazure.com/de-de/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Windows Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/jj156090.aspx
  [Test Lab Guide: Windows Server 2012 R2-Basiskonfiguration auf Windows Azure]: http://www.microsoft.com/de-de/download/details.aspx?id=41684
  [Unterschiede zwischen der virtuellen und der lokalen Umgebung]: #differ
  [Schritt 1: Erstellen eines virtuellen Netzwerks in Azure]: #createvnet
  [Schritt 2: Erstellen eines virtuellen Computers (VM) für die Serverrollen Domänencontroller und DNS]: #createvm
  [Schritt 3: Installieren von Windows Server Active Directory]: #installad
  [Schritt 4: Einrichten des DNS-Servers für das virtuelle Azure-Netzwerk]: #dns
  [Schritt 5: Erstellen von virtuellen Computern und Bereitstellen als Domänenmitglieder]: #domainmembers
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  [cmdlet "Set-AzureStaticVNetIP Azure PowerShell"]: http://msdn.microsoft.com/de-de/library/windowsazure/dn630228.aspx
  [Installieren Sie AD DS]: http://technet.microsoft.com/library/jj574166.aspx
  [1]: http://msdn.microsoft.com/library/azure/jj156090.aspx
  [Konfigurieren eines virtuellen Nur-Cloud-Netzwerks im Verwaltungsportal]: http://msdn.microsoft.com/de-de/library/dn631643.aspx
  [Konfigurieren eines Standort-zu-Standort-VPNs im Verwaltungsportal]: http://msdn.microsoft.com/de-de/library/dn133795.aspx
  [Installieren eines Active Directory-Replikatdomänencontrollers in Windows Azure]: http://azure.microsoft.com/de-de/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [Windows Azure IT Pro IaaS: (01) Grundlagen zu virtuellen Computern]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01
  [Windows Azure IT Pro IaaS: (05) Erstellen virtueller Netzwerke und Herstellen standortübergreifender Verbindungen]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05
  [Installieren und Konfigurieren von Azure PowerShell]: http://www.windowsazure.com/de-de/documentation/articles/install-configure-powershell/
  [Windows Azure PowerShell]: http://msdn.microsoft.com/de-de/library/windowsazure/jj156055.aspx
  [Windows Azure-Verwaltungs-Cmdlets]: http://msdn.microsoft.com/de-de/library/windowsazure/jj152841
  [Einrichten einer statischen IP-Adresse für einen virtuellen Computer in Azure]: http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address
  [How to assign Static IP to Azure VM (in englischer Sprache)]: http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/
  [Einführung in die Virtualisierung von Active Directory-Domänendiensten (AD DS) (Ebene 100).]: http://technet.microsoft.com/de-de/library/hh831734.aspx
