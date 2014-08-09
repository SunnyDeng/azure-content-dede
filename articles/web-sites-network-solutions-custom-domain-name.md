<properties title="Learn how to configure an Azure web site to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (Network Solutions)
================================================================================================

[Custom Domain](/de-de/documentation/articles/web-sites-custom-domain-name "Custom Domain")[GoDaddy](/de-de/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/de-de/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/de-de/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/de-de/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/de-de/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name/ "Websites") | [Website mit Traffic Manager](/de-de/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/ "Website mit Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel informiert Sie über die Verwendung eines von [Network Solutions](https://networksolutions.com) erworbenen personalisierten Domänennamens auf einer Azure-Website.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Interpretation von DNS-Datensätzen](#understanding-records)
-   [Konfigurieren Ihrer Websites für Basis-, Freigabe- und Standardmodus](#bkmk_configsharedmode)
-   [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne](#bkmk_configurecname)
-   [Aktivieren der Domäne auf Ihrer Website](#enabledomain)

Interpretation von DNS-Datensätzen
----------------------------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

Konfigurieren Ihrer Websites für Basis-, Freigabe- und Standardmodus
--------------------------------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

## Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne

Um Ihrer benutzerdefinierten Domäne eine Azure-Website zuzuweisen, müssen Sie in der DNS-Tabelle für Ihre benutzerdefinierte Domäne mithilfe der von Network Solutions bereitgestellten Tools einen neuen Eintrag hinzufügen. Gehen Sie wie folgt vor, um die DNS-Tools von networksolutions.com zu suchen und zu verwenden:

1.  Melden Sie sich bei networksolutions.com an und wählen Sie oben rechts **My Account** (Mein Konto).

2.  Wählen Sie auf der Registerkarte **My Products and Services** (Meine Produkte und Services) **Edit DNS** (DNS bearbeiten) aus.

    ![DNS-Seite bearbeiten](./media/web-sites-custom-domain-name/ns-editdns.png)

3.  Wählen Sie im Bereich \*\*Manage \*\* ( verwalten) der Seite \*\*Domain Names\*\* (Domänennamen) \*\*Edit Advanced DNS Records\*\* (Erweiterte DNS-Datensätze bearbeiten) aus.

    ![Seite der Domänennamen – Bearbeiten erweiterter DNS-Datensätze hervorgehoben](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4.  Auf der Seite **Update Advanced DNS** (Erweiterten DNS-Datensatz aktualisieren) befindet sich ein Abschnitt für jede Art von Datensatz, unter dem sich die Schaltfläche **Edit** (Bearbeiten) befindet.

    -   Verwenden Sie für A-Datensätze den Abschnitt **IP Address (A Records)** (IP-Adresse (A-Datensätze)).
    -   Verwenden Sie für CNAME-Datensätze den Abschnitt **Host Alias (CNAME Records)** (Host Alias (CNAME-Datensätze)).

    ![Seite für die Aktualisierung erweiterter DNS-Datensätze](./media/web-sites-custom-domain-name/ns-updateadvanced.png)

5.  Wenn Sie auf **Edit** (Bearbeiten) klicken, wird Ihnen ein Formular angezeigt, das Sie verwenden können, um bestehende Datensätze zu ändern oder neue hinzuzufügen.

    > [WACOM.NOTE] Bevor Sie neue Einträge hinzufügen, beachten Sie bitte, dass Network Solutions für einige Standard-DNS-Datensätze wie die Stammdomäne ('@') und für Unterdomänen den Platzhalter ('\*') erstellt hat. Sollte der Datensatz, den Sie verwenden möchten, bereits bestehen, ändern Sie ihn, statt einen neuen zu erstellen.

    -   Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Alias** (Alias) zuerst die Unterdomäne festlegen, die Sie verwenden möchten. Beispiel: **www**. Wählen Sie das Kreisfeld neben dem Feld **Other host** (Sonstiger Host) aus und legen Sie **Other host** (Sonstiger Host) auf den Domänennamen **.azurewebsites.net** Ihrer Azure-Website fest. Beispiel: **contoso.azurwebsites.net**. Lassen Sie im Feld **Refers to Host Name** (Bezug auf Hostname) **Select** (Auswählen) stehen, da dieses Feld nicht benötigt wird, wenn ein CNAME-Datensatz für die Verwendung mit einer Azure-Website erstellt wird.

        ![CNAME-Formular](./media/web-sites-custom-domain-name/ns-cname.png)

        > [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie auch einen CNAME-Datensatz mit der folgenden Konfiguration hinzufügen:
        >
        > -   Ein **Alias** mit **www** mit einem **Other host** (Sonstigen Host), der wie folgt lautet **&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > ODER
        >
        > -   Ein **Alias** mit **awverify.www** mit einem **Other host** (Sonstigen Host), der wie folgt lautet **awverify.&lt;yourwebsitename\>.azurewebsites.net**.
        >
        > Der CNAME-Datensatz wird von Azure verwendet, um zu prüfen, ob Sie die Domäne, die vom A-Datensatz beschrieben wird, besitzen.

    -   Wenn Sie einen A-Datensatz hinzufügen, müssen Sie das Feld **Host** entweder auf **@** (präsentiert den Stammdomänennamen wie z. B. **contoso.com**), \* (ein Platzhalter für verschiedene passende Unterdomänen) oder die Unterdomäne, die Sie verwenden möchten (Beispiel: **www**) festlegen. Sie müssen das Feld **Numeric IP** (Numerische IP) auf die IP-Adresse Ihrer Azure-Website festlegen.

        ![Datensatzformular](./media/web-sites-custom-domain-name/ns-arecord.png)

6.  Wenn Sie die Datensätze hinzugefügt oder geändert haben, klicken Sie auf **Continue** (Weiter), um die Änderungen anzuzeigen. Wählen Sie **Save changes only** (Nur Änderungen speichern), um die Änderungen zu speichern.

Aktivieren des Domänennamens auf Ihrer Website
----------------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

