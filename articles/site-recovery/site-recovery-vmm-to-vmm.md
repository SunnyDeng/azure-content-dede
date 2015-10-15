<properties 
	pageTitle="Einrichten von Schutz zwischen lokalen VMM-Standorten" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung von virtuellen Hyper-V-Computern zwischen lokalen VMM-Standorten." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="raynew"/>

# Einrichten von Schutz zwischen lokalen VMM-Standorten


## Übersicht


Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer in einer Vielzahl von Bereitstellungsszenarios aufeinander abgestimmt werden. Eine vollständige Liste der Bereitstellungsszenarios finden Sie unter [Übersicht über Azure Site Recovery](site-recovery-overview.md).

Dieser Szenarioleitfaden beschreibt die Bereitstellung von Site Recovery zum Organisieren und Automatisieren von Schutz für Workloads auf virtuellen Computern auf Hyper-V-Hostservern, die sich in privaten VMM-Clouds befinden. In diesem Szenario werden virtuelle Computer von einem primären VMM-Standort mithilfe von Hyper-V-Replikaten auf einen sekundären VMM-Standort repliziert.

Das Handbuch enthält Informationen zu Voraussetzungen für das Szenario und zeigt, wie Sie einen Site Recovery-Tresor einrichten, den Azure Site Recovery-Anbieter auf dem Quell- und dem Ziel-VMM-Server installieren, den Server im Tresor registrieren, Schutzeinstellungen für VMM-Clouds konfigurieren, die auf alle geschützten virtuellen Computer angewendet werden, und den Schutz für diese virtuellen Computer aktivieren. Zum Schluss testen Sie das Failover, um sicherzustellen, dass alles wie erwartet funktioniert.

Sollten beim Einrichten dieses Szenarios Probleme auftreten, besuchen Sie das [Azure Recovery Services-Forum](http://go.microsoft.com/fwlink/?LinkId=313628).


## Vorbereitung
Stellen Sie sicher, dass diese Voraussetzungen erfüllt werden:
### Voraussetzungen für Azure

- Sie benötigen ein [Microsoft Azure](http://azure.microsoft.com/)-Konto. Beginnen Sie mit einer [kostenlosen Testversion](http://aka.ms/try-azure), falls Sie kein Konto haben. Darüber hinaus können Sie sich über die [Azure Site Recovery-Preise](http://go.microsoft.com/fwlink/?LinkId=378268) informieren.
- Um zu verstehen, wie Informationen und Daten verwendet werden, lesen Sie die [Datenschutzbestimmungen zu Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899) und die zusätzlichen Informationen zum <a href="#privacy">Datenschutz für Site Recovery</a> am Ende dieses Themas.

### VMM-Voraussetzungen
- Sie benötigen mindestens einen VMM-Server.
- Auf dem VMM-Server sollte mindestens System Center 2012 SP1 mit den neuesten kumulativen Updates ausgeführt werden. 
- Auf allen VMM-Servern mit virtuellen Computern, die Sie schützen möchten, muss der Azure Site Recovery-Anbieter ausgeführt werden. Dieser wird bei der Bereitstellung von Azure Site Recovery installiert.
- Wenn Sie den Schutz mit einem einzelnen VMM-Server einrichten möchten, müssen mindestens zwei Clouds auf dem Server konfiguriert sein.
- Wenn Sie den Schutz mit zwei VMM-Servern bereitstellen möchten, muss für jeden Server mindestens eine Cloud auf dem primären VMM-Server, der geschützt werden soll, und eine Cloud auf dem sekundären VMM-Server konfiguriert sein, der für Schutz und Wiederherstellung verwendet werden soll.
- Für alle VMM-Clouds muss das Hyper-V-Kapazitätsprofil eingestellt sein.
- Die Quellcloud, die Sie schützen möchten, muss Folgendes enthalten:
	- Eine oder mehrere VMM-Hostgruppen.
	- Einen oder mehrere Hyper-V-Hostserver in jeder Hostgruppe.
	- Einen oder mehrere virtuelle Computer auf dem Hostserver. 
- Erfahren Sie mehr über das Einrichten von VMM-Clouds:
	- Weitere Informationen über private VMM-Clouds erhalten Sie unter [Private Cloud-Neuerungen mit System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) und unter [VMM 2012 und die Clouds](http://go.microsoft.com/fwlink/?LinkId=324956). 
	- Informationen zum [Planen der VMM-Infrastruktur](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- Sobald Ihre Cloudfabricelemente eingerichtet sind, finden Sie Informationen zum Erstellen privater Clouds unter [Erstellen einer privaten Cloud in VMM](http://go.microsoft.com/fwlink/?LinkId=324953) und [Exemplarische Vorgehensweise: Erstellen von privaten Clouds mit System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/?LinkId=324954)

### Hyper-V-Voraussetzungen

- Auf dem Host- und dem Ziel-Hyper-V-Server muss mindestens Windows Server 2012 mit Hyper-V-Rolle ausgeführt werden, und die neuesten Updates müssen installiert sein.
- Wenn Sie Hyper-V in einem Cluster ausführen, wird der Clusterbroker nicht automatisch erstellt, wenn Sie einen Cluster mit statischen IP-Adressen verwenden. Sie müssen den Clusterbroker manuell konfigurieren. Anweisungen finden Sie unter [Configure Replica Broker Role (Cluster to Cluster Replication)](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx) (in englischer Sprache).
- Alle Hyper-V-Hostserver oder Cluster, deren Schutz Sie verwalten möchten, müssen in einer VMM-Cloud enthalten sein.

Die folgende Abbildung zeigt die verschiedenen Kommunikationskanäle und Ports, die Azure Site Recovery für die Orchestrierung und Replikation verwendet.

![E2E-Topologie](./media/site-recovery-vmm-to-vmm/E2ETopology.png)

 
### Voraussetzungen für die Netzwerkzuordnung
Die Netzwerkzuordnung stellt sicher, dass die virtuelle Replikatcomputer nach einem Failover optimal auf Hyper-V-Hostservern platziert werden und sich mit den entsprechenden VM-Netzwerken verbinden können. Wenn Sie keine Netzwerkzuordnung konfigurieren, werden virtuelle Replikatcomputer nach einem Failover nicht mit VM-Netzwerken verbunden. Wenn Sie eine Netzwerkzuordnung bereitstellen möchten, benötigen Sie Folgendes:

- Die virtuellen Computer, die Sie auf dem VMM-Quellserver schützen möchten, sollten mit einem VM-Netzwerk verbunden sein. Dieses Netzwerk sollte mit einem logischen Netzwerk verbunden sein, das der Cloud zugeordnet ist.
- Für die Zielcloud auf dem sekundären VMM-Server, die Sie für die Wiederherstellung verwenden, sollte ein entsprechendes VM-Netzwerk konfiguriert sein. Dieses sollte wiederum mit einem entsprechenden logischen Netzwerk verknüpft sein, das der Zielcloud zugeordnet ist.

Informationen zur Netzwerkzuordnung finden Sie unter:

- [Konfigurieren von logischen Netzwerken in VMM](http://go.microsoft.com/fwlink/?LinkId=386307)
- [Konfiguration von VM-Netzwerken und Gateways in VMM](http://go.microsoft.com/fwlink/?LinkId=386308)

### Voraussetzungen für die Speicherzuordnung
Standardmäßig werden beim Replizieren eines virtuellen Computers auf einem Hyper-V-Host-Quellserver auf einen Hyper-V-Host-Zielserver die replizierten Daten am Standardspeicherort gespeichert, der für den Hyper-V-Zielhost in Hyper-V-Manager angegeben wurde. Um die Ablage von Replikationsdaten zu steuern, können Sie eine Speicherzuordnung konfigurieren. Zu diesem Zweck müssen Sie die Speicherklassifizierungen auf dem Quell- und dem Ziel-VMM-Server einrichten, bevor Sie mit der Bereitstellung beginnen.
Anweisungen hierzu finden Sie unter [Erstellen von Speicher-Klassifizierungen in VMM](http://go.microsoft.com/fwlink/?LinkId=400937).


## Schritt 1: Erstellen eines Site Recovery-Tresors

1. Melden Sie sich über den VMM-Server, den Sie registrieren möchten, beim [Verwaltungsportal](https://portal.azure.com) an.

2. Erweitern Sie **Data Services** > **Recovery Services**, und klicken Sie auf **Site Recovery-Tresor**.


3. Klicken Sie auf **Neu erstellen** > **Schnellerfassung**.
	
4. Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird.

5. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Eine Liste mit den unterstützten Regionen finden Sie unter [Azure Site Recovery-Preise](http://go.microsoft.com/fwlink/?LinkId=389880) unter „Geografische Verfügbarkeit“.

6. Klicken Sie auf **Tresor erstellen**.

	![Tresor erstellen](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CreateVault.png)

Überprüfen Sie in der Statusleiste, ob der Tresor erstellt wurde. Der Tresor wird auf der Hauptseite von Recovery Services als **aktiv** aufgelistet.

## Schritt 2: Generieren eines Tresorregistrierungsschlüssels

Generieren Sie einen Registrierungsschlüssel im Tresor. Nachdem Sie den Azure Site Recovery-Anbieter heruntergeladen und auf dem VMM-Server installiert haben, verwenden Sie diesen Schlüssel, um den VMM-Server im Tresor zu registrieren.

1. Klicken Sie auf der Seite **Recovery Services** auf den Tresor, um die Seite „Schnellstart“ zu öffnen. Schnellstart kann auch jederzeit über das Symbol geöffnet werden.

	![Schnellstart-Symbol](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_QuickStartIcon.png)

2. Wählen Sie in der Dropdownliste den Eintrag **Zwischen zwei lokalen Hyper-V-Sites** aus.
3. Klicken Sie unter **VMM-Server vorbereiten** auf **Registrierungsschlüsseldatei erstellen**. Die Schlüsseldatei wird automatisch generiert und ist nach ihrer Erstellung für 5 Tage gültig. Wenn Sie nicht vom VMM-Server aus auf das Azure-Portal zugreifen, müssen Sie diese Datei auf den Server kopieren. 

	![Registrierungsschlüssel](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_E2ERegisterKey.png)
	
## Schritt 3: Installieren des Azure Site Recovery-Anbieters

4. Klicken Sie auf der Seite *Schnellstart* unter **VMM-Server vorbereiten** auf *Microsoft Azure Site Recovery-Anbieter für die Installation auf VMM-Servern herunterladen*, um die neueste Version der Anbieter-Installationsdatei herunterzuladen.

2. Führen Sie diese Datei auf dem VMM-Quellserver aus. Wenn VMM in einem Cluster bereitgestellt wird und Sie den Anbieter erstmals installieren, führen Sie die Installation auf einem aktiven Knoten durch und schließen Sie sie ab, um den VMM-Server im Tresor zu registrieren. Installieren Sie den Anbieter anschließend auf den anderen Knoten. Beachten Sie, dass Sie ein Upgrade des Anbieters auf allen Knoten durchführen müssen, da auf allen Knoten dieselbe Anbieterversion ausgeführt werden sollte.


3. Das Installationsprogramm führt eine **Voraussetzungsprüfung** durch und fordert die Berechtigung zum Beenden des VMM-Diensts an, um das Setup des Anbieters zu starten. Der VMM-Dienst wird nach Abschluss des Setups automatisch neu gestartet. Bei der Installation auf einem VMM-Cluster werden Sie aufgefordert, die Clusterrolle zu beenden.

4. Unter **Microsoft Update** können Sie Updates abonnieren. Mit dieser Einstellung werden Anbieterupdates gemäß Ihrer Microsoft Update-Richtlinie installiert.

	![Microsoft Updates](./media/site-recovery-vmm-to-vmm/VMMASRInstallMUScreen.png)


1.  Der Installationspfad ist auf **<SystemDrive>\\Programme\\Microsoft System Center 2012 R2\\Virtual Machine Manager\\bin** festgelegt. Klicken Sie auf die Schaltfläche "Installieren", um die Installation des Anbieters zu starten.
	![InstallLocation](./media/site-recovery-vmm-to-vmm/VMMASRInstallLocationScreen.png)



1. Klicken Sie nach der Installation des Anbieters auf die Schaltfläche "Registrieren", um den Server im Tresor zu registrieren.
	![InstallComplete](./media/site-recovery-vmm-to-vmm/VMMASRInstallComplete.png)

5. Geben Sie auf der Seite **Internetverbindung** an, wie sich der Anbieter auf dem VMM-Server mit dem Internet verbinden soll. Wählen Sie *Proxyeinstellungen des Systems verwenden* aus, um die Standard-Internetverbindungseinstellungen auf dem Server zu verwenden.

	![Interneteinstellungen](./media/site-recovery-vmm-to-vmm/VMMASRRegisterProxyDetailsScreen.png)
	- Wenn Sie einen benutzerdefinierten Proxy verwenden möchten, sollten Sie diesen vor der Installation des Anbieters einrichten. Wenn Sie benutzerdefinierte Proxyeinstellungen konfigurieren, wird ein Test ausgeführt, um die Proxyverbindung zu überprüfen.
	- Wenn Sie einen benutzerdefinierten Proxy verwenden oder Ihr Standardproxy eine Authentifizierung erfordert, müssen Sie die Proxydetails einschließlich der Proxyadresse und des Proxyports eingeben.
	- Der VMM-Server und die Hyper-V-Hosts müssen auf die folgenden URLs Zugriff haben.
		- *.hypervrecoverymanager.windowsazure.com
		- *.accesscontrol.windows.net
		- *.backup.windowsazure.com
		- *.blob.core.windows.net
		- *.store.core.windows.net
	- Erlauben Sie die unter [IP-Bereiche des Azure-Rechenzentrums](http://go.microsoft.com/fwlink/?LinkId=511094) beschriebenen IP-Adressen und das HTTPS (443)-Protokoll. Fügen Sie die IP-Adressbereiche der zu verwendenden Azure-Region sowie die IP-Adressbereiche der westlichen USA einer Positivliste hinzu.

	- Wenn Sie einen benutzerdefinierten Proxy verwenden, wird ein ausführendes VMM-Konto (DRAProxyAccount) automatisch mit den angegebenen Proxyanmeldeinformationen erstellt. Konfigurieren Sie den Proxyserver so, dass dieses Konto erfolgreich authentifiziert werden kann. In der VMM-Konsole können die Einstellungen des ausführenden VMM-Kontos geändert werden. Zu diesem Zweck öffnen Sie den Arbeitsbereich "Einstellungen", erweitern Sie "Sicherheit", klicken Sie auf "Ausführende Konten", und ändern Sie das Kennwort für DRAProxyAccount. Sie müssen den VMM-Dienst neu starten, damit diese Einstellung wirksam wird.

6. Wählen Sie unter **Registrierungsschlüssel** den Schlüssel aus, den Sie von Azure Site Recovery heruntergeladen und auf den VMM-Server kopiert haben.
7. Prüfen Sie unter **Tresorname** den Namen des Tresors, in dem der Server registriert wird. Klicken Sie auf *Weiter*.


	![Serverregistrierung](./media/site-recovery-vmm-to-vmm/VMMASRRegisterVaultCreds.png)

9. Diese Einstellung kommt nur für das VMM-zu-Azure-Szenario zur Anwendung. Wenn Sie ausschließlich VMM-zu-VMM verwenden, können Sie diesen Bildschirm ignorieren.

	![Serverregistrierung](./media/site-recovery-vmm-to-vmm/VMMASRRegisterEncryptionScreen.png)

8. Geben Sie unter **Servername** einen Anzeigenamen ein, um den VMM-Server im Tresor zu identifizieren. Geben Sie in einer Clusterkonfiguration den Rollennamen des VMM-Clusters an.

8. Wählen Sie unter **Erste Cloudmetadaten-Synchronisierung** aus, ob Sie Metadaten für alle Clouds auf dem VMM-Server mit dem Tresor synchronisieren möchten. Diese Aktion muss für jeden VMM-Server nur einmal ausgeführt werden. Wenn Sie nicht alle Clouds synchronisieren möchten, können Sie diese Einstellung deaktiviert lassen und die Clouds in den Cloudeigenschaften in der VMM-Konsole einzeln synchronisieren.
	![Serverregistrierung](./media/site-recovery-vmm-to-vmm/VMMASRRegisterFriendlyName.png)


8. Klicken Sie auf *Weiter*, um den Prozess abzuschließen. Nach der Registrierung werden die Metadaten vom VMM-Server von Azure Site Recovery abgerufen. Der Server wird im Tresor auf der Registerkarte *VMM-Server* der Seite **Server** angezeigt.

>[AZURE.NOTE]Der Azure Site Recovery-Anbieter kann auch über die folgende Befehlszeile installiert werden. Mit dieser Methode kann der Anbieter in Server Core für Windows Server 2012 R2 installiert werden.
>
>1. Laden Sie die Installationsdatei und den Registrierungsschlüssel des Anbieters in einen Ordner herunter, z. B. in "C:\\ASR".
>2. Beenden Sie den System Center Virtual Machine Manager-Dienst.
>3. Extrahieren Sie das Installationsprogramm für den Anbieter, indem Sie die folgenden Befehle über eine Befehlszeile mit **Administratorrechten** ausführen. 
>
    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
>4. Installieren Sie den Anbieter mithilfe des folgenden Befehls:
>
		C:\ASR> setupdr.exe /i
>5. Registrieren Sie den Anbieter mithilfe des folgenden Befehls:
>
    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         
 ####Parameter für die Installation über die Befehlszeile####
>
 - **/Credentials**: erforderlicher Parameter zum Angeben des Speicherorts der Registrierungsschlüsseldatei.  
 - **/FriendlyName**: erforderlicher Parameter für den Namen des Hyper-V-Hostservers, der im Azure Site Recovery-Portal angezeigt wird.
 - **/EncryptionEnabled**: optionaler Parameter, der nur im VMM-zu-Azure-Szenario verwendet werden muss, wenn Sie die inaktiven virtuellen Computer in Azure verschlüsseln möchten. Stellen Sie sicher, dass der Name der angegebenen Datei die Dateierweiterung **.pfx** aufweist.
 - **/proxyAddress**: optionaler Parameter, der die Adresse des Proxyservers angibt.
 - **/proxyport**: optionaler Parameter, der den Port des Proxyservers angibt.
 - **/proxyUsername**: optionaler Parameter, der den Proxybenutzernamen angibt (sofern der Proxy eine Authentifizierung erfordert).
 - **/proxyPassword**: optionaler Parameter, der das Kennwort für die Authentifizierung mit dem Proxyserver angibt (sofern der Proxy eine Authentifizierung erfordert).  

## Schritt 4: Konfigurieren der Cloudschutzeinstellungen

Nachdem Sie die VMM-Server registriert haben, können Sie Schutzeinstellungen für die Clouds konfigurieren. Wenn Sie bei der Installation des Anbieters die Option **Clouddaten mit Tresor synchronisieren** aktiviert haben, werden alle Clouds auf dem VMM-Server auf der Registerkarte **Geschützte Elemente** im Tresor angezeigt. Andernfalls können Sie über die Registerkarte **Allgemein** auf der Eigenschaftenseite der Cloud in der VMM-Konsole eine bestimmte Cloud mit Azure Site Recovery synchronisieren.

![Veröffentlichte Cloud](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudsList.png)

1. Klicken Sie auf der Seite „Schnellstart“ auf **Schutz für VMM-Clouds einrichten**.
2. Wählen Sie auf der Registerkarte **VMM-Clouds** die Cloud aus, die Sie konfigurieren möchten, und wechseln Sie zur Registerkarte **Konfiguration**. 
3. Wählen Sie unter **Ziel** **VMM** aus.
4. Wählen Sie unter **Zielspeicherort** den lokalen VMM-Server aus, der die Cloud verwaltet, die Sie für die Wiederherstellung verwenden möchten.
4. Wählen Sie unter **Zielcloud** die Zielcloud aus, die Sie für das Failover virtueller Computer in der Quellcloud verwenden möchten. Beachten Sie Folgendes:
	- Es wird empfohlen, eine Zielcloud auszuwählen, die die Voraussetzungen für die Wiederherstellung der virtuellen Computer erfüllt, die Sie schützen möchten.
	- Eine Cloud kann nur zu einem einzigen Cloudpaar gehören – entweder als primäre oder als Zielcloud.
6. Geben Sie unter **Kopierhäufigkeit** an, wie oft die Daten zwischen dem Quell- und dem Zielspeicherort synchronisiert werden sollen. Beachten Sie, dass diese Einstellung nur relevant ist, wenn auf dem Hyper-V-Host Windows Server 2012 R2 ausgeführt wird. Für andere Server wird die Standardeinstellung "5 Minuten" verwendet.
7. Geben Sie unter **Zusätzliche Wiederherstellungspunkte** an, ob zusätzliche Wiederherstellungspunkte erstellt werden sollen. Wenn die Standardeinstellung 0 (Null) aktiviert ist, wird nur der letzte Wiederherstellungspunkt für einen primären virtuellen Computer auf einem Replikathostserver gespeichert. Beachten Sie, dass für die Aktivierung mehrerer Wiederherstellungspunkte zusätzlicher Speicherplatz für die Momentaufnahmen erforderlich ist, die an jedem Wiederherstellungspunkt gespeichert werden. Standardmäßig werden Wiederherstellungspunkte jede Stunde erstellt, sodass jeder Wiederherstellungspunkt die Daten einer Stunde enthält. Der Wiederherstellungspunktwert, den Sie für den virtuellen Computer in der VMM-Konsole zuweisen, sollte nicht kleiner als der Wert sein, den Sie in der Azure Site Recovery-Konsole zuweisen.
8. Geben Sie unter **Häufigkeit von anwendungskonsistenten Momentaufnahmen** an, wie oft anwendungskonsistente Momentaufnahmen erstellt werden sollen. Hyper-V verwendet zwei Momentaufnahmen: eine Standard-Momentaufnahme, die eine inkrementelle Momentaufnahme des gesamten virtuellen Computers bereitstellt, und eine anwendungskonsistente Momentaufnahme, die eine Zeitpunkt-Momentaufnahme der Anwendungsdaten innerhalb des virtuellen Computers erfasst. Anwendungskonsistente Momentaufnahmen verwenden den Volumeschattenkopie-Dienst (Volume Shadow Copy Service, VSS), um sicherzustellen, dass Anwendungen sich bei der Erstellung der Momentaufnahme in einem konsistenten Zustand befinden. Beachten Sie, dass die Leistung von Anwendungen auf virtuellen Quellcomputern durch die Aktivierung anwendungskonsistenter Momentaufnahmen beeinträchtigt wird. Stellen Sie sicher, dass der festgelegte Wert kleiner als die konfigurierte Anzahl der zusätzlichen Wiederherstellungspunkte ist.

	![Konfigurieren von Schutzeinstellungen](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudSettings.png)

9. Geben Sie unter **Datenübertragungskomprimierung** an, ob replizierte Daten, die übertragen werden, komprimiert werden sollen.
10. Geben Sie unter **Authentifizierung** an, wie der Datenverkehr zwischen dem primären und dem Hyper V-Wiederherstellungs-Hostserver authentifiziert wird. Wählen Sie HTTPS, sofern Sie keine funktionierende Kerberos-Umgebung konfiguriert haben. Azure Site Recovery konfiguriert automatisch Zertifikate für die HTTPS-Authentifizierung. Es ist keine manuelle Konfiguration notwendig. Wenn Sie Kerberos auswählen, wird ein Kerberos-Ticket für die gegenseitige Authentifizierung der Hostserver verwendet. Standardmäßig werden die Ports 8083 und 8084 (für Zertifikate) in der Windows-Firewall auf den Hyper-V-Hostservern geöffnet. Bitte beachten Sie, dass diese Einstellung nur für Hyper-V-Hostserver relevant ist, die unter Windows Server 2012 R2 laufen.
11. Ändern Sie unter **Port** die Portnummer, an der der Quell- und der Zielhostcomputer auf Replikationsdatenverkehr lauschen. Beispielsweise können Sie die Einstellung ändern, wenn Sie die QoS-Netzwerk-Bandbreitendrosselung (Quality of Service) für Replikationsdatenverkehr anwenden möchten. Stellen Sie sicher, dass der Port von keiner anderen Anwendung verwendet wird und in den Firewalleinstellungen geöffnet wurde.
12. Geben Sie unter **Replikationsmethode** an, wie die erste Replikation von Daten vom Quell- zum Zielspeicherort erfolgen soll, bevor die reguläre Replikation beginnt. 
	- **Über das Netzwerk**–– Das Kopieren von Daten über das Netzwerk kann zeitraubend und ressourcenintensiv sein. Es wird empfohlen, diese Option zu verwenden, wenn die Cloud virtuelle Computer mit relativ kleinen virtuellen Festplatten enthält und der primäre VMM-Server über eine große Bandbreite mit dem sekundären VMM-Server verbunden ist. Sie können angeben, dass der Kopiervorgang sofort gestartet werden soll, oder eine Uhrzeit auswählen. Wenn Sie Netzwerkreplikation verwenden, empfehlen wir, deren Ausführung außerhalb der Spitzenzeiten einzuplanen.
	- **Offline**–– Mit dieser Methode wird die erste Replikation unter Verwendung externer Medien durchgeführt. Dies ist sinnvoll, wenn Sie einen Abfall der Netzwerkleistung vermeiden möchten oder wenn Sie mit geografisch fernen Orten arbeiten. Wenn Sie diese Methode anwenden möchten, müssen Sie den Exportspeicherort für die Quellcloud und den Importspeicherort angeben. Wenn sie den Schutz für einen virtuellen Computer aktivieren, wird die virtuelle Festplatte an den angegebenen Exportspeicherort kopiert. Sie senden sie an den Zielstandort und kopieren sie an den Importspeicherort. Das System kopiert die importierten Informationen auf die virtuellen Replikatcomputer. Eine vollständige Liste der Voraussetzungen für die Offline-Replikation finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=323469">Schritt 3: Konfigurieren der Schutzeinstellungen für VMM-Clouds</a> im Bereitstellungshandbuch.
13. Wählen Sie **Virtuellen Replikatcomputer löschen** aus, um anzugeben, dass der virtuelle Replikatcomputer gelöscht werden soll, wenn er nicht mehr geschützt wird, weil Sie die Option **Schutz für virtuellen Computer löschen** auf der Registerkarte "Virtuelle Computer" der Cloud-Eigenschaften ausgewählt haben. Wenn diese Einstellung aktiviert ist und Sie den Schutz deaktivieren, wird der virtuelle Computer aus Azure Site Recovery entfernt, die Site Recovery-Einstellungen für den virtuellen Computer werden aus der VMM-Konsole entfernt, und das Replikat wird gelöscht. ![Konfigurieren von Schutzeinstellungen](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_CloudSettingsRep.png)

<p>Nachdem Sie die Einstellungen gespeichert haben, wird ein Auftrag erstellt, der auf der Registerkarte **Aufträge** überwacht werden kann. Alle Hyper-V-Hostserver in der VMM-Quellcloud werden für die Replikation konfiguriert. Die Cloudeinstellungen können auf der Registerkarte **Konfigurieren** bearbeitet werden. Wenn Sie den Zielspeicherort oder die Zielcloud ändern möchten, müssen Sie die Cloudkonfiguration entfernen und die Cloud anschließend neu konfigurieren.</p>

### Vorbereiten der ersten Offlinereplikation

Sie müssen die folgenden Aktionen durchführen, um die erste Replikation offline vorzubereiten:

- Geben Sie auf dem Quellserver einen Pfad an, von dem der Datenexport stattfinden soll. Weisen Sie Vollzugriff für NTFS und Freigabeberechtigungen für den VMM-Dienst auf dem Exportpfad zu. Auf dem Zielserver geben Sie einen Pfad an, von dem der Datenimport stattfinden soll. Weisen Sie für diesen Importpfad die gleichen Berechtigungen zu. 
- Wenn der Import- oder Exportpfad freigegeben ist, weisen Sie Mitgliedschaften in den Gruppen "Administratoren", "Hauptbenutzer", "Druck-Operatoren" oder "Server-Operatoren" für das VMM-Dienstkonto auf dem Remotecomputer zu, auf dem sich die Freigabe befindet.
- Wenn Sie zum Hinzufügen von Hosts ausführende Konten verwenden, weisen Sie auf dem Import- und Exportpfad Lese- und Schreibberechtigungen für die ausführenden Konten in VMM zu.
- Die Import- und Exportfreigaben sollten sich nicht auf Computern befinden, die als Hyper-V-Hostserver eingesetzt werden, da die Loopbackkonfiguration von Hyper-V nicht unterstützt wird.
- Aktivieren und konfigurieren Sie die eingeschränkte Delegierung in Active Directory auf jedem Hyper-V-Hostserver mit virtuellen Computern, die Sie schützen möchten, um den Remotecomputern zu vertrauen, auf denen sich der Import- und der Exportpfad befinden. Gehen Sie dabei wie folgt vor:
	1. Öffnen Sie auf dem Domänencontroller **Active Directory-Benutzer und-Computer**.
	2. Klicken Sie in der Konsolenstruktur auf **Domänenname** > **Computer**.
	3. Klicken Sie mit der rechten Maustaste auf den Namen des Hyper-V-Hostservers > **Eigenschaften**.
	4. Klicken Sie auf der Registerkarte **Delegierung** auf **Computer bei Delegierungen angegebener Dienste vertrauen**.
	5. Klicken Sie auf **Beliebiges Authentifizierungsprotokoll verwenden**.
	6. Klicken Sie auf **Hinzufügen** > **Benutzer und Computer**.
	7. Geben Sie den Namen des Computers ein, der den Exportpfad hostet > **OK**. Halten Sie die STRG-Taste gedrückt, und klicken Sie in der Liste der verfügbaren Dienste auf **CIFS** > **OK**. Wiederholen Sie diesen Vorgang für den Namen des Computers, der den Importpfad hostet. Wiederholen Sie den Vorgang bei Bedarf für zusätzliche Hyper-V-Hostserver.
	
## Schritt 5: Konfigurieren der Netzwerkzuordnung
1. Klicken Sie auf der Seite „Schnellstart“ auf **Netzwerke zuordnen**.
2. Wählen Sie den VMM-Quellserver aus, von dem aus Sie Netzwerke zuordnen möchten, und dann den Ziel-VMM-Server, dem die Netzwerke zugeordnet werden soll. Die Liste der Quellnetzwerke und ihre zugeordneten Zielnetzwerke werden angezeigt. Für Netzwerke, die derzeit nicht zugeordnet sind, wird ein leerer Wert angezeigt. 
3. Wählen Sie ein Netzwerk unter **Netzwerk auf Quelle** > **Zuordnen**. Der Dienst erkennt die VM-Netzwerke auf dem Zielserver und zeigt sie an. Klicken Sie auf das Informationssymbol neben den Namen des Quell- und Zielnetzwerks, um die Subnetze für jedes Netzwerk anzuzeigen.

	![Konfigurieren der Netzwerkzuordnung](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_NetworkMapping1.png)

4. Wählen Sie im Dialogfeld eines der VM-Netzwerke vom VMM-Zielserver aus.

	![Auswählen eines Zielnetzwerks](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_NetworkMapping2.png)

5. Wenn Sie ein Zielnetzwerk auswählen, werden die geschützten Clouds angezeigt, die das Quellnetzwerk verwenden. Die verfügbaren Zielnetzwerke, die den zum Schutz verwendeten Clouds zugeordnet sind, werden ebenfalls angezeigt. Es wird empfohlen, ein Zielnetzwerk auszuwählen, das für alle für den Schutz verwendeten Clouds verfügbar ist. Oder Sie können auch zum VMM-Server wechseln und die Cloudeigenschaften ändern, um das logische Netzwerk hinzuzufügen, das mit dem gewünschten VM-Netzwerk korrespondiert.
6. Aktivieren Sie das Kontrollkästchen, um den Zuordnungsprozess abzuschließen. Ein Auftrag beginnt mit der Verfolgung des Zuordnungsprozesses. Dieser kann auf der Registerkarte **Aufträge** angezeigt werden.


## Schritt 6: Konfigurieren der Speicherzuordnung
Standardmäßig werden beim Replizieren eines virtuellen Computers auf einem Hyper-V-Host-Quellserver auf einen Hyper-V-Host-Zielserver die replizierten Daten am Standardspeicherort gespeichert, der für den Hyper-V-Zielhost in Hyper-V-Manager angegeben wurde. Um die Ablage von Replikationsdaten zu steuern, können Sie Speicherzuordnungen folgendermaßen konfigurieren:

- Definieren Sie Speicherklassifizierungen auf dem Quell- und Ziel-VMM-Server. Anweisungen hierzu finden Sie unter [Erstellen von Speicher-Klassifizierungen in VMM](http://go.microsoft.com/fwlink/?LinkId=400937). Klassifizierungen müssen den Hyper-V-Hostservern in Quell- und Ziel-Clouds zur Verfügung stehen. Klassifizierungen müssen nicht den gleichen Speichertyp aufweisen. Ordnen Sie z. B. eine Quellklassifizierung mit SMB-Freigaben einer Zielklassifizierung zu, die CSVs enthält.
- Nachdem die Klassifizierungen eingerichtet wurden, können Sie Zuordnungen erstellen.
1. Wählen Sie auf der Seite **Schnellstart** > **Speicher zuordnen**.
1. Klicken Sie auf die Registerkarte **Speicher** > **Speicherklassifizierungen zuordnen**.
1. Wählen Sie auf der Registerkarte **Speicherklassifizierungen zuordnen** die Klassifizierungen auf dem VMM-Quellserver und dem VMM-Zielserver aus. Speichern Sie die Einstellungen.

	![Auswählen eines Zielnetzwerks](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_StorageMapping1.png)


## Schritt 7: Aktivieren des Schutzes für virtuelle Computer
Nach der korrekten Konfiguration von Servern, Clouds und Netzwerken können Sie den Schutz für die virtuellen Computer in der Cloud aktivieren.

1. Klicken Sie auf der Registerkarte **Virtuelle Computer** in der Cloud, in dem sich der virtuelle Computer befindet, auf **Schutz aktivieren** > **Virtuelle Computer hinzufügen**. 
2. Wählen Sie aus der Liste der virtuellen Computer in der Cloud denjenigen aus, den Sie schützen möchten.


![Aktivieren des Schutzes für virtuelle Computer](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_EnableProtectionVM.png)

Auf der Registerkarte **Aufträge** können Sie den Fortschritt der Schutzaktivierung und der ersten Replikation verfolgen. Nachdem der Auftrag zum Abschließen des Schutzes ausgeführt wurde, ist der virtuelle Computer bereit für das Failover. Nach dem Aktivieren des Schutzes und der Replikation der virtuellen Computer können Sie die virtuellen Computer in Azure sehen.

Beachten Sie, dass Sie auch Schutz für virtuelle Computer in der VMM-Konsole aktivieren können. Klicken Sie auf der Symbolleiste in der Azure Site Recovery-Registerkarte der Eigenschaften des virtuellen Computers auf "Schutz aktivieren".


![Schutzauftrag für virtuellen Computer](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_VMJobs.png)

### Integrieren vorhandener virtueller Computer
Wenn in VMM virtuelle Computer vorhanden sind, die über Hyper-V-Replikate repliziert werden, müssen Sie diese für den Schutz durch Azure Site Recovery wie folgt integrieren:
1. Stellen Sie sicher, dass Sie über primäre und sekundäre Clouds verfügen. Stellen Sie sicher, dass der Hyper-V-Hostserver für den vorhandenen virtuellen Computer sich in der primären Cloud befindet und dass der Hyper-V-Hostserver für den virtuellen Replikatcomputer sich in der sekundären Cloud befindet. Stellen Sie sicher, dass Sie die Schutzeinstellungen für die Clouds konfiguriert haben. Die Einstellungen sollten den aktuell für Hyper-V Replikate konfigurierten entsprechen. Andernfalls funktioniert die Replikation virtueller Computer möglicherweise nicht wie erwartet.
2. Aktivieren Sie anschließend den Schutz für den primären virtuellen Computer. Azure Site Recovery und VMM stellen sicher, dass derselbe Replikathost und virtuelle Computer erkannt werden. Azure Site Recovery verwendet die Replikation wieder und stellt sie anhand der Einstellungen, die während der Cloudkonfiguration festgelegt wurden, erneut her.


## Testen der Bereitstellung

Um Ihre Bereitstellung zu testen, können Sie ein Testfailover für einen einzelnen virtuellen Computer durchführen oder einen Wiederherstellungsplan erstellen, der mehrere virtuelle Computer umfasst, und ein Testfailover für diesen Plan durchführen. Das Test-Failover simuliert Ihre Failover- und Wiederherstellungsmechanismen in einem isolierten Netzwerk.

### Erstellen eines Wiederherstellungsplans

1. Klicken Sie auf der Registerkarte **Wiederherstellungspläne** auf **Wiederherstellungsplan erstellen**.
2. Geben Sie einen Namen für den Wiederherstellungsplan sowie die VMM-Quell- und Zielserver an. Der Quellserver muss virtuelle Computer aufweisen, die für Failover und Wiederherstellung aktiviert sind. Wählen Sie **Hyper-V** aus, um nur Clouds anzuzeigen, die für die Hyper-V-Replikation konfiguriert sind.

	![Wiederherstellungsplan erstellen](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_RP1.png)

3. Wählen Sie unter **Virtuellen Computer auswählen** Replikationsgruppen aus. Alle der Replikationsgruppe zugeordneten virtuellen Computer werden ausgewählt und dem Wiederherstellungsplan hinzugefügt. Diese virtuellen Computer werden der Standardgruppe im Wiederherstellungsplan, Gruppe 1, hinzugefügt. Sie können bei Bedarf weitere Gruppen hinzufügen. Beachten Sie, dass nach der Replikation virtuelle Computer gemäß der Reihenfolge der Wiederherstellungsplangruppen gestartet werden.

	![Virtuelle Computer hinzufügen](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_RP2.png)

Erstellte Wiederherstellungspläne werden auf der Registerkarte **Wiederherstellungspläne** angezeigt.

###Durchführen eines Test-Failovers

1. Wählen Sie auf der Registerkarte **Wiederherstellungspläne** den gewünschten Wiederherstellungsplan aus, und klicken Sie auf **Testfailover**.
2. Wählen Sie auf der Seite **Testfailover bestätigen** den Eintrag **Kein** aus. Bei Aktivierung dieser Option werden nach einem Failover die virtuellen Computer im Replikat mit keinem Netzwerk verbunden. Es wird getestet, ob das Failover für den virtuellen Computer wie erwartet erfolgt, ohne dass Ihre Netzwerkumgebung für die Replikation getestet wird. Wenn Sie ein umfassenderes Testfailover durchführen möchten, lesen Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Testen einer Bereitstellung zwischen lokalen Standorten in MSDN</a> nach.

	![Testnetzwerk auswählen](./media/site-recovery-vmm-to-vmm/ASRE2EHVR_TestFailover1.png)


7. Der virtuelle Computer für den Test wird auf dem Host erstellt, auf dem auch der replizierte virtuelle Computer vorhanden ist. Er wird der gleichen Cloud hinzugefügt, in der sich der replizierte virtuelle Computer befindet.

### Ausführen eines Wiederherstellungsplans
Nach der Replikation hat der replizierte virtuelle Computer möglicherweise keine IP-Adresse, die nicht der IP-Adresse des primären virtuellen Computers entspricht. Virtuelle Computer aktualisieren den DNS-Server, den sie nach dem Start verwenden. Sie können auch ein Skript zum Aktualisieren des DNS-Servers hinzufügen, um ein rechtzeitiges Update sicherzustellen.

#### Skript zum Abrufen der IP-Adresse
Führen Sie diese Beispielskript aus, um die IP-Adresse abzurufen.

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

#### Skript zum Aktualisieren von DNS
Führen Sie dieses Beispielskript aus, um DNS zu aktualisieren, wobei Sie die IP-Adresse angeben, die Sie mithilfe des vorherigen Beispielskripts abgerufen haben.

		string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



##<a name="privacy"></a>Datenschutzinformationen für Site Recovery

Dieser Abschnitt enthält zusätzliche Datenschutzinformationen für den Microsoft Azure Site Recovery-Dienst ("Dienst"). Die Datenschutzbestimmungen für Microsoft Azure-Dienste finden Sie in den [Datenschutzbestimmungen für Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899).

**Feature: Registrierung**

- **Funktionsbeschreibung**: registriert Server beim Dienst, sodass virtuelle Computer geschützt werden können.
- **Gesammelte Informationen**: sammelt, verarbeitet und überträgt nach dem Registrieren des Diensts Informationen zum Verwaltungszertifikat von dem VMM-Server, der für die Bereitstellung der Notfallwiederherstellung festgelegt wurde. Dies geschieht anhand des Dienstnamens des VMM-Servers und der Namen der Clouds für virtuelle Computer auf dem VMM-Server.
- **Nutzung von Informationen**: 
	- Verwaltungszertifikat – Wird verwendet, um den registrierten VMM-Server für den Zugriff auf den Dienst zu identifizieren und zu authentifizieren. Der Dienst verwendet den öffentlichen Schlüsselteil des Zertifikats zum Sichern eines Tokens, auf das nur der registrierte VMM-Server zugreifen kann. Der Server muss dieses Token für den Zugriff auf die Dienstfunktionen verwenden.
	- Name des VMM-Servers – Der VMM-Servername ist für die Identifizierung und die Kommunikation mit dem entsprechenden VMM-Server erforderlich, auf dem sich die Clouds befinden.
	- Namen der Clouds auf dem VMM-Server – Der Cloudname wird bei Verwendung der unten beschriebenen Funktion zur Dienst-Cloud-Kopplung/-Entkopplung benötigt. Wenn Sie eine Cloud von einem primären Rechenzentrum aus mit einer anderen Cloud im Wiederherstellungsrechenzentrum koppeln möchten, werden die Namen aller Clouds im Wiederherstellungsrechenzentrum aufgeführt.

- **Wahl**: Diese Informationen sind ein wesentlicher Bestandteil des Dienstregistrierungsprozesses, da sie Sie und den Dienst bei der Identifikation des VMM-Servers, für den Sie den Azure Site Recovery-Schutz bereitstellen möchten, sowie bei der Identifikation des richtigen registrierten VMM-Servers unterstützen. Wenn Sie nicht möchten, dass diese Informationen an den Dienst gesendet werden, verwenden Sie diesen Dienst nicht. Wenn Sie den Server registrieren und die Registrierung dann später aufheben möchten, können Sie dazu die VMM-Serverinformationen im Dienstportal (dem Azure-Portal) löschen.

**Feature: Azure Site Recovery-Schutz aktivieren**

- **Funktionsbeschreibung**: Der auf dem VMM-Server installierte Azure Site Recovery-Anbieter ist für die Kommunikation mit dem Dienst zuständig. Der Anbieter ist eine Dynamic Link Library (DLL), die im VMM-Prozess gehostet wird. Nachdem der Anbieter installiert ist, wird die Funktion "Datacenter Recovery" in der VMM-Administratorkonsole aktiviert. Alle neuen oder vorhandenen virtuellen Computer in einer Cloud können eine Eigenschaft namens "Datacenter Recovery" aktivieren, um den virtuellen Computer zu schützen. Sobald diese Eigenschaft festgelegt ist, sendet der Anbieter den Namen und die ID des virtuellen Computers an den Dienst. Der virtuelle Schutz wird in der Hyper-V-Replikationstechnologie von Windows Server 2012 oder Windows Server 2012 R2 aktiviert. Die Daten des virtuellen Computers werden von einem Hyper-V-Host auf einen anderen repliziert (der sich in der Regel in einem anderen "Recovery"-Rechenzentrum befindet).

- **Gesammelte Informationen**: Der Service erfasst, verarbeitet und überträgt Metadaten für den virtuellen Computer, einschließlich des Namens, der ID, des virtuellen Netzwerks und des Namens der Cloud, zu der er gehört.

- **Nutzung von Informationen**: Der Dienst füllt anhand der obigen Informationen die Informationen zum virtuellen Computer in Ihrem Dienstportal auf.

- **Wahl**: Dies ist ein wesentlicher Bestandteil des Diensts und kann nicht deaktiviert werden. Wenn Sie nicht möchten, dass diese Informationen an den Dienst gesendet werden, aktivieren Sie nicht den Azure Site Recovery-Schutz für virtuelle Computer. Beachten Sie, dass alle Daten über HTTPS vom Anbieter an den Dienst gesendet werden.

**Feature: Wiederherstellungsplan**

- **Funktionsbeschreibung**: Diese Funktion unterstützt Sie beim Erstellen eines Orchestrierungsplans für das "Wiederherstellungs"-Rechenzentrum. Sie können die Reihenfolge definieren, in der die virtuellen Computer oder eine Gruppe virtueller Computer am Wiederherstellungsstandort gestartet werden sollen. Außerdem können Sie die Ausführung beliebiger automatisierter Skripts oder manueller Aktionen angeben, die zum Zeitpunkt der Wiederherstellung für jeden virtuellen Computer ausgeführt werden. Failover (im nächsten Abschnitt behandelt) wird in der Regel auf der Ebene des Wiederherstellungsplans ausgelöst, um eine koordinierte Wiederherstellung einzuleiten.

- **Gesammelte Informationen**: Der Dienst sammelt, verarbeitet und überträgt Metadaten für den Wiederherstellungsplan, einschließlich der Metadaten für virtuelle Computer sowie der Metadaten von Automatisierungsskripts und Hinweisen für manuelle Aktionen.

- **Nutzung von Informationen**: Die oben beschriebenen Metadaten werden zum Erstellen des Wiederherstellungsplans in Ihrem Dienstportal verwendet.

- **Wahl**: Dies ist ein wesentlicher Bestandteil des Diensts und kann nicht deaktiviert werden. Wenn Sie nicht möchten, dass diese Informationen an den Dienst gesendet werden, erstellen Sie in diesem Dienst keine Wiederherstellungspläne.

**Feature: Netzwerkzuordnung**

- **Funktionsbeschreibung**: Mithilfe dieser Funktion können Sie Netzwerkinformationen aus dem primären Rechenzentrum dem Rechenzentrum für die Wiederherstellung zuordnen. Wenn die virtuellen Computer am Wiederherstellungsstandort wiederhergestellt werden, hilft diese Zuordnung beim Herstellen der Netzwerkkonnektivität.

- **Gesammelte Informationen**: Im Rahmen der Funktion für die Netzwerkzuordnung erfasst, verarbeitet und überträgt der Dienst die Metadaten der logischen Netzwerke für die einzelnen Standorte (primär und Rechenzentrum).

- **Nutzung von Informationen**: Der Dienst verwendet die Metadaten zum Auffüllen des Dienstportals, in dem Sie die Netzwerkinformationen zuordnen können.

- **Wahl**: Dies ist ein wesentlicher Bestandteil des Diensts und kann nicht deaktiviert werden. Wenn Sie nicht möchten, dass diese Informationen an den Dienst gesendet werden, verwenden Sie die Funktion zur Netzwerkzuordnung nicht.

**Feature: Failover – geplant, ungeplant, Test**

- **Funktionsweise**: Mit dieser Funktion erfolgt ein Failover eines virtuellen Computers aus einem VMM-verwalteten Rechenzentrum auf ein anderes VMM-verwaltetes Rechenzentrum. Die Failoveraktion wird durch den Benutzer in ihrem Dienstportal ausgelöst. Mögliche Gründe für ein Failover sind ein ungeplantes Ereignis (z. B. im Fall einer Naturkatastrophe), ein geplantes Ereignis (z. B. ein Lastenausgleich im Rechenzentrum) oder ein Testfailover (z. B. ein Probelauf für einen Wiederherstellungsplan).

Der Anbieter auf dem VMM-Server wird vom Dienst über das Ereignis benachrichtigt und führt über VMM-Schnittstellen eine Failoveraktion auf dem Hyper-V-Host durch. Das eigentliche Failover des virtuellen Computers von einem Hyper-V-Host auf einen anderen (der in der Regel in einem anderen "Recovery"-Rechenzentrum ausgeführt wird) erfolgt über die Hyper-V-Replikationstechnologie von Windows Server 2012 oder Windows Server 2012 R2. Nachdem das Failover abgeschlossen ist, sendet der auf dem VMM-Server des "Recovery"-Rechenzentrums installierte Anbieter die Erfolgsinformationen an den Dienst.

- **Gesammelte Informationen**: Der Dienst füllt anhand der obigen Informationen den Status der Failoveraktionsinformationen in Ihrem Dienstportal auf.

- **Nutzung von Informationen**: Der Dienst verwendet die obigen Informationen wie folgt:

	- Verwaltungszertifikat – Wird verwendet, um den registrierten VMM-Server für den Zugriff auf den Dienst zu identifizieren und zu authentifizieren. Der Dienst verwendet den öffentlichen Schlüsselteil des Zertifikats zum Sichern eines Tokens, auf das nur der registrierte VMM-Server zugreifen kann. Der Server muss dieses Token für den Zugriff auf die Dienstfunktionen verwenden.
	- Name des VMM-Servers – Der VMM-Servername ist für die Identifizierung und die Kommunikation mit dem entsprechenden VMM-Server erforderlich, auf dem sich die Clouds befinden.
	- Namen der Clouds auf dem VMM-Server – Der Cloudname wird bei Verwendung der unten beschriebenen Funktion zur Dienst-Cloud-Kopplung/-Entkopplung benötigt. Wenn Sie eine Cloud von einem primären Rechenzentrum aus mit einer anderen Cloud im Wiederherstellungsrechenzentrum koppeln möchten, werden die Namen aller Clouds im Wiederherstellungsrechenzentrum aufgeführt.

- **Wahl**: Dies ist ein wesentlicher Bestandteil des Diensts und kann nicht deaktiviert werden. Wenn Sie nicht möchten, dass diese Informationen an den Dienst gesendet werden, verwenden Sie diesen Dienst nicht.

 

<!------HONumber=Oct15_HO1-->