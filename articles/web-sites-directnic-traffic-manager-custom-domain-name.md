<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure web site using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Directnic, Traffic Manager" description="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Konfigurieren eines benutzerdefinierten Domänennamens für eine Windows Azure-Website mit Traffic Manager (Directnic)
====================================================================================================================

[Benutzerdefinierte Domäne](/en-us/documentation/articles/web-sites-custom-domain-name "Benutzerdefinierte Domäne")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/en-us/documentation/articles/web-sites-directnic-custom-domain-name/ "Websites") | [Website mit Verwendung von Traffic Manager](/en-us/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/ "Website mit Verwendung von Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines benutzerdefinierten Domänennamens, der über [DirectNic.com](https://directnic.com) erworben wurde, mit Azure-Websites.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Grundlegendes zu DNS-Datensätzen](#understanding-records)
-   [Konfigurieren Ihrer Websites für den Standardmodus](#bkmk_configsharedmode)
-   [Hinzufügen eines DNS-Datensatzes für Ihre benutzerdefinierte Domäne](#bkmk_configurecname)
-   [Aktivieren von Traffic Manager für Ihre Website](#enabledomain)

Grundlegendes zu DNS-Datensätzen
--------------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

Konfigurieren Ihrer Websites für den Standardmodus
--------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

## Hinzufügen eines DNS-Datensatzes für Ihre benutzerdefinierte Domäne

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag zur DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzufügen. Verwenden Sie hierzu die durch Directnic bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für Directnic.com abrufen.

1.  Melden Sie sich bei Ihrem Konto auf Directnic.com an, und wählen Sie **My Services** und dann **Domains** aus.

    ![Directnic Services-Menü](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2.  Klicken Sie auf den Domänennamen, den Sie mit Ihrer Azure-Website verwenden möchten.

3.  Klicken Sie auf der Verwaltungsseite für Ihre Domäne auf die Schaltfläche **Manage** für **DNS** im Fensterbereich **Services**.

    ![Services-Fensterbereich](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4.  Fügen Sie DNS-Datensätze hinzu, indem Sie die Felder **Type**, **Name** und **Data** ausfüllen. Abschließend klicken Sie auf die Schaltfläche **Add Record**.

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS_TM.png)

    -   Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Name** die gewünschte Unterdomäne einstellen. Beispiel: **www**. Stellen Sie das Feld **Data** auf den **.trafficmanager.net**-Domänennamen des Traffic Manager-Profils, das Sie mit Ihrer Azure-Website verwenden, ein. Beispiel: **contoso.trafficmanager.net**.

        > [WACOM.NOTE] Sie dürfen nur CNAME-Datensätze verwenden, wenn Sie Ihren benutzerdefinierten Domänennamen mit einer Website verknüpfen, die Lastenausgleich mit Traffic Manager durchführt.

Aktivieren der Traffic Manager-Website
--------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

