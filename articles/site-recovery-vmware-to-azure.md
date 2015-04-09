<properties 
	pageTitle="Einrichten von Schutz zwischen lokalen virtuellen VMware-Maschinen oder physischen Servern und Azure" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung von virtuellen Maschinen auf lokalen VMware-Servern nach Azure und zwischen physischen lokalen Servern und Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/26/2015" 
	ms.author="raynew"/>


# Einrichten von Schutz zwischen lokalen virtuellen VMware-Maschinen oder physischen Servern und Azure

Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server aufeinander abgestimmt werden. Informieren Sie sich über mögliche Bereitstellungsszenarien in der [Übersicht über die Azure Site Recovery](hyper-v-recovery-manager-overview.md).

In dieser exemplarischen Vorgehensweise wird beschrieben, wie Site Recovery zu folgenden Zwecken bereitgestellt wird:

- **Schützen lokaler virtueller VMware-Maschinen in Azure**
- **Schützen lokaler physischer Windows- und Linux-Server in Azure**

Unternehmen profitieren von folgenden Vorteilen:

- Schutz physischer Windows- oder Linux-Server.
- Einfache Replikation, Failover und Wiederherstellung über das Azure Site Recovery-Portal.
- Datenreplikation über das Internet, eine VPN-Verbindung zwischen den Standorten oder über Azure ExpressRoute.   
- Failback (Wiederherstellung) von Azure zu einer lokalen VMware-Infrastruktur. 
- Vereinfachte Erkennung virtueller VMware-Maschinen.
- Konsistenz zwischen mehreren virtuellen Computern, sodass virtuelle Computer und physische Server mit bestimmten Arbeitsauslastungen zusammen an einem konsistenten Datenpunkt wiederhergestellt werden können.
- Wiederherstellungspläne für vereinfachtes Failover und Wiederherstellung von Arbeitsauslastungen, die über mehrere Computer mehrstufig verteilt sind.

Diese Funktion steht derzeit als Vorschau zur Verfügung. Lesen Sie die [ergänzenden Nutzungsbedingungen für Vorschauen](preview-supplemental-terms).

## Zu diesem Handbuch

Dieses Handbuch enthält eine Übersicht und Voraussetzungen für die Bereitstellung. Es führt Sie durch die Einrichtung aller Bereitstellungskomponenten und durch die Aktivierung des Schutzes für virtuelle Computer und Server. Zum Schluss wird das Failover getestet, um sicherzustellen, dass alles wie erwartet funktioniert.

Wenn Probleme auftreten, stellen Sie Ihre Fragen im [Azure Recovery Services-Forum](http://go.microsoft.com/fwlink/?LinkId=313628).

## Übersicht

Dieses Diagramm veranschaulicht die Bereitstellungskomponenten.

![New vault](./media/site-recovery-vmware-to-azure/ASRVMWare_Arch.png)

### Bereitstellungskomponenten

- **Lokale Computer** - Ihr lokaler Standort verfügt über Computer, die Sie schützen möchten. Hierbei handelt es sich entweder um virtuelle Maschinen auf einem VMware-Hypervisor oder um physische Server, auf denen Windows oder Linux ausgeführt wird.

- **Lokaler Prozessserver** - Geschützte Computer senden Replikationsdaten an den lokalen Prozessserver. Der Prozessserver führt eine Reihe von Aktionen für diese Daten aus. Sie werden optimiert, bevor sie an den Masterzielserver in Azure gesendet werden. Der Server verfügt über einen datenträgerbasierten Cache zum Zwischenspeichern der empfangenen Replikationsdaten. Außerdem verarbeitet er die Pushinstallation des Mobilitätsdiensts, der auf jedem zu schützenden virtuellen Computer oder physischen Server installiert sein muss, und führt die automatische Ermittlung von VMware vCenter-Servern aus. Der Prozessserver ist ein virtueller oder physischer Server unter Windows Server 2012 R2. Es wird empfohlen, diesen in demselben Netzwerk und LAN-Segment zu platzieren, in dem sich auch die zu schützenden Computer befinden. Er kann jedoch auch in einem anderen Netzwerk ausgeführt werden, solange die geschützten Computer über L3-Netzwerksichtbarkeit verfügen. Während der Bereitstellung richten Sie den Prozessserver ein und registrieren ihn beim Konfigurationsserver.

- **Azure Site Recovery-Tresor** - Der Tresor koordiniert und organisiert Datenreplikate, Failover und Wiederherstellung zwischen dem lokalen Standort und Azure.

- **Azure-Konfigurationsserver** - Der Konfigurationsserver koordiniert die Kommunikation zwischen geschützten Computern, dem Prozessserver und den Masterzielservern in Azure. Bei einem Failover richtet er die Replikation ein und koordiniert die Wiederherstellung in Azure. Der Konfigurationsserver läuft auf einem virtuellen Azure Standard A3-Computer im Azure-Abonnement. Während der Bereitstellung richten Sie den Server ein und registrieren ihn beim Azure Site Recovery-Tresor.

- **Masterzielserver** - Der Masterzielserver in Azure enthält replizierte Daten von Ihren geschützten Computern über die angefügten virtuellen Festplatten auf Blob-Speicher in Ihrem Azure-Speicherkonto. Sie stellen die Daten als virtuellen Azure-Computer, als Windows-Server, der auf einem Windows Server 2012 R2-Galerie-Image basiert (zum Schutz von Windows-Computern) oder als Linux-Server bereit, der auf einem OpenLogic CentOS 6.6-Galerie-Image basiert (zum Schutz von Linux-Computern). Es stehen zwei Größenoptionen zur Verfügung - Standard-A3 und Standard-D14. Der Server ist mit demselben Azure-Netzwerk verbunden wie der Konfigurationsserver. Während der Bereitstellung erstellen Sie den Server und registrieren ihn beim Konfigurationsserver.

- **Mobilitätsdienst** - Sie installieren den Mobilitätsdienst auf den einzelnen virtuellen VMware-Maschinen oder auf den physischen Windows-/Linux-Servern, die Sie schützen möchten. Der Dienst sendet Replikationsdaten an den Prozessserver, der diese wiederum an den Masterzielserver in Azure sendet. Der Prozessserver kann den Mobilitätsdienst automatisch auf geschützten Computern installieren, oder Sie können den Dienst manuell über Ihren internen Softwarebereitstellungsprozess bereitstellen.

- **Datenkommunikations- und Replikationskanal** - Es stehen einige Optionen zur Verfügung. Beachten Sie, dass Sie für keine der Optionen eingehende Netzwerkports auf geschützten Computern öffnen müssen. Die gesamte Netzwerkkommunikation wird vom lokalen Standort aus initiiert.
	- **Über das Internet** - Kommuniziert und repliziert Daten von lokalen geschützten Servern und Azure über eine sichere öffentliche Internetverbindung. Dies ist die Standardoption.
	- **VPN/ExpressRoute** - Kommuniziert und repliziert Daten zwischen lokalen Servern und Azure über eine VPN-Verbindung. Sie müssen ein VPN oder eine [ExpressRoute](expressroute) -Verbindung zwischen dem lokalen Standort und Ihrem Azure-Netzwerk einrichten. 

 
## Kapazitätsplanung

- Um eine optimale Leistung zu erzielen und die Konsistenzfunktion für mehrere VMs zu nutzen, bei der mehrere geschützte Computer an einem konsistenten Datenpunkt wiederhergestellt werden, empfiehlt es sich, virtuelle Computer nach Arbeitsauslastung in Schutzgruppen zusammenzufassen.
- Ein einzelner Computer kann nicht über mehrere Masterzielserver geschützt werden, da bei der Datenträgerreplikation eine virtuelle Festplatte, die die Größe des Datenträgers widerspiegelt, auf Azure Blob-Speicher erstellt und als Datenträger mit dem Masterzielserver verbunden wird. Natürlich können Sie mehrere Computer mit einem einzelnen Masterzielserver schützen.
- Der virtuelle Computer des Masterzielservers kann dem Azure-Standard A4 oder D14 entsprechen:
	- Bei einem Standard-A4-Datenträger können Sie jedem virtuellen Computer 16 Datenträger (maximal 1023 GB pro Datenträger) hinzufügen.
	- Bei einem Standard-D14-Datenträger können Sie jedem virtuellen Computer 32 Datenträger (maximal 1023 GB pro Datenträger) hinzufügen.
- Beachten Sie, dass eine mit dem Masterzielserver verbundene Festplatte als Beibehaltungslaufwerk reserviert ist. Mit Azure Site Recovery können Sie Beibehaltungszeitfenster definieren und geschützte Computer jederzeit innerhalb dieser Zeitfenster wiederherstellen. Das Beibehaltungslaufwerk verwaltet ein Journal mit Datenträgeränderungen für die Dauer der Zeitfenster.  Dadurch verringert sich die maximale Datenträgeranzahl für A4 auf 15 und für D14 auf 31. 
- Zum Skalieren Ihrer Bereitstellung fügen Sie mehrere Prozessserver und Masterzielserver hinzu. Wenn auf einem vorhandenen Masterzielserver nicht genügend freie Datenträger verfügbar sind, sollten Sie einen zweiten Masterzielserver bereitstellen. Wenn die Datenänderungsrate der geschützten Computer die Kapazität eines vorhandenen Prozessservers übersteigt, sollten Sie einen zusätzlichen Prozessserver bereitstellen. Beachten Sie, dass Prozessserver und Masterzielservern keine 1:1-Zuordnung erfordern. Sie können beispielsweise den ersten Prozessserver mit dem zweiten Masterzielserver bereitstellen.
Der Prozessserver verwendet einen datenträgerbasierten Cache. Stellen Sie sicher, dass auf C:/ genügend freier Speicherplatz für den Cache bereitsteht. Die Cachegröße wird von der Datenänderungsrate der geschützten Computer beeinflusst. In der Regel wird für mittelgroße Bereitstellungen eine Cacheverzeichnisgröße von 600 GB empfohlen. Sie können jedoch die folgenden Richtlinien anwenden.

	![Cache guidelines](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerSize.png)


## Vorbereitung

### Voraussetzungen für Azure

- Sie benötigen ein [Microsoft Azure](http://azure.microsoft.com/)-Konto. Beginnen Sie mit einem [kostenlosen Testkonto](http://aka.ms/try-azure). 
- Sie benötigen ein Azure-Speicherkonto, um replizierte Daten zu speichern. Für das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region wie der Azure Site Recovery-Tresor befinden und dem gleichen Abonnement zugeordnet sein. Weitere Informationen finden Sie unter [Einführung in Microsoft Azure Storage](http://go.microsoft.com/fwlink/?LinkId=398704).
- Sie benötigen ein virtuelles Azure-Netzwerk, in dem der Konfigurationsserver und der Masterzielserver bereitgestellt werden. Es muss in demselben Abonnement und in derselben Region enthalten sein wie der Azure Site Recovery-Tresor.
- Stellen Sie sicher, dass Sie genügend Azure-Ressourcen für die Bereitstellung aller Komponenten besitzen. Weitere Informationen finden Sie unter [Beschränkungen des Azure-Abonnements](azure-subscription-service-limits).
- Überprüfen Sie, ob Computer, die Sie schützen möchten, den Anforderungen für virtuelle Azure-Computer entsprechen. 

	- **Anzahl der Datenträger** - Bis zu 31 Datenträger können auf einem einzelnen geschützten Server unterstützt werden.
	- **Datenträgergrößen** - Die Kapazität der einzelnen Datenträger sollte 1023 GB nicht überschreiten.
	- **Clustering** - Gruppierte Server werden nicht unterstützt.
	- **Start** - Das Starten über UEFI (Unified Extensible Firmware Interface)/EFI (Extensible Firmware Interface) wird nicht unterstützt.
	- **Volumes** - Mit Bitlocker verschlüsselte Volumes werden nicht unterstützt.
	- **Servernamen** - Namen müssen zwischen 1 und 63 Zeichen (Buchstaben, Ziffern und Bindestriche) enthalten. Der Name muss mit einem Buchstaben oder einer Zahl beginnen und mit einem Buchstaben oder einer Zahl enden. Nachdem ein Computer geschützt wurde, können Sie den Azure-Namen ändern.	

	Weitere Informationen zu Azure-Anforderungen finden Sie unter [Unterstützung virtueller Computer](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A).

### Voraussetzungen für ein Szenario 

- Prozessserver:
	- Sie können den Prozessserver auf einem physischen oder virtuellen Computer mit Windows Server 2012 R2 mit den neuesten Updates bereitstellen. Installieren Sie auf Laufwerk C:/.
	- Der Server benötigt mindestens 8 Prozessorkerne, 64 GB RAM und 300 GB freier Speicherplatz auf Laufwerk C: werden empfohlen.
	- Es wird empfohlen, dass Sie den Server im gleichen Netzwerk und Subnetz wie die Computer platzieren, die Sie schützen möchten.
	- Installieren Sie VMware vSphere CLI 5.1 auf dem Server, damit eine automatische Erkennung von VMware vCenter-Servern durchgeführt werden kann.
- Der Installationspfad für den Konfigurationsserver, den Masterzielserver, den Prozessserver und die Failbackserver sollte nur englische Zeichen umfassen. Für einen Masterzielserver unter Linux sollte der Pfad z. B. **/usr/local/ASR** lauten.

### VMware-Voraussetzungen

- Ein VMware vCenter-Server zur Verwaltung Ihrer VMware vSphere-Hypervisoren. Darauf sollte vCenter Version 5.0 oder höher mit den neuesten Updates ausgeführt werden. 
- Ein oder mehrere vSphere-Hypervisoren, mit virtuellen VMware-Maschinen, die Sie schützen möchten. Auf dem Hypervisor muss ESX/ESXi Version 5.0 oder höher mit den neuesten Updates ausgeführt werden.
- Auf virtuellen VMware-Maschinen, die über einen vCenter-Server ermittelt werden, sollten VMware-Tools installiert und ausgeführt werden.

### Voraussetzungen für geschützte Windows-Computer

Geschützte physische Server oder virtuelle VMware-Maschinen unter Windows sollten folgende Voraussetzungen erfüllen:

- Ein unterstütztes 64-Bit-Betriebssystem: Windows Server 2012 R2, Windows Server 2012 und Windows Server 2008 R2 mit SP1 oder höher.
- Der Hostname, die Bereitstellungspunkte, die Gerätenamen, der Windows-Systempfad (z. B. C:\Windows) sollten nur englische Zeichen verwenden.
- Das Betriebssystem sollte auf Laufwerk C:\ installiert werden.
- Reguläre Speicheroptionen für Windows-Server werden unterstützt.
- Die Firewallregeln auf geschützten Computern sollten diesen den Zugriff auf den Konfigurations- und den Masterzielserver in Azure ermöglichen. 
- Wenn virtuelle Windows-Computer in Azure nach einem Failover mit Remotedesktop eine Verbindung herstellen sollen, stellen Sie sicher, dass Remotedesktop für den lokalen Computer aktiviert ist. Wenn Sie die Verbindung nicht über ein VPN herstellen, sollten die Firewallregeln  Remotedesktopverbindungen über das Internet ermöglichen.

### Voraussetzungen für geschützte Linux-Computer

Geschützte physische Server oder virtuelle VMware-Maschinen unter Linux sollten folgende Voraussetzungen erfüllen:

- Ein unterstütztes Betriebssystem: Centos 6.4, 6.5, 6.6 (32- oder 64-Bit), Oracle Enterprise Linux  6.4, 6.5 (32- oder 64-Bit), auf dem entweder der Red Hat-kompatible Kernel oder UEK3 (Unbreakable Enterprise Kern Release 3) ausgeführt wird,  SUSE Linux Enterprise Server 11 SP3 (32-Bit oder 64-Bit).
- Der Hostname, Bereitstellungspunkte, Gerätenamen und Linux-Systempfade und -Dateinamen (z. B./etc/, /usr) sollten nur englische Zeichen umfassen.
- Der Schutz kann für lokale Computer mit dem folgenden Speicher aktiviert werden:
	- Dateisystem: EXT3, ETX4, ReiserFS, XFS
	- Multipfadsoftware: Device Mapper - Multipfad
	- Volume-Manager: LVM2
	- Physische Server mit HP CCISS-Controllerspeicher werden nicht unterstützt.
- Die Firewallregeln auf geschützten Computern sollten diesen den Zugriff auf den Konfigurations- und den Masterzielserver in Azure ermöglichen.
- Wenn Sie nach einem Failover eine Verbindung mit virtuellen Azure-Computern unter Linux über einen Secure Shell-Client (SSH) herstellen möchten, stellen Sie sicher, dass der Secure Shell-Dienst auf dem geschützten Computer beim Systemstart automatisch gestartet wird und dass die Firewallregeln eine SSH-Verbindung zulassen.  

### Drittanbietervoraussetzungen

Einige Bereitstellungskomponenten in diesem Szenario sind von Drittanbietersoftware abhängig, um ordnungsgemäß zu funktionieren. Eine vollständige Liste finden Sie unter <a href="#thirdparty">Hinweise und Informationen zu Drittanbietersoftware</a>. 

## Schritt 1: Erstellen eines Tresors

1. Melden Sie sich beim [Verwaltungsportal an](https://portal.azure.com).


2. Erweitern Sie **Data Services** > **Recovery Services**, und klicken Sie auf **Site Recovery-Tresor**.


3. Klicken Sie auf **Neu erstellen** > **Schnellerfassung**.
	
4. Geben Sie unter **Name** einen Anzeigenamen ein, mit dem der Tresor identifiziert wird.

5. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Sie finden eine Liste der unterstützten Regionen unter "Geografische Verfügbarkeit" auf der Seite [Azure Site Recovery-Preisdetails](href="http://go.microsoft.com/fwlink/?LinkId=389880).

6. Klicken Sie auf **Tresor erstellen**. 

	![New vault](./media/site-recovery-vmware-to-azure/ASRVMWare_CreateVault.png)

Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von **Recovery Services** als **Aktiv** aufgelistet.

## Schritt 2: Bereitstellen eines Konfigurationsservers


1. Klicken Sie auf der Seite **Recovery Services** auf den Tresor, um die Seite "Schnellstart" zu öffnen. Schnellstart kann auch jederzeit über das Symbol geöffnet werden.

	![Quick Start Icon](./media/site-recovery-vmware-to-azure/ASRVMWare_QuickStartIcon.png)

2. Wählen Sie in der Dropdownliste den Eintrag **Zwischen einem lokalen Standort mit VMware-/physischen Servern und Azure**.
3. Klicken Sie unter **Zielressourcen vorbereiten** auf **Konfigurationsserver bereitstellen**.

	![Deploy configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_DeployCS.png)

4. Geben Sie die Konfigurationsserverdetails und die Anmeldeinformationen für die Verbindung mit dem Server ein. Wählen Sie das Azure-Netzwerk aus, in dem sich der Server befinden soll. Geben Sie die interne IP-Adresse und das Subnetz an, die bzw. das dem Server zugewiesen werden soll. Wenn Sie auf **OK** klicken, wird ein virtueller Standard-A3-Computer basierend auf einem Azure Site Recovery Windows Server 2012 R2-Galerie-Image in Ihrem Abonnement für den Konfigurationsserver erstellt. Er wird als erste Instanz in einem neuen Cloud-Dienst mit einer reservierten öffentlichen IP-Adresse erstellt.

	![Configuration server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_CSDetails.png)

5. Sie können den Fortschritt auf der Registerkarte **Aufträge** überwachen.

	![Monitor progress](./media/site-recovery-vmware-to-azure/ASRVMWare_MonitorConfigServer.png)

8.  Nach dem Bereitstellen des Konfigurationsservers notieren Sie die ihm zugewiesene öffentliche IP-Adresse auf der Seite **Virtuelle Computer** im Azure-Portal. Notieren Sie dann auf der Registerkarte **Endpunkte** den öffentlichen HTTPS-Port, der dem privaten Port 443  zugeordnet ist. Diese Informationen benötigen Sie später, wenn Sie den Masterzielserver und den Prozessserver beim Konfigurationsserver registrieren. Der Konfigurationsserver wird mit 4 öffentlichen Endpunkten bereitgestellt:

	- 443: HTTPS-Kanal zum Koordinieren der Kommunikation zwischen Komponentenservern und Azure.
	- 9443: Für das Failbacktool und die Failbackkommunikation.
	- Remotedesktop
	- PowerShell


## Schritt 3: Registrieren des Konfigurationsservers im Tresor

1. Klicken Sie unter **Zielressourcen vorbereiten** auf **Registrierungsschlüssel herunterladen**. Die Schlüsseldatei wird automatisch generiert. Sie ist nach der Erstellung 5 Tage lang gültig. Kopieren Sie die Datei auf den Konfigurationsserver.
2. Klicken Sie auf der Seite **Dashboard** der virtuellen Maschine auf **Verbinden**. Verwenden Sie die heruntergeladene RDP-Datei, um sich über Remotedesktop beim Konfigurationsserver anzumelden.  Wenn Sie sich zum ersten Mal anmelden, wird der Azure Site Recovery-Assistent für Installation und Registrierung automatisch ausgeführt.

	![Registration](./media/site-recovery-vmware-to-azure/ASRVMWareRegister1.png)

3. Folgen Sie den Anweisungen des Assistenten. Sie müssen MySQL Server herunterladen und installieren sowie die entsprechenden Anmeldeinformationen eingeben. Suchen Sie auf der Seite **Azure Site Recovery-Registrierung** nach der Schlüsseldatei, die Sie auf den Server kopiert haben.

	![Registration key](./media/site-recovery-vmware-to-azure/ASRVMWareRegister2.png)

4. Nach Abschluss der Registrierung wird eine Passphrase generiert. Kopieren Sie sie an einen sicheren Ort. Sie benötigen diese Informationen, um den Prozess- und den Masterzielserver beim Konfigurationsserver zu authentifizieren und zu registrieren. Außerdem dienen sie zur Sicherstellung der Kanalintegrität bei der Kommunikation mit dem Konfigurationsserver. Sie können die Passphrase neu generieren. In diesem Fall müssen Sie jedoch den Masterzielserver und den Prozessserver anhand der neuen Passphrase neu registrieren.

	![Passphrase](./media/site-recovery-vmware-to-azure/ASRVMWareRegister2.png)

Nach der Registrierung wird der Konfigurationsserver auf der Seite **Konfigurationsserver** im Tresor aufgeführt.

## Schritt 4: Einrichten einer VPN-Verbindung
 
Sie können eine Verbindung mit dem Konfigurationsserver über das Internet oder über eine VPN- oder ExpressRoute-Verbindung herstellen. Eine Internetverbindung verwendet die Endpunkte des virtuellen Computers in Verbindung mit der öffentlichen virtuellen IP-Adresse des Servers. VPN verwendet die interne IP-Adresse des Servers zusammen mit den Ports des privaten Endpunkts.
 
Sie können eine VPN-Verbindung mit dem Server wie folgt konfigurieren:

1. Wenn Sie keine Verbindung zwischen den Standorten und keine Azure ExpressRoute-Verbindung eingerichtet haben, erhalten Sie hier weitere Informationen:

	- [ExpressRoute or VPN - What's right for me](http://azure.microsoft.com/blog/2014/06/10/expressroute-or-virtual-network-vpn-whats-right-for-me/) (in englischer Sprache)
	- [Konfigurieren eines Standort-zu-Standort-VPNs im Verwaltungsportal](https://msdn.microsoft.com/library/azure/dn133795.aspx)
	- [Konfigurieren von ExpressRoute](https://msdn.microsoft.com/library/azure/dn606306.aspx)
	
2. Klicken Sie im Tresor auf **Server** > **Konfigurationsserver** > Konfigurationsserver > **Konfigurieren**.
3. Legen Sie unter **Konnektivitätseinstellungen** den **Konnektivitätstyp** auf **VPN** fest. Wenn Sie ein VPN eingerichtet haben und vom lokalen Standort aus keinen Internetzugriff besitzen, stellen Sie sicher, dass Sie die VPN-Option auswählen. Andernfalls kann der Prozessserver keine Replikationsdaten an den Masterzielserver an den öffentlichen Endpunkten senden.

	![Enable VPN](./media/site-recovery-vmware-to-azure/ASRVMWare_EnableVPN.png)

## Schritt 5: Bereitstellen des Masterzielservers

1. Klicken Sie unter **Zielressourcen vorbereiten** auf **Masterzielserver bereitstellen**.
2. Geben Sie die Details und Anmeldeinformationen für den Masterzielserver an. Der Server wird in demselben Azure-Netzwerk wie der Konfigurationsserver bereitgestellt, bei dem er registriert ist. Wenn Sie zum Abschluss klicken, wird ein virtueller Azure-Computer mit einem Windows- oder Linux-Galerie-Image erstellt. 

	![Target server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_TSDetails.png)

3. Ein virtueller Windows-Masterserver wird mit diesen öffentlichen TCP-Endpunkten erstellt:

	- Benutzerdefiniert: Der öffentliche Port wird zum Senden von Replikationsdaten über das Internet verwendet. Der private Port 9443 wird vom Prozessserver zum Senden von Daten an den Masterzielserver über VPN verwendet.
	- Benutzerdefiniert1: Der private Port 9080 wird vom Prozessserver zum Senden von Daten über VPN an den Zielserver verwendet.
	- PowerShell: Privater Port: 5986
	- Remotedesktop: Privater Port: 3389

4. Ein virtueller Linux-Masterserver wird mit diesen Endpunkten erstellt:

	- Benutzerdefiniert: Der öffentliche Port wird zum Senden von Replikationsdaten über das Internet verwendet. Der private Port 9443 wird vom Prozessserver zum Senden von Daten an den Masterzielserver über VPN verwendet.
	- Benutzerdefiniert1: Der private Port 9080 wird vom Prozessserver zum Senden von Daten an den Masterzielserver über VPN verwendet.
	- SSH: Privater Port 22

5. Warten Sie unter **Virtuelle Computer**, bis der virtuelle Computer gestartet ist. 

	- Wenn Sie den Server mit Windows konfiguriert haben, notieren Sie die Details für Remotedesktop.
	- Wenn Sie ihn mit Linux konfiguriert haben und eine Verbindung über VPN herstellen, notieren Sie die interne IP-Adresse des virtuellen Computers. Notieren Sie die öffentliche IP-Adresse, wenn Sie eine Verbindung über das Internet herstellen.
6.  Melden Sie sich beim Server an, um die Installation abzuschließen und den Server beim Konfigurationsserver zu registrieren. Wenn Sie Windows verwenden:

	1. Initiieren Sie eine Remotedesktopverbindung mit dem virtuellen Computer. Beim ersten Anmelden wird ein Skript in einem PowerShell-Fenster ausgeführt. Schließen Sie es nicht. Bei Fertigstellung wird das Host-Agent-Konfigurationstool zum Registrieren des Servers automatisch geöffnet.
	2. Geben Sie unter **Host-Agent-Konfiguration** die interne IP-Adresse des Konfigurationsservers und Port 443 an. Sie können die interne Adresse und den privaten Port 443 auch dann verwenden, wenn Sie keine Verbindung über den VPN-Modus herstellen, da der virtuelle Computer demselben Azure-Netzwerk zugeordnet ist wie der Konfigurationsserver. Lassen Sie **HTTPS verwenden** aktiviert. Geben Sie die Passphrase für den Konfigurationsserver ein, die Sie zuvor notiert haben. Klicken Sie auf **OK**, um den Server zu registrieren. Beachten Sie, dass Sie die NAT-Optionen auf der Seite ignorieren können. Sie werden nicht verwendet.

	![Windows master target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSRegister.png)

6. Wenn Sie Linux verwenden:
	1. Kopieren Sie die [TAR-Datei des Serverinstallationsprogramms](http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409) über einen SFTP-Client auf den virtuellen Computer. Alternativ können Sie sich anmelden und die Datei mit wget von dem Link auf der Seite "Schnellstart" herunterladen. 
	2. Melden Sie sich über einen Secure Shell-Client beim Server an. Wenn Sie über VPN mit dem Azure-Netzwerk verbunden sind, verwenden Sie die interne IP-Adresse. Verwenden Sie andernfalls die externe IP-Adresse und den öffentlichen SSH-Endpunkt.
	3. Extrahieren Sie die Dateien aus dem GZIP-Installationsprogramm, indem Sie folgenden Befehl ausführen: **tar -xvzf Microsoft-ASR_UA_8.2.0.0_RHEL6-64***."   

		![Linux master target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinuxTar.png)

	4. Stellen Sie sicher, dass Sie sich in dem Verzeichnis befinden, in das Sie den Inhalt der tar-Datei extrahiert haben, und führen Sie den Befehl "**sudo ./install.sh**" aus. Wählen Sie Option **2. Masterziel**. Behalten Sie bei den anderen Optionen die Standardeinstellungen bei.

		![Register target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinux.png)

	5. Nach Abschluss der Installation wird die Befehlszeile **Host Config Interface** angezeigt. Ändern Sie nicht die Größe des Fensters. 
	6. Wählen Sie mithilfe der Pfeiltasten **Global** aus, und drücken Sie die EINGABETASTE.
	7. Geben Sie unter **IP-Adresse eingeben** die interne Adresse des Konfigurationsservers und Port 22 ein.
	8.  Geben Sie die Passphrase des Konfigurationsservers an, die Sie zuvor notiert haben, und drücken Sie die EINGABETASTE. Wählen Sie **Beenden** um die Installation fertig zu stellen. Wenn Sie den PuTTY-Client zum Herstellen einer SSH-Verbindung mit dem virtuellen Computer verwenden, können Sie zum Einfügen UMSCHALT+EINFG verwenden. 
	9.  Verwenden Sie die die rechte Pfeiltaste zum Beenden, und drücken Sie die EINGABETASTE.

		![Master target server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinux2.png)

7. Warten Sie einige Minuten (5 bis 10), und stellen Sie auf der Seite **Server** > **Konfigurationsserver** sicher, dass der Masterzielserver auf der Registerkarte **Serverdetails** als registriert aufgeführt ist. Wenn Sie Linux verwenden und der Server nicht registriert wurde, führen Sie das Hostkonfigurationstool über "/usr/local/ASR/Vx/bin/hostconfigcli" erneut aus. Sie müssen die Zugriffsberechtigungen festlegen, indem Sie chmod als Administrator ausführen.

	![Verify target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSList.png)

## Schritt 6: Bereitstellen eines lokalen Prozessservers

1. Klicken Sie auf "Schnellstart" > **Prozessserver lokal installieren** > **Prozessserver herunterladen und installieren**.

	![Install process server](./media/site-recovery-vmware-to-azure/ASRVMWare_PSDeploy.png)

2. Kopieren Sie die heruntergeladene ZIP-Datei auf den Server, auf dem Sie den Prozessserver installieren möchten. Die ZIP-Datei enthält zwei Installationsdateien:

	- Microsoft-ASR_CX_TP_8.2.0.0_Windows*
	- Microsoft-ASR_CX_8.2.0.0_Windows*

3. Entpacken Sie das Archiv, und kopieren Sie die Installationsdateien in einen Speicherort auf dem Server.
4. Führen Sie die Installationsdatei **Microsoft-ASR_CX_TP_8.2.0.0_Windows*** aus, und folgen Sie den Anweisungen. Dadurch werden die Drittanbieterkomponenten installiert, die für die Bereitstellung erforderlich sind.
5. Führen Sie dann **Microsoft-ASR_CX_8.2.0.0_Windows*** aus.
6. Wählen Sie auf der Seite **Servermodus** die Option **Prozessserver** aus.
7.	Wenn Sie eine Verbindung über VPN herstellen, geben Sie unter **Konfigurationsserverdetails** die interne IP-Adresse des Konfigurationsservers und 443 für den Port an. Geben Sie andernfalls die öffentliche virtuelle IP-Adresse und den zugeordneten öffentlichen HTTP-Endpunkt.
8.	Deaktivieren Sie **Softwaresignatur des Mobilitätsdiensts prüfen**, wenn Sie die automatische Pushfunktion zum Installieren des Dienstes verwenden und die Überprüfung deaktivieren möchten. Die Signaturüberprüfung benötigt eine Internetverbindung vom Prozessserver aus.
9.	Geben Sie die Passphrase des Konfigurationsservers ein.

	![Register configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_CSRegister.png)

8. Beenden Sie die Serverinstallation. Denken Sie daran, dass Sie auf dem Server VMware vSphere CLI 5.1 installieren müssen, um vCenter-Server ermitteln zu können.

	![Register process server](./media/site-recovery-vmware-to-azure/ASRVMWare_PSRegister2.png)

Überprüfen Sie, ob der Prozessserver erfolgreich registriert wurde unter Tresor > **Konfigurationsserver** > **Serverdetails**.

![Validate process server](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerRegister.png)

Wenn Sie beim Registrieren des Prozessservers die Signaturüberprüfung für den Mobilitätsdienst nicht deaktiviert haben, können Sie dies später nachholen:

1. Melden Sie sich beim Prozessserver als Administrator an, und öffnen Sie die Datei "C:\pushinstallsvc\pushinstaller.conf", um sie zu bearbeiten. Fügen Sie im Abschnitt **[PushInstaller.transport]** folgende Zeile hinzu: **SignatureVerificationChecks="0"**. Speichern und schließen Sie die Datei.
1. Starten Sie den InMage PushInstall-Dienst neu.


## Schritt 7: Hinzufügen von vCenter-Servern

1. Wählen Sie auf der Registerkarte **Server** > **Konfigurationsserver** den Konfigurationsserver aus, und klicken Sie, um einen vCenter-Server hinzuzufügen.

	![Select vCenter server](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter.png)

2. Geben Sie Details für den vCenter-Server an, und wählen Sie den Prozessserver aus, der zu dessen Ermittlung verwendet wird.  Der Prozessserver muss in demselben Netzwerk wie der vCenter-Server enthalten sein und VMware vSphere CLI 5.1 installiert haben.
3. Nach Abschluss der Erkennung wird der vCenter-Server unter den Serverkonfigurationsdetails aufgeführt.
	
	![vCenter server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter2.png)


## Schritt 8: Erstellen einer Schutzgruppe

1. Öffnen Sie **Geschützte Elemente** > **Schutzgruppe**, und klicken Sie, um eine Schutzgruppe hinzuzufügen.

	![Create protection group](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG1.png)

2. Geben Sie auf der Seite **Schutzgruppeneinstellungen angeben** einen Namen für die Gruppe an, und wählen Sie den Konfigurationsserver aus, auf dem Sie die Gruppe erstellen möchten.

	![Protection group settings](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG2.png)

3. Konfigurieren Sie auf der Seite **Replikationseinstellungen angeben** die Replikationseinstellungen an, die für alle Computer in der Gruppe verwendet werden.   

	![Protection group replication](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG3.png)

4. Einstellungen:
	- **Konsistenz zwischen mehreren virtuellen Computern**: Wenn Sie diese Option aktivieren, werden freigegebene anwendungskonsistente Wiederherstellungspunkte auf den Computern in der Schutzgruppe erstellt. Diese Einstellung ist besonders wichtig, wenn auf allen Computern in der Schutzgruppe dieselbe Arbeitsauslastung ausgeführt wird. Alle Computer werden an demselben Datenpunkt wiederhergestellt. Steht nur bei Windows-Servern zur Verfügung. 
	- **RPO-Schwellenwert**: Warnungen werden generiert, wenn die Replikations-RPO für den kontinuierlichen Datenschutz den konfigurierten RPO-Schwellenwert überschreitet.
	- **Wiederherstellungspunktbeibehaltung**: Gibt die Beibehaltungsdauer an. Geschützte Computer können innerhalb dieses Zeitfensters an einem beliebigen Punkt wiederhergestellt werden.
	- **Häufigkeit von anwendungskonsistenten Momentaufnahmen**: Gibt an, wie oft Wiederherstellungspunkte erstellt werden, die anwendungskonsistente Momentaufnahmen enthalten.

Auf der Seite **Geschützte Elemente** können Sie die Erstellung der Schutzgruppen überwachen. 

## Schritt 9: Installieren des Mobilitätsdiensts über die Pushfunktion

Beim Hinzufügen von Computern zu einer Schutzgruppe wird der Mobilitätsdienst vom Prozessserver automatisch per Pushfunktion auf jedem Computer installiert. Verwenden Sie diesen automatischen Pushmechanismus für geschützte Windows-Computer verwenden möchten, müssen Sie auf jedem Computer folgende Aktionen durchführen:

1. Konfigurieren Sie die Windows-Firewall so, dass die **Datei- und Druckerfreigabe** und die **Windows-Verwaltungsinstrumentation** erlaubt werden. Für Computer, die einer Domäne angehören, können Sie die Firewallrichtlinie mit einem Gruppenrichtlinienobjekt konfigurieren.
2. Das zur Durchführung der Pushinstallation verwendete Konto muss in der Gruppe "Administratoren" auf dem Computer enthalten sein, den Sie schützen möchten. Beachten Sie, dass diese Anmeldeinformationen nur für die Pushinstallation verwendet werden. Sie werden vom Mobilitätsdienst nicht an einer beliebigen Stelle gespeichert und werden verworfen, sobald der Server geschützt ist. Sie stellen diese Anmeldeinformationen bereit, wenn Sie einen Computer einer Schutzgruppe hinzufügen.

	![Mobility credentials](./media/site-recovery-vmware-to-azure/ASRVMWare_PushCredentials.png)

3. Wenn es sich bei dem Administratorkonto nicht um ein Domänenkonto handelt, müssen Sie die Remote-Benutzerzugriffssteuerung auf dem lokalen Computer deaktivieren. Hierzu erstellen Sie in "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" ggf. den Eintrag "LocalAccountTokenFilterPolicy" und weisen ihm folgenden DWORD-Wert zu: 

Wenn Sie Computer unter Linux schützen möchten, müssen Sie folgende Aktionen ausführen:

1. Stellen Sie sicher, dass das Konto einem Root-Benutzer auf dem Linux-Quellserver entspricht.
1. Installieren Sie die neuesten openssh-, openssh-server- und openssl-Pakete auf dem Computer, den Sie schützen möchten.
1. Aktivieren Sie SSH-Port 22.
2. Aktivieren Sie das SFTP-Subsystem und die Kennwortauthentifizierung in der SSHD-Konfigurationsdatei:
	1. Melden Sie sich mit dem Root-Benutzerkonto an.
	2. Suchen Sie in der Datei "/etc/ssh/sshd_config" die Zeile, die mit "PasswordAuthentication" beginnt. Heben Sie die Auskommentierung der Zeile auf, und ändern Sie den Wert von "no" in "yes". 

		![Linux mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

	4. Suchen Sie die Zeile, die mit "Subsystem" beginnt, und heben Sie die Auskommentierung der Zeile auf. 

		![Linux push mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

## Schritt 10: Hinzufügen von Computern zu einer Schutzgruppe

1. Öffnen Sie die Registerkarte **Geschützte Elemente** > **Schutzgruppe** > **Computer**, und fügen Sie virtuelle oder physische Computer hinzu, die von einem erkannten vCenter-Server verwaltet werden. Es empfiehlt sich, dass Schutzgruppen ihre Arbeitsauslastungen spiegeln, damit Sie Computer, auf denen eine bestimmte Anwendung ausgeführt wird, derselben Gruppe hinzufügen.

	![Add machines](./media/site-recovery-vmware-to-azure/ASRVMWare_PushCredentials.png)

2. Wählen Sie auf der Seite **Virtuellen Computer auswählen** in **Virtuellen Computer hinzufügen** einen vCenter-Server aus, und wählen Sie dann Computer aus.

	![Add V-Center server](./media/site-recovery-vmware-to-azure/ASRVMWare_SelectVMs.png)

3. Wenn Sie einer Schutzgruppe Computer hinzufügen, wird der Mobilitätsdienst vom lokalen Prozessserver automatisch installiert. Damit der automatische Pushmechanismus funktioniert, stellen Sie sicher, dass Sie die geschützten Computer so eingerichtet haben, wie im vorherigen Schritt beschrieben wurde.
4. Wählen Sie unter **Virtuelle Computer auswählen** den vCenter-Server aus, der die zu schützenden Computer verwaltet, und wählen Sie dann die virtuellen Computer aus.

4. Wählen Sie die Server und den Speicher für die Replikation aus. 

	![vCenter server](./media/site-recovery-vmware-to-azure/ASRVMWare_MachinesResources.png)

5. Geben Sie die Benutzeranmeldeinformationen für den Quellserver an. Dies ist erforderlich, um den Mobilitätsdienst automatisch auf den Quellcomputern zu installieren. Für Windows-Server sollte das Konto über Administratorrechte auf dem Quellserver verfügen. Für Linux muss das Konto Superuser-Berechtigungen auf dem Server besitzen.

	![Linux credentials](./media/site-recovery-vmware-to-azure/ASRVMWare_VMMobilityInstall.png)

6. Klicken Sie auf das Häkchen, um das Hinzufügen von Computern zur Schutzgruppe zu beenden und die erste Replikation für jeden Computer zu starten. Sie können den Status auf der Seite **Aufträge** überwachen.

	![Add V-Center server](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)

7. Klicken Sie außerdem auf **Geschützte Elemente** > <Schutzgruppe> **Virtuelle Computer**, um den Schutzstatus zu überwachen. Nachdem die erste Replikation abgeschlossen ist und die Computer Daten synchronisieren, wird der Status **Geschützt** angezeigt.

	![Virtual machine jobs](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)

## Schritt 11: Festlegen der Eigenschaften geschützter Computer

1. Sobald ein Computer den Status **Geschützt** aufweist, können Sie die Failovereigenschaften konfigurieren. Wählen Sie in den Schutzgruppendetails den Computer aus, und öffnen Sie die Registerkarte **Konfigurieren**.
2. Sie können den Namen, der dem Computer in Azure nach dem Failover zugewiesen wird, und die Azure-Größe ändern. Außerdem können Sie das Azure-Netzwerk auswählen, mit dem der Computer nach einem Failover verbunden werden soll. Beachten Sie Folgendes:

	- Der Name des Azure-Computers muss den Azure-Anforderungen entsprechen, die in den Voraussetzungen beschrieben werden.
	- Standardmäßig werden replizierte virtuelle Computer in Azure nicht mit einem Azure-Netzwerk verbunden. Wenn repliziere virtuelle Computer kommunizieren sollen, stellen Sie sicher, dass für beide dasselbe Azure-Netzwerk festgelegt wird.

	![Set virtual machine properties](./media/site-recovery-vmware-to-azure/ASRVMWare_VMProperties.png)

## Schritt 12: Ausführen eines Failovers

1. Fügen Sie auf der Seite **Wiederherstellungspläne** einen Wiederherstellungsplan hinzu. Geben Sie Details für den Plan an, und wählen Sie **Azure** als Ziel aus.

	![Configure recovery plan](./media/site-recovery-vmware-to-azure/ASRVMWare_RP1.png)

2. Wählen Sie unter **Virtuellen Computer auswählen** eine Schutzgruppe aus, und wählen Sie dann Computer in der Gruppe aus, die dem Wiederherstellungsplan hinzugefügt werden sollen.

	![Add virtual machines](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

3. Bei Bedarf können Sie den Plan anpassen und Gruppen erstellen sowie die Reihenfolge anpassen, in der für die Computer im Wiederherstellungsplan ein Failover durchgeführt wird. Sie können auch Aufforderungen für manuelle Aktionen und Skripts hinzufügen.

	![Customize recovery plan](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

5. Wählen Sie den Plan auf der Seite **Wiederherstellungspläne** aus, und klicken Sie auf **Testfailover**.
6. Überprüfen Sie unter **Failover bestätigen** die Failoverrichtung (nach Azure), und wählen Sie den Wiederherstellungspunkt für das Failover aus. 
7. Warten Sie, bis der Failoverauftrag abgeschlossen ist, und prüfen Sie dann, ob das Failover wie erwartet durchgeführt wurde und die replizierten virtuellen Computer in Azure erfolgreich gestartet wurden.

<a name="thirdparty"></a><h2>HINWEISE UND INFORMATIONEN ZU DRITTANBIETERSOFTWARE</h2>

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, "Third Party Code").  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms. 

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=530254)  h. Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.


<!--HONumber=49-->