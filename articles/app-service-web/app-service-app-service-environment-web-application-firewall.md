<properties 
	pageTitle="Konfigurieren einer Web Application Firewall (WAF) für eine App Service-Umgebung" 
	description="Erfahren Sie, wie eine Ihrer App Service-Umgebung vorgelagerte Web Application Firewall konfigurieren." 
	services="app-service\web" 
	documentationCenter="" 
	authors="naziml" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="naziml"/>

# Konfigurieren einer Web Application Firewall (WAF) für eine App Service-Umgebung

## Übersicht ##
Web Application Firewalls wie [Barracuda WAF for Azure](https://www.barracuda.com/programs/azure) (im [Azure Marketplace](http://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) verfügbar) dienen zum Schützen Ihrer Webanwendungen, indem eingehender Webdatenverkehr untersucht wird, um SQL-Injections, Cross-Site Scripting, das Hochladen von Schadsoftware sowie DDoS- und andere Angriffe zu blockieren. WAF überprüft auch zur Verhinderung von Datenverlust (Data Loss Prevention, DLP) die Antworten von den Back-End-Webservern. Zusammen mit der von App Service-Umgebungen bereitgestellten Isolierung und zusätzlichen Skalierung ergibt sich dadurch eine ideale Umgebung für das Hosten geschäftswichtiger Webanwendungen, die böswilligen Anforderungen abwehren und hohe Datenverkehrsvolumen bewältigen müssen.

## Einrichtung ##
Für dieses Dokument konfigurieren wir unsere App Service-Umgebung hinter mehreren Barracuda WAF-Instanzen mit Lastenausgleich, sodass nur Datenverkehr von der WAF die App Service-Umgebung erreichen kann und kein Zugriff aus der DMZ möglich ist. Außerdem haben wir Azure Traffic Manager unseren Barracuda WAF-Instanzen für den Lastenausgleich von Azure-Datencentern und Regionen vorgelagert. Ein allgemeines Diagramm der Einrichtung wird nachstehend gezeigt.

![Architektur][Architecture]

## Konfigurieren der App Service-Umgebung ##
Informationen zum Konfigurieren einer App Service-Umgebung finden Sie in [unserer Dokumentation](app-service-web-how-to-create-an-app-service-environment.md) zu diesem Thema. Sobald Sie ein App Service-Umgebung eingerichtet haben, können Sie in dieser Umgebung [Web Apps](app-service-web-overview.md), [API-Apps](app-service-api-apps-why-best-platform.md) und [mobile Apps](app-service-mobile-value-prop-preview.md) erstellen, die alle hinter der WAF geschützt werden, die wir im nächsten Abschnitt konfigurieren.

## Konfiguration des Barracuda WAF-Clouddiensts ##
Barracuda hat einen [ausführlichen Artikel](https://techlib.barracuda.com/WAF/AzureDeploy) zur Bereitstellung seiner WAF auf einem virtuellen Computer in Azure verfasst. Wir befolgen diese Anweisungen, wollen aber zugleich Redundanz und keine einzelne Fehlerquelle hinzufügen, weshalb wir mindestens 2 VMs mit WAF-Instanzen im selben Clouddienst bereitstellen.

### Hinzufügen von Endpunkten zum Clouddienst ###
Sobald Ihr Clouddienst zwei oder mehr WAF VM-Instanzen aufweist, können Sie im [Azure-Verwaltungsportal](https://portal.azure.com) HTTP- und HTTPS-Endpunkte hinzufügen, die von Ihrer Anwendung verwendet werden (siehe die folgende Abbildung).

![Endpunkt konfigurieren][ConfigureEndpoint]

Wenn Ihre Anwendungen andere Endpunkte verwenden, sollten Sie diese auch unbedingt dieser Liste hinzufügen.

### Konfigurieren von Barracuda WAF über das dazugehörige Verwaltungsportal ###
Barracuda WAF verwendet für die Konfiguration über sein Verwaltungsportal den TCP-Port 8000. Da wir mehrere Instanzen von WAF-VMs haben, müssen Sie die Schritte hier für jede VM-Instanz wiederholen.


> Hinweis: Sobald Sie die WAF-Konfiguration abgeschlossen haben, entfernen Sie den TCP/8000-Endpunkt aus allen WAF-VMs, um Ihre WAF zu schützen.

Fügen Sie, wie in der folgenden Abbildung gezeigt, den Verwaltungsendpunkt hinzu, damit Sie Ihre Barracuda WAF konfigurieren können.

![Verwaltungsendpunkt hinzufügen][AddManagementEndpoint]
 
Navigieren Sie in einem Browser zum Verwaltungsendpunkt für Ihren Clouddienst. Wenn der Clouddienst "test.cloudapp.net" heißt, greifen Sie auf diesen Endpunkt zu, indem Sie zu http://test.cloudapp.net:8000 navigieren. Es sollte, wie nachstehend gezeigt, eine Anmeldeseite angezeigt werden, auf der Sie sich mit den Anmeldeinformationen anmelden können, die Sie beim Einrichten der WAF-VM angegeben haben.

![Anmeldeseite für die Verwaltung][ManagementLoginPage]

Nach der Anmeldung sollte ein Dashboard wie in der folgenden Abbildung angezeigt werden, das grundlegende Statistiken zum WAF-Schutz enthält.

![Dashboard für die Verwaltung][ManagementDashboard]

Auf der Registerkarte "Dienste" können Sie Ihre WAF für die Dienste konfigurieren, die von ihr geschützt werden sollen. Weitere Informationen zum Konfigurieren Ihrer Barracuda WAF finden Sie in [der dazugehörigen Dokumentation](https://techlib.barracuda.com/waf/getstarted1). Im nachstehende Beispiel wurde eine Azure-Web-App zum Verarbeiten von Datenverkehr über HTTP und HTTPS konfiguriert.

![Verwaltungsdienste hinzufügen][ManagementAddServices]

> Hinweis: Je nachdem, wie Ihre Anwendungen konfiguriert sind und welche Features in Ihrer App Service-Umgebung verwendet werden, müssen Sie Datenverkehr für alle TCP-Ports außer 80 und 443 weiterleiten, wenn Sie IP-SSL für eine Webanwendung eingerichtet haben. Eine Liste der Netzwerkports, die in App Service-Umgebungen verwendet werden, finden Sie in der [Dokumentation zum Steuern des eingehenden Datenverkehrs](app-service-app-service-environment-control-inbound-traffic.md) im Abschnitt "Netzwerkports".

## Konfigurieren von Microsoft Azure Traffic Manager (optional) ##
Wenn Ihre Anwendung in mehreren Regionen verfügbar ist, sollten Sie mithilfe von [Azure Traffic Manager](traffic-manager.md) für einen Lastenausgleich sorgen. Fügen Sie hierzu im [Azure-Verwaltungsportal](https://manage.azure.com) einen Endpunkt unter Angabe des Clouddienstnamens für Ihre WAF dem Traffic Manager-Profil hinzu (siehe die folgende Abbildung).

![Traffic Manager-Endpunkt][TrafficManagerEndpoint]

Wenn Ihre Anwendung eine Authentifizierung erfordert, stellen Sie sicher, dass Sie über eine Ressource verfügen, die keine Authentifizierung bei Traffic Manager benötigt, um ein Pingsignal zum Prüfen der Verfügbarkeit der Anwendung senden zu können. Sie können die URL im [Azure-Verwaltungsportal](https://manage.azure.com), wie nachstehend gezeigt, im Abschnitt "Konfigurieren" konfigurieren.

![Traffic Manager konfigurieren][ConfigureTrafficManager]

Zum Weiterleiten der Traffic Manager-Pingsignale von Ihrer WAF zu Ihrer Anwendung müssen Sie für Ihre Barracuda WAF die Funktion "Websiteübersetzungen" so einrichten, dass Datenverkehr zu Ihrer Anwendung weitergeleitet wird (siehe das folgende Beispiel).

![Websiteübersetzungen][WebsiteTranslations]

## Schützen des Datenverkehrs zu einer App Service-Umgebung mithilfe von Netzwerkressourcengruppen##
In der [Dokumentation zum Steuern des eingehenden Datenverkehrs](app-service-app-service-environment-control-inbound-traffic.md) finden Sie ausführliche Informationen zum Einschränken des Datenverkehrs zu Ihrer App Service-Umgebung von der WAF nur durch Verwenden der VIP-Adresse Ihres Clouddiensts. Hier ist ein PowerShell-Beispielbefehl zur Durchführung dieser Aufgabe für TCP-Port 80.


    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Ersetzen Sie "SourceAddressPrefix" durch die virtuelle IP-Adresse (VIP) des Clouddiensts Ihrer WAF.

> Hinweis: Die VIP Ihres Clouddiensts ändert sich, wenn Sie diesen löschen und neu erstellen. Stellen Sie sicher, dass Sie nach diesem Schritt die IP-Adresse in der Netzwerkressourcengruppe ändern.
 
<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png

<!---HONumber=July15_HO3-->