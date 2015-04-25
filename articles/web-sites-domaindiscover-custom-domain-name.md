<properties 
	pageTitle="Konfigurieren eines DomainDiscover-Domänennamens für eine Azure-Website" 
	description="Erfahren Sie mehr über die Konfiguration einer Azure-Website für die Verwendung eines bei DomainDiscover - TierraNet registrierten Domänennamens." 
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

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (DomainDiscover / TierraNet)

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Benutzerdefinierte Domäne</a><a href="/de-de/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/de-de/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/de-de/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/de-de/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/de-de/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Websites" class="current">Website</a> | <a href="/de-de/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Website mit Traffic Manager</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [Einführung](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines bei [DomainDiscover.com](https://domaindiscover.com) erworbenen benutzerdefinierten Domänennamens mit Azure-Websites. DomainDiscover.com ist ein Teil von TierraNet.

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

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von DomainDiscover bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für DomainDiscover.com abrufen.

1. Melden Sie sich mit Ihrem Konto auf DomainDiscover.com (TierraNet) an, indem Sie im Menü **Login** die Option **Control Panel** auswählen.

    ![DomainDiscover-Anmeldemenü](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2. Wählen Sie auf der Seite **Domain Services** die Domäne aus, die Sie für Ihre Azure-Website verwenden möchten.

    ![Domänenverwaltungsseite](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3. Klicken Sie unter den Domäneneinstellungen für **DNS Service** auf die Schaltfläche **Edit**.

    ![DNS-Bearbeitungsschaltfläche](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4. Wählen Sie im Fenster **Manage DNS** den Typ des DNS-Eintrags aus, der in der Liste **Add Records** hinzugefügt werden soll. Klicken Sie dann auf die Schaltfläche **Add**.

    ![DNS-Bearbeitungsschaltfläche](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5. Geben Sie auf der folgenden Seite die DNS-Datensatzwerte ein. Klicken Sie dann auf die Schaltfläche **Add**.

    ![DNS-Bearbeitungsschaltfläche](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png)

    * Wenn Sie einen CNAME-Eintrag hinzufügen, müssen Sie zuerst auf der Seite **Manage DNS** die Einstellung **CNAME (Alias)** auswählen. Legen Sie dann im Feld **Host** die gewünschte Unterdomäne fest. Beispiel: **www**. Legen Sie das Feld **Alias Hostname** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website fest. Beispiel: **contoso.azurwebsites.net**. Geben Sie dann einen Wert für die Lebensdauer (TTL) an, z. B. 1800 Sekunden.

    * Wenn Sie einen A-Eintrag hinzufügen, müssen Sie zunächst auf der Seite **Manage DNS** die Einstellung **A** auswählen. Legen Sie dann das Feld **Name** entweder auf **@** (steht für den Stammdomänennamen, z. B. **contoso.com**) oder die zu verwendende Unterdomäne (z. B. **www**) fest. Sie müssen das Feld **IP Address** auf die IP-Adresse Ihrer Azure-Website festlegen. Geben Sie dann einen Wert für die Lebensdauer (TTL) an, z. B. 1800 Sekunden.

		> [AZURE.NOTE] Wenn Sie einen A-Eintrag verwenden, müssen Sie auch einen CNAME-Eintrag mit der folgenden Konfiguration hinzufügen:
		> 
		> * **Host**-Wert **www** mit einem **Alias Hostname**-Wert **&lt;IhreWebsite&gt;.azurewebsites.net**.
		> 
		> ODER
		> 
		> * **Host**-Wert **awverify.www** mit einem **Alias Hostname**-Wert **awverify.&lt;IhreWebsite&gt;.azurewebsites.net**.
		> 
		> Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die Domäne besitzen, die von dem A-Datensatz beschrieben wird.


5. Wenn Sie einen A-Datensatz hinzugefügt haben, werden Sie möglicherweise gewarnt, dass ein vorhandener A-Datensatz für Ihre Domäne nicht deaktiviert ist. Es wird der zuletzt geänderte Datensatz verwendet, und DomainDirect verfügt bereits über einen Standard-A-Datensatz für den Stammdomänennamen. Sie können sich entweder auf diesen Vorrang verlassen oder den Standard-A-Eintrag mit der Schaltfläche **DELETE** entfernen.

<h2><a name="enabledomain"></a>Aktivieren des Domänennamens auf Ihrer Website</h2>

[AZURE.INCLUDE [Modi](../includes/custom-dns-web-site-enable-on-web-site.md)]





<!--HONumber=42-->
