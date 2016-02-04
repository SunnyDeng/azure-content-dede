<properties
	pageTitle="Replizieren von Hyper-V-VMs (in einer VMM-Cloud) an einen sekundären Standort mit Azure Site Recovery per SAN | Microsoft Azure"
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung von virtuellen Hyper-V-Computern zwischen lokalen Standorten mithilfe der SAN-Replikation."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="raynew"/>

# Replizieren von Hyper-V-VMs (in einer VMM-Cloud) an einen sekundären Standort mit Azure Site Recovery per SAN

Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server aufeinander abgestimmt werden. Computer können in Azure oder in einem sekundären lokalen Datencenter repliziert werden. Eine kurze Übersicht über das Gesamtthema finden Sie unter [Was ist Azure Site Recovery?](site-recovery-overview.md).

## Übersicht

In diesem Artikel wird beschrieben, wie Sie Site Recovery bereitstellen, um den Schutz für virtuelle Hyper-V-Maschinen in Private Clouds mit System Center Virtual Machine Manager (VMM) zu orchestrieren und zu automatisieren. In diesem Szenario werden virtuelle Computer von einem primären VMM-Standort per Site Recovery und SAN-Replikation an einen sekundären VMM-Standort repliziert.

Der Artikel enthält eine Übersicht und informiert über die Voraussetzungen für die Bereitstellung. Hier erfahren Sie, wie Sie die Replikation in VMM und im Site Recovery-Tresor konfigurieren und aktivieren. Sie bestimmen und klassifizieren SAN-Speicher in VMM, stellen LUNs bereit und weisen Hyper-V-Clustern Speicher zu. Zum Schluss wird das Failover getestet, um sicherzustellen, dass alles wie erwartet funktioniert.

Etwaige Fragen können Sie im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) stellen.

Beispiele für geschäftliche Vorteile dieses Szenarios:

- Bereitstellung einer skalierbaren und durch Site Recovery automatisierten Replikationslösung für Unternehmen.
- Nutzung der SAN-Replikationsfunktionen gewerblicher Speicherpartner (sowohl über Fibre Channel als auch über iSCSI). Eine Liste mit unseren SAN-Speicherpartnern finden Sie [hier](http://go.microsoft.com/fwlink/?LinkId=518669).
- Nutzung Ihrer vorhandenen SAN-Infrastruktur zum Schutz unternehmenskritischer, in Hyper-V-Clustern bereitgestellter Anwendungen.
- Unterstützung von Gastclustern.
- Gewährleistung der Replikationskonsistenz auf verschiedenen Ebenen einer Anwendung mit synchronisierter Replikation für niedrige RTO- und RPO-Werte sowie mit nicht synchronisierter Replikation für hohe Flexibilität (abhängig von den Funktionen des Speicherarrays).  
- VMM-Integration zur SAN-Verwaltung über die VMM-Konsole und SMI-S in VMM zur Erkennung des vorhandenen Speichers.  



## Architektur

In diesem Szenario werden virtuelle Hyper-V-Computer eines lokalen VMM-Standorts unter Verwendung der SAN-Replikation an einem anderen Standort gesichert, um deren Workloads zu schützen.

![SAN-Architektur](./media/site-recovery-vmm-san/architecture.png)

Komponenten dieses Szenarios:

- **Lokale virtuelle Computer**: Ihre lokalen, in Private Clouds von VMM verwalteten Hyper-V-Server enthalten virtuelle Computer, die Sie schützen möchten.
- **Lokale VMM-Server**: Am primären, zu schützenden Standort können ein einzelner oder mehrere VMM-Server vorhanden sein. Gleiches gilt für den sekundären Standort.
- **SAN-Speicher**: Je ein SAN-Array am primären und sekundären Standort.
-  **Azure Site Recovery-Tresor**: Der Tresor koordiniert und orchestriert Datenreplikation, Failover und Wiederherstellung zwischen Ihren lokalen Standorten.
- **Azure Site Recovery-Anbieter**: Wird auf jedem VMM-Server installiert.

## Vorbereitung

Stellen Sie sicher, dass diese Voraussetzungen erfüllt werden:

**Voraussetzungen** | **Details** 
--- | ---
**Azure**| Sie benötigen ein [Microsoft Azure](http://azure.microsoft.com/)-Konto. Für den Einstieg steht eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/) zur Verfügung. [Erfahren Sie mehr](https://azure.microsoft.com/pricing/details/site-recovery/) über die Preise für Site Recovery. 
**VMM** | Sie benötigen mindestens einen VMM-Server, der als physischer oder virtueller eigenständiger Server oder als virtueller Cluster bereitgestellt wird. <br/><br/>Auf dem VMM-Server sollte System Center 2012 R2 mit den neuesten kumulativen Updates ausgeführt werden.<br/><br/>Sie benötigen mindestens eine Cloud, die auf dem zu schützenden primären VMM-Server konfiguriert ist, und eine Cloud, die auf dem sekundären VMM-Server für den Schutz und die Wiederherstellung konfiguriert ist.<br/><br/>Die Quellcloud, die geschützt werden soll, muss mindestens eine VMM-Hostgruppe enthalten.<br/><br/>Für alle VMM-Clouds muss das Hyper-V-Kapazitätsprofil eingestellt sein.<br/><br/>Weitere Informationen zum Einrichten von VMM-Clouds finden Sie unter [Konfigurieren der VMM-Cloud-Fabric](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) und [Walkthrough: Creating Private Clouds with System Center 2012 SP1 Virtual Machine Manager](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
**Hyper-V** | Sie benötigen mindestens einen Hyper-V-Cluster an den primären und sekundären Standorten und mindestens eine VM im Hyper-V-Quellcluster. VMM-Hostgruppen an primären und sekundären Standorten sollten in jeder Gruppe über mindestens einen Hyper-V-Cluster verfügen.<br/><br/>Auf den Hyper-V-Host- und -Zielservern muss mindestens Windows Server 2012 mit der Hyper-V-Rolle ausgeführt werden, und die aktuellen Updates müssen installiert sein.<br/><br/>Alle Hyper-V-Server mit zu schützenden VMs müssen in einer VMM-Cloud angeordnet sein.<br/><br/>Bedenken Sie beim Ausführen von Hyper-V in einem Cluster, dass der Clusterbroker nicht automatisch erstellt wird, wenn Sie einen Cluster mit statischen IP-Adressen verwenden. Sie müssen den Clusterbroker manuell konfigurieren. [Weitere Informationen](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)
**SAN-Speicher** | Mithilfe der SAN-Replikation können virtuelle Computer in Gastclustern mit iSCSI- oder Fibre Channel-Speicher oder freigegebenen virtuellen Festplatten (VHDX) replizieren.<br/><br/>Sie benötigen zwei eingerichtete SAN-Arrays, eines am primären und eines am sekundären Standort.<br/><br/>Zwischen den Arrays muss Netzwerkinfrastruktur eingerichtet sein. Peering und Replikation müssen konfiguriert sein. Replikationslizenzen müssen in Einklang mit den Anforderungen an Speicherarrays eingerichtet sein.<br/><br/>Zwischen den Hyper-V-Hostservern und dem Speicherarray müssen Netzwerkverbindungen eingerichtet sein, damit die Hosts über iSCSI oder Fibre Channel mit den Speicher-LUNs kommunizieren können.<br/><br/> Sehen Sie sich die [Liste mit den unterstützten Speicherarrays](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) an.<br/><br/>Von den Speicherarray-Herstellern bereitgestellte SMI-S-Anbieter müssen installiert sein, und die SAN-Arrays müssen vom Anbieter verwaltet werden. Richten Sie den Anbieter gemäß der Dokumentation ein.<br/><br/>Vergewissern Sie sich, dass sich der SMI-S-Anbieter des Arrays auf einem Server befindet, auf den der VMM-Server über das Netzwerk per IP-Adresse oder FQDN zugreifen kann.<br/><br/>Für jedes SAN-Array muss mindestens ein Speicherpool in dieser Bereitstellung verfügbar sein. Der VMM-Server am primären Standort muss das primäre Array und der sekundäre VMM-Server das sekundäre Array verwalten.<br/><br/>Der VMM-Server am primären Standort muss das primäre Array verwalten, und der sekundäre VMM-Server kümmert sich um die Verwaltung des sekundären Arrays.
**Netzwerkzuordnung** | Sie können eine Netzwerkzuordnung konfigurieren, um sicherzustellen, dass replizierte virtuelle Maschinen nach einem Failover optimal auf sekundären Hyper-V-Hostservern platziert werden und eine Verbindung mit den entsprechenden VM-Netzwerken herstellen können. Wenn Sie die Netzwerkzuordnung nicht konfigurieren, wird für Replikat-VMs nach dem Failover keine Netzwerkverbindung hergestellt.<br/><br/>Stellen Sie zum Einrichten der Netzwerkzuordnung während der Bereitstellung sicher, dass für die virtuellen Maschinen auf dem Hyper-V-Quellhostserver Verbindungen mit einem VMM-VM-Netzwerk bestehen. Dieses Netzwerk sollte mit einem logischen Netzwerk verbunden sein, das der Cloud zugeordnet ist.<br/<br/>Für die Zielcloud auf dem sekundären VMM-Server, die Sie für die Wiederherstellung verwenden, sollte ein entsprechendes VM-Netzwerk konfiguriert sein. Dieses sollte wiederum mit einem entsprechenden logischen Netzwerk verknüpft sein, das der Zielcloud zugeordnet ist.<br/><br/>Lesen Sie sich die [weiteren Informationen](site-recovery-network-mapping.md) zur Netzwerkzuordnung durch.


## Schritt 1: Vorbereiten der VMM-Infrastruktur

Die Vorbereitung der VMM-Infrastruktur umfasst folgende Schritte:

1. Sicherstellen, dass VMM-Clouds eingerichtet sind
2. Integrieren und Klassifizieren von SAN-Speicher in VMM
3. Erstellen von LUNs und Zuweisen von Speicher
4. Erstellen von Replikationsgruppen
5. Einrichten von VM-Netzwerken

### Sicherstellen, dass VMM-Clouds eingerichtet sind

Site Recovery orchestriert den Schutz für virtuelle Computer auf lokalen Hyper-V-Hostservern in VMM-Clouds. Sie müssen sicherstellen, dass diese Clouds ordnungsgemäß eingerichtet sind, bevor Sie mit der Bereitstellung von Site Recovery beginnen. Hilfreiche Ressourcen:

- [Vorbereiten der VMM-Infrastruktur – SAN](https://msdn.microsoft.com/library/azure/dn883636.aspx#BKMK_Fabric)
- [Creating Private Clouds](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) im Blog von Keith Mayer

### Integrieren und Klassifizieren von SAN-Speicher in VMM

Gehen Sie wie folgt vor, um SANs in der VMM-Konsole hinzuzufügen und zu klassifizieren:

1. Klicken Sie im Arbeitsbereich **Fabric** auf **Speicher**. Klicken Sie auf **Start** > **Ressourcen hinzufügen** > **Speichergeräte**, um den Assistenten zum Hinzufügen von Speichergeräten zu öffnen.
2. Wählen Sie auf der Seite **Wählen Sie einen Speicheranbietertyp aus** den Eintrag **Von einem SMI-S-Anbieter erkannte und verwaltete SAN- und NAS-Geräte** aus.

	![Anbietertyp](./media/site-recovery-vmm-san/provider-type.png)

3. Wählen Sie auf der Seite **Protokoll und Adresse des SMI-S-Speicheranbieters angeben** den Eintrag **SMI-S CIMXML** aus, und geben Sie die Einstellungen zum Herstellen einer Verbindung mit dem Anbieter an.
4. Geben Sie unter **IP-Adresse oder FQDN des Anbieters** und **TCP/IP-Port** die Einstellungen zum Herstellen einer Verbindung mit dem Anbieter an. Für SMI-S CIMXML können Sie nur eine SSL-Verbindung verwenden.

	![Anbieterverbindung](./media/site-recovery-vmm-san/connect-settings.png)

5. Geben Sie unter **Ausführendes Konto** ein ausführendes VMM-Konto an, das auf den Anbieter zugreifen kann, oder erstellen Sie ein neues Konto.
6. Auf der Seite **Informationen sammeln** versucht VMM, die Informationen zu den Speichergeräten automatisch zu erfassen. Klicken Sie zum Wiederholen der Erkennung auf **Anbieter durchsuchen**. Bei Erfolg des Erkennungsprozesses werden erkannte Speicherarrays und -pools, Hersteller, Modell und Kapazität auf der Seite aufgeführt.

	![Speicher ermitteln](./media/site-recovery-vmm-san/discover.png)

7. Wählen Sie unter **Wählen Sie Speicherpools aus, die Sie unter "Verwaltung" platzieren, und weisen Sie dann eine Klassifizierung zu** die Speicherpools aus, die VMM verwalten soll, und weisen Sie ihnen eine Klassifizierung zu. LUN-Informationen werden aus den Speicherpools importiert. Erstellen Sie LUNs basierend auf den zu schützenden Anwendungen, ihrer Kapazitätsanforderungen und Ihrer Anforderungen an die Elemente, die gemeinsam repliziert werden sollen.

	![Speicher klassifizieren](./media/site-recovery-vmm-san/classify.png)

### Erstellen von LUNs und Zuweisen von Speicher

1. Nach der Integration von SAN-Speicher in VMM können Sie logische Einheiten (Logical Units, LUNs) erstellen (bereitstellen):

	- [Auswählen einer Methode zum Erstellen von logischen Einheiten in VMM](https://technet.microsoft.com/library/gg610624.aspx)
	- [Bereitstellen von logischen Speichereinheiten in VMM](https://technet.microsoft.com/library/gg696973.aspx)

2. Weisen Sie anschließend dem Hyper-V-Hostcluster Speicherkapazität zu, damit VMM Daten auf virtuellen Computern im bereitgestellten Speicher bereitstellen kann:

	- Vor der Zuordnung von Speicher zum Cluster müssen Sie den Speicher der VMM-Hostgruppe zuordnen, in der sich der Cluster befindet. Weitere Informationen finden Sie unter [Zuordnen von logischen Speichereinheiten zu einer Hostgruppe](https://technet.microsoft.com/library/gg610686.aspx) sowie unter [Zuordnen von Speicherpools zu einer Hostgruppe](https://technet.microsoft.com/library/gg610635.aspx). </a>.
	- Ordnen Sie dem Cluster Speicherkapazität gemäß den Angaben unter [Konfigurieren von Speicher für einen Hyper-V-Hostcluster in VMM](https://technet.microsoft.com/library/gg610692.aspx) zu. </a>.

### Erstellen von Replikationsgruppen

Erstellen Sie eine Replikationsgruppe, die alle LUNs enthält, die gemeinsam repliziert werden sollen.

1. Öffnen Sie in der VMM-Konsole die Registerkarte **Replikationsgruppen** der Speicherarray-Eigenschaften, und klicken Sie auf **Neu**.
2. Erstellen Sie anschließend die Replikationsgruppe.

	![SAN-Replikationsgruppe](./media/site-recovery-vmm-san/rep-group.png)

### Einrichten der Netzwerke

Wenn Sie eine Netzwerkzuordnung konfigurieren möchten, gehen Sie wie folgt vor:

1. [Machen Sie sich mit der Netzwerkzuordnung vertraut](site-recovery-network-mapping.md).
2. Bereiten Sie VM-Netzwerke in VMM vor:

	- [Konfigurieren von logischen Netzwerken in VMM (Übersicht)](https://technet.microsoft.com/library/jj721568.aspx).
	- [Richten Sie VM-Netzwerke ein](https://technet.microsoft.com/library/jj721575.aspx).

## Schritt 2: Erstellen eines Tresors


1. Melden Sie sich über den VMM-Server, den Sie registrieren möchten, beim [Verwaltungsportal](https://portal.azure.com) an.

2. Erweitern Sie **Data Services** > **Recovery Services**, und klicken Sie auf **Site Recovery-Tresor**.

3. Klicken Sie auf **Neu erstellen** > **Schnellerfassung**.

4. Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird.

5. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Eine Liste mit den unterstützten Regionen finden Sie unter [Azure Site Recovery-Preisübersicht](http://go.microsoft.com/fwlink/?LinkId=389880) unter „Geografische Verfügbarkeit“.

6. Klicken Sie auf **Tresor erstellen**.

	![Neuer Tresor](./media/site-recovery-vmm-san/create-vault.png)

Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von **Recovery Services** als **Aktiv** angegeben.


### Registrieren der VMM-Server

1. Öffnen Sie auf der Seite **Recovery Services** die Seite „Schnellstart“. Schnellstart kann auch jederzeit über das Symbol geöffnet werden.

	![Schnellstart-Symbol](./media/site-recovery-vmm-san/quick-start-icon.png)

2. Wählen Sie in der Dropdownliste den Eintrag **Zwischen lokalen Hyper-V-Standorten mithilfe der Arrayreplikation** aus.

	![Registrierungsschlüssel](./media/site-recovery-vmm-san/select-san.png)


3. Laden Sie unter **VMM-Server vorbereiten** die aktuelle Version der Installationsdatei für den Azure Site Recovery-Anbieter herunter.
4. Führen Sie diese Datei auf dem VMM-Quellserver aus. Wenn VMM in einem Cluster bereitgestellt wird und Sie den Anbieter erstmals installieren, führen Sie die Installation auf einem aktiven Knoten durch und schließen Sie sie ab, um den VMM-Server im Tresor zu registrieren. Installieren Sie den Anbieter anschließend auf den anderen Knoten. Beachten Sie, dass Sie ein Upgrade des Anbieters auf allen Knoten durchführen müssen, da auf allen Knoten dieselbe Anbieterversion ausgeführt werden sollte.
5. Das Installationsprogramm führt eine **Voraussetzungsprüfung** durch und fordert die Berechtigung zum Beenden des VMM-Diensts an, um das Setup des Anbieters zu starten. Der VMM-Dienst wird nach Abschluss des Setups automatisch neu gestartet. Bei der Installation auf einem VMM-Cluster werden Sie aufgefordert, die Clusterrolle zu beenden.
6. Unter **Microsoft Update** können Sie Updates abonnieren. Mit dieser Einstellung werden Anbieterupdates gemäß Ihrer Microsoft Update-Richtlinie installiert.

	![Microsoft Updates](./media/site-recovery-vmm-san/ms-update.png)

7. Der Installationspfad ist auf **<SystemDrive>\\Programme\\Microsoft System Center 2012 R2\\Virtual Machine Manager\\bin** festgelegt. Klicken Sie auf die Schaltfläche „Installieren“, um die Installation des Anbieters zu starten.

	![InstallLocation](./media/site-recovery-vmm-san/install-location.png)

8. Klicken Sie nach der Installation des Anbieters auf die Schaltfläche „Registrieren“, um den Server im Tresor zu registrieren.

	![InstallComplete](./media/site-recovery-vmm-san/install-complete.png)

9. Geben Sie auf der Seite **Internetverbindung** an, wie sich der Anbieter auf dem VMM-Server mit dem Internet verbinden soll. Wählen Sie **Proxyeinstellungen des Systems verwenden** aus, um die Standard-Internetverbindungseinstellungen auf dem Server zu verwenden.

	![Interneteinstellungen](./media/site-recovery-vmm-san/proxy-details.png)

	- Wenn Sie einen benutzerdefinierten Proxy verwenden möchten, sollten Sie diesen vor der Installation des Anbieters einrichten. Wenn Sie benutzerdefinierte Proxyeinstellungen konfigurieren, wird ein Test ausgeführt, um die Proxyverbindung zu überprüfen.
	- Wenn Sie einen benutzerdefinierten Proxy verwenden oder Ihr Standardproxy eine Authentifizierung verlangt, müssen Sie die Proxydetails einschließlich der Proxyadresse und des Ports eingeben.
	- Der VMM-Server und die Hyper-V-Hosts sollten auf die folgenden URLs Zugriff haben:
		- *.hypervrecoverymanager.windowsazure.com
		- *.accesscontrol.windows.net
		- *.backup.windowsazure.com
		- *.blob.core.windows.net
		- *.store.core.windows.net
	- Lassen Sie die unter [IP-Bereiche des Azure-Rechenzentrums](https://www.microsoft.com/download/confirmation.aspx?id=41653) angegebenen IP-Adressen sowie das HTTPS-Protokoll (443) zu. Fügen Sie die IP-Adressbereiche der zu verwendenden Azure-Region sowie die IP-Adressbereiche der westlichen USA einer Positivliste hinzu.
	- Wenn Sie einen benutzerdefinierten Proxy verwenden, wird ein ausführendes VMM-Konto (DRAProxyAccount) automatisch mit den angegebenen Proxyanmeldeinformationen erstellt. Konfigurieren Sie den Proxyserver so, dass dieses Konto erfolgreich authentifiziert werden kann. In der VMM-Konsole können die Einstellungen des ausführenden VMM-Kontos geändert werden. Zu diesem Zweck öffnen Sie den Arbeitsbereich "Einstellungen", erweitern Sie "Sicherheit", klicken Sie auf "Ausführende Konten", und ändern Sie das Kennwort für DRAProxyAccount. Sie müssen den VMM-Dienst neu starten, damit diese Einstellung wirksam wird.

10. Wählen Sie unter **Registrierungsschlüssel** den Schlüssel aus, den Sie von Azure Site Recovery heruntergeladen und auf den VMM-Server kopiert haben.
11. Prüfen Sie unter **Tresorname** den Namen des Tresors, in dem der Server registriert wird. 

	![Serverregistrierung](./media/site-recovery-vmm-san/vault-creds.png)

12. Diese Verschlüsselungseinstellung kommt nur für das VMM-zu-Azure-Szenario zur Anwendung. Wenn Sie ausschließlich VMM-zu-VMM verwenden, können Sie diesen Bildschirm ignorieren.

	![Serverregistrierung](./media/site-recovery-vmm-san/encrypt.png)

13. Geben Sie unter **Servername** einen Anzeigenamen ein, um den VMM-Server im Tresor zu identifizieren. Geben Sie in einer Clusterkonfiguration den Rollennamen des VMM-Clusters an.
14. Geben Sie unter **Anfängliche Cloud-Metadatensynchronisierung** einen Anzeigenamen für den Server an, der im Tresor angezeigt wird. Wählen Sie außerdem aus, ob die Metadaten für alle Clouds auf dem VMM-Server mit dem Tresor synchronisiert werden sollen. Diese Aktion muss für jeden VMM-Server nur einmal ausgeführt werden. Wenn Sie nicht alle Clouds synchronisieren möchten, können Sie diese Einstellung deaktiviert lassen und in den Cloudeigenschaften in der VMM-Konsole jede Cloud einzeln synchronisieren.

	![Serverregistrierung](./media/site-recovery-vmm-san/friendly-name.png)

15. Klicken Sie auf **Weiter**, um den Prozess abzuschließen. Nach der Registrierung werden die Metadaten vom VMM-Server von Azure Site Recovery abgerufen. Der Server wird im Tresor auf der Registerkarte *VMM-Server* der Seite **Server** angezeigt.

### Installation über die Befehlszeile

Der Azure Site Recovery-Anbieter kann auch über die folgende Befehlszeile installiert werden. Mit dieser Methode kann der Anbieter in Server Core für Windows Server 2012 R2 installiert werden.

1. Laden Sie die Installationsdatei und den Registrierungsschlüssel des Anbieters in einen Ordner herunter, z. B. in "C:\\ASR".
2. Beenden Sie den System Center Virtual Machine Manager-Dienst.
3. Extrahieren Sie das Installationsprogramm für den Anbieter, indem Sie Folgendes über eine Eingabeaufforderung mit **Administratorrechten** ausführen:

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Installieren Sie den Anbieter, indem Sie Folgendes ausführen:

		C:\ASR> setupdr.exe /i

5. Registrieren Sie den Anbieter, indem Sie Folgendes ausführen:

    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

Die Parameter lauten:

 - **/Credentials**: erforderlicher Parameter zum Angeben des Speicherorts der Registrierungsschlüsseldatei.  
 - **/FriendlyName**: erforderlicher Parameter für den Namen des Hyper-V-Hostservers, der im Azure Site Recovery-Portal angezeigt wird.
 - **/EncryptionEnabled**: optionaler Parameter, der nur im VMM-zu-Azure-Szenario verwendet werden muss, wenn Sie die inaktiven virtuellen Computer in Azure verschlüsseln möchten. Stellen Sie sicher, dass der Name der angegebenen Datei die Dateierweiterung **.pfx** aufweist.
 - **/proxyAddress**: optionaler Parameter, der die Adresse des Proxyservers angibt.
 - **/proxyport**: optionaler Parameter, der den Port des Proxyservers angibt.
 - **/proxyUsername**: optionaler Parameter, der den Proxybenutzernamen angibt (sofern der Proxy eine Authentifizierung erfordert).
 - **/proxyPassword**: optionaler Parameter, der das Kennwort für die Authentifizierung mit dem Proxyserver angibt (sofern der Proxy eine Authentifizierung erfordert).


## Schritt 3: Zuordnen von Speicherarrays und -pools

Ordnen Sie Arrays zu, um anzugeben, welcher sekundäre Speicherpool Replikationsdaten vom primären Pool empfangen soll. Es empfiehlt sich, den Speicher vor der Konfiguration des Cloudschutzes zuzuordnen, da die Zuordnungsinformationen verwendet werden, wenn Sie den Schutz für Replikationsgruppen aktivieren.

Prüfen Sie, bevor Sie beginnen, ob im Tresor Clouds vorhanden sind. Clouds werden erkannt, wenn Sie bei der Installation des Anbieters in der VMM-Konsole in den Cloud-Einstellungen auf der Registerkarte **Allgemein** die Option zum Synchronisieren aller Clouds oder einer bestimmten Cloud auswählen. Ordnen Sie dann Speicherarrays und -pools wie folgt zu:

1. Klicken Sie auf **Ressourcen** > **Serverspeicher** > **Quell- und Zielarrays zuordnen**. ![Serverregistrierung](./media/site-recovery-vmm-san/storage-map.png)
2. Wählen Sie die Speicherarrays für den primären Standort aus, und ordnen Sie sie Speicherarrays für den sekundären Standort zu.
3.  Ordnen Sie innerhalb der Arrays Quell- und Zielspeicherpools zu. Wählen Sie hierzu unter **Speicherpools** einen zuzuordnenden Quell- und Zielspeicherpool aus.

	![Serverregistrierung](./media/site-recovery-vmm-san/storage-map-pool.png)

## Schritt 4: Konfigurieren der Cloudschutzeinstellungen

Nachdem Sie die VMM-Server registriert haben, können Sie Schutzeinstellungen für die Clouds konfigurieren. Sie haben bei der Installation des Anbieters die Option **Cloud-Daten mit Tresor synchronisieren** aktiviert. Deshalb werden alle Clouds auf dem VMM-Server im Tresor auf der Registerkarte <b>Geschützte Elemente</b> angezeigt.

![Veröffentlichte Cloud](./media/site-recovery-vmm-san/clouds-list.png)

1. Klicken Sie auf der Seite „Schnellstart“ auf **Schutz für VMM-Clouds einrichten**.
2. Wählen Sie auf der Registerkarte **Geschützte Elemente** die Cloud aus, die Sie konfigurieren möchten, und gehen Sie zur Registerkarte **Konfiguration**. Beachten Sie Folgendes:
3. Wählen Sie unter <b>Ziel</b> die Option <b>VMM</b> aus.
4. Wählen Sie unter <b>Zielspeicherort</b> den lokalen VMM-Server aus, der die Cloud verwaltet, die Sie für die Wiederherstellung verwenden möchten.
5. Wählen Sie unter <b>Zielcloud</b> die Zielcloud aus, die Sie für das Failover virtueller Computer in der Quellcloud verwenden möchten. Beachten Sie Folgendes:
	- Es wird empfohlen, eine Zielcloud auszuwählen, die die Voraussetzungen für die Wiederherstellung der virtuellen Computer erfüllt, die Sie schützen möchten.
	- Eine Cloud kann nur zu einem einzigen Cloudpaar gehören – entweder als primäre oder als Zielcloud.
6. Azure Site Recovery prüft, ob Clouds Zugriff auf für die SAN-Replikation geeigneten Speicher haben und ob für die Speicherarrays ein Peering erfolgt ist. Teilnehmende Arraypeers werden angezeigt.
7. Wählen Sie nach erfolgreicher Überprüfung unter **Replikationstyp** die Option **SAN** aus.

<p>Nachdem Sie die Einstellungen gespeichert haben, wird ein Auftrag erstellt, der auf der Registerkarte <b>Aufträge</b> überwacht werden kann. Die Cloudeinstellungen können auf der Registerkarte <b>Konfigurieren</b> bearbeitet werden. Wenn Sie den Zielspeicherort oder die Zielcloud ändern möchten, müssen Sie die Cloudkonfiguration entfernen und die Cloud anschließend neu konfigurieren.</p>

## Schritt 5: Aktivieren der Netzwerkzuordnung

1. Klicken Sie auf der Seite „Schnellstart“ auf **Netzwerke zuordnen**.
2. Wählen Sie den VMM-Quellserver aus, von dem aus Sie Netzwerke zuordnen möchten, und dann den Ziel-VMM-Server, dem die Netzwerke zugeordnet werden soll. Die Liste der Quellnetzwerke und ihre zugeordneten Zielnetzwerke werden angezeigt. Für Netzwerke, die derzeit nicht zugeordnet sind, wird ein leerer Wert angezeigt. Klicken Sie auf das Informationssymbol neben den Namen des Quell- und Zielnetzwerks, um die Subnetze für jedes Netzwerk anzuzeigen.
3. Wählen Sie unter **Netzwerk auf Quelle** ein Netzwerk aus, und klicken Sie anschließend auf **Zuordnen**. Der Dienst erkennt die VM-Netzwerke auf dem Zielserver und zeigt sie an.

	![SAN-Architektur](./media/site-recovery-vmm-san/network-map1.png)

4. Wählen Sie im Dialogfeld eines der VM-Netzwerke des VMM-Zielservers aus.

	![SAN-Architektur](./media/site-recovery-vmm-san/network-map2.png)

5. Wenn Sie ein Zielnetzwerk auswählen, werden die geschützten Clouds angezeigt, die das Quellnetzwerk verwenden. Die verfügbaren Zielnetzwerke, die den zum Schutz verwendeten Clouds zugeordnet sind, werden ebenfalls angezeigt. Es wird empfohlen, ein Zielnetzwerk auszuwählen, das für alle für den Schutz verwendeten Clouds verfügbar ist.
6.  Aktivieren Sie das Kontrollkästchen, um den Zuordnungsprozess abzuschließen. Ein Auftrag beginnt mit der Verfolgung des Zuordnungsprozesses. Dieser kann auf der Registerkarte **Aufträge** angezeigt werden.


## Schritt 6: Aktivieren der Replikation für Replikationsgruppen</h3>

Bevor Sie Schutz für virtuelle Computer aktivieren können, müssen Sie die Replikation für Speicherreplikationsgruppen aktivieren.

1. Öffnen Sie im Azure Site Recovery-Portal auf der Eigenschaftenseite der primären Cloud die Registerkarte **Virtuelle Computer**. Klicken Sie auf **Replikationsgruppe hinzufügen**.
2. Wählen Sie eine oder mehrere VMM-Replikationsgruppen aus, die der Cloud zugeordnet sind, überprüfen Sie Quell- und Zielarrays, und geben Sie die Replikationsfrequenz an.

Nach diesem Vorgang stellt Azure Site Recovery gemeinsam mit VMM und den SMI-S-Anbietern die Speicher-LUNs für den Zielstandort bereit und aktiviert die Speicherreplikation. Falls die Replikationsgruppe bereits repliziert wurde, verwendet Azure Site Recovery die vorhandene Replikationsbeziehung erneut und aktualisiert die Informationen in Azure Site Recovery.

## Schritt 7: Aktivieren des Schutzes für virtuelle Computer

Nach der Replikation einer Speichergruppe aktivieren Sie den Schutz für virtuelle Computer in der VMM-Konsole mittels einer der folgenden Methoden:

- **Neuer virtueller Computer**: Wenn Sie in der VMM-Konsole einen neuen virtuellen Computer erstellen, aktivieren Sie den Azure Site Recovery-Schutz, und ordnen Sie den virtuellen Computer der Replikationsgruppe zu. Bei Wahl dieser Option arbeitet VMM mit der intelligenten Platzierung, um den Speicher virtueller Computer optimal in den LUNs der Replikationsgruppe zu platzieren. Azure Site Recovery orchestriert die Erstellung eines virtuellen Schattencomputers am sekundären Standort und weist Kapazität so zu, dass replizierte virtuelle Computer nach einem Failover gestartet werden können.
- **Vorhandener virtueller Computer**: Wenn in VMM bereits ein virtueller Computer bereitgestellt ist, können Sie den Azure Site Recovery-Schutz aktivieren und eine Speichermigration zu einer Replikationsgruppe durchführen. Im Anschluss erkennen VMM und Azure Site Recovery den neuen virtuellen Computer und beginnen mit dessen Verwaltung in Azure Site Recovery zu Schutzzwecken. Ein virtueller Schattencomputer wird am sekundären Standort erstellt. Diesem wird Kapazität so zugewiesen, dass der replizierte virtuelle Computer nach einem Failover gestartet werden kann.

	![Schutz aktivieren](./media/site-recovery-vmm-san/enable-protect.png)

Nachdem virtuelle Computer zu Schutzzwecken aktiviert wurden, werden sie in der Azure Site Recovery-Konsole angezeigt. Sie können die Eigenschaften virtueller Computer anzeigen, den Status nachverfolgen und für Replikationsgruppen, die mehrere virtuelle Computer enthalten, ein Failover ausführen. Beachten Sie, dass bei der SAN-Replikation für alle einer Replikationsgruppe zugeordneten virtuellen Computer das Failover gemeinsam erfolgen muss. Der Grund ist, dass das Failover zuerst auf der Speicherebene erfolgt. Wichtig ist es, dass Sie Ihre Replikationsgruppen ordnungsgemäß gruppieren und nur einander zugeordnete virtuelle Computer gemeinsam platzieren.

Auf der Registerkarte **Aufträge** können Sie den Fortschritt der Schutzaktivierung und der ersten Replikation verfolgen. Nachdem der Auftrag zum Abschließen des Schutzes ausgeführt wurde, ist der virtuelle Computer bereit für das Failover.

![Schutzauftrag für virtuellen Computer](./media/site-recovery-vmm-san/job-props.png)

## Schritt 8: Testen der Bereitstellung

Testen Sie Ihre Bereitstellung, um sicherzustellen, dass das Failover von virtuellen Computern und Daten wie erwartet erfolgt. Hierzu erstellen Sie einen Wiederherstellungsplan durch Auswählen von Replikationsgruppen. Führen Sie dann ein Testfailover gemäß dem Plan aus.

1. Klicken Sie auf der Registerkarte **Wiederherstellungspläne** auf **Wiederherstellungsplan erstellen**.
2. Geben Sie einen Namen für den Wiederherstellungsplan sowie die VMM-Quell- und Zielserver an. Der Quellserver muss virtuelle Computer aufweisen, die für Failover und Wiederherstellung aktiviert sind. Wählen Sie **SAN** aus, um nur Clouds anzuzeigen, die für die SAN-Replikation konfiguriert sind.
3.
	![Wiederherstellungsplan erstellen](./media/site-recovery-vmm-san/r-plan.png)

4. Wählen Sie unter **Virtuellen Computer auswählen** Replikationsgruppen aus. Alle der Replikationsgruppe zugeordneten virtuellen Computer werden ausgewählt und dem Wiederherstellungsplan hinzugefügt. Diese virtuellen Computer werden der Standardgruppe im Wiederherstellungsplan, Gruppe 1, hinzugefügt. Sie können bei Bedarf weitere Gruppen hinzufügen. Beachten Sie, dass virtuelle Maschinen nach der Replikation gemäß der Reihenfolge der Wiederherstellungsplangruppen gestartet werden.

	![Virtuelle Computer hinzufügen](./media/site-recovery-vmm-san/r-plan-vm.png)
5. Erstellte Wiederherstellungspläne werden auf der Registerkarte **Wiederherstellungspläne** angezeigt.
6. Wählen Sie auf der Registerkarte **Wiederherstellungspläne** den gewünschten Wiederherstellungsplan aus, und klicken Sie auf **Testfailover**.
7. Wählen Sie auf der Seite **Testfailover bestätigen** den Eintrag **Kein** aus. Bei Aktivierung dieser Option werden nach einem Failover die virtuellen Computer im Replikat mit keinem Netzwerk verbunden. Es wird getestet, ob das Failover für den virtuellen Computer wie erwartet erfolgt, ohne dass Ihre Netzwerkumgebung für die Replikation getestet wird. Sehen Sie sich an, wie [ein Testfailover ausgeführt wird](site-recovery-failover.md#run-a-test-failover), um mehr über das Verwenden verschiedener Netzwerkoptionen zu erfahren.


	![Testnetzwerk auswählen](./media/site-recovery-vmm-san/test-fail1.png)


8. Der virtuelle Computer für den Test wird auf dem Host erstellt, auf dem auch der replizierte virtuelle Computer vorhanden ist. Er wird nicht der Cloud hinzugefügt, in der sich der replizierte virtuelle Computer befindet.
9. Nach der Replikation hat der replizierte virtuelle Computer eine IP-Adresse, die nicht der IP-Adresse des primären virtuellen Computers entspricht. Wenn Sie Adressen über DHCP ausstellen, wird DNS automatisch aktualisiert. Wenn Sie nicht DHCP verwenden und sicherstellen möchten, dass die Adressen identisch sind, müssen Sie verschiedene Skripts ausführen.
10. Führen Sie diese Beispielskript aus, um die IP-Adresse abzurufen.

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

11. Führen Sie dieses Beispielskript aus, um DNS zu aktualisieren, wobei Sie die IP-Adresse angeben, die Sie mithilfe des vorherigen Beispielskripts abgerufen haben.

		[string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## Nächste Schritte

Nachdem Sie ein Testfailover ausgeführt haben, um die richtige Funktionsweise der Umgebung zu überprüfen, können Sie sich die [Informationen](site-recovery-failover.md) zu den unterschiedlichen Arten von Failovern durchlesen.

<!---HONumber=AcomDC_0121_2016-->
