<properties 
	pageTitle="Konfigurieren eines benutzerdefinierten Domänennamens für eine Web-App in Azure App Services, der Traffic Manager verwendet." 
	description="Verwenden Sie einen benutzerdefinierten Domänennamen für eine Web-App in Azure App Services mit Traffic Manager für den Lastenausgleich." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

#Konfigurieren eines benutzerdefinierten Domänennamens für eine Web-App in Azure App Services, der Traffic Manager verwendet.

[AZURE.INCLUDE [web-selector](../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dieser Artikel enthält allgemeine Anweisungen zur Verwendung eines benutzerdefinierten Domänennamens mit Azure App Services unter Verwendung von Traffic Manager für den Lastenausgleich.

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Grundlegendes zu DNS-Einträgen](#understanding-records)
-   [Konfigurieren Ihrer Web-Apps für den Modus "Standard"](#bkmk_configsharedmode)
-   [Hinzufügen eines DNS-Eintrags für Ihre benutzerdefinierte Domäne](#bkmk_configurecname)
-   [Aktivieren von Traffic Manager für Ihre Web-App](#enabledomain)

<a name="understanding-records"></a>
## Grundlegendes zu DNS-Einträgen

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## Konfigurieren Ihrer Web-Apps für den Modus "Standard"

[AZURE.INCLUDE [Modi](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## Hinzufügen eines DNS-Eintrags für Ihre benutzerdefinierte Domäne

Um Ihrer benutzerdefinierten Domäne eine Web-App in Azure App Services zuzuweisen, müssen Sie in der DNS-Tabelle für Ihre benutzerdefinierte Domäne mithilfe der von der Domänenregistrierungsstelle bereitgestellten Tools einen neuen Eintrag hinzufügen. Gehen Sie wie folgt vor, um die DNS-Tools zu suchen und zu verwenden:

1. Melden Sie sich bei Ihrem Konto mit der Domänenregistrierungsstelle an und suchen Sie nach der Seite, auf der DNS-Datensätze verwaltet werden. Suchen Sie nach Links oder Bereichen auf der Website mit der Bezeichnung **Domänenname**, **DNS** oder **Namenserververwaltung**. Häufig finden Sie in Ihren Kontoinformationen einen Link auf diese Seite. Suchen Sie anschließend nach einem Link, der Sie zu **Meine Domänen** führt.

4. Sobald Sie die Verwaltungsseite für Ihren Domänennamen gefunden haben, suchen Sie nach einem Link, über den Sie die DNS-Datensätze bearbeiten können. Dieser Konfigurationslink kann als **Zone file** (Zonendatei), **DNS Records** (DNS-Datensatz) oder als **Advanced** (Erweitert) bezeichnet werden.

	* Auf der Seite wurden wahrscheinlich bereits einige Datensätze erstellt, beispielsweise ein Eintrag, der **@** oder "*" mit einer 'domain parking'-Seite verknüpft. Möglicherweise sind auch bereits Datensätze wie übliche Unterdomänen wie **www** darauf gespeichert.
	* Auf der Seite werden **CNAME-Datensätze** angegeben, oder Sie finden ein Dropdownmenü, in dem Sie die Art des Datensatzes auswählen können. Möglicherweise werden auch andere Datensätze wie **A-Datensätze** und **MX-Einträge** aufgeführt. Mitunter werden CNAME-Datensätze anderweitig bezeichnet, z. B. als **Aliasdatensatz**.
	* Auf der Seite werden außerdem Felder angezeigt, über die Sie über einen **Hostnamen** oder **Domänennamen** eine **Zuweisung** zu einem anderen Domänennamen vornehmen können.

5. Jede Registrierungsstelle ist spezifisch, doch i. Allg. führen Sie eine Zuordnung ( *from*) von Ihrem benutzerdefinierten Domänennamen (z. B. **contoso.com**) zum ( *to*) Traffic Manager-Domänennamen (**contoso.trafficmanager.net**) durch, der für Ihre Azure-Web-App verwendet wird.

6. Speichern Sie die Änderungen, sobald Sie die DNS-Datensätze in Ihrer Registrierung hinzugefügt oder geändert haben.

<a name="enabledomain"></a>
## Aktivieren des Traffic Manager

[AZURE.INCLUDE [Modi](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Hinweise zu den Änderungen des neuen Portals gegenüber dem alten finden Sie unter: [Referenz für das Navigieren im Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--HONumber=52-->