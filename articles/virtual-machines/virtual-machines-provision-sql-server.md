<properties 
	pageTitle="Bereitstellen eines virtuellen Computers mit SQL Server in Azure" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie einen virtuellen Computer mit SQL Server in Azure erstellen und konfigurieren." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg" 
	editor="monicar"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/28/2015" 
	ms.author="jroth"/>

# Bereitstellen eines virtuellen Computers mit SQL Server auf Azure #

In der Galerie der virtuellen Computer von Azure sind verschiedene Images zu finden, die Microsoft SQL Server enthalten. Sie können eines der Images virtueller Computer aus der Galerie auswählen, und mit wenigen Klicks können Sie den virtuellen Computer auf Ihrer Azure-Umgebung bereitstellen.

In diesem Lernprogramm lernen Sie Folgendes:

* [Verbinden mit dem Azure-Verwaltungsportal und Bereitstellen eines virtuellen Computers aus dem Katalog](#Provision)
* [Öffnen des virtuellen Computers mithilfe von Remotedesktop und vollständige Einrichtung](#RemoteDesktop)
* [Fertigstellen der Konfigurationsschritte, um mithilfe von SQL Server Management Studio auf einem anderen Computer eine Verbindung zum virtuellen Computer herzustellen](#SSMS)
* [Nächste Schritte](#Optional)

##<a id="Provision">Bereitstellen eines virtuellen SQL Server-Computers aus dem Katalog</a>

1. Melden Sie sich auf dem [Azure-Verwaltungsportal](http://manage.windowsazure.com) bei Ihrem Konto an. Wenn Sie kein Azure-Konto haben, sollten Sie die Seite [Kostenlose einmonatige Testversion](http://azure.microsoft.com/pricing/free-trial/) besuchen.

2. Klicken Sie im Azure-Verwaltungsportal unten links auf der Webseite auf **+NEU**, klicken Sie auf **COMPUTE**, klicken Sie auf **VIRTUELLER COMPUTER** und dann auf **AUS GALERIE**.

3. Klicken Sie auf der Seite **Image auswählen** auf **SQL SERVER**. Wählen Sie dann ein SQL Server-Image aus. Klicken Sie am unteren rechten Rand der Seite auf den Pfeil "Weiter".

	![Image auswählen](./media/virtual-machines-provision-sql-server/choose-sql-vm.png)

Aktuelle Informationen zu den unterstützten SQL Server-Images in Azure finden Sie unter [Erste Schritte mit SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294720).

>[AZURE.NOTE]Wenn Sie über einen virtuellen Computer verfügen, der von einem Plattformimage mit der SQL Server Evaluation-Edition erstellt wurde, können Sie kein Upgrade auf ein minütlich bezahltes Editionsimage in der Galerie durchführen. Sie haben zwei Möglichkeiten:
>
> - Sie können einen neuen virtuellen Computer erstellen, indem Sie die minütlich abgerechnete SQL Server-Edition aus dem Katalog verwenden und die Datenbankdateien in diesen neuen virtuellen Computer migrieren, indem Sie den Schritten unter [Migrieren einer Datenbank zu SQL Server in einer Azure VM](virtual-machines-migrate-onpremises-database) folgen.
> - Sie können auch eine vorhandene Instanz von SQL Server Evaluation Edition gemäß der Vereinbarung [Lizenzmobilität von Software Assurance unter Windows Azure](http://azure.microsoft.com/pricing/license-mobility/) auf eine andere SQL Server-Edition aktualisieren, indem Sie die in [Aktualisieren auf eine andere Edition von SQL Server](https://msdn.microsoft.com/library/cc707783.aspx) beschriebenen Schritte ausführen. Informationen zum Erwerb der lizenzierten Kopie von SQL Server finden Sie unter [So kaufen Sie SQL Server](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx).

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
	> - A2 ist die kleinste empfohlene Größe für Produktionsarbeitsauslastungen. 
    > - Die empfohlene Minimalgröße für einen virtuellen Computer ist A3, wenn SQL Server Enterprise Edition verwendet wird.
    > - Wählen Sie A3 oder höher aus, wenn Sie SQL Server Enterprise Edition verwenden.
   	> - Wählen Sie A4 oder höher aus, wenn Sie SQL Server 2012- oder 2014 Enterprise-Images verwenden, die für Transaktionsworkloads optimiert sind.  
   	> - Wählen Sie A7 oder höher aus, wenn Sie SQL Server 2012- oder 2014 Enterprise-Images verwenden, die für Data Warehouse-Workloads optimiert sind. 
   	> - Die beste Leistung erhalten Sie bei Nutzung von DS2 oder DS3 mit Storage Premium. Weitere Informationen finden Sie unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](https://msdn.microsoft.com/library/azure/dn133149.aspx).
   	> - Die ausgewählte Größe begrenzt die Anzahl der Datenträger, die Sie konfigurieren können. Die aktuellsten Informationen zu verfügbaren Größen virtueller Computer und der Anzahl an Datenträgern, die Sie zu einem virtuellen Computer hinzufügen können, finden Sie unter [Größen virtueller Computer für Azure](virtual-machines-size-specs.md).

5. Klicken Sie nach der Eingabe der VM-Konfigurationsdetails rechts unten auf den Pfeil "Weiter", um fortzufahren.

5. Konfigurieren Sie auf der zweiten Seite **Konfiguration des virtuellen Computers** die Ressourcen für Netzwerk, Speicher und Verfügbarkeit:
	- Wählen Sie im Feld **Cloud-Dienst** die Option **Einen neuen Cloud-Dienst erstellen** aus.
	- Geben Sie im Feld **DNS-Name des Cloud-Diensts** den ersten Teil eines DNS-Namens Ihrer Wahl ein, sodass insgesamt ein Name des Formats **TESTNAME.cloudapp.net** entsteht. 
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

[AZURE.INCLUDE [Herstellen einer Verbindung mit SQL Server auf einem virtuellen Computer](../../includes/virtual-machines-sql-server-connection-steps.md)]

## <a id="cdea">Verbinden mit dem Datenbankmodul von Ihrer Anwendung aus</a>

Wenn Sie eine Verbindung zu einer Instanz von SQL Server, die auf einem virtuellen Azure-Computer ausgeführt wird, mithilfe von Management Studio herstellen können, sollten Sie in der Lage sein, eine Verbindung mithilfe einer Verbindungszeichenfolge wie unten herzustellen.

	connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Weitere Informationen finden Sie unter [How to Troubleshoot Connecting to the SQL Server Database Engine](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) (So beheben Sie Fehler bei der Verbindung zum SQL Server-Datenbankmodul, in englischer Sprache).

##<a id="Optional">Nächste Schritte</a>

Sie haben jetzt erfahren, wie Sie einen SQL Server auf einem virtuellen Azure-Computer mithilfe des Plattform-Image erstellen und konfigurieren. In vielen Fällen ist der nächste Schritt, die Datenbanken auf diese neue SQL Server-VM zu migrieren. Anleitungen zur Datenbankmigration finden Sie unter [Migrieren einer Datenbank zu SQL Server auf einer Azure-VM](virtual-machines-migrate-onpremises-database.md).

Zusätzlich zu diesen Ressourcen wird empfohlen, dass Sie sich die [anderen Themen im Zusammenhang mit in Azure Virtual Machines ausgeführtem SQL Server ansehen](virtual-machines-sql-server-infrastructure-services.md). Die folgende Liste enthält einige spezifische Empfehlungen.

### Empfohlene Ressourcen für SQL Server in Azure-VMs:
- [Erste Schritte mit SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294720)

- [Konnektivitätsüberlegungen für SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294723)

- [Leistungsüberlegungen zu SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/?LinkId=294724)

- [Sicherheitsüberlegungen für SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294725)

### Hohe Verfügbarkeit und Notfallwiederherstellung:
- [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294727)

- [Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294728)

### SQL Server-Workloads in Azure:
- [SQL Server-Business Intelligence auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/p/?LinkId=294729)

- [SQL Server Data Warehousing und transaktionale Arbeitslasten auf virtuellen Azure-Computern](http://msdn.microsoft.com/library/windowsazure/dn387396.aspx)

### Whitepaper:
- [Grundlegendes zur Azure SQL-Datenbank und SQL Server in Azure Virtual Machines](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [Anwendungsmuster und Entwicklungsstrategien für SQL Server auf Azure Virtual Machines](http://msdn.microsoft.com/library/azure/dn574746.aspx)

<!----HONumber=August15_HO8-->