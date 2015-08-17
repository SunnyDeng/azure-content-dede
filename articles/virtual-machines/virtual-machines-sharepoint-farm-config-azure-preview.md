<properties
	pageTitle="SharePoint-Serverfarm-Konfigurationsdetails"
	description="Dieser Artikel beschreibt die Standardkonfiguration von SharePoint-Farmen, wenn Sie das SharePoint-Serverfarm-Feature des Azure-Vorschauportals nutzen."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2015"
	ms.author="josephd"/>


# SharePoint-Serverfarm-Konfigurationsdetails

Die SharePoint-Serverfarm ist ein Feature des Azure-Vorschauportals für die automatische Erstellung einer vorkonfigurierten SharePoint Server 2013-Farm für Sie. Es gibt zwei Farmkonfigurationen:

- Basic
- Hohe Verfügbarkeit

Die folgenden Abschnitte beinhalten Konfigurationsdetails für jede Farm.

Zusätzliche Informationen finden Sie im Thema zur [SharePoint-Serverfarm](virtual-machines-sharepoint-farm-azure-preview.md).

## Grundlegende SharePoint-Farm

Die grundlegende SharePoint-Farm besteht aus drei virtuellen Computern mit der folgenden Konfiguration:

![SharePoint-Farm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png)

Im Folgenden finden Sie die Konfigurationsdetails:

-	Azure-Abonnement: Wird während der anfänglichen Konfiguration angegeben.
-	Azure-Domänennamen (auch Clouddienste genannt): Separate Domänennamen werden automatisch für jeden virtuellen Computer erstellt.
-	Speicherkonto: Wird während der anfänglichen Konfiguration angegeben.
-	Virtuelles Netzwerk:
	-   Typ: Nur Cloud
    -	Adressraum: 10.0.0.0/26

- Virtuelle Computer:
	-	*HostNamePrefix*-DC (AD DS-Domänencontroller)
	-	*HostNamePrefix*-SQL (SQL Server 2014-Server)
	-	*HostNamePrefix*-SP (SharePoint 2013-Server)

- Domänencontroller:
	-	Virtual Machine-Image: WindowsServer 2012 R2
	-	Hostnamenspräfix: Wird während der anfänglichen Konfiguration angegeben.
	-	Größe: A1 (Standard).
	-	Domänenname: contoso.com (Standard).
	-	Kontoname des Domänenadministrators: Wird während der anfänglichen Konfiguration angegeben.
	-	Kennwort des Domänenadministratorkontos: Wird während der anfänglichen Konfiguration angegeben.

- Datenbankserver
	-	Virtual Machine-Abbild: SQL Server 2014 RTM Enterprise unter Windows Server 2012 R2
	-	Hostnamenspräfix: Wird während der anfänglichen Konfiguration angegeben.
	-	Größe: A5 (Standard).
	-	Kontoname für den Datenbankzugriff: Wird während der anfänglichen Konfiguration angegeben.
	-	Kennwort für das Datenbankzugriffskonto: Wird während der anfänglichen Konfiguration angegeben.
	-	Kontoname für den SQL Server-Dienst: sqlservice (Standard).
	-	Kennwort für das SQL Server-Dienstkonto: Wird während der anfänglichen Konfiguration angegeben.

- SharePoint-Server
	-	Virtual Machine-Abbild: SharePoint Server 2013-Testversion.
	-	Hostnamenspräfix: Wird während der anfänglichen Konfiguration angegeben.
	-	Größe: A2 (Standard).
	-	Kontoname für die SharePoint-Farm: sp\_farm (Standard).
	-	Kontokennwort für die SharePoint-Farm: Wird während der anfänglichen Konfiguration angegeben.
	-	Passphrase für die SharePoint-Farm: Wird während der anfänglichen Konfiguration angegeben.


## Hochverfügbare SharePoint-Farm

Die hochverfügbare SharePoint-Farm besteht aus neun virtuellen Computern mit der folgenden Konfiguration:

![SharePoint-Farm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)

Im Folgenden finden Sie die Konfigurationsdetails:

-	Azure-Abonnement: Wird während der anfänglichen Konfiguration angegeben.
-	Azure-Domänennamen (auch Clouddienste genannt): Separate Domänennamen werden entsprechend der obigen Abbildung erstellt.
-	Speicherkonto: Wird während der anfänglichen Konfiguration angegeben.
-	Virtuelles Netzwerk:
	-	Typ: Nur Cloud
	-	Adressraum: 10.0.0.0/26

-	Virtuelle Computer:
	-	*HostNamePrefix*-DC1 (AD DS-Domänencontroller)
	-	*HostNamePrefix*-DC2 (AD DS-Domänencontroller)
	-	*HostNamePrefix*-SQL1 (SQL Server 2014-Server)
	-	*HostNamePrefix*-SQL2 (SQL Server 2014-Server)
	-	*HostNamePrefix*-SQL0 (Windows Server 2012 R2-Server)
	-	*HostNamePrefix*-WEB1 (SharePoint 2013-Server)
	-	*HostNamePrefix*-WEB2 (SharePoint 2013-Server)
	-	*HostNamePrefix*-APP1 (SharePoint 2013-Server)
	-	*HostNamePrefix*-APP2 (SharePoint 2013-Server)

-	Domänencontroller:
	-	Virtual Machine-Image: WindowsServer 2012 R2
	-	Hostnamenspräfix: Wird während der anfänglichen Konfiguration angegeben.
	-	Größe: A1 (Standard).
	-	Domänenname: contoso.com (Standard).
	-	Kontoname des Domänenadministrators: Wird während der anfänglichen Konfiguration angegeben.
	-	Kennwort des Domänenadministratorkontos: Wird während der anfänglichen Konfiguration angegeben.

-	Datenbankserver:
	-	Virtual Machine-Abbild: SQL Server 2014 RTM Enterprise unter Windows Server 2012 R2
	-	Hostnamenspräfix: Wird während der anfänglichen Konfiguration angegeben.
	-	Größe: A5 (Standard) für Datenbankserver, A0 (Standard) für den Dateifreigabenzeugen.
	-	Kontoname für den Datenbankzugriff: Wird während der anfänglichen Konfiguration angegeben.
	-	Kennwort für das Datenbankzugriffskonto: Wird während der anfänglichen Konfiguration angegeben.
	-	Kontoname für den SQL Server-Dienst: sqlservice (Standard).
	-	Kennwort für das SQL Server-Dienstkonto: Wird während der anfänglichen Konfiguration angegeben.

-	SharePoint-Server:
	-	Virtual Machine-Abbild: SharePoint Server 2013-Testversion.
	-	Hostnamenspräfix: Wird während der anfänglichen Konfiguration angegeben.
	-	Größe: A2 (Standard).
	-	Kontoname für die SharePoint-Farm: sp\_farm (Standard).
	-	Kontokennwort für die SharePoint-Farm: Wird während der anfänglichen Konfiguration angegeben.
	-	Passphrase für die SharePoint-Farm: Wird während der anfänglichen Konfiguration angegeben.

> [AZURE.NOTE]Die SharePoint-Server werden aus dem SharePoint Server 2013-Testversion-Image erstellt. Um den virtuellen Computer nach dem Ablauf der Testversion weiterhin verwenden zu können, müssen Sie die Installation so abändern, dass sie einen Lizenzschlüssel einer Verkaufsversion oder einer Volumenlizenz für die Standard oder Enterprise Edition von SharePoint Server 2013 verwendet.

## Azure Resource Manager

Die SharePoint-Serverfarm-Funktion des Azure-Vorschauportals erstellt virtuelle Computer in der Dienstverwaltung. Weitere Informationen zum Erstellen von SharePoint Server 2013-Farmen im Azure Ressourcen-Manager finden Sie unter [Bereitstellen von SharePoint-Farmen mit Azure-Ressourcen-Manager-Vorlagen](virtual-machines-workload-template-sharepoint.md).

## Zusätzliche Ressourcen

[SharePoint-Serverfarm](virtual-machines-sharepoint-farm-azure-preview.md)

[SharePoint auf Azure Virtual Machines](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[Einrichten einer SharePoint-Intranetfarm in einer Hybrid Cloud zu Testzwecken](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

<!---HONumber=August15_HO6-->