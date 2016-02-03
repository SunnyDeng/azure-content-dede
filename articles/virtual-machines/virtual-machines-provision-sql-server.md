<properties
	pageTitle="Bereitstellen eines virtuellen Computers mit SQL Server | Microsoft Azure"
	description="In diesem Tutorial erfahren Sie, wie Sie einen virtuellen Computer mit SQL Server in Azure erstellen und konfigurieren."
	services="virtual-machines"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"	/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="jroth"/>

# Bereitstellen eines virtuellen Computers mit SQL Server in Azure

> [AZURE.SELECTOR]
- [Classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)
- [Azure Resource Manager portal](virtual-machines-sql-server-provision-resource-manager.md)

## Übersicht

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.

In der Galerie der virtuellen Computer von Azure sind verschiedene Images zu finden, die Microsoft SQL Server enthalten. Sie können eines der Images virtueller Computer aus der Galerie auswählen, und mit wenigen Klicks können Sie den virtuellen Computer auf Ihrer Azure-Umgebung bereitstellen.

In diesem Lernprogramm lernen Sie Folgendes:

* [Herstellen einer Verbindung mit dem klassischen Azure-Portal und Bereitstellen eines virtuellen Computers aus dem Katalog](#Provision)
* [Öffnen des virtuellen Computers mithilfe von Remotedesktop und vollständige Einrichtung](#RemoteDesktop)
* [Fertigstellen der Konfigurationsschritte, um mithilfe von SQL Server Management Studio auf einem anderen Computer eine Verbindung zum virtuellen Computer herzustellen](#SSMS)
* [Nächste Schritte](#Optional)

>[AZURE.NOTE]Dieser Artikel beschreibt, wie virtuelle SQL Server-Computer mit dem vorhandenen Portal bereitgestellt werden. Es ist jedoch auch möglich, SQL Server-VMs im [neuen Portal](https://manage.windowsazure.com) zu erstellen und zu verwalten. Das neue Portal bietet einige Vorteile, zum Beispiel die standardmäßige Verwendung von Storage Premium und weitere Optionen, zu denen die automatische Anwendung von Patches, die automatisierte Sicherung und AlwaysOn-Konfigurationen gehören. Zukünftiger Inhalt wird schrittweise Anleitungen umfassen.

##<a id="Provision">Bereitstellen eines virtuellen SQL Server-Computers aus dem Katalog</a>

1. Melden Sie sich mit Ihrem Konto beim [klassischen Azure-Portal](http://manage.windowsazure.com) an. Wenn Sie kein Azure-Konto haben, sollten Sie die Seite [Kostenlose einmonatige Testversion](http://azure.microsoft.com/pricing/free-trial/) besuchen.

2. Klicken Sie im klassischen Azure-Portal unten links auf der Webseite auf **+NEU**, und klicken Sie dann nacheinander auf **COMPUTE**, auf **VIRTUELLER COMPUTER** und dann auf **AUS KATALOG**.

3. Klicken Sie auf der Seite **Image auswählen** auf **SQL SERVER**. Wählen Sie dann ein SQL Server-Image aus. Klicken Sie am unteren rechten Rand der Seite auf den Pfeil "Weiter".

	![Image auswählen](./media/virtual-machines-provision-sql-server/choose-sql-vm.png)

Aktuelle Informationen zu den unterstützten SQL Server-Images in Azure finden Sie unter [Übersicht zu SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-infrastructure-services.md).

>[AZURE.NOTE]Wenn Sie über einen virtuellen Computer verfügen, der von einem Plattformimage mit der SQL Server Evaluation-Edition erstellt wurde, können Sie kein Upgrade auf ein minütlich bezahltes Editionsimage in der Galerie durchführen. Sie haben zwei Möglichkeiten:
>
> - Sie können einen neuen virtuellen Computer erstellen, indem Sie die minütlich abgerechnete SQL Server-Edition aus dem Katalog verwenden und die Datenbankdateien zu diesem neuen virtuellen Computer migrieren. Führen Sie dazu die Schritte unter [Migrieren einer Datenbank zu SQL Server auf einem virtuellen Azure-Computer](virtual-machines-migrate-onpremises-database) aus.
> - Sie können auch eine vorhandene Instanz von SQL Server Evaluation Edition gemäß der Vereinbarung [Lizenzmobilität durch Software Assurance für Azure](http://azure.microsoft.com/pricing/license-mobility/) auf eine andere SQL Server-Edition aktualisieren, indem Sie die Schritte unter [Aktualisieren auf eine andere Edition von SQL Server](https://msdn.microsoft.com/library/cc707783.aspx) ausführen. Informationen zum Erwerb der lizenzierten Kopie von SQL Server finden Sie unter [So kaufen Sie SQL Server](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx).

4. Geben Sie zunächst auf der Seite **Konfiguration des virtuellen Computers** die folgenden Informationen ein:
	- EIN **VERÖFFENTLICHUNGSDATUM DER VERSION** Wenn mehrere Images verfügbar sind, wählen Sie das neueste aus.
	- Ein eindeutiger **NAME FÜR DEN VIRTUELLEN COMPUTER**
	- Geben Sie im Feld **NEUER BENUTZERNAME** einen eindeutigen Benutzernamen für das lokale Administratorkonto des Computers ein.
	- Geben Sie in das Feld **NEUES KENNWORT** ein sicheres Kennwort ein.
	- Geben Sie das Kennwort im Feld **KENNWORT BESTÄTIGEN** nochmals ein.
	- Wählen Sie in der Dropdownliste die geeignete **GRÖSSE** aus.

	![Konfiguration des virtuellen Computers](./media/virtual-machines-provision-sql-server/4VM-Config.png)

	>[AZURE.NOTE]Die Größe des virtuellen Computers wird während der Bereitstellung angegeben:
 	>
	> - Für Produktionsworkloads empfehlen wir die Verwendung von Storage Premium mit den folgenden empfohlenen Mindestgrößen: **DS3** für SQL Server Enterprise Edition und **DS2** für SQL Server Standard Edition. Weitere Informationen finden Sie unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-sql-server-performance-best-practices.md).
	> - Die ausgewählte Größe begrenzt die Anzahl von Datenträgern, die Sie konfigurieren können. Die aktuellsten Informationen zu verfügbaren Größen virtueller Computer und der Anzahl an Datenträgern, die Sie zu einem virtuellen Computer hinzufügen können, finden Sie unter [Größen virtueller Computer für Azure](virtual-machines-size-specs.md).

5. Klicken Sie nach der Eingabe der VM-Konfigurationsdetails rechts unten auf den Pfeil "Weiter", um fortzufahren.

5. Konfigurieren Sie auf der zweiten Seite **Konfiguration des virtuellen Computers** die Ressourcen für Netzwerk, Speicher und Verfügbarkeit:
	- Wählen Sie im Feld **Clouddienst** die Option **Einen neuen Clouddienst erstellen** aus.
	- Geben Sie im Feld **DNS-Name des Clouddiensts** den ersten Teil eines DNS-Namens Ihrer Wahl ein, sodass insgesamt ein Name des Formats **TESTNAME.cloudapp.net** entsteht.
	- Wählen Sie ein **ABONNEMENT** aus, wenn Sie über mehrere Abonnements verfügen. Die Auswahl bestimmt, welche **Speicherkonten** verfügbar sind.
	- Wählen Sie im Feld **REGION/AFFINITY GROUP/VIRTUAL NETWORK** eine Region aus, in der dieses virtuelle Image gehostet wird.
	- Erstellen Sie in **Speicherkonto** automatisch ein Konto, oder wählen Sie ein Konto aus der Liste aus. Ändern Sie das **ABONNEMENT**, um weitere Konten anzuzeigen.
	- Wählen Sie im Feld **AVAILABILITY SET** den Eintrag **(none)**.
	- Lesen Sie sich die Bedingungen durch, und bestätigen Sie diese.


6. Klicken Sie auf den Pfeil zum Aufrufen der nächsten Seite, um fortzufahren.


7. Klicken Sie auf das Häkchen unten rechts, um fortzufahren.

8. Warten Sie, während Azure den virtuellen Computer vorbereitet. Der virtuelle Computer wird folgende Phasen durchlaufen:

	- **Wird gestartet (Bereitstellung)**
	- **Beendet**
	- **Wird gestartet (Bereitstellung)**
	- **Wird ausgeführt (Bereitstellung)**
	- **Wird ausgeführt**


##<a id="RemoteDesktop">Öffnen des virtuellen Computers mit Remotedesktop, um das Setup abzuschließen</a>

1. Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf den Namen des virtuellen Computers, um zur Seite "DASHBOARD" zu gelangen. Klicken Sie unten auf der Seite auf **Verbinden**.

2. Klicken Sie auf die Schaltfläche **Öffnen**.

	![Klicken Sie auf die Schaltfläche "Öffnen"](./media/virtual-machines-provision-sql-server/click-open-to-connect.png)

3. Klicken Sie im Dialogfeld **Windows-Sicherheit** auf **Anderes Konto verwenden**.

	![Klicken Sie auf "Anderes Konto verwenden"](./media/virtual-machines-provision-sql-server/credentials.png)

4. Verwenden Sie den Namen des Computers als Domänennamen gefolgt von Ihrem Administratornamen im folgenden Format: `machinename\username`. Geben Sie Ihr Kennwort ein, und stellen Sie eine Verbindung mit dem Computer her.

4. Beim ersten Anmelden werden einige Prozesse ausgeführt, darunter die Einrichtung Ihres Desktops, Windows-Updates und die Fertigstellung der ersten Windows-Konfigurationsaufgaben (sysprep). Nachdem Windows-sysprep abgeschlossen ist, stellt die SQL Server-Einrichtung die Konfigurationsaufgaben fertig. Diese Tasks können während der Ausführung eine Verzögerung von wenigen Minuten verursachen. `SELECT @@SERVERNAME` gibt den richtigen Namen möglicherweise erst zurück, wenn die Einrichtung von SQL Server abgeschlossen ist. Zudem wird SQL Server Management Studio möglicherweise nicht auf der Startseite angezeigt.

Nachdem Sie über Windows Remotedesktop mit dem virtuellen Computer verbunden sind, funktioniert der virtuelle Computer wie jeder andere Computer. Stellen Sie ganz normal eine Verbindung zur Standardinstanz von SQL Server mit SQL Server Management Studio, das auf dem virtuellen Computer ausgeführt wird, her.

##<a id="SSMS">Herstellen einer Verbindung mit der Instanz eines virtuellen SQL Server-Computers über SSMS auf einem anderen Computer</a>

Die folgenden Schritte veranschaulichen, wie Sie mithilfe von SQL Server Management Studio (SSMS) über das Internet eine Verbindung mit der SQL Server-Instanz herstellen. Mit den gleichen Schritten ermöglichen Sie jedoch auch den Zugriff der lokal und im klassischen Azure-Bereitstellungsmodell ausgeführten Anwendungen auf den virtuellen SQL Server-Computer. Wenn Ihr virtueller Computer im Ressourcen-Manager-Modell bereitgestellt wird, finden Sie weitere Informationen unter [Verbinden mit SQL Server-Instanzen auf virtuellen Azure-Computern (Ressourcen-Manager)](virtual-machines-sql-server-connectivity-resource-manager.md).

Bevor Sie eine Verbindung mit der Instanz von SQL Server über einen anderen virtuellen Computer oder über das Internet herstellen können, müssen Sie folgende Aufgaben abschließen, die in den nächsten Abschnitten beschrieben werden:

- [Erstellen eines TCP-Endpunkts für den virtuellen Computer](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Öffnen der TCP-Ports in der Windows-Firewall](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Konfigurieren von SQL Server für das Abhören des TCP-Protokolls](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Konfigurieren von SQL Server für die Authentifizierung mit gemischtem Modus](#configure-sql-server-for-mixed-mode-authentication)
- [Erstellen von Anmeldenamen für die SQL Server-Authentifizierung](#create-sql-server-authentication-logins)
- [Bestimmen des DNS-Namens des virtuellen Computers](#determine-the-dns-name-of-the-virtual-machine)
- [Verbinden mit dem Datenbankmodul von einem anderen Computer aus](#connect-to-the-database-engine-from-another-computer)

Der Verbindungspfad wird von folgendem Diagramm zusammengefasst:

![Verbinden mit einem virtuellen SQL Server-Computer](../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[AZURE.INCLUDE [Herstellen einer Verbindung mit SQL Server auf einem klassischen VM-TCP-Endpunkt](../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[AZURE.INCLUDE [Herstellen einer Verbindung mit SQL Server auf einem virtuellen Computer](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Herstellen einer Verbindung mit SQL Server auf einem klassischen virtuellen Computer – Schritte](../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a id="cdea">Verbinden mit dem Datenbankmodul von Ihrer Anwendung aus</a>

Wenn Sie eine Verbindung zu einer Instanz von SQL Server, die auf einem virtuellen Azure-Computer ausgeführt wird, mithilfe von Management Studio herstellen können, sollten Sie in der Lage sein, eine Verbindung mithilfe einer Verbindungszeichenfolge wie unten herzustellen.

	connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Weitere Informationen finden Sie unter [How to Troubleshoot Connecting to the SQL Server Database Engine](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) (So beheben Sie Fehler bei der Verbindung zum SQL Server-Datenbankmodul, in englischer Sprache).

##<a id="Optional">Nächste Schritte</a>

Sie haben jetzt erfahren, wie Sie einen SQL Server auf einem virtuellen Azure-Computer mithilfe des Plattform-Image erstellen und konfigurieren. In vielen Fällen ist der nächste Schritt, die Datenbanken auf diese neue SQL Server-VM zu migrieren. Anleitungen zur Datenbankmigration finden Sie unter [Migrieren einer Datenbank zu SQL Server auf einer Azure-VM](virtual-machines-migrate-onpremises-database.md).

Die folgende Liste enthält zusätzliche Ressourcen für SQL Server auf virtuellen Azure-Computern.

### Empfohlene Ressourcen für SQL Server in Azure-VMs:
- [Übersicht zu SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-infrastructure-services.md)

- [Verbinden mit SQL Server-Instanzen auf virtuellen Azure-Computern](virtual-machines-sql-server-connectivity.md)

- [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-sql-server-performance-best-practices.md)

- [Sicherheitsüberlegungen für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-security-considerations.md)

### Hohe Verfügbarkeit und Notfallwiederherstellung:
- [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)

- [Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-backup-and-restore.md)

### SQL Server-Workloads in Azure:
- [SQL Server-Business Intelligence auf virtuellen Azure-Computern](virtual-machines-sql-server-business-intelligence.md)

### Whitepaper:
- [Grundlegendes zur Azure SQL-Datenbank und SQL Server in Azure Virtual Machines](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [Anwendungsmuster und Entwicklungsstrategien für SQL Server auf Azure Virtual Machines](virtual-machines-sql-server-application-patterns-and-development-strategies.md)

<!---HONumber=AcomDC_0107_2016-->