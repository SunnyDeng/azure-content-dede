<properties title="Learn how to configure an Azure website to use a domain name registered with GoDaddy" pageTitle="Konfigurieren eines GoDaddy-Domänennamens für eine Azure-Website" metaKeywords="Azure, Azure-Websites, Domänennamen" description=""services""web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Custom Domain</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites" class="current">Website</a> | <a href="/en-us/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Website mit Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines über [Go Daddy](https://godaddy.com) erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Interpretation von DNS-Datensätzen](#understanding-records)
-   [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne](#bkmk_configurecname)
-   [Aktivieren der Domäne auf Ihrer Website](#enabledomain)

<h2><a name="understanding-records"></a>Interpretation von DNS-Datensätzen</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]


<h2><a name="bkmk_configurecname"></a>Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne</h2>

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von GoDaddy bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für GoDaddy.com abrufen.

1. Melden Sie sich bei Ihrem Konto auf GoDaddy.com an, und wählen Sie **My Account** und dann **Manage my domains** aus. Wählen Sie schließlich das Dropdownmenü für den Domänennamen, den Sie mit Ihrer Azure-Website verwenden möchten, und dann **Manage DNS** aus.

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. Rufen Sie auf der Seite **Domain details** die Registerkarte **DNS Zone File** auf. Dieser Abschnitt wird verwendet, um die DNS-Datensätze für die Domäne hinzuzufügen und zu ändern. 

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

	Wählen Sie **Add Record** aus, um einen vorhandenen Datensatz hinzuzufügen.

	Um einen vorhandenen Datensatz zu **bearbeiten**,wählen Sie das Stift- und Papiersymbol neben dem Datensatz aus.

	> [WACOM.NOTE] Bevor Sie neue Datensätze hinzufügen, beachten Sie, dass GoDaddy bereits DNS-Datensätze für gängige Stammdomänen (im Editor als **Host** bezeichnet) erstellt hat, z. B. **email**, **files**, **mail** usw. Wenn der Name, den Sie verwenden möchten, bereits vorhanden ist, bearbeiten Sie den vorhandenen Datensatz, statt einen neuen zu erstellen.

4. Beim Hinzufügen eines Datensatzes müssen Sie zuerst den Datensatztyp auswählen.

	![select record type](./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png)

	Als Nächstes müssen Sie den **Host** angeben (die benutzerdefinierte Domäne oder Unterdomäne) und festlegen, worauf dieser verweist (unter **Points to**.

	![add zone record](./media/web-sites-custom-domain-name/godaddy-addzonerecord.png)

	* Wenn Sie einen **A (Host)**-Datensatz hinzufügen, müssen Sie das Feld **Host** entweder auf **@** (dies steht für den Stammdomänennamen wie **contoso.com**,) * (ein Platzhalter für verschiedene passende Unterdomänen) oder die zu verwendende Unterdomäne (z. B. **www**) einstellen. Geben Sie im Feld **Points To** die IP-Adresse Ihrer Azure-Website an.
	
		> [WACOM.NOTE] Bei Verwendung von A (Host)-Datensätzen müssen Sie auch einen CNAME-Datensatz mit der folgenden Konfiguration hinzufügen:
		> 
		> * Ein **Host**-Wert von **awverify**, der **Points to** einen Wert von **awverify.&lt;ihrwebsitename&gt;.azurewebsites.net**.
		> 
		> Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die Domäne besitzen, die von dem A-Datensatz beschrieben wird.

	* Wenn Sie einen **CNAME (Alias)**-Datensatz hinzufügen, müssen Sie im Feld **Host** die gewünschte Unterdomäne festlegen. Beispiel: **www**. Stellen Sie das Feld **Points To** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website ein. Beispiel: **contoso.azurwebsites.net**.


5. Nachdem Sie die gewünschten Datensätze hinzugefügt oder bearbeitet haben, klicken Sie auf **Finish**, um die Änderungen zu speichern.

<h2><a name="enabledomain"></a>Aktivieren des Domänennamens auf Ihrer Website</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

