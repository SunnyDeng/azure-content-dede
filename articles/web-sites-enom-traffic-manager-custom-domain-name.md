<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure web site that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website mit Traffic Manager (eNom)
=======================================================================================================

[Benutzerdefinierte Domäne](/en-us/documentation/articles/web-sites-custom-domain-name "Benutzerdefinierte Domäne")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/en-us/documentation/articles/web-sites-enom-custom-domain-name/ "Websites") | [Website mit Verwendung von Traffic Manager](/en-us/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/ "Website mit Verwendung von Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines benutzerdefinierten Domänennamens, der über [eNom](https://enom.com) erworben wurde, mit Azure-Websites.

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

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag zur DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzufügen. Verwenden Sie hierzu die durch eNom bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für enom.com abrufen.

1.  Melden Sie sich bei Ihrem Konto auf eNom an, und wählen Sie **Domains** und dann **My Domains** aus. Daraufhin werden Ihre Domänennamen angezeigt.

2.  Verwenden Sie auf der Seite **My Domains** das Feld **Manage Domain**, um **Host Records** auszuwählen. Damit werden die Host-Datensatzfelder angezeigt.

    ![Registerkarte der DNS-Zonendatei](./media/web-sites-custom-domain-name/e-hostrecords.png)

3.  Im Host-Datensatzeditor können Sie den entsprechenden Datensatztyp über das Feld **Record Type** auswählen. Für Azure-Websites, die Traffic Manager verwenden, sollten Sie unbedingt **CNAME (Alias)** auswählen, da Traffic Manager nur mit CNAME-Datensätzen funktioniert.

    ![Zonendatei-Editor](./media/web-sites-custom-domain-name/e-editrecordstm.png)

    -   Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Host Name** die gewünschte Unterdomäne einstellen. Beispiel: **www**. Stellen Sie das Feld **Address** auf den **.trafficmanager.net**-Domänennamen des Traffic Manager-Profils, das Sie mit Ihrer Azure-Website verwenden, ein. Beispiel: **contoso.trafficmanager.net**.

4.  Nachdem Sie die gewünschten Datensätze hinzugefügt oder bearbeitet haben, klicken Sie auf **Save**, um die Änderungen zu speichern.

Aktivieren der Traffic Manager-Website
--------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

