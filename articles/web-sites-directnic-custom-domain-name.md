<properties title="Learn how to configure an Azure web site to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, Directnic" description="Learn how to configure an Azure web site to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (Directnic)
========================================================================================

[Benutzerdefinierte Domäne](/de-de/documentation/articles/web-sites-custom-domain-name "Benutzerdefinierte Domäne")[GoDaddy](/de-de/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/de-de/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/de-de/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/de-de/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/de-de/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/de-de/documentation/articles/web-sites-directnic-custom-domain-name/ "Websites") | [Website mit Verwendung von Traffic Manager](/de-de/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/ "Website mit Verwendung von Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines benutzerdefinierten Domänennamens, der über [DirectNic.com](https://directnic.com) erworben wurde, mit Azure-Websites.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Grundlegendes zu DNS-Datensätzen](#understanding-records)
-   [Konfigurieren Ihrer Websites für den Shared-, Basic- oder Standardmodus](#bkmk_configsharedmode)
-   [Hinzufügen eines DNS-Datensatzes für Ihre benutzerdefinierte Domäne](#bkmk_configurecname)
-   [Aktivieren der Domäne auf der Website](#enabledomain)

Grundlegendes zu DNS-Datensätzen
--------------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

Konfigurieren Ihrer Websites für den Shared-, Basic- oder Standardmodus
-----------------------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

## Hinzufügen eines DNS-Datensatzes für Ihre benutzerdefinierte Domäne&lt;/h2\>

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag zur DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzufügen. Verwenden Sie hierzu die durch Directnic bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für Directnic.com abrufen.

1.  Melden Sie sich bei Ihrem Konto auf Directnic.com an, und wählen Sie **My Services** und dann **Domains** aus.

    ![Directnic Services-Menü](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2.  Klicken Sie auf den Domänennamen, den Sie mit Ihrer Azure-Website verwenden möchten.

3.  Klicken Sie auf der Verwaltungsseite für Ihre Domäne auf die Schaltfläche **Manage** für **DNS** im Fensterbereich **Services**.

    ![Services-Fensterbereich](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4.  Fügen Sie DNS-Datensätze hinzu, indem Sie die Felder **Type**, **Name** und **Data** ausfüllen. Abschließend klicken Sie auf die Schaltfläche **Add Record**.

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png)

    -   Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Name** die gewünschte Unterdomäne einstellen. Beispiel: **www**. Stellen Sie das Feld **Data** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website ein. Beispiel: **contoso.azurwebsites.net**.

    -   Wenn Sie einen A-Datensatz hinzufügen, müssen Sie das Feld **Name** entweder auf **@** (dies steht für den Stammdomänennamen wie **contoso.com**) oder die zu verwendende Unterdomäne (z. B. **www**) einstellen. Geben Sie im Feld **Data** die IP-Adresse Ihrer Azure-Website an.

        > [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie außerdem einen CNAME-Datensatz mit einer der folgenden Konfigurationen hinzufügen:
        >
        > -   Wert für **Name** ist **www**, Wert für **Data** ist **&lt;ihrwebsitename\>.azurewebsites.net**.
        >
        > ODER
        >
        > -   Wert für **Name** ist **awverify.www**, Wert für **Data** ist **awverify.&lt;ihrwebsitename\>.azurewebsites.net**.
        >
        > Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die im A-Datensatz beschriebene Domäne besitzen.

Aktivieren des Domänennamens auf der Website
--------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

