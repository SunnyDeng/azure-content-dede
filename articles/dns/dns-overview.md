<properties 
   pageTitle="Übersicht über Azure DNS | Microsoft Azure" 
   description="Übersicht über die Azure DNS-Hostingdienste in Microsoft Azure und Hosten der Domäne in Microsoft Azure" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/09/2016"
   ms.author="joaoma"/>

# Azure DNS – Übersicht

Hinter jeder Website und jedem Dienst im Internet steht eine IP-Adresse. www.microsoft.com verwendet beispielsweise die IP-Adresse 134.170.185.46. Das Domain Name System, oder DNS, ist für die Übersetzung (oder Auflösung) des Website- oder Dienstnamens in die IP-Adresse verantwortlich.

Azure DNS ist ein Hostingdienst für DNS-Domänen, der die Namensauflösung mithilfe der Microsoft Azure-Infrastruktur durchführt. Durch das Hosten Ihrer Domänen in Azure können Sie Ihre DNS-Einträge mithilfe der gleichen Anmeldeinformationen, APIs, Tools und Abrechnung wie für die anderen Azure-Dienste verwalten.

DNS-Domänen in Azure DNS werden im globalen Azure-Netzwerk von DNS-Servern gehostet. Wir verwenden Anycast-Netzwerke, sodass jede DNS-Abfrage vom nächsten verfügbaren DNS-Server beantwortet wird. Damit wird eine schnelle Leistung und hohe Verfügbarkeit für Ihre Domäne sichergestellt.

Der Dienst basiert auf dem Azure-Ressourcen-Manager (ARM). Daher profitiert er von ARM-Funktionen, wie z. B. die rollenbasierte Zugriffskontrolle, Überwachungsprotokolle und Ressourcensperren.

Ihren Domänen und Einträge können über das Azure-Portal, Azure PowerShell-Cmdlets und der plattformübergreifenden Azure-Befehlszeilenschnittstelle verwaltet werden. Anwendungen, die eine automatische DNS-Verwaltung erfordern, können über die REST-API und SDKs mit dem Dienst zusammenarbeiten.

Azure DNS unterstützt derzeit nicht den Kauf von Domänennamen. Um Domänen zu erwerben, sollten Sie eine Drittanbieter-Domänennamenregistrierungsstelle verwenden, die in der Regel eine kleine jährliche Gebühr erhebt. Diese Domänen können dann in Azure DNS gehostet werden, um DNS-Einträge zu verwalten. Weitere Informationen finden Sie unter [Delegieren einer Domäne an Azure DNS](dns-domain-delegation.md).


## Nächste Schritte

[Erste Schritte beim Erstellen von DNS-Zonen](dns-getstarted-create-dnszone.md)

[Automatisieren von Azure-Vorgängen mit dem .NET SDK](dns-sdk.md)

[REST-API-Referenz für Azure DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx)




 

<!---HONumber=AcomDC_0309_2016-->