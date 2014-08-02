<properties title="Learn how to configure an Azure web site to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure web site" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (eNom)
===================================================================================

[Benutzerdefinierte Domäne](/en-us/documentation/articles/web-sites-custom-domain-name "Benutzerdefinierte Domäne")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/en-us/documentation/articles/web-sites-enom-custom-domain-name/ "Websites") | [Website mit Verwendung von Traffic Manager](/en-us/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/ "Website mit Verwendung von Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines benutzerdefinierten Domänennamens, der über [eNom](https://enom.com) erworben wurde, mit Azure-Websites.

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

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag zur DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzufügen. Verwenden Sie hierzu die durch eNom bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für enom.com abrufen.

1.  Melden Sie sich bei Ihrem Konto auf eNom an, und wählen Sie **Domains** und dann **My Domains** aus. Daraufhin werden Ihre Domänennamen angezeigt.

2.  Verwenden Sie auf der Seite **My Domains** das Feld **Manage Domain**, um **Host Records** auszuwählen. Damit werden die Host-Datensatzfelder angezeigt.

    ![Registerkarte der DNS-Zonendatei](./media/web-sites-custom-domain-name/e-hostrecords.png)

3.  Im Host-Datensatzeditor können Sie den entsprechenden Datensatztyp über das Feld **Record Type** auswählen. Für Azure-Websites sollten Sie nur die Auswahl **CNAME (Alias)** oder **A (Address)** verwenden.

    ![Zonendatei-Editor](./media/web-sites-custom-domain-name/e-editrecords.png)

    > [WACOM.NOTE] Bevor Sie Einträge zur Zonendatei hinzufügen, beachten Sie, dass eNom bereits DNS-Datensätze für die Stammdomäne ('@') und einen Platzhalter für Unterdomänen ('\*') erstellt hat. Wenn Sie die Stammdomäne auf Ihre Website umleiten möchten oder einen Platzhalter-A-Datensatz verwenden möchten, sollten Sie diese Einträge bearbeiten, statt neue zu erstellen.

    -   Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Host Name** die gewünschte Unterdomäne einstellen. Beispiel: **www**. Stellen Sie das Feld **Address** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website ein. Beispiel: **contoso.azurwebsites.net**.

        > [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie außerdem einen CNAME-Datensatz mit einer der folgenden Konfigurationen hinzufügen:
        >
        > -   Wert für **Alias** ist **www**, Wert für **Other host** ist **&lt;ihrwebsitename\>.azurewebsites.net**.
        >
        > ODER
        >
        > -   Wert für **Alias** ist **awverify.www**, Wert für **Other host** ist **awverify.&lt;ihrwebsitename\>.azurewebsites.net**.
        >
        > Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die im A-Datensatz beschriebene Domäne besitzen.

    -   Wenn Sie einen A-Datensatz hinzufügen, müssen Sie das Feld **Host Name** entweder auf **@** (dies steht für den Stammdomänennamen wie **contoso.com**), \* (ein Platzhalter für mehrere Unterdomänen) oder die zu verwendende Unterdomäne (z. B. **www**) einstellen. Geben Sie im Feld **Address** die IP-Adresse Ihrer Azure-Website an.

        > [WACOM.NOTE] Wenn Sie einen A-Datensatz hinzufügen, müssen Sie außerdem einen CNAME-Datensatz mit dem Host **awverify** und dem Wert **Points to** "\*\*awverify.&lt;ihrwebsitename\>.azurewebsites.net" hinzufügen.

4.  Nachdem Sie die gewünschten Datensätze hinzugefügt oder bearbeitet haben, klicken Sie auf **Save**, um die Änderungen zu speichern.

Aktivieren des Domänennamens auf der Website
--------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

