<properties 
	pageTitle="Sicheres Verbinden mit Back-End-Ressourcen von einer App Service-Umgebung aus" 
	description="Erfahren Sie, wie Sie von einer App Service-Umgebung aus eine sichere Verbindung mit Back-End-Ressourcen herstellen." 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="stefsh"/>

# Sicheres Verbinden mit Back-End-Ressourcen von einer App Service-Umgebung aus #

## Übersicht ##
Da eine App Service-Umgebung immer in einem Subnetz eines regionalen [virtuellen Netzwerks][virtualnetwork] erstellt wird, können aus einer App Service-Umgebung ausgehende Verbindungen zu anderen Back-End-Ressourcen ausschließlich über das virtuelle Netzwerk übertragen werden.

Beispielsweise kann ein SQL Server auf einem Cluster virtueller Computer ausgeführt werden, wenn Port 1433 gesperrt ist. Der Endpunkt kann durch eine ACL geschützt werden, um nur den Zugriff von anderen Ressourcen im selben virtuellen Netzwerk aus zuzulassen.

Als weiteres Beispiel können vertrauliche Endpunkte lokal ausgeführt werden und mit Azure entweder über [Site-to-Site][SiteToSite]- oder [Azure ExpressRoute][ExpressRoute]-Verbindungen verbunden sein. Daher können nur Ressourcen in virtuellen Netzwerken, die mit den Site-to-Site- oder ExpressRoute-Tunneln verbunden sind, auf lokale Standorte zugreifen.

In all diesen Szenarien können Apps, die in einer App Service-Umgebung ausgeführt werden, sich sicher mit den verschiedenen Servern und Ressourcen verbinden. Ausgehender Datenverkehr von in einer App Service-Umgebung ausgeführten Apps zu privaten Endpunkte in demselben virtuellen Netzwerk (oder die mit demselben virtuellen Netzwerk verbunden sind) wird nur über das virtuelle Netzwerk geleitet. Ausgehender Datenverkehr an private Endpunkte verläuft nicht über das öffentliche Internet.

## Ausgehende Verbindungen und DNS-Anforderungen ##
Damit eine App-Service-Umgebung ordnungsgemäß funktioniert, ist ein ausgehender Zugriff auf Azure Storage sowie eine SQL-Datenbank in derselben Azure-Region erforderlich. Wenn der ausgehende Internetzugriff im virtuellen Netzwerk blockiert ist, können App Service-Umgebungen nicht auf diese Azure-Endpunkte zugreifen.

Kunden können auch über benutzerdefinierte DNS-Server verfügen, die im virtuellen Netzwerk konfiguriert sind. App Service-Umgebungen müssen Azure-Endpunkte unter *.database.windows.net, *.file.core.windows.net und *.blob.core.windows.net auflösen können.

Es empfiehlt sich auch, benutzerdefinierte DNS-Server im virtuellen Netzwerk vor dem Erstellen einer App-Service-Umgebung einzurichten. Wenn die DNS-Konfiguration eines virtuellen Netzwerks geändert wird, während eine App Service-Umgebung erstellt wird, misslingt das Erstellen der App Service-Umgebung.

## Verbinden mit einem SQL Server
Eine gängige SQL Server-Konfiguration verfügt über einen Endpunkt, der an Port 1433 lauscht:

![SQL Server-Endpunkt][SqlServerEndpoint]

Es gibt zwei Ansätze zum Einschränken des Datenverkehrs zu diesem Endpunkt:


- [Netzwerk-Zugriffssteuerungslisten][NetworkAccessControlLists] (Netzwerk-ACLs)

- [Netzwerksicherheitsgruppen][NetworkSecurityGroups]


## Einschränken des Zugriffs mit einer Netzwerk-ACL

Port 1433 kann über eine Netzwerk-Zugriffssteuerungsliste geschützt werden. Im Beispiel unten werden Clientadressen innerhalb des virtuellen Netzwerks auf eine weiße Liste gesetzt und der Zugriff auf alle anderen Clients blockiert.

![Beispiel zu Netzwerk-Zugriffssteuerungslisten][NetworkAccessControlListExample]

Alle Anwendungen, die in der App Service-Umgebung in demselben virtuellen Netzwerk wie SQL Server ausgeführt werden, können über die **VNet-interne** IP-Adresse für den virtuellen SQL Server-Computer eine Verbindung mit der SQL Server-Instanz herstellen.

Die unten aufgeführte Beispiel-Verbindungszeichenfolge verweist auf den SQL Server über dessen private IP-Adresse.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Obwohl der virtuelle Computer auch über einen öffentlichen Endpunkt verfügt, werden Verbindungsversuche anhand der öffentlichen IP-Adresse aufgrund der Netzwerk-ACL zurückgewiesen.

## Einschränken des Zugriffs mit einer Netzwerksicherheitsgruppe
Als alternative Methode zum Sichern des Zugriffs kann eine Netzwerksicherheitsgruppe verwendet werden. Netzwerksicherheitsgruppen können auf einzelne virtuelle Computer oder auf ein Subnetz mit virtuellen Computern angewendet werden.

Zuerst muss eine Netzwerksicherheitsgruppe erstellt werden:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Das Beschränken des Zugriffs auf den VNet-internen Datenverkehr ist mit einer Netzwerksicherheitsgruppe sehr einfach. Die Standardregeln in einer Netzwerksicherheitsgruppe ermöglichen nur den Zugriff über andere Netzwerkclients in demselben virtuellen Netzwerk.

Demzufolge muss zum Sperren des Zugriffs auf SQL Server einfach nur eine Netzwerksicherheitsgruppe mit ihren Standardregeln entweder auf die virtuellen Computer mit SQL Server oder auf das Subnetz mit den virtuellen Computern angewendet werden.

Im folgenden Beispiel wird eine Netzwerksicherheitsgruppe auf das enthaltende Subnetz angewendet:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
Das Endergebnis ist ein Satz von Sicherheitsregeln, die den externen Zugriff blockieren, während der VNet-interne Zugriff zugelassen wird:

![Standard-Netzwerksicherheitsgruppen][DefaultNetworkSecurityRules]


## Erste Schritte

Informationen zum Einstieg in App Service-Umgebungen finden Sie unter [Einführung in die App Service-Umgebung][IntroToAppServiceEnvironment]

Details zum Steuern des eingehenden Datenverkehrs in Ihre App Service-Umgebung finden Sie unter [Steuern von eingehendem Datenverkehr in eine App Service-Umgebung][ControlInboundASE]

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[ControlInboundTraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://msdn.microsoft.com/library/azure/dn133795.aspx
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://msdn.microsoft.com/library/azure/dn376541.aspx
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ControlInboundASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png

<!---HONumber=July15_HO3-->