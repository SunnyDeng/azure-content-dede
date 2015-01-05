<properties title="SharePoint Server Farm Configuration Details" pageTitle="SharePoint-Serverfarm-Konfigurationsdetails" description="Describes the default configuration of SharePoint farms" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" manager="timlt"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="josephd" />


#SharePoint-Serverfarm-Konfigurationsdetails#

Die SharePoint-Serverfarm ist ein Feature des Microsoft Azure-Vorschauportals für die automatische Erstellung einer vorkonfigurierten SharePoint Server 2013-Farm für Sie. Es gibt zwei Farmkonfigurationen:

- Basic
- Hohe Verfügbarkeit

Die folgenden Abschnitte beinhalten Konfigurationsdetails für jede Farm.

Weitere Informationen finden Sie unter [SharePoint-Serverfarm](../virtual-machines-sharepoint-farm-azure-preview/).

##Grundlegende SharePoint-Farm##

Die grundlegende SharePoint-Farm besteht aus drei virtuellen Computern mit der folgenden Konfiguration:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png) 

Im Folgenden finden Sie die Konfigurationsdetails:

-	Azure-Abonnement: Wird während der anfänglichen Konfiguration angegeben.
-	Azure-Domänennamen (auch bekannt als Cloud-Dienste): Separate Domänennamen werden automatisch für jeden virtuellen Computer erstellt.
-	Speicherkonto: Wird während der anfänglichen Konfiguration angegeben.
-	Virtuelles Netzwerk 	
	-   Geben Sie Folgendes ein: Nur Cloud	
    -	Adressraum: 192.168.16.0/26    

- Virtuelle Computer
	-	HostNamePrefix-DC (AD DS-Domänencontroller)
	-	HostNamePrefix-SQL (SQL Server 2014-Server)
	-	HostNamePrefix-SP (SharePoint 2013-Server)

- Domänencontroller
	-	Hostnamenspräfix: Wird während der anfänglichen Konfiguration angegeben.
	-	Größe: A1 (Standard)
	-	Domänenname: contoso.com (default)
	-	Kontoname des Domänenadministrators: Wird während der anfänglichen Konfiguration angegeben.
	-	Kontokennwort des Domänenadministrators: Wird während der anfänglichen Konfiguration angegeben.

- SQL Server
	-	Hostnamenspräfix: Wird während der anfänglichen Konfiguration angegeben.
	-	Größe: A5 (Standard)
	-	Kontoname für den Datenbankzugriff: Wird während der anfänglichen Konfiguration angegeben.
	-	Kontokennwort für den Datenbankzugriff: Wird während der anfänglichen Konfiguration angegeben.
	-	Kontoname für den SQL Server-Dienst: Wird während der anfänglichen Konfiguration angegeben.
	-	Kontokennwort für den SQL Server-Dienst: Wird während der anfänglichen Konfiguration angegeben.

- SharePoint-Server
	-	Hostnamenspräfix: Wird während der anfänglichen Konfiguration angegeben.
	-	Größe: A2 (Standard)
	-	Kontoname für die SharePoint-Farm: Wird während der anfänglichen Konfiguration angegeben.
	-	Kontokennwort für die SharePoint-Farm: Wird während der anfänglichen Konfiguration angegeben.
	-	Passphrase für SharePoint-Farm: Wird während der anfänglichen Konfiguration angegeben.


##Hochverfügbarkeit##

Die hochverfügbare SharePoint-Farm besteht aus neun virtuellen Computern mit der folgenden Konfiguration:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)
 
Im Folgenden finden Sie die Konfigurationsdetails:

-	Azure-Abonnement: Wird während der anfänglichen Konfiguration angegeben.
-	Azure-Domänennamen (auch bekannt als Cloud-Dienste): Separate Domänennamen werden automatisch für jeden virtuellen Computer erstellt.
-	Speicherkonto: Wird während der anfänglichen Konfiguration angegeben.
-	Virtuelles Netzwerk	
	-	Geben Sie Folgendes ein: Nur Cloud
	-	Adressraum: 192.168.16.0/26	

-	Virtuelle Computer
	-	HostNamePrefix-DC1 (AD DS-Domänencontroller)
	-	HostNamePrefix-DC2 (AD DS-Domänencontroller)
	-	HostNamePrefix-SQL1 (SQL Server 2014-Server)
	-	HostNamePrefix-SQL2 (SQL Server 2014-Server)
	-	HostNamePrefix-SQL0 (SQL Server 2014-Server)
	-	HostNamePrefix-WEB1 (SharePoint 2013-Server)
	-	HostNamePrefix-WEB2 (SharePoint 2013-Server)
	-	HostNamePrefix-APP1 (SharePoint 2013-Server)
	-	HostNamePrefix-APP2 (SharePoint 2013-Server)

-	Domänencontroller
	-	Hostnamenspräfix: Wird während der anfänglichen Konfiguration angegeben.
	-	Größe: A1 (Standard)
	-	Domänennamee: contoso.com (default)
	-	Kontoname des Domänenadministrators: Wird während der anfänglichen Konfiguration angegeben.
	-	Kontokennwort des Domänenadministrators: Wird während der anfänglichen Konfiguration angegeben.

-	SQL Server
	-	Hostnamenspräfix: Wird während der anfänglichen Konfiguration angegeben.
	-	Größe: A5 (Standard)
	-	Kontoname für den Datenbankzugriff: Wird während der anfänglichen Konfiguration angegeben.
	-	Kontokennwort für den Datenbankzugriff: Wird während der anfänglichen Konfiguration angegeben.
	-	Kontoname für den SQL Server-Dienst: Wird während der anfänglichen Konfiguration angegeben.
	-	Kontokennwort für den SQL Server-Dienst: Wird während der anfänglichen Konfiguration angegeben.

-	SharePoint-Server
	-	Hostnamenspräfix: Wird während der anfänglichen Konfiguration angegeben.
	-	Größe: A2 (Standard)
	-	Kontoname für die SharePoint-Farm: Wird während der anfänglichen Konfiguration angegeben.
	-	Kontokennwort für die SharePoint-Farm: Wird während der anfänglichen Konfiguration angegeben.		
	-	Passphrase für SharePoint-Farm: Wird während der anfänglichen Konfiguration angegeben.


<!--HONumber=35.1-->
