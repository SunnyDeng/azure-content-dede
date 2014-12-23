<properties title="Learn how to configure an Azure website to use a domain name registered with Network Solutions" pageTitle="Konfigurieren eines Network Solutions-Domänennamens für eine Azure-Website" metaKeywords="Azure, Azure-Websites, Domänennamen" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Custom Domain</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Websites" class="current">Website</a> | <a href="/en-us/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Website mit Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines über [Network Solutions](https://www.networksolutions.com) erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Interpretation von DNS-Datensätzen](#understanding-records)
-   [Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"](#bkmk_configsharedmode)
-   [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne](#bkmk_configurecname)
-   [Aktivieren der Domäne auf Ihrer Website](#enabledomain)

<h2><a name="understanding-records"></a>Interpretation von DNS-Datensätzen</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"</h2>

[WACOM.INCLUDE [Modi](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne</h2>

Um Ihrer benutzerdefinierten Domäne eine Azure-Website zuzuweisen, müssen Sie in der DNS-Tabelle für Ihre benutzerdefinierte Domäne mithilfe der von Network Solutions bereitgestellten Tools einen neuen Eintrag hinzufügen. Gehen Sie wie folgt vor, um die DNS-Tools von networksolutions.com zu suchen und zu verwenden:

1. Melden Sie sich bei networksolutions.com an und wählen Sie oben rechts **My Account** (Mein Konto).

3. Wählen Sie auf der Registerkarte **My Products and Services** (Meine Produkte und Services) die Option **Edit DNS** (DNS bearbeiten) aus.

	![edit dns page](./media/web-sites-custom-domain-name/ns-editdns.png)

2. Wählen Sie im Bereich **Manage <yourdomainname>** (<IhrDomänenname> verwalten) der Seite **Domain Names** (Domänennamen) die Option **Edit Advanced DNS Records** (Erweiterte DNS-Datensätze bearbeiten) aus.

	![domain names page with edit advanced dns records highlighted](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4. Auf der Seite **Update Advanced DNS** (Erweiterten DNS-Datensatz aktualisieren) befindet sich ein Abschnitt für jede Art von Datensatz, unter dem sich die Schaltfläche **Edit** (Bearbeiten) befindet.
	
	* Verwenden Sie für A-Datensätze den Abschnitt **IP Address (A Records)** (IP-Adresse (A-Datensätze)).
	* Verwenden Sie für CNAME-Datensätze den Abschnitt **Host Alias (CNAME Records)**  (Host Alias (CNAME-Datensätze)).

	![update advanced dns page](./media/web-sites-custom-domain-name/ns-updateadvanced.png)

5. Wenn Sie auf **Edit** (Bearbeiten) klicken, wird Ihnen ein Formular angezeigt, das Sie verwenden können, um bestehende Datensätze zu ändern oder neue hinzuzufügen. 

	> [WACOM.NOTE] Bevor Sie neue Einträge hinzufügen, beachten Sie bitte, dass Network Solutions einige Standard-DNS-Datensätze wie die Stammdomäne ('@') und für Unterdomänen den Platzhalter ('*') erstellt hat. Sollte der Datensatz, den Sie verwenden möchten, bereits bestehen, ändern Sie ihn, statt einen neuen zu erstellen.

	* Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Alias** die gewünschte Unterdomäne einstellen. Beispiel: **www**. Wählen Sie das Kreisfeld neben dem Feld **Other host** (Sonstiger Host) aus, und legen Sie **Other host** (Sonstiger Host) auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website fest. Beispiel: **contoso.azurwebsites.net**. Lassen Sie im Feld **Refers to Host Name** (Bezug auf Hostname) **Select** (Auswählen) stehen, da dieses Feld nicht benötigt wird, wenn ein CNAME-Datensatz für die Verwendung mit einer Azure-Website erstellt wird.
	
		![cname form](./media/web-sites-custom-domain-name/ns-cname.png)

		> [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie auch einen CNAME-Datensatz mit der folgenden Konfiguration hinzufügen:
		> 
		> * Ein **Alias**-Wert von **www** mit einem **Other host**-Wert von **&lt;nameihrerwebsite&gt;.azurewebsites.net**.
		> 
		> ODER
		> 
		> * Ein **Alias**-Wert von **awverify.www** mit einem **Other host**-Wert von **awverify.&lt;nameihrerwebsite&gt;.azurewebsites.net**.
		> 
		> Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die Domäne besitzen, die von dem A-Datensatz beschrieben wird.

	* Wenn Sie einen A-Datensatz hinzufügen, müssen Sie das Feld **Host** entweder auf **@** (stellt den Stammdomänennamen wie z. B. **contoso.com** dar), * (ein Platzhalter für verschiedene passende Unterdomänen) oder die Unterdomäne, die Sie verwenden möchten (Beispiel: **www**) festlegen. Geben Sie im Feld **Numeric IP** (Numerische IP) die IP-Adresse Ihrer Azure-Website an.

		![a record form](./media/web-sites-custom-domain-name/ns-arecord.png)

5. Wenn Sie die Datensätze hinzugefügt oder geändert haben, klicken Sie auf **Continue** (Weiter), um die Änderungen anzuzeigen. Wählen Sie **Save changes only** (Nur Änderungen speichern), um die Änderungen zu speichern.

<h2><a name="enabledomain"></a>Aktivieren des Domänennamens auf Ihrer Website</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
