<properties title="Learn how to configure an Azure website to use a domain name registered with Moniker" pageTitle="Konfigurieren eines Moniker-Domänennamens für eine Azure-Website" metaKeywords="Azure, Azure-Websites, Domänennamen" description="Learn how to configure an Azure website to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Custom Domain</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name/" title="Websites" class="current">Website</a> | <a href="/en-us/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Website mit Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines über [Moniker.com](https://moniker.com) erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

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

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von Moniker bereitgestellten Tools. Gehen Sie wie folgt vor, um die DNS-Tools für Moniker.com zu suchen:

1. Melden Sie sich auf Moniker.com an und wählen Sie **My Domains** (Meine Domänen). Klicken Sie anschließend auf **Manage Templates** (Vorlagen verwalten).

    ![My Domains page for Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2. Wählen Sie auf der Seite **Zone Template Management** (Zone-Vorlagenverwaltung) die Option **Create New Template** (Neue Vorlage erstellen) aus.

    ![Moniker Zone Template Management](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3. Geben Sie einen **Vorlagennamen** ein. 

5. Erstellen Sie anschließend einen DNS-Datensatz, indem Sie zuerst die **Art des Datensatzes** auswählen. Geben Sie anschließend den **Hostnamen** und die **Adresse** ein. 

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png)

    * Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Hostname** die gewünschte Unterdomäne einstellen. Beispiel: **www**. Stellen Sie das Feld **Address** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website ein. Beispiel: **contoso.azurwebsites.net**.

    * Wenn Sie einen A-Datensatz hinzufügen, müssen Sie für den **Hostnamen** entweder **@** eingeben (dies steht für den Namen der Root-Domäne, z. B. **contoso.com**,) oder die Unterdomäne, die Sie verwenden möchten (z. B. **www**.) Geben Sie im Feld **Address** die IP-Adresse Ihrer Azure-Website an.

		> [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie auch einen CNAME-Datensatz mit der folgenden Konfiguration hinzufügen:
		> 
		> * Ein **Hostname**-Wert von **www** mit einem **Address**-Wert von **&lt;nameihrerwebsite&gt;.azurewebsites.net**.
		> 
		> ODER
		> 
		> * Ein **Hostname**-Wert von **awverify.www** mit einem **Address**-Wert von **awverify.&lt;nameihrerwebsite&gt;.azurewebsites.net**.
		> 
		> Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die Domäne besitzen, die von dem A-Datensatz beschrieben wird.

7. Klicken Sie auf die Schaltfläche **Add** (Hinzufügen), um den Eintrag hinzuzufügen.

8. Klicken Sie, nachdem alle Einträge hinzugefügt wurden, auf **Save** (Speichern).

5. Wählen Sie **Domain Manager** (Domänenmanager), um zur Liste der Domänen zurückzukehren.

6. Aktivieren Sie das Kästchen neben Ihrer Zieldomäne und klicken Sie erneut auf **Manage Templates** (Vorlagen verwalten).

7. Wählen Sie die neue Vorlage aus, die Sie zuvor erstellt haben. Klicken Sie anschließend auf den Link **place selected domains (1) into this Template** (Ausgewählte Domänen (1) in diese Vorlage kopieren).

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

<h2><a name="enabledomain"></a>Aktivieren des Domänennamens auf Ihrer Website</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]
