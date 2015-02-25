<properties 
	pageTitle="Konfigurieren eines Directnic-Domänennamens für eine Azure-Website" 
	description="Erfahren Sie mehr über die Konfiguration einer Azure-Website für die Verwendung eines bei Directnic registrierten Domänennamens." 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr,jroth"/>

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Benutzerdefinierte Domäne</a><a href="/de-de/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/de-de/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/de-de/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/de-de/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/de-de/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/web-sites-directnic-custom-domain-name/" title="Websites" class="current">Website</a> | <a href="/de-de/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Website mit Traffic Manager</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]


[AZURE.INCLUDE [Einführung](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines bei [DirectNic.com](https://directnic.com) erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Grundlegendes zu DNS-Einträgen](#understanding-records)
-   [Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"](#bkmk_configsharedmode)
-   [Hinzufügen eines DNS-Eintrags zu Ihrer benutzerdefinierten Domäne](#bkmk_configurecname)
-   [Aktivieren der Domäne auf Ihrer Website](#enabledomain)

<h2><a name="understanding-records"></a>Grundlegendes zu DNS-Einträgen</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"</h2>

[AZURE.INCLUDE [Modi](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Hinzufügen eines DNS-Eintrags für Ihre benutzerdefinierte Domäne</h2>

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von Directnic bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für Directnic.com abrufen.

1. Melden Sie sich mit Ihrem Konto auf Directnic.com an, und wählen Sie **My Services** und dann **Domains** aus.

    ![Directnic Services-Menü](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2. Klicken Sie auf den Domänennamen, den Sie mit Ihrer Azure-Website verwenden möchten.

2. Klicken Sie auf der Verwaltungsseite für Ihre Domäne im Fensterbereich **Services** auf die Schaltfläche **Manage** für **DNS**.

    ![Services-Fensterbereich](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4. Fügen Sie DNS-Einträge hinzu, indem Sie die Felder **Type**, **Name** und **Data** ausfüllen. Abschließend klicken Sie auf die Schaltfläche **Add Record**.   

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png)

    * Wenn Sie einen CNAME-Eintrag erstellen, müssen Sie im Feld **Name** die Unterdomäne festlegen, die Sie verwenden möchten. Beispiel: **www**. Legen Sie das Feld **Data** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website fest. Beispiel: **contoso.azurwebsites.net**.

    * Wenn Sie einen A-Eintrag hinzufügen, müssen Sie das Feld **Name** entweder auf **@** (steht für den Stammdomänennamen, z. B. **contoso.com**) oder die zu verwendende Unterdomäne (z. B. **www**) festlegen. Geben Sie im Feld **Data** die IP-Adresse Ihrer Azure-Website an.

		> [AZURE.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie auch einen CNAME-Datensatz mit der folgenden Konfiguration hinzufügen:
		> 
		> * **Name**-Wert **www** mit einem **Data**-Wert **&lt;IhreWebsite&gt;.azurewebsites.net**.
		> 
		> ODER
		> 
		> * **Name**-Wert **awverify.www** mit einem **Data**-Wert **awverify.&lt;IhreWebsite&gt;.azurewebsites.net**.
		> 
		> Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die Domäne besitzen, die von dem A-Datensatz beschrieben wird.


<h2><a name="enabledomain"></a>Aktivieren des Domänennamens auf Ihrer Website</h2>

[AZURE.INCLUDE [Modi](../includes/custom-dns-web-site-enable-on-web-site.md)]




<!--HONumber=42-->
