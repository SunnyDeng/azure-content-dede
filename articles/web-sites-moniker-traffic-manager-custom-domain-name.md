<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Websites, Moniker, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website mit Traffic Manager (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Custom Domain</a><a href="/de-de/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/de-de/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/de-de/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/de-de/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/de-de/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/de-de/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/de-de/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/de-de/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/web-sites-moniker-custom-domain-name/" title="Websites">Website</a> | <a href="/de-de/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Website mit Verwendung von Traffic Manager" class="current">Website mit Verwendung von Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines über [Moniker][1] erworbenen benutzerdefinierten Domänennamens mit Azure-Websites.

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
Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von Moniker bereitgestellten Tools. Gehen Sie wie folgt vor, um die DNS-Tools für Moniker.com zu suchen:

1.  Melden Sie sich auf Moniker.com an und wählen Sie **My Domains** (Meine Domänen). Klicken Sie anschließend auf **Manage Templates** (Vorlagen verwalten).

    ![Seite "My Domains" (Meine Domänen) auf Moniker][Seite "My Domains" (Meine Domänen) auf Moniker]

2.  Wählen Sie auf der Seite **Zone Template Management** (Zonen-Vorlagenverwaltung) **Create New Template** (Neue Vorlage erstellen).

    ![Moniker Zonen-Vorlagenverwaltung][Moniker Zonen-Vorlagenverwaltung]

3.  Geben Sie einen **Vorlagennamen** ein.

4.  Erstellen Sie anschließend einen DNS-Datensatz, indem Sie zuerst die **Art des Datensatzes** auswählen. Geben Sie anschließend den **Hostnamen** und die **Adresse** ein.

    ![Moniker Zonen-Vorlage erstellen][Moniker Zonen-Vorlage erstellen]

    -   Wenn Sie einen CNAME-Datensatz erstellen, müssen Sie im Feld **Hostname** zuerst die Unterdomäne festlegen, die Sie verwenden möchten. Beispiel: **www**. Stellen Sie das Feld **Address** auf den **.trafficmanager.net**-Domänennamen des Traffic Manager-Profils ein, das Sie mit Ihrer Azure-Website verwenden. Beispiel: **contoso.trafficmanager.net**.

        > [WACOM.NOTE] Verwenden Sie CNAME-Datensätze ausschließlich dann, wenn Sie einen benutzerdefinierten Domänennamen einer Website zuweisen, deren Lastenausgleich über Traffic Manager erfolgt.

5.  Klicken Sie auf die Schaltfläche **Add** (Hinzufügen), um den Eintrag hinzuzufügen.

6.  Klicken Sie, nachdem alle Einträge hinzugefügt wurden, auf **Save** (Speichern).

7.  Wählen Sie **Domain Manager** (Domänenmanager), um zur Liste der Domänen zurückzukehren.

8.  Aktivieren Sie das Kästchen neben Ihrer Zieldomäne und klicken Sie erneut auf **Manage Templates** (Vorlagen verwalten).

9.  Wählen Sie die neue Vorlage aus, die Sie zuvor erstellt haben. Klicken Sie anschließend auf den Link **place selected domains (1) into this Template** (Ausgewählte Domänen (1) in diese Vorlage kopieren).

    ![Moniker Zonen-Vorlage erstellen][2]

## <a name="enabledomain"></a>Aktivieren einer Traffic Manager-Website

[WACOM.INCLUDE [modes][3]]

  [Moniker]: /de-de/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [1]: https://moniker.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [Interpretation von DNS-Datensätzen]: #understanding-records
  [Konfigurieren Ihrer Websites für den Modus "Standard"]: #bkmk_configsharedmode
  [Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne]: #bkmk_configurecname
  [Aktivieren des Traffic Manager für Ihre Website]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Moniker Zonen-Vorlagenverwaltung]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png
  [Moniker Zonen-Vorlage erstellen]: .\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png
  [2]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png
  [3]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
