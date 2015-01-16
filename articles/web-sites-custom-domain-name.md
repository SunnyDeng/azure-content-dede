<properties title="Learn how to configure an Azure website to use a custom domain name" pageTitle="Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website" metaKeywords="Azure, Azure Web Sites, domain name" description=""services""web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth, mwasson" />

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">Custom Domain</a><a href="/en-us/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/en-us/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/en-us/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/en-us/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/en-us/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/web-sites-custom-domain-name/" title="Websites" class="current">Website</a> | <a href="/en-us/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Website mit Traffic Manager</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

Wenn Sie eine Website erstellen, weist Azure diese einer Unterdomäne von "azurewebsites.net" zu. Bei einer Website mit dem Name **contoso** lautet die URL beispielsweise **contoso.azurewebsites.net**. Darüber hinaus weist Azure eine virtuelle IP-Adresse zu.

![contoso.azurewebsites.net subdomain][subdomain]

Bei einer produktiven Website bezwecken Sie wahrscheinlich, dass Benutzer einen benutzerdefinierten Domänennamen sehen. In diesem Artikel wird erläutert, wie Sie eine benutzerdefinierte Domäne mit Azure-Websites erstellen. (Der Artikel enthält allgemeine Anleitungen für beliebige Domänenregistrierungsstellen. Die Registerkarten im oberen Bereich führen zu einigen Artikeln für spezifische Registrierungsstellen.)

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:


-   [Übersicht]
-   [DNS-Eintragstypen]
-   [Suchen der virtuellen IP-Adresse]
-   [Erstellen von DNS-Einträgen]
-   [Erstellen eines "awverify"-Eintrags (nur A-Einträge)](#awverify)
-   [Aktivieren des Domänennamens auf Ihrer Website]


## Übersicht

Zum Konfigurieren eines benutzerdefinierten Domänennamens führen Sie die folgenden allgemeinen Schritte aus:

1. Reservieren Sie Ihren Domänennamen. Diese Schritte werden in diesem Artikel jedoch nicht erläutert. Es gibt zahlreiche Domänenregistrierungsstellen, unter denen Sie auswählen können. Wenn Sie sich registrieren, werden Sie auf der Website durch die erforderlichen Schritte geführt.
1. Erstellen Sie DNS-Einträge, die der Domäne Ihrer Azure-Website zugeordnet sind. 
1. Fügen Sie im Azure-Verwaltungsportal den Domänennamen hinzu. 

In diesem grundlegenden Entwurf sind einige Sonderfälle zu berücksichtigen:

- Zuordnen Ihrer Stammdomäne. Die Stammdomäne ist die Domäne, die Sie bei der Domänenregistrierungsstelle reserviert haben. Beispiel: **contoso.com**.
- Zuordnen einer Unterdomäne. Beispiel: **blogs.contoso.com**.  Sie können verschiedenen Websites unterschiedliche Unterdomänen zuordnen.
- Zuordnen eines Platzhalters. Beispiel: ***.contoso.com**. Ein Platzhaltereintrag gilt für alle Unterdomänen der Domäne. 
 
[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]


## DNS-Eintragstypen

Das Domain Name System (DNS) verwendet Einträge für die Zuordnung von Domänennamen und IP-Adressen. Es gibt mehrere Typen von DNS-Einträgen. Bei Websites erstellen Sie entweder einen *A*-Eintrag oder einen *CNAME*-Eintrag.

- Ein A-Eintrag **(Adresse)** ordnet einer IP-Adresse einen Domänennamen zu. 
- Ein **CNAME**-Eintrag (kanonischer Name) nimmt eine Zuordnung zwischen zwei Domänennamen vor. DNS verwendet den zweiten Namen für die Suche nach der Adresse. Die Benutzer sehen dabei immer noch den ersten Domänennamen in ihrem Browser. Beispielsweise können Sie eine Zuordnung zwischen "contoso.com" und *&lt;IhreWebsite&gt;*.azurewebsites.net. vornehmen.

Wenn sich die IP-Adresse ändert, bleibt der CNAME-Eintrag gültig, während ein A-Eintrag aktualisiert werden muss. Einige Domänenregistrierungsstellen lassen jedoch keine CNAME-Einträge für die Stammdomäne bzw. für Platzhalterdomänen zu. In diesem Fall müssen Sie einen A-Eintrag verwenden. 

> [WACOM.NOTE] Die IP-Adresse kann sich ändern, wenn Sie Ihre Website löschen und neu erstellen bzw. den Websitemodus wieder in den Modus "Kostenlos" ändern.


## Suchen der virtuellen IP-Adresse 

Überspringen Sie diesen Schritt, wenn Sie einen CNAME-Eintrag erstellen. Zum Erstellen eines A-Eintrags benötigen Sie die virtuelle IP-Adresse Ihrer Website. So rufen Sie die IP-Adresse ab

1.	Öffnen Sie das [Azure-Verwaltungsportal](https://manage.windowsazure.com) in Ihrem Browser.
2.	Klicken Sie auf der Registerkarte **Websites** auf den Namen Ihrer Website, und wählen Sie **Dashboard** aus.
3.	Klicken Sie unten auf der Seite auf **Domänen verwalten**. (Wenn diese Option deaktiviert ist, müssen Sie den Modus "Shared", "Basic" oder "Standard" verwenden. Weitere Informationen finden Sie unter [Skalieren von Websites](http://www.windowsazure.com/en-us/documentation/articles/web-sites-scale/).) 

	![](media/web-sites-custom-domain-name/dncmntask-cname-6.png)

4.	Die IP-Adresse ist im unteren Bereich des Dialogfelds aufgeführt.

	![](media/web-sites-custom-domain-name/ipaddress.png)

## Erstellen von DNS-Einträgen

Melden Sie sich bei Ihrer Domänenregistrierungsstelle an, und verwenden Sie die verfügbaren Tools, um einen A-Eintrag oder einen CNAME-Eintrag zu erstellen. Die Websites der einzelnen Registrierungsstellen unterscheiden sich geringfügig voneinander, folgen jedoch gemeinsamen Grundregeln. 

1.	Suchen Sie die Seite für die Verwaltung von DSN-Einträgen. Suchen Sie nach Links oder Bereichen der Website, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden. Häufig finden Sie in Ihren Kontoinformationen einen Link auf diese Seite. Suchen Sie anschließend nach einem Link, der Sie zu Ihren **eigenen Domänen** führt.
2.	Wenn Sie die Verwaltungsseite finden, suchen Sie nach einen Link, über den Sie DNS-Einträge hinzufügen oder bearbeiten können. Dieser Konfigurationslink kann als **Zone file** (Zonendatei), **DNS Records** (DNS-Datensätze) oder als **Advanced** (Erweitert) bezeichnet werden.

Auf der Seite werden A-Einträge und CNAME-Einträge separat aufgeführt, oder es steht eine Dropdownliste zur Auswahl eines Eintragstyps zur Verfügung. Möglicherweise werden auch andere Namen für die Eintragstypen verwendet wie **IP-Adresseintrag** anstelle von A-Eintrag oder **Aliaseintrag** anstelle von CNAME-Eintrag.  Normalerweise erstellt die Registrierungsstelle einige Einträge für Sie, sodass Einträge für die Stammdomäne oder allgemeine Unterdomänen wie **www** bereits vorhanden sein können.

Wenn Sie einen Eintrag erstellen oder bearbeiten, können Sie die Felder verwenden, um Ihren Domänennamen einer IP-Adresse (für A-Einträge) oder einer anderen Domäne (für CNAME-Einträge) zuzuordnen. Bei einem CNAME-Eintrag erfolgt die Zuordnung *von* Ihrer benutzerdefinierten Domäne *zur* Unterdomäne mit dem Namen "azurewebsites.net". 

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


<h2 id="awverify">Erstellen eines "awverify"-Eintrags (nur A-Einträge)</h2>

Wenn Sie einen A-Eintrag erstellen, ist für Azure-Websites auch ein spezieller CNAME-Eintrag erforderlich. Darüber wird sichergestellt, dass Sie der Besitzer der Domäne sind, die Sie verwenden möchten. Der CNAME-Eintrag muss das folgende Format aufweisen. 

- *Wenn der A-Eintrag die Stammdomäne oder eine Platzhalterdomäne zuordnet:* Erstellen Sie einen CNAME-Eintrag, der eine Zuordnung von **awverify.&lt;yourdomain&gt;** zu **awverify.&lt;IhrWebsitename&gt;.azurewebsites.net** vornimmt.  Wenn sich der A-Eintrag beispielsweise auf **contoso.com** bezieht, erstellen Sie einen CNAME-Eintrag für **awverify.contoso.com**.
- *Wenn der A-Eintrag eine spezifische Unterdomäne zuordnet:* Erstellen Sie einen CNAME-Eintrag, der eine Zuordnung von **awverify.&lt;subdomain&gt;** zu **awverify.&lt;yourwebsitename&gt;.azurewebsites.net** vornimmt. Wenn sich der A-Eintrag beispielsweise auf **blogs.contoso.com** bezieht, erstellen Sie einen CNAME-Eintrag für **awverify.blogs.contoso.com**.

Besucher Ihrer Website sehen die awverify-Unterdomäne nicht; sie dient nur zum Überprüfen der Domäne in Azure.

## Aktivieren des Domänennamens auf Ihrer Website

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


<!-- Anchors. -->
[Übersicht]: #overview
[DNS-Eintragstypen]: #dns-record-types
[Suchen der virtuellen IP-Adresse]: #find-the-virtual-ip-address
[Erstellen von DNS-Einträgen]: #create-the-dns-records
[Aktivieren des Domänennamens auf Ihrer Website]: #enable-the-domain-name-on-your-website

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png
