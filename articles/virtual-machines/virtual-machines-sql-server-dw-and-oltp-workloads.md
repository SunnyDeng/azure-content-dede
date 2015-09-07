<properties 
	pageTitle="SQL Server Data Warehousing und transaktionale Arbeitslasten auf virtuellen Azure-Computern"
	description="Beschreibt die vorkonfigurierten und optimierten virtuellen SQL Server-Computerimages in Azure für Data Warehousing- und OLTP-Arbeitslasten."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="jroth"/>

# SQL Server Data Warehousing und transaktionale Arbeitslasten auf virtuellen Azure-Computern

Für die Verwendung von SQL Server für Data Warehousing oder Transaktionsarbeitslasten auf virtuellen Azure-Computern wird die Nutzung eines der vorkonfigurierten virtuellen Computerimages im Azure-Katalog für virtuelle Computer empfohlen. Diese Images wurden auf der Grundlage der Empfehlungen in [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-sql-server-performance-best-practices.md) optimiert.

Dieser Artikel konzentriert sich auf das Ausführen dieser Arbeitslasten auf virtuellen Azure-Computer (dies wird auch als „Infrastructure-as-a-Service“ oder „IaaS“ bezeichnet). Sie können Data Warehouse- und transaktionale Arbeitslasten auch als Dienst in Azure ausführen. Weitere Informationen finden Sie unter [Azure SQL Data Warehouse-Vorschau](http://azure.microsoft.com/documentation/services/sql-data-warehouse/) und [Azure SQL-Datenbank](http://azure.microsoft.com/documentation/services/sql-database/).

## Welche vorkonfigurierten VM-Images sind verfügbar?

Die folgenden vorkonfigurierten VM-Images sind im Azure-VM-Katalog verfügbar:

- [SQL Server 2014 Enterprise optimiert für transaktionale Arbeitslasten auf Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014fortransactionalworkloadswindowsserver2012r2/)

- [SQL Server 2014 Enterprise optimiert für DataWarehousing auf Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014datawarehousingwindowsserver2012r2/)

- [SQL Server 2012 SP2 Enterprise optimiert für trnsaktionale Arbeitslasten auf Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012r2)

- [SQL Server 2012 SP2 Enterprise optimiert für DataWarehousing-Arbeitslasten auf Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012r2)

- [SQL Server 2012 SP2 Enterprise optimiert für transaktionale Arbeitslasten auf Windows Server 2012](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012/)

- [SQL Server 2012 SP2 Enterprise optimiert für DataWarehousing auf Windows Server 2012](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012/)

Zurzeit unterstützen wir diese Images in VM-Instanzen, die das Anfügen von bis zu 16 Datenträgern erlauben, um äußerst hohen Durchsatz (oder aggregierte Bandbreite) zu ermöglichen. Diese Instanzen stehen in den Standard-Ebenen A4, A7, A8, A9, D4, D13, D14, F3, G4 und G5 und in der Basic-Ebene A4 zur Verfügung. Details zu den verfügbaren Größen und Optionen finden Sie unter [Größen für virtual Machines](virtual-machines-size-specs.md).

>[AZURE.NOTE]Vor September 2014 waren andere Katalogimages für transaktionale und DW-Lasten verfügbar. Für die Verwendung dieser Images war jedoch das Anfügen von Datenträgern erforderlich. Es wird empfohlen, die oben aufgeführten neueren Images zu verwenden, da sie ab dem Moment der Bereitstellung betriebsbereit sind.

## Bereitstellen einer SQL-VM aus dem Katalog mithilfe der Images für transaktionale bzw. DW-Lasten

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/) an.

1. Klicken Sie in den Azure-Menüelementen im linken Bereich auf **VIRTUELLER COMPUTER**.

1. Klicken Sie in der unteren linken Ecke auf **NEU**, und klicken Sie dann auf **COMPUTE**, **VIRTUELLER COMPUTER** und **AUS KATALOG**.

1. Wählen Sie auf der Auswahlseite für Images von virtuellen Computern eins der SQL Server-Images für transaktionale Arbeitslasten oder Data Warehousing aus.

	![Azure VM-Katalog](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814362.png)

1. Wählen Sie auf der Seite **Konfiguration von virtuellen Computern** unter der Option **GRÖSSE** eine der unterstützten Größen aus.

	![Azure VM-Katalogkonfiguration](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814363.png)

	>[AZURE.NOTE]Aktuell werden auf der Standard-Ebene nur A4, A7, A8, A9, D4, D13, D14, G3, G4 und G5 sowie auf der Basic-Ebene A4 unterstützt. Bereitstellungsversuche von nicht unterstützten VM-Größen sind nicht erfolgreich.

1. Warten Sie auf den Abschluss der Bereitstellung. Während der Bereitstellung wird der jeweilige Status auf der Seite der virtuellen Computer angezeigt (wie im Bild unten zu sehen). Wenn die Bereitstellung abgeschlossen ist, ist der Status **Wird ausgeführt** mit einem Häkchen.

	![Azure-VM-Katalogstatus](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814364.png)

## Verwenden von PowerShell zum Erstellen der Images für transaktionale/DW-Arbeitslasten

Sie können zum Erstellen der VM auch das PowerShell-Commandlet **New-AzureQuickVM** verwenden. Dazu müssen Sie den Namen Ihres Clouddiensts, den VM-Namen, den Imagenamen, den Administratorbenutzernamen und das zugehörige Kennwort und ähnliche Informationen als Parameter übergeben. Eine einfache Möglichkeit zum Abrufen des Imagenamens besteht in der Verwendung von **Get-AzureVMImage**, um alle verfügbaren VM-Images aufzulisten.

Beispielsweise gibt der folgende PowerShell-Befehl das neueste Image aus der Liste im vorhergehenden Abschnitt zurück, das mit der Imagebezeichnung **SQL Server 2012 SP2 Enterprise optimiert für DataWarehousing-Arbeitslasten auf Windows Server 2012 R2** übereinstimmt.

	(Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads on Windows Server 2012 R2"} | sort PublishedDate -Descending)[0].ImageName

Weitere Informationen zum Erstellen von Images mit PowerShell finden Sie unter [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](virtual-machines-ps-create-preconfigure-windows-vms.md).

## Bestimmte in den Images für transaktionale/DW-Arbeitslasten enthaltene Konfigurationen

Die in den Images enthaltenen Optimierungen basieren auf [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-sql-server-performance-best-practices.md). Insbesondere umfassen die Konfigurationen dieser Images die folgenden Optimierungen.

>[AZURE.NOTE]Wenn Sie eine eigene Lizenz einbringen und einen virtuellen Computer für Data Warehousing oder transaktionale Arbeitslasten von Grund auf neu erstellen, können Sie Ihre Optimierungen auf dem Leistungsartikel und dem Beispiel für Optimierungen in den nachfolgend genannten vorkonfigurierten Images aufbauen.

### Datenträgerkonfigurationen

|Konfiguration|Einstellung|
|---|---|
|Anzahl der angefügten Datenträger|15|
|Speicherplätze|Zwei Speicherpools:<br/>– 1 Datenpool mit 12-Datenträgern; feste Größe 12 TB; Spalte = 12<br/>– 1 Protokollpool mit 3 Datenträgern; feste Größe 3 TB; Spalte = 3<br/><br/>Ein Datenträger verbleibt für die Anfügung durch den Benutzer mit frei bestimmbarer Nutzung.<br/><br/>**DW**: Stripegröße = 256 KB<br/>**Transaktional**: Stripegröße = 64 KB|
|Datenträgergrößen|1 TB jeder|
|Zwischenspeichern|HostCache=None|
|Zuordnungsgröße|Größe der NTFS-Zuordnungseinheit = 64 KB|

### SQL Server-Konfigurationen

|Konfiguration|Einstellung|
|---|---|
|Startparameter|-T1117, um die Größe der Datendateien gleich zu halten, für den Fall dass Autogrow für die Datenbank erforderlich ist<br/><br/>-T1118, um die Skalierbarkeit von „tempdb“ zu unterstützen (weitere Informationen finden Sie unter [SQL Server (2005 und 2008) Trace Flag 1118 (-T1118) Usage](http://blogs.msdn.com/b/psssql/archive/2008/12/17/sql-server-2005-and-2008-trace-flag-1118-t1118-usage.aspx?WT.mc_id=Blog_SQL_Announce_Announce).)|
|Wiederherstellungsmodell|**DW**: Auf SIMPLE für die Modelldatenbank festgelegt mit ALTER DATABASE<br/>**Transaktional**: Keine Änderung|
|Setup-Standardspeicherorte|Verschieben der Verzeichnisse für das SQL Server-Fehlerprotokoll und die Ablaufverfolgungsdateien auf die Daten-Datenträger|
|Standardspeicherorte für Datenbanken|Systemdatenbanken auf die Datenträger verschoben.<br/><br/>Der Speicherort für das Erstellen von Benutzerdatenbanken wurde in Datenträger geändert.|
|Sofortige Dateiinitialisierung|Aktiviert|
|Sperren von Seiten im Speicher|Aktiviert (weitere Informationen finden Sie unter [Aktivieren der Option zum Sperren von Seiten im Speicher (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)).|

## Häufig gestellte Fragen

- Gibt es einen Preisunterschied zwischen den optimierten und den nicht optimierten Images?

	Nein. Für die optimierten Images gilt das gleiche Preismodell (Details finden Sie [hier](http://azure.microsoft.com/pricing/details/virtual-machines/)) ohne Zusatzkosten. Beachten Sie, dass die größeren VM-Instanzen mit höheren Kosten einhergehen.

- Gibt es weitere Verbesserungen der Leistung, die in Erwägung gezogen werden sollten?

	Ja, Sie sollten die Anwendung der relevanten Leistungsverbesserungen für SQL Server in Betracht ziehen:

	- [FIX: Schlechte E/A-Leistung beim Ausführen von SELECT für temporäre Tabellenoperationen in SQL Server 2012](https://support.microsoft.com/kb/2958012)

	- [FIX: "SQL Server-Leistungsindikatoren sind deaktiviert" beim Verschieben der SQL Server-Ressource in SQL Server 2014](http://support.microsoft.com/kb/2973444)

- Wo finde ich weitere Informationen zu Speicherplätzen?

	Weitere Informationen zu Speicherplätzen finden Sie unter [Häufig gestellte Fragen (FAQ) zu Speicherplätzen](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)

- Was ist der Unterschied zwischen dem neuen DW-Image und dem vorherigen?

	Für das vorhergehende DW-Image musste der Kunde zusätzliche Schritte ausführen, etwa durch Anfügen von Datenträgern nach dem Erstellen der VM, während das neue DW-Image ab dem Moment der Erstellung betriebsbereit ist, sodass es schneller verfügbar und weniger fehleranfällig ist.

- Wie gehe ich vor, wenn ich das vorherige DW-Image verwenden muss? Kann darauf noch zugegriffen werden?

	Die vorherigen VM-Images sind noch verfügbar, allerdings nicht direkt aus dem Katalog. Stattdessen können Sie aber weiterhin Powershell-Commandlets verwenden. Beispielsweise können Sie **Get-AzureVMImage** verwenden, um alle Images aufzulisten, und wenn Sie das gewünschte vorherige DW-Image basierend auf der Beschreibung und dem Veröffentlichungsdatum finden, können Sie **New-AzureVM** verwenden, um es bereitzustellen.

## Nächste Schritte

Nach der Installation jedes virtuellen Computers mit SQL Server möchten Sie wahrscheinlich die folgenden Schritte ausführen:

- [Migrieren Ihrer Daten](virtual-machines-migrate-onpremises-database.md)
- [Einrichten der Konnektivität](virtual-machines-sql-server-connectivity.md)

Weitere Informationen zum Ausführen von SQL Server auf virtuellen Azure-Computern finden Sie unter [SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=August15_HO9-->