<properties pageTitle="Konfigurieren eines benutzerdefinierten Domänennamens für eine Web-App in Azure App Services, der Traffic Manager verwendet."wpickett"Verwenden Sie einen benutzerdefinierten Domänennamen für eine Web-App in Azure App Services mit Traffic Manager für den Lastenausgleich." description="Verwenden Sie einen benutzerdefinierten Domänennamen für eine Web-App in Azure App Services mit Traffic Manager für den Lastenausgleich." services="app-service\\web" documentationCenter="" authors="rmcmurray" manager="wpickett" editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="robmcm"/>

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Web-App in Azure App Services, der Traffic Manager verwendet.

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [Einführung](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel enthält allgemeine Anweisungen zur Verwendung eines benutzerdefinierten Domänennamens mit Azure App Services unter Verwendung von Traffic Manager für den Lastenausgleich.

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## Interpretation von DNS-Datensätzen

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## Konfigurieren Ihrer Web-Apps für den Modus "Standard"

[AZURE.INCLUDE [Modi](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne

> [AZURE.NOTE]Wenn Sie eine Domäne über Azure App Service-Web-Apps erworben haben, überspringen Sie die folgenden Schritte, und lesen Sie den letzten Schritt des Artikels zum [Erwerb einer Domäne für Web-Apps](custom-dns-web-site-buydomains-web-app.md).

Um Ihrer benutzerdefinierten Domäne eine Web-App in Azure App Services zuzuweisen, müssen Sie in der DNS-Tabelle für Ihre benutzerdefinierte Domäne mithilfe der von der Domänenregistrierungsstelle bereitgestellten Tools einen neuen Eintrag hinzufügen. Gehen Sie wie folgt vor, um die DNS-Tools zu suchen und zu verwenden:

1. Melden Sie sich bei Ihrem Konto mit der Domänenregistrierungsstelle an und suchen Sie nach der Seite, auf der DNS-Datensätze verwaltet werden. Suchen Sie nach Links oder Bereichen der Site, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden. Häufig finden Sie in Ihren Kontoinformationen einen Link auf diese Seite. Suchen Sie anschließend nach einem Link, der Sie zu **My domains** (Meine Domänen) führt.

4. Sobald Sie die Verwaltungsseite für Ihren Domänennamen gefunden haben, suchen Sie nach einem Link, über den Sie die DNS-Datensätze bearbeiten können. Dieser Konfigurationslink kann als **Zone file** (Zonendatei), **DNS Records** (DNS-Datensatz) oder als **Advanced** (Erweitert) bezeichnet werden.

	* Auf der Seite wurden wahrscheinlich bereits Datensätze wie ein Eintrag, der "**@**" oder '*' mit einer Domain-Parking-Seite in Verbindung setzt. Möglicherweise sind auch bereits Datensätze wie übliche Unterdomänen wie **www** darauf gespeichert.
	* Auf der Seite werden **CNAME-Datensätze** angegeben oder Sie finden ein Dropdownmenü, aus dem Sie die Art des Datensatzes auswählen können. Möglicherweise werden auch andere Datensätze wie **A-Datensätze** und **MX-Datensätze** aufgeführt. In manchen Fällen werden CNAME-Datensätze anderweitig bezeichnet, z. B. als **Alias-Datensatz**.
	* Auf der Seite werden außerdem Felder angezeigt, über die Sie über einen **Hostnamen** oder **Domainnamen** eine **Zuweisung** zu einem anderen Domainnamen vornehmen können.

5. Jede Registrierungsstelle ist spezifisch, generell weisen Sie jedoch *von* Ihrem benutzerdefinierten Domänennamen (z. B. **contoso.com**) *zum* Traffic Manager-Domänennamen (**contoso.trafficmanager.net**) zu, der für Ihre Web-App verwendet wird.

6. Speichern Sie die Änderungen, sobald Sie die DNS-Datensätze in Ihrer Registrierung hinzugefügt oder geändert haben.

<a name="enabledomain"></a>
## Aktivieren des Traffic Manager

[AZURE.INCLUDE [Modi](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 

<!---HONumber=AcomDC_0114_2016-->