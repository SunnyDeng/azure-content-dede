<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Directnic" pageTitle="Konfigurieren eines Directnic-Domänennamens für eine Azure-Website mit Traffic Manager" metaKeywords="Microsoft Azure, Microsoft Azure-Websites, Directnic, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Microsoft Azure-Website mit Traffic Manager (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Custom Domain</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name/" title="Websites">Website</a> | <a href="/en-us/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Website mit Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines über [DirectNic.com](https://directnic.com) erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

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

[WACOM.INCLUDE [Modi](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne</h2>

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von Directnic bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für Directnic.com abrufen.

1. Melden Sie sich bei Ihrem Konto auf Directnic.com an, und wählen Sie **My Services** und dann **Domains** aus. 

    ![Directnic Services Menu](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2. Klicken Sie auf den Domänennamen, den Sie mit Ihrer Azure-Website verwenden möchten.

2. Klicken Sie auf der Verwaltungsseite für Ihre Domäne auf die Schaltfläche **Manage** für **DNS** im Fensterbereich **Services**.

    ![Services Panel](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4. Fügen Sie DNS-Einträge hinzu, indem Sie die Felder **Typ**, **Name** und **Daten** ausfüllen. Abschließend klicken Sie auf die Schaltfläche **Add Record**.

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS_TM.png)

    * Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Name** die gewünschte Unterdomäne einstellen. Beispiel: **www**. Stellen Sie das Feld **Data** auf den **.trafficmanager.net**-Domänennamen des Traffic Manager-Profils ein, das Sie mit Ihrer Azure-Website verwenden. Beispiel: **contoso.trafficmanager.net**.

	    > [WACOM.NOTE] Verwenden Sie CNAME-Datensätze ausschließlich dann, wenn Sie einen benutzerdefinierten Domänennamen einer Website zuweisen, deren Lastenausgleich über Traffic Manager erfolgt.

<h2><a name="enabledomain"></a>Aktivieren einer Traffic Manager-Website</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]
