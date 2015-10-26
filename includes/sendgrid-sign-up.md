Kunden von Azure können monatlich 25.000 kostenlose E-Mails freischalten. Mit diesen kostenlosen 25.000 monatlichen E-Mails erhalten Sie Zugriff auf erweiterte Berichterstellung und Analysen sowie auf [sämtliche APIs][] (Web, SMTP, Ereignis, Analyse usw.). Informationen über die zusätzlichen Dienste von SendGrid finden Sie auf der Seite [SendGrid Features][] (in englischer Sprache).

### So registrieren Sie ein SendGrid-Konto

1. Melden Sie sich beim [Azure-Verwaltungsportal][] an.

2. Klicken Sie im unteren Bereich des Verwaltungsportal auf **Neu**.

	![command-bar-new][command-bar-new]

3. Klicken Sie auf **Marketplace**.

	![sendgrid-store][sendgrid-store]

4. Klicken Sie im Dialogfeld **Choose an Application and Service** auf **SendGrid** und anschließend auf den Pfeil nach rechts.

5. Klicken Sie im Dialogfeld **Personalize Application and Service** auf den **SendGrid**-Plan, für den Sie sich anmelden möchten.

6. Geben Sie einen Namen zur Identifizierung Ihres **SendGrid**-Diensts in Ihren Azure-Einstellungen ein, oder verwenden Sie den Standardwert **SendGridEmailDelivery.Simplified.SMTPWebAPI**. Namen müssen zwischen 1 und 100 Zeichen lang sein und dürfen nur alphanumerische Zeichen, Gedankenstriche, Punkte und Unterstriche enthalten. Der Name muss in der Liste der abonnierten Azure Store-Artikel eindeutig sein.

	![store-screen-2][store-screen-2]

7. Wählen Sie einen Wert für die Region aus; z. B. West-USA.

8. Klicken Sie auf den Pfeil nach rechts.

9. Prüfen Sie den Plan, die Preisinformationen sowie die rechtlichen Bedingungen auf der Registerkarte **Bestellung prüfen**. Aktivieren Sie das Kontrollkästchen, wenn Sie den Bedingungen zustimmen. Nachdem Sie das Kontrollkästchen aktiviert haben, beginnt der [SendGrid-Bereitstellungsvorgang] für Ihr SendGrid-Konto.

	![store-screen-3][store-screen-3]

10. Nach der Bestätigung Ihres Einkaufs werden Sie zum Dashboard für Add-Ons weitergeleitet. Dort wird Ihnen die Meldung **Purchasing SendGrid** angezeigt.

	![sendgrid-purchasing-message][sendgrid-purchasing-message]

	Ihr SendGrid-Konto wird sofort bereitgestellt, und Ihnen wird die Meldung **Successfully purchased Add-On SendGrid** angezeigt. Jetzt erfolgt die Erstellung Ihres Kontos und Ihrer Anmeldedaten. Von nun an können Sie E-Mails versenden.

	Wenn Sie Ihren Abonnementplan ändern oder die SendGrid-Kontakteinstellungen anzeigen möchten, klicken Sie auf den Namen des SendGrid-Diensts, um das SendGrid-Marketplace-Dashboard zu öffnen.

	![sendgrid-add-on-dashboard][sendgrid-add-on-dashboard]

	Wenn Sie eine E-Mail über SendGrid versenden möchten, müssen Sie Ihre Konto-Anmeldeinformationen angeben (Benutzername und Kennwort).

### So finden Sie Ihre SendGrid-Anmeldeinformationen ###

1. Klicken Sie auf **Verbindungsinformationen**.

	![sendgrid-connection-info-button][sendgrid-connection-info-button]

2. Kopieren Sie im Feld *Verbindungsinformationen* das **Kennwort** und den Benutzernamen, um beides später in diesem Lernprogramm zu verwenden.

	![sendgrid-connection-info][sendgrid-connection-info]

	Um Einstellungen für die Zustellung von E-Mails festzulegen, klicken Sie auf die Schaltfläche **Verwalten**. Hiermit werden Sie zur SendGrid-Systemsteuerung umgeleitet.

	![sendgrid-control-panel][sendgrid-control-panel]

	Weitere Informationen über die ersten Schritte mit SendGrid finden Sie unter [SendGrid Getting Started][] (in englischer Sprache).

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[SendGrid Features]: http://sendgrid.com/features
[Azure-Verwaltungsportal]: https://manage.windowsazure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid-Bereitstellungsvorgang]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[sämtliche APIs]: https://sendgrid.com/docs/API_Reference/index.html

<!---HONumber=Oct15_HO3-->