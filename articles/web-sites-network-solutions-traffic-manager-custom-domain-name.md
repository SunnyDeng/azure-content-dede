<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure web site that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Konfiguration eines benutzerdefinierten Domänennamens für eine Azure-Website mithilfe des Traffic Manager (Network Solutions)
=============================================================================================================================

[Custom Domain](/en-us/documentation/articles/web-sites-custom-domain-name "Custom Domain")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name/ "Websites") | [Website mit Traffic Manager](/en-us/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/ "Website mit Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel informiert Sie über die Verwendung eines von [Network Solutions](https://networksolutions.com) erworbenen personalisierten Domänennamens auf einer Azure-Website.

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

Um Ihrer benutzerdefinierten Domäne eine Azure-Website zuzuweisen, müssen Sie in der DNS-Tabelle für Ihre benutzerdefinierte Domäne mithilfe der von Network Solutions bereitgestellten Tools einen neuen Eintrag hinzufügen. Gehen Sie wie folgt vor, um die DNS-Tools zu suchen und zu verwenden:

1.  Melden Sie sich bei networksolutions.com an und wählen Sie oben rechts **My Account** (Mein Konto).

2.  Wählen Sie auf der Registerkarte **My Products and Services** (Meine Produkte und Services) **Edit DNS** (DNS bearbeiten) aus.

    ![DNS-Seite bearbeiten](./media/web-sites-custom-domain-name/ns-editdns.png)

3.  Wählen Sie im Bereich \*\*Manage \*\* ( verwalten) der Seite \*\*Domain Names\*\* (Domänennamen) \*\*Edit Advanced DNS Records\*\* (Erweiterte DNS-Datensätze bearbeiten) aus.

    ![Seite der Domänennamen – Bearbeiten erweiterter DNS-Datensätze hervorgehoben](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4.  Auf der Seite **Update Advanced DNS** (Erweiterten DNS-Datensatz aktualisieren) befindet sich ein Abschnitt für jede Art von Datensatz, unter dem sich die Schaltfläche **Edit** (Bearbeiten) befindet. Verwenden Sie für CNAME-Datensätze den Abschnitt **Host Alias (CNAME Records)** (Host Alias (CNAME-Datensätze)).

    ![Seite für die Aktualisierung erweiterter DNS-Datensätze](./media/web-sites-custom-domain-name/ns-updatecnameadvanced.png)

5.  Wenn Sie auf **Edit** (Bearbeiten) klicken, wird Ihnen ein Formular angezeigt, das Sie verwenden können, um bestehende Datensätze zu ändern oder neue hinzuzufügen.

    Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Alias** (Alias) zuerst die Unterdomäne festlegen, die Sie verwenden möchten. Beispiel: **www**. Wählen Sie das Kreisfeld neben dem Feld **Other host** (Sonstiger Host) aus und legen Sie **Other host** (Sonstiger Host) auf den Domänennamen **.trafficmanager.net** Ihrer Azure-Website fest. Beispiel: **contoso.trafficmanager.net**. Lassen Sie im Feld **Refers to Host Name** (Bezug auf Hostname) **Select** (Auswählen) stehen, da dieses Feld nicht benötigt wird, wenn ein CNAME-Datensatz für die Verwendung mit einer Azure-Website erstellt wird.

    ![CNAME-Formular](./media/web-sites-custom-domain-name/ns-cnametm.png)

6.  Wenn Sie die Datensätze hinzugefügt oder geändert haben, klicken Sie auf **Continue** (Weiter), um die Änderungen anzuzeigen. Wählen Sie **Save changes only** (Nur Änderungen speichern), um die Änderungen zu speichern.

Aktivieren einer Traffic Manager-Website
----------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

