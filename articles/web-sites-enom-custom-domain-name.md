<properties 
	pageTitle="Konfigurieren eines eNom-Domänennamens für eine Azure-Website" 
	description="Erfahren Sie, wie Sie einen Domänennamen von eNom mit Azure-Websites verwenden" 
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
	ms.author="larryfr, jroth"/>

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Benutzerdefinierte Domäne</a><a href="/de-de/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/de-de/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/de-de/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/de-de/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/de-de/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/web-sites-enom-custom-domain-name/" title="Websites" class="current">Website</a> | <a href="/de-de/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Website mit Traffic Manager</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines bei [eNom](https://enom.com) erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Grundlegendes zu DNS-Einträgen](#understanding-records)
-   [Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"](#bkmk_configsharedmode)
-   [Hinzufügen eines DNS-Eintrags für Ihre benutzerdefinierte Domäne](#bkmk_configurecname)
-   [Aktivieren der Domäne auf Ihrer Website](#enabledomain)

<h2><a name="understanding-records"></a>Grundlegendes zu DNS-Einträgen</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Hinzufügen eines DNS-Eintrags für Ihre benutzerdefinierte Domäne</h2>

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von eNom bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für enom.com abrufen.

1. Melden Sie sich mit Ihrem Konto auf eNom an, und wählen Sie **Domains** und dann **My Domains** aus. Daraufhin werden Ihre Domänennamen angezeigt.

2. Wählen Sie auf der Seite **My Domains** im Feld **Manage Domain** den Eintrag **Host Records** aus. Damit werden die Host-Datensatzfelder angezeigt.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/e-hostrecords.png)

4. Im Host-Datensatzeditor können Sie den entsprechenden Datensatztyp über das Feld **Record Type** auswählen. Für Azure-Websites sollten Sie nur die Auswahl **CNAME (Alias)** oder **A (Address)** verwenden.

	![zone file editor](./media/web-sites-custom-domain-name/e-editrecords.png)

	> [AZURE.NOTE]  Bevor Sie Einträge zur Zonendatei hinzufügen, beachten Sie, dass eNom bereits DNS-Datensätze für die Stammdomäne ('@') und einen Platzhalter für Unterdomänen ('\*') erstellt hat. Wenn Sie die Stammdomäne auf Ihre Website umleiten oder einen Platzhalter-A-Datensatz verwenden möchten, sollten Sie diese Einträge bearbeiten, statt neue zu erstellen.

	* Wenn Sie einen CNAME-Eintrag erstellen, müssen Sie im Feld **Host Name ** die Unterdomäne festlegen, die Sie verwenden möchten. Beispiel: **www**. Legen Sie das Feld **Address** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website fest. Beispiel: **contoso.azurwebsites.net**.

		> [AZURE.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie auch einen CNAME-Datensatz mit der folgenden Konfiguration hinzufügen:
		> 
		> * Den Wert **www** für **Alias** mit **&lt;NameIhrerWebsite&gt;.azurewebsites.net** als Wert für **Other host** (Anderer Host).
		> 
		> ODER
		> 
		> * Den Wert **awverify.www** für **Alias** mit **awverify.&lt;NameIhrerWebsite&gt;.azurewebsites.net** als Wert für **Other host** (Anderer Host).
		> 
		> Der CNAME-Datensatz wird von Azure verwendet, um zu prüfen, ob Sie die Domäne, die vom A-Datensatz beschrieben wird, besitzen.

	* Wenn Sie einen A-Datensatz hinzufügen, müssen Sie das Feld **Host Name** entweder auf **@** (dies steht für den Stammdomänennamen wie **contoso.com**), * (ein Platzhalter für verschiedene passende Unterdomänen) oder die zu verwendende Unterdomäne (z. B. **www**) festlegen. Legen Sie das Feld **Address** auf die IP-Adresse Ihrer Azure-Website fest.

		> [AZURE.NOTE] Wenn Sie einen A-Datensatz hinzufügen, müssen Sie außerdem einen CNAME-Datensatz mit dem Host **awverify** und dem Wert **Points to** **awverify.&lt;yourwebsitename&gt;.azurewebsites.net hinzufügen.

5. Nachdem Sie die gewünschten Datensätze hinzugefügt oder bearbeitet haben, klicken Sie auf **Save**, um die Änderungen zu speichern.

<h2><a name="enabledomain"></a>Aktivieren des Domänennamens auf Ihrer Website</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]




<!--HONumber=42-->
