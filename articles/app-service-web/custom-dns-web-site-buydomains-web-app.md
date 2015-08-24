
<properties
	pageTitle="Kaufen eines benutzerdefinierten Domänennamens für Azure App Service-Web-Apps"
	description="Erfahren Sie, wie Sie einen benutzerdefinierten Domänennamen für eine Web-App in Azure App Service kaufen."
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
	ms.date="05/21/2015"
	ms.author="mwasson"/>

# Kaufen und Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service

> [AZURE.SELECTOR]
- [Buy Domain for Web Apps](custom-dns-web-site-buydomains-web-app.md)
- [Web Apps with External Domains](web-sites-custom-domain-name.md)
- [Web Apps with Traffic Manager](web-sites-traffic-manager-custom-domain-name.md)
- [GoDaddy](web-sites-godaddy-custom-domain-name.md)




[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

Wenn Sie eine Web-App erstellen, weist Azure dieser eine Unterdomäne von "azurewebsites.NET" zu. Wenn Ihre Web-App beispielsweise **contoso** heißt, lautet die URL **contoso.azurewebsites.net**. Darüber hinaus weist Azure eine virtuelle IP-Adresse zu.

Bei einer Produktions-Web-App sollten Sie Benutzern einen benutzerdefinierten Domänennamen anzeigen. In diesem Artikel wird erläutert, wie Sie eine benutzerdefinierte Domäne für [App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) kaufen und konfigurieren.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## Übersicht

> [AZURE.NOTE]Versuchen Sie nicht, den Kauf einer Domäne mithilfe eines Abonnements durchzuführen, dem keine aktive Kreditkarte zugeordnet ist. Dadurch kann das Abonnement deaktiviert werden.

Wenn Sie über keinen Domänennamen für Ihre Web-App verfügen, können Sie diesen im [Azure-Verwaltungsportal](https://portal.azure.com) auf einfache Weise erwerben. Während des Einkaufsprozesses können Sie festlegen, dass die DNS-Einträge für WWW und die Stammdomäne automatisch Ihrer Web-App zugeordnet werden. Sie können Ihre Domäne auch direkt im Azure-Portal verwalten.


Gehen Sie folgendermaßen vor, um Domänennamen zu erwerben und Ihrer Web-App zuzuweisen.

1. Öffnen Sie das [Azure-Verwaltungsportal](https://portal.azure.com) in Ihrem Browser.

2. Klicken Sie auf der Registerkarte **Web-Apps** auf den Namen der Web-App, wählen Sie **Einstellungen** und dann **Benutzerdefinierte Domänen und SSL** aus.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. Klicken Sie auf dem Blatt **Custom domains and SSL** auf **Buy domains**.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. Geben Sie auf dem Blatt **Buy Domains** im Textfeld den Domänennamen ein, den Sie kaufen möchten, und drücken Sie die EINGABETASTE. Die vorgeschlagenen verfügbaren Domänen werden direkt unter dem Textfeld angezeigt. Wählen Sie die Domäne aus, die Sie kaufen möchten. Sie können auch mehrere Domänen auf einmal kaufen.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. Klicken Sie auf **Contact Information**, und füllen Sie das Formular mit den Kontaktinformationen für die Domäne aus.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

> [AZURE.NOTE]Es ist sehr wichtig, dass Sie alle erforderlichen Felder so genau wie möglich ausfüllen, insbesondere das Feld für die E-Mail-Adresse. Bei Erwerb der Domäne ohne Datenschutz werden Sie möglicherweise dazu aufgefordert, Ihre E-Mail-Adresse zu überprüfen, bevor die Domäne aktiviert wird. In einigen Fällen führen fehlerhafte Daten in den Kontaktinformationen dazu, dass der Domänenkauf fehlschlägt.

6. Nun stehen folgende Optionen zur Auswahl:

	a) "Auto renew", um die Domäne jedes Jahr automatisch zu verlängern
	
	b) "Privacy protection", um den KOSTENLOS im Kaufpreis enthaltenen Datenschutz zu aktivieren
	
	c) "Assign default hostnames", um der aktuellen Web-App Standardhostnamen für WWW und Stammdomäne zuzuweisen

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
> [AZURE.NOTE]Bei Option C werden DNS- und Hostnamenbindungen automatisch für Sie konfiguriert. Auf diese Weise kann über die benutzerdefinierte Domäne auf die Web-App zugegriffen werden, sobald der Kauf abgeschlossen ist (in einigen Fällen können Verzögerungen bei der DNS-Übertragung auftreten). Falls die Verwaltung der Web-App über Azure Traffic Manager erfolgt, wird keine Option zum Zuweisen der Stammdomäne angezeigt, da A-Einträge nicht mit Traffic Manager funktionieren.
>
>Sie können die für eine Web-App erworbenen Domänen/Unterdomänen jederzeit einer anderen Web-App zuweisen und umgekehrt. Weitere Informationen finden Sie in Schritt 8.

	
7. Klicken Sie auf dem Blatt **Buy Domains** auf **Auswählen**. Daraufhin wird das Blatt **Purchase confirmation** mit den Informationen zum Kauf angezeigt. Wenn Sie den rechtlichen Bedingungen zustimmen und auf **Buy** klicken, wird Ihre Bestellung gesendet und Sie können den Einkaufsprozess unter **Notification** überwachen. Der Domänenkauf kann einige Minuten dauern. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. Wenn Sie erfolgreich eine Domäne bestellt haben, können Sie die Domäne verwalten und Ihrer Web-App zuweisen. Klicken Sie rechts neben Ihrer Domäne auf **...**. Anschließend können Sie entweder **Cancel purchase** oder **Domäne verwalten** auswählen. Klicken Sie auf **Domäne verwalten**, damit auf dem Blatt **Domäne verwalten** die **Unterdomäne** an die Web-App gebunden werden kann. Wenn Sie eine **Unterdomäne** an eine andere Web-App binden möchten, führen Sie diesen Schritt aus dem Kontext der entsprechenden Web-App heraus durch. Hier können Sie die Domäne einem Traffic Manager-Endpunkt zuweisen (sofern die Web-App über Traffic Manager verwaltet wird), indem Sie einfach im Dropdownmenü den Traffic Manager-Namen auswählen. Auf diese Weise wird die Domäne/Unterdomäne automatisch allen Web-Apps hinter diesem Traffic Manager-Endpunkt zugewiesen. 

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

> [AZURE.NOTE]Sie können innerhalb von 5 Tagen auf "Cancel purchase" klicken, um den Kauf unter vollständiger Rückerstattung der Kosten zu stornieren. Nach 5 Tagen wird anstelle von "Cancel purchase" eine Option zum Löschen der Domäne angezeigt. Wenn Sie die Domäne löschen, wird diese ohne Erstattung aus dem Abonnement entfernt und wieder als verfügbare Domäne aufgeführt.

	Once configuration has completed, the custom domain name will be listed in the **Hostname bindings** section of your web app.

Zu diesem Zeitpunkt sollten Sie den benutzerdefinierten Domänennamen in Ihren Browser eingeben können und sehen, dass er Sie erfolgreich zu Ihrer Web-App bringt.
 

<!---HONumber=August15_HO7-->