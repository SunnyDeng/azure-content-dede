<properties title="SharePoint Server Farm" pageTitle="SharePoint-Serverfarm" description="Describes the new SharePoint Server Farm feature available in the Azure Preview Portal" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" manager="timlt"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="josephd" />

#SharePoint-Serverfarm#

Mit der SharePoint-Serverfarm erstellt das Microsoft Azure-Vorschauportal eine vorkonfigurierte SharePoint Server 2013-Farm für Sie. Damit können Sie viel Zeit sparen, wenn Sie eine grundlegende oder hochverfügbare SharePoint-Farm für eine Test- oder Entwicklungsumgebung benötigen oder wenn Sie SharePoint Server 2013 als Zusammenarbeitslösung für Ihre Organisation bewerten möchten.

Die grundlegende SharePoint-Farm besteht aus drei virtuellen Computern mit der folgenden Konfiguration:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png)

Sie können diese Farmkonfiguration für eine vereinfachte Einrichtung für die SharePoint-App-Entwicklung oder für eine erste Bewertung von SharePoint 2013 verwenden.

Die hochverfügbare SharePoint-Farm besteht aus neun virtuellen Computern mit der folgenden Konfiguration:

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png)

Sie können diese Farmkonfiguration verwenden, um höhere Clientlasten, Hochverfügbarkeit eines externen SharePoint-Standorts sowie SQL Server AlwaysOn für eine SharePoint-Farm zu testen. Außerdem können Sie diese Konfiguration für die SharePoint-App-Entwicklung in einer Hochverfügbarkeitsumgebung nutzen.
 
Konfigurationsdetails zu diesen beiden Farmen finden Sie unter [Konfigurationsdetails zur SharePoint-Serverfarm](../virtual-machines-sharepoint-farm-config-azure-preview/).

Diese Farmen haben einen vorkonfigurierten Endpunkt, der authentifizierten Webdatenverkehr (TCP-Port 80) zum SharePoint-Webserver über einen mit dem Internet verbundenen Clientcomputer ermöglicht. Dieser Endpunkt gehört zu einer vorkonfigurierten Teamwebsite. So greifen Sie auf diese Teamwebsite zu

1.	Klicken Sie im Azure-Vorschauportal auf **Durchsuchen** und dann auf **Ressourcengruppen**. 
2.	Klicken Sie in der Liste der Ressourcengruppen auf den Namen Ihrer SharePoint-Farmressourcengruppe.
3.	Klicken Sie im Bereich für Ihre SharePoint-Farmressourcengruppe auf **Bereitstellungsverlauf**. 
4.	Klicken Sie im Bereich **Bereitstellungsverlauf** auf **Microsoft.SharePointFarm**.
5.	Wählen Sie im Bereich **Microsoft.SharePointFarm** die URL im Feld "SHAREPOINTSITEURL" aus, und kopieren Sie sie. 
6.	Fügen Sie in Ihrem Internetbrowser die URL in das Adressenfeld ein.
7.	Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen ein, die Sie beim Erstellen der Farm angegeben haben.

Auf der SharePoint-Website für die Zentraladministration können Sie eigene Websites, SharePoint-Anwendungen und andere Funktionen konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren von SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx).  So greifen Sie auf die SharePoint-Website für die Zentraladministration zu

1.	Klicken Sie im Azure-Vorschauportal auf **Durchsuchen** und dann auf **Ressourcengruppen**. 
2.	Klicken Sie in der Liste der Ressourcengruppen auf den Namen Ihrer SharePoint-Farmressourcengruppe.
3.	Klicken Sie im Bereich für Ihre SharePoint-Farmressourcengruppe auf **Bereitstellungsverlauf**. 
4.	Klicken Sie im Bereich **Bereitstellungsverlauf** auf **Microsoft.SharePointFarm**.
5.	Wählen Sie im Bereich **Microsoft.SharePointFarm** die URL im Feld "SHAREPOINTCENTRALADMINURL" aus, und kopieren Sie sie. 
6.	Fügen Sie in Ihrem Internetbrowser die URL in das Adressenfeld ein.
7.	Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen ein, die Sie beim Erstellen der Farm angegeben haben.


Hinweise:

- Das Azure-Vorschauportal erstellt diese virtuellen Computer innerhalb Ihres Abonnements.
- Das Azure-Vorschauportal erstellt beide Farmen in einem virtuellen Cloudnetzwerk mit einer über das Internet zugängigen Webpräsenz. Es gibt keine Standort-zu-Standort-VPN-Verbindung mit Ihrem Unternehmensnetzwerk. 
- Sie können diese Server über Remotedesktopverbindungen verwalten.

##Schrittweise Anleitung zur Konfiguration##

Gehen Sie wie folgt vor, um Ihre SharePoint-Farm mit SharePoint Farm zu erstellen:

1. Klicken Sie im [Microsoft Azure-Vorschauportal](https://portal.azure.com/) auf **Virtuelle Computer** > **SharePoint-Serverfarm**.
2. Geben Sie im Bereich **SharePoint-Farm erstellen** den Namen einer Ressourcengruppe ein.
3. Geben Sie auf jedem virtuellen Computer in der Farm einen Benutzernamen und das Kennwort für ein lokales Administratorkonto ein. Wählen Sie einen Namen und ein schwer zu erratendes Kennwort.
4. Falls Sie eine Hochverfügbarkeitsfarm wünschen, klicken Sie auf **Hohe Verfügbarkeit aktivieren**.
5. Klicken Sie zur Konfiguration des Domänencontrollers auf den Pfeil. Sie können ein Hostnamenpräfix (standardmäßig der Ressourcengruppenname), einen Gesamtstruktur-Stammdomänennamen (standardmäßig contoso.com) und die Größe des Domänencontrollers (standardmäßig A1) angeben.
6. Klicken Sie zur Konfiguration des SQL-Servers auf den Pfeil. Sie können ein Hostnamenpräfix (standardmäßig der Ressourcengruppenname), die Größe des SQL-Servers (standardmäßig A5), Name und Kennwort des Datenbankzugriffskontos (standardmäßig das Administratorkonto), einen SQL-Server-Dienstkontonamen (standardmäßig sqlservice) sowie das Kennwort (standardmäßig dasselbe wie im Administratorkonto) angeben.
7. Klicken Sie zur Konfiguration des SharePoint-Servers auf den Pfeil. Sie können ein Hostnamenpräfix (standardmäßig der Ressourcengruppenname), die Größe des SharePoint-Servers (standardmäßig A3), Name und Kennwort des SharePoint-Benutzerkontos (Standardname ist sp_setup), Name und Kennwort des SharePoint-Farmkontos (Standardname ist sp_farm) sowie die Passphrase der SharePoint-Farm angeben. Standardmäßig wird das Administratorkennwort für das Benutzerkonto, das Farmkonto und die Passphrase von SharePoint verwendet.
8. Klicken Sie zur Konfiguration der optionalen Konfiguration (virtuelles Netzwerk oder Speicherkonto-Diagnose) auf den Pfeil.
9. Klicken Sie auf den Pfeil, um das Abonnement anzugeben.
10. Klicken Sie auf den Pfeil, um den Standort (Region) anzugeben.
11. Klicken Sie auf **Erstellen**, wenn Sie fertig sind.

##Azure Resource Manager##

Die SharePoint-Serverfarm verwendet den Azure Resource Manager und Skripte, um die Infrastruktur und die Serverkonfigurationen für diese SharePoint-Farmen automatisch zu erstellen. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit Resource Manager](http://azure.microsoft.com/de-de/documentation/articles/powershell-azure-resource-manager/).


<!--HONumber=35.1-->
