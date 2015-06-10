<properties 
	pageTitle="SharePoint-Serverfarm" 
	description="Beschreibt das neue im Azure-Vorschauportal verfügbare SharePoint-Serverfarmfeature." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-sharepoint" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="josephd"/>

# SharePoint-Serverfarm

Mit der SharePoint-Serverfarm erstellt das Microsoft Azure-Vorschauportal eine vorkonfigurierte SharePoint Server 2013-Farm für Sie. Damit können Sie viel Zeit sparen, wenn Sie eine grundlegende oder hochverfügbare SharePoint-Farm für eine Test- oder Entwicklungsumgebung benötigen oder wenn Sie SharePoint Server 2013 als Zusammenarbeitslösung für Ihre Organisation bewerten möchten.

Die grundlegende SharePoint-Farm besteht aus drei virtuellen Computern mit der folgenden Konfiguration:

![SharePoint-Farm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png)

Sie können diese Farmkonfiguration für eine vereinfachte Einrichtung für die SharePoint-App-Entwicklung oder für eine erste Bewertung von SharePoint 2013 verwenden.

Die hochverfügbare SharePoint-Farm besteht aus neun virtuellen Computern mit der folgenden Konfiguration:

![SharePoint-Farm](./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png)

Sie können diese Farmkonfiguration verwenden, um höhere Clientlasten, Hochverfügbarkeit eines externen SharePoint-Standorts sowie SQL Server AlwaysOn für eine SharePoint-Farm zu testen. Außerdem können Sie diese Konfiguration für die SharePoint-App-Entwicklung in einer Hochverfügbarkeitsumgebung nutzen.
 
Konfigurationsdetails zu diesen beiden Farmen finden Sie unter [Konfigurationsdetails zur SharePoint-Serverfarm](virtual-machines-sharepoint-farm-config-azure-preview.md).

## Schrittweise Anleitung zur Konfiguration
 
Gehen Sie wie folgt vor, um eine SharePoint-Farm mit der Vorlage "SharePoint-Serverfarm" zu erstellen:

1. Klicken Sie im [Microsoft Azure-Vorschauportal](https://portal.azure.com/) auf **Neu** > **Server** > **SharePoint-Serverfarm**. Wenn **SharePoint-Serverfarm** nicht angezeigt wird, klicken Sie auf **Neu** > **Server** > **Azure Marketplace**, geben Sie **SharePoint** in **Alles durchsuchen** ein, und klicken Sie dann auf **SharePoint-Serverfarm**. 
2. Geben Sie im Bereich **SharePoint-Farm erstellen** den Namen einer Ressourcengruppe ein.
3. Geben Sie auf jedem virtuellen Computer in der Farm einen Benutzernamen und das Kennwort für ein lokales Administratorkonto ein. Wählen Sie einen Namen und ein Kennwort aus, die schwer zu erraten sind. Notieren Sie es dann, und lagern Sie es an einem sicheren Ort.
4. Falls Sie eine Hochverfügbarkeitsfarm wünschen, klicken Sie auf **Hohe Verfügbarkeit aktivieren**.
5. Klicken Sie zur Konfiguration des Domänencontrollers auf den Pfeil. Sie können ein Hostnamenpräfix (standardmäßig der Ressourcengruppenname), einen Gesamtstruktur-Stammdomänennamen (standardmäßig contoso.com) und die Größe des Domänencontrollers (standardmäßig A1) angeben.
6. Klicken Sie zur Konfiguration des SQL-Servers auf den Pfeil. Sie können ein Hostnamenpräfix (standardmäßig der Ressourcengruppenname), die Größe des SQL-Servers (standardmäßig A5), Name und Kennwort des Datenbankzugriffskontos (standardmäßig das Administratorkonto), einen SQL-Server-Dienstkontonamen (standardmäßig sqlservice) sowie das Kennwort (standardmäßig dasselbe wie im Administratorkonto) angeben.
7. Klicken Sie zur Konfiguration des SharePoint-Servers auf den Pfeil. Sie können ein Hostnamenpräfix (standardmäßig der Ressourcengruppenname), die Größe des SharePoint-Servers (standardmäßig A2), Name und Kennwort des SharePoint-Benutzerkontos (Standardname ist "sp_setup"), Name und Kennwort des SharePoint-Farmkontos (Standardname ist "sp_farm") sowie die Passphrase der SharePoint-Farm angeben. Standardmäßig wird das Administratorkennwort für das Benutzerkonto, das Farmkonto und die Passphrase von SharePoint verwendet.
8. Um die optionalen Konfigurationseinstellungen für das virtuelle Netzwerk, das Speicherkonto und die Diagnose zu konfigurieren, klicken Sie auf den entsprechenden Pfeil.
9. Klicken Sie auf den Pfeil, um das Abonnement anzugeben.
10. Klicken Sie auf **Erstellen**, wenn Sie fertig sind.

> [AZURE.NOTE]Auf dem Domänencontroller werden die Active Directory-Verwaltungstools nicht standardmäßig installiert. Zur Installation führen Sie auf dem virtuellen Domänencontrollercomputer den Befehl **Install-WindowsFeature AD DS-IncludeManagementTools** von einer Windows PowerShell-Eingabeaufforderung mit Administratorrechten aus.

## Zugreifen auf und Verwalten der SharePoint-Farmen

Die SharePoint-Farmen haben einen vorkonfigurierten Endpunkt, der nicht authentifizierten Webdatenverkehr (TCP-Port 80) zum SharePoint-Webserver über einen mit dem Internet verbundenen Clientcomputer ermöglicht. Dieser Endpunkt gehört zu einer vorkonfigurierten Teamwebsite. So greifen Sie auf die Teamwebsite zu

1.	Klicken Sie im Azure-Vorschauportal auf **Durchsuchen** und dann auf **Ressourcengruppen**. 
2.	Klicken Sie in der Liste der Ressourcengruppen auf den Namen Ihrer SharePoint-Farmressourcengruppe.
3.	Klicken Sie im Bereich für Ihre SharePoint-Farmressourcengruppe auf **Bereitstellungsverlauf**. 
4.	Klicken Sie im Bereich **Bereitstellungsverlauf** auf **Microsoft.SharePointFarm**.
5.	Wählen Sie im Bereich **Microsoft.SharePointFarm** die URL im Feld „SHAREPOINTSITEURL“ aus, und kopieren Sie sie. 
6.	Fügen Sie in Ihrem Internetbrowser die URL in das Adressenfeld ein.
7.	Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen ein, die Sie beim Erstellen der Farm angegeben haben.

Auf der SharePoint-Website für die Zentraladministration können Sie eigene Websites, SharePoint-Anwendungen und andere Funktionen konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren von SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx). So greifen Sie auf die SharePoint-Website für die Zentraladministration:

1.	Klicken Sie im Azure-Vorschauportal auf **Durchsuchen** und dann auf **Ressourcengruppen**. 
2.	Klicken Sie in der Liste der Ressourcengruppen auf den Namen Ihrer SharePoint-Farmressourcengruppe.
3.	Klicken Sie im Bereich für Ihre SharePoint-Farmressourcengruppe auf **Bereitstellungsverlauf**. 
4.	Klicken Sie im Bereich **Bereitstellungsverlauf** auf **Microsoft.SharePointFarm**.
5.	Wählen Sie im Bereich **Microsoft.SharePointFarm** die URL im Feld „SHAREPOINTCENTRALADMINURL“ aus, und kopieren Sie sie. 
6.	Fügen Sie in Ihrem Internetbrowser die URL in das Adressenfeld ein.
7.	Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen ein, die Sie beim Erstellen der Farm angegeben haben.


Hinweise:

- Das Azure-Vorschauportal erstellt diese virtuellen Computer innerhalb Ihres Abonnements.
- Das Azure-Vorschauportal erstellt beide Farmen in einem virtuellen Cloudnetzwerk mit einer über das Internet zugängigen Webpräsenz. Es gibt keine Standort-zu-Standort-VPN- oder ExpressRoute-Verbindung mit Ihrem Unternehmensnetzwerk. 
- Sie können diese Server über Remotedesktopverbindungen verwalten. Weitere Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird](virtual-machines-log-on-windows-server.md).


## Azure Resource Manager

Die SharePoint-Serverfarm verwendet den Azure Resource Manager und Skripte, um die Infrastruktur und die Serverkonfigurationen für diese SharePoint-Farmen automatisch zu erstellen. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit Resource Manager](powershell-azure-resource-manager.md).

## Zusätzliche Ressourcen

[SharePoint-Serverfarm-Konfigurationsdetails](virtual-machines-sharepoint-farm-config-azure-preview.md)

[SharePoint in Azure-Infrastrukturdiensten](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[Einrichten einer SharePoint-Intranetfarm in einer Hybrid Cloud zu Testzwecken](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen](virtual-machines-sharepoint-infrastructure-services.md)

<!---HONumber=58-->