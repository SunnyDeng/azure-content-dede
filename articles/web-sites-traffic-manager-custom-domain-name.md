<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a custom domain name" pageTitle="Configure a custom domain name for an Azure web site that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Konfiguration eines benutzerdefinierten Domänennamens für eine Azure-Website mithilfe des Traffic Manager
=========================================================================================================

[Custom Domain](/en-us/documentation/articles/web-sites-custom-domain-name "Custom Domain")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/en-us/documentation/articles/web-sites-custom-domain-name/ "Websites") | [Website mit Traffic Manager](/en-us/documentation/articles/web-sites-traffic-manager-custom-domain-name/ "Website mit Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel bietet generelle Anweisungen zur Verwendung eines benutzerdefinierten Domänennamens mit einer Azure-Website, die für den Lastenausgleich den Traffic Manager verwendet. Prüfen Sie die Registerkarten oberhalb dieses Artikels, um zu prüfen, ob Ihre Domänenregistrierung aufgeführt ist. Wenn ja, wählen Sie diese Registerkarte für die Registrierung aus.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Interpretation von DNS-Datensätzen](#understanding-records)
-   [Konfigurieren Ihrer Websites für den Standardmodus](#bkmk_configsharedmode)
-   [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne](#bkmk_configurecname)
-   [Aktivieren des Traffic Manager für Ihre Website](#enabledomain)

Interpretation von DNS-Datensätzen
----------------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

Konfigurieren Ihrer Websites für den Standardmodus
--------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

## Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne

Um Ihrer benutzerdefinierten Domäne eine Azure-Website zuzuweisen, müssen Sie in der DNS-Tabelle für Ihre benutzerdefinierte Domäne mithilfe der von der Domänenregistrierung bereitgestellten Tools einen neuen Eintrag hinzufügen. Gehen Sie wie folgt vor, um die DNS-Tools zu suchen und zu verwenden:

1.  Melden Sie sich bei Ihrem Konto mit der Domänenregistrierungsstelle an und suchen Sie nach der Seite, auf der DNS-Datensätze verwaltet werden. Suchen Sie nach Links oder Bereichen der Site, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden. Häufig finden Sie in Ihren Kontoinformationen einen Link auf diese Seite. Suchen Sie anschließend nach einem Link, der Sie zu **My domains** (Meine Domänen) führt.

2.  Sobald Sie die Verwaltungsseite für Ihren Domänennamen gefunden haben, suchen Sie nach einem Link, über den Sie die DNS-Datensätze bearbeiten können. Dieser Konfigurationslink kann als **Zone file** (Zonendatei), **DNS Records** (DNS-Datensatz) oder als **Advanced** (Erweitert) bezeichnet werden.

    -   Auf der Seite wurden wahrscheinlich bereits Datensätze wie ein Eintrag, der "**@**" oder '\*' mit einer Domain-Parking-Seite in Verbindung setzt. Möglicherweise sind auch bereits Datensätze wie übliche Unterdomänen wie **www** darauf gespeichert.
    -   Auf der Seite werden **CNAME-Datensätze** angegeben oder Sie finden ein Dropdownmenü, aus dem Sie die Art des Datensatzes auswählen können. Möglicherweise werden auch andere Datensätze wie **A-Datensätze** und **MX-Datensätze** aufgeführt. In manchen Fällen werden CNAME-Datensätze anderweitig bezeichnet, z. B. als **Alias-Datensatz**.
    -   Auf der Seite werden außerdem Felder angezeigt, über die Sie über einen **Hostnamen** oder **Domainnamen** eine **Zuweisung** zu einem anderen Domainnamen vornehmen können.

3.  Jede Registrierung ist spezifisch, doch generell führen Sie eine Zuweisung *von* Ihrem benutzerdefinierten Domänennamen (z. B. **contoso.com**) *zum* Traffic Manager-Domänennamen (**contoso.trafficmanager.net**) durch, der für Ihre Azure-Website verwendet wird.

4.  Speichern Sie die Änderungen, sobald Sie die DNS-Datensetze in Ihrer Registrierung hinzugefügt oder geändert haben.

Aktivieren einer Traffic Manager-Website
----------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

