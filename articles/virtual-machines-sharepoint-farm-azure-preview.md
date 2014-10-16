<properties title="SharePoint Server Farm" pageTitle="SharePoint Server Farm" description="Describes the new SharePoint Server Farm feature available in the Azure Preview Portal" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="josephd"></tags>

# SharePoint-Serverfarm

Mit der SharePoint-Serverfarm erstellt das Microsoft Azure-Vorschauportal eine vorkonfigurierte SharePoint Server 2013-Farm für Sie. Damit können Sie viel Zeit sparen, wenn Sie eine grundlegende oder hochverfügbare SharePoint-Farm für eine Test- oder Entwicklungsumgebung benötigen oder wenn Sie SharePoint Server 2013 als Zusammenarbeitslösung für Ihre Organisation bewerten möchten.

Die grundlegende SharePoint-Farm besteht aus drei virtuellen Computern mit der folgenden Konfiguration:

![SharePoint-Farm][]

Sie können diese Farmkonfiguration für eine vereinfachte Einrichtung für die SharePoint-App-Entwicklung oder für eine erste Bewertung von SharePoint 2013 verwenden.

Die hochverfügbare SharePoint-Farm besteht aus neun virtuellen Computern mit der folgenden Konfiguration:

![SharePoint-Farm][1]

Sie können diese Farmkonfiguration verwenden, um höhere Clientlasten, Hochverfügbarkeit eines externen SharePoint-Standorts sowie SQL Server AlwaysOn für eine SharePoint-Farm zu testen. Außerdem können Sie diese Konfiguration für die SharePoint-App-Entwicklung in einer Hochverfügbarkeitsumgebung nutzen.

Konfigurationsdetails zu diesen beiden Farmen finden Sie unter [Konfigurationsdetails zur SharePoint-Serverfarm][].

Diese Farmen haben einen vorkonfigurierten Endpunkt, der authentifizierten Webdatenverkehr (TCP-Port 80) zum SharePoint-Webserver über einen mit dem Internet verbundenen Clientcomputer ermöglicht. Dieser Endpunkt gehört zu einer vorkonfigurierten Teamwebsite. Zum Zugriff auf diese Teamwebsite benötigen Sie zuerst den Domänennamen des Webservers der SharePoint-Farm.

1.  Klicken Sie im Azure-Vorschauportal auf **Durchsuchen** und dann auf **Virtuelle Computer**.
2.  Klicken Sie in der Liste der virtuellen Computer auf den Namen Ihres Webservers (endet auf SP, WEB1 oder WEB2).
3.  Klicken Sie im Bereich Ihres virtuellen Webservercomputers auf **Eigenschaften**.
4.  Beachten Sie die Angabe FQDN in **Domänenname**.
5.  Rufen Sie die URL **[http://[FQDN]][]** über Ihren Internetbrowser auf. Die URL für den Domänennamen "spfarm1-web1-contoso.cloudpapp.net" lautet beispielsweise **<http://spfarm1-web1-contoso.cloudpapp.net>**.
6.  Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen ein, die Sie beim Erstellen der Farm angegeben haben.

Auf der SharePoint-Website für die Zentraladministration können Sie eigene Websites, SharePoint-Anwendungen und andere Funktionen konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren von SharePoint 2013][]. Zum Zugriff auf die SharePoint-Website "Zentraladministration" benötigen Sie zuerst die externe Portnummer.

1.  Klicken Sie im Azure-Vorschauportal auf **Durchsuchen** und dann auf **Virtuelle Computer**.
2.  Klicken Sie in der Liste der virtuellen Computer auf den Namen des SharePoint-Servers für die grundlegende Konfiguration (endet auf SP) oder den Namen des Anwendungsservers für die Hochverfügbarkeitskonfiguration (endet auf APP1 oder APP2).
3.  Klicken Sie im Bereich des virtuellen Computers auf **Eigenschaften**.
4.  Beachten Sie die Angabe FQDN in **Domänenname**.
5.  Schließen Sie das Fenster **Eigenschaften**.
6.  Blättern Sie im Bereich des virtuellen Computers bis zur Liste **Endpunkte**.
7.  Notieren Sie sich die Portnummer für den Endpunkt namens SPCentralAdmin.
8.  Rufen Sie die URL **[http://[FQDN]:[Portnummer]** über Ihren Internetbrowser auf. Die URL für den Domänennamen "spfarm1-app1-contoso.cloudpapp.net" mit der externen Portnummer 54398 lautet beispielsweise **<http://spfarm1-app1-contoso.cloudpapp.net:54398>**.
9.  Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen ein, die Sie beim Erstellen der Farm angegeben haben.

Hinweise:

-   Das Azure-Vorschauportal erstellt diese virtuellen Computer innerhalb Ihres Abonnements.
-   Das Azure-Vorschauportal erstellt beide Farmen in einem virtuellen Cloudnetzwerk mit einer über das Internet zugängigen Webpräsenz. Es gibt keine Standort-zu-Standort-VPN-Verbindung mit Ihrem Unternehmensnetzwerk.
-   Sie können diese Server über Remotedesktopverbindungen verwalten.

## Schrittweise Anleitung zur Konfiguration

Gehen Sie wie folgt vor, um Ihre SharePoint-Farm mit SharePoint Farm zu erstellen:

1.  Klicken Sie im [Microsoft Azure-Vorschauportal][] auf **Virtuelle Computer** \> **SharePoint-Serverfarm**.
2.  Geben Sie im Bereich **SharePoint-Farm erstellen** den Namen einer Ressourcengruppe ein.
3.  Geben Sie auf jedem virtuellen Computer in der Farm einen Benutzernamen und das Kennwort für ein lokales Administratorkonto ein. Wählen Sie einen Namen und ein schwer zu erratendes Kennwort.
4.  Falls Sie eine Hochverfügbarkeitsfarm wünschen, klicken Sie auf **Hohe Verfügbarkeit aktivieren**.
5.  Klicken Sie zur Konfiguration des Domänencontrollers auf den Pfeil. Sie können ein Hostnamenpräfix (standardmäßig der Ressourcengruppenname), einen Gesamtstruktur-Stammdomänennamen (standardmäßig contoso.com) und die Größe des Domänencontrollers (standardmäßig A1) angeben.
6.  Klicken Sie zur Konfiguration des SQL-Servers auf den Pfeil. Sie können ein Hostnamenpräfix (standardmäßig der Ressourcengruppenname), die Größe des SQL-Servers (standardmäßig A5), Name und Kennwort des Datenbankzugriffskontos (standardmäßig das Administratorkonto), einen SQL-Server-Dienstkontonamen (standardmäßig sqlservice) sowie das Kennwort (standardmäßig dasselbe wie im Administratorkonto) angeben.
7.  Klicken Sie zur Konfiguration des SharePoint-Servers auf den Pfeil. Sie können ein Hostnamenpräfix (standardmäßig der Ressourcengruppenname), die Größe des SharePoint-Servers (standardmäßig A23), Name und Kennwort des SharePoint-Benutzerkontos (Standardname ist sp\_setup), Name und Kennwort des SharePoint-Farmkontos (Standardname ist sp\_farm) sowie die Passphrase der SharePoint-Farm angeben. Standardmäßig wird das Administratorkennwort für das Benutzerkonto, das Farmkonto und die Passphrase von SharePoint verwendet.
8.  Klicken Sie zur Konfiguration der optionalen Konfiguration (virtuelles Netzwerk oder Speicherkonto-Diagnose) auf den Pfeil.
9.  Klicken Sie auf den Pfeil, um das Abonnement anzugeben.
10. Klicken Sie auf den Pfeil, um den Standort (Region) anzugeben.
11. Klicken Sie auf **Erstellen**, wenn Sie fertig sind.

## Azure Resource Manager

Die SharePoint-Serverfarm verwendet den Azure Resource Manager und Skripte, um die Infrastruktur und die Serverkonfigurationen für diese SharePoint-Farmen automatisch zu erstellen. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit Resource Manager][].

  [SharePoint-Farm]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png
  [1]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png
  [Konfigurationsdetails zur SharePoint-Serverfarm]: ../virtual-machines-sharepoint-farm-config-azure-preview/
  
  [Konfigurieren von SharePoint 2013]: http://technet.microsoft.com/library/ee836142.aspx
  [Microsoft Azure-Vorschauportal]: https://portal.azure.com/
  [Verwenden von Windows PowerShell mit Resource Manager]: http://azure.microsoft.com/de-de/documentation/articles/powershell-azure-resource-manager/
