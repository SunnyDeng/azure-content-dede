<properties
	pageTitle="Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service (GoDaddy)"
	description="Erfahren Sie, wie Sie einen Domänennamen von GoDaddy mit Azure-Web-Apps verwenden"
	services="app-service"
	documentationCenter=""
	authors="erikre"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="erikre"/>

# Konfigurieren eines benutzerdefinierten (direkt von GoDaddy erworbenen) Domänennamens in Azure App Service

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [Einführung](../../includes/custom-dns-web-site-intro.md)]

Wenn Sie eine Domäne über Azure App Service-Web-Apps erworben haben, lesen Sie den letzten Schritt zum [Erwerben einer Domäne für Web-Apps](custom-dns-web-site-buydomains-web-app.md).

Dieser Artikel enthält Anweisungen zur Verwendung eines benutzerdefinierten Domänennamens, der mit [App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) direkt bei [GoDaddy](https://godaddy.com) erworben wurde.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
##Interpretation von DNS-Datensätzen

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-raw.md)]

<a name="bkmk_configurecname"></a>
## Hinzufügen eines DNS-Datensatzes zu Ihrer benutzerdefinierten Domäne

Um Ihre benutzerdefinierte Domäne mit einer Web-App im App Service zu verknüpfen, müssen Sie einen neuen Eintrag in die DNS-Tabelle für Ihre benutzerdefinierte Domäne einfügen. Verwenden Sie hierzu die von GoDaddy bereitgestellten Tools. Mit den folgenden Schritten können Sie die DNS-Tools für GoDaddy.com abrufen.

1. Melden Sie sich bei Ihrem Konto auf GoDaddy.com an, und wählen Sie **My Account** und dann **Manage my domains** aus. Wählen Sie abschließend das Dropdownmenü für den Domänennamen, den Sie mit Ihrer Azure-Web-App verwenden möchten, und anschließend **DNS verwalten** aus.

	![Benutzerdefinierte Domänenseite für GoDaddy](./media/web-sites-godaddy-custom-domain-name/godaddy-customdomain.png)

2. Rufen Sie auf der Seite **Domain details** die Registerkarte **DNS Zone File** auf. Dieser Abschnitt wird verwendet, um die DNS-Datensätze für die Domäne hinzuzufügen und zu ändern.

	![Registerkarte der DNS-Zonendatei](./media/web-sites-godaddy-custom-domain-name/godaddy-zonetab.png)

	Wählen Sie **Add Record** aus, um einen vorhandenen Datensatz hinzuzufügen.

	Um einen vorhandenen Datensatz zu **bearbeiten**, wählen Sie das Stift- und Papiersymbol neben dem Datensatz aus.

	> [AZURE.NOTE] Beachten Sie vor dem Hinzufügen von neuen Datensätzen, dass GoDaddy bereits DNS-Datensätze für gängige Stammdomänen (im Editor als **Host** bezeichnet) erstellt hat, z. B. **email**, **files**, **mail** und andere. Wenn der Name, den Sie verwenden möchten, bereits vorhanden ist, bearbeiten Sie den vorhandenen Datensatz, statt einen neuen zu erstellen.

4. Beim Hinzufügen eines Datensatzes müssen Sie zuerst den Datensatztyp auswählen.

	![Datensatztyp auswählen](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)

	Als Nächstes müssen Sie den **Host** angeben (die benutzerdefinierte Domäne oder Unterdomäne) und worauf dieser verweist (unter **Points to**).

	![Zonendatensatz hinzufügen](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)

	* Wenn Sie einen **A (Host)**-Datensatz hinzufügen, müssen Sie das Feld **Host** entweder auf **@** (dies steht für den Stammdomänennamen wie **contoso.com**,) auf * (ein Platzhalter für den Abgleich von mehreren Unterdomänen) oder auf die zu verwendende Unterdomäne (z. B. **www**) einstellen. Sie müssen das Feld **Zeigt auf** auf die IP-Adresse Ihrer Azure-Web-App festlegen.

	* Wenn Sie einen **CNAME (Alias)**-Datensatz hinzufügen, müssen Sie im Feld **Host** die gewünschte Unterdomäne festlegen. Beispiel: **www**. Sie müssen das Feld **Zeigt auf** auf den **.azurewebsites.net**-Domänennamen Ihrer Azure-Web-App festlegen. Beispiel: **contoso.azurwebsites.net**.

5. Klicken Sie auf **Weiteren hinzufügen**.
6. Wählen Sie **CNAME** als Eintragstyp aus, und geben Sie dann den **Host**-Wert **Awverify** ein und den **Verweist auf**-Wert **Awverify. & Lt; Yourwebappname & Gt;. *.azurewebsites.NET** ein.

	> [AZURE.NOTE] Dieser CNAME-Datensatz wird von Azure verwendet, um zu überprüfen, ob Sie die Domäne besitzen, die von dem A-Datensatz oder dem ersten CNAME-Eintrag beschrieben wird. Nachdem die Domäne der Web-App im Azure-Portal zugeordnet wurde, kann der **Awverify**-Eintrag entfernt werden.

5. Nachdem Sie die gewünschten Datensätze hinzugefügt oder bearbeitet haben, klicken Sie auf **Finish**, um die Änderungen zu speichern.

<a name="enabledomain"></a>
## Aktivieren des Domänennamens auf Ihrer Web-App

[AZURE.INCLUDE [Modi](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Änderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).

<!---HONumber=AcomDC_0128_2016-->