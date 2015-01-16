<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a custom domain name" pageTitle="Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website, die Traffic Manager verwendet" metaKeywords="Microsoft Azure, Microsoft Azure-Websites, Domänenname" description=""services""web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website mit Traffic Manager

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Custom Domain</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-custom-domain-name/" title="Websites">Website</a> | <a href="/en-us/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Website mit Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel bietet generelle Anweisungen zur Verwendung eines benutzerdefinierten Domänennamens mit Azure-Websites, die Traffic Manager für den Lastenausgleich verwenden. Prüfen Sie die Registerkarten oberhalb dieses Artikels, um zu prüfen, ob Ihre Domänenregistrierung aufgeführt ist. Wenn ja, wählen Sie diese Registerkarte für die Registrierung aus.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Interpretation von DNS-Datensätzen](#understanding-records)
-   [Konfigurieren Ihrer Websites für den Modus "Standard"](#bkmk_configsharedmode)
-   [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne](#bkmk_configurecname)
-   [Aktivieren des Traffic Manager für Ihre Website](#enabledomain)

<h2><a name="understanding-records"></a>Interpretation von DNS-Datensätzen</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Konfigurieren Ihrer Websites für den Modus "Standard"</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne</h2>

Um Ihrer benutzerdefinierten Domäne eine Azure-Website zuzuweisen, müssen Sie in der DNS-Tabelle für Ihre benutzerdefinierte Domäne mithilfe der von der Domänenregistrierungsstelle bereitgestellten Tools einen neuen Eintrag hinzufügen. Gehen Sie wie folgt vor, um die DNS-Tools zu suchen und zu verwenden:

1. Melden Sie sich bei Ihrem Konto mit der Domänenregistrierungsstelle an, und suchen Sie nach der Seite, auf der DNS-Datensätze verwaltet werden. Suchen Sie nach Links oder Bereichen der Site, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden. Häufig finden Sie in Ihren Kontoinformationen einen Link auf diese Seite. Suchen Sie anschließend nach einem Link, der Sie zu **My domains** (Meine Domänen) führt.

4. Sobald Sie die Verwaltungsseite für Ihren Domänennamen gefunden haben, suchen Sie nach einem Link, über den Sie die DNS-Datensätze bearbeiten können. Dieser Konfigurationslink kann als **Zone file** (Zonendatei), **DNS Records** (DNS-Datensatz) oder als **Advanced** (Erweitert) bezeichnet werden.

	* Auf der Seite wurden wahrscheinlich bereits Datensätze erstellt, z. B. ein Eintrag, der "**@**" oder "\*" mit einer Domain-Parking-Seite in Verbindung setzt. Möglicherweise sind auch bereits Datensätze wie übliche Unterdomänen wie **www** darauf gespeichert.
	* 	Auf der Seite werden **CNAME-Datensätze** angegeben, oder Sie finden ein Dropdownmenü, aus dem Sie die Art des Datensatzes auswählen können. Möglicherweise werden auch andere Datensätze wie **A-Datensätze** und **MX-Datensätze** aufgeführt. In manchen Fällen werden CNAME-Datensätze anderweitig bezeichnet, z. B. als **Alias-Datensatz**.
	* Auf der Seite werden außerdem Felder angezeigt, über die Sie über einen **Hostnamen** oder **Domänennamen** eine **Zuweisung** zu einem anderen Domänennamen vornehmen können.

5. 	Jede Registrierungsstelle ist spezifisch, doch generell führen Sie eine Zuordnung *von* Ihrem benutzerdefinierten Domänennamen (z. B. **contoso.com**,) *zum* Traffic Manager-Domänennamen (**contoso.trafficmanager.net**) durch, der für Ihre Azure-Website verwendet wird.

6. Speichern Sie die Änderungen, sobald Sie die DNS-Datensetze in Ihrer Registrierung hinzugefügt oder geändert haben.

<h2><a name="enabledomain"></a>Aktivieren einer Traffic Manager-Website</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]
