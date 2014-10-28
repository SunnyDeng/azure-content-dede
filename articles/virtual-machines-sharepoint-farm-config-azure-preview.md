<properties title="SharePoint Server Farm Configuration Details" pageTitle="SharePoint Server Farm Configuration Details" description="Describes the default configuration of SharePoint farms" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="josephd"></tags>

# Konfigurationsdetails für SharePoint-Serverfarm

SharePoint-Serverfarm ist ein Feature des Microsoft Azure-Vorschauportals, das automatisch eine vorkonfigurierte SharePoint Server 2013-Farm für Sie erstellt. Es gibt zwei Farm-Konfigurationen:

-   Basic
-   Hochverfügbarkeit

Die folgenden Abschnitte enthalten Konfigurationsinformationen für jede Farm.

Weitere Informationen finden Sie unter [SharePoint-Serverfarm][SharePoint-Serverfarm].

## Basic SharePoint-Farmen

Die grundlegende SharePoint-Farm besteht aus drei virtuellen Computern mit der folgenden Konfiguration:

![SharePoint-Farm][SharePoint-Farm]

Dies sind die Konfigurationsinformationen:

-   Azure-Abonnement: Wird während der anfänglichen Konfiguration festgelegt.
-   Azure Domänennamen (auch als Cloud-Dienste bekannt): Es werden automatisch separate Domänennamen für jeden virtuellen Computer erzeugt.
-   Speicherkonto: Wird während der anfänglichen Konfiguration festgelegt.
-   Virtuelles Netzwerk

    -   Geben Sie Folgendes ein: Ausschließlich in der Cloud
    -   Adressraum: 192.168.16.0/26
-   Virtuelle Computer (Virtual Machines, VMs)

    -   HostNamePrefix-DC (AD DS Domänencontroller)
    -   HostNamePrefix-SQL (SQL Server 2014-Server)
    -   HostNamePrefix-SP (SharePoint 2013-Server)
-   Domänencontroller

    -   Hostnamenpräfix: Wird während der anfänglichen Konfiguration festgelegt.
    -   Größe: A1 (Vorgabe)
    -   Domainname: contoso.com (standard)
    -   Kontoname des Domänenadministrators: Wird während der anfänglichen Konfiguration festgelegt.
    -   Kontokennwort des Domänenadministrators: Wird während der anfänglichen Konfiguration festgelegt.
-   SQL Server

    -   Hostnamenpräfix: Wird während der anfänglichen Konfiguration festgelegt.
    -   Größe: A5(Vorgabe)
    -   Kontoname für Datenbankzugriff: Wird während der anfänglichen Konfiguration festgelegt.
    -   Kontokennwort für Datenbankzugriff: Wird während der anfänglichen Konfiguration festgelegt.
    -   Kontoname für SQL Server-Dienst: Wird während der anfänglichen Konfiguration festgelegt.
    -   Kontokennwort für SQL Server-Dienst: Wird während der anfänglichen Konfiguration festgelegt.
-   SharePoint-Server

    -   Hostnamenpräfix: Wird während der anfänglichen Konfiguration festgelegt.
    -   Größe: A2 (Vorgabe)
    -   Kontoname der SharePoint-Farm: Wird während der anfänglichen Konfiguration festgelegt.
    -   Kontokennwort der SharePoint-Farm: Wird während der anfänglichen Konfiguration festgelegt.
    -   Passphrase der SharePoint-Farm: Wird während der anfänglichen Konfiguration festgelegt.

## Hochverfügbarkeit

Die hochverfügbare SharePoint-Farm besteht aus neun virtuellen Computern mit der folgenden Konfiguration:

![SharePoint-Farm][1]

Dies sind die Konfigurationsinformationen:

-   Azure-Abonnement: Wird während der anfänglichen Konfiguration festgelegt.
-   Azure Domänennamen (auch als Cloud-Dienste bekannt): Es werden automatisch separate Domänennamen für jeden virtuellen Computer erzeugt.
-   Speicherkonto: Wird während der anfänglichen Konfiguration festgelegt.
-   Virtuelles Netzwerk

    -   Geben Sie Folgendes ein: Ausschließlich in der Cloud
    -   Adressraum: 192.168.16.0/26
-   Virtuelle Computer (Virtual Machines, VMs)

    -   HostNamePrefix-DC1 (AD DS Domänencontroller)
    -   HostNamePrefix-DC2 (AD DS Domänencontroller)
    -   HostNamePrefix-SQL1 (SQL Server 2014-Server)
    -   HostNamePrefix-SQL2 (SQL Server 2014-Server)
    -   HostNamePrefix-SQL0 (SQL Server 2014-Server)
    -   HostNamePrefix-WEB1 (SharePoint 2013-Server)
    -   HostNamePrefix-WEB2 (SharePoint 2013-Server)
    -   HostNamePrefix-APP1 (SharePoint 2013-Server)
    -   HostNamePrefix-APP2 (SharePoint 2013-Server)
-   Domänencontroller

    -   Hostnamenpräfix: Wird während der anfänglichen Konfiguration festgelegt.
    -   Größe: A1 (Vorgabe)
    -   Domainname: contoso.com (standard)
    -   Kontoname des Domänenadministrators: Wird während der anfänglichen Konfiguration festgelegt.
    -   Kontokennwort des Domänenadministrators: Wird während der anfänglichen Konfiguration festgelegt.
-   SQL-Server

    -   Hostnamenpräfix: Wird während der anfänglichen Konfiguration festgelegt.
    -   Größe: A5(Vorgabe)
    -   Kontoname für Datenbankzugriff: Wird während der anfänglichen Konfiguration festgelegt.
    -   Kontokennwort für Datenbankzugriff: Wird während der anfänglichen Konfiguration festgelegt.
    -   Kontoname für SQL Server-Dienst: Wird während der anfänglichen Konfiguration festgelegt.
    -   Kontokennwort für SQL Server-Dienst: Wird während der anfänglichen Konfiguration festgelegt.
-   SharePoint-Server

    -   Hostnamenpräfix: Wird während der anfänglichen Konfiguration festgelegt.
    -   Größe: A2 (Vorgabe)
    -   Kontoname der SharePoint-Farm: Wird während der anfänglichen Konfiguration festgelegt.
    -   Kontokennwort der SharePoint-Farm: Wird während der anfänglichen Konfiguration festgelegt.
    -   Passphrase der SharePoint-Farm: Wird während der anfänglichen Konfiguration festgelegt.

  [SharePoint-Serverfarm]: ../virtual-machines-sharepoint-farm-azure-preview/
  [SharePoint-Farm]: ./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png
  [1]: ./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png
