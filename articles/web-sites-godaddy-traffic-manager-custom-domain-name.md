<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure web site that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Konfiguration eines benutzerdefinierten Domänennamens für eine Azure-Website mithilfe des Traffic Manager (GoDaddy)
===================================================================================================================

[Custom Domain](/en-us/documentation/articles/web-sites-custom-domain-name "Custom Domain")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name/ "Websites") | [Website mit Traffic Manager](/en-us/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/ "Website mit Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines benutzerdefinierten Domänennamens, der über [GoDaddy](https://godaddy.com) erworben wurde, mit Azure-Websites.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Interpretation von DNS-Datensätzen](#understanding-records)
-   [Konfigurieren Ihrer Websites für den Standardmodus](#bkmk_configsharedmode)
-   [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne](#bkmk_configurecname)
-   [Aktivieren des Traffic Manager für Ihre Website](#enabledomain)

Interpretation von DNS-Datensätzen
----------------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

Konfigurieren Ihrer Websites für den Standardmodus
--------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

## Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag zur DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzufügen. Verwenden Sie hierzu die durch GoDaddy bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für GoDaddy.com abrufen.

1.  Melden Sie sich bei Ihrem Konto auf GoDaddy.com an, und wählen Sie **My Account** und dann **Manage your domains** aus. Wählen Sie den Domänennamen aus, den Sie mit Ihrer Azure-Website verwenden möchten.

    ![Benutzerdefinierte Domänenseite für GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2.  Wählen Sie auf der Seite **Domain details** die Registerkarte **DNS Zone File** aus. Dieser Abschnitt wird verwendet, um die DNS-Datensätze für die Domäne hinzuzufügen und zu ändern. Verwenden Sie die Schaltfläche **Edit**, um den **Zone File Editor** anzuzeigen.

    ![Registerkarte der DNS-Zonendatei](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

3.  Der **Zone File Editor** ist in Abschnitte für jeden Datensatztyp unterteilt, beginnend mit A-Datensätzen (aufgelistet als **A (Host)** im ersten Abschnitt, gefolgt von CNAME-Datensätzen (aufgelistet als **CNAME (Alias)**.) Um einen neuen Eintrag hinzuzufügen, verwenden Sie die Schaltfläche **Quick Add** unterhalb des entsprechenden Abschnitts. Um einen vorhandenen Eintrag zu bearbeiten, wählen Sie diesen Eintrag aus, und passen Sie die vorhandenen Informationen an.

    ![Zonendatei-Editor](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

    > [WACOM.NOTE] Bevor Sie Einträge zur Zonendatei hinzufügen, beachten Sie, dass GoDaddy bereits DNS-Datensätze für gängige Stammdomänen (im Editor als **Host** bezeichnet) erstellt hat, z. B. **email**, **files**, **mail** usw. Wenn der Name, den Sie verwenden möchten, bereits vorhanden ist, bearbeiten Sie den vorhandenen Datensatz, statt einen neuen zu erstellen.

    Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Host** die gewünschte Unterdomäne festlegen. Beispiel: **www**. Legen Sie das Feld **Points to** auf den **.trafficmanager.net**-Domänennamen des Traffic Manager-Profils fest, das Sie mit Ihrer Azure-Website verwenden. Beispiel: **contoso.trafficmanager.net**.

    > [WACOM.NOTE] Verwenden Sie CNAME-Datensätze ausschließlich dann, wenn Sie einen benutzerdefinierten Domänennamen zu einer Website zuweisen, deren Lastenausgleich über Traffic Manager erfolgt.

4.  Nachdem Sie die gewünschten Datensätze hinzugefügt oder bearbeitet haben, klicken Sie auf **Save Zone File**, um die Änderungen zu speichern.

Aktivieren einer Traffic Manager-Website
----------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

