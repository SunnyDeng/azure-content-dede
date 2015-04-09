<properties 
	pageTitle="Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service (GoDaddy)" 
	description="Erfahren Sie, wie Sie einen Domänennamen von GoDaddy mit Azure-Web-Apps verwenden" 
	services="app-service\web" 
	documentationCenter="" 
	authors="wpickett" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-services-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="wpickett"/>

#Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service (GoDaddy)

[AZURE.INCLUDE [web-selector](../includes/websites-custom-domain-selector.md)]


[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

Dieser Artikel enthält Anweisungen zur Verwendung eines über [Go Daddy](https://godaddy.com) erworbenen benutzerdefinierten Domänennamens mit [App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

Themen in diesem Artikel:

-   [Grundlegendes zu DNS-Einträgen](#understanding-records)
-   [Hinzufügen eines DNS-Eintrags für Ihre benutzerdefinierte Domäne](#bkmk_configurecname)
-   [Aktivieren der Domäne in Ihrem Web](#enabledomain)

<h2><a name="understanding-records"></a>Grundlegendes zu DNS-Einträgen</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]


<h2><a name="bkmk_configurecname"></a>Hinzufügen eines DNS-Eintrags für Ihre benutzerdefinierte Domäne</h2>

Um Ihre benutzerdefinierte Domäne mit einer Web-App im App Service zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von GoDaddy bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für GoDaddy.com abrufen.

1. Melden Sie sich bei Ihrem Konto auf GoDaddy.com an, und wählen Sie **My Account** und dann **Manage your domains** aus. Wählen Sie zum Schluss das Dropdownmenü für den Domänennamen, den Sie mit Ihrer Azure-Web-App verwenden möchten, und dann **Manage DNS** aus.

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. Wählen Sie auf der Seite **Domain details** die Registerkarte **DNS Zone File** aus. Dieser Abschnitt wird verwendet, um die DNS-Datensätze für die Domäne hinzuzufügen und zu ändern. 

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

	Wählen Sie **Add Record** aus, um einen vorhandenen Datensatz hinzuzufügen.

	Um einen vorhandenen Datensatz zu **bearbeiten**, wählen Sie das Stift- und Papiersymbol neben dem Datensatz aus.

	> [AZURE.NOTE] Bevor Sie Einträge hinzufügen, beachten Sie, dass GoDaddy bereits DNS-Datensätze für gängige Unterdomänen (im Editor als **Host** bezeichnet) erstellt hat, z. B. **email**, **files**, **mail**. Wenn der Name, den Sie verwenden möchten, bereits vorhanden ist, bearbeiten Sie den vorhandenen Datensatz, statt einen neuen zu erstellen.

4. Beim Hinzufügen eines Datensatzes müssen Sie zuerst den Datensatztyp auswählen.

	![select record type](./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png)

	Als Nächstes müssen Sie den **Host** angeben (die benutzerdefinierte Domäne oder Unterdomäne) und worauf dieser verweist (unter **Points to**).

	![add zone record](./media/web-sites-custom-domain-name/godaddy-addzonerecord.png)

	* Wenn Sie einen **A-(Host-)Datensatz** hinzufügen, müssen Sie das Feld **Host** entweder auf **@** (dies steht für den Stammdomänennamen wie **contoso.com**), * (ein Platzhalter für verschiedene passende Unterdomänen) oder die zu verwendende Unterdomäne (z. B. **www**) festlegen. Sie müssen das Feld **points to** auf die IP-Adresse Ihrer Azure-Web-App festlegen.
	
		> [AZURE.NOTE] Bei Verwendung von A (Host)-Datensätzen müssen Sie auch einen CNAME-Datensatz mit der folgenden Konfiguration hinzufügen:
		> 
		> * Den Wert **awverify** für **Host** mit **points to** auf den Wert **awverify.&lt;NameIhrerWebApp&gt;.azurewebsites.net**.
		> 
		> Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die Domäne besitzen, die von dem A-Datensatz beschrieben wird.

	* Wenn Sie einen **CNAME (Alias)-Datensatz** hinzufügen, müssen Sie im Feld **Host** die gewünschte Unterdomäne festlegen. Beispiel: **www**. Sie müssen das Feld **Points to** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Web-App festlegen. Beispiel: **contoso.azurwebsites.net**.


5. Nachdem Sie die gewünschten Datensätze hinzugefügt oder bearbeitet haben, klicken Sie auf **Finish**, um die Änderungen zu speichern.

<h2><a name="enabledomain"></a>Aktivieren des Domänennamens in Ihrer Web-App</h2>

[AZURE.INCLUDE [Modi](../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service and existing Azure services (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Hinweise zu den Änderungen des neuen Portals gegenüber dem alten finden Sie unter: [Reference for navigating the preview portal (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->