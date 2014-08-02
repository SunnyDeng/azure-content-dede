<properties title="Learn how to configure an Azure web site to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (GoDaddy)
======================================================================================

[Benutzerdefinierte Domäne](/en-us/documentation/articles/web-sites-custom-domain-name "Benutzerdefinierte Domäne")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name/ "Websites") | [Website mit Verwendung von Traffic Manager](/en-us/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/ "Website mit Verwendung von Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines benutzerdefinierten Domänennamens, der über [GoDaddy](https://godaddy.com) erworben wurde, mit Azure-Websites.

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

Hinzufügen eines DNS-Datensatzes für Ihre benutzerdefinierte Domäne
-------------------------------------------------------------------

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag zur DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzufügen. Verwenden Sie hierzu die durch GoDaddy bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für GoDaddy.com abrufen.

1.  Melden Sie sich bei Ihrem Konto auf GoDaddy.com an, und wählen Sie **My Account** und dann **Manage your domains** aus. Wählen Sie den Domänennamen aus, den Sie mit Ihrer Azure-Website verwenden möchten.

    ![Benutzerdefinierte Domänenseite für GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2.  Wählen Sie auf der Seite **Domain details** die Registerkarte **DNS Zone File** aus. Dieser Abschnitt wird verwendet, um die DNS-Datensätze für die Domäne hinzuzufügen und zu ändern. Verwenden Sie die Schaltfläche **Edit**, um den **Zone File Editor** anzuzeigen.

    ![Registerkarte der DNS-Zonendatei](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

3.  Der **Zone File Editor** ist in Abschnitte für jeden Datensatztyp unterteilt, beginnend mit A-Datensätzen (aufgelistet als **A (Host)** im ersten Abschnitt, gefolgt von CNAME-Datensätzen (aufgelistet als **CNAME (Alias)**.) Um einen neuen Eintrag hinzuzufügen, verwenden Sie die Schaltfläche **Quick Add** unterhalb des entsprechenden Abschnitts. Um einen vorhandenen Eintrag zu bearbeiten, wählen Sie diesen Eintrag aus, und passen Sie die vorhandenen Informationen an.

    > [WACOM.NOTE] Bevor Sie Einträge zur Zonendatei hinzufügen, beachten Sie, dass GoDaddy bereits DNS-Datensätze für gängige Stammdomänen (im Editor als **Host** bezeichnet) erstellt hat, z. B. **email**, **files**, **mail** usw. Wenn der Name, den Sie verwenden möchten, bereits vorhanden ist, bearbeiten Sie den vorhandenen Datensatz, statt einen neuen zu erstellen.

    -   Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Host** die gewünschte Unterdomäne einstellen. Beispiel: **www**. Stellen Sie das Feld **Points To** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website ein. Beispiel: **contoso.azurwebsites.net**.

        ![Zonendatei-Editor](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

        > [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie außerdem einen CNAME-Datensatz mit einer der folgenden Konfigurationen hinzufügen:
        >
        > -   Wert für **Host** ist **www**, Wert für **Points To** ist **&lt;ihrwebsitename\>.azurewebsites.net**.
        >
        > ODER
        >
        > -   Wert für **Host** ist **awverify.www**, Wert für **Points To** ist **awverify.&lt;ihrwebsitename\>.azurewebsites.net**.
        >
        > Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die im A-Datensatz beschriebene Domäne besitzen.

    -   Wenn Sie einen A-Datensatz hinzufügen, müssen Sie das Feld **Host** entweder auf **@** (dies steht für den Stammdomänennamen wie **contoso.com**), \* (ein Platzhalter für mehrere Unterdomänen) oder die zu verwendende Unterdomäne (z. B. **www**) einstellen. Geben Sie im Feld **Points To** die IP-Adresse Ihrer Azure-Website an.

        ![Zonendatei-Editor für einen Datensatz](./media/web-sites-custom-domain-name/godaddy-quickaddarecord.png)

4.  Nachdem Sie die gewünschten Datensätze hinzugefügt oder bearbeitet haben, klicken Sie auf **Save Zone File**, um die Änderungen zu speichern.

Aktivieren des Domänennamens auf der Website
--------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

