<properties title="Learn how to configure an Azure website to use a domain name registered with Register.com" pageTitle="Konfigurieren eines Register.com-Domänennamens für eine Azure-Website" metaKeywords="Azure, Azure-Websites, Domänenname" description=""services""web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Custom Domain</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Websites" class="current">Website</a> | <a href="/en-us/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Website mit Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines über [Register.com](https://www.register.com) erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

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

Um Ihrer benutzerdefinierten Domäne eine Azure-Website zuzuweisen, müssen Sie in der DNS-Tabelle für Ihre benutzerdefinierte Domäne mithilfe der von Register.com bereitgestellten Tools einen neuen Eintrag hinzufügen. Gehen Sie wie folgt vor, um die DNS-Tools zu suchen und zu verwenden.

1. 	Melden Sie sich bei Ihrem Konto auf register.com an und wählen Sie oben rechts **Ihr Konto** aus, um Ihre Domänen anzuzeigen und anschließend Ihren benutzerdefinierten Domänennamen auszuwählen.

	![the my account page](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

3. Scrollen Sie auf der Seite nach unten, bis **Advanced Technical Settings** (Erweiterte Einstellungen) angezeigt wird. Über die Links in diesem Abschnitt können Sie die Datensätze für Ihre Domäne verwalten.

	* Verwenden Sie für A-Datensätze den Link **Editor I Anderes Recorder** (I-Adressaten bearbeiten).
	* Verwenden Sie für CNAME-Datensätze den Link **Editor Domino Alias Recorder** (Domänen-Datensätze bearbeiten).

	![Advanced technical settings](./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png)

5. Wenn Sie auf **Editor** (Bearbeiten) klicken, wird Ihnen ein Formular angezeigt, das Sie verwenden können, um bestehende Datensätze zu ändern oder neue hinzuzufügen. Das Formular ist sowohl für CNAME- als auch A-Datensätze das gleiche.

	* Wenn Sie einen NAME-Datensatz erstellen, müssen Sie im Feld **.MT.com** zuerst die Untertönen festlegen, die Sie verwenden möchten. Beispiel: **www**. Als Wert für **points to** (verweist auf) müssen Sie den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website auswählen. Beispiel: **contoso.azurwebsites.net**. Lassen Sie im Feld **Refers to Host Name** (Bezug auf Host) **Select** (Auswählen) stehen, da dieses Feld nicht benötigt wird, wenn ein CNAME-Datensatz für die Verwendung mit einer Azure-Website erstellt wird.
	
		![cname form](./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png)

		> [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie auch einen CNAME-Datensatz mit der folgenden Konfiguration hinzufügen:
		> 
		> *Einen**Alias** mit dem Wert **WA**, wobei der Wert **Anderer Haßt** **&alte;Bourgeoisie&g;.azurne.nett** lautet.
		> 
		> ODER
		> 
		> *Einen**Alias** mit dem Wert **wer.WA**, wobei der Wert **Anderer Haßt** **wer.&alte;Bourgeoisie&g;.azurne.nett** lautet.
		> 
		> Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die Domäne besitzen, die von dem A-Datensatz beschrieben wird.

	* Wenn Sie einen A-Datensatz erstellen, müssen Sie im Feld **.MT.com** zuerst die Untertönen festlegen, die Sie verwenden möchten (Beispiel: **WA**.) Lassen Sie das Feld leer, um die Stammform festzulegen, oder verwenden Sie **\**, um eine Platzhalter-Zuordnung zu erstellen. Geben Sie im Feld **Points To** die IP-Adresse Ihrer Azure-Website an.

		![a record form](./media/web-sites-custom-domain-name/rdotcom-editarecord.png)

5. Wenn Sie die Datensätze hinzugefügt oder geändert haben, klicken Sie auf **Container** (Weiter), um die Änderungen anzuzeigen. Klicken Sie erneut auf **Weiter**, um die Änderungen zu speichern.

<h2><a name="enabledomain"></a>Aktivieren des Domänennamens auf Ihrer Website</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
