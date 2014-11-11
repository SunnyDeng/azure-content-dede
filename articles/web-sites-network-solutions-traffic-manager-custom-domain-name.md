<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure website that uses Traffic Manager" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website mit Traffic Manager (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Custom Domain</a><a href="/de-de/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/de-de/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/de-de/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/de-de/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/de-de/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Websites">Website</a> | <a href="/de-de/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="Website mit Verwendung von Traffic Manager" class="current">Website mit Verwendung von Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines über [Network Solutions][1] erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Interpretation von DNS-Datensätzen][Interpretation von DNS-Datensätzen]
-   [Konfigurieren Ihrer Websites für den Modus "Standard"][Konfigurieren Ihrer Websites für den Modus "Standard"]
-   [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne][Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne]
-   [Aktivieren des Traffic Manager für Ihre Website][Aktivieren des Traffic Manager für Ihre Website]

## <a name="understanding-records"></a>Interpretation von DNS-Datensätzen

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

## <a name="bkmk_configsharedmode"></a>Konfigurieren Ihrer Websites für den Modus "Standard"

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne

</p>
Um Ihrer benutzerdefinierten Domäne eine Azure-Website zuzuweisen, müssen Sie in der DNS-Tabelle für Ihre benutzerdefinierte Domäne mithilfe der von Network Solutions bereitgestellten Tools einen neuen Eintrag hinzufügen. Gehen Sie wie folgt vor, um die DNS-Tools zu suchen und zu verwenden:

1.  Melden Sie sich bei networksolutions.com an und wählen Sie oben rechts **My Account** (Mein Konto).

2.  Wählen Sie auf der Registerkarte **My Products and Services** (Meine Produkte und Services) **Edit DNS** (DNS bearbeiten) aus.

    ![DNS-Seite bearbeiten][DNS-Seite bearbeiten]

3.  Wählen Sie im Bereich **Manage <yourdomainname>** (\<IhrDomänenname\> verwalten) der Seite **Domain Names** (Domänennamen) die Option **Edit Advanced DNS Records** (Erweiterte DNS-Datensätze bearbeiten) aus.

    ![Seite der Domänennamen – Bearbeiten erweiterter DNS-Datensätze hervorgehoben][Seite der Domänennamen – Bearbeiten erweiterter DNS-Datensätze hervorgehoben]

4.  Auf der Seite **Update Advanced DNS** (Erweiterten DNS-Datensatz aktualisieren) befindet sich ein Abschnitt für jede Art von Datensatz, unter dem sich die Schaltfläche **Edit** (Bearbeiten) befindet. Verwenden Sie für CNAME-Datensätze den Abschnitt **Host Alias (CNAME Records)** (Host Alias (CNAME-Datensätze)).

    ![Seite für die Aktualisierung erweiterter DNS-Datensätze][Seite für die Aktualisierung erweiterter DNS-Datensätze]

5.  Wenn Sie auf **Edit** (Bearbeiten) klicken, wird Ihnen ein Formular angezeigt, das Sie verwenden können, um bestehende Datensätze zu ändern oder neue hinzuzufügen.

    Wenn Sie einen CNAME-Datensatz hinzufügen, müssen Sie im Feld **Alias** (Alias) zuerst die Unterdomäne festlegen, die Sie verwenden möchten. Beispiel: **www**. Wählen Sie das Kreisfeld neben dem Feld **Other host** (Sonstiger Host) aus, und legen Sie **Other host** (Sonstiger Host) auf den **.trafficmanager.net**-Domänennamen des Traffic Manager-Profils fest, das Sie mit Ihrer Azure-Website verwenden. Beispiel: **contoso.trafficmanager.net**. Lassen Sie im Feld **Refers to Host Name** (Bezug auf Hostname) **Select** (Auswählen) stehen, da dieses Feld nicht benötigt wird, wenn ein CNAME-Datensatz für die Verwendung mit einer Azure-Website erstellt wird.

    ![CNAME-Formular][CNAME-Formular]

6.  Wenn Sie die Datensätze hinzugefügt oder geändert haben, klicken Sie auf **Continue** (Weiter), um die Änderungen anzuzeigen. Wählen Sie **Save changes only** (Nur Änderungen speichern), um die Änderungen zu speichern.

## <a name="enabledomain"></a>Aktivieren einer Traffic Manager-Website

[WACOM.INCLUDE [modes][2]]

  [Network Solutions]: /de-de/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [1]: https://www.networksolutions.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Interpretation von DNS-Datensätzen]: #understanding-records
  [Konfigurieren Ihrer Websites für den Modus "Standard"]: #bkmk_configsharedmode
  [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne]: #bkmk_configurecname
  [Aktivieren des Traffic Manager für Ihre Website]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [DNS-Seite bearbeiten]: ./media/web-sites-custom-domain-name/ns-editdns.png
  [Seite der Domänennamen – Bearbeiten erweiterter DNS-Datensätze hervorgehoben]: ./media/web-sites-custom-domain-name/ns-editadvanced.png
  [Seite für die Aktualisierung erweiterter DNS-Datensätze]: ./media/web-sites-custom-domain-name/ns-updatecnameadvanced.png
  [CNAME-Formular]: ./media/web-sites-custom-domain-name/ns-cnametm.png
  [2]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
