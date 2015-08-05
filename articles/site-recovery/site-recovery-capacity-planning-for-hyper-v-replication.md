<properties
	pageTitle="Kapazitätsplanung für die Hyper-V-Replikation"
	description="Dieser Artikel enthält Anweisungen zur Verwendung des Tools Capacity Planner für Azure Site Recovery und umfasst Ressourcen zur Kapazitätsplanung für die Hyper-V-Replikation."
	services="site-recovery"
	documentationCenter="na"
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn" />
<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="06/01/2015"
	ms.author="lauraa" />

# Kapazitätsplanung für die Hyper-V-Replikation

Azure Site Recovery verwendet das Hyper-V-Replikat für die Replikation zwischen zwei lokalen VMM-Standorten oder zwischen einem lokalen VMM-Server und dem Azure-Speicher. Dieser Artikel enthält schrittweise Anweisungen zur Verwendung des Tools Capacity Planner for Hyper-V Replica für Azure Site Recovery (ASR). Das Tool Capacity Planner for Hyper-V Replica für ASR unterstützt IT-Administratoren beim Entwerfen der Server-, Speicher- und Netzwerkinfrastruktur, die für die erfolgreiche Bereitstellung des Hyper-V-Replikats und zum Überprüfen der Netzwerkverbindung zwischen zwei Standorten erforderlich ist.

## Systemanforderungen
- Betriebssystem: Windows Server® 2012 oder Windows Server® 2012 R2
- Arbeitsspeicher: 20 MB (Minimum)
- CPU: 5 % freie Auslastung (Minimum)
- Festplattenspeicher: 5 MB (Minimum)

## Schritte im Lernprogramm
- Schritt 1: Vorbereiten des primären Standorts
- Schritt 2: Vorbereiten des Wiederherstellungsstandorts, wenn es sich um einen lokalen Wiederherstellungsstandort handelt
- Schritt 3: Ausführen des Tools Capacity Planner
- Schritt 4: Interpretieren der Ergebnisse

## Schritt 1: Vorbereiten des primären Standorts
1. Erstellen Sie eine Liste aller virtuellen Hyper-V-Computer, die für die Replikation aktiviert werden müssen, und der entsprechenden primären Hyper-V-Hosts/Cluster.
2. Gruppieren Sie die primären Hyper-V-Hosts und -Cluster in eine der folgenden Kategorien:

  - Eigenständige Windows Server® 2012-Server
  - Windows Server® 2012-Cluster
  - Eigenständige Windows Server® 2012 R2-Server
  - Windows Server® 2012 R2-Cluster

3. Sie müssen das Tool Capacity Planner einmal für jede eigenständige Servergruppe und einmal für jeden Cluster ausführen.
4. Aktivieren Sie den Remotezugriff auf WMI auf allen primären Hosts und Clustern. Stellen Sie sicher, dass die richtige Gruppe von Firewallregeln und Benutzerberechtigungen festgelegt ist.

        netsh firewall set service RemoteAdmin enable

5. Aktivieren Sie die Leistungsüberwachung auf den primären Hosts.

  - Öffnen Sie die **Windows-Firewall** mit dem Snap-In **Erweiterte Sicherheit**, und aktivieren Sie dann die folgenden eingehenden Regeln:
    - COM+-Netzwerkzugriff (DCOM-IN)
    - Alle Regeln in der Gruppe "Remote-Ereignisprotokollverwaltung"

## Schritt 2: Vorbereiten des Wiederherstellungsstandorts
Wenn Sie Azure als Wiederherstellungsstandort verwenden oder der lokale Wiederherstellungsstandort noch nicht verfügbar ist, können Sie diesen Abschnitt überspringen. In diesem Fall können Sie jedoch nicht die verfügbare Bandbreite zwischen den beiden Standorten messen.

1. Bestimmen der Authentifizierungsmethode

	a. Kerberos: Zu verwenden, wenn sich der primäre und der Hyper-V-Wiederherstellungshost in der gleichen Domäne oder in Domänen befinden, die sich gegenseitig als vertrauenswürdig einstufen.

	b. Zertifikat: Zu verwenden, wenn sich der primäre und der Hyper-V-Wiederherstellungshost in verschiedenen Domänen befinden. Zertifikate können mithilfe von makecert erstellt werden. Informationen zu den erforderlichen Schritten zum Bereitstellen von Zertifikaten mit dieser Methode finden Sie im Blogbeitrag [Hyper-V Replica Certificate Based Authentication - makecert](http://blogs.technet.com/b/virtualization/archive/2013/04/13/hyper-v-replica-certificate-based-authentication-makecert.aspx) (in englischer Sprache).

2. Bestimmen Sie einen **einzelnen** Hyper-V-Wiederherstellungshost/-cluster aus dem Wiederherstellungsstandort.

	a. Dieser Wiederherstellungshost/-cluster wird zum Replizieren eines virtuellen Dummy-Computers und zum Schätzen der verfügbaren Bandbreite zwischen dem primären und sekundären Standort verwendet.

	b. **Empfohlen:** Verwenden Sie einen einzelnen Hyper-V-Wiederherstellungshost zum Durchführen der Tests.

### Vorbereiten eines einzelnen Hyper-V-Hosts als Wiederherstellungsserver
1. Klicken Sie im Hyper-V-Manager im Bereich **Aktionen** auf **Hyper-V-Einstellungen**.
2. Klicken Sie im Dialogfeld **Hyper-V-Einstellungen** auf **Replikationskonfiguration**.
3. Aktivieren Sie im Bereich "Details" die Option **Diesen Computer als Replikatserver aktivieren**.
4. Wählen Sie im Bereich **Authentifizierung und Ports** die Authentifizierungsmethode aus, die Sie zuvor festgelegt haben. Geben Sie für die Authentifizierungsmethode den zu verwendenden Port an (die Standardports sind 80 für Kerberos über HTTP und 443 für die zertifikatbasierte Authentifizierung über HTTPS).
5. Klicken Sie bei Verwendung der zertifikatbasierten Authentifizierung auf **Zertifikat auswählen**, und geben Sie dann die Zertifikatinformationen an.
6. Geben Sie im Bereich **Autorisierung und Speicherung** an, dass **alle** authentifizierten (primären) Server Replikationsdaten an diesen Replikatserver senden können.
7. Klicken Sie auf **OK** oder **Übernehmen**.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

8. Führen Sie den Befehl "netsh http show servicestate" aus, um zu überprüfen, ob der HTTPS-Listener ausgeführt wird.
9. Öffnen Sie die Firewallports:

        Port 443 (certificae-based authentication):
          Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"


        Port 80 (Kerberos):
          Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"

### Vorbereiten eines einzelnen Hyper-V-Clusters als Wiederherstellungsziel
Wenn Sie bereits einen eigenständigen Hyper-V-Host als Wiederherstellungsserver vorbereitet haben, können Sie diesen Abschnitt überspringen.

1. Konfigurieren Sie den Hyper-V-Replikatbroker

	a. Öffnen Sie im **Server-Manager** den **Failovercluster-Manager**.

	b. Stellen Sie im linken Bereich eine Verbindung mit dem Cluster her, und klicken Sie im Bereich **Aktionen** auf **Rolle konfigurieren**, während der Clustername hervorgehoben ist. Dadurch wird der **Assistent für hohe Verfügbarkeit** geöffnet.

	c. Wählen Sie im Fenster **Rolle auswählen** die Option **Hyper-V-Replikatbroker** aus.

	d. Geben Sie einen **NetBIOS-Namen** und eine **IP-Adresse** als Verbindungspunkt mit dem Cluster (als Clientzugriffspunkt bezeichnet) an, und schließen Sie den Assistenten ab. Damit ist der **Hyper-V-Replikatbroker** konfiguriert, und ein Name für den Clientzugriffspunkt wird angezeigt. Notieren Sie sich den Namen des Clientzugriffspunkts, Sie verwenden ihn später zum Konfigurieren des Replikats.

	e. Vergewissern Sie sich, dass die Rolle "Hyper-V-Replikatbroker" erfolgreich online geschaltet wird und ein Failover zwischen allen Knoten des Clusters ausführen kann. Klicken Sie dazu auf die Rolle, zeigen Sie auf **Verschieben**, und klicken Sie dann auf **Knoten auswählen**. Wählen Sie einen Knoten aus, und klicken Sie auf **OK**.

	f. Wenn Sie die zertifikatbasierte Authentifizierung verwenden, stellen Sie sicher, dass das Zertifikat für alle Clusterknoten und den Clientzugriffspunkt des Hyper-V-Replikatbrokers installiert ist.

2. Konfigurieren Sie die Einstellungen des Replikats:

	a. Öffnen Sie im **Server-Manager** den **Failovercluster-Manager**.
	
	b. Stellen Sie im linken Bereich eine Verbindung mit dem Cluster her, und klicken Sie in der Kategorie **Navigieren** des Bereichs **Details** auf **Rollen**, während der Clustername hervorgehoben ist.
	
	c. Klicken Sie mit der rechten Maustaste auf die Rolle, und wählen Sie dann **Replikationseinstellungen** aus.
	
	d. Aktivieren Sie im Bereich **Details** die Option **Diesen Cluster als Replikatserver aktivieren**.

	e. Wählen Sie im Bereich **Authentifizierung und Ports** die Authentifizierungsmethode aus, die Sie zuvor festgelegt haben. Geben Sie für die Authentifizierungsmethode den zu verwendenden Port an (die Standardports sind 80 für Kerberos über HTTP und 443 für die zertifikatbasierte Authentifizierung über HTTPS).

	f. Klicken Sie bei Verwendung der zertifikatbasierten Authentifizierung auf **Zertifikat auswählen**, und geben Sie dann die erforderlichen Zertifikatinformationen an.

	g. Legen Sie im Bereich **Autorisierung und Speicherung** fest, ob **alle** authentifizierten (primären) Server Replikationsdaten an diesen Replikatserver senden können oder ob die Replikation auf bestimmte primäre Server beschränkt ist. Sie können Platzhalterzeichen verwenden, um die Akzeptanz auf Server einer bestimmten Domäne zu beschränken, ohne die Server jeweils einzeln angeben zu müssen (z. B. *.contoso.com).

	h. Öffnen Sie die Firewallports auf allen Hyper-V-Wiederherstellungshosts: Port 443 (Zertifikatstelle): Get-ClusterNode | ForEach-Object {Invoke-command -computername \$_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}


          Port 80 (Kerberos auth):
              Get-ClusterNode | ForEach-Object {Invoke-command -computername \$_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}


## Schritt 3: Ausführen des Tools Capacity Planner
1. Laden Sie das Tool Capacity Planner herunter.
2. Führen Sie das Tool auf einem der primären Server (oder in einem der Knoten des primären Clusters) aus. Klicken Sie mit der rechten Maustaste auf die EXE-Datei, und wählen Sie dann **Als Administrator ausführen** aus.
3. Nehmen Sie die **Terms of License** an, und klicken Sie dann auf **Weiter**.
4. Wählen Sie einen Wert für **Duration for metric collection** aus. Es wird dringend empfohlen, das Tool während des Produktionsbetriebs auszuführen, um sicherzustellen, dass die repräsentativsten Daten erfasst werden. Die vorgeschlagene Dauer für die Metriksammlung ist 30 Minuten. Wenn Sie lediglich die Netzwerkverbindung überprüfen möchten, können Sie 1 Minute als Dauer auswählen.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

5. Geben Sie wie gezeigt die **Primary Site Details**, an, und klicken Sie dann auf **Weiter**.

	Geben Sie für einen eigenständigen Host den Servernamen oder den vollqualifizierten Domänennamen ein.

	Wenn der primäre Host Teil eines Clusters ist, können Sie den vollqualifizierten Domänennamen einer der folgenden Komponenten eingeben:

	a. Clientzugriffspunkt (CAP) des Hyper-V-Replikatbrokers

	b. Clustername

	c. Jeder Knoten des Clusters

  ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)


6. Geben Sie die **Replica Site Details** ein (nur für die Replikation zwischen lokalen Standorten).

  Wenn Sie die Replikation für Azure aktivieren möchten oder keinen Hyper-V-Host oder -Cluster als Wiederherstellungsserver vorbereitet haben (wie in Schritt 2 erläutert), sollten Sie Tests im Hinblick auf den Replikatstandort überspringen.

  Geben Sie die **Replica Site Details** an, und klicken Sie dann auf **Weiter**.

i. Geben Sie für einen eigenständigen Host den Servernamen oder den vollqualifizierten Domänennamen ein.

ii. Wenn der primäre Host Teil eines Clusters ist, können Sie den vollqualifizierten Domänennamen einer der folgenden Komponenten eingeben:

a. Clientzugriffspunkt (CAP) des Hyper-V-Replikatbrokers

b. Clustername

c. Jeder Knoten des Clusters

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

7. Überspringen Sie die Tests im Hinblick auf **Extended Replica site**. Sie werden von ASR nicht unterstützt.
8. Wählen Sie die gewünschten virtuellen Computer aus. Das Tool stellt eine Verbindung mit dem Cluster oder den eigenständigen Servern her, die unter **Primary Site Details** angegeben sind, und zählt die ausgeführten virtuellen Computer auf. Wählen Sie die virtuellen Computer und virtuellen Datenträger aus, für die Metriken erfasst werden sollen.

Die folgenden virtuellen Computer werden nicht aufgezählt oder angezeigt:

- Virtuelle Computer, die bereits für die Replikation aktiviert wurden
- Virtuelle Computer, die nicht ausgeführt werden

9. Geben Sie Angaben unter **Network Information** ein (dies gilt nur für die Replikation zwischen lokalen Standorten und wenn Angaben unter "Replica Site Details" gemacht werden).

Geben Sie die erforderlichen Netzwerkinformationen an, und klicken Sie dann auf **Weiter**.

- Geschätzte WAN-Bandbreite
- Das Zertifikat für die Authentifizierung (optional): Wenn Sie die zertifikatbasierte Authentifizierung verwenden möchten, sollten Sie die erforderlichen Zertifikate auf dieser Seite angeben.

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. Klicken Sie in den nächsten Bildschirmen auf **Weiter**, um das Tool zu starten.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

11. Wenn das Tool ausgeführt wurde, klicken Sie auf **View Report**, um die Ausgabe zu prüfen.

    Standardspeicherort des Berichts

    %systemdrive%\Users\Public\Documents\Capacity Planner

    Speicherort der Protokolle:

    %systemdrive%\Users\Public\Documents\CapacityPlanner

## Schritt 4: Interpretieren der Ergebnisse
Sie können Metriken ignorieren, die unter den beiden folgenden Szenarios nicht aufgeführt sind, da sie für dieses Beispielszenario nicht von Bedeutung sind.

### Replikation zwischen lokalen Standorten
  - Auswirkung der Replikation auf die Rechenkapazität und den Speicher des primären Hosts
  - Auswirkung der Replikation auf den Festplattenspeicherplatz des primären und Wiederherstellungshosts (IOPS)
  - Erforderliche Gesamtbandbreite für die Deltareplikation (MBit/s)
  - Aufgezeichnete Netzwerkbandbreite zwischen dem primären Host und dem Wiederherstellungshost (MBit/s)
  - Vorschlag für die ideale Anzahl der aktiven parallelen Übertragungen zwischen den beiden Hosts oder Clustern

### Replikation zwischen einem lokalen Standort und Azure
  - Auswirkung der Replikation auf die Rechenkapazität und den Speicher des primären Hosts
  - Auswirkung der Replikation auf den Speicherplatz des primären Hosts (IOPS)
  - Erforderliche Gesamtbandbreite für die Deltareplikation (MBit/s)

Weitere ausführliche Anweisungen finden Sie auf der [Downloadseite für Capacity Planner for Hyper-V Replica](http://go.microsoft.com/?linkid=9876170) (in englischer Sprache).

## Weitere Ressourcen
Die folgenden Ressourcen helfen Ihnen bei der Kapazitätsplanung für die Hyper-V-Replikation:

- [Update: Capacity Planner for Hyper-V Replica](http://go.microsoft.com/fwlink/?LinkId=510891) (in englischer Sprache): In diesem Blog finden Sie eine Übersicht über dieses neue Tool.

- [Capacity Planner for Hyper-V Replica](http://go.microsoft.com/fwlink/?LinkId=510892) (in englischer Sprache): Hier können Sie die neueste Version des Tools herunterladen.

- [Guided Hands-On Lab](http://go.microsoft.com/fwlink/?LinkId=510893) (in englischer Sprache): ausführliche exemplarische Vorgehensweise zur Kapazitätsplanung mit diesem Tool im TechNet-Blog von Keith Mayer.

- [Testen von Leistung und Skalierung zwischen lokalen Standorten](https://msdn.microsoft.com/library/azure/dn760892.aspx): Lesen Sie die Ergebnisse der Replikationstests für die Bereitstellung zwischen lokalen Standorten.


## Nächste Schritte

So stellen Sie ASR bereit:

- [Einrichten von Schutz zwischen einem lokalen VMM-Standort und Azure](site-recovery-vmm-to-azure.md)
- [Einrichten von Schutz zwischen einem lokalen Hyper-V-Standort und Azure](site-recovery-hyper-v-site-to-azure)
- [Einrichten von Schutz zwischen zwei lokalen VMM-Standorten](site-recovery-vmm-to-vmm)
- [Einrichten von Schutz zwischen lokalen VMM-Standorten mit einem SAN](site-recovery-vmm-san)
- [Einrichten von Schutz mit einem einzelnen VMM-Server](site-recovery-single-vmm)
 

<!---HONumber=July15_HO4-->