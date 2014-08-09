<properties title="Learn how to configure an Azure web site to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, DomainDiscover, TierraNet" description="Learn how to configure an Azure web site to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (DomainDiscover/TierraNet)
=======================================================================================================

[Benutzerdefinierte Domäne](/de-de/documentation/articles/web-sites-custom-domain-name "Benutzerdefinierte Domäne")[GoDaddy](/de-de/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/de-de/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/de-de/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/de-de/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/de-de/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name/ "Websites") | [Website mit Verwendung von Traffic Manager](/de-de/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/ "Website mit Verwendung von Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines benutzerdefinierten Domänennamens, der über [DomainDiscover.com](https://domaindiscover.com) erworben wurde, mit Azure-Websites. DomainDiscover.com ist ein Teil von TierraNet.

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

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag zur DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzufügen. Verwenden Sie hierzu die durch DomainDiscover bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für DomainDiscover.com abrufen.

1.  Melden Sie sich bei Ihrem Konto unter DomainDiscover.com (TierraNet) an, indem Sie **Systemsteuerung** im Menü **Anmeldung** auswählen.

    ![DomainDiscover-Anmeldemenü](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2.  Wählen Sie auf der Seite **Domänendienste** die Domäne aus, die Sie für Ihre Azure-Website verwenden möchten.

    ![Domänenverwaltungsseite](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3.  Klicken Sie unter den Domäneneinstellungen für **DNS-Dienst** auf die Schaltfläche **Bearbeiten**.

    ![DNS-Bearbeitungsschaltfläche](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4.  Wählen Sie im Fenster **DNS verwalten** den Typ des DNS-Datensatzes aus, der in der Liste **Add Records** hinzugefügt werden soll. Klicken Sie dann auf **Hinzufügen**.

    ![DNS-Bearbeitungsschaltfläche](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5.  Geben Sie auf der folgenden Seite die DNS-Datensatzwerte ein. Klicken Sie dann auf **Hinzufügen**.

    ![DNS-Bearbeitungsschaltfläche](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png)

    -   Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie zuerst **CNAME (Alias)** auf der Seite **DNS verwalten** auswählen. Dann stellen Sie im Feld **Host** die gewünschte Unterdomäne ein. Beispiel: **www**. Stellen Sie das Feld **Alias Hostname** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website ein. Beispiel: **contoso.azurwebsites.net**. Geben Sie dann einen Wert für die Lebensdauer (TTL) an, z. B. 1800 Sekunden.

    -   Wenn Sie einen A-Datensatz hinzufügen, müssen Sie zuerst **A** auf der Seite **DNS verwalten** auswählen. Dann stellen Sie das Feld **Host** entweder auf **@** (dies steht für den Stammdomänennamen wie **contoso.com**) oder die zu verwendende Unterdomäne (z. B. **www**) ein. Geben Sie im Feld **IP-Adresse** die IP-Adresse Ihrer Azure-Website an. Geben Sie dann einen Wert für die Lebensdauer (TTL) an, z. B. 1800 Sekunden.

        > [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie außerdem einen CNAME-Datensatz mit einer der folgenden Konfigurationen hinzufügen:
        >
        > -   Wert für **Host** ist **www**, Wert für **Alias Hostname** ist **&lt;ihrwebsitename\>.azurewebsites.net**.
        >
        > ODER
        >
        > -   Wert für **Host** ist **awverify.www**, Wert für **Alias Hostname** ist **awverify.&lt;ihrwebsitename\>.azurewebsites.net**.
        >
        > Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die im A-Datensatz beschriebene Domäne besitzen.

6.  Wenn Sie einen A-Datensatz hinzugefügt haben, werden Sie möglicherweise gewarnt, dass ein vorhandener A-Datensatz für Ihre Domäne nicht deaktiviert ist. Es wird der zuletzt geänderte Datensatz verwendet, und DomainDirect verfügt bereits über einen Standard-A-Datensatz für den Stammdomänennamen. Sie können sich entweder auf diesen Vorrang verlassen oder den Standard-A-Datensatz mit der Schaltfläche **DELETE** entfernen.

Aktivieren des Domänennamens auf der Website
--------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

