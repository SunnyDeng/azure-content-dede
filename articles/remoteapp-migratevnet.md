<properties 
    pageTitle="So migrieren Sie von einem RemoteApp-VNET auf ein Azure-VNET"
    description="Erfahren Sie, wie Sie von einem RemoteApp-VNET auf ein Azure-VNET migrieren" 
    services="remoteapp" 
    solutions="" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/23/2015" 
    ms.author="elizapo" />



# So migrieren Sie eine Hybridsammlung von einem RemoteApp-VNET auf ein Azure-VNET

Gute Nachrichten! Sie können nun hybride RemoteApp-Sammlungen direkt in Ihre vorhandenen virtuellen Azure-Netzwerke \(VNETs\) einstellen, anstatt RemoteApp-spezifische VNETs zu erstellen. Der Vorteil hierbei ist, dass Sie die neuesten VNET-Funktionen \(wie ExpressRoute\) nutzen können und Ihre Hybridsammlungen direkten Netzwerkzugriff auf andere Azure-Dienste und virtuelle Computer bekommen, die auf diesem VNET bereitgestellt werden. \(Dadurch erhalten Sie eine bessere Leistung und eine einfachere Einrichtung als bei VNET-zu-VNET-Konfigurationen\).


Nehmen wir an, dass Sie bereits eine hybride RemoteApp-Sammlung namens *OriginalCollection* mit einem RemoteApp-VNET namens *RemoteAppVNET* erstellt haben. Dies sind die Schritte zum Migrieren auf ein neues Azure-VNET namens *AzureVNET*.

1.	Erstellen Sie in der Registerkarte **Netzwerke** im [Verwaltungsportal](http://manage.windowsazure.com/) ein VNET namens *AzureVNET*, mit dem gleichen Speicherort, der gleichen DNS-Konfiguration und dem gleichen Adressraum \(für mindestens eines der *AzureVNET*-Subnetze\) wie für *RemoteAppVNET*.
2.	Konfigurieren Sie *AzureVNET* so, dass es die Active Directory-Bereitstellung entweder hostet oder eine Netzwerkverbindung dazu besteht, mit der *OriginalCollection* über die Domäne verbunden ist.
3.	Erstellen Sie in der Registerkarte **RemoteApps** eine neue RemoteApp-Sammlung namens *NewCollection*. \(Verwenden Sie die Option **Mit VPN erstellen** und nicht **Schnellerfassung**.\)
3.	Konfigurieren Sie die Bereitstellung von *NewCollection* mit einem Subnetz in *AzureVNET*.
4.	Konfigurieren Sie *NewCollection* so, dass die gleichen Image-  und Domänenverbindungsinformationen wie für *OriginalCollection* verwendet werden.
5.	Nach ein paar Stunden wird *NewCollection* in Ihrer Sammlungsliste mit dem Zustand "aktiv" angezeigt.

Wenn Sie keine Benutzerinformationen von der ursprünglichen Sammlung in die neue Sammlung migrieren müssen, befolgen Sie als Nächstes die folgenden Schritte:

6.	Löschen Sie *OriginalCollection*.
7.	Löschen Sie *RemoteAppVNET*.

Und Sie haben es geschafft!

Wenn Sie jedoch Benutzerinformationen aus der ursprünglichen Sammlung in die neue Sammlung migrieren möchten, führen Sie diese Schritte als Nächstes aus:

6.	Senden Sie eine E-Mail an [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration) mit Ihrer Azure-Abonnement-ID, dem Namen Ihrer ursprünglichen Sammlung und dem Namen Ihrer neuen Sammlung. Bitten Sie um die Migration Ihrer Benutzerinformationen.
7.	Innerhalb von 2 Arbeitstagen wird das RemoteApp-Team die Benutzerzugriffsliste und alle Benutzerdokumente und -einstellungen aus der ursprünglichen Sammlung in die neue Sammlung verschieben.
8.	Löschen Sie *OriginalCollection*.
9.	Löschen Sie *RemoteAppVNET*.

Und jetzt haben Sie es geschafft!

Wenn Sie Fragen haben oder besondere Unterstützung benötigen, senden Sie eine E-Mail an [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help).

<!--HONumber=52-->
