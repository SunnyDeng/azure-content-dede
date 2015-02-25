<properties 
	pageTitle="Konfigurieren eines Moniker-Domänennamens für eine Azure-Website mit Traffic Manager" 
	description="Erfahren Sie mehr über die Konfiguration einer Azure-Website, die Traffic Manager verwendet, um einen bei Moniker registrierten Domänennamen zu benutzen." 
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

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website mit Traffic Manager (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Benutzerdefinierte Domäne</a><a href="/de-de/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/de-de/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/de-de/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/de-de/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/de-de/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/web-sites-moniker-custom-domain-name/" title="Websites">Website</a> | <a href="/de-de/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Website mit Traffic Manager</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines bei [Moniker](https://moniker.com) erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

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

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>Hinzufügen eines DNS-Eintrags für Ihre benutzerdefinierte Domäne</h2>

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von Moniker bereitgestellten Tools. Gehen Sie wie folgt vor, um die DNS-Tools für Moniker.com zu suchen:

1. Melden Sie sich bei Ihrem Konto auf Moniker.com an, und wählen Sie **My Domains** (Meine Domänen). Klicken Sie anschließend auf **Manage Templates** (Vorlagen verwalten).

    ![My Domains page for Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2. Wählen Sie auf der Seite Zone **Template Management** (Zonenvorlagenverwaltung) **Create New Template** (Neue Vorlage erstellen) aus.

    ![Moniker Zone Template Management](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3. Füllen Sie **Template Name** (Vorlagenname) aus. 

5. Erstellen Sie anschließend einen DNS-Eintrag, indem Sie zuerst die **Art des Datensatzes** auswählen. Geben Sie anschließend den **Hostnamen** und die **Adresse** ein. 

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png)

    * Wenn Sie einen CNAME-Eintrag erstellen, müssen Sie im Feld **Hostname** zuerst die Unterdomäne festlegen, die Sie verwenden möchten. Beispiel: **www**. Sie müssen das Feld **Address** auf den Domänennamen **.trafficmanager.net**  des Traffic Manager-Profils festlegen, das Sie mit Ihrer Azure-Website verwenden. Beispiel: **contoso.trafficmanager.net**.

	    > [AZURE.NOTE] Verwenden Sie CNAME-Datensätze ausschließlich dann, wenn Sie einen benutzerdefinierten Domänennamen einer Website zuweisen, deren Lastenausgleich über Traffic Manager erfolgt.
	 
7. Klicken Sie auf die Schaltfläche **Add** (Hinzufügen), um den Eintrag hinzuzufügen.

8. Klicken Sie, nachdem alle Einträge hinzugefügt wurden, auf **Save** (Speichern).

5. Wählen Sie **Domain Manager** (Domänen-Manager) aus, um zur Liste der Domänen zurückzukehren.

6. Aktivieren Sie das Kästchen neben Ihrer Zieldomäne, und klicken Sie erneut auf **Manage Templates** (Vorlagen verwalten).

7. Wählen Sie die neue Vorlage aus, die Sie zuvor erstellt haben. Klicken Sie anschließend auf den Link **Place selected domains (1) into this Template** (Ausgewählte Domänen (1) in diese Vorlage kopieren).

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

<h2><a name="enabledomain"></a>Aktivieren einer Traffic Manager-Website</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]




<!--HONumber=42-->
