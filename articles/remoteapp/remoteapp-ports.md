
<properties
    pageTitle="Liste mit Ports und URLs, die für Azure RemoteApp im Virtual Network von Kunden auf die Positivliste gesetzt werden | Microsoft Azure"
    description="Erfahren Sie, welche Ports und URLs Sie für die Kommunikation über Azure RemoteApp konfigurieren müssen."
    services="remoteapp"
	documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/22/2016"
    ms.author="elizapo" />



# Liste mit Ports und URLs zum Gewähren des Zugriffs für Azure RemoteApp im Virtual Network des Kunden 

Folgendes gilt für Azure RemoteApp bei einer Cloud- oder Hybridsammlung, wenn Sie die Anwendung in einem Virtual Network (VNET) bereitstellen. Weitere Informationen zu Virtual Networks finden Sie unter [Übersicht über virtuelle Netzwerke](virtual-networks-overview.md). Angenommen, Sie haben eine Netzwerksicherheitsgruppe (NSG) erstellt, bei der der Datenverkehr auf die von Ihnen für Azure RemoteApp gewählten Ressourcen des Virtual Network eingeschränkt wird. Stellen Sie in diesem Fall sicher, dass folgende Elemente erreichbar sind und von den Sicherheitsrichtlinien im Virtual Network zugelassen werden. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Was ist eine Netzwerksicherheitsgruppe (NSG)?](virtual-networks-nsg.md)

##  Für das Azure RemoteApp-Subnetz ist der Zugriff auf diese Endpunkte und URLs erforderlich: 
*	**.servicebus.windows.net
*	 **.servicebus.net
*	 https://*.remoteapp.windwsazure.com  
*	 https://www.remoteapp.windowsazure.com 
*	 https://*remoteapp.windowsazure.com  
*	 https://*.core.windows.net  
*	 Ausgehend: TCP: 443, TCP: 10101 - 10175 
*	 Optional – UDP: 10201-10275  
 
## Azure RemoteApp-Clients benötigen Zugriff auf diese Endpunkte und URLs: 

Mit Clients sind die Desktops, Geräte usw. gemeint, mit denen Benutzer eine Verbindung mit den Apps herstellen, die in der Azure RemoteApp-Sammlung bereitgestellt werden.

-  https://telemetry.remoteapp.windowsazure.com  
-  https://**.remoteapp.windowsazure.com (optionale UDP-Ports gelten für diese Adresse) 
-  https://login.windows.net  
-  https://login.microsoftonline.com  
-  https://www.remoteapp.windowsazure.com 
-  https://**.remoteapp.windowsazure.com  
-  https://*.core.windows.net  
-  Ausgehend: TCP: 443  
-  Optional – UDP: 3391 

<!---HONumber=AcomDC_0128_2016-->