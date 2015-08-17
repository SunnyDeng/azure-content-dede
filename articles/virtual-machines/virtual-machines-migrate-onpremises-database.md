<properties
	pageTitle="Migrieren einer lokalen Datenbank auf SQL Server in einem virtuellen Azure-Computer"
	description="Erfahren Sie mehr über das Migrieren einer lokalen Benutzerdatenbank auf SQL Server in einem virtuellen Azure-Computer."
	services="virtual-machines"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="carlrab"/>


# Migrieren einer Datenbank zu SQL Server auf einer Azure-VM

Es gibt verschiedene Methoden zur Migration einer lokalen SQL Server-Benutzerdatenbank zu SQL Server in einer Azure-VM. In diesem Artikel werden die verschiedenen Methoden kurz angesprochen, die jeweils beste Methode für verschiedene Szenarios empfohlen und ein Lernprogramm vorgestellt, das Sie beim Durchlaufen des Assistenten für das Bereitstellen einer SQL Server-Datenbank auf einer Microsoft Azure-VM leitet.

## Was sind die primären Migrationsmethoden?

Die primären Migrationsmethoden sind:

- Verwenden des Assistenten für das Bereitstellen einer SQL Server-Datenbank auf einer Microsoft Azure-VM
- Durchführen eines lokalen Backups mit Komprimierung und anschließendes manuelles Kopieren der Sicherungsdatei auf den virtuellen Azure-Computer
- Durchführen einer Sicherung auf eine URL und Wiederherstellung im virtuellen Azure-Computer aus der URL
- Trennen und anschließendes Kopieren der Daten-und Protokolldateien in einen Azure-Blob-Speicher, dann Verbinden mit SQL Server in der Azure-VM über URL
- Konvertieren eines lokalen physischen Computers in Hyper-V-VHD, Hochladen in einen Azure-Blob-Speicher und anschließendes Bereitstellen als neue VM mithilfe des hochgeladenen VHD
- Versenden einer Festplatte mithilfe des Windows-Import/Export-Diensts

> [AZURE.NOTE]Bei einer lokalen Bereitstellung, die immer aktiviert ist, kann auch mit dem [Assistenten zum Hinzufügen von Azure-Replikaten](https://msdn.microsoft.com/library/dn463980.aspx) in Azure ein Replikat erstellt werden, auf das dann ein Failover durchgeführt werden kann.

## Auswählen der Migrationsmethode

Für eine optimale Übertragungsleistung ist die Migration der Datenbankdateien in eine Azure-VM anhand einer komprimierten Sicherungsdatei im Allgemeinen die beste Methode. Dies ist die Methode, die der [Assistent für das Bereitstellen einer SQL Server-Datenbank auf einer Microsoft Azure-VM](#azure-vm-deployment-wizard-tutorial)verwendet. Dieser Assistent ist die empfohlene Methode für das Migrieren einer lokalen Benutzerdatenbank, die auf SQL Server 2005 oder höher ausgeführt wird, auf SQL Server 2014, wenn die Sicherungsdatei der komprimierten Datenbank weniger als 1 TB groß ist.

Falls es nicht möglich ist, den Assistenten zu verwenden, weil die Datenbanksicherungsdatei zu groß ist oder die SQL Server-Zielinstanz nicht SQL Server 2014 oder höher ist, ist der Migrationsvorgang ein manueller Prozess, der i. d. R. mit einer Datenbanksicherung und dem Kopieren dieser Sicherung in Azure beginnt und dann mit dem Wiederherstellen der Datenbank abgeschlossen wird. Sie können auch die Datenbankdateien selbst in Azure kopieren. Es gibt mehrere Methoden, mit denen dieser manuelle Prozess zum Migrieren einer Datenbank in eine Azure-VM ausgeführt werden kann.

> [AZURE.NOTE]Wenn Sie aus älteren Versionen von SQL Server auf SQL Server 2014 oder SQL Server 2016 aktualisieren, sollten Sie erwägen, ob Änderungen erforderlich sind. Es wird empfohlen, dass Sie alle Abhängigkeiten von Funktionen, die von der neuen Version von SQL Server nicht unterstützt werden, im Rahmen des Migrationsprojekts behandeln. Weitere Informationen zu den unterstützten Editionen und Szenarios finden Sie unter[Aktualisieren auf SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

In der folgenden Tabelle werden alle primären Migrationsmethoden aufgeführt, und es wird erläutert, wann die Verwendung der jeweiligen Methode am besten geeignet ist.

| Methode | Version der Quelldatenbank | Version der Zieldatenbank | Größeneinschränkung für Sicherung der Quelldatenbank | Hinweise |
|---|---|---|---|---|
| [Verwenden des Assistenten für das Bereitstellen einer SQL Server-Datenbank auf einer Microsoft Azure-VM](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 oder höher | SQL Server 2014 oder höher | > 1 TB | Schnellste und einfachste Methode, sollte möglichst immer verwendet werden, um eine neue oder vorhandene SQL Server-Instanz in einem virtuellen Azure-Computer zu migrieren |
| [Durchführen einer lokalen Sicherung mit Komprimierung und anschließendes manuelles Kopieren der Sicherungsdatei auf den virtuellen Azure-Computer](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 oder höher | SQL Server 2005 oder höher | [Azure VM-Speichergrenze](https://azure.microsoft.com/de-de/documentation/articles/azure-subscription-service-limits/) | Nur verwenden, wenn Sie den Assistenten nicht verwenden können, da z. B. die Version der Zieldatenbank eine frühere ist als SQL Server 2012 SP1 CU2 oder da die Größe der Datenbanksicherung 1 TB überschreitet (bei SQL Server 2016 12,8 TB) |
| [Durchführen einer Sicherung auf eine URL und Wiederherstellung auf dem virtuellen Azure-Computer aus der URL](#backup-to-url-and-restore) | SQL Server 2012 SP1 CU2 oder höher | SQL Server 2012 SP1 CU2 oder höher | > 1 TB (für SQLServer 2016 < 12,8 TB) | Im Allgemeinen ist die Leistung der Methode [SQL Server-URL-Sicherung](https://msdn.microsoft.com/library/dn435916.aspx) etwa gleichwertig zur Verwendung des Assistenten, jedoch nicht ganz so einfach. |
| [Trennen und anschließendes Kopieren der Daten- und Protokolldateien in einen Azure Blob-Speicher, dann Anschließen an den SQL Server auf dem virtuellen Azure-Computer über URL](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 oder höher | SQL Server 2014 oder höher | [Azure VM-Speichergrenze](https://azure.microsoft.com/de-de/documentation/articles/azure-subscription-service-limits/) | Beim Anfügen von Datenbankdateien an SQL Server in einer Azure-VM [und speichern dieser Dateien mit dem Azure-Blob-Speicherdienst](https://msdn.microsoft.com/library/dn385720.aspx), insbesondere bei sehr großen Datenbanken |
| [Konvertieren eines lokalen physischen Computers in Hyper-V-VHD, Hochladen in einen Azure-Blob-Speicher und anschließendes Bereitstellen als neuen virtuellen Computer mithilfe des hochgeladenen VHD](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 oder höher | SQL Server 2005 oder höher | [Azure VM-Speichergrenze](https://azure.microsoft.com/de-de/documentation/articles/azure-subscription-service-limits/) | Eignet sich, wenn Sie [Ihre eigene SQL Server-Lizenz mitbringen](../data-management-azure-sql-database-and-sql-server-iaas/), bei der Migration einer Datenbank, die auf einer älteren Version von SQL Server ausgeführt wird, oder bei der gemeinsamen Migration von System- und Benutzerdatenbanken als Teil der Migration einer Datenbank, die von anderen Benutzer- und/oder Systemdatenbanken anhängig sind. |
| [Versenden einer Festplatte mithilfe des Windows-Import-Export-Diensts](#ship-hard-drive) | SQL Server 2005 oder höher | SQL Server 2005 oder höher | [Azure VM-Speichergrenze](https://azure.microsoft.com/de-de/documentation/articles/azure-subscription-service-limits/) | Verwenden Sie die Methode[Windows-Import-Export-Dienst](../storage-import-export-service/), wenn das manuelle Kopieren zu langsam wäre, z. B. bei sehr großen Datenbanken. |

## Lernprogramm zum Assistenten für die Azure VM-Bereitstellung

Verwenden Sie den Assistenten für das Bereitstellen einer SQL Server-Datenbank auf einer Microsoft Azure-VM in Microsoft SQL Server Management Studio, um eine lokale Benutzerdatenbank (bis zu 1 TB) auf SQL Server 2005, SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, SQL Server 2014 oder SQL Server 2016 zu SQL Server 2014 oder SQL Server 2016 auf einem virtuellen Azure-Computer zu migrieren. Verwenden Sie diesen Assistenten, um eine Benutzerdatenbank zu einem vorhandenen virtuellen Azure-Computer zu migrieren oder zu einem virtuellen Azure-Computer mit SQL Server, der während des Migrationsprozesses vom Assistenten erstellt wird. Wenn Sie eine Datenbank auf eine neuere Version von SQL Server migrieren, wird die Datenbank während des Prozesses automatisch aktualisiert.

### Abrufen der neuesten Version des Assistenten für das Bereitstellen einer SQL Server-Datenbank auf einer Microsoft Azure-VM

Verwenden Sie die neueste Version von Microsoft SQL Server Management Studio für SQL Server, um sicherzustellen, dass Sie die neueste Version des Assistenten für das Bereitstellen einer SQL Server-Datenbank auf einer Microsoft Azure-VM besitzen. Die neueste Version dieses Assistenten enthält die neuesten Updates des Azure-Portals und unterstützt die neuesten Azure VM-Images im Katalog (ältere Versionen des Assistenten funktionieren möglicherweise nicht). Um die neueste Version von Microsoft SQL Server Management Studio für SQL Server zu erhalten, [laden Sie sie herunter](http://go.microsoft.com/fwlink/?LinkId=616025), und installieren Sie sie auf einem Client-Computer mit Verbindung zu der Datenbank, die Sie für die Migration vorgesehen haben, und zum Internet.

### Konfigurieren des vorhandenen virtuellen Azure-Computers und ggf. der SQL Server-Instanz

Wenn Sie zu einer vorhandenen Azure-VM migrieren, sind die folgenden Konfigurationsschritte erforderlich:

- Konfigurieren Sie den virtuellen Azure-Computer und die SQL Server-Instanz so, dass die Verbindung von einem anderen Computer aus möglich ist, indem Sie die Schritte im Abschnitt "Verbinden mit einer SQL Server-VM-Instanz aus SSMS auf einem anderen Computer" in [Bereitstellen eines virtuellen Computers mit SQL Server auf Azure](../virtual-machines-provision-sql-server/#SSMS) befolgen. Nur die Images von SQL Server 2014 und SQL Server 2016 im Katalog werden unterstützt, wenn Sie mithilfe des Assistenten migrieren.
- Konfigurieren Sie einen offenen Endpunkt für den Cloudadapter des SQL Server-Diensts auf dem Microsoft Azure-Gateway mit dem privaten Port 11435. Dieser Port wird als Teil der Bereitstellung von SQL Server 2014 oder SQL Server 2016 auf einer Microsoft Azure-VM erstellt. Der Cloudadapter erstellt außerdem eine Windows-Firewall-Regel, um eingehende TCP-Verbindungen über den Standardport 11435 zuzulassen. Dieser Endpunkt ermöglicht dem Assistenten die Nutzung des Cloudadapterdiensts, um die Sicherungsdateien aus der lokalen Instanz auf die Azure-VM zu kopieren. Weitere Informationen finden Sie unter[Cloud-Adapter für SQL Server](https://msdn.microsoft.com/library/dn169301.aspx).

	![Erstellen eines Cloudadapter-Endpunkts](./media/virtual-machines-migrate-onpremises-database/cloud-adapter-endpoint.png)

### Ausführen des Assistenten für das Bereitstellen einer SQL Server-Datenbank auf einer Microsoft Azure-VM

1. Öffnen Sie Microsoft SQL Server Management Studio für Microsoft SQL Server 2016, und stellen Sie eine Verbindung mit der SQL Server-Instanz her, die die Datenbank enthält, die zu einer Azure-VM migriert werden soll.
2. Klicken Sie mit der rechten Maustaste auf die Datenbank, die Sie migrieren möchten, zeigen Sie auf "Tasks", und klicken Sie dann auf "Deploy to a Microsoft Azure VM".

	![Assistenten starten](./media/virtual-machines-migrate-onpremises-database/start-wizard.png)

3. Klicken Sie auf der Einführungsseite auf "Weiter".
4. Stellen Sie auf der Seite "Quelleinstellungen" eine Verbindung mit der SQL Server-Instanz her, die die Datenbank enthält, die Sie zu einer Azure-VM migrieren möchten.
5. Geben Sie einen temporären Speicherort für die Sicherungsdateien an. Wenn eine Verbindung mit einem Remoteserver hergestellt werden soll, müssen Sie ein Netzwerklaufwerk angeben.

	![Einstellungen für die Datenquelle](./media/virtual-machines-migrate-onpremises-database/source-settings.png)

6. Klicken Sie auf Weiter.
7. Klicken Sie auf der Microsoft Azure-Anmeldeseite auf "Anmelden", um sich an Ihrem Azure-Konto anzumelden.
8. Wählen Sie das Abonnement aus, das Sie nutzen möchten, und klicken Sie auf "Weiter".

	![Azure-Anmeldung](./media/virtual-machines-migrate-onpremises-database/azure-signin.png)

9. Auf der Seite "Bereitstellungseinstellungen" können Sie für den Clouddienstnamen und den Namen eines virtuellen Computers einen neuen oder vorhandenen angeben:

 - Geben Sie neue Namen für den Clouddienst und den virtuellen Computer ein, um einen neuen Clouddienst mit einem neuen virtuellen Azure-Computer anhand eines SQL Server 2014- oder SQL Server 2016-Katalogimages zu erstellen.

     - Wenn Sie einen neuen Namen für den Clouddienst angeben, legen Sie das Speicherkonto fest, das Sie verwenden möchten.

     - Wenn Sie einen vorhandenen Namen für den Clouddienst angeben, wird das Speicherkonto für Sie abgerufen und eingegeben.

 - Geben Sie einen vorhandenen Namen für den Clouddienst und einen neuen Namen für den virtuellen Computer ein, um einen neuen virtuellen Computer in einem vorhandenen Clouddienst zu erstellen. Geben Sie nur SQL Server 2014- oder SQL Server 2016-Katalogimages an.
 - Geben Sie vorhandene Namen für den Clouddienst und den Namen des virtuellen Computers ein, um einen vorhandenen virtuellen Azure-Computer zu verwenden. Dabei muss es sich um ein mit SQL Server 2014 oder SQL Server 2016 erstelltes Katalogimage handeln.

		![Deploymnent Settings](./media/virtual-machines-migrate-onpremises-database/deployment-settings.png)

10. Klicken Sie auf "Einstellungen"
  - Wenn sowohl der angegebene Clouddienstname als auch der Name des virtuellen Computers bereits vorhanden sind, werden Sie aufgefordert, den Benutzernamen und das Kennwort anzugeben.

		![Azure machine settings](./media/virtual-machines-migrate-onpremises-database/azure-machine-settings.png)

	- Wenn Sie einen neuen Namen für den virtuellen Computer angegeben haben, werden Sie aufgefordert, ein Image aus der Liste der Katalogimages auszuwählen und die folgenden Informationen anzugeben:
	  - Image – nur "SQL Server 2014" oder "SQL Server 2016" auswählen
		- Benutzername
		- Neues Kennwort
		- Kennwort bestätigen
		- Verzeichnis
		- Größe
 	- Bestätigen Sie durch Klicken das automatisch generierte Zertifikat für diesen neuen virtuellen Microsoft Azure-Computer, und klicken Sie dann auf "OK".

	![Azure-Einstellungen für neue Computer](./media/virtual-machines-migrate-onpremises-database/azure-new-machine-settings.png)

11. Geben Sie den Namen der Zieldatenbank an, wenn sich dieser vom Namen der Quelldatenbank unterscheidet. Wenn die Zieldatenbank bereits vorhanden ist, inkrementiert das System den Datenbanknamen automatisch, anstatt die vorhandene Datenbank zu überschreiben.
12. Klicken Sie auf "Weiter" und anschließend auf "Fertig stellen".

	![Ergebnisse](./media/virtual-machines-migrate-onpremises-database/results.png)

13. Stellen Sie nach Abschluss des Assistenten eine Verbindung mit dem virtuellen Computer her, und stellen Sie sicher, dass Ihre Datenbank migriert wurde.
14. Wenn Sie einen neuen virtuellen Computer erstellt haben, konfigurieren Sie den virtuellen Azure-Computer und die SQL Server-Instanz mithilfe der Schritte im Abschnitt "Verbinden mit einer SQL Server-VM-Instanz aus SSMS auf einem anderen Computer" in [Bereitstellen eines virtuellen Computers mit SQL Server auf Azure](../virtual-machines-provision-sql-server/#SSMS).

## Sichern in einer Datei und Kopieren in die VM zur Wiederherstellung

Verwenden Sie diese Methode, wenn Sie den Assistenten für das Bereitstellen einer SQL Server-Datenbank auf einer Microsoft Azure-VM nicht verwenden können, da das Migrationsziel eine Version von SQL Server vor SQL Server 2014 ist oder Ihre Sicherungsdatei größer als 1 TB ist. Wenn die Sicherungsdatei größer als 1 TB ist, müssen Sie ein Stripeset erstellen, da die maximale Größe der VM-Datenträger 1 TB beträgt. Verwenden Sie die folgenden allgemeinen Schritte zum Migrieren einer Benutzerdatenbank anhand dieser manuellen Methode:

1.	Führen Sie eine vollständige Datenbanksicherung an einem lokalen Speicherort durch.
2.	Erstellen Sie einen virtuellen Computer mit der gewünschten Version von SQL Server, oder laden Sie ihn hoch.
3.	Stellen Sie den virtuellen Computer mithilfe der Schritte in[Bereitstellung eines virtuellen Computers für SQL Server auf Azure](../virtual-machines-provision-sql-server/#SSMS) bereit.
4.	Kopieren Sie Ihre Sicherungsdateien mithilfe von Remotedesktop, Windows Explorer oder des Kopierbefehls auf den virtuellen Computer.

## Sichern und Wiederherstellen per URL

Verwenden Sie die Methode [SQL Server-URL-Sicherung](https://msdn.microsoft.com/library/dn435916.aspx), wenn die Migrationsquelle und das Ziel SQL Server 2016 ist und Sie den Assistenten für das Bereitstellen einer SQL Server-Datenbank auf einer Microsoft Azure-VM nicht verwenden können, da die Sicherungsdatei größer als 1 TB ist. Für Datenbanken, die kleiner als 1 TB sind, oder für Versionen von SQL Server vor SQL Server 2016 wird die Verwendung des Assistenten empfohlen. SQL Server 2016 unterstützt Stripesetsicherungen, die aus Leistungsgründen empfohlen werden und erforderlich sind, um die Größenbeschränkung pro Blob zu umgehen. Für sehr große Datenbanken wird die Verwendung des[Windows-Import-Export-Diensts](../storage-import-export-service/) empfohlen.

## Trennen und Kopieren nach URL kopieren sowie Anschließen per URL

Verwenden Sie diese Methode, wenn Sie planen, [diese Dateien mithilfe des Azure Blob-Speicherdiensts zu speichern](https://msdn.microsoft.com/library/dn385720.aspx) und sie mit SQL Server in einer Azure-VM zu verbinden, insbesondere bei sehr großen Datenbanken. Verwenden Sie die folgenden allgemeinen Schritte zum Migrieren einer Benutzerdatenbank anhand dieser manuellen Methode:

1.	Trennen Sie die Datenbankdateien von der lokalen Datenbankinstanz.
2.	Kopieren Sie die getrennten Datenbankdateien in den Azure-Blob-Speicher mit dem Befehlszeilenprogramm [AZCopy](../storage-use-azcopy/).
3.	Fügen Sie die Datenbankdateien über die Azure-URL der SQL Server-Instanz in der Azure-VM an.

## Konvertieren in eine VM, Hochladen zur URL und Bereitstellen als neue VM

Verwenden Sie diese Methode, um alle System- und Benutzerdatenbanken in einer lokalen SQL Server-Instanz zu einem virtuellen Computer zu migrieren. Verwenden Sie die folgenden allgemeinen Schritte, um eine vollständige SQL Server-Instanz mit dieser manuellen Methode zu migrieren:

1.	Konvertieren Sie physische oder virtuelle Computer in Hyper-V-VHDs mithilfe von [Microsoft Virtual Machine Converter](http://technet.microsoft.com/library/dn873998.aspx).
2.	Laden Sie VHD-Dateien mithilfe des Cmdlets [Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx) in den Azure-Speicher hoch.
3.	Stellen Sie einen neuen virtuellen Computer mithilfe der hochgeladenen VHD bereit.

> [AZURE.NOTE]Um eine gesamte Anwendung zu migrieren, sollten Sie den Einsatz von [Azure Site Recovery](../services/site-recovery/) erwägen.

## Versenden einer Festplatte

Mithilfe der Methode [Windows Import-Export-Dienst](../storage-import-export-service/) können Sie große Mengen von Dateidaten an Azure-Blob-Speicher übertragen, beispielsweise, wenn das Hochladen über das Netzwerk zu kostenintensiv oder nicht machbar ist. Mit diesem Dienst versenden Sie Festplatten mit den Daten an ein Azure-Rechenzentrum, in dem die Daten in Ihr Speicherkonto hochgeladen werden.

<!---HONumber=August15_HO6-->