<properties
	pageTitle="Replizieren zwischen lokalen virtuellen Hyper-V-Maschinen und Azure (ohne VMM) mit Site Recovery | Microsoft Azure"
	description="Dieser Artikel beschreibt, wie virtuelle Hyper-V-Maschinen in Azure mit Azure Site Recovery repliziert werden, wenn Computer nicht in VMM-Clouds verwaltet werden."
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
	ms.date="02/16/2016"
	ms.author="raynew"/>


# Replizieren zwischen lokalen virtuellen Hyper-V-Maschinen und Azure (ohne VMM) mit Azure Site Recovery

Der Dienst Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server aufeinander abgestimmt werden. Computer können in Azure oder in einem sekundären lokalen Datencenter repliziert werden. Eine kurze Übersicht Eine kurze Übersicht über das Gesamtthema finden Sie unter [Was ist Azure Site Recovery?](site-recovery-overview.md).

## Übersicht

In diesem Artikel wird beschrieben, wie Sie Site Recovery für die Replikation von virtuellen Hyper-V-Maschinen bereitstellen, wenn Hyper-V-Hosts nicht in System Center Virtual Machine Manager-Clouds (VMM) verwaltet werden.

Dieser Artikel enthält eine Zusammenfassung der Bereitstellungsvoraussetzungen und unterstützt Sie beim Konfigurieren der Replikationseinstellungen sowie beim Aktivieren des Schutzes für virtuelle Computer. Zum Schluss wird das Failover getestet, um sicherzustellen, dass alles wie erwartet funktioniert.


Kommentare oder Fragen können Sie am Ende dieses Artikels oder im [Forum zu Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) veröffentlichen.


## Vorbereitung

Vergewissern Sie sich, dass alle Voraussetzungen erfüllt sind, ehe Sie starten.

### Voraussetzungen für Azure

- Sie benötigen ein [Microsoft Azure](https://azure.microsoft.com/)-Konto. Für den Einstieg steht ein [kostenloses Testkonto](pricing/free-trial/) zur Verfügung.
- Sie benötigen ein Azure-Speicherkonto, um replizierte Daten zu speichern. Für das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region wie der Azure Site Recovery-Tresor befinden und dem gleichen Abonnement zugeordnet sein. [Hier erhalten Sie weitere Informationen zu Azure Storage](../storage/storage-introduction.md).
- Sie benötigen ein virtuelles Azure-Netzwerk, damit für virtuelle Azure-Maschinen eine Netzwerkverbindung besteht, wenn Sie ein Failover von Ihrem primären Standort durchführen.

### Hyper-V-Voraussetzungen

- Am lokalen Quellstandort benötigen Sie mindestens einen Server mit Windows Server 2012 R2 und installierter Hyper-V-Rolle. Für diesen Server sollten folgende Anforderungen erfüllt sein:
- Er enthält eine oder mehrere virtuelle Maschinen.
- Er muss entweder direkt oder über einen Proxy mit dem Internet verbunden sein.
- Es müssen die unter KB [2961977](https://support.microsoft.com/de-DE/kb/2961977 "KB2961977") beschriebenen Fixes ausgeführt werden.

### Voraussetzungen für virtuelle Computer

Die zu schützenden virtuellen Maschinen müssen die [Voraussetzungen für virtuelle Maschinen](site-recovery-best-practices.md#virtual-machines) erfüllen.

### Voraussetzungen für Anbieter und Agent

Im Rahmen der Azure Site Recovery-Bereitstellung werden auf jedem Hyper-V-Server der Azure Site Recovery-Anbieter und der Azure Recovery Services-Agent installiert. Beachten Sie Folgendes:

- Es wird empfohlen, immer die aktuellen Versionen des Anbieters und des Agents auszuführen Diese sind im Site Recovery-Portal verfügbar.
- Alle Hyper-V-Server in einem Tresor sollten über die gleichen Versionen des Anbieters und Agents verfügen.
- Der Anbieter, der auf dem Server ausgeführt wird, stellt die Verbindung mit Site Recovery über das Internet her. Sie können dies ohne Proxy, mit den derzeit auf dem Hyper-V-Server konfigurierten Proxyeinstellungen oder mit benutzerdefinierten Proxyeinstellungen durchführen, die Sie während der Installation des Anbieters konfigurieren. Sie müssen sicherstellen, dass der Proxyserver, den Sie verwenden möchten, Zugriff auf die URLs für die Verbindungsherstellung mit Azure hat:
	- *.hypervrecoverymanager.windowsazure.com
	- *.accesscontrol.windows.net
	- *.backup.windowsazure.com
	- *.blob.core.windows.net
	- *.store.core.windows.net
 
- Lassen Sie außerdem die unter [IP-Bereiche des Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653) angegebenen IP-Adressen sowie das HTTPS-Protokoll (443) zu. Fügen Sie die IP-Adressbereiche der zu verwendenden Azure-Region sowie die IP-Adressbereiche der westlichen USA einer Positivliste hinzu.


Diese Grafik zeigt die verschiedenen Kommunikationskanäle und Ports, die Site Recovery für die Orchestrierung und Replikation verwendet.

![B2A-Topology](./media/site-recovery-hyper-v-site-to-azure/B2ATopology.png)


## Schritt 1: Erstellen eines Tresors

1. Melden Sie sich im [Verwaltungsportal](https://portal.azure.com) an.


2. Erweitern Sie **Data Services** > **Recovery Services**, und klicken Sie auf **Site Recovery-Tresor**.


3. Klicken Sie auf **Neu erstellen** > **Schnellerfassung**.

4. Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird.

5. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Eine Liste mit den unterstützten Regionen finden Sie in den [Preisdetails zu Azure Site Recovery](pricing/details/site-recovery/) unter „Geografische Verfügbarkeit“.

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
Installieren Sie den Anbieter und den Agent auf allen Hyper-V-Servern, die zu schützende VMs enthalten.

Wenn Sie die Installation für einen Hyper-V-Cluster vornehmen, führen Sie die Schritte 5 bis 11 auf jedem Knoten des Failoverclusters durch. Wenn alle Knoten registriert sind und der Schutz aktiviert wurde, sind die virtuellen Computer sogar bei der knotenübergreifenden Migration innerhalb des Clusters geschützt.

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

9. Klicken Sie auf der Seite **Tresoreinstellungen** auf **Durchsuchen**, um die Schlüsseldatei auszuwählen. Geben Sie das Azure Site Recovery-Abonnement, den Tresornamen und den Hyper-V-Standort an, zu dem der Hyper-V-Server gehört.

	![Serverregistrierung](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectKey.png)


11. Die Registrierung des Servers im Tresor beginnt.

	![Serverregistrierung](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider6.png)

11. Nach der Registrierung ruft Azure Site Recovery Metadaten vom Hyper-V-Server ab, und der Server wird im Tresor auf der Registerkarte **Hyper-V-Standorte** der Seite **Server** angezeigt.

	![Serverregistrierung](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider7.png)


### Installieren des Anbieters über die Befehlszeile

Alternativ können Sie den Azure Site Recovery-Anbieter auch über die Befehlszeile installieren. Verwenden Sie dieses Verfahren, wenn Sie den Anbieter auf einem Computer mit Windows Server Core 2012 R2 installieren möchten. Führen Sie über die Befehlszeile Folgendes aus:

1. Laden Sie die Installationsdatei und den Registrierungsschlüssel des Anbieters in einen Ordner herunter. Beispiel: C:\\ASR.
2. Führen Sie eine Eingabeaufforderung als Administrator aus, und geben Sie Folgendes ein:

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

3. Führen Sie anschließend den folgenden Befehl aus, um den Anbieter zu installieren:

		C:\ASR> setupdr.exe /i

4. Führen Sie Folgendes aus, um die Registrierung abzuschließen:

    	CD C:\Program Files\Microsoft Azure Site Recovery Provider
    	C:\Program Files\Microsoft Azure Site Recovery Provider> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

Parameter:

- **/Credentials**: Geben Sie den Speicherort des heruntergeladenen Registrierungsschlüssels an.
- **/FriendlyName**: Geben Sie einen Namen zur Identifizierung des Hyper-V-Hostservers an. Dieser Name wird im Portal angezeigt.
- **/EncryptionEnabled**: Optional. Geben Sie an, ob Sie virtuelle Replikatmaschinen in Azure verschlüsseln möchten (Verschlüsselung im ruhenden Zustand).
- **/proxyAddress**; **/proxyport**; **/proxyUsername**; **/proxyPassword**: Optional. Geben Sie Proxyparameter ein, wenn Sie einen benutzerdefinierten Proxyserver verwenden möchten oder falls für den vorhandenen Proxy eine Authentifizierung erforderlich ist.


## Schritt 4: Erstellen eines Azure-Speicherkontos 

1. Wählen Sie unter **Ressourcen vorbereiten** die Option **Speicherkonto erstellen** aus, um ein Azure-Speicherkonto zu erstellen, sofern noch nicht vorhanden. Für das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region befinden wie der Azure Site Recovery-Tresor und dem gleichen Abonnement zugeordnet sein.

	![Speicherkonto erstellen](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateResources1.png)

## Schritt 5: Erstellen und Konfigurieren von Schutzgruppen

Schutzgruppen sind logische Gruppen virtueller Computer, für die die gleichen Schutzeinstellungen gelten sollen. Sie weisen einer Schutzgruppe Schutzeinstellungen zu, die für alle virtuellen Computer gelten, die Sie der Gruppe hinzufügen.

1. Klicken Sie in **Schutzgruppen erstellen und konfigurieren** auf **Schutzgruppe erstellen**. Falls nicht alle Voraussetzungen erfüllt sind, erscheint eine entsprechende Meldung. Klicken Sie auf **Details anzeigen**, um weitere Informationen zu erhalten.

2. Fügen Sie auf der Registerkarte **Schutzgruppen** eine neue Schutzgruppe hinzu. Geben Sie einen Namen, den Hyper-V-Quellstandort, das Ziel in **Azure**, den Namen Ihres Azure Site Recovery-Abonnements und das Azure-Speicherkonto an.

	![Schutzgruppe](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroupCreate3.png)


2. Legen Sie unter **Replikationseinstellungen** die Kopierhäufigkeit fest, um anzugeben, wie oft geänderte Daten zwischen Quelle und Ziel synchronisiert werden sollen. Verfügbare Optionen sind 30 Sekunden, fünf Minuten und 15 Minuten.
3. Geben Sie unter **Wiederherstellungspunkte beibehalten für** an, für wie viele Stunden der Wiederherstellungsverlauf gespeichert werden soll.
4. Unter **Häufigkeit von anwendungskonsistenten Momentaufnahmen** können Sie angeben, ob Momentaufnahmen erstellt werden sollen, bei denen mithilfe des Volumeschattenkopie-Diensts (Volume Shadow Copy Service, VSS) sichergestellt wird, dass sich die Anwendungen bei der Erstellung der Momentaufnahme in einem konsistenten Zustand befinden. Standardmäßig werden solche Momentaufnahmen nicht erstellt. Stellen Sie sicher, dass der Wert geringer ist als die Anzahl der konfigurierten zusätzlichen Wiederherstellungspunkte. Dies wird nur für virtuelle Computer mit einem Windows-Betriebssystem unterstützt.
5. Geben Sie im Feld für die Startzeit der ersten Replikation an, wann die erste Replikation der virtuellen Computer in der Schutzgruppe an Azure gesendet werden soll.

	![Schutzgruppe](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroup4.png)


## Schritt 6: Aktivieren des Schutzes für virtuelle Computer


Fügen Sie einer Schutzgruppe virtuelle Computer hinzu, um sie zu schützen.

>[AZURE.NOTE] Das Schützen von VMs, auf denen Linux mit einer statischen IP-Adresse ausgeführt wird, wird nicht unterstützt.

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
	- Konfigurieren weiterer Einstellungen für den virtuellen Computer unter **Geschützte Elemente** > **Schutzgruppen** > *<Name der Schutzgruppe>* > **Virtuelle Computer** > *<Name des virtuellen Computers>* > **Konfigurieren**. Hierzu zählen:

		- **Netzwerkadapter**: Die Anzahl der Netzwerkadapter hängt von der Größe ab, die Sie für den virtuellen Zielcomputer angeben. Überprüfen Sie in den [Spezifikationen für virtuelle Computer](../virtual-machines/virtual-machines-size-specs.md#size-tables), wie viele Netzwerkschnittstellenkarten von virtuellen Maschinen einer bestimmten Größe unterstützt werden.


			Wenn Sie die Größe für einen virtuellen Computer ändern und die Einstellungen speichern, wird die Anzahl der Netzwerkadapter beim nächsten Öffnen der Seite **Konfigurieren** geändert. Die Anzahl der Netzwerkadapter der virtuellen Zielcomputer entspricht mindestens der Anzahl der Netzwerkadapter auf virtuellen Quellcomputern und der maximalen Anzahl der Netzwerkadapter, die nach der unterstützten Größe des virtuellen Computers ausgewählt werden. Hierzu eine kurze Erläuterung:


			- Wenn die Anzahl der Netzwerkadapter des Quellcomputers maximal der Anzahl der Netzwerkadapter entspricht, die für die Größe des Zielcomputers zulässig ist, hat der Zielcomputer die gleiche Anzahl von Netzwerkadaptern wie der Quellcomputer.
			- Wenn die Anzahl der Netzwerkadapter für den virtuellen Quellcomputer die maximal zulässige Anzahl für die Größe des Zielcomputers übersteigt, wird die Anzahl verwendet, die maximal für die Größe des Zielcomputers zulässig ist.
			- Ein Beispiel: Wenn ein Quellcomputer zwei Netzwerkkarten besitzt und der Zielcomputer aufgrund seiner Größe vier Netzwerkkarten unterstützt, erhält der Zielcomputer zwei Netzwerkkarten. Wenn der Quellcomputer dagegen zwei Netzwerkadapter besitzt und der Zielcomputer aufgrund seiner Größe nur einen Adapter unterstützt, erhält der Zielcomputer nur einen Adapter. 	
		- **Azure-Netzwerk**: Geben Sie das Failover-Zielnetzwerk für den virtuellen Computer an. Wenn der virtuelle Computer mehrere Netzwerkadapter besitzt, müssen alle Adapter mit dem gleichen Azure-Netzwerk verbunden werden.
		- **Subnetz**: Wählen Sie für jeden Netzwerkadapter des virtuellen Computers das Subnetz im Azure-Netzwerk aus, mit dem der Computer nach einem Failover eine Verbindung herstellen soll.
		- **Ziel-IP-Adresse**: Wenn der Netzwerkadapter der virtuellen Quellmaschine für die Verwendung einer statischen IP-Adresse konfiguriert ist, können Sie die IP-Adresse für die virtuelle Zielmaschine angeben, um sicherzustellen, dass die Maschine nach dem Failover die gleiche IP-Adresse besitzt. Wenn Sie keine IP-Adresse angeben, wird beim Failover eine der verfügbaren Adressen zugewiesen. Wenn Sie eine Adresse angeben, die bereits verwendet wird, ist das Failover nicht erfolgreich.

		![Konfigurieren der Eigenschaften virtueller Computer](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_VMMultipleNic.png)




## Schritt 7: Erstellen eines Wiederherstellungsplans

Zum Testen der Bereitstellung können Sie ein Test-Failover für einen einzelnen virtuellen Computer durchführen. Alternativ können Sie einen Wiederherstellungsplan erstellen, der mehrere virtuelle Computer enthält. Lesen Sie die [weiteren Informationen](site-recovery-create-recovery-plans.md) zur Erstellung eines Wiederherstellungsplans.

## Schritt 8: Testen der Bereitstellung

Es gibt zwei Möglichkeiten, ein Testfailover in Azure auszuführen.

- **Testfailover ohne Azure-Netzwerk**: Bei dieser Art von Testfailover wird überprüft, ob die virtuelle Maschine in Azure richtig hochgefahren wird. Der virtuelle Computer wird nach einem Failover mit keinem Azure-Netzwerk verbunden.
- **Testfailover mit Azure-Netzwerk**: Bei dieser Art von Failover wird überprüft, ob die gesamte Replikationsumgebung wie erwartet hochgefahren wird und die betreffenden virtuellen Maschinen mit dem angegebenen Azure-Zielnetzwerk verbunden werden. Beachten Sie Folgendes: Beim Testfailover wird das Subnetz der virtuellen Testmaschine basierend auf dem Subnetz der virtuellen Replikatmaschine festgelegt. Dies unterscheidet sich von der regulären Replikation, bei der das Subnetz des virtuellen Replikatcomputers auf dem Subnetz des virtuellen Quellcomputers basiert.

Wenn Sie ein Testfailover durchführen möchten, ohne ein Azure-Netzwerk anzugeben, müssen Sie keine Vorbereitungen treffen.

Für ein Testfailover mit einem Azure-Zielnetzwerk müssen Sie ein neues Azure-Netzwerk erstellen, das von Ihrem Azure-Produktionsnetzwerk isoliert ist (Standardverhalten bei der Erstellung eines neuen Netzwerks in Azure). Unter [Ausführen eines Testfailovers](site-recovery-failover.md#run-a-test-failover) finden Sie weitere Details.


Um Ihre Replikation und Netzwerkbereitstellung vollständig zu testen, müssen Sie die Infrastruktur einrichten, damit die replizierte virtuelle Maschine wie erwartet funktioniert. Eine Möglichkeit ist: Richten Sie eine virtuelle Maschine als Domänencontroller mit DNS ein, und replizieren Sie sie in Azure. Verwenden Sie Site Recovery für die Erstellung im Testnetzwerk, indem Sie ein Testfailover ausführen. Lesen Sie sich die [weiteren Informationen](site-recovery-active-directory.md#considerations-for-test-failover) zu den Testfailover-Aspekten für Active Directory durch.

Führen Sie das Testfailover wie folgt aus:

1. Wählen Sie auf der Registerkarte **Wiederherstellungspläne** den gewünschten Wiederherstellungsplan aus, und klicken Sie auf **Testfailover**.
2. Wählen Sie auf der Seite **Testfailover bestätigen** den Eintrag **Kein** oder ein bestimmtes Azure-Netzwerk aus. Beachten Sie, dass ein Testfailover bei Auswahl von **Kein** zwar prüft, ob der virtuelle Computer ordnungsgemäß zu Azure repliziert wurde, die Konfiguration des Replikationsnetzwerks wird jedoch nicht geprüft.

	![Testfailover](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_TestFailoverNoNetwork.png)

3. Auf der Registerkarte **Aufträge** können Sie den Fortschritt des Failovers verfolgen. Außerdem wird Ihnen das Testreplikat des virtuellen Computers im Azure-Portal angezeigt. Wenn Sie den Zugriff auf virtuelle Computer aus Ihrem lokalen Netzwerk eingerichtet haben, können Sie eine Remotedesktopverbindung mit dem virtuellen Computer herstellen.
4. Klicken Sie auf **Test abschließen**, wenn das Failover die Endphase des Tests erreicht. Auf der Registerkarte **Auftrag** können Sie den Failoverfortschritt und -status detailliert nachverfolgen und etwaige erforderliche Aufgaben ausführen.
5. Nach dem Failover können Sie das Testreplikat des virtuellen Computers im Azure-Portal sehen. Wenn Sie den Zugriff auf virtuelle Computer aus Ihrem lokalen Netzwerk eingerichtet haben, können Sie eine Remotedesktopverbindung mit dem virtuellen Computer herstellen.

	1. Prüfen Sie, ob die virtuellen Computer erfolgreich starten.
    2. Wenn Sie nach dem Failover eine Verbindung mit dem virtuellen Computer in Azure über Remote Desktop herstellen möchten, aktivieren Sie die Remote Desktop-Verbindung auf dem virtuellen Computer, bevor Sie das Test-Failover ausführen. Außerdem müssen Sie dem virtuellen Computer einen RDP-Endpunkt hinzufügen. Hierzu können Sie ein [Azure Automation-Runbook](site-recovery-runbook-automation.md) verwenden.
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

<!----HONumber=AcomDC_0218_2016-->