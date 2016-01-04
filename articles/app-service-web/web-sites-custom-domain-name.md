<properties
	pageTitle="Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service"
	description="Erfahren Sie, wie Sie einen benutzerdefinierten Domänennamen mit einer Web-App in Azure App Service verwenden."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"
	tags="top-support-issue"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/23/2015"
	ms.author="cephalin"/>

# Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service

> [AZURE.SELECTOR]
- [Buy Domain for Web Apps](custom-dns-web-site-buydomains-web-app.md)
- [Web Apps with External Domains](web-sites-custom-domain-name.md)
- [Web Apps with Traffic Manager](web-sites-traffic-manager-custom-domain-name.md)
- [GoDaddy](web-sites-godaddy-custom-domain-name.md)

Wenn Sie eine Web-App erstellen, weist Azure dieser eine Unterdomäne von "azurewebsites.NET" zu. Wenn Ihre Web-App beispielsweise **contoso** heißt, lautet die URL **contoso.azurewebsites.net**. Darüber hinaus weist Azure eine virtuelle IP-Adresse zu.

Bei einer Produktions-Web-App können Sie Benutzern einen benutzerdefinierten Domänennamen anzeigen. In diesem Artikel wird erläutert, wie Sie eine benutzerdefinierte Domäne für [App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) konfigurieren.

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](http://azure.microsoft.com/support/forums/) stellen, um dort Hilfe von Azure-Experten zu erhalten. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](http://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## Übersicht

Wenn Sie noch keinen externen Domänennamen registriert haben (d. h. nicht „*.azurewebsites.net“), ist die einfachste Möglichkeit zum Einrichten einer benutzerdefinierten Domäne der direkte Kauf im [Azure-Portal](https://portal.azure.com). Mit dieser Vorgehensweise können Sie den Domänennamen Ihrer Web-Apps direkt im Portal verwalten, anstatt auf einer Website von Drittanbietern wie GoDaddy. Außerdem wird das Konfigurieren des Domänennamens in Ihrer Web-App erheblich vereinfacht, und zwar unabhängig davon, ob Ihre Web-App [Azure Traffic Manager](web-sites-traffic-manager-custom-domain-name.md) verwendet oder nicht. Weitere Informationen finden Sie unter [Kaufen und Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service](custom-dns-web-site-buydomains-web-app.md).

Wenn Sie bereits über einen Domänennamen verfügen oder eine Domäne aus anderen Domänenregistrierungsstellen reservieren möchten, führen Sie die folgenden allgemeinen Schritte aus, um einen benutzerdefinierten Domänennamen für Web-Apps zu verwenden (weitere Informationen finden Sie in den [Anweisungen für GoDaddy.com](web-sites-godaddy-custom-domain-name.md)):

1. Reservieren Sie Ihren Domänennamen. Diese Schritte werden in diesem Artikel jedoch nicht erläutert. Es gibt zahlreiche Domänenregistrierungsstellen, unter denen Sie auswählen können. Wenn Sie sich registrieren, werden Sie auf der Website durch die erforderlichen Schritte geführt.
1. Erstellen Sie DNS-Einträge, die die Domäne Ihrer Azure-Web-App zuordnen.
1. Fügen Sie den Domänennamen im [Azure-Portal](https://portal.azure.com) hinzu.

In diesem grundlegenden Entwurf sind eine Sonderfälle zu berücksichtigen:

- Zuordnen Ihrer Stammdomäne. Die Stammdomäne ist die Domäne, die Sie bei der Domänenregistrierungsstelle reserviert haben. Beispiel: **contoso.com**.
- Zuordnen einer Unterdomäne. Beispiel: **blogs.contoso.com**. Sie können verschiedenen Web-Apps verschiedene Unterdomänen zuordnen.
- Zuordnen eines Platzhalters. Beispiel: ***.contoso.com**. Ein Platzhaltereintrag gilt für alle Unterdomänen der Domäne.

[AZURE.INCLUDE [Modi](../../includes/custom-dns-web-site-modes.md)]


## DNS-Eintragstypen

Das Domain Name System (DNS) verwendet Einträge für die Zuordnung von Domänennamen und IP-Adressen. Es gibt mehrere Typen von DNS-Einträgen. Für Web-Apps erstellen Sie entweder einen *A*-Eintrag oder einen *CNAME*-Eintrag.

- Ein A-Eintrag **(Adresse)** ordnet einer IP-Adresse einen Domänennamen zu.
- Ein **CNAME**-Eintrag (kanonischer Name) nimmt eine Zuordnung zwischen zwei Domänennamen vor. DNS verwendet den zweiten Namen für die Suche nach der Adresse. Die Benutzer sehen dabei immer noch den ersten Domänennamen in ihrem Browser. Beispielsweise können Sie eine Zuordnung zwischen "contoso.com" und *&lt;IhreWebApp&gt;*.azurewebsites.net vornehmen.

Wenn sich die IP-Adresse ändert, bleibt der CNAME-Entrag gültig, während ein A-Eintrag aktualisiert werden muss. Einige Domänenregistrierungsstellen lassen jedoch keine CNAME-Einträge für die Stammdomäne bzw. für Platzhalterdomänen zu. In diesem Fall müssen Sie einen A-Eintrag verwenden.

> [AZURE.NOTE]Die IP-Adresse kann sich ändern, wenn Sie Ihre Web-App löschen und neu erstellen bzw. den Web-App-Modus wieder in den Modus "Kostenlos" ändern.


## Suchen der virtuellen IP-Adresse

Überspringen Sie diesen Schritt, wenn Sie einen CNAME-Eintrag erstellen. Zum Erstellen eines A-Datensatzes benötigen Sie die virtuelle IP-Adresse Ihrer Web-App. So rufen Sie die IP-Adresse ab

1.	Öffnen Sie in Ihrem Browser das [Azure-Portal](https://portal.azure.com).
2.	Klicken Sie auf der Seite links auf die Option **Durchsuchen**.
3.	Klicken Sie auf das Blatt **Web-Apps**.
4.	Klicken Sie auf den Namen der Web-App.
5.	Klicken Sie auf der Seite **Essentials** auf **Alle Einstellungen**.
6.	Klicken Sie auf **Benutzerdefinierte Domänen und SSL**.
7.	Klicken Sie auf dem Blatt **Benutzerdefinierte Domänen und SSL** auf **Externe Domänen einreichen**. Die IP-Adresse befindet sich unten in diesem Bereich.

## Erstellen von DNS-Einträgen

Melden Sie sich bei Ihrer Domänenregistrierungsstelle an, und verwenden Sie die verfügbaren Tools, um einen A-Eintrag oder einen CNAME-Eintrag zu erstellen. Die Web-Apps der einzelnen Registrierungsstellen unterscheiden sich geringfügig voneinander, folgen jedoch gemeinsamen Grundregeln.

1.	Suchen Sie die Seite für die Verwaltung von DSN-Einträgen. Suchen Sie nach Links oder Bereichen der Website, die als **Domänenname**, **DNS** oder **Namenserververwaltung** bezeichnet werden. Häufig finden Sie in Ihren Kontoinformationen einen Link auf diese Seite. Suchen Sie anschließend nach einem Link, der Sie zu Ihren eigenen Domänen führt.
2.	Wenn Sie die Verwaltungsseite finden, suchen Sie nach einen Link, über den Sie DNS-Einträge hinzufügen oder bearbeiten können. Dieser Konfigurationslink kann als **Zone file** (Zonendatei), **DNS Records** (DNS-Eintrag) oder als **Advanced** (Erweitert) bezeichnet werden.

Auf der Seite werden A-Einträge und CNAME-Einträge separat aufgeführt, oder es steht eine Dropdownliste zur Auswahl eines Eintragstyps zur Verfügung. Möglicherweise werden auch andere Namen für die Eintragstypen verwendet wie **IP-Adresseintrag** anstelle von A-Eintrag oder **Aliaseintrag** anstelle von CNAME-Eintrag. Normalerweise erstellt die Registrierungsstelle einige Einträge für Sie, sodass Einträge für die Stammdomäne oder allgemeine Unterdomänen wie **www** bereits vorhanden sein können.

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
    <td>168.62.48.183</td>
  </tr>
  <tr>
    <td>www</td>
    <td>CNAME (Alias)</td>
    <td>contoso.azurewebsites.net</td>
  </tr>
</table>

Wenn der Name Ihrer benutzerdefinierten Domäne z. B. "contoso.com" lautet, werden die folgenden Einträge erstellt:

- **contoso.com** mit einer Zuordnung zu 168.62.48.183.
- **www.contoso.com** mit einer Zuordnung zu **contoso.azurewebsites.net**.

>[AZURE.NOTE]Die erforderlichen Domäneneinträge für Ihre Web-App können mit Azure DNS gehostet werden. Informationen zum Konfigurieren Ihrer benutzerdefinierten Domäne sowie zum Erstellen Ihrer Einträge in Azure DNS finden Sie unter [Erstellen von benutzerdefinierten DNS-Einträgen für eine Web-App](../dns-web-sites-custom-domain).

<a name="awverify" />
## Erstellen eines awverify-Eintrags (nur A-Einträge)

Wenn Sie einen A-Datensatz erstellen, fordert die Web-App auch einen speziellen CNAME-Datensatz an. Über diesen wird sichergestellt, dass Sie der Besitzer der Domäne sind, die Sie verwenden möchten. Der CNAME-Eintrag muss das folgende Format aufweisen.

- *Wenn der A-Eintrag die Stammdomäne oder eine Platzhalterdomäne zuordnet:* Erstellen Sie einen CNAME-Eintrag, der eine Zuordnung von **awverify.&lt;IhreDomäne&gt;** zu **awverify.&lt;IhrWebsitename&gt;.azurewebsites.net** vornimmt. Wenn sich der A-Eintrag beispielsweise auf **contoso.com** bezieht, erstellen Sie einen CNAME-Eintrag für **awverify.contoso.com**.
- *Wenn der A-Eintrag eine spezifische Unterdomäne zuordnet:* Erstellen Sie einen CNAME-Eintrag, der eine Zuordnung von **awverify.&lt;Subdomain&gt;** zu **awverify.&lt;IhrWebsitename&gt;.azurewebsites.net** vornimmt. Wenn sich der A-Eintrag beispielsweise auf **blogs.contoso.com** bezieht, erstellen Sie einen CNAME-Eintrag für **awverify.blogs.contoso.com**.

Besucher Ihrer Web-App sehen die awverify-Unterdomäne nicht. Sie dient nur zum Überprüfen der Domäne in Azure.

## Aktivieren des Domänennamens in Ihrer Web-App

[AZURE.INCLUDE [Modi](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Überprüfen der DNS-Verteilung

Es kann nach Abschluss der Konfigurationsschritte einige Zeit dauern, bis die Änderungen übernommen wurden – dies hängt von Ihrem DNS-Anbieter ab. Mit [http://digwebinterface.com/](http://digwebinterface.com/) können Sie überprüfen, ob die DNS-Verteilung erwartungsgemäß funktioniert. Geben Sie nach dem Navigieren zu der Website die Hostnamen in das Textfeld ein, und klicken Sie auf **Dig**. Überprüfen Sie anhand der Ergebnisse, ob die aktuellen Änderungen übernommen wurden.

![](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE]Die Verteilung von DNS-Einträgen kann bis zu 48 Stunden (manchmal mehr) dauern. Wenn alles richtig konfiguriert wurde, müssen Sie trotzdem noch warten, bis die Verteilung durchgeführt wurde.

## Nächste Schritte

Weitere Informationen finden Sie unter [Erste Schritte mit Azure DNS](../dns/dns-getstarted-create-dnszone.md) sowie unter [Delegieren von Domänen an Azure DNS](../dns/dns-domain-delegation.md).

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).

<!-- Anchors. -->
[Overview]: #overview
[DNS record types]: #dns-record-types
[Find the virtual IP address]: #find-the-virtual-ip-address
[Create the DNS records]: #create-the-dns-records
[Enable the domain name on your web app]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!---HONumber=AcomDC_1203_2015-->