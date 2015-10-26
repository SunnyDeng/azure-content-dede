<properties
	pageTitle="Einrichten von Schutz zwischen einem lokalen Hyper-V-Standort und Azure"
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung virtueller Computer auf lokalen Hyper-V-Servern zu Azure."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="10/12/2015"
	ms.author="raynew"/>


# Einrichten von Schutz zwischen einem lokalen Hyper-V-Standort und Azure


## Übersicht

Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server aufeinander abgestimmt werden. Informationen zu möglichen Bereitstellungsszenarien finden Sie unter [Übersicht über Site Recovery](site-recovery-overview.md).

In diesem Artikel erfahren Sie, wie Sie Site Recovery bereitstellen, um virtuelle Computer auf lokalen Hyper-V-Servern unter Windows Server 2012 R2 zu replizieren. Die Replikation zu Azure-Speicher wird von Site Recovery orchestriert. Diese Bereitstellung ist besonders hilfreich, wenn Sie Hyper-V-Server verwenden und System Center Virtual Machine Manager (VMM) nicht bereitgestellt ist.


## Informationen zum Artikel

Dieser Artikel enthält eine Zusammenfassung der Bereitstellungsvoraussetzungen und unterstützt Sie beim Konfigurieren der Replikationseinstellungen sowie beim Aktivieren des Schutzes für virtuelle Computer. Zum Schluss wird das Failover getestet, um sicherzustellen, dass alles wie erwartet funktioniert.

Sollten Probleme auftreten, besuchen Sie das [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Vorbereitung

Vergewissern Sie sich, dass Sie alle Voraussetzungen erfüllen, ehe Sie starten.

### Voraussetzungen für Azure

- Sie benötigen ein [Microsoft Azure](http://azure.microsoft.com/)-Konto. Für den Einstieg steht ein [kostenloses Testkonto](pricing/free-trial/) zur Verfügung.
- Sie benötigen ein Azure-Speicherkonto, um replizierte Daten zu speichern. Für das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region wie der Azure Site Recovery-Tresor befinden und dem gleichen Abonnement zugeordnet sein. Weitere Informationen finden Sie unter [Einführung in Microsoft Azure Storage](../storage/storage-introduction.md).
- Sie benötigen ein virtuelles Azure-Netzwerk, damit die replizierten virtuellen Computer nach einem Failover mit einem Netzwerk verbunden sind.

### Hyper-V-Voraussetzungen

- Am lokalen Quellstandort muss mindestens ein Server mit Windows Server 2012 R2 und der Hyper-V-Rolle vorhanden sein.
- Der Hyper-V-Server muss mindestens einen virtuellen Computer enthalten.
- Hyper-V-Server müssen entweder direkt oder über einen Proxy mit dem Internet verbunden sein.
- Auf Hyper-V-Servern sollten die in [KB2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977") genannten Fehlerbehebungen installiert sein.

### Voraussetzungen für virtuelle Computer

Die zu schützenden virtuellen Computer müssen die [Voraussetzungen für virtuelle Computer](site-recovery-best-practices.md#virtual-machines) erfüllen.

### Voraussetzungen für Anbieter und Agent

Im Rahmen der Azure Site Recovery-Bereitstellung werden auf jedem Hyper-V-Server, der über zu schützende virtuelle Computer verfügt, der Azure Site Recovery-Anbieter und der Azure Recovery Services-Agent installiert. Beachten Sie Folgendes:

- Verwenden Sie jeweils die neueste Anbieter- und Agent-Version.
- Alle Hyper-V-Server in einem Tresor müssen die gleiche Version aufweisen.
- Der Anbieter muss eine Internetverbindung mit Azure Site Recovery herstellen. Hierbei können Sie entweder auf die Verwendung eines Proxys verzichten oder die derzeit auf dem VMM-Server konfigurierten Proxyeinstellungen bzw. die benutzerdefinierten Proxyeinstellungen verwenden, die Sie bei der Anbieterinstallation konfiguriert haben. Wenn Sie einen vorhandenen Proxyserver verwenden möchten, stellen Sie sicher, dass die URLs für die Verbindung mit Azure nicht durch die Firewall blockiert werden.
	- *.hypervrecoverymanager.windowsazure.com
	- *.accesscontrol.windows.net
	- *.backup.windowsazure.com
	- *.blob.core.windows.net
	- *.store.core.windows.net
 
- Wenn Sie einen benutzerdefinierten Proxyserver verwenden möchten, richten Sie diesen vor der Installation des Anbieters ein. Bei der Anbieterinstallation müssen Sie dann die Adresse und den Port des Proxyservers sowie die Anmeldeinformationen für den Zugriff angeben. Beachten Sie, dass HTTPS-basierte Proxys nicht unterstützt werden.

Die folgende Abbildung zeigt die verschiedenen Kommunikationskanäle und Ports, die Azure Site Recovery für die Orchestrierung und Replikation verwendet.

![B2A-Topology](./media/site-recovery-hyper-v-site-to-azure/B2ATopology.png)


## Schritt 1: Erstellen eines Tresors

1. Melden Sie sich im [Verwaltungsportal](https://portal.azure.com) an.


2. Erweitern Sie **Data Services** > **Recovery Services**, und klicken Sie auf **Site Recovery-Tresor**.


3. Klicken Sie auf **Neu erstellen** > **Schnellerfassung**.

4. Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird.

5. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Eine Liste mit den unterstützten Regionen finden Sie in den [Preisübersicht zu Azure Site Recovery](pricing/details/site-recovery/) unter „Geografische Verfügbarkeit“.

6. Klicken Sie auf **Tresor erstellen**.

	![Neuer Tresor](./media/site-recovery-hyper-v-site-to-azure/SR_HvVault.png)

Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von Recovery Services als **aktiv** aufgelistet.


## Schritt 2: Erstellen eines Hyper-V-Standorts

1. Klicken Sie auf der Seite Recovery Services auf den Tresor, um die Seite "Schnellstart" zu öffnen. Schnellstart kann auch jederzeit über das Symbol geöffnet werden.

	![Schnellstart](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_QuickStartIcon.png)

2. Wählen Sie in der Dropdownliste den Eintrag **Zwischen einem lokalen Hyper-V-Standort und Azure** aus.

	![Hyper-V-Standortszenario](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectScenario.png)

3. Klicken Sie unter **Hyper-V-Standort erstellen** auf **Hyper-V-Standort erstellen**. Geben Sie einen Standortnamen ein, und speichern Sie ihn.

	![Hyper-V-Standort](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateSite2.png)


## Schritt 3: Installieren des Anbieters und des Agents
Installieren Sie Anbieter und Agent. Wenn Sie die Installation für einen Hyper-V-Cluster vornehmen, führen Sie die Schritte 5 bis 11 auf jedem Knoten des Failoverclusters durch. Wenn alle Knoten registriert sind und der Schutz aktiviert wurde, sind die virtuellen Computer sogar bei der knotenübergreifenden Migration innerhalb des Failoverclusters geschützt.

1. Klicken Sie unter **Hyper-V-Server vorbereiten** auf **Registrierungsschlüssel herunterladen**.
2. Klicken Sie auf der Seite **Registrierungsschlüssel herunterladen** neben dem Standort auf **Herunterladen**. Laden Sie den Schlüssel an einen sicheren Speicherort herunter, auf den der Hyper-V-Server einfach zugreifen kann. Der Schlüssel ist nach der Erstellung 5 Tage lang gültig.

	![Registrierungsschlüssel](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_DownloadKey2.png)

4. Klicken Sie auf **Anbieter herunterladen**, um die neueste Version herunterzuladen.
5. Führen Sie die Datei auf allen Hyper-V-Servern aus, die Sie im Tresor registrieren möchten. Die Datei installiert zwei Komponenten:
	- **Azure Site Recovery-Anbieter**: Diese Komponente ist zuständig für die Kommunikation und Orchestrierung zwischen Hyper-V-Server und Azure Site Recovery-Portal.
	- **Azure Recovery Services-Agent**: Diese Komponente ist zuständig für den Datentransport zwischen virtuellen Computern auf dem Hyper-V-Quellserver und Azure-Speicher.
6. Unter **Microsoft Update** können Sie Updates abonnieren. Mit dieser Einstellung werden Anbieter- und Agent-Updates gemäß Ihrer Microsoft Update-Richtlinie installiert.

	![Microsoft Updates](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider1.png)

7. Geben Sie unter **Installation** an, wo der Anbieter und der Agent auf dem Hyper-V-Server installiert werden sollen.

	![Installationspfad](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider2.png)

8. Fahren Sie nach der Installation mit der Einrichtung fort, um den Server im Tresor zu registrieren.

	![Installation abgeschlossen](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider3.png)


9. Geben Sie auf der Seite **Internetverbindung** an, wie die Verbindung zwischen Anbieter und Azure Site Recovery hergestellt werden soll. Wählen Sie **Proxyeinstellungen des Systems verwenden** aus, um die Standard-Internetverbindungseinstellungen auf dem Server zu verwenden. Wenn Sie keinen Wert angeben, werden die Standardeinstellungen verwendet.

	![Interneteinstellungen](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider4.png)

	Beachten Sie Folgendes:

	- Wenn für den Standardproxy des Hyper-V-Servers eine Authentifizierung erforderlich ist, müssen Sie die Option für den benutzerdefinierten Proxyserver auswählen. Geben Sie die Details zum Standardproxy sowie die Anmeldeinformationen ein.
	- Wenn Sie einen benutzerdefinierten Proxyserver verwenden möchten, richten Sie diesen vor der Anbieterinstallation ein.
	- Der Hyper-V-Host sollte auf die folgenden URLs Zugriff haben:
		- *.hypervrecoverymanager.windowsazure.com
		- *.accesscontrol.windows.net
		- *.backup.windowsazure.com
		- *.blob.core.windows.net
		- *.store.core.windows.net

	- Lassen Sie die unter [IP-Bereiche des Azure-Rechenzentrums](http://go.microsoft.com/fwlink/?LinkId=511094) angegebenen IP-Adressen sowie das HTTPS-Protokoll (443) zu. Fügen Sie die IP-Adressbereiche der zu verwendenden Azure-Region sowie die IP-Adressbereiche der westlichen USA einer Positivliste hinzu.

9. Klicken Sie auf der Seite **Tresoreinstellungen** auf **Durchsuchen**, um die Schlüsseldatei auszuwählen. Geben Sie das Azure Site Recovery-Abonnement, den Tresornamen und den Hyper-V-Standort an, zu dem der Hyper-V-Server gehört.

	![Serverregistrierung](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectKey.png)


11. Die Registrierung des Servers im Tresor beginnt.

	![Serverregistrierung](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider6.png)

11. Nach der Registrierung ruft Azure Site Recovery Metadaten vom Hyper-V-Server ab, und der Server wird im Tresor auf der Registerkarte **Hyper-V-Standorte** der Seite **Server** angezeigt.

	![Serverregistrierung](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider7.png)

	> [AZURE.NOTE]Der Azure Site Recovery-Anbieter kann auch über die folgende Befehlszeile installiert werden. Mit dieser Methode kann der Anbieter in Server Core für Windows Server 2012 R2 installiert werden.
	>
	>1. Laden Sie die Installationsdatei und den Registrierungsschlüssel des Anbieters in einen Ordner herunter, z. B. in "C:\\ASR".
	>2. Extrahieren Sie das Installationsprogramm für den Anbieter, indem Sie die folgenden Befehle über eine Befehlszeile mit **Administratorrechten** ausführen.
	>
	    	C:\Windows\System32> CD C:\ASR
	    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
	>4. Installieren Sie den Anbieter mithilfe des folgenden Befehls:
	>
			C:\ASR> setupdr.exe /i
	>5. Registrieren Sie den Anbieter mithilfe des folgenden Befehls:
	>
	    	CD C:\Program Files\Microsoft Azure Site Recovery Provider\
	    	C:\Program Files\Microsoft Azure Site Recovery Provider> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file>


	>----------
          
	>####Parameter für die Installation über die Befehlszeile####
	>- **/Credentials**: erforderlicher Parameter zum Angeben des Speicherorts der Registrierungsschlüsseldatei.  
	> - **/FriendlyName**: erforderlicher Parameter für den Namen des Hyper-V-Hostservers, der im Azure Site Recovery-Portal angezeigt wird.
	> - **/proxyAddress**: optionaler Parameter, der die Adresse des Proxyservers angibt.
	> - **/proxyport**: optionaler Parameter, der den Port des Proxyservers angibt.
	> - **/proxyUsername**: optionaler Parameter, der den Proxybenutzernamen angibt (sofern der Proxy eine Authentifizierung erfordert).
	> - **/proxyPassword**: optionaler Parameter, der das Kennwort für die Authentifizierung mit dem Proxyserver angibt (sofern der Proxy eine Authentifizierung erfordert).

>[AZURE.TIP]Sie können jeden einzelnen Hyper-V-Host so konfigurieren, dass unterschiedliche Netzwerk-Bandbreiteneinstellungen verwendet werden, um virtuelle Computer in Azure zu replizieren. Weitere Informationen über das [Verwalten der Netzwerkbandbreitennutzung für den Schutz zwischen einem lokalen Standort und Azure](https://support.microsoft.com/en-us/kb/3056159)


## Schritt 4: Erstellen der Azure-Ressourcen

1. Wählen Sie unter **Ressourcen vorbereiten** die Option **Speicherkonto erstellen** aus, um ein Azure-Speicherkonto zu erstellen, sofern noch nicht vorhanden. Für das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region befinden wie der Azure Site Recovery-Tresor und dem gleichen Abonnement zugeordnet sein.

	![Speicherkonto erstellen](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateResources1.png)

## Schritt 5: Erstellen und Konfigurieren von Schutzgruppen

Schutzgruppen sind logische Gruppen virtueller Computer, für die die gleichen Schutzeinstellungen gelten sollen. Sie weisen einer Schutzgruppe Schutzeinstellungen zu, die für alle virtuellen Computer gelten, die Sie der Gruppe hinzufügen. 1. Klicken Sie in **Schutzgruppen erstellen und konfigurieren** auf **Schutzgruppe erstellen**. Falls nicht alle Voraussetzungen erfüllt sind, erscheint eine entsprechende Meldung. Klicken Sie auf **Details anzeigen**, um weitere Informationen zu erhalten.

2. Fügen Sie auf der Registerkarte **Schutzgruppen** eine neue Schutzgruppe hinzu. Geben Sie einen Namen, den Hyper-V-Quellstandort, das Ziel in **Azure**, den Namen Ihres Azure Site Recovery-Abonnements und das Azure-Speicherkonto an.

	![Schutzgruppe](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroupCreate3.png)


2. Legen Sie unter **Replikationseinstellungen** die Kopierhäufigkeit fest, um anzugeben, wie oft geänderte Daten zwischen Quelle und Ziel synchronisiert werden sollen. Verfügbare Optionen sind 30 Sekunden, fünf Minuten und 15 Minuten.
3. Geben Sie unter **Wiederherstellungspunkte beibehalten für** an, für wie viele Stunden der Wiederherstellungsverlauf gespeichert werden soll.
4. Unter **Häufigkeit von anwendungskonsistenten Momentaufnahmen** können Sie angeben, ob Momentaufnahmen erstellt werden sollen, bei denen mithilfe des Volumeschattenkopie-Diensts (Volume Shadow Copy Service, VSS) sichergestellt wird, dass sich die Anwendungen bei der Erstellung der Momentaufnahme in einem konsistenten Zustand befinden. Standardmäßig werden solche Momentaufnahmen nicht erstellt. Stellen Sie sicher, dass der Wert geringer ist als die Anzahl der konfigurierten zusätzlichen Wiederherstellungspunkte. Dies wird nur für virtuelle Computer mit einem Windows-Betriebssystem unterstützt.
5. Geben Sie im Feld für die Startzeit der ersten Replikation an, wann die erste Replikation der virtuellen Computer in der Schutzgruppe an Azure gesendet werden soll.

	![Schutzgruppe](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroup4.png)


## Schritt 6: Aktivieren des Schutzes für virtuelle Computer


Fügen Sie einer Schutzgruppe virtuelle Computer hinzu, um sie zu schützen.

1. Klicken Sie auf der Registerkarte **Virtuelle Computer** der Schutzgruppe auf **Virtuelle Computer zum Aktivieren von Schutz zu Schutzgruppen hinzufügen**.
2. Wählen Sie auf der Seite **Schutz für virtuelle Computer aktivieren** die virtuellen Computer aus, die Sie schützen möchten.

	![Aktivieren des Schutzes für virtuelle Computer](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_AddVM3.png)

	Der Auftrag Schutz aktivieren wird ausgeführt. Der Fortschritt kann auf der Registerkarte **Aufträge** nachverfolgt werden. Nachdem der Auftrag zum Abschließen des Schutzes ausgeführt wurde, ist der virtuelle Computer bereit für das Failover.
3. Nach der Einrichtung des Schutzes haben Sie folgende Möglichkeiten:

	- Anzeigen virtueller Computer unter **Geschützte Elemente** > **Schutzgruppen** > *<Name der Schutzgruppe>* > **Virtuelle Computer**. Auf der Registerkarte **Eigenschaften** können Sie Detailinformationen zu den Computern anzeigen.
	- Konfigurieren der Failovereigenschaften für einen virtuellen Computer unter **Geschützte Elemente** > **Schutzgruppen** > *<Name der Schutzgruppe>* > **Virtuelle Computer** > *<Name des virtuellen Computers>* > **Konfigurieren**. Sie können Folgendes konfigurieren:
		- **Name**: Der Name des virtuellen Computers in Azure.
		- **Größe**: Die Zielgröße des virtuellen Computers, für den ein Failover durchgeführt wird.

		![Konfigurieren der Eigenschaften virtueller Computer](./media/site-recovery-hyper-v-site-to-azure/VMProperties.png)
	- Konfigurieren weiterer Einstellungen für den virtuellen Computer unter *Geschützte Elemente** > **Schutzgruppen** > *<Name der Schutzgruppe>* > **Virtuelle Computer** > *<Name des virtuellen Computers>* > **Konfigurieren**. Hierzu zählen:

		- **Netzwerkadapter**: Die Anzahl der Netzwerkadapter hängt von der Größe ab, die Sie für den virtuellen Zielcomputer angeben.
			- Groß (A3) und A6: 2
			- Extragroß (A4) und A7:
			- A9: 2
			- D3: 2
			- D4: 4
			- D13: 4

			Wenn Sie die Größe für einen virtuellen Computer ändern und die Einstellungen speichern, werden beim nächsten Öffnen der Seite **Konfigurieren** die Netzwerkadapter angezeigt. Die Anzahl der Adapter für einen virtuellen Computer wird wie folgt bestimmt:


			- Wenn die Anzahl der Netzwerkadapter des Quellcomputers maximal der Anzahl der Netzwerkadapter entspricht, die für die Größe des Zielcomputers zulässig ist, hat der Zielcomputer die gleiche Anzahl von Netzwerkadaptern wie der Quellcomputer.
			- Wenn die Anzahl der Netzwerkadapter für den virtuellen Quellcomputer die maximal zulässige Anzahl für die Größe des Zielcomputers übersteigt, wird die Anzahl verwendet, die maximal für die Größe des Zielcomputers zulässig ist.
			- Ein Beispiel: Wenn ein Quellcomputer zwei Netzwerkadapter besitzt und der Zielcomputer aufgrund seiner Größe vier Adapter unterstützt, erhält der Zielcomputer zwei Adapter. Wenn der Quellcomputer dagegen zwei Netzwerkadapter besitzt und der Zielcomputer aufgrund seiner Größe nur einen Adapter unterstützt, erhält der Zielcomputer nur einen Adapter. 	
		- **Azure-Netzwerk**: Geben Sie das Failover-Zielnetzwerk für den virtuellen Computer an. Wenn der virtuelle Computer mehrere Netzwerkadapter besitzt, müssen alle Adapter mit dem gleichen Azure-Netzwerk verbunden werden.
		- **Subnetz**: Wählen Sie für jeden Netzwerkadapter des virtuellen Computers das Subnetz im Azure-Netzwerk aus, mit dem der Computer nach einem Failover eine Verbindung herstellen soll.
		- **Ziel-IP-Adresse**: Wenn der Netzwerkadapter des virtuellen Quellcomputers für die Verwendung einer statischen IP-Adresse konfiguriert ist, können Sie die IP-Adresse für den virtuellen Zielcomputer angeben, um sicherzustellen, dass der Computer nach dem Failover die gleiche IP-Adresse besitzt. Wenn Sie keine IP-Adresse angeben, wird beim Failover eine der verfügbaren Adressen zugewiesen. Wenn Sie eine Adresse angeben, die bereits verwendet wird, ist das Failover nicht erfolgreich.

		![Konfigurieren der Eigenschaften virtueller Computer](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_VMMultipleNic.png)

## Schritt 7: Erstellen eines Wiederherstellungsplans

Zum Testen der Bereitstellung können Sie ein Test-Failover für einen einzelnen virtuellen Computer durchführen. Alternativ können Sie einen Wiederherstellungsplan erstellen, der mehrere virtuelle Computer enthält. Zum Erstellen eines Wiederherstellungsplans [befolgen Sie diese Anweisungen](site-recovery-create-recovery-plans.md).

## Schritt 8: Testen der Bereitstellung

Es gibt zwei Möglichkeiten, ein Testfailover in Azure auszuführen.

- Testfailover ohne Azure-Netzwerk – Bei dieser Art von Testfailover wird überprüft, dass der virtuelle Computer in Azure ordnungsgemäß hochgefahren wird. Der virtuelle Computer wird nach einem Failover mit keinem Azure-Netzwerk verbunden.
- Testfailover mit Azure-Netzwerk – Bei dieser Art von Failover wird überprüft, ob die gesamte Replikationsumgebung wie erwartet hochgefahren wird und die betreffenden virtuellen Computer mit dem angegebenen Azure-Zielnetzwerk verbunden werden. Bei der Subnetzverarbeitung wird für das Testfailover das Subnetz des virtuellen Testcomputers basierend auf dem Subnetz des virtuellen Replikatcomputers festgelegt. Dies unterscheidet sich von der regulären Replikation, bei der das Subnetz des virtuellen Replikatcomputers auf dem Subnetz des virtuellen Quellcomputers basiert.

Wenn Sie ein Testfailover für einen virtuellen Computer, der für den Schutz in Azure aktiviert ist, ohne Angabe eines Azure-Zielnetzwerks durchführen möchten, müssen Sie nichts vorbereiten. Für ein Testfailover mit einem Azure-Zielnetzwerk müssen Sie ein neues Azure-Netzwerk erstellen, das von Ihrem Azure-Produktionsnetzwerk isoliert ist (Standardverhalten bei der Erstellung eines neuen Netzwerks in Azure). Unter [Ausführen eines Testfailovers](site-recovery-failover.md#run-a-test-failover) finden Sie weitere Details.


Sie müssen außerdem die Infrastruktur so einrichten, dass der replizierte virtuelle Computer erwartungsgemäß funktioniert. So kann beispielsweise ein virtueller Computer mit Domänencontroller und DNS mithilfe von Azure Site Recovery zu Azure repliziert und mittels Testfailover im Testnetzwerk erstellt werden. Unter [Überlegungen zum Testfailover für Active Directory](site-recovery-active-directory.md#considerations-for-test-failover) finden Sie weitere Details.

Gehen Sie wie folgt vor, um das Testfailover durchzuführen:

1. Wählen Sie auf der Registerkarte **Wiederherstellungspläne** den gewünschten Plan aus, und klicken Sie auf **Testfailover**.
2. Wählen Sie auf der Seite **Testfailover bestätigen** den Eintrag **Kein** oder ein bestimmtes Azure-Netzwerk aus. Beachten Sie, dass ein Testfailover bei Auswahl von **Kein** zwar prüft, ob der virtuelle Computer ordnungsgemäß zu Azure repliziert wurde, die Konfiguration des Replikationsnetzwerks wird jedoch nicht geprüft.

	![Testfailover](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_TestFailoverNoNetwork.png)

3. Auf der Registerkarte **Aufträge** können Sie den Fortschritt des Failovers verfolgen. Außerdem wird Ihnen das Testreplikat des virtuellen Computers im Azure-Portal angezeigt. Wenn Sie den Zugriff auf virtuelle Computer aus Ihrem lokalen Netzwerk eingerichtet haben, können Sie eine Remotedesktopverbindung mit dem virtuellen Computer herstellen.
4. Klicken Sie auf **Test abschließen**, wenn das Failover die Endphase des Tests erreicht. Auf der Registerkarte **Auftrag** können Sie den Failoverfortschritt und -status detailliert nachverfolgen und etwaige erforderliche Aufgaben ausführen.
5. Nach dem Failover können Sie das Testreplikat des virtuellen Computers im Azure-Portal sehen. Wenn Sie den Zugriff auf virtuelle Computer aus Ihrem lokalen Netzwerk eingerichtet haben, können Sie eine Remotedesktopverbindung mit dem virtuellen Computer herstellen.

	1. Prüfen Sie, ob die virtuellen Computer erfolgreich starten.
    2. Wenn Sie nach dem Failover eine Verbindung mit dem virtuellen Computer in Azure über Remote Desktop herstellen möchten, aktivieren Sie die Remote Desktop-Verbindung auf dem virtuellen Computer, bevor Sie das Test-Failover ausführen. Außerdem müssen Sie dem virtuellen Computer einen RDP-Endpunkt hinzufügen. Hierzu können Sie ein Azure Automation-Runbook[](site-recovery-runbook-automation.md) verwenden.
    3. Falls Sie nach dem Failover über eine öffentliche IP-Adresse eine Remotedesktopverbindung mit dem virtuellen Computer in Azure herstellen, achten Sie darauf, dass keine Domänenrichtlinien vorhanden sind, die dies verhindern.

6. Gehen Sie nach dem Test wie folgt vor:

	- Klicken Sie auf **Das Testfailover ist abgeschlossen**. Bereinigen Sie die Testumgebung, um die virtuellen Testcomputer automatisch auszuschalten und zu löschen.
	- Klicken Sie auf **Notizen**, um alle Beobachtungen im Zusammenhang mit dem Test-Failover aufzuzeichnen und zu speichern.
7. Wenn das Failover die Phase **Test abschließen** erreicht, schließen Sie die Überprüfung wie folgt ab:
	1. Zeigen Sie den virtuellen Replikatcomputer im Azure-Portal an. Prüfen Sie, ob der virtuelle Computer erfolgreich startet.
	2. Wenn Sie den Zugriff auf virtuelle Computer aus Ihrem lokalen Netzwerk eingerichtet haben, können Sie eine Remotedesktopverbindung mit dem virtuellen Computer herstellen.
	3. Klicken Sie auf **Test abschließen**, um den Test abzuschließen.
	4. Klicken Sie auf **Notizen**, um alle Beobachtungen im Zusammenhang mit dem Test-Failover aufzuzeichnen und zu speichern.
	5.  Klicken Sie auf **Das Testfailover ist abgeschlossen**. Bereinigen Sie die Testumgebung, um den virtuellen Testcomputer automatisch auszuschalten und ihn zu löschen.

## Nächste Schritte

Wenn die Bereitstellung eingerichtet ist und ausgeführt wird, informieren Sie sich über [Failover](site-recovery-failover.md).

<!---HONumber=Oct15_HO3-->