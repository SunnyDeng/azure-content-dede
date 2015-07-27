<properties 
	pageTitle="Steuern des Azure-Web-App-Verkehrs mit Azure Traffic Manager" 
	description="Dieser Artikel bietet zusammenfassende Informationen zu Azure Traffic Manager im Hinblick auf Azure-Web-Apps." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="cephalin"/>

# Steuern des Azure-Web-App-Verkehrs mit Azure Traffic Manager

> [AZURE.NOTE]Dieser Artikel bietet zusammenfassende Informationen zu Microsoft Azure Traffic Manager im Hinblick auf Azure Web Service-Web-Apps. Weitere Informationen über Azure Traffic Manager selbst finden Sie unter den Links am Ende dieses Artikels.

## Einführung
Mit Azure Traffic Manager können Sie steuern, wie Anforderungen von Webclients auf Web-Apps in Azure Web Service verteilt werden. Wenn einem Azure Traffic Manager-Profil Azure-Web-App-Endpunkte hinzugefügt werden, verfolgt Azure Traffic Manager den Status Ihrer Web-Apps (aktiv, angehalten oder gelöscht), sodass der gewünschte Endpunkt als Empfänger des Verkehrs gewählt werden kann.

## Lastenausgleichsmethoden
Azure Traffic Manager verwendet drei verschiedene Lastenausgleichsmethoden. Diese werden in der folgenden Liste beschrieben, da sie Azure-Web-Apps betreffen.

* **Failover**: Wenn Sie in verschiedenen Regionen über Web-App-Klone verfügen, können Sie mit dieser Methode eine Web-App konfigurieren, um den gesamten Webclient-Verkehr zu bedienen. Dann können Sie eine andere Web-App in einer anderen Region konfigurieren, um den Verkehr zu verarbeiten, falls die erste Web-App nicht verfügbar sein sollte. 
	
* **RoundRobin**Wenn Sie über Web-App-Klone in verschiedenen Regionen verfügen, können Sie mit dieser Methode den Verkehr gleichmäßig auf die Web-Apps in unterschiedlichen Regionen verteilen.
	
* **Leistung**:Mithilfe der Leistungsmethode wird der Verkehr anhand der kürzesten Roundtripzeit an Clients verteilt. Die Leistungsmethode kann für Web-Apps innerhalb derselben Region oder in verschiedenen Regionen verwendet werden.

Detaillierte Informationen zum Lastenausgleich in Azure Traffic Manager finden Sie unter [Traffic Manager-Lastenausgleichsmethoden](../traffic-manager/traffic-manager-load-balancing-methods.md).

##Web-Apps und Traffic Manager-Profile 
Für die Konfiguration zur Steuerung des Web-App-Verkehrs erstellen Sie ein Profil in Azure Traffic Manager, das eine der drei zuvor beschriebenen Lastenausgleichsmethoden verwendet. Fügen Sie dann dem Profil die Endpunkte (in diesem Fall Web-Apps) hinzu, für die Sie den Verkehr steuern möchten. Der Web-App-Status (aktiv, angehalten oder gelöscht) wird regelmäßig an das Profil übermittelt, sodass Azure Traffic Manager den Verkehr entsprechend leiten kann.

Beachten Sie die folgenden Aspekte, wenn Sie Azure Traffic Manager mit Azure verwenden:

* Bei reinen Web-App-Bereitstellungen innerhalb derselben Region bieten Web-Apps bereits eine Failover- und RoundRobin-Funktion, unabhängig vom Web-App-Modus.

* Bei Bereitstellungen in derselben Region, die Azure-Web-Apps zusammen mit anderen Azure-Cloud-Diensten verwenden, können Sie beide Endpunkttypen kombinieren, um Hybridszenarios zu ermöglichen.

* Sie können in einem Profil nur einen Web-App-Endpunkt pro Region angeben. Wenn Sie eine Web-App als Endpunkt für eine Region auswählen, stehen die verbleibenden Websites in dieser Region nicht mehr für dieses Profil zur Auswahl zur Verfügung.

* Die Web-App-Endpunkte, die Sie in einem Azure Traffic Manager-Profil festlegen, werden im Abschnitt **Domänennamen** auf der Konfigurationsseite für die Web-App im Profil angezeigt, dort jedoch nicht konfiguriert.

* Nachdem Sie einem Profil eine Web-App hinzugefügt haben, wird in der **Site-URL** im Dashboard der Portalseite der Web-App die benutzerdefinierte Domänen-URL der Web-App angezeigt, wenn Sie diese eingerichtet haben. Anderenfalls wird die URL des Traffic Manager-Profils angezeigt (z. B. `contoso.trafficmgr.com`). Sowohl der direkte Domänenname der Web-App als auch die Traffic Manager-URL werden auf der Konfigurationsseite der Web-App im Abschnitt **Domänennamen** angezeigt.

* Ihre benutzerdefinierten Domänennamen funktionieren wie erwartet. Sie fügen sie Ihren Web-Apps hinzu, müssen jedoch auch die DNS-Zuordnung konfigurieren, um auf die Traffic Manager-URL zu verweisen. Informationen zum Einrichten einer benutzerdefinierten Domäne für eine Azure-Web-App finden Sie unter [Konfigurieren eines benutzerdefinierten Domänen-namens für eine Azure-Website](web-sites-custom-domain-name.md).

* Sie können einem Azure Traffic Manager-Profil nur Web-Apps hinzufügen, die sich im Standardmodus befinden.

## Nächste Schritte

Einen Überblick über die Konzepte und technischen Aspekte von Azure Traffic Manager finden Sie unter [Traffic Manager-Übersicht](../traffic-manager/traffic-manager-overview.md).

Informationen zum Konfigurieren von Azure Traffic Manager, einschließlich der Anwendung auf Web-Apps, finden Sie unter [Traffic Manager-Konfigurationsaufgaben](http://msdn.microsoft.com/library/windowsazure/hh744830.aspx).

Detaillierte Informationen zum Lastenausgleich in Azure Traffic Manager finden Sie unter [Traffic Manager-Lastenausgleichsmethoden](../traffic-manager/traffic-manager-load-balancing-methods.md).

Weitere Informationen zur Verwendung von Traffic Manager mit Web-Apps finden Sie in den Blogbeiträgen [Verwenden von Azure Traffic Manager mit Azure-Websites](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) und [(Azure Traffic Manager kann jetzt in Azure-Websites integriert werden](http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).
 

<!---HONumber=July15_HO3-->