<properties title="Learn how to configure an Azure web site to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, Dotster" description="Learn how to configure an Azure web site to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (Dotster)
======================================================================================

[Benutzerdefinierte Domäne](/en-us/documentation/articles/web-sites-custom-domain-name "Benutzerdefinierte Domäne")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/en-us/documentation/articles/web-sites-dotster-custom-domain-name/ "Websites") | [Website mit Verwendung von Traffic Manager](/en-us/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/ "Website mit Verwendung von Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines benutzerdefinierten Domänennamens, der über [Dotster.com](https://dotster.com) erworben wurde, mit Azure-Websites.

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

## Hinzufügen eines DNS-Datensatzes für Ihre benutzerdefinierte Domäne

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag zur DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzufügen. Verwenden Sie hierzu die durch Dotster bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für Dotster.com abrufen.

1.  Melden Sie sich bei Ihrem Konto auf Dotster.com an, und wählen Sie **Domain** und dann **DomainCentral** aus.

    ![DomainCentral-Dotster-Menü](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png)

2.  Wählen Sie Ihre Domäne aus, um eine Liste mit Einstellungen anzuzeigen. Klicken Sie auf den Link **Nameservers**.

    ![Dotster-Domänenkonfigurationsoptionen](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png)

3.  Wählen Sie die Option **Use different name servers** aus. Um den DNS-Dienst von Dotster nutzen zu können, müssen Sie die folgenden Nameserver angeben: ns1.nameresolve.com, ns2.nameresolve.com, ns3.nameresolve.com, ns4.nameresolve.com.

    ![Dotster-Domänenkonfigurationsoptionen](.\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png)

    > [WACOM.NOTE] Es kann 24-48 Stunden dauern, bis die Änderungen am Nameserver wirksam werden. Die restlichen Schritte in diesem Artikel funktionieren erst dann.

4.  Wählen Sie unter DomainCentral Ihre Domäne und dann **DNS** aus. Wählen Sie in der Liste **Modify** den Typ des DNS-Datensatzes aus, der hinzugefügt werden soll (**CNAME Alias** oder **A Record**).

    ![Dotster-Domänenkonfigurationsoptionen](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png)

5.  Füllen Sie dann die Felder **Host** und **Points To** für den Datensatz aus. Abschließend klicken Sie auf die Schaltfläche **Add**.

    ![Dotster-Domänenkonfigurationsoptionen](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME.png)

    -   Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Host** die gewünschte Unterdomäne einstellen. Beispiel: **www**. Stellen Sie das Feld **Points To** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website ein. Beispiel: **contoso.azurwebsites.net**.

    -   Wenn Sie einen A-Datensatz hinzufügen, müssen Sie das Feld **Host** entweder auf **@** (dies steht für den Stammdomänennamen wie **contoso.com**) oder die zu verwendende Unterdomäne (z. B. **www**) einstellen. Geben Sie im Feld **Points To** die IP-Adresse Ihrer Azure-Website an.

        > [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie außerdem einen CNAME-Datensatz mit einer der folgenden Konfigurationen hinzufügen:
        >
        > -   Wert für **Host** ist **www**, Wert für **Points To** ist **&lt;ihrwebsitename\>.azurewebsites.net**.
        >
        > ODER
        >
        > -   Wert für **Host** ist **awverify.www**, Wert für **Points To** ist **awverify.&lt;ihrwebsitename\>.azurewebsites.net**.
        >
        > Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die im A-Datensatz beschriebene Domäne besitzen.

Aktivieren des Domänennamens auf der Website
--------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

