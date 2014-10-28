<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Directnic, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Konfigurieren eines benutzerdefinierten Domänennamens für eine Windows Azure-Website mit Traffic Manager (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/web-sites-custom-domain-name" title="Benutzerdefinierte Dom&auml;ne">Benutzerdefinierte Dom&auml;ne</a><a href="/de-de/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/de-de/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/de-de/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/de-de/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/de-de/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/web-sites-directnic-custom-domain-name/" title="Websites">Website</a> | <a href="/de-de/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Website mit Verwendung von Traffic Manager" class="current">Website mit Verwendung von Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

Dieser Artikel enthält Anweisungen zur Verwendung eines über [Directnic.com][Directnic.com] erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

[WACOM.INCLUDE [tmwebsitefooter][tmwebsitefooter]]

[WACOM.INCLUDE [introfooter][introfooter]]

Themen in diesem Artikel:

-   [Interpretation von DNS-Datensätzen][Interpretation von DNS-Datensätzen]
-   [Konfigurieren Ihrer Websites für den Modus "Standard"][Konfigurieren Ihrer Websites für den Modus "Standard"]
-   [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne][Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne]
-   [Aktivieren des Traffic Manager für Ihre Website][Aktivieren des Traffic Manager für Ihre Website]

## <a name="understanding-records"></a>Interpretation von DNS-Datensätzen

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configsharedmode"></a>Konfigurieren Ihrer Websites für den Modus "Standard"

[WACOM.INCLUDE [modes][modes]]

<a name="bkmk_configurecname"></a>

## Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne

</p>
Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von Directnic bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für Directnic.com abrufen.

1.  Melden Sie sich bei Ihrem Konto auf Directnic.com an, und wählen Sie **My Services** und dann **Domains** aus.

    ![Directnic Services-Menü][Directnic Services-Menü]

2.  Klicken Sie auf den Domänennamen, den Sie mit Ihrer Azure-Website verwenden möchten.

3.  Klicken Sie auf der Verwaltungsseite für Ihre Domäne auf die Schaltfläche **Manage** für **DNS** im Fensterbereich **Services**.

    ![Services-Fensterbereich][Services-Fensterbereich]

4.  Fügen Sie DNS-Datensätze hinzu, indem Sie die Felder **Type**, **Name** und **Data** ausfüllen. Abschließend klicken Sie auf die Schaltfläche **Add Record**.

    ![][]

    -   Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Name** die gewünschte Unterdomäne einstellen. Beispiel: **www**. Stellen Sie das Feld **Data** auf den **.trafficmanager.net**-Domänennamen des Traffic Manager-Profils ein, das Sie mit Ihrer Azure-Website verwenden. Beispiel: **contoso.trafficmanager.net**.

        > [WACOM.NOTE] Verwenden Sie CNAME-Datensätze ausschließlich dann, wenn Sie einen benutzerdefinierten Domänennamen einer Website zuweisen, deren Lastenausgleich über Traffic Manager erfolgt.

## <a name="enabledomain"></a>Aktivieren einer Traffic Manager-Website

[WACOM.INCLUDE [modes][1]]

  [Benutzerdefinierte Domäne]: /de-de/documentation/articles/web-sites-custom-domain-name "Benutzerdefinierte Domäne"
  [GoDaddy]: /de-de/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /de-de/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /de-de/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /de-de/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /de-de/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /de-de/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Website]: /de-de/documentation/articles/web-sites-directnic-custom-domain-name/ "Websites"
  [Website mit Verwendung von Traffic Manager]: /de-de/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/ "Website mit Verwendung von Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [Directnic.com]: https://directnic.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Interpretation von DNS-Datensätzen]: #understanding-records
  [Konfigurieren Ihrer Websites für den Modus "Standard"]: #bkmk_configsharedmode
  [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne]: #bkmk_configurecname
  [Aktivieren des Traffic Manager für Ihre Website]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Directnic Services-Menü]: .\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png
  [Services-Fensterbereich]: .\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png
  []: .\media\web-sites-directnic-custom-domain-name\Directnic_DNS_TM.png
  [1]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
