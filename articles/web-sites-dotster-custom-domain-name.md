<properties title="Learn how to configure an Azure website to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, Dotster" description="Learn how to configure an Azure website to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/web-sites-custom-domain-name" title="Benutzerdefinierte Dom&auml;ne">Benutzerdefinierte Dom&auml;ne</a><a href="/de-de/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/de-de/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/de-de/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/de-de/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/de-de/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/web-sites-dotster-custom-domain-name/" title="Websites" class="current">Website</a> | <a href="/de-de/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Website mit Verwendung von Traffic Manager">Website mit Verwendung von Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

Dieser Artikel enthält Anweisungen zur Verwendung eines über [Dotster.com][Dotster.com] erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

[WACOM.INCLUDE [introfooter][introfooter]]

Themen in diesem Artikel:

-   [Interpretation von DNS-Datensätzen][Interpretation von DNS-Datensätzen]
-   [Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"][Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"]
-   [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne][Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne]
-   [Aktivieren der Domäne auf Ihrer Website][Aktivieren der Domäne auf Ihrer Website]

## <a name="understanding-records"></a>Interpretation von DNS-Datensätzen

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configsharedmode"></a>Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"

[WACOM.INCLUDE [modes][modes]]

<a name="bkmk_configurecname"></a>

## Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne

</p>
Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von Dotster bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für Dotster.com abrufen.

1.  Melden Sie sich bei Ihrem Konto auf Dotster.com an, und wählen Sie **Domain** und dann **DomainCentral** aus.

    ![DomainCentral-Dotster-Menü][DomainCentral-Dotster-Menü]

2.  Wählen Sie Ihre Domäne aus, um eine Liste mit Einstellungen anzuzeigen. Klicken Sie auf den Link **Nameservers**.

    ![Dotster-Domänenkonfigurationsoptionen][Dotster-Domänenkonfigurationsoptionen]

3.  Wählen Sie die Option **Use different name servers** aus. Um den DNS-Dienst von Dotster nutzen zu können, müssen Sie die folgenden Nameserver angeben: ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com, ns4.nameresolve.com.

    ![Dotster-Domänenkonfigurationsoptionen][1]

    > [WACOM.NOTE] Es kann 24-48 Stunden dauern, bis die Änderungen am Nameserver wirksam werden. Die restlichen Schritte in diesem Artikel funktionieren erst dann.

4.  Wählen Sie unter DomainCentral Ihre Domäne und dann **DNS** aus. Wählen Sie in der Liste **Modify** den Typ des DNS-Datensatzes aus, der hinzugefügt werden soll (**CNAME Alias** oder **A Record**).

    ![Dotster-Domänenkonfigurationsoptionen][2]

5.  Füllen Sie dann die Felder **Host** und **Points To** für den Datensatz aus. Abschließend klicken Sie auf die Schaltfläche **Add**.

    ![Dotster-Domänenkonfigurationsoptionen][3]

    -   Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Host** die gewünschte Unterdomäne einstellen. Beispiel: **www**. Stellen Sie das Feld **Points To** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website ein. Beispiel: **contoso.azurwebsites.net**.

    -   Wenn Sie einen A-Datensatz hinzufügen, müssen Sie das Feld **Host** entweder auf [\*\*@\*\*][\*\*@\*\*] (dies steht für den Stammdomänennamen wie **contoso.com**) oder die zu verwendende Unterdomäne (z. B. **www**) einstellen. Geben Sie im Feld **Points To** die IP-Adresse Ihrer Azure-Website an.

        > [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie auch einen CNAME-Datensatz mit der folgenden Konfiguration hinzufügen:
        >
        > -   Wert für **Host** ist **www**, Wert für **Points To** ist **\<ihrwebsitename\>.azurewebsites.net**.
        >
        > OR
        >
        > -   Wert für **Host** ist **awverify.www**, Wert für **Points To** ist **awverify.\<ihrwebsitename\>.azurewebsites.net**.
        >
        > Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die Domäne besitzen, die von dem A-Datensatz beschrieben wird.

## <a name="enabledomain"></a>Aktivieren des Domänennamens auf Ihrer Website

[WACOM.INCLUDE [modes][4]]

  [Benutzerdefinierte Domäne]: /de-de/documentation/articles/web-sites-custom-domain-name "Benutzerdefinierte Domäne"
  [GoDaddy]: /de-de/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /de-de/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /de-de/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /de-de/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /de-de/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /de-de/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Website]: /de-de/documentation/articles/web-sites-dotster-custom-domain-name/ "Websites"
  [Website mit Verwendung von Traffic Manager]: /de-de/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/ "Website mit Verwendung von Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [Dotster.com]: https://dotster.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Interpretation von DNS-Datensätzen]: #understanding-records
  [Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"]: #bkmk_configsharedmode
  [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne]: #bkmk_configurecname
  [Aktivieren der Domäne auf Ihrer Website]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [DomainCentral-Dotster-Menü]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png
  [Dotster-Domänenkonfigurationsoptionen]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png
  [1]: .\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png
  [2]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png
  [3]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME.png
  [\*\*@\*\*]: mailto:**@*
  [4]: ../includes/custom-dns-web-site-enable-on-web-site.md
