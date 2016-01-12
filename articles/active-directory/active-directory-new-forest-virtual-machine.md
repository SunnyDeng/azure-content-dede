<properties 
	pageTitle="Installieren einer Active Directory-Gesamtstruktur in einem virtuellen Azure-Netzwerk | Microsoft Azure" 
	description="Ein Lernprogramm, in dem erläutert wird, wie Sie eine neue Active Directory-Gesamtstruktur auf einer virtuellen Maschine (VM) in einem virtuellen Azure-Netzwerk erstellen" 
	services="active-directory, virtual-network" 
	documentationCenter="" 
	authors="markusvi" 
	manager="stevenpo" 
	tags=""/>

<tags 
	ms.service="active-directory" 
	ms.devlang="na" 
	ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
	ms.date="10/20/2015" 
	ms.author="markusvi"/>


# Installieren einer neuen Active Directory-Gesamtstruktur auf einem virtuellen Azure-Netzwerk

In diesem Thema wird das Erstellen einer neuen Windows Server Active Directory-Umgebung in einem virtuellen Azure-Netzwerk erläutert, das auf einem virtuellen Computer (Virtual Machine, VM) mit [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) läuft. In diesem Fall ist das virtuelle Azure-Netzwerk nicht mit einem lokalen Netzwerk verbunden.

Folgende Themen könnten für Sie ebenfalls von Interesse sein:

- Ein Video, das diese Schritte veranschaulicht, finden Sie unter [Installieren einer neuen Active Directory-Gesamtstruktur in einem virtuellen Azure-Netzwerk](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network).
- Optional können Sie ein [Standort-zu-Standort-VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) konfigurieren und dann entweder eine neue Gesamtstruktur installieren oder eine lokale Gesamtstruktur in einem virtuellen Azure-Netzwerk erweitern. Erläuterungen hierzu finden Sie unter [Installieren eines Active Directory-Replikatdomänencontrollers unter virtuellen Netzwerken in Microsot Azure](../virtual-networks-install-replica-active-directory-domain-controller.md).
-  Weitere Anleitungen zu den Konzepten für die Installation von Active Directory-Domänendiensten (AD DS) auf einem virtuellen Azure-Netzwerk finden Sie unter [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Microsoft Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## Szenario (Diagramm)

In diesem Szenario müssen externe Benutzer auf Anwendungen zugreifen, die auf in die Domäne eingebundenen Servern ausgeführt werden. Die virtuellen Computer, auf denen die Anwendungsserver ausgeführt werden, und die virtuellen Computer, auf denen Domänencontroller ausgeführt werden, werden in einem virtuellen Azure-Netzwerk in einem eigenen Clouddienst ausgeführt. Sie sind auch zur Verbesserung der Fehlertoleranz in einer Verfügbarkeitsgruppe enthalten.

![][1] 7
## Unterschiede zwischen der virtuellen und der lokalen Umgebung

Die Installation eines Domänencontrollers in Azure unterscheidet sich nicht wesentlich von der lokalen Installation. Die Hauptunterschiede sind in der folgenden Tabelle aufgeführt.

Konfigurieren | Lokal | Azure Virtual Network	
------------- | -------------  | ------------
**IP-Adresse des Domänencontrollers** | Statische IP-Adresse in den Netzwerkadaptereigenschaften zuweisen | Ausführen des Cmdlets "Set-AzureStaticVNetIP" zum Zuweisen einer statischen IP-Adresse
**DNS-Clientauflösung** | Bevorzugte und alternative Adresse des DNS-Servers in den Netzwerkadaptereigenschaften von Domänenmitgliedern eingeben | Adresse des DNS-Servers in den Eigenschaften des virtuellen Netzwerks eingeben
**Speicherort der Active Directory-Datenbank** | Standardspeicherort C:\\ kann optional geändert werden | Standardspeicherort C:\\ muss geändert werden



## Erstellen eines virtuellen Azure-Netzwerks

1. Melden Sie sich am klassischen Azure-Portal an.
2. Erstellen Sie ein virtuelles Netzwerk. Klicken Sie auf **Netzwerke** > **Create a virtual network**. Geben Sie bei Ausführen des Assistenten die Werte aus der folgenden Tabelle ein. 

	Seite des Assistenten… | Einzugebende Werte
	------------- | -------------
	**Details zum virtuellen Netzwerk** | <p>Name: Geben Sie einen Namen für Ihr virtuelles Netzwerk ein.</p><p>Region: Wählen Sie die nächstgelegene Region.</p>
	**DNS und VPN** | <p>Lassen Sie das Feld für den DNS-Server leer.</p><p>Wählen Sie keine VPN-Option aus.</p>
	**Adressräume des virtuellen Netzwerks** | <p>Subnetzname: Geben Sie einen Namen für das Subnetz ein.</p><p>Start-IP: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



## Erstellen von virtuellen Computern (VMs) für die Serverrollen Domänencontroller und DNS
 
Wiederholen Sie die folgenden Schritte, um virtuelle Computer zum Hosten der DC-Rolle nach Bedarf zu erstellen. Sie sollten mindestens zwei virtuelle DC bereitstellen, um Fehlertoleranz und Redundanz zu gewährleisten. Wenn das virtuelle Azure-Netzwerk über mindestens zwei Domänencontroller verfügt, die auf ähnliche Weise konfiguriert sind (d. h. beide sind GCs, führen DNS-Server aus und beide führen keine FSMO-Rolle aus usw.), stellen Sie die virtuellen Computer, auf denen diese DCs ausgeführt werden, in eine Verfügbarkeitsgruppe, um eine verbesserte Fehlertoleranz zu erreichen.

Um die virtuellen Computer mithilfe von Windows PowerShell anstelle der Benutzeroberfläche zu erstellen, lesen Sie [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](../virtual-machines-ps-create-preconfigure-windows-vms.md).

1. Klicken Sie im klassischen Portal auf **Neu** > **Compute** > **Virtueller Computer** > **Aus Katalog**. Verwenden Sie die folgenden Werte, um den Assistenten abzuschließen. Übernehmen Sie den Standardwert für eine Einstellung, sofern kein anderer Wert empfohlen wird oder erforderlich ist.

    Seite des Assistenten… | Einzugebende Werte
	------------- | -------------
	**Image auswählen** | Windows Server 2012 R2 Datacenter
	**Konfiguration des virtuellen Computers** | <p>Name des virtuellen Computers: Geben Sie einen Namen mit einer einzelnen Bezeichnung ein (z. B. AzureDC1).</p><p>Neuer Benutzername: Geben Sie den Namen eines Benutzers ein. Dieser Benutzer wird Mitglied der lokalen Administratorgruppe auf dem virtuellen Computer. Sie benötigen diesen Namen, um sich zum ersten Mal bei dem virtuellen Computer anzumelden. Das integrierte Konto mit dem Namen "Administrator" funktioniert nicht.</p><p>Neues Kennwort/Bestätigen: Geben Sie ein Kennwort ein.</p>
	**Konfiguration des virtuellen Computers** | <p>Clouddienst: Wählen Sie <b>Einen neuen Clouddienst erstellen</b> für den ersten virtuellen Computer aus, und wählen Sie denselben Clouddienstnamen aus, wenn Sie weitere virtuelle Computer erstellen, die die DC-Rolle hosten.</p><p>DNS-Name des Clouddiensts: Geben Sie einen global eindeutigen Namen an.</p><p>Region/Affinitätsgruppe/Virtuelles Netzwerk: Geben Sie den Namen des virtuellen Netzwerks an (z. B. WestUSVNet).</p><p>Speicherkonto: Wählen Sie <b>Ein automatisch generiertes Speicherkonto verwenden</b> für den ersten virtuellen Computer aus, und wählen Sie dann denselben Speicherkontonamen aus, wenn Sie weitere virtuelle Computer erstellen, die die DC-Rolle hosten.</p><p>Verfügbarkeitsgruppe: Wählen Sie <b>Verfügbarkeitsgruppe erstellen</b> aus.</p><p>Verfügbarkeitsgruppenname: Geben Sie einen Namen für die Verfügbarkeitsgruppe ein, wenn Sie den ersten virtuellen Computer erstellen, und wählen Sie dann denselben Namen aus, wenn Sie weitere virtuelle Computer erstellen.</p>
	**Konfiguration des virtuellen Computers** | <p>Wählen Sie <b>VM-Agent installieren</b> und alle anderen erforderlichen Erweiterungen aus.</p>
2. Fügen Sie an jeden virtuellen Computer, der die DC-Serverrolle ausführt, einen Datenträger an. Der zusätzliche Speicherplatz ist erforderlich, um die AD-Datenbank, Protokolle und SYSVOL zu speichern. Geben Sie eine Größe für den Datenträger (z. B. 10 GB) an, und belassen Sie für **Hostcacheeinstellungen** die Einstellung **Keine**. Weitere Informationen finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer](../storage-windows-attach-disk.md).
3. Nachdem Sie sich erstmals auf dem virtuellen Computer angemeldet haben, öffnen Sie **Server-Manager** > **Datei- und Speicherdienste**, um auf diesem Datenträger ein Volume mit NTFS zu erstellen.
4. Reservieren Sie eine statische IP-Adresse für virtuelle Computer, auf denen die DC-Rolle ausgeführt wird. Laden Sie zum Reservieren einer statischen IP-Adresse den Microsoft-Webplattform-Installer herunter, [installieren Sie Azure PowerShell](../powershell-install-configure.md), und führen Sie anschließend das Cmdlet "Set-AzureStaticVNetIP" aus. Beispiel:

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

Weitere Informationen zum Festlegen einer statischen IP-Adresse finden Sie unter [Konfigurieren einer statischen internen IP-Adresse für einen virtuellen Computer](../virtual-network/virtual-networks-reserved-private-ip.md).

## Installieren von Windows Server Active Directory

Verwenden Sie dieselbe Routine zum [Installieren von AD DS](https://technet.microsoft.com/library/jj574166.aspx), die Sie lokal verwenden (d. h. Sie können die Benutzeroberfläche, eine Antwortdatei oder Windows PowerShell verwenden). Für die Installation einer neuen Gesamtstruktur brauchen Sie Administratoranmeldeinformationen. Damit die Active Directory-Datenbank, Protokolle und SYSVOL auf dem zusätzlich an den virtuellen Computer angeschlossenen Datenträger gespeichert werden können, müssen Sie den Standardspeicherort vom Betriebssystemlaufwerk auf den zusätzlichen Datenträger verlagern.

Wenn die DC-Installation beendet ist, schließen Sie den virtuellen Computer wieder an und melden sich beim DC an. Denken Sie daran, die Domänenanmeldeinformationen anzugeben.

## Zurücksetzen des DNS-Servers für das virtuelle Azure-Netzwerk

1. Setzen Sie die Einstellung für die DNS-Weiterleitung auf dem neuen Domänencontroller/DNS-Server zurück. 
  1. Klicken Sie im Server-Manager auf **Tools** > **DNS**. 
  2. Klicken Sie im **DNS-Manager** mit der rechten Maustaste auf den Namen des DNS-Servers, und klicken Sie dann auf **Eigenschaften**. 
  3. Klicken Sie auf der Registerkarte **Weiterleitungen** auf die IP-Adresse der Weiterleitung, und klicken Sie dann auf **Bearbeiten**. Wählen Sie die IP-Adresse aus, und klicken Sie auf **Löschen**. 
  4. Klicken Sie auf **OK**, um den Editor zu schließen, und dann erneut auf **OK**, um die Eigenschaften des DNS-Servers zu schließen. 
2. Aktualisieren Sie die DNS-Servereinstellung für das virtuelle Netzwerk. 
  1. Klicken Sie auf **Virtuelle Netzwerke**, doppelklicken Sie auf das erstellte virtuelle Netzwerk > **Konfigurieren** > **DNS-Server**. Geben Sie dann den Namen und die DIP eines der virtuellen Computer ein, der die Domänencontroller/DNS-Serverrolle ausführt, und klicken Sie abschließend auf **Speichern**. 
  2. Wählen Sie den virtuellen Computer aus, und klicken Sie auf **Neu starten**, damit der virtuelle Computer die DNS-Resolver-Einstellungen mit der IP-Adresse des neuen DNS-Servers konfiguriert. 


## Erstellen von virtuellen Computern für Domänenmitglieder

1. Wiederholen Sie die folgenden Schritte, um virtuelle Computer zu erstellen, die als Anwendungsserver ausgeführt werden. Übernehmen Sie den Standardwert für eine Einstellung, sofern kein anderer Wert empfohlen wird oder erforderlich ist.

	Seite des Assistenten… | Einzugebende Werte
	------------- | -------------
	**Image auswählen** | Windows Server 2012 R2 Datacenter
	**Konfiguration des virtuellen Computers** | <p>Name des virtuellen Computers: Geben Sie einen Namen mit einer einzelnen Bezeichnung ein (z. B. AppServer1).</p><p>Neuer Benutzername: Geben Sie den Namen eines Benutzers ein. Dieser Benutzer wird Mitglied der lokalen Administratorgruppe auf dem virtuellen Computer. Sie benötigen diesen Namen, um sich zum ersten Mal bei dem virtuellen Computer anzumelden. Das integrierte Konto mit dem Namen "Administrator" funktioniert nicht.</p><p>Neues Kennwort/Bestätigen: Geben Sie ein Kennwort ein.</p>
	**Konfiguration des virtuellen Computers** | <p>Clouddienst: Wählen Sie **Einen neuen Clouddienst erstellen** für den ersten virtuellen Computer aus, und wählen Sie denselben Clouddienstnamen aus, wenn Sie weitere virtuelle Computer erstellen, die die Anwendung hosten.</p><p>DNS-Name des Clouddiensts: Geben Sie einen global eindeutigen Namen an.</p><p>Region/Affinitätsgruppe/Virtuelles Netzwerk: Geben Sie den Namen des virtuellen Netzwerks an (z. B. WestUSVNet).</p><p>Speicherkonto: Wählen Sie **Ein automatisch generiertes Speicherkonto verwenden** für den ersten virtuellen Computer aus, und wählen Sie dann denselben Speicherkontonamen aus, wenn Sie weitere virtuelle Computer erstellen, die die Anwendung hosten.</p><p>Verfügbarkeitsgruppe: Wählen Sie **Verfügbarkeitsgruppe erstellen** aus.</p><p>Verfügbarkeitsgruppenname: Geben Sie einen Namen für die Verfügbarkeitsgruppe ein, wenn Sie den ersten virtuellen Computer erstellen, und wählen Sie dann denselben Namen aus, wenn Sie weitere virtuelle Computer erstellen.</p>
	**Konfiguration des virtuellen Computers** | <p>Wählen Sie <b>VM-Agent installieren</b> und alle anderen erforderlichen Erweiterungen aus.</p>
2. Nachdem alle virtuellen Computer bereitgestellt wurden, melden Sie sich an, und stellen eine Verbindung zur Domäne her. Klicken Sie im **Server-Manager** auf **Lokaler Server** > **ARBEITSGRUPPE** > **Ändern…**, wählen Sie dann **Domäne** aus, und geben Sie anschließend den Namen Ihrer lokalen Domäne ein. Geben Sie die Anmeldeinformationen eines Domänenbenutzers ein, und starten Sie den virtuellen Computer neu, um das Beitreten zu der Domäne abzuschließen.

Um die virtuellen Computer mithilfe von Windows PowerShell anstelle der Benutzeroberfläche zu erstellen, lesen Sie [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](../virtual-machines-ps-create-preconfigure-windows-vms.md).

Weitere Informationen zum Verwenden von Windows PowerShell finden Sie unter [Erste Schritte mit Azure Cmdlets](https://msdn.microsoft.com/library/azure/jj554332.aspx) und [Azure-Cmdlet-Referenz](https://msdn.microsoft.com/library/azure/jj554330.aspx).


## Siehe auch

-  [Installieren einer neuen Active Directory-Gesamtstruktur in einem virtuellen Azure-Netzwerk](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
-  [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Azure-Computern](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [Erstellen eines virtuellen Netzwerks komplett in der Cloud](../virtual-network/virtual-networks-create-vnet.md)
-  [Konfigurieren eines Standort-zu-Standort-VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)
-  [Installieren eines Active Directory-Replikatdomänencontrollers in einem virtuellen Azure-Netzwerk](../virtual-networks-install-replica-active-directory-domain-controller.md)
-  [Microsoft Azure IT Pro IaaS: (01) Grundlagen zu virtuellen Computern](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IT Pro IaaS: (05) Erstellen virtueller Netzwerke und Herstellen standortübergreifender Verbindungen](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Virtuelle Netzwerke – Übersicht](../virtual-network/virtual-networks-overview.md)
-  [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Azure-Cmdlet-Referenz](https://msdn.microsoft.com/library/azure/jj554330.aspx)
-  [Festlegen einer statischen IP-Adresse für einen virtuellen Computer in Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
-  [Zuweisen einer statischen IP zum virtuellen Azure-Computer](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
-  [Installieren einer neuen Active Directory-Gesamtstruktur](https://technet.microsoft.com/library/jj574166.aspx)
-  [Einführung in die Virtualisierung von Active Directory-Domänendiensten (AD DS) (Ebene 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png

 

<!---HONumber=Oct15_HO4-->