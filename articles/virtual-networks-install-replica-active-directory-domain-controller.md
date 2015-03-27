<properties 
	pageTitle="Installieren eines Replikatdomänencontrollers in Azure" 
	description="Ein Lernprogramm, in dem Sie erfahren, wie Sie einen Domänencontroller aus einer lokalen Active Directory-Gesamtstruktur auf einem virtuellen Azure-Computer installieren." 
	services="virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="Justinha"/>


# Installieren eines Active Directory-Replikatdomänencontrollers in einem virtuellen Azure-Netzwerk

In diesem Thema wird gezeigt, wie zusätzliche Domänencontroller (die auch als Replikatdomänencontroller bezeichnet werden) für eine lokale Active Directory-Domäne auf virtuellen Azure-Computern in einem virtuellen Azure-Netzwerk installiert werden. 

Folgende Themen könnten für Sie ebenfalls von Interesse sein:

- Sie können optional eine neue Active Directory-Gesamtstruktur in einem virtuellen Azure-Netzwerk installieren. Diese Schritte finden Sie unter [Installieren einer neuen Active Directory-Gesamtstruktur in einem virtuellen Azure-Netzwerk](http://azure.microsoft.com/documentation/articles/active-directory-new-forest-virtual-machine/).
-  Weitere Anleitungen zu den Konzepten für die Installation von Active Directory-Domänendiensten (AD DS) auf einem virtuellen Azure-Netzwerk finden Sie unter [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Azure-Computern](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).
-  Schrittweise Anleitungen, wie Sie auf Azure eine Testumgebung mit AD DS erstellen, finden Sie unter [Test Lab Guide: Basiskonfiguration in Azure](http://www.microsoft.com/download/details.aspx?id=41684).

## Szenario (Diagramm)

In diesem Szenario müssen externe Benutzer auf Anwendungen zugreifen, die auf in die Domäne eingebundenen Servern ausgeführt werden. Die virtuellen Computer, auf denen die Anwendungsserver und die Replikatdomänencontroller ausgeführt werden, sind in einem virtuellen Azure-Netzwerk installiert. Das virtuelle Netzwerk kann über eine [Site-to-Site-VPN](http://msdn.microsoft.com/library/azure/dn133795.aspx)-Verbindung, wie in der folgenden Abbildung dargestellt, mit dem lokalen Netzwerk verbunden werden, oder Sie können [ExpressRoute](http://azure.microsoft.com/services/expressroute/) für eine schnellere Verbindung verwenden. 

Die Anwendungsserver und Domänencontroller werden in separaten [Cloud-Diensten](http://azure.microsoft.com/documentation/articles/cloud-services-what-is/) bereitgestellt, um die Serververarbeitung zu verteilen, und in [Verfügbarkeitsgruppen](http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/) für eine bessere Fehlertoleranz. 
Die Domänencontroller werden durch die Active Directory-Replikation untereinander und mit lokalen Domänencontroller repliziert. Es sind keine Synchronisierungstools erforderlich.

![][1]

## Schritt 1: Erstellen eines Active Directory-Standorts für das virtuelle Netzwerk in Azure

Es bietet sich an, einen Standort in Active Directory zu erstellen, der die Netzwerkregion entsprechend dem virtuellen Netzwerk darstellt. Dadurch werden die Authentifizierung, Replikation sowie andere Vorgänge am Standort des Domänencontrollers optimiert. In den folgenden Schritten wird erläutert, wie ein Standort erstellt wird. Weitere Hintergrundinformationen erhalten Sie unter [Hinzufügen eines neuen Standorts](http://technet.microsoft.com/library/cc781496.aspx).

1. Öffnen Sie Active Directory-Standorte und -Dienste: **Server-Manager** > **Tools** > **Active Directory-Standorte und -Dienste**.
2. Erstellen Sie einen Standort, der die Region darstellt, in der Sie ein virtuelles Azure-Netzwerk erstellt haben: Klicken Sie auf **Standorte** > **Aktion** > **Neuer Standort** > geben Sie den Namen des neuen Standorts ein, z. B. Azure West > wählen Sie eine Standortverknüpfung > **OK**.
3. Erstellen Sie ein Subnetz, und ordnen Sie es dem neuen Standort zu: Doppelklicken Sie auf **Standorte** > klicken Sie mit der rechten Maustaste auf **Subnetze** > **Neues Subnetz** > geben Sie den IP-Adressbereich des virtuellen Netzwerks ein (z. B. 10.1.0.0/16 im Szenariodiagramm) > wählen Sie den neuen Azure-Standort aus > **OK**.

## Schritt 2: Erstellen eines virtuellen Azure-Netzwerks

<ol><li><p>Klicken Sie im Azure-Verwaltungsportal auf <b>Neu</b> > <b>Netzwerkdienste</b> > <b>Virtuelles Netzwerk</b> > <b>Benutzerdefiniert erstellen</b>, und verwenden Sie die folgenden Werte, um den Assistenten abzuschließen.</p>

<table style="width:100%">
<tr>
<td>Seite des Assistenten</td>
<td>Einzugebende Werte</td>
</tr>
<tr>
<td><b>Details zum virtuellen Netzwerk</b></td>
<td><ul><li>Name: Geben Sie einen Namen für das virtuelle Netzwerk ein, z. B. WestUSVNet.</li><li>Region: Dies ist der Azure-Standort, an dem sich das virtuelle Netzwerk befindet, z. B. Westen der USA. Dieser Standort kann nicht geändert werden, nachdem das virtuelle Netzwerk erstellt wurde.</li></ul>
</td>
</tr>
<tr>
<td><b>DNS-Server und VPN-Konnektivität</b></td>
<td><ul><li>DNS-Server: Geben Sie den Namen und die IP-Adresse von mindestens einem lokalen DNS-Server an.</li><li>Konnektivität: Wählen Sie <b>Site-to-Site-VPN konfigurieren</b> aus.</li><li>Lokales Netzwerk: Geben Sie ein neues lokales Netzwerk an.</li></ul>Bei Verwendung von ExpressRoute anstelle eines VPN finden Sie unter <a href="http://msdn.microsoft.com/library/azure/dn606306.aspx">Konfigurieren einer ExpressRoute-Verbindung über einen Exchange-Anbieter</a>.</td>
</tr>
<tr>
<td><b>Site-to-Site-Konnektivität</b></td>
<td><ul><li>Name: Geben Sie einen Namen für das lokale Netzwerk an.</li><li>IP-Adresse des VPN-Geräts: Geben Sie die öffentliche IP-Adresse des Geräts an, das mit dem virtuellen Netzwerk verbunden wird. Das VPN-Gerät kann sich nicht hinter einer NAT befinden.</li><li>Adresse: Geben Sie die Adressbereiche für das lokale Netzwerk an (z. B. 192.168.0.0/16 im Szenariodiagramm).</li></ul></td>
</tr>
<tr>
<td><b>Adressräume des virtuellen Netzwerks</b></td>
<td><ul><li>Adressraum: Geben Sie den IP-Adressbereich für virtuelle Computer an, die im virtuellen Azure-Netzwerk ausgeführt werden sollen (z. B. 10.1.0.0/16 im Szenariodiagramm). Dieser Adressbereich darf nicht mit den Adressbereichen des lokalen Netzwerks überlappen.</li><li>Subnetze: Geben Sie einen Namen und eine Adresse für ein Subnetz für die Anwendungsserver (z. B. Front-End, 10.1.1.0/24) und für die Domänencontroller an (z. B. Back-End, 10.1.2.0/24).</li><li>Klicken Sie auf <b>Gatewaysubnetz hinzufügen</b>.</li></ul></td>
</tr>
</table>
</li>
<li><p>Als Nächstes konfigurieren Sie das Gateway des virtuellen Netzwerks, um eine sichere Site-to-Site-VPN-Verbindung zu erstellen. Anweisungen finden Sie unter <a href = "http://msdn.microsoft.com/library/azure/jj156210.aspx">Konfigurieren eines Gateways eines virtuellen Netzwerks im Verwaltungsportal</a> .</p>
</li>
<li><p>Erstellen Sie die Site-to-Site-VPN-Verbindung zwischen dem neuen virtuellen Netzwerk und einem lokalen VPN-Gerät. Anweisungen finden Sie unter <a href = "http://msdn.microsoft.com/library/azure/jj156210.aspx">Konfigurieren eines Gateways eines virtuellen Netzwerks im Verwaltungsportal</a> .</p>
</li>
</ol>

## Schritt 3: Erstellen von virtuellen Azure-Computern für die DC-Rollen

<p>Wiederholen Sie die folgenden Schritte, um virtuelle Computer zum Hosten der DC-Rolle nach Bedarf zu erstellen. Sie sollten mindestens zwei virtuelle DC bereitstellen, um Fehlertoleranz und Redundanz zu gewährleisten. Wenn das virtuelle Azure-Netzwerk über mindestens zwei Domänencontroller verfügt, die auf ähnliche Weise konfiguriert sind (d. h. beide sind GCs, führen DNS-Server aus und beide führen keine FSMO-Rolle aus usw.), stellen Sie die virtuellen Computer, auf denen diese DCs ausgeführt werden, in eine Verfügbarkeitsgruppe, um eine verbesserte Fehlertoleranz zu erreichen.</p>


<ol><li><p>Klicken Sie im Azure-Verwaltungsportal auf <b>Neu</b> > <b>Compute</b> > <b>Virtueller Computer</b> > <b>Aus Katalog</b>. Verwenden Sie die folgenden Werte, um den Assistenten abzuschließen. Übernehmen Sie den Standardwert für eine Einstellung, sofern kein anderer Wert empfohlen wird oder erforderlich ist.</p>
<table style="width:100%">
<tr>
<td><b>Seite des Assistenten</b></td>
<td><b>Einzugebende Werte</b></td>
</tr>
<tr>
<td><b>Image auswählen</b></td>
<td>Windows Server 2012 R2 Datacenter</td>
</tr>
<tr>
<td><b>Konfiguration des virtuellen Computers</b></td>
<td><ul><li>Name des virtuellen Computers: Geben Sie einen Namen mit einer einzelnen Bezeichnung ein (z. B. AzureDC2).</li><li>Neuer Benutzername': Geben Sie den Namen eines Benutzers ein. Dieser Benutzer wird Mitglied der lokalen Administratorgruppe auf dem virtuellen Computer. Sie benötigen diesen Namen, um sich zum ersten Mal bei dem virtuellen Computer anzumelden. Das integrierte Konto mit dem Namen "Administrator" funktioniert nicht.</li><li>Neues Kennwort/Bestätigen: Geben Sie ein Kennwort ein.</li></ul></td>
</tr>
<tr>
<td><b>Konfiguration des virtuellen Computers</b></td>
<td><ul><li>Cloud-Dienst: Wählen Sie <b>Einen neuen Cloud-Dienst erstellen</b> für den ersten virtuellen Computer aus, und wählen Sie denselben Cloud-Dienstnamen aus, wenn Sie weitere virtuelle Computer erstellen, die die Anwendung hosten.</li><li>DNS-Name des Cloud-Diensts: Geben Sie einen global eindeutigen Namen an.</li><li>Region/Affinitätsgruppe/Virtuelles Netzwerk: Geben Sie den Namen des virtuellen Netzwerks an (z. B. WestUSVNet).</li><li>Speicherkonto: Wählen Sie <b>Ein automatisch generiertes Speicherkonto verwenden</b> für den ersten virtuellen Computer aus, und wählen Sie dann denselben Speicherkontonamen aus, wenn Sie weitere virtuelle Computer erstellen, die die DC-Rolle hosten.</li><li>Verfügbarkeitsgruppe: Wählen Sie <b>Verfügbarkeitsgruppe erstellen</b> aus.</li><li>Verfügbarkeitsgruppenname: Geben Sie einen Namen für die Verfügbarkeitsgruppe ein, wenn Sie den ersten virtuellen Computer erstellen, und wählen Sie dann denselben Namen aus, wenn Sie weitere virtuelle Computer erstellen.</li></ul></td>
</tr>
<tr>
<td><b>Konfiguration des virtuellen Computers</b></td>
<td>Wählen Sie <b>VM-Agent installieren</b> und alle anderen erforderlichen Erweiterungen aus.</td>
</tr>
</table>
</li>
<li><p>Fügen Sie an jeden virtuellen Computer, der die DC-Serverrolle ausführt, einen Datenträger an. Der zusätzliche Speicherplatz ist erforderlich, um die AD-Datenbank, Protokolle und SYSVOL zu speichern. Geben Sie eine Größe für den Datenträger (z. B. 10 GB) an, und belassen Sie für die <b>Hostcacheeinstellungen</b> die Einstellung <b>Keine</b>. Nachdem Sie sich zuerst auf dem virtuellen Computer angemeldet haben, öffnen Sie <b>Server-Manager</b> > <b>Datei- und Speicherdienste</b>, um auf diesem Datenträger ein Volume mit NTFS zu erstellen.</p></li>
<li><p>Reservieren Sie eine statische IP-Adresse für virtuelle Computer, auf denen die DC-Rolle ausgeführt wird. Um eine statische IP-Adresse zu reservieren, laden Sie den Microsoft-Webplattform-Installer herunter, <a href = "http://azure.microsoft.com/documentation/articles/install-configure-powershell/">installieren Azure PowerShell,</a> und führen das Cmdlet <a href = "http://msdn.microsoft.com/library/azure/dn630228.aspx">Set-AzureStaticVNetIP</a> aus.</p></li>

</ol>

## Schritt 4: Installieren von AD DS auf virtuellen Azure-Computern

Melden Sie sich bei einem virtuellen Computer an, und stellen Sie sicher, dass Sie über die Site-to-Site-VPN-Verbindung bzw. die ExpressRoute-Verbindung mit Ressourcen im lokalen Netzwerk verbunden sind. Installieren Sie dann AD DS auf den virtuellen Azure-Computern. Sie können denselben Prozess wie bei der Installation eines zusätzlichen Domänencontrollers im lokalen Netzwerk verwenden (UI, Windows PowerShell oder eine Antwortdatei). Stellen Sie beim Installieren von AD DS sicher, dass Sie das neue Volume für den Standort der AD-Datenbank, der Protokolle und von SYSVOL angeben. Wenn Sie Ihre Kenntnisse über die AD DS-Installation auffrischen möchten, finden Sie unter [Installieren von Active Directory-Domänendiensten (Stufe 100)](http://technet.microsoft.com/library/hh472162.aspx) oder [Installieren eines Windows Server 2012-Domänencontrollerreplikats in einer vorhandenen Domäne (Stufe 200)](http://technet.microsoft.com/library/jj574134.aspx) weitere Informationen.

## Schritt 5: Erneutes Konfigurieren des DNS-Servers für das virtuelle Netzwerk

<ol>
<li><p>Klicken Sie im Azure-Verwaltungsportal auf den Namen des virtuellen Netzwerks, und klicken Sie dann auf die Registerkarte <b>Konfigurieren</b>, um <a href = "http://msdn.microsoft.com/library/azure/dn275925.aspx">die IP-Adressen der DNS-Server für das virtuelle Netzwerk neu zu konfigurieren,</a> sodass die den Replikatdomänencontrollern zugewiesenen statischen IP-Adressen anstelle der IP-Adressen eines lokalen DNS-Servers verwendet werden. </p>
</li>
<li><p>Um sicherzustellen, dass alle virtuellen Computer des Replikatdomänencontroller im virtuellen Netzwerk für die Verwendung der DNS-Server im virtuellen Netzwerk konfiguriert sind, klicken Sie auf <b>Virtuelle Computer</b>, klicken Sie auf die Statusspalte für die einzelnen virtuellen Computer, und klicken Sie dann auf <b>Neustart</b>. Warten Sie, bis der virtuelle Computer den Status <b>Wird ausgeführt</b> anzeigt, bevor Sie versuchen, sich anzumelden. 
</p>
</li>
</ol>

## Schritt 6: Erstellen von virtuellen Computern für Anwendungsserver

<ol><li><p>Wiederholen Sie die folgenden Schritte, um virtuelle Computer zu erstellen, die als Anwendungsserver ausgeführt werden. Übernehmen Sie den Standardwert für eine Einstellung, sofern kein anderer Wert empfohlen wird oder erforderlich ist.</p>

<table style="width:100%">
<tr>
<td><b>Seite des Assistenten</b></td>
<td><b>Einzugebende Werte</b></td>
</tr>
<tr>
<td><b>Image auswählen</b></td>
<td>Windows Server 2012 R2 Datacenter</td>
</tr>
<tr>
<td><b>Konfiguration des virtuellen Computers</b></td>
<td><ul><li>Name des virtuellen Computers: Geben Sie einen Namen mit einer einzelnen Bezeichnung ein (z. B. TreyAppServer1).</li><li>Neuer Benutzername: Geben Sie den Namen eines Benutzers ein. Dieser Benutzer wird Mitglied der lokalen Administratorgruppe auf dem virtuellen Computer. Sie benötigen diesen Namen, um sich zum ersten Mal bei dem virtuellen Computer anzumelden. Das integrierte Konto mit dem Namen "Administrator" funktioniert nicht.</li><li>Neues Kennwort/Bestätigen:  Geben Sie ein Kennwort ein.</li></ul></td>
</tr>
<tr>
<td><b>Konfiguration des virtuellen Computers</b></td>
<td><ul><li>Cloud-Dienst: Wählen Sie "Einen neuen Cloud-Dienst erstellen" für den ersten virtuellen Computer aus, und wählen Sie denselben Cloud-Dienstnamen aus, wenn Sie weitere virtuelle Computer erstellen, die die Anwendung hosten.</li><li>DNS-Name des Cloud-Diensts: Geben Sie einen global eindeutigen Namen an.</li><li>Region/Affinitätsgruppe/Virtuelles Netzwerk: Geben Sie den Namen des virtuellen Netzwerks an (z. B. WestUSVNet).</li><li>Speicherkonto: Wählen Sie <b>Ein automatisch generiertes Speicherkonto verwenden</b> für den ersten virtuellen Computer aus, und wählen Sie dann denselben Speicherkontonamen aus, wenn Sie weitere virtuelle Computer erstellen, die die DC-Rolle hosten.</li><li>Verfügbarkeitsgruppe: Wählen Sie <b>Verfügbarkeitsgruppe erstellen</b> aus.</li><li>Verfügbarkeitsgruppenname: Geben Sie einen Namen für die Verfügbarkeitsgruppe ein, wenn Sie den ersten virtuellen Computer erstellen, und wählen Sie dann denselben Namen aus, wenn Sie weitere virtuelle Computer erstellen.</li></ul></td>
</tr>
<tr>
<td><b>Konfiguration des virtuellen Computers</b></td>
<td>Wählen Sie <b>VM-Agent installieren</b> und alle anderen erforderlichen Erweiterungen aus.</td>
</tr>
</table>


</li>
<li><p>Nachdem alle virtuellen Computer bereitgestellt wurden, melden Sie sich an, und stellen eine Verbindung zur Domäne her. Klicken Sie im <b>Server-Manager</b> auf <b>Lokaler Server</b> > <b>ARBEITSGRUPPE</b> > <b>Ändern...</b>, und wählen Sie dann <b>Domäne</b> aus, und geben Sie anschließend den Namen Ihrer lokalen Domäne ein. Geben Sie die Anmeldeinformationen eines Domänenbenutzers ein, und starten Sie den virtuellen Computer neu, um das Beitreten zu der Domäne abzuschließen.
</p>
</li>
</ol>
<p>
Als Alternative zur Verwendung des Verwaltungsportals für die Bereitstellung von virtuellen Computern, können Sie Windows PowerShell für Microsoft Azure verwenden. Verwenden Sie <a href = "http://msdn.microsoft.com/library/azure/dn495159.aspx">New-AzureVMConfig</a> - und <a href = "http://msdn.microsoft.com/library/azure/dn495299.aspx">Add-AzureProvisioningConfig,</a> um einen virtuellen Computer als in eine Domäne eingebundenen Computer bereitzustellen, wenn er zum ersten Mal gestartet wird. Verwenden Sie <a href = "http://msdn.microsoft.com/library/azure/dn495254.aspx">New-AzureVM,</a> um den virtuellen Computer selbst zu erstellen. 
</p>

Weitere Informationen zum Verwenden von Windows PowerShell finden Sie unter [Erste Schritte mit Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/jj156055.aspx) und [Azure-Cmdlets für die Verwaltung](http://msdn.microsoft.com/library/windowsazure/jj152841).


## Zusätzliche Ressourcen

-  [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Azure-Computern](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Hochladen vorhandener lokaler Hyper-V-Domänencontroller in Azure mithilfe von Azure PowerShell](http://support.microsoft.com/kb/2904015)

-  [Installieren einer neuen Active Directory-Gesamtstruktur auf einem virtuellen Azure-Netzwerk](http://azure.microsoft.com/documentation/articles/active-directory-new-forest-virtual-machine/)

-  [Azure Virtual Network](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [Azure IT Pro IaaS: (01) Grundlagen zu virtuellen Computern](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Microsoft Azure IT Pro IaaS: (05) Erstellen virtueller Netzwerke und Herstellen standortübergreifender Verbindungen](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/jj156055.aspx)

-  [Azure-Verwaltungs-Cmdlets](http://msdn.microsoft.com/library/windowsazure/jj152841)

<!--Image references-->
[1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png

<!--HONumber=47-->
