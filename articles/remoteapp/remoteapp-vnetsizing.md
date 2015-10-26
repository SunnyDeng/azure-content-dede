
<properties 
    pageTitle="Größeninformationen für ein VNET in Azure RemoteApp"
    description="Erfahren Sie mehr über die Anforderungen für IP-Adressen für ein VNET mit Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/02/2015" 
    ms.author="elizapo" />



# Größeninformationen für ein VNET in Azure RemoteApp

Wenn Sie Azure RemoteApp mit einem virtuellen Netzwerk (VNET) verwenden, werden von RemoteApp IP-Adressen innerhalb des Subnetzes verwendet. Je nach Größe Ihres RemoteApp-Diensts müssen Sie sicherstellen, dass Ihr Subnetz über genügend IP-Adressen für die virtuellen Computer der RemoteApp verfügt. Die Größeninformationen können nicht ganz präzise sein, da die virtuellen Computer in einer Sammlung von der RemoteApp dynamisch angepasst werden. Die Informationen dienen daher nur zur Einschätzung Ihres Subnetzbereichs. Dies ist besonders wichtig, da nach dem Platzieren eines RemoteApp-Diensts in einem VNET keine Erhöhung der Subnetzgröße möglich ist, ohne die RemoteApp zu entfernen.

Sie sollten für jede RemoteApp-Sammlung, die bei maximaler Kapazität ausgeführt werden soll, 100 IP-Adressen vorhalten. Wenn Sie beispielsweise RemoteApp-Sammlung im Standardplan haben und das Maximum von 500 Benutzern ausschöpfen möchten, müssen Sie für diese Sammlung 100 IP-Adressen vorhalten. Analog benötigen Sie für eine RemoteApp-Sammlung im Basisplan mit 800 Benutzern 100 IP-Adressen. Wenn Sie mit weniger Benutzern (unterhalb des Maximums) planen, können Sie die pro Sammlung erforderlichen IP-Adressen reduzieren. Die Mindestgrößenanforderung des Subnetzes liegt bei 30 Adressen (/27).

Sehen Sie sich die folgenden Informationen an, um sicherzustellen, dass Ihr VNET ist die konfiguriert ist und ordnungsgemäß funktioniert:

- [Migrieren Sie von einem persönlichen VNET zu einem Azure-VNET](remoteapp-migratevnet.md)
- [Überprüfen des Azure-VNET für die Verwendung mit Azure RemoteApp](remoteapp-vnet.md)
  
 

<!---HONumber=Oct15_HO3-->