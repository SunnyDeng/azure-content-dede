<properties title="Learn how to configure an Azure website to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Konfigurieren eines DomainDiscover-Domänennamens für eine Azure-Website" metaKeywords="Azure, Azure-Websites, DomainDiscover, TierraNet" description="Erfahren Sie mehr über die Konfiguration einer Azure-Website für die Verwendung eines bei DomainDiscover - TierraNet registrierten Domänennamens." services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (DomainDiscover/TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Custom Domain</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Websites" class="current">Website</a> | <a href="/en-us/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Website mit Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines über [DomainDiscover.com](https://domaindiscover.com) erworbenen benutzerdefinierten Domänennamens mit Azure-Websites. DomainDiscover.com ist ein Teil von TierraNet.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Interpretation von DNS-Datensätzen](#understanding-records)
-   [Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"](#bkmk_configsharedmode)
-   [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne](#bkmk_configurecname)
-   [Aktivieren der Domäne auf Ihrer Website](#enabledomain)

<h2><a name="understanding-records"></a>Interpretation von DNS-Datensätzen</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne</h2>

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von DomainDiscover bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für DomainDiscover.com abrufen.

1. Melden Sie sich bei Ihrem Konto unter DomainDiscover.com (TierraNet) an, indem Sie **Control Panel** im Menü **Login** auswählen.

    ![DomainDiscover Login Menu](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2. Wählen Sie auf der Seite **Domain Services** die Domäne aus, die Sie für Ihre Azure-Website verwenden möchten.

    ![Domain management page](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3. Klicken Sie unter den Domäneneinstellungen für **DNS Service** auf die Schaltfläche **Edit**.

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4. Wählen Sie im Fenster **Manage DNS** den Typ des DNS-Datensatzes aus, der in der Liste **Add Records** hinzugefügt werden soll. Klicken Sie dann auf die Schaltfläche **Add**.

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5. Geben Sie auf der folgenden Seite die DNS-Datensatzwerte ein. Klicken Sie dann auf die Schaltfläche **Add**.

    ![DNS edit button](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png)

    * Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie zuerst **CNAME (Alias)** auf der Seite **Manage DNS** auswählen. Dann stellen Sie im Feld **Host** die gewünschte Unterdomäne ein. Beispiel: **www**. Stellen Sie das Feld **Alias Hostname** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website ein. Beispiel: **contoso.azurwebsites.net**. Geben Sie dann einen Wert für die Lebensdauer (TTL) an, z. B. 1800 Sekunden.

    * Wenn Sie einen A-Datensatz hinzufügen, müssen Sie zuerst **A** auf der Seite **Manage DNS** auswählen. Dann stellen Sie das Feld **Host** entweder auf **@** (dies steht für den Stammdomänennamen wie **contoso.com**) oder die zu verwendende Unterdomäne (z. B. **www**) ein. Geben Sie im Feld **IP Address** die IP-Adresse Ihrer Azure-Website an. Geben Sie dann einen Wert für die Lebensdauer (TTL) an, z. B. 1800 Sekunden.

		> [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie auch einen CNAME-Datensatz mit der folgenden Konfiguration hinzufügen:
		> 
		> * Ein **Host**-Wert **www** mit einem **Alias Hostname**-Wert **&lt;IhrWebsitename&gt;.azurewebsites.net**.
		> 
		> ODER
		> 
		> * Ein **Host**-Wert **awverify.www** mit einem **Alias Hostname**-Wert **awverify.&lt;IhrWebsitename&gt;.azurewebsites.net**.
		> 
		> Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die Domäne besitzen, die von dem A-Datensatz beschrieben wird.


5. Wenn Sie einen A-Datensatz hinzugefügt haben, werden Sie möglicherweise gewarnt, dass ein vorhandener A-Datensatz für Ihre Domäne nicht deaktiviert ist. Es wird der zuletzt geänderte Datensatz verwendet, und DomainDirect verfügt bereits über einen Standard-A-Datensatz für den Stammdomänennamen. Sie können sich entweder auf diesen Vorrang verlassen oder den Standard-A-Datensatz mit der Schaltfläche **DELETE** entfernen.

<h2><a name="enabledomain"></a>Aktivieren des Domänennamens auf Ihrer Website</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
