<properties
	pageTitle="Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service"
	description="Erfahren Sie, wie Sie einen benutzerdefinierten Domänennamen mit einer Web-App in Azure App Service verwenden."
	services="app-service\web"
	documentationCenter=""
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="mwasson"/>

# Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service

<div class="dev-center-tutorial-selector sublanding">
  <a href="/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Benutzerdefinierte Domäne</a>
  <a href="/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a>
  <a href="/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a>
  <a href="/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a>
  <a href="/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a>
  <a href="/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a>
  <a href="/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a>
  <a href="/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a>
  <a href="/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a>
</div>
<div class="dev-center-tutorial-subselector">
  <a href="/documentation/articles/web-sites-custom-domain-name/" title="Websites" class="current">Web-Apps</a> | <a href="/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Web Apps using Traffic Manager">Web-Apps mit Traffic Manager</a>
</div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

Wenn Sie eine Web-App erstellen, weist Azure dieser eine Unterdomäne von "azurewebsites.NET" zu. Wenn Ihre Web-App beispielsweise **contoso** heißt, lautet die URL **contoso.azurewebsites.net**. Darüber hinaus weist Azure eine virtuelle IP-Adresse zu.

<!--todo:![contoso.azurewebsites.net subdomain][subdomain]-->

Bei einer Produktions-Web-App sollten Sie Benutzern einen benutzerdefinierten Domänennamen anzeigen. In diesem Artikel wird erläutert, wie Sie eine benutzerdefinierte Domäne für [App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) konfigurieren. (Der Artikel enthält allgemeine Anleitungen für beliebige Domänenregistrierungsstellen. Die Registerkarten im oberen Bereich führen zu einigen Artikeln für spezifische Registrierungsstellen.)

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:


-   [Übersicht]
-   [DNS-Eintragstypen]
-   [Suchen der virtuellen IP-Adresse]
-   [Erstellen von DNS-Einträgen]
-   [Erstellen eines "awverify"-Eintrags (nur A-Einträge)](#awverify)
-   [Aktivieren des Domänennamens in Ihrer Web-App]


## Übersicht

Zum Konfigurieren eines benutzerdefinierten Domänennamens führen Sie die folgenden allgemeinen Schritte aus:

1. Reservieren Sie Ihren Domänennamen. Diese Schritte werden in diesem Artikel jedoch nicht erläutert. Es gibt zahlreiche Domänenregistrierungsstellen, unter denen Sie auswählen können. Wenn Sie sich registrieren, werden Sie auf der Website durch die erforderlichen Schritte geführt.
1. Erstellen Sie DNS-Einträge, die die Domäne Ihrer Azure-Web-App zuordnen.
1. Fügen Sie den Domänennamen im [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) hinzu.

In diesem grundlegenden Entwurf sind einige Sonderfälle zu berücksichtigen:

- Zuordnen Ihrer Stammdomäne. Die Stammdomäne ist die Domäne, die Sie bei der Domänenregistrierungsstelle reserviert haben. Beispiel: **contoso.com**.
- Zuordnen einer Unterdomäne. Beispiel: **blogs.contoso.com**.  Sie können verschiedenen Web-Apps verschiedene Unterdomänen zuordnen.
- Zuordnen eines Platzhalters. Beispiel: ***.contoso.com**. Ein Platzhaltereintrag gilt für alle Unterdomänen der Domäne.

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]


## DNS-Eintragstypen

Das Domain Name System (DNS) verwendet Einträge für die Zuordnung von Domänennamen und IP-Adressen. Es gibt mehrere Typen von DNS-Einträgen. Für Web-Apps erstellen Sie entweder einen *A*-Datensatz oder einen *CNAME*-Datensatz.

- Ein A-Eintrag **(Adresse)** ordnet einer IP-Adresse einen Domänennamen zu.
- Ein **CNAME-Eintrag (kanonischer Name)** nimmt eine Zuordnung zwischen zwei Domänennamen vor. DNS verwendet den zweiten Namen für die Suche nach der Adresse. Die Benutzer sehen dabei immer noch den ersten Domänennamen in ihrem Browser. Beispielsweise können Sie eine Zuordnung zwischen "contoso.com" und *&lt;IhreWebApp&gt;*.azurewebsites.net. vornehmen.

Wenn sich die IP-Adresse ändert, bleibt der CNAME-Datensatz gültig, während ein A-Datensatz aktualisiert werden muss. Einige Domänenregistrierungsstellen lassen jedoch keine CNAME-Einträge für die Stammdomäne bzw. für Platzhalterdomänen zu. In diesem Fall müssen Sie einen A-Eintrag verwenden.

> [AZURE.NOTE] Die IP-Adresse kann sich ändern, wenn Sie Ihre Web-App löschen und neu erstellen bzw. den Web-App-Modus wieder in den Modus "Kostenlos" ändern.


## Suchen der virtuellen IP-Adresse

Überspringen Sie diesen Schritt, wenn Sie einen CNAME-Eintrag erstellen. Zum Erstellen eines A-Datensatzes benötigen Sie die virtuelle IP-Adresse Ihrer Web-App. So rufen Sie die IP-Adresse ab

1.	Öffnen Sie das [Azure-Verwaltungsportal](https://portal.azure.com) in Ihrem Browser.
2.	Klicken Sie auf der Seite links auf die Option **Durchsuchen**.
3.	Klicken Sie auf das Blatt **Web-Apps**.
4.	Klicken Sie auf den Namen der Web-App.
5.	Klicken Sie auf der Seite **Essentials** auf **Alle Einstellungen**.
6.	Klicken Sie auf **Custom domains and SSL**. Sie finden die IP-Adresse unten auf der Seite (direkt über dem Abschnitt **SSL-Bindungen**).

## Erstellen von DNS-Einträgen

Melden Sie sich bei Ihrer Domänenregistrierungsstelle an, und verwenden Sie die verfügbaren Tools, um einen A-Eintrag oder einen CNAME-Eintrag zu erstellen. Die Web-Apps der einzelnen Registrierungsstellen unterscheiden sich geringfügig voneinander, folgen jedoch gemeinsamen Grundregeln.

1.	Suchen Sie die Seite für die Verwaltung von DSN-Einträgen. Suchen Sie nach Links oder Bereichen der Site mit der Bezeichnung **Domänenname**, **DNS** oder **Namenserververwaltung**. Häufig finden Sie in Ihren Kontoinformationen einen Link auf diese Seite. Suchen Sie anschließend nach einem Link, der Sie zu Ihren eigenen Domänen führt, wie beispielsweise **My domains**.
2.	Wenn Sie die Verwaltungsseite finden, suchen Sie nach einen Link, über den Sie DNS-Einträge hinzufügen oder bearbeiten können. Dieser Konfigurationslink kann als **Zone file** (Zonendatei), **DNS Records** (DNS-Datensatz) oder als **Advanced** (Erweitert) bezeichnet werden.

Auf der Seite werden A-Einträge und CNAME-Einträge separat aufgeführt, oder es steht eine Dropdownliste zur Auswahl eines Eintragstyps zur Verfügung. Möglicherweise werden auch andere Namen für die Eintragstypen verwendet, wie z. B. **IP-Adresseintrag** anstelle von A-Datensatz oder **Aliaseintrag** anstelle von CNAME-Datensatz.  Normalerweise erstellt die Registrierungsstelle einige Einträge für Sie, sodass Einträge für die Stammdomäne oder allgemeine Unterdomänen wie **www** bereits vorhanden sein können.

Wenn Sie einen Eintrag erstellen oder bearbeiten, können Sie die Felder verwenden, um Ihren Domänennamen einer IP-Adresse (für A-Einträge) oder einer anderen Domäne (für CNAME-Einträge) zuzuordnen. Bei einem CNAME-Datensatz erfolgt die Zuordnung *from* (von) Ihrer benutzerdefinierten Domäne *to* (zur) Unterdomäne von "azurewebsites.net".

In vielen Registrierungsstellentools geben Sie einfach den Unterdomänenteil Ihrer Domäne und nicht den gesamten Domänennamen ein. Viele Tools verwenden anstelle der Stammdomäne einfach ein @-Zeichen. Beispiel:

<table cellspacing="0" border="1">
  <tr>
    <th>Host</th>
    <th>Eintragstyp</th>
    <th>IP-Adresse oder URL</th>
  </tr>
  <tr>
    <td>@</td>
    <td>A (Adresse)</td>
    <td>127.0.0.1</td>
  </tr>
  <tr>
    <td>www</td>
    <td>CNAME (Alias)</td>
    <td>contoso.azurewebsites.net</td>
  </tr>
</table>

Wenn der Name Ihrer benutzerdefinierten Domäne z. B. "contoso.com" lautet, werden die folgenden Einträge erstellt:

- **contoso.com** mit einer Zuordnung zu 127.0.0.1.
- **www.contoso.com** mit einer Zuordnung zu **contoso.azurewebsites.net**.


<h2 id="awverify">Erstellen eines "awverify"-Datensatzes (nur A-Datensätze)</h2>

Wenn Sie einen A-Datensatz erstellen, fordert die Web-App auch einen speziellen CNAME-Datensatz an. Über diesen wird sichergestellt, dass Sie der Besitzer der Domäne sind, die Sie verwenden möchten. Der CNAME-Eintrag muss das folgende Format aufweisen.

- *Wenn der A-Datensatz der Stammdomäne oder einer Platzhalterdomäne zugeordnet ist:* Erstellen Sie einen CNAME-Datensatz, der **awverify.&lt;IhreDomäne&gt;** der Domäne **awverify.&lt;IhrWebAppName&gt;.azurewebsites.net** zuordnet.  Wenn sich der A-Datensatz beispielsweise auf **contoso.com** bezieht, erstellen Sie einen CNAME-Datensatz für **awverify.contoso.com**.
- *Wenn der A-Datensatz einer bestimmten Unterdomäne zugeordnet ist:* Erstellen Sie einen CNAME-Datensatz, der **awverify.&lt;Unterdomäne&gt;** der Domäne **awverify.&lt;IhrWebAppName&gt;.azurewebsites.net** zuordnet. Wenn sich der A-Datensatz beispielsweise auf **blogs.contoso.com** bezieht, erstellen Sie einen CNAME-Datensatz für **awverify.blogs.contoso.com**.

Besucher Ihrer Web-App sehen die awverify-Unterdomäne nicht. Sie dient nur zum Überprüfen der Domäne in Azure.

## Aktivieren des Domänennamens in Ihrer Web-App

[AZURE.INCLUDE [Modi](../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service and existing Azure services (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Hinweise zu den Änderungen des neuen Portals gegenüber dem alten finden Sie unter: [Reference for navigating the preview portal (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Anchors. -->
[Übersicht]: #overview
[DNS-Eintragstypen]: #dns-record-types
[Suchen der virtuellen IP-Adresse]: #find-the-virtual-ip-address
[Erstellen von DNS-Einträgen]: #create-the-dns-records
[Aktivieren des Domänennamens in Ihrer Web-App]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!--HONumber=49-->