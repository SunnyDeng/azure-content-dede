Wenn Sie eine Domäne benötigen, können Sie diese direkt im [Azure-Verwaltungsportal](https://portal.azure.com) kaufen. Gehen Sie folgendermaßen vor, um Domänennamen zu erwerben und Ihrer Web-App zuzuweisen.

1. Öffnen Sie das [Azure-Verwaltungsportal](https://portal.azure.com) in Ihrem Browser.

2. Klicken Sie auf der Registerkarte **Web-Apps** auf den Namen der Web-App, wählen Sie **Einstellungen** und dann **Benutzerdefinierte Domänen und SSL** aus.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Klicken Sie auf dem Blatt **Custom domains and SSL** auf **Buy domains**.

	![](./media/custom-dns-web-site/dncmntask-cname-buydomains-1.png)

4. Geben Sie auf dem Blatt **Buy Domains** im Textfeld den Domänennamen ein, den Sie kaufen möchten. Die vorgeschlagenen verfügbaren Domänen werden direkt unter dem Textfeld angezeigt. Wählen Sie die Domäne aus, die Sie kaufen möchten.

  ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-2.png)

5. Klicken Sie auf **Contact Information**, und füllen Sie das Formular mit den Kontaktinformationen für die Domäne aus.

  ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-3.png)

6. Klicken Sie auf dem Blatt **Buy Domains** auf **Auswählen**. Daraufhin wird das Blatt **Purchase confirmation** mit den Informationen zum Kauf angezeigt. Wenn Sie den rechtlichen Bedingungen zustimmen und auf **Buy** klicken, wird Ihre Bestellung gesendet und Sie können den Einkaufsprozess unter **Notification** überwachen.

  ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-5.png)

7. Wenn Sie erfolgreich eine Domäne bestellt haben, können Sie die Domäne verwalten und Ihrer Web-App zuweisen. Klicken Sie rechts neben Ihrer Domäne auf **...**. Anschließend können Sie entweder **Cancel purchase** oder **Domäne verwalten** auswählen. Klicken Sie auf **Domäne verwalten**, damit auf dem Blatt **Domäne verwalten** die **Unterdomäne** an die Web-App gebunden werden kann.

	![](./media/custom-dns-web-site/dncmntask-cname-buydomains-6.png)

	Sobald die Konfiguration abgeschlossen ist, wird der benutzerdefinierte Domänenname im Abschnitt **Hostname bindings** Ihrer Web-App aufgeführt.

Zu diesem Zeitpunkt sollten Sie den benutzerdefinierten Domänennamen in Ihren Browser eingeben können und sehen, dass er Sie erfolgreich zu Ihrer Web-App bringt.

<!---HONumber=62-->