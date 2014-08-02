<properties  title="Learn how to configure an Azure web site to use a custom domain name" pageTitle="Configure a custom domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

# Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="Benutzerdefinierte Domäne" class="current">Benutzerdefinierte Domäne</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Netzwerklösungen">Netzwerklösungen</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="eNom">eNom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

 
<div  class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-custom-domain-name/" title="Websites" class="current">Website</a> | <a href="/en-us/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website mit Traffic Manager">Website mit Traffic Manager</a></div>

 [WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält allgemeine Anweisungen für die Verwendung eines benutzerdefinierten Domänennamens auf Azure-Websites. Schauen Sie auf den Registerkarten oben in diesem Artikel nach, ob Ihre Domänenregistrierungsstelle aufgeführt ist. Wenn ja, wählen Sie die Registerkarte, um die Schritte für Ihre Domänenregistrierung anzuzeigen.

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

* [Informationen zu DNS-Einträgen](#understanding-records)
* [Konfigurieren Ihrer Websites für den Basis-, Freigabe- oder
  Standardmodus](#bkmk_configsharedmode)
* [Hinzufügen eines DNS-Eintrags für Ihre benutzerdefinierte
  Domäne](#bkmk_configurecname)
* [Aktivieren der Domäne auf Ihrer Website](#enabledomain)

<h2><a name="understanding-records"></a>Informationen zu DNS-Einträgen</h2>


[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Konfigurieren Ihrer Websites für den Basis-, Freigabe- oder Standardmodus</h2>


[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>Hinzufügen eines DNS-Eintrags für Ihre
benutzerdefinierte Domäne</h2>

Um Ihre benutzerdefinierte Domäne mit einer Azure-Website zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie dazu die Tools der Domänenregistrierungsstelle, von der Sie Ihren Domänennamen erworben haben. Führen Sie die folgenden Schritte aus, um die DNS-Tools zu finden und zu verwenden.

1.  Melden Sie sich am Konto bei Ihrer Domänenregistrierung an, und
    suchen Sie nach einer Seite zum Verwalten von DNS-Einträgen. Suchen
    Sie nach Links oder Bereichen auf der Website mit der Bezeichnung
    **Domänenname**, **DNS** oder **Namenserververwaltung**. Häufig
    befindet sich ein Link zu einer solchen Seite in Ihren
    Kontoinformationen. Suchen Sie dann nach einem Link wie etwa **Meine
    Domänen**.

2.  Wenn Sie die Seite zur Verwaltung Ihres Domänennamens gefunden
    haben, suchen Sie nach einem Link, der die Bearbeitung der
    DNS-Einträge ermöglicht. Dieser könnte unter der Bezeichnung
    **Zonendatei**, **DNS-Einträge** oder als Konfigurationslink
    **Erweitert** zu finden sein.
    
    * Auf der Seite sind wahrscheinlich schon einige Einträge erstellt
      worden, z. B. ein Eintrag, der '**@**' oder '\*' mit einer
      Seite zum Domainparking verknüpft. Sie kann auch Einträge für
      allgemeine Unterdomänen wie **www** enthalten.
    * Auf der Seite werden **A-Datensätze** und **CNAME-Datensätze**
      erwähnt, oder es steht eine Dropdownliste zur Auswahl eines
      Datensatztyps zur Verfügung. Die Seite kann auch andere Datensätze
      wie **MX-Datensätze** enthalten. In einigen Fällen haben diese
      eine andere Bezeichnung wie z. B. **IP-Adressdatensätze** anstelle
      von A-Datensätzen bzw. **Aliasdatensätze** anstelle von
      CNAME-Datensätzen.
    * Die Seite umfasst auch Felder, die die **Zuordnung** von einem
      **Hostnamen** oder **Domänennamen** zu einer **IP-Adresse** oder
      einem anderen Domänennamen ermöglichen.

3.  Je nach Registrierungsstelle kann dies zwar unterschiedlich sein, im
    Allgemeinen nehmen Sie die Zuordnung jedoch *von* Ihrem
    benutzerdefinierten Domänennamen (z. B. **contoso.com**) *zum*
    Domänennamen der Azure-Website (z. B. **contoso.azurewebsites.net**)
    oder zur virtuellen IP-Adresse der Azure-Website vor.
    
    * CNAME-Datensätze werden immer der Azure-Websitedomäne
      (**contoso.azurewebsites.net**) zugeordnet. Sie nehmen die
      Zuordnung also *von* einer Domäne wie **www** *zu* Ihrer
      **<Websitename>.azurewebsites.net**-Adresse vor.


     
      > [WACOM.NOTE] Wenn Sie einen A-Datensatz verwenden, müssen Sie
      > auch einen CNAME-Datensatz mit einer der folgenden
      > Konfigurationen hinzufügen:
      > 
      > * Zuordnung *von* **www** *zu* Ihrer
      >   **<Websitename>.azurewebsites.net**
      > 
      > ODER
      > 
      > * Zuordnung *von* **awverify.www** *zu*
      >   **awverify.<Websitename>.azurewebsites.net**
      > 
      > Dieser CNAME-Datensatz wird von Azure verwendet, um zu
      > überprüfen, dass Sie die durch den A-Datensatz beschriebene
      > Domäne besitzen.
    
    * A-Datensätze werden immer der virtuellen IP-Adresse von
      Azure-Websites zugeordnet. Sie nehmen also eine Zuordnung *von*
      einer Domäne wie **www** *zur* virtuellen IP-Adresse der Website
      vor.


     
      > [WACOM.NOTE] Um eine Stammdomäne (z. B. **contoso.com**) einer
      > Azure-Website zuzuordnen, nehmen Sie oft eine Zuordnung von
      > '**@**' oder einem leeren Eintrag zur virtuellen IP-Adresse
      > vor. Um eine Platzhalterzuordnung zu erstellen, die alle
      > Unterdomänen mit der virtuellen IP-Adresse verknüpft, nehmen Sie
      > in der Regel eine Zuordnung von '\*' zur virtuellen IP-Adresse
      > vor.
      > 
      > Wie die Zuordnung eines Stamms oder Platzhalters genau
      > funktioniert, hängt von der Registrierungsstelle ab. Lesen Sie
      > die Dokumentation Ihrer Registrierungsstelle, um spezifischere
      > Anleitungen zu erhalten.

4.  Nachdem Sie die DNS-Einträge bei Ihrer Domänenregistrierungsstelle
    hinzugefügt bzw. bearbeitet haben, speichern Sie die Änderungen.

<h2><a name="enabledomain"></a>Aktivieren der Domäne auf Ihrer Website</h2>


[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]