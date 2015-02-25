<properties 
	pageTitle="Konfigurieren eines Register.com-Domänennamens für eine Azure-Website" 
	description="Erfahren Sie, wie Sie einen Domänennamen von Register.com mit Azure-Websites verwenden" 
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

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Benutzerdefinierte Domäne</a><a href="/de-de/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/de-de/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/de-de/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/de-de/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/de-de/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Websites" class="current">Website</a> | <a href="/de-de/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Website mit Traffic Manager</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines bei [Register.com](https://www.register.com) erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

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

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von Register.com bereitgestellten Tools. Gehen Sie wie folgt vor, um die DNS-Tools zu suchen und zu verwenden:

1. Melden Sie sich bei Ihrem Konto auf register.com an, und wählen Sie oben rechts **Ihr Konto** aus, um Ihre Domänen anzuzeigen und anschließend Ihren benutzerdefinierten Domänennamen auszuwählen.

	![the my account page](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

3. Führen Sie auf der Seite einen Bildlauf nach unten aus, bis **Advanced Technical Settings** (Erweiterte Einstellungen) angezeigt wird. Über die Links in diesem Abschnitt können Sie die Datensätze für Ihre Domäne verwalten.

	* Verwenden Sie für A-Datensätze den Link **Edit IP Address Records (IP-Adressdatensätze bearbeiten)**.
	* Verwenden Sie für CNAME-Datensätze den Link **Edit Domain Aliases Records (Domänenalias-Datensätze bearbeiten)**.

	![Advanced technical settings](./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png)

5. Wenn Sie auf **Edit** (Bearbeiten) klicken, wird Ihnen ein Formular angezeigt, das Sie verwenden können, um bestehende Datensätze zu ändern oder neue hinzuzufügen. Das Formular ist sowohl für CNAME- als auch A-Datensätze das gleiche.

	* Wenn Sie einen CNAME-Eintrag erstellen, müssen Sie im Feld **.mydomainname.com** zuerst die Unterdomäne festlegen, die Sie verwenden möchten. Beispiel: **www**. Als Wert für **points to** (verweist auf) müssen Sie den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website auswählen. Beispiel: **contoso.azurwebsites.net**. Lassen Sie im Feld **Refers to Host Name** (Bezug auf Hostname) **Select** (Auswählen) stehen, da dieses Feld nicht benötigt wird, wenn ein CNAME-Eintrag für die Verwendung mit einer Azure-Website erstellt wird.
	
		![cname form](./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png)

		> [AZURE.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie auch einen CNAME-Datensatz mit der folgenden Konfiguration hinzufügen:
		> 
		> * Den Wert **www** für **Alias** mit **&lt;NameIhrerWebsite&gt;.azurewebsites.net** als Wert für **Other host** (Anderer Host).
		> 
		> ODER
		> 
		> * Den Wert **awverify.www** für **Alias** mit **awverify.&lt;NameIhrerWebsite&gt;.azurewebsites.net** als Wert für **Other host** (Anderer Host).
		> 
		> Der CNAME-Datensatz  wird von Azure verwendet, um zu prüfen, ob Sie die Domäne, die vom A-Datensatz beschrieben wird, besitzen.

	* Wenn Sie einen A-Datensatz erstellen, müssen Sie im Feld **.mydomainname.com** zuerst die Unterdomäne festlegen, die Sie verwenden möchten (Beispiel: **www**.) Lassen Sie das Feld leer, um die Stammdomäne festzulegen oder verwenden Sie **\**, um eine Platzhalterzuordnung zu erstellen. Geben Sie im Feld **Points To** (Verweist auf) die IP-Adresse Ihrer Azure-Website an.

		![a record form](./media/web-sites-custom-domain-name/rdotcom-editarecord.png)

5. Wenn Sie die Datensätze hinzugefügt oder geändert haben, klicken Sie auf **Continue** (Weiter), um die Änderungen anzuzeigen. Klicken Sie erneut auf **Weiter**, um die Änderungen zu speichern.

<h2><a name="enabledomain"></a>Aktivieren des Domänennamens auf Ihrer Website</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]




<!--HONumber=42-->
