<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure web site that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

Konfiguration eines benutzerdefinierten Domänennamens für eine Azure-Website mithilfe des Traffic Manager (Register.com)
========================================================================================================================

[Custom Domain](/en-us/documentation/articles/web-sites-custom-domain-name "Custom Domain")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[Network Solutions](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[Website](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name/ "Websites") | [Website mit Traffic Manager](/en-us/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/ "Website mit Traffic Manager")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel informiert Sie über die Verwendung eines von [Register.com](https://register.com) erworbenen personalisierten Domänennamens auf einer Azure-Website.

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

Um Ihrer benutzerdefinierten Domäne eine Azure-Website zuzuweisen, müssen Sie in der DNS-Tabelle für Ihre benutzerdefinierte Domäne mithilfe der von Register.com bereitgestellten Tools einen neuen Eintrag hinzufügen.

1.  Melden Sie sich bei Ihrem Konto auf register.com an und wählen Sie oben rechts **Ihr Konto** aus, um Ihre Domänen anzuzeigen und anschließend Ihren benutzerdefinierten Domänennamen auszuwählen.

    ![Seite: Mein Konto](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

2.  Scrollen Sie auf der Seite nach unten, bis Ihnen **Advanced Technical Settings** (Erweiterte Einstellungen) angezeigt wird. Über die Links in diesem Abschnitt können Sie die Datensätze für Ihre Domain verwalten. Verwenden Sie für CNAME-Datensätze den Link **Edit Domain Aliases Records** (Domänenalias-Datensätze bearbeiten).

    ![Erweiterte Einstellungen](./media/web-sites-custom-domain-name/rdotcom-advancedsettingstm.png)

3.  Wenn Sie auf **Edit** (Bearbeiten) klicken, wird Ihnen ein Formular angezeigt, das Sie verwenden können, um bestehende Datensätze zu ändern oder neue hinzuzufügen. Das Formular ist sowohl für CNAME- als auch A-Datensätze das gleiche.

    -   Wenn Sie einen CNAME-Datensatz erstellen, müssen Sie im Feld **.mydomainname.com** zuerst die Unterdomäne festlegen, die Sie verwenden möchten. Beispiel: **www**. Sie müssen den Wert festlegen, der auf den Domänennamen **.trafficmanager.net** des Traffic Manager-Profils zeigt, das Sie mit Ihrer Azure-Website verwenden. Beispiel: **contoso.trafficmanager.net**. Lassen Sie im Feld **Refers to Host Name** (Bezug auf Hostname) **Select** (Auswählen) stehen, da dieses Feld nicht benötigt wird, wenn ein CNAME-Datensatz für die Verwendung mit einer Azure-Website erstellt wird.

        ![CNAME-Formular](./media/web-sites-custom-domain-name/rdotcom-editcnamerecordtm.png)

4.  Wenn Sie die Datensätze hinzugefügt oder geändert haben, klicken Sie auf **Continue** (Weiter), um die Änderungen anzuzeigen. Wählen Sie **Weiter**, um die Änderungen zu speichern.

Aktivieren einer Traffic Manager-Website
----------------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

