<properties title="Learn how to configure an Azure website to use a domain name registered with Register.com" pageTitle="Configure a Register.com domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Custom Domain</a><a href="/de-de/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/de-de/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/de-de/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/de-de/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/de-de/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Websites" class="current">Website</a> | <a href="/de-de/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="Website mit Verwendung von Traffic Manager">Website mit Verwendung von Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

Dieser Artikel enthält Anweisungen zur Verwendung eines über [Register.com][1] erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

[WACOM.INCLUDE [introfooter][introfooter]]

Themen in diesem Artikel:

-   [Interpretation von DNS-Datensätzen][Interpretation von DNS-Datensätzen]
-   [Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"][Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"]
-   [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne][Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne]
-   [Aktivieren der Domäne auf Ihrer Website][Aktivieren der Domäne auf Ihrer Website]

## <a name="understanding-records"></a>Interpretation von DNS-Datensätzen

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configsharedmode"></a>Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"

[WACOM.INCLUDE [modes][modes]]

<a name="bkmk_configurecname"></a>

## Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne

Um Ihrer benutzerdefinierten Domäne eine Azure-Website zuzuweisen, müssen Sie in der DNS-Tabelle für Ihre benutzerdefinierte Domäne mithilfe der von Register.com bereitgestellten Tools einen neuen Eintrag hinzufügen. Gehen Sie wie folgt vor, um die DNS-Tools zu suchen und zu verwenden.

1.  Melden Sie sich bei Ihrem Konto auf register.com an und wählen Sie oben rechts **Ihr Konto** aus, um Ihre Domänen anzuzeigen und anschließend Ihren benutzerdefinierten Domänennamen auszuwählen.

    ![Seite: Mein Konto][Seite: Mein Konto]

2.  Scrollen Sie auf der Seite nach unten, bis Ihnen **Advanced Technical Settings** (Erweiterte Einstellungen) angezeigt wird. Über die Links in diesem Abschnitt können Sie die Datensätze für Ihre Domain verwalten.

    -   Verwenden Sie für A-Datensätze den Link **Edit IP Address Records** (IP-Adressdatensätze bearbeiten).
    -   Verwenden Sie für CNAME-Datensätze den Link **Edit Domain Aliases Records** (Domänenalias-Datensätze bearbeiten).

    ![Erweiterte Einstellungen][Erweiterte Einstellungen]

3.  Wenn Sie auf **Edit** (Bearbeiten) klicken, wird Ihnen ein Formular angezeigt, das Sie verwenden können, um bestehende Datensätze zu ändern oder neue hinzuzufügen. Das Formular ist sowohl für CNAME- als auch A-Datensätze das gleiche.

    -   Wenn Sie einen CNAME-Datensatz erstellen, müssen Sie im Feld **.mydomainname.com** zuerst die Unterdomäne festlegen, die Sie verwenden möchten. Beispiel: **www**. Als Wert für **points to** (verweist auf) müssen Sie den **.azurewebsites.net**-Domänennamen Ihrer Azure-Website auswählen. Beispiel: **contoso.azurwebsites.net**. Lassen Sie im Feld **Refers to Host Name** (Bezug auf Hostname) **Select** (Auswählen) stehen, da dieses Feld nicht benötigt wird, wenn ein CNAME-Datensatz für die Verwendung mit einer Azure-Website erstellt wird.

        ![CNAME-Formular][CNAME-Formular]

        > [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie auch einen CNAME-Datensatz mit der folgenden Konfiguration hinzufügen:
        >
        > -   Ein **Alias** mit **www** mit einem **Other host** (Sonstigen Host), der wie folgt lautet **\<yourwebsitename\>.azurewebsites.net**.
        >
        > OR
        >
        > -   Ein **Alias** mit **awverify.www** mit einem **Other host** (Sonstigen Host), der wie folgt lautet **awverify.\<yourwebsitename\>.azurewebsites.net**.
        >
        > Der CNAME-Datensatz wird von Azure verwendet, um zu prüfen, ob Sie die Domäne, die vom A-Datensatz beschrieben wird, besitzen.

    -   Wenn Sie einen A-Datensatz erstellen, müssen Sie im Feld **.mydomainname.com** zuerst die Unterdomäne festlegen, die Sie verwenden möchten (Beispiel: **www**.) Lassen Sie das Feld leer, um die Stammdomäne festzulegen oder verwenden Sie, um eine Platzhalter-Zuordnung zu erstellen. Geben Sie im Feld "**Points To**" (Verweist auf) die IP-Adresse Ihrer Azure-Website an.

        ![Datensatzformular][Datensatzformular]

4.  Wenn Sie die Datensätze hinzugefügt oder geändert haben, klicken Sie auf **Continue** (Weiter), um die Änderungen anzuzeigen. Wählen Sie **Weiter**, um die Änderungen zu speichern.

## <a name="enabledomain"></a>Aktivieren des Domänennamens auf Ihrer Website

[WACOM.INCLUDE [modes][2]]

  [Custom Domain]: /de-de/documentation/articles/web-sites-custom-domain-name "Custom Domain"
  [GoDaddy]: /de-de/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /de-de/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /de-de/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /de-de/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /de-de/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /de-de/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Website]: /de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name/ "Websites"
  [Website mit Verwendung von Traffic Manager]: /de-de/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/ "Website mit Verwendung von Traffic Manager"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [1]: https://www.register.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Interpretation von DNS-Datensätzen]: #understanding-records
  [Konfigurieren Ihrer Websites für die Modi "Basic", "Freigegeben" oder "Standard"]: #bkmk_configsharedmode
  [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne]: #bkmk_configurecname
  [Aktivieren der Domäne auf Ihrer Website]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [Seite: Mein Konto]: ./media/web-sites-custom-domain-name/rdotcom-myaccount.png
  [Erweiterte Einstellungen]: ./media/web-sites-custom-domain-name/rdotcom-advancedsettings.png
  [CNAME-Formular]: ./media/web-sites-custom-domain-name/rdotcom-editcnamerecord.png
  [Datensatzformular]: ./media/web-sites-custom-domain-name/rdotcom-editarecord.png
  [2]: ../includes/custom-dns-web-site-enable-on-web-site.md
