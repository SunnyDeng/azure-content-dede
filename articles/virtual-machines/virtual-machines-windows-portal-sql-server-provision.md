<properties
	pageTitle="Bereitstellen einer virtuellen SQL Server-Maschine im Azure-Ressourcen-Manager (GUI) | Microsoft Azure"
	description="Erstellen einer virtuellen SQL Server-Maschine im Azure-Ressourcen-Manager-Modus In diesem Tutorial werden die Benutzeroberfläche und Tools anstelle von Skripts verwendet."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="MikeRayMSFT"
    editor=""
	manager="jeffreyg"
	tags="azure-resource-manager" />


<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/22/2015"
	ms.author="mikeray" />

# Bereitstellen einer virtuellen SQL Server-Maschine im Azure-Ressourcen-Manager

> [AZURE.SELECTOR]
- [Klassisches Portal](virtual-machines-windows-classic-portal-sql.md)
- [PowerShell](virtual-machines-windows-classic-ps-sql-create.md)
- [Azure Resource Manager-Portal](virtual-machines-windows-portal-sql-server-provision.md)

<br/>

>[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]klassisches Bereitstellungsmodell.

In diesem umfassenden Lernprogramm erfahren Sie, wie Sie eine virtuelle Azure-Maschine im Portal bereitstellen, indem Sie das Azure-Ressourcen-Manager-Modell verwenden und SQL Server über eine Vorlage im Azure-Katalog konfigurieren.

Der Azure Virtual Machine-Katalog enthält mehrere Images mit Microsoft SQL Server. Sie können ein VM-Image im Katalog auswählen und die VM mit wenigen Klicks in Ihrer Azure-Umgebung bereitstellen.

In diesem Lernprogramm lernen Sie Folgendes:

- [Herstellen einer Verbindung mit dem Azure-Portal und Bereitstellen eines SQL-VM-Images aus dem Katalog mit dem Ressourcen-Manager-Bereitstellungsmodell](#Provision)

- [Konfigurieren der Einstellungen für die virtuelle Maschine und SQL Server](#ConfigureVM)

- [Öffnen der virtuellen Maschine per Remotedesktop](#Open)

- [Herstellen einer Verbindung mit der SQL Server-Instanz über SQL Server Management Studio auf einem anderen Computer](#Connect)

- [Nächste Schritte](#Next)

In diesem Lernprogramm wird davon ausgegangen, dass Sie bereits ein Azure-Konto haben. Wenn Sie kein Azure-Konto haben, sollten Sie die Seite [Kostenlose einmonatige Testversion](https://azure.microsoft.com/pricing/free-trial/) besuchen.

## <a id="Provision">Bereitstellen eines SQL-VM-Images aus dem Katalog mit dem Resource Manager-Bereitstellungsmodell

1. Melden Sie sich mit Ihrem Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie im Azure-Portal auf **+Neu**. Im Portal wird das Blatt **Neu** geöffnet. Die SQL Server-VM-Vorlagen befinden sich in der Gruppe **Compute** im Marketplace.

1. Klicken Sie im Blatt **Neu** auf **Compute**.
1. Klicken Sie zum Anzeigen aller Arten von Ressourcen auf dem Blatt **Compute** auf **Alle anzeigen**. <br/> ![Azure Blatt „Compute“](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade.png) <br/>
1. Klicken Sie unter **Datenbankserver** auf **SQL Server**, um alle Vorlagen anzuzeigen, die für SQL Server verfügbar sind. Unter Umständen müssen Sie nach unten zu **Datenbankserver** scrollen.
1. 	Für jede Vorlage sind eine SQL Server-Version und ein Betriebssystem angegeben. Wählen Sie in der Liste ein Image aus, um ein Blatt mit den dazugehörigen Details anzuzeigen.
1.	Das Blatt mit den Details enthält eine Beschreibung des VM-Images, und Sie können ein Bereitstellungsmodell auswählen. Wählen Sie unter **Bereitstellungsmodell auswählen** die Option **Resource Manager**, und klicken Sie auf **Erstellen**. <br/> ![Azure Blatt „Compute“](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png) <br/>

## <a id="ConfigureVM"> Konfigurieren der VM
Das Azure-Portal enthält fünf Blätter zum Konfigurieren einer virtuellen SQL Server-Maschine.

1.	Grundeinstellungen konfigurieren
1.	VM-Größe auswählen
1.	VM-Einstellungen konfigurieren
1.	SQL Server konfigurieren
1.	Zusammenfassung prüfen

## 1\. Grundeinstellungen konfigurieren
Geben Sie auf dem Blatt **Virtuellen Computer erstellen** unter **Grundlagen** die folgenden Informationen ein:

* Ein eindeutiger **Name** für den virtuellen Computer
* Im Feld **Benutzername** einen eindeutigen Benutzernamen für das lokale Administratorkonto des Computers. Dieses Konto wird auch zu einem Mitglied der festen SQL Server-Serverrolle „sysadmin“.
* Geben Sie im Feld **Kennwort** ein sicheres Kennwort ein.
* Wenn Sie über mehrere Abonnements verfügen, müssen Sie überprüfen, ob das Abonnement für die zu erstellende VM korrekt ist.
* Geben Sie im Feld **Ressourcengruppe** einen Namen für die Ressourcengruppe ein. Klicken Sie auf **Vorhandene auswählen**, um alternativ dazu eine vorhandene Ressourcengruppe zu verwenden. Eine Ressourcengruppe ist eine Sammlung von verwandten Diensten in Azure. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über den Azure Resource Manager](../resource-group-overview.md). Stellen Sie sicher, dass der **Speicherort** für Ihre Anforderungen geeignet ist.
* Klicken Sie auf **OK**, um die Einstellungen zu speichern. <br/>

>![SQL-ARM Grundlagen](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-basic.png) <br/>

## 2\. VM-Größe auswählen
Wählen Sie auf dem Blatt **Virtuellen Computer erstellen** unter **Größe** eine Größe für den virtuellen Computer aus. Im Azure-Portal werden die empfohlenen Größen angezeigt. Weitere Informationen zu den Größen virtueller Computer finden Sie unter [Sizes for virtual machines](virtual-machines-linux-sizes.md) (Größen virtueller Computer). Die Größen basieren auf der Vorlage, die Sie ausgewählt haben. Für eine Größe werden die monatlichen Kosten für die Ausführung der VM geschätzt. Wählen Sie eine VM-Größe für den Server aus. Informationen zu SQL Server-VM-Größen finden Sie unter [Performance best practices for SQL Server in Azure Virtual Machines](virtual-machines-windows-classic-sql-perf.md) (Optimieren der Leistungsfähigkeit von SQL Server in Azure Virtual Machines).

## 3\. VM-Einstellungen konfigurieren
Konfigurieren Sie auf dem Blatt **Virtuellen Computer erstellen** unter **Einstellungen** den Azure-Speicher, die Netzwerkverbindung und die Überwachung für den virtuellen Computer.

- Geben Sie unter **Speicher** einen Datenträgertyp an. Für Produktionsworkloads wird Storage Premium empfohlen.

>[AZURE.NOTE] Storage Premium ist standardmäßig aktiviert. Ihre Maschine wird automatisch in ein Größe geändert, die Storage Premium unterstützt. Wenn Sie Storage Premium deaktivieren, wird Ihre vorherige Auswahl der Maschinengröße verwendet.

- Unter **Speicherkonto** können Sie entweder den automatisch bereitgestellten Speicherkontonamen übernehmen, oder auf **Speicherkonto** klicken, um ein vorhandenes Konto auszuwählen und den Speicherkontotyp zu konfigurieren. Standardmäßig erstellt Azure ein neues Speicherkonto mit lokal redundantem Speicher.

- Unter **Netzwerk** können Sie die automatisch eingefügten Werte für Features übernehmen oder auf die einzelnen Features klicken, um die Optionen **Virtuelles Netzwerk**, **Subnetz**, **Öffentliche IP-Adresse** und **Netzwerksicherheitsgruppe** zu konfigurieren. Standardmäßig werden diese Werte von Azure automatisch konfiguriert.

- In Azure ist die **Überwachung** standardmäßig mit demselben Speicherkonto eingerichtet, das für die VM angegeben wurde. Sie können diese Einstellungen hier ändern.

- Geben Sie unter **Verfügbarkeitsgruppe** eine Verfügbarkeitsgruppe an. In diesem Tutorial können Sie hier **Keine** wählen. Falls Sie die Einrichtung von SQL AlwaysOn-Verfügbarkeitsgruppen planen, können Sie die Verfügbarkeit konfigurieren, um die Neuerstellung der virtuellen Maschine zu vermeiden. Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer](virtual-machines-windows-manage-availability.md).

## 4\. SQL Server konfigurieren
Konfigurieren Sie auf dem Blatt **Virtuellen Computer erstellen** unter **SQL Server konfigurieren** die spezifischen Einstellungen und Optimierungen für SQL Server. Die Einstellungen, die Sie für SQL Server konfigurieren können, lauten:

- Konnektivität
- Authentifizierung
- Speicheroptimierung
- Patching
- Backups
- Schlüsseltresor-Integration

### Konnektivität
Geben Sie unter **SQL-Konnektivität** die Option **Öffentlich (Internet)** an, um Verbindungen zu SQL Server von Computern oder Diensten aus dem Internet zuzulassen. Wenn diese Option aktiviert ist, konfiguriert Azure die Firewall und die Netzwerksicherheitsgruppe automatisch, um Datenverkehr über Port 1433 zuzulassen. <br/>![SQL-ARM Konnektivität](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png) <br/>

Um eine Verbindung mit SQL Server über das Internet herzustellen, müssen Sie außerdem die SQL Server-Authentifizierung aktivieren.

>[AZURE.NOTE]Schränken Sie den Quellport aus Sicherheitsgründen ein, indem Sie die Netzwerksicherheitsgruppe verwenden. Weitere Informationen finden Sie unter [Was ist eine Netzwerksicherheitsgruppe (NSG)?](../virtual-network/virtual-networks-nsg.md).

Falls Sie die Verbindungen mit dem Datenbankmodul nicht automatisch über das Internet aktivieren möchten, wählen Sie eine der folgenden Optionen:
- **Lokal (nur innerhalb der VM)**, um Verbindungen zu SQL Server nur von innerhalb der VM zuzulassen.
- **Privat (innerhalb des Virtual Network)**, um Verbindungen zu SQL Server von Computern oder Diensten im gleichen virtuellen Netzwerk zuzulassen.


Als **Port** wird standardmäßig 1433 verwendet. Sie können eine andere Portnummer angeben. Weitere Informationen finden Sie unter [Connect to a SQL Server Virtual Machine (Resource Manager) | Microsoft Azure](virtual-machines-windows-sql-connect.md) (Verbinden mit einem virtuellen SQL Server-Computer (Ressource Manager) | Microsoft Azure).



### Authentifizierung
Wenn Sie die SQL Server-Authentifizierung benötigen, klicken Sie unter **SQL-Authentifizierung** auf **Aktivieren**.

<br/>![SQL-ARM Authentifizierung](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png) <br/>


Geben Sie beim Aktivieren der SQL Server-Authentifizierung **Anmeldename** und **Kennwort** an. Dieser Benutzername wird als Anmeldung für die SQL Server-Authentifizierung verwendet und ist Mitglied der festen Serverrolle „sysadmin“. Weitere Informationen zu Authentifizierungsmodi finden Sie unter [Auswählen eines Authentifizierungsmodus](http://msdn.microsoft.com/library/ms144284.aspx). Standardmäßig wird die SQL Server-Authentifizierung von SQL Server nicht aktiviert. In diesem Szenario können lokale Administratoren auf der virtuellen Maschine eine Verbindung mit der SQL Server-Instanz herstellen.

>[AZURE.NOTE] Wenn Sie auf SQL Server über das Internet zugreifen möchten (Konnektivitätsoption „Öffentlich“), sollten Sie die SQL-Authentifizierung hier aktivieren. Für den öffentlichen Zugriff auf SQL Server muss die SQL-Authentifizierung verwendet werden.

### Speicheroptimierung
Klicken Sie auf **Speicherkonfiguration**, um die Speicheranforderungen anzugeben. Sie können die Anforderungen als Eingabe-/Ausgabevorgänge pro Sekunde (IOPS), Durchsatz in MB/s und Gesamtspeichergröße angeben. Konfigurieren Sie diese mit den Schiebereglern. Das Portal berechnet basierend auf diesen Anforderungen automatisch die Anzahl der Datenträger.

Standardmäßig optimiert Azure den Speicher für 5.000 IOPS, 200 MB und 1 TB Speicherplatz. Sie können diese Speichereinstellungen basierend auf der Workload ändern. Wählen Sie unter **Speicher optimiert für** eine der folgenden Optionen:

- **Allgemein** ist die Standardeinstellung und bietet Unterstützung für die meisten Workloads.
- Beim Verarbeitungstyp **Transaktional** wird der Speicher für herkömmliche OLTP-Datenbankworkloads optimiert.
- Bei der Option **Data Warehousing** wird der Speicher für Analyse- und Berichterstellungsworkloads optimiert.

Die folgende Abbildung zeigt das Blatt für die Speicherkonfiguration. <br/>![SQL-ARM Speicher](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png) <br/>

>[AZURE.NOTE] Die Grenzwerte für die Speicherkonfiguration hängen jeweils von der VM-Größe ab. Weitere Informationen finden Sie unter [Sizes for virtual machines](virtual-machines-linux-sizes.md) (Größen für virtuelle Computer).

### Patching
**Automatisiertes SQL-Patchen** ist standardmäßig aktiviert. Beim automatisierten Patchen kann Azure automatisch Patches für SQL Server und das Betriebssystem anwenden. Geben Sie einen Wochentag, eine Uhrzeit und eine Dauer für das Wartungsfenster an. Azure führt das Patchen dann während des Wartungsfensters durch. Für die Zeitplanung des Wartungsfensters wird die Uhrzeit des VM-Gebietsschemas verwendet. Wenn Sie nicht möchten, dass SQL Server und das Betriebssystem automatisch gepatcht werden, klicken Sie auf **Deaktivieren**.

<br/>![SQL-ARM Patchen](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png) <br/>

Weitere Informationen finden Sie unter [Automated Patching for SQL Server in Azure Virtual Machines](virtual-machines-windows-classic-ps-sql-patch.md) (Automatisches Patchen für SQL Server in Azure Virtual Machines).

### Backups
Aktivieren Sie automatische Datenbanksicherungen für alle Datenbanken unter **Automatisierte SQL-Sicherung**. Wenn Sie die automatisierte SQL-Sicherung aktivieren, können Sie Folgendes konfigurieren:

- Aufbewahrungszeitraum der Sicherung in Tagen
- Verwendetes Speicherkonto für Sicherungen
- Verschlüsselung der Sicherung Klicken Sie auf **Aktivieren**, um die Sicherung zu verschlüsseln. Geben Sie ein Kennwort an, wenn die automatischen Sicherungen verschlüsselt werden. Azure erstellt ein Zertifikat zum Verschlüsseln der Sicherungen und verwendet das angegebene Kennwort, um das Zertifikat zu schützen.

<br/>![SQL-ARM Sicherung](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup.png) <br/>

 Weitere Informationen finden Sie unter [Automatisierte Sicherung für SQL Server in Azure Virtual Machines](virtual-machines-windows-classic-ps-sql-backup.md).

### Schlüsseltresor-Integration
Klicken Sie zum Speichern von geheimen Sicherheitsschlüsseln für die Verschlüsselung in Azure auf **Azure-Schlüsseltresorintegration** und dann auf **Aktivieren**.

<br/>![SQL-ARM Azure-Schlüsseltresor-Integration](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png) <br/>

In der folgenden Tabelle sind die Parameter aufgeführt, die zum Konfigurieren der Azure-Schlüsseltresor-Integration erforderlich sind.

|PARAMETER|BESCHREIBUNG|BEISPIEL|
|----------|----------|-------|
|**Schlüsseltresor-URL** | Gibt den Speicherort des Schlüsseltresors an.|https://contosokeyvault.vault.azure.net/ |
|**Azure-Schlüsseltresor-Prinzipalname** |Gibt den Namen des Azure Active Directory-Dienstprinzipals an. Wird auch als Client-ID bezeichnet. |fde2b411-33d5-4e11-af04eb07b669ccf2|
| **Geheimer Schlüssel des Azure-Schlüsseltresor-Prinzipals**|Die Azure-Schlüsseltresor-Integration erstellt Anmeldeinformationen in SQL Server, damit die virtuelle Maschine Zugriff auf den Schlüsseltresor hat. Wählen Sie einen Namen für diese Anmeldeinformation. | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=|
|**Name der Anmeldeinformationen**|Wählen Sie einen Namen zum Identifizieren der Anmeldeinformationen.| mycred1|

Weitere Informationen finden Sie unter [Configure Azure Key Vault Integration for SQL Server on Azure VMs](virtual-machines-windows-classic-ps-sql-keyvault.md) (Konfigurieren der Azure-Schlüsseltresorintegration für SQL Server auf Azure Virtual Machines).

## 5\. Zusammenfassung prüfen
Überprüfen Sie die Zusammenfassung, und klicken Sie auf **OK**, um SQL Server, die Ressourcengruppe und die für diese VM angegebenen Ressourcen zu erstellen. Sie können die Bereitstellung über das Azure-Portal überwachen. In der Schaltfläche **Benachrichtigungen** oben auf der Seite wird der grundlegende Status der Bereitstellung angezeigt.

##<a id="Open"> Öffnen des virtuellen Computers mithilfe von Remotedesktop und Abschließen des Setups
Führen Sie diese Schritte aus, um Remotedesktop zum Öffnen der virtuellen Maschine zu verwenden:

1.	Nach der Erstellung der Azure-VM wird auf dem Azure-Dashboard ein Symbol für die VM angezeigt. Klicken Sie auf das Symbol, um Informationen zur VM anzuzeigen.
1.	Klicken Sie oben auf dem VM-Blatt auf **Verbinden**. Der Browser lädt eine RDP-Datei für die VM herunter. Öffnen Sie die RDP-Datei.
1.	Sie werden von der Remotedesktopverbindung benachrichtigt, dass der Herausgeber dieser Remoteverbindung nicht identifiziert werden kann. Sie werden gefragt, ob die Verbindung trotzdem hergestellt werden soll. Klicken Sie auf **Verbinden**.
1.	Klicken Sie im Dialogfeld **Windows-Sicherheit** auf **Anderes Konto verwenden**. Geben Sie unter **Benutzername** den <machine name><Benutzernamen> ein, den Sie beim Konfigurieren der VM angegeben haben.

Nachdem Sie eine Verbindung mit der virtuellen SQL Server-Maschine hergestellt haben, können Sie SQL Server Management Studio starten und mit Ihren Anmeldeinformationen für den lokalen Administrator eine Verbindung mit der Windows-Authentifizierung herstellen. Außerdem können Sie die Firewalleinstellungen oder die Einstellungen für die SQL Server-Konfiguration bei Bedarf nach der Bereitstellung ändern.

##<a id="Connect"> Verbinden mit SQL Server über das Internet

Wenn Sie über das Internet eine Verbindung mit Ihrem SQL Server-Datenbankmodul herstellen möchten, sind mehrere Schritte erforderlich: Konfigurieren der Firewall, Aktivieren der SQL Server-Authentifizierung und Konfigurieren der Netzwerksicherheitsgruppe. Sie benötigen eine Regel für die Netzwerksicherheitsgruppe, die TCP-Datenverkehr an Port 1433 zulässt.

Falls Sie das Portal verwenden, um mit dem Resource Manager ein SQL Server-VM-Image bereitzustellen, wurden diese Schritte für Sie ausgeführt, wenn Sie für die SQL-Konnektivitätsoption **Öffentlich** auswählen und die SQL Server-Authentifizierung aktivieren. Sie müssen aber noch einige verbleibende Schritte ausführen, um über das Internet auf die SQL Server-Instanz zugreifen zu können.

>[AZURE.NOTE] Wenn Sie bei der Bereitstellung nicht die Option „Öffentlich“ gewählt haben, müssen Sie weitere Schritte ausführen, um den Zugriff auf die SQL Server-Instanz über das Internet zu ermöglichen. Weitere Informationen finden Sie unter [Connect to a SQL Server Virtual Machine (Resource Manager) | Microsoft Azure](virtual-machines-windows-sql-connect.md) (Verbinden mit einem virtuellen SQL Server -Computer (Resource Manager) | Microsoft Azure).

Die folgenden Schritte sind nicht erforderlich, wenn Sie auf Ihre virtuelle Maschine nur lokal oder innerhalb eines Virtual Network zugreifen müssen.

> [AZURE.INCLUDE [Herstellen einer Verbindung mit SQL Server im VM-Ressourcen-Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

##<a id="Next">Nächste Schritte
Weitere Informationen zur Verwendung von SQL Server in Azure finden Sie unter [SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-classic-sql-overview.md).

<!---HONumber=AcomDC_0323_2016-->