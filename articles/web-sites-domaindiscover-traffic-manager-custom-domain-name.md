<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure web site using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, DomainDiscover, TierraNet, Traffic Manager" description="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

Konfigurieren eines benutzerdefinierten Domänennamens für eine Windows Azure-Website mit Traffic Manager (DomainDiscover/TierraNet)
===================================================================================================================================

[Benutzerdefinierte Domäne](/de-de/documentation/articles/web-sites-custom-domain-name "Benutzerdefinierte Domäne")[GoDaddy](/de-de/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/de-de/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/de-de/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/de-de/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/de-de/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name/ "Websites") | [Website mit Verwendung von Traffic Manager](/de-de/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/ "Website mit Verwendung von Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines benutzerdefinierten Domänennamens, der über [DomainDiscover.com](https://domaindiscover.com) erworben wurde, mit Azure-Websites. DomainDiscover.com ist ein Teil von [TierraNet](https://www.tierra.net/).

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

    ![DNS-Bearbeitungsschaltfläche](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png)

    -   Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie zuerst **CNAME (Alias)** auf der Seite **DNS verwalten** auswählen. Dann stellen Sie im Feld **Host** die gewünschte Unterdomäne ein. Beispiel: **www**. Stellen Sie das Feld **Alias Hostname** auf den **.trafficmanager.net**-Domänennamen des Traffic Manager-Profils, das Sie mit Ihrer Azure-Website verwenden, ein. Beispiel: **contoso.trafficmanager.net**. Geben Sie dann einen Wert für die Lebensdauer (TTL) an, z. B. 1800 Sekunden.

        > [WACOM.NOTE] Sie dürfen nur CNAME-Datensätze verwenden, wenn Sie Ihren benutzerdefinierten Domänennamen mit einer Website verknüpfen, die Lastenausgleich mit Traffic Manager durchführt.

Aktivieren der Traffic Manager-Website
--------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

