<properties title="Learn how to configure an Azure website to use a domain name registered with eNom" pageTitle="Konfigurieren eines eNom-Domänennamens für eine Azure-Website" metaKeywords="Microsoft Azure, Microsoft Azure-Websites, Domänenname" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Custom Domain</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name/" title="Websites" class="current">Website</a> | <a href="/en-us/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Website mit Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines über [eNom](https://enom.com) erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

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

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von eNom bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für enom.com abrufen.

1. Melden Sie sich bei Ihrem Konto auf eNom an, und wählen Sie **Domains** und dann **My Domains** aus. Daraufhin werden Ihre Domänennamen angezeigt.

2. Verwenden Sie auf der Seite **My Domains** das Feld **Manage Domain**, um **Host Records** auszuwählen. Damit werden die Host-Datensatzfelder angezeigt.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/e-hostrecords.png)

4. Im Host-Datensatzeditor können Sie den entsprechenden Datensatztyp über das Feld **Record Type** auswählen. Für Azure-Websites sollten Sie nur die Auswahl **CNAME (Alias)** oder **A (Address)** verwenden.

	![zone file editor](./media/web-sites-custom-domain-name/e-editrecords.png)

	> [WACOM.NOTE] Bevor Sie Einträge zur Zonendatei hinzufügen, beachten Sie, dass eNom bereits DNS-Datensätze für die Stammdomäne ('@') und einen Platzhalter für Unterdomänen ('\*') erstellt hat. Wenn Sie die Stammdomäne auf Ihre Website umleiten oder einen Platzhalter-A-Datensatz verwenden möchten, sollten Sie diese Einträge bearbeiten, statt neue zu erstellen.

	* Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Host Name** die gewünschte Unterdomäne einstellen. Beispiel: **www**. Stellen Sie das Feld **Address** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website ein. Beispiel: **contoso.azurwebsites.net**.

		> [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie auch einen CNAME-Datensatz mit der folgenden Konfiguration hinzufügen:
		> 
		> * Ein **Alias**-Wert **www** mit einem **Other host**-Wert **&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> ODER
		> 
		> * Ein **Alias**-Wert **awverify.www** mit einem **Other host**-Wert **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**.
		> 
		> Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die Domäne besitzen, die von dem A-Datensatz beschrieben wird.

	* Wenn Sie einen A-Datensatz hinzufügen, müssen Sie das Feld **Host Name** entweder auf **@** (dies steht für den Stammdomänennamen wie **contoso.com**), * (ein Platzhalter für mehrere Unterdomänen) oder die zu verwendende Unterdomäne (z. B. **www**)einstellen. Geben Sie im Feld **Address** die IP-Adresse Ihrer Azure-Website an.

		> [WACOM.NOTE] Wenn Sie einen A-Datensatz hinzufügen, müssen Sie außerdem einen CNAME-Datensatz mit dem Host **awverify** und dem Wert **Points to** **awverify.&lt;yourwebsitename&gt;.azurewebsites.net.

5. Nachdem Sie die gewünschten Datensätze hinzugefügt oder bearbeitet haben, klicken Sie auf **Save**, um die Änderungen zu speichern.

<h2><a name="enabledomain"></a>Aktivieren des Domänennamens auf Ihrer Website</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
