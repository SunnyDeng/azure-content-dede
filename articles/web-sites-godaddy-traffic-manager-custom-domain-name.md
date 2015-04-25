<properties 
	pageTitle="Konfigurieren eines GoDaddy-Domänennamens für eine Azure-Website mit Traffic Manager" 
	description="Erfahren Sie, wie Sie einen Domänennamen von GoDaddy mit Azure-Websites verwenden" 
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

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website mit Traffic Manager (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Benutzerdefinierte Domäne</a><a href="/de-de/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/de-de/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/de-de/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/de-de/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/de-de/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites">Website</a> | <a href="/de-de/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Website mit Traffic Manager</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines über [Go Daddy](https://godaddy.com) erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Grundlegendes zu DNS-Einträgen](#understanding-records)
-   [Konfigurieren Ihrer Websites für den Modus "Standard"](#bkmk_configsharedmode)
-   [Hinzufügen eines DNS-Eintrags für Ihre benutzerdefinierte Domäne](#bkmk_configurecname)
-   [Aktivieren von Traffic Manager für Ihre Website](#enabledomain)

<h2><a name="understanding-records"></a>Grundlegendes zu DNS-Einträgen</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Konfigurieren Ihrer Websites für den Modus "Standard"</h2>

[AZURE.INCLUDE [Modi](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Hinzufügen eines DNS-Eintrags für Ihre benutzerdefinierte Domäne</h2>

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von GoDaddy bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für GoDaddy.com abrufen.

1. Melden Sie sich bei Ihrem Konto auf GoDaddy.com an, und wählen Sie **My Account** und dann **Manage your domains** aus. Wählen Sie den Domänennamen aus, den Sie mit Ihrer Azure-Website verwenden möchten.

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. Wählen Sie auf der Seite **Domain details** die Registerkarte **DNS Zone File** aus. Dieser Abschnitt wird verwendet, um die DNS-Datensätze für die Domäne hinzuzufügen und zu ändern. Klicken Sie auf die Schaltfläche **Edit**, um den **Zone File Editor** anzuzeigen.

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

4. Der **Zone File Editor** ist in Abschnitte für jeden Datensatztyp unterteilt, beginnend mit A-Datensätzen (aufgelistet als **A (Host)** im ersten Abschnitt, gefolgt von CNAME-Datensätzen (aufgelistet als **CNAME (Alias)**.) Um einen neuen Eintrag hinzuzufügen, verwenden Sie die Schaltfläche **Quick Add** unterhalb des entsprechenden Abschnitts. Um einen vorhandenen Eintrag zu bearbeiten, wählen Sie diesen Eintrag aus, und passen Sie die vorhandenen Informationen an.

	![zone file editor](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

	> [AZURE.NOTE] Bevor Sie Einträge zur Zonendatei hinzufügen, beachten Sie, dass GoDaddy bereits DNS-Datensätze für gängige Unterdomänen (im Editor als **Host** bezeichnet) erstellt hat, z. B. **email**, **files**, **mail**. Wenn der Name, den Sie verwenden möchten, bereits vorhanden ist, bearbeiten Sie den vorhandenen Datensatz, statt einen neuen zu erstellen.

	Wenn Sie einen CNAME-Eintrag erstellen, müssen Sie im Feld **host** die Unterdomäne festlegen, die Sie verwenden möchten. Beispiel: **www**. Legen Sie das Feld **Points to** auf den **.trafficmanager.net**-Domänennamen des Traffic Manager-Profils fest, das Sie mit Ihrer Azure-Website verwenden. Beispiel: **contoso.trafficmanager.net**.

	> [AZURE.NOTE] Verwenden Sie CNAME-Datensätze ausschließlich dann, wenn Sie einen benutzerdefinierten Domänennamen einer Website zuweisen, deren Lastenausgleich über Traffic Manager erfolgt.

5. Nachdem Sie die gewünschten Datensätze hinzugefügt oder bearbeitet haben, klicken Sie auf **Save Zone File**, um die Änderungen zu speichern.

<h2><a name="enabledomain"></a>Aktivieren einer Traffic Manager-Website</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]





<!--HONumber=42-->
